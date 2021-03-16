---
title: 'Snabb start: Distribuera ett AKS-kluster med hjälp av Azure CLI'
description: Lär dig hur du snabbt kan skapa ett Kubernetes-kluster, distribuera ett program och övervaka prestanda i Azure Kubernetes Service (AKS) med hjälp av Azure CLI.
services: container-service
ms.topic: quickstart
ms.date: 02/26/2021
ms.custom:
- H1Hack27Feb2017
- mvc
- devcenter
- seo-javascript-september2019
- seo-javascript-october2019
- seo-python-october2019
- devx-track-azurecli
- contperf-fy21q1
ms.openlocfilehash: b3d6c7695f74c048cb03e3f4e7ae822005c81c06
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103492888"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-cluster-using-the-azure-cli"></a>Snabb start: Distribuera ett Azure Kubernetes service-kluster med Azure CLI

Azure Kubernetes Service (AKS) är en hanterad Kubernetes-tjänst som gör att du snabbt kan distribuera och hantera kluster. I den här snabb starten kommer du att:
* Distribuera ett AKS-kluster med hjälp av Azure CLI. 
* Köra ett program med flera behållare med en webb klient del och en Redis-instans i klustret. 
* Övervaka hälso tillståndet för klustret och poddar som kör ditt program.

  ![Röstnings app distribuerad i Azure Kubernetes-tjänsten](./media/container-service-kubernetes-walkthrough/voting-app-deployed-in-azure-kubernetes-service.png)

Den här snabbstarten förutsätter grundläggande kunskaper om Kubernetes-begrepp. Mer information finns i [Viktiga koncept för Azure Kubernetes Service (AKS)][kubernetes-concepts].

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Mer information om hur du skapar en pool med Windows Server-noder finns i [skapa ett AKS-kluster som stöder Windows Server-behållare][windows-container-cli].

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2.0.64 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

> [!NOTE]
> Kör kommandona som administratör om du planerar att köra kommandona i den här snabb starten lokalt i stället för i Azure Cloud Shell.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En [Azure-resurs grupp](../azure-resource-manager/management/overview.md) är en logisk grupp där Azure-resurser distribueras och hanteras. När du skapar en resurs grupp uppmanas du att ange en plats. Den här platsen är: 
* Lagrings platsen för dina metadata för resurs gruppen.
* Var dina resurser ska köras i Azure om du inte anger någon annan region när du skapar en resurs. 

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

Skapa en resursgrupp med hjälp av kommandot [az group create][az-group-create].


```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Utdata för resurs gruppen har skapats:

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="enable-cluster-monitoring"></a>Aktivera kluster övervakning

1. Verifiera att *Microsoft. OperationsManagement* och *Microsoft. OperationalInsights* är registrerade i din prenumeration. Kontrol lera registrerings statusen:

    ```azurecli
    az provider show -n Microsoft.OperationsManagement -o table
    az provider show -n Microsoft.OperationalInsights -o table
    ```
 
    Om de inte är registrerade registrerar du *Microsoft. OperationsManagement* och *Microsoft. OperationalInsights* med:
 
    ```azurecli
    az provider register --namespace Microsoft.OperationsManagement
    az provider register --namespace Microsoft.OperationalInsights
    ```

2. Aktivera [Azure Monitor för behållare][azure-monitor-containers] med hjälp av *övervaknings parametern--Enable-addons* . 

## <a name="create-aks-cluster"></a>Skapa AKS-kluster

Skapa ett AKS-kluster med kommandot [AZ AKS Create][az-aks-create] . I följande exempel skapas ett kluster med namnet *myAKSCluster* med en nod: 

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --node-count 1 --enable-addons monitoring --generate-ssh-keys
```

Efter några minuter slutförs kommandot och returnerar JSON-formaterad information om klustret.

