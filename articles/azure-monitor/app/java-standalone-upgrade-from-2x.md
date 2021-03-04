---
title: Uppgradera från 2. x-Azure Monitor Application Insights Java
description: Uppgradera från Azure Monitor Application Insights Java 2. x
ms.topic: conceptual
ms.date: 11/25/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 6e1c7e15ff77fd75ff2fb70a6741ea2dd9a4cab8
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102040251"
---
# <a name="upgrading-from-application-insights-java-2x-sdk"></a>Uppgradera från Application Insights Java 2. x SDK

Om du redan använder Application Insights Java 2. x SDK i programmet behöver du inte ta bort den.
Java 3,0-agenten identifierar den och samlar in och korrelerar eventuella anpassade telemetri som du skickar via SDK: n för 2. x, samtidigt som du undertrycker automatisk insamling som utförs av 2. x SDK för att förhindra dubblett av telemetri.

Om du använde Application Insights 2. x-agenten måste du ta bort det `-javaagent:` JVM-arg som pekade på 2. x-agenten.

Resten av det här dokumentet beskriver begränsningar och ändringar som kan uppstå när du uppgraderar från 2. x till 3,0, samt vissa lösningar som du kan ha nytta av.

## <a name="telemetryinitializers-and-telemetryprocessors"></a>TelemetryInitializers och TelemetryProcessors

