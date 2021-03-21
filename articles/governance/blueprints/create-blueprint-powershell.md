---
title: 'Snabb start: skapa en skiss med PowerShell'
description: I den här snabb starten använder du Azure-ritningar för att skapa, definiera och distribuera artefakter med hjälp av PowerShell.
ms.date: 01/27/2021
ms.topic: quickstart
ms.openlocfilehash: 65d573d0aec7d5f292bc985483e1f12c350ae03a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98918287"
---
# <a name="quickstart-define-and-assign-an-azure-blueprint-with-powershell"></a>Snabb start: definiera och tilldela en Azure Blueprint med PowerShell

Genom att lära dig hur du skapar och tilldelar ritningar kan du definiera vanliga mönster för att utveckla återanvändbara och snabbt distribuerade konfigurationer baserat på Azure Resource Manager mallar (ARM-mallar), principer, säkerhet och mycket mer. I den här självstudien får du lära dig att använda Azure Blueprint för att utföra några av de vanliga uppgifter som rör generering, publicering och tilldelning av en skiss i din organisation. Du lär dig till exempel att:

## <a name="prerequisites"></a>Krav

- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free) innan du börjar.
- Om den inte redan är installerad följer du anvisningarna i [Lägg till modulen AZ. skiss](./how-to/manage-assignments-ps.md#add-the-azblueprint-module) för att installera och validera modulen **AZ. skiss** från PowerShell-galleriet.
- Om du inte har använt Azure-modeller tidigare registrerar du resurs leverantören genom att Azure PowerShell med `Register-AzResourceProvider -ProviderNamespace Microsoft.Blueprint` .

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-blueprint"></a>Skapa en skiss

Det första steget när du definierar ett standardmönster för efterlevnad är att skapa en skiss från de tillgängliga resurserna. Vi skapar en skiss med namnet ”MyBlueprint” för att konfigurera roll- och principtilldelningar för prenumerationen. Sedan lägger vi till en resurs grupp, en ARM-mall och en roll tilldelning i resurs gruppen.

> [!NOTE]
> När du använder PowerShell skapas _skiss_ -objektet först. För varje _artefakt_ som ska läggas till som har parametrar, måste parametrarna definieras i förväg i den första _skissen_.

1. Skapa det första _skissobjektet_. Parametern **BlueprintFile** tar en JSON-fil som innehåller egenskaper för skissen, alla resurs grupper som ska skapas och alla skiss nivå parametrar. Parametrarna anges vid tilldelning och används av artefakterna som lagts till i senare steg.

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
     > Använd fil namnet _blueprint.js_ när du skapar dina skiss definitioner program mässigt.
     > Det här fil namnet används när du anropar [import-AzBlueprintWithArtifact](/powershell/module/az.blueprint/import-azblueprintwithartifact).

     Skiss-objektet skapas som standard i standard prenumerationen. Använd parametern **ManagementGroupId** för att ange hanterings gruppen. Om du vill ange prenumerationen använder du parameter **SubscriptionId**.

1. Lägg till rolltilldelningen för prenumerationen. **ArtifactFile** definierar _typen_ av artefakt. egenskaperna justeras till roll Definitions-ID: t och huvudidentiteterna skickas som en matris med värden. I exemplet nedan konfigureras huvudidentiteterna som beviljas den angivna rollen till en parameter som anges under skisstilldelningen. I det här exemplet används den inbyggda rollen _Deltagare_ med ett GUID på `b24988ac-6180-42a0-ab88-20f7382dd24c`.

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

1. Lägg till principtilldelning för prenumerationen. **ArtifactFile** definierar _typen_ av artefakt, egenskaper som anpassas till en princip eller initiativ definition och konfigurerar princip tilldelningen så att den använder de definierade skiss parametrarna för att konfigurera under skiss tilldelningen. I det här exemplet används den inbyggda principen _Lägg till tagg och standardvärdet i resursgrupper_ med ett GUID på `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71`.

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

1. Lägg till en mall under resursgruppen. **TemplateFile** för en arm-mall innehåller den normala JSON-komponenten i mallen. Mallen återanvänder även skissparametrarna **storageAccountType**, **tagName** och **tagValue** genom att dem till mallen. Skiss parametrarna är tillgängliga för mallen med hjälp av parametern **TemplateParameterFile** och inuti mall-JSON som nyckel/värde-paret används för att mata in värdet. Namn på skiss och mall kan vara identiska.

   - Filen JSON ARM-mall – \artifacts\templateStorage.jspå

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

   - Parameter fil för JSON ARM-mall – \artifacts\templateStorageParams.jspå

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

När en skiss har publicerats med PowerShell kan den tilldelas en prenumeration. Tilldela skissen som du skapade till någon av prenumerationerna i din hierarki med hanteringsgrupper. Om skissen sparas till en prenumeration kan den endast tilldelas till den prenumerationen. **Skiss** parametern anger den skiss som ska tilldelas. Om du vill ange parametrar för namn, plats, identitet, lås och skiss använder du de matchande PowerShell-parametrarna i `New-AzBlueprintAssignment` cmdleten eller anger dem i **AssignmentFile** -parameterns JSON-fil.

1. Kör skissdistributionen genom att tilldela den till en prenumeration. Eftersom **deltagar** -och **ägar** parametrarna kräver en matris med objectIds av de säkerhetsobjekt som ska tilldelas roll tilldelningen använder [Azure Active Directory Graph API](../../active-directory/develop/active-directory-graph-api.md) för att samla in objectIds för användning i **AssignmentFile** för dina egna användare, grupper eller tjänstens huvud namn.

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
     I det här fallet ändras **identitets** delen av JSON-tilldelnings filen enligt följande. Ersätt `{tenantId}` , `{subscriptionId}` , `{yourRG}` , och `{userIdentity}` med ditt tenantId, subscriptionId, resurs gruppens namn och namnet på din användarspecifika hanterade identitet.

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

Du kan ta bort en skiss från en prenumeration. Borttagningen görs ofta när artefaktresurserna inte längre behövs. När en skiss tas bort blir artefakterna som tilldelats som en del av skissen kvar. Om du vill ta bort en skiss tilldelning använder du `Remove-AzBlueprintAssignment` cmdleten:

assignMyBlueprint

```azurepowershell-interactive
Remove-AzBlueprintAssignment -Name 'assignMyBlueprint'
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du skapat, tilldelat och tagit bort en skiss med PowerShell. Om du vill veta mer om Azure-ritningar kan du fortsätta till ritnings livs cykel artikeln.

> [!div class="nextstepaction"]
> [Lär dig mer om skiss livs cykeln](./concepts/lifecycle.md)