---
title: RDP till AKS Windows Server-noder
titleSuffix: Azure Kubernetes Service
description: Lär dig hur du skapar en RDP-anslutning med Windows Server Azure Kubernetes Service klusternoder (AKS) för felsöknings- och underhållsuppgifter.
services: container-service
ms.topic: article
ms.date: 06/04/2019
ms.openlocfilehash: 62f29c0550b858e34d888da61f1bd7fbd358f82d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782935"
---
# <a name="connect-with-rdp-to-azure-kubernetes-service-aks-cluster-windows-server-nodes-for-maintenance-or-troubleshooting"></a>Ansluta med RDP till Azure Kubernetes Service (AKS) Windows Server-noder för underhåll eller felsökning

Under hela livscykeln för ditt Azure Kubernetes Service(AKS)-kluster kan du behöva komma åt en AKS Windows Server-nod. Den här åtkomsten kan vara för underhåll, logginsamling eller andra felsökningsåtgärder. Du kan komma åt AKS Windows Server-noder med RDP. Om du vill använda SSH för att få åtkomst till AKS Windows Server-noderna och du har åtkomst till samma nyckelpar som användes när klustret skapades kan du följa stegen i SSH till [AKS-klusternoder (Azure Kubernetes Service) i SSH.][ssh-steps] Av säkerhetsskäl exponeras inte AKS-noderna mot Internet.

Den här artikeln visar hur du skapar en RDP-anslutning med en AKS-nod med hjälp av deras privata IP-adresser.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster med en Windows Server-nod. Om du behöver ett AKS-kluster kan du läsa artikeln om hur du [skapar ett AKS-kluster med en Windows-container med hjälp av Azure CLI.][aks-windows-cli] Du behöver Windows-administratörens användarnamn och lösenord för den Windows Server-nod som du vill felsöka. Om du inte känner till dem kan du återställa dem genom att följa Fjärrskrivbordstjänster lösenord eller dess [administratörslösenord på en virtuell Windows-dator. ](/troubleshoot/azure/virtual-machines/reset-rdp) Du behöver också en RDP-klient, till [exempel Microsoft Fjärrskrivbord][rdp-mac].

Azure CLI version 2.0.61 eller senare måste också vara installerat och konfigurerat. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

## <a name="deploy-a-virtual-machine-to-the-same-subnet-as-your-cluster"></a>Distribuera en virtuell dator till samma undernät som klustret

Windows Server-noderna i ditt AKS-kluster har inte externt tillgängliga IP-adresser. Om du vill skapa en RDP-anslutning kan du distribuera en virtuell dator med en offentligt tillgänglig IP-adress till samma undernät som dina Windows Server-noder.

I följande exempel skapas en virtuell dator med namnet *myVM* i *resursgruppen myResourceGroup.*

Hämta först undernätet som används av din Windows Server-nodpool. För att hämta undernäts-ID:t behöver du namnet på undernätet. För att hämta namnet på undernätet behöver du namnet på det virtuella nätverket. Hämta namnet på det virtuella nätverket genom att fråga klustret efter listan över nätverk. Om du vill fråga klustret behöver du dess namn. Du kan hämta alla dessa genom att köra följande i Azure Cloud Shell:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
VNET_NAME=$(az network vnet list -g $CLUSTER_RG --query [0].name -o tsv)
SUBNET_NAME=$(az network vnet subnet list -g $CLUSTER_RG --vnet-name $VNET_NAME --query [0].name -o tsv)
SUBNET_ID=$(az network vnet subnet show -g $CLUSTER_RG --vnet-name $VNET_NAME --name $SUBNET_NAME --query id -o tsv)
```

Nu när du har SUBNET_ID kör du följande kommando i samma Azure Cloud Shell för att skapa den virtuella datorn:

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2019datacenter \
    --admin-username azureuser \
    --admin-password myP@ssw0rd12 \
    --subnet $SUBNET_ID \
    --query publicIpAddress -o tsv
```

Följande exempelutdata visar att den virtuella datorn har skapats och visar den virtuella datorns offentliga IP-adress.

```console
13.62.204.18
```

Registrera den virtuella datorns offentliga IP-adress. Du kommer att använda den här adressen i ett senare steg.

## <a name="allow-access-to-the-virtual-machine"></a>Tillåt åtkomst till den virtuella datorn

