---
title: Lägga till eller ta bort en undernätsdelegering i ett virtuellt Azure-nätverk
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
ms.openlocfilehash: 401124ed4b2794d891ca224ba3dc1c78edcae8d5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783421"
---
# <a name="add-or-remove-a-subnet-delegation"></a>Lägga till eller ta bort en undernätsdelegering

Undernätsdelegering ger explicit behörighet till tjänsten för att skapa tjänstspecifika resurser i undernätet med hjälp av en unik identifierare när tjänsten distribueras. I den här artikeln beskrivs hur du lägger till eller tar bort ett delegerat undernät för en Azure-tjänst.

## <a name="portal"></a>Portalen

### <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

### <a name="create-the-virtual-network"></a>Skapa det virtuella nätverket

I det här avsnittet skapar du ett virtuellt nätverk och det undernät som du senare ska delegera till en Azure-tjänst.

1. Längst upp till vänster på skärmen väljer du Skapa **en resurs**  >  **Virtuellt nätverk**  >  .
1. I **Skapa virtuellt nätverk** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange *MyVirtualNetwork*. |
    | Adressutrymme | Ange *10.0.0.0/16*. |
    | Prenumeration | Välj din prenumeration.|
    | Resursgrupp | Välj **Skapa ny**, ange *myResourceGroup* och välj sedan **OK**. |
    | Location | Välj EastUS ( **USA, östra).**|
    | Undernät – Namn | Ange *mySubnet*. |
    | Undernät – adressintervall | Ange *10.0.0.0/24*. |
    |||
1. Lämna resten som standard och välj sedan **Skapa.**

### <a name="permissions"></a>Behörigheter

Om du inte skapade det undernät som du vill delegera till en Azure-tjänst behöver du följande behörighet: `Microsoft.Network/virtualNetworks/subnets/write` .

Den inbyggda rollen [Nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) innehåller också de behörigheter som krävs.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Delegera ett undernät till en Azure-tjänst

I det här avsnittet delegerar du det undernät som du skapade i föregående avsnitt till en Azure-tjänst.

1. I portalens sökfält anger du *myVirtualNetwork*. När **myVirtualNetwork** visas i sökresultatet väljer du det.
2. I sökresultaten väljer du *myVirtualNetwork*.
3. Välj **Undernät** under **INSTÄLLNINGAR** och välj sedan **mySubnet**.
4. På sidan *mySubnet* för  listan Delegering av undernät väljer du bland de tjänster som listas under Delegera undernät till en **tjänst** (till exempel **Microsoft.DBforPostgreSQL/serversv2).**  

### <a name="remove-subnet-delegation-from-an-azure-service"></a>Ta bort undernätsdelegering från en Azure-tjänst

1. I portalens sökfält anger du *myVirtualNetwork*. När **myVirtualNetwork** visas i sökresultatet väljer du det.
2. I sökresultaten väljer du *myVirtualNetwork*.
3. Välj **Undernät** under **INSTÄLLNINGAR** och välj sedan **mySubnet**.
4. På *sidan mySubnet* går du till **listan Undernätsdelegering** och väljer **Ingen** från de tjänster som visas under **Delegera undernät till en tjänst**. 

## <a name="azure-cli"></a>Azure CLI

Förbered din miljö för Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Den här artikeln kräver version 2.0.28 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp
Skapa en resursgrupp med [az group create](/cli/azure/group). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

I följande exempel skapas en resursgrupp med **namnet myResourceGroup** på **platsen eastus:**

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

Den inbyggda rollen [Nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) innehåller också de behörigheter som krävs.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Delegera ett undernät till en Azure-tjänst

I det här avsnittet delegerar du det undernät som du skapade i föregående avsnitt till en Azure-tjänst. 

Använd [az network vnet subnet update för](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) att uppdatera undernätet med namnet **mySubnet med** en delegering till en Azure-tjänst.  I det här **exemplet används Microsoft.DBforPostgreSQL/serversv2** för exempeldelegeringen:

```azurecli-interactive
  az network vnet subnet update \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --delegations Microsoft.DBforPostgreSQL/serversv2
```

