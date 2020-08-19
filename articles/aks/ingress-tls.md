---
title: Skapa ingress med automatisk TLS
titleSuffix: Azure Kubernetes Service
description: Lär dig hur du installerar och konfigurerar en NGINX ingångs hanterare som använder kryptera för att skapa TLS-certifikat i ett Azure Kubernetes service-kluster (AKS).
services: container-service
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: c86b4e921dce6258ac585375e686bec5fa44b211
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88508965"
---
# <a name="create-an-https-ingress-controller-on-azure-kubernetes-service-aks"></a>Skapa en HTTPS ingress-styrenhet på Azure Kubernetes service (AKS)

En ingress-kontrollant är en del av programvaran som tillhandahåller omvänd proxy, konfigurerbar trafikroutning och TLS-Avslut för Kubernetes-tjänster. Kubernetes ingress-resurser används för att konfigurera inkommande regler och vägar för enskilda Kubernetes-tjänster. Med hjälp av en ingress-kontrollant och ingress-regler kan en IP-adress användas för att dirigera trafik till flera tjänster i ett Kubernetes-kluster.

Den här artikeln visar hur du distribuerar [nginx ingress-kontrollanten][nginx-ingress] i ett Azure Kubernetes service-kluster (AKS). [Cert Manager-][cert-manager] projektet används för att automatiskt generera och konfigurera att [kryptera][lets-encrypt] certifikat. Slutligen körs två program i AKS-klustret, som var och en är tillgänglig över en enskild IP-adress.

Du kan även:

- [Skapa en grundläggande ingångs kontroll med extern nätverks anslutning][aks-ingress-basic]
- [Aktivera routnings tillägget för HTTP-program][aks-http-app-routing]
- [Skapa en ingångs kontroll enhet som använder ett internt, privat nätverk och IP-adress][aks-ingress-internal]
- [Skapa en ingångs kontroll enhet som använder dina egna TLS-certifikat][aks-ingress-own-tls]
- [Skapa en ingångs kontroll enhet som använder kryptera för att automatiskt generera TLS-certifikat med en statisk offentlig IP-adress][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster kan du läsa snabb starten för AKS [med hjälp av Azure CLI][aks-quickstart-cli] eller [Azure Portal][aks-quickstart-portal].

Den här artikeln förutsätter också att du har [en anpassad domän][custom-domain] med en [DNS-zon][dns-zone] i samma resurs grupp som ditt AKS-kluster.

I den här artikeln används [Helm 3][helm] för att installera nginx ingress-kontrollanten och cert Manager. Kontrol lera att du använder den senaste versionen av Helm och att du har åtkomst till de *stabila* och *jetstack* Helm-databaserna. Anvisningar om hur du uppgraderar finns i [installations dokument för Helm][helm-install]. Mer information om hur du konfigurerar och använder Helm finns i [installera program med Helm i Azure Kubernetes service (AKS)][use-helm].

Den här artikeln kräver också att du kör Azure CLI-version 2.0.64 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Skapa en ingångs kontroll enhet

Om du vill skapa en ingångs kontroll använder du `helm` kommandot för att installera *nginx-ingress*. För ytterligare redundans distribueras två repliker av NGINX-ingresskontrollanterna med parametern `--set controller.replicaCount`. Se till att det finns fler än en nod i ditt AKS-kluster för att få full nytta av att köra repliker av ingångs styrenheten.

Ingresskontrollanten måste också schemaläggas på en Linux-nod. Windows Server-noder bör inte köra ingresskontrollanten. En nodväljare anges med parametern `--set nodeSelector` för att instruera Kubernetes-schemaläggaren att köra NGINX-ingresskontrollanten på en Linux-baserad nod.

> [!TIP]
> I följande exempel skapas ett Kubernetes-namnområde för de ingress-resurser som heter *ingress-Basic*. Ange ett namn område för din egen miljö efter behov.

> [!TIP]
> Om du vill aktivera [IP-konservering för klient källa][client-source-ip] för förfrågningar till behållare i klustret, lägger `--set controller.service.externalTrafficPolicy=Local` du till det i Helm install-kommandot. Klientens käll-IP lagras i begär ande huvudet under *X-forwarded – for*. TLS-vidarekoppling fungerar inte när du använder en ingångs kontroll för att aktivera IP-konservering i klient källan.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the official stable repo
helm repo add stable https://kubernetes-charts.storage.googleapis.com/

# Use Helm to deploy an NGINX ingress controller
helm install nginx stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Under installationen skapas en offentlig Azure-IP-adress för ingångs styrenheten. Den här offentliga IP-adressen är statisk för den ingångs kontrollens livs längd. Om du tar bort ingångs kontrollen går den offentliga IP-adresstilldelning förlorad. Om du sedan skapar ytterligare en ingångs kontroll, tilldelas en ny offentlig IP-adress. Om du vill behålla användningen av den offentliga IP-adressen kan du i stället skapa en ingångs [kontroll enhet med en statisk offentlig IP-adress][aks-ingress-static-tls].

Använd kommandot för att hämta den offentliga IP-adressen `kubectl get service` . Det tar några minuter innan IP-adressen tilldelas till tjänsten.

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                             TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
nginx-ingress-controller                         LoadBalancer   10.0.182.160   MY_EXTERNAL_IP  80:30920/TCP,443:30426/TCP   20m
nginx-ingress-default-backend                    ClusterIP      10.0.255.77    <none>          80/TCP                       20m
```

Inga ingress-regler har skapats ännu. Om du bläddrar till den offentliga IP-adressen visas sidan NGINX ingångs styrenhetens standard 404.

## <a name="add-an-a-record-to-your-dns-zone"></a>Lägg till en A-post i DNS-zonen

Lägg till en *A* -post i DNS-zonen med den externa IP-adressen för nginx-tjänsten med [AZ Network DNS Record-set A Add-Record][az-network-dns-record-set-a-add-record].

```console
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name '*' \
    --ipv4-address MY_EXTERNAL_IP
```

> [!NOTE]
> Alternativt kan du konfigurera ett fullständigt domän namn för IP-adressen för ingångs enheten i stället för en anpassad domän. Observera att det här exemplet gäller för ett bash-gränssnitt.
> 
> ```azurecli-interactive
> # Public IP address of your ingress controller
> IP="MY_EXTERNAL_IP"
> 
> # Name to associate with public IP address
> DNSNAME="demo-aks-ingress"
> 
> # Get the resource-id of the public ip
> PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)
> 
> # Update public ip address with DNS name
> az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
> 
> # Display the FQDN
> az network public-ip show --ids $PUBLICIPID --query "[dnsSettings.fqdn]" --output tsv
> ```

## <a name="install-cert-manager"></a>Installera cert-manager

NGINX-ingresskontrollanten stöder TLS-avslutning. Det finns flera sätt att hämta och konfigurera certifikat för HTTPS. Den här artikeln visar hur du använder [cert Manager][cert-manager], som [ger automatiska funktioner][lets-encrypt] för att skapa och hantera certifikat.

Installera certifikat hanterarens kontrollant:

```console
# Label the ingress-basic namespace to disable resource validation
kubectl label namespace ingress-basic cert-manager.io/disable-validation=true

