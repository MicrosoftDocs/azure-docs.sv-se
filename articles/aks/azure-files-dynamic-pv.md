---
title: Skapa en Azure Files resurs
titleSuffix: Azure Kubernetes Service
description: Lär dig hur du dynamiskt skapar en beständig volym med Azure Files för användning med flera samtidiga poddar i Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 07/01/2020
ms.openlocfilehash: f301a01e479d03647bebf7cb042564a258e9250e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776131"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-files-in-azure-kubernetes-service-aks"></a>Skapa och använda en permanent volym dynamiskt med Azure Files i AKS (Azure Kubernetes Service)

En beständig volym representerar en lagringsplats som har etablerats för användning med Kubernetes-poddar. En beständig volym kan användas av en eller flera poddar och kan etableras dynamiskt eller statiskt. Om flera poddar behöver samtidig åtkomst till samma lagringsvolym kan du använda Azure Files för att ansluta med [SMB-protokollet (Server Message Block).][smb-overview] Den här artikeln visar hur du dynamiskt skapar en Azure Files-resurs för användning av flera poddar i ett Azure Kubernetes Service-kluster (AKS).

Mer information om Kubernetes-volymer finns i [Lagringsalternativ för program i AKS][concepts-storage].

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster kan du gå till AKS-snabbstarten med Hjälp av [Azure CLI][aks-quickstart-cli] eller [använda Azure Portal][aks-quickstart-portal].

Du måste också ha installerat och konfigurerat Azure CLI version 2.0.59 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

## <a name="create-a-storage-class"></a>Skapa en lagringsklass

En lagringsklass används för att definiera hur en Azure-filresurs skapas. Ett lagringskonto skapas automatiskt i [nodresursgruppen för][node-resource-group] användning med lagringsklassen som ska innehålla Azure-filresurser. Välj följande Azure [Storage-redundans][storage-skus] för *skuName:*

* *Standard_LRS* – standard lokalt redundant lagring (LRS)
* *Standard_GRS* – geo-redundant standardlagring (GRS)
* *Standard_ZRS* – zonredundant standardlagring (ZRS)
* *Standard_RAGRS* – standard geo-redundant lagring med läsbehörighet (RA-GRS)
* *Premium_LRS* – lokalt redundant premiumlagring (LRS)
* *Premium_ZRS* – ZRS (Premium Zone Redundant Storage)

> [!NOTE]
> Azure Files stöd för Premium Storage i AKS-kluster som kör Kubernetes 1.13 eller senare är den lägsta premiumfilresursen 100 GB

Mer information om Kubernetes-lagringsklasser för Azure Files finns i [Kubernetes-lagringsklasser][kubernetes-storage-classes].

Skapa en fil med namnet `azure-file-sc.yaml` och kopiera följande exempelmanifest. Mer information om *mountOptions finns* i [avsnittet Monteringsalternativ.][mount-options]

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: my-azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=0
  - gid=0
  - mfsymlinks
  - cache=strict
  - actimeo=30
parameters:
  skuName: Standard_LRS
```

Skapa lagringsklassen med [kommandot kubectl apply:][kubectl-apply]

```console
kubectl apply -f azure-file-sc.yaml
```

## <a name="create-a-persistent-volume-claim"></a>Skapa ett beständigt volymanspråk

Ett beständigt volymanspråk (PV) använder lagringsklassobjektet för att dynamiskt etablera en Azure-filresurs. Följande YAML kan användas för att skapa ett beständigt volymanspråk *på 5 GB* i storlek *med ReadWriteMany-åtkomst.* Mer information om åtkomstlägen finns i dokumentationen för [beständig Kubernetes-volym.][access-modes]

Skapa nu en fil med `azure-file-pvc.yaml` namnet och kopiera följande YAML. Kontrollera att *storageClassName matchar lagringsklassen* som skapades i det sista steget:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-azurefile
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: my-azurefile
  resources:
    requests:
      storage: 5Gi
```

> [!NOTE]
> Om du *använder Premium_LRS-SKU:n* för din lagringsklass måste det minsta värdet för lagring vara *100Gi*. 

Skapa det beständiga volymanspråk med [kommandot kubectl apply:][kubectl-apply]

```console
kubectl apply -f azure-file-pvc.yaml
```

När det är klart skapas filresursen. En Kubernetes-hemlighet skapas också som innehåller anslutningsinformation och autentiseringsuppgifter. Du kan använda [kommandot kubectl get][kubectl-get] för att visa status för PV:

```console
$ kubectl get pvc my-azurefile

NAME           STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
my-azurefile   Bound     pvc-8436e62e-a0d9-11e5-8521-5a8664dc0477   5Gi        RWX            my-azurefile      5m
```

## <a name="use-the-persistent-volume"></a>Använda den beständiga volymen

Följande YAML skapar en podd som använder det beständiga volymanspråket *my-azurefile* för att montera Azure-filresursen på *sökvägen /mnt/azure.* För Windows Server-containrar anger du *en mountPath* med hjälp av Windows sökvägskonvention, till *exempel "D:".*

Skapa en fil med `azure-pvc-files.yaml` namnet och kopiera följande YAML. Kontrollera att *claimName matchar* PV som skapades i det senaste steget.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/azure"
      name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: my-azurefile
```

Skapa podden med [kommandot kubectl apply.][kubectl-apply]

```console
kubectl apply -f azure-pvc-files.yaml
```

Nu har du en podd som körs med Azure Files resurs monterad i *katalogen /mnt/azure.* Den här konfigurationen kan visas när du inspekterar podden via `kubectl describe pod mypod` . Följande komprimerade exempelutdata visar volymen som monterats i containern:

```
Containers:
  mypod:
    Container ID:   docker://053bc9c0df72232d755aa040bfba8b533fa696b123876108dec400e364d2523e
    Image:          mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    Image ID:       docker-pullable://nginx@sha256:d85914d547a6c92faa39ce7058bd7529baacab7e0cd4255442b04577c4d1f424
    State:          Running
      Started:      Fri, 01 Mar 2019 23:56:16 +0000
    Ready:          True
    Mounts:
      /mnt/azure from volume (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-8rv4z (ro)
[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  my-azurefile
    ReadOnly:   false
[...]
```

## <a name="mount-options"></a>Monteringsalternativ

Standardvärdet för *fileMode* och *dirMode* *är 0777* för Kubernetes version 1.13.0 och senare. Om du skapar den beständiga volymen dynamiskt med en lagringsklass kan monteringsalternativ anges för lagringsklassobjektet. I följande exempel anges *0777*:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: my-azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=0
  - gid=0
  - mfsymlinks
  - cache=strict
  - actimeo=30
parameters:
  skuName: Standard_LRS
```

## <a name="next-steps"></a>Nästa steg

Associerade metodtips finns i [Metodtips för lagring och säkerhetskopieringar i AKS.][operator-best-practices-storage]

Parametrar för lagringsklass finns i [Dynamisk etablering.](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/docs/driver-parameters.md#dynamic-provision)

Läs mer om Beständiga Kubernetes-volymer med Azure Files.

> [!div class="nextstepaction"]
> [Kubernetes-plugin-program för Azure Files][kubernetes-files]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/#azure-file
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[pv-static]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#static
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-group-list]: /cli/azure/group#az_group_list
[az-resource-show]: /cli/azure/aks#az_aks_show
[az-storage-account-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
[mount-options]: #mount-options
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az_aks_show
[storage-skus]: ../storage/common/storage-redundancy.md
[kubernetes-rbac]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
