---
title: Skapa volym för Azure disks dynamiskt
titleSuffix: Azure Kubernetes Service
description: Lär dig att dynamiskt skapa en permanent volym med Azure-diskar i Azure Kubernetes service (AKS)
services: container-service
ms.topic: article
ms.date: 09/21/2020
ms.openlocfilehash: ad51bfdf8c494e763921de880926b839cdb7be62
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96021647"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Skapa och använda en permanent volym med Azure-diskar i Azure Kubernetes service (AKS) dynamiskt

En beständig volym representerar en lagrings enhet som har etablerats för användning med Kubernetes poddar. En permanent volym kan användas av en eller flera poddar och kan vara dynamiskt eller statiskt etablerad. Den här artikeln visar hur du dynamiskt skapar permanenta volymer med Azure disks för användning av en enda Pod i ett Azure Kubernetes service-kluster (AKS).

> [!NOTE]
> Det går bara att montera en Azure-disk med *åtkomst läges* typen *ReadWriteOnce*, vilket gör den tillgänglig för en nod i AKS. Om du behöver dela en permanent volym över flera noder använder du [Azure Files][azure-files-pvc].

Mer information om Kubernetes-volymer finns i [lagrings alternativ för program i AKS][concepts-storage].

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster kan du läsa snabb starten för AKS [med hjälp av Azure CLI][aks-quickstart-cli] eller [Azure Portal][aks-quickstart-portal].

Du måste också ha Azure CLI-versionen 2.0.59 eller senare installerad och konfigurerad. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

## <a name="built-in-storage-classes"></a>Inbyggda lagrings klasser

En lagrings klass används för att definiera hur en lagrings enhet dynamiskt skapas med en permanent volym. Mer information om Kubernetes lagrings klasser finns i [Kubernetes Storage-klasser][kubernetes-storage-classes].

Varje AKS-kluster innehåller fyra i förväg skapade lagrings klasser, två av dem som är konfigurerade för att fungera med Azure-diskar:

* *Standard* lagrings klassen tillhandahåller en standard SSD Azure-disk.
    * Standard lagring backas upp av standard SSD och levererar kostnads effektiv lagring samtidigt som du ändå levererar tillförlitlig prestanda. 
* Den *hanterade Premium Storage-* klassen tillhandahåller en Premium Azure-disk.
    * Premiumdiskar backas upp av SSD-baserade diskar med höga prestanda och låg latens. Passar perfekt för virtuella datorer som kör produktionsarbetsbelastningar. Om AKS-noderna i klustret använder Premium Storage väljer du den *hanterade-Premium-* klassen.
    
Om du använder en av standard lagrings klasserna kan du inte uppdatera volym storleken när lagrings klassen har skapats. Om du vill kunna uppdatera volym storleken när en lagrings klass har skapats lägger du till den `allowVolumeExpansion: true` i en av standard lagrings klasserna, eller så kan du skapa en egen anpassad lagrings klass. Observera att det inte finns stöd för att minska storleken på en PVC (för att förhindra data förlust). Du kan redigera en befintlig lagrings klass med hjälp av `kubectl edit sc` kommandot. 

