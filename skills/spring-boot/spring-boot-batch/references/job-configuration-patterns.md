# Job Configuration Patterns

This guide covers the key patterns for configuring and running Spring Batch jobs.

## Basic Job Configuration

### Simple Linear Job (Sequential Steps)

The most common pattern: execute steps one after another in order.

```java
@Configuration
public class BatchConfiguration {

    @Bean
    public Job importUserJob(JobRepository jobRepository, Step step1, Step step2) {
        return new JobBuilder("importUserJob", jobRepository)
                .start(step1)
                .next(step2)
                .build();
    }
}
```

**When to use:** Most batch jobs follow a linear flow where each step depends on the previous step completing successfully.

### Conditional Job Flow

Execute different steps based on the exit status of the previous step.

```java
@Bean
public Job conditionalJob(JobRepository jobRepository, Step step1, Step step2, Step step3) {
    return new JobBuilder("conditionalJob", jobRepository)
            .start(step1)
            .on("COMPLETED").to(step2)           // if step1 completes successfully
            .from(step1).on("FAILED").to(step3)  // if step1 fails
            .end()
            .build();
}
```

**Exit statuses:**
- `COMPLETED` - Step/job completed successfully
- `FAILED` - Step/job failed
- `STOPPED` - Step/job was stopped by operator
- `UNKNOWN` - Step/job status is unknown

**When to use:** When you need different behaviors based on step outcomes (e.g., retry on error, fallback steps).

### Parallel Steps

Execute multiple steps in parallel (if infrastructure supports it).

```java
@Bean
public Job parallelStepsJob(JobRepository jobRepository, Step step1, Step step2, Step step3) {
    return new JobBuilder("parallelStepsJob", jobRepository)
            .start(step1)
            .split(new SimpleAsyncTaskExecutor())
            .add(step2, step3)
            .end()
            .build();
}
```

**When to use:** When you have independent steps that can run concurrently to improve throughput.

## JobRepository Configuration

### Default In-Memory Repository

Good for testing or simple development.

```java
@Configuration
@EnableBatchProcessing
public class BatchConfiguration {
    // JobRepository is auto-created with @EnableBatchProcessing
}
```

### Database-Backed Repository

For production use, store job metadata in a database.

**Spring Boot Auto-Configuration (Recommended):**
```yaml
spring:
  batch:
    jdbc:
      initialize-database: always
      platform: postgresql  # or mysql, h2, etc.
```

Spring Boot automatically creates the required tables.

**Manual Configuration:**
```java
@Configuration
@EnableBatchProcessing
public class BatchConfiguration {

    @Bean
    public JobRepository jobRepository(DataSource dataSource,
                                       PlatformTransactionManager transactionManager) {
        JobRepositoryFactoryBean factory = new JobRepositoryFactoryBean();
        factory.setDataSource(dataSource);
        factory.setTransactionManager(transactionManager);
        factory.setDatabaseType("postgresql");
        factory.setTablePrefix("BATCH_");
        return factory.getObject();
    }
}
```

**Table prefix:** Customize table names (default: `BATCH_`).

### Job Metadata Tables

Spring Batch creates these tables automatically:
- `BATCH_JOB_INSTANCE` - Job instances
- `BATCH_JOB_EXECUTION` - Job execution records
- `BATCH_STEP_EXECUTION` - Step execution records
- `BATCH_JOB_EXECUTION_PARAMS` - Job parameters
- `BATCH_JOB_EXECUTION_CONTEXT` - Job execution context
- `BATCH_STEP_EXECUTION_CONTEXT` - Step execution context

## JobLauncher Configuration

### Synchronous JobLauncher (Default)

Waits for job completion before returning.

```java
@Configuration
public class BatchConfiguration {

    @Bean
    public JobLauncher jobLauncher(JobRepository jobRepository) {
        SimpleJobLauncher launcher = new SimpleJobLauncher();
        launcher.setJobRepository(jobRepository);
        return launcher;
    }
}
```

**When to use:** Most common for scheduled batch jobs.

### Asynchronous JobLauncher

Returns immediately without waiting for job completion.

```java
@Configuration
public class BatchConfiguration {

    @Bean
    public JobLauncher jobLauncher(JobRepository jobRepository) {
        SimpleJobLauncher launcher = new SimpleJobLauncher();
        launcher.setJobRepository(jobRepository);
        launcher.setTaskExecutor(new SimpleAsyncTaskExecutor());
        return launcher;
    }
}
```

**When to use:** Web-based job triggering where you don't want to block the HTTP request.

## Running a Job

### Running via Command Line

Spring Boot applications automatically configure CommandLineJobRunner.

```bash
java -jar batch-application.jar \
  --spring.batch.job.names=importUserJob \
  --file.name=input.csv
```

### Running Programmatically

