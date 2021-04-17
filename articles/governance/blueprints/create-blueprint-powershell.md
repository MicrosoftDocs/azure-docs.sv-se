---
title: 'Snabbstart: Skapa en skiss med PowerShell'
description: I den här snabbstarten använder du Azure Blueprints för att skapa, definiera och distribuera artefakter med hjälp av PowerShell.
ms.date: 01/27/2021
ms.topic: quickstart
ms.custom:
- mode-api
ms.openlocfilehash: 29c43da0c1467c74ddc85a447a0cf9addb574c7e
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538891"
---
# <a name="quickstart-define-and-assign-an-azure-blueprint-with-powershell"></a>Snabbstart: Definiera och tilldela en Azure Blueprint med PowerShell

Genom att lära dig hur du skapar och tilldelar skisser kan du definiera vanliga mönster för att utveckla återanvändbara och snabbt distribuerade konfigurationer baserat på ARM-mallar (Azure Resource Manager), princip, säkerhet med mera. I den här självstudien får du lära dig att använda Azure Blueprint för att utföra några av de vanliga uppgifter som rör generering, publicering och tilldelning av en skiss i din organisation. Du lär dig till exempel att:

## <a name="prerequisites"></a>Krav

- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free) innan du börjar.
- Om den inte redan är installerad följer du anvisningarna i Lägg till [Az.Blueprint-modulen](./how-to/manage-assignments-ps.md#add-the-azblueprint-module) för att installera och verifiera **Az.Blueprint-modulen** från PowerShell-galleriet.
- Om du inte har använt Azure Blueprints tidigare registrerar du resursprovidern via Azure PowerShell med `Register-AzResourceProvider -ProviderNamespace Microsoft.Blueprint` .

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-blueprint"></a>Skapa en skiss

Det första steget när du definierar ett standardmönster för efterlevnad är att skapa en skiss från de tillgängliga resurserna. Vi skapar en skiss med namnet ”MyBlueprint” för att konfigurera roll- och principtilldelningar för prenumerationen. Sedan lägger vi till en resursgrupp, en ARM-mall och en rolltilldelning för resursgruppen.

> [!NOTE]
> När du använder PowerShell _skapas skissobjektet_ först. För varje _artefakt_ som ska läggas till som har parametrar, måste parametrarna definieras i förväg i den första _skissen_.

1. Skapa det första _skissobjektet_. Parametern **BlueprintFile** tar en JSON-fil som innehåller egenskaper för skissen, eventuella resursgrupper som ska skapas och alla parametrar på skissnivå. Parametrarna anges vid tilldelning och används av artefakterna som lagts till i senare steg.

   - JSON-fil – blueprint.jspå

     ```json
     {
         "properties": {
             "description": "This blueprint sets tag policy and role assignment on the subscription, creates a ResourceGroup, and deploys a resource template and role assignment to that ResourceGroup.",
             "targetScope": "subscription",
             "parameters": {
                 "storageAccountType": {
                     "type": "string",
                     "defaultValue": "Standard_LRS",
                     "allowedValues": [
                         "Standard_LRS",
                         "Standard_GRS",
                         "Standard_ZRS",
                         "Premium_LRS"
                     ],
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
                         "description": "List of AAD object IDs that is assigned Contributor role at the subscription",
                         "strongType": "PrincipalId"
                     }
                 },
                 "owners": {
                     "type": "array",
                     "metadata": {
                         "description": "List of AAD object IDs that is assigned Owner role at the resource group",
                         "strongType": "PrincipalId"
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

   - PowerShell-kommando

     ```azurepowershell-interactive
     # Login first with Connect-AzAccount if not using Cloud Shell

     # Get a reference to the new blueprint object, we'll use it in subsequent steps
     $blueprint = New-AzBlueprint -Name 'MyBlueprint' -BlueprintFile .\blueprint.json
     ```

     > [!NOTE]
     > Använd filnamnet som _blueprint.jsnär du_ skapar skissdefinitionerna programmatiskt.
     > Det här filnamnet används när du [anropar Import-AzBlueprintWithArtifact](/powershell/module/az.blueprint/import-azblueprintwithartifact).

     Skissobjektet skapas som standard i standardprenumerationen. Använd parametern ManagementGroupId för att **ange hanteringsgruppen.** Använd parametern SubscriptionId för att **ange prenumerationen.**

1. Lägg till rolltilldelningen för prenumerationen. ArtifactFile **definierar** typen _av_ artefakt, egenskaperna justeras efter rolldefinitionsidentifieraren och huvudidentiteterna skickas som en matris med värden. I exemplet nedan konfigureras huvudidentiteterna som beviljas den angivna rollen till en parameter som anges under skisstilldelningen. I det här exemplet används den inbyggda rollen _Deltagare_ med ett GUID på `b24988ac-6180-42a0-ab88-20f7382dd24c`.

   - JSON-fil – \artifacts\roleContributor.jspå

     ```json
     {
         "kind": "roleAssignment",
         "properties": {
             "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
             "principalIds": "[parameters('contributors')]"
         }
     }
     ```

   - PowerShell-kommando

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'roleContributor' -ArtifactFile .\artifacts\roleContributor.json
     ```

1. Lägg till principtilldelning för prenumerationen. ArtifactFile **definierar** _typen_ av artefakt, egenskaperna som justeras efter en princip- eller initiativdefinition och konfigurerar principtilldelningen så att den använder de definierade skissparametrarna som ska konfigureras under skisstilldelningen. I det här exemplet används den inbyggda principen _Lägg till tagg och standardvärdet i resursgrupper_ med ett GUID på `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71`.

   - JSON-fil – \artifacts\policyTags.jspå

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
             "displayName": "Apply tag and its default value to resource groups",
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

   - PowerShell-kommando

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'policyTags' -ArtifactFile .\artifacts\policyTags.json
     ```

1. Lägg till en till principtilldelning för lagringstaggen (genom att återanvända parametern _storageAccountType_) för prenumerationen. Den här ytterligare principtilldelningsartefakten visar att en parameter som definierats för skissen kan användas av mer än en artefakt. I exemplet används **storageAccountType** för att ange en tagg på resursgruppen. Det här värdet anger information om lagringskontot som skapas i nästa steg. I det här exemplet används den inbyggda principen _Lägg till tagg och standardvärdet i resursgrupper_ med ett GUID på `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71`.

   - JSON-fil – \artifacts\policyStorageTags.jspå

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
             "displayName": "Apply storage tag to resource group",
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

   - PowerShell-kommando

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'policyStorageTags' -ArtifactFile .\artifacts\policyStorageTags.json
     ```

1. Lägg till en mall under resursgruppen. TemplateFile **för** en ARM-mall innehåller den normala JSON-komponenten för mallen. Mallen återanvänder även skissparametrarna **storageAccountType**, **tagName** och **tagValue** genom att dem till mallen. Skissparametrarna är tillgängliga för mallen med parametern **TemplateParameterFile** och inuti mall-JSON används nyckel/värde-par för att mata in värdet. Namnen på skiss- och mallparametrarna kan vara samma.

   - JSON ARM-mallfil – \artifacts\templateStorage.jspå

     ```json
     {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
             "storageAccountTypeFromBP": {
                 "type": "string",
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
             "location": "[resourceGroup().location]",
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
     }
     ```

   - JSON ARM-mallparameterfil – \artifacts\templateStorageParams.jspå

     ```json
     {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
         "contentVersion": "1.0.0.0",
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
     ```

   - PowerShell-kommando

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Type TemplateArtifact -Name 'templateStorage' -TemplateFile .\artifacts\templateStorage.json -TemplateParameterFile .\artifacts\templateStorageParams.json -ResourceGroupName storageRG
     ```

1. Lägg till rolltilldelning under resursgruppen. På liknande sätt som i föregående rolltilldelningspost använder exemplet nedan definitionsidentifieraren för rollen **Ägare** och tilldelar den en annan parameter från skissen. I det här exemplet används den inbyggda rollen _Ägare_ med ett GUID på `8e3af657-a8ff-443c-a75c-2fe8c4bcb635`.

   - JSON-fil – \artifacts\roleOwner.jspå

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

   - PowerShell-kommando

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'roleOwner' -ArtifactFile .\artifacts\roleOwner.json
     ```

## <a name="publish-a-blueprint"></a>Publicera en skiss

Nu när artefakter har lagts till i skissen är det dags att publicera den. När den har publicerats kan den tilldelas till en prenumeration.

```azurepowershell-interactive
# Use the reference to the new blueprint object from the previous steps
Publish-AzBlueprint -Blueprint $blueprint -Version '{BlueprintVersion}'
```

Värdet för `{BlueprintVersion}` är en sträng med bokstäver, siffror och bindestreck (inga blanksteg eller andra specialtecken) med en högsta längd på 20 tecken. Använd något unikt och beskrivande som **v20180622-135541**.

## <a name="assign-a-blueprint"></a>Tilldela en skiss

När en skiss har publicerats med PowerShell kan den tilldelas till en prenumeration. Tilldela skissen som du skapade till någon av prenumerationerna i din hierarki med hanteringsgrupper. Om skissen sparas till en prenumeration kan den endast tilldelas till den prenumerationen. Parametern **Skiss** anger vilken skiss som ska tilldelas. Om du vill ange parametrar för namn, plats, identitet, lås och skiss använder du matchande PowerShell-parametrar på cmdleten eller anger dem i `New-AzBlueprintAssignment` JSON-filen **med parametern AssignmentFile.**

1. Kör skissdistributionen genom att tilldela den till en prenumeration. Eftersom **parametrarna**  deltagare och ägare kräver att en matris med objectIds för huvudnamnen beviljas rolltilldelningen använder du [Azure Active Directory Graph API](/graph/migrate-azure-ad-graph-planning-checklist) för att samla in objectIds för användning i **AssignmentFile** för dina egna användare, grupper eller tjänstens huvudnamn.

   - JSON-fil – blueprintAssignment.jspå

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

   - PowerShell-kommando

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintAssignment -Blueprint $blueprint -Name 'assignMyBlueprint' -AssignmentFile .\blueprintAssignment.json
     ```

   - Användartilldelad hanterad identitet

     En skisstilldelning kan även använda en [användartilldelad hanterad identitet](../../active-directory/managed-identities-azure-resources/overview.md).
     I det här fallet ändras **identitetsdelen** av JSON-tilldelningsfilen på följande sätt. Ersätt `{tenantId}` , , och med ditt `{subscriptionId}` `{yourRG}` tenantId, subscriptionId, resursgruppens namn och namnet på `{userIdentity}` din användar tilldelade hanterade identitet.

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
     > Azure Blueprints hanterar inte den användar tilldelade hanterade identiteten. Användarna är ansvariga för att tilldela tillräckligt med roller och behörigheter, för att inte skisstilldelningen ska misslyckas.

## <a name="clean-up-resources"></a>Rensa resurser

### <a name="unassign-a-blueprint"></a>Ta bort en skisstilldelning

Du kan ta bort en skiss från en prenumeration. Borttagningen görs ofta när artefaktresurserna inte längre behövs. När en skiss tas bort blir artefakterna som tilldelats som en del av skissen kvar. Om du vill ta bort en skisstilldelning använder du `Remove-AzBlueprintAssignment` cmdleten :

assignMyBlueprint

```azurepowershell-interactive
Remove-AzBlueprintAssignment -Name 'assignMyBlueprint'
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat, tilldelat och tagit bort en skiss med PowerShell. Om du vill veta mer Azure Blueprints kan du fortsätta till artikeln livscykel för skisser.

> [!div class="nextstepaction"]
> [Lär dig mer om livscykeln för en skiss](./concepts/lifecycle.md)
