---
title: Azure Monitor Application Insights Java
description: Övervakning av program prestanda för Java-program som körs i vilken miljö som helst utan kod ändring. Distribuerad spårning och program karta.
ms.topic: conceptual
ms.date: 03/29/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 811827c1053349d4fa80a25e5cf362331e5d87bc
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100383185"
---
# <a name="java-codeless-application-monitoring-azure-monitor-application-insights"></a>Java-kodad program övervakning Azure Monitor Application Insights

Java-kodad program övervakning är allt om enkelhet – det finns inga kod ändringar, Java-agenten kan aktive ras via bara ett par konfigurations ändringar.

 Java-agenten fungerar i valfri miljö och gör att du kan övervaka alla Java-program. Med andra ord, oavsett om du kör Java-appar på virtuella datorer, lokalt, i AKS i Windows, Linux – du namnger den, kommer Java 3,0-agenten att övervaka din app.

Det krävs inte längre att lägga till Application Insights Java SDK i programmet, eftersom 3,0-agenten automatiskt samlar in begär Anden, beroenden och loggar på egen hand.

Du kan fortfarande skicka anpassad telemetri från ditt program. 3,0-agenten spårar och korrelerar den tillsammans med all automatisk insamlad telemetri.

3,0-agenten stöder Java 8 och senare.

## <a name="quickstart"></a>Snabbstart

**1. Ladda ned agenten**

> [!WARNING]
> **Om du uppgraderar från 3,0 för hands version**
>
> Granska alla [konfigurations alternativ](./java-standalone-config.md) noggrant, eftersom JSON-strukturen har ändrats helt och hållet, förutom själva fil namnet som var i gemener.

Ladda ned [applicationinsights-agent-3.0.2. jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.2/applicationinsights-agent-3.0.2.jar)

**2. peka JVM till agenten**

Lägg till `-javaagent:path/to/applicationinsights-agent-3.0.2.jar` i programmets JVM-argument

Typiska JVM-argument inkluderar `-Xmx512m` och `-XX:+UseG1GC` . Så om du vet var du vill lägga till dessa, vet du redan var du ska lägga till det.

Mer hjälp om hur du konfigurerar programmets JVM-argument finns i [tips för att uppdatera dina JVM-argument](./java-standalone-arguments.md).

**3. peka agenten till din Application Insights-resurs**

Om du inte redan har en Application Insights resurs kan du skapa en ny genom att följa stegen i guiden för att [Skapa resurser](./create-new-resource.md).

Peka agenten till Application Insights resurs, antingen genom att ange en miljö variabel:

```
APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=...
```

Eller genom att skapa en konfigurations fil med namnet `applicationinsights.json` och placera den i samma katalog som `applicationinsights-agent-3.0.2.jar` , med följande innehåll:

```json
{
  "connectionString": "InstrumentationKey=..."
}
```

Du kan hitta din anslutnings sträng i Application Insights-resursen:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights anslutnings sträng":::

**4!!!**

Starta ditt program och gå till din Application Insights-resurs i Azure Portal för att se dina övervaknings data.

> [!NOTE]
> Det kan ta några minuter innan dina övervaknings data visas i portalen.


## <a name="configuration-options"></a>Konfigurationsalternativ

I `applicationinsights.json` filen kan du också konfigurera:

* Namn på moln roll
* Moln roll instans
* Samling
* JMX mått
* Anpassade dimensioner
* Telemetri-processorer (för hands version)
* Automatisk insamlad loggning
* Automatiskt insamlade micrometer-mått (inklusive värden för våren Boot-motstånd)
* Pulsslag
* HTTP-proxy
* Själv diagnostik

Se [konfigurations alternativ](./java-standalone-config.md) för fullständig information.

## <a name="auto-collected-requests-dependencies-logs-and-metrics"></a>Automatiskt insamlade begär Anden, beroenden, loggar och mått

