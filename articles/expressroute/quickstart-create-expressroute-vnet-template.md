---
title: 'Snabbstart: Skapa en ExpressRoute-krets med hjälp av Azure Resource Manager mall (ARM-mall)'
description: Den här snabbstarten visar hur du skapar en ExpressRoute-krets med hjälp Azure Resource Manager mall (ARM-mall).
services: expressroute
author: duongau
ms.author: duau
manager: kumud
ms.date: 10/12/2020
ms.topic: quickstart
ms.service: expressroute
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 3dc1d5f5ec3dfb004468deb2bec80927c7ec189d
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529894"
---
# <a name="quickstart-create-an-expressroute-circuit-with-private-peering-using-an-arm-template"></a>Snabbstart: Skapa en ExpressRoute-krets med privat peering med hjälp av en ARM-mall

Den här snabbstarten beskriver hur du använder en Azure Resource Manager mall (ARM-mall) för att skapa en ExpressRoute-krets med privat peering.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-expressroute-private-peering-vnet%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-expressroute-private-peering-vnet).

I den här snabbstarten skapar du en ExpressRoute-krets *med Equinix* som tjänstleverantör. Kretsen använder en *Premium-SKU* med en bandbredd på *50* Mbit/s och peeringplatsen för *Washington DC*. Privat peering aktiveras med ett primärt och sekundärt undernät *på 192.168.10.16/30* respektive *192.168.10.20/30.* Ett virtuellt nätverk skapas också tillsammans med en *HighPerformance ExpressRoute-gateway.*

:::code language="json" source="~/quickstart-templates/101-expressroute-private-peering-vnet/azuredeploy.json":::

Flera Azure-resurser har definierats i mallen:

* [**Microsoft.Network/expressRouteCircuits**](/azure/templates/microsoft.network/expressRouteCircuits)
* [**Microsoft.Network/expressRouteCircuits/peerings**](/azure/templates/microsoft.network/expressRouteCircuits/peerings) (används för att aktiverat privat peering på kretsen)
* [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networkSecurityGroups) (nätverkssäkerhetsgruppen tillämpas på undernäten i det virtuella nätverket)
* [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualNetworks)
* [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicIPAddresses) (offentlig IP används av ExpressRoute-gatewayen)
* [**Microsoft.Network/virtualNetworkGateways**](/azure/templates/microsoft.network/virtualNetworkGateways) (ExpressRoute-gateway används för att länka VNet till kretsen)

Om du vill hitta fler mallar som är relaterade till ExpressRoute kan du läsa [Azure-snabbstartsmallar.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular)

## <a name="deploy-the-template"></a>Distribuera mallen

1. Välj **Prova från** följande kodblock för att öppna Azure Cloud Shell och följ sedan anvisningarna för att logga in på Azure.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-expressroute-private-peering-vnet/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    Vänta tills du ser prompten från konsolen.

1. Välj **Kopiera** från föregående kodblock för att kopiera PowerShell-skriptet.

1. Högerklicka på gränssnittskonsolfönstret och välj klistra **in**.

1. Ange värdena.

    Resursgruppens namn är projektnamnet med **rg** tillagt.

    Det tar cirka 20 minuter att distribuera mallen. När du är klar ser utdata ut ungefär så här:

    :::image type="content" source="./media/quickstart-create-expressroute-vnet/expressroute-powershell-output.png" alt-text="Utdata för ExpressRoute Resource Manager mall för PowerShell-distribution":::

Azure PowerShell används för att distribuera mallen. Förutom att Azure PowerShell kan du även använda Azure Portal, Azure CLI och REST API. Mer information om andra distributionsmetoder finns i [Distribuera mallar.](../azure-resource-manager/templates/deploy-portal.md)

## <a name="validate-the-deployment"></a>Verifiera distributionen

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj **Resursgrupper** i det vänstra fönstret.

1. Välj den resursgrupp som du skapade i föregående avsnitt. Standardresursgruppens namn är projektnamnet med **rg** tillagt.

1. Resursgruppen ska innehålla följande resurser som visas här:

     :::image type="content" source="./media/quickstart-create-expressroute-vnet/expressroute-resource-group.png" alt-text="Resursgrupp för ExpressRoute-distribution":::

1. Välj  **ExpressRoute-kretsen er-ck01** för att kontrollera att kretsstatusen är **Aktiverad,** providerstatus är Inte etablerat och privat peering har **statusen Etablerat.**

    :::image type="content" source="./media/quickstart-create-expressroute-vnet/expressroute-circuit.png" alt-text="Distributionskrets för ExpressRoute":::

> [!NOTE]
> Du måste anropa providern för att slutföra etableringsprocessen innan du kan länka det virtuella nätverket till kretsen.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver de resurser som du skapade med ExpressRoute-kretsen tar du bort resursgruppen. Detta tar bort ExpressRoute-kretsen och alla relaterade resurser.

Om du vill ta bort resursgruppen anropar du `Remove-AzResourceGroup` cmdleten :

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en:

* ExpressRoute-krets
* Virtual Network
* VPN Gateway
* Offentlig IP-adress
* nätverkssäkerhetsgrupper

Om du vill lära dig hur du länkar ett virtuellt nätverk till en krets fortsätter du till ExpressRoute-självstudierna.

> [!div class="nextstepaction"]
> [ExpressRoute-självstudier](expressroute-howto-linkvnet-portal-resource-manager.md)
