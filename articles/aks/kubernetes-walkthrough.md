---
title: 'Snabb start: Distribuera ett AKS-kluster med hjälp av Azure CLI'
description: Lär dig hur du snabbt kan skapa ett Kubernetes-kluster, distribuera ett program och övervaka prestanda i Azure Kubernetes Service (AKS) med hjälp av Azure CLI.
services: container-service
ms.topic: quickstart
ms.date: 01/12/2021
ms.custom:
- H1Hack27Feb2017
- mvc
- devcenter
- seo-javascript-september2019
- seo-javascript-october2019
- seo-python-october2019
- devx-track-azurecli
- contperf-fy21q1
ms.openlocfilehash: afa1aaafe7ea339eb82e35039980009908295008
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2021
ms.locfileid: "98248623"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-cluster-using-the-azure-cli"></a>Snabb start: Distribuera ett Azure Kubernetes service-kluster med Azure CLI

I den här snabb starten distribuerar du ett Azure Kubernetes service-kluster (AKS) med hjälp av Azure CLI. AKS är en hanterad Kubernetes-tjänst som gör att du snabbt kan distribuera och hantera kluster. Ett flerbehållarprogram som består av en webbklientdel och en Redis-instans körs sedan i klustret. Då ser du hur du övervakar hälsotillståndet för klustret och poddar som kör programmet.

Mer information om hur du skapar en pool med Windows Server-noder finns i [skapa ett AKS-kluster som stöder Windows Server-behållare][windows-container-cli].

![Röstnings app distribuerad i Azure Kubernetes-tjänsten](./media/container-service-kubernetes-walkthrough/voting-app-deployed-in-azure-kubernetes-service.png)

Den här snabbstarten förutsätter grundläggande kunskaper om Kubernetes-begrepp. Mer information finns i [Viktiga koncept för Azure Kubernetes Service (AKS)][kubernetes-concepts].

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2.0.64 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

> [!NOTE]
> Om du kör kommandona i den här snabb starten lokalt (i stället för Azure Cloud Shell), måste du kontrol lera att du kör kommandona som administratör.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk grupp där Azure-resurser distribueras och hanteras. När du skapar en resursgrupp uppmanas du att ange en plats. Den här platsen är den plats där resurs gruppens metadata lagras, men det är även där dina resurser körs i Azure om du inte anger någon annan region när du skapar en resurs. Skapa en resursgrupp med hjälp av kommandot [az group create][az-group-create].

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Utdata som liknar följande exempel visar att resurs gruppen har skapats:

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

## <a name="create-aks-cluster"></a>Skapa AKS-kluster

Använd kommandot [az aks create][az-aks-create] för att skapa ett AKS-kluster. I följande exempel skapas ett kluster med namnet *myAKSCluster* och en enda nod. Det tar flera minuter att slutföra.

> [!NOTE]
> [Azure Monitor för behållare][azure-monitor-containers] aktive ras med *övervaknings parametern--Enable-addon* , som kräver att *Microsoft. OperationsManagement* och *Microsoft. OperationalInsights* registreras på din prenumeration. Kontrol lera registrerings statusen:
> 
> ```azurecli
> az provider show -n Microsoft.OperationsManagement -o table
> az provider show -n Microsoft.OperationalInsights -o table
> ```
> 
> Om de inte är registrerade använder du följande kommando för att registrera *Microsoft. OperationsManagement* och *Microsoft. OperationalInsights*:
> 
> ```azurecli
> az provider register --namespace Microsoft.OperationsManagement
> az provider register --namespace Microsoft.OperationalInsights
> ```

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --node-count 1 --enable-addons monitoring --generate-ssh-keys
```

Efter några minuter slutförs kommandot och returnerar JSON-formaterad information om klustret.

> [!NOTE]
> När du skapar ett AKS-kluster skapas en andra resurs grupp automatiskt för att lagra AKS-resurserna. Mer information finns i avsnittet [varför skapas två resurs grupper med AKS?](./faq.md#why-are-two-resource-groups-created-with-aks)

## <a name="connect-to-the-cluster"></a>Anslut till klustret

För att hantera Kubernetes-kluster använder du [kubectl][kubectl], Kubernetes kommandoradsklient. Om du använder Azure Cloud Shell är `kubectl` redan installerat. För att installera `kubectl` lokalt använder du kommandot [az aks install-cli][az-aks-install-cli]:

```azurecli
az aks install-cli
```

För att konfigurera `kubectl` till att ansluta till ditt Kubernetes-kluster använder du kommandot [az aks get-credentials][az-aks-get-credentials]. Det här kommandot laddar ned autentiseringsuppgifter och konfigurerar Kubernetes CLI för att använda dem.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

> [!NOTE]
> Kommandot ovan använder standard platsen för [konfigurations filen Kubernetes][kubeconfig-file], som är `~/.kube/config` . Du kan ange en annan plats för Kubernetes-konfigurations filen med *--File*.

Du kan kontrollera anslutningen till klustret genom att köra kommandot [kubectl get][kubectl-get] för att returnera en lista över klusternoderna.

```azurecli-interactive
kubectl get nodes
```

Följande exempelutdata visar den enskilda nod som skapades i föregående steg. Kontrollera att status för noden är *Klar*:

```output
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.12.8
```

## <a name="run-the-application"></a>Kör programmet

En [manifest fil för Kubernetes][kubernetes-deployment] definierar ett önskat tillstånd för klustret, till exempel vilka behållar avbildningar som ska köras. I den här snabb starten används ett manifest för att skapa alla objekt som behövs för att köra [Azures röst program][azure-vote-app]. Det här manifestet innehåller två [Kubernetes-distributioner][kubernetes-deployment] – en för exemplet på Azure Vote Python-program och den andra för en Redis-instans. Två [Kubernetes-tjänster][kubernetes-service] skapas också – en intern tjänst för Redis-instansen och en extern tjänst för att komma åt Azure Vote-programmet från Internet.

Skapa en fil med namnet `azure-vote.yaml` och kopiera följande YAML-definition. Om du använder Azure Cloud Shell kan filen skapas med `code` , `vi` eller `nano` som om du arbetar på ett virtuellt eller fysiskt system:

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

Distribuera programmet med kommandot [kubectl apply][kubectl-apply] och ange namnet på ditt YAML-manifest:

```console
kubectl apply -f azure-vote.yaml
```

Följande exempelutdata visar de distributioner och tjänster som skapats:

```output
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>Testa programmet

