---
title: Variabler i mallar
description: Beskriver hur du definierar variabler i en Azure Resource Manager mall (ARM-mall) och bicep-fil.
ms.topic: conceptual
ms.date: 02/19/2021
ms.openlocfilehash: 3ab14c9acfcc2d6c9edd23fb3bc4d876cd5ac756
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102123385"
---
# <a name="variables-in-arm-templates"></a>Variabler i ARM-mallar

Den här artikeln beskriver hur du definierar och använder variabler i din Azure Resource Manager-mall (ARM-mall) eller bicep-fil. Du kan använda variabler för att förenkla din mall. I stället för att upprepa komplexa uttryck i hela mallen definierar du en variabel som innehåller det komplexa uttrycket. Sedan använder du denna variabel vid behov i hela mallen.

Resource Manager löser variabler innan distributions åtgärderna påbörjas. Oavsett var variabeln används i mallen ersätter Resource Manager den med det matchade värdet.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="define-variable"></a>Definiera variabel

När du definierar en variabel anger du inte en [datatyp](data-types.md) för variabeln. Ange i stället ett värde-eller mall-uttryck. Variabel typen härleds från det matchade värdet. I följande exempel anges en variabel till en sträng.

# <a name="json"></a>[JSON](#tab/json)

```json
"variables": {
  "stringVar": "example value"
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var stringVar = 'example value'
```

---

Du kan använda värdet från en parameter eller en annan variabel när du skapar variabeln.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "inputValue": {
    "defaultValue": "deployment parameter",
    "type": "string"
  }
},
"variables": {
  "stringVar": "myVariable",
  "concatToVar": "[concat(variables('stringVar'), '-addtovar') ]",
  "concatToParam": "[concat(parameters('inputValue'), '-addtoparam')]"
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param inputValue string = 'deployment parameter'

var stringVar = 'myVariable'
var concatToVar =  '${stringVar}-addtovar'
var concatToParam = '${inputValue}-addtoparam'
```

---

Du kan använda [mall funktioner](template-functions.md) för att skapa variabelvärdet.

I följande exempel skapas ett sträng värde för ett lagrings konto namn. Den använder flera mallar för att hämta ett parameter värde och sammanfogar dem till en unik sträng.

# <a name="json"></a>[JSON](#tab/json)

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var storageName = '${toLower(storageNamePrefix)}${uniqueString(resourceGroup().id)}'
```

---

I JSON-mallar kan du inte använda [referens](template-functions-resource.md#reference) funktionen eller någon av [list](template-functions-resource.md#list) funktionerna i variabel deklarationen. Dessa funktioner hämtar körnings status för en resurs och kan inte utföras före distributionen när variablerna är lösta.

I bicep-filer är referens-och list funktionerna giltiga när du deklarerar en variabel.

## <a name="use-variable"></a>Använd variabel

I följande exempel visas hur du använder variabeln för en resurs egenskap.

# <a name="json"></a>[JSON](#tab/json)

I en JSON-mall refererar du till värdet för variabeln med hjälp av funktionen [variabler](template-functions-deployment.md#variables) .

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageName')]",
    ...
  }
]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

I en bicep-fil refererar du till värdet för variabeln genom att ange variabel namnet.

```bicep
var storageName = '${toLower(storageNamePrefix)}${uniqueString(resourceGroup().id)}'

resource demoAccount 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageName
```

---

## <a name="example-template"></a>Exempel mal len

Följande mall distribuerar inga resurser. Det visar några sätt att deklarera variabler av olika typer.

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variables.json":::

# <a name="bicep"></a>[Bicep](#tab/bicep)

Bicep stöder för närvarande inte slingor.

:::code language="bicep" source="~/resourcemanager-templates/azure-resource-manager/variables.bicep":::

---

## <a name="configuration-variables"></a>Variabler för konfiguration

Du kan definiera variabler som innehåller relaterade värden för att konfigurera en miljö. Du definierar variabeln som ett objekt med värdena. I följande exempel visas ett objekt som innehåller värden för två miljöer – **test** och **Prod**. Skicka in ett av dessa värden under distributionen.

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variablesconfigurations.json":::

# <a name="bicep"></a>[Bicep](#tab/bicep)

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variablesconfigurations.bicep":::

---

## <a name="next-steps"></a>Nästa steg

* Mer information om tillgängliga egenskaper för variabler finns i [förstå strukturen och syntaxen för ARM-mallar](template-syntax.md).
* Rekommendationer om hur du skapar variabler finns i [metod tips – variabler](template-best-practices.md#variables).
* För en exempel mall som tilldelar säkerhets regler till en nätverks säkerhets grupp, se [nätverks säkerhets regler](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) och [parameter fil](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json).
