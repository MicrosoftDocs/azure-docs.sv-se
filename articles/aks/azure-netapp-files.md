---
title: Integrera Azure NetApp Files med Azure Kubernetes-tjänsten
description: Lär dig att integrera Azure NetApp Files med Azure Kubernetes-tjänsten
services: container-service
ms.topic: article
ms.date: 10/23/2020
ms.openlocfilehash: 19727d3c3322b05f340463d94a2bc3884e5d9d93
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98196018"
---
# <a name="integrate-azure-netapp-files-with-azure-kubernetes-service"></a>Integrera Azure NetApp Files med Azure Kubernetes-tjänsten

[Azure NetApp Files][anf] är en högpresterande, högpresterande fil lagrings tjänst med hög prestanda som körs på Azure. Den här artikeln visar hur du integrerar Azure NetApp Files med Azure Kubernetes service (AKS).

## <a name="before-you-begin"></a>Innan du börjar
Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster kan du läsa snabb starten för AKS [med hjälp av Azure CLI][aks-quickstart-cli] eller [Azure Portal][aks-quickstart-portal].

> [!IMPORTANT]
> Ditt AKS-kluster måste också finnas [i en region som stöder Azure NetApp Files][anf-regions].

Du måste också ha Azure CLI-versionen 2.0.59 eller senare installerad och konfigurerad. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

### <a name="limitations"></a>Begränsningar

Följande begränsningar gäller när du använder Azure NetApp Files:

