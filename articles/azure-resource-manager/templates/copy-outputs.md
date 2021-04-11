---
title: Definiera flera instanser av ett utdata-värde
description: Använd kopierings åtgärden i en Azure Resource Manager-mall (ARM-mall) om du vill iterera flera gånger när du returnerar ett värde från en distribution.
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: 49050f4c0a494bbfb470b64704a09d8738a727f7
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2021
ms.locfileid: "106385742"
---
# <a name="output-iteration-in-arm-templates"></a>Utdata iteration i ARM-mallar

Den här artikeln visar hur du skapar fler än ett värde för utdata i din Azure Resource Manager-mall (ARM-mall). Genom att lägga till en kopierings slinga i avsnittet utdata i mallen kan du returnera ett antal objekt dynamiskt under distributionen.

Du kan också använda kopierings slinga med [resurser](copy-resources.md), [Egenskaper i en resurs](copy-properties.md)och [variabler](copy-variables.md).

## <a name="syntax"></a>Syntax

# <a name="json"></a>[JSON](#tab/json)

Lägg till `copy` elementet i utdata-avsnittet i mallen för att returnera ett antal objekt. Kopierings elementet har följande allmänna format:

```json
"copy": {
  "count": <number-of-iterations>,
  "input": <values-for-the-output>
}
```

`count`Egenskapen anger antalet iterationer som du vill använda för utmatning svärdet.

`input`Egenskapen anger de egenskaper som du vill upprepa. Du skapar en matris med element som skapats från värdet i `input` egenskapen. Det kan vara en enskild egenskap (till exempel en sträng) eller ett objekt med flera egenskaper.

# <a name="bicep"></a>[Bicep](#tab/bicep)

Slingor kan användas för att returnera ett antal objekt under distributionen:

- Iterera över en matris:

  ```bicep
  output <output-name> array = [for <item> in <collection>: {
    <properties>
  }]

  ```

- Iterera över elementen i en matris

  ```bicep
  output <output-name> array = [for <item>, <index> in <collection>: {
    <properties>
  }]
  ```

- Använda loop-index

  ```bicep
  output <output-name> array = [for <index> in range(<start>, <stop>): {
    <properties>
  }]
  ```

---

## <a name="copy-limits"></a>Kopierings gränser

Antalet får inte överskrida 800.

Antalet får inte vara ett negativt tal. Det kan vara noll om du distribuerar mallen med en senare version av Azure CLI, PowerShell eller REST API. Mer specifikt måste du använda:

- Azure PowerShell **2,6** eller senare
- Azure CLI- **2.0.74** eller senare
- REST API version **2019-05-10** eller senare
- [Länkade distributioner](linked-templates.md) måste använda API version **2019-05-10** eller senare för distributions resurs typen

Tidigare versioner av PowerShell, CLI och REST API stöder inte noll för Count.

## <a name="outputs-iteration"></a>Upprepningar av utdata

I följande exempel skapas ett variabelt antal lagrings konton och en slut punkt returneras för varje lagrings konto:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageCount": {
      "type": "int",
      "defaultValue": 2
    }
  },
  "variables": {
    "baseName": "[concat('storage', uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(), variables('baseName'))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": "[parameters('storageCount')]"
      }
    }
  ],
  "outputs": {
    "storageEndpoints": {
      "type": "array",
      "copy": {
        "count": "[parameters('storageCount')]",
        "input": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]"
      }
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param storageCount int = 2

var baseName_var = 'storage${uniqueString(resourceGroup().id)}'

resource baseName 'Microsoft.Storage/storageAccounts@2019-04-01' = [for i in range(0, storageCount): {
  name: '${i}${baseName_var}'
  location: resourceGroup().location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}]

output storageEndpoints array = [for i in range(0, storageCount): reference(${i}${baseName_var}).primaryEndpoints.blob]
```

---

Föregående mall returnerar en matris med följande värden:

```json
[
  "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
  "https://1storagecfrbqnnmpeudi.blob.core.windows.net/"
]
```

I nästa exempel returneras tre egenskaper från de nya lagrings kontona.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageCount": {
      "type": "int",
      "defaultValue": 2
    }
  },
  "variables": {
    "baseName": "[concat('storage', uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(), variables('baseName'))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": "[parameters('storageCount')]"
      }
    }
  ],
  "outputs": {
    "storageInfo": {
      "type": "array",
      "copy": {
        "count": "[parameters('storageCount')]",
        "input": {
          "id": "[reference(concat(copyIndex(), variables('baseName')), '2019-04-01', 'Full').resourceId]",
          "blobEndpoint": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]",
          "status": "[reference(concat(copyIndex(), variables('baseName'))).statusOfPrimary]"
        }
      }
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param storageCount int = 2

var baseName_var = 'storage${uniqueString(resourceGroup().id)}'

resource baseName 'Microsoft.Storage/storageAccounts@2019-04-01' = [for i in range(0, storageCount): {
  name: '${i}${baseName_var}'
  location: resourceGroup().location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}]

output storageInfo array = [for i in range(0, storageCount): {
  id: reference(concat(i, baseName_var), '2019-04-01', 'Full').resourceId
  blobEndpoint: reference(concat(i, baseName_var)).primaryEndpoints.blob
  status: reference(concat(i, baseName_var)).statusOfPrimary
}]
```

---

Föregående exempel returnerar en matris med följande värden:

```json
[
  {
    "id": "Microsoft.Storage/storageAccounts/0storagecfrbqnnmpeudi",
    "blobEndpoint": "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
    "status": "available"
  },
  {
    "id": "Microsoft.Storage/storageAccounts/1storagecfrbqnnmpeudi",
    "blobEndpoint": "https://1storagecfrbqnnmpeudi.blob.core.windows.net/",
    "status": "available"
  }
]
```

## <a name="next-steps"></a>Nästa steg

- Information om hur du går igenom självstudierna finns i [Självstudier: skapa flera resurs instanser med ARM-mallar](template-tutorial-create-multiple-instances.md).
- För annan användning av kopierings slingan, se:
  - [Resurs upprepning i ARM-mallar](copy-resources.md)
  - [Egenskaps upprepning i ARM-mallar](copy-properties.md)
  - [Variabel iteration i ARM-mallar](copy-variables.md)
- Om du vill lära dig mer om avsnitten i en mall, se [förstå strukturen och syntaxen för ARM-mallar](template-syntax.md).
- Information om hur du distribuerar din mall finns i [distribuera resurser med ARM-mallar och Azure PowerShell](deploy-powershell.md).
