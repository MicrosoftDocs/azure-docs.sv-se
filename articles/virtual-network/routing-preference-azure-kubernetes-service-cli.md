---
title: Konfigurera cirkulations inställningar för en Azure Kubernetes-tjänst med Azure CLI
titlesuffix: Azure Virtual Network
description: Lär dig hur du konfigurerar ett AKS-kluster med cirkulations inställningar med hjälp av Azure CLI.
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
ms.openlocfilehash: ac70f48a3c484f8865c54e09c59662a14a259e74
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101680341"
---
# <a name="configure-routing-preference-for-a-kubernetes-cluster-using-azure-cli"></a>Konfigurera cirkulations inställningar för ett Kubernetes-kluster med Azure CLI

Den här artikeln visar hur du konfigurerar inställningar för routning via Internet LEVERANTÖRs nätverk (**Internet** alternativ) för ett Kubernetes-kluster med Azure CLI. Inställningen routning anges genom att skapa en offentlig IP-adress för inställnings typen för routning * * Internet * * * * och sedan använda den när du skapar AKS-klustret.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2.0.49 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp
Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az-group-create). I följande exempel skapas en resurs grupp i Azure-regionen **USA, östra** :

```azurecli
  az group create --name myResourceGroup --location eastus
```
## <a name="create-a-public-ip-address"></a>Skapa en offentlig IP-adress

Skapa en offentlig IP-adress med Dirigerings inställning av **Internet** typ med kommandot [AZ Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create).

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

## <a name="get-the-id-of-public-ip-address"></a>Hämta ID för offentlig IP-adress

Följande kommando returnerar det offentliga IP-adress-ID som skapades i föregående avsnitt:
```azurecli
az network public-ip show \
--resource-group myResourceGroup \
--name myRoutingPrefIP \
--query id
```
## <a name="create-kubernetes-cluster-with-the-public-ip"></a>Skapa Kubernetes-kluster med den offentliga IP-adressen

Följande kommando skapar AKS-klustret med den offentliga IP-adress som skapades i föregående avsnitt:

```azurecli
az aks create \
--resource-group MyResourceGroup \
--name MyAKSCluster \
--load-balancer-outbound-ips "Enter the public IP ID from previous step" --generate-ssh-key
```

>[!NOTE]
>Det tar några minuter att distribuera AKS-klustret.

Om du vill validera söker du efter den offentliga IP-adressen som skapats i det tidigare steget i Azure Portal så visas IP-adressen som är kopplad till belastningsutjämnaren som är associerad med Kubernetes-klustret enligt nedan:

 ![Dirigerings inställningar offentlig IP för Kubernetes](./media/routing-preference-azure-kubernetes-service-cli/routing-preference-azure-kubernetes-service.png)


## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [cirkulations inställningar i offentliga IP-adresser](routing-preference-overview.md). 
- [Konfigurera Dirigerings inställningar för en virtuell dator med hjälp av Azure CLI](configure-routing-preference-virtual-machine-cli.md).

