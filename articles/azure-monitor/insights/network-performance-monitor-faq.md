---
title: Vanliga frågor och svar – Övervakare av nätverksprestanda lösning i Azure | Microsoft Docs
description: Den här artikeln innehåller vanliga frågor om Övervakare av nätverksprestanda i Azure. Övervakare av nätverksprestanda (NPM) hjälper dig att övervaka nätverkets prestanda i nära real tid och identifiera och hitta Flask halsar i nätverks prestanda.
ms.subservice: logs
ms.topic: conceptual
author: vinynigam
ms.author: vinigam
ms.date: 10/12/2018
ms.openlocfilehash: 8e26a4d996e4c1767856b3dd98ae318d8992a89b
ms.sourcegitcommit: 61d2b2211f3cc18f1be203c1bc12068fc678b584
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2021
ms.locfileid: "98562700"
---
# <a name="network-performance-monitor-solution-faq"></a>Vanliga frågor om Övervakare av nätverksprestanda-lösning

![Övervakare av nätverksprestanda symbol](media/network-performance-monitor-faq/npm-symbol.png)

Den här artikeln innehåller vanliga frågor och svar om Övervakare av nätverksprestanda (NPM) i Azure

[Övervakare av nätverksprestanda](../../networking/network-monitoring-overview.md) är en molnbaserad [hybrid nätverks övervaknings](./network-performance-monitor-performance-monitor.md) lösning som hjälper dig att övervaka nätverks prestanda mellan olika platser i din nätverks infrastruktur. Du kan också övervaka nätverks anslutningen till [tjänst-och program slut punkter](./network-performance-monitor-service-connectivity.md) och [övervaka Azure-ExpressRoute prestanda](./network-performance-monitor-expressroute.md). 

Övervakare av nätverksprestanda identifierar nätverks problem som trafik blackholing, synkroniseringsfel och problem som konventionella nätverks övervaknings metoder inte kan identifiera. Lösningen genererar aviseringar och meddelar dig när ett tröskelvärde överskrids för en nätverkslänk. Detta säkerställer att problem med nätverksprestanda upptäcks i god tid och att källan till problemet kan ringas in till ett visst nätverkssegment eller enhet. 

Mer information om de olika funktionerna som stöds av [övervakare av nätverksprestanda](../../networking/network-monitoring-overview.md) finns online.

## <a name="set-up-and-configure-agents"></a>Konfigurera och konfigurera agenter

### <a name="what-are-the-platform-requirements-for-the-nodes-to-be-used-for-monitoring-by-npm"></a>Vilka är plattforms kraven för de noder som ska användas för övervakning av NPM?
Nedan visas plattforms kraven för NPM olika funktioner:

- NPM för prestanda övervakning och tjänst anslutnings funktioner stöder både Windows Server och Windows-datorer/-klient operativ system. Windows Server OS-versioner som stöds är 2008 SP1 eller senare. Windows-datorer/-klient versioner som stöds är Windows 10, Windows 8,1, Windows 8 och Windows 7. 
- NPM för ExpressRoute-övervakaren stöder bara Windows Server (2008 SP1 eller senare) operativ system.

### <a name="can-i-use-linux-machines-as-monitoring-nodes-in-npm"></a>Kan jag använda Linux-datorer som övervaknings-noder i NPM?
Funktionen för att övervaka nätverk som använder Linux-baserade noder är nu allmänt tillgänglig. Öppna agenten [här](../../virtual-machines/extensions/oms-linux.md). Linux-agenter tillhandahåller endast övervaknings funktioner för prestanda övervakaren i NPM och är inte tillgängliga för övervaknings funktionerna för tjänst anslutning och ExpressRoute

### <a name="what-are-the-size-requirements-of-the-nodes-to-be-used-for-monitoring-by-npm"></a>Vilka är storleks kraven för noderna som ska användas för övervakning av NPM?
För att köra NPM-lösningen på nod-VM: ar för att övervaka nätverk ska noderna ha minst 500 MB minne och en kärna. Du behöver inte använda separata noder för att köra NPM. Lösningen kan köras på noder som har andra arbets belastningar som körs på den. Lösningen har möjlighet att stoppa övervaknings processen om den använder mer än 5% processor.