### <a name="requests"></a>Begäranden

* JMS-konsumenter
* Kafka-konsumenter
* Nett/webflöde
* Servlets
* Våren-schemaläggning

### <a name="dependencies-with-distributed-trace-propagation"></a>Beroenden med Distributed trace-spridning

* Apache HttpClient och HttpAsyncClient
* gRPC
* Java. net. HttpURLConnection
* JMS
* Kafka
* Nett klient
* OkHttp

### <a name="other-dependencies"></a>Andra beroenden

* Cassandra
* JDBC
* MongoDB (asynkron och synkronisering)
* Redis (sallat och Jedis)

### <a name="logs"></a>Loggar

* Java. util. logging
* Log4J (inklusive MDC-egenskaper)
* SLF4J/logback (inklusive MDC-egenskaper)

### <a name="metrics"></a>Mått

* Micrometer (inklusive egenskaper för vårens start motstånd)
* JMX mått

## <a name="send-custom-telemetry-from-your-application"></a>Skicka anpassad telemetri från ditt program

Vårt mål i 3.0 + är att du ska kunna skicka din anpassade telemetri med standard-API: er.

Vi stöder micrometer, populära loggnings ramverk och Application Insights Java 2. x SDK hittills.
Application Insights Java 3,0 samlar automatiskt in telemetri som skickas via dessa API: er och korrelerar dem med automatiskt insamlad telemetri.

### <a name="supported-custom-telemetry"></a>Anpassad telemetri stöds

