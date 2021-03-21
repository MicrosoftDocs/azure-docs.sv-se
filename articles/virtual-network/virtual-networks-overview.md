---
title: Azure Virtual Network
description: Lär dig mer om Azure Virtual Network-koncept och-funktioner, inklusive adress utrymme, undernät, regioner och prenumerationer.
services: virtual-network
documentationcenter: na
author: KumudD
Customer intent: As someone with a basic network background that is new to Azure, I want to understand the capabilities of Azure Virtual Network, so that my Azure resources such as VMs, can securely communicate with each other, the internet, and my on-premises resources.
ms.service: virtual-network
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/03/2020
ms.author: kumud
ms.openlocfilehash: 849127ed0846928a77795ac0a1ea3f7a091468b5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100635956"
---
# <a name="what-is-azure-virtual-network"></a>Vad är Azure Virtual Network?

Azure Virtual Network (VNet) är det grundläggande Bygg blocket för ditt privata nätverk i Azure. VNet möjliggör många typer av Azure-resurser, till exempel Azure Virtual Machines (VM), för att på ett säkert sätt kommunicera med varandra, Internet och lokala nätverk. VNet liknar ett traditionellt nätverk som du kommer att använda i ditt eget Data Center, men ger ytterligare fördelar med Azures infrastruktur, till exempel skalning, tillgänglighet och isolering.

## <a name="why-use-an-azure-virtual-network"></a>Varför ska jag använda ett virtuellt Azure-nätverk?
Azure Virtual Network gör det möjligt för Azure-resurser att kommunicera på ett säkert sätt med varandra, Internet och lokala nätverk. Viktiga scenarier som du kan utföra med ett virtuellt nätverk inkluderar-kommunikation av Azure-resurser med Internet, kommunikation mellan Azure-resurser, kommunikation med lokala resurser, filtrering av nätverks trafik, routning av nätverks trafik och integrering med Azure-tjänster.

### <a name="communicate-with-the-internet"></a>Kommunicera med Internet

Alla resurser i ett VNet kan som standard kommunicera utgående till Internet. Du kan kommunicera inkommande trafik till en resurs genom att tilldela en offentlig IP-adress eller en offentlig lastbalanserare. Du kan också använda en offentlig IP-adress eller en offentlig lastbalanserare för att hantera utgående anslutningar.  Mer information om utgående anslutningar i Azure finns i avsnitten om [utgående anslutningar](../load-balancer/load-balancer-outbound-connections.md), [offentliga IP-adresser](virtual-network-public-ip-address.md) och [lastbalanseraren](../load-balancer/load-balancer-overview.md).

>[!NOTE]
>Om du bara använder en intern [standardlastbalanserare](../load-balancer/load-balancer-overview.md), är utgående anslutningar inte tillgängliga förrän du definierar hur du vill att [utgående anslutningar](../load-balancer/load-balancer-outbound-connections.md) ska fungera med en offentlig IP-adress på instansnivå eller en offentlig lastbalanserare.

### <a name="communicate-between-azure-resources"></a>Kommunikation mellan Azure-resurser

Azure-resurser kommunicerar säkert med varandra på något av följande sätt:

- **Via ett virtuellt nätverk**: Du kan distribuera virtuella datorer och flera andra typer av Azure-resurser till ett virtuellt nätverk, som Azure App Service-miljöer, AKS (Azure Kubernetes Service) och Azure Virtual Machine Scale Sets. En fullständig lista över Azure-resurser som du kan distribuera till ett virtuellt nätverk finns i [Tjänstintegration för virtuella nätverk](virtual-network-for-azure-services.md).
- **Via en tjänst slut punkt för virtuella nätverk**: utöka det privata adress utrymmet för det virtuella nätverket och identiteten för ditt virtuella nätverk till Azures tjänst resurser, till exempel Azure Storage konton och Azure SQL Database, via en direkt anslutning. Med tjänstslutpunkter kan du skydda dina kritiska Azure-tjänstresurser till endast ett virtuellt nätverk. Läs mer i [Översikt över tjänstslutpunkter för virtuella nätverk](virtual-network-service-endpoints-overview.md).
- **Genom VNet-peering**: du kan ansluta virtuella nätverk till varandra, vilket gör det möjligt för resurser i det virtuella nätverket att kommunicera med varandra, med hjälp av peering av virtuella nätverk. De virtuella nätverken du ansluter kan finnas i samma, eller olika, Azure-regioner. Läs mer i [Peerkoppling av virtuella nätverk](virtual-network-peering-overview.md).

### <a name="communicate-with-on-premises-resources"></a>Kommunikation med lokala resurser

Du kan ansluta lokala datorer och nätverk till ett virtuellt nätverk med hjälp av valfri kombination av följande alternativ:

- **Punkt-till-plats-VPN (virtuellt privat nätverk):** Upprättas mellan ett virtuellt nätverk och en dator i ditt nätverk. Anslutningen måste konfigureras för varje dator som du vill ansluta till ett virtuellt nätverk. Den här anslutningstypen är bra om du precis har börjat med Azure, eller för utvecklare, eftersom det krävs få eller inga ändringar i ditt befintliga nätverk. Kommunikationen mellan datorn och ett virtuellt nätverk skickas genom en krypterad tunnel via Internet. Läs mer i [Punkt-till-plats-VPN](../vpn-gateway/point-to-site-about.md?toc=%2fazure%2fvirtual-network%2ftoc.json#).
- **Plats-till-plats-VPN:** Upprättas mellan den lokala VPN-enheten och Azure VPN Gateway som distribueras i ett virtuellt nätverk. Med den här anslutningstypen kan alla lokala resurser som du ger behörighet få åtkomst till ett virtuellt nätverk. Kommunikationen mellan den lokala VPN-enheten och en Azure VPN-gateway skickas genom en krypterad tunnel via Internet. Läs mer i [Plats-till-plats-VPN](../vpn-gateway/design.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti).
- **Azure ExpressRoute:** Upprättas mellan ditt nätverk och Azure, via en ExpressRoute-partner. Den här anslutningen är privat. Trafiken går inte via Internet. Läs mer i [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="filter-network-traffic"></a>Filtrera nätverkstrafik

Du kan filtrera nätverkstrafik mellan undernät med ett eller båda av följande alternativ:

- **Nätverks säkerhets grupper:** Nätverks säkerhets grupper och program säkerhets grupper kan innehålla flera inkommande och utgående säkerhets regler som gör att du kan filtrera trafik till och från resurser efter källans och målets IP-adress, port och protokoll. Mer information finns i [Nätverkssäkerhetsgrupper](./network-security-groups-overview.md#network-security-groups) eller [Programsäkerhetsgrupper](./network-security-groups-overview.md#application-security-groups).
- **Virtuella nätverksinstallationer:** En virtuell nätverksinstallation är en virtuell dator som utför en nätverksfunktion, till exempel en brandvägg, WAN-optimering eller annan nätverksfunktion. En lista över tillgängliga virtuella nätverksinstallationer som du kan distribuera i ett virtuellt nätverk finns i [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances).

### <a name="route-network-traffic"></a>Dirigera nätverkstrafik

Azure dirigerar trafik mellan undernät, anslutna virtuella nätverk, lokala nätverk och Internet, som standard. Du kan implementera ett eller båda av följande alternativ för att åsidosätta de standardvägar som Azure skapar:

- **Routningstabeller:** Du kan skapa anpassade routningstabeller med vägar som styr vart trafik dirigeras för varje undernät. Läs mer om [routningstabeller](virtual-networks-udr-overview.md#user-defined).
- **BGP-vägar (Border Gateway Protocol):** Om du ansluter ditt virtuella nätverk till ditt lokala nätverk med hjälp av en Azure VPN Gateway- eller ExpressRoute-anslutning kan du sprida de lokala BGP-vägarna till de virtuella nätverken. Lär dig mer om hur du använder BGP med [Azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) och [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#dynamic-route-exchange).

### <a name="virtual-network-integration-for-azure-services"></a>Integrering av virtuella nätverk för Azure-tjänster

Integrering av Azure-tjänster på ett virtuellt Azure-nätverk möjliggör privat åtkomst till tjänsten från virtuella datorer eller beräknings resurser i det virtuella nätverket.
Du kan integrera Azure-tjänster i ditt virtuella nätverk med följande alternativ:
- Distribuera [dedikerade instanser av tjänsten](virtual-network-for-azure-services.md) till ett virtuellt nätverk. Tjänsterna kan sedan nås privat i det virtuella nätverket och från lokala nätverk.
- Använd [privat länk](../private-link/private-link-overview.md) för att få åtkomst till privat en speciell instans av tjänsten från det virtuella nätverket och från lokala nätverk.
- Du kan också komma åt tjänsten med offentliga slut punkter genom att utöka ett virtuellt nätverk till tjänsten via [tjänstens slut punkter](virtual-network-service-endpoints-overview.md). Med tjänst slut punkter kan tjänst resurser skyddas till det virtuella nätverket.
 

## <a name="azure-vnet-limits"></a>Azure VNet-gränser

Det finns vissa begränsningar kring antalet Azure-resurser som du kan distribuera. De flesta gränser för Azure-nätverk har högsta tillåtna värden. Du kan dock [öka vissa nätverks gränser](../azure-portal/supportability/networking-quota-requests.md) enligt vad som anges på [sidan VNet-gränser](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits). 

## <a name="pricing"></a>Priser

Det kostar inget att använda Azure VNet, utan kostnad. Standard avgifterna gäller för resurser, till exempel Virtual Machines (VM) och andra produkter. Mer information finns i avsnittet om [priser för VNet](https://azure.microsoft.com/pricing/details/virtual-network/) och [pris kalkylatorn](https://azure.microsoft.com/pricing/calculator/)för Azure.

## <a name="next-steps"></a>Nästa steg
 - Lär dig mer om [Azure Virtual Network-koncept och bästa praxis](concepts-and-best-practices.md) .
 - Kom igång med ett virtuellt nätverk genom att skapa ett, distribuera några virtuella datorer till det och kommunicera mellan de virtuella datorerna. Mer information finns i snabbstarten [Skapa ett virtuellt nätverk](quick-create-portal.md).
