---
title: Översikt över Azures nätverkstjänster
description: Lär dig om nätverks tjänster i Azure, inklusive anslutning, program skydd, program leverans och tjänster för nätverks övervakning.
services: networking
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/07/2021
ms.author: kumud
ms.openlocfilehash: 34e31bacbb276feb5b11d1b955096877376e989b
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107711"
---
# <a name="azure-networking-services-overview"></a>Översikt över Azures nätverkstjänster

Nätverks tjänsterna i Azure tillhandahåller en mängd nätverksfunktioner som kan användas tillsammans eller separat. Klicka på någon av följande viktiga funktioner för att lära dig mer om dem:
- [**Anslutnings tjänster**](#connect): Anslut Azure-resurser och lokala resurser med valfri eller en kombination av dessa nätverks tjänster i Azure-Virtual Network (VNet), virtuellt WAN, ExpressRoute, VPN gateway, NAT-gateway för virtuella nätverk, Azure DNS, peering-tjänst och Azure skydds.
- [**Program skydds tjänster**](#protect): skydda dina program med hjälp av en eller flera kombinationer av dessa nätverks tjänster i Azure-Load Balancer, privat länk, DDoS skydd, brand vägg, nätverks säkerhets grupper, brand vägg för webbaserade program och Virtual Network slut punkter.
- [**Program leverans tjänster**](#deliver): leverera program i Azure-nätverket med valfri eller en kombination av dessa nätverks tjänster i azure-Content Delivery Network (CDN), Azure-tjänsten för front dörr, Traffic Manager, Application Gateway, Internet analys och Load Balancer.
- [**Nätverks övervakning**](#monitor): övervaka dina nätverks resurser med valfri eller en kombination av dessa nätverks tjänster i Azure-Network Watcher, ExpressRoute-övervakare, Azure Monitor eller VNet Terminal Access Point (Knacka).

## <a name="connectivity-services"></a><a name="connect"></a>Anslutningstjänster
 
I det här avsnittet beskrivs tjänster som ger anslutning mellan Azure-resurser, anslutning från ett lokalt nätverk till Azure-resurser och filial till gren anslutning i Azure-Virtual Network (VNet), ExpressRoute, VPN Gateway, virtuellt WAN, virtuell nätverks-NAT-gateway, Azure DNS, Azure peering-tjänsten och Azure skydds.


### <a name="virtual-network"></a><a name="vnet"></a>Virtuellt nätverk

Azure Virtual Network (VNet) är det grundläggande Bygg blocket för ditt privata nätverk i Azure. Du kan använda en virtuella nätverk för att:
- **Kommunicera mellan Azure-resurser**: du kan distribuera virtuella datorer och flera andra typer av Azure-resurser till ett virtuellt nätverk, till exempel Azure App Service miljöer, Azure Kubernetes service (AKS) och Azure Virtual Machine Scale Sets. En fullständig lista över Azure-resurser som du kan distribuera till ett virtuellt nätverk finns i [Tjänstintegration för virtuella nätverk](../../virtual-network/virtual-network-for-azure-services.md).
- **Kommunicera mellan varandra**: du kan ansluta virtuella nätverk till varandra, vilket gör det möjligt för resurser i det virtuella nätverket att kommunicera med varandra, med hjälp av peering av virtuella nätverk. De virtuella nätverken du ansluter kan finnas i samma, eller olika, Azure-regioner. Mer information finns i [peering för virtuella nätverk](../../virtual-network/virtual-network-peering-overview.md).
- **Kommunicera med Internet**: alla resurser i ett VNet kan som standard kommunicera utgående till Internet. Du kan kommunicera inkommande trafik till en resurs genom att tilldela en offentlig IP-adress eller en offentlig lastbalanserare. Du kan också använda [offentliga IP-adresser](../../virtual-network/virtual-network-public-ip-address.md) eller offentliga [Load Balancer](../../load-balancer/load-balancer-overview.md) för att hantera dina utgående anslutningar.
- **Kommunicera med lokala nätverk**: du kan ansluta dina lokala datorer och nätverk till ett virtuellt nätverk med hjälp av [VPN gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) eller [ExpressRoute](../../expressroute/expressroute-introduction.md).

Mer information finns i [Vad är Azure Virtual Network?](../../virtual-network/virtual-networks-overview.md).

### <a name="expressroute"></a><a name="expressroute"></a>ExpressRoute
Med ExpressRoute kan du utöka dina lokala nätverk till Microsoft-molnet via en privat anslutning som under lättas av en anslutnings leverantör. Den här anslutningen är privat. Trafiken går inte via Internet. Med ExpressRoute kan du upprätta anslutningar till Microsofts molntjänster som Microsoft Azure, Microsoft 365 och Dynamics 365.  Mer information finns i [Vad är ExpressRoute?](../../expressroute/expressroute-introduction.md).

:::image type="content" source="./media/networking-overview/expressroute-connection-overview.png" alt-text="Azure ExpressRoute" border="false":::

### <a name="vpn-gateway"></a><a name="vpngateway"></a>VPN Gateway
VPN Gateway hjälper dig att skapa krypterade anslutningar mellan olika platser till ditt virtuella nätverk från lokala platser eller skapa krypterade anslutningar mellan virtuella nätverk. Det finns olika konfigurationer som är tillgängliga för VPN Gateway anslutningar, till exempel plats-till-plats, punkt-till-plats eller VNet-till-VNet.
Följande diagram illustrerar flera plats-till-plats-VPN-anslutningar till samma virtuella nätverk.

:::image type="content" source="./media/networking-overview/vpngateway-multisite-connection-diagram.png" alt-text="Azure VPN Gateway-anslutningar från plats till plats":::

Mer information om olika typer av VPN-anslutningar finns [VPN gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="virtual-wan"></a><a name="virtualwan"></a>Virtuellt WAN
Azure Virtual WAN är en nätverks tjänst som tillhandahåller optimerad och automatiserad gren anslutning till och via Azure. Azure-regioner fungerar som hubbar som du kan välja att ansluta dina grenar till. Du kan använda Azures stamnät för att även ansluta grenar och dra nytta av anslutningar från förgrening till VNet. Azure Virtual WAN ger tillsammans många Azure-tjänster för moln anslutning, till exempel VPN för plats till plats, ExpressRoute, punkt-till-plats-VPN för användare i ett enda drift gränssnitt. Anslutning till Azure-virtuella nätverk upprättas med hjälp av virtuella nätverks anslutningar. Mer information finns i [Vad är Azure Virtual WAN?](../../virtual-wan/virtual-wan-about.md).

:::image type="content" source="./media/networking-overview/virtualwan1.png" alt-text="Virtual WAN-diagram":::

### <a name="azure-dns"></a><a name="dns"></a>Azure DNS
Azure DNS är en värdtjänst för DNS-domäner som tillhandahåller namnmatchning med hjälp av Microsoft Azure-infrastrukturen. Genom att använda Azure som värd för dina domäner kan du hantera dina DNS-poster med hjälp av samma autentiseringsuppgifter, API:er, verktyg och fakturering som för dina andra Azure-tjänster. Mer information finns i [Vad är Azure DNS?](../../dns/dns-overview.md).

### <a name="azure-bastion"></a><a name="bastion"></a>Azure Bastion
Azure skydds-tjänsten är en ny helt plattforms oberoende PaaS-tjänst som du etablerar i det virtuella nätverket. Den ger säker och sömlös RDP/SSH-anslutning till dina virtuella datorer direkt i Azure Portal via TLS. När du ansluter via Azure Bastion behöver dina virtuella datorer inte någon offentlig IP-adress. Mer information finns i [Vad är Azure skydds?](../../bastion/bastion-overview.md).

:::image type="content" source="./media/networking-overview/architecture.png" alt-text="Azure skydds-arkitektur":::

### <a name="virtual-network-nat-gateway"></a><a name="nat"></a>NAT-gateway för virtuellt nätverk
Virtual Network NAT (Network Address Translation) fören klar endast utgående Internet-anslutning för virtuella nätverk. När den konfigureras i ett undernät använder all utgående anslutning dina angivna statiska offentliga IP-adresser. Utgående anslutningar är möjlig utan belastningsutjämnare eller offentliga IP-adresser som är direkt anslutna till virtuella datorer. Mer information finns i [Vad är NAT-gateway för virtuella nätverk?](../../virtual-network/nat-overview.md).

:::image type="content" source="./media/networking-overview/flow-map.png" alt-text="NAT-gateway för virtuellt nätverk":::

### <a name="azure-peering-service"></a><a name="azurepeeringservice"></a> Azure-peering-tjänst
Azure peering-tjänsten ger bättre kund anslutning till Microsofts moln tjänster, till exempel Microsoft 365, Dynamics 365, SaaS-tjänster (Software as a Service), Azure eller Microsoft-tjänster som är tillgängliga via det offentliga Internet. Mer information finns i [Vad är Azure peering-tjänsten?](../../peering-service/about.md).

### <a name="azure-edge-zones"></a><a name="edge-zones"></a>Azure Edge Zones

Azure Edge Zone är en familje med erbjudanden från Microsoft Azure som möjliggör data behandling nära användaren. Du kan distribuera virtuella datorer, behållare och andra valda Azure-tjänster i gräns zoner för att hantera program med låg latens och höga data flödes krav.

### <a name="azure-orbital"></a><a name="orbital"></a>Azure-orbital

Azure orbital är en helt hanterad molnbaserad jord station som en tjänst som gör att du kan kommunicera med din rymder eller satellit-Constellations, Downlink och överordnad information, bearbeta dina data i molnet, kedja tjänster med Azure-tjänster i unika scenarier och skapa produkter för dina kunder. Det här systemet bygger på den globala Azure-infrastrukturen och det globala fiber nätverket med låg latens.

## <a name="application-protection-services"></a><a name="protect"></a>Program skydds tjänster

I det här avsnittet beskrivs nätverks tjänster i Azure som hjälper dig att skydda dina nätverks resurser – skydda dina program med hjälp av någon eller en kombination av dessa nätverks tjänster i Azure-DDoS-skydd, privat länk, brand vägg, brand vägg för webbaserade program, nätverks säkerhets grupper och Virtual Network tjänst slut punkter.

### <a name="ddos-protection"></a><a name="ddosprotection"></a>DDoS Protection 
[Azure DDoS Protection](../../ddos-protection/manage-ddos-protection.md) tillhandahåller motåtgärder mot de mest sofistikerade DDoS-hoten. Tjänsten tillhandahåller förbättrade funktioner för DDoS-minskning för ditt program och resurser som distribueras i dina virtuella nätverk. Dessutom kan kunder som använder Azure DDoS Protection ha till gång till DDoS Rapid Response-support för att engagera DDoS-experter under ett aktivt angrepp.

:::image type="content" source="./media/networking-overview/ddos-protection.png" alt-text="DDoS Protection":::

### <a name="azure-private-link"></a><a name="privatelink"></a>Azure Private Link
Med [Azures privata länk](../../private-link/private-link-overview.md) kan du få åtkomst till Azure PaaS-tjänster (till exempel Azure Storage och SQL Database) och Azure-värdbaserade/partner tjänster som ägs av en privat slut punkt i det virtuella nätverket.
Trafik mellan ditt virtuella nätverk och tjänsten flyttar Microsoft stamnät nätverket. Det är inte längre nödvändigt att exponera tjänsten för det offentliga Internet. Du kan skapa en egen privat länk-tjänst i ditt virtuella nätverk och leverera den till dina kunder.

:::image type="content" source="./media/networking-overview/private-endpoint.png" alt-text="Översikt över privat slut punkt":::

### <a name="azure-firewall"></a><a name="firewall"></a>Azure Firewall
Azure Firewall är en hanterad, molnbaserad tjänst för nätverkssäkerhet som skyddar dina Azure Virtual Network-resurser. Med hjälp av Azure Firewall kan du centralt skapa, tillämpa och logga program-och nätverks anslutnings principer över prenumerationer och virtuella nätverk. Azure Firewall använder en statisk offentlig IP-adress för din virtuella nätverksresurser som tillåter att externa brandväggar identifierar trafik som kommer från ditt virtuella nätverk. 

Mer information om Azure-brandväggen finns i [dokumentationen för Azure-brandväggen](../../firewall/overview.md).

:::image type="content" source="./media/networking-overview/firewall-threat.png" alt-text="Översikt över brandväggar":::

### <a name="web-application-firewall"></a><a name="waf"></a>Brandvägg för webbaserade program
[Azure brand vägg](../../web-application-firewall/overview.md) för webbaserade program (WAF) ger skydd för dina webb program från vanliga webbutnyttjande och sårbarheter som SQL-inmatning och Cross Site Scripting. Azure WAF tillhandahåller direkt skydd från OWASP de 10 viktigaste säkerhets problemen via hanterade regler. Dessutom kan kunderna också konfigurera anpassade regler, som är kund hanterade regler för att ge ytterligare skydd baserat på käll-IP-intervall och begära attribut, till exempel huvuden, cookies, formulär data fält eller frågesträngs parametrar.

Kunderna kan välja att distribuera [Azure-WAF med Application Gateway](../../web-application-firewall/ag/ag-overview.md) som tillhandahåller regionalt skydd till entiteter i offentligt och privat adress utrymme. Kunder kan också välja att distribuera [Azure-WAF med en front dörr](../../web-application-firewall/afds/afds-overview.md) som ger skydd på nätverks sidan till offentliga slut punkter.

:::image type="content" source="./media/networking-overview/waf-overview.png" alt-text="Brandvägg för webbaserade program":::

### <a name="network-security-groups"></a><a name="nsg"></a>Nätverks säkerhets grupper
Du kan filtrera nätverkstrafik till och från Azure-resurser i ett virtuellt nätverk i Azure med en nätverkssäkerhetsgrupp. Mer information finns i [nätverks säkerhets grupper](../../virtual-network/network-security-groups-overview.md).

### <a name="service-endpoints"></a><a name="serviceendpoints"></a>Tjänstslutpunkter
Med tjänstslutpunkter för Virtual Network (VNet) får du ett utökat privat adressutrymme för det virtuella nätverket och identiteten för ditt VNet till Azure-tjänsterna, via en direktanslutning. Med slutpunkter kan du skydda dina kritiska Azure-tjänstresurser till endast dina virtuella nätverk. Trafik från ditt VNet till Azure-tjänsten förblir alltid på Microsoft Azure-stamnätverket. Mer information finns i [tjänst slut punkter för virtuella nätverk](../../virtual-network/virtual-network-service-endpoints-overview.md).

:::image type="content" source="./media/networking-overview/vnet-service-endpoints-overview.png" alt-text="Tjänstslutpunkter för virtuellt nätverk":::

## <a name="application-delivery-services"></a><a name="deliver"></a>Program leverans tjänster

I det här avsnittet beskrivs nätverks tjänster i Azure som hjälper dig att leverera program Content Delivery Network, Azure-tjänsten för front dörr, Traffic Manager, Load Balancer och Application Gateway.

### <a name="content-delivery-network"></a><a name="cdn"></a>Content Delivery Network
Med CDN får utvecklare en global lösning för snabb innehållsleverans med hög bandbredd till användarna eftersom innehållet cachelagras på fysiska noder på strategiska platser runt om i världen. Mer information om Azure CDN finns i [Azure Content Delivery Network](../../cdn/cdn-overview.md).

:::image type="content" source="./media/networking-overview/cdn-overview.png" alt-text="Azure CDN":::

### <a name="azure-front-door-service"></a><a name="frontdoor"></a>Azure Front Door Service
Med Azure Front Door Service kan du definiera, hantera och övervaka global routning av din webbtrafik, genom att optimera för bästa prestanda och omedelbar global redundans för hög tillgänglighet. Med Front Door kan du transformera dina globala (för flera regioner) konsument- och företagsprogram till robusta, högpresterande och anpassade moderna program, API:er och innehåll som når en global publik med Azure. Mer information finns i [Azures front dörr](../../frontdoor/front-door-overview.md).

:::image type="content" source="./media/networking-overview/front-door-visual-diagram.png" alt-text="Översikt över front dörrs tjänsten":::

### <a name="traffic-manager"></a><a name="trafficmanager"></a>Traffic Manager

Azure Traffic Manager är en lastbalanserare för DNS-baserad trafik som hjälper dig att distribuera trafiken optimalt till tjänster i globala Azure-regioner, med hög tillgänglighet och korta svarstider. Traffic Manager tillhandahåller en mängd metoder för trafik cirkulation för att distribuera trafik som prioritet, viktat, prestanda, geografiskt, flera värden eller undernät. Mer information om metoder för trafikroutning finns i [Traffic Manager metoder för routning](../../traffic-manager/traffic-manager-routing-methods.md).

Följande diagram visar den slut punkts prioritetbaserade routningen med Traffic Manager:

:::image type="content" source="./media/networking-overview/priority.png" alt-text="Traffic-routningsmetod för Azure Traffic Manager":::

Mer information om Traffic Manager finns i [Vad är Azure Traffic Manager?](../../traffic-manager/traffic-manager-overview.md)

### <a name="load-balancer"></a><a name="loadbalancer"></a>Load Balancer
Azure Load Balancer ger hög prestanda, låg latens Layer 4-belastnings utjämning för alla UDP-och TCP-protokoll. Den hanterar inkommande och utgående anslutningar. Du kan konfigurera offentliga och interna belastningsutjämnade slut punkter. Du kan definiera regler för att mappa inkommande anslutningar till backend-platser genom att använda TCP-och HTTP-hälsohälso-/Bing-alternativ för att hantera tjänstens tillgänglighet. Läs mer om Load Balancer i artikeln [Load Balancer översikt](../../load-balancer/load-balancer-overview.md) .

Följande bild visar ett Internet-riktat program på flera nivåer som använder både externa och interna belastningsutjämnare:

:::image type="content" source="./media/networking-overview/load-balancer.png" alt-text="Azure Load Balancer exempel":::

### <a name="application-gateway"></a><a name="applicationgateway"></a>Application Gateway
Azure Application Gateway är en lastbalanserare för webbtrafik som gör det möjligt för dig att hantera trafik till dina webbappar. Det är en program leverans kontroll (ADC) som en tjänst som erbjuder olika Layer 7 belastnings Utjämnings funktioner för dina program. Mer information finns i [Vad är Azure Application Gateway?](../../application-gateway/overview.md).

Följande diagram visar URL Path-baserad routning med Application Gateway.

:::image type="content" source="./media/networking-overview/figure1-720.png" alt-text="Application Gateway exempel":::

## <a name="network-monitoring-services"></a><a name="monitor"></a>Tjänster för nätverks övervakning
I det här avsnittet beskrivs nätverks tjänster i Azure som hjälper dig att övervaka dina nätverks resurser – Network Watcher, Azure Monitor nätverks insikter, Azure Monitor, ExpressRoute-övervakaren och Virtual Network trycker.

### <a name="network-watcher"></a><a name="networkwatcher"></a>Network Watcher
I Azure Network Watcher finns verktyg för att övervaka, diagnostisera, visa mått samt aktivera eller inaktivera loggar för resurser i ett virtuellt Azure-nätverk. Mer information finns i [Vad är Network Watcher?](../../network-watcher/network-watcher-monitoring-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).

### <a name="azure-monitor-network-insights"></a>Azure Monitor nätverks insikter
Azure Monitor för nätverk ger en omfattande vy över hälsa och mått för alla distribuerade nätverks resurser, utan att kräva någon konfiguration. Den ger även till gång till funktioner för nätverks övervakning, t. ex. [anslutnings övervakaren](../../network-watcher/connection-monitor-overview.md), [flödes loggning för nätverks säkerhets grupper](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)och [trafikanalys](../../network-watcher/traffic-analytics.md). Mer information finns i [Azure Monitor Network Insights](../../azure-monitor/insights/network-insights-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).

### <a name="expressroute-monitor"></a><a name="expressroutemonitor"></a>Övervakaren ExpressRoute
Information om hur du visar ExpressRoute krets mått, resurs loggar och aviseringar finns i [ExpressRoute övervakning, mått och aviseringar](../../expressroute/expressroute-monitoring-metrics-alerts.md?toc=%2fazure%2fnetworking%2ftoc.json).
### <a name="azure-monitor"></a><a name="azuremonitor"></a>Azure Monitor
Azure Monitor maximerar programmens tillgänglighet och prestanda genom att leverera en heltäckande lösning för att samla in, analysera och arbeta med telemetri från dina molnmiljöer och lokala miljöer. Det hjälper dig att förstå hur dina program fungerar och identifierar proaktivt problem som påverkar dem och de resurser som de förlitar sig på. Mer information finns i [Azure Monitor översikt](../../azure-monitor/overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
### <a name="virtual-network-tap"></a><a name="vnettap"></a>Virtual Network trycka på
Med hjälp av Azure Virtual Network-TRYCKNINGen (Terminal Access Point) kan du kontinuerligt strömma din virtuella dators nätverks trafik till en insamlare eller ett analys verktyg för nätverks paket. Insamlings-eller analys verktyget tillhandahålls av en [virtuell nätverks utrustnings](https://azure.microsoft.com/solutions/network-appliances/) partner.

Följande bild visar hur det virtuella nätverket trycker på Works:

:::image type="content" source="./media/networking-overview/virtual-network-tap-architecture.png" alt-text="Hur det virtuella nätverket trycker på Works":::

Mer information finns i [Vad är Virtual Network tryck](../../virtual-network/virtual-network-tap-overview.md).

## <a name="next-steps"></a>Nästa steg

- Skapa ditt första virtuella nätverk och Anslut några virtuella datorer till det genom att slutföra stegen i artikeln [skapa ditt första virtuella nätverk](../../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) .
- Anslut datorn till ett virtuellt nätverk genom att följa stegen i [artikeln Konfigurera en punkt-till-plats-anslutning](../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json).
- Belastningsutjämna Internet trafik till offentliga servrar genom att slutföra stegen i artikeln [skapa en Internetbaserad belastningsutjämnare](../../load-balancer/quickstart-load-balancer-standard-public-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) .