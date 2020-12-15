---
title: Skapa och distribuera mall-specifikation
description: Lär dig hur du skapar en mall-specifikation från ARM-mallen. Distribuera sedan mallen till en resurs grupp i din prenumeration.
author: tfitzmac
ms.date: 12/14/2020
ms.topic: quickstart
ms.author: tomfitz
ms.openlocfilehash: 1d3e7c6ee6d19d4d2cd3828d5abf95ccb5457e76
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97511356"
---
# <a name="quickstart-create-and-deploy-template-spec-preview"></a>Snabb start: skapa och distribuera mall specifikation (för hands version)

Den här snabb starten visar hur du paketerar en Azure Resource Manager-mall (ARM-mall) i en [mall-specifikation](template-specs.md). Sedan distribuerar du mallen specifikation. Din Template-specifikation innehåller en ARM-mall som distribuerar ett lagrings konto.

## <a name="prerequisites"></a>Krav

Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> Mallens specifikationer är för närvarande en för hands version. Om du vill använda den med Azure PowerShell måste du installera [version 5.0.0 eller senare](/powershell/azure/install-az-ps). Använd [version 2.14.2 eller senare](/cli/azure/install-azure-cli)för att använda den med Azure CLI.

## <a name="create-template"></a>Skapa mallen

Du skapar en mall-specifikation från en lokal mall. Kopiera följande mall och spara den lokalt i en fil med namnet **azuredeploy.jspå**. Den här snabb starten förutsätter att du har sparat till en sökväg **c:\Templates\azuredeploy.js** men du kan använda valfri sökväg.

:::code language="json" source="~/quickstart-templates/101-storage-account-create/azuredeploy.json":::

## <a name="create-template-spec"></a>Skapa mall-specifikation

Mallens specifikation är en resurs typ med namnet `Microsoft.Resources/templateSpecs` . Om du vill skapa en mall-specifikation använder du PowerShell, Azure CLI, portalen eller en ARM-mall.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Skapa en ny resurs grupp som innehåller mallens specifikation.

    ```azurepowershell
    New-AzResourceGroup `
      -Name templateSpecRG `
      -Location westus2
    ```

1. Skapa mallen specifikation i den resurs gruppen. Ge den nya mallen specifikation av namnet **storageSpec**.

    ```azurepowershell
    New-AzTemplateSpec `
      -Name storageSpec `
      -Version "1.0" `
      -ResourceGroupName templateSpecRG `
      -Location westus2 `
      -TemplateFile "c:\Templates\azuredeploy.json"
    ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

1. Skapa en ny resurs grupp som innehåller mallens specifikation.

    ```azurecli
    az group create \
      --name templateSpecRG \
      --location westus2
    ```

1. Skapa mallen specifikation i den resurs gruppen. Ge den nya mallen specifikation av namnet **storageSpec**.

    ```azurecli
    az ts create \
      --name storageSpec \
      --version "1.0" \
      --resource-group templateSpecRG \
      --location "westus2" \
      --template-file "c:\Templates\azuredeploy.json"
    ```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Sök efter **specifikationer för mallar**. Välj **mall-specifikationerna** från de tillgängliga alternativen.

    :::image type="content" source="./media/quickstart-create-template-specs/search-template-spec.png" alt-text="specifikationer för sökmall":::

1. Välj **Importera mall**.

    :::image type="content" source="./media/quickstart-create-template-specs/import-template.png" alt-text="Importera mall":::

1. Välj mappikonen.

    :::image type="content" source="./media/quickstart-create-template-specs/open-folder.png" alt-text="Öppna mapp":::

1. Navigera till den lokala mall som du sparade och markera den. Välj **Öppna**.
1. Välj **Importera**.

    :::image type="content" source="./media/quickstart-create-template-specs/select-import.png" alt-text="Välj import alternativ":::

1. Ange följande värden:

    - **Namn**: Ange ett namn för mall-specifikationen.  Till exempel **storageSpec**
    - **Prenumeration**: Välj en Azure-prenumeration som används för att skapa mallen specifikation.
    - **Resurs grupp**: Välj **Skapa ny** och ange sedan ett nytt resurs grupp namn.  Till exempel **templateSpecRG**.
    - **Plats**: Välj en plats för resursgruppen. Till exempel  **USA, västra 2**.
    - **Version**: Ange en version för mallen specifikation. Använd **1,0**.

1. Välj **Granska + skapa**.
1. Välj **Skapa**.

# <a name="arm-template"></a>[ARM-mall](#tab/azure-resource-manager)

