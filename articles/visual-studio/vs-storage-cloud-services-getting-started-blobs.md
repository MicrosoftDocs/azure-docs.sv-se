---
title: Kom igång med Blob Storage med hjälp av Visual Studio (Cloud Services)
description: Komma igång med Azure Blob Storage i ett moln tjänst projekt i Visual Studio efter anslutning till ett lagrings konto med hjälp av Visual Studio Connected Services
services: storage
author: ghogen
manager: jillfra
ms.assetid: 1144a958-f75a-4466-bb21-320b7ae8f304
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure, devx-track-csharp
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 89e0d6873ebfd8f8396c36185730c57a66af0dd9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96007057"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Kom igång med Azure Blob Storage-och Visual Studio-anslutna tjänster (Cloud Services-projekt)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Översikt
Den här artikeln beskriver hur du kommer igång med Azure Blob Storage när du har skapat eller refererat till ett Azure Storage-konto med hjälp av Visual Studio dialog rutan **Lägg till anslutna tjänster** i ett projekt i Visual Studio Cloud Services. Vi visar dig hur du får åtkomst till och skapar BLOB-behållare och hur du utför vanliga åtgärder som att ladda upp, lista och hämta blobbar. Exemplen skrivs i C \# och använder [Microsoft Azure Storage klient biblioteket för .net](/previous-versions/azure/dn261237(v=azure.100)).

Azure Blob Storage är en tjänst för att lagra stora mängder ostrukturerade data som kan nås från var som helst i världen via HTTP eller HTTPS. En enda BLOB kan vara vilken storlek som helst. Blobbar kan vara saker som bilder, ljud-och videofiler, rå data och dokument-filer.

Precis som filer Live i mappar, Storage blobbar i behållare. När du har skapat ett lagrings utrymme skapar du en eller flera behållare i lagrings utrymmet. I ett lagrings utrymme med namnet "klipp bok" kan du till exempel skapa behållare i lagrings utrymmet som kallas "bilder" för att lagra bilder och en annan som kallas "ljud" för att lagra ljudfiler. När du har skapat behållarna kan du överföra enskilda BLOB-filer till dem.

* Mer information om program mässig manipulering av blobbar finns i [Kom igång med Azure Blob Storage med hjälp av .net](../storage/blobs/storage-quickstart-blobs-dotnet.md).
* Allmän information om Azure Storage finns i [lagrings dokumentation](https://azure.microsoft.com/documentation/services/storage/).
* Allmän information om Azure Cloud Services finns i [Cloud Services dokumentation](https://azure.microsoft.com/documentation/services/cloud-services/).
* Mer information om programmering av ASP.NET-program finns i [ASP.net](https://www.asp.net).

## <a name="access-blob-containers-in-code"></a>Komma åt BLOB-behållare i kod
För att program mässigt få åtkomst till blobbar i moln tjänst projekt måste du lägga till följande objekt, om de inte redan är närvarande.

1. Lägg till följande kod namns deklarationer högst upp i C#-filen som du vill ha åtkomst till Azure Storage via programmering.
   
    ```csharp
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```
2. Hämta ett **CloudStorageAccount** -objekt som representerar lagrings konto informationen. Använd följande kod för att hämta lagrings anslutnings strängen och lagrings konto informationen från Azure-tjänstekonfigurationen.
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("<storage account name>_AzureStorageConnectionString"));
    ```
3. Hämta ett **CloudBlobClient** -objekt för att referera till en befintlig behållare i ditt lagrings konto.
   
    ```csharp
    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```
4. Hämta ett **CloudBlobContainer** -objekt för att referera till en angiven BLOB-behållare.
   
    ```csharp
    // Get a reference to a container named "mycontainer."
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    ```

> [!NOTE]
> Använd all kod som visas i föregående procedur framför koden som visas i följande avsnitt.
> 
> 

## <a name="create-a-container-in-code"></a>Skapa en behållare i kod
> [!NOTE]
> Vissa API: er som utför anrop till Azure Storage i ASP.NET är asynkrona. Se [asynkron programmering med async och await](/previous-versions/hh191443(v=vs.140)) för mer information. Koden i följande exempel förutsätter att du använder asynkrona programmerings metoder.
> 
> 

Om du vill skapa en behållare i ditt lagrings konto behöver du bara lägga till ett anrop till **CreateIfNotExistsAsync** som i följande kod:

```csharp
// If "mycontainer" doesn't exist, create it.
await container.CreateIfNotExistsAsync();
```


Om du vill göra filerna i behållaren tillgängliga för alla kan du ange att behållaren ska vara offentlig med hjälp av följande kod.

```csharp
await container.SetPermissionsAsync(new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
});
```


Alla på Internet kan se blobbar i en offentlig behållare, men du kan bara ändra eller ta bort dem om du har rätt åtkomst nyckel.

## <a name="upload-a-blob-into-a-container"></a>Ladda upp en blob till en container
Azure Storage stöder block-blobbar och Page blobbar. I de flesta fall är blockblobbar den rekommenderade typen.

Om du vill ladda upp en fil till en blockblob hämtar du en referens för containern och använder den för att hämta en referens för blockbloben. När du har en blobbreferens kan du ladda upp en dataström till den genom att anropa metoden **UploadFromStream**. Den här åtgärden skapar blobben om den inte redan fanns, eller skriver över den om den finns. Följande exempel visar hur du laddar upp en blob till en container och förutsätter att containern redan hade skapats.

```csharp
// Retrieve a reference to a blob named "myblob".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

// Create or overwrite the "myblob" blob with contents from a local file.
using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
{
    blockBlob.UploadFromStream(fileStream);
}
```

## <a name="list-the-blobs-in-a-container"></a>Visa blobar i en container
Om du vill visa blobar i en container börjar du med att hämta en referens för containern. Sedan kan du använda containerns **ListBlobs**-metod för att hämta blobarna och/eller katalogerna i den. För att få åtkomst till den omfattande uppsättningen med egenskaper och metoder för en returnerad **IListBlobItem**, måste du omvandla den till ett **CloudBlockBlob**-, **CloudPageBlob**-eller **CloudBlobDirectory** -objekt. Om typen är okänd kan du använda en typkontroll för att avgöra till vilket objekt den ska skickas. Följande kod visar hur du hämtar och returnerar URI:n för varje objekt i **photos**-containern:

```csharp
// Loop over items within the container and output the length and URI.
foreach (IListBlobItem item in container.ListBlobs(null, false))
{
    if (item.GetType() == typeof(CloudBlockBlob))
    {
        CloudBlockBlob blob = (CloudBlockBlob)item;

        Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);

    }
    else if (item.GetType() == typeof(CloudPageBlob))
    {
        CloudPageBlob pageBlob = (CloudPageBlob)item;

        Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);

    }
    else if (item.GetType() == typeof(CloudBlobDirectory))
    {
        CloudBlobDirectory directory = (CloudBlobDirectory)item;

        Console.WriteLine("Directory: {0}", directory.Uri);
    }
}
```

Som det visas i föregående kod exempel har Blob-tjänsten även begreppet kataloger i behållare. Detta är så att du kan organisera dina blobbar i en mer mappstruktur. Titta exempelvis på följande uppsättning blockblobar i en container med namnet **photos**:

```output
photo1.jpg
2010/architecture/description.txt
2010/architecture/photo3.jpg
2010/architecture/photo4.jpg
2011/architecture/photo5.jpg
2011/architecture/photo6.jpg
2011/architecture/description.txt
2011/photo7.jpg
```

När du anropar **ListBlobs** i behållaren (som i föregående exempel) innehåller samlingen som returneras **CloudBlobDirectory** och **CloudBlockBlob** objekt som representerar de kataloger och blobbar som finns på den översta nivån. Här är resultatet av utdata:

```output
Directory: https://<accountname>.blob.core.windows.net/photos/2010/
Directory: https://<accountname>.blob.core.windows.net/photos/2011/
Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg
```


Om du vill kan du ange parametern **UseFlatBlobListing** för **ListBlobs**-metoden till **True**. Detta resulterar i att varje BLOB returneras som en **CloudBlockBlob**, oavsett katalog. Här är anropet till **ListBlobs**:

```csharp
// Loop over items within the container and output the length and URI.
foreach (IListBlobItem item in container.ListBlobs(null, true))
{
    ...
}
```

Här är resultatet:

```output
Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg
```

Mer information finns i [CloudBlobContainer. ListBlobs](/rest/api/storageservices/List-Blobs).

## <a name="download-blobs"></a>Ladda ned blobbar
Om du vill ladda ned blobbar börjar du med att hämta en blobbreferens och anropar sedan **DownloadToStream**-metoden. I följande exempel används metoden **DownloadToStream** för att överföra blobbinnehållet till ett dataströmsobjekt som du sedan kan spara till en lokal fil.

```csharp
// Get a reference to a blob named "photo1.jpg".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

