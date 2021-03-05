---
title: Utveckla för Azure NetApp Files med REST API | Microsoft Docs
description: REST API för Azure NetApp Files-tjänsten definierar HTTP-åtgärder för resurser, till exempel NetApp-konto, kapacitets uppsättning, volymer och ögonblicks bilder.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/02/2020
ms.author: b-juche
ms.openlocfilehash: c5993dc1dc645319e272ab310a97bc3ff8ac495d
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102174248"
---
# <a name="develop-for-azure-netapp-files-with-rest-api"></a>Utveckla för Azure NetApp Files med REST-API 

REST API för Azure NetApp Files-tjänsten definierar HTTP-åtgärder mot resurser, till exempel NetApp-konto, kapacitets uppsättning, volymer och ögonblicks bilder. Den här artikeln hjälper dig att komma igång med att använda Azure NetApp Files REST API.

## <a name="azure-netapp-files-rest-api-specification"></a>Azure NetApp Files REST API specifikation

REST APIs specifikationen för Azure NetApp Files publiceras via [GitHub](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager):

`https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager`


## <a name="access-the-azure-netapp-files-rest-api"></a>Få åtkomst till Azure NetApp Files REST API  

1. [Installera Azure CLI](/cli/azure/install-azure-cli) om du inte redan har gjort det.
2. Skapa ett huvud namn för tjänsten i din Azure Active Directory (Azure AD):
   1. Kontrol lera att du har [tillräcklig behörighet](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

   2. Ange följande kommando i Azure CLI: 
    
        ```azurecli
        az ad sp create-for-rbac --name $YOURSPNAMEGOESHERE
        ```

      Kommandots utdata liknar följande exempel:  

        ```output
        { 
            "appId": "appIDgoeshere", 
            "displayName": "APPNAME", 
            "name": "http://APPNAME", 
            "password": "supersecretpassword", 
            "tenant": "tenantIDgoeshere" 
        } 
        ```

      Behåll kommandots utdata.  Du kommer att behöva `appId` `password` värdena, och `tenant` . 

3. Begär en OAuth-åtkomsttoken:

    I exemplen i den här artikeln används sväng. Du kan också använda olika API-verktyg som [Postman](https://www.getpostman.com/), [sömnlöshet](https://insomnia.rest/)och [Paw](https://paw.cloud/).  

    Ersätt variablerna i följande exempel med kommandots utdata från steg 2 ovan. 
    
    ```azurecli
    curl -X POST -d 'grant_type=client_credentials&client_id=[APP_ID]&client_secret=[PASSWORD]&resource=https%3A%2F%2Fmanagement.azure.com%2F' https://login.microsoftonline.com/[TENANT_ID]/oauth2/token
    ```

    Utdata ger en åtkomsttoken som liknar följande exempel:

    `eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSIsImtpZCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSJ9`

    Den visade token är giltig i 3600 sekunder. Efter det måste du begära en ny token. 
    Spara token i en text redigerare.  Du behöver den för nästa steg.

4. Skicka ett test samtal och inkludera token för att verifiera åtkomsten till REST API:

    ```azurecli
    curl -X GET -H "Authorization: Bearer [TOKEN]" -H "Content-Type: application/json" https://management.azure.com/subscriptions/[SUBSCRIPTION_ID]/providers/Microsoft.Web/sites?api-version=2019-11-01
    ```

## <a name="examples-using-the-api"></a>Exempel som använder API: et  

I den här artikeln används följande URL för bas linjen för begär Anden. URL: en pekar till roten i Azure NetApp Files namn området. 

`https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts?api-version=2019-11-01`

Du bör ersätta `SUBIDGOESHERE` värdena och `RESOURCEGROUPGOESHERE` i följande exempel med dina egna värden. 

### <a name="get-request-examples"></a>GET Request-exempel

Du kan använda en GET-begäran för att skicka frågor mot objekt i Azure NetApp Files i en prenumeration, som i följande exempel: 

```azurecli
#get NetApp accounts 
curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts?api-version=2019-11-01
```

```azurecli
#get capacity pools for NetApp account 
curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools?api-version=2019-11-01
```

```azurecli
#get volumes in NetApp account & capacity pool 
curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes?api-version=2019-11-01
```

```azurecli
#get snapshots for a volume 
curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/VOLUMEGOESHERE/snapshots?api-version=2019-11-01
```

### <a name="put-request-examples"></a>Lägg till exempel på begäran

Du kan använda en skicka-begäran för att skapa nya objekt i Azure NetApp Files, vilket visas i följande exempel. Bröd texten i beställaren kan innehålla de JSON-formaterade data för ändringarna. Den måste inkluderas i kommandot spiral som text eller referenser som en fil. Om du vill referera till texten som en fil sparar du JSON-exemplet till en fil och lägger till `-d @<filename>` i kommandot vändning.

```azurecli
#create a NetApp account  
curl -d @<filename> -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE?api-version=2019-11-01
```

```azurecli
#create a capacity pool  
curl -d @<filename> -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE?api-version=2019-11-01
```

```azurecli
#create a volume  
curl -d @<filename> -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/MYNEWVOLUME?api-version=2019-11-01
```

```azurecli
 #create a volume snapshot  
curl -d @<filename> -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/MYNEWVOLUME/Snapshots/SNAPNAME?api-version=2019-11-01
```

### <a name="json-examples"></a>JSON-exempel

I följande exempel visas hur du skapar ett NetApp-konto:

```json
{ 
    "name": "MYNETAPPACCOUNT", 
    "type": "Microsoft.NetApp/netAppAccounts", 
    "location": "westus2", 
    "properties": { 
        "name": "MYNETAPPACCOUNT" 
    }
} 
```

I följande exempel visas hur du skapar en pool för kapacitet: 

```json
{
    "name": "MYNETAPPACCOUNT/POOLNAME",
    "type": "Microsoft.NetApp/netAppAccounts/capacityPools",
    "location": "westus2",
    "properties": {
        "name": "POOLNAME",
        "size": "4398046511104",
        "serviceLevel": "Premium"
    }
}
```

I följande exempel visas hur du skapar en ny volym. (Standard protokollet för volymen är NFSV3.) 

```json
{
    "name": "MYNEWVOLUME",
    "type": "Microsoft.NetApp/netAppAccounts/capacityPools/volumes",
    "location": "westus2",
    "properties": {
        "serviceLevel": "Premium",
        "usageThreshold": "322122547200",
        "creationToken": "MY-FILEPATH",
        "snapshotId": "",
        "subnetId": "/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.Network/virtualNetworks/VNETGOESHERE/subnets/MYDELEGATEDSUBNET.sn"
         }
}
```

I följande exempel visas hur du skapar en ögonblicks bild av en volym: 

```json
{
    "name": "apitest2/apiPool01/apiVol01/snap02",
    "type": "Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/Snapshots",
    "location": "westus2",
    "properties": {
         "name": "snap02",
        "fileSystemId": "0168704a-bbec-da81-2c29-503825fe7420"
    }
}
```

> [!NOTE] 
> Du måste ange `fileSystemId` för att skapa en ögonblicks bild.  Du kan hämta `fileSystemId` värdet med en get-begäran till en volym. 

## <a name="next-steps"></a>Nästa steg

[Se referens för Azure NetApp Files REST API](/rest/api/netapp/)