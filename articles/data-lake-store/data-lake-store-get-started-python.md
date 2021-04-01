---
title: Hantera ett Azure Data Lake Storage Gen1 konto med python
description: Lär dig hur du använder python SDK för Azure Data Lake Storage Gen1 konto hanterings åtgärder.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: devx-track-python
ms.openlocfilehash: 35e71b80c6f47bb13f7a2b490b493b0cb42acf04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92103517"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-python"></a>Konto hanterings åtgärder på Azure Data Lake Storage Gen1 med python
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST-API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Lär dig hur du använder python SDK för Azure Data Lake Storage Gen1 för att utföra grundläggande konto hanterings åtgärder, till exempel skapa ett Data Lake Storage Gen1 konto, lista Data Lake Storage Gen1-kontona osv. Instruktioner för hur du utför fil Systems åtgärder på Data Lake Storage Gen1 med python finns i [fil Systems åtgärder på data Lake Storage gen1 med python](data-lake-store-data-operations-python.md).

## <a name="prerequisites"></a>Förutsättningar

* **Python**. Du kan hämta Python [här](https://www.python.org/downloads/). I den här artikeln används Python 3.6.2.

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* **En Azure-resursgrupp**. Instruktioner finns i [skapa en Azure-resursgrupp](../azure-resource-manager/management/manage-resource-groups-portal.md).

## <a name="install-the-modules"></a>Installera modulerna

Om du vill arbeta med Data Lake Storage Gen1 med python måste du installera tre moduler.

* `azure-mgmt-resource`-modulen, som innehåller Azure-moduler för Active Directory osv.
* `azure-mgmt-datalake-store`Modulen, som innehåller Azure Data Lake Storage gen1 konto hanterings åtgärder. Mer information om den här modulen finns i [referens för Azure Data Lake Storage gen1 Management-modulen](/python/api/azure-mgmt-datalake-store/).
* `azure-datalake-store`Modulen, som innehåller Azure Data Lake Storage gen1 fil Systems åtgärder. Mer information om den här modulen finns i [referens för Azure-datalake-Store-filsystem-modul](/python/api/azure-datalake-store/azure.datalake.store.core/).

Installera modulerna med hjälp av följande kommandon.

```console
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Skapa ett nytt Python-program

1. Skapa ett nytt Python-program i valfritt IDE, t.ex. **mysample.py**.

2. Lägg till följande kodavsnitt för att importera de nödvändiga modulerna

    ```python
    ## Use this only for Azure AD service-to-service authentication
    from azure.common.credentials import ServicePrincipalCredentials

    ## Use this only for Azure AD end-user authentication
    from azure.common.credentials import UserPassCredentials

    ## Use this only for Azure AD multi-factor authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Data Lake Storage Gen1 account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Data Lake Storage Gen1 filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
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

## <a name="create-client-and-data-lake-storage-gen1-account"></a>Skapa klient-och Data Lake Storage Gen1 konto

Följande fragment skapar först Data Lake Storage Gen1 Account-klienten. Klient objekt används för att skapa ett Data Lake Storage Gen1-konto. Slutligen skapar kodfragmentet ett klientobjekt för filsystemet.

```python
## Declare variables
subscriptionId = 'FILL-IN-HERE'
adlsAccountName = 'FILL-IN-HERE'
resourceGroup = 'FILL-IN-HERE'
location = 'eastus2'

## Create Data Lake Storage Gen1 account management client object
adlsAcctClient = DataLakeStoreAccountManagementClient(armCreds, subscriptionId)

## Create a Data Lake Storage Gen1 account
adlsAcctResult = adlsAcctClient.account.create(
    resourceGroup,
    adlsAccountName,
    DataLakeStoreAccount(
        location=location
    )
).wait()
```

    
## <a name="list-the-data-lake-storage-gen1-accounts"></a>Lista Data Lake Storage Gen1 konton

```python
## List the existing Data Lake Storage Gen1 accounts
result_list_response = adlsAcctClient.account.list()
result_list = list(result_list_response)
for items in result_list:
    print(items)
```

## <a name="delete-the-data-lake-storage-gen1-account"></a>Ta bort Data Lake Storage Gen1 kontot

```python
## Delete an existing Data Lake Storage Gen1 account
adlsAcctClient.account.delete(adlsAccountName)
```
    

## <a name="next-steps"></a>Nästa steg
* [Fil Systems åtgärder på data Lake Storage gen1 med python](data-lake-store-data-operations-python.md).

## <a name="see-also"></a>Se även

* [referens för Azure-datalake-Store python (fil system)](/python/api/azure-datalake-store/azure.datalake.store.core)
* [Stor data program med öppen källkod som är kompatibla med Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)