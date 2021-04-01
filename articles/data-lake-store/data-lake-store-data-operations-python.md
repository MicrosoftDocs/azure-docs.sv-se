---
title: 'Python: fil Systems åtgärder på Azure Data Lake Storage Gen1 | Microsoft Docs'
description: Lär dig hur du använder python SDK för att arbeta med Data Lake Storage Gen1 fil systemet.
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: devx-track-python
ms.openlocfilehash: 8ca455a802b180163579f67104a61f455dd54f94
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92109229"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-python"></a>Fil Systems åtgärder på Azure Data Lake Storage Gen1 med python
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST-API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

I den här artikeln får du lära dig hur du använder python SDK för att utföra fil Systems åtgärder på Azure Data Lake Storage Gen1. Instruktioner för hur du utför konto hanterings åtgärder på Data Lake Storage Gen1 med python finns i [konto hanterings åtgärder på data Lake Storage gen1 med python](data-lake-store-get-started-python.md).

## <a name="prerequisites"></a>Förutsättningar

* **Python**. Du kan hämta Python [här](https://www.python.org/downloads/). I den här artikeln används Python 3.6.2.

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Azure Data Lake Storage gen1 konto**. Följ anvisningarna i [Kom igång med Azure Data Lake Storage gen1 med hjälp av Azure Portal](data-lake-store-get-started-portal.md).

## <a name="install-the-modules"></a>Installera modulerna

Om du vill arbeta med Data Lake Storage Gen1 med python måste du installera tre moduler.

* `azure-mgmt-resource`-modulen, som innehåller Azure-moduler för Active Directory osv.
* `azure-mgmt-datalake-store`Modulen, som innehåller Azure Data Lake Storage gen1 konto hanterings åtgärder. Mer information om den här modulen finns i [referens för Azure-MGMT-datalake-Store-modulen](/python/api/azure-mgmt-datalake-store/).
* `azure-datalake-store`Modulen, som innehåller Azure Data Lake Storage gen1 fil Systems åtgärder. Mer information om den här modulen finns i [referens för Azure-datalake-Store-File-System-module](/python/api/azure-datalake-store/azure.datalake.store.core/).

Installera modulerna med hjälp av följande kommandon.

```console
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Skapa ett nytt Python-program

1. Skapa ett nytt Python-program i valfritt IDE, t.ex. **mysample.py**.

2. Lägg till följande rader för att importera de nödvändiga modulerna

   ```python
   ## Use this only for Azure AD service-to-service authentication
   from azure.common.credentials import ServicePrincipalCredentials

   ## Use this only for Azure AD end-user authentication
   from azure.common.credentials import UserPassCredentials

   ## Use this only for Azure AD multi-factor authentication
   from msrestazure.azure_active_directory import AADTokenCredentials

   ## Required for Azure Data Lake Storage Gen1 account management
   from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
   from azure.mgmt.datalake.store.models import DataLakeStoreAccount

   ## Required for Azure Data Lake Storage Gen1 filesystem management
   from azure.datalake.store import core, lib, multithread

   ## Common Azure imports
   from azure.mgmt.resource.resources import ResourceManagementClient
   from azure.mgmt.resource.resources.models import ResourceGroup

   ## Use these as needed for your application
   import logging, getpass, pprint, uuid, time
   ```

3. Spara ändringarna i mysample.py.

## <a name="authentication"></a>Autentisering

I det här avsnittet tittar vi på hur du kan autentisera med Azure AD på olika sätt. De tillgängliga alternativen är:

* För slutanvändarens autentisering för programmet, se [slutanvändarens autentisering med data Lake Storage gen1 med python](data-lake-store-end-user-authenticate-python.md).
* Information om tjänst-till-tjänst-autentisering för programmet finns i [tjänst-till-tjänst-autentisering med data Lake Storage gen1 med hjälp av python](data-lake-store-service-to-service-authenticate-python.md).

## <a name="create-filesystem-client"></a>Skapa filsystemklient

Följande fragment skapar först Data Lake Storage Gen1 Account-klienten. Klient objekt används för att skapa ett Data Lake Storage Gen1-konto. Slutligen skapar kodfragmentet ett klientobjekt för filsystemet.

```python
## Declare variables
subscriptionId = 'FILL-IN-HERE'
adlsAccountName = 'FILL-IN-HERE'

## Create a filesystem client object
adlsFileSystemClient = core.AzureDLFileSystem(adlCreds, store_name=adlsAccountName)
```

## <a name="create-a-directory"></a>Skapa en katalog

```python
## Create a directory
adlsFileSystemClient.mkdir('/mysampledirectory')
```

## <a name="upload-a-file"></a>Ladda upp en fil

```python
## Upload a file
multithread.ADLUploader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)
```


## <a name="download-a-file"></a>Ladda ned en fil

```python
## Download a file
multithread.ADLDownloader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt.out', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)
```

## <a name="delete-a-directory"></a>Ta bort en katalog

```python
## Delete a directory
adlsFileSystemClient.rm('/mysampledirectory', recursive=True)
```

## <a name="next-steps"></a>Nästa steg
* [Konto hanterings åtgärder på data Lake Storage gen1 med python](data-lake-store-get-started-python.md).

## <a name="see-also"></a>Se även

* [Referens för Azure Data Lake Storage Gen1 python (fil system)](/python/api/azure-datalake-store/azure.datalake.store.core)
* [Stor data program med öppen källkod som är kompatibla med Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)