### <a name="to-use-npm-should-i-connect-my-nodes-as-direct-agent-or-through-system-center-operations-manager"></a>Ska jag använda NPM för att ansluta mina noder som direkt agent eller via System Center Operations Manager?
Både prestanda övervakaren och tjänsten för tjänst anslutnings övervakaren stöder noder [som är anslutna som direkta agenter](../platform/agent-windows.md) och [anslutna via Operations Manager](../platform/om-agents.md).

För ExpressRoute Monitor-kapacitet ska Azure-noderna endast anslutas som direkta agenter. Azure-noder som är anslutna via Operations Manager stöds inte. Noder som är anslutna som direkta agenter och via Operations Manager stöds för att övervaka en ExpressRoute-krets för lokala noder.

### <a name="which-protocol-among-tcp-and-icmp-should-be-chosen-for-monitoring"></a>Vilka protokoll mellan TCP och ICMP ska väljas för övervakning?
Om du övervakar nätverket med hjälp av Windows Server-baserade noder rekommenderar vi att du använder TCP som övervaknings protokoll eftersom det ger bättre noggrannhet. 

ICMP rekommenderas för noder i Windows-datorer/klient operativ system. Den här plattformen tillåter inte att TCP-data skickas över RAW-socketar som används av NPM för att identifiera nätverkstopologi.

