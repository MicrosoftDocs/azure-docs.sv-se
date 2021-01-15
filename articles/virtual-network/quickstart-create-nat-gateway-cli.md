---
title: Skapa en NAT-gateway – Azure CLI
titlesuffix: Azure Virtual Network NAT
description: Den här snabb starten visar hur du skapar en NAT-gateway med Azure CLI
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.custom: devx-track-azurecli
ms.openlocfilehash: 43b2c8271090d2254bcb4834c3b566c3601a104b
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98223252"
---
# <a name="create-a-nat-gateway-using-azure-cli"></a>Skapa en NAT-gateway med Azure CLI

Den här självstudien visar hur du använder Azure Virtual Network NAT-tjänsten. Du skapar en NAT-gateway för att tillhandahålla utgående anslutning för en virtuell dator i Azure. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2.0.71 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med [az group create](/cli/azure/group). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

I följande exempel skapas en resurs grupp med namnet **myResourceGroupNAT** på **eastus2** -platsen:

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
```

## <a name="create-the-nat-gateway"></a>Skapa NAT-gatewayen

### <a name="create-a-public-ip-address"></a>Skapa en offentlig IP-adress

För att få åtkomst till det offentliga Internet behöver du en eller flera offentliga IP-adresser för NAT-gatewayen. Använd [AZ Network Public-IP Create](/cli/azure/network/public-ip) för att skapa en offentlig IP-adressresurs med namnet **myPublicIP** i **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIP \
    --sku standard
```

### <a name="create-a-public-ip-prefix"></a>Skapa ett offentligt IP-prefix

Du kan använda en eller flera offentliga IP-adressresurser, offentliga IP-prefix eller både och med NAT-gateway. Vi "l Lägg till en resurs för offentliga IP-prefix i det här scenariot för att demonstrera.   Använd [AZ Network Public-IP prefix Create](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-create) för att skapa en offentlig IP-prefixlängd som heter **myPublicIPprefix** i **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPprefix \
    --length 31
```

### <a name="create-a-nat-gateway-resource"></a>Skapa en NAT-gateway-resurs

I det här avsnittet beskrivs hur du kan skapa och konfigurera följande komponenter i NAT-tjänsten med hjälp av NAT-gateway-resursen:
  - En offentlig IP-pool och ett offentligt IP-prefix som används för utgående flöden som översätts av NAT gateway-resursen.
  - Ändra tids gränsen för inaktivitet från standardvärdet 4 minuter till 10 minuter.

Skapa en global Azure NAT-gateway med [AZ Network NAT gateway Create](/cli/azure/network/nat?view=azure-cli-latest) med namnet **myNATgateway**. Kommandot använder både den offentliga IP- **myPublicIP** och det offentliga IP-prefixet **myPublicIPprefix**. Kommandot ändrar tids gränsen för inaktivitet till **10** minuter.

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIP \
    --public-ip-prefixes myPublicIPprefix \
    --idle-timeout 10       
  ```

NAT-gatewayen är nu funktionell och allt som saknas är att konfigurera vilka undernät i ett virtuellt nätverk som ska använda det.

## <a name="configure-virtual-network"></a>Konfigurera ett virtuellt nätverk

Innan du distribuerar en virtuell dator och kan använda din NAT-gateway måste du skapa det virtuella nätverket.

Skapa ett virtuellt nätverk med namnet **myVnet** med ett undernät med namnet **mitt undernät** i **myResourceGroupNAT** med [AZ Network VNet Create](/cli/azure/network/vnet).  IP-adressutrymmet för det virtuella nätverket är **192.168.0.0/16**. Under nätet i det virtuella nätverket är **192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location eastus2 \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.0.0/24
```

### <a name="configure-nat-service-for-source-subnet"></a>Konfigurera NAT-tjänst för käll under nät

Vi konfigurerar käll under nätets **undernät** i det virtuella nätverket **myVnet** för att använda en angiven NAT-gateway resurs **myNATgateway** med [AZ Network VNet Subnet Update](/cli/azure/network/vnet/subnet).  Med det här kommandot aktive ras NAT-tjänsten på det angivna under nätet.

```azurecli-interactive
  az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnet \
    --name mySubnet \
    --nat-gateway myNATgateway