> [!NOTE]
> När du skapar ett AKS-kluster skapas automatiskt en andra resurs grupp för att lagra AKS-resurserna. Mer information finns i [Varför är två resurs grupper skapade med AKS?](./faq.md#why-are-two-resource-groups-created-with-aks)

## <a name="connect-to-the-cluster"></a>Anslut till klustret

Om du vill hantera ett Kubernetes-kluster använder du kommando rads klienten Kubernetes, [kubectl][kubectl]. `kubectl` är redan installerat om du använder Azure Cloud Shell. 

1. Installera `kubectl` lokalt med kommandot [AZ AKS install-CLI][az-aks-install-cli] :

    ```azurecli
    az aks install-cli
    ```

2. Konfigurera `kubectl` för att ansluta till ditt Kubernetes-kluster med kommandot [AZ AKS get-credentials][az-aks-get-credentials] . Följande kommando:  
      * Hämtar autentiseringsuppgifter och konfigurerar Kubernetes CLI för att använda dem.
      * Använder `~/.kube/config` är standard platsen för [konfigurations filen för Kubernetes][kubeconfig-file]. Ange en annan plats för Kubernetes-konfigurations filen med *--File*.


    ```azurecli-interactive
    az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
    ```

3. Kontrol lera anslutningen till klustret med hjälp av kommandot [kubectl get][kubectl-get] . Det här kommandot returnerar en lista över klusternoderna.

    ```azurecli-interactive
    kubectl get nodes
    ```

    Utdata visar den enskilda noden som skapades i föregående steg. Kontrol lera att nodens status är *klar*:

    ```output
    NAME                       STATUS   ROLES   AGE     VERSION
    aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.12.8
    ```

## <a name="run-the-application"></a>Kör programmet

En [Kubernetes manifest fil][kubernetes-deployment] definierar ett klusters önskade tillstånd, till exempel vilka behållar avbildningar som ska köras. 

I den här snabb starten ska du använda ett manifest för att skapa alla objekt som behövs för att köra [Azures röst program][azure-vote-app]. Detta manifest innehåller två [Kubernetes-distributioner][kubernetes-deployment]:
* Azures exempel på python-program för Azure-röstning.
* En Redis-instans. 

Två [Kubernetes-tjänster][kubernetes-service] skapas också:
* En intern tjänst för Redis-instansen.
* En extern tjänst för att få åtkomst till Azures röst program från Internet.

1. Skapa en fil som heter `azure-vote.yaml`.
    * Om du använder Azure Cloud Shell kan filen skapas med `code` , `vi` eller `nano` som om du arbetar med ett virtuellt eller fysiskt system
1. Kopiera i följande YAML-definition:

    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: azure-vote-back
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: azure-vote-back
      template:
        metadata:
          labels:
            app: azure-vote-back
        spec:
          nodeSelector:
            "beta.kubernetes.io/os": linux
          containers:
          - name: azure-vote-back
            image: mcr.microsoft.com/oss/bitnami/redis:6.0.8
            env:
            - name: ALLOW_EMPTY_PASSWORD
              value: "yes"
            resources:
              requests:
                cpu: 100m
                memory: 128Mi
              limits:
                cpu: 250m
                memory: 256Mi
            ports:
            - containerPort: 6379
              name: redis
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: azure-vote-back
    spec:
      ports:
      - port: 6379
      selector:
        app: azure-vote-back
    ---
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: azure-vote-front
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: azure-vote-front
      template:
        metadata:
          labels:
            app: azure-vote-front
        spec:
          nodeSelector:
            "beta.kubernetes.io/os": linux
          containers:
          - name: azure-vote-front
            image: mcr.microsoft.com/azuredocs/azure-vote-front:v1
            resources:
              requests:
                cpu: 100m
                memory: 128Mi
              limits:
                cpu: 250m
                memory: 256Mi
            ports:
            - containerPort: 80
            env:
            - name: REDIS
              value: "azure-vote-back"
    ---
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
    ```

1. Distribuera programmet med kommandot [kubectl apply][kubectl-apply] och ange namnet på ditt YAML-manifest:

    ```console
    kubectl apply -f azure-vote.yaml
    ```

    Utdata visar de distributioner och tjänster som har skapats:

    ```output
    deployment "azure-vote-back" created
    service "azure-vote-back" created
    deployment "azure-vote-front" created
    service "azure-vote-front" created
    ```

## <a name="test-the-application"></a>Testa programmet

När programmet körs så exponerar en Kubernetes-tjänst programmets klientdel mot Internet. Den här processen kan ta ett par minuter att slutföra.

Övervaka förloppet med hjälp av kommandot [kubectl get service][kubectl-get] med `--watch` argumentet.

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

Utdata från den **externa IP-adressen** för `azure-vote-front` tjänsten visas i början som *väntande*.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

När den **externa IP** -adressen ändras från *väntande* till en verklig offentlig IP-adress, använder `CTRL-C` du för att stoppa `kubectl` bevaknings processen. Följande exempelutdata visar en giltig offentlig IP-adress som har tilldelats tjänsten:

```output
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Om du vill se hur Azure Vote-appen fungerar i praktiken så öppnar du en webbläsare till den externa IP-adressen för din tjänst.

![Röstnings app distribuerad i Azure Kubernetes-tjänsten](./media/container-service-kubernetes-walkthrough/voting-app-deployed-in-azure-kubernetes-service.png)

Visa de klusternoder och poddar-hälsomått som registrerats av [Azure Monitor för behållare][azure-monitor-containers] i Azure Portal. 

## <a name="delete-the-cluster"></a>Ta bort klustret

Ta bort onödiga resurser för att undvika Azure-avgifter. Använd kommandot [az group delete][az-group-delete] för att ta bort resursgruppen, containertjänsten och alla relaterade resurser.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> När du tar bort klustret tas Azure Active Directory-tjänstens huvudnamn, som används av AKS-klustret, inte bort. Stegvisa instruktioner om hur du tar bort tjänstens huvudnamn finns i dokumentationen om [viktiga överväganden och borttagning av AKS-tjänsten][sp-delete].
> 
> Om du använde en hanterad identitet hanteras identiteten av plattformen och kräver inte borttagning.

## <a name="get-the-code"></a>Hämta koden

Befintliga behållar avbildningar användes i den här snabb starten för att skapa en Kubernetes-distribution. Den relaterade program koden, Dockerfile och Kubernetes manifest filen [finns på GitHub.][azure-vote-app]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du distribuerat ett Kubernetes-kluster och sedan distribuerat ett program med flera behållare till det. [Få åtkomst till Kubernetes-webbinstrumentpanelen][kubernetes-dashboard] för ditt AKS-kluster.

Om du vill lära dig mer om AKS, och gå igenom ett exempel med fullständig distributionskod, fortsätter du till självstudiekursen om Kubernetes-kluster.

> [!div class="nextstepaction"]
> [Självstudiekurs om AKS][aks-tutorial]

<!-- LINKS - external -->
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubeconfig-file]: https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: ../azure-monitor/containers/container-insights-onboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks#az-aks-browse
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks#az-aks-install-cli
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-monitor-containers]: ../azure-monitor/containers/container-insights-overview.md
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[windows-container-cli]: windows-container-cli.md