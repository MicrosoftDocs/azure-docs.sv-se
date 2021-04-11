---
title: Definiera flera instanser av en egenskap
description: Använd kopierings åtgärden i en Azure Resource Manager mall (ARM-mall) om du vill iterera flera gånger när du skapar en egenskap för en resurs.
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: 94bc153a49f80694ab9b2d5b04fdf57e8a12e8c8
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2021
ms.locfileid: "106385759"
---
# <a name="property-iteration-in-arm-templates"></a>Egenskaps upprepning i ARM-mallar

Den här artikeln visar hur du skapar fler än en instans av en egenskap i din Azure Resource Manager-mall (ARM-mall). Genom att lägga till en kopierings slinga i avsnittet Egenskaper för en resurs i mallen kan du dynamiskt ange antalet objekt för en egenskap under distributionen. Du behöver inte heller upprepa syntaxen för mallar.

Du kan bara använda Copy-loop med toppnivå resurser, även när du använder kopierings slinga till en egenskap. Mer information om hur du ändrar en underordnad resurs till en resurs på den översta nivån finns i [iteration för en underordnad resurs](copy-resources.md#iteration-for-a-child-resource).

Du kan också använda kopierings slinga med [resurser](copy-resources.md), [variabler](copy-variables.md)och [utdata](copy-outputs.md).

## <a name="syntax"></a>Syntax

# <a name="json"></a>[JSON](#tab/json)

Lägg till `copy` elementet i avsnittet resurser i mallen för att ange antalet objekt för en egenskap. Kopierings elementet har följande allmänna format:

```json
"copy": [
  {
    "name": "<name-of-property>",
    "count": <number-of-iterations>,
    "input": <values-for-the-property>
  }
]
```

För `name` anger du namnet på den resurs egenskap som du vill skapa.

`count`Egenskapen anger antalet iterationer som du vill använda för egenskapen.

`input`Egenskapen anger de egenskaper som du vill upprepa. Du skapar en matris med element som skapats från värdet i `input` egenskapen.

# <a name="bicep"></a>[Bicep](#tab/bicep)

Loopar kan användas för att deklarera flera egenskaper med:

- Iterera över en matris:

  ```bicep
  <property-name>: [for <item> in <collection>: {
    <properties>
  }
  ```

- Iterera över elementen i en matris

  ```bicep
  <property-name>: [for (<item>, <index>) in <collection>: {
    <properties>
  }
  ```

- Använda loop-index

  ```bicep
  <property-name>: [for <index> in range(<start>, <stop>): {
    <properties>
  }
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

## <a name="property-iteration"></a>Egenskaps upprepning

I följande exempel visas hur du använder kopiera loop till `dataDisks` egenskapen på en virtuell dator:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numberOfDataDisks": {
      "type": "int",
      "minValue": 0,
      "maxValue": 16,
      "defaultValue": 3,
      "metadata": {
        "description": "The number of dataDisks to create."
      }
    },
    ...
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2020-06-01",
      ...
      "properties": {
        "storageProfile": {
          ...
          "copy": [
            {
              "name": "dataDisks",
              "count": "[parameters('numberOfDataDisks')]",
              "input": {
                "lun": "[copyIndex('dataDisks')]",
                "createOption": "Empty",
                "diskSizeGB": 1023
              }
            }
          ]
        }
        ...
      }
    }
  ]
}
```

Observera att när `copyIndex` du använder i en egenskap iteration måste du ange namnet på iterationen. Egenskapen iteration stöder också ett offset-argument. Förskjutningen måste komma efter namnet på iterationen, till exempel `copyIndex('dataDisks', 1)` .

Den distribuerade mallen blir:

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2020-06-01",
  "properties": {
    "storageProfile": {
      "dataDisks": [
        {
          "lun": 0,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 2,
          "createOption": "Empty",
          "diskSizeGB": 1023
        }
      ],
      ...
```

Kopierings åtgärden är användbar när du arbetar med matriser eftersom du kan iterera igenom varje element i matrisen. Använd `length` funktionen på matrisen för att ange antalet iterationer och `copyIndex` för att hämta det aktuella indexet i matrisen.

I följande exempel mall skapas en failover-grupp för databaser som skickas in som en matris.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "primaryServerName": {
      "type": "string"
    },
    "secondaryServerName": {
      "type": "string"
    },
    "databaseNames": {
      "type": "array",
      "defaultValue": [
        "mydb1",
        "mydb2",
        "mydb3"
      ]
    }
  },
  "variables": {
    "failoverName": "[concat(parameters('primaryServerName'),'/', parameters('primaryServerName'),'failovergroups')]"
  },
  "resources": [
    {
      "type": "Microsoft.Sql/servers/failoverGroups",
      "apiVersion": "2015-05-01-preview",
      "name": "[variables('failoverName')]",
      "properties": {
        "readWriteEndpoint": {
          "failoverPolicy": "Automatic",
          "failoverWithDataLossGracePeriodMinutes": 60
        },
        "readOnlyEndpoint": {
          "failoverPolicy": "Disabled"
        },
        "partnerServers": [
          {
            "id": "[resourceId('Microsoft.Sql/servers', parameters('secondaryServerName'))]"
          }
        ],
        "copy": [
          {
            "name": "databases",
            "count": "[length(parameters('databaseNames'))]",
            "input": "[resourceId('Microsoft.Sql/servers/databases', parameters('primaryServerName'), parameters('databaseNames')[copyIndex('databases')])]"
          }
        ]
      }
    }
  ],
  "outputs": {
  }
}
```

`copy`Elementet är en matris så att du kan ange mer än en egenskap för resursen.

```json
{
  "type": "Microsoft.Network/loadBalancers",
  "apiVersion": "2017-10-01",
  "name": "exampleLB",
  "properties": {
    "copy": [
      {
        "name": "loadBalancingRules",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      },
      {
        "name": "probes",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      }
    ]
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minValue(0)
@maxValue(16)
@description('The number of dataDisks to be returned in the output array.')
param numberOfDataDisks int = 16

resource vmName 'Microsoft.Compute/virtualMachines@2020-06-01' = {
  ...
  properties: {
    storageProfile: {
      ...
      dataDisks: [for i in range(0, numberOfDataDisks): {
        lun: i
        createOption: 'Empty'
        diskSizeGB: 1023
      }]
    }
    ...
  }
}
```

Den distribuerade mallen blir:

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2020-06-01",
  "properties": {
    "storageProfile": {
      "dataDisks": [
        {
          "lun": 0,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 2,
          "createOption": "Empty",
          "diskSizeGB": 1023
        }
      ],
      ...
```

---

Du kan använda en iteration av resurs och egenskap tillsammans. Referera till egenskapen iteration efter namn.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "type": "Microsoft.Network/virtualNetworks",
  "apiVersion": "2018-04-01",
  "name": "[concat(parameters('vnetname'), copyIndex())]",
  "copy":{
    "count": 2,
    "name": "vnetloop"
  },
  "location": "[resourceGroup().location]",
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "[parameters('addressPrefix')]"
      ]
    },
    "copy": [
      {
        "name": "subnets",
        "count": 2,
        "input": {
          "name": "[concat('subnet-', copyIndex('subnets'))]",
          "properties": {
            "addressPrefix": "[variables('subnetAddressPrefix')[copyIndex('subnets')]]"
          }
        }
      }
    ]
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource vnetname_resource 'Microsoft.Network/virtualNetworks@2018-04-01' = [for i in range(0, 2): {
  name: concat(vnetname, i)
  location: resourceGroup().location
  properties: {
    addressSpace: {
      addressPrefixes: [
        addressPrefix
      ]
    }
    subnets: [for j in range(0, 2): {
      name: 'subnet-${j}'
      properties: {
        addressPrefix: subnetAddressPrefix[j]
      }
    }]
  }
}]
```

---

## <a name="example-templates"></a>Exempel på mallar

I följande exempel visas ett vanligt scenario för att skapa mer än ett värde för en egenskap.

|Mall  |Beskrivning  |
|---------|---------|
|[VM-distribution med ett variabel antal data diskar](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Distribuerar flera data diskar till en virtuell dator. |

## <a name="next-steps"></a>Nästa steg

- Information om hur du går igenom självstudierna finns i [Självstudier: skapa flera resurs instanser med ARM-mallar](template-tutorial-create-multiple-instances.md).
- För annan användning av kopierings slingan, se:
  - [Resurs upprepning i ARM-mallar](copy-resources.md)
  - [Variabel iteration i ARM-mallar](copy-variables.md)
  - [Utdata iteration i ARM-mallar](copy-outputs.md)
- Om du vill lära dig mer om avsnitten i en mall, se [förstå strukturen och syntaxen för ARM-mallar](template-syntax.md).
- Information om hur du distribuerar din mall finns i [distribuera resurser med ARM-mallar och Azure PowerShell](deploy-powershell.md).
