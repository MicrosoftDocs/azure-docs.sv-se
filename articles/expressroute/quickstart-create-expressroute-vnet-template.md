---
title: 'Snabb start: skapa en ExpressRoute-krets med hjälp av en Azure Resource Manager-mall (ARM-mall)'
description: Den här snabb starten visar hur du skapar en ExpressRoute-krets med hjälp av Azure Resource Manager mall (ARM-mall).
services: expressroute
author: duongau
mnager: kumud
ms.service: expressroute
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 10/12/2020
ms.author: duau
ms.openlocfilehash: 7521344a2bb6aae67724c8bfbb9131e2ff1e6b94
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92789734"
---
# <a name="quickstart-create-an-expressroute-circuit-with-private-peering-using-an-arm-template"></a>Snabb start: skapa en ExpressRoute-krets med privat peering med en ARM-mall

I den här snabb starten beskrivs hur du använder en Azure Resource Manager mall (ARM-mall) för att skapa en ExpressRoute-krets med privat peering.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-expressroute-private-peering-vnet%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-expressroute-private-peering-vnet).

I den här snabb starten skapar du en ExpressRoute-krets med *Equinix* som tjänst leverantör. Kretsen använder en *Premium-SKU*, med bandbredd på *50 Mbit/s* och peering-platsen för *Washington DC*. Privat peering aktive ras med ett primärt och sekundärt undernät av *192.168.10.16/30* respektive *192.168.10.20/30* . Ett virtuellt nätverk kommer också att skapas tillsammans med en *HighPerformance ExpressRoute-Gateway*.

:::code language="json" source="~/quickstart-templates/101-expressroute-private-peering-vnet/azuredeploy.json":::

Flera Azure-resurser har definierats i mallen:

* [**Microsoft. Network/expressRouteCircuits**](/azure/templates/microsoft.network/expressRouteCircuits)
* [**Microsoft. Network/expressRouteCircuits/peering**](/azure/templates/microsoft.network/expressRouteCircuits/peerings) (används för att aktivera privat peering på kretsen)
* [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networkSecurityGroups) (nätverks säkerhets gruppen tillämpas på under näten i det virtuella nätverket)
* [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualNetworks)
* [**Microsoft. Network/publicIPAddresses**](/azure/templates/microsoft.network/publicIPAddresses) (offentlig IP används av ExpressRoute-gatewayen)
* [**Microsoft. Network/virtualNetworkGateways**](/azure/templates/microsoft.network/virtualNetworkGateways) (ExpressRoute Gateway används för att länka VNet till kretsen)

Du hittar fler mallar som är relaterade till ExpressRoute i [Azure snabb starts mallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Distribuera mallen

1. Välj **prova** från följande kodblock för att öppna Azure Cloud Shell och följ sedan anvisningarna för att logga in på Azure.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-expressroute-private-peering-vnet/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    Vänta tills du ser prompten från-konsolen.

1. Kopiera PowerShell-skriptet genom att välja **Kopiera** från föregående kodblock.

1. Högerklicka på fönstret Shell-konsol och välj sedan **Klistra in**.

1. Ange värdena.

    Resurs gruppens namn är projekt namnet med **RG** tillagt.

    Det tar cirka 20 minuter att distribuera mallen. När det är slutfört ser utdata ut ungefär så här:

    :::image type="content" source="./media/quickstart-create-expressroute-vnet/expressroute-powershell-output.png" alt-text="Utdata för PowerShell-distribution för ExpressRoute Resource Manager-mall":::

Azure PowerShell används för att distribuera mallen. Förutom Azure PowerShell kan du också använda Azure Portal, Azure CLI och REST API. Mer information om andra distributions metoder finns i [distribuera mallar](../azure-resource-manager/templates/deploy-portal.md).

## <a name="validate-the-deployment"></a>Verifiera distributionen

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj **resurs grupper** i det vänstra fönstret.

1. Välj den resurs grupp som du skapade i föregående avsnitt. Standard resurs grupps namnet är projekt namnet med **RG** tillagt.

1. Resurs gruppen måste innehålla följande resurser som visas här:

     :::image type="content" source="./media/quickstart-create-expressroute-vnet/expressroute-resource-group.png" alt-text="Resurs grupp för ExpressRoute-distribution":::

1. Välj ExpressRoute-kretsen **er-ck01** för att kontrol lera att krets statusen **är aktive rad**, providerns status är **inte etablerad** och privat peering har statusen **etablerad**.

    :::image type="content" source="./media/quickstart-create-expressroute-vnet/expressroute-circuit.png" alt-text="ExpressRoute distributions krets":::

> [!NOTE]
> Du måste anropa providern för att slutföra etablerings processen innan du kan länka det virtuella nätverket till kretsen.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver de resurser som du skapade med ExpressRoute-kretsen tar du bort resurs gruppen. Detta tar bort ExpressRoute-kretsen och alla relaterade resurser.

Anropa cmdleten om du vill ta bort resurs gruppen `Remove-AzResourceGroup` :

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en:

* ExpressRoute-krets
* Virtual Network
* VPN Gateway
* Offentlig IP-adress
* nätverks säkerhets grupper

Om du vill lära dig hur du länkar ett virtuellt nätverk till en krets fortsätter du till ExpressRoute-självstudierna.

> [!div class="nextstepaction"]
> [ExpressRoute-självstudier](expressroute-howto-linkvnet-portal-resource-manager.md)
