---
title: Python-tjänst-till-tjänst-autentisering – Data Lake Storage Gen1
description: Lär dig hur du uppnår tjänst-till-tjänst-autentisering med Azure Data Lake Storage Gen1 att använda Azure Active Directory med python
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: has-adal-ref, devx-track-python
ms.openlocfilehash: fe987070438c689e899dee18edaf4f708555ab11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92106764"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-python"></a>Tjänst-till-tjänst-autentisering med Azure Data Lake Storage Gen1 med python
> [!div class="op_single_selector"]
> * [Använda Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Använda .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Använda Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Använda REST-API](data-lake-store-service-to-service-authenticate-rest-api.md)
>
>

I den här artikeln får du lära dig hur du använder python SDK för att utföra tjänst-till-tjänst-autentisering med Azure Data Lake Storage Gen1. För autentisering med slutanvändare med Data Lake Storage Gen1 med python, se [slutanvändarens autentisering med data Lake Storage gen1 med python](data-lake-store-end-user-authenticate-python.md).


## <a name="prerequisites"></a>Förutsättningar

* **Python**. Du kan hämta Python [här](https://www.python.org/downloads/). I den här artikeln används Python 3.6.2.

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Skapa ett Azure Active Directory "Web"-program**. Du måste ha slutfört stegen i [tjänst-till-tjänst-autentisering med data Lake Storage gen1 med Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="install-the-modules"></a>Installera modulerna

Om du vill arbeta med Data Lake Storage Gen1 med python måste du installera tre moduler.

* `azure-mgmt-resource`-modulen, som innehåller Azure-moduler för Active Directory osv.
* `azure-mgmt-datalake-store`Modulen, som innehåller data Lake Storage gen1 konto hanterings åtgärder. Mer information om den här modulen finns i [referens för Azure Data Lake Storage gen1 Management-modulen](/python/api/azure-mgmt-datalake-store/).
* `azure-datalake-store`Modulen, som innehåller data Lake Storage gen1 fil Systems åtgärder. Mer information om den här modulen finns i [referens för Azure-datalake-Store-filsystem-modul](/python/api/azure-datalake-store/azure.datalake.store.core/).

Installera modulerna med hjälp av följande kommandon.

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Skapa ett nytt Python-program

1. Skapa ett nytt Python-program i valfritt IDE, t.ex. **mysample.py**.

2. Lägg till följande kodavsnitt för att importera de nödvändiga modulerna

    ```
    ## Use this for Azure AD authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Data Lake Storage Gen1 account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Data Lake Storage Gen1 filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    import adal
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, getpass, pprint, uuid, time
    ```

3. Spara ändringarna i mysample.py.

## <a name="service-to-service-authentication-with-client-secret-for-account-management"></a>”Tjänst till tjänst”-autentisering med klienthemlighet för kontohantering

Använd det här kodfragmentet för att autentisera med Azure AD för konto hanterings åtgärder på Data Lake Storage Gen1, till exempel skapa ett Data Lake Storage Gen1 konto, ta bort ett Data Lake Storage Gen1-konto osv. Följande kodfragment kan användas för att autentisera ditt program icke-interaktivt, med hjälp av klient hemligheten för ett program/tjänst objekt för ett befintligt Azure AD-program för webb program.

```python
authority_host_uri = 'https://login.microsoftonline.com'
tenant = '<TENANT>'
authority_uri = authority_host_uri + '/' + tenant
RESOURCE = 'https://management.core.windows.net/'
client_id = '<CLIENT_ID>'
client_secret = '<CLIENT_SECRET>'

context = adal.AuthenticationContext(authority_uri, api_version=None)
mgmt_token = context.acquire_token_with_client_credentials(RESOURCE, client_id, client_secret)
armCreds = AADTokenCredentials(mgmt_token, client_id, resource=RESOURCE)
```

## <a name="service-to-service-authentication-with-client-secret-for-filesystem-operations"></a>Tjänst-till-tjänst-autentisering med klienthemlighet för filsystemsåtgärder

Använd följande kodfragment för att autentisera med Azure AD för fil Systems åtgärder på Data Lake Storage Gen1, till exempel skapa mapp, ladda upp fil osv. Följande kodfragment kan användas för att autentisera ditt program icke-interaktivt, med hjälp av klient hemligheten för ett program/tjänstens huvud namn. Använd detta med ett befintligt Azure AD-webbappsprogram.

```python
tenant = '<TENANT>'
RESOURCE = 'https://datalake.azure.net/'
client_id = '<CLIENT_ID>'
client_secret = '<CLIENT_SECRET>'

adlCreds = lib.auth(tenant_id = tenant,
                client_secret = client_secret,
                client_id = client_id,
                resource = RESOURCE)
```

<!-- ## Service-to-service authentication with certificate for account management

Use this snippet to authenticate with Azure AD for account management operations on Data Lake Storage Gen1 such as create a Data Lake Storage Gen1 account, delete a Data Lake Storage Gen1 account, etc. The following snippet can be used to authenticate your application non-interactively, using the certificate of an existing Azure AD "Web App" application. For instructions on how to create an Azure AD application, see [Create service principal with certificates](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).

    authority_host_uri = 'https://login.microsoftonline.com'
    tenant = '<TENANT>'
    authority_uri = authority_host_uri + '/' + tenant
    resource_uri = 'https://management.core.windows.net/'
    client_id = '<CLIENT_ID>'
    client_cert = '<CLIENT_CERT>'
    client_cert_thumbprint = '<CLIENT_CERT_THUMBPRINT>'

    context = adal.AuthenticationContext(authority_uri, api_version=None)
    mgmt_token = context.acquire_token_with_client_certificate(resource_uri, client_id, client_cert, client_cert_thumbprint)
    credentials = AADTokenCredentials(mgmt_token, client_id) -->

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur du använder tjänst-till-tjänst-autentisering för att autentisera med Data Lake Storage Gen1 med python. Nu kan du titta på följande artiklar som talar om hur du använder python för att arbeta med Data Lake Storage Gen1.

* [Konto hanterings åtgärder på Data Lake Storage Gen1 med python](data-lake-store-get-started-python.md)
* [Data åtgärder på Data Lake Storage Gen1 med python](data-lake-store-data-operations-python.md)