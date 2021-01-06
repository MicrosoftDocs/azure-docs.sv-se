---
title: Skapa en mall-specifikation med länkade mallar
description: Lär dig hur du skapar en mall-specifikation med länkade mallar.
ms.topic: conceptual
ms.date: 01/05/2021
ms.openlocfilehash: e5725ece165f5716480afbcb4ef9098274c09993
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97900645"
---
# <a name="tutorial-create-a-template-spec-with-linked-templates-preview"></a>Självstudie: skapa en mall-specifikation med länkade mallar (förhands granskning)

Lär dig hur du skapar en [mall-specifikation](template-specs.md) med en huvud-mall och en [länkad mall](linked-templates.md#linked-template). Du använder mall-specifikationer för att dela ARM-mallar med andra användare i din organisation. Den här artikeln visar hur du skapar en mall-specifikation för att paketera en huvud-mall och dess länkade mallar med hjälp av `relativePath` egenskapen för [distributions resursen](/azure/templates/microsoft.resources/deployments).

## <a name="prerequisites"></a>Förutsättningar

Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> Mallens specifikationer är för närvarande en för hands version. Om du vill använda den med Azure PowerShell måste du installera [version 5.0.0 eller senare](/powershell/azure/install-az-ps). Använd [version 2.14.2 eller senare](/cli/azure/install-azure-cli)för att använda den med Azure CLI.

## <a name="create-linked-templates"></a>Skapa länkade mallar

Skapa huvud mal len och den länkade mallen.

Om du vill länka en mall lägger du till en [distributions resurs](/azure/templates/microsoft.resources/deployments) i huvud mal len. I `templateLink` egenskapen anger du den relativa sökvägen till den länkade mallen i enlighet med sökvägen till den överordnade mallen.

Den länkade mallen kallas **linkedTemplate.jspå** och lagras i en undermapp som kallas **artefakter** i sökvägen där huvud mal len lagras.  Du kan använda något av följande värden för relativePath:

- `./artifacts/linkedTemplate.json`
- `/artifacts/linkedTemplate.json`
- `artifacts/linkedTemplate.json`

`relativePath`Egenskapen är alltid relativ i förhållande till mallfilen där `relativePath` deklareras, så om det finns en annan linkedTemplate2.jspå den som anropas från linkedTemplate.jspå och linkedTemplate2.jspå lagras i samma artefakter undermapp, är relativePath som anges i linkedTemplate.jspå bara `linkedTemplate2.json` .

1. Skapa huvud mal len med följande JSON. Spara huvud mal len som **azuredeploy.jspå** den lokala datorn. Den här självstudien förutsätter att du har sparat till en sökväg **c:\Templates\linkedTS\azuredeploy.js** men du kan använda valfri sökväg.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "location": {
          "type": "string",
          "defaultValue": "westus2",
          "metadata":{
            "description": "Specify the location for the resources."
          }
        },
        "storageAccountType": {
          "type": "string",
          "defaultValue": "Standard_LRS",
          "metadata":{
            "description": "Specify the storage account type."
          }
        }
      },
      "variables": {
        "appServicePlanName": "[concat('plan', uniquestring(resourceGroup().id))]"
      },
      "resources": [
        {
          "type": "Microsoft.Web/serverfarms",
          "apiVersion": "2016-09-01",
          "name": "[variables('appServicePlanName')]",
          "location": "[parameters('location')]",
          "sku": {
            "name": "B1",
            "tier": "Basic",
            "size": "B1",
            "family": "B",
            "capacity": 1
          },
          "kind": "linux",
          "properties": {
            "perSiteScaling": false,
            "reserved": true,
            "targetWorkerCount": 0,
            "targetWorkerSizeId": 0
          }
        },
        {
          "type": "Microsoft.Resources/deployments",
          "apiVersion": "2020-06-01",
          "name": "createStorage",
          "properties": {
            "mode": "Incremental",
            "templateLink": {
              "relativePath": "artifacts/linkedTemplate.json"
            },
            "parameters": {
              "storageAccountType": {
                "value": "[parameters('storageAccountType')]"
              }
            }
          }
        }
      ]
    }
    ```

    > [!NOTE]
    > API version `Microsoft.Resources/deployments` måste vara 2020-06-01 eller senare.

1. Skapa en katalog med namnet **artefakter** i mappen där huvud mal len sparas.
1. Skapa den länkade mallen med följande JSON:

    ```json
    {
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
          "defaultValue": "[resourceGroup().location]",
          "metadata": {
            "description": "Location for all resources."
          }
        }
      },
      "variables": {
        "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
      },
      "resources": [
        {
          "type": "Microsoft.Storage/storageAccounts",
          "apiVersion": "2019-04-01",
          "name": "[variables('storageAccountName')]",
          "location": "[parameters('location')]",
          "sku": {
            "name": "[parameters('storageAccountType')]"
          },
          "kind": "StorageV2",
          "properties": {}
        }
      ],
      "outputs": {
        "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
        }
      }
    }
    ```

1. Spara mallen som **linkedTemplate.js** i mappen **artefakter** .

## <a name="create-template-spec"></a>Skapa mall-specifikation

Specifikationer för mallar lagras i resurs grupper.  Skapa en resurs grupp och skapa sedan en mall-specifikation med följande skript. Mallens Specifikations namn är **webspec**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name templateSpecRG `
  -Location westus2

New-AzTemplateSpec `
  -Name webSpec `
  -Version "1.0.0.0" `
  -ResourceGroupName templateSpecRG `
  -Location westus2 `
  -TemplateFile "c:\Templates\linkedTS\azuredeploy.json"
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az group create \
  --name templateSpecRG \
  --location westus2

az ts create \
  --name webSpec \
  --version "1.0.0.0" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "<path-to-main-template>"
```

---

När du är färdig kan du Visa mallens specifikation från Azure Portal eller genom att använda följande cmdlet:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Get-AzTemplateSpec -ResourceGroupName templatespecRG -Name webSpec
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az ts show --name webSpec --resource-group templateSpecRG --version "1.0.0.0"
```

---

## <a name="deploy-template-spec"></a>Specifikation för att distribuera mall

Nu kan du distribuera mallens specifikation. Att distribuera mallens specifikation är precis som att distribuera mallen den innehåller, förutom att du skickar in resurs-ID: t för mallens specifikation. Du använder samma distributions kommandon och skickar vid behov parameter värden för mallen specifikation.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name webRG `
  -Location westus2

$id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name webSpec -Version "1.0.0.0").Versions.Id

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName webRG
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az group create \
  --name webRG \
  --location westus2

id = $(az ts show --name webSpec --resource-group templateSpecRG --version "1.0.0.0" --query "id")

az deployment group create \
  --resource-group webRG \
  --template-spec $id
```

> [!NOTE]
> Det finns ett känt problem med att hämta ett Specifikations-ID för mallen och tilldela det till en variabel i Windows PowerShell.

---

## <a name="next-steps"></a>Nästa steg

Information om hur du distribuerar en mall specifikation som en länkad mall finns i [självstudie: Distribuera en mall specifikation som en länkad mall](template-specs-deploy-linked-template.md).
