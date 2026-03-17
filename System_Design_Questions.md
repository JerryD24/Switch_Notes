# System Design Notes

---

## 1. URL Shortener (like bit.ly, tinyurl)

### What It Does
● Converts a long URL into a short, unique alias (e.g., `https://short.ly/aB3xK9`) that redirects users to the original URL.
● Read-heavy system — for every 1 URL shortened, there are ~100 redirect requests.

---

### Functional Requirements
● Given a long URL, generate a unique short URL.
● When a user hits the short URL, redirect them to the original long URL.
● Short links should expire after a configurable time (optional).
● Users can optionally pick a custom short alias.
● Track click analytics (click count, timestamp, location).

### Non-Functional Requirements
● Low latency — redirect should happen in milliseconds.
● High availability — the service should be up 99.99%.
● Short codes should not be predictable (for security).
● System should handle billions of URLs.

---

### Capacity Estimation
● New URLs created per day: ~100 Million (writes)
● Redirects per day: ~10 Billion (reads)
● Read:Write ratio: 100:1
● QPS (writes): 100M / 86400 ≈ 1,200/sec
● QPS (reads): 10B / 86400 ≈ 120,000/sec
● Storage per record: ~500 bytes (short code + long URL + metadata)
● Storage for 5 years: 100M × 365 × 5 × 500B ≈ 90 TB
● Short code length: 7 characters (base62) → 62^7 = 3.5 trillion unique codes

---

### HLD (High-Level Design)

```
Client → Load Balancer → App Servers (Spring Boot) → Cache (Redis) → Database (MySQL/Postgres)
```

● **Load Balancer** — Distributes traffic across multiple app server instances.
● **App Servers** — Stateless Spring Boot services handling shorten and redirect APIs.
● **Cache (Redis)** — Stores frequently accessed short-code-to-URL mappings. Since reads are 100x writes, caching is critical.
● **Database (MySQL/Cassandra)** — Persistent storage for all URL mappings.
● **Key Generation Service (KGS)** — (Optional) Pre-generates unique short codes in batches. App servers request a code from KGS instead of computing one at runtime. Eliminates collision handling.

#### Architecture Diagram
```
                        ┌──────────────┐
    User ──────────►    │ Load Balancer │
                        └──────┬───────┘
                               │
                ┌──────────────┼──────────────┐
                │              │              │
         ┌──────▼──────┐ ┌────▼────┐ ┌───────▼─────┐
         │ App Server 1 │ │ App 2   │ │ App Server N│
         │ (Spring Boot)│ │         │ │             │
         └──────┬───────┘ └────┬────┘ └──────┬──────┘
                │              │             │
         ┌──────▼──────────────▼─────────────▼──────┐
         │                  Redis Cache              │
         │          (short_code → long_url)          │
         └─────────────────────┬────────────────────┘
                               │ (cache miss)
         ┌─────────────────────▼────────────────────┐
         │           Database (MySQL/Cassandra)       │
         │           url_mapping table                │
         └────────────────────────────────────────────┘
```

---

### LLD (Low-Level Design)

#### API Design
```
POST /api/shorten
  Request:  { "long_url": "https://example.com/very/long/path", "custom_alias": "mylink" (optional), "expiry_days": 30 (optional) }
  Response: { "short_url": "https://short.ly/aB3xK9", "expires_at": "2026-04-17T00:00:00Z" }
  Status:   201 Created

GET /{shortCode}
  Response: 302 Redirect → Location: https://example.com/very/long/path
  Status:   302 Found (or 301 Moved Permanently)
  Error:    404 Not Found (if code doesn't exist or expired)
```

#### Database Schema
```sql
CREATE TABLE url_mapping (
    id          BIGINT PRIMARY KEY AUTO_INCREMENT,
    short_code  VARCHAR(10) UNIQUE NOT NULL,
    long_url    TEXT NOT NULL,
    created_at  TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    expires_at  TIMESTAMP NULL,
    click_count BIGINT DEFAULT 0,
    user_id     BIGINT NULL
);

CREATE INDEX idx_short_code ON url_mapping(short_code);
CREATE INDEX idx_expires_at ON url_mapping(expires_at);
```

