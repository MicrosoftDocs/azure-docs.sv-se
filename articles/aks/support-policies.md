---
title: Support principer för Azure Kubernetes service (AKS)
description: Lär dig mer om Azure Kubernetes service (AKS) support principer, delade ansvar och funktioner i för hands versionen (eller alpha eller beta).
services: container-service
ms.topic: article
ms.date: 09/18/2020
ms.openlocfilehash: 86b1c0bba30b41a2ee17cfbdf05286c4d2b3fb8a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91892718"
---
# <a name="support-policies-for-azure-kubernetes-service"></a>Support principer för Azure Kubernetes-tjänsten

Den här artikeln innehåller information om tekniska support principer och begränsningar för Azure Kubernetes service (AKS). Artikeln innehåller också information om hantering av Gent, hanterade kontroll Plans komponenter, komponenter från tredje part med öppen källkod och säkerhets-eller uppdaterings hantering.

## <a name="service-updates-and-releases"></a>Service uppdateringar och versioner

* Versions information finns i [AKS-versions anteckningar](https://github.com/Azure/AKS/releases).
* Information om funktioner i för hands version finns i [AKS Preview-funktioner och relaterade projekt](https://awesomeopensource.com/projects/aks?categoryPage=11).

## <a name="managed-features-in-aks"></a>Hanterade funktioner i AKS

Grundläggande infrastruktur komponenter för infrastruktur som en tjänst (IaaS), till exempel Compute-eller Networking-komponenter, ger dig till gång till kontroller på låg nivå och anpassnings alternativ. Som kontrast tillhandahåller AKS en nyckel färdig Kubernetes-distribution som ger dig den gemensamma uppsättningen konfigurationer och funktioner som du behöver för klustret. Som AKS-användare har du begränsade anpassnings-och distributions alternativ. I Exchange behöver du inte bekymra dig om eller hantera Kubernetes-kluster direkt.

Med AKS får du ett helt hanterat *kontroll plan*. Kontroll planet innehåller alla komponenter och tjänster som du behöver för att kunna hantera och tillhandahålla Kubernetes-kluster till slutanvändarna. Alla Kubernetes-komponenter upprätthålls och drivs av Microsoft.

Microsoft hanterar och övervakar följande komponenter i kontroll fönstret:

* Kubelet-eller Kubernetes-API-servrar
* Etcd eller ett kompatibelt nyckel värdes lager som tillhandahåller QoS (Quality of Service), skalbarhet och körning
* DNS-tjänster (till exempel Kube-DNS eller CoreDNS)
* Kubernetes-proxy eller nätverk
* Ytterligare tillägg eller system komponenter som körs i Kube-systemets namnrymd

AKS är inte en PaaS-lösning (Platform-as-a-Service). Vissa komponenter, till exempel agent-noder, har *delat ansvar*, där användarna måste underhålla AKS-klustret. Användarindata krävs, till exempel för att tillämpa en säkerhets korrigering för agentens operativ system (OS).

Tjänsterna *hanteras* i den mening att Microsoft och AKS-teamet distribuerar, agerar och ansvarar för tjänstens tillgänglighet och funktioner. Kunder kan inte ändra dessa hanterade komponenter. Microsoft begränsar anpassningen för att säkerställa en konsekvent och skalbar användar upplevelse. En helt anpassningsbar lösning finns i [AKS-motorn](https://github.com/Azure/aks-engine).

## <a name="shared-responsibility"></a>Delat ansvar

När ett kluster skapas definierar du de Kubernetes-agent-noder som AKS skapar. Dina arbets belastningar körs på dessa noder.

Eftersom dina agent-noder kör privat kod och lagrar känsliga data kan Microsoft Support komma åt dem endast på ett mycket begränsat sätt. Microsoft Support kan inte logga in på, köra kommandon i eller Visa loggar för dessa noder utan uttrycklig behörighet eller hjälp.

Alla ändringar som görs direkt till agent-noderna med någon av IaaS-API: erna återger klustret som inte stöds. Alla ändringar som görs på agent-noderna måste utföras med Kubernetes-inbyggda mekanismer som `Daemon Sets` .

På samma sätt kan du lägga till alla metadata till klustret och noderna, till exempel taggar och etiketter, vilket innebär att det inte finns stöd för att ändra alla systemgenererade metadata.

## <a name="aks-support-coverage"></a>Support täckning för AKS

Microsoft tillhandahåller teknisk support för följande exempel:

* Anslutning till alla Kubernetes-komponenter som Kubernetes-tjänsten tillhandahåller och stöder, t. ex. API-servern.
* Hantering, drift tid, QoS och drift av Kubernetes Control plan-tjänster (t. ex. Kubernetes kontroll plan, API-Server, etcd och coreDNS).
* Etcd data lager. Support omfattar automatiserade, transparenta säkerhets kopieringar av alla etcd-data var 30: e minut för katastrof planering och återställning av kluster tillstånd. Dessa säkerhets kopior är inte direkt tillgängliga för dig eller användare. De säkerställer data tillförlitlighet och konsekvens. Etcd. återställning på begäran eller återställning stöds inte som en funktion.
* Alla integrerings platser i Azure Cloud Provider-drivrutinen för Kubernetes. Detta inkluderar integreringar i andra Azure-tjänster, till exempel belastningsutjämnare, beständiga volymer eller nätverk (Kubernetes och Azure CNI).
* Frågor eller problem med anpassning av kontroll Plans komponenter som Kubernetes API-Server, etcd och coreDNS.
* Problem med nätverk, till exempel Azure CNI, Kubernetes eller andra problem med nätverks åtkomst och funktioner. Problem kan vara DNS-matchning, paket förlust, Routning och så vidare. Microsoft har stöd för olika nätverks scenarier:
  * Kubernetes och Azure CNI med hanterade virtuella nätverk eller med anpassade (ta med egna) undernät.
  * Anslutning till andra Azure-tjänster och-program
  * Ingångs-och ingångs-eller belastnings Utjämnings konfigurationer
  * Nätverks prestanda och svars tid


> [!NOTE]
> Alla kluster åtgärder som utförs av Microsoft/AKS görs med användar medgivande under en inbyggd Kubernetes-roll `aks-service` och inbyggd roll bindning `aks-service-rolebinding` . Med den här rollen kan AKS felsöka och diagnostisera kluster problem, men det går inte att ändra behörigheter eller skapa roller eller roll bindningar eller andra åtgärder med hög behörighet. Roll åtkomst är bara aktiverat under aktiva support biljetter med JIT-åtkomst (just-in-Time).

Microsoft tillhandahåller inte teknisk support för följande exempel:

* Frågor om hur du använder Kubernetes. Microsoft Support ger dig till exempel inte råd om hur du skapar anpassade ingångs styrenheter, använder program arbets belastningar eller använder program varu paket från tredje part eller öppen källkod eller verktyg.
  > [!NOTE]
  > Microsoft Support kan informera om AKS kluster funktioner, anpassning och justering (till exempel problem med Kubernetes-åtgärder och procedurer).
* Projekt med öppen källkod från tredje part som inte tillhandahålls som en del av Kubernetes-kontroll planet eller distribueras med AKS-kluster. Dessa projekt kan innehålla Istio, Helm, mottagare eller andra.
  > [!NOTE]
  > Microsoft kan tillhandahålla bästa möjliga support för projekt med öppen källkod från tredje part, till exempel Helm. Om verktyget med öppen källkod integrerar med Kubernetes Azure-molnet eller andra AKS buggar, stöder Microsoft exempel och program från Microsoft-dokumentationen.
* Program vara från sluten program vara från tredje part. Den här program varan kan innehålla verktyg för säkerhets genomsökning och nätverks enheter eller program.
* Andra nätverks anpassningar än de som anges i [AKS-dokumentationen](./index.yml).


## <a name="aks-support-coverage-for-agent-nodes"></a>AKS support täckning för agent-noder

### <a name="microsoft-responsibilities-for-aks-agent-nodes"></a>Microsoft-ansvar för AKS-agent-noder

Microsoft och användare delar ansvar för Kubernetes-agent-noder där:

* Bas operativ system avbildningen har nödvändiga tillägg (till exempel övervaknings-och nätverks agenter).
* Agent-noderna får OS-uppdateringar automatiskt.
* Problem med de Kubernetes Control plan-komponenter som körs på agent-noderna åtgärdas automatiskt. Dessa komponenter innehåller följande:
  * `Kube-proxy`
  * Nätverks tunnlar som tillhandahåller kommunikations vägar till Kubernetes huvud komponenter
  * `Kubelet`
  * `Moby` eller `ContainerD`

> [!NOTE]
> Om en agent-nod inte fungerar kan AKS starta om enskilda komponenter eller hela agent-noden. De här omstarts åtgärderna automatiseras och tillhandahåller automatisk reparation av vanliga problem. Om du vill veta mer om automatiska reparations metoder, se [noden automatisk reparation](node-auto-repair.md)

### <a name="customer-responsibilities-for-aks-agent-nodes"></a>Kund ansvar för AKS-agent-noder

Microsoft tillhandahåller korrigeringar och nya avbildningar för dina bildnoder varje vecka, men korrigerar dem automatiskt som standard. Om du vill att agent-nodens operativ system och körnings komponenter ska korrigeras, bör du behålla ett uppgraderings schema för en vanlig [nods avbildning](node-image-upgrade.md) eller automatisera det.

På samma sätt släpper AKS regelbundet nya Kubernetes-korrigeringsfiler och del versioner. Dessa uppdateringar kan innehålla säkerhets-eller funktions förbättringar för Kubernetes. Du är ansvarig för att hålla klustrets Kubernetes-version uppdaterad och enligt [principen AKS Kubernetes support version](supported-kubernetes-versions.md).

#### <a name="user-customization-of-agent-nodes"></a>Användar anpassning av agent-noder
> [!NOTE]
> AKS agent-noder visas i Azure Portal som vanliga Azure IaaS-resurser. Men de här virtuella datorerna distribueras till en anpassad Azure-resurs grupp (vanligt vis föregås av MC_ \* ). Du kan inte ändra bas operativ system avbildningen eller göra några direkta anpassningar till dessa noder med IaaS-API: erna eller resurser. Eventuella anpassade ändringar som inte görs via AKS-API: et behålls inte genom en uppgradering, skalning, uppdatering eller omstart. Undvik att utföra ändringar av agent noderna om Microsoft Support dirigerar dig om att göra ändringar.

AKS hanterar livs cykeln och åtgärder för agent-noderna för din räkning – det **går inte** att ändra de IaaS-resurser som är kopplade till agent-noderna. Ett exempel på en åtgärd som inte stöds är att anpassa skalnings uppsättningen för en virtuell dator i en pool genom att manuellt ändra konfigurationerna via den virtuella datorns skal uppsättnings portal eller API.
 
AKS rekommenderar att du använder [Kubernetes `daemon sets` ](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/)för arbets belastnings bara konfigurationer eller paket.

Med hjälp av Kubernetes Privileged `daemon sets` och init containers kan du finjustera/ändra eller installera program vara från tredje part på klusternoder. Exempel på sådana anpassningar är att lägga till anpassade program för säkerhets genomsökning eller uppdatera sysctl-inställningar.

Även om den här sökvägen rekommenderas om ovanstående krav är uppfyllda kan AKS Engineering and support inte hjälpa till att felsöka eller diagnostisera ändringar som gör att noden inte är tillgänglig på grund av en anpassad distribution `daemon set` .

### <a name="security-issues-and-patching"></a>Säkerhets problem och uppdatering

Om det finns ett säkerhets fel i en eller flera av de hanterade komponenterna i AKS, kommer AKS-teamet att uppdatera alla berörda kluster för att undvika problemet. Alternativt ger teamet användarna uppgraderings vägledning.

För att agent-noder påverkas av ett säkerhets fel meddelar Microsoft dig information om konsekvenserna och stegen för att åtgärda eller minimera säkerhets problemet (normalt en uppgradering av en uppgradering av en nod eller en uppgradering av kluster korrigering).

### <a name="node-maintenance-and-access"></a>Underhåll och åtkomst till nod

Även om du kan logga in på och ändra agent-noder, så rekommenderas inte den här åtgärden eftersom det inte går att göra ett kluster.

## <a name="network-ports-access-and-nsgs"></a>Nätverks portar, åtkomst och NSG: er

Du kan bara anpassa NSG: er på anpassade undernät. Du kan inte anpassa NSG: er i hanterade undernät eller på NÄTVERKSKORTs nivån för agent-noderna. AKS har utgångs krav för särskilda slut punkter, för att kontrol lera utgående trafik och säkerställa nödvändig anslutning, se [begränsa utgående trafik](limit-egress-traffic.md).

## <a name="stopped-or-de-allocated-clusters"></a>Stoppade eller förallokerade kluster

Som tidigare nämnts förallokerar manuellt alla klusternoder via IaaS-API: erna/CLI/portalen. Det enda stödda sättet att stoppa/ta bort alla noder är att [stoppa AKS-klustret](start-stop-cluster.md#stop-an-aks-cluster), vilket bevarar kluster tillstånd i upp till 12 månader.

Kluster som har stoppats under mer än 12 månader kommer inte längre att behålla sitt tillstånd. 

Kluster som inte är allokerade utanför AKS-API: erna har ingen garanti för tillstånds bevarande. Kontroll planen för kluster i det här läget kommer att arkiveras efter 30 dagar och tas bort efter 12 månader.

AKS förbehåller sig rätten att arkivera kontroll plan som har kon figurer ATS ur support rikt linjerna för utökade perioder som är lika med och bortom 30 dagar. AKS upprätthåller säkerhets kopiering av etcd metadata i kluster och kan enkelt allokera om klustret. Den här Omallokeringen kan initieras av en pågående åtgärd som tar klustret tillbaka till support, till exempel en uppgradering eller skalning till aktiva agent-noder.

Om din prenumeration har inaktiverats eller tagits bort tas ditt klusters kontroll plan och tillstånd bort efter 90 dagar.

## <a name="unsupported-alpha-and-beta-kubernetes-features"></a>Funktioner som inte stöds av alpha och beta Kubernetes

AKS stöder endast stabila och beta funktioner i det överordnade Kubernetes-projektet. Om inget annat har dokumenterats stöder AKS inte någon alfa funktion som är tillgänglig i det överordnade Kubernetes-projektet.

## <a name="preview-features-or-feature-flags"></a>Förhands gransknings funktioner eller funktions flaggor

För funktioner och funktioner som kräver utökad testning och feedback från användaren, släpper Microsoft nya för hands versions funktioner eller funktioner bakom en funktions flagga. Tänk på dessa funktioner som för hands version eller beta funktioner.

Funktionerna för förhands granskning eller funktions flagga är inte avsedda för produktion. Pågående ändringar i API: er och beteende, fel korrigeringar och andra ändringar kan resultera i instabila kluster och stillestånds tid.

Funktioner som ingår i den offentliga för hands versionen är under stöd för bästa prestanda eftersom dessa funktioner är i för hands version och inte är avsedda för produktion och stöds endast av AKS-teamen för teknisk support under kontors tid. Mer information finns i:

* [Vanliga frågor och svar om support för Azure](https://azure.microsoft.com/support/faq/)

## <a name="upstream-bugs-and-issues"></a>Överordna buggar och problem

Med tanke på hastigheten på utvecklingen i det överordnade Kubernetes-projektet, uppstår buggar. Vissa av dessa buggar kan inte korrigeras eller bearbetas runt i AKS-systemet. I stället kräver fel korrigeringar större korrigeringar för att överföra projekt (till exempel Kubernetes, Node-eller agent-operativsystem och kernel). För komponenter som Microsoft äger (t. ex. Azure Cloud Provider) är AKS och Azure personal engagerade i att åtgärda problem med överströms i communityn.

När ett tekniskt support ärende är rot som orsakas av en eller flera överströms buggar, kommer AKS support och teknik team att:

* Identifiera och länka de överordnade buggar med all ytterligare information som kan hjälpa dig att förklara varför det här problemet påverkar klustret eller arbets belastningen. Kunderna får länkar till de nödvändiga databaserna så att de kan se problemen och se när en ny version kommer att tillhandahålla korrigeringar.
* Tillhandahålla möjliga lösningar eller åtgärder. Om problemet kan undvikas kommer ett [känt problem](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue) att arkiveras i AKS-lagringsplatsen. Den kända problem filen förklarar:
  * Problemet, inklusive länkar till överordnade buggar.
  * Lösning och information om en uppgradering eller en annan beständig lösning.
  * Tuffa tids linjer för ärendets inkludering baserat på den överordnade versionen takt.