> [!NOTE]
> I stället för att använda en ARM-mall rekommenderar vi att du använder PowerShell eller CLI för att skapa din mall-specifikation. Dessa verktyg konverterar automatiskt länkade mallar till artefakter som är anslutna till din huvud mall. När du använder en ARM-mall för att skapa en malls specifikation måste du manuellt lägga till de länkade mallarna som artefakter, vilket kan vara komplicerat.

1. När du använder en ARM-mall för att skapa en mall, bäddas mallen in i resurs definitionen. Det finns vissa ändringar du behöver göra i din lokala mall. Kopiera följande mall och spara den lokalt som **azuredeploy.jspå**.

    > [!NOTE]
    > I den inbäddade mallen måste alla [Template-uttryck](template-expressions.md) föregås av en andra vänster hak paren tes. Använd `"[[` i stället för `"[` . JSON-matriser använder fortfarande en enda vänster hak paren tes.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/templateSpecs",
          "apiVersion": "2019-06-01-preview",
          "name": "storageSpec",
          "location": "westus2",
          "properties": {
            "displayName": "Storage template spec"
          },
          "tags": {},
          "resources": [
            {
              "type": "versions",
              "apiVersion": "2019-06-01-preview",
              "name": "1.0",
              "location": "westus2",
              "dependsOn": [ "storageSpec" ],
              "properties": {
                "template": {
                  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                  "contentVersion": "1.0.0.0",
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
                        "description": "Storage Account type"
                      }
                    },
                    "location": {
                      "type": "string",
                      "defaultValue": "[[resourceGroup().location]",
                      "metadata": {
                        "description": "Location for all resources."
                      }
                    }
                  },
                  "variables": {
                    "storageAccountName": "[[concat('store', uniquestring(resourceGroup().id))]"
                  },
                  "resources": [
                    {
                      "type": "Microsoft.Storage/storageAccounts",
                      "apiVersion": "2019-04-01",
                      "name": "[[variables('storageAccountName')]",
                      "location": "[[parameters('location')]",
                      "sku": {
                        "name": "[[parameters('storageAccountType')]"
                      },
                      "kind": "StorageV2",
                      "properties": {}
                    }
                  ],
                  "outputs": {
                    "storageAccountName": {
                      "type": "string",
                      "value": "[[variables('storageAccountName')]"
                    }
                  }
                }
              },
              "tags": {}
            }
          ]
        }
      ],
      "outputs": {}
    }
    ```

1. Använd Azure CLI eller PowerShell för att skapa en ny resurs grupp.

    ```azurepowershell
    New-AzResourceGroup `
      -Name templateSpecRG `
      -Location westus2
    ```

    ```azurecli
    az group create \
      --name templateSpecRG \
      --location westus2
    ```

1. Distribuera din mall med Azure CLI eller PowerShell.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName templateSpecRG `
      -TemplateFile "c:\Templates\azuredeploy.json"
    ```

    ```azurecli
    az deployment group create \
      --name templateSpecRG \
      --template-file "c:\Templates\azuredeploy.json"
    ```

---

## <a name="deploy-template-spec"></a>Specifikation för att distribuera mall

Om du vill distribuera en mall använder du samma distributions kommandon som du använde för att distribuera en mall. Skicka in resurs-ID: t för den mall som du vill distribuera.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Skapa en resurs grupp som innehåller det nya lagrings kontot.

    ```azurepowershell
    New-AzResourceGroup `
      -Name storageRG `
      -Location westus2
    ```

1. Hämta resurs-ID för mallens specifikation.

    ```azurepowershell
    $id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name storageSpec -Version "1.0").Versions.Id
    ```

1. Distribuera mallen specifikation.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -TemplateSpecId $id `
      -ResourceGroupName storageRG
    ```

1. Du anger parametrar exakt som för en ARM-mall. Distribuera om mallen specifikation med en parameter för lagrings konto typen.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -TemplateSpecId $id `
      -ResourceGroupName storageRG `
      -storageAccountType Standard_GRS
    ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

1. Skapa en resurs grupp som innehåller det nya lagrings kontot.

    ```azurecli
    az group create \
      --name storageRG \
      --location westus2
    ```

1. Hämta resurs-ID för mallens specifikation.

    ```azurecli
    id=$(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0" --query "id")
    ```

    > [!NOTE]
    > Det finns ett känt problem med att hämta ett Specifikations-ID för mallen och tilldela det till en variabel i Windows PowerShell.

1. Distribuera mallen specifikation.

    ```azurecli
    az deployment group create \
      --resource-group storageRG \
      --template-spec $id
    ```

1. Du anger parametrar exakt som för en ARM-mall. Distribuera om mallen specifikation med en parameter för lagrings konto typen.

    ```azurecli
    az deployment group create \
      --resource-group storageRG \
      --template-spec $id \
      --parameters storageAccountType='Standard_GRS'
    ```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Välj den mall specifikation som du skapade.

    :::image type="content" source="./media/quickstart-create-template-specs/select-template-spec.png" alt-text="Välj mall-specifikationer":::

1. Välj **Distribuera**.

    :::image type="content" source="./media/quickstart-create-template-specs/deploy-template-spec.png" alt-text="distribuera specifikationer för mallar":::

1. Ange följande värden:

    - **Prenumeration**: Välj en Azure-prenumeration för att skapa resursen.
    - **Resurs grupp**: Välj **Skapa ny** och ange sedan **storageRG**.
    - **Typ av lagrings konto**: Välj **Standard_GRS**.

1. Välj **Granska + skapa**.
1. Välj **Skapa**.

# <a name="arm-template"></a>[ARM-mall](#tab/azure-resource-manager)

1. Kopiera följande mall och spara den lokalt i en fil med namnet **storage.jspå**.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/deployments",
          "apiVersion": "2020-06-01",
          "name": "demo",
          "properties": {
            "templateLink": {
              "id": "[resourceId('templateSpecRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '1.0')]"
            },
            "parameters": {
            },
            "mode": "Incremental"
          }
        }
      ],
      "outputs": {}
    }
    ```

1. Använd Azure CLI eller PowerShell för att skapa en ny resurs grupp för lagrings kontot.

    ```azurepowershell
    New-AzResourceGroup `
      -Name storageRG `
      -Location westus2
    ```

    ```azurecli
    az group create \
      --name storageRG \
      --location westus2
    ```

1. Distribuera din mall med Azure CLI eller PowerShell.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName storageRG `
      -TemplateFile "c:\Templates\storage.json"
    ```

    ```azurecli
    az deployment group create \
      --name storageRG \
      --template-file "c:\Templates\storage.json"
    ```

---

## <a name="grant-access"></a>Bevilja åtkomst

Om du vill låta andra användare i din organisation distribuera din malls specifikation måste du ge dem Läs behörighet. Du kan tilldela rollen läsare till en Azure AD-grupp för den resurs grupp som innehåller de mall-specifikationer som du vill dela. Mer information finns i [Självstudier: bevilja en grupp åtkomst till Azure-resurser med hjälp av Azure PowerShell](../../role-based-access-control/tutorial-role-assignments-group-powershell.md).

## <a name="update-template"></a>Uppdatera mall

Låt oss anta att du har identifierat en ändring som du vill göra till mallen i din mall specifikation. Följande mall liknar din tidigare mall, men lägger till ett prefix för lagrings kontots namn. Kopiera följande mall och uppdatera azuredeploy.jspå filen.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/templatespecs/azuredeploy2.json":::

## <a name="update-template-spec-version"></a>Uppdatera mallens Specifikations version

I stället för att skapa en ny mall-specifikation för den ändrade mallen lägger du till en ny version med namnet i `2.0` specifikationen för den befintliga mallen. användarna kan välja vilken version som ska distribueras.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Skapa en ny version för mallen specifikation.

   ```powershell
   New-AzTemplateSpec `
     -Name storageSpec `
     -Version "2.0" `
     -ResourceGroupName templateSpecRG `
     -Location westus2 `
     -TemplateFile "c:\Templates\azuredeploy.json"
   ```

1. Hämta resurs-ID för versionen om du vill distribuera den nya versionen `2.0` .

   ```powershell
   $id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name storageSpec -Version "2.0").Versions.Id
   ```

1. Distribuera den versionen. Ange ett prefix för lagrings konto namnet.

   ```powershell
   New-AzResourceGroupDeployment `
     -TemplateSpecId $id `
     -ResourceGroupName storageRG `
     -namePrefix "demoaccount"
   ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

1. Skapa en ny version för mallen specifikation.

   ```azurecli
   az ts create \
     --name storageSpec \
     --version "2.0" \
     --resource-group templateSpecRG \
     --location "westus2" \
     --template-file "c:\Templates\azuredeploy.json"
   ```

1. Hämta resurs-ID för versionen om du vill distribuera den nya versionen `2.0` .

   ```azurecli
   id=$(az ts show --name storageSpec --resource-group templateSpecRG --version "2.0" --query "id")
   ```

1. Distribuera den versionen. Ange ett prefix för lagrings konto namnet.

   ```azurecli
    az deployment group create \
      --resource-group storageRG \
      --template-spec $id \
      --parameters namePrefix='demoaccount'
    ```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. I specifikationen mall väljer du **Skapa ny version**.

   :::image type="content" source="./media/quickstart-create-template-specs/select-versions.png" alt-text="Skapa ny version":::

1. Namnge den nya versionen `2.0` och Lägg eventuellt till anteckningar. Välj **Redigera mall**.

   :::image type="content" source="./media/quickstart-create-template-specs/add-version-name.png" alt-text="namn ny version":::

1. Ersätt innehållet i mallen med din uppdaterade mall. Välj **Granska och spara**.
1. Välj **Spara ändringar**.

1. Om du vill distribuera den nya versionen väljer du **versioner**

   :::image type="content" source="./media/quickstart-create-template-specs/see-versions.png" alt-text="List versioner":::

1. För den version som du vill distribuera väljer du de tre punkterna och **distribuerar**.

   :::image type="content" source="./media/quickstart-create-template-specs/deploy-version.png" alt-text="Välj vilken version du vill distribuera":::

1. Fyll i fälten som du gjorde när du distribuerade den tidigare versionen.
1. Välj **Granska + skapa**.
1. Välj **Skapa**.

# <a name="arm-template"></a>[ARM-mall](#tab/azure-resource-manager)

1. Återigen måste du göra några ändringar i din lokala mall för att det ska fungera med specifikationer för mallar. Kopiera följande mall och spara den lokalt som azuredeploy.jspå.

   ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/templateSpecs",
          "apiVersion": "2019-06-01-preview",
          "name": "storageSpec",
          "location": "westus2",
          "properties": {
            "displayName": "Storage template spec"
          },
          "tags": {},
          "resources": [
            {
              "type": "versions",
              "apiVersion": "2019-06-01-preview",
              "name": "2.0",
              "location": "westus2",
              "dependsOn": [ "storageSpec" ],
              "properties": {
                "template": {
                  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                  "contentVersion": "1.0.0.0",
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
                        "description": "Storage Account type"
                      }
                    },
                    "location": {
                      "type": "string",
                      "defaultValue": "[[resourceGroup().location]",
                      "metadata": {
                        "description": "Location for all resources."
                      }
                    },
                    "namePrefix": {
                      "type": "string",
                      "maxLength": 11,
                      "defaultValue": "store",
                      "metadata": {
                        "description": "Prefix for storage account name"
                      }
                    }
                  },
                  "variables": {
                    "storageAccountName": "[[concat(parameters('namePrefix'), uniquestring(resourceGroup().id))]"
                  },
                  "resources": [
                    {
                      "type": "Microsoft.Storage/storageAccounts",
                      "apiVersion": "2019-04-01",
                      "name": "[[variables('storageAccountName')]",
                      "location": "[[parameters('location')]",
                      "sku": {
                        "name": "[[parameters('storageAccountType')]"
                      },
                      "kind": "StorageV2",
                      "properties": {}
                    }
                  ],
                  "outputs": {
                    "storageAccountName": {
                      "type": "string",
                      "value": "[[variables('storageAccountName')]"
                    }
                  }
                }
              },
              "tags": {}
            }
          ]
        }
      ],
      "outputs": {}
    }
    ```

