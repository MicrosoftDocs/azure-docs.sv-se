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
ms.openlocfilehash: 526d8a6a103e7623bac459004bf9ac79e4927541
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "81686992"
---
1. Visa anslutningar för den valda direkt peeringen.
    ```powershell
    $directPeering.Connections

    PeeringDBFacilityId         : 6
    UseForPeeringService        : False
    SessionAddressProvider      : Peer
    SessionPrefixV4             : 12.245.179.20/30
    ConnectionIdentifier        : 3f196dfe-c174-4900-ab1c-fb7b50ad1535
    SessionPrefixV6             :
    MicrosoftSessionIPv4Address : 12.245.179.22
    BandwidthInMbps             : 100000
    SessionStateV4              : Established
    SessionStateV6              : None
    ConnectionState             : Active
    ```
1. Välj den anslutning som du vill aktivera för peering-tjänsten. I det här exemplet ska vi använda den enda tillgängliga anslutningen.
    ```powershell
    $directPeering.Connections[1] = $directPeering.Connections[1] | Set-AzPeeringDirectConnectionObject -UseForPeeringService $true

    PeeringDBFacilityId         : 6
    UseForPeeringService        : True
    SessionAddressProvider      : Peer
    SessionPrefixV4             : 12.245.179.20/30
    ConnectionIdentifier        : 3f196dfe-c174-4900-ab1c-fb7b50ad1535
    SessionPrefixV6             :
    MicrosoftSessionIPv4Address : 12.245.179.22
    BandwidthInMbps             : 100000
    SessionStateV4              : Established
    SessionStateV6              : None
    ConnectionState             : Active
    ```
1. Spara nu de ändringar som gjorts i direkt peering med hjälp av det här kommandot:
    ```powershell
    $directPeering | Update-AzPeering
    ```
    
    Här är ett exempel på utdata:
    
    ```powershell
        Name                 : SeattleDirectPeering
        Sku.Name             : Basic_Premium_Free
        Kind                 : Direct
        Connections          : {71,71}
        PeerAsn.Id           : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/SeattleDirectPeering
        UseForPeeringService : True
        PeeringLocation      : Seattle
        ProvisioningState    : Succeeded
        Location             : centralus
        Id                   : /subscriptions/{subscriptionId}/resourceGroups/PeeringResourceGroup/providers/Microsoft.Peering/peerings/SeattleDirectPeering
        Type                 : Microsoft.Peering/peerings
        Tags                 : {}
    ```
    