---
title: Diagnostisera ett problem med Routning av VM-nätverk – Azure CLI
titleSuffix: Azure Network Watcher
description: I den här artikeln får du lära dig hur du använder Azure CLI för att diagnostisera ett problem med nätverks routning i en virtuell dator med hjälp av nästa hopp funktion i Azure Network Watcher.
services: network-watcher
documentationcenter: network-watcher
author: damendo
editor: ''
tags: azure-resource-manager
Customer intent: I need to diagnose virtual machine (VM) network routing problem that prevents communication to different destinations.
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 01/07/2021
ms.author: damendo
ms.custom: ''
ms.openlocfilehash: 5dac16e5cc1e88d833bf6d3c2660570fcf8b8e9e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102216963"
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-cli"></a>Diagnostisera ett problem med nätverks routning för virtuella datorer – Azure CLI

I den här artikeln distribuerar du en virtuell dator (VM) och kontrollerar sedan kommunikationen med en IP-adress och URL. Du lär dig också hur du fastställer orsaken till ett kommunikationsfel och hur du löser problemet.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2,0 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad. 

- Azure CLI-kommandona i den här artikeln är formaterade för att köras i ett bash-gränssnitt.

## <a name="create-a-vm"></a>Skapa en virtuell dator

Innan du kan skapa en virtuell dator måste du skapa en resursgrupp som innehåller den virtuella datorn. Skapa en resursgrupp med [az group create](/cli/azure/group#az-group-create). I följande exempel skapas en resurs grupp med namnet *myResourceGroup* på platsen för *öster* :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Skapa en virtuell dator med [az vm create](/cli/azure/vm#az-vm-create). Om det inte redan finns SSH-nycklar på en standardnyckelplats skapar kommandot dem. Om du vill använda en specifik uppsättning nycklar använder du alternativet `--ssh-key-value`. I följande exempel skapas en virtuell dator med namnet *myVm*:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image UbuntuLTS \
  --generate-ssh-keys
```

Det tar några minuter att skapa den virtuella datorn. Fortsätt inte med återstående steg förrän den virtuella datorn har skapats och Azure CLI returnerar utdata.

## <a name="test-network-communication"></a>Testa nätverkskommunikationen

Om du vill testa nätverkskommunikation med Network Watcher måste du först aktivera en nätverks bevakare i den region som den virtuella datorn som du vill testa är i och sedan använda Network Watchers nästa hopp-funktion för att testa kommunikationen.

### <a name="enable-network-watcher"></a>Aktivera nätverksbevakare

Om du redan har en nätverks övervakare aktive rad i regionen USA, östra, hoppar du över till [Använd nästa hopp](#use-next-hop). Använd [AZ Network Watcher Configure](/cli/azure/network/watcher#az-network-watcher-configure) för att skapa en nätverks övervakare i regionen USA, östra:

```azurecli-interactive
az network watcher configure \
  --resource-group NetworkWatcherRG \
  --locations eastus \
  --enabled
```

### <a name="use-next-hop"></a>Använda funktionen för nästa hopp

Azure skapar automatiskt vägar till olika standardmål. Du kan skapa egna vägar som ersätter standardvägarna. Ibland kan egna vägar göra att kommunikationen misslyckas. Om du vill testa routning från en virtuell dator använder du [AZ Network Watcher show-Next-hop](/cli/azure/network/watcher#az-network-watcher-show-next-hop) för att fastställa nästa Dirigerings hopp när trafiken är avsedd för en speciell adress.

Testa utgående kommunikation från den virtuella datorn till någon av IP-adresserna för www.bing.com:

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 13.107.21.200 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVm \
  --nic myVmVMNic \
  --out table
```

Efter några sekunder informerar utdata om att **nextHopType** är **Internet** och att **routeTableId** är **system väg**. Det innebär att du vet att det finns en giltig väg till målet.

Testa utgående kommunikation från den virtuella datorn till 172.31.0.100:

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 172.31.0.100 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVm \
  --nic myVmVMNic \
  --out table
```

Utdata som returneras informerar dig om att **ingen** är **NextHopType** och att **routeTableId** också är **system väg**. Resultatet visar att det visserligen finns en giltig systemväg till målet, men att det inte finns något nästa hopp för att dirigera trafiken till målet.

## <a name="view-details-of-a-route"></a>Visa information om en väg

Om du vill analysera routning ytterligare granskar du de effektiva vägarna för nätverks gränssnittet med kommandot [AZ Network NIC show-effektiv-Route-Table](/cli/azure/network/nic#az-network-nic-show-effective-route-table) :

```azurecli-interactive
az network nic show-effective-route-table \
  --resource-group myResourceGroup \
  --name myVmVMNic
```

Följande text ingår i returnerade utdata:

```console
{
  "additionalProperties": {
    "disableBgpRoutePropagation": false
  },
  "addressPrefix": [
    "0.0.0.0/0"
  ],
  "name": null,
  "nextHopIpAddress": [],
  "nextHopType": "Internet",
  "source": "Default",
  "state": "Active"
},
```

När du använde `az network watcher show-next-hop` kommandot för att testa utgående kommunikation till 13.107.21.200 i [Använd nästa hopp](#use-next-hop)användes vägen med **addressPrefix** 0.0.0.0/0 * * för att dirigera trafik till adressen, eftersom ingen annan väg i utdata innehåller adressen. Som standard dirigeras alla adresser som inte anges inom adressprefixet till en annan väg till internet.

När du använde `az network watcher show-next-hop` kommandot för att testa utgående kommunikation till 172.31.0.100, informeras du om att det inte fanns någon nästa hopp typ. I returnerade utdata visas även följande text:

```console
{
  "additionalProperties": {
    "disableBgpRoutePropagation": false
      },
  "addressPrefix": [
    "172.16.0.0/12"
  ],
  "name": null,
  "nextHopIpAddress": [],
  "nextHopType": "None",
  "source": "Default",
  "state": "Active"
},
```

Som du kan se i utdata från `az network watcher nic show-effective-route-table` kommandot, men det finns en standard väg till prefixet 172.16.0.0/12, som innehåller 172.31.0.100-adressen, är **nextHopType** **none**. Azure skapar en standardväg till 172.16.0.0/12 men anger inte en nästa hopptyp förrän det finns någon anledning till det. Om du till exempel har lagt till adress intervallet 172.16.0.0/12 i det virtuella nätverkets adress utrymme ändrar Azure **nextHopType** till det **virtuella nätverket** för vägen. En kontroll visar sedan det **virtuella nätverket** som **nextHopType**.

## <a name="clean-up-resources"></a>Rensa resurser

När resurserna inte behövs längre kan du använda [az group delete](/cli/azure/group#az-group-delete) för att ta bort resursgruppen och alla relaterade resurser den innehåller:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du skapat en virtuell dator och en diagnostiserad nätverks dirigering från den virtuella datorn. Du har lärt dig att Azure skapar flera standardvägar och testat routning till två olika mål. Läs mer om [routning i Azure](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) och hur du [skapar egna vägar](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

För utgående VM-anslutningar kan du också bestämma svars tiden och tillåten och nekad nätverks trafik mellan den virtuella datorn och en slut punkt med hjälp av Network Watcher [anslutnings fel söknings](network-watcher-connectivity-cli.md) funktion. Du kan övervaka kommunikationen mellan en virtuell dator och en slut punkt, till exempel en IP-adress eller URL, med tiden med hjälp av funktionen för Network Watcher anslutnings övervakaren. Mer information finns i [övervaka en nätverks anslutning](connection-monitor.md).
