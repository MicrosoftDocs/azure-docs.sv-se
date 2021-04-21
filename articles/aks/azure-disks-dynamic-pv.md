---
title: Skapa En Azure-diskvolym dynamiskt
titleSuffix: Azure Kubernetes Service
description: Lär dig hur du dynamiskt skapar en beständig volym med Azure-diskar Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 09/21/2020
ms.openlocfilehash: 066a52024e91610882889bb7fbe6b20efa262b71
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776149"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Skapa och använd en permanent volym dynamiskt med Azure-diskar i Azure Kubernetes Service (AKS)

En beständig volym representerar en lagringsplats som har etablerats för användning med Kubernetes-poddar. En beständig volym kan användas av en eller flera poddar och kan etableras dynamiskt eller statiskt. Den här artikeln visar hur du dynamiskt skapar beständiga volymer med Azure-diskar för användning av en enskild podd i ett Azure Kubernetes Service-kluster (AKS).

> [!NOTE]
> En Azure-disk kan  bara monteras med åtkomstlägestypen *ReadWriteOnce*, vilket gör den tillgänglig för en nod i AKS. Om du behöver dela en beständig volym över flera noder använder du [Azure Files][azure-files-pvc].

Mer information om Kubernetes-volymer finns i [Lagringsalternativ för program i AKS][concepts-storage].

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster kan du gå till AKS-snabbstarten med Hjälp av [Azure CLI][aks-quickstart-cli] eller [använda Azure Portal][aks-quickstart-portal].

Du måste också ha installerat och konfigurerat Azure CLI version 2.0.59 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

## <a name="built-in-storage-classes"></a>Inbyggda lagringsklasser

En lagringsklass används för att definiera hur en lagringsenhet skapas dynamiskt med en beständig volym. Mer information om Kubernetes-lagringsklasser finns i [Kubernetes-lagringsklasser][kubernetes-storage-classes].

Varje AKS-kluster innehåller fyra förskapade lagringsklasser, två av dem konfigurerade för att fungera med Azure-diskar:

* *Standardlagringsklassen* tillhandahåller en Standard SSD Azure-disk.
    * Standard Storage backas upp av Standard-STANDARD-STANDARD OCH levererar kostnadseffektiv lagring samtidigt som du levererar tillförlitliga prestanda. 
* *Managed-Premium Storage-klassen* tillhandahåller en Premium Azure-disk.
    * Premiumdiskar backas upp av SSD-baserade diskar med höga prestanda och låg latens. Passar perfekt för virtuella datorer som kör produktionsarbetsbelastningar. Om AKS-noderna i klustret använder Premium Storage väljer du *klassen managed-premium.*
    
Om du använder en av standardlagringsklasserna kan du inte uppdatera volymstorleken när lagringsklassen har skapats. Om du vill kunna uppdatera volymstorleken när en lagringsklass har skapats lägger du till raden i någon av standardlagringsklasserna, eller så kan du skapa en `allowVolumeExpansion: true` egen anpassad lagringsklass. Observera att det inte stöds för att minska storleken på en PV (för att förhindra dataförlust). Du kan redigera en befintlig lagringsklass med hjälp av `kubectl edit sc` kommandot . 

