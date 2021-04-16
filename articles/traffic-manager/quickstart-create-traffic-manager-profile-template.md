---
title: 'Snabbstart: Skapa en Traffic Manager med hjälp Azure Resource Manager mall (ARM-mall)'
description: Den här snabbstartsartikeln beskriver hur du skapar en Azure Traffic Manager-profil med hjälp Azure Resource Manager mall (ARM-mall).
services: traffic-manager
author: duongau
ms.author: duau
ms.date: 09/01/2020
ms.topic: quickstart
ms.service: traffic-manager
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 03342d33731ed29b60908044cd29d529aaa5677b
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531250"
---
# <a name="quickstart-create-a-traffic-manager-profile-using-an-arm-template"></a>Snabbstart: Skapa en Traffic Manager med en ARM-mall

Den här snabbstarten beskriver hur du använder en Azure Resource Manager-mall (ARM-mall) för att skapa en Traffic Manager-profil med externa slutpunkter med hjälp av prestandaroutningsmetoden.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-traffic-manager-external-endpoint%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-traffic-manager-external-endpoint).

:::code language="json" source="~/quickstart-templates/101-traffic-manager-external-endpoint/azuredeploy.json":::

En Azure-resurs har definierats i mallen:

* [**Microsoft.Network/trafficManagerProfiles**](/azure/templates/microsoft.network/trafficmanagerprofiles)

Om du vill hitta fler mallar som är relaterade Azure Traffic Manager finns i [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Distribuera mallen

1. Välj **Prova från** följande kodblock för att öppna Azure Cloud Shell och följ sedan anvisningarna för att logga in på Azure.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-traffic-manager-external-endpoint/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    Vänta tills du ser prompten från konsolen.

1. Välj **Kopiera** från föregående kodblock för att kopiera PowerShell-skriptet.

1. Högerklicka på gränssnittskonsolfönstret och välj klistra **in**.

1. Ange värdena.

    Malldistributionen skapar en profil med två externa slutpunkter. **Endpoint1** använder en målslutpunkt `www.microsoft.com` för med platsen i **Europa, norra.** **Endpoint2** använder en målslutpunkt `docs.microsoft.com` för med platsen i **USA, södra centrala.**

    Resursgruppens namn är projektnamnet med **rg** tillagt.

    > [!NOTE]
    > **uniqueDNSname** måste vara ett globalt unikt namn för att mallen ska kunna distribueras. Om distributionen misslyckas börjar du om med steg 1.

    Det tar några minuter att distribuera mallen. När du är klar ser utdata ut ungefär så här:

    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/traffic-manager-arm-powershell-output.png" alt-text="Azure Traffic Manager Resource Manager PowerShell-distributionsutdata för mall":::

Azure PowerShell används för att distribuera mallen. Förutom att Azure PowerShell kan du även använda Azure Portal, Azure CLI och REST API. Mer information om andra distributionsmetoder finns i [Distribuera mallar.](../azure-resource-manager/templates/deploy-portal.md)

## <a name="validate-the-deployment"></a>Verifiera distributionen

1. Fastställ DNS-namnet för Traffic Manager profilen med [hjälp av Get-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/get-aztrafficmanagerprofile).

    ```azurepowershell-interactive
    Get-AzTrafficManagerProfile -Name ExternalEndpointExample -ResourceGroupName $resourceGroupName | Select RelativeDnsName
    ```

    Kopiera värdet **RelativeDnsName.** DNS-namnet för din Traffic Manager profil är `<relativednsname>.trafficmanager.net` .

1. Kör följande kommando från en lokal PowerShell genom att ersätta **variabeln {relativeDNSname}** med `<relativednsname>.trafficmanager.net` .

    ```powershell
    Resolve-DnsName -Name {relativeDNSname} | Select-Object NameHost | Select -First 1
    ```

    Du bör få en NameHost för `www.microsoft.com` antingen `docs.microsoft.com` eller beroende på vilken region som är närmare dig.

1. Om du vill kontrollera om du kan matcha med den andra slutpunkten inaktiverar du slutpunkten för målet som du fick i det senaste steget. Ersätt **{endpointName} med** **antingen endpoint1** **eller endpoint2** för att inaktivera målet `www.microsoft.com` för `docs.microsoft.com` eller.

    ```azurepowershell-interactive
    Disable-AzTrafficManagerEndpoint -Name {endpointName} -Type ExternalEndpoints -ProfileName ExternalEndpointExample -ResourceGroupName $resourceGroupName -Force
    ```

1. Kör kommandot från steg 2 igen i ett lokalt PowerShell. Den här gången bör du hämta den andra NameHost för den andra slutpunkten.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver Traffic Manager profil tar du bort resursgruppen. Detta tar Traffic Manager och alla relaterade resurser.

Om du vill ta bort resursgruppen anropar du `Remove-AzResourceGroup` cmdleten :

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en Traffic Manager-profil.

Om du vill veta mer om att dirigera trafik kan du gå vidare till Traffic Manager-självstudierna.

> [!div class="nextstepaction"]
> [Självstudier för Traffic Manager](tutorial-traffic-manager-improve-website-response.md)
