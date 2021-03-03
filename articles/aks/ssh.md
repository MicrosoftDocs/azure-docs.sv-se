---
title: SSH till AKS-klusternoder (Azure Kubernetes Service)
description: Lär dig hur du skapar en SSH-anslutning med AKS-klusternoder (Azure Kubernetes service) för fel söknings-och underhålls aktiviteter.
services: container-service
ms.topic: article
ms.date: 07/31/2019
ms.openlocfilehash: 96334985862c65000d783df3a205406f046be07a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101740553"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>Ansluta med SSH till AKS-klusternoder (Azure Kubernetes Service) vid underhåll eller felsökning

Under hela livs cykeln för ditt Azure Kubernetes service-kluster (AKS) kan du behöva komma åt en AKS-nod. Den här åtkomsten kan vara för underhåll, logg insamling eller andra fel söknings åtgärder. Du kan komma åt AKS-noder med SSH, inklusive Windows Server-noder. Du kan också [ansluta till Windows Server-noder med RDP-anslutningar (Remote Desktop Protocol)][aks-windows-rdp]. Av säkerhets synpunkt visas inte AKS-noderna för Internet. För SSH till AKS-noderna använder du den privata IP-adressen.

Den här artikeln visar hur du skapar en SSH-anslutning med en AKS-nod med hjälp av sina privata IP-adresser.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster kan du läsa snabb starten för AKS [med hjälp av Azure CLI][aks-quickstart-cli] eller [Azure Portal][aks-quickstart-portal].

Som standard hämtas SSH-nycklar, eller genereras, och läggs sedan till noderna när du skapar ett AKS-kluster. Den här artikeln visar hur du anger olika SSH-nycklar än de SSH-nycklar som används när du skapade ditt AKS-kluster. Artikeln visar också hur du fastställer nodens privata IP-adress och ansluter till den med hjälp av SSH. Om du inte behöver ange en annan SSH-nyckel kan du hoppa över steget för att lägga till den offentliga SSH-nyckeln till noden.

Den här artikeln förutsätter också att du har en SSH-nyckel. Du kan skapa en SSH-nyckel med [MacOS eller Linux][ssh-nix] eller [Windows][ssh-windows]. Om du använder funktionen SparaTillFil för att skapa nyckel paret sparar du nyckel paret i ett OpenSSH-format i stället för standardvärdet för formatet SparaTillFil-privat nyckel (. PPK-fil).

Du måste också ha Azure CLI-versionen 2.0.64 eller senare installerad och konfigurerad. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

## <a name="configure-virtual-machine-scale-set-based-aks-clusters-for-ssh-access"></a>Konfigurera AKS-kluster för skalnings uppsättningar för virtuella datorer för SSH-åtkomst

Om du vill konfigurera skalnings uppsättningen för virtuella datorer, kan du hitta namnet på klustrets skalnings uppsättning för virtuella datorer och lägga till din offentliga SSH-nyckel till den skalnings uppsättningen.

Använd kommandot [AZ AKS show][az-aks-show] för att hämta resurs grupps namnet för ditt AKS-kluster, sedan kommandot [AZ VMSS List][az-vmss-list] för att hämta namnet på din skalnings uppsättning.

```azurecli-interactive
CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
SCALE_SET_NAME=$(az vmss list --resource-group $CLUSTER_RESOURCE_GROUP --query '[0].name' -o tsv)
```

Exemplet ovan tilldelar namnet på kluster resurs gruppen för *myAKSCluster* i *myResourceGroup* till *CLUSTER_RESOURCE_GROUP*. Exemplet använder sedan *CLUSTER_RESOURCE_GROUP* för att visa namnet på skalnings uppsättningen och tilldela det till *SCALE_SET_NAME*.

