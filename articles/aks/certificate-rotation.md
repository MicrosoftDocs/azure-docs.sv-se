---
title: Rotera certifikat i Azure Kubernetes Service (AKS)
description: Lär dig hur du roterar dina certifikat i ett Azure Kubernetes Service-kluster (AKS).
services: container-service
ms.topic: article
ms.date: 11/15/2019
ms.openlocfilehash: 6baad681a9d629c397c53ab90057cc5746fc3b85
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776023"
---
# <a name="rotate-certificates-in-azure-kubernetes-service-aks"></a>Rotera certifikat i Azure Kubernetes Service (AKS)

Azure Kubernetes Service (AKS) använder certifikat för autentisering med många av dess komponenter. Med jämna mellanrum kan du behöva rotera dessa certifikat av säkerhets- eller principskäl. Du kan till exempel ha en princip för att rotera alla certifikat var 90:e dag.

Den här artikeln visar hur du roterar certifikaten i ditt AKS-kluster.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln kräver att du kör Azure CLI version 2.0.77 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="aks-certificates-certificate-authorities-and-service-accounts"></a>AKS-certifikat, certifikatutfärdare och tjänstkonton

AKS genererar och använder följande certifikat, certifikatutfärdare och tjänstkonton:

* AKS API-servern skapar en certifikatutfärdare (CA) som kallas klustercertifikatutfärdaren.
* API-servern har en klustercertifikatutfärdare som signerar certifikat för envägskommunikation från API-servern till kubelets.
* Varje kubelet skapar också en certifikatsigneringsbegäran (CSR), som signeras av klustercertifikatutfärdaren för kommunikation från kubelet till API-servern.
* API-aggregatorn använder klustercertifikatutfärdaren för att utfärda certifikat för kommunikation med andra API:er. API-aggregatorn kan också ha en egen certifikatutfärdare för att utfärda dessa certifikat, men den använder för närvarande kluster-CA:n.
* Varje nod använder en SA-token (tjänstkonto) som signeras av kluster-CA:n.
* Klienten `kubectl` har ett certifikat för kommunikation med AKS-klustret.

> [!NOTE]
> AKS-kluster som skapats före mars 2019 har certifikat som upphör att gälla efter två år. Alla kluster som skapas efter mars 2019 eller ett kluster som har sina certifikat roterade har kluster-CA-certifikat som upphör att gälla efter 30 år. Alla andra certifikat upphör att gälla efter två år. Om du vill kontrollera när klustret skapades använder du `kubectl get nodes` för att se *nodpoolens* ålder.
> 
> Du kan också kontrollera utgångsdatumet för klustrets certifikat. Följande Bash-kommando visar till exempel certifikatinformationen för *myAKSCluster-klustret.*
> ```console
> kubectl config view --raw -o jsonpath="{.clusters[?(@.name == 'myAKSCluster')].cluster.certificate-authority-data}" | base64 -d | openssl x509 -text | grep -A2 Validity
> ```

## <a name="rotate-your-cluster-certificates"></a>Rotera klustercertifikaten

> [!WARNING]
> Att rotera certifikaten `az aks rotate-certs` med kan orsaka upp till 30 minuters driftstopp för AKS-klustret.

Använd [az aks get-credentials för][az-aks-get-credentials] att logga in på ditt AKS-kluster. Det här kommandot laddar även ned och konfigurerar `kubectl` klientcertifikatet på den lokala datorn.

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

Använd `az aks rotate-certs` för att rotera alla certifikat, certifikat och SA:er i klustret.

```azurecli
az aks rotate-certs -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

> [!IMPORTANT]
> Det kan ta upp till 30 minuter att `az aks rotate-certs` slutföra. Om kommandot misslyckas innan du slutför använder du `az aks show` för att kontrollera att klustrets status är *Certifikatrotering.* Om klustret är i ett misslyckat tillstånd kör du om för `az aks rotate-certs` att rotera dina certifikat igen.

Kontrollera att de gamla certifikaten inte längre är giltiga genom att köra ett `kubectl` kommando. Eftersom du inte har uppdaterat de certifikat som `kubectl` används av visas ett fel.  Exempel:

```console
$ kubectl get no
Unable to connect to the server: x509: certificate signed by unknown authority (possibly because of "crypto/rsa: verification error" while trying to verify candidate authority certificate "ca")
```

Uppdatera certifikatet som används genom `kubectl` att köra `az aks get-credentials` .

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME --overwrite-existing
```

Kontrollera att certifikaten har uppdaterats genom att köra `kubectl` ett kommando som nu kommer att lyckas. Exempel:

```console
kubectl get no
```

> [!NOTE]
> Om du har några tjänster som körs ovanpå AKS, till exempel [Azure Dev Spaces,][dev-spaces]kan du behöva uppdatera certifikat som är relaterade [till dessa][dev-spaces-rotate] tjänster också.

## <a name="next-steps"></a>Nästa steg

Den här artikeln visade hur du automatiskt roterar klustrets certifikat, CERTIFIKAT och SA:er. Du kan läsa [Metodtips för klustersäkerhet och uppgraderingar i Azure Kubernetes Service (AKS)][aks-best-practices-security-upgrades] för mer information om metodtips för AKS-säkerhet.


[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[aks-best-practices-security-upgrades]: operator-best-practices-cluster-security.md
[dev-spaces]: ../dev-spaces/index.yml
[dev-spaces-rotate]: ../dev-spaces/troubleshooting.md#error-using-dev-spaces-after-rotating-aks-certificates
