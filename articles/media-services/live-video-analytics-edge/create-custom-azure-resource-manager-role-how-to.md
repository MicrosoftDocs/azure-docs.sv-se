---
title: Skapa en Azure Resource Manager roll och tilldela till tjänstens huvudnamn – Azure
description: Den här artikeln innehåller råd om hur du skapar en anpassad Azure Resource Manager roll och tilldelar till tjänstens huvudnamn för Live Video Analytics på IoT Edge med Azure CLI.
ms.topic: how-to
ms.date: 05/27/2020
ms.openlocfilehash: 6c33f6703522fc0b28237e22c16c96587467df40
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788519"
---
# <a name="create-custom-azure-resource-manager-role-and-assign-to-service-principal"></a>Skapa en Azure Resource Manager roll och tilldela till tjänstens huvudnamn

Live Video Analytics på IoT Edge-modulinstansen ett aktivt Azure Media Services konto för att den ska fungera korrekt. Relationen mellan Live Video Analytics på IoT Edge och Azure Media Service-kontot upprättas via en uppsättning egenskaper för modultvilling. En av dessa tvillingegenskaper är [ett](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) huvudnamn för tjänsten som gör att modulinstansen kan kommunicera med och utlösa nödvändiga åtgärder på Media Services kontot. För att minimera eventuellt missbruk och/eller oavsiktlig dataexponering från gränsenhet bör tjänstens huvudnamn ha minsta möjliga behörighet.

Den här artikeln visar stegen för att skapa en anpassad Azure Resource Manager med Azure Cloud Shell, som sedan används för att skapa ett huvudnamn för tjänsten.

## <a name="prerequisites"></a>Förutsättningar  

Kraven för den här artikeln är följande:

* Azure-prenumeration med ägarprenumeration.
* En Azure Active Directory med behörighet att skapa en app och tilldela tjänstens huvudnamn till en roll.

