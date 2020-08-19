---
title: Använd ingångs styrenhet med statisk IP
titleSuffix: Azure Kubernetes Service
description: Lär dig hur du installerar och konfigurerar en NGINX ingress Controller med en statisk offentlig IP-adress i ett Azure Kubernetes service-kluster (AKS).
services: container-service
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: 60e0ace70fa87c6a4c47e94eb3ff7f121c9a37cb
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88509050"
---
# <a name="create-an-ingress-controller-with-a-static-public-ip-address-in-azure-kubernetes-service-aks"></a>Skapa en ingångs kontroll enhet med en statisk offentlig IP-adress i Azure Kubernetes service (AKS)

En ingress-kontrollant är en del av programvaran som tillhandahåller omvänd proxy, konfigurerbar trafikroutning och TLS-Avslut för Kubernetes-tjänster. Kubernetes ingress-resurser används för att konfigurera inkommande regler och vägar för enskilda Kubernetes-tjänster. Med hjälp av en ingress-kontrollant och ingress-regler kan en IP-adress användas för att dirigera trafik till flera tjänster i ett Kubernetes-kluster.

Den här artikeln visar hur du distribuerar [nginx ingress-kontrollanten][nginx-ingress] i ett Azure Kubernetes service-kluster (AKS). Ingångs styrenheten är konfigurerad med en statisk offentlig IP-adress. [Cert Manager-][cert-manager] projektet används för att automatiskt generera och konfigurera att [kryptera][lets-encrypt] certifikat. Slutligen körs två program i AKS-klustret, som var och en är tillgänglig över en enskild IP-adress.

Du kan även:

- [Skapa en grundläggande ingångs kontroll med extern nätverks anslutning][aks-ingress-basic]
- [Aktivera routnings tillägget för HTTP-program][aks-http-app-routing]
- [Skapa en ingångs kontroll enhet som använder dina egna TLS-certifikat][aks-ingress-own-tls]
- [Skapa en ingångs kontroll enhet som använder kryptera för att automatiskt generera TLS-certifikat med en dynamisk offentlig IP-adress][aks-ingress-tls]

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster kan du läsa snabb starten för AKS [med hjälp av Azure CLI][aks-quickstart-cli] eller [Azure Portal][aks-quickstart-portal].

I den här artikeln används [Helm 3][helm] för att installera nginx ingress-kontrollanten och cert Manager. Kontrol lera att du använder den senaste versionen av Helm och att du har åtkomst till de *stabila* och *jetstack* Helm-databaserna. Anvisningar om hur du uppgraderar finns i [installations dokument för Helm][helm-install]. Mer information om hur du konfigurerar och använder Helm finns i [installera program med Helm i Azure Kubernetes service (AKS)][use-helm].

Den här artikeln kräver också att du kör Azure CLI-version 2.0.64 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Skapa en ingångs kontroll enhet

Som standard skapas en NGINX ingångs kontroll med en ny offentlig IP-adresstilldelning. Den här offentliga IP-adressen är bara statisk för livs cykeln för ingångs styrenheten och försvinner om styrenheten tas bort och återskapas. Ett vanligt konfigurations krav är att tillhandahålla NGINX ingress-kontrollanten en befintlig statisk offentlig IP-adress. Den statiska offentliga IP-adressen är kvar om ingångs kontroll enheten tas bort. Med den här metoden kan du använda befintliga DNS-poster och nätverkskonfigurationer på ett konsekvent sätt under hela programmets livs cykel.

Om du behöver skapa en statisk offentlig IP-adress måste du först hämta resurs grupp namnet för AKS-klustret med kommandot [AZ AKS show][az-aks-show] :

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

Skapa sedan en offentlig IP-adress med metoden för *statisk* allokering med kommandot [AZ Network Public-IP Create][az-network-public-ip-create] . I följande exempel skapas en offentlig IP-adress med namnet *myAKSPublicIP* i kluster resurs gruppen AKS som hämtades i föregående steg:

```azurecli-interactive
az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --sku Standard --allocation-method static --query publicIp.ipAddress -o tsv
```

> [!NOTE]
> Kommandona ovan skapar en IP-adress som tas bort om du tar bort ditt AKS-kluster. Du kan också skapa en IP-adress i en annan resurs grupp som kan hanteras separat från ditt AKS-kluster. Om du skapar en IP-adress i en annan resurs grupp ser du till att tjänstens huvud namn som används av AKS-klustret har delegerade behörigheter till den andra resurs gruppen, t. ex. *nätverks deltagare*.

