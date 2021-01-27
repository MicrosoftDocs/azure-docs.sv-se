---
title: Variabler i mallar
description: Beskriver hur du definierar variabler i en Azure Resource Manager-mall (ARM-mall).
ms.topic: conceptual
ms.date: 01/26/2021
ms.openlocfilehash: feecc4b5df77e6a3bf51294cb12aabf44899dde5
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98874442"
---
# <a name="variables-in-arm-template"></a>Variabler i ARM-mallen

Den här artikeln beskriver hur du definierar och använder variabler i din Azure Resource Manager-mall (ARM-mall). Du kan använda variabler för att förenkla din mall. I stället för att upprepa komplexa uttryck i hela mallen definierar du en variabel som innehåller det komplexa uttrycket. Sedan kan du referera till variabeln efter behov i hela mallen.

Resource Manager löser variabler innan distributions åtgärderna påbörjas. Oavsett var variabeln används i mallen ersätter Resource Manager den med det matchade värdet.

## <a name="define-variable"></a>Definiera variabel

När du definierar en variabel anger du ett värde-eller mall-uttryck som matchar en [datatyp](template-syntax.md#data-types). Du kan använda värdet från en parameter eller en annan variabel när du skapar variabeln.

Du kan använda [mallar](template-functions.md) i variabel deklarationen, men du kan inte använda funktionen [Reference](template-functions-resource.md#reference) eller någon av [list](template-functions-resource.md#list) funktionerna. Dessa funktioner hämtar körnings status för en resurs och kan inte utföras före distributionen när variablerna är lösta.

I följande exempel visas en variabel definition. Det skapar ett sträng värde för ett lagrings konto namn. Den använder flera mallar för att hämta ett parameter värde och sammanfogar dem till en unik sträng.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

## <a name="use-variable"></a>Använd variabel

I mallen refererar du till värdet för parametern med hjälp av funktionen [variabler](template-functions-deployment.md#variables) . I följande exempel visas hur du använder variabeln för en resurs egenskap.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageName')]",
    ...
  }
]
```

## <a name="example-template"></a>Exempel mal len

Följande mall distribuerar inga resurser. Det visar bara några sätt att deklarera variabler.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variables.json":::

## <a name="configuration-variables"></a>Variabler för konfiguration

Du kan definiera variabler som innehåller relaterade värden för att konfigurera en miljö. Du definierar variabeln som ett objekt med värdena. I följande exempel visas ett objekt som innehåller värden för två miljöer – **test** och **Prod**. Du skickar ett av följande värden under distributionen.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variablesconfigurations.json":::

## <a name="next-steps"></a>Nästa steg

* Mer information om tillgängliga egenskaper för variabler finns i [förstå strukturen och syntaxen för ARM-mallar](template-syntax.md).
* Rekommendationer om hur du skapar variabler finns i [metod tips – variabler](template-best-practices.md#variables).
* För en exempel mall som tilldelar säkerhets regler till en nätverks säkerhets grupp, se [nätverks säkerhets regler](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) och [parameter fil](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json).
