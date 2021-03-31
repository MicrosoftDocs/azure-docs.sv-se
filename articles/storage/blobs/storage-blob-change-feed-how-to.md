---
title: Bearbeta ändrings flöde i Azure Blob Storage | Microsoft Docs
description: Lär dig hur du bearbetar ändringar i flödes loggar i ett .NET-klient program
author: normesta
ms.author: normesta
ms.date: 09/08/2020
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.custom: devx-track-csharp
ms.openlocfilehash: f0e89fdfba852fc056cf48efd1b92daabb272cf0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89568259"
---
# <a name="process-change-feed-in-azure-blob-storage"></a>Bearbeta ändrings flöde i Azure Blob Storage

Ändra feed innehåller transaktions loggar för alla ändringar som sker i blobbar och blob-metadata i ditt lagrings konto. Den här artikeln visar hur du läser ändringar av flödes poster med hjälp av processor biblioteket för BLOB Change-feed.

Mer information om ändrings flödet finns i [ändra feed i Azure Blob Storage](storage-blob-change-feed.md).

## <a name="get-the-blob-change-feed-processor-library"></a>Hämta processor biblioteket för BLOB Change feed

1. Öppna ett kommando fönster (till exempel: Windows PowerShell).
2. Från projekt katalogen installerar du [paketet **Azure. Storage. blobs. Changefeed** NuGet](https://www.nuget.org/packages/Azure.Storage.Blobs.ChangeFeed/).

```console
dotnet add package Azure.Storage.Blobs --version 12.5.1
dotnet add package Azure.Storage.Blobs.ChangeFeed --version 12.0.0-preview.4
```
## <a name="read-records"></a>Läsa poster

> [!NOTE]
> Ändrings flödet är en oföränderlig och skrivskyddad entitet i ditt lagrings konto. Ett valfritt antal program kan läsa och bearbeta ändrings flödet samtidigt och självständigt med egen bekvämlighet. Poster tas inte bort från ändra feed när ett program läser dem. Läsnings-eller upprepnings statusen för varje förbruknings läsare är oberoende och underhålls endast av ditt program.

Det här exemplet itererar igenom alla poster i ändrings flödet, lägger till dem i en lista och returnerar sedan listan till anroparen.
 
```csharp
public async Task<List<BlobChangeFeedEvent>> ChangeFeedAsync(string connectionString)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();

    List<BlobChangeFeedEvent> changeFeedEvents = new List<BlobChangeFeedEvent>();

    // Get all the events in the change feed. 
    await foreach (BlobChangeFeedEvent changeFeedEvent in changeFeedClient.GetChangesAsync())
    {
        changeFeedEvents.Add(changeFeedEvent);
    }

    return changeFeedEvents;
}
```

Det här exemplet skriver ut till konsolen med några värden från varje post i listan. 

```csharp
public void showEventData(List<BlobChangeFeedEvent> changeFeedEvents)
{
    foreach (BlobChangeFeedEvent changeFeedEvent in changeFeedEvents)
    {
        string subject = changeFeedEvent.Subject;
        string eventType = changeFeedEvent.EventType.ToString();
        string api = changeFeedEvent.EventData.Api;

        Console.WriteLine("Subject: " + subject + "\n" +
        "Event Type: " + eventType + "\n" +
        "Api: " + api);
    }
}
```

## <a name="resume-reading-records-from-a-saved-position"></a>Återuppta läsning av poster från en sparad position

Du kan välja att spara Läs positionen i ändrings flödet och sedan återuppta sökningen genom posterna vid ett senare tillfälle. Du kan spara Läs positionen genom att hämta Change feed-markören. Markören är en **sträng** och ditt program kan spara strängen på ett sätt som passar ditt programs design (till exempel: till en fil eller databas).

Det här exemplet itererar igenom alla poster i ändrings flödet, lägger till dem i en lista och sparar markören. Listan och markören returneras till anroparen. 

```csharp
public async Task<(string, List<BlobChangeFeedEvent>)> ChangeFeedResumeWithCursorAsync
    (string connectionString,  string cursor)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();
    List<BlobChangeFeedEvent> changeFeedEvents = new List<BlobChangeFeedEvent>();

    IAsyncEnumerator<Page<BlobChangeFeedEvent>> enumerator = changeFeedClient
        .GetChangesAsync(continuation: cursor)
        .AsPages(pageSizeHint: 10)
        .GetAsyncEnumerator();

    await enumerator.MoveNextAsync();

    foreach (BlobChangeFeedEvent changeFeedEvent in enumerator.Current.Values)
    {
    
        changeFeedEvents.Add(changeFeedEvent);             
    }
    
    // Update the change feed cursor.  The cursor is not required to get each page of events,
    // it is intended to be saved and used to resume iterating at a later date.
    cursor = enumerator.Current.ContinuationToken;
    return (cursor, changeFeedEvents);
}
```

## <a name="stream-processing-of-records"></a>Strömmande bearbetning av poster

Du kan välja att bearbeta ändrings flödes poster när de allokeras till ändrings flödet. Se [specifikationer](storage-blob-change-feed.md#specifications). Ändrings händelserna publiceras i ändrings flödet vid en period på 60 sekunder i genomsnitt. Vi rekommenderar att du söker efter nya ändringar med den här tiden när du anger ditt avsöknings intervall.

Det här exemplet söker regelbundet efter ändringar.  Om det finns ändrings poster bearbetar den här koden dessa poster och sparar ändrings flödes markören. På så sätt om processen har stoppats och sedan startats igen kan programmet använda markören för att återuppta bearbetningen av poster där den senast slutade. I det här exemplet sparas markören till en lokal program konfigurations fil, men programmet kan spara den i alla formulär som är mest begripligt för ditt scenario. 

```csharp
public async Task ChangeFeedStreamAsync
    (string connectionString, int waitTimeMs, string cursor)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();

    while (true)
    {
        IAsyncEnumerator<Page<BlobChangeFeedEvent>> enumerator = changeFeedClient
        .GetChangesAsync(continuation: cursor).AsPages().GetAsyncEnumerator();

        while (true) 
        {
            var result = await enumerator.MoveNextAsync();

            if (result)
            {
                foreach (BlobChangeFeedEvent changeFeedEvent in enumerator.Current.Values)
                {
                    string subject = changeFeedEvent.Subject;
                    string eventType = changeFeedEvent.EventType.ToString();
                    string api = changeFeedEvent.EventData.Api;

                    Console.WriteLine("Subject: " + subject + "\n" +
                        "Event Type: " + eventType + "\n" +
                        "Api: " + api);
                }
            
                // helper method to save cursor. 
                SaveCursor(enumerator.Current.ContinuationToken);
            }
            else
            {
                break;
            }

        }
        await Task.Delay(waitTimeMs);
    }

}

public void SaveCursor(string cursor)
{
    System.Configuration.Configuration config = 
        ConfigurationManager.OpenExeConfiguration
        (ConfigurationUserLevel.None);

    config.AppSettings.Settings.Clear();
    config.AppSettings.Settings.Add("Cursor", cursor);
    config.Save(ConfigurationSaveMode.Modified);
}
```

## <a name="reading-records-within-a-time-range"></a>Läser poster inom ett tidsintervall

Du kan läsa poster som ligger inom ett angivet tidsintervall. Det här exemplet itererar igenom alla poster i bytet som faller mellan 3:00 PM den 2 2020 och 2:00: a augusti 7 2020, lägger till dem i en lista och returnerar sedan listan till anroparen.

### <a name="selecting-segments-for-a-time-range"></a>Välja segment för ett tidsintervall

```csharp
public async Task<List<BlobChangeFeedEvent>> ChangeFeedBetweenDatesAsync(string connectionString)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();
    List<BlobChangeFeedEvent> changeFeedEvents = new List<BlobChangeFeedEvent>();

    // Create the start and end time.  The change feed client will round start time down to
    // the nearest hour, and round endTime up to the next hour if you provide DateTimeOffsets
    // with minutes and seconds.
    DateTimeOffset startTime = new DateTimeOffset(2020, 3, 2, 15, 0, 0, TimeSpan.Zero);
    DateTimeOffset endTime = new DateTimeOffset(2020, 8, 7, 2, 0, 0, TimeSpan.Zero);

    // You can also provide just a start or end time.
    await foreach (BlobChangeFeedEvent changeFeedEvent in changeFeedClient.GetChangesAsync(
        start: startTime,
        end: endTime))
    {
        changeFeedEvents.Add(changeFeedEvent);
    }

    return changeFeedEvents;
}
```

Start tiden som du anger avrundas nedåt till närmaste timme och slut tiden avrundas uppåt till närmaste timme. Det är möjligt att användarna kan se händelser som inträffat före start tiden och efter slut tiden. Det är också möjligt att vissa händelser som inträffar mellan start-och slut tiden inte visas. Det beror på att händelser kan registreras under den timme som tidigare är start tid eller under timmen efter slut tiden.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om att ändra flödes loggar. Se [ändra feed i Azure Blob Storage](storage-blob-change-feed.md)
