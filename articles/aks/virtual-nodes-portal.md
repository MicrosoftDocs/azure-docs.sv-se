---
title: Skapa virtuella noder med hjälp av portalen i Azure Kubernetes Services (AKS)
description: Lär dig hur du använder Azure Portal för att skapa ett Azure Kubernetes Services-kluster (AKS) som använder virtuella noder för att köra poddar.
services: container-service
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: references_regions
ms.openlocfilehash: bfb9e4a9a7c788255ea7fcba0fb42404829f82f3
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379357"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-in-the-azure-portal"></a>Skapa och konfigurera ett AKS-kluster (Azure Kubernetes Services) för att använda virtuella noder i Azure Portal

Den här artikeln visar hur du använder Azure Portal för att skapa och konfigurera de virtuella nätverksresurserna och ett AKS-kluster med virtuella noder aktiverade.

> [!NOTE]
> [Den här](virtual-nodes.md) artikeln ger dig en översikt över regionens tillgänglighet och begränsningar med hjälp av virtuella noder.

## <a name="before-you-begin"></a>Innan du börjar

Virtuella noder möjliggör nätverkskommunikation mellan poddar som körs i Azure Container Instances (ACI) och AKS-klustret. För att tillhandahålla den här kommunikationen skapas ett undernät för virtuellt nätverk och delegerade behörigheter tilldelas. Virtuella noder fungerar bara med AKS-kluster som skapats med *hjälp av avancerade* nätverk (Azure CNI). Som standard skapas AKS-kluster med *grundläggande nätverk* (kubenet). Den här artikeln visar hur du skapar ett virtuellt nätverk och undernät och sedan distribuerar ett AKS-kluster som använder avancerade nätverk.

Om du inte har använt ACI tidigare registrerar du tjänstleverantören i din prenumeration. Du kan kontrollera statusen för ACI-providerregistreringen med kommandot [az provider list,][az-provider-list] som du ser i följande exempel:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

*Microsoft.ContainerInstance-providern* ska rapportera som *Registrerad*, enligt följande exempelutdata:

```output
Namespace                    RegistrationState    RegistrationPolicy
---------------------------  -------------------  --------------------
Microsoft.ContainerInstance  Registered           RegistrationRequired
```

Om providern visas som *NotRegistered* registrerar du providern med hjälp av [az provider register][az-provider-register] enligt följande exempel:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-an-aks-cluster"></a>Skapa ett AKS-kluster

Välj Skapa en resurs   >  **Kubernetes** Service Azure Portal det övre vänstra hörnet på sidan.

På sidan **Grunder** konfigurerar du följande alternativ:

- *PROJEKTINFORMATION*: Välj en Azure-prenumeration och välj sedan eller skapa en Azure-resursgrupp, till exempel *myResourceGroup*. Ange ett **Kubernetes-klusternamn**, till exempel *myAKSCluster*.
- *KLUSTERINFORMATION:* Välj en region och en Kubernetes-version för AKS-klustret.
- *PRIMÄR NODPOOL:* Välj en VM-storlek för AKS-noderna. VM-storleken **kan inte** ändras efter att ett AKS-kluster har distribuerats.
     - Välj även det antal noder som ska distribueras till klustret. I den här artikeln anger du **Antal noder** till *1*. Antalet noder **kan** justeras efter att klustret har distribuerats.

Klicka **på Nästa: Nodpooler.**

På sidan **Nodpooler** väljer du *Aktivera virtuella noder.*

:::image type="content" source="media/virtual-nodes-portal/enable-virtual-nodes.png" alt-text="I en webbläsare visas hur du skapar ett kluster med virtuella noder aktiverade på Azure Portal. Alternativet &quot;Aktivera virtuella noder&quot; är markerat.":::

Som standard skapas en klusteridentitet. Den här klusteridentiteten används för klusterkommunikation och integrering med andra Azure-tjänster. Som standard är den här klusteridentiteten en hanterad identitet. Mer information finns i Använda [hanterade identiteter.](use-managed-identity.md) Du kan också använda tjänstens huvudnamn som klusteridentitet.

Klustret är också konfigurerat för avancerade nätverk. De virtuella noderna är konfigurerade för att använda sina egna virtuella Azure-nätverksundernät. Det här undernätet har delegerad behörighet att ansluta Azure-resurser mellan AKS-klustret. Om du inte redan har delegerat undernät skapar och konfigurerar Azure Portal det virtuella Azure-nätverket och undernätet för användning med de virtuella noderna.

