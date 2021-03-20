---
title: Skapa en Azure Application Gateway & skriva om HTTP-huvuden
description: Den här artikeln innehåller information om hur du skapar en Azure Application Gateway och skriver om HTTP-huvuden med Azure PowerShell
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/19/2019
ms.author: absha
ms.openlocfilehash: 4a1a122eb7b5b0abcc47cd321c74267a1a4aecda
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93396863"
---
# <a name="create-an-application-gateway-and-rewrite-http-headers"></a>Skapa en Application Gateway och skriv om HTTP-huvuden

Du kan använda Azure PowerShell för att konfigurera [regler för att skriva om HTTP-begäran och svarshuvuden](rewrite-http-headers.md) när du skapar den nya [autoskalning och den Zone-redundanta Application Gateway-SKU: n](./application-gateway-autoscaling-zone-redundant.md)

I den här artikeln kan du se hur du:

* Skapa ett virtuellt nätverk för autoskalning
* Skapa en reserverad offentlig IP-adress
* Konfigurera infrastrukturen för din application gateway
* Ange en regel konfiguration för att skriva över HTTP-huvud
* Ange automatisk skalning
* Skapa programgatewayen
* Testa programgatewayen

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln kräver att du kör Azure PowerShell lokalt. Du måste ha AZ-modul version 1.0.0 eller senare installerad. Kör `Import-Module Az` och sedan `Get-Module Az` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). När du har verifierat PowerShell-versionen kör du `Login-AzAccount` för att skapa en anslutning till Azure.

## <a name="sign-in-to-azure"></a>Logga in på Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp
Skapa en resursgrupp på en av de tillgängliga platserna.

```azurepowershell
$location = "East US 2"
$rg = "<rg name>"

#Create a new Resource Group
New-AzResourceGroup -Name $rg -Location $location
```

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Skapa ett virtuellt nätverk med ett dedikerat undernät för en programgateway för automatisk skalning. För närvarande går det bara att distribuera en programgateway för automatisk skalning i varje dedikerat undernät.

```azurepowershell
#Create VNet with two subnets
$sub1 = New-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -AddressPrefix "10.0.0.0/24"
$sub2 = New-AzVirtualNetworkSubnetConfig -Name "BackendSubnet" -AddressPrefix "10.0.1.0/24"
$vnet = New-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg `
       -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $sub1, $sub2
```

## <a name="create-a-reserved-public-ip"></a>Skapa en reserverad offentlig IP-adress

Ange allokeringsmetoden för PublicIPAddress som **Statisk**. En virtuell IP-adress för en programgateway för automatisk skalning kan bara vara statisk. Dynamiska IP-adresser stöds inte. Endast standard-SKU:n för PublicIpAddress stöds.

```azurepowershell
#Create static public IP
$pip = New-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP" `
       -location $location -AllocationMethod Static -Sku Standard
```

## <a name="retrieve-details"></a>Hämta information

Hämta information för resursgruppen, undernätet och IP-adressen i ett lokalt objekt för att skapa informationen om programgatewayens IP-konfiguration.

```azurepowershell
$resourceGroup = Get-AzResourceGroup -Name $rg
$publicip = Get-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP"
$vnet = Get-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -VirtualNetwork $vnet
```

## <a name="configure-the-infrastructure"></a>Konfigurera infrastrukturen

Konfigurera IP-konfigurationen, klient delens IP-konfiguration, backend-pool, HTTP-inställningar, certifikat, port och lyssnare i ett identiskt format till den befintliga standard Application Gateway. Den nya SKU:n följer samma objektmodell som standard-SKU:n.

```azurepowershell
$ipconfig = New-AzApplicationGatewayIPConfiguration -Name "IPConfig" -Subnet $gwSubnet
$fip = New-AzApplicationGatewayFrontendIPConfig -Name "FrontendIPCOnfig" -PublicIPAddress $publicip
$pool = New-AzApplicationGatewayBackendAddressPool -Name "Pool1" `
       -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com
$fp01 = New-AzApplicationGatewayFrontendPort -Name "HTTPPort" -Port 80

$listener01 = New-AzApplicationGatewayHttpListener -Name "HTTPListener" `
             -Protocol Http -FrontendIPConfiguration $fip -FrontendPort $fp01

$setting = New-AzApplicationGatewayBackendHttpSettings -Name "BackendHttpSetting1" `
          -Port 80 -Protocol Http -CookieBasedAffinity Disabled
```

