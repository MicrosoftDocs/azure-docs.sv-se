---
title: SSH till AKS-klusternoder (Azure Kubernetes Service)
description: Lär dig hur du skapar en SSH-anslutning med Azure Kubernetes Service-klusternoder (AKS) för felsöknings- och underhållsaktiviteter.
services: container-service
ms.topic: article
ms.date: 07/31/2019
ms.openlocfilehash: 8df3e8be14e258aac34881014057dd7ee7ec3239
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769543"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>Ansluta med SSH till AKS-klusternoder (Azure Kubernetes Service) vid underhåll eller felsökning

Under livscykeln för ditt Azure Kubernetes Service(AKS)-kluster kan du behöva komma åt en AKS-nod. Den här åtkomsten kan vara för underhåll, logginsamling eller andra felsökningsåtgärder. Du kan komma åt AKS-noder med hjälp av SSH, inklusive Windows Server-noder. Du kan också [ansluta till Windows Server-noder med RDP-anslutningar (Remote Desktop Protocol).][aks-windows-rdp] Av säkerhetsskäl exponeras inte AKS-noderna mot Internet. Om du vill använda SSH till AKS-noderna använder du den privata IP-adressen.

Den här artikeln visar hur du skapar en SSH-anslutning med en AKS-nod med hjälp av deras privata IP-adresser.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster kan du gå till AKS-snabbstarten med hjälp av [Azure CLI][aks-quickstart-cli] eller [använda Azure Portal][aks-quickstart-portal].

Som standard hämtas eller genereras SSH-nycklar och läggs sedan till i noder när du skapar ett AKS-kluster. Den här artikeln visar hur du anger andra SSH-nycklar än de SSH-nycklar som användes när du skapade AKS-klustret. Artikeln visar också hur du fastställer den privata IP-adressen för noden och ansluter till den med hjälp av SSH. Om du inte behöver ange en annan SSH-nyckel kan du hoppa över steget för att lägga till den offentliga SSH-nyckeln till noden.

Den här artikeln förutsätter också att du har en SSH-nyckel. Du kan skapa en SSH-nyckel med [hjälp av macOS, Linux][ssh-nix] eller [Windows.][ssh-windows] Om du använder PuTTY Gen för att skapa nyckelparet sparar du nyckelparet i OpenSSH-format i stället för standardformatet för privat PuTTy-nyckel (.ppk-fil).

Azure CLI version 2.0.64 eller senare måste också vara installerat och konfigurerat. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

## <a name="configure-virtual-machine-scale-set-based-aks-clusters-for-ssh-access"></a>Konfigurera vm-skalningsuppsättningsbaserade AKS-kluster för SSH-åtkomst

Om du vill konfigurera din VM-skalningsuppsättningsbaserad för SSH-åtkomst söker du upp namnet på klustrets VM-skalningsuppsättning och lägger till din offentliga SSH-nyckel i skalningsuppsättningen.

Använd kommandot [az aks show][az-aks-show] för att hämta resursgruppens namn för ditt AKS-kluster och sedan kommandot [az vmss list][az-vmss-list] för att hämta namnet på din skalningsuppsättning.

```azurecli-interactive
CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
SCALE_SET_NAME=$(az vmss list --resource-group $CLUSTER_RESOURCE_GROUP --query '[0].name' -o tsv)
```

I exemplet ovan tilldelas namnet på klusterresursgruppen för *myAKSCluster* i *myResourceGroup till* *CLUSTER_RESOURCE_GROUP*. I exemplet används sedan *CLUSTER_RESOURCE_GROUP för* att lista skalningsuppsättningens namn och tilldela den *till SCALE_SET_NAME*.

> [!IMPORTANT]
> Just nu bör du bara uppdatera dina SSH-nycklar för dina VM-skalningsuppsättningsbaserade AKS-kluster med hjälp av Azure CLI.
> 
> För Linux-noder kan SSH-nycklar för närvarande endast läggas till med hjälp av Azure CLI. Om du vill ansluta till en Windows Server-nod med SSH använder du de SSH-nycklar som angavs när du skapade AKS-klustret och hoppar över nästa uppsättning kommandon för att lägga till din offentliga SSH-nyckel. Du behöver fortfarande IP-adressen för den nod som du vill felsöka, vilket visas i det sista kommandot i det här avsnittet. Du kan också ansluta till [Windows Server-noder med RDP-anslutningar (Remote Desktop Protocol) i][aks-windows-rdp] stället för att använda SSH.

