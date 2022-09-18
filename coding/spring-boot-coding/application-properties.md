# Application properties

### Folder structure

```
/resources
    .gitignore
    # main prop file for production values
    application.properties (application.yml)
    # for dev environment 
    appplication-dev.properties (application-dev.yml)
    # for local environment (ignore by git) - developer defined custom settings
    application-local.properties
```

.gitignore >&#x20;

```
application-local.yml
local.properties
```

### Database config defaults&#x20;

For local environments you should always define standard data access (database, user, password) properties between other developers

```properties
spring:
  datasource:
    driver: org.postgresql.Driver
    username: postgres
    password: postgres
    url: jdbc:postgresql://localhost:5432/postgres?currentSchema=svc_user

```

* database: postgres
* username: postgres
* password: postgres
* port: 5432
* currentSchema - each service has it's own schema, svc\_{name}

### Error handling defaults

```properties
error:
  handling:
    exception-logging: MESSAGE_ONLY
    full-stacktrace-http-statuses[0]: 5xx
```

### Logging defaults

```properties
logging:
  file:
    name: logs/application.log
  level.root: INFO
  level.org.springframework: INFO
  level.org.hibernate: INFO
  level.org.springframework.jdbc.core.JdbcTemplate: INFO
  pattern:
    file:
      "%d{dd-MM-yyyy HH:mm:ss.SSS} %magenta([%thread]) %highlight(%-5level) %logger{36}.%M - %msg%n"
    console:
      "%d{dd-MM-yyyy HH:mm:ss.SSS} %magenta([%thread]) %highlight(%-5level) %logger{36}.%M - %msg%n"
```