# Add the Jetstack Helm repository
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

# Install the cert-manager Helm chart
helm install \
  cert-manager \
  --namespace ingress-basic \
  --version v0.16.1 \
  --set installCRDs=true \
  --set nodeSelector."beta\.kubernetes\.io/os"=linux \
  jetstack/cert-manager
```

Mer information om cert Manager-konfiguration finns i [cert-Manager-projektet][cert-manager].

## <a name="create-a-ca-cluster-issuer"></a>Skapa en certifikat utfärdare för kluster utfärdare

Innan certifikaten kan utfärdas kräver certifikat hanteraren en [utfärdare][cert-manager-issuer] eller en [ClusterIssuer][cert-manager-cluster-issuer] -resurs. Dessa Kubernetes-resurser är identiska i funktioner, men `Issuer` fungerar i ett enda namn område och `ClusterIssuer` fungerar i alla namn områden. Mer information finns i dokumentationen om [cert Manager-utfärdaren][cert-manager-issuer] .

Skapa en kluster utfärdare, till exempel `cluster-issuer.yaml` , med hjälp av följande exempel manifest. Uppdatera e-postadressen med en giltig adress från din organisation:

```yaml
apiVersion: cert-manager.io/v1alpha2
kind: ClusterIssuer
metadata:
  name: letsencrypt
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: MY_EMAIL_ADDRESS
    privateKeySecretRef:
      name: letsencrypt
    solvers:
    - http01:
        ingress:
          class: nginx
          podTemplate:
            spec:
              nodeSelector:
                "kubernetes.io/os": linux
```

Använd kommandot för att skapa utfärdaren `kubectl apply` .

```console
kubectl apply -f cluster-issuer.yaml
```

## <a name="run-demo-applications"></a>Köra demo program

En ingångs kontroll och en certifikat hanterings lösning har kon figurer ATS. Nu ska vi köra två demo program i ditt AKS-kluster. I det här exemplet används Helm för att distribuera två instanser av ett enkelt *Hello World* -program.

Om du vill se en ingångs kontroll i praktiken kör du två demo program i ditt AKS-kluster. I det här exemplet använder du `kubectl apply` för att distribuera två instanser av ett enkelt *Hello World* -program.

Skapa en *AKS-HelloWorld-One. yaml-* fil och kopiera i följande exempel yaml:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld-one
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld-one
  template:
    metadata:
      labels:
        app: aks-helloworld-one
    spec:
      containers:
      - name: aks-helloworld-one
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
  name: aks-helloworld-one
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld-one
```

Skapa en *AKS-HelloWorld-två. yaml-* fil och kopiera i följande exempel yaml:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld-two
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld-two
  template:
    metadata:
      labels:
        app: aks-helloworld-two
    spec:
      containers:
      - name: aks-helloworld-two
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
  name: aks-helloworld-two
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld-two
```

Kör de två demo programmen med `kubectl apply` :

```console
kubectl apply -f aks-helloworld-one.yaml --namespace ingress-basic
kubectl apply -f aks-helloworld-two.yaml --namespace ingress-basic
```

## <a name="create-an-ingress-route"></a>Skapa en ingress-väg

Båda programmen körs nu på ditt Kubernetes-kluster. De är dock konfigurerade med en tjänst av typen `ClusterIP` och är inte tillgängliga från Internet. Skapa en Kubernetes ingress-resurs för att göra dem offentligt tillgängliga. I ingress-resursen konfigureras de regler som dirigerar trafik till ett av de två programmen.

I följande exempel, trafik till adressen *Hello-World-ingress. MY_CUSTOM_DOMAIN* dirigeras till *AKS-HelloWorld-* tjänsten. Trafik till adressen *Hello-World-ingress. MY_CUSTOM_DOMAIN/Hello-World-Two* dirigeras till tjänsten *AKS-HelloWorld-två* . Trafik till *Hello – värld – ingress. MY_CUSTOM_DOMAIN/static* dirigeras till tjänsten med namnet *AKS-HelloWorld* för statiska till gångar.

> [!NOTE]
> Om du har konfigurerat ett fullständigt domän namn för IP-adressen för ingångs enheten i stället för en anpassad domän använder du FQDN i stället för *Hello-World-ingress. MY_CUSTOM_DOMAIN*. Om ditt FQDN till exempel är *demo-AKS-ingress.eastus.cloudapp.Azure.com*ersätter du *Hello-World-ingress. MY_CUSTOM_DOMAIN* med *demo-AKS-ingress.eastus.cloudapp.Azure.com* i `hello-world-ingress.yaml` .

Skapa en fil med namnet `hello-world-ingress.yaml` med hjälp av nedanstående exempel yaml. Uppdatera *värdarna* och *värden* till det DNS-namn som du skapade i föregående steg.

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /$1
    nginx.ingress.kubernetes.io/use-regex: "true"
    cert-manager.io/cluster-issuer: letsencrypt
spec:
  tls:
  - hosts:
    - hello-world-ingress.MY_CUSTOM_DOMAIN
    secretName: tls-secret
  rules:
  - host: hello-world-ingress.MY_CUSTOM_DOMAIN
    http:
      paths:
      - backend:
          serviceName: aks-helloworld-one
          servicePort: 80
        path: /hello-world-one(/|$)(.*)
      - backend:
          serviceName: aks-helloworld-two
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
      - backend:
          serviceName: aks-helloworld-one
          servicePort: 80
        path: /(.*)
---
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress-static
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /static/$2
    nginx.ingress.kubernetes.io/use-regex: "true"
    cert-manager.io/cluster-issuer: letsencrypt
spec:
  tls:
  - hosts:
    - hello-world-ingress.MY_CUSTOM_DOMAIN
    secretName: tls-secret
  rules:
  - host: hello-world-ingress.MY_CUSTOM_DOMAIN
    http:
      paths:
      - backend:
          serviceName: aks-helloworld-one
          servicePort: 80
        path: /static(/|$)(.*)
```

Skapa den inkommande resursen med hjälp av `kubectl apply` kommandot.

```console
kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic
```

## <a name="verify-a-certificate-object-has-been-created"></a>Verifiera att ett certifikat objekt har skapats

Därefter måste du skapa en certifikat resurs. Certifikat resursen definierar det önskade X. 509-certifikatet. Mer information finns i certifikat för certifikat [hanterare][cert-manager-certificates]. CERT Manager har automatiskt skapat ett certifikat objekt åt dig med hjälp av ingress-shim, som automatiskt distribueras med cert Manager sedan v 0.2.2. Mer information finns i dokumentationen om [ingress-shim][ingress-shim].