#### Short Code Generation Approaches

| Approach | How It Works | Pros | Cons |
|----------|-------------|------|------|
| Base62 Encoding | Convert auto-increment ID to base62 (a-z, A-Z, 0-9) | Simple, zero collisions | Predictable/sequential |
| MD5/SHA256 Hashing | Hash the long URL, take first 7 chars | Deterministic (same URL = same code) | Collisions possible |
| Pre-generated Keys (KGS) | Generate random keys in advance, assign on demand | Fast, no collision at runtime | Needs separate key service |
| UUID-based | Generate UUID, encode to short string | Globally unique | Longer codes |

#### Collision Handling (if using hashing)
```java
int salt = 0;
String shortCode = base62(hash(longUrl)).substring(0, 7);
while (db.exists(shortCode)) {
    salt++;
    shortCode = base62(hash(longUrl + salt)).substring(0, 7);
}
```
● Append an incrementing salt to the URL, re-hash until a unique code is found.
● In practice, collisions are rare with 7-char base62 (3.5 trillion combinations).

#### Class Diagram
```
┌──────────────────┐     ┌────────────────────┐     ┌──────────────────┐
│  UrlController    │────►│  UrlService          │────►│  UrlRepository   │
│  (REST layer)     │     │  (Business logic)    │     │  (Data access)   │
├──────────────────┤     ├────────────────────┤     ├──────────────────┤
│ + shorten()       │     │ + createShortUrl()   │     │ + save()         │
│ + redirect()      │     │ + getOriginalUrl()   │     │ + findByCode()   │
└──────────────────┘     │ + isExpired()        │     │ + existsByCode() │
                          └─────────┬──────────┘     └──────────────────┘
                                    │
                          ┌─────────▼──────────┐
                          │ CodeGenerator       │  ← Interface (Strategy Pattern)
                          ├────────────────────┤
                          │ + generate(): String│
                          └─────────┬──────────┘
                                    │
                     ┌──────────────┼──────────────┐
                     │              │              │
              ┌──────▼───┐  ┌──────▼─────┐  ┌────▼────────┐
              │ Base62Gen │  │ HashBasedGen│  │ UUIDGen     │
              └──────────┘  └────────────┘  └─────────────┘
```

#### Design Patterns Used
● **Strategy Pattern** — `CodeGenerator` interface with swappable implementations (Base62, Hash, UUID).
● **Repository Pattern** — `UrlRepository` abstracts database access from service logic.
● **Cache-Aside Pattern** — Check Redis first, fallback to DB on cache miss, populate cache after DB read.
● **Builder Pattern** — For constructing `UrlMapping` objects with optional fields (expiry, custom alias).

#### Core Service Logic
```java
@Service
public class UrlService {

    private final UrlRepository repo;
    private final CodeGenerator codeGenerator;
    private final RedisTemplate<String, String> cache;

    public String createShortUrl(String longUrl) {
        if (!isValidUrl(longUrl)) throw new InvalidUrlException(longUrl);

        Optional<UrlMapping> existing = repo.findByLongUrl(longUrl);
        if (existing.isPresent()) return existing.get().getShortCode();

        String code;
        do {
            code = codeGenerator.generate();
        } while (repo.existsByShortCode(code));

        repo.save(new UrlMapping(code, longUrl));
        cache.opsForValue().set(code, longUrl, 24, TimeUnit.HOURS);
        return code;
    }

    public String getOriginalUrl(String shortCode) {
        String cached = cache.opsForValue().get(shortCode);
        if (cached != null) return cached;

        UrlMapping mapping = repo.findByShortCode(shortCode)
            .orElseThrow(() -> new UrlNotFoundException(shortCode));

        if (mapping.isExpired()) throw new UrlExpiredException(shortCode);

        cache.opsForValue().set(shortCode, mapping.getLongUrl(), 24, TimeUnit.HOURS);
        repo.incrementClickCount(shortCode);
        return mapping.getLongUrl();
    }
}
```

