---
title: 'Azure-ExpressRoute: krav'
description: Den här sidan innehåller en lista med de krav som ska vara uppfyllda innan du kan beställa en Azure ExpressRoute-krets. Den innehåller en check lista.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: duau
ms.openlocfilehash: 1fad1bca18d16ac3b6a654a3c289d0a14e3cd2e2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92204800"
---
# <a name="expressroute-prerequisites--checklist"></a>ExpressRoute-krav och checklista
Om du vill ansluta till Microsofts molntjänster med ExpressRoute måste du kontrollera att följande krav som anges i följande avsnitt är uppfyllda.

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

## <a name="azure-account"></a>Azure-konto
* Ett giltigt och aktivt Microsoft Azure-konto. Detta konto är nödvändigt för att kunna konfigurera ExpressRoute-kretsen. ExpressRoute-kretsar är resurser i Azure-prenumerationer. En Azure-prenumeration är ett krav även om anslutningen är begränsad till Microsofts moln tjänster som inte kommer från Azure, till exempel Microsoft 365.
* En aktiv Microsoft 365-prenumeration (om du använder Microsoft 365 Services). Mer information finns i avsnittet Microsoft 365 särskilda krav i den här artikeln.

## <a name="connectivity-provider"></a>Anslutningsleverantör

* Du kan samarbeta med en [ExpressRoute-anslutningspartner](expressroute-locations.md#partners) för att ansluta till Microsoft-molnet. Du kan konfigurera en anslutning mellan ditt lokala nätverk och Microsoft på [tre sätt](expressroute-introduction.md).
* Om din leverantör inte är en ExpressRoute-anslutningspartner kan du fortfarande ansluta till Microsoft-molnet via en [molnutbytesleverantör](expressroute-locations.md#connectivity-through-exchange-providers).

## <a name="network-requirements"></a>Nätverkskrav
* **Redundans på varje peering-plats**: Microsoft kräver att REDUNDANTa BGP-sessioner konfigureras mellan Microsofts routrar och peering-routrarna på varje ExpressRoute-krets (även om du bara har [en fysisk anslutning till ett moln utbyte](expressroute-faqs.md#onep2plink)).
* **Redundans för haveri beredskap**: Microsoft rekommenderar att du konfigurerar minst två ExpressRoute-kretsar på olika peering-platser för att undvika en enskild felpunkt.
* **Routning**: Beroende på hur du ansluter till Microsoft Cloud måste du eller din leverantör konfigurera och hantera BGP-sessioner för [routningsdomäner](expressroute-circuit-peerings.md). Vissa Ethernet-anslutningsleverantörer eller molnutbytesleverantörer kan erbjuda BGP-hantering som en mervärdestjänst.
* **NAT**: Microsoft godkänner bara offentliga IP-adresser via Microsoft-peering. Om du använder privata IP-adresser i det lokala nätverket måste du eller din leverantör översätta dem till offentliga IP-adresser [med hjälp av NAT](expressroute-nat.md).
* **QoS**: Skype för företag har olika tjänster (till exempel: röst, video, text) som kräver särskild QoS-behandling. Du och din leverantör bör följa [QoS-kraven](expressroute-qos.md).
* **Nätverkssäkerhet**: Fundera över [nätverkssäkerheten](/azure/cloud-adoption-framework/reference/networking-vdc) när du ansluter till Microsoft Cloud via ExpressRoute.

## <a name="microsoft-365"></a>Microsoft 365
Om du planerar att aktivera Microsoft 365 på ExpressRoute kan du läsa följande dokument för mer information om Microsoft 365 krav.

* [Azure-ExpressRoute för Microsoft 365](/microsoft-365/enterprise/azure-expressroute)
* [Routning med ExpressRoute för Microsoft 365](/microsoft-365/enterprise/routing-with-expressroute)
* [Hög tillgänglighet och redundans med ExpressRoute](/microsoft-365/enterprise/network-planning-with-expressroute)
* [Microsoft 365-URL: er och IP-adressintervall](/microsoft-365/enterprise/urls-and-ip-address-ranges)
* [Nätverks planering och prestanda justering för Microsoft 365](/microsoft-365/enterprise/network-planning-and-performance)
* [Planera nätverks-och migrering för Microsoft 365](/microsoft-365/enterprise/network-and-migration-planning)
* [Microsoft 365 integrering med lokala miljöer](/microsoft-365/enterprise/microsoft-365-integration)
* [ExpressRoute på Office 365 – avancerade utbildningsvideor](https://channel9.msdn.com/series/aer/)

## <a name="next-steps"></a>Nästa steg
* Mer information om ExpressRoute finns i [vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).
* Hitta en ExpressRoute-anslutningsleverantör. Se [ExpressRoute-partners och peeringplatser](expressroute-locations.md).
* Se kraven för [routning](expressroute-routing.md), [NAT](expressroute-nat.md) och [QoS](expressroute-qos.md).
* Konfigurera ExpressRoute-anslutningen.
  * [Skapa en ExpressRoute-krets](expressroute-howto-circuit-arm.md)
  * [Konfigurera routning](expressroute-howto-routing-arm.md)
  * [Länka ett VNet till en ExpressRoute-krets](expressroute-howto-linkvnet-arm.md)