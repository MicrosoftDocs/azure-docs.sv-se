---
title: Analysera nätverks säkerhet med vyn säkerhets grupp – Azure CLI
titleSuffix: Azure Network Watcher
description: Den här artikeln beskriver hur du använder Azure CLI för att analysera säkerheten för virtuella datorer med vyn säkerhets grupp.
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
ms.openlocfilehash: b3aa963c4da7802a9db714f25e7b544b3a132d4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94948654"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-azure-cli"></a>Analysera säkerheten för virtuella datorer med vyn säkerhets grupp med hjälp av Azure CLI

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [Azure CLI](network-watcher-security-group-view-cli.md)
> - [REST-API](network-watcher-security-group-view-rest.md)

> [!NOTE]
> API: t för vyn säkerhets grupp underhålls inte längre och kommer snart att vara inaktuellt. Använd [funktionen gällande säkerhets regler](./network-watcher-security-group-view-overview.md) som ger samma funktioner.


Vyn säkerhets grupp returnerar konfigurerade och effektiva nätverks säkerhets regler som tillämpas på en virtuell dator. Den här funktionen är användbar för att granska och diagnostisera nätverks säkerhets grupper och regler som har kon figurer ATS på en virtuell dator för att säkerställa att trafiken är korrekt tillåten eller nekad. I den här artikeln visar vi hur du hämtar de konfigurerade och effektiva säkerhets reglerna till en virtuell dator med hjälp av Azure CLI

För att utföra stegen i den här artikeln måste du [Installera Azures kommando rads gränssnitt för Mac, Linux och Windows (CLI)](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Innan du börjar

Det här scenariot förutsätter att du redan har följt stegen i [skapa ett Network Watcher](network-watcher-create.md) för att skapa ett Network Watcher.

## <a name="scenario"></a>Scenario

I det scenario som beskrivs i den här artikeln hämtas de konfigurerade och effektiva säkerhets reglerna för en angiven virtuell dator.

## <a name="get-a-vm"></a>Hämta en virtuell dator

En virtuell dator krävs för att köra `vm list` cmdleten. Följande kommando visar de virtuella datorerna i en resurs grupp:

```azurecli
az vm list -resource-group resourceGroupName
```

När du känner till den virtuella datorn kan du använda `vm show` cmdleten för att få sitt resurs-ID:

```azurecli
az vm show -resource-group resourceGroupName -name virtualMachineName
```

## <a name="retrieve-security-group-view"></a>Hämta vyn säkerhets grupp

Nästa steg är att hämta resultatet av säkerhets gruppen.

```azurecli
az network watcher show-security-group-view --resource-group resourceGroupName --vm vmName
```

## <a name="viewing-the-results"></a>Visa resultaten

Följande exempel är ett förkortat svar på resultaten som returneras. Resultaten visar alla effektiva och tillämpade säkerhets regler på den virtuella datorn, uppdelade i grupper av **NetworkInterfaceSecurityRules**, **DefaultSecurityRules** och **EffectiveSecurityRules**.

```json
{
  "networkInterfaces": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
      "resourceGroup": "{resourceGroupName}",
      "securityRuleAssociations": {
        "defaultSecurityRules": [
          {
            "access": "Allow",
            "description": "Allow inbound traffic from all VMs in VNET",
            "destinationAddressPrefix": "VirtualNetwork",
            "destinationPortRange": "*",
            "direction": "Inbound",
            "etag": null,
            "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups//providers/Microsoft.Network/networkSecurityGroups/{nsgName}/defaultSecurityRules/AllowVnetInBound",
            "name": "AllowVnetInBound",
            "priority": 65000,
            "protocol": "*",
            "provisioningState": "Succeeded",
            "resourceGroup": "",
            "sourceAddressPrefix": "VirtualNetwork",
            "sourcePortRange": "*"
          }...
        ],
        "effectiveSecurityRules": [
          {
            "access": "Deny",
            "destinationAddressPrefix": "*",
            "destinationPortRange": "0-65535",
            "direction": "Outbound",
            "expandedDestinationAddressPrefix": null,
            "expandedSourceAddressPrefix": null,
            "name": "DefaultOutboundDenyAll",
            "priority": 65500,
            "protocol": "All",
            "sourceAddressPrefix": "*",
            "sourcePortRange": "0-65535"
          },
          {
            "access": "Allow",
            "destinationAddressPrefix": "VirtualNetwork",
            "destinationPortRange": "0-65535",
            "direction": "Outbound",
            "expandedDestinationAddressPrefix": [
              "10.1.0.0/24",
              "168.63.129.16/32"
            ],
            "expandedSourceAddressPrefix": [
              "10.1.0.0/24",
              "168.63.129.16/32"
            ],
            "name": "DefaultRule_AllowVnetOutBound",
            "priority": 65000,
            "protocol": "All",
            "sourceAddressPrefix": "VirtualNetwork",
            "sourcePortRange": "0-65535"
          },...
        ],
        "networkInterfaceAssociation": {
          "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
          "resourceGroup": "{resourceGroupName}",
          "securityRules": [
            {
              "access": "Allow",
              "description": null,
              "destinationAddressPrefix": "*",
              "destinationPortRange": "3389",
              "direction": "Inbound",
              "etag": "W/\"efb606c1-2d54-475a-ab20-da3f80393577\"",
              "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/securityRules/default-allow-rdp",
              "name": "default-allow-rdp",
              "priority": 1000,
              "protocol": "TCP",
              "provisioningState": "Succeeded",
              "resourceGroup": "{resourceGroupName}",
              "sourceAddressPrefix": "*",
              "sourcePortRange": "*"
            }
          ]
        },
        "subnetAssociation": null
      }
    }
  ]
}
```

## <a name="next-steps"></a>Nästa steg

Besök [granskning av nätverks säkerhets grupper (NSG) med Network Watcher](network-watcher-nsg-auditing-powershell.md) för att lära dig hur du automatiserar validering av nätverks säkerhets grupper.

Läs mer om de säkerhets regler som tillämpas på dina nätverks resurser genom att gå till [Översikt över vyn säkerhets grupp](network-watcher-security-group-view-overview.md)