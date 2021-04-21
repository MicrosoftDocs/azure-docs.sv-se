---
title: Skapa en statisk volym för poddar i Azure Kubernetes Service (AKS)
description: Lär dig hur du skapar en volym manuellt med Azure-diskar för användning med en podd Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/01/2019
ms.openlocfilehash: 617ad75eda766963a91fe3d41b1dbfefae62b41b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776221"
---
# <a name="manually-create-and-use-a-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Skapa och använd en volym manuellt med Azure-diskar i AKS (Azure Kubernetes Service)

Containerbaserade program behöver ofta åtkomst till och bevara data i en extern datavolym. Om en enda podd behöver åtkomst till lagring kan du använda Azure-diskar för att presentera en intern volym för programanvändning. Den här artikeln visar hur du manuellt skapar en Azure-disk och kopplar den till en podd i AKS.

> [!NOTE]
> En Azure-disk kan bara monteras på en enda podd i taget. Om du behöver dela en beständig volym över flera poddar använder du [Azure Files][azure-files-volume].

Mer information om Kubernetes-volymer finns i [Lagringsalternativ för program i AKS][concepts-storage].

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster kan du gå till AKS-snabbstarten med Hjälp av [Azure CLI][aks-quickstart-cli] eller [använda Azure Portal][aks-quickstart-portal].

Du måste också ha installerat och konfigurerat Azure CLI version 2.0.59 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

## <a name="create-an-azure-disk"></a>Skapa en Azure-disk

När du skapar en Azure-disk för användning med AKS kan du skapa diskresursen i **nodresursgruppen.** Med den här metoden kan AKS-klustret komma åt och hantera diskresursen. Om du i stället skapar disken i en separat resursgrupp måste du ge den Azure Kubernetes Service (AKS) hanterade identiteten för klustret rollen `Contributor` till diskens resursgrupp.

I den här artikeln skapar du disken i nodresursgruppen. Hämta först resursgruppens namn med kommandot [az aks show][az-aks-show] och lägg till `--query nodeResourceGroup` frågeparametern. I följande exempel hämtar nodresursgruppen för AKS-klusternamnet *myAKSCluster* i resursgruppens namn *myResourceGroup*:

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Skapa nu en disk med kommandot [az disk][az-disk-create] create. Ange namnet på nodresursgruppen som du fick i föregående kommando och sedan ett namn för diskresursen, till exempel *myAKSDisk*. I följande exempel skapas en *20* GiB-disk och diskens ID matas ut när den har skapats. Om du behöver skapa en disk för användning med Windows Server-containrar lägger du till `--os-type windows` parametern för att formatera disken korrekt.

```azurecli-interactive
az disk create \
  --resource-group MC_myResourceGroup_myAKSCluster_eastus \
  --name myAKSDisk \
  --size-gb 20 \
  --query id --output tsv
```

> [!NOTE]
> Azure-diskar debiteras av SKU för en viss storlek. Dessa SKU:er sträcker sig från 32GiB för S4- eller P4-diskar till 32TiB för S80- eller P80-diskar (i förhandsversion). Dataflödet och IOPS-prestandan för en Premium-hanterad disk beror på både SKU:n och instansstorleken för noderna i AKS-klustret. Se [Priser och prestanda för Managed Disks][managed-disk-pricing-performance].

Diskresurs-ID:t visas när kommandot har slutförts, som du ser i följande exempelutdata. Detta disk-ID används för att montera disken i nästa steg.

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

## <a name="mount-disk-as-volume"></a>Montera disk som volym

Om du vill montera Azure-disken i din podd konfigurerar du volymen i containerspecifikationen. Skapa en ny fil med `azure-disk-pod.yaml` namnet med följande innehåll. Uppdatera med namnet på disken som skapades i föregående steg och med `diskName` `diskURI` disk-ID:t som visas i utdata från kommandot disk create. Om du vill uppdaterar du `mountPath` , som är sökvägen där Azure-disken är monterad i podden. För Windows Server-containrar anger du *en mountPath* med hjälp av Windows sökvägskonvention, till *exempel "D:".*

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
  volumes:
      - name: azure
        azureDisk:
          kind: Managed
          diskName: myAKSDisk
          diskURI: /subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

Använd kommandot `kubectl` för att skapa podden.

```console
kubectl apply -f azure-disk-pod.yaml
```

Nu har du en podd som körs med en Azure-disk monterad på `/mnt/azure` . Du kan använda `kubectl describe pod mypod` för att kontrollera att disken har monterats. Följande komprimerade exempelutdata visar volymen som monterats i containern:

```
[...]
Volumes:
  azure:
    Type:         AzureDisk (an Azure Data Disk mount on the host and bind mount to the pod)
    DiskName:     myAKSDisk
    DiskURI:      /subscriptions/<subscriptionID/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
    Kind:         Managed
    FSType:       ext4
    CachingMode:  ReadWrite
    ReadOnly:     false
  default-token-z5sd7:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-z5sd7
    Optional:    false
[...]
Events:
  Type    Reason                 Age   From                               Message
  ----    ------                 ----  ----                               -------
  Normal  Scheduled              1m    default-scheduler                  Successfully assigned mypod to aks-nodepool1-79590246-0
  Normal  SuccessfulMountVolume  1m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "default-token-z5sd7"
  Normal  SuccessfulMountVolume  41s   kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "azure"
[...]
```

## <a name="next-steps"></a>Nästa steg

Tillhörande metodtips finns i [Metodtips för lagring och säkerhetskopieringar i AKS.][operator-best-practices-storage]

Mer information om hur AKS-kluster interagerar med Azure-diskar finns i [Kubernetes-plugin-programmet för Azure Disks.][kubernetes-disks]

<!-- LINKS - external -->
[kubernetes-disks]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_disk/README.md
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[az-disk-list]: /cli/azure/disk#az_disk_list
[az-disk-create]: /cli/azure/disk#az_disk_create
[az-group-list]: /cli/azure/group#az_group_list
[az-resource-show]: /cli/azure/resource#az_resource_show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[az-aks-show]: /cli/azure/aks#az_aks_show
[install-azure-cli]: /cli/azure/install-azure-cli
[azure-files-volume]: azure-files-volume.md
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
