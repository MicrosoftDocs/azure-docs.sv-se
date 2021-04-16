---
title: 'Självstudie: Skapa en NAT-gateway – Azure CLI'
titlesuffix: Azure Virtual Network NAT
description: Kom igång med att skapa en NAT-gateway med hjälp av Azure CLI.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 03/10/2021
ms.custom: template-tutorial, devx-track-azurecli
ms.openlocfilehash: d312702f441cfe2ad94e347cadcdfc88d4cc2a72
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479329"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-cli"></a>Självstudie: Skapa en NAT-gateway med Hjälp av Azure CLI

Den här självstudien visar hur du använder Azure Virtual Network NAT-tjänsten. Du skapar en NAT-gateway för att tillhandahålla utgående anslutning för en virtuell dator i Azure. 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa ett virtuellt nätverk.
> * Skapa en virtuell dator.
> * Skapa en NAT-gateway och associera med det virtuella nätverket.
> * Anslut till den virtuella datorn och verifiera NAT-IP-adressen.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Den här snabbstarten kräver version 2.0.28 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med [az group create](/cli/azure/group#az_group_create). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

I följande exempel skapas en resursgrupp med **namnet myResourceGroupNAT** på **platsen eastus2:**

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
```

## <a name="create-the-nat-gateway"></a>Skapa NAT-gatewayen

I det här avsnittet skapar vi NAT-gatewayen och stödresurser.

### <a name="create-public-ip-address"></a>Skapa offentlig IP-adress

För att få åtkomst till Internet behöver du en eller flera offentliga IP-adresser för NAT-gatewayen. Använd [az network public-ip create för](/cli/azure/network/public-ip#az_network_public_ip_create) att skapa en offentlig IP-adressresurs med namnet **myPublicIP** i **myResourceGroupNAT.** 

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIP \
    --sku standard \
    --allocation static
```

### <a name="create-nat-gateway-resource"></a>Skapa en NAT-gatewayresurs

Skapa en global Azure NAT-gateway med [az network nat gateway create](/cli/azure/network/nat#az_network_nat_gateway_create). Resultatet av det här kommandot skapar en gatewayresurs med namnet **myNATgateway som** använder den offentliga IP-adressen **myPublicIP**. Tidsgränsen för inaktivitet är inställd på 10 minuter.  

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIP \
    --idle-timeout 10       
  ```

### <a name="create-virtual-network"></a>Skapa det virtuella nätverket

Skapa ett virtuellt nätverk med **namnet myVnet** med ett undernät med namnet **mySubnet** [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) i **resursgruppen myResourceGroup.** IP-adressutrymmet för det virtuella nätverket är **10.1.0.0/16.** Undernätet i det virtuella nätverket är **10.1.0.0/24.**

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location eastus2 \
    --name myVnet \
    --address-prefix 10.1.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 10.1.0.0/24
```

### <a name="create-bastion-host"></a>Skapa skyddsvärd

Skapa en Azure Bastion med namnet **myBastionHost för att** få åtkomst till den virtuella datorn. 

Använd [az network vnet subnet create för](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) att skapa Azure Bastion undernät.

```azurecli-interactive
az network vnet subnet create \
    --resource-group myResourceGroupNAT \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

Skapa en offentlig IP-adress för skyddsvärden med [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create). 

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myBastionIP \
    --sku Standard
```

Använd [az network bastion create](/cli/azure/network/bastion#az-network-bastion-create) för att skapa skyddsvärden. 

```azurecli-interactive
az network bastion create \
    --resource-group myResourceGroupNAT \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus2
```

### <a name="configure-nat-service-for-source-subnet"></a>Konfigurera NAT-tjänsten för källundernätet

Vi konfigurerar källundernätet **mySubnet** i det virtuella nätverket **myVnet** för att använda en specifik NAT-gatewayresurs **myNATgateway** [med az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update). Det här kommandot aktiverar NAT-tjänsten i det angivna undernätet.

```azurecli-interactive
  az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnet \
    --name mySubnet \
    --nat-gateway myNATgateway
```

All utgående trafik till Internetmål använder nu NAT-gatewayen.  Du behöver inte konfigurera en UDR.


## <a name="virtual-machine"></a>Virtuell dator

I det här avsnittet skapar du en virtuell dator för att testa NAT-gatewayen för att verifiera den offentliga IP-adressen för den utgående anslutningen.

Skapa den virtuella datorn med [az vm create](/cli/azure/vm#az-vm-create).

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

I det här avsnittet testar vi NAT-gatewayen. Först identifierar vi den offentliga IP-adressen för NAT-gatewayen. Sedan ansluter vi till den virtuella testdatorn och verifierar den utgående anslutningen via NAT-gatewayen.
    
1. Logga in på [Azure-portalen](https://portal.azure.com)

1. Hitta den offentliga IP-adressen för NAT-gatewayen på **skärmen** Översikt. Välj **Alla tjänster** på den vänstra menyn, välj Alla **resurser** och välj sedan **myPublicIP.**

2. Anteckna den offentliga IP-adressen:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/find-public-ip.png" alt-text="Identifiera offentlig IP-adress för NAT-gateway" border="true":::

3. Välj **Alla tjänster** på den vänstra menyn, välj Alla resurser och välj sedan **myVM** som finns i resursgruppen **myResourceGroupNAT** i resurslistan.

4. På sidan **Översikt** väljer du **Anslut** och sedan **Bastion**.

5. Välj den blå **knappen Använd Bastion.**

6. Ange det användarnamn och lösenord som angavs när den virtuella datorn skapades.

7. Öppna **Internet Explorer** på **myTestVM**.

8. Ange **https://whatsmyip.com** i adressfältet.

9. Kontrollera att IP-adressen som visas matchar NAT-gatewayadressen som du antecknade i föregående steg:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/my-ip.png" alt-text="Internet Explorer visar extern utgående IP-adress" border="true":::

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda det här programmet tar du bort det virtuella nätverket, den virtuella datorn och NAT-gatewayen med följande steg:

```azurecli-interactive 
  az group delete \
    --name myResourceGroupNAT
```

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Virtual Network NAT finns i:
> [!div class="nextstepaction"]
> [Virtual Network NAT översikt](nat-overview.md)