```java
@Component
public class JobRunner implements CommandLineRunner {

    private final JobLauncher jobLauncher;
    private final Job importUserJob;

    public JobRunner(JobLauncher jobLauncher, Job importUserJob) {
        this.jobLauncher = jobLauncher;
        this.importUserJob = importUserJob;
    }

    @Override
    public void run(String... args) throws Exception {
        JobParameters jobParameters = new JobParametersBuilder()
                .addString("fileName", "input.csv")
                .addDate("runDate", new Date())
                .toJobParameters();

        JobExecution execution = jobLauncher.run(importUserJob, jobParameters);
        System.out.println("Job Status: " + execution.getStatus());
    }
}
```

### Running via REST Endpoint

```java
@RestController
@RequestMapping("/batch")
public class JobController {

    private final JobLauncher jobLauncher;
    private final Job importUserJob;

    public JobController(JobLauncher jobLauncher, Job importUserJob) {
        this.jobLauncher = jobLauncher;
        this.importUserJob = importUserJob;
    }

    @PostMapping("/import")
    public ResponseEntity<Long> startJob() throws Exception {
        JobParameters jobParameters = new JobParametersBuilder()
                .addDate("runDate", new Date())
                .toJobParameters();

        JobExecution execution = jobLauncher.run(importUserJob, jobParameters);
        return ResponseEntity.ok(execution.getId());
    }

    @GetMapping("/status/{executionId}")
    public ResponseEntity<Map<String, Object>> getJobStatus(@PathVariable Long executionId) {
        // Implementation to fetch job status from JobRepository
        return ResponseEntity.ok(/* status map */);
    }
}
```

## Job Parameters

Job parameters allow jobs to be run with different configurations.

### Defining Job Parameters

```java
@Bean
public Job importUserJob(JobRepository jobRepository, Step step1) {
    return new JobBuilder("importUserJob", jobRepository)
            .start(step1)
            .build();
}
```

### Accessing Job Parameters in Steps/Processors

```java
@Component
public class PersonItemProcessor implements ItemProcessor<Person, Person> {

    @Value("#{jobParameters['mode']}")
    private String mode;

    @Override
    public Person process(Person person) {
        if ("uppercase".equals(mode)) {
            return new Person(
                person.firstName().toUpperCase(),
                person.lastName().toUpperCase()
            );
        }
        return person;
    }
}
```

### Using Job Parameters

```java
JobParameters jobParameters = new JobParametersBuilder()
        .addString("fileName", "input.csv")
        .addString("mode", "uppercase")
        .addDate("runDate", new Date())
        .toJobParameters();

JobExecution execution = jobLauncher.run(importUserJob, jobParameters);
```

**Note:** Only String, Long, Date, and Double parameters are supported.

## Job Restart Capability

### Preventing Job Restart

```java
@Bean
public Job nonRestartableJob(JobRepository jobRepository, Step step1) {
    return new JobBuilder("nonRestartableJob", jobRepository)
            .preventRestart()  // This job cannot be restarted
            .start(step1)
            .build();
}
```

### Allowing Job Restart

```java
@Bean
public Job restartableJob(JobRepository jobRepository, Step step1) {
    return new JobBuilder("restartableJob", jobRepository)
            .start(step1)
            .build();  // Restartable by default
}
```

**When restart is useful:** Long-running jobs that may fail and need to be resumed from where they left off.

## Error Handling at Job Level

### Job Execution Listener

```java
@Component
public class JobCompletionNotificationListener extends JobExecutionListenerSupport {

    @Override
    public void afterJob(JobExecution jobExecution) {
        if (jobExecution.getStatus() == BatchStatus.COMPLETED) {
            log.info("Job finished successfully");
        } else if (jobExecution.getStatus() == BatchStatus.FAILED) {
            log.error("Job failed");
            jobExecution.getFailureExceptions().forEach(e -> log.error("Cause: " + e));
        }
    }
}
```

### Using the Listener in Job

```java
@Bean
public Job jobWithListener(JobRepository jobRepository,
                           Step step1,
                           JobCompletionNotificationListener listener) {
    return new JobBuilder("jobWithListener", jobRepository)
            .listener(listener)
            .start(step1)
            .build();
}
```

## Best Practices for Job Configuration

1. **Use Spring Boot auto-configuration** - Let Spring Boot handle most configuration automatically
2. **Externalize configuration** - Use `application.properties` or `application.yml` for paths, databases, etc.
3. **Make jobs idempotent** - Jobs should produce same result if run multiple times with same parameters
4. **Use meaningful job names** - Names should clearly describe what the job does
5. **Enable job restart** - For production jobs that may fail and need recovery
6. **Use transaction manager** - Proper transaction management for data consistency
7. **Add listeners** - Monitor job lifecycle events for logging and alerting
8. **Use job parameters** - Don't hardcode configuration in job definitions
