---
title: Filtrera nätverks trafik – Azure CLI | Microsoft Docs
description: I den här artikeln får du lära dig hur du filtrerar nätverks trafik till ett undernät med en nätverks säkerhets grupp med hjälp av Azure CLI.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I want to filter network traffic to virtual machines that perform similar functions, such as web servers.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/30/2018
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: d2cabecfb2a70e6d325129335c066bb35aeb02ba
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98221909"
---
# <a name="filter-network-traffic-with-a-network-security-group-using-the-azure-cli"></a>Filtrera nätverks trafik med en nätverks säkerhets grupp med hjälp av Azure CLI

Du kan filtrera inkommande och utgående nätverkstrafik till och från ett undernät i ett virtuellt nätverk med en nätverkssäkerhetsgrupp. Nätverkssäkerhetsgrupper innehåller säkerhetsregler som filtrerar nätverkstrafik efter IP-adress, port och protokoll. Säkerhetsregler tillämpas på resurser som har distribuerats i ett undernät. I den här artikeln kan du se hur du:

* Skapa en nätverkssäkerhetsgrupp och säkerhetsregler
* Skapa ett virtuellt nätverk och associera en nätverkssäkerhetsgrupp med ett undernät
* Distribuera virtuella datorer (VM) i ett undernät
* Testa trafikfilter

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2.0.28 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

En nätverkssäkerhetsgrupp innehåller säkerhetsregler. Säkerhetsregler anger en källa och ett mål. Källor och mål kan vara programsäkerhetsgrupper.

### <a name="create-application-security-groups"></a>Skapa programsäkerhetsgrupper

Skapa först en resurs grupp för alla resurser som skapats i den här artikeln med [AZ Group Create](/cli/azure/group). I följande exempel skapas en resursgrupp på platsen *eastus*: 

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

Skapa en program säkerhets grupp med [AZ Network grupperna Create](/cli/azure/network/asg). En programsäkerhetsgrupp gör att du kan gruppera servrar med liknande portfiltreringskrav. I följande exempel skapas två programsäkerhetsgrupper.

```azurecli-interactive
az network asg create \
  --resource-group myResourceGroup \
  --name myAsgWebServers \
  --location eastus

az network asg create \
  --resource-group myResourceGroup \
  --name myAsgMgmtServers \
  --location eastus
```

### <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

Skapa en nätverks säkerhets grupp med [AZ Network NSG Create](/cli/azure/network/nsg). I följande exempel skapas en nätverkssäkerhetsgrupp med namnet *myNsg*: 

```azurecli-interactive 
# Create a network security group
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsg
```

### <a name="create-security-rules"></a>Skapa säkerhetsregler

Skapa en säkerhets regel med [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule). I följande exempel skapas en regel som tillåter inkommande trafik från Internet till programsäkerhetsgruppen *myWebServers* via port 80 och 443:

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsg \
  --name Allow-Web-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-asgs "myAsgWebServers" \
  --destination-port-range 80 443
```

I följande exempel skapas en regel som tillåter trafik inkommande från Internet till *myMgmtServers* program säkerhets grupp via port 22:

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsg \
  --name Allow-SSH-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 110 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-asgs "myAsgMgmtServers" \
  --destination-port-range 22
```

