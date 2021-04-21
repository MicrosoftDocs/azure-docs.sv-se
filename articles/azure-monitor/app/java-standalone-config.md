---
title: Konfigurationsalternativ – Azure Monitor Application Insights för Java
description: Så här konfigurerar du Azure Monitor Application Insights för Java
ms.topic: conceptual
ms.date: 11/04/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: b78aaa659598e6eb58841c5cef0c209daaced5e0
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811984"
---
# <a name="configuration-options---azure-monitor-application-insights-for-java"></a>Konfigurationsalternativ – Azure Monitor Application Insights för Java

> [!WARNING]
> **Om du uppgraderar från 3.0 Preview**
>
> Granska alla konfigurationsalternativ nedan noggrant eftersom json-strukturen har ändrats helt, förutom själva filnamnet som bara innehåller gemener.

## <a name="connection-string-and-role-name"></a>Anslutningssträng och rollnamn

Anslutningssträngen och rollnamnet är de vanligaste inställningarna som behövs för att komma igång:

```json
{
  "connectionString": "InstrumentationKey=...",
  "role": {
    "name": "my cloud role name"
  }
}
```

Anslutningssträngen krävs och rollnamnet är viktigt varje gång du skickar data från olika program till samma Application Insights resurs.

Du hittar mer information och ytterligare konfigurationsalternativ nedan.

## <a name="configuration-file-path"></a>Sökväg till konfigurationsfil

Som standard Application Insights Java 3.0 att konfigurationsfilen får namnet och finns `applicationinsights.json` i samma katalog som `applicationinsights-agent-3.0.3.jar` .

Du kan ange en egen sökväg för konfigurationsfilen med hjälp av antingen

* `APPLICATIONINSIGHTS_CONFIGURATION_FILE` miljövariabel, eller
* `applicationinsights.configuration.file` Java-systemegenskap

Om du anger en relativ sökväg matchas den i förhållande till den katalog `applicationinsights-agent-3.0.3.jar` där finns.

## <a name="connection-string"></a>Anslutningssträng

Anslutningssträng krävs. Du hittar anslutningssträngen i din Application Insights resurs:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights anslutningssträng":::


```json
{
  "connectionString": "InstrumentationKey=..."
}
```

Du kan också ange anslutningssträngen med hjälp av miljövariabeln (som sedan har företräde framför anslutningssträngen `APPLICATIONINSIGHTS_CONNECTION_STRING` som anges i json-konfigurationen).

Om du inte anger anslutningssträngen inaktiveras Java-agenten.

## <a name="cloud-role-name"></a>Namn på molnroll

Namnet på molnrollen används för att märka komponenten på programkartan.

Om du vill ange namnet på molnrollen:

```json
{
  "role": {   
    "name": "my cloud role name"
  }
}
```

Om molnrollnamnet inte anges används Application Insights resursnamn för att märka komponenten på programkartan.

Du kan också ange namnet på molnrollen med hjälp av miljövariabeln (som sedan har företräde framför molnrollnamnet som `APPLICATIONINSIGHTS_ROLE_NAME` anges i json-konfigurationen).

## <a name="cloud-role-instance"></a>Molnrollinstans

Molnrollinstansen får datornamnet som standard.

Om du vill ange molnrollinstansen till något annat i stället för datornamnet:

```json
{
  "role": {
    "name": "my cloud role name",
    "instance": "my cloud role instance"
  }
}
```

Du kan också ange molnrollinstansen med hjälp av miljövariabeln (som sedan har företräde framför molnrollinstansen som `APPLICATIONINSIGHTS_ROLE_INSTANCE` anges i json-konfigurationen).

## <a name="sampling"></a>Samling

Sampling är användbart om du behöver minska kostnaderna.
Sampling utförs som en funktion på åtgärds-ID:t (även kallat spårnings-ID), så att samma åtgärds-ID alltid resulterar i samma samplingsbeslut. Detta säkerställer att du inte får delar av en distribuerad transaktion samplat in medan andra delar av den samplas ut.

Om du till exempel ställer in sampling på 10 % ser du bara 10 % av dina transaktioner, men var och en av dessa 10 % har fullständig transaktionsinformation från slutet till slut.

Här är ett exempel på hur du ställer in samplingen på att samla in ungefär **1/3** av alla transaktioner – se till att du anger den samplingsfrekvens som är korrekt för ditt användningsfall:

```json
{
  "sampling": {
    "percentage": 33.333
  }
}
```

Du kan också ange samplingsprocenten med hjälp av miljövariabeln (som sedan har företräde framför samplingsprocenten `APPLICATIONINSIGHTS_SAMPLING_PERCENTAGE` som anges i json-konfigurationen).

