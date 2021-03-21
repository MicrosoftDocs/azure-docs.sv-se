---
title: Övervakare av nätverksprestanda lösning i Azure Log Analytics | Microsoft Docs
description: Använd ExpressRoute Monitor-funktionen i Övervakare av nätverksprestanda för att övervaka anslutnings möjligheter från slut punkt till slut punkt och prestanda mellan dina avdelnings kontor och Azure, via Azure ExpressRoute.
ms.topic: conceptual
author: abshamsft
ms.author: absha
ms.date: 11/27/2018
ms.openlocfilehash: 0b005e46e9f9a740fee153e063bebf108c22b303
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101725704"
---
# <a name="expressroute-monitor"></a>Övervakaren ExpressRoute

> [!IMPORTANT]
> Från och med 1 juli 2021 kommer du inte att kunna lägga till nya tester på en befintlig arbets yta eller aktivera en ny arbets yta i Övervakare av nätverksprestanda. Du kan fortsätta att använda testerna som skapats före den 1 juli 2021. [Migrera dina tester från övervakare av nätverksprestanda till den nya anslutnings övervakaren](../../network-watcher/migrate-to-connection-monitor-from-network-performance-monitor.md) i Azure Network Watcher före den 29 februari 2024 för att minimera avbrott i tjänsten för dina aktuella arbets belastningar.

Du kan använda Azure ExpressRoute Monitor-funktionen i [övervakare av nätverksprestanda](network-performance-monitor.md) för att övervaka anslutnings möjligheter från slut punkt till slut punkt och prestanda mellan dina avdelnings kontor och Azure, via Azure ExpressRoute. Viktiga fördelar: 

- Automatisk identifiering av ExpressRoute-kretsar som är associerade med din prenumeration.
- Spårning av bandbredds användning, förlust och svars tid på kretsen, peering och Azure Virtual Network nivå för ExpressRoute.
- Identifiering av nätverk sto pol Ogin för dina ExpressRoute-kretsar.

![Övervakaren ExpressRoute](media/network-performance-monitor-expressroute/expressroute-intro.png)

## <a name="configuration"></a>Konfiguration 
Öppna konfigurationen för Övervakare av nätverksprestanda genom att öppna [övervakare av nätverksprestanda lösning](network-performance-monitor.md) och välja **Konfigurera**.

### <a name="configure-network-security-group-rules"></a>Konfigurera regler för nätverks säkerhets grupper 
För de servrar i Azure som används för övervakning via Övervakare av nätverksprestanda konfigurerar du regler för nätverks säkerhets grupper (NSG) för att tillåta TCP-trafik på den port som används av Övervakare av nätverksprestanda för syntetiska transaktioner. Standard porten är 8084. Med den här konfigurationen kan Log Analytics-agenten som är installerad på virtuella Azure-datorer kommunicera med en lokal övervaknings agent. 

Mer information om NSG: er finns i [nätverks säkerhets grupper](../../virtual-network/manage-network-security-group.md). 

>[!NOTE]
> Innan du fortsätter med det här steget ska du installera den lokala server agenten och Azure Server agenten och köra EnableRules.ps1 PowerShell-skriptet. 

 
### <a name="discover-expressroute-peering-connections"></a>Identifiera ExpressRoute peering-anslutningar 
 
1. Välj vyn **ExpressRoute peering** .
2. Välj **identifiera nu** för att identifiera alla ExpressRoute-privata peer-datorer som är anslutna till de virtuella nätverken i Azure-prenumerationen som är länkad till den här Azure Log Analytics-arbetsytan.

    >[!NOTE]
    > Lösningen identifierar för närvarande endast ExpressRoute privata peering. 

    >[!NOTE]
    > Endast privata peer-anslutningar anslutna till de virtuella nätverk som är kopplade till den prenumeration som är länkad till den här Log Analytics arbets ytan identifieras. Om ExpressRoute är anslutet till virtuella nätverk utanför den prenumeration som är länkad till den här arbets ytan skapar du en Log Analytics arbets yta i dessa prenumerationer. Använd sedan Övervakare av nätverksprestanda för att övervaka dessa peer-kopplingar. 

    ![Konfiguration av ExpressRoute-övervakaren](media/network-performance-monitor-expressroute/expressroute-configure.png)
 
   När identifieringen är klar visas de identifierade privata peering-anslutningarna i en tabell. Övervakningen av dessa peer-kopplingar är inlednings vis i inaktiverat tillstånd. 

