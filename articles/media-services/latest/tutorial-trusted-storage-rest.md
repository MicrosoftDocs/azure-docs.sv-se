---
title: Azure Media Services betrodd lagring
description: I den här självstudien får du lära dig hur du aktiverar betrodd lagring för Azure Media Services, använder hanterade identiteter för betrodd lagring och ger Azure-tjänster åtkomst till ett lagrings konto när du använder en brand vägg eller VPN.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: tutorial
ms.date: 2/8/2021
ms.openlocfilehash: 18cb4e3ada94822c2f4cb1ca7675310a37e44e84
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100590655"
---
# <a name="tutorial-media-services-trusted-storage"></a>Självstudie: Media Services betrodd lagring

I den här självstudiekursen lär du dig:

> [!div class="checklist"]
> - Så här aktiverar du betrodd lagring för Azure Media Services
> - Använda hanterade identiteter för betrodd lagring
> - Så här ger du Azure-tjänster åtkomst till ett lagrings konto när du använder nätverks åtkomst kontroll, till exempel en brand vägg eller VPN

Med 2020-05-01-API: et kan du aktivera betrodd lagring genom att associera en hanterad identitet med ett Media Services konto.

>[!NOTE]
>Betrodd lagring är endast tillgängligt i API och är för närvarande inte aktiverat i Azure Portal.

Media Services kan automatiskt komma åt ditt lagrings konto med systemautentisering. Media Services verifierar att Media Services-kontot och lagrings kontot finns i samma prenumeration. Det validerar också att användaren som lägger till associationen har åtkomst till lagrings kontot med Azure Resource Manager RBAC.

Men om du vill använda Network Access Control för att skydda ditt lagrings konto och aktivera betrodd lagring, krävs autentisering av [hanterade identiteter](concept-managed-identities.md) . Den ger Media Services åtkomst till lagrings kontot som har kon figurer ATS med en brand vägg eller en VNet-begränsning via betrodd lagrings åtkomst.

## <a name="overview"></a>Översikt

> [!IMPORTANT]
> Använd 2020-05-01-API: et för alla begär Anden att Media Services.

Detta är de allmänna stegen för att skapa betrodd lagring för Media Services:

1. Skapa en resursgrupp.
1. Skapa ett lagringskonto.
1. Avsök lagrings kontot tills det är klart. När lagrings kontot är klart returnerar begäran tjänstens huvud namns-ID.
1. Hitta ID: t för rollen *Storage BLOB data Contributor* .
1. Anropa auktoriseringsservern och Lägg till en roll tilldelning.
1. Uppdatera Media Services-kontot för att autentisera till lagrings kontot med hjälp av hanterad identitet.
1. Ta bort resurserna om du inte vill fortsätta att använda dem och debitera dem för dem.

<!-- Link to storage role contributor role access differences information -->

## <a name="prerequisites"></a>Förutsättningar

