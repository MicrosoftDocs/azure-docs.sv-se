---
title: 'Snabbstart: Skapa en Azure Route Server med hjälp av en Azure Resource Manager (ARM-mall)'
description: Den här snabbstarten visar hur du skapar en Azure Route Server med hjälp Azure Resource Manager mall (ARM-mall).
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 04/05/2021
ms.author: duau
ms.openlocfilehash: 3476e5fa2c274f0fc2c180711480375b0ebefaf2
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388048"
---
# <a name="quickstart-create-an-azure-route-server-using-an-arm-template"></a>Snabbstart: Skapa en Azure Route Server med hjälp av en ARM-mall

Den här snabbstarten beskriver hur du använder en Azure Resource Manager mall (ARM-mall) för att distribuera en Azure Route Server till ett nytt eller befintligt virtuellt nätverk.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-route-server%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-route-server).

I den här snabbstarten distribuerar du en Azure Route Server till ett nytt eller befintligt virtuellt nätverk. Ett dedikerat `RouteServerSubnet` undernät med namnet skapas som värd för vägservern. Vägservern konfigureras också med peer-ASN och peer-IP för att upprätta en BGP-peering.

:::code language="json" source="~/quickstart-templates/101-route-server/azuredeploy.json" range="001-145" highlight="105-142":::

Flera Azure-resurser har definierats i mallen:

* [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualNetworks)
* [**Microsoft.Network/virtualNetworks/subnets**](/azure/templates/microsoft.network/virtualNetworks/subnets) (två undernät, ett med namnet `routeserversubnet` )
* [**Microsoft.Network/virtualHubs**](/azure/templates/microsoft.network/virtualhubs) (Route Server-distribution)
* [**Microsoft.Network/virtualHubs/ipConfigurations**](/azure/templates/microsoft.network/virtualhubs/ipConfigurations)
* [**Microsoft.Network/virtualHubs/bgpConnections**](/azure/templates/microsoft.network/virtualhubs/bgpconnections) (peer-ASN och peer-IP-konfiguration)


Om du vill hitta fler mallar som är relaterade till ExpressRoute kan du läsa [Azure-snabbstartsmallar.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular)

## <a name="deploy-the-template"></a>Distribuera mallen

1. Välj **Prova från** följande kodblock för att öppna Azure Cloud Shell och följ sedan anvisningarna för att logga in på Azure.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-route-server/azuredeploy.json"

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

    :::image type="content" source="./media/quickstart-configure-template/powershell-output.png" alt-text="Dirigera server Resource Manager PowerShell-distributionsutdata.":::

Azure PowerShell används för att distribuera mallen. Förutom att Azure PowerShell kan du även använda Azure Portal, Azure CLI och REST API. Mer information om andra distributionsmetoder finns i [Distribuera mallar.](../azure-resource-manager/templates/deploy-portal.md)

## <a name="validate-the-deployment"></a>Verifiera distributionen

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj **Resursgrupper** i det vänstra fönstret.

1. Välj den resursgrupp som du skapade i föregående avsnitt. Standardresursgruppens namn är projektnamnet med **rg** tillagt.

1. Resursgruppen ska endast innehålla det virtuella nätverket:

     :::image type="content" source="./media/quickstart-configure-template/resource-group.png" alt-text="Dirigera serverdistributionsresursgrupp med virtuellt nätverk.":::

1. Gå till https://aka.ms/routeserver.

1. Välj vägservern med namnet **routeserver för** att kontrollera att distributionen lyckades.

    :::image type="content" source="./media/quickstart-configure-template/deployment.png" alt-text="Skärmbild av översiktssidan för Route Server.":::

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen när du inte längre behöver de resurser som du skapade med vägservern. Detta tar bort vägservern och alla relaterade resurser.

Om du vill ta bort resursgruppen anropar du `Remove-AzResourceGroup` cmdleten :

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en:

* Vägserver
* Virtual Network
* Undernät

När du har skapat Azure Route Server kan du fortsätta att lära dig mer om hur Azure Route Server interagerar med ExpressRoute och VPN-gatewayer: 

> [!div class="nextstepaction"]
> [Azure ExpressRoute och Azure VPN-stöd](expressroute-vpn-support.md)
