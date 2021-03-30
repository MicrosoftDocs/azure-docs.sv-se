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
ms.openlocfilehash: b2609a069872ec55ac9068fadcbb3f312d68a630
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "81680806"
---
I det här avsnittet beskrivs hur du utför följande ändrings åtgärder för direkt peering:

* Lägg till direkta peering-anslutningar.
* Ta bort direkta peering-anslutningar.
* Uppgradera eller nedgradera bandbredd på aktiva anslutningar.
* Lägg till IPv4-eller IPv6-sessioner på aktiva anslutningar.
* Ta bort IPv4-eller IPv6-sessioner på aktiva anslutningar.

### <a name="add-direct-peering-connections"></a>Lägg till direkta peering-anslutningar

Det här exemplet beskriver hur du lägger till anslutningar till befintlig direkt peering.

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"

$connection = New-AzPeeringDirectConnection `
    -PeeringDBFacilityId $peeringLocation.PeeringDBFacilityId `
    -SessionPrefixV4 "10.22.31.0/31" `
    -SessionPrefixV6 "fe02::3e:0/127" `
    -MaxPrefixesAdvertisedIPv4 1000 `
    -MaxPrefixesAdvertisedIPv6 100 `
    -BandwidthInMbps 10000

$directPeering.Connections.Add($connection)

$directPeering | Update-AzPeering
```

### <a name="remove-direct-peering-connections"></a>Ta bort direkta peering-anslutningar

Det finns för närvarande inte stöd för att ta bort en anslutning på PowerShell. Kontakta [Microsoft-peering](mailto:peeringexperience@microsoft.com)om du vill ha mer information.

<!--
```powershell
$directPeering.Connections.Remove($directPeering.Connections[0])

$directPeering | Update-AzPeering
```
-->

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Uppgradera eller nedgradera bandbredd på aktiva anslutningar

Det här exemplet beskriver hur du lägger till 10 Gbit/s i en befintlig direkt anslutning.

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BandwidthInMbps  = 20000
$directPeering | Update-AzPeering

```

### <a name="add-ipv4-or-ipv6-sessions-on-active-connections"></a>Lägg till IPv4-eller IPv6-sessioner på aktiva anslutningar

Det här exemplet beskriver hur du lägger till en IPv6-session på en befintlig direkt anslutning med endast en IPv4-session. 

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BGPSession.SessionPrefixv6 = "fe01::3e:0/127"
$directPeering | Update-AzPeering

```

### <a name="remove-ipv4-or-ipv6-sessions-on-active-connections"></a>Ta bort IPv4-eller IPv6-sessioner på aktiva anslutningar

Det finns för närvarande inte stöd för att ta bort en IPv4-eller IPv6-session från en befintlig anslutning på PowerShell. Kontakta [Microsoft-peering](mailto:peeringexperience@microsoft.com)om du vill ha mer information.