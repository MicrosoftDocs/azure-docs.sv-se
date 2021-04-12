---
title: Skapa Azure Files resurs dynamiskt
titleSuffix: Azure Kubernetes Service
description: Lär dig att dynamiskt skapa en permanent volym med Azure Files för användning med flera samtidiga poddar i Azure Kubernetes service (AKS)
services: container-service
ms.topic: article
ms.date: 07/01/2020
ms.openlocfilehash: 2ad2affee34348e8c2fc7b734c8b49d0aec8db40
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96744917"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-files-in-azure-kubernetes-service-aks"></a>Skapa och använda en beständig volym dynamiskt med Azure Files i Azure Kubernetes service (AKS)

En beständig volym representerar en lagrings enhet som har etablerats för användning med Kubernetes poddar. En permanent volym kan användas av en eller flera poddar och kan vara dynamiskt eller statiskt etablerad. Om flera poddar behöver samtidig åtkomst till samma lagrings volym kan du använda Azure Files för att ansluta med [SMB-protokollet (Server Message Block)][smb-overview]. Den här artikeln visar hur du skapar en Azure Files-resurs dynamiskt för användning av flera poddar i ett Azure Kubernetes service-kluster (AKS).

Mer information om Kubernetes-volymer finns i [lagrings alternativ för program i AKS][concepts-storage].

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster kan du läsa snabb starten för AKS [med hjälp av Azure CLI][aks-quickstart-cli] eller [Azure Portal][aks-quickstart-portal].

Du måste också ha Azure CLI-versionen 2.0.59 eller senare installerad och konfigurerad. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

## <a name="create-a-storage-class"></a>Skapa en lagrings klass

En lagrings klass används för att definiera hur en Azure-filresurs skapas. Ett lagrings konto skapas automatiskt i [resurs gruppen nod][node-resource-group] för användning med lagrings klassen för att lagra Azure-filresurserna. Välj följande [Azure Storage-redundans][storage-skus] för *skuName*:

* *Standard_LRS* -standard lokalt redundant lagring (LRS)
* *Standard_GRS* -standard Geo-redundant lagring (GRS)
* *Standard_ZRS* -standard zon redundant lagring (ZRS)
* *Standard_RAGRS* -standard Geo-redundant lagring med Läs behörighet (RA-GRS)
* *Premium_LRS* – Premium lokalt redundant lagring (LRS)
* *Premium_ZRS* -Premium Zone-redundant lagring (ZRS)

> [!NOTE]
> Azure Files support Premium Storage i AKS-kluster som kör Kubernetes 1,13 eller högre, är den minsta Premium-filresursen 100 GB

Mer information om Kubernetes lagrings klasser för Azure Files finns i [Kubernetes Storage-klasser][kubernetes-storage-classes].

Skapa en fil med namnet `azure-file-sc.yaml` och kopiera i följande exempel manifest. Mer information om *mountOptions* finns i avsnittet [monterings alternativ][mount-options] .

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

Skapa lagrings klassen med kommandot [kubectl Apply][kubectl-apply] :

```console
kubectl apply -f azure-file-sc.yaml
```

## <a name="create-a-persistent-volume-claim"></a>Skapa ett beständigt volym anspråk

Ett permanent volym anspråk (PVC) använder lagrings klass objekt för att dynamiskt etablera en Azure-filresurs. Följande YAML kan användas för att skapa ett beständigt volym anspråk *5 GB* i storlek med *ReadWriteMany* -åtkomst. Mer information om åtkomst lägen finns i dokumentationen för [Kubernetes-beständig volym][access-modes] .

Skapa nu en fil med namnet `azure-file-pvc.yaml` och kopiera den i följande yaml. Kontrol lera att *storageClassName* matchar lagrings klassen som skapades i det senaste steget:

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
> Om du använder *Premium_LRS* SKU för lagrings klassen måste det lägsta värdet för *lagring* vara *100Gi*.

Skapa ett beständigt volym anspråk med kommandot [kubectl Apply][kubectl-apply] :

```console
kubectl apply -f azure-file-pvc.yaml
```

När den är klar skapas fil resursen. En Kubernetes-hemlighet skapas också som innehåller anslutnings information och autentiseringsuppgifter. Du kan använda kommandot [kubectl get][kubectl-get] för att visa status för PVC: n:

```console
$ kubectl get pvc my-azurefile

NAME           STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
my-azurefile   Bound     pvc-8436e62e-a0d9-11e5-8521-5a8664dc0477   5Gi        RWX            my-azurefile      5m
```

## <a name="use-the-persistent-volume"></a>Använd beständig volym

Följande YAML skapar en pod som använder beständiga volym anspråk *My-azurefile* för att montera Azure-filresursen på */mnt/Azure* -sökvägen. För Windows Server-behållare anger du en *mountPath* med hjälp av Windows Sök vägs konvention, till exempel *":"*.

Skapa en fil med namnet `azure-pvc-files.yaml` och kopiera i följande yaml. Kontrol lera att *claimName* matchar den PVC som skapades i det sista steget.

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

Skapa Pod med kommandot [kubectl Apply][kubectl-apply] .

```console
kubectl apply -f azure-pvc-files.yaml
```

Nu har du en aktiv Pod med din Azure Files resurs som är monterad i katalogen */mnt/Azure* . Den här konfigurationen kan ses när du inspekterar din POD via `kubectl describe pod mypod` . I följande komprimerade exempel utdata visas den volym som är monterad i behållaren:

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

Standardvärdet för *fileMode* och *dirMode* är *0777* för Kubernetes version 1.13.0 och senare. Om du dynamiskt skapar den permanenta volymen med en lagrings klass kan monterings alternativ anges för objektet lagrings klass. I följande exempel anges *0777*:

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

För associerade metod tips, se [metod tips för lagring och säkerhets kopiering i AKS][operator-best-practices-storage].

För lagrings klass parametrar, se [dynamisk etablering](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/docs/driver-parameters.md#dynamic-provision).

Läs mer om Kubernetes-beständiga volymer med Azure Files.

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
[az-group-create]: /cli/azure/group#az-group-create
[az-group-list]: /cli/azure/group#az-group-list
[az-resource-show]: /cli/azure/aks#az-aks-show
[az-storage-account-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-key-list]: /cli/azure/storage/account/keys#az-storage-account-keys-list
[az-storage-share-create]: /cli/azure/storage/share#az-storage-share-create
[mount-options]: #mount-options
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[storage-skus]: ../storage/common/storage-redundancy.md
[kubernetes-rbac]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
