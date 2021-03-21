---
title: Tillägg för HTTP-programroutning på Azure Kubernetes service (AKS)
description: Använd tillägget HTTP Application routing för att få åtkomst till program som distribuerats på Azure Kubernetes service (AKS).
services: container-service
author: lachie83
ms.topic: article
ms.date: 07/20/2020
ms.author: laevenso
ms.openlocfilehash: 25fc021a48e8936f242df35f7485fc59a93bba13
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102172808"
---
# <a name="http-application-routing"></a>Routning av HTTP-program

Lösningen för HTTP-programroutning gör det enkelt att komma åt program som distribueras till ditt AKS-kluster (Azure Kubernetes service). När lösningen har Aktiver ATS konfigurerar den en ingångs [kontroll](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/) i ditt AKS-kluster. När program distribueras skapar lösningen även offentligt tillgängliga DNS-namn för program slut punkter.

När tillägget har Aktiver ATS skapas en DNS-zon i din prenumeration. Mer information om DNS-kostnader finns i [DNS-priser][dns-pricing].

> [!CAUTION]
> Tillägget för att dirigera HTTP-program är utformat så att du snabbt kan skapa en ingångs kontroll och få åtkomst till dina program. Det här tillägget är för närvarande inte avsett för användning i en produktions miljö och rekommenderas inte för produktions användning. För drift färdiga distributioner som inkluderar flera repliker och TLS-stöd, se [skapa en https](./ingress-tls.md)ingångs kontroll.

## <a name="http-routing-solution-overview"></a>Översikt över HTTP-routning

Tillägget distribuerar två komponenter: en [Kubernetes ingress-styrenhet][ingress] och en [extern DNS-][external-dns] styrenhet.

- **Ingress Controller**: ingångs styrenheten exponeras för Internet med hjälp av en Kubernetes-tjänst av typen Loadbalancer. Ingångs kontrollen bevakar och implementerar [Kubernetes ingress-resurser][ingress-resource]som skapar vägar till program slut punkter.
- **Extern DNS-styrenhet**: söker efter Kubernetes ingress-resurser och skapar DNS A-poster i den KLUSTERbaserade DNS-zonen.

## <a name="deploy-http-routing-cli"></a>Distribuera HTTP-Routning: CLI

Du kan aktivera routnings tillägget för HTTP-program med Azure CLI när du distribuerar ett AKS-kluster. Det gör du genom att använda kommandot [AZ AKS Create][az-aks-create] med `--enable-addons` argumentet.

```azurecli
az aks create --resource-group myResourceGroup --name myAKSCluster --enable-addons http_application_routing
```

> [!TIP]
> Om du vill aktivera flera tillägg kan du ange dem som en kommaavgränsad lista. Om du till exempel vill aktivera Routning och övervakning av HTTP-program använder du formatet `--enable-addons http_application_routing,monitoring` .

Du kan också aktivera HTTP-routning i ett befintligt AKS-kluster med kommandot [AZ AKS Enable-addons][az-aks-enable-addons] . Om du vill aktivera HTTP-routning i ett befintligt kluster lägger du till `--addons` parametern och anger *http_application_routing* som visas i följande exempel:

```azurecli
az aks enable-addons --resource-group myResourceGroup --name myAKSCluster --addons http_application_routing
```

När klustret har distribuerats eller uppdaterats använder du kommandot [AZ AKS show][az-aks-show] för att hämta DNS-zonens namn.

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query addonProfiles.httpApplicationRouting.config.HTTPApplicationRoutingZoneName -o table
```

Det här namnet krävs för att distribuera program till AKS-klustret och visas i följande exempel på utdata:

```console
9f9c1fe7-21a1-416d-99cd-3543bb92e4c3.eastus.aksapp.io
```

## <a name="deploy-http-routing-portal"></a>Distribuera HTTP-Routning: Portal

Du kan aktivera routnings tillägget för HTTP-program genom Azure Portal när du distribuerar ett AKS-kluster.

![Aktivera funktionen HTTP-routning](media/http-routing/create.png)

När klustret har distribuerats bläddrar du till resurs gruppen automatiskt skapade AKS och väljer DNS-zonen. Anteckna DNS-zonens namn. Det här namnet krävs för att distribuera program till AKS-klustret.

![Hämta DNS-zonens namn](media/http-routing/dns.png)

## <a name="connect-to-your-aks-cluster"></a>Anslut till ditt AKS-kluster

När du ska ansluta till Kubernetes-klustret från din lokala dator använder du [kubectl][kubectl], Kubernetes kommandoradsklient.

Om du använder Azure Cloud Shell är `kubectl` redan installerat. Du kan även installera det lokalt med hjälp av kommandot [az aks install-cli][]:

```azurecli
az aks install-cli
```

För att konfigurera `kubectl` till att ansluta till ditt Kubernetes-kluster använder du kommandot [az aks get-credentials][]. I följande exempel hämtas autentiseringsuppgifter för AKS-klustret med namnet *MyAKSCluster* i *MyResourceGroup*:

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKSCluster
```

