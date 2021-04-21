---
title: Använda Container Storage Interface (CSI)-drivrutiner för Azure Files on Azure Kubernetes Service (AKS)
description: Lär dig hur du använder Container Storage Interface (CSI)-drivrutiner för Azure Files i ett Azure Kubernetes Service-kluster (AKS).
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: a83d2222862db6bc3e3ff86ba4074114c1a872e5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776167"
---
# <a name="use-azure-files-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks-preview"></a>Använda Azure Files Container Storage Interface (CSI)-drivrutiner i Azure Kubernetes Service (AKS) (förhandsversion)

CSI-drivrutinen (Azure Files Container Storage Interface) är en [CSI-specifikation](https://github.com/container-storage-interface/spec/blob/master/spec.md)som följer standard och som används av Azure Kubernetes Service (AKS) för att hantera livscykeln för Azure Files resurser.

CSI är en standard för att exponera godtyckliga block- och fillagringssystem för containeriserade arbetsbelastningar i Kubernetes. Genom att använda och använda CSI kan AKS nu skriva, distribuera och iterera plugin-program för att exponera nya eller förbättra befintliga lagringssystem i Kubernetes utan att behöva röra Kubernetes-kärnkoden och vänta på lanseringscyklerna.

Information om hur du skapar ett AKS-kluster med CSI-drivrutinsstöd finns i Aktivera [CSI-drivrutiner för Azure-diskar Azure Files på AKS](csi-storage-drivers.md).

>[!NOTE]
> *Träddrivrutiner refererar* till de aktuella lagringsdrivrutinerna som ingår i Kubernetes-kärnkoden jämfört med de nya CSI-drivrutinerna, som är plugin-program.

## <a name="use-a-persistent-volume-with-azure-files"></a>Använda en beständig volym med Azure Files

En [beständig volym (PV)](concepts-storage.md#persistent-volumes) representerar en lagringsplats som etableras för användning med Kubernetes-poddar. Ett PV kan användas av en eller flera poddar och kan etableras dynamiskt eller statiskt. Om flera poddar behöver samtidig åtkomst till samma lagringsvolym kan du använda Azure Files för att ansluta med [hjälp av SMB-protokollet (Server Message Block).][smb-overview] Den här artikeln visar hur du dynamiskt skapar en Azure Files-resurs för användning av flera poddar i ett AKS-kluster. För statisk etablering, se [Skapa och använda en volym manuellt med en Azure Files resurs](azure-files-volume.md).

Mer information om Kubernetes-volymer finns i [Lagringsalternativ för program i AKS][concepts-storage].

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes"></a>Skapa datorer Azure Files dynamiskt med hjälp av de inbyggda lagringsklasserna

En lagringsklass används för att definiera hur Azure Files resurs skapas. Ett lagringskonto skapas automatiskt i [nodresursgruppen][node-resource-group] för användning med lagringsklassen för att lagra Azure Files resurser. Välj någon av följande [SKU:er för Azure Storage-redundans][storage-skus] *för skuName:*

* **Standard_LRS:** Standard lokalt redundant lagring
* **Standard_GRS:** Geo-redundant standardlagring
* **Standard_ZRS:** Standardzonredundant lagring
* **Standard_RAGRS:** Geo-redundant standardlagring med läsbehörighet
* **Premium_LRS:** Lokalt redundant Premium-lagring

> [!NOTE]
> Azure Files har stöd för Azure Premium Storage. Den minsta premiumfilresursen är 100 GB.

När du använder CSI-lagringsdrivrutiner på AKS finns det två ytterligare inbyggda som använder `StorageClasses` CSI Azure Files lagringsdrivrutiner. De ytterligare CSI-lagringsklasserna skapas med klustret tillsammans med standardlagringsklasserna i trädet.

- `azurefile-csi`: Använder Azure Standard Storage för att skapa Azure Files resurs.
- `azurefile-csi-premium`: Använder Azure Premium Storage för att skapa en Azure Files resurs.

Principen för att frigöra på båda lagringsklasserna säkerställer att den underliggande Azure Files-resursen tas bort när respektive PV tas bort. Lagringsklasserna konfigurerar även filresurser så att de kan expanderas. Du behöver bara redigera beständigt volymanspråk (PV) med den nya storleken.

Om du vill använda dessa lagringsklasser skapar du [en PV](concepts-storage.md#persistent-volume-claims) och respektive podd som refererar till och använder dem. En PV används för att automatiskt etablera lagring baserat på en lagringsklass. En PV kan använda en av de förskapade lagringsklasserna eller en användardefinierad lagringsklass för att skapa en Azure Files resurs för önskad SKU och storlek. När du skapar en podddefinition anges PV för att begära önskad lagring.

Skapa ett [exempel på PV och podd som skriver ut det aktuella datumet till en `outfile` ](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/statefulset.yaml) med kommandot [kubectl apply:][kubectl-apply]

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/pvc-azurefile-csi.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/nginx-pod-azurefile.yaml

persistentvolumeclaim/pvc-azurefile created
pod/nginx-azurefile created
```

När podden är i körningstillstånd kan du verifiera att filresursen är korrekt monterad genom att köra följande kommando och verifiera att utdata innehåller `outfile` :

```console
$ kubectl exec nginx-azurefile -- ls -l /mnt/azurefile

total 29
-rwxrwxrwx 1 root root 29348 Aug 31 21:59 outfile
```

## <a name="create-a-custom-storage-class"></a>Skapa en anpassad lagringsklass

Standardlagringsklasserna passar de vanligaste scenarierna, men inte alla. I vissa fall kanske du vill ha en egen lagringsklass som är anpassad efter dina egna parametrar. Använd till exempel följande manifest för att konfigurera `mountOptions` för filresursen.

Standardvärdet för *fileMode och* *dirMode är* *0777 för* Kubernetes-monterade filresurser. Du kan ange olika monteringsalternativ för lagringsklassobjektet.

Skapa en fil med namnet `azure-file-sc.yaml` och klistra in följande exempelmanifest:

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

Skapa lagringsklassen med [kommandot kubectl apply:][kubectl-apply]

```console
kubectl apply -f azure-file-sc.yaml

storageclass.storage.k8s.io/my-azurefile created
```

Den Azure Files CSI-drivrutinen har stöd [för att skapa ögonblicksbilder av beständiga](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html) volymer och underliggande filresurser.

Skapa en [volymögonblicksbildklass](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/snapshot/volumesnapshotclass-azurefile.yaml) med [kommandot kubectl apply:][kubectl-apply]

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/snapshot/volumesnapshotclass-azurefile.yaml

volumesnapshotclass.snapshot.storage.k8s.io/csi-azurefile-vsc created
```

Skapa en [ögonblicksbild av volymen](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/snapshot/volumesnapshot-azurefile.yaml) från PV som vi skapade dynamiskt i början av den här [självstudien](#dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes), `pvc-azurefile` .


```bash
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/snapshot/volumesnapshot-azurefile.yaml


volumesnapshot.snapshot.storage.k8s.io/azurefile-volume-snapshot created
```

Kontrollera att ögonblicksbilden har skapats korrekt:

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

Du kan begära en större volym för en PV. Redigera PV-objektet och ange en större storlek. Den här ändringen utlöser expansionen av den underliggande volymen som backar upp PV.

> [!NOTE]
> Ett nytt PV skapas aldrig för att uppfylla anspråket. I stället ändras storleken på en befintlig volym.

I AKS har den inbyggda lagringsklassen redan stöd för expansion, så använd `azurefile-csi` PV som skapades tidigare med den här [lagringsklassen](#dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes). PV begärde en 100Gi-filresurs. Vi kan bekräfta detta genom att köra:

```console 
$ kubectl exec -it nginx-azurefile -- df -h /mnt/azurefile

Filesystem                                                                                Size  Used Avail Use% Mounted on
//f149b5a219bd34caeb07de9.file.core.windows.net/pvc-5e5d9980-da38-492b-8581-17e3cad01770  100G  128K  100G   1% /mnt/azurefile
```

Expandera PV genom att öka `spec.resources.requests.storage` fältet:

```console
$ kubectl patch pvc pvc-azurefile --type merge --patch '{"spec": {"resources": {"requests": {"storage": "200Gi"}}}}'

persistentvolumeclaim/pvc-azurefile patched
```

Kontrollera att både PV och filsystemet i podden visar den nya storleken:

```console
$ kubectl get pvc pvc-azurefile
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS    AGE
pvc-azurefile   Bound    pvc-5e5d9980-da38-492b-8581-17e3cad01770   200Gi      RWX            azurefile-csi   64m

$ kubectl exec -it nginx-azurefile -- df -h /mnt/azurefile
Filesystem                                                                                Size  Used Avail Use% Mounted on
//f149b5a219bd34caeb07de9.file.core.windows.net/pvc-5e5d9980-da38-492b-8581-17e3cad01770  200G  128K  200G   1% /mnt/azurefile
```


## <a name="nfs-file-shares"></a>NFS-filresurser
[Azure Files har nu stöd för NFS v4.1-protokollet](../storage/files/storage-files-how-to-create-nfs-shares.md). NFS 4.1-stöd för Azure Files ger dig ett fullständigt hanterat NFS-filsystem som en tjänst som bygger på en mycket tillgänglig och mycket beständig distribuerad elastisk lagringsplattform.

 Det här alternativet är optimerat för arbetsbelastningar med slumpmässig åtkomst med datauppdateringar på plats och ger fullständigt stöd för POSIX-filsystemet. Det här avsnittet visar hur du använder NFS-resurser med Azure File CSI-drivrutinen i ett AKS-kluster.

Se till att kontrollera begränsningarna [och](../storage/files/storage-files-compare-protocols.md#limitations) [tillgängligheten för regioner](../storage/files/storage-files-compare-protocols.md#regional-availability) under förhandsversionsfasen.

### <a name="register-the-allownfsfileshares-preview-feature"></a>Registrera `AllowNfsFileShares` förhandsgranskningsfunktionen

Om du vill skapa en filresurs som använder NFS 4.1 måste du aktivera `AllowNfsFileShares` funktionsflaggan för din prenumeration.

Registrera `AllowNfsFileShares` funktionsflaggan med [kommandot az feature register,][az-feature-register] som du ser i följande exempel:

```azurecli-interactive
az feature register --namespace "Microsoft.Storage" --name "AllowNfsFileShares"
```

Det tar några minuter för statusen att visa *Registrerad*. Kontrollera registreringsstatusen med hjälp av [kommandot az feature list:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/AllowNfsFileShares')].{Name:name,State:properties.state}"
```

När du är klar uppdaterar du registreringen av *resursprovidern Microsoft.Storage* med kommandot [az provider register:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.Storage
```

### <a name="create-a-storage-account-for-the-nfs-file-share"></a>Skapa ett lagringskonto för NFS-filresursen

[Skapa en `Premium_LRS` Azure Storage-konto](../storage/files/storage-how-to-create-file-share.md) med följande konfigurationer för att stödja NFS-resurser:
- kontotyp: FileStorage
- säker överföring krävs (aktivera endast HTTPS-trafik): falskt
- välj det virtuella nätverket för dina agentnoder i Brandväggar och virtuella nätverk, så du kanske föredrar att skapa lagringskontot i MC_ resursgruppen.

### <a name="create-nfs-file-share-storage-class"></a>Skapa en lagringsklass för NFS-filresurs

Spara en `nfs-sc.yaml` fil med manifestet nedan och redigera respektive platshållare.

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

När du har redigerat och sparat filen skapar du lagringsklassen med [kommandot kubectl apply:][kubectl-apply]

```console
$ kubectl apply -f nfs-sc.yaml

storageclass.storage.k8s.io/azurefile-csi created
```

### <a name="create-a-deployment-with-an-nfs-backed-file-share"></a>Skapa en distribution med en NFS-backad filresurs
Du kan distribuera ett exempel [på en](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/statefulset.yaml) tillståndsfull uppsättning som sparar tidsstämplar i en fil genom att distribuera följande kommando med `data.txt` kommandot [kubectl apply:][kubectl-apply]

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
> Observera att eftersom NFS-filresursen finns i Premium-kontot är den minsta filresursstorleken 100 GB. Om du skapar en PV med en liten lagringsstorlek kan du stöta på felet "det gick inte att skapa filresursen ... storlek (5)...".


## <a name="windows-containers"></a>Windows-containrar

Den Azure Files CSI-drivrutinen stöder också Windows-noder och -containrar. Om du vill använda Windows-containrar följer du självstudien [om Windows-containrar för](windows-container-cli.md) att lägga till en Windows-nodpool.

När du har en Windows-nodpool kan du använda de inbyggda lagringsklasserna `azurefile-csi` som eller skapa anpassade. Du kan distribuera en [Windows-baserad](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/windows/statefulset.yaml) tillståndsbaserad uppsättning som sparar tidsstämplar i en fil genom att distribuera följande kommando med `data.txt` [kommandot kubectl apply:][kubectl-apply]

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

- Information om hur du använder CSI-drivrutiner för Azure-diskar finns i [Använda Azure-diskar med CSI-drivrutiner.](azure-disk-csi.md)
- Mer information om metodtips för lagring finns [i Metodtips för lagring och säkerhetskopieringar i Azure Kubernetes Service][operator-best-practices-storage].


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
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[storage-skus]: ../storage/common/storage-redundancy.md