> [!IMPORTANT]
> Vid detta tillfälle bör du bara uppdatera dina SSH-nycklar för dina virtuella datorers skalnings uppsättnings AKS-kluster med hjälp av Azure CLI.
> 
> För Linux-noder kan SSH-nycklar för närvarande endast läggas till med hjälp av Azure CLI. Om du vill ansluta till en Windows Server-nod med SSH använder du de SSH-nycklar som angavs när du skapade AKS-klustret och hoppar över nästa uppsättning kommandon för att lägga till din offentliga SSH-nyckel. Du kommer fortfarande att behöva IP-adressen för den nod som du vill felsöka, som visas i det sista kommandot i det här avsnittet. Alternativt kan du [ansluta till Windows Server-noder med hjälp av RDP-anslutningar (Remote Desktop Protocol)][aks-windows-rdp] i stället för att använda SSH.

Om du vill lägga till SSH-nycklar till noderna i en skalnings uppsättning för virtuella datorer använder du kommandona [AZ VMSS Extension set][az-vmss-extension-set] och [AZ VMSS Update-instances][az-vmss-update-instances] .

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

Exemplet ovan använder *CLUSTER_RESOURCE_GROUP* och *SCALE_SET_NAME* variabler från föregående kommandon. Exemplet ovan använder även *~/.ssh/id_rsa. pub* som plats för din offentliga SSH-nyckel.

> [!NOTE]
> Som standard är användar namnet för AKS-noderna *azureuser*.

När du har lagt till din offentliga SSH-nyckel i skalnings uppsättningen kan du använda SSH i en virtuell nods dator i den skalnings uppsättningen med dess IP-adress. Visa de privata IP-adresserna för AKS-klusternoderna med [kommandot kubectl get][kubectl-get].

```console
kubectl get nodes -o wide
```

I följande exempel utdata visas de interna IP-adresserna för alla noder i klustret, inklusive en Windows Server-nod.

```console
$ kubectl get nodes -o wide

NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Registrera den interna IP-adressen för den nod som du vill felsöka.

Om du vill komma åt din nod med SSH följer du stegen i [skapa ssh-anslutningen](#create-the-ssh-connection).

## <a name="configure-virtual-machine-availability-set-based-aks-clusters-for-ssh-access"></a>Konfigurera tillgänglighets uppsättnings AKS kluster för virtuell dator för SSH-åtkomst

Om du vill konfigurera den virtuella datorns tillgänglighets uppsättnings AKS-kluster för SSH-åtkomst, letar du reda på namnet på klustrets Linux-nod och lägger till din offentliga SSH-nyckel till noden.

Använd kommandot [AZ AKS show][az-aks-show] för att hämta resurs grupps namnet för ditt AKS-kluster, därefter kommandot [AZ VM List][az-vm-list] för att lista det virtuella dator namnet för klustrets Linux-nod.

```azurecli-interactive
CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az vm list --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

Exemplet ovan tilldelar namnet på kluster resurs gruppen för *myAKSCluster* i *myResourceGroup* till *CLUSTER_RESOURCE_GROUP*. Exemplet använder sedan *CLUSTER_RESOURCE_GROUP* för att visa namnet på den virtuella datorn. I exempel resultatet visas namnet på den virtuella datorn:

```
Name                      ResourceGroup                                  Location
------------------------  ---------------------------------------------  ----------
aks-nodepool1-79590246-0  MC_myResourceGroupAKS_myAKSClusterRBAC_eastus  eastus
```

Om du vill lägga till SSH-nycklar till noden använder du kommandot [AZ VM User Update][az-vm-user-update] .

```azurecli-interactive
az vm user update \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --name aks-nodepool1-79590246-0 \
    --username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Exemplet ovan använder variabeln *CLUSTER_RESOURCE_GROUP* och nodens virtuella dator namn från föregående kommandon. Exemplet ovan använder även *~/.ssh/id_rsa. pub* som plats för din offentliga SSH-nyckel. Du kan också använda innehållet i den offentliga SSH-nyckeln i stället för att ange en sökväg.

> [!NOTE]
> Som standard är användar namnet för AKS-noderna *azureuser*.

När du har lagt till din offentliga SSH-nyckel till den virtuella noden i noden kan du använda SSH i den virtuella datorn med dess IP-adress. Visa den privata IP-adressen för en AKS-klusternod med kommandot [AZ VM List-IP-addresss][az-vm-list-ip-addresses] .

```azurecli-interactive
az vm list-ip-addresses --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

