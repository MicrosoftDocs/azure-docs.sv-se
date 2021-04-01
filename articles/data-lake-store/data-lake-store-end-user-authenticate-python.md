---
title: Autentisering med slutanvändare – python med Data Lake Storage Gen1 – Azure
description: Lär dig hur du uppnår autentisering för slutanvändare med Azure Data Lake Storage Gen1 att använda Azure Active Directory med python
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: has-adal-ref, devx-track-python
ms.openlocfilehash: c7ee58dec4e8bc04eb07695378e0d4dc973b6705
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92109093"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-python"></a>Autentisering med slutanvändare med Azure Data Lake Storage Gen1 med python
> [!div class="op_single_selector"]
> * [Använda Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Använda .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Använda Python](data-lake-store-end-user-authenticate-python.md)
> * [Använda REST-API](data-lake-store-end-user-authenticate-rest-api.md)
>
>

I den här artikeln får du lära dig hur du använder python SDK för att utföra autentisering med slutanvändare med Azure Data Lake Storage Gen1. Autentisering med slutanvändare kan delas upp i två kategorier:

* Autentisering med slutanvändare utan Multi-Factor Authentication
* Autentisering med slutanvändare med Multi-Factor Authentication

Båda alternativen beskrivs i den här artikeln. För tjänst-till-tjänst-autentisering med Data Lake Storage Gen1 med python, se [tjänst-till-tjänst-autentisering med data Lake Storage gen1 med hjälp av python](data-lake-store-service-to-service-authenticate-python.md).

## <a name="prerequisites"></a>Förutsättningar

* **Python**. Du kan hämta Python [här](https://www.python.org/downloads/). I den här artikeln används Python 3.6.2.

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Skapa ett Azure Active Directory "internt"-program**. Du måste ha slutfört stegen i [slut användar autentisering med data Lake Storage gen1 med hjälp av Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

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

1. Skapa ett nytt python-program i valfri IDE-app, till exempel **mysample.py**.

2. Lägg till följande kodavsnitt för att importera de nödvändiga modulerna

    ```
    ## Use this for Azure AD authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Azure Data Lake Storage Gen1 account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Azure Data Lake Storage Gen1 filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    import adal
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, pprint, uuid, time
    ```

3. Spara ändringarna i mysample.py.

## <a name="end-user-authentication-with-multi-factor-authentication"></a>Autentisering med slutanvändare med Multi-Factor Authentication

### <a name="for-account-management"></a>För konto hantering

Använd följande kodfragment för att autentisera med Azure AD för konto hanterings åtgärder på ett Data Lake Storage Gen1-konto. Följande kodfragment kan användas för att autentisera ditt program med multifaktorautentisering. Ange värdena nedan för ett befintligt **internt** Azure AD-program.

```python
authority_host_url = "https://login.microsoftonline.com"
tenant = "FILL-IN-HERE"
authority_url = authority_host_url + '/' + tenant
client_id = 'FILL-IN-HERE'
redirect = 'urn:ietf:wg:oauth:2.0:oob'
RESOURCE = 'https://management.core.windows.net/'

context = adal.AuthenticationContext(authority_url)
code = context.acquire_user_code(RESOURCE, client_id)
print(code['message'])
mgmt_token = context.acquire_token_with_device_code(RESOURCE, code, client_id)
armCreds = AADTokenCredentials(mgmt_token, client_id, resource = RESOURCE)
```

### <a name="for-filesystem-operations"></a>För fil Systems åtgärder

Använd detta för att autentisera med Azure AD för fil Systems åtgärder på ett Data Lake Storage Gen1-konto. Följande kodfragment kan användas för att autentisera ditt program med multifaktorautentisering. Ange värdena nedan för ett befintligt **internt** Azure AD-program.

```console
adlCreds = lib.auth(tenant_id='FILL-IN-HERE', resource = 'https://datalake.azure.net/')
```

## <a name="end-user-authentication-without-multi-factor-authentication"></a>Autentisering med slutanvändare utan Multi-Factor Authentication

Detta är inaktuellt. Mer information finns i [Azure-autentisering med python SDK](/azure/python/python-sdk-azure-authenticate).

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur du använder autentisering med slutanvändare för att autentisera med Azure Data Lake Storage Gen1 med python. Nu kan du titta på följande artiklar som talar om hur du använder python för att arbeta med Azure Data Lake Storage Gen1.

* [Konto hanterings åtgärder på Data Lake Storage Gen1 med python](data-lake-store-get-started-python.md)
* [Data åtgärder på Data Lake Storage Gen1 med python](data-lake-store-data-operations-python.md)