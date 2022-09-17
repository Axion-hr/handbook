# JPA & Hibernate guidelines

Tutorials collection

* What is JPA? Difference between hibernate?
  * [https://www.javatpoint.com/spring-boot-jpa#:\~:text=Spring%20Boot%20JPA%20is%20a,is%20a%20set%20of%20interfaces.](https://www.javatpoint.com/spring-boot-jpa)
* REST Query Language with Spring Data JPA Specifications [https://reflectoring.io/spring-data-specifications/](https://reflectoring.io/spring-data-specifications/)

### Enums types (database, API)&#x20;

Tutorials

* [https://www.baeldung.com/jpa-persisting-enums-in-jpa](https://www.baeldung.com/jpa-persisting-enums-in-jpa)

#### Database (SQL) definitions

Always define enums as Strings on database level!&#x20;

* Don't use ordinal types&#x20;
* Don't use database level enums (e.g. postgres db types)
* Use small caps! (camel case)
  * easier SQL queries&#x20;

```java
@Entity
@Table(name = "users")
@Data
public class User {

    @Enumerated(EnumType.STRING)
    @Column(name = "status")
    private UserStatus status;
}
```

#### Mapping enum in Java classes (DTO, Entity)

* If calling code is responsible for all enum values - **use Java enums**!
  * Pros: Validation included for DTO objects, easy type conversion
* &#x20;If database is filled from external types and all types are dynamic - **use Strings** (!)

DTO and Entity objects can reference same EnumType unless there is a special need to creating EnumDto and EnumEntity separation of the same type (e.g. validation etc).&#x20;

