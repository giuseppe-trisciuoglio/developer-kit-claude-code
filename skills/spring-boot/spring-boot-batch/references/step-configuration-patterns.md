# Step Configuration Patterns

This guide covers the key patterns for configuring steps in Spring Batch jobs.

## Chunk-Oriented Step Configuration

The most common step type: read items, process them, and write them in chunks.

### Basic Chunk-Oriented Step

```java
@Configuration
public class BatchConfiguration {

    @Bean
    public Step importStep(JobRepository jobRepository,
                           PlatformTransactionManager transactionManager,
                           ItemReader<Person> reader,
                           ItemProcessor<Person, Person> processor,
                           ItemWriter<Person> writer) {
        return new StepBuilder("importStep", jobRepository)
                .<Person, Person> chunk(10)  // Chunk size: process 10 items, then write
                .reader(reader)
                .processor(processor)
                .writer(writer)
                .build();
    }
}
```

**Key components:**
- `chunk(10)` - Commit interval: items are written in batches of 10
- `reader()` - Where items come from
- `processor()` - Optional: transforms each item
- `writer()` - Where items go

### Without Processor

If no transformation is needed, skip the processor.

```java
@Bean
public Step simpleImportStep(JobRepository jobRepository,
                             PlatformTransactionManager transactionManager,
                             ItemReader<Person> reader,
                             ItemWriter<Person> writer) {
    return new StepBuilder("simpleImportStep", jobRepository)
            .<Person, Person> chunk(10)
            .reader(reader)
            .writer(writer)
            .build();
}
```

## Configuring ItemReader

### FlatFileItemReader (CSV Files)

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

### Fixed-Width File Reader

```java
@Bean
public FlatFileItemReader<Person> fixedWidthItemReader() {
    return new FlatFileItemReaderBuilder<Person>()
            .name("fixedWidthItemReader")
            .resource(new FileSystemResource("input/data.txt"))
            .fixedLength()
            .addColumns(new Range(1, 10))     // firstName: columns 1-10
            .addColumns(new Range(11, 20))    // lastName: columns 11-20
            .names("firstName", "lastName")
            .targetType(Person.class)
            .build();
}
```

### Database ItemReader (JDBC)

```java
@Bean
public JdbcCursorItemReader<Person> jdbcItemReader(DataSource dataSource) {
    return new JdbcCursorItemReaderBuilder<Person>()
            .name("jdbcItemReader")
            .dataSource(dataSource)
            .sql("SELECT first_name, last_name FROM people ORDER BY id")
            .rowMapper((rs, rowNum) -> new Person(
                    rs.getString("first_name"),
                    rs.getString("last_name")
            ))
            .build();
}
```

### Database ItemReader (JPA)

```java
@Bean
public JpaPagingItemReader<Person> jpaItemReader(EntityManagerFactory emf) {
    return new JpaPagingItemReaderBuilder<Person>()
            .name("jpaItemReader")
            .entityManagerFactory(emf)
            .queryString("SELECT p FROM Person p ORDER BY p.id")
            .pageSize(10)
            .build();
}
```

### JSON File ItemReader

```java
@Bean
public JsonItemReader<Person> jsonItemReader() {
    return new JsonItemReaderBuilder<Person>()
            .jsonObjectReader(new JacksonJsonObjectReader<>(Person.class))
            .resource(new FileSystemResource("input/data.json"))
            .name("jsonItemReader")
            .build();
}
```

## Configuring ItemProcessor

### Simple Transformer

```java
@Component
public class PersonItemProcessor implements ItemProcessor<Person, Person> {

    @Override
    public Person process(final Person person) throws Exception {
        final String firstName = person.firstName().toUpperCase();
        final String lastName = person.lastName().toUpperCase();

        return new Person(firstName, lastName);
    }
}
```

### Filtering Processor (Return Null to Skip)

