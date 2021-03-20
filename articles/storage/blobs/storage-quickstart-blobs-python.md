---
title: 'Snabb start: Azure Blob Storage Library V12 – python'
description: I den här snabb starten får du lära dig hur du använder Azure-Blob Storage klient bibliotek version 12 för python för att skapa en behållare och en BLOB i blob-lagring (objekt). Du får lära dig hur du hämtar bloben till din lokala dator och hur du visar alla blobar i en container.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/28/2021
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: e315f0f4f7bfff03a659de430e6fe182037f1b8a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99096414"
---
# <a name="quickstart-manage-blobs-with-python-v12-sdk"></a>Snabb start: hantera blobbar med python V12 SDK

I den här snabb starten lär du dig att hantera blobbar med hjälp av python. Blobbar är objekt som kan innehålla stora mängder text eller binära data, inklusive bilder, dokument, strömmande media och Arkiv data. Du laddar upp, laddar ned och listar blobbar och skapar och tar bort behållare.

Fler resurser:

* [Referensdokumentation för API](/python/api/azure-storage-blob)
* [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob)
* [Paket (python-paket index)](https://pypi.org/project/azure-storage-blob/)
* [Exempel](../common/storage-samples-python.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Ett Azure Storage-konto. [Skapa ett lagrings konto](../common/storage-account-create.md).
- [Python](https://www.python.org/downloads/) 2,7, 3,5 eller senare.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>Konfigurera

Det här avsnittet beskriver hur du förbereder ett projekt så att det fungerar med Azure-Blob Storage klient biblioteks V12 för python.

### <a name="create-the-project"></a>Skapa projektet

Skapa ett python-program med namnet *BLOB-snabb start-V12*.

1. Skapa en ny katalog för projektet i ett konsol fönster (till exempel cmd, PowerShell eller bash).

    ```console
    mkdir blob-quickstart-v12
    ```

1. Växla till den nyss skapade *V12-katalogen för BLOB-snabb start* .

    ```console
    cd blob-quickstart-v12
    ```

1. På sidan *BLOB-snabb start-V12-* katalogen skapar du en annan katalog med namnet *data*. Den här katalogen är den plats där BLOB-datafilerna skapas och lagras.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Installera paketet

När du fortfarande är i program katalogen installerar du Azure Blob Storage-klient biblioteket för python-paket med hjälp av `pip install` kommandot.

```console
pip install azure-storage-blob
```

Det här kommandot installerar Azure Blob Storage-klient biblioteket för python-paket och alla bibliotek som det är beroende av. I det här fallet är det bara Azures kärn bibliotek för python.

### <a name="set-up-the-app-framework"></a>Konfigurera app Framework

Från projekt katalogen:

1. Öppna en ny textfil i kod redigeraren
1. Lägg till `import` instruktioner
1. Skapa strukturen för programmet, inklusive grundläggande undantags hantering

    Här är koden:

    :::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/app_framework.py":::

1. Spara den nya filen som *BLOB-QuickStart-V12.py* i katalogen *BLOB-snabb start-V12* .

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objekt modell

Azure Blob Storage optimeras för att lagra enorma mängder ostrukturerade data. Ostrukturerade data är data som inte följer en viss data modell eller definition, till exempel text eller binära data. I blobblagringen finns tre typer av resurser:

* Lagrings kontot
* En container på lagringskontot
* En BLOB i behållaren

Följande diagram visar relationen mellan de här resurserna.

![Diagram över blobblagringens arkitektur](./media/storage-blobs-introduction/blob1.png)

Använd följande python-klasser för att interagera med dessa resurser:

* [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient): `BlobServiceClient` klassen låter dig ändra Azure Storage-resurser och blob-behållare.
* [ContainerClient](/python/api/azure-storage-blob/azure.storage.blob.containerclient): `ContainerClient` klassen låter dig ändra Azure Storage behållare och deras blobbar.
* [BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient): `BlobClient` klassen låter dig ändra Azure Storage blobbar.

## <a name="code-examples"></a>Kodexempel

I de här exempel kods tycken visar vi hur du gör följande uppgifter med Azure Blob Storage-klient biblioteket för python:

* [Hämta anslutningssträngen](#get-the-connection-string)
* [Skapa en container](#create-a-container)
* [Ladda upp blobbar till en behållare](#upload-blobs-to-a-container)
* [Visa blobar i en container](#list-the-blobs-in-a-container)
* [Ladda ned blobbar](#download-blobs)
* [Ta bort en container](#delete-a-container)

### <a name="get-the-connection-string"></a>Hämta anslutningssträngen

I koden nedan hämtas anslutnings strängen för lagrings kontot från den miljö variabel som skapades i avsnittet [Konfigurera din lagrings anslutnings sträng](#configure-your-storage-connection-string) .

Lägg till den här koden inuti `try` blocket:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_ConnectionString":::

### <a name="create-a-container"></a>Skapa en container

Välj ett namn för den nya behållaren. Koden nedan lägger till ett UUID-värde till behållar namnet för att säkerställa att det är unikt.

> [!IMPORTANT]
> Containernamn måste använda gemener. Mer information om namngivning av containrar och blobar finns i [Namngivning och referens av containrar, blobar och metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Skapa en instans av klassen [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) genom att anropa metoden [from_connection_string](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#from-connection-string-conn-str--credential-none----kwargs-) . Anropa sedan [create_container](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#create-container-name--metadata-none--public-access-none----kwargs-) -metoden för att skapa behållaren i ditt lagrings konto.

Lägg till den här koden i slutet av `try` blocket:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_CreateContainer":::

### <a name="upload-blobs-to-a-container"></a>Ladda upp blobbar till en behållare

Följande kodfragment:

1. Skapar en lokal katalog för att lagra filer.
1. Skapar en textfil i den lokala katalogen.
1. Hämtar en referens till ett [BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient) -objekt genom att anropa metoden [get_blob_client](/python/api/azure-storage-blob/azure.storage.blob.containerclient#get-blob-client-blob--snapshot-none-) på [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) från avsnittet [skapa en behållare](#create-a-container) .
1. Överför den lokala text filen till blobben genom att anropa metoden [upload_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#upload-blob-data--blob-type--blobtype-blockblob---blockblob----length-none--metadata-none----kwargs-) .

Lägg till den här koden i slutet av `try` blocket:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_UploadBlobs":::

### <a name="list-the-blobs-in-a-container"></a>Visa blobar i en container

Visa en lista över blobarna i behållaren genom att anropa metoden [list_blobs](/python/api/azure-storage-blob/azure.storage.blob.containerclient#list-blobs-name-starts-with-none--include-none----kwargs-) . I det här fallet har endast en BLOB lagts till i behållaren, så List åtgärden returnerar bara den en blob.

Lägg till den här koden i slutet av `try` blocket:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_ListBlobs":::

### <a name="download-blobs"></a>Ladda ned blobbar

Ladda ned den tidigare skapade blobben genom att anropa metoden [download_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#download-blob-offset-none--length-none----kwargs-) . Exempel koden lägger till suffixet "DOWNLOAD" i fil namnet så att du kan se båda filerna i det lokala fil systemet.

Lägg till den här koden i slutet av `try` blocket:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_DownloadBlobs":::

### <a name="delete-a-container"></a>Ta bort en container

Följande kod rensar resurserna som skapats av appen genom att ta bort hela behållaren med hjälp av metoden [delete_container](/python/api/azure-storage-blob/azure.storage.blob.containerclient#delete-container---kwargs-) . Du kan också ta bort de lokala filerna, om du vill.

Appen pausar indata från användaren genom `input()` att anropa innan den tar bort BLOB, container och lokala filer. Kontrol lera att resurserna har skapats korrekt innan de tas bort.

Lägg till den här koden i slutet av `try` blocket:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_CleanUp":::

## <a name="run-the-code"></a>Kör koden

Den här appen skapar en test fil i din lokala mapp och laddar upp den till Azure Blob Storage. Exemplet visar sedan blobarna i behållaren och laddar ned filen med ett nytt namn. Du kan jämföra de gamla och nya filerna.

Navigera till den katalog som innehåller *BLOB-QuickStart-V12.py* -filen och kör sedan följande `python` kommando för att köra appen.

```console
python blob-quickstart-v12.py
```

Utdata från appen liknar följande exempel:

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

Innan du påbörjar rensnings processen kontrollerar du mappen *data* för de två filerna. Du kan öppna dem och se att de är identiska.

När du har verifierat filerna trycker du på **RETUR** -tangenten för att ta bort testfilerna och slutföra demon.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig att ladda upp, ladda ned och lista blobar med python.

Om du vill se exempel appar för Blob Storage fortsätter du till:

> [!div class="nextstepaction"]
> [Azure Blob Storage SDK V12 python-exempel](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples)

* Mer information finns i [Azure Storage klient bibliotek för python](/azure/developer/python/sdk/storage/overview).
* Självstudier, exempel, snabb starter och annan dokumentation finns på [Azure för python-utvecklare](/azure/python/).
