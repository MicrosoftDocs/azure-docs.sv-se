---
title: Konfigurera Azure CNI Networking i Azure Kubernetes service (AKS)
description: Lär dig hur du konfigurerar Azure CNI (avancerat) nätverk i Azure Kubernetes service (AKS), inklusive hur du distribuerar ett AKS-kluster till ett befintligt virtuellt nätverk och undernät.
services: container-service
ms.topic: article
ms.date: 06/03/2019
ms.openlocfilehash: 0506eb6350358f7256a61c8d6f164b6594d20554
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88566122"
---
# <a name="configure-azure-cni-networking-in-azure-kubernetes-service-aks"></a>Konfigurera Azure CNI Networking i Azure Kubernetes service (AKS)

Som standard använder AKS-kluster [Kubernetes][kubenet]och ett virtuellt nätverk och undernät skapas åt dig. Med *Kubernetes*hämtar noder en IP-adress från ett virtuellt nätverks under nät. NAT (Network Address Translation) konfigureras sedan på noderna och poddar får en IP-adress "Hidden" bakom nodens IP-adress. Den här metoden minskar antalet IP-adresser som du behöver reservera i ditt nätverks utrymme för att poddar ska kunna användas.

Med [Azure Container Network Interface (cni)][cni-networking]hämtar varje Pod en IP-adress från under nätet och kan nås direkt. De här IP-adresserna måste vara unika i ditt nätverks utrymme och måste planeras i förväg. Varje nod har en konfigurations parameter för det maximala antalet poddar som stöds. Motsvarande antal IP-adresser per nod är sedan reserverade för den noden. Den här metoden kräver mer planering, och leder ofta till IP-prisslutning eller behovet av att återskapa kluster i ett större undernät när ditt program kräver en tillväxt.

Den här artikeln visar hur du använder *Azure cni* Networking för att skapa och använda ett virtuellt nätverks under nät för ett AKS-kluster. Mer information om nätverks alternativ och överväganden finns i [nätverks koncept för Kubernetes och AKS][aks-network-concepts].

## <a name="prerequisites"></a>Förutsättningar

