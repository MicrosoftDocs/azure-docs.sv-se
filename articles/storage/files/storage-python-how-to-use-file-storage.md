---
title: Utveckla för Azure Files med Python-| Microsoft Docs
description: Lär dig hur du utvecklar Python-program och -tjänster som använder Azure Files för att lagra fildata.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/08/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-python
ms.openlocfilehash: 8739bfaf1a41758ef3267c71cba883ef2445c39d
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817800"
---
# <a name="develop-for-azure-files-with-python"></a>Utveckla för Azure Files med Python

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

Lär dig grunderna i att använda Python för att utveckla appar eller tjänster som använder Azure Files för att lagra fildata. Skapa en enkel konsolapp och lär dig hur du utför grundläggande åtgärder med Python och Azure Files:

- Skapa Azure-filresurser
- Skapa kataloger
- Räkna upp filer och kataloger i en Azure-filresurs
- Ladda upp, ladda ned och ta bort en fil
- Skapa säkerhetskopior av filresurs med hjälp av ögonblicksbilder

> [!NOTE]
> Eftersom Azure Files kan nås via SMB, är det möjligt att skriva enkla program som har åtkomst till Azure-filresursen med hjälp av standardklasserna och funktionerna i Python I/O. Den här artikeln beskriver hur du skriver appar som använder Azure Files [](/rest/api/storageservices/file-service-rest-api) Storage Python SDK, som använder Azure Files REST API för att prata med Azure Files.

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Ladda ned och Azure Storage SDK för Python

> [!NOTE]
> Om du uppgraderar från Azure Storage SDK för Python version 0.36 eller tidigare avinstallerar du den äldre SDK:n med hjälp av innan du installerar `pip uninstall azure-storage` det senaste paketet.

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

[Azure File Storage-klientbiblioteket v12.x för Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-share) kräver Python 2.7 eller 3.6+.

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

