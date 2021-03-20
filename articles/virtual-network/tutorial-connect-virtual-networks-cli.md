---
title: Ansluta virtuella nätverk med VNet-peering – Azure CLI
description: I den här artikeln får du lära dig hur du ansluter virtuella nätverk med peering av virtuella nätverk med hjälp av Azure CLI.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
tags: azure-resource-manager
Customer intent: I want to connect two virtual networks so that virtual machines in one virtual network can communicate with virtual machines in the other virtual network.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9c6399e437fa314aa82e0b41cbf8a170ea3ab72e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94741800"
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-the-azure-cli"></a>Ansluta virtuella nätverk med peering för virtuella nätverk med hjälp av Azure CLI

Du kan ansluta virtuella nätverk till varandra med peerkoppling. När virtuella nätverk har peerkopplats kan resurser i båda virtuella nätverken kommunicera med varandra, med samma svarstid och bandbredd som om resurserna fanns i samma virtuella nätverk. I den här artikeln kan du se hur du:

* Skapa två virtuella nätverk
* Koppla samman två virtuella nätverk med en peerkoppling
* Distribuera en virtuell dator (VM) till varje virtuellt nätverk
* Kommunicera mellan virtuella datorer

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2.0.28 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-virtual-networks"></a>Skapa virtuella nätverk

Innan du skapar ett virtuellt nätverk måste du skapa en resurs grupp för det virtuella nätverket och alla andra resurser som skapas i den här artikeln. Skapa en resursgrupp med [az group create](/cli/azure/group). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Skapa ett virtuellt nätverk med kommandot [az network vnet create](/cli/azure/network/vnet). I följande exempel skapas ett virtuellt nätverk med namnet *myVirtualNetwork1* med adressprefixet *10.0.0.0/16*.

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.0.0.0/24
```

Skapa ett virtuellt nätverk med namnet *myVirtualNetwork2* med adressprefixet *10.1.0.0/16*:

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --address-prefixes 10.1.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.1.0.0/24
```

## <a name="peer-virtual-networks"></a>Peerkoppla virtuella nätverk

Peering upprättas mellan virtuella nätverks-ID: n, så du måste först hämta ID för varje virtuellt nätverk med [AZ Network VNet show](/cli/azure/network/vnet) och lagra ID: t i en variabel.

```azurecli-interactive
# Get the id for myVirtualNetwork1.
vNet1Id=$(az network vnet show \
  --resource-group myResourceGroup \
  --name myVirtualNetwork1 \
  --query id --out tsv)

# Get the id for myVirtualNetwork2.
vNet2Id=$(az network vnet show \
  --resource-group myResourceGroup \
  --name myVirtualNetwork2 \
  --query id \
  --out tsv)
```

Skapa en peering från *myVirtualNetwork1* till *myVirtualNetwork2* med [AZ Network VNet peering Create](/cli/azure/network/vnet/peering). Om `--allow-vnet-access` parametern inte anges upprättas en peering, men ingen kommunikation kan flöda genom den.

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --remote-vnet $vNet2Id \
  --allow-vnet-access
```

I utdata som returneras efter att föregående kommando körts, ser du att **peeringState** har *initierats*. Peer-kopplingen förblir i det *initierade* läget tills du skapar peer-kopplingen från *myVirtualNetwork2* till *myVirtualNetwork1*. Skapa en peering från *myVirtualNetwork2* till *myVirtualNetwork1*. 

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork2-myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork2 \
  --remote-vnet $vNet1Id \
  --allow-vnet-access
```

I utdata som returneras efter att föregående kommando körts, ser du att **peeringState** är *ansluten*. Azure ändrade också peering-statusen för *myVirtualNetwork1-myVirtualNetwork2-* peering till *ansluten*. Bekräfta att peering-statusen för *myVirtualNetwork1-myVirtualNetwork2-* peering har ändrats till *ansluten* med [AZ Network VNet peering show](/cli/azure/network/vnet/peering).

```azurecli-interactive
az network vnet peering show \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --query peeringState
```

Resurser i ett virtuellt nätverk kan inte kommunicera med resurser i det andra virtuella nätverket förrän **peeringState** för peering i båda de virtuella nätverken är *anslutna*. 

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa en virtuell dator i varje virtuellt nätverk så att du kan kommunicera mellan dem i ett senare steg.

### <a name="create-the-first-vm"></a>Skapa den första virtuella datorn

Skapa en virtuell dator med [az vm create](/cli/azure/vm). I följande exempel skapas en virtuell dator med namnet *myVm1* i det virtuella *myVirtualNetwork1* -nätverket. Om det inte redan finns SSH-nycklar på en standardnyckelplats skapar kommandot dem. Om du vill använda en specifik uppsättning nycklar använder du alternativet `--ssh-key-value`. `--no-wait`Alternativet skapar den virtuella datorn i bakgrunden, så att du kan fortsätta till nästa steg.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork1 \
  --subnet Subnet1 \
  --generate-ssh-keys \
  --no-wait
```

### <a name="create-the-second-vm"></a>Skapa den andra virtuella datorn

Skapa en virtuell dator i det virtuella *myVirtualNetwork2* -nätverket.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork2 \
  --subnet Subnet1 \
  --generate-ssh-keys
```

Det tar några minuter att skapa den virtuella datorn. När den virtuella datorn har skapats visar Azure CLI information som liknar följande exempel: 

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm2",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.1.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Anteckna **publicIpAddress**. Den här adressen används för att komma åt den virtuella datorn från Internet i ett senare steg.

## <a name="communicate-between-vms"></a>Kommunicera mellan virtuella datorer

Använd följande kommando för att skapa en SSH-session med den virtuella datorn *myVm2* . Ersätt `<publicIpAddress>` med den offentliga IP-adressen för den virtuella datorn. I föregående exempel är den offentliga IP-adressen *13.90.242.231*.

```bash
ssh <publicIpAddress>
```

Pinga den virtuella datorn i *myVirtualNetwork1*.

```bash
ping 10.0.0.4 -c 4
```

Du får fyra svar. 

Stäng SSH-sessionen till den virtuella *myVm2* -datorn. 

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs använder du [AZ Group Delete](/cli/azure/group) för att ta bort resurs gruppen och alla resurser den innehåller.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du ansluter två nätverk i samma Azure-region, med peering av virtuella nätverk. Du kan också peerkoppla virtuella nätverk i olika [regioner som stöds](virtual-network-manage-peering.md#cross-region) och i [olika Azure-prenumerationer](create-peering-different-subscriptions.md#cli), samt skapa [nav- och ekernätverksdesigner](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) med peerkoppling. Mer information om peerkoppling av virtuella nätverk finns i [Översikt över peerkoppling av virtuella nätverk](virtual-network-peering-overview.md) och [Hantera peerkopplingar av virtuella nätverk](virtual-network-manage-peering.md).

Du kan [ansluta din egen dator till ett virtuellt nätverk](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) via en VPN-anslutning och interagera med resurser i ett virtuellt nätverk eller i peer-kopplade virtuella nätverk. För återanvändbara skript för att utföra många av de uppgifter som beskrivs i de virtuella nätverks artiklarna, se [skript exempel](cli-samples.md).
