---
title: Använda en offentlig Load Balancer
titleSuffix: Azure Kubernetes Service
description: Lär dig hur du använder en offentlig lastbalanserare med en standard-SKU för att exponera dina tjänster med Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 11/14/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 3f2219f5052aee0c0a9cd43aa87df8789adbcae2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783097"
---
# <a name="use-a-public-standard-load-balancer-in-azure-kubernetes-service-aks"></a>Använda en offentlig Standard Load Balancer i Azure Kubernetes Service (AKS)

Den Azure Load Balancer finns på L4 i Open Systems Interconnection (OSI)-modellen som stöder både inkommande och utgående scenarier. Den distribuerar inkommande flöden som kommer till lastbalanseras frontend till instanserna av backend-poolen.

En **offentlig** Load Balancer när den är integrerad med AKS har två syften:

1. För att tillhandahålla utgående anslutningar till klusternoderna i det virtuella AKS-nätverket. Det uppnår det här målet genom att översätta nodernas privata IP-adress till en offentlig IP-adress som är en del av dess *utgående pool.*
2. För att ge åtkomst till program via Kubernetes-tjänster av typen `LoadBalancer` . Med den kan du enkelt skala dina program och skapa tjänster med hög tillgång.

En **intern (eller privat)** lastbalanserare används där endast privata IP-adresser tillåts som frontend. Interna lastbalanserare används för att belastningsutjämna trafik i ett virtuellt nätverk. En lastbalanserare kan också nås från ett lokalt nätverk i ett hybridscenario.

Det här dokumentet beskriver integreringen med offentlig lastbalanserare. Intern integrering Load Balancer I dokumentationen för [AKS Internal Load Balancer.](internal-lb.md)

## <a name="before-you-begin"></a>Innan du börjar

Azure Load Balancer finns i två SKU:er – *Basic* och *Standard*. Standard-SKU:n används när du skapar ett AKS-kluster.  Använd *standard-SKU* för att få åtkomst till ytterligare funktioner, [](use-multiple-node-pools.md)till exempel en större backend-pool, flera nodpooler [**och Tillgänglighetszoner**](availability-zones.md). Det är den rekommenderade SKU Load Balancer för AKS.

Mer information om Basic- och *Standard-SKU:er* finns i Jämförelse av SKU:er för [Azure Load Balancer.][azure-lb-comparison] 

Den här artikeln förutsätter att du  har ett AKS-kluster med standard-SKU Azure Load Balancer och går igenom hur du använder och konfigurerar några av funktionerna i lastbalanseraren. Om du behöver ett AKS-kluster kan du gå till AKS-snabbstarten med hjälp av [Azure CLI][aks-quickstart-cli] eller [använda Azure Portal][aks-quickstart-portal].

> [!IMPORTANT]
> Om du föredrar att inte använda Azure Load Balancer för att tillhandahålla utgående anslutning och i stället har en egen gateway, brandvägg eller proxy för detta ändamål kan du hoppa över skapandet av den utgående poolen för lastbalanserare och respektive ip-adress på frontend genom att använda utgående typ som [**UserDefinedRouting (UDR).**](egress-outboundtype.md) Typen Utgående definierar utgående metod för ett kluster och standardvärdet är: lastbalanserare.

## <a name="use-the-public-standard-load-balancer"></a>Använda den offentliga standardlastbalanseraren

När du har skapat ett AKS-kluster med utgående typ: Load Balancer (standard) är klustret redo att använda lastbalanseraren för att även exponera tjänster.

För detta kan du skapa en offentlig tjänst av `LoadBalancer` typen som visas i följande exempel. Börja med att skapa ett tjänstmanifest med namnet `public-svc.yaml` :

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

Distribuera det offentliga tjänstmanifestet [med kubectl apply][kubectl-apply] och ange namnet på ditt YAML-manifest:

```azurecli-interactive
kubectl apply -f public-svc.yaml
```

Den Azure Load Balancer konfigureras med en ny offentlig IP-adress som kommer att använda den här nya tjänsten. Eftersom Azure Load Balancer kan ha flera IP-adresser för frontend får varje ny distribuerad tjänst en ny dedikerad IP-adress för frontend som är unikt åtkomlig.

