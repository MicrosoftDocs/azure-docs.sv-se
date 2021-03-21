---
title: Skapa en statisk volym för poddar i Azure Kubernetes service (AKS)
description: Lär dig hur du manuellt skapar en volym med Azure-diskar för användning med en POD i Azure Kubernetes service (AKS)
services: container-service
ms.topic: article
ms.date: 03/01/2019
ms.openlocfilehash: 7d8a038926fc6bf3234b43a82c0259ba633df11e
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102506658"
---
# <a name="manually-create-and-use-a-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Skapa och använda en volym med Azure-diskar i Azure Kubernetes service (AKS) manuellt

Containerbaserade program behöver ofta åtkomst till och bevara data i en extern data volym. Om en enskild Pod behöver åtkomst till lagringen kan du använda Azure-diskar för att presentera en inbyggd volym för program användning. Den här artikeln visar hur du skapar en Azure-disk manuellt och kopplar den till en POD i AKS.

> [!NOTE]
> En Azure-disk kan bara monteras till en enda Pod i taget. Om du behöver dela en permanent volym över flera poddar, använder du [Azure Files][azure-files-volume].

Mer information om Kubernetes-volymer finns i [lagrings alternativ för program i AKS][concepts-storage].

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster kan du läsa snabb starten för AKS [med hjälp av Azure CLI][aks-quickstart-cli] eller [Azure Portal][aks-quickstart-portal].

Du måste också ha Azure CLI-versionen 2.0.59 eller senare installerad och konfigurerad. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

## <a name="create-an-azure-disk"></a>Skapa en Azure-disk

När du skapar en Azure-disk för användning med AKS kan du skapa disk resursen i resurs gruppen för **noden** . Den här metoden gör att AKS-klustret kan komma åt och hantera disk resursen. Om du i stället skapar disken i en separat resurs grupp måste du bevilja Azure Kubernetes service (AKS)-hanterad identitet för ditt kluster `Contributor` rollen till diskens resurs grupp.

I den här artikeln skapar du disken i resurs gruppen för noden. Börja med att hämta resurs gruppens namn med kommandot [AZ AKS show][az-aks-show] och Lägg till `--query nodeResourceGroup` Frågeparametern. I följande exempel hämtas resurs gruppen för AKS kluster namnet *myAKSCluster* i resurs grupps namnet *myResourceGroup*:

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Skapa nu en disk med kommandot [AZ disk Create][az-disk-create] . Ange resurs grupp namnet för noden som hämtades i föregående kommando och ett namn för disk resursen, till exempel *myAKSDisk*. I följande exempel skapas en *20* GIB-disk och utdata skapas för disken när den har skapats. Om du behöver skapa en disk för användning med Windows Server-behållare lägger du till `--os-type windows` parametern för att formatera disken korrekt.

```azurecli-interactive
az disk create \
  --resource-group MC_myResourceGroup_myAKSCluster_eastus \
  --name myAKSDisk \
  --size-gb 20 \
  --query id --output tsv
```

> [!NOTE]
> Azure-diskar debiteras per SKU för en speciell storlek. Dessa SKU: er sträcker sig från 32GiB för S4-eller P4-diskar till 32TiB för S80-eller P80-diskar (för hands version). Genomflödet och IOPS-prestanda för en Premium-hanterad disk beror på både SKU: n och instans storleken för noderna i AKS-klustret. Se [priser och prestanda för Managed disks][managed-disk-pricing-performance].

Disk resurs-ID: t visas när kommandot har slutförts, som visas i följande exempel på utdata. Detta disk-ID används för att montera disken i nästa steg.

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

## <a name="mount-disk-as-volume"></a>Montera disk som volym

Om du vill montera Azure-disken i din POD konfigurerar du volymen i behållar specifikationen. Skapa en ny fil `azure-disk-pod.yaml` med namnet med följande innehåll. Uppdatera `diskName` med namnet på disken som skapades i föregående steg och `diskURI` med det disk-ID som visas i utdata från kommandot disk Create. Om du vill kan du uppdatera `mountPath` , som är den sökväg där Azure-disken är monterad i pod. För Windows Server-behållare anger du en *mountPath* med hjälp av Windows Sök vägs konvention, till exempel *":"*.

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

Använd `kubectl` kommandot för att skapa pod.

```console
kubectl apply -f azure-disk-pod.yaml
```

Nu har du en igång-Pod med en Azure-disk monterad på `/mnt/azure` . Du kan använda `kubectl describe pod mypod` för att kontrol lera att disken har monterats. I följande komprimerade exempel utdata visas den volym som är monterad i behållaren:

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

För associerade metod tips, se [metod tips för lagring och säkerhets kopiering i AKS][operator-best-practices-storage].

Mer information om AKS-kluster interagerar med Azure-diskar finns i [Kubernetes-plugin-programmet för Azure-diskar][kubernetes-disks].

<!-- LINKS - external -->
[kubernetes-disks]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_disk/README.md
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-group-list]: /cli/azure/group#az-group-list
[az-resource-show]: /cli/azure/resource#az-resource-show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[install-azure-cli]: /cli/azure/install-azure-cli
[azure-files-volume]: azure-files-volume.md
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
