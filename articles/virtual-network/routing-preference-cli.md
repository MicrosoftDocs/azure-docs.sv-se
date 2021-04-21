---
title: Konfigurera routningsinställningen för en offentlig IP-adress med hjälp av Azure CLI
titlesuffix: Azure Virtual Network
description: Lär dig hur du skapar en offentlig IP-adress med inställningar för Internettrafikroutning med hjälp av Azure CLI.
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
ms.openlocfilehash: 58b91c105ed48617b64356904942f5ab6b461cda
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776563"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-azure-cli"></a>Konfigurera routningsinställningen för en offentlig IP-adress med hjälp av Azure CLI

Den här artikeln visar hur du konfigurerar routningsinställningar via Internetleverantörsnätverk **(internetalternativet)** för en offentlig IP-adress med Hjälp av Azure CLI. När du har skapat den offentliga IP-adressen kan du associera den med följande Azure-resurser för inkommande och utgående trafik till Internet:

* Virtuell dator
* Skaluppsättning för virtuella datorer
* Azure Kubernetes Service (AKS)
* Internetuppriktad lastbalanserare
* Application Gateway
* Azure Firewall

Som standard anges routningsinställningen för offentlig IP-adress till Microsofts globala nätverk för alla Azure-tjänster och kan associeras med alla Azure-tjänster.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2.0.49 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp
Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az_group_create). I följande exempel skapas en resursgrupp i **Azure-regionen USA,** östra:

```azurecli
  az group create --name myResourceGroup --location eastus
```
## <a name="create-a-public-ip-address"></a>Skapa en offentlig IP-adress

Skapa en offentlig IP-adress med routningsinställning av **Internettyp** med kommandot [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create), med formatet som visas nedan.

Följande kommando skapar en ny offentlig IP-adress med **Internetroutningsinställning** i **Azure-regionen USA,** östra.

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
>  För närvarande stöder routningsinställningarna endast offentliga IP-adresser för IPV4.

Du kan associera den offentliga IP-adress som skapades ovan med en virtuell [Windows-](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [Linux-dator.](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Använd CLI-avsnittet på självstudiesidan: Associera en offentlig [IP-adress](associate-public-ip-address-vm.md#azure-cli) till en virtuell dator för att associera den offentliga IP-adressen med den virtuella datorn. Du kan också associera den offentliga IP-adress som skapades ovan [med en Azure Load Balancer](../load-balancer/load-balancer-overview.md), genom att tilldela den till lastbalanseringskonfigurationen för **frontend.** Den offentliga IP-adressen fungerar som en belastningsutjämnad virtuell IP-adress (VIP).

## <a name="next-steps"></a>Nästa steg

- Läs mer om [routningsinställningar i offentliga IP-adresser.](routing-preference-overview.md) 
- [Konfigurera routningsinställningen för en virtuell dator med hjälp av Azure CLI.](configure-routing-preference-virtual-machine-cli.md)