Kontrol lera att certifikatet har skapats genom att använda `kubectl get certificate --namespace ingress-basic` kommandot och verifiera att *klart* är *Sant*, vilket kan ta flera minuter.

```
$ kubectl get certificate --namespace ingress-basic

NAME         READY   SECRET       AGE
tls-secret   True    tls-secret   11m
```

## <a name="test-the-ingress-configuration"></a>Testa ingress-konfigurationen

Öppna en webbläsare för att *Hello-World-ingress. MY_CUSTOM_DOMAIN* av Kubernetes ingångs kontroll. Observera att du omdirigerar till att använda HTTPS och att certifikatet är betrott och demo programmet visas i webbläsaren. Lägg till */Hello-World-Two* -sökvägen och Observera att det andra demonstrations programmet med den anpassade rubriken visas.

## <a name="clean-up-resources"></a>Rensa resurser

I den här artikeln används Helm för att installera ingångs komponenter, certifikat och exempel appar. När du distribuerar ett Helm-diagram skapas ett antal Kubernetes-resurser. De här resurserna omfattar poddar, distributioner och tjänster. Om du vill rensa dessa resurser kan du antingen ta bort hela exempel namnområdet eller enskilda resurser.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Ta bort exempel namn området och alla resurser

Om du vill ta bort hela exempel namnområdet använder du `kubectl delete` kommandot och anger namn på namn området. Alla resurser i namn området tas bort.

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>Ta bort resurser individuellt

Alternativt är en mer detaljerad metod att ta bort de enskilda resurserna som skapats. Ta först bort kluster utfärdarens resurser:

```console
kubectl delete -f cluster-issuer.yaml --namespace ingress-basic
```

Visar en lista med Helm-versioner med `helm list` kommandot. Leta efter diagram med namnet *nginx* och *cert Manager*, som visas i följande exempel på utdata:

```
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
cert-manager            ingress-basic   1               2020-01-15 10:23:36.515514 -0600 CST    deployed        cert-manager-v0.13.0    v0.13.0    
nginx                   ingress-basic   1               2020-01-15 10:09:45.982693 -0600 CST    deployed        nginx-ingress-1.29.1    0.27.0  
```

Avinstallera versioner med `helm uninstall` kommandot. I följande exempel avinstalleras NGINX-inkommande och cert Manager-distributioner.

```
$ helm uninstall cert-manager nginx --namespace ingress-basic

release "cert-manager" uninstalled
release "nginx" uninstalled
```

Ta sedan bort de två exempel programmen:

```console
kubectl delete -f aks-helloworld-one.yaml --namespace ingress-basic
kubectl delete -f aks-helloworld-two.yaml --namespace ingress-basic
```

Ta bort ingångs vägen som riktar sig mot trafik till exempel apparna:

```console
kubectl delete -f hello-world-ingress.yaml --namespace ingress-basic
```

Slutligen kan du ta bort själva namn området. Använd `kubectl delete` kommandot och ange namn på namn område:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln ingår några externa komponenter i AKS. Mer information om dessa komponenter finns i följande projekt sidor:

- [Helm CLI][helm-cli]
- [NGINX ingress-styrenhet][nginx-ingress]
- [cert-manager][cert-manager]

Du kan även:

- [Skapa en grundläggande ingångs kontroll med extern nätverks anslutning][aks-ingress-basic]
- [Aktivera routnings tillägget för HTTP-program][aks-http-app-routing]
- [Skapa en ingångs kontroll enhet som använder ett internt, privat nätverk och IP-adress][aks-ingress-internal]
- [Skapa en ingångs kontroll enhet som använder dina egna TLS-certifikat][aks-ingress-own-tls]
- [Skapa en ingångs kontroll enhet som använder kryptera för att automatiskt generera TLS-certifikat med en statisk offentlig IP-adress][aks-ingress-static-tls]

<!-- LINKS - external -->
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-add-record
[custom-domain]: ../app-service/manage-custom-dns-buy-domain.md#buy-the-domain
[dns-zone]: ../dns/dns-getstarted-cli.md
[helm]: https://helm.sh/
[helm-cli]: ./kubernetes-helm.md
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[ingress-shim]: https://docs.cert-manager.io/en/latest/tasks/issuing-certificates/ingress-shim.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
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
[aks-ingress-own-tls]: ingress-own-tls.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[client-source-ip]: concepts-network.md#ingress-controllers
[install-azure-cli]: /cli/azure/install-azure-cli
