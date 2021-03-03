---
title: Övervakare av nätverksprestanda lösning i Azure | Microsoft Docs
description: Övervakare av nätverksprestanda i Azure hjälper dig att övervaka nätverkets prestanda, i nära real tid, för att identifiera och hitta Flask halsar i nätverks prestanda.
ms.topic: conceptual
author: vinynigam
ms.author: vinigam
ms.date: 02/20/2018
ms.openlocfilehash: dd9f35a0b7c3bb5c2c5888765e4ebac436b4cde1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101711135"
---
# <a name="network-performance-monitor-solution-in-azure"></a>Övervakare av nätverksprestanda lösning i Azure

![Övervakare av nätverksprestanda symbol](./media/network-performance-monitor/npm-symbol.png)

> [!IMPORTANT]
> Från och med 1 juli 2021 kommer du inte att kunna lägga till nya tester på en befintlig arbets yta eller aktivera en ny arbets yta i Övervakare av nätverksprestanda. Du kan fortsätta att använda testerna som skapats före den 1 juli 2021. [Migrera dina tester från övervakare av nätverksprestanda till den nya anslutnings övervakaren](../../network-watcher/migrate-to-connection-monitor-from-network-performance-monitor.md) i Azure Network Watcher före den 29 februari 2024 för att minimera avbrott i tjänsten för dina aktuella arbets belastningar.

Övervakare av nätverksprestanda är en molnbaserad hybrid nätverks övervaknings lösning som hjälper dig att övervaka nätverks prestanda mellan olika platser i din nätverks infrastruktur. Du kan också övervaka nätverksanslutningar till tjänsten och programslutpunkterna samt övervaka prestanda för Azure ExpressRoute. 

Övervakare av nätverksprestanda identifierar nätverks problem som trafik blackholing, synkroniseringsfel och problem som konventionella nätverks övervaknings metoder inte kan identifiera. Lösningen genererar aviseringar och meddelar dig när ett tröskelvärde överskrids för en nätverkslänk. Detta säkerställer att problem med nätverksprestanda upptäcks i god tid och att källan till problemet kan ringas in till ett visst nätverkssegment eller enhet. 

Övervakare av nätverksprestanda erbjuder tre breda funktioner: 

* [Prestanda övervakare](network-performance-monitor-performance-monitor.md): du kan övervaka nätverks anslutningar över moln distributioner och lokala platser, flera data Center och avdelnings kontor och verksamhets kritiska program på flera nivåer eller mikrotjänster. Med prestanda övervakaren kan du identifiera nätverks problem innan användarna klagar.

* [Övervakare för tjänst anslutning](network-performance-monitor-service-connectivity.md): du kan övervaka anslutningen från dina användare till de tjänster du bryr dig om, avgöra vilken infrastruktur som finns i sökvägen och identifiera var nätverks Flask halsar sker. Du kan veta mer om avbrott innan användarna och se exakt var problemen finns på din nätverks Sök väg. 

    Den här funktionen hjälper dig att utföra tester baserade på HTTP, HTTPS, TCP och ICMP för att övervaka i nära real tid eller historiskt tillgänglighet och svars tid för din tjänst. Du kan också övervaka nätverkets bidrag i paket förlust och svars tid. Du kan isolera nätverks fördröjningar med en karta över nätverks sto pol Ogin. Du kan identifiera problem fläckar som inträffar längs nätverks Sök vägen från noden till tjänsten, med latens data på varje hopp. Med inbyggda tester kan du övervaka nätverks anslutningen till Microsoft 365 och Dynamics CRM utan förkonfiguration. Med den här funktionen kan du övervaka nätverks anslutningen till alla TCP-kompatibla slut punkter, till exempel webbplatser, SaaS-program, PaaS-program och SQL-databaser.

* [ExpressRoute-övervakare](network-performance-monitor-expressroute.md): övervaka anslutnings möjligheter från slut punkt till slut punkt och prestanda mellan dina avdelnings kontor och Azure, via Azure ExpressRoute.  