### <a name="enable-monitoring-of-the-expressroute-peering-connections"></a>Aktivera övervakning av ExpressRoute-peering-anslutningar 

1. Välj den privata peering-anslutning som du vill övervaka.
2. Markera kryss rutan **övervaka denna peering** i rutan till höger. 
3. Om du tänker skapa hälso händelser för den här anslutningen väljer du **Aktivera hälso övervakning för denna peering**. 
4. Välj övervaknings villkor. Du kan ange anpassade tröskelvärden för generering av hälso händelser genom att ange tröskelvärdena. När värdet för villkoret hamnar ovanför det valda tröskelvärdet för peering-anslutningen genereras en hälso händelse. 
5. Välj **Lägg till agenter** för att välja de övervaknings agenter som du vill använda för övervakning av den här peering-anslutningen. Se till att du lägger till agenter i båda ändar av anslutningen. Du behöver minst en agent i det virtuella nätverket som är anslutet till denna peering. Du behöver också minst en lokal agent som är ansluten till denna peering. 
6. Välj **Spara** för att spara konfigurationen. 

   ![Konfiguration av ExpressRoute-övervakning](media/network-performance-monitor-expressroute/expressroute-configure-discovery.png)


När du har aktiverat reglerna och valt värden och agenter, vänta 30 till 60 minuter för värdena att fylla i och **ExpressRoute övervaknings** paneler visas. När du ser övervaknings panelerna övervakas nu ExpressRoute-kretsarna och anslutnings resurserna av Övervakare av nätverksprestanda. 

>[!NOTE]
> Den här funktionen fungerar tillförlitligt på arbets ytor som har uppgraderats till det nya frågespråket.

## <a name="walkthrough"></a>Genomgång 

Instrument panelen Övervakare av nätverksprestanda visar en översikt över hälso tillståndet för ExpressRoute-kretsar och peering-anslutningar. 

![Övervakare av nätverksprestanda instrument panel](media/network-performance-monitor-expressroute/npm-dashboard-expressroute.png) 

### <a name="circuits-list"></a>Krets lista 

Om du vill se en lista över alla övervakade ExpressRoute-kretsar väljer du panelen ExpressRoute-kretsar. Du kan välja en krets och visa dess hälso tillstånd, trend diagram för paket förlust, bandbredds användning och svars tid. Diagrammen är interaktiva. Du kan välja ett anpassat tids fönster för att rita diagram. Dra musen över ett yta i diagrammet för att zooma in och se detaljerade data punkter. 

![Lista med ExpressRoute-kretsar](media/network-performance-monitor-expressroute/expressroute-circuits.png) 

### <a name="trends-of-loss-latency-and-throughput"></a>Trender för förlust, svars tid och data flöde 

Diagrammets användnings-, latens-och förlust diagram är interaktiva. Du kan zooma in till ett avsnitt i dessa diagram med hjälp av mus kontroller. Du kan också se bandbredd, latens och förlust data för andra intervall. I det övre vänstra hörnet under knappen **åtgärder** väljer du  **datum/tid**. 

![ExpressRoute-svars tid](media/network-performance-monitor-expressroute/expressroute-latency.png) 

### <a name="peerings-list"></a>Peering-lista 

Om du vill visa en lista över alla anslutningar till virtuella nätverk via privat peering väljer du panelen **privata peer** -kopplingar på instrument panelen. Här kan du välja en virtuell nätverks anslutning och visa dess hälso tillstånd, trend diagram för paket förlust, bandbredds användning och svars tid. 

![ExpressRoute-peering](media/network-performance-monitor-expressroute/expressroute-peerings.png) 

### <a name="circuit-topology"></a>Krets sto pol Ogin 

