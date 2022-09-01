# Application properties

folder structure

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

### Database config&#x20;

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