Tabellen nedan representerar anpassade typer av anpassade telemetri som stöds för närvarande och som du kan använda för att komplettera Java 3,0-agenten. För att sammanfatta kan anpassade mått hanteras via micrometer, anpassade undantag och spårningar kan aktive ras via loggnings ramverk och alla typer av anpassad telemetri stöds via [Application Insights Java 2. x SDK](#send-custom-telemetry-using-the-2x-sdk).

|                     | Micrometer | Log4j, logback, JUL | 2. x SDK |
|---------------------|------------|---------------------|---------|
| **Anpassade händelser**   |            |                     |  Ja    |
| **Anpassade mått**  |  Ja       |                     |  Ja    |
| **Beroenden**    |            |                     |  Ja    |
| **Undantag**      |            |  Ja                |  Ja    |
| **Sid visningar**      |            |                     |  Ja    |
| **Begäranden**        |            |                     |  Ja    |
| **Spårningar**          |            |  Ja                |  Ja    |

Vi planerar inte att lansera en SDK med Application Insights 3,0 för tillfället.

Application Insights Java 3,0 lyssnar redan efter telemetri som skickas till Application Insights Java 2. x SDK. Den här funktionen är en viktig del av uppgraderings artikeln för befintliga 2. x-användare och det fyller en viktig lucka i vårt stöd för anpassad telemetri tills API: t opentelemetri är GA.

### <a name="send-custom-metrics-using-micrometer"></a>Skicka anpassade mått med micrometer

Lägg till micrometer i ditt program:

```xml
<dependency>
  <groupId>io.micrometer</groupId>
  <artifactId>micrometer-core</artifactId>
  <version>1.6.1</version>
</dependency>
```

Använd den globala micrometer- [registret](https://micrometer.io/docs/concepts#_global_registry) för att skapa en mätare:

```java
static final Counter counter = Metrics.counter("test_counter");
```

och använder den för att registrera mått:

```java
counter.increment();
```

### <a name="send-custom-traces-and-exceptions-using-your-favorite-logging-framework"></a>Skicka anpassade spår och undantag med ditt favorit loggnings ramverk

Log4j, logback och Java. util. logging är automatiskt instrumenterade och loggning som utförs via dessa loggnings ramverk samlas automatiskt in som spårning och undantags telemetri.

Som standard samlas loggning endast in när loggningen utförs på informations nivån eller över.
Se [konfigurations alternativen](./java-standalone-config.md#auto-collected-logging) för hur du ändrar den här nivån.

Om du vill koppla anpassade dimensioner till dina loggar kan du använda [Log4j 1,2 MDC](https://logging.apache.org/log4j/1.2/apidocs/org/apache/log4j/MDC.html), [log4j 2 MDC](https://logging.apache.org/log4j/2.x/manual/thread-context.html)eller [logback MDC](http://logback.qos.ch/manual/mdc.html), och Application Insights Java 3,0 samlar automatiskt in dessa MDC egenskaper som anpassade dimensioner för telemetri och undantag.

### <a name="send-custom-telemetry-using-the-2x-sdk"></a>Skicka anpassad telemetri med hjälp av 2. x SDK

Lägg till `applicationinsights-core-2.6.2.jar` i ditt program (alla 2. x-versioner stöds av Application Insights Java 3,0, men det är värt att använda det senaste om du har ett val):

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-core</artifactId>
  <version>2.6.2</version>
</dependency>
```

Skapa en TelemetryClient:

  ```java
static final TelemetryClient telemetryClient = new TelemetryClient();
```

och använder den för att skicka anpassad telemetri:

##### <a name="events"></a>Händelser

```java
telemetryClient.trackEvent("WinGame");
```

##### <a name="metrics"></a>Mått

```java
telemetryClient.trackMetric("queueLength", 42.0);
```

##### <a name="dependencies"></a>Beroenden

```java
boolean success = false;
long startTime = System.currentTimeMillis();
try {
    success = dependency.call();
} finally {
    long endTime = System.currentTimeMillis();
    RemoteDependencyTelemetry telemetry = new RemoteDependencyTelemetry();
    telemetry.setSuccess(success);
    telemetry.setTimestamp(new Date(startTime));
    telemetry.setDuration(new Duration(endTime - startTime));
    telemetryClient.trackDependency(telemetry);
}
```

##### <a name="logs"></a>Loggar

```java
telemetryClient.trackTrace(message, SeverityLevel.Warning, properties);
```

##### <a name="exceptions"></a>Undantag

```java
try {
    ...
} catch (Exception e) {
    telemetryClient.trackException(e);
}
```

### <a name="add-request-custom-dimensions-using-the-2x-sdk"></a>Lägg till anpassade dimensioner för begäran med hjälp av 2. x SDK

> [!NOTE]
> Den här funktionen är endast i 3.0.2 och senare

Lägg till `applicationinsights-web-2.6.2.jar` i ditt program (alla 2. x-versioner stöds av Application Insights Java 3,0, men det är värt att använda det senaste om du har ett val):

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.2</version>
</dependency>
```

och Lägg till anpassade dimensioner i koden:

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
requestTelemetry.getProperties().put("mydimension", "myvalue");
```

### <a name="set-the-request-telemetry-user_id-using-the-2x-sdk"></a>Ange telemetri för begäran user_Id med hjälp av 2. x SDK

> [!NOTE]
> Den här funktionen är endast i 3.0.2 och senare

Lägg till `applicationinsights-web-2.6.2.jar` i ditt program (alla 2. x-versioner stöds av Application Insights Java 3,0, men det är värt att använda det senaste om du har ett val):

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.2</version>
</dependency>
```

och ange `user_Id` i din kod:

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
requestTelemetry.getContext().getUser().setId("myuser");
```

### <a name="override-the-request-telemetry-name-using-the-2x-sdk"></a>Åsidosätt namnet på begäran om telemetri med hjälp av 2. x SDK

> [!NOTE]
> Den här funktionen är endast i 3.0.2 och senare

Lägg till `applicationinsights-web-2.6.2.jar` i ditt program (alla 2. x-versioner stöds av Application Insights Java 3,0, men det är värt att använda det senaste om du har ett val):

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.2</version>
</dependency>
```

och ange namnet i koden:

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
requestTelemetry.setName("myname");
```
