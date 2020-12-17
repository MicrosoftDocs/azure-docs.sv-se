---
title: Konfigurations alternativ – Azure Monitor Application Insights för Java
description: Så här konfigurerar du Azure Monitor Application Insights för Java
ms.topic: conceptual
ms.date: 11/04/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 7c5cac0b52fb0a224595ca7e328b551fd48d6661
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97614242"
---
# <a name="configuration-options---azure-monitor-application-insights-for-java"></a>Konfigurations alternativ – Azure Monitor Application Insights för Java

> [!WARNING]
> **Om du uppgraderar från 3,0 för hands version**
>
> Granska alla konfigurations alternativ nedan, eftersom JSON-strukturen har ändrats helt och hållet, förutom själva fil namnet som var i gemener.

## <a name="connection-string-and-role-name"></a>Anslutnings sträng och rollnamn

Anslutnings strängen och roll namnet är de vanligaste inställningarna som krävs för att komma igång:

```json
{
  "connectionString": "InstrumentationKey=...",
  "role": {
    "name": "my cloud role name"
  }
}
```

Anslutnings strängen krävs och roll namnet är viktigt när du skickar data från olika program till samma Application Insights-resurs.

Du hittar mer information och ytterligare konfigurations alternativ nedan.

## <a name="configuration-file-path"></a>Sökväg till konfigurations fil

Application Insights Java 3,0 förväntar sig som standard konfigurations filen som ska namnges `applicationinsights.json` och placeras i samma katalog som `applicationinsights-agent-3.0.0.jar` .

Du kan ange en egen sökväg för konfigurations filen med antingen

* `APPLICATIONINSIGHTS_CONFIGURATION_FILE` miljö variabel eller
* `applicationinsights.configuration.file` Java system egenskap

Om du anger en relativ sökväg kommer den att matchas i förhållande till den katalog där `applicationinsights-agent-3.0.0.jar` finns.

## <a name="connection-string"></a>Anslutningssträng

Anslutnings sträng krävs. Du kan hitta din anslutnings sträng i Application Insights-resursen:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights anslutnings sträng":::


```json
{
  "connectionString": "InstrumentationKey=..."
}
```

Du kan också ange anslutnings strängen med hjälp av miljövariabeln `APPLICATIONINSIGHTS_CONNECTION_STRING` .

Om du inte anger anslutnings strängen inaktive ras Java-agenten.

## <a name="cloud-role-name"></a>Namn på moln roll

Namnet på moln rollen används för att märka komponenten på program kartan.

Om du vill ange namnet på moln rollen:

```json
{
  "role": {   
    "name": "my cloud role name"
  }
}
```

Om du inte anger namnet på moln rollen används Application Insights resursens namn för att märka komponenten på program kartan.

Du kan också ange namnet på moln rollen med hjälp av miljövariabeln `APPLICATIONINSIGHTS_ROLE_NAME` .

## <a name="cloud-role-instance"></a>Moln roll instans

Moln roll instansen har som standard dator namnet.

Om du vill ställa in en annan moln roll instans i stället för namnet på datorn:

```json
{
  "role": {
    "name": "my cloud role name",
    "instance": "my cloud role instance"
  }
}
```

Du kan också ställa in moln Rolls instansen med hjälp av miljövariabeln `APPLICATIONINSIGHTS_ROLE_INSTANCE` .

## <a name="sampling"></a>Samling

Sampling är användbart om du behöver minska kostnaderna.
Sampling utförs som en funktion i åtgärds-ID (även kallat spårnings-ID), så att samma åtgärds-ID alltid kommer att resultera i samma samplings beslut. På så sätt kan du inte hämta delar av en distribuerad transaktion, medan andra delar av det samplas ut.

Om du till exempel ställer in sampling till 10% visas bara 10% av dina transaktioner, men var och en av de 10% har fullständig transaktions information från slut punkt till slut punkt.

