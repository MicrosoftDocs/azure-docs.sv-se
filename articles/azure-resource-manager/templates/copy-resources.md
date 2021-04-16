---
title: Distribuera flera instanser av resurser
description: Använd kopieringsåtgärd och matriser i en Azure Resource Manager mall (ARM-mall) för att distribuera resurstyp många gånger.
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: 5ddb0cabf0acae1ffe9b9e77e6defa70f9cbd61b
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479975"
---
# <a name="resource-iteration-in-arm-templates"></a>Resurs iteration i ARM-mallar

Den här artikeln visar hur du skapar fler än en instans av en resurs i din Azure Resource Manager mall (ARM-mall). Genom att lägga till en kopieringsloop i resursavsnittet i mallen kan du dynamiskt ange antalet resurser som ska distribueras. Du undviker också att behöva upprepa mallsyntaxen.

Du kan också använda en kopieringsloop [med](copy-properties.md)egenskaperna , [variabler](copy-variables.md)och [utdata](copy-outputs.md).

Om du vill ange om en resurs har distribuerats alls kan du se [villkorselementet](conditional-resource-deployment.md).

## <a name="syntax"></a>Syntax

# <a name="json"></a>[JSON](#tab/json)

Lägg till `copy` elementet i resursavsnittet i mallen för att distribuera flera instanser av resursen. Elementet `copy` har följande allmänna format:

```json
"copy": {
  "name": "<name-of-loop>",
  "count": <number-of-iterations>,
  "mode": "serial" <or> "parallel",
  "batchSize": <number-to-deploy-serially>
}
```

Egenskapen `name` är ett värde som identifierar loopen. Egenskapen `count` anger antalet iterationer som du vill använda för resurstypen.

Använd egenskaperna `mode` och för att ange om resurserna `batchSize` distribueras parallellt eller i följd. Dessa egenskaper beskrivs i [Seriell eller Parallell](#serial-or-parallel).

# <a name="bicep"></a>[Bicep](#tab/bicep)

Loopar kan användas för att deklarera flera resurser genom att:

- Iterera över en matris:

  ```bicep
  @batchSize(<number>)
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for <item> in <collection>: {
    <resource-properties>
  }]
  ```

- Iterera över elementen i en matris

  ```bicep
  @batchSize(<number>)
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for (<item>, <index>) in <collection>: {
    <resource-properties>
  }]
  ```

- Använda loopindex

  ```bicep
  @batchSize(<number>)
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for <index> in range(<start>, <stop>): {
    <resource-properties>
  }]
  ```

---

## <a name="copy-limits"></a>Kopieringsgränser

Antalet får inte överstiga 800.

Antalet kan inte vara ett negativt tal. Det kan vara noll om du distribuerar mallen med en ny version av Azure CLI, PowerShell eller REST API. Mer specifikt måste du använda:

- Azure PowerShell **2.6** eller senare
- Azure CLI **2.0.74** eller senare
- REST API version **2019-05-10** eller senare
- [Länkade distributioner](linked-templates.md) måste använda API-version **2019-05-10** eller senare för distributionsresurstypen

Tidigare versioner av PowerShell, CLI och REST API stöder inte noll för antal.

Var försiktig med att [använda distribution i fullständigt läge](deployment-modes.md) med kopieringsloop. Om du distribuerar om med fullständigt läge till en resursgrupp tas alla resurser som inte har angetts i mallen när kopieringsloopen har lösts bort.

## <a name="resource-iteration"></a>Resurs iteration

I följande exempel skapas antalet lagringskonton som anges i `storageCount` parametern .

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
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
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
  ]
}
```

Observera att namnet på varje resurs innehåller funktionen `copyIndex()` , som returnerar den aktuella iterationen i loopen. `copyIndex()` är nollbaserat. Så, i följande exempel:

```json
"name": "[concat('storage', copyIndex())]",
```

Skapar dessa namn:

- storage0
- storage1
- storage2.

Om du vill förskjuta indexvärdet kan du skicka ett värde i `copyIndex()` funktionen . Antalet iterationer anges fortfarande i kopieringselementet, men värdet för `copyIndex` förskjuts av det angivna värdet. Så, i följande exempel:

```json
"name": "[concat('storage', copyIndex(1))]",
```

Skapar dessa namn:

- storage1
- storage2
- storage3

Kopieringsåtgärden är användbar när du arbetar med matriser eftersom du kan iterera genom varje element i matrisen. Använd funktionen `length` i matrisen för att ange antalet iterationer och för `copyIndex` att hämta det aktuella indexet i matrisen.

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param storageCount int = 2

resource storage_id 'Microsoft.Storage/storageAccounts@2019-04-01' = [for i in range(0, storageCount): {
  name: '${i}storage${uniqueString(resourceGroup().id)}'
  location: resourceGroup().location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}]
```

