---
title: Självstudie om Kubernetes i Azure – Skala program
description: I den här självstudien om Azure Kubernetes Service (AKS) lär du dig hur du skalar noder och poddar i Kubernetes och hur du implementerar horisontell automatisk skalning av poddar.
services: container-service
ms.topic: tutorial
ms.date: 01/12/2021
ms.custom: mvc
ms.openlocfilehash: a268d39ec514fc7b88b555221ece7dc044ca49ba
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767519"
---
# <a name="tutorial-scale-applications-in-azure-kubernetes-service-aks"></a>Självstudie: Skala program i Azure Kubernetes Service (AKS)

Om du har följt självstudierna så har du ett fungerande Kubernetes-kluster i AKS och du har distribuerat Azure Voting-exempelappen. I den här självstudien, som är del fem av sju, skalar du ut poddarna i appen och provar autoskalning av poddar. Du får också lära dig hur du skalar ut antalet Azure VM-noder så att du ändrar klustrets kapacitet som värd för arbetsbelastningar. Lär dig att:

> [!div class="checklist"]
> * Skala Kubernetes-noderna
> * Skala Kubernetes-poddar som kör ditt program manuellt
> * Konfigurera poddar för automatisk skalning som kör appens klientdel

I senare självstudier uppdateras Azure Vote-programmet till en ny version.

## <a name="before-you-begin"></a>Innan du börjar

I tidigare självstudier paketerades en app i en containeravbildning. Den här avbildningen laddades upp till Azure Container Registry, och du skapade ett AKS-kluster. Programmet distribuerades sedan till AKS-klustret. Om du inte har utfört de här stegen och vill följa med så kan du börja med [Självstudie 1 – Skapa containeravbildningar][aks-tutorial-prepare-app].

Den här självstudien kräver att du kör Azure CLI version 2.0.53 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="manually-scale-pods"></a>Skala poddar manuellt

När Azure Vote-programmet på klientsidan och Redis-instansen distribuerades i de föregående självstudierna skapades en enda replik. Om du vill visa antalet och tillståndet för poddar i ditt kluster använder du kommandot [kubectl get][kubectl-get] på följande sätt:

```console
kubectl get pods
```

Följande exempelutdata visar en frontend-pod och en backend-pod:

```output
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2549686872-4d2r5   1/1       Running   0          31m
azure-vote-front-848767080-tf34m   1/1       Running   0          31m
```

Om du vill ändra antalet poddar i *azure-vote-front*-distributionen manuellt använder du kommandot [kubectl scale][kubectl-scale]. I följande exempel ökas antalet frontend-poddar till *5*:

```console
kubectl scale --replicas=5 deployment/azure-vote-front
```

Kör [kubectl get pods][kubectl-get] igen för att verifiera att AKS har skapat de ytterligare poddarna. Efter någon minut är poddarna tillgängliga i klustret:

```console
kubectl get pods

                                    READY     STATUS    RESTARTS   AGE
azure-vote-back-2606967446-nmpcf    1/1       Running   0          15m
azure-vote-front-3309479140-2hfh0   1/1       Running   0          3m
azure-vote-front-3309479140-bzt05   1/1       Running   0          3m
azure-vote-front-3309479140-fvcvm   1/1       Running   0          3m
azure-vote-front-3309479140-hrbf2   1/1       Running   0          15m
azure-vote-front-3309479140-qphz8   1/1       Running   0          3m
```

## <a name="autoscale-pods"></a>Automatisk skalning av poddar

Kubernetes har stöd för [horisontell autoskalning av poddar][kubernetes-hpa] så att antalet poddar i en distribution justeras beroende på CPU-användningen eller något annat mått du väljer. [Metrics Server][metrics-server] används för att förse Kubernetes resursutnyttjande och distribueras automatiskt i AKS-kluster i version 1.10 och högre. Om du vill se AKS-klusterversionen använder du kommandot [az aks show][az-aks-show], som visas i följande exempel:

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query kubernetesVersion --output table
```

> [!NOTE]
> Om ditt AKS-kluster är mindre *än 1,10* installeras inte måttservern automatiskt. Metrics Server-installationsmanifest är tillgängliga som en tillgång i Metrics Server-versioner, vilket innebär att du `components.yaml` kan installera dem via en URL. Mer information om dessa YAML-definitioner finns i [avsnittet Distribution][metrics-server-github] i viktigt.
> 
> Exempelinstallation:
> ```console
> kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/download/v0.3.6/components.yaml
> ```

Om du vill använda autoskalning måste alla containrar i dina poddar och poddar ha definierade CPU-begäranden och -gränser. I `azure-vote-front`-distributionen begär klientdelscontainern redan 0,25 CPU med maxgränsen 0,5 CPU. Dessa resursbegäranden och begränsningar definieras enligt följande exempelavsnitt:

```yaml
resources:
  requests:
     cpu: 250m
  limits:
     cpu: 500m