## <a name="use-http-routing"></a>Använd HTTP-routning

Lösningen för HTTP-programroutning kan bara aktive ras på ingångs resurser som kommenteras enligt följande:

```yaml
annotations:
  kubernetes.io/ingress.class: addon-http-application-routing
```

Skapa en fil med namnet **exempel-http-Application-routing. yaml** och kopiera i följande yaml. På rad 43 uppdaterar `<CLUSTER_SPECIFIC_DNS_ZONE>` du med DNS-zonnamn som samlades in i föregående steg i den här artikeln.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld  
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld
  template:
    metadata:
      labels:
        app: aks-helloworld
    spec:
      containers:
      - name: aks-helloworld
        image: mcr.microsoft.com/azuredocs/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "Welcome to Azure Kubernetes Service (AKS)"
---
apiVersion: v1
kind: Service
metadata:
  name: aks-helloworld  
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld
---
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: aks-helloworld
  annotations:
    kubernetes.io/ingress.class: addon-http-application-routing
spec:
  rules:
  - host: aks-helloworld.<CLUSTER_SPECIFIC_DNS_ZONE>
    http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /
```

Använd kommandot [kubectl Apply][kubectl-apply] för att skapa resurserna.

```bash
kubectl apply -f samples-http-application-routing.yaml
```

Följande exempel visar de skapade resurserna:

```bash
$ kubectl apply -f samples-http-application-routing.yaml

deployment.apps/aks-helloworld created
service/aks-helloworld created
ingress.networking.k8s.io/aks-helloworld created
```

Öppna en webbläsare till *AKS – HelloWorld \<CLUSTER_SPECIFIC_DNS_ZONE\> .*, till exempel *AKS-HelloWorld.9f9c1fe7-21a1-416d-99cd-3543bb92e4c3.eastus.aksapp.io* och kontrol lera att du ser demonstrations programmet. Det kan ta några minuter innan programmet visas.

## <a name="remove-http-routing"></a>Ta bort HTTP-routning

Lösningen för HTTP-routning kan tas bort med hjälp av Azure CLI. Kör följande kommando för att ersätta ditt AKS-kluster och resurs grupps namnet.

```azurecli
az aks disable-addons --addons http_application_routing --name myAKSCluster --resource-group myResourceGroup --no-wait
```

När tillägget för routning av HTTP-program är inaktiverat kan vissa Kubernetes-resurser finnas kvar i klustret. De här resurserna omfattar *configMaps* och *hemligheter* och skapas i namn rymden *Kube-system* . Om du vill underhålla ett rent kluster kanske du vill ta bort dessa resurser.

Leta efter *tillägg-http-app-routing* -resurser med följande [kubectl get][kubectl-get] -kommandon:

```console
kubectl get deployments --namespace kube-system
kubectl get services --namespace kube-system
kubectl get configmaps --namespace kube-system
kubectl get secrets --namespace kube-system
```

Följande exempel på utdata visar configMaps som ska tas bort:

```
$ kubectl get configmaps --namespace kube-system

NAMESPACE     NAME                                                       DATA   AGE
kube-system   addon-http-application-routing-nginx-configuration         0      9m7s
kube-system   addon-http-application-routing-tcp-services                0      9m7s
kube-system   addon-http-application-routing-udp-services                0      9m7s
```

Använd kommandot [kubectl Delete][kubectl-delete] för att ta bort resurser. Ange resurs typ, resurs namn och namn område. I följande exempel tar bort ett av de tidigare configmaps:

```console
kubectl delete configmaps addon-http-application-routing-nginx-configuration --namespace kube-system
```

Upprepa föregående `kubectl delete` steg för alla *tillägg-http-app-routing-* resurser som funnits i klustret.

## <a name="troubleshoot"></a>Felsöka

Använd kommandot [kubectl logs][kubectl-logs] för att visa program loggarna för det externa DNS-programmet. Loggarna bör bekräfta att en A-och TXT DNS-post har skapats.

```
$ kubectl logs -f deploy/addon-http-application-routing-external-dns -n kube-system

time="2018-04-26T20:36:19Z" level=info msg="Updating A record named 'aks-helloworld' to '52.242.28.189' for Azure DNS zone '471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io'."
time="2018-04-26T20:36:21Z" level=info msg="Updating TXT record named 'aks-helloworld' to '"heritage=external-dns,external-dns/owner=default"' for Azure DNS zone '471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io'."
```

Dessa poster kan också visas på DNS-zonens resurs i Azure Portal.

![Hämta DNS-posterna](media/http-routing/clippy.png)

Använd kommandot [kubectl logs][kubectl-logs] för att visa program loggarna för ingångs styrenheten nginx. Loggarna bör bekräfta `CREATE` en ingress-resurs och omladdningen av kontrollanten. Alla HTTP-aktiviteter loggas.

```bash
$ kubectl logs -f deploy/addon-http-application-routing-nginx-ingress-controller -n kube-system

