---
title: Översikt över Azure Virtual Network-tryckning | Microsoft Docs
description: Lär dig mer om Virtual Network-tryckning. Med Virtual Network-tryckning får du en djup kopia av den virtuella datorns nätverks trafik som kan strömmas till en paket insamlare.
services: virtual-network
documentationcenter: na
author: karthikananth
manager: ganesr
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/14/2019
ms.author: kaanan
ms.openlocfilehash: 6160dd09edc57f2f52306d4dad0dde413fff0616
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102617190"
---
# <a name="virtual-network-tap"></a>Virtual Network TAP
> [!IMPORTANT]
> För hands versionen av virtuellt nätverk är för närvarande stoppad i alla Azure-regioner. Du kan e-posta oss <azurevnettap@microsoft.com> med ditt prenumerations-ID så meddelar vi dig kommande uppdateringar om förhands granskningen. Under tiden kan du använda agentbaserade eller NVA lösningar som tillhandahåller tryck-/nätverks Synlighets funktioner via våra [partner lösningar för paket](#virtual-network-tap-partner-solutions) integrering som finns i [Azure Marketplace-erbjudanden](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances%3Ball&search=Network%20Traffic&filters=partners).

Med hjälp av Azure Virtual Network-TRYCKNINGen (Terminal Access Point) kan du kontinuerligt strömma din virtuella dators nätverks trafik till en insamlare eller ett analys verktyg för nätverks paket. Insamlings-eller analys verktyget tillhandahålls av en [virtuell nätverks utrustnings](https://azure.microsoft.com/solutions/network-appliances/) partner. För en lista över partner lösningar som har verifierats för att fungera med Virtual Network-tryckning, se [partner lösningar](#virtual-network-tap-partner-solutions).
Följande bild visar hur det virtuella nätverket trycker på Works. Du kan lägga till en tryck konfiguration i ett [nätverks gränssnitt](virtual-network-network-interface.md) som är kopplat till en virtuell dator som distribueras i det virtuella nätverket. Målet är en IP-adress för virtuellt nätverk i samma virtuella nätverk som det övervakade nätverks gränssnittet eller ett [peer](virtual-network-peering-overview.md) -kopplat virtuellt nätverk. Insamlings lösningen för Virtual Network-tryckning kan distribueras bakom en intern Azure-belastningsutjämnare för hög tillgänglighet.
![Hur det virtuella nätverket trycker på Works](./media/virtual-network-tap/architecture.png)

## <a name="prerequisites"></a>Förutsättningar

Innan du skapar ett virtuellt nätverk trycker du på en bekräftelse som du har registrerat i förhands granskningen och har en eller flera virtuella datorer som skapats med [Azure Resource Manager](../azure-resource-manager/management/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) distributions modell och en partner lösning för att aggregera trafiken i samma Azure-region. Om du inte har en partner lösning i ditt virtuella nätverk, se [partner lösningar](#virtual-network-tap-partner-solutions) för att distribuera en. Du kan använda samma virtuella nätverk för att aggregera trafik från flera nätverks gränssnitt i samma eller olika prenumerationer. Om de övervakade nätverks gränssnitten finns i olika prenumerationer måste prenumerationerna vara kopplade till samma Azure Active Directory-klient. Dessutom kan de övervakade nätverks gränssnitten och mål slut punkten för att aggregera trafikövervakningen vara i peer-kopplat virtuella nätverk i samma region. Om du använder den här distributions modellen kontrollerar du att [peering av virtuella nätverk](virtual-network-peering-overview.md) är aktiverat innan du konfigurerar det virtuella nätverket.

## <a name="permissions"></a>Behörigheter

De konton som du använder för att tillämpa tryck på konfiguration på nätverks gränssnitt måste tilldelas rollen [nätverks deltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) eller en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som har tilldelats nödvändiga åtgärder från följande tabell:

| Action | Name |
|---|---|
| Microsoft. Network/virtualNetworkTaps/* | Krävs för att skapa, uppdatera, läsa och ta bort ett virtuellt nätverk tryck på resurs |
| Microsoft. Network/networkInterfaces/Read | Krävs för att läsa nätverks gränssnitts resursen där TRYCKNINGen ska konfigureras |
| Microsoft. Network/tapConfigurations/* | Krävs för att skapa, uppdatera, läsa och ta bort TRYCKNINGs konfigurationen på ett nätverks gränssnitt |


## <a name="virtual-network-tap-partner-solutions"></a>Virtuella nätverk tryck på partner lösningar

### <a name="network-packet-brokers"></a>Nätverks paket hanterare

- [GigaVUE Cloud Suite för Azure](https://www.gigamon.com/solutions/cloud/public-cloud/gigavue-cloud-suite-azure.html)
- [Ixia CloudLens](https://www.ixiacom.com/cloudlens/cloudlens-azure)
- [Nubeva Prisms](https://www.nubeva.com/azurevtap)
- [Stor växel för stor övervakning av infrastruktur resurser](https://www.bigswitch.com/products/big-monitoring-fabric/public-cloud/microsoft-azure)

### <a name="security-analytics-networkapplication-performance-management"></a>Säkerhets analys, hantering av nätverks-/program prestanda

- [Aktiv säkerhet](https://awakesecurity.com/technology-partners/microsoft-azure/)
- [Cisco Stealthwatch-moln](https://blogs.cisco.com/security/cisco-stealthwatch-cloud-and-microsoft-azure-reliable-cloud-infrastructure-meets-comprehensive-cloud-security)
- [Darktrace](https://www.darktrace.com/en/azure/)
- [ExtraHop Reveal(x)](https://www.extrahop.com/partners/tech-partners/microsoft/)
- [Fidelis cybersäkerhet](https://www.fidelissecurity.com/technology-partners/microsoft-azure )
- [Flowmon](https://www.flowmon.com/blog/azure-vtap)
- [NetFort LANGuardian](https://www.netfort.com/languardian/solutions/visibility-in-azure-network-tap/)
- [Netscout-vSTREAM]( https://www.netscout.com/marketplace-azure)
- [NoName-säkerhet](https://nonamesecurity.com/)
- [Riverbed SteelCentral-AppResponse]( https://www.riverbed.com/products/steelcentral/steelcentral-appresponse-11.html)
- [RSA netvittne®-plattform](https://www.rsa.com/content/dam/en/solution-brief/rsa-netwitness-platform-overview-for-federal-agencies.pdf)
- [Vectra Cognito](https://vectra.ai/microsoftazure)



## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [skapar ett virtuellt nätverk genom att trycka på](tutorial-tap-virtual-network-cli.md).