* Det virtuella nätverket för AKS-klustret måste tillåta utgående Internet anslutning.
* AKS-kluster får inte använda `169.254.0.0/16` , `172.30.0.0/16` , `172.31.0.0/16` eller `192.0.2.0/24` för Kubernetes-tjänstens adress intervall.
* Tjänstens huvud namn som används av AKS-klustret måste ha minst [nätverks deltagar](../role-based-access-control/built-in-roles.md#network-contributor) behörighet för under nätet i det virtuella nätverket. Om du vill definiera en [anpassad roll](../role-based-access-control/custom-roles.md) i stället för att använda den inbyggda rollen nätverks deltagare, krävs följande behörigheter:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`
* I stället för ett huvud namn för tjänsten kan du använda systemtilldelad hanterad identitet för behörigheter. Mer information finns i [använda hanterade identiteter](use-managed-identity.md).
* Under nätet som tilldelats till AKS-noden kan inte vara ett [delegerat undernät](../virtual-network/subnet-delegation-overview.md).

## <a name="plan-ip-addressing-for-your-cluster"></a>Planera IP-adresser för klustret

Kluster som kon figurer ATS med Azure CNI-nätverk kräver ytterligare planering. Storleken på ditt virtuella nätverk och dess undernät måste innehålla antalet poddar som du planerar att köra och antalet noder för klustret.

IP-adresserna för poddar och klustrets noder tilldelas från det angivna under nätet i det virtuella nätverket. Varje nod konfigureras med en primär IP-adress. Som standard är 30 ytterligare IP-adresser förkonfigurerade av Azure-CNI som har tilldelats poddar schemalagda på noden. När du skalar upp klustret, konfigureras varje nod på samma sätt med IP-adresser från under nätet. Du kan också visa den [maximala poddar per nod](#maximum-pods-per-node).

> [!IMPORTANT]
> Antalet IP-adresser som krävs bör innehålla överväganden vid uppgradering och skalnings åtgärder. Om du ställer in IP-adressintervallet så att det bara stöder ett fast antal noder kan du inte uppgradera eller skala klustret.
>
> - När du **uppgraderar** AKS-klustret distribueras en ny nod i klustret. Tjänster och arbets belastningar börjar köras på den nya noden och en äldre nod tas bort från klustret. Den här löpande uppgraderings processen kräver minst ett ytterligare block med IP-adresser för att vara tillgängligt. Antalet noder är sedan `n + 1` .
>   - Detta är särskilt viktigt när du använder Windows Server-nodkonfigurationer. Windows Server-noder i AKS tillämpar inte automatiskt Windows-uppdateringar, i stället för att utföra en uppgradering på Node-poolen. Den här uppgraderingen distribuerar nya noder med den senaste Windows Server 2019 Base Node-avbildningen och säkerhets uppdateringar. Mer information om hur du uppgraderar en pool för Windows Server-noder finns [i uppgradera en Node-pool i AKS][nodepool-upgrade].
>
> - När du **skalar** ett AKS-kluster distribueras en ny nod i klustret. Tjänster och arbets belastningar börjar köras på den nya noden. Ditt IP-adressintervall måste ta hänsyn till hur du kanske vill skala upp antalet noder och poddar ditt kluster kan stödja. Ytterligare en nod för uppgraderings åtgärder bör också inkluderas. Antalet noder är sedan `n + number-of-additional-scaled-nodes-you-anticipate + 1` .

Om du förväntar dig att noderna ska köra det maximala antalet poddar och regelbundet förstöra och distribuera poddar, bör du även faktor i vissa ytterligare IP-adresser per nod. Dessa ytterligare IP-adresser tar hänsyn till det kan ta några sekunder för en tjänst att tas bort och IP-adressen som publicerats för att en ny tjänst ska distribueras och erhålla adressen.

IP-adressboken för ett AKS-kluster består av ett virtuellt nätverk, minst ett undernät för noder och poddar och ett Kubernetes service-adressintervall.

| Adress intervall/Azure-resurs | Gränser och storlek |
| --------- | ------------- |
| Virtuellt nätverk | Det virtuella Azure-nätverket kan vara så stort som/8, men det är begränsat till 65 536 konfigurerade IP-adresser. Överväg alla dina nätverks behov, inklusive kommunikation med tjänster i andra virtuella nätverk, innan du konfigurerar adress utrymmet. Om du till exempel konfigurerar för stort av ett adress utrymme kan du stöta på problem med överlappande andra adress utrymmen i nätverket.|
| Undernät | Måste vara tillräckligt stor för att rymma noderna, poddar och alla Kubernetes och Azure-resurser som kan vara etablerade i klustret. Om du till exempel distribuerar en intern Azure Load Balancer allokeras klientens IP-adresser från klustrets undernät, inte offentliga IP-adresser. Under näts storleken bör också ta hänsyn till uppgraderings åtgärder eller framtida skalnings behov.<p />Beräkna den *minsta* under näts storleken inklusive ytterligare en nod för uppgraderings åtgärder: `(number of nodes + 1) + ((number of nodes + 1) * maximum pods per node that you configure)`<p/>Exempel på ett 50-nods kluster: `(51) + (51  * 30 (default)) = 1,581` (/21 eller större)<p/>Exempel för ett 50-nods kluster som även omfattar etablering för att skala upp ytterligare 10 noder: `(61) + (61 * 30 (default)) = 1,891` (/21 eller större)<p>Om du inte anger ett maximalt antal poddar per nod när du skapar klustret, anges det maximala antalet poddar per nod till *30*. Det minsta antalet IP-adresser som krävs baseras på det värdet. Om du beräknar krav för minsta IP-adress på ett annat Max värde, se [så här konfigurerar du det maximala antalet poddar per nod](#configure-maximum---new-clusters) för att ange det här värdet när du distribuerar klustret. |
| Kubernetes Service-adressintervall | Intervallet ska inte användas av något nätverks element på eller är anslutet till det här virtuella nätverket. Tjänst adressens CIDR måste vara mindre än/12. Du kan återanvända det här intervallet över olika AKS-kluster. |
| IP-adress för Kubernetes DNS-tjänst | IP-adress inom Kubernetes-tjänstens adress intervall som ska användas av kluster tjänst identifiering (Kube-DNS). Använd inte den första IP-adressen i adress intervallet, till exempel. 1. Den första adressen i under nätets intervall används för *Kubernetes. default. svc. Cluster. local* . |
| Docker-bryggadress | Docker-bryggnätverksadressen representerar den standardmässiga *docker0*-bryggnätverksadress som finns i alla Docker-installationer. Även om *docker0* -bryggan inte används av AKS-kluster eller själva poddar, måste du ange den här adressen så att den fortfarande stöder scenarier som *Docker-build* i AKS-klustret. Det krävs att du väljer en CIDR för Docker-bryggans nätverks adress eftersom en annan Docker kommer att välja ett undernät automatiskt som kan hamna i konflikt med andra CIDR-enheter. Du måste välja ett adress utrymme som inte kolliderar med resten av CIDR-inställningarna i dina nätverk, inklusive klustrets tjänst-CIDR och Pod CIDR. Standard för 172.17.0.1/16. Du kan återanvända det här intervallet över olika AKS-kluster. |

## <a name="maximum-pods-per-node"></a>Maximalt antal poddar per nod

Det maximala antalet poddar per nod i ett AKS-kluster är 250. *Standardvärdet* för högsta antal poddar per nod varierar mellan *KUBERNETES* och *Azure cni* -nätverk och metoden för kluster distribution.

| Distributions metod | Kubernetes standard | Standard för Azure-CNI | Kan konfigureras vid distribution |
| -- | :--: | :--: | -- |
| Azure CLI | 110 | 30 | Ja (upp till 250) |
| Resource Manager-mall | 110 | 30 | Ja (upp till 250) |
| Portalen | 110 | 30 | No |

### <a name="configure-maximum---new-clusters"></a>Konfigurera högsta – nya kluster

Du kan konfigurera maximalt antal poddar per nod vid kluster distributions tid eller när du lägger till nya nodkonfigurationer. Om du distribuerar med Azure CLI eller med en Resource Manager-mall, kan du ange den maximala poddar per nod-värde så högt som 250.

Om du inte anger maxPods när du skapar nya noder får du ett standardvärde på 30 för Azure CNI.

Ett minsta värde för maximalt poddar per nod upprätthålls för att garantera utrymme för system poddar som är viktiga för kluster hälsan. Det minsta värdet som kan ställas in för maximalt poddar per nod är 10 om och bara om konfigurationen för varje nod har utrymme för minst 30 poddar. Om du till exempel ställer in den maximala poddar per nod till minst 10 måste varje enskild nod ha minst tre noder. Detta krav gäller för varje ny nod som skapas också, så om 10 definieras som maximalt poddar per nod måste varje efterföljande nod som läggs till ha minst 3 noder.

| Nätverk | Minimum | Maximal |
| -- | :--: | :--: |
| Azure-CNI | 10 | 250 |
| Kubernetes | 10 | 110 |

> [!NOTE]
> Det minimala värdet i tabellen ovan tillämpas helt av AKS-tjänsten. Du kan inte ange ett maxPods-värde som är lägre än det minsta antalet som visas på så sätt kan förhindra att klustret startar.

* **Azure CLI**: ange `--max-pods` argumentet när du distribuerar ett kluster med kommandot [AZ AKS Create][az-aks-create] . Det maximala värdet är 250.
* **Resource Manager-mall**: ange `maxPods` egenskapen i [ManagedClusterAgentPoolProfile] -objektet när du distribuerar ett kluster med en Resource Manager-mall. Det maximala värdet är 250.
* **Azure Portal**: du kan inte ändra det maximala antalet poddar per nod när du distribuerar ett kluster med Azure Portal. Azure CNI nätverks kluster är begränsade till 30 poddar per nod när du distribuerar med hjälp av Azure Portal.

### <a name="configure-maximum---existing-clusters"></a>Konfigurera maximalt antal befintliga kluster

Inställningen maxPod per nod kan definieras när du skapar en ny Node-pool. Om du behöver öka inställningen för maxPod per nod i ett befintligt kluster lägger du till en ny nod med det nya önskade maxPod antalet. När du har migrerat poddar till den nya poolen tar du bort den äldre poolen. Om du vill ta bort en äldre pool i ett kluster måste du kontrol lera att du anger lägen för resurspool enligt definitionen i [dokumentet för pooler för system][system-node-pools].

## <a name="deployment-parameters"></a>Distributions parametrar

När du skapar ett AKS-kluster kan följande parametrar konfigureras för Azure CNI-nätverk:

**Virtuellt nätverk**: det virtuella nätverk som du vill distribuera Kubernetes-klustret till. Om du vill skapa ett nytt virtuellt nätverk för klustret väljer du *Skapa nytt* och följer stegen i avsnittet *Skapa virtuellt nätverk* . Information om gränser och kvoter för ett virtuellt Azure-nätverk finns i [Azure-prenumerationer, tjänst gränser, kvoter och begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).

**Undernät**: under nätet i det virtuella nätverket där du vill distribuera klustret. Om du vill skapa ett nytt undernät i det virtuella nätverket för klustret väljer du *Skapa nytt* och följer stegen i avsnittet *skapa undernät* . För Hybrid anslutning bör adress intervallet inte överlappa några andra virtuella nätverk i din miljö.

**Kubernetes-tjänstens adress intervall**: Detta är den uppsättning virtuella IP-adresser som Kubernetes tilldelar interna [tjänster][services] i klustret. Du kan använda ett privat adress intervall som uppfyller följande krav:

* Får inte ligga inom det virtuella nätverkets IP-adressintervall för klustret
* Får inte överlappa några andra virtuella nätverk som klustrets virtuella nätverk peer-kopplas till
* Får inte överlappa alla lokala IP-adresser
* Får inte ligga inom intervallet `169.254.0.0/16` ,, `172.30.0.0/16` `172.31.0.0/16` eller `192.0.2.0/24`

Även om det är tekniskt möjligt att ange ett tjänst adress intervall inom samma virtuella nätverk som klustret, rekommenderas det inte. Oförutsägbart beteende kan uppstå om överlappande IP-intervall används. Mer information finns i avsnittet [vanliga frågor och svar](#frequently-asked-questions) i den här artikeln. Mer information om Kubernetes-tjänster finns i [tjänster][services] i Kubernetes-dokumentationen.

**IP-adress för KUBERNETES DNS-tjänst**: IP-adressen för KLUSTRETs DNS-tjänst. Den här adressen måste ligga inom *Kubernetes Service-adressintervallet*. Använd inte den första IP-adressen i adress intervallet, till exempel. 1. Den första adressen i under nätets intervall används för *Kubernetes. default. svc. Cluster. local* .

**Docker-bryggans adress**: Docker-bryggans nätverks adress representerar den standard nätverks adress för *docker0* -brygga som finns i alla Docker-installationer. Även om *docker0* -bryggan inte används av AKS-kluster eller själva poddar, måste du ange den här adressen så att den fortfarande stöder scenarier som *Docker-build* i AKS-klustret. Det krävs att du väljer en CIDR för Docker-bryggans nätverks adress eftersom en annan Docker kommer att välja ett undernät automatiskt som kan hamna i konflikt med andra CIDR-enheter. Du måste välja ett adress utrymme som inte kolliderar med resten av CIDR-inställningarna i dina nätverk, inklusive klustrets tjänst-CIDR och Pod CIDR.

## <a name="configure-networking---cli"></a>Konfigurera nätverk – CLI

När du skapar ett AKS-kluster med Azure CLI kan du också konfigurera Azure CNI-nätverk. Använd följande kommandon för att skapa ett nytt AKS-kluster med Azure CNI Networking aktiverat.

Börja med att hämta under nätets resurs-ID för det befintliga under nätet där AKS-klustret ska anslutas:

```azurecli-interactive
$ az network vnet subnet list \
    --resource-group myVnet \
    --vnet-name myVnet \
    --query "[0].id" --output tsv

/subscriptions/<guid>/resourceGroups/myVnet/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/default
```

Använd kommandot [AZ AKS Create][az-aks-create] med `--network-plugin azure` argumentet för att skapa ett kluster med avancerade nätverk. Uppdatera `--vnet-subnet-id` värdet med det Undernäts-ID som samlades in i föregående steg:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 \
    --generate-ssh-keys
```

## <a name="configure-networking---portal"></a>Konfigurera nätverk – Portal

Följande skärm bild från Azure Portal visar ett exempel på hur du konfigurerar dessa inställningar när du skapar AKS-kluster:

![Avancerad nätverks konfiguration i Azure Portal][portal-01-networking-advanced]

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

Följande frågor och svar gäller nätverks konfigurationen för **Azure cni** .

* *Kan jag distribuera virtuella datorer i mitt kluster under nät?*

  Ja.

* *Vilken käll-IP-adress ser externa system för trafik som kommer från en Azure CNI-aktiverad Pod?*

  System i samma virtuella nätverk som AKS-klustret ser Pod IP som käll adress för all trafik från pod. System utanför AKS-klustrets virtuella nätverk ser nodens IP-adress som käll adress för all trafik från pod. 

* *Kan jag konfigurera principer per POD nätverk?*

  Ja, Kubernetes nätverks princip är tillgänglig i AKS. Kom igång genom att se [säker trafik mellan poddar med hjälp av nätverks principer i AKS][network-policy].

* *Är det maximala antalet poddar som kan distribueras till en nod som kan konfigureras?*

  Ja, när du distribuerar ett kluster med Azure CLI eller en Resource Manager-mall. Se [maximalt antal poddar per nod](#maximum-pods-per-node).

  Du kan inte ändra det maximala antalet poddar per nod i ett befintligt kluster.

* *Hur gör jag för att konfigurera ytterligare egenskaper för under nätet som jag skapade när jag skapade AKS-kluster? Till exempel tjänst slut punkter.*

  Den fullständiga listan över egenskaper för det virtuella nätverk och undernät som du skapar när du skapar AKS-kluster kan konfigureras på sidan standard konfiguration av virtuellt nätverk i Azure Portal.

* *Kan jag använda ett annat undernät i mitt virtuella kluster nätverk för* **Kubernetes-tjänstens adress intervall**?

  Det rekommenderas inte, men den här konfigurationen är möjlig. Tjänst adress intervallet är en uppsättning virtuella IP-adresser (VIP) som Kubernetes tilldelar interna tjänster i klustret. Azure-nätverk har ingen insyn i tjänstens IP-intervall för Kubernetes-klustret. På grund av brist på insyn i klustrets tjänst adress intervall, är det möjligt att senare skapa ett nytt undernät i det virtuella kluster nätverket som överlappar tjänst adress intervallet. Om ett sådant överlappande inträffar kan Kubernetes tilldela en tjänst en IP-adress som redan används av en annan resurs i under nätet, vilket orsakar oförutsägbara beteenden eller fel. Genom att se till att du använder ett adress intervall utanför klustrets virtuella nätverk kan du undvika överlappande risk.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om nätverk i AKS i följande artiklar:

- [Använd en statisk IP-adress med AKS-belastningsutjämnaren (Azure Kubernetes service)](static-ip.md)
- [Använda en intern belastningsutjämnare med Azure Container Service (AKS)](internal-lb.md)

- [Skapa en grundläggande ingångs kontroll med extern nätverks anslutning][aks-ingress-basic]
- [Aktivera routnings tillägget för HTTP-program][aks-http-app-routing]
- [Skapa en ingångs kontroll enhet som använder ett internt, privat nätverk och IP-adress][aks-ingress-internal]
- [Skapa en ingångs kontroll enhet med en dynamisk offentlig IP-adress och konfigurera låt oss kryptera för att automatiskt generera TLS-certifikat][aks-ingress-tls]
- [Skapa en ingångs kontroll enhet med en statisk offentlig IP-adress och konfigurera låt oss kryptera för att automatiskt generera TLS-certifikat][aks-ingress-static-tls]

### <a name="aks-engine"></a>AKS-motor

[Azure Kubernetes service Engine (AKS Engine)][aks-engine] är ett projekt med öppen källkod som genererar Azure Resource Manager mallar som du kan använda för att distribuera Kubernetes-kluster på Azure.

Kubernetes-kluster som skapats med AKS-motorn stöder både [Kubernetes][kubenet] -och [Azure cni][cni-networking] -plugin-program. Därför stöds båda nätverks scenarierna av AKS-motorn.

<!-- IMAGES -->
[advanced-networking-diagram-01]: ./media/networking-overview/advanced-networking-diagram-01.png
[portal-01-networking-advanced]: ./media/networking-overview/portal-01-networking-advanced.png

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[services]: https://kubernetes.io/docs/concepts/services-networking/service/
[portal]: https://portal.azure.com
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[aks-ssh]: ssh.md
[ManagedClusterAgentPoolProfile]: /azure/templates/microsoft.containerservice/managedclusters#managedclusteragentpoolprofile-object
[aks-network-concepts]: concepts-network.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-internal]: ingress-internal-ip.md
[network-policy]: use-network-policies.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[network-comparisons]: concepts-network.md#compare-network-models
[system-node-pools]: use-system-pools.md
