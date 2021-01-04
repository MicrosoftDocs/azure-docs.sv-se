---
title: Underordnade resurser i mallar
description: Beskriver hur du anger namn och typ för underordnade resurser i en Azure Resource Manager mall.
ms.topic: conceptual
ms.date: 12/21/2020
ms.openlocfilehash: c594096fd95f663db2120b29c575b341924dcc36
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/22/2020
ms.locfileid: "97721951"
---
# <a name="set-name-and-type-for-child-resources"></a>Ange namn och typ för underordnade resurser

Underordnade resurser är resurser som bara finns inom kontexten för en annan resurs. Det kan till exempel finnas ett [tillägg för virtuell dator](/azure/templates/microsoft.compute/virtualmachines/extensions) utan en [virtuell dator](/azure/templates/microsoft.compute/virtualmachines). Tilläggs resursen är en underordnad till den virtuella datorn.

Varje överordnad resurs accepterar bara vissa resurs typer som underordnade resurser. Resurs typen för den underordnade resursen innehåller resurs typen för den överordnade resursen. Till exempel är **Microsoft. Web/Sites/config** och **Microsoft. Web/Sites/Extensions** båda underordnade resurser för **Microsoft. Web/Sites**. Godkända resurs typer anges i det [mall schema](https://github.com/Azure/azure-resource-manager-schemas) som tillhör den överordnade resursen.

I en Azure Resource Manager-mall (ARM-mall) kan du ange den underordnade resursen antingen i den överordnade resursen eller utanför den överordnade resursen. I följande exempel visas den underordnade resurs som ingår i egenskapen Resources för den överordnade resursen.

```json
"resources": [
  {
    <parent-resource>
    "resources": [
      <child-resource>
    ]
  }
]
```

Underordnade resurser kan bara definieras fem nivåer djup.

I nästa exempel visas den underordnade resursen utanför den överordnade resursen. Du kan använda den här metoden om den överordnade resursen inte har distribuerats i samma mall, eller om du vill använda [Kopiera](copy-resources.md) för att skapa fler än en underordnad resurs.

```json
"resources": [
  {
    <parent-resource>
  },
  {
    <child-resource>
  }
]
```

Värdena som du anger för resurs namn och typ varierar beroende på om den underordnade resursen definieras i eller utanför den överordnade resursen.

## <a name="within-parent-resource"></a>I överordnad resurs

När du har definierat i den överordnade resurs typen formaterar du typ-och namn värden som ett enda ord utan snedstreck.

```json
"type": "{child-resource-type}",
"name": "{child-resource-name}",
```

I följande exempel visas ett virtuellt nätverk och ett undernät. Observera att under nätet ingår i Resources-matrisen för det virtuella nätverket. Namnet anges till **Subnet1** och typen anges till **undernät**. Den underordnade resursen är markerad som beroende av den överordnade resursen eftersom den överordnade resursen måste finnas innan den underordnade resursen kan distribueras.

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2018-10-01",
    "name": "VNet1",
    "location": "[parameters('location')]",
    "properties": {
      "addressSpace": {
        "addressPrefixes": [
          "10.0.0.0/16"
        ]
      }
    },
    "resources": [
      {
        "type": "subnets",
        "apiVersion": "2018-10-01",
        "name": "Subnet1",
        "location": "[parameters('location')]",
        "dependsOn": [
          "VNet1"
        ],
        "properties": {
          "addressPrefix": "10.0.0.0/24"
        }
      }
    ]
  }
]
```

Den fullständiga resurs typen är fortfarande **Microsoft. Network/virtualNetworks/subnets**. Du tillhandahåller inte **Microsoft. Network/virtualNetworks/** eftersom det antas från den överordnade resurs typen.

Det underordnade resurs namnet är inställt på **Subnet1** men det fullständiga namnet innehåller det överordnade namnet. Du kan inte ange **VNet1** eftersom den antas från den överordnade resursen.

## <a name="outside-parent-resource"></a>Utanför överordnad resurs

När du definierar utanför den överordnade resursen formaterar du typ och med snedstreck för att inkludera den överordnade typen och namnet.

```json
"type": "{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}",
"name": "{parent-resource-name}/{child-resource-name}",
```

I följande exempel visas ett virtuellt nätverk och undernät som båda definieras på rotnivå. Observera att under nätet inte ingår i Resources-matrisen för det virtuella nätverket. Namnet är inställt på **VNet1/Subnet1** och typen anges till **Microsoft. Network/virtualNetworks/subnets**. Den underordnade resursen är markerad som beroende av den överordnade resursen eftersom den överordnade resursen måste finnas innan den underordnade resursen kan distribueras.

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2018-10-01",
    "name": "VNet1",
    "location": "[parameters('location')]",
    "properties": {
      "addressSpace": {
        "addressPrefixes": [
          "10.0.0.0/16"
        ]
      }
    }
  },
  {
    "type": "Microsoft.Network/virtualNetworks/subnets",
    "apiVersion": "2018-10-01",
    "location": "[parameters('location')]",
    "name": "VNet1/Subnet1",
    "dependsOn": [
      "VNet1"
    ],
    "properties": {
      "addressPrefix": "10.0.0.0/24"
    }
  }
]
```

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du skapar ARM-mallar finns i [Redigera mallar](template-syntax.md).

* Mer information om formatet på resurs namnet när du refererar till resursen finns i [referens funktionen](template-functions-resource.md#reference).
