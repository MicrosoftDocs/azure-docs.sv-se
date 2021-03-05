---
title: Aktivera driv rutiner för container Storage-gränssnitt (CSI) på Azure Kubernetes service (AKS)
description: Lär dig hur du aktiverar CSI-drivrutinerna (container Storage Interface) för Azure-diskar och Azure Files i ett Azure Kubernetes service-kluster (AKS).
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: fa40ab22f0c1ebf47bb490a50f782a848d1441e1
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102182119"
---
# <a name="enable-container-storage-interface-csi-drivers-for-azure-disks-and-azure-files-on-azure-kubernetes-service-aks-preview"></a>Aktivera driv rutiner för container Storage-gränssnitt (CSI) för Azure-diskar och Azure Files på Azure Kubernetes service (AKS) (för hands version)

Container Storage-gränssnittet (CSI) är en standard för att exponera godtyckliga block-och fil lagrings system till arbets belastningar i behållare på Kubernetes. Genom att anta och använda CSI kan Azure Kubernetes service (AKS) skriva, distribuera och iterera plugin-program för att exponera nya eller förbättra befintliga lagrings system i Kubernetes utan att behöva röra kärnan Kubernetes-koden och vänta på dess lanserings cykler.

Med hjälp av driv rutins stödet för CSI på AKS kan du använda internt:
- [*Azure-diskar*](azure-disk-csi.md), som kan användas för att skapa en Kubernetes *DataDisk* -resurs. Diskar kan använda Azure Premium Storage, som backas upp av högpresterande SSD eller Azure standard Storage, som backas upp av vanliga hård diskar eller standard-SSD. Använd Premium Storage för de flesta arbets belastningar för produktion och utveckling. Azure-diskar monteras som *ReadWriteOnce*, så de är bara tillgängliga för en enda pod. För lagrings volymer som kan nås av flera poddar samtidigt använder du Azure Files.
- [*Azure Files*](azure-files-csi.md), som kan användas för att montera en SMB 3,0-resurs som backas upp av ett Azure Storage konto till poddar. Med Azure Files kan du dela data över flera noder och poddar. Azure Files kan använda Azure standard Storage som backas upp av vanliga hård diskar eller Azure-Premium Storage som backas upp av SSD med höga prestanda.

> [!IMPORTANT]
> Från och med Kubernetes version 1,21 kommer Kubernetes endast att använda CSI-drivrutiner och som standard. De här driv rutinerna är framtiden för lagrings support i Kubernetes.
>
> *Driv rutiner i ett träd* refererar till de aktuella lagrings driv rutiner som ingår i Kubernetes-koden och de nya CSI-drivrutinerna, som är plugin-program.

## <a name="limitations"></a>Begränsningar

- Den här funktionen kan bara ställas in när klustret skapas.
- Lägsta Kubernetes-lägre version som stöder CSI-drivrutiner är v 1.17.
- Under för hands versionen är standard lagrings klassen fortfarande [samma i-träd lagrings klass](concepts-storage.md#storage-classes). När den här funktionen är allmänt tillgänglig blir standard lagrings klassen lagrings `managed-csi` klassen och lagrings klasserna i trädet tas bort.
- Under den första förhands gransknings fasen stöds endast Azure CLI.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="register-the-enableazurediskfilecsidriver-preview-feature"></a>Registrera `EnableAzureDiskFileCSIDriver` förhands gransknings funktionen

Om du vill skapa ett AKS-kluster som kan använda CSI-drivrutiner för Azure-diskar och Azure Files måste du aktivera `EnableAzureDiskFileCSIDriver` funktions flaggan i din prenumeration.

Registrera `EnableAzureDiskFileCSIDriver` funktions flaggan med hjälp av kommandot [AZ Feature register][az-feature-register] , som du ser i följande exempel:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableAzureDiskFileCSIDriver"
```

Det tar några minuter för statusen att visa *registrerad*. Verifiera registrerings statusen med hjälp av kommandot [AZ feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableAzureDiskFileCSIDriver')].{Name:name,State:properties.state}"
```

När du är klar uppdaterar du registreringen av resurs leverantören *Microsoft. container service* med hjälp av kommandot [AZ Provider register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="install-aks-preview-cli-extension"></a>Installera CLI-tillägget aks-preview

Om du vill skapa ett AKS-kluster eller en Node-pool som kan använda CSI-lagringsenheter, behöver du det senaste *AKS-för hands versionen* av Azure CLI-tillägget. Installera *AKS-Preview* Azure CLI-tillägget med kommandot [AZ Extension Add][az-extension-add] . Eller installera eventuella tillgängliga uppdateringar med kommandot [AZ Extension Update][az-extension-update] .

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 


## <a name="create-a-new-cluster-that-can-use-csi-storage-drivers"></a>Skapa ett nytt kluster som kan använda CSI Storage-drivrutiner

Skapa ett nytt kluster som kan använda CSI-lagringsenheter för Azure-diskar och Azure Files med hjälp av följande CLI-kommandon. Använd `--aks-custom-headers` flaggan för att ställa in `EnableAzureDiskFileCSIDriver` funktionen.

Skapa en Azure-resurs grupp:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location canadacentral
```

Skapa AKS-klustret med stöd för CSI-lagrings driv rutiner:

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --network-plugin azure -k 1.17.9 --aks-custom-headers EnableAzureDiskFileCSIDriver=true
```

Om du vill skapa kluster i träd lagrings driv rutiner i stället för CSI-lagringsenheter kan du göra det genom att utesluta den anpassade `--aks-custom-headers` parametern.


Kontrol lera hur många Azure disk-baserade volymer du kan koppla till den här noden genom att köra:

```console
$ kubectl get nodes
aks-nodepool1-25371499-vmss000000
aks-nodepool1-25371499-vmss000001
aks-nodepool1-25371499-vmss000002

$ echo $(kubectl get CSINode <NODE NAME> -o jsonpath="{.spec.drivers[1].allocatable.count}")
8
```

## <a name="next-steps"></a>Nästa steg

- Information om hur du använder CSI-enheten för Azure-diskar finns i [använda Azure-diskar med CSI-drivrutiner](azure-disk-csi.md).
- Om du vill använda CSI-enheten för Azure Files, se [använd Azure Files med CSI-drivrutiner](azure-files-csi.md).
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