Om du vill lägga till dina SSH-nycklar till noderna i en VM-skalningsuppsättning använder du kommandona [az vmss extension set][az-vmss-extension-set] och az [vmss update-instances.][az-vmss-update-instances]

```azurecli-interactive
az vmss extension set  \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --vmss-name $SCALE_SET_NAME \
    --name VMAccessForLinux \
    --publisher Microsoft.OSTCExtensions \
    --version 1.4 \
    --protected-settings "{\"username\":\"azureuser\", \"ssh_key\":\"$(cat ~/.ssh/id_rsa.pub)\"}"

az vmss update-instances --instance-ids '*' \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --name $SCALE_SET_NAME
```

I exemplet ovan används *variablerna CLUSTER_RESOURCE_GROUP* *och SCALE_SET_NAME* från föregående kommandon. I exemplet ovan används även *~/.ssh/id_rsa.pub* som plats för din offentliga SSH-nyckel.

> [!NOTE]
> Som standard är användarnamnet för AKS-noderna *azureuser*.

När du har lagt till din offentliga SSH-nyckel i skalningsuppsättningen kan du använda SSH till en virtuell noddator i skalningsuppsättningen med dess IP-adress. Visa de privata IP-adresserna för AKS-klusternoderna med [kommandot kubectl get][kubectl-get].

```console
kubectl get nodes -o wide
```

Följande exempelutdata visar de interna IP-adresserna för alla noder i klustret, inklusive en Windows Server-nod.

```console
$ kubectl get nodes -o wide

NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Registrera den interna IP-adressen för den nod som du vill felsöka.

Om du vill komma åt din nod med hjälp av SSH följer du stegen [i Skapa SSH-anslutningen](#create-the-ssh-connection).

## <a name="configure-virtual-machine-availability-set-based-aks-clusters-for-ssh-access"></a>Konfigurera AKS-kluster för tillgänglighetsuppsättning för virtuell dator för SSH-åtkomst

Om du vill konfigurera det AKS-baserade AKS-klustret för SSH-åtkomst för den virtuella datorns tillgänglighetsuppsättning söker du upp namnet på klustrets Linux-nod och lägger till din offentliga SSH-nyckel till noden.

Använd kommandot [az aks show][az-aks-show] för att hämta resursgruppsnamnet för ditt AKS-kluster och sedan kommandot [az vm list][az-vm-list] för att visa namnet på den virtuella datorn för klustrets Linux-nod.

```azurecli-interactive
CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az vm list --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

I exemplet ovan tilldelas namnet på klusterresursgruppen för *myAKSCluster i* *myResourceGroup till* *CLUSTER_RESOURCE_GROUP*. I exemplet används sedan *CLUSTER_RESOURCE_GROUP för* att lista namnet på den virtuella datorn. Exempelutdata visar namnet på den virtuella datorn:

```
Name                      ResourceGroup                                  Location
------------------------  ---------------------------------------------  ----------
aks-nodepool1-79590246-0  MC_myResourceGroupAKS_myAKSClusterRBAC_eastus  eastus
```

Om du vill lägga till dina SSH-nycklar till noden använder du [kommandot az vm user update.][az-vm-user-update]

```azurecli-interactive
az vm user update \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --name aks-nodepool1-79590246-0 \
    --username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

I exemplet ovan används *variabeln CLUSTER_RESOURCE_GROUP* och nodens namn på den virtuella datorn från tidigare kommandon. I exemplet ovan används även *~/.ssh/id_rsa.pub* som plats för din offentliga SSH-nyckel. Du kan också använda innehållet i din offentliga SSH-nyckel i stället för att ange en sökväg.

> [!NOTE]
> Som standard är användarnamnet för AKS-noderna *azureuser*.

När du har lagt till din offentliga SSH-nyckel till den virtuella noddatorn kan du använda SSH i den virtuella datorn med dess IP-adress. Visa den privata IP-adressen för en AKS-klusternod med kommandot [az vm list-ip-addresses.][az-vm-list-ip-addresses]

```azurecli-interactive
az vm list-ip-addresses --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

I exemplet ovan används *CLUSTER_RESOURCE_GROUP* variabeln som angetts i föregående kommandon. Följande exempelutdata visar de privata IP-adresserna för AKS-noderna:

