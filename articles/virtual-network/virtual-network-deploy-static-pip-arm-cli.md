---
title: Skapa en virtuell dator med en statisk offentlig IP-adress – Azure CLI | Microsoft Docs
description: Lär dig hur du skapar en virtuell dator med en statisk offentlig IP-adress med hjälp av kommando rads gränssnittet för Azure (CLI).
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
ms.openlocfilehash: c4062dd086eeee712376a402da2792352fa3c3ae
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98221350"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-cli"></a>Skapa en virtuell dator med en statisk offentlig IP-adress med hjälp av Azure CLI

Du kan skapa en virtuell dator med en statisk offentlig IP-adress. Med en offentlig IP-adress kan du kommunicera med en virtuell dator från Internet. Tilldela en statisk offentlig IP-adress i stället för en dynamisk adress för att säkerställa att adressen aldrig ändras. Läs mer om [statiska offentliga IP-adresser](./public-ip-addresses.md#allocation-method). Om du vill ändra en offentlig IP-adress som tilldelats till en befintlig virtuell dator från dynamisk till statisk, eller om du vill arbeta med privata IP-adresser, se [Lägg till, ändra eller ta bort IP-](virtual-network-network-interface-addresses.md)adresser. Offentliga IP-adresser har en [nominell avgift](https://azure.microsoft.com/pricing/details/ip-addresses)och det finns en [gräns](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) för hur många offentliga IP-adresser som du kan använda per prenumeration.

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Du kan utföra följande steg från din lokala dator eller med hjälp av Azure Cloud Shell. Se till att du har [installerat Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)för att använda din lokala dator. Om du vill använda Azure Cloud Shell väljer du **testa det** i det övre högra hörnet i alla kommando rutor som följer. Cloud Shell signerar dig till Azure.

1. Om du använder Cloud Shell går du vidare till steg 2. Öppna en kommando-session och logga in på Azure med `az login` .
2. Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az-group-create). I följande exempel skapas en resurs grupp i Azure-regionen USA, östra:

   ```azurecli-interactive
   az group create --name myResourceGroup --location eastus
   ```

3. Skapa en virtuell dator med kommandot [az vm create](/cli/azure/vm#az-vm-create). `--public-ip-address-allocation=static`Alternativet tilldelar den virtuella datorn en statisk offentlig IP-adress. I följande exempel skapas en virtuell Ubuntu-dator med en statisk, grundläggande SKU offentlig IP-adress med namnet *myPublicIpAddress*:

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

   Om den offentliga IP-adressen måste vara en standard-SKU, lägger `--public-ip-sku Standard` du till föregående kommando. Lär dig mer om [offentliga IP-adresser SKU: er](./public-ip-addresses.md#sku). Om den virtuella datorn ska läggas till i backend-poolen för en offentlig Azure Load Balancer måste SKU: n för den virtuella datorns offentliga IP-adress matcha SKU: n för den offentliga IP-adressen för belastningsutjämnaren. Mer information finns i [Azure Load Balancer](../load-balancer/skus.md).

4. Visa den offentliga IP-adress som tilldelats och bekräfta att den har skapats som en statisk, grundläggande SKU-adress med [AZ Network Public-IP show](/cli/azure/network/public-ip#az-network-public-ip-show):

   ```azurecli-interactive
   az network public-ip show \
     --resource-group myResourceGroup \
     --name myPublicIpAddress \
     --query [ipAddress,publicIpAllocationMethod,sku] \
     --output table
   ```

   Azure tilldelade en offentlig IP-adress från adresser som används i den region där du skapade den virtuella datorn i. Du kan ladda ned listan över intervall (prefix) för [offentliga](https://www.microsoft.com/download/details.aspx?id=56519) Azure-moln och för Azure-moln för [amerikanska myndigheter](https://www.microsoft.com/download/details.aspx?id=57063), [Kina](https://www.microsoft.com/download/details.aspx?id=57062) eller [Tyskland](https://www.microsoft.com/download/details.aspx?id=57064).

> [!WARNING]
> Ändra inte inställningarna för IP-adress i den virtuella datorns operativ system. Operativ systemet är inte medvetna om offentliga Azure-IP-adresser. Även om du kan lägga till inställningar för privata IP-adresser i operativ systemet rekommenderar vi att du inte gör det om det inte behövs, och inte förrän du har läst [lägga till en privat IP-adress till ett operativ system](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Rensa resurser

När resurserna inte behövs längre kan du använda [az group delete](/cli/azure/group#az-group-delete) för att ta bort resursgruppen och alla relaterade resurser den innehåller:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Nästa steg

- Läs mer om [offentliga IP-adresser](./public-ip-addresses.md#public-ip-addresses) i Azure
- Läs mer om [Inställningar för offentliga IP-adresser](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Läs mer om [privata IP-adresser](./private-ip-addresses.md) och tilldelning av en [statisk privat IP-adress](virtual-network-network-interface-addresses.md#add-ip-addresses) till en virtuell Azure-dator
- Lär dig mer om att skapa virtuella [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -och [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -datorer