Mer information om de olika funktionerna som stöds av [övervakare av nätverksprestanda](../../networking/network-monitoring-overview.md) finns online.
 
## <a name="supported-regions"></a>Regioner som stöds
NPM kan övervaka anslutningar mellan nätverk och program i valfri del av världen, från en arbets yta som finns i någon av följande regioner:
* Europa, norra
* Europa, västra
* Schweiz, norra
* Frankrike, centrala
* Sydafrika, norra
* Kanada, centrala
* USA, västra
* USA, västra centrala
* USA, norra centrala
* USA, södra centrala
* Central US
* East US
* USA, östra 2
* USA, västra 2
* Östra Japan
* Sydostasien
* Sydöstra Australien
* Australien, centrala
* Australien, östra
* Södra Storbritannien
* Asien, östra
* Sydkorea, centrala
* Indien, centrala
* AMERIKANSKA myndigheter i Virginia
* AMERIKANSKA myndigheters Arizona
* Kina, östra 2


Listan över regioner som stöds för ExpressRoute-övervakaren finns i [dokumentationen](../../expressroute/how-to-npm.md?utm_swu=8117).


## <a name="set-up-and-configure"></a>Konfigurera och konfigurera

> [!NOTE]
> Du kan också se Log Analytics-agenten som kallas Microsoft Monitoring Agent (MMA) eller OMS Linux-agenten.

### <a name="install-and-configure-agents"></a>Installera och konfigurera agenter 

Använd de grundläggande processerna för att installera agenter på [Anslut Windows-datorer till Azure Monitor](../agents/agent-windows.md), [ansluta Linux-datorer till Azure Monitor (för hands version)](../../virtual-machines/extensions/oms-linux.md) och [ansluta Operations Manager till Azure Monitor](../agents/om-agents.md).

### <a name="where-to-install-the-agents"></a>Var agenter ska installeras 

* **Prestanda övervakare**: installera Log Analytics agenter på minst en nod som är ansluten till varje under nätverk från vilken du vill övervaka nätverks anslutningen till andra under nätverk.

    Om du vill övervaka en nätverks länk installerar du agenter på båda slut punkterna för länken. Om du är osäker på nätverkets topologi installerar du agenterna på servrar med kritiska arbets belastningar mellan vilka du vill övervaka nätverks prestanda. Om du till exempel vill övervaka nätverks anslutningen mellan en webb server och en server som kör SQL, installerar du en agent på båda servrarna. Agenter övervakar nätverks anslutningar (länkar) mellan värdar, inte själva värdarna. 

* **Övervakare för tjänst anslutning**: installera en Log Analytics-agent på varje nod som du vill övervaka nätverks anslutningen till tjänstens slut punkt. Ett exempel är om du vill övervaka nätverks anslutningen till Microsoft 365 från dina Office-platser med etiketten O1, O2 och O3. Installera Log Analytics agent på minst en nod varje i O1, O2 och O3. 

* **ExpressRoute-övervakare**: installera minst en Log Analytics agent i ditt virtuella Azure-nätverk. Installera även minst en agent i det lokala under nätverket, som är anslutet via ExpressRoute privata peering.  

### <a name="configure-log-analytics-agents-for-monitoring"></a>Konfigurera Log Analytics agenter för övervakning 

Övervakare av nätverksprestanda använder syntetiska transaktioner för att övervaka nätverks prestanda mellan käll-och mål agenter. Du kan välja mellan TCP och ICMP som protokoll för övervakning i prestanda övervakaren och tjänst anslutningens övervaknings funktioner. Endast TCP är tillgängligt som övervaknings protokoll för ExpressRoute-övervakaren. Kontrol lera att brand väggen tillåter kommunikation mellan Log Analyticss agenter som används för övervakning av det protokoll du väljer. 

