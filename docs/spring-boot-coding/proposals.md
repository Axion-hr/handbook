---
layout: default
title: Proposals
parent: Spring Boot guidelines
nav_order: 1
---


## Libraries 
### Logback
Every application needs to have proper logging in place in order to function, Spring Boot comes with LogBack so we can use it as is

What to log:
- request response, this will be provided as a library to be used
- every change in the system

PROPERLY USER LOG LEVELS! 

usage:
```
class ....
    Logger logger = LoggerFactory.getLogger(Weather.class);
    
    method
        logger.info("Some text {}", variable);
```

Please don't concatenate in code like ```logger.info("Some text " + variable);```, but use place holders like they are intended to be used.

We will most probably need additional control over the logs so here is an example:
logback-spring.xml
```
<?xml version="1.0" encoding="UTF-8"?>
<configuration>

    <property name="LOGS" value="./logs" />

    <appender name="Console"
              class="ch.qos.logback.core.ConsoleAppender">
        <layout class="ch.qos.logback.classic.PatternLayout">
            <Pattern>
                %black(%d{ISO8601}) %highlight(%-5level) [%blue(%t)] %yellow(%C{1.}): %msg%n%throwable
            </Pattern>
        </layout>
    </appender>

    <appender name="RollingFile"
              class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>${LOGS}/spring-boot-logger.log</file>
        <encoder
                class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <Pattern>%d %p %C{1.} [%t] %m%n</Pattern>
        </encoder>

        <rollingPolicy
                class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!-- rollover daily and when the file reaches 10 MegaBytes -->
            <fileNamePattern>${LOGS}/archived/spring-boot-logger-%d{yyyy-MM-dd}.%i.log
            </fileNamePattern>
            <timeBasedFileNamingAndTriggeringPolicy
                    class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <maxFileSize>10MB</maxFileSize>
            </timeBasedFileNamingAndTriggeringPolicy>
        </rollingPolicy>
    </appender>

    <!-- LOG everything at INFO level -->
    <root level="info">
        <appender-ref ref="RollingFile" />
        <appender-ref ref="Console" />
    </root>

    <logger name="hr.axion" level="trace" additivity="false">
        <appender-ref ref="RollingFile" />
        <appender-ref ref="Console" />
    </logger>
</configuration>
```

#### Structured logging
In order for the logs to be machine-readable they need to be structured so the data points can be easily extracted. 
The requirement of course can be resolved in different ways, and they include:
- standard logging and parsing via regex. This is probably the easiest for development but very hard on the infrastructure
as every event type needs to have a specific regex. 
- logfmt. This needs some adoption on the dev and infra side but the current investigation shows it is 
supported by all of our tools, both on dev and infrastructure
- json. This approach is probably the most scalable and easiest to machine process, the current investigation shows that
it is difficult to implement in strongly typed languages like Java. GZ: there is a possibility to define class that would 
be serialized in a message, or maybe hashmap but I'm still investigating

#### Structured logging with logfmt

sources:
- [splunk](https://dev.splunk.com/enterprise/docs/developapps/addsupport/logging/loggingbestpractices/)
- [DataDog](https://docs.datadoghq.com/logs/log_configuration/parsing/?tab=matchers0)
- [Loki (Grafana)](https://grafana.com/blog/2020/10/28/loki-2.0-released-transform-logs-as-youre-querying-them-and-set-up-alerts-within-loki/#filter)


In addition to the regular logging configuration that would have date(UTC) LOG_LEVEL class message, the message would be structured as a key value pairs that would be parsed in log aggregation system. 
```
key1=value1, key2=value2, key3=value3
```


##### Grafana view
The log message that started as ```2022-05-24 23:14:30
22-05-24 23:12:58,805 INFO hr.axion.demo.controller.Weather [http-nio-8080-exec-1] ID=123 ime=Nela, prezime=nseto, akcija="paymant-init"```

![grafana search](./img/grafana-logfmt.png)


#### CorrelateId / TraceId
Idea to use OpenTelemetry instead of manufacturing our custom solution 
sources:
- [Splunk and good information](https://www.splunk.com/en_us/data-insider/what-is-opentelemetry.html#important-to-devops)
- [Spring boot with Grafana](https://grafana.com/blog/2022/04/26/set-up-and-observe-a-spring-boot-application-with-grafana-cloud-prometheus-and-opentelemetry/https://grafana.com/blog/2022/04/26/set-up-and-observe-a-spring-boot-application-with-grafana-cloud-prometheus-and-opentelemetry/)
- [DataDog](https://docs.datadoghq.com/tracing/setup_overview/open_standards/)
- [AWS CloudWatch](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch-Agent-open-telemetry.html)


### Lombok
One of the biggest complaints against Java is how much noise can be found in a single class. Project Lombok saw this as a problem and aims to reduce the noise of some of the worst offenders by replacing them with a simple set of annotations.

Most used annotations
- @Getter/@Setter - adds getter / setter
- @ToString - adds toString 
- @EqualsAndHashCode - adds equals and hashCode
- @Data - adds everything