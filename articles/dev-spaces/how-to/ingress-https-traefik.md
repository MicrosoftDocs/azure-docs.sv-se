---
title: Använd en anpassad traefik ingångs kontroll och konfigurera HTTPS
services: azure-dev-spaces
ms.date: 12/10/2019
ms.topic: conceptual
description: Lär dig hur du konfigurerar Azure dev Spaces för att använda en anpassad traefik ingångs kontroll och konfigurera HTTPS med den här ingångs styrenheten
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes service, Containers, Helm, service nät, service nät-routning, kubectl, K8s
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 036725f3c1eb909407e157d33ece05b1f55213ce
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102204107"
---
# <a name="use-a-custom-traefik-ingress-controller-and-configure-https"></a>Använd en anpassad traefik ingångs kontroll och konfigurera HTTPS

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Den här artikeln visar hur du konfigurerar Azure dev Spaces för att använda en anpassad traefik ingångs kontroll. Den här artikeln visar också hur du konfigurerar den anpassade ingångs styrenheten att använda HTTPS.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon, kan du skapa ett [kostnadsfritt konto][azure-account-create].
* [Azure CLI installerat][az-cli].
* [Azure Kubernetes service (AKS)-kluster med Azure dev-platser aktiverat] [QS-CLI].
* [kubectl][kubectl] installerad.
* [Helm 3 är installerat][helm-installed].
* [En anpassad domän][custom-domain] med en [DNS-zon][dns-zone]. Den här artikeln förutsätter att den anpassade domänen och DNS-zonen finns i samma resurs grupp som ditt AKS-kluster, men det går att använda en anpassad domän och DNS-zon i en annan resurs grupp.

## <a name="configure-a-custom-traefik-ingress-controller"></a>Konfigurera en anpassad traefik-kontrollant

Anslut till klustret med [kubectl][kubectl], Kubernetes kommando rads klient. För att konfigurera `kubectl` till att ansluta till ditt Kubernetes-kluster använder du kommandot [az aks get-credentials][az-aks-get-credentials]. Det här kommandot laddar ned autentiseringsuppgifter och konfigurerar Kubernetes CLI för att använda dem.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKS
```

Du kan kontrollera anslutningen till klustret genom att köra kommandot [kubectl get][kubectl-get] för att returnera en lista över klusternoderna.

```console
kubectl get nodes
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.1
```

Lägg till den [officiella stabila Helm-lagringsplatsen][helm-stable-repo]som innehåller Helm-diagrammet traefik ingress ingångs kontroll.

```console
helm repo add stable https://charts.helm.sh/stable
```

Skapa ett Kubernetes-namnområde för traefik ingress-kontrollanten och installera den med hjälp av `helm` .

> [!NOTE]
> Om ditt AKS-kluster inte har Kubernetes RBAC aktiverat tar du bort parametern *--set RBAC. enabled = True* .

```console
kubectl create ns traefik
helm install traefik stable/traefik --namespace traefik --set kubernetes.ingressClass=traefik --set rbac.enabled=true --set fullnameOverride=customtraefik --set kubernetes.ingressEndpoint.useDefaultPublishedService=true --version 1.85.0
```

> [!NOTE]
> Exemplet ovan skapar en offentlig slut punkt för din ingångs kontroll. Om du behöver använda en privat slut punkt för ingångs styrenheten i stället lägger du till *--set-tjänsten. anteckningar. service \\ . beta \\ . Kubernetes \\ . io/Azure-Load-Balancer-Internal "= true-* parameter till kommandot *Helm install* .
> ```console
> helm install traefik stable/traefik --namespace traefik --set kubernetes.ingressClass=traefik --set rbac.enabled=true --set fullnameOverride=customtraefik --set kubernetes.ingressEndpoint.useDefaultPublishedService=true --set service.annotations."service\.beta\.kubernetes\.io/azure-load-balancer-internal"=true --version 1.85.0
> ```
> Den här privata slut punkten exponeras i det virtuella nätverket där du AKS-klustret distribueras.

Hämta IP-adressen för tjänsten traefik ingångs kontroll med hjälp av [kubectl get][kubectl-get].

```console
kubectl get svc -n traefik --watch
```

Exempel resultatet visar IP-adresserna för alla tjänster i *traefik* namn området.

```console
NAME      TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
traefik   LoadBalancer   10.0.205.78   <pending>     80:32484/TCP,443:30620/TCP   20s
...
traefik   LoadBalancer   10.0.205.78   MY_EXTERNAL_IP   80:32484/TCP,443:30620/TCP   60s
```

Lägg till en *A* -post i DNS-zonen med den externa IP-adressen för traefik-tjänsten med [AZ Network DNS Record-set A Add-Record][az-network-dns-record-set-a-add-record].

```azurecli
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name *.traefik \
    --ipv4-address MY_EXTERNAL_IP
```

Exemplet ovan lägger till en *A* -post i *MY_CUSTOM_DOMAIN* DNS-zonen.

I den här artikeln använder du [exempel programmet Azure dev Spaces Bike sharing](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) för att demonstrera användningen av Azure dev Spaces. Klona programmet från GitHub och gå till programmets katalog:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/charts
```

Öppna [Values. yaml][values-yaml] och gör följande uppdateringar:
* Ersätt alla instanser av *<REPLACE_ME_WITH_HOST_SUFFIX>* med *traefik. MY_CUSTOM_DOMAIN* att använda din domän för *MY_CUSTOM_DOMAIN*. 
* Ersätt *Kubernetes.io/ingress.Class: traefik-azds # dev Spaces-/regionsspecifika* med *Kubernetes.io/ingress.Class: Traefik # Custom ingress*. 

Nedan visas ett exempel på en uppdaterad `values.yaml` fil:

```yaml
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

bikesharingweb:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
    hosts:
      - dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space

gateway:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
    hosts:
      - dev.gateway.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
```

Spara ändringarna och Stäng filen.

Skapa ett *dev* -utrymme med ditt exempel program med hjälp av `azds space select` .

```console
azds space select -n dev -y
```

Distribuera exempel programmet med `helm install` .

```console
helm install bikesharingsampleapp . --dependency-update --namespace dev --atomic
```

Exemplet ovan distribuerar exempel programmet till *dev* -namnområdet.

Visa URL: erna för att komma åt exempel programmet med `azds list-uris` .

```console
azds list-uris
```

I nedanstående utdata visas exempel-URL: er från `azds list-uris` .

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

Navigera till *bikesharingweb* -tjänsten genom att öppna den offentliga URL: en från `azds list-uris` kommandot. I exemplet ovan är den offentliga URL: en för *bikesharingweb* -tjänsten `http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/` .

> [!NOTE]
> Om du ser en felsida i stället för *bikesharingweb* -tjänsten kontrollerar du att du har uppdaterat **både** *Kubernetes.io/ingress.class* -anteckningen och värden i filen *Values. yaml* .

Använd `azds space select` kommandot för att skapa ett underordnat utrymme under *dev* och listar URL: erna för att få åtkomst till det underordnade dev-utrymmet.

```console
azds space select -n dev/azureuser1 -y
azds list-uris
```

I nedanstående utdata visas exempel-URL: er från `azds list-uris` för att komma åt exempel programmet i *azureuser1* -underordnat dev-utrymme.

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

Navigera till *bikesharingweb* -tjänsten i det *azureuser1* underordnade dev-området genom att öppna den offentliga URL: en från `azds list-uris` kommandot. I exemplet ovan är den offentliga URL: en för *bikesharingweb* -tjänsten i det *azureuser1* underordnade dev-utrymmet `http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/` .

## <a name="configure-the-traefik-ingress-controller-to-use-https"></a>Konfigurera traefik ingress Controller för att använda HTTPS

Använd [cert Manager][cert-manager] för att automatisera hanteringen av TLS-certifikatet när du konfigurerar din traefik-ingångs kontroll för att använda https. Använd `helm` för att installera *certmanager* -diagrammet.

```console
kubectl apply --validate=false -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.12/deploy/manifests/00-crds.yaml --namespace traefik
kubectl label namespace traefik certmanager.k8s.io/disable-validation=true
helm repo add jetstack https://charts.jetstack.io
helm repo update
helm install cert-manager --namespace traefik --version v0.12.0 jetstack/cert-manager --set ingressShim.defaultIssuerName=letsencrypt --set ingressShim.defaultIssuerKind=ClusterIssuer
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
            class: traefik
```

> [!NOTE]
> För testning finns det också en [fristående server][letsencrypt-staging-issuer] som du kan använda för din *ClusterIssuer*.

Använd `kubectl` för att tillämpa `letsencrypt-clusterissuer.yaml` .

```console
kubectl apply -f letsencrypt-clusterissuer.yaml --namespace traefik
```

Ta bort föregående *traefik* - *ClusterRole* och *ClusterRoleBinding* och uppgradera sedan traefik för att använda https med hjälp av `helm` .

> [!NOTE]
> Om ditt AKS-kluster inte har Kubernetes RBAC aktiverat tar du bort parametern *--set RBAC. enabled = True* .

```console
kubectl delete ClusterRole traefik
kubectl delete ClusterRoleBinding traefik
helm upgrade traefik stable/traefik --namespace traefik --set kubernetes.ingressClass=traefik --set rbac.enabled=true --set kubernetes.ingressEndpoint.useDefaultPublishedService=true --version 1.85.0 --set ssl.enabled=true --set ssl.enforced=true --set ssl.permanentRedirect=true
```

Hämta den uppdaterade IP-adressen för traefik ingress Controller-tjänsten med hjälp av [kubectl get][kubectl-get].

```console
kubectl get svc -n traefik --watch
```

Exempel resultatet visar IP-adresserna för alla tjänster i *traefik* namn området.

```console
NAME      TYPE           CLUSTER-IP    EXTERNAL-IP          PORT(S)                      AGE
traefik   LoadBalancer   10.0.205.78   <pending>            80:32484/TCP,443:30620/TCP   20s
...
traefik   LoadBalancer   10.0.205.78   MY_NEW_EXTERNAL_IP   80:32484/TCP,443:30620/TCP   60s
```

Lägg till en *A* -post i DNS-zonen med den nya externa IP-adressen för traefik-tjänsten med [AZ Network DNS Record-set a Add-Record][az-network-dns-record-set-a-add-record] och ta bort den tidigare *A* -posten med [AZ Network DNS Record-set a Remove-Record][az-network-dns-record-set-a-remove-record].

```azurecli
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name *.traefik \
    --ipv4-address MY_NEW_EXTERNAL_IP

az network dns record-set a remove-record \
    --resource-group myResourceGroup \
    --zone-name  MY_CUSTOM_DOMAIN \
    --record-set-name *.traefik \
    --ipv4-address PREVIOUS_EXTERNAL_IP
```

Exemplet ovan uppdaterar *en* post i DNS-zonen *MY_CUSTOM_DOMAIN* att använda *PREVIOUS_EXTERNAL_IP*.

Uppdatera [Values. yaml][values-yaml] för att inkludera information om hur du använder *cert Manager* och https. Nedan visas ett exempel på en uppdaterad `values.yaml` fil:

```yaml
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

bikesharingweb:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
      cert-manager.io/cluster-issuer: letsencrypt
    hosts:
      - dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
    tls:
    - hosts:
      - dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN
      secretName: dev-bikesharingweb-secret

gateway:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
      cert-manager.io/cluster-issuer: letsencrypt
    hosts:
      - dev.gateway.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
    tls:
    - hosts:
      - dev.gateway.traefik.MY_CUSTOM_DOMAIN
      secretName: dev-gateway-secret
```

Uppgradera exempel programmet med `helm` :

```console
helm upgrade bikesharingsampleapp . --namespace dev --atomic
```

Navigera till exempel programmet i det underordnade området *dev/azureuser1* och Observera att du omdirigeras till att använda https.

> [!IMPORTANT]
> Det kan ta 30 minuter eller mer för DNS-ändringarna att slutföras och ditt exempel program är tillgängligt.

Observera också att sidan läses in, men webbläsaren visar vissa fel. Om du öppnar webb läsar konsolen visas felet relaterar till en HTTPS-sida vid försök att läsa in HTTP-resurser. Exempel:

```console
Mixed Content: The page at 'https://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/devsignin' was loaded over HTTPS, but requested an insecure resource 'http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/api/user/allUsers'. This request has been blocked; the content must be served over HTTPS.
```

Åtgärda felet genom att uppdatera [BikeSharingWeb/azds. yaml][azds-yaml] för att använda *traefik* för *Kubernetes.io/ingress.class* och din anpassade domän för *$ (hostSuffix)*. Exempel:

```yaml
...
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik
      hosts:
      # This expands to [space.s.][rootSpace.]bikesharingweb.<random suffix>.<region>.azds.io
      - $(spacePrefix)$(rootSpacePrefix)bikesharingweb.traefik.MY_CUSTOM_DOMAIN
...
```

Uppdatera [BikeSharingWeb/package.jspå][package-json] med ett beroende för *URL* -paketet.

```json
{
...
    "react-responsive": "^6.0.1",
    "universal-cookie": "^3.0.7",
    "url": "0.11.0"
  },
...
```

Uppdatera *getApiHostAsync* -metoden i [BikeSharingWeb/lib/helpers.js][helpers-js] att använda https:

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

Navigera till `BikeSharingWeb` katalogen och Använd `azds up` för att köra din uppdaterade BikeSharingWeb-tjänst.

```console
cd ../BikeSharingWeb/
azds up
```

Navigera till exempel programmet i det underordnade området *dev/azureuser1* och Observera att du omdirigeras till att använda https utan fel.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur Azure dev Spaces fungerar.

> [!div class="nextstepaction"]
> [Så här fungerar Azure Dev Spaces](../how-dev-spaces-works.md)


[az-cli]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/a#az-network-dns-record-set-a-add-record
[az-network-dns-record-set-a-remove-record]: /cli/azure/network/dns/record-set/a#az-network-dns-record-set-a-remove-record
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