Observera att `i` indexet används för att skapa lagringskontots resursnamn.

---

I följande exempel skapas ett lagringskonto för varje namn som anges i parametern .

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageNames": {
          "type": "array",
          "defaultValue": [
            "contoso",
            "fabrikam",
            "coho"
          ]
      }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(parameters('storageNames')[copyIndex()], uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": "[length(parameters('storageNames'))]"
      }
    }
  ],
  "outputs": {}
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param storageNames array = [
  'contoso'
  'fabrikam'
  'coho'
]

resource storageNames_id 'Microsoft.Storage/storageAccounts@2019-04-01' = [for name in storageNames: {
  name: concat(name, uniqueString(resourceGroup().id))
  location: resourceGroup().location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}]
```

---

Om du vill returnera värden från de distribuerade resurserna kan du använda [kopiera i avsnittet utdata.](copy-outputs.md)

## <a name="serial-or-parallel"></a>Seriell eller parallell

Som standard Resource Manager resurserna parallellt. Det gäller ingen gräns för antalet resurser som distribueras parallellt, förutom den totala gränsen på 800 resurser i mallen. Ordningen som de skapas i garanteras inte.

Men du kanske vill ange att resurserna ska distribueras i följd. När du till exempel uppdaterar en produktionsmiljö kanske du vill sprida uppdateringarna så att bara ett visst nummer uppdateras åt gången.

Om du till exempel vill distribuera lagringskonton i serie två i taget använder du:

# <a name="json"></a>[JSON](#tab/json)

Om du vill distribuera fler än en instans av en resurs seriellt anger du till seriell och till antalet instanser `mode` som ska  `batchSize` distribueras åt gången. Med seriellt Resource Manager skapar ett beroende på tidigare instanser i loopen, så att den inte startar en batch förrän den föregående batchen har slutförts.

Värdet för `batchSize` får inte överskrida värdet för i `count` kopieringselementet.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "copy": {
        "name": "storagecopy",
        "count": 4,
        "mode": "serial",
        "batchSize": 2
      },
      "properties": {}
    }
  ],
  "outputs": {}
}
```

Egenskapen `mode` accepterar även parallell , vilket är standardvärdet.

# <a name="bicep"></a>[Bicep](#tab/bicep)