Kontrollera att delegeringen tillämpades med [az network vnet subnet show](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_show). Kontrollera att tjänsten har delegerats till undernätet under egenskapen **serviceName**:

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

### <a name="remove-subnet-delegation-from-an-azure-service"></a>Ta bort undernätsdelegering från en Azure-tjänst

Använd [az network vnet subnet update för](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) att ta bort delegeringen från undernätet med namnet **mySubnet**:

```azurecli-interactive
  az network vnet subnet update \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --remove delegations
```
Kontrollera att delegeringen har tagits bort med [az network vnet subnet show](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_show). Kontrollera att tjänsten tas bort från undernätet under egenskapen **serviceName**:

```azurecli-interactive
  az network vnet subnet show \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --query delegations
```
Utdata från kommandot är en hakparentes med null-värden:
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

I följande exempel skapas en resursgrupp med *namnet myResourceGroup* på *platsen eastus:*

```azurepowershell-interactive
  New-AzResourceGroup -Name myResourceGroup -Location eastus
```
### <a name="create-virtual-network"></a>Skapa det virtuella nätverket

Skapa ett virtuellt nätverk med namnet **myVnet** med ett undernät med namnet **mySubnet** med [new-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) i **myResourceGroup** med [hjälp av New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). IP-adressutrymmet för det virtuella nätverket är **10.0.0.0/16.** Undernätet i det virtuella nätverket är **10.0.0.0/24.**  

```azurepowershell-interactive
  $subnet = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix "10.0.0.0/24"

  New-AzVirtualNetwork -Name myVnet -ResourceGroupName myResourceGroup -Location eastus -AddressPrefix "10.0.0.0/16" -Subnet $subnet
```
### <a name="permissions"></a>Behörigheter

Om du inte skapade det undernät som du vill delegera till en Azure-tjänst behöver du följande behörighet: `Microsoft.Network/virtualNetworks/subnets/write` .

Den inbyggda rollen [Nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) innehåller också de behörigheter som krävs.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Delegera ett undernät till en Azure-tjänst

I det här avsnittet delegerar du det undernät som du skapade i föregående avsnitt till en Azure-tjänst. 

Använd [Add-AzDelegation för](/powershell/module/az.network/add-azdelegation) att uppdatera undernätet med namnet **mySubnet** med en delegering med namnet **myDelegation** till en Azure-tjänst.  I det här **exemplet används Microsoft.DBforPostgreSQL/serversv2** för exempeldelegeringen:

```azurepowershell-interactive
  $vnet = Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName "myResourceGroup"
  $subnet = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $vnet
  $subnet = Add-AzDelegation -Name "myDelegation" -ServiceName "Microsoft.DBforPostgreSQL/serversv2" -Subnet $subnet
  Set-AzVirtualNetwork -VirtualNetwork $vnet
```
Använd [Get-AzDelegation för](/powershell/module/az.network/get-azdelegation) att verifiera delegeringen:

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
### <a name="remove-subnet-delegation-from-an-azure-service"></a>Ta bort undernätsdelegering från en Azure-tjänst

Använd [Remove-AzDelegation för](/powershell/module/az.network/remove-azdelegation) att ta bort delegeringen från undernätet med namnet **mySubnet**:

```azurepowershell-interactive
  $vnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup"
  $subnet = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $vnet
  $subnet = Remove-AzDelegation -Name "myDelegation" -Subnet $subnet
  Set-AzVirtualNetwork -VirtualNetwork $vnet
```
Använd [Get-AzDelegation för att](/powershell/module/az.network/get-azdelegation) kontrollera att delegeringen har tagits bort:

```azurepowershell-interactive
  $subnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup" | Get-AzVirtualNetworkSubnetConfig -Name "mySubnet"
  Get-AzDelegation -Name "myDelegation" -Subnet $subnet

  Get-AzDelegation: Sequence contains no matching element

```

## <a name="next-steps"></a>Nästa steg
- Lär dig hur [du hanterar undernät i Azure](virtual-network-manage-subnet.md).
