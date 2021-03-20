---
title: Schemalägg körning av indexerare
titleSuffix: Azure Cognitive Search
description: Schemalägg Azure Kognitiv sökning indexerare för att indexera innehåll regelbundet eller vid vissa tidpunkter.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/09/2021
ms.openlocfilehash: 8ae9a89ddba2010603ae5a5f6b812e3aa1e1e3a6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100097984"
---
# <a name="how-to-schedule-indexers-in-azure-cognitive-search"></a>Så här schemalägger du indexerare i Azure Kognitiv sökning

En indexerare körs vanligt vis en gång, omedelbart efter att den har skapats. Därefter kan du köra den igen på begäran med antingen Azure Portal, [köra indexerare (rest)](/rest/api/searchservice/run-indexer)eller ett Azure SDK. Alternativt kan du också konfigurera en indexerare så att den körs enligt ett schema. Några situationer där indexerare schemaläggning är användbart är bland annat:

* Källdata ändras med tiden och du vill att Sök indexeraren automatiskt ska bearbeta differensen.

* Källdata är mycket stora och du vill sprida bearbetningen av indexeraren över tid. Indexerare-jobb omfattas av en maximal körnings tid på 24 timmar för vanliga data källor och 2 timmar för indexerare med färdighetsuppsättningar. Om indexeringen inte kan slutföras inom det maximala intervallet kan du konfigurera ett schema som körs var 2: e timme. Indexerare kan automatiskt hämta var de slutade, som ett tecken på ett internt vatten märke som markerar var indexeringen senast avslutades. Att köra en indexerare på ett återkommande 2 tim schema gör det möjligt för IT att bearbeta en mycket stor data uppsättning (många miljoner dokument) utöver intervallet som tillåts för ett enda jobb. Mer information om hur du indexerar stora data volymer finns i [så här indexerar du stora data uppsättningar i Azure kognitiv sökning](search-howto-large-index.md).

* Ett Sök index fylls i från flera data källor och du vill att indexerarna ska köras vid olika tidpunkter för att minska konflikter.

Ett schema kan se ut ungefär så här: från och med 1 januari och körs var 50: e minut.

```json
{
    "dataSourceName" : "myazuresqldatasource",
    "targetIndexName" : "target index name",
    "schedule" : { "interval" : "PT50M", "startTime" : "2021-01-01T00:00:00Z" }
}
```

> [!NOTE]
> Scheduler är en inbyggd funktion i Azure Kognitiv sökning. Det finns inget stöd för externa schemaläggare.

## <a name="schedule-property"></a>Schema egenskap

Ett schema är en del av index definitions definitionen. Om **Schedule** -egenskapen utelämnas körs indexeraren bara en gång omedelbart efter att den har skapats. Om du lägger till en **schema** egenskap anger du två delar.

| Egenskap | Beskrivning |
|----------|-------------|
|**Intervall** | kunna Tiden mellan starten av två efterföljande körningar av indexerare. Det minsta tillåtna intervallet är 5 minuter och det längsta är 1440 minuter (24 timmar). Det måste formateras som ett XSD "dayTimeDuration"-värde (en begränsad delmängd av ett [varaktighets värde på ISO 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). Mönstret för detta är: `P(nD)(T(nH)(nM))` . <br/><br/>Exempel: `PT15M` för var 15: e `PT2H` timme, för var 2: e timme.|
| **Start tid (UTC)** | valfritt Anger när schemalagda körningar ska börja. Om den utelämnas används den aktuella UTC-tiden. Den här tiden kan vara förr, i vilket fall den första körningen schemaläggs som om indexeraren har körts kontinuerligt sedan den ursprungliga **Starttimen**.<br/><br/>Exempel: `2021-01-01T00:00:00Z` från och med midnatt den 1 januari, `2021-01-05T22:28:00Z` från 10:28 kl. den 5 januari.|

## <a name="scheduling-behavior"></a>Schemaläggnings beteende

Endast en körning av en indexerare kan köras i taget. Om en indexerare redan körs när nästa körning har schemalagts, senareläggs körningen tills nästa schemalagda tid.

Låt oss ta en titt på ett exempel för att göra detta mer konkret. Anta att vi konfigurerar ett indexare schema med ett **intervall** på varje timme och **Start tiden** den 1 juni 2019 kl. 8:00:00 am UTC. Det här kan inträffa när en indexerare-körning tar längre tid än en timme:

* Den första indexerare-körningen börjar vid eller med den 1 juni 2019 kl. 8:00 AM UTC. Anta att den här körningen tar 20 minuter (eller när som helst mindre än en timme).
* Den andra körningen börjar vid eller runt den 1 juni 2019 9:00 AM UTC. Anta att den här körningen tar 70 minuter – mer än en timme – och att den inte slutförs förrän 10:10 är UTC.
* Den tredje körningen är schemalagd att starta kl. 10:00 AM UTC, men vid den tidpunkten körs fortfarande föregående körning. Den här schemalagda körningen hoppas över. Nästa körning av indexeraren kommer inte att starta förrän 11:00 AM UTC.

> [!NOTE]
> Om en indexerare har angetts till ett visst schema men upprepade gånger Miss lyckas i samma dokument varje gång, kommer indexeraren att köras på ett mindre frekvent intervall (upp till det maximala intervallet på minst en gång var 24: e timme) tills den har börjat att gälla. Om du tror att du har fast det underliggande problemet kan du köra indexeraren manuellt, och om indexeringen lyckas, kommer indexeraren att återgå till sitt regelbundna schema.

## <a name="schedule-using-rest"></a>Schemalägg med REST

Ange egenskapen **schema** när du skapar eller uppdaterar indexeraren.

```http
    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2021-01-01T00:00:00Z" }
    }
```

## <a name="schedule-using-net"></a>Schemalägg med .NET

Följande C#-exempel skapar en Azure SQL Database-indexerare med hjälp av en fördefinierad data källa och ett index, och ställer in schemat så att det körs en gång per dag, med början nu:

```csharp
var schedule = new IndexingSchedule(TimeSpan.FromDays(1))
{
    StartTime = DateTimeOffset.Now
};

var indexer = new SearchIndexer("hotels-sql-idxr", dataSource.Name, searchIndex.Name)
{
    Description = "Data indexer",
    Schedule = schedule
};

await indexerClient.CreateOrUpdateIndexerAsync(indexer);
```

Schemat definieras med [IndexingSchedule](/dotnet/api/azure.search.documents.indexes.models.indexingschedule) -klassen när du skapar eller uppdaterar en indexerare med hjälp av [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient). **IndexingSchedule** -konstruktorn kräver en **intervall** parameter som anges med ett **TimeSpan** -objekt. Som tidigare nämnts är det minsta värdet på intervallet 5 minuter och det största värdet är 24 timmar. Den andra **StartTime** -parametern, som anges som ett **DateTimeOffset** -objekt, är valfri.

## <a name="next-steps"></a>Nästa steg

För indexerare som körs enligt ett schema kan du övervaka åtgärder genom att hämta status från Sök tjänsten eller Hämta detaljerad information genom att aktivera diagnostisk loggning.

* [Övervaka status för Sök indexering](search-howto-monitor-indexers.md)
* [Samla in och analysera loggdata](search-monitor-logs.md)