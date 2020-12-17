---
title: Översikt över Azure Page blobbar | Microsoft Docs
description: En översikt över Azure Page blobbar och deras fördelar, inklusive användnings fall med exempel skript.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/15/2020
ms.author: tamram
ms.reviewer: wielriac
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: aada418b4f74c38a2a35c793deb85b94b703fb89
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97629365"
---
# <a name="overview-of-azure-page-blobs"></a>Översikt över Azure Page blobbar

Azure Storage erbjuder tre typer av Blob Storage: block-Blobs, bifoga blobbar och Page blobbar. Block-blobar består av block och är idealiska för att lagra text eller binära filer, och för att ladda upp stora filer effektivt. Tillägg av blobbar består också av block, men de är optimerade för att lägga till åtgärder, vilket gör dem perfekta för loggnings scenarier. Page blobbar består av 512 byte-sidor upp till 8 TB i Total storlek och är utformade för frekventa slumpmässiga Läs/skriv-åtgärder. Page blobbar är grunden för Azure IaaS-diskar. Den här artikeln fokuserar på att förklara funktionerna och fördelarna med Page blobbar.

Page blobbar är en samling med sidor på 512 byte, som ger möjlighet att läsa/skriva valfria intervall med byte. Därför är Page blobbar idealiska för lagring av indexbaserade och glesa data strukturer som operativ system och data diskar för Virtual Machines och databaser. Azure SQL DB använder till exempel sid-blobbar som underliggande beständig lagring för dess databaser. Dessutom används även sid-blobar ofta för filer med Range-Based uppdateringar.  

Viktiga funktioner i Azure Page blobbar är dess REST-gränssnitt, hållbarheten hos det underliggande lagrings utrymmet och de sömlösa funktionerna för migrering till Azure. Dessa funktioner beskrivs närmare i nästa avsnitt. Dessutom stöds Azure Page blobbar för närvarande på två typer av lagring: Premium Storage och standard lagring. Premium Storage är särskilt utformad för arbets belastningar som kräver konsekvent hög prestanda och låg latens för att göra Premium-sid-blobar perfekta för högpresterande lagrings scenarier. Standard lagrings konton är mer kostnads effektiva för att köra svars känsliga arbets belastningar.

## <a name="restrictions"></a>Begränsningar

Page blobbar kan bara använda **frekvent åtkomst nivå** , de kan inte använda antingen låg frekvent **lagring** eller **Arkiv** lag rings nivå. Mer information om åtkomst nivåer finns i [åtkomst nivåer för Azure Blob Storage – frekvent,](storage-blob-storage-tiers.md)låg frekvent och Arkiv lag ring.

## <a name="sample-use-cases"></a>Exempel på användningsfall

Låt oss diskutera några användnings fall för sid-blobar som börjar med Azure IaaS-diskar. Azure Page blobbar är stamnätet för Virtual disks-plattformen för Azure IaaS. Både Azure OS och data diskar implementeras som virtuella diskar där data varaktigt bevaras i Azure Storage-plattformen och sedan levereras till de virtuella datorerna för högsta prestanda. Azure-diskar sparas i [VHD-format](/previous-versions/windows/it-pro/windows-7/dd979539(v=ws.10)) för Hyper-V och lagras som en [sid-BLOB](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs) i Azure Storage. Förutom att använda virtuella diskar för virtuella Azure IaaS-datorer, aktiverar Page blobbar också PaaS-och DBaaS-scenarier som Azure SQL DB-tjänsten, som för närvarande använder Page blobbar för att lagra SQL-data, vilket möjliggör snabba, slumpmässiga Läs-och skriv åtgärder för-databasen. Ett annat exempel är om du har en PaaS-tjänst för delad medie åtkomst för samverkande video redigerings program, kan du använda Page blobbar för snabb åtkomst till slumpmässiga platser i mediet. Det ger också snabb och effektiv redigering och sammanfogning av samma media av flera användare. 

Första parts Microsoft-tjänster som Azure Site Recovery, Azure Backup, och många utvecklare av tredje part har implementerat branschledande innovationer med hjälp av Page blobs REST-gränssnitt. Här följer några av de unika scenarier som implementeras i Azure: 

