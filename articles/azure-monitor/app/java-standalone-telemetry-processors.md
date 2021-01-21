---
title: Telemetri-processorer (för hands version) – Azure Monitor Application Insights för Java
description: Så här konfigurerar du telemetri-processorer i Azure Monitor Application Insights för Java
ms.topic: conceptual
ms.date: 10/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: c0745dd4069c64292fbcaef666d843ae2d25f7b3
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632588"
---
# <a name="telemetry-processors-preview---azure-monitor-application-insights-for-java"></a>Telemetri-processorer (för hands version) – Azure Monitor Application Insights för Java

> [!NOTE]
> Den här funktionen är fortfarande under för hands version.

Java 3,0-agenten för Application Insights har nu funktioner för att bearbeta telemetridata innan data exporteras.

Följande är några användnings fall av telemetri-processorer:
 * Maskera känsliga data
 * Lägg till anpassade dimensioner villkorligt
 * Uppdatera namnet som används för agg regering och Visa i Azure Portal
 * Släpp span-attribut för att kontrol lera inmatnings kostnaden

## <a name="terminology"></a>Terminologi

Innan vi hoppar till telemetri-processorer är det viktigt att förstå vad termen sträcker syftar på.

Ett intervall är en allmän term för någon av dessa tre saker:

* En inkommande begäran
* Ett utgående beroende (t. ex. ett fjärran rop till en annan tjänst)
* Ett pågående beroende (t. ex. arbete som utförs av underordnade komponenter i tjänsten)

För telemetri-processorer är de viktiga komponenterna i ett intervall:

* Name
* Attribut

Intervall namnet är den primära visning som används för förfrågningar och beroenden i Azure Portal.

Span-attributen representerar både standard-och anpassade egenskaper för en specifik begäran eller ett beroende.

## <a name="telemetry-processor-types"></a>Typer av telemetri-processorer

Det finns för närvarande två typer av telemetri-processorer.

#### <a name="attribute-processor"></a>Attribut processor

En attribut processor kan infoga, uppdatera, ta bort eller hash-attribut.
Det kan också extrahera (via ett reguljärt uttryck) ett eller flera nya attribut från ett befintligt attribut.

#### <a name="span-processor"></a>Spänn processor

En intervall processor kan uppdatera namnet på telemetri.
Det kan också extrahera (via ett reguljärt uttryck) ett eller flera nya attribut från intervall namnet.

> [!NOTE]
> Observera att för närvarande enbart telemetri processorer bearbetar attribut av typen sträng och inte bearbetar attribut av typen Boolean eller Number.

## <a name="getting-started"></a>Komma igång

Skapa en konfigurations fil med namnet `applicationinsights.json` och placera den i samma katalog som `applicationinsights-agent-*.jar` , med följande mall.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        ...
      },
      {
        "type": "attribute",
        ...
      },
      {
        "type": "span",
        ...
      }
    ]
  }
}
```

## <a name="includeexclude-criteria"></a>Inkludera/exkludera villkor

Både attribut processorer och intervall processorer stöder valfria `include` och `exclude` kriterier.
En processor tillämpas bara på de intervall som matchar dess `include` kriterier (om de finns) _och_ inte matchar dess `exclude` kriterier (om det finns).

För att konfigurera det här alternativet under `include` och/eller `exclude` minst ett `matchType` och ett av `spanNames` eller `attributes` krävs.
Konfigurationen inkludera/exkludera stöds för att ha mer än ett angivet villkor.
Alla angivna villkor måste utvärderas till sant för att en matchning ska inträffa. 

**Obligatoriskt fält**: 
* `matchType` styr hur objekt i `spanNames` och `attributes` matriser tolkas. Möjliga värden är `regexp` eller `strict`. 

**Valfria fält**: 
* `spanNames` måste matcha minst ett av objekten. 
* `attributes` anger listan över attribut som ska matchas mot. Alla dessa attribut måste matcha exakt för att en matchning ska ske.

> [!NOTE]
> Om både `include` och `exclude` anges, `include` kontrol leras egenskaperna innan `exclude` egenskaperna.

#### <a name="sample-usage"></a>Exempelanvändning

```json

"processors": [
  {
    "type": "attribute",
    "include": {
      "matchType": "strict",
      "spanNames": [
        "spanA",
        "spanB"
      ]
    },
    "exclude": {
      "matchType": "strict",
      "attributes": [
        {
          "key": "redact_trace",
          "value": "false"
        }
      ]
    },
    "actions": [
      {
        "key": "credit_card",
        "action": "delete"
      },
      {
        "key": "duplicate_key",
        "action": "delete"
      }
    ]
  }
]
```
Mer förståelse finns i exempel dokumentationen för [telemetri-processorn](./java-standalone-telemetry-processors-examples.md) .

## <a name="attribute-processor"></a>Attribut processor

Attributens processor ändrar attributen för ett intervall. Det kan också användas för att inkludera/exkludera intervall. Den tar en lista med åtgärder som utförs i den ordning som anges i konfigurations filen. De åtgärder som stöds är:

### `insert`

Infogar ett nytt attribut i intervall där nyckeln inte redan finns.   

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "value": "value1",
        "action": "insert"
      }
    ]
  }
]
```
För `insert` åtgärden krävs följande:
  * `key`
  * en `value` eller `fromAttribute`
  * `action`:`insert`

### `update`

