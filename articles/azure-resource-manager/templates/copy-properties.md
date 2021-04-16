---
title: Definiera flera instanser av en egenskap
description: Använd kopieringsåtgärden i en Azure Resource Manager mall (ARM-mall) för att iterera flera gånger när du skapar en egenskap på en resurs.
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: 16c293f1c3aff64aeb8b6cae4b7f1aa14dcd0a77
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107480009"
---
# <a name="property-iteration-in-arm-templates"></a>Egenskaps iteration i ARM-mallar

Den här artikeln visar hur du skapar fler än en instans av en egenskap i din Azure Resource Manager mall (ARM-mall). Genom att lägga till en kopieringsloop i egenskapsavsnittet för en resurs i mallen kan du dynamiskt ange antalet objekt för en egenskap under distributionen. Du undviker också att behöva upprepa mallsyntaxen.

Du kan bara använda en kopieringsloop med resurser på den översta nivån, även när du tillämpar en kopieringsloop på en egenskap. Mer information om hur du ändrar en underordnad resurs till en resurs på den översta nivån finns [i Iteration for a child resource (Iteration för en underordnad resurs).](copy-resources.md#iteration-for-a-child-resource)

Du kan också använda en kopieringsloop [med](copy-resources.md)resurser, [variabler](copy-variables.md) [och utdata.](copy-outputs.md)

## <a name="syntax"></a>Syntax

# <a name="json"></a>[JSON](#tab/json)

Lägg till `copy` elementet i resursavsnittet i mallen för att ange antalet objekt för en egenskap. Kopieringselementet har följande allmänna format:

```json
"copy": [
  {
    "name": "<name-of-property>",
    "count": <number-of-iterations>,
    "input": <values-for-the-property>
  }
]
```

För `name` anger du namnet på den resursegenskap som du vill skapa.

Egenskapen `count` anger antalet iterationer som du vill använda för egenskapen .

Egenskapen `input` anger de egenskaper som du vill upprepa. Du skapar en matris med element som skapas från värdet i `input` egenskapen .

# <a name="bicep"></a>[Bicep](#tab/bicep)

Loopar kan användas för att deklarera flera egenskaper genom att:

- Iterera över en matris:

  ```bicep
  <property-name>: [for <item> in <collection>: {
    <properties>
  }]
  ```

- Iterera över elementen i en matris

  ```bicep
  <property-name>: [for (<item>, <index>) in <collection>: {
    <properties>
  }]
  ```

- Använda loopindex

  ```bicep
  <property-name>: [for <index> in range(<start>, <stop>): {
    <properties>
  }]
  ```

---

## <a name="copy-limits"></a>Kopieringsgränser

Antalet får inte överstiga 800.

Antalet får inte vara ett negativt tal. Det kan vara noll om du distribuerar mallen med en ny version av Azure CLI, PowerShell eller REST API. Mer specifikt måste du använda:

- Azure PowerShell **2.6** eller senare
- Azure CLI **2.0.74** eller senare
- REST API version **2019-05-10** eller senare
- [Länkade distributioner](linked-templates.md) måste använda API-version **2019-05-10** eller senare för distributionsresurstypen

Tidigare versioner av PowerShell, CLI och REST API stöder inte noll för antal.

## <a name="property-iteration"></a>Egenskaps iteration

I följande exempel visas hur du tillämpar en kopieringsloop på `dataDisks` egenskapen på en virtuell dator:

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

Observera att när du `copyIndex` använder inuti en egenskaps iteration måste du ange namnet på iterationen. Egenskaps iteration stöder också ett offset-argument. Förskjutningen måste komma efter namnet på iterationen, till exempel `copyIndex('dataDisks', 1)` .

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

Kopieringsåtgärden är användbar när du arbetar med matriser eftersom du kan iterera genom varje element i matrisen. Använd funktionen `length` i matrisen för att ange antalet iterationer och för `copyIndex` att hämta det aktuella indexet i matrisen.

I följande exempelmall skapas en redundansgrupp för databaser som skickas som en matris.

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

Elementet `copy` är en matris så du kan ange fler än en egenskap för resursen.

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

Du kan använda resurs- och egenskaps iteration tillsammans. Referera till egenskapens iteration efter namn.

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

## <a name="example-templates"></a>Exempelmallar

I följande exempel visas ett vanligt scenario för att skapa fler än ett värde för en egenskap.

|Mall  |Beskrivning  |
|---------|---------|
|[VM-distribution med ett variabelt antal datadiskar](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Distribuerar flera datadiskar med en virtuell dator. |

## <a name="next-steps"></a>Nästa steg

- Om du vill gå igenom en självstudie [kan du gå igenom Självstudie: Skapa flera resursinstanser med ARM-mallar.](template-tutorial-create-multiple-instances.md)
- För andra användningsområden för kopieringsloopen, se:
  - [Resurs iteration i ARM-mallar](copy-resources.md)
  - [Variabel iteration i ARM-mallar](copy-variables.md)
  - [Iteration av utdata i ARM-mallar](copy-outputs.md)
- Om du vill lära dig mer om avsnitten i en mall kan du [läsa Förstå strukturen och syntaxen för ARM-mallar.](template-syntax.md)
- Information om hur du distribuerar mallen finns i [Distribuera resurser med ARM-mallar och Azure PowerShell](deploy-powershell.md).
