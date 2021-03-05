---
title: Skapa en anpassad Azure Resource Manager roll och tilldela till tjänstens huvud namn – Azure
description: Den här artikeln innehåller vägledning om hur du skapar en anpassad Azure Resource Manager roll och tilldelar tjänstens huvud namn för video analys på IoT Edge med Azure CLI.
ms.topic: how-to
ms.date: 05/27/2020
ms.openlocfilehash: 80974c111dd451314635d06334766322bc68e437
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102210452"
---
# <a name="create-custom-azure-resource-manager-role-and-assign-to-service-principal"></a>Skapa anpassad Azure Resource Manager roll och tilldela till tjänstens huvud namn

Live video analys på IoT Edge module-instans måste ha ett aktivt Azure Media Services-konto för att det ska fungera korrekt. Relationen mellan live video analys i IoT Edge-modulen och Azure Media Service-kontot upprättas via en uppsättning av modulens dubbla egenskaper. En av dessa dubbla egenskaper är ett [tjänst huvud namn](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) som gör att module-instansen kan kommunicera med och utlösa nödvändiga åtgärder på det Media Services kontot. För att minimera potentiell missbruk och/eller oavsiktlig data exponering från gräns enheten bör det här tjänstens huvud namn ha minst en mängd privilegier.

Den här artikeln visar hur du skapar en anpassad Azure Resource Manager roll med Azure Cloud Shell, som sedan används för att skapa ett huvud namn för tjänsten.

## <a name="prerequisites"></a>Förutsättningar  

Krav för den här artikeln är följande:

* Azure-prenumeration med ägar prenumeration.
* En Azure Active Directory med behörighet att skapa en app och tilldela tjänstens huvud namn till en roll.

