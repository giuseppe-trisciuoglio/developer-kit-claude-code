---
name: spring-boot-batch
description: Configure and implement Spring Batch jobs with chunk-oriented and tasklet steps. Design job and step execution flows with error handling. Use when building batch processing applications, configuring job/step components, or implementing batch processing patterns in Spring Boot.
allowed-tools: Read, Write, Bash
category: backend
tags: [spring-boot, batch, jobs, chunk-processing, tasklets]
version: 1.0.0
---

# Spring Boot Batch

## Overview

This skill enables the configuration and implementation of Spring Batch jobs in Spring Boot applications. Spring Batch is a framework for building robust, scalable batch processing applications that handle large volumes of data with proper transaction management, error handling, and job restart capabilities.

This skill covers the fundamental patterns for:
- Configuring Jobs and Steps
- Setting up ItemReaders, ItemProcessors, and ItemWriters
- Implementing chunk-oriented and tasklet-based step processing
- Error handling with skip and retry logic
- Job execution and management

## When to Use This Skill

Use this skill when:
- Building batch processing jobs that read data from files or databases
- Configuring chunk-oriented processing (read-process-write patterns)
- Implementing tasklet-based steps for simple operations
- Setting up job repositories and job launchers
- Handling errors with skip and retry logic
- Managing job parameters and execution context

## Core Concepts

Start with understanding the fundamental building blocks:

### Job and Step Structure
A **Job** is a container for one or more **Steps**. Each Step encapsulates an independent phase of batch processing and may contain:
- **ItemReader** - reads data one item at a time
- **ItemProcessor** - transforms each item (optional)
- **ItemWriter** - writes items in batches

### Two Step Types
1. **Chunk-Oriented Step** - Read-Process-Write pattern (most common)
2. **Tasklet Step** - Single task execution

Refer to [Spring Batch Core Concepts](references/api_reference.md) for detailed domain language, architecture, and best practices.

## Job Configuration Patterns

### Creating a Simple Job

The most common pattern: configure sequential steps with Spring Boot auto-configuration.

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

**Process:**
1. Inject `JobRepository` and `Step` beans
2. Use `JobBuilder` to configure the job
3. Define step flow with `.start()` and `.next()`
4. Build and return the job

### Conditional Step Execution

Route to different steps based on the previous step's exit status.

```java
@Bean
public Job conditionalJob(JobRepository jobRepository, Step step1, Step step2, Step step3) {
    return new JobBuilder("conditionalJob", jobRepository)
            .start(step1)
            .on("COMPLETED").to(step2)
            .from(step1).on("FAILED").to(step3)
            .end()
            .build();
}
```

**Exit statuses:** `COMPLETED`, `FAILED`, `STOPPED`, `UNKNOWN`

For additional job configuration patterns including parallel execution, error handling, and advanced configurations, refer to [Job Configuration Patterns](references/job-configuration-patterns.md).

## Step Configuration Patterns

### Basic Chunk-Oriented Step

The standard pattern: define chunk size, reader, processor (optional), and writer.

```java
@Bean
public Step importStep(JobRepository jobRepository,
                       PlatformTransactionManager transactionManager,
                       ItemReader<Person> reader,
                       ItemProcessor<Person, Person> processor,
                       ItemWriter<Person> writer) {
    return new StepBuilder("importStep", jobRepository)
            .<Person, Person> chunk(10)  // Process 10 items per transaction
            .reader(reader)
            .processor(processor)
            .writer(writer)
            .build();
}
```

**Key decisions:**
- **Chunk size** - Balance memory usage vs. transaction overhead (typically 100-1000)
- **Reader** - Where data comes from (file, database, etc.)
- **Processor** - Optional transformation (skip if no transformation needed)
- **Writer** - Where processed data goes

### ItemReader: Reading from CSV

```java
@Bean
public FlatFileItemReader<Person> csvItemReader() {
    return new FlatFileItemReaderBuilder<Person>()
            .name("csvItemReader")
            .resource(new FileSystemResource("input/sample-data.csv"))
            .delimited()
            .names("firstName", "lastName")
            .targetType(Person.class)
            .build();
}
```

