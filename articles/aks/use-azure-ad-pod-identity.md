---
title: Använda Azure Active Directory Pod-hanterade identiteter i Azure Kubernetes service (för hands version)
description: Lär dig hur du använder AAD-Pod hanterade identiteter i Azure Kubernetes service (AKS)
services: container-service
ms.topic: article
ms.date: 12/01/2020
ms.openlocfilehash: 22b7a03a8598aa6e4b7c392567905d467776360c
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99557366"
---
# <a name="use-azure-active-directory-pod-managed-identities-in-azure-kubernetes-service-preview"></a>Använda Azure Active Directory Pod-hanterade identiteter i Azure Kubernetes service (för hands version)

Azure Active Directory Pod-hanterade identiteter använder Kubernetes-primitiver för att associera [hanterade identiteter för Azure-resurser][az-managed-identities] och identiteter i Azure Active Directory (AAD) med poddar. Administratörer skapar identiteter och bindningar som Kubernetes-primitiver som ger poddar åtkomst till Azure-resurser som förlitar sig på AAD som identitets leverantör.

> [!NOTE]
> Om du har en befintlig installation av AADPODIDENTITY måste du ta bort den befintliga installationen. Aktivering av den här funktionen innebär att MIC-komponenten inte behövs.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>Innan du börjar

Du måste ha följande resurs installerad:

* Azure CLI, version 2.8.0 eller senare
* `azure-preview`Tilläggs version 0.4.68 eller senare

### <a name="limitations"></a>Begränsningar

* Högst 50 Pod-identiteter tillåts för ett kluster.
* Högst 50 Pod-identitets undantag är tillåtna för ett kluster.
* Pod-hanterade identiteter är bara tillgängliga på Linux-nodkonfigurationer.

### <a name="register-the-enablepodidentitypreview"></a>Registrera `EnablePodIdentityPreview`

Registrera `EnablePodIdentityPreview` funktionen:

```azurecli
az feature register --name EnablePodIdentityPreview --namespace Microsoft.ContainerService
```

### <a name="install-the-aks-preview-azure-cli"></a>Installera `aks-preview` Azure CLI

Du behöver också *AKS – för hands* version av Azure CLI-tillägget 0.4.64 eller senare. Installera *AKS-Preview* Azure CLI-tillägget med kommandot [AZ Extension Add][az-extension-add] . Eller installera eventuella tillgängliga uppdateringar med kommandot [AZ Extension Update][az-extension-update] .

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="create-an-aks-cluster-with-managed-identities"></a>Skapa ett AKS-kluster med hanterade identiteter

Skapa ett AKS-kluster med en hanterad identitet och Pod-hanterad identitet aktive rad. Följande kommandon använder [AZ Group Create][az-group-create] för att skapa en resurs grupp med *namnet myResourceGroup* och kommandot [AZ AKS Create][az-aks-create] för att skapa ett AKS-kluster med namnet *myAKSCluster* i resurs gruppen *myResourceGroup* .

```azurecli-interactive
az group create --name myResourceGroup --location eastus
az aks create -g myResourceGroup -n myAKSCluster --enable-managed-identity --enable-pod-identity --network-plugin azure
```

Använd [AZ AKS get-credentials][az-aks-get-credentials] för att logga in på ditt AKS-kluster. Det här kommandot hämtar också och konfigurerar `kubectl` klient certifikatet på din utvecklings dator.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="create-an-identity"></a>Skapa en identitet

Skapa en identitet med [AZ Identity Create][az-identity-create] och ange *IDENTITY_CLIENT_ID* och *IDENTITY_RESOURCE_ID* variabler.

```azurecli-interactive
az group create --name myIdentityResourceGroup --location eastus
export IDENTITY_RESOURCE_GROUP="myIdentityResourceGroup"
export IDENTITY_NAME="application-identity"
az identity create --resource-group ${IDENTITY_RESOURCE_GROUP} --name ${IDENTITY_NAME}
export IDENTITY_CLIENT_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME} --query clientId -otsv)"
export IDENTITY_RESOURCE_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME} --query id -otsv)"
```

## <a name="create-a-pod-identity"></a>Skapa en POD-identitet

Skapa en POD-identitet för klustret med hjälp av `az aks pod-identity add` .

> [!IMPORTANT]
> Du måste ha rätt behörighet, till exempel `Owner` , på din prenumeration för att skapa identiteten och roll bindningen.

```azurecli-interactive
export POD_IDENTITY_NAME="my-pod-identity"
export POD_IDENTITY_NAMESPACE="my-app"
az aks pod-identity add --resource-group myResourceGroup --cluster-name myAKSCluster --namespace ${POD_IDENTITY_NAMESPACE}  --name ${POD_IDENTITY_NAME} --identity-resource-id ${IDENTITY_RESOURCE_ID}
```

> [!NOTE]
> När du aktiverar Pod-hanterad identitet i ditt AKS-kluster läggs en AzurePodIdentityException med namnet *AKS-addon-Exception* till i namn området för *Kube-systemet* . En AzurePodIdentityException gör det möjligt för poddar med vissa etiketter att komma åt Azure Instance Metadata Service-slutpunkten (IMDS) utan att fångas upp av NMI-servern (Node-Managed Identity). Med *AKS-addon-Exception* kan AKS från första part, till exempel AAD Pod-hanterad identitet, användas utan att behöva konfigurera en AzurePodIdentityException manuellt. Du kan också lägga till, ta bort och uppdatera en AzurePodIdentityException med hjälp av `az aks pod-identity exception add` , `az aks pod-identity exception delete` , `az aks pod-identity exception update` , eller `kubectl` .

## <a name="run-a-sample-application"></a>Köra ett exempel program

För att en POD ska kunna använda AAD Pod-hanterad identitet behöver Pod en *aadpodidbinding* -etikett med ett värde som matchar en selektor från en *AzureIdentityBinding*. Om du vill köra ett exempel program med AAD Pod-hanterad identitet skapar du en `demo.yaml` fil med följande innehåll. Ersätt *POD_IDENTITY_NAME*, *IDENTITY_CLIENT_ID* och *IDENTITY_RESOURCE_GROUP* med värdena från föregående steg. Ersätt *SUBSCRIPTION_ID* med ditt prenumerations-ID.

> [!NOTE]
> I föregående steg skapade du variablerna *POD_IDENTITY_NAME*, *IDENTITY_CLIENT_ID* och *IDENTITY_RESOURCE_GROUP* . Du kan använda ett kommando `echo` för att visa värdet som du anger för variabler, till exempel `echo $IDENTITY_NAME` .

```yml
apiVersion: v1
kind: Pod
metadata:
  name: demo
  labels:
    aadpodidbinding: POD_IDENTITY_NAME
spec:
  containers:
  - name: demo
    image: mcr.microsoft.com/oss/azure/aad-pod-identity/demo:v1.6.3
    args:
      - --subscriptionid=SUBSCRIPTION_ID
      - --clientid=IDENTITY_CLIENT_ID
      - --resourcegroup=IDENTITY_RESOURCE_GROUP
    env:
      - name: MY_POD_NAME
        valueFrom:
          fieldRef:
            fieldPath: metadata.name
      - name: MY_POD_NAMESPACE
        valueFrom:
          fieldRef:
            fieldPath: metadata.namespace
      - name: MY_POD_IP
        valueFrom:
          fieldRef:
            fieldPath: status.podIP
  nodeSelector:
    kubernetes.io/os: linux
```

Observera att Pod-definitionen har en *aadpodidbinding* -etikett med ett värde som matchar namnet på pod-identiteten som du körde `az aks pod-identity add` i föregående steg.

Distribuera `demo.yaml` till samma namnrymd som din POD-identitet med hjälp av `kubectl apply` :

```azurecli-interactive
kubectl apply -f demo.yaml --namespace $POD_IDENTITY_NAMESPACE
```

Kontrol lera att exempel programmet har körts med `kubectl logs` .

```azurecli-interactive
kubectl logs demo --follow --namespace $POD_IDENTITY_NAMESPACE
```

Verifiera att loggarna visar att token har hämtats och att *hämtningen* lyckades.
 
```output
...
successfully doARMOperations vm count 0
successfully acquired a token using the MSI, msiEndpoint(http://169.254.169.254/metadata/identity/oauth2/token)
successfully acquired a token, userAssignedID MSI, msiEndpoint(http://169.254.169.254/metadata/identity/oauth2/token) clientID(xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx)
successfully made GET on instance metadata
...
```

## <a name="clean-up"></a>Rensa

Ta bort AAD Pod-hanterad identitet från klustret genom att ta bort exempel programmet och Pod-identiteten från klustret. Ta sedan bort identiteten.

```azurecli-interactive
kubectl delete pod demo --namespace $POD_IDENTITY_NAMESPACE
az aks pod-identity delete --name ${POD_IDENTITY_NAME} --namespace ${POD_IDENTITY_NAMESPACE} --resource-group myResourceGroup --cluster-name myAKSCluster
az identity delete -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME}
```

## <a name="next-steps"></a>Nästa steg

Mer information om hanterade identiteter finns i [hanterade identiteter för Azure-resurser][az-managed-identities].

<!-- LINKS - external -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest#az-extension-add&preserve-view=true
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest#az-extension-update&preserve-view=true
[az-group-create]: /cli/azure/group#az-group-create
[az-identity-create]: /cli/azure/identity?view=azure-cli-latest#az_identity_create
[az-managed-identities]: ../active-directory/managed-identities-azure-resources/overview.md
[az-role-assignment-create]: /cli/azure/role/assignment?view=azure-cli-latest#az_role_assignment_create
