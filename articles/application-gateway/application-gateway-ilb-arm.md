---
title: Använd med intern Load Balancer-Azure Application Gateway
description: Den här sidan innehåller anvisningar för hur du skapar, konfigurerar, startar och tar bort en Azure-programgateway med en intern lastbalanserare (ILB) med hjälp av Azure Resource Manager
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: 3d663dc4e2bd860ec9494785ecbf6dbf10a4c5b5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93397764"
---
# <a name="create-an-application-gateway-with-an-internal-load-balancer-ilb"></a>Skapa en Programgateway med en intern belastningsutjämnare (ILB)

Azure Application Gateway kan konfigureras med en Internetuppkopplad VIP eller med en intern slutpunkt som inte är exponerad för Internet, även kallad en ILB-slutpunkt (intern lastbalanserare). Det kan vara praktiskt att konfigurera gatewayen med en ILB för interna affärsprogram som inte är exponerade för Internet. Det är också användbart för tjänster och nivåer i ett program med flera nivåer som är i en säkerhets gränser som inte är utsatt för Internet men som fortfarande kräver resursallokering (Round-Robin), varaktighet eller Transport Layer Security (TLS), som tidigare kallades Secure Sockets Layer (SSL), avslutning.

Den här artikeln beskriver steg för steg hur du konfigurerar en programgateway med en ILB.

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Installera den senaste versionen av Azure PowerShell-modulen genom att följa [installations anvisningarna](/powershell/azure/install-az-ps).
2. Du ska skapa ett virtuellt nätverk och ett undernät för Application Gateway. Kontrollera att inga virtuella datorer eller molndistributioner använder undernätet. Application Gateway måste vara fristående i ett virtuellt nätverks undernät.
3. De servrar som du konfigurerar för användning av programgatewayen måste finnas i det virtuella nätverket eller ha slutpunkter som skapats där eller tilldelats en offentlig IP-/VIP-adress.

## <a name="what-is-required-to-create-an-application-gateway"></a>Vad krävs för att skapa en programgateway?

* **Backend-serverpool:** Listan med IP-adresser för backend-servrarna. IP-adresserna som anges måste antingen höra till det virtuella nätverket men i ett annat undernät för programgatewayen eller vara en offentlig IP/VIP.
* **Inställningar för Server delens Server grupp:** Varje pool har inställningar som port, protokoll och cookie-baserad tillhörighet. Dessa inställningar är knutna till en pool och tillämpas på alla servrar i poolen.
* **Klient dels port:** Den här porten är den offentliga porten som öppnas på Application Gateway. Trafiken kommer till den här porten och omdirigeras till en av backend-servrarna.
* **Lyssnare:** Lyssnaren har en frontend-port, ett protokoll (Http eller Https; dessa är skiftlägeskänsliga) och SSL-certifikatnamnet (om du konfigurerar SSL-avlastning).
* **Regel:** Regeln binder lyssnaren och backend-serverpoolen och definierar vilken backend-serverpool som trafiken ska dirigeras till när den når en viss lyssnare. För närvarande stöds endast regeln *basic*. Regeln *basic* använder belastningsutjämning med resursallokering.

## <a name="create-an-application-gateway"></a>Skapa en programgateway

Skillnaden mellan att använda den klassiska Azure-portalen och Azure Resource Manager är i vilken ordning du skapar programgatewayen och de objekt som ska konfigureras.
Med Resource Manager konfigureras alla objekt som bildar en programgateway separat och sätts sedan ihop för att skapa en programgatewayresurs.

Här följer de steg som krävs för att skapa en programgateway:

1. Skapa en resursgrupp för Resource Manager
2. Skapa ett virtuellt nätverk och ett undernät för programgatewayen
3. Skapa ett konfigurationsobjekt för programgatewayen
4. Skapa en resurs för en programgateway

## <a name="create-a-resource-group-for-resource-manager"></a>Skapa en resursgrupp för Resource Manager

Glöm inte att byta PowerShell-läge så att du kan använda cmdlets för Azure Resource Manager. Mer information finns i [Använda Windows PowerShell med Resource Manager](../azure-resource-manager/management/manage-resources-powershell.md).

### <a name="step-1"></a>Steg 1

```powershell
Connect-AzAccount
```

### <a name="step-2"></a>Steg 2

Kontrollera prenumerationerna för kontot.

```powershell
Get-AzSubscription
```

Du ombeds att autentisera dig med dina autentiseringsuppgifter.

### <a name="step-3"></a>Steg 3

Välj vilka av dina Azure-prenumerationer som du vill använda.

```powershell
Select-AzSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>Steg 4

Skapa en ny resursgrupp (hoppa över detta steg om du använder en befintlig resursgrupp).

```powershell
New-AzResourceGroup -Name appgw-rg -location "West US"
```

Azure Resource Manager kräver att alla resursgrupper anger en plats. Den här platsen används som standardplats för resurser i resursgruppen. Se till att alla kommandon du använder för att skapa en programgateway använder samma resursgrupp.

I föregående exempel skapade vi en resurs grupp med namnet "appgw-RG" och platsen "västra USA".

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Skapa ett virtuellt nätverk och ett undernät för programgatewayen

Följande exempel illustrerar hur du skapar ett virtuellt nätverk med hjälp av Resource Manager:

### <a name="step-1"></a>Steg 1

```powershell
$subnetconfig = New-AzVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

