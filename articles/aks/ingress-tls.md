---
title: Skapa ingress med automatisk TLS
titleSuffix: Azure Kubernetes Service
description: Lär dig hur du installerar och konfigurerar en NGINX-ingresskontrollant som använder Let's Encrypt för automatisk generering av TLS-certifikat i ett Azure Kubernetes Service-kluster (AKS).
services: container-service
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: a3f78c0777d1377c8afdc5b43b457873de395cd6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779803"
---
# <a name="create-an-https-ingress-controller-on-azure-kubernetes-service-aks"></a>Skapa en HTTPS-inresskontrollant Azure Kubernetes Service (AKS)

En ingress-kontrollant är en del av programvaran som tillhandahåller omvänd proxy, konfigurerbar trafikroutning och TLS-Avslut för Kubernetes-tjänster. Kubernetes ingress-resurser används för att konfigurera inkommande regler och vägar för enskilda Kubernetes-tjänster. Med hjälp av en ingress-kontrollant och ingress-regler kan en IP-adress användas för att dirigera trafik till flera tjänster i ett Kubernetes-kluster.

Den här artikeln visar hur du distribuerar [NGINX-ingress-kontrollanten][nginx-ingress] i ett Azure Kubernetes Service-kluster (AKS). [Cert-Manager-projektet][cert-manager] används för att automatiskt generera och konfigurera [Let's Encrypt-certifikat.][lets-encrypt] Slutligen körs två program i AKS-klustret, som var och en kan nås via en enda IP-adress.

Du kan även:

- [Skapa en grundläggande ingressstyrenhet med extern nätverksanslutning][aks-ingress-basic]
- [Aktivera tillägget för HTTP-programroutning][aks-http-app-routing]
- [Skapa en ingress-kontrollant som använder ett internt, privat nätverk och en IP-adress][aks-ingress-internal]
- [Skapa en ingress-kontrollant som använder dina egna TLS-certifikat][aks-ingress-own-tls]
- [Skapa en ingress-kontrollant som använder Let's Encrypt för att automatiskt generera TLS-certifikat med en statisk offentlig IP-adress][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster kan du gå till AKS-snabbstarten med Hjälp av [Azure CLI][aks-quickstart-cli] eller [använda Azure Portal][aks-quickstart-portal].

Den här artikeln förutsätter också att [du har en anpassad][custom-domain] domän med en [DNS-zon][dns-zone] i samma resursgrupp som ditt AKS-kluster.

Den här artikeln [använder Helm 3][helm] för att installera NGINX-inress-kontrollanten och cert-manager. Kontrollera att du använder den senaste versionen av Helm och har åtkomst till *Helm-lagringsplatsen ingress-nginx* och *jetstack.* Uppgraderingsanvisningar finns i [installationsdokumenten för Helm.][helm-install] Mer information om hur du konfigurerar och använder Helm finns [i Installera program med Helm i Azure Kubernetes Service (AKS).][use-helm]

Den här artikeln kräver också att du kör Azure CLI version 2.0.64 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Skapa en ingress-kontrollant

Om du vill skapa ingress-kontrollanten använder `helm` du kommandot för att installera *nginx-ingress*. För ytterligare redundans distribueras två repliker av NGINX-ingresskontrollanterna med parametern `--set controller.replicaCount`. Om du vill dra full nytta av att köra repliker av ingress-kontrollanten kontrollerar du att det finns fler än en nod i AKS-klustret.

Ingresskontrollanten måste också schemaläggas på en Linux-nod. Windows Server-noder bör inte köra ingresskontrollanten. En nodväljare anges med parametern `--set nodeSelector` för att instruera Kubernetes-schemaläggaren att köra NGINX-ingresskontrollanten på en Linux-baserad nod.

> [!TIP]
> I följande exempel skapas ett Kubernetes-namnområde för ingressresurserna med *namnet ingress-basic*. Ange ett namnområde för din egen miljö efter behov.

> [!TIP]
> Om du vill aktivera bevarande av [klientkällans IP-adress][client-source-ip] för begäranden till containrar i klustret lägger du `--set controller.service.externalTrafficPolicy=Local` till i installationskommandot för Helm. Klientens käll-IP lagras i begärandehuvudet under *X-Forwarded-For*. När du använder en ingress-kontrollant med klientkällans IP-bevarande aktiverat fungerar inte TLS-genomgående.

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

Hämta den offentliga IP-adressen med `kubectl get service` kommandot . Det tar några minuter för IP-adressen att tilldelas till tjänsten.

```console
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Inga inkommande regler har skapats ännu. Om du bläddrar till den offentliga IP-adressen visas NGINX-ingresskontrollantens standardsida 404.

## <a name="add-an-a-record-to-your-dns-zone"></a>Lägga till en A-post i DNS-zonen

Lägg till *en A-post* i DNS-zonen med den externa IP-adressen för NGINX-tjänsten med [az network dns record-set a add-record][az-network-dns-record-set-a-add-record].

```console
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name * \
    --ipv4-address MY_EXTERNAL_IP
```

> [!NOTE]
> Du kan också konfigurera ett FQDN för ingress-kontrollantens IP-adress i stället för en anpassad domän. Observera att det här exemplet är för ett Bash-gränssnitt.
> 
> ```bash
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

NGINX-ingresskontrollanten stöder TLS-avslutning. Det finns flera sätt att hämta och konfigurera certifikat för HTTPS. Den här artikeln visar hur du [använder cert-manager][cert-manager], som tillhandahåller automatisk [Låter kryptera certifikatgenerering][lets-encrypt] och hanteringsfunktioner.

Så här installerar du cert-manager-styrenheten:

```console
# Label the ingress-basic namespace to disable resource validation
kubectl label namespace ingress-basic cert-manager.io/disable-validation=true

# Add the Jetstack Helm repository
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

# Install the cert-manager Helm chart
helm install cert-manager jetstack/cert-manager \
  --namespace ingress-basic \
  --set installCRDs=true \
  --set nodeSelector."kubernetes\.io/os"=linux \
  --set webhook.nodeSelector."kubernetes\.io/os"=linux \
  --set cainjector.nodeSelector."kubernetes\.io/os"=linux
```

Mer information om cert-manager-konfiguration finns i [cert-manager-projektet][cert-manager].

## <a name="create-a-ca-cluster-issuer"></a>Skapa en CA-klusterutfärdare

Innan certifikat kan utfärdas kräver cert-manager en [Issuer- eller][cert-manager-issuer] [ClusterIssuer-resurs.][cert-manager-cluster-issuer] Dessa Kubernetes-resurser är identiska i funktioner, men fungerar i ett enda `Issuer` namnområde `ClusterIssuer` och fungerar i alla namnområden. Mer information finns i dokumentationen [för cert-manager issuer.][cert-manager-issuer]

Skapa en klusterutfärdare, till exempel `cluster-issuer.yaml` , med hjälp av följande exempelmanifest. Uppdatera e-postadressen med en giltig adress från din organisation:

```yaml
apiVersion: cert-manager.io/v1
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

Använd kommandot för att skapa `kubectl apply` utfärdaren.

```console
kubectl apply -f cluster-issuer.yaml
```

## <a name="run-demo-applications"></a>Köra demoprogram

En ingress-kontrollant och en certifikathanteringslösning har konfigurerats. Nu ska vi köra två demoprogram i ditt AKS-kluster. I det här exemplet används Helm för att distribuera två instanser av ett enkelt *Hello World-program.*

Om du vill se hur ingress-kontrollanten används kör du två demoprogram i ditt AKS-kluster. I det här exemplet använder du `kubectl apply` för att distribuera två instanser av ett enkelt Hello *World-program.*

Skapa en *aks-helloworld-one.yaml-fil* och kopiera följande YAML-exempel:

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
  name: aks-helloworld-one
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld-one
```

Skapa en *aks-helloworld-two.yaml-fil* och kopiera följande YAML-exempel:

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
  name: aks-helloworld-two
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld-two
```

Kör de två demoprogrammen med `kubectl apply` :

```console
kubectl apply -f aks-helloworld-one.yaml --namespace ingress-basic
kubectl apply -f aks-helloworld-two.yaml --namespace ingress-basic
```

## <a name="create-an-ingress-route"></a>Skapa en ingressväg

Båda programmen körs nu i kubernetes-klustret. De är dock konfigurerade med en tjänst av `ClusterIP` typen och är inte tillgängliga från Internet. Om du vill göra dem offentligt tillgängliga skapar du en ingressresurs för Kubernetes. Ingressresursen konfigurerar reglerna som dirigerar trafik till ett av de två programmen.

I följande exempel trafik till adressen *hello-world-ingress. MY_CUSTOM_DOMAIN* dirigeras till tjänsten *aks-helloworld-one.* Trafik till adressen *hello-world-ingress. MY_CUSTOM_DOMAIN/hello-world-two* dirigeras till tjänsten *aks-helloworld-two.* Trafik till *hello-world-ingress. MY_CUSTOM_DOMAIN/static* dirigeras till tjänsten *aks-helloworld-one för* statiska tillgångar.

> [!NOTE]
> Om du har konfigurerat ett FQDN för ingress-kontrollantens IP-adress i stället för en anpassad domän använder du FQDN i stället för *hello-world-ingress. MY_CUSTOM_DOMAIN*. Om ditt FQDN till exempel *demo-aks-ingress.eastus.cloudapp.azure.com* ersätter du *hello-world-ingress. MY_CUSTOM_DOMAIN* med *demo-aks-ingress.eastus.cloudapp.azure.com* i `hello-world-ingress.yaml` .

Skapa en fil med namnet `hello-world-ingress.yaml` med nedanstående YAML-exempel. Uppdatera *värdarna och* *värden till* DNS-namnet som du skapade i föregående steg.

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

Skapa ingressresursen med `kubectl apply` kommandot .

```console
kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic
```

## <a name="verify-a-certificate-object-has-been-created"></a>Kontrollera att ett certifikatobjekt har skapats

Därefter måste en certifikatresurs skapas. Certifikatresursen definierar det önskade X.509-certifikatet. Mer information finns i [cert-manager-certifikat.][cert-manager-certificates] Cert-manager har automatiskt skapat ett certifikatobjekt åt dig med hjälp av ingress-shim, som distribueras automatiskt med cert-manager sedan v0.2.2. Mer information finns i [dokumentationen om ingress-shim.][ingress-shim]

Kontrollera att certifikatet har skapats genom att använda kommandot och kontrollera `kubectl get certificate --namespace ingress-basic` att *READY* är *Sant*, vilket kan ta flera minuter.

```console
$ kubectl get certificate --namespace ingress-basic

NAME         READY   SECRET       AGE
tls-secret   True    tls-secret   11m
```

## <a name="test-the-ingress-configuration"></a>Testa ingresskonfigurationen

Öppna en webbläsare för *hello-world-ingress. MY_CUSTOM_DOMAIN* din Kubernetes-indatakontrollant. Observera att du omdirigeras för att använda HTTPS och att certifikatet är betrott och att demoprogrammet visas i webbläsaren. Lägg till *sökvägen /hello-world-two* och lägg märke till att det andra demoprogrammet med den anpassade rubriken visas.

## <a name="clean-up-resources"></a>Rensa resurser

Den här artikeln använde Helm för att installera ingresskomponenter, certifikat och exempelappar. När du distribuerar ett Helm-diagram skapas ett antal Kubernetes-resurser. Dessa resurser omfattar poddar, distributioner och tjänster. Om du vill rensa resurserna kan du antingen ta bort hela exempelnamnområdet eller de enskilda resurserna.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Ta bort exempelnamnområdet och alla resurser

Om du vill ta bort hela exempelnamnrymden använder `kubectl delete` du kommandot och anger namnet på namnområdet. Alla resurser i namnområdet tas bort.

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>Ta bort resurser individuellt

En mer detaljerad metod kan också vara att ta bort de enskilda resurser som skapats. Ta först bort resurserna för klusterutfärdaren:

```console
kubectl delete -f cluster-issuer.yaml --namespace ingress-basic
```

Visa en lista över Helm-versioner med `helm list` kommandot . Leta efter diagram med *namnet nginx* *och cert-manager*, som du ser i följande exempelutdata:

```console
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
cert-manager            ingress-basic   1               2020-01-15 10:23:36.515514 -0600 CST    deployed        cert-manager-v0.13.0    v0.13.0    
nginx                   ingress-basic   1               2020-01-15 10:09:45.982693 -0600 CST    deployed        nginx-ingress-1.29.1    0.27.0  
```

Avinstallera versionerna med `helm uninstall` kommandot . I följande exempel avinstalleras NGINX-ingress- och cert-manager-distributionerna.

```console
$ helm uninstall cert-manager nginx --namespace ingress-basic

release "cert-manager" uninstalled
release "nginx" uninstalled
```

Ta sedan bort de två exempelprogrammen:

```console
kubectl delete -f aks-helloworld-one.yaml --namespace ingress-basic
kubectl delete -f aks-helloworld-two.yaml --namespace ingress-basic
```

Ta bort den ingressväg som dirigerade trafik till exempelapparna:

```console
kubectl delete -f hello-world-ingress.yaml --namespace ingress-basic
```

Slutligen kan du ta bort själva namnområdet. Använd kommandot `kubectl delete` och ange namnet på namnområdet:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Nästa steg

Den här artikeln innehåller några externa komponenter till AKS. Mer information om dessa komponenter finns på följande projektsidor:

- [Helm CLI][helm-cli]
- [NGINX-ingresskontrollant][nginx-ingress]
- [cert-manager][cert-manager]

Du kan även:

- [Skapa en grundläggande ingress-kontrollant med extern nätverksanslutning][aks-ingress-basic]
- [Aktivera tillägget för HTTP-programroutning][aks-http-app-routing]
- [Skapa en ingress-kontrollant som använder ett internt, privat nätverk och en IP-adress][aks-ingress-internal]
- [Skapa en ingress-kontrollant som använder dina egna TLS-certifikat][aks-ingress-own-tls]
- [Skapa en ingresskontrollant som använder Let's Encrypt för att automatiskt generera TLS-certifikat med en statisk offentlig IP-adress][aks-ingress-static-tls]

<!-- LINKS - external -->
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/#az_network_dns_record_set_a_add_record
[custom-domain]: ../app-service/manage-custom-dns-buy-domain.md#buy-an-app-service-domain
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
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az_network_public_ip_create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[client-source-ip]: concepts-network.md#ingress-controllers
[install-azure-cli]: /cli/azure/install-azure-cli