När programmet körs så exponerar en Kubernetes-tjänst programmets klientdel mot Internet. Den här processen kan ta ett par minuter att slutföra.

Du kan övervaka förloppet genom att använda kommandot [kubectl get service][kubectl-get] med argumentet `--watch`.

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

Till en början visas *EXTERNAL-IP* för *azure-vote-front*-tjänsten som *väntande*.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

När *EXTERNAL-IP*-adressen ändras från *väntande* till en faktisk offentlig IP-adress använder du `CTRL-C` för att stoppa `kubectl`-övervakningsprocessen. Följande exempelutdata visar en giltig offentlig IP-adress som har tilldelats tjänsten:

```output
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Om du vill se hur Azure Vote-appen fungerar i praktiken så öppnar du en webbläsare till den externa IP-adressen för din tjänst.

![Röstnings app distribuerad i Azure Kubernetes-tjänsten](./media/container-service-kubernetes-walkthrough/voting-app-deployed-in-azure-kubernetes-service.png)

När AKS-klustret skapades har [Azure Monitor för behållare][azure-monitor-containers] Aktiver ATS för att avbilda hälso mått för både klusternoderna och poddar. De här hälsomåtten är tillgängliga i Azure-portalen.

## <a name="delete-the-cluster"></a>Ta bort klustret

För att undvika Azure-avgifter bör du rensa resurser som inte behövs.  När klustret inte längre behövs kan du använda kommandot [az group delete][az-group-delete] för att ta bort resursgruppen, containertjänsten och alla relaterade resurser.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> När du tar bort klustret tas Azure Active Directory-tjänstens huvudnamn, som används av AKS-klustret, inte bort. Stegvisa instruktioner om hur du tar bort tjänstens huvudnamn finns i dokumentationen om [viktiga överväganden och borttagning av AKS-tjänsten][sp-delete]. Om du använde en hanterad identitet hanteras identiteten av plattformen och kräver inte borttagning.

## <a name="get-the-code"></a>Hämta koden

I den här snabbstarten har fördefinierade containeravbildningar användes för att skapa en Kubernetes-distribution. Den tillhörande programkoden, Dockerfile och Kubernetes-manifestfilen finns på GitHub.

[https://github.com/Azure-Samples/azure-voting-app-redis][azure-vote-app]

## <a name="next-steps"></a>Nästa steg

I den här snabbstartsguiden distribuerade du ett Kubernetes-kluster och distribuerade sedan ett flercontainerprogram till det. Du kan också [komma åt Kubernetes-webbinstrumentpanelen][kubernetes-dashboard] för ditt AKS-kluster.

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
[aks-monitor]: ../azure-monitor/insights/container-insights-onboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks?view=azure-cli-latest&preserve-view=true#az-aks-browse
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest&preserve-view=true#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest&preserve-view=true#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest&preserve-view=true#az-aks-install-cli
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-monitor-containers]: ../azure-monitor/insights/container-insights-overview.md
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[windows-container-cli]: windows-container-cli.md