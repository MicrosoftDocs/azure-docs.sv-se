---
title: Konfigurera end-to-end-TLS med Azure Application Gateway
description: Den här artikeln beskriver hur du konfigurerar end-to-end-TLS med Azure Application Gateway med hjälp av PowerShell
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 06/09/2020
ms.author: victorh
ms.openlocfilehash: 47891dfa7fc0c9b30ccdbf2ed7710125eb36e4a3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397815"
---
# <a name="configure-end-to-end-tls-by-using-application-gateway-with-powershell"></a>Konfigurera SSL från slutpunkt till slutpunkt i Application Gateway via PowerShell

## <a name="overview"></a>Översikt

Azure Application Gateway stöder kryptering från slut punkt till slut punkt av trafik. Application Gateway avslutar TLS/SSL-anslutningen på Application Gateway. Gatewayen tillämpar sedan routningsregler i trafiken, krypterar om paketet och vidarebefordrar paketet till lämplig backend-server baserat på de routningsregler som definierats. Eventuella svar från webbservern genomgår samma process på väg tillbaka till användaren.

Application Gateway har stöd för att definiera anpassade TLS-alternativ. Den har också stöd för att inaktivera följande protokoll versioner: **tlsv 1.0**, **Tlsv 1.1** och **tlsv 1.2**, samt definiera vilka chiffersviter som ska användas och prioritetsordning. Mer information om konfigurerbara TLS-alternativ finns i [Översikt över TLS-principer](application-gateway-SSL-policy-overview.md).

> [!NOTE]
> SSL 2,0 och SSL 3,0 är inaktiverat som standard och kan inte aktive ras. De betraktas som oskyddade och kan inte användas med Application Gateway.

![scenariobild][scenario]

## <a name="scenario"></a>Scenario

I det här scenariot får du lära dig hur du skapar en Programgateway med hjälp av end-to-end-TLS med PowerShell.

Det här scenariot kommer att:

* Skapa en resurs grupp med namnet **appgw-RG**.
* Skapa ett virtuellt nätverk med namnet **appgwvnet** med ett adress utrymme på **10.0.0.0/16**.
* Skapa två undernät som kallas **appgwsubnet** och **appsubnet**.
* Skapa en Small Application Gateway som stöder slut punkt till slut punkt för TLS-kryptering som begränsar TLS-protokoll versioner och chiffersviter.

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Om du vill konfigurera en end-to-end-TLS med en Programgateway krävs ett certifikat för den gateway och de certifikat som krävs för backend-servrarna. Gateway-certifikatet används för att härleda en symmetrisk nyckel enligt specifikationen TLS-protokoll. Den symmetriska nyckeln används sedan för att kryptera och dekryptera trafiken som skickas till gatewayen. Gateway-certifikatet måste vara i PFX-format (personal information Exchange). Med det här fil formatet kan du exportera den privata nyckeln som krävs av programgatewayen för att utföra kryptering och dekryptering av trafik.

För att utföra TLS-kryptering från slut punkt till slut punkt måste Server delen uttryckligen tillåtas av programgatewayen. Överför Server dels certifikatets offentliga certifikat till programgatewayen. Genom att lägga till certifikatet ser du till att programgatewayen bara kommunicerar med kända backend-instanser. Detta skyddar all kommunikation från slut punkt till slut punkt.

Konfigurations processen beskrivs i följande avsnitt.

## <a name="create-the-resource-group"></a>Skapa resursgruppen

Det här avsnittet vägleder dig genom att skapa en resurs grupp som innehåller programgatewayen.

1. Logga in på ditt Azure-konto.

   ```powershell
   Connect-AzAccount
   ```

2. Välj den prenumeration som ska användas för det här scenariot.

   ```powershell
   Select-Azsubscription -SubscriptionName "<Subscription name>"
   ```

3. Skapa en resursgrupp. (Hoppa över det här steget om du använder en befintlig resursgrupp.)

   ```powershell
   New-AzResourceGroup -Name appgw-rg -Location "West US"
   ```

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Skapa ett virtuellt nätverk och ett undernät för programgatewayen

I följande exempel skapas ett virtuellt nätverk och två undernät. Ett undernät används för att hålla Application Gateway. Det andra under nätet används för Server delen som är värd för webb programmet.

1. Tilldela ett adress intervall för det undernät som ska användas för Application Gateway.

   ```powershell
   $gwSubnet = New-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24
   ```

   > [!NOTE]
   > Undernät som har kon figurer ATS för en Programgateway bör vara rätt storlek. En Application Gateway kan konfigureras för upp till 10 instanser. Varje instans tar en IP-adress från under nätet. För litet av ett undernät kan påverka skalning av en Programgateway negativt.
   >

2. Tilldela ett adress intervall som ska användas för backend-adresspoolen.

   ```powershell
   $nicSubnet = New-AzVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24
   ```

3. Skapa ett virtuellt nätverk med de undernät som definierats i föregående steg.

   ```powershell
   $vnet = New-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
   ```

4. Hämta de virtuella nätverks resurserna och under näts resurserna som ska användas i stegen som följer.

   ```powershell
   $vnet = Get-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
   $gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
   $nicSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet
   ```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Skapa en offentlig IP-adress för frontend-konfigurationen

Skapa en offentlig IP-resurs som ska användas för Application Gateway. Den här offentliga IP-adressen används i något av de steg som följer.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "West US" -AllocationMethod Dynamic
```

> [!IMPORTANT]
> Application Gateway stöder inte användning av en offentlig IP-adress som skapats med en definierad domän etikett. Endast en offentlig IP-adress med en dynamiskt skapad domän etikett stöds. Om du behöver ett eget DNS-namn för Application Gateway rekommenderar vi att du använder en CNAME-post som ett alias.

## <a name="create-an-application-gateway-configuration-object"></a>Skapa ett konfigurationsobjekt för programgatewayen

Alla konfigurations objekt anges innan du skapar programgatewayen. Följande steg skapar konfigurationsobjekten som behövs för en programgatewayresurs.

1. Skapa en IP-konfiguration för Application Gateway. Den här inställningen anger vilka av de undernät som Application Gateway använder. När Application Gateway startar hämtar den en IP-adress från det konfigurerade under nätet och dirigerar nätverks trafik till IP-adresserna i backend-IP-poolen. Tänk på att varje instans använder en IP-adress.

   ```powershell
   $gipconfig = New-AzApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet
   ```

2. Skapa en IP-konfiguration på klient sidan. Den här inställningen mappar en privat eller offentlig IP-adress till klient delen av programgatewayen. I följande steg associeras den offentliga IP-adressen i föregående steg med klient delens IP-konfiguration.

   ```powershell
   $fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip
   ```

3. Konfigurera backend-IP-adresspoolen med IP-adresserna för backend-webbservrarna. De här IP-adreserna är de IP-adresser som tar emot den nätverkstrafik som kommer från frontend-IP-slutpunkten. Ersätt IP-adresserna i exemplet med dina egna program-IP-slutpunkter.

   ```powershell
   $pool = New-AzApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3
   ```

   > [!NOTE]
   > Ett fullständigt kvalificerat domän namn (FQDN) är också ett giltigt värde som ska användas i stället för en IP-adress för backend-servrarna. Du aktiverar det genom att använda växeln **-BackendFqdns** . 

4. Konfigurera klient delens IP-port för den offentliga IP-slutpunkten. Den här porten är den port som slutanvändarna ansluter till.

   ```powershell
   $fp = New-AzApplicationGatewayFrontendPort -Name 'port01'  -Port 443
   ```

5. Konfigurera certifikatet för Application Gateway. Det här certifikatet används för att dekryptera och kryptera om trafiken på Application Gateway.

   ```powershell
   $passwd = ConvertTo-SecureString  <certificate file password> -AsPlainText -Force 
   $cert = New-AzApplicationGatewaySSLCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password $passwd 
   ```

   > [!NOTE]
   > Det här exemplet konfigurerar certifikatet som används för TLS-anslutningen. Certifikatet måste vara i PFX-format och lösen ordet måste vara mellan 4 och 12 tecken.

6. Skapa HTTP-lyssnaren för programgatewayen. Tilldela klient delens IP-konfiguration, port och TLS/SSL-certifikat som ska användas.

   ```powershell
   $listener = New-AzApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SSLCertificate $cert
   ```

7. Ladda upp certifikatet som ska användas på de TLS-aktiverade Server dels resurserna.

   > [!NOTE]
   > Standard avsökningen hämtar den offentliga nyckeln från *standard* TLS-bindningen på backend-IP-adressen och jämför det offentliga nyckel värde som det tar emot till det offentliga nyckel värde du anger här. 
   > 
   > Om du använder värdhuvuden och Servernamnindikator (SNI) på Server sidan kanske den hämtade offentliga nyckeln inte är den avsedda platsen som trafikflöden ska skickas till. Om du är osäker kan du besöka https://127.0.0.1/ backend-servrarna för att bekräfta vilket certifikat som används för TLS-standardbindningen.  Använd den offentliga nyckeln från denna begäran i det här avsnittet. Om du använder värd-och SNI på HTTPS-bindningar och du inte får något svar och certifikat från en manuell webb läsar förfrågan till https://127.0.0.1/ backend-servrarna måste du konfigurera en standard-TLS-bindning. Om du inte gör det går det inte att utföra avsökningar och Server delen är inte tillåten.
   
   Mer information om SNI i Application Gateway finns i [Översikt över TLS-terminering och slut punkt till slut punkt för TLS med Application Gateway](ssl-overview.md).

   ```powershell
   $authcert = New-AzApplicationGatewayAuthenticationCertificate -Name 'allowlistcert1' -CertificateFile C:\cert.cer
   ```

   > [!NOTE]
   > Certifikatet i föregående steg bör vara den offentliga nyckeln för det. PFX-certifikat som finns på Server delen. Exportera certifikatet (inte rot certifikatet) som är installerat på backend-servern i formatet för anspråk, bevis och orsaker (CER) och Använd det i det här steget. Det här steget gör det möjligt för Server delen med programgatewayen.

   Om du använder Application Gateway v2-SKU: n skapar du ett betrott rot certifikat i stället för ett autentiseringscertifikat. Mer information finns i [Översikt över end to end-TLS med Application Gateway](ssl-overview.md#end-to-end-tls-with-the-v2-sku):

   ```powershell
   $trustedRootCert01 = New-AzApplicationGatewayTrustedRootCertificate -Name "test1" -CertificateFile  <path to root cert file>
   ```

8. Konfigurera HTTP-inställningarna för Programgateway Server del. Tilldela det certifikat som överfördes i föregående steg till HTTP-inställningarna.

   ```powershell
   $poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert
   ```

   För Application Gateway v2-SKU: n använder du följande kommando:

   ```powershell
   $poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name “setting01” -Port 443 -Protocol Https -CookieBasedAffinity Disabled -TrustedRootCertificate $trustedRootCert01 -HostName "test1"
   ```

9. Skapa en routningsregler för belastnings utjämning som konfigurerar belastnings Utjämnings beteendet. I det här exemplet skapas en grundläggande regel för resursallokering (Round-Robin).

   ```powershell
   $rule = New-AzApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
   ```

10. Konfigurera programgatewayens instansstorlek. De tillgängliga storlekarna är **standard \_ små**, **standard \_ medium** och **standard \_ stora**.  För kapacitet är de tillgängliga värdena **1** till och med **10**.

    ```powershell
    $sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
    ```

    > [!NOTE]
    > Du kan välja ett instans antal 1 i test syfte. Det är viktigt att veta att antalet instanser under två instanser inte omfattas av SLA och därför inte rekommenderas. Små gatewayer ska användas för dev-test och inte för produktions syfte.

11. Konfigurera den TLS-princip som ska användas på Application Gateway. Application Gateway stöder möjligheten att ange en lägsta version för TLS-protokoll.

    Följande värden är en lista över protokoll versioner som kan definieras:

    - **TLSV1_0**
    - **TLSV1_1**
    - **TLSV1_2**
    
    I följande exempel anges den lägsta protokoll versionen till **TLSv1_2** och aktiverar **TLS- \_ ECDHE \_ ECDSA \_ med \_ AES \_ 128 \_ GCM \_ SHA256**, **TLS \_ ECDHE \_ ECDSA \_ med \_ AES \_ 256 \_ GCM \_ SHA384** och **TLS \_ RSA \_ med \_ AES \_ 128 \_ GCM \_ SHA256** .

    ```powershell
    $SSLPolicy = New-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -PolicyType Custom
    ```

## <a name="create-the-application-gateway"></a>Skapa programgatewayen

Skapa programgatewayen med hjälp av alla föregående steg. Att skapa gatewayen är en process som tar lång tid att köra.

För v1 SKU använder du kommandot nedan
```powershell
$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -AuthenticationCertificates $authcert -Verbose
```

För v2-SKU: n använder du kommandot nedan
```powershell
$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -TrustedRootCertificate $trustedRootCert01 -Verbose
```

## <a name="apply-a-new-certificate-if-the-back-end-certificate-is-expired"></a>Använd ett nytt certifikat om Server dels certifikatet har upphört att gälla

Använd den här proceduren om du vill använda ett nytt certifikat om Server dels certifikatet har upphört att gälla.

1. Hämta programgatewayen som ska uppdateras.

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```
   
2. Lägg till den nya certifikat resursen från CER-filen som innehåller certifikatets offentliga nyckel och kan också vara samma certifikat som har lagts till i lyssnaren för TLS-avslutning på Application Gateway.

   ```powershell
   Add-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name 'NewCert' -CertificateFile "appgw_NewCert.cer" 
   ```
    
3. Hämta det nya objektet för autentisering av certifikat till en variabel (TypeName: Microsoft. Azure. commands. Network. Models. PSApplicationGatewayAuthenticationCertificate).

   ```powershell
   $AuthCert = Get-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name NewCert
   ```
 
 4. Tilldela det nya certifikatet till **BackendHttp** -inställningen och referera det med variabeln $AuthCert. (Ange det HTTP-inställnings namn som du vill ändra.)
 
   ```powershell
   $out= Set-AzApplicationGatewayBackendHttpSetting -ApplicationGateway $gw -Name "HTTP1" -Port 443 -Protocol "Https" -CookieBasedAffinity Disabled -AuthenticationCertificates $Authcert
   ```
    
 5. Genomför ändringen i programgatewayen och överför den nya konfigurationen som finns i $out variabeln.
 
   ```powershell
   Set-AzApplicationGateway -ApplicationGateway $gw  
   ```

## <a name="remove-an-unused-expired-certificate-from-http-settings"></a>Ta bort ett oanvänt utgånget certifikat från HTTP-inställningarna

Använd den här proceduren för att ta bort ett oanvänt utgånget certifikat från HTTP-inställningarna.

1. Hämta programgatewayen som ska uppdateras.

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```
   
2. Ange namnet på det autentiseringscertifikat som du vill ta bort.

   ```powershell
   Get-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw | select name
   ```
    
3. Ta bort Autentiseringscertifikatet från en Programgateway.

   ```powershell
   $gw=Remove-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name ExpiredCert
   ```
 
 4. Genomför ändringen.
 
   ```powershell
   Set-AzApplicationGateway -ApplicationGateway $gw
   ```

   
## <a name="limit-tls-protocol-versions-on-an-existing-application-gateway"></a>Begränsa TLS-protokollets versioner på en befintlig Application Gateway

Föregående steg tog dig genom att skapa ett program med end-to-end-TLS och inaktivera vissa TLS-protokoll versioner. I följande exempel inaktive ras vissa TLS-principer på en befintlig Application Gateway.

1. Hämta programgatewayen som ska uppdateras.

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```

2. Definiera en TLS-princip. I följande exempel är **tlsv 1.0** och **tlsv 1.1** inaktiverade och cipher-paketen **TLS \_ ECDHE \_ ECDSA \_ med \_ AES \_ 128 \_ GCM \_ SHA256**, **TLS \_ ECDHE \_ ECDSA \_ med \_ AES \_ 256 \_ GCM \_** SHA384 och **TLS \_ RSA \_ med \_ AES \_ 128 \_ GCM \_ SHA256** är de enda tillåtna.

   ```powershell
   Set-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -PolicyType Custom -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -ApplicationGateway $gw

   ```

3. Uppdatera därefter gatewayen. Det sista steget är en tids krävande uppgift. När den är klar konfigureras end-to-end-TLS i Application Gateway.

   ```powershell
   $gw | Set-AzApplicationGateway
   ```

## <a name="get-an-application-gateway-dns-name"></a>Hämta ett DNS-namn för Application Gateway

När gatewayen har skapats är nästa steg att konfigurera klient delen för kommunikation. Application Gateway kräver ett dynamiskt tilldelat DNS-namn när du använder en offentlig IP-adress, vilket inte är vänligt. För att garantera att slutanvändare kan nå programgatewayen kan du använda en CNAME-post för att peka på den offentliga slut punkten för programgatewayen. Mer information finns i [Konfigurera ett anpassat domän namn för i Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). 

Om du vill konfigurera ett alias hämtar du information om programgatewayen och dess associerade IP/DNS-namn med hjälp av **PublicIPAddress** -elementet som är kopplat till Application Gateway. Använd programgatewayens DNS-namn för att skapa en CNAME-post som pekar på de två webb programmen till det här DNS-namnet. Vi rekommenderar inte att du använder A-poster eftersom VIP kan ändras vid omstart av programgatewayen.

```powershell
Get-AzPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
```

```
Name                     : publicIP01
ResourceGroupName        : appgw-RG
Location                 : westus
Id                       : /subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/publicIPAddresses/publicIP01
Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
ResourceGuid             : 00000000-0000-0000-0000-000000000000
ProvisioningState        : Succeeded
Tags                     : 
PublicIpAllocationMethod : Dynamic
IpAddress                : xx.xx.xxx.xx
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                                "Id": "/subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIP
                            Configurations/frontend1"
                            }
DnsSettings              : {
                                "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
                            }
```

## <a name="next-steps"></a>Nästa steg

Mer information om hur du härdningr säkerheten för dina webb program med brand vägg för webbaserade program via Application Gateway finns i [Översikt över brand väggen för webb program](../web-application-firewall/ag/ag-overview.md).

[scenario]: ./media/application-gateway-end-to-end-SSL-powershell/scenario.png