---
title: Använda Azure Active Directory podd-hanterade identiteter i Azure Kubernetes Service (förhandsversion)
description: Lär dig hur du använder AAD-podd-hanterade identiteter i Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 3/12/2021
ms.openlocfilehash: f090f5e11688f35ce090bb07ec0d23530bf9d90e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777859"
---
# <a name="use-azure-active-directory-pod-managed-identities-in-azure-kubernetes-service-preview"></a>Använda Azure Active Directory podd-hanterade identiteter i Azure Kubernetes Service (förhandsversion)

Azure Active Directory podd-hanterade identiteter använder Kubernetes-primitiver för att associera hanterade identiteter för [Azure-resurser][az-managed-identities] och identiteter i Azure Active Directory (AAD) med poddar. Administratörer skapar identiteter och bindningar som Kubernetes-primitiver som tillåter poddar att komma åt Azure-resurser som förlitar sig på AAD som identitetsprovider.

> [!NOTE]
> Om du har en befintlig installation av AADPODIDENTITY måste du ta bort den befintliga installationen. Aktivering av den här funktionen innebär att MIC-komponenten inte behövs.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>Innan du börjar

Du måste ha följande resurs installerad:

* Azure CLI, version 2.20.0 eller senare
* Tilläggsversion `azure-preview` 0.5.5 eller senare

### <a name="limitations"></a>Begränsningar

* Högst 200 poddidentiteter tillåts för ett kluster.
* Högst 200 poddidentitetundantag tillåts för ett kluster.
* Podd-hanterade identiteter är endast tillgängliga i Linux-nodpooler.

### <a name="register-the-enablepodidentitypreview"></a>Registrera `EnablePodIdentityPreview`

Registrera `EnablePodIdentityPreview` funktionen:

```azurecli
az feature register --name EnablePodIdentityPreview --namespace Microsoft.ContainerService
```

### <a name="install-the-aks-preview-azure-cli"></a>Installera `aks-preview` Azure CLI

Du behöver även *Azure CLI-tillägget aks-preview* version 0.4.64 eller senare. Installera Azure *CLI-tillägget aks-preview* med kommandot [az extension add.][az-extension-add] Eller installera eventuella tillgängliga uppdateringar med hjälp av [kommandot az extension update.][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="create-an-aks-cluster-with-azure-cni"></a>Skapa ett AKS-kluster med Azure CNI

> [!NOTE]
> Det här är den rekommenderade standardkonfigurationen

Skapa ett AKS-kluster med Azure CNI och podd-hanterad identitet aktiverad. Följande kommandon använder [az group create][az-group-create] för att skapa en resursgrupp med namnet *myResourceGroup* och [kommandot az aks create][az-aks-create] för att skapa ett AKS-kluster med namnet *myAKSCluster* i *resursgruppen myResourceGroup.*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
az aks create -g myResourceGroup -n myAKSCluster --enable-pod-identity --network-plugin azure
```

Använd [az aks get-credentials för][az-aks-get-credentials] att logga in på ditt AKS-kluster. Det här kommandot laddar även ned och konfigurerar `kubectl` klientcertifikatet på utvecklingsdatorn.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="update-an-existing-aks-cluster-with-azure-cni"></a>Uppdatera ett befintligt AKS-kluster med Azure CNI

Uppdatera ett befintligt AKS-kluster med Azure CNI att inkludera podd-hanterad identitet.

```azurecli-interactive
az aks update -g $MY_RESOURCE_GROUP -n $MY_CLUSTER --enable-pod-identity --network-plugin azure
```
## <a name="using-kubenet-network-plugin-with-azure-active-directory-pod-managed-identities"></a>Använda kubenet-nätverks-plugin Azure Active Directory med podd-hanterade identiteter 

> [!IMPORTANT]
> Att köra aad-pod-identity i ett kluster med Kubenet är inte en rekommenderad konfiguration på grund av säkerhetsaspekten. Följ åtgärdsstegen och konfigurera principer innan du aktiverar aad-pod-identity i ett kluster med Kubenet.

## <a name="mitigation"></a>Åtgärd

För att minska säkerhetsrisken på klusternivå kan du använda OpenPolicyAgent-antagningskontrollanten tillsammans med Gatekeeper som verifierar webhooken. Förutsatt att du redan har Gatekeeper installerat i klustret lägger du till ConstraintTemplate av typen K8sPSPCapabilities:

```
kubectl apply -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper-library/master/library/pod-security-policy/capabilities/template.yaml
```
Lägg till en mall för att begränsa hur poddar skapas med NET_RAW funktionen:

```
apiVersion: constraints.gatekeeper.sh/v1beta1
kind: K8sPSPCapabilities
metadata:
  name: prevent-net-raw
spec:
  match:
    kinds:
      - apiGroups: [""]
        kinds: ["Pod"]
    excludedNamespaces:
      - "kube-system"
  parameters:
    requiredDropCapabilities: ["NET_RAW"]
```

## <a name="create-an-aks-cluster-with-kubenet-network-plugin"></a>Skapa ett AKS-kluster med kubenet-plugin-programmet för nätverk

Skapa ett AKS-kluster med kubenet-nätverks-plugin-programmet och podd-hanterad identitet aktiverat.

```azurecli-interactive
az aks create -g $MY_RESOURCE_GROUP -n $MY_CLUSTER --enable-pod-identity --enable-pod-identity-with-kubenet
```

## <a name="update-an-existing-aks-cluster-with-kubenet-network-plugin"></a>Uppdatera ett befintligt AKS-kluster med kubenet-nätverks-plugin-programmet

Uppdatera ett befintligt AKS-kluster med Plugin-programmet för Kubnet-nätverk för att inkludera podd-hanterad identitet.

```azurecli-interactive
az aks update -g $MY_RESOURCE_GROUP -n $MY_CLUSTER --enable-pod-identity --enable-pod-identity-with-kubenet
```

## <a name="create-an-identity"></a>Skapa en identitet

Skapa en identitet med [az identity create][az-identity-create] och ange *IDENTITY_CLIENT_ID* och *IDENTITY_RESOURCE_ID* variabler.

```azurecli-interactive
az group create --name myIdentityResourceGroup --location eastus
export IDENTITY_RESOURCE_GROUP="myIdentityResourceGroup"
export IDENTITY_NAME="application-identity"
az identity create --resource-group ${IDENTITY_RESOURCE_GROUP} --name ${IDENTITY_NAME}
export IDENTITY_CLIENT_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME} --query clientId -otsv)"
export IDENTITY_RESOURCE_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME} --query id -otsv)"
```

## <a name="assign-permissions-for-the-managed-identity"></a>Tilldela behörigheter för den hanterade identiteten

Den *IDENTITY_CLIENT_ID* identiteten måste ha läsbehörighet i resursgruppen som innehåller VM-skalningsuppsättningen för ditt AKS-kluster.

```azurecli-interactive
NODE_GROUP=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NODES_RESOURCE_ID=$(az group show -n $NODE_GROUP -o tsv --query "id")
az role assignment create --role "Reader" --assignee "$IDENTITY_CLIENT_ID" --scope $NODES_RESOURCE_ID
```

## <a name="create-a-pod-identity"></a>Skapa en poddidentitet

Skapa en poddidentitet för klustret med hjälp av `az aks pod-identity add` .

> [!IMPORTANT]
> Du måste ha rätt behörigheter, till exempel `Owner` , för din prenumeration för att skapa identitets- och rollbindningen.

```azurecli-interactive
export POD_IDENTITY_NAME="my-pod-identity"
export POD_IDENTITY_NAMESPACE="my-app"
az aks pod-identity add --resource-group myResourceGroup --cluster-name myAKSCluster --namespace ${POD_IDENTITY_NAMESPACE}  --name ${POD_IDENTITY_NAME} --identity-resource-id ${IDENTITY_RESOURCE_ID}
```

> [!NOTE]
> När du aktiverar podd-hanterad identitet i ditt AKS-kluster läggs ett AzurePodIdentityException med namnet *aks-addon-exception* till i *namnområdet kube-system.* Med En AzurePodIdentityException kan poddar med vissa etiketter komma åt Slutpunkten för Azure Instance Metadata Service (IMDS) utan att fångas upp av NMI-servern (node-managed identity). Med *aks-addon-exception* kan AKS-tillägg från första part, till exempel AAD-poddad identitet, fungera utan att behöva konfigurera en AzurePodIdentityException manuellt. Du kan också lägga till, ta bort och uppdatera en AzurePodIdentityException med `az aks pod-identity exception add` hjälp av , , eller `az aks pod-identity exception delete` `az aks pod-identity exception update` `kubectl` .

## <a name="run-a-sample-application"></a>Köra ett exempelprogram

För att en podd ska kunna använda en AAD-podd-hanterad identitet behöver podden en *aadpodidbinding-etikett* med ett värde som matchar en väljare från *en AzureIdentityBinding*. Om du vill köra ett exempelprogram med en AAD-poddad identitet skapar `demo.yaml` du en fil med följande innehåll. Ersätt *POD_IDENTITY_NAME*, *IDENTITY_CLIENT_ID* *och IDENTITY_RESOURCE_GROUP* med värdena från föregående steg. Ersätt *SUBSCRIPTION_ID med* ditt prenumerations-ID.

> [!NOTE]
> I föregående steg skapade du *variablerna POD_IDENTITY_NAME*, *IDENTITY_CLIENT_ID* och *IDENTITY_RESOURCE_GROUP.* Du kan använda ett kommando som för `echo` att visa det värde som du har angett för variabler, till exempel `echo $IDENTITY_NAME` .

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

Observera att podddefinitionen har *en aadpodidbinding-etikett* med ett värde som matchar namnet på den poddidentitet som `az aks pod-identity add` du körde i föregående steg.

Distribuera `demo.yaml` till samma namnområde som din poddidentitet med hjälp av `kubectl apply` :

```azurecli-interactive
kubectl apply -f demo.yaml --namespace $POD_IDENTITY_NAMESPACE
```

Kontrollera att exempelprogrammet körs med `kubectl logs` .

```azurecli-interactive
kubectl logs demo --follow --namespace $POD_IDENTITY_NAMESPACE
```

Kontrollera att loggarna visar att en token har inhämtats och *att GET-åtgärden* lyckades.
 
```output
...
successfully doARMOperations vm count 0
successfully acquired a token using the MSI, msiEndpoint(http://169.254.169.254/metadata/identity/oauth2/token)
successfully acquired a token, userAssignedID MSI, msiEndpoint(http://169.254.169.254/metadata/identity/oauth2/token) clientID(xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx)
successfully made GET on instance metadata
...
```

## <a name="clean-up"></a>Rensa

Om du vill ta bort en AAD-podd-hanterad identitet från klustret tar du bort exempelprogrammet och poddidentiteten från klustret. Ta sedan bort identiteten.

```azurecli-interactive
kubectl delete pod demo --namespace $POD_IDENTITY_NAMESPACE
az aks pod-identity delete --name ${POD_IDENTITY_NAME} --namespace ${POD_IDENTITY_NAMESPACE} --resource-group myResourceGroup --cluster-name myAKSCluster
az identity delete -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME}
```

## <a name="next-steps"></a>Nästa steg

Mer information om hanterade identiteter finns i [Hanterade identiteter för Azure-resurser.][az-managed-identities]

<!-- LINKS - external -->
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-group-create]: /cli/azure/group#az_group_create
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-managed-identities]: ../active-directory/managed-identities-azure-resources/overview.md
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
