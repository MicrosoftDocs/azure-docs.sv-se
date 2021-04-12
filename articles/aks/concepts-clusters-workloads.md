---
title: Koncept – grundläggande information om Kubernetes för Azure Kubernetes Services (AKS)
description: Lär dig mer om de grundläggande kluster-och arbets belastnings komponenterna i Kubernetes och hur de relaterar till funktioner i Azure Kubernetes service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 5e505ed44d221b20178ea5ffb1d9125fb2bddd4c
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105943"
---
# <a name="kubernetes-core-concepts-for-azure-kubernetes-service-aks"></a>Kubernetes Core-koncept för Azure Kubernetes service (AKS)

Program utveckling fortsätter att gå till en container-baserad metod, vilket ökar behovet av att dirigera och hantera resurser. Som den ledande plattformen tillhandahåller Kubernetes tillförlitlig schemaläggning av feltoleranta program arbets belastningar. Azure Kubernetes service (AKS), ett hanterat Kubernetes-erbjudande, underlättar distribution och hantering av containerbaserade program.

Den här artikeln beskriver:
* Kärn Kubernetes infrastruktur komponenter:
    * *kontroll plan*
    * *artikelnoder*
    * *Node-pooler*
* Arbets belastnings resurser: 
    * *poddar*
    * *distributioner*
    * *definierar* 
* Gruppera resurser i *namn områden*.

## <a name="what-is-kubernetes"></a>Vad är Kubernetes?

Kubernetes är en snabbt växande plattform som hanterar containerbaserade program och deras associerade nätverks-och lagrings komponenter. Kubernetes fokuserar på program arbets belastningarna, inte de underliggande infrastruktur komponenterna. Kubernetes innehåller en deklarativ metod för distributioner, som backas upp av en robust uppsättning API: er för hanterings åtgärder.

Du kan skapa och köra moderna, bärbara och mikrotjänster-baserade program med hjälp av Kubernetes för att dirigera och hantera tillgängligheten för program komponenterna. Kubernetes har stöd för både tillstånds lösa och tillstånds känsliga program som lag förloppet genom att använda mikrotjänster-baserade program.

Som en öppen plattform kan du med Kubernetes skapa dina program med önskat programmeringsspråk, OS, bibliotek eller meddelande buss. Befintliga verktyg för kontinuerlig integrering och verktyg för kontinuerlig leverans (CI/CD) kan integreras med Kubernetes för att schemalägga och distribuera versioner.

AKS tillhandahåller en hanterad Kubernetes-tjänst som minskar komplexiteten vid distribution och kärn hanterings uppgifter, som uppgraderings samordning. Azure-plattformen hanterar AKS-kontroll planet och du betalar bara för de AKS-noder som kör dina program. AKS skapas ovanpå Azure-Kubernetes med öppen källkod: [AKS-Engine][aks-engine].

## <a name="kubernetes-cluster-architecture"></a>Arkitektur för Kubernetes-kluster

Ett Kubernetes-kluster är uppdelat i två komponenter:

- *Kontroll plan*: tillhandahåller kärn Kubernetes-tjänster och dirigering av program arbets belastningar.
- *Noder*: kör dina program arbets belastningar.

![Kubernetes kontroll plan och nod komponenter](media/concepts-clusters-workloads/control-plane-and-nodes.png)

## <a name="control-plane"></a>Kontrollplan

När du skapar ett AKS-kluster skapas och konfigureras ett kontroll plan automatiskt. Det här kontroll planet tillhandahålls utan kostnad som en hanterad Azure-resurs som är abstrakt från användaren. Du betalar bara för de noder som är kopplade till AKS-klustret. Kontroll planet och dess resurser finns bara i den region där du skapade klustret.

Kontroll planet innehåller följande kärn Kubernetes-komponenter:

| Komponent | Beskrivning |  
| ----------------- | ------------- |  
| *kube-apiserver*                                                                                 | API-servern är hur de underliggande Kubernetes-API: erna exponeras. Den här komponenten ger interaktion för hanterings verktyg, till exempel `kubectl` eller Kubernetes-instrumentpanelen.                                                        |  
| *etcd* | För att upprätthålla statusen för ditt Kubernetes-kluster och-konfiguration är den hög tillgängliga *etcd* ett nyckel värdes lager i Kubernetes.                                      |  
| *Kube – Scheduler*                                                                            | När du skapar eller skalar program bestämmer Scheduler vilka noder som kan köra arbets belastningen och startar dem.                                                                                    |  
| *Kube-Controller-Manager*                                                                            | Kontrollant Manager ser över ett antal mindre styrenheter som utför åtgärder som att replikera poddar och hantera Node-åtgärder.                                                                  |  

