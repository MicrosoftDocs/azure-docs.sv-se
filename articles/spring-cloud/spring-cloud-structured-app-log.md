---
title: Strukturerad program logg för Azure våren Cloud | Microsoft Docs
description: Den här artikeln förklarar hur du genererar och samlar in strukturerade program logg data i Azure våren Cloud.
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/13/2021
ms.author: brendanm
ms.custom: devx-track-java
ms.openlocfilehash: c5c35fe8a352a1bc3467e9512a7fcbc068375bfb
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99056183"
---
# <a name="structured-application-log-for-azure-spring-cloud"></a>Strukturerad program logg för Azure våren Cloud

Den här artikeln förklarar hur du genererar och samlar in strukturerade program logg data i Azure våren Cloud. Med rätt konfiguration tillhandahåller Azure våren Cloud användbara program logg frågor och analyser genom Log Analytics.

## <a name="log-schema-requirements"></a>Logg schema krav
För att förbättra logg frågans upplevelse måste en program logg vara i JSON-format och följa ett schema. Azure våren Cloud använder det här schemat för att analysera ditt program och strömma till Log Analytics. 

**Krav för JSON-schema:**

| JSON-nyckel      | JSON-värdetyp|  Obligatorisk | Kolumn i Log Analytics| Description |
| --------------| ------------|-----------|-----------------|--------------------------|
| timestamp     | sträng      |     Ja   | AppTimestamp    | tidsstämpel i UTC-format  |
| loggar        | sträng      |     No    | Loggar          | loggar                   |
| nivå         | sträng      |     No    | CustomLevel     | loggnings nivå                |
| trådtoken        | sträng      |     No    | Trådtoken          | trådtoken                   |
| meddelande       | sträng      |     No    | Meddelande         | logg meddelande              |
| stackTrace    | sträng      |     No    | StackTrace      | undantags stack spårning    |
| exceptionClass| sträng      |     No    | ExceptionClass  | undantags klass namn     |
| MDC           | kapslad JSON |     Inga    |                 | mappad diagnostisk kontext|
| MDC. traceId   | sträng      |     No    | TraceId         |spårnings-ID för distribuerad spårning|
| MDC. spanId    | sträng      |     No    | SpanId          |intervall-ID för distribuerad spårning |
|               |             |           |                 |                          |

* Fältet "tidsstämpel" är obligatoriskt och ska vara i UTC-format, alla andra fält är valfria.
* "traceId" och "spanId" i fältet "MDC" används för spårnings ändamål.
* Logga varje JSON-post på en rad. 

**Exempel på logg post** 
 ```
{"timestamp":"2021-01-08T09:23:51.280Z","logger":"com.example.demo.HelloController","level":"ERROR","thread":"http-nio-1456-exec-4","mdc":{"traceId":"c84f8a897041f634","spanId":"c84f8a897041f634"},"stackTrace":"java.lang.RuntimeException: get an exception\r\n\tat com.example.demo.HelloController.throwEx(HelloController.java:54)\r\n\","message":"Got an exception","exceptionClass":"RuntimeException"}
```

