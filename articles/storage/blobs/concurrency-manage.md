---
title: Hantera samtidighet i Blob Storage
titleSuffix: Azure Storage
description: Lär dig hur du hanterar flera skrivare till en BLOB genom att implementera antingen optimistisk eller pessimistisk concurrency i ditt program. Optimistisk samtidighet kontrollerar ETag-värdet för en blob och jämför det med den angivna ETag-filen. Pessimistisk samtidighet använder ett exklusivt lån för att låsa blobben till andra skrivare.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: ea0bed0884a3a03e2cd15b274b2afb0f054b0cbd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96523638"
---
# <a name="managing-concurrency-in-blob-storage"></a>Hantera samtidighet i Blob Storage

Moderna program har ofta flera användare som visar och uppdaterar data samtidigt. Programutvecklare måste noggrant tänka på hur man ger användarna en förutsägbar upplevelse, särskilt för scenarier där flera användare kan uppdatera samma data. Det finns tre huvudsakliga data samtidighets strategier som utvecklare vanligt vis funderar på:  

- **Optimistisk samtidighet**: ett program som utför en uppdatering kommer som en del av uppdateringen att avgöra om data har ändrats sedan programmet senast läste dessa data. Om till exempel två användare som visar en wiki-sida gör en uppdatering av sidan måste wiki-plattformen se till att den andra uppdateringen inte skriver över den första uppdateringen. Det måste också se till att båda användarna förstår om deras uppdatering lyckades. Den här strategin används oftast i webb program.

- **Pessimistisk samtidighet**: ett program som söker efter en uppdatering tar ett lås på ett objekt som hindrar andra användare från att uppdatera data förrän låset släpps. I ett scenario med primär/sekundär datareplikering där endast primärt utför uppdateringar, är den primära vanligt vis ett exklusivt lås på data under en längre tid, så att ingen annan kan uppdatera den.

- **Senaste skrivarens WINS**: en metod som gör att uppdaterings åtgärder kan fortsätta utan att först fastställa om ett annat program har uppdaterat data sedan den lästes. Den här metoden används vanligt vis när data partitioneras på ett sådant sätt att flera användare inte kommer åt samma data på samma gång. Det kan också vara användbart när korta data strömmar bearbetas.

Azure Storage stöder alla tre strategier, men det är särskilt möjligt att ge fullständig support för optimistisk och pessimistisk samtidighet. Azure Storage har utformats för att omfatta en stark konsekvens modell som garanterar att när tjänsten utför en INSERT-eller Update-åtgärd, returnerar efterföljande Läs åtgärder den senaste uppdateringen.

Förutom att välja en lämplig samtidighets strategi bör utvecklare också vara medvetna om hur en lagrings plattform isolerar ändringar, särskilt ändringar av samma objekt mellan transaktioner. Azure Storage använder ögonblicks bild isolering för att tillåta Läs åtgärder samtidigt med Skriv åtgärder inom en enda partition. Isolering av ögonblicks bilder garanterar att alla Läs åtgärder returnerar en konsekvent ögonblicks bild av data även när uppdateringar inträffar.

Du kan välja att använda antingen optimistisk eller pessimistisk samtidighets modell för att hantera åtkomst till blobbar och behållare. Om du inte uttryckligen anger en strategi är den senaste skrivarens senaste skrivare som standard.  

## <a name="optimistic-concurrency"></a>Optimistisk samtidighet

Azure Storage tilldelar en identifierare till varje objekt som lagras. Den här identifieraren uppdateras varje gång en Skriv åtgärd utförs för ett objekt. Identifieraren returneras till klienten som en del av ett HTTP GET-svar i ETag-rubriken som definieras av HTTP-protokollet.