Det här steget tilldelar adress intervallet 10.0.0.0/24 till en under näts variabel som ska användas för att skapa ett virtuellt nätverk.

### <a name="step-2"></a>Steg 2

```powershell
$vnet = New-AzVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnetconfig
```

Det här steget skapar ett virtuellt nätverk med namnet "appgwvnet" i resurs gruppen "appgw-RG" för regionen Västra USA med prefixet 10.0.0.0/16 med under nätet 10.0.0.0/24.

### <a name="step-3"></a>Steg 3

```powershell
$subnet = $vnet.subnets[0]
```

Det här steget tilldelar under näts objekt till variabel $subnet för nästa steg.

## <a name="create-an-application-gateway-configuration-object"></a>Skapa ett konfigurationsobjekt för programgatewayen

### <a name="step-1"></a>Steg 1

```powershell
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

Det här steget skapar en programgateways IP-konfiguration med namnet "gatewayIP01". När Application Gateway startar hämtar den en IP-adress från det konfigurerade undernätet och dirigerar nätverkstrafik till IP-adresserna i backend-IP-poolen. Tänk på att varje instans använder en IP-adress.

### <a name="step-2"></a>Steg 2

```powershell
$pool = New-AzApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 10.1.1.8,10.1.1.9,10.1.1.10
```

Det här steget konfigurerar backend-IP-adresspoolen med namnet "pool01" med IP-adresser "10.1.1.8, 10.1.1.9, 10.1.1.10". Det här är IP-adresserna som tar emot nätverkstrafiken som kommer från frontend-IP-slutpunkten. Du ersätter de omnämnda IP-adresserna och lägger till ditt eget programs IP-adresslutpunkter.

### <a name="step-3"></a>Steg 3

```powershell
$poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled
```

Det här steget konfigurerar Programgateway-inställningen "poolsetting01" för den belastningsutjämnade nätverks trafiken i backend-poolen.

### <a name="step-4"></a>Steg 4

```powershell
$fp = New-AzApplicationGatewayFrontendPort -Name frontendport01  -Port 80
```

I det här steget konfigureras klient delens IP-port med namnet "frontendport01" för ILB.

### <a name="step-5"></a>Steg 5

```powershell
$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name fipconfig01 -Subnet $subnet
```

Det här steget skapar klient delens IP-konfiguration med namnet "fipconfig01" och associerar den med en privat IP-adress från det aktuella virtuella nätverkets undernät.

### <a name="step-6"></a>Steg 6

```powershell
$listener = New-AzApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp
```

Det här steget skapar lyssnaren med namnet "listener01" och associerar frontend-porten med klient delens IP-konfiguration.

### <a name="step-7"></a>Steg 7

```powershell
$rule = New-AzApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

I det här steget skapas Routningsprincipen för belastnings utjämning med namnet "rule01" som konfigurerar belastnings Utjämnings beteendet.

### <a name="step-8"></a>Steg 8

```powershell
$sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
```

I det här steget konfigureras instans storleken för Application Gateway.

> [!NOTE]
> Standardvärdet för kapacitet är 2. Du kan välja mellan Standard_Small, Standard_Medium och Standard_Large för SKU-namn.

## <a name="create-an-application-gateway-by-using-new-azureapplicationgateway"></a>Skapa en programgateway med hjälp av New-AzureApplicationGateway

Skapar en Programgateway med alla konfigurations objekt från föregående steg. I det här exemplet heter programgatewayen ”appgwtest”.

```powershell
$appgw = New-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

Det här steget skapar en Programgateway med alla konfigurations objekt från föregående steg. I det här exemplet heter programgatewayen ”appgwtest”.

## <a name="delete-an-application-gateway"></a>Ta bort en programgateway

Om du vill ta bort en Programgateway måste du utföra följande steg i ordning:

1. Stoppa gatewayen med hjälp av cmdleten `Stop-AzApplicationGateway`.
2. Ta bort gatewayen med hjälp av cmdleten `Remove-AzApplicationGateway`.
3. Kontrollera att gatewayen har tagits bort med hjälp av cmdleten `Get-AzureApplicationGateway`.

### <a name="step-1"></a>Steg 1

Hämta objektet för programgatewayen och associera det med variabeln ”$getgw”.

```powershell
$getgw =  Get-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

### <a name="step-2"></a>Steg 2

Använd `Stop-AzApplicationGateway` för att stoppa programgatewayen. I det här exemplet visas `Stop-AzApplicationGateway` cmdleten på den första raden, följt av utdata.

```powershell
Stop-AzApplicationGateway -ApplicationGateway $getgw  
```

```
VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error
----       ----------------     ------------                             ----
Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8
```

När programgatewayen är i ett stoppat läge använder du cmdleten `Remove-AzApplicationGateway` för att ta bort tjänsten.

```powershell
Remove-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Force
```

```
VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error
----       ----------------     ------------                             ----
Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301
```

> [!NOTE]
> Du kan använda växeln **-force** om du inte vill att några bekräftelsemeddelanden ska visas.

Du kan kontrollera att tjänsten har tagits bort genom att använda cmdleten `Get-AzApplicationGateway`. Det här steget är inte obligatoriskt.

```powershell
Get-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

```
VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
```

## <a name="next-steps"></a>Nästa steg

Om du vill konfigurera SSL-avlastning läser du [Konfigurera en programgateway för SSL-avlastning](./tutorial-ssl-powershell.md).

Om du vill ha mer information om belastningsutjämningsalternativ i allmänhet läser du:

* [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)