### ItemProcessor: Transforming Items

```java
@Component
public class PersonItemProcessor implements ItemProcessor<Person, Person> {

    @Override
    public Person process(final Person person) throws Exception {
        // Return null to skip an item
        // Return modified item to process it
        return new Person(
            person.firstName().toUpperCase(),
            person.lastName().toUpperCase()
        );
    }
}
```

### ItemWriter: Writing to Database

```java
@Bean
public JdbcBatchItemWriter<Person> jdbcItemWriter(DataSource dataSource) {
    return new JdbcBatchItemWriterBuilder<Person>()
            .itemSqlParameterSourceProvider(new BeanPropertyItemSqlParameterSourceProvider<>())
            .sql("INSERT INTO people (first_name, last_name) VALUES (:firstName, :lastName)")
            .dataSource(dataSource)
            .build();
}
```

### Error Handling: Skip and Retry Logic

```java
@Bean
public Step robustStep(JobRepository jobRepository,
                       PlatformTransactionManager transactionManager,
                       ItemReader<Person> reader,
                       ItemWriter<Person> writer) {
    return new StepBuilder("robustStep", jobRepository)
            .<Person, Person> chunk(10)
            .reader(reader)
            .writer(writer)
            .skip(PermanentException.class)      // Skip items causing this exception
            .skipLimit(10)                        // Skip up to 10 items
            .retry(TemporaryException.class)     // Retry if this exception occurs
            .retryLimit(3)                        // Retry up to 3 times
            .build();
}
```

### Tasklet Step: Single Task Execution

```java
@Component
public class MyTasklet implements Tasklet {

    @Override
    public RepeatStatus execute(StepContribution contribution, ChunkContext chunkContext)
            throws Exception {
        System.out.println("Executing tasklet");
        return RepeatStatus.FINISHED;
    }
}

@Bean
public Step taskletStep(JobRepository jobRepository,
                        PlatformTransactionManager transactionManager,
                        MyTasklet myTasklet) {
    return new StepBuilder("taskletStep", jobRepository)
            .tasklet(myTasklet, transactionManager)
            .build();
}
```

For comprehensive step configuration patterns, ItemReader/Processor/Writer implementations, and listener configuration, refer to [Step Configuration Patterns](references/step-configuration-patterns.md).

## Running Jobs

### Using Spring Boot Auto-Configuration

Enable batch processing with `@EnableBatchProcessing` annotation.

```java
@SpringBootApplication
@EnableBatchProcessing
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

### Programmatic Job Execution

```java
@Component
public class JobRunner implements CommandLineRunner {

    private final JobLauncher jobLauncher;
    private final Job importUserJob;

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

### Via REST Endpoint

```java
@RestController
@RequestMapping("/batch")
public class JobController {

    private final JobLauncher jobLauncher;
    private final Job importUserJob;

    @PostMapping("/import")
    public ResponseEntity<Long> startJob() throws Exception {
        JobParameters jobParameters = new JobParametersBuilder()
                .addDate("runDate", new Date())
                .toJobParameters();

        JobExecution execution = jobLauncher.run(importUserJob, jobParameters);
        return ResponseEntity.ok(execution.getId());
    }
}
```

## Best Practices

1. **Use Spring Boot auto-configuration** - Let Spring Boot handle JobRepository and JobLauncher setup
2. **Externalize configuration** - Keep file paths and database queries in `application.properties`
3. **Use appropriate chunk sizes** - Start with 100 and adjust based on performance testing
4. **Keep processors stateless** - Don't maintain state between item processing calls
5. **Design jobs to be idempotent** - Same parameters should produce same result if run twice
6. **Add listeners for monitoring** - Track job and step execution for visibility
7. **Use provided readers and writers** - Avoid custom implementations unless necessary
8. **Enable job restart** - For production jobs that may fail and need recovery

## References

- [Spring Batch Core Concepts](references/api_reference.md) - Fundamental domain objects, architecture, and components
- [Job Configuration Patterns](references/job-configuration-patterns.md) - Job configuration, repositories, and launchers
- [Step Configuration Patterns](references/step-configuration-patterns.md) - Complete step patterns, readers, processors, writers, and error handling
