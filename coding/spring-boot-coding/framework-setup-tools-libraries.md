# Framework setup, tools, libraries



## HTTP clients

For basic usage, 2 HTTP Client libraries should be used:&#x20;

* WebClient
*   OpenFeign (part of Spring Boot Cloud libraries)&#x20;

    * Tutorials&#x20;
      * [https://levelup.gitconnected.com/how-to-build-rest-api-client-using-feign-in-spring-boot-50db38289420](https://levelup.gitconnected.com/how-to-build-rest-api-client-using-feign-in-spring-boot-50db38289420)
      * [https://www.baeldung.com/spring-cloud-openfeign](https://www.baeldung.com/spring-cloud-openfeign)
      * It supports non-blocking via 3rd party library (Flux, Mono) - [https://github.com/PlaytikaOSS/feign-reactive](https://github.com/PlaytikaOSS/feign-reactive)



Don't use Rest template! [https://www.baeldung.com/rest-template](https://www.baeldung.com/rest-template)

It's going to be deprecated. &#x20;
