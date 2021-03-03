---
title: Deklarera resurser i mallar
description: Beskriver hur du deklarerar resurser som ska distribueras i en Azure Resource Manager-mall (ARM-mall).
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 13f4a08162c40cbb36173627d88a4a8202a4ed26
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101746662"
---
# <a name="resource-declaration-in-arm-templates"></a>Resurs deklaration i ARM-mallar

Om du vill distribuera en resurs via en Azure Resource Manager-mall (ARM-mall) lägger du till en resurs deklaration. Använd `resources` matrisen för JSON-mallen eller `resource` nyckelordet för bicep.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="set-resource-type-and-version"></a>Ange resurs typ och version

När du lägger till en resurs i mallen börjar du med att ange resurs typen och API-versionen. Värdena avgör vilka andra egenskaper som är tillgängliga för resursen.

I följande exempel visas hur du anger resurs typ och API-version för ett lagrings konto. Exemplet visar inte fullständig resurs deklaration.

# <a name="json"></a>[JSON](#tab/json)

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    ...
  }
]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  ...
}
```

---

För bicep anger du ett symboliskt namn för resursen. I föregående exempel är det symboliska namnet `sa` . Du kan använda valfritt värde för det symboliska namnet, men det får inte vara samma som en annan resurs, parameter eller variabel i mallen. Det symboliska namnet är inte detsamma som resurs namnet. Du kan använda det symboliska namnet för att enkelt referera till resursen i andra delar av mallen.

## <a name="set-resource-name"></a>Ange resurs namn

Varje resurs har ett namn. När du anger resurs namnet bör du tänka på [reglerna och begränsningarna för resurs namn](../management/resource-name-rules.md).

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "storageAccountName": {
    "type": "string",
    "minLength": 3,
    "maxLength": 24
  }
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    "name": "[parameters('storageAccountName')]",
    ...
  }
]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName
  ...
}
```

---

## <a name="set-location"></a>Ange plats

Många resurser kräver en plats. Du kan kontrol lera om resursen behöver en plats, antingen via IntelliSense eller [mal len referens](/azure/templates/). I följande exempel läggs en plats parameter till som används för lagrings kontot.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "storageAccountName": {
    "type": "string",
    "minLength": 3,
    "maxLength": 24
  },
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    "name": "[parameters('storageAccountName')]",
    "location": "[parameters('location')]",
    ...
  }
]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string
param location string = resourceGroup().location

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName
  location: location
  ...
}
```

---

Mer information finns i [Ange resurs plats i arm-mallen](resource-location.md).

## <a name="set-tags"></a>Ange Taggar

Du kan använda taggar för en resurs under distributionen. Taggar hjälper dig att logiskt organisera dina distribuerade resurser. Exempel på olika sätt som du kan använda för att ange taggar finns i [taggar för ARM-mallar](../management/tag-resources.md#arm-templates).

## <a name="set-resource-specific-properties"></a>Ange Resource-/regionsspecifika egenskaper

De föregående egenskaperna är generiska för de flesta resurs typer. När du har angett dessa värden måste du ange de egenskaper som är speciella för den resurs typ som du distribuerar.

Använd IntelliSense eller [mal len referens](/azure/templates/) för att avgöra vilka egenskaper som är tillgängliga och vilka som krävs. I följande exempel anges de återstående egenskaperna för ett lagrings konto.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string",
      "minLength": 3,
      "maxLength": 24
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "functions": [],
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[parameters('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "StorageV2",
      "properties": {
        "accessTier": "Hot"
      }
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string
param location string = resourceGroup().location

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName
  location: location
  sku: {
    name: 'Standard_LRS'
    tier: 'Standard'
  }
  kind: 'StorageV2'
  properties: {
    accessTier: 'Hot'
  }
}
```

---

## <a name="next-steps"></a>Nästa steg

* För att villkorligt distribuera en resurs, se [villkorlig distribution i arm-mallar](conditional-resource-deployment.md).
* Information om hur du anger resurs beroenden finns i [definiera ordningen för att distribuera resurser i arm-mallar](define-resource-dependency.md).
