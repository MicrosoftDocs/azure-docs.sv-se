---
title: 'Snabbstart: Konfigurera hög tillgänglighet med Azure Front Door – Azure PowerShell'
description: Den här snabbstarten visar hur du använder Azure Front Door för att skapa en global webbapp med hög tillgänglighet och höga prestanda med Azure PowerShell.
services: front-door
documentationcenter: na
author: duongau
ms.author: duau
manager: KumudD
ms.date: 04/19/2021
ms.topic: quickstart
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom:
- mode-api
ms.openlocfilehash: 17fa18e1f29622b941c281b9cdce27f6e72eb13a
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739988"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application-using-azure-powershell"></a>Snabbstart: Skapa en Front Door för en global webbapp med hög Azure PowerShell

Kom igång med Azure Front Door hjälp av Azure PowerShell för att skapa ett globalt webbprogram med hög kapacitet och höga prestanda.

Den Front Door dirigerar webbtrafik till specifika resurser i en backend-pool. Du har definierat frontend-domänen, lagt till resurser i en serverpool och skapat en routningsregel. Den här artikeln använder en enkel konfiguration av en serverdelspool med två webbappresurser och en enda routningsregel med standardsökvägen som matchar "/*".

:::image type="content" source="media/quickstart-create-front-door/environment-diagram.png" alt-text="Diagram över Front Door miljödiagram med PowerShell." border="false":::

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure PowerShell lokalt eller Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-resource-group"></a>Skapa resursgrupp

I Azure allokerar du relaterade resurser till en resursgrupp. Du kan antingen använda en befintlig resursgrupp eller skapa en ny.

Skapa en resursgrupp med [New-AzResourceGroup:](/powershell/module/az.resources/new-azresourcegroup)

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupFD -Location centralus
```

## <a name="create-two-instances-of-a-web-app"></a>Skapa två instanser av en webbapp

Den här snabbstarten kräver två instanser av ett webbprogram som körs i olika Azure-regioner. Båda webbprograminstanserna körs i aktivt/aktivt läge, så att någon av dem kan ta trafik. Den här konfigurationen skiljer sig från en aktiv/fristående konfiguration, där en fungerar som en redundans.

Om du inte redan har en webbapp kan du använda följande skript för att konfigurera två exempelwebbappar.

```azurepowershell-interactive
# Create first web app in Central US region.
$webapp1 = New-AzWebApp `
-Name "WebAppContoso-1" `
-Location centralus `
-ResourceGroupName myResourceGroupFD `
-AppServicePlan myAppServicePlanCentralUS

# Create second web app in South Central US region.
$webapp2 = New-AzWebApp `
-Name "WebAppContoso-2" `
-Location southcentralus `
-ResourceGroupName myResourceGroupFD `
-AppServicePlan myAppServicePlanEastUS
```

## <a name="create-a-front-door"></a>Skapa en Front Door

Det här avsnittet beskriver hur du kan skapa och konfigurera följande komponenter i Front Door:
    
* Ett frontend-objekt innehåller Front Door standarddomänen.
* En backend-pool är en uppsättning motsvarande backends som Front Door belastningsutjämnar din klientbegäran.
* En routningsregel mappar din frontend-värd och matchande URL-sökvägsmönster till en specifik serverpool.

### <a name="create-a-frontend-object"></a>Skapa ett frontend-objekt

Frontend-objektet konfigurerar värdnamnet för Front Door. Som standard har värdnamnet suffixet **.azurefd.net*.

```azurepowershell-interactive
# Create a unique name
$fdname = "contoso-frontend-$(Get-Random)"

#Create the frontend object
$FrontendEndObject = New-AzFrontDoorFrontendEndpointObject `
-Name "frontendEndpoint1" `
-HostName $fdname".azurefd.net"
```

### <a name="create-the-backend-pool"></a>Skapa serverdelspoolen

Backend-poolen består av de två webbappen som skapades i början av den här snabbstarten. Inställningarna för hälsoavsökning och belastningsutjämning som definieras i det här steget använder standardvärden.

```azurepowershell-interactive
# Create backend objects that points to the hostname of the web apps
$backendObject1 = New-AzFrontDoorBackendObject `
-Address $webapp1.DefaultHostName
$backendObject2 = New-AzFrontDoorBackendObject `
-Address $webapp2.DefaultHostName

# Create a health probe object
$HealthProbeObject = New-AzFrontDoorHealthProbeSettingObject `
-Name "HealthProbeSetting"

# Create the load balancing setting object
$LoadBalancingSettingObject = New-AzFrontDoorLoadBalancingSettingObject `
-Name "Loadbalancingsetting" `
-SampleSize "4" `
-SuccessfulSamplesRequired "2" `
-AdditionalLatencyInMilliseconds "0"

# Create a backend pool using the backend objects, health probe, and load balancing settings
$BackendPoolObject = New-AzFrontDoorBackendPoolObject `
-Name "myBackendPool" `
-FrontDoorName $fdname `
-ResourceGroupName myResourceGroupFD `
-Backend $backendObject1,$backendObject2 `
-HealthProbeSettingsName "HealthProbeSetting" `
-LoadBalancingSettingsName "Loadbalancingsetting"
```

### <a name="create-a-routing-rule"></a>Skapa en routningsregel

Routningsregeln mappar serverpoolen till frontend-domänen och anger standardvärdet för sökvägsmatchning till "/*".

```azurepowershell-interactive
# Create a routing rule mapping the frontend host to the backend pool
$RoutingRuleObject = New-AzFrontDoorRoutingRuleObject `
-Name LocationRule `
-FrontDoorName $fdname `
-ResourceGroupName myResourceGroupFD `
-FrontendEndpointName "frontendEndpoint1" `
-BackendPoolName "myBackendPool" `
-PatternToMatch "/*"
```
### <a name="create-the-front-door"></a>Skapa Front Door

Nu när du har skapat de nödvändiga objekten skapar du Front Door:

```azurepowershell-interactive
# Creates the Front Door
New-AzFrontDoor `
-Name $fdname `
-ResourceGroupName myResourceGroupFD `
-RoutingRule $RoutingRuleObject `
-BackendPool $BackendPoolObject `
-FrontendEndpoint $FrontendEndObject `
-LoadBalancingSetting $LoadBalancingSettingObject `
-HealthProbeSetting $HealthProbeObject
```

När distributionen är lyckad kan du testa den genom att följa stegen i nästa avsnitt.

## <a name="test-the-front-door"></a>Testa Front Door

Kör följande kommandon för att hämta värdnamnet för Front Door.

```azurepowershell-interactive
# Gets Front Door in resource group and output the hostname of the frontend domain.
$fd = Get-AzFrontDoor -ResourceGroupName myResourceGroupFD
$fd.FrontendEndpoints[0].Hostname
```

Öppna en webbläsare och ange det värdnamn som du får från kommandona. Den Front Door dirigerar din begäran till en av backend-resurserna. 

:::image type="content" source="./media/quickstart-create-front-door-powershell/front-door-test-page.png" alt-text="Front Door testsida":::

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver de resurser som du skapade med Front Door kan du ta bort resursgruppen. När du tar bort resursgruppen tar du även bort Front Door alla relaterade resurser. 

Om du vill ta bort resursgruppen anropar du `Remove-AzResourceGroup` cmdleten :

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupFD
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en:
* Front Door
* Två webbappar

Om du vill lära dig hur du lägger till en anpassad domän Front Door kan du fortsätta till Front Door självstudierna.

> [!div class="nextstepaction"]
> [Lägga till en anpassad domän](front-door-custom-domain.md)
