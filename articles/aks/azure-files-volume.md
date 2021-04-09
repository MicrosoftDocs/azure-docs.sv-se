---
title: Skapa Azure Files resurs manuellt
titleSuffix: Azure Kubernetes Service
description: Lär dig hur du manuellt skapar en volym med Azure Files för användning med flera samtidiga poddar i Azure Kubernetes service (AKS)
services: container-service
ms.topic: article
ms.date: 03/01/2019
ms.openlocfilehash: 4e009c5de2e24c1b0bd94fb4c11b0c52a3bc378d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102609081"
---
# <a name="manually-create-and-use-a-volume-with-azure-files-share-in-azure-kubernetes-service-aks"></a>Skapa och använda en volym med Azure Files resurs i Azure Kubernetes service (AKS) manuellt

Containerbaserade program behöver ofta åtkomst till och bevara data i en extern data volym. Om flera poddar behöver samtidig åtkomst till samma lagrings volym kan du använda Azure Files för att ansluta med [SMB-protokollet (Server Message Block)][smb-overview]. Den här artikeln visar hur du skapar en Azure Files-resurs manuellt och kopplar den till en POD i AKS.

Mer information om Kubernetes-volymer finns i [lagrings alternativ för program i AKS][concepts-storage].

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster kan du läsa snabb starten för AKS [med hjälp av Azure CLI][aks-quickstart-cli] eller [Azure Portal][aks-quickstart-portal].

Du måste också ha Azure CLI-versionen 2.0.59 eller senare installerad och konfigurerad. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

## <a name="create-an-azure-file-share"></a>Skapa en Azure-filresurs

Innan du kan använda Azure Files som Kubernetes-volym måste du skapa ett Azure Storage-konto och fil resursen. Följande kommandon skapar en resurs grupp med namnet *myAKSShare*, ett lagrings konto och en fil resurs med namnet *aksshare*:

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

Anteckna namnet på lagrings kontot och nyckeln som visas i slutet av utdata från skriptet. Dessa värden behövs när du skapar Kubernetes-volymen i något av följande steg.

## <a name="create-a-kubernetes-secret"></a>Skapa en Kubernetes-hemlighet

Kubernetes måste ha autentiseringsuppgifter för att få åtkomst till fil resursen som skapades i föregående steg. Autentiseringsuppgifterna lagras i en Kubernetes- [hemlighet][kubernetes-secret]som refereras till när du skapar en Kubernetes-pod.

Använd `kubectl create secret` kommandot för att skapa hemligheten. I följande exempel skapas en delad med namnet *Azure-Secret* och fyller i *azurestorageaccountname* och *azurestorageaccountkey* från föregående steg. Om du vill använda ett befintligt Azure Storage-konto anger du kontots namn och nyckel.

```console
kubectl create secret generic azure-secret --from-literal=azurestorageaccountname=$AKS_PERS_STORAGE_ACCOUNT_NAME --from-literal=azurestorageaccountkey=$STORAGE_KEY
```

## <a name="mount-file-share-as-an-inline-volume"></a>Montera fil resurs som en infogad volym
> Obs! från och med 1.18.15, 1.19.7, 1.20.2, 1.21.0, det hemliga namn området i en intern `azureFile` volym kan bara anges som `default` namn område, för att ange ett annat hemligt namn område, Använd nedanstående beständiga volym exempel i stället.

Om du vill montera Azure Files resursen i din POD konfigurerar du volymen i behållar specifikationen. Skapa en ny fil `azure-files-pod.yaml` med namnet med följande innehåll. Om du har ändrat namnet på fil resursen eller det hemliga namnet uppdaterar du *resurs* namn och *secretName*. Om du vill kan du uppdatera `mountPath` , vilket är sökvägen till fil resursen som är monterad i pod. För Windows Server-behållare anger du en *mountPath* med hjälp av Windows Sök vägs konvention, till exempel *":"*.

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

Använd `kubectl` kommandot för att skapa pod.

```console
kubectl apply -f azure-files-pod.yaml
```

Nu har du en aktiv Pod med en Azure Files resurs som är monterad på */mnt/Azure*. Du kan använda `kubectl describe pod mypod` för att kontrol lera att resursen har monterats. I följande komprimerade exempel utdata visas den volym som är monterad i behållaren:

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

## <a name="mount-file-share-as-an-persistent-volume"></a>Montera fil resurs som en beständig volym
 - Monteringsalternativ

Standardvärdet för *fileMode* och *dirMode* är *0777* för Kubernetes version 1,15 och senare. I följande exempel anges *0755* på *PersistentVolume* -objektet:

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

Om du vill uppdatera monterings alternativen skapar du en *azurefile-Mount-Options-PV. yaml-* fil med en *PersistentVolume*. Exempel:

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

Skapa en *azurefile-Mount-Options-PVC. yaml-* fil med en *PersistentVolumeClaim* som använder *PersistentVolume*. Exempel:

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

Använd `kubectl` kommandona för att skapa *PersistentVolume* och *PersistentVolumeClaim*.

```console
kubectl apply -f azurefile-mount-options-pv.yaml
kubectl apply -f azurefile-mount-options-pvc.yaml
```

Verifiera att din *PersistentVolumeClaim* har skapats och bundits till *PersistentVolume*.

```console
$ kubectl get pvc azurefile

NAME        STATUS   VOLUME      CAPACITY   ACCESS MODES   STORAGECLASS   AGE
azurefile   Bound    azurefile   5Gi        RWX            azurefile      5s
```

Uppdatera din container specifikation för att referera till din *PersistentVolumeClaim* och uppdatera din POD. Exempel:

```yaml
...
  volumes:
  - name: azure
    persistentVolumeClaim:
      claimName: azurefile
```

## <a name="next-steps"></a>Nästa steg

För associerade metod tips, se [metod tips för lagring och säkerhets kopiering i AKS][operator-best-practices-storage].

Mer information om AKS-kluster samverkar med Azure Files finns i [Kubernetes-plugin-programmet för Azure Files][kubernetes-files].

För lagrings klass parametrar, se [statisk etablering (ta med din egen fil resurs)](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/docs/driver-parameters.md#static-provisionbring-your-own-file-share).

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-storage-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-key-list]: /cli/azure/storage/account/keys#az-storage-account-keys-list
[az-storage-share-create]: /cli/azure/storage/share#az-storage-share-create
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
