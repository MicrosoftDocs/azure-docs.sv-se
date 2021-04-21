---
title: Skapa virtuella noder med Azure CLI
titleSuffix: Azure Kubernetes Service
description: Lär dig hur du använder Azure CLI för att skapa ett Azure Kubernetes Services-kluster (AKS) som använder virtuella noder för att köra poddar.
services: container-service
ms.topic: conceptual
ms.date: 03/16/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 86f1d8923eea961471883c44168c4fa848ac12d1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769291"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-using-the-azure-cli"></a>Skapa och konfigurera ett Azure Kubernetes Services-kluster (AKS) för att använda virtuella noder med hjälp av Azure CLI

Den här artikeln visar hur du använder Azure CLI för att skapa och konfigurera de virtuella nätverksresurserna och AKS-klustret och sedan aktivera virtuella noder.


## <a name="before-you-begin"></a>Innan du börjar

Virtuella noder möjliggör nätverkskommunikation mellan poddar som körs i Azure Container Instances (ACI) och AKS-klustret. För att tillhandahålla den här kommunikationen skapas ett undernät för virtuellt nätverk och delegerade behörigheter tilldelas. Virtuella noder fungerar bara med AKS-kluster som skapats med *hjälp av avancerade* nätverk (Azure CNI). Som standard skapas AKS-kluster med *grundläggande nätverk* (kubenet). Den här artikeln visar hur du skapar ett virtuellt nätverk och undernät och sedan distribuerar ett AKS-kluster som använder avancerade nätverk.

> [!IMPORTANT]
> Innan du använder virtuella noder med AKS bör du granska både begränsningarna för [virtuella AKS-noder][virtual-nodes-aks] och begränsningarna [för virtuella nätverk i ACI][virtual-nodes-networking-aci]. Dessa begränsningar påverkar plats, nätverkskonfiguration och annan konfigurationsinformation för både ditt AKS-kluster och de virtuella noderna.

Om du inte har använt ACI tidigare registrerar du tjänstleverantören i din prenumeration. Du kan kontrollera statusen för ACI-providerregistreringen med kommandot [az provider list,][az-provider-list] som du ser i följande exempel:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

*Microsoft.ContainerInstance-providern* bör rapportera som *Registrerad*, enligt följande exempelutdata:

```output
Namespace                    RegistrationState    RegistrationPolicy
---------------------------  -------------------  --------------------
Microsoft.ContainerInstance  Registered           RegistrationRequired
```

Om providern visas som *NotRegistered* registrerar du providern med hjälp av [az provider register][az-provider-register] enligt följande exempel:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto.

