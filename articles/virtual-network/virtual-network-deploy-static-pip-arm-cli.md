---
title: Skapa en virtuell dator med en statisk offentlig IP-adress – Azure CLI | Microsoft Docs
description: Lär dig hur du skapar en virtuell dator med en statisk offentlig IP-adress med hjälp av Azure-kommandoradsgränssnittet (CLI).
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: 55bc21b0-2a45-4943-a5e7-8d785d0d015c
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: kumud
ms.openlocfilehash: f0f61cc4ef02033a2c21ce5acde68caea483e743
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790139"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-cli"></a>Skapa en virtuell dator med en statisk offentlig IP-adress med hjälp av Azure CLI

Du kan skapa en virtuell dator med en statisk offentlig IP-adress. Med en offentlig IP-adress kan du kommunicera med en virtuell dator från Internet. Tilldela en statisk offentlig IP-adress i stället för en dynamisk adress för att säkerställa att adressen aldrig ändras. Läs mer om [statiska offentliga IP-adresser.](./public-ip-addresses.md#allocation-method) Om du vill ändra en offentlig IP-adress som tilldelats till en befintlig virtuell dator från dynamisk till statisk, eller arbeta med privata IP-adresser, se Lägga till, ändra eller [ta bort IP-adresser.](virtual-network-network-interface-addresses.md) Offentliga IP-adresser har [en nominell](https://azure.microsoft.com/pricing/details/ip-addresses)avgift och det finns en [gräns](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) för hur många offentliga IP-adresser du kan använda per prenumeration.

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Du kan utföra följande steg från den lokala datorn eller med hjälp av Azure Cloud Shell. Om du vill använda den lokala datorn måste du kontrollera att [Azure CLI är installerat.](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) Om du vill Azure Cloud Shell väljer **du Prova i** det övre högra hörnet i valfri kommandoruta som följer. Den Cloud Shell loggar in dig på Azure.

1. Om du använder Cloud Shell går du vidare till steg 2. Öppna en kommandosession och logga in på Azure med `az login` .
2. Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az_group_create). I följande exempel skapas en resursgrupp i Azure-regionen USA, östra:

   ```azurecli-interactive
   az group create --name myResourceGroup --location eastus
   ```

3. Skapa en virtuell dator med kommandot [az vm create](/cli/azure/vm#az_vm_create). Alternativet `--public-ip-address-allocation=static` tilldelar en statisk offentlig IP-adress till den virtuella datorn. I följande exempel skapas en virtuell Ubuntu-dator med en statisk, grundläggande offentlig IP-adress för SKU med namnet *myPublicIpAddress:*

   ```azurecli-interactive
   az vm create \
     --resource-group myResourceGroup \
     --name myVM \
     --image UbuntuLTS \
     --admin-username azureuser \
     --generate-ssh-keys \
     --public-ip-address myPublicIpAddress \
     --public-ip-address-allocation static
   ```

   Om den offentliga IP-adressen måste vara en standard-SKU lägger du `--public-ip-sku Standard` till i föregående kommando. Läs mer om [SKU:er för offentliga IP-adresser.](./public-ip-addresses.md#sku) Om den virtuella datorn ska läggas till i serverpoolen för en offentlig Azure Load Balancer måste SKU:n för den virtuella datorns offentliga IP-adress matcha SKU:n för lastbalanseringsdatorns offentliga IP-adress. Mer information finns i [Azure Load Balancer](../load-balancer/skus.md).

4. Visa den tilldelade offentliga IP-adressen och bekräfta att den har skapats som en statisk, grundläggande SKU-adress med [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show):

   ```azurecli-interactive
   az network public-ip show \
     --resource-group myResourceGroup \
     --name myPublicIpAddress \
     --query [ipAddress,publicIpAllocationMethod,sku] \
     --output table
   ```

   Azure tilldelade en offentlig IP-adress från adresser som används i den region där du skapade den virtuella datorn. Du kan ladda ned listan över intervall (prefix) för [offentliga](https://www.microsoft.com/download/details.aspx?id=56519) Azure-moln och för Azure-moln för [amerikanska myndigheter](https://www.microsoft.com/download/details.aspx?id=57063), [Kina](https://www.microsoft.com/download/details.aspx?id=57062) eller [Tyskland](https://www.microsoft.com/download/details.aspx?id=57064).

> [!WARNING]
> Ändra inte IP-adressinställningarna i den virtuella datorns operativsystem. Operativsystemet känner inte till offentliga IP-adresser i Azure. Även om du kan lägga till inställningar för privata IP-adresser i operativsystemet rekommenderar vi att du inte gör det om det inte behövs, och inte förrän du har läst Lägg till en privat [IP-adress i ett operativsystem](virtual-network-network-interface-addresses.md#private).

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="clean-up-resources"></a>Rensa resurser

När resurserna inte behövs längre kan du använda [az group delete](/cli/azure/group#az_group_delete) för att ta bort resursgruppen och alla relaterade resurser den innehåller:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Nästa steg

- Läs mer om [offentliga IP-adresser](./public-ip-addresses.md#public-ip-addresses) i Azure
- Läs mer om alla inställningar [för offentliga IP-adresser](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Läs mer om [privata IP-adresser och](./private-ip-addresses.md) hur du tilldelar en [statisk privat IP-adress](virtual-network-network-interface-addresses.md#add-ip-addresses) till en virtuell Azure-dator
- Läs mer om hur du skapar [virtuella Linux-](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [och Windows-datorer](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
