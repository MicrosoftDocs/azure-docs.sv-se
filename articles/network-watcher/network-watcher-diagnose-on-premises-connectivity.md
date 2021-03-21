---
title: Diagnostisera lokal anslutning via VPN-gateway
titleSuffix: Azure Network Watcher
description: Den här artikeln beskriver hur du diagnostiserar lokal anslutning via VPN-gateway med Azure Network Watcher Resource Troubleshooting.
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: aeffbf3d-fd19-4d61-831d-a7114f7534f9
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 3b8b53446799eec3473d63c89672393a35f9787a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104670963"
---
# <a name="diagnose-on-premises-connectivity-via-vpn-gateways"></a>Diagnostisera lokal anslutning via VPN-gatewayer

Med Azure VPN Gateway kan du skapa en hybrid lösning som tillgodoser behovet av en säker anslutning mellan ditt lokala nätverk och ditt virtuella Azure-nätverk. Eftersom dina krav är unika, så är valet av lokal VPN-enhet. Azure stöder för närvarande [flera VPN-enheter](../vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable) som ständigt verifieras i partnerskap med enhets leverantörer. Granska de enhetsspecifika konfigurations inställningarna innan du konfigurerar den lokala VPN-enheten. På samma sätt konfigureras Azure VPN Gateway med en uppsättning [IPsec-parametrar som stöds](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec) för att upprätta anslutningar. För närvarande finns det inget sätt att ange eller välja en speciell kombination av IPsec-parametrar från Azure-VPN Gateway. För att upprätta en lyckad anslutning mellan lokala och Azure måste inställningarna för den lokala VPN-enheten vara i överensstämmelse med IPsec-parametrarna som föreskrivs i Azure VPN Gateway. Om inställningarna är felaktiga går det inte att ansluta och tills de här problemen är lösta, och det tog aldrig några timmar att identifiera och åtgärda problemet.

Med Azure Network Watcher fel söknings funktionen kan du diagnostisera eventuella problem med din gateway och dina anslutningar och inom några minuter har tillräckligt med information för att fatta ett välgrundat beslut om att åtgärda problemet.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenario"></a>Scenario

Du vill konfigurera en plats-till-plats-anslutning mellan Azure och lokalt med FortiGate som lokal VPN Gateway. För att uppnå det här scenariot behöver du följande konfiguration:

1. Virtual Network Gateway – VPN Gateway på Azure
1. Lokal nätverksgateway – den lokala Nätverksgatewayen [(Fortigate) VPN gateway](../vpn-gateway/tutorial-site-to-site-portal.md#LocalNetworkGateway) representation i Azure-molnet
1. Plats-till-plats-anslutning (Route-baserad) – [anslutning mellan VPN gateway och den lokala routern](../vpn-gateway/tutorial-site-to-site-portal.md#CreateConnection)
1. [Konfigurera FortiGate](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/Site-to-Site_VPN_using_FortiGate.md)

Detaljerade anvisningar för hur du konfigurerar en plats-till-plats-konfiguration finns på: [skapa ett VNet med en plats-till-plats-anslutning med hjälp av Azure Portal](../vpn-gateway/tutorial-site-to-site-portal.md).

Ett av de viktiga konfigurations stegen är att konfigurera IPsec-kommunikations parametrarna, eventuella felkonfigurationer leder till förlust av anslutning mellan det lokala nätverket och Azure. För närvarande har Azure VPN-gatewayer kon figurer ATS för att stödja följande IPsec-parametrar för fas 1. Som du kan se i tabellen nedan är de krypteringsalgoritmer som stöds av Azure VPN Gateway AES256, AES128 och 3DES.

### <a name="ike-phase-1-setup"></a>IKE fas 1-installation

| **Egenskap** | **Principbaserad** | **Routningsbaserad och standard eller High-Performance VPN-gateway** |
| --- | --- | --- |
| IKE-version |IKEv1 |IKEv2 |
| Diffie-Hellman Group |Grupp 2 (1 024 bitar) |Grupp 2 (1 024 bitar) |
| Autentiseringsmetod |I förväg delad nyckel |I förväg delad nyckel |
| Krypteringsalgoritmer |AES256 AES128 3DES |AES256 3DES |
| Hash-algoritm |SHA1(SHA128) |SHA1(SHA128), SHA2(SHA256) |
| Fas 1, Security Association (SA), livslängd (tid) |28 800 sekunder |28 800 sekunder |

Som användare måste du konfigurera din FortiGate, en exempel konfiguration finns på [GitHub](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/fortigate_show%20full-configuration.txt). Vet du att du har konfigurerat din FortiGate att använda SHA-512 som hash-algoritm. Eftersom den här algoritmen inte är en algoritm som stöds för principbaserade anslutningar fungerar VPN-anslutningen.

De här problemen är svåra att felsöka och Rotors Aker är ofta inte intuitiva. I det här fallet kan du öppna ett support ärende för att få hjälp med att lösa problemet. Men med Azure Network Watcher felsöka API kan du identifiera problemen på egen hand.

## <a name="troubleshooting-using-azure-network-watcher"></a>Fel sökning med Azure Network Watcher

För att diagnosticera anslutningen ansluter du till Azure PowerShell och initierar `Start-AzNetworkWatcherResourceTroubleshooting` cmdleten. Du hittar information om hur du använder denna cmdlet vid [fel sökning Virtual Network gateway och anslutningar – PowerShell](network-watcher-troubleshoot-manage-powershell.md). Den här cmdleten kan ta upp till några minuter att slutföra.

När cmdleten har slutförts kan du gå till den lagrings plats som anges i cmdlet: en för att få detaljerad information om problemet och loggarna. Azure Network Watcher skapar en zip-mapp som innehåller följande loggfiler:

![1][1]

Öppna filen med namnet IKEErrors.txt så visas följande fel meddelande, som anger ett problem med den lokala IKE-inställningen fel konfiguration.

```
Error: On-premises device rejected Quick Mode settings. Check values.
     based on log : Peer sent NO_PROPOSAL_CHOSEN notify
```

Du kan få detaljerad information från Scrubbed-wfpdiag.txt om felet, som i det här fallet nämns det att det inte gick att `ERROR_IPSEC_IKE_POLICY_MATCH` leda till att anslutningen fungerar korrekt.

En annan vanlig fel konfiguration är att ange Felaktiga delade nycklar. Om du i föregående exempel har angett olika delade nycklar, visar IKEErrors.txt följande fel: `Error: Authentication failed. Check shared key` .

Med Azure Network Watcher fel söknings funktionen kan du diagnostisera och felsöka din VPN Gateway och anslutning med enkel PowerShell-cmdlet. För närvarande stöder vi diagnostisering av följande villkor och arbetar med att lägga till fler villkor.

### <a name="gateway"></a>Gateway

| Feltyp | Orsak | Loggas|
|---|---|---|
| NoFault | När inget fel har identifierats. |Ja|
| GatewayNotFound | Det går inte att hitta någon gateway eller gateway. |Nej|
| PlannedMaintenance |  Underhåll utförs på Gateway-instansen.  |Nej|
| UserDrivenUpdate | När en användar uppdatering pågår. Detta kan vara en åtgärd för storleks ändring. | Nej |
| VipUnResponsive | Det går inte att komma åt den primära instansen av gatewayen. Detta inträffar när hälso avsökningen Miss lyckas. | Nej |
| PlatformInActive | Det är något problem med plattformen. | Inga|
| ServiceNotRunning | Den underliggande tjänsten körs inte. | Inga|
| NoConnectionsFoundForGateway | Det finns inga anslutningar på gatewayen. Detta är endast en varning.| Inga|
| ConnectionsNotConnected | Ingen anslutning är ansluten. Detta är endast en varning.| Ja|
| GatewayCPUUsageExceeded | CPU-användningen för aktuell gateway-användning är > 95%. | Ja |

### <a name="connection"></a>Anslutning

| Feltyp | Orsak | Loggas|
|---|---|---|
| NoFault | När inget fel har identifierats. |Ja|
| GatewayNotFound | Det går inte att hitta någon gateway eller gateway. |Nej|
| PlannedMaintenance | Underhåll utförs på Gateway-instansen.  |Nej|
| UserDrivenUpdate | När en användar uppdatering pågår. Detta kan vara en åtgärd för storleks ändring.  | Nej |
| VipUnResponsive | Det går inte att komma åt den primära instansen av gatewayen. Det inträffar när hälso avsökningen Miss lyckas. | Inga |
| ConnectionEntityNotFound | Anslutnings konfigurationen saknas. | Inga |
| ConnectionIsMarkedDisconnected | Anslutningen är markerad som frånkopplad. |Inga|
| ConnectionNotConfiguredOnGateway | Ingen anslutning har kon figurer ATS för den underliggande tjänsten. | Ja |
| ConnectionMarkedStandby | Den underliggande tjänsten är markerad som standby.| Ja|
| Autentisering | Matchnings fel för i förväg delad nyckel. | Ja|
| PeerReachability | Det går inte att komma åt peer-gatewayen. | Ja|
| IkePolicyMismatch | Peer-gatewayen har IKE-principer som inte stöds av Azure. | Ja|
| WfpParse-fel | Ett fel uppstod vid parsning av WFP-loggen. |Ja|

## <a name="next-steps"></a>Nästa steg

Lär dig att kontrol lera VPN Gateway-anslutningen med PowerShell och Azure Automation genom att gå till [övervaka VPN-gatewayer med Azure Network Watcher fel sökning](network-watcher-monitor-with-azure-automation.md)

[1]: ./media/network-watcher-diagnose-on-premises-connectivity/figure1.png