Undernät för AKS-nodpooler skyddas som standard med NSG:er (nätverkssäkerhetsgrupper). För att få åtkomst till den virtuella datorn måste du ha aktiverat åtkomst i NSG:n.

> [!NOTE]
> NSG:erna styrs av AKS-tjänsten. Ändringar som du gör i NSG:n skrivs när som helst över av kontrollplanet.
>

Hämta först resursgruppen och nsg-namnet för nsg för att lägga till regeln i:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NSG_NAME=$(az network nsg list -g $CLUSTER_RG --query [].name -o tsv)
```

Skapa sedan NSG-regeln:

```azurecli-interactive
az network nsg rule create --name tempRDPAccess --resource-group $CLUSTER_RG --nsg-name $NSG_NAME --priority 100 --destination-port-range 3389 --protocol Tcp --description "Temporary RDP access to Windows nodes"
```

## <a name="get-the-node-address"></a>Hämta nodadressen

För att hantera Kubernetes-kluster använder du [kubectl][kubectl], Kubernetes kommandoradsklient. Om du använder Azure Cloud Shell är `kubectl` redan installerat. För att installera `kubectl` lokalt använder du kommandot [az aks install-cli][az-aks-install-cli]:
    
```azurecli-interactive
az aks install-cli
```

För att konfigurera `kubectl` till att ansluta till ditt Kubernetes-kluster använder du kommandot [az aks get-credentials][az-aks-get-credentials]. Det här kommandot laddar ned autentiseringsuppgifter och konfigurerar Kubernetes CLI för att använda dem.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Visa en lista med den interna IP-adressen för Windows Server-noderna med [kommandot kubectl get:][kubectl-get]

```console
kubectl get nodes -o wide
```

Följande exempelutdata visar de interna IP-adresserna för alla noder i klustret, inklusive Windows Server-noderna.

```console
$ kubectl get nodes -o wide
NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Registrera den interna IP-adressen för den Windows Server-nod som du vill felsöka. Du kommer att använda den här adressen i ett senare steg.

## <a name="connect-to-the-virtual-machine-and-node"></a>Ansluta till den virtuella datorn och noden

Anslut till den offentliga IP-adressen för den virtuella dator som du skapade tidigare med hjälp av en RDP-klient, till [exempel Microsoft Fjärrskrivbord][rdp-mac].

![Bild av anslutning till den virtuella datorn med hjälp av en RDP-klient](media/rdp/vm-rdp.png)

När du har anslutit till den virtuella datorn ansluter du till den interna *IP-adressen* för den Windows Server-nod som du vill felsöka med hjälp av en RDP-klient från den virtuella datorn.

![Bild av anslutning till Windows Server-noden med hjälp av en RDP-klient](media/rdp/node-rdp.png)

Nu är du ansluten till din Windows Server-nod.

![Bild av cmd-fönstret i Windows Server-noden](media/rdp/node-session.png)

Nu kan du köra alla felsökningskommandon *i cmd-fönstret.* Eftersom Windows Server-noder använder Windows Server Core finns det inte något fullständigt grafiskt användargränssnitt eller andra GUI-verktyg när du ansluter till en Windows Server-nod via RDP.

## <a name="remove-rdp-access"></a>Ta bort RDP-åtkomst

När du är klar avslutar du RDP-anslutningen till Windows Server-noden och avslutar RDP-sessionen till den virtuella datorn. När du har avslutat båda RDP-sessionerna tar du bort den virtuella datorn med [kommandot az vm delete:][az-vm-delete]

```azurecli-interactive
az vm delete --resource-group myResourceGroup --name myVM
```

Och NSG-regeln:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NSG_NAME=$(az network nsg list -g $CLUSTER_RG --query [].name -o tsv)
```

```azurecli-interactive
az network nsg rule delete --resource-group $CLUSTER_RG --nsg-name $NSG_NAME --name tempRDPAccess
```

## <a name="next-steps"></a>Nästa steg

Om du behöver ytterligare felsökningsdata kan du visa [Kubernetes-huvudnodloggarna][view-master-logs] eller [Azure Monitor][azure-monitor-containers].

<!-- EXTERNAL LINKS -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[rdp-mac]: https://aka.ms/rdmac

<!-- INTERNAL LINKS -->
[aks-windows-cli]: windows-container-cli.md
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-vm-delete]: /cli/azure/vm#az_vm_delete
[azure-monitor-containers]: ../azure-monitor/containers/container-insights-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[ssh-steps]: ssh.md
[view-master-logs]: view-master-logs.md