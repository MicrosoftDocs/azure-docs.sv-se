---
title: Lägga till eller ta bort en under näts delegering i ett virtuellt Azure-nätverk
titlesuffix: Azure Virtual Network
description: Lär dig hur du lägger till eller tar bort ett delegerat undernät för en tjänst i Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2019
ms.author: kumud
ms.openlocfilehash: bc43dc7afb234d410eb17d20beb13cd5cb44bb18
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222538"
---
# <a name="add-or-remove-a-subnet-delegation"></a>Lägga till eller ta bort en under näts delegering

Under näts delegering ger explicita behörigheter till tjänsten för att skapa tjänstespecifika resurser i under nätet med en unik identifierare när tjänsten distribueras. Den här artikeln beskriver hur du lägger till eller tar bort ett delegerat undernät för en Azure-tjänst.

## <a name="portal"></a>Portalen

### <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

### <a name="create-the-virtual-network"></a>Skapa det virtuella nätverket

I det här avsnittet skapar du ett virtuellt nätverk och under nätet som du senare delegerar till en Azure-tjänst.

1. På den övre vänstra sidan av skärmen väljer du **skapa en resurs**  >  **nätverk**  >  **virtuellt nätverk**.
1. I **Skapa virtuellt nätverk** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange *MyVirtualNetwork*. |
    | Adressutrymme | Ange *10.0.0.0/16*. |
    | Prenumeration | Välj din prenumeration.|
    | Resursgrupp | Välj **Skapa ny**, ange *myResourceGroup* och välj sedan **OK**. |
    | Plats | Välj **öster**.|
    | Undernät – Namn | Ange *undernät*. |
    | Undernät – adressintervall | Ange *10.0.0.0/24*. |
    |||
1. Lämna resten som standard och välj sedan **skapa**.

### <a name="permissions"></a>Behörigheter

Om du inte skapade det undernät som du vill delegera till en Azure-tjänst behöver du följande behörighet: `Microsoft.Network/virtualNetworks/subnets/write` .

Den inbyggda rollen [nätverks deltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) innehåller även de behörigheter som krävs.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Delegera ett undernät till en Azure-tjänst

I det här avsnittet delegerar du det undernät som du skapade i föregående avsnitt till en Azure-tjänst.

1. I portalens sökfält anger du *myVirtualNetwork*. När **myVirtualNetwork** visas i sökresultatet väljer du det.
2. I Sök resultaten väljer du *myVirtualNetwork*.
3. Välj **undernät** under **Inställningar** och välj sedan **undernät**.
4. På sidan för **under näts Delegerings** listan *väljer du bland* de tjänster som listas under **delegera undernät till en tjänst** (till exempel **Microsoft. DBforPostgreSQL/serversv2**).  

### <a name="remove-subnet-delegation-from-an-azure-service"></a>Ta bort under näts delegering från en Azure-tjänst

1. I portalens sökfält anger du *myVirtualNetwork*. När **myVirtualNetwork** visas i sökresultatet väljer du det.
2. I Sök resultaten väljer du *myVirtualNetwork*.
3. Välj **undernät** under **Inställningar** och välj sedan **undernät**.
4. Välj **ingen** från tjänsterna som listas under **delegera undernät till en tjänst** för under **näts delegering** *på sidan för undernät.* 

## <a name="azure-cli"></a>Azure CLI

Förbered din miljö för Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Den här artikeln kräver version 2.0.28 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp
Skapa en resursgrupp med [az group create](/cli/azure/group). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

I följande exempel skapas en resurs grupp med namnet **myResourceGroup** på platsen för **öster** :

```azurecli-interactive

  az group create \
    --name myResourceGroup \
    --location eastus

```

### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk
Skapa det virtuella nätverket **myVnet** med undernätet **mySubnet** i **myResourceGroup** med [az network vnet create](/cli/azure/network/vnet).

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myVnet \
    --address-prefix 10.0.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 10.0.0.0/24
```
### <a name="permissions"></a>Behörigheter

Om du inte skapade det undernät som du vill delegera till en Azure-tjänst behöver du följande behörighet: `Microsoft.Network/virtualNetworks/subnets/write` .

Den inbyggda rollen [nätverks deltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) innehåller även de behörigheter som krävs.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Delegera ett undernät till en Azure-tjänst

I det här avsnittet delegerar du det undernät som du skapade i föregående avsnitt till en Azure-tjänst. 

Använd [AZ Network VNet Subnet Update](/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update) för att uppdatera under nätet med namnet **mitt undernät** med en delegering till en Azure-tjänst.  I det här exemplet används **Microsoft. DBforPostgreSQL/serversv2** för delegering av exempel:

```azurecli-interactive
  az network vnet subnet update \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --delegations Microsoft.DBforPostgreSQL/serversv2
```

Använd [AZ Network VNet Subnet show](/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-show)för att kontrol lera att delegeringen har tillämpats. Kontrol lera att tjänsten är delegerad till under nätet under egenskapen **serviceName**:

```azurecli-interactive
  az network vnet subnet show \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --query delegations
