---
title: Skapa ett virtuellt nätverk – snabbstart – Azure CLI
titlesuffix: Azure Virtual Network
description: I den här snabbstarten lär du dig att skapa ett virtuellt nätverk med hjälp av Azure CLI. Med ett virtuellt nätverk kan Azure-resurser kommunicera med varandra och med Internet.
author: KumudD
ms.service: virtual-network
ms.topic: quickstart
ms.date: 03/06/2021
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 407207c0dcb6270f08fb511a01e6e4e835b9fab9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776761"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-cli"></a>Snabbstart: Skapa ett virtuellt nätverk med hjälp av Azure CLI

Med ett virtuellt nätverk kan Azure-resurser, till exempel virtuella datorer, kommunicera privat med varandra och med Internet. 

I den här snabbstarten får du lära dig hur du skapar ett virtuellt nätverk. När du har skapat ett virtuellt nätverk distribuerar du två virtuella datorer i det virtuella nätverket. Sedan ansluter du till de virtuella datorerna från Internet och kommunicerar privat i det nya virtuella nätverket.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Den här snabbstarten kräver version 2.0.28 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-a-resource-group-and-a-virtual-network"></a>Skapa en resursgrupp och ett virtuellt nätverk

Innan du kan skapa ett virtuellt nätverk måste du skapa en resursgrupp som ska vara värd för det virtuella nätverket. Skapa en resursgrupp med [az group create](/cli/azure/group#az_group_create). I det här exemplet skapas en **resursgrupp med namnet CreateVNetQS-rg** på **platsen Eastus:**

```azurecli-interactive
az group create \
    --name CreateVNetQS-rg \
    --location eastus
```

Skapa ett virtuellt nätverk med kommandot [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). I det här exemplet skapas ett virtuellt standardnätverk **med namnet myVNet** med ett undernät med namnet **default**:

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

I det här exemplet skapas en virtuell dator **med namnet myVM1:**

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

Du använde `--no-wait` alternativet i föregående steg. Du kan skapa den andra virtuella datorn med namnet **myVM2.**

```azurecli-interactive
az vm create \
  --resource-group CreateVNetQS-rg \
  --name myVM2 \
  --image UbuntuLTS \
  --public-ip-address myPublicIP-myVM2 \
  --generate-ssh-keys
```

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

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

Hämta den offentliga IP-adressen **myVM2** med [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show):

```azurecli-interactive
az network public-ip show \
  --resource-group CreateVNetQS-rg  \
  --name myPublicIP-myVM2 \
  --query ipAddress \
  --output tsv
```

## <a name="connect-to-a-vm-from-the-internet"></a>Ansluta till en virtuell dator från Internet

I det här kommandot ersätter `<publicIpAddress>` du med den offentliga IP-adressen för den virtuella datorn **myVM2:**

```bash
ssh <publicIpAddress>
```

## <a name="communicate-between-vms"></a>Kommunicera mellan virtuella datorer

Ange följande kommando för att bekräfta privat kommunikation mellan de virtuella datorerna **myVM2** och **myVM1:**

```bash
ping myVM1 -c 4
```

Du får fyra svar från *10.0.0.4*.

Avsluta SSH-sessionen med den virtuella **datorn myVM2.**

## <a name="clean-up-resources"></a>Rensa resurser

När de inte behövs längre kan du använda [az group delete](/cli/azure/group#az_group_delete) för att ta bort resursgruppen och alla resurser som den innehåller:

```azurecli-interactive
az group delete \
    --name CreateVNetQS-rg \
    --yes
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten: 

* Du har skapat ett virtuellt standardnätverk och två virtuella datorer. 
* Du har anslutit till en virtuell dator från Internet och kommunicerat privat mellan de två virtuella datorerna.

Privat kommunikation mellan virtuella datorer är obegränsad i ett virtuellt nätverk. 

Gå vidare till nästa artikel om du vill veta mer om hur du konfigurerar olika typer av VM-nätverkskommunikation:
> [!div class="nextstepaction"]
> [Filtrera nätverkstrafik](tutorial-filter-network-traffic.md)
