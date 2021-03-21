---
title: Skapa virtuella noder med Azure CLI
titleSuffix: Azure Kubernetes Service
description: Lär dig hur du använder Azure CLI för att skapa ett AKS-kluster (Azure Kubernetes Services) som använder virtuella noder för att köra poddar.
services: container-service
ms.topic: conceptual
ms.date: 03/16/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 1c673cae41fcbd3d54aa9b4062dd030ace9f0767
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104577809"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-using-the-azure-cli"></a>Skapa och konfigurera ett Azure Kubernetes Services-kluster (AKS) för att använda virtuella noder med Azure CLI

Den här artikeln visar hur du använder Azure CLI för att skapa och konfigurera virtuella nätverks resurser och AKS-kluster och sedan Aktivera virtuella noder.


## <a name="before-you-begin"></a>Innan du börjar

Virtuella noder möjliggör nätverkskommunikation mellan poddar som körs i Azure Container Instances (ACI) och AKS-klustret. För att tillhandahålla den här kommunikationen skapas ett virtuellt nätverks under nät och delegerade behörigheter tilldelas. Virtuella noder fungerar bara med AKS-kluster som skapats med *Advanced* Networking (Azure cni). Som standard skapas AKS-kluster med *Basic* Networking (Kubernetes). Den här artikeln visar hur du skapar ett virtuellt nätverk och undernät och sedan distribuerar ett AKS-kluster som använder avancerade nätverk.

> [!IMPORTANT]
> Innan du använder virtuella noder med AKS granskar du båda [begränsningarna för virtuella AKS-noder][virtual-nodes-aks] och de [virtuella nätverks begränsningarna i Aci][virtual-nodes-networking-aci]. Dessa begränsningar påverkar plats, nätverks konfiguration och annan konfigurations information för både ditt AKS-kluster och de virtuella noderna.

Om du inte tidigare har använt ACI registrerar du tjänst leverantören med din prenumeration. Du kan kontrol lera statusen för registreringen av ACI-providern med kommandot [AZ Provider List][az-provider-list] , som du ser i följande exempel:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

*Microsoft. ContainerInstance* -providern ska rapportera som *registrerad*, vilket visas i följande exempel på utdata:

```output
Namespace                    RegistrationState    RegistrationPolicy
---------------------------  -------------------  --------------------
Microsoft.ContainerInstance  Registered           RegistrationRequired
```

Om providern visas som *NotRegistered* registrerar du providern med [AZ-providerns register][az-provider-register] som visas i följande exempel:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto.

