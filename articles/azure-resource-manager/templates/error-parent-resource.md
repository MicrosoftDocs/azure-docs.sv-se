---
title: Överordnad resurs fel
description: Beskriver hur du löser fel när du arbetar med en överordnad resurs i en Azure Resource Manager-mall.
ms.topic: troubleshooting
ms.date: 08/01/2018
ms.openlocfilehash: 474cb85d16382136e24e5502b87ba8a1a65488ef
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "84673668"
---
# <a name="resolve-errors-for-parent-resources"></a>Lös fel för överordnade resurser

I den här artikeln beskrivs de fel som du kan få när du distribuerar en resurs som är beroende av en överordnad resurs.

## <a name="symptom"></a>Symptom

När du distribuerar en resurs som är underordnad en annan resurs kan du få följande fel meddelande:

```
Code=ParentResourceNotFound;
Message=Can not perform requested operation on nested resource. Parent resource 'exampleserver' not found."
```

## <a name="cause"></a>Orsak

När en resurs är underordnad en annan resurs måste den överordnade resursen finnas innan du skapar den underordnade resursen. Namnet på den underordnade resursen definierar anslutningen till den överordnade resursen. Namnet på den underordnade resursen är i formatet `<parent-resource-name>/<child-resource-name>` . Till exempel kan en SQL Database definieras som:

```json
{
  "type": "Microsoft.Sql/servers/databases",
  "name": "[concat(variables('databaseServerName'), '/', parameters('databaseName'))]",
  ...
```

Om du distribuerar både-servern och-databasen i samma mall, men inte anger ett beroende på servern, kan databas distributionen starta innan servern har distribuerats.

Om den överordnade resursen redan finns och inte har distribuerats i samma mall får du det här felet när resurs hanteraren inte kan associera den underordnade resursen med överordnad. Det här felet kan inträffa om den underordnade resursen inte har rätt format eller om den underordnade resursen distribueras till en annan resurs grupp än resurs gruppen för den överordnade resursen.

## <a name="solution"></a>Lösning

För att lösa det här felet när överordnade och underordnade resurser distribueras i samma mall, ska du inkludera ett beroende.

```json
"dependsOn": [
  "[variables('databaseServerName')]"
]
```

För att lösa det här felet när den överordnade resursen tidigare har distribuerats i en annan mall, anger du inget beroende. Distribuera i stället det underordnade till samma resurs grupp och ange namnet på den överordnade resursen.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "sqlServerName": {
      "type": "string"
    },
    "databaseName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Sql/servers/databases",
      "apiVersion": "2014-04-01",
      "name": "[concat(parameters('sqlServerName'), '/', parameters('databaseName'))]",
      "location": "[resourceGroup().location]",
      "properties": {
        "collation": "SQL_Latin1_General_CP1_CI_AS",
        "edition": "Basic"
      }
    }
  ],
  "outputs": {}
}
```

Mer information finns i [definiera ordningen för att distribuera resurser i Azure Resource Manager mallar](define-resource-dependency.md).