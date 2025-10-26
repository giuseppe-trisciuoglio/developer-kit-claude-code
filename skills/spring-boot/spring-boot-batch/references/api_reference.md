# Spring Batch Core Concepts

## What is Spring Batch?

Spring Batch is a lightweight, comprehensive batch processing framework designed to enable the development of robust batch applications vital for the daily operations of enterprise systems. It provides reusable functions that are essential in processing large volumes of records, including:

- Logging and tracing
- Transaction management
- Job processing statistics
- Job restart capability
- Skip logic
- Resource management

## Domain Language of Batch

### Job

A Job is an encapsulation of an entire batch process. It is a container for Steps and contains:

- Job name
- Step ordering definitions
- Whether the job is restartable
- Job-level properties and parameters

**Configuration Pattern:**
```java
@Bean
public Job importUserJob(JobRepository jobRepository, Step step1, Step step2) {
    return new JobBuilder("importUserJob", jobRepository)
            .start(step1)
            .next(step2)
            .build();
}
```

### Step

A Step is a domain object that encapsulates an independent, sequential phase of a batch job. Every Job is made up of one or more Steps. A Step contains all of the information necessary to define and control the actual batch processing.

A Step can contain:
- **ItemReader**: Reads data from a source
- **ItemProcessor**: Transforms the data
- **ItemWriter**: Writes the processed data

**Two Types of Steps:**
1. **Chunk-oriented Step** (Read-Process-Write in chunks)
2. **Tasklet Step** (Single task execution)

### JobRepository

The JobRepository is the persistence mechanism for all of the above stereotypes. It provides CRUD operations for Job, Step, and JobExecution domain objects.

### JobLauncher

The JobLauncher is the mechanism by which a Job is started. It takes a Job and JobParameters and executes the Job.

### JobExecution and StepExecution

When a Job is run, a JobExecution is created. JobExecution represents a single attempt to run a Job. Similarly, when a Step is run, a StepExecution is created.

## Architecture Overview

Spring Batch uses a layered architecture consisting of three tiers:

1. **Infrastructure layer** - Common infrastructure shared by both the application and the batch processing
2. **Core layer** - Core classes necessary to launch and control a batch job
3. **Application layer** - Contains the code written by developers (job configuration, custom code)

## Key Components

### ItemReader
Reads data one item at a time from a particular source (file, database, queue, etc.).

**Provided implementations:**
- FlatFileItemReader (CSV, fixed-width files)
- StaxEventItemReader (XML files)
- JdbcCursorItemReader (Database queries)
- JpaPagingItemReader (JPA queries)
- JsonFileItemReader (JSON files)

### ItemProcessor
Transforms items one at a time. Can filter items (return null to skip).

**Contract:**
```java
public interface ItemProcessor<I, O> {
    O process(I item) throws Exception;
}
```

### ItemWriter
Writes items in batches (chunks).

**Provided implementations:**
- FlatFileItemWriter (CSV, fixed-width files)
- StaxEventItemWriter (XML files)
- JdbcBatchItemWriter (Database inserts/updates)
- JpaItemWriter (JPA writes)
- JsonFileItemWriter (JSON files)

## Chunk-Oriented Processing

The most common pattern in Spring Batch is the chunk-oriented processing model:

1. Read items one at a time (ItemReader)
2. Process each item (ItemProcessor)
3. Write items in a batch/chunk (ItemWriter)

**Configuration pattern:**
```java
@Bean
public Step step1(JobRepository jobRepository, PlatformTransactionManager transactionManager) {
    return new StepBuilder("step1", jobRepository)
            .<Person, Person> chunk(10)  // chunk size
            .reader(reader())
            .processor(processor())
            .writer(writer())
            .build();
}
```

## Tasklet Step

For simple operations that don't require bulk reading and writing, use a Tasklet instead.

**Example:**
```java
@Bean
public Step step1(JobRepository jobRepository, PlatformTransactionManager transactionManager) {
    return new StepBuilder("step1", jobRepository)
            .tasklet(myTasklet(), transactionManager)
            .build();
}
```

## Transaction Management

Spring Batch manages transactions at the chunk level by default. This means:
- All items in a chunk are written within a single transaction
- If an item causes an exception, the entire chunk is rolled back

**Commit interval** is the number of items to process before committing a transaction.

## Best Practices

1. **Use the right reader/writer for your data source** - Don't reinvent existing implementations
2. **Keep processors stateless** - Processors are called once per item and shouldn't maintain state
3. **Set appropriate chunk sizes** - Balance memory usage with database round trips
4. **Use Job parameters for configuration** - Don't hardcode file paths or database queries
5. **Configure proper error handling** - Use skip logic for recoverable errors, retry logic for transient failures
6. **Use appropriate transaction boundaries** - Larger chunks = fewer commits = better performance (but more memory)
7. **Test your components in isolation** - Use Spring Batch test utilities for testing jobs and steps
