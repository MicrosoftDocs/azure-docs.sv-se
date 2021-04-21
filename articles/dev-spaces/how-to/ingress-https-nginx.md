---
title: Använda en anpassad NGINX-ingresskontrollant och konfigurera HTTPS
services: azure-dev-spaces
ms.date: 12/10/2019
ms.topic: conceptual
description: Lär dig hur du konfigurerar Azure Dev Spaces att använda en anpassad NGINX-ingresskontrollant och konfigurerar HTTPS med hjälp av den ingress-kontrollanten
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s
ms.custom: devx-track-js
ms.openlocfilehash: b07d66e0031b907811c4ec251987aa020542b05a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777517"
---
# <a name="use-a-custom-nginx-ingress-controller-and-configure-https"></a>Använda en anpassad NGINX-ingresskontrollant och konfigurera HTTPS

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Den här artikeln visar hur du konfigurerar Azure Dev Spaces för att använda en anpassad NGINX-ingresskontrollant. Den här artikeln visar också hur du konfigurerar den anpassade ingress-kontrollanten för att använda HTTPS.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon, kan du skapa ett [kostnadsfritt konto][azure-account-create].
* [Azure CLI installerat][az-cli].
* Azure Kubernetes Service kluster (AKS) med Azure Dev Spaces aktiverat.
* [kubectl][kubectl] installerat.
* [Helm 3 installerat.][helm-installed]
* [En anpassad domän][custom-domain] med en [DNS-zon][dns-zone].  Den här artikeln förutsätter att den anpassade domänen och DNS-zonen finns i samma resursgrupp som ditt AKS-kluster, men det är möjligt att använda en anpassad domän och DNS-zon i en annan resursgrupp.

## <a name="configure-a-custom-nginx-ingress-controller"></a>Konfigurera en anpassad NGINX-ingresskontrollant

Anslut till klustret med [kubectl][kubectl], Kubernetes-kommandoradsklienten. För att konfigurera `kubectl` till att ansluta till ditt Kubernetes-kluster använder du kommandot [az aks get-credentials][az-aks-get-credentials]. Det här kommandot laddar ned autentiseringsuppgifter och konfigurerar Kubernetes CLI för att använda dem.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKS
```

Du kan kontrollera anslutningen till klustret genom att köra kommandot [kubectl get][kubectl-get] för att returnera en lista över klusternoderna.

```console
kubectl get nodes
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.1
```

Lägg till [den officiella stabila Helm-lagringsplatsen][helm-stable-repo], som innehåller Helm-diagrammet för NGINX-ingresskontrollanten.

```console
helm repo add stable https://kubernetes-charts.storage.googleapis.com/
```

Skapa ett Kubernetes-namnområde för NGINX-indatakontrollanten och installera den med hjälp av `helm` .

```console
kubectl create ns nginx
helm install nginx stable/nginx-ingress --namespace nginx --version 1.27.0
```

> [!NOTE]
> I exemplet ovan skapas en offentlig slutpunkt för ingress-kontrollanten. Om du i stället behöver använda en privat slutpunkt för ingress-kontrollanten lägger du till *--set controller.service.annotations." service \\ .beta \\ .kubernetes \\ .io/azure-load-balancer-internal"=true* parameter to the *helm install* command. Exempel:
> ```console
> helm install nginx stable/nginx-ingress --namespace nginx --set controller.service.annotations."service\.beta\.kubernetes\.io/azure-load-balancer-internal"=true --version 1.27.0
> ```
> Den här privata slutpunkten exponeras i det virtuella nätverk där du distribuerar ditt AKS-kluster.

Hämta IP-adressen för NGINX-ingresskontrollanttjänsten med [kubectl get][kubectl-get].

```console
kubectl get svc -n nginx --watch
```

Exempelutdata visar IP-adresserna för alla tjänster i *namnrymden nginx.*

```console
NAME                                  TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                      AGE
nginx-nginx-ingress-controller        LoadBalancer   10.0.19.39     <pending>        80:31314/TCP,443:30521/TCP   10s
nginx-nginx-ingress-default-backend   ClusterIP      10.0.210.231   <none>           80/TCP                       10s
...
nginx-nginx-ingress-controller        LoadBalancer   10.0.19.39     MY_EXTERNAL_IP   80:31314/TCP,443:30521/TCP   26s
```

Lägg till *en A-post* i DNS-zonen med den externa IP-adressen för NGINX-tjänsten med [az network dns record-set a add-record][az-network-dns-record-set-a-add-record].

```azurecli
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name *.nginx \
    --ipv4-address MY_EXTERNAL_IP
