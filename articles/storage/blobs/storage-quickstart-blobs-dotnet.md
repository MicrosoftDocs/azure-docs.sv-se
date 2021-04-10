---
title: 'Snabb start: Azure Blob Storage Library V12 – .NET'
description: I den här snabb starten får du lära dig hur du använder Azure-Blob Storage klient bibliotek version 12 för .NET för att skapa en behållare och en BLOB i blob-lagring (objekt). Du får lära dig hur du hämtar bloben till din lokala dator och hur du visar alla blobar i en container.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/03/2021
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: 82dcfa0942f21f01424c4744aa0b23f4672a135f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105106725"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-net"></a>Snabb start: Azure Blob Storage V12 för klient bibliotek för .NET

Kom igång med Azure-Blob Storage klient bibliotek V12 för .NET. Azure Blob Storage är Microsofts objekt lagrings lösning för molnet. Följ stegen för att installera paketet och prova exempel koden för grundläggande uppgifter. Blob Storage är optimerad för lagring av enorma mängder ostrukturerade data.

Använd Azure Blob Storage-V12 för klient bibliotek för .NET för att:

* Skapa en container
* Ladda upp en blob till Azure Storage
* Lista alla blobar i en behållare
* Ladda ned blobben till den lokala datorn
* Ta bort en container

Ytterligare resurser:

* [Referensdokumentation för API](/dotnet/api/azure.storage.blobs)
* [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs)
* [Paket (NuGet)](https://www.nuget.org/packages/Azure.Storage.Blobs)
* [Exempel](../common/storage-samples-dotnet.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
* Azure Storage-konto – [skapa ett lagrings konto](../common/storage-account-create.md)
* Nuvarande [.net Core SDK](https://dotnet.microsoft.com/download/dotnet-core) för ditt operativ system. Se till att hämta SDK och inte körnings miljön.

## <a name="setting-up"></a>Konfigurera

Det här avsnittet beskriver hur du förbereder ett projekt för att arbeta med Azure Blob Storage-V12 för .NET.

### <a name="create-the-project"></a>Skapa projektet

Skapa ett .NET Core-program med namnet *BlobQuickstartV12*.

1. I ett konsol fönster (till exempel cmd, PowerShell eller bash) använder du `dotnet new` kommandot för att skapa en ny konsol app med namnet *BlobQuickstartV12*. Det här kommandot skapar ett enkelt "Hello World" C#-projekt med en enda källfil: *program. cs*.

   ```console
   dotnet new console -n BlobQuickstartV12
   ```

1. Växla till den nyligen skapade *BlobQuickstartV12* -katalogen.

   ```console
   cd BlobQuickstartV12
   ```

1. Skapa en annan katalog med namnet *data* på sidan *BlobQuickstartV12* . Det är här som BLOB-datafilerna ska skapas och lagras.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Installera paketet

När du fortfarande är i program katalogen installerar du Azure-Blob Storage klient biblioteket för .NET-paketet med hjälp av `dotnet add package` kommandot.

```console
dotnet add package Azure.Storage.Blobs
```

### <a name="set-up-the-app-framework"></a>Konfigurera app Framework

Från projekt katalogen:

1. Öppna filen *program. cs* i redigeraren.
1. Ta bort `Console.WriteLine("Hello World!");` instruktionen.
1. Lägg till `using` direktiv.
1. Uppdatera `Main` metod deklarationen för att stödja asynkron.

    Här är koden:

    :::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/app_framework.cs":::

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objekt modell

Azure Blob Storage optimeras för att lagra enorma mängder ostrukturerade data. Ostrukturerade data är data som inte följer en viss datamodell eller definition, till exempel text eller binära data. I blobblagringen finns tre typer av resurser:

* Lagrings kontot
* En container på lagringskontot
* En BLOB i behållaren

Följande diagram visar relationen mellan de här resurserna.

![Diagram över blobblagringens arkitektur](./media/storage-blobs-introduction/blob1.png)

Använd följande .NET-klasser för att interagera med dessa resurser:

* [BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient): `BlobServiceClient` klassen låter dig ändra Azure Storage-resurser och blob-behållare.
* [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient): `BlobContainerClient` klassen låter dig ändra Azure Storage behållare och deras blobbar.
* [BlobClient](/dotnet/api/azure.storage.blobs.blobclient): `BlobClient` klassen låter dig ändra Azure Storage blobbar.
* [BlobDownloadInfo](/dotnet/api/azure.storage.blobs.models.blobdownloadinfo): `BlobDownloadInfo` klassen representerar egenskaper och innehåll som returneras från att hämta en blob.

## <a name="code-examples"></a>Kodexempel

I dessa exempel kods tycken visas hur du gör följande med Azure Blob Storage-klient biblioteket för .NET:

* [Hämta anslutningssträngen](#get-the-connection-string)
* [Skapa en container](#create-a-container)
* [Ladda upp blobbar till en behållare](#upload-blobs-to-a-container)
* [Visa blobar i en container](#list-the-blobs-in-a-container)
* [Ladda ned blobbar](#download-blobs)
* [Ta bort en container](#delete-a-container)

### <a name="get-the-connection-string"></a>Hämta anslutningssträngen

Koden nedan hämtar anslutnings strängen för lagrings kontot från den miljö variabel som skapades i avsnittet [Konfigurera din lagrings anslutnings sträng](#configure-your-storage-connection-string) .

Lägg till den här koden i- `Main` metoden:

:::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/Program.cs" id="Snippet_ConnectionString":::

### <a name="create-a-container"></a>Skapa en container

Välj ett namn för den nya behållaren. Koden nedan lägger till ett GUID-värde till behållar namnet för att säkerställa att det är unikt.

> [!IMPORTANT]
> Containernamn måste använda gemener. Mer information om namngivning av containrar och blobar finns i [Namngivning och referens av containrar, blobar och metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Skapa en instans av klassen [BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient) . Anropa sedan [CreateBlobContainerAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.createblobcontainerasync) -metoden för att skapa behållaren i ditt lagrings konto.

Lägg till den här koden i slutet av `Main` metoden:

:::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/Program.cs" id="Snippet_CreateContainer":::

### <a name="upload-blobs-to-a-container"></a>Ladda upp blobbar till en behållare

Följande kodfragment:

1. Skapar en textfil i den lokala *data* katalogen.
1. Hämtar en referens till ett [BlobClient](/dotnet/api/azure.storage.blobs.blobclient) -objekt genom att anropa metoden [GetBlobClient](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobclient) i behållaren från avsnittet [skapa en behållare](#create-a-container) .
1. Överför den lokala text filen till blobben genom att anropa metoden [UploadAsync](/dotnet/api/azure.storage.blobs.blobclient.uploadasync#Azure_Storage_Blobs_BlobClient_UploadAsync_System_IO_Stream_System_Boolean_System_Threading_CancellationToken_) . Den här metoden skapar bloben om den inte redan finns, och skriver över den om den finns.

Lägg till den här koden i slutet av `Main` metoden:

:::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/Program.cs" id="Snippet_UploadBlobs":::

### <a name="list-the-blobs-in-a-container"></a>Visa blobar i en container

Visa en lista över blobarna i behållaren genom att anropa metoden [GetBlobsAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsasync) . I det här fallet har endast en BLOB lagts till i behållaren, så List åtgärden returnerar bara den en blob.

Lägg till den här koden i slutet av `Main` metoden:

:::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/Program.cs" id="Snippet_ListBlobs":::

### <a name="download-blobs"></a>Ladda ned blobbar

Ladda ned den tidigare skapade blobben genom att anropa [DownloadAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.downloadtoasync) -metoden. Exempel koden lägger till suffixet "DOWNLOADed" till fil namnet så att du kan se båda filerna i det lokala fil systemet.

Lägg till den här koden i slutet av `Main` metoden:

:::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/Program.cs" id="Snippet_DownloadBlobs":::

### <a name="delete-a-container"></a>Ta bort en container

Följande kod rensar resurserna som skapats av appen genom att ta bort hela behållaren med hjälp av [DeleteAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.deleteasync). Den tar också bort de lokala filer som skapats av appen.

Appen pausar indata från användaren genom `Console.ReadLine` att anropa innan den tar bort BLOB, container och lokala filer. Det här är en bra chans att verifiera att resurserna faktiskt har skapats korrekt innan de tas bort.

Lägg till den här koden i slutet av `Main` metoden:

:::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/Program.cs" id="Snippet_DeleteContainer":::

## <a name="run-the-code"></a>Kör koden

Den här appen skapar en test fil i din *lokala* datamapp och laddar upp den till Blob Storage. Exemplet visar sedan blobarna i behållaren och laddar ned filen med ett nytt namn så att du kan jämföra de gamla och nya filerna.

Gå till program katalogen och skapa och kör sedan programmet.

```console
dotnet build
```

```console
dotnet run
```

Utdata från appen liknar följande exempel:

```output
Azure Blob Storage v12 - .NET quickstart sample

Uploading to Blob storage as blob:
         https://mystorageacct.blob.core.windows.net/quickstartblobs60c70d78-8d93-43ae-954d-8322058cfd64/quickstart2fe6c5b4-7918-46cb-96f4-8c4c5cb2fd31.txt

Listing blobs...
        quickstart2fe6c5b4-7918-46cb-96f4-8c4c5cb2fd31.txt

Downloading blob to
        ./data/quickstart2fe6c5b4-7918-46cb-96f4-8c4c5cb2fd31DOWNLOADED.txt

Press any key to begin clean up
Deleting blob container...
Deleting the local source and downloaded files...
Done
```

Innan du påbörjar rensnings processen kontrollerar du mappen *data* för de två filerna. Du kan öppna dem och se att de är identiska.

När du har verifierat filerna trycker du på **RETUR** -tangenten för att ta bort testfilerna och slutföra demon.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig att ladda upp, ladda ned och lista blobar med hjälp av .NET.

Om du vill se exempel appar för Blob Storage fortsätter du till:

> [!div class="nextstepaction"]
> [Azure Blob Storage SDK V12 .NET-exempel](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples)

* För självstudier, exempel, snabb starter och annan dokumentation går du till [Azure för .net och .net Core-utvecklare](/dotnet/azure/).
* Mer information om .NET Core finns i [Kom igång med .NET på 10 minuter](https://www.microsoft.com/net/learn/get-started/).
