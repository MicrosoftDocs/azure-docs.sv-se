---
title: Utforma elastiska lösningar med Azure DDoS Protection
description: Lär dig mer om hur du kan använda loggnings data för att få djupgående insikter om ditt program.
services: security
author: terrylanfear
manager: RKarlin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/18/2018
ms.author: terrylan
ms.openlocfilehash: e298cb0d1a2c510a096f8ead03f8af7e39c206a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96498939"
---
# <a name="azure-ddos-protection---designing-resilient-solutions"></a>Azure DDoS Protection – utforma elastiska lösningar

Den här artikeln är till för besluts fattare och säkerhets personal. Det förutsätter att du är bekant med Azure, nätverk och säkerhet.
DDoS är en typ av attack som försöker utnyttja program resurser. Målet är att påverka programmets tillgänglighet och dess förmåga att hantera legitima begär Anden. Angrepp blir mer sofistikerade och större i storlek och påverkan. DDoS-attacker kan riktas mot valfri slutpunkt som kan nås offentligt via Internet. Design för DDoS-återhämtning (distributed denial of Service) kräver planering och design för en mängd olika fellägen. Azure ger kontinuerligt skydd mot DDoS-attacker. Det här skyddet integreras i Azure-plattformen som standard och utan extra kostnad.

Förutom kärn DDoS-skyddet i plattformen tillhandahåller [Azure DDoS Protection standard](https://azure.microsoft.com/services/ddos-protection/) avancerade funktioner för DDoS-minskning mot nätverks attacker. Det är automatiskt anpassat för att skydda dina Azure-resurser. Det är enkelt att aktivera skydd när nya virtuella nätverk skapas. Du kan också göra det när det har skapats och inga program-eller resurs ändringar krävs.

![Rollen Azure DDoS Protection för att skydda kunder och ett virtuellt nätverk från en angripare](./media/ddos-best-practices/image1.png)


## <a name="fundamental-best-practices"></a>Grundläggande regelverk

I följande avsnitt får du vägledning för att skapa DDoS-elastiska tjänster på Azure.

### <a name="design-for-security"></a>Design för säkerhet

Se till att säkerheten är en prioritet under hela livs cykeln för ett program, från design och implementering till distribution och drift. Program kan ha buggar som tillåter en relativt låg mängd begär Anden att använda en inordinat-mängd resurser, vilket resulterar i avbrott i tjänsten. 

För att skydda en tjänst som körs på Microsoft Azure bör du ha en god förståelse för din program arkitektur och fokusera på de [fem bild stenarna med program kvalitet](/azure/architecture/guide/pillars).
Du bör känna till vanliga trafik volymer, anslutnings modellen mellan programmet och andra program och de tjänst slut punkter som exponeras för det offentliga Internet.

Att se till att ett program är tillräckligt flexibelt för att hantera en denial of service som är riktad mot själva programmet är viktigast. Säkerhet och sekretess är inbyggda i Azure-plattformen, från och med [säkerhets utvecklings livs cykeln (SDL)](https://www.microsoft.com/sdl/default.aspx). SDL adresserar säkerheten i varje utvecklings fas och säkerställer att Azure uppdateras kontinuerligt för att göra det ännu säkrare.

### <a name="design-for-scalability"></a>Design för skalbarhet

Skalbarhet är hur väl ett system kan hantera ökad belastning. Utforma dina program så att de kan [skalas horisontellt](/azure/architecture/guide/design-principles/scale-out) för att möta efter frågan på en förstärkt belastning, särskilt i händelse av en DDoS-attack. Om ditt program är beroende av en enda instans av en tjänst skapas en enskild felpunkt. Genom att tillhandahålla flera instanser blir systemet mer flexibelt och mer skalbart.

För [Azure App Service](../../app-service/overview.md)väljer du en [App Service plan](../../app-service/overview-hosting-plans.md) som erbjuder flera instanser. För Azure Cloud Services konfigurerar du var och en av dina roller så att de använder [flera instanser](../../cloud-services/cloud-services-choose-me.md). För [Azure Virtual Machines](../../virtual-machines/index.yml)kontrollerar du att arkitekturen för virtuella datorer (VM) innehåller fler än en virtuell dator och att varje virtuell dator ingår i en [tillgänglighets uppsättning](../../virtual-machines/windows/tutorial-availability-sets.md). Vi rekommenderar att du använder [skalnings uppsättningar för virtuella datorer](../../virtual-machine-scale-sets/overview.md) för funktioner för automatisk skalning.

### <a name="defense-in-depth"></a>Skydd på djupet

Idén bakom försvaret är att hantera risker genom att använda olika metoder för försvar. Skiktning av säkerhets försvar i ett program minskar risken för en lyckad attack. Vi rekommenderar att du implementerar säkra utformningar för dina program genom att använda de inbyggda funktionerna i Azure-plattformen.

Risken för angrepp ökar t. ex. den storlek (arean *) i* programmet. Du kan minska arean genom att använda en godkännande lista för att stänga det exponerade IP-adressutrymmet och lyssnande portar som inte behövs på belastningsutjämnaren ([Azure Load Balancer](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) och [Azure Application Gateway](../../application-gateway/application-gateway-create-probe-portal.md)). [Nätverks säkerhets grupper (NSG: er)](../../virtual-network/network-security-groups-overview.md) är ett annat sätt att minska angrepps ytan.
Du kan använda [tjänst Taggar](../../virtual-network/network-security-groups-overview.md#service-tags) och [program säkerhets grupper](../../virtual-network/network-security-groups-overview.md#application-security-groups) för att minimera komplexiteten för att skapa säkerhets regler och konfigurera nätverks säkerhet som ett naturligt tillägg till ett programs struktur.

Du bör Distribuera Azure-tjänster i ett [virtuellt nätverk](../../virtual-network/virtual-networks-overview.md) närhelst det är möjligt. Den här metoden gör det möjligt för tjänst resurser att kommunicera via privata IP-adresser. Azure Service-trafik från ett virtuellt nätverk använder offentliga IP-adresser som standard käll-IP-adresser. Genom att använda [tjänst slut punkter](../../virtual-network/virtual-network-service-endpoints-overview.md) växlar tjänst trafiken till att använda privata adresser i det virtuella nätverket som käll-IP-adresser när de ansluter till Azure-tjänsten från ett virtuellt nätverk.

Vi ser ofta kunders lokala resurser som tar emot angrepp tillsammans med sina resurser i Azure. Om du ansluter en lokal miljö till Azure rekommenderar vi att du minimerar exponeringen för lokala resurser till det offentliga Internet. Du kan använda funktionerna för skalning och avancerad DDoS i Azure genom att distribuera välkända offentliga entiteter i Azure. Eftersom dessa offentligt tillgängliga entiteter ofta är ett mål för DDoS-attacker, minskar risken för lokala resurser genom att placera dem i Azure.

## <a name="azure-offerings-for-ddos-protection"></a>Azure-erbjudanden för DDoS-skydd

Azure har två DDoS tjänst erbjudanden som ger skydd mot nätverks attacker (Layer 3 och 4): DDoS Protection Basic och DDoS Protection standard. 

### <a name="ddos-protection-basic"></a>DDoS Protection Basic

Grundläggande skydd integreras i Azure som standard utan extra kostnad. Skalan och kapaciteten för det globalt distribuerade Azure-nätverket ger skydd mot vanliga attacker på nätverks nivå genom att alltid övervaka trafik övervakning och i real tid. För DDoS Protection Basic krävs inga användar konfigurations-eller program ändringar. DDoS Protection Basic hjälper till att skydda alla Azure-tjänster, inklusive PaaS-tjänster som Azure DNS.

![Kart åter givning av Azure-nätverket med texten "global DDoS-minskning" och "ledande DDoS för minskning av"](./media/ddos-best-practices/image3.png)

Basic DDoS Protection i Azure består av både program-och maskin varu komponenter. Ett program kontroll plan bestämmer när, var och vilken typ av trafik som ska styras genom maskin varu apparater som analyserar och tar bort attack trafik. Kontroll planet gör detta beslut baserat på en DDoS Protection *princip* för hela infrastrukturen. Den här principen är statiskt inställd och används universellt för alla Azure-kunder.

DDoS Protection principen anger till exempel vilken trafik volym som skyddet ska *utlösas.* (Det vill säga klientens trafik ska dirigeras via skrubbning apparater.) Principen anger sedan hur skrubbnings utrustningarna bör *minimera* attacken.

Tjänsten Azure DDoS Protection Basic är riktad mot skydd av infrastrukturen och skyddet av Azure-plattformen. Den minskar trafiken när den överskrider en hastighet som är så viktig att den kan påverka flera kunder i en miljö med flera innehavare. Den ger inga aviseringar eller anpassade principer per kund.

### <a name="ddos-protection-standard"></a>DDoS Protection Standard

Standard skydd ger förbättrade funktioner för DDoS-minskning. Den är automatiskt anpassad för att hjälpa till att skydda dina Azure-resurser i ett virtuellt nätverk. Det är enkelt att aktivera skydd på nya eller befintliga virtuella nätverk och det krävs inga program-eller resurs ändringar. Det har flera fördelar jämfört med den grundläggande tjänsten, inklusive loggning, avisering och telemetri. I följande avsnitt beskrivs viktiga funktioner i Azure DDoS Protection standard tjänsten.

#### <a name="adaptive-real-time-tuning"></a>Anpassad real tids justering

Tjänsten Azure DDoS Protection Basic hjälper till att skydda kunder och förhindra påverkan på andra kunder. Om en tjänst till exempel tillhandahålls för en typisk volym av legitim inkommande trafik som är mindre än *utlösarens hastighet* för DDoS Protection principen för hela infrastrukturen, kan en DDoS-attack på kundens resurser vara påslagen. I allmänhet är komplexiteten för de senaste attackerna (till exempel multi-Vector-DDoS) och de programspecifika beteenden för klient organisationer som anropar per kund, anpassade skydds principer. Tjänsten utför den här anpassningen genom att använda två insikter:

- Automatisk inlärning av trafik mönster per kund (per IP) för Layer 3 och 4.

- Minimera antalet falska positiva identifieringar, med hänsyn till att Azures skala kan absorbera en betydande mängd trafik.

![Diagram över hur DDoS Protection standard fungerar med "princip generering" inringad](./media/ddos-best-practices/image5.png)

#### <a name="ddos-protection-telemetry-monitoring-and-alerting"></a>DDoS Protection telemetri, övervakning och aviseringar

DDoS Protection standard exponerar avancerad telemetri via [Azure Monitor](../../azure-monitor/overview.md) under en DDoS attack. Du kan konfigurera aviseringar för de Azure Monitor mått som DDoS Protection använder. Du kan integrera loggning med Splunk (Azure Event Hubs), Azure Monitor loggar och Azure Storage för avancerad analys via Azure Monitor Diagnostics-gränssnittet.

##### <a name="ddos-mitigation-policies"></a>Principer för DDoS-minskning

I Azure Portal väljer du **övervaka**  >  **mått**. I fönstret **mått** väljer du resurs grupp, väljer en resurs typ för **offentlig IP-adress** och väljer din offentliga Azure-IP-adress. DDoS mått visas i fönstret **tillgängliga mått** .

DDoS Protection standard tillämpar tre automatiskt justerade begränsnings principer (TCP-SYN, TCP och UDP) för varje offentlig IP-adress för den skyddade resursen i det virtuella nätverk där DDoS är aktiverat. Du kan visa princip tröskelvärdena genom att välja måttet **inkommande paket för att utlösa DDoS-minskning**.

![Tillgängliga mått och mått diagram](./media/ddos-best-practices/image7.png)

Princip tröskelvärdena konfigureras automatiskt via Machine Learning-baserad nätverks trafik profilering. DDoS-minskning sker endast för en IP-adress under attacken när princip tröskelvärdet har överskridits.

##### <a name="metric-for-an-ip-address-under-ddos-attack"></a>Mått för en IP-adress under DDoS-attack

Om den offentliga IP-adressen är utsatt för angrepp, värdet för måttet **under DDoS-attack eller inte** ändras till 1 som DDoS Protection utför minskning av attack trafiken.

!["Under DDoS-angrepp eller inte" mått och diagram](./media/ddos-best-practices/image8.png)

Vi rekommenderar att du konfigurerar en avisering om detta mått. Därefter får du ett meddelande när det finns en aktiv DDoS-åtgärd som utförs på din offentliga IP-adress.

Mer information finns i [hantera Azure DDoS Protection standard med hjälp av Azure Portal](../../ddos-protection/manage-ddos-protection.md).

#### <a name="web-application-firewall-for-resource-attacks"></a>Brand vägg för webbaserade program för resurs attacker

Specifika för resurs attacker i program lagret bör du konfigurera en brand vägg för webbaserade program (WAF) för att skydda webb program. En WAF kontrollerar inkommande webb trafik för att blockera SQL-injektioner, skript körning över flera webbplatser, DDoS och andra nivå 7-attacker. Azure tillhandahåller [WAF som en funktion i Application Gateway](../../web-application-firewall/ag/ag-overview.md) för centraliserat skydd av dina webb program mot vanliga sårbarheter och sårbarheter. Det finns andra WAF-erbjudanden som är tillgängliga från Azure-partner som kan vara mer lämpliga för dina behov via [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?search=WAF&page=1).

Även brand väggar för webb program är mottagliga för angrepp med tillstånds-och tillstånds angrepp. Vi rekommenderar starkt att du aktiverar DDoS Protection standard på det virtuella WAF-nätverket för att skydda dig från volym-och protokoll attacker. Mer information finns i avsnittet [DDoS Protection referens arkitekturer](#ddos-protection-reference-architectures) .

### <a name="protection-planning"></a>Skydds planering

Planering och förberedelser är avgörande för att förstå hur ett system kommer att utföras under en DDoS-attack. Att designa en svars plan för incident hantering är en del av den här ansträngningen.

Om du har DDoS Protection standard kontrollerar du att den är aktive rad på det virtuella nätverket för Internet-riktade slut punkter. Genom att konfigurera DDoS-aviseringar kan du ständigt titta efter eventuella attacker i infrastrukturen. 

Övervaka dina program oberoende av varandra. Förstå det normala beteendet i ett program. Förbered för att agera om programmet inte fungerar som förväntat under ett DDoS-angrepp.

#### <a name="testing-through-simulations"></a>Testa genom simuleringar

Det är en bra idé att testa dina antaganden om hur dina tjänster kommer att svara på ett angrepp genom att utföra periodiska simuleringar. Under testningen kontrollerar du att tjänsterna eller programmen fortsätter att fungera som förväntat och att användar upplevelsen inte störs. Identifiera luckor från både en teknik och ett process syn och införliva dem i DDoS-svars strategin. Vi rekommenderar att du utför sådana tester i tillfälliga miljöer eller under låg belastnings tider för att minimera påverkan på produktions miljön.

Vi samarbetar med [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) för att skapa ett gränssnitt där Azure-kunder kan generera trafik mot DDoS Protection-aktiverade offentliga slut punkter för simuleringar. Du kan använda [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) simulering för att:

- Validera hur Azure DDoS Protection hjälper till att skydda dina Azure-resurser från DDoS-attacker.

- Optimera incidenthanteringsprocessen under DDoS-angrepp.

- Dokumentera DDoS-efterlevnad.

- Träna dina nätverkssäkerhetsgrupper.

Cybersäkerhet kräver ständig innovation i försvars verksamhet. Azure DDoS standard Protection är ett bra erbjudande med en effektiv lösning för att minimera ökande komplexa DDoS-attacker.

## <a name="components-of-a-ddos-response-strategy"></a>Komponenter i en strategi för att hantera DDoS-attacker

En DDoS-attack som är riktad mot Azure-resurser kräver vanligt vis minimalt ingripande från en användar synpunkt. Att införliva DDoS-minskning som en del av en strategi för incident svar bidrar fortfarande till att minimera påverkan på affärs kontinuitet.

### <a name="microsoft-threat-intelligence"></a>Microsoft Threat Intelligence

Microsoft har ett omfattande hot informations nätverk. I det här nätverket används en samlad kunskap om en utökad säkerhets grupp som stöder Microsoft onlinetjänster, Microsoft-partner och-relationer i Internet Security-communityn. 

Som en kritisk infrastruktur leverantör får Microsoft tidiga varningar om hot. Microsoft samlar in Hot information från dess onlinetjänster och från dess globala kund bas. Microsoft införlivar all den här hot informationen igen i Azure DDoS Protection produkter.

Dessutom utför Microsoft Digital brottslighet-enheten (DCU) stötande strategier mot botnät. Botnät är en gemensam källa för kommando och kontroll för DDoS-attacker.

### <a name="risk-evaluation-of-your-azure-resources"></a>Riskbedömning av dina Azure-resurser

Det är absolut nödvändigt att förstå omfattningen av din risk från en DDoS-attack kontinuerligt. Fråga dig själv med jämna mellanrum:

- Vilka nya offentligt tillgängliga Azure-resurser behöver skydd?

- Finns det en enskild felpunkt i tjänsten? 

- Hur kan tjänster isoleras för att begränsa effekten av ett angrepp samtidigt som tjänster blir tillgängliga för giltiga kunder?

- Finns det virtuella nätverk där DDoS Protection standard ska aktive ras, men är det inte? 

- Är mina tjänster aktiva/aktiva med redundans över flera regioner?

### <a name="customer-ddos-response-team"></a>Kund DDoS-svars team

Att skapa en DDoS-svars grupp är ett viktigt steg i att svara på en attack snabbt och effektivt. Identifiera kontakter i din organisation som ska övervaka både planering och körning. Det här DDoS-svars teamet bör noggrant förstå Azure DDoS Protection standard tjänsten. Se till att teamet kan identifiera och åtgärda ett angrepp genom att koordinera med interna och externa kunder, inklusive Microsoft Support-teamet.

För ditt DDoS-svars Team rekommenderar vi att du använder simulerings övningar som en normal del av tjänstens tillgänglighet och kontinuitets planering. De här övningarna bör omfatta skalnings testning.

### <a name="alerts-during-an-attack"></a>Aviseringar under en attack

Azure DDoS Protection standard identifierar och minimerar DDoS-attacker utan att användaren behöver vidta några åtgärder. Om du vill få ett meddelande när det finns en aktiv åtgärd för en skyddad offentlig IP-adress kan du [Konfigurera en avisering](../../ddos-protection/manage-ddos-protection.md) på måttet **under DDoS-attack eller inte**. Du kan välja att skapa aviseringar för de andra DDoS-måtten för att förstå storleken på angreppet, trafik som släpps och annan information.

#### <a name="when-to-contact-microsoft-support"></a>När du ska kontakta Microsoft-supporten

- Under en DDoS-attack märker du att prestandan för den skyddade resursen är mycket försämrad eller att resursen inte är tillgänglig.

- Du tror att DDoS Protections tjänsten inte fungerar som förväntat. 

  Den DDoS Protection tjänsten startar bara en minskning om mått värde **principen för att utlösa DDoS-minskning (TCP/TCP syn/UDP)** är lägre än den trafik som tas emot på den skyddade offentliga IP-resursen.

- Du planerar ett viralt evenemang som kommer att öka nätverkstrafiken markant.

- En aktör har hotat att starta en DDoS-attack mot dina resurser.

- Om du behöver tillåta lista ett IP-eller IP-intervall från Azure DDoS Protection standard. Ett vanligt scenario är att tillåta List-IP om trafiken dirigeras från ett externt moln WAF till Azure. 

För attacker som har kritisk inverkan på företaget kan du skapa en allvarlighets grad – ett [support ärende](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

### <a name="post-attack-steps"></a>Steg efter angrepp

Det är alltid en lämplig strategi att göra en Postmortem efter ett angrepp och justera DDoS-svars strategin efter behov. Saker att tänka på:

- Fanns det några avbrott i tjänsten eller användar upplevelsen på grund av brist på skalbar arkitektur?

- Vilka program eller tjänster drabbades mest?

- Hur effektivt var DDoS svars strategi och hur kan den förbättras?

Om du misstänker att du befinner dig under ett DDoS-angrepp kan du eskalera de vanliga support kanalerna för Azure.

## <a name="ddos-protection-reference-architectures"></a>DDoS Protection referens arkitekturer

DDoS Protection standard är utformad [för tjänster som distribueras i ett virtuellt nätverk](../../virtual-network/virtual-network-for-azure-services.md). För andra tjänster gäller standard tjänsten för DDoS Protection Basic. Följande referens arkitekturer ordnas efter scenarier, med arkitektur mönster grupperade tillsammans.

### <a name="virtual-machine-windowslinux-workloads"></a>Arbets belastningar för virtuella datorer (Windows/Linux)

#### <a name="application-running-on-load-balanced-vms"></a>Program som körs på belastningsutjämnade virtuella datorer

Den här referens arkitekturen visar en uppsättning beprövade metoder för att köra flera virtuella Windows-datorer i en skalnings uppsättning bakom en belastningsutjämnare för att förbättra tillgängligheten och skalbarheten. Den här arkitekturen kan användas för alla tillstånds lösa arbets belastningar, till exempel en webb server.

![Diagram över referens arkitekturen för ett program som körs på belastningsutjämnade virtuella datorer](./media/ddos-best-practices/image9.png)

En arbetsbelastning distribueras över flera VM-instanser i den här arkitekturen. Det finns en enda offentlig IP-adress och Internet trafiken distribueras till de virtuella datorerna via en belastningsutjämnare. DDoS Protection standard har Aktiver ATS på det virtuella nätverket för belastningsutjämnaren i Azure (Internet) som har den offentliga IP-adress som är kopplad till den.

Belastningsutjämnaren distribuerar inkommande Internet-begäranden till VM-instanserna. Skalnings uppsättningar för virtuella datorer tillåter att antalet virtuella datorer skalas in eller ut manuellt eller automatiskt baserat på fördefinierade regler. Detta är viktigt om resursen är under DDoS-attack. Mer information om den här referens arkitekturen finns i [den här artikeln](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm).

#### <a name="application-running-on-windows-n-tier"></a>Program som körs på Windows N-nivå

Det finns många sätt att implementera en arkitektur på N-nivå. Följande diagram visar ett typiskt webb program på tre nivåer. Den här arkitekturen bygger på artikeln [Kör belastningsutjämnade virtuella datorer för skalbarhet och tillgänglighet](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm). Webb- och företagsnivåer använder belastningsutjämnade virtuella datorer.

![Diagram över referens arkitekturen för ett program som körs på Windows N-nivå](./media/ddos-best-practices/image10.png)

I den här arkitekturen är DDoS Protection standard aktiverat i det virtuella nätverket. Alla offentliga IP-adresser i det virtuella nätverket får DDoS-skydd för Layer 3 och 4. För Layer 7-skydd distribuerar du Application Gateway i SKU: n för WAF. Mer information om den här referens arkitekturen finns i [den här artikeln](/azure/architecture/reference-architectures/virtual-machines-windows/n-tier).

#### <a name="paas-web-application"></a>PaaS-webbprogram

Den här referens arkitekturen visar körning av ett Azure App Service program i en enda region. Den här arkitekturen visar en uppsättning beprövade metoder för ett webb program som använder [Azure App Service](https://azure.microsoft.com/documentation/services/app-service/) och [Azure SQL Database](https://azure.microsoft.com/documentation/services/sql-database/).
En vänte region har kon figurer ATS för failover-scenarier.

![Diagram över referens arkitekturen för ett PaaS-webbprogram](./media/ddos-best-practices/image11.png)

Azure Traffic Manager dirigerar inkommande begär anden till Application Gateway i en av regionerna. Vid normal drift dirigerar den förfrågningar till Application Gateway i den aktiva regionen. Om regionen blir otillgänglig, Traffic Manager växlar över till Application Gateway i vänte läge.

All trafik från Internet som är avsedd för webb programmet dirigeras till den [Application Gateway offentliga IP-adressen](../../application-gateway/application-gateway-web-app-overview.md) via Traffic Manager. I det här scenariot är App Service (webbapp) inte direkt externt och skyddas av Application Gateway. 

Vi rekommenderar att du konfigurerar Application Gateway WAF SKU (förhindra läge) för att skydda mot Layer 7-attacker (HTTP/HTTPS/WebSocket). Dessutom är Web Apps konfigurerade för att [endast acceptera trafik från Application Gateway](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/) IP-adress.

Mer information om den här referens arkitekturen finns i [den här artikeln](/azure/architecture/reference-architectures/app-service-web-app/multi-region).

### <a name="mitigation-for-non-web-paas-services"></a>Minskning för icke-PaaS tjänster

#### <a name="hdinsight-on-azure"></a>HDInsight på Azure

Den här referens arkitekturen visar hur du konfigurerar DDoS Protection standard för ett [Azure HDInsight-kluster](../../hdinsight/index.yml). Se till att HDInsight-klustret är länkat till ett virtuellt nätverk och att DDoS Protection är aktiverat på det virtuella nätverket.

![Panelerna "HDInsight" och "avancerade inställningar" med inställningar för virtuellt nätverk](./media/ddos-best-practices/image12.png)

![Val för att aktivera DDoS Protection](./media/ddos-best-practices/image13.png)

I den här arkitekturen dirigeras trafik till HDInsight-klustret från Internet till den offentliga IP-adress som är associerad med belastningsutjämnaren för HDInsight Gateway. Gateway-belastningsutjämnaren skickar sedan trafiken till Head-noderna eller arbetsnoderna direkt. Eftersom DDoS Protection standard är aktiverat i det virtuella HDInsight-nätverket får alla offentliga IP-adresser i det virtuella nätverket DDoS skydd för Layer 3 och 4. Den här referens arkitekturen kan kombineras med referens arkitekturer för N-nivå och flera regioner.

Mer information om den här referens arkitekturen finns i [utöka Azure HDInsight med hjälp av en Azure Virtual Network](../../hdinsight/hdinsight-plan-virtual-network-deployment.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -dokumentation.


> [!NOTE]
> Azure App Service Environment för PowerApps eller API Management i ett virtuellt nätverk med en offentlig IP-adress stöds inte internt.

## <a name="next-steps"></a>Nästa steg

* [Delat ansvar i molnet](shared-responsibility.md)
* [Sidan Azure DDoS Protection produkt](https://azure.microsoft.com/services/ddos-protection/)
* [Azure DDoS Protection dokumentation](../../ddos-protection/ddos-protection-overview.md)