---
title: Utveckla för Azure Files med python | Microsoft Docs
description: Lär dig hur du utvecklar python-program och-tjänster som använder Azure Files för att lagra fildata.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/08/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-python
ms.openlocfilehash: d45ce3a782d7ee145f769283b82e34647c78f26e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104799878"
---
# <a name="develop-for-azure-files-with-python"></a>Utveckla för Azure Files med Python

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

Lär dig grunderna i att använda python för att utveckla appar eller tjänster som använder Azure Files för att lagra fildata. Skapa en enkel konsol app och lär dig hur du utför grundläggande åtgärder med python och Azure Files:

- Skapa Azure-filresurser
- Skapa kataloger
- Räkna upp filer och kataloger i en Azure-filresurs
- Ladda upp, ladda ned och ta bort en fil
- Skapa säkerhets kopior av fil resurser med hjälp av ögonblicks bilder

> [!NOTE]
> Eftersom Azure Files kan nås via SMB, är det möjligt att skriva enkla program som har åtkomst till Azure-filresursen med hjälp av standard-I/O-klasser och-funktioner. Den här artikeln beskriver hur du skriver appar som använder Azure Files Storage python SDK, som använder [Azure Files REST API](/rest/api/storageservices/file-service-rest-api) för att prata med Azure Files.

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Hämta och installera Azure Storage SDK för python

> [!NOTE]
> Om du uppgraderar från Azure Storage SDK för python version 0,36 eller tidigare, måste du avinstallera den äldre SDK: n med `pip uninstall azure-storage` innan du installerar det senaste paketet.

# <a name="python-v12"></a>[Python-V12](#tab/python)

[Azure File Storage klient biblioteket V12. x för python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-share) kräver python 2,7 eller 3.6 +.

# <a name="python-v2"></a>[Python v2](#tab/python2)