Du behöver en Azure-prenumeration för att komma igång.  Om du inte har en Azure-prenumeration kan du [skapa ett kostnads fritt utvärderings konto](https://azure.microsoft.com/free/).

### <a name="get-your-tenant-id-and-subscription-id"></a>Hämta ditt klient-ID och prenumerations-ID

Om du inte vet hur du ska hämta ditt klient-ID och din prenumerations-ID kan du läsa [så här hittar du klient-ID](how-to-set-azure-tenant.md) och [letar efter klient-ID](how-to-set-azure-tenant.md).

### <a name="create-a-service-principal-and-secret"></a>Skapa ett huvud namn för tjänsten och hemlighet

Om du inte vet hur du skapar ett huvud namn för tjänsten och hemligheten läser du [Hämta autentiseringsuppgifter för att få åtkomst till Media Services API](access-api-howto.md).

## <a name="use-a-rest-client"></a>Använd en REST-klient

Det här skriptet är avsett att användas med en REST-klient, till exempel vad som är tillgängligt i Visual Studio Code-tillägg.  Anpassa den för din utvecklings miljö.

## <a name="set-initial-variables"></a>Ange inledande variabler

Den här delen av skriptet används i en REST-klient. Du kan använda variabler på olika sätt i din utvecklings miljö.

```rest
### AAD details
@tenantId = your tenant ID
@servicePrincipalId = the service principal ID
@servicePrincipalSecret = the service principal secret

### AAD resources
@armResource = https%3A%2F%2Fmanagement.core.windows.net%2F
@graphResource = https%3A%2F%2Fgraph.windows.net%2F
@storageResource = https%3A%2F%2Fstorage.azure.com%2F

### Service endpoints
@armEndpoint = management.azure.com
@graphEndpoint = graph.windows.net
@aadEndpoint = login.microsoftonline.com

### ARM details
@subscription = your subscription id
@resourceGroup = the resource group you'll be creating
@storageName = the name of the storage you'll be creating
@accountName = the name of the account you'll be creating
@resourceLocation = East US (or the location that works best for your region)
```

## <a name="get-a-token-for-azure-resource-manager"></a>Hämta en token för Azure Resource Manager

```rest
// @name getArmToken
POST https://{{aadEndpoint}}/{{tenantId}}/oauth2/token
Accept: application/json
Content-Type: application/x-www-form-urlencoded

resource={{armResource}}&client_id={{servicePrincipalId}}&client_secret={{servicePrincipalSecret}}&grant_type=client_credentials
```

## <a name="get-a-token-for-the-graph-api"></a>Hämta en token för Graph API

Den här delen av skriptet används i en REST-klient. Du kan använda variabler på olika sätt i din utvecklings miljö.

```rest
// @name getGraphToken
POST https://{{aadEndpoint}}/{{tenantId}}/oauth2/token
Accept: application/json
Content-Type: application/x-www-form-urlencoded

resource={{graphResource}}&client_id={{servicePrincipalId}}&client_secret={{servicePrincipalSecret}}&grant_type=client_credentials
```

## <a name="get-the-service-principal-details"></a>Hämta information om tjänstens huvud namn

```rest
// @name getServicePrincipals
GET https://{{graphEndpoint}}/{{tenantId}}/servicePrincipals?$filter=appId%20eq%20'{{servicePrincipalId}}'&api-version=1.6
x-ms-client-request-id: cae3e4f7-17a0-476a-a05a-0dab934ba959
Authorization:  Bearer {{getGraphToken.response.body.access_token}}
```

## <a name="store-the-service-principal-id"></a>Lagra tjänstens huvud namn-ID

```rest
@servicePrincipalObjectId = {{getServicePrincipals.response.body.value[0].objectId}}
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

```rest
// @name createResourceGroup
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}
    ?api-version=2016-09-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
    "location": "{{resourceLocation}}"
}
```

## <a name="create-storage-account"></a>Skapa lagringskonto

```rest
// @name createStorageAccount
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
    "sku": {
    "name": "Standard_GRS"
    },
    "kind": "StorageV2",
    "location": "{{resourceLocation}}",
    "properties": {
    }
}
```

## <a name="get-the-storage-account-status"></a>Hämta lagrings kontots status

Lagrings kontot tar en stund att vara redo så den här begäran avsöker för dess status.  Upprepa denna begäran tills lagrings kontot är klart.

```rest
// @name getStorageAccountStatus
GET {{createStorageAccount.response.headers.Location}}
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

## <a name="get-the-storage-account-details"></a>Hämta information om lagrings kontot

Hämta egenskaperna för lagrings kontot när lagrings kontot är klart.

```rest
// @name getStorageAccount
GET https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

## <a name="get-a-token-for-arm"></a>Hämta en token för ARM

```rest
// @name getStorageToken
POST https://{{aadEndpoint}}/{{tenantId}}/oauth2/token
Accept: application/json
Content-Type: application/x-www-form-urlencoded

