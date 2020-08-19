---
title: Använd dina TLS-certifikat för ingress
titleSuffix: Azure Kubernetes Service
description: Lär dig hur du installerar och konfigurerar en NGINX ingress-kontrollant som använder dina egna certifikat i ett Azure Kubernetes service-kluster (AKS).
services: container-service
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: 0254b8746c757d98ee98e4815dc53b22d5014765
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88272815"
---
# <a name="create-an-https-ingress-controller-and-use-your-own-tls-certificates-on-azure-kubernetes-service-aks"></a>Skapa en inkommande styrenhet för HTTPS och använd dina egna TLS-certifikat på Azure Kubernetes Service (AKS)

En ingress-kontrollant är en del av programvaran som tillhandahåller omvänd proxy, konfigurerbar trafikroutning och TLS-Avslut för Kubernetes-tjänster. Kubernetes ingress-resurser används för att konfigurera inkommande regler och vägar för enskilda Kubernetes-tjänster. Med hjälp av en ingress-kontrollant och ingress-regler kan en IP-adress användas för att dirigera trafik till flera tjänster i ett Kubernetes-kluster.

Den här artikeln visar hur du distribuerar [nginx ingress-kontrollanten][nginx-ingress] i ett Azure Kubernetes service-kluster (AKS). Du genererar dina egna certifikat och skapar en Kubernetes-hemlighet för användning med ingångs vägen. Slutligen körs två program i AKS-klustret, som var och en är tillgänglig över en enskild IP-adress.

Du kan även:

- [Skapa en grundläggande ingångs kontroll med extern nätverks anslutning][aks-ingress-basic]
- [Aktivera routnings tillägget för HTTP-program][aks-http-app-routing]
- [Skapa en ingångs kontroll enhet som använder ett internt, privat nätverk och IP-adress][aks-ingress-internal]
- Skapa en ingångs kontroll enhet som använder kryptera för att automatiskt generera TLS [-certifikat med en dynamisk offentlig IP-adress][aks-ingress-tls] eller [med en statisk offentlig IP-adress][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln använder [Helm 3][helm] för att installera nginx ingress-kontrollanten. Kontrol lera att du använder den senaste versionen av Helm och att du har åtkomst till *stabil* Helm-lagringsplats. Anvisningar om hur du uppgraderar finns i [installations dokument för Helm][helm-install]. Mer information om hur du konfigurerar och använder Helm finns i [installera program med Helm i Azure Kubernetes service (AKS)][use-helm].

Den här artikeln kräver också att du kör Azure CLI-version 2.0.64 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Skapa en ingångs kontroll enhet

Om du vill skapa en ingångs kontroll, använder `Helm` du för att installera *nginx-ingress*. För ytterligare redundans distribueras två repliker av NGINX-ingresskontrollanterna med parametern `--set controller.replicaCount`. Se till att det finns fler än en nod i ditt AKS-kluster för att få full nytta av att köra repliker av ingångs styrenheten.

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
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Under installationen skapas en offentlig Azure-IP-adress för ingångs styrenheten. Den här offentliga IP-adressen är statisk för den ingångs kontrollens livs längd. Om du tar bort ingångs kontrollen går den offentliga IP-adresstilldelning förlorad. Om du sedan skapar ytterligare en ingångs kontroll, tilldelas en ny offentlig IP-adress. Om du vill behålla användningen av den offentliga IP-adressen kan du i stället skapa en ingångs [kontroll enhet med en statisk offentlig IP-adress][aks-ingress-static-tls].

Använd kommandot för att hämta den offentliga IP-adressen `kubectl get service` .

```console
kubectl get service -l app=nginx-ingress --namespace ingress-basic
```

Det tar några minuter innan IP-adressen tilldelas till tjänsten.

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                             TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)                      AGE
nginx-ingress-controller         LoadBalancer   10.0.61.144    EXTERNAL_IP   80:30386/TCP,443:32276/TCP   6m2s
nginx-ingress-default-backend    ClusterIP      10.0.192.145   <none>        80/TCP                       6m2s
```

Anteckna den här offentliga IP-adressen, eftersom den används i det sista steget för att testa distributionen.

Inga ingress-regler har skapats ännu. Om du bläddrar till den offentliga IP-adressen visas sidan NGINX ingångs styrenhetens standard 404.

## <a name="generate-tls-certificates"></a>Generera TLS-certifikat

I den här artikeln ska vi generera ett självsignerat certifikat med `openssl` . För produktions användningen bör du begära ett betrott signerat certifikat via en provider eller din egen certifikat utfärdare (CA). I nästa steg genererar du en Kubernetes- *hemlighet* med TLS-certifikatet och den privata nyckeln som genereras av OpenSSL.

I följande exempel genereras ett 2048-bitars RSA X509-certifikat giltigt i 365 dagar med namnet *AKS-ingress-TLS. CRT*. Den privata nyckel filen heter *AKS-ingress-TLS. Key*. En Kubernetes TLS-hemlighet kräver båda dessa filer.

Den här artikeln fungerar med det *demo.Azure.com* ämnets gemensamma namn och behöver inte ändras. För produktions användning anger du dina egna organisations värden för `-subj` parametern:

```console
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -out aks-ingress-tls.crt \
    -keyout aks-ingress-tls.key \
    -subj "/CN=demo.azure.com/O=aks-ingress-tls"
