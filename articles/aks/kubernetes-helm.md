---
title: Installera befintliga program med Helm i AKS
description: Lär dig hur du använder Helm packnings verktyg för att distribuera behållare i ett Azure Kubernetes service-kluster (AKS)
services: container-service
author: zr-msft
ms.topic: article
ms.date: 12/07/2020
ms.author: zarhoads
ms.openlocfilehash: f12dffe0b538738a8f6dd00cd3d87d44da828f21
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96779175"
---
# <a name="install-existing-applications-with-helm-in-azure-kubernetes-service-aks"></a>Installera befintliga program med Helm i Azure Kubernetes service (AKS)

[Helm][helm] är ett paket med öppen källkod som hjälper dig att installera och hantera livs cykeln för Kubernetes-program. Precis som Linux-paket hanterare som *apt* och *yum*, används Helm för att hantera Kubernetes-diagram, som är paket med förkonfigurerade Kubernetes-resurser.

Den här artikeln visar hur du konfigurerar och använder Helm i ett Kubernetes-kluster på AKS.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster kan du läsa snabb starten för AKS [med hjälp av Azure CLI][aks-quickstart-cli] eller [Azure Portal][aks-quickstart-portal].

Du behöver också Helm CLI installerat, som är den klient som körs i utvecklings systemet. Du kan starta, stoppa och hantera program med Helm. Om du använder Azure Cloud Shell är Helm CLI redan installerat. Installationsinstruktioner på din lokala plattform finns i [Installera Helm][helm-install].

> [!IMPORTANT]
> Helm är avsedd att köras på Linux-noder. Om du har Windows Server-noder i klustret måste du se till att Helm-poddar endast är schemalagda att köras på Linux-noder. Du måste också se till att alla Helm-diagram som du installerar också är schemalagda att köras på rätt noder. Kommandona i den här artikeln använder [Node-Selector] [K8s-Node-Selector] för att se till att poddar är schemalagda till rätt noder, men inte alla Helm-diagram kan exponera en Node-selektor. Du kan också överväga att använda andra alternativ i klustret, till exempel för-och- [smak][taints].

## <a name="verify-your-version-of-helm"></a>Verifiera din version av Helm

Använd `helm version` kommandot för att kontrol lera att du har Helm 3 installerat:

```console
helm version
```

I följande exempel visas Helm version 3.0.0 installerat:

```console
$ helm version

version.BuildInfo{Version:"v3.0.0", GitCommit:"e29ce2a54e96cd02ccfce88bee4f58bb6e2a28b6", GitTreeState:"clean", GoVersion:"go1.13.4"}
```

## <a name="install-an-application-with-helm-v3"></a>Installera ett program med Helm v3

### <a name="add-helm-repositories"></a>Lägg till Helm-databaser

Använd kommandot [Helm lagrings platsen][helm-repo-add] för att lägga till databasen *ingress-nginx* .

```console
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
```

### <a name="find-helm-charts"></a>Hitta Helm-diagram

Helm-diagram används för att distribuera program till ett Kubernetes-kluster. Om du vill söka efter tidigare skapade Helm-diagram använder du kommandot [Helm search][helm-search] :

```console
helm search repo ingress-nginx
```

I följande komprimerade exempel utdata visas några av Helm-diagrammen som är tillgängliga för användning:

```console
$ helm search repo ingress-nginx

NAME                            CHART VERSION   APP VERSION     DESCRIPTION                                       
ingress-nginx/ingress-nginx     2.12.0          0.34.1          Ingress controller for Kubernetes using NGINX a...
```

Om du vill uppdatera listan över diagram använder du kommandot [Helm lagrings platsen Update][helm-repo-update] .

```console
helm repo update
```

I följande exempel visas en lyckad lagrings platsen-uppdatering:

```console
$ helm repo update

Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "ingress-nginx" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

### <a name="run-helm-charts"></a>Köra Helm-diagram

Om du vill installera diagram med Helm använder du kommandot [Helm install][helm-install-command] och anger ett versions namn och namnet på det diagram som ska installeras. Om du vill se hur du installerar ett Helm-diagram i praktiken ska vi installera en grundläggande nginx-distribution med ett Helm-diagram.

```console
helm install my-nginx-ingress ingress-nginx/ingress-nginx \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

I följande komprimerade exempel utdata visas distributions status för de Kubernetes-resurser som skapats av Helm-diagrammet:

```console
$ helm install my-nginx-ingress ingress-nginx/ingress-nginx \
>     --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
>     --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux

NAME: my-nginx-ingress
LAST DEPLOYED: Fri Nov 22 10:08:06 2019
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The nginx-ingress controller has been installed.
It may take a few minutes for the LoadBalancer IP to be available.
You can watch the status by running 'kubectl --namespace default get services -o wide -w my-nginx-ingress-ingress-nginx-controller'
...
```

Använd `kubectl get services` kommandot för att hämta den *externa IP-adressen* för din tjänst.

```console
kubectl --namespace default get services -o wide -w my-nginx-ingress-ingress-nginx-controller
```

Till exempel visar kommandot nedan den *externa IP-adressen* för *My-nginx-ingress-* ingångs-nginx-Controller-tjänsten:

```console
$ kubectl --namespace default get services -o wide -w my-nginx-ingress-ingress-nginx-controller

NAME                                        TYPE           CLUSTER-IP   EXTERNAL-IP      PORT(S)                      AGE   SELECTOR
my-nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.2.237   <EXTERNAL-IP>    80:31380/TCP,443:32239/TCP   72s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=my-nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

### <a name="list-releases"></a>Lista över versioner

Om du vill se en lista över versioner som är installerade i klustret använder du `helm list` kommandot.

```console
helm list
```

I följande exempel visas den *nginx-* ingångs version som distribuerades i föregående steg:

```console
$ helm list

NAME                NAMESPACE   REVISION    UPDATED                                 STATUS      CHART                   APP VERSION
my-nginx-ingress    default     1           2019-11-22 10:08:06.048477 -0600 CST    deployed    nginx-ingress-1.25.0    0.26.1 
```

### <a name="clean-up-resources"></a>Rensa resurser

När du distribuerar ett Helm-diagram skapas ett antal Kubernetes-resurser. Dessa resurser omfattar poddar, distributioner och tjänster. Om du vill rensa resurserna använder du kommandot [Helm Uninstall][helm-cleanup] och anger namnet på din utgåva, som du hittar i föregående `helm list` kommando.

```console
helm uninstall my-nginx-ingress
```

I följande exempel visas versionen som heter *My-nginx-* ingångs funktionen har avinstallerats:

```console
$ helm uninstall my-nginx-ingress

release "my-nginx-ingress" uninstalled
```

## <a name="next-steps"></a>Nästa steg

Mer information om hur du hanterar Kubernetes program distributioner med Helm finns i Helm-dokumentationen.

> [!div class="nextstepaction"]
> [Helm-dokumentation][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-cleanup]: https://helm.sh/docs/intro/using_helm/#helm-uninstall-uninstalling-a-release
[helm-documentation]: https://helm.sh/docs/
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-command]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[helm-repo-add]: https://helm.sh/docs/intro/quickstart/#initialize-a-helm-chart-repository
[helm-search]: https://helm.sh/docs/intro/using_helm/#helm-search-finding-charts
[helm-repo-update]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
            
<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[taints]: operator-best-practices-advanced-scheduler.md