## <a name="generate-schema-compliant-json-log"></a>Skapa schema kompatibel JSON-logg  
För våren-program kan du generera förväntat JSON-logg format med vanliga [loggnings ramverk](https://docs.spring.io/spring-boot/docs/2.1.13.RELEASE/reference/html/boot-features-logging.html#boot-features-custom-log-configuration), till exempel [logback](http://logback.qos.ch/) och [log4j2](https://logging.apache.org/log4j/2.x/). 

### <a name="log-with-logback"></a>Logga med logback 
När du använder våren Boot-startstartare används logback som standard. För logback-appar använder du [logstash-Encoder](https://github.com/logstash/logstash-logback-encoder) för att generera JSON-formaterad logg. Den här metoden stöds i vår start version 2.1 +. 

Proceduren:

1. Lägg till logstash-beroende i pom.xml-filen. 

    ```json
    <dependency>
        <groupId>net.logstash.logback</groupId>
        <artifactId>logstash-logback-encoder</artifactId>
        <version>6.5</version>
    </dependency>
    ```
1. Uppdatera logback.xml konfigurations fil för att ange JSON-formatet.
    ```json
    <configuration>
        <appender name="stdout" class="ch.qos.logback.core.ConsoleAppender">
            <encoder class="net.logstash.logback.encoder.LoggingEventCompositeJsonEncoder">
            <providers>
                <timestamp>
                    <fieldName>timestamp</fieldName>
                    <timeZone>UTC</timeZone>
                </timestamp>
                <loggerName>
                    <fieldName>logger</fieldName>
                </loggerName>
                <logLevel>
                    <fieldName>level</fieldName>
                </logLevel>
                <threadName>
                    <fieldName>thread</fieldName>
                </threadName>
                <nestedField>
                    <fieldName>mdc</fieldName>
                    <providers>
                        <mdc/>
                    </providers>
                </nestedField>
                <stackTrace>
                    <fieldName>stackTrace</fieldName>
                </stackTrace>
                <message/>
                <throwableClassName>
                    <fieldName>exceptionClass</fieldName>
                </throwableClassName>
            </providers>
            </encoder>
        </appender>
        <root level="info">
            <appender-ref ref="stdout"/>
        </root>
    </configuration>
    ```

### <a name="log-with-log4j2"></a>Logga med log4j2 

För log4j2-appar använder du [JSON-Template-layout](https://logging.apache.org/log4j/2.x/manual/json-template-layout.html) för att generera JSON-formaterad logg. Den här metoden stöds i vår start version 2.1 +.

Proceduren:

1. Ta `spring-boot-starter-logging` bort `spring-boot-starter` beroenden från, Lägg till beroenden `spring-boot-starter-log4j2` `log4j-layout-template-json` i pom.xml-filen.

    ```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <exclusions>
                    <exclusion>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-starter-logging</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-log4j2</artifactId>
        </dependency>
        <dependency>
            <groupId>org.apache.logging.log4j</groupId>
            <artifactId>log4j-layout-template-json</artifactId>
            <version>2.14.0</version>
        </dependency>
    ```

2. Förbered en mall för JSON-layoutblock jsonTemplate.jsi din klass Sök väg.

    ```json
        {
            "mdc": {
                "$resolver": "mdc"
            },
            "exceptionClass": {
                "$resolver": "exception",
                "field": "className"
            },
            "stackTrace": {
                "$resolver": "exception",
                "field": "stackTrace",
                "stringified": true
            },
            "message": {
                "$resolver": "message",
                "stringified": true
            },
            "thread": {
                "$resolver": "thread",
                "field": "name"
            },
            "timestamp": {
                "$resolver": "timestamp",
                "pattern": {
                    "format": "yyyy-MM-dd'T'HH:mm:ss.SSS'Z'",
                    "timeZone": "UTC"
                }
            },
            "level": {
                "$resolver": "level",
                "field": "name"
            },
            "logger": {
                "$resolver": "logger",
                "field": "name"
            }
        }
    ```

3. Använd den här mallen för JSON-layout i log4j2.xml konfigurations filen. 

    ```json
        <configuration>
            <appenders>
                <console name="Console" target="SYSTEM_OUT">
                <JsonTemplateLayout eventTemplateUri="classpath:jsonTemplate.json"/>
                </console>
            </appenders>
            <loggers>
                <root level="info">
                <appender-ref ref="Console"/>
                </root>
            </loggers>
        </configuration>
    ```

## <a name="analyze-the-logs-in-log-analytics"></a>Analysera loggarna i Log Analytics

När programmet har kon figurer ATS på rätt sätt strömmas program konsol loggen till Log Analytics. Strukturen möjliggör effektiv fråga i Log Analytics.

### <a name="check-log-structure-in-log-analytics"></a>Kontrol lera logg strukturen i Log Analytics
Följ dessa steg:
1. Gå till tjänst översikts sidan för din tjänst instans.
2. Klicka på `Logs` post under `Monitoring` avsnitt.
3. Kör den här frågan.

   ```
   AppPlatformLogsforSpring
   | where TimeGenerated > ago(1h)
   | project AppTimestamp, Logger, CustomLevel, Thread, Message, ExceptionClass, StackTrace, TraceId, SpanId
   ```

4. Program loggar återgår som visas i följande bild:

![JSON-logg show](media/spring-cloud-structured-app-log/json-log-query.png)

### <a name="show-log-entries-containing-errors"></a>Visa logg poster som innehåller fel

Om du vill granska logg poster som har ett fel kör du följande fråga:

```
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h) and CustomLevel == "ERROR" 
| project AppTimestamp, Logger, ExceptionClass, StackTrace, Message, AppName 
| sort by AppTimestamp
```

Använd den här frågan för att hitta fel eller ändra sökorden för att hitta en speciell undantags klass eller felkod. 

### <a name="show-log-entries-for-a-specific-traceid"></a>Visa logg poster för en speciell traceId
Om du vill granska logg poster för ett speciellt spårnings-ID "trace_id" kör du följande fråga:

```
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where TraceId == "trace_id" 
| project AppTimestamp, Logger, TraceId, SpanId, StackTrace, Message, AppName 
| sort by AppTimestamp
```

## <a name="next-steps"></a>Nästa steg
* Mer information om logg frågan finns i [Kom igång med logg frågor i Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)
