---
title: Aktivera Ultra Disk stöd för Azure Kubernetes Service (AKS)
description: Lär dig hur du aktiverar och konfigurerar Ultradiskar i ett Azure Kubernetes Service-kluster (AKS)
services: container-service
ms.topic: article
ms.date: 07/10/2020
ms.openlocfilehash: 7dbe0a75ce2079bdec752f7fee0c3e97e3ae2ffa
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767357"
---
# <a name="use-azure-ultra-disks-on-azure-kubernetes-service-preview"></a>Använda Azure Ultra Disks på Azure Kubernetes Service (förhandsversion)

[Azures ultradiskar](../virtual-machines/disks-enable-ultra-ssd.md) erbjuder disklagring med högt dataflöde, hög IOPS och konsekvent låg latens för dina tillståndsfula program. En stor fördel med ultradiskar är möjligheten att dynamiskt ändra SSD-prestanda tillsammans med dina arbetsbelastningar utan att behöva starta om agentnoderna. Ultradiskar passar för dataintensiva arbetsbelastningar.

## <a name="before-you-begin"></a>Innan du börjar

Den här funktionen kan bara anges när klustret skapas eller nodpoolen skapas.

> [!IMPORTANT]
> Azures ultradiskar kräver att nodpooler distribueras i tillgänglighetszoner och regioner som stöder dessa diskar samt endast specifika VM-serier. Se [**Omfång och begränsningar för Ultra-diskars GA.**](../virtual-machines/disks-enable-ultra-ssd.md#ga-scope-and-limitations)

### <a name="register-the-enableultrassd-preview-feature"></a>Registrera `EnableUltraSSD` förhandsgranskningsfunktionen

Om du vill skapa ett AKS-kluster eller en nodpool som kan utnyttja Ultradiskar måste du aktivera `EnableUltraSSD` funktionsflaggan för din prenumeration.

Registrera `EnableUltraSSD` funktionsflaggan med [kommandot az feature register][az-feature-register] enligt följande exempel:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableUltraSSD"
```

Det tar några minuter för statusen att visa *Registrerad*. Du kan kontrollera registreringsstatusen med kommandot [az feature list:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableUltraSSD')].{Name:name,State:properties.state}"
```

När du är klar uppdaterar du registreringen av *resursprovidern Microsoft.ContainerService* med kommandot [az provider register:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="install-aks-preview-cli-extension"></a>Installera CLI-tillägget aks-preview

Om du vill skapa ett AKS-kluster eller en nodpool som Ultradiskar behöver du det senaste *CLI-tillägget aks-preview.* Installera *Azure CLI-tillägget aks-preview* med [kommandot az extension add][az-extension-add] eller installera eventuella tillgängliga uppdateringar med kommandot az extension [update:][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 

### <a name="limitations"></a>Begränsningar
- Se [ **omfång och begränsningar för Ultra-diskars GA**](../virtual-machines/disks-enable-ultra-ssd.md#ga-scope-and-limitations)
- Storleksintervallet som stöds för ultradiskar är mellan 100 och 1 500

## <a name="create-a-new-cluster-that-can-use-ultra-disks"></a>Skapa ett nytt kluster som kan använda Ultra-diskar

Skapa ett AKS-kluster som kan utnyttja Ultradiskar med hjälp av följande CLI-kommandon. Använd `--aks-custom-headers` flaggan för att ange `EnableUltraSSD` funktionen.

Skapa en Azure-resursgrupp:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Skapa AKS-klustret med stöd för Ultradiskar.

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster -l westus2 --node-vm-size Standard_L8s_v2 --zones 1 2 --node-count 2 --aks-custom-headers EnableUltraSSD=true
```

Om du vill skapa kluster utan ultradiskstöd kan du göra det genom att utelämna den anpassade `--aks-custom-headers` parametern.

## <a name="enable-ultra-disks-on-an-existing-cluster"></a>Aktivera Ultradiskar i ett befintligt kluster

Du kan aktivera ultradiskar på befintliga kluster genom att lägga till en ny nodpool i klustret som stöder ultradiskar. Konfigurera en ny nodpool att använda ultradiskar med hjälp av `--aks-custom-headers` flaggan .

```azurecli
az aks nodepool add --name ultradisk --cluster-name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_L8s_v2 --zones 1 2 --node-count 2 --aks-custom-headers EnableUltraSSD=true
```

Om du vill skapa nya nodpooler utan stöd för ultradiskar kan du göra det genom att utelämna den anpassade `--aks-custom-headers` parametern.

## <a name="use-ultra-disks-dynamically-with-a-storage-class"></a>Använda ultradiskar dynamiskt med en lagringsklass

Om du vill använda ultradiskar i våra distributioner eller tillståndsfulla uppsättningar kan du använda en [lagringsklass för dynamisk etablering.](azure-disks-dynamic-pv.md)

### <a name="create-the-storage-class"></a>Skapa lagringsklassen

En lagringsklass används för att definiera hur en lagringsenhet skapas dynamiskt med en beständig volym. Mer information om Kubernetes-lagringsklasser finns i [Kubernetes-lagringsklasser][kubernetes-storage-classes].

I det här fallet skapar vi en lagringsklass som refererar till ultradiskar. Skapa en fil med `azure-ultra-disk-sc.yaml` namnet och kopiera följande manifest.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: ultra-disk-sc
provisioner: kubernetes.io/azure-disk
volumeBindingMode: WaitForFirstConsumer # optional, but recommended if you want to wait until the pod that will use this disk is created 
parameters:
  skuname: UltraSSD_LRS
  kind: managed
  cachingmode: None
  diskIopsReadWrite: "2000"  # minimum value: 2 IOPS/GiB 
  diskMbpsReadWrite: "320"   # minimum value: 0.032/GiB
```

Skapa lagringsklassen med [kommandot kubectl apply][kubectl-apply] och ange filen *azure-ultra-disk-sc.yaml:*

```console
$ kubectl apply -f azure-ultra-disk-sc.yaml


storageclass.storage.k8s.io/ultra-disk-sc created
```

## <a name="create-a-persistent-volume-claim"></a>Skapa ett beständigt volymanspråk

Ett beständigt volymanspråk (PV) används för att automatiskt etablera lagring baserat på en lagringsklass. I det här fallet kan en PV använda den tidigare skapade lagringsklassen för att skapa en ultradisk.

Skapa en fil med `azure-ultra-disk-pvc.yaml` namnet och kopiera följande manifest. Anspråket begär en disk med `ultra-disk` namnet som är *1 000 GB* stor med *ReadWriteOnce-åtkomst.* *Lagringsklassen ultra-disk-sc* anges som lagringsklass.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: ultra-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: ultra-disk-sc
  resources:
    requests:
      storage: 1000Gi
```

Skapa det beständiga volymanspråk med [kommandot kubectl apply][kubectl-apply] och ange filen *azure-ultra-disk-pv.yaml:*

```console
$ kubectl apply -f azure-ultra-disk-pvc.yaml

persistentvolumeclaim/ultra-disk created
```

## <a name="use-the-persistent-volume"></a>Använda den beständiga volymen

När det beständiga volymanspråket har skapats och disken har etablerats kan en podd skapas med åtkomst till disken. Följande manifest skapar en grundläggande NGINX-podd som använder det beständiga volymanspråk som heter ultra-disk för att montera *Azure-disken* på sökvägen `/mnt/azure` .

Skapa en fil med `nginx-ultra.yaml` namnet och kopiera följande manifest.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-ultra
spec:
  containers:
  - name: nginx-ultra
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
        claimName: ultra-disk
```

Skapa podden med [kommandot kubectl apply,][kubectl-apply] som du ser i följande exempel:

```console
$ kubectl apply -f nginx-ultra.yaml

pod/nginx-ultra created
```

Nu har du en podd som körs med din Azure-disk monterad i `/mnt/azure` katalogen . Den här konfigurationen kan visas när du inspekterar podden via `kubectl describe pod nginx-ultra` , som du ser i följande komprimerade exempel:

```console
$ kubectl describe pod nginx-ultra

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


## <a name="next-steps"></a>Nästa steg

- Mer information om ultradiskar finns i [Använda Azure Ultra-diskar.](../virtual-machines/disks-enable-ultra-ssd.md)
- Mer information om metodtips för lagring finns [i Metodtips för lagring och säkerhetskopieringar i Azure Kubernetes Service (AKS)][operator-best-practices-storage]

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
