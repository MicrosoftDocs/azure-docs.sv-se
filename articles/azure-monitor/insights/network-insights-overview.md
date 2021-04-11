---
title: Azure Monitor för nätverk
description: En översikt över Azure Monitor för nätverk, som ger en omfattande vy över hälsa och mått för alla distribuerade nätverks resurser utan någon konfiguration.
ms.topic: conceptual
author: KumudD
ms.author: kumud
ms.date: 11/25/2020
ms.openlocfilehash: db94a376112b3d715da9095acb05c0b4fd4ea02f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104605808"
---
# <a name="azure-monitor-for-networks"></a>Azure Monitor för nätverk

Azure Monitor för nätverk ger en omfattande vy över [hälsa](../../service-health/resource-health-checks-resource-types.md) och [mått](../essentials/metrics-supported.md) för alla distribuerade nätverks resurser, utan att kräva någon konfiguration. Den ger även till gång till funktioner för nätverks övervakning, t. ex. [anslutnings övervakaren](../../network-watcher/connection-monitor-overview.md), [flödes loggning för nätverks säkerhets grupper (nsg: er)](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)och [trafikanalys](../../network-watcher/traffic-analytics.md). Och innehåller [andra funktioner för nätverksdiagnostik.](../../network-watcher/network-watcher-monitoring-overview.md#diagnostics)

Azure Monitor för nätverk är strukturerad kring dessa viktiga komponenter i övervakningen:
- [Nätverks hälsa och-mått](#networkhealth)
- [Anslutningsmöjligheter](#connectivity)
- [Trafik](#traffic)
- [Diagnostikverktyg](#diagnostictoolkit)

## <a name="network-health-and-metrics"></a><a name="networkhealth"></a>Nätverks hälsa och-mått

**Översikts** sidan för Azure Monitor för nätverk ger ett enkelt sätt att visualisera inventeringen av dina nätverks resurser, tillsammans med resurs hälsa och aviseringar. Den är uppdelad i fyra viktiga funktions områden: sökning och filtrering, resurs hälsa och mått, aviseringar och beroende vy.

[![Skärmbild som visar sidan Översikt](media/network-insights-overview/overview.png)](media/network-insights-overview/overview.png#lightbox)

### <a name="search-and-filtering"></a>Sökning och filtrering
Du kan anpassa vyn resurs hälsa och aviseringar med hjälp av filter som **prenumeration**, **resurs grupp** och **typ**.

Du kan använda sökrutan för att söka efter resurser och deras associerade resurser. Till exempel är en offentlig IP-adress associerad med en Application Gateway. En sökning efter den offentliga IP-adressens DNS-namn kommer att returnera både den offentliga IP-adressen och den associerade programgatewayen:

[![Skärm bild som visar Azure Monitor för nätverk Sök resultat.](media/network-insights-overview/search.png)](media/network-insights-overview/search.png#lightbox)


### <a name="resource-health-and-metrics"></a>Resurs hälsa och-mått
I följande exempel representerar varje panel en resurs typ. Panelen visar antalet instanser av den resurs typ som har distribuerats i alla valda prenumerationer. Den visar också resursens hälso status. I det här exemplet finns det 105 ER och VPN-anslutningar distribuerade. 103 är felfria och 2 är inte tillgängliga.

![Skärm bild som visar resurs hälsa och-mått i Azure Monitor för nätverk.](media/network-insights-overview/resource-health.png)

Om du väljer de otillgängliga återställnings-och VPN-anslutningarna visas en Metric-vy: 

![Skärm bild som visar Metric-vyn i Azure Monitor för nätverk.](media/network-insights-overview/metric-view.png)

Du kan välja valfritt objekt i diagramvyn. Välj ikonen i kolumnen **hälsa** för att få resurs hälsa för anslutningen. Välj värdet i kolumnen **avisering** för att gå till sidan aviseringar och mått för anslutningen. 

### <a name="alerts"></a>Aviseringar
**Aviserings** rutan till höger på sidan ger en översikt över alla aviseringar som har genererats för de valda resurserna i alla prenumerationer. Välj det antal aviseringar som ska gå till en detaljerad aviserings sida.

### <a name="dependency-view"></a>Beroende vy
Beroende vy hjälper dig att visualisera hur en resurs har kon figurer ATS. Beroende vy är för närvarande tillgänglig för Azure Application Gateway, Azure Virtual WAN och Azure Load Balancer. För Application Gateway kan du till exempel komma åt beroende vy genom att välja Application Gateway resurs namnet i vyn mått rutnät. Du kan göra samma sak för virtuella WAN-och Load Balancer.

![Sreenshot som visar Application Gateways visning i Azure Monitor för nätverk.](media/network-insights-overview/application-gateway.png)

Beroende vyn för Application Gateway ger en förenklad vy över hur klient delens IP-adresser är anslutna till lyssnare, regler och backend-pool. De anslutande linjerna är färgkodade och ger ytterligare information baserat på Server delens hälso tillstånd. Vyn innehåller också en detaljerad vy över Application Gateway mått och mått för alla relaterade Server dels pooler, till exempel virtuell dators skalnings uppsättning och VM-instanser.

[![Skärm bild som visar beroende visning i Azure Monitor för nätverk.](media/network-insights-overview/dependency-view.png)](media/network-insights-overview/dependency-view.png#lightbox)

Beroende diagrammet ger enkel navigering till konfigurations inställningar. Högerklicka på en backend-pool för att få åtkomst till annan information. Om till exempel en VM-pool är en virtuell dator kan du direkt komma åt VM-insikter och fel sökning av Azure Network Watcher-anslutning för att identifiera anslutnings problem:

![Skärm bild som visar menyn beroende vy i Azure Monitor för nätverk.](media/network-insights-overview/dependency-view-menu.png)

Sök-och filter fältet i beroende vyn är ett enkelt sätt att söka igenom grafen. Om du till exempel söker efter **AppGWTestRule** i föregående exempel, kommer vyn att skalas ned till alla noder som är anslutna via AppGWTestRule:

![Skärm bild som visar ett exempel på en sökning i Azure Monitor för nätverk.](media/network-insights-overview/search-example.png)

Med olika filter kan du skala ned till en angiven sökväg och tillstånd. Välj till exempel endast **ohälsosamt** i listan **hälso status** om du vill visa alla kanter där tillståndet är skadat.

Välj **Visa detaljerade mått** för att öppna en förkonfigurerad arbets bok som tillhandahåller detaljerade mått för programgatewayen, alla resurser för Server dels poolen och klient-IP-adresser. 

## <a name="connectivity"></a><a name="connectivity"></a>Anslutning

Fliken **anslutning** är ett enkelt sätt att visualisera alla tester som kon figurer ATS via [anslutnings övervakaren](../../network-watcher/connection-monitor-overview.md) och anslutnings övervakaren (klassisk) för den valda uppsättningen prenumerationer.

![Skärm bild som visar fliken anslutning i Azure Monitor för nätverk.](media/network-insights-overview/azure-monitor-for-networks-connectivity-tab.png)

Test grupperas efter **paneler** och **destinationer** och visar tillgänglighets status för varje test. Tillgängliga inställningar ger enkel åtkomst till konfigurationer för dina kriterier för tillgänglighet, baserat på misslyckade kontroller (%) och Sökefter (MS). När du har angett värden måste status för varje test uppdateras utifrån urvalskriterierna.

[![Skärm bild som visar anslutnings test i Azure Monitor för nätverk.](media/network-insights-overview/azure-monitor-for-networks-connectivity-tests.png)](media/network-insights-overview/azure-monitor-for-networks-connectivity-tests.png#lightbox)

Du kan välja valfri käll-eller mål panel för att öppna en Metric-vy:

[![Skärm bild som visar anslutnings mått i Azure Monitor för nätverk.](media/network-insights-overview/azure-monitor-for-networks-connectivity-metrics.png)](media/network-insights-overview/azure-monitor-for-networks-connectivity-metrics.png#lightbox)


Du kan välja valfritt objekt i diagramvyn. Välj ikonen i kolumnen för **tillgänglighet** för att gå till Portal sidan anslutnings övervakare och Visa hopp för hopp-topologin och anslutning som påverkar problem som identifieras. Välj värdet i kolumnen **avisering** för att gå till aviseringar. Markera graferna i kolumnerna **kontrollerna misslyckades procent** och **tur och retur tid (MS)** för att gå till sidan mått för den valda anslutnings övervakaren.

 **Aviserings** rutan till höger på sidan ger en översikt över alla aviseringar som har genererats för de anslutnings test som kon figurer ATS i alla prenumerationer. Välj det antal aviseringar som ska gå till en detaljerad aviserings sida.

## <a name="traffic"></a><a name="traffic"></a>Trafik
Fliken **trafik** ger åtkomst till alla NSG: er som kon figurer ATS för [NSG flödes loggar](../../network-watcher/network-watcher-nsg-flow-logging-overview.md) och [trafikanalys](../../network-watcher/traffic-analytics.md) för den valda uppsättningen prenumerationer, grupperat efter plats. Med Sök funktionen på den här fliken kan du identifiera NSG: er som kon figurer ATS för den genomsökta IP-adressen. Du kan söka efter alla IP-adresser i din miljö. I den regionala vyn i vyn visas alla NSG: er tillsammans med NSG flödes loggar och Trafikanalys konfigurations status.

[![Skärm bild som visar fliken trafik i Azure Monitor för nätverk.](media/network-insights-overview/azure-monitor-for-networks-traffic-view.png)](media/network-insights-overview/azure-monitor-for-networks-traffic-view.png#lightbox)

Om du väljer en region panel visas en rutnätsvy. Rutnätet innehåller NSG flödes loggar och Trafikanalys i en vy som är lätt att läsa och konfigurera:  

[![Skärm bild som visar vyn trafik region i Azure Monitor för nätverk.](media/network-insights-overview/azure-monitor-for-networks-traffic-region-view.png)](media/network-insights-overview/azure-monitor-for-networks-traffic-region-view.png#lightbox)

Du kan välja valfritt objekt i diagramvyn. Välj ikonen i kolumnen **konfigurations status för logg** för att redigera NSG Flow-loggen och trafikanalys konfiguration. Välj värdet i kolumnen **avisering** för att gå till trafik aviseringarna som kon figurer ATS för den valda NSG. På samma sätt kan du gå till Trafikanalys visning genom att välja **arbets ytan trafikanalys**.  

 **Aviserings** rutan till höger på sidan ger en översikt över alla trafikanalys arbets ytans baserade aviseringar över alla prenumerationer. Välj det antal aviseringar som ska gå till en detaljerad aviserings sida.

## <a name="diagnostic-toolkit"></a><a name="diagnostictoolkit"></a> Diagnostikverktyg
Diagnostic Toolkit ger till gång till alla diagnostiska funktioner som är tillgängliga för fel sökning av nätverket. Du kan använda den här List rutan för att få åtkomst till funktioner som [paket avbildning](../../network-watcher/network-watcher-packet-capture-overview.md), [VPN-felsökning](../../network-watcher/network-watcher-troubleshoot-overview.md), [anslutnings fel sökning](../../network-watcher/network-watcher-connectivity-overview.md), [nästa hopp](../../network-watcher/network-watcher-next-hop-overview.md)och [kontrol lera IP-flödet](../../network-watcher/network-watcher-ip-flow-verify-overview.md):

![Skärm bild som visar fliken Diagnostic Toolkit.](media/network-insights-overview/azure-monitor-for-networks-diagnostic-toolkit.png)

## <a name="onboarded-resources"></a>Inbyggda resurser 

Inbyggda resurser har inbyggda arbets böcker och beroende vyer. För tillfället inbyggda resurser är virtuella WAN, Application Gateway, Load Balancer och ExpressRoute.

## <a name="troubleshooting"></a>Felsökning 
Allmän fel söknings vägledning finns i [artikeln](troubleshoot-workbooks.md)om dedikerad insikter om arbets böcker-baserade insikter.
Det här avsnittet hjälper dig att diagnostisera och felsöka vanliga problem som kan uppstå när du använder Azure Monitor för nätverk. 

### <a name="how-do-i-resolve-performance-problems-or-failures"></a>Hur gör jag för att lösa prestanda problem eller fel?

Information om hur du felsöker eventuella nätverksrelaterade problem som du identifierar med Azure Monitor för nätverk finns i fel söknings dokumentationen för fel söknings resurser. 

Här följer några länkar till fel söknings artiklar för tjänster som används ofta. Mer felsöknings artiklar om dessa tjänster finns i de andra artiklarna i fel söknings avsnittet i innehålls förteckningen för tjänsten.
* [Azure Virtual Network](../../virtual-network/virtual-network-troubleshoot-peering-issues.md)
* [Azure Application Gateway](../../application-gateway/create-gateway-internal-load-balancer-app-service-environment.md)
* [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-troubleshoot.md)
* [Azure ExpressRoute](../../expressroute/expressroute-troubleshooting-expressroute-overview.md) 
* [Azure Load Balancer](../../load-balancer/load-balancer-troubleshoot.md) 

### <a name="why-dont-i-see-the-resources-for-all-the-subscriptions-ive-selected"></a>Varför visas inte resurserna för alla prenumerationer jag har valt?

Azure Monitor för nätverk kan bara visa resurser för fem prenumerationer åt gången. 

### <a name="how-do-i-make-changes-or-add-visualizations-to-azure-monitor-for-networks"></a>Hur gör jag för att göra ändringar eller lägga till visualiseringar i Azure Monitor för nätverk?

Om du vill göra ändringar väljer du **redigerings läge** för att ändra arbets boken. Du kan sedan spara ändringarna som en ny arbets bok som är kopplad till en angiven prenumeration och resurs grupp.

### <a name="whats-the-time-grain-after-i-pin-any-part-of-the-workbooks"></a>Vad är tids kornig het efter att jag har fäst någon del av arbets böckerna?

Azure Monitor för nätverk använder **Automatisk** tids kornig het, så tids kornig het baseras på det valda tidsintervallet.

### <a name="whats-the-time-range-when-any-part-of-a-workbook-is-pinned"></a>Vad är tidsintervallet när någon del av en arbets bok är fäst?

Tidsintervallet beror på instrument panelens inställningar.

### <a name="what-if-i-want-to-see-other-data-or-make-my-own-visualizations-how-can-i-make-changes-to-azure-monitor-for-networks"></a>Vad händer om jag vill se andra data eller göra egna visualiseringar? Hur kan jag göra ändringar i Azure Monitor för nätverk?

Du kan redigera arbets boken som du ser i valfri sida-eller detaljerad Metric-vy med redigerings läget. Du kan sedan spara ändringarna som en ny arbets bok.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om nätverks övervakning: [Vad är Azure Network Watcher?](../../network-watcher/network-watcher-monitoring-overview.md)
- Lär dig mer om arbets böckerna i scenarier är utformade för att stödja, skapa rapporter och anpassa befintliga rapporter med mera: [skapa interaktiva rapporter med Azure Monitor arbets böcker](../visualize/workbooks-overview.md)