Distribuera nu *nginx-ingress-* diagrammet med Helm. För ytterligare redundans distribueras två repliker av NGINX-ingresskontrollanterna med parametern `--set controller.replicaCount`. Se till att det finns fler än en nod i ditt AKS-kluster för att få full nytta av att köra repliker av ingångs styrenheten.

Du måste skicka två ytterligare parametrar till Helm-versionen så att ingångs styrenheten blir medveten om att både den statiska IP-adressen för belastningsutjämnaren som ska allokeras till ingångs styrenhets tjänsten och DNS-namnets etikett används för den offentliga IP-adressresursen. För att HTTPS-certifikaten ska fungera korrekt används en DNS-benämning för att konfigurera ett fullständigt domän namn för IP-adressen för ingångs enheten.

1. Lägg till `--set controller.service.loadBalancerIP` parametern. Ange din egen offentliga IP-adress som skapades i föregående steg.
1. Lägg till `--set controller.service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name"` parametern. Ange en DNS-benämning som ska tillämpas på den offentliga IP-adressen som skapades i föregående steg.

Ingresskontrollanten måste också schemaläggas på en Linux-nod. Windows Server-noder bör inte köra ingresskontrollanten. En nodväljare anges med parametern `--set nodeSelector` för att instruera Kubernetes-schemaläggaren att köra NGINX-ingresskontrollanten på en Linux-baserad nod.

> [!TIP]
> I följande exempel skapas ett Kubernetes-namnområde för de ingress-resurser som heter *ingress-Basic*. Ange ett namn område för din egen miljö efter behov. Om ditt AKS-kluster inte är RBAC-aktiverat lägger du till dem `--set rbac.create=false` i Helm-kommandona.

> [!TIP]
> Om du vill aktivera [IP-konservering för klient källa][client-source-ip] för förfrågningar till behållare i klustret, lägger `--set controller.service.externalTrafficPolicy=Local` du till det i Helm install-kommandot. Klientens käll-IP lagras i begär ande huvudet under *X-forwarded – for*. TLS-vidarekoppling fungerar inte när du använder en ingångs kontroll för att aktivera IP-konservering i klient källan.

Uppdatera följande skript med **IP-adressen** för din ingångs kontroll och ett **unikt namn** som du vill använda för FQDN-prefixet.

> [!IMPORTANT]
> Du måste uppdatera Ersätt *STATIC_IP* och *DNS_LABEL* med din egen IP-adress och ett unikt namn när du kör kommandot.

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
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.service.loadBalancerIP="STATIC_IP" \
    --set controller.service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name"="DNS_LABEL"
```

När belastnings Utjämnings tjänsten för Kubernetes skapas för NGINX-ingångs styrenheten, tilldelas den statiska IP-adressen, som visas i följande exempel:

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                        TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)                      AGE
nginx-ingress-controller                    LoadBalancer   10.0.232.56   STATIC_IP      80:31978/TCP,443:32037/TCP   3m
nginx-ingress-default-backend               ClusterIP      10.0.95.248   <none>         80/TCP                       3m
```

Inga ingångs regler har skapats ännu. därför visas sidan NGINX ingress Controller standard 404 om du bläddrar till den offentliga IP-adressen. Ingress-regler konfigureras i följande steg.

Du kan kontrol lera att DNS-namnets etikett har tillämpats genom att fråga FQDN på den offentliga IP-adressen enligt följande:

```azurecli-interactive
az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query "[?name=='myAKSPublicIP'].[dnsSettings.fqdn]" -o tsv
```

Ingångs styrenheten är nu tillgänglig via IP-adressen eller det fullständiga domän namnet.

## <a name="install-cert-manager"></a>Installera cert-manager

NGINX-ingresskontrollanten stöder TLS-avslutning. Det finns flera sätt att hämta och konfigurera certifikat för HTTPS. Den här artikeln visar hur du använder [cert Manager][cert-manager], som [ger automatiska funktioner][lets-encrypt] för att skapa och hantera certifikat.

> [!NOTE]
> I den här artikeln används `staging` miljön för att kryptera. I produktions distributioner använder du `letsencrypt-prod` och `https://acme-v02.api.letsencrypt.org/directory` i resurs definitionerna och när du installerar Helm-diagrammet.

Om du vill installera certifikat hanterarens kontrollant i ett RBAC-aktiverat kluster, använder du följande `helm install` kommando:

```console
# Label the cert-manager namespace to disable resource validation
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
  name: letsencrypt-staging
spec:
  acme:
    server: https://acme-staging-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-staging
    solvers:
    - http01:
        ingress:
          class: nginx
          podTemplate:
            spec:
              nodeSelector:
                "kubernetes.io/os": linux
```

Använd kommandot för att skapa utfärdaren `kubectl apply -f cluster-issuer.yaml` .

```
$ kubectl apply -f cluster-issuer.yaml --namespace ingress-basic

clusterissuer.cert-manager.io/letsencrypt-staging created
```

## <a name="run-demo-applications"></a>Köra demo program

En ingångs kontroll och en certifikat hanterings lösning har kon figurer ATS. Nu ska vi köra två demo program i ditt AKS-kluster. I det här exemplet används Helm för att distribuera två instanser av ett enkelt "Hello World"-program.

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

I följande exempel dirigeras trafik till adressen `https://demo-aks-ingress.eastus.cloudapp.azure.com/` till tjänsten med namnet `aks-helloworld` . Trafik till adressen `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` dirigeras till `ingress-demo` tjänsten. Uppdatera *värdarna* och *värden* till det DNS-namn som du skapade i föregående steg.

Skapa en fil med namnet `hello-world-ingress.yaml` och kopiera i följande exempel yaml.

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    cert-manager.io/cluster-issuer: letsencrypt-staging
    nginx.ingress.kubernetes.io/rewrite-target: /$1
    nginx.ingress.kubernetes.io/use-regex: "true"
spec:
  tls:
  - hosts:
    - demo-aks-ingress.eastus.cloudapp.azure.com
    secretName: tls-secret
  rules:
  - host: demo-aks-ingress.eastus.cloudapp.azure.com
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

Skapa den inkommande resursen med hjälp av `kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic` kommandot.

```
$ kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic

ingress.extensions/hello-world-ingress created
```

## <a name="create-a-certificate-object"></a>Skapa ett certifikat objekt

Därefter måste du skapa en certifikat resurs. Certifikat resursen definierar det önskade X. 509-certifikatet. Mer information finns i certifikat för certifikat [hanterare][cert-manager-certificates].

CERT Manager har troligt vis skapat ett certifikat objekt automatiskt för dig med hjälp av ingress-shim, som automatiskt distribueras med cert Manager sedan v 0.2.2. Mer information finns i dokumentationen om [ingress-shim][ingress-shim].

Kontrol lera att certifikatet har skapats genom att använda `kubectl describe certificate tls-secret --namespace ingress-basic` kommandot.

Om certifikatet har utfärdats visas utdata som liknar följande:
```
Type    Reason          Age   From          Message
----    ------          ----  ----          -------
  Normal  CreateOrder     11m   cert-manager  Created new ACME order, attempting validation...
  Normal  DomainVerified  10m   cert-manager  Domain "demo-aks-ingress.eastus.cloudapp.azure.com" verified with "http-01" validation
  Normal  IssueCert       10m   cert-manager  Issuing certificate...
  Normal  CertObtained    10m   cert-manager  Obtained certificate from ACME server
  Normal  CertIssued      10m   cert-manager  Certificate issued successfully
```

Om du behöver skapa ytterligare en certifikat resurs kan du göra det med följande exempel manifest. Uppdatera *dnsNames* och *domänerna* till det DNS-namn som du skapade i föregående steg. Om du använder en intern ingångs kontroll anger du det interna DNS-namnet för din tjänst.

```yaml
apiVersion: cert-manager.io/v1alpha2
kind: Certificate
metadata:
  name: tls-secret
  namespace: ingress-basic
spec:
  secretName: tls-secret
  dnsNames:
  - demo-aks-ingress.eastus.cloudapp.azure.com
  acme:
    config:
    - http01:
        ingressClass: nginx
      domains:
      - demo-aks-ingress.eastus.cloudapp.azure.com
  issuerRef:
    name: letsencrypt-staging
    kind: ClusterIssuer
```

Använd kommandot för att skapa certifikat resursen `kubectl apply -f certificates.yaml` .

```
$ kubectl apply -f certificates.yaml

certificate.cert-manager.io/tls-secret created
```