```

I exemplet ovan läggs en *A-post* till i *MY_CUSTOM_DOMAIN* DNS-zon.

I den här artikeln använder du [exempelprogrammet Azure Dev Spaces Bike Sharing för](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) att demonstrera hur du använder Azure Dev Spaces. Klona programmet från GitHub och gå till programmets katalog:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/charts
```

Öppna [values.yaml][values-yaml] och gör följande uppdateringar:
* Ersätt alla instanser av *<REPLACE_ME_WITH_HOST_SUFFIX>* med *nginx. MY_CUSTOM_DOMAIN* med din domän för *MY_CUSTOM_DOMAIN*. 
* Ersätt *kubernetes.io/ingress.class: traefik-azds # Dev Spaces-specific* *med kubernetes.io/ingress.class: nginx # Custom Ingress*. 

Nedan visas ett exempel på en uppdaterad `values.yaml` fil:

```yaml
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

bikesharingweb:
  ingress:
    annotations:
      kubernetes.io/ingress.class: nginx  # Custom Ingress
    hosts:
      - dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space

gateway:
  ingress:
    annotations:
      kubernetes.io/ingress.class: nginx  # Custom Ingress
    hosts:
      - dev.gateway.nginx.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
```

Spara ändringarna och stäng filen.

Skapa *utvecklingsutrymmet* med exempelprogrammet med hjälp av `azds space select` .

```console
azds space select -n dev -y
```

Distribuera exempelprogrammet med `helm install` .

```console
helm install bikesharingsampleapp . --dependency-update --namespace dev --atomic
```

Exemplet ovan distribuerar exempelprogrammet till *namnområdet* dev.

Visa URL:erna för att komma åt exempelprogrammet med hjälp av `azds list-uris` .

```console
azds list-uris
```

Följande utdata visar exempel-URL:er från `azds list-uris` .

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN/  Available
http://dev.gateway.nginx.MY_CUSTOM_DOMAIN/         Available
```

Navigera till *bikesharingweb-tjänsten* genom att öppna den offentliga URL:en från `azds list-uris` kommandot . I exemplet ovan är den offentliga URL:en *för bikesharingweb-tjänsten* `http://dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN/` .

> [!NOTE]
> Om du ser en felsida i stället för *bikesharingweb-tjänsten* kontrollerar du att du har uppdaterat både *kubernetes.io/ingress.class-anteckningen* och värden i *filen values.yaml.* 

Använd kommandot `azds space select` för att skapa ett under-utrymme under *dev* och lista URL:erna för åtkomst till det underordnade utvecklingsutrymmet.

```console
azds space select -n dev/azureuser1 -y
azds list-uris
```

Följande utdata visar exempel-URL:er från `azds list-uris` för att komma åt exempelprogrammet i utrymmet *azureuser1* child dev.

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://azureuser1.s.dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN/  Available
http://azureuser1.s.dev.gateway.nginx.MY_CUSTOM_DOMAIN/         Available
```

Navigera till *bikesharingweb-tjänsten* i utrymmet *azureuser1* child dev genom att öppna den offentliga URL:en från `azds list-uris` kommandot . I exemplet ovan är den offentliga URL:en för *bikesharingweb-tjänsten* i den underordnade utvecklingsrymden *azureuser1* `http://azureuser1.s.dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN/` .

## <a name="configure-the-nginx-ingress-controller-to-use-https"></a>Konfigurera NGINX-ingressstyrenheten att använda HTTPS

Använd [cert-manager för][cert-manager] att automatisera hanteringen av TLS-certifikatet när du konfigurerar NGINX-ingress-kontrollanten att använda HTTPS. Använd `helm` för att installera *certmanager-diagrammet.*

```console
kubectl apply --validate=false -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.12/deploy/manifests/00-crds.yaml --namespace nginx
kubectl label namespace nginx certmanager.k8s.io/disable-validation=true
helm repo add jetstack https://charts.jetstack.io
helm repo update
helm install cert-manager --namespace nginx --version v0.12.0 jetstack/cert-manager --set ingressShim.defaultIssuerName=letsencrypt --set ingressShim.defaultIssuerKind=ClusterIssuer
```

Skapa en `letsencrypt-clusterissuer.yaml` fil och uppdatera e-postfältet med din e-postadress.

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
```

> [!NOTE]
> För testning finns det också en [mellanlagringsserver][letsencrypt-staging-issuer] som du kan använda för *clusterIssuer.*

Använd `kubectl` för att tillämpa `letsencrypt-clusterissuer.yaml` .

```console
kubectl apply -f letsencrypt-clusterissuer.yaml --namespace nginx
```

Uppdatera [values.yaml så][values-yaml] att den innehåller information om hur *du använder cert-manager* och HTTPS. Nedan visas ett exempel på en uppdaterad `values.yaml` fil:

```yaml
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

bikesharingweb:
  ingress:
    annotations:
      kubernetes.io/ingress.class: nginx  # Custom Ingress
      cert-manager.io/cluster-issuer: letsencrypt
    hosts:
      - dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
    tls:
    - hosts:
      - dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN
      secretName: dev-bikesharingweb-secret

gateway:
  ingress:
    annotations:
      kubernetes.io/ingress.class: nginx  # Custom Ingress
      cert-manager.io/cluster-issuer: letsencrypt
    hosts:
      - dev.gateway.nginx.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
    tls:
    - hosts:
      - dev.gateway.nginx.MY_CUSTOM_DOMAIN
      secretName: dev-gateway-secret
```

Uppgradera exempelprogrammet med `helm` :

```console
helm upgrade bikesharingsampleapp . --namespace dev --atomic
```

Gå till exempelprogrammet i det underordnade *utrymmet dev/azureuser1* och lägg märke till att du omdirigeras för att använda HTTPS. Observera också att sidan läses in, men webbläsaren visar några fel. När du öppnar webbläsarkonsolen visas felet som rör en HTTPS-sida som försöker läsa in HTTP-resurser. Exempel:

```console
Mixed Content: The page at 'https://azureuser1.s.dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN/devsignin' was loaded over HTTPS, but requested an insecure resource 'http://azureuser1.s.dev.gateway.nginx.MY_CUSTOM_DOMAIN/api/user/allUsers'. This request has been blocked; the content must be served over HTTPS.
```

Åtgärda det här felet genom att [uppdatera BikeSharingWeb/azds.yaml][azds-yaml] enligt nedan:

```yaml
...
    ingress:
      annotations:
        kubernetes.io/ingress.class: nginx
        cert-manager.io/cluster-issuer: letsencrypt
      hosts:
      # This expands to [space.s.][rootSpace.]bikesharingweb.<random suffix>.<region>.azds.io
      - $(spacePrefix)$(rootSpacePrefix)bikesharingweb.nginx.MY_CUSTOM_DOMAIN
      tls:
      - hosts:
        - $(spacePrefix)$(rootSpacePrefix)bikesharingweb.nginx.MY_CUSTOM_DOMAIN
        secretName: dev-bikesharingweb-secret
...
```

Uppdatera [BikeSharingWeb/package.jspå][package-json] med ett beroende för *URL-paketet.*

```json
{
...
    "react-responsive": "^6.0.1",
    "universal-cookie": "^3.0.7",
    "url": "0.11.0"
  },
...
```

Uppdatera metoden *getApiHostAsync* i [BikeSharingWeb/lib/helpers.jsatt][helpers-js] använda HTTPS:

```javascript
...
    getApiHostAsync: async function() {
        const apiRequest = await fetch('/api/host');
        const data = await apiRequest.json();
        
        var urlapi = require('url');
        var url = urlapi.parse(data.apiHost);

        console.log('apiHost: ' + "https://"+url.host);
        return "https://"+url.host;
    },
...
```

Gå till katalogen `BikeSharingWeb` och använd för att köra din uppdaterade `azds up` *BikeSharingWeb-tjänst.*

```console
cd ../BikeSharingWeb/
azds up
```

Gå till exempelprogrammet i det underordnade *utrymmet dev/azureuser1* och lägg märke till att du omdirigeras till https utan fel.

## <a name="next-steps"></a>Nästa steg

Läs mer om hur Azure Dev Spaces fungerar.

> [!div class="nextstepaction"]
> [Så här fungerar Azure Dev Spaces](../how-dev-spaces-works.md)


[az-cli]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/a#az_network_dns_record_set_a_add_record
[custom-domain]: ../../app-service/manage-custom-dns-buy-domain.md#buy-an-app-service-domain
[dns-zone]: ../../dns/dns-getstarted-cli.md
[azds-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/azds.yaml
[azure-account-create]: https://azure.microsoft.com/free
[cert-manager]: https://cert-manager.io/
[helm-installed]: https://helm.sh/docs/intro/install/
[helm-stable-repo]: https://helm.sh/docs/intro/quickstart/#initialize-a-helm-chart-repository
[helpers-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/lib/helpers.js#L7
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[letsencrypt-staging-issuer]: https://cert-manager.io/docs/configuration/acme/#creating-a-basic-acme-issuer
[package-json]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/package.json
[values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml
