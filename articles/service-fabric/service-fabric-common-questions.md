---
title: Vanliga frågor om Microsoft Azure Service Fabric
description: Vanliga frågor och svar om Service Fabric, inklusive funktioner, användnings fall och vanliga scenarier.
ms.topic: troubleshooting
ms.date: 08/18/2017
ms.author: pepogors
ms.openlocfilehash: 95463865c52ee501ceca22cae60d19a089236fd1
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105048705"
---
# <a name="commonly-asked-service-fabric-questions"></a>Vanliga frågor och svar om Service Fabric

Det finns många vanliga frågor om vad Service Fabric kan göra och hur det ska användas. Det här dokumentet beskriver många av dessa vanliga frågor och svar.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cluster-setup-and-management"></a>Kluster konfiguration och hantering

### <a name="how-do-i-roll-back-my-service-fabric-cluster-certificate"></a>Hur gör jag för att återställa min Service Fabric kluster certifikat?

Att återställa all uppgradering till programmet kräver identifiering av hälso tillstånd innan ditt Service Fabric-klusterkvorum genomför ändringen. genomförda ändringar kan bara vidarebefordras. Det kan vara nödvändigt att återställa klustret med hjälp av kund support tjänster, om en oövervakad ändring av certifikatet har introducerats.  [Service Fabric programmets program uppgradering](./service-fabric-application-upgrade.md) tillämpar [parametrarna för program uppgradering](./service-fabric-application-upgrade-parameters.md)och ger noll avbrotts löfte.  Efter den rekommenderade program uppgraderingens övervakade läge baseras automatisk förloppet via uppdaterings domäner på hälso kontroller som passerar, och återställs automatiskt om det inte går att uppdatera en standard tjänst.
 
Om klustret fortfarande använder den klassiska certifikatets tumavtryck-egenskap i din Resource Manager-mall, rekommenderar vi att du [ändrar klustret från certifikatets tumavtryck till ett eget namn](./service-fabric-cluster-change-cert-thumbprint-to-cn.md), för att utnyttja moderna hemligheter-hanterings funktioner.

### <a name="can-i-create-a-cluster-that-spans-multiple-azure-regions-or-my-own-datacenters"></a>Kan jag skapa ett kluster som sträcker sig över flera Azure-regioner eller mina egna data Center?

Ja. 

Kärn Service Fabric klustring teknik kan användas för att kombinera datorer som körs var som helst i världen, så länge de har nätverks anslutning till varandra. Det kan dock vara svårt att skapa och köra ett sådant kluster.

