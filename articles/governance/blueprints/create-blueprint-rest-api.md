---
title: 'Snabb start: skapa en skiss med REST API'
description: I den här snabb starten använder du Azure-ritningar för att skapa, definiera och distribuera artefakter med hjälp av REST API.
ms.date: 01/27/2021
ms.topic: quickstart
ms.openlocfilehash: eaf6dbb2ff14106ba8d2798d86a8f093855de85e
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98915649"
---
# <a name="quickstart-define-and-assign-an-azure-blueprint-with-rest-api"></a>Snabb start: definiera och tilldela en Azure Blueprint med REST API

Genom att lära dig hur du skapar och tilldelar ritningar kan du definiera vanliga mönster för att utveckla återanvändbara och snabbt distribuerade konfigurationer baserat på Azure Resource Manager mallar (ARM-mallar), principer, säkerhet och mycket mer. I den här självstudien får du lära dig att använda Azure Blueprint för att utföra några av de vanliga uppgifter som rör generering, publicering och tilldelning av en skiss i din organisation. Du lär dig till exempel att:

## <a name="prerequisites"></a>Krav

- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free) innan du börjar.
- Registrera `Microsoft.Blueprint` resurs leverantören. Anvisningar finns i [resurs leverantörer och typer](../../azure-resource-manager/management/resource-providers-and-types.md).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="getting-started-with-rest-api"></a>Komma igång med REST API