Om du till exempel vill använda en disk med storlek 4 TiB måste du skapa en lagrings klass som definierar `cachingmode: None` eftersom [diskcachelagring inte stöds för disk 4 TIB och större](../virtual-machines/premium-storage-performance.md#disk-caching).

Mer information om lagrings klasser och hur du skapar en egen lagrings klass finns i [lagrings alternativ för program i AKS][storage-class-concepts].

Använd kommandot [kubectl get SC][kubectl-get] för att se de i förväg skapade lagrings klasserna. I följande exempel visas de för hands skapa lagrings klasser som är tillgängliga i ett AKS-kluster:

```console
$ kubectl get sc

NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

> [!NOTE]
> Beständiga volym anspråk anges i GiB, men Azure Managed disks debiteras per SKU för en viss storlek. Dessa SKU: er sträcker sig från 32GiB för S4-eller P4-diskar till 32TiB för S80-eller P80-diskar (för hands version). Genomflödet och IOPS-prestanda för en Premium-hanterad disk beror på både SKU: n och instans storleken för noderna i AKS-klustret. Mer information finns i [pris-och prestanda för Managed disks][managed-disk-pricing-performance].

## <a name="create-a-persistent-volume-claim"></a>Skapa ett beständigt volym anspråk

Ett permanent volym anspråk (PVC) används för att automatiskt etablera lagring baserat på en lagrings klass. I det här fallet kan en PVC använda en av de i förväg skapade lagrings klasserna för att skapa en standard-eller Premium Azure-hanterad disk.

Skapa en fil med namnet `azure-premium.yaml` och kopiera i följande manifest. Anspråket begär en disk med namnet `azure-managed-disk` *5 GB* som är i storlek med *ReadWriteOnce* -åtkomst. Lagrings klassen för *Managed Premium* har angetts som lagrings klass.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium
  resources:
    requests:
      storage: 5Gi
```

> [!TIP]
> Använd `storageClassName: default` i stället för *hanterad Premium* om du vill skapa en disk som använder standard lagring.

Skapa ett beständigt volym anspråk med kommandot [kubectl Apply][kubectl-apply] och ange din *Azure-Premium. yaml* -fil:

```console
$ kubectl apply -f azure-premium.yaml

persistentvolumeclaim/azure-managed-disk created
```

## <a name="use-the-persistent-volume"></a>Använd beständig volym

När beständiga volym anspråk har skapats och disken har etablerats kan du skapa en POD med åtkomst till disken. Följande manifest skapar en grundläggande NGINX-Pod som använder det beständiga volym anspråket med namnet *Azure-hanterad disk* för att montera Azure-disken på sökvägen `/mnt/azure` . För Windows Server-behållare anger du en *mountPath* med hjälp av Windows Sök vägs konvention, till exempel *":"*.

Skapa en fil med namnet `azure-pvc-disk.yaml` och kopiera i följande manifest.

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
        claimName: azure-managed-disk
```

Skapa Pod med kommandot [kubectl Apply][kubectl-apply] , som du ser i följande exempel:

```console
$ kubectl apply -f azure-pvc-disk.yaml

pod/mypod created
```

Nu har du en igång-Pod med Azure-disken monterad i `/mnt/azure` katalogen. Den här konfigurationen kan ses när du inspekterar din POD via `kubectl describe pod mypod` , som du ser i följande komprimerade exempel:

```console
$ kubectl describe pod mypod

[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  azure-managed-disk
    ReadOnly:   false
  default-token-smm2n:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-smm2n
    Optional:    false
[...]
Events:
  Type    Reason                 Age   From                               Message
  ----    ------                 ----  ----                               -------
  Normal  Scheduled              2m    default-scheduler                  Successfully assigned mypod to aks-nodepool1-79590246-0
  Normal  SuccessfulMountVolume  2m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "default-token-smm2n"
  Normal  SuccessfulMountVolume  1m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "pvc-faf0f176-8b8d-11e8-923b-deb28c58d242"
[...]
```

## <a name="use-ultra-disks"></a>Använd Ultra disks
Om du vill använda Ultra disk ser du [Använd Ultra disks på Azure Kubernetes service (AKS)](use-ultra-disks.md).

## <a name="back-up-a-persistent-volume"></a>Säkerhetskopiera en beständig volym

Ta en ögonblicks bild av den hanterade disken för volymen om du vill säkerhetskopiera data i den beständiga volymen. Du kan sedan använda den här ögonblicks bilden för att skapa en återställd disk och ansluta till poddar som ett sätt att återställa data.

Börja med att hämta volym namnet med `kubectl get pvc` kommandot, till exempel för den PVC som heter *Azure-Managed-disk*:

```console
$ kubectl get pvc azure-managed-disk

NAME                 STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
azure-managed-disk   Bound     pvc-faf0f176-8b8d-11e8-923b-deb28c58d242   5Gi        RWO            managed-premium   3m
```

Det här volym namnet utgör det underliggande namnet för Azure-disken. Fråga efter disk-ID med [AZ disk List][az-disk-list] och ange PVC-volymens namn, som du ser i följande exempel:

```azurecli-interactive
$ az disk list --query '[].id | [?contains(@,`pvc-faf0f176-8b8d-11e8-923b-deb28c58d242`)]' -o tsv

/subscriptions/<guid>/resourceGroups/MC_MYRESOURCEGROUP_MYAKSCLUSTER_EASTUS/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

Använd disk-ID för att skapa en ögonblicks bild disk med [AZ Snapshot Create][az-snapshot-create]. I följande exempel skapas en ögonblicks bild med namnet *pvcSnapshot* i samma resurs grupp som AKS-klustret (*MC_myResourceGroup_myAKSCluster_eastus*). Du kan stöta på behörighets problem om du skapar ögonblicks bilder och återställer diskar i resurs grupper som AKS-klustret inte har åtkomst till.

```azurecli-interactive
$ az snapshot create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name pvcSnapshot \
    --source /subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

Beroende på mängden data på disken kan det ta några minuter att skapa ögonblicks bilden.

## <a name="restore-and-use-a-snapshot"></a>Återställa och använda en ögonblicks bild

Du återställer disken och använder den med en Kubernetes-Pod genom att använda ögonblicks bilden som källa när du skapar en disk med [AZ disk Create][az-disk-create]. Den här åtgärden bevarar den ursprungliga resursen om du behöver komma åt den ursprungliga data ögonblicks bilden. I följande exempel skapas en disk med namnet *pvcRestored* från ögonblicks bilden med namnet *pvcSnapshot*:

```azurecli-interactive
az disk create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --source pvcSnapshot
```

Om du vill använda den återställda disken med en POD anger du ID för disken i manifestet. Hämta disk-ID: t med kommandot [AZ disk show][az-disk-show] . I följande exempel hämtas disk-ID: t för *pvcRestored* som skapats i föregående steg:

```azurecli-interactive
az disk show --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --query id -o tsv
```

Skapa ett Pod-manifest med namnet `azure-restored.yaml` och ange disk-URI: n som hämtades i föregående steg. I följande exempel skapas en grundläggande NGINX-webbserver med den återställda disken monterad som en volym på */mnt/Azure*:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypodrestored
spec:
  containers:
  - name: mypodrestored
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
      azureDisk:
        kind: Managed
        diskName: pvcRestored
        diskURI: /subscriptions/<guid>/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/pvcRestored
```

Skapa Pod med kommandot [kubectl Apply][kubectl-apply] , som du ser i följande exempel:

```console
$ kubectl apply -f azure-restored.yaml

pod/mypodrestored created
```

Du kan använda `kubectl describe pod mypodrestored` för att visa information om pod, till exempel följande komprimerade exempel som visar volym informationen:

```console
$ kubectl describe pod mypodrestored

[...]
Volumes:
  volume:
    Type:         AzureDisk (an Azure Data Disk mount on the host and bind mount to the pod)
    DiskName:     pvcRestored
    DiskURI:      /subscriptions/19da35d3-9a1a-4f3b-9b9c-3c56ef409565/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/pvcRestored
    Kind:         Managed
    FSType:       ext4
    CachingMode:  ReadWrite
    ReadOnly:     false
[...]
```

## <a name="next-steps"></a>Nästa steg

För associerade metod tips, se [metod tips för lagring och säkerhets kopiering i AKS][operator-best-practices-storage].

Läs mer om Kubernetes-beständiga volymer med Azure-diskar.

> [!div class="nextstepaction"]
> [Kubernetes-plugin-program för Azure-diskar][azure-disk-volume]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[azure-disk-volume]: azure-disk-volume.md
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/disks-types.md
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-snapshot-create]: /cli/azure/snapshot#az-snapshot-create
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-disk-show]: /cli/azure/disk#az-disk-show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[storage-class-concepts]: concepts-storage.md#storage-classes
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
