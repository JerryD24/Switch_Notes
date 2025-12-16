# ☁️ Spring Cloud Projects - Complete Guide with Detailed Theory

> A comprehensive guide covering Spring Cloud components for building Microservices architecture with in-depth theoretical explanations

**Spring Cloud Version:** `<spring.cloud.version>3.0.1</spring.cloud.version>`

---

# 📑 Table of Contents

1. [Overview of Spring Cloud Components](#1-overview-of-spring-cloud-components)
2. [Spring Cloud OpenFeign](#2-spring-cloud-openfeign)
3. [Service Discovery & Registry (Eureka)](#3-service-discovery--registry-eureka)
4. [Client Side Load Balancer](#4-client-side-load-balancer)
5. [API Gateway](#5-api-gateway)
6. [Fault Tolerance & Circuit Breaker (Resilience4j)](#6-fault-tolerance--circuit-breaker-resilience4j)
7. [Distributed Tracing (Sleuth & Zipkin)](#7-distributed-tracing-sleuth--zipkin)
8. [Config Server](#8-config-server)

---

# 1. Overview of Spring Cloud Components

## What is Spring Cloud?

Spring Cloud provides tools for developers to quickly build some of the common patterns in distributed systems. It builds on Spring Boot and provides a suite of tools to build cloud-native applications.

## Why Do We Need Spring Cloud?

In a microservices architecture, we face several challenges:

```
┌─────────────────────────────────────────────────────────────────────────┐
│                 MICROSERVICES CHALLENGES & SOLUTIONS                     │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                          │
│  CHALLENGE                          │  SPRING CLOUD SOLUTION            │
│  ═══════════════════════════════════╪══════════════════════════════════ │
│                                      │                                   │
│  How do services communicate?        │  OpenFeign                        │
│  (Inter-service communication)       │  (Declarative REST client)        │
│                                      │                                   │
│  How do services find each other?    │  Eureka                           │
│  (Service Discovery)                 │  (Service Registry)               │
│                                      │                                   │
│  How to distribute load?             │  Spring Cloud LoadBalancer        │
│  (Load Balancing)                    │  (Client-side load balancing)     │
│                                      │                                   │
│  How to have single entry point?     │  API Gateway                      │
│  (Unified Access)                    │  (Routing, Auth, Rate Limiting)   │
│                                      │                                   │
│  How to handle service failures?     │  Resilience4j                     │
│  (Fault Tolerance)                   │  (Circuit Breaker)                │
│                                      │                                   │
│  How to track requests?              │  Sleuth + Zipkin                  │
│  (Distributed Tracing)               │  (Trace & Span IDs)               │
│                                      │                                   │
│  How to manage configurations?       │  Config Server                    │
│  (Centralized Configuration)         │  (Git-based config)               │
│                                      │                                   │
└─────────────────────────────────────────────────────────────────────────┘
```

## Spring Cloud Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────────────┐
│                    SPRING CLOUD MICROSERVICES ARCHITECTURE               │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                          │
│                              ┌─────────────────┐                        │
│                              │  CONFIG SERVER  │                        │
│                              │  (Centralized   │                        │
│                              │   Properties)   │                        │
│                              └────────┬────────┘                        │
│                                       │                                  │
│                              ┌────────┴────────┐                        │
│                              │  EUREKA SERVER  │                        │
│                              │ (Service Disc.) │                        │
│                              └────────┬────────┘                        │
│                                       │                                  │
│   ┌───────────────────────────────────┼───────────────────────────────┐ │
│   │                                   │                               │ │
│   │                          ┌────────┴────────┐                      │ │
│   │      CLIENTS ──────────▶│   API GATEWAY   │                      │ │
│   │                          │ (Entry Point)   │                      │ │
│   │                          └────────┬────────┘                      │ │
│   │                                   │                               │ │
│   │              ┌────────────────────┼────────────────────┐          │ │
│   │              │                    │                    │          │ │
│   │              ▼                    ▼                    ▼          │ │
│   │      ┌─────────────┐      ┌─────────────┐      ┌─────────────┐   │ │
│   │      │   Student   │      │   Address   │      │   Course    │   │ │
│   │      │   Service   │◀────▶│   Service   │◀────▶│   Service   │   │ │
│   │      └──────┬──────┘      └──────┬──────┘      └──────┬──────┘   │ │
│   │             │                    │                    │          │ │
│   │             │    OPEN FEIGN      │                    │          │ │
│   │             │  (Communication)   │                    │          │ │
│   │             │                    │                    │          │ │
│   │      ┌──────┴──────┐      ┌──────┴──────┐      ┌──────┴──────┐   │ │
│   │      │     DB      │      │     DB      │      │     DB      │   │ │
│   │      └─────────────┘      └─────────────┘      └─────────────┘   │ │
│   │                                                                   │ │
│   │                    ┌─────────────────────┐                       │ │
│   │                    │   SLEUTH & ZIPKIN   │                       │ │
│   │                    │ (Distributed Trace) │                       │ │
│   │                    └─────────────────────┘                       │ │
│   │                                                                   │ │
│   └───────────────────────────────────────────────────────────────────┘ │
│                                                                          │
└─────────────────────────────────────────────────────────────────────────┘
```

## Spring Cloud Components - Detailed Theory

### 1. Spring Cloud OpenFeign
**Purpose:** We need to integrate the Microservices in order to establish the communication between them.

**Theory:** OpenFeign is a declarative web service client. It makes writing web service clients easier. Instead of writing boilerplate code to make HTTP calls, you simply declare an interface with annotations.

### 2. Spring Cloud Netflix Eureka
**Purpose:** Each of our Microservice will register itself with the Eureka and it will be having the unique ID. So, our MS will communicate with another MS using the **service name**. It does NOT use the URL of another MS.

**Theory:** Eureka is a REST-based service that is primarily used for locating services for the purpose of load balancing and failover. In microservices architecture, services need to find and communicate with each other, and Eureka provides this capability.

### 3. Spring Cloud Load Balancer
**Purpose:** For multiple instances of a Microservice, we need a load balancer to balance the calls from the client MS.

**Theory:** When you have multiple instances of a service running (for high availability), the load balancer distributes incoming requests across all instances to ensure no single instance is overwhelmed.

### 4. Spring Cloud API Gateway
**Purpose:** It is the entry point of each of the MS. All calls first come to API Gateway to perform some common tasks, like authentication, logging, rate limiting, etc.

**Theory:** API Gateway acts as a "front door" for all requests entering the system. It handles cross-cutting concerns that you don't want to implement in every microservice.

### 5. Fault Tolerance (Resilience4j)
**Purpose:** Downing of one MS will NOT impact the work of other MSs.

**Theory:** In a distributed system, failures are inevitable. Circuit breaker pattern prevents cascading failures by stopping the flow of requests to a failing service and providing fallback responses.

### 6. Sleuth and Zipkin
**Purpose:** Sleuth gives the complete flow of how MS is communicating with each other whereas Zipkin is a UI tool to see this complete flow - from where to where your request is going.

**Theory:** In microservices, a single request might pass through multiple services. Distributed tracing helps you understand the complete journey of a request, making debugging and performance optimization easier.

### 7. Config Server
**Purpose:** Common set of configurations are done in this for each MS, like database properties, feature flags, etc.

**Theory:** Instead of hardcoding configuration in each microservice, Config Server provides a centralized place to manage configurations for all services. Configurations are typically stored in Git for version control.

---

# 2. Spring Cloud OpenFeign

## What is OpenFeign?

**OpenFeign is a declarative REST client** by which we can make REST calls to other services. It simplifies HTTP API clients by allowing you to define the API as a simple interface.

```
┌─────────────────────────────────────────────────────────────────────────┐
│                         WHAT IS OPENFEIGN?                               │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                          │
│  WITHOUT FEIGN (Traditional RestTemplate):                               │
│  ══════════════════════════════════════════                              │
│                                                                          │
│  @Service                                                                │
│  public class StudentService {                                           │
│      @Autowired                                                          │
│      private RestTemplate restTemplate;                                  │
│                                                                          │
│      public Address getAddress(Long id) {                                │
│          String url = "http://address-service/api/address/" + id;        │
│          ResponseEntity<Address> response = restTemplate.exchange(       │
│              url, HttpMethod.GET, null,                                  │
│              new ParameterizedTypeReference<Address>() {}                │
│          );                                                              │
│          return response.getBody();                                      │
│      }                                                                   │
│  }                                                                       │
│                                                                          │
│  WITH FEIGN (Declarative Approach):                                      │
│  ═══════════════════════════════════                                     │
│                                                                          │
│  @FeignClient(value = "address-service", path = "/api/address")          │
│  public interface AddressFeignClient {                                   │
│      @GetMapping("/{id}")                                                │
│      Address getAddress(@PathVariable Long id);                          │
│  }                                                                       │
│                                                                          │
│  // Just inject and use - No boilerplate code!                          │
│  @Autowired                                                              │
│  private AddressFeignClient addressFeignClient;                          │
│                                                                          │
└─────────────────────────────────────────────────────────────────────────┘
```

## How Feign Works Internally

```
┌─────────────────────────────────────────────────────────────────────────┐
│                      FEIGN INTERNAL WORKING                              │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                          │
│   1. You define an interface with @FeignClient                          │
│                                                                          │
│   2. Spring creates a proxy implementation at runtime                   │
│                                                                          │
│   3. When you call a method on the interface:                           │
│      a. Feign intercepts the call                                       │
│      b. Builds the HTTP request based on annotations                    │
│      c. If using Eureka, resolves service name to actual URL            │
│      d. Makes the HTTP call                                             │
│      e. Deserializes response and returns                               │
│                                                                          │
│   ┌─────────────────┐                        ┌─────────────────┐        │
│   │ Student Service │                        │ Address Service │        │
│   │                 │                        │                 │        │
│   │  ┌───────────┐  │    REST Call via       │  ┌───────────┐  │        │
│   │  │ Controller│  │    OpenFeign           │  │ Controller│  │        │
│   │  └─────┬─────┘  │  ─────────────────▶    │  └─────┬─────┘  │        │
│   │        │        │                        │        │        │        │
│   │        ▼        │                        │        ▼        │        │
│   │  ┌───────────┐  │                        │  ┌───────────┐  │        │
│   │  │  Service  │  │                        │  │  Service  │  │        │
│   │  └─────┬─────┘  │                        │  └─────┬─────┘  │        │
│   │        │        │                        │        │        │        │
│   │        ▼        │                        │        ▼        │        │
│   │  ┌───────────┐  │                        │  ┌───────────┐  │        │
│   │  │  Feign    │  │                        │  │Repository │  │        │
│   │  │  Client   │──┼────────────────────────┼──│           │  │        │
│   │  │(Interface)│  │                        │  └───────────┘  │        │
│   │  └───────────┘  │                        │                 │        │
│   │                 │                        │                 │        │
│   └─────────────────┘                        └─────────────────┘        │
│                                                                          │
└─────────────────────────────────────────────────────────────────────────┘
```

## Dependency

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```

## Setup and Configuration

### Step 1: Enable Feign Clients

For enabling Feign in the main service, we need to add an annotation `@EnableFeignClients` below the `@SpringBootApplication`:

```java
@SpringBootApplication
@EnableFeignClients("com.feignclients")  // Package where Feign clients are located
public class StudentServiceApplication {
    public static void main(String[] args) {
        SpringApplication.run(StudentServiceApplication.class, args);
    }
}
```

**Explanation:**
- `@EnableFeignClients` tells Spring to scan for interfaces annotated with `@FeignClient`
- The package parameter specifies where to look for Feign client interfaces
- Spring will create proxy implementations for these interfaces at runtime

### Step 2: Create Feign Client Interface

As Feign is an **interface**, we need to declare and define it:

```java
// Using direct URL (when NOT using Eureka)
@FeignClient(
    url = "${address.service.url}",      // URL from application.properties
    value = "address-feign-client",       // Unique name for this client
    path = "/api/address"                 // Base path for all endpoints
)
public interface AddressFeignClient {
    
    @GetMapping("/getById/{id}")
    AddressResponse getById(@PathVariable("id") Long id);
    
    @PostMapping("/create")
    AddressResponse create(@RequestBody AddressRequest request);
    
    @PutMapping("/update/{id}")
    AddressResponse update(@PathVariable("id") Long id, 
                          @RequestBody AddressRequest request);
    
    @DeleteMapping("/delete/{id}")
    void delete(@PathVariable("id") Long id);
}
```

**@FeignClient Attributes Explained:**

| Attribute | Description | Example |
|-----------|-------------|---------|
| `value` | Unique name for the Feign client (required) | `"address-feign-client"` |
| `url` | Base URL of the target service | `"http://localhost:8081"` |
| `path` | Common path prefix for all methods | `"/api/address"` |
| `name` | Same as `value`, used for service discovery | `"address-service"` |
| `fallback` | Fallback class for circuit breaker | `AddressFallback.class` |

### Step 3: Application Properties

```properties
# When NOT using Eureka - specify the direct URL
address.service.url=http://localhost:8081
```

### Step 4: Use Feign Client in Service

```java
@Service
public class StudentService {
    
    @Autowired
    private AddressFeignClient addressFeignClient;  // Just inject like any bean!
    
    @Autowired
    private StudentRepository studentRepository;
    
    public StudentResponse getStudentWithAddress(Long studentId) {
        // Get student from database
        Student student = studentRepository.findById(studentId)
            .orElseThrow(() -> new StudentNotFoundException(studentId));
        
        // Call Address Service using Feign Client - looks like a local method call!
        AddressResponse address = addressFeignClient.getById(student.getAddressId());
        
        return new StudentResponse(student, address);
    }
}
```

**Key Points:**
- Feign client is injected like any Spring bean
- Method calls look like local calls but actually make HTTP requests
- No need to handle HTTP response codes, serialization/deserialization manually

---

# 3. Service Discovery & Registry (Eureka)

## What is Eureka?

**Eureka is a Service Discovery and Registry server.** Each Microservice registers itself with Spring Cloud Eureka. Services communicate using **service names** instead of hardcoded URLs.

```
┌─────────────────────────────────────────────────────────────────────────┐
│                    WHY SERVICE DISCOVERY?                                │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                          │
│  PROBLEM WITHOUT SERVICE DISCOVERY:                                      │
│  ══════════════════════════════════                                      │
│                                                                          │
│  • Hardcoded URLs: http://192.168.1.100:8081/api/address                │
│  • What if IP changes? → Update all services manually                   │
│  • What if port changes? → Update all services manually                 │
│  • What if new instance added? → Update all services manually           │
│  • No dynamic scaling possible                                          │
│                                                                          │
│  SOLUTION WITH EUREKA:                                                   │
│  ═════════════════════                                                   │
│                                                                          │
│  • Services register with names: "address-service"                      │
│  • Other services call by name: http://address-service/api/address      │
│  • Eureka resolves name to actual IP:PORT                               │
│  • New instances auto-register                                          │
│  • Failed instances auto-deregister                                     │
│  • Dynamic scaling works seamlessly                                     │
│                                                                          │
└─────────────────────────────────────────────────────────────────────────┘
```

## How Eureka Works

```
┌─────────────────────────────────────────────────────────────────────────┐
│                         EUREKA ARCHITECTURE                              │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                          │
│                     ┌───────────────────────┐                           │
│                     │    EUREKA SERVER      │                           │
│                     │     (Port 8761)       │                           │
│                     │                       │                           │
│                     │  ┌─────────────────┐  │                           │
│                     │  │ Service Registry│  │                           │
│                     │  │                 │  │                           │
│                     │  │ student-service │◀─┼── Heartbeat every 30s    │
│                     │  │   → 8080        │  │                           │
│                     │  │   → 8081        │  │                           │
│                     │  │                 │  │                           │
│                     │  │ address-service │◀─┼── Heartbeat every 30s    │
│                     │  │   → 9090        │  │                           │
│                     │  │   → 9091        │  │                           │
│                     │  │                 │  │                           │
│                     │  │ api-gateway     │  │                           │
│                     │  │   → 8765        │  │                           │
│                     │  └─────────────────┘  │                           │
│                     └───────────┬───────────┘                           │
│                                 │                                        │
│         ┌───────────────────────┼───────────────────────┐               │
│         │                       │                       │               │
│         ▼                       ▼                       ▼               │
│   ┌───────────┐           ┌───────────┐           ┌───────────┐        │
│   │ Student   │ Register  │ Address   │ Register  │ Course    │        │
│   │ Service   │──────────▶│ Service   │──────────▶│ Service   │        │
│   │           │           │           │           │           │        │
│   │ "I am     │           │ "I am     │           │ "I am     │        │
│   │ student-  │           │ address-  │           │ course-   │        │
│   │ service   │           │ service   │           │ service   │        │
│   │ at :8080" │           │ at :9090" │           │ at :7070" │        │
│   └───────────┘           └───────────┘           └───────────┘        │
│                                                                          │
│   REGISTRATION FLOW:                                                     │
│   ══════════════════                                                     │
│   1. Service starts and registers with Eureka (name + host + port)      │
│   2. Eureka stores this info in its registry                            │
│   3. Service sends heartbeat every 30 seconds                           │
│   4. If no heartbeat for 90 seconds, Eureka removes the service         │
│   5. Other services query Eureka to find service locations              │
│                                                                          │
└─────────────────────────────────────────────────────────────────────────┘
```

## Eureka Server Setup

### Step 1: Dependency

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
</dependency>
```

### Step 2: Enable Eureka Server

Add `@EnableEurekaServer` below the `@SpringBootApplication`:

```java
@SpringBootApplication
@EnableEurekaServer
public class EurekaServerApplication {
    public static void main(String[] args) {
        SpringApplication.run(EurekaServerApplication.class, args);
    }
}
```

### Step 3: Eureka Server Properties

**Important:** Eureka Server registers itself also with itself, and we don't want that as it is NOT a Microservice. We need to prevent this with the below configuration:

```properties
server.port=8761
spring.application.name=eureka-server

# IMPORTANT: Prevent Eureka Server from registering itself
eureka.client.register-with-eureka=false
eureka.client.fetch-registry=false
```

**Configuration Explained:**

| Property | Description | Why Needed |
|----------|-------------|------------|
| `register-with-eureka=false` | Don't register this server as a client | Eureka Server is not a microservice |
| `fetch-registry=false` | Don't fetch registry from other Eureka servers | Single Eureka server setup |

### Step 4: Access Eureka Dashboard

Open browser: `http://localhost:8761`

You'll see a dashboard showing all registered services.

## Eureka Client Setup (Microservices)

### Step 1: Dependency

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```

### Step 2: Enable Eureka Client

Add `@EnableEurekaClient` annotation to client MSs like student or address:

```java
@SpringBootApplication
@EnableEurekaClient
public class StudentServiceApplication {
    public static void main(String[] args) {
        SpringApplication.run(StudentServiceApplication.class, args);
    }
}
```

### Step 3: Client Properties

```properties
server.port=8080
spring.application.name=student-service

# Eureka Server URL - where to register
eureka.client.service-url.defaultZone=http://localhost:8761/eureka

# Hostname configuration (prevents UnknownHostException)
eureka.instance.hostname=localhost
```

## Updated Feign Client (With Eureka)

For the OpenFeign, just **change the service name**, rest is same as below:

```java
// BEFORE (without Eureka) - Using URL
@FeignClient(url = "${address.service.url}", value = "address-feign-client", path = "/api/address")

// AFTER (with Eureka) - Using service name only!
@FeignClient(value = "address-service", path = "/api/address")
public interface AddressFeignClient {
    
    @GetMapping("/getById/{id}")
    AddressResponse getById(@PathVariable("id") Long id);
}
```

**Key Difference:**
- No more `url` attribute needed!
- `value` now contains the **service name** registered in Eureka
- Eureka will resolve `address-service` to actual `http://192.168.1.100:9090`

---

# 4. Client Side Load Balancer

## What is Load Balancing?

**Load Balancer distributes incoming requests across multiple service instances.** When you have multiple instances of a microservice running (for high availability and performance), the load balancer ensures requests are distributed evenly.

```
┌─────────────────────────────────────────────────────────────────────────┐
│                      WHY LOAD BALANCING?                                 │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                          │
│  SCENARIO: address-service has 2 instances running                      │
│  ──────────────────────────────────────────────────                      │
│                                                                          │
│  WITHOUT Load Balancer:                                                 │
│  ═══════════════════════                                                 │
│  All requests go to Instance 1 → Instance 1 overloaded!                 │
│  Instance 2 sits idle                                                   │
│                                                                          │
│  WITH Load Balancer:                                                    │
│  ═══════════════════                                                     │
│  Request 1 → Instance 1                                                 │
│  Request 2 → Instance 2                                                 │
│  Request 3 → Instance 1                                                 │
│  Request 4 → Instance 2                                                 │
│  ... (Balanced distribution!)                                           │
│                                                                          │
└─────────────────────────────────────────────────────────────────────────┘
```

## Load Balancing Algorithm: Round-Robin

**Load Balancer follows the Round-Robin method.** 

```
┌─────────────────────────────────────────────────────────────────────────┐
│                      ROUND-ROBIN ALGORITHM                               │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                          │
│  Suppose we have 4 requests for 2 instances of address-service:        │
│                                                                          │
│                      ┌───────────────────┐                              │
│                      │  Student Service  │                              │
│                      │                   │                              │
│                      │  ┌─────────────┐  │                              │
│                      │  │Load Balancer│  │                              │
│                      │  └──────┬──────┘  │                              │
│                      └─────────┼─────────┘                              │
│                                │                                         │
│              ┌─────────────────┴─────────────────┐                      │
│              │                                   │                      │
│              ▼                                   ▼                      │
│      ┌─────────────┐                     ┌─────────────┐               │
│      │  Address    │                     │  Address    │               │
│      │  Service    │                     │  Service    │               │
│      │ Instance A  │                     │ Instance B  │               │
│      │  :8081      │                     │  :8082      │               │
│      └─────────────┘                     └─────────────┘               │
│                                                                          │
│  Request Distribution:                                                  │
│  ═════════════════════                                                   │
│  Request 1 ──▶ Instance A                                               │
│  Request 2 ──▶ Instance B                                               │
│  Request 3 ──▶ Instance A  (back to first)                              │
│  Request 4 ──▶ Instance B                                               │
│                                                                          │
│  The load balancer rotates through instances in order.                  │
│                                                                          │
└─────────────────────────────────────────────────────────────────────────┘
```

## Dependency

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-loadbalancer</artifactId>
</dependency>
```

## Load Balancer Configuration

For applying load balancing to Feign client, we have to make a LoadBalancer class as below:

```java
@Configuration
@LoadBalancerClient(value = "address-service", configuration = AddressServiceLoadBalancerConfig.class)
public class AddressServiceLoadBalancerConfig {
    
    @LoadBalanced
    @Bean
    public Feign.Builder feignBuilder() {
        // Load balance the Feign client for address-service
        // as we are using Feign client for calling the services
        return Feign.builder();
    }
}
```

**Explanation:**
- `@LoadBalancerClient(value = "address-service")` - Applies load balancing for calls to address-service
- `@LoadBalanced` - Enables client-side load balancing on the Feign builder
- This ensures that when multiple instances of address-service are available, calls are distributed among them

## Alternative: Using RestTemplate with Load Balancer

```java
@Configuration
public class RestTemplateConfig {
    
    @LoadBalanced  // This enables load balancing!
    @Bean
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }
}

// Usage in Service
@Service
public class StudentService {
    
    @Autowired
    private RestTemplate restTemplate;
    
    public AddressResponse getAddress(Long id) {
        // Use SERVICE NAME instead of URL!
        return restTemplate.getForObject(
            "http://address-service/api/address/getById/" + id,
            AddressResponse.class
        );
    }
}
```

> **Important Note:** API Gateway handles client-side load balancing for us automatically, so we have no longer need the separate LoadBalancer configuration. We can comment the load balancer code when using API Gateway.

---

# 5. API Gateway

## What is API Gateway?

**API Gateway is the entry point of each of the Microservices.** All calls first come to API Gateway to perform some common tasks, like authentication, logging, rate limiting, etc.

```
┌─────────────────────────────────────────────────────────────────────────┐
│                      WHY API GATEWAY?                                    │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                          │
│  WITHOUT API Gateway:                                                   │
│  ═════════════════════                                                   │
│                                                                          │
│  • Client needs to know all service URLs                                │
│  • Each service implements its own authentication                       │
│  • No centralized logging                                               │
│  • No rate limiting                                                     │
│  • CORS issues with each service                                        │
│                                                                          │
│  WITH API Gateway:                                                      │
│  ═══════════════════                                                     │
│                                                                          │
│  • Single entry point for all clients                                   │
│  • Centralized authentication/authorization                             │
│  • Centralized logging and monitoring                                   │
│  • Rate limiting at one place                                           │
│  • Request/Response transformation                                      │
│  • Load balancing                                                       │
│  • SSL termination                                                      │
│                                                                          │
└─────────────────────────────────────────────────────────────────────────┘
```

## API Gateway Architecture

```
┌─────────────────────────────────────────────────────────────────────────┐
│                           API GATEWAY FLOW                               │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                          │
│     CLIENTS                      API GATEWAY                             │
│   ┌─────────┐              ┌───────────────────┐                        │
│   │   Web   │              │                   │                        │
│   │  App    │──────────────▶  ┌─────────────┐  │                        │
│   └─────────┘              │  │ PRE-FILTER  │  │                        │
│                            │  │             │  │                        │
│   ┌─────────┐              │  │ • Auth      │  │      ┌─────────────┐  │
│   │ Mobile  │──────────────▶  │ • Logging   │  │──────▶│  Student   │  │
│   │  App    │              │  │ • Validate  │  │      │  Service   │  │
│   └─────────┘              │  └─────────────┘  │      └─────────────┘  │
│                            │         │         │                        │
│   ┌─────────┐              │         ▼         │      ┌─────────────┐  │
│   │   IoT   │──────────────▶  ┌─────────────┐  │──────▶│  Address   │  │
│   │ Device  │              │  │   ROUTER    │  │      │  Service   │  │
│   └─────────┘              │  │ (Routes to  │  │      └─────────────┘  │
│                            │  │  services)  │  │                        │
│                            │  └─────────────┘  │      ┌─────────────┐  │
│                            │         │         │──────▶│  Course    │  │
│                            │         ▼         │      │  Service   │  │
│                            │  ┌─────────────┐  │      └─────────────┘  │
│                            │  │ POST-FILTER │  │                        │
│                            │  │             │  │                        │
│                            │  │ • Response  │  │                        │
│                            │  │   Logging   │  │                        │
│                            │  │ • Headers   │  │                        │
│                            │  └─────────────┘  │                        │
│                            │                   │                        │
│                            └───────────────────┘                        │
│                                                                          │
└─────────────────────────────────────────────────────────────────────────┘
```

## API Gateway Setup

### Step 1: Create a Different Service

Create a different service using Spring Initializr with **Gateway** and **Eureka Client** dependencies.

### Step 2: Dependencies

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-gateway</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```

### Step 3: Main Application

Add `@EnableEurekaClient` above the public class of `ApiGatewayApplication`:

```java
@SpringBootApplication
@EnableEurekaClient
public class ApiGatewayApplication {
    public static void main(String[] args) {
        SpringApplication.run(ApiGatewayApplication.class, args);
    }
}
```

### Step 4: Application Properties

```properties
server.port=8765
spring.application.name=api-gateway

# Eureka configuration
eureka.client.service-url.defaultZone=http://localhost:8761/eureka

# Gateway configuration for working with Eureka
spring.cloud.gateway.discovery.locator.enabled=true

# For reading the service name in lower case
spring.cloud.gateway.discovery.locator.lower-case-service-id=true
```

**Configuration Explained:**

| Property | Description |
|----------|-------------|
| `discovery.locator.enabled=true` | Enables Gateway to discover services from Eureka |
| `lower-case-service-id=true` | Converts service names to lowercase (student-service, not STUDENT-SERVICE) |

## Custom Filters

### Pre-Filter (Before routing to microservice)

If we want to add **pre-filter** in API-Gateway, then we can use below code:

```java
@Configuration
public class CustomFilter implements GlobalFilter {
    
    Logger logger = LoggerFactory.getLogger(CustomFilter.class);
    
    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        
        // Getting the request
        ServerHttpRequest request = exchange.getRequest();
        
        // Getting Authorization header - Auth Code or Logic
        logger.info("Authorization Header: {}", 
            request.getHeaders().getFirst("Authorization"));
        
        // Validate the token here
        String authHeader = request.getHeaders().getFirst("Authorization");
        if (authHeader == null || !authHeader.startsWith("Bearer ")) {
            // Return 401 Unauthorized
            exchange.getResponse().setStatusCode(HttpStatus.UNAUTHORIZED);
            return exchange.getResponse().setComplete();
        }
        
        // Return to responsible MS if pass, else we need to throw error
        return chain.filter(exchange);
    }
}
```

### Post-Filter (After response from microservice)

For **post-filter** just modify above code with lambda function as shown below:

```java
@Configuration
public class CustomPostFilter implements GlobalFilter {
    
    Logger logger = LoggerFactory.getLogger(CustomPostFilter.class);
    
    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        
        // Pre-filter logic here (if any)
        ServerHttpRequest request = exchange.getRequest();
        logger.info("Request Path: {}", request.getPath());
        
        // Post-filter logic using lambda (then + Mono.fromRunnable)
        return chain.filter(exchange).then(Mono.fromRunnable(() -> {
            
            // Get response
            ServerHttpResponse response = exchange.getResponse();
            
            // Log response status code
            logger.info("Response Status Code: {}", response.getStatusCode());
            
            // Add custom headers if needed
            response.getHeaders().add("X-Response-Time", 
                String.valueOf(System.currentTimeMillis()));
        }));
    }
}
```

**Explanation:**
- `chain.filter(exchange)` - Routes to the actual microservice
- `.then(Mono.fromRunnable(() -> {...}))` - Executes after response is received
- This allows you to inspect/modify the response

## Updated Feign Client (With API Gateway)

For the OpenFeign, just change the service name to `api-gateway`, rest is same:

```java
@FeignClient(value = "api-gateway")  // Route through API Gateway
public interface AddressFeignClient {
    
    // Include FULL PATH with service name!
    @GetMapping("/address-service/api/address/getById/{id}")
    AddressResponse getById(@PathVariable("id") Long id);
}
```

**Important Changes:**
- `value = "api-gateway"` - All calls go through gateway now
- Path includes `/address-service/...` - Gateway routes based on service name prefix

## Troubleshooting

If we face error like `java.net.UnknownHostException: failed to resolve....` then add below property in your student and address microservices:

```properties
eureka.instance.hostname=localhost
```

> **Note:** API Gateway is doing the client-side load balancing for us automatically, so we have no longer need the separate load balancer configuration. We can comment that code.

---

# 6. Fault Tolerance & Circuit Breaker (Resilience4j)

## What is Fault Tolerance?

**Fault Tolerance means that the downing of one Microservice will NOT impact the work of other Microservices.**

```
┌─────────────────────────────────────────────────────────────────────────┐
│                      WHY CIRCUIT BREAKER?                                │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                          │
│  PROBLEM - Cascading Failures:                                          │
│  ══════════════════════════════                                          │
│                                                                          │
│  1. Address Service goes down                                           │
│  2. Student Service keeps calling Address Service                       │
│  3. All Student Service threads get blocked waiting                     │
│  4. Student Service becomes unresponsive                                │
│  5. Services calling Student Service also fail                          │
│  6. Entire system collapses!                                            │
│                                                                          │
│  SOLUTION - Circuit Breaker:                                            │
│  ═══════════════════════════                                             │
│                                                                          │
│  1. Address Service goes down                                           │
│  2. Circuit Breaker detects failures                                    │
│  3. Circuit OPENS - no more calls to Address Service                    │
│  4. Fallback response returned immediately                              │
│  5. Student Service remains responsive                                  │
│  6. System continues working (degraded but functional)                  │
│                                                                          │
└─────────────────────────────────────────────────────────────────────────┘
```

## Circuit Breaker States

```
┌─────────────────────────────────────────────────────────────────────────┐
│                      CIRCUIT BREAKER STATES                              │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                          │
│                                                                          │
│   ┌─────────────────────────────────────────────────────────────────┐   │
│   │                                                                   │   │
│   │                         ┌──────────┐                             │   │
│   │                         │  CLOSED  │                             │   │
│   │                         │ (Normal) │                             │   │
│   │                         │          │                             │   │
│   │                         │ All calls│                             │   │
│   │                         │ go thru  │                             │   │
│   │                         └────┬─────┘                             │   │
│   │                              │                                    │   │
│   │            Failure Rate >= failureRateThreshold                  │   │
│   │                              │                                    │   │
│   │                              ▼                                    │   │
│   │                         ┌──────────┐                             │   │
│   │                         │   OPEN   │                             │   │
│   │                         │(Fail Fast│                             │   │
│   │                         │          │                             │   │
│   │                         │ All calls│                             │   │
│   │                         │ rejected │                             │   │
│   │                         │ Fallback │                             │   │
│   │                         │ returned │                             │   │
│   │                         └────┬─────┘                             │   │
│   │                              │                                    │   │
│   │           After waitDurationInOpenState = 30s                    │   │
│   │           (Automatic transition)                                  │   │
│   │                              │                                    │   │
│   │                              ▼                                    │   │
│   │                        ┌───────────┐                             │   │
│   │                        │ HALF-OPEN │                             │   │
│   │                        │  (Test)   │                             │   │
│   │                        │           │                             │   │
│   │                        │ Limited   │                             │   │
│   │                        │ calls     │                             │   │
│   │                        │ allowed   │                             │   │
│   │                        └─────┬─────┘                             │   │
│   │                              │                                    │   │
│   │          ┌───────────────────┴───────────────────┐               │   │
│   │          │                                       │               │   │
│   │    Success Rate OK                     Still Failing             │   │
│   │          │                                       │               │   │
│   │          ▼                                       ▼               │   │
│   │     ┌──────────┐                           ┌──────────┐         │   │
│   │     │  CLOSED  │                           │   OPEN   │         │   │
│   │     │  (Back   │                           │  (Stay   │         │   │
│   │     │   to     │                           │   open)  │         │   │
│   │     │  normal) │                           │          │         │   │
│   │     └──────────┘                           └──────────┘         │   │
│   │                                                                   │   │
│   └─────────────────────────────────────────────────────────────────┘   │
│                                                                          │
│   Health Indicator Mapping:                                              │
│   ═════════════════════════                                              │
│   • CLOSED    → UP                                                      │
│   • OPEN      → DOWN                                                    │
│   • HALF-OPEN → UNKNOWN                                                 │
│                                                                          │
└─────────────────────────────────────────────────────────────────────────┘
```

## Circuit Breaker Properties Explained

| Property | Description | Example |
|----------|-------------|---------|
| `slidingWindowSize` | Number of last calls to consider for failure rate calculation | 10 |
| `failureRateThreshold` | Percentage of failures that triggers OPEN state | 50 (50%) |
| `waitDurationInOpenState` | Time to wait in OPEN state before transitioning to HALF-OPEN | 30000 (30 sec) |
| `automaticTransitionFromOpenToHalfOpenEnabled` | Auto transition after wait time | true |
| `permittedNumberOfCallsInHalfOpenState` | Number of test calls allowed in HALF-OPEN | 5 |

## Dependencies

```xml
<dependency>
    <groupId>io.github.resilience4j</groupId>
    <artifactId>resilience4j-spring-boot2</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

**Why these dependencies?**
- `resilience4j-spring-boot2` - Core circuit breaker functionality
- `spring-boot-starter-aop` - Resilience4j uses Spring AOP internally
- `spring-boot-starter-actuator` - To monitor circuit breaker health

## Configuration Properties

In `application.properties` of main MS (student-service):

```properties
# Circuit Breaker Configuration for addressService
resilience4j.circuitbreaker.instances.addressService.sliding-window-size=10
resilience4j.circuitbreaker.instances.addressService.failure-rate-threshold=50
resilience4j.circuitbreaker.instances.addressService.wait-duration-in-open-state=30000
resilience4j.circuitbreaker.instances.addressService.automatic-transition-from-open-to-half-open-enabled=true
resilience4j.circuitbreaker.instances.addressService.permitted-number-of-calls-in-half-open-state=5
resilience4j.circuitbreaker.instances.addressService.allow-health-indicator-to-fail=true
resilience4j.circuitbreaker.instances.addressService.register-health-indicator=true

# Actuator Configuration for monitoring
management.health.circuitbreakers.enabled=true
management.endpoints.web.exposure.include=health
management.endpoint.health.show-details=always
```

## Implementation

Add `@CircuitBreaker` annotation above the method calling the other service using FeignClient. The **fallbackMethod** is used when circuit is open or call fails. We **must** define the fallbackMethod.

```java
@Service
public class StudentService {
    
    @Autowired
    private AddressFeignClient addressFeignClient;
    
    @Autowired
    private StudentRepository studentRepository;
    
    @CircuitBreaker(name = "addressService", fallbackMethod = "fallbackGetAddressById")
    public StudentResponse getStudentWithAddress(Long studentId) {
        Student student = studentRepository.findById(studentId)
            .orElseThrow(() -> new StudentNotFoundException(studentId));
        
        // This call is protected by Circuit Breaker
        AddressResponse address = addressFeignClient.getById(student.getAddressId());
        
        return new StudentResponse(student, address);
    }
    
    // FALLBACK METHOD - Called when circuit is OPEN or call fails
    // MUST have same return type + Exception parameter
    public StudentResponse fallbackGetAddressById(Long studentId, Exception ex) {
        log.error("Fallback triggered for studentId: {}, Error: {}", 
            studentId, ex.getMessage());
        
        Student student = studentRepository.findById(studentId)
            .orElseThrow(() -> new StudentNotFoundException(studentId));
        
        // Return response with default/cached address
        AddressResponse defaultAddress = new AddressResponse();
        defaultAddress.setMessage("Address service is currently unavailable");
        
        return new StudentResponse(student, defaultAddress);
    }
}
```

**Important Notes:**
- `name = "addressService"` must match the config property name
- Fallback method MUST have same return type as original method
- Fallback method takes same parameters + Exception at the end
- Resilience4j uses Spring AOP internally to wrap methods

---

# 7. Distributed Tracing (Sleuth & Zipkin)

## What is Distributed Tracing?

**Sleuth gives the complete flow of how MS is communicating with each other, whereas Zipkin is a UI tool to see this complete flow - from where to where your request is going.**

```
┌─────────────────────────────────────────────────────────────────────────┐
│                   TRACE ID vs SPAN ID                                    │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                          │
│   TRACE ID (T-1): Unique ID for ENTIRE request flow across ALL services│
│   SPAN ID (S-x): Unique ID for request WITHIN SAME service             │
│                                                                          │
│   ┌────────────────────────────────────────────────────────────────┐   │
│   │                                                                  │   │
│   │   Request ──▶ API Gateway ──▶ Student Service ──▶ Address Svc  │   │
│   │                                                                  │   │
│   │               T-1, S-1        T-1, S-2           T-1, S-3      │   │
│   │                                                                  │   │
│   │   • Trace ID (T-1) is SAME across all services                  │   │
│   │   • Span ID changes in each service                             │   │
│   │   • This helps us track the complete request journey            │   │
│   │                                                                  │   │
│   └────────────────────────────────────────────────────────────────┘   │
│                                                                          │
│   Example Log Output:                                                    │
│   ══════════════════                                                     │
│                                                                          │
│   [api-gateway,T-1,S-1] Received request /students/1                    │
│   [student-service,T-1,S-2] Processing student request                  │
│   [address-service,T-1,S-3] Fetching address for student 1              │
│   [student-service,T-1,S-2] Returning student response                  │
│   [api-gateway,T-1,S-1] Sending response to client                      │
│                                                                          │
│   Notice: T-1 is SAME everywhere - that's how we correlate logs!       │
│                                                                          │
└─────────────────────────────────────────────────────────────────────────┘
```

## Sleuth Setup

### Dependency

Add to **ALL microservices** (except the Eureka Server):

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-sleuth</artifactId>
</dependency>
```

### API Gateway Configuration

In API Gateway's `application.properties`:

```properties
# Required for API Gateway with Sleuth
spring.sleuth.reactor.instrumentation-type=decorate-on-each
```

## Zipkin Setup

**If we want to see the tracing in a UI, then we use Zipkin server.**

### Step 1: Start Zipkin Server

```bash
# Using Docker
docker run -d -p 9411:9411 openzipkin/zipkin

# Or download and run
java -jar zipkin-server.jar
```

### Step 2: Add Zipkin Dependency

Add to **ALL microservices** pom.xml:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-zipkin</artifactId>
</dependency>
```

### Step 3: Configure Zipkin URL

In main MS (student-service) and other services:

```properties
# Zipkin server URL
spring.zipkin.base-url=http://localhost:9411

# Optional: Send 100% of traces (default is 10%)
spring.sleuth.sampler.probability=1.0
```

### Step 4: Access Zipkin UI

Open browser: `http://localhost:9411`

## Zipkin Visualization

```
┌─────────────────────────────────────────────────────────────────────────┐
│                      ZIPKIN UI VISUALIZATION                             │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                          │
│   ┌─────────────────────────────────────────────────────────────────┐  │
│   │ TRACE: abc123xyz                                  Duration: 150ms│  │
│   │                                                                   │  │
│   │ ├─ api-gateway (S-1)                    [0ms ─────────── 150ms] │  │
│   │ │  └─ GET /student-service/students/1                           │  │
│   │ │                                                                 │  │
│   │ └─── student-service (S-2)              [20ms ────────── 140ms] │  │
│   │      │ └─ GET /api/students/1                                    │  │
│   │      │                                                            │  │
│   │      └─── address-service (S-3)         [50ms ───── 100ms]      │  │
│   │           └─ GET /api/address/5                                  │  │
│   │                                                                   │  │
│   └─────────────────────────────────────────────────────────────────┘  │
│                                                                          │
│   You can see:                                                          │
│   • Complete request flow                                               │
│   • Time spent in each service                                          │
│   • Which service is slow (bottleneck)                                  │
│   • Errors and exceptions                                               │
│                                                                          │
└─────────────────────────────────────────────────────────────────────────┘
```

---

# 8. Config Server

## What is Config Server?

**Config Server is a separate Spring Boot application which will read the properties from Git and help to pass it to our Microservices.** 

It provides a centralized place to manage common configurations for all microservices, like database properties, feature flags, etc.

```
┌─────────────────────────────────────────────────────────────────────────┐
│                      WHY CONFIG SERVER?                                  │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                          │
│  WITHOUT Config Server:                                                 │
│  ═══════════════════════                                                 │
│                                                                          │
│  • Each service has its own application.properties                      │
│  • Database URL changes? Update all services manually!                  │
│  • Password changes? Redeploy all services!                             │
│  • No version control for configurations                                │
│  • No environment-specific configurations (dev/test/prod)              │
│                                                                          │
│  WITH Config Server:                                                    │
│  ═════════════════════                                                   │
│                                                                          │
│  • All configurations in Git (version controlled)                       │
│  • Database URL changes? Update Git, services refresh!                  │
│  • Environment-specific configs (dev/test/prod)                         │
│  • Dynamic refresh without restart (using @RefreshScope)                │
│  • Single source of truth for all configs                               │
│                                                                          │
└─────────────────────────────────────────────────────────────────────────┘
```

## Config Server Architecture

```
┌─────────────────────────────────────────────────────────────────────────┐
│                         CONFIG SERVER ARCHITECTURE                       │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                          │
│   ┌─────────────────────────────────────────────────────────────────┐   │
│   │                                                                   │   │
│   │                    ┌────────────────────┐                        │   │
│   │                    │    GIT REPOSITORY  │                        │   │
│   │                    │                    │                        │   │
│   │                    │ • student-service  │                        │   │
│   │                    │   .properties      │                        │   │
│   │                    │ • student-service  │                        │   │
│   │                    │   _dev.properties  │                        │   │
│   │                    │ • student-service  │                        │   │
│   │                    │   _prod.properties │                        │   │
│   │                    │ • address-service  │                        │   │
│   │                    │   .properties      │                        │   │
│   │                    │ • application.yml  │                        │   │
│   │                    │   (common props)   │                        │   │
│   │                    │                    │                        │   │
│   │                    └─────────┬──────────┘                        │   │
│   │                              │                                    │   │
│   │                              │ Fetch configs                      │   │
│   │                              ▼                                    │   │
│   │                    ┌────────────────────┐                        │   │
│   │                    │   CONFIG SERVER    │                        │   │
│   │                    │    (Port 8888)     │                        │   │
│   │                    │                    │                        │   │
│   │                    │  Reads from Git    │                        │   │
│   │                    │  Serves to clients │                        │   │
│   │                    └─────────┬──────────┘                        │   │
│   │                              │                                    │   │
│   │           ┌──────────────────┼──────────────────┐                │   │
│   │           │                  │                  │                │   │
│   │           ▼                  ▼                  ▼                │   │
│   │   ┌─────────────┐    ┌─────────────┐    ┌─────────────┐         │   │
│   │   │  Student    │    │  Address    │    │  Course     │         │   │
│   │   │  Service    │    │  Service    │    │  Service    │         │   │
│   │   │             │    │             │    │             │         │   │
│   │   │ Fetches own │    │ Fetches own │    │ Fetches own │         │   │
│   │   │ properties  │    │ properties  │    │ properties  │         │   │
│   │   │ at startup  │    │ at startup  │    │ at startup  │         │   │
│   │   └─────────────┘    └─────────────┘    └─────────────┘         │   │
│   │                                                                   │   │
│   └─────────────────────────────────────────────────────────────────┘   │
│                                                                          │
└─────────────────────────────────────────────────────────────────────────┘
```

## Config Server Setup

### Step 1: Create Config Server Service

Create a different service using Spring Initializr with **Config Server** and **Eureka Discovery Client** dependencies.

### Step 2: Dependencies

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-server</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```

### Step 3: Enable Config Server

Add `@EnableConfigServer` & `@EnableEurekaClient` above the public class:

```java
@SpringBootApplication
@EnableConfigServer
@EnableEurekaClient
public class ConfigServerApplication {
    public static void main(String[] args) {
        SpringApplication.run(ConfigServerApplication.class, args);
    }
}
```

### Step 4: Config Server Properties

```properties
server.port=8888
spring.application.name=config-server

# Git Repository URL (Local or Remote)
# For local file system:
spring.cloud.config.server.git.uri=file:///D:/LocalRepo

# For remote Git:
# spring.cloud.config.server.git.uri=https://github.com/user/config-repo
# spring.cloud.config.server.git.username=user
# spring.cloud.config.server.git.password=password

# Eureka registration
eureka.client.service-url.defaultZone=http://localhost:8761/eureka
```

## Config Client Setup (Microservices)

### Step 1: Dependencies

For adding config server support in any MS, use below artifacts:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-bootstrap</artifactId>
</dependency>
```

### Step 2: Bootstrap Properties

Create `bootstrap.properties` (loads before application.properties):

```properties
spring.application.name=student-service

# Enable discovery of config server through Eureka
spring.cloud.config.discovery.enabled=true
spring.cloud.config.discovery.service-id=config-server

# If you are NOT using Eureka, specify URL directly:
# spring.cloud.config.uri=http://localhost:8888

# Profile (dev, test, prod)
spring.cloud.config.profile=dev
```

**Note:** Rest of the configuration will move to Git repository in serviceName.properties file.

## Git Repository Structure

```
ConfigRepo/
├── application.properties              # Common properties for ALL services
├── application-dev.properties          # Common DEV properties
├── application-prod.properties         # Common PROD properties
├── student-service.properties          # Student service specific
├── student-service-dev.properties      # Student service DEV profile
├── student-service-prod.properties     # Student service PROD profile
├── address-service.properties          # Address service specific
└── address-service-dev.properties      # Address service DEV profile
```

### Example: student-service-dev.properties

For adding profile (dev, test, prod), use below naming convention:

```properties
# student-service-dev.properties

# Database configuration for DEV
spring.datasource.url=jdbc:mysql://localhost:3306/student_dev
spring.datasource.username=dev_user
spring.datasource.password=dev_password

# Custom property
address.service.timeout=5000

# Feature flag
feature.new-ui.enabled=true

# Testing property
address.test=testing
```

## Dynamic Property Refresh

**For getting updated property values without restarting our MS:**

### Step 1: Add Actuator Dependency

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

### Step 2: Enable Refresh Endpoint

```properties
# Expose refresh endpoint
management.endpoints.web.exposure.include=refresh
```

### Step 3: Add @RefreshScope Annotation

Add `@RefreshScope` annotation where you use the property:

```java
@RestController
@RefreshScope  // This enables dynamic refresh!
public class StudentController {
    
    @Value("${address.service.timeout}")
    private int timeout;
    
    @Value("${address.test}")
    private String testValue;
    
    @GetMapping("/config")
    public Map<String, Object> getConfig() {
        Map<String, Object> config = new HashMap<>();
        config.put("timeout", timeout);
        config.put("testValue", testValue);
        return config;
    }
}
```

### Step 4: Trigger Refresh

After updating properties in Git, call the refresh endpoint:

```bash
# POST request to refresh endpoint
curl -X POST http://localhost:8080/actuator/refresh

# Response shows which properties changed
["address.service.timeout", "address.test"]
```

**The properties will be refreshed WITHOUT restarting the microservice!**

---

# 📋 Quick Reference Summary

## Dependencies Summary

| Component | Artifact ID |
|-----------|-------------|
| OpenFeign | `spring-cloud-starter-openfeign` |
| Eureka Server | `spring-cloud-starter-netflix-eureka-server` |
| Eureka Client | `spring-cloud-starter-netflix-eureka-client` |
| Load Balancer | `spring-cloud-starter-loadbalancer` |
| API Gateway | `spring-cloud-starter-gateway` |
| Resilience4j | `resilience4j-spring-boot2` |
| Sleuth | `spring-cloud-starter-sleuth` |
| Zipkin | `spring-cloud-starter-zipkin` |
| Config Server | `spring-cloud-config-server` |
| Config Client | `spring-cloud-config-client` + `spring-cloud-starter-bootstrap` |

## Annotations Summary

| Annotation | Purpose | Where to Use |
|------------|---------|--------------|
| `@EnableFeignClients` | Enable Feign client support | Main Application class |
| `@FeignClient` | Declare a REST client | Interface |
| `@EnableEurekaServer` | Enable Eureka Server | Eureka Server main class |
| `@EnableEurekaClient` | Register service with Eureka | Microservice main class |
| `@LoadBalanced` | Enable load balancing | Bean method (RestTemplate/Feign) |
| `@LoadBalancerClient` | Configure load balancer for service | Configuration class |
| `@EnableConfigServer` | Enable Config Server | Config Server main class |
| `@CircuitBreaker` | Apply circuit breaker pattern | Service method |
| `@RefreshScope` | Enable dynamic property refresh | Controller/Service class |

## Port Conventions

| Service | Default Port |
|---------|-------------|
| Eureka Server | 8761 |
| Config Server | 8888 |
| API Gateway | 8765 |
| Zipkin Server | 9411 |
| Microservices | 8080, 8081, 8082, etc. |

## Complete Request Flow

```
┌─────────────────────────────────────────────────────────────────────────┐
│                    COMPLETE REQUEST FLOW                                 │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                          │
│   1. Services start and REGISTER with EUREKA                            │
│   2. Services fetch CONFIGURATION from CONFIG SERVER                    │
│   3. Client sends request to API GATEWAY                                │
│   4. Gateway applies PRE-FILTERS (auth, logging)                        │
│   5. Gateway routes to appropriate SERVICE (via Eureka)                 │
│   6. Service calls other services via OPENFEIGN                         │
│   7. LOAD BALANCER distributes calls across instances                   │
│   8. CIRCUIT BREAKER handles failures gracefully                        │
│   9. SLEUTH adds trace/span IDs for tracking                           │
│   10. ZIPKIN collects and visualizes traces                            │
│   11. Gateway applies POST-FILTERS (response logging)                   │
│   12. Response returns to client                                        │
│                                                                          │
└─────────────────────────────────────────────────────────────────────────┘
```

---

*This comprehensive guide covers Spring Cloud components for building production-ready microservices architecture with detailed theoretical explanations.*
