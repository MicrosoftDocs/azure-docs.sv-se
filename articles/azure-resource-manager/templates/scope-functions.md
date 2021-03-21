---
title: Mall funktioner i omfångs distributioner
description: Beskriver hur du löser mallarnas funktioner i omfångs distributioner. Omfånget kan vara en klient, hanterings grupper, prenumerationer och resurs grupper.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: f128448380612bc9b8d9114226e8a3036feeead8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99492101"
---
# <a name="arm-template-functions-in-deployment-scopes"></a>ARM-mallens funktioner i distributions omfång

Med Azure Resource Manager mallar (ARM-mallar) kan du distribuera till resurs grupper, prenumerationer, hanterings grupper eller klienter. I allmänhet fungerar [arm-mallarna](template-functions.md) likadant för alla omfattningar. I den här artikeln beskrivs skillnaderna som finns för vissa funktioner beroende på omfattningen.

## <a name="supported-functions"></a>Funktioner som stöds

När du distribuerar till olika omfång finns det några viktiga överväganden:

* Funktionen [resourceGroup ()](template-functions-resource.md#resourcegroup) **stöds** för resurs grupps distributioner.
* Funktionen [Subscription ()](template-functions-resource.md#subscription) **stöds** för distributioner av resurs grupper och prenumerationer.
* Funktionerna [Reference ()](template-functions-resource.md#reference) och [List ()](template-functions-resource.md#list) **stöds** för alla omfång.
* Använd [resourceId ()](template-functions-resource.md#resourceid) för att hämta ID: t för en resurs som distribuerats i resurs gruppen.

  ```json
  "subnet": {
    "id": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnet1Name'))]"
  }
  ```

* Använd funktionen [subscriptionResourceId ()](template-functions-resource.md#subscriptionresourceid) för att hämta ID: t för en resurs som distribueras i prenumerationen.

  Om du till exempel vill hämta resurs-ID för en princip definition som distribueras till en prenumeration använder du:

  ```json
  "roleDefinitionId": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
  ```

* Använd funktionen [extensionResourceId ()](template-functions-resource.md#extensionresourceid) för resurser som implementeras som tillägg för hanterings gruppen. Anpassade princip definitioner som distribueras till hanterings gruppen är tillägg för hanterings gruppen.

  Använd följande för att hämta resurs-ID för en anpassad princip definition på hanterings grupps nivå:

  ```json
  "policyDefinitionId": "[extensionResourceId(variables('mgScope'), 'Microsoft.Authorization/policyDefinitions', parameters('policyDefinitionID'))]"
  ```

* Använd funktionen [tenantResourceId ()](template-functions-resource.md#tenantresourceid) för att hämta ID: t för en resurs som distribueras hos klienten. Inbyggda princip definitioner är klient nivå resurser. När du tilldelar en inbyggd princip på hanterings grupps nivå använder du funktionen tenantResourceId.

  Om du vill hämta resurs-ID för en inbyggd princip definition använder du:

  ```json
  "policyDefinitionId": "[tenantResourceId('Microsoft.Authorization/policyDefinitions', parameters('policyDefinitionID'))]"
  ```

## <a name="function-resolution-in-scopes"></a>Funktions upplösning i omfattningar

När du distribuerar till fler än ett omfång matchas [resourceGroup ()](template-functions-resource.md#resourcegroup) och [prenumerationen ()](template-functions-resource.md#subscription) på olika sätt utifrån hur du anger mallen. När du länkar till en extern mall, matchas alltid funktionerna med omfånget för mallen. När du kapslar en mall i en överordnad mall använder du `expressionEvaluationOptions` egenskapen för att ange om funktionerna ska matcha till resurs gruppen och prenumerationen för den överordnade mallen eller den kapslade mallen. Ställ in egenskapen på för `inner` att matcha omfånget för den kapslade mallen. Ställ in egenskapen på för `outer` att matcha omfånget för den överordnade mallen.

Följande tabell visar om funktionerna matchas med den överordnade eller inbäddade resurs gruppen och prenumerationen.

| Malltyp | Omfång | Lösning |
| ------------- | ----- | ---------- |
| Nest        | yttre (standard) | Överordnad resurs grupp |
| Nest        | innersta | Under resurs grupp |
| länkade        | Ej tillämpligt   | Under resurs grupp |

Följande [exempel-mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) visar:

* kapslad mall med standard omfång (yttre)
* kapslad mall med inre omfång
* länkad mall

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crossresourcegroupproperties.json":::

Testa föregående mall och se resultatet med PowerShell eller Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name parentGroup -Location southcentralus
New-AzResourceGroup -Name inlineGroup -Location southcentralus
New-AzResourceGroup -Name linkedGroup -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

Utdata från föregående exempel är:

```output
 Name             Type                       Value
 ===============  =========================  ==========
 parentRG         String                     Parent resource group is parentGroup
 defaultScopeRG   String                     Default scope resource group is parentGroup
 innerScopeRG     String                     Inner scope resource group is inlineGroup
 linkedRG         String                     Linked resource group is linkedgroup
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

Utdata från föregående exempel är:

```output
"outputs": {
  "defaultScopeRG": {
    "type": "String",
    "value": "Default scope resource group is parentGroup"
  },
  "innerScopeRG": {
    "type": "String",
    "value": "Inner scope resource group is inlineGroup"
  },
  "linkedRG": {
    "type": "String",
    "value": "Linked resource group is linkedGroup"
  },
  "parentRG": {
    "type": "String",
    "value": "Parent resource group is parentGroup"
  }
},
```

---

## <a name="next-steps"></a>Nästa steg

* Information om hur du definierar parametrar i din mall finns i [förstå strukturen och syntaxen för ARM-mallar](template-syntax.md).
* Tips om hur du löser vanliga distributions fel finns i [Felsöka vanliga problem med Azure-distribution med Azure Resource Manager](common-deployment-errors.md).
* Information om hur du distribuerar en mall som kräver en SAS-token finns i [distribuera privat arm-mall med SAS-token](secure-template-with-sas-token.md).