#### 301 vs 302 Redirect
● **301 (Moved Permanently)** — Browser caches the redirect. Faster for users, but you lose analytics on repeat visits because the browser won't hit your server again.
● **302 (Found/Temporary)** — Browser always hits your server first. Better for tracking click counts and analytics. Most URL shorteners use 302.

---

### Scaling Considerations
● **Database Sharding** — Shard by first character of short code or hash-based sharding. Distributes data across multiple DB instances.
● **Cache Eviction** — Use LRU (Least Recently Used) policy in Redis. Most URLs follow a power-law distribution — a few URLs get most of the traffic.
● **Read Replicas** — Since reads >> writes, use MySQL read replicas for redirect queries.
● **CDN** — For global users, put redirect endpoints behind a CDN for geographic caching.
● **Cleanup Job** — Run a scheduled batch job to delete expired URLs and reclaim storage.

---

### Common Interview Follow-ups
● **How to prevent abuse?** — Rate limiting per user/IP (see Rate Limiter section below).
● **How to handle custom aliases?** — Check uniqueness before saving; reject if already taken.
● **How to track analytics?** — Publish click events to Kafka asynchronously, aggregate with a batch job.
● **How to ensure uniqueness across distributed servers?** — Use a Key Generation Service (KGS) or Twitter Snowflake for distributed ID generation.
● **How to handle expiry?** — Check `expires_at` on read; run a cron job to purge expired rows.

---
---

## 2. Rate Limiter

### What It Does
● Controls the number of requests a client can make to an API within a given time window.
● Protects backend services from being overwhelmed by too many requests (DDoS attacks, bots, misbehaving clients).
● Returns HTTP `429 Too Many Requests` when the limit is exceeded.

---

### Functional Requirements
● Limit the number of API requests per client (identified by user ID, API key, or IP address).
● Support different rate limits for different APIs or user tiers (e.g., free: 100 req/min, premium: 1000 req/min).
● Return a clear response (429) with headers indicating remaining quota and retry-after time.
● The limiter should work in a distributed environment (multiple app server instances).

### Non-Functional Requirements
● Low latency — rate check must add minimal overhead (< 1ms).
● High availability — if the rate limiter goes down, it should fail open (allow requests) rather than block everything.
● Accurate — should not allow significantly more requests than the configured limit.
● Scalable — should handle millions of users and billions of requests.

---

### Where to Place the Rate Limiter

```
Option 1: Client-side       → Easy to bypass, not reliable
Option 2: Server-side       → Inside the app (middleware/filter)
Option 3: API Gateway       → Best for microservices (Kong, Nginx, AWS API Gateway)
Option 4: Dedicated service → Separate rate limiter service (Redis-based)
```

● Most common in interviews: **Server-side middleware** or **API Gateway** with a **Redis backend**.

---

### Rate Limiting Algorithms

#### Algorithm 1: Fixed Window Counter
```
● Divide time into fixed windows (e.g., 1-minute intervals).
● Maintain a counter per client per window.
● If counter > limit → reject (429).
● Reset counter when the window expires.

Example: Limit = 5 requests/minute
  Window: 10:00:00 – 10:00:59
  Request 1 → counter = 1 ✓
  Request 2 → counter = 2 ✓
  ...
  Request 5 → counter = 5 ✓
  Request 6 → counter = 6 ✗ (429 Too Many Requests)
  10:01:00 → counter resets to 0

Pros: Simple, low memory.
Cons: Burst at window boundary — a user can send 5 requests at 10:00:59
      and 5 more at 10:01:00 = 10 requests in 2 seconds.
```

#### Algorithm 2: Sliding Window Log
```
● Store the timestamp of every request in a sorted set (per client).
● On each request, remove all timestamps older than the window.
● Count remaining entries — if count > limit → reject.

Example: Limit = 5 requests/minute
  Timestamps: [10:00:10, 10:00:20, 10:00:30, 10:00:40, 10:00:50]
  New request at 10:01:05:
    Remove entries before 10:00:05 → [10:00:10, 10:00:20, 10:00:30, 10:00:40, 10:00:50]
    Count = 5 → REJECT (429)

Pros: Very accurate, no boundary burst issue.
Cons: High memory — stores every timestamp.
```

