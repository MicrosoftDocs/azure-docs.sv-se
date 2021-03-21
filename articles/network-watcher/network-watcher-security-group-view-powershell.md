---
title: Analysera nätverks säkerhet – vy över säkerhets grupp – Azure PowerShell
titleSuffix: Azure Network Watcher
description: Den här artikeln beskriver hur du använder PowerShell för att analysera säkerheten för virtuella datorer med vyn säkerhets grupp.
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
ms.openlocfilehash: 2da4ebfa6a9ce64b03e6e1d29956f740f6cb3c1b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94960656"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-powershell"></a>Analysera säkerheten för virtuella datorer med vyn säkerhets grupp med hjälp av PowerShell

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [Azure CLI](network-watcher-security-group-view-cli.md)
> - [REST-API](network-watcher-security-group-view-rest.md)

> [!NOTE]
> API: t för vyn säkerhets grupp underhålls inte längre och kommer snart att vara inaktuellt. Använd [funktionen gällande säkerhets regler](./network-watcher-security-group-view-overview.md) som ger samma funktioner. 

Vyn säkerhets grupp returnerar konfigurerade och effektiva nätverks säkerhets regler som tillämpas på en virtuell dator. Den här funktionen är användbar för att granska och diagnostisera nätverks säkerhets grupper och regler som har kon figurer ATS på en virtuell dator för att säkerställa att trafiken är korrekt tillåten eller nekad. I den här artikeln visar vi hur du hämtar de konfigurerade och effektiva säkerhets reglerna till en virtuell dator med hjälp av PowerShell


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Innan du börjar

I det här scenariot kör du `Get-AzNetworkWatcherSecurityGroupView` cmdlet: en för att hämta säkerhets regel informationen.

Det här scenariot förutsätter att du redan har följt stegen i [skapa ett Network Watcher](network-watcher-create.md) för att skapa ett Network Watcher.

## <a name="scenario"></a>Scenario

I det scenario som beskrivs i den här artikeln hämtas de konfigurerade och effektiva säkerhets reglerna för en angiven virtuell dator.

## <a name="retrieve-network-watcher"></a>Hämta Network Watcher

Det första steget är att hämta Network Watcher-instansen. Den här variabeln skickas till `Get-AzNetworkWatcherSecurityGroupView` cmdleten.

```powershell
$networkWatcher = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" }
```

## <a name="get-a-vm"></a>Hämta en virtuell dator

En virtuell dator krävs för att köra `Get-AzNetworkWatcherSecurityGroupView` cmdleten mot. I följande exempel hämtas ett VM-objekt.

```powershell
$VM = Get-AzVM -ResourceGroupName testrg -Name testvm1
```

## <a name="retrieve-security-group-view"></a>Hämta vyn säkerhets grupp

Nästa steg är att hämta resultatet av säkerhets gruppen.

```powershell
$secgroup = Get-AzNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="viewing-the-results"></a>Visa resultaten

Följande exempel är ett förkortat svar på resultaten som returneras. Resultaten visar alla effektiva och tillämpade säkerhets regler på den virtuella datorn, uppdelade i grupper av **NetworkInterfaceSecurityRules**, **DefaultSecurityRules** och **EffectiveSecurityRules**.

```
NetworkInterfaces : [
                      {
                        "NetworkInterfaceSecurityRules": [
                          {
                            "Name": "default-allow-rdp",
                            "Etag": "W/\"d4c411d4-0d62-49dc-8092-3d4b57825740\"",
                            "Id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg2/providers/Microsoft.Network/networkSecurityGroups/testvm2-nsg/securityRules/default-allow-rdp",
                            "Protocol": "TCP",
                            "SourcePortRange": "*",
                            "DestinationPortRange": "3389",
                            "SourceAddressPrefix": "*",
                            "DestinationAddressPrefix": "*",
                            "Access": "Allow",
                            "Priority": 1000,
                            "Direction": "Inbound",
                            "ProvisioningState": "Succeeded"
                          }
                          ...
                        ],
                        "DefaultSecurityRules": [
                          {
                            "Name": "AllowVnetInBound",
                            "Id": "/subscriptions00000000-0000-0000-0000-000000000000/resourceGroups/testrg2/providers/Microsoft.Network/networkSecurityGroups/testvm2-nsg/defaultSecurityRules/",
                            "Description": "Allow inbound traffic from all VMs in VNET",
                            "Protocol": "*",
                            "SourcePortRange": "*",
                            "DestinationPortRange": "*",
                            "SourceAddressPrefix": "VirtualNetwork",
                            "DestinationAddressPrefix": "VirtualNetwork",
                            "Access": "Allow",
                            "Priority": 65000,
                            "Direction": "Inbound",
                            "ProvisioningState": "Succeeded"
                          }
                          ...
                        ],
                        "EffectiveSecurityRules": [
                          {
                            "Name": "DefaultOutboundDenyAll",
                            "Protocol": "All",
                            "SourcePortRange": "0-65535",
                            "DestinationPortRange": "0-65535",
                            "SourceAddressPrefix": "*",
                            "DestinationAddressPrefix": "*",
                            "ExpandedSourceAddressPrefix": [],
                            "ExpandedDestinationAddressPrefix": [],
                            "Access": "Deny",
                            "Priority": 65500,
                            "Direction": "Outbound"
                          },
                          ...
                        ]
                      }
                    ]
```

## <a name="next-steps"></a>Nästa steg

Besök [granskning av nätverks säkerhets grupper (NSG) med Network Watcher](network-watcher-nsg-auditing-powershell.md) för att lära dig hur du automatiserar validering av nätverks säkerhets grupper.