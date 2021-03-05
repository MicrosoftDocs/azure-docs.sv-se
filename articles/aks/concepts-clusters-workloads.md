---
title: Koncept – grundläggande information om Kubernetes för Azure Kubernetes Services (AKS)
description: Lär dig mer om de grundläggande kluster-och arbets belastnings komponenterna i Kubernetes och hur de relaterar till funktioner i Azure Kubernetes service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: 2a1718d906ab5f51ea71be9b304028576c9fffa0
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102122450"
---
# <a name="kubernetes-core-concepts-for-azure-kubernetes-service-aks"></a>Kubernetes Core-koncept för Azure Kubernetes service (AKS)

När program utvecklingen flyttas till en container-baserad metod är behovet av att dirigera och hantera resurser viktigt. Kubernetes är den ledande plattformen som ger möjlighet att tillhandahålla tillförlitlig schemaläggning av feltoleranta program arbets belastningar. Azure Kubernetes service (AKS) är ett hanterat Kubernetes-erbjudande som ytterligare fören klar distribution och hantering av container-baserade program.

Den här artikeln beskriver kärn Kubernetes infrastruktur komponenter som *kontroll plan*, *noder* och *nodkonfigurationer*. Arbets belastnings resurser som *poddar*, *distributioner* och *uppsättningar* introduceras också, tillsammans med hur du grupperar resurser till *namn områden*.

## <a name="what-is-kubernetes"></a>Vad är Kubernetes?

Kubernetes är en snabbt växande plattform som hanterar containerbaserade program och deras associerade nätverks-och lagrings komponenter. Fokus är på program arbets belastningarna, inte de underliggande infrastruktur komponenterna. Kubernetes innehåller en deklarativ metod för distributioner, som backas upp av en robust uppsättning API: er för hanterings åtgärder.

Du kan skapa och köra moderna, bärbara och mikrotjänster-baserade program som drar nytta av Kubernetes-dirigering och hantering av tillgängligheten för dessa program komponenter. Kubernetes har stöd för både tillstånds lösa och tillstånds känsliga program som lag förloppet genom att använda mikrotjänster-baserade program.

Som en öppen plattform kan du med Kubernetes skapa dina program med önskat programmeringsspråk, OS, bibliotek eller meddelande buss. Befintliga verktyg för kontinuerlig integrering och verktyg för kontinuerlig leverans (CI/CD) kan integreras med Kubernetes för att schemalägga och distribuera versioner.

Azure Kubernetes service (AKS) tillhandahåller en hanterad Kubernetes-tjänst som minskar komplexiteten för distributions-och kärn hanterings aktiviteter, inklusive samordning av uppgraderingar. AKS-kontroll planet hanteras av Azure-plattformen och du betalar bara för de AKS-noder som kör dina program. AKS bygger på[AKS-motorn][aks-engine]med öppen källkod i Azure Kubernetes.

## <a name="kubernetes-cluster-architecture"></a>Arkitektur för Kubernetes-kluster

Ett Kubernetes-kluster är uppdelat i två komponenter:

- *Kontroll planet* tillhandahåller kärn Kubernetes-tjänster och dirigering av program arbets belastningar.
- *Noder* som kör dina program arbets belastningar.

![Kubernetes kontroll plan och nod komponenter](media/concepts-clusters-workloads/control-plane-and-nodes.png)

## <a name="control-plane"></a>Kontrollplan

När du skapar ett AKS-kluster skapas och konfigureras ett kontroll plan automatiskt. Det här kontroll planet tillhandahålls som en hanterad Azure-resurs som är abstrakt från användaren. Det finns ingen kostnad för kontroll planet, bara de noder som ingår i AKS-klustret. Kontroll planet och dess resurser finns bara i den region där du skapade klustret.

Kontroll planet innehåller följande kärn Kubernetes-komponenter:

- *Kube-apiserver* – API-servern är hur de underliggande Kubernetes-API: erna exponeras. Den här komponenten ger interaktion för hanterings verktyg, till exempel `kubectl` eller Kubernetes-instrumentpanelen.
- *etcd* – för att underhålla status för ditt Kubernetes-kluster och-konfiguration är hög tillgänglig *etcd* ett nyckel värdes lager inom Kubernetes.
- *Kube-Scheduler* – när du skapar eller skalar program bestämmer Scheduler vilka noder som kan köra arbets belastningen och startar dem.
- *Kube-Controller-Manager* – Controller Manager ser över ett antal mindre styrenheter som utför åtgärder som att replikera poddar och hantera Node-åtgärder.

AKS tillhandahåller ett kontroll plan för en enskild klient, med en dedikerad API-Server, Scheduler, osv. Du definierar antalet och storleken på noderna, och Azure-plattformen konfigurerar den säkra kommunikationen mellan kontroll planet och noderna. Interaktion med kontroll planet sker via Kubernetes-API: er, till exempel `kubectl` eller Kubernetes-instrumentpanelen.

Detta hanterade kontroll plan innebär att du inte behöver konfigurera komponenter som en hög tillgänglig *etcd* -butik, men det innebär också att du inte kan komma åt kontroll planet direkt. Uppgraderingar av Kubernetes dirigeras via Azure CLI eller Azure Portal, som uppgraderar kontroll planet och noderna. Om du vill felsöka eventuella problem kan du granska kontroll Plans loggarna via Azure Monitor loggar.

Om du behöver konfigurera kontroll planet på ett visst sätt eller behöver direkt åtkomst till det kan du distribuera ditt eget Kubernetes-kluster med [AKS-Engine][aks-engine].

För associerade metod tips, se [metod tips för kluster säkerhet och uppgraderingar i AKS][operator-best-practices-cluster-security].

## <a name="nodes-and-node-pools"></a>Noder och Node-pooler

Om du vill köra program och stöd tjänster behöver du en Kubernetes- *nod*. Ett AKS-kluster har en eller flera noder, som är en virtuell Azure-dator (VM) som kör Kubernetes-nodens komponenter och behållar körningen:

