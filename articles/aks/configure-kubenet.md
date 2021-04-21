---
title: Konfigurera kubenet-nätverk i Azure Kubernetes Service (AKS)
description: Lär dig hur du konfigurerar kubenet-nätverk (grundläggande) i Azure Kubernetes Service (AKS) för att distribuera ett AKS-kluster till ett befintligt virtuellt nätverk och undernät.
services: container-service
ms.topic: article
ms.date: 06/02/2020
ms.reviewer: nieberts, jomore
ms.openlocfilehash: c373e45c8607f10c36f40a23c776bd081bf13207
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789527"
---
# <a name="use-kubenet-networking-with-your-own-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Använda kubenet-nätverk med dina egna IP-adressintervall i Azure Kubernetes Service (AKS)

Som standard använder AKS-kluster [kubenet][kubenet], och ett virtuellt Azure-nätverk och undernät skapas åt dig. Med *kubenet* hämtar noder en IP-adress från det virtuella Azure-nätverkets undernät. Poddar får en IP-adress från ett annat logiskt adressutrymme än det virtuella Azure-nätverkets undernät för noderna. NAT (Network Address Translation) konfigureras sedan så att poddarna kan komma åt resurser i det virtuella Azure-nätverket. Källans IP-adress för trafiken är NAT till nodens primära IP-adress. Den här metoden minskar avsevärt det antal IP-adresser som du behöver reservera i ditt nätverksutrymme för poddar att använda.

Med [Azure Container Networking Interface (CNI)][cni-networking]får varje podd en IP-adress från undernätet och kan nås direkt. Dessa IP-adresser måste vara unika i nätverket och måste planeras i förväg. Varje nod har en konfigurationsparameter för det maximala antalet poddar som stöds. Motsvarande antal IP-adresser per nod reserveras sedan direkt för den noden. Den här metoden kräver mer planering och leder ofta till att IP-adresserna tar slut eller att kluster måste återskapas i ett större undernät när dina programbehov ökar. Du kan konfigurera det maximala antalet poddar som kan distribueras till en nod när klustret skapas eller när du skapar nya nodpooler. Om du inte anger maxPods när du skapar nya nodpooler får du standardvärdet 110 för kubenet.

Den här artikeln visar hur du använder *kubenet-nätverk* för att skapa och använda ett virtuellt nätverksundernät för ett AKS-kluster. Mer information om nätverksalternativ och nätverksöverväganden finns [i Nätverksbegrepp för Kubernetes och AKS.][aks-network-concepts]

## <a name="prerequisites"></a>Förutsättningar