```
VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-79590246-0  10.240.0.4
```

## <a name="create-the-ssh-connection"></a>Skapa SSH-anslutningen

Om du vill skapa en SSH-anslutning till en AKS-nod kör du en hjälppodd i ditt AKS-kluster. Den här hjälppodden ger dig SSH-åtkomst till klustret och sedan ytterligare SSH-nodåtkomst. Om du vill skapa och använda den här hjälppodden utför du följande steg:

1. Kör en `debian` containeravbildning och bifoga en terminalsession till den. Den här containern kan användas för att skapa en SSH-session med valfri nod i AKS-klustret:

    ```console
    kubectl run -it --rm aks-ssh --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
    ```

    > [!TIP]
    > Om du använder Windows Server-noder lägger du till en nodväljare i kommandot för att schemalägga Debian-containern på en Linux-nod:
    >
    > ```console
    > kubectl run -it --rm aks-ssh --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 --overrides='{"apiVersion":"v1","spec":{"nodeSelector":{"beta.kubernetes.io/os":"linux"}}}'
    > ```

1. När terminalsessionen är ansluten till containern installerar du en SSH-klient med hjälp av `apt-get` :

    ```console
    apt-get update && apt-get install openssh-client -y
    ```

1. Öppna ett nytt terminalfönster, som inte är anslutet till containern, och kopiera din privata SSH-nyckel till hjälppodden. Den här privata nyckeln används för att skapa SSH i AKS-noden. 

   Om det behövs ändrar *du ~/.ssh/id_rsa* till platsen för din privata SSH-nyckel:

    ```console
    kubectl cp ~/.ssh/id_rsa $(kubectl get pod -l run=aks-ssh -o jsonpath='{.items[0].metadata.name}'):/id_rsa
    ```

1. Gå tillbaka till terminalsessionen till containern och uppdatera behörigheterna för den kopierade privata `id_rsa` SSH-nyckeln så att den är skrivskyddad av användaren:

    ```console
    chmod 0400 id_rsa
    ```

1. Skapa en SSH-anslutning till AKS-noden. Återigen är standardanvändarnamnet för AKS-noder *azureuser*. Acceptera uppmaningen att fortsätta med anslutningen eftersom SSH-nyckeln först är betrodd. Du får sedan bash-prompten för AKS-noden:

    ```console
    $ ssh -i id_rsa azureuser@10.240.0.4

    ECDSA key fingerprint is SHA256:A6rnRkfpG21TaZ8XmQCCgdi9G/MYIMc+gFAuY9RUY70.
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added '10.240.0.4' (ECDSA) to the list of known hosts.

    Welcome to Ubuntu 16.04.5 LTS (GNU/Linux 4.15.0-1018-azure x86_64)

     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage

      Get cloud support with Ubuntu Advantage Cloud Guest:
        https://www.ubuntu.com/business/services/cloud

    [...]

    azureuser@aks-nodepool1-79590246-0:~$
    ```

## <a name="remove-ssh-access"></a>Ta bort SSH-åtkomst

När du är `exit` klar, SSH-sessionen och sedan `exit` den interaktiva containersessionen. När containersessionen stängs tas den podd som används för SSH-åtkomst från AKS-klustret bort.

## <a name="next-steps"></a>Nästa steg

Om du behöver ytterligare felsökningsdata kan du visa [kubelet-loggarna][view-kubelet-logs] eller [visa Kubernetes-huvudnodloggarna.][view-master-logs]

<!-- EXTERNAL LINKS -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-vm-list]: /cli/azure/vm#az_vm_list
[az-vm-user-update]: /cli/azure/vm/user#az_vm_user_update
[az-vm-list-ip-addresses]: /cli/azure/vm#az_vm_list_ip_addresses
[view-kubelet-logs]: kubelet-logs.md
[view-master-logs]: ./view-control-plane-logs.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-windows-rdp]: rdp.md
[ssh-nix]: ../virtual-machines/linux/mac-create-ssh-keys.md
[ssh-windows]: ../virtual-machines/linux/ssh-from-windows.md
[az-vmss-list]: /cli/azure/vmss#az_vmss_list
[az-vmss-extension-set]: /cli/azure/vmss/extension#az_vmss_extension_set
[az-vmss-update-instances]: /cli/azure/vmss#az_vmss_update_instances