-------------------------------------------------------------------------------
NGINX Ingress controller
  Release:    0.13.0
  Build:      git-4bc943a
  Repository: https://github.com/kubernetes/ingress-nginx
-------------------------------------------------------------------------------

I0426 20:30:12.212936       9 flags.go:162] Watching for ingress class: addon-http-application-routing
W0426 20:30:12.213041       9 flags.go:165] only Ingress with class "addon-http-application-routing" will be processed by this ingress controller
W0426 20:30:12.213505       9 client_config.go:533] Neither --kubeconfig nor --master was specified.  Using the inClusterConfig.  This might not work.
I0426 20:30:12.213752       9 main.go:181] Creating API client for https://10.0.0.1:443
I0426 20:30:12.287928       9 main.go:225] Running in Kubernetes Cluster version v1.8 (v1.8.11) - git (clean) commit 1df6a8381669a6c753f79cb31ca2e3d57ee7c8a3 - platform linux/amd64
I0426 20:30:12.290988       9 main.go:84] validated kube-system/addon-http-application-routing-default-http-backend as the default backend
I0426 20:30:12.294314       9 main.go:105] service kube-system/addon-http-application-routing-nginx-ingress validated as source of Ingress status
I0426 20:30:12.426443       9 stat_collector.go:77] starting new nginx stats collector for Ingress controller running in namespace  (class addon-http-application-routing)
I0426 20:30:12.426509       9 stat_collector.go:78] collector extracting information from port 18080
I0426 20:30:12.448779       9 nginx.go:281] starting Ingress controller
I0426 20:30:12.463585       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-nginx-configuration", UID:"2588536c-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"559", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-nginx-configuration
I0426 20:30:12.466945       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-tcp-services", UID:"258ca065-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"561", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-tcp-services
I0426 20:30:12.467053       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-udp-services", UID:"259023bc-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"562", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-udp-services
I0426 20:30:13.649195       9 nginx.go:302] starting NGINX process...
I0426 20:30:13.649347       9 leaderelection.go:175] attempting to acquire leader lease  kube-system/ingress-controller-leader-addon-http-application-routing...
I0426 20:30:13.649776       9 controller.go:170] backend reload required
I0426 20:30:13.649800       9 stat_collector.go:34] changing prometheus collector from  to default
I0426 20:30:13.662191       9 leaderelection.go:184] successfully acquired lease kube-system/ingress-controller-leader-addon-http-application-routing
I0426 20:30:13.662292       9 status.go:196] new leader elected: addon-http-application-routing-nginx-ingress-controller-5cxntd6
I0426 20:30:13.763362       9 controller.go:179] ingress backend successfully reloaded...
I0426 21:51:55.249327       9 event.go:218] Event(v1.ObjectReference{Kind:"Ingress", Namespace:"default", Name:"aks-helloworld", UID:"092c9599-499c-11e8-a5e1-0a58ac1f0ef2", APIVersion:"extensions", ResourceVersion:"7346", FieldPath:""}): type: 'Normal' reason: 'CREATE' Ingress default/aks-helloworld
W0426 21:51:57.908771       9 controller.go:775] service default/aks-helloworld does not have any active endpoints
I0426 21:51:57.908951       9 controller.go:170] backend reload required
I0426 21:51:58.042932       9 controller.go:179] ingress backend successfully reloaded...
167.220.24.46 - [167.220.24.46] - - [26/Apr/2018:21:53:20 +0000] "GET / HTTP/1.1" 200 234 "" "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Trident/5.0)" 197 0.001 [default-aks-helloworld-80] 10.244.0.13:8080 234 0.004 200
```

## <a name="clean-up"></a>Rensa

Ta bort de associerade Kubernetes-objekten som skapats i den här artikeln med hjälp av `kubectl delete` .

```bash
kubectl delete -f samples-http-application-routing.yaml
```

Exempel resultatet visar Kubernetes-objekt har tagits bort.

```bash
$ kubectl delete -f samples-http-application-routing.yaml

deployment "aks-helloworld" deleted
service "aks-helloworld" deleted
ingress "aks-helloworld" deleted
```

## <a name="next-steps"></a>Nästa steg

Information om hur du installerar en HTTPS-skyddad ingångs styrenhet i AKS finns i https-ingångar [på Azure Kubernetes service (AKS)][ingress-https].

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-show]: /cli/azure/aks#az-aks-show
[ingress-https]: ./ingress-tls.md
[az-aks-enable-addons]: /cli/azure/aks#az-aks-enable-addons
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials

<!-- LINKS - external -->
[dns-pricing]: https://azure.microsoft.com/pricing/details/dns/
[external-dns]: https://github.com/kubernetes-incubator/external-dns
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[ingress]: https://kubernetes.io/docs/concepts/services-networking/ingress/
[ingress-resource]: https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource
