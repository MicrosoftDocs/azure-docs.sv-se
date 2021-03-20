---
title: Azure DDoS Protection funktioner
description: Lär dig Azure DDoS Protection funktioner
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 602bb98f2cdc8a96874eba8dadfa33f3267d19ac
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97746566"
---
# <a name="azure-ddos-protection-standard-features"></a>Funktioner i Azure DDoS Protection Standard

I följande avsnitt beskrivs viktiga funktioner i Azure DDoS Protection standard tjänsten.

## <a name="always-on-traffic-monitoring"></a>Always on trafik Monitoring

DDoS Protection standard övervakar faktisk trafik användning och jämför den kontinuerligt med de tröskelvärden som definierats i DDoS-principen. När trafik tröskelvärdet överskrids initieras DDoS-minskning automatiskt. När trafiken returneras under tröskelvärdena stoppas lösningen.

![Azure DDoS Protection standard minskning](./media/ddos-protection-overview/mitigation.png)

Under minskningen omdirigeras trafik som skickas till den skyddade resursen av DDoS Protection service och flera kontroller utförs, till exempel:

- Se till att paketen överensstämmer med Internet-specifikationerna och inte är felaktiga.
- Interagera med klienten för att avgöra om trafiken kan vara ett manipulerat paket (t. ex. SYN-auth eller SYN-cookie eller genom att släppa ett paket för källan för att överföra det igen).
- Hastighets begränsnings paket, om ingen annan tvingande metod kan utföras.

DDoS-skyddet släpper attack trafik och vidarebefordrar den återstående trafiken till det avsedda målet. Inom några minuter efter att ett angrepp har identifierats meddelas du via Azure Monitor-mått. Genom att konfigurera inloggning DDoS Protection standard telemetri kan du skriva loggarna till tillgängliga alternativ för framtida analys. Mått data i Azure Monitor för DDoS Protection standard är kvar i 30 dagar.

## <a name="adaptive-real-time-tuning"></a>Anpassad real tids justering

Tjänsten Azure DDoS Protection Basic hjälper till att skydda kunder och förhindra påverkan på andra kunder. Om en tjänst till exempel tillhandahålls för en typisk volym av legitim inkommande trafik som är mindre än *utlösarens hastighet* för DDoS Protection principen för hela infrastrukturen, kan en DDoS-attack på kundens resurser vara påslagen. I allmänhet är komplexiteten för de senaste attackerna (till exempel multi-Vector DDoS) och de programspecifika beteenden för klienterna som anropar per kund, skräddarsydda skydds principer. Tjänsten utför detta genom att använda två insikter:

- Automatisk inlärning av trafik mönster per kund (per offentlig IP) för nivå 3 och 4.

- Minimera antalet falska positiva identifieringar, med hänsyn till att Azures skala kan absorbera en betydande mängd trafik.

![Diagram över hur DDoS Protection standard fungerar med "princip generering" inringad](./media/ddos-best-practices/image-5.png)

## <a name="ddos-protection-telemetry-monitoring-and-alerting"></a>DDoS Protection telemetri, övervakning och aviseringar

DDoS Protection standard visar avancerad telemetri via [Azure Monitor](../azure-monitor/overview.md). Du kan konfigurera aviseringar för de Azure Monitor mått som DDoS Protection använder. Du kan integrera loggning med Splunk (Azure Event Hubs), Azure Monitor loggar och Azure Storage för avancerad analys via Azure Monitor Diagnostics-gränssnittet.

### <a name="ddos-mitigation-policies"></a>Principer för DDoS-minskning

I Azure Portal väljer du **övervaka**  >  **mått**. I fönstret **mått** väljer du resurs grupp, väljer en resurs typ för **offentlig IP-adress** och väljer din offentliga Azure-IP-adress. DDoS mått visas i fönstret **tillgängliga mått** .

DDoS Protection standard tillämpar tre automatiskt justerade begränsnings principer (TCP-SYN, TCP och UDP) för varje offentlig IP-adress för den skyddade resursen i det virtuella nätverk där DDoS är aktiverat. Du kan visa princip tröskelvärdena genom att välja måttet **inkommande paket för att utlösa DDoS-minskning**.

![Tillgängliga mått och mått diagram](./media/ddos-best-practices/image-7.png)

Princip tröskelvärdena konfigureras automatiskt via Machine Learning-baserad nätverks trafik profilering. DDoS-minskning sker endast för en IP-adress under attacken när princip tröskelvärdet har överskridits.

### <a name="metric-for-an-ip-address-under-ddos-attack"></a>Mått för en IP-adress under DDoS-attack

Om den offentliga IP-adressen är utsatt för angrepp, värdet för måttet **under DDoS-attack eller inte** ändras till 1 som DDoS Protection utför minskning av attack trafiken.

!["Under DDoS-angrepp eller inte" mått och diagram](./media/ddos-best-practices/image-8.png)

Vi rekommenderar att du konfigurerar en avisering om detta mått. Därefter får du ett meddelande när det finns en aktiv DDoS-åtgärd som utförs på din offentliga IP-adress.

Mer information finns i [hantera Azure DDoS Protection standard med hjälp av Azure Portal](manage-ddos-protection.md).

## <a name="web-application-firewall-for-resource-attacks"></a>Brand vägg för webbaserade program för resurs attacker

Specifika för resurs attacker i program lagret bör du konfigurera en brand vägg för webbaserade program (WAF) för att skydda webb program. En WAF kontrollerar inkommande webb trafik för att blockera SQL-injektioner, skript körning över flera webbplatser, DDoS och andra nivå 7-attacker. Azure tillhandahåller [WAF som en funktion i Application Gateway](../web-application-firewall/ag/ag-overview.md) för centraliserat skydd av dina webb program mot vanliga sårbarheter och sårbarheter. Det finns andra WAF-erbjudanden som är tillgängliga från Azure-partner som kan vara mer lämpliga för dina behov via [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?search=WAF&page=1).

Även brand väggar för webb program är mottagliga för angrepp med tillstånds-och tillstånds angrepp. Vi rekommenderar starkt att du aktiverar DDoS Protection standard på det virtuella WAF-nätverket för att skydda dig från volym-och protokoll attacker. Mer information finns i avsnittet [DDoS Protection referens arkitekturer](ddos-protection-reference-architectures.md) .

## <a name="protection-planning"></a>Skydds planering

Planering och förberedelser är avgörande för att förstå hur ett system kommer att utföras under en DDoS-attack. Att designa en svars plan för incident hantering är en del av den här ansträngningen.

Om du har DDoS Protection standard kontrollerar du att den är aktive rad på det virtuella nätverket för Internet-riktade slut punkter. Genom att konfigurera DDoS-aviseringar kan du ständigt titta efter eventuella attacker i infrastrukturen. 

Övervaka dina program oberoende av varandra. Förstå det normala beteendet i ett program. Förbered för att agera om programmet inte fungerar som förväntat under ett DDoS-angrepp. 

Lär dig hur dina tjänster kommer att svara på ett angrepp genom att [testa genom simuleringar](test-through-simulations.md).

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [skapar en DDoS-skydds plan](manage-ddos-protection.md).