```

## <a name="create-kubernetes-secret-for-the-tls-certificate"></a>Skapa Kubernetes-hemlighet för TLS-certifikat

Om du vill tillåta att Kubernetes använder TLS-certifikatet och den privata nyckeln för ingångs styrenheten skapar du och använder en hemlighet. Hemligheten definieras en gång och använder certifikatet och nyckel filen som skapades i föregående steg. Du refererar sedan till den här hemligheten när du definierar ingress-vägar.

I följande exempel skapas ett hemligt namn *AKS – ingress-TLS*:

```console
kubectl create secret tls aks-ingress-tls \
    --namespace ingress-basic \
    --key aks-ingress-tls.key \
    --cert aks-ingress-tls.crt
```

## <a name="run-demo-applications"></a>Köra demo program

En ingångs kontroll och en hemlighet med ditt certifikat har kon figurer ATS. Nu ska vi köra två demo program i ditt AKS-kluster. I det här exemplet används Helm för att distribuera två instanser av ett enkelt "Hello World"-program.

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

Båda programmen körs nu på ditt Kubernetes-kluster, men de har kon figurer ATS med en tjänst av typen `ClusterIP` . Därför är programmen inte tillgängliga från Internet. Skapa en Kubernetes ingress-resurs för att göra dem offentligt tillgängliga. I ingress-resursen konfigureras de regler som dirigerar trafik till ett av de två programmen.

I följande exempel dirigeras trafik till adressen `https://demo.azure.com/` till tjänsten med namnet `aks-helloworld` . Trafik till adressen `https://demo.azure.com/hello-world-two` dirigeras till `ingress-demo` tjänsten. I den här artikeln behöver du inte ändra namnen på dessa demonstrations värdar. För produktions användning anger du namnen som anges som en del av certifikatbegäran och generations processen.

> [!TIP]
> Om det värdnamn som anges under processen för certifikatbegäran, CN-namnet, inte matchar den angivna värden i din ingångs väg visas ingångs enheten i en Kubernetes ingångs *kontroll enhet falsk certifikat* varning. Kontrol lera att ditt certifikat och att dirigera värdnamn matchar.

Avsnittet *TLS* instruerar ingångs vägen att använda hemligheten med namnet *AKS-ingress-tls* för värden *demo.Azure.com*. För produktions användning anger du din egen värd adress.

Skapa en fil med namnet `hello-world-ingress.yaml` och kopiera i följande exempel yaml.

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/use-regex: "true"
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  tls:
  - hosts:
    - demo.azure.com
    secretName: aks-ingress-tls
  rules:
  - host: demo.azure.com
    http:
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

Exempel resultatet visar ingångs resursen skapas.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-configuration"></a>Testa ingress-konfigurationen

Om du vill testa certifikaten med vår falska *demo.Azure.com* -värd, använder `curl` du och anger parametern *--* parameter. Med den här parametern kan du mappa *demo.Azure.com* -namnet till den offentliga IP-adressen för din ingångs kontroll. Ange den offentliga IP-adressen för din egen ingångs kontroll, som du ser i följande exempel:

```
curl -v -k --resolve demo.azure.com:443:EXTERNAL_IP https://demo.azure.com
```

Ingen ytterligare sökväg tillhandahölls med adressen, så ingångs styrenhetens standardinställning för */* vägen. Det första demo programmet returneras, som du ser i följande komprimerade exempel utdata:

```
$ curl -v -k --resolve demo.azure.com:443:EXTERNAL_IP https://demo.azure.com

[...]
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

Parametern *-v* i vårt `curl` kommando matar utförlig information, inklusive det mottagna TLS-certifikatet. Halvvägs genom dina typografiska utdata kan du kontrol lera att ditt eget TLS-certifikat användes. Parametern *-k* fortsätter att läsa in sidan även om vi använder ett självsignerat certifikat. I följande exempel visas att *utfärdaren: CN = demo. Azure. com; O = AKS-ingångs-TLS-* certifikat användes:

```
[...]
* Server certificate:
*  subject: CN=demo.azure.com; O=aks-ingress-tls
*  start date: Oct 22 22:13:54 2018 GMT
*  expire date: Oct 22 22:13:54 2019 GMT
*  issuer: CN=demo.azure.com; O=aks-ingress-tls
*  SSL certificate verify result: self signed certificate (18), continuing anyway.
[...]
```

Lägg nu till */Hello-World-Two* sökväg till adressen, till exempel `https://demo.azure.com/hello-world-two` . Det andra demonstrations programmet med den anpassade rubriken returneras, som du ser i följande komprimerade exempel utdata:

```
$ curl -v -k --resolve demo.azure.com:443:EXTERNAL_IP https://demo.azure.com/hello-world-two

[...]
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>AKS Ingress Demo</title>
[...]
```

## <a name="clean-up-resources"></a>Rensa resurser

I den här artikeln används Helm för att installera ingångs komponenter och exempel appar. När du distribuerar ett Helm-diagram skapas ett antal Kubernetes-resurser. Dessa resurser omfattar poddar, distributioner och tjänster. Om du vill rensa dessa resurser kan du antingen ta bort hela exempel namnområdet eller enskilda resurser.

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

Leta efter diagram med namnet *nginx – ingress* som visas i följande exempel på utdata:

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

Ta bort certifikat hemligheten:

```console
kubectl delete secret aks-ingress-tls
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
- [Skapa en ingångs kontroll enhet som använder ett internt, privat nätverk och IP-adress][aks-ingress-internal]
- Skapa en ingångs kontroll enhet som använder kryptera för att automatiskt generera TLS [-certifikat med en dynamisk offentlig IP-adress][aks-ingress-tls] eller [med en statisk offentlig IP-adress][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm-cli]: ./kubernetes-helm.md
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm]: https://helm.sh/
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-tls]: ingress-tls.md
[client-source-ip]: concepts-network.md#ingress-controllers
