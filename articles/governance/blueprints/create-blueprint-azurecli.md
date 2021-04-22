---
title: 'Snabbstart: Skapa en skiss med Azure CLI'
description: I den här snabbstarten använder du Azure Blueprints för att skapa, definiera och distribuera artefakter med hjälp av Azure CLI.
ms.date: 01/27/2021
ms.topic: quickstart
ms.openlocfilehash: 506877eddd78ce54681bd4870e1d9040b4738c27
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877416"
---
# <a name="quickstart-define-and-assign-an-azure-blueprint-with-azure-cli"></a>Snabbstart: Definiera och tilldela en Azure Blueprint med Azure CLI

Genom att lära dig hur du skapar och tilldelar skisser kan du definiera vanliga mönster för att utveckla återanvändbara och snabbt distribuerade konfigurationer baserat på arm-mallar (Azure Resource Manager), policyer, säkerhet med mera. I den här självstudien får du lära dig att använda Azure Blueprint för att utföra några av de vanliga uppgifter som rör generering, publicering och tilldelning av en skiss i din organisation. Du lär dig till exempel att:

## <a name="prerequisites"></a>Krav

- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free) innan du börjar.
- Om du inte har använt Azure Blueprints tidigare registrerar du resursprovidern via Azure CLI med `az provider register --namespace Microsoft.Blueprint` .

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-blueprint-extension"></a>Lägga till skisstillägget