Om du till exempel vill använda en disk med storleken 4 TiB måste du skapa en lagringsklass som definierar eftersom diskcachelagring inte stöds för `cachingmode: None` [diskar 4 TiB](../virtual-machines/premium-storage-performance.md#disk-caching)och större .

Mer information om lagringsklasser och hur du skapar en egen lagringsklass finns i [Lagringsalternativ för program i AKS.][storage-class-concepts]

Använd [kommandot kubectl get sc][kubectl-get] för att se de förskapade lagringsklasserna. I följande exempel visas de lagringsklasser som är tillgängliga i ett AKS-kluster i förväg:

```console
$ kubectl get sc

NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

> [!NOTE]
> Beständiga volymanspråk anges i GiB, men Azure-hanterade diskar debiteras av SKU för en viss storlek. Dessa SKU:er sträcker sig från 32GiB för S4- eller P4-diskar till 32TiB för S80- eller P80-diskar (i förhandsversion). Dataflödet och IOPS-prestandan för en Premium-hanterad disk beror på både SKU:n och instansstorleken för noderna i AKS-klustret. Mer information finns i [Prissättning och prestanda för Managed Disks][managed-disk-pricing-performance].

## <a name="create-a-persistent-volume-claim"></a>Skapa ett beständigt volymanspråk

Ett beständigt volymanspråk (PV) används för att automatiskt etablera lagring baserat på en lagringsklass. I det här fallet kan en PV använda en av de förskapade lagringsklasserna för att skapa en hanterad Standard- eller Premium Azure-disk.

Skapa en fil med `azure-premium.yaml` namnet och kopiera följande manifest. Anspråket begär en disk med `azure-managed-disk` namnet som är *5 GB* stor med *ReadWriteOnce-åtkomst.* *Lagringsklassen managed-premium* anges som lagringsklass.

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
> Om du vill skapa en disk som använder standardlagring använder `storageClassName: default` du i stället för *managed-premium*.

Skapa det beständiga volymanspråk med [kommandot kubectl apply][kubectl-apply] och ange *filen azure-premium.yaml:*

```console
$ kubectl apply -f azure-premium.yaml

persistentvolumeclaim/azure-managed-disk created
```

## <a name="use-the-persistent-volume"></a>Använda den beständiga volymen

När det beständiga volymanspråket har skapats och disken har etablerats kan en podd skapas med åtkomst till disken. Följande manifest skapar en grundläggande NGINX-podd som använder det beständiga volymanspråk som heter *azure-managed-disk* för att montera Azure-disken på sökvägen `/mnt/azure` . För Windows Server-containrar anger du *en mountPath* med hjälp av Windows sökvägskonvention, till *exempel "D:".*

Skapa en fil med `azure-pvc-disk.yaml` namnet och kopiera följande manifest.

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

Skapa podden med [kommandot kubectl apply,][kubectl-apply] som du ser i följande exempel:

```console
$ kubectl apply -f azure-pvc-disk.yaml

pod/mypod created
```

Nu har du en podd som körs med din Azure-disk monterad i `/mnt/azure` katalogen . Den här konfigurationen kan visas när du inspekterar podden via `kubectl describe pod mypod` , som du ser i följande komprimerade exempel:

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

## <a name="use-ultra-disks"></a>Använda Ultradiskar
Information om hur du använder [ultradiskar finns i Använda Ultradiskar på Azure Kubernetes Service (AKS).](use-ultra-disks.md)

## <a name="back-up-a-persistent-volume"></a>Backa upp en beständig volym

Om du vill säkerhetskopiera data i den beständiga volymen tar du en ögonblicksbild av den hanterade disken för volymen. Du kan sedan använda den här ögonblicksbilden för att skapa en återställd disk och ansluta till poddar som ett sätt att återställa data.

Först hämtar du volymnamnet med kommandot `kubectl get pvc` , till exempel för PV med namnet *azure-managed-disk*:

```console
$ kubectl get pvc azure-managed-disk

NAME                 STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
azure-managed-disk   Bound     pvc-faf0f176-8b8d-11e8-923b-deb28c58d242   5Gi        RWO            managed-premium   3m
```

Det här volymnamnet utgör det underliggande Azure-disknamnet. Fråga efter disk-ID:t [med az disk list][az-disk-list] och ange ditt PV-volymnamn, som du ser i följande exempel:

```azurecli-interactive
$ az disk list --query '[].id | [?contains(@,`pvc-faf0f176-8b8d-11e8-923b-deb28c58d242`)]' -o tsv

/subscriptions/<guid>/resourceGroups/MC_MYRESOURCEGROUP_MYAKSCLUSTER_EASTUS/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

Använd disk-ID:t för att skapa en ögonblicksbildsdisk [med az snapshot create][az-snapshot-create]. I följande exempel skapas en ögonblicksbild med *namnet pvSnapshot* i samma resursgrupp som AKS-klustret (*MC_myResourceGroup_myAKSCluster_eastus*). Du kan stöta på behörighetsproblem om du skapar ögonblicksbilder och återställer diskar i resursgrupper som AKS-klustret inte har åtkomst till.

```azurecli-interactive
$ az snapshot create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name pvcSnapshot \
    --source /subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

Beroende på mängden data på disken kan det ta några minuter att skapa ögonblicksbilden.

## <a name="restore-and-use-a-snapshot"></a>Återställa och använda en ögonblicksbild

Om du vill återställa disken och använda den med en Kubernetes-podd använder du ögonblicksbilden som källa när du skapar en disk med [az disk create][az-disk-create]. Den här åtgärden bevarar den ursprungliga resursen om du sedan behöver komma åt den ursprungliga ögonblicksbilden av data. I följande exempel skapas en disk med namnet *pvRestored från* ögonblicksbilden med namnet *pvSnapshot*:

```azurecli-interactive
az disk create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --source pvcSnapshot
```

Om du vill använda den återställda disken med en podd anger du DISKENs ID i manifestet. Hämta disk-ID:t med [kommandot az disk show.][az-disk-show] I följande exempel hämtar disk-ID:t *för pvRestored* som skapades i föregående steg:

```azurecli-interactive
az disk show --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --query id -o tsv
```

Skapa ett poddmanifest `azure-restored.yaml` med namnet och ange den disk-URI som du fick i föregående steg. I följande exempel skapas en grundläggande NGINX-webbserver med den återställda disken monterad som en volym *på /mnt/azure*:

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

Skapa podden med [kommandot kubectl apply,][kubectl-apply] som du ser i följande exempel:

```console
$ kubectl apply -f azure-restored.yaml

pod/mypodrestored created
```

Du kan använda `kubectl describe pod mypodrestored` för att visa information om podden, till exempel följande komprimerade exempel som visar volyminformationen:

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

Tillhörande metodtips finns i [Metodtips för lagring och säkerhetskopieringar i AKS.][operator-best-practices-storage]

Läs mer om Beständiga Kubernetes-volymer med Hjälp av Azure-diskar.

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
[az-disk-list]: /cli/azure/disk#az_disk_list
[az-snapshot-create]: /cli/azure/snapshot#az_snapshot_create
[az-disk-create]: /cli/azure/disk#az_disk_create
[az-disk-show]: /cli/azure/disk#az_disk_show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[storage-class-concepts]: concepts-storage.md#storage-classes
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