* Det virtuella nätverket för AKS-klustret måste tillåta utgående Internetanslutning.
* Skapa inte fler än ett AKS-kluster i samma undernät.
* AKS-kluster får inte använda , , eller för `169.254.0.0/16` `172.30.0.0/16` `172.31.0.0/16` `192.0.2.0/24` Kubernetes-tjänstens adressintervall, poddadressintervall eller adressintervall för virtuellt klusternätverk.
* Klusteridentiteten som används av AKS-klustret måste minst ha rollen [Nätverksdeltagare](../role-based-access-control/built-in-roles.md#network-contributor) i undernätet i det virtuella nätverket. Du måste också ha rätt behörigheter, till exempel prenumerationsägaren, för att skapa en klusteridentitet och tilldela den behörigheter. Om du vill definiera en [anpassad roll i](../role-based-access-control/custom-roles.md) stället för att använda den inbyggda rollen Nätverksdeltagare krävs följande behörigheter:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

> [!WARNING]
> Om du vill använda Windows Server-nodpooler måste du använda Azure CNI. Användningen av kubenet som nätverksmodell är inte tillgänglig för Windows Server-containrar.

## <a name="before-you-begin"></a>Innan du börjar

Azure CLI version 2.0.65 eller senare måste vara installerat och konfigurerat. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

## <a name="overview-of-kubenet-networking-with-your-own-subnet"></a>Översikt över kubenet-nätverk med ditt eget undernät

I många miljöer har du definierat virtuella nätverk och undernät med allokerade IP-adressintervall. Dessa virtuella nätverksresurser används för att stödja flera tjänster och program. För att tillhandahålla nätverksanslutning kan AKS-kluster använda *kubenet* (grundläggande nätverk) eller Azure CNI (*avancerat nätverk).*

Med *kubenet* får endast noderna en IP-adress i det virtuella nätverkets undernät. Poddar kan inte kommunicera direkt med varandra. I stället används användardefinierad routning (UDR) och IP-vidarekoppling för anslutning mellan poddar mellan noder. Som standard skapas och underhålls konfiguration av UDR:er och IP-vidarebefordran av AKS-tjänsten, men du måste välja att ta med din egen vägtabell för [anpassad väghantering.][byo-subnet-route-table] Du kan också distribuera poddar bakom en tjänst som tar emot en tilldelad IP-adress och belastningsutjämnar trafik för programmet. Följande diagram visar hur AKS-noderna tar emot en IP-adress i det virtuella nätverkets undernät, men inte poddarna:

![Kubenet-nätverksmodell med ett AKS-kluster](media/use-kubenet/kubenet-overview.png)

Azure stöder högst 400 vägar i en UDR, så du kan inte ha ett AKS-kluster som är större än 400 noder. Virtuella [AKS-noder][virtual-nodes] och Azure-nätverksprinciper stöds inte med *kubenet*.  Du kan [användaCoCo-nätverksprinciper][calico-network-policies]eftersom de stöds med kubenet.

Med *Azure CNI* får varje podd en IP-adress i IP-undernätet och kan kommunicera direkt med andra poddar och tjänster. Dina kluster kan vara så stora som det IP-adressintervall som du anger. IP-adressintervallet måste dock planeras i förväg och alla IP-adresser används av AKS-noderna baserat på det maximala antalet poddar som de har stöd för. Avancerade nätverksfunktioner och scenarier som [virtuella noder eller][virtual-nodes] nätverksprinciper (antingen Azure ellerOfco) stöds med hjälp *Azure CNI*.

### <a name="limitations--considerations-for-kubenet"></a>Begränsningar & överväganden för kubenet

* Ett ytterligare hopp krävs i utformningen av kubenet, som lägger till mindre fördröjning i poddkommunikationen.
* Vägtabeller och användardefinierade vägar krävs för att använda kubenet, vilket ökar komplexiteten i åtgärder.
* Direkt poddadressering stöds inte för kubenet på grund av kubenet-designen.
* Till Azure CNI kluster kan flera kubenet-kluster inte dela ett undernät.
* Funktioner **som inte stöds på kubenet** är:
   * [Azure-nätverksprinciper](use-network-policies.md#create-an-aks-cluster-and-enable-network-policy), men Principerna för Nätverksco stöds på kubenet
   * [Windows-nodpooler](./windows-faq.md)
   * [Tillägg för virtuella noder](virtual-nodes.md#network-requirements)

### <a name="ip-address-availability-and-exhaustion"></a>Tillgänglighet och slut på IP-adresser

Med *Azure CNI* är ett vanligt problem att det tilldelade IP-adressintervallet är för litet för att sedan lägga till ytterligare noder när du skalar eller uppgraderar ett kluster. Nätverksteamet kanske inte heller kan utfärda ett tillräckligt stort IP-adressintervall för att stödja dina förväntade programkrav.

Som en kompromiss kan du skapa ett AKS-kluster som använder *kubenet* och ansluta till ett befintligt undernät för virtuellt nätverk. Med den här metoden kan noderna ta emot definierade IP-adresser utan att behöva reservera ett stort antal IP-adresser direkt för alla potentiella poddar som kan köras i klustret.

Med *kubenet* kan du använda ett mycket mindre IP-adressintervall och stödja stora kluster och programkrav. Till exempel kan du köra ett 20–25-nodkluster med tillräckligt med utrymme för skalning eller uppgradering även med ett */27* IP-adressintervall i undernätet. Den här klusterstorleken stöder upp *till 2 200–2 750* poddar (med högst 110 poddar per nod som standard). Det maximala antalet poddar per nod som du kan konfigurera med *kubenet* i AKS är 110.

I följande grundläggande beräkningar jämförs skillnaden i nätverksmodeller:

- **kubenet** – ett enkelt */24* IP-adressintervall har stöd för upp till *251* noder i klustret (varje virtuellt Azure-nätverksundernät reserverar de första tre IP-adresserna för hanteringsåtgärder)
  - Det här antalet noder kan stödja *upp till 27 610* poddar (med ett standardvärde på högst 110 poddar per nod *med kubenet*)
- **Azure CNI** – samma grundläggande */24-undernätsintervall* kunde bara stödja *högst 8* noder i klustret
  - Det här antalet noder kunde bara stödja *upp till 240* poddar (med högst 30 poddar per nod som standard med *Azure CNI*)

> [!NOTE]
> Dessa maxbelopp tar inte hänsyn till uppgraderings- eller skalningsåtgärder. I praktiken kan du inte köra det maximala antalet noder som stöds av undernätets IP-adressintervall. Du måste lämna vissa IP-adresser tillgängliga för användning under skalning av uppgraderingsåtgärder.

### <a name="virtual-network-peering-and-expressroute-connections"></a>Peering för virtuella nätverk och ExpressRoute-anslutningar

För att tillhandahålla lokal anslutning kan både *nätverksmetoderna kubenet* och *Azure-CNI* använda [peering][vnet-peering] för virtuella Azure-nätverk eller [ExpressRoute-anslutningar.][express-route] Planera dina IP-adressintervall noggrant för att förhindra överlappning och felaktig trafikroutning. Till exempel använder många lokala nätverk ett *adressintervall på 10.0.0.0/8* som annonseras via ExpressRoute-anslutningen. Vi rekommenderar att du skapar dina AKS-kluster i undernät för virtuella Azure-nätverk utanför det här adressintervallet, till exempel *172.16.0.0/16.*

### <a name="choose-a-network-model-to-use"></a>Välj en nätverksmodell som ska användas

Valet av vilket nätverks-plugin-program som ska användas för ditt AKS-kluster är vanligtvis en balans mellan flexibilitet och avancerade konfigurationsbehov. Följande överväganden hjälper dig att beskriva när varje nätverksmodell kan vara den lämpligaste.

Använd *kubenet* när:

- Du har begränsat IP-adressutrymme.
- Merparten av poddkommunikationen finns i klustret.
- Du behöver inte avancerade AKS-funktioner som virtuella noder eller Azure-nätverksprincip.  [AnvändCo-nätverksprinciper][calico-network-policies].

Använd *Azure CNI* när:

- Du har tillgängligt IP-adressutrymme.
- Merparten av poddkommunikationen är till resurser utanför klustret.
- Du vill inte hantera användardefinierade vägar för poddanslutning.
- Du behöver AKS avancerade funktioner, till exempel virtuella noder eller Azure-nätverksprincip.  [AnvändCo-nätverksprinciper][calico-network-policies].

Mer information som hjälper dig att avgöra vilken nätverksmodell som ska användas finns i [Jämför nätverksmodeller och deras supportomfång.][network-comparisons]

## <a name="create-a-virtual-network-and-subnet"></a>Skapa ett virtuellt nätverk och ett undernät

För att komma igång med *att använda kubenet* och ditt eget virtuella nätverksundernät måste du först skapa en resursgrupp med [kommandot az group create.][az-group-create] I följande exempel skapas en resursgrupp med *namnet myResourceGroup* på *platsen eastus:*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Om du inte har något befintligt virtuellt nätverk och undernät att använda skapar du dessa nätverksresurser med [kommandot az network vnet create.][az-network-vnet-create] I följande exempel heter det virtuella nätverket *myVnet* med adressprefixet *192.168.0.0/16*. Ett undernät skapas med namnet *myAKSSubnet* med adressprefixet *192.168.1.0/24.*

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myAKSVnet \
    --address-prefixes 192.168.0.0/16 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-a-service-principal-and-assign-permissions"></a>Skapa ett huvudnamn för tjänsten och tilldela behörigheter

Om ett AKS-kluster ska kunna interagera med andra Azure-resurser behövs ett huvudnamn för tjänsten i Azure Active Directory. Tjänstens huvudnamn måste ha behörighet att hantera det virtuella nätverk och undernät som AKS-noderna använder. Om du vill skapa ett huvudnamn för tjänsten använder [du kommandot az ad sp create-for-rbac:][az-ad-sp-create-for-rbac]

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

Följande exempelutdata visar program-ID och lösenord för tjänstens huvudnamn. Dessa värden används i ytterligare steg för att tilldela en roll till tjänstens huvudnamn och sedan skapa AKS-klustret:

```output
{
  "appId": "476b3636-5eda-4c0e-9751-849e70b5cfad",
  "displayName": "azure-cli-2019-01-09-22-29-24",
  "name": "http://azure-cli-2019-01-09-22-29-24",
  "password": "a1024cd7-af7b-469f-8fd7-b293ecbb174e",
  "tenant": "72f998bf-85f1-41cf-92ab-2e7cd014db46"
}
```

Om du vill tilldela rätt delegeringar i de återstående stegen använder du kommandona [az network vnet show][az-network-vnet-show] och az network [vnet subnet show][az-network-vnet-subnet-show] för att hämta nödvändiga resurs-IP-nummer. Dessa resurs-ID lagras som variabler och refereras i de återstående stegen:

```azurecli-interactive
VNET_ID=$(az network vnet show --resource-group myResourceGroup --name myAKSVnet --query id -o tsv)
SUBNET_ID=$(az network vnet subnet show --resource-group myResourceGroup --vnet-name myAKSVnet --name myAKSSubnet --query id -o tsv)
```

Tilldela nu tjänstens huvudnamn  för dina behörigheter för AKS-klusternätverksdeltagare i det virtuella nätverket med hjälp av [kommandot az role assignment create.][az-role-assignment-create] Ange dina egna *\<appId>* enligt utdata från föregående kommando för att skapa tjänstens huvudnamn:

```azurecli-interactive
az role assignment create --assignee <appId> --scope $VNET_ID --role "Network Contributor"
```

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>Skapa ett AKS-kluster i det virtuella nätverket

Nu har du skapat ett virtuellt nätverk och undernät och skapat och tilldelat behörigheter för ett tjänsthuvudnamn för att använda dessa nätverksresurser. Skapa nu ett AKS-kluster i ditt virtuella nätverk och undernät med kommandot [az aks create.][az-aks-create] Definiera ditt eget huvudnamn *\<appId>* för tjänsten och , som du ser i *\<password>* utdata från föregående kommando för att skapa tjänstens huvudnamn.

Följande IP-adressintervall definieras också som en del av processen för att skapa kluster:

* *--service-cidr används* för att tilldela interna tjänster i AKS-klustret en IP-adress. Det här IP-adressintervallet bör vara ett adressutrymme som inte används någon annanstans i nätverksmiljön, inklusive lokala nätverksintervall om du ansluter eller planerar att ansluta dina virtuella Azure-nätverk med Express Route eller en VPN-anslutning från plats till plats.

* *--dns-service-ip-adressen* ska vara *0,10-adressen* för tjänstens IP-adressintervall.

* *--pod-cidr* bör vara ett stort adressutrymme som inte används någon annanstans i nätverksmiljön. Det här intervallet innehåller alla lokala nätverksintervall om du ansluter, eller planerar att ansluta, dina virtuella Azure-nätverk med Express Route eller en VPN-anslutning från plats till plats.
    * Det här adressintervallet måste vara tillräckligt stort för att hantera det antal noder som du förväntar dig att skala upp till. Du kan inte ändra det här adressintervallet när klustret har distribuerats om du behöver fler adresser för ytterligare noder.
    * Poddens IP-adressintervall används för att tilldela varje nod i klustret ett */24-adressutrymme.* I följande exempel tilldelar *--pod-cidr* *10.244.0.0/16* den första noden *10.244.0.0/24*, den andra noden *10.244.1.0/24* och den tredje noden *10.244.2.0/24*.
    * När klustret skalas eller uppgraderas fortsätter Azure-plattformen att tilldela varje ny nod ett podd-IP-adressintervall.
    
* Med *--docker-bridge-address kan AKS-noderna* kommunicera med den underliggande hanteringsplattformen. Den här IP-adressen får inte vara inom klustrets IP-adressintervall för det virtuella nätverket och får inte överlappa andra adressintervall som används i nätverket.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 3 \
    --network-plugin kubenet \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --pod-cidr 10.244.0.0/16 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal <appId> \
    --client-secret <password>
```

> [!Note]
> Om du vill aktivera ett AKS-kluster för att inkludera en [Nätverksprincip förCo Kan][calico-network-policies] du använda följande kommando.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 3 \
    --network-plugin kubenet --network-policy calico \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --pod-cidr 10.244.0.0/16 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal <appId> \
    --client-secret <password>
```

När du skapar ett AKS-kluster skapas automatiskt en nätverkssäkerhetsgrupp och en vägtabell. Dessa nätverksresurser hanteras av AKS-kontrollplanet. Nätverkssäkerhetsgruppen associeras automatiskt med de virtuella nätverkskorten på noderna. Vägtabellen associeras automatiskt med det virtuella nätverkets undernät. Regler för nätverkssäkerhetsgrupp och vägtabeller uppdateras automatiskt när du skapar och exponerar tjänster.

## <a name="bring-your-own-subnet-and-route-table-with-kubenet"></a>Ta med ditt eget undernät och din vägtabell med kubenet

Med kubenet måste det finnas en vägtabell i klustrets undernät. AKS har stöd för att ta med ditt eget befintliga undernät och din egen vägtabell.

Om ditt anpassade undernät inte innehåller en vägtabell skapar AKS en åt dig och lägger till regler i det under klustrets livscykel. Om ditt anpassade undernät innehåller en vägtabell när du skapar klustret bekräftar AKS den befintliga vägtabellen under klusteråtgärder och lägger till/uppdaterar regler för molnleverantörsåtgärder.

> [!WARNING]
> Anpassade regler kan läggas till i den anpassade vägtabellen och uppdateras. Regler läggs dock till av Kubernetes-molnleverantören som inte får uppdateras eller tas bort. Regler som 0.0.0.0/0 måste alltid finnas i en viss vägtabell och mappas till målet för din Internetgateway, till exempel en NVA eller en annan utgående gateway. Var försiktig när du uppdaterar regler som endast dina anpassade regler ändras.

Läs mer om att konfigurera en [anpassad vägtabell.][custom-route-table]

Kubenet-nätverk kräver organiserade regler för att dirigera begäranden. På grund av den här designen måste flödestabeller underhållas noggrant för varje kluster som förlitar sig på den. Flera kluster kan inte dela en routningstabell eftersom podd-CIDR från olika kluster kan överlappa, vilket leder till oväntad och bruten routning. När du konfigurerar flera kluster i samma virtuella nätverk eller dedikerar ett virtuellt nätverk för varje kluster, se till att följande begränsningar beaktas.

Begränsningar:

* Behörigheter måste tilldelas innan klustret skapas. Se till att du använder ett huvudnamn för tjänsten med skrivbehörighet till ditt anpassade undernät och din anpassade vägtabell.
* En anpassad vägtabell måste associeras med undernätet innan du skapar AKS-klustret.
* Den associerade resurs för vägtabellen kan inte uppdateras när klustret har skapats. Det går inte att uppdatera resursen för vägtabellen, men anpassade regler kan ändras i vägtabellen.
* Varje AKS-kluster måste använda en enda unik vägtabell för alla undernät som är associerade med klustret. Du kan inte återanvända en routningstabell med flera kluster på grund av risken för överlappande podd-CIDR och motstridiga routningsregler.

När du har skapat en anpassad vägtabell och associerat den med ditt undernät i det virtuella nätverket kan du skapa ett nytt AKS-kluster som använder din vägtabell.
Du måste använda undernäts-ID:t för den plats där du planerar att distribuera ditt AKS-kluster. Det här undernätet måste också associeras med din anpassade vägtabell.

```azurecli-interactive
# Find your subnet ID
az network vnet subnet list --resource-group
                            --vnet-name
                            [--subscription]
```

```azurecli-interactive
# Create a kubernetes cluster with with a custom subnet preconfigured with a route table
az aks create -g MyResourceGroup -n MyManagedCluster --vnet-subnet-id MySubnetID
```

## <a name="next-steps"></a>Nästa steg

Med ett AKS-kluster distribuerat till ditt befintliga virtuella nätverksundernät kan du nu använda klustret som vanligt. Kom igång med att [skapa nya appar med hjälp av Helm][develop-helm] eller distribuera befintliga appar med hjälp av [Helm][use-helm].

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet
[Calico-network-policies]: https://docs.projectcalico.org/v3.9/security/calico-network-policy

<!-- LINKS - Internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-network-concepts]: concepts-network.md
[az-group-create]: /cli/azure/group#az_group_create
[az-network-vnet-create]: /cli/azure/network/vnet#az_network_vnet_create
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-network-vnet-show]: /cli/azure/network/vnet#az_network_vnet_show
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet#az_network_vnet_subnet_show
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-aks-create]: /cli/azure/aks#az_aks_create
[byo-subnet-route-table]: #bring-your-own-subnet-and-route-table-with-kubenet
[develop-helm]: quickstart-helm.md
[use-helm]: kubernetes-helm.md
[virtual-nodes]: virtual-nodes-cli.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[express-route]: ../expressroute/expressroute-introduction.md
[network-comparisons]: concepts-network.md#compare-network-models
[custom-route-table]: ../virtual-network/manage-route-table.md