Uppdaterar ett attribut i intervall där nyckeln finns

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "value": "newValue",
        "action": "update"
      }
    ]
  }
]
```
För `update` åtgärden krävs följande:
  * `key`
  * en `value` eller `fromAttribute`
  * `action`:`update`


### `delete` 

Tar bort ett attribut från ett intervall

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "action": "delete"
      }
    ]
  }
]
```
För `delete` åtgärden krävs följande:
  * `key`
  * `action`: `delete`

### `hash`

Hash-värden (SHA1) ett befintligt attributvärde

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "action": "hash"
      }
    ]
  }
]
```
För `hash` åtgärden krävs följande:
* `key`
* `action` : `hash`

### `extract`

> [!NOTE]
> Den här funktionen är endast i 3.0.2 och senare

Extraherar värden med en regel för reguljära uttryck från den inmatade nyckeln till mål nycklar som anges i regeln. Om det redan finns en mål nyckel kommer den att åsidosättas. Den fungerar på samma sätt som inställningen för [span-processorn](#extract-attributes-from-span-name) `toAttributes` med det befintliga attributet som källa.

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "pattern": "<regular pattern with named matchers>",
        "action": "extract"
      }
    ]
  }
]
```
För `extract` åtgärden krävs följande:
* `key`
* `pattern`
* `action` : `extract`

Mer förståelse finns i exempel dokumentationen för [telemetri-processorn](./java-standalone-telemetry-processors-examples.md) .

## <a name="span-processor"></a>Spänn processor

Spänn processorn ändrar antingen intervall namnet eller attributen för ett intervall baserat på intervall namnet. Det kan också användas för att inkludera/exkludera intervall.

### <a name="name-a-span"></a>Namnge ett intervall

Följande inställning krävs som en del av avsnittet Name:

* `fromAttributes`: Attributvärdet för nycklarna används för att skapa ett nytt namn i den ordning som anges i konfigurationen. Alla attributändringar måste anges i intervallet för att processorn ska kunna byta namn på den.

Du kan konfigurera följande inställningar:

* `separator`: En sträng som anges kommer att användas för att dela värden
> [!NOTE]
> Om namnbytet är beroende av attribut som ändras av attributets processor, se till att intervallet processor anges efter attributets processor i pipeline-specifikationen.

```json
"processors": [
  {
    "type": "span",
    "name": {
      "fromAttributes": [
        "attributeKey1",
        "attributeKey2",
      ],
      "separator": "::"
    }
  }
] 
```

### <a name="extract-attributes-from-span-name"></a>Extrahera attribut från intervall namn

Använder en lista över reguljära uttryck för att matcha intervall namn mot och extrahera attribut från det baserat på under uttryck. Måste anges under `toAttributes` avsnittet.

Följande inställningar krävs:

`rules` : En lista med regler för att extrahera attributvärden från intervall namn. Värdena i intervall namnet ersätts av extraherade attributnamn. Varje regel i listan är regex-mönster sträng. Intervall namnet kontrol leras mot regex. Om regex matchar extraheras alla namngivna under uttryck i regex som attribut och läggs till i intervallet. Varje under uttrycks namn blir ett attributnamn och en matchad del av under uttryck blir attributvärdet. Den matchade delen i intervall namnet ersätts av ett extraherat attributnamn. Om attributen redan finns i intervallet kommer de att skrivas över. Processen upprepas för alla regler i den ordning som de anges. Varje efterföljande regel fungerar på det intervall namn som är resultatet när föregående regel har bearbetats.

```json

"processors": [
  {
    "type": "span",
    "name": {
      "toAttributes": {
        "rules": [
          "rule1",
          "rule2",
          "rule3"
        ]
      }
    }
  }
]

```

## <a name="list-of-attributes"></a>Lista över attribut

Nedan visas en lista över några vanliga span-attribut som kan användas i telemetri-processorerna.

### <a name="http-spans"></a>HTTP-intervall

| Attribut  | Typ | Description | 
|---|---|---|
| `http.method` | sträng | Metod för HTTP-begäran.|
| `http.url` | sträng | Fullständig URL för HTTP-begäran i formuläret `scheme://host[:port]/path?query[#fragment]` . Vanligt vis skickas inte fragment över HTTP, men om det är känt bör det tas med.|
| `http.status_code` | antal | [Status kod för HTTP-svar](https://tools.ietf.org/html/rfc7231#section-6).|
| `http.flavor` | sträng | Typ av HTTP-protokoll som används |
| `http.user_agent` | sträng | Värdet för [http-User-Agent-](https://tools.ietf.org/html/rfc7231#section-5.5.3) huvudet som skickas av klienten. |

### <a name="jdbc-spans"></a>JDBC-intervall

| Attribut  | Typ | Description  |
|---|---|---|
| `db.system` | sträng | En identifierare för den DBMS-produkt (Database Management System) som används. |
| `db.connection_string` | sträng | Anslutnings strängen som används för att ansluta till databasen. Vi rekommenderar att du tar bort inbäddade autentiseringsuppgifter.|
| `db.user` | sträng | Användar namn för åtkomst till databasen. |
| `db.name` | sträng | Det här attributet används för att rapportera namnet på databasen som öppnas. För kommandon som växlar databasen ska detta ställas in på mål databasen (även om kommandot Miss lyckas).|
| `db.statement` | sträng | Databas instruktionen som körs.|