SDK-TelemetryInitializers och TelemetryProcessors för 2. x kommer inte att köras när du använder 3,0-agenten.
Många av de användnings fall som tidigare krävdes kan lösas i 3,0 genom att konfigurera [anpassade dimensioner](./java-standalone-config.md#custom-dimensions) eller konfigurera [telemetri-processorer](./java-standalone-telemetry-processors.md).

## <a name="multiple-applications-in-a-single-jvm"></a>Flera program i en enda JVM

För närvarande stöder 3,0 bara en enda [anslutnings sträng och roll namn](./java-standalone-config.md#connection-string-and-role-name) per process som körs. I synnerhet kan du inte ha flera Tomcat-webbappar i samma Tomcat-distribution som använder olika anslutnings strängar eller olika roll namn ännu.

## <a name="operation-names"></a>Åtgärds namn

Åtgärds namn i 3,0 har ändrats till allmänt sett en bättre sammanställd vy i Application Insightss portalen U/X.

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-3-0.png" alt-text="Åtgärds namn i 3,0":::

För vissa program kan du dock fortfarande föredra den sammanställda vyn i U/X som tillhandahölls av de tidigare åtgärds namnen, och i så fall kan du använda funktionen [telemetri-processorer](./java-standalone-telemetry-processors.md) (för hands version) i 3,0 för att replikera det tidigare beteendet.

### <a name="prefix-the-operation-name-with-the-http-method-get-post-etc"></a>Prefixet åtgärds namn med HTTP-metoden ( `GET` , `POST` osv.)

I 2. x SDK har åtgärds namnen prefixet av http-metoden ( `GET` , `POST` osv.), t. ex.

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-prefixed-by-http-method.png" alt-text="Förkorrigerat åtgärds namn med http-metod":::

I kodfragmentet nedan konfigureras 3 telemetri-processorer som kombineras för att replikera det tidigare beteendet.
Telemetri-processorerna utför följande åtgärder (i ordning):

1. Den första telemetriprocessor är en intervall processor (har typ `span` ), vilket innebär att den gäller för `requests` och `dependencies` .

   Det matchar alla intervall som har ett attribut med namnet `http.method` och har ett intervall namn som börjar med `/` .

   Sedan extraheras detta intervall namn till ett attribut med namnet `tempName` .

2. Den andra telemetri-processorn är också en intervall processor.

   Den matchar alla intervall som har ett attribut med namnet `tempName` .

   Sedan uppdateras intervall namnet genom att sammanfoga de två attributen `http.method` och `tempName` avgränsade med blank steg.

3. Den senaste telemetri-processorn är en attribut processor (har typ `attribute` ), vilket innebär att den gäller all telemetri som har attribut (för närvarande `requests` `dependencies` och `traces` ).

   Den matchar all telemetri som har ett attribut med namnet `tempName` .

   Sedan tas attributet med namnet `tempName` , så att det inte rapporteras som en anpassad dimension.

```
{
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "regexp",
          "attributes": [
            { "key": "http.method", "value": "" }
          ],
          "spanNames": [ "^/" ]
        },
        "name": {
          "toAttributes": {
            "rules": [ "^(?<tempName>.*)$" ]
          }
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempName" }
          ]
        },
        "name": {
          "fromAttributes": [ "http.method", "tempName" ],
          "separator": " "
        }
      },
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempName" }
          ]
        },
        "actions": [
          { "key": "tempName", "action": "delete" }
        ]
      }
    ]
  }
}
```

### <a name="set-the-operation-name-to-the-full-path"></a>Ange den fullständiga sökvägen till åtgärds namnet

I vissa fall, i ett 2. x SDK, i vissa fall, innehåller åtgärds namnen den fullständiga sökvägen, t. ex.

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-with-full-path.png" alt-text="Åtgärds namn med fullständig sökväg":::

I kodfragmentet nedan konfigureras 4 telemetri-processorer som kombineras för att replikera det tidigare beteendet.
Telemetri-processorerna utför följande åtgärder (i ordning):

1. Den första telemetriprocessor är en intervall processor (har typ `span` ), vilket innebär att den gäller för `requests` och `dependencies` .

   Den matchar alla intervall som har ett attribut med namnet `http.url` .

   Sedan uppdateras intervall namnet med `http.url` attributvärdet.

   Detta kan vara slutet på det, förutom att det `http.url` ser ut ungefär som `http://host:port/path` du bara vill ha `/path` .

2. Den andra telemetri-processorn är också en intervall processor.

   Det matchar alla intervall som har ett attribut med namnet `http.url` (med andra ord, alla intervall som den första processorn matchade).

   Sedan extraheras Sök vägs delen av intervall namnet i ett attribut med namnet `tempName` .

3. Den tredje telemetri processorn är också en intervall processor.

   Den matchar alla intervall som har ett attribut med namnet `tempPath` .

   Sedan uppdateras intervall namnet från attributet `tempPath` .

4. Den senaste telemetri-processorn är en attribut processor (har typ `attribute` ), vilket innebär att den gäller all telemetri som har attribut (för närvarande `requests` `dependencies` och `traces` ).

   Den matchar all telemetri som har ett attribut med namnet `tempPath` .

   Sedan tas attributet med namnet `tempPath` , så att det inte rapporteras som en anpassad dimension.

```
{
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "http.url" }
          ]
        },
        "name": {
          "fromAttributes": [ "http.url" ]
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "http.url" }
          ]
        },
        "name": {
          "toAttributes": {
            "rules": [ "https?://[^/]+(?<tempPath>/[^?]*)" ]
          }
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempPath" }
          ]
        },
        "name": {
          "fromAttributes": [ "tempPath" ]
        }
      },
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempPath" }
          ]
        },
        "actions": [
          { "key": "tempPath", "action": "delete" }
        ]
      }
    ]
  }
}
```

## <a name="dependency-names"></a>Beroende namn

Beroende namn i 3,0 har också ändrats, och ger vanligt vis en bättre sammanställd vy i Application Insightss portalen U/X.

För vissa program kan du fortfarande föredra den sammanställda vyn i U/X som angavs av de tidigare beroende namnen, i så fall kan du använda liknande metoder som ovan för att replikera det tidigare beteendet.

## <a name="operation-name-on-dependencies"></a>Åtgärds namn på beroenden

Tidigare i 2. x SDK angavs åtgärds namnet från telemetri för begäran även på beroende telemetri.
Application Insights Java 3,0 fyller inte längre i åtgärds namn i beroende telemetri.
Om du vill se åtgärds namnet för begäran som är överordnad för beroende telemetri, kan du skriva en loggare (Kusto) fråga för att ansluta från beroende tabellen till Request-tabellen, t. ex.

```
let start = datetime('...');
let end = datetime('...');
dependencies
| where timestamp between (start .. end)
| project timestamp, type, name, operation_Id
| join (requests
    | where timestamp between (start .. end)
    | project operation_Name, operation_Id)
    on $left.operation_Id == $right.operation_Id
| summarize count() by operation_Name, type, name
```

## <a name="2x-sdk-logging-appenders"></a>2. x SDK-loggning tillägg

3,0-agenten [automatiskt samlar in loggning](./java-standalone-config.md#auto-collected-logging) utan att behöva konfigurera några logg tillägg.
Om du använder 2. x SDK-loggnings tillägg kan de tas bort, eftersom de ignoreras av 3,0-agenten ändå.

## <a name="2x-sdk-spring-boot-starter"></a>2. x SDK våren start starter

Det finns inget 3,0 fjäder start starter.
Installationen och konfigurationen av 3,0-agenten följer samma [enkla steg](./java-in-process-agent.md#quickstart) oavsett om du använder våren boot eller inte.

När du uppgraderar från 2. x SDK våren start starter, Observera att moln roll namnet inte längre är standard för `spring.application.name` .
Se [konfigurations dokumenten för 3,0](./java-standalone-config.md#cloud-role-name) för att ange moln roll namnet i 3,0 via JSON-konfiguration eller miljö variabel.
