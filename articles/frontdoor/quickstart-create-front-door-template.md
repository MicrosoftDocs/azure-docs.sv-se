---
title: 'Snabbstart: Skapa en Azure Front Door Service med hjälp av en Azure Resource Manager (ARM-mall)'
description: Den här snabbstarten beskriver hur du skapar en Azure Front Door Service med hjälp Azure Resource Manager mall (ARM-mall).
services: front-door
documentationcenter: ''
author: duongau
ms.author: duau
editor: ''
ms.date: 09/14/2020
ms.topic: quickstart
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: de8a592f6eecbb43b58a044096e8ba2e0f9b5973
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538994"
---
# <a name="quickstart-create-a-front-door-using-an-arm-template"></a>Snabbstart: Skapa en Front Door med hjälp av en ARM-mall

Den här snabbstarten beskriver hur du använder en Azure Resource Manager-mall (ARM-mall) för att skapa en Front Door för att konfigurera hög tillgänglighet för en webbslutpunkt.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-front-door-create-basic%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
* IP eller FQDN för en webbplats eller ett webbprogram.

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-front-door-create-basic).

I den här snabbstarten skapar du en Front Door med en enda server och en standardsökväg som matchar `/*` .

:::code language="json" source="~/quickstart-templates/101-front-door-create-basic/azuredeploy.json":::

En Azure-resurs har definierats i mallen:

* [**Microsoft.Network/frontDoors**](/azure/templates/microsoft.network/frontDoors)

## <a name="deploy-the-template"></a>Distribuera mallen

1. Välj **Prova från** följande kodblock för att öppna Azure Cloud Shell och följ sedan anvisningarna för att logga in på Azure.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-front-door-create-basic/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    Vänta tills du ser prompten från konsolen.

1. Välj **Kopiera** från föregående kodblock för att kopiera PowerShell-skriptet.

1. Högerklicka på gränssnittskonsolfönstret och välj klistra **in**.

1. Ange värdena.

    Malldistributionen skapar en Front Door med en enda backend. I det här `microsoft.com` exemplet används som **backendAddress**.

    Resursgruppens namn är projektnamnet med **rg** tillagt.

    > [!NOTE]
    > **frontDoorName** måste vara ett globalt unikt namn för att mallen ska kunna distribueras. Om distributionen misslyckas börjar du om med steg 1.

    Det tar några minuter att distribuera mallen. När du är klar ser utdata ut ungefär så här:

    :::image type="content" source="./media/quickstart-create-front-door-template/front-door-template-deployment-powershell-output.png" alt-text="Front Door Resource Manager PowerShell-distributionsutdata för mall":::

Azure PowerShell används för att distribuera mallen. Förutom att Azure PowerShell kan du även använda Azure Portal, Azure CLI och REST API. Mer information om andra distributionsmetoder finns i [Distribuera mallar.](../azure-resource-manager/templates/deploy-portal.md)

## <a name="validate-the-deployment"></a>Verifiera distributionen

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj **Resursgrupper** i det vänstra fönstret.

1. Välj den resursgrupp som du skapade i föregående avsnitt. Standardresursgruppens namn är projektnamnet med **rg** tillagt.

1. Välj det Front Door som du skapade tidigare och klicka på **länken Frontend host (Värd för frontend).** Länken öppnar en webbläsare som omdirigerar dig till det FQDN för backend som du definierade när du skapade den.

    :::image type="content" source="./media/quickstart-create-front-door-template/front-door-overview.png" alt-text="Front Door översikt över Front Door portalen":::

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver Front Door tjänsten tar du bort resursgruppen. Detta tar Front Door och alla relaterade resurser.

Om du vill ta bort resursgruppen anropar du `Remove-AzResourceGroup` cmdleten :

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en Front Door.

Om du vill lära dig hur du lägger till en anpassad domän Front Door kan du fortsätta till Front Door självstudierna.

> [!div class="nextstepaction"]
> [Front Door självstudier](front-door-custom-domain.md)
