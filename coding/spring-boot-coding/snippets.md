# Snippets

### Webclient & API Service client

```java
@EnableWebMvc
@Configuration
public class WebConfig implements WebMvcConfigurer {
    @Bean
    public ExchangeStrategies provideExchangeStrategies(ObjectMapper objectMapper) {
        return ExchangeStrategies
                .builder()
                .codecs(clientDefaultCodecsConfigurer -> {
                    clientDefaultCodecsConfigurer.defaultCodecs().jackson2JsonEncoder(new Jackson2JsonEncoder(objectMapper, MediaType.APPLICATION_JSON));
                    clientDefaultCodecsConfigurer.defaultCodecs().jackson2JsonDecoder(new Jackson2JsonDecoder(objectMapper, MediaType.APPLICATION_JSON));
                }).build();
    }
}
```

```java
@Service
@Slf4j
public class CardMgmApiClientImpl implements CardMgmApiClient {

    private final CardMgmApiConfig cardConfig;
    // https://stackoverflow.com/a/43997021/557859
    private final ExchangeStrategies exchangeStrategies;
    private final WebClient webClient;

    @Autowired
    public CardMgmApiClientImpl(CardMgmApiConfig cardConfig, ExchangeStrategies exchangeStrategies) {
        this.cardConfig = cardConfig;
        this.exchangeStrategies = exchangeStrategies;
        webClient = buildWebClient();
    }


    @NotNull
    @Override
    public List<UserCard> fetchCards(UserAuthDto userAuth) {
        UserCard[] userCards = webClient
                .get()
                .uri(builder -> builder.path("user-cards")
                        .build())
                .header("Authorization", userAuth.getAuthorizationHeader())
                .retrieve()
                .bodyToMono(UserCard[].class)
                .doOnError(e -> log.error("Cannot retrieve cards for a user with awsCognitoId={}", userAuth.getAwsCognitoId()))
                .block();
        if (userCards == null) {
            return new ArrayList<>();
        } else {
            return Arrays.stream(userCards).toList();
        }
    }

    protected WebClient buildWebClient() {
        return WebClient.builder()
                .exchangeStrategies(exchangeStrategies)
                .baseUrl(cardConfig.getApiUrl())
                .defaultHeader(HttpHeaders.CONTENT_TYPE, MediaType.APPLICATION_JSON_VALUE)
                .filter(errorHandler())
                .build();
    }

    protected ExchangeFilterFunction errorHandler() {
        return ExchangeFilterFunction.ofResponseProcessor(clientResponse -> {
            if (clientResponse.statusCode().is2xxSuccessful()) {
                return Mono.just(clientResponse);
            } else {
                return clientResponse.bodyToMono(String.class)
                        .flatMap(errorBody -> Mono.error(new CardFetchException(errorBody.trim())));
            }
        });
    }
}
```