```java
@Component
public class PersonFilterProcessor implements ItemProcessor<Person, Person> {

    @Override
    public Person process(final Person person) throws Exception {
        // Filter out people with empty last names
        if (person.lastName() == null || person.lastName().isEmpty()) {
            return null;  // Skip this item
        }
        return person;
    }
}
```

### Processor with Dependencies

```java
@Component
public class PersonEnrichmentProcessor implements ItemProcessor<Person, Person> {

    private final PersonRepository personRepository;

    public PersonEnrichmentProcessor(PersonRepository personRepository) {
        this.personRepository = personRepository;
    }

    @Override
    public Person process(final Person person) throws Exception {
        // Enrich person with additional data from database
        var additional = personRepository.findByName(person.firstName());
        return person.withAdditionalData(additional);
    }
}
```

### Composite Processor

Chain multiple processors together.

```java
@Bean
public CompositeItemProcessor<Person, Person> compositeProcessor() {
    List<ItemProcessor<Person, Person>> processors = new ArrayList<>();
    processors.add(new PersonFilterProcessor());
    processors.add(new PersonItemProcessor());

    CompositeItemProcessor<Person, Person> composite = new CompositeItemProcessor<>();
    composite.setDelegates(processors);
    return composite;
}
```

## Configuring ItemWriter

### FlatFileItemWriter (CSV)

```java
@Bean
public FlatFileItemWriter<Person> csvItemWriter() {
    return new FlatFileItemWriterBuilder<Person>()
            .name("csvItemWriter")
            .resource(new FileSystemResource("output/output.csv"))
            .delimited()
            .names("firstName", "lastName")
            .build();
}
```

### Database ItemWriter (JDBC)

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

### Database ItemWriter (JPA)

```java
@Bean
public JpaItemWriter<Person> jpaItemWriter(EntityManagerFactory emf) {
    JpaItemWriter<Person> writer = new JpaItemWriter<>();
    writer.setEntityManagerFactory(emf);
    return writer;
}
```

### JSON File ItemWriter

```java
@Bean
public JsonFileItemWriter<Person> jsonItemWriter() {
    return new JsonFileItemWriterBuilder<Person>()
            .jsonObjectMarshaller(new JacksonJsonObjectMarshaller<>())
            .resource(new FileSystemResource("output/data.json"))
            .name("jsonItemWriter")
            .build();
}
```

## Tasklet Step Configuration

For single-task operations that don't follow the read-process-write pattern.

### Simple Tasklet Implementation

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
```

### Tasklet Step Configuration

```java
@Bean
public Step taskletStep(JobRepository jobRepository,
                        PlatformTransactionManager transactionManager,
                        MyTasklet myTasklet) {
    return new StepBuilder("taskletStep", jobRepository)
            .tasklet(myTasklet, transactionManager)
            .build();
}
```

### Tasklet with Dependencies

```java
@Component
public class FileProcessingTasklet implements Tasklet {

    private final FileService fileService;

    public FileProcessingTasklet(FileService fileService) {
        this.fileService = fileService;
    }

