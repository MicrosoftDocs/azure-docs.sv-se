---
title: Använda CSI-drivrutiner (container Storage Interface) för Azure-diskar på Azure Kubernetes service (AKS)
description: Lär dig hur du använder CSI-drivrutinerna (container Storage Interface) för Azure-diskar i ett Azure Kubernetes service-kluster (AKS).
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: 2b4079b6d4eb39b65a7a60cd4d149c7748ab39ce
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102178889"
---
# <a name="use-the-azure-disk-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks-preview"></a>Använd driv rutinerna för Azure disk container Storage-gränssnittet (CSI) i Azure Kubernetes service (AKS) (för hands version)
Driv rutinen för Azure disk container Storage-gränssnittet (CSI) är en [CSI Specification](https://github.com/container-storage-interface/spec/blob/master/spec.md)-kompatibel driv rutin som används av Azure Kubernetes service (AKS) för att hantera livs cykeln för Azure-diskar.

CSI är en standard för att exponera godtyckliga block-och fil lagrings system för arbets belastningar i behållare på Kubernetes. Genom att anta och använda CSI kan AKS skriva, distribuera och iterera plugin-program för att exponera nya eller förbättra befintliga lagrings system i Kubernetes utan att behöva röra kärnan Kubernetes Code och vänta på dess versions cykler.

Om du vill skapa ett AKS-kluster med stöd för CSI-drivrutiner, se [Aktivera CSI-drivrutiner för Azure-diskar och Azure Files på AKS](csi-storage-drivers.md).

>[!NOTE]
> *Driv rutiner i ett träd* refererar till de aktuella lagrings driv rutiner som ingår i Kubernetes-koden och de nya CSI-drivrutinerna, som är plugin-program.

## <a name="use-csi-persistent-volumes-with-azure-disks"></a>Använd CSI-beständiga volymer med Azure-diskar

En [beständig volym](concepts-storage.md#persistent-volumes) (PV) representerar en lagrings enhet som tillhandahålls för användning med Kubernetes poddar. Ett PV kan användas av en eller flera poddar och kan vara dynamiskt eller statiskt allokerat. Den här artikeln visar hur du dynamiskt skapar PVs med Azure disks för användning av en enda Pod i ett AKS-kluster. För statisk etablering, se [skapa och använda en volym med Azure-diskar manuellt](azure-disk-volume.md).

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Mer information om Kubernetes-volymer finns i [lagrings alternativ för program i AKS][concepts-storage].

## <a name="dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes"></a>Skapa Azure disk-PVs dynamiskt med hjälp av de inbyggda lagrings klasserna

En lagrings klass används för att definiera hur en lagrings enhet dynamiskt skapas med en permanent volym. Mer information om Kubernetes lagrings klasser finns i [Kubernetes Storage-klasser][kubernetes-storage-classes]. När du använder driv rutiner för lagring CSI på AKS finns det två ytterligare inbyggda `StorageClasses` som använder Azure disk CSI Storage-drivrutiner. De ytterligare klasserna för CSI-lagring skapas med klustret tillsammans med standard lagrings klasser i trädet.

- `managed-csi`: Använder Azure Standard SSD lokalt redundant lagring (LRS) för att skapa en hanterad disk.
- `managed-csi-premium`: Använder Azure Premium-LRS för att skapa en hanterad disk.

Anspråks principen i båda lagrings klasserna säkerställer att den underliggande Azure-disken tas bort när respektive PV tas bort. Lagrings klasserna konfigurerar också PVs till att kunna expanderas. Du behöver bara redigera det permanenta volym anspråket (PVC) med den nya storleken.

Om du vill utnyttja dessa lagrings klasser skapar du en [PVC](concepts-storage.md#persistent-volume-claims) och varje Pod som refererar till och använder dem. En PVC används för att automatiskt etablera lagring baserat på en lagrings klass. En PVC kan använda en av de i förväg skapade lagrings klasserna eller en användardefinierad lagrings klass för att skapa en Azure-hanterad disk för önskad SKU och storlek. När du skapar en POD-definition, anges PVC: n för att begära det önskade lagrings utrymmet.

Skapa ett exempel på pod och respektive PVC med kommandot [kubectl Apply][kubectl-apply] :

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/pvc-azuredisk-csi.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

persistentvolumeclaim/pvc-azuredisk created
pod/nginx-azuredisk created
```

När pod är i körnings läge skapar du en ny fil med namnet `test.txt` .

```bash
$ kubectl exec nginx-azuredisk -- touch /mnt/azuredisk/test.txt
```

Nu kan du kontrol lera att disken är korrekt monterad genom att köra följande kommando och verifiera att du ser `test.txt` filen i utdata:

```console
$ kubectl exec nginx-azuredisk -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="create-a-custom-storage-class"></a>Skapa en anpassad lagrings klass

Standard lagrings klasserna passar de vanligaste scenarierna, men inte alla. I vissa fall kanske du vill ha en egen lagrings klass anpassad med dina egna parametrar. Vi har till exempel ett scenario där du kanske vill ändra `volumeBindingMode` klassen.

Standard lagrings klasserna använder en `volumeBindingMode: Immediate` klass som garanterar att det sker omedelbart när PVC: n skapas. I de fall där Node-poolerna är topologi begränsade, till exempel med hjälp av tillgänglighets zoner, skulle PVs vara bundna eller etablerade utan kännedom om Pod schema krav (i det här fallet vara i en speciell zon).

För att åtgärda det här scenariot kan du använda `volumeBindingMode: WaitForFirstConsumer` , vilket förskjuter bindningen och etableringen av ett PV tills en pod som använder PVC: n skapas. På så sätt uppfyller PV och tillhandahålls i tillgänglighets zonen (eller någon annan topologi) som anges av Pod schemaläggnings begränsningar.

Skapa en fil med namnet `sc-azuredisk-csi-waitforfirstconsumer.yaml` och klistra in följande manifest.
Lagrings klassen är samma som vår `managed-csi` lagrings klass men med en annan `volumeBindingMode` klass.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azuredisk-csi-waitforfirstconsumer
provisioner: disk.csi.azure.com
parameters:
  skuname: StandardSSD_LRS 
allowVolumeExpansion: true
reclaimPolicy: Delete
volumeBindingMode: WaitForFirstConsumer
```

Skapa lagrings klassen med kommandot [kubectl Apply][kubectl-apply] och ange din `sc-azuredisk-csi-waitforfirstconsumer.yaml` fil:

```console
$ kubectl apply -f sc-azuredisk-csi-waitforfirstconsumer.yaml

storageclass.storage.k8s.io/azuredisk-csi-waitforfirstconsumer created
```

## <a name="volume-snapshots"></a>Ögonblicks bilder av volymer

Driv rutinen för Azure disk CSI stöder skapande [av ögonblicks bilder av permanenta volymer](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html). Som en del av den här funktionen kan driv rutinen utföra antingen *fullständiga* eller [ *stegvisa* ögonblicks bilder](../virtual-machines/disks-incremental-snapshots.md) beroende på vilket värde som anges i `incremental` parametern (som standard är det sant).

Mer information om alla parametrar finns i [klass parametrar för volym ögonblicks bilder](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/docs/driver-parameters.md#volumesnapshotclass).

### <a name="create-a-volume-snapshot"></a>Skapa en ögonblicks bild av en volym

För ett exempel på den här funktionen skapar du en [volym ögonblicks bild klass](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml) med kommandot [kubectl Apply][kubectl-apply] :

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml

volumesnapshotclass.snapshot.storage.k8s.io/csi-azuredisk-vsc created
```

Nu ska vi skapa en [ögonblicks bild](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/azuredisk-volume-snapshot.yaml) av en volym från den PVC som [vi skapade dynamiskt i början av den här kursen](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes) `pvc-azuredisk` .


```bash
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/azuredisk-volume-snapshot.yaml

volumesnapshot.snapshot.storage.k8s.io/azuredisk-volume-snapshot created
```

Kontrol lera att ögonblicks bilden skapades korrekt:

```bash
$ kubectl describe volumesnapshot azuredisk-volume-snapshot

Name:         azuredisk-volume-snapshot
Namespace:    default
Labels:       <none>
Annotations:  API Version:  snapshot.storage.k8s.io/v1beta1
Kind:         VolumeSnapshot
Metadata:
  Creation Timestamp:  2020-08-27T05:27:58Z
  Finalizers:
    snapshot.storage.kubernetes.io/volumesnapshot-as-source-protection
    snapshot.storage.kubernetes.io/volumesnapshot-bound-protection
  Generation:        1
  Resource Version:  714582
  Self Link:         /apis/snapshot.storage.k8s.io/v1beta1/namespaces/default/volumesnapshots/azuredisk-volume-snapshot
  UID:               dd953ab5-6c24-42d4-ad4a-f33180e0ef87
Spec:
  Source:
    Persistent Volume Claim Name:  pvc-azuredisk
  Volume Snapshot Class Name:      csi-azuredisk-vsc
Status:
  Bound Volume Snapshot Content Name:  snapcontent-dd953ab5-6c24-42d4-ad4a-f33180e0ef87
  Creation Time:                       2020-08-31T05:27:59Z
  Ready To Use:                        true
  Restore Size:                        10Gi
Events:                                <none>
```

### <a name="create-a-new-pvc-based-on-a-volume-snapshot"></a>Skapa en ny PVC som baseras på en ögonblicks bild av en volym

Du kan skapa en ny PVC baserat på en ögonblicks bild av en volym. Använd ögonblicks bilden som skapades i föregående steg och skapa en [ny PVC](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml) och en [ny Pod](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml) för att använda den.

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml

persistentvolumeclaim/pvc-azuredisk-snapshot-restored created
pod/nginx-restored created
```

Slutligen ska vi kontrol lera att det är samma PVC som skapats innan genom att kontrol lera innehållet.

```console
$ kubectl exec nginx-restored -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

Som förväntat kan vi fortfarande se vår tidigare skapade `test.txt` fil.

## <a name="clone-volumes"></a>Klona volymer

En klonad volym definieras som en dubblett av en befintlig Kubernetes-volym. Mer information om kloning av volymer i Kubernetes finns i den konceptuella dokumentationen för [volym kloning](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#volume-cloning).

CSI-drivrutinen för Azure-diskar stöder volym kloning. För att demonstrera, skapar du en [klonad volym](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml) av den [tidigare skapade](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes) `azuredisk-pvc` och [en ny Pod för att använda den](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml).


```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/pvc-azuredisk-cloning.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml

persistentvolumeclaim/pvc-azuredisk-cloning created
pod/nginx-restored-cloning created
```

Nu kan vi kontrol lera innehållet i den klonade volymen genom att köra följande kommando och bekräfta att vi fortfarande ser vår `test.txt` skapade fil.

```console
$ kubectl exec nginx-restored-cloning -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="resize-a-persistent-volume"></a>Ändra storlek på en beständig volym

Du kan i stället begära en större volym för en PVC. Redigera PVC-objektet och ange en större storlek. Den här ändringen utlöser expansionen av den underliggande volymen som backar upp NUVÄRDEt.

> [!NOTE]
> En ny PV skapas aldrig för att uppfylla kravet. I stället ändras storleken på en befintlig volym.

I AKS tillåter den inbyggda `managed-csi` lagrings klassen redan expansion, så Använd den [PVC som skapades tidigare med denna lagrings klass](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes). PVC: n begärde en 10-GI-beständig volym. Vi kan bekräfta detta genom att köra:

```console 
$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk

Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc        9.8G   42M  9.8G   1% /mnt/azuredisk
```

> [!IMPORTANT]
> För närvarande stöder Azure disk CSI-driv rutinen bara storleks ändring av PVC: er utan poddar (och volymen har inte monterats till en speciell nod).

Därför tar vi bort Pod vi skapade tidigare:

```console
$ kubectl delete -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

pod "nginx-azuredisk" deleted
```

Nu ska vi expandera PVC: n genom att öka `spec.resources.requests.storage` fältet:

```console
$ kubectl patch pvc pvc-azuredisk --type merge --patch '{"spec": {"resources": {"requests": {"storage": "15Gi"}}}}'

persistentvolumeclaim/pvc-azuredisk patched
```

Nu ska vi kontrol lera att volymen är större:

```console
$ kubectl get pv

NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                                     STORAGECLASS   REASON   AGE
pvc-391ea1a6-0191-4022-b915-c8dc4216174a   15Gi       RWO            Delete           Bound    default/pvc-azuredisk                     managed-csi             2d2h
(...)
```

> [!NOTE]
> PVC: n visar inte den nya storleken förrän den har en pod som är kopplad till den igen.

Nu ska vi skapa en ny pod:

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

pod/nginx-azuredisk created
```

Och slutligen bekräftar du storleken på PVC: n och i pod:
```console
$ kubectl get pvc pvc-azuredisk
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
pvc-azuredisk   Bound    pvc-391ea1a6-0191-4022-b915-c8dc4216174a   15Gi       RWO            managed-csi    2d2h

$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk
Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc         15G   46M   15G   1% /mnt/azuredisk
```

## <a name="shared-disk"></a>Delad disk

[Azure delade diskar](../virtual-machines/disks-shared.md) är en funktion för Azure Managed disks som gör att du kan koppla en Azure-disk till agent-noder samtidigt. Genom att ansluta en hanterad disk till flera agent-noder kan du till exempel distribuera nya eller migrera befintliga klustrade program till Azure.

> [!IMPORTANT] 
> För närvarande stöds endast RAW block Device ( `volumeMode: Block` ) av Azure disk CSI-drivrutinen. Program bör hantera samordning och kontroll av skrivningar, läsningar, lås, cacheminnen, monteringar och staket på den delade disken, som visas som en RAW block-enhet.

Nu ska vi skapa en fil `shared-disk.yaml` med namnet genom att kopiera följande kommando som innehåller den delade disk lagrings klassen och PVC:

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: managed-csi-shared
provisioner: disk.csi.azure.com
parameters:
  skuname: Premium_LRS  # Currently shared disk is only available with premium SSD
  maxShares: "2"
  cachingMode: None  # ReadOnly cache is not available for premium SSD with maxShares>1
reclaimPolicy: Delete
---
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: pvc-azuredisk-shared
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 256Gi  # minimum size of shared disk is 256GB (P15)
  volumeMode: Block
  storageClassName: managed-csi-shared
```

Skapa lagrings klassen med kommandot [kubectl Apply][kubectl-apply] och ange din `shared-disk.yaml` fil:

```console
$ kubectl apply -f shared-disk.yaml

storageclass.storage.k8s.io/managed-csi-shared created
persistentvolumeclaim/pvc-azuredisk-shared created
``` 

Nu ska vi skapa en fil `deployment-shared.yml` med namnet genom att kopiera följande kommando:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx
  name: deployment-azuredisk
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
      name: deployment-azuredisk
    spec:
      containers:
        - name: deployment-azuredisk
          image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
          volumeDevices:
            - name: azuredisk
              devicePath: /dev/sdx
      volumes:
        - name: azuredisk
          persistentVolumeClaim:
            claimName: pvc-azuredisk-shared
```

Skapa distributionen med kommandot [kubectl Apply][kubectl-apply] och ange din `deployment-shared.yml` fil:

```console
$ kubectl apply -f deployment-shared.yml

deployment/deployment-azuredisk created
```

Till sist kontrollerar vi att enheten är blockerad i pod:

```console
# kubectl exec -it deployment-sharedisk-7454978bc6-xh7jp sh
/ # dd if=/dev/zero of=/dev/sdx bs=1024k count=100
100+0 records in
100+0 records out/s
```

## <a name="windows-containers"></a>Windows-containrar

Driv rutinen för Azure disk CSI stöder också Windows-noder och behållare. Om du vill använda Windows-behållare följer du [själv studie kursen för Windows-behållare](windows-container-cli.md) för att lägga till en Windows-Node-pool.

När du har en Windows Node-pool kan du nu använda de inbyggda lagrings klasserna som `managed-csi` . Du kan distribuera ett exempel på en [Windows-baserad tillstånds känslig uppsättning](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/windows/statefulset.yaml) som sparar tidsstämplar i filen `data.txt` genom att distribuera följande kommando med kommandot [kubectl Apply][kubectl-apply] :

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/windows/statefulset.yaml

statefulset.apps/busybox-azuredisk created
```

Nu kan du validera innehållet på volymen genom att köra:

```console
$ kubectl exec -it busybox-azuredisk-0 -- cat c:\\mnt\\azuredisk\\data.txt # on Linux/MacOS Bash
$ kubectl exec -it busybox-azuredisk-0 -- cat c:\mnt\azuredisk\data.txt # on Windows Powershell/CMD

2020-08-27 08:13:41Z
2020-08-27 08:13:42Z
2020-08-27 08:13:44Z
(...)
```

## <a name="next-steps"></a>Nästa steg

- Information om hur du använder CSI-drivrutiner för Azure Files finns i [använda Azure Files med CSI-drivrutiner](azure-files-csi.md).
- Mer information om metod tips för lagring finns i [metod tips för lagring och säkerhets kopiering i Azure Kubernetes-tjänsten][operator-best-practices-storage].


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
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register