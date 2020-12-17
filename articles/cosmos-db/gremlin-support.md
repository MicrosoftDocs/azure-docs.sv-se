---
title: Azure Cosmos DB Gremlin-stöd och kompatibilitet med TinkerPop-funktioner
description: Läs mer om Gremlin-språket från Apache TinkerPop. Lär dig mer om vilka funktioner och steg som är tillgängliga i Azure Cosmos DB och TinkerPop för diagram motorn.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 11/11/2020
ms.author: sngun
ms.openlocfilehash: 036338e90a3e7b466924d419400c0dcc692dec5f
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97630759"
---
# <a name="azure-cosmos-db-gremlin-graph-support-and-compatibility-with-tinkerpop-features"></a>Azure Cosmos DB Gremlin Graph-stöd och kompatibilitet med TinkerPop-funktioner
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Azure Cosmos DB har stöd för [Apache Tinkerpops](https://tinkerpop.apache.org) diagram Traversal, som kallas [Gremlin](https://tinkerpop.apache.org/docs/3.3.2/reference/#graph-traversal-steps). Du kan använda Gremlin-språket för att skapa diagramentiteter (brytpunkter och kanter), ändra egenskaper inom de entiteterna, utföra frågor och bläddringar samt ta bort entiteter.

Azure Cosmos DB diagram motor närmar sig följande steg Specifikation för [Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) , men det finns skillnader i implementeringen som är specifik för Azure Cosmos dB. I den här artikeln ger vi en snabb genom gång av Gremlin och räknar upp de Gremlin-funktioner som stöds av Gremlin-API: et.

## <a name="compatible-client-libraries"></a>Kompatibla klient bibliotek

Följande tabell visar populära Gremlin-drivrutiner som du kan använda mot Azure Cosmos DB:

| Ladda ned | Källa | Komma igång | Version av anslutningsappen som stöds |
| --- | --- | --- | --- |
| [.NET](https://tinkerpop.apache.org/docs/3.4.6/reference/#gremlin-DotNet) | [Gremlin.NET på GitHub](https://github.com/apache/tinkerpop/tree/master/gremlin-dotnet) | [Skapa diagram med .NET](create-graph-dotnet.md) | 3.4.6 |
| [Java](https://mvnrepository.com/artifact/com.tinkerpop.gremlin/gremlin-java) | [Gremlin JavaDoc](https://tinkerpop.apache.org/javadocs/current/full/) | [Skapa diagram med Java](create-graph-java.md) | 3.2.0+ |
| [Node.js](https://www.npmjs.com/package/gremlin) | [Gremlin-JavaScript på GitHub](https://github.com/apache/tinkerpop/tree/master/gremlin-javascript) | [Skapa diagram med Node.js](create-graph-nodejs.md) | 3.3.4 + |
| [Python](https://tinkerpop.apache.org/docs/3.3.1/reference/#gremlin-python) | [Gremlin-Python på GitHub](https://github.com/apache/tinkerpop/tree/master/gremlin-python) | [Skapa diagram med Python](create-graph-python.md) | 3.2.7 |
| [PHP](https://packagist.org/packages/brightzone/gremlin-php) | [Gremlin-PHP på GitHub](https://github.com/PommeVerte/gremlin-php) | [Skapa diagram med PHP](create-graph-php.md) | 3.1.0 |
| [Gå till lang](https://github.com/supplyon/gremcos/) | [Gå till lang](https://github.com/supplyon/gremcos/) | | Det här biblioteket har skapats av externa deltagare. Azure Cosmos DBs teamet erbjuder inget stöd eller underhålla biblioteket. |
| [Gremlin-konsol](https://tinkerpop.apache.org/downloads.html) | [TinkerPop-dokument](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) |  [Skapa diagram med Gremlin-konsolen](create-graph-gremlin-console.md) | 3.2.0 + |

## <a name="supported-graph-objects"></a>Diagram objekt som stöds

TinkerPop är en standard som omfattar en mängd olika diagramtekniker. Därför har den standardterminologi som beskriver vilka funktioner som tillhandahålls av en diagramprovider. Azure Cosmos DB tillhandahåller en beständig, skrivbar diagramdatabas med hög samtidighet som kan partitioneras över flera servrar eller kluster. 

Följande tabell visar den TinkerPop-funktioner som implementeras av Azure Cosmos DB: 

| Kategori | Azure Cosmos DB-implementering |  Obs! | 
| --- | --- | --- |
| Diagramfunktioner | Ger beständighet och ConcurrentAccess. Designad att stödja transaktioner | Datormetoder kan implementeras via Spark-anslutningsappen. |
| Variabla funktioner | Stöder boolesk, heltal, byte, dubbel, flyttal, heltal, lång, sträng | Har stöd för primitiva typer, är kompatibel med komplexa typer via datamodellen |
| Brytpunktsfunktioner | Stöder RemoveVertices, MetaProperties, AddVertices, MultiProperties, StringIds, UserSuppliedIds, AddProperty, RemoveProperty  | Stöder att skapa, ändra och ta bort brytpunkter |
| Funktioner för brytpunktsegenskapen | StringIds, UserSuppliedIds, AddProperty, RemoveProperty, BooleanValues, ByteValues, DoubleValues, FloatValues, IntegerValues, LongValues, StringValues | Stöder att skapa, ändra och ta bort brytpunktsegenskaper |
| Kantfunktioner | AddEdges, RemoveEdges, StringIds, UserSuppliedIds, AddProperty, RemoveProperty | Stöder att skapa, ändra och ta bort kanter |
| Kantegenskapsfunktioner | Properties, BooleanValues, ByteValues, DoubleValues, FloatValues, IntegerValues, LongValues, StringValues | Stöder att skapa, ändra och ta bort kantegenskaper |

## <a name="gremlin-wire-format"></a>Gremlin-kabel format

Azure Cosmos DB använder JSON-formatet för att returnera resultat från Gremlin-åtgärder. Azure Cosmos DB stöder för närvarande JSON-formatet. I följande kodfragment visas till exempel en JSON-representation av ett hörn *som returnerats till klienten* från Azure Cosmos DB:

```json
  {
    "id": "a7111ba7-0ea1-43c9-b6b2-efc5e3aea4c0",
    "label": "person",
    "type": "vertex",
    "outE": {
      "knows": [
        {
          "id": "3ee53a60-c561-4c5e-9a9f-9c7924bc9aef",
          "inV": "04779300-1c8e-489d-9493-50fd1325a658"
        },
        {
          "id": "21984248-ee9e-43a8-a7f6-30642bc14609",
          "inV": "a8e3e741-2ef7-4c01-b7c8-199f8e43e3bc"
        }
      ]
    },
    "properties": {
      "firstName": [
        {
          "value": "Thomas"
        }
      ],
      "lastName": [
        {
          "value": "Andersen"
        }
      ],
      "age": [
        {
          "value": 45
        }
      ]
    }
  }
```

De egenskaper som används av JSON-formatet för hörnen beskrivs nedan:

| Egenskap | Beskrivning | 
| --- | --- | --- |
| `id` | ID för brytpunkten. Måste vara unik (i kombination med värdet för `_partition` om tillämpligt). Om inget värde anges kommer det att anges automatiskt med ett GUID | 
| `label` | Etiketten för brytpunkten. Den här egenskapen används för att beskriva enhets typen. |
| `type` | Används för att särskilja brytpunkter från icke-diagramdokument |
| `properties` | En uppsättning användardefinierade egenskaper associerade med brytpunkten. Varje egenskap kan ha flera värden. |
| `_partition` | Partitionsnyckeln för brytpunkten. Används för [diagram partitionering](graph-partitioning.md). |
| `outE` | Den här egenskapen innehåller en lista över kanter från ett hörn. Lagring av angränsande information med brytpunkter för snabbare körning av bläddring. Kanter grupperas baserat på deras etiketter. |

Och kanten innehåller följande information för att underlätta navigeringen till andra delar av diagrammet.

| Egenskap | Beskrivning |
| --- | --- |
| `id` | ID för kanten. Måste vara unikt (i kombination med värdet `_partition` om tillämpligt) |
| `label` | Etiketten för kanten. Den här egenskapen är valfri och används för att beskriva relationstypen. |
| `inV` | Den här egenskapen innehåller en lista över i hörn för en kant. Lagring av angränsningsinformation med kanter tillåter snabb körning av bläddringar. Brytpunkter grupperas baserat på deras etiketter. |
| `properties` | En uppsättning användardefinierade egenskaper associerade med kanten. Varje egenskap kan ha flera värden. |

Varje egenskap kan lagra flera värden inom en matris. 

| Egenskap | Beskrivning |
| --- | --- |
| `value` | Värdet på egenskapen

## <a name="gremlin-steps"></a>Gremlin-steg

Nu ska vi titta på de Gremlin-steg som stöds av Azure Cosmos DB. En fullständig referens om Gremlin finns i [TinkerPop-referens](https://tinkerpop.apache.org/docs/3.3.2/reference).

| steg | Beskrivning | TinkerPop 3.2-dokumentation |
| --- | --- | --- |
| `addE` | Lägger till en kant mellan två brytpunkter | [addE step](https://tinkerpop.apache.org/docs/3.3.2/reference/#addedge-step) |
| `addV` | Lägger till en brytpunkt i diagrammet | [addV step](https://tinkerpop.apache.org/docs/3.3.2/reference/#addvertex-step) |
| `and` | Ser till att alla bläddringar returnerar ett värde | [and step](https://tinkerpop.apache.org/docs/3.3.2/reference/#and-step) |
| `as` | Ett stegmodulator för att tilldela en variabel till utdata från ett steg | [as step](https://tinkerpop.apache.org/docs/3.3.2/reference/#as-step) |
| `by` | En stegmodulator som används med `group` och `order` | [by step](https://tinkerpop.apache.org/docs/3.3.2/reference/#by-step) |
| `coalesce` | Returnerar den första bläddringen som returnerar ett resultat | [coalesce step](https://tinkerpop.apache.org/docs/3.3.2/reference/#coalesce-step) |
| `constant` | Returnerar ett konstant värde. Används med `coalesce`| [constant step](https://tinkerpop.apache.org/docs/3.3.2/reference/#constant-step) |
| `count` | Returnerar antalet från bläddringen | [count step](https://tinkerpop.apache.org/docs/3.3.2/reference/#count-step) |
| `dedup` | Returnerar värden med borttagna dubbletter | [dedup step](https://tinkerpop.apache.org/docs/3.3.2/reference/#dedup-step) |
| `drop` | Släpper värdena (brytpunkt/kant) | [drop step](https://tinkerpop.apache.org/docs/3.3.2/reference/#drop-step) |
| `executionProfile` | Skapar en beskrivning av alla åtgärder som genererats av steget utförd Gremlin | [executionProfile-steg](graph-execution-profile.md) |
| `fold` | Fungerar som en barriär som beräknar sammanställningen av resultat| [fold step](https://tinkerpop.apache.org/docs/3.3.2/reference/#fold-step) |
| `group` | Grupperar värdena baserat på de angivna etiketterna| [group step](https://tinkerpop.apache.org/docs/3.3.2/reference/#group-step) |
| `has` | Används för att filtrera egenskaper, brytpunkter och kanter. Stöder varianterna `hasLabel`, `hasId`, `hasNot` och `has`. | [has step](https://tinkerpop.apache.org/docs/3.3.2/reference/#has-step) |
| `inject` | Matar in värden i en dataström| [inject step](https://tinkerpop.apache.org/docs/3.3.2/reference/#inject-step) |
| `is` | Används för att utföra ett filter med ett booleskt uttryck | [is step](https://tinkerpop.apache.org/docs/3.3.2/reference/#is-step) |
| `limit` | Används för att begränsa antalet objekt i bläddringen| [limit step](https://tinkerpop.apache.org/docs/3.3.2/reference/#limit-step) |
| `local` | Bäddar in ett avsnitt av en bläddring lokalt, liknar en underfråga | [local step](https://tinkerpop.apache.org/docs/3.3.2/reference/#local-step) |
| `not` | Används för att skapa negationer av ett filter | [not step](https://tinkerpop.apache.org/docs/3.3.2/reference/#not-step) |
| `optional` | Returnerar resultatet av den angivna bläddringen om den ger upphov till ett resultat, annars returneras det anropande elementet | [Valfritt steg](https://tinkerpop.apache.org/docs/3.3.2/reference/#optional-step) |
| `or` | Garanterar att minst en av bläddringarna returnerar ett värde | [or step](https://tinkerpop.apache.org/docs/3.3.2/reference/#or-step) |
| `order` | Returnerar resultat i den angivna sorteringsordningen | [order step](https://tinkerpop.apache.org/docs/3.3.2/reference/#order-step) |
| `path` | Returnerar den fullständiga sökvägen för bläddringen | [path step](https://tinkerpop.apache.org/docs/3.3.2/reference/#path-step) |
| `project` | Projicerar egenskaperna som en karta | [project step](https://tinkerpop.apache.org/docs/3.3.2/reference/#project-step) |
| `properties` | Returnerar egenskaperna för de angivna etiketterna | [properties step](https://tinkerpop.apache.org/docs/3.3.2/reference/#_properties_step) |
| `range` | Filtrerar till det angivna intervallet med värden| [range step](https://tinkerpop.apache.org/docs/3.3.2/reference/#range-step) |
| `repeat` | Upprepar steget för det angivna antalet gånger. Används för upprepning | [repeat step](https://tinkerpop.apache.org/docs/3.3.2/reference/#repeat-step) |
| `sample` | Används för exempelresultat för bläddringen | [sample step](https://tinkerpop.apache.org/docs/3.3.2/reference/#sample-step) |
| `select` | Används för att projicera resultat från bläddringen |  [select step](https://tinkerpop.apache.org/docs/3.3.2/reference/#select-step) |
| `store` | Används för icke-blockerande sammanställningar från bläddringen | [store step](https://tinkerpop.apache.org/docs/3.3.2/reference/#store-step) |
| `TextP.startingWith(string)` | Sträng filtrerings funktion. Den här funktionen används som ett predikat för `has()` steget för att matcha en egenskap med början av en specifik sträng | [TextP-predikat](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.endingWith(string)` |  Sträng filtrerings funktion. Den här funktionen används som ett predikat för `has()` steget för att matcha en egenskap med slutet av en specifik sträng | [TextP-predikat](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.containing(string)` | Sträng filtrerings funktion. Den här funktionen används som ett predikat för `has()` steget för att matcha en egenskap med innehållet i en specifik sträng | [TextP-predikat](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notStartingWith(string)` | Sträng filtrerings funktion. Den här funktionen används som ett predikat för `has()` steget för att matcha en egenskap som inte börjar med en specifik sträng | [TextP-predikat](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notEndingWith(string)` | Sträng filtrerings funktion. Den här funktionen används som ett predikat för `has()` steget för att matcha en egenskap som inte slutar med en specifik sträng | [TextP-predikat](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notContaining(string)` | Sträng filtrerings funktion. Den här funktionen används som ett predikat för `has()` steget för att matcha en egenskap som inte innehåller en specifik sträng | [TextP-predikat](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `tree` | Sammanställ sökvägar från en brytpunkt i ett träd | [tree step](https://tinkerpop.apache.org/docs/3.3.2/reference/#tree-step) |
| `unfold` | Rulla upp en iterator som ett steg| [unfold step](https://tinkerpop.apache.org/docs/3.3.2/reference/#unfold-step) |
| `union` | Sammanfoga resultat från flera bläddringar| [union step](https://tinkerpop.apache.org/docs/3.3.2/reference/#union-step) |
| `V` | Inkluderar de steg som krävs för bläddringar mellan brytpunkter och kanter `V`, `E`, `out`, `in`, `both`, `outE`, `inE`, `bothE`, `outV`, `inV`, `bothV` och `otherV` för | [vertex steps](https://tinkerpop.apache.org/docs/3.3.2/reference/#vertex-steps) |
| `where` | Används för att filtrera resultat från bläddringen. Stöder operatorerna `eq`, `neq`, `lt`, `lte`, `gt`, `gte` och `between`  | [where step](https://tinkerpop.apache.org/docs/3.3.2/reference/#where-step) |

Den skrivoptimerade motorn som tillhandahålls av Azure Cosmos DB stöder automatisk indexering av alla egenskaperna i brytpunkter och kanter som standard. Därför bearbetas frågor med filter, intervallfrågor, sortering eller sammanställning av alla egenskaper från index och hanteras effektivt. Mer information om hur indexering fungerar i Azure Cosmos DV finns i vårt papper om [schemaoberoende indexering](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf).

## <a name="behavior-differences"></a>Beteende skillnader

* Azure Cosmos DB Graph-motorn kör ***Bredd-första** _ traverser medan TinkerPop Gremlin är djup-först. Med det här beteendet uppnås bättre prestanda i horisontellt skalbart system som Cosmos DB.

## <a name="unsupported-features"></a>Funktioner som inte stöds

_ ***[Gremlin bytekod](https://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)** _ är en oberoende-specifikation för programmerings språk för diagram bläddringskontroll. Cosmos DB Graph har inte stöd för det än. Använd `GremlinClient.SubmitAsync()` och skicka Traversal som en text sträng.

_ * **`property(set, 'xyz', 1)`** _ set kardinalitet stöds inte idag. Använd `property(list, 'xyz', 1)` i stället. Mer information finns i [Egenskaper för hörn med TinkerPop](http://tinkerpop.apache.org/docs/current/reference/#vertex-properties).

_ *-**`match()` Steg** _ är inte tillgängligt för tillfället. Det här steget tillhandahåller funktioner för deklarativ fråga.

_ ***Objekt som egenskaper** _ i hörn eller kanter stöds inte. Egenskaper kan bara vara primitiva typer eller matriser.

_ ***Sortering efter mat ris egenskaper** _ `order().by(<array property>)` stöds inte. Sortering stöds endast av primitiva typer.

_ ***Icke-primitiva JSON-typer** _ stöds inte. Användnings `string` -, `number` -eller- `true` / `false` typer. `null` värden stöds inte. 

_ ***GraphSONv3** _ serialiseraren stöds inte för närvarande. Använd `GraphSONv2` serialiserare, läsare och skrivar klasser i anslutnings konfigurationen. Resultaten som returneras av Azure Cosmos DB Gremlin-API: t har inte samma format som GraphSON-formatet. 

**Lambda-uttryck och-funktioner** stöds inte för närvarande. Detta omfattar `.map{<expression>}` `.by{<expression>}` funktionerna, och `.filter{<expression>}` . Om du vill veta mer och lära dig att skriva om dem med Gremlin-steg, se [en kommentar om lambda](http://tinkerpop.apache.org/docs/current/reference/#a-note-on-lambdas)-tal.

* ***Transaktionerna** stöds inte på grund av systemets distribuerade natur.  Konfigurera lämplig konsekvens modell på Gremlin-kontot för att "läsa dina egna skrivningar" och Använd optimistisk samtidighet för att lösa skrivningar som står i konflikt med varandra.

## <a name="known-limitations"></a>Kända begränsningar

_ **Index användning för Gremlin-frågor med `.V()` steg för steg-för-steg-åtgärder**: för närvarande kommer endast det första `.V()` anropet av en genom gång att använda indexet för att matcha eventuella filter eller predikat som är kopplade till den. Efterföljande anrop kommer inte att se indexet, vilket kan öka svars tiden och kostnaden för frågan.
    
Vid antagande av standard indexering använder en typisk Read Gremlin-fråga som börjar med `.V()` steget parametrar i sina kopplade filtrerings steg, till exempel `.has()` eller `.where()` för att optimera frågans kostnad och prestanda. Exempel:

```java
g.V().has('category', 'A')
```

Men när mer än ett `.V()` steg ingår i Gremlin-frågan, kanske inte matchningen av data för frågan är optimal. Gör följande fråga som exempel:

```java
g.V().has('category', 'A').as('a').V().has('category', 'B').as('b').select('a', 'b')
```

Den här frågan returnerar två grupper av hörn baserat på deras egenskap som kallas `category` . I det här fallet kommer endast det första anropet att `g.V().has('category', 'A')` använda indexet för att matcha hörnen baserat på egenskaperna för deras egenskaper.

En lösning för den här frågan är att använda under steg, till exempel `.map()` och `union()` . Detta är WINS nedan:

```java
// Query workaround using .map()
g.V().has('category', 'A').as('a').map(__.V().has('category', 'B')).as('b').select('a','b')

// Query workaround using .union()
g.V().has('category', 'A').fold().union(unfold(), __.V().has('category', 'B'))
```

Du kan granska prestanda för frågorna med hjälp av Gremlin- [ `executionProfile()` steget](graph-execution-profile.md).

## <a name="next-steps"></a>Nästa steg

* Kom igång med att skapa ett diagramprogram [med våra SDK:er](create-graph-dotnet.md) 
* Läs mer om [diagramstöd](graph-introduction.md) i Azure Cosmos DB