    @Override
    public RepeatStatus execute(StepContribution contribution, ChunkContext chunkContext)
            throws Exception {
        fileService.processFiles();
        contribution.incrementProcessSkipCount(0);
        return RepeatStatus.FINISHED;
    }
}
```

## Chunk Configuration Options

### Custom Chunk Size

Balance between memory usage and database round trips.

```java
@Bean
public Step largeChunkStep(JobRepository jobRepository,
                          PlatformTransactionManager transactionManager,
                          ItemReader<Person> reader,
                          ItemWriter<Person> writer) {
    return new StepBuilder("largeChunkStep", jobRepository)
            .<Person, Person> chunk(1000)  // Larger chunk size for better performance
            .reader(reader)
            .writer(writer)
            .build();
}
```

### Commit Interval

Number of items to process before committing the transaction.

```java
@Bean
public Step commitIntervalStep(JobRepository jobRepository,
                              PlatformTransactionManager transactionManager,
                              ItemReader<Person> reader,
                              ItemWriter<Person> writer) {
    return new StepBuilder("commitIntervalStep", jobRepository)
            .<Person, Person> chunk(100)
            .reader(reader)
            .writer(writer)
            .build();
}
```

## Step-Level Error Handling

### Skip Logic

Skip a certain number of exceptions gracefully.

```java
@Bean
public Step stepWithSkipLogic(JobRepository jobRepository,
                             PlatformTransactionManager transactionManager,
                             ItemReader<Person> reader,
                             ItemProcessor<Person, Person> processor,
                             ItemWriter<Person> writer) {
    return new StepBuilder("stepWithSkipLogic", jobRepository)
            .<Person, Person> chunk(10)
            .reader(reader)
            .processor(processor)
            .writer(writer)
            .skip(Exception.class)
            .skipLimit(5)  // Skip up to 5 exceptions
            .build();
}
```

### Retry Logic

Retry transient failures automatically.

```java
@Bean
public Step stepWithRetryLogic(JobRepository jobRepository,
                              PlatformTransactionManager transactionManager,
                              ItemReader<Person> reader,
                              ItemProcessor<Person, Person> processor,
                              ItemWriter<Person> writer) {
    return new StepBuilder("stepWithRetryLogic", jobRepository)
            .<Person, Person> chunk(10)
            .reader(reader)
            .processor(processor)
            .writer(writer)
            .retry(TemporaryException.class)
            .retryLimit(3)  // Retry up to 3 times
            .build();
}
```

### Combined Skip and Retry

```java
@Bean
public Step robustStep(JobRepository jobRepository,
                      PlatformTransactionManager transactionManager,
                      ItemReader<Person> reader,
                      ItemProcessor<Person, Person> processor,
                      ItemWriter<Person> writer) {
    return new StepBuilder("robustStep", jobRepository)
            .<Person, Person> chunk(10)
            .reader(reader)
            .processor(processor)
            .writer(writer)
            .skip(PermanentException.class)
            .skipLimit(10)
            .retry(TemporaryException.class)
            .retryLimit(3)
            .noSkip(SQLException.class)  // Don't skip SQL errors even if Exception is skipped
            .noRetry(PermanentException.class)
            .build();
}
```

## Step Listeners

Monitor and control step execution.

### StepExecutionListener

```java
@Component
public class StepExecutionListener {

    @BeforeStep
    public void beforeStep(StepExecution stepExecution) {
        System.out.println("Step starting: " + stepExecution.getStepName());
    }

    @AfterStep
    public ExitStatus afterStep(StepExecution stepExecution) {
        System.out.println("Step finished: " + stepExecution.getStepName());
        System.out.println("Items read: " + stepExecution.getReadCount());
        System.out.println("Items written: " + stepExecution.getWriteCount());
        System.out.println("Items skipped: " + stepExecution.getSkipCount());
        return stepExecution.getExitStatus();
    }
}
```

### Using the Listener in Step

```java
@Bean
public Step monitoredStep(JobRepository jobRepository,
                         PlatformTransactionManager transactionManager,
                         ItemReader<Person> reader,
                         ItemWriter<Person> writer,
                         StepExecutionListener listener) {
    return new StepBuilder("monitoredStep", jobRepository)
            .<Person, Person> chunk(10)
            .reader(reader)
            .writer(writer)
            .listener(listener)
            .build();
}
```

## Best Practices for Step Configuration

1. **Start with sensible chunk sizes** - 100-1000 items depending on item size and complexity
2. **Use provided readers and writers** - Don't reinvent the wheel with custom implementations
3. **Keep processors simple and stateless** - One responsibility per processor
4. **Handle errors explicitly** - Use skip and retry for expected failures
5. **Monitor step execution** - Use listeners to track performance and issues
6. **Use Spring Boot builders** - Much cleaner than manual factory bean configuration
7. **Externalize configuration** - Use properties for file paths, database queries, chunk sizes
8. **Test components independently** - Use Spring Batch test fixtures for unit testing
