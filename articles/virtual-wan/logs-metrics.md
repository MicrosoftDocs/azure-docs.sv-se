---
title: Loggar och mått
titleSuffix: Azure Virtual WAN
description: Lär dig mer om Azures virtuella WAN-loggar och mått
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 06/05/2020
ms.author: cherylmc
ms.openlocfilehash: 62979a2cbe2a5912476ca65327a06eef9c36c1cb
ms.sourcegitcommit: 02b1179dff399c1aa3210b5b73bf805791d45ca2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98127897"
---
# <a name="azure-virtual-wan-logs-and-metrics"></a>Azure Virtual WAN-loggar och-mått

Du kan övervaka Azure Virtual WAN med Azure Monitor. Det virtuella WAN-nätverket är en nätverks tjänst som kombinerar många funktioner för nätverk, säkerhet och routning för att tillhandahålla ett enda drift gränssnitt. Virtuella WAN-gatewayer, ExpressRoute-gatewayer och Azure-brandväggen har loggning och mått som är tillgängliga via Azure Monitor.

Den här artikeln beskriver mått och diagnostik som är tillgängliga via portalen. Måtten är lätta att använda för att ge stöd för scenarier i nästan real tid, vilket gör dem användbara för avisering och snabb problem identifiering.

## <a name="monitoring-secured-hub-azure-firewall"></a>Övervaka säker hubb (Azure-brandvägg) 

Du kan övervaka den skyddade hubben med hjälp av Azure Firewall-loggar. Du kan också använda aktivitetsloggar till att granska åtgärder som utförs på Azure Firewall-resurser.

Om du har valt att skydda din virtuella hubb med Azure-brandväggen finns relevanta loggar och mått här: [Azure Firewall-loggar och-mått](../firewall/logs-and-metrics.md).

## <a name="metrics"></a>Mått

Mått i Azure Monitor är numeriska värden som beskriver viss aspekt av ett system vid en viss tidpunkt. Mått samlas in varje minut och är användbara för aviseringar eftersom de kan samplas ofta. En avisering kan utlösas snabbt med relativt enkel logik.

### <a name="site-to-site-vpn-gateways"></a>VPN-gatewayer för plats-till-plats

Följande mått är tillgängliga för Azure plats-till-plats VPN-gatewayer:

* **Gateway-bandbredd** – genomsnittlig plats-till-plats-aggregerad bandbredd för en gateway i byte per sekund.
* **Tunnel bandbredd** – genomsnittlig bandbredd för en tunnel i byte per sekund.
* Utgående **byte i tunnel** – utgående byte i en tunnel. 
* Utgående **paket för tunnel** – antal utgående paket för en tunnel. 
* **Tunnel utgång TS matchnings fel paket Drop** – utgående paket-utgående paket från trafik väljare matchnings fel för en tunnel. 
* **Tunnel** ingångs byte – inkommande byte i en tunnel. 
* **Tunnel** ingångs paket – antal inkommande paket för en tunnel. 
* Tunnel ingången **TS matchnings fel paket släpps** – inkommande paket ignorerar matchnings fel för en tunnel. 

### <a name="point-to-site-vpn-gateways"></a>Punkt-till-plats-VPN-gatewayer

Följande mått är tillgängliga för Azure punkt-till-plats VPN-gatewayer:

* **Gateway P2s bandbredd** – genomsnittlig punkt-till-plats sammanställd bandbredd för en gateway i byte per sekund.
* Antal **anslutningar för P2s** – punkt-till-plats-anslutning för en gateway.

### <a name="azure-expressroute-gateways"></a>Azure ExpressRoute-gatewayer

Följande mått är tillgängliga för Azure ExpressRoute-gatewayer:

* **BitsInPerSecond** – BITS inkommande Azure per sekund.
* **BitsOutPerSecond** – BITS som utgående Azure per sekund.

### <a name="view-gateway-metrics"></a><a name="metrics-steps"></a>Visa Gateway-mått

Följande steg hjälper dig att hitta och visa mått:

1. I portalen navigerar du till det virtuella hubb som har gatewayen.

2. Välj **VPN (plats till plats)** för att hitta en plats-till-plats-Gateway, **ExpressRoute** för att hitta en ExpressRoute-gateway eller **User VPN (peka på plats)** för att hitta en punkt-till-plats-Gateway. På sidan kan du se Gateway-informationen. Kopiera den här informationen. Du kommer att använda den senare för att Visa diagnostik med Azure Monitor.

3. Välj **Mått**.

   :::image type="content" source="./media/logs-metrics/metrics.png" alt-text="Skärm bild som visar en plats i fönstret V P N N med Visa i Azure Monitor valt.":::

4. På sidan **mått** kan du Visa de mått som du är intresse rad av.

   :::image type="content" source="./media/logs-metrics/metrics-page.png" alt-text="Skärm bild som visar sidan &quot;mått&quot; med de kategorier som är markerade.":::

## <a name="diagnostic-logs"></a><a name="diagnostic"></a>Diagnostikloggar

### <a name="site-to-site-vpn-gateways"></a>VPN-gatewayer för plats-till-plats

Följande diagnostik är tillgänglig för Azure plats-till-plats VPN-gatewayer:

* **Gateway-diagnostikloggar** – Gateway-speciell diagnostik, till exempel hälso tillstånd, konfiguration, tjänst uppdateringar, och ytterligare diagnostik.
* **Tunnel diagnostiska loggar** – dessa är IPSec-tunnel-relaterade loggar som Connect och från kopplings händelser för en plats-till-plats-IPSec-tunnel, förhandlad SAS, från kopplings skäl, samt ytterligare diagnostik.
* **Vidarebefordra diagnostikloggar** – dessa loggar som är relaterade till händelser för statiska vägar, BGP, väg uppdateringar och ytterligare diagnostik.
* **IKE-diagnostikloggar** – IKE-speciell diagnostik för IPSec-anslutningar.

### <a name="point-to-site-vpn-gateways"></a>Punkt-till-plats-VPN-gatewayer

Följande diagnostik är tillgänglig för Azure punkt-till-plats VPN-gatewayer:

* **Gateway-diagnostikloggar** – Gateway-speciell diagnostik, till exempel hälso tillstånd, konfiguration, tjänst uppdateringar, och annan diagnostik.
* **IKE-diagnostikloggar** – IKE-speciell diagnostik för IPSec-anslutningar.
* **P2s-diagnostikloggar** – dessa är användar-VPN (punkt-till-plats) P2s-konfiguration och klient händelser. De omfattar klient anslutning/från koppling, VPN-klientautentisering samt annan diagnostik.

### <a name="view-diagnostic-logs"></a><a name="diagnostic-steps"></a>Visa diagnostikloggar

Följande steg hjälper dig att hitta och Visa diagnostik:

1. I portalen navigerar du till din virtuella WAN-resurs. I avsnittet **Översikt** på sidan virtuellt WAN-nätverk i portalen väljer du **Essentials** för att expandera vyn och hämta information om resurs gruppen. Kopiera resurs grupps informationen.

   :::image type="content" source="./media/logs-metrics/3.png" alt-text="Skärm bild som visar avsnittet &quot;Översikt&quot; med en pil som pekar på knappen &quot;Kopiera&quot;.":::

2. I avsnittet övervakning, navigerar du till resurs gruppen. Välj **diagnostikinställningar** och ange sedan resurs informationen. Det här är den resursinformation som du kopierade i steg 2 från avsnittet [Visa Gateway-mått](#metrics-steps) tidigare i den här artikeln.

   :::image type="content" source="./media/logs-metrics/4.png" alt-text="Skärm bild som visar avsnittet &quot;övervakning&quot; med en pil som pekar på list rutan &quot;resurs&quot;.":::

3. På sidan resultat väljer du **+ Lägg till diagnostisk inställning** och väljer sedan ett alternativ. Du kan välja att skicka till Log Analytics, strömma till en händelsehubben eller att bara arkivera till ett lagrings konto.

   :::image type="content" source="./media/logs-metrics/5.png" alt-text="Sidan mått":::

### <a name="log-analytics-sample-query"></a><a name="sample-query"></a>Log Analytics exempel fråga

Loggarna finns i **Azure Log Analytics-arbetsytan**. Du kan ställa in en fråga i Log Analytics. I följande exempel finns en fråga för att hämta diagnostik för plats-till-plats-routning.

```AzureDiagnostics | where Category == "RouteDiagnosticLog"```

Ersätt värdena nedan efter **= =**, efter behov.

* "GatewayDiagnosticLog"
* "IKEDiagnosticLog"
* "P2SDiagnosticLog"
* "TunnelDiagnosticLog"
* "RouteDiagnosticLog"

## <a name="activity-logs"></a><a name="activity-logs"></a>Aktivitets loggar

**Aktivitets logg** poster samlas in som standard och kan visas i Azure Portal. Du kan använda Azure aktivitets loggar (tidigare kallade *drift loggar* och *gransknings loggar*) för att visa alla åtgärder som skickats till din Azure-prenumeration.

## <a name="next-steps"></a>Nästa steg

* Information om hur du övervakar Azure Firewall-loggar och-mått finns i [självstudie: övervaka Azure Firewall-loggar](../firewall/firewall-diagnostics.md).
* Mer information om mått i Azure Monitor finns i [mått i Azure Monitor](../azure-monitor/platform/data-platform-metrics.md).
