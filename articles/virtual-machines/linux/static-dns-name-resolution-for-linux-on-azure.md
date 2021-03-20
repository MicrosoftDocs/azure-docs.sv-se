---
title: Använd intern DNS för matchning av namn på virtuella datorer med Azure CLI
description: Så här skapar du nätverkskort för virtuella nätverkskort och använder interna DNS för namn matchning för virtuella datorer på Azure med Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/16/2017
ms.author: cynthn
ms.openlocfilehash: 3d68ac7aa9927e62011c58b17139d7232ce4a10c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98200761"
---
# <a name="create-virtual-network-interface-cards-and-use-internal-dns-for-vm-name-resolution-on-azure"></a>Skapa nätverkskort för virtuella nätverkskort och Använd interna DNS för namn matchning för virtuella datorer på Azure

Den här artikeln visar hur du ställer in statiska interna DNS-namn för virtuella Linux-datorer med hjälp av virtuella nätverks gränssnitts kort (virtuella nätverkskort) och DNS-etikett namn med Azure CLI. Statiska DNS-namn används för permanenta infrastruktur tjänster som en Jenkins build-Server, som används för det här dokumentet eller en git-Server.

Kraven är:

* [ett Azure-konto](https://azure.microsoft.com/pricing/free-trial/)
* [offentliga och privata SSH-nyckelfiler](mac-create-ssh-keys.md)

## <a name="quick-commands"></a>Snabbkommandon
Om du snabbt behöver utföra uppgiften kan du använda följande avsnitt för att få information om de kommandon som behövs. Mer detaljerad information och kontext för varje steg finns i resten av dokumentet, med [början här](#detailed-walkthrough). För att utföra de här stegen måste du ha den senaste versionen av [Azure CLI](/cli/azure/install-az-cli2) installerat och inloggad på ett Azure-konto med [AZ-inloggning](/cli/azure/reference-index).

Före krav: resurs grupp, virtuellt nätverk och undernät, nätverks säkerhets grupp med SSH inkommande.

### <a name="create-a-virtual-network-interface-card-with-a-static-internal-dns-name"></a>Skapa ett virtuellt nätverks gränssnitts kort med ett statiskt internt DNS-namn
Skapa vNic med [AZ Network NIC Create](/cli/azure/network/nic). `--internal-dns-name`CLI-flaggan är för att ställa in DNS-etiketten, som tillhandahåller det statiska DNS-namnet för det virtuella nätverkskortet (vNic). I följande exempel skapas en vNic med namnet `myNic` , ansluter den till det `myVnet` virtuella nätverket och skapar en intern DNS-namnserver som heter `jenkins` :

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

### <a name="deploy-a-vm-and-connect-the-vnic"></a>Distribuera en virtuell dator och Anslut vNic
Skapa en virtuell dator med [az vm create](/cli/azure/vm). `--nics`Flaggan ansluter vNic till den virtuella datorn under distributionen till Azure. I följande exempel skapas en virtuell dator med namnet `myVM` med Azure Managed disks och bifogas vNic med namnet `myNic` från föregående steg:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="detailed-walkthrough"></a>Detaljerad genomgång

En fullständig infrastruktur för kontinuerlig integrering och kontinuerlig distribution (CiCd) i Azure kräver att vissa servrar är statiska eller långvariga servrar. Vi rekommenderar att Azure-till gångar som virtuella nätverk och nätverks säkerhets grupper är statiska och långvariga resurser som sällan distribueras. När ett virtuellt nätverk har distribuerats kan det återanvändas av nya distributioner utan att något skadligt påverkar infrastrukturen. Du kan senare lägga till en git-lagringsplats eller en Jenkins Automation-server som levererar CiCd till det här virtuella nätverket för dina utvecklings-eller test miljöer.  

Interna DNS-namn kan bara matchas i ett virtuellt Azure-nätverk. Eftersom DNS-namnen är interna kan de inte matchas mot den externa Internet, vilket ger ytterligare säkerhet i infrastrukturen.

Ersätt exempel parameter namn med dina egna värden i följande exempel. Exempel på parameter namn är `myResourceGroup` , `myNic` och `myVM` .

## <a name="create-the-resource-group"></a>Skapa resursgruppen
Skapa först en resurs grupp med [AZ Group Create](/cli/azure/group). I följande exempel skapas en resursgrupp med namnet `myResourceGroup` på platsen `westus`:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-the-virtual-network"></a>Skapa det virtuella nätverket

Nästa steg är att bygga ett virtuellt nätverk för att starta de virtuella datorerna i. Det virtuella nätverket innehåller ett undernät för den här genom gången. Mer information om virtuella Azure-nätverk finns i [skapa ett virtuellt nätverk](../../virtual-network/manage-virtual-network.md#create-a-virtual-network). 

Skapa det virtuella nätverket med [AZ Network VNet Create](/cli/azure/network/vnet). I följande exempel skapas ett virtuellt nätverk med namnet `myVnet` och under nätet med namnet `mySubnet` :

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-the-network-security-group"></a>Skapa nätverks säkerhets gruppen
Azure nätverks säkerhets grupper motsvarar en brand vägg på nätverks nivån. Mer information om nätverks säkerhets grupper finns i [så här skapar du NSG: er i Azure CLI](../../virtual-network/tutorial-filter-network-traffic-cli.md). 

Skapa nätverks säkerhets gruppen med [AZ Network NSG Create](/cli/azure/network/nsg). I följande exempel skapas en nätverks säkerhets grupp med namnet `myNetworkSecurityGroup` :

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="add-an-inbound-rule-to-allow-ssh"></a>Lägg till en regel för inkommande trafik som tillåter SSH
Lägg till en regel för inkommande trafik för nätverks säkerhets gruppen med [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule). I följande exempel skapas en regel med namnet `myRuleAllowSSH` :

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myRuleAllowSSH \
    --protocol tcp \
    --direction inbound \
    --priority 1000 \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 22 \
    --access allow
```

## <a name="associate-the-subnet-with-the-network-security-group"></a>Koppla under nätet till nätverks säkerhets gruppen
Om du vill koppla under nätet till nätverks säkerhets gruppen använder du [AZ Network VNet Subnet Update](/cli/azure/network/vnet/subnet). I följande exempel associeras under nätets namn `mySubnet` med nätverks säkerhets gruppen med namnet `myNetworkSecurityGroup` :

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```


## <a name="create-the-virtual-network-interface-card-and-static-dns-names"></a>Skapa det virtuella nätverks gränssnitts kortet och statiska DNS-namn
Azure är mycket flexibelt, men om du vill använda DNS-namn för VM-namnmatchning måste du skapa virtuella nätverks gränssnitts kort (virtuella nätverkskort) som innehåller en DNS-etikett. Virtuella nätverkskort är viktiga eftersom du kan återanvända dem genom att ansluta dem till olika virtuella datorer över infrastrukturens livs cykel. Den här metoden behåller vNic som en statisk resurs medan de virtuella datorerna kan vara tillfälliga. Genom att använda DNS-märkning på vNic kan vi aktivera enkel namn matchning från andra virtuella datorer i det virtuella nätverket. Med hjälp av matchade namn kan andra virtuella datorer få åtkomst till Automation-servern via DNS-namnet `Jenkins` eller git-servern som `gitrepo` .  

Skapa vNic med [AZ Network NIC Create](/cli/azure/network/nic). I följande exempel skapas en vNic med namnet `myNic` , ansluter den till det `myVnet` virtuella nätverket med namnet `myVnet` och skapar en intern DNS-namnserver som heter `jenkins` :

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

## <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>Distribuera den virtuella datorn till infrastrukturen för virtuellt nätverk
Nu har vi ett virtuellt nätverk och undernät, en nätverks säkerhets grupp som fungerar som en brand vägg för att skydda vårt undernät genom att blockera all inkommande trafik utom port 22 för SSH, och en vNic. Nu kan du distribuera en virtuell dator i den här befintliga nätverks infrastrukturen.

Skapa en virtuell dator med [az vm create](/cli/azure/vm). I följande exempel skapas en virtuell dator med namnet `myVM` med Azure Managed disks och bifogas vNic med namnet `myNic` från föregående steg:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Genom att använda CLI-flaggorna för att anropa befintliga resurser instruerar vi Azure att distribuera den virtuella datorn i det befintliga nätverket. För att upprepa igen när ett VNet och ett undernät har distribuerats kan de lämnas som statiska eller permanenta resurser i din Azure-region.  

## <a name="next-steps"></a>Nästa steg
* [Skapa en egen anpassad miljö för en virtuell Linux-dator med hjälp av Azure CLI-kommandon](create-cli-complete.md)
* [Skapa en virtuell Linux-dator i Azure med hjälp av mallar](create-ssh-secured-vm-from-template.md)