```

All utgående trafik till Internet destinationer använder nu NAT-gatewayen.  Det är inte nödvändigt att konfigurera en UDR.

## <a name="create-a-vm-to-use-the-nat-service"></a>Skapa en virtuell dator för att använda tjänsten NAT

Nu ska vi skapa en virtuell dator för att använda NAT-tjänsten.  Den här virtuella datorn har en offentlig IP-adress som kan användas som en offentlig IP-adress på instans nivå för att få åtkomst till den virtuella datorn.  NAT-tjänsten är en flödes riktning som är medveten om och kommer att ersätta standard målet för Internet i ditt undernät. Den virtuella datorns offentliga IP-adress används inte för utgående anslutningar.

### <a name="create-public-ip-for-source-vm"></a>Skapa en offentlig IP-adress för den virtuella käll datorn

Vi skapar en offentlig IP-adress som ska användas för åtkomst till den virtuella datorn.  Använd [AZ Network Public-IP Create](/cli/azure/network/public-ip) för att skapa en offentlig IP-adressresurs med namnet **myPublicIPVM** i **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPVM \
    --sku standard
```

### <a name="create-an-nsg-for-vm"></a>Skapa en NSG för virtuell dator

Eftersom standard-offentliga IP-adresser är "säkra som standard", måste vi skapa en NSG för att tillåta inkommande åtkomst för SSH-åtkomst. Använd [AZ Network NSG Create](/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) för att skapa en NSG-resurs med namnet **myNSG** i **myResourceGroupNAT**.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSG 
```

### <a name="expose-ssh-endpoint-on-source-vm"></a>Exponera SSH-slutpunkt på den virtuella käll datorn

Vi skapar en regel i NSG för SSH-åtkomst till den virtuella käll datorn. Använd [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) för att skapa en NSG-regel med namnet **SSH** i NSG som heter **myNSG** i **myResourceGroupNAT**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSG \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
```

### <a name="create-nic-for-vm"></a>Skapa NIC för virtuell dator

Skapa ett nätverks gränssnitt med [AZ Network NIC Create](/cli/azure/network/nic#az-network-nic-create) och associera med den offentliga IP-adressen och nätverks säkerhets gruppen. 

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --public-ip-address myPublicIPVM \
    --network-security-group myNSG
```

### <a name="create-vm"></a>Skapa en virtuell dator

Skapa den virtuella datorn med [AZ VM Create](/cli/azure/vm#az-vm-create).  Vi genererar SSH-nycklar för den här virtuella datorn och lagrar den privata nyckeln för senare användning.

 ```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupNAT \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --generate-ssh-keys
```

Vänta tills den virtuella datorn har distribuerats och fortsätt sedan med resten av stegen.

## <a name="discover-the-ip-address-of-the-vm"></a>Identifiera IP-adressen för den virtuella datorn

Först måste vi identifiera IP-adressen för den virtuella dator som du har skapat. Hämta den offentliga IP-adressen för den virtuella datorn med [AZ Network Public-IP show](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPVM \
    --query [ipAddress] \
    --output tsv
``` 

>[!IMPORTANT]
>Kopiera den offentliga IP-adressen och klistra in den i ett anteckningar så att du kan använda den för att få åtkomst till den virtuella datorn.

### <a name="sign-in-to-vm"></a>Logga in på virtuell dator

SSH-autentiseringsuppgifterna ska lagras i Cloud Shell från föregående åtgärd.  Öppna en [Azure Cloud Shell](https://shell.azure.com) i webbläsaren. Använd IP-adressen som hämtades i föregående steg för SSH till den virtuella datorn.

```bash
ssh <ip-address-destination>
```

Du är nu redo att använda NAT-tjänsten.

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du använda kommandot [AZ Group Delete](/cli/azure/group#az-group-delete) för att ta bort resurs gruppen och alla resurser som ingår i.

```azurecli-interactive 
  az group delete \
    --name myResourceGroupNAT
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat en NAT-gateway och en virtuell dator för att använda den. 

Granska mått i Azure Monitor för att se hur NAT-tjänsten fungerar. Diagnostisera problem som resurs utbelastning av tillgängliga SNAT-portar.  Resurs utbelastningen på SNAT-portar åtgärdas genom att lägga till ytterligare offentliga IP-adressresurser eller resurser för offentliga IP-prefix eller både och.


- Lär dig mer om [Azure Virtual Network NAT](./nat-overview.md)
- Lär dig om [NAT gateway-resurs](./nat-gateway-resource.md).
- Snabb start för att distribuera [NAT gateway-resurs med hjälp av Azure CLI](./quickstart-create-nat-gateway-cli.md).
- Snabb start för att distribuera [NAT gateway-resurs med hjälp av Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Snabb start för att distribuera [NAT gateway-resurs med hjälp av Azure Portal](./quickstart-create-nat-gateway-portal.md).
> [!div class="nextstepaction"]