Exemplet ovan använder variabeln *CLUSTER_RESOURCE_GROUP* som angetts i föregående kommandon. Följande exempel på utdata visar de privata IP-adresserna för AKS-noderna:

```
VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-79590246-0  10.240.0.4
```

## <a name="create-the-ssh-connection"></a>Skapa SSH-anslutningen

Om du vill skapa en SSH-anslutning till en AKS-nod kör du en hjälp-Pod i ditt AKS-kluster. Med hjälp av den här Pod får du SSH-åtkomst till klustret och sedan ytterligare åtkomst till SSH-noden. Utför följande steg för att skapa och använda den här hjälp-pod:

1. Kör en `debian` behållar avbildning och koppla en terminalsession till den. Den här behållaren kan användas för att skapa en SSH-session med en nod i AKS-klustret:

    ```console
    kubectl run -it --rm aks-ssh --image=debian
    ```

    > [!TIP]
    > Om du använder Windows Server-noder lägger du till en Node-selektor till kommandot för att schemalägga behållaren Debian på en Linux-nod:
    >
    > ```console
    > kubectl run -it --rm aks-ssh --image=debian --overrides='{"apiVersion":"v1","spec":{"nodeSelector":{"beta.kubernetes.io/os":"linux"}}}'
    > ```

1. När Terminal-sessionen är ansluten till behållaren installerar du en SSH-klient med hjälp av `apt-get` :

    ```console
    apt-get update && apt-get install openssh-client -y
    ```

1. Öppna ett nytt terminalfönster, inte anslutet till din behållare, kopiera din privata SSH-nyckel till hjälp-pod. Den privata nyckeln används för att skapa SSH i AKS-noden. 

   Om det behövs ändrar du *~/.ssh/id_rsa* till platsen för din privata SSH-nyckel:

    ```console
    kubectl cp ~/.ssh/id_rsa $(kubectl get pod -l run=aks-ssh -o jsonpath='{.items[0].metadata.name}'):/id_rsa
    ```

1. Gå tillbaka till sessionen till din behållare, uppdatera behörigheterna för den kopierade `id_rsa` privata SSH-nyckeln så att den är skrivskyddad:

    ```console
    chmod 0600 id_rsa
    ```

1. Skapa en SSH-anslutning till AKS-noden. Igen är standard användar namnet för AKS-noder *azureuser*. Acceptera uppvarningen för att fortsätta med anslutningen eftersom SSH-nyckeln först är betrodd. Du tillhandahålls sedan med bash-prompten för AKS-noden:

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

När du är färdig, `exit` SSH-sessionen och `exit` den interaktiva container-sessionen. När den här behållarobjektet stängs tas Pod som används för SSH-åtkomst från AKS-klustret bort.

## <a name="next-steps"></a>Nästa steg

Om du behöver ytterligare felsöknings data kan du [Visa kubelet-loggarna][view-kubelet-logs] eller [Visa Kubernetes Master Node-loggarna][view-master-logs].

<!-- EXTERNAL LINKS -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-vm-list]: /cli/azure/vm#az-vm-list
[az-vm-user-update]: /cli/azure/vm/user#az-vm-user-update
[az-vm-list-ip-addresses]: /cli/azure/vm#az-vm-list-ip-addresses
[view-kubelet-logs]: kubelet-logs.md
[view-master-logs]: ./view-control-plane-logs.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-windows-rdp]: rdp.md
[ssh-nix]: ../virtual-machines/linux/mac-create-ssh-keys.md
[ssh-windows]: ../virtual-machines/linux/ssh-from-windows.md
[az-vmss-list]: /cli/azure/vmss#az-vmss-list
[az-vmss-extension-set]: /cli/azure/vmss/extension#az-vmss-extension-set
[az-vmss-update-instances]: /cli/azure/vmss#az-vmss-update-instances