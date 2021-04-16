---
title: 'Snabbstart: Azure Blob Storage bibliotek v12 – Python'
description: I den här snabbstarten lär du dig hur du använder Azure Blob Storage-klientbibliotek version 12 för Python för att skapa en container och en blob i Blob-lagring (objekt). Du får lära dig hur du hämtar bloben till din lokala dator och hur du visar alla blobar i en container.
author: twooley
ms.author: twooley
ms.date: 01/28/2021
ms.topic: quickstart
ms.service: storage
ms.subservice: blobs
ms.custom:
- devx-track-python
- mode-api
ms.openlocfilehash: 9a6ccbe5482ac8ffe484c3155334cba1b50ba0dc
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535655"
---
# <a name="quickstart-manage-blobs-with-python-v12-sdk"></a>Snabbstart: Hantera blobar med Python v12 SDK

I den här snabbstarten lär du dig att hantera blobar med hjälp av Python. Blobar är objekt som kan innehålla stora mängder text eller binära data, inklusive bilder, dokument, strömmande media och arkivdata. Du laddar upp, laddar ned och listar blobar och skapar och tar bort containrar.

Fler resurser:

* [Referensdokumentation för API](/python/api/azure-storage-blob)
* [Bibliotekskällkod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob)
* [Paket (Python-paketindex)](https://pypi.org/project/azure-storage-blob/)
* [Exempel](../common/storage-samples-python.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Ett Azure Storage-konto. [Skapa ett lagringskonto](../common/storage-account-create.md).
- [Python](https://www.python.org/downloads/) 2.7 eller 3.6+.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>Inrätta

Det här avsnittet går igenom hur du förbereder ett projekt för att arbeta Azure Blob Storage klientbiblioteket v12 för Python.

### <a name="create-the-project"></a>Skapa projektet

Skapa ett Python-program *med namnet blob-quickstart-v12.*

1. I ett konsolfönster (till exempel cmd, PowerShell eller Bash) skapar du en ny katalog för projektet.

    ```console
    mkdir blob-quickstart-v12
    ```

1. Växla till den nyligen skapade *katalogen blob-quickstart-v12.*

    ```console
    cd blob-quickstart-v12
    ```

1. På sidan *blob-quickstart-v12-katalogen* skapar du en annan katalog med namnet *data*. I den här katalogen skapas och lagras blobdatafilerna.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Installera paketet

När du fortfarande är i programkatalogen installerar du Azure Blob Storage-klientbiblioteket för Python-paketet med hjälp av `pip install` kommandot .

```console
pip install azure-storage-blob
```

Det här kommandot installerar Azure Blob Storage-klientbiblioteket för Python-paketet och alla bibliotek som det är beroende av. I det här fallet är det bara Azure-kärnbiblioteket för Python.

### <a name="set-up-the-app-framework"></a>Konfigurera appramverket

Från projektkatalogen:

1. Öppna en ny textfil i kodredigeraren
1. Lägga till `import` instruktioner
1. Skapa strukturen för programmet, inklusive grundläggande undantagshantering

    Här är koden:

    :::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/app_framework.py":::

1. Spara den nya filen som *blob-quickstart-v12.py* i *katalogen blob-quickstart-v12.*

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objektmodell

Azure Blob Storage är optimerad för att lagra enorma mängder ostrukturerade data. Ostrukturerade data är data som inte följer en viss datamodell eller definition, till exempel text eller binära data. I blobblagringen finns tre typer av resurser:

* Lagringskontot
* En container på lagringskontot
* En blob i containern

Följande diagram visar relationen mellan de här resurserna.

![Diagram över blobblagringens arkitektur](./media/storage-blobs-introduction/blob1.png)

Använd följande Python-klasser för att interagera med dessa resurser:

* [BlobServiceClient:](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient)Med `BlobServiceClient` klassen kan du ändra Azure Storage och blobcontainrar.
* [ContainerClient:](/python/api/azure-storage-blob/azure.storage.blob.containerclient)Med `ContainerClient` klassen kan du ändra Azure Storage och deras blobar.
* [BlobClient:](/python/api/azure-storage-blob/azure.storage.blob.blobclient)Med `BlobClient` klassen kan du ändra Azure Storage blobar.

## <a name="code-examples"></a>Kodexempel

Dessa exempelkodfragment visar hur du utför följande uppgifter med Azure Blob Storage klientbibliotek för Python:

* [Hämta anslutningssträngen](#get-the-connection-string)
* [Skapa en container](#create-a-container)
* [Ladda upp blobar till en container](#upload-blobs-to-a-container)
* [Visa blobar i en container](#list-the-blobs-in-a-container)
* [Ladda ned blobbar](#download-blobs)
* [Ta bort en container](#delete-a-container)

### <a name="get-the-connection-string"></a>Hämta anslutningssträngen

Koden nedan hämtar anslutningssträngen för lagringskontot från miljövariabeln som skapades i [avsnittet Konfigurera lagringsanslutningssträngen.](#configure-your-storage-connection-string)

Lägg till den här koden i `try` blocket:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_ConnectionString":::

### <a name="create-a-container"></a>Skapa en container

Bestäm ett namn för den nya containern. Koden nedan lägger till ett UUID-värde i containernamnet för att säkerställa att det är unikt.

> [!IMPORTANT]
> Containernamn måste använda gemener. Mer information om namngivning av containrar och blobar finns i [Namngivning och referens av containrar, blobar och metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Skapa en instans av [klassen BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) genom att anropa [from_connection_string](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#from-connection-string-conn-str--credential-none----kwargs-) metoden. Anropa sedan create_container [för](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#create-container-name--metadata-none--public-access-none----kwargs-) att skapa containern i ditt lagringskonto.

Lägg till den här koden i slutet av `try` blocket:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_CreateContainer":::

### <a name="upload-blobs-to-a-container"></a>Ladda upp blobar till en container

Följande kodfragment:

1. Skapar en lokal katalog för att lagra datafiler.
1. Skapar en textfil i den lokala katalogen.
1. Hämtar en referens till ett [BlobClient-objekt](/python/api/azure-storage-blob/azure.storage.blob.blobclient) [genom att anropa get_blob_client-metoden](/python/api/azure-storage-blob/azure.storage.blob.containerclient#get-blob-client-blob--snapshot-none-) i [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) från [avsnittet Skapa en container.](#create-a-container)
1. Laddar upp den lokala textfilen till [](/python/api/azure-storage-blob/azure.storage.blob.blobclient#upload-blob-data--blob-type--blobtype-blockblob---blockblob----length-none--metadata-none----kwargs-) bloben genom att anropa upload_blob-metoden.

Lägg till den här koden i slutet av `try` blocket:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_UploadBlobs":::

### <a name="list-the-blobs-in-a-container"></a>Visa blobar i en container

Visa en lista över blobarna i containern genom att [anropa list_blobs-metoden.](/python/api/azure-storage-blob/azure.storage.blob.containerclient#list-blobs-name-starts-with-none--include-none----kwargs-) I det här fallet har endast en blob lagts till i containern, så liståtgärden returnerar bara den bloben.

Lägg till den här koden i slutet av `try` blocket:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_ListBlobs":::

### <a name="download-blobs"></a>Ladda ned blobbar

Ladda ned bloben som du skapade tidigare genom att [anropa download_blob-metoden.](/python/api/azure-storage-blob/azure.storage.blob.blobclient#download-blob-offset-none--length-none----kwargs-) Exempelkoden lägger till suffixet "DOWNLOAD" i filnamnet så att du kan se båda filerna i det lokala filsystemet.

Lägg till den här koden i slutet av `try` blocket:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_DownloadBlobs":::

### <a name="delete-a-container"></a>Ta bort en container

Följande kod rensar resurserna som appen skapade genom att ta bort hela containern med [hjälp delete_container](/python/api/azure-storage-blob/azure.storage.blob.containerclient#delete-container---kwargs-) metoden. Du kan också ta bort de lokala filerna om du vill.

Appen pausar för användarindata genom att `input()` anropa innan den tar bort bloben, containern och lokala filer. Kontrollera att resurserna har skapats korrekt innan de tas bort.

Lägg till den här koden i slutet av `try` blocket:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_CleanUp":::

## <a name="run-the-code"></a>Kör koden

Den här appen skapar en testfil i din lokala mapp och laddar upp den till Azure Blob Storage. Exemplet visar sedan blobarna i containern och laddar ned filen med ett nytt namn. Du kan jämföra de gamla och nya filerna.

Gå till katalogen som innehåller *blob-quickstart-v12.py* och kör sedan följande `python` kommando för att köra appen.

```console
python blob-quickstart-v12.py
```

Appens utdata liknar följande exempel:

```output
Azure Blob Storage v12 - Python quickstart sample

Uploading to Azure Storage as blob:
        quickstartcf275796-2188-4057-b6fb-038352e35038.txt

Listing blobs...
        quickstartcf275796-2188-4057-b6fb-038352e35038.txt

Downloading blob to
        ./data/quickstartcf275796-2188-4057-b6fb-038352e35038DOWNLOAD.txt

Press the Enter key to begin clean up

Deleting blob container...
Deleting the local source and downloaded files...
Done
```

Innan du påbörjar rensningsprocessen kontrollerar du *datamappen* för de två filerna. Du kan öppna dem och se att de är identiska.

När du har verifierat filerna trycker du på **Retur för** att ta bort testfilerna och avsluta demonstrationen.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du laddar upp, laddar ned och listar blobar med Python.

Om du vill se Blob Storage-exempelappar fortsätter du till:

> [!div class="nextstepaction"]
> [Azure Blob Storage SDK v12 Python-exempel](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples)

* Mer information finns i Azure Storage [klientbibliotek för Python.](/azure/developer/python/sdk/storage/overview)
* Självstudier, exempel, snabbstarter och annan dokumentation finns i [Azure för Python-utvecklare.](/azure/python/)
