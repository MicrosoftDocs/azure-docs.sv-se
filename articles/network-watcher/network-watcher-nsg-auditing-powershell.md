---
title: Automatisera NSG granskning – vyn säkerhets grupp
titleSuffix: Azure Network Watcher
description: Den här sidan innehåller instruktioner för hur du konfigurerar granskning av en nätverks säkerhets grupp
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
ms.openlocfilehash: 177215775c9e83286aa98872eed0ab211a8f36ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94948756"
---
# <a name="automate-nsg-auditing-with-azure-network-watcher-security-group-view"></a>Automatisera NSG granskning med vyn Azure Network Watcher säkerhets grupp

Kunder är ofta inriktade mot utmaningen med att verifiera säkerhets position i sin infrastruktur. Den här utmaningen skiljer sig inte från de virtuella datorerna i Azure. Det är viktigt att ha en liknande säkerhets profil baserat på reglerna för nätverks säkerhets gruppen (NSG) som tillämpas. Med hjälp av vyn säkerhets grupp kan du nu hämta en lista över regler som tillämpas på en virtuell dator i en NSG. Du kan definiera en gyllene NSG säkerhets profil och initiera vyn säkerhets grupp på en veckovis takt och jämföra utdata med den gyllene profilen och skapa en rapport. På så sätt kan du enkelt identifiera alla virtuella datorer som inte överensstämmer med den angivna säkerhets profilen.

Om du inte är bekant med nätverks säkerhets grupper kan du läsa [Översikt över nätverks säkerhet](../virtual-network/network-security-groups-overview.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Innan du börjar

I det här scenariot jämför du en känd, fungerande bas linje för de resultat som returneras för en virtuell dator i säkerhets gruppen.

Det här scenariot förutsätter att du redan har följt stegen i [skapa ett Network Watcher](network-watcher-create.md) för att skapa ett Network Watcher. Scenariot förutsätter också att det finns en resurs grupp med en giltig virtuell dator som kan användas.

## <a name="scenario"></a>Scenario

I det scenario som beskrivs i den här artikeln hämtas vyn säkerhets grupp för en virtuell dator.

I det här scenariot kommer du att:

- Hämta en fungerande regel uppsättning
- Hämta en virtuell dator med REST API
- Hämta vyn säkerhets grupp för virtuell dator
- Utvärdera svar

## <a name="retrieve-rule-set"></a>Hämta regel uppsättning

Det första steget i det här exemplet är att arbeta med en befintlig bas linje. I följande exempel är en JSON-extraherad från en befintlig nätverks säkerhets grupp med hjälp av den `Get-AzNetworkSecurityGroup` cmdlet som används som bas linje för det här exemplet.

```json
[
    {
        "Description":  null,
        "Protocol":  "TCP",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "3389",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1000,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "default-allow-rdp",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/default-allow-rdp"
    },
    {
        "Description":  null,
        "Protocol":  "*",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "111",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1010,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "MyRuleDoNotDelete",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/MyRuleDoNotDelete"
    },
    {
        "Description":  null,
        "Protocol":  "*",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "112",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1020,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "My2ndRuleDoNotDelete",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/My2ndRuleDoNotDelete"
    },
    {
        "Description":  null,
        "Protocol":  "TCP",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "5672",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Deny",
        "Priority":  1030,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "ThisRuleNeedsToStay",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/ThisRuleNeedsToStay"
    }
]
```

## <a name="convert-rule-set-to-powershell-objects"></a>Konvertera regel uppsättning till PowerShell-objekt

I det här steget läser vi en JSON-fil som skapades tidigare med de regler som förväntas finnas i nätverks säkerhets gruppen för det här exemplet.

```powershell
$nsgbaserules = Get-Content -Path C:\temp\testvm1-nsg.json | ConvertFrom-Json
```

## <a name="retrieve-network-watcher"></a>Hämta Network Watcher

Nästa steg är att hämta Network Watcher-instansen. `$networkWatcher`Variabeln skickas till `AzNetworkWatcherSecurityGroupView` cmdleten.

```powershell
$networkWatcher = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
```

## <a name="get-a-vm"></a>Hämta en virtuell dator

En virtuell dator krävs för att köra `Get-AzNetworkWatcherSecurityGroupView` cmdleten mot. I följande exempel hämtas ett VM-objekt.

```powershell
$VM = Get-AzVM -ResourceGroupName "testrg" -Name "testvm1"
```

## <a name="retrieve-security-group-view"></a>Hämta vyn säkerhets grupp

Nästa steg är att hämta resultatet av säkerhets gruppen. Resultatet jämförs med "bas linje" JSON som visades tidigare.

```powershell
$secgroup = Get-AzNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="analyzing-the-results"></a>Analysera resultaten

Svaret grupperas efter nätverks gränssnitt. De olika typerna av regler som returneras är effektiva och standard säkerhets regler. Resultatet delas upp ytterligare av hur det tillämpas, antingen på ett undernät eller ett virtuellt nätverkskort.

Följande PowerShell-skript jämför resultatet av vyn säkerhets grupp med en befintlig utmatning av en NSG. I följande exempel visas ett enkelt exempel på hur resultatet kan jämföras med `Compare-Object` cmdlet.

```powershell
Compare-Object -ReferenceObject $nsgbaserules `
-DifferenceObject $secgroup.NetworkInterfaces[0].NetworkInterfaceSecurityRules `
-Property Name,Description,Protocol,SourcePortRange,DestinationPortRange,SourceAddressPrefix,DestinationAddressPrefix,Access,Priority,Direction
```

Följande exempel är resultatet. Du kan se två av reglerna som fanns i den första regel uppsättningen fanns inte i jämförelsen.

```
Name                     : My2ndRuleDoNotDelete
Description              : 
Protocol                 : *
SourcePortRange          : *
DestinationPortRange     : 112
SourceAddressPrefix      : *
DestinationAddressPrefix : *
Access                   : Allow
Priority                 : 1020
Direction                : Inbound
SideIndicator            : <=

Name                     : ThisRuleNeedsToStay
Description              : 
Protocol                 : TCP
SourcePortRange          : *
DestinationPortRange     : 5672
SourceAddressPrefix      : *
DestinationAddressPrefix : *
Access                   : Deny
Priority                 : 1030
Direction                : Inbound
SideIndicator            : <=
```

## <a name="next-steps"></a>Nästa steg

Om inställningarna har ändrats läser du [Hantera nätverks säkerhets grupper](../virtual-network/manage-network-security-group.md) för att spåra de nätverks säkerhets grupper och säkerhets regler som är i fråga.