Du kan bekräfta att tjänsten har skapats och att lastbalanseraren har konfigurerats genom att köra till exempel:

```azurecli-interactive
kubectl get service public-svc
```

```console
NAMESPACE     NAME          TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)         AGE
default       public-svc    LoadBalancer   10.0.39.110    52.156.88.187   80:32068/TCP    52s
```

När du visar tjänstinformationen visas den offentliga IP-adress som skapades för den här tjänsten i lastbalanseraren i *kolumnen EXTERNAL-IP.* Det kan ta en minut eller två innan IP-adressen ändras från till en faktisk offentlig *\<pending\>* IP-adress, som du ser i exemplet ovan.

## <a name="configure-the-public-standard-load-balancer"></a>Konfigurera den offentliga standardlastbalanseraren

När du använder offentlig standard-SKU-lastbalanserare finns det en uppsättning alternativ som kan anpassas när den skapas eller genom att uppdatera klustret. Med dessa alternativ kan du anpassa Load Balancer för att uppfylla dina arbetsbelastningsbehov och bör granskas i enlighet med detta. Med standardlastbalanserare kan du:

* Ange eller skala antalet hanterade utgående IP-adresser
* Ta med dina egna anpassade [utgående IP-adresser eller utgående IP-prefix](#provide-your-own-outbound-public-ips-or-prefixes)
* Anpassa antalet allokerade utgående portar till varje nod i klustret
* Konfigurera tidsgränsinställningen för inaktiva anslutningar

> [!IMPORTANT]
> Endast ett alternativ för utgående IP -adresser (hanterade IP-adresser, bring your own IP eller IP-prefix) kan användas vid en viss tidpunkt.

### <a name="scale-the-number-of-managed-outbound-public-ips"></a>Skala antalet hanterade utgående offentliga IP-adresser

Azure Load Balancer ger utgående anslutning från ett virtuellt nätverk utöver inkommande. Regler för utgående trafik gör det enkelt att konfigurera Standard Load Balancer offentliga nätverkets utgående nätverksadressöversättning.

Precis som Load Balancer regler följer regler för utgående trafik samma välbekanta syntax som belastningsutjämning och inkommande NAT-regler:

***IP-adresser för frontend + parametrar + backend-pool***

En regel för utgående trafik konfigurerar utgående NAT för alla virtuella datorer som identifieras av serverpoolen som ska översättas till frontend. Parametrarna ger ytterligare ingående kontroll över den utgående NAT-algoritmen.

Även om en utgående regel bara kan användas med en enda offentlig IP-adress, underlättar utgående regler konfigurationsbelastningen vid skalning av utgående NAT. Du kan använda flera IP-adresser för att planera för storskaliga scenarier och du kan använda utgående regler för att minska SNAT-mönster som är känsliga för utmattning. Varje ytterligare IP-adress som tillhandahålls av en frontend tillhandahåller 64 000 tillfälliga portar som Load Balancer använda som SNAT-portar. 

När du använder en standard-SKU-lastbalanserare med hanterade utgående offentliga IP-adresser, som skapas som standard, kan du skala antalet hanterade utgående offentliga IP-adresser med hjälp av  **`load-balancer-managed-ip-count`** parametern .

Kör följande kommando för att uppdatera ett befintligt kluster. Den här parametern kan också anges vid kluster create-time för att ha flera hanterade utgående offentliga IP-adresser.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

I exemplet ovan anges antalet hanterade utgående offentliga IP-adresser till *2* för *myAKSCluster-klustret* i *myResourceGroup*. 

Du kan också använda parametern för att ange det första antalet hanterade offentliga IP-adresser när du skapar klustret genom att lägga till parametern och ange det **`load-balancer-managed-ip-count`** **`--load-balancer-managed-outbound-ip-count`** till önskat värde. Standardantalet för hanterade utgående offentliga IP-adresser är 1.

### <a name="provide-your-own-outbound-public-ips-or-prefixes"></a>Ange dina egna utgående offentliga IP-adresser eller prefix

När du  använder en standard-SKU-lastbalanserare skapar AKS-klustret som standard automatiskt en offentlig IP-adress i resursgruppen för den AKS-hanterade infrastrukturen och tilldelar den till den utgående poolen för lastbalanserare.

En offentlig IP-adress som skapats av AKS betraktas som en AKS-hanterad resurs. Det innebär att livscykeln för den offentliga IP-adressen är avsedd att hanteras av AKS och kräver ingen användaråtgärd direkt på den offentliga IP-resursen. Du kan också tilldela din egen anpassade offentliga IP-adress eller offentliga IP-prefix när klustret skapas. Dina anpassade IP-adresser kan också uppdateras i ett befintligt klusters lastbalanseringsegenskaper.

Krav för att använda din egen offentliga IP-adress eller prefix:

- Anpassade offentliga IP-adresser måste skapas och ägas av användaren. Hanterade offentliga IP-adresser som skapats av AKS kan inte återanvändas som en egen anpassad IP-adress eftersom det kan orsaka hanteringskonflikter.
- Du måste se till att AKS-klusteridentiteten (tjänstens huvudnamn eller hanterade identitet) har behörighet att komma åt den utgående IP-adressen. Enligt listan över [offentliga IP-behörigheter som krävs.](kubernetes-service-principal.md#networking)
- Se till att du uppfyller de krav och begränsningar som krävs för att konfigurera utgående IP-adresser eller utgående [IP-prefix.](../virtual-network/public-ip-address-prefix.md#constraints)

#### <a name="update-the-cluster-with-your-own-outbound-public-ip"></a>Uppdatera klustret med din egen utgående offentliga IP-adress

Använd kommandot [az network public-ip show][az-network-public-ip-show] för att lista IP-adresserna för dina offentliga IP-adresser.

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

Kommandot ovan visar ID:t för den offentliga *IP-adressen myPublicIP* i *resursgruppen myResourceGroup.*

Använd kommandot `az aks update` med **`load-balancer-outbound-ips`** parametern för att uppdatera klustret med dina offentliga IP-adresser.

I följande exempel används `load-balancer-outbound-ips` parametern med ID:na från föregående kommando.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

#### <a name="update-the-cluster-with-your-own-outbound-public-ip-prefix"></a>Uppdatera klustret med ditt eget offentliga IP-prefix för utgående trafik

Du kan också använda offentliga IP-prefix  för utgående med standard-SKU-lastbalanseraren. I följande exempel används kommandot [az network public-ip prefix show][az-network-public-ip-prefix-show] för att lista IP-adresserna för dina offentliga IP-prefix:

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

Kommandot ovan visar ID:t för det offentliga *IP-prefixet myPublicIPPrefix* i *resursgruppen myResourceGroup.*

I följande exempel används *parametern load-balancer-outbound-ip-prefixes* med ID:n från föregående kommando.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

#### <a name="create-the-cluster-with-your-own-public-ip-or-prefixes"></a>Skapa klustret med din egen offentliga IP-adress eller prefix

Du kanske vill ta med dina egna IP-adresser eller IP-prefix för utgående när klustret skapas för att stödja scenarier som att lägga till utgående slutpunkter i en lista över tillåtna. Lägg till samma parametrar som visas ovan i steget för att skapa kluster för att definiera dina egna offentliga IP-adresser och IP-prefix i början av ett klusters livscykel.

Använd kommandot *az aks create* med parametern *load-balancer-outbound-ips* för att skapa ett nytt kluster med dina offentliga IP-adresser i början.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Använd kommandot *az aks create* med parametern *load-balancer-outbound-ip-prefixes* för att skapa ett nytt kluster med dina offentliga IP-prefix i början.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

### <a name="configure-the-allocated-outbound-ports"></a>Konfigurera de allokerade utgående portarna

> [!IMPORTANT]
> Om du har program i klustret som förväntas upprätta ett stort antal anslutningar till en liten uppsättning mål, t.ex. många klientinstanser som ansluter till en SQL-databas. Du har ett scenario som är mycket sårbart för SNAT-portutmattning (slut på portar att ansluta från). I dessa scenarier rekommenderar vi starkt att du ökar de allokerade utgående portarna och utgående IP-adresser på lastbalanseraren. Ökningen bör ta hänsyn till att en (1) ytterligare IP-adress lägger till 64 000 ytterligare portar för distribution över alla klusternoder.


Om inget annat anges använder AKS standardvärdet Allokerade utgående portar som definieras Standard Load Balancer när det konfigureras. Det här värdet **är null** i AKS-API:et **eller 0** i SLB-API:et enligt följande kommando:

```azurecli-interactive
NODE_RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az network lb outbound-rule list --resource-group $NODE_RG --lb-name kubernetes -o table
```

Föregående kommandon visar en lista över regeln för utgående trafik för lastbalanseraren, till exempel:

```console
AllocatedOutboundPorts    EnableTcpReset    IdleTimeoutInMinutes    Name             Protocol    ProvisioningState    ResourceGroup
------------------------  ----------------  ----------------------  ---------------  ----------  -------------------  -------------
0                         True              30                      aksOutboundRule  All         Succeeded            MC_myResourceGroup_myAKSCluster_eastus  
```

Dessa utdata innebär inte att du har 0 portar, utan i stället att du använder den automatiska tilldelningen av utgående [portar][azure-lb-outbound-preallocatedports]baserat på serverpoolens storlek, så om ett kluster har 50 eller färre noder allokeras 1 024 portar för varje nod, när du ökar antalet noder därifrån får du gradvis färre portar per nod.


Om du vill definiera eller öka antalet allokerade utgående portar kan du följa exemplet nedan:


```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 7 \
    --load-balancer-outbound-ports 4000
```

Det här exemplet skulle ge dig 4 000 allokerade utgående portar för varje nod i mitt kluster, och med 7 IP-adresser skulle du ha 4 000 portar per nod ** 100 noder = totalt 400 000 portar < = totalt 448 000 portar = 7 IP-adresser * 64 000 portar per IP-adress.* På så sätt kan du på ett säkert sätt skala till 100 noder och ha en standarduppgraderingsåtgärd. Det är viktigt att allokera tillräckligt med portar för ytterligare noder som behövs för uppgradering och andra åtgärder. AKS använder som standard en buffertnod för uppgradering. I det här exemplet krävs 4 000 kostnadsfria portar vid en given tidpunkt. Om du [använder maxSurge-värden](upgrade-cluster.md#customize-node-surge-upgrade)multiplicerar du de utgående portarna per nod med ditt maxSurge-värde.

Om du vill gå över 100 noder på ett säkert sätt måste du lägga till fler IP-adresser.


> [!IMPORTANT]
> Du måste [beräkna den kvot som krävs och kontrollera kraven][requirements] innan du anpassar *allocatedOutboundPorts* för att undvika problem med anslutning eller skalning.

Du kan också använda **`load-balancer-outbound-ports`** parametrarna när du skapar ett kluster, men du måste också ange **`load-balancer-managed-outbound-ip-count`** antingen **`load-balancer-outbound-ips`** , eller **`load-balancer-outbound-ip-prefixes`** .  Exempel:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-sku standard \
    --load-balancer-managed-outbound-ip-count 2 \
    --load-balancer-outbound-ports 1024 
```

### <a name="configure-the-load-balancer-idle-timeout"></a>Konfigurera tidsgränsen för inaktivitet för lastbalanserare

När SNAT-portresurser är uttömda misslyckas utgående flöden tills befintliga flöden släpper SNAT-portar. Load Balancer SNAT-portar när flödet stängs och den AKS-konfigurerade lastbalanseraren använder en tidsgräns på 30 minuter för inaktivitet för att frigöra SNAT-portar från inaktiva flöden.
Du kan också använda transport (till exempel ) eller för att uppdatera ett inaktivt flöde och återställa tidsgränsen för **`TCP keepalives`** **`application-layer keepalives`** inaktivitet om det behövs. Du kan konfigurera den här tidsgränsen enligt exemplet nedan: 


```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-idle-timeout 4
```

Om du förväntar dig att ha många kortvariga anslutningar och inga anslutningar som är långlivade och kan ha långa tider av inaktivitet, som att utnyttja eller överväga att använda ett lågt timeout-värde, till exempel `kubectl proxy` `kubectl port-forward` 4 minuter. När du använder TCP keepalives räcker det också att aktivera dem på ena sidan av anslutningen. Det räcker till exempel att aktivera dem på serversidan för att bara återställa inaktiv timer för flödet och det är inte nödvändigt för båda sidor att starta TCP keepalives. Det finns liknande begrepp för programlagret, inklusive databasklient-server-konfigurationer. Kontrollera vilka alternativ som finns på serversidan för programspecifika keepalives.

> [!IMPORTANT]
> AKS aktiverar TCP-återställning vid inaktivitet som standard och rekommenderar att du behåller den här konfigurationen på och utnyttjar den för mer förutsägbart programbeteende i dina scenarier.
> TCP RST skickas endast under TCP-anslutningen i etablerat tillstånd. Du kan läsa mer om det [här](../load-balancer/load-balancer-tcp-reset.md).

### <a name="requirements-for-customizing-allocated-outbound-ports-and-idle-timeout"></a>Krav för att anpassa allokerade utgående portar och tidsgräns för inaktivitet

- Det värde som du anger *för allocatedOutboundPorts* måste också vara en multipel av 8.
- Du måste ha tillräckligt med utgående IP-kapacitet baserat på antalet virtuella nod-datorer och nödvändiga allokerade utgående portar. Verifiera att du har tillräckligt med utgående IP-kapacitet med hjälp av följande formel: 
 
*utgående IP-adresser* \* 64 000 \> *nodeVMs* \* *desiredAllocatedOutboundPorts*.
 
Om du till exempel har 3 *nodeVMs* och 50 000 *desiredAllocatedOutboundPorts* måste du ha minst 3 *utgående IP-adresser.* Vi rekommenderar att du använder ytterligare utgående IP-kapacitet utöver vad du behöver. Dessutom måste du ta hänsyn till autoskalning av kluster och möjligheten att uppgradera nodpoolen vid beräkning av utgående IP-kapacitet. För autoskalning av kluster granskar du det aktuella antalet noder och det maximala antalet noder och använder det högre värdet. För uppgradering tar du hänsyn till ytterligare en virtuell nod för varje nodpool som tillåter uppgradering.

- När du *ställer in IdleTimeoutInMinutes* till ett annat värde än standardvärdet på 30 minuter bör du överväga hur länge dina arbetsbelastningar behöver en utgående anslutning. Tänk också på att standardvärdet för timeout för en standard-SKU-lastbalanserare som används utanför AKS är 4 minuter.  Ett *IdleTimeoutInMinutes-värde* som bättre återspeglar din specifika AKS-arbetsbelastning kan minska SNAT-belastningen som orsakas av att anslutningar inte längre används.

> [!WARNING]
> Om du ändrar värdena för *AllocatedOutboundPorts* och *IdleTimeoutInMinutes* kan du avsevärt ändra beteendet för den utgående regeln för lastbalanseraren. Du bör inte göra detta utan att du förstår kompromisserna och anslutningsmönstren för ditt program. Läs avsnittet om felsökning av [SNAT][troubleshoot-snat] nedan och granska [Load Balancer][azure-lb-outbound-rules-overview] utgående regler och utgående anslutningar i [Azure][azure-lb-outbound-connections] innan du uppdaterar dessa värden för att få en fullständig förståelse för hur ändringarna påverkas.

## <a name="restrict-inbound-traffic-to-specific-ip-ranges"></a>Begränsa inkommande trafik till specifika IP-intervall

Följande manifest använder *loadBalancerSourceRanges för att* ange ett nytt IP-intervall för inkommande extern trafik:

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
> Inkommande, extern trafik flödar från lastbalanseraren till det virtuella nätverket för ditt AKS-kluster. Det virtuella nätverket har en nätverkssäkerhetsgrupp (NSG) som tillåter all inkommande trafik från lastbalanseraren. Den här NSG:n [använder en tjänsttagg][service-tags] av *typen LoadBalancer* för att tillåta trafik från lastbalanseraren.

## <a name="maintain-the-clients-ip-on-inbound-connections"></a>Underhålla klientens IP-adress vid inkommande anslutningar

Som standard beständiga inte en tjänst av typen `LoadBalancer` [i Kubernetes](https://kubernetes.io/docs/tutorials/services/source-ip/#source-ip-for-services-with-type-loadbalancer) och i AKS klientens IP-adress vid anslutningen till podden. Käll-IP-adressen för paketet som levereras till podden är nodens privata IP-adress. Om du vill behålla klientens IP-adress måste du ange `service.spec.externalTrafficPolicy` till `local` i tjänstdefinitionen. Följande manifest visar ett exempel:

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

Nedan visas en lista över anteckningar som stöds för Kubernetes-tjänster med typen . Dessa anteckningar `LoadBalancer` gäller endast för **INBOUND-flöden:**

| Anteckning | Värde | Beskrivning
| ----------------------------------------------------------------- | ------------------------------------- | ------------------------------------------------------------ 
| `service.beta.kubernetes.io/azure-load-balancer-internal`         | `true` eller `false`                     | Ange om lastbalanseraren ska vara intern. Standardinställningen är offentlig om den inte har angetts.
| `service.beta.kubernetes.io/azure-load-balancer-internal-subnet`  | Namnet på undernätet                    | Ange vilket undernät som den interna lastbalanseraren ska bindas till. Standardvärdet är det undernät som konfigurerats i molnkonfigurationsfilen om det inte har angetts.
| `service.beta.kubernetes.io/azure-dns-label-name`                 | Namn på DNS-etiketten på offentliga IP-adresser   | Ange DNS-etikettnamnet för den **offentliga** tjänsten. Om den är inställd på tom sträng används inte DNS-posten i den offentliga IP-adressen.
| `service.beta.kubernetes.io/azure-shared-securityrule`            | `true` eller `false`                     | Ange att tjänsten ska exponeras med hjälp av en Azure-säkerhetsregel som kan delas med en annan tjänst, handelsspecifikitet för regler för en ökning av antalet tjänster som kan exponeras. Den här anteckningen är beroende av funktionen [Azure-förhöjda säkerhetsregler](../virtual-network/network-security-groups-overview.md#augmented-security-rules) i nätverkssäkerhetsgrupper. 
| `service.beta.kubernetes.io/azure-load-balancer-resource-group`   | Namnet på resursgruppen            | Ange resursgruppen för offentliga IP-adresser för lastbalanserare som inte finns i samma resursgrupp som klusterinfrastrukturen (nodresursgrupp).
| `service.beta.kubernetes.io/azure-allowed-service-tags`           | Lista över tillåtna tjänsttaggar          | Ange en lista över tillåtna [tjänsttaggar avgränsade][service-tags] med kommatecken.
| `service.beta.kubernetes.io/azure-load-balancer-tcp-idle-timeout` | Tidsgränser för TCP-inaktivitet i minuter          | Ange tiden i minuter för tcp-anslutningens tidsgränser för inaktivitet som ska inträffa på lastbalanseraren. Standardvärdet och minimivärdet är 4. Maxvärdet är 30. Måste vara ett heltal.
|`service.beta.kubernetes.io/azure-load-balancer-disable-tcp-reset` | `true`                                | Inaktivera `enableTcpReset` för SLB


## <a name="troubleshooting-snat"></a>Felsöka SNAT

Om du vet att du startar många utgående TCP- eller UDP-anslutningar till samma mål-IP-adress och port, och du ser misslyckade utgående anslutningar, eller om du får råd av support om att SNAT-portarna (förallokerade tillfälliga portar som används av PAT) tar slut, har du flera allmänna skyddsalternativ. Granska de här alternativen och bestäm vad som är tillgängligt och bäst för ditt scenario. Det är möjligt att en eller flera kan hjälpa dig att hantera det här scenariot. Detaljerad information finns i [felsökningsguiden för utgående anslutningar.](../load-balancer/troubleshoot-outbound-connection.md)

Rotorsaken till SNAT-utmattning är ofta ett antimönster för hur utgående anslutningar upprättas, hanteras eller konfigurerbara timers ändras från deras standardvärden. Granska det här avsnittet noggrant.

### <a name="steps"></a>Steg
1. Kontrollera om dina anslutningar är inaktiva under en längre tid och förlitar sig på standardtidsinställningen för inaktivitet för att frigöra porten. I så fall kan standard-timeouten på 30 min behöva minskas för ditt scenario.
2. Undersök hur ditt program skapar utgående anslutning (till exempel kodgranskning eller paketinfångst).
3. Kontrollera om den här aktiviteten är förväntat beteende eller om programmet beter sig felaktigt. Använd [mått och](../load-balancer/load-balancer-standard-diagnostics.md) loggar i [Azure Monitor](../load-balancer/load-balancer-monitor-log.md) att bekräfta dina resultat. Använd till exempel kategorin "Misslyckades" för måttet SNAT-anslutningar.
4. Utvärdera om [lämpliga mönster](#design-patterns) följs.
5. Utvärdera om SNAT-portutmattning ska minskas med ytterligare [utgående IP-adresser + ytterligare allokerade utgående portar.](#configure-the-allocated-outbound-ports)

### <a name="design-patterns"></a>Designmönster
Dra alltid nytta av återanvändning av anslutningar och anslutningspooler när det är möjligt. De här mönstren undviker problem med resursutmattning och resulterar i förutsägbart beteende. Primitiver för dessa mönster finns i många utvecklingsbibliotek och ramverk.

- Atomiska begäranden (en begäran per anslutning) är vanligtvis inte ett bra designval. Sådana antimönster begränsar skalning, minskar prestanda och minskar tillförlitligheten. Återanvänd i stället HTTP/S-anslutningar för att minska antalet anslutningar och associerade SNAT-portar. Programskalan ökar och prestandan förbättras på grund av minskade kostnader för handskakningar, omkostnader och kryptografisk åtgärd vid användning av TLS.
- Om du använder utanför kluster/anpassad DNS, eller om anpassade överordnade servrar på coreDNS har i åtanke att DNS kan introducera många enskilda flöden på volymen när klienten inte cachelagrar DNS-matchare resultatet. Se till att anpassa coreDNS först i stället för att använda anpassade DNS-servrar och definiera ett bra cachelagringsvärde.
- UDP-flöden (till exempel DNS-sökning) allokerar SNAT-portar under tidsgränsen för inaktivitet. Ju längre tidsgränsen för inaktivitet är, desto högre är trycket på SNAT-portar. Använd kort tidsgräns för inaktivitet (till exempel 4 minuter).
Använd anslutningspooler för att forma anslutningsvolymen.
- Lämna aldrig ett TCP-flöde tyst och förlita dig på TCP-timers för att rensa flödet. Om du inte tillåter att TCP uttryckligen stänger anslutningen förblir tillståndet allokerat i mellanliggande system och slutpunkter och gör SNAT-portar otillgängliga för andra anslutningar. Det här mönstret kan utlösa programfel och SNAT-utmattning.
- Ändra inte TCP-stängningsrelaterade timervärden på operativsystemnivå utan expertkunskaper om påverkan. TCP-stacken återställs, men programmets prestanda kan påverkas negativt när slutpunkterna för en anslutning inte matchar förväntningarna. Att vilja ändra timers är vanligtvis ett tecken på ett underliggande designproblem. Granska följande rekommendationer.


I exemplet ovan uppdateras regeln så att den endast tillåter inkommande extern trafik *MY_EXTERNAL_IP_RANGE* intervallet. Om du *ersätter MY_EXTERNAL_IP_RANGE* ip-adressen för det interna undernätet begränsas trafiken till endast interna IP-adresser i klustret. Detta tillåter inte att klienter utanför Ditt Kubernetes-kluster får åtkomst till lastbalanseraren.

## <a name="moving-from-a-basic-sku-load-balancer-to-standard-sku"></a>Flytta från en grundläggande SKU-lastbalanserare till standard-SKU

Om du har ett befintligt kluster med Basic SKU Load Balancer finns det viktiga beteendeskillnader att observera när du migrerar till ett kluster med standard-SKU:n Load Balancer.

Det är till exempel vanligt att göra blå/gröna distributioner för att migrera kluster, eftersom typen av kluster bara kan definieras när `load-balancer-sku` klustret skapas. Dock använder *Basic SKU* Load Balancers Grundläggande *SKU* IP-adresser, som inte är kompatibla med *Standard SKU* Load Balancers eftersom de kräver IP-adresser av *standard-SKU:n.* När du migrerar kluster för att uppgradera Load Balancer SKU:er krävs en ny IP-adress med en kompatibel IP-adress-SKU.

Mer information om hur du migrerar kluster finns i vår dokumentation om migreringsöverväganden för att visa en lista över viktiga ämnen att tänka på när du migrerar. [](aks-migration.md) Begränsningarna nedan är också viktiga beteendeskillnader att observera när du använder standard-SKU-lastbalanserare i AKS.

## <a name="limitations"></a>Begränsningar

Följande begränsningar gäller när du skapar och hanterar AKS-kluster  som stöder en lastbalanserare med standard-SKU:n:

* Minst en offentlig IP- eller IP-prefix krävs för att tillåta utgående trafik från AKS-klustret. Den offentliga IP-adressen eller IP-prefixet krävs också för att upprätthålla anslutningen mellan kontrollplanet och agentnoderna och för att upprätthålla kompatibilitet med tidigare versioner av AKS. Du har följande alternativ för att ange offentliga  IP-adresser eller IP-prefix med en standard-SKU-lastbalanserare:
    * Ange dina egna offentliga IP-adresser.
    * Ange dina egna offentliga IP-prefix.
    * Ange ett tal på upp till 100 så att AKS-klustret kan skapa så många offentliga IP-adresser för Standard-SKU:er i samma resursgrupp som skapas som AKS-klustret, som vanligtvis namnges *med MC_* i början.  AKS tilldelar den offentliga  IP-adressen till standard-SKU-lastbalanserare. Som standard skapas en offentlig IP-adress automatiskt i samma resursgrupp som AKS-klustret, om ingen offentlig IP-adress, offentligt IP-prefix eller antal IP-adresser har angetts. Du måste också tillåta offentliga adresser och undvika att skapa Azure Policy som förbjuder skapande av IP-adresser.
* En offentlig IP-adress som skapats av AKS kan inte återanvändas som en anpassad bring your own public IP-adress. Alla anpassade IP-adresser måste skapas och hanteras av användaren.
* Du kan bara definiera lastbalanserings-SKU:n när du skapar ett AKS-kluster. Du kan inte ändra lastbalanserings-SKU:n när ett AKS-kluster har skapats.
* Du kan bara använda en typ av SKU för lastbalanserare (Basic eller Standard) i ett enda kluster.
* *Standard* SKU-lastbalanserare stöder endast *IP-adresser av standardklass.*


## <a name="next-steps"></a>Nästa steg

Läs mer om Kubernetes-tjänster i [kubernetes services-dokumentationen.][kubernetes-services]

Läs mer om att använda internal Load Balancer för inkommande trafik i [dokumentationen för interna AKS-Load Balancer.](internal-lb.md)

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
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-group-create]: /cli/azure/group#az_group_create
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-network-lb-outbound-rule-list]: /cli/azure/network/lb/outbound-rule#az_network_lb_outbound_rule_list
[az-network-public-ip-show]: /cli/azure/network/public-ip#az_network_public_ip_show
[az-network-public-ip-prefix-show]: /cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_show
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
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
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[requirements]: #requirements-for-customizing-allocated-outbound-ports-and-idle-timeout
[use-multiple-node-pools]: use-multiple-node-pools.md
[troubleshoot-snat]: #troubleshooting-snat
[service-tags]: ../virtual-network/network-security-groups-overview.md#service-tags
