---
title: Dirigera nätverkstrafik – Azure CLI-| Microsoft Docs
description: I den här artikeln får du lära dig hur du dirigerar nätverkstrafik med en vägtabell med hjälp av Azure CLI.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2ff643c39820fa529c8678c7a36881dd25da354c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762505"
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-cli"></a>Dirigera nätverkstrafik med en vägtabell med hjälp av Azure CLI

Azure dirigerar automatiskt trafik mellan alla undernät inom ett virtuella nätverk som standard. Du kan skapa egna vägar för att åsidosätta Azures standardroutning. Möjligheten att skapa anpassade vägar är användbar om du exempelvis vill dirigera trafik mellan undernät via en virtuell nätverksinstallation (NVA). I den här artikeln kan du se hur du:

* Skapa en routningstabell
* Skapa en väg
* Skapa ett virtuellt nätverk med flera undernät
* Associera en routningstabell till ett undernät
* Skapa en NVA som dirigerar trafik
* Distribuera virtuella datorer till olika undernät
* Dirigera trafik från ett undernät till ett annat via en NVA

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2.0.28 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-a-route-table"></a>Skapa en routningstabell

Innan du kan skapa en vägtabell skapar du en resursgrupp med [az group create för](/cli/azure/group) alla resurser som skapats i den här artikeln. 

```azurecli-interactive
# Create a resource group.
az group create \
  --name myResourceGroup \
  --location eastus
```

