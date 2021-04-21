---
title: Använda API Management i Virtual Network med Application Gateway
titleSuffix: Azure API Management
description: Lär dig hur du konfigurerar Azure API Management i Intern Virtual Network med Application Gateway (WAF) som FrontEnd
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
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6500ecdb811306239951cb339abe2043d77b8cf2
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813082"
---
# <a name="integrate-api-management-in-an-internal-vnet-with-application-gateway"></a>Integrera API Management i ett internt VNET med Application Gateway

## <a name="overview"></a><a name="overview"></a> Översikt

Tjänsten API Management kan konfigureras i en Virtual Network i internt läge, vilket gör den endast tillgänglig från Virtual Network. Azure Application Gateway är en PAAS-tjänst som tillhandahåller en Layer-7-lastbalanserare. Den fungerar som en omvänd proxytjänst och tillhandahåller bland sina erbjudanden en Web Application Firewall (WAF).

Genom att API Management som etablerats i ett internt VNET med Application Gateway-Application Gateway kan du använda följande scenarier:

* Använd samma API Management för användning av både interna och externa konsumenter.
* Använd en API Management resurs och ha en delmängd AV API:er som definierats i API Management tillgänglig för externa konsumenter.
* Ange en nyckel för att växla åtkomst till API Management från det offentliga Internet på och av.

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Om du vill följa stegen som beskrivs i den här artikeln måste du ha:

* En aktiv Azure-prenumeration.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

* Certifikat – pfx och cer för API-värdnamnet och pfx för utvecklarportalens värdnamn.

## <a name="scenario"></a><a name="scenario"></a> Scenario

Den här artikeln beskriver hur du använder en enda API Management-tjänst för både interna och externa användare och gör att den fungerar som en enda frontend för både lokala och molnbaserade API:er. Du kommer också att se hur du bara exponerar en delmängd av dina API:er (i det här exemplet är de markerade i grönt) för extern förbrukning med hjälp av routningsfunktioner som är tillgängliga i Application Gateway.

I det första konfigurationsexempel hanteras alla dina API:er endast inifrån Virtual Network. Interna konsumenter (markerade i orange) kan komma åt alla dina interna och externa API:er. Trafiken går aldrig ut till Internet. Anslutningar med höga prestanda levereras via Express Route-kretsar.

![URL-väg](./media/api-management-howto-integrate-internal-vnet-appgateway/api-management-howto-integrate-internal-vnet-appgateway.png)

## <a name="before-you-begin"></a><a name="before-you-begin"></a> Innan du börjar

* Kontrollera att du använder den senaste versionen av Azure PowerShell. Se installationsanvisningarna på [Installera Azure PowerShell](/powershell/azure/install-az-ps). 

## <a name="what-is-required-to-create-an-integration-between-api-management-and-application-gateway"></a>Vad krävs för att skapa en integrering mellan API Management och Application Gateway?

* **Backend-serverpool:** Det här är den interna virtuella IP-adressen för API Management tjänsten.
* **Inställningar för backend-serverpool:** Varje pool har inställningar som port, protokoll och cookiebaserad tillhörighet. De här inställningarna tillämpas på alla servrar i poolen.
* **Frontend-port:** Det här är den offentliga porten som öppnas på programgatewayen. Trafik som når den omdirigeras till en av backend-servrarna.
* **Lyssnare:** Lyssnaren har en klientport, ett protokoll (Http eller Https, dessa värden är fallkänsliga) och TLS/SSL-certifikatnamnet (om du konfigurerar TLS-avlastning).
* **Regel:** Regeln binder en lyssnare till en backend-serverpool.
* **Anpassad hälsoavsökning:** Application Gateway använder ip-adressbaserade avsökningar som standard för att ta reda på vilka servrar i BackendAddressPool som är aktiva. Tjänsten API Management bara på begäranden med rätt värdhuvud, vilket gör att standardavsökningarna misslyckas. En anpassad hälsoavsökning måste definieras för att hjälpa Application Gateway att avgöra att tjänsten är levande och att den ska vidarebefordra begäranden.
* **Anpassade domäncertifikat:** För att API Management från Internet måste du skapa en CNAME-mappning av dess värdnamn till Application Gateway DNS-namnet på serversidan. Detta säkerställer att värdnamnets huvud och certifikat som skickas till Application Gateway som vidarebefordras till API Management är en APIM som kan identifiera som giltig. I det här exemplet använder vi två certifikat – för server och för utvecklarportalen.  

## <a name="steps-required-for-integrating-api-management-and-application-gateway"></a><a name="overview-steps"></a> Steg som krävs för att API Management och Application Gateway

1. Skapa en resursgrupp för Resource Manager.
2. Skapa en Virtual Network, undernät och offentlig IP-adress för Application Gateway. Skapa ett annat undernät för API Management.
3. Skapa en API Management i VNET-undernätet som skapades ovan och se till att du använder det interna läget.
4. Konfigurera ett anpassat domännamn i API Management tjänsten.
5. Skapa ett Application Gateway-konfigurationsobjekt.
6. Skapa en Application Gateway resurs.
7. Skapa ett CNAME från det offentliga DNS-namnet för Application Gateway till API Management proxyvärdnamn.

## <a name="exposing-the-developer-portal-externally-through-application-gateway"></a>Exponera utvecklarportalen externt via Application Gateway

I den här guiden kommer vi även att **exponera utvecklarportalen** för externa målgrupper via Application Gateway. Det krävs ytterligare steg för att skapa utvecklarportalens lyssnare, avsökning, inställningar och regler. All information finns i respektive steg.

> [!WARNING]
> Om du använder Azure AD eller autentisering från tredje part aktiverar du [funktionen cookiebaserad sessionstillhörighet](../application-gateway/features.md#session-affinity) i Application Gateway.

> [!WARNING]
> Om du Application Gateway waf från att bryta nedladdningen av OpenAPI-specifikationen i utvecklarportalen måste du inaktivera brandväggsregeln `942200 - "Detects MySQL comment-/space-obfuscated injections and backtick termination"` .
> 
> Application Gateway WAF-regler som kan bryta portalens funktioner är:
> 
> - `920300`, `920330` , , , , , , , , , `931130` för det `942100` administrativa `942110` `942180` `942200` `942260` `942340` `942370` läget
> - `942200`, `942260` , , , för den publicerade `942370` `942430` `942440` portalen

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

Azure Resource Manager kräver att alla resursgrupper anger en plats. Den här platsen används som standardplats för resurser i resursgruppen. Se till att alla kommandon för att skapa en programgateway använder samma resursgrupp.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Skapa en Virtual Network och ett undernät för programgatewayen

I följande exempel visas hur du skapar en Virtual Network med Resource Manager.

### <a name="step-1"></a>Steg 1

Tilldela adressintervallet 10.0.0.0/24 till undernätsvariabeln som ska användas för Application Gateway när du skapar Virtual Network.

```powershell
$appgatewaysubnet = New-AzVirtualNetworkSubnetConfig -Name "apim01" -AddressPrefix "10.0.0.0/24"
```

### <a name="step-2"></a>Steg 2

Tilldela adressintervallet 10.0.1.0/24 till undernätsvariabeln som ska användas för API Management när du skapar Virtual Network.

```powershell
$apimsubnet = New-AzVirtualNetworkSubnetConfig -Name "apim02" -AddressPrefix "10.0.1.0/24"
```

### <a name="step-3"></a>Steg 3

Skapa en Virtual Network med **namnet appgwvnet** i resursgruppen **apim-appGw-RG** för regionen USA, västra. Använd prefixet 10.0.0.0/16 med undernäten 10.0.0.0/24 och 10.0.1.0/24.

```powershell
$vnet = New-AzVirtualNetwork -Name "appgwvnet" -ResourceGroupName $resGroupName -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $appgatewaysubnet,$apimsubnet
```

### <a name="step-4"></a>Steg 4

Tilldela en undernätsvariabel för nästa steg

```powershell
$appgatewaysubnetdata = $vnet.Subnets[0]
$apimsubnetdata = $vnet.Subnets[1]
```

## <a name="create-an-api-management-service-inside-a-vnet-configured-in-internal-mode"></a>Skapa en API Management i ett VNET som konfigurerats i internt läge

I följande exempel visas hur du skapar en API Management i ett VNET som endast konfigurerats för intern åtkomst.

### <a name="step-1"></a>Steg 1

Skapa ett API Management Virtual Network objekt med hjälp av undernätsobjektet som $apimsubnetdata skapade ovan.

```powershell
$apimVirtualNetwork = New-AzApiManagementVirtualNetwork -SubnetResourceId $apimsubnetdata.Id
```

### <a name="step-2"></a>Steg 2

Skapa en API Management-tjänst i Virtual Network.

```powershell
$apimServiceName = "ContosoApi"       # API Management service instance name
$apimOrganization = "Contoso"         # organization name
$apimAdminEmail = "admin@contoso.com" # administrator's email address
$apimService = New-AzApiManagement -ResourceGroupName $resGroupName -Location $location -Name $apimServiceName -Organization $apimOrganization -AdminEmail $apimAdminEmail -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Developer"
```

När kommandot ovan lyckas refererar du till [den DNS-konfiguration som krävs för att få åtkomst till interna VNET API Management tjänsten för](api-management-using-with-internal-vnet.md#apim-dns-configuration) att komma åt den. Det här steget kan ta mer än en halvtimme.

## <a name="set-up-a-custom-domain-name-in-api-management"></a>Konfigurera ett anpassat domännamn i API Management

> [!IMPORTANT]
> Den [nya utvecklarportalen](api-management-howto-developer-portal.md) kräver också att du aktiverar anslutning API Management till hanteringsslutpunkten för API Management utöver stegen nedan.

### <a name="step-1"></a>Steg 1

Initiera följande variabler med information om certifikaten med privata nycklar för domänerna. I det här exemplet använder vi `api.contoso.net` och `portal.contoso.net` .  

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

Skapa och ange konfigurationsobjekt för värdnamn för proxyn och för portalen.  

```powershell
$proxyHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $gatewayHostname -HostnameType Proxy -PfxPath $gatewayCertPfxPath -PfxPassword $certPwd
$portalHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $portalHostname -HostnameType DeveloperPortal -PfxPath $portalCertPfxPath -PfxPassword $certPortalPwd

$apimService.ProxyCustomHostnameConfiguration = $proxyHostnameConfig
$apimService.PortalCustomHostnameConfiguration = $portalHostnameConfig
Set-AzApiManagement -InputObject $apimService
```

> [!NOTE]
> För att konfigurera den äldre utvecklarportalanslutningen måste du ersätta `-HostnameType DeveloperPortal` med `-HostnameType Portal` .

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Skapa en offentlig IP-adress för frontend-konfigurationen

Skapa en offentlig **IP-resurs publicIP01** i resursgruppen.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $resGroupName -name "publicIP01" -location $location -AllocationMethod Dynamic
```

En IP-adress tilldelas till programgatewayen när tjänsten startas.

## <a name="create-application-gateway-configuration"></a>Skapa konfiguration av programgateway

Alla konfigurationsobjekt måste konfigureras innan programgatewayen skapas. Följande steg skapar konfigurationsobjekten som behövs för en programgatewayresurs.

### <a name="step-1"></a>Steg 1

Skapa en IP-konfiguration för programgatewayen **med namnet gatewayIP01.** När Application Gateway startar hämtar den en IP-adress från det konfigurerade undernätet och dirigerar nätverkstrafik till IP-adresserna i backend-IP-poolen. Tänk på att varje instans använder en IP-adress.

```powershell
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "gatewayIP01" -Subnet $appgatewaysubnetdata
```

### <a name="step-2"></a>Steg 2

Konfigurera klientsidans IP-port för den offentliga IP-slutpunkten. Den här porten är den port som slutanvändarna ansluter till.

```powershell
$fp01 = New-AzApplicationGatewayFrontendPort -Name "port01"  -Port 443
```

### <a name="step-3"></a>Steg 3

Konfigurera klientdelens IP med den offentliga IP-slutpunkten.

```powershell
$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>Steg 4

Konfigurera certifikaten för Application Gateway, som används för att dekryptera och omkryptera trafiken som passerar.

```powershell
$cert = New-AzApplicationGatewaySslCertificate -Name "cert01" -CertificateFile $gatewayCertPfxPath -Password $certPwd
$certPortal = New-AzApplicationGatewaySslCertificate -Name "cert02" -CertificateFile $portalCertPfxPath -Password $certPortalPwd
```

### <a name="step-5"></a>Steg 5

Skapa HTTP-lyssnare för Application Gateway. Tilldela frontend-IP-konfiguration, port och TLS/SSL-certifikat till dem.

```powershell
$listener = New-AzApplicationGatewayHttpListener -Name "listener01" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $cert -HostName $gatewayHostname -RequireServerNameIndication true
$portalListener = New-AzApplicationGatewayHttpListener -Name "listener02" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $certPortal -HostName $portalHostname -RequireServerNameIndication true
```

### <a name="step-6"></a>Steg 6

Skapa anpassade avsökningar till `ContosoApi` API Management-tjänstproxydomänens slutpunkt. Sökvägen `/status-0123456789abcdef` är en standardslutpunkt för hälsotillstånd som finns på alla API Management tjänster. Ange `api.contoso.net` som ett anpassat avsökningsvärdnamn för att skydda det med TLS/SSL-certifikatet.

> [!NOTE]
> Värdnamnet är `contosoapi.azure-api.net` det standardvärdnamn för proxyn som konfigureras när en tjänst `contosoapi` med namnet skapas i offentliga Azure.
>

```powershell
$apimprobe = New-AzApplicationGatewayProbeConfig -Name "apimproxyprobe" -Protocol "Https" -HostName $gatewayHostname -Path "/status-0123456789abcdef" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
$apimPortalProbe = New-AzApplicationGatewayProbeConfig -Name "apimportalprobe" -Protocol "Https" -HostName $portalHostname -Path "/internal-status-0123456789abcdef" -Interval 60 -Timeout 300 -UnhealthyThreshold 8
```

### <a name="step-7"></a>Steg 7

Ladda upp certifikatet som ska användas på de TLS-aktiverade resurserna för serverpoolen. Det här är samma certifikat som du angav i steg 4 ovan.

```powershell
$authcert = New-AzApplicationGatewayAuthenticationCertificate -Name "whitelistcert1" -CertificateFile $gatewayCertCerPath
```

### <a name="step-8"></a>Steg 8

Konfigurera HTTP-inställningar för Application Gateway. Detta inkluderar att ange en tidsgräns för backend-begäran, var efter vilken de avbryts. Det här värdet skiljer sig från avsökningens time-out.

```powershell
$apimPoolSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimprobe -AuthenticationCertificates $authcert -RequestTimeout 180
$apimPoolPortalSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolPortalSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimPortalProbe -AuthenticationCertificates $authcert -RequestTimeout 180
```

### <a name="step-9"></a>Steg 9

Konfigurera en IP-adresspool på servernivå med **namnet apimbackend**  med den interna virtuella IP-adressen för den API Management som skapades ovan.

```powershell
$apimProxyBackendPool = New-AzApplicationGatewayBackendAddressPool -Name "apimbackend" -BackendIPAddresses $apimService.PrivateIPAddresses[0]
```

### <a name="step-10"></a>Steg 10

Skapa regler för Application Gateway ska använda grundläggande routning.

```powershell
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType Basic -HttpListener $listener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolSetting
$rule02 = New-AzApplicationGatewayRequestRoutingRule -Name "rule2" -RuleType Basic -HttpListener $portalListener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolPortalSetting
```

> [!TIP]
> Ändra -RuleType och routning för att begränsa åtkomsten till vissa sidor i utvecklarportalen.

### <a name="step-11"></a>Steg 11

Konfigurera antalet instanser och storleken för Application Gateway. I det här exemplet använder vi [WAF SKU](../web-application-firewall/ag/ag-overview.md) för ökad säkerhet för API Management resurs.

```powershell
$sku = New-AzApplicationGatewaySku -Name "WAF_Medium" -Tier "WAF" -Capacity 2
```

### <a name="step-12"></a>Steg 12

Konfigurera WAF till att vara i "skyddsläge".

```powershell
$config = New-AzApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>Skapa Application Gateway

Skapa en Application Gateway med alla konfigurationsobjekt från föregående steg.

```powershell
$appgwName = "apim-app-gw"
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $resGroupName -Location $location -BackendAddressPools $apimProxyBackendPool -BackendHttpSettingsCollection $apimPoolSetting, $apimPoolPortalSetting  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener, $portalListener -RequestRoutingRules $rule01, $rule02 -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert, $certPortal -AuthenticationCertificates $authcert -Probes $apimprobe, $apimPortalProbe
```

## <a name="cname-the-api-management-proxy-hostname-to-the-public-dns-name-of-the-application-gateway-resource"></a>CNAME-API Management proxyvärdnamn till det offentliga DNS-namnet för Application Gateway resursen

När du har skapat gatewayen, är nästa steg att konfigurera klientprogrammet för kommunikation. När du använder en offentlig IP Application Gateway du ett dynamiskt tilldelat DNS-namn, vilket kanske inte är lätt att använda.

Den Application Gateway DNS-namnet ska användas för att skapa en CNAME-post som pekar APIM-proxyvärdnamnet (t.ex. i exemplen ovan) till det `api.contoso.net` här DNS-namnet. Om du vill konfigurera CNAME-posten för IP-adress i serverdelen hämtar du informationen om Application Gateway och dess associerade IP/DNS-namn med hjälp av PublicIPAddress-elementet. Användning av A-poster rekommenderas inte eftersom VIP kan ändras vid omstart av gatewayen.

```powershell
Get-AzPublicIpAddress -ResourceGroupName $resGroupName -Name "publicIP01"
```

## <a name="summary"></a><a name="summary"></a> Sammanfattning
Azure API Management som konfigurerats i ett VNET tillhandahåller ett enda gatewaygränssnitt för alla konfigurerade API:er, oavsett om de finns lokalt eller i molnet. Integreringen Application Gateway med API Management ger flexibiliteten att selektivt aktivera vissa API:er så att de är tillgängliga på Internet, samt att tillhandahålla en Web Application Firewall som en frontend för din API Management-instans.

## <a name="next-steps"></a><a name="next-steps"></a> Nästa steg
* Läs mer om Azure Application Gateway
  * [Application Gateway översikt](../application-gateway/overview.md)
  * [Application Gateway Web Application Firewall](../web-application-firewall/ag/ag-overview.md)
  * [Application Gateway med sökvägsbaserad routning](../application-gateway/tutorial-url-route-powershell.md)
* Läs mer om API Management virtuella datorer
  * [Använda API Management endast tillgängligt i det virtuella nätverket](api-management-using-with-internal-vnet.md)
  * [Använda API Management i VNET](api-management-using-with-vnet.md)
