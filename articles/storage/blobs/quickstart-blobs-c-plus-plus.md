---
title: 'Snabb start: Azure Blob Storage Library V12 – C++'
description: I den här snabb starten får du lära dig hur du använder Azure-Blob Storage klient bibliotek version 12 för C++ för att skapa en behållare och en BLOB i blob-lagring (objekt). Du får lära dig hur du hämtar bloben till din lokala dator och hur du visar alla blobar i en container.
author: twooley
ms.author: twooley
ms.date: 10/21/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 7a0d2bdb1879e4f68b582ee56367f0238285a401
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278090"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-c"></a>Snabb start: Azure Blob Storage klient bibliotek V12 för C++

Kom igång med Azure-Blob Storage klient bibliotek V12 för C++. Azure Blob Storage är Microsofts objekt lagrings lösning för molnet. Följ stegen för att installera paketet och prova exempel koden för grundläggande uppgifter. Blob Storage är optimerad för att lagra enorma mängder ostrukturerade data.

Använd Azure-Blob Storage klient bibliotek V12 för C++ för att:

- Skapa en container
- Ladda upp en blob till Azure Storage
- Lista alla blobar i en behållare
- Ladda ned blobben till den lokala datorn
- Ta bort en container

Resurser:

- [Referensdokumentation för API](https://azure.github.io/azure-sdk-for-cpp/storage.html)
- [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-cpp/tree/master/sdk/storage)
- [Exempel](../common/storage-samples-c-plus-plus.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Förutsättningar

- [Azure-prenumeration](https://azure.microsoft.com/free/)
- [Azure Storage-konto](../common/storage-account-create.md)
- [C++-kompilator](https://azure.github.io/azure-sdk/cpp_implementation.html#supported-platforms)
- [CMake](https://cmake.org/)
- [Vcpkg-C och C++ Package Manager](https://github.com/microsoft/vcpkg/blob/master/docs/index.md)
- [Lib-sväng](https://curl.haxx.se/libcurl/)
- [LibXML2](http://www.xmlsoft.org/)

## <a name="setting-up"></a>Konfigurera

Det här avsnittet beskriver hur du förbereder ett projekt för att arbeta med Azure-Blob Storage klient bibliotek V12 för C++.

### <a name="install-the-packages"></a>Installera paketen

Om du inte redan har gjort det installerar du lib-och LibXML2-paketen med hjälp av `vcpkg install` kommandot.

```console
vcpkg.exe install libxml2:x64-windows curl:x64-windows
```

Följ anvisningarna på GitHub för att hämta och bygga [Azure SDK för C++](https://github.com/Azure/azure-sdk-for-cpp/).

### <a name="create-the-project"></a>Skapa projektet

Skapa ett nytt C++-konsol program för Windows med namnet *BlobQuickstartV12* i Visual Studio.

:::image type="content" source="./media/quickstart-blobs-c-plus-plus/vs-create-project.jpg" alt-text="Visual Studio-dialog ruta för att konfigurera en ny app med Windows-konsolen för C++":::

Lägg till följande bibliotek i projektet:

- libsväng. lib
- libxml2. lib
- bcrypt. lib
- Crypt32. lib
- WS2_32. lib
- Azure-Core. lib
- Azure-Storage-common. lib
- Azure-Storage-blobs. lib

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objekt modell

Azure Blob Storage optimeras för att lagra enorma mängder ostrukturerade data. Ostrukturerade data är data som inte följer en viss data modell eller definition, till exempel text eller binära data. Blob Storage erbjuder tre typer av resurser:

- Lagrings kontot
- En container på lagringskontot
- En BLOB i behållaren

Följande diagram visar relationen mellan de här resurserna.

![Diagram över Blob Storage arkitektur](./media/storage-blobs-introduction/blob1.png)

Använd följande C++-klasser för att interagera med dessa resurser:

- [BlobServiceClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_service_client.html): `BlobServiceClient` klassen låter dig ändra Azure Storage-resurser och blob-behållare.
- [BlobContainerClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html): `BlobContainerClient` klassen låter dig ändra Azure Storage behållare och deras blobbar.
- [BlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_client.html): `BlobClient` klassen låter dig ändra Azure Storage blobbar. Det är Bask Lassen för alla specialiserade BLOB-klasser.
- [BlockBlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_block_blob_client.html): `BlockBlobClient` klassen låter dig ändra Azure Storage block blobbar.

## <a name="code-examples"></a>Kodexempel

I de här exempel kods tycken visar vi hur du gör följande uppgifter med Azure Blob Storage-klient biblioteket för C++:

- [Lägg till inkludera filer](#add-include-files)
- [Hämta anslutningssträngen](#get-the-connection-string)
- [Skapa en container](#create-a-container)
- [Ladda upp blobbar till en behållare](#upload-blobs-to-a-container)
- [Visa blobar i en container](#list-the-blobs-in-a-container)
- [Ladda ned blobbar](#download-blobs)
- [Ta bort en container](#delete-a-container)

### <a name="add-include-files"></a>Lägg till inkludera filer

Från projekt katalogen:

1. Öppna lösnings filen *BlobQuickstartV12. SLN* i Visual Studio
1. Öppna käll filen *BlobQuickstartV12. cpp* i Visual Studio
1. Ta bort all kod inuti `main` som genererades automatiskt
1. Lägg till `#include` instruktioner

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_Includes":::

### <a name="get-the-connection-string"></a>Hämta anslutningssträngen

Koden nedan hämtar anslutnings strängen för ditt lagrings konto från den miljö variabel som skapades i [Konfigurera din lagrings anslutnings sträng](#configure-your-storage-connection-string).

Lägg till den här koden inuti `main()` :

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_ConnectionString":::

### <a name="create-a-container"></a>Skapa en container

Skapa en instans av klassen [BlobContainerClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html) genom att anropa [CreateFromConnectionString](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#ad103d1e3a7ce7c53a82da887d12ce6fe) -funktionen. Anropa sedan [create](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#a22a1e001068a4ec52bb1b6bd8b52c047) för att skapa den faktiska behållaren i ditt lagrings konto.

> [!IMPORTANT]
> Containernamn måste använda gemener. Mer information om namngivning av containrar och blobar finns i [Namngivning och referens av containrar, blobar och metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Lägg till den här koden i slutet av `main()` :

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_CreateContainer":::

### <a name="upload-blobs-to-a-container"></a>Ladda upp blobbar till en behållare

Följande kodfragment:

1. Deklarerar en sträng som innehåller "Hej Azure!".
1. Hämtar en referens till ett [BlockBlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_block_blob_client.html) -objekt genom att anropa [GetBlockBlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#acd8c68e3f37268fde0010dd478ff048f) i behållaren från avsnittet [skapa en behållare](#create-a-container) .
1. Överför strängen till blobben genom att anropa funktionen [UploadFrom](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_block_blob_client.html#af93af7e37f8806e39481596ef253f93d) . Den här funktionen skapar blobben om den inte redan finns, eller uppdaterar den om den gör det.

Lägg till den här koden i slutet av `main()` :

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_UploadBlob":::

### <a name="list-the-blobs-in-a-container"></a>Visa blobar i en container

Visa en lista över blobarna i behållaren genom att anropa funktionen [ListBlobsFlatSegment](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#ac7712bc46909dc061d6bf554b496550c) . Endast en BLOB har lagts till i behållaren, så åtgärden returnerar bara den blobben.

Lägg till den här koden i slutet av `main()` :

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_ListBlobs":::

### <a name="download-blobs"></a>Ladda ned blobbar

Hämta egenskaperna för den överförda blobben. Deklarera och ändra sedan storlek på ett nytt `std::string` objekt med hjälp av egenskaperna för den överförda blobben. Ladda ned den tidigare skapade blobben till det nya `std::string` objektet genom att anropa funktionen [DownloadTo](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_client.html#aa844f37a8c216f3cb0f27912b114c4d2) i Bask Lassen [BlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_client.html) . Slutligen visar du hämtade BLOB-data.

Lägg till den här koden i slutet av `main()` :

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_DownloadBlob":::

### <a name="delete-a-blob"></a>Ta bort en BLOB

Följande kod tar bort blobben från Azure Blob Storage-behållaren genom att anropa funktionen [BlobClient. Delete](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_client.html#a621eabcc8d23893ca1eb106494198615) .

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_DeleteBlob":::

### <a name="delete-a-container"></a>Ta bort en container

Följande kod rensar resurserna som skapats av appen genom att ta bort hela behållaren med hjälp av [BlobContainerClient. Delete](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#aa6b1db52697ae92e9a1227e2e02a5178).

Lägg till den här koden i slutet av `main()` :

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_DeleteContainer":::

## <a name="run-the-code"></a>Kör koden

Den här appen skapar en behållare och överför en textfil till Azure Blob Storage. I exemplet visas sedan en lista över blobarna i behållaren, laddar ned filen och visar filens innehåll. Slutligen tar appen bort blobben och behållaren.

Utdata från appen liknar följande exempel:

```output
Azure Blob Storage v12 - C++ quickstart sample
Creating container: myblobcontainer
Uploading blob: blob.txt
Listing blobs...
Blob name: blob.txt
Downloaded blob contents: Hello Azure!
Deleting blob: blob.txt
Deleting container: myblobcontainer
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig att ladda upp, ladda ned och lista blobar med hjälp av C++. Du har också lärt dig hur du skapar och tar bort en Azure Blob Storage-behållare.

Om du vill se ett C++ Blob Storage-exempel fortsätter du till:

> [!div class="nextstepaction"]
> [Exempel på Azure Blob Storage SDK-V12 för C++](https://github.com/Azure/azure-sdk-for-cpp/tree/master/sdk/storage/azure-storage-blobs/sample)