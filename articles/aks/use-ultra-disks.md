---
title: Aktivera stöd för Ultra disk på Azure Kubernetes service (AKS)
description: Lär dig hur du aktiverar och konfigurerar Ultra disks i ett Azure Kubernetes service-kluster (AKS)
services: container-service
ms.topic: article
ms.date: 07/10/2020
ms.openlocfilehash: c743162ed3f75386287e050443e82069e797ced9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102502577"
---
# <a name="use-azure-ultra-disks-on-azure-kubernetes-service-preview"></a>Använd Azure Ultra disks på Azure Kubernetes service (för hands version)

[Azure Ultra disks](../virtual-machines/disks-enable-ultra-ssd.md) erbjuder högt data flöde, hög IOPS och konsekvent låg latens disk lagring för dina tillstånds känsliga program. En stor fördel med Ultra disks är möjligheten att dynamiskt ändra prestanda för SSD tillsammans med dina arbets belastningar utan att behöva starta om dina agent-noder. Ultra disks lämpar sig för data intensiva arbets belastningar.

## <a name="before-you-begin"></a>Innan du börjar

Den här funktionen kan bara ställas in när klustret skapas eller när en nod skapas.

> [!IMPORTANT]
> Azure Ultra disks kräver att nodepools distribueras i tillgänglighets zoner och regioner som har stöd för dessa diskar samt endast vissa VM-serier. Se [**omfattning och begränsningar för Ultra disks ga**](../virtual-machines/disks-enable-ultra-ssd.md#ga-scope-and-limitations).

### <a name="register-the-enableultrassd-preview-feature"></a>Registrera `EnableUltraSSD` förhands gransknings funktionen

Om du vill skapa ett AKS-kluster eller en resurspool som kan utnyttja Ultra disks måste du aktivera `EnableUltraSSD` funktions flaggan i din prenumeration.

Registrera `EnableUltraSSD` funktions flaggan med hjälp av kommandot [AZ Feature register][az-feature-register] som visas i följande exempel:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableUltraSSD"
```

Det tar några minuter för statusen att visa *registrerad*. Du kan kontrol lera registrerings statusen med hjälp av kommandot [AZ feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableUltraSSD')].{Name:name,State:properties.state}"
```

När du är klar uppdaterar du registreringen av resurs leverantören *Microsoft. container service* med hjälp av [AZ Provider register][az-provider-register] kommando:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="install-aks-preview-cli-extension"></a>Installera CLI-tillägget aks-preview

Om du vill skapa ett AKS-kluster eller en Node-pool som kan använda Ultra disks, behöver du det senaste *AKS CLI-* tillägget. Installera *AKS-Preview* Azure CLI-tillägget med kommandot [AZ Extension Add][az-extension-add] eller installera alla tillgängliga uppdateringar med kommandot [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 

### <a name="limitations"></a>Begränsningar
- Se [ **definitions område och begränsningar för Ultra DISKs ga**](../virtual-machines/disks-enable-ultra-ssd.md#ga-scope-and-limitations)
- Storleks intervallet som stöds för Ultra disks är mellan 100 och 1500

## <a name="create-a-new-cluster-that-can-use-ultra-disks"></a>Skapa ett nytt kluster som kan använda Ultra disks

Skapa ett AKS-kluster som kan utnyttja Ultra disks med hjälp av följande CLI-kommandon. Använd `--aks-custom-headers` flaggan för att ställa in `EnableUltraSSD` funktionen.

Skapa en Azure-resurs grupp:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Skapa AKS-klustret med stöd för Ultra disks.

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster -l westus2 --node-vm-size Standard_L8s_v2 --zones 1 2 --node-count 2 --aks-custom-headers EnableUltraSSD=true
```

Om du vill skapa kluster utan stöd för Ultra disk kan du göra det genom att utesluta den anpassade `--aks-custom-headers` parametern.

## <a name="enable-ultra-disks-on-an-existing-cluster"></a>Aktivera Ultra disks i ett befintligt kluster

Du kan aktivera Ultra disks i befintliga kluster genom att lägga till en ny Node-pool i klustret som stöder Ultra disks. Konfigurera en ny Node-pool att använda Ultra disks med hjälp av `--aks-custom-headers` flaggan.

```azurecli
az aks nodepool add --name ultradisk --cluster-name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_L8s_v2 --zones 1 2 --node-count 2 --aks-custom-headers EnableUltraSSD=true
```

Om du vill skapa nya resurspooler utan stöd för Ultra disks kan du göra det genom att utesluta den anpassade `--aks-custom-headers` parametern.

## <a name="use-ultra-disks-dynamically-with-a-storage-class"></a>Använd Ultra disks dynamiskt med en lagrings klass

Om du vill använda Ultra disks i våra distributioner eller tillstånds känsliga uppsättningar kan du använda en [lagrings klass för dynamisk etablering](azure-disks-dynamic-pv.md).

### <a name="create-the-storage-class"></a>Skapa lagrings klassen

En lagrings klass används för att definiera hur en lagrings enhet dynamiskt skapas med en permanent volym. Mer information om Kubernetes lagrings klasser finns i [Kubernetes Storage-klasser][kubernetes-storage-classes].

I det här fallet skapar vi en lagrings klass som refererar till Ultra disks. Skapa en fil med namnet `azure-ultra-disk-sc.yaml` och kopiera i följande manifest.

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

Skapa lagrings klassen med kommandot [kubectl Apply][kubectl-apply] och ange din *Azure-Ultra-disk-SC. yaml-* fil:

```console
$ kubectl apply -f azure-ultra-disk-sc.yaml


storageclass.storage.k8s.io/ultra-disk-sc created
```

## <a name="create-a-persistent-volume-claim"></a>Skapa ett beständigt volym anspråk

Ett permanent volym anspråk (PVC) används för att automatiskt etablera lagring baserat på en lagrings klass. I det här fallet kan en PVC använda den tidigare skapade lagrings klassen för att skapa en Ultra-disk.

Skapa en fil med namnet `azure-ultra-disk-pvc.yaml` och kopiera i följande manifest. Anspråket begär en disk med namnet `ultra-disk` *1000 GB* i storlek med *ReadWriteOnce* -åtkomst. Lagrings klassen *Ultra-disk-SC* har angetts som lagrings klass.

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

Skapa beständiga volym anspråk med kommandot [kubectl Apply][kubectl-apply] och ange din *Azure-Ultra-disk-PVC. yaml-* fil:

```console
$ kubectl apply -f azure-ultra-disk-pvc.yaml

persistentvolumeclaim/ultra-disk created
```

## <a name="use-the-persistent-volume"></a>Använd beständig volym

När beständiga volym anspråk har skapats och disken har etablerats kan du skapa en POD med åtkomst till disken. Följande manifest skapar en grundläggande NGINX-Pod som använder beständigt volym anspråk med namnet *Ultra-disk* för att montera Azure-disken på sökvägen `/mnt/azure` .

Skapa en fil med namnet `nginx-ultra.yaml` och kopiera i följande manifest.

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

Skapa Pod med kommandot [kubectl Apply][kubectl-apply] , som du ser i följande exempel:

```console
$ kubectl apply -f nginx-ultra.yaml

pod/nginx-ultra created
```

Nu har du en igång-Pod med Azure-disken monterad i `/mnt/azure` katalogen. Den här konfigurationen kan ses när du inspekterar din POD via `kubectl describe pod nginx-ultra` , som du ser i följande komprimerade exempel:

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

- Mer information om Ultra disks finns i [använda Azure Ultra disks](../virtual-machines/disks-enable-ultra-ssd.md).
- Mer information om metod tips för lagring finns i [metod tips för lagring och säkerhets kopiering i Azure Kubernetes service (AKS)][operator-best-practices-storage]

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
