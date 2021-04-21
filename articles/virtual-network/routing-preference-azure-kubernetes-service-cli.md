---
title: Konfigurera routningsinställning för en Azure Kubernetes-tjänst med hjälp av Azure CLI
titlesuffix: Azure Virtual Network
description: Lär dig hur du konfigurerar ett AKS-kluster med routningsinställningar med hjälp av Azure CLI.
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
ms.openlocfilehash: 9eaad12e254150109498be0fac2f285f33a5965c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776617"
---
# <a name="configure-routing-preference-for-a-kubernetes-cluster-using-azure-cli"></a>Konfigurera routningsinställning för ett Kubernetes-kluster med Hjälp av Azure CLI

Den här artikeln visar hur du konfigurerar routningsinställningar via INTERNET-nätverk **(internetalternativet)** för ett Kubernetes-kluster med Azure CLI. Routningsinställningarna anges genom att skapa en offentlig IP-adress av routningsinställningstypen **Internet*** och sedan använda den när du skapar AKS-klustret.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2.0.49 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp
Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az_group_create). I följande exempel skapas en resursgrupp i **Azure-regionen USA,** östra:

```azurecli
  az group create --name myResourceGroup --location eastus
```
## <a name="create-a-public-ip-address"></a>Skapa en offentlig IP-adress

Skapa en offentlig IP-adress med routningsinställning av **Internettyp** med kommandot [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create).

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

För att verifiera genom att söka efter den offentliga IP-adress som skapades i det tidigare steget i Azure Portal ser du att IP-adressen är associerad med lastbalanseraren som är associerad med Kubernetes-klustret enligt nedan:

 ![Offentlig IP-adress för routningsinställning för Kubernetes](./media/routing-preference-azure-kubernetes-service-cli/routing-preference-azure-kubernetes-service.png)


## <a name="next-steps"></a>Nästa steg

- Läs mer om [routningsinställningar i offentliga IP-adresser.](routing-preference-overview.md) 
- [Konfigurera routningsinställningen för en virtuell dator med hjälp av Azure CLI.](configure-routing-preference-virtual-machine-cli.md)
