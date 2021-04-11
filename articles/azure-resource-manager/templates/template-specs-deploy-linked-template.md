---
title: Distribuera en mall specifikation som en länkad mall
description: Lär dig hur du distribuerar en befintlig mall-specifikation i en länkad distribution.
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 8d4ccd77c8b37a696fab7494a8d3f8052fc89b35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104889271"
---
# <a name="tutorial-deploy-a-template-spec-as-a-linked-template-preview"></a>Självstudie: Distribuera en mall specifikation som en länkad mall (förhands granskning)

Lär dig hur du distribuerar en befintlig [mall-specifikation](template-specs.md) med hjälp av en [länkad distribution](linked-templates.md#linked-template). Du använder mall-specifikationer för att dela ARM-mallar med andra användare i din organisation. När du har skapat en mall-specifikation kan du distribuera mallen med hjälp av Azure PowerShell eller Azure CLI. Du kan också distribuera mal Lav specifikationen som en del av din lösning med hjälp av en länkad mall.

## <a name="prerequisites"></a>Förutsättningar

Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> Mallens specifikationer är för närvarande en för hands version. Om du vill använda den med Azure PowerShell måste du installera [version 5.0.0 eller senare](/powershell/azure/install-az-ps). Använd [version 2.14.2 eller senare](/cli/azure/install-azure-cli)för att använda den med Azure CLI.

## <a name="create-a-template-spec"></a>Skapa en mall specifikation

Följ [snabb start: skapa och distribuera mall-specifikation](quickstart-create-template-specs.md) för att skapa en mall-specifikation för att distribuera ett lagrings konto. Du behöver resurs grupp namnet för mallens specifikation, mallens Specifikations namn och versions specifikationen för mallen i nästa avsnitt.

## <a name="create-the-main-template"></a>Skapa huvud mal len

Om du vill distribuera en malls specifikation i en ARM-mall lägger du till en [distributions resurs](/azure/templates/microsoft.resources/deployments) i huvud mal len. I `templateLink` egenskapen anger du resurs-ID för en mall specifikation. skapa en mall med följande JSON som kallas **azuredeploy.jspå**. Den här självstudien förutsätter att du har sparat till en sökväg **c:\Templates\deployTS\azuredeploy.js** men du kan använda valfri sökväg.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    },
    "tsResourceGroup":{
      "type": "string",
      "metadata": {
        "Description": "Specifies the resource group name of the template spec."
      }
    },
    "tsName": {
      "type": "string",
      "metadata": {
        "Description": "Specifies the name of the template spec."
      }
    },
    "tsVersion": {
      "type": "string",
      "defaultValue": "1.0.0.0",
      "metadata": {
        "Description": "Specifies the version the template spec."
      }
    },
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "metadata": {
        "Description": "Specifies the storage account type required by the template spec."
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
      "apiVersion": "2020-10-01",
      "name": "createStorage",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "id": "[resourceId(parameters('tsResourceGroup'), 'Microsoft.Resources/templateSpecs/versions', parameters('tsName'), parameters('tsVersion'))]"
        },
        "parameters": {
          "storageAccountType": {
            "value": "[parameters('storageAccountType')]"
          }
        }
      }
    }
  ],
  "outputs": {
    "templateSpecId": {
      "type": "string",
      "value": "[resourceId(parameters('tsResourceGroup'), 'Microsoft.Resources/templateSpecs/versions', parameters('tsName'), parameters('tsVersion'))]"
    }
  }
}
```

Mallens Specifikations-ID genereras med hjälp av [`resourceID()`](template-functions-resource.md#resourceid) funktionen. Resurs grupps argumentet i funktionen resourceID () är valfritt om templateSpec finns i samma resurs grupp för den aktuella distributionen.  Du kan också direkt skicka in resurs-ID som en parameter. Hämta ID: t genom att använda:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$id = (Get-AzTemplateSpec -ResourceGroupName $resourceGroupName -Name $templateSpecName -Version $templateSpecVersion).Versions.Id
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli-interactive
id = $(az ts show --name $templateSpecName --resource-group $resourceGroupName --version $templateSpecVersion --query "id")
```

> [!NOTE]
> Det finns ett känt problem med att hämta ett Specifikations-ID för mallen och tilldela det till en variabel i Windows PowerShell.

---

Syntaxen för att skicka parametrar till specifikationen för mallen är:

```json
"parameters": {
  "storageAccountType": {
    "value": "[parameters('storageAccountType')]"
  }
}
```

> [!NOTE]
> API version `Microsoft.Resources/deployments` måste vara 2020-06-01 eller senare.

## <a name="deploy-the-template"></a>Distribuera mallen

När du distribuerar den länkade mallen distribuerar den både webb programmet och lagrings kontot. Distributionen är samma som att distribuera andra ARM-mallar.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name webRG `
  -Location westus2

New-AzResourceGroupDeployment `
  -ResourceGroupName webRG `
  -TemplateFile "c:\Templates\deployTS\azuredeploy.json" `
  -tsResourceGroup templateSpecRg `
  -tsName storageSpec `
  -tsVersion 1.0
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az group create \
  --name webRG \
  --location westus2

az deployment group create \
  --resource-group webRG \
  --template-file "c:\Templates\deployTS\azuredeploy.json" \
  --parameters tsResourceGroup=templateSpecRG tsName=storageSpec tsVersion=1.0
```

---

## <a name="next-steps"></a>Nästa steg

Information om hur du skapar en mall-specifikation som innehåller länkade mallar finns i [skapa en mall specifikation av en länkad mall](template-specs-create-linked.md).
