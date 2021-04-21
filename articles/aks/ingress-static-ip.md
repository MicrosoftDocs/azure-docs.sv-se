---
title: Använda ingress-kontrollant med statisk IP
titleSuffix: Azure Kubernetes Service
description: Lär dig hur du installerar och konfigurerar en NGINX ingress-kontrollant med en statisk offentlig IP-adress i ett AKS-kluster (Azure Kubernetes Service).
services: container-service
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: 8de0d25bc30d53f11ddaed5ab7b53ff992a5c88c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779839"
---
# <a name="create-an-ingress-controller-with-a-static-public-ip-address-in-azure-kubernetes-service-aks"></a>Skapa en ingångskontrollant med en statisk offentlig IP-adress i AKS (Azure Kubernetes service)

En ingress-kontrollant är en del av programvaran som tillhandahåller omvänd proxy, konfigurerbar trafikroutning och TLS-Avslut för Kubernetes-tjänster. Kubernetes ingress-resurser används för att konfigurera inkommande regler och vägar för enskilda Kubernetes-tjänster. Med hjälp av en ingress-kontrollant och ingress-regler kan en IP-adress användas för att dirigera trafik till flera tjänster i ett Kubernetes-kluster.

Den här artikeln visar hur du distribuerar [NGINX-ingress-kontrollanten][nginx-ingress] i ett Azure Kubernetes Service-kluster (AKS). Ingress-kontrollanten konfigureras med en statisk offentlig IP-adress. [Cert-Manager-projektet][cert-manager] används för att automatiskt generera och konfigurera [Let's Encrypt-certifikat.][lets-encrypt] Slutligen körs två program i AKS-klustret, som var och en kan nås via en enda IP-adress.

Du kan även:

- [Skapa en grundläggande ingressstyrenhet med extern nätverksanslutning][aks-ingress-basic]
- [Aktivera tillägget för HTTP-programroutning][aks-http-app-routing]
- [Skapa en ingress-kontrollant som använder dina egna TLS-certifikat][aks-ingress-own-tls]
- [Skapa en ingress-kontrollant som använder Let's Encrypt för att automatiskt generera TLS-certifikat med en dynamisk offentlig IP-adress][aks-ingress-tls]

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster kan du gå till AKS-snabbstarten med Hjälp av [Azure CLI][aks-quickstart-cli] eller [använda Azure Portal][aks-quickstart-portal].

Den här artikeln [använder Helm 3][helm] för att installera NGINX-inress-kontrollanten och cert-manager. Kontrollera att du använder den senaste versionen av Helm och har åtkomst till *Helm-lagringsplatsen ingress-nginx* och *jetstack.* Uppgraderingsanvisningar finns i [installationsdokumenten för Helm.][helm-install] Mer information om hur du konfigurerar och använder Helm finns [i Installera program med Helm i Azure Kubernetes Service (AKS).][use-helm]

Den här artikeln kräver också att du kör Azure CLI version 2.0.64 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Skapa en ingress-kontrollant

Som standard skapas en NGINX-ingresskontrollant med en ny tilldelning av offentliga IP-adresser. Den här offentliga IP-adressen är endast statisk för ingress-kontrollantens livslängd och går förlorad om kontrollanten tas bort och skapas på nytt. Ett vanligt konfigurationskrav är att ge NGINX-ingress-kontrollanten en befintlig statisk offentlig IP-adress. Den statiska offentliga IP-adressen finns kvar om ingress-kontrollanten tas bort. Med den här metoden kan du använda befintliga DNS-poster och nätverkskonfigurationer på ett konsekvent sätt under hela livscykeln för dina program.

Om du behöver skapa en statisk offentlig IP-adress hämtar du först resursgruppens namn för AKS-klustret med [kommandot az aks show:][az-aks-show]

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

Skapa sedan en offentlig IP-adress med den *statiska allokeringsmetoden* med [kommandot az network public-ip][az-network-public-ip-create] create. I följande exempel skapas en offentlig IP-adress med *namnet myAKSPublicIP* i AKS-klusterresursgruppen som du fick i föregående steg:

```azurecli-interactive
az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --sku Standard --allocation-method static --query publicIp.ipAddress -o tsv
```

> [!NOTE]
> Kommandona ovan skapar en IP-adress som tas bort om du tar bort ditt AKS-kluster. Du kan också skapa en IP-adress i en annan resursgrupp som kan hanteras separat från ditt AKS-kluster. Om du skapar en IP-adress i en annan resursgrupp ska du se till att klusteridentiteten som används av AKS-klustret har delegerade behörigheter till den andra resursgruppen, till exempel *Nätverksdeltagare.* Mer information finns i Använda [en statisk offentlig IP-adress och DNS-etikett med AKS-lastbalanserare.][aks-static-ip]

Distribuera nu *nginx-ingress-diagrammet* med Helm. För ytterligare redundans distribueras två repliker av NGINX-ingresskontrollanterna med parametern `--set controller.replicaCount`. Om du vill dra full nytta av att köra repliker av ingress-kontrollanten kontrollerar du att det finns fler än en nod i AKS-klustret.

Du måste skicka två ytterligare parametrar till Helm-versionen så att ingress-kontrollanten blir medveten om både den statiska IP-adressen för lastbalanseraren som ska allokeras till ingresskontrollanttjänsten och dns-namnetiketten som tillämpas på den offentliga IP-adressresursen. För att HTTPS-certifikaten ska fungera korrekt används en DNS-namnetikett för att konfigurera ett FQDN för ingress-kontrollantens IP-adress.

1. Lägg till `--set controller.service.loadBalancerIP` parametern . Ange din egen offentliga IP-adress som skapades i föregående steg.
1. Lägg till `--set controller.service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name"` parametern . Ange en DNS-namnetikett som ska tillämpas på den offentliga IP-adress som skapades i föregående steg.

Ingresskontrollanten måste också schemaläggas på en Linux-nod. Windows Server-noder bör inte köra ingresskontrollanten. En nodväljare anges med parametern `--set nodeSelector` för att instruera Kubernetes-schemaläggaren att köra NGINX-ingresskontrollanten på en Linux-baserad nod.

> [!TIP]
> I följande exempel skapas ett Kubernetes-namnområde för ingressresurserna med namnet *ingress-basic*. Ange ett namnområde för din egen miljö efter behov. Om ditt AKS-kluster inte är Kubernetes RBAC aktiverat lägger du till `--set rbac.create=false` i Helm-kommandona.

> [!TIP]
> Om du vill aktivera bevarande av [klientkällans IP-adress][client-source-ip] för begäranden till containrar i klustret lägger du `--set controller.service.externalTrafficPolicy=Local` till i installationskommandot helm. Klientens käll-IP lagras i begärandehuvudet under *X-Forwarded-For*. När du använder en ingress-kontrollant med klientkällans IP-bevarande aktiverat fungerar inte TLS-genomgående.

Uppdatera följande skript med **IP-adressen** för ingress-kontrollanten och ett unikt namn som du vill använda för FQDN-prefixet. 

> [!IMPORTANT]
> Du måste uppdatera *ersätter STATIC_IP* och *DNS_LABEL* med din egen IP-adress och unika namn när du kör kommandot.

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
    --set controller.admissionWebhooks.patch.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.service.loadBalancerIP="STATIC_IP" \
    --set controller.service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name"="DNS_LABEL"
```

När Kubernetes-lastbalanseringstjänsten skapas för NGINX-indatakontrollanten tilldelas din statiska IP-adress, som du ser i följande exempelutdata:

```
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Inga inkommande regler har skapats ännu, så NGINX-inkommande styrenhetens standardsida 404 visas om du bläddrar till den offentliga IP-adressen. Ingress-regler konfigureras i följande steg.

Du kan kontrollera att DNS-namnetiketten har tillämpats genom att fråga FQDN på den offentliga IP-adressen på följande sätt:

```azurecli-interactive
az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query "[?ipAddress=='myAKSPublicIP'].[dnsSettings.fqdn]" -o tsv
```

Ingress-kontrollanten är nu tillgänglig via IP-adressen eller FQDN.

## <a name="install-cert-manager"></a>Installera cert-manager

NGINX-ingresskontrollanten stöder TLS-avslutning. Det finns flera sätt att hämta och konfigurera certifikat för HTTPS. Den här artikeln visar hur du [använder cert-manager][cert-manager], som innehåller automatisk lets encrypt certificate generation and management functionality [(Låter kryptera][lets-encrypt] certifikatgenerering och hanteringsfunktioner).

> [!NOTE]
> Den här artikeln använder `staging` miljön för Let's Encrypt. I produktionsdistributioner använder du `letsencrypt-prod` och `https://acme-v02.api.letsencrypt.org/directory` i resursdefinitionerna och när du installerar Helm-diagrammet.

Om du vill installera cert-manager-kontrollanten i ett Kubernetes RBAC-aktiverat kluster använder du följande `helm install` kommando:

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

Mer information om cert-manager-konfiguration finns i [cert-manager-projektet][cert-manager].

## <a name="create-a-ca-cluster-issuer"></a>Skapa en CA-klusterutfärdare

Innan certifikat kan utfärdas kräver cert-manager en [Issuer- eller][cert-manager-issuer] [ClusterIssuer-resurs.][cert-manager-cluster-issuer] Dessa Kubernetes-resurser är identiska i funktionalitet, men fungerar i ett enda `Issuer` namnområde `ClusterIssuer` och fungerar i alla namnområden. Mer information finns i dokumentationen [om cert-manager issuer.][cert-manager-issuer]

Skapa en klusterutfärdare, till exempel `cluster-issuer.yaml` , med hjälp av följande exempelmanifest. Uppdatera e-postadressen med en giltig adress från din organisation:

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

Använd kommandot för att skapa `kubectl apply` utfärdaren.

```
kubectl apply -f cluster-issuer.yaml --namespace ingress-basic
```

Utdata bör likna det här exemplet:

```
clusterissuer.cert-manager.io/letsencrypt-staging created
```

## <a name="run-demo-applications"></a>Köra demoprogram

En ingress-kontrollant och en certifikathanteringslösning har konfigurerats. Nu ska vi köra två demoprogram i ditt AKS-kluster. I det här exemplet används Helm för att distribuera två instanser av ett enkelt Hello World-program.

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

Båda programmen körs nu i Kubernetes-klustret, men de är konfigurerade med en tjänst av typen `ClusterIP` . Därför är programmen inte tillgängliga från Internet. Om du vill göra dem offentligt tillgängliga skapar du en Kubernetes-ingressresurs. Ingressresursen konfigurerar reglerna som dirigerar trafik till ett av de två programmen.

I följande exempel dirigeras trafik till `https://demo-aks-ingress.eastus.cloudapp.azure.com/` adressen till tjänsten med namnet `aks-helloworld` . Trafik till adressen `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` dirigeras till `ingress-demo` tjänsten. Uppdatera *värdarna och* *värden till* DNS-namnet som du skapade i föregående steg.

Skapa en fil med `hello-world-ingress.yaml` namnet och kopiera i följande YAML-exempel.

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

Skapa ingressresursen med `kubectl apply` kommandot .

```
kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic
```

Utdata bör likna det här exemplet:

```
ingress.extensions/hello-world-ingress created
```

## <a name="create-a-certificate-object"></a>Skapa ett certifikatobjekt

Därefter måste en certifikatresurs skapas. Certifikatresursen definierar det önskade X.509-certifikatet. Mer information finns i [cert-manager-certifikat.][cert-manager-certificates]

Cert-manager har förmodligen automatiskt skapat ett certifikatobjekt åt dig med hjälp av ingress-shim, som distribueras automatiskt med cert-manager sedan v0.2.2. Mer information finns i [ingress-shim-dokumentationen.][ingress-shim]

Kontrollera att certifikatet har skapats med hjälp av `kubectl describe certificate tls-secret --namespace ingress-basic` kommandot .

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

Om du behöver skapa ytterligare en certifikatresurs kan du göra det med följande exempelmanifest. Uppdatera *dnsNames och* *domänerna till* DNS-namnet som du skapade i föregående steg. Om du använder en intern ingress-kontrollant anger du det interna DNS-namnet för din tjänst.

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

Använd kommandot för att skapa `kubectl apply` certifikatresursen.

```
$ kubectl apply -f certificates.yaml

certificate.cert-manager.io/tls-secret created
```

## <a name="test-the-ingress-configuration"></a>Testa ingresskonfigurationen

Öppna en webbläsare till FQDN för din Kubernetes-indatakontrollant, till exempel *`https://demo-aks-ingress.eastus.cloudapp.azure.com`* .

Eftersom de här `letsencrypt-staging` exemplen använder är det utfärdade TLS/SSL-certifikatet inte betrott av webbläsaren. Acceptera varningen för att fortsätta till ditt program. Certifikatinformationen visar att detta *falska LE Intermediate X1-certifikat* har utfärdats av Let's Encrypt. Det här falska certifikatet `cert-manager` anger att begäran har bearbetats korrekt och tagit emot ett certifikat från providern:

![Vi krypterar mellanlagringscertifikatet](media/ingress/staging-certificate.png)

När du ändrar Let's Encrypt till att använda i stället för används ett betrott certifikat som utfärdats av `prod` `staging` Let's Encrypt, enligt följande exempel:

![Let's Encrypt-certifikat](media/ingress/certificate.png)

Demoprogrammet visas i webbläsaren:

![Programexempel ett](media/ingress/app-one.png)

Lägg nu till *sökvägen /hello-world-two* till FQDN, till exempel *`https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two`* . Det andra demoprogrammet med den anpassade rubriken visas:

![Programexempel två](media/ingress/app-two.png)

## <a name="clean-up-resources"></a>Rensa resurser

Den här artikeln använde Helm för att installera ingresskomponenter, certifikat och exempelappar. När du distribuerar ett Helm-diagram skapas ett antal Kubernetes-resurser. Dessa resurser omfattar poddar, distributioner och tjänster. Om du vill rensa resurserna kan du antingen ta bort hela exempelnamnområdet eller de enskilda resurserna.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Ta bort exempelnamnområdet och alla resurser

Om du vill ta bort hela exempelnamnrymden använder `kubectl delete` du kommandot och anger namnet på namnområdet. Alla resurser i namnområdet tas bort.

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>Ta bort resurser individuellt

En mer detaljerad metod kan också vara att ta bort de enskilda resurser som skapats. Ta först bort certifikatresurserna:

```console
kubectl delete -f certificates.yaml
kubectl delete -f cluster-issuer.yaml
```

Nu listar du Helm-versionerna med `helm list` kommandot . Leta efter diagram med *namnet nginx-ingress* *och cert-manager* enligt följande exempelutdata:

```
$ helm list --all-namespaces

NAME                    NAMESPACE       REVISION        UPDATED                        STATUS          CHART                   APP VERSION
nginx-ingress           ingress-basic   1               2020-01-11 14:51:03.454165006  deployed        nginx-ingress-1.28.2    0.26.2
cert-manager            ingress-basic   1               2020-01-06 21:19:03.866212286  deployed        cert-manager-v0.13.0    v0.13.0
```

Avinstallera versionerna med `helm uninstall` kommandot . I följande exempel avinstalleras NGINX-ingressdistributionen och certifikathanterarens distributioner.

```
$ helm uninstall nginx-ingress cert-manager -n ingress-basic

release "nginx-ingress" deleted
release "cert-manager" deleted
```

Ta sedan bort de två exempelprogrammen:

```console
kubectl delete -f aks-helloworld.yaml --namespace ingress-basic
kubectl delete -f ingress-demo.yaml --namespace ingress-basic
```

Ta bort själva namnområdet. Använd kommandot `kubectl delete` och ange namnet på namnområdet:

```console
kubectl delete namespace ingress-basic
```

Slutligen tar du bort den statiska offentliga IP-adress som skapades för ingress-kontrollanten. Ange namnet *MC_* klusterresursgruppen som du fick i det första steget i den här artikeln, till *exempel MC_myResourceGroup_myAKSCluster_eastus*:

```azurecli-interactive
az network public-ip delete --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP
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
- [Skapa en ingresskontrollant med en dynamisk offentlig IP-adress och konfigurera Let's Encrypt för att automatiskt generera TLS-certifikat][aks-ingress-tls]

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
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az_network_public_ip_create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[client-source-ip]: concepts-network.md#ingress-controllers
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-static-ip]: static-ip.md
