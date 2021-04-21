---
title: Azure Monitor Application Insights Java
description: Övervakning av programprestanda för Java-program som körs i alla miljöer utan att kod behöver ändras. Distribuerad spårning och programkarta.
ms.topic: conceptual
ms.date: 03/29/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 3f22e165fe4a3f86ecce8b1e307b19fae0eeac81
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812056"
---
# <a name="java-codeless-application-monitoring-azure-monitor-application-insights"></a>Övervakning av kodlösa Java-Azure Monitor Application Insights

Övervakning av kodlösa Java-program handlar om enkelhet – det finns inga kodändringar. Java-agenten kan aktiveras med bara några konfigurationsändringar.

 Java-agenten fungerar i alla miljöer och gör att du kan övervaka alla dina Java-program. Oavsett om du kör dina Java-appar på virtuella datorer, lokalt, i AKS, i Windows, Linux , kommer Java 3.0-agenten med andra ord att övervaka din app.

Att lägga Application Insights Java SDK till ditt program krävs inte längre eftersom 3.0-agenten automatiskt samlar in begäranden, beroenden och loggar på egen hand.

Du kan fortfarande skicka anpassad telemetri från ditt program. 3.0-agenten spårar och korrelerar den med all automatiskt insamlade telemetri.

3.0-agenten stöder Java 8 och högre.

## <a name="quickstart"></a>Snabbstart

**1. Ladda ned agenten**

> [!WARNING]
> **Om du uppgraderar från 3.0 Preview**
>
> Granska alla [konfigurationsalternativ noggrant](./java-standalone-config.md) eftersom json-strukturen har ändrats helt, förutom själva filnamnet, som bara innehåller gemener.

Ladda [ned applicationinsights-agent-3.0.3.jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.3/applicationinsights-agent-3.0.3.jar)

**2. Peka JVM till agenten**

Lägg `-javaagent:path/to/applicationinsights-agent-3.0.3.jar` till i programmets JVM-args

Typiska JVM-args inkluderar `-Xmx512m` och `-XX:+UseG1GC` . Så om du vet var du ska lägga till dessa vet du redan var du ska lägga till detta.

Mer hjälp med att konfigurera programmets JVM-args finns i [Tips för att uppdatera JVM-args](./java-standalone-arguments.md).

**3. Peka agenten på Application Insights resurs**

Om du inte redan har en Application Insights resurs kan du skapa en ny genom att följa stegen i guiden [för resursskapande.](./create-new-resource.md)

Peka agenten till Application Insights resurs, antingen genom att ange en miljövariabel:

```
APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=...
```

Eller genom att skapa en `applicationinsights.json` konfigurationsfil med namnet och placera den i samma katalog som , med följande `applicationinsights-agent-3.0.3.jar` innehåll:

```json
{
  "connectionString": "InstrumentationKey=..."
}
```

Du hittar anslutningssträngen i din Application Insights resurs:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights anslutningssträng":::

**4. Det var allt!**

Starta nu programmet och gå till din Application Insights i Azure Portal för att se dina övervakningsdata.

> [!NOTE]
> Det kan ta några minuter innan dina övervakningsdata visas i portalen.


## <a name="configuration-options"></a>Konfigurationsalternativ

I `applicationinsights.json` filen kan du dessutom konfigurera:

* Namn på molnroll
* Molnrollinstans
* Samling
* JMX-mått
* Anpassade dimensioner
* Telemetriprocessorer (förhandsversion)
* Automatiskt insamlade loggning
* Automatiskt insamlade Micrometer-mått (inklusive Spring Boot Actuator-mått)
* Pulsslag
* HTTP-proxy
* Självdiagnostik

Fullständig [information finns i](./java-standalone-config.md) Konfigurationsalternativ.

## <a name="auto-collected-requests-dependencies-logs-and-metrics"></a>Automatiskt insamlade begäranden, beroenden, loggar och mått

### <a name="requests"></a>Begäranden

* JMS-konsumenter
* Kafka-konsumenter
* Netty/WebFlux
* Servlets
* Spring Scheduling

### <a name="dependencies-with-distributed-trace-propagation"></a>Beroenden med distribuerad spårningsspridning

* Apache HttpClient och HttpAsyncClient
* gRPC
* java.net.HttpURLConnection
* Jms
* Kafka
* Netty-klient
* OkHttp

### <a name="other-dependencies"></a>Andra beroenden

* Cassandra
* JDBC
* MongoDB (asynkron och synkronisering)
* Redis (Lettuce och Jedis)

### <a name="logs"></a>Loggar

* java.util.logging
* Log4j (inklusive MDC-egenskaper)
* SLF4J/Logback (inklusive MDC-egenskaper)

### <a name="metrics"></a>Mått

* Micrometer (inklusive Spring Boot Actuator-mått)
* JMX-mått

### <a name="azure-sdks-preview"></a>Azure-SDK:er (förhandsversion)

