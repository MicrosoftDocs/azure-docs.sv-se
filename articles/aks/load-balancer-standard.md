---
title: Använda en offentlig Load Balancer
titleSuffix: Azure Kubernetes Service
description: Lär dig hur du använder en offentlig belastningsutjämnare med en standard-SKU för att exponera dina tjänster med Azure Kubernetes service (AKS).
services: container-service
ms.topic: article
ms.date: 11/14/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: e37c5a748a8e99f49e3535946268427139bbbf44
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102184431"
---
# <a name="use-a-public-standard-load-balancer-in-azure-kubernetes-service-aks"></a>Använda en offentlig Standard Load Balancer i Azure Kubernetes service (AKS)

Azure Load Balancer finns på L4 av OSI-modellen (Open Systems Interconnection) som stöder både inkommande och utgående scenarier. Den distribuerar inkommande flöden som anländer till belastningsutjämnarens klient del till instanserna för Server delens pool.

En **offentlig** Load Balancer när det är integrerat med AKS i två syfte:

1. För att tillhandahålla utgående anslutningar till klusternoderna i det virtuella AKS-nätverket. Den uppnår detta mål genom att översätta nodernas privata IP-adress till en offentlig IP-adress som ingår i den *utgående poolen*.
2. För att ge åtkomst till program via Kubernetes-tjänster av typen `LoadBalancer` . Med den kan du enkelt skala dina program och skapa tjänster med hög tillgänglighet.

En **intern (eller privat)** belastningsutjämnare används där endast privata IP-adresser tillåts som klient del. Interna belastnings utjämning används för att belastningsutjämna trafik i ett virtuellt nätverk. En belastningsutjämnare för belastnings utjämning kan också nås från ett lokalt nätverk i ett hybrid scenario.

Det här dokumentet omfattar integrering med offentlig belastningsutjämnare. Intern Load Balancer-integrering finns i [dokumentationen för intern belastningsutjämnare för AKS](internal-lb.md).

## <a name="before-you-begin"></a>Innan du börjar

Azure Load Balancer finns i två SKU: er – *Basic* och *standard*. *Standard* -SKU: n används som standard när du skapar ett AKS-kluster. Använd *standard* -SKU: n för att få åtkomst till ytterligare funktioner, till exempel en större backend-pool, [**flera noder**](use-multiple-node-pools.md)och [**Tillgänglighetszoner**](availability-zones.md). Det är den rekommenderade Load Balancer SKU: n för AKS.

Mer information om *Basic* -och *standard* -SKU: er finns i [jämförelse av Azure Load Balancer SKU][azure-lb-comparison].

Den här artikeln förutsätter att du har ett AKS-kluster med *standard* -SKU-Azure Load Balancer och går igenom hur du använder och konfigurerar några av funktionerna i belastningsutjämnaren. Om du behöver ett AKS-kluster kan du läsa snabb starten för AKS [med hjälp av Azure CLI][aks-quickstart-cli] eller [Azure Portal][aks-quickstart-portal].

> [!IMPORTANT]
> Om du föredrar att inte använda Azure Load Balancer för att tillhandahålla utgående anslutning och i stället ha en egen Gateway, brand vägg eller proxy för detta ändamål kan du hoppa över skapandet av den utgående poolen för belastningsutjämnaren och respektive klient dels-IP med hjälp av [**utgående typ som UserDefinedRouting (UDR)**](egress-outboundtype.md). Utgående typ definierar utgångs metoden för ett kluster och den är som standard typen: Load Balancer.

## <a name="use-the-public-standard-load-balancer"></a>Använd den offentliga standard belastnings utjämning

När du har skapat ett AKS-kluster med utgående typ: Load Balancer (standard) är klustret redo att använda belastnings utjämning för att exponera tjänster också.

För att du ska kunna skapa en offentlig tjänst av typen `LoadBalancer` som visas i följande exempel. Börja med att skapa ett tjänst manifest med namnet `public-svc.yaml` :

```yaml
apiVersion: v1
kind: Service
metadata:
  name: public-svc
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: public-app
```

Distribuera det offentliga tjänst manifestet med hjälp av [kubectl Apply][kubectl-apply] och ange namnet på ditt yaml-manifest:

```azurecli-interactive
kubectl apply -f public-svc.yaml
```

Azure Load Balancer konfigureras med en ny offentlig IP-adress som kommer fram till den här nya tjänsten. Eftersom Azure Load Balancer kan ha flera IP-adresser för klient delar kommer varje ny tjänst som distribueras att få en ny dedikerad IP-adress för klient delen som ska användas unikt.

Du kan kontrol lera att tjänsten har skapats och belastningsutjämnaren konfigureras genom att köra till exempel:

```azurecli-interactive
kubectl get service public-svc
```

```console
NAMESPACE     NAME          TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)         AGE
default       public-svc    LoadBalancer   10.0.39.110    52.156.88.187   80:32068/TCP    52s
```

När du visar tjänst informationen visas den offentliga IP-adressen som skapats för den här tjänsten på belastningsutjämnaren i kolumnen *extern IP-* adress. Det kan ta en minut eller två för IP-adressen att ändras från *\<pending\>* till en verklig offentlig IP-adress, som visas i exemplet ovan.

## <a name="configure-the-public-standard-load-balancer"></a>Konfigurera den offentliga standard belastnings utjämning

När du använder standard-SKU: s offentliga belastningsutjämnare finns det en uppsättning alternativ som kan anpassas när du skapar den, eller genom att uppdatera klustret. Med de här alternativen kan du anpassa Load Balancer efter dina arbets belastnings behov och bör granskas i enlighet med detta. Med standard belastnings utjämning kan du:

* Ange eller skala antalet hanterade utgående IP-adresser
* Ta med egna anpassade [IP-prefix för utgående IP eller utgående trafik](#provide-your-own-outbound-public-ips-or-prefixes)
* Anpassa antalet allokerade utgående portar till varje nod i klustret
* Konfigurera tids gräns inställningen för inaktiva anslutningar

> [!IMPORTANT]
> Endast ett utgående IP-alternativ (hanterade IP-adresser, ta med din egen IP-adress eller IP-prefix) kan användas vid en specifik tidpunkt.

### <a name="scale-the-number-of-managed-outbound-public-ips"></a>Skala antalet hanterade utgående offentliga IP-adresser

Azure Load Balancer tillhandahåller utgående anslutning från ett virtuellt nätverk utöver inkommande. Utgående regler gör det enkelt att konfigurera offentliga Standard Load Balancers utgående Network Address Translation.

Precis som alla Load Balancer regler följer utgående regler samma välkända syntax som belastnings utjämning och inkommande NAT-regler:

***klient del IP + parametrar + backend-pool***

En utgående regel konfigurerar utgående NAT för alla virtuella datorer som identifieras av backend-poolen som ska översättas till klient delen. Parametrarna och ger ytterligare detaljerad kontroll över den utgående NAT-algoritmen.

Även om en utgående regel kan användas med bara en enda offentlig IP-adress, underlättar utgående regler konfigurations bördan för skalning av utgående NAT. Du kan använda flera IP-adresser för att planera för storskaliga scenarier och du kan använda utgående regler för att minska mönstren för SNAT-överbelastningar. Varje ytterligare IP-adress som tillhandahållits av en frontend tillhandahåller 64 KB tillfälliga portar för Load Balancer som ska användas som SNAT-portar. 

När du använder en *standard* -SKU för belastningsutjämnare med hanterade utgående offentliga IP-adresser, som skapas som standard, kan du skala antalet hanterade utgående offentliga IP-adresser med hjälp av **`load-balancer-managed-ip-count`** parametern.

Kör följande kommando för att uppdatera ett befintligt kluster. Den här parametern kan också ställas in i klustret Create-Time för att ha flera hanterade utgående offentliga IP-adresser.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

Exemplet ovan anger antalet hanterade utgående offentliga IP-adresser till *2* för *myAKSCluster* -klustret i *myResourceGroup*. 

Du kan också använda- **`load-balancer-managed-ip-count`** parametern för att ange det första antalet hanterade, offentliga IP-adresser när du skapar klustret genom att lägga till **`--load-balancer-managed-outbound-ip-count`** parametern och ange önskat värde. Standardvärdet för hanterade utgående offentliga IP-adresser är 1.

### <a name="provide-your-own-outbound-public-ips-or-prefixes"></a>Ange egna utgående offentliga IP-adresser eller prefix

När du använder en *standard* -SKU-BELASTNINGSUTJÄMNARE skapar AKS-klustret automatiskt en offentlig IP-adress i resurs gruppen AKS-hanterad infrastruktur och tilldelar den utgående pool för belastningsutjämnaren.

En offentlig IP-adress som skapats av AKS betraktas som en AKS-hanterad resurs. Det innebär att livs cykeln för den offentliga IP-adressen är avsedd att hanteras av AKS och kräver ingen användar åtgärd direkt på den offentliga IP-resursen. Du kan också tilldela egna offentliga IP-adresser eller offentliga IP-prefix när klustret skapas. Dina anpassade IP-adresser kan också uppdateras på ett befintligt klusters egenskaper för belastningsutjämnare.

Krav för att använda din egen offentliga IP-adress eller prefix:

- Anpassade offentliga IP-adresser måste skapas och ägas av användaren. Hanterade offentliga IP-adresser som skapats av AKS kan inte återanvändas som en egen anpassad IP-adress eftersom det kan orsaka hanterings konflikter.
- Du måste se till att AKS-kluster identiteten (tjänstens huvud namn eller hanterad identitet) har behörighet att komma åt den utgående IP-adressen. Enligt [listan över nödvändiga offentliga IP-behörigheter](kubernetes-service-principal.md#networking).
- Kontrol lera att du uppfyller de [krav och begränsningar](../virtual-network/public-ip-address-prefix.md#constraints) som krävs för att konfigurera utgående IP-adresser eller utgående IP-prefix.

#### <a name="update-the-cluster-with-your-own-outbound-public-ip"></a>Uppdatera klustret med din egen utgående offentliga IP-adress

Använd kommandot [AZ Network Public-IP show][az-network-public-ip-show] för att visa ID: n för dina offentliga IP-adresser.

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

Kommandot ovan visar ID: t för *myPublicIP* offentliga IP i resurs gruppen *myResourceGroup* .

Använd `az aks update` kommandot med **`load-balancer-outbound-ips`** parametern för att uppdatera ditt kluster med dina offentliga IP-adresser.

I följande exempel används `load-balancer-outbound-ips` parametern med ID: n från föregående kommando.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

#### <a name="update-the-cluster-with-your-own-outbound-public-ip-prefix"></a>Uppdatera klustret med ditt eget utgående offentliga IP-prefix

Du kan också använda offentliga IP-prefix för utgående trafik med SKU: n för *standard* -SKU. I följande exempel används kommandot [AZ Network Public-IP prefixet show][az-network-public-ip-prefix-show] för att visa en lista med ID: n för dina offentliga IP-prefix:

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

Kommandot ovan visar ID: t för det offentliga IP-prefixet *myPublicIPPrefix* i resurs gruppen *myResourceGroup* .

I följande exempel används parametern *Load-Balancer-utgående-IP-prefix* med ID: n från föregående kommando.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

#### <a name="create-the-cluster-with-your-own-public-ip-or-prefixes"></a>Skapa klustret med din egen offentliga IP-adress eller prefix

Du kanske vill ta med dina egna IP-adresser eller IP-prefix för utgående trafik när klustret skapas för att ge stöd för scenarier som att lägga till utgående slut punkter i en lista över tillåtna. Lägg till samma parametrar som visas ovan i steget Skapa kluster för att definiera egna offentliga IP-adresser och IP-prefix i början av ett klusters livs cykel.

Använd kommandot *AZ AKS Create* med parametern *Load-Balancer-utgående-IP* för att skapa ett nytt kluster med dina offentliga IP-adresser vid start.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Använd kommandot *AZ AKS Create* med parametern *Load-Balancer-utgående-IP-prefix* för att skapa ett nytt kluster med dina offentliga IP-prefix vid starten.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

### <a name="configure-the-allocated-outbound-ports"></a>Konfigurera de allokerade utgående portarna

> [!IMPORTANT]
> Om du har program i klustret som förväntas upprätta ett stort antal anslutningar till en liten uppsättning destinationer, t. ex. många klient dels instanser ansluter till en SQL-databas har du ett scenario som är mycket känsligt för att drabbas av antalet SNAT-portar (att ta slut på portar att ansluta från). I dessa scenarier rekommenderar vi starkt att du ökar de allokerade utgående portarna och IP-adresserna för utgående klient del på belastningsutjämnaren. Ökningen bör se till att en (1) ytterligare IP-adress lägger till 64 KB ytterligare portar att distribuera över alla klusternoder.


Om inget annat anges kommer AKS att använda standardvärdet för allokerade utgående portar som Standard Load Balancer definierar när den konfigureras. Det här värdet är **Null** i AKS-API: et eller **0** i SLB-API: et som visas i kommandot nedan:

```azurecli-interactive
NODE_RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az network lb outbound-rule list --resource-group $NODE_RG --lb-name kubernetes -o table
```

Föregående kommandon visar utgående regel för belastningsutjämnaren, till exempel:

```console
AllocatedOutboundPorts    EnableTcpReset    IdleTimeoutInMinutes    Name             Protocol    ProvisioningState    ResourceGroup
------------------------  ----------------  ----------------------  ---------------  ----------  -------------------  -------------
0                         True              30                      aksOutboundRule  All         Succeeded            MC_myResourceGroup_myAKSCluster_eastus  
```

Det innebär inte att du har 0 portar, men i stället använder du den [automatiska utgående port tilldelningen baserat på storleken på backend-poolen][azure-lb-outbound-preallocatedports], så om ett kluster har 50 eller färre noder allokeras 1024-portar för varje nod, när du ökar antalet noder, så att du gradvis får färre portar per nod.


Om du vill definiera eller öka antalet allokerade utgående portar kan du följa exemplet nedan:


```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 7 \
    --load-balancer-outbound-ports 4000
```

I det här exemplet får du 4000 allokerade utgående portar för varje nod i mitt kluster och med 7 IP-adresser kan du ha *4000 portar per nod * 100 Nodes = 400k total ports < = 448k total ports = 7 IP-adresser * 64 KB portar per IP*. På så sätt kan du på ett säkert sätt skala till 100 noder och ha en standard uppgraderings åtgärd. Det är viktigt att allokera tillräckligt många portar för ytterligare noder som krävs för uppgradering och andra åtgärder. AKS är som standard en buffert för uppgradering, i det här exemplet kräver detta att 4000 kostnads fria portar vid varje viss tidpunkt. Om du använder [maxSurge-värden](upgrade-cluster.md#customize-node-surge-upgrade)multiplicerar du utgående portar per nod med ditt maxSurge-värde.

För att säkert gå över 100 noder måste du lägga till fler IP-adresser.


> [!IMPORTANT]
> Du måste [Beräkna den begärda kvoten och kontrol lera kraven][requirements] innan du anpassar *allocatedOutboundPorts* för att undvika problem med anslutning eller skalning.

Du kan också använda **`load-balancer-outbound-ports`** parametrarna när du skapar ett kluster, men du måste även ange antingen **`load-balancer-managed-outbound-ip-count`** , **`load-balancer-outbound-ips`** eller **`load-balancer-outbound-ip-prefixes`** .  Exempel:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-sku standard \
    --load-balancer-managed-outbound-ip-count 2 \
    --load-balancer-outbound-ports 1024 
```

### <a name="configure-the-load-balancer-idle-timeout"></a>Konfigurera belastningsutjämnaren för inaktivitet

När SNAT-port resurserna är slut så att utgående flöden inte fungerar förrän befintliga flöden frigör SNAT-portar. Load Balancer frigör SNAT-portar när flödet stängs och den AKS som har kon figurer ATS använder en inaktiv tids gräns på 30 minuter för att frigöra SNAT-portar från inaktiva flöden.
Du kan också använda transport (till exempel **`TCP keepalives`** ) eller **`application-layer keepalives`** Uppdatera ett inaktivt flöde och återställa denna tids gräns vid behov. Du kan konfigurera denna tids gräns enligt exemplet nedan: 


```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-idle-timeout 4
```

Om du förväntar dig att ha flera kort periodiska anslutningar, och det inte finns några anslutningar som är långa och som kan ha långa tids krävande, som att `kubectl proxy` `kubectl port-forward` använda eller överväga att använda ett lågt timeoutvärde, till exempel 4 minuter. När du använder TCP keepalive-objekt räcker det med att aktivera dem på ena sidan av anslutningen. Det räcker till exempel att aktivera dem på Server sidan endast för att återställa inaktiv timer för flödet och det är inte nödvändigt för båda sidorna att starta TCP keepalive. Det finns liknande koncept för program lager, inklusive databas klient server konfiguration. Kontrol lera Server sidan för vilka alternativ som finns för programspecifika keepalive-objekt.

> [!IMPORTANT]
> AKS aktiverar TCP-återställning vid inaktivitet som standard och rekommenderar att du behåller den här konfigurationen och utnyttjar den för mer förutsägbart program beteende i dina scenarier.
> TCP: a skickas bara under TCP-anslutning i upprättat läge. Du kan läsa mer om det [här](../load-balancer/load-balancer-tcp-reset.md).

### <a name="requirements-for-customizing-allocated-outbound-ports-and-idle-timeout"></a>Krav för att anpassa allokerade utgående portar och tids gräns för inaktivitet

- Värdet som du anger för *allocatedOutboundPorts* måste också vara en multipel av 8.
- Du måste ha tillräckligt med utgående IP-kapacitet baserat på antalet virtuella noder och nödvändiga allokerade portar. Om du vill kontrol lera att du har tillräckligt med utgående IP-kapacitet använder du följande formel: 
 
*outboundIPs* \* 64 000 \> *nodeVMs* \* *desiredAllocatedOutboundPorts*.
 
Om du till exempel har 3 *nodeVMs* och 50 000 *desiredAllocatedOutboundPorts* måste du ha minst 3 *outboundIPs*. Vi rekommenderar att du införlivar ytterligare utgående IP-kapacitet utöver det du behöver. Dessutom måste du ha ett konto för den automatiska skalnings tjänsten för klustret och möjligheten att uppgradera noder i noden när du beräknar utgående IP-kapacitet. Granska antalet aktuella noder och maximalt antal noder och Använd det högre värdet för klustrets autoskalning. För att uppgradera måste du konto för en ytterligare virtuell nod för varje nod som tillåter uppgradering.

- När du anger *IdleTimeoutInMinutes* till ett annat värde än standardvärdet på 30 minuter bör du fundera på hur länge dina arbets belastningar behöver en utgående anslutning. Tänk också på att standardvärdet för timeout för en *standard* -SKU-belastningsutjämnare som används utanför AKS är 4 minuter. Ett *IdleTimeoutInMinutes* -värde som bättre återspeglar din speciella AKS-arbetsbelastning kan minska SNAT-belastningen som orsakas av att de kopplings anslutningar som inte längre används.

> [!WARNING]
> Att ändra värdena för *AllocatedOutboundPorts* och *IdleTimeoutInMinutes* kan märkbart ändra beteendet för den utgående regeln för belastningsutjämnaren och bör inte göras lätt, utan att förstå kompromisserna och programmets anslutnings mönster, se [avsnittet om fel sökning av SNAT nedan][troubleshoot-snat] och granska [Load Balancer utgående regler][azure-lb-outbound-rules-overview] och [utgående anslutningar i Azure][azure-lb-outbound-connections] innan du uppdaterar dessa värden för att helt förstå effekten av dina ändringar.

## <a name="restrict-inbound-traffic-to-specific-ip-ranges"></a>Begränsa inkommande trafik till vissa IP-intervall

I följande manifest används *loadBalancerSourceRanges* för att ange ett nytt IP-intervall för inkommande extern trafik:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
  loadBalancerSourceRanges:
  - MY_EXTERNAL_IP_RANGE
```

> [!NOTE]
> Inkommande, extern trafik flödar från belastningsutjämnaren till det virtuella nätverket för ditt AKS-kluster. Det virtuella nätverket har en nätverks säkerhets grupp (NSG) som tillåter all inkommande trafik från belastningsutjämnaren. I den här NSG används ett [service tag][service-tags] av typen *Loadbalancer* för att tillåta trafik från belastningsutjämnaren.

## <a name="maintain-the-clients-ip-on-inbound-connections"></a>Behåll klientens IP på inkommande anslutningar

Som standard behåller en tjänst av typen `LoadBalancer` [i Kubernetes](https://kubernetes.io/docs/tutorials/services/source-ip/#source-ip-for-services-with-type-loadbalancer) och i AKS inte klientens IP-adress på anslutningen till pod. Käll-IP: en för det paket som levereras till pod är den privata IP-adressen för noden. Om du vill underhålla klientens IP-adress måste du ange `service.spec.externalTrafficPolicy` som `local` i tjänst definitionen. Följande manifest visar ett exempel:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  externalTrafficPolicy: Local
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

## <a name="additional-customizations-via-kubernetes-annotations"></a>Ytterligare anpassningar via Kubernetes-anteckningar

Nedan visas en lista över anteckningar som stöds för Kubernetes-tjänster med typ `LoadBalancer` , dessa anteckningar gäller endast för **inkommande** flöden:

| Anteckning | Värde | Beskrivning
| ----------------------------------------------------------------- | ------------------------------------- | ------------------------------------------------------------ 
| `service.beta.kubernetes.io/azure-load-balancer-internal`         | `true` eller `false`                     | Ange om belastningsutjämnaren ska vara intern. Den är som standard offentlig om den inte har angetts.
| `service.beta.kubernetes.io/azure-load-balancer-internal-subnet`  | Namn på under nätet                    | Ange vilket undernät som den interna belastningsutjämnaren ska bindas till. Det är standard under nätet som kon figurer ATS i moln konfigurations filen om det inte har angetts.
| `service.beta.kubernetes.io/azure-dns-label-name`                 | Namnet på DNS-etiketten på offentliga IP-adresser   | Ange namnet på DNS-etiketten för den **offentliga** tjänsten. Om den är inställd på en tom sträng kommer DNS-posten i den offentliga IP-adressen inte att användas.
| `service.beta.kubernetes.io/azure-shared-securityrule`            | `true` eller `false`                     | Ange att tjänsten ska exponeras med hjälp av en Azure-säkerhetsregel som kan delas med en annan tjänst, handels specificitet för regler för en ökning av antalet tjänster som kan exponeras. Den här anteckningen förlitar sig på funktionen Azure- [förstärkta säkerhets regler](../virtual-network/network-security-groups-overview.md#augmented-security-rules) i nätverks säkerhets grupper. 
| `service.beta.kubernetes.io/azure-load-balancer-resource-group`   | Namnet på resurs gruppen            | Ange resurs gruppen för offentliga IP-adresser i belastningsutjämnaren som inte finns i samma resurs grupp som kluster infrastrukturen (resurs grupp för resurs).
| `service.beta.kubernetes.io/azure-allowed-service-tags`           | Lista över tillåtna service märken          | Ange en lista över tillåtna [service märken][service-tags] avgränsade med kommatecken.
| `service.beta.kubernetes.io/azure-load-balancer-tcp-idle-timeout` | Timeout för TCP-inaktivitet på några minuter          | Ange efter hur lång tid i minuter som TCP-anslutningens tids gräns ska ske i belastningsutjämnaren. Standard och minimalt värde är 4. Högsta värdet är 30. Måste vara ett heltal.
|`service.beta.kubernetes.io/azure-load-balancer-disable-tcp-reset` | `true`                                | Inaktivera `enableTcpReset` för SLB


## <a name="troubleshooting-snat"></a>Felsöka SNAT

Om du vet att du startar många utgående TCP-eller UDP-anslutningar till samma mål-IP-adress och port, och du ser att det inte går att använda utgående anslutningar eller om du underrättar support om att du har frigjort SNAT-portar (förallokerade tillfälliga portar som används av PAT) har du flera allmänna alternativ för att minska. Granska de här alternativen och Bestäm vad som är tillgängligt och bäst för ditt scenario. Det är möjligt att en eller flera kan hjälpa dig att hantera det här scenariot. Detaljerad information finns i [fel söknings guiden för utgående anslutningar](../load-balancer/troubleshoot-outbound-connection.md).

Ofta är rotor saken av SNAT-belastning ett antimönster för hur utgående anslutningar upprättas, hanteras eller konfigurerbara timers som ändras från standardvärdena. Granska det här avsnittet noggrant.

### <a name="steps"></a>Steg
1. Kontrol lera om dina anslutningar är inaktiva under en längre tid och förlitar sig på den förvalda tids gränsen för att släppa porten. Om så är fallet kan standard tids gränsen på 30 min behöva minskas för ditt scenario.
2. Undersök hur programmet skapar utgående anslutning (till exempel kod granskning eller paket fångst).
3. Avgöra om den här aktiviteten är förväntad eller om programmet är felgenererat. Använd [mått](../load-balancer/load-balancer-standard-diagnostics.md) och [loggar](../load-balancer/load-balancer-monitor-log.md) i Azure Monitor för att styrka dina resultat. Använd kategorin "misslyckades" för mått för SNAT-anslutningar.
4. Utvärdera om lämpliga [mönster](#design-patterns) följs.
5. Utvärdera om minnes belastningen för SNAT ska begränsas med [ytterligare utgående IP-adresser + ytterligare tilldelade utgående portar](#configure-the-allocated-outbound-ports) .

### <a name="design-patterns"></a>Designmönster
Dra alltid nytta av anslutnings åter användning och anslutningspoolen närhelst det är möjligt. De här mönstren kommer att undvika problem med resurs överbelastning och resultera i förutsägbara beteenden. Primitiver för dessa mönster finns i många utvecklings bibliotek och ramverk.

- Atomiska förfrågningar (en förfrågan per anslutning) är vanligt vis inte ett bra design val. Sådana kant mönster gränser skalar, minskar prestandan och minskar tillförlitligheten. I stället kan du använda HTTP/S-anslutningar för att minska antalet anslutningar och associerade SNAT-portar. Program skalan ökar och förbättrar prestandan på grund av minskad hand skakning, kostnader för omkostnader och kryptografiska åtgärder när du använder TLS.
- Om du använder kluster/anpassad DNS, eller om anpassade överordnade servrar på coreDNS, har i åtanke att DNS kan introducera många enskilda flöden på volymen när klienten inte cachelagrar resultatet av DNS-matcharen. Se till att anpassa coreDNS i stället för att använda anpassade DNS-servrar och definiera ett lämpligt värde för cachelagring.
- UDP-flöden (till exempel DNS-sökningar) allokera SNAT-portar för varaktigheten för tids gränsen för inaktivitet. Ju längre tids gräns för inaktivitet, desto högre belastning på SNAT-portar. Använd kort tids gräns för inaktivitet (till exempel 4 minuter).
Använd anslutningspooler för att forma din anslutnings volym.
- Överge aldrig ett TCP-flöde och Använd TCP-timers för att rensa flödet. Om du inte tillåter TCP att uttryckligen stänga anslutningen är tillstånd fortfarande allokerat på mellan system och slut punkter och gör SNAT-portar otillgängliga för andra anslutningar. Det här mönstret kan utlösa program haverier och SNAT-belastningar.
- Ändra inte TCP/Close-relaterade timer-värden i OS-nivå utan expert kunskaper om påverkan. Även om TCP-stacken kommer att återställas kan program prestandan påverkas negativt när slut punkterna för en anslutning har fel matchnings förväntningar. Att ändra timers är vanligt vis ett tecken på ett underliggande design problem. Granska följande rekommendationer.


Exemplet ovan uppdaterar regeln för att endast tillåta inkommande extern trafik från *MY_EXTERNAL_IP_RANGEs* intervallet. Om du ersätter *MY_EXTERNAL_IP_RANGE* med IP-adressen för det interna under nätet är trafiken begränsad till enbart interna IP-adresser. Detta tillåter inte att klienter utanför Kubernetes-klustret får åtkomst till belastningsutjämnaren.

## <a name="moving-from-a-basic-sku-load-balancer-to-standard-sku"></a>Flytta från en Basic SKU-belastningsutjämnare till standard-SKU: n

Om du har ett befintligt kluster med Basic-SKU Load Balancer, finns det viktiga skillnader att tänka på när du migrerar till att använda ett kluster med standard-SKU: n Load Balancer.

Om du till exempel gör blå/gröna distributioner för att migrera kluster, är det en vanlig metod som `load-balancer-sku` ger en typ av kluster som bara kan definieras i klustrets skapande tid. *Basic SKU* -belastningsutjämnare använder dock *grundläggande SKU* -IP-adresser, som inte är kompatibla med *standardsku* : er för SKU: er som *standard-SKU* : er. När du migrerar kluster för att uppgradera Load Balancer SKU: er krävs en ny IP-adress med en kompatibel IP-SKU.

Mer information om hur du migrerar kluster finns i [vår dokumentation om migrering](aks-migration.md) för att se en lista över viktiga ämnen som du bör tänka på när du migrerar. Begränsningarna nedan är också viktiga skillnader när du antecknar när du använder standard-SKU: er i AKS.

## <a name="limitations"></a>Begränsningar

Följande begränsningar gäller när du skapar och hanterar AKS-kluster som stöder en belastningsutjämnare med *standard* -SKU: n:

* Minst en offentlig IP-adress eller ett IP-prefix krävs för att tillåta utgående trafik från AKS-klustret. Det offentliga IP-eller IP-prefixet krävs också för att upprätthålla anslutningen mellan kontroll planet och agent-noder och för att upprätthålla kompatibilitet med tidigare versioner av AKS. Du kan välja mellan följande alternativ för att ange offentliga IP-adresser eller IP-prefix med en *standard* -SKU-belastningsutjämnare:
    * Ange dina egna offentliga IP-adresser.
    * Ange egna offentliga IP-prefix.
    * Ange ett tal upp till 100 för att tillåta att AKS-klustret skapar att många *standard* -SKU offentliga IP-adresser i samma resurs grupp som skapats som AKS-kluster, som vanligt vis heter med *MC_* i början. AKS tilldelar den offentliga IP-adressen till *standard* -SKU-belastningsutjämnaren. Som standard skapas en offentlig IP-adress automatiskt i samma resurs grupp som AKS-klustret, om ingen offentlig IP, ett offentligt IP-prefix eller antal IP-adresser anges. Du måste också tillåta offentliga adresser och undvika att skapa Azure Policy som tillåter att IP skapas.
* En offentlig IP-adress som skapats av AKS kan inte återanvändas som en anpassad offentlig IP-adress. Alla anpassade IP-adresser måste skapas och hanteras av användaren.
* Du kan bara definiera belastningsutjämnare-SKU: n när du skapar ett AKS-kluster. Du kan inte ändra SKU: n för belastningsutjämnaren efter att ett AKS-kluster har skapats.
* Du kan bara använda en typ av SKU för belastningsutjämnare (Basic eller standard) i ett enda kluster.
* *Standard* SKU load Balances stöder endast IP-adresser för *standard* -SKU.


## <a name="next-steps"></a>Nästa steg

Läs mer om Kubernetes Services i [dokumentationen för Kubernetes Services][kubernetes-services].

Läs mer om hur du använder interna Load Balancer för inkommande trafik i [AKS interna Load Balancer-dokumentationen](internal-lb.md).

<!-- LINKS - External -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-network-lb-outbound-rule-list]: /cli/azure/network/lb/outbound-rule#az-network-lb-outbound-rule-list
[az-network-public-ip-show]: /cli/azure/network/public-ip#az-network-public-ip-show
[az-network-public-ip-prefix-show]: /cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/skus.md
[azure-lb-outbound-rules]: ../load-balancer/load-balancer-outbound-connections.md#outboundrules
[azure-lb-outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md
[azure-lb-outbound-preallocatedports]: ../load-balancer/load-balancer-outbound-connections.md#preallocatedports
[azure-lb-outbound-rules-overview]: ../load-balancer/load-balancer-outbound-connections.md#outboundrules
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[requirements]: #requirements-for-customizing-allocated-outbound-ports-and-idle-timeout
[use-multiple-node-pools]: use-multiple-node-pools.md
[troubleshoot-snat]: #troubleshooting-snat
[service-tags]: ../virtual-network/network-security-groups-overview.md#service-tags
