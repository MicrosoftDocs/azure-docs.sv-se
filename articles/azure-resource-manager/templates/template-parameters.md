---
title: Parametrar i mallar
description: Beskriver hur du definierar parametrar i en Azure Resource Manager-mall (ARM-mall).
ms.topic: conceptual
ms.date: 11/24/2020
ms.openlocfilehash: 929d871bc0087d4fda585773b349dee4e0945c7c
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97934721"
---
# <a name="parameters-in-arm-templates"></a>Parametrar i ARM-mallar

Den här artikeln beskriver hur du definierar och använder parametrar i din Azure Resource Manager-mall (ARM-mall). Genom att ange olika värden för parametrar kan du återanvända en mall för olika miljöer.

Resource Manager matchar parameter värden innan distributions åtgärderna startas. Oavsett var parametern används i mallen ersätter Resource Manager den med det matchade värdet.

Varje parameter måste anges till en av [data typerna](template-syntax.md#data-types).

## <a name="define-parameter"></a>Definiera parameter

I följande exempel visas en enkel parameter definition. Den definierar en parameter med namnet `storageSKU` . Parametern är ett sträng värde och accepterar bara värden som är giltiga för den avsedda användningen. Parametern använder ett standardvärde när inget värde anges under distributionen.

```json
"parameters": {
  "storageSKU": {
    "type": "string",
    "allowedValues": [
      "Standard_LRS",
      "Standard_ZRS",
      "Standard_GRS",
      "Standard_RAGRS",
      "Premium_LRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
      "description": "The type of replication to use for the storage account."
    }
  }
}
```

## <a name="use-parameter"></a>Använd parameter

I mallen refererar du till värdet för parametern med hjälp av funktionen [parametrar](template-functions-deployment.md#parameters) . I följande exempel används parametervärdet för att ange SKU för lagrings kontot.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "sku": {
      "name": "[parameters('storageSKU')]"
    },
    ...
  }
]
```

## <a name="template-functions"></a>Mallfunktioner

När du anger standardvärdet för en parameter kan du använda de flesta mall-funktionerna. Du kan använda ett annat parameter värde för att bygga ett standardvärde. Följande mall visar hur funktionen används i standardvärdet. När inget namn har angetts för platsen skapas ett unikt sträng värde och läggs till på **platsen**. När inget namn har angetts för värd planen tar det med värdet för platsen och lägger till **plan**.

```json
"parameters": {
  "siteName": {
    "type": "string",
    "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]",
    "metadata": {
      "description": "The site name. To use the default value, do not specify a new value."
    }
  },
  "hostingPlanName": {
    "type": "string",
    "defaultValue": "[concat(parameters('siteName'),'-plan')]",
    "metadata": {
      "description": "The host name. To use the default value, do not specify a new value."
    }
  }
}
```

Du kan inte använda [referens](template-functions-resource.md#reference) funktionen eller någon av [list](template-functions-resource.md#list) funktionerna i avsnittet parametrar. Dessa funktioner hämtar körnings status för en resurs och kan inte utföras före distributionen när parametrarna har matchats.

## <a name="objects-as-parameters"></a>Objekt som parametrar

Det kan vara lättare att organisera relaterade värden genom att skicka dem som ett objekt. Den här metoden minskar också antalet parametrar i mallen.

I följande exempel visas en parameter som är ett objekt. Standardvärdet visar de förväntade egenskaperna för objektet.

```json
"parameters": {
  "VNetSettings": {
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
```

Du refererar till egenskaperna för objektet med hjälp av punkt operatorn.

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2015-06-15",
    "name": "[parameters('VNetSettings').name]",
    "location": "[parameters('VNetSettings').location]",
    "properties": {
      "addressSpace":{
        "addressPrefixes": [
          "[parameters('VNetSettings').addressPrefixes[0].addressPrefix]"
        ]
      },
      "subnets":[
        {
          "name":"[parameters('VNetSettings').subnets[0].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[0].addressPrefix]"
          }
        },
        {
          "name":"[parameters('VNetSettings').subnets[1].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[1].addressPrefix]"
          }
        }
      ]
    }
  }
]
```

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
