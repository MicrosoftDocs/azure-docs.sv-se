---
title: Ingress Controller på internt nätverk
titleSuffix: Azure Kubernetes Service
description: Lär dig hur du installerar och konfigurerar en NGINX ingress Controller för ett internt, privat nätverk i ett Azure Kubernetes service-kluster (AKS).
services: container-service
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: f7bb917387accb986f1fc97a9ebc752281234b35
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88272624"
---
# <a name="create-an-ingress-controller-to-an-internal-virtual-network-in-azure-kubernetes-service-aks"></a>Skapa en ingångs kontroll för ett internt virtuellt nätverk i Azure Kubernetes service (AKS)

En ingress-kontrollant är en del av programvaran som tillhandahåller omvänd proxy, konfigurerbar trafikroutning och TLS-Avslut för Kubernetes-tjänster. Kubernetes ingress-resurser används för att konfigurera inkommande regler och vägar för enskilda Kubernetes-tjänster. Med hjälp av en ingress-kontrollant och ingress-regler kan en IP-adress användas för att dirigera trafik till flera tjänster i ett Kubernetes-kluster.

Den här artikeln visar hur du distribuerar [nginx ingress-kontrollanten][nginx-ingress] i ett Azure Kubernetes service-kluster (AKS). Ingångs styrenheten är konfigurerad på ett internt, privat virtuellt nätverk och IP-adress. Ingen extern åtkomst tillåts. Två program körs sedan i AKS-klustret, som var och en är tillgänglig över den enskilda IP-adressen.

Du kan även:

- [Skapa en grundläggande ingångs kontroll med extern nätverks anslutning][aks-ingress-basic]
- [Aktivera routnings tillägget för HTTP-program][aks-http-app-routing]
- [Skapa en ingångs kontroll enhet som använder dina egna TLS-certifikat][aks-ingress-own-tls]
- Skapa en ingångs kontroll enhet som använder kryptera för att automatiskt generera TLS [-certifikat med en dynamisk offentlig IP-adress][aks-ingress-tls] eller [med en statisk offentlig IP-adress][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln använder [Helm 3][helm] för att installera nginx ingress-kontrollanten. Kontrol lera att du använder den senaste versionen av Helm och att du har åtkomst till *stabil* Helm-lagringsplats. Mer information om hur du konfigurerar och använder Helm finns i [installera program med Helm i Azure Kubernetes service (AKS)][use-helm].

Den här artikeln kräver också att du kör Azure CLI-version 2.0.64 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Skapa en ingångs kontroll enhet

Som standard skapas en NGINX ingångs kontroll med en dynamisk offentlig IP-adresstilldelning. Ett vanligt konfigurations krav är att använda ett internt, privat nätverk och IP-adress. Med den här metoden kan du begränsa åtkomsten till dina tjänster till interna användare utan någon extern åtkomst.

Skapa en fil med namnet *Internal-ingress. yaml* med hjälp av följande exempel manifest fil. I det här exemplet tilldelas *10.240.0.42* till *loadBalancerIP* -resursen. Ange din egna interna IP-adress för användning med ingångs styrenheten. Kontrol lera att den här IP-adressen inte redan används i det virtuella nätverket. Om du använder ett befintligt virtuellt nätverk och undernät måste du också konfigurera ditt AKS-kluster med rätt behörigheter för att hantera det virtuella nätverket och under nätet. Mer information finns i [använda Kubernetes-nätverk med dina egna IP-adressintervall i Azure Kubernetes service (AKS)][aks-configure-kubenet-networking] eller [Konfigurera Azure cni-nätverk i Azure KUBERNETES service (AKS)][aks-configure-advanced-networking] .

```yaml
controller:
  service:
    loadBalancerIP: 10.240.0.42
    annotations:
      service.beta.kubernetes.io/azure-load-balancer-internal: "true"
```

Distribuera nu *nginx-ingress-* diagrammet med Helm. Om du vill använda manifest filen som skapades i föregående steg lägger du till `-f internal-ingress.yaml` parametern. För ytterligare redundans distribueras två repliker av NGINX-ingresskontrollanterna med parametern `--set controller.replicaCount`. Se till att det finns fler än en nod i ditt AKS-kluster för att få full nytta av att köra repliker av ingångs styrenheten.

Ingresskontrollanten måste också schemaläggas på en Linux-nod. Windows Server-noder bör inte köra ingresskontrollanten. En nodväljare anges med parametern `--set nodeSelector` för att instruera Kubernetes-schemaläggaren att köra NGINX-ingresskontrollanten på en Linux-baserad nod.

> [!TIP]
> I följande exempel skapas ett Kubernetes-namnområde för de ingress-resurser som heter *ingress-Basic*. Ange ett namn område för din egen miljö efter behov. Om ditt AKS-kluster inte är RBAC-aktiverat lägger du till dem `--set rbac.create=false` i Helm-kommandona.

> [!TIP]
> Om du vill aktivera [IP-konservering för klient källa][client-source-ip] för förfrågningar till behållare i klustret, lägger `--set controller.service.externalTrafficPolicy=Local` du till det i Helm install-kommandot. Klientens käll-IP lagras i begär ande huvudet under *X-forwarded – for*. TLS-vidarekoppling fungerar inte när du använder en ingångs kontroll för att aktivera IP-konservering i klient källan.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the official stable repository
helm repo add stable https://kubernetes-charts.storage.googleapis.com/

# Use Helm to deploy an NGINX ingress controller
helm install nginx-ingress stable/nginx-ingress \
    --namespace ingress-basic \
    -f internal-ingress.yaml \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

När Kubernetes-tjänsten för belastnings utjämning skapas för NGINX-ingångs styrenheten tilldelas den interna IP-adressen. Använd kommandot för att hämta den offentliga IP-adressen `kubectl get service` .

```console
kubectl get service -l app=nginx-ingress --namespace ingress-basic
```

Det tar några minuter för IP-adressen att tilldelas till tjänsten, vilket visas i följande exempel på utdata:

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                             TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)                      AGE
nginx-ingress-controller         LoadBalancer   10.0.61.144    10.240.0.42   80:30386/TCP,443:32276/TCP   6m2s
nginx-ingress-default-backend    ClusterIP      10.0.192.145   <none>        80/TCP                       6m2s
```

Inga ingångs regler har skapats ännu. därför visas sidan NGINX ingress Controller standard 404 om du bläddrar till den interna IP-adressen. Ingress-regler konfigureras i följande steg.

## <a name="run-demo-applications"></a>Köra demo program

Om du vill se en ingångs kontroll i praktiken kör du två demo program i ditt AKS-kluster. I det här exemplet använder du `kubectl apply` för att distribuera två instanser av ett enkelt *Hello World* -program.

Skapa en *AKS-HelloWorld. yaml* -fil och kopiera i följande exempel yaml:

```yml
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
        image: neilpeterson/aks-helloworld:v1
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
```

Skapa en *ingress-demo. yaml-* fil och kopiera i följande exempel yaml:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ingress-demo
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ingress-demo
  template:
    metadata:
      labels:
        app: ingress-demo
    spec:
      containers:
      - name: ingress-demo
        image: neilpeterson/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "AKS Ingress Demo"
---
apiVersion: v1
kind: Service
metadata:
  name: ingress-demo
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: ingress-demo
```

Kör de två demo programmen med `kubectl apply` :

```console
kubectl apply -f aks-helloworld.yaml --namespace ingress-basic
kubectl apply -f ingress-demo.yaml --namespace ingress-basic
```

## <a name="create-an-ingress-route"></a>Skapa en ingress-väg

Båda programmen körs nu på ditt Kubernetes-kluster. Skapa en Kubernetes ingress-resurs för att dirigera trafik till varje program. I ingress-resursen konfigureras de regler som dirigerar trafik till ett av de två programmen.

I följande exempel dirigeras trafik till adressen `http://10.240.0.42/` till tjänsten med namnet `aks-helloworld` . Trafik till adressen `http://10.240.0.42/hello-world-two` dirigeras till `ingress-demo` tjänsten.

