---
title: 'Snabbstart: Azure Blob Storage bibliotek v12 – .NET'
description: I den här snabbstarten lär du dig hur du använder Azure Blob Storage-klientbiblioteksversion 12 för .NET för att skapa en container och en blob i blobblagring (objekt). Du får lära dig hur du hämtar bloben till din lokala dator och hur du visar alla blobar i en container.
author: twooley
ms.author: twooley
ms.date: 03/03/2021
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: f913b33d0bea425a24d2fd336c9d065978606e82
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869265"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-net"></a>Snabbstart: Azure Blob Storage klientbibliotek v12 för .NET

Kom igång med Azure Blob Storage-klientbiblioteket v12 för .NET. Azure Blob Storage är Microsofts objektlagringslösning för molnet. Följ stegen för att installera paketet och prova exempelkoden för grundläggande uppgifter. Blob Storage är optimerad för lagring av enorma mängder ostrukturerade data.

Använd Azure Blob Storage-klientbiblioteket v12 för .NET för att:

* Skapa en container
* Ladda upp en blob till Azure Storage
* Lista alla blobar i en container
* Ladda ned bloben till din lokala dator
* Ta bort en container

Ytterligare resurser:

* [Referensdokumentation för API](/dotnet/api/azure.storage.blobs)
* [Bibliotekskällkod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs)
* [Paket (NuGet)](https://www.nuget.org/packages/Azure.Storage.Blobs)
* [Exempel](../common/storage-samples-dotnet.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration [– skapa en kostnadsfritt](https://azure.microsoft.com/free/)
* Azure Storage-konto – [skapa ett lagringskonto](../common/storage-account-create.md)
* Aktuella [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) för ditt operativsystem. Se till att hämta SDK och inte körningen.

## <a name="setting-up"></a>Inrätta

Det här avsnittet går igenom hur du förbereder ett projekt för att arbeta Azure Blob Storage klientbiblioteket v12 för .NET.

### <a name="create-the-project"></a>Skapa projektet

Skapa ett .NET Core-program med namnet *BlobQuickstartV12.*

1. I ett konsolfönster (till exempel cmd, PowerShell eller Bash) använder du kommandot för att skapa en ny konsolapp med namnet `dotnet new` *BlobQuickstartV12.* Det här kommandot skapar ett enkelt "Hello World" C#-projekt med en enda källfil: *Program.cs*.

   ```console
   dotnet new console -n BlobQuickstartV12
   ```

1. Växla till den nyligen skapade *BlobQuickstartV12-katalogen.*

   ```console
   cd BlobQuickstartV12
   ```

1. Skapa en annan katalog med namnet *data* på sidan av katalogen *BlobQuickstartV12.* Det är här som blobdatafilerna skapas och lagras.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Installera paketet

När du fortfarande är i programkatalogen installerar du Azure Blob Storage för .NET-paketet med hjälp av `dotnet add package` kommandot .

```console
dotnet add package Azure.Storage.Blobs
```

### <a name="set-up-the-app-framework"></a>Konfigurera appramverket

Från projektkatalogen:

1. Öppna filen *Program.cs* i redigeringsprogrammet.
1. Ta bort `Console.WriteLine("Hello World!");` -instruktionen.
1. Lägg `using` till direktiv.
1. Uppdatera `Main` metoddeklarationen för att stödja asynkronisering.

    Här är koden:

    :::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/app_framework.cs":::

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objektmodell

Azure Blob Storage är optimerad för att lagra enorma mängder ostrukturerade data. Ostrukturerade data är data som inte följer en viss datamodell eller definition, till exempel text eller binära data. I blobblagringen finns tre typer av resurser:

* Lagringskontot
* En container på lagringskontot
* En blob i containern

Följande diagram visar relationen mellan de här resurserna.

![Diagram över blobblagringens arkitektur](./media/storage-blobs-introduction/blob1.png)

Använd följande .NET-klasser för att interagera med dessa resurser:

* [BlobServiceClient:](/dotnet/api/azure.storage.blobs.blobserviceclient)Med `BlobServiceClient` klassen kan du ändra Azure Storage och blobcontainrar.
* [BlobContainerClient:](/dotnet/api/azure.storage.blobs.blobcontainerclient)Med `BlobContainerClient` klassen kan du ändra Azure Storage och deras blobar.
* [BlobClient:](/dotnet/api/azure.storage.blobs.blobclient)Med `BlobClient` klassen kan du ändra Azure Storage blobar.
* [BlobDownloadInfo:](/dotnet/api/azure.storage.blobs.models.blobdownloadinfo)Klassen `BlobDownloadInfo` representerar de egenskaper och innehåll som returneras när en blob laddas ned.

## <a name="code-examples"></a>Kodexempel

De här exempelkodfragmenten visar hur du utför följande med Azure Blob Storage klientbibliotek för .NET:

* [Hämta anslutningssträngen](#get-the-connection-string)
* [Skapa en container](#create-a-container)
* [Ladda upp blobar till en container](#upload-blobs-to-a-container)
* [Visa blobar i en container](#list-the-blobs-in-a-container)
* [Ladda ned blobbar](#download-blobs)
* [Ta bort en container](#delete-a-container)

### <a name="get-the-connection-string"></a>Hämta anslutningssträngen

Koden nedan hämtar anslutningssträngen för lagringskontot från miljövariabeln som skapades i [avsnittet Konfigurera lagringsanslutningssträngen.](#configure-your-storage-connection-string)

Lägg till den här koden i `Main` metoden :

:::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/Program.cs" id="Snippet_ConnectionString":::

### <a name="create-a-container"></a>Skapa en container

Bestäm ett namn för den nya containern. Koden nedan lägger till ett GUID-värde i containernamnet för att säkerställa att det är unikt.

> [!IMPORTANT]
> Containernamn måste använda gemener. Mer information om namngivning av containrar och blobar finns i [Namngivning och referens av containrar, blobar och metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Skapa en instans av [klassen BlobServiceClient.](/dotnet/api/azure.storage.blobs.blobserviceclient) Anropa sedan metoden [CreateBlobContainerAsync för](/dotnet/api/azure.storage.blobs.blobserviceclient.createblobcontainerasync) att skapa containern i ditt lagringskonto.

Lägg till den här koden i slutet av `Main` metoden:

:::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/Program.cs" id="Snippet_CreateContainer":::

### <a name="upload-blobs-to-a-container"></a>Ladda upp blobar till en container

Följande kodfragment:

1. Skapar en textfil i den lokala *datakatalogen.*
1. Hämtar en referens till ett [BlobClient-objekt](/dotnet/api/azure.storage.blobs.blobclient) genom att anropa metoden [GetBlobClient](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobclient) i containern från [avsnittet Skapa en container.](#create-a-container)
1. Laddar upp den lokala textfilen till bloben genom att anropa [metoden UploadAsync.](/dotnet/api/azure.storage.blobs.blobclient.uploadasync#Azure_Storage_Blobs_BlobClient_UploadAsync_System_IO_Stream_System_Boolean_System_Threading_CancellationToken_) Den här metoden skapar bloben om den inte redan finns, och skriver över den om den finns.

Lägg till den här koden i slutet av `Main` metoden:

:::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/Program.cs" id="Snippet_UploadBlobs":::

### <a name="list-the-blobs-in-a-container"></a>Visa blobar i en container

Visa en lista över blobarna i containern genom att anropa [metoden GetBlobsAsync.](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsasync) I det här fallet har endast en blob lagts till i containern, så liståtgärden returnerar bara den enda bloben.

Lägg till den här koden i slutet av `Main` metoden:

:::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/Program.cs" id="Snippet_ListBlobs":::

### <a name="download-blobs"></a>Ladda ned blobbar

Ladda ned bloben som du skapade tidigare genom att anropa [downloadAsync-metoden.](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.downloadtoasync) Exempelkoden lägger till suffixet "DOWNLOADED" i filnamnet så att du kan se båda filerna i det lokala filsystemet.

Lägg till den här koden i slutet av `Main` metoden:

:::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/Program.cs" id="Snippet_DownloadBlobs":::

### <a name="delete-a-container"></a>Ta bort en container

Följande kod rensar de resurser som appen skapade genom att ta bort hela containern med hjälp av [DeleteAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.deleteasync). Den tar även bort de lokala filer som skapats av appen.

Appen pausar för användarindata genom att `Console.ReadLine` anropa innan den tar bort bloben, containern och lokala filer. Det här är en bra chans att kontrollera att resurserna faktiskt har skapats korrekt, innan de tas bort.

Lägg till den här koden i slutet av `Main` metoden:

:::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/Program.cs" id="Snippet_DeleteContainer":::

## <a name="run-the-code"></a>Kör koden

Den här appen skapar en testfil i din lokala *datamapp* och laddar upp den till Blob Storage. Exemplet visar sedan blobarna i containern och laddar ned filen med ett nytt namn så att du kan jämföra de gamla och nya filerna.

Gå till programkatalogen och skapa och kör sedan programmet.

```console
dotnet build
```

```console
dotnet run
```

Appens utdata liknar följande exempel:

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

Kontrollera datamappen för de två filerna innan *du* påbörjar rensningsprocessen. Du kan öppna dem och se att de är identiska.

När du har verifierat filerna trycker du på **Retur för** att ta bort testfilerna och avsluta demonstrationen.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig att ladda upp, ladda ned och lista blobar med hjälp av .NET.

Om du vill se Blob Storage-exempelappar fortsätter du till:

> [!div class="nextstepaction"]
> [Azure Blob Storage SDK v12 .NET-exempel](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples)

* Självstudier, exempel, snabbstarter och annan dokumentation finns i [Azure för .NET- och .NET Core-utvecklare.](/dotnet/azure/)
* Mer information om .NET Core finns i [Kom igång med .NET på 10 minuter](https://dotnet.microsoft.com/learn/dotnet/hello-world-tutorial/intro).