Om du vill visa krets sto pol Ogin väljer du panelen **topologi** . Den här åtgärden tar dig till Topology-vyn för den valda kretsen eller peering. Topology-diagrammet ger svars tiden för varje segment i nätverket och varje nivå 3-hopp representeras av en nod i diagrammet. Genom att välja ett hopp visar du mer information om hoppet. Om du vill öka Synlighets nivån så att den inkluderar lokala hopp, flytta du skjutreglaget under **filter**. Om du flyttar skjutreglaget till vänster eller höger ökar eller minskar du antalet hopp i Topology-diagrammet. Svars tiden för varje segment visas, vilket möjliggör snabbare isolering av segment med hög latens i nätverket.

![ExpressRoute-topologi](media/network-performance-monitor-expressroute/expressroute-topology.png)

### <a name="detailed-topology-view-of-a-circuit"></a>Detaljerad Topology-vy över en krets 

I den här vyn visas virtuella nätverks anslutningar. 

![ExpressRoute virtuella nätverks anslutningar](media/network-performance-monitor-expressroute/expressroute-vnet.png)
 
## <a name="diagnostics"></a>Diagnostik 

Övervakare av nätverksprestanda hjälper dig att diagnostisera flera problem med anslutning till kretsen. Några av de problem som du kan se visas nedan.

Du kan se meddelande koderna och ange aviseringar på dem via **LogAnalytics**. På sidan **NPM Diagnostics** kan du se beskrivningar för varje diagnostik-meddelande som utlöses.

| Meddelande kod (loggar) | Beskrivning |
| --- | --- |
| 5501 | Det går inte att passera genom den sekundära anslutningen av ExpressRoute-kretsen |
| 5502 | Det gick inte att passera genom den primära anslutningen av ExpressRoute-kretsen |
| 5503 | Ingen krets hittades för den prenumeration som är länkad till arbets ytan | 
| 5508 | Det går inte att avgöra om trafiken passerar genom en eller flera kretsar för sökvägen |
| 5510 | Trafiken passerar inte genom den avsedda kretsen | 
| 5511 | Trafiken passerar inte genom det avsedda virtuella nätverket | 

**Kretsen är avstängd.** Övervakare av nätverksprestanda meddelar dig så snart anslutningen mellan dina lokala resurser och virtuella Azure-nätverk förloras. Det här meddelandet hjälper dig att vidta förebyggande åtgärder innan du får användar eskaleringer och minskar stillestånds tiden.

![ExpressRoute-kretsen är nere](media/network-performance-monitor-expressroute/expressroute-circuit-down.png)
 

**Trafik flödar inte genom avsedd krets.** Övervakare av nätverksprestanda meddelar dig när trafiken inte flödar genom avsedd ExpressRoute-krets. Det här problemet kan inträffa om kretsen är avstängd och trafik flödar genom säkerhets kopierings vägen. Det kan också inträffa om det uppstår ett problem med routningen. Den här informationen hjälper dig att proaktivt hantera eventuella konfigurations problem i routningsprinciperna och se till att den mest optimala och säkra vägen används. 

 

**Trafiken flödar inte genom primär krets.** Övervakare av nätverksprestanda meddelar dig när trafik flödar genom den sekundära ExpressRoute-kretsen. Även om du inte kommer att uppleva några anslutnings problem i det här fallet, kan du proaktivt felsöka problemen med den primära kretsen för beredde bättre. 

 
![ExpressRoute-trafikflöde](media/network-performance-monitor-expressroute/expressroute-traffic-flow.png)


**Försämring på grund av hög belastning.** Du kan korrelera trenden för bandbredds användning med svars tids trenden för att identifiera om Azures arbets belastnings försämring beror på en belastning i bandbredds användningen eller inte. Sedan kan du vidta åtgärder i enlighet med detta.

![ExpressRoute bandbredds användning](media/network-performance-monitor-expressroute/expressroute-peak-utilization.png)

 

## <a name="next-steps"></a>Nästa steg
[Sök i loggar](../logs/log-query-overview.md) om du vill visa detaljerade data poster för nätverks prestanda.