Du kan få mer information om de relativa fördelarna med varje protokoll [här](./network-performance-monitor-performance-monitor.md#choose-the-protocol).

### <a name="how-can-i-configure-a-node-to-support-monitoring-using-tcp-protocol"></a>Hur gör jag för att konfigurera en nod för att stödja övervakning med TCP-protokoll?
För noden för att stödja övervakning med TCP-protokoll: 
* Se till att Node Platform är Windows Server (2008 SP1 eller senare).
* Kör [EnableRules.ps1](https://aka.ms/npmpowershellscript) PowerShell-skript på noden. Se [anvisningar](./network-performance-monitor.md#configure-log-analytics-agents-for-monitoring) för mer information.


### <a name="how-can-i-change-the-tcp-port-being-used-by-npm-for-monitoring"></a>Hur kan jag ändra TCP-porten som används av NPM för övervakning?
Du kan ändra TCP-porten som används av NPM för övervakning genom att köra [EnableRules.ps1](https://aka.ms/npmpowershellscript) -skriptet. Du måste ange det port nummer som du vill använda som parameter. Om du till exempel vill aktivera TCP på port 8060 kör du `EnableRules.ps1 8060` . Se till att du använder samma TCP-port på alla noder som används för övervakning.

Skriptet konfigurerar endast Windows-brandväggen lokalt. Om du har regler för nätverks brand vägg eller nätverks säkerhets grupp (NSG) kontrollerar du att de tillåter trafik till TCP-porten som används av NPM.

### <a name="how-many-agents-should-i-use"></a>Hur många agenter ska jag använda?
Du bör använda minst en agent för varje undernät som du vill övervaka.

### <a name="what-is-the-maximum-number-of-agents-i-can-use-or-i-see-error--youve-reached-your-configuration-limit"></a>Det maximala antalet agenter jag kan använda eller jag ser fel meddelandet ".... har du nått din konfigurations gräns "?
NPM begränsar antalet IP-adresser till 5000 IP-adresser per arbets yta. Om en nod har både IPv4-och IPv6-adresser räknas detta som två IP-adresser för noden. Den här gränsen på 5000 IP-adresser skulle därför bestämma den övre gränsen för antalet agenter. Du kan ta bort inaktiva agenter från fliken noder i NPM >> konfigurera. NPM upprätthåller också historiken för alla IP-adresser som någonsin har tilldelats den virtuella dator som är värd för agenten och var och en räknas som separat IP-adress som bidrar till den övre gränsen på 5000 IP-adresser. Om du vill frigöra IP-adresser för din arbets yta kan du använda sidan noder för att ta bort de IP-adresser som inte används.

## <a name="monitoring"></a>Övervakning

### <a name="how-are-loss-and-latency-calculated"></a>Hur beräknas förlust och svars tid
Käll agenter skickar antingen TCP/versal-begäranden (om TCP väljs som protokoll för övervakning) eller ICMP-EKOBEGÄRAN (om ICMP väljs som protokoll för övervakning) till målets IP-adress med jämna mellanrum för att säkerställa att alla sökvägar mellan käll-IP-kombinationen omfattas. Procent andelen mottagna paket och svars tid för Packet mäts för att beräkna förlust och svars tid för varje sökväg. Dessa data sammanställs under avsöknings intervallet och över alla sökvägar för att hämta de sammanställda värdena för förlust och svars tid för IP-kombinationen för det specifika avsöknings intervallet.

### <a name="with-what-frequency-does-the-source-agent-send-packets-to-the-destination-for-monitoring"></a>I vilken frekvens skickar käll agenten paket till målet för övervakning?
För prestanda övervakaren och ExpressRoute övervakar källan paketen var femte sekund och registrerar nätverks måtten. Dessa data sammanställs under ett avsöknings intervall på tre minuter för att beräkna de genomsnittliga och högsta värdena för förlust och latens. För övervakning av tjänst anslutnings funktioner bestäms frekvensen för hur många paket som ska skickas för nätverks mätning av den frekvens som användaren har angett för det aktuella testet när testet konfigureras.

### <a name="how-many-packets-are-sent-for-monitoring"></a>Hur många paket skickas för övervakning?
Antalet paket som skickas av käll agenten till målet i en avsökning är anpassningsbart och bestäms av vår tillverkarspecifika algoritm, som kan vara olika för olika nätverkstopologier. Fler nätverks Sök vägar mellan käll målets IP-kombination, mer är antalet paket som skickas. Systemet ser till att alla sökvägar mellan IP-kombinationen för käll målet täcks.

### <a name="how-does-npm-discover-network-topology-between-source-and-destination"></a>Hur upptäcker NPM nätverks sto pol Ogin mellan källa och mål?
NPM använder en tillverkarspecifik algoritm baserat på traceroute för att identifiera alla sökvägar och hopp mellan källa och mål.

### <a name="does-npm-provide-routing-and-switching-level-info"></a>Tillhandahåller NPM information om Routning och växlings nivå 
Även om NPM kan identifiera alla möjliga vägar mellan käll agenten och målet, ger den ingen insyn i vilken väg som har tagits emot av de paket som har skickats av dina speciella arbets belastningar. Lösningen kan hjälpa dig att identifiera Sök vägarna och underliggande nätverks hopp, vilket ökar svars tiden än förväntat.

### <a name="why-are-some-of-the-paths-unhealthy"></a>Varför är vissa av Sök vägarna felaktiga?
Olika nätverks Sök vägar kan finnas mellan käll-och mål-IP-adresser och varje sökväg kan ha olika värde för förlust och svars tid. NPM markerar dessa sökvägar som felaktiga (med röd färg) för vilka värdena för förlust och/eller svars tid är större än respektive tröskelvärde som angetts i övervaknings konfigurationen.

### <a name="what-does-a-hop-in-red-color-signify-in-the-network-topology-map"></a>Vad indikerar en hopp i röd färg i nätverk sto pol Map?
Om ett hopp är rött betyder det att det är en del av minst en felaktig sökväg. NPM markerar bara Sök vägarna som felaktiga, den särskiljer inte hälso status för varje sökväg. Om du vill identifiera problematiska-hoppen kan du Visa hopp svars tiden och särskilja de som lägger till mer än förväntad svars tid.

### <a name="how-does-fault-localization-in-performance-monitor-work"></a>Hur fungerar fel lokalisering i prestanda övervakaren?
I NPM används en Probabilistic-mekanism för att tilldela fel sannolikhet till varje nätverks Sök väg, nätverks segment och komponenternas nätverks hopp baserat på antalet felaktiga sökvägar som de är en del av. När nätverks segmenten och hoppen blir en del av fler felaktiga sökvägar ökar sannolikheten för att risken för fel. Den här algoritmen fungerar bäst om du har många noder med NPM-agenten ansluten till varandra eftersom detta ökar data punkterna för att beräkna fel sannolikheten.

### <a name="how-can-i-create-alerts-in-npm"></a>Hur kan jag skapa aviseringar i NPM?
För närvarande går det inte att skapa aviseringar från NPM-gränssnittet på grund av ett känt problem. [Skapa aviseringar manuellt](../platform/alerts-log.md).

### <a name="what-are-the-default-log-analytics-queries-for-alerts"></a>Vad är standard Log Analytics frågor för aviseringar
Fråga om prestanda övervakaren

```kusto
NetworkMonitoring
 | where (SubType == "SubNetwork" or SubType == "NetworkPath") 
 | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy") and RuleName == "<<your rule name>>"
```

Fråga om tjänst anslutnings övervakare

```kusto
NetworkMonitoring
 | where (SubType == "EndpointHealth" or SubType == "EndpointPath")
 | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or ServiceResponseHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy") and TestName == "<<your test name>>"
```

ExpressRoute övervaka frågor: krets frågor

```kusto
NetworkMonitoring
 | where (SubType == "ERCircuitTotalUtilization") and (UtilizationHealthState == "Unhealthy") and CircuitResourceId == "<<your circuit resource ID>>"
```

Privat peering

```kusto
NetworkMonitoring
 | where (SubType == "ExpressRoutePeering" or SubType == "ERVNetConnectionUtilization" or SubType == "ExpressRoutePath")   
 | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") and CircuitName == "<<your circuit name>>" and VirtualNetwork == "<<vnet name>>"
```

Microsoft-peering

```kusto
NetworkMonitoring
 | where (SubType == "ExpressRoutePeering" or SubType == "ERMSPeeringUtilization" or SubType == "ExpressRoutePath")
 | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") and CircuitName == ""<<your circuit name>>" and PeeringType == "MicrosoftPeering"
```

Vanlig fråga

```kusto
NetworkMonitoring
 | where (SubType == "ExpressRoutePeering" or SubType == "ERVNetConnectionUtilization" or SubType == "ERMSPeeringUtilization" or SubType == "ExpressRoutePath")
 | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy")
```

### <a name="can-npm-monitor-routers-and-servers-as-individual-devices"></a>Kan NPM övervaka routrar och servrar som enskilda enheter?
NPM identifierar bara IP-och värd namnet för underliggande nätverks hopp (växlar, routrar, servrar osv.) mellan käll-och mål-IP-adresser. Den identifierar även svars tiden mellan dessa identifierade hopp. Dessa underliggande hopp övervakas inte separat.

### <a name="can-npm-be-used-to-monitor-network-connectivity-between-azure-and-aws"></a>Kan NPM användas för att övervaka nätverks anslutningen mellan Azure och AWS?
Ja. Mer information finns i artikeln [övervaka Azure, AWS och lokala nätverk med hjälp av NPM](/archive/blogs/msoms/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor) .

### <a name="is-the-expressroute-bandwidth-usage-incoming-or-outgoing"></a>Är ExpressRoute bandbredds användning inkommande eller utgående?
Bandbredds användning är summan av inkommande och utgående bandbredd. Den uttrycks i bitar per sekund.

### <a name="can-we-get-incoming-and-outgoing-bandwidth-information-for-the-expressroute"></a>Kan vi hämta information om inkommande och utgående bandbredd för ExpressRoute?
Inkommande och utgående värden för både primär och sekundär bandbredd kan fångas.

För information om MS peering-nivå använder du nedanstående fråga i loggs ökning

```kusto
NetworkMonitoring
 | where SubType == "ERMSPeeringUtilization"
 | project CircuitName,PeeringName,BitsInPerSecond,BitsOutPerSecond 
```

För information om privat peering-nivå använder du ovanstående fråga i loggs ökning

```kusto
NetworkMonitoring
 | where SubType == "ERVNetConnectionUtilization"
 | project CircuitName,PeeringName,BitsInPerSecond,BitsOutPerSecond
```

För information om krets nivåer använder du den angivna frågan i loggs ökning

```kusto
NetworkMonitoring
 | where SubType == "ERCircuitTotalUtilization"
 | project CircuitName, BitsInPerSecond, BitsOutPerSecond
```

### <a name="which-regions-are-supported-for-npms-performance-monitor"></a>Vilka regioner stöds för NPM-prestanda övervakaren?
NPM kan övervaka anslutningar mellan nätverk i valfri del av världen, från en arbets yta som finns i någon av de [regioner som stöds](./network-performance-monitor.md#supported-regions)

### <a name="which-regions-are-supported-for-npms-service-connectivity-monitor"></a>Vilka regioner stöds av tjänst anslutnings övervakaren i NPM?
NPM kan övervaka anslutningar till tjänster i valfri del av världen, från en arbets yta som finns i någon av de [regioner som stöds](./network-performance-monitor.md#supported-regions)

### <a name="which-regions-are-supported-for-npms-expressroute-monitor"></a>Vilka regioner stöds för NPM ExpressRoute-övervakaren?
NPM kan övervaka dina ExpressRoute-kretsar som finns i valfri Azure-region. Om du vill publicera till NPM måste du ha en Log Analytics arbets yta som måste finnas i någon av de [regioner som stöds](../../expressroute/how-to-npm.md)

## <a name="troubleshoot"></a>Felsöka

### <a name="why-are-some-of-the-hops-marked-as-unidentified-in-the-network-topology-view"></a>Varför har vissa hopp marker ATS som oidentifierade i vyn nätverkstopologi?
NPM använder en modifierad version av traceroute för att identifiera topologin från käll agenten till målet. Ett oidentifierat hopp representerar att nätverks hoppet inte svarade på käll agentens traceroute-begäran. Om tre efterföljande nätverks hopp inte svarar på agentens traceroute, markerar lösningen de svar som inte svarar som oidentifierade och försöker inte identifiera fler hopp.

Ett hopp kan inte svara på en traceroute i ett eller flera av följande scenarier:

* Routrarna har kon figurer ATS för att inte avslöja sin identitet.
* Nätverks enheterna tillåter inte ICMP_TTL_EXCEEDED trafik.
* En brand vägg blockerar ICMP_TTL_EXCEEDED svaret från nätverks enheten.

När någon av slut punkterna är i Azure visar traceroute identifierade hopp eftersom Azure-infrastrukturen inte avslöjar identiteten för traceroute. 

### <a name="i-get-alerts-for-unhealthy-tests-but-i-do-not-see-the-high-values-in-npms-loss-and-latency-graph-how-do-i-check-what-is-unhealthy"></a>Jag får aviseringar om fel test, men jag ser inte de höga värdena i NPM förlust och svars tid. Hur gör jag för att kontrollerar du vad som är ohälsosamt?
NPM genererar en avisering om slut punkt till slut punkt mellan källa och mål korsar tröskelvärdet för valfri sökväg mellan dem. Vissa nätverk har flera vägar som ansluter samma källa och mål. NPM genererar en avisering. sökvägen är felaktig. Förlusten och svars tiden som visas i graferna är det genomsnittliga värdet för alla sökvägar, och därför kanske det inte visar exakt värdet för en enskild sökväg. För att förstå var tröskelvärdet har brutits, kan du leta efter kolumnen "undertyp" i aviseringen. Om problemet orsakas av en sökväg, kommer under typens värde att NetworkPath (för test av prestanda övervakare), EndpointPath (för övervakning av tjänst anslutnings övervakare) och ExpressRoutePath (för ExpressRotue övervaknings test). 

Exempel frågan för att hitta är en felaktig sökväg:

```kusto
NetworkMonitoring
 | where ( SubType == "ExpressRoutePath")
 | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") and CircuitResourceID =="<your ER circuit ID>" and ConnectionResourceId == "<your ER connection resource id>"
 | project SubType, LossHealthState, LatencyHealthState, MedianLatency
```

### <a name="why-does-my-test-show-unhealthy-but-the-topology-does-not"></a>Varför visar mitt test inte felfri men topologin har inte 
NPM övervakar avbrott i slut punkt till slut punkt, svars tid och topologi med olika intervall. Förlust och svars tider mäts en gång var femte sekund och sammanställt var tredje minut (för prestanda övervakaren och Express Route Monitor) medan topologin beräknas med traceroute en gång var tionde minut. 4:04 3:44 till exempel kan topologin uppdateras tre gånger (3:44, 3:54, 4:04), men förlust och svars tid uppdateras ungefär sju gånger (3:44, 3:47, 3:50, 3:53, 3:56, 3:59, 4:02). Topologin som genereras vid 3:54 kommer att återges för den förlust och svars tid som beräknas vid 3:56, 3:59 och 4:02. Anta att du får en avisering om att ER-kretsen var ohälsosam vid 3:59. Du loggar in på NPM och försöker ange topologins tid till 3:59. NPM kommer att återge topologin som genererats vid 3:54. För att förstå den senaste kända topologin i nätverket, jämför fälten TimeProcessed (tid då förlust och svars tid beräknades) och TracerouteCompletedTime (tid då topologin beräknades). 

### <a name="what-is-the-difference-between-the-fields-e2emedianlatency-and-avghoplatencylist-in-the-networkmonitoring-table"></a>Vad är skillnaden mellan fälten E2EMedianLatency och AvgHopLatencyList i tabellen NetworkMonitoring
E2EMedianLatency är svars tiden uppdaterad var tredje minut efter att ha samlat in resultaten av TCP ping-tester, medan AvgHopLatencyList uppdateras var tionde minut baserat på traceroute. Använd fältet TimeProcessed för att förstå exakt den tid då E2EMedianLatency beräknades. Om du vill förstå exakt hur lång tid traceroute slutfördes och uppdaterade AvgHopLatencyList använder du fältet TracerouteCompletedTime

### <a name="why-does-hop-by-hop-latency-numbers-differ-from-hoplatencyvalues"></a>Varför skiljer sig hopp på hopp-nummer från HopLatencyValues 
HopLatencyValues är källa till slut punkt.
Till exempel: humle-A, B, C. AvgHopLatency – 10, 15, 20. Det innebär att källa till en svars tid = 10, källa till B latens = 15 och källa till C-svars tid är 20. UI beräknar en-B-hopp fördröjning som 5 i topologin

### <a name="the-solution-shows-100-loss-but-there-is-connectivity-between-the-source-and-destination"></a>Lösningen visar 100% förlust, men det finns en anslutning mellan källan och målet
Detta kan inträffa om antingen värd brand väggen eller mellanliggande brand vägg (nätverks brand vägg eller Azure-NSG) blockerar kommunikationen mellan käll agenten och målet via den port som används för övervakning av NPM (som standard är porten 8084, om inte kunden har ändrat detta).

* Kontrol lera att värd brand väggen inte blockerar kommunikationen på den begärda porten genom att Visa hälso statusen för käll-och mål-noderna från följande vy: Övervakare av nätverksprestanda-> konfigurations > noder. 
  Om de inte är felfria kan du Visa anvisningarna och vidta lämpliga åtgärder. Om noderna är felfria flyttar du till steg b. nedan.
* Kontrol lera att en mellanliggande nätverks brand vägg eller Azure-NSG inte blockerar kommunikationen på den begärda porten genom att använda PsPing för tredje part med hjälp av anvisningarna nedan:
  * psping-verktyget är tillgängligt för nedladdning [här](/sysinternals/downloads/psping) 
  * Kör följande kommando från Källnoden.
    * psping-n 15 \<destination node IPAddress\> :P Ortnumber som standard använder NPM 8084-port. Om du uttryckligen har ändrat detta genom att använda EnableRules.ps1-skriptet anger du det anpassade port numret som du använder). Det här är ett ping från en Azure-dator till en lokal plats
* Kontrol lera att pingarna fungerar. Annars indikerar det att en mellanliggande nätverks brand vägg eller Azure-NSG blockerar trafiken på den här porten.
* Kör nu kommandot från målnod till nodens IP-adress.


### <a name="there-is-loss-from-node-a-to-b-but-not-from-node-b-to-a-why"></a>Det går förlorad från nod A till B, men inte från nod B till en. Varför?
Eftersom nätverks Sök vägarna mellan A och B kan skilja sig från nätverks Sök vägarna mellan B till en, kan olika värden för förlust och svars tid observeras.

### <a name="why-are-all-my-expressroute-circuits-and-peering-connections-not-being-discovered"></a>Varför identifieras inte alla mina ExpressRoute-kretsar och peering-anslutningar?
NPM identifierar nu ExpressRoute-kretsar och peering-anslutningar i alla prenumerationer som användaren har åtkomst till. Välj alla prenumerationer där dina ExpressRoute-resurser är länkade och aktivera övervakning för varje identifierad resurs. NPM söker efter anslutnings objekt när en privat peering identifieras, så kontrol lera om ett VNET är associerat med din peering. NPM identifierar inte kretsar och peering som finns i en annan klient organisation än den Log Analytics arbets ytan.

### <a name="the-er-monitor-capability-has-a-diagnostic-message-traffic-is-not-passing-through-any-circuit-what-does-that-mean"></a>ER-skärms funktion har ett diagnostiskt meddelande "trafiken passerar inte genom någon krets". Vad innebär det?

Det kan finnas ett scenario där det finns en felfri anslutning mellan de lokala och Azure-noderna, men trafiken går inte över ExpressRoute-kretsen som kon figurer ATS för att övervakas av NPM. 

Detta kan inträffa i följande fall:

* ER-kretsen är inte tillgänglig.
* Flödes filtren konfigureras på ett sådant sätt att de ger prioritet till andra vägar (t. ex. en VPN-anslutning eller en annan ExpressRoute-krets) över avsedd ExpressRoute-krets. 
* De lokala och Azure-noderna som väljs för att övervaka ExpressRoute-kretsen i övervaknings konfigurationen har ingen anslutning till varandra via den avsedda ExpressRoute-kretsen. Se till att du har valt rätt noder som har anslutning till varandra över den ExpressRoute-krets som du vill övervaka.

### <a name="why-does-expressroute-monitor-report-my-circuitpeering-as-unhealthy-when-it-is-available-and-passing-data"></a>Varför övervakar ExpressRoute min krets/peering som ohälsosam när den är tillgänglig och skickar data.
ExpressRoute-övervakaren jämför nätverks prestanda värden (förlust, fördröjning och bandbredds användning) som rapporteras av agenter/tjänsten med tröskelvärdena som anges under konfigurationen. Om bandbredds användningen som rapporteras är större än tröskelvärdet i konfigurationen är kretsen markerad som ohälsosam. För peer-koppling är peer-kopplingen markerad som ohälsosam om förlusten, fördröjningen eller bandbredds användningen som rapporteras är större än tröskelvärdet som angetts i konfigurationen. NPM använder inte mått eller någon annan form av data för att deicde hälso tillstånd.

### <a name="why-does-expressroute-monitorbandwidth-utilisation-report-a-value-differrent-from-metrics-bits-inout"></a>Varför rapporterar ExpressRoute Monitor'bandwidth-användning ett värde annan från mått bitar in/ut
För ExpressRoute-övervakaren är bandbredds utiliation genomsnittet av inkommande och utgående bandbredd under de senaste 20 minuterna uttryckt i bitar per sekund. För Express Route-mått är bit in/ut per minut data punkter. Internt som används för båda är samma, men agg valies mellan NPM och ER-mått. För detaljerad övervakning av minuter per minut och snabba aviseringar rekommenderar vi att du ställer in aviseringar direkt på ER-statistik

### <a name="while-configuring-monitoring-of-my-expressroute-circuit-the-azure-nodes-are-not-being-detected"></a>När du konfigurerar övervakning av min ExpressRoute-krets identifieras inte Azure-noderna.
Detta kan inträffa om Azure-noderna är anslutna via Operations Manager. ExpressRoute Monitor-kapacitet stöder bara de Azure-noder som är anslutna som direkta agenter.

### <a name="i-cannot-discover-by-expressroute-circuits-in-the-oms-portal"></a>Jag kan inte identifiera av ExpressRoute-kretsar i OMS-portalen
Även om NPM kan användas både från Azure Portal och OMS-portalen, fungerar krets identifieringen i ExpressRoute-övervakarens funktion bara genom Azure Portal. När kretsarna har identifierats via Azure Portal kan du använda funktionen i någon av de två portalerna. 

### <a name="in-the-service-connectivity-monitor-capability-the-service-response-time-network-loss-as-well-as-latency-are-shown-as-na"></a>I tjänst anslutnings övervakarens funktion, svars tiden för tjänsten, nätverks förlust, samt svars tid visas som NA
Detta kan inträffa om en eller flera är sanna:

* Tjänsten är avstängd.
* Noden som används för att kontrol lera nätverks anslutningen till tjänsten är avstängd.
* Målet som angavs i test konfigurationen är felaktigt.
* Noden har ingen nätverks anslutning.

### <a name="in-the-service-connectivity-monitor-capability-a-valid-service-response-time-is-shown-but-network-loss-as-well-as-latency-are-shown-as-na"></a>I tjänst anslutnings övervakarens funktion visas en giltig svars tid för tjänsten, men både nätverks förlust och svars tid visas som NA
 Detta kan inträffa om en eller flera är sanna:

* Om noden som används för att kontrol lera nätverks anslutningen till tjänsten är en Windows-klientdator blockerar antingen mål tjänsten ICMP-begäranden eller så blockerar den nätverks brand väggen ICMP-begäranden som kommer från noden.
* Kryss rutan utför nätverks mätningar är tom i test konfigurationen.

### <a name="in-the-service-connectivity-monitor-capability-the-service-response-time-is-na-but-network-loss-as-well-as-latency-are-valid"></a>I tjänst anslutnings övervakarens funktion är svars tiden för tjänsten, men både nätverks förlust och svars tid är giltiga
Detta kan inträffa om mål tjänsten inte är ett webb program, men testet är konfigurerat som ett webb test. Redigera test konfigurationen och välj test typ som nätverk i stället för webben.

## <a name="miscellaneous"></a>Övriga farliga ämnen

### <a name="is-there-a-performance-impact-on-the-node-being-used-for-monitoring"></a>Påverkas prestandan på noden som används för övervakning?
NPM-processen har kon figurer ATS att stoppa om den använder mer än 5% av värd processor resurserna. Detta är för att säkerställa att du kan fortsätta att använda noderna för sina vanliga arbets belastningar utan att påverka prestandan.

### <a name="does-npm-edit-firewall-rules-for-monitoring"></a>Kan NPM redigera brand Väggs regler för övervakning?
NPM skapar bara en lokal regel för Windows-brandväggen på noderna där EnableRules.ps1 PowerShell-skriptet körs för att tillåta agenterna att skapa TCP-anslutningar med varandra på den angivna porten. Lösningen ändrar inte någon nätverks brand vägg eller regler för nätverks säkerhets grupper (NSG).

### <a name="how-can-i-check-the-health-of-the-nodes-being-used-for-monitoring"></a>Hur kan jag kontrol lera hälso tillståndet för noderna som används för övervakning?
Du kan visa hälso status för de noder som används för övervakning från följande vy: Övervakare av nätverksprestanda-> konfigurations > noder. Om en nod inte är felfri kan du Visa fel informationen och vidta den föreslagna åtgärden.

### <a name="can-npm-report-latency-numbers-in-microseconds"></a>Kan NPM rapportera latens i mikrosekunder?
NPM avrundar latens-talen i användar gränssnittet och i millisekunder. Samma data lagras med en högre precision (ibland upp till fyra decimaler).

## <a name="next-steps"></a>Nästa steg

- Läs mer om Övervakare av nätverksprestanda genom att referera till [övervakare av nätverksprestanda-lösning i Azure](./network-performance-monitor.md).

