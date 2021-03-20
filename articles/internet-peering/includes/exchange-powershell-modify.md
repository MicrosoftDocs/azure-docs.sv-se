---
title: ta med fil
titleSuffix: Azure
description: ta med fil
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 12d169697a35af446392843eb57e6ec3a5508e45
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "81678688"
---
Följande ändrings åtgärder stöds för Exchange-peering:
* Lägg till Exchange peering-anslutningar.
* Ta bort Exchange peering-anslutningar.
* Lägg till en IPv4-eller IPv6-session på aktiva anslutningar.
* Ta bort en IPv4-eller IPv6-session på aktiva anslutningar.


### <a name="add-exchange-peering-connections"></a>Lägg till Exchange peering-anslutningar

Det här exemplet beskriver hur du lägger till anslutningar till en befintlig Exchange-peering.

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

$connection = New-AzPeeringExchangeConnectionObject `
    -PeeringDBFacilityId $exchangeLocation[1].PeeringDBFacilityId `
    -PeerSessionIPv4Address 198.32.134.22 `
    -PeerSessionIPv6Address  2001:504:12::22 `
    -MaxPrefixesAdvertisedIPv4 2000 `
    -MaxPrefixesAdvertisedIPv6 2000 `

$exchangePeering.Connections.Add($connection)

$exchangePeering | Update-AzPeering

```

### <a name="remove-exchange-peering-connections"></a>Ta bort Exchange peering-anslutningar

Det här exemplet beskriver hur du tar bort anslutningar till en befintlig Exchange-peering.

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

```

Visa alla anslutningar och välj den anslutning som du vill ta bort. 

```powershell

$exchangePeering

Name              : SeattleExchangePeering
Sku.Name          : Basic_Exchange_Free
Kind              : Exchange
Connections       : {11}
PeerAsn.Id        : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/{peerAsnName}
PeeringLocation   : Seattle
ProvisioningState : Succeeded
Location          : West US 2
Id                : /subscriptions/{subscriptionId}/resourceGroups/PeeringResourceGroup/providers/Microsoft.Peering/peerings/SeattleExchangePeering
Type              : Microsoft.Peering/peerings
Tags              : {}

```

I följande kommando, i stället för 0, anger du index numret för den anslutning som du vill ta bort.

```powershell

$connection = $exchangePeering.Connections[0]

$exchangePeering.Connections.Remove($connection)

$exchangePeering | Update-AzPeering

```

### <a name="add-an-ipv4-or-ipv6-session-on-active-connections"></a>Lägg till en IPv4-eller IPv6-session på aktiva anslutningar

Det här exemplet beskriver hur du lägger till en IPv6-session i en befintlig Exchange-anslutning.

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

$exchangePeering.Connections[0].BgpSession.PeerSessionIPv6Address = "2001:504:12::34"

$exchangePeering | Update-AzPeering

```

### <a name="remove-an-ipv4-or-ipv6-session-on-active-connections"></a>Ta bort en IPv4-eller IPv6-session på aktiva anslutningar

Det finns för närvarande inte stöd för att ta bort en IPv4-eller IPv6-session från en befintlig anslutning på PowerShell. Kontakta [Microsoft-peering](mailto:peeringexperience@microsoft.com)om du vill ha mer information.