Om du vill Cloud Shell väljer du **Prova från** det övre högra hörnet av ett kodblock. Du kan också starta Cloud Shell på en separat webbläsarflik genom att gå till [https://shell.azure.com/bash](https://shell.azure.com/bash) . Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.

Om du föredrar att installera och använda CLI lokalt kräver den här artikeln Azure CLI version 2.0.49 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk grupp där Azure-resurser distribueras och hanteras. Skapa en resursgrupp med kommandot [az group create][az-group-create]. I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *westus*.

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Skapa ett virtuellt nätverk med [kommandot az network vnet][az-network-vnet-create] create. I följande exempel skapas ett virtuellt nätverksnamn *myVnet* med adressprefixet *10.0.0.0/8* och ett undernät med namnet *myAKSSubnet*. Adressprefixet för det här undernätet är som standard *10.240.0.0/16*:

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16
```

Skapa nu ytterligare ett undernät för virtuella noder med kommandot [az network vnet subnet create.][az-network-vnet-subnet-create] I följande exempel skapas ett undernät med namnet *myVirtualNodeSubnet* med adressprefixet *10.241.0.0/16*.

```azurecli-interactive
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name myVirtualNodeSubnet \
    --address-prefixes 10.241.0.0/16
```

## <a name="create-a-service-principal-or-use-a-managed-identity"></a>Skapa ett huvudnamn för tjänsten eller använda en hanterad identitet

För att låta ett AKS-kluster interagera med andra Azure-resurser används en klusteridentitet. Den här klusteridentiteten kan skapas automatiskt av Azure CLI eller portalen, eller så kan du skapa en i förväg och tilldela ytterligare behörigheter. Som standard är den här klusteridentiteten en hanterad identitet. Mer information finns i Använda [hanterade identiteter.](use-managed-identity.md) Du kan också använda tjänstens huvudnamn som klusteridentitet. Följande steg visar hur du manuellt skapar och tilldelar tjänstens huvudnamn till klustret.

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

Om du vill tillåta att klustret använder och hanterar det virtuella nätverket måste du ge AKS-tjänstens huvudnamn rätt behörighet att använda nätverksresurserna.

Hämta först resurs-ID:t för det virtuella nätverket [med az network vnet show][az-network-vnet-show]:

```azurecli-interactive
az network vnet show --resource-group myResourceGroup --name myVnet --query id -o tsv
```

Om du vill ge rätt åtkomst för AKS-klustret att använda det virtuella nätverket skapar du en rolltilldelning med [kommandot az role assignment create.][az-role-assignment-create] Byt ut `<appId`> och `<vnetId>` mot värdena du antecknade i föregående två steg.

```azurecli-interactive
az role assignment create --assignee <appId> --scope <vnetId> --role Contributor
```

## <a name="create-an-aks-cluster"></a>Skapa ett AKS-kluster

Du distribuerar ett AKS-kluster till det AKS-undernät som skapades i ett tidigare steg. Hämta ID:t för det här undernätet [med az network vnet subnet show][az-network-vnet-subnet-show]:

```azurecli-interactive
az network vnet subnet show --resource-group myResourceGroup --vnet-name myVnet --name myAKSSubnet --query id -o tsv
```

Använd kommandot [az aks create][az-aks-create] för att skapa ett AKS-kluster. I följande exempel skapas ett kluster med namnet *myAKSCluster* och en enda nod. Ersätt `<subnetId>` med ID:t som hämtades i föregående steg och `<appId>` sedan och med värdena som `<password>` samlades in i föregående avsnitt.

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

Om du vill aktivera virtuella noder använder du [nu kommandot az aks enable-addons.][az-aks-enable-addons] I följande exempel används undernätet *myVirtualNodeSubnet som* skapades i ett tidigare steg:

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

Följande exempelutdata visar den enskilda VM-noden som skapades och sedan den virtuella noden för Linux, *virtual-node-aci-linux:*

```output
NAME                          STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux        Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0      Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Distribuera en exempelapp

Skapa en fil med `virtual-node.yaml` namnet och kopiera följande YAML. För att schemalägga containern på noden [definieras en nodeSelector][node-selector] [och][toleration] tolerans.

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

Kör programmet med [kommandot kubectl apply.][kubectl-apply]

```console
kubectl apply -f virtual-node.yaml
```

Använd [kommandot kubectl get pods][kubectl-get] med argumentet för att `-o wide` mata ut en lista över poddar och den schemalagda noden. Observera att `aci-helloworld` podden har schemalagts på `virtual-node-aci-linux` noden.

```console
kubectl get pods -o wide
```

```output
NAME                            READY     STATUS    RESTARTS   AGE       IP           NODE
aci-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

Podden tilldelas en intern IP-adress från det virtuella Azure-nätverkets undernät som delegerats för användning med virtuella noder.

> [!NOTE]
> Om du använder avbildningar som lagras Azure Container Registry [konfigurerar och använder du en Kubernetes-hemlighet.][acr-aks-secrets] En aktuell begränsning för virtuella noder är att du inte kan använda integrerad autentisering med Azure AD-tjänstens huvudnamn. Om du inte använder en hemlighet kan poddar som schemalagts på virtuella noder inte starta och rapportera felet `HTTP response status code 400 error code "InaccessibleImage"` .

## <a name="test-the-virtual-node-pod"></a>Testa den virtuella nodpodden

Om du vill testa podden som körs på den virtuella noden bläddrar du till demoprogrammet med en webbklient. När podden tilldelas en intern IP-adress kan du snabbt testa anslutningen från en annan podd i AKS-klustret. Skapa en testpodd och koppla en terminalsession till den:

```console
kubectl run -it --rm testvk --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
```

Installera `curl` i podden med `apt-get` :

```console
apt-get update && apt-get install -y curl
```

Nu får du åtkomst till adressen för din podd `curl` med , till exempel *http://10.241.0.4* . Ange din egen interna IP-adress som visas i föregående `kubectl get pods` kommando:

```console
curl -L http://10.241.0.4
```

Demoprogrammet visas, som du ser i följande komprimerade exempelutdata:

```output
<html>
<head>
  <title>Welcome to Azure Container Instances!</title>
</head>
[...]
```

Stäng terminalsessionen till testpodden med `exit` . När sessionen är slut tas podden bort.

## <a name="remove-virtual-nodes"></a>Ta bort virtuella noder

Om du inte längre vill använda virtuella noder kan du inaktivera dem med kommandot [az aks disable-addons.][az aks disable-addons] 

Om det behövs går du [https://shell.azure.com](https://shell.azure.com) till för att Azure Cloud Shell i webbläsaren.

Ta först bort `aci-helloworld` podden som körs på den virtuella noden:

```console
kubectl delete -f virtual-node.yaml
```

Följande exempelkommando inaktiverar de virtuella Linux-noderna:

```azurecli-interactive
az aks disable-addons --resource-group myResourceGroup --name myAKSCluster --addons virtual-node
```

Ta nu bort de virtuella nätverksresurserna och resursgruppen:

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

I den här artikeln har en podd schemalagts på den virtuella noden och tilldelats en privat, intern IP-adress. Du kan i stället skapa en tjänstdistribution och dirigera trafik till din podd via en lastbalanserare eller ingress-kontrollant. Mer information finns i Skapa [en grundläggande ingress-kontrollant i AKS][aks-basic-ingress].

Virtuella noder är ofta en komponent i en skalningslösning i AKS. Mer information om skalningslösningar finns i följande artiklar:

- [Använda Kubernetes horizontal pod autoscaler][aks-hpa]
- [Använda Autoskalning av Kubernetes-kluster][aks-cluster-autoscaler]
- [Kolla in autoskalningsexempel för virtuella noder][virtual-node-autoscale]
- [Läs mer om Virtual Kubelet-biblioteket med öppen källkod][virtual-kubelet-repo]

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
[az-group-create]: /cli/azure/group#az_group_create
[az-network-vnet-create]: /cli/azure/network/vnet#az_network_vnet_create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az_network_vnet_subnet_create
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-network-vnet-show]: /cli/azure/network/vnet#az_network_vnet_show
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet#az_network_vnet_subnet_show
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-enable-addons]: /cli/azure/aks#az_aks_enable_addons
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az aks disable-addons]: /cli/azure/aks#az_aks_disable_addons
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: ./cluster-autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[az-provider-list]: /cli/azure/provider#az_provider_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[virtual-nodes-aks]: virtual-nodes.md
[virtual-nodes-networking-aci]: ../container-instances/container-instances-virtual-network-concepts.md