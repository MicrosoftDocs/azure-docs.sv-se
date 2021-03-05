---
title: Parametrar i mallar
description: Beskriver hur du definierar parametrar i en Azure Resource Manager mall (ARM-mall) och bicep-fil.
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: a8608e4733960d7f5ba7e5f548c47f16a2b244bb
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102123402"
---
# <a name="parameters-in-arm-templates"></a>Parametrar i ARM-mallar

Den här artikeln beskriver hur du definierar och använder parametrar i din Azure Resource Manager-mall (ARM-mall) och bicep-fil. Genom att ange olika värden för parametrar kan du återanvända en mall för olika miljöer.

Resource Manager matchar parameter värden innan distributions åtgärderna startas. Oavsett var parametern används i mallen ersätter Resource Manager den med det matchade värdet.

Varje parameter måste anges till en av [data typerna](data-types.md).

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="minimal-declaration"></a>Minimal deklaration

Som minst måste varje parameter ha ett namn och en typ. En parameter kan inte ha samma namn som en variabel, resurs, utdata eller annan parameter i samma omfång i bicep.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "demoString": {
    "type": "string"
  },
  "demoInt": {
    "type": "int"
  },
  "demoBool": {
    "type": "bool"
  },
  "demoObject": {
    "type": "object"
  },
  "demoArray": {
    "type": "array"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param demoString string
param demoInt int
param demoBool bool
param demoObject object
param demoArray array
```

---

## <a name="secure-parameters"></a>Säkra parametrar

Du kan markera sträng-eller objekt parametrar som säkra. Värdet för en säker parameter sparas inte i distributions historiken och loggas inte.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "demoPassword": {
    "type": "secureString"
  },
  "demoSecretObject": {
    "type": "secureObject"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@secure()
param demoPassword string

@secure()
param demoSecretObject object
```

---

## <a name="allowed-values"></a>Tillåtna värden

Du kan definiera tillåtna värden för en parameter. Du anger tillåtna värden i en matris. Distributionen Miss lyckas under verifieringen om ett värde har angetts för parametern som inte är ett av de tillåtna värdena.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "demoEnum": {
    "type": "string",
    "allowedValues": [
      "one",
      "two"
    ]
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@allowed([
  'one'
  'two'
])
param demoEnum string
```

---

## <a name="default-value"></a>Standardvärde

Du kan ange ett standardvärde för en parameter. Standardvärdet används när ett värde inte anges under distributionen.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "demoParam": {
    "type": "string",
    "defaultValue": "Contoso"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param demoParam string = 'Contoso'
```

---

Använd följande syntax om du vill ange ett standardvärde tillsammans med andra egenskaper för parametern.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "demoParam": {
    "type": "string",
    "defaultValue": "Contoso",
    "allowedValues": [
      "Contoso",
      "Fabrikam"
    ]
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@allowed([
  'Contoso'
  'Fabrikam'
])
param demoParam string = 'Contoso'
```

---

Du kan använda uttryck med standardvärdet. Du kan inte använda [referens](template-functions-resource.md#reference) funktionen eller någon av [list](template-functions-resource.md#list) funktionerna i avsnittet parametrar. Dessa funktioner hämtar körnings status för en resurs och kan inte utföras före distributionen när parametrarna har matchats.

Uttryck är inte tillåtna med andra parameter egenskaper.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param location string = resourceGroup().location
```

---

Du kan använda ett annat parameter värde för att bygga ett standardvärde. Följande mall skapar ett värd prenumerations namn från plats namnet.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "siteName": {
    "type": "string",
    "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]"
  },
  "hostingPlanName": {
    "type": "string",
    "defaultValue": "[concat(parameters('siteName'),'-plan')]"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param siteName string = 'site${uniqueString(resourceGroup().id)}'
param hostingPlanName string = '${siteName}-plan'
```

---

## <a name="length-constraints"></a>Längd begränsningar

Du kan ange minsta och högsta längd för sträng-och mat ris parametrar. Du kan ange ett eller båda villkor. För strängar anger längden antalet tecken. För matriser anger längden antalet objekt i matrisen.

I följande exempel deklareras två parametrar. En parameter är för ett lagrings konto namn som måste innehålla 3-24 tecken. Den andra parametern är en matris som måste innehålla från 1-5 objekt.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "storageAccountName": {
    "type": "string",
    "minLength": 3,
    "maxLength": 24
  },
  "appNames": {
    "type": "array",
    "minLength": 1,
    "maxLength": 5
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string

@minLength(1)
@maxLength(5)
param appNames array
```

---

## <a name="integer-constraints"></a>Heltals begränsningar

Du kan ange lägsta och högsta värden för heltals parametrar. Du kan ange ett eller båda villkor.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "month": {
    "type": "int",
    "minValue": 1,
    "maxValue": 12
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minValue(1)
@maxValue(12)
param month int
```

---

## <a name="description"></a>Beskrivning

Du kan lägga till en beskrivning till en parameter för att hjälpa användare av din mall att förstå det värde som ska tillhandahållas. När du distribuerar mallen via portalen används den text som du anger i beskrivningen automatiskt som ett tips för den parametern. Lägg endast till en beskrivning när texten innehåller mer information än vad som kan härledas från parameter namnet.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "virtualMachineSize": {
    "type": "string",
    "metadata": {
      "description": "Must be at least Standard_A3 to support 2 NICs."
    },
    "defaultValue": "Standard_DS1_v2"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@description('Must be at least Standard_A3 to support 2 NICs.')
param virtualMachineSize string = 'Standard_DS1_v2'
```

---

## <a name="use-parameter"></a>Använd parameter

I en JSON-mall refererar du till värdet för parametern med hjälp av funktionen [parametrar](template-functions-deployment.md#parameters) . I bicep använder du parameter namnet. I följande exempel används ett parameter värde för ett Key Vault namn.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vaultName": {
      "type": "string",
      "defaultValue": "[format('keyVault{0}', uniqueString(resourceGroup().id))]"
    }
  },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2019-09-01",
      "name": "[parameters('vaultName')]",
      ...
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param vaultName string = 'keyVault${uniqueString(resourceGroup().id)}'

resource keyvault 'Microsoft.KeyVault/vaults@2019-09-01' = {
  name: vaultName
  ...
}
```

---

## <a name="objects-as-parameters"></a>Objekt som parametrar

Det kan vara lättare att organisera relaterade värden genom att skicka dem som ett objekt. Den här metoden minskar också antalet parametrar i mallen.

I följande exempel visas en parameter som är ett objekt. Standardvärdet visar de förväntade egenskaperna för objektet. Dessa egenskaper används när du definierar den resurs som ska distribueras.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vNetSettings": {
      "type": "object",
      "defaultValue": {
        "name": "VNet1",
        "location": "eastus",
        "addressPrefixes": [
          {
            "name": "firstPrefix",
            "addressPrefix": "10.0.0.0/22"
          }
        ],
        "subnets": [
          {
            "name": "firstSubnet",
            "addressPrefix": "10.0.0.0/24"
          },
          {
            "name": "secondSubnet",
            "addressPrefix": "10.0.1.0/24"
          }
        ]
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Network/virtualNetworks",
      "apiVersion": "2020-06-01",
      "name": "[parameters('vNetSettings').name]",
      "location": "[parameters('vNetSettings').location]",
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "[parameters('vNetSettings').addressPrefixes[0].addressPrefix]"
          ]
        },
        "subnets": [
          {
            "name": "[parameters('vNetSettings').subnets[0].name]",
            "properties": {
              "addressPrefix": "[parameters('vNetSettings').subnets[0].addressPrefix]"
            }
          },
          {
            "name": "[parameters('vNetSettings').subnets[1].name]",
            "properties": {
              "addressPrefix": "[parameters('vNetSettings').subnets[1].addressPrefix]"
            }
          }
        ]
      }
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param vNetSettings object = {
  name: 'VNet1'
  location: 'eastus'
  addressPrefixes: [
    {
      name: 'firstPrefix'
      addressPrefix: '10.0.0.0/22'
    }
  ]
  subnets: [
    {
      name: 'firstSubnet'
      addressPrefix: '10.0.0.0/24'
    }
    {
      name: 'secondSubnet'
      addressPrefix: '10.0.1.0/24'
    }
  ]
}
resource vnet 'Microsoft.Network/virtualNetworks@2020-06-01' = {
  name: vNetSettings.name
  location: vNetSettings.location
  properties: {
    addressSpace: {
      addressPrefixes: [
        vNetSettings.addressPrefixes[0].addressPrefix
      ]
    }
    subnets: [
      {
        name: vNetSettings.subnets[0].name
        properties: {
          addressPrefix: vNetSettings.subnets[0].addressPrefix
        }
      }
      {
        name: vNetSettings.subnets[1].name
        properties: {
          addressPrefix: vNetSettings.subnets[1].addressPrefix
        }
      }
    ]
  }
}
```

---

## <a name="example-templates"></a>Exempel på mallar

I följande exempel demonstreras scenarier för att använda parametrar.

|Mall  |Beskrivning  |
|---------|---------|
|[parametrar med funktioner för standardvärden](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Visar hur du använder mall funktioner när du definierar standardvärden för parametrar. Mallen distribuerar inga resurser. Den skapar parameter värden och returnerar dessa värden. |
|[parameter objekt](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Visar hur man använder ett objekt för en parameter. Mallen distribuerar inga resurser. Den skapar parameter värden och returnerar dessa värden. |

## <a name="next-steps"></a>Nästa steg

* Mer information om tillgängliga egenskaper för parametrar finns i [förstå strukturen och syntaxen för ARM-mallar](template-syntax.md).
* Information om hur du skickar parameter värden som en fil finns i [create Resource Manager parameter File](parameter-files.md).
* Rekommendationer för att skapa parametrar finns i [metod tips – parametrar](template-best-practices.md#parameters).
