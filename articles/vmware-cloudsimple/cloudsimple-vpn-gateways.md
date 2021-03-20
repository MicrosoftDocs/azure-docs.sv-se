---
title: Azure VMware-lösning av CloudSimple-VPN-gatewayer
description: Lär dig mer om att CloudSimple plats-till-plats-och punkt-till-plats-VPN-gatewayer, som används för att skicka krypterad trafik mellan en CloudSimple region och andra resurser.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6e013bb96990a8f3a0ef7d3a58529b200919e276
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88140640"
---
# <a name="vpn-gateways-overview"></a>Översikt över VPN-gatewayer

En VPN-gateway används för att skicka krypterad trafik mellan ett CloudSimple regions nätverk på en lokal plats eller en dator via det offentliga Internet.  Varje region kan ha en VPN-gateway som har stöd för flera anslutningar. När du skapar flera anslutningar till samma VPN-gateway delar alla VPN-tunnlar på den tillgängliga bandbredden.

CloudSimple tillhandahåller två typer av VPN-gatewayer:

* Plats-till-plats-VPN-gateway
* Punkt-till-plats-VPN-gateway

## <a name="site-to-site-vpn-gateway"></a>Plats-till-plats-VPN-gateway

En plats-till-plats-VPN-gateway används för att skicka krypterad trafik mellan ett CloudSimple region nätverk och ett lokalt Data Center. Använd den här anslutningen för att definiera undernät/CIDR-intervall för nätverks trafik mellan ditt lokala nätverk och CloudSimple regions nätverk.

VPN-gatewayen gör att du kan använda tjänster lokalt i ditt privata moln och tjänster i ditt privata moln från det lokala nätverket.  CloudSimple tillhandahåller en principbaserad VPN-server för att upprätta anslutningen från ditt lokala nätverk.

Användnings fall för plats-till-plats-VPN:

* Tillgänglighet för ditt privata moln vCenter från valfri arbets station i ditt lokala nätverk.
* Användning av din lokala Active Directory som vCenter-identitets källa.
* Bekväm överföring av VM-mallar, ISO och andra filer från dina lokala resurser till ditt privata moln vCenter.
* Tillgänglighet för arbets belastningar som körs i ditt privata moln från ditt lokala nätverk.

![Topologi för plats-till-plats-VPN-anslutning](media/cloudsimple-site-to-site-vpn-connection.png)

### <a name="cryptographic-parameters"></a>Kryptografiska parametrar

En plats-till-plats-VPN-anslutning använder följande kryptografiska standard parametrar för att upprätta en säker anslutning.  När du skapar en anslutning från din lokala VPN-enhet använder du någon av följande parametrar som stöds av din lokala VPN-gateway.

#### <a name="phase-1-proposals"></a>Fas 1-förslag

| Parameter | Förslag 1 | Förslag 2 | Förslag 3 |
|-----------|------------|------------|------------|
| IKE-version | IKEv1 | IKEv1 | IKEv1 |
| Kryptering | AES 128 | AES 256 | AES 256 |
| Hash-algoritm| SHA 256 | SHA 256 | SHA 1 |
| Diffie Hellman-grupp (DH-grupp) | 2 | 2 | 2 |
| Livs längd | 28 800 sekunder | 28 800 sekunder | 28 800 sekunder |
| Datavolym | 4 GB | 4 GB | 4 GB |

#### <a name="phase-2-proposals"></a>Fas 2-förslag

| Parameter | Förslag 1 | Förslag 2 | Förslag 3 |
|-----------|------------|------------|------------|
| Kryptering | AES 128 | AES 256 | AES 256 |
| Hash-algoritm| SHA 256 | SHA 256 | SHA 1 |
| PFS-grupp (Perfect Forward Secrecy) | Inga | Inga | Inga |
| Livs längd | 1 800 sekunder | 1 800 sekunder | 1 800 sekunder |
| Datavolym | 4 GB | 4 GB | 4 GB |


> [!IMPORTANT]
> Ange TCP MSS-ihopfogning vid 1200 på VPN-enheten. Eller om VPN-enheterna inte stöder MSS-ihopfogning kan du i stället ange MTU för tunnel gränssnittet till 1240 byte.

## <a name="point-to-site-vpn-gateway"></a>Punkt-till-plats-VPN-gateway

En punkt-till-plats-VPN används för att skicka krypterad trafik mellan ett CloudSimple region nätverk och en klient dator.  Punkt-till-plats-VPN är det enklaste sättet att komma åt ditt privata moln nätverk, inklusive ditt privata moln vCenter och virtuella datorer för arbets belastning.  Använd punkt-till-plats-VPN-anslutning om du ansluter till det privata molnet via en fjärr anslutning.

## <a name="next-steps"></a>Nästa steg

* [Konfigurera VPN gateway](vpn-gateway.md)
