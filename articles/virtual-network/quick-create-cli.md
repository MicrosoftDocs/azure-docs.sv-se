---
title: Skapa ett virtuellt nätverk – snabbstart – Azure CLI
titlesuffix: Azure Virtual Network
description: I den här snabb starten lär du dig att skapa ett virtuellt nätverk med hjälp av Azure CLI. Ett virtuellt nätverk gör att Azure-resurser kan kommunicera med varandra och med Internet.
author: KumudD
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.service: virtual-network
ms.topic: quickstart
ms.date: 03/06/2021
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3f4cd0a09c64c8c89116bf3a7dec40bae9f05f71
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102199075"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-cli"></a>Snabbstart: Skapa ett virtuellt nätverk med hjälp av Azure CLI

Med ett virtuellt nätverk kan Azure-resurser, till exempel virtuella datorer, kommunicera privat med varandra och med Internet. 

I den här snabbstarten får du lära dig hur du skapar ett virtuellt nätverk. När du har skapat ett virtuellt nätverk distribuerar du två virtuella datorer i det virtuella nätverket. Sedan ansluter du till de virtuella datorerna från Internet och kommunicerar privat i det nya virtuella nätverket.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Den här snabb starten kräver version 2.0.28 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-a-resource-group-and-a-virtual-network"></a>Skapa en resursgrupp och ett virtuellt nätverk

Innan du kan skapa ett virtuellt nätverk måste du skapa en resursgrupp som ska vara värd för det virtuella nätverket. Skapa en resursgrupp med [az group create](/cli/azure/group#az_group_create). I det här exemplet skapas en resurs grupp med namnet **CreateVNetQS-RG** på platsen för **öster** :

```azurecli-interactive
az group create \
    --name CreateVNetQS-rg \
    --location eastus
```

Skapa ett virtuellt nätverk med kommandot [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). I det här exemplet skapas ett virtuellt standard nätverk med namnet **myVNet** med ett undernät som heter **default**:

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group CreateVNetQS-rg \
  --subnet-name default
```

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa två virtuella datorer i det virtuella nätverket.

### <a name="create-the-first-vm"></a>Skapa den första virtuella datorn

Skapa en virtuell dator med [az vm create](/cli/azure/vm#az_vm_create). 

Om det inte redan finns SSH-nycklar på en standardnyckelplats skapar kommandot dem. Om du vill använda en specifik uppsättning nycklar använder du alternativet `--ssh-key-value`. 

Alternativet `--no-wait` skapar den virtuella datorn i bakgrunden. Du kan fortsätta till nästa steg. 

I det här exemplet skapas en virtuell dator med namnet **myVM1**:

```azurecli-interactive
az vm create \
  --resource-group CreateVNetQS-rg \
  --name myVM1 \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --public-ip-address myPublicIP-myVM1 \
  --no-wait
```

### <a name="create-the-second-vm"></a>Skapa den andra virtuella datorn

Du har använt `--no-wait` alternativet i föregående steg. Du kan gå vidare och skapa den andra virtuella datorn med namnet **myVM2**.

```azurecli-interactive
az vm create \
  --resource-group CreateVNetQS-rg \
  --name myVM2 \
  --image UbuntuLTS \
  --public-ip-address myPublicIP-myVM2 \
  --generate-ssh-keys
```

### <a name="azure-cli-output-message"></a>Utdatameddelande från Azure CLI

Det tar några minuter att skapa de virtuella datorerna. När Azure skapar de virtuella datorerna, returnerar Azure CLI utdata som liknar detta:

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/CreateVNetQS-rg/providers/Microsoft.Compute/virtualMachines/myVM2",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.5",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "CreateVNetQS-rg"
  "zones": ""
}
```

## <a name="vm-public-ip"></a>Offentlig IP-adress för virtuell dator

Hämta den offentliga IP- **myVM2** genom att använda [AZ Network Public-IP show](/cli/azure/network/public-ip#az-network-public-ip-show):

```azurecli-interactive
az network public-ip show \
  --resource-group CreateVNetQS-rg  \
  --name myPublicIP-myVM2 \
  --query ipAddress \
  --output tsv
```

## <a name="connect-to-a-vm-from-the-internet"></a>Ansluta till en virtuell dator från Internet

I det här kommandot ersätter `<publicIpAddress>` du med den offentliga IP-adressen för din virtuella **myVM2** -dator:

```bash
ssh <publicIpAddress>
```

## <a name="communicate-between-vms"></a>Kommunicera mellan virtuella datorer

För att bekräfta privat kommunikation mellan **myVM2** -och **myVM1** -VM: ar, anger du följande kommando:

```bash
ping myVM1 -c 4
```

Du får fyra svar från *10.0.0.4*.

Avsluta SSH-sessionen med den virtuella **myVM2** -datorn.

## <a name="clean-up-resources"></a>Rensa resurser

När de inte behövs längre kan du använda [az group delete](/cli/azure/group#az_group_delete) för att ta bort resursgruppen och alla resurser som den innehåller:

```azurecli-interactive
az group delete \
    --name CreateVNetQS-rg \
    --yes
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten: 

* Du har skapat ett virtuellt standard nätverk och två virtuella datorer. 
* Du har anslutit till en virtuell dator från Internet och kommunicerat privat mellan de två virtuella datorerna.

Privat kommunikation mellan virtuella datorer är obegränsad i ett virtuellt nätverk. 

Fortsätt till nästa artikel om du vill lära dig mer om att konfigurera olika typer av nätverkskommunikation för virtuella datorer:
> [!div class="nextstepaction"]
> [Filtrera nätverkstrafik](tutorial-filter-network-traffic.md)
