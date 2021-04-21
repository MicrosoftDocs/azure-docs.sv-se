---
title: Skapa en Azure Files resurs
titleSuffix: Azure Kubernetes Service
description: Lär dig hur du skapar en volym manuellt med Azure Files för användning med flera samtidiga poddar i Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/01/2019
ms.openlocfilehash: 7f3c8ae63e908f440740277084293a011b80b9d7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776096"
---
# <a name="manually-create-and-use-a-volume-with-azure-files-share-in-azure-kubernetes-service-aks"></a>Skapa och använd en volym manuellt med Azure Files resurs i Azure Kubernetes Service (AKS)

Containerbaserade program behöver ofta åtkomst till och bevara data i en extern datavolym. Om flera poddar behöver samtidig åtkomst till samma lagringsvolym kan du använda Azure Files för att ansluta med [SMB-protokollet (Server Message Block).][smb-overview] Den här artikeln visar hur du manuellt skapar en Azure Files resurs och kopplar den till en podd i AKS.

Mer information om Kubernetes-volymer finns i [Lagringsalternativ för program i AKS][concepts-storage].

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster kan du gå till AKS-snabbstarten med Hjälp av [Azure CLI][aks-quickstart-cli] eller [använda Azure Portal][aks-quickstart-portal].

Du måste också ha installerat och konfigurerat Azure CLI version 2.0.59 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

## <a name="create-an-azure-file-share"></a>Skapa en Azure-filresurs

Innan du kan använda Azure Files som en Kubernetes-volym måste du skapa ett Azure Storage-konto och filresursen. Följande kommandon skapar en resursgrupp med namnet *myAKSShare, ett lagringskonto* och en Files-resurs med namnet *aksshare:*

```azurecli-interactive
# Change these four parameters as needed for your own environment
AKS_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
AKS_PERS_RESOURCE_GROUP=myAKSShare
AKS_PERS_LOCATION=eastus
AKS_PERS_SHARE_NAME=aksshare

# Create a resource group
az group create --name $AKS_PERS_RESOURCE_GROUP --location $AKS_PERS_LOCATION

# Create a storage account
az storage account create -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -l $AKS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=$(az storage account show-connection-string -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -o tsv)

# Create the file share
az storage share create -n $AKS_PERS_SHARE_NAME --connection-string $AZURE_STORAGE_CONNECTION_STRING

# Get storage account key
STORAGE_KEY=$(az storage account keys list --resource-group $AKS_PERS_RESOURCE_GROUP --account-name $AKS_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" -o tsv)

# Echo storage account name and key
echo Storage account name: $AKS_PERS_STORAGE_ACCOUNT_NAME
echo Storage account key: $STORAGE_KEY
```

Anteckna lagringskontots namn och nyckel som visas i slutet av skriptets utdata. Dessa värden behövs när du skapar Kubernetes-volymen i något av följande steg.

## <a name="create-a-kubernetes-secret"></a>Skapa en Kubernetes-hemlighet

Kubernetes behöver autentiseringsuppgifter för att få åtkomst till filresursen som skapades i föregående steg. Dessa autentiseringsuppgifter lagras i en [Kubernetes-hemlighet][kubernetes-secret], som du refererar till när du skapar en Kubernetes-podd.

Använd kommandot `kubectl create secret` för att skapa hemligheten. I följande exempel skapas en delad med namnet *azure-secret* och *fyller i azurestorageaccountname* *och azurestorageaccountkey* från föregående steg. Om du vill använda ett befintligt Azure Storage-konto anger du kontonamnet och nyckeln.

```console
kubectl create secret generic azure-secret --from-literal=azurestorageaccountname=$AKS_PERS_STORAGE_ACCOUNT_NAME --from-literal=azurestorageaccountkey=$STORAGE_KEY
```

## <a name="mount-file-share-as-an-inline-volume"></a>Montera filresurs som en infogade volym
> Obs! Från och med 1.18.15, 1.19.7, 1.20.2, 1.21.0 kan hemligt namnområde på den infogade volymen bara anges som namnområde, för att ange ett annat hemligt namnområde använder du nedanstående beständiga volymexempel i `azureFile` `default` stället.

Om du vill Azure Files till din podd konfigurerar du volymen i containerspecifikationen. Skapa en ny fil med `azure-files-pod.yaml` namnet med följande innehåll. Om du har ändrat namnet på Files-resursen eller det hemliga namnet uppdaterar du *shareName* och *secretName*. Om du vill uppdaterar du `mountPath` , som är sökvägen där Files-resursen är monterad i podden. För Windows Server-containrar anger du *en mountPath* med hjälp av Windows sökvägskonvention, till *exempel "D:".*

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
  volumes:
  - name: azure
    azureFile:
      secretName: azure-secret
      shareName: aksshare
      readOnly: false
```

Använd kommandot `kubectl` för att skapa podden.

```console
kubectl apply -f azure-files-pod.yaml
```

Nu har du en podd som körs med Azure Files resurs monterad på */mnt/azure*. Du kan använda `kubectl describe pod mypod` för att kontrollera att resursen har monterats. Följande komprimerade exempelutdata visar volymen som monterats i containern:

```
Containers:
  mypod:
    Container ID:   docker://86d244cfc7c4822401e88f55fd75217d213aa9c3c6a3df169e76e8e25ed28166
    Image:          mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    Image ID:       docker-pullable://nginx@sha256:9ad0746d8f2ea6df3a17ba89eca40b48c47066dfab55a75e08e2b70fc80d929e
    State:          Running
      Started:      Sat, 02 Mar 2019 00:05:47 +0000
    Ready:          True
    Mounts:
      /mnt/azure from azure (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-z5sd7 (ro)
[...]
Volumes:
  azure:
    Type:        AzureFile (an Azure File Service mount on the host and bind mount to the pod)
    SecretName:  azure-secret
    ShareName:   aksshare
    ReadOnly:    false
  default-token-z5sd7:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-z5sd7
[...]
```

## <a name="mount-file-share-as-an-persistent-volume"></a>Montera filresurs som en beständig volym
 - Monteringsalternativ

Standardvärdet för *fileMode och* *dirMode* är *0777* för Kubernetes version 1.15 och senare. I följande exempel anges *0755* för *PersistentVolume-objektet:*

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: azurefile
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  azureFile:
    secretName: azure-secret
    secretNamespace: default
    shareName: aksshare
    readOnly: false
  mountOptions:
  - dir_mode=0755
  - file_mode=0755
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
```

Om du vill uppdatera monteringsalternativen skapar du en *azurefile-mount-options-pv.yaml-fil* med *en PersistentVolume*. Exempel:

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: azurefile
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  azureFile:
    secretName: azure-secret
    shareName: aksshare
    readOnly: false
  mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
```

Skapa en *azurefile-mount-options-pv.yaml-fil* med *en PersistentVolumeClaim* som använder *PersistentVolume*. Exempel:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azurefile
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 5Gi
```

Använd `kubectl` kommandona för att skapa *PersistentVolume* och *PersistentVolumeClaim.*

```console
kubectl apply -f azurefile-mount-options-pv.yaml
kubectl apply -f azurefile-mount-options-pvc.yaml
```

Kontrollera att *PersistentVolumeClaim har* skapats och är bundet till *PersistentVolume.*

```console
$ kubectl get pvc azurefile

NAME        STATUS   VOLUME      CAPACITY   ACCESS MODES   STORAGECLASS   AGE
azurefile   Bound    azurefile   5Gi        RWX            azurefile      5s
```

Uppdatera containerspecifikationen så att den refererar *till PersistentVolumeClaim* och uppdaterar podden. Exempel:

```yaml
...
  volumes:
  - name: azure
    persistentVolumeClaim:
      claimName: azurefile
```

## <a name="next-steps"></a>Nästa steg

Tillhörande metodtips finns i [Metodtips för lagring och säkerhetskopieringar i AKS.][operator-best-practices-storage]

Mer information om hur AKS-kluster interagerar med Azure Files finns i [Kubernetes-plugin-programmet för Azure Files][kubernetes-files].

För parametrar för lagringsklass, [se Statisk etablering(bring your own file share)](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/docs/driver-parameters.md#static-provisionbring-your-own-file-share).

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
