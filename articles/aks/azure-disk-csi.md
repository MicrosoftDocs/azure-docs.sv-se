---
title: Använda Container Storage Interface (CSI) för Azure Disks på Azure Kubernetes Service (AKS)
description: Lär dig hur du använder Container Storage Interface (CSI)-drivrutiner för Azure-diskar i ett Azure Kubernetes Service-kluster (AKS).
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: c3421b767f465a4a705bdeb4882fd261c5cf914f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776239"
---
# <a name="use-the-azure-disk-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks-preview"></a>Använda CSI-drivrutiner (Azure disk Container Storage Interface) i Azure Kubernetes Service (förhandsversion)
CSI-drivrutinen (Azure Disk Container Storage Interface) är en [CSI-specifikation](https://github.com/container-storage-interface/spec/blob/master/spec.md)som följer standard och som används av Azure Kubernetes Service (AKS) för att hantera livscykeln för Azure-diskar.

CSI är en standard för att exponera godtyckliga block- och fillagringssystem för containeriserade arbetsbelastningar i Kubernetes. Genom att implementera och använda CSI kan AKS skriva, distribuera och iterera plugin-program för att exponera nya eller förbättra befintliga lagringssystem i Kubernetes utan att behöva röra kärnkoden för Kubernetes och vänta på lanseringscyklerna.

Information om hur du skapar ett AKS-kluster med CSI-drivrutinsstöd finns i Aktivera [CSI-drivrutiner för Azure-diskar Azure Files på AKS](csi-storage-drivers.md).

>[!NOTE]
> *Träddrivrutiner refererar* till de aktuella lagringsdrivrutinerna som ingår i Kubernetes-kärnkoden jämfört med de nya CSI-drivrutinerna, som är plugin-program.

## <a name="use-csi-persistent-volumes-with-azure-disks"></a>Använda beständiga CSI-volymer med Azure-diskar

En [beständig](concepts-storage.md#persistent-volumes) volym (PV) representerar en lagringsplats som etableras för användning med Kubernetes-poddar. Ett PV kan användas av en eller flera poddar och kan etableras dynamiskt eller statiskt. Den här artikeln visar hur du dynamiskt skapar PV:er med Azure-diskar för användning av en enskild podd i ett AKS-kluster. För statisk etablering, se [Skapa och använda en volym manuellt med Azure-diskar](azure-disk-volume.md).

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Mer information om Kubernetes-volymer finns i [Lagringsalternativ för program i AKS][concepts-storage].

## <a name="dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes"></a>Skapa Azure-disk-PV:er dynamiskt med hjälp av de inbyggda lagringsklasserna

En lagringsklass används för att definiera hur en lagringsenhet skapas dynamiskt med en beständig volym. Mer information om Kubernetes-lagringsklasser finns i [Kubernetes-lagringsklasser.][kubernetes-storage-classes] När du använder CSI-lagringsdrivrutiner på AKS finns det två ytterligare inbyggda som använder `StorageClasses` CSI-lagringsdrivrutiner för Azure-diskar. De ytterligare CSI-lagringsklasserna skapas med klustret tillsammans med standardlagringsklasserna i trädet.

- `managed-csi`: Använder Azure Standard SSD lokalt redundant lagring (LRS) för att skapa en hanterad disk.
- `managed-csi-premium`: Använder Azure Premium LRS för att skapa en hanterad disk.

Principen för att frigöra i båda lagringsklasserna säkerställer att den underliggande Azure-disken tas bort när respektive PV tas bort. Lagringsklasserna konfigurerar också de PV:er som kan utökas. Du behöver bara redigera beständigt volymanspråk (PV) med den nya storleken.

Om du vill utnyttja dessa lagringsklasser skapar du [en PV](concepts-storage.md#persistent-volume-claims) och respektive podd som refererar till och använder dem. En PV används för att automatiskt etablera lagring baserat på en lagringsklass. En PV kan använda en av de förskapade lagringsklasserna eller en användardefinierad lagringsklass för att skapa en Azure-hanterad disk för önskad SKU och storlek. När du skapar en podddefinition anges PV för att begära önskad lagring.

Skapa en exempelpodd och respektive PV med [kommandot kubectl apply:][kubectl-apply]

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/pvc-azuredisk-csi.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

persistentvolumeclaim/pvc-azuredisk created
pod/nginx-azuredisk created
```

När podden är i körningstillstånd skapar du en ny fil med namnet `test.txt` .

```bash
$ kubectl exec nginx-azuredisk -- touch /mnt/azuredisk/test.txt
```

Du kan nu kontrollera att disken är korrekt monterad genom att köra följande kommando och kontrollera att du ser `test.txt` filen i utdata:

```console
$ kubectl exec nginx-azuredisk -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="create-a-custom-storage-class"></a>Skapa en anpassad lagringsklass

Standardlagringsklasserna passar de vanligaste scenarierna, men inte alla. I vissa fall kanske du vill ha en egen lagringsklass anpassad med dina egna parametrar. Vi har till exempel ett scenario där du kanske vill ändra `volumeBindingMode` klassen.

Du kan använda en `volumeBindingMode: Immediate` klass som garanterar att den inträffar omedelbart när PV har skapats. I fall där dina nodpooler är topologibegränsade, till exempel med hjälp av tillgänglighetszoner, skulle PV:er bindas eller etableras utan kunskap om poddens schemaläggningskrav (i det här fallet i en specifik zon).

För att hantera det här scenariot kan du använda , vilket fördrödar bindningen och etableringen av ett PV tills en podd som `volumeBindingMode: WaitForFirstConsumer` använder PV skapas. På så sätt kommer PV att överensstämma och etableras i tillgänglighetszonen (eller någon annan topologi) som anges av poddens schemaläggningsbegränsningar. Standardlagringsklasserna använder `volumeBindingMode: WaitForFirstConsumer` klassen .

Skapa en fil med `sc-azuredisk-csi-waitforfirstconsumer.yaml` namnet och klistra in följande manifest.
Lagringsklassen är samma som vår `managed-csi` lagringsklass men med en annan `volumeBindingMode` klass.

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

Skapa lagringsklassen med [kommandot kubectl apply][kubectl-apply] och ange `sc-azuredisk-csi-waitforfirstconsumer.yaml` filen:

```console
$ kubectl apply -f sc-azuredisk-csi-waitforfirstconsumer.yaml

storageclass.storage.k8s.io/azuredisk-csi-waitforfirstconsumer created
```

## <a name="volume-snapshots"></a>Ögonblicksbilder av volymer

CSI-drivrutinen för Azure-diskar har stöd [för att skapa ögonblicksbilder av beständiga volymer.](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html) Som en del av den här  funktionen [  ](../virtual-machines/disks-incremental-snapshots.md) kan drivrutinen utföra fullständiga eller inkrementella ögonblicksbilder beroende på värdet som anges i `incremental` parametern (som standard är det sant).

Mer information om alla parametrar finns i klassparametrar [för volymögonblicksbilder.](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/docs/driver-parameters.md#volumesnapshotclass)

### <a name="create-a-volume-snapshot"></a>Skapa en ögonblicksbild av volymen

Ett exempel på den här funktionen är att skapa en [volymögonblicksbildsklass](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml) med [kommandot kubectl apply:][kubectl-apply]

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml

volumesnapshotclass.snapshot.storage.k8s.io/csi-azuredisk-vsc created
```

Nu ska vi skapa en ögonblicksbild [av volymen](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/azuredisk-volume-snapshot.yaml) från PV som vi skapade dynamiskt i början av den [här självstudien](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes), `pvc-azuredisk` .


```bash
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/azuredisk-volume-snapshot.yaml

volumesnapshot.snapshot.storage.k8s.io/azuredisk-volume-snapshot created
```

Kontrollera att ögonblicksbilden har skapats korrekt:

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

### <a name="create-a-new-pvc-based-on-a-volume-snapshot"></a>Skapa en ny PV baserat på en ögonblicksbild av en volym

Du kan skapa en ny PV baserat på en ögonblicksbild av volymen. Använd ögonblicksbilden som skapades i föregående steg och skapa en ny [PV och](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml) en ny [podd för](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml) att använda den.

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml

persistentvolumeclaim/pvc-azuredisk-snapshot-restored created
pod/nginx-restored created
```

Slutligen kontrollerar vi att det är samma PV som skapades innan genom att kontrollera innehållet.

```console
$ kubectl exec nginx-restored -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

Som förväntat kan vi fortfarande se vår tidigare skapade `test.txt` fil.

## <a name="clone-volumes"></a>Klona volymer

En klonad volym definieras som en dubblett av en befintlig Kubernetes-volym. Mer information om kloning av volymer i Kubernetes finns i konceptuell dokumentation för [volymkloning.](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#volume-cloning)

CSI-drivrutinen för Azure-diskar stöder volymkloning. För att demonstrera skapar du en [klonad volym](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml) av den [tidigare skapade och](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes) en ny `azuredisk-pvc` [podd för att använda den.](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml)


```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/pvc-azuredisk-cloning.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml

persistentvolumeclaim/pvc-azuredisk-cloning created
pod/nginx-restored-cloning created
```

Nu kan vi kontrollera innehållet i den klonade volymen genom att köra följande kommando och bekräfta att vi fortfarande ser vår `test.txt` skapade fil.

```console
$ kubectl exec nginx-restored-cloning -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="resize-a-persistent-volume"></a>Ändra storlek på en beständig volym

Du kan i stället begära en större volym för en PV. Redigera PV-objektet och ange en större storlek. Den här ändringen utlöser expansionen av den underliggande volymen som backar upp PV.

> [!NOTE]
> En ny PV skapas aldrig för att uppfylla anspråket. I stället ändras storleken på en befintlig volym.

I AKS tillåter den inbyggda lagringsklassen redan expansion, så använd PV som skapades `managed-csi` tidigare med den här [lagringsklassen](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes). PV begärde en beständiga 10 Gi-volym. Vi kan bekräfta detta genom att köra:

```console 
$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk

Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc        9.8G   42M  9.8G   1% /mnt/azuredisk
```

> [!IMPORTANT]
> För närvarande stöder CSI-drivrutinen för Azure-diskar endast storleksändring av PVC:er utan associerade poddar (och volymen är inte monterad på en specifik nod).

Därför tar vi bort den podd som vi skapade tidigare:

```console
$ kubectl delete -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

pod "nginx-azuredisk" deleted
```

Nu ska vi expandera PV genom att öka `spec.resources.requests.storage` fältet:

```console
$ kubectl patch pvc pvc-azuredisk --type merge --patch '{"spec": {"resources": {"requests": {"storage": "15Gi"}}}}'

persistentvolumeclaim/pvc-azuredisk patched
```

Nu ska vi bekräfta att volymen är större:

```console
$ kubectl get pv

NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                                     STORAGECLASS   REASON   AGE
pvc-391ea1a6-0191-4022-b915-c8dc4216174a   15Gi       RWO            Delete           Bound    default/pvc-azuredisk                     managed-csi             2d2h
(...)
```

> [!NOTE]
> PV återspeglar inte den nya storleken förrän den har en associerad podd igen.

Nu ska vi skapa en ny podd:

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

pod/nginx-azuredisk created
```

Bekräfta slutligen storleken på PV och inuti podden:
```console
$ kubectl get pvc pvc-azuredisk
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
pvc-azuredisk   Bound    pvc-391ea1a6-0191-4022-b915-c8dc4216174a   15Gi       RWO            managed-csi    2d2h

$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk
Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc         15G   46M   15G   1% /mnt/azuredisk
```

## <a name="shared-disk"></a>Delad disk

[Delade Azure-diskar är](../virtual-machines/disks-shared.md) en funktion för hanterade Diskar i Azure som gör det möjligt att koppla en Azure-disk till agentnoder samtidigt. Genom att koppla en hanterad disk till flera agentnoder kan du till exempel distribuera nya eller migrera befintliga klustrade program till Azure.

> [!IMPORTANT] 
> För närvarande stöds endast råblockenhet ( `volumeMode: Block` ) av Azure-diskens CSI-drivrutin. Program bör hantera samordning och kontroll av skrivningar, läsningar, lås, cacheminnen, monteringar och avspärrning på den delade disken, som exponeras som en råblockenhet.

Nu ska vi skapa en fil med `shared-disk.yaml` namnet genom att kopiera följande kommando som innehåller den delade disklagringsklassen och PV:

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

Skapa lagringsklassen med [kommandot kubectl apply][kubectl-apply] och ange `shared-disk.yaml` filen:

```console
$ kubectl apply -f shared-disk.yaml

storageclass.storage.k8s.io/managed-csi-shared created
persistentvolumeclaim/pvc-azuredisk-shared created
``` 

Nu ska vi skapa en fil med `deployment-shared.yml` namnet genom att kopiera följande kommando:

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

Skapa distributionen med [kommandot kubectl apply][kubectl-apply] och ange `deployment-shared.yml` filen:

```console
$ kubectl apply -f deployment-shared.yml

deployment/deployment-azuredisk created
```

Slutligen kontrollerar vi blockenheten i podden:

```console
# kubectl exec -it deployment-sharedisk-7454978bc6-xh7jp sh
/ # dd if=/dev/zero of=/dev/sdx bs=1024k count=100
100+0 records in
100+0 records out/s
```

## <a name="windows-containers"></a>Windows-containrar

CSI-drivrutinen för Azure-diskar stöder också Windows-noder och -containrar. Om du vill använda Windows-containrar följer du självstudien [om Windows-containrar för](windows-container-cli.md) att lägga till en Windows-nodpool.

När du har en Windows-nodpool kan du nu använda de inbyggda lagringsklasserna som `managed-csi` . Du kan distribuera en [Windows-baserad](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/windows/statefulset.yaml) tillståndsbaserad uppsättning som sparar tidsstämplar i filen genom att distribuera följande kommando med `data.txt` [kommandot kubectl apply:][kubectl-apply]

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/windows/statefulset.yaml

statefulset.apps/busybox-azuredisk created
```

Nu kan du verifiera innehållet på volymen genom att köra:

```console
$ kubectl exec -it busybox-azuredisk-0 -- cat c:\\mnt\\azuredisk\\data.txt # on Linux/MacOS Bash
$ kubectl exec -it busybox-azuredisk-0 -- cat c:\mnt\azuredisk\data.txt # on Windows Powershell/CMD

2020-08-27 08:13:41Z
2020-08-27 08:13:42Z
2020-08-27 08:13:44Z
(...)
```

## <a name="next-steps"></a>Nästa steg

- Information om hur du använder CSI-drivrutiner för Azure Files finns i [Använda Azure Files med CSI-drivrutiner.](azure-files-csi.md)
- Mer information om metodtips för lagring finns [i Metodtips för lagring och säkerhetskopieringar i Azure Kubernetes Service][operator-best-practices-storage].


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
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
