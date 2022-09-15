# Tips and tricks



### Strings vs Enums (for db types): use Strings!

Instead of this

```
public enum DatabaseType {  
    type1, 
    type2, 
    type3
}
```

use this

```
public class DatabaseType {  
    public static final String TYPE1 = "type1", 
    public static final String TYPE2 = "type2",
    public static final String TYPE3 = "type3"
}
```

Reasons:&#x20;

* Strings are more flexible and it's the same level of effort for implementation
* e.g. if external system writes in the database, and service doesn't recognises enum type - it fails&#x20;

> Note: db types should be small caps&#x20;



### Builders for DTO and Model objects

* Use builder patters as much as possible! (via Lombok)

```java
@Data
@Builder(toBuilder = true)
@AllArgsConstructor
@NoArgsConstructor
public class CreateTransactionRequestDto {
    private String cardNumber;
    private ZonedDateTime transactionStart;
    private ZonedDateTime transactionEnd;
    private String locationId;
    ....
}

```

* easier creation, immutability pattern, and easier creation of copy objects (!)

```java
CreateTransactionRequestDto createTransaction = CreateTransactionRequestDto.builder()
        .locationId("locationId")
        .cardNumber("cardNumber")
        .build();


CreateTransactionRequestDto modifiedTransaction = createTransaction.toBuilder()
        .transactionOrigin("modifySomePropery!")
        .build();
```