Det enklaste sättet att kontrollera om kontot har tillräcklig behörighet är via portalen. Se [Kontrollera behörighet som krävs](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

## <a name="overview"></a>Översikt  

Vi går igenom stegen för att skapa en anpassad roll och länka den till ett huvud namn för tjänsten i följande ordning:

1. Skapa ett medie tjänst konto om du inte redan har ett.
1. Skapa ett huvudnamn för tjänsten.
1. Skapa en anpassad Azure Resource Manager-roll med begränsade privilegier.
1. Begränsa behörigheten för tjänstens huvud namn med hjälp av den anpassade rollen som skapats.
1. Kör ett enkelt test för att se om vi kan begränsa tjänstens huvud namn.
1. Avbilda de parametrar som ska användas i distributions manifesten för IoT Edge.

### <a name="create-a-media-services-account"></a>Skapa ett Media Services-konto  

Om du inte har ett media service-konto kan du skapa ett med hjälp av följande steg.

1. Bläddra till [Cloud Shell](https://shell.azure.com/).
1. Välj "bash" som din miljö i list rutan till vänster i Shell-fönstret

    ![Skärm capturs visar bash som valts från Shell-fönstret.](./media/create-custom-azure-resource-manager-role-how-to/bash.png)
1. Ange din Azure-prenumeration som standard konto med hjälp av följande kommando mal len:
    
    ```
    az account set --subscription " <yourSubscriptionName or yourSubscriptionId>"
    ```
1. Skapa en [resurs grupp](/cli/azure/group#az-group-create) och ett [lagrings konto](/cli/azure/storage/account#az-storage-account-create).
1. Skapa nu ett Azure Media Service-konto med hjälp av följande kommando mal len i Cloud Shell:

    ```
    az ams account create --name <yourAMSAccountName>  --resource-group <yourResouceGroup>  --storage-account <yourStorageAccountName>
    ```

### <a name="create-service-principal"></a>Skapa tjänstens huvudnamn  

Nu ska vi skapa ett nytt huvud namn för tjänsten och länka det till ditt Media Service-konto.

Utan parametrar för autentisering används lösenordsbaserad autentisering med ett slumpmässigt lösen ord för tjänstens huvud namn. Använd följande kommando malation i Cloud Shell:

```
az ams account sp create --account-name < yourAMSAccountName > --resource-group < yourResouceGroup >
```

Det här kommandot ger ett svar så här:

```
{
  "AadClientId": "00000000-0000-0000-0000-000000000000",
  "AadEndpoint": "https://login.microsoftonline.com",
  "AadSecret": "<yourServicePrincipalPassword>",
  "AadTenantId": "00000000-0000-0000-0000-000000000000",
  "AccountName": " <yourAMSAccountName >",
  "ArmAadAudience": "https://management.core.windows.net/",
  "ArmEndpoint": "https://management.azure.com/",
  "Region": "Central US",
  "ResourceGroup": " <yourResouceGroup >",
  "SubscriptionId": "<yourSubscriptionId>"
}

```
1. Utdata för ett huvud namn för tjänsten med lösenordsautentisering innehåller lösen ords nyckeln som i det här fallet är parametern "AadSecret". 

    Se till att du kopierar det här värdet – det går inte att hämta det. Om du glömmer bort lösen ordet [återställer du autentiseringsuppgifterna för tjänstens huvud namn](/cli/azure/create-an-azure-service-principal-azure-cli#reset-credentials).
1. AppId och klient nyckeln visas i utdata som "AadClientId" respektive "AadTenantId". De används vid autentisering av tjänstens huvud namn. Registrera sina värden, men de kan hämtas när som helst med [AZ AD SP-lista](/cli/azure/ad/sp#az-ad-sp-list).

### <a name="create-a-custom-role-definition"></a>Skapa en anpassad roll definition  

Här följer några steg som du bör följa om du vill skapa en anpassad roll:

1. Skapa en JSON-fil för roll definition på det lokala systemet och spara följande text i filen. 
    1. Ersätt < yourSubscriptionId> med ditt Azure-prenumerations-ID
    1. De enda åtgärder som tillåts för den här rollen är:
        * listContainerSas – hjälper modulen att ange URL: er för lagrings behållare med signaturer för delad åtkomst (SAS) för att ladda upp och ladda ned till gångs innehåll.
        * Skriv till gångar – hjälper modulen att skapa eller uppdatera en till gång
        * listEdgePolicies – listar de principer som tillämpas på gräns enheten  
        
        ```
        {
          "Name": "LVAEdge User",
          "IsCustom": true,
          "Description": "Can create assets, view list of containers and view Edge policies",
          "Actions": [
            "Microsoft.Media/mediaservices/assets/listContainerSas/action",
            "Microsoft.Media/mediaservices/assets/write",
            "Microsoft.Media/mediaservices/listEdgePolicies/action"
          ],
          "NotActions": [],
          "DataActions": [],
          "NotDataActions": [],
          "AssignableScopes": [
            "/subscriptions/<yourSubscriptionId>"
          ]
        }
        ```  
          
1. När du har skapat kör du följande kommando mall för att skapa den nya roll definitionen i prenumerationen:
    
    ```
    az role definition create --role-definition "<location of the Role Definition JSON file >"
    ```

    När kommandot har körts visas följande utdata:
    
    ```
    {
      "assignableScopes": [
      "/subscriptions/<yourSubscriptionId>"
      ],
      "description": "Can create assets, view list of containers and view Edge policies",
      "id": "/subscriptions/<yourSubscriptionId>/providers/Microsoft.Authorization/roleDefinitions/<unique name>",
      "name": "<unique name>",
      "permissions": [
        {
          "actions": [
            "Microsoft.Media/mediaservices/assets/listContainerSas/action",
            "Microsoft.Media/mediaservices/assets/write",
            "Microsoft.Media/mediaservices/listEdgePolicies/action",
          ],
          "dataActions": [],
          "notActions": [],
          "notDataActions": []
        }
      ],
      "roleName": " LVAEdge User ",
      "roleType": "CustomRole",
      "type": "Microsoft.Authorization/roleDefinitions"
    }
    ```

### <a name="create-role-assignment"></a>Skapa roll tilldelning  

Om du vill lägga till en roll tilldelning behöver du objectId för det tjänst huvud namn som du vill tilldela den anpassade rollen som du nyss skapade.

Använd följande kommando i Cloud Shell för att hämta objectId:

```
az ad sp show --id "<appId>" | Select-String "objectId"
```

> [!NOTE]
> `<appId>` kan hämtas från utdata från steget [skapa tjänstens huvud namn](#create-service-principal) .

Kommandot ovan kommer att skriva ut objectId för tjänstens huvud namn. 

```
“objectId” : “<yourObjectId>”,
```

Använd [AZ roll tilldelning skapa kommando](/cli/azure/role/assignment#az-role-assignment-create) mal len för länken till den anpassade rollen med tjänstens huvud namn:

```
az role assignment create --role “LVAEdge User” --assignee-object-id < objectId>    
```

Parametrar:

|Parametrar|Description| 
|---|---|
|--roll |Namn eller ID för anpassad roll. I vårt exempel: "LVAEdge User".|
|--tilldelad person-objekt-ID|Objekt-ID för tjänstens huvud namn som du kommer att använda.|

Resultatet kommer att se ut så här:

```
{
  "canDelegate": null,
  "id": "/subscriptions/<yourSubscriptionId>/providers/Microsoft.Authorization/roleAssignments/<yourCustomRoleId>",
  "name": "<yourCustomRoleId>",
  "principalId": "<yourServicePrincipalId>",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<yourSubscriptionId>/providers/Microsoft.Authorization/roleDefinitions/<yourRoleDefinitionId>",
  "scope": "/subscriptions/<yourSubscriptionId>",
  "type": "Microsoft.Authorization/roleAssignments"
} 
```

### <a name="confirm-that-role-assignment-happened"></a>Bekräfta att roll tilldelningen har gjorts

Du kan kontrol lera att tjänstens huvud namn nu är länkat till den anpassade roll som vi nyss skapade genom att köra följande kommando:

```
az role assignment list  --assignee < objectId>
```

Resultatet bör se ut så här:

```
[
  {
    "canDelegate": null,
    "id": "/subscriptions/xxx/providers/Microsoft.Authorization/roleAssignments/00000000-0000-0000-0000-000000000000",
    "name": "00000000-0000-0000-0000-000000000000",
    "principalId": "<yourServicePrincipalID>",
    "principalName": "<yourServicePrincipalName>",
    "principalType": "ServicePrincipal",
    "roleDefinitionId": "/subscriptions/xxx/providers/Microsoft.Authorization/roleDefinitions/zzz",
    "roleDefinitionName": "LVAEdge User",
    "scope": "/subscriptions/<yourSubscription ID>",
    "type": "Microsoft.Authorization/roleAssignments
  }
]  
```
 
Leta efter "roleDefinitionName" och se att dess värde är inställt på "LVAEdge User". 

Detta bekräftar att vi har länkat den anpassade användar rollen med det tjänst huvud namn som används för programmet.

### <a name="test-the-service-principal-access-control"></a>Testa tjänstens huvud princip för åtkomst kontroll

1. Logga in med tjänstens huvud namn. För detta behöver vi tre informations objekt för Azure Active Directory för att ge oss rätt åtkomsttoken som vi kan hämta från utdata från avsnittet [skapa tjänstens huvud namn](#create-service-principal) :
    1. AadClientID 
    1. AadSecret
    1. AadTenantId
1. Nu kan du försöka logga in med kommando mal len nedan:
    
    ```
    az login --service-principal --username "< AadClientID>" --password " <AadSecret>" --tenant "<AadTenantId>"
    ```
3.  Nu ska vi se om inloggningen är begränsad till tjänstens huvud namn med rollen "LVAEdge User" genom att försöka skapa en resurs grupp för att kontrol lera att den Miss lyckas. Kör följande kommando i Cloud Shell:

    ```
    az group create --location "central us" --name "testresourcegroup"
    ```

    Kommandot bör inte fungera och kommer att se ut så här:
    
    ```
    The client '<AadClientId>' with object id '<AadClientId>' does not have authorization to perform action 'Microsoft.Resources/subscriptions/resourcegroups/write' over scope '/subscriptions/<yourSubscriptionId>/resourcegroups/testresourcegroup' or the scope is invalid. If access was recently granted, please refresh your credentials.
    ```

## <a name="next-steps"></a>Nästa steg  

Observera följande värden i den här artikeln. Dessa värden krävs för att du ska kunna konfigurera de dubbla egenskaperna för live video analys i IoT Edge modul, se [modul dubbla JSON-scheman](module-twin-configuration-schema.md).

| Variabel från den här artikeln|Dubbelt egenskaps namn för video analys i real tid på IoT Edge|
|---|---|
|AadSecret |    aadServicePrincipalPassword|
|AadTenantId |  aadTenantId|
|AadClientId    |aadServicePrincipalAppId|
