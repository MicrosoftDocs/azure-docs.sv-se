---
title: Azure-front dörr – vanliga frågor och svar
description: Den här sidan innehåller svar på vanliga frågor om Azures front dörr
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/20/2020
ms.author: duau
ms.openlocfilehash: 6fdcedd19c02251035a838d34ba51e786595134b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102212458"
---
# <a name="frequently-asked-questions-for-azure-front-door"></a>Vanliga frågor och svar om Azures front dörr

I den här artikeln besvaras vanliga frågor om funktioner och funktioner i Azures front-dörr. Om du inte ser svaret på din fråga kan du kontakta oss genom följande kanaler (i eskalerad ordning):

1. Avsnittet kommentarer i den här artikeln.
2. [UserVoice för Azure-dörren](https://feedback.azure.com/forums/217313-networking?category_id=345025).
3. **Microsoft Support:** Om du vill skapa en ny supportbegäran går du till fliken **Hjälp** i Azure Portal, väljer **Hjälp + Support** -knappen och väljer sedan **ny supportbegäran**.

## <a name="general"></a>Allmänt

### <a name="what-is-azure-front-door"></a>Vad är Azure Front Door?

Azures front dörr är en Application Delivery Network (och) som en tjänst som erbjuder olika belastnings Utjämnings funktioner i Layer 7 för dina program. Den erbjuder acceleration av dynamisk webbplats tillsammans med global belastningsutjämning med redundansväxling nästan i realtid. Tjänsten har hög tillgänglighet och skalbarhet, och hanteras helt av Azure.

### <a name="what-features-does-azure-front-door-support"></a>Vilka funktioner stöder Azures frontend-dörr?

Azure-front dörren stöder dynamisk webbplats acceleration (DSA), TLS/SSL-avlastning och slut punkt till slut punkt för TLS, WebApplication-brandvägg, cookie-baserad sessionsgräns, URL-sökväg-baserad routning, lediga certifikat och flera domän hantering, och andra. En fullständig lista över funktioner som stöds finns i [Översikt över Azures front dörr](front-door-overview.md).

### <a name="what-is-the-difference-between-azure-front-door-and-azure-application-gateway"></a>Vad är skillnaden mellan Azure-dörren och Azure Application Gateway?

Både front dörren och Application Gateway är Layer 7-belastningsutjämnare (HTTP/HTTPS), den främsta skillnaden är att frontend-dörren är en global tjänst, medan Application Gateway är en regional tjänst. Även om en front dörr kan belastningsutjämna mellan dina olika skal enheter/kluster/Stamp-enheter över flera regioner kan du Application Gateway belastningsutjämna mellan dina virtuella datorer/behållare osv. i skalnings enheten.

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>När bör vi distribuera en Application Gateway bakom en front dörr?

De viktiga scenarier som bör använda Application Gateway bakom front dörren är:

- Front dörren kan bara utföra Path-baserad belastnings utjämning på global nivå, men om en vill belastningsutjämna trafik ytterligare i det virtuella nätverket (VNET) bör de använda Application Gateway.
- Eftersom front dörren inte fungerar på en VM/container-nivå, så det går inte att tömma anslutningen. Application Gateway gör det dock möjligt att tömma anslutningarna. 
- Med en Application Gateway bakom en front dörr kan du få 100% TLS/SSL-avlastning och endast dirigera HTTP-begäranden inom sitt virtuella nätverk (VNET).
- Främre dörren och Application Gateway både tillhörighet mellan sessioner. Även om front dörren kan dirigera efterföljande trafik från en användarsession till samma kluster eller Server del i en specifik region, kan Application Gateway dirigera tilldela trafiken till samma server i klustret.  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>Kan vi Distribuera Azure Load Balancer bakom en front dörr?

Azures front dörr behöver ett offentligt VIP-namn eller ett offentligt tillgängligt DNS-namn för att dirigera trafiken till. Att distribuera en Azure Load Balancer bakom en front dörr är ett vanligt användnings fall.

### <a name="what-protocols-does-azure-front-door-support"></a>Vilka protokoll stöder Azures frontend-dörr?

Azures front dörr stöder HTTP, HTTPS och HTTP/2.

### <a name="how-does-azure-front-door-support-http2"></a>Hur stöder Azures front dörr HTTP/2?

Stöd för HTTP/2-protokoll är endast tillgängligt för klienter som ansluter till Azures frontend-dörr. Kommunikationen till Server delen i backend-poolen är över HTTP/1.1. HTTP/2-stöd är aktiverat som standard.

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>Vilka resurser stöds idag som en del av backend-poolen?

Backend-pooler kan bestå av lagring, webbappar, Kubernetes-instanser eller andra anpassade värd namn som har offentlig anslutning. Azures front dörr kräver att Server delarna definieras antingen via en offentlig IP-adress eller ett offentligt matchat DNS-värdnamn. Medlemmar i backend-pooler kan vara mellan zoner, regioner eller till och med utanför Azure så länge de har offentlig anslutning.

### <a name="what-regions-is-the-service-available-in"></a>Vilka regioner finns tjänsten i?

Azures front dörr är en global tjänst som inte är kopplad till någon enskild Azure-region. Den enda plats som du måste ange när du skapar en front dörr är resurs gruppens plats, som i princip anger var metadata för resurs gruppen ska lagras. Resursen front dörr skapas som en global resurs och konfigurationen distribueras globalt till alla pop (punkt för närvaro). 

### <a name="what-are-the-pop-locations-for-azure-front-door"></a>Vilka är POP-platserna för Azures front dörr?

Azures front dörr har samma lista med POP-platser (punkt för närvaro) som Azure CDN från Microsoft. En fullständig lista över våra pop- [platser finns Azure CDN pop-platser från Microsoft](../cdn/cdn-pop-locations.md).

### <a name="is-azure-front-door-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>Är Azure front dörr en dedikerad distribution för mitt program eller delas den mellan kunder?

Azures front dörr är en globalt distribuerad tjänst för flera innehavare. Därför delas infrastrukturen för front dörren över alla sina kunder. Genom att skapa en profil för en frontend-profil definierar du dock den specifika konfiguration som krävs för ditt program och inga ändringar som gjorts i din front dörr påverkar andra konfigurations inställningar för front dörren.

### <a name="is-http-https-redirection-supported"></a>Stöds HTTP->HTTPS-omdirigering?

Ja. I själva verket stöder Azures front dörr värd, sökväg och omdirigering av frågesträngar samt en del av URL-omdirigeringen. Läs mer om [URL-omdirigering](front-door-url-redirect.md). 

### <a name="in-what-order-are-routing-rules-processed"></a>I vilken ordning bearbetas routningsregler?

Vägar för din front dörr sorteras inte och en viss väg väljs utifrån den bästa matchningen. Läs mer om [hur front dörr matchar begär anden till en regel för routning](front-door-route-matching.md).

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door"></a>Hur gör jag för att du bara låsa åtkomsten till min server del till Azures front dörr?

> [!NOTE]
> Ny SKU front dörr Premium är ett mer rekommenderat sätt att låsa ditt program via privat slut punkt. [Läs mer om privat slut punkt](./standard-premium/concept-private-link.md)

Om du vill låsa ditt program för att endast acceptera trafik från din specifika front dörr måste du konfigurera IP-ACL: er för Server delen och sedan begränsa trafiken på Server delen till det specifika värdet för huvudet "X-Azure-FDID" som skickas av en front dörr. De här stegen beskrivs nedan:

- Konfigurera IP-ACLing för dina Server delar för att acceptera trafik från Azure-klientens Server dels IP-adressutrymme och Azures infrastruktur tjänster. Se IP-informationen nedan för att ACLing din server del:
 
    - Mer information finns i avsnittet om *AzureFrontDoor. backend* i [Azure IP-intervall och service märken](https://www.microsoft.com/download/details.aspx?id=56519) för IP-adressintervall för IPv4-backend-IP-adresser eller också kan du använda service tag- *AzureFrontDoor. backend* i dina [nätverks säkerhets grupper](../virtual-network/network-security-groups-overview.md#security-rules).
    - Azures [grundläggande infrastruktur tjänster](../virtual-network/network-security-groups-overview.md#azure-platform-considerations) via virtualiserade värd-IP-adresser: `168.63.129.16` och `169.254.169.254`

    > [!WARNING]
    > Front dörrens IP-utrymme kan ändras senare, men vi kommer att se till att vi har integrerat med [Azure IP-intervall och service Taggar](https://www.microsoft.com/download/details.aspx?id=56519)innan det inträffar. Vi rekommenderar att du prenumererar på [Azure IP-intervall och service märken](https://www.microsoft.com/download/details.aspx?id=56519) för ändringar eller uppdateringar.

- Leta efter `Front Door ID` värdet under översikts avsnittet från den främre dörr Portal sidan. Du kan sedan filtrera på det inkommande huvudet "**X-Azure-FDID**" som skickas av front dörren till Server delen med det värdet för att säkerställa att endast din egen specifika instans av front dörren tillåts (eftersom IP-intervallen ovan delas med andra instanser av andra instanser av andra kunder).

- Använd regel filtrering på backend-webbservern för att begränsa trafik baserat på det resulterande "X-Azure-FDID"-huvudets värde. Observera att vissa tjänster som Azure App Service ger denna [rubrikbaserade filtrerings](../app-service/app-service-ip-restrictions.md#restrict-access-to-a-specific-azure-front-door-instance-preview) kapacitet utan att behöva ändra ditt program eller din värd.

  Här är ett exempel på [Microsoft Internet Information Services (IIS)](https://www.iis.net/):

    ``` xml
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
        <system.webServer>
            <rewrite>
                <rules>
                    <rule name="Filter_X-Azure-FDID" patternSyntax="Wildcard" stopProcessing="true">
                        <match url="*" />
                        <conditions>
                            <add input="{HTTP_X_AZURE_FDID}" pattern="xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" negate="true" />
                        </conditions>
                        <action type="AbortRequest" />
                    </rule>
                </rules>
            </rewrite>
        </system.webServer>
    </configuration>
    ```



### <a name="can-the-anycast-ip-change-over-the-lifetime-of-my-front-door"></a>Kan anycast-IP-förändring under hela front dörren?

Klient delens anycast-IP för din front dörr bör normalt inte ändras och kan vara statisk för livs längden för front dörren. Det finns dock **inga garantier** för samma. Skriv inte några direkta beroenden på IP-adressen.

### <a name="does-azure-front-door-support-static-or-dedicated-ips"></a>Stöder Azures front dörr statiska eller dedikerade IP-adresser?

Nej, Azures front dörr stöder för närvarande inte statiska eller dedikerade anycast-IP: er för klient delen. 

### <a name="does-azure-front-door-support-x-forwarded-for-headers"></a>Stöder Azure front dörr x-vidarebefordrad – för rubriker?

Ja, Azure-frontend stöder de X-vidarebefordrade-för-, X-vidarebefordrade-värd-och X-forwarded-proto-sidhuvudena. För X-vidarebefordrad – för om huvudet redan fanns lägger front dörren till klientens socket-IP till den. Annars lägger den till rubriken med klientens socket-IP som värde. För X-vidarebefordrad-värd och X-forwarded-proto, åsidosätts värdet.

Läs mer om [HTTP-huvuden som stöds av frontend-dörren](front-door-http-headers-protocol.md).  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-does-my-front-door-still-work-when-being-updated"></a>Hur lång tid tar det att distribuera en Azure-frontend-dörr? Fungerar min front dörr fortfarande när den uppdateras?

En ny front dörr skapas eller alla uppdateringar av en befintlig front dörr tar cirka 3 till 5 minuter för global distribution. Det innebär att när du använder 3 till 5 minuter, distribueras din konfiguration av din front dörr över alla dina pop globalt.

Obs! anpassade TLS/SSL-certifikat uppdateringar tar cirka 30 minuter att distribueras globalt.

Eventuella uppdateringar av vägar eller backend-pooler är sömlösa och kommer att orsaka noll stillestånds tid (om den nya konfigurationen är korrekt). Certifikat uppdateringar är också atomiska och orsakar inga avbrott, om du inte växlar från "AFD Managed" till "Använd ditt eget certifikat" eller vice versa.


## <a name="configuration"></a>Konfiguration

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>Kan Azures belastnings utjämning för front dörren eller dirigera trafik inom ett virtuellt nätverk?

Azures front dörr (AFD) kräver en offentlig IP-adress eller offentligt matchat DNS-namn för att dirigera trafik. Svaret är därför ingen AFD direkt kan inte dirigera inom ett virtuellt nätverk, men att använda en Application Gateway eller Azure Load Balancer mellan kommer att lösa det här scenariot.

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door"></a>Vilka är de olika tids gränserna och gränserna för Azures front dörr?

Lär dig mer om alla dokumenterade [tids gränser och begränsningar för Azures front dörr](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-front-door-service-limits).

### <a name="how-long-does-it-take-for-a-rule-to-take-effect-after-being-added-to-the-front-door-rules-engine"></a>Hur lång tid tar det innan en regel börjar gälla när den har lagts till i motorn för front dörrs regler?

Konfigurationen av regel motorn tar cirka 10 till 15 minuter att slutföra en uppdatering. Du kan se till att regeln börjar gälla så fort uppdateringen har slutförts. 

### <a name="can-i-configure-azure-cdn-behind-my-front-door-profile-or-vice-versa"></a>Kan jag konfigurera Azure CDN bakom min profil för front dörren eller vice versa?

Azures front dörr och Azure CDN kan inte konfigureras tillsammans eftersom båda tjänsterna använder samma Azure Edge-webbplatser när de svarar på begär Anden. 

## <a name="performance"></a>Prestanda

### <a name="how-does-azure-front-door-support-high-availability-and-scalability"></a>Hur stöder Azures front dörr hög tillgänglighet och skalbarhet?

Azures frontend är en globalt distribuerad plattform för flera innehavare med enorma volymer av kapacitet för att tillgodose ditt programs skalbarhets behov. Front dörren är en global belastnings Utjämnings funktion som gör att du kan redundansväxla hela programmet eller till och med enskilda mikrotjänster i regioner eller olika moln.

## <a name="tls-configuration"></a>TLS-konfiguration

### <a name="what-tls-versions-are-supported-by-azure-front-door"></a>Vilka TLS-versioner stöds av Azures frontend-dörr?

Alla profiler för front dörren som skapats efter september 2019 använder TLS 1,2 som standard minimum.

Frontend-dörren stöder TLS-versionerna 1,0, 1,1 och 1,2. TLS 1,3 stöds inte ännu.

### <a name="what-certificates-are-supported-on-azure-front-door"></a>Vilka certifikat stöds på Azures frontend-dörr?

Om du vill aktivera HTTPS-protokollet för säker leverans av innehåll på en anpassad domän i en annan dator, kan du välja att använda ett certifikat som hanteras av Azures front dörr eller använda ditt eget certifikat.
Alternativet front dörr Managed tillhandahåller ett TLS/SSL-standardcertifikat via DigiCert och lagras i front dörrens Key Vault. Om du väljer att använda ditt eget certifikat kan du publicera ett certifikat från en certifikat utfärdare som stöds och kan vara ett standard-TLS, ett utökat verifierings certifikat eller till och med ett jokertecken. Självsignerade certifikat stöds inte. Lär dig [hur du aktiverar HTTPS för en anpassad domän](./front-door-custom-domain-https.md).

### <a name="does-front-door-support-autorotation-of-certificates"></a>Stöder front dörren autorotation av certifikat?

För alternativet klient som hanteras av den främre dörren, roteras certifikaten autoefter front dörren. Om du använder ett certifikat från en front dörr och ser att utgångs datumet för certifikatet är mindre än 60 dagar, File a Support Ticket.
</br>Autorotation stöds inte för ditt eget anpassade TLS/SSL-certifikat. På samma sätt som när den konfigurerades första gången för en viss anpassad domän måste du peka fram sidan till rätt certifikat version i din Key Vault och kontrol lera att tjänstens huvud namn för front dörren fortfarande har åtkomst till Key Vault. Den här uppdaterade certifikat distributions åtgärden efter front dörren är atomisk och orsakar ingen produktions påverkan förutsatt att ämnes namnet eller SAN-nätverket för certifikatet inte ändras.

### <a name="what-are-the-current-cipher-suites-supported-by-azure-front-door"></a>Vilka är de aktuella chiffersviter som stöds av Azures frontend-dörr?

Följande chiffersviter stöds för TLS 1.2: 

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256

När du använder anpassade domäner med TLS 1.0/1.1 aktiverat kan följande chiffersviter användas:

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384

### <a name="can-i-configure-tls-policy-to-control-tls-protocol-versions"></a>Kan jag konfigurera TLS-principen för att kontrol lera TLS-protokoll versioner?

Du kan konfigurera en lägsta TLS-version i Azure-front dörren i de anpassade HTTPS-inställningarna för domänen via Azure Portal eller [Azure-REST API](/rest/api/frontdoorservice/frontdoor/frontdoors/createorupdate#minimumtlsversion). För närvarande kan du välja mellan 1,0 och 1,2.

### <a name="can-i-configure-front-door-to-only-support-specific-cipher-suites"></a>Kan jag konfigurera front dörren till att bara stödja vissa chiffersviter?

Nej, det finns inte stöd för att konfigurera front dörren för speciella chiffersviter. Du kan dock hämta ditt eget anpassade TLS/SSL-certifikat från certifikat utfärdaren (t. ex. VeriSign, Entrust eller DigiCert) och ha särskilda chiffersviter som är markerade för certifikatet när du har skapat det. 

### <a name="does-front-door-support-ocsp-stapling"></a>Har front dörr stöd för OCSP-häftnings funktioner?

Ja, OCSP-häftnings funktion stöds som standard av front dörren och ingen konfiguration krävs.

### <a name="does-azure-front-door-also-support-re-encryption-of-traffic-to-the-backend"></a>Stöder Azures front dörr återkryptering av trafik till Server delen?

Ja, Azure-frontend stöder TLS/SSL-avlastning och slut punkt till slut punkt TLS, som omkrypterar trafiken till Server delen. Eftersom anslutningarna till Server delen sker över den offentliga IP-adressen rekommenderar vi att du konfigurerar din front dörr att använda HTTPS som protokoll för vidarebefordran.

### <a name="does-front-door-support-self-signed-certificates-on-the-backend-for-https-connection"></a>Stöder front dörren självsignerade certifikat på Server delen för HTTPS-anslutningen?

Nej, självsignerade certifikat stöds inte på frontend-dörren och begränsningen gäller för båda:

1. Server **delar: du** kan inte använda självsignerade certifikat när du vidarebefordrar trafiken som https-eller https-hälsoavsökare eller fyller i cachen för från ursprung för routningsregler med cachelagring aktiverat.
2. **Klient** del: du kan inte använda självsignerade certifikat när du använder ditt eget anpassade TLS/SSL-certifikat för att aktivera https på den anpassade domänen.

### <a name="why-is-https-traffic-to-my-backend-failing"></a>Varför fungerar inte HTTPS-trafik till min server del?

Om du har lyckade HTTPS-anslutningar till din server del om det finns hälso avsökningar eller begär Anden om vidarebefordran kan det finnas två orsaker till varför HTTPS-trafik kan Miss lyckas:

1. **Certifikatets ämnes namn matchar inte**: för HTTPS-anslutningar förväntar sig front dörren att Server delen visar certifikat från en giltig certifikat utfärdare med ämnes namn som matchar backend-värdnamnet. Exempel: om ditt Server dels namn är inställt på `myapp-centralus.contosonews.net` och det certifikat som din server dels visar under TLS-handskakningen varken har `myapp-centralus.contosonews.net` eller `*myapp-centralus*.contosonews.net` i ämnes namnet, kommer front dörren att neka anslutningen och resultera i ett fel. 
    1. **Lösning**: även om det inte rekommenderas från en efterlevnadsprincip kan du lösa det här felet genom att inaktivera kontroll av certifikatets ämnes namn för din front dörr. Detta finns under Inställningar i Azure Portal och under BackendPoolsSettings i API: et.
2. **Server dels värd certifikat från ogiltig certifikat utfärdare**: endast certifikat från [giltiga certifikat utfärdare](./front-door-troubleshoot-allowed-ca.md) kan användas på Server delen med frontend. Certifikat från interna certifikat utfärdare eller självsignerade certifikat är inte tillåtna.

### <a name="can-i-use-clientmutual-authentication-with-azure-front-door"></a>Kan jag använda klient/ömsesidig autentisering med Azures frontend-dörr?

Nej. Även om Azures frontend-dörr stöder TLS 1,2, som introducerade klient-och ömsesidig autentisering i [RFC 5246](https://tools.ietf.org/html/rfc5246), stöder för närvarande inte klient/ömsesidig autentisering i Azures front dörr.

## <a name="diagnostics-and-logging"></a>Diagnostik och loggning

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door"></a>Vilka typer av mått och loggar är tillgängliga med Azures frontend-dörr?

Information om loggar och andra diagnostiska funktioner finns i [övervaka mått och loggar för front dörren](front-door-diagnostics.md).

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>Vad är bevarande principen för diagnostikloggar?

Diagnostikloggar loggar till lagrings kontot för kunder och kunderna kan ange bevarande principen utifrån deras preferens. Diagnostikloggar kan också skickas till en Event Hub-eller Azure Monitor-loggar. Mer information finns i [Azure-diagnostik för front dörr](front-door-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-azure-front-door"></a>Hur gör jag för att hämta gransknings loggar för Azures frontend-dörr?

Gransknings loggar är tillgängliga för Azures front dörr. I portalen klickar du på **aktivitets logg** på Meny bladet för din front dörr för att få åtkomst till gransknings loggen. 

### <a name="can-i-set-alerts-with-azure-front-door"></a>Kan jag ställa in aviseringar med Azures front dörr?

Ja, Azures front dörr stöder aviseringar. Aviseringar har kon figurer ATS för mått. 

## <a name="next-steps"></a>Nästa steg

- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
- Läs [hur Front Door fungerar](front-door-routing-architecture.md).