#### Algorithm 3: Sliding Window Counter (Hybrid)
```
● Combines Fixed Window + Sliding Window.
● Uses counters from the current window and previous window.
● Weighted formula: count = (prev_window_count × overlap%) + current_window_count

Example: Limit = 10 requests/minute
  Previous window (10:00 - 10:01): 8 requests
  Current window  (10:01 - 10:02): 3 requests
  Request arrives at 10:01:40 → 40 seconds into current window
  Overlap with previous = 1 - (40/60) = 33%
  Weighted count = (8 × 0.33) + 3 = 2.64 + 3 = 5.64 → ALLOW (< 10)

Pros: Accurate + low memory (only 2 counters per client).
Cons: Approximate (not 100% precise, but close enough).
```

#### Algorithm 4: Token Bucket (Most Popular)
```
● Each client has a bucket that holds tokens (max = bucket size).
● Tokens are added at a fixed rate (e.g., 10 tokens/second).
● Each request consumes 1 token.
● If bucket is empty → reject (429).
● Allows short bursts up to the bucket size.

Example: Bucket size = 10, Refill rate = 2 tokens/sec
  t=0: bucket = 10
  5 requests arrive → bucket = 5 ✓
  t=1: 2 tokens added → bucket = 7
  3 requests arrive → bucket = 4 ✓
  t=2: 2 tokens added → bucket = 6
  ...
  If 10 rapid requests → bucket = 0 → next request = 429

Pros: Allows controlled bursts, smooth rate limiting.
Cons: Two parameters to tune (bucket size + refill rate).
```

#### Algorithm 5: Leaky Bucket
```
● Requests enter a queue (bucket) of fixed size.
● Requests are processed at a fixed rate (constant outflow).
● If queue is full → reject (429).
● Shapes traffic into a smooth, constant rate.

Example: Queue size = 5, Process rate = 1 req/sec
  3 requests arrive at once → queue = [R1, R2, R3]
  R1 processed at t=0, R2 at t=1, R3 at t=2
  If queue is full and new request arrives → 429

Pros: Smooth, constant output rate.
Cons: Does not allow bursts (even legitimate ones). Old requests can starve if queue is always full.
```

#### Algorithm Comparison

| Algorithm | Accuracy | Memory | Burst Handling | Complexity |
|-----------|----------|--------|---------------|------------|
| Fixed Window | Low (boundary burst) | Very Low | Poor | Simple |
| Sliding Window Log | Very High | High | Good | Medium |
| Sliding Window Counter | High | Low | Good | Medium |
| Token Bucket | High | Low | Good (controlled) | Medium |
| Leaky Bucket | High | Low | No bursts allowed | Medium |

● **Interview recommendation:** Token Bucket or Sliding Window Counter — both are practical and commonly used in production.

---

### HLD (High-Level Design)

```
Client → API Gateway / Load Balancer → Rate Limiter Middleware → App Server → Database
                                              │
                                        ┌─────▼─────┐
                                        │   Redis    │
                                        │ (counters/ │
                                        │  tokens)   │
                                        └────────────┘
```

● **Redis** is the central store for rate limit counters/tokens across all app server instances.
● Rate limiter runs as a **filter/interceptor** before the request reaches the controller.
● If limit exceeded, return `429` immediately without hitting the backend.

#### Architecture Diagram
```
                        ┌──────────────┐
     Client ────────►   │  API Gateway  │
                        └──────┬───────┘
                               │
                        ┌──────▼───────┐
                        │ Rate Limiter  │──────► Redis
                        │  (Filter)     │       (per-client counters)
                        └──────┬───────┘
                               │ (allowed)
                        ┌──────▼───────┐
                        │  App Server   │
                        │ (Controller)  │
                        └──────┬───────┘
                               │
                        ┌──────▼───────┐
                        │   Database    │
                        └──────────────┘
```

---

### LLD (Low-Level Design)