En klient som utför en uppdatering kan skicka den ursprungliga ETag-filen tillsammans med en villkors rubrik för att säkerställa att en uppdatering endast sker om ett visst villkor har uppfyllts. Om till exempel rubriken **If-Match** har angetts kontrollerar Azure Storage att värdet för etag som anges i uppdaterings förfrågan är detsamma som etag för objektet som uppdateras. Mer information om villkorliga huvuden finns i [ange villkorliga rubriker för BLOB service åtgärder](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations).

Den här processens översikt är följande:  

1. Hämta en BLOB från Azure Storage. Svaret innehåller ett HTTP ETag-huvud som identifierar den aktuella versionen av objektet.
1. När du uppdaterar blobben inkluderar du ETag-värdet som du fick i steg 1 i villkors rubriken **If-Match** i Write-begäran. Azure Storage jämför ETag-värdet i begäran med det aktuella ETag-värdet för blobben.
1. Om blobens aktuella ETag-värde skiljer sig från det som anges i villkors rubriken **If-Match** som anges i begäran, returnerar Azure Storage HTTP-statuskod 412 (förhands villkor misslyckades). Det här felet anger att klienten som en annan process har uppdaterat blobben sedan klienten först hämtade den.
1. Om det aktuella ETag-värdet för blobben har samma version som ETag i villkors rubriken **If-Match** i begäran, Azure Storage utföra den begärda åtgärden och uppdaterar det aktuella etag-värdet för blobben.  

Följande kod exempel visar hur du skapar ett **If-Match-** villkor på den skrivbegäran som kontrollerar etag-värdet för en blob. Azure Storage utvärderar om blobens aktuella ETag är samma som den ETag som anges på begäran och utför Skriv åtgärden endast om de två ETag-värdena matchar. Om en annan process har uppdaterat blobben i Interim, returnerar Azure Storage ett HTTP 412 (Precondition Failed) status meddelande.  

# <a name="net-v12"></a>[\.NET-V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Concurrency.cs" id="Snippet_DemonstrateOptimisticConcurrencyBlob":::

# <a name="net-v11"></a>[\.NET-v11](#tab/dotnetv11)

```csharp
public void DemonstrateOptimisticConcurrencyBlob(string containerName, string blobName)
{
    Console.WriteLine("Demonstrate optimistic concurrency");

    // Parse connection string and create container.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExists();

    // Create test blob. The default strategy is last writer wins, so
    // write operation will overwrite existing blob if present.
    CloudBlockBlob blockBlob = container.GetBlockBlobReference(blobName);
    blockBlob.UploadText("Hello World!");

    // Retrieve the ETag from the newly created blob.
    string originalETag = blockBlob.Properties.ETag;
    Console.WriteLine("Blob added. Original ETag = {0}", originalETag);

    /// This code simulates an update by another client.
    string helloText = "Blob updated by another client.";
    // No ETag was provided, so original blob is overwritten and ETag updated.
    blockBlob.UploadText(helloText);
    Console.WriteLine("Blob updated. Updated ETag = {0}", blockBlob.Properties.ETag);

    // Now try to update the blob using the original ETag value.
    try
    {
        Console.WriteLine(@"Attempt to update blob using original ETag
                            to generate if-match access condition");
        blockBlob.UploadText(helloText, accessCondition: AccessCondition.GenerateIfMatchCondition(originalETag));
    }
    catch (StorageException ex)
    {
        if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        {
            Console.WriteLine(@"Precondition failure as expected.
                                Blob's ETag does not match.");
        }
        else
        {
            throw;
        }
    }
    Console.WriteLine();
}
```

---

Azure Storage stöder också andra villkorliga huvuden, inklusive som **If-Modified-sedan**, **IF-Unmodified-** on och **If-None-Match**. Mer information finns i [ange villkorliga huvuden för BLOB service-åtgärder](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations).  

## <a name="pessimistic-concurrency-for-blobs"></a>Pessimistisk samtidighet för blobbar