AKS tillhandahåller ett kontroll plan för en enskild klient, med en dedikerad API-Server, Scheduler, osv. Du definierar antalet och storleken på noderna, och Azure-plattformen konfigurerar den säkra kommunikationen mellan kontroll planet och noderna. Interaktion med kontroll planet sker via Kubernetes-API: er, till exempel `kubectl` eller Kubernetes-instrumentpanelen.

Även om du inte behöver konfigurera komponenter (t. ex. ett *etcd* lager med hög tillgänglighet) med det här hanterade kontroll planet kan du inte komma åt kontroll planet direkt. Kubernetes kontroll plan och uppgraderingar av noder dirigeras via Azure CLI eller Azure Portal. Om du vill felsöka eventuella problem kan du granska kontroll Plans loggarna via Azure Monitor loggar.

Om du vill konfigurera eller direkt komma åt ett kontroll plan distribuerar du ditt eget Kubernetes-kluster med [AKS-Engine][aks-engine].

För associerade metod tips, se [metod tips för kluster säkerhet och uppgraderingar i AKS][operator-best-practices-cluster-security].

## <a name="nodes-and-node-pools"></a>Noder och Node-pooler

Om du vill köra program och stöd tjänster behöver du en Kubernetes- *nod*. Ett AKS-kluster har minst en nod, en virtuell Azure-dator (VM) som kör Kubernetes-nodens komponenter och container Runtime.