#### API Response Headers
```
HTTP/1.1 200 OK
X-RateLimit-Limit: 100              ← max requests allowed in window
X-RateLimit-Remaining: 42           ← requests remaining
X-RateLimit-Reset: 1679012400       ← Unix timestamp when the window resets

HTTP/1.1 429 Too Many Requests
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 0
X-RateLimit-Reset: 1679012400
Retry-After: 30                      ← seconds to wait before retrying
Content-Type: application/json
{ "error": "Rate limit exceeded. Try again in 30 seconds." }
```

#### Database/Redis Schema

**Rate Limit Rules (MySQL — for configuration)**
```sql
CREATE TABLE rate_limit_rules (
    id          BIGINT PRIMARY KEY AUTO_INCREMENT,
    api_path    VARCHAR(255) NOT NULL,
    user_tier   VARCHAR(50) DEFAULT 'FREE',
    max_requests INT NOT NULL,
    window_seconds INT NOT NULL
);

-- Example rules
INSERT INTO rate_limit_rules VALUES (1, '/api/shorten', 'FREE', 100, 60);
INSERT INTO rate_limit_rules VALUES (2, '/api/shorten', 'PREMIUM', 1000, 60);
INSERT INTO rate_limit_rules VALUES (3, '/api/*', 'FREE', 500, 3600);
```

**Redis Keys (for runtime counters)**
```
Token Bucket:
  Key:   rate_limit:{client_id}:{api_path}
  Value: { "tokens": 8, "last_refill": 1679012345 }
  TTL:   window size (auto-cleanup)

Fixed Window:
  Key:   rate_limit:{client_id}:{api_path}:{window_timestamp}
  Value: 42  (request count)
  TTL:   window size
```

#### Class Diagram
```
┌──────────────────────┐
│  RateLimiterFilter    │  ← Spring Filter / Interceptor
│  (Entry point)        │
├──────────────────────┤
│ + doFilter(request)   │
└──────────┬───────────┘
           │
┌──────────▼───────────┐     ┌───────────────────────┐
│  RateLimiterService   │────►│  RateLimitRuleRepo     │
│  (Orchestration)      │     │  (Load rules from DB)  │
├──────────────────────┤     └───────────────────────┘
│ + isAllowed(clientId) │
│ + getRemainingQuota() │
└──────────┬───────────┘
           │
┌──────────▼───────────┐
│  RateLimitStrategy    │  ← Interface (Strategy Pattern)
├──────────────────────┤
│ + tryConsume(key): bool│
│ + getRemaining(key)   │
└──────────┬───────────┘
           │
    ┌──────┼──────────┬────────────────┐
    │      │          │                │
┌───▼────┐ ┌▼────────┐ ┌▼──────────┐ ┌─▼───────────┐
│Token   │ │Sliding  │ │Fixed      │ │Leaky        │
│Bucket  │ │Window   │ │Window     │ │Bucket       │
│Strategy│ │Strategy │ │Strategy   │ │Strategy     │
└────────┘ └─────────┘ └───────────┘ └─────────────┘
    All use Redis (RedisTemplate) as the backing store
```

#### Design Patterns Used
● **Strategy Pattern** — `RateLimitStrategy` interface with multiple algorithm implementations. Swap Token Bucket for Sliding Window without changing any other code.
● **Filter/Interceptor Pattern** — `RateLimiterFilter` intercepts requests before they reach controllers.
● **Factory Pattern** — `RateLimitStrategyFactory` creates the correct strategy based on configuration.
● **Cache-Aside Pattern** — Rate limit rules loaded from DB and cached in-memory with periodic refresh.

