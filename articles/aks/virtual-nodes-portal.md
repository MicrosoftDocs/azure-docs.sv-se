---
title: Skapa virtuella noder med portalen i Azure Kubernetes Services (AKS)
description: Lär dig hur du använder Azure Portal för att skapa ett AKS-kluster (Azure Kubernetes Services) som använder virtuella noder för att köra poddar.
services: container-service
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: c1ecaa88dd5329d86818565983a6ba891a6d8424
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104577841"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-in-the-azure-portal"></a>Skapa och konfigurera ett Azure Kubernetes Services-kluster (AKS) för att använda virtuella noder i Azure Portal

Den här artikeln visar hur du använder Azure Portal för att skapa och konfigurera virtuella nätverks resurser och ett AKS-kluster med virtuella noder aktiverade.

> [!NOTE]
> [Den här artikeln](virtual-nodes.md) ger en översikt över regionens tillgänglighet och begränsningar med hjälp av virtuella noder.

## <a name="before-you-begin"></a>Innan du börjar

Virtuella noder möjliggör nätverkskommunikation mellan poddar som körs i Azure Container Instances (ACI) och AKS-klustret. För att tillhandahålla den här kommunikationen skapas ett virtuellt nätverks under nät och delegerade behörigheter tilldelas. Virtuella noder fungerar bara med AKS-kluster som skapats med *Advanced* Networking (Azure cni). Som standard skapas AKS-kluster med *Basic* Networking (Kubernetes). Den här artikeln visar hur du skapar ett virtuellt nätverk och undernät och sedan distribuerar ett AKS-kluster som använder avancerade nätverk.

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

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-an-aks-cluster"></a>Skapa ett AKS-kluster

I det övre vänstra hörnet av Azure Portal väljer du **skapa en resurs**  >  **Kubernetes-tjänst**.

På sidan **Grunder** konfigurerar du följande alternativ:

- *PROJEKTINFORMATION*: Välj en Azure-prenumeration och välj sedan eller skapa en Azure-resursgrupp, till exempel *myResourceGroup*. Ange ett **Kubernetes-klusternamn**, till exempel *myAKSCluster*.
- *Kluster information*: Välj en region och en Kubernetes-version för AKS-klustret.
- *Primär Node-pool*: Välj en VM-storlek för AKS-noderna. VM-storleken **kan inte** ändras efter att ett AKS-kluster har distribuerats.
     - Välj även det antal noder som ska distribueras till klustret. I den här artikeln ställer du in **antal noder** på *1*. Antalet noder **kan** justeras efter att klustret har distribuerats.

Klicka på **Nästa: Node-pooler**.

På sidan **Node-pooler** väljer du *Aktivera virtuella noder*.

:::image type="content" source="media/virtual-nodes-portal/enable-virtual-nodes.png" alt-text="I en webbläsare visas hur du skapar ett kluster med virtuella noder aktiverade på Azure Portal. Alternativet Aktivera virtuella noder är markerat.":::

Som standard skapas en kluster identitet. Den här kluster identiteten används för kluster kommunikation och integrering med andra Azure-tjänster. Som standard är den här kluster identiteten en hanterad identitet. Mer information finns i [använda hanterade identiteter](use-managed-identity.md). Du kan också använda ett tjänst huvud namn som kluster identitet.

Klustret är också konfigurerat för avancerade nätverk. De virtuella noderna har kon figurer ATS för att använda sina egna Azure Virtual Network-undernät. Det här under nätet har delegerade behörigheter för att ansluta Azure-resurser mellan AKS-klustret. Om du inte redan har ett delegerat undernät skapar och konfigurerar Azure Portal det virtuella Azure-nätverket och under nätet för användning med de virtuella noderna.

Välj **Granska + skapa**. När verifieringen är klar väljer du **skapa**.

Det tar några minuter för AKS-klustret att skapas och bli redo för användning.

## <a name="connect-to-the-cluster"></a>Anslut till klustret

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Hantera Kubernetes-kluster med [kubectl][kubectl], Kubernetes kommandoradsklient. `kubectl`-klienten är förinstallerad i Azure Cloud Shell.

