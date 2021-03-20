---
title: 'Snabb start: Konfigurera hög tillgänglighet med Azures frontend-Azure PowerShell'
description: Den här snabb starten visar hur du använder Azures front dörr för att skapa ett globalt och högpresterande globalt webb program med hjälp av Azure PowerShell.
services: front-door
documentationcenter: na
author: duongau
manager: KumudD
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.assetid: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/21/2020
ms.author: duau
ms.openlocfilehash: 8ccc50dfc4c53f1ee207a04d7d7775775f13cb65
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91349031"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application-using-azure-powershell"></a>Snabb start: skapa en front dörr för ett globalt webb program med hög tillgänglighet med hjälp av Azure PowerShell

Kom igång med Azures front dörr genom att använda Azure PowerShell för att skapa en hög tillgänglig global webbapp med hög prestanda.

Front dörren dirigerar webb trafik till vissa resurser i en backend-pool. Du definierade frontend-domänen, lägger till resurser i en backend-pool och skapar en regel för routning. Den här artikeln använder en enkel konfiguration av en backend-pool med två webb program resurser och en regel för routning med standard Sök väg som matchar "/*".

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure PowerShell installerat lokalt eller Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-resource-group"></a>Skapa resursgrupp

I Azure allokerar du relaterade resurser till en resursgrupp. Du kan antingen använda en befintlig resurs grupp eller skapa en ny.

Skapa en resurs grupp med [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupFD -Location centralus
```

## <a name="create-two-instances-of-a-web-app"></a>Skapa två instanser av en webbapp

Den här snabb starten kräver två instanser av ett webb program som körs i olika Azure-regioner. Båda instanserna av webb programmet körs i aktivt/aktivt läge, vilket innebär att det kan ta trafik. Den här konfigurationen skiljer sig från en aktiv/fristående konfiguration, där en fungerar som en redundansväxling.

Om du inte redan har en webbapp använder du följande skript för att konfigurera två exempel på webbappar.

```azurepowershell-interactive
# Create first web app in Central US region.
$webapp1 = New-AzWebApp `
-Name "WebAppContoso-$(Get-Random)" `
-Location centralus `
-ResourceGroupName myResourceGroupFD `
-AppServicePlan myAppServicePlanCentralUS

# Create second web app in South Central US region.
$webapp2 = New-AzWebApp `
-Name "WebAppContoso-$(Get-Random)" `
-Location southcentralus `
-ResourceGroupName myResourceGroupFD `
-AppServicePlan myAppServicePlanSouthCentralUS
```

## <a name="create-a-front-door"></a>Skapa en Front Door

I det här avsnittet beskrivs hur du kan skapa och konfigurera följande komponenter i front dörren:
    
* Ett klient-frontend-objekt innehåller standard domänen för den främre dörren.
* En backend-pool är en uppsättning motsvarande Server delar som belastningen på klient begär Anden balanserar.
* En routningstabell mappar din klient dels värd och matchande URL-sökmönster till en viss backend-pool.

### <a name="create-a-frontend-object"></a>Skapa ett klient dels objekt

Klient delens objekt konfigurerar värd namnet för den främre dörren. Värd namnet har som standard suffixet **. azurefd.net*.

```azurepowershell-interactive
# Create a unique name
$fdname = "contoso-frontend-$(Get-Random)"

#Create the frontend object
$FrontendEndObject = New-AzFrontDoorFrontendEndpointObject `
-Name "frontendEndpoint1" `
-HostName $fdname".azurefd.net"
```

### <a name="create-the-backend-pool"></a>Skapa serverdelspoolen

Backend-poolen består av de två webbappar som skapas i början av den här snabb starten. Inställningarna för hälso avsökning och belastnings utjämning som definierats i det här steget använder standardvärden.

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

Regeln för routning mappar backend-poolen till klient delens domän och anger standard Sök vägens matchnings värde till "/*".

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
### <a name="create-the-front-door"></a>Skapa den främre dörren

Nu när du har skapat de objekt som krävs skapar du en front dörr:

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

När distributionen har slutförts kan du testa den genom att följa anvisningarna i nästa avsnitt.

## <a name="test-the-front-door"></a>Testa front dörren

Kör följande kommandon för att hämta värd namnet för den främre dörren.

```azurepowershell-interactive
# Gets Front Door in resource group and output the hostname of the frontend domain.
$fd = Get-AzFrontDoor -ResourceGroupName myResourceGroupFD
$fd.FrontendEndpoints[0].Hostname
```

Öppna en webbläsare och ange värd namnet hämta från-kommandona. Front dörren dirigerar din begäran till en av Server dels resurserna. 

:::image type="content" source="./media/quickstart-create-front-door-powershell/front-door-test-page.png" alt-text="Test sida för front dörren":::

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resurs gruppen när du inte längre behöver de resurser som du skapade med frontend-dörren. När du tar bort resurs gruppen tar du även bort frontend-dörren och alla relaterade resurser. 

Anropa cmdleten om du vill ta bort resurs gruppen `Remove-AzResourceGroup` :

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupFD
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en:
* Front Door
* Två webb program

Om du vill lära dig hur du lägger till en anpassad domän i din frontend-guide kan du fortsätta till självstudierna för front dörren.

> [!div class="nextstepaction"]
> [Lägga till en anpassad domän](front-door-custom-domain.md)
