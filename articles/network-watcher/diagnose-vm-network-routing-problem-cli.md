---
title: Diagnostisera problem med nätverksroutning för virtuella datorer – Azure CLI
titleSuffix: Azure Network Watcher
description: I den här artikeln får du lära dig hur du använder Azure CLI för att diagnostisera problem med nätverksroutning för virtuella datorer med hjälp av funktionen för nästa hopp i Azure Network Watcher.
services: network-watcher
documentationcenter: network-watcher
author: damendo
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 01/07/2021
ms.author: damendo
ms.custom: ''
ms.openlocfilehash: 2ca7a3b25b1355e21782c1d9f736d20a14cbd4ac
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785459"
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-cli"></a>Diagnostisera problem med nätverksroutning för virtuella datorer – Azure CLI

I den här artikeln distribuerar du en virtuell dator (VM) och kontrollerar sedan kommunikationen till en IP-adress och url. Du lär dig också hur du fastställer orsaken till ett kommunikationsfel och hur du löser problemet.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2.0 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad. 

- Azure CLI-kommandona i den här artikeln är formaterade för att köras i ett Bash-gränssnitt.

## <a name="create-a-vm"></a>Skapa en virtuell dator

Innan du kan skapa en virtuell dator måste du skapa en resursgrupp som innehåller den virtuella datorn. Skapa en resursgrupp med [az group create](/cli/azure/group#az_group_create). I följande exempel skapas en resursgrupp med *namnet myResourceGroup* på *platsen eastus:*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Skapa en virtuell dator med [az vm create](/cli/azure/vm#az_vm_create). Om det inte redan finns SSH-nycklar på en standardnyckelplats skapar kommandot dem. Om du vill använda en specifik uppsättning nycklar använder du alternativet `--ssh-key-value`. I följande exempel skapas en virtuell dator med namnet *myVm*:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image UbuntuLTS \
  --generate-ssh-keys
```

Det tar några minuter att skapa den virtuella datorn. Fortsätt inte med de återstående stegen förrän den virtuella datorn har skapats och Azure CLI returnerar utdata.

## <a name="test-network-communication"></a>Testa nätverkskommunikationen

Om du vill testa nätverkskommunikationen med Network Watcher måste du först aktivera en nätverks bevakare i den region där den virtuella dator som du vill testa finns i och sedan använda Network Watcher:s nästa hopp-funktion för att testa kommunikationen.

### <a name="enable-network-watcher"></a>Aktivera nätverksbevakare

Om du redan har aktiverat en nätverks bevakare i regionen USA, östra går du vidare [till Använd nästa hopp.](#use-next-hop) Använd kommandot [az network watcher configure](/cli/azure/network/watcher#az_network_watcher_configure) för att skapa en network watcher i regionen USA, östra:

```azurecli-interactive
az network watcher configure \
  --resource-group NetworkWatcherRG \
  --locations eastus \
  --enabled
```

### <a name="use-next-hop"></a>Använda funktionen för nästa hopp

Azure skapar automatiskt vägar till olika standardmål. Du kan skapa egna vägar som ersätter standardvägarna. Ibland kan egna vägar göra att kommunikationen misslyckas. Om du vill testa routningen från en virtuell dator använder du [az network watcher show-next-hop](/cli/azure/network/watcher#az_network_watcher_show_next_hop) för att fastställa nästa routningshopp när trafiken är avsedd för en viss adress.

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

Efter några sekunder informerar utdata dig om att **nextHopType** är **Internet** och att **routeTableId** är **System Route**. Det här resultatet visar att det finns en giltig väg till målet.

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

De utdata som returneras informerar **dig om att None** är **nextHopType** och att **routeTableId** också är **System Route**. Resultatet visar att det visserligen finns en giltig systemväg till målet, men att det inte finns något nästa hopp för att dirigera trafiken till målet.

## <a name="view-details-of-a-route"></a>Visa information om en väg

Om du vill analysera routningen ytterligare granskar du de effektiva vägarna för nätverksgränssnittet med [kommandot az network nic show-effective-route-table:](/cli/azure/network/nic#az_network_nic_show_effective_route_table)

```azurecli-interactive
az network nic show-effective-route-table \
  --resource-group myResourceGroup \
  --name myVmVMNic
```

Följande text ingår i de returnerade utdata:

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

När du använde kommandot för att testa utgående kommunikation till `az network watcher show-next-hop` 13.107.21.200 i Använd [nästa hopp](#use-next-hop)användes vägen med **addressPrefix** 0.0.0.0/0** för att dirigera trafik till adressen, eftersom ingen annan väg i utdata innehåller adressen. Som standard dirigeras alla adresser som inte anges inom adressprefixet till en annan väg till internet.

När du använde kommandot för att testa utgående kommunikation till `az network watcher show-next-hop` 172.31.0.100 visade resultatet att det inte fanns någon nästa hopptyp. I de returnerade utdata visas även följande text:

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

Som du ser i utdata från kommandot är nextHopType Ingen även om det finns en standardväg till `az network watcher nic show-effective-route-table` prefixet 172.16.0.0/12,  som innehåller adressen 172.31.0.100. Azure skapar en standardväg till 172.16.0.0/12 men anger inte en nästa hopptyp förrän det finns någon anledning till det. Om du till exempel har lagt till adressintervallet 172.16.0.0/12 i adressutrymmet för det virtuella nätverket ändrar Azure **nextHopType** till **Virtuellt** nätverk för vägen. En kontroll visar sedan **Virtuellt nätverk** som **nextHopType**.

## <a name="clean-up-resources"></a>Rensa resurser

När resurserna inte behövs längre kan du använda [az group delete](/cli/azure/group#az_group_delete) för att ta bort resursgruppen och alla relaterade resurser den innehåller:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du skapat en virtuell dator och diagnostiserat nätverksroutning från den virtuella datorn. Du har lärt dig att Azure skapar flera standardvägar och testat routning till två olika mål. Läs mer om [routning i Azure](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) och hur du [skapar egna vägar](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

För utgående VM-anslutningar kan du också fastställa svarstiden och tillåten och nekad nätverkstrafik mellan den virtuella datorn och en slutpunkt med hjälp Network Watcher funktionen för [felsökning av](network-watcher-connectivity-cli.md) anslutningar. Du kan övervaka kommunikationen mellan en virtuell dator och en slutpunkt, till exempel en IP-adress eller URL, över tid med hjälp Network Watcher funktionen för anslutningsövervakaren. Information om hur du gör finns [i Övervaka en nätverksanslutning.](connection-monitor.md)
