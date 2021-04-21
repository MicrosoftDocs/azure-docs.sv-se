---
title: Konfigurera Azure CNI nätverk i Azure Kubernetes Service (AKS)
description: Lär dig hur du konfigurerar Azure CNI (avancerat) nätverk i Azure Kubernetes Service (AKS), inklusive distribution av ett AKS-kluster till ett befintligt virtuellt nätverk och undernät.
services: container-service
ms.topic: article
ms.date: 06/03/2019
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 839aa012cedaaa6f5bd3d1edad60e3ea7278133b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775897"
---
# <a name="configure-azure-cni-networking-in-azure-kubernetes-service-aks"></a>Konfigurera Azure CNI nätverk i Azure Kubernetes Service (AKS)

Som standard använder AKS-kluster [kubenet][kubenet], och ett virtuellt nätverk och undernät skapas åt dig. Med *kubenet* hämtar noder en IP-adress från ett virtuellt nätverksundernät. Nat (Network Address Translation) konfigureras sedan på noderna och poddar får en IP-adress som är "dold" bakom nod-IP-adressen. Den här metoden minskar antalet IP-adresser som du behöver reservera i ditt nätverksutrymme så att poddar kan använda dem.

Med [Azure Container Networking Interface (CNI)][cni-networking]får varje podd en IP-adress från undernätet och kan nås direkt. Dessa IP-adresser måste vara unika i nätverket och måste planeras i förväg. Varje nod har en konfigurationsparameter för det maximala antalet poddar som stöds. Motsvarande antal IP-adresser per nod reserveras sedan direkt för den noden. Den här metoden kräver mer planering och leder ofta till att IP-adresserna tar slut eller att kluster måste återskapas i ett större undernät när dina programbehov växer.

Den här artikeln visar hur du använder *Azure CNI* för att skapa och använda ett undernät för ett virtuellt nätverk för ett AKS-kluster. Mer information om nätverksalternativ och nätverksöverväganden finns [i Nätverksbegrepp för Kubernetes och AKS.][aks-network-concepts]

## <a name="prerequisites"></a>Förutsättningar