```

I följande exempel används kommandot [kubectl autoscale][kubectl-autoscale] för att automatiskt skala antalet poddar i *azure-vote-front*-distributionen. Om den genomsnittliga processoranvändningen för alla poddar överskrider 50 % av den begärda användningen ökar autoskalningen poddarna upp till *högst 10* instanser. Minst *3* instanser definieras sedan för distributionen:

```console
kubectl autoscale deployment azure-vote-front --cpu-percent=50 --min=3 --max=10
```

Du kan också skapa en manifestfil för att definiera autoskalningsbeteendet och resursbegränsningarna. Följande är ett exempel på en manifestfil med namnet `azure-vote-hpa.yaml` .

```yaml
apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: azure-vote-back-hpa
spec:
  maxReplicas: 10 # define max replica count
  minReplicas: 3  # define min replica count
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: azure-vote-back
  targetCPUUtilizationPercentage: 50 # target CPU utilization

---

apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: azure-vote-front-hpa
spec:
  maxReplicas: 10 # define max replica count
  minReplicas: 3  # define min replica count
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: azure-vote-front
  targetCPUUtilizationPercentage: 50 # target CPU utilization
```

Använd `kubectl apply` för att tillämpa autoskalning som definierats i `azure-vote-hpa.yaml` manifestfilen.

```console
kubectl apply -f azure-vote-hpa.yaml
```

Om du vill visa statusen för autoskalningen använder du `kubectl get hpa`-kommandot på följande sätt:

```
kubectl get hpa

NAME               REFERENCE                     TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
azure-vote-front   Deployment/azure-vote-front   0% / 50%   3         10        3          2m
```

Efter några minuter, med minimal belastning på Azure Vote-appen, minskar antalet podrepliker automatiskt till tre. Du kan använda `kubectl get pods` igen om du vill visa de onödiga poddarna som tas bort.

## <a name="manually-scale-aks-nodes"></a>Skala AKS-noder manuellt

Om du skapade Kubernetes-klustret med hjälp av kommandona i föregående självstudie har det två noder. Du kan justera antalet noder manuellt om du planerar att ha fler eller färre containerarbetsbelastningar i klustret.

I följande exempel ökas antalet agentnoder till tre i Kubernetes-klustret med namn *myAKSCluster*. Det tar några minuter att slutföra kommandot.

```azurecli
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 3
```

När klustret har skalats liknar utdata följande exempel:

```output
"agentPoolProfiles": [
  {
    "count": 3,
    "dnsPrefix": null,
    "fqdn": null,
    "name": "myAKSCluster",
    "osDiskSizeGb": null,
    "osType": "Linux",
    "ports": null,
    "storageProfile": "ManagedDisks",
    "vmSize": "Standard_D2_v2",
    "vnetSubnetId": null
  }
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du använt olika skalningsfunktioner i Kubernetes-klustret. Du har lärt dig att:

> [!div class="checklist"]
> * Skala Kubernetes-poddar som kör ditt program manuellt
> * Konfigurera poddar för automatisk skalning som kör appens klientdel
> * Skala manuellt Kubernetes-noderna

Gå vidare till nästa självstudie och lär dig hur du uppdaterar program i Kubernetes.

> [!div class="nextstepaction"]
> [Uppdatera ett program i Kubernetes][aks-tutorial-update-app]

<!-- LINKS - external -->
[kubectl-autoscale]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#autoscale
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-scale]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#scale
[kubernetes-hpa]: https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/
[metrics-server-github]: https://github.com/kubernetes-sigs/metrics-server/blob/master/README.md#deployment
[metrics-server]: https://kubernetes.io/docs/tasks/debug-application-cluster/resource-metrics-pipeline/#metrics-server

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-update-app]: ./tutorial-kubernetes-app-update.md
[az-aks-scale]: /cli/azure/aks#az_aks_scale
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az_aks_show
