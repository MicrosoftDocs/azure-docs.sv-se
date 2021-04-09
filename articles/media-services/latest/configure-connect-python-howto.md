---
title: Ansluta till Azure Media Services v3 API – python
description: Den här artikeln visar hur du ansluter till Media Services v3 API med python.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/18/2020
ms.author: inhenkel
ms.custom: devx-track-python
ms.openlocfilehash: 24e2ba4027dc818256dc9572f697fe7ec5a5a56b
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105960715"
---
# <a name="connect-to-media-services-v3-api---python"></a>Ansluta till Media Services v3 API – python

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Den här artikeln visar hur du ansluter till Azure Media Services v3 python SDK med hjälp av inloggnings metoden för tjänstens huvud namn.

## <a name="prerequisites"></a>Förutsättningar

- Hämta python från [python.org](https://www.python.org/downloads/)
- Se till att ställa in `PATH` miljövariabeln
- [Skapa ett Media Services-konto](./account-create-how-to.md). Glöm inte att komma ihåg resurs gruppens namn och namnet på Media Services kontot.
- Följ stegen i avsnittet [åtkomst-API: er](./access-api-howto.md) och välj autentiseringsmetod för tjänstens huvud namn. Registrera prenumerations-ID ( `SubscriptionId` ), programklient-ID ( `AadClientId` ), den autentiseringsnyckel ( `AadSecret` ) och klient-ID ( `AadTenantId` ) som du behöver i de senare stegen.

> [!IMPORTANT]
> Granska [namngivnings konventioner](media-services-apis-overview.md#naming-conventions).

## <a name="install-the-modules"></a>Installera modulerna

Om du vill arbeta med Azure Media Services med hjälp av python måste du installera dessa moduler.

* `azure-mgmt-resource`Modulen, som innehåller Azure-moduler för Active Directory.
* `azure-mgmt-media`Modulen, som innehåller Media Services entiteter.

    Se till att hämta [den senaste versionen av Media Services SDK för python](https://pypi.org/project/azure-mgmt-media/).

Öppna ett kommando rads verktyg och Använd följande kommandon för att installera modulerna.

```
pip3 install azure-mgmt-resource
pip3 install azure-mgmt-media==3.0.0
```

## <a name="connect-to-the-python-client"></a>Anslut till python-klienten

1. Skapa en fil med ett `.py` tillägg
1. Öppna filen i din favorit redigerare
1. Lägg till koden som följer efter filen. Koden importerar de nödvändiga modulerna och skapar objektet Active Directory autentiseringsuppgifter som du måste ansluta till Media Services.

      Ange Variablernas värden till de värden som du har fått från [åtkomst till API: er](./access-api-howto.md). Uppdatera- `ACCOUNT_NAME` och- `RESOUCE_GROUP_NAME` variablerna till Media Services konto namn och resurs grupp namn som används när du skapar dessa resurser.

      ```
      import adal
      from azure.mgmt.media import AzureMediaServices
      from msrestazure.azure_active_directory import AdalAuthentication
      from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD

      RESOURCE = "https://management.core.windows.net/"
      # Tenant ID for your Azure Subscription (AadTenantId)
      TENANT_ID = "00000000-0000-0000-000000000000"
      # Your Service Principal App ID (AadClientId)
      CLIENT = "00000000-0000-0000-000000000000"
      # Your Service Principal Password (AadSecret)
      KEY = "00000000-0000-0000-000000000000"
      # Your Azure Subscription ID (SubscriptionId)
      SUBSCRIPTION_ID = "00000000-0000-0000-000000000000"
      # Your Azure Media Service (AMS) Account Name
      ACCOUNT_NAME = "amsaccount"
      # Your Resource Group Name
      RESOUCE_GROUP_NAME = "amsResourceGroup"

      LOGIN_ENDPOINT = AZURE_PUBLIC_CLOUD.endpoints.active_directory
      RESOURCE = AZURE_PUBLIC_CLOUD.endpoints.active_directory_resource_id

      context = adal.AuthenticationContext(LOGIN_ENDPOINT + "/" + TENANT_ID)
      credentials = AdalAuthentication(
          context.acquire_token_with_client_credentials, RESOURCE, CLIENT, KEY
      )

      # The AMS Client
      # You can now use this object to perform different operations to your AMS account.
      client = AzureMediaServices(credentials, SUBSCRIPTION_ID)

      print("signed in")

      # Now that you are authenticated, you can manipulate the entities.
      # For example, list assets in your AMS account
      print(client.assets.list(RESOUCE_GROUP_NAME, ACCOUNT_NAME).get(0))
      ```

1. Kör filen

## <a name="next-steps"></a>Nästa steg

- Använd [python SDK](https://aka.ms/ams-v3-python-sdk).
- Granska Media Services [Python-referensdokumentationen](/python/api/overview/azure/mediaservices/management).
