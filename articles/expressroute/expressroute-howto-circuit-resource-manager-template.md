---
title: 'Azure ExpressRoute-mall: skapa en ExpressRoute-krets'
description: Lär dig hur du skapar en Azure ExpressRoute-krets genom att distribuera en Azure Resource Manager-mall med hjälp av Azure PowerShell.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 11/13/2019
ms.author: duau
ms.openlocfilehash: 2e9b6ddc9da4467590946af12a47f1473a4ea494
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92202063"
---
# <a name="create-an-expressroute-circuit-by-using-azure-resource-manager-template"></a>Skapa en ExpressRoute-krets med hjälp av Azure Resource Manager mall

> [!div class="op_single_selector"]
> * [Azure-portalen](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Azure Resource Manager-mall](expressroute-howto-circuit-resource-manager-template.md)
> * [Video – Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klassisk)](expressroute-howto-circuit-classic.md)
>

Lär dig hur du skapar en ExpressRoute-krets genom att distribuera en Azure Resource Manager-mall med hjälp av Azure PowerShell. Mer information om hur du utvecklar Resource Manager-mallar finns i [Resource Manager-dokumentation](../azure-resource-manager/index.yml) och [mallen referens](/azure/templates/microsoft.network/expressroutecircuits).

## <a name="before-you-begin"></a>Innan du börjar

* Granska [nödvändiga komponenter](expressroute-prerequisites.md) och [arbets flöden](expressroute-workflows.md) innan du påbörjar konfigurationen.
* Se till att du har behörighet att skapa nya nätverks resurser. Kontakta konto administratören om du inte har rätt behörigheter.
* Du kan [Visa en video](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) innan du börjar för att bättre förstå stegen.

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Skapa och etablera en ExpressRoute-krets

[Azure snabb starts mallar](https://azure.microsoft.com/resources/templates/) har en utmärkt samling Resource Manager-mall. Du kan använda en av de [befintliga mallarna](https://azure.microsoft.com/resources/templates/101-expressroute-circuit-create/) för att skapa en ExpressRoute-krets.

[!code-json[create-azure-expressroute-circuit](~/quickstart-templates/101-expressroute-circuit-create/azuredeploy.json)]

Om du vill se fler relaterade mallar väljer du [här](https://azure.microsoft.com/resources/templates/?term=expressroute).

Så här skapar du en ExpressRoute-krets genom att distribuera en mall:

1. Välj **prova** med följande kodblock och följ sedan anvisningarna för att logga in på Azure Cloud Shell.

    ```azurepowershell-interactive
    $circuitName = Read-Host -Prompt "Enter a circuit name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${circuitName}rg"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-expressroute-circuit-create/azuredeploy.json"

    $serviceProviderName = "Equinix"
    $peeringLocation = "Silicon Valley"
    $bandwidthInMbps = 500
    $sku_tier = "Premium"
    $sku_family = "MeteredData"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -circuitName $circuitName -serviceProviderName $serviceProviderName -peeringLocation $peeringLocation -bandwidthInMbps $bandwidthInMbps -sku_tier $sku_tier -sku_family $sku_family

    Write-Host "Press [ENTER] to continue ..."
    ```

   * **SKU-nivån** avgör om en ExpressRoute-krets är [lokal](expressroute-faqs.md#expressroute-local), standard eller [Premium](expressroute-faqs.md#expressroute-premium). Du kan ange *lokal*, * standard eller *Premium*. Du kan inte ändra SKU: n från *Standard/Premium* till *lokal*.
   * **SKU-serien** bestämmer fakturerings typen. Du kan ange *Metereddata* för ett data abonnemang med datapriser och *Unlimiteddata* för ett obegränsat data abonnemang. Du kan ändra fakturerings typen från *Metereddata* till *Unlimiteddata*, men du kan inte ändra typen från *Unlimiteddata* till *Metereddata*. En *lokal* krets är endast *Unlimiteddata* .
   * **Peering-platsen** är den fysiska plats där du peer-koppla med Microsoft.

     > [!IMPORTANT]
     > Peering-platsen anger den [fysiska plats](expressroute-locations.md) där du peer-koppla med Microsoft. Detta är **inte** länkat till "location"-egenskap som refererar till den geografi där Azure Network Resource-providern finns. Även om de inte är relaterade är det en bra idé att välja en nätverks resurs-Provider geografiskt nära peering-platsen för kretsen.

    Resurs gruppens namn är namnet på Service Bus-namnområdet med **RG** tillagt.

2. Välj **Kopiera** för att kopiera PowerShell-skriptet.
3. Högerklicka på Shell-konsolen och välj sedan **Klistra in**.

Det tar en stund att skapa en Event Hub.

Azure PowerShell används för att distribuera mallen i den här självstudien. För andra metoder för mall distribution, se:

* Med [hjälp av Azure Portal](../azure-resource-manager/templates/deploy-portal.md).
* Med [hjälp av Azure CLI](../azure-resource-manager/templates/deploy-cli.md).
* Med [hjälp av REST API](../azure-resource-manager/templates/deploy-rest.md).

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>Avetablera och ta bort en ExpressRoute-krets

Du kan ta bort ExpressRoute-kretsen genom att välja ikonen **ta bort** . Notera följande information:

* Du måste ta bort länken till alla virtuella nätverk från ExpressRoute-kretsen. Om den här åtgärden Miss lyckas kontrollerar du om några virtuella nätverk är länkade till kretsen.
* Om etablerings statusen för ExpressRoute-kretsen **etableras** eller **etableras** måste du arbeta med tjänst leverantören för att avetablera kretsen på sin sida. Vi fortsätter att reservera resurser och fakturera dig tills tjänste leverantören har slutfört avetableringen av kretsen och meddelar oss oss.
* Om tjänst leverantören har avetablerat kretsen (etablerings statusen för tjänst leverantören är inställd på **inte etablerad**) kan du ta bort kretsen. Därmed avbryts faktureringen för kretsen.

Du kan ta bort ExpressRoute-kretsen genom att köra följande PowerShell-kommando:

```azurepowershell-interactive
$circuitName = Read-Host -Prompt "Enter the same circuit name that you used earlier"
$resourceGroupName = "${circuitName}rg"

Remove-AzExpressRouteCircuit -ResourceGroupName $resourceGroupName -Name $circuitName
```

## <a name="next-steps"></a>Nästa steg

När du har skapat din krets fortsätter du med följande steg:

* [Skapa och ändra routning för din ExpressRoute-krets](expressroute-howto-routing-portal-resource-manager.md)
* [Länka ditt virtuella nätverk till din ExpressRoute-krets](expressroute-howto-linkvnet-arm.md)