---
title: Konfigurera cirkulations inställningar för en offentlig IP-adress med hjälp av Azure CLI
titlesuffix: Azure Virtual Network
description: Lär dig hur du skapar en offentlig IP-adress med en Internet trafik cirkulations inställning med hjälp av Azure CLI.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: mnayak
ms.openlocfilehash: 2291767c162953f8339fb8cc27e55b96290ef795
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101665948"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-azure-cli"></a>Konfigurera cirkulations inställningar för en offentlig IP-adress med hjälp av Azure CLI

Den här artikeln visar hur du konfigurerar inställningar för routning via Internet LEVERANTÖRs nätverk (**Internet** alternativ) för en offentlig IP-adress med hjälp av Azure CLI. När du har skapat den offentliga IP-adressen kan du associera den med följande Azure-resurser för inkommande och utgående trafik till Internet:

* Virtuell dator
* Skaluppsättning för virtuella datorer
* Azure Kubernetes Service (AKS)
* Belastningsutjämnare mot Internet
* Application Gateway
* Azure Firewall

Som standard är cirkulations inställningen för offentlig IP-adress inställd på Microsofts globala nätverk för alla Azure-tjänster och kan associeras med valfri Azure-tjänst.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2.0.49 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp
Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az-group-create). I följande exempel skapas en resurs grupp i Azure-regionen **USA, östra** :

```azurecli
  az group create --name myResourceGroup --location eastus
```
## <a name="create-a-public-ip-address"></a>Skapa en offentlig IP-adress

Skapa en offentlig IP-adress med Dirigerings inställning av **Internet** typ med kommandot [AZ Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create), med formatet som visas nedan.

Följande kommando skapar en ny offentlig IP-adress med inställningar för **Internet** routning i Azure-regionen **USA, östra** .

```azurecli
az network public-ip create \
--name MyRoutingPrefIP \
--resource-group MyResourceGroup \
--location eastus \
--ip-tags 'RoutingPreference=Internet' \
--sku STANDARD \
--allocation-method static \
--version IPv4
```

> [!NOTE]
>  Inställningen routning stöder för närvarande endast IPV4 offentliga IP-adresser.

Du kan associera ovanstående skapade offentliga IP-adress med en virtuell [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -eller [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -dator. Använd avsnittet CLI på sidan självstudie: [associera en offentlig IP-adress till en virtuell dator](associate-public-ip-address-vm.md#azure-cli) för att koppla den offentliga IP-adressen till den virtuella datorn. Du kan också associera den offentliga IP-adressen som skapats ovan med en [Azure Load Balancer](../load-balancer/load-balancer-overview.md), genom att tilldela den till konfiguration av belastningsutjämnarens **klient** del. Den offentliga IP-adressen fungerar som en belastningsutjämnad virtuell IP-adress (VIP).

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [cirkulations inställningar i offentliga IP-adresser](routing-preference-overview.md). 
- [Konfigurera Dirigerings inställningar för en virtuell dator med hjälp av Azure CLI](configure-routing-preference-virtual-machine-cli.md).