- `kubelet`Är Kubernetes-agenten som bearbetar Orchestration-begärandena från kontroll planet och schemaläggning av att köra de begärda behållarna.
- Virtuella nätverk hanteras av *Kube-proxy* på varje nod. Proxyservern dirigerar nätverks trafik och hanterar IP-adresser för tjänster och poddar.
- *Container runtime* är den komponent som gör det möjligt för program i behållare att köra och interagera med ytterligare resurser, till exempel det virtuella nätverket och lagringen. AKS-kluster med Kubernetes version 1,19 och större användning `containerd` som behållar körning. AKS-kluster som använder Kubernetes före v 1.19 för Node-pooler använder [Moby](https://mobyproject.org/) (överordnad Docker) som dess behållar körning.

![Virtuell Azure-dator och stöd resurser för en Kubernetes-nod](media/concepts-clusters-workloads/aks-node-resource-interactions.png)

Storleken på virtuella Azure-datorer för dina noder definierar hur många processorer, hur mycket minne och storlek och typ av lagring som är tillgängliga (till exempel SSD eller vanlig hård disk med höga prestanda). Om du förväntar dig ett behov av program som kräver stora mängder CPU-och minnes-eller lagrings utrymme för hög prestanda bör du planera storleken på noderna. Du kan också skala ut antalet noder i ditt AKS-kluster för att möta efter frågan.

I AKS baseras den virtuella dator avbildningen för noderna i klustret för närvarande på Ubuntu Linux eller Windows Server 2019. När du skapar ett AKS-kluster eller skalar ut antalet noder, skapar Azure-plattformen det begärda antalet virtuella datorer och konfigurerar dem. Det finns ingen manuell konfiguration som du kan utföra. Agent-noder faktureras som standard virtuella datorer så att eventuella rabatter som du har på den virtuella dator storleken som du använder (inklusive [Azure-reservationer][reservation-discounts]) används automatiskt.

Om du behöver använda ett annat värd operativ system, container runtime eller inkludera anpassade paket kan du distribuera ditt eget Kubernetes-kluster med [AKS-Engine][aks-engine]. De överordnade versions `aks-engine` funktionerna och innehåller konfigurations alternativ innan de stöds officiellt i AKS-kluster. Om du till exempel vill använda en behållar körning som inte `containerd` är eller Moby kan du använda `aks-engine` för att konfigurera och distribuera ett Kubernetes-kluster som uppfyller dina aktuella behov.

### <a name="resource-reservations"></a>Resurs reservationer

Node-resurser används av AKS för att göra Node-funktionen som en del av klustret. Den här användningen kan skapa en avvikelse mellan nodens totala resurser och de resurser som allocatable när de används i AKS. Den här informationen är viktig för att notera när du ställer in förfrågningar och begränsningar för användare som har distribuerat poddar.

Om du vill hitta en nods allocatable-resurser kör du:
```kubectl
kubectl describe node [NODE_NAME]
```

För att upprätthålla prestanda och funktioner för noden reserveras resurser på varje nod av AKS. När en nod växer större i resurser växer resurs reservationen på grund av en högre mängd användar distribuerade poddar som behöver hanteras.

>[!NOTE]
> Användning av AKS-tillägg som container Insights (OMS) kommer att använda ytterligare resurs resurser.

Två typer av resurser är reserverade:

- **CPU** -reserverad CPU är beroende av nodtypen och kluster konfigurationen, vilket kan orsaka mindre allocatable CPU på grund av att ytterligare funktioner körs

   | PROCESSOR kärnor på värd | 1    | 2    | 4    | 8    | 16 | 32|64|
   |---|---|---|---|---|---|---|---|
   |Kube-reserverade (millicores)|60|100|140|180|260|420|740|

- **Minnes** minne som används av AKS inkluderar summan av två värden.

   1. Daemon för kubelet har installerats på alla Kubernetes-agent-noder för att hantera skapande och avslutning av behållare. Som standard har daemonen följande borttagnings regel i AKS: *minne. available<750Mi*, vilket innebär att en nod alltid måste ha minst 750 mi allocatable.  När en värd unders tiger den tröskeln för tillgängligt minne, kommer kubelet att avsluta en av de poddar som körs för att frigöra minne på värddatorn och skydda den. Den här åtgärden utlöses när det tillgängliga minnet minskar efter 750Mi tröskel.

   2. Det andra värdet är en autoregressiva hastighet för de minnes reservationer som kubelet-daemonen ska fungera korrekt (Kube-reserverad).
      - 25% av de första 4 GB minne
      - 20% av nästa 4 GB minne (upp till 8 GB)
      - 10% av nästa 8 GB minne (upp till 16 GB)
      - 6% av nästa 112 GB minne (upp till 128 GB)
      - 2% av ett minne över 128 GB

Reglerna ovan för minne och PROCESSORALLOKERING används för att hålla agent-noderna felfria, inklusive vissa värd system poddar som är viktiga för kluster hälsan. Dessa allokeringsregler leder också till att noden rapporterar mindre allocatable minne och CPU än vanligt vis om den inte var en del av ett Kubernetes-kluster. Det går inte att ändra resurs reservationerna ovan.

Om en nod till exempel erbjuder 7 GB, kommer den att rapportera 34% av minnet som inte allocatable, inklusive 750Mi för hård borttagning.

`0.75 + (0.25*4) + (0.20*3) = 0.75GB + 1GB + 0.6GB = 2.35GB / 7GB = 33.57% reserved`

Förutom reservationer för Kubernetes, reserverar den underliggande noden också en mängd processor-och minnes resurser för att underhålla OS-funktioner.

För associerade metod tips, se [metod tips för grundläggande funktioner i Schemaläggaren i AKS][operator-best-practices-scheduler].

### <a name="node-pools"></a>Nodpooler

Noder i samma konfiguration grupperas tillsammans i *noder i pooler*. Ett Kubernetes-kluster innehåller en eller flera Node-pooler. Det ursprungliga antalet noder och storlek definieras när du skapar ett AKS-kluster, vilket skapar en *standardnod*. Denna standardnod i AKS innehåller de underliggande virtuella datorerna som kör dina agent-noder.

> [!NOTE]
> För att säkerställa att klustret fungerar på ett tillförlitligt sätt bör du köra minst två (två) noder i standardnodens adresspool.

När du skalar eller uppgraderar ett AKS-kluster utförs åtgärden mot standardnoden. Du kan också välja att skala eller uppgradera en angiven Node-pool. För uppgraderings åtgärder schemaläggs körning av behållare på andra noder i Node-poolen tills alla noder har uppgraderats.

Mer information om hur du använder flera Node-pooler i AKS finns i [skapa och hantera flera noder för ett kluster i AKS][use-multiple-node-pools].

### <a name="node-selectors"></a>Node-väljare

I ett AKS-kluster som innehåller flera resurspooler kan du behöva meddela Kubernetes Scheduler vilken Node-pool som ska användas för en specifik resurs. Till exempel ska ingångs styrenheter inte köras på Windows Server-noder. Med Node-väljare kan du definiera olika parametrar, till exempel nodens operativ system, för att styra var Pod ska schemaläggas.

Följande grundläggande exempel schemalägger en NGINX-instans på en Linux-nod med hjälp av Node-väljaren *"beta.Kubernetes.io/OS": Linux*:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
spec:
  containers:
    - name: myfrontend
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.12-alpine
  nodeSelector:
    "beta.kubernetes.io/os": linux
```

Mer information om hur du styr var poddar schemaläggs finns i [metod tips för avancerade Scheduler-funktioner i AKS][operator-best-practices-advanced-scheduler].

## <a name="pods"></a>Poddar

Kubernetes använder *poddar* för att köra en instans av programmet. En POD representerar en enda instans av ditt program. Poddar har vanligt vis en 1:1-mappning med en behållare, även om det finns avancerade scenarier där en POD kan innehålla flera behållare. Dessa poddar för flera behållare schemaläggs tillsammans på samma nod och tillåter att behållare delar relaterade resurser.

När du skapar en POD kan du definiera *resurs begär Anden* för att begära en viss mängd CPU-eller minnes resurser. Kubernetes Scheduler försöker schemalägga poddar att köra på en nod med tillgängliga resurser för att uppfylla begäran. Du kan också ange högsta antal resurs gränser som förhindrar att en specifik Pod förbrukar för mycket data bearbetnings resurser från den underliggande noden. Ett bra tips är att inkludera resurs gränser för alla poddar som hjälper Kubernetes Scheduler att förstå vilka resurser som behövs och är tillåtna.

Mer information finns i [Kubernetes poddar][kubernetes-pods] och [Kubernetes Pod Lifecycle][kubernetes-pod-lifecycle].

En pod är en logisk resurs, men behållarna är där program arbets belastningarna körs. Poddar är vanligt vis tillfälliga, disponibla resurser och individuellt schemalagda poddar saknar några av funktionerna för hög tillgänglighet och redundans Kubernetes ger. I stället distribueras och hanteras poddar av Kubernetes- *kontrollanter*, till exempel distributions styrenheten.

## <a name="deployments-and-yaml-manifests"></a>Distributioner och YAML-manifest

En *distribution* representerar en eller flera identiska poddar som hanteras av distributions styrenheten för Kubernetes. En distribution definierar antalet *repliker* (poddar) som ska skapas och Schemaläggaren för Kubernetes säkerställer att ytterligare poddar schemaläggs på felfria noder om poddar eller noder stöter på problem.

Du kan uppdatera distributioner för att ändra konfigurationen för poddar, behållar avbildning som används eller bifogad lagring. Distributions styrenheten tömmer och avslutar ett angivet antal repliker, skapar repliker från den nya distributions definitionen och fortsätter processen tills alla repliker i distributionen har uppdaterats.

De flesta tillstånds lösa program i AKS bör använda distributions modellen i stället för att schemalägga enskilda poddar. Kubernetes kan övervaka hälso tillstånd och status för distributioner för att säkerställa att antalet repliker som krävs körs i klustret. När du bara schemalägger enskilda poddar startas poddar om, om det uppstår ett problem och inte omplaneras på felfria noder om deras aktuella nod påträffar ett problem.

Om ett program kräver ett kvorum av instanser för att alltid vara tillgängligt för hanterings beslut, vill du inte att en uppdaterings process ska störa den möjligheten. *Pod avbrott i budgetar* kan användas för att definiera hur många repliker i en distribution som kan tas offline under en uppdatering eller nod-uppgradering. Om du till exempel har *fem (5)* repliker i distributionen kan du definiera ett Pod-avbrott på *4* för att endast tillåta att en replik tas bort/ombokas i taget. Precis som med Pod resurs gränser är det en bra idé att definiera Pod avbrotts budgetar för program som kräver att det alltid finns ett minsta antal repliker.

Distributioner skapas och hanteras vanligt vis med `kubectl create` eller `kubectl apply` . Om du vill skapa en distribution definierar du en manifest fil i formatet YAML (YAML Ain't Markup Language). I följande exempel skapas en grundläggande distribution av NGINX-webbservern. I distributionen anges *tre (3)* repliker som ska skapas, och port *80* måste vara öppen på behållaren. Resurs begär Anden och gränser definieras också för processor och minne.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: mcr.microsoft.com/oss/nginx/nginx:1.15.2-alpine
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: 250m
            memory: 64Mi
          limits:
            cpu: 500m
            memory: 256Mi
```

Mer komplexa program kan skapas genom att även inkludera tjänster som belastningsutjämnare i YAML-manifestet.

Mer information finns i [Kubernetes-distributioner][kubernetes-deployments].

### <a name="package-management-with-helm"></a>Paket hantering med Helm

En vanlig metod för att hantera program i Kubernetes är med [Helm][helm]. Du kan bygga och använda befintliga offentliga Helm- *diagram* som innehåller en paketerad version av program koden och Kubernetes yaml-manifest för att distribuera resurser. Dessa Helm-diagram kan lagras lokalt eller ofta i en fjärrlagringsplats, till exempel ett [Azure Container Registry Helm-diagram lagrings platsen][acr-helm].

Om du vill använda Helm installerar du Helm-klienten på datorn eller använder Helm-klienten i [Azure Cloud Shell][azure-cloud-shell]. Du kan söka efter eller skapa Helm-diagram med klienten och sedan installera dem i ditt Kubernetes-kluster. Mer information finns i [Installera befintliga program med Helm i AKS][aks-helm].

## <a name="statefulsets-and-daemonsets"></a>StatefulSets och DaemonSets

Distributions styrenheten använder Kubernetes Scheduler för att köra ett angivet antal repliker på alla tillgängliga noder med tillgängliga resurser. Den här metoden för att använda distributioner kan vara tillräckligt för tillstånds lösa program, men inte för program som kräver en beständig namngivnings konvention eller lagring. För program som kräver att en replik finns på varje nod, eller valda noder, i ett kluster, tittar distributions hanteraren inte på hur repliker distribueras mellan noderna.

Det finns två Kubernetes-resurser som låter dig hantera följande typer av program:

- *StatefulSets* – underhålla program tillstånd bortom en enskild Pod-livscykel, till exempel lagring.
- *DaemonSets* – se till att en instans körs på varje nod, tidigt i Start processen för Kubernetes.

### <a name="statefulsets"></a>StatefulSets

Modern program utveckling syftar ofta till tillstånds lösa program, men *StatefulSets* kan användas för tillstånds känsliga program, till exempel program som innehåller databas komponenter. En StatefulSet liknar en distribution i som en eller flera identiska poddar skapas och hanteras. Repliker i en StatefulSet följer en korrekt, sekventiell metod för distribution, skalning, uppgraderingar och avslutning. Med en StatefulSet (som repliker omplaneras) namngivnings konventionen, nätverks namn och lagrings utrymme.

Du definierar programmet i YAML-format med `kind: StatefulSet` , och StatefulSet-kontrollanten hanterar sedan distributionen och hanteringen av de nödvändiga replikerna. Data skrivs till beständig lagring, som tillhandahålls av Azure Managed Disks eller Azure Files. Med StatefulSets förblir den underliggande beständiga lagringen även om StatefulSet tas bort.

Mer information finns i [Kubernetes StatefulSets][kubernetes-statefulsets].

Repliker i en StatefulSet schemaläggs och körs över alla tillgängliga noder i ett AKS-kluster. Om du behöver se till att minst en POD i uppsättningen körs på en nod kan du i stället använda en DaemonSet.

### <a name="daemonsets"></a>DaemonSets

För specifik logg samling eller övervaknings behov kan du behöva köra en viss Pod på alla eller valda noder. En *DaemonSet* används återigen för att distribuera en eller flera identiska poddar, men DaemonSet-styrenheten säkerställer att varje nod som anges kör en instans av pod.

DaemonSet-kontrollanten kan schemalägga poddar på noderna tidigt i kluster start processen, innan standard-Kubernetes Scheduler har startat. Den här funktionen säkerställer att poddar i en DaemonSet startas innan traditionella poddar i en distribution eller StatefulSet schemaläggs.

Som StatefulSets definieras en DaemonSet som en del av en YAML-definition med hjälp av `kind: DaemonSet` .

Mer information finns i [Kubernetes DaemonSets][kubernetes-daemonset].

> [!NOTE]
> Om du använder [tillägg för virtuella noder](virtual-nodes-cli.md#enable-virtual-nodes-addon)kommer DaemonSets inte att skapa poddar på den virtuella noden.

## <a name="namespaces"></a>Namnrymder

Kubernetes-resurser, till exempel poddar och distributioner, grupperas logiskt i ett *namn område*. Dessa grupperingar ger ett sätt att logiskt dela upp ett AKS-kluster och begränsa åtkomsten till att skapa, Visa eller hantera resurser. Du kan till exempel skapa namn områden för att separera affärs grupper. Användare kan bara interagera med resurser inom de tilldelade namn områdena.

![Kubernetes namn områden som logiskt delar resurser och program](media/concepts-clusters-workloads/namespaces.png)

När du skapar ett AKS-kluster är följande namn rymder tillgängliga:

- *standard* – det här namn området är där poddar och distributioner skapas som standard när inget anges. I mindre miljöer kan du distribuera program direkt till standard namn området utan att skapa ytterligare logiska separationer. När du interagerar med Kubernetes-API: t, till exempel med `kubectl get pods` , används standard namn området när inget anges.
- *Kube-system* – det här namn området är där kärn resurser finns, till exempel nätverks funktioner som DNS och proxy eller Kubernetes-instrumentpanelen. Du distribuerar vanligt vis inte dina egna program till det här namn området.
- *Kube* – det här namn området används vanligt vis inte, men kan användas för att visa resurser över hela klustret och kan visas av alla användare.

Mer information finns i [Kubernetes-namnområden][kubernetes-namespaces].

## <a name="next-steps"></a>Nästa steg

Den här artikeln beskriver några av de viktigaste Kubernetes-komponenterna och hur de gäller för AKS-kluster. Mer information om kärn Kubernetes-och AKS-koncept finns i följande artiklar:

- [Kubernetes/AKS-åtkomst och identitet][aks-concepts-identity]
- [Kubernetes/AKS-säkerhet][aks-concepts-security]
- [Kubernetes/AKS virtuella nätverk][aks-concepts-network]
- [Kubernetes/AKS-lagring][aks-concepts-storage]
- [Kubernetes/AKS-skala][aks-concepts-scale]

<!-- EXTERNAL LINKS -->
[aks-engine]: https://github.com/Azure/aks-engine
[kubernetes-pods]: https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/
[kubernetes-pod-lifecycle]: https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/
[kubernetes-deployments]: https://kubernetes.io/docs/concepts/workloads/controllers/deployment/
[kubernetes-statefulsets]: https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/
[kubernetes-daemonset]: https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/
[kubernetes-namespaces]: https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/
[helm]: https://helm.sh/
[azure-cloud-shell]: https://shell.azure.com

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[acr-helm]: ../container-registry/container-registry-helm-repos.md
[aks-helm]: kubernetes-helm.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[operator-best-practices-scheduler]: operator-best-practices-scheduler.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[reservation-discounts]:../cost-management-billing/reservations/save-compute-costs-reservations.md