```

```json
[
  {
    "actions": [
      "Microsoft.Network/virtualNetworks/subnets/join/action"
    ],
    "etag": "W/\"8a8bf16a-38cf-409f-9434-fe3b5ab9ae54\"",
    "id": "/subscriptions/3bf09329-ca61-4fee-88cb-7e30b9ee305b/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet/delegations/0",
    "name": "0",
    "provisioningState": "Succeeded",
    "resourceGroup": "myResourceGroup",
    "serviceName": "Microsoft.DBforPostgreSQL/serversv2",
    "type": "Microsoft.Network/virtualNetworks/subnets/delegations"
  }
]
```

### <a name="remove-subnet-delegation-from-an-azure-service"></a>Ta bort under näts delegering från en Azure-tjänst

Använd [AZ Network VNet Subnet Update](/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update) för att ta bort delegeringen från under nätet med namnet **subsubnet**:

```azurecli-interactive
  az network vnet subnet update \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --remove delegations
```
Om du vill kontrol lera att delegeringen har tagits bort använder du [AZ Network VNet Subnet show](/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-show). Kontrol lera att tjänsten har tagits bort från under nätet under egenskapen **serviceName**:

```azurecli-interactive
  az network vnet subnet show \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --query delegations
```
Utdata från kommandot är en null-klammer:
```json
[]
```

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="connect-to-azure"></a>Anslut till Azure

```azurepowershell-interactive
  Connect-AzAccount
```

### <a name="create-a-resource-group"></a>Skapa en resursgrupp
Skapa en resursgrupp med [New-AzResourceGroup](/cli/azure/group). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

I följande exempel skapas en resurs grupp med namnet *myResourceGroup* på platsen för *öster* :

```azurepowershell-interactive
  New-AzResourceGroup -Name myResourceGroup -Location eastus
```
### <a name="create-virtual-network"></a>Skapa det virtuella nätverket

Skapa ett virtuellt nätverk med namnet **myVnet** med ett undernät med namnet mina **undernät** med [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) i **myResourceGroup** med [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork?view=latest). IP-adressutrymmet för det virtuella nätverket är **10.0.0.0/16**. Under nätet i det virtuella nätverket är **10.0.0.0/24**.  

```azurepowershell-interactive
  $subnet = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix "10.0.0.0/24"

  New-AzVirtualNetwork -Name myVnet -ResourceGroupName myResourceGroup -Location eastus -AddressPrefix "10.0.0.0/16" -Subnet $subnet
```
### <a name="permissions"></a>Behörigheter

Om du inte skapade det undernät som du vill delegera till en Azure-tjänst behöver du följande behörighet: `Microsoft.Network/virtualNetworks/subnets/write` .

Den inbyggda rollen [nätverks deltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) innehåller även de behörigheter som krävs.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Delegera ett undernät till en Azure-tjänst

I det här avsnittet delegerar du det undernät som du skapade i föregående avsnitt till en Azure-tjänst. 

Använd [Add-AzDelegation](/powershell/module/az.network/add-azdelegation?view=latest) för att uppdatera under nätet med namnet mina **undernät** med en delegering som heter **delegering** till en Azure-tjänst.  I det här exemplet används **Microsoft. DBforPostgreSQL/serversv2** för delegering av exempel:

```azurepowershell-interactive
  $vnet = Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName "myResourceGroup"
  $subnet = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $vnet
  $subnet = Add-AzDelegation -Name "myDelegation" -ServiceName "Microsoft.DBforPostgreSQL/serversv2" -Subnet $subnet
  Set-AzVirtualNetwork -VirtualNetwork $vnet
```
Använd [Get-AzDelegation](/powershell/module/az.network/get-azdelegation?view=latest) för att verifiera delegeringen:

```azurepowershell-interactive
  $subnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup" | Get-AzVirtualNetworkSubnetConfig -Name "mySubnet"
  Get-AzDelegation -Name "myDelegation" -Subnet $subnet

  ProvisioningState : Succeeded
  ServiceName       : Microsoft.DBforPostgreSQL/serversv2
  Actions           : {Microsoft.Network/virtualNetworks/subnets/join/action}
  Name              : myDelegation
  Etag              : W/"9cba4b0e-2ceb-444b-b553-454f8da07d8a"
  Id                : /subscriptions/3bf09329-ca61-4fee-88cb-7e30b9ee305b/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet/delegations/myDelegation

```
### <a name="remove-subnet-delegation-from-an-azure-service"></a>Ta bort under näts delegering från en Azure-tjänst

Använd [Remove-AzDelegation](/powershell/module/az.network/remove-azdelegation?view=latest) för att ta bort delegeringen från under nätet med namnet **subsubnet**:

```azurepowershell-interactive
  $vnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup"
  $subnet = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $vnet
  $subnet = Remove-AzDelegation -Name "myDelegation" -Subnet $subnet
  Set-AzVirtualNetwork -VirtualNetwork $vnet
```
Använd [Get-AzDelegation](/powershell/module/az.network/get-azdelegation?view=latest) för att verifiera att delegeringen har tagits bort:

```azurepowershell-interactive
  $subnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup" | Get-AzVirtualNetworkSubnetConfig -Name "mySubnet"
  Get-AzDelegation -Name "myDelegation" -Subnet $subnet

  Get-AzDelegation: Sequence contains no matching element

```

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du [hanterar undernät i Azure](virtual-network-manage-subnet.md).