[Azure Storage SDK för python](https://github.com/azure/azure-storage-python) kräver python 2,7 eller 3.6 +.

---

## <a name="install-via-pypi"></a>Installera via PyPI

Om du vill installera via python-paket indexet (PyPI) skriver du:

# <a name="python-v12"></a>[Python-V12](#tab/python)

```console
pip install azure-storage-file-share
```

# <a name="python-v2"></a>[Python v2](#tab/python2)

```console
pip install azure-storage-file
```

### <a name="view-the-sample-application"></a>Visa exempel programmet

Om du vill visa och köra ett exempel program som visar hur du använder python med Azure Files, se [Azure Storage: komma igång med Azure Files i python](https://github.com/Azure-Samples/storage-file-python-getting-started).

Kontrol lera att du har installerat både-och-paketen för att köra exempel programmet `azure-storage-file` `azure-storage-common` .

---

## <a name="set-up-your-application-to-use-azure-files"></a>Konfigurera ditt program så att det använder Azure Files

Lägg till följande längst upp i en python-källfil för att använda kodfragmenten i den här artikeln.

# <a name="python-v12"></a>[Python-V12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_Imports":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
from azure.storage.file import FileService
```

---

## <a name="set-up-a-connection-to-azure-files"></a>Konfigurera en anslutning till Azure Files

# <a name="python-v12"></a>[Python-V12](#tab/python)

Med [ShareServiceClient](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareserviceclient) kan du arbeta med resurser, kataloger och filer. Följande kod skapar ett- `ShareServiceClient` objekt med hjälp av anslutnings strängen för lagrings kontot.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateShareServiceClient":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Med [FileService](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true) -objektet kan du arbeta med resurser, kataloger och filer. Följande kod skapar ett- `FileService` objekt med hjälp av lagrings kontots namn och konto nyckeln. Ersätt `<myaccount>` och `<mykey>` med kontonamnet och nyckeln.

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

---

## <a name="create-an-azure-file-share"></a>Skapa en Azure-filresurs

# <a name="python-v12"></a>[Python-V12](#tab/python)

I följande kod exempel används ett [ShareClient](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareclient) -objekt för att skapa resursen om den inte finns.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateFileShare":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

I följande kod exempel används ett [FileService](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true) -objekt för att skapa resursen om den inte finns.

```python
file_service.create_share('myshare')
```

---

## <a name="create-a-directory"></a>Skapa en katalog

Du kan organisera lagringen genom att placera filer i under kataloger i stället för att använda dem i rot katalogen.

# <a name="python-v12"></a>[Python-V12](#tab/python)

Följande metod skapar en katalog i roten för den angivna fil resursen med hjälp av ett [ShareDirectoryClient](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.sharedirectoryclient) -objekt.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateDirectory":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Koden nedan skapar en under katalog med namnet *sampledir* under rot katalogen.

```python
file_service.create_directory('myshare', 'sampledir')
```

---

## <a name="upload-a-file"></a>Ladda upp en fil

I det här avsnittet får du lära dig hur du överför en fil från lokal lagring till Azure File Storage.

# <a name="python-v12"></a>[Python-V12](#tab/python)

Följande metod överför innehållet i den angivna filen till den angivna katalogen i den angivna Azure-filresursen.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_UploadFile":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

En Azure-filresurs innehåller minst en rot katalog där filer kan finnas. Om du vill skapa en fil och överföra data använder du metoderna [create_file_from_path](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-path-share-name--directory-name--file-name--local-file-path--content-settings-none--metadata-none--validate-content-false--progress-callback-none--max-connections-2--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object---timeout-none-), [create_file_from_stream](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-stream-share-name--directory-name--file-name--stream--count--content-settings-none--metadata-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object--), [create_file_from_bytes](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-bytes-share-name--directory-name--file-name--file--index-0--count-none--content-settings-none--metadata-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object--)eller [create_file_from_text](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-text-share-name--directory-name--file-name--text--encoding--utf-8---content-settings-none--metadata-none--validate-content-false--timeout-none--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object--) . De är avancerade metoder som utför den nödvändiga delningen när storleken på data överskrider 64 MB.

`create_file_from_path` överför innehållet i en fil från den angivna sökvägen och `create_file_from_stream` laddar upp innehållet från en redan öppnad fil/data ström. `create_file_from_bytes` laddar upp en matris med byte och `create_file_from_text` laddar upp det angivna textvärdet med den angivna kodningen (Standardvärdet är UTF-8).

I följande exempel överförs innehållet i *sunset.png* -filen **till filen.**

```python
from azure.storage.file import ContentSettings
file_service.create_file_from_path(
    'myshare',
    None,  # We want to create this file in the root directory, so we specify None for the directory_name
    'myfile',
    'sunset.png',
    content_settings=ContentSettings(content_type='image/png'))
```

---

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Räkna upp filer och kataloger i en Azure-filresurs

# <a name="python-v12"></a>[Python-V12](#tab/python)

Om du vill visa en lista över filer och kataloger i en under katalog använder du metoden [list_directories_and_files](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareclient#list-directories-and-files-directory-name-none--name-starts-with-none--marker-none----kwargs-) . Den här metoden returnerar en ITER med automatisk växling. Följande kod matar ut **namnet** på varje fil och under katalog i den angivna katalogen till-konsolen.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_ListFilesAndDirs":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Om du vill visa en lista över filer och kataloger i en resurs använder du metoden [list_directories_and_files](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#list-directories-and-files-share-name--directory-name-none--num-results-none--marker-none--timeout-none--prefix-none--snapshot-none-) . Den här metoden returnerar en generator. Följande kod matar ut **namnet** på varje fil och katalog i en resurs till-konsolen.

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

---

## <a name="download-a-file"></a>Ladda ned en fil

# <a name="python-v12"></a>[Python-V12](#tab/python)

Använd [Download_File](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.sharefileclient#download-file-offset-none--length-none----kwargs-)om du vill hämta data från en fil.

I följande exempel visas hur `download_file` du hämtar innehållet i den angivna filen och lagrar det lokalt med **nedladdad-** anpassningsprefix till fil namnet.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DownloadFile":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Om du vill hämta data från en fil använder du [get_file_to_path](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-path-share-name--directory-name--file-name--file-path--open-mode--wb---start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-), [get_file_to_stream](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-stream-share-name--directory-name--file-name--stream--start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-), [get_file_to_bytes](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-bytes-share-name--directory-name--file-name--start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-)eller [get_file_to_text](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-text-share-name--directory-name--file-name--encoding--utf-8---start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-). De är avancerade metoder som utför den nödvändiga delningen när storleken på data överskrider 64 MB.

I följande exempel visas hur `get_file_to_path` du hämtar innehållet i **filen i** filen och lagrar det i *out-sunset.png* -filen.

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

---

## <a name="create-a-share-snapshot"></a>Skapa en ögonblicksbild av en resurs

Du kan skapa en tidpunkts kopia av hela fil resursen.

# <a name="python-v12"></a>[Python-V12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateSnapshot":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
snapshot = file_service.snapshot_share(share_name)
snapshot_id = snapshot.snapshot
```

**Skapa resurs ögonblicks bild med metadata**

```python
metadata = {"foo": "bar"}
snapshot = file_service.snapshot_share(share_name, metadata=metadata)
```

---

## <a name="list-shares-and-snapshots"></a>Lista över resurser och ögonblicks bilder

Du kan visa en lista över alla ögonblicks bilder för en viss resurs.

# <a name="python-v12"></a>[Python-V12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_ListSharesAndSnapshots":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
shares = list(file_service.list_shares(include_snapshots=True))
```

---

## <a name="browse-share-snapshot"></a>Bläddra i resurs ögonblicks bild

Du kan bläddra varje resurs ögonblicks bild för att hämta filer och kataloger från den tidpunkten.

# <a name="python-v12"></a>[Python-V12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_BrowseSnapshotDir":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
directories_and_files = list(
    file_service.list_directories_and_files(share_name, snapshot=snapshot_id))
```

---

## <a name="get-file-from-share-snapshot"></a>Hämta fil från resurs ögonblicks bild

Du kan ladda ned en fil från en resurs ögonblicks bild. På så sätt kan du återställa en tidigare version av en fil.

# <a name="python-v12"></a>[Python-V12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DownloadSnapshotFile":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
with open(FILE_PATH, 'wb') as stream:
    file = file_service.get_file_to_stream(
        share_name, directory_name, file_name, stream, snapshot=snapshot_id)
```

---

## <a name="delete-a-single-share-snapshot"></a>Ta bort en ögonblicks bild av en enskild resurs
Du kan ta bort en ögonblicks bild av en enskild resurs.

# <a name="python-v12"></a>[Python-V12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DeleteSnapshot":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
file_service.delete_share(share_name, snapshot=snapshot_id)
```

---

## <a name="delete-a-file"></a>Ta bort en fil

# <a name="python-v12"></a>[Python-V12](#tab/python)

Anropa [delete_file](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.sharefileclient#delete-file---kwargs-)om du vill ta bort en fil.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DeleteFile":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Anropa [delete_file](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#delete-file-share-name--directory-name--file-name--timeout-none-)om du vill ta bort en fil.

```python
file_service.delete_file('myshare', None, 'myfile')
```

---

## <a name="delete-share-when-share-snapshots-exist"></a>Ta bort resurs när resurs ögonblicks bilder finns

# <a name="python-v12"></a>[Python-V12](#tab/python)

Om du vill ta bort en resurs som innehåller ögonblicks bilder anropar [delete_share](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareclient#delete-share-delete-snapshots-false----kwargs-) med `delete_snapshots=True` .

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DeleteShare":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Det går inte att ta bort en resurs som innehåller ögonblicks bilder om inte alla ögonblicks bilder tas bort först.

```python
file_service.delete_share(share_name, delete_snapshots=DeleteSnapshot.Include)
```

---

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du manipulerar Azure Files med python kan du följa dessa länkar om du vill veta mer.

- [Python Developer Center](/azure/developer/python/)
- [REST-API för Azure Storage Services](/rest/api/azure/)
- [Microsoft Azure Storage SDK för python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage)
