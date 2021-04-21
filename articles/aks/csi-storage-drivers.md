---
title: Aktivera Container Storage Interface (CSI)-drivrutiner på Azure Kubernetes Service (AKS)
description: Lär dig hur du aktiverar Container Storage Interface (CSI)-drivrutiner för Azure-diskar och Azure Files i ett Azure Kubernetes Service-kluster (AKS).
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: c9edfdf1c9740ec1fdaaeeedbc6ba92793eb0b3f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779965"
---
# <a name="enable-container-storage-interface-csi-drivers-for-azure-disks-and-azure-files-on-azure-kubernetes-service-aks-preview"></a>Aktivera Container Storage Interface (CSI) drivrutiner för Azure-diskar och Azure Files på Azure Kubernetes Service (AKS) (förhandsversion)

CSI (Container Storage Interface) är en standard för att exponera godtyckliga block- och fillagringssystem för containeriserade arbetsbelastningar på Kubernetes. Genom att implementera och använda CSI kan Azure Kubernetes Service (AKS) skriva, distribuera och iterera plugin-program för att exponera nya eller förbättra befintliga lagringssystem i Kubernetes utan att behöva röra Kubernetes-kärnkoden och vänta på lanseringscyklerna.

Med stöd för CSI-lagringsdrivrutiner i AKS kan du använda inbyggt:
- [*Azure-diskar*](azure-disk-csi.md), som kan användas för att skapa en Kubernetes *DataDisk-resurs.* Diskar kan använda Azure Premium Storage, backas upp av högpresterande SSD:er eller Azure Standard Storage, backas upp av vanliga hårddiskar eller Standard-HÅRDDISKAR. För de flesta arbetsbelastningar för produktion och utveckling använder Premium Storage. Azure-diskar monteras som *ReadWriteOnce,* så de är bara tillgängliga för en enda podd. För lagringsvolymer som kan nås av flera poddar samtidigt använder du Azure Files.
- [*Azure Files*](azure-files-csi.md), som kan användas för att montera en SMB 3.0-resurs som backas upp av ett Azure Storage-konto till poddar. Med Azure Files kan du dela data över flera noder och poddar. Azure Files kan Azure Standard Storage av vanliga hårddiskar eller Azure Premium Storage med högpresterande HÅRDDISKAR.

> [!IMPORTANT]
> Från och med Kubernetes version 1.21 använder Kubernetes endast CSI-drivrutiner och som standard. Dessa drivrutiner är framtidens lagringsstöd i Kubernetes.
>
> *Träddrivrutiner refererar* till de aktuella lagringsdrivrutinerna som ingår i Kubernetes-kärnkoden jämfört med de nya CSI-drivrutinerna, som är plugin-program.

## <a name="limitations"></a>Begränsningar

- Den här funktionen kan bara anges när klustret skapas.
- Den lägsta Kubernetes-delversionen som stöder CSI-drivrutiner är v1.17.
- I förhandsversionen är standardlagringsklassen fortfarande [samma lagringsklass i trädet.](concepts-storage.md#storage-classes) När den här funktionen är allmänt tillgänglig blir standardlagringsklassen lagringsklassen `managed-csi` och lagringsklasser i trädet tas bort.
- Under den första förhandsversionsfasen stöds endast Azure CLI.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="register-the-enableazurediskfilecsidriver-preview-feature"></a>Registrera `EnableAzureDiskFileCSIDriver` förhandsgranskningsfunktionen

Om du vill skapa ett AKS-kluster som kan använda CSI-drivrutiner för Azure-diskar och Azure Files måste du aktivera `EnableAzureDiskFileCSIDriver` funktionsflaggan för din prenumeration.

Registrera `EnableAzureDiskFileCSIDriver` funktionsflaggan med [kommandot az feature register,][az-feature-register] som du ser i följande exempel:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableAzureDiskFileCSIDriver"
```

Det tar några minuter för statusen att visa *Registrerad*. Kontrollera registreringsstatusen med hjälp av [kommandot az feature list:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableAzureDiskFileCSIDriver')].{Name:name,State:properties.state}"
```

När du är klar uppdaterar du registreringen av *resursprovidern Microsoft.ContainerService* med kommandot [az provider register:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="install-aks-preview-cli-extension"></a>Installera CLI-tillägget aks-preview

Om du vill skapa ett AKS-kluster eller en nodpool som kan använda CSI-lagringsdrivrutiner behöver du det senaste *Azure CLI-tillägget aks-preview.* Installera Azure *CLI-tillägget aks-preview* med hjälp av [kommandot az extension add.][az-extension-add] Eller installera eventuella tillgängliga uppdateringar med hjälp av [kommandot az extension update.][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 


## <a name="create-a-new-cluster-that-can-use-csi-storage-drivers"></a>Skapa ett nytt kluster som kan använda CSI-lagringsdrivrutiner

Skapa ett nytt kluster som kan använda CSI-lagringsdrivrutiner för Azure-diskar Azure Files med hjälp av följande CLI-kommandon. Använd `--aks-custom-headers` flaggan för att ange `EnableAzureDiskFileCSIDriver` funktionen.

Skapa en Azure-resursgrupp:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location canadacentral
```

Skapa AKS-klustret med stöd för CSI-lagringsdrivrutiner:

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --network-plugin azure  --aks-custom-headers EnableAzureDiskFileCSIDriver=true
```

Om du vill skapa kluster i trädlagringsdrivrutiner i stället för CSI-lagringsdrivrutiner kan du göra det genom att utelämna den anpassade `--aks-custom-headers` parametern.


Kontrollera hur många Azure-diskbaserade volymer du kan ansluta till den här noden genom att köra:

```console
$ kubectl get nodes
aks-nodepool1-25371499-vmss000000
aks-nodepool1-25371499-vmss000001
aks-nodepool1-25371499-vmss000002

$ echo $(kubectl get CSINode <NODE NAME> -o jsonpath="{.spec.drivers[1].allocatable.count}")
8
```

## <a name="next-steps"></a>Nästa steg

- Information om hur du använder CSI-enheten för Azure-diskar finns [i Använda Azure-diskar med CSI-drivrutiner.](azure-disk-csi.md)
- Information om hur du använder CSI-Azure Files finns i [Använda Azure Files med CSI-drivrutiner.](azure-files-csi.md)
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