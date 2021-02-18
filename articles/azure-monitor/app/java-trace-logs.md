---
title: Utforska Java trace-loggar i Azure Application Insights
description: Sök Log4J-eller logback-spårningar i Application Insights
ms.topic: conceptual
ms.date: 05/18/2019
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 4013b60a5718de6c196e806f894503f63ec9dafd
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100593680"
---
# <a name="explore-java-trace-logs-in-application-insights"></a>Utforska Java trace-loggar i Application Insights

> [!IMPORTANT]
> Den metod som beskrivs i det här dokumentet rekommenderas inte längre.
>
> Den rekommenderade metoden för att övervaka Java-program är att använda den automatiska Instrumentation utan att ändra koden. Följ rikt linjerna för [Application Insights Java 3,0-agenten](./java-in-process-agent.md).

Om du använder logback eller Log4J (v 1.2 eller v 2.0) för spårning kan du låta spårnings loggarna skickas automatiskt till Application Insights där du kan utforska och söka efter dem.

> [!TIP]
> Du behöver bara ange din Application Insights Instrumentation-nyckel en gång för ditt program. Om du använder ett ramverk som Java våren kanske du redan har registrerat nyckeln någon annan stans i appens konfiguration.

## <a name="using-the-application-insights-java-agent"></a>Använda Application Insights Java-agenten

Som standard samlar Application Insights Java-agenten automatiskt in loggning som utförs på `WARN` nivå och över.

Du kan ändra tröskelvärdet för loggning som samlas in med hjälp av `AI-Agent.xml` filen:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn>
         <Logging threshold="info"/>
      </BuiltIn>
   </Instrumentation>
</ApplicationInsightsAgent>
```

Du kan inaktivera loggnings avbildningen av Java-agenten med hjälp av `AI-Agent.xml` filen:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn>
         <Logging enabled="false"/>
      </BuiltIn>
   </Instrumentation>
</ApplicationInsightsAgent>
```

## <a name="alternatively-as-opposed-to-using-the-java-agent-you-can-follow-the-instructions-below"></a>Du kan också (till skillnad från Java-agenten) följa anvisningarna nedan

### <a name="install-the-java-sdk"></a>Installera Java SDK

Följ anvisningarna för att installera [Application Insights SDK för Java][java], om du inte redan har gjort det.

### <a name="add-logging-libraries-to-your-project"></a>Lägg till logg bibliotek i projektet
*Välj lämplig metod för ditt projekt.*

#### <a name="if-youre-using-maven"></a>Om du använder Maven …
Om ditt projekt redan har kon figurer ATS för att använda Maven för build, sammanfoga ett av följande kod avsnitt i din pom.xml-fil.

Uppdatera sedan projekt beroendena för att få de hämtade binärfilerna.

*Logback*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-logback</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J v 2.0*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j2</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J v 1.2*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j1_2</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

#### <a name="if-youre-using-gradle"></a>Om du använder Gradle …
Om ditt projekt redan har kon figurer ATS för att använda Gradle för Build lägger du till någon av följande rader `dependencies` i gruppen i filen build. Gradle:

Uppdatera sedan projekt beroendena för att få de hämtade binärfilerna.

**Logback**

```

    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-logback', version: '2.0.+'
```

**Log4J v 2.0**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j2', version: '2.0.+'
```

**Log4J v 1.2**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j1_2', version: '2.0.+'
```

#### <a name="otherwise-"></a>Eller …
Följ rikt linjerna för att installera Application Insights Java SDK manuellt, Hämta burken (när du har tillkommer till Central sidan i maven klickar du på länken ' jar ' i nedladdnings avsnittet) för lämplig tillägg och lägger till den nedladdade tilläggs jar i projektet.

| Loggar | Ladda ned | Bibliotek |
| --- | --- | --- |
| Logback |[Logback tilläggsprogram-jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-logback%22) |applicationinsights-Logging-logback |
| Log4J v 2.0 |[Log4J v2-tilläggsprogram](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j2%22) |applicationinsights-Logging-log4j2 |
| Log4j v 1.2 |[Log4J v 1.2 tillfogar jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j1_2%22) |applicationinsights-Logging-log4j1_2 |


### <a name="add-the-appender-to-your-logging-framework"></a>Lägg till tillägget i loggnings ramverket
Du börjar hämta spår genom att slå samman det relevanta kodfragmentet till Log4J-eller logback-konfigurations filen: 

*Logback*

```XML

    <appender name="aiAppender" 
      class="com.microsoft.applicationinsights.logback.ApplicationInsightsAppender">
        <instrumentationKey>[APPLICATION_INSIGHTS_KEY]</instrumentationKey>
    </appender>
    <root level="trace">
      <appender-ref ref="aiAppender" />
    </root>
```

*Log4J v 2.0*

```XML

    <Configuration packages="com.microsoft.applicationinsights.log4j.v2">
      <Appenders>
        <ApplicationInsightsAppender name="aiAppender" instrumentationKey="[APPLICATION_INSIGHTS_KEY]" />
      </Appenders>
      <Loggers>
        <Root level="trace">
          <AppenderRef ref="aiAppender"/>
        </Root>
      </Loggers>
    </Configuration>
```

*Log4J v 1.2*

```XML

    <appender name="aiAppender" 
         class="com.microsoft.applicationinsights.log4j.v1_2.ApplicationInsightsAppender">
        <param name="instrumentationKey" value="[APPLICATION_INSIGHTS_KEY]" />
    </appender>
    <root>
      <priority value ="trace" />
      <appender-ref ref="aiAppender" />
    </root>
```

Application Insights tillägg kan refereras till av en konfigurerad loggare och inte nödvändigt vis av rot loggaren (som visas i kod exemplen ovan).

## <a name="explore-your-traces-in-the-application-insights-portal"></a>Utforska spårningarna i Application Insightss portalen
Nu när du har konfigurerat projektet för att skicka spår till Application Insights kan du Visa och söka igenom dessa spårningar i Application Insights portalen på [Sök][diagnostic] bladet.

Undantag som skickas via loggar visas i portalen som telemetri för undantag.

![Öppna Sök i Application Insights-portalen](./media/java-trace-logs/01-diagnostics.png)

## <a name="next-steps"></a>Nästa steg
[Diagnostiksökning][diagnostic]

<!--Link references-->

[diagnostic]: ./diagnostic-search.md
[java]: java-get-started.md

