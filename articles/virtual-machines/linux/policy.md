---
title: Framtvinga säkerhet med principer på virtuella Linux-datorer i Azure
description: Så här tillämpar du en princip på en virtuell Azure Resource Manager Linux-dator
author: mimckitt
ms.service: virtual-machines
ms.collection: linux
ms.subservice: security
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 08/02/2017
ms.author: mimckitt
ms.openlocfilehash: c1fb80001f0669672e9f6e8a567685b799314df1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102549829"
---
# <a name="apply-policies-to-linux-vms-with-azure-resource-manager"></a>Tillämpa principer för virtuella Linux-datorer med Azure Resource Manager
Med hjälp av principer kan en organisation tillämpa olika konventioner och regler i hela företaget. Verk ställandet av det önskade beteendet kan hjälpa till att minska risken och bidra till organisationens framgång. I den här artikeln beskriver vi hur du kan använda Azure Resource Manager principer för att definiera det önskade beteendet för organisationens Virtual Machines.

För en introduktion till principer, se [Vad är Azure policy?](../../governance/policy/overview.md).

## <a name="permitted-virtual-machines"></a>Tillåten Virtual Machines
För att säkerställa att virtuella datorer för din organisation är kompatibla med ett program kan du begränsa tillåtna operativ system. I följande princip exempel tillåter du att endast Ubuntu 14.04.2-LTS Virtual Machines skapas.

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "in": [
          "Microsoft.Compute/disks",
          "Microsoft.Compute/virtualMachines",
          "Microsoft.Compute/VirtualMachineScaleSets"
        ]
      },
      {
        "not": {
          "allOf": [
            {
              "field": "Microsoft.Compute/imagePublisher",
              "in": [
                "Canonical"
              ]
            },
            {
              "field": "Microsoft.Compute/imageOffer",
              "in": [
                "UbuntuServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageSku",
              "in": [
                "14.04.2-LTS"
              ]
            },
            {
              "field": "Microsoft.Compute/imageVersion",
              "in": [
                "latest"
              ]
            }
          ]
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

Använd ett jokertecken för att ändra den tidigare principen för att tillåta valfri Ubuntu LTS-avbildning: 

```json
{
  "field": "Microsoft.Compute/virtualMachines/imageSku",
  "like": "*LTS"
}
```

Information om princip fält finns i [princip-alias](../../governance/policy/concepts/definition-structure.md#aliases).

## <a name="managed-disks"></a>Hanterade diskar

Om du vill kräva användning av hanterade diskar använder du följande princip:

```json
{
  "if": {
    "anyOf": [
      {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/virtualMachines"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/osDisk.uri",
            "exists": true
          }
        ]
      },
      {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/VirtualMachineScaleSets"
          },
          {
            "anyOf": [
              {
                "field": "Microsoft.Compute/VirtualMachineScaleSets/osDisk.vhdContainers",
                "exists": true
              },
              {
                "field": "Microsoft.Compute/VirtualMachineScaleSets/osdisk.imageUrl",
                "exists": true
              }
            ]
          }
        ]
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="images-for-virtual-machines"></a>Avbildningar för Virtual Machines

Av säkerhets skäl kan du kräva att endast godkända anpassade avbildningar distribueras i din miljö. Du kan ange antingen resurs gruppen som innehåller de godkända avbildningarna eller de angivna godkända avbildningarna.

Följande exempel kräver bilder från en godkänd resurs grupp:

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "in": [
                    "Microsoft.Compute/virtualMachines",
                    "Microsoft.Compute/VirtualMachineScaleSets"
                ]
            },
            {
                "not": {
                    "field": "Microsoft.Compute/imageId",
                    "contains": "resourceGroups/CustomImage"
                }
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
} 
```

I följande exempel anges godkända avbildnings-ID: n:

```json
{
    "field": "Microsoft.Compute/imageId",
    "in": ["{imageId1}","{imageId2}"]
}
```

## <a name="virtual-machine-extensions"></a>Tillägg för virtuell dator

Du kanske vill förbjuda användning av vissa typer av tillägg. Till exempel kanske ett tillägg inte är kompatibelt med vissa anpassade avbildningar av virtuella datorer. I följande exempel visas hur du blockerar ett speciellt tillägg. Den använder utgivare och typ för att avgöra vilket tillägg som ska blockeras.

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Compute/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                "equals": "Microsoft.Compute"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/type",
                "equals": "{extension-type}"

      }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```


## <a name="next-steps"></a>Nästa steg
* När du har definierat en princip regel (som visas i föregående exempel), måste du skapa princip definitionen och tilldela den till ett omfång. Omfånget kan vara en prenumeration, en resurs grupp eller en resurs. Om du vill tilldela principer, se [använda Azure Portal för att tilldela och hantera resurs principer](../../governance/policy/assign-policy-portal.md), [använda PowerShell för att tilldela principer](../../governance/policy/assign-policy-powershell.md)eller [använda Azure CLI för att tilldela principer](../../governance/policy/assign-policy-azurecli.md).
* En introduktion till resurs principer finns i [Vad är Azure policy?](../../governance/policy/overview.md).
* Vägledning för hur företag kan använda resurshanteraren för att effektivt hantera prenumerationer finns i [Azure enterprise scaffold - förebyggande prenumerationsåtgärder](/azure/architecture/cloud-adoption-guide/subscription-governance).