Skapa en fil med namnet `hello-world-ingress.yaml` och kopiera i följande exempel yaml.

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/use-regex: "true"
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /hello-world-one(/|$)(.*)
      - backend:
          serviceName: ingress-demo
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /(.*)
```

Skapa den inkommande resursen med hjälp av `kubectl apply -f hello-world-ingress.yaml` kommandot.

```console
kubectl apply -f hello-world-ingress.yaml
```

Följande exempel på utdata visar att resursen ingress skapas.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-controller"></a>Testa ingångs styrenheten

Om du vill testa vägarna för ingångs styrenheten bläddrar du till de två programmen med en webb klient. Vid behov kan du snabbt testa den här interna funktionen från en POD i AKS-klustret. Skapa en test-Pod och koppla en terminalsession till den:

```console
kubectl run -it --rm aks-ingress-test --image=debian --namespace ingress-basic
```

Installera `curl` i pod med `apt-get` :

```console
apt-get update && apt-get install -y curl
```

Nu kan du komma åt adressen till din Kubernetes ingångs hanterare med `curl` , till exempel *http://10.240.0.42* . Ange din egna interna IP-adress som anges när du distribuerade ingångs styrenheten i det första steget i den här artikeln.

```console
curl -L http://10.240.0.42
```

Ingen ytterligare sökväg tillhandahölls med adressen, så ingångs styrenhetens standardinställning för */* vägen. Det första demo programmet returneras, som du ser i följande komprimerade exempel utdata:

```
$ curl -L http://10.240.0.42

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

Lägg nu till */Hello-World-Two* sökväg till adressen, till exempel *http://10.240.0.42/hello-world-two* . Det andra demonstrations programmet med den anpassade rubriken returneras, som du ser i följande komprimerade exempel utdata:

```
$ curl -L -k http://10.240.0.42/hello-world-two

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>AKS Ingress Demo</title>
[...]
```

## <a name="clean-up-resources"></a>Rensa resurser

I den här artikeln används Helm för att installera ingångs komponenterna. När du distribuerar ett Helm-diagram skapas ett antal Kubernetes-resurser. De här resurserna omfattar poddar, distributioner och tjänster. Om du vill rensa dessa resurser kan du antingen ta bort hela exempel namnområdet eller enskilda resurser.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Ta bort exempel namn området och alla resurser

Om du vill ta bort hela exempel namnområdet använder du `kubectl delete` kommandot och anger namn på namn området. Alla resurser i namn området tas bort.

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>Ta bort resurser individuellt

Alternativt är en mer detaljerad metod att ta bort de enskilda resurserna som skapats. Visar en lista med Helm-versioner med `helm list` kommandot. 

```console
helm list --namespace ingress-basic
```

Leta efter diagram med namnet *nginx – ingress* och *AKS-HelloWorld*, som du ser i följande exempel resultat:

```
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
nginx-ingress           ingress-basic   1               2020-01-06 19:55:46.358275 -0600 CST    deployed        nginx-ingress-1.27.1    0.26.1  
```

Avinstallera versioner med `helm uninstall` kommandot.

```console
helm uninstall nginx-ingress --namespace ingress-basic
```

I följande exempel avinstalleras NGINX ingress-distributionen.

```
$ helm uninstall nginx-ingress --namespace ingress-basic

release "nginx-ingress" uninstalled
```

Ta sedan bort de två exempel programmen:

```console
kubectl delete -f aks-helloworld.yaml --namespace ingress-basic
kubectl delete -f ingress-demo.yaml --namespace ingress-basic
```

Ta bort ingångs vägen som riktar sig mot trafik till exempel apparna:

```console
kubectl delete -f hello-world-ingress.yaml
```

Slutligen kan du ta bort själva namn området. Använd `kubectl delete` kommandot och ange namn på namn område:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln ingår några externa komponenter i AKS. Mer information om dessa komponenter finns i följande projekt sidor:

- [Helm CLI][helm-cli]
- [NGINX ingress-styrenhet][nginx-ingress]

Du kan även:

- [Skapa en grundläggande ingångs kontroll med extern nätverks anslutning][aks-ingress-basic]
- [Aktivera routnings tillägget för HTTP-program][aks-http-app-routing]
- [Skapa en ingångs kontroll enhet med en dynamisk offentlig IP-adress och konfigurera låt oss kryptera för att automatiskt generera TLS-certifikat][aks-ingress-tls]
- [Skapa en ingångs kontroll enhet med en statisk offentlig IP-adress och konfigurera låt oss kryptera för att automatiskt generera TLS-certifikat][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-cli]: ./kubernetes-helm.md
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[client-source-ip]: concepts-network.md#ingress-controllers
[aks-configure-kubenet-networking]: configure-kubenet.md
[aks-configure-advanced-networking]: configure-azure-cni.md