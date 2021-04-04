---
title: Använda API Management i Virtual Network med Application Gateway
titleSuffix: Azure API Management
description: Lär dig att konfigurera och konfigurera Azure API Management i interna Virtual Network med Application Gateway (WAF) som klient del
services: api-management
documentationcenter: ''
author: solankisamir
manager: kjoshi
editor: vlvinogr
ms.assetid: a8c982b2-bca5-4312-9367-4a0bbc1082b1
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: sasolank
ms.openlocfilehash: 3db1c8bfc3a11151342589af0873d88e3d90c6a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91825620"
---
# <a name="integrate-api-management-in-an-internal-vnet-with-application-gateway"></a>Integrera API Management i ett internt VNET med Application Gateway

## <a name="overview"></a><a name="overview"></a> Översikt

API Managements tjänsten kan konfigureras i en Virtual Network i internt läge, vilket gör den endast tillgänglig från Virtual Network. Azure Application Gateway är en PAAS-tjänst som tillhandahåller en Layer-7-belastningsutjämnare. Den fungerar som en omvänd proxy-tjänst och tillhandahåller en brand vägg för webbaserade program (WAF).

Om du kombinerar API Management som har skapats i ett internt virtuellt nätverk med Application Gateway-frontend kan du använda följande scenarier:

* Använd samma API Management-resurs för användning av både interna konsumenter och externa konsumenter.
* Använd en enda API Management resurs och har en delmängd av API: er definierade i API Management tillgängliga för externa konsumenter.
* Tillhandahålla ett sätt att byta till API Management från det offentliga Internet på och av.

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

För att följa stegen som beskrivs i den här artikeln måste du ha:

* En aktiv Azure-prenumeration.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

* Certifikat – PFX och CER för API hostname och PFX för Developer-portalens värdnamn.

## <a name="scenario"></a><a name="scenario"></a> Scenario

Den här artikeln beskriver hur du använder en enskild API Management tjänst för både interna och externa konsumenter och gör att den fungerar som en enda klient del för både lokala och molnbaserade API: er. Du kommer också att se hur du exponerar endast en delmängd av dina API: er (i exemplet är de markerade i grönt) för extern förbrukning med hjälp av routningsfunktioner som är tillgängliga i Application Gateway.

I det första installations exemplet hanteras bara dina API: er från din Virtual Network. Interna konsumenter (markerade i orange) kan komma åt alla dina interna och externa API: er. Trafiken hamnar aldrig på Internet. Anslutning med hög prestanda levereras via Express Route-kretsar.

![URL-väg](./media/api-management-howto-integrate-internal-vnet-appgateway/api-management-howto-integrate-internal-vnet-appgateway.png)

## <a name="before-you-begin"></a><a name="before-you-begin"></a> Innan du börjar

* Kontrollera att du använder den senaste versionen av Azure PowerShell. Se installations anvisningarna vid [installationen Azure PowerShell](/powershell/azure/install-az-ps). 

## <a name="what-is-required-to-create-an-integration-between-api-management-and-application-gateway"></a>Vad krävs för att skapa en integration mellan API Management och Application Gateway?

* **Backend-serverpoolen:** Det här är den interna virtuella IP-adressen för den API Management tjänsten.
* **Inställningar för Server delens Server grupp:** Varje pool har inställningar som port, protokoll och cookie-baserad tillhörighet. De här inställningarna tillämpas på alla servrar i poolen.
* **Klient dels port:** Detta är den offentliga porten som öppnas på Application Gateway. Trafiken som påträffas omdirigeras till en av backend-servrarna.
* **Lyssnare:** Lyssnaren har en frontend-port, ett protokoll (http eller https, dessa värden är Skift läges känsliga) och TLS/SSL-certifikatets namn (om du konfigurerar TLS-avlastning).
* **Regel:** Regeln binder en lyssnare till backend-serverpoolen.
* **Anpassad hälso avsökning:** Application Gateway används som standard IP-baserade avsökningar för att ta reda på vilka servrar i BackendAddressPool som är aktiva. Tjänsten API Management svarar bara på begär Anden med rätt värd huvud, och därför fungerar inte standard avsökningarna. En anpassad hälso avsökning måste definieras för att hjälpa Application Gateway att fastställa att tjänsten är aktiv och att den vidarebefordrar begär Anden.
* **Anpassade domän certifikat:** För att få åtkomst till API Management från Internet måste du skapa en CNAME-mappning av sitt värdnamn till Application Gateway DNS-namn på klient sidan. Detta säkerställer att värd namns huvudet och certifikatet som skickas till Application Gateway som vidarebefordras till API Management är ett APIM som kan identifieras som giltigt. I det här exemplet ska vi använda två certifikat – för Server delen och för Developer-portalen.  

## <a name="steps-required-for-integrating-api-management-and-application-gateway"></a><a name="overview-steps"></a> Steg som krävs för att integrera API Management och Application Gateway

1. Skapa en resursgrupp för Resource Manager.
2. Skapa en Virtual Network, ett undernät och en offentlig IP-adress för Application Gateway. Skapa ett annat undernät för API Management.
3. Skapa en API Management tjänst i VNET-undernätet som skapades ovan och se till att du använder det interna läget.
4. Konfigurera ett anpassat domän namn i API Managements tjänsten.
5. Skapa ett konfigurations objekt för Application Gateway.
6. Skapa en Application Gateway-resurs.
7. Skapa ett CNAME från det offentliga DNS-namnet för Application Gateway till API Management proxy-värdnamn.

## <a name="exposing-the-developer-portal-externally-through-application-gateway"></a>Exponerar Developer-portalen externt genom Application Gateway

I den här guiden kommer vi också att exponera **utvecklings portalen** för externa mål grupper via Application Gateway. Det krävs ytterligare steg för att skapa en utvecklares Portals lyssnare, avsökning, inställningar och regler. Alla detaljer finns i respektive steg.

> [!WARNING]
> Om du använder autentisering med Azure AD eller tredje part, aktiverar du funktionen [cookie-baserad sessionsgräns](../application-gateway/features.md#session-affinity) i Application Gateway.

> [!WARNING]
> Om du vill förhindra att Application Gateway WAF avbryter hämtningen av OpenAPI-specifikationen i Developer-portalen måste du inaktivera brand Väggs regeln `942200 - "Detects MySQL comment-/space-obfuscated injections and backtick termination"` .
> 
> Application Gateway WAF-regler, som kan bryta portalens funktioner:
> 
> - `920300`,,,,,,, `920330` `931130` `942100` `942110` `942180` `942200` `942260` `942340` `942370` för administrations läge
> - `942200`,,, `942260` `942370` `942430` , `942440` för den publicerade portalen

## <a name="create-a-resource-group-for-resource-manager"></a>Skapa en resursgrupp för Resource Manager

### <a name="step-1"></a>Steg 1

Logga in på Azure

```powershell
Connect-AzAccount
```

Autentisera med dina autentiseringsuppgifter.

### <a name="step-2"></a>Steg 2

Välj önskad prenumeration.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000" # GUID of your Azure subscription
Get-AzSubscription -Subscriptionid $subscriptionId | Select-AzSubscription
```

### <a name="step-3"></a>Steg 3

Skapa en resursgrupp (hoppa över detta steg om du använder en befintlig resursgrupp).

```powershell
$resGroupName = "apim-appGw-RG" # resource group name
$location = "West US"           # Azure region
New-AzResourceGroup -Name $resGroupName -Location $location
```

Azure Resource Manager kräver att alla resursgrupper anger en plats. Den här platsen används som standardplats för resurser i resursgruppen. Se till att alla kommandon för att skapa en Application Gateway använder samma resurs grupp.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Skapa en Virtual Network och ett undernät för Application Gateway

I följande exempel visas hur du skapar en Virtual Network med hjälp av Resource Manager.

### <a name="step-1"></a>Steg 1

Tilldela adress intervallet 10.0.0.0/24 till den under näts variabel som ska användas för Application Gateway när du skapar en Virtual Network.

```powershell
$appgatewaysubnet = New-AzVirtualNetworkSubnetConfig -Name "apim01" -AddressPrefix "10.0.0.0/24"
```

### <a name="step-2"></a>Steg 2

Tilldela adress intervallet 10.0.1.0/24 till den under näts variabel som ska användas för API Management när du skapar en Virtual Network.

```powershell
$apimsubnet = New-AzVirtualNetworkSubnetConfig -Name "apim02" -AddressPrefix "10.0.1.0/24"
```

### <a name="step-3"></a>Steg 3

Skapa en Virtual Network med namnet **appgwvnet** i resurs gruppen **APIM-appGw-RG** för regionen USA, västra. Använd prefixet 10.0.0.0/16 med under nätet 10.0.0.0/24 och 10.0.1.0/24.

```powershell
$vnet = New-AzVirtualNetwork -Name "appgwvnet" -ResourceGroupName $resGroupName -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $appgatewaysubnet,$apimsubnet
```

### <a name="step-4"></a>Steg 4

Tilldela en under näts variabel för nästa steg

```powershell
$appgatewaysubnetdata = $vnet.Subnets[0]
$apimsubnetdata = $vnet.Subnets[1]
```

## <a name="create-an-api-management-service-inside-a-vnet-configured-in-internal-mode"></a>Skapa en API Management tjänst inuti ett virtuellt nätverk som kon figurer ATS i internt läge

I följande exempel visas hur du skapar en API Management tjänst i ett virtuellt nätverk som är konfigurerat för intern åtkomst.

### <a name="step-1"></a>Steg 1

Skapa ett API Management Virtual Network-objekt med hjälp av under nätet $apimsubnetdata som du skapade ovan.

```powershell
$apimVirtualNetwork = New-AzApiManagementVirtualNetwork -SubnetResourceId $apimsubnetdata.Id
```

### <a name="step-2"></a>Steg 2

Skapa en API Management-tjänst inuti Virtual Network.

```powershell
$apimServiceName = "ContosoApi"       # API Management service instance name
$apimOrganization = "Contoso"         # organization name
$apimAdminEmail = "admin@contoso.com" # administrator's email address
$apimService = New-AzApiManagement -ResourceGroupName $resGroupName -Location $location -Name $apimServiceName -Organization $apimOrganization -AdminEmail $apimAdminEmail -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Developer"
```

När kommandot ovan har slutförts refererar den [DNS-konfiguration som krävs för att komma åt den interna VNET API Management-tjänsten](api-management-using-with-internal-vnet.md#apim-dns-configuration) att komma åt den. Det här steget kan ta mer än en halvtimme.

## <a name="set-up-a-custom-domain-name-in-api-management"></a>Konfigurera ett anpassat domän namn i API Management

> [!IMPORTANT]
> Den [nya Developer-portalen](api-management-howto-developer-portal.md) kräver också att du aktiverar anslutning till API Managements hanterings slut punkt utöver stegen nedan.

### <a name="step-1"></a>Steg 1

Initiera följande variabler med information om certifikaten med privata nycklar för domänerna. I det här exemplet ska vi använda `api.contoso.net` och `portal.contoso.net` .  

```powershell
$gatewayHostname = "api.contoso.net"                 # API gateway host
$portalHostname = "portal.contoso.net"               # API developer portal host
$gatewayCertCerPath = "C:\Users\Contoso\gateway.cer" # full path to api.contoso.net .cer file
$gatewayCertPfxPath = "C:\Users\Contoso\gateway.pfx" # full path to api.contoso.net .pfx file
$portalCertPfxPath = "C:\Users\Contoso\portal.pfx"   # full path to portal.contoso.net .pfx file
$gatewayCertPfxPassword = "certificatePassword123"   # password for api.contoso.net pfx certificate
$portalCertPfxPassword = "certificatePassword123"    # password for portal.contoso.net pfx certificate

$certPwd = ConvertTo-SecureString -String $gatewayCertPfxPassword -AsPlainText -Force
$certPortalPwd = ConvertTo-SecureString -String $portalCertPfxPassword -AsPlainText -Force
```

### <a name="step-2"></a>Steg 2

Skapa och ange värd namns konfigurations objekt för proxyservern och för portalen.  

```powershell
$proxyHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $gatewayHostname -HostnameType Proxy -PfxPath $gatewayCertPfxPath -PfxPassword $certPwd
$portalHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $portalHostname -HostnameType DeveloperPortal -PfxPath $portalCertPfxPath -PfxPassword $certPortalPwd

$apimService.ProxyCustomHostnameConfiguration = $proxyHostnameConfig
$apimService.PortalCustomHostnameConfiguration = $portalHostnameConfig
Set-AzApiManagement -InputObject $apimService
```

> [!NOTE]
> Om du vill konfigurera den äldre anslutningen för utvecklare-portalen måste du ersätta `-HostnameType DeveloperPortal` med `-HostnameType Portal` .

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Skapa en offentlig IP-adress för frontend-konfigurationen

Skapa en offentlig IP- **publicIP01** i resurs gruppen.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $resGroupName -name "publicIP01" -location $location -AllocationMethod Dynamic
```

En IP-adress tilldelas till programgatewayen när tjänsten startas.

## <a name="create-application-gateway-configuration"></a>Skapa Application Gateway-konfiguration

Alla konfigurationsobjekt måste konfigureras innan programgatewayen skapas. Följande steg skapar konfigurationsobjekten som behövs för en programgatewayresurs.

### <a name="step-1"></a>Steg 1

Skapa en IP-konfiguration för programgatewayen med namnet **gatewayIP01**. När Application Gateway startar hämtar den en IP-adress från det konfigurerade undernätet och dirigerar nätverkstrafik till IP-adresserna i backend-IP-poolen. Tänk på att varje instans använder en IP-adress.

```powershell
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "gatewayIP01" -Subnet $appgatewaysubnetdata
```

### <a name="step-2"></a>Steg 2

Konfigurera klient delens IP-port för den offentliga IP-slutpunkten. Den här porten är den port som slutanvändarna ansluter till.

```powershell
$fp01 = New-AzApplicationGatewayFrontendPort -Name "port01"  -Port 443
```

### <a name="step-3"></a>Steg 3

Konfigurera klientdelens IP med den offentliga IP-slutpunkten.

```powershell
$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>Steg 4

Konfigurera certifikaten för Application Gateway, som ska användas för att dekryptera och kryptera om trafiken som passerar genom.

```powershell
$cert = New-AzApplicationGatewaySslCertificate -Name "cert01" -CertificateFile $gatewayCertPfxPath -Password $certPwd
$certPortal = New-AzApplicationGatewaySslCertificate -Name "cert02" -CertificateFile $portalCertPfxPath -Password $certPortalPwd
```

### <a name="step-5"></a>Steg 5

Skapa HTTP-lyssnare för Application Gateway. Tilldela dem klient delens IP-konfiguration, port och TLS/SSL-certifikat.

```powershell
$listener = New-AzApplicationGatewayHttpListener -Name "listener01" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $cert -HostName $gatewayHostname -RequireServerNameIndication true
$portalListener = New-AzApplicationGatewayHttpListener -Name "listener02" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $certPortal -HostName $portalHostname -RequireServerNameIndication true
```

### <a name="step-6"></a>Steg 6

Skapa anpassade avsökningar till domän slut punkten för API Management tjänstens `ContosoApi` proxy. Sökvägen `/status-0123456789abcdef` är en standard hälso slut punkt som finns på alla API Management-tjänster. Ange `api.contoso.net` som ett anpassat avsöknings-värdnamn för att skydda det med TLS/SSL-certifikatet.

> [!NOTE]
> Hostname `contosoapi.azure-api.net` är standardvärdet för proxy som konfigureras när en tjänst med namnet `contosoapi` skapas i en offentlig Azure.
>

```powershell
$apimprobe = New-AzApplicationGatewayProbeConfig -Name "apimproxyprobe" -Protocol "Https" -HostName $gatewayHostname -Path "/status-0123456789abcdef" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
$apimPortalProbe = New-AzApplicationGatewayProbeConfig -Name "apimportalprobe" -Protocol "Https" -HostName $portalHostname -Path "/internal-status-0123456789abcdef" -Interval 60 -Timeout 300 -UnhealthyThreshold 8
```

### <a name="step-7"></a>Steg 7

Överför certifikatet som ska användas i de TLS-aktiverade resurserna för backend-poolen. Det här är samma certifikat som du angav i steg 4 ovan.

```powershell
$authcert = New-AzApplicationGatewayAuthenticationCertificate -Name "whitelistcert1" -CertificateFile $gatewayCertCerPath
```

### <a name="step-8"></a>Steg 8

Konfigurera inställningar för HTTP-backend för Application Gateway. Detta omfattar att ange en tids gräns för backend-begäran, efter vilken de har avbrutits. Det här värdet skiljer sig från timeout-värdet för avsökning.

```powershell
$apimPoolSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimprobe -AuthenticationCertificates $authcert -RequestTimeout 180
$apimPoolPortalSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolPortalSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimPortalProbe -AuthenticationCertificates $authcert -RequestTimeout 180
```

### <a name="step-9"></a>Steg 9

Konfigurera en backend-IP-adresspool med namnet **apimbackend**  med den interna virtuella IP-adressen för den API Management tjänst som skapades ovan.

```powershell
$apimProxyBackendPool = New-AzApplicationGatewayBackendAddressPool -Name "apimbackend" -BackendIPAddresses $apimService.PrivateIPAddresses[0]
```

### <a name="step-10"></a>Steg 10

Skapa regler för Application Gateway för att använda grundläggande routning.

```powershell
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType Basic -HttpListener $listener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolSetting
$rule02 = New-AzApplicationGatewayRequestRoutingRule -Name "rule2" -RuleType Basic -HttpListener $portalListener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolPortalSetting
```

> [!TIP]
> Ändra RuleType och routning, för att begränsa åtkomsten till vissa sidor i Developer-portalen.

### <a name="step-11"></a>Steg 11

Konfigurera antalet instanser och storlek för Application Gateway. I det här exemplet använder vi [WAF SKU](../web-application-firewall/ag/ag-overview.md) för att öka säkerheten för den API Management resursen.

```powershell
$sku = New-AzApplicationGatewaySku -Name "WAF_Medium" -Tier "WAF" -Capacity 2
```

### <a name="step-12"></a>Steg 12

Konfigurera WAF så att det är i "förebyggande" läge.

```powershell
$config = New-AzApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>Skapa Application Gateway

Skapa en Application Gateway med alla konfigurations objekt från föregående steg.

```powershell
$appgwName = "apim-app-gw"
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $resGroupName -Location $location -BackendAddressPools $apimProxyBackendPool -BackendHttpSettingsCollection $apimPoolSetting, $apimPoolPortalSetting  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener, $portalListener -RequestRoutingRules $rule01, $rule02 -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert, $certPortal -AuthenticationCertificates $authcert -Probes $apimprobe, $apimPortalProbe
```

## <a name="cname-the-api-management-proxy-hostname-to-the-public-dns-name-of-the-application-gateway-resource"></a>CNAME API Management proxy-värdnamnet till det offentliga DNS-namnet för den Application Gateway resursen

När du har skapat gatewayen, är nästa steg att konfigurera klientprogrammet för kommunikation. När du använder en offentlig IP-adress kräver Application Gateway ett dynamiskt tilldelat DNS-namn, vilket kanske inte är enkelt att använda.

Application Gatewayens DNS-namn ska användas för att skapa en CNAME-post som pekar på APIM (t. ex. `api.contoso.net` i exemplen ovan) till det här DNS-namnet. Om du vill konfigurera klient delens IP CNAME-post hämtar du information om Application Gateway och dess associerade IP/DNS-namn med hjälp av PublicIPAddress-elementet. Användning av A-poster rekommenderas inte eftersom VIP kan ändras vid omstart av gatewayen.

```powershell
Get-AzPublicIpAddress -ResourceGroupName $resGroupName -Name "publicIP01"
```

## <a name="summary"></a><a name="summary"></a> Sammanfattning
Azure API Management som kon figurer ATS i ett VNET tillhandahåller ett enda Gateway-gränssnitt för alla konfigurerade API: er, oavsett om de finns lokalt eller i molnet. Genom att integrera Application Gateway med API Management får du flexibiliteten att selektivt aktivera specifika API: er som kan nås via Internet, samt att tillhandahålla en brand vägg för webbaserade program som en klient del till din API Management-instans.

## <a name="next-steps"></a><a name="next-steps"></a> Nästa steg
* Läs mer om Azure Application Gateway
  * [Översikt över Application Gateway](../application-gateway/overview.md)
  * [Application Gateway brand vägg för webbaserade program](../web-application-firewall/ag/ag-overview.md)
  * [Application Gateway med hjälp av väg-baserad routning](../application-gateway/tutorial-url-route-powershell.md)
* Läs mer om API Management och virtuella nätverk
  * [Använda API Management endast tillgängligt i VNET](api-management-using-with-internal-vnet.md)
  * [Använda API Management i VNET](api-management-using-with-vnet.md)