* **TCP-protokoll**: om du väljer TCP som protokoll för övervakning öppnar du brand Väggs porten på de agenter som används för övervakare av nätverksprestanda-och ExpressRoute-övervakaren för att kontrol lera att agenterna kan ansluta till varandra. För att öppna porten för Windows-datorer kör du [EnableRules.ps1](https://aka.ms/npmpowershellscript) PowerShell-skriptet utan parametrar i ett PowerShell-fönster med administratörs behörighet.
PortNumbers som ska användas måste ändras manuellt för Linux-datorer. 
* Navigera till sökvägen:/var/opt/Microsoft/omsagent/npm_state. 
* Öppna fil: npmdregistry
* Ändra värdet för port nummer ```“PortNumber:<port of your choice>”```

 Observera att port nummer som används ska vara samma för alla agenter som används i en arbets yta. 

Skriptet skapar register nycklar som krävs av lösningen. Det skapar också regler för Windows-brandväggen för att tillåta agenter att skapa TCP-anslutningar med varandra. Register nycklarna som skapas av skriptet anger om fel söknings loggarna och sökvägen till logg filen ska loggas. Skriptet definierar också den agent-TCP-port som används för kommunikation. Värdena för dessa nycklar anges automatiskt av skriptet. Ändra inte nycklarna manuellt. Porten som öppnas som standard är 8084. Du kan använda en anpassad port genom att ange parametern port nummer i skriptet. Använd samma port på alla datorer där skriptet körs. 

   >[!NOTE]
   > Skriptet konfigurerar endast Windows-brandväggen lokalt. Om du har en nätverks brand vägg kontrollerar du att den tillåter trafik som är avsedd för TCP-porten som används av Övervakare av nätverksprestanda.

   >[!NOTE]
   > Du behöver inte köra PowerShell-skriptet [EnableRules.ps1](https://aka.ms/npmpowershellscript ) för övervakning av tjänst anslutning.

    

* **ICMP-protokoll**: om du väljer ICMP som protokoll för övervakning aktiverar du följande brand Väggs regler för att på ett tillförlitligt sätt använda ICMP:
    
   ```
   netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
   ```
 

### <a name="configure-the-solution"></a>Konfigurera lösningen 

1. Lägg till Övervakare av nätverksprestanda-lösningen till din arbets yta från [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/solarwinds.solarwinds-orion-network-performance-monitor?tab=Overview). Du kan också använda processen som beskrivs i [Lägg till Azure Monitor lösningar från Lösningsgalleriet](./solutions.md). 
2. Öppna din Log Analytics arbets yta och välj panelen **Översikt** . 
3. Välj panelen **övervakare av nätverksprestanda** med meddelande *lösningen kräver ytterligare konfiguration*.

   ![Övervakare av nätverksprestanda panel](media/network-performance-monitor/npm-config.png)

4. På sidan **installation** visas alternativet för att installera Log Analytics agenter och konfigurera agenterna för övervakning i vyn **vanliga inställningar** . Som tidigare förklaras, om du har installerat och konfigurerat Log Analytics agenter, väljer du vyn **konfiguration** för att konfigurera den funktion som du vill använda. 

   **Prestanda övervakare**: Välj det protokoll som ska användas för syntetiska transaktioner i **standard** regeln för prestanda övervakning och välj **Spara & Fortsätt**. Det här protokoll valet innehåller bara för den systemgenererade standard regeln. Du måste välja protokollet varje gången du skapar en prestanda övervaknings regel explicit. Du kan alltid gå till **standard** regel inställningarna på fliken **prestanda övervakning** (den visas när du har slutfört din dags 0-konfiguration) och ändra protokollet senare. Om du inte vill ha prestanda övervakaren kan du inaktivera standard regeln från **standard** regel inställningarna på fliken **prestanda övervakning** .

   ![Vy över prestanda övervakaren](media/network-performance-monitor/npm-synthetic-transactions.png)
    
   **Övervakare för tjänst anslutning**: funktionen innehåller inbyggda förkonfigurerade tester för att övervaka nätverks anslutningen till Microsoft 365 och Dynamics 365 från dina agenter. Välj de Microsoft 365-och Dynamics 365-tjänster som du vill övervaka genom att markera kryss rutorna bredvid dem. Välj de agenter som du vill övervaka från genom att välja **Lägg till agenter**. Om du inte vill använda den här funktionen eller vill ställa in den senare, väljer du inte något och väljer **spara & Fortsätt**.

   ![Vy över tjänst anslutnings övervakare](media/network-performance-monitor/npm-service-endpoint-monitor.png)

   **ExpressRoute övervakare**: Välj **identifiera nu** för att identifiera alla ExpressRoute-privata peer-datorer som är anslutna till de virtuella nätverken i Azure-prenumerationen som är länkad till den här Log Analytics arbets ytan. 

   ![ExpressRoute Monitor-vy](media/network-performance-monitor/npm-express-route.png)

   När identifieringen är färdig visas de identifierade kretsarna och peer kopplingarna i en tabell. 

   ![Övervakare av nätverksprestanda konfigurations sida](media/network-performance-monitor/npm-private-peerings.png)
    
Övervakningen av dessa kretsar och peering är inlednings vis i ett inaktiverat tillstånd. Markera varje resurs som du vill övervaka och konfigurera övervakning för dem från vyn information till höger. Välj **Spara** för att spara konfigurationen. Mer information finns i artikeln "Konfigurera övervakning av ExpressRoute". 

När installationen är slutförd tar det 30 minuter till en timme innan data fylls. När lösningen sammanställer data från ditt nätverk kan du se att meddelande *lösningen kräver ytterligare konfiguration* på panelen övervakare av nätverksprestanda **Översikt** . När data har samlats in och indexerats ändras **översikts** panelen och informerar dig om din nätverks hälsa i en sammanfattning. Sedan kan du redigera övervakningen av noder där Log Analyticss agenter är installerade, samt de undernät som identifierats från din miljö.

#### <a name="edit-monitoring-settings-for-subnets-and-nodes"></a>Redigera övervaknings inställningar för undernät och noder 

Alla undernät med minst en agent installerad visas på fliken under **nätverk** på sidan konfiguration. 


Aktivera eller inaktivera övervakning av vissa under nätverk:

1. Markera eller avmarkera kryss rutan bredvid under **nätverkets ID**. Kontrol lera att **Använd för övervakning** är markerat eller avmarkerat, efter vad som är tillämpligt. Du kan markera eller avmarkera flera undernät. När det här alternativet är inaktiverat övervakas inte under nätverk och agenterna uppdateras för att stoppa pinga andra agenter. 
2. Välj de noder som du vill övervaka i ett visst under nätverk. Välj under nätverket i listan och flytta de nödvändiga noderna mellan listorna som innehåller oövervakade och övervakade noder. Du kan lägga till en anpassad beskrivning i under nätverket.
3. Välj **Spara** för att spara konfigurationen. 

#### <a name="choose-nodes-to-monitor"></a>Välj noder som ska övervakas

Alla noder som har en agent installerad på dem visas på fliken **noder** . 

1. Markera eller avmarkera de noder som du vill övervaka eller stoppa övervakningen. 
2. Välj **Använd för övervakning** eller avmarkera det efter behov. 
3. Välj **Spara**. 


Konfigurera de funktioner som du vill använda:

- [Prestandaövervakaren](network-performance-monitor-performance-monitor.md#configuration)
- [Övervakare av tjänstanslutning](network-performance-monitor-performance-monitor.md#configuration)
- [Övervakaren ExpressRoute](network-performance-monitor-expressroute.md#configuration)

 

## <a name="data-collection-details"></a>Information om data insamling
Om du vill samla in information om förlust och latens använder Övervakare av nätverksprestanda TCP SYN-SYNACK – handskaknings paket när du väljer TCP som protokoll. Övervakare av nätverksprestanda använder ICMP ECHO ICMP ECHO svara när du väljer ICMP som protokoll. Spårnings väg används också för att hämta Topology-information.

I följande tabell visas metoder för data insamling och annan information om hur data samlas in för Övervakare av nätverksprestanda.

| Plattform | Direkt agent | System Center Operations Manager agent | Azure Storage | Operations Manager krävs. | Operations Manager agent data som skickas via hanterings gruppen | Insamlingsfrekvens |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | &#8226; | &#8226; |  |  |  |TCP-handskakning/ICMP ECHO-meddelanden var 5: e sekund, data som skickas var 3: e minut |
 

 
Lösningen använder syntetiska transaktioner för att utvärdera nätverkets hälso tillstånd. Log Analytics agenter som installerats på olika platser i nätverket Exchange TCP-paket eller ICMP-eko med varandra. Huruvida agenterna använder TCP-paket eller ICMP-eko beror på vilket protokoll som du har valt för övervakning. I processen lär sig agenterna tiden för fördröjning och paket förlust, om det finns några. Med jämna mellanrum utför varje agent även en spårnings väg till andra agenter för att hitta alla de olika vägarna i nätverket som måste testas. Med hjälp av dessa data kan agenter härleda nätverks fördröjning och paket förlust siffror. Testerna upprepas var femte sekund. Data sammanställs i cirka tre minuter av agenterna innan de överförs till Log Analytics arbets ytan i Azure Monitor.



>[!NOTE]
> Även om agenter kommunicerar med varandra, genererar de inte betydande nätverks trafik samtidigt som de utför testerna. Agenter är endast beroende av TCP-SYNACK – ACK-handskaknings paket för att fastställa förlust och latens. Inga data paket utbyts. Under den här processen kommunicerar agenter med varandra endast när det behövs. Agent kommunikations sto pol Ogin är optimerad för att minska nätverks trafiken.

## <a name="use-the-solution"></a>Använd lösningen 

### <a name="network-performance-monitor-overview-tile"></a>Panelen Övervakare av nätverksprestanda översikt 

När du har aktiverat Övervakare av nätverksprestanda lösning ger lösnings panelen på sidan **Översikt** en snabb översikt över nätverks hälsan. 

 ![Panelen Övervakare av nätverksprestanda översikt](media/network-performance-monitor/npm-overview-tile.png)

### <a name="network-performance-monitor-dashboard"></a>Övervakare av nätverksprestanda instrument panel 

* **Främsta nätverks hälso händelser**: den här sidan innehåller en lista över de senaste hälso händelserna och aviseringarna i systemet och tiden sedan händelserna har varit aktiva. En hälso händelse eller en avisering genereras när värdet för det valda måttet (förlust, svars tid, svars tid eller bandbredds användning) för övervaknings regeln överskrider tröskelvärdet. 

* **ExpressRoute-övervakare**: den här sidan innehåller hälso översikter för de olika ExpressRoute-peering-anslutningarna. Panelen **topologi** visar antalet nätverks Sök vägar genom de ExpressRoute-kretsar som övervakas i nätverket. Välj den här panelen för att gå till vyn **topologi** .

* **Övervakare för tjänst anslutning**: den här sidan innehåller hälso översikter för de olika test som du har skapat. Panelen **topologi** visar antalet slut punkter som övervakas. Välj den här panelen för att gå till vyn **topologi** .

* **Prestanda övervakare**: den här sidan innehåller hälso översikter för **nätverks** länkar och under **nätverks** länkar som lösningen övervakar. Panelen **topologi** visar antalet nätverks Sök vägar som övervakas i nätverket. Välj den här panelen för att gå till vyn **topologi** . 

* **Vanliga frågor**: den här sidan innehåller en uppsättning Sök frågor som hämtar rå data för nätverks övervakning direkt. Du kan använda dessa frågor som utgångs punkt för att skapa egna frågor för anpassad rapportering. 

   ![Övervakare av nätverksprestanda instrument panel](media/network-performance-monitor/npm-dashboard.png)

 

### <a name="drill-down-for-depth"></a>Öka detalj nivån för djupet 

Du kan välja olika länkar på instrument panelen för lösningen för att öka detalj nivån i alla intresse områden. Om du till exempel ser en avisering eller en nätverks länk som inte är felfri visas på instrument panelen, väljer du den för att undersöka ytterligare. En sida visar alla under nätverks Länkar för den specifika nätverks länken. Du kan se förlust, svars tid och hälso status för varje under nätverks länk. Du kan snabbt ta reda på vilken under nätverks länk som orsakar problem. Välj **Visa noden länkar** om du vill se alla länkar för länken för den felaktiga under nätet. Sedan kan du se enskilda Länkar för nod-till-nod och hitta länkar för felaktiga noder. 

Välj **Visa topologi** för att Visa hopp-för-hop-topologin för vägarna mellan käll-och mål-noderna. De felaktiga vägarna visas i rött. Du kan visa svars tiden som har bidragit till varje hopp så att du snabbt kan identifiera problemet i en viss del av nätverket.

 

### <a name="network-state-recorder-control"></a>Kontroll av nätverks tillstånds inspelning

Varje vy visar en ögonblicks bild av din nätverks hälsa vid en viss tidpunkt. Som standard visas det senaste läget. Fältet längst upp på sidan visar den tidpunkt då statusen visas. Om du vill visa en ögonblicks bild av din nätverks hälsa vid en tidigare tidpunkt väljer du **åtgärder**. Du kan också aktivera eller inaktivera automatisk uppdatering för alla sidor medan du visar det senaste läget. 

 ![Registrering av nätverks tillstånd](media/network-performance-monitor/network-state-recorder.png)

 

### <a name="trend-charts"></a>Trend diagram 

På varje nivå som du ökar detalj nivån kan du se trenden för det aktuella måttet. Det kan vara förlust, svars tid, svars tid eller bandbredds användning. Om du vill ändra tids perioden för trenden använder du tids kontrollen överst i diagrammet. 

Trend diagram visar ett historik perspektiv för prestanda måttets prestanda. Vissa nätverks problem är tillfälliga och är svåra att fånga genom att bara titta på nätverkets aktuella tillstånd. Problem kan snabbt och försvinna innan alla meddelanden visas vid en senare tidpunkt. Sådana tillfälliga problem kan också vara svåra för program administratörer. Problemen visas ofta som en klar ökning av programmets svars tid, även när alla program komponenter verkar köras smidigt. 

Du kan enkelt identifiera dessa typer av problem genom att titta på ett trend diagram. Problemet visas som en plötslig topp i nätverks fördröjningen eller paket förlusten. Du kan undersöka problemet genom att använda kontrollen nätverks tillstånds kontroll för att Visa nätverks ögonblicks bilden och topologin för den tidpunkten när problemet uppstod.

 
![Trend diagram](media/network-performance-monitor/trend-charts.png)
 

### <a name="topology-map"></a>Topology-karta 

Övervakare av nätverksprestanda visar en hopp-till-hopp-topologi mellan käll-och mål slut punkten i en interaktiv Topology-karta. Om du vill visa topologins karta väljer du panelen **topologi** på lösningens instrument panel. Du kan också välja länken **Visa topologi** på sidan öka detalj nivån. 

Topology-kartan visar hur många vägar som är mellan källan och målet och vilka sökvägar som data paketen tar. Den svars tid som varje nätverks hopp bidragit med visas också. Alla sökvägar för vilka svars tiden för den totala sökvägen är över tröskelvärdet (anges i motsvarande övervaknings regel) visas i rött. 

När du väljer en nod eller hovrar över den i Topology-kartan visas nodens egenskaper, till exempel FQDN och IP-adress. Välj ett hopp för att se dess IP-adress. Du kan identifiera problematiska nätverks hopp genom att märker den fördröjning det bidrar till. Om du vill filtrera särskilda vägar använder du filtren i fönstret komprimerbar åtgärd. Om du vill förenkla nätverkets topologier döljer du mellanliggande hopp med hjälp av skjutreglaget i åtgärds fönstret. Du kan zooma in eller ut ur topologins karta med hjälp av mus hjulet. 

Topologin som visas i kartan är Layer 3-topologi och innehåller inte Layer 2-enheter och-anslutningar. 

 
![Topology-karta](media/network-performance-monitor/topology-map.png)
 

## <a name="log-queries-in-azure-monitor"></a>Logg frågor i Azure Monitor

Alla data som visas grafiskt via Övervakare av nätverksprestanda instrument panelen och detalj nivå sidorna är också tillgängliga i [logg frågor](../logs/log-query-overview.md). Du kan utföra interaktiv analys av data i lagrings platsen och korrelera data från olika källor. Du kan också skapa anpassade aviseringar och vyer och exportera data till Excel, Power BI eller en delnings bar länk. Områdena **vanliga frågor** på instrument panelen har några användbara frågor som du kan använda som start punkt för att skapa egna frågor och rapporter. 

## <a name="alerts"></a>Aviseringar

Övervakare av nätverksprestanda använder aviserings funktionerna i [Azure Monitor](../alerts/alerts-overview.md).

Det innebär att alla meddelanden hanteras med hjälp av [Åtgärds grupper](../alerts/action-groups.md).  

Om du är en NPM-användare som skapar en avisering via Log Analytics: 
1. Du ser en länk som kommer att omdirigera dig till Azure Portal. Klicka på den för att få åtkomst till portalen.
2. Klicka på panelen Övervakare av nätverksprestanda lösning. 
3. Navigera till konfigurera.  
4. Välj det test som du vill skapa en avisering på och följ anvisningarna nedan.

Om du är en NPM-användare som skapar en avisering via Azure Portal:  
1. Du kan välja att ange din e-postadress direkt eller så kan du välja att skapa aviseringar via åtgärds grupper.
2. Om du väljer att ange din e-postadress direkt skapas en åtgärds grupp med namnet **NPM e-ActionGroup** och e-post-ID: t läggs till i den åtgärds gruppen.
3. Om du väljer att använda åtgärds grupper måste du välja en tidigare skapad åtgärds grupp. Du kan lära dig hur du skapar en åtgärds grupp [här.](../alerts/action-groups.md#create-an-action-group-by-using-the-azure-portal) 
4. När aviseringen har skapats kan du använda länken Hantera aviseringar för att hantera dina aviseringar. 

Varje gång du skapar en avisering skapar NPM en fråga baserad logg aviserings regel i Azure Monitor. Den här frågan utlöses var 5: e minut som standard. Azure Monitor debiterar inte för de första 250 logg aviserings reglerna som skapats, och eventuella varnings regler ovanför gränsen på 250 logg aviserings regler debiteras enligt [priserna för varningar på sidan Azure Monitor prissättning](https://azure.microsoft.com/pricing/details/monitor/).
Meddelanden debiteras separat enligt [priset per meddelande på sidan Azure Monitor prissättning](https://azure.microsoft.com/pricing/details/monitor/).


## <a name="pricing"></a>Prissättning

Information om priser är tillgänglig [online](network-performance-monitor-pricing-faq.md).

## <a name="provide-feedback"></a>Ge feedback 

* **UserVoice:** Du kan publicera dina idéer för Övervakare av nätverksprestanda funktioner som vi vill att vi ska arbeta med. Besök [sidan UserVoice](https://feedback.azure.com/forums/267889-log-analytics/category/188146-network-monitoring). 

* **Delta i vår kohort:** Vi är alltid intresserade av att låta nya kunder delta i vår kohort. Som en del av det får du tidig till gång till nya funktioner och en möjlighet att hjälpa oss att förbättra Övervakare av nätverksprestanda. Om du är intresse rad av att ansluta kan du fylla i den här [snabb undersökningen](https://aka.ms/npmcohort). 

## <a name="next-steps"></a>Nästa steg 
Läs mer om [prestanda övervakaren](network-performance-monitor-performance-monitor.md), [tjänst anslutnings övervakaren](network-performance-monitor-performance-monitor.md)och [ExpressRoute-övervakaren](network-performance-monitor-expressroute.md).