Om du vill öppna Cloud Shell väljer du **testa den** från det övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell på en separat webbläsare-flik genom att gå till [https://shell.azure.com/bash](https://shell.azure.com/bash) . Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.

Använd kommandot [az aks get-credentials][az-aks-get-credentials] för att konfigurera `kubectl` till att ansluta till ditt Kubernetes-kluster. I följande exempel hämtas autentiseringsuppgifterna för klusternamnet *myAKSCluster* i den resursgrupp som heter *myResourceGroup*:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Du kan kontrollera anslutningen till klustret genom att köra kommandot [kubectl get][kubectl-get] för att returnera en lista över klusternoderna.

```console
kubectl get nodes
```

Följande exempel på utdata visar att den enskilda VM-noden har skapats och sedan den virtuella noden för Linux, *virtuell-Node-ACI-Linux*:

```output
NAME                           STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux         Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0       Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Distribuera en exempel App

I Azure Cloud Shell skapar du en fil med namnet `virtual-node.yaml` och kopierar i följande yaml. Om du vill schemalägga behållaren på noden definieras [en][node-selector] avsökning och [tolererande][toleration] . Med de här inställningarna kan Pod schemaläggas på den virtuella noden och bekräfta att funktionen har Aktiver ATS.

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
```

Kör programmet med kommandot [kubectl Apply][kubectl-apply] .

```azurecli-interactive
kubectl apply -f virtual-node.yaml
```

Använd kommandot [kubectl get poddar][kubectl-get] med `-o wide` argumentet för att mata ut en lista över poddar och den schemalagda noden. Observera att `virtual-node-helloworld` Pod har schemalagts på `virtual-node-linux` noden.

```console
kubectl get pods -o wide
```

```output
NAME                                     READY     STATUS    RESTARTS   AGE       IP           NODE
virtual-node-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

Pod tilldelas en intern IP-adress från Azure Virtual Network-undernätet delegerad för användning med virtuella noder.

> [!NOTE]
> Om du använder avbildningar som lagras i Azure Container Registry [konfigurerar och använder du en Kubernetes-hemlighet][acr-aks-secrets]. En aktuell begränsning av virtuella noder är att du inte kan använda integrerad autentisering för Azure AD-tjänstens huvud namn. Om du inte använder en hemlighet kan poddar schemalagda på virtuella noder inte starta och rapportera felet `HTTP response status code 400 error code "InaccessibleImage"` .

## <a name="test-the-virtual-node-pod"></a>Testa den virtuella noden Pod

Om du vill testa Pod som körs på den virtuella noden bläddrar du till demonstrations programmet med en webb klient. När Pod tilldelas en intern IP-adress kan du snabbt testa den här anslutningen från en annan Pod i AKS-klustret. Skapa en test-Pod och koppla en terminalsession till den:

```console
kubectl run -it --rm virtual-node-test --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
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

## <a name="next-steps"></a>Nästa steg

I den här artikeln har en POD schemalagts på den virtuella noden och tilldelats en privat, intern IP-adress. Du kan i stället skapa en tjänst distribution och dirigera trafik till din POD via en belastningsutjämnare eller ingångs kontroll. Mer information finns i [skapa en grundläggande ingress-styrenhet i AKS][aks-basic-ingress].

Virtuella noder är en komponent i en skalnings lösning i AKS. Mer information om skalnings lösningar finns i följande artiklar:

- [Använd Kubernetes horisontell Pod autoskalning][aks-hpa]
- [Använda Kubernetes-kluster autoskalning][aks-cluster-autoscaler]
- [Kolla in autoskalning-exemplet för virtuella noder][virtual-node-autoscale]
- [Läs mer om det virtuella Kubelet-biblioteket med öppen källkod][virtual-kubelet-repo]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[aks-github]: https://github.com/azure/aks/issues]
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet
[acr-aks-secrets]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/

<!-- LINKS - internal -->
[aks-network]: ./configure-azure-cni.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: cluster-autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[az-provider-list]: /cli/azure/provider#az-provider-list
[az-provider-register]: /cli/azure/provider#az-provider-register
