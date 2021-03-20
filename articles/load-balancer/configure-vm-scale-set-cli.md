---
title: Konfigurera skalnings uppsättning för virtuell dator med en befintlig Azure Load Balancer – Azure CLI
description: Lär dig hur du konfigurerar en skalnings uppsättning för virtuella datorer med en befintlig Azure Load Balancer med hjälp av Azure CLI.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: how-to
ms.date: 03/25/2020
ms.openlocfilehash: a60a6889217ce6ca8dccd5ebf5ee74b8f67a7757
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94518217"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-the-azure-cli"></a>Konfigurera en skalnings uppsättning för virtuella datorer med en befintlig Azure Load Balancer med hjälp av Azure CLI

I den här artikeln får du lära dig hur du konfigurerar en skalnings uppsättning för virtuella datorer med en befintlig Azure Load Balancer.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar 

- Du behöver en befintlig SKU för standard-SKU i prenumerationen där den virtuella datorns skalnings uppsättning ska distribueras.

- Du behöver ett Azure-Virtual Network för skalnings uppsättningen för den virtuella datorn.
 
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Den här artikeln kräver version 2.0.28 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="deploy-a-virtual-machine-scale-set-with-existing-load-balancer"></a>Distribuera en skalnings uppsättning för virtuell dator med befintlig belastningsutjämnare

Ersätt värdena inom hakparenteser med namnen på resurserna i konfigurationen.

```azurecli-interactive
az vmss create \
    --resource-group <resource-group> \
    --name <vmss-name>\
    --image <your-image> \
    --admin-username <admin-username> \
    --generate-ssh-keys  \
    --upgrade-policy-mode Automatic \
    --instance-count 3 \
    --vnet-name <virtual-network-name> \
    --subnet <subnet-name> \
    --lb <load-balancer-name> \
    --backend-pool-name <backend-pool-name>
```

I exemplet nedan distribueras en skalnings uppsättning för virtuella datorer med:

- Skalnings uppsättning för virtuell dator med namnet **myVMSS**
- Azure Load Balancer med namnet **myLoadBalancer**
- Backend-pool för belastningsutjämnare med namnet **myBackendPool**
- Azure Virtual Network med namnet **myVnet**
- Undernät med namnet mina **undernät**
- Resurs grupp med namnet **myResourceGroup**
- Ubuntu Server-avbildning för den virtuella datorns skal uppsättning

```azurecli-interactive
az vmss create \
    --resource-group myResourceGroup \
    --name myVMSS \
    --image Canonical:UbuntuServer:18.04-LTS:latest \
    --admin-username adminuser \
    --generate-ssh-keys \
    --upgrade-policy-mode Automatic \
    --instance-count 3 \
    --vnet-name myVnet\
    --subnet mySubnet \
    --lb myLoadBalancer \
    --backend-pool-name myBackendPool
```
> [!NOTE]
> När skalnings uppsättningen har skapats kan Server dels porten inte ändras för en belastnings Utjämnings regel som används av en hälso avsökning av belastningsutjämnaren. Om du vill ändra porten kan du ta bort hälso avsökningen genom att uppdatera skalnings uppsättningen för den virtuella Azure-datorn, uppdatera porten och sedan konfigurera hälso avsökningen igen.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du distribuerat en skalnings uppsättning för virtuella datorer med en befintlig Azure Load Balancer.  Mer information om skalnings uppsättningar för virtuella datorer och belastningsutjämnare finns i:

- [Vad är Azure Load Balancer?](load-balancer-overview.md)
- [Vad är skalningsuppsättningar för virtuella datorer?](../virtual-machine-scale-sets/overview.md)
                                