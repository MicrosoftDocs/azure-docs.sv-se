---
title: Mall funktioner – distribution
description: Beskriver de funktioner som används i en Azure Resource Manager mall (ARM-mall) för att hämta distributions information.
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: a9a073284c62efac4e77f8f9b35e8730c350e5f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101722729"
---
# <a name="deployment-functions-for-arm-templates"></a>Distributions funktioner för ARM-mallar

Resource Manager innehåller följande funktioner för att hämta värden som är relaterade till den aktuella distributionen av din Azure Resource Manager-mall (ARM-mall):

* [spridningen](#deployment)
* [miljö](#environment)
* [komponentparametrar](#parameters)
* [användarvariabler](#variables)

För att hämta värden från resurser, resurs grupper eller prenumerationer, se [resurs funktioner](template-functions-resource.md).

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="deployment"></a>distribution

`deployment()`

Returnerar information om den aktuella distributions åtgärden.

### <a name="return-value"></a>Returvärde

Den här funktionen returnerar det objekt som skickas under distributionen. Egenskaperna i det returnerade objektet skiljer sig åt beroende på om du är:

* distribuera en mall eller en mall-specifikation.
* distribuera en mall som är en lokal fil eller distribuera en mall som är en fjärrstyrd fil som nås via en URI.
* distribuera till en resurs grupp eller distribuera till något av de andra omfången ([Azure-prenumeration](deploy-to-subscription.md), [hanterings grupp](deploy-to-management-group.md)eller [klient organisation](deploy-to-tenant.md)).

När du distribuerar en lokal mall till en resurs grupp: funktionen returnerar följande format:

```json
{
  "name": "",
  "properties": {
    "template": {
      "$schema": "",
      "contentVersion": "",
      "parameters": {},
      "variables": {},
      "resources": [],
      "outputs": {}
    },
    "templateHash": "",
    "parameters": {},
    "mode": "",
    "provisioningState": ""
  }
}
```

När du distribuerar en fjärran sluten mall till en resurs grupp: funktionen returnerar följande format:

```json
{
  "name": "",
  "properties": {
    "templateLink": {
      "uri": ""
    },
    "template": {
      "$schema": "",
      "contentVersion": "",
      "parameters": {},
      "variables": {},
      "resources": [],
      "outputs": {}
    },
    "templateHash": "",
    "parameters": {},
    "mode": "",
    "provisioningState": ""
  }
}
```

När du distribuerar en mall specifikation till en resurs grupp: funktionen returnerar följande format:

```json
{
  "name": "",
  "properties": {
    "templateLink": {
      "id": ""
    },
    "template": {
      "$schema": "",
      "contentVersion": "",
      "parameters": {},
      "variables": {},
      "resources": [],
      "outputs": {}
    },
    "templateHash": "",
    "parameters": {},
    "mode": "",
    "provisioningState": ""
  }
}
```

När du distribuerar till en Azure-prenumeration, hanterings grupp eller klient, innehåller returvärdet en `location` egenskap. Egenskapen Location ingår när du distribuerar antingen en lokal mall eller en extern mall. Formatet är:

```json
{
  "name": "",
  "location": "",
  "properties": {
    "template": {
      "$schema": "",
      "contentVersion": "",
      "resources": [],
      "outputs": {}
    },
    "templateHash": "",
    "parameters": {},
    "mode": "",
    "provisioningState": ""
  }
}
```

### <a name="remarks"></a>Kommentarer

Du kan använda Deployment () för att länka till en annan mall baserat på den överordnade mallens URI.

# <a name="json"></a>[JSON](#tab/json)

```json
"variables": {
  "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var sharedTemplateUrl = uri(deployment().prperties.templateLink.uri, 'shared-resources.json')
```

---

Om du distribuerar om en mall från distributions historiken i portalen distribueras mallen som en lokal fil. `templateLink`Egenskapen returneras inte i distributions funktionen. Om mallen är beroende av `templateLink` att skapa en länk till en annan mall ska du inte använda portalen för att distribuera om den. Använd i stället de kommandon som du använde för att distribuera mallen från början.

### <a name="example"></a>Exempel

Följande [exempel-mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deployment.json) returnerar distributions objekt:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "deploymentOutput": {
      "type": "object",
      "value": "[deployment()]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output deploymentOutput object = deployment()
```

---

Föregående exempel returnerar följande objekt:

```json
{
  "name": "deployment",
  "properties": {
    "template": {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": [],
      "outputs": {
        "deploymentOutput": {
          "type": "Object",
          "value": "[deployment()]"
        }
      }
    },
    "templateHash": "13135986259522608210",
    "parameters": {},
    "mode": "Incremental",
    "provisioningState": "Accepted"
  }
}
```

## <a name="environment"></a>miljö

`environment()`

Returnerar information om den Azure-miljö som används för distribution.

### <a name="return-value"></a>Returvärde

Den här funktionen returnerar egenskaper för den aktuella Azure-miljön. I följande exempel visas egenskaperna för Global Azure. Suveräna moln kan returnera något annorlunda egenskaper.

```json
{
  "name": "",
  "gallery": "",
  "graph": "",
  "portal": "",
  "graphAudience": "",
  "activeDirectoryDataLake": "",
  "batch": "",
  "media": "",
  "sqlManagement": "",
  "vmImageAliasDoc": "",
  "resourceManager": "",
  "authentication": {
    "loginEndpoint": "",
    "audiences": [
      "",
      ""
    ],
    "tenant": "",
    "identityProvider": ""
  },
  "suffixes": {
    "acrLoginServer": "",
    "azureDatalakeAnalyticsCatalogAndJob": "",
    "azureDatalakeStoreFileSystem": "",
    "azureFrontDoorEndpointSuffix": "",
    "keyvaultDns": "",
    "sqlServerHostname": "",
    "storage": ""
  }
}
```

### <a name="example"></a>Exempel

Följande exempel-mall returnerar miljö objekt.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "environmentOutput": {
      "type": "object",
      "value": "[environment()]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output environmentOutput object = environment()
```

---

Föregående exempel returnerar följande objekt när det distribueras till Global Azure:

```json
{
  "name": "AzureCloud",
  "gallery": "https://gallery.azure.com/",
  "graph": "https://graph.windows.net/",
  "portal": "https://portal.azure.com",
  "graphAudience": "https://graph.windows.net/",
  "activeDirectoryDataLake": "https://datalake.azure.net/",
  "batch": "https://batch.core.windows.net/",
  "media": "https://rest.media.azure.net",
  "sqlManagement": "https://management.core.windows.net:8443/",
  "vmImageAliasDoc": "https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json",
  "resourceManager": "https://management.azure.com/",
  "authentication": {
    "loginEndpoint": "https://login.windows.net/",
    "audiences": [
      "https://management.core.windows.net/",
      "https://management.azure.com/"
    ],
    "tenant": "common",
    "identityProvider": "AAD"
  },
  "suffixes": {
    "acrLoginServer": ".azurecr.io",
    "azureDatalakeAnalyticsCatalogAndJob": "azuredatalakeanalytics.net",
    "azureDatalakeStoreFileSystem": "azuredatalakestore.net",
    "azureFrontDoorEndpointSuffix": "azurefd.net",
    "keyvaultDns": ".vault.azure.net",
    "sqlServerHostname": ".database.windows.net",
    "storage": "core.windows.net"
  }
}
```

## <a name="parameters"></a>parametrar

`parameters(parameterName)`

Returnerar ett parameter värde. Det angivna parameter namnet måste definieras i avsnittet Parameters i mallen.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| parameterName |Ja |sträng |Namnet på den parameter som ska returneras. |

### <a name="return-value"></a>Returvärde

Värdet för den angivna parametern.

### <a name="remarks"></a>Kommentarer

Normalt använder du parametrar för att ange resurs värden. I följande exempel anges namnet på webbplatsen till det parameter värde som överfördes under distributionen.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "siteName": {
    "type": "string"
  }
}, "resources": [
  {
    "type": "Microsoft.Web/Sites",
    "apiVersion": "2016-08-01",
    "name": "[parameters('siteName')]",
    ...
  }
]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param siteName string

resource mySite 'Microsoft.Web/Sites@2016-08-01' = {
  name: siteName
  ...
}
```

---

### <a name="example"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/parameters.json) visas en förenklad användning av funktionen Parameters.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringParameter": {
      "type": "string",
      "defaultValue": "option 1"
    },
    "intParameter": {
      "type": "int",
      "defaultValue": 1
    },
    "objectParameter": {
      "type": "object",
      "defaultValue": {
        "one": "a",
        "two": "b"
      }
    },
    "arrayParameter": {
      "type": "array",
      "defaultValue": [ 1, 2, 3 ]
    },
    "crossParameter": {
      "type": "string",
      "defaultValue": "[parameters('stringParameter')]"
    }
  },
  "variables": {},
  "resources": [],
  "outputs": {
    "stringOutput": {
      "value": "[parameters('stringParameter')]",
      "type": "string"
    },
    "intOutput": {
      "value": "[parameters('intParameter')]",
      "type": "int"
    },
    "objectOutput": {
      "value": "[parameters('objectParameter')]",
      "type": "object"
    },
    "arrayOutput": {
      "value": "[parameters('arrayParameter')]",
      "type": "array"
    },
    "crossOutput": {
      "value": "[parameters('crossParameter')]",
      "type": "string"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param stringParameter string = 'option 1'
param intParameter int = 1
param objectParameter object = {
  'one': 'a'
  'two': 'b'
}
param arrayParameter array = [
  1
  2
  3
]
param crossParameter string = stringParameter

output stringOutput string = stringParameter
output intOutput int = intParameter
output objectOutput object = objectParameter
output arrayOutput array = arrayParameter
output crossOutput string = crossParameter
```

---

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| stringOutput | Sträng | alternativ 1 |
| intOutput | Int | 1 |
| objectOutput | Objekt | {"One": "a", "två": "b"} |
| arrayOutput | Matris | [1, 2, 3] |
| crossOutput | Sträng | alternativ 1 |

Mer information om hur du använder parametrar finns [i parametrar i arm-mallar](template-parameters.md).

## <a name="variables"></a>användarvariabler

`variables(variableName)`

Returnerar värdet för variabeln. Det angivna variabel namnet måste definieras i avsnittet Variables i mallen.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| variableName |Ja |Sträng |Namnet på variabeln som ska returneras. |

### <a name="return-value"></a>Returvärde

Värdet för den angivna variabeln.

### <a name="remarks"></a>Kommentarer

Normalt använder du variabler för att förenkla din mall genom att endast konstruera komplexa värden en gång. I följande exempel skapas ett unikt namn för ett lagrings konto.

# <a name="json"></a>[JSON](#tab/json)

```json
"variables": {
  "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageName')]",
    ...
  },
  {
    "type": "Microsoft.Compute/virtualMachines",
    "dependsOn": [
      "[variables('storageName')]"
    ],
    ...
  }
],

```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var storageName = 'storage${uniqueString(resourceGroup().id)}'

resource myStorage 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageName
  ...
}

resource myVm 'Microsoft.Compute/virtualMachines@2020-06-01' = {
  ...
}
```

---

### <a name="example"></a>Exempel

Följande [exempel-mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/variables.json) returnerar olika variabel värden.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "var1": "myVariable",
    "var2": [ 1, 2, 3, 4 ],
    "var3": "[ variables('var1') ]",
    "var4": {
      "property1": "value1",
      "property2": "value2"
    }
  },
  "resources": [],
  "outputs": {
    "exampleOutput1": {
      "value": "[variables('var1')]",
      "type": "string"
    },
    "exampleOutput2": {
      "value": "[variables('var2')]",
      "type": "array"
    },
    "exampleOutput3": {
      "value": "[variables('var3')]",
      "type": "string"
    },
    "exampleOutput4": {
      "value": "[variables('var4')]",
      "type": "object"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var var1 = 'myVariable'
var var2 = [
  1
  2
  3
  4
]
var var3 = var1
var var4 = {
  'property1': 'value1'
  'property2': 'value2'
}

output exampleOutput1 string = var1
output exampleOutput2 array = var2
output exampleOutput3 string = var3
output exampleOutput4 object = var4
```

---

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| exampleOutput1 | Sträng | Variabel |
| exampleOutput2 | Matris | [1, 2, 3, 4] |
| exampleOutput3 | Sträng | Variabel |
| exampleOutput4 |  Objekt | {"Egenskap1": "värde1", "Egenskap2": "värde2"} |

Mer information om hur du använder variabler finns [i variabler i arm-mallen](template-variables.md).

## <a name="next-steps"></a>Nästa steg

* En beskrivning av avsnitten i en ARM-mall finns i [förstå strukturen och syntaxen för ARM-mallar](template-syntax.md).
