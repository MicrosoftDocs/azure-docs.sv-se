---
title: Skriv om HTTP-huvuden i Azure Application Gateway
description: Den här artikeln innehåller information om hur du skriver om HTTP-huvuden i Azure Application Gateway med Azure PowerShell
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: how-to
ms.date: 04/12/2019
ms.author: absha
ms.openlocfilehash: 29ca3aff7d75c7a14bf7b325719924936762d191
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101711696"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-powershell"></a>Skriv om HTTP-begäran och svarshuvuden med Azure Application Gateway – Azure PowerShell

Den här artikeln beskriver hur du använder Azure PowerShell för att konfigurera en instans av [Application Gateway v2-SKU](./application-gateway-autoscaling-zone-redundant.md) för att skriva om HTTP-huvudena i begär Anden och svar.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="before-you-begin"></a>Innan du börjar

- Du måste köra Azure PowerShell lokalt för att kunna slutföra stegen i den här artikeln. Du måste också ha AZ-modul version 1.0.0 eller senare installerad. Kör `Import-Module Az` och `Get-Module Az` identifiera den version som du har installerat. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). När du har verifierat PowerShell-versionen kör du `Login-AzAccount` för att skapa en anslutning till Azure.
- Du måste ha en instans av Application Gateway v2-SKU. Omskrivning av rubriker stöds inte i v1 SKU. Om du inte har installerat v2-SKU: n skapar du en instans av [Application Gateway v2-SKU](./tutorial-autoscale-ps.md) innan du börjar.

## <a name="create-required-objects"></a>Skapa nödvändiga objekt

Om du vill konfigurera omskrivning av HTTP-huvud måste du slutföra de här stegen.

1. Skapa de objekt som krävs för omskrivning av HTTP-huvud:

   - **RequestHeaderConfiguration**: används för att ange de begär ande huvud fält som du tänker skriva om och det nya värdet för rubrikerna.

   - **ResponseHeaderConfiguration**: används för att ange de svars huvud fält som du tänker skriva om och det nya värdet för rubrikerna.

   - **ActionSet**: innehåller konfigurationerna för begär ande och svarshuvuden som har angetts tidigare.

   - **Villkor**: en valfri konfiguration. Omskrivnings villkor utvärderar innehållet i HTTP (S)-begär Anden och svar. Återskrivning görs om HTTP (S)-begäran eller-svaret matchar omskrivnings villkoret.

     Om du associerar fler än ett villkor med en åtgärd sker åtgärden endast när alla villkor är uppfyllda. Med andra ord är åtgärden ett logiskt och en åtgärd.

   - **RewriteRule**: innehåller kombinationer av flera omskrivnings åtgärder/omskrivnings villkor.

   - **RuleSequence**: en valfri konfiguration som hjälper till att fastställa i vilken ordning omskrivnings regler ska köras. Den här konfigurationen är användbar när du har flera omskrivnings regler i en omskrivnings uppsättning. En omskrivnings regel som har ett lägre regel ordnings värde körs först. Om du tilldelar samma regel ordnings värde till två omskrivnings regler är körnings ordningen icke-deterministisk.

     Om du inte anger RuleSequence explicit anges standardvärdet 100.

   - **RewriteRuleSet**: innehåller flera omskrivnings regler som ska associeras med en regel för anslutningsbegäran.

2. Koppla RewriteRuleSet till en regel för routning. Den omskrivna konfigurationen är kopplad till käll lyssnaren via regeln för routning. När du använder en regel för grundläggande routning associeras konfigurationen för omskrivning av huvuden med en käll lyssnare och är en omskrivning av globala huvuden. När du använder en regel för Sök vägs-baserad routning definieras konfigurationen för att skriva över rubriker i sökvägen till URL-sökvägen. I så fall gäller det bara för det angivna Sök vägs området på en plats.

Du kan skapa flera Skriv-och skriv åtgärder för HTTP-huvudet och tillämpa varje skrivnings uppsättning på flera lyssnare. Men du kan endast använda en omskrivnings uppsättning för en speciell lyssnare.

## <a name="sign-in-to-azure"></a>Logga in på Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-the-http-header-rewrite-rule-configuration"></a>Ange en regel konfiguration för att skriva om HTTP-huvud

I det här exemplet ändrar vi en URL för omdirigering genom att skriva om plats rubriken i HTTP-svaret när plats huvudet innehåller en referens till azurewebsites.net. Det gör du genom att lägga till ett villkor för att utvärdera om plats rubriken i svaret innehåller azurewebsites.net. Vi använder mönstret `(https?)://.*azurewebsites.net(.*)$` . Vi ska använda `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` som huvud värde. Det här värdet kommer att ersätta *azurewebsites.net* med *contoso.com* i plats rubriken.

```azurepowershell
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Location" -HeaderValue "{http_resp_Location_1}://contoso.com{http_resp_Location_2}"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration
$condition = New-AzApplicationGatewayRewriteRuleCondition -Variable "http_resp_Location" -Pattern "(https?):\/\/.*azurewebsites\.net(.*)$" -IgnoreCase
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name LocationHeader -ActionSet $actionSet
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name LocationHeaderRewrite -RewriteRule $rewriteRule
```

## <a name="retrieve-the-configuration-of-your-application-gateway"></a>Hämta konfigurationen för Application Gateway

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-the-configuration-of-your-request-routing-rule"></a>Hämta konfigurationen för regeln för anslutningsbegäran

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>Uppdatera programgatewayen med konfigurationen för omskrivning av HTTP-rubriker

```azurepowershell
Add-AzApplicationGatewayRewriteRuleSet -ApplicationGateway $appgw -Name LocationHeaderRewrite -RewriteRule $rewriteRuleSet.RewriteRules
Set-AzApplicationGatewayRequestRoutingRule -ApplicationGateway $appgw -Name rule1 -RuleType $reqRoutingRule.RuleType -BackendHttpSettingsId $reqRoutingRule.BackendHttpSettings.Id -HttpListenerId $reqRoutingRule.HttpListener.Id -BackendAddressPoolId $reqRoutingRule.BackendAddressPool.Id -RewriteRuleSetId $rewriteRuleSet.Id
Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="delete-a-rewrite-rule"></a>Ta bort en omskrivnings regel

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
Remove-AzApplicationGatewayRewriteRuleSet -Name "LocationHeaderRewrite" -ApplicationGateway $appgw
$requestroutingrule= Get-AzApplicationGatewayRequestRoutingRule -Name "rule1" -ApplicationGateway $appgw
$requestroutingrule.RewriteRuleSet= $null
set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="next-steps"></a>Nästa steg

Mer information om hur du konfigurerar några vanliga användnings fall finns i [vanliga scenarier](./rewrite-http-headers.md)för att skriva om huvuden.