Om du är intresse rad av det här scenariot, uppmuntrar vi dig att kontakta antingen via [listan Service Fabric GitHub-problem](https://github.com/azure/service-fabric-issues) eller genom support representanten för att få ytterligare vägledning. Service Fabrics teamet arbetar för att ge ytterligare klarhet, vägledning och rekommendationer för det här scenariot. 

Några saker att tänka på: 

1. Service Fabric kluster resursen i Azure är regional idag, precis som de virtuella datorernas skalnings uppsättningar som klustret bygger på. Det innebär att om ett regionalt fel uppstår kan du förlora möjligheten att hantera klustret via Azure Resource Manager eller Azure Portal. Detta kan inträffa även om klustret fortfarande körs och du kommer att kunna interagera med det direkt. Dessutom erbjuder Azure idag inte möjligheten att ha ett enda virtuellt nätverk som kan användas i olika regioner. Det innebär att ett kluster med flera regioner i Azure kräver antingen [offentliga IP-adresser för varje virtuell dator i skalnings uppsättningar för virtuella datorer](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine) eller [Azure VPN-gatewayer](../vpn-gateway/vpn-gateway-about-vpngateways.md). De här nätverks valen har olika konsekvenser för kostnader, prestanda och en viss grad av program design, så noggrann analys och planering krävs innan du står inför en sådan miljö.
2. Underhåll, hantering och övervakning av de här datorerna kan bli krångligt, särskilt när de sträcker sig över olika _typer_ av miljöer, till exempel mellan olika moln leverantörer eller mellan lokala resurser och Azure. Det måste vara viktigt att se till att uppgraderingar, övervakning, hantering och diagnostik förstås för både klustret och programmen innan du kör produktions arbets belastningar i en sådan miljö. Om du redan har erfarenhet av att lösa dessa problem i Azure eller i dina egna data Center, är det troligt att samma lösningar kan användas när du skapar eller kör Service Fabric klustret. 

### <a name="do-service-fabric-nodes-automatically-receive-os-updates"></a>Får Service Fabric noder automatiskt uppdateringar av operativ systemet?

Du kan använda [Automatisk uppdatering av skalnings uppsättningar för virtuella datorer](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) allmänt tillgänglig funktion idag.

För kluster som inte körs i Azure har vi [tillhandahållit ett program](service-fabric-patch-orchestration-application.md) för att korrigera operativ systemen under Service Fabric noderna.

### <a name="can-i-use-large-virtual-machine-scale-sets-in-my-sf-cluster"></a>Kan jag använda stora skalnings uppsättningar för virtuella datorer i mitt SF-kluster? 

**Kort svar** – nej. 

**Långt svar** – även om stora skalnings uppsättningar för virtuella datorer låter dig skala en virtuell dator skalnings uppsättning upp till 1000 VM-instanser gör det det genom att använda placerings grupper (PGS). Fel domäner (fd) och uppgraderings domäner (UDs) är bara konsekventa inom en placerings grupp Service Fabric använder fd och UDs för att fatta beslut om tjänst repliker/tjänst instanser. Eftersom fd och UDs endast är jämförbara inom en placerings grupp, kan inte SF använda den. Om t. ex. VM1 i SG1 har en topologi av FD = 0 och VM9 i SG2 har en topologi av FD = 4, betyder det inte att VM1 och VM2 finns på två olika maskin varu rack, och därför kan SF inte använda FD-värdena i det här fallet för att fatta beslut om placering.

Det finns för närvarande andra problem med stora skalnings uppsättningar för virtuella datorer, t. ex. brist på nivå 4 belastnings Utjämnings stöd. Se för [information om stora skalnings uppsättningar](../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md)



### <a name="what-is-the-minimum-size-of-a-service-fabric-cluster-why-cant-it-be-smaller"></a>Vilken är minimi storleken på ett Service Fabric kluster? Varför kan det inte vara mindre?

Den minsta storlek som stöds för ett Service Fabric kluster som kör produktions arbets belastningar är fem noder. För utvecklings scenarier har vi stöd för en nod (optimerad för snabb utvecklings upplevelse i Visual Studio) och fem noder i klustret.

Ett produktions kluster måste ha minst fem noder på grund av följande tre orsaker:
1. Även om inga användar tjänster körs kör ett Service Fabric-kluster en uppsättning tillstånds känsliga system tjänster, inklusive namngivnings tjänsten och tjänsten failover Manager. Dessa system tjänster är nödvändiga för att klustret ska fortsätta att fungera.
2. Vi placerar alltid en replik av en tjänst per nod, så kluster storleken är den övre gränsen för antalet repliker som en tjänst (faktiskt en partition) kan ha.
3. Eftersom en kluster uppgradering kommer att ta minst en nod, vill vi ha en buffert på minst en nod, och vi vill därför att ett produktions kluster ska ha minst två noder *förutom* det lägsta. Minimi kravet är kvorumresursens storlek på en system tjänst enligt beskrivningen nedan.  

Vi vill att klustret ska vara tillgängligt i ett samtidigt fel i två noder. För att ett Service Fabric kluster ska vara tillgängligt måste system tjänsterna vara tillgängliga. Tillstånds känsliga system tjänster som namngivnings tjänst och failover Manager-tjänst, som spårar vilka tjänster som har distribuerats till klustret och var de finns, beror på stark konsekvens. Att stark konsekvens, i sin tur, beror på möjligheten att förvärva ett *kvorum* för en eventuell uppdatering av status för dessa tjänster, där ett kvorum representerar en strikt majoritet av replikerna (N/2 + 1) för en specifik tjänst. Om vi vill vara elastiska mot Samtidig förlust av två noder (alltså samtidigt förlust av två repliker av en system tjänst) måste vi ha ClusterSize-QuorumSize >= 2, vilket tvingar den minsta storleken att vara fem. Om du vill se att klustret har N noder och det finns N repliker av en system tjänst – en på varje nod. Kvorumresursens storlek för en system tjänst är (N/2 + 1). Ovanstående olikhet ser ut som N-(N/2 + 1) >= 2. Det finns två fall att tänka på: när N är jämnt och när N är udda. Om N är jämnt, säger du N = 2 \* m där m >= 1, ser olikheten ut som 2 \* m-(2 \* m/2 + 1) >= 2 eller m >= 3. Minimivärdet för N är 6 och uppnås när m = 3. Å andra sidan, om N är udda, säg N = 2 \* m + 1 där m >= 1, ser olikheten ut som 2 \* m + 1-((2 \* m + 1)/2 + 1) >= 2 eller 2 \* m + 1-(m + 1) >= 2 eller m >= 2. Minimivärdet för N är 5 och uppnås när m = 2. Därför är det bland alla värden av N som uppfyller ClusterSize-QuorumSize >= 2, minimivärdet är 5.

Obs! i ovanstående argument har vi förmodat att varje nod har en replik av en system tjänst, vilket innebär att kvorumdisken beräknas baserat på antalet noder i klustret. Genom att ändra *TargetReplicaSetSize* skulle vi dock kunna göra kvorumloggen mindre än (N/2 + 1), vilket kan ge intryck av att vi kan ha ett kluster som är mindre än 5 noder och fortfarande ha 2 extra noder ovanför kvorumresursens storlek. Om vi till exempel anger TargetReplicaSetSize till 3 i ett kluster med fyra noder, är kvorumresursens storlek baserad på TargetReplicaSetSize (3/2 + 1) eller 2, så vi har ClusterSize-QuorumSize = 4-2 >= 2. Vi kan dock inte garantera att system tjänsten kommer att finnas på eller över kvorum om vi förlorar några noder samtidigt, men det kan bero på att de två noderna som vi förlorade var värdar för två repliker, så att system tjänsten kommer att gå förlorade i kvorum (med en enda replik kvar) och kommer att bli otillgänglig.

Med den bakgrunden undersöker vi några tänkbara klusterkonfigurationer:

**En nod**: det här alternativet ger inte hög tillgänglighet eftersom förlusten av den enskilda noden av någon anledning innebär att hela klustret förloras.

**Två noder**: ett kvorum för en tjänst som distribuerats över två noder (N = 2) är 2 (2/2 + 1 = 2). När en enskild replik går förlorad är det omöjligt att skapa ett kvorum. Eftersom utförandet av en tjänst uppgradering kräver tillfälligt en replik, är detta inte en användbar konfiguration.

**Tre noder**: med tre noder (N = 3) är kravet på att skapa ett kvorum fortfarande två noder (3/2 + 1 = 2). Det innebär att du kan förlora en enskild nod och fortfarande upprätthålla kvorum, men ett samtidigt fel på två noder kommer att driva system tjänsterna till att förlora kvorum och kan orsaka att klustret blir otillgängligt.

**Fyra noder**: med fyra noder (N = 4) måste kravet för att skapa ett kvorum vara tre noder (4/2 + 1 = 3). Det innebär att du kan förlora en enskild nod och fortfarande upprätthålla kvorum, men ett samtidigt fel på två noder kommer att driva system tjänsterna till att förlora kvorum och kan orsaka att klustret blir otillgängligt.

**Fem noder**: med fem noder (N = 5) är kravet på att skapa ett kvorum fortfarande tre noder (5/2 + 1 = 3). Det innebär att du kan förlora två noder samtidigt och fortfarande upprätthålla kvorum för system tjänsterna.

För produktions arbets belastningar måste du vara elastisk till samtidiga fel på minst två noder (till exempel en på grund av kluster uppgradering, en på grund av andra orsaker), så fem noder krävs.

### <a name="can-i-turn-off-my-cluster-at-nightweekends-to-save-costs"></a>Kan jag stänga av mitt kluster på natten/helger för att spara kostnaderna?

Nej. I allmänhet inte. Service Fabric lagrar tillstånd på lokala, tillfälliga diskar, vilket innebär att om den virtuella datorn flyttas till en annan värd flyttas inte data med den. I normal drift är det inte ett problem eftersom den nya noden har uppdaterats av andra noder. Men om du stoppar alla noder och sedan startar om dem senare, finns det en betydande möjlighet att de flesta noderna börjar på nya värdar och gör att systemet inte kan återställas.

Om du vill skapa kluster för att testa ditt program innan det distribueras, rekommenderar vi att du dynamiskt skapar dessa kluster som en del av [pipelinen för kontinuerlig integrering/kontinuerlig distribution](service-fabric-tutorial-deploy-app-with-cicd-vsts.md).


### <a name="how-do-i-upgrade-my-operating-system-for-example-from-windows-server-2012-to-windows-server-2016"></a>Hur gör jag för att uppgraderar du mitt operativ system (till exempel från Windows Server 2012 till Windows Server 2016)?

Vi arbetar på en förbättrad upplevelse, idag, och du ansvarar för uppgraderingen. Du måste uppgradera OS-avbildningen på virtuella datorer för klustret en virtuell dator i taget. 

### <a name="can-i-encrypt-attached-data-disks-in-a-cluster-node-type-virtual-machine-scale-set"></a>Kan jag kryptera anslutna data diskar i en typ av klusternod (virtuell dators skalnings uppsättning)?
Ja.  Mer information finns i [skapa ett kluster med anslutna data diskar](../virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks.md#create-a-service-fabric-cluster-with-attached-data-disks) och [Azure Disk Encryption för Virtual Machine Scale Sets](../virtual-machine-scale-sets/disk-encryption-overview.md).

### <a name="can-i-use-low-priority-vms-in-a-cluster-node-type-virtual-machine-scale-set"></a>Kan jag använda virtuella datorer med låg prioritet i en typ av klusternod (skalnings uppsättning för virtuella datorer)?
Nej. Virtuella datorer med låg prioritet stöds inte. 

### <a name="what-are-the-directories-and-processes-that-i-need-to-exclude-when-running-an-anti-virus-program-in-my-cluster"></a>Vad är de kataloger och processer som jag behöver undanta när jag kör ett antivirus program i mitt kluster?

| **Undantagna Antivirus kataloger** |
| --- |
| Program\Microsoft Service Fabric |
| FabricDataRoot (från kluster konfiguration) |
| FabricLogRoot (från kluster konfiguration) |

| **Undantagna Antivirus processer** |
| --- |
| Fabric.exe |
| FabricHost.exe |
| FabricInstallerService.exe |
| FabricSetup.exe |
| FabricDeployer.exe |
| ImageBuilder.exe |
| FabricGateway.exe |
| FabricDCA.exe |
| FabricFAS.exe |
| FabricUOS.exe |
| FabricRM.exe |
| FileStoreService.exe |
 
### <a name="how-can-my-application-authenticate-to-key-vault-to-get-secrets"></a>Hur kan mitt program autentisera till Key Vault för att få hemligheter?
Följande innebär att ditt program kan hämta autentiseringsuppgifter för autentisering till Key Vault:

A. Under program bygget/packnings jobbet kan du hämta ett certifikat till ditt SF-programs data paket och använda det för att autentisera till Key Vault.
B. För virtuella datorer i skalnings uppsättningen för virtuella datorer kan du utveckla en enkel PowerShell-SetupEntryPoint för din SF-app för att få [en åtkomsttoken från MSI-slutpunkten](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)och sedan [Hämta dina hemligheter från Key Vault](/powershell/module/azurerm.keyvault/get-azurekeyvaultsecret).

## <a name="application-design"></a>Program design

### <a name="whats-the-best-way-to-query-data-across-partitions-of-a-reliable-collection"></a>Vad är det bästa sättet att fråga efter data mellan partitioner i en tillförlitlig samling?

Tillförlitliga samlingar är vanligt vis [partitionerade](service-fabric-concepts-partitioning.md) för att möjliggöra skalbarhet för bättre prestanda och data flöde. Det innebär att ett tillstånd för en specifik tjänst kan spridas över flera eller hundratals datorer. Om du vill utföra åtgärder över den fullständiga data uppsättningen har du några alternativ:

- Skapa en tjänst som frågar alla partitioner i en annan tjänst för att hämta de data som krävs.
- Skapa en tjänst som kan ta emot data från alla partitioner i en annan tjänst.
- Skicka regelbundet data från varje tjänst till en extern lagrings plats. Den här metoden är bara lämplig om frågorna som du utför inte är en del av din kärn affärs logik, eftersom det externa data lagrets data är inaktuella.
- Du kan också lagra data som måste ha stöd för frågor till alla poster direkt i ett data lager, i stället för i en tillförlitlig samling. Detta eliminerar problemet med inaktuella data, men tillåter inte att fördelarna med tillförlitliga samlingar utnyttjas.


### <a name="whats-the-best-way-to-query-data-across-my-actors"></a>Vad är det bästa sättet att fråga efter data i alla aktörer?

Aktörerna är utformade för att vara oberoende enheter och beräkning, så det rekommenderas inte att utföra breda frågor för aktörs status vid körning. Om du behöver fråga över hela uppsättningen aktörs tillstånd, bör du överväga något av följande:

- Ersätta din aktörs tjänst med tillstånds känsliga Reliable Services, så att antalet nätverks begär Anden att samla in all information från antalet aktörer till antalet partitioner i tjänsten.
- Utforma dina aktörer för att regelbundet skicka sitt tillstånd till en extern lagrings plats för enklare frågor. Som ovan är den här metoden endast lönsam om frågorna som du utför inte krävs för ditt körnings beteende.

### <a name="how-much-data-can-i-store-in-a-reliable-collection"></a>Hur mycket data kan jag lagra i en tillförlitlig samling?

Tillförlitliga tjänster är vanligt vis partitionerade, så den mängd du kan lagra begränsas bara av antalet datorer som du har i klustret och mängden tillgängligt minne på dessa datorer.

Anta till exempel att du har en tillförlitlig samling i en tjänst med 100 partitioner och 3 repliker, och lagrar objekt som är i storlek 1 KB. Anta nu att du har ett 10-dator kluster med 16 GB minne per dator. För enkelhetens skull bör du förutsätta att operativ system-och system tjänsterna, Service Fabric Runtime och dina tjänster förbrukar 6 GB av som lämnar 10 GB tillgängliga per dator eller 100 GB för klustret.

Om varje objekt måste lagras tre gånger (en primär och två repliker) skulle du ha tillräckligt med minne för cirka 35 000 000 objekt i din samling när du arbetar med full kapacitet. Vi rekommenderar dock att du överlappar den samtidiga förlusten av en feldomän och en uppgraderings domän som representerar cirka 1/3 av kapaciteten och minskar antalet till ungefär 23 000 000.

Den här beräkningen förutsätter även:

- Att data fördelningen över partitionerna är ungefär enhetlig eller att du rapporterar inläsnings mått till kluster resurs hanteraren. Som standard är Service Fabric belastnings utjämning baserat på antalet repliker. I föregående exempel skulle det medföra 10 primära repliker och 20 sekundära repliker på varje nod i klustret. Det fungerar bra för belastning som är jämnt fördelat över partitionerna. Om belastningen inte är ens måste du rapportera belastningen så att Resource Manager kan packa mindre repliker tillsammans och tillåta större repliker att förbruka mer minne på en enskild nod.

- Att den tillförlitliga tjänsten i fråga är den enda lagrings statusen i klustret. Eftersom du kan distribuera flera tjänster till ett kluster måste du vara mindful av de resurser som måste köras och hantera sitt tillstånd.

- Att själva klustret inte växer eller krymper. Om du lägger till fler datorer kommer Service Fabric att balansera om dina repliker för att utnyttja den ytterligare kapaciteten tills antalet datorer överskrider antalet partitioner i tjänsten, eftersom en enskild replik inte kan omfatta datorer. Om du däremot minskar storleken på klustret genom att ta bort datorer, packas dina repliker mer tätt och har mindre total kapacitet.

### <a name="how-much-data-can-i-store-in-an-actor"></a>Hur mycket data kan jag lagra i en aktör?

Precis som med Reliable Services begränsas mängden data som du kan lagra i en aktörs tjänst bara av det totala disk utrymmet och det tillgängliga minnet på noderna i klustret. Enskilda aktörer är dock mest effektiva när de används för att kapsla in en liten del av tillstånd och tillhör ande affärs logik. Som en allmän regel ska en enskild aktör ha ett tillstånd som mäts i kilobyte.


### <a name="where-does-azure-service-fabric-resource-provider-store-customer-data"></a>Var lagrar Azure Service Fabric Resource Provider kund information?

Azure Service Fabric Resource Provider flyttar eller lagrar inte kund information från den region som den har distribuerats i.


## <a name="other-questions"></a>Andra frågor

### <a name="how-does-service-fabric-relate-to-containers"></a>Hur relaterar Service Fabric till behållare?

Behållare är ett enkelt sätt att paketera tjänster och deras beroenden, så att de körs konsekvent i alla miljöer och kan fungera på ett isolerat sätt på en enda dator. Service Fabric erbjuder ett sätt att distribuera och hantera tjänster, inklusive [tjänster som har paketerats i en behållare](service-fabric-containers-overview.md).

### <a name="are-you-planning-to-open-source-service-fabric"></a>Planerar du att använda Service Fabric med öppen källkod?

Vi har öppen källkod i Service Fabric ([Reliable Services Framework](https://github.com/Azure/service-fabric-services-and-actors-dotnet), [Reliable frameworks Framework](https://github.com/Azure/service-fabric-services-and-actors-dotnet), [ASP.NET Core integrations bibliotek](https://github.com/Azure/service-fabric-aspnetcore), [Service Fabric Explorer](https://github.com/Azure/service-fabric-explorer)och [Service Fabric CLI](https://github.com/Azure/service-fabric-cli)) på GitHub och accepterar community-bidrag till dessa projekt. 

Vi [meddelade nyligen](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) att vi planerar att öppna källan Service Fabric Runtime. Nu har vi [Service Fabric lagrings platsen](https://github.com/Microsoft/service-fabric/) på GitHub med Linux-build-och test verktyg, vilket innebär att du kan klona lagrings platsen, bygga Service Fabric för Linux, köra grundläggande tester, öppna problem och skicka pull-begäranden. Vi arbetar hårt för att hämta Windows build-miljön som migrerats även, tillsammans med en komplett CI-miljö.

Följ [Service Fabric blogg](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) om du vill ha mer information när de annonseras.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [kärn Service Fabric koncept](service-fabric-technical-overview.md) och [bästa praxis](./service-fabric-best-practices-security.md)