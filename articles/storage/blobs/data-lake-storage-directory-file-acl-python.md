---
title: Använd python för att hantera data i Azure Data Lake Storage Gen2
description: Använd python för att hantera kataloger och filer i lagrings konton med hierarkiskt namn område aktiverat.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-python
ms.openlocfilehash: a143c0aa19241b532cabff95fe6bf85679e4007c
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652300"
---
# <a name="use-python-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Använd python för att hantera kataloger och filer i Azure Data Lake Storage Gen2

Den här artikeln visar hur du använder python för att skapa och hantera kataloger och filer i lagrings konton som har ett hierarkiskt namn område.

Information om hur du hämtar, anger och uppdaterar åtkomst kontrol listorna (ACL) för kataloger och filer finns i [använda python för att hantera ACL: er i Azure Data Lake Storage Gen2](data-lake-storage-acl-python.md).

[Paket (python-paket index)](https://pypi.org/project/azure-storage-file-datalake/)  |  [Exempel](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)  |  [API-referens](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)  |  [Gen1 till Gen2-mappning](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)  |  [Ge feedback](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

- Ett lagrings konto med hierarkiskt namn område aktiverat. Följ [de här](create-data-lake-storage-account.md) anvisningarna för att skapa en.

## <a name="set-up-your-project"></a>Konfigurera projektet

Installera Azure Data Lake Storage klient bibliotek för python med hjälp av [pip](https://pypi.org/project/pip/).

```
pip install azure-storage-file-datalake
```

Lägg till dessa import uttryck längst upp i din kod fil.

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
from azure.core._match_conditions import MatchConditions
from azure.storage.filedatalake._models import ContentSettings
```

## <a name="connect-to-the-account"></a>Anslut till kontot

Om du vill använda kodfragmenten i den här artikeln måste du skapa en **DataLakeServiceClient** -instans som representerar lagrings kontot. 

### <a name="connect-by-using-an-account-key"></a>Anslut med hjälp av en konto nyckel

Detta är det enklaste sättet att ansluta till ett konto. 

I det här exemplet skapas en **DataLakeServiceClient** -instans med hjälp av en konto nyckel.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithKey":::

- Ersätt platshållarvärdet `storage_account_name` med namnet på ditt lagringskonto.

- Ersätt `storage_account_key` placeholder-värdet med din åtkomst nyckel för lagrings kontot.

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Anslut med hjälp av Azure Active Directory (Azure AD)

Du kan använda [klient biblioteket för Azure Identity för python för](https://pypi.org/project/azure-identity/) att autentisera ditt program med Azure AD.

I det här exemplet skapas en **DataLakeServiceClient** -instans med hjälp av ett klient-ID, en klient hemlighet och ett klient-ID.  Om du vill hämta dessa värden läser du [Hämta en token från Azure AD för att auktorisera begär Anden från ett klient program](../common/storage-auth-aad-app.md).

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> Fler exempel finns i [klient biblioteket för Azure Identity för python](https://pypi.org/project/azure-identity/) -dokumentation.

## <a name="create-a-container"></a>Skapa en container

En behållare fungerar som ett fil system för dina filer. Du kan skapa en genom att anropa metoden **FileSystemDataLakeServiceClient.create_file_system** .

I det här exemplet skapas en behållare med namnet `my-file-system` .

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_CreateContainer":::

## <a name="create-a-directory"></a>Skapa en katalog

Skapa en katalog referens genom att anropa metoden **FileSystemClient.create_directory** .

Det här exemplet lägger till en katalog med namnet `my-directory` i en behållare. 

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_CreateDirectory":::

## <a name="rename-or-move-a-directory"></a>Byta namn på eller flytta en katalog

Byt namn på eller flytta en katalog genom att anropa metoden **DataLakeDirectoryClient.rename_directory** . Skicka sökvägen till önskad katalog en parameter. 

I det här exemplet byter namn på en under katalog till namnet `my-subdirectory-renamed` .

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_RenameDirectory":::

## <a name="delete-a-directory"></a>Ta bort en katalog

Ta bort en katalog genom att anropa metoden **DataLakeDirectoryClient.delete_directory** .

Det här exemplet tar bort en katalog med namnet `my-directory` .  

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_DeleteDirectory":::

## <a name="upload-a-file-to-a-directory"></a>Ladda upp en fil till en katalog

Börja med att skapa en fil referens i mål katalogen genom att skapa en instans av klassen **DataLakeFileClient** . Ladda upp en fil genom att anropa metoden **DataLakeFileClient.append_data** . Se till att slutföra överföringen genom att anropa metoden **DataLakeFileClient.flush_data** .

I det här exemplet överförs en textfil till en katalog med namnet `my-directory` .   

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_UploadFile":::

> [!TIP]
> Om din fil storlek är stor måste koden göra flera anrop till metoden **DataLakeFileClient.append_data** . Överväg att använda metoden **DataLakeFileClient.upload_data** i stället. På så sätt kan du ladda upp hela filen i ett enda anrop. 

## <a name="upload-a-large-file-to-a-directory"></a>Ladda upp en stor fil till en katalog

Använd metoden **DataLakeFileClient.upload_data** för att överföra stora filer utan att behöva göra flera anrop till **DataLakeFileClient.append_data** -metoden.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_UploadFileBulk":::

## <a name="download-from-a-directory"></a>Ladda ned från en katalog 

Öppna en lokal fil för skrivning. Skapa sedan en **DataLakeFileClient** -instans som representerar den fil som du vill ladda ned. Anropa **DataLakeFileClient.read_file** för att läsa byte från filen och skriv sedan dessa byte till den lokala filen. 

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_DownloadFromDirectory":::

## <a name="list-directory-contents"></a>Lista kataloginnehåll

Lista katalog innehåll genom att anropa metoden **FileSystemClient.get_paths** och sedan räkna upp genom resultaten.

I det här exemplet skrivs sökvägen till varje under katalog och fil som finns i en katalog med namnet `my-directory` .

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_ListFilesInDirectory":::

## <a name="see-also"></a>Se även

- [Referensdokumentation för API](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)
- [Paket (python-paket index)](https://pypi.org/project/azure-storage-file-datalake/)
- [Exempel](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
- [Gen1 till Gen2-mappning](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
- [Kända problem](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Ge feedback](https://github.com/Azure/azure-sdk-for-python/issues)