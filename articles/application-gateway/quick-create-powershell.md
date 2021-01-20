---
title: 'Snabb start: direkt webb trafik med hjälp av PowerShell'
titleSuffix: Azure Application Gateway
description: I den här snabb starten får du lära dig hur du använder Azure PowerShell för att skapa en Azure Application-Gateway som dirigerar webb trafik till virtuella datorer i en backend-pool.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 01/19/2021
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 8073d1e18b08a6deb0175f8eaf18de382e93e299
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2021
ms.locfileid: "98601845"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway-using-azure-powershell"></a>Snabb start: direkt webb trafik med Azure Application Gateway med Azure PowerShell

I den här snabb starten använder du Azure PowerShell för att skapa en Programgateway. Sedan testar du det för att kontrol lera att det fungerar korrekt. 

Application Gateway dirigerar program webb trafik till specifika resurser i en backend-pool. Du tilldelar lyssnare till portar, skapar regler och lägger till resurser i en backend-pool. För enkelhetens skull använder den här artikeln en enkel installation med en offentlig IP-adress för klient delen, en grundläggande lyssnare som är värd för en enda plats på programgatewayen, en grundläggande regel för routning av begäran och två virtuella datorer i backend-poolen.

Du kan också slutföra den här snabb starten med [Azure CLI](quick-create-cli.md) eller [Azure Portal](quick-create-portal.md).

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Azure PowerShell version 1.0.0 eller senare](/powershell/azure/install-az-ps) (om du kör Azure PowerShell lokalt).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="connect-to-azure"></a>Anslut till Azure

Kör för att ansluta till Azure `Connect-AzAccount` .

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

I Azure allokerar du relaterade resurser till en resursgrupp. Du kan antingen använda en befintlig resurs grupp eller skapa en ny.

Använd cmdleten för att skapa en ny resurs grupp `New-AzResourceGroup` : 

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```
## <a name="create-network-resources"></a>Skapa nätverksresurser

För att Azure ska kunna kommunicera mellan resurserna som du skapar krävs ett virtuellt nätverk.  Undernätet för en programgateway kan endast innehålla programgatewayer. Inga andra resurser är tillåtna.  Du kan antingen skapa ett nytt undernät för Application Gateway eller använda ett befintligt. Du skapar två undernät i det här exemplet: ett för programgatewayen och ett för backend-servrarna. Du kan konfigurera klient delens IP-adress för Application Gateway att vara offentlig eller privat enligt ditt användnings fall. I det här exemplet väljer du en offentlig IP-adress för klient delen.

1. Skapa under näts konfigurationerna med hjälp av `New-AzVirtualNetworkSubnetConfig` .
2. Skapa det virtuella nätverket med under näts konfigurationerna med hjälp av `New-AzVirtualNetwork` . 
3. Skapa den offentliga IP-adressen med hjälp av `New-AzPublicIpAddress` . 
> [!NOTE]
> [Slut punkts principer för virtuella nätverk](../virtual-network/virtual-network-service-endpoint-policies-overview.md) stöds för närvarande inte i ett Application Gateway-undernät.

```azurepowershell-interactive
$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.1.0/24
$backendSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.2.0/24
New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $agSubnetConfig, $backendSubnetConfig
New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Static `
  -Sku Standard
```
## <a name="create-an-application-gateway"></a>Skapa en programgateway

### <a name="create-the-ip-configurations-and-frontend-port"></a>Skapa IP-konfigurationerna och klientdelsporten

1. Använd `New-AzApplicationGatewayIPConfiguration` för att skapa den konfiguration som associerar det undernät som du skapade med Application Gateway. 
2. Använd `New-AzApplicationGatewayFrontendIPConfig` för att skapa den konfiguration som tilldelar den offentliga IP-adressen som du skapade tidigare för Application Gateway. 
3. Använd `New-AzApplicationGatewayFrontendPort` för att tilldela port 80 för åtkomst till Application Gateway.

```azurepowershell-interactive
$vnet   = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name myAGSubnet
$pip    = Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress 
$gipconfig = New-AzApplicationGatewayIPConfiguration `
  -Name myAGIPConfig `
  -Subnet $subnet
$fipconfig = New-AzApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -PublicIPAddress $pip
$frontendport = New-AzApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 80
```

### <a name="create-the-backend-pool"></a>Skapa serverdelspoolen

1. Använd `New-AzApplicationGatewayBackendAddressPool` för att skapa backend-poolen för Application Gateway. Backend-poolen är tom för tillfället. När du skapar backend-serverns nätverkskort i nästa avsnitt lägger du till dem i backend-poolen.
2. Konfigurera inställningarna för backend-poolen med `New-AzApplicationGatewayBackendHttpSetting` .

```azurepowershell-interactive
$backendPool = New-AzApplicationGatewayBackendAddressPool `
  -Name myAGBackendPool
$poolSettings = New-AzApplicationGatewayBackendHttpSetting `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 30
```