1. Om du vill lägga till den nya versionen i din mall specifikation distribuerar du din mall med Azure CLI eller PowerShell.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName templateSpecRG `
      -TemplateFile "c:\Templates\azuredeploy.json"
    ```

    ```azurecli
    az deployment group create \
      --name templateSpecRG \
      --template-file "c:\Templates\azuredeploy.json"
    ```

1. Kopiera följande mall och spara den lokalt i en fil med namnet **storage.jspå**.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/deployments",
          "apiVersion": "2020-06-01",
          "name": "demo",
          "properties": {
            "templateLink": {
              "id": "[resourceId('templateSpecRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '2.0')]"
            },
            "parameters": {
            },
            "mode": "Incremental"
          }
        }
      ],
      "outputs": {}
    }
    ```

1. Distribuera din mall med Azure CLI eller PowerShell.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName storageRG `
      -TemplateFile "c:\Templates\storage.json"
    ```

    ```azurecli
    az deployment group create \
      --name storageRG \
      --template-file "c:\Templates\storage.json"
    ```

---

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort båda resurs grupper som du har skapat för att rensa den resurs som du har distribuerat i den här snabb starten.

1. Från Azure-portalen väljer du Resursgrupp från den vänstra menyn.

1. Ange resurs gruppens namn (templateSpecRG och storageRG) i fältet filtrera efter namn.

1. Välj resursgruppens namn.

1. Välj Ta bort resursgrupp från menyn längst upp.

## <a name="next-steps"></a>Nästa steg

Information om hur du skapar en mall-specifikation som innehåller länkade mallar finns i [skapa en mall specifikation av en länkad mall](template-specs-create-linked.md).