## <a name="test-the-ingress-configuration"></a>Testa ingress-konfigurationen

Öppna en webbläsare till FQDN för din Kubernetes-ingångs kontroll, till exempel *`https://demo-aks-ingress.eastus.cloudapp.azure.com`* .

Som de här exemplen använder `letsencrypt-staging` är det utfärdade TLS/SSL-certifikatet inte betrott av webbläsaren. Godkänn varningen om du vill fortsätta till ditt program. Certifikat informationen visar att det *mellanliggande x1* -certifikatet för falska användare utfärdas av vi krypterar. Detta falska certifikat indikerar `cert-manager` att begäran bearbetades korrekt och tagits emot från providern:

![Låt oss kryptera mellanlagrings certifikat](media/ingress/staging-certificate.png)

När du ändrar vad som är krypterat att använda `prod` i stället för `staging` , används ett betrott certifikat som utfärdats av att kryptera, som du ser i följande exempel:

![Låt oss kryptera certifikat](media/ingress/certificate.png)

Demo programmet visas i webbläsaren:

![Exempel på program ett](media/ingress/app-one.png)

Lägg nu till sökvägen till */Hello-World-Two* i FQDN, till exempel *`https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two`* . Det andra demonstrations programmet med den anpassade rubriken visas:

![Program exempel två](media/ingress/app-two.png)

## <a name="clean-up-resources"></a>Rensa resurser

I den här artikeln används Helm för att installera ingångs komponenter, certifikat och exempel appar. När du distribuerar ett Helm-diagram skapas ett antal Kubernetes-resurser. De här resurserna omfattar poddar, distributioner och tjänster. Om du vill rensa dessa resurser kan du antingen ta bort hela exempel namnområdet eller enskilda resurser.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Ta bort exempel namn området och alla resurser

Om du vill ta bort hela exempel namnområdet använder du `kubectl delete` kommandot och anger namn på namn området. Alla resurser i namn området tas bort.

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>Ta bort resurser individuellt

Alternativt är en mer detaljerad metod att ta bort de enskilda resurserna som skapats. Ta först bort certifikat resurserna:

```console
kubectl delete -f certificates.yaml
kubectl delete -f cluster-issuer.yaml
```

Visar nu Helm-versionerna med `helm list` kommandot. Leta efter diagram med namnet *nginx – ingress* och *cert Manager* enligt följande exempel:

```
$ helm list --all-namespaces

NAME                    NAMESPACE       REVISION        UPDATED                        STATUS          CHART                   APP VERSION
nginx-ingress           ingress-basic   1               2020-01-11 14:51:03.454165006  deployed        nginx-ingress-1.28.2    0.26.2
cert-manager            ingress-basic   1               2020-01-06 21:19:03.866212286  deployed        cert-manager-v0.13.0    v0.13.0
```

Avinstallera versioner med `helm uninstall` kommandot. I följande exempel avinstalleras distributionen av NGINX ingress-distribution och certifikat hanterare.

```
$ helm uninstall nginx-ingress cert-manager -n ingress-basic

release "nginx-ingress" deleted
release "cert-manager" deleted
```

Ta sedan bort de två exempel programmen:

```console
kubectl delete -f aks-helloworld.yaml --namespace ingress-basic
kubectl delete -f ingress-demo.yaml --namespace ingress-basic
```

Ta bort det egna namn området. Använd `kubectl delete` kommandot och ange namn på namn område:

```console
kubectl delete namespace ingress-basic
```

Slutligen tar du bort den statiska offentliga IP-adressen som skapats för ingångs styrenheten. Ange namnet på din *MC_* kluster resurs grupp som hämtades i det första steget i den här artikeln, till exempel *MC_myResourceGroup_myAKSCluster_eastus*:

```azurecli-interactive
az network public-ip delete --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP
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
- [Skapa en ingångs kontroll enhet med en dynamisk offentlig IP-adress och konfigurera låt oss kryptera för att automatiskt generera TLS-certifikat][aks-ingress-tls]

<!-- LINKS - external -->
[helm-cli]: ./kubernetes-helm.md
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm]: https://helm.sh/
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm
[ingress-shim]: https://docs.cert-manager.io/en/latest/tasks/issuing-certificates/ingress-shim.html

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[client-source-ip]: concepts-network.md#ingress-controllers
[install-azure-cli]: /cli/azure/install-azure-cli