#### Token Bucket Implementation (Redis + Lua)
```java
@Component
public class TokenBucketStrategy implements RateLimitStrategy {

    private final RedisTemplate<String, String> redis;

    private static final String LUA_SCRIPT = """
        local key = KEYS[1]
        local max_tokens = tonumber(ARGV[1])
        local refill_rate = tonumber(ARGV[2])
        local now = tonumber(ARGV[3])

        local data = redis.call('HMGET', key, 'tokens', 'last_refill')
        local tokens = tonumber(data[1]) or max_tokens
        local last_refill = tonumber(data[2]) or now

        -- Calculate tokens to add based on elapsed time
        local elapsed = now - last_refill
        tokens = math.min(max_tokens, tokens + (elapsed * refill_rate))

        if tokens >= 1 then
            tokens = tokens - 1
            redis.call('HMSET', key, 'tokens', tokens, 'last_refill', now)
            redis.call('EXPIRE', key, math.ceil(max_tokens / refill_rate) * 2)
            return {1, tokens}  -- allowed, remaining
        else
            return {0, 0}       -- rejected, 0 remaining
        end
    """;

    public RateLimitResult tryConsume(String clientId, String apiPath, int maxTokens, int refillRate) {
        String key = "rate_limit:" + clientId + ":" + apiPath;
        long now = Instant.now().getEpochSecond();

        List<Long> result = redis.execute(
            RedisScript.of(LUA_SCRIPT, List.class),
            List.of(key), String.valueOf(maxTokens), String.valueOf(refillRate), String.valueOf(now)
        );

        boolean allowed = result.get(0) == 1;
        long remaining = result.get(1);
        return new RateLimitResult(allowed, remaining);
    }
}
```
● **Why Lua script?** — Redis executes Lua atomically. Without it, a race condition can occur: two requests read the same token count simultaneously, both think there's 1 token left, and both pass — exceeding the limit.

#### Spring Boot Filter
```java
@Component
@Order(1)
public class RateLimiterFilter extends OncePerRequestFilter {

    private final RateLimiterService rateLimiterService;

    @Override
    protected void doFilterInternal(HttpServletRequest request,
                                     HttpServletResponse response,
                                     FilterChain chain) throws ServletException, IOException {

        String clientId = extractClientId(request);  // from API key, JWT, or IP
        String apiPath = request.getRequestURI();

        RateLimitResult result = rateLimiterService.checkLimit(clientId, apiPath);

        response.setHeader("X-RateLimit-Limit", String.valueOf(result.getLimit()));
        response.setHeader("X-RateLimit-Remaining", String.valueOf(result.getRemaining()));
        response.setHeader("X-RateLimit-Reset", String.valueOf(result.getResetTimestamp()));

        if (!result.isAllowed()) {
            response.setStatus(429);
            response.setHeader("Retry-After", String.valueOf(result.getRetryAfterSeconds()));
            response.getWriter().write("{\"error\": \"Rate limit exceeded\"}");
            return;
        }

        chain.doFilter(request, response);  // allowed — proceed to controller
    }

    private String extractClientId(HttpServletRequest request) {
        String apiKey = request.getHeader("X-API-Key");
        if (apiKey != null) return apiKey;
        return request.getRemoteAddr();  // fallback to IP
    }
}
```

---

### Scaling Considerations
● **Redis Cluster** — For millions of clients, use Redis Cluster to shard rate limit keys across nodes.
● **Local + Global Limiting** — Use an in-memory counter per app server (local) + Redis (global). Local counter acts as a fast first check, reducing Redis calls.
● **Fail-Open Policy** — If Redis is unreachable, allow requests through rather than blocking all traffic. Availability > strict rate enforcement.
● **Race Conditions** — Always use Redis Lua scripts or MULTI/EXEC for atomic read-check-update operations.
● **Distributed Sync** — In a multi-datacenter setup, each datacenter can maintain its own Redis and sync periodically, accepting slight over-limit tolerance.

---

### Common Interview Follow-ups
● **How to identify the client?** — API key (best), JWT/user ID (authenticated), IP address (fallback, unreliable behind NAT).
● **What if Redis goes down?** — Fail open (allow all) or use local in-memory fallback with degraded accuracy.
● **How to handle different limits for different APIs?** — Store rules in DB per API path and user tier, cache in-memory.
● **How does an API Gateway handle it?** — Kong, Nginx, AWS API Gateway have built-in rate limiting with Redis backends.
● **Token Bucket vs Leaky Bucket?** — Token Bucket allows bursts (good for real traffic patterns), Leaky Bucket enforces constant rate (good for protecting downstream services).
● **How to rate limit by IP when users are behind a proxy?** — Use `X-Forwarded-For` header, but be careful — it can be spoofed.