| Komponent | Beskrivning |  
| ----------------- | ------------- |  
| `kubelet`                                                                                 | Kubernetes-agenten som bearbetar Orchestration-begärandena från kontroll planet och schemaläggning av att köra de begärda behållarna.                                                        |  
| *Kube-proxy* | Hanterar virtuella nätverk på varje nod. Proxyservern dirigerar nätverks trafik och hanterar IP-adresser för tjänster och poddar.                                      |  
| *container runtime*                                                                            | Tillåter att behållar program körs och interagerar med ytterligare resurser, till exempel det virtuella nätverket och lagringen. AKS-kluster med Kubernetes-version 1.19 + Node-pooler använder `containerd` sig av sin container Runtime. AKS-kluster som använder Kubernetes före Node pool version 1,19 för Node-pooler använder [Moby](https://mobyproject.org/) (överordnad Docker) som behållar körning.                                                                                    |  


![Virtuell Azure-dator och stöd resurser för en Kubernetes-nod](media/concepts-clusters-workloads/aks-node-resource-interactions.png)

Storleken på den virtuella Azure-datorn för dina noder definierar lagrings processor, minne, storlek och typ som är tillgängliga (till exempel SSD eller en vanlig hård disk med hög prestanda). Planera Node-storleken runt om dina program kan kräva stora mängder CPU och minne eller lagring med höga prestanda. Skala ut antalet noder i ditt AKS-kluster för att möta efter frågan.

I AKS baseras VM-avbildningen för klustrets noder på Ubuntu Linux eller Windows Server 2019. När du skapar ett AKS-kluster eller skalar ut antalet noder skapar och konfigurerar Azure-plattformen automatiskt det begärda antalet virtuella datorer. Agent-noder faktureras som virtuella standard datorer, så alla VM-storlekar (inklusive [Azure-reservationer][reservation-discounts]) tillämpas automatiskt.

Distribuera ditt eget Kubernetes-kluster med [AKS-Engine][aks-engine] om du använder ett annat värd operativ system, container runtime eller med olika anpassade paket. De överordnade versions `aks-engine` funktionerna och innehåller konfigurations alternativ före stöd i AKS-kluster. Så om du vill använda en annan behållar körning än `containerd` eller [Moby](https://mobyproject.org/), kan du köra `aks-engine` för att konfigurera och distribuera ett Kubernetes-kluster som uppfyller dina aktuella behov.

### <a name="resource-reservations"></a>Resurs reservationer

AKS använder Node-resurser för att hjälpa Node-funktionen som en del av klustret. Den här användningen kan skapa en avvikelse mellan nodens totala resurser och allocatable-resurserna i AKS. Kom ihåg den här informationen när du ställer in förfrågningar och begränsningar för användare som distribuerats av poddar.

Om du vill hitta en nods allocatable-resurser kör du:
```kubectl
kubectl describe node [NODE_NAME]
```

För att underhålla prestanda och funktioner för noden reserverar AKS resurser på varje nod. När en nod växer större i resurser växer resurs reservationen på grund av ett högre behov av hantering av poddar-distribuerade användare.

>[!NOTE]
> Användning av AKS-tillägg som container Insights (OMS) kommer att använda ytterligare resurs resurser.

Två typer av resurser är reserverade:

- **Processor**  
    Reserverad processor är beroende av nodtypen och kluster konfigurationen, vilket kan orsaka mindre allocatable CPU på grund av att ytterligare funktioner körs.

   | PROCESSOR kärnor på värd | 1    | 2    | 4    | 8    | 16 | 32|64|
   |---|---|---|---|---|---|---|---|
   |Kube-reserverade (millicores)|60|100|140|180|260|420|740|

- **Minne**  
    Det minne som används av AKS inkluderar summan av två värden.

   1. **`kubelet` program**   
       `kubelet`Daemon installeras på alla Kubernetes-agent-noder för att hantera skapande och avslutning av behållare. 
   
        Som standard i AKS `kubelet` har daemon *minnet. tillgänglig<750Mi* -avtagnings regel, vilket säkerställer att en nod alltid måste ha minst 750 mi allocatable. När en värd är under den tillgängliga minnes tröskeln `kubelet` utlöses den för att avsluta en av de poddar som körs och frigöra minne på värddatorn.

   2. **En autoregressiva hastighet för minnes reservationer** för kubelet-daemon att fungera korrekt (*Kube-reserverad*).
      - 25% av de första 4 GB minne
      - 20% av nästa 4 GB minne (upp till 8 GB)
      - 10% av nästa 8 GB minne (upp till 16 GB)
      - 6% av nästa 112 GB minne (upp till 128 GB)
      - 2% av ett minne över 128 GB

Minnes-och processor tilldelnings regler:
* Se till att agent-noderna är felfria, inklusive vissa värd system poddar som är viktiga för kluster hälsan. 
* Orsaka att noden rapporterar mindre allocatable minne och CPU än om den inte var en del av ett Kubernetes-kluster. 

Det går inte att ändra resurs reservationerna ovan.

Om en nod till exempel erbjuder 7 GB, kommer den att rapportera 34% av minnet som inte allocatable, inklusive 750Mi för hård borttagning.

`0.75 + (0.25*4) + (0.20*3) = 0.75GB + 1GB + 0.6GB = 2.35GB / 7GB = 33.57% reserved`

Förutom reservationer för Kubernetes, reserverar den underliggande noden också en mängd processor-och minnes resurser för att underhålla OS-funktioner.

För associerade metod tips, se [metod tips för grundläggande funktioner i Schemaläggaren i AKS][operator-best-practices-scheduler].

### <a name="node-pools"></a>Nodpooler

Noder i samma konfiguration grupperas tillsammans i *noder i pooler*. Ett Kubernetes-kluster innehåller minst en Node-pool. Det ursprungliga antalet noder och storlek definieras när du skapar ett AKS-kluster, vilket skapar en *standardnod*. Denna standardnod i AKS innehåller de underliggande virtuella datorerna som kör dina agent-noder.

> [!NOTE]
> För att säkerställa att klustret fungerar på ett tillförlitligt sätt bör du köra minst två (2) noder i standardnodens adresspool.

Du kan skala eller uppgradera ett AKS-kluster mot standardnodens adresspool. Du kan välja att skala eller uppgradera en angiven Node-pool. För uppgraderings åtgärder schemaläggs körning av behållare på andra noder i Node-poolen tills alla noder har uppgraderats.

Mer information om hur du använder flera Node-pooler i AKS finns i [skapa och hantera flera noder för ett kluster i AKS][use-multiple-node-pools].

### <a name="node-selectors"></a>Node-väljare

I ett AKS-kluster med flera noder i pooler kan du behöva meddela Kubernetes Scheduler vilken Node-pool som ska användas för en specifik resurs. Till exempel ska ingångs styrenheter inte köras på Windows Server-noder. 

Med Node-väljare kan du definiera olika parametrar, t. ex. Node OS, för att styra var Pod ska schemaläggas.

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

Kubernetes använder *poddar* för att köra en instans av programmet. En POD representerar en enda instans av ditt program. 

Poddar har vanligt vis en 1:1-mappning med en behållare. I avancerade scenarier kan en POD innehålla flera behållare. Poddar för flera behållare schemaläggs tillsammans på samma nod och tillåter att behållare delar relaterade resurser.

När du skapar en POD kan du definiera *resurs begär Anden* för att begära en viss mängd CPU-eller minnes resurser. Kubernetes Scheduler försöker uppfylla begäran genom att schemalägga att poddar ska köras på en nod med tillgängliga resurser. Du kan också ange maximala resurs gränser för att förhindra att en POD använder för mycket data bearbetnings resurser från den underliggande noden. Bästa praxis är att inkludera resurs gränser för alla poddar som hjälper Kubernetes Scheduler att identifiera nödvändiga resurser.

Mer information finns i [Kubernetes poddar][kubernetes-pods] och [Kubernetes Pod Lifecycle][kubernetes-pod-lifecycle].

En pod är en logisk resurs, men program arbets belastningar körs på behållarna. Poddar är vanligt vis tillfälliga och disponibla resurser. Individuellt schemalagt poddar missa några av funktionerna för hög tillgänglighet och redundans Kubernetes. I stället distribueras och hanteras poddar av Kubernetes- *kontrollanter*, till exempel distributions styrenheten.

## <a name="deployments-and-yaml-manifests"></a>Distributioner och YAML-manifest

En *distribution* representerar identiska poddar som hanteras av distributions styrenheten för Kubernetes. En distribution definierar antalet Pod- *repliker* som ska skapas. Kubernetes Scheduler ser till att ytterligare poddar planeras på felfria noder om poddar eller noder stöter på problem.

Du kan uppdatera distributioner för att ändra konfigurationen för poddar, behållar avbildning som används eller bifogad lagring. Distributions styrenheten:
* Tömmer och avslutar ett angivet antal repliker.
* Skapar repliker från den nya distributions definitionen.
* Fortsätter processen tills alla repliker i distributionen har uppdaterats.

De flesta tillstånds lösa program i AKS bör använda distributions modellen i stället för att schemalägga enskilda poddar. Kubernetes kan övervaka distributionens hälso tillstånd och status för att säkerställa att antalet repliker som krävs körs i klustret. När de är schemalagda startas inte poddar om de stöter på problem, och de har inte schemalagts om på felfria noder om deras aktuella nod påträffar ett problem.

Du vill inte avbryta hanterings beslut med en uppdaterings process om ditt program kräver ett minsta antal tillgängliga instanser. *Pod avbrotts budgetar* definierar hur många repliker i en distribution som kan tas offline under en uppdatering eller nod-uppgradering. Om du till exempel har *fem (5)* repliker i distributionen kan du definiera ett Pod-avbrott på *4 (fyra)* för att bara tillåta att en replik tas bort eller schemaläggas om i taget. Precis som med Pod Resource Limits är det bästa sättet att definiera Pod avbrotts budgetar för program som kräver att det alltid finns ett minsta antal repliker.

Distributioner skapas och hanteras vanligt vis med `kubectl create` eller `kubectl apply` . Skapa en distribution genom att definiera en manifest fil i YAML-formatet. 

I följande exempel skapas en grundläggande distribution av NGINX-webbservern. I distributionen anges *tre (3)* repliker som ska skapas, och port *80* måste vara öppen på behållaren. Resurs begär Anden och gränser definieras också för processor och minne.

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

Mer komplexa program kan skapas genom att inkludera tjänster (till exempel belastningsutjämnare) i YAML-manifestet.

Mer information finns i [Kubernetes-distributioner][kubernetes-deployments].

### <a name="package-management-with-helm"></a>Paket hantering med Helm

[Helm][helm] används ofta för att hantera program i Kubernetes. Du kan distribuera resurser genom att skapa och använda befintliga offentliga Helm- *diagram* som innehåller en paketerad version av program koden och Kubernetes yaml-manifest. Du kan lagra Helm-diagram lokalt eller i en fjärrlagringsplats, till exempel ett [Azure Container Registry Helm-diagram lagrings platsen][acr-helm].

Om du vill använda Helm installerar du Helm-klienten på datorn eller använder Helm-klienten i [Azure Cloud Shell][azure-cloud-shell]. Sök efter eller skapa Helm-diagram och installera dem sedan i ditt Kubernetes-kluster. Mer information finns i [Installera befintliga program med Helm i AKS][aks-helm].

## <a name="statefulsets-and-daemonsets"></a>StatefulSets och DaemonSets

Med hjälp av Kubernetes Scheduler kör distributions hanteraren repliker på alla tillgängliga noder med tillgängliga resurser. Den här metoden kan vara tillräcklig för tillstånds lösa program, men distributions styrenheten är inte perfekt för program som kräver:
* En beständig namngivnings konvention eller lagrings plats. 
* En replik som ska finnas på varje Välj nod i ett kluster.

Med två Kubernetes-resurser kan du dock hantera följande typer av program:

- *StatefulSets* upprätthåller program tillstånd bortom en enskild Pod-livscykel, till exempel lagring.
- *DaemonSets* se till att en instans körs på varje nod, tidigt i Start processen för Kubernetes.

### <a name="statefulsets"></a>StatefulSets

Modern program utveckling syftar ofta till tillstånds lösa program. För tillstånds känsliga program, t. ex. sådana som innehåller databas komponenter, kan du använda *StatefulSets*. Precis som distributioner skapar och hanterar StatefulSet minst en identisk pod. Repliker i en StatefulSet följer en korrekt, sekventiell metod för distribution, skalning, uppgradering och avslutning. Namngivnings konventionen, nätverks namn och lagrings utrymme är kvar som repliker omplaneras med en StatefulSet.

Definiera programmet i YAML-format med hjälp av `kind: StatefulSet` . Därifrån hanterar StatefulSet-styrenheten distributionen och hanteringen av de nödvändiga replikerna. Data skrivs till beständig lagring, som tillhandahålls av Azure Managed Disks eller Azure Files. Med StatefulSets förblir den underliggande permanenta lagringen även om StatefulSet tas bort.

Mer information finns i [Kubernetes StatefulSets][kubernetes-statefulsets].

Repliker i en StatefulSet schemaläggs och körs över alla tillgängliga noder i ett AKS-kluster. För att säkerställa att minst en POD i uppsättningen körs på en nod använder du en DaemonSet i stället.

### <a name="daemonsets"></a>DaemonSets

För en viss logg samling eller övervakning kan du behöva köra en POD på alla eller valda noder. Du kan använda *DaemonSet* för att distribuera en eller flera identiska poddar, men DaemonSet-styrenheten säkerställer att varje nod som anges kör en instans av pod.

DaemonSet-kontrollanten kan schemalägga poddar på noderna tidigt i kluster start processen, innan standard-Kubernetes Scheduler har startat. Den här funktionen säkerställer att poddar i en DaemonSet startas innan traditionella poddar i en distribution eller StatefulSet schemaläggs.

Som StatefulSets definieras en DaemonSet som en del av en YAML-definition med hjälp av `kind: DaemonSet` .

Mer information finns i [Kubernetes DaemonSets][kubernetes-daemonset].

> [!NOTE]
> Om du använder [tillägg för virtuella noder](virtual-nodes-cli.md#enable-virtual-nodes-addon)kommer DaemonSets inte att skapa poddar på den virtuella noden.

## <a name="namespaces"></a>Namnrymder

Kubernetes-resurser, till exempel poddar och distributioner, grupperas logiskt i ett *namn område* för att dela upp ett AKS-kluster och begränsa skapande, visning eller hantering av åtkomst till resurser. Du kan till exempel skapa namn områden för att separera affärs grupper. Användare kan bara interagera med resurser inom de tilldelade namn områdena.

![Kubernetes namn områden som logiskt delar resurser och program](media/concepts-clusters-workloads/namespaces.png)

När du skapar ett AKS-kluster är följande namn rymder tillgängliga:

| Namnområde | Beskrivning |  
| ----------------- | ------------- |  
| *standard*                                                                                 | Där poddar och distributioner skapas som standard när inget anges. I mindre miljöer kan du distribuera program direkt till standard namn området utan att skapa ytterligare logiska separationer. När du interagerar med Kubernetes-API: t, till exempel med `kubectl get pods` , används standard namn området när inget anges.                                                        |  
| *kube-system* | Där kärn resurser finns, till exempel nätverks funktioner som DNS och proxy eller Kubernetes-instrumentpanelen. Du distribuerar vanligt vis inte dina egna program till det här namn området.                                      |  
| *kube-public*                                                                            | Används vanligt vis inte, men kan användas för att visa resurser i hela klustret, och de kan visas av alla användare.                                                                                    |  


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
