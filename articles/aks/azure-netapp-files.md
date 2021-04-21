---
title: Integrera Azure NetApp Files med Azure Kubernetes Service
description: Lär dig hur du integrerar Azure NetApp Files med Azure Kubernetes Service
services: container-service
ms.topic: article
ms.date: 10/23/2020
ms.openlocfilehash: 28c5b77f06bc48bf06575e45194adfaed068b30f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776059"
---
# <a name="integrate-azure-netapp-files-with-azure-kubernetes-service"></a>Integrera Azure NetApp Files med Azure Kubernetes Service

[Azure NetApp Files][anf] är en fillagringstjänst med höga prestanda i företagsklass som körs i Azure. Den här artikeln visar hur du integrerar Azure NetApp Files med Azure Kubernetes Service (AKS).

## <a name="before-you-begin"></a>Innan du börjar
Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster kan du gå till AKS-snabbstarten med hjälp av [Azure CLI][aks-quickstart-cli] eller [använda Azure Portal][aks-quickstart-portal].

> [!IMPORTANT]
> Ditt AKS-kluster måste också [finnas i en region som stöder Azure NetApp Files][anf-regions].

Azure CLI version 2.0.59 eller senare måste också vara installerat och konfigurerat. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

### <a name="limitations"></a>Begränsningar

Följande begränsningar gäller när du använder Azure NetApp Files:

* Azure NetApp Files är endast tillgängligt i [valda Azure-regioner.][anf-regions]
* Innan du kan använda Azure NetApp Files måste du beviljas åtkomst till Azure NetApp Files tjänsten. Om du vill ansöka om åtkomst kan du använda Azure NetApp Files att skicka [in väntelistan][anf-waitlist] eller gå till https://azure.microsoft.com/services/netapp/#getting-started . Du kan inte komma åt Azure NetApp Files förrän du får den officiella bekräftelsemeddelandet från Azure NetApp Files teamet.
* Efter den första distributionen av ett AKS-kluster stöds endast statisk Azure NetApp Files distribution.
* Om du vill använda dynamisk Azure NetApp Files måste du installera och konfigurera [NetApp Trident](https://netapp-trident.readthedocs.io/) version 19.07 eller senare.

## <a name="configure-azure-netapp-files"></a>Konfigurera Azure NetApp Files

> [!IMPORTANT]
> Innan du kan registrera  *resursprovidern Microsoft.NetApp* måste du fylla [i Azure NetApp Files][anf-waitlist] eller gå till https://azure.microsoft.com/services/netapp/#getting-started för din prenumeration. Du kan inte registrera resursen förrän du får den officiella bekräftelsemeddelandet från Azure NetApp Files teamet.

Registrera *resursprovidern Microsoft.NetApp:*

```azurecli
az provider register --namespace Microsoft.NetApp --wait
```

> [!NOTE]
> Det kan ta lite tid att slutföra.

När du skapar ett Azure NetApp-konto för användning med AKS måste du skapa kontot i **nodresursgruppen.** Hämta först resursgruppens namn med kommandot [az aks show][az-aks-show] och lägg till `--query nodeResourceGroup` frågeparametern. I följande exempel hämtar nodresursgruppen för AKS-klustret med namnet *myAKSCluster* i resursgruppens namn *myResourceGroup*:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

```output
MC_myResourceGroup_myAKSCluster_eastus
```

Skapa ett Azure NetApp Files i nodresursgruppen och samma region som ditt AKS-kluster med [az netappfiles account create][az-netappfiles-account-create].  I följande exempel skapas ett konto med *namnet myaccount1* i *MC_myResourceGroup_myAKSCluster_eastus* och *regionen eastus:*

```azurecli
az netappfiles account create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1
```

Skapa en ny kapacitetspool med [hjälp av az netappfiles pool create][az-netappfiles-pool-create]. I följande exempel skapas en ny kapacitetspool med *namnet mypool1* med 4 TB i storlek och Premium-servicenivå: 

```azurecli
az netappfiles pool create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1 \
    --pool-name mypool1 \
    --size 4 \
    --service-level Premium
```

Skapa ett undernät att [delegera till Azure NetApp Files][anf-delegate-subnet] hjälp av az network [vnet subnet create][az-network-vnet-subnet-create]. *Det här undernätet måste finnas i samma virtuella nätverk som ditt AKS-kluster.*

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyNetAppSubnet
az network vnet subnet create \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $VNET_NAME \
    --name $SUBNET_NAME \
    --delegations "Microsoft.NetApp/volumes" \
    --address-prefixes 10.0.0.0/28
```

Skapa en volym med hjälp [av az netappfiles volume create][az-netappfiles-volume-create].

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
LOCATION=eastus
ANF_ACCOUNT_NAME=myaccount1
POOL_NAME=mypool1
SERVICE_LEVEL=Premium
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyNetAppSubnet
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP --vnet-name $VNET_NAME --name $SUBNET_NAME --query "id" -o tsv)
VOLUME_SIZE_GiB=100 # 100 GiB
UNIQUE_FILE_PATH="myfilepath2" # Please note that file path needs to be unique within all ANF Accounts

az netappfiles volume create \
    --resource-group $RESOURCE_GROUP \
    --location $LOCATION \
    --account-name $ANF_ACCOUNT_NAME \
    --pool-name $POOL_NAME \
    --name "myvol1" \
    --service-level $SERVICE_LEVEL \
    --vnet $VNET_ID \
    --subnet $SUBNET_ID \
    --usage-threshold $VOLUME_SIZE_GiB \
    --file-path $UNIQUE_FILE_PATH \
    --protocol-types "NFSv3"
```

## <a name="create-the-persistentvolume"></a>Skapa PersistentVolume

Visa information om volymen med [az netappfiles volume show][az-netappfiles-volume-show]

```azurecli
az netappfiles volume show --resource-group $RESOURCE_GROUP --account-name $ANF_ACCOUNT_NAME --pool-name $POOL_NAME --volume-name "myvol1"
```

```output
{
  ...
  "creationToken": "myfilepath2",
  ...
  "mountTargets": [
    {
      ...
      "ipAddress": "10.0.0.4",
      ...
    }
  ],
  ...
}
```

Skapa en `pv-nfs.yaml` definierande PersistentVolume. Ersätt `path` med *creationToken och* med `server` *ipAddress* från föregående kommando. Exempel:

```yaml
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-nfs
spec:
  capacity:
    storage: 100Gi
  accessModes:
    - ReadWriteMany
  mountOptions:
    - vers=3
  nfs:
    server: 10.0.0.4
    path: /myfilepath2
```

Uppdatera servern *och* *sökvägen* till värdena för den NFS-volym (Network File System) som du skapade i föregående steg. Skapa PersistentVolume med [kommandot kubectl apply:][kubectl-apply]

```console
kubectl apply -f pv-nfs.yaml
```

Kontrollera att *Status* för PersistentVolume är *Tillgänglig med* kommandot [kubectl describe:][kubectl-describe]

```console
kubectl describe pv pv-nfs
```

## <a name="create-the-persistentvolumeclaim"></a>Skapa PersistentVolumeClaim

Skapa en `pvc-nfs.yaml` definierande PersistentVolume. Exempel:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-nfs
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 1Gi
```

Skapa PersistentVolumeClaim med [kommandot kubectl apply:][kubectl-apply]

```console
kubectl apply -f pvc-nfs.yaml
```

Kontrollera att *Status* för PersistentVolumeClaim är *bunden* med kommandot [kubectl describe:][kubectl-describe]

```console
kubectl describe pvc pvc-nfs
```

## <a name="mount-with-a-pod"></a>Montera med en podd

Skapa en `nginx-nfs.yaml` definierande podd som använder PersistentVolumeClaim. Exempel:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-nfs
spec:
  containers:
  - image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    name: nginx-nfs
    command:
    - "/bin/sh"
    - "-c"
    - while true; do echo $(date) >> /mnt/azure/outfile; sleep 1; done
    volumeMounts:
    - name: disk01
      mountPath: /mnt/azure
  volumes:
  - name: disk01
    persistentVolumeClaim:
      claimName: pvc-nfs
```

Skapa podden med [kommandot kubectl apply:][kubectl-apply]

```console
kubectl apply -f nginx-nfs.yaml
```

Kontrollera att podden *körs med* kommandot [kubectl describe:][kubectl-describe]

```console
kubectl describe pod nginx-nfs
```

Kontrollera att volymen har monterats i podden med [kubectl exec][kubectl-exec] för att ansluta till podden och kontrollera `df -h` om volymen är monterad.

```console
$ kubectl exec -it nginx-nfs -- sh
```

```output
/ # df -h
Filesystem             Size  Used Avail Use% Mounted on
...
10.0.0.4:/myfilepath2  100T  384K  100T   1% /mnt/azure
...
```

## <a name="next-steps"></a>Nästa steg

Mer information om Azure NetApp Files finns i [Vad är Azure NetApp Files][anf]. Mer information om hur du använder NFS med AKS finns i Skapa och använda en [NFS (Network File System) Linux-servervolym manuellt med Azure Kubernetes Service (AKS).][aks-nfs]


[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-nfs]: azure-nfs-volume.md
[anf]: ../azure-netapp-files/azure-netapp-files-introduction.md
[anf-delegate-subnet]: ../azure-netapp-files/azure-netapp-files-delegate-subnet.md
[anf-quickstart]: ../azure-netapp-files/
[anf-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all
[anf-waitlist]: https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-netappfiles-account-create]: /cli/azure/netappfiles/account#az_netappfiles_account_create
[az-netappfiles-pool-create]: /cli/azure/netappfiles/pool#az_netappfiles_pool_create
[az-netappfiles-volume-create]: /cli/azure/netappfiles/volume#az_netappfiles_volume_create
[az-netappfiles-volume-show]: /cli/azure/netappfiles/volume#az_netappfiles_volume_show
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az_network_vnet_subnet_create
[install-azure-cli]: /cli/azure/install-azure-cli
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
