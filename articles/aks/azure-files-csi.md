---
title: Använda CSI-drivrutiner (container Storage Interface) för Azure Files på Azure Kubernetes service (AKS)
description: Lär dig hur du använder CSI-drivrutinerna (container Storage Interface) för Azure Files i ett Azure Kubernetes service-kluster (AKS).
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: 93f7f7a3c59beca362145ac16f7cf727df773f81
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102174069"
---
# <a name="use-azure-files-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks-preview"></a>Använda CSI-drivrutiner (Azure Files container Storage Interface) i Azure Kubernetes service (AKS) (för hands version)

Driv rutinen för Azure Files container Storage Interface (CSI) är en [CSI Specification](https://github.com/container-storage-interface/spec/blob/master/spec.md)-kompatibel driv rutin som används av Azure Kubernetes service (AKS) för att hantera Azure Files resursernas livs cykel.

CSI är en standard för att exponera godtyckliga block-och fil lagrings system för arbets belastningar i behållare på Kubernetes. Genom att införa och använda CSI kan AKS nu skriva, distribuera och iterera plugin-program för att exponera nya eller förbättra befintliga lagrings system i Kubernetes utan att behöva röra kärnan Kubernetes Code och vänta på dess versions cykler.

Om du vill skapa ett AKS-kluster med stöd för CSI-drivrutiner, se [Aktivera CSI-drivrutiner för Azure-diskar och Azure Files på AKS](csi-storage-drivers.md).

>[!NOTE]
> *Driv rutiner i ett träd* refererar till de aktuella lagrings driv rutiner som ingår i Kubernetes-koden och de nya CSI-drivrutinerna, som är plugin-program.

## <a name="use-a-persistent-volume-with-azure-files"></a>Använd en permanent volym med Azure Files

En [beständig volym (PV)](concepts-storage.md#persistent-volumes) representerar en lagrings enhet som tillhandahålls för användning med Kubernetes poddar. Ett PV kan användas av en eller flera poddar och kan vara dynamiskt eller statiskt allokerat. Om flera poddar behöver samtidig åtkomst till samma lagrings volym kan du använda Azure Files för att ansluta med hjälp av [SMB-protokollet (Server Message Block)][smb-overview]. Den här artikeln visar hur du skapar en Azure Files-resurs dynamiskt för användning av flera poddar i ett AKS-kluster. För statisk etablering, se [skapa och använda en volym med en Azure Files resurs manuellt](azure-files-volume.md).

Mer information om Kubernetes-volymer finns i [lagrings alternativ för program i AKS][concepts-storage].

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes"></a>Skapa Azure Files PVs dynamiskt med hjälp av de inbyggda lagrings klasserna

En lagrings klass används för att definiera hur en Azure Files resurs skapas. Ett lagrings konto skapas automatiskt i [resurs gruppen resurs][node-resource-group] för användning med lagrings klassen som innehåller Azure Files resurser. Välj någon av följande [redundans-SKU: er för Azure Storage][storage-skus] för *skuName*:

* **Standard_LRS**: standard lokalt redundant lagring
* **Standard_GRS**: standard Geo-redundant lagring
* **Standard_ZRS**: standard zon – redundant lagring
* **Standard_RAGRS**: standard Geo-redundant lagring med Läs behörighet
* **Premium_LRS**: Premium lokalt redundant lagring

> [!NOTE]
> Azure Files stöder Azure Premium Storage. Den minsta Premium fil resursen är 100 GB.

När du använder driv rutiner för lagring CSI på AKS finns det två ytterligare inbyggda `StorageClasses` som använder Azure Files CSI-lagringsenheter. De ytterligare klasserna för CSI-lagring skapas med klustret tillsammans med standard lagrings klasser i trädet.

- `azurefile-csi`: Använder Azure standard Storage för att skapa en Azure Files-resurs.
- `azurefile-csi-premium`: Använder Azure Premium Storage för att skapa en Azure Files-resurs.

Reclaim-principen på båda lagrings klasserna säkerställer att den underliggande Azure Files resursen tas bort när respektive PV tas bort. Lagrings klasserna konfigurerar även fil resurserna så att de kan utökas, du behöver bara redigera det permanenta volym anspråket (PVC) med den nya storleken.

Om du vill använda dessa lagrings klasser skapar du en [PVC](concepts-storage.md#persistent-volume-claims) och respektive POD som refererar till och använder dem. En PVC används för att automatiskt etablera lagring baserat på en lagrings klass. En PVC kan använda en av de i förväg skapade lagrings klasserna eller en användardefinierad lagrings klass för att skapa en Azure Files-resurs för önskad SKU och storlek. När du skapar en POD-definition, anges PVC: n för att begära det önskade lagrings utrymmet.

Skapa ett [exempel på PVC och Pod som skriver ut det aktuella datumet `outfile` till ett](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/statefulset.yaml) med kommandot [kubectl Apply][kubectl-apply] :

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/pvc-azurefile-csi.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/nginx-pod-azurefile.yaml

persistentvolumeclaim/pvc-azurefile created
pod/nginx-azurefile created
```

När pod är i körnings läge kan du kontrol lera att fil resursen är korrekt monterad genom att köra följande kommando och kontrol lera att utdata innehåller `outfile` :

```console
$ kubectl exec nginx-azurefile -- ls -l /mnt/azurefile

total 29
-rwxrwxrwx 1 root root 29348 Aug 31 21:59 outfile
```

## <a name="create-a-custom-storage-class"></a>Skapa en anpassad lagrings klass

Standard lagrings klasserna passar de vanligaste scenarierna, men inte alla. I vissa fall kanske du vill ha en egen lagrings klass anpassad med dina egna parametrar. Använd till exempel följande manifest för att konfigurera `mountOptions` fil resursen.

Standardvärdet för *fileMode* och *dirMode* är *0777* för Kubernetes-monterade fil resurser. Du kan ange olika monterings alternativ för objektet lagrings klass.

Skapa en fil med namnet `azure-file-sc.yaml` och klistra in följande exempel manifest:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: my-azurefile
provisioner: file.csi.azure.com
reclaimPolicy: Delete
volumeBindingMode: Immediate
allowVolumeExpansion: true
mountOptions:
  - dir_mode=0640
  - file_mode=0640
  - uid=0
  - gid=0
  - mfsymlinks
  - cache=strict # https://linux.die.net/man/8/mount.cifs
  - nosharesock
parameters:
  skuName: Standard_LRS
```

Skapa lagrings klassen med kommandot [kubectl Apply][kubectl-apply] :

```console
kubectl apply -f azure-file-sc.yaml

storageclass.storage.k8s.io/my-azurefile created
```

Azure Files CSI-drivrutinen har stöd [för att skapa ögonblicks bilder av permanenta volymer](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html) och de underliggande fil resurserna.

Skapa en [volym ögonblicks bild klass](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/snapshot/volumesnapshotclass-azurefile.yaml) med kommandot [kubectl Apply][kubectl-apply] :

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/snapshot/volumesnapshotclass-azurefile.yaml

volumesnapshotclass.snapshot.storage.k8s.io/csi-azurefile-vsc created
```

Skapa en [ögonblicks bild av en ögonblicks bild](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/snapshot/volumesnapshot-azurefile.yaml) från PVC: n som du [skapade dynamiskt i början av den här kursen](#dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes) `pvc-azurefile` .


```bash
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/snapshot/volumesnapshot-azurefile.yaml


volumesnapshot.snapshot.storage.k8s.io/azurefile-volume-snapshot created
```

Verifiera att ögonblicks bilden skapades korrekt:

```bash
$ kubectl describe volumesnapshot azurefile-volume-snapshot

Name:         azurefile-volume-snapshot
Namespace:    default
Labels:       <none>
Annotations:  API Version:  snapshot.storage.k8s.io/v1beta1
Kind:         VolumeSnapshot
Metadata:
  Creation Timestamp:  2020-08-27T22:37:41Z
  Finalizers:
    snapshot.storage.kubernetes.io/volumesnapshot-as-source-protection
    snapshot.storage.kubernetes.io/volumesnapshot-bound-protection
  Generation:        1
  Resource Version:  955091
  Self Link:         /apis/snapshot.storage.k8s.io/v1beta1/namespaces/default/volumesnapshots/azurefile-volume-snapshot
  UID:               c359a38f-35c1-4fb1-9da9-2c06d35ca0f4
Spec:
  Source:
    Persistent Volume Claim Name:  pvc-azurefile
  Volume Snapshot Class Name:      csi-azurefile-vsc
Status:
  Bound Volume Snapshot Content Name:  snapcontent-c359a38f-35c1-4fb1-9da9-2c06d35ca0f4
  Ready To Use:                        false
Events:                                <none>
```

## <a name="resize-a-persistent-volume"></a>Ändra storlek på en beständig volym

Du kan begära en större volym för en PVC. Redigera PVC-objektet och ange en större storlek. Den här ändringen utlöser expansionen av den underliggande volymen som backar upp NUVÄRDEt.

> [!NOTE]
> En ny PV skapas aldrig för att uppfylla kravet. I stället ändras storleken på en befintlig volym.

I AKS stöder den inbyggda `azurefile-csi` lagrings klassen redan expansion, så Använd den [PVC som skapades tidigare med denna lagrings klass](#dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes). PVC-filen begärde en 100Gi-filresurs. Vi kan bekräfta detta genom att köra:

```console 
$ kubectl exec -it nginx-azurefile -- df -h /mnt/azurefile

Filesystem                                                                                Size  Used Avail Use% Mounted on
//f149b5a219bd34caeb07de9.file.core.windows.net/pvc-5e5d9980-da38-492b-8581-17e3cad01770  100G  128K  100G   1% /mnt/azurefile
```

Expandera PVC: n genom att öka `spec.resources.requests.storage` fältet:

```console
$ kubectl patch pvc pvc-azurefile --type merge --patch '{"spec": {"resources": {"requests": {"storage": "200Gi"}}}}'

persistentvolumeclaim/pvc-azurefile patched
```

Kontrol lera att både PVC-och fil systemet i pod visar den nya storleken:

```console
$ kubectl get pvc pvc-azurefile
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS    AGE
pvc-azurefile   Bound    pvc-5e5d9980-da38-492b-8581-17e3cad01770   200Gi      RWX            azurefile-csi   64m

$ kubectl exec -it nginx-azurefile -- df -h /mnt/azurefile
Filesystem                                                                                Size  Used Avail Use% Mounted on
//f149b5a219bd34caeb07de9.file.core.windows.net/pvc-5e5d9980-da38-492b-8581-17e3cad01770  200G  128K  200G   1% /mnt/azurefile
```


## <a name="nfs-file-shares"></a>NFS-filresurser
[Azure Files har nu stöd för NFS v 4.1-protokollet](../storage/files/storage-files-how-to-create-nfs-shares.md). NFS 4,1-stöd för Azure Files ger dig ett fullständigt hanterat NFS-filsystem som en tjänst som bygger på en hög tillgänglig och mycket varaktig distribuerad elastisk lagrings plattform.

 Det här alternativet är optimerat för slumpmässiga åtkomst arbets belastningar med data uppdateringar på plats och ger fullständig POSIX-filsystem stöd. Det här avsnittet visar hur du använder NFS-resurser med Azure-filen CSI-drivrutinen i ett AKS-kluster.

Se till att kontrol lera [begränsningar](../storage/files/storage-files-compare-protocols.md#limitations) och [regions tillgänglighet](../storage/files/storage-files-compare-protocols.md#regional-availability) under för hands versions fasen.

### <a name="register-the-allownfsfileshares-preview-feature"></a>Registrera `AllowNfsFileShares` förhands gransknings funktionen

Om du vill skapa en fil resurs som utnyttjar NFS 4,1 måste du aktivera `AllowNfsFileShares` funktions flaggan i din prenumeration.

Registrera `AllowNfsFileShares` funktions flaggan med hjälp av kommandot [AZ Feature register][az-feature-register] , som du ser i följande exempel:

```azurecli-interactive
az feature register --namespace "Microsoft.Storage" --name "AllowNfsFileShares"
```

Det tar några minuter för statusen att visa *registrerad*. Verifiera registrerings statusen med hjälp av kommandot [AZ feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/AllowNfsFileShares')].{Name:name,State:properties.state}"
```

När du är klar uppdaterar du registreringen av *Microsoft. Storage* Resource Provider med hjälp av [AZ Provider register][az-provider-register] kommando:

```azurecli-interactive
az provider register --namespace Microsoft.Storage
```

### <a name="create-a-storage-account-for-the-nfs-file-share"></a>Skapa ett lagrings konto för NFS-filresursen

[Skapa en `Premium_LRS` Azure Storage-konto](../storage/files/storage-how-to-create-file-share.md) med följande konfigurationer för att stödja NFS-resurser:
- konto typ: FileStorage
- säker överföring krävs (Aktivera endast HTTPS-trafik): falskt
- Välj det virtuella nätverket för dina agent-noder i brand väggar och virtuella nätverk – så du kanske föredrar att skapa lagrings kontot i MC_ resurs gruppen.

### <a name="create-nfs-file-share-storage-class"></a>Skapa fil resurs lagrings klass för NFS

Spara en `nfs-sc.yaml` fil med manifestet nedan redigera respektive plats hållare.

```yml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: azurefile-csi-nfs
provisioner: file.csi.azure.com
parameters:
  resourceGroup: EXISTING_RESOURCE_GROUP_NAME  # optional, required only when storage account is not in the same resource group as your agent nodes
  storageAccount: EXISTING_STORAGE_ACCOUNT_NAME
  protocol: nfs
```

När du har redigerat och sparat filen skapar du lagrings klassen med kommandot [kubectl Apply][kubectl-apply] :

```console
$ kubectl apply -f nfs-sc.yaml

storageclass.storage.k8s.io/azurefile-csi created
```

### <a name="create-a-deployment-with-an-nfs-backed-file-share"></a>Skapa en distribution med en NFS-baserad fil resurs
Du kan distribuera en exempel [tillstånds känslig uppsättning](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/statefulset.yaml) som sparar tidsstämplar i en fil `data.txt` genom att distribuera följande kommando med kommandot [kubectl Apply][kubectl-apply] :

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/statefulset.yaml

statefulset.apps/statefulset-azurefile created
```

Verifiera volymens innehåll genom att köra:

```console
$ kubectl exec -it statefulset-azurefile-0 -- df -h

Filesystem      Size  Used Avail Use% Mounted on
...
/dev/sda1                                                                                 29G   11G   19G  37% /etc/hosts
accountname.file.core.windows.net:/accountname/pvc-fa72ec43-ae64-42e4-a8a2-556606f5da38  100G     0  100G   0% /mnt/azurefile
...
```

>[!NOTE]
> Observera att eftersom NFS-filresursen är i Premium-kontot är den minsta fil resurs storleken 100 GB. Om du skapar en PVC med en liten lagrings storlek kan du stöta på ett fel som "Det gick inte att skapa fil resursen... storlek (5)... ".


## <a name="windows-containers"></a>Windows-containrar

Azure Files CSI-drivrutinen stöder också Windows-noder och behållare. Om du vill använda Windows-behållare följer du [själv studie kursen för Windows-behållare](windows-container-cli.md) för att lägga till en Windows-Node-pool.

När du har en Windows-adresspool använder du de inbyggda lagrings klasserna som `azurefile-csi` eller skapar anpassade. Du kan distribuera ett exempel på en [Windows-baserad tillstånds känslig uppsättning](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/windows/statefulset.yaml) som sparar tidsstämplar i en fil `data.txt` genom att distribuera följande kommando med kommandot [kubectl Apply][kubectl-apply] :

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/windows/statefulset.yaml

statefulset.apps/busybox-azurefile created
```

Verifiera volymens innehåll genom att köra:

```console
$ kubectl exec -it busybox-azurefile-0 -- cat c:\\mnt\\azurefile\\data.txt # on Linux/MacOS Bash
$ kubectl exec -it busybox-azurefile-0 -- cat c:\mnt\azurefile\data.txt # on Windows Powershell/CMD

2020-08-27 22:11:01Z
2020-08-27 22:11:02Z
2020-08-27 22:11:04Z
(...)
```

## <a name="next-steps"></a>Nästa steg

- Information om hur du använder CSI-drivrutiner för Azure-diskar finns i [använda Azure-diskar med CSI-drivrutiner](azure-disk-csi.md).
- Mer information om metod tips för lagring finns i [metod tips för lagring och säkerhets kopiering i Azure Kubernetes-tjänsten][operator-best-practices-storage].


<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview


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
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[storage-skus]: ../storage/common/storage-redundancy.md