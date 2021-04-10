---
title: Åsidosättningar av sampling (för hands version) – Azure Monitor Application Insights för Java
description: Lär dig hur du konfigurerar samplings åsidosättningar i Azure Monitor Application Insights för Java.
ms.topic: conceptual
ms.date: 03/22/2021
author: trask
ms.custom: devx-track-java
ms.author: trstalna
ms.openlocfilehash: 17979bd548ca0d7b704ebdeb4d060bf35973b319
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105024155"
---
# <a name="sampling-overrides-preview---azure-monitor-application-insights-for-java"></a>Åsidosättningar av sampling (för hands version) – Azure Monitor Application Insights för Java

> [!NOTE]
> Funktionen för åsidosättning av samplingar är i för hands version, från och med 3.0.3-BETA. 2.

Med exempel på åsidosättningar kan du åsidosätta [standard samplings procenten](./java-standalone-config.md#sampling), till exempel:
 * Ange samplings procenten till 0 (eller ett litet värde) för hälso kontroller i bruset.
 * Ange samplings procenten till 0 (eller ett litet värde) för beroende anrop i bruset.
 * Ange samplings procenten till 100 för en viktig typ av begäran (t. ex. `/login` ) även om du har konfigurerat standard samplingen till något lägre.

## <a name="terminology"></a>Terminologi

Innan du lär dig mer om samplings åsidosättningar bör du förstå term *omfånget*. Ett intervall är en allmän term för:

* En inkommande begäran.
* Ett utgående beroende (till exempel ett fjärran rop till en annan tjänst).
* Ett pågående beroende (till exempel arbete som utförs av under komponenter till tjänsten).

För åsidosättningar av samplingar är dessa intervall komponenter viktiga:

* Attribut

Span-attributen representerar både standard-och anpassade egenskaper för en specifik begäran eller ett beroende.

## <a name="getting-started"></a>Komma igång

Börja genom att skapa en konfigurations fil med namnet *applicationinsights.jspå*. Spara den i samma katalog som *applicationinsights-agent- \* . jar*. Använd följande mall.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "sampling": {
    "percentage": 10
  },
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            ...
          ],
          "percentage": 0
        },
        {
          "attributes": [
            ...
          ],
          "percentage": 100
        }
      ]
    }
  }
}
```

## <a name="how-it-works"></a>Så här fungerar det

När ett intervall startas, används de attribut som finns i intervallet vid den tidpunkten för att kontrol lera om någon av samplings åsidosättningarna stämmer överens.

Om en av samplingen åsidosätter matchningen används dess samplings procent för att bestämma om du vill sampla om intervallet eller inte.

Endast den första samplings åsidosättning som matchar används.

Om inga samplings åsidosättningar matchar:

* Om detta är det första omfånget i spårningen används [standard samplings procenten](./java-standalone-config.md#sampling) .
* Om detta inte är det första omfånget i spårningen används det överordnade samplings beslutet.

> [!IMPORTANT]
> När ett beslut har fattats om att inte samla in ett intervall kommer alla efterföljande spans inte heller att samlas in, även om det finns samplings åsidosättningar som matchar det efterföljande intervallet.
> Detta är nödvändigt eftersom annars brutna spår skulle resultera i att efterföljande intervall samlas in men översätts till intervall som inte samlats in.

> [!NOTE]
> Samplings beslutet baseras på hashing av traceId (även kallat operationId) till ett tal mellan 0 och 100, och denna hash jämförs sedan med samplings procenten.
> Eftersom alla intervall i en specifik spårning kommer att ha samma traceId, kommer de att ha samma hash-värde, och därför kommer samplings beslutet att bli konsekvent över hela spårningen.

## <a name="example-suppress-collecting-telemetry-for-health-checks"></a>Exempel: förhindra insamling av telemetri för hälso kontroller

Detta förhindrar att telemetri samlas in för alla begär anden till `/health-checks` .

Detta innebär också att du kan samla in eventuella efterföljande intervall (beroenden) som normalt skulle samlas in under `/health-checks` .

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            {
              "key": "http.url",
              "value": "https?://[^/]+/health-check",
              "matchType": "regexp"
            }
          ],
          "percentage": 0
        }
      ]
    }
  }
}
```

## <a name="example-suppress-collecting-telemetry-for-a-noisy-dependency-call"></a>Exempel: förhindra insamling av telemetri för ett brus beroende anrop

Detta förhindrar att telemetri samlas in för alla `GET my-noisy-key` Redis-anrop.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            {
              "key": "db.system",
              "value": "redis",
              "matchType": "strict"
            },
            {
              "key": "db.statement",
              "value": "GET my-noisy-key",
              "matchType": "strict"
            }
          ],
          "percentage": 0
        }
      ]
    }
  }
}
```

## <a name="example-collect-100-of-telemetry-for-an-important-request-type"></a>Exempel: samla in 100% av telemetri för en viktig typ av begäran

Detta samlar in 100% av telemetri för `/login` .

Eftersom underordnade förfaller (beroenden) respekterar överordnad provtagnings beslut (saknade eventuella samplings åsidosättningar för det underordnade intervallet) samlas de också in för alla "/login"-begär Anden.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "sampling": {
    "percentage": 10
  },
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            {
              "key": "http.url",
              "value": "https?://[^/]+/login",
              "matchType": "regexp"
            }
          ],
          "percentage": 100
        }
      ]
    }
  }
}
```

## <a name="common-span-attributes"></a>Vanliga span-attribut

I det här avsnittet visas några vanliga span-attribut som kan användas för att sampla åsidosättningar.

### <a name="http-spans"></a>HTTP-intervall

| Attribut  | Typ | Description | 
|---|---|---|
| `http.method` | sträng | Metod för HTTP-begäran.|
| `http.url` | sträng | Fullständig URL för HTTP-begäran i formuläret `scheme://host[:port]/path?query[#fragment]` . Fragmentet skickas vanligt vis inte via HTTP. Men om fragmentet är känt bör det inkluderas.|
| `http.status_code` | antal | [Status kod för HTTP-svar](https://tools.ietf.org/html/rfc7231#section-6).|
| `http.flavor` | sträng | Typ av HTTP-protokoll. |
| `http.user_agent` | sträng | Värdet för [http-User-Agent-](https://tools.ietf.org/html/rfc7231#section-5.5.3) huvudet som skickas av klienten. |

### <a name="jdbc-spans"></a>JDBC-intervall

| Attribut  | Typ | Description  |
|---|---|---|
| `db.system` | sträng | Identifierare för den DBMS-produkt (Database Management System) som används. |
| `db.connection_string` | sträng | Anslutnings strängen som används för att ansluta till databasen. Vi rekommenderar att du tar bort inbäddade autentiseringsuppgifter.|
| `db.user` | sträng | Användar namn för åtkomst till databasen. |
| `db.name` | sträng | Sträng som används för att rapportera namnet på databasen som öppnas. För kommandon som växlar databasen ska den här strängen anges till mål databasen, även om kommandot Miss lyckas.|
| `db.statement` | sträng | Databas instruktion som körs.|