* Azure NetApp Files är endast tillgängligt [i valda Azure-regioner][anf-regions].
* Innan du kan använda Azure NetApp Files måste du beviljas åtkomst till tjänsten Azure NetApp Files. Om du vill använda för åtkomst kan du använda [Azure NetApp Files Waitlist-formuläret för överföring][anf-waitlist] eller gå till https://azure.microsoft.com/services/netapp/#getting-started . Du har inte åtkomst till tjänsten Azure NetApp Files förrän du får e-postmeddelandet från Azure NetApp Files teamet.
* Efter den första distributionen av ett AKS-kluster stöds endast statisk etablering för Azure NetApp Files.
* Om du vill använda dynamisk etablering med Azure NetApp Files installerar och konfigurerar du [NetApp Trident](https://netapp-trident.readthedocs.io/) version 19,07 eller senare.

## <a name="configure-azure-netapp-files"></a>Konfigurera Azure NetApp Files

> [!IMPORTANT]
> Innan du kan registrera  *Microsoft. NetApp* -resurs leverantören måste du fylla i formuläret för att [Skicka Azure NetApp Files Waitlist][anf-waitlist] eller gå till https://azure.microsoft.com/services/netapp/#getting-started för din prenumeration. Du kan inte registrera resursen förrän du får det officiella bekräftelse meddelandet från Azure NetApp Filess teamet.

Registrera *Microsoft. NetApp* -resurs leverantören:

```azurecli
az provider register --namespace Microsoft.NetApp --wait
```

> [!NOTE]
> Det kan ta lite tid att slutföra.

När du skapar ett Azure NetApp-konto som ska användas med AKS måste du skapa kontot i resurs gruppen för **noden** . Börja med att hämta resurs gruppens namn med kommandot [AZ AKS show][az-aks-show] och Lägg till `--query nodeResourceGroup` Frågeparametern. I följande exempel hämtas nodens resurs grupp för AKS-klustret med namnet *myAKSCluster* i resurs grupps namnet *myResourceGroup*:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

```output
MC_myResourceGroup_myAKSCluster_eastus
```

Skapa ett Azure NetApp Files-konto i resurs gruppen för **noden** och samma region som ditt AKS-kluster med [AZ netappfiles Account Create][az-netappfiles-account-create]. I följande exempel skapas ett konto med namnet *myaccount1* i resurs gruppen *MC_myResourceGroup_myAKSCluster_eastus* och *östra* regionen:

```azurecli
az netappfiles account create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1
```

Skapa en ny kapacitets uppsättning med [AZ netappfiles pool Create][az-netappfiles-pool-create]. I följande exempel skapas en ny pool med namnet *mypool1* med 4 TB i storleks-och *Premium* servicenivå:

```azurecli
az netappfiles pool create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1 \
    --pool-name mypool1 \
    --size 4 \
    --service-level Premium
```

Skapa ett undernät för att [delegera till Azure NetApp Files att][anf-delegate-subnet] använda [AZ Network VNet Subnet Create][az-network-vnet-subnet-create]. *Det här under nätet måste finnas i samma virtuella nätverk som ditt AKS-kluster.*

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

Skapa en volym med hjälp av [AZ netappfiles Volume Create][az-netappfiles-volume-create].

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

Visa information om din volym med hjälp av [AZ netappfiles Volume show][az-netappfiles-volume-show]

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

Skapa en `pv-nfs.yaml` definition av en PersistentVolume. Ersätt `path` med *creationToken* och `server` med *ipAddress* från föregående kommando. Exempel:

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

Uppdatera *servern* och *sökvägen* till värdena för din NFS-volym (Network File System) som du skapade i föregående steg. Skapa PersistentVolume med kommandot [kubectl Apply][kubectl-apply] :

```console
kubectl apply -f pv-nfs.yaml
```

Kontrol lera att *status* för PersistentVolume är *tillgänglig* med kommandot [kubectl Beskrivning][kubectl-describe] :

```console
kubectl describe pv pv-nfs
```

## <a name="create-the-persistentvolumeclaim"></a>Skapa PersistentVolumeClaim

Skapa en `pvc-nfs.yaml` definition av en PersistentVolume. Exempel:

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

Skapa PersistentVolumeClaim med kommandot [kubectl Apply][kubectl-apply] :

```console
kubectl apply -f pvc-nfs.yaml
```

Kontrol lera att *status* för PersistentVolumeClaim är *kopplad* till kommandot [kubectl Beskrivning][kubectl-describe] :

```console
kubectl describe pvc pvc-nfs
```

## <a name="mount-with-a-pod"></a>Montera med en POD

Skapa en `nginx-nfs.yaml` definition av en pod som använder PersistentVolumeClaim. Exempel:

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

Skapa Pod med kommandot [kubectl Apply][kubectl-apply] :

```console
kubectl apply -f nginx-nfs.yaml
```

Kontrol lera att Pod *körs* med hjälp av kommandot [kubectl Beskrivning][kubectl-describe] :

```console
kubectl describe pod nginx-nfs
```

Kontrol lera att volymen har monterats i pod genom att använda [kubectl exec][kubectl-exec] för att ansluta till Pod `df -h` för att kontrol lera om volymen är monterad.

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

Mer information om Azure NetApp Files finns i [Vad är Azure NetApp Files][anf]. Mer information om hur du använder NFS med AKS finns i [skapa och använda en NFS-volym (Network File System) manuellt med Azure Kubernetes service (AKS)][aks-nfs].


[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-nfs]: azure-nfs-volume.md
[anf]: ../azure-netapp-files/azure-netapp-files-introduction.md
[anf-delegate-subnet]: ../azure-netapp-files/azure-netapp-files-delegate-subnet.md
[anf-quickstart]: ../azure-netapp-files/
[anf-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all
[anf-waitlist]: https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-netappfiles-account-create]: /cli/azure/netappfiles/account?view=azure-cli-latest#az-netappfiles-account-create
[az-netappfiles-pool-create]: /cli/azure/netappfiles/pool?view=azure-cli-latest#az-netappfiles-pool-create
[az-netappfiles-volume-create]: /cli/azure/netappfiles/volume?view=azure-cli-latest#az-netappfiles-volume-create
[az-netappfiles-volume-show]: /cli/azure/netappfiles/volume?view=azure-cli-latest#az-netappfiles-volume-show
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-create
[install-azure-cli]: /cli/azure/install-azure-cli
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
