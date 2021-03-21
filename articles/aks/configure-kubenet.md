---
title: Konfigurera Kubernetes-nätverk i Azure Kubernetes service (AKS)
description: Lär dig hur du konfigurerar Kubernetes-nätverk (Basic) i Azure Kubernetes service (AKS) för att distribuera ett AKS-kluster till ett befintligt virtuellt nätverk och undernät.
services: container-service
ms.topic: article
ms.date: 06/02/2020
ms.reviewer: nieberts, jomore
ms.openlocfilehash: a32b06163f446fe0df7f1f1581d741d889cf8e9f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104583521"
---
# <a name="use-kubenet-networking-with-your-own-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Använda Kubernetes-nätverk med dina egna IP-adressintervall i Azure Kubernetes service (AKS)

Som standard använder AKS-kluster [Kubernetes][kubenet]och ett virtuellt Azure-nätverk och undernät skapas åt dig. Med *Kubernetes* hämtar noder en IP-adress från det virtuella nätverkets undernät i Azure. Poddar får en IP-adress från ett annat logiskt adressutrymme än det virtuella Azure-nätverkets undernät för noderna. NAT (Network Address Translation) konfigureras sedan så att poddarna kan komma åt resurser i det virtuella Azure-nätverket. Käll-IP-adressen för trafiken är NAT till nodens primära IP-adress. Den här metoden minskar antalet IP-adresser som du behöver reservera i ditt nätverks utrymme för att poddar ska kunna användas.

Med [Azure Container Network Interface (cni)][cni-networking]hämtar varje Pod en IP-adress från under nätet och kan nås direkt. De här IP-adresserna måste vara unika i ditt nätverks utrymme och måste planeras i förväg. Varje nod har en konfigurations parameter för det maximala antalet poddar som stöds. Motsvarande antal IP-adresser per nod är sedan reserverade för den noden. Den här metoden kräver mer planering, och leder ofta till IP-prisslutning eller behovet av att återskapa kluster i ett större undernät när ditt program kräver en tillväxt. Du kan konfigurera den maximala poddar som kan distribueras till en nod vid klustrets skapande tid eller när du skapar nya nodkonfigurationer. Om du inte anger maxPods när du skapar nya Node-pooler får du ett standardvärde på 110 för Kubernetes.

Den här artikeln visar hur du använder *Kubernetes* -nätverk för att skapa och använda ett virtuellt nätverks under nät för ett AKS-kluster. Mer information om nätverks alternativ och överväganden finns i [nätverks koncept för Kubernetes och AKS][aks-network-concepts].

## <a name="prerequisites"></a>Förutsättningar

* Det virtuella nätverket för AKS-klustret måste tillåta utgående Internet anslutning.
* Skapa inte fler än ett AKS-kluster i samma undernät.
* AKS-kluster får inte använda `169.254.0.0/16` , `172.30.0.0/16` , `172.31.0.0/16` eller `192.0.2.0/24` för Kubernetes-tjänstens adress intervall, Pod-adressintervall eller adress intervall för virtuella kluster nätverk.
* Den kluster identitet som används av AKS-klustret måste ha minst rollen [nätverks deltagare](../role-based-access-control/built-in-roles.md#network-contributor) i under nätet i det virtuella nätverket. Du måste också ha rätt behörigheter, till exempel prenumerations ägaren, för att skapa en kluster identitet och tilldela den behörighet. Om du vill definiera en [anpassad roll](../role-based-access-control/custom-roles.md) i stället för att använda den inbyggda rollen nätverks deltagare, krävs följande behörigheter:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

> [!WARNING]
> Om du vill använda pooler för Windows Server-noder måste du använda Azure-CNI. Användningen av Kubernetes som nätverks modell är inte tillgänglig för Windows Server-behållare.

## <a name="before-you-begin"></a>Innan du börjar

Du behöver Azure CLI-versionen 2.0.65 eller senare installerad och konfigurerad. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

## <a name="overview-of-kubenet-networking-with-your-own-subnet"></a>Översikt över Kubernetes-nätverk med ditt eget undernät

I många miljöer har du definierat virtuella nätverk och undernät med allokerade IP-adressintervall. Dessa virtuella nätverks resurser används för att stödja flera tjänster och program. För att tillhandahålla nätverks anslutning kan AKS-kluster använda *Kubernetes* (grundläggande nätverk) eller Azure cni (*avancerade nätverk*).

Med *Kubernetes* får bara noderna en IP-adress i det virtuella nätverkets undernät. Poddar kan inte kommunicera direkt med varandra. I stället används UDR (User Defined routing) och IP-vidarebefordring för anslutning mellan poddar över noder. Som standard skapas och underhålls UDR och underhålls konfigurationen av AKS-tjänsten, men du måste välja att [ta med din egen routningstabell för anpassad väg hantering][byo-subnet-route-table]. Du kan också distribuera poddar bakom en tjänst som tar emot en tilldelad IP-adress och belastnings Utjämnings trafik för programmet. Följande diagram visar hur AKS-noder får en IP-adress i det virtuella nätverkets undernät, men inte poddar:

![Kubernetes nätverks modell med ett AKS-kluster](media/use-kubenet/kubenet-overview.png)

Azure har stöd för högst 400 vägar i en UDR, så du kan inte ha ett AKS-kluster som är större än 400 noder. AKS [virtuella noder][virtual-nodes] och Azure Network policies stöds inte med *Kubernetes*.  Du kan använda [Calico nätverks principer][calico-network-policies]eftersom de stöds med Kubernetes.

Med *Azure cni* får varje Pod en IP-adress i IP-undernätet och kan kommunicera direkt med andra poddar och tjänster. Klustren kan vara så stora som det IP-adressintervall som du anger. IP-adressintervallet måste dock planeras i förväg, och alla IP-adresser används av AKS-noderna baserat på det maximala antalet poddar som de kan stödja. Avancerade nätverksfunktioner och scenarier som [virtuella noder][virtual-nodes] eller nätverks principer (antingen Azure eller Calico) stöds med *Azure cni*.

### <a name="limitations--considerations-for-kubenet"></a>Begränsningar & överväganden för Kubernetes

* Det krävs ytterligare hopp vid utformningen av Kubernetes, vilket ger mindre svars tid till Pod-kommunikation.
* Routningstabeller och användardefinierade vägar krävs för att använda Kubernetes, vilket ökar komplexiteten för åtgärder.
* Direkt Pod-adressering stöds inte för Kubernetes på grund av Kubernetes design.
* Till skillnad från Azure CNI-kluster kan flera Kubernetes-kluster inte dela ett undernät.
* Funktioner som **inte stöds i Kubernetes** är:
   * Nätverks [principer i Azure](use-network-policies.md#create-an-aks-cluster-and-enable-network-policy), men Calico nätverks principer stöds på Kubernetes
   * [Windows-noder i pooler](./windows-faq.md)
   * [Tillägg för virtuella noder](virtual-nodes.md#network-requirements)

### <a name="ip-address-availability-and-exhaustion"></a>Tillgänglighet och utbelastning för IP-adress

Med *Azure cni* är ett vanligt problem att det tilldelade IP-adressintervallet är för litet för att lägga till ytterligare noder när du skalar eller uppgraderar ett kluster. Nätverks teamet kanske inte heller kan utfärda ett stort tillräckligt med IP-adressintervall för att stödja förväntade program krav.

Som en kompromiss kan du skapa ett AKS-kluster som använder *Kubernetes* och ansluter till ett befintligt undernät för virtuella nätverk. Med den här metoden kan noderna ta emot definierade IP-adresser, utan att behöva reservera ett stort antal IP-adresser fram för alla potentiella poddar som kan köras i klustret.

Med *Kubernetes* kan du använda ett mycket mindre IP-adressintervall och kunna stödja stora kluster och program krav. Till exempel, även med ett */27* IP-adressintervall i ditt undernät, kan du köra ett 20-25-nods-kluster med tillräckligt utrymme för att skala eller uppgradera. Den här kluster storleken har stöd för upp till *2200-2750* poddar (med ett standardvärde på högst 110 poddar per nod). Det maximala antalet poddar per nod som du kan konfigurera med *Kubernetes* i AKS är 110.

Följande grundläggande beräkningar Jämför skillnaden i nätverks modeller:

- **Kubernetes** – ett enkelt */24* -adressintervall har stöd för upp till *251* noder i klustret (varje Azure Virtual Network-undernät reserverar de första tre IP-adresserna för hanterings åtgärder)
  - Antalet noder kan stödja upp till *27 610* poddar (med ett standardvärde som är högst 110 poddar per nod med *Kubernetes*)
- **Azure-cni** – samma bas *-/24* -undernät kan bara stödja högst *8* noder i klustret
  - Antalet noder kunde bara stödja upp till *240* poddar (med ett standardvärde som är högst 30 poddar per nod med *Azure cni*)

> [!NOTE]
> Dessa maximum tar inte hänsyn till uppgradering eller skalnings åtgärder. I praktiken kan du inte köra det maximala antalet noder som under nätets IP-adressintervall stöder. Du måste lämna vissa IP-adresser tillgängliga för användning under skalningen av uppgraderings åtgärder.

### <a name="virtual-network-peering-and-expressroute-connections"></a>Peering-och ExpressRoute-anslutningar för virtuella nätverk

För att tillhandahålla lokal anslutning kan både *Kubernetes* -och *Azure-cni-* nätverks metoder använda peering-eller ExpressRoute- [anslutningar][express-route]i [Azure Virtual Network][vnet-peering] . Planera IP-adressintervall noggrant för att förhindra överlappande och felaktig trafik dirigering. Många lokala nätverk använder till exempel ett *10.0.0.0/8* -adressintervall som annonseras via ExpressRoute-anslutningen. Vi rekommenderar att du skapar dina AKS-kluster i Azure Virtual Network-undernät utanför det här adress intervallet, till exempel *172.16.0.0/16*.

### <a name="choose-a-network-model-to-use"></a>Välj en nätverks modell som ska användas

Valet av vilken nätverks-plugin som ska användas för AKS-klustret är vanligt vis ett balans mellan flexibilitet och avancerade konfigurations behov. Följande överväganden är hjälp disposition när varje nätverks modell kan vara den lämpligaste.

Använd *Kubernetes* när:

- Du har begränsat IP-adressutrymmet.
- De flesta av Pod-kommunikationen är i klustret.
- Du behöver inte avancerade AKS-funktioner som virtuella noder eller Azure Network Policy.  Använd [Calico nätverks principer][calico-network-policies].

Använd *Azure-cni* när:

- Du har tillgängliga IP-adressutrymme.
- De flesta av Pod-kommunikationen är till resurser utanför klustret.
- Du vill inte hantera användardefinierade vägar för Pod-anslutning.
- Du behöver AKS avancerade funktioner som virtuella noder eller Azure Network Policy.  Använd [Calico nätverks principer][calico-network-policies].

Mer information som hjälper dig att avgöra vilken nätverks modell som ska användas finns i [jämföra nätverks modeller och deras support omfång][network-comparisons].

## <a name="create-a-virtual-network-and-subnet"></a>Skapa ett virtuellt nätverk och ett undernät

Kom igång med att använda *Kubernetes* och ditt eget virtuella nätverks under nät genom att först skapa en resurs grupp med kommandot [AZ Group Create][az-group-create] . I följande exempel skapas en resurs grupp med namnet *myResourceGroup* på platsen för *öster* :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Om du inte har ett befintligt virtuellt nätverk och undernät att använda skapar du dessa nätverks resurser med hjälp av kommandot [AZ Network VNet Create][az-network-vnet-create] . I följande exempel heter det virtuella nätverket *myVnet* med adressprefixet *192.168.0.0/16*. Ett undernät skapas med namnet *myAKSSubnet* med adressprefixet *192.168.1.0/24*.

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myAKSVnet \
    --address-prefixes 192.168.0.0/16 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-a-service-principal-and-assign-permissions"></a>Skapa ett huvud namn för tjänsten och tilldela behörigheter

Om ett AKS-kluster ska kunna interagera med andra Azure-resurser behövs ett huvudnamn för tjänsten i Azure Active Directory. Tjänstens huvud namn måste ha behörighet att hantera det virtuella nätverk och undernät som AKS-noderna använder. Om du vill skapa ett huvud namn för tjänsten använder du kommandot [AZ AD SP Create-for-RBAC][az-ad-sp-create-for-rbac] :

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

Följande exempel på utdata visar programmets ID och lösen ord för tjänstens huvud namn. Dessa värden används i ytterligare steg för att tilldela en roll till tjänstens huvud namn och sedan skapa AKS-klustret:

```output
{
  "appId": "476b3636-5eda-4c0e-9751-849e70b5cfad",
  "displayName": "azure-cli-2019-01-09-22-29-24",
  "name": "http://azure-cli-2019-01-09-22-29-24",
  "password": "a1024cd7-af7b-469f-8fd7-b293ecbb174e",
  "tenant": "72f998bf-85f1-41cf-92ab-2e7cd014db46"
}
```

Om du vill tilldela rätt delegering i de återstående stegen använder du [AZ Network VNet show][az-network-vnet-show] och [AZ Network VNet Subnet show][az-network-vnet-subnet-show] commands för att hämta de resurs-ID: n som krävs. Dessa resurs-ID: n lagras som variabler och refereras till i de återstående stegen:

```azurecli-interactive
VNET_ID=$(az network vnet show --resource-group myResourceGroup --name myAKSVnet --query id -o tsv)
SUBNET_ID=$(az network vnet subnet show --resource-group myResourceGroup --vnet-name myAKSVnet --name myAKSSubnet --query id -o tsv)
```

Tilldela nu tjänstens huvud namn till AKS för klustrets *nätverks deltagare* i det virtuella nätverket med hjälp av kommandot [AZ roll tilldelning skapa][az-role-assignment-create] . *\<appId>* Skapa tjänstens huvud namn genom att ange det som visas i utdata från föregående kommando:

```azurecli-interactive
az role assignment create --assignee <appId> --scope $VNET_ID --role "Network Contributor"
```

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>Skapa ett AKS-kluster i det virtuella nätverket

Nu har du skapat ett virtuellt nätverk och undernät och skapat och tilldelat behörigheter för ett huvud namn för tjänsten för att använda dessa nätverks resurser. Skapa nu ett AKS-kluster i ditt virtuella nätverk och under nätet med kommandot [AZ AKS Create][az-aks-create] . Definiera ditt eget huvud namn *\<appId>* för tjänsten och *\<password>* se resultatet från föregående kommando för att skapa tjänstens huvud namn.

Följande IP-adressintervall definieras också som en del av klustret Create process:

* *--Service-CIDR* används för att tilldela interna tjänster i AKS-klustret en IP-adress. Detta IP-adressintervall ska vara ett adress utrymme som inte används någon annan stans i din nätverks miljö, inklusive alla lokala nätverks intervall om du ansluter eller planerar att ansluta till dina virtuella Azure-nätverk med hjälp av Express Route eller en VPN-anslutning från plats till plats.

* IP-adressen för *--DNS-service-IP* måste vara *.10* -adressen till tjänstens IP-adressintervall.

* *--Pod-CIDR* bör vara ett stort adress utrymme som inte används någon annan stans i din nätverks miljö. Det här intervallet omfattar alla lokala nätverks intervall om du ansluter eller planerar att ansluta, dina virtuella Azure-nätverk med Express Route eller en VPN-anslutning från plats till plats.
    * Adress intervallet måste vara tillräckligt stort för att rymma antalet noder som du förväntar dig att skala upp till. Du kan inte ändra det här adress intervallet när klustret har distribuerats om du behöver fler adresser för ytterligare noder.
    * IP-adressintervallet Pod används för att tilldela ett */24* -adressutrymme till varje nod i klustret. I följande exempel tilldelar *--Pod-CIDR-* *10.244.0.0/16* den första noden *10.244.0.0/24*, den andra noden *10.244.1.0/24* och den tredje noden *10.244.2.0/24*.
    * När klustret skalas eller uppgraderas fortsätter Azure-plattformen att tilldela ett Pod IP-adressintervall till varje ny nod.
    
* *--Docker-Bridge-Address* låter AKS-noderna kommunicera med den underliggande hanterings plattformen. Den här IP-adressen får inte ligga inom det virtuella nätverkets IP-adressintervall och får inte överlappa andra adress intervall som används i nätverket.

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
> Om du vill aktivera ett AKS-kluster för att inkludera en [Calico-nätverks princip][calico-network-policies] kan du använda följande kommando.

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

När du skapar ett AKS-kluster skapas automatiskt en nätverks säkerhets grupp och en routningstabell. Dessa nätverks resurser hanteras av AKS-kontroll planet. Nätverks säkerhets gruppen associeras automatiskt med de virtuella nätverkskorten på noderna. Routningstabellen associeras automatiskt med det virtuella nätverkets undernät. Regler för nätverks säkerhets grupper och routningstabeller uppdateras automatiskt när du skapar och exponerar tjänster.

## <a name="bring-your-own-subnet-and-route-table-with-kubenet"></a>Ta med ditt eget undernät och routningstabellen med Kubernetes

I Kubernetes måste det finnas en routningstabell i dina kluster under nät (er). AKS stöder att du använder ditt eget befintliga undernät och routningstabellen.

Om det anpassade under nätet inte innehåller någon routningstabell, skapar AKS ett för dig och lägger till regler till den under hela kluster livs cykeln. Om ditt anpassade undernät innehåller en routningstabell när du skapar klustret, bekräftar AKS den befintliga routningstabellen under kluster åtgärder och lägger till/uppdaterar regler för moln leverantörs åtgärder.

> [!WARNING]
> Anpassade regler kan läggas till i tabellen för anpassade vägar och uppdateras. Regler läggs dock till av Kubernetes Cloud-providern som inte får uppdateras eller tas bort. Regler som 0.0.0.0/0 måste alltid finnas i en specifik routningstabell och mappas till målet för din Internet-gateway, till exempel en NVA eller andra utgående Gateway. Var försiktig när du uppdaterar regler som bara de anpassade reglerna ändras i.

Lär dig mer om att skapa en [anpassad routningstabell][custom-route-table].

Kubernetes nätverk kräver strukturerade väg tabell regler för att kunna dirigera begär Anden. På grund av den här designen måste routningstabeller upprätthållas noggrant för varje kluster som förlitar sig på den. Flera kluster kan inte dela en routningstabell eftersom Pod CIDR-fel från olika kluster kan överlappa, vilket orsakar oväntad och bruten routning. Se till att följande begränsningar beaktas när du konfigurerar flera kluster i samma virtuella nätverk eller dedicera ett virtuellt nätverk till varje kluster.

Begränsningar:

* Behörigheter måste tilldelas innan klustret skapas, se till att du använder ett huvud namn för tjänsten med Skriv behörighet för ditt anpassade undernät och en anpassad routningstabell.
* En anpassad routningstabell måste vara kopplad till under nätet innan du skapar AKS-klustret.
* Den tillhör ande väg tabell resursen kan inte uppdateras efter att klustret har skapats. Medan väg tabell resursen inte kan uppdateras kan anpassade regler ändras i routningstabellen.
* Varje AKS-kluster måste använda en enda, unik routningstabell för alla undernät som är associerade med klustret. Du kan inte återanvända en routningstabell med flera kluster på grund av risken för överlappande Pod CIDR och motstridiga regler för routning.

När du har skapat en anpassad routningstabell och associerat den till ditt undernät i ditt virtuella nätverk kan du skapa ett nytt AKS-kluster som använder routningstabellen.
Du måste använda under nätets ID för var du planerar att distribuera ditt AKS-kluster. Det här under nätet måste också vara kopplat till din anpassade routningstabell.

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

Med ett AKS-kluster som distribueras i ditt befintliga undernät för virtuella nätverk kan du nu använda klustret som vanligt. Kom igång med att [skapa nya appar med Helm][develop-helm] eller [distribuera befintliga appar med hjälp av Helm][use-helm].

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet
[Calico-network-policies]: https://docs.projectcalico.org/v3.9/security/calico-network-policy

<!-- LINKS - Internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-network-concepts]: concepts-network.md
[az-group-create]: /cli/azure/group#az-group-create
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-network-vnet-show]: /cli/azure/network/vnet#az-network-vnet-show
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[byo-subnet-route-table]: #bring-your-own-subnet-and-route-table-with-kubenet
[develop-helm]: quickstart-helm.md
[use-helm]: kubernetes-helm.md
[virtual-nodes]: virtual-nodes-cli.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[express-route]: ../expressroute/expressroute-introduction.md
[network-comparisons]: concepts-network.md#compare-network-models
[custom-route-table]: ../virtual-network/manage-route-table.md
