---
title: Azure VMware-lösning enligt CloudSimple-Network check lista
description: Check lista för att allokera nätverks-CIDR på Azure VMware-lösning från CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 09/25/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: bfb170036293dc9f519259dc92737f30380aa84a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "77025018"
---
# <a name="networking-prerequisites-for-azure-vmware-solution-by-cloudsimple"></a>Nätverks krav för Azure VMware-lösning av CloudSimple

Azure VMware-lösningen av CloudSimple erbjuder en privat VMware-miljö som är tillgänglig för användare och program från lokala miljöer, företags hanterade enheter och Azure-resurser. Anslutningen levereras via nätverks tjänster som VPN-och Azure ExpressRoute-anslutningar. Några av dessa nätverks tjänster kräver att du anger nätverks adress intervall för att aktivera tjänsterna. 

Tabellerna i den här artikeln beskriver en uppsättning adress intervall och motsvarande tjänster som använder de angivna adresserna. Några av adresserna är obligatoriska och vissa är beroende av de tjänster som du vill distribuera. Dessa adress utrymmen ska inte överlappa något av dina lokala undernät, Azure Virtual Network-undernät eller planerade CloudSimple arbets belastnings undernät.

## <a name="network-address-ranges-required-for-creating-a-private-cloud"></a>Nätverks adress intervall som krävs för att skapa ett privat moln

När du skapar en CloudSimple-tjänst och ett privat moln måste du följa de angivna CIDR-intervallen för nätverks klassen (CIDR) enligt följande.

| Namn/Använd för     | Beskrivning                                                                                                                            | Adressintervall            |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------|--------------------------|
| Gateway-CIDR      | Krävs för Edge Services (VPN-gatewayer).  Denna CIDR krävs när CloudSimple-tjänsten skapas och måste vara från RFC 1918-utrymmet. | /28                      |
| vSphere/virtuellt San CIDR | Krävs för VMware Management Networks. Denna CIDR måste anges när du skapar ett privat moln.                                    | /24 eller/23 eller/22 eller/21 |

## <a name="network-address-range-required-for-azure-network-connection-to-an-on-premises-network"></a>Nätverks adress intervall som krävs för Azure-nätverksanslutning till ett lokalt nätverk

Anslutning från ett [lokalt nätverk till det privata moln nätverket via ExpressRoute](on-premises-connection.md) upprättar en global Reach anslutning.  Anslutningen använder Border Gateway Protocol (BGP) för att utväxla vägar mellan ditt lokala nätverk, ditt privata moln nätverk och dina Azure-nätverk.

| Namn/Använd för             | Beskrivning                                                                                                                                                                             | Adressintervall |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|
| ExpressRoute peering CIDR | Krävs när du använder ExpressRoute Global Reach för lokal anslutning. Denna CIDR måste anges när en Global Reach anslutningsbegäran görs via ett support ärende. | /29           |

## <a name="network-address-range-required-for-using-a-site-to-site-vpn-connection-to-an-on-premises-network"></a>Nätverks adress intervall som krävs för att använda en plats-till-plats-VPN-anslutning till ett lokalt nätverk

Anslutning från ett [lokalt nätverk till det privata moln nätverket genom att använda plats-till-plats-VPN](vpn-gateway.md) kräver följande IP-adresser, lokalt nätverk och identifierare. 

| Adress/adress intervall | Beskrivning                                                                                                                                                                                                                                                           |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Peer-IP               | Lokal VPN gateway offentlig IP-adress. Krävs för att upprätta en VPN-anslutning från plats till plats mellan ett lokalt Data Center och CloudSimple tjänste region. Den här IP-adressen krävs vid skapande av VPN gateway för plats-till-plats.                                         |
| Peer-ID       | Peer-ID för den lokala VPN-gatewayen. Detta är vanligt vis samma som **peer-IP**.  Om en unik identifierare anges på din lokala VPN-gateway måste identifieraren anges.  Peer-ID krävs vid skapande av VPN gateway för plats-till-plats.   |
| Lokala nätverk   | Lokala prefix som behöver åtkomst till CloudSimple-nätverk i regionen.  Ta med alla prefix från ett lokalt nätverk som kommer åt CloudSimple-nätverket, inklusive klient nätverket där användare kommer åt nätverket.                                         |

## <a name="network-address-range-required-for-using-point-to-site-vpn-connections"></a>Nätverks adress intervall som krävs för att använda punkt-till-plats-VPN-anslutningar

En punkt-till-plats-VPN-anslutning ger åtkomst till CloudSimple-nätverket från en klient dator.  [Om du vill konfigurera punkt-till-plats-VPN](vpn-gateway.md)måste du ange följande nätverks adress intervall.

| Adress/adress intervall | Beskrivning                                                                                                                                                                                                                                                                                                  |
|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Klient under nät         | DHCP-adresser tillhandahålls av klient under nätet när du ansluter med hjälp av en punkt-till-plats-VPN. Det här under nätet krävs när du skapar en punkt-till-plats-VPN-gateway på en CloudSimple-Portal.  Nätverket är uppdelat i två undernät; en för UDP-anslutningen och den andra för TCP-anslutningar. |

## <a name="next-steps"></a>Nästa steg

* [Installation av lokala brand väggar för att komma åt ditt privata moln](on-premises-firewall-configuration.md)
* [Snabb start – skapa en CloudSimple-tjänst](quickstart-create-cloudsimple-service.md)
* [Snabb start – konfigurera ett privat moln](quickstart-create-private-cloud.md)
* Läs mer om [Azures nätverks anslutningar](cloudsimple-azure-network-connection.md)
* Läs mer om [VPN-gatewayer](cloudsimple-vpn-gateways.md)