[Sdk Azure Storage för Python kräver](https://github.com/azure/azure-storage-python) Python 2.7 eller 3.6+.

---

## <a name="install-via-pypi"></a>Installera via PyPI

Om du vill installera via Python Package Index (PyPI) skriver du:

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

```console
pip install azure-storage-file-share
```

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

```console
pip install azure-storage-file
```

### <a name="view-the-sample-application"></a>Visa exempelprogrammet

Om du vill visa och köra ett exempelprogram som visar hur du använder Python med Azure Files kan du [Azure Storage: Komma igång med Azure Files i Python](https://github.com/Azure-Samples/storage-file-python-getting-started).

Om du vill köra exempelprogrammet kontrollerar du att du har installerat både `azure-storage-file` paketen och `azure-storage-common` .

---

## <a name="set-up-your-application-to-use-azure-files"></a>Konfigurera programmet att använda Azure Files

Lägg till följande längst upp i en Python-källfil för att använda kodfragmenten i den här artikeln.

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_Imports":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

```python
from azure.storage.file import FileService
```

---

## <a name="set-up-a-connection-to-azure-files"></a>Konfigurera en anslutning till Azure Files

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

[Med ShareServiceClient](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareserviceclient) kan du arbeta med resurser, kataloger och filer. Följande kod skapar ett -objekt `ShareServiceClient` med anslutningssträngen för lagringskontot.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateShareServiceClient":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

Med [FileService-objektet](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true) kan du arbeta med resurser, kataloger och filer. Följande kod skapar ett `FileService` -objekt med lagringskontots namn och kontonyckel. Ersätt `<myaccount>` och `<mykey>` med kontonamnet och nyckeln.

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

---

## <a name="create-an-azure-file-share"></a>Skapa en Azure-filresurs

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

I följande kodexempel används ett [ShareClient-objekt](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareclient) för att skapa resursen om den inte finns.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateFileShare":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

I följande kodexempel används ett [FileService-objekt](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true) för att skapa resursen om den inte finns.

```python
file_service.create_share('myshare')
```

---

## <a name="create-a-directory"></a>Skapa en katalog

Du kan organisera lagringen genom att placera filer i underkataloger i stället för att ha alla i rotkatalogen.

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

Följande metod skapar en katalog i roten för den angivna filresursen med hjälp av ett [ShareDirectoryClient-objekt.](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.sharedirectoryclient)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateDirectory":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

Koden nedan skapar en underkatalog med namnet *sampledir* under rotkatalogen.

```python
file_service.create_directory('myshare', 'sampledir')
```

---

## <a name="upload-a-file"></a>Ladda upp en fil

I det här avsnittet lär du dig att ladda upp en fil från lokal lagring till Azure File Storage.

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

Följande metod laddar upp innehållet i den angivna filen till den angivna katalogen i den angivna Azure-filresursen.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_UploadFile":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

En Azure-filresurs innehåller minst en rotkatalog där filer kan finnas. Om du vill skapa en fil och ladda upp data [create_file_from_path](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-path-share-name--directory-name--file-name--local-file-path--content-settings-none--metadata-none--validate-content-false--progress-callback-none--max-connections-2--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object---timeout-none-), [create_file_from_stream](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-stream-share-name--directory-name--file-name--stream--count--content-settings-none--metadata-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object--), [create_file_from_bytes](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-bytes-share-name--directory-name--file-name--file--index-0--count-none--content-settings-none--metadata-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object--)eller [create_file_from_text](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-text-share-name--directory-name--file-name--text--encoding--utf-8---content-settings-none--metadata-none--validate-content-false--timeout-none--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object--) metoder. De är avancerade metoder som utför den nödvändiga segmentering när storleken på data överskrider 64 MB.

`create_file_from_path` laddar upp innehållet i en fil från den angivna sökvägen och `create_file_from_stream` laddar upp innehållet från en fil/ström som redan har öppnats. `create_file_from_bytes` laddar upp en matris med byte och laddar upp det angivna textvärdet med angiven kodning `create_file_from_text` (standardvärdet är UTF-8).

I följande exempel laddas innehållet i filen *sunset.png* upp till **filen myfile.**

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

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

Om du vill visa en lista över filer [](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareclient#list-directories-and-files-directory-name-none--name-starts-with-none--marker-none----kwargs-) och kataloger i en underkatalog använder list_directories_and_files-metoden. Den här metoden returnerar en iterbar automatisk växling. Följande kod matar ut namnet **på** varje fil och underkatalog i den angivna katalogen till konsolen.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_ListFilesAndDirs":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

Om du vill visa en lista över [](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#list-directories-and-files-share-name--directory-name-none--num-results-none--marker-none--timeout-none--prefix-none--snapshot-none-) filer och kataloger i en resurs använder list_directories_and_files-metoden. Den här metoden returnerar en generator. Följande kod matar ut namnet **på** varje fil och katalog i en resurs till konsolen.

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

---

## <a name="download-a-file"></a>Ladda ned en fil

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

Om du vill ladda ned data från en fil använder [du download_file](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.sharefileclient#download-file-offset-none--length-none----kwargs-).

Följande exempel visar hur du använder för att hämta innehållet i den angivna filen och lagra den lokalt med `download_file` **DOWNLOADED-** före filnamnet.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DownloadFile":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

Om du vill ladda ned data från [en fil använder du get_file_to_path](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-path-share-name--directory-name--file-name--file-path--open-mode--wb---start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-), [get_file_to_stream](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-stream-share-name--directory-name--file-name--stream--start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-), [get_file_to_bytes](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-bytes-share-name--directory-name--file-name--start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-)eller [get_file_to_text](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-text-share-name--directory-name--file-name--encoding--utf-8---start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-). De är avancerade metoder som utför den nödvändiga segmentering när storleken på data överskrider 64 MB.

I följande exempel visas hur du använder för att ladda ned innehållet i `get_file_to_path` **filen myfile** och lagra den *iout-sunset.png* filen.

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

---

## <a name="create-a-share-snapshot"></a>Skapa en ögonblicksbild av en resurs

Du kan skapa en kopia till en tidpunkt av hela filresursen.

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateSnapshot":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

```python
snapshot = file_service.snapshot_share(share_name)
snapshot_id = snapshot.snapshot
```

**Skapa resursögonblicksbild med metadata**

```python
metadata = {"foo": "bar"}
snapshot = file_service.snapshot_share(share_name, metadata=metadata)
```

---

## <a name="list-shares-and-snapshots"></a>Lista resurser och ögonblicksbilder

Du kan visa en lista över alla ögonblicksbilder för en viss resurs.

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_ListSharesAndSnapshots":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

```python
shares = list(file_service.list_shares(include_snapshots=True))
```

---

## <a name="browse-share-snapshot"></a>Bläddra i resursögonblicksbild

Du kan bläddra i varje resursögonblicksbild för att hämta filer och kataloger från den tidpunkten.

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_BrowseSnapshotDir":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

```python
directories_and_files = list(
    file_service.list_directories_and_files(share_name, snapshot=snapshot_id))
```

---

## <a name="get-file-from-share-snapshot"></a>Hämta fil från resursögonblicksbild

Du kan ladda ned en fil från en resursögonblicksbild. På så sätt kan du återställa en tidigare version av en fil.

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DownloadSnapshotFile":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

```python
with open(FILE_PATH, 'wb') as stream:
    file = file_service.get_file_to_stream(
        share_name, directory_name, file_name, stream, snapshot=snapshot_id)
```

---

## <a name="delete-a-single-share-snapshot"></a>Ta bort en enskild resursögonblicksbild
Du kan ta bort en enskild resursögonblicksbild.

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DeleteSnapshot":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

```python
file_service.delete_share(share_name, snapshot=snapshot_id)
```

---

## <a name="delete-a-file"></a>Ta bort en fil

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

Om du vill ta bort en fil [anropar du delete_file](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.sharefileclient#delete-file---kwargs-).

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DeleteFile":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

Om du vill ta bort en fil [anropar du delete_file](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#delete-file-share-name--directory-name--file-name--timeout-none-).

```python
file_service.delete_file('myshare', None, 'myfile')
```

---

## <a name="delete-share-when-share-snapshots-exist"></a>Ta bort resurs när resursögonblicksbilder finns

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

Om du vill ta bort en resurs som innehåller ögonblicksbilder [anropar delete_share](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareclient#delete-share-delete-snapshots-false----kwargs-) med `delete_snapshots=True` .

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DeleteShare":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

En resurs som innehåller ögonblicksbilder kan inte tas bort om inte alla ögonblicksbilder tas bort först.

```python
file_service.delete_share(share_name, delete_snapshots=DeleteSnapshot.Include)
```

---

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du ändrar Azure Files med Python kan du följa dessa länkar för att lära dig mer.

- [Python Developer Center](/azure/developer/python/)
- [REST-API för Azure Storage Services](/rest/api/azure/)
- [Microsoft Azure Storage SDK för Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage)