* Det virtuella nätverket för AKS-klustret måste tillåta utgående Internetanslutning.
* AKS-kluster får inte använda , , eller för `169.254.0.0/16` `172.30.0.0/16` `172.31.0.0/16` `192.0.2.0/24` Kubernetes-tjänstens adressintervall, poddadressintervall eller adressintervall för virtuellt klusternätverk.
* Klusteridentiteten som används av AKS-klustret måste ha minst behörighet [som nätverksdeltagare](../role-based-access-control/built-in-roles.md#network-contributor) på undernätet i det virtuella nätverket. Om du vill definiera en [anpassad roll i](../role-based-access-control/custom-roles.md) stället för att använda den inbyggda rollen Nätverksdeltagare krävs följande behörigheter:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`
* Undernätet som tilldelats till AKS-nodpoolen kan inte vara ett [delegerat undernät.](../virtual-network/subnet-delegation-overview.md)

## <a name="plan-ip-addressing-for-your-cluster"></a>Planera IP-adressering för klustret

Kluster som konfigurerats med Azure CNI nätverk kräver ytterligare planering. Storleken på det virtuella nätverket och dess undernät måste hantera det antal poddar som du planerar att köra och antalet noder för klustret.

IP-adresser för poddarna och klustrets noder tilldelas från det angivna undernätet i det virtuella nätverket. Varje nod konfigureras med en primär IP-adress. Som standard förkonfigureras 30 ytterligare IP-adresser av Azure CNI som tilldelas till poddar som schemaläggs på noden. När du skalar ut klustret konfigureras varje nod på samma sätt med IP-adresser från undernätet. Du kan också visa det [maximala antalet poddar per nod.](#maximum-pods-per-node)

> [!IMPORTANT]
> Antalet IP-adresser som krävs bör innehålla överväganden för uppgraderings- och skalningsåtgärder. Om du ställer in IP-adressintervallet så att det endast stöder ett fast antal noder kan du inte uppgradera eller skala klustret.
>
> * När du **uppgraderar** ditt AKS-kluster distribueras en ny nod till klustret. Tjänster och arbetsbelastningar börjar köras på den nya noden och en äldre nod tas bort från klustret. Den här löpande uppgraderingsprocessen kräver att minst ett ytterligare block med IP-adresser är tillgängligt. Antalet noder är sedan `n + 1` .
>   * Detta är särskilt viktigt när du använder Windows Server-nodpooler. Windows Server-noder i AKS tillämpar inte Windows-uppdateringar automatiskt, utan i stället utför du en uppgradering på nodpoolen. Den här uppgraderingen distribuerar nya noder med den senaste Windows Server 2019-basnodavbildningen och säkerhetskorrigeringarna. Mer information om hur du uppgraderar en Windows Server-nodpool finns [i Uppgradera en nodpool i AKS.][nodepool-upgrade]
>
> * När du **skalar** ett AKS-kluster distribueras en ny nod till klustret. Tjänster och arbetsbelastningar börjar köras på den nya noden. Ip-adressintervallet måste ta hänsyn till hur du kan skala upp antalet noder och poddar som klustret har stöd för. En ytterligare nod för uppgraderingsåtgärder bör också inkluderas. Antalet noder är sedan `n + number-of-additional-scaled-nodes-you-anticipate + 1` .

Om du förväntar dig att noderna kör det maximala antalet poddar och regelbundet förstör och distribuerar poddar bör du även ta med ytterligare IP-adresser per nod i faktorer. Dessa ytterligare IP-adresser tar hänsyn till det kan ta några sekunder innan en tjänst tas bort och IP-adressen som släpps för att en ny tjänst ska distribueras och hämta adressen.

IP-adressplanen för ett AKS-kluster består av ett virtuellt nätverk, minst ett undernät för noder och poddar samt ett Kubernetes-tjänstadressintervall.

| Adressintervall/Azure-resurs | Gränser och storlek |
| --------- | ------------- |
| Virtuellt nätverk | Det virtuella Azure-nätverket kan vara så stort som /8, men det är begränsat till 65 536 konfigurerade IP-adresser. Överväg alla nätverksbehov, inklusive kommunikation med tjänster i andra virtuella nätverk, innan du konfigurerar adressutrymmet. Om du till exempel konfigurerar för stort adressutrymme kan du få problem med överlappande andra adressutrymmen i nätverket.|
| Undernät | Måste vara tillräckligt stort för de noder, poddar och alla Kubernetes- och Azure-resurser som kan etableras i klustret. Om du till exempel distribuerar en intern Azure Load Balancer allokeras dess IP-adresser på serversidan från klustrets undernät, inte offentliga IP-adresser. Undernätets storlek bör också ta hänsyn till uppgraderingsåtgärder eller framtida skalningsbehov.<p />Så här beräknar *du den minsta* undernätsstorleken, inklusive ytterligare en nod för uppgraderingsåtgärder: `(number of nodes + 1) + ((number of nodes + 1) * maximum pods per node that you configure)`<p/>Exempel för ett kluster med 50 noder: `(51) + (51  * 30 (default)) = 1,581` (/21 eller större)<p/>Exempel för ett 50-nodkluster som även innehåller etablering för att skala upp ytterligare 10 noder: `(61) + (61 * 30 (default)) = 1,891` (/21 eller större)<p>Om du inte anger ett maximalt antal poddar per nod när du skapar klustret anges det maximala antalet poddar per nod till *30*. Det minsta antalet IP-adresser som krävs baseras på det värdet. Om du beräknar dina minsta IP-adresskrav för ett annat maxvärde kan du se hur du konfigurerar det maximala antalet [poddar per](#configure-maximum---new-clusters) nod för att ange det här värdet när du distribuerar klustret. |
| Kubernetes Service-adressintervall | Det här intervallet ska inte användas av något nätverkselement på eller vara anslutet till det här virtuella nätverket. Tjänstadress-CIDR måste vara mindre än /12. Du kan återanvända det här intervallet i olika AKS-kluster. |
| IP-adress för Kubernetes DNS-tjänst | IP-adress inom Kubernetes-tjänstens adressintervall som ska användas av identifiering av klustertjänst. Använd inte den första IP-adressen i adressintervallet, till exempel .1. Den första adressen i undernätsintervallet används för *adressen kubernetes.default.svc.cluster.local.* |
| Docker-bryggadress | Docker-bryggnätverksadressen representerar den standardmässiga *docker0*-bryggnätverksadress som finns i alla Docker-installationer. Även *om docker0-bryggan* inte används av AKS-kluster eller själva poddarna, måste du ange den här adressen för att fortsätta att stödja scenarier som *docker build* i AKS-klustret. Du måste välja en CIDR för Docker-bryggnätverksadressen eftersom Docker annars väljer ett undernät automatiskt, vilket kan vara i konflikt med andra CIDR. Du måste välja ett adressutrymme som inte krockar med resten av CIDR i dina nätverk, inklusive klustrets tjänst-CIDR och podd-CIDR. Standardvärdet är 172.17.0.1/16. Du kan återanvända det här intervallet i olika AKS-kluster. |

## <a name="maximum-pods-per-node"></a>Maximalt antal poddar per nod

Det maximala antalet poddar per nod i ett AKS-kluster är 250. Det *maximala* standardantalet poddar per nod varierar mellan *kubenet* *Azure CNI* nätverk och metoden för klusterdistribution.

| Distributionsmetod | Kubenet-standard | Azure CNI standard | Kan konfigureras vid distribution |
| -- | :--: | :--: | -- |
| Azure CLI | 110 | 30 | Ja (upp till 250) |
| Resource Manager-mall | 110 | 30 | Ja (upp till 250) |
| Portalen | 110 | 110 (konfigureras på fliken Nodpooler) | No |

### <a name="configure-maximum---new-clusters"></a>Konfigurera maximalt – nya kluster

Du kan konfigurera det maximala antalet poddar per nod vid klusterdistributionen eller när du lägger till nya nodpooler. Om du distribuerar med Azure CLI eller med en Resource Manager-mall kan du ange det högsta antalet poddar per nodvärde så högt som 250.

Om du inte anger maxPods när du skapar nya nodpooler får du standardvärdet 30 för Azure CNI.

Ett minsta värde för maximalt antal poddar per nod tillämpas för att garantera utrymme för systempoddar som är viktiga för klustrets hälsa. Det minsta värde som kan anges för maximalt antal poddar per nod är 10 om och endast om konfigurationen för varje nodpool har utrymme för minst 30 poddar. Om du till exempel ställer in det högsta antalet poddar per nod till minst 10 måste varje enskild nodpool ha minst 3 noder. Det här kravet gäller även för varje ny nodpool som skapas, så om 10 definieras som maximalt antal poddar per nod måste varje efterföljande nodpool som läggs till ha minst 3 noder.

| Nätverk | Minimum | Maximal |
| -- | :--: | :--: |
| Azure CNI | 10 | 250 |
| Kubenet | 10 | 110 |

> [!NOTE]
> Det lägsta värdet i tabellen ovan tillämpas strikt av AKS-tjänsten. Du kan inte ange ett maxPods-värde som är lägre än det minsta som visas eftersom det kan förhindra att klustret startar.

* **Azure CLI:** Ange `--max-pods` argumentet när du distribuerar ett kluster med kommandot [az aks create.][az-aks-create] Det maximala värdet är 250.
* **Resource Manager:** Ange egenskapen i `maxPods` objektet [ManagedClusterAgentPoolProfile] när du distribuerar ett kluster med en Resource Manager mall. Det maximala värdet är 250.
* **Azure Portal:** Du kan inte ändra det maximala antalet poddar per nod när du distribuerar ett kluster med Azure Portal. Azure CNI nätverkskluster är begränsade till 30 poddar per nod när du distribuerar med Azure Portal.

### <a name="configure-maximum---existing-clusters"></a>Konfigurera maximalt – befintliga kluster

Inställningen maxPod per nod kan definieras när du skapar en ny nodpool. Om du behöver öka inställningen maxPod per nod i ett befintligt kluster lägger du till en ny nodpool med det nya önskade maxPod-antalet. När du har migrerat dina poddar till den nya poolen tar du bort den äldre poolen. Om du vill ta bort en äldre pool i ett kluster måste du kontrollera att du anger nodpoollägen enligt definitionen i dokumentet [systemnodpooler.][system-node-pools]

## <a name="deployment-parameters"></a>Distributionsparametrar

När du skapar ett AKS-kluster kan följande parametrar konfigureras för Azure CNI nätverk:

**Virtuellt** nätverk: Det virtuella nätverk som du vill distribuera Kubernetes-klustret till. Om du vill skapa ett nytt virtuellt nätverk för klustret väljer du *Skapa nytt* och följer stegen i avsnittet *Skapa virtuellt* nätverk. Information om gränser och kvoter för ett virtuellt Azure-nätverk finns i [Azure-prenumeration och tjänstbegränsningar, kvoter och begränsningar.](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits)

**Undernät:** Undernätet i det virtuella nätverket där du vill distribuera klustret. Om du vill skapa ett nytt undernät i det virtuella nätverket för klustret väljer du *Skapa nytt* och följer stegen i avsnittet *Skapa undernät.* För hybridanslutningar får adressintervallet inte överlappa med andra virtuella nätverk i din miljö.

**Plugin-program** för Azure-nätverk: När plugin-programmet för Azure-nätverk används kan den interna LoadBalancer-tjänsten med "externalTrafficPolicy=Local" inte nås från virtuella datorer med en IP-adress i clusterCIDR som inte tillhör AKS-kluster.

**Kubernetes-tjänstens adressintervall:** Den här parametern är den uppsättning virtuella IP-adresser som Kubernetes tilldelar till [interna tjänster][services] i klustret. Du kan använda alla privata adressintervall som uppfyller följande krav:

* Får inte vara inom det virtuella nätverkets IP-adressintervall för klustret
* Får inte överlappa med andra virtuella nätverk som klustrets virtuella nätverk är peer-datorer med
* Får inte överlappa med några lokala IP-adresser
* Får inte vara inom `169.254.0.0/16` intervallen , `172.30.0.0/16` , `172.31.0.0/16` eller `192.0.2.0/24`

Även om det är tekniskt möjligt att ange ett adressintervall för tjänsten i samma virtuella nätverk som klustret, rekommenderas det inte. Oförutsägbart beteende kan uppstå om överlappande IP-intervall används. Mer information finns i avsnittet vanliga [frågor och](#frequently-asked-questions) svar i den här artikeln. Mer information om Kubernetes-tjänster finns [i Tjänster][services] i Kubernetes-dokumentationen.

**Kubernetes DNS-tjänstens** IP-adress: IP-adressen för klustrets DNS-tjänst. Den här adressen måste ligga inom *Kubernetes Service-adressintervallet*. Använd inte den första IP-adressen i adressintervallet, till exempel .1. Den första adressen i undernätsintervallet används för *adressen kubernetes.default.svc.cluster.local.*

**Docker-bryggadress:** Docker-bryggnätverksadressen representerar standardadressen för *docker0-bryggnätverk* som finns i alla Docker-installationer. Även *om docker0-bryggan* inte används av AKS-kluster eller själva poddarna måste du ange den här adressen för att fortsätta att stödja scenarier som *docker build* i AKS-klustret. Du måste välja en CIDR för Docker-bryggnätverksadressen eftersom Annars väljer Docker automatiskt ett undernät som kan vara i konflikt med andra CIDR. Du måste välja ett adressutrymme som inte krockar med resten av CIDR i dina nätverk, inklusive klustrets tjänst-CIDR och podd-CIDR.

## <a name="configure-networking---cli"></a>Konfigurera nätverk – CLI

När du skapar ett AKS-kluster med Azure CLI kan du även konfigurera Azure CNI nätverk. Använd följande kommandon för att skapa ett nytt AKS-kluster med Azure CNI nätverk aktiverat.

Hämta först undernätets resurs-ID för det befintliga undernät som AKS-klustret ska vara ansluten till:

```azurecli-interactive
$ az network vnet subnet list \
    --resource-group myVnet \
    --vnet-name myVnet \
    --query "[0].id" --output tsv

/subscriptions/<guid>/resourceGroups/myVnet/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/default
```

Använd kommandot [az aks create][az-aks-create] med argumentet `--network-plugin azure` för att skapa ett kluster med avancerade nätverk. Uppdatera värdet `--vnet-subnet-id` med det undernäts-ID som samlades in i föregående steg:

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

## <a name="configure-networking---portal"></a>Konfigurera nätverk – portalen

Följande skärmbild från Azure Portal visar ett exempel på hur du konfigurerar de här inställningarna när AKS-klustret skapas:

! [Avancerad nätverkskonfiguration i Azure Portal] [portal-01-networking-advanced]

## <a name="dynamic-allocation-of-ips-and-enhanced-subnet-support-preview"></a>Dynamisk allokering av IP-adresser och förbättrat stöd för undernät (förhandsversion)

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

> [!NOTE] 
> Den här förhandsgranskningsfunktionen är för närvarande tillgänglig i följande regioner:
>
> * USA, västra centrala

En nackdel med traditionella CNI är att podd-IP-adresserna tar slut när AKS-klustret växer, vilket gör att hela klustret måste återskapas i ett större undernät. Den nya funktionen för dynamisk IP-allokering i Azure CNI löser det här problemet genom att tilldela podd-IP-adresser från ett undernät som är separat från det undernät som är värd för AKS-klustret.  Det ger följande fördelar:

* **Bättre IP-användning:** IP-adresser allokeras dynamiskt till klusterpoddar från poddundernätet. Detta leder till bättre användning av IP-adresser i klustret jämfört med den traditionella CNI-lösningen, som gör statisk allokering av IP-adresser för varje nod.  

* **Skalbart och flexibelt:** Nod- och poddundernät kan skalas oberoende av varandra. Ett enda poddundernät kan delas mellan flera nodpooler i ett kluster eller över flera AKS-kluster som distribueras i samma virtuella nätverk. Du kan också konfigurera ett separat poddundernät för en nodpool.  

* **Höga prestanda:** Eftersom podden tilldelas VNet-IP-adresser har de direkt anslutning till andra klusterpoddar och resurser i det virtuella nätverket. Lösningen stöder mycket stora kluster utan prestandaförsämring.

* **Separata VNet-principer för poddar:** Eftersom poddar har ett separat undernät kan du konfigurera separata VNet-principer för dem som skiljer sig från nodprinciper. Detta möjliggör många användbara scenarier, till exempel att endast tillåta Internetanslutning för poddar och inte för noder, åtgärda käll-IP för podd i en nodpool med hjälp av ett VNet-nätverks-NAT och använda NSG:er för att filtrera trafik mellan nodpooler.  

* **Kubernetes-nätverksprinciper:** Både Azure-nätverksprinciperna ochNdeco fungerar med den här nya lösningen.  

### <a name="install-the-aks-preview-azure-cli"></a>Installera `aks-preview` Azure CLI

Du behöver Azure *CLI-tillägget aks-preview.* Installera Azure *CLI-tillägget aks-preview* med hjälp av [kommandot az extension add.][az-extension-add] Eller installera eventuella tillgängliga uppdateringar med hjälp av [kommandot az extension update.][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-the-podsubnetpreview-preview-feature"></a>Registrera `PodSubnetPreview` förhandsgranskningsfunktionen

Om du vill använda funktionen måste du också aktivera `PodSubnetPreview` funktionsflaggan för din prenumeration.

Registrera `PodSubnetPreview` funktionsflaggan med [kommandot az feature register,][az-feature-register] som du ser i följande exempel:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "PodSubnetPreview"
```

Det tar några minuter för statusen att visa *Registrerad*. Kontrollera registreringsstatusen med hjälp av [kommandot az feature list:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSubnetPreview')].{Name:name,State:properties.state}"
```

När du är klar uppdaterar du registreringen av *resursprovidern Microsoft.ContainerService* med kommandot [az provider register:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="additional-prerequisites"></a>Ytterligare krav

Kraven som redan anges för Azure CNI gäller fortfarande, men det finns några ytterligare begränsningar:

* Endast Linux-nodkluster och nodpooler stöds.
* AKS Engine- och GÖR-kluster stöds inte.

### <a name="planning-ip-addressing"></a>Planera IP-adressering

När du använder den här funktionen är planeringen mycket enklare. Eftersom noderna och poddarna skalas oberoende av varandra kan deras adressutrymmen också planeras separat. Eftersom poddundernät kan konfigureras till kornigheten för en nodpool kan kunder alltid lägga till ett nytt undernät när de lägger till en nodpool. Systempoddarna i en kluster-/nodpool tar även emot IP-adresser från poddundernätet, så det här beteendet måste redovisas.

Planeringen av IP-adresser för K8S-tjänster och Docker-bryggan förblir oförändrad.

### <a name="maximum-pods-per-node-in-a-cluster-with-dynamic-allocation-of-ips-and-enhanced-subnet-support"></a>Maximalt antal poddar per nod i ett kluster med dynamisk allokering av IP-adresser och förbättrat stöd för undernät

Poddarna per nodvärden när du använder Azure CNI med dynamisk allokering av IP-adresser har ändrats något från det traditionella CNI-beteendet:

|Cni|Distributionsmetod|Standardvärde|Kan konfigureras vid distribution|
|--|--| :--: |--|
|Traditionella Azure CNI|Azure CLI|30|Ja (upp till 250)|
|Azure CNI med dynamisk allokering av IP-adresser|Azure CLI|250|Ja (upp till 250)|

All annan vägledning som rör konfiguration av maximalt antal noder per podd förblir densamma.

### <a name="additional-deployment-parameters"></a>Ytterligare distributionsparametrar

Distributionsparametrarna som beskrivs ovan är fortfarande giltiga, med ett undantag:

* **Undernätsparametern** refererar nu till det undernät som är relaterat till klustrets noder.
* Ytterligare ett **parameter-poddundernät** används för att ange det undernät vars IP-adresser ska allokeras dynamiskt till poddar.

### <a name="configure-networking---cli-with-dynamic-allocation-of-ips-and-enhanced-subnet-support"></a>Konfigurera nätverk – CLI med dynamisk allokering av IP-adresser och förbättrat stöd för undernät

Användningen av dynamisk allokering av IP-adresser och förbättrat stöd för undernät i klustret liknar standardmetoden för att konfigurera ett kluster Azure CNI. I följande exempel går vi igenom hur du skapar ett nytt virtuellt nätverk med ett undernät för noder och ett undernät för poddar och skapar ett kluster som använder Azure CNI med dynamisk allokering av IP-adresser och förbättrat stöd för undernät. Se till att ersätta variabler som med `$subscription` dina egna värden:

Skapa först det virtuella nätverket med två undernät:

```azurecli-interactive
$resourceGroup="myResourceGroup"
$vnet="myVirtualNetwork"

# Create our two subnet network 
az network vnet create -g $rg --name $vnet --address-prefixes 10.0.0.0/8 -o none 
az network vnet subnet create -g $rg --vnet-name $vnet --name nodesubnet --address-prefixes 10.240.0.0/16 -o none 
az network vnet subnet create -g $rg --vnet-name $vnet --name podsubnet --address-prefixes 10.241.0.0/16 -o none 
```

Skapa sedan klustret och referera till nodundernätet med hjälp av `--vnet-subnet-id` och poddundernätet med hjälp av `--pod-subnet-id` :

```azurecli-interactive
$clusterName="myAKSCluster"
$location="eastus"
$subscription="aaaaaaa-aaaaa-aaaaaa-aaaa"

az aks create -n $clusterName -g $resourceGroup -l $location --max-pods 250 --node-count 2 --network-plugin azure --vnet-subnet-id /subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.Network/virtualNetworks/$vnet/subnets/nodesubnet --pod-subnet-id /subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.Network/virtualNetworks/$vnet/subnets/podsubnet  
```

#### <a name="adding-node-pool"></a>Lägga till nodpool

När du lägger till nodpoolen refererar du till nodundernätet `--vnet-subnet-id` med och poddundernätet med `--pod-subnet-id` . I följande exempel skapas två nya undernät som sedan refereras till när en ny nodpool skapas:

```azurecli-interactive
az network vnet subnet create -g $resourceGroup --vnet-name $vnet --name node2subnet --address-prefixes 10.242.0.0/16 -o none 
az network vnet subnet create -g $resourceGroup --vnet-name $vnet --name pod2subnet --address-prefixes 10.243.0.0/16 -o none 

az aks nodepool add --cluster-name $clusterName -g $resourceGroup  -n newNodepool --max-pods 250 --node-count 2 --vnet-subnet-id /subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.Network/virtualNetworks/$vnet/subnets/node2subnet  --pod-subnet-id /subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.Network/virtualNetworks/$vnet/subnets/pod2subnet --no-wait 
```

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

Följande frågor och svar gäller för **Azure CNI** nätverkskonfigurationen.

* *Kan jag distribuera virtuella datorer i mitt klusterundernät?*

  Ja.

* *Vilken käll-IP ser externa system för trafik som kommer från en Azure CNI-aktiverad podd?*

  System i samma virtuella nätverk som AKS-klustret ser podd-IP-adressen som källadress för all trafik från podden. System utanför det virtuella AKS-klustrets nätverk ser nod-IP som källadress för all trafik från podden.

* *Kan jag konfigurera nätverksprinciper per podd?*

  Ja, Kubernetes-nätverksprincipen är tillgänglig i AKS. Information om hur du kommer igång [finns i Skydda trafik mellan poddar med hjälp av nätverksprinciper i AKS.][network-policy]

* *Går det att konfigurera det maximala antalet poddar till en nod?*

  Ja, när du distribuerar ett kluster med Azure CLI eller en Resource Manager mall. Se [Maximalt antal poddar per nod.](#maximum-pods-per-node)

  Du kan inte ändra det maximala antalet poddar per nod i ett befintligt kluster.

* *Hur gör jag för att konfigurera ytterligare egenskaper för det undernät som jag skapade när AKS-klustret skapades? Till exempel tjänstslutpunkter.*

  Den fullständiga listan med egenskaper för det virtuella nätverket och undernäten som du skapar när AKS-klustret skapas kan konfigureras på standardsidan för konfiguration av virtuella nätverk i Azure Portal.

* *Kan jag använda ett annat undernät i mitt virtuella klusternätverk för* **Kubernetes-tjänstens adressintervall?**

  Det rekommenderas inte, men den här konfigurationen är möjlig. Tjänstadressintervallet är en uppsättning virtuella IP-adresser (VIP) som Kubernetes tilldelar till interna tjänster i klustret. Azure-nätverkstjänster har ingen insyn i Kubernetes-klustrets tjänst-IP-intervall. På grund av bristen på insyn i klustrets tjänstadressintervall kan du senare skapa ett nytt undernät i klustrets virtuella nätverk som överlappar tjänstadressintervallet. Om en sådan överlappning inträffar kan Kubernetes tilldela en tjänst en IP-adress som redan används av en annan resurs i undernätet, vilket orsakar oförutsägbart beteende eller fel. Genom att se till att du använder ett adressintervall utanför klustrets virtuella nätverk kan du undvika den här överlappande risken.

### <a name="dynamic-allocation-of-ip-addresses-and-enhanced-subnet-support-faqs"></a>Vanliga frågor och svar om dynamiskt allokering av IP-adresser och förbättrat undernätsstöd

Följande frågor och svar gäller nätverkskonfigurationen för Azure CNI när du använder dynamisk allokering av **IP-adresser och förbättrat stöd för undernät.**

* *Kan jag tilldela flera poddundernät till ett kluster/en nodpool?*

  Endast ett undernät kan tilldelas till ett kluster eller en nodpool. Flera kluster eller nodpooler kan dock dela ett enda undernät.

* *Kan jag tilldela poddundernät från ett annat VNet helt och hållet?*

  Poddundernätet ska komma från samma virtuella nätverk som klustret.  

* *Kan vissa nodpooler i ett kluster använda den traditionella CNI:en medan andra använder den nya CNI:en?*

  Hela klustret bör endast använda en typ av CNI.

## <a name="aks-engine"></a>AKS-motor

[Azure Kubernetes Service Engine (AKS Engine)][aks-engine] är ett projekt med öppen källkod som genererar Azure Resource Manager-mallar som du kan använda för att distribuera Kubernetes-kluster i Azure.

Kubernetes-kluster som skapats med AKS Engine stöder både [kubenet-][kubenet] och [Azure CNI-plugin-program.][cni-networking] Därför stöds båda nätverksscenarierna av AKS-motorn.

## <a name="next-steps"></a>Nästa steg

Läs mer om nätverk i AKS i följande artiklar:

* [Använda en statisk IP-adress med Azure Kubernetes Service lastbalanserare (AKS)](static-ip.md)
* [Använda en intern lastbalanserare med Azure Container Service (AKS)](internal-lb.md)

* [Skapa en grundläggande ingressstyrenhet med extern nätverksanslutning][aks-ingress-basic]
* [Aktivera tillägget för HTTP-programroutning][aks-http-app-routing]
* [Skapa en ingress-kontrollant som använder ett internt, privat nätverk och en IP-adress][aks-ingress-internal]
* [Skapa en ingresskontrollant med en dynamisk offentlig IP-adress och konfigurera Let's Encrypt för att automatiskt generera TLS-certifikat][aks-ingress-tls]
* [Skapa en ingress-kontrollant med en statisk offentlig IP-adress och konfigurera Let's Encrypt för att automatiskt generera TLS-certifikat][aks-ingress-static-tls]
<!-- IMAGES -->
[advanced-networking-diagram-01]: ./media/networking-overview/advanced-networking-diagram-01.png [portal-01-networking-advanced]: ./media/networking-overview/portal-01-networking-advanced.png

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[services]: https://kubernetes.io/docs/concepts/services-networking/service/
[portal]: https://portal.azure.com
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[az-aks-create]: /cli/azure/aks#az_aks_create
[aks-ssh]: ssh.md
[ManagedClusterAgentPoolProfile]: /azure/templates/microsoft.containerservice/managedclusters#managedclusteragentpoolprofile-object
[aks-network-concepts]: concepts-network.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-internal]: ingress-internal-ip.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[network-policy]: use-network-policies.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[network-comparisons]: concepts-network.md#compare-network-models
[system-node-pools]: use-system-pools.md