Skapa en vägtabell med [az network route-table create](/cli/azure/network/route-table#az_network_route_table_create). I följande exempel skapas en vägtabell med namnet *myRouteTablePublic*. 

```azurecli-interactive
# Create a route table
az network route-table create \
  --resource-group myResourceGroup \
  --name myRouteTablePublic
```

## <a name="create-a-route"></a>Skapa en väg

Skapa en väg i vägtabellen med [az network route-table route create](/cli/azure/network/route-table/route#az_network_route_table_route_create). 

```azurecli-interactive
az network route-table route create \
  --name ToPrivateSubnet \
  --resource-group myResourceGroup \
  --route-table-name myRouteTablePublic \
  --address-prefix 10.0.1.0/24 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address 10.0.2.4
```

## <a name="associate-a-route-table-to-a-subnet"></a>Associera en routningstabell till ett undernät

Innan du kan associera en routningstabell till ett undernät måste du skapa ett virtuellt nätverk och ett undernät. Skapa ett virtuellt nätverk med ett undernät med [az network vnet create](/cli/azure/network/vnet).

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

Skapa ytterligare två undernät med [az network vnet subnet create](/cli/azure/network/vnet/subnet).

```azurecli-interactive
# Create a private subnet.
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24

# Create a DMZ subnet.
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name DMZ \
  --address-prefix 10.0.2.0/24
```

Associera *vägtabellen myRouteTablePublic* med det *offentliga undernätet* med [az network vnet subnet update](/cli/azure/network/vnet/subnet).

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Public \
  --resource-group myResourceGroup \
  --route-table myRouteTablePublic
```

## <a name="create-an-nva"></a>Skapa en NVA

En NVA är en virtuell dator som utför en nätverksfunktion, som routning, brandvägg eller WAN-optimering.

Skapa en NVA i *DMZ-undernätet* med [az vm create](/cli/azure/vm). När du skapar en virtuell dator skapar och tilldelar Azure som standard en offentlig IP-adress till den virtuella datorn. Parametern instruerar Azure att inte skapa och tilldela en offentlig IP-adress till den virtuella datorn eftersom den virtuella datorn inte behöver vara `--public-ip-address ""` ansluten till från Internet. Om det inte redan finns SSH-nycklar på en standardnyckelplats skapar kommandot dem. Om du vill använda en specifik uppsättning nycklar använder du alternativet `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmNva \
  --image UbuntuLTS \
  --public-ip-address "" \
  --subnet DMZ \
  --vnet-name myVirtualNetwork \
  --generate-ssh-keys
```

Det tar några minuter att skapa den virtuella datorn. Fortsätt inte till nästa steg förrän Azure har skapat den virtuella datorn och returnerar utdata om den virtuella datorn. 

För att ett nätverksgränssnitt ska kunna vidarebefordra nätverkstrafik som skickas till det som inte är avsett för den egna IP-adressen måste IP-vidarebefordran vara aktiverat för nätverksgränssnittet. Aktivera IP-vidarebefordran för nätverksgränssnittet [med az network nic update](/cli/azure/network/nic).

```azurecli-interactive
az network nic update \
  --name myVmNvaVMNic \
  --resource-group myResourceGroup \
  --ip-forwarding true
```

I den virtuella datorn måste operativsystemet, eller ett program som körs i den virtuella datorn, kunna vidarebefordra nätverkstrafik. Aktivera IP-vidarebefordran i den virtuella datorns operativsystem med [az vm extension set](/cli/azure/vm/extension):

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVmNva \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
```

Det kan ta upp till en minut att köra kommandot.

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa två virtuella datorer i det virtuella nätverket  så att du kan  verifiera att trafik från det offentliga undernätet dirigeras till det privata undernätet via NVA i ett senare steg. 

Skapa en virtuell dator i *det offentliga* undernätet med az [vm create](/cli/azure/vm). Parametern `--no-wait` gör att Azure kan köra kommandot i bakgrunden så att du kan fortsätta till nästa kommando. För att effektivisera den här artikeln används ett lösenord. Nycklar används vanligtvis i produktionsdistributioner. Om du använder nycklar måste du också konfigurera vidarebefordran av SSH-agenten. Mer information finns i dokumentationen för SSH-klienten. Ersätt `<replace-with-your-password>` i följande kommando med val av lösenord.

```azurecli-interactive
adminPassword="<replace-with-your-password>"

az vm create \
  --resource-group myResourceGroup \
  --name myVmPublic \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --admin-username azureuser \
  --admin-password $adminPassword \
  --no-wait
```

Skapa en virtuell dator i *det privata* undernätet.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPrivate \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --admin-username azureuser \
  --admin-password $adminPassword
```

Det tar några minuter att skapa den virtuella datorn. När den virtuella datorn har skapats visar Azure CLI information som liknar följande exempel: 

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmPrivate",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.1.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Anteckna **publicIpAddress**. Den här adressen används för att komma åt den virtuella datorn från Internet i ett senare steg.

## <a name="route-traffic-through-an-nva"></a>Dirigera trafik via NVA

Använd följande kommando för att skapa en SSH-session med den virtuella *datorn myVmPrivate.* Ersätt *\<publicIpAddress>* med den offentliga IP-adressen för den virtuella datorn. I exemplet ovan är IP-adressen *13.90.242.231.*

```bash
ssh azureuser@<publicIpAddress>
```

När du uppmanas att ange ett lösenord anger du det lösenord som du valde [i Skapa virtuella datorer.](#create-virtual-machines)

Använd följande kommando för att installera spårningsvägen på den virtuella *datorn myVmPrivate:*

```bash
sudo apt-get install traceroute
```

Använd följande kommando för att testa routning för nätverkstrafik till *den virtuella datorn myVmPublic* från den virtuella *datorn myVmPrivate.*

```bash
traceroute myVmPublic
```

Svaret liknar följande exempel:

```output
traceroute to myVmPublic (10.0.0.4), 30 hops max, 60 byte packets
1  10.0.0.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```

Du ser att trafik vidarebefordras direkt från *myVmPrivate* till the *myVmPublic*. Azures standardvägar dirigerar trafik direkt mellan undernät. 

Använd följande kommando för att SSH till den virtuella *datorn myVmPublic* från den *virtuella datorn myVmPrivate:*

```bash
ssh azureuser@myVmPublic
```

Använd följande kommando för att installera spårningsvägen på den virtuella *datorn myVmPublic:*

```bash
sudo apt-get install traceroute
```

Använd följande kommando för att testa routning för nätverkstrafik till den virtuella *datorn myVmPrivate* från den *virtuella datorn myVmPublic.*

```bash
traceroute myVmPrivate
```

Svaret liknar följande exempel:

```output
traceroute to myVmPrivate (10.0.1.4), 30 hops max, 60 byte packets
1  10.0.2.4 (10.0.2.4)  0.781 ms  0.780 ms  0.775 ms
2  10.0.1.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```

Du kan se att det första hoppet är 10.0.2.4, som är NVA-enhetens privata IP-adress. Det andra hoppet är is 10.0.1.4, som är den privata IP-adressen för den virtuella datorn *myVmPrivate*. Vägen som har lagts till i routningstabellen *myRouteTablePublic* och associerats till det *offentliga* undernätet gjorde så att Azure dirigerade trafiken via NVA istället för direkt till det *privata* undernätet.

Stäng SSH-sessionerna till både de virtuella *datorerna myVmPublic* och *myVmPrivate.*

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs använder [du az group delete](/cli/azure/group) för att ta bort resursgruppen och alla resurser som den innehåller.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du skapat en vägtabell och kopplat den till ett undernät. Du har skapat en enkel NVA som dirigerade trafik från ett offentligt till ett privat undernät. Distribuera en mängd olika förkonfigurerade NVA-enheter som utför nätverksfunktioner som brandvägg och WAN-optimering från [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). Mer information om routning finns i [routningsöversikten](virtual-networks-udr-overview.md) och [Hantera en routningstabell](manage-route-table.md).

Du kan distribuera många Azure-resurser inom ett virtuellt nätverk, men resurser för vissa Azure PaaS-tjänster går inte att distribuera till ett virtuellt nätverk. Du kan fortfarande begränsa åtkomsten för resurserna i vissa Azure PaaS-tjänster till trafik enbart från ett undernät för ett virtuell dator. Mer information finns i Begränsa [nätverksåtkomsten till PaaS-resurser.](tutorial-restrict-network-access-to-resources-cli.md)
