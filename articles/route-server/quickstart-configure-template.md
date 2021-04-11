---
title: 'Snabb start: skapa en Azure Route-server med hjälp av en Azure Resource Manager mall (ARM-mall)'
description: Den här snabb starten visar hur du skapar en Azure Route-Server genom att använda Azure Resource Manager mall (ARM-mall).
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 04/05/2021
ms.author: duau
ms.openlocfilehash: 6f56b9fb1f6a1f5a1fe0811617fb20412c52fd72
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106452218"
---
# <a name="quickstart-create-an-azure-route-server-using-an-arm-template"></a>Snabb start: skapa en Azure Route-server med en ARM-mall

I den här snabb starten beskrivs hur du använder en Azure Resource Manager mall (ARM-mall) för att distribuera en Azure Route-server i ett nytt eller befintligt virtuellt nätverk.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-route-server%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-route-server).

I den här snabb starten ska du distribuera en Azure Route-server i ett nytt eller befintligt virtuellt nätverk. Ett dedikerat undernät med namnet `RouteServerSubnet` kommer att skapas för att vara värd för väg servern. Väg servern kommer också att konfigureras med peer-ASN och peer-IP för att upprätta en BGP-peering.

:::code language="json" source="~/quickstart-templates/101-route-server/azuredeploy.json" range="001-145" highlight="105-142":::

Flera Azure-resurser har definierats i mallen:

* [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualNetworks)
* [**Microsoft. Network/virtualNetworks/subnets**](/azure/templates/microsoft.network/virtualNetworks/subnets) (två undernät, ett namn `routeserversubnet` )
* [**Microsoft. Network/virtualHubs**](/azure.templates/microsoft.network/virtualhubs) (Route Server-distribution)
* [**Microsoft. Network/virtualHubs/ipConfigurations**](/azure.templates/microsoft.network/virtualhubs/ipConfigurations)
* [**Microsoft. Network/virtualHubs/bgpConnections**](/azure.templates/microsoft.network/virtualhubs/bgpConnections) (peer-ASN och peer IP-konfiguration)


Du hittar fler mallar som är relaterade till ExpressRoute i [Azure snabb starts mallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Distribuera mallen

1. Välj **prova** från följande kodblock för att öppna Azure Cloud Shell och följ sedan anvisningarna för att logga in på Azure.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-route-server/azuredeploy.json"

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

    :::image type="content" source="./media/quickstart-configure-template/powershell-output.png" alt-text="Route Server Resource Manager-mall PowerShell-distribution av utdata.":::

Azure PowerShell används för att distribuera mallen. Förutom Azure PowerShell kan du också använda Azure Portal, Azure CLI och REST API. Mer information om andra distributions metoder finns i [distribuera mallar](../azure-resource-manager/templates/deploy-portal.md).

## <a name="validate-the-deployment"></a>Verifiera distributionen

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj **resurs grupper** i det vänstra fönstret.

1. Välj den resurs grupp som du skapade i föregående avsnitt. Standard resurs grupps namnet är projekt namnet med **RG** tillagt.

1. Resurs gruppen får bara innehålla det virtuella nätverket:

     :::image type="content" source="./media/quickstart-configure-template/resource-group.png" alt-text="Dirigera Server distribution resurs grupp med virtuellt nätverk.":::

1. Gå till https://aka.ms/routeserver.

1. Välj Route-servern med namnet **routeserver** för att kontrol lera att distributionen har slutförts.

    :::image type="content" source="./media/quickstart-configure-template/deployment.png" alt-text="Skärm bild av översikts sidan för Route Server.":::

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver de resurser som du skapade med Route-servern tar du bort resurs gruppen. Detta tar bort väg servern och alla relaterade resurser.

Anropa cmdleten om du vill ta bort resurs gruppen `Remove-AzResourceGroup` :

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en:

* Route-Server
* Virtual Network
* Undernät

När du har skapat Azure Route Server kan du fortsätta med att lära dig hur Azure Route Server samverkar med ExpressRoute och VPN-gatewayer: 

> [!div class="nextstepaction"]
> [Azure-ExpressRoute och Azure VPN-support](expressroute-vpn-support.md)
