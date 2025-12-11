# Java Annotations in ACE Migration Project - Interview Guide

## Overview
This document explains all annotations used in the ACE Migration Extractor/Injector project, organized by category for interview preparation.

---

## 📋 Table of Contents
1. [Spring Framework Annotations](#spring-framework-annotations)
2. [JPA/Hibernate Annotations](#jpahibernate-annotations)
3. [Lombok Annotations](#lombok-annotations)
4. [Spring Retry Annotations](#spring-retry-annotations)
5. [Jakarta EE Annotations](#jakarta-ee-annotations)

---

## 1. Spring Framework Annotations

### `@SpringBootApplication`
**Location:** `AceMigExtratorApplication.java`

**Purpose:** 
- Main Spring Boot annotation that combines:
  - `@Configuration` - Marks class as configuration source
  - `@EnableAutoConfiguration` - Enables Spring Boot auto-configuration
  - `@ComponentScan` - Scans for Spring components in package

**Interview Answer:**
"This annotation marks the main application class. It enables Spring Boot auto-configuration and component scanning. It's equivalent to using all three annotations separately."

**Example:**
```java
@SpringBootApplication
public class AceMigExtratorApplication {
    public static void main(String[] args) {
        SpringApplication.run(AceMigExtratorApplication.class, args);
    }
}
```

---

### `@Service`
**Location:** All service classes (`DataCopyService`, `InjectorService`, `DataSourceService`, etc.)

**Purpose:**
- Marks a class as a Spring service component
- Auto-detected by component scanning
- Used for business logic layer

**Interview Answer:**
"`@Service` indicates this is a service layer component. Spring's component scanning automatically creates a bean instance. It's functionally similar to `@Component` but semantically indicates business logic."

**Example:**
```java
@Service
@Profile({"!injector", "extractor"})
public class DataCopyService {
    // Business logic here
}
```

**Common Interview Questions:**
- **Q:** What's the difference between `@Service`, `@Component`, and `@Repository`?
- **A:** They're all `@Component` specializations. `@Service` = business logic, `@Repository` = data access, `@Component` = generic Spring-managed bean. Semantically different but functionally similar.

---

### `@Repository`
**Location:** All repository interfaces (`AceMigrationMasterRepo`, `ExtractLogRepo`, etc.)

**Purpose:**
- Marks a class as a Data Access Object (DAO)
- Provides exception translation (SQLException → DataAccessException)
- Used for database operations

**Interview Answer:**
"`@Repository` marks Spring Data JPA repositories. It provides automatic exception translation from database-specific exceptions to Spring's DataAccessException hierarchy. It's discovered by `@EnableJpaRepositories`."

**Example:**
```java
@Repository
@EnableJpaRepositories
public interface AceMigrationMasterRepo extends JpaRepository<AceMigMaster, Long> {
    // Repository methods
}
```

---

### `@Autowired`
**Location:** Throughout service classes for dependency injection

**Purpose:**
- Performs dependency injection by type
- Can be used on fields, constructors, or setters
- Spring finds matching bean and injects it

**Interview Answer:**
"`@Autowired` enables dependency injection. Spring finds a bean of matching type and injects it. If multiple beans match, use `@Qualifier` to specify which one. Prefer constructor injection for immutability and testability."

**Example:**
```java
@Autowired
private AceMigrationMasterRepo aceMigrationMasterRepo;

@Autowired
private DataSource dataSource;
```

**Common Interview Questions:**
- **Q:** What if multiple beans match the type?
- **A:** Use `@Qualifier("beanName")` to specify which bean to inject.
- **Q:** What if no bean is found?
- **A:** Throws `NoSuchBeanDefinitionException` unless `required=false`.

---

### `@Value`
**Location:** Service classes for property injection

**Purpose:**
- Injects values from property files (application.properties)
- Supports SpEL (Spring Expression Language)
- Can have default values

**Interview Answer:**
"`@Value` injects values from properties files. The `${property.name}` syntax reads from properties, and `:defaultValue` provides a fallback. Useful for configuration injection."

**Example:**
```java
@Value("${ace.injection.max.threads:100}")
private int maxThreads;

@Value("${cban.list.table.name}")
private String cbanListTableName;

@Value("${spring.datasource.hikari.maximum-pool-size}")
private Integer maxPoolSize;
```

**Interview Tip:** Mention that `@Value` uses SpEL and can reference environment variables, system properties, or other beans.

---

### `@Profile`
**Location:** Service classes (`@Profile({"!injector", "extractor"})`)

**Purpose:**
- Activates beans only when specific profiles are active
- Uses `!` for negation (e.g., `!injector` means "not injector")
- Enables environment-specific configurations

**Interview Answer:**
"`@Profile` conditionally activates beans based on active Spring profiles. We use it to have different implementations for extractor vs injector modes. The `!` means negation - `!injector` means 'not injector profile'."

**Example:**
```java
@Service
@Profile({"!injector", "extractor"})  // Active when extractor profile is active
public class DataCopyService {
    // Extractor-specific code
}

@Service
@Profile({"injector", "!extractor"})  // Active when injector profile is active
public class InjectorService {
    // Injector-specific code
}
```

**Common Interview Questions:**
- **Q:** How do you activate profiles?
- **A:** `--spring.profiles.active=extractor` or `spring.profiles.active` system property
- **Q:** Can a bean have multiple profiles?
- **A:** Yes, bean is active if ANY profile matches (OR logic)

---

### `@EnableScheduling`
**Location:** `AceMigExtratorApplication.java`

**Purpose:**
- Enables Spring's scheduled task execution
- Required for `@Scheduled` annotation to work
- Configures task scheduler

**Interview Answer:**
"`@EnableScheduling` enables Spring's scheduling infrastructure. Without it, `@Scheduled` methods won't execute. It creates a default thread pool for scheduled tasks."

**Example:**
```java
@SpringBootApplication
@EnableScheduling
public class AceMigExtratorApplication {
    // Application code
}
```

---

### `@Scheduled`
**Location:** `DataCopySchedulerService.java`, `InjectorSchedulerService.java`

**Purpose:**
- Marks a method to be executed on a schedule
- Supports `fixedRate`, `fixedDelay`, `cron` expressions
- Method must return void and take no parameters

**Interview Answer:**
"`@Scheduled` marks a method for periodic execution. `fixedRate` runs at fixed intervals regardless of execution time. `fixedDelay` waits for completion before next execution. Values can come from properties."

**Example:**
```java
@Scheduled(fixedRateString = "${extract-fixed-rate}")  // Every 5 minutes
public void extractScheduler() {
    // Scheduled execution
}

@Scheduled(fixedDelayString = "${ace.injection.scheduler.delay:300000}")  // 5 min default
public void processInjection() {
    // Scheduled execution
}
```

**Common Interview Questions:**
- **Q:** Difference between `fixedRate` and `fixedDelay`?
- **A:** `fixedRate` = fixed interval between starts. `fixedDelay` = fixed delay after completion.
- **Q:** Can you use properties in `@Scheduled`?
- **A:** Yes, use `fixedRateString` instead of `fixedRate` for property placeholders.

---

### `@EnableJpaRepositories`
**Location:** Repository interfaces

**Purpose:**
- Enables Spring Data JPA repositories
- Scans for repository interfaces
- Configures JPA repository infrastructure

**Interview Answer:**
"`@EnableJpaRepositories` enables Spring Data JPA. It scans for interfaces extending `JpaRepository` and creates proxy implementations. Usually placed on configuration class or main application class."

**Example:**
```java
@EnableJpaRepositories
@Repository
public interface AceMigrationMasterRepo extends JpaRepository<AceMigMaster, Long> {
    // Repository methods
}
```

---

### `@Component`
**Location:** `StartupValidator.java`

**Purpose:**
- Generic Spring component annotation
- Marks class for component scanning
- Base annotation for `@Service`, `@Repository`, `@Controller`

**Interview Answer:**
"`@Component` is the base annotation for Spring-managed beans. Other annotations like `@Service` and `@Repository` are specializations. Use `@Component` for generic utility classes."

**Example:**
```java
@Component
@Slf4j
public class StartupValidator {
    // Utility class
}
```

---

### `@Transactional`
**Location:** Service methods, repository methods

**Purpose:**
- Declares transaction boundaries
- Ensures ACID properties
- Can specify isolation level, propagation, rollback rules

**Interview Answer:**
"`@Transactional` marks a method to run within a database transaction. If method completes successfully, transaction commits. If exception occurs, transaction rolls back. Can be applied at class or method level."

**Example:**
```java
@Transactional
public void updateAceMigMasterExSt(String dbName) {
    // Database operations
}

@Modifying
@Transactional
@Query("UPDATE AceMigMaster...")
int updateProcIndById(@Param("id") Long id, @Param("procInd") String procInd);
```

**Common Interview Questions:**
- **Q:** What's the default propagation behavior?
- **A:** `REQUIRED` - joins existing transaction or creates new one.
- **Q:** What exceptions trigger rollback?
- **A:** Runtime exceptions (unchecked). Checked exceptions don't rollback by default.
- **Q:** Can you have nested transactions?
- **A:** Yes, use `PROPAGATION_REQUIRES_NEW` for new transaction.

---

### `@Query`
**Location:** Repository interfaces

**Purpose:**
- Defines custom JPQL or native SQL queries
- Can use named parameters with `@Param`
- Supports `nativeQuery = true` for SQL

**Interview Answer:**
"`@Query` defines custom queries in repositories. JPQL queries are database-agnostic. Set `nativeQuery=true` for raw SQL. Parameters use `:paramName` syntax with `@Param` annotation."

**Example:**
```java
@Query("SELECT a FROM AceMigMaster a WHERE a.procInd = :procInd")
List<AceMigMaster> findByProcInd(@Param("procInd") String procInd);

@Query(value = "SELECT * FROM ACE_MIG_MASTER WHERE PROC_IND = :procInd", 
       nativeQuery = true)
List<AceMigMaster> findByProcIndNative(@Param("procInd") String procInd);
```

**Common Interview Questions:**
- **Q:** Difference between JPQL and native SQL?
- **A:** JPQL works with entity classes, native SQL is database-specific.
- **Q:** How do you handle pagination?
- **A:** Add `Pageable` parameter and return `Page<T>` instead of `List<T>`.

---

### `@Modifying`
**Location:** Repository interfaces (UPDATE/DELETE queries)

**Purpose:**
- Indicates query modifies database (UPDATE/DELETE)
- Required for modifying queries
- Must be used with `@Transactional`

**Interview Answer:**
"`@Modifying` marks queries that modify data (UPDATE/DELETE/INSERT). Spring Data JPA requires this annotation for non-SELECT queries. Must be combined with `@Transactional`."

**Example:**
```java
@Modifying
@Transactional
@Query("UPDATE AceMigMaster a SET a.procInd = :procInd WHERE a.iterationId = :id")
int updateProcIndById(@Param("id") Long id, @Param("procInd") String procInd);
```

---

### `@Param`
**Location:** Repository method parameters

**Purpose:**
- Binds method parameters to query parameters
- Used with `@Query` annotation
- Maps `:paramName` in query to method parameter

**Interview Answer:**
"`@Param` binds method parameters to named parameters in `@Query`. The parameter name in query (`:procInd`) must match the `@Param` value."

**Example:**
```java
@Query("SELECT a FROM AceMigMaster a WHERE a.procInd = :procInd")
List<AceMigMaster> findByProcInd(@Param("procInd") String procInd);
```

---

## 2. JPA/Hibernate Annotations

### `@Entity`
**Location:** All entity classes (`AceMigMaster`, `AceSrcExtractMapping`, etc.)

**Purpose:**
- Marks a class as a JPA entity
- Maps class to database table
- Required for JPA persistence

**Interview Answer:**
"`@Entity` marks a POJO as a JPA entity. It maps the class to a database table. The class must have a no-arg constructor and an `@Id` field. JPA manages the entity lifecycle."

**Example:**
```java
@Entity
@Table(name = "ACE_MIG_MASTER")
@Data
public class AceMigMaster {
    @Id
    private Long iterationId;
    // Other fields
}
```

---

### `@Table`
**Location:** Entity classes

**Purpose:**
- Specifies database table name
- Overrides default naming convention
- Can specify schema and catalog

**Interview Answer:**
"`@Table` specifies the database table name. Without it, JPA uses the class name. Useful when table name differs from class name or uses different schema."

**Example:**
```java
@Entity
@Table(name = "ACE_MIG_MASTER")
public class AceMigMaster {
    // Fields
}

@Entity
@Table(name = "ace_src_extract_mapping")
public class AceSrcExtractMapping {
    // Fields
}
```

---

### `@Id`
**Location:** Entity classes (primary key fields)

**Purpose:**
- Marks primary key field
- Required for every entity
- Can be simple or composite

**Interview Answer:**
"`@Id` marks the primary key field. Every entity must have exactly one `@Id`. Can be combined with `@GeneratedValue` for auto-generation. For composite keys, use `@EmbeddedId` or `@IdClass`."

**Example:**
```java
@Id
@Column(name = "ITERATION_ID")
@GeneratedValue(strategy = GenerationType.IDENTITY)
private Long iterationId;
```

---

### `@GeneratedValue`
**Location:** Primary key fields

**Purpose:**
- Configures primary key generation strategy
- Strategies: IDENTITY, SEQUENCE, TABLE, AUTO
- IDENTITY = auto-increment

**Interview Answer:**
"`@GeneratedValue` configures how the primary key is generated. `IDENTITY` uses database auto-increment (MySQL, SQL Server). `SEQUENCE` uses database sequences (Oracle). `AUTO` lets JPA choose."

**Example:**
```java
@Id
@GeneratedValue(strategy = GenerationType.IDENTITY)
private Long id;

@Id
@GeneratedValue(strategy = GenerationType.IDENTITY)
private Long iterationId;
```

**Common Interview Questions:**
- **Q:** What's the difference between IDENTITY and SEQUENCE?
- **A:** IDENTITY = auto-increment (MySQL), SEQUENCE = database sequence (Oracle). SEQUENCE allows batch inserts.
- **Q:** Can you customize generation?
- **A:** Yes, use `@SequenceGenerator` or `@TableGenerator` for custom strategies.

---

### `@Column`
**Location:** Entity fields

**Purpose:**
- Maps entity field to database column
- Can specify column name, length, nullable, unique
- Overrides default naming

**Interview Answer:**
"`@Column` maps entity fields to database columns. Specifies column name, nullable, length, unique constraints. Without it, JPA uses field name. Useful for snake_case to camelCase mapping."

**Example:**
```java
@Column(name = "LGC_CUSTOMER_ID")
private Long lgcCustomerId;

@Column(name = "extract_sql", columnDefinition = "CLOB")
private String extractSql;

@Column(name = "PROC_IND")
private String procInd;
```

**Common Interview Questions:**
- **Q:** What's `columnDefinition`?
- **A:** Allows specifying exact SQL column type (e.g., CLOB for large text).
- **Q:** When to use `@Column`?
- **A:** When column name differs from field name, or when you need constraints (length, nullable, unique).

---

### `@IdClass`
**Location:** `ExtractLog.java` (composite primary key)

**Purpose:**
- Defines composite primary key
- References separate class containing key fields
- Alternative to `@EmbeddedId`

**Interview Answer:**
"`@IdClass` handles composite primary keys. Creates a separate class for the key and marks multiple `@Id` fields. Simpler than `@EmbeddedId` when you need direct access to key fields."

**Example:**
```java
@Entity
@Table(name = "TLG_EXTRACTION_LOG")
@IdClass(ExtractLogPK.class)
public class ExtractLog {
    @Id
    @Column(name = "EXTRACTION_ID")
    private Long extractionId;
    
    @Id
    @Column(name = "TGT_DB_NAME")
    private String tgtDbName;
    
    @Id
    @Column(name = "EXT_SRC_TABLE_NAME")
    private String extSrcTableName;
}

@NoArgsConstructor
@AllArgsConstructor
@EqualsAndHashCode
public class ExtractLogPK implements Serializable {
    private Long extractionId;
    private String tgtDbName;
    private String extSrcTableName;
}
```

---

## 3. Lombok Annotations

### `@Data`
**Location:** Entity classes

**Purpose:**
- Generates getters, setters, toString, equals, hashCode
- Combines `@Getter`, `@Setter`, `@ToString`, `@EqualsAndHashCode`
- Reduces boilerplate code

**Interview Answer:**
"`@Data` is a Lombok annotation that generates getters, setters, toString, equals, and hashCode methods. It dramatically reduces boilerplate code. Works at compile time via annotation processing."

**Example:**
```java
@Entity
@Table(name = "ACE_MIG_MASTER")
@Data
public class AceMigMaster {
    @Id
    private Long iterationId;
    // Getters/setters/toString/equals/hashCode generated automatically
}
```

**Common Interview Questions:**
- **Q:** How does Lombok work?
- **A:** Uses annotation processing at compile time to generate bytecode. No runtime dependency.
- **Q:** What if you need custom getter/setter?
- **A:** Manually write the method - Lombok won't generate it if it exists.

---

### `@Slf4j`
**Location:** Service classes, application class

**Purpose:**
- Generates SLF4J logger field
- Creates `private static final Logger log = LoggerFactory.getLogger(ClassName.class)`
- Simplifies logging setup

**Interview Answer:**
"`@Slf4j` generates a logger field for SLF4J. Instead of manually creating `Logger log = LoggerFactory.getLogger(ClassName.class)`, Lombok generates it automatically. Use `log.info()`, `log.error()`, etc."

**Example:**
```java
@Service
@Slf4j
public class DataCopyService {
    // Automatically generates: private static final Logger log = ...
    
    public void method() {
        log.info("Processing data");  // Use log directly
        log.error("Error occurred", e);
    }
}
```

---

### `@NoArgsConstructor`
**Location:** Entity classes, PK classes

**Purpose:**
- Generates no-argument constructor
- Required by JPA
- Can be combined with `@AllArgsConstructor`

**Interview Answer:**
"`@NoArgsConstructor` generates a no-argument constructor. Required by JPA entities. Can use `force = true` to initialize final fields with default values."

**Example:**
```java
@NoArgsConstructor
@AllArgsConstructor
@EqualsAndHashCode
public class ExtractLogPK {
    private Long extractionId;
    private String tgtDbName;
}
```

---

### `@AllArgsConstructor`
**Location:** Entity classes, PK classes

**Purpose:**
- Generates constructor with all fields
- Useful for initialization
- Cannot be used with `@NoArgsConstructor` if fields are final (unless force=true)

**Interview Answer:**
"`@AllArgsConstructor` generates a constructor accepting all fields as parameters. Useful for creating objects with all values at once. Often paired with `@NoArgsConstructor`."

**Example:**
```java
@NoArgsConstructor
@AllArgsConstructor
@EqualsAndHashCode
public class ExtractLogPK {
    private Long extractionId;
    private String tgtDbName;
}
```

---

### `@EqualsAndHashCode`
**Location:** PK classes

**Purpose:**
- Generates `equals()` and `hashCode()` methods
- Important for collections (Set, Map keys)
- Can exclude fields with `exclude`

**Interview Answer:**
"`@EqualsAndHashCode` generates equals and hashCode methods. Critical for entities used in Sets or as Map keys. Can exclude fields that shouldn't affect equality."

**Example:**
```java
@EqualsAndHashCode
public class ExtractLogPK {
    private Long extractionId;
    private String tgtDbName;
}
```

---

### `@ToString`
**Location:** Entity classes

**Purpose:**
- Generates `toString()` method
- Useful for debugging
- Can exclude fields

**Interview Answer:**
"`@ToString` generates a toString method. Useful for logging and debugging. Can exclude fields with `exclude` parameter. `@Data` includes this automatically."

**Example:**
```java
@Entity
@Data  // Includes @ToString
@ToString  // Explicit (redundant with @Data)
public class AceSrcExtractMapping {
    // Fields
}
```

---

### `@Setter` / `@Getter`
**Location:** Entity classes (when not using `@Data`)

**Purpose:**
- Generates getters or setters
- Can be applied at class or field level
- Part of `@Data` functionality

**Interview Answer:**
"`@Getter` and `@Setter` generate getter and setter methods respectively. Can be applied at class level (all fields) or field level (specific field). `@Data` includes both."

---

## 4. Spring Retry Annotations

### `@EnableRetry`
**Location:** `RetryableBatchExecService.java`

**Purpose:**
- Enables Spring Retry functionality
- Required for `@Retryable` and `@Recover` to work
- Configures retry infrastructure

**Interview Answer:**
"`@EnableRetry` enables Spring Retry's AOP proxy creation. Without it, `@Retryable` methods won't retry. It creates proxies around methods to handle retry logic."

**Example:**
```java
@Service
@EnableRetry
public class RetryableBatchExecService {
    @Retryable(...)
    public void executeBatch(...) {
        // Retry logic
    }
}
```

---

### `@Retryable`
**Location:** `RetryableBatchExecService.java`

**Purpose:**
- Marks method for retry on failure
- Configures retry behavior (max attempts, exceptions, backoff)
- Automatic retry on specified exceptions

**Interview Answer:**
"`@Retryable` marks a method to automatically retry on failure. Configures which exceptions trigger retry, maximum attempts, and backoff strategy. Uses AOP proxies to intercept method calls."

**Example:**
```java
@Retryable(
    retryFor = {
        SQLException.class,
        CannotGetJdbcConnectionException.class,
        DataAccessException.class
    },
    maxAttempts = 3,
    backoff = @Backoff(delay = 2000)
)
public void executeBatch(...) {
    // Will retry up to 3 times with 2 second delay
}
```

**Common Interview Questions:**
- **Q:** What exceptions trigger retry?
- **A:** Only exceptions specified in `retryFor`. Default is all exceptions.
- **Q:** How does backoff work?
- **A:** Waits specified delay before retry. Can use exponential backoff with `multiplier`.

---

### `@Backoff`
**Location:** Within `@Retryable`

**Purpose:**
- Configures retry delay strategy
- Supports fixed delay or exponential backoff
- Can specify multiplier for exponential growth

**Interview Answer:**
"`@Backoff` configures the delay between retries. `delay` sets fixed delay in milliseconds. `multiplier` enables exponential backoff (delay doubles each retry). `maxDelay` caps maximum delay."

**Example:**
```java
@Retryable(
    maxAttempts = 3,
    backoff = @Backoff(delay = 2000)  // 2 second delay
)

// Exponential backoff example:
backoff = @Backoff(delay = 1000, multiplier = 2, maxDelay = 10000)
// Retries: 1s, 2s, 4s, 8s (capped at 10s)
```

---

### `@Recover`
**Location:** `RetryableBatchExecService.java`

**Purpose:**
- Marks recovery method called after all retries fail
- Must match `@Retryable` method signature
- Handles final failure scenario

**Interview Answer:**
"`@Recover` marks a fallback method called after all retries are exhausted. Method signature must match the `@Retryable` method. Used for logging failures, sending alerts, or alternative processing."

**Example:**
```java
@Retryable(...)
public void executeBatch(...) {
    // Retry logic
}

@Recover
public void recoverBatch(RuntimeException e, String dbName, Long extractionId, ...) {
    // Called after 3 failed attempts
    log.error("Failed after retries: {}", e.getMessage());
    // Log failure or send alert
}
```

**Common Interview Questions:**
- **Q:** When is `@Recover` called?
- **A:** After all retry attempts fail. It's the final fallback.
- **Q:** Can you have multiple `@Recover` methods?
- **A:** Yes, Spring matches by exception type and method signature.

---

## 5. Jakarta EE Annotations

### `@PostConstruct`
**Location:** Service classes (`DataSourceService`, `InjectorService`, `DataCopyService`)

**Purpose:**
- Marks method to execute after dependency injection
- Called after constructor and field injection
- Used for initialization logic

**Interview Answer:**
"`@PostConstruct` marks a method to run after Spring finishes dependency injection. Useful for initialization that requires injected dependencies. Runs once per bean instance, after constructor and field injection."

**Example:**
```java
@Autowired
private AceSrcExtractMappingRepository mappingRepository;

@PostConstruct
void init() {
    // Initialize dependency mappings after injection
    List<AceSrcExtractMapping> mappings = mappingRepository.findAll();
    this.createDependencyMapping(mappings);
}
```

**Common Interview Questions:**
- **Q:** When does `@PostConstruct` run?
- **A:** After all dependencies are injected, before bean is ready for use.
- **Q:** What if `@PostConstruct` throws exception?
- **A:** Bean creation fails, exception propagated to application context.
- **Q:** Difference between `@PostConstruct` and constructor?
- **A:** Constructor runs first, but dependencies aren't injected yet. `@PostConstruct` runs after injection.

---

## 6. CommandLineRunner Interface

### `implements CommandLineRunner`
**Location:** `AceMigExtratorApplication.java`

**Purpose:**
- Spring Boot interface for command-line applications
- `run()` method executes after application context loads
- Receives command-line arguments

**Interview Answer:**
"`CommandLineRunner` is a Spring Boot interface for running code after application startup. The `run()` method receives command-line arguments. Useful for CLI applications where you want to execute logic after Spring context is ready."

**Example:**
```java
@SpringBootApplication
public class AceMigExtratorApplication implements CommandLineRunner {
    
    @Override
    public void run(String... args) throws Exception {
        // Execute after Spring context loads
        if (args.length > 0) {
            String command = args[0];
            // Process command
        }
    }
}
```

**Common Interview Questions:**
- **Q:** Difference between `CommandLineRunner` and `ApplicationRunner`?
- **A:** `CommandLineRunner` receives `String[]`, `ApplicationRunner` receives `ApplicationArguments` with parsed options.
- **Q:** Can you have multiple runners?
- **A:** Yes, implement `@Order` to control execution order.

---

## 📊 Summary Table

| Category | Annotation | Purpose | Used In |
|----------|-----------|---------|---------|
| **Spring Core** | `@SpringBootApplication` | Main app configuration | Main class |
| | `@Service` | Business logic component | Service classes |
| | `@Repository` | Data access component | Repository interfaces |
| | `@Component` | Generic Spring bean | Utility classes |
| | `@Autowired` | Dependency injection | Service fields |
| | `@Value` | Property injection | Configuration fields |
| | `@Profile` | Conditional bean activation | Service classes |
| | `@EnableScheduling` | Enable scheduled tasks | Main class |
| | `@Scheduled` | Schedule method execution | Scheduler services |
| | `@Transactional` | Transaction management | Service/repository methods |
| | `@Query` | Custom queries | Repository methods |
| | `@Modifying` | Modifying queries | Repository UPDATE/DELETE |
| | `@Param` | Query parameter binding | Repository parameters |
| **JPA** | `@Entity` | JPA entity marker | Entity classes |
| | `@Table` | Table name mapping | Entity classes |
| | `@Id` | Primary key | Entity PK fields |
| | `@GeneratedValue` | PK generation strategy | Entity PK fields |
| | `@Column` | Column mapping | Entity fields |
| | `@IdClass` | Composite primary key | Entities with composite PK |
| **Lombok** | `@Data` | Generate getters/setters/etc | Entity classes |
| | `@Slf4j` | Generate logger | Service classes |
| | `@NoArgsConstructor` | Generate no-arg constructor | Entity/PK classes |
| | `@AllArgsConstructor` | Generate all-args constructor | Entity/PK classes |
| | `@EqualsAndHashCode` | Generate equals/hashCode | PK classes |
| **Spring Retry** | `@EnableRetry` | Enable retry functionality | Service classes |
| | `@Retryable` | Mark method for retry | Service methods |
| | `@Backoff` | Configure retry delay | Within @Retryable |
| | `@Recover` | Recovery method | Fallback methods |
| **Jakarta EE** | `@PostConstruct` | Post-injection initialization | Service init methods |

---

## 🎯 Interview Tips

1. **Know the Categories:** Group annotations by purpose (Spring, JPA, Lombok, etc.)
2. **Explain Purpose:** Always explain what the annotation does, not just name it
3. **Give Examples:** Reference actual code from the project
4. **Common Questions:** Be ready for "difference between" questions
5. **Best Practices:** Mention when to use each annotation
6. **Combinations:** Explain how annotations work together (e.g., `@Modifying` + `@Transactional`)

---

## 💡 Key Interview Questions & Answers

### Q: What's the difference between `@Service`, `@Component`, and `@Repository`?
**A:** All are `@Component` specializations. `@Service` = business logic, `@Repository` = data access with exception translation, `@Component` = generic bean. Functionally similar, semantically different.

### Q: Why use `@Transactional` on service methods?
**A:** Ensures business operations execute atomically. If any step fails, entire operation rolls back. Keeps transaction boundaries at business logic level.

### Q: How does `@Scheduled` work?
**A:** Uses Spring AOP to create proxies. Task scheduler executes methods at specified intervals. Requires `@EnableScheduling`. Supports cron, fixedRate, fixedDelay.

### Q: What's the purpose of `@Profile`?
**A:** Conditionally activates beans based on active profiles. Enables different configurations for different environments (dev, prod, extractor, injector).

### Q: How does Lombok work?
**A:** Compile-time annotation processing. Generates bytecode for getters/setters/etc. No runtime dependency. Reduces boilerplate code significantly.

### Q: Explain `@Retryable` and `@Recover`.
**A:** `@Retryable` automatically retries failed methods. `@Recover` provides fallback after all retries fail. Useful for transient failures (network, database connections).

### Q: When to use `@PostConstruct`?
**A:** For initialization that requires injected dependencies. Runs after injection completes, before bean is used. Perfect for cache initialization, dependency mapping setup.

---

## 📝 Quick Reference

**Most Important Annotations:**
1. `@SpringBootApplication` - Main app
2. `@Service` - Business logic
3. `@Repository` - Data access
4. `@Autowired` - Dependency injection
5. `@Transactional` - Transaction management
6. `@Entity` - JPA entities
7. `@Data` - Lombok boilerplate reduction
8. `@Scheduled` - Periodic tasks
9. `@Retryable` - Retry logic
10. `@PostConstruct` - Initialization

**Remember:** Annotations are metadata. They configure behavior but don't execute code themselves - frameworks use reflection/annotation processing to apply behavior.



