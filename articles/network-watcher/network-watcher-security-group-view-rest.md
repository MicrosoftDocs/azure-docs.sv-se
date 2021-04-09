---
title: Analysera nätverks säkerhet – vyn säkerhets grupp – Azure REST API
titleSuffix: Azure Network Watcher
description: Den här artikeln beskriver hur du kan analysera en virtuell dators säkerhet med vyn säkerhets grupp i Azure REST API.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 2efd3e9c9ca97ea3d94b03bd5e440cd24d5da5da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94960629"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-rest-api"></a>Analysera säkerheten för virtuella datorer med vyn säkerhets grupp med REST API

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [Azure CLI](network-watcher-security-group-view-cli.md)
> - [REST-API](network-watcher-security-group-view-rest.md)

> [!NOTE]
> API: t för vyn säkerhets grupp underhålls inte längre och kommer snart att vara inaktuellt. Använd [funktionen gällande säkerhets regler](./network-watcher-security-group-view-overview.md) som ger samma funktioner. 

Vyn säkerhets grupp returnerar konfigurerade och effektiva nätverks säkerhets regler som tillämpas på en virtuell dator. Den här funktionen är användbar för att granska och diagnostisera nätverks säkerhets grupper och regler som har kon figurer ATS på en virtuell dator för att säkerställa att trafiken är korrekt tillåten eller nekad. I den här artikeln visar vi hur du hämtar effektiva och tillämpade säkerhets regler på en virtuell dator med hjälp av REST API


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Innan du börjar

I det här scenariot anropar du Network Watcher REST API för att hämta vyn säkerhets grupp för en virtuell dator. ARMclient används för att anropa REST API med hjälp av PowerShell. ARMClient finns på choklad på [ARMClient](https://chocolatey.org/packages/ARMClient)

Det här scenariot förutsätter att du redan har följt stegen i [skapa ett Network Watcher](network-watcher-create.md) för att skapa ett Network Watcher. Scenariot förutsätter också att det finns en resurs grupp med en giltig virtuell dator som kan användas.

## <a name="scenario"></a>Scenario

I det scenario som beskrivs i den här artikeln hämtas effektiva och tillämpade säkerhets regler för en specifik virtuell dator.

## <a name="log-in-with-armclient"></a>Logga in med ARMClient

```powershell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>Hämta en virtuell dator

Kör följande skript för att returnera en virtuell machineThe följande kod behöver variabler:

- **subscriptionId** -prenumerations-ID kan också hämtas med cmdleten **Get-AzSubscription** .
- **resourceGroupName** – namnet på en resurs grupp som innehåller virtuella datorer.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = '<resource group name>'

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

Den information som behövs är **ID** under typen som `Microsoft.Compute/virtualMachines` svar, som visas i följande exempel:

```json
...,
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft
.Network/networkInterfaces/{nicName}"
            }
          ]
        },
        "provisioningState": "Succeeded"
      },
      "resources": [
        {
          "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Com
pute/virtualMachines/{vmName}/extensions/CustomScriptExtension"
        }
      ],
      "type": "Microsoft.Compute/virtualMachines",
      "location": "westcentralus",
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute
/virtualMachines/{vmName}",
      "name": "{vmName}"
    }
  ]
}
```

## <a name="get-security-group-view-for-virtual-machine"></a>Hämta vyn säkerhets grupp för virtuell dator

I följande exempel begärs vyn säkerhets grupp för en riktad virtuell dator. Resultatet från det här exemplet kan användas för att jämföra med de regler och den säkerhet som definierats av ursprunget för att söka efter konfigurations avvikelser.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>"
$networkWatcherName = "<network watcher name>"
$targetUri = "<uri of target resource>" # Example: /subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.compute/virtualMachine/$vmName

$requestBody = @"
{
    'targetResourceId': '${targetUri}'

}
"@
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/securityGroupView?api-version=2016-12-01" $requestBody -verbose
```

## <a name="view-the-response"></a>Visa svaret

Följande exempel är svaret som returnerades från föregående kommando. Resultaten visar alla effektiva och tillämpade säkerhets regler på den virtuella datorn, uppdelade i grupper av **NetworkInterfaceSecurityRules**, **DefaultSecurityRules** och **EffectiveSecurityRules**.

```json

{
  "networkInterfaces": [
    {
      "securityRuleAssociations": {
        "networkInterfaceAssociation": {
          "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
          "securityRules": [
            {
              "name": "default-allow-rdp",
              "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/securityRules/default-allow-rdp",
              "etag": "W/\"d4c411d4-0d62-49dc-8092-3d4b57825740\"",
              "properties": {
                "provisioningState": "Succeeded",
                "protocol": "TCP",
                "sourcePortRange": "*",
                "destinationPortRange": "3389",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 1000,
                "direction": "Inbound"
              }
            }
          ]
        },
        "defaultSecurityRules": [
          {
            "name": "AllowVnetInBound",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/defaultSecurityRules/",
            "properties": {
              "provisioningState": "Succeeded",
              "description": "Allow inbound traffic from all VMs in VNET",
              "protocol": "*",
              "sourcePortRange": "*",
              "destinationPortRange": "*",
              "sourceAddressPrefix": "VirtualNetwork",
              "destinationAddressPrefix": "VirtualNetwork",
              "access": "Allow",
              "priority": 65000,
              "direction": "Inbound"
            }
          },
          ...
        ],
        "effectiveSecurityRules": [
          {
            "name": "DefaultOutboundDenyAll",
            "protocol": "All",
            "sourcePortRange": "0-65535",
            "destinationPortRange": "0-65535",
            "sourceAddressPrefix": "*",
            "destinationAddressPrefix": "*",
            "access": "Deny",
            "priority": 65500,
            "direction": "Outbound"
          },
          ...
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>Nästa steg

Besök [granskning av nätverks säkerhets grupper (NSG) med Network Watcher](network-watcher-security-group-view-powershell.md) för att lära dig hur du automatiserar validering av nätverks säkerhets grupper.