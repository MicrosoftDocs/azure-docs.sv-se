---
title: Övervaka status och resultat för indexerare
titleSuffix: Azure Cognitive Search
description: Övervaka status, förlopp och resultat för Azure Kognitiv sökning indexerare i Azure Portal, med hjälp av REST API eller .NET SDK.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/28/2021
ms.openlocfilehash: a94720e6b84821d53a3bfdcbdce249390078940f
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063261"
---
# <a name="how-to-monitor-azure-cognitive-search-indexer-status-and-results"></a>Övervaka status och resultat för Azure Kognitiv sökning Indexer

Du kan övervaka bearbetning av indexerare i Azure Portal eller via programmering via REST-anrop eller en Azure SDK. Förutom status för själva indexeraren kan du granska start-och slut tider och detaljerade fel och varningar från en viss körning.

## <a name="monitor-using-azure-portal"></a>Övervaka med hjälp av Azure Portal

Du kan se aktuell status för alla indexerare på sidan Översikt över Sök tjänsten. Portal sidor uppdateras med några minuters mellanrum, så att du inte ser bevis på att en ny indexerare körs omedelbart.

   ![Lista med indexerare](media/search-monitor-indexers/indexers-list.png "Lista med indexerare")

| Status | Beskrivning |
|--------|-------------|
| **Pågår** | Indikerar aktiv körning. Portalen kommer att rapportera om delvis information. När indexeringen fortskrider kan du titta på att värdet för **dokumentets slutförda** värde växer som svar. Indexerare som bearbetar stora data volymer kan ta lång tid att köra. Till exempel kan indexerare som hanterar miljon tals käll dokument köras i 24 timmar och sedan starta om nästan omedelbart. Status för hög volym indexerare kan alltid stå i **förlopp** i portalen. Även om en indexerare körs, finns det information om pågående förloppet och tidigare körningar. |
| **Klart** | Indikerar att körningen lyckades. En indexerare-körning kan lyckas även om enskilda dokument innehåller fel, om antalet fel är mindre än inställningen för **maximalt antal misslyckade objekt** i indexeraren. |
| **Misslyckades** | Antalet fel överskred Max antalet **misslyckade objekt** och indexeringen har stoppats. |
| **Återställ** | Indexeraren interna ändrings spårnings tillstånd har återställts. Indexeraren körs fullständigt, uppdaterar alla dokument och inte bara de med nyare tidsstämplar. |

Du kan klicka på en indexerare i listan om du vill se mer information om indexeraren aktuella och senaste körningar.

   ![Sammanfattning och körnings historik för indexerare](media/search-monitor-indexers/indexer-summary.png "Sammanfattning och körnings historik för indexerare")

I **sammanfattnings diagrammet för indexeraren** visas ett diagram över antalet bearbetade dokument i de senaste körningarna.

Listan med **körnings information** visar upp till 50 av de senaste körnings resultaten. Klicka på ett körnings resultat i listan om du vill se information om den här körningen. Detta omfattar dess start-och slut tider och eventuella fel och varningar som har inträffat.

   ![Information om körning av indexerare](media/search-monitor-indexers/indexer-execution.png "Information om körning av indexerare")

Om det fanns användarspecifika problem under körningen visas de i fälten fel och varningar.

   ![Information om indexerare med fel](media/search-monitor-indexers/indexer-execution-error.png "Information om indexerare med fel")

Varningar är vanliga med vissa typer av indexerare och indikerar inte alltid ett problem. Till exempel indexerare som använder kognitiva tjänster kan rapportera varningar när bild-eller PDF-filer inte innehåller någon text att bearbeta. 

Mer information om hur du undersöker index fel och varningar finns i [Felsöka vanliga indexerings problem i Azure kognitiv sökning](search-indexer-troubleshooting.md).

## <a name="monitor-using-get-indexer-status-rest-api"></a>Övervaka med hjälp av Get Indexer-status (REST API)

Du kan hämta status och körnings historik för en indexerare med hjälp av [status kommandot Get indexerare](/rest/api/searchservice/get-indexer-status):

```http
GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2020-06-30
api-key: [Search service admin key]
```

Svaret innehåller övergripande indexerings status, det sista (eller inaktuella) indexerings anropet och historiken för senaste indexerare-anrop.