> [!NOTE]
> För samplingsprocenten väljer du en procentandel som är nära 100/N där N är ett heltal. För närvarande har sampling inte stöd för andra värden.

## <a name="sampling-overrides-preview"></a>Åsidosättningar av sampling (förhandsversion)

Den här funktionen är en förhandsversion från och med 3.0.3.

Med åsidosättningar av sampling kan du åsidosätta [standardsamplingsprocenten,](#sampling)till exempel:
* Ange samplingsprocenten till 0 (eller något litet värde) för hälsokontroller med brus.
* Ange samplingsprocenten till 0 (eller något litet värde) för störande beroendeanrop.
* Ange samplingsprocenten till 100 för en viktig typ av begäran (t.ex. ) även om standardsampling har `/login` konfigurerats till något lägre.

Mer information finns i dokumentationen om [åsidosättningar av sampling.](./java-standalone-sampling-overrides.md)

## <a name="jmx-metrics"></a>JMX-mått

Om du vill samla in några ytterligare JMX-mått:

```json
{
  "jmxMetrics": [
    {
      "name": "JVM uptime (millis)",
      "objectName": "java.lang:type=Runtime",
      "attribute": "Uptime"
    },
    {
      "name": "MetaSpace Used",
      "objectName": "java.lang:type=MemoryPool,name=Metaspace",
      "attribute": "Usage.used"
    }
  ]
}
```

`name` är måttnamnet som ska tilldelas det här JMX-måttet (kan vara vad som helst).

`objectName` är [objektnamnet för](https://docs.oracle.com/javase/8/docs/api/javax/management/ObjectName.html) den JMX MBean som du vill samla in.

`attribute` är attributnamnet i JMX MBean som du vill samla in.

Numeriska och booleska JMX-måttvärden stöds. Booleska JMX-mått mappas till `0` för false och för `1` true.

## <a name="custom-dimensions"></a>Anpassade dimensioner

Om du vill lägga till anpassade dimensioner i all telemetri:

```json
{
  "customDimensions": {
    "mytag": "my value",
    "anothertag": "${ANOTHER_VALUE}"
  }
}
```

`${...}` kan användas för att läsa värdet från den angivna miljövariabeln vid start.

> [!NOTE]
> Om du från och med version 3.0.2 lägger till en anpassad dimension med namnet lagras värdet i kolumnen i tabellen Application Insights Logs i stället för `service.version` `application_Version` som en anpassad dimension.

## <a name="telemetry-processors-preview"></a>Telemetriprocessorer (förhandsversion)

Den här funktionen är en förhandsversion.

Du kan konfigurera regler som ska tillämpas på telemetri för förfrågningar, beroenden och spårning, till exempel:
 * Maskera känsliga data
 * Villkorligt lägga till anpassade dimensioner
 * Uppdatera span-namnet, som används för att aggregera liknande telemetri i Azure Portal.
 * Ta bort specifika span-attribut för att kontrollera datainmatningskostnader.

Mer information finns i dokumentationen [om telemetriprocessorn.](./java-standalone-telemetry-processors.md)

> [!NOTE]
> Om du vill ta bort specifika (hela) intervall för att kontrollera inmatningskostnaden kan du se [åsidosättningar för sampling.](./java-standalone-sampling-overrides.md)

## <a name="auto-collected-logging"></a>Automatiskt insamlad loggning

Log4j, Logback och java.util.logging instrumenteras automatiskt och loggning som utförs via dessa loggningsramverk samlas in automatiskt.

Loggning samlas bara in om den först uppfyller den nivå som har konfigurerats för loggningsramverket, och för det andra också uppfyller den nivå som har konfigurerats för Application Insights.

Om ditt loggningsramverk till exempel är konfigurerat för att logga (och högre) från paketet , och Application Insights har konfigurerats för att avbilda (och högre) kommer Application Insights endast att avbilda `WARN` `com.example` `INFO` `WARN` (och högre) från paketet `com.example` .

Standardnivån som konfigurerats för Application Insights är `INFO` . Om du vill ändra den här nivån:

```json
{
  "instrumentation": {
    "logging": {
      "level": "WARN"
    }
  }
}
```

Du kan också ange nivån med hjälp av miljövariabeln (som sedan har `APPLICATIONINSIGHTS_INSTRUMENTATION_LOGGING_LEVEL` företräde framför nivån som anges i json-konfigurationen).

Det här är de giltiga värden som du kan ange i filen och hur de `level` `applicationinsights.json` motsvarar loggningsnivåer i olika loggningsramverk:

| nivå             | Log4j  | Logback | Jul     |
|-------------------|--------|---------|---------|
| OFF               | OFF    | OFF     | OFF     |
| Dödlig             | Dödlig  | ERROR   | SEVERE  |
| FEL (eller ALLVARLIGT) | ERROR  | ERROR   | SEVERE  |
| VARNA (eller VARNING) | Varna   | Varna    | WARNING |
| INFO              | INFO   | INFO    | INFO    |
| CONFIG            | FELSÖK  | FELSÖK   | CONFIG  |
| FELSÖKA (eller FINE)   | FELSÖK  | FELSÖK   | FINE    |
| FINER             | FELSÖK  | FELSÖK   | FINER   |
| SPÅRNING (eller FINEST) | Spåra  | Spåra   | FINEST  |
| ALL               | ALL    | ALL     | ALL     |

> [!NOTE]
> Om ett undantagsobjekt skickas till loggaren visas loggmeddelandet (och undantagsobjektinformationen) i Azure Portal tabellen i stället för `exceptions` `traces` tabellen.

## <a name="auto-collected-micrometer-metrics-including-spring-boot-actuator-metrics"></a>Automatiskt insamlade Micrometer-mått (inklusive Spring Boot Actuator-mått)

Om ditt program [använder Micrometer](https://micrometer.io)samlas mått som skickas till det globala registret för Micrometer in automatiskt.

Om ditt program använder [Spring Boot actuator](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html)samlas även mått som konfigurerats av Spring Boot Actuator in automatiskt.

Så här inaktiverar du automatisk insamling av Micrometer-mått (inklusive Spring Boot Actuator-mått):

> [!NOTE]
> Anpassade mått debiteras separat och kan generera ytterligare kostnader. Se till att kontrollera den detaljerade [prisinformationen](https://azure.microsoft.com/pricing/details/monitor/). Om du vill inaktivera måtten Micrometer och Spring Actuator lägger du till konfigurationen nedan i konfigurationsfilen.

```json
{
  "instrumentation": {
    "micrometer": {
      "enabled": false
    }
  }
}
```

## <a name="auto-collected-azure-sdk-telemetry-preview"></a>Automatiskt insamlade Azure SDK-telemetri (förhandsversion)

Många av de senaste Azure SDK-biblioteken skickar telemetri (se [den fullständiga listan](./java-in-process-agent.md#azure-sdks-preview)).

Från och Application Insights Java 3.0.3 kan du aktivera avbildning av den här telemetrin.

Om du vill aktivera den här funktionen:

```json
{
  "preview": {
    "instrumentation": {
      "azureSdk": {
        "enabled": true
      }
    }
  }
}
```

Du kan också aktivera den här funktionen med hjälp av miljövariabeln `APPLICATIONINSIGHTS_PREVIEW_INSTRUMENTATION_AZURE_SDK_ENABLED`
(som sedan har företräde framför aktiverat som anges i json-konfigurationen).

## <a name="suppressing-specific-auto-collected-telemetry"></a>Ignorera specifik automatiskt insamlade telemetri

Från och med version 3.0.3 kan specifik automatiskt insamlade telemetri ignoreras med hjälp av följande konfigurationsalternativ:

```json
{
  "instrumentation": {
    "cassandra": {
      "enabled": false
    },
    "jdbc": {
      "enabled": false
    },
    "jms": {
      "enabled": false
    },
    "kafka": {
      "enabled": false
    },
    "micrometer": {
      "enabled": false
    },
    "mongo": {
      "enabled": false
    },
    "redis": {
      "enabled": false
    },
    "springScheduling": {
      "enabled": false
    }
  }
}
```

Du kan också utelämna dessa instrumentationer med hjälp av dessa miljövariabler:

* `APPLICATIONINSIGHTS_INSTRUMENTATION_CASSANDRA_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_JDBC_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_JMS_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_KAFKA_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_MICROMETER_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_MONGO_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_REDIS_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_SPRING_SCHEDULING_ENABLED`

(som sedan har företräde framför aktiverat som anges i json-konfigurationen).

> Obs! Om du vill ha mer information om kontroll, t.ex. för att förhindra vissa Redis-anrop men inte alla Redis-anrop, kan du läsa [om åsidosättningar](./java-standalone-sampling-overrides.md)i sampling.

## <a name="heartbeat"></a>Pulsslag

Som standard skickar Application Insights Java 3.0 ett pulsslagsmått en gång var 15:e minut. Om du använder pulsslagsmåttet för att utlösa aviseringar kan du öka frekvensen för det här pulsslaget:

```json
{
  "heartbeat": {
    "intervalSeconds": 60
  }
}
```

> [!NOTE]
> Du kan inte öka intervallet till längre än 15 minuter, eftersom pulsslagsdata också används för att spåra Application Insights användning.

## <a name="http-proxy"></a>HTTP-proxy

Om ditt program finns bakom en brandvägg och inte kan ansluta direkt till Application Insights (se [IP-adresser](./ip-addresses.md)som används av Application Insights ) kan du konfigurera Application Insights Java 3.0 att använda en HTTP-proxy:

```json
{
  "proxy": {
    "host": "myproxy",
    "port": 8080
  }
}
```

Application Insights java 3.0 respekterar även globala `-Dhttps.proxyHost` och om de har `-Dhttps.proxyPort` angetts.

## <a name="metric-interval"></a>Måttintervall

Den här funktionen är en förhandsversion.

Som standard avbildas mått var 60:e sekund.

Från och med version 3.0.3 kan du ändra det här intervallet:

```json
{
  "preview": {
    "metricIntervalSeconds": 300
  }
}
```

Inställningen gäller för alla dessa mått:

* Standardprestandaräknare, t.ex. processor och minne
* Anpassade standardmått, t.ex. tidsinställning för skräpinsamling
* Konfigurerade JMX-mått[(se ovan)](#jmx-metrics)
* Micrometer-mått ([se ovan](#auto-collected-micrometer-metrics-including-spring-boot-actuator-metrics))


[//]: # "Obs! OpenTelemetry-stöd är i privat förhandsversion tills OpenTelemetry-API:et når 1.0"

[//]: # "## Stöd för OpenTelemetry API-versioner före 1.0"

[//]: # "Stöd för opentelemetry-API-versioner före 1.0 är valt, eftersom OpenTelemetry-API:et inte är stabilt än"
[//]: # "och därför stöder varje version av agenten endast en specifik tidigare version än 1.0 av OpenTelemetry-API:et"
[//]: # "(den här begränsningen gäller inte när OpenTelemetry API 1.0 har släppts)."

[//]: # "'''json"
[//]: # "{"
[//]: # "  \"förhandsversion: \" {"
[//]: # "    \"openTelemetryApiSupport \" : true"
[//]: # "  }"
[//]: # "}"
[//]: # "```"

## <a name="self-diagnostics"></a>Självdiagnostik

"Självdiagnostik" avser intern loggning från Application Insights Java 3.0.

Den här funktionen kan vara användbar för att upptäcka och diagnostisera problem med Application Insights sig själv.

Som standard Application Insights Java 3.0-loggar på nivå till både filen och `INFO` `applicationinsights.log` konsolen, vilket motsvarar den här konfigurationen:

```json
{
  "selfDiagnostics": {
    "destination": "file+console",
    "level": "INFO",
    "file": {
      "path": "applicationinsights.log",
      "maxSizeMb": 5,
      "maxHistory": 1
    }
  }
}
```

`destination` kan vara något av `file` , `console` eller `file+console` .

`level` kan vara något `OFF` av , , , , , eller `ERROR` `WARN` `INFO` `DEBUG` `TRACE` .

`path` kan vara en absolut eller relativ sökväg. Relativa sökvägar matchas mot katalogen `applicationinsights-agent-3.0.3.jar` där finns.

`maxSizeMb` är den maximala storleken för loggfilen innan den återställs.

`maxHistory` är antalet överrullningsade loggfiler som bevaras (förutom den aktuella loggfilen).

Från och med version 3.0.2 kan du också ange självdiagnostik med hjälp av miljövariabeln (som sedan har företräde framför självdiagnostiknivån som anges `level` `APPLICATIONINSIGHTS_SELF_DIAGNOSTICS_LEVEL` i json-konfigurationen).

## <a name="an-example"></a>Ett exempel

Det här är bara ett exempel som visar hur en konfigurationsfil ser ut med flera komponenter.
Konfigurera specifika alternativ baserat på dina behov.

```json
{
  "connectionString": "InstrumentationKey=...",
  "role": {
    "name": "my cloud role name"
  },
  "sampling": {
    "percentage": 100
  },
  "jmxMetrics": [
  ],
  "customDimensions": {
  },
  "instrumentation": {
    "logging": {
      "level": "INFO"
    },
    "micrometer": {
      "enabled": true
    }
  },
  "proxy": {
  },
  "preview": {
    "processors": [
    ]
  },
  "selfDiagnostics": {
    "destination": "file+console",
    "level": "INFO",
    "file": {
      "path": "applicationinsights.log",
      "maxSizeMb": 5,
      "maxHistory": 1
    }
  }
}
```