Se [konfigurationsalternativen för](./java-standalone-config.md#auto-collected-azure-sdk-telemetry-preview) att aktivera den här förhandsgranskningsfunktionen och avbilda telemetri som genereras av dessa Azure-SDK:er:

* [App Configuration](https://docs.microsoft.com/java/api/overview/azure/data-appconfiguration-readme) 1.1.10+
* [Cognitive Search](https://docs.microsoft.com/java/api/overview/azure/search-documents-readme) 11.3.0+
* [Kommunikationschatt](https://docs.microsoft.com/java/api/overview/azure/communication-chat-readme) 1.0.0+
* [Communication Common](https://docs.microsoft.com/java/api/overview/azure/communication-common-readme) 1.0.0+
* [Kommunikationsidentitet](https://docs.microsoft.com/java/api/overview/azure/communication-identity-readme) 1.0.0+
* [Kommunikations-SMS](https://docs.microsoft.com/java/api/overview/azure/communication-sms-readme) 1.0.0+
* [Cosmos DB](https://docs.microsoft.com/java/api/overview/azure/cosmos-readme) 4.13.0+
* [Event Grid](https://docs.microsoft.com/java/api/overview/azure/messaging-eventgrid-readme) 4.0.0+
* [Event Hubs](https://docs.microsoft.com/java/api/overview/azure/messaging-eventhubs-readme) 5.6.0+
* [Event Hubs – Azure Blob Storage Checkpoint Store](https://docs.microsoft.com/java/api/overview/azure/messaging-eventhubs-checkpointstore-blob-readme) 1.5.1+
* [Formigenkänning](https://docs.microsoft.com/java/api/overview/azure/ai-formrecognizer-readme) 3.0.6+
* [Identitet](https://docs.microsoft.com/java/api/overview/azure/identity-readme) 1.2.4+
* [Key Vault – Certifikat](https://docs.microsoft.com/java/api/overview/azure/security-keyvault-certificates-readme) 4.1.6+
* [Key Vault – Nycklar](https://docs.microsoft.com/java/api/overview/azure/security-keyvault-keys-readme) 4.2.6+
* [Key Vault – Secrets](https://docs.microsoft.com/java/api/overview/azure/security-keyvault-secrets-readme) 4.2.6+
* [Service Bus](https://docs.microsoft.com/java/api/overview/azure/messaging-servicebus-readme) 7.1.0+
* [Textanalys](https://docs.microsoft.com/java/api/overview/azure/ai-textanalytics-readme) 5.0.4+

[//]: # "ovanstående namn och länkar som är uppkrapade från https://azure.github.io/azure-sdk/releases/latest/java.html"
[//]: # "och versionssynkronisering skulle manuellt mot den äldsta versionen i Maven Central som bygger på azure-core 1.14.0"
[//]: # ""
[//]: # "var table = document.querySelector('#tg-sb-content > div > table')"
[//]: # "var str = ''"
[//]: # "för (var i = 1, row; row = table.rows[i]; i++) {"
[//]: # "  var name = row.cells[0].getElementsByTagName('div')[0].textContent.trim()"
[//]: # "  var stableRow = row.cells[1]"
[//]: # "  var versionBadge = stableRow.querySelector('.badge')"
[//]: # "  if (!versionBadge) {"
[//]: # "    Fortsätta"
[//]: # "  }"
[//]: # "  var version = versionBadge.textContent.trim()"
[//]: # "  var link = stableRow.querySelectorAll('a')[2].href"
[//]: # "  str += '* [' + name + '](' + länk + ') ' + version"
[//]: # "}"
[//]: # "console.log(str)"

## <a name="send-custom-telemetry-from-your-application"></a>Skicka anpassad telemetri från ditt program

Vårt mål i 3.0+ är att du ska kunna skicka din anpassade telemetri med hjälp av standard-API:er.

Vi stöder Micrometer, populära loggningsramverk och Application Insights Java 2.x SDK hittills.
Application Insights Java 3.0 samlar automatiskt in telemetri som skickas via dessa API:er och korrelerar den med automatiskt insamlade telemetri.

### <a name="supported-custom-telemetry"></a>Anpassad telemetri som stöds

Tabellen nedan representerar för närvarande stödda anpassade telemetrityper som du kan aktivera för att komplettera Java 3.0-agenten. Sammanfattningsvis stöds anpassade mått via mikrometer, anpassade undantag och spårningar kan aktiveras via loggningsramverk och alla typer av anpassad telemetri stöds via [Application Insights Java 2.x SDK](#send-custom-telemetry-using-the-2x-sdk).

|                     | Micrometer | Log4j, logback, JULI | 2.x SDK |
|---------------------|------------|---------------------|---------|
| **Anpassade händelser**   |            |                     |  Yes    |
| **Anpassade mått**  |  Ja       |                     |  Ja    |
| **Beroenden**    |            |                     |  Yes    |
| **Undantag**      |            |  Ja                |  Ja    |
| **Sidvisningar**      |            |                     |  Yes    |
| **Begäranden**        |            |                     |  Yes    |
| **Spårningar**          |            |  Ja                |  Ja    |

Vi planerar inte att släppa en SDK med Application Insights 3.0 just nu.

Application Insights Java 3.0 lyssnar redan efter telemetri som skickas till Application Insights Java 2.x SDK. Den här funktionen är en viktig del av uppgraderingsberättelsen för befintliga 2.x-användare och fyller en viktig lucka i vårt anpassade telemetristöd tills OpenTelemetry-API:et är GA.

### <a name="send-custom-metrics-using-micrometer"></a>Skicka anpassade mått med Micrometer

Lägg till Micrometer i ditt program:

```xml
<dependency>
  <groupId>io.micrometer</groupId>
  <artifactId>micrometer-core</artifactId>
  <version>1.6.1</version>
</dependency>
```

Använd det globala registret [Micrometer för](https://micrometer.io/docs/concepts#_global_registry) att skapa en mätare:

```java
static final Counter counter = Metrics.counter("test_counter");
```

och använder den för att registrera mått:

```java
counter.increment();
```

### <a name="send-custom-traces-and-exceptions-using-your-favorite-logging-framework"></a>Skicka anpassade spårningar och undantag med ditt favoritramverk för loggning

Log4j, Logback och java.util.logging instrumenteras automatiskt och loggning som utförs via dessa loggningsramverk samlas in automatiskt som spårnings- och undantagstelemetri.

Som standard samlas loggning bara in när loggning utförs på INFO-nivå eller högre.
Se [konfigurationsalternativen för](./java-standalone-config.md#auto-collected-logging) hur du ändrar den här nivån.

Om du vill koppla anpassade dimensioner till loggarna kan du använda [Log4j 1.2 MDC,](https://logging.apache.org/log4j/1.2/apidocs/org/apache/log4j/MDC.html) [Log4j 2 MDC](https://logging.apache.org/log4j/2.x/manual/thread-context.html)eller [Logback MDC](http://logback.qos.ch/manual/mdc.html)och Application Insights Java 3.0 samlar automatiskt in dessa MDC-egenskaper som anpassade dimensioner i spårnings- och undantagstelemetrin.

### <a name="send-custom-telemetry-using-the-2x-sdk"></a>Skicka anpassad telemetri med 2.x SDK

Lägg `applicationinsights-core-2.6.2.jar` till i ditt program (alla 2.x-versioner stöds av Application Insights Java 3.0, men det är värt att använda den senaste om du har ett val):

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

och använder det för att skicka anpassad telemetri:

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

### <a name="add-request-custom-dimensions-using-the-2x-sdk"></a>Lägga till anpassade dimensioner för begäran med 2.x SDK

> [!NOTE]
> Den här funktionen finns bara i 3.0.2 och senare

Lägg `applicationinsights-web-2.6.2.jar` till i ditt program (alla 2.x-versioner stöds av Application Insights Java 3.0, men det är värt att använda den senaste om du har ett val):

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.2</version>
</dependency>
```

och lägg till anpassade dimensioner i koden:

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
requestTelemetry.getProperties().put("mydimension", "myvalue");
```

### <a name="set-the-request-telemetry-user_id-using-the-2x-sdk"></a>Ställ in begärandetelemetri user_Id 2.x SDK

> [!NOTE]
> Den här funktionen finns bara i 3.0.2 och senare

Lägg `applicationinsights-web-2.6.2.jar` till i ditt program (alla 2.x-versioner stöds av Application Insights Java 3.0, men det är värt att använda den senaste om du har ett val):

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.2</version>
</dependency>
```

och ange `user_Id` i koden:

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
requestTelemetry.getContext().getUser().setId("myuser");
```

### <a name="override-the-request-telemetry-name-using-the-2x-sdk"></a>Åsidosätt begärandetelemetrinamnet med hjälp av 2.x SDK

> [!NOTE]
> Den här funktionen finns bara i 3.0.2 och senare

Lägg `applicationinsights-web-2.6.2.jar` till i ditt program (alla 2.x-versioner stöds av Application Insights Java 3.0, men det är värt att använda den senaste om du har ett val):

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

### <a name="get-the-request-telemetry-id-and-the-operation-id-using-the-2x-sdk"></a>Hämta telemetri-ID:t för begäran och åtgärds-ID:t med hjälp av 2.x SDK

> [!NOTE]
> Den här funktionen finns bara i 3.0.3 och senare

Lägg `applicationinsights-web-2.6.2.jar` till i ditt program (alla 2.x-versioner stöds av Application Insights Java 3.0, men det är värt att använda den senaste om du har ett val):

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.2</version>
</dependency>
```

och hämta telemetri-ID:t för begäran och åtgärds-ID:t i koden:

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
String requestId = requestTelemetry.getId();
String operationId = requestTelemetry.getContext().getOperation().getId();
```
