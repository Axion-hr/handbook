# Tips and tricks









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