I den här artikeln exponeras SSH (port 22) för Internet för den virtuella datorn *myAsgMgmtServers* . För produktions miljöer, i stället för att exponera port 22 till Internet, rekommenderar vi att du ansluter till Azure-resurser som du vill hantera med hjälp av en [VPN-](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [privat](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nätverks anslutning.

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Skapa ett virtuellt nätverk med kommandot [az network vnet create](/cli/azure/network/vnet). I följande exempel skapas ett virtuellt nätverk med namnet *myVirtualNetwork*:

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16
```

Lägg till ett undernät i ett virtuellt nätverk med [AZ Network VNet Subnet Create](/cli/azure/network/vnet/subnet). I följande exempel läggs ett undernät med namnet *mySubnet* till i det virtuella nätverket och nätverkssäkerhetsgruppen *myNsg* associeras med det:

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name mySubnet \
  --address-prefix 10.0.0.0/24 \
  --network-security-group myNsg
```

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa två virtuella datorer i det virtuella nätverket så att du kan verifiera trafikfiltrering i ett senare steg. 

Skapa en virtuell dator med [az vm create](/cli/azure/vm). I följande exempel skapas en virtuell dator som fungerar som en webbserver. `--asgs myAsgWebServers`Alternativet gör att Azure skapar nätverks gränssnittet för den virtuella datorn som medlem i program säkerhets gruppen *myAsgWebServers* .

`--nsg ""`Alternativet anges för att förhindra att Azure skapar en standard nätverks säkerhets grupp för nätverks gränssnittet Azure skapar när den virtuella datorn skapas. För att förenkla den här artikeln används ett lösen ord. Nycklar används vanligt vis i produktions distributioner. Om du använder nycklar måste du också konfigurera vidarebefordran av SSH-agenten för de återstående stegen. Mer information finns i dokumentationen för SSH-klienten. Ersätt `<replace-with-your-password>` i följande kommando med ett lösen ord som du väljer själv.

```azurecli-interactive
adminPassword="<replace-with-your-password>"

az vm create \
  --resource-group myResourceGroup \
  --name myVmWeb \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet mySubnet \
  --nsg "" \
  --asgs myAsgWebServers \
  --admin-username azureuser \
  --admin-password $adminPassword
```

Det tar några minuter att skapa den virtuella datorn. När den virtuella datorn har skapats returneras utdata som liknar följande exempel: 

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmWeb",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Anteckna **publicIpAddress**. Den här adressen används för att komma åt den virtuella datorn från Internet i ett senare steg.  Skapa en virtuell dator som fungerar som en hanteringsserver:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmMgmt \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet mySubnet \
  --nsg "" \
  --asgs myAsgMgmtServers \
  --admin-username azureuser \
  --admin-password $adminPassword
```

Det tar några minuter att skapa den virtuella datorn. När den virtuella datorn har skapats noterar du **publicIpAddress** i returnerade utdata. Den här adressen används för att få åtkomst till den virtuella datorn i nästa steg. Fortsätt inte med nästa steg förrän Azure har skapat den virtuella datorn.

## <a name="test-traffic-filters"></a>Testa trafikfilter

Använd kommandot som följer för att skapa en SSH-session med den virtuella datorn *myVmMgmt* . Ersätt *\<publicIpAddress>* med den offentliga IP-adressen för den virtuella datorn. I exemplet ovan är IP-adressen *13.90.242.231*.

```bash 
ssh azureuser@<publicIpAddress>
```

När du uppmanas att ange ett lösen ord anger du det lösen ord som du angav i [skapa virtuella datorer](#create-virtual-machines).

Anslutningen lyckas eftersom port 22 tillåts inkommande från Internet till den *myAsgMgmtServers* program säkerhets grupp som är ansluten till den virtuella *myVmMgmt* -datorn.

Använd följande kommando för att använda SSH till den virtuella *myVmWeb* -datorn från den virtuella datorn *myVmMgmt* :

```bash 
ssh azureuser@myVmWeb
```

Anslutningen lyckas eftersom en standardsäkerhetsregel i varje nätverkssäkerhetsgrupp tillåter trafik via alla portar mellan alla IP-adresser i ett virtuellt nätverk. Du kan inte använda SSH till den virtuella datorn *myVmWeb* från Internet eftersom säkerhets regeln för *myAsgWebServers* inte tillåter port 22 inkommande från Internet.

Använd följande kommandon för att installera nginx-webbservern på den virtuella datorn *myVmWeb* :

```bash 
# Update package source
sudo apt-get -y update

# Install NGINX
sudo apt-get -y install nginx
```

Den virtuella *myVmWeb* -datorn tillåts utgående till Internet för att hämta nginx eftersom en standard säkerhets regel tillåter all utgående trafik till Internet. Avsluta *myVmWeb* SSH-sessionen, som lämnar dig vid `username@myVmMgmt:~$` prompten av den virtuella *myVmMgmt* -datorn. För att hämta välkomst skärmen nginx från den virtuella *myVmWeb* -datorn anger du följande kommando:

```bash
curl myVmWeb
```

Utloggning av den virtuella *myVmMgmt* -datorn. För att bekräfta att du har åtkomst till *myVmWeb* -webbservern utanför Azure anger du `curl <publicIpAddress>` från din egen dator. Anslutningen lyckas eftersom port 80 tillåts inkommande från Internet till den *myAsgWebServers* program säkerhets grupp som är ansluten till den virtuella *myVmWeb* -datorn.

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs använder du [AZ Group Delete](/cli/azure/group) för att ta bort resurs gruppen och alla resurser den innehåller.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du skapat en nätverks säkerhets grupp och kopplat den till ett virtuellt nätverks under nät. Mer information om nätverkssäkerhetsgrupper finns i [Översikt över nätverkssäkerhetsgrupper](./network-security-groups-overview.md) och [Hantera en nätverkssäkerhetsgrupp](manage-network-security-group.md).

Azure dirigerar som standard trafik mellan undernät. Du kan i stället välja att exempelvis dirigera trafik mellan undernät via en virtuell dator, som fungerar som en brandvägg. Mer information finns i [skapa en routningstabell](tutorial-create-route-table-cli.md).