Om du inte är bekant med REST API börjar du med att gå igenom [referensen för Azure REST API](/rest/api/azure/) så att du förstår hur REST-API:et fungerar, särskilt begärande-URI och begärandetext. I den här artikeln används dessa begrepp för att förklara hur du arbetar med Azure-skisser och artikeln förutsätter att du har grundläggande kunskap om dem. Verktyg som [ARMClient](https://github.com/projectkudu/ARMClient) och andra kan hantera auktorisering automatiskt och rekommenderas för nybörjare.

För specifikationerna för Azure-ritningar, se [Azure-ritningar REST API](/rest/api/blueprints/).

### <a name="rest-api-and-powershell"></a>REST API och PowerShell

Om du inte redan har ett verktyg för att göra REST API-anrop kan du använda PowerShell för dessa anvisningar. Följande är ett exempelhuvud för autentisering med Azure. Generera ett autentiseringshuvud, även kallat en **ägartoken**, och ange REST API-URI:n som du vill ansluta till med valfria parametrar eller en **begärandetext**:

```azurepowershell-interactive
# Log in first with Connect-AzAccount if not using Cloud Shell

$azContext = Get-AzContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
$authHeader = @{
    'Content-Type'='application/json'
    'Authorization'='Bearer ' + $token.AccessToken
}

# Invoke the REST API
$restUri = 'https://management.azure.com/subscriptions/{subscriptionId}?api-version=2020-01-01'
$response = Invoke-RestMethod -Uri $restUri -Method Get -Headers $authHeader
```

Ersätt `{subscriptionId}` i variabeln **$restUri** ovan för att få information om din prenumeration. Variabeln $response innehåller resultatet från cmdleten `Invoke-RestMethod`, som kan parsas med cmdletar som [ConvertFrom-Json](/powershell/module/microsoft.powershell.utility/convertfrom-json). Om REST API-tjänstslutpunkten förväntar sig en **begärandetext** lägger du till en JSON-formaterad variabel till `-Body`-parametern för `Invoke-RestMethod`.

## <a name="create-a-blueprint"></a>Skapa en skiss

Det första steget när du definierar ett standardmönster för efterlevnad är att skapa en skiss från de tillgängliga resurserna. Vi skapar en skiss med namnet ”MyBlueprint” för att konfigurera roll- och principtilldelningar för prenumerationen. Sedan lägger vi till en resurs grupp, en ARM-mall och en roll tilldelning i resurs gruppen.

> [!NOTE]
> När du använder REST-API:et skapas _skissobjektet_ först. För varje _artefakt_ som ska läggas till som har parametrar, måste parametrarna definieras i förväg i den första _skissen_.

I varje REST API-URI finns det variabler som används och som du måste ersätta med egna värden:

- `{YourMG}` – Ersätt med ID för din hanteringsgrupp
- `{subscriptionId}` – Ersätt med ditt prenumerations-ID

> [!NOTE]
> Ritningar kan också skapas på prenumerations nivå. Ett exempel finns i [skapa skiss vid prenumerations exempel](/rest/api/blueprints/blueprints/createorupdate#subscriptionblueprint).

1. Skapa det första _skissobjektet_. **Begärandetexten** innehåller egenskaper för skissen, de resursgrupper som ska skapas och alla parametrar på skissnivå. Parametrarna anges vid tilldelning och används av artefakterna som lagts till i senare steg.

   - REST API-URI

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2018-11-01-preview
     ```

   - Begärandetext

     ```json
     {
         "properties": {
             "description": "This blueprint sets tag policy and role assignment on the subscription, creates a ResourceGroup, and deploys a resource template and role assignment to that ResourceGroup.",
             "targetScope": "subscription",
             "parameters": {
                 "storageAccountType": {
                     "type": "string",
                     "metadata": {
                         "displayName": "storage account type.",
                         "description": null
                     }
                 },
                 "tagName": {
                     "type": "string",
                     "metadata": {
                         "displayName": "The name of the tag to provide the policy assignment.",
                         "description": null
                     }
                 },
                 "tagValue": {
                     "type": "string",
                     "metadata": {
                         "displayName": "The value of the tag to provide the policy assignment.",
                         "description": null
                     }
                 },
                 "contributors": {
                     "type": "array",
                     "metadata": {
                         "description": "List of AAD object IDs that is assigned Contributor role at the subscription"
                     }
                 },
                 "owners": {
                     "type": "array",
                     "metadata": {
                         "description": "List of AAD object IDs that is assigned Owner role at the resource group"
                     }
                 }
             },
             "resourceGroups": {
                 "storageRG": {
                     "description": "Contains the resource template deployment and a role assignment."
                 }
             }
         }
     }
     ```

1. Lägg till rolltilldelningen för prenumerationen. **Begärandetexten** definierar _typen_ av artefakt, egenskaperna justeras efter rolldefinitions-ID:t och huvudidentiteterna skickas som en matris med värden. I exemplet nedan konfigureras huvudidentiteterna som beviljas den angivna rollen till en parameter som anges under skisstilldelningen. I det här exemplet används den inbyggda rollen _Deltagare_ med ett GUID på `b24988ac-6180-42a0-ab88-20f7382dd24c`.

   - REST API-URI

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleContributor?api-version=2018-11-01-preview
     ```

   - Begärandetext

     ```json
     {
         "kind": "roleAssignment",
         "properties": {
             "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
             "principalIds": "[parameters('contributors')]"
         }
     }
     ```

1. Lägg till principtilldelning för prenumerationen. **Begärandetexten** definierar _typen_ av artefakt, egenskaperna som justeras efter en princip- eller initiativdefinition, samt konfigurerar principtilldelningen så att de definierade skissparametrarna som konfigureras under skisstilldelningen används. I det här exemplet används den inbyggda principen _Lägg till tagg och standardvärdet i resursgrupper_ med ett GUID på `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71`.

   - REST API-URI

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyTags?api-version=2018-11-01-preview
     ```

   - Begärandetext

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
             "description": "Apply tag and its default value to resource groups",
             "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
             "parameters": {
                 "tagName": {
                     "value": "[parameters('tagName')]"
                 },
                 "tagValue": {
                     "value": "[parameters('tagValue')]"
                 }
             }
         }
     }
     ```

1. Lägg till en till principtilldelning för lagringstaggen (genom att återanvända parametern _storageAccountType_) för prenumerationen. Den här ytterligare principtilldelningsartefakten visar att en parameter som definierats för skissen kan användas av mer än en artefakt. I exemplet används **storageAccountType** för att ange en tagg på resursgruppen. Det här värdet anger information om lagringskontot som skapas i nästa steg. I det här exemplet används den inbyggda principen _Lägg till tagg och standardvärdet i resursgrupper_ med ett GUID på `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71`.

   - REST API-URI

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyStorageTags?api-version=2018-11-01-preview
     ```

   - Begärandetext

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
             "description": "Apply storage tag and the parameter also used by the template to resource groups",
             "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
             "parameters": {
                 "tagName": {
                     "value": "StorageType"
                 },
                 "tagValue": {
                     "value": "[parameters('storageAccountType')]"
                 }
             }
         }
     }
     ```

1. Lägg till en mall under resursgruppen. **Begär ande texten** för en arm-mall innehåller den normala JSON-komponenten i mallen och definierar mål resurs gruppen med **egenskaperna. resourceGroup**. Mallen återanvänder även skissparametrarna **storageAccountType**, **tagName** och **tagValue** genom att dem till mallen. Skissparametrarna är tillgängliga för mallen genom att **properties.parameters** definieras, och inuti mallens JSON används nyckel/värde-paret för att mata in värdet. Namnen på skiss- och mallparametrarna kan vara samma, men är olika i exemplet för att illustrera hur varje parameter skickas från skissen till mallartefakten.

   - REST API-URI

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/templateStorage?api-version=2018-11-01-preview
     ```

   - Begärandetext

     ```json
     {
         "kind": "template",
         "properties": {
             "template": {
                 "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                 "contentVersion": "1.0.0.0",
                 "parameters": {
                     "storageAccountTypeFromBP": {
                         "type": "string",
                         "defaultValue": "Standard_LRS",
                         "allowedValues": [
                             "Standard_LRS",
                             "Standard_GRS",
                             "Standard_ZRS",
                             "Premium_LRS"
                         ],
                         "metadata": {
                             "description": "Storage Account type"
                         }
                     },
                     "tagNameFromBP": {
                         "type": "string",
                         "defaultValue": "NotSet",
                         "metadata": {
                             "description": "Tag name from blueprint"
                         }
                     },
                     "tagValueFromBP": {
                         "type": "string",
                         "defaultValue": "NotSet",
                         "metadata": {
                             "description": "Tag value from blueprint"
                         }
                     }
                 },
                 "variables": {
                     "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
                 },
                 "resources": [{
                     "type": "Microsoft.Storage/storageAccounts",
                     "name": "[variables('storageAccountName')]",
                     "apiVersion": "2016-01-01",
                     "tags": {
                        "[parameters('tagNameFromBP')]": "[parameters('tagValueFromBP')]"
                     },
                     "location": "[resourceGroups('storageRG').location]",
                     "sku": {
                         "name": "[parameters('storageAccountTypeFromBP')]"
                     },
                     "kind": "Storage",
                     "properties": {}
                 }],
                 "outputs": {
                     "storageAccountSku": {
                         "type": "string",
                         "value": "[variables('storageAccountName')]"
                     }
                 }
             },
             "resourceGroup": "storageRG",
             "parameters": {
                 "storageAccountTypeFromBP": {
                     "value": "[parameters('storageAccountType')]"
                 },
                 "tagNameFromBP": {
                     "value": "[parameters('tagName')]"
                 },
                 "tagValueFromBP": {
                     "value": "[parameters('tagValue')]"
                 }
             }
         }
     }
     ```

1. Lägg till rolltilldelning under resursgruppen. På liknande sätt som i föregående rolltilldelningspost använder exemplet nedan definitionsidentifieraren för rollen **Ägare** och tilldelar den en annan parameter från skissen. I det här exemplet används den inbyggda rollen _Ägare_ med ett GUID på `8e3af657-a8ff-443c-a75c-2fe8c4bcb635`.

   - REST API-URI

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleOwner?api-version=2018-11-01-preview
     ```

   - Begärandetext

     ```json
     {
         "kind": "roleAssignment",
         "properties": {
             "resourceGroup": "storageRG",
             "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
             "principalIds": "[parameters('owners')]"
         }
     }
     ```

## <a name="publish-a-blueprint"></a>Publicera en skiss

Nu när artefakter har lagts till i skissen är det dags att publicera den. När den har publicerats kan den tilldelas till en prenumeration.

- REST API-URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/versions/{BlueprintVersion}?api-version=2018-11-01-preview
  ```

Värdet för `{BlueprintVersion}` är en sträng med bokstäver, siffror och bindestreck (inga blanksteg eller andra specialtecken) med en högsta längd på 20 tecken. Använd något unikt och beskrivande som **v20180622-135541**.

## <a name="assign-a-blueprint"></a>Tilldela en skiss

När en skiss har publicerats med hjälp av REST API kan den tilldelas till en prenumeration. Tilldela skissen som du skapade till någon av prenumerationerna i din hierarki med hanteringsgrupper. Om skissen sparas till en prenumeration kan den endast tilldelas till den prenumerationen. **Begärandetexten** anger vilken skiss som tilldelas, anger namnet och platsen för resursgrupper i skissdefinitionen och anger alla parametrar som definierades i skissen och som används av en eller flera kopplade artefakter.

I varje REST API-URI finns det variabler som används och som du måste ersätta med egna värden:

- `{tenantId}` – Ersätt med ditt klientorganisations-ID
- `{YourMG}` – Ersätt med ID för din hanteringsgrupp
- `{subscriptionId}` – Ersätt med ditt prenumerations-ID

1. Ge Azure Blueprint-tjänstobjektet rollen **Ägare** för målprenumerationen. AppId är static ( `f71766dc-90d9-4b7d-bd9d-4499c4331c3f` ), men tjänstens huvud namns-ID varierar av klienten. Information kan begäras för din klientorganisation med hjälp av följande REST API. Den använder [Azure Active Directory Graph API](../../active-directory/develop/active-directory-graph-api.md), som har olika auktorisering.

   - REST API-URI

     ```http
     GET https://graph.windows.net/{tenantId}/servicePrincipals?api-version=1.6&$filter=appId eq 'f71766dc-90d9-4b7d-bd9d-4499c4331c3f'
     ```

1. Kör skissdistributionen genom att tilldela den till en prenumeration. Eftersom parametrarna **contributors** och **owners** kräver en matris med objectIds för huvudobjekten för att rolltilldelningen ska beviljas använder du [Azure Active Directory Graph API](../../active-directory/develop/active-directory-graph-api.md) för att samla in objectIds för användning i **begärandetexten** för dina egna användare, grupper eller tjänstobjekt.

   - REST API-URI

     ```http
     PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2018-11-01-preview
     ```

   - Begärandetext

     ```json
     {
         "properties": {
             "blueprintId": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint",
             "resourceGroups": {
                 "storageRG": {
                     "name": "StorageAccount",
                     "location": "eastus2"
                 }
             },
             "parameters": {
                 "storageAccountType": {
                     "value": "Standard_GRS"
                 },
                 "tagName": {
                     "value": "CostCenter"
                 },
                 "tagValue": {
                     "value": "ContosoIT"
                 },
                 "contributors": {
                     "value": [
                         "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
                         "38833b56-194d-420b-90ce-cff578296714"
                     ]
                 },
                 "owners": {
                     "value": [
                         "44254d2b-a0c7-405f-959c-f829ee31c2e7",
                         "316deb5f-7187-4512-9dd4-21e7798b0ef9"
                     ]
                 }
             }
         },
         "identity": {
             "type": "systemAssigned"
         },
         "location": "westus"
     }
     ```

   - Användartilldelad hanterad identitet

     En skisstilldelning kan även använda en [användartilldelad hanterad identitet](../../active-directory/managed-identities-azure-resources/overview.md).
     I det här fallet ändras **identitets** delen av begärandetexten på följande sätt. Ersätt `{yourRG}` och `{userIdentity}` med ditt resursgruppnamn och namnet på din användartilldelade hanterade identitet.

     ```json
     "identity": {
         "type": "userAssigned",
         "tenantId": "{tenantId}",
         "userAssignedIdentities": {
             "/subscriptions/{subscriptionId}/resourceGroups/{yourRG}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{userIdentity}": {}
         }
     },
     ```

     Den **användartilldelade hanterade identiteten** kan finnas i någon av de prenumerationer och resursgrupper som användaren som tilldelade skissen har behörighet till.

     > [!IMPORTANT]
     > Azure-ritningar hanterar inte den användare som tilldelats den hanterade identiteten. Användarna är ansvariga för att tilldela tillräckligt med roller och behörigheter, för att inte skisstilldelningen ska misslyckas.

## <a name="clean-up-resources"></a>Rensa resurser

### <a name="unassign-a-blueprint"></a>Ta bort en skisstilldelning

Du kan ta bort en skiss från en prenumeration. Borttagningen görs ofta när artefaktresurserna inte längre behövs. När en skiss tas bort blir artefakterna som tilldelats som en del av skissen kvar. Om du vill ta bort en skisstilldelning använder du följande REST API-åtgärd:

- REST API-URI

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2018-11-01-preview
  ```

### <a name="delete-a-blueprint"></a>Ta bort en skiss

Om du vill ta bort själva skissen använder du följande REST API-åtgärd:

- REST API-URI

  ```http
  DELETE https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2018-11-01-preview
  ```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du skapat, tilldelat och tagit bort en skiss med REST API. Om du vill veta mer om Azure-ritningar kan du fortsätta till ritnings livs cykel artikeln.

> [!div class="nextstepaction"]
> [Lär dig mer om skiss livs cykeln](./concepts/lifecycle.md)