### <a name="create-the-listener-and-add-a-rule"></a>Skapa lyssnaren och lägga till en regel

Azure kräver att en lyssnare aktiverar programgatewayen för korrekt dirigering av trafiken till serverdelspoolen. Azure kräver även en regel för att lyssnaren ska veta vilken serverdelspool som ska användas för inkommande trafik. 

1. Skapa en lyssnare med hjälp av `New-AzApplicationGatewayHttpListener` klient dels konfigurationen och frontend-porten som du skapade tidigare. 
2. Används `New-AzApplicationGatewayRequestRoutingRule` för att skapa en regel med namnet *regel 1*. 

```azurepowershell-interactive
$defaultlistener = New-AzApplicationGatewayHttpListener `
  -Name myAGListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport
$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultlistener `
  -BackendAddressPool $backendPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>Skapa programgatewayen

Nu när du har skapat de nödvändiga stödresurserna skapar du programgatewayen:

1. Använd `New-AzApplicationGatewaySku` för att ange parametrar för Application Gateway.
2. Använd `New-AzApplicationGateway` för att skapa programgatewayen.

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_v2 `
  -Tier Standard_v2 `
  -Capacity 2
New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $backendPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $defaultlistener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku
```

### <a name="backend-servers"></a>Serverdelsservrar

Nu när du har skapat Application Gateway skapar du de virtuella server dels datorer som ska vara värdar för webbplatserna. En server del kan bestå av nätverkskort, skalnings uppsättningar för virtuella datorer, offentlig IP-adress, intern IP-adress, fullständigt kvalificerade domän namn (FQDN) och backend-ändar för flera klienter som Azure App Service. 

I det här exemplet skapar du två virtuella datorer som ska användas som backend-servrar för programgatewayen. Du installerar även IIS på de virtuella datorerna för att verifiera att Azure har skapat programgatewayen.

#### <a name="create-two-virtual-machines"></a>Skapa två virtuella datorer

1. Hämta den nyligen skapade Application Gateway konfigurationen för backend-poolen med `Get-AzApplicationGatewayBackendAddressPool` .
2. Skapa ett nätverks gränssnitt med `New-AzNetworkInterface` .
3. Skapa en konfiguration för virtuell dator med `New-AzVMConfig` .
4. Skapa den virtuella datorn med `New-AzVM` .

När du kör följande kodexempel för att skapa virtuella datorer uppmanas du av Azure att ange autentiseringsuppgifter. Ange ett användar namn och ett lösen ord:
    
```azurepowershell-interactive
$appgw = Get-AzApplicationGateway -ResourceGroupName myResourceGroupAG -Name myAppGateway
$backendPool = Get-AzApplicationGatewayBackendAddressPool -Name myAGBackendPool -ApplicationGateway $appgw
$vnet   = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name myBackendSubnet
$cred = Get-Credential
for ($i=1; $i -le 2; $i++)
{
  $nic = New-AzNetworkInterface `
    -Name myNic$i `
    -ResourceGroupName myResourceGroupAG `
    -Location EastUS `
    -Subnet $subnet `
    -ApplicationGatewayBackendAddressPool $backendpool
  $vm = New-AzVMConfig `
    -VMName myVM$i `
    -VMSize Standard_DS2_v2
  Set-AzVMOperatingSystem `
    -VM $vm `
    -Windows `
    -ComputerName myVM$i `
    -Credential $cred
  Set-AzVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
  Add-AzVMNetworkInterface `
    -VM $vm `
    -Id $nic.Id
  Set-AzVMBootDiagnostic `
    -VM $vm `
    -Disable
  New-AzVM -ResourceGroupName myResourceGroupAG -Location EastUS -VM $vm
  Set-AzVMExtension `
    -ResourceGroupName myResourceGroupAG `
    -ExtensionName IIS `
    -VMName myVM$i `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
    -Location EastUS
}
```

## <a name="test-the-application-gateway"></a>Testa programgatewayen

Även om IIS inte krävs för att skapa programgatewayen installerar du den i den här snabb starten för att kontrol lera om Azure har skapat programgatewayen.

Använd IIS för att testa programgatewayen:

1. Kör `Get-AzPublicIPAddress` för att hämta den offentliga IP-adressen för programgatewayen. 
2. Kopiera och klistra in den offentliga IP-adressen i webbläsarens adressfält. När du uppdaterar webbläsaren bör du se namnet på den virtuella datorn. Ett giltigt svar verifierar att Application Gateway har skapats och kan ansluta till Server delen.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Testa programgatewayen](./media/quick-create-powershell/application-gateway-iistest.png)


## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver de resurser som du skapade med Application Gateway, tar du bort resurs gruppen. När du tar bort resurs gruppen tar du även bort programgatewayen och dess relaterade resurser. 

Anropa cmdleten om du vill ta bort resurs gruppen `Remove-AzResourceGroup` :

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hantera webbtrafik med en programgateway med Azure PowerShell](./tutorial-manage-web-traffic-powershell.md)