Om du vill göra det möjligt för Azure CLI att hantera skissdefinitioner och tilldelningar måste tillägget läggas till.
Det här tillägget fungerar överallt där Azure CLI kan användas, inklusive [bash i Windows 10](/windows/wsl/install-win10), [Cloud Shell](https://shell.azure.com) (både fristående och i portalen), [Azure CLI-dockeravbildningen](https://hub.docker.com/_/microsoft-azure-cli) och där det är lokalt installerat.

1. Kontrollera att den senaste versionen av Azure CLI är installerad (minst **2.0.76**). Om den ännu inte är installerad följer du [de här instruktionerna](/cli/azure/install-azure-cli-windows).

1. I din valda Azure CLI-miljö importerar du den med följande kommando:

   ```azurecli-interactive
   # Add the Blueprint extension to the Azure CLI environment
   az extension add --name blueprint
   ```

1. Kontrollera att tillägget har installerats och är den förväntade versionen (minst **0.1.0**):

   ```azurecli-interactive
   # Check the extension list (note that you may have other extensions installed)
   az extension list

   # Run help for extension options
   az blueprint -h
   ```

## <a name="create-a-blueprint"></a>Skapa en skiss

Det första steget när du definierar ett standardmönster för efterlevnad är att skapa en skiss från de tillgängliga resurserna. Vi skapar en skiss med namnet ”MyBlueprint” för att konfigurera roll- och principtilldelningar för prenumerationen. Sedan lägger vi till en resursgrupp, en ARM-mall och en rolltilldelning för resursgruppen.

> [!NOTE]
> När du använder Azure CLI _skapas skissobjektet_ först. För varje _artefakt_ som ska läggas till som har parametrar, måste parametrarna definieras i förväg i den första _skissen_.

1. Skapa det första _skissobjektet_. **Parameterparametern** tar en JSON-fil som innehåller alla parametrar på skissnivå. Parametrarna anges vid tilldelning och används av artefakterna som lagts till i senare steg.

   - JSON-fil – blueprintparms.jspå

     ```json
     {
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
     }
     ```

   - Azure CLI-kommando

     ```azurecli-interactive
     # Login first with az login if not using Cloud Shell

     # Create the blueprint object
     az blueprint create \
        --name 'MyBlueprint' \
        --description 'This blueprint sets tag policy and role assignment on the subscription, creates a ResourceGroup, and deploys a resource template and role assignment to that ResourceGroup.' \
        --parameters blueprintparms.json
     ```

     > [!NOTE]
     > Använd filnamnet som _blueprint.jsnär du_ importerar skissdefinitionerna.
     > Det här filnamnet används när az [blueprint import anropas.](/cli/azure/blueprint#az_blueprint_import)

     Skissobjektet skapas som standard i standardprenumerationen. Använd parameter managementgroup för att ange **hanteringsgruppen.** Använd parameterprenumerationen för att **ange prenumerationen.**

1. Lägg till resursgruppen för lagringsartefakter i definitionen.

   ```azurecli-interactive
   az blueprint resource-group add \
      --blueprint-name 'MyBlueprint' \
      --artifact-name 'storageRG' \
      --description 'Contains the resource template deployment and a role assignment.'
   ```

1. Lägg till rolltilldelningen för prenumerationen. I exemplet nedan konfigureras huvudidentiteterna som beviljas den angivna rollen till en parameter som anges under skisstilldelningen. I det här exemplet används den inbyggda rollen _Deltagare_ med ett GUID på `b24988ac-6180-42a0-ab88-20f7382dd24c`.

   ```azurecli-interactive
   az blueprint artifact role create \
      --blueprint-name 'MyBlueprint' \
      --artifact-name 'roleContributor' \
      --role-definition-id '/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c' \
      --principal-ids "[parameters('contributors')]"
   ```

1. Lägg till principtilldelning för prenumerationen. I det här exemplet används den inbyggda principen _Lägg till tagg och standardvärdet i resursgrupper_ med ett GUID på `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71`.

   - JSON-fil – artifacts\policyTags.jspå

     ```json
     {
        "tagName": {
           "value": "[parameters('tagName')]"
        },
        "tagValue": {
           "value": "[parameters('tagValue')]"
        }
     }
     ```

   - Azure CLI-kommando

     ```azurecli-interactive
     az blueprint artifact policy create \
        --blueprint-name 'MyBlueprint' \
        --artifact-name 'policyTags' \
        --policy-definition-id '/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71' \
        --display-name 'Apply tag and its default value to resource groups' \
        --description 'Apply tag and its default value to resource groups' \
        --parameters artifacts\policyTags.json
     ```

     > [!NOTE]
     > När du `az blueprint` använder på en Mac ersätter du med för `\` `/` parametervärden som innehåller sökvägen. I det här fallet blir värdet **för parametrar** `artifacts/policyTags.json` .

1. Lägg till en till principtilldelning för lagringstaggen (genom att återanvända parametern _storageAccountType_) för prenumerationen. Den här ytterligare principtilldelningsartefakten visar att en parameter som definierats för skissen kan användas av mer än en artefakt. I exemplet används **storageAccountType** för att ange en tagg på resursgruppen. Det här värdet anger information om lagringskontot som skapas i nästa steg. I det här exemplet används den inbyggda principen _Lägg till tagg och standardvärdet i resursgrupper_ med ett GUID på `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71`.

   - JSON-fil – artifacts\policyStorageTags.jspå

     ```json
     {
        "tagName": {
           "value": "StorageType"
        },
        "tagValue": {
           "value": "[parameters('storageAccountType')]"
        }
     }
     ```

   - Azure CLI-kommando

     ```azurecli-interactive
     az blueprint artifact policy create \
        --blueprint-name 'MyBlueprint' \
        --artifact-name 'policyStorageTags' \
        --policy-definition-id '/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71' \
        --display-name 'Apply storage tag to resource group' \
        --description 'Apply storage tag and the parameter also used by the template to resource groups' \
        --parameters artifacts\policyStorageTags.json
     ```

     > [!NOTE]
     > När du `az blueprint` använder på en Mac ersätter du med för `\` `/` parametervärden som innehåller sökvägen. I det här fallet blir värdet **för parametrar** `artifacts/policyStorageTags.json` .

1. Lägg till en mall under resursgruppen. **Mallparametern** för en ARM-mall innehåller de vanliga JSON-komponenterna i mallen. Mallen återanvänder även skissparametrarna **storageAccountType**, **tagName** och **tagValue** genom att dem till mallen. Skissparametrarna är tillgängliga för mallen med **hjälp** av parameterparametrar och inuti mallens JSON används nyckel/värde-paret för att mata in värdet. Namnen på skiss- och mallparametrarna kan vara samma.

   - JSON ARM-mallfil – artifacts\templateStorage.jspå

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

   - Parameterfil för JSON ARM-mall – artifacts\templateStorageParams.jspå

     ```json
     {
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
     ```

   - Azure CLI-kommando

     ```azurecli-interactive
     az blueprint artifact template create \
        --blueprint-name 'MyBlueprint' \
        --artifact-name 'templateStorage' \
        --template artifacts\templateStorage.json \
        --parameters artifacts\templateStorageParams.json \
        --resource-group-art 'storageRG'
     ```

     > [!NOTE]
     > När du `az blueprint` använder på en Mac ersätter du med för `\` `/` parametervärden som innehåller sökvägen. I det här fallet blir värdet **för mallen** och `artifacts/templateStorage.json` **parametrarna** blir `artifacts/templateStorageParams.json` .

1. Lägg till rolltilldelning under resursgruppen. På liknande sätt som i föregående rolltilldelningspost använder exemplet nedan definitionsidentifieraren för rollen **Ägare** och tilldelar den en annan parameter från skissen. I det här exemplet används den inbyggda rollen _Ägare_ med ett GUID på `8e3af657-a8ff-443c-a75c-2fe8c4bcb635`.

   ```azurecli-interactive
   az blueprint artifact role create \
      --blueprint-name 'MyBlueprint' \
      --artifact-name 'roleOwner' \
      --role-definition-id '/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635' \
      --principal-ids "[parameters('owners')]" \
      --resource-group-art 'storageRG'
   ```

## <a name="publish-a-blueprint"></a>Publicera en skiss

Nu när artefakter har lagts till i skissen är det dags att publicera den. När den har publicerats kan den tilldelas till en prenumeration.

```azurecli-interactive
az blueprint publish --blueprint-name 'MyBlueprint' --version '{BlueprintVersion}'
```

Värdet för `{BlueprintVersion}` är en sträng med bokstäver, siffror och bindestreck (inga blanksteg eller andra specialtecken) med en högsta längd på 20 tecken. Använd något unikt och informationsligt, till exempel **v20200605-135541.**

## <a name="assign-a-blueprint"></a>Tilldela en skiss

När en skiss har publicerats med hjälp av Azure CLI kan den tilldelas till en prenumeration. Tilldela skissen som du skapade till någon av prenumerationerna i din hierarki med hanteringsgrupper. Om skissen sparas till en prenumeration kan den endast tilldelas till den prenumerationen. Parametern **skissnamn** anger vilken skiss som ska tilldelas. Om du vill ange parametrarna namn, plats, identitet, lås och skiss använder du matchande Azure CLI-parametrar i kommandot eller anger dem i `az blueprint assignment create` **parametrarna** JSON-filen.

1. Kör skissdistributionen genom att tilldela den till en prenumeration. Eftersom **parametrarna**  deltagare och ägare kräver att en matris med objectIds för huvudnamnen beviljas rolltilldelningen använder  du [Azure Active Directory Graph API](/graph/migrate-azure-ad-graph-planning-checklist) för att samla in objectIds för användning i parametrarna för dina egna användare, grupper eller tjänstens huvudnamn.

   - JSON-fil – blueprintAssignment.jspå

     ```json
     {
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
     ```

   - Azure CLI-kommando

     ```azurecli-interactive
     az blueprint assignment create \
        --name 'assignMyBlueprint' \
        --location 'westus' \
        --resource-group-value artifact_name=storageRG name=StorageAccount location=eastus \
        --parameters blueprintAssignment.json
     ```

   - Användartilldelad hanterad identitet

     En skisstilldelning kan även använda en [användartilldelad hanterad identitet](../../active-directory/managed-identities-azure-resources/overview.md).
     I det här fallet **anges parametern identity-type** till _UserAssigned_ och parametern **user-assigned-identities** anger identiteten. Ersätt `{userIdentity}` med namnet på den användar tilldelade hanterade identiteten.

     ```azurecli-interactive
     az blueprint assignment create \
        --name 'assignMyBlueprint' \
        --location 'westus' \
        --identity-type UserAssigned \
        --user-assigned-identities {userIdentity} \
        --resource-group-value artifact_name=storageRG name=StorageAccount location=eastus \
        --parameters blueprintAssignment.json
     ```

     Den **användartilldelade hanterade identiteten** kan finnas i någon av de prenumerationer och resursgrupper som användaren som tilldelade skissen har behörighet till.

     > [!IMPORTANT]
     > Azure Blueprints hanterar inte den användar tilldelade hanterade identiteten. Användarna är ansvariga för att tilldela tillräckligt med roller och behörigheter, för att inte skisstilldelningen ska misslyckas.

## <a name="clean-up-resources"></a>Rensa resurser

### <a name="unassign-a-blueprint"></a>Ta bort en skisstilldelning

Du kan ta bort en skiss från en prenumeration. Borttagningen görs ofta när artefaktresurserna inte längre behövs. När en skiss tas bort blir artefakterna som tilldelats som en del av skissen kvar. Om du vill ta bort en skisstilldelning använder du `az blueprint assignment delete` kommandot :

```azurecli-interactive
az blueprint assignment delete --name 'assignMyBlueprint'
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat, tilldelat och tagit bort en skiss med Azure CLI. Om du vill veta mer Azure Blueprints kan du fortsätta till artikeln livscykel för skisser.

> [!div class="nextstepaction"]
> [Lär dig mer om livscykeln för en skiss](./concepts/lifecycle.md)