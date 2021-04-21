---
title: Skapa en offentlig IP-adress – Azure CLI
description: Lär dig hur du skapar en offentlig IP-adress med Azure CLI
services: virtual-network
documentationcenter: na
author: blehr
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: blehr
ms.openlocfilehash: ff0dbf31f6f428b23e00f9366d55703416847b90
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767699"
---
# <a name="quickstart-create-a-public-ip-address-using-azure-cli"></a>Snabbstart: Skapa en offentlig IP-adress med Azure CLI

Den här artikeln visar hur du skapar en offentlig IP-adressresurs med hjälp av Azure CLI. Mer information om vilka resurser som detta kan associeras till, skillnaden mellan Basic- och Standard-SKU och annan relaterad information finns i [Offentliga IP-adresser.](./public-ip-addresses.md)  I det här exemplet fokuserar vi endast på IPv4-adresser. Mer information om IPv6-adresser finns i [IPv6 för Azure VNet](./ipv6-overview.md).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2.0.28 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

Skapa en resursgrupp med [az group create](/cli/azure/group#az_group_create) med namnet **myResourceGroup** på **platsen eastus2.**

```azurecli-interactive
  az group create \
    --name myResourceGroup \
    --location eastus2
```

## <a name="create-public-ip"></a>Skapa offentlig IP

---
# <a name="standard-sku---using-zones"></a>[**Standard-SKU – Använda zoner**](#tab/option-create-public-ip-standard-zones)

>[!NOTE]
>Följande kommando fungerar för API-version 2020-08-01 eller senare.  Mer information om den API-version som för närvarande används finns i [Resursproviders och typer.](../azure-resource-manager/management/resource-providers-and-types.md)

Använd [az network public-ip create för](/cli/azure/network/public-ip#az_network_public_ip_create) att skapa en standardzonredundant offentlig IP-adress med namnet **myStandardZRPublicIP** **i myResourceGroup**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardZRPublicIP \
    --sku Standard
    --zone 1 2 3
```
> [!IMPORTANT]
> För versioner av API:et som är äldre än 2020-08-01 kör du kommandot ovan utan att ange en zonparameter för att skapa en zonredundant IP-adress. 
>

Använd följande kommando för att skapa en zonindead offentlig IP-Zon 2 med namnet **myStandardZonalPublicIP** i **myResourceGroup:**

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myStandardZonalPublicIP \
    --sku Standard \
    --zone 2
```

Observera att ovanstående alternativ för zoner endast är giltiga val i regioner med [Tillgänglighetszoner](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

# <a name="standard-sku---no-zones"></a>[**Standard-SKU – Inga zoner**](#tab/option-create-public-ip-standard)

>[!NOTE]
>Följande kommando fungerar för API-version 2020-08-01 eller senare.  Mer information om den API-version som för närvarande används finns i [Resursproviders och typer.](../azure-resource-manager/management/resource-providers-and-types.md)

Använd [az network public-ip create för](/cli/azure/network/public-ip#az_network_public_ip_create) att skapa en offentlig IP-standardadress som en icke-zonindead resurs med namnet **myStandardPublicIP** **i myResourceGroup**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardPublicIP \
    --sku Standard
```
Det här valet är giltigt i alla regioner och är standardvalet för offentliga IP-standardadresser i regioner utan [Tillgänglighetszoner](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

# <a name="basic-sku"></a>[**Grundläggande SKU**](#tab/option-create-public-ip-basic)

Använd [az network public-ip create för](/cli/azure/network/public-ip#az_network_public_ip_create) att skapa en grundläggande statisk offentlig IP-adress med namnet **myBasicPublicIP** i **myResourceGroup**.  Grundläggande offentliga IP-adresser har inte begreppet tillgänglighetszoner.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myBasicPublicIP \
    --sku Basic \
    --allocation-method Static
```
Om IP-adressen kan ändras med tiden **kan** du välja dynamisk IP-tilldelning genom att ändra allokeringsmetoden till Dynamisk.

---

## <a name="additional-information"></a>Ytterligare information 

Mer information om de enskilda variabler som anges ovan finns i Hantera [offentliga IP-adresser.](./virtual-network-public-ip-address.md#create-a-public-ip-address)

## <a name="next-steps"></a>Nästa steg
- Associera en [offentlig IP-adress med en virtuell dator](./associate-public-ip-address-vm.md#azure-portal).
- Läs mer om [offentliga IP-adresser](./public-ip-addresses.md#public-ip-addresses) i Azure.
- Läs mer om alla inställningar [för offentliga IP-adresser.](virtual-network-public-ip-address.md#create-a-public-ip-address)
