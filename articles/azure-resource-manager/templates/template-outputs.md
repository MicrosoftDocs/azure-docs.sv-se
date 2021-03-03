---
title: Utdata i mallar
description: Beskriver hur du definierar utdataparametrar i en Azure Resource Manager mall (ARM-mall) och bicep-fil.
ms.topic: conceptual
ms.date: 02/19/2021
ms.openlocfilehash: 91feb1a0b653e4b6e96e38df57f87af27e4676f5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101703842"
---
# <a name="outputs-in-arm-templates"></a>Utdata i ARM-mallar

I den här artikeln beskrivs hur du definierar utdataparametrar i din Azure Resource Manager-mall (ARM-mall) och bicep-fil. Du använder utdata när du behöver returnera värden från de distribuerade resurserna.

Formatet för varje utmatnings värde måste matcha en av [data typerna](template-syntax.md#data-types).

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="define-output-values"></a>Definiera värden för utdata

I följande exempel visas hur du returnerar en egenskap från en distribuerad resurs.

# <a name="json"></a>[JSON](#tab/json)

För JSON lägger du till `outputs` avsnittet i mallen. Värdet för utdata hämtar det fullständigt kvalificerade domän namnet för en offentlig IP-adress.

```json
"outputs": {
  "hostname": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))).dnsSettings.fqdn]"
    },
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

För bicep använder du `output` nyckelordet.

I följande exempel `publicIP` är identifieraren för en offentlig IP-adress som distribuerats i bicep-filen. Värdet för utdata hämtar det fullständigt kvalificerade domän namnet för den offentliga IP-adressen.

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

---

Om du behöver mata ut en egenskap som har ett bindestreck i namnet använder du hakparenteser runt namnet i stället för punkt notation. Använd till exempel  `['property-name']` i stället för `.property-name` .

# <a name="json"></a>[JSON](#tab/json)

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "user": {
            "user-name": "Test Person"
        }
    },
    "resources": [
    ],
    "outputs": {
        "nameResult": {
            "type": "string",
            "value": "[variables('user')['user-name']]"
        }
    }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var user = {
  'user-name': 'Test Person'
}

output stringOutput string = user['user-name']
```

---

## <a name="conditional-output"></a>Villkorliga utdata

Du kan villkorligt returnera ett värde. Normalt använder du villkorliga utdata när du har [villkorligt distribuerat](conditional-resource-deployment.md) en resurs. I följande exempel visas hur du villkorligt returnerar resurs-ID: t för en offentlig IP-adress baserat på om en ny har distribuerats:

# <a name="json"></a>[JSON](#tab/json)

I JSON lägger du till `condition` elementet för att definiera om utdata returneras.

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

Om du vill ange en villkorlig utmatning i bicep använder du `?` operatorn. I följande exempel returnerar antingen en slut punkts-URL eller en tom sträng beroende på ett villkor.

```bicep
param deployStorage bool = true
param storageName string
param location string = resourceGroup().location

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = if (deployStorage) {
  name: storageName
  location: location
  kind: 'StorageV2'
  sku:{
    name:'Standard_LRS'
    tier: 'Standard'
  }
  properties: {
    accessTier: 'Hot'
  }
}

output endpoint string = deployStorage ? sa.properties.primaryEndpoints.blob : ''
```

---

Ett enkelt exempel på villkorliga utdata finns i [mallen för villkorsstyrda utdata](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json).

## <a name="dynamic-number-of-outputs"></a>Dynamiskt antal utdata

I vissa fall vet du inte hur många instanser av ett värde du behöver returnera när du skapar mallen. Du kan returnera ett variabelt antal värden med iterativa utdata.

# <a name="json"></a>[JSON](#tab/json)

I JSON lägger du till `copy` elementet för att iterera utdata.

```json
"outputs": {
  "storageEndpoints": {
    "type": "array",
    "copy": {
      "count": "[parameters('storageCount')]",
      "input": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

Iterativa utdata är för närvarande inte tillgängliga för bicep.

---

Mer information finns i [utdata iteration i arm-mallar](copy-outputs.md).

## <a name="linked-templates"></a>Länkade mallar

I JSON-mallar kan du distribuera relaterade mallar med hjälp av [länkade mallar](linked-templates.md). Om du vill hämta värdet för utdata från en länkad mall använder du funktionen [Reference](template-functions-resource.md#reference) i den överordnade mallen. Syntaxen i den överordnade mallen är:

```json
"[reference('<deploymentName>').outputs.<propertyName>.value]"
```

I följande exempel visas hur du ställer in IP-adressen på en belastningsutjämnare genom att hämta ett värde från en länkad mall.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

Om egenskaps namnet har ett bindestreck, använder du hakparenteser runt namnet i stället för punkt notation.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs['resource-ID'].value]"
}
```

Du kan inte använda `reference` funktionen i avsnittet utdata i en [kapslad mall](linked-templates.md#nested-template). Om du vill returnera värdena för en distribuerad resurs i en kapslad mall konverterar du den kapslade mallen till en länkad mall.

Den [offentliga IP-adress mal len](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) skapar en offentlig IP-adress och matar ut resurs-ID: t. [Belastnings Utjämnings mal len](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) länkar till föregående mall. Den använder resurs-ID i utdata när du skapar belastningsutjämnaren.

## <a name="modules"></a>Moduler

I bicep-filer kan du distribuera relaterade mallar med hjälp av moduler. Använd följande syntax för att hämta ett utdata-värde från en modul:

```bicep
<module-name>.outputs.<property-name>
```

I följande exempel visas hur du ställer in IP-adressen på en belastningsutjämnare genom att hämta ett värde från en modul. Namnet på modulen är `publicIP` .

```bicep
publicIPAddress: {
  id: publicIP.outputs.resourceID
}
```

## <a name="example-template"></a>Exempel mal len

Följande mall distribuerar inga resurser. Det visar några sätt att returnera utdata av olika typer.

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/outputs.json":::

# <a name="bicep"></a>[Bicep](#tab/bicep)

Bicep stöder för närvarande inte slingor.

:::code language="bicep" source="~/resourcemanager-templates/azure-resource-manager/outputs.bicep":::

---

## <a name="get-output-values"></a>Hämta utgående värden

När distributionen lyckas returneras värdena för utdata automatiskt i resultatet av distributionen.

Du kan använda skript för att hämta utdata från distributions historiken.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
(Get-AzResourceGroupDeployment `
  -ResourceGroupName <resource-group-name> `
  -Name <deployment-name>).Outputs.resourceID.value
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az deployment group show \
  -g <resource-group-name> \
  -n <deployment-name> \
  --query properties.outputs.resourceID.value
```

---

## <a name="next-steps"></a>Nästa steg

* Mer information om tillgängliga egenskaper för utdata finns i [förstå strukturen och syntaxen för ARM-mallar](template-syntax.md).
