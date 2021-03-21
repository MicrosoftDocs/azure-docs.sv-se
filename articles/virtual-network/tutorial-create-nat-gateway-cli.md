---
title: 'Självstudie: skapa en NAT-gateway – Azure CLI'
titlesuffix: Azure Virtual Network NAT
description: Kom igång med att skapa en NAT-gateway med Azure CLI.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 03/10/2021
ms.custom: template-tutorial
ms.openlocfilehash: 5dd431a5a7377c409be0794511c5f402d1c5a3a9
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102663394"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-cli"></a>Självstudie: skapa en NAT-gateway med Azure CLI

Den här självstudien visar hur du använder Azure Virtual Network NAT-tjänsten. Du skapar en NAT-gateway för att tillhandahålla utgående anslutning för en virtuell dator i Azure. 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa ett virtuellt nätverk.
> * Skapa en virtuell dator.
> * Skapa en NAT-gateway och koppla den till det virtuella nätverket.
> * Anslut till den virtuella datorn och kontrol lera IP-adressen för NAT.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Den här snabb starten kräver version 2.0.28 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med [az group create](/cli/azure/group#az_group_create). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

I följande exempel skapas en resurs grupp med namnet **myResourceGroupNAT** på **eastus2** -platsen:

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
```

## <a name="create-the-nat-gateway"></a>Skapa NAT-gatewayen

I det här avsnittet skapar vi NAT-gateway och stöd resurser.

### <a name="create-public-ip-address"></a>Skapa offentlig IP-adress

För att få åtkomst till Internet behöver du en eller flera offentliga IP-adresser för NAT-gatewayen. Använd [AZ Network Public-IP Create](/cli/azure/network/public-ip#az_network_public_ip_create) för att skapa en offentlig IP-adressresurs med namnet **myPublicIP** i **myResourceGroupNAT**. 

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIP \
    --sku standard \
    --allocation static
```

### <a name="create-nat-gateway-resource"></a>Skapa NAT-gateway-resurs

Skapa en global Azure NAT-gateway med [AZ Network NAT gateway Create](/cli/azure/network/nat#az_network_nat_gateway_create). Resultatet av det här kommandot skapar en gateway-resurs med namnet **myNATgateway** som använder den offentliga IP- **myPublicIP**. Tids gränsen för inaktivitet har angetts till 10 minuter.  

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIP \
    --idle-timeout 10       
  ```

### <a name="create-virtual-network"></a>Skapa det virtuella nätverket

Skapa ett virtuellt nätverk med namnet **myVnet** med ett undernät med namnet **undernät** [AZ Network VNet Create](/cli/azure/network/vnet#az_network_vnet_create) i resurs gruppen **myResourceGroup** . IP-adressutrymmet för det virtuella nätverket är **10.1.0.0/16**. Under nätet i det virtuella nätverket är **10.1.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location eastus2 \
    --name myVnet \
    --address-prefix 10.1.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 10.1.0.0/24
```

### <a name="create-bastion-host"></a>Skapa skydds-värd

Skapa en Azure skydds-värd med namnet **myBastionHost** för att få åtkomst till den virtuella datorn. 

Använd [AZ Network VNet Subnet Create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) för att skapa ett Azure skydds-undernät.

```azurecli-interactive
az network vnet subnet create \
    --resource-group myResourceGroupNAT \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

Skapa en offentlig IP-adress för skydds-värden med [AZ Network Public-IP Create](/cli/azure/network/public-ip#az_network_public_ip_create). 

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myBastionIP \
    --sku Standard
```

Använd [AZ Network skydds Create](/cli/azure/network/bastion#az-network-bastion-create) för att skapa skydds-värden. 

```azurecli-interactive
az network bastion create \
    --resource-group myResourceGroupNAT \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus2
```

### <a name="configure-nat-service-for-source-subnet"></a>Konfigurera NAT-tjänst för käll under nät

Vi konfigurerar käll under nätets **undernät** i det virtuella nätverket **myVnet** för att använda en angiven NAT-gateway resurs **myNATgateway** med [AZ Network VNet Subnet Update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update). Med det här kommandot aktive ras NAT-tjänsten på det angivna under nätet.

```azurecli-interactive
  az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnet \
    --name mySubnet \
    --nat-gateway myNATgateway
```

All utgående trafik till Internet destinationer använder nu NAT-gatewayen.  Det är inte nödvändigt att konfigurera en UDR.


## <a name="virtual-machine"></a>Virtuell dator

I det här avsnittet ska du skapa en virtuell dator för att testa NAT-gatewayen för att verifiera den offentliga IP-adressen för den utgående anslutningen.

Skapa den virtuella datorn med [AZ VM Create](/cli/azure/vm#az-vm-create).

```azurecli-interactive
az vm create \
    --name myVM \
    --resource-group myResourceGroupNAT  \
    --admin-username azureuser \
    --image win2019datacenter \
    --public-ip-address "" \
    --subnet mySubnet \
    --vnet-name myVNet
```

Vänta tills den virtuella datorn har skapats innan du går vidare till nästa avsnitt.

## <a name="test-nat-gateway"></a>Testa NAT-gateway

I det här avsnittet ska vi testa NAT-gatewayen. Vi identifierar först den offentliga IP-adressen för NAT-gatewayen. Sedan ansluter vi till den virtuella test datorn och verifierar den utgående anslutningen via NAT-gatewayen.
    
1. Logga in på [Azure-portalen](https://portal.azure.com)

1. Hitta den offentliga IP-adressen för NAT-gatewayen på **översikts** skärmen. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser** och välj sedan **myPublicIP**.

2. Anteckna den offentliga IP-adressen:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/find-public-ip.png" alt-text="Identifiera offentlig IP-adress för NAT-gateway" border="true":::

3. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser** och välj sedan **myVM** i resurs gruppen **myResourceGroupNAT** i resurs gruppen.

4. På sidan **Översikt** väljer du **Anslut** och sedan **skydds**.

5. Välj knappen blå **användnings skydds** .

6. Ange det användar namn och lösen ord som angavs när den virtuella datorn skapades.

7. Öppna **Internet Explorer** på **myTestVM**.

8. Ange **https://whatsmyip.com** i adress fältet.

9. Kontrol lera att den IP-adress som visas matchar NAT-gateway-adressen som du antecknade i föregående steg:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/my-ip.png" alt-text="Internet Explorer som visar extern utgående IP" border="true":::

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda det här programmet tar du bort det virtuella nätverket, den virtuella datorn och NAT-gatewayen med följande steg:

```azurecli-interactive 
  az group delete \
    --name myResourceGroupNAT
```

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Virtual Network NAT finns i:
> [!div class="nextstepaction"]
> [Översikt över Virtual Network NAT](nat-overview.md)