## <a name="specify-your-http-header-rewrite-rule-configuration"></a>Ange en regel konfiguration för att skriva över HTTP-huvud

Konfigurera de nya objekt som krävs för att skriva om HTTP-huvudena:

- **RequestHeaderConfiguration**: det här objektet används för att ange de begär ande huvud fält som du tänker skriva om och det nya värdet som de ursprungliga huvudena måste skrivas om till.
- **ResponseHeaderConfiguration**: det här objektet används för att ange de svars huvud fält som du tänker skriva om och det nya värdet som de ursprungliga huvudena måste skrivas om till.
- **ActionSet**: det här objektet innehåller konfigurationerna för de begär ande och svars rubriker som anges ovan. 
- **RewriteRule**: det här objektet innehåller alla *actionSets* som anges ovan. 
- **RewriteRuleSet**– det här objektet innehåller alla *rewriteRules* och måste kopplas till en regel för routning av förfrågningar – Basic eller Path-based.

   ```azurepowershell
   $requestHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-isThroughProxy" -HeaderValue "True"
   $responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Strict-Transport-Security" -HeaderValue "max-age=31536000"
   $actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration    
   $rewriteRule = New-AzApplicationGatewayRewriteRule -Name rewriteRule1 -ActionSet $actionSet    
   $rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name rewriteRuleSet1 -RewriteRule $rewriteRule
   ```

## <a name="specify-the-routing-rule"></a>Ange regel för routning

Skapa en regel för routning av begäran. När den här konfigurationen har skapats kopplas den här omskrivnings konfigurationen till käll lyssnaren via regeln för routning. När du använder en regel för grundläggande routning associeras konfigurationen för omskrivning av huvuden med en käll lyssnare och är en omskrivning av globala huvuden. När en sökväg baserad routningsregler används definieras konfigurationen för att skriva över rubriker i sökvägen till URL-sökvägen. Detta gäller endast för det speciella Sök vägs området på en plats. Nedan skapas en grundläggande routningsprincip och den omskrivnings regel uppsättningen är kopplad.

```azurepowershell
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "Rule1" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener01 -BackendAddressPool $pool -RewriteRuleSet $rewriteRuleSet
```

## <a name="specify-autoscale"></a>Ange automatisk skalning

Nu kan du ange konfigurationen för automatisk skalning för programgatewayen. Två typer av konfiguration för automatisk skalning stöds:

* **Läget för bestämd kapacitet**. I det här läget skalas inte programgatewayen automatiskt och körs med en fast skalningsenhetskapacitet.

   ```azurepowershell
   $sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2 -Capacity 2
   ```

* **Läget för automatisk skalning**. I det här läget skalas programgatewayen automatiskt baserat på programmets trafikmönster.

   ```azurepowershell
   $autoscaleConfig = New-AzApplicationGatewayAutoscaleConfiguration -MinCapacity 2
   $sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
   ```

## <a name="create-the-application-gateway"></a>Skapa programgatewayen

Skapa programgatewayen och inkludera redundanszoner och konfigurationen för automatisk skalning.

```azurepowershell
$appgw = New-AzApplicationGateway -Name "AutoscalingAppGw" -Zone 1,2,3 -ResourceGroupName $rg -Location $location -BackendAddressPools $pool -BackendHttpSettingsCollection $setting -GatewayIpConfigurations $ipconfig -FrontendIpConfigurations $fip -FrontendPorts $fp01 -HttpListeners $listener01 -RequestRoutingRules $rule01 -Sku $sku -AutoscaleConfiguration $autoscaleConfig -RewriteRuleSet $rewriteRuleSet
```

## <a name="test-the-application-gateway"></a>Testa programgatewayen

Använd Get-AzPublicIPAddress för att hämta den offentliga IP-adressen för programgatewayen. Kopiera den offentliga IP-adressen eller DNS-namnet och klistra in det i webbläsarens adressfält.

```azurepowershell
Get-AzPublicIPAddress -ResourceGroupName $rg -Name AppGwVIP
```



## <a name="clean-up-resources"></a>Rensa resurser

Utforska först de resurser som har skapats med application gateway. När du inte behöver dem längre kan du använda kommandot `Remove-AzResourceGroup` för att ta bort resursgruppen, programgatewayen och alla relaterade resurser.

`Remove-AzResourceGroup -Name $rg`

## <a name="next-steps"></a>Nästa steg

- [Skapa en programgateway med webbadressbaserade routningsregler](./tutorial-url-route-powershell.md)