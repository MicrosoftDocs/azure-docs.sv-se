---
title: Vanliga frågor och svar om Azure Kubernetes Service (AKS)
description: Få svar på några vanliga frågor om Azure Kubernetes Service (AKS).
ms.topic: conceptual
ms.date: 08/06/2020
ms.custom: references_regions
ms.openlocfilehash: f13d7a33ce1dc04700932072fe0af80a901c681f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783205"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Vanliga frågor om Azure Kubernetes Service (AKS)

Den här artikeln tar upp vanliga frågor om Azure Kubernetes Service (AKS).

## <a name="which-azure-regions-currently-provide-aks"></a>Vilka Azure-regioner tillhandahåller för närvarande AKS?

En fullständig lista över tillgängliga regioner finns i [AKS-regioner och tillgänglighet.][aks-regions]

## <a name="can-i-spread-an-aks-cluster-across-regions"></a>Kan jag sprida ett AKS-kluster mellan regioner?

Nej. AKS-kluster är regionala resurser och kan inte sträcka sig över flera regioner. Se [metodtips för affärskontinui och haveriberedskap][bcdr-bestpractices] för vägledning om hur du skapar en arkitektur som omfattar flera regioner.

## <a name="can-i-spread-an-aks-cluster-across-availability-zones"></a>Kan jag sprida ett AKS-kluster över tillgänglighetszoner?

Ja. Du kan distribuera ett AKS-kluster över en eller flera [tillgänglighetszoner][availability-zones] i [regioner som stöder dem.][az-regions]

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>Kan jag begränsa vem som har åtkomst till Kubernetes API-servern?

Ja. Det finns två alternativ för att begränsa åtkomsten till API-servern:

- Använd [API Server-auktoriserade IP-intervall][api-server-authorized-ip-ranges] om du vill behålla en offentlig slutpunkt för API-servern men begränsa åtkomsten till en uppsättning betrodda IP-intervall.
- Använd [ett privat kluster][private-clusters] om du vill begränsa API-servern till att *endast* vara tillgänglig inifrån det virtuella nätverket.

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>Kan jag ha olika VM-storlekar i ett enda kluster?

Ja, du kan använda olika storlekar för virtuella datorer i ditt AKS-kluster genom att skapa flera [nodpooler.][multi-node-pools]

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Tillämpas säkerhetsuppdateringar på AKS-agentnoder?

Azure tillämpar automatiskt säkerhetskorrigeringar på Linux-noderna i klustret enligt ett schema varje natt. Du ansvarar dock för att se till att Linux-noderna startas om efter behov. Du har flera alternativ för att starta om noder:

- Manuellt via Azure Portal eller Azure CLI.
- Genom att uppgradera ditt AKS-kluster. Klustret uppgraderar [avspärrnings-][cordon-drain] och tömningsnoder automatiskt och tar sedan en ny nod online med den senaste Ubuntu-avbildningen och en ny korrigeringsversion eller en mindre Kubernetes-version. Mer information finns i Uppgradera [ett AKS-kluster.][aks-upgrade]
- Med hjälp av [nodavbildningsuppgradering](node-image-upgrade.md).

### <a name="windows-server-nodes"></a>Windows Server-noder

För Windows Server-Windows Update inte automatiskt och tillämpar de senaste uppdateringarna. Enligt ett regelbundet schema runt Windows Update-lanseringscykeln och din egen verifieringsprocess bör du utföra en uppgradering på klustret och Windows Server-nodpoolerna i ditt AKS-kluster. Den här uppgraderingsprocessen skapar noder som kör den senaste Windows Server-avbildningen och korrigeringarna och tar sedan bort de äldre noderna. Mer information om den här processen finns i [Uppgradera en nodpool i AKS.][nodepool-upgrade]

## <a name="why-are-two-resource-groups-created-with-aks"></a>Varför skapas två resursgrupper med AKS?