Om du vill distribuera fler än en instans av en resurs seriellt anger du antalet instanser som ska distribueras i taget för `batchSize` [](./bicep-file.md#resource-and-module-decorators) målaren. Med seriellt Resource Manager skapar ett beroende på tidigare instanser i loopen, så den startar inte en batch förrän den föregående batchen har slutförts.

```bicep
@batchSize(2)
resource storage_id 'Microsoft.Storage/storageAccounts@2019-04-01' = [for i in range(0, 4): {
  name: '${i}storage${uniqueString(resourceGroup().id)}'
  location: resourceGroup().location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}]
```

---

## <a name="iteration-for-a-child-resource"></a>Iteration för en underordnad resurs

Du kan inte använda en kopieringsloop för en underordnad resurs. Om du vill skapa fler än en instans av en resurs som du vanligtvis definierar som kapslad i en annan resurs måste du i stället skapa den resursen som en resurs på den översta nivån. Du definierar relationen med den överordnade resursen via egenskaperna typ och namn.

Anta till exempel att du vanligtvis definierar en datauppsättning som en underordnad resurs i en datafabrik.

```json
"resources": [
{
  "type": "Microsoft.DataFactory/factories",
  "name": "exampleDataFactory",
  ...
  "resources": [
    {
      "type": "datasets",
      "name": "exampleDataSet",
      "dependsOn": [
        "exampleDataFactory"
      ],
      ...
    }
  ]
```

Om du vill skapa fler än en datauppsättning flyttar du den utanför datafabriken. Datamängden måste vara på samma nivå som datafabriken, men den är fortfarande en underordnad resurs för datafabriken. Du bevarar relationen mellan datauppsättningen och datafabriken via egenskaperna typ och namn. Eftersom typen inte längre kan härledas från dess position i mallen måste du ange den fullständigt kvalificerade typen i formatet: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}` .

Om du vill upprätta en överordnad/underordnad relation med en instans av datafabriken anger du ett namn för datauppsättningen som innehåller det överordnade resursnamnet. Använd formatet `{parent-resource-name}/{child-resource-name}`.

I följande exempel visas implementeringen:

# <a name="json"></a>[JSON](#tab/json)

```json
"resources": [
{
  "type": "Microsoft.DataFactory/factories",
  "name": "exampleDataFactory",
  ...
},
{
  "type": "Microsoft.DataFactory/factories/datasets",
  "name": "[concat('exampleDataFactory', '/', 'exampleDataSet', copyIndex())]",
  "dependsOn": [
    "exampleDataFactory"
  ],
  "copy": {
    "name": "datasetcopy",
    "count": "3"
  },
  ...
}]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource dataFactoryName_resource 'Microsoft.DataFactory/factories@2018-06-01' = {
  name: "exampleDataFactory"
  ...
}

resource dataFactoryName_ArmtemplateTestDatasetIn 'Microsoft.DataFactory/factories/datasets@2018-06-01' = [for i in range(0, 3): {
  name: 'exampleDataFactory/exampleDataset${i}'
  ...
}
```

---

## <a name="example-templates"></a>Exempelmallar

I följande exempel visas vanliga scenarier för att skapa fler än en instans av en resurs eller egenskap.

|Mall  |Beskrivning  |
|---------|---------|
|[Kopiera lagring](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) |Distribuerar fler än ett lagringskonto med ett indexnummer i namnet. |
|[Seriell kopieringslagring](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) |Distribuerar flera lagringskonton en i taget. Namnet innehåller indexnumret. |
|[Kopiera lagring med matris](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) |Distribuerar flera lagringskonton. Namnet innehåller ett värde från en matris. |

## <a name="next-steps"></a>Nästa steg

- Information om hur du anger beroenden för resurser som skapas i en kopieringsloop finns i Definiera ordningen för [distribution av resurser i ARM-mallar.](define-resource-dependency.md)
- Om du vill gå igenom en självstudie kan [du gå igenom Självstudie: Skapa flera resursinstanser med ARM-mallar.](template-tutorial-create-multiple-instances.md)
- En modul Microsoft Learn omfattar resurskopiering finns i [Hantera komplexa molndistributioner med hjälp av avancerade ARM-mallfunktioner.](/learn/modules/manage-deployments-advanced-arm-template-features/)
- För andra användningsområden för kopieringsloopen, se:
  - [Egenskaps iteration i ARM-mallar](copy-properties.md)
  - [Variabel iteration i ARM-mallar](copy-variables.md)
  - [Iteration av utdata i ARM-mallar](copy-outputs.md)
- Information om hur du använder kopiera med kapslade mallar finns i [Använda kopiera](linked-templates.md#using-copy).