// Save blob contents to a file.
using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
{
    blockBlob.DownloadToStream(fileStream);
}
```

Du kan också använda metoden **DownloadToStream** för att ladda ned innehållet i en blobb som en textsträng.

```csharp
// Get a reference to a blob named "myblob.txt"
CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

string text;
using (var memoryStream = new MemoryStream())
{
    blockBlob2.DownloadToStream(memoryStream);
    text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
}
```

## <a name="delete-blobs"></a>Ta bort blobar
Om du vill ta bort en BLOB måste du först skaffa en BLOB-referens och sedan anropa **Delete** -metoden.

```csharp
// Get a reference to a blob named "myblob.txt".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

// Delete the blob.
blockBlob.Delete();
```


## <a name="list-blobs-in-pages-asynchronously"></a>Visa en lista över blobbar på sidor asynkront
Om du visar ett stort antal blobbar, eller om du vill styra hur många resultat som returneras i samma åtgärd, kan du visa blobbar på resultatsidor. Det här exemplet illustrerar hur du returnerar resultat på sidor asynkront, så att körningen inte blockeras när ett stort antal resultat väntar på att returneras.

I det här exemplet returneras en platt blobblista, men du kan också visa en hierarkiskt ordnad lista genom att ange **useFlatBlobListing**-parametern för **ListBlobsSegmentedAsync**-metoden till **false**.

Eftersom exempelmetoden anropar en asynkron metod måste den föregås av nyckelordet **async** och returnera ett **Task**-objekt. Nyckelordet await som angetts för **ListBlobsSegmentedAsync**-metoden pausar körningen av exempelmetoden tills listuppgiften har slutförts.

```csharp
async public static Task ListBlobsSegmentedInFlatListing(CloudBlobContainer container)
{
    // List blobs to the console window, with paging.
    Console.WriteLine("List blobs in pages:");

    int i = 0;
    BlobContinuationToken continuationToken = null;
    BlobResultSegment resultSegment = null;

    // Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
    // When the continuation token is null, the last page has been returned and execution can exit the loop.
    do
    {
        // This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
        // or by calling a different overload.
        resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);
        if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
        foreach (var blobItem in resultSegment.Results)
        {
            Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
        }
        Console.WriteLine();

        //Get the continuation token.
        continuationToken = resultSegment.ContinuationToken;
    }
    while (continuationToken != null);
}
```

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]