* Programdirigerad stegvis ögonblicks bilds hantering: program kan dra nytta av ögonblicks bilder av sid-blob och REST-API: er för att spara program kontroll punkter utan att det påverkar kostsam dubblering av data. Azure Storage stöder lokala ögonblicks bilder för sid-blobar som inte kräver att hela blobben kopieras. Dessa API: er för offentliga ögonblicks bilder ger också till gång till och kopiering av delta mellan ögonblicks bilder.
* Direktmigrering av program och data från lokalt till molnet: Kopiera lokala data och Använd REST-API: er för att skriva direkt till en Azure Page-BLOB medan den lokala virtuella datorn fortsätter att köras. När målet har skapats kan du snabbt redundansväxla till virtuell Azure-dator med dessa data. På så sätt kan du migrera dina virtuella datorer och virtuella diskar från lokalt till molnet med minimal stillestånds tid eftersom datamigreringen sker i bakgrunden medan du fortsätter att använda den virtuella datorn och avbrotts tiden som krävs för redundans kommer att vara kort (i minuter).
* [SAS-baserad](../common/storage-sas-overview.md) delad åtkomst, som möjliggör scenarier som flera läsare och en enskild skrivare med stöd för samtidiga kontroller.

## <a name="pricing"></a>Prissättning

Båda typerna av lagring som erbjuds med Page blobbar har sin egen pris modell. Premium Page-blobbar följer pris modellen för Managed disks, medan standard sid blobbar faktureras på använt storlek och för varje transaktion. Mer information finns på sidan med [priser för Azure Page blobbar](https://azure.microsoft.com/pricing/details/storage/page-blobs/).

## <a name="page-blob-features"></a>Funktioner för sidblob

### <a name="rest-api"></a>REST-API

Se följande dokument för att komma igång med att [utveckla med hjälp av Page blobbar](./storage-quickstart-blobs-dotnet.md). Du kan till exempel titta på hur du kommer åt sid-blobbar med lagrings klient bibliotek för .NET. 

Följande diagram beskriver de övergripande relationerna mellan konto, behållare och sid-blobbar.

![Skärm bild som visar relationer mellan konton, behållare och sid-blobbar](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure1.png)

#### <a name="creating-an-empty-page-blob-of-a-specified-size"></a>Skapa en tom Page-BLOB med en angiven storlek

# <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

Börja med att hämta en referens till en behållare. Om du vill skapa en sid-BLOB anropar du metoden GetPageBlobClient och anropar sedan metoden [PageBlobClient. Create](/dotnet/api/azure.storage.blobs.specialized.pageblobclient.create) . Överför Max storleken för blobben att skapa. Storleken måste vara en multipel av 512 byte.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_CreatePageBlob":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

För att skapa en Page BLOB skapar vi först ett **CloudBlobClient** -objekt med bas-URI: n för att komma åt Blob Storage för ditt lagrings konto (*pbaccount* i bild 1) tillsammans med **StorageCredentialsAccountAndKey** -objektet, som du ser i följande exempel. Exemplet visar sedan hur du skapar en referens till ett **CloudBlobContainer** -objekt och sedan skapar behållaren (*testvhds*) om den inte redan finns. Sedan använder du **CloudBlobContainer** -objektet och skapar en referens till ett **CloudPageBlob** -objekt genom att ange det sid-BLOB-namn (OS4. VHD) som ska användas. Om du vill skapa en sid-BLOB anropar du [CloudPageBlob. Create](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.create), och skickar den maximala storleken för blobben att skapa. *BlobSize* måste vara en multipel av 512 byte.

```csharp
using Microsoft.Azure;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Blob;

long OneGigabyteAsBytes = 1024 * 1024 * 1024;
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("testvhds");

// Create the container if it doesn't already exist.
container.CreateIfNotExists();

CloudPageBlob pageBlob = container.GetPageBlobReference("os4.vhd");
pageBlob.Create(16 * OneGigabyteAsBytes);
```

---

#### <a name="resizing-a-page-blob"></a>Ändra storlek på en sid-BLOB

# <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

Om du vill ändra storlek på en sid-BLOB när du har skapat den använder du metoden för att [ändra storlek](/dotnet/api/azure.storage.blobs.specialized.pageblobclient.resize) . Den begärda storleken måste vara en multipel av 512 byte.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ResizePageBlob":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

Om du vill ändra storlek på en sid-BLOB när du har skapat den använder du metoden för att [ändra storlek](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.resize) . Den begärda storleken måste vara en multipel av 512 byte.

```csharp
pageBlob.Resize(32 * OneGigabyteAsBytes);
```

---

#### <a name="writing-pages-to-a-page-blob"></a>Skriva sidor till en sid-BLOB

# <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

Använd metoden [PageBlobClient. UploadPages](/dotnet/api/azure.storage.blobs.specialized.pageblobclient.uploadpages) för att skriva sidor.  

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_WriteToPageBlob":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

Använd metoden [CloudPageBlob. WritePages](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.beginwritepages) för att skriva sidor.  

```csharp
pageBlob.WritePages(dataStream, startingOffset); 
```

---

På så sätt kan du skriva en sekventiell uppsättning sidor upp till 4MBs. Den förskjutning som skrivs till måste starta på en 512 byte-gränser (startingOffset %512 = = 0) och sluta på en 512-gränser-1. 

Så snart en skrivbegäran för en sekventiell uppsättning sidor lyckas i Blob-tjänsten och replikeras för hållbarhet och återhämtning, så har skrivningen genomförts och slutförs tillbaka till klienten.  

Diagrammet nedan visar 2 separata Skriv åtgärder:

![Ett diagram som visar de två separata Skriv alternativen.](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure2.png)

1.  En Skriv åtgärd som börjar vid förskjutningen 0 med längden 1024 byte 
2.  En Skriv åtgärd som börjar vid förskjutningen 4096 med längden 1024 

#### <a name="reading-pages-from-a-page-blob"></a>Läser sidor från en sid-BLOB

# <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

Om du vill läsa sidor använder du metoden [PageBlobClient. Download](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.download) för att läsa ett antal byte från Page blob. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ReadFromPageBlob":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

Om du vill läsa sidor använder du metoden [CloudPageBlob. DownloadRangeToByteArray](/dotnet/api/microsoft.azure.storage.blob.icloudblob.downloadrangetobytearray) för att läsa ett antal byte från Page blob. 

```csharp
byte[] buffer = new byte[rangeSize];
pageBlob.DownloadRangeToByteArray(buffer, bufferOffset, pageBlobOffset, rangeSize); 
```

---

På så sätt kan du ladda ned hela blobben eller ett intervall med byte som börjar med en offset i blobben. Vid läsningen behöver inte förskjutningen starta på en multipel av 512. När du läser byte från en NUL-sida returnerar tjänsten noll byte.

Följande bild visar en Läs åtgärd med en förskjutning på 256 och en intervall storlek på 4352. Returnerade data är markerade i orange. Nollor returneras för NUL-sidor.

![Ett diagram som visar en Läs åtgärd med en förskjutning på 256 och en intervall storlek på 4352](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure3.png)

Om du har en sparse-ifylld BLOB kanske du bara vill hämta giltiga sid regioner för att undvika att betala för utgående av noll byte och för att minska hämtnings fördröjningen.  

# <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

För att avgöra vilka sidor som backas upp av data, Använd [PageBlobClient. GetPageRanges](/dotnet/api/azure.storage.blobs.specialized.pageblobclient.getpageranges). Du kan sedan räkna upp de returnerade intervallen och hämta data i varje intervall. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ReadValidPageRegionsFromPageBlob":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

För att avgöra vilka sidor som backas upp av data, Använd [CloudPageBlob. GetPageRanges](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.getpageranges). Du kan sedan räkna upp de returnerade intervallen och hämta data i varje intervall. 

```csharp
IEnumerable<PageRange> pageRanges = pageBlob.GetPageRanges();

foreach (PageRange range in pageRanges)
{
    // Calculate the range size
    int rangeSize = (int)(range.EndOffset + 1 - range.StartOffset);

    byte[] buffer = new byte[rangeSize];

    // Read from the correct starting offset in the page blob and
    // place the data in the bufferOffset of the buffer byte array
    pageBlob.DownloadRangeToByteArray(buffer, bufferOffset, range.StartOffset, rangeSize); 

    // Then use the buffer for the page range just read
}
```

---

#### <a name="leasing-a-page-blob"></a>Låna en sid-BLOB

Leasing-BLOB-åtgärden upprättar och hanterar ett lås på en BLOB för Skriv-och borttagnings åtgärder. Den här åtgärden är användbar i scenarier där en sid-BLOB nås från flera klienter för att säkerställa att endast en klient kan skriva till blobben i taget. Azure-diskar utnyttjar exempelvis denna operationella mekanism för att säkerställa att disken endast hanteras av en enda virtuell dator. Längden på låset kan vara mellan 15 och 60 sekunder, eller så kan den vara oändlig. Mer information finns [i dokumentationen.](/rest/api/storageservices/lease-blob)

Förutom rika REST API: er ger sid-blobar också delad åtkomst, hållbarhet och förbättrad säkerhet. Vi kommer att gå igenom dessa förmåner i mer detalj i nästa stycken. 

### <a name="concurrent-access"></a>Samtidig åtkomst

Med hjälp av Page blobbar REST API och dess leasing mekanism kan program få åtkomst till sidans BLOB från flera klienter. Anta till exempel att du behöver bygga en distribuerad moln tjänst som delar lagrings objekt med flera användare. Det kan vara ett webb program som hanterar en stor samling avbildningar till flera användare. Ett alternativ för att implementera detta är att använda en virtuell dator med anslutna diskar. Nack delen med detta inkluderar (i) villkoret att en disk bara kan kopplas till en enda virtuell dator, vilket begränsar skalbarheten, flexibiliteten och ökande risker. Om det finns ett problem med den virtuella datorn eller tjänsten som körs på den virtuella datorn, är avbildningen inte tillgänglig förrän lånet upphör att gälla eller är bruten. och (II) ytterligare kostnad för att ha en IaaS VM. 

Ett alternativt alternativ är att använda sid-blobar direkt via Azure Storage REST API: er. Det här alternativet eliminerar behovet av kostsamma IaaS-VM: ar ger fullständig flexibilitet för direkt åtkomst från flera klienter, vilket fören klar den klassiska distributions modellen genom att eliminera behovet av att ansluta/koppla från diskar och eliminera risken för problem på den virtuella datorn. Och ger samma prestanda nivå för slumpmässiga Läs-och skriv åtgärder som en disk

### <a name="durability-and-high-availability"></a>Hållbarhet och hög tillgänglighet

Både standard-och Premium lagring är varaktig lagring där sid-BLOB-data alltid replikeras för att säkerställa hållbarhet och hög tillgänglighet. Mer information om Azure Storage redundans finns i den här [dokumentationen](../common/storage-redundancy.md). Azure har ständigt levererat tålighet i företags klass för IaaS-diskar och Page blobbar, med en branschledande noll procents [frekvens](https://en.wikipedia.org/wiki/Annualized_failure_rate).

### <a name="seamless-migration-to-azure"></a>Sömlös migrering till Azure

För kunder och utvecklare som är intresserade av att implementera sin egen anpassade säkerhets kopierings lösning erbjuder Azure också stegvisa ögonblicks bilder som bara innehåller delta. Den här funktionen eliminerar kostnaden för den första fullständiga kopian, vilket minskar säkerhets kopierings kostnaden avsevärt. Tillsammans med möjligheten att effektivt läsa och kopiera differentiella data, är det här en annan kraftfull funktion som gör det möjligt ännu fler innovationer från utvecklare, vilket leder till en förstklassig säkerhets kopiering och haveri beredskap (DR) i Azure. Du kan ställa in en egen säkerhets kopierings-eller DR-lösning för dina virtuella datorer på Azure med hjälp av [BLOB-ögonblicksbilder](/rest/api/storageservices/snapshot-blob) tillsammans med [Hämta sid intervall](/rest/api/storageservices/get-page-ranges) API och det [stegvisa Copy BLOB](/rest/api/storageservices/incremental-copy-blob) -API: et, som du kan använda för att enkelt kopiera de stegvisa data för Dr. 

Dessutom har många företag kritiska arbets belastningar som redan körs i lokala data Center. För att migrera arbets belastningen till molnet skulle ett av de viktigaste problemen vara den mängd stillestånd som krävs för att kopiera data och risken för oförutsedda problem efter växlingen. I många fall kan stillestånds tiden vara en Showstopper för migrering till molnet. Med hjälp av Page blobbar REST API, löser Azure problemet genom att aktivera molnbaserad migrering med minimalt avbrott i kritiska arbets belastningar. 

Exempel på hur du tar en ögonblicks bild och hur du återställer en sid-BLOB från en ögonblicks bild finns i artikeln om att [skapa en säkerhets kopierings process med stegvisa ögonblicks bilder](../../virtual-machines/windows/incremental-snapshots.md) .