Välj **Granska + skapa**. När verifieringen är klar väljer du **Skapa**.

Det tar några minuter för AKS-klustret att skapas och bli redo för användning.

## <a name="connect-to-the-cluster"></a>Anslut till klustret

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Hantera Kubernetes-kluster med [kubectl][kubectl], Kubernetes kommandoradsklient. `kubectl`-klienten är förinstallerad i Azure Cloud Shell.

Om du vill Cloud Shell väljer **du Prova i** det övre högra hörnet av ett kodblock. Du kan också starta Cloud Shell på en separat webbläsarflik genom att gå till [https://shell.azure.com/bash](https://shell.azure.com/bash) . Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.

Använd kommandot [az aks get-credentials][az-aks-get-credentials] för att konfigurera `kubectl` till att ansluta till ditt Kubernetes-kluster. I följande exempel hämtas autentiseringsuppgifterna för klusternamnet *myAKSCluster* i den resursgrupp som heter *myResourceGroup*:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Du kan kontrollera anslutningen till klustret genom att köra kommandot [kubectl get][kubectl-get] för att returnera en lista över klusternoderna.

```console
kubectl get nodes
```

Följande exempelutdata visar den enskilda VM-noden som skapades och sedan den virtuella noden för Linux, *virtual-node-aci-linux:*

```output
NAME                           STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux         Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0       Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Distribuera en exempelapp

I Azure Cloud Shell skapar du en fil med `virtual-node.yaml` namnet och kopierar följande YAML. För att schemalägga containern på noden [definieras en nodeSelector][node-selector] [och][toleration] tolerans. Med de här inställningarna kan podden schemaläggas på den virtuella noden och bekräfta att funktionen har aktiverats.

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

Kör programmet med [kommandot kubectl apply.][kubectl-apply]

```azurecli-interactive
kubectl apply -f virtual-node.yaml
```

Använd kommandot [kubectl get pods][kubectl-get] med argumentet för att `-o wide` mata ut en lista över poddar och den schemalagda noden. Observera att `virtual-node-helloworld` podden har schemalagts på `virtual-node-linux` noden.

```console
kubectl get pods -o wide
```

```output
NAME                                     READY     STATUS    RESTARTS   AGE       IP           NODE
virtual-node-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

Podden tilldelas en intern IP-adress från det virtuella Azure-nätverkets undernät som delegerats för användning med virtuella noder.

> [!NOTE]
> Om du använder avbildningar som lagras Azure Container Registry [konfigurerar och använder du en Kubernetes-hemlighet.][acr-aks-secrets] En aktuell begränsning för virtuella noder är att du inte kan använda integrerad autentisering med Azure AD-tjänstens huvudnamn. Om du inte använder en hemlighet kan poddar som schemalagts på virtuella noder inte starta och rapportera felet `HTTP response status code 400 error code "InaccessibleImage"` .

## <a name="test-the-virtual-node-pod"></a>Testa den virtuella nodpodden

Om du vill testa podden som körs på den virtuella noden bläddrar du till demoprogrammet med en webbklient. När podden tilldelas en intern IP-adress kan du snabbt testa anslutningen från en annan podd i AKS-klustret. Skapa en testpodd och koppla en terminalsession till den:

```console
kubectl run -it --rm virtual-node-test --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
```

Installera `curl` i podden med `apt-get` :

```console
apt-get update && apt-get install -y curl
```

Nu kan du komma åt adressen för podden `curl` med hjälp av , till exempel *http://10.241.0.4* . Ange din egen interna IP-adress som visas i föregående `kubectl get pods` kommando:

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

## <a name="next-steps"></a>Nästa steg

I den här artikeln har en podd schemalagts på den virtuella noden och tilldelats en privat, intern IP-adress. Du kan i stället skapa en tjänstdistribution och dirigera trafik till din podd via en lastbalanserare eller ingress-kontrollant. Mer information finns i Skapa [en grundläggande ingress-kontrollant i AKS][aks-basic-ingress].

Virtuella noder är en komponent i en skalningslösning i AKS. Mer information om skalningslösningar finns i följande artiklar:

- [Använda horisontell autoskalning av poddar i Kubernetes][aks-hpa]
- [Använda Autoskalning av Kubernetes-kluster][aks-cluster-autoscaler]
- [Kolla in autoskalningsexempel för virtuella noder][virtual-node-autoscale]
- [Läs mer om Virtual Kubelet-biblioteket med öppen källkod][virtual-kubelet-repo]

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