AKS bygger på ett antal Azure-infrastrukturresurser, inklusive VM-skalningsuppsättningar, virtuella nätverk och hanterade diskar. På så sätt kan du utnyttja många av de viktigaste funktionerna i Azure-plattformen i den hanterade Kubernetes-miljön som tillhandahålls av AKS. De flesta typer av virtuella Azure-datorer kan till exempel användas direkt med AKS och Azure-reservationer kan användas för att automatiskt få rabatter för dessa resurser.

För att aktivera den här arkitekturen omfattar varje AKS-distribution två resursgrupper:

1. Du skapar den första resursgruppen. Den här gruppen innehåller bara Kubernetes Service-resursen. AKS-resursprovidern skapar automatiskt den andra resursgruppen under distributionen. Ett exempel på den andra resursgruppen är *MC_myResourceGroup_myAKSCluster_eastus*. Information om hur du anger namnet på den här andra resursgruppen finns i nästa avsnitt.
1. Den andra resursgruppen, som kallas *nodresursgruppen*, innehåller alla infrastrukturresurser som är associerade med klustret. Dessa resurser omfattar virtuella Kubernetes-nods-datorer, virtuella nätverk och lagring. Som standard har nodresursgruppen ett namn som *MC_myResourceGroup_myAKSCluster_eastus*. AKS tar automatiskt bort nodresursen när klustret tas bort, så det bör endast användas för resurser som delar klustrets livscykel.

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>Kan jag ange mitt eget namn för resursgruppen för AKS-noden?

Ja. Som standard ger AKS nodresursgruppen namnet *MC_resourcegroupname_clustername_location*, men du kan också ange ditt eget namn.

Om du vill ange ett eget resursgruppsnamn installerar du [Azure CLI-tillägget aks-preview][aks-preview-cli] *version 0.3.2* eller senare. När du skapar ett AKS-kluster med kommandot [az aks create][az-aks-create] använder du parametern och `--node-resource-group` anger ett namn för resursgruppen. Om du [använder en Azure Resource Manager för][aks-rm-template] att distribuera ett AKS-kluster kan du definiera resursgruppens namn med hjälp av egenskapen *nodeResourceGroup.*

* Den sekundära resursgruppen skapas automatiskt av Azure-resursprovidern i din egen prenumeration.
* Du kan bara ange ett anpassat resursgruppnamn när du skapar klustret.

När du arbetar med nodresursgruppen bör du komma ihåg att du inte kan:

* Ange en befintlig resursgrupp för nodresursgruppen.
* Ange en annan prenumeration för nodresursgruppen.
* Ändra namnet på nodresursgruppen när klustret har skapats.
* Ange namn för de hanterade resurserna i nodresursgruppen.
* Ändra eller ta bort Azure-skapade taggar för hanterade resurser i nodresursgruppen. (Se ytterligare information i nästa avsnitt.)

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group"></a>Kan jag ändra taggar och andra egenskaper för AKS-resurserna i nodresursgruppen?

Om du ändrar eller tar bort taggar som skapats av Azure och andra resursegenskaper i nodresursgruppen kan du få oväntade resultat som skalning och uppgraderingsfel. Med AKS kan du skapa och ändra anpassade taggar som skapats av slutanvändare och du kan lägga till dessa taggar när du [skapar en nodpool](use-multiple-node-pools.md#specify-a-taint-label-or-tag-for-a-node-pool). Du kanske vill skapa eller ändra anpassade taggar, till exempel för att tilldela en affärsenhet eller ett kostnadsställe. Detta kan också uppnås genom att skapa Azure-principer med ett omfång för den hanterade resursgruppen.

Men att ändra **azure-skapade** taggar på resurser under nodresursgruppen i AKS-klustret är en åtgärd som inte stöds, vilket bryter servicenivåmålet (SLO). Mer information finns i [Erbjuder AKS ett serviceavtal?](#does-aks-offer-a-service-level-agreement)

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>Vilka Kubernetes-antagningsstyrenheter stöder AKS? Kan antagningskontrollanter läggas till eller tas bort?

AKS stöder följande [antagningskontrollanter:][admission-controllers]

- *NamnrymdLivscykel*
- *LimitRanger*
- *ServiceAccount*
- *DefaultStorageClass*
- *DefaultTolerationSeconds*
- *MutatingAdmissionWebhook*
- *ValidatingAdmissionWebhook*
- *ResourceQuota*
- *PodNodeSelector*
- *PodTolerationRestriction*
- *ExtendedResourceToleration*

För närvarande kan du inte ändra listan över antagningsstyrenheter i AKS.

## <a name="can-i-use-admission-controller-webhooks-on-aks"></a>Kan jag använda webhooks för antagningskontrollant i AKS?

Ja, du kan använda webhooks för antagningskontrollant på AKS. Vi rekommenderar att du undantar interna AKS-namnrymder som har markerats med **kontrollplansetiketten.** Till exempel genom att lägga till nedanstående i webhook-konfigurationen:

```
namespaceSelector:
    matchExpressions:
    - key: control-plane
      operator: DoesNotExist
```

AKS brandväggar utgående API-server så att webhookarna för antagningskontrollanten måste vara tillgängliga från klustret.

## <a name="can-admission-controller-webhooks-impact-kube-system-and-internal-aks-namespaces"></a>Kan webhooks för antagningskontrollanter påverka kube-system och interna AKS-namnrymder?

För att skydda systemets stabilitet och förhindra att anpassade antagningskontrollanter påverkar interna tjänster i kube-system, har namnrymden AKS en **antagningskontrollant**, som automatiskt exkluderar interna namnrymder för kube-system och AKS. Den här tjänsten säkerställer att de anpassade antagningskontrollanterna inte påverkar de tjänster som körs i kube-system.

Om du har ett kritiskt användningsfall för att ha något distribuerat på kube-system (rekommenderas inte) som du behöver omfattas av din anpassade webhook för antagning kan du lägga till etiketten nedan eller anteckningen så att Admissions Enforcer ignorerar den.

Etikett: ```"admissions.enforcer/disabled": "true"``` eller anteckning: ```"admissions.enforcer/disabled": true```

## <a name="is-azure-key-vault-integrated-with-aks"></a>Är Azure Key Vault integrerad med AKS?

AKS är för närvarande inte inbyggt i Azure Key Vault. Dock möjliggör [Azure Key Vault CSI Secrets Store][csi-driver] direkt integrering från Kubernetes-poddar för att Key Vault hemligheter.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Kan jag köra Windows Server-containrar på AKS?

Ja, Windows Server-containrar är tillgängliga i AKS. Om du vill köra Windows Server-containrar i AKS skapar du en nodpool som kör Windows Server som gästoperativsystem. Windows Server-containrar kan bara använda Windows Server 2019. Kom igång genom att gå till [Skapa ett AKS-kluster med en Windows Server-nodpool.][aks-windows-cli]

Windows Server-stöd för nodpooler innehåller vissa begränsningar som ingår i det överordnade Windows Server-projektet i Kubernetes. Mer information om dessa begränsningar finns i [Windows Server-containrar i AKS-begränsningar.][aks-windows-limitations]

## <a name="does-aks-offer-a-service-level-agreement"></a>Erbjuder AKS ett serviceavtal?

AKS tillhandahåller SLA-garantier som en valfri tilläggsfunktion med [drifttids-SLA.][uptime-sla] 

Den kostnadsfria SKU:n som erbjuds som standard har inget  associerat serviceavtal, men har ett servicenivåmål på 99,5 %. Det kan hända att tillfälliga anslutningsproblem observeras vid uppgraderingar, felaktiga underlagernoder, plattformsunderhåll, program som överväldigar API-servern med begäranden osv. Om din arbetsbelastning inte tolererar omstart av API Server föreslår vi att du använder serviceavtalet för drifttid.

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>Kan jag tillämpa rabatter för Azure-reservationer på mina AKS-agentnoder?

AKS-agentnoder debiteras som virtuella Azure-standarddatorer, [][reservation-discounts] så om du har köpt Azure-reservationer för den VM-storlek som du använder i AKS tillämpas dessa rabatter automatiskt.

## <a name="can-i-movemigrate-my-cluster-between-azure-tenants"></a>Kan jag flytta/migrera mitt kluster mellan Azure-klienter?

Det finns för närvarande inte stöd för att flytta ditt AKS-kluster mellan klienter.

## <a name="can-i-movemigrate-my-cluster-between-subscriptions"></a>Kan jag flytta/migrera mitt kluster mellan prenumerationer?

Förflyttning av kluster mellan prenumerationer stöds för närvarande inte.

## <a name="can-i-move-my-aks-clusters-from-the-current-azure-subscription-to-another"></a>Kan jag flytta mina AKS-kluster från den aktuella Azure-prenumerationen till en annan?

Det går inte att flytta ditt AKS-kluster och dess associerade resurser mellan Azure-prenumerationer.

## <a name="can-i-move-my-aks-cluster-or-aks-infrastructure-resources-to-other-resource-groups-or-rename-them"></a>Kan jag flytta mina AKS-kluster eller AKS-infrastrukturresurser till andra resursgrupper eller byta namn på dem?

Det går inte att flytta eller byta namn på AKS-klustret och dess associerade resurser.

## <a name="why-is-my-cluster-delete-taking-so-long"></a>Varför tar kluster borttagningen så lång tid?

De flesta kluster tas bort vid användarbegäran. i vissa fall, särskilt när kunder tar med sin egen resursgrupp eller utför åtgärder för flera RG-aktiviteter kan ta ytterligare tid eller misslyckas. Om du har problem med borttagningar kontrollerar du att du inte har lås på RG:en, att resurser utanför RG inte är associerade med RG och så vidare.

## <a name="if-i-have-pod--deployments-in-state-nodelost-or-unknown-can-i-still-upgrade-my-cluster"></a>Om jag har poddar/distributioner i tillståndet NodeLost eller Okänd, kan jag fortfarande uppgradera mitt kluster?

Det kan du, men AKS rekommenderar inte detta. Uppgraderingar bör utföras när klustrets tillstånd är känt och felfritt.

## <a name="if-i-have-a-cluster-with-one-or-more-nodes-in-an-unhealthy-state-or-shut-down-can-i-perform-an-upgrade"></a>Kan jag utföra en uppgradering om jag har ett kluster med en eller flera noder i ett felaktigt tillstånd eller stängs av?

Nej, ta bort/ta bort noder i ett misslyckat tillstånd eller på annat sätt bort från klustret innan du uppgraderar.

## <a name="i-ran-a-cluster-delete-but-see-the-error-errno-11001-getaddrinfo-failed"></a>Jag har kört en kluster borttagning, men ser felet `[Errno 11001] getaddrinfo failed`

Detta beror oftast på att användare har en eller flera nätverkssäkerhetsgrupper (NSG:er) som fortfarande används och är associerade med klustret.  Ta bort dem och försök att ta bort dem igen.

## <a name="i-ran-an-upgrade-but-now-my-pods-are-in-crash-loops-and-readiness-probes-fail"></a>Jag har kört en uppgradering, men nu är mina poddar i kraschslingor och beredskapsavsökningarna misslyckas?

Bekräfta att tjänstens huvudnamn inte har upphört att gälla.  Se: [AKS-tjänstens huvudnamn](./kubernetes-service-principal.md) och [AKS uppdaterar autentiseringsuppgifter.](./update-credentials.md)

## <a name="my-cluster-was-working-but-suddenly-cant-provision-loadbalancers-mount-pvcs-etc"></a>Mitt kluster fungerade, men kan plötsligt inte etablera LoadBalancers, montera PVC:er osv.?

Bekräfta att tjänstens huvudnamn inte har upphört att gälla.  Se: [AKS-tjänstens huvudnamn](./kubernetes-service-principal.md) och [AKS uppdaterar autentiseringsuppgifter.](./update-credentials.md)

## <a name="can-i-scale-my-aks-cluster-to-zero"></a>Kan jag skala mitt AKS-kluster till noll?
Du kan helt [stoppa ett AKS-kluster som](start-stop-cluster.md)körs, vilket sparar på respektive beräkningskostnader. Dessutom kan du välja att skala eller autoskala alla eller specifika [ `User` nodpooler](scale-cluster.md#scale-user-node-pools-to-0) till 0, vilket endast upprätthåller den klusterkonfiguration som krävs.
Du kan inte direkt skala [systemnodpooler](use-system-pools.md) till noll.

## <a name="can-i-use-the-virtual-machine-scale-set-apis-to-scale-manually"></a>Kan jag använda API:er för VM-skalningsuppsättningen för att skala manuellt?

Nej, skalningsåtgärder med hjälp av VM-skalningsuppsättningens API:er stöds inte. Använd AKS-API:erna ( `az aks scale` ).

## <a name="can-i-use-virtual-machine-scale-sets-to-manually-scale-to-zero-nodes"></a>Kan jag använda VM-skalningsuppsättningar för att manuellt skala till noll noder?

Nej, skalningsåtgärder med hjälp av API:erna för VM-skalningsuppsättningen stöds inte. Du kan använda AKS-API:et för att skala till noll nodpooler som inte är [systemnoder eller stoppa klustret i](start-stop-cluster.md) stället.

## <a name="can-i-stop-or-de-allocate-all-my-vms"></a>Kan jag stoppa eller ta bort allokera alla mina virtuella datorer?

Även om AKS har motståndskraftsmekanismer för att klara en sådan konfiguration och återställa från den, är detta inte en konfiguration som stöds. [Stoppa klustret i](start-stop-cluster.md) stället.

## <a name="can-i-use-custom-vm-extensions"></a>Kan jag använda anpassade VM-tillägg?

Log Analytics-agenten stöds eftersom det är ett tillägg som hanteras av Microsoft. Annars nej, AKS är en hanterad tjänst och manipulering av IaaS-resurser stöds inte. Om du vill installera anpassade komponenter använder du Kubernetes API:er och mekanismer. Använd till exempel DaemonSets för att installera nödvändiga komponenter.

## <a name="does-aks-store-any-customer-data-outside-of-the-clusters-region"></a>Lagrar AKS kunddata utanför klustrets region?

Funktionen för att möjliggöra lagring av kunddata i en enda region är för närvarande endast tillgänglig i Sydostasien-regionen (Singapore) i regionen Asien och stillahavsområdet Geo och Brasilien, södra (Delstaten Sao Paulo) Brasilien. För alla andra regioner lagras kunddata i Geo.

## <a name="are-aks-images-required-to-run-as-root"></a>Krävs AKS-avbildningar för att köras som rot?

Förutom följande två avbildningar behöver inte AKS-avbildningar köras som rot:

- *mcr.microsoft.com/oss/kubernetes/coredns*
- *mcr.microsoft.com/azuremonitor/containerinsights/ciprod*

## <a name="what-is-azure-cni-transparent-mode-vs-bridge-mode"></a>Vad är Azure CNI transparent läge jämfört med bryggläge?

Från v1.2.0 Azure CNI transparent läge som standard för linux CNI-distributioner med enskild innehavare. Transparent läge ersätter bryggläget. I det här avsnittet diskuterar vi mer om skillnaderna mellan båda lägena och vilka fördelar/begränsningar som finns vid användning av transparent läge i Azure CNI.

### <a name="bridge-mode"></a>Bryggläge

Som namnet antyder skapar bridge mode Azure CNI på ett "just-in-time"-sätt en L2-brygga med namnet "azure0". Alla gränssnitt för `veth` poddpar på värdsidan ansluts till den här bryggan. Så Pod-Pod kommunikationen mellan virtuella datorer och den återstående trafiken går genom den här bryggan. Bryggan i fråga är en virtuell Layer 2-enhet som på egen hand inte kan ta emot eller överföra något såvida du inte binder en eller flera verkliga enheter till den. Därför måste eth0 för den virtuella Linux-datorn konverteras till en underordnad "azure0"-brygga. Detta skapar en komplex nätverkstopologi i den virtuella Linux-datorn och som ett symtom var CNI tvungen att ta hand om andra nätverksfunktioner som DNS-serveruppdatering och så vidare.

:::image type="content" source="media/faq/bridge-mode.png" alt-text="Topologi för bryggläge":::

Nedan visas ett exempel på hur konfigurationen av IP-vägen ser ut i Bridge-läge. Oavsett hur många poddar noden har finns det bara två vägar. Den första säger att all trafik exklusive lokal på azure0 går till standardgatewayen för undernätet via gränssnittet med ip"src 10.240.0.4" (som är Node primär IP) och den andra säger "10.20.x.x" Pod space to kernel for kernel to decide.

```bash
default via 10.240.0.1 dev azure0 proto dhcp src 10.240.0.4 metric 100
10.240.0.0/12 dev azure0 proto kernel scope link src 10.240.0.4
172.17.0.0/16 dev docker0 proto kernel scope link src 172.17.0.1 linkdown
root@k8s-agentpool1-20465682-1:/#
```

### <a name="transparent-mode"></a>Transparent läge
Transparent läge har en tydlig metod för att konfigurera Linux-nätverk. I det här Azure CNI inte några egenskaper för eth0-gränssnittet på den virtuella Linux-datorn. Den här minimala metoden för att ändra Linux-nätverksegenskaper hjälper till att minska komplexa problem med hörnfall som kluster kan få med bridge-läget. I transparent läge skapar Azure CNI och lägger till gränssnitt för poddpar på `veth` värdsidan som läggs till i värdnätverket. Kommunikation mellan virtuella datorer med podd-till-podd är via IP-vägar som CNI kommer att lägga till. I princip är kommunikation mellan poddar över layer 3 och poddtrafik dirigeras av L3-routningsregler.

:::image type="content" source="media/faq/transparent-mode.png" alt-text="Topologi för transparent läge":::

Nedan visas ett exempel på en IP-vägskonfiguration av transparent läge. Varje podds gränssnitt får en statisk väg kopplad så att trafik med den första IP-adressen eftersom podden skickas direkt till poddens gränssnitt för `veth` värdsidans par.

```bash
10.240.0.216 dev azv79d05038592 proto static
10.240.0.218 dev azv8184320e2bf proto static
10.240.0.219 dev azvc0339d223b9 proto static
10.240.0.222 dev azv722a6b28449 proto static
10.240.0.223 dev azve7f326f1507 proto static
10.240.0.224 dev azvb3bfccdd75a proto static
168.63.129.16 via 10.240.0.1 dev eth0 proto dhcp src 10.240.0.4 metric 100
169.254.169.254 via 10.240.0.1 dev eth0 proto dhcp src 10.240.0.4 metric 100
172.17.0.0/16 dev docker0 proto kernel scope link src 172.17.0.1 linkdown
```

### <a name="benefits-of-transparent-mode"></a>Fördelar med transparent läge

- Åtgärdar DNS-parallella rastillstånd och undviker problem med DNS-svarstider på 5 sek utan att du behöver konfigurera lokal DNS för noder (du kan fortfarande använda nod-lokal `conntrack` DNS av prestandaskäl).
- Eliminerar den inledande CNI-bryggningsläget på 5 sekunders DNS-svarstid som introduceras idag på grund av just-in-time-bryggkonfigurationen.
- Ett av hörnfallen i bryggläge är att Azure CNI kan fortsätta uppdatera den anpassade DNS-serverlistan som användare lägger till i ANTINGEN VNET eller nätverkskortet. Detta resulterar i att CNI endast hämtar den första instansen av DNS-serverlistan. Löses i transparent läge eftersom CNI inte ändrar några eth0-egenskaper. Mer information [finns här.](https://github.com/Azure/azure-container-networking/issues/713)
- Ger bättre hantering av UDP-trafik och riskreducering för UDP-stormar när ARP-tiden går ut. När bridge inte känner till en MAC-adress för målpodden i kommunikation mellan virtuella datorer i bryggläge, resulterar detta i en storm av paketet till alla portar. Löst i transparent läge eftersom det inte finns några L2-enheter i sökvägen. Mer information [finns här.](https://github.com/Azure/azure-container-networking/issues/704)
- Transparent läge presterar bättre i kommunikation mellan virtuella datorer med podd-till-podd vad gäller dataflöde och svarstid jämfört med bryggläge.

## <a name="how-to-avoid-permission-ownership-setting-slow-issues-when-the-volume-has-a-lot-of-files"></a>Hur undviker du problem med att ställa in behörighetsägarskap när volymen har många filer?

Traditionellt om din podd körs som en icke-rotanvändare (vilket du bör), måste du ange en inuti poddens säkerhetskontext så att volymen kan läsas och vara skrivbar av `fsGroup` podden. Det här kravet tas upp i detalj [i här](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/).

Men en sidoeffekt av inställningen är att kubernetes måste rekursivt och alla filer och kataloger i volymen varje gång en volym monteras, med några få undantag som anges `fsGroup` `chown()` `chmod()` nedan. Detta inträffar även om gruppägarskapet för volymen redan matchar den begärda , och kan vara ganska dyrt för större volymer med många små filer, vilket gör att `fsGroup` poddstarten tar lång tid. Det här scenariot har varit ett känt problem före v1.20 och lösningen är att ange podden som rot:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext:
    runAsUser: 0
    fsGroup: 0
```

Problemet har lösts av Kubernetes v1.20. Mer information finns i [Kubernetes 1.20: Detaljerad](https://kubernetes.io/blog/2020/12/14/kubernetes-release-1.20-fsgroupchangepolicy-fsgrouppolicy/) kontroll över volymbehörighetsändringar.


<!-- LINKS - internal -->

[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./cluster-autoscaler.md
[aks-advanced-networking]: ./configure-azure-cni.md
[aks-rbac-aad]: ./azure-ad-integration-cli.md
[node-updates-kured]: node-updates-kured.md
[aks-preview-cli]: /cli/azure/ext/aks-preview/aks
[az-aks-create]: /cli/azure/aks#az_aks_create
[aks-rm-template]: /azure/templates/microsoft.containerservice/2019-06-01/managedclusters
[aks-cluster-autoscaler]: cluster-autoscaler.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[aks-windows-cli]: windows-container-cli.md
[aks-windows-limitations]: ./windows-faq.md
[reservation-discounts]:../cost-management-billing/reservations/save-compute-costs-reservations.md
[api-server-authorized-ip-ranges]: ./api-server-authorized-ip-ranges.md
[multi-node-pools]: ./use-multiple-node-pools.md
[availability-zones]: ./availability-zones.md
[private-clusters]: ./private-clusters.md
[bcdr-bestpractices]: ./operator-best-practices-multi-region.md#plan-for-multiregion-deployment
[availability-zones]: ./availability-zones.md
[az-regions]: ../availability-zones/az-region.md
[uptime-sla]: ./uptime-sla.md

<!-- LINKS - external -->
[aks-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[private-clusters-github-issue]: https://github.com/Azure/AKS/issues/948
[csi-driver]: https://github.com/Azure/secrets-store-csi-driver-provider-azure
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines/