resource={{storageResource}}&client_id={{servicePrincipalId}}&client_secret={{servicePrincipalSecret}}&grant_type=client_credentials
```

## <a name="create-a-media-services-account-with-a-system-assigned-managed-identity"></a>Skapa ett Media Services konto med en systemtilldelad hanterad identitet

```rest
// @name createMediaServicesAccount
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
  "identity": {
      "type": "SystemAssigned"
  },
  "properties": {
    "storageAccounts": [
      {
        "id": "{{getStorageAccountStatus.response.body.id}}"
      }
    ],
    "encryption": {
      "type": "SystemKey"
    }
  },
  "location": "{{resourceLocation}}"
}
```

## <a name="get-the-storage-storage-blob-data-role-definition"></a>Hämta BLOB-datarolls definitionen för lagrings lagring

```rest
// @name getStorageBlobDataContributorRoleDefinition
GET https://management.azure.com/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}/providers/Microsoft.Authorization/roleDefinitions?$filter=roleName%20eq%20%27Storage%20Blob%20Data%20Contributor%27&api-version=2015-07-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

### <a name="set-the-storage-role-assignment"></a>Ange lagrings roll tilldelningen

Roll tilldelningen anger att tjänstens huvud namn för Media Services-kontot har lagrings rollens *BLOB-data deltagare*.  Detta kan ta en stund och det är viktigt att vänta eller att Media Services kontot inte är korrekt konfigurerat.

```rest
PUT https://management.azure.com/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}/providers/Microsoft.Authorization/roleAssignments/{{$guid}}?api-version=2020-04-01-preview
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "roleDefinitionId": "/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}/providers/Microsoft.Authorization/roleDefinitions/{{getStorageBlobDataContributorRoleDefinition.response.body.value[0].name}}",
    "principalId": "{{createMediaServicesAccount.response.body.identity.principalId}}"
  }
}
```

## <a name="give-managed-identity-bypass-access-to-the-storage-account"></a>Ge hanterad identitet kringgå åtkomst till lagrings kontot

Den här åtgärden ändrar åtkomsten från Systemhanterad identitet till den hanterade identiteten. På så sätt kan lagrings kontot komma åt lagrings kontot via en brand vägg eftersom Azure-tjänster kan komma åt lagrings kontot, oavsett IP-regler (ACL: er).

Vänta igen, vänta tills rollen har tilldelats i lagrings kontot, eller så är Media Services kontot felaktigt konfigurerat.

```rest
// @name setStorageAccountFirewall
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
    "sku": {
    "name": "Standard_GRS"
    },
    "kind": "StorageV2",
    "location": "{{resourceLocation}}",
    "properties": {
      "minimumTlsVersion": "TLS1_2",
      "networkAcls": {
        "bypass": "AzureServices",
        "virtualNetworkRules": [],
        "ipRules": [],
        "defaultAction": "Deny"
      }
    }
}
```

## <a name="update-the-media-services-account-to-use-the-managed-identity"></a>Uppdatera Media Services kontot för att använda den hanterade identiteten

Den här begäran kan behöva göras om några gånger eftersom lagrings roll tilldelningen kan ta några minuter att sprida.

```rest
// @name updateMediaServicesAccountWithManagedStorageAuth
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
  "identity": {
      "type": "SystemAssigned"
  },
  "properties": {
    "storageAccounts": [
      {
        "id": "{{getStorageAccountStatus.response.body.id}}"
      }
    ],
    "storageAuthentication": "ManagedIdentity",
    "encryption": {
      "type": "SystemKey"
    }
  },
  "location": "{{resourceLocation}}"
}
```

## <a name="test-access"></a>Testa åtkomst

Testa åtkomst genom att skapa en till gång i lagrings kontot.

```rest
// @name createAsset
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}/assets/testasset{{index}}withoutmi?api-version=2018-07-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
}
```

## <a name="delete-resources"></a>Ta bort resurser

Om du inte vill behålla de resurser som du har skapat och fortsätta att debiteras för dem tar du bort dem.

```rest
### Clean up the Storage account
DELETE https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}

### Clean up the Media Services account
DELETE https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}

### Clean up the Media Services account
GET https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

## <a name="next-steps"></a>Nästa steg

[Så här skapar du en till gång](how-to-create-asset.md)
