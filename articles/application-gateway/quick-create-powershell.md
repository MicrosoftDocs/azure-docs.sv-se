---
title: 'Snabbstart: Dirigera webbtrafik med PowerShell'
titleSuffix: Azure Application Gateway
description: I den här snabbstarten lär du dig hur du använder Azure PowerShell för att skapa en Azure Application Gateway som dirigerar webbtrafik till virtuella datorer i en serverdelspool.
services: application-gateway
author: vhorne
ms.author: victorh
ms.date: 01/19/2021
ms.topic: quickstart
ms.service: application-gateway
ms.custom:
- mvc
- mode-api
ms.openlocfilehash: a4aa16ba4334e5f1e035face9549f4c03ad3a14f
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538552"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway-using-azure-powershell"></a>Snabbstart: Dirigera webbtrafik med Azure Application Gateway med Azure PowerShell

I den här snabbstarten använder du Azure PowerShell för att skapa en programgateway. Sedan testar du den för att se till att den fungerar korrekt. 

Application Gateway dirigerar programwebbtrafik till specifika resurser i en backend-pool. Du tilldelar lyssnare till portar, skapar regler och lägger till resurser i en backend-pool. För enkelhetens skull använder den här artikeln en enkel konfiguration med en offentlig IP-adress för frontend, en grundläggande lyssnare som är värd för en enda plats på programgatewayen, en grundläggande routningsregel för förfrågningar och två virtuella datorer i serverpoolen.

Du kan också slutföra den här snabbstarten [med Hjälp av Azure CLI](quick-create-cli.md) eller [Azure Portal](quick-create-portal.md).

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Azure PowerShell version 1.0.0 eller senare](/powershell/azure/install-az-ps) (om du kör Azure PowerShell lokalt).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="connect-to-azure"></a>Anslut till Azure

Kör för att ansluta med `Connect-AzAccount` Azure.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

I Azure allokerar du relaterade resurser till en resursgrupp. Du kan antingen använda en befintlig resursgrupp eller skapa en ny.

Använd cmdleten för att skapa en `New-AzResourceGroup` ny resursgrupp: 

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```
## <a name="create-network-resources"></a>Skapa nätverksresurser

För att Azure ska kunna kommunicera mellan resurserna som du skapar krävs ett virtuellt nätverk.  Undernätet för en programgateway kan endast innehålla programgatewayer. Inga andra resurser är tillåtna.  Du kan antingen skapa ett nytt undernät för Application Gateway eller använda ett befintligt. Du skapar två undernät i det här exemplet: ett för programgatewayen och ett för backend-servrarna. Du kan konfigurera IP-adressen för Application Gateway vara offentlig eller privat enligt ditt användningsfall. I det här exemplet väljer du en IP-adress för offentlig frontend.

1. Skapa undernätskonfigurationerna med `New-AzVirtualNetworkSubnetConfig` .
2. Skapa det virtuella nätverket med undernätskonfigurationerna med hjälp av `New-AzVirtualNetwork` . 
3. Skapa den offentliga IP-adressen med `New-AzPublicIpAddress` . 
> [!NOTE]
> [Principer för tjänstslutpunkter för virtuellt](../virtual-network/virtual-network-service-endpoint-policies-overview.md) nätverk stöds för närvarande inte i Application Gateway undernät.

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

1. Använd `New-AzApplicationGatewayIPConfiguration` för att skapa konfigurationen som associerar det undernät som du skapade med programgatewayen. 
2. Använd `New-AzApplicationGatewayFrontendIPConfig` för att skapa konfigurationen som tilldelar den offentliga IP-adress som du skapade tidigare för programgatewayen. 
3. Använd `New-AzApplicationGatewayFrontendPort` för att tilldela port 80 för åtkomst till programgatewayen.

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

1. Använd `New-AzApplicationGatewayBackendAddressPool` för att skapa programgatewayens backend-pool. Backend-poolen är tom just nu. När du skapar nätverkskorten för serverdelen i nästa avsnitt lägger du till dem i serverdelspoolen.
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

1. Skapa en lyssnare med den konfiguration och port för `New-AzApplicationGatewayHttpListener` frontend som du skapade tidigare. 
2. Använd `New-AzApplicationGatewayRequestRoutingRule` för att skapa en regel med namnet *rule1*. 

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

1. Använd `New-AzApplicationGatewaySku` för att ange parametrar för programgatewayen.
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

Nu när du har skapat Application Gateway skapar du de virtuella serverdatorerna som ska vara värdar för webbplatserna. En server kan bestå av nätverkskort, VM-skalningsuppsättningar, offentliga IP-adresser, interna IP-adresser, fullständigt kvalificerade domännamn (FQDN) och serveruppsättningar för flera klienter som Azure App Service. 

I det här exemplet skapar du två virtuella datorer som ska användas som backend-servrar för programgatewayen. Du installerar även IIS på de virtuella datorerna för att verifiera att Azure har skapat programgatewayen.

#### <a name="create-two-virtual-machines"></a>Skapa två virtuella datorer

1. Hämta konfigurationen för den Application Gateway serverpoolen med `Get-AzApplicationGatewayBackendAddressPool` .
2. Skapa ett nätverksgränssnitt med `New-AzNetworkInterface` .
3. Skapa en konfiguration för virtuell dator med `New-AzVMConfig` .
4. Skapa den virtuella datorn med `New-AzVM` .

När du kör följande kodexempel för att skapa virtuella datorer uppmanas du av Azure att ange autentiseringsuppgifter. Ange ett användarnamn och ett lösenord:
    
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

Även om IIS inte krävs för att skapa programgatewayen installerade du den i den här snabbstarten för att kontrollera om Azure har skapat programgatewayen.

Använd IIS för att testa programgatewayen:

1. Kör `Get-AzPublicIPAddress` för att hämta den offentliga IP-adressen för programgatewayen. 
2. Kopiera och klistra in den offentliga IP-adressen i webbläsarens adressfält. När du uppdaterar webbläsaren bör du se namnet på den virtuella datorn. Ett giltigt svar verifierar att programgatewayen har skapats och att den kan ansluta till backend-datorn.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Testa programgatewayen](./media/quick-create-powershell/application-gateway-iistest.png)


## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver de resurser som du skapade med programgatewayen tar du bort resursgruppen. När du tar bort resursgruppen tar du även bort programgatewayen och alla dess relaterade resurser. 

Om du vill ta bort resursgruppen anropar du `Remove-AzResourceGroup` cmdleten :

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hantera webbtrafik med en programgateway med Azure PowerShell](./tutorial-manage-web-traffic-powershell.md)