Det enklaste sättet att kontrollera om kontot har tillräcklig behörighet är via portalen. Se [Kontrollera behörighet som krävs](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

## <a name="overview"></a>Översikt  

Vi går igenom stegen för att skapa en anpassad roll och länka den till tjänstens huvudnamn i följande ordning:

1. Skapa ett Media Service-konto om du inte redan har ett.
1. Skapa ett huvudnamn för tjänsten.
1. Skapa en Azure Resource Manager roll med begränsad behörighet.
1. "Begränsa" behörigheter för tjänstens huvudnamn med hjälp av den anpassade roll som skapats.
1. Kör ett enkelt test för att se om vi kan begränsa tjänstens huvudnamn.
1. Samla in de parametrar som ska användas i IoT Edge distributionsmanifest.

### <a name="create-a-media-services-account"></a>Skapa ett Media Services-konto  

Om du inte har ett Media Service-konto kan du använda följande steg för att skapa ett.

1. Bläddra till [Cloud Shell](https://shell.azure.com/).
1. Välj "Bash" som miljö i listrutan till vänster i gränssnittsfönstret

    ![Skärmbilder visar Bash valt från gränssnittsfönstret.](./media/create-custom-azure-resource-manager-role-how-to/bash.png)
1. Ange din Azure-prenumeration som standardkonto med hjälp av följande kommandomall:
    
    ```
    az account set --subscription " <yourSubscriptionName or yourSubscriptionId>"
    ```
1. Skapa en [resursgrupp och](/cli/azure/group#az_group_create) ett [lagringskonto](/cli/azure/storage/account#az_storage_account_create).
1. Skapa nu ett Azure Media Service-konto med hjälp av följande kommandomall i Cloud Shell:

    ```
    az ams account create --name <yourAMSAccountName>  --resource-group <yourResouceGroup>  --storage-account <yourStorageAccountName>
    ```

### <a name="create-service-principal"></a>Skapa tjänstens huvudnamn  

Nu ska vi skapa ett nytt huvudnamn för tjänsten och länka det till ditt Media Service-konto.

Utan autentiseringsparametrar används lösenordsbaserad autentisering med ett slumpmässigt lösenord för tjänstens huvudnamn. I Cloud Shell du följande kommandomall:

```
az ams account sp create --account-name < yourAMSAccountName > --resource-group < yourResouceGroup >
```

Det här kommandot genererar ett svar som liknar följande:

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
1. Utdata för tjänstens huvudnamn med lösenordsautentisering innehåller lösenordsnyckeln som i det här fallet är parametern "AadSecret". 

    Se till att du kopierar det här värdet – det kan inte hämtas. Om du glömmer lösenordet återställer du [autentiseringsuppgifterna för tjänstens huvudnamn.](/cli/azure/create-an-azure-service-principal-azure-cli#reset-credentials)
1. AppId och klientnyckel visas i utdata som "AadClientId" respektive "AadTenantId". De används i autentisering med tjänstens huvudnamn. Registrera sina värden, men de kan hämtas när som helst med [az ad sp list](/cli/azure/ad/sp#az_ad_sp_list).

### <a name="create-a-custom-role-definition"></a>Skapa en anpassad rolldefinition  

Om du vill skapa en anpassad roll följer du dessa steg:

1. Skapa en JSON-fil för rolldefinition på det lokala systemet och spara följande text i filen. 
    1. Ersätt < dittPrenumerationId-> ditt Prenumerations-ID för Azure
    1. De enda åtgärder som tillåts för den här rollen är:
        * listContainerSas – hjälper modulen att lista lagringscontainerns URL:er med signaturer för delad åtkomst (SAS) för att ladda upp och ladda ned tillgångsinnehåll.
        * Skriva tillgångar – hjälper modulen att skapa eller uppdatera en tillgång
        * listEdgePolicies – visar en lista över de principer som tillämpas på gränsenheterna  
        
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
          
1. När du har skapat den kör du följande kommandomall för att skapa den nya rolldefinitionen i prenumerationen:
    
    ```
    az role definition create --role-definition "<location of the Role Definition JSON file >"
    ```

    När kommandot har lyckats visas följande utdata:
    
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

### <a name="create-role-assignment"></a>Skapa rolltilldelning  

Om du vill lägga till en rolltilldelning behöver du objectId för tjänstens huvudnamn som du vill tilldela den anpassade rollen som du nyss skapade.

Använd följande kommando i Cloud Shell för att hämta objectId:

```
az ad sp show --id "<appId>" | Select-String "objectId"
```

> [!NOTE]
> `<appId>`kan hämtas från utdata från steget [Skapa tjänstens huvudnamn.](#create-service-principal)

Kommandot ovan skriver ut objectId för tjänstens huvudnamn. 

```
“objectId” : “<yourObjectId>”,
```

Använd [kommandomallen az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) till länken till den anpassade rollen med tjänstens huvudnamn:

```
az role assignment create --role “LVAEdge User” --assignee-object-id < objectId>    
```

Parametrar:

|Parametrar|Description| 
|---|---|
|--role |Anpassat rollnamn eller ID. I vårt fall: "EDGEEdge User".|
|--assignee-object-id|Objekt-ID för tjänstens huvudnamn som du ska använda.|

Resultatet ser ut så här:

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

### <a name="confirm-that-role-assignment-happened"></a>Bekräfta att rolltilldelningen har skett

Bekräfta att tjänstens huvudnamn nu är länkat till den anpassade rollen som vi nyss skapade genom att köra följande kommando:

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
 
Leta efter "roleDefinitionName" och se att dess värde är inställt på "GAEdge User". 

Detta bekräftar att vi har länkat den anpassade användarrollen med tjänstens huvudnamn som används för vårt program.

### <a name="test-the-service-principal-access-control"></a>Testa åtkomstkontrollen för tjänstens huvudnamn

1. Logga in med tjänstens huvudnamn. För detta behöver vi tre uppgifter för Azure Active Directory för att ge oss rätt åtkomsttoken som vi kan hämta från utdata från steget [Skapa tjänstens huvudnamn:](#create-service-principal)
    1. AadClientID 
    1. AadSecret
    1. AadTenantId
1. Nu ska vi försöka logga in med hjälp av kommandomallen nedan:
    
    ```
    az login --service-principal --username "< AadClientID>" --password " <AadSecret>" --tenant "<AadTenantId>"
    ```
3.  Nu ska vi se om inloggningen är begränsad till tjänstens huvudnamn med rollen "LOGGNINGEdge-användare" genom att försöka skapa en resursgrupp för att se till att den misslyckas. Kör följande kommando i Cloud Shell:

    ```
    az group create --location "central us" --name "testresourcegroup"
    ```

    Det här kommandot bör misslyckas och ser ut så här:
    
    ```
    The client '<AadClientId>' with object id '<AadClientId>' does not have authorization to perform action 'Microsoft.Resources/subscriptions/resourcegroups/write' over scope '/subscriptions/<yourSubscriptionId>/resourcegroups/testresourcegroup' or the scope is invalid. If access was recently granted, please refresh your credentials.
    ```

## <a name="next-steps"></a>Nästa steg  

Observera följande värden från den här artikeln. Dessa värden krävs för att du ska kunna konfigurera tvillingegenskaperna för Live Video Analytics på IoT Edge modulen. Se [JSON-schema för modultvilling.](module-twin-configuration-schema.md)

| Variabel från den här artikeln|Tvillingegenskapsnamn för Live Video Analytics på IoT Edge|
|---|---|
|AadSecret |    aadServicePrincipalPassword|
|AadTenantId |  aadTenantId|
|AadClientId    |aadServicePrincipalAppId|
