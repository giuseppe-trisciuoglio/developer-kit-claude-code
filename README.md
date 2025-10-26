# Developer Kit for Claude Code

> A curated collection of reusable skills and agents for automating development tasks in Claude Code — focusing on
> Java/Spring Boot patterns with extensibility to PHP, TypeScript, and Python

**Developer Kit for Claude Code** teaches Claude how to **perform development tasks in a repeatable way** across
multiple languages and frameworks. It includes specialized agents for code review, testing patterns, REST API design,
and AI integration.

## 🚀 Quick Start

### Claude Code CLI

```bash
/plugin marketplace add giuseppe-trisciuoglio/developer-kit
```

### Claude Desktop

[Enable Skills in Settings](https://claude.ai/settings/capabilities)

## 🛠️ Installation & Setup

### Claude Code CLI

```bash
# Install from marketplace
/plugin marketplace add giuseppe-trisciuoglio/developer-kit

# Or install from local directory
/plugin install /path/to/developer-kit
```

### Claude Desktop

1. Go to [Settings > Capabilities](https://claude.ai/settings/capabilities)
2. Enable Skills toggle
3. Browse available skills or upload custom skills
4. Start using in conversations

## 📚 Available Skills

### Spring Boot Skills

| Skill                                                                                                  | Purpose                              | Key Topics                                                        |
|--------------------------------------------------------------------------------------------------------|--------------------------------------|-------------------------------------------------------------------|
| **[spring-boot-actuator](skills/spring-boot/spring-boot-actuator/SKILL.md)**                           | Production monitoring and metrics    | Health checks, custom endpoints, metrics, startup tracking        |
| **[spring-boot-dependency-injection](skills/spring-boot/spring-boot-dependency-injection/SKILL.md)**   | Constructor injection best practices | DI patterns, testing strategies, anti-patterns                    |
| **[spring-boot-crud-patterns](skills/spring-boot/spring-boot-crud-patterns/SKILL.md)**                 | REST CRUD API design with DDD        | Feature-based architecture, Lombok, Spring Data                   |
| **[spring-boot-event-driven-patterns](skills/spring-boot/spring-boot-event-driven-patterns/SKILL.md)** | Event-driven architecture            | Domain events, Kafka, Spring Cloud Stream, transactional patterns |
| **[spring-boot-rest-api-standards](skills/spring-boot/spring-boot-rest-api-standards/SKILL.md)**       | REST API design standards            | HTTP semantics, error handling, pagination, security headers      |
| **[spring-boot-test-patterns](skills/spring-boot/spring-boot-test-patterns/SKILL.md)**                 | Integration testing patterns         | Testcontainers, Spring slice tests, database strategies           |
| **[spring-boot-cache](skills/spring-boot/spring-boot-cache/SKILL.md)**                                 | Spring Boot caching patterns         | Cache configuration, eviction strategies, distributed caching     |
| **[spring-boot-batch](skills/spring-boot/spring-boot-batch/SKILL.md)**                                 | Batch processing patterns            | Job configuration, step management, chunk processing, partitioning |
| **[spring-boot-saga-pattern](skills/spring-boot/spring-boot-saga-pattern/SKILL.md)**                               | Distributed transaction management   | Saga pattern, choreography, orchestration, compensating transactions |
| **[spring-data-jpa](skills/spring-boot/spring-data-jpa/SKILL.md)**                                     | Spring Data JPA best practices       | Query methods, custom repositories, performance optimization      |
| **[spring-data-neo4j](skills/spring-boot/spring-data-neo4j/SKILL.md)**                                 | Neo4j graph database integration     | Graph modeling, Cypher queries, relationships, reactive mode      |
| **[spring-boot-openapi-documentation](skills/spring-boot/spring-boot-openapi-documentation/SKILL.md)** | OpenAPI/Swagger documentation        | API documentation, schema generation, SpringDoc                   |

### JUnit Testing Skills

| Skill                                                                                               | Purpose                          | Key Topics                                        |
|-----------------------------------------------------------------------------------------------------|----------------------------------|---------------------------------------------------|
| **[unit-test-service-layer](skills/junit-test/unit-test-service-layer/SKILL.md)**                   | Service layer unit testing       | Mocking, assertions, test organization            |
| **[unit-test-controller-layer](skills/junit-test/unit-test-controller-layer/SKILL.md)**             | Controller layer testing         | Mock MVC, request/response handling, status codes |
| **[unit-test-parameterized](skills/junit-test/unit-test-parameterized/SKILL.md)**                   | Parameterized tests              | JUnit 5 @ParameterizedTest, multiple scenarios    |
| **[unit-test-exception-handler](skills/junit-test/unit-test-exception-handler/SKILL.md)**           | Exception handling tests         | Custom exceptions, error scenarios                |
| **[unit-test-bean-validation](skills/junit-test/unit-test-bean-validation/SKILL.md)**               | Jakarta Validation testing       | Bean validation constraints, custom validators    |
| **[unit-test-security-authorization](skills/junit-test/unit-test-security-authorization/SKILL.md)** | Spring Security testing          | @WithMockUser, authorization checks, roles        |
| **[unit-test-application-events](skills/junit-test/unit-test-application-events/SKILL.md)**         | Application event testing        | Event publishing, listeners, @EventListener       |
| **[unit-test-scheduled-async](skills/junit-test/unit-test-scheduled-async/SKILL.md)**               | Async and scheduled task testing | @Async, @Scheduled, TestScheduler                 |
| **[unit-test-json-serialization](skills/junit-test/unit-test-json-serialization/SKILL.md)**         | JSON serialization testing       | Jackson, @JsonTest, custom serializers            |
| **[unit-test-config-properties](skills/junit-test/unit-test-config-properties/SKILL.md)**           | Configuration properties testing | @ConfigurationProperties, binding validation      |
| **[unit-test-mapper-converter](skills/junit-test/unit-test-mapper-converter/SKILL.md)**             | Mapper and converter testing     | Entity to DTO, custom converters                  |
| **[unit-test-caching](skills/junit-test/unit-test-caching/SKILL.md)**                               | Caching layer testing            | @Cacheable, cache eviction, hit/miss scenarios    |
| **[unit-test-boundary-conditions](skills/junit-test/unit-test-boundary-conditions/SKILL.md)**       | Boundary condition testing       | Edge cases, limits, null values                   |
| **[unit-test-utility-methods](skills/junit-test/unit-test-utility-methods/SKILL.md)**               | Utility method testing           | Static methods, helpers, common functions         |
| **[unit-test-wiremock-rest-api](skills/junit-test/unit-test-wiremock-rest-api/SKILL.md)**           | REST API mocking with WireMock   | HTTP stubs, response matching, status codes       |

### LangChain4J Skills

| Skill                                                                                                                    | Purpose                          | Key Topics                                              |
|--------------------------------------------------------------------------------------------------------------------------|----------------------------------|---------------------------------------------------------|
| **[langchain4j-spring-boot-integration](skills/langchain4j/langchain4j-spring-boot-integration/SKILL.md)**               | LangChain4J with Spring Boot     | Configuration, bean management, integration patterns    |
| **[langchain4j-rag-implementation-patterns](skills/langchain4j/langchain4j-rag-implementation-patterns/SKILL.md)**       | Retrieval-Augmented Generation   | Vector stores, retrievers, prompt chaining              |
| **[langchain4j-ai-services-patterns](skills/langchain4j/langchain4j-ai-services-patterns/SKILL.md)**                     | AI service design                | Service patterns, model configuration, error handling   |
| **[langchain4j-mcp-server-patterns](skills/langchain4j/langchain4j-mcp-server-patterns/SKILL.md)**                       | Model Context Protocol servers   | MCP integration, tool exposure, data access             |
| **[langchain4j-tool-function-calling-patterns](skills/langchain4j/langchain4j-tool-function-calling-patterns/SKILL.md)** | Tool and function calling        | Agent patterns, tool definition, function orchestration |
| **[langchain4j-testing-strategies](skills/langchain4j/langchain4j-testing-strategies/SKILL.md)**                         | Testing LangChain4J applications | Mock models, test containers, integration tests         |
| **[langchain4j-vector-stores-configuration](skills/langchain4j/langchain4j-vector-stores-configuration/SKILL.md)**       | Vector store configuration       | Embeddings, similarity search, provider setup           |
| **[qdrant-java-development](skills/langchain4j/qdrant/SKILL.md)** | Qdrant vector database integration | Java, Spring Boot, Langchain4j, RAG, vector search |

### AWS Java Skills

| Skill                                                                                           | Purpose                                  | Key Topics                                                    |
|-------------------------------------------------------------------------------------------------|------------------------------------------|---------------------------------------------------------------|
| **[aws-rds-spring-boot-integration](skills/aws-java/aws-rds-spring-boot-integration/SKILL.md)** | Aurora RDS configuration with Spring JPA | Cluster setup, connection pooling, read/write split, failover |
| **[aws-sdk-java-v2-core](skills/aws-java/aws-sdk-java-v2-core/SKILL.md)**                       | AWS SDK v2 core patterns                 | Client configuration, credential management, async operations |
| **[aws-sdk-java-v2-s3](skills/aws-java/aws-sdk-java-v2-s3/SKILL.md)**                           | S3 operations with SDK v2                | Bucket operations, object upload/download, presigned URLs     |
| **[aws-sdk-java-v2-dynamodb](skills/aws-java/aws-sdk-java-v2-dynamodb/SKILL.md)**               | DynamoDB operations                      | Table operations, queries, scans, transactions                |
| **[aws-sdk-java-v2-lambda](skills/aws-java/aws-sdk-java-v2-lambda/SKILL.md)**                   | Lambda function development              | Handler patterns, event processing, custom runtimes           |
| **[aws-sdk-java-v2-secrets-manager](skills/aws-java/aws-sdk-java-v2-secrets-manager/SKILL.md)** | Secrets Manager integration              | Secret retrieval, rotation, caching                           |
| **[aws-sdk-java-v2-kms](skills/aws-java/aws-sdk-java-v2-kms/SKILL.md)**                         | KMS encryption operations                | Key management, encryption/decryption, envelope encryption    |
| **[aws-sdk-java-v2-messaging](skills/aws-java/aws-sdk-java-v2-messaging/SKILL.md)**             | SQS/SNS messaging patterns               | Queue operations, topic publishing, message processing        |
| **[aws-sdk-java-v2-rds](skills/aws-java/aws-sdk-java-v2-rds/SKILL.md)**                         | RDS management operations                | Instance management, snapshots, parameter groups              |
| **[aws-sdk-java-v2-bedrock](skills/aws-java/aws-sdk-java-v2-bedrock/SKILL.md)**                 | AWS Bedrock AI integration               | Model invocation, streaming, guardrails                       |

## 🤖 Available Agents

| Agent                                                                                          | Specialization          | Best For                                  |
|------------------------------------------------------------------------------------------------|-------------------------|-------------------------------------------|
| **[spring-boot-code-review-specialist](agents/spring-boot-code-review-specialist.md)**         | Spring Boot code review | Architecture, patterns, security review   |
| **[spring-boot-backend-development-expert](agents/spring-boot-backend-development-expert.md)** | Backend development     | Implementation, testing, optimization     |
| **[langchain4j-ai-development-expert](agents/langchain4j-ai-development-expert.md)**           | AI integration          | LangChain4J implementation, RAG, agents   |
| **[spring-boot-unit-testing-expert](agents/spring-boot-unit-testing-expert.md)**               | Unit testing            | Test patterns, Mockito, test organization |

## 🗂️ Repository Structure

```
developer-kit-claude-code/
├── agents/                              # Ready-to-use specialized agents
│   ├── spring-boot-code-review-specialist.md
│   ├── spring-boot-backend-development-expert.md
│   ├── langchain4j-ai-development-expert.md
│   └── spring-boot-unit-testing-expert.md
├── skills/                              # Reusable skills organized by domain
│   ├── spring-boot/                     # Spring Boot framework skills (12)
│   │   ├── spring-boot-actuator/
│   │   ├── spring-boot-batch/
│   │   ├── spring-boot-cache/
│   │   ├── spring-boot-crud-patterns/
│   │   ├── spring-boot-dependency-injection/
│   │   ├── spring-boot-event-driven-patterns/
│   │   ├── spring-boot-openapi-documentation/
│   │   ├── spring-boot-rest-api-standards/
│   │   ├── spring-boot-saga-pattern/
│   │   ├── spring-boot-test-patterns/
│   │   ├── spring-data-jpa/
│   │   └── spring-data-neo4j/
│   ├── junit-test/                      # JUnit 5 unit testing skills (15)
│   │   ├── unit-test-service-layer/
│   │   ├── unit-test-controller-layer/
│   │   ├── unit-test-parameterized/
│   │   ├── unit-test-exception-handler/
│   │   ├── unit-test-bean-validation/
│   │   ├── unit-test-security-authorization/
│   │   ├── unit-test-application-events/
│   │   ├── unit-test-scheduled-async/
│   │   ├── unit-test-json-serialization/
│   │   ├── unit-test-config-properties/
│   │   ├── unit-test-mapper-converter/
│   │   ├── unit-test-caching/
│   │   ├── unit-test-boundary-conditions/
│   │   ├── unit-test-utility-methods/
│   │   └── unit-test-wiremock-rest-api/
│   ├── langchain4j/                     # LangChain4J AI integration skills (7)
│   │   ├── langchain4j-spring-boot-integration/
│   │   ├── langchain4j-rag-implementation-patterns/
│   │   ├── langchain4j-ai-services-patterns/
│   │   ├── langchain4j-mcp-server-patterns/
│   │   ├── langchain4j-tool-function-calling-patterns/
│   │   ├── langchain4j-testing-strategies/
│   │   └── langchain4j-vector-stores-configuration/
│   └── aws-java/                        # AWS SDK for Java v2 skills (10)
│       ├── aws-rds-spring-boot-integration/
│       ├── aws-sdk-java-v2-core/
│       ├── aws-sdk-java-v2-s3/
│       ├── aws-sdk-java-v2-dynamodb/
│       ├── aws-sdk-java-v2-lambda/
│       ├── aws-sdk-java-v2-secrets-manager/
│       ├── aws-sdk-java-v2-kms/
│       ├── aws-sdk-java-v2-messaging/
│       ├── aws-sdk-java-v2-rds/
│       └── aws-sdk-java-v2-bedrock/
├── commands/                            # Sample invocation commands
├── .claude-plugin/                      # Plugin configuration
└── README.md                            # This file
```

## ✨ Key Features

**Specialized** — Domain-specific agents for code review, testing, and AI development tailored to Java/Spring Boot

**Composable** — Skills stack together automatically. Claude identifies which skills are needed and uses them in
combination

**Portable** — Use the same skills across Claude.ai, Claude Code CLI, Claude Desktop, and the Claude API

**Efficient** — Skills load on-demand, consuming minimal tokens until they're actively used

## 🏗️ Architecture & Patterns

This kit promotes a **feature-based, DDD-inspired architecture** for Java/Spring Boot projects:

```
feature/
├── domain/              # Domain-pure logic
│   ├── model/           # Spring-free entities
│   ├── repository/      # Domain ports (interfaces)
│   └── service/         # Domain services
├── application/         # Use cases & business logic
│   ├── service/         # Application services (@Service)
│   └── dto/             # Immutable DTOs/records
├── presentation/        # HTTP layer
│   └── rest/            # REST controllers & mappers
└── infrastructure/      # Technical adapters
    └── persistence/     # JPA repositories & adapters
```

### Core Principles

- ✅ Constructor injection exclusively (never field injection)
- ✅ Java records (16+) preferred for DTOs
- ✅ Repository pattern with domain interfaces
- ✅ Immutability throughout (`final` fields, `@Value`, records)
- ✅ Clean separation: domain ↔ framework

## 🔧 Technology Stack

| Category                | Technology                             | Version             |
|-------------------------|----------------------------------------|---------------------|
| **Java**                | OpenJDK/GraalVM                        | 16+                 |
| **Framework**           | Spring Boot                            | 3.x                 |
| **Testing**             | JUnit 5 (Jupiter) + Mockito + AssertJ  | 5.x / 4.x+          |
| **Integration Testing** | Testcontainers                         | 1.19.0+             |
| **Data**                | Spring Data JPA                        | Jakarta Persistence |
| **Graph Database**      | Spring Data Neo4j                      | 7.x                 |
| **Validation**          | Jakarta Validation                     | 3.0+                |
| **Utilities**           | Lombok                                 | 1.18.30+            |
| **Messaging**           | Spring Cloud Stream                    | 4.x                 |
| **AWS SDK**             | AWS SDK for Java v2                    | 2.20+               |
| **Cloud**               | AWS (RDS Aurora, S3, Lambda, DynamoDB) | Latest              |
| **AI/ML**               | LangChain4J, AWS Bedrock               | Latest              |

## 🎯 Use Cases

**Code Review & Architecture** — Automated Spring Boot code reviews, architecture validation, security analysis

**REST API Design** — Standardized API development with proper HTTP semantics, error handling, pagination

**Testing Strategies** — Unit test patterns, integration testing with Testcontainers, test organization

**AI Integration** — LangChain4J implementation guidance, RAG patterns, agent development, MCP server creation

**Event-Driven Architecture** — Domain events, event sourcing patterns, Kafka integration, transactional consistency

**AWS Cloud Development** — RDS Aurora configuration, S3 operations, Lambda functions, DynamoDB, Secrets Manager, KMS
encryption

## 📖 How to Use

### For Individual Contributors

1. Install the skill/agent in Claude Code
2. Describe your development task
3. Claude will automatically load relevant skills and provide guidance

### For Teams

1. Clone or fork this repository
2. Customize skills for your team's standards
3. Deploy via `/plugin install /path/to/developer-kit-claude-code`
4. Share the plugin marketplace link with your team

## 🔧 Custom Commands

This kit includes specialized commands for workflow orchestration, verification, and skill validation:

### GitHub Spec Kit Commands

| Command                          | Purpose                                        | Use Case                                                                             |
|----------------------------------|------------------------------------------------|--------------------------------------------------------------------------------------|
| **`/speckit.check-integration`** | Verify task integration with existing codebase | Run AFTER `/speckit.tasks` to detect duplication and integration opportunities       |
| **`/speckit.optimize`**          | Optimize execution plan for parallelization    | Run AFTER check-integration to plan subagent delegation and resource allocation      |
| **`/speckit.verify`**            | Comprehensive implementation verification      | Run AFTER `/speckit.implement` to validate all requirements, tests, and code quality |

### Developer Kit Commands

| Command                          | Purpose                                        | Use Case                                                                             |
|----------------------------------|------------------------------------------------|--------------------------------------------------------------------------------------|
| **`/devkit.verify-skill`**       | Validate skill against DevKit standards        | Verify a skill's compliance with requirements, format, and best practices             |

### GitHub Spec Kit Workflow

```
/speckit.tasks
    ↓
/speckit.check-integration    ← Detect existing code & integration opportunities
    ↓
/speckit.optimize             ← Plan parallelization & subagent assignment
    ↓
/speckit.implement            ← Execute with optimization
    ↓
/speckit.verify               ← Verify completeness & quality
```

### Spec Kit Command Details

**`/speckit.check-integration`**

- Analyzes tasks.md for codebase integration opportunities
- Detects duplication risks and existing implementations
- Verifies architectural alignment
- **Output**: Integration analysis report (READ-ONLY)
- **Timing**: After task generation, before optimization

**`/speckit.optimize`**

- Builds dependency graph and identifies parallelization opportunities
- Allocates tasks to specialized subagents
- Generates phased execution plan with validation checkpoints
- **Output**: Optimization report with phase execution matrix (READ-ONLY)
- **Timing**: After integration check, before implementation

**`/speckit.verify`**

- Validates all tasks completed
- Confirms requirements coverage
- Runs test suites and checks coverage metrics
- Audits code quality, security, and performance
- Generates verification-report.md
- **Output**: verification-report.md (PASS/FAIL)
- **Timing**: After implementation execution

### Developer Kit Command Details

**`/devkit.verify-skill`**

- Validates skill compliance with DevKit standards and official Skills documentation
- Checks SKILL.md frontmatter (name, description, allowed-tools, etc.)
- Verifies file structure and syntax (no YAML errors, valid paths)
- Validates content quality (clear triggers, instructions, examples)
- Confirms all referenced files exist in skill directory
- **Usage**: `/devkit.verify-skill skill-name`
- **Output**: Comprehensive validation report with PASS/FAIL status
- **Best for**: Skill authors before submitting PRs or publishing

## 🌍 Language Roadmap

- **✅ Java/Spring Boot** — Comprehensive patterns and agents
- **📋 TypeScript** — Node.js, NestJS, Express patterns (planned)
- **📋 Python** — Django, FastAPI patterns (planned)
- **📋 PHP** — Laravel, Symfony patterns (planned)

## 🤝 Contributing

### Adding a New Skill

1. Create `skills/<domain>/<skill-name>/SKILL.md`
    - For Spring Boot: `skills/spring-boot/spring-boot-*/SKILL.md`
    - For JUnit Testing: `skills/junit-test/unit-test-*/SKILL.md`
    - For LangChain4J: `skills/langchain4j/langchain4j-*/SKILL.md`
    - For AWS Java: `skills/aws-java/aws-*/SKILL.md`
2. Include YAML frontmatter (name, description, language)
3. Add practical examples and best practices
4. Document dependencies and commands
5. Add supporting files (examples.md, references.md) if needed
6. Update marketplace.json with new skill path
7. Submit a PR with description and examples

### Adding a New Domain

1. Create `skills/<new-domain>/` directory
2. Add domain-specific skills following the pattern
3. Create corresponding agent if needed
4. Follow existing structure for consistency
5. Update README.md with new domain section
6. Update marketplace.json with new paths

See [CONTRIBUTING guidelines](CONTRIBUTING.md) for detailed instructions.

## 🔒 Security & Best Practices

⚠️ **Important**: Skills can execute code in Claude's environment. Review all custom skills before deploying.

**Best Practices**

- Only install skills from trusted sources
- Review SKILL.md and all scripts before enabling
- Keep skills version-controlled with git
- Audit regularly for updates and changes
- Test in non-production environments first

## 📚 Resources

### Official Documentation

- [What are Skills?](https://support.claude.com/en/articles/12512176-what-are-skills) — Claude support article
- [Using Skills in Claude](https://support.claude.com/en/articles/12512180-using-skills-in-claude) — Setup guide
- [Claude Developer Platform](https://docs.claude.com/) — Official docs
- [Skills API Documentation](https://docs.claude.com/en/api/skills) — API reference

### Related Projects

- [anthropics/skills](https://github.com/anthropics/skills) — Official Anthropic skills repository
- [awesome-claude-skills](https://github.com/travisvn/awesome-claude-skills) — Community skills collection
- [obra/superpowers](https://github.com/obra/superpowers) — Battle-tested skills library

## 📝 License

This project is licensed under the [LICENSE](LICENSE) file in the repository root. See that file for details.

## 📞 Support

- **Questions?** Open an [issue](https://github.com/giuseppe-trisciuoglio/developer-kit-claude-code/issues)
- **Contributions?** Submit a [pull request](https://github.com/giuseppe-trisciuoglio/developer-kit-claude-code/pulls)
- **Integration proposals?** Create a discussion or reach out

## 📅 Changelog

| Version   | Changes                                                                                                                                                                                                             |
|-----------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **1.5.0** | Refactor frontmatter skills. Added new command for review a skill.                                                                                                                                                  |
| **1.4.0** | Refactor structure skills. Added 10 AWS Java SDK v2 skills (RDS Aurora, S3, DynamoDB, Lambda, Secrets Manager, etc.).<br/> Added Spring Data Neo4j skill with graph database patterns, Cypher queries, reactive mode |
| **1.3.0** | Added spring-boot-unit-testing-expert agent                                                                                                                                                                         |
| **1.2.1** | Fixed speckit.optimize report and summary                                                                                                                                                                           |
| **1.2.0** | Added speckit.optimize command for workflow parallelization                                                                                                                                                         |
| **1.1.0** | Added Github Spec Kit commands: check-integration, verify-spec-implementation                                                                                                                                       |
| **1.0.0** | Initial release: Java support with Spring Boot and LangChain4J integration                                                                                                                                          |

---

**Made with ❤️ for Java/Spring Boot developers using Claude Code**
