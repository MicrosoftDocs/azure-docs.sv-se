---
title: 'Snabb start: skapa en Azure frontend-tjänst genom att använda en Azure Resource Manager-mall (ARM-mall)'
description: I den här snabb starten beskrivs hur du skapar en Azure-frontend-tjänst genom att använda Azure Resource Manager mall (ARM-mall).
services: front-door
documentationcenter: ''
author: duongau
editor: ''
ms.assetid: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2020
ms.author: duau
ms.custom: subject-armqs
ms.openlocfilehash: 223006193219afe4179f3161d5e60e6439207b22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92896063"
---
# <a name="quickstart-create-a-front-door-using-an-arm-template"></a>Snabb start: skapa en front dörr med en ARM-mall

I den här snabb starten beskrivs hur du använder en Azure Resource Manager mall (ARM-mall) för att skapa en front dörr för att konfigurera hög tillgänglighet för en webb slut punkt.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-front-door-create-basic%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
* IP eller FQDN för en webbplats eller ett webb program.

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-front-door-create-basic).

I den här snabb starten skapar du en konfiguration med en front dörr med en enda server del och en enda standard Sök vägs matchning `/*` .

:::code language="json" source="~/quickstart-templates/101-front-door-create-basic/azuredeploy.json":::

En Azure-resurs har definierats i mallen:

* [**Microsoft. Network/frontDoors**](/azure/templates/microsoft.network/frontDoors)

## <a name="deploy-the-template"></a>Distribuera mallen

1. Välj **prova** från följande kodblock för att öppna Azure Cloud Shell och följ sedan anvisningarna för att logga in på Azure.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-front-door-create-basic/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    Vänta tills du ser prompten från-konsolen.

1. Kopiera PowerShell-skriptet genom att välja **Kopiera** från föregående kodblock.

1. Högerklicka på fönstret Shell-konsol och välj sedan **Klistra in**.

1. Ange värdena.

    Mallen för distribution skapar en frontend-dörr med en enda server del. I det här exemplet `microsoft.com` används som **backendAddress**.

    Resurs gruppens namn är projekt namnet med **RG** tillagt.

    > [!NOTE]
    > **frontDoorName** måste vara ett globalt unikt namn för att mallen ska kunna distribueras korrekt. Om distributionen Miss lyckas börjar du med steg 1.

    Det tar några minuter att distribuera mallen. När det är slutfört ser utdata ut ungefär så här:

    :::image type="content" source="./media/quickstart-create-front-door-template/front-door-template-deployment-powershell-output.png" alt-text="Start dörr Resource Manager-mall PowerShell-distribution av utdata":::

Azure PowerShell används för att distribuera mallen. Förutom Azure PowerShell kan du också använda Azure Portal, Azure CLI och REST API. Mer information om andra distributions metoder finns i [distribuera mallar](../azure-resource-manager/templates/deploy-portal.md).

## <a name="validate-the-deployment"></a>Verifiera distributionen

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj **resurs grupper** i det vänstra fönstret.

1. Välj den resurs grupp som du skapade i föregående avsnitt. Standard resurs grupps namnet är projekt namnet med **RG** tillagt.

1. Välj den främre dörren som du skapade tidigare och klicka på **klient dels värd** länken. Länken öppnar en webbläsare som omdirigerar dig till ditt FQDN-namn som du definierade när du skapade.

    :::image type="content" source="./media/quickstart-create-front-door-template/front-door-overview.png" alt-text="Översikt över front dörrs portalen":::

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver tjänsten frontend, tar du bort resurs gruppen. Detta tar bort den främre dörren och alla relaterade resurser.

Anropa cmdleten om du vill ta bort resurs gruppen `Remove-AzResourceGroup` :

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du skapat en front dörr.

Om du vill lära dig hur du lägger till en anpassad domän i din frontend-guide kan du fortsätta till självstudierna för front dörren.

> [!div class="nextstepaction"]
> [Självstudier för front dörr](front-door-custom-domain.md)
