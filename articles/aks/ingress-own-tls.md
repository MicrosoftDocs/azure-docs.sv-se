---
title: Använda dina TLS-certifikat för ingress
titleSuffix: Azure Kubernetes Service
description: Lär dig hur du installerar och konfigurerar en NGINX-ingresskontrollant som använder dina egna certifikat i ett Azure Kubernetes Service-kluster (AKS).
services: container-service
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: d3d554fbbbbb16910b76e7eb56afd13c436f8cf5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779947"
---
# <a name="create-an-https-ingress-controller-and-use-your-own-tls-certificates-on-azure-kubernetes-service-aks"></a>Skapa en inkommande styrenhet för HTTPS och använd dina egna TLS-certifikat på Azure Kubernetes Service (AKS)

En ingress-kontrollant är en del av programvaran som tillhandahåller omvänd proxy, konfigurerbar trafikroutning och TLS-Avslut för Kubernetes-tjänster. Kubernetes ingress-resurser används för att konfigurera inkommande regler och vägar för enskilda Kubernetes-tjänster. Med hjälp av en ingress-kontrollant och ingress-regler kan en IP-adress användas för att dirigera trafik till flera tjänster i ett Kubernetes-kluster.

Den här artikeln visar hur du distribuerar [NGINX-ingress-kontrollanten][nginx-ingress] i ett Azure Kubernetes Service-kluster (AKS). Du genererar dina egna certifikat och skapar en Kubernetes-hemlighet för användning med ingressvägen. Slutligen körs två program i AKS-klustret, som var och en kan nås via en enda IP-adress.

Du kan även:

- [Skapa en grundläggande ingressstyrenhet med extern nätverksanslutning][aks-ingress-basic]
- [Aktivera tillägget för HTTP-programroutning][aks-http-app-routing]
- [Skapa en ingress-kontrollant som använder ett internt, privat nätverk och en IP-adress][aks-ingress-internal]
- Skapa en ingress-kontrollant som använder Let's Encrypt för att automatiskt generera TLS-certifikat med en dynamisk [offentlig IP-adress][aks-ingress-tls] eller [med en statisk offentlig IP-adress][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln [använder Helm 3][helm] för att installera NGINX-ingress-kontrollanten. Kontrollera att du använder den senaste versionen av Helm och har åtkomst till *Helm-lagringsplatsen ingress-nginx.* Uppgraderingsanvisningar finns i [installationsdokumenten för Helm.][helm-install] Mer information om hur du konfigurerar och använder Helm finns [i Installera program med Helm i Azure Kubernetes Service (AKS).][use-helm]

Den här artikeln kräver också att du kör Azure CLI version 2.0.64 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Skapa en ingress-kontrollant

För att skapa ingress-kontrollanten använder `Helm` du för att installera *nginx-ingress*. För ytterligare redundans distribueras två repliker av NGINX-ingresskontrollanterna med parametern `--set controller.replicaCount`. Om du vill dra full nytta av att köra repliker av ingress-kontrollanten kontrollerar du att det finns fler än en nod i ditt AKS-kluster.

Ingresskontrollanten måste också schemaläggas på en Linux-nod. Windows Server-noder bör inte köra ingresskontrollanten. En nodväljare anges med parametern `--set nodeSelector` för att instruera Kubernetes-schemaläggaren att köra NGINX-ingresskontrollanten på en Linux-baserad nod.

> [!TIP]
> I följande exempel skapas ett Kubernetes-namnområde för ingressresurserna med namnet *ingress-basic*. Ange ett namnområde för din egen miljö efter behov. Om ditt AKS-kluster inte är Kubernetes RBAC aktiverat lägger du till `--set rbac.create=false` i Helm-kommandona.

> [!TIP]
> Om du vill aktivera bevarande av [klientkällans IP-adress][client-source-ip] för begäranden till containrar i klustret lägger du `--set controller.service.externalTrafficPolicy=Local` till i installationskommandot helm. Klientens käll-IP lagras i begärandehuvudet under *X-Forwarded-For*. När du använder en ingress-kontrollant med klientkällans IP-bevarande aktiverat fungerar inte TLS-genomgående.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the ingress-nginx repository
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

# Use Helm to deploy an NGINX ingress controller
helm install nginx-ingress ingress-nginx/ingress-nginx \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.admissionWebhooks.patch.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Under installationen skapas en offentlig IP-adress i Azure för ingress-kontrollanten. Den här offentliga IP-adressen är statisk för ingress-kontrollantens livslängd. Om du tar bort ingress-kontrollanten går tilldelningen av den offentliga IP-adressen förlorad. Om du sedan skapar ytterligare en ingress-kontrollant tilldelas en ny offentlig IP-adress. Om du vill behålla användningen av den offentliga IP-adressen kan du i stället skapa en [ingress-kontrollant med en statisk offentlig IP-adress][aks-ingress-static-tls].

Hämta den offentliga IP-adressen med `kubectl get service` kommandot .

```console
kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller
```

Det tar några minuter för IP-adressen att tilldelas till tjänsten.

```
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Anteckna den här offentliga IP-adressen eftersom den används i det sista steget för att testa distributionen.

Inga inkommande regler har skapats ännu. Om du bläddrar till den offentliga IP-adressen visas NGINX-ingresskontrollantens standardsida 404.

## <a name="generate-tls-certificates"></a>Generera TLS-certifikat

I den här artikeln ska vi generera ett själv signerat certifikat med `openssl` . För produktionsanvändning bör du begära ett betrott, signerat certifikat via en provider eller din egen certifikatutfärdare (CA). I nästa steg genererar du en  Kubernetes-hemlighet med hjälp av TLS-certifikatet och den privata nyckeln som genereras av OpenSSL.

I följande exempel genereras ett 2 048-bitars RSA X509-certifikat som är giltigt i 365 dagar med namnet *aks-ingress-tls.crt*. Den privata nyckelfilen heter *aks-ingress-tls.key*. En Kubernetes TLS-hemlighet kräver båda dessa filer.

Den här artikeln *demo.azure.com* ämnesnamnet och behöver inte ändras. För produktionsanvändning anger du egna organisationsvärden för `-subj` parametern :

```console
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -out aks-ingress-tls.crt \
    -keyout aks-ingress-tls.key \
    -subj "/CN=demo.azure.com/O=aks-ingress-tls"
```

## <a name="create-kubernetes-secret-for-the-tls-certificate"></a>Skapa Kubernetes-hemlighet för TLS-certifikat

Om du vill tillåta Kubernetes att använda TLS-certifikatet och den privata nyckeln för ingress-kontrollanten skapar och använder du en hemlighet. Hemligheten definieras en gång och använder certifikatet och nyckelfilen som skapades i föregående steg. Sedan refererar du till den här hemligheten när du definierar ingressvägar.

I följande exempel skapas ett hemligt *namn aks-ingress-tls*:

```console
kubectl create secret tls aks-ingress-tls \
    --namespace ingress-basic \
    --key aks-ingress-tls.key \
    --cert aks-ingress-tls.crt
```

## <a name="run-demo-applications"></a>Köra demoprogram

En ingresskontrollant och en hemlighet med ditt certifikat har konfigurerats. Nu ska vi köra två demoprogram i ditt AKS-kluster. I det här exemplet används Helm för att distribuera två instanser av ett enkelt Hello World-program.

Om du vill se hur ingress-kontrollanten används kör du två demoprogram i ditt AKS-kluster. I det här exemplet använder du `kubectl apply` för att distribuera två instanser av ett enkelt Hello *World-program.*

Skapa en *aks-helloworld.yaml-fil* och kopiera följande YAML-exempel:

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
```

Skapa en *ingress-demo.yaml-fil* och kopiera följande YAML-exempel:

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
        image: mcr.microsoft.com/azuredocs/aks-helloworld:v1
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

Kör de två demoprogrammen med `kubectl apply` :

```console
kubectl apply -f aks-helloworld.yaml --namespace ingress-basic
kubectl apply -f ingress-demo.yaml --namespace ingress-basic
```

## <a name="create-an-ingress-route"></a>Skapa en ingressväg

Båda programmen körs nu i kubernetes-klustret, men de är konfigurerade med en tjänst av typen `ClusterIP` . Därför är programmen inte tillgängliga från Internet. Om du vill göra dem offentligt tillgängliga skapar du en ingressresurs för Kubernetes. Ingressresursen konfigurerar reglerna som dirigerar trafik till ett av de två programmen.

I följande exempel dirigeras trafik till adressen `https://demo.azure.com/` till tjänsten med namnet `aks-helloworld` . Trafik till adressen `https://demo.azure.com/hello-world-two` dirigeras till `ingress-demo` tjänsten. I den här artikeln behöver du inte ändra dessa demovärdnamn. För produktionsanvändning anger du de namn som anges som en del av processen för certifikatbegäran och generering.

> [!TIP]
> Om värdnamnet som angavs under certifikatbegäranprocessen, CN-namnet, inte matchar värden som definierats i ingressvägen, visar ingress-kontrollanten en varning från *Kubernetes Ingress Controller Fake Certificate.* Kontrollera att certifikat- och ingressvägsvärdnamnen matchar.

Avsnittet *tls* talar om för ingressvägen att använda hemligheten med namnet *aks-ingress-tls* för värden *demo.azure.com*. För produktionsanvändning anger du återigen din egen värdadress.

Skapa en fil med `hello-world-ingress.yaml` namnet och kopiera följande YAML-exempel.

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/use-regex: "true"
    nginx.ingress.kubernetes.io/rewrite-target: /$2
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

Skapa ingressresursen med `kubectl apply -f hello-world-ingress.yaml` kommandot .

```console
kubectl apply -f hello-world-ingress.yaml
```

Exempelutdata visar att ingressresursen har skapats.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-configuration"></a>Testa ingresskonfigurationen

Om du vill testa certifikaten med *vår falska demo.azure.com* värd använder du och anger `curl` parametern *--resolve.* Med den här parametern kan *demo.azure.com* mappa namnet till den offentliga IP-adressen för ingress-kontrollanten. Ange den offentliga IP-adressen för din egen ingress-kontrollant, som du ser i följande exempel:

```
curl -v -k --resolve demo.azure.com:443:EXTERNAL_IP https://demo.azure.com
```

Ingen ytterligare sökväg angavs med adressen, så ingress-kontrollanten använder vägen som */* standard. Det första demoprogrammet returneras, som du ser i följande komprimerade exempelutdata:

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

Parametern *-v* i kommandot `curl` matar ut utförlig information, inklusive det mottagna TLS-certifikatet. Halvvägs genom curl-utdata kan du kontrollera att ditt eget TLS-certifikat har använts. Parametern *-k* fortsätter att läsa in sidan även om vi använder ett själv signerat certifikat. Följande exempel visar att *utfärdaren: CN=demo.azure.com; O=aks-ingress-tls-certifikatet* användes:

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

Lägg nu *till /hello-world-two-sökvägen* till adressen, till exempel `https://demo.azure.com/hello-world-two` . Det andra demoprogrammet med den anpassade rubriken returneras, som du ser i följande komprimerade exempelutdata:

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

Den här artikeln använde Helm för att installera ingresskomponenterna och exempelapparna. När du distribuerar ett Helm-diagram skapas ett antal Kubernetes-resurser. Dessa resurser omfattar poddar, distributioner och tjänster. Om du vill rensa resurserna kan du antingen ta bort hela exempelnamnområdet eller de enskilda resurserna.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Ta bort exempelnamnområdet och alla resurser

Om du vill ta bort hela exempelnamnrymden använder `kubectl delete` du kommandot och anger namnet på namnområdet. Alla resurser i namnområdet tas bort.

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>Ta bort resurser individuellt

En mer detaljerad metod kan också vara att ta bort de enskilda resurser som skapats. Visa en lista över Helm-versioner med `helm list` kommandot . 

```console
helm list --namespace ingress-basic
```

Leta efter diagram med *namnet nginx-ingress* enligt följande exempelutdata:

```
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
nginx-ingress           ingress-basic   1               2020-01-06 19:55:46.358275 -0600 CST    deployed        nginx-ingress-1.27.1    0.26.1 
```

Avinstallera versionerna med `helm uninstall` kommandot . 

```console
helm uninstall nginx-ingress --namespace ingress-basic
```

I följande exempel avinstalleras NGINX-ingressdistributionen.

```
$ helm uninstall nginx-ingress --namespace ingress-basic

release "nginx-ingress" uninstalled
```

Ta sedan bort de två exempelprogrammen:

```console
kubectl delete -f aks-helloworld.yaml --namespace ingress-basic
kubectl delete -f ingress-demo.yaml --namespace ingress-basic
```

Ta bort den ingressväg som dirigerade trafiken till exempelapparna:

```console
kubectl delete -f hello-world-ingress.yaml
```

Ta bort certifikathemligheten:

```console
kubectl delete secret aks-ingress-tls --namespace ingress-basic
```

Slutligen kan du ta bort själva namnområdet. Använd kommandot `kubectl delete` och ange namnet på namnområdet:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Nästa steg

Den här artikeln innehåller några externa komponenter till AKS. Mer information om dessa komponenter finns på följande projektsidor:

- [Helm CLI][helm-cli]
- [NGINX-ingresskontrollant][nginx-ingress]

Du kan även:

- [Skapa en grundläggande ingress-kontrollant med extern nätverksanslutning][aks-ingress-basic]
- [Aktivera tillägget för HTTP-programroutning][aks-http-app-routing]
- [Skapa en ingress-kontrollant som använder ett internt, privat nätverk och en IP-adress][aks-ingress-internal]
- Skapa en ingresskontrollant som använder Let's Encrypt för att automatiskt generera TLS-certifikat med en dynamisk [offentlig IP-adress][aks-ingress-tls] eller [med en statisk offentlig IP-adress][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm-cli]: ./kubernetes-helm.md
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm]: https://helm.sh/
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az_network_public_ip_create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-tls]: ingress-tls.md
[client-source-ip]: concepts-network.md#ingress-controllers