Om du vill låsa en BLOB för exklusiv användning kan du skaffa ett lån på den. När du skaffar lånet anger du varaktigheten för lånet. Ett begränsat lån kan vara giltigt mellan 15 och 60 sekunder. Ett lån kan också vara oändligt, vilket uppgår till ett exklusivt lås. Du kan förnya ett begränsat lån för att utöka det och du kan frigöra lånet när du är klar med det. Azure Storage automatiskt frigör begränsade lån när de upphör att gälla.  

Lån gör det möjligt att stödja olika typer av synkronisering, inklusive exklusiva Skriv-/delade Läs åtgärder, exklusivt Skriv-/exklusivt Läs åtgärder och delade Skriv-/exklusivt Läs åtgärder. När ett lån föreligger, Azure Storage tillämpar exklusiv åtkomst till Skriv åtgärder för låne tagaren. Att se till att exklusivitet för Läs åtgärder kräver att utvecklaren ser till att alla klient program använder ett låne-ID och att endast en klient i taget har ett giltigt låne-ID. Läs åtgärder som inte innehåller ett låne-ID resulterar i delade läsningar.  

I följande kod exempel visas hur du skaffar ett exklusivt lån på en BLOB, uppdaterar innehållet i blobben genom att ange låne-ID och sedan släppa lånet. Om lånet är aktivt och låne-ID: t inte anges för en skrivbegäran, Miss lyckas Skriv åtgärden med felkoden 412 (förhands villkoret misslyckades).  

# <a name="net-v12"></a>[\.NET-V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Concurrency.cs" id="Snippet_DemonstratePessimisticConcurrencyBlob":::

# <a name="net-v11"></a>[\.NET-v11](#tab/dotnetv11)

```csharp
public void DemonstratePessimisticConcurrencyBlob(string containerName, string blobName)
{
    Console.WriteLine("Demonstrate pessimistic concurrency");

    // Parse connection string and create container.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExists();

    CloudBlockBlob blockBlob = container.GetBlockBlobReference(blobName);
    blockBlob.UploadText("Hello World!");
    Console.WriteLine("Blob added.");

    // Acquire lease for 15 seconds.
    string lease = blockBlob.AcquireLease(TimeSpan.FromSeconds(15), null);
    Console.WriteLine("Blob lease acquired. Lease = {0}", lease);

    // Update blob using lease. This operation should succeed.
    const string helloText = "Blob updated";
    var accessCondition = AccessCondition.GenerateLeaseCondition(lease);
    blockBlob.UploadText(helloText, accessCondition: accessCondition);
    Console.WriteLine("Blob updated using an exclusive lease");

    // Simulate another client attempting to update to blob without providing lease.
    try
    {
        // Operation will fail as no valid lease was provided.
        Console.WriteLine("Now try to update blob without valid lease.");
        blockBlob.UploadText("Update operation will fail without lease.");
    }
    catch (StorageException ex)
    {
        if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        {
            Console.WriteLine(@"Precondition failure error as expected.
                                Blob lease not provided.");
        }
        else
        {
            throw;
        }
    }

    // Release lease proactively.
    blockBlob.ReleaseLease(accessCondition);
    Console.WriteLine();
}
```

---

## <a name="pessimistic-concurrency-for-containers"></a>Pessimistisk samtidighet för behållare

Lån i behållare möjliggör samma synkroniseringspartner som stöds för blobbar, inklusive exklusiv Skriv-/delad läsning, exklusiv skrivning/exklusiv läsning och delad Skriv-/exklusiv läsning. För behållare tillämpas dock det exklusiva låset bara på Delete-åtgärder. Om du vill ta bort en behållare med ett aktivt lån måste en klient inkludera det aktiva låne-ID: t med begäran om borttagning. Alla andra container åtgärder kommer att lyckas på en lånad behållare utan låne-ID.  

## <a name="next-steps"></a>Nästa steg

* [Ange villkors rubriker för Blob Service åtgärder](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations)
* [Lease container](/rest/api/storageservices/lease-container)
* [Låna blob](/rest/api/storageservices/lease-blob)