Här är ett exempel på hur du ställer in samplingen för att samla in cirka **1/3 av alla transaktioner** – se till att du ställer in den samplings frekvens som är korrekt för ditt användnings fall:

```json
{
  "sampling": {
    "percentage": 33.333
  }
}
```

Du kan också ange samplings procenten med hjälp av miljövariabeln `APPLICATIONINSIGHTS_SAMPLING_PERCENTAGE` .

> [!NOTE]
> För samplings procenten väljer du en procents ATS som ligger nära 100/N där N är ett heltal. För närvarande stöder sampling inte andra värden.

## <a name="jmx-metrics"></a>JMX mått

Om du vill samla in ytterligare JMX-mått:

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

`name` är mått namnet som tilldelas till det här JMX-måttet (kan vara vad som helst).

`objectName` är [objekt namnet](https://docs.oracle.com/javase/8/docs/api/javax/management/ObjectName.html) för den JMX-MBean som du vill samla in.

`attribute` är attributnamnet i den JMX-MBean som du vill samla in.

Numeriska och booleska JMX-mått stöds. Booleska JMX-mått mappas till `0` för falskt och `1` true.

[//]: # "Obs!: dokumentera APPLICATIONINSIGHTS_JMX_METRICS här"
[//]: # "JSON Embedded i kuvert var är rörigt och bör bara dokumenteras för kod kopplings scenario"

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

`${...}` kan användas för att läsa värdet från angiven miljö variabel vid start.

> [!NOTE]
> Från och med version 3.0.1 – BETA, om du lägger till en anpassad dimension med namnet `service.version` , kommer värdet att lagras i `application_Version` kolumnen i tabellen Application Insights loggar i stället för som en anpassad dimension.

## <a name="telemetry-processors-preview"></a>Telemetri-processorer (för hands version)

Den här funktionen är en förhandsversion.

Det gör att du kan konfigurera regler som ska tillämpas på begäran, beroende och trace-telemetri, till exempel:
 * Maskera känsliga data
 * Lägg till anpassade dimensioner villkorligt
 * Uppdatera telemetri-namnet som används för agg regering och visning

Mer information finns i dokumentationen om [telemetri-processorn](./java-standalone-telemetry-processors.md) .

## <a name="auto-collected-logging"></a>Automatisk insamlad loggning

Log4j, logback och Java. util. logging är automatiskt instrumenterade och loggning som utförs via dessa loggnings ramverk samlas automatiskt in.

Loggning registreras endast om det stämmer överens med det konfigurerade tröskelvärdet för loggnings ramverk, och den andra uppfyller även det Application Insights konfigurerade tröskelvärdet.

Standard Application Insights tröskelvärdet är `INFO` . Om du vill ändra den här nivån:

```json
{
  "instrumentation": {
    "logging": {
      "level": "WARN"
    }
  }
}
```

Du kan också ange tröskelvärdet med hjälp av miljövariabeln `APPLICATIONINSIGHTS_INSTRUMENTATION_LOGGING_LEVEL` .

Dessa är giltiga `level` värden som du kan ange i `applicationinsights.json` filen och hur de motsvarar loggnings nivåer i olika loggnings ramverk:

| nivå             | Log4j  | Logback | Jul     |
|-------------------|--------|---------|---------|
| OFF               | OFF    | OFF     | OFF     |
| DÖDS             | DÖDS  | ERROR   | SEVERE  |
| FEL (eller allvarligt) | ERROR  | ERROR   | SEVERE  |
| Varna (eller varning) | Varna   | Varna    | WARNING |
| INFO              | INFO   | INFO    | INFO    |
| CONFIG            | FELSÖK  | FELSÖK   | CONFIG  |
| FELSÖKA (eller fint)   | FELSÖK  | FELSÖK   | FINE    |
| FINER             | FELSÖK  | FELSÖK   | FINER   |
| TRACE (eller FINEST) | Rita  | Rita   | FINEST  |
| ALL               | ALL    | ALL     | ALL     |

## <a name="auto-collected-micrometer-metrics-including-spring-boot-actuator-metrics"></a>Automatiskt insamlade micrometer-mått (inklusive värden för våren Boot-motstånd)

Om ditt program använder [micrometer](https://micrometer.io), samlas mått som skickas till micrometer globala registret automatiskt.

Om ditt program använder [våren Boot-motstånd](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html), samlas även statistik som kon figurer ATS av våren Boot-motstånd automatiskt in.

Så här inaktiverar du automatisk insamling av micrometer-mått (inklusive värden för våren Boot-motstånd):

> [!NOTE]
> Anpassade mått faktureras separat och kan generera ytterligare kostnader. Se till att se den detaljerade [pris informationen](https://azure.microsoft.com/pricing/details/monitor/). Om du vill inaktivera Mät värdena för micrometer och våren lägger du till nedanstående konfiguration i konfigurations filen.

```json
{
  "instrumentation": {
    "micrometer": {
      "enabled": false
    }
  }
}
```

## <a name="heartbeat"></a>Pulsslag

Som standard skickar Application Insights Java 3,0 ett pulsslags mått var 15: e minut. Om du använder pulsslags måttet för att utlösa aviseringar kan du öka frekvensen för detta pulsslag:

```json
{
  "heartbeat": {
    "intervalSeconds": 60
  }
}
```

> [!NOTE]
> Du kan inte minska frekvensen på pulsslaget, eftersom pulsslags data också används för att spåra Application Insights användning.

## <a name="http-proxy"></a>HTTP-proxy

Om ditt program ligger bakom en brand vägg och inte kan ansluta direkt till Application Insights (se [IP-adresser som används av Application Insights](./ip-addresses.md)) kan du konfigurera Application Insights Java 3,0 för att använda en http-proxy:

```json
{
  "proxy": {
    "host": "myproxy",
    "port": 8080
  }
}
```

[//]: # "Observera att inte annonsera stöd för opentelemetri förrän vi har stöd för 0.10.0, som har enorma ändringar från 0.9.0"

[//]: # "# # Stöd för opentelemetri API pre-1,0-versioner"

[//]: # "Stöd för pre-1,0-versioner av opentelemetri-API är deltagande, eftersom API för opentelemetri inte är stabilt än"
[//]: # "så varje version av agenten har endast stöd för en bestämd pre-1,0-version av opentelemetri-API"
[//]: # "(den här begränsningen gäller inte när opentelemetri API 1,0 har släppts)."

[//]: # "JSON"
[//]: # "{"
[//]: # "  \"för hands version \" : {"
[//]: # "    \"openTelemetryApiSupport \" : sant"
[//]: # "  }"
[//]: # "}"
[//]: # "```"

## <a name="self-diagnostics"></a>Själv diagnostik

"Self-Diagnostics" syftar på intern loggning från Application Insights Java 3,0.

Den här funktionen kan vara till hjälp för att upptäcka och diagnostisera problem med Application Insights.

Som standard loggar Application Insights Java 3,0 på nivå `INFO` till både filen `applicationinsights.log` och konsolen, som motsvarar den här konfigurationen:

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

`destination` kan vara en av `file` `console` eller `file+console` .

`level` kan vara en av,,,, `OFF` `ERROR` `WARN` `INFO` `DEBUG` eller `TRACE` .

`path` kan vara en absolut eller relativ sökväg. Relativa sökvägar matchas mot den katalog där `applicationinsights-agent-3.0.0.jar` finns.

`maxSizeMb` är logg filens Max storlek innan den slås samman.

`maxHistory` är antalet upplyft över loggfiler som bevaras (utöver den aktuella logg filen).

## <a name="an-example"></a>Ett exempel

Detta är bara ett exempel för att visa hur en konfigurations fil ser ut som med flera komponenter.
Konfigurera olika alternativ utifrån dina behov.

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