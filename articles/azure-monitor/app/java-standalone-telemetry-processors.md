---
title: Telemetri-processorer (för hands version) – Azure Monitor Application Insights för Java
description: Lär dig att konfigurera telemetri-processorer i Azure Monitor Application Insights för Java.
ms.topic: conceptual
ms.date: 10/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: 35e53454e5b2c6265082bbedb4a8b60e82df7191
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101734578"
---
# <a name="telemetry-processors-preview---azure-monitor-application-insights-for-java"></a>Telemetri-processorer (för hands version) – Azure Monitor Application Insights för Java

> [!NOTE]
> Funktionen telemetri-processorer är i för hands version.

Java 3,0-agenten för Application Insights kan bearbeta telemetridata innan data exporteras.

Här är några användnings fall för telemetri-processorer:
 * Skapa känsliga data.
 * Lägg villkorligt till anpassade dimensioner.
 * Uppdatera intervall namnet, som används för att aggregera liknande telemetri i Azure Portal.
 * Släpp span-attribut för att kontrol lera inmatnings kostnader.

## <a name="terminology"></a>Terminologi

Innan du lär dig om telemetri-processorer bör du förstå term *omfånget*. Ett intervall är en allmän term för:

* En inkommande begäran.
* Ett utgående beroende (till exempel ett fjärran rop till en annan tjänst).
* Ett pågående beroende (till exempel arbete som utförs av under komponenter till tjänsten).

För telemetri-processorer är dessa intervall komponenter viktiga:

* Namn
* Attribut

Intervall namnet är den primära visningen för begär Anden och beroenden i Azure Portal. Span-attribut representerar både standard-och anpassade egenskaper för en specifik begäran eller beroende.

## <a name="telemetry-processor-types"></a>Typer av telemetri-processorer

De två typerna av telemetri-processorer är för närvarande attribut processorer och intervall processorer.

En attribut processor kan infoga, uppdatera, ta bort eller hash-attribut.
Det kan också använda ett reguljärt uttryck för att extrahera ett eller flera nya attribut från ett befintligt attribut.

En intervall processor kan uppdatera namnet på telemetri.
Det kan också använda ett reguljärt uttryck för att extrahera ett eller flera nya attribut från intervall namnet.

> [!NOTE]
> Telemetri-processorer bearbetar för närvarande endast attribut av typen sträng. De bearbetar inte attribut av typen Boolean eller Number.

## <a name="getting-started"></a>Komma igång

Börja genom att skapa en konfigurations fil med namnet *applicationinsights.jspå*. Spara den i samma katalog som *applicationinsights-agent- \* . jar*. Använd följande mall.

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

## <a name="include-criteria-and-exclude-criteria"></a>Inkludera villkor och exkludera villkor

Både attribut processorer och intervall processorer stöder valfria `include` och `exclude` kriterier.
En processor används endast för att omfatta intervall som matchar dess `include` kriterier (om den finns) _och_ inte matchar dess `exclude` kriterier (om den är angiven).

Om du vill konfigurera det här alternativet, under `include` eller `exclude` (eller båda), anger du minst ett `matchType` och antingen `spanNames` eller `attributes` .
Med-exkludering-konfigurationen tillåts fler än ett angivet villkor.
Alla angivna villkor måste utvärderas till sant för att resultera i en matchning. 

* **Obligatoriskt fält**: `matchType` styr hur objekt i `spanNames` matriser och `attributes` matriser tolkas. Möjliga värden är `regexp` och `strict` . 

* **Valfria fält**: 
    * `spanNames` måste matcha minst ett av objekten. 
    * `attributes` anger listan över attribut som ska matchas. Alla dessa attribut måste matcha exakt för att resultera i en matchning.
    
> [!NOTE]
> Om både `include` och `exclude` anges, `include` kontrol leras egenskaperna innan `exclude` egenskaperna kontrol leras.

### <a name="sample-usage"></a>Exempel användning

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
Mer information finns i [exempel på telemetri processor](./java-standalone-telemetry-processors-examples.md).

## <a name="attribute-processor"></a>Attribut processor

Attributets processor ändrar attributen för ett intervall. Det kan ge stöd för möjligheten att ta med eller undanta intervall. Det tar en lista med åtgärder som utförs i den ordning som konfigurations filen anger. Processorn stöder följande åtgärder:

- `insert`
- `update`
- `delete`
- `hash`
- `extract`
### `insert`

`insert`Åtgärden infogar ett nytt attribut i intervall där nyckeln inte redan finns.   

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
`insert`Åtgärden kräver följande inställningar:
* `key`
* Antingen `value` eller `fromAttribute`
* `action`: `insert`

### `update`

`update`Åtgärden uppdaterar ett attribut i intervall där nyckeln redan finns.

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
`update`Åtgärden kräver följande inställningar:
* `key`
* Antingen `value` eller `fromAttribute`
* `action`: `update`


### `delete` 

`delete`Åtgärden tar bort ett attribut från ett intervall.

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
`delete`Åtgärden kräver följande inställningar:
* `key`
* `action`: `delete`

### `hash`

`hash`Hash-värdet (SHA1) för ett befintligt attributvärde.

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
`hash`Åtgärden kräver följande inställningar:
* `key`
* `action`: `hash`

### `extract`

> [!NOTE]
> `extract`Funktionen är endast tillgänglig i version 3.0.2 och senare.

`extract`Åtgärden extraherar värden med hjälp av en regel för reguljära uttryck från den inmatade nyckeln till mål nycklar som regeln anger. Om det redan finns en mål nyckel, åsidosätts den. Den här åtgärden fungerar som inställningen för [span-processorer](#extract-attributes-from-the-span-name) `toAttributes` , där det befintliga attributet är källan.

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
`extract`Åtgärden kräver följande inställningar:
* `key`
* `pattern`
* `action`: `extract`

Mer information finns i [exempel på telemetri processor](./java-standalone-telemetry-processors-examples.md).

## <a name="span-processor"></a>Spänn processor

Spänn processorn ändrar antingen intervall namnet eller attributen för ett intervall baserat på intervall namnet. Det kan ge stöd för möjligheten att ta med eller undanta intervall.

### <a name="name-a-span"></a>Namnge ett intervall

`name`Avsnittet kräver `fromAttributes` inställningen. Värdena från dessa attribut används för att skapa ett nytt namn, sammanfogat i den ordning som konfigurationen anger. Processorn kommer bara att ändra intervall namnet om alla dessa attribut finns i intervallet.

`separator`Inställningen är valfri. Den här inställningen är en sträng. Den har angetts för att dela värden.
> [!NOTE]
> Om namnbytet är beroende av attributens processor för att ändra attribut, se till att intervallet processor har angetts efter attributets processor i pipeline-specifikationen.

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

### <a name="extract-attributes-from-the-span-name"></a>Extrahera attribut från intervall namnet

I `toAttributes` avsnittet visas de reguljära uttryck som matchar intervall namnet mot. Den extraherar attribut baserat på under uttryck.

`rules`Inställningen är obligatorisk. Den här inställningen visar de regler som används för att extrahera attributvärden från intervall namnet. 

Värdena i intervall namnet ersätts av extraherade attributnamn. Varje regel i listan är en uttryck för reguljära uttryck (regex). 

Så här ersätts värden av extraherade attributnamn:

1. Intervall namnet kontrol leras mot regex. 
1. Om regex matchar extraheras alla namngivna under uttryck i regex som attribut. 
1. De extraherade attributen läggs till i intervallet. 
1. Varje underexpresss namn blir ett attributnamn. 
1. Den matchade delen av under uttryck blir attributvärdet. 
1. Den matchade delen i intervall namnet ersätts av namnet på det extraherade attributet. Om attributen redan finns i intervallet skrivs de över. 
 
Den här processen upprepas för alla regler i den ordning som de har angetts. Varje efterföljande regel fungerar på det intervall namn som är resultatet av föregående regel.

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

## <a name="common-span-attributes"></a>Vanliga span-attribut

I det här avsnittet listas några vanliga span-attribut som telemetri-processorer kan använda.

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