```output
{
    "status":"running",
    "lastResult": {
        "status":"success",
        "errorMessage":null,
        "startTime":"2018-11-26T03:37:18.853Z",
        "endTime":"2018-11-26T03:37:19.012Z",
        "errors":[],
        "itemsProcessed":11,
        "itemsFailed":0,
        "initialTrackingState":null,
        "finalTrackingState":null
     },
    "executionHistory":[ {
        "status":"success",
         "errorMessage":null,
        "startTime":"2018-11-26T03:37:18.853Z",
        "endTime":"2018-11-26T03:37:19.012Z",
        "errors":[],
        "itemsProcessed":11,
        "itemsFailed":0,
        "initialTrackingState":null,
        "finalTrackingState":null
    }]
}
```

Körnings historiken innehåller upp till de 50 senaste körningarna, som sorteras i omvänd kronologisk ordning (senast första).

Observera att det finns två olika status värden. Status på den högsta nivån är för själva indexeraren. En indexerare-status för **körning** innebär att indexeraren är korrekt inställd och tillgänglig för körning, men inte att den körs för närvarande.

Varje körning av indexeraren har också sin egen status som anger om den specifika körningen pågår (**körs**) eller redan har slutförts med statusen **lyckades**, **transientFailure** eller **persistentFailure** . 

När en indexerare återställs för att uppdatera status för ändrings spårning läggs en separat post för körnings historik till med en **återställnings** status.

Mer information om status koder och övervaknings data för indexerare finns i [Hämta indexerings status](/rest/api/searchservice/get-indexer-status).

## <a name="monitor-using-net"></a>Övervaka med .NET

Med hjälp av Azure kognitiv sökning .NET SDK skriver följande C#-exempel information om en indexerare status och resultatet av den senaste (eller pågående) körningen till-konsolen.

```csharp
static void CheckIndexerStatus(SearchIndexerClient indexerClient, SearchIndexer indexer)
{
    try
    {
        string indexerName = "hotels-sql-idxr";
        SearchIndexerStatus execInfo = indexerClient.GetIndexerStatus(indexerName);

        Console.WriteLine("Indexer has run {0} times.", execInfo.ExecutionHistory.Count);
        Console.WriteLine("Indexer Status: " + execInfo.Status.ToString());

        IndexerExecutionResult result = execInfo.LastResult;

        Console.WriteLine("Latest run");
        Console.WriteLine("Run Status: {0}", result.Status.ToString());
        Console.WriteLine("Total Documents: {0}, Failed: {1}", result.ItemCount, result.FailedItemCount);

        TimeSpan elapsed = result.EndTime.Value - result.StartTime.Value;
        Console.WriteLine("StartTime: {0:T}, EndTime: {1:T}, Elapsed: {2:t}", result.StartTime.Value, result.EndTime.Value, elapsed);

        string errorMsg = (result.ErrorMessage == null) ? "none" : result.ErrorMessage;
        Console.WriteLine("ErrorMessage: {0}", errorMsg);
        Console.WriteLine(" Document Errors: {0}, Warnings: {1}\n", result.Errors.Count, result.Warnings.Count);
    }
    catch (Exception e)
    {
        // Handle exception
    }
}
```

Utdata i-konsolen ser ut ungefär så här:

```output
Indexer has run 18 times.
Indexer Status: Running
Latest run
  Run Status: Success
  Total Documents: 7, Failed: 0
  StartTime: 11:29:31 PM, EndTime: 11:29:31 PM, Elapsed: 00:00:00.2560000
  ErrorMessage: none
  Document Errors: 0, Warnings: 0
```

Observera att det finns två olika status värden. Status på den högsta nivån är status för själva indexeraren. En indexerare status för **körning** innebär att indexeraren är korrekt inställd och tillgänglig för körning, men inte att den körs.

Varje körning av indexeraren har också sin egen status för huruvida den specifika körningen pågår (**körs**) eller redan har slutförts med statusen **lyckades** eller **TransientError** . 

När en indexerare återställs för att uppdatera ändrings spårnings statusen läggs en separat historik post till med en **återställnings** status.

## <a name="next-steps"></a>Nästa steg

Mer information om status koder och information om övervakning av indexerare finns i följande API-referens:

* [GetIndexerStatus (REST API)](/rest/api/searchservice/get-indexer-status)
* [IndexerStatus](/dotnet/api/azure.search.documents.indexes.models.indexerstatus)
* [IndexerExecutionStatus](/dotnet/api/azure.search.documents.indexes.models.indexerexecutionstatus)
* [IndexerExecutionResult](/dotnet/api/azure.search.documents.indexes.models.indexerexecutionresult)