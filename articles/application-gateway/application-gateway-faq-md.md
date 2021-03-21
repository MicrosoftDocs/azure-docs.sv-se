---
title: Vanliga frågor om Azure Application Gateway
description: Få svar på vanliga frågor om Azure Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 05/26/2020
ms.author: victorh
ms.custom: references_regions
ms.openlocfilehash: 83779dcd319614ae15de6b7e3e4e3abfd9599089
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102619162"
---
# <a name="frequently-asked-questions-about-application-gateway"></a>Vanliga frågor och svar om Application Gateway

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Följande är vanliga frågor som ställs om Azure Application Gateway.

## <a name="general"></a>Allmänt

### <a name="what-is-application-gateway"></a>Vad är Application Gateway?

Azure Application Gateway tillhandahåller en program leverans kontroll (ADC) som en tjänst. Det erbjuder olika belastnings Utjämnings funktioner i lager 7 för dina program. Den här tjänsten är hög tillgänglig, skalbar och helt hanterad av Azure.

### <a name="what-features-does-application-gateway-support"></a>Vilka funktioner stöder Application Gateway?

Application Gateway stöder automatisk skalning, TLS-avlastning och autentisering från slut punkt till slut punkt, en brand vägg för webbaserade program (WAF), cookie-baserad session tillhörighet, URL-sökväg baserad routning, multisite-värd och andra funktioner. En fullständig lista över funktioner som stöds finns i [Introduktion till Application Gateway](./overview.md).

### <a name="how-do-application-gateway-and-azure-load-balancer-differ"></a>Hur skiljer sig Application Gateway och Azure Load Balancer?

Application Gateway är en Layer 7-belastningsutjämnare, vilket innebär att den endast fungerar med webb trafik (HTTP, HTTPS, WebSocket och HTTP/2). Den har stöd för funktioner som TLS-avslutning, cookie-baserad sessionsgräns och resursallokering för belastnings Utjämnings trafik. Load Balancer belastnings Utjämnings trafik på Layer 4 (TCP eller UDP).

### <a name="what-protocols-does-application-gateway-support"></a>Vilka protokoll stöder Application Gateway?

Application Gateway stöder HTTP, HTTPS, HTTP/2 och WebSocket.

### <a name="how-does-application-gateway-support-http2"></a>Hur stöder Application Gateway HTTP/2?

Se [stöd för http/2](./configuration-listeners.md#http2-support).

### <a name="what-resources-are-supported-as-part-of-a-backend-pool"></a>Vilka resurser stöds som en del av en backend-pool?

Se [stödda Server dels resurser](./application-gateway-components.md#backend-pools).

### <a name="in-what-regions-is-application-gateway-available"></a>I vilka regioner är Application Gateway tillgängliga?

Application Gateway v1 (standard och WAF) är tillgängligt i alla regioner i Global Azure. Det är också tillgängligt i [Azure Kina 21Vianet](https://www.azure.cn/) och [Azure Government](https://azure.microsoft.com/overview/clouds/government/).

Application Gateway v2 (Standard_v2 och WAF_v2) tillgänglighet finns i [regioner som stöds för Application Gateway v2](./application-gateway-autoscaling-zone-redundant.md#supported-regions)

### <a name="is-this-deployment-dedicated-for-my-subscription-or-is-it-shared-across-customers"></a>Är den här distributionen dedikerad för min prenumeration eller delas den mellan kunder?

Application Gateway är en dedikerad distribution i det virtuella nätverket.

### <a name="does-application-gateway-support-http-to-https-redirection"></a>Stöder Application Gateway HTTP-till-HTTPS-omdirigering?

Omdirigering stöds. Se [Application Gateway omdirigerings översikt](./redirect-overview.md).

### <a name="in-what-order-are-listeners-processed"></a>I vilken ordning bearbetas lyssnare?

Se [ordningen för avlyssnings bearbetning](./configuration-listeners.md#order-of-processing-listeners).

### <a name="where-do-i-find-the-application-gateway-ip-and-dns"></a>Var hittar jag Application Gateway IP och DNS?

Om du använder en offentlig IP-adress som en slut punkt hittar du IP-och DNS-information om den offentliga IP-adressresursen. Eller hitta den i portalen på sidan Översikt för Application Gateway. Om du använder interna IP-adresser hittar du informationen på sidan Översikt.

För v2-SKU: n öppnar du den offentliga IP-resursen och väljer **konfiguration**. Fältet **DNS-namn (valfritt)** är tillgängligt för att konfigurera DNS-namnet.

### <a name="what-are-the-settings-for-keep-alive-timeout-and-tcp-idle-timeout"></a>Vad är inställningarna för Keep-Alive timeout och timeout för TCP-inaktivitet?

*Keep-Alive-timeout* styr hur länge Application Gateway väntar på att en klient ska skicka en annan http-begäran på en permanent anslutning innan den återanvänds eller stängas. *Timeout för TCP-inaktivitet* styr hur länge en TCP-anslutning hålls öppen om ingen aktivitet är öppen. 

*Timeoutvärdet för Keep-Alive* i Application Gateway v1 sku är 120 sekunder och i v2-SKU: n är 75 sekunder. *Timeout för TCP-inaktivitet* är en fyra minuters standard på klient delens virtuella IP (VIP) för både v1-och v2-SKU: er för Application Gateway. Du kan konfigurera timeout-värdet för TCP-inaktivitet på v1-och v2-programgatewayer var som helst mellan 4 minuter och 30 minuter. För både v1-och v2-programgatewayer måste du gå till den offentliga IP-adressen för Application Gateway och ändra timeout-värdet för TCP-inaktivitet under bladet "konfiguration" i den offentliga IP-adressen på portalen. Du kan ange timeout-värdet för TCP-inaktivitet för den offentliga IP-adressen via PowerShell genom att köra följande kommandon: 

```azurepowershell-interactive
$publicIP = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyResourceGroup
$publicIP.IdleTimeoutInMinutes = "15"
Set-AzPublicIpAddress -PublicIpAddress $publicIP
```

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>Ändras IP-eller DNS-namn under programgatewayens livs längd?

I Application Gateway v1 SKU: n kan du ändra VIP om du stoppar och startar programgatewayen. Men det DNS-namn som är associerat med programgatewayen ändras inte över gatewayens livstid. Eftersom DNS-namnet inte ändras bör du använda ett CNAME-alias och peka det mot DNS-adressen för Application Gateway. I Application Gateway v2 SKU: n kan du ange IP-adressen som statisk, så IP-och DNS-namn kommer inte att ändras under programmets livs längd. 

### <a name="does-application-gateway-support-static-ip"></a>Stöder Application Gateway statisk IP?

Ja, Application Gateway v2-SKU: n stöder statiska offentliga IP-adresser. V1 SKU stöder statiska interna IP-adresser.

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>Stöder Application Gateway flera offentliga IP-adresser på gatewayen?

En Application Gateway stöder bara en offentlig IP-adress.

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>Hur stor ska jag göra mitt undernät för Application Gateway?

Se [Application Gateway-undernätets storleks överväganden](./configuration-infrastructure.md#size-of-the-subnet).

### <a name="can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>Kan jag distribuera fler än en Application Gateway resurs till ett enda undernät?

Ja. Förutom flera instanser av en specifik Application Gateway distribution kan du etablera en annan unik Application Gateway resurs till ett befintligt undernät som innehåller en annan Application Gateway resurs.

Ett enda undernät har inte stöd för både v2-och v1-Application Gateway SKU: er.

### <a name="does-application-gateway-v2-support-user-defined-routes-udr"></a>Stöder Application Gateway v2 användardefinierade vägar (UDR)?

Ja, men endast vissa scenarier. Mer information finns i [Application Gateway infrastruktur konfiguration](configuration-infrastructure.md#supported-user-defined-routes).

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>Stöder Application Gateway x-vidarebefordrade-för rubriker?

Ja. Se [ändringar av en begäran](./how-application-gateway-works.md#modifications-to-the-request).

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-will-my-application-gateway-work-while-its-being-updated"></a>Hur lång tid tar det att distribuera en Application Gateway? Fungerar min Application Gateway när den uppdateras?

Det kan ta upp till 20 minuter att etablera nya Application Gateway v1 SKU-distributioner. Ändringar av instans storleken eller antalet är inte störande och gatewayen förblir aktiv under den här tiden.

De flesta distributioner som använder v2-SKU: n tar cirka 6 minuter att etablera. Det kan dock ta längre tid beroende på typen av distribution. Till exempel kan distributioner över flera Tillgänglighetszoner med många instanser ta mer än 6 minuter. 

### <a name="can-i-use-exchange-server-as-a-backend-with-application-gateway"></a>Kan jag använda Exchange Server som Server del med Application Gateway?

Nej. Application Gateway stöder inte e-postprotokoll som SMTP, IMAP och POP3.

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>Finns det några rikt linjer som du kan använda för att migrera från v1 SKU till v2-SKU: n?

Ja. Mer information finns i [migrera Azure Application Gateway och brand vägg för webbaserade program från v1 till v2](migrate-v1-v2.md).

### <a name="will-the-application-gateway-v1-sku-continue-to-be-supported"></a>Kommer Application Gateway v1 SKU fortsätta att stödjas?

Ja. Application Gateway v1 SKU kommer att fortsätta att stödjas. Vi rekommenderar dock starkt att du flyttar till v2 för att dra nytta av funktions uppdateringarna i SKU: n. Mer information finns i [autoskalning och Zone-redundant Application Gateway v2](application-gateway-autoscaling-zone-redundant.md).

### <a name="does-application-gateway-v2-support-proxying-requests-with-ntlm-authentication"></a>Stöder Application Gateway v2 proxy-begäranden med NTLM-autentisering?

Nej. Application Gateway v2 har inte stöd för proxy-begäranden med NTLM-autentisering.

### <a name="does-application-gateway-affinity-cookie-support-samesite-attribute"></a>Stöder Application Gateway SameSite för tillhörighets-cookie?
Ja, Chrome- [webbläsarens](https://www.chromium.org/Home) [V80-uppdatering](https://chromiumdash.appspot.com/schedule) introducerade ett mandat på HTTP cookies utan SameSite-attribut som ska behandlas som SameSite = lax. Det innebär att den Application Gateway tillhörighets-cookien inte skickas av webbläsaren i en tredjeparts kontext. 

För att stödja det här scenariot infogar Application Gateway en annan cookie med namnet *ApplicationGatewayAffinityCORS* förutom den befintliga *ApplicationGatewayAffinity* -cookien.  Dessa cookies liknar varandra, men cookien *ApplicationGatewayAffinityCORS* har två fler attribut som läggs till: *SameSite = none; Säker*. De här attributen upprätthåller tröga sessioner även för frågor mellan ursprung. Mer information finns i [avsnittet cookie-baserad tillhörighet](configuration-http-settings.md#cookie-based-affinity) .

## <a name="performance"></a>Prestanda

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>Hur stöder Application Gateway hög tillgänglighet och skalbarhet?

Application Gateway v1 SKU stöder scenarier med hög tillgänglighet när du har distribuerat två eller fler instanser. Azure distribuerar dessa instanser över uppdaterings-och fel domäner för att säkerställa att instanserna inte fungerar samtidigt. V1 SKU stöder skalbarhet genom att lägga till flera instanser av samma Gateway för att dela belastningen.

V2-SKU: n ser automatiskt till att nya instanser sprids mellan fel domäner och uppdaterings domäner. Om du väljer zon redundans sprids även de nyaste instanserna över tillgänglighets zoner för att erbjuda zonindelade-felåterhämtning.

### <a name="how-do-i-achieve-a-dr-scenario-across-datacenters-by-using-application-gateway"></a>Hur gör jag för att uppnå ett DR-scenario över data Center med Application Gateway?

Använd Traffic Manager för att distribuera trafik över flera programgatewayer i olika data Center.

### <a name="does-application-gateway-support-autoscaling"></a>Stöder Application Gateway automatisk skalning?

Ja, Application Gateway v2-SKU: n stöder automatisk skalning. Mer information finns i [autoskalning och zon-redundant Application Gateway](application-gateway-autoscaling-zone-redundant.md).

### <a name="does-manual-or-automatic-scale-up-or-scale-down-cause-downtime"></a>Är manuell eller automatisk skalning eller manuell skalning att göra drift stopp?

Nej. Instanser distribueras mellan uppgraderings domäner och fel domäner.

### <a name="does-application-gateway-support-connection-draining"></a>Har Application Gateway stöd för anslutning som töms?

Ja. Du kan konfigurera anslutnings tömning för att ändra medlemmar i en backend-pool utan avbrott. Mer information finns i [avsnittet om tömning av anslutningar i Application Gateway](features.md#connection-draining).

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>Kan jag ändra instans storleken från medel till stor utan avbrott?

Ja.

## <a name="configuration"></a>Konfiguration

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>Är Application Gateway alltid distribuerat i ett virtuellt nätverk?

Ja. Application Gateway distribueras alltid i ett undernät för virtuellt nätverk. Det här under nätet får bara innehålla programgatewayer. Mer information finns i [krav för virtuellt nätverk och undernät](./configuration-infrastructure.md#virtual-network-and-dedicated-subnet).

### <a name="can-application-gateway-communicate-with-instances-outside-of-its-virtual-network-or-outside-of-its-subscription"></a>Kan Application Gateway kommunicera med instanser utanför det virtuella nätverket eller utanför prenumerationen?

Så länge som du har IP-anslutning kan Application Gateway kommunicera med instanser utanför det virtuella nätverk där det finns. Application Gateway kan också kommunicera med instanser utanför prenumerationen som det finns i. Om du planerar att använda interna IP-adresser som medlemmar i backend-poolen använder du [peering för virtuella nätverk](../virtual-network/virtual-network-peering-overview.md) eller [Azure VPN gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>Kan jag distribuera något annat i Application Gateway-undernätet?

Nej. Men du kan distribuera andra programgatewayer i under nätet.

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>Stöds nätverks säkerhets grupper i undernät för Application Gateway?

Se [nätverks säkerhets grupper i Application Gateway under nätet](./configuration-infrastructure.md#network-security-groups).

### <a name="does-the-application-gateway-subnet-support-user-defined-routes"></a>Har Application Gateway-undernätet stöd för användardefinierade vägar?

Se [användardefinierade vägar som stöds i Application Gateway under nätet](./configuration-infrastructure.md#supported-user-defined-routes).

### <a name="are-service-endpoint-policies-supported-in-the-application-gateway-subnet"></a>Stöds service slut punkts principer i Application Gateway-undernätet?

Nej. [Tjänst slut punkts principer](../virtual-network/virtual-network-service-endpoint-policies-overview.md) för lagrings konton stöds inte i Application Gateway under nät och konfigureras blockerar Azures infrastruktur trafik.

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>Vilka är gränserna för Application Gateway? Kan jag öka de här gränserna?

Se [Application Gateway gränser](../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits).

### <a name="can-i-simultaneously-use-application-gateway-for-both-external-and-internal-traffic"></a>Kan jag använda Application Gateway samtidigt för både extern och intern trafik?

Ja. Application Gateway stöder en intern IP-adress och en extern IP per Application Gateway.

### <a name="does-application-gateway-support-virtual-network-peering"></a>Stöder Application Gateway virtuell nätverks-peering?

Ja. Det virtuella nätverkets peering hjälper belastnings Utjämnings trafik i andra virtuella nätverk.

### <a name="can-i-talk-to-on-premises-servers-when-theyre-connected-by-expressroute-or-vpn-tunnels"></a>Kan jag prata med lokala servrar när de är anslutna via ExpressRoute eller VPN-tunnlar?

Ja, så länge trafik tillåts.

### <a name="can-one-backend-pool-serve-many-applications-on-different-ports"></a>Kan en backend-pool betjäna många program på olika portar?

Mikrotjänstens arkitektur stöds. Om du vill avsöka på olika portar måste du konfigurera flera HTTP-inställningar.

### <a name="do-custom-probes-support-wildcards-or-regex-on-response-data"></a>Stöder anpassade avsökningar jokertecken eller regex för svars data?

Nej. 

### <a name="how-are-routing-rules-processed-in-application-gateway"></a>Hur bearbetas routningsregler i Application Gateway?

Se [ordning för bearbetnings regler](./configuration-request-routing-rules.md#order-of-processing-rules).

### <a name="for-custom-probes-what-does-the-host-field-signify"></a>Vad indikerar värd fältet för anpassade avsökningar?

Fältet värd anger namnet som avsökningen ska skickas till när du har konfigurerat multisite på Application Gateway. Använd annars 127.0.0.1. Det här värdet skiljer sig från värd namnet för den virtuella datorn. Formatet är \<protocol\> :// \<host\> : \<port\> \<path\> .

### <a name="can-i-allow-application-gateway-access-to-only-a-few-source-ip-addresses"></a>Kan jag bara tillåta Application Gateway åtkomst till några få käll-IP-adresser?

Ja. Se [begränsa åtkomsten till vissa käll-IP-adresser](./configuration-infrastructure.md#allow-access-to-a-few-source-ips).

### <a name="can-i-use-the-same-port-for-both-public-facing-and-private-facing-listeners"></a>Kan jag använda samma port för både offentlig och privat lyssnare?

Nej.

### <a name="does-application-gateway-support-ipv6"></a>Stöder Application Gateway IPv6?

Application Gateway v2 stöder för närvarande inte IPv6. Den kan köras i ett VNet med dubbla stackar som bara använder IPv4, men Gateway-undernätet måste vara endast IPv4. Application Gateway v1 stöder inte dubbla stack-virtuella nätverk. 

### <a name="how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address"></a>Hur gör jag för att använda Application Gateway v2 med endast en privat klient dels-IP-adress?

Application Gateway v2 stöder för närvarande inte enbart privat IP-läge. Det stöder följande kombinationer
* Privat IP och offentlig IP
* Endast offentlig IP

Men om du vill använda Application Gateway v2 med endast privat IP kan du följa stegen nedan:
1. Skapa en Application Gateway med både offentlig och privat klient dels-IP-adress
2. Skapa inga lyssnare för den offentliga IP-adressen för klient delen. Application Gateway lyssnar inte på någon trafik på den offentliga IP-adressen om inga lyssnare har skapats för den.
3. Skapa och koppla en [nätverks säkerhets grupp](../virtual-network/network-security-groups-overview.md) för Application Gateway under nätet med följande konfiguration i prioritetsordning:
    
    a. Tillåt trafik från källa som **GatewayManager** service tag och destination som **valfri** port och målport som **65200-65535**. Det här port intervallet krävs för kommunikation mellan Azure-infrastrukturen. Dessa portar är skyddade (låsta) av certifikatautentisering. Externa entiteter, inklusive Gateway-användarens administratörer, kan inte påbörja ändringar av dessa slut punkter utan lämpliga certifikat på plats
    
    b. Tillåt trafik från källa som **AzureLoadBalancer** service tag och mål-och mål port som **alla**
    
    c. Neka all inkommande trafik från källan som **Internet** service tag och mål-och mål port som **en**. Ge den här regeln *lägst prioritet* i reglerna för inkommande trafik
    
    d. Behåll standard reglerna som att tillåta VirtualNetwork inkommande så att åtkomsten till den privata IP-adressen inte är blockerad
    
    e. Det går inte att blockera utgående Internet anslutning. Annars är det problem med loggning, mått osv.

Exempel på NSG-konfiguration för privat IP-åtkomst: ![ Application Gateway v2 NSG-konfiguration endast för privat IP-åtkomst](./media/application-gateway-faq/appgw-privip-nsg.png)

## <a name="configuration---tls"></a>Konfiguration – TLS

### <a name="what-certificates-does-application-gateway-support"></a>Vilka certifikat stöder Application Gateway?

Application Gateway stöder självsignerade certifikat, certifikat utfärdare (CA), certifikat för utökad verifiering (EV), certifikat för flera domäner (SAN) och certifikat med jokertecken.

### <a name="what-cipher-suites-does-application-gateway-support"></a>Vilka chiffersviter stöder Application Gateway?

Application Gateway stöder följande chiffersviter. 

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

Information om hur du anpassar TLS-alternativ finns i [Konfigurera versioner av TLS-principer och cipher-paket på Application Gateway](application-gateway-configure-ssl-policy-powershell.md).

### <a name="does-application-gateway-support-reencryption-of-traffic-to-the-backend"></a>Stöder Application Gateway kryptering av trafik till Server delen?

Ja. Application Gateway stöder TLS-avläsning och autentisering från slut punkt till slut punkt, som krypterar trafik till Server delen.

### <a name="can-i-configure-tls-policy-to-control-tls-protocol-versions"></a>Kan jag konfigurera TLS-principen för att kontrol lera TLS-protokoll versioner?

Ja. Du kan konfigurera Application Gateway att neka TLS 1.0, TLS 1.1 och TLS 1.2. Som standard är SSL 2,0 och 3,0 redan inaktiverat och kan inte konfigureras.

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>Kan jag konfigurera cipher-paket och princip ordning?

Ja. I Application Gateway kan du [Konfigurera chiffersviter](application-gateway-ssl-policy-overview.md). Aktivera minst en av följande chiffersviter om du vill definiera en anpassad princip. 

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

Application Gateway använder SHA256 för Server dels hantering.

### <a name="how-many-tlsssl-certificates-does-application-gateway-support"></a>Hur många TLS/SSL-certifikat stöder Application Gateway?

Application Gateway stöder upp till 100 TLS/SSL-certifikat.

### <a name="how-many-authentication-certificates-for-backend-reencryption-does-application-gateway-support"></a>Hur många autentiserings certifikat för Omkryptering av backend-Application Gateway stöd för?

Application Gateway stöder upp till 100 autentiserings-certifikat.

### <a name="does-application-gateway-natively-integrate-with-azure-key-vault"></a>Integrerar Application Gateway internt med Azure Key Vault?

Ja, Application Gateway v2-SKU: n stöder Key Vault. Läs mer i [TLS-avslutning med Key Vault-certifikat](key-vault-certs.md).

### <a name="how-do-i-configure-https-listeners-for-com-and-net-sites"></a>Hur gör jag för att konfigurera HTTPS-lyssnare för. com-och .net-platser? 

För flera domänbaserade (värdbaserade) routningar kan du skapa multisite-lyssnare, konfigurera lyssnare som använder HTTPS som protokoll och associera lyssnarna med routningsregler. Mer information finns i vara [värd för flera platser med hjälp av Application Gateway](./multiple-site-overview.md).

### <a name="can-i-use-special-characters-in-my-pfx-file-password"></a>Kan jag använda specialtecken i lösen ordet för PFX-filen?

Nej, Använd endast alfanumeriska tecken i PFX-filens lösen ord.

### <a name="my-ev-certificate-is-issued-by-digicert-and-my-intermediate-certificate-has-been-revoked-how-do-i-renew-my-certificate-on-application-gateway"></a>Mitt EV-certifikat utfärdas av DigiCert och mitt mellanliggande certifikat har återkallats. Hur gör jag för att förnya mitt certifikat på Application Gateway?

Webb läsar medlemmar för certifikat utfärdare (CA) har nyligen publicerat rapporter med information om flera certifikat som utfärdats av leverantörer av certifikat utfärdare som används av våra kunder, Microsoft och den större teknik communityn som inte var kompatibla med bransch standarder för offentligt betrodda certifikat utfärdare.Rapporterna om icke-kompatibla ca: er kan hittas här:  

* [Bugg 1649951](https://bugzilla.mozilla.org/show_bug.cgi?id=1649951)
* [Bugg 1650910](https://bugzilla.mozilla.org/show_bug.cgi?id=1650910)

Som enligt branschens krav på efterlevnad började CA-leverantörer återkallade icke-kompatibla certifikat utfärdare och utfärdande av kompatibla ca: er som kräver att kunderna har återupprättat sina certifikat.Microsoft samarbetar nära dessa leverantörer för att minimera den potentiella påverkan till Azure-tjänster, **men dina självutfärdade certifikat eller certifikat som används i "ta dina egna certifikat" (BYOC) är fortfarande utsatta för att oväntade återkallas**.

För att kontrol lera om certifikat som används av ditt program har återkallats referens [DigiCert meddelande](https://knowledge.digicert.com/alerts/DigiCert-ICA-Replacement) och [Spårare för certifikat återkallning](https://misissued.com/#revoked). Om dina certifikat har återkallats eller kommer att återkallas måste du begära nya certifikat från den CA-leverantör som används i dina program. För att undvika att programmets tillgänglighet avbryts på grund av att certifikaten har återkallats, eller om du vill uppdatera ett certifikat som har återkallats, kan du gå till våra Azure updates-uppdateringar för reparations Länkar för olika Azure-tjänster som stöder BYOC: https://azure.microsoft.com/updates/certificateauthorityrevocation/

Application Gateway detaljerad information finns under

Om du använder ett certifikat som utfärdats av en av de återkallade ICAs kan programmets tillgänglighet avbrytas och beroende på ditt program kan du få en rad fel meddelanden, inklusive men inte begränsat till: 

1.  Ogiltigt certifikat/återkallat certifikat
2.  Anslutningens tidsgräns uppnåddes
3.  HTTP 502

För att undvika avbrott i ditt program på grund av det här problemet, eller för att utfärda en certifikat utfärdare som har återkallats, måste du vidta följande åtgärder: 

1.  Kontakta din certifikat leverantör om hur du utfärdar certifikat på nytt
2.  Uppdatera dina certifikat på Azure Application Gateway-WAF med en fullständig [förtroende kedja](/windows/win32/seccrypto/certificate-chains) (löv, mellanliggande rot certifikat) när du har utfärdat det. Baserat på var du använder certifikatet, antingen på lyssnaren eller HTTP-inställningarna för Application Gateway, följer du stegen nedan för att uppdatera certifikaten och kontrol lera dokumentations länkarna som nämns för mer information.
3.  Uppdatera Server dels applikations servrarna så att de använder det återutfärdade certifikatet. Beroende på vilken backend-server du använder kan ditt certifikat uppdaterings steg variera. Mer information finns i dokumentationen från leverantören.

Så här uppdaterar du certifikatet i din lyssnare:

1.  Öppna Application Gateway-resursen i [Azure Portal](https://portal.azure.com/)
2.  Öppna inställningarna för lyssnare som är associerade med ditt certifikat
3.  Klicka på "förnya eller redigera det valda certifikatet"
4.  Överför ditt nya PFX-certifikat med lösen ordet och klicka på Spara
5.  Gå till webbplatsen och kontrol lera att platsen fungerar som förväntat. mer information finns i dokumentationen [här](./renew-certificates.md).

Om du refererar till certifikat från Azure-valv i din Application Gateway lyssnare rekommenderar vi följande steg för en snabb ändring –

1.  I [Azure Portal](https://portal.azure.com/)navigerar du till dina Azure-inställningar för nyckel valv som har associerats med Application Gateway
2.  Lägg till/importera det återkallade certifikatet i butiken. I dokumentationen [finns mer information](../key-vault/certificates/quick-create-portal.md) om hur du gör.
3.  När certifikatet har importer ATS navigerar du till Application Gateway lyssnar inställningar och under "Välj ett certifikat från Key Vault" klickar du på list rutan "certifikat" och väljer det nyligen tillagda certifikatet
4.  Klicka på Spara om du vill ha mer information om TLS-avslutning på Application Gateway med Key Vault certifikat, Läs dokumentationen [här](./key-vault-certs.md).


Så här uppdaterar du certifikatet i HTTP-inställningarna:

Om du använder v1 SKU för tjänsten Application Gateway/WAF måste du ladda upp det nya certifikatet som ditt certifikat för autentisering av Server del.
1.  Öppna Application Gateway-resursen i [Azure Portal](https://portal.azure.com/)
2.  Öppna de HTTP-inställningar som är associerade med ditt certifikat
3.  Klicka på "Lägg till certifikat" och överför det återutfärdade certifikatet och klicka på Spara
4.  Du kan ta bort det gamla certifikatet senare genom att klicka på "..." alternativ knappen bredvid det gamla certifikatet och välj Ta bort och klicka på Spara.
Mer information hittar du i dokumentationen [här](./end-to-end-ssl-portal.md#add-authenticationtrusted-root-certificates-of-back-end-servers).

Om du använder v2-SKU: n för tjänsten Application Gateway/WAF behöver du inte ladda upp det nya certifikatet i HTTP-inställningarna eftersom v2 SKU använder "betrodda rot certifikat" och ingen åtgärd behöver vidtas här.

## <a name="configuration---ingress-controller-for-aks"></a>Konfiguration – ingress-styrenhet för AKS

### <a name="what-is-an-ingress-controller"></a>Vad är en ingångs kontroll?

Med Kubernetes kan `deployment` du skapa och- `service` resurs för att exponera en grupp poddar internt i klustret. För att exponera samma tjänst externt [`Ingress`](https://kubernetes.io/docs/concepts/services-networking/ingress/) definieras en resurs som tillhandahåller belastnings utjämning, TLS-avslutning och namnbaserade virtuella värdar.
För att tillfredsställa den här `Ingress` resursen krävs en ingångs kontroll som lyssnar efter eventuella ändringar av `Ingress` resurser och konfigurerar belastnings Utjämnings principerna.

Med Application Gateway ingångs styrenhet (AGIC) kan [Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) användas som ingångs punkt för en [Azure Kubernetes-tjänst](https://azure.microsoft.com/services/kubernetes-service/) även kallat ett AKS-kluster.

### <a name="can-a-single-ingress-controller-instance-manage-multiple-application-gateways"></a>Kan en enskild instans av kontroll enheten hantera flera programgatewayer?

För närvarande kan en instans av ingångs styrenheten bara kopplas till en Application Gateway.

### <a name="why-is-my-aks-cluster-with-kubenet-not-working-with-agic"></a>Varför fungerar inte mitt AKS-kluster med Kubernetes med AGIC?

AGIC försöker automatiskt koppla väg tabell resursen till Application Gateway under nätet, men kan Miss lyckas på grund av otillräckliga behörigheter från AGIC. Om AGIC inte kan koppla routningstabellen till Application Gateway under nätet, kommer det att finnas ett fel i AGIC-loggarna, i vilket fall måste du associera routningstabellen som skapats av AKS-klustret manuellt till Application Gatewayens undernät. Mer information finns i [användar definierade vägar som stöds](configuration-infrastructure.md#supported-user-defined-routes).

### <a name="can-i-connect-my-aks-cluster-and-application-gateway-in-separate-virtual-networks"></a>Kan jag ansluta mitt AKS-kluster och Application Gateway i separata virtuella nätverk? 

Ja, så länge de virtuella nätverken har peer-kopplats och de inte har några överlappande adress utrymmen. Om du kör AKS med Kubernetes ska du associera routningstabellen som genererats av AKS till Application Gateway under nätet. 

### <a name="what-features-are-not-supported-on-the-agic-add-on"></a>Vilka funktioner stöds inte för AGIC-tillägget? 

Se skillnaderna mellan AGIC som distribueras via Helm jämfört med distribueras som ett AKS-tillägg [här](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on)

### <a name="when-should-i-use-the-add-on-versus-the-helm-deployment"></a>När ska jag använda tillägget jämfört med Helm-distributionen? 

Se skillnaderna mellan AGIC som distribuerats via Helm jämfört med distribueras som ett AKS-tillägg [här](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on), särskilt tabellerna som dokumenterar vilka scenarier som stöds av AGIC som distribueras via Helm i stället för ett AKS-tillägg. I allmänhet gör distributionen via Helm att du kan testa beta funktioner och lanserings kandidater innan du börjar med en officiell version. 

### <a name="can-i-control-which-version-of-agic-will-be-deployed-with-the-add-on"></a>Kan jag kontrol lera vilken version av AGIC som ska distribueras med tillägget?

Nej, AGIC-tillägg är en hanterad tjänst, vilket innebär att Microsoft automatiskt kommer att uppdatera tillägget till den senaste stabila versionen. 

## <a name="diagnostics-and-logging"></a>Diagnostik och loggning

### <a name="what-types-of-logs-does-application-gateway-provide"></a>Vilka typer av loggar Application Gateway tillhandahåller?

Application Gateway innehåller tre loggar: 

* **ApplicationGatewayAccessLog**: åtkomst loggen innehåller varje begäran som skickats till Application Gateway-frontend. Data innehåller anroparens IP, URL begärd, svars fördröjning, retur kod och byte in och ut. Den innehåller en post per Application Gateway.
* **ApplicationGatewayPerformanceLog**: prestanda loggen samlar in prestanda information för varje Application Gateway. Informationen omfattar data flödet i byte, totalt antal hanterade begär Anden, misslyckade begär Anden och antalet felfria och felaktiga Server dels instanser.
* **ApplicationGatewayFirewallLog**: för programgatewayer som du konfigurerar med WAF innehåller brand Väggs loggen begär Anden som loggas via antingen identifierings läge eller skydds läge.

Alla loggar samlas in var 60: e sekund. Mer information finns i [backend-hälsohälsa, diagnostikloggar och mått för Application Gateway](application-gateway-diagnostics.md).

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>Hur gör jag för att veta om mina medlemmar i Server delens medlemmar är felfria?

Verifiera hälsan med hjälp av PowerShell-cmdleten `Get-AzApplicationGatewayBackendHealth` eller portalen. Mer information finns i [Application Gateway Diagnostics](application-gateway-diagnostics.md).

### <a name="whats-the-retention-policy-for-the-diagnostic-logs"></a>Vad är bevarande principen för diagnostikloggar?

Diagnostiska loggar flödar till kundens lagrings konto. Kunder kan ange bevarande principen utifrån deras preferens. Diagnostikloggar kan också skickas till en Event Hub-eller Azure Monitor-loggar. Mer information finns i [Application Gateway Diagnostics](application-gateway-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>Hur gör jag för att hämta gransknings loggar för Application Gateway?

I portalen väljer du **aktivitets logg** för att få åtkomst till gransknings loggen på Meny bladet för en Programgateway. 

### <a name="can-i-set-alerts-with-application-gateway"></a>Kan jag ange aviseringar med Application Gateway?

Ja. I Application Gateway konfigureras aviseringar för mått. Mer information finns i [Application Gateway mått](./application-gateway-metrics.md) och [ta emot aviseringar](../azure-monitor/alerts/alerts-overview.md).

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>Hur gör jag för att analysera trafik statistik för Application Gateway?

Du kan visa och analysera åtkomst loggar på flera sätt. Använd Azure Monitor loggar, Excel, Power BI och så vidare.

Du kan också använda en Resource Manager-mall som installerar och kör den populära [GoAccess](https://goaccess.io/) log analyzer för Application Gateway åtkomst loggar. GoAccess tillhandahåller värdefull statistik för HTTP-trafik, till exempel unika besökare, begärda filer, värdar, operativ system, webbläsare och HTTP-statuskod. Mer information finns i [Readme-filen i mappen Resource Manager-mall](https://aka.ms/appgwgoaccessreadme)i GitHub.

### <a name="what-could-cause-backend-health-to-return-an-unknown-status"></a>Vad kan orsaka Server dels hälso tillstånd att returnera en okänd status?

Normalt ser du en okänd status när åtkomst till Server delen blockeras av en nätverks säkerhets grupp (NSG), anpassad DNS eller användardefinierad routning (UDR) i Application Gateway-undernätet. Mer information finns i [backend-hälsa, diagnostikloggning och mått för Application Gateway](application-gateway-diagnostics.md).

### <a name="are-nsg-flow-logs-supported-on-nsgs-associated-to-application-gateway-v2-subnet"></a>Stöds NSG flödes loggar på NSG: er som är kopplade till Application Gateway v2-undernätet?

På grund av de aktuella plattforms begränsningarna, om du har en NSG på under nätet Application Gateway v2 (Standard_v2, WAF_v2) och om du har aktiverat NSG flödes loggar på den, kan du se icke deterministiskt beteende och det här scenariot stöds inte för närvarande.

### <a name="where-does-application-gateway-store-customer-data"></a>Var lagrar Application Gateway kund information?

Application Gateway flyttar eller lagrar inte kund information från den region som den har distribuerats i.

## <a name="next-steps"></a>Nästa steg

Mer information om Application Gateway finns i [Vad är Azure Application Gateway?](overview.md).