Om du vill öppna Cloud Shell väljer du **testa den** från det övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell på en separat webbläsare-flik genom att gå till [https://shell.azure.com/bash](https://shell.azure.com/bash) . Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.

Om du föredrar att installera och använda CLI lokalt kräver den här artikeln Azure CLI version 2.0.49 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk grupp där Azure-resurser distribueras och hanteras. Skapa en resursgrupp med kommandot [az group create][az-group-create]. I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *westus*.

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Skapa ett virtuellt nätverk med hjälp av kommandot [AZ Network VNet Create][az-network-vnet-create] . I följande exempel skapas ett virtuellt nätverks namn *myVnet* med adressprefixet *10.0.0.0/8* och ett undernät med namnet *myAKSSubnet*. Adressprefixet för det här under nätet är som standard *10.240.0.0/16*:

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16
```

Skapa nu ytterligare ett undernät för virtuella noder med kommandot [AZ Network VNet Subnet Create][az-network-vnet-subnet-create] . I följande exempel skapas ett undernät med namnet *myVirtualNodeSubnet* med adressprefixet för *10.241.0.0/16*.

```azurecli-interactive
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name myVirtualNodeSubnet \
    --address-prefixes 10.241.0.0/16
```

## <a name="create-a-service-principal-or-use-a-managed-identity"></a>Skapa ett huvud namn för tjänsten eller Använd en hanterad identitet

Om du vill tillåta ett AKS-kluster att samverka med andra Azure-resurser används en kluster identitet. Den här kluster identiteten kan skapas automatiskt av Azure CLI eller portalen, eller så kan du skapa en ny och tilldela ytterligare behörigheter. Som standard är den här kluster identiteten en hanterad identitet. Mer information finns i [använda hanterade identiteter](use-managed-identity.md). Du kan också använda ett tjänst huvud namn som kluster identitet. Följande steg visar hur du manuellt skapar och tilldelar tjänstens huvud namn till klustret.

Skapa ett huvudnamn för tjänsten med kommandot [az ad sp create-for-rbac][az-ad-sp-create-for-rbac]. Parametern `--skip-assignment` gör att inga ytterligare behörigheterna tilldelas.

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

Utdata ser ut ungefär så här:

```output
{
  "appId": "bef76eb3-d743-4a97-9534-03e9388811fc",
  "displayName": "azure-cli-2018-11-21-18-42-00",
  "name": "http://azure-cli-2018-11-21-18-42-00",
  "password": "1d257915-8714-4ce7-a7fb-0e5a5411df7f",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

Anteckna värdena för *appId* och *password*. De här värdena används i senare steg.

## <a name="assign-permissions-to-the-virtual-network"></a>Tilldela behörigheter till det virtuella nätverket

Om du vill tillåta att klustret använder och hanterar det virtuella nätverket måste du bevilja AKS-tjänstens huvud namn rätt behörighet för att använda nätverks resurserna.

Börja med att hämta det virtuella nätverkets resurs-ID med [AZ Network VNet show][az-network-vnet-show]:

```azurecli-interactive
az network vnet show --resource-group myResourceGroup --name myVnet --query id -o tsv
```

För att ge rätt åtkomst för AKS-klustret att använda det virtuella nätverket skapar du en roll tilldelning med kommandot [AZ roll tilldelning skapa][az-role-assignment-create] . Byt ut `<appId`> och `<vnetId>` mot värdena du antecknade i föregående två steg.

```azurecli-interactive
az role assignment create --assignee <appId> --scope <vnetId> --role Contributor
```

## <a name="create-an-aks-cluster"></a>Skapa ett AKS-kluster

Du distribuerar ett AKS-kluster till AKS-undernätet som skapades i ett föregående steg. Hämta ID för under nätet med [AZ Network VNet Subnet show][az-network-vnet-subnet-show]:

```azurecli-interactive
az network vnet subnet show --resource-group myResourceGroup --vnet-name myVnet --name myAKSSubnet --query id -o tsv
```

Använd kommandot [az aks create][az-aks-create] för att skapa ett AKS-kluster. I följande exempel skapas ett kluster med namnet *myAKSCluster* och en enda nod. Ersätt `<subnetId>` med det ID som hämtades i föregående steg och sedan `<appId>` och `<password>` med de värden som samlats in i föregående avsnitt.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --network-plugin azure \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id <subnetId> \
    --service-principal <appId> \
    --client-secret <password>
```

Efter flera minuter slutförs kommandot och returnerar JSON-formaterad information om klustret.

## <a name="enable-virtual-nodes-addon"></a>Aktivera tillägg för virtuella noder

Om du vill aktivera virtuella noder använder du nu kommandot [AZ AKS Enable-addons][az-aks-enable-addons] . I följande exempel används under nätet med namnet *myVirtualNodeSubnet* som skapades i ett föregående steg:

```azurecli-interactive
az aks enable-addons \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --addons virtual-node \
    --subnet-name myVirtualNodeSubnet
```

## <a name="connect-to-the-cluster"></a>Anslut till klustret

För att konfigurera `kubectl` till att ansluta till ditt Kubernetes-kluster använder du kommandot [az aks get-credentials][az-aks-get-credentials]. I det här steget laddar vi ned autentiseringsuppgifter och konfigurerar Kubernetes CLI för att använda dem.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Du kan kontrollera anslutningen till klustret genom att köra kommandot [kubectl get][kubectl-get] för att returnera en lista över klusternoderna.

```console
kubectl get nodes
```

Följande exempel på utdata visar att den enskilda VM-noden har skapats och sedan den virtuella noden för Linux, *virtuell-Node-ACI-Linux*:

```output
NAME                          STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux        Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0      Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Distribuera en exempel App

Skapa en fil med namnet `virtual-node.yaml` och kopiera i följande yaml. Om du vill schemalägga behållaren på noden definieras [en][node-selector] avsökning och [tolererande][toleration] .

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aci-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aci-helloworld
  template:
    metadata:
      labels:
        app: aci-helloworld
    spec:
      containers:
      - name: aci-helloworld
        image: mcr.microsoft.com/azuredocs/aci-helloworld
        ports:
        - containerPort: 80
      nodeSelector:
        kubernetes.io/role: agent
        beta.kubernetes.io/os: linux
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Exists
      - key: azure.com/aci
        effect: NoSchedule
```

Kör programmet med kommandot [kubectl Apply][kubectl-apply] .

```console
kubectl apply -f virtual-node.yaml
```

Använd kommandot [kubectl get poddar][kubectl-get] med `-o wide` argumentet för att mata ut en lista över poddar och den schemalagda noden. Observera att `aci-helloworld` Pod har schemalagts på `virtual-node-aci-linux` noden.

```console
kubectl get pods -o wide
```

```output
NAME                            READY     STATUS    RESTARTS   AGE       IP           NODE
aci-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

Pod tilldelas en intern IP-adress från Azure Virtual Network-undernätet delegerad för användning med virtuella noder.

> [!NOTE]
> Om du använder avbildningar som lagras i Azure Container Registry [konfigurerar och använder du en Kubernetes-hemlighet][acr-aks-secrets]. En aktuell begränsning av virtuella noder är att du inte kan använda integrerad autentisering för Azure AD-tjänstens huvud namn. Om du inte använder en hemlighet kan poddar schemalagda på virtuella noder inte starta och rapportera felet `HTTP response status code 400 error code "InaccessibleImage"` .

## <a name="test-the-virtual-node-pod"></a>Testa den virtuella noden Pod

Om du vill testa Pod som körs på den virtuella noden bläddrar du till demonstrations programmet med en webb klient. När Pod tilldelas en intern IP-adress kan du snabbt testa den här anslutningen från en annan Pod i AKS-klustret. Skapa en test-Pod och koppla en terminalsession till den:

```console
kubectl run -it --rm testvk --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
```

Installera `curl` i pod med `apt-get` :

```console
apt-get update && apt-get install -y curl
```

Nu kan du få åtkomst till adressen till din POD med `curl` , till exempel *http://10.241.0.4* . Ange din egna interna IP-adress som visas i föregående `kubectl get pods` kommando:

```console
curl -L http://10.241.0.4
```

Demo programmet visas, som du ser i följande komprimerade exempel i utdata:

```output
<html>
<head>
  <title>Welcome to Azure Container Instances!</title>
</head>
[...]
```

Stäng terminalfönstret för test Pod med `exit` . När sessionen är slut tas Pod bort.

## <a name="remove-virtual-nodes"></a>Ta bort virtuella noder

Om du inte längre vill använda virtuella noder kan du inaktivera dem med kommandot [AZ AKS Disable-addons][az aks disable-addons] . 

Om det behövs går du till [https://shell.azure.com](https://shell.azure.com) för att öppna Azure Cloud Shell i webbläsaren.

Ta först bort `aci-helloworld` Pod som körs på den virtuella noden:

```console
kubectl delete -f virtual-node.yaml
```

Följande exempel kommando inaktiverar de virtuella Linux-noderna:

```azurecli-interactive
az aks disable-addons --resource-group myResourceGroup --name myAKSCluster --addons virtual-node
```

Ta nu bort de virtuella nätverks resurserna och resurs gruppen:

```azurecli-interactive
# Change the name of your resource group, cluster and network resources as needed
RES_GROUP=myResourceGroup
AKS_CLUSTER=myAKScluster
AKS_VNET=myVnet
AKS_SUBNET=myVirtualNodeSubnet

# Get AKS node resource group
NODE_RES_GROUP=$(az aks show --resource-group $RES_GROUP --name $AKS_CLUSTER --query nodeResourceGroup --output tsv)

# Get network profile ID
NETWORK_PROFILE_ID=$(az network profile list --resource-group $NODE_RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Delete the subnet delegation to Azure Container Instances
az network vnet subnet update --resource-group $RES_GROUP --vnet-name $AKS_VNET --name $AKS_SUBNET --remove delegations 0
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har en POD schemalagts på den virtuella noden och tilldelats en privat, intern IP-adress. Du kan i stället skapa en tjänst distribution och dirigera trafik till din POD via en belastningsutjämnare eller ingångs kontroll. Mer information finns i [skapa en grundläggande ingress-styrenhet i AKS][aks-basic-ingress].

Virtuella noder är ofta en komponent i en skalnings lösning i AKS. Mer information om skalnings lösningar finns i följande artiklar:

- [Använd Kubernetes horisontell Pod autoskalning][aks-hpa]
- [Använda Kubernetes-kluster autoskalning][aks-cluster-autoscaler]
- [Kolla in autoskalning-exemplet för virtuella noder][virtual-node-autoscale]
- [Läs mer om det virtuella Kubelet-biblioteket med öppen källkod][virtual-kubelet-repo]

<!-- LINKS - external -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[aks-github]: https://github.com/azure/aks/issues
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet
[acr-aks-secrets]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-create]: /cli/azure/group#az-group-create
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-network-vnet-show]: /cli/azure/network/vnet#az-network-vnet-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-show
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-enable-addons]: /cli/azure/aks#az-aks-enable-addons
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az aks disable-addons]: /cli/azure/aks#az-aks-disable-addons
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: ./cluster-autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[az-provider-list]: /cli/azure/provider#az-provider-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[virtual-nodes-aks]: virtual-nodes.md
[virtual-nodes-networking-aci]: ../container-instances/container-instances-virtual-network-concepts.md