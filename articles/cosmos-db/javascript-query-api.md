---
title: Arbeta med Java Script Integrated Query API i Azure Cosmos DB lagrade procedurer och utlösare
description: Den här artikeln beskriver begreppen för JavaScript-språk – integrerat fråge-API för att skapa lagrade procedurer och utlösare i Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: tisande
ms.reviewer: sngun
ms.custom: devx-track-js
ms.openlocfilehash: b2563a9af0e0ca6943059698e29d139143780d93
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93340999"
---
# <a name="javascript-query-api-in-azure-cosmos-db"></a>JavaScript-fråge-API i Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Förutom att skicka frågor med hjälp av SQL-API: et i [Cosmos DB Azure Cosmos DB tillhandahåller SDK: n på Server sidan](https://github.com/Azure/azure-cosmosdb-js-server/) ett JavaScript-gränssnitt för att utföra optimerade frågor i Cosmos DB lagrade procedurer och utlösare. Du behöver inte vara medveten om SQL-språket för att kunna använda det här JavaScript-gränssnittet. Med Java Script-API: et kan du skapa frågor genom programmering genom att skicka predikat till sekvenser med funktions anrop, med en syntax som är välbekant för ECMAScript5's-matriser och populära JavaScript-bibliotek som Lodash. Frågor parsas av JavaScript-körningen och körs effektivt med Azure Cosmos DB index.

## <a name="supported-javascript-functions"></a>JavaScript-funktioner som stöds

| **Funktion** | **Beskrivning** |
|---------|---------|
|`chain() ... .value([callback] [, options])`|Startar ett länkat samtal som måste avslutas med Value ().|
|`filter(predicateFunction [, options] [, callback])`|Filtrerar inmatade objekt med en predikat-funktion som returnerar true/false för att filtrera in/ut indatafiler i den resulterande uppsättningen. Den här funktionen fungerar ungefär som en WHERE-sats i SQL.|
|`flatten([isShallow] [, options] [, callback])`|Kombinerar och fören klar matriser från varje inobjekt till en enda matris. Den här funktionen fungerar ungefär som SelectMany i LINQ.|
|`map(transformationFunction [, options] [, callback])`|Använder en projektion med en Transformations funktion som mappar varje inobjekt till ett JavaScript-objekt eller-värde. Den här funktionen fungerar ungefär som en SELECT-sats i SQL.|
|`pluck([propertyName] [, options] [, callback])`|Den här funktionen är en genväg till en karta som extraherar värdet för en enskild egenskap från varje inobjekt.|
|`sortBy([predicate] [, options] [, callback])`|Skapar en ny uppsättning dokument genom att sortera dokumenten i indata-dataströmmen i stigande ordning genom att använda det angivna predikatet. Den här funktionen fungerar likadant som en ORDER BY-sats i SQL.|
|`sortByDescending([predicate] [, options] [, callback])`|Skapar en ny uppsättning dokument genom att sortera dokumenten i indata-dataströmmen i fallande ordning med det angivna predikatet. Den här funktionen fungerar ungefär som en ORDER BY x DESC-sats i SQL.|
|`unwind(collectionSelector, [resultSelector], [options], [callback])`|Utför en själv koppling med en inre matris och lägger till resultat från båda sidor som tupler till resultat projektionen. Till exempel kan du ansluta till ett person dokument med person. hus djur skulle producera [person, PET]-tupler. Detta liknar SelectMany i .NET-länken.|

När de ingår i predikat och/eller Selector-funktioner, optimeras följande JavaScript-skript automatiskt för att köras direkt på Azure Cosmos DB index:

- Enkla operatorer `=` `+` `-` `*` `/` : `%` `|` `^` `&` `==` `!=` `===` `!===` `<` `>` `<=` `>=` `||` `&&` `<<` `>>` `>>>!``~`
- Litteraler, inklusive objektets litteraler: {}
- var, retur

Följande JavaScript-konstruktioner blir inte optimerade för Azure Cosmos DB index:

- Kontroll flöde (till exempel, om, till exempel)
- Funktions anrop

Mer information finns på sidan om [Cosmos DB på Server sidans JavaScript-dokumentation](https://github.com/Azure/azure-cosmosdb-js-server/).

## <a name="sql-to-javascript-cheat-sheet"></a>SQL till JavaScript lathund-blad

I följande tabell presenteras olika SQL-frågor och motsvarande JavaScript-frågor. Som med SQL-frågor är egenskaper (till exempel item.id) Skift läges känsliga.

> [!NOTE]
> `__` (dubbelunderstreck) är ett alias till `getContext().getCollection()` när du använder frågan JavaScript API.

|**SQL**|**Java Script-fråge-API**|**Beskrivning**|
|---|---|---|
|Select<br>FRÅN dokument| _ _. map (funktion (doc) { <br>&nbsp;&nbsp;&nbsp;&nbsp;returnera dokument;<br>});|Resulterar i alla dokument (med fortsättnings-token) som är.|
|SELECT <br>&nbsp;&nbsp;&nbsp;docs.id,<br>&nbsp;&nbsp;&nbsp;dok. Message som MSG,<br>&nbsp;&nbsp;&nbsp;dokument. åtgärder <br>FRÅN dokument|_ _. map (funktion (doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;returrelaterade<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ID: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Msg: doc. Message,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;åtgärder: doc. Actions<br>&nbsp;&nbsp;&nbsp;&nbsp;};<br>});|Projekterar ID, meddelande (alias till MSG) och åtgärd från alla dokument.|
|Select<br>FRÅN dokument<br>WHERE<br>&nbsp;&nbsp;&nbsp;dok. ID = "X998_Y998"|_ _ filter (Function (doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;returnera doc.id = = = "X998_Y998";<br>});|Frågor för dokument med predikatet: ID = "X998_Y998".|
|Select<br>FRÅN dokument<br>WHERE<br>&nbsp;&nbsp;&nbsp;ARRAY_CONTAINS (dokument. Taggar, 123)|_ _. filter (Function (x) {<br>&nbsp;&nbsp;&nbsp;&nbsp;returnera x. Taggar && x. Tags. indexOf (123) >-1;<br>});|Frågor för dokument med egenskapen Taggar och taggar är en matris som innehåller värdet 123.|
|SELECT<br>&nbsp;&nbsp;&nbsp;docs.id,<br>&nbsp;&nbsp;&nbsp;dok. Message som MSG<br>FRÅN dokument<br>WHERE<br>&nbsp;&nbsp;&nbsp;dok. ID = "X998_Y998"|_ _. Chain ()<br>&nbsp;&nbsp;&nbsp;&nbsp;. filter (Function (doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;returnera doc.id = = = "X998_Y998";<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;. map (funktion (doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;returrelaterade<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ID: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Msg: doc. Message<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;};<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>. Value ();|Frågor om dokument med ett predikat, ID = "X998_Y998" och projekterar sedan ID och meddelande (alias till MSG).|
|Välj värde tagg<br>FRÅN dokument<br>KOPPLA tagg i dokument. Taggen<br>Sortera efter docs._ts|_ _. Chain ()<br>&nbsp;&nbsp;&nbsp;&nbsp;. filter (Function (doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;returnera dokument. Taggar && array. isArray (doc. Taggar);<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;. sortBy (Function (doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;returnera doc._ts;<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;. plocka ("Taggar")<br>&nbsp;&nbsp;&nbsp;&nbsp;. förenkla ()<br>&nbsp;&nbsp;&nbsp;&nbsp;. Value ()|Filter för dokument som har en mat ris egenskap, taggar och sorterar de resulterande dokumenten med system egenskapen _ts timestamp, och sedan lägger Project + samman taggarna array.|

## <a name="next-steps"></a>Nästa steg

Lär dig mer om begrepp och hur du skriver och använder lagrade procedurer, utlösare och användardefinierade funktioner i Azure Cosmos DB:

- [Skriva lagrade procedurer och utlösare med Java Script-fråga-API](how-to-write-javascript-query-api.md)
- [Arbeta med Azure Cosmos DB lagrade procedurer, utlösare och användardefinierade funktioner](stored-procedures-triggers-udfs.md)
- [Använda lagrade procedurer, utlösare, användardefinierade funktioner i Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md)
- [API-referens för Azure Cosmos DB JavaScript-Server Sidan](https://azure.github.io/azure-cosmosdb-js-server)
- [JavaScript-ES6 (ECMA 2015)](https://www.ecma-international.org/ecma-262/6.0/)
