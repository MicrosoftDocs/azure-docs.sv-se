---
title: 'Azure-front dörr: vanliga frågor och svar'
description: Den här sidan innehåller svar på vanliga frågor om Azures främre dörr standard/Premium.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: a42601b696f292e9d2a9da90070fea3662acae87
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "101100461"
---
# <a name="frequently-asked-questions-for-azure-front-door-standardpremium-preview"></a>Vanliga frågor och svar om Azures front dörr standard/Premium (för hands version)

I den här artikeln besvaras vanliga frågor om funktioner och funktioner i Azures front-dörr.

## <a name="general"></a>Allmänt

### <a name="what-is-azure-front-door"></a>Vad är Azure Front Door?

Azures front dörr är en snabb, tillförlitlig och säker modern moln-CDN med intelligent hot skydd. Den tillhandahåller statisk och dynamisk innehålls acceleration, global belastnings utjämning och förbättrad säkerhet för dina globala Hyper-Scale-program, API: er, webbplatser och moln tjänster med intelligent hot skydd.

### <a name="what-features-does-azure-front-door-support"></a>Vilka funktioner stöder Azures frontend-dörr?

Azures frontend-dörr stöder:

* Både statiskt innehåll och dynamisk program acceleration
* TLS/SSL-avlastning och slut punkt till slut punkt-TLS
* Brandvägg för webbaserade program
* Cookie-baserad session tillhörighet
* URL-sökväg baserad på Routning
* Kostnads fria certifikat och flera domän hantering

En fullständig lista över funktioner som stöds finns i [Översikt över Azures front dörr](overview.md).

### <a name="what-is-the-difference-between-azure-front-door-and-azure-application-gateway"></a>Vad är skillnaden mellan Azure-dörren och Azure Application Gateway?

Både front dörren och Application Gateway är Layer 7-belastningsutjämnare (HTTP/HTTPS), den främsta skillnaden är att frontend-dörren är en global tjänst. Application Gateway är en regional tjänst. Även om en front dörr kan belastningsutjämna mellan olika enheter/kluster/Stamp-enheter över flera regioner, kan du Application Gateway belastningsutjämna mellan dina virtuella datorer/behållare i skalnings enheten.

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>När bör vi distribuera en Application Gateway bakom en front dörr?

De viktiga scenarier som bör använda Application Gateway bakom front dörren är:

* Front dörren kan bara använda vägbaserade belastnings utjämning på global nivå, men om en vill belastningsutjämna trafik ytterligare i det virtuella nätverket (VNET) bör de använda Application Gateway.
* Eftersom front dörren inte fungerar på en VM/container-nivå, så det går inte att tömma anslutningen. Application Gateway gör det dock möjligt att tömma anslutningarna. 
* Med en Application Gateway bakom en front dörr kan du få 100% TLS/SSL-avlastning och endast dirigera HTTP-begäranden inom sitt virtuella nätverk (VNET).
* Främre dörren och Application Gateway både tillhörighet mellan sessioner. Front dörren kan dirigera trafik från en användarsession till samma kluster eller Server del i en specifik region. Application Gateway kan dirigera tilldela trafiken till samma server i klustret.  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>Kan vi Distribuera Azure Load Balancer bakom en front dörr?

Azures front dörr behöver ett offentligt VIP-namn eller ett offentligt tillgängligt DNS-namn för att dirigera trafiken till. Att distribuera en Azure Load Balancer bakom en front dörr är ett vanligt användnings fall.

### <a name="what-protocols-does-azure-front-door-support"></a>Vilka protokoll stöder Azures frontend-dörr?

Azures front dörr stöder HTTP, HTTPS och HTTP/2.

### <a name="how-does-azure-front-door-support-http2"></a>Hur stöder Azures front dörr HTTP/2?

Stöd för HTTP/2-protokoll är endast tillgängligt för klienter som ansluter till Azures frontend-dörr. Kommunikationen till Server delen i backend-poolen är över HTTP/1.1. HTTP/2-stöd är aktiverat som standard.

### <a name="what-resources-are-supported-today-as-part-of-origin-group"></a>Vilka resurser stöds idag som en del av en ursprungs grupp?

Ursprungs gruppen kan bestå av lagring, webbapp, Kubernetes-instanser eller andra anpassade värd namn som har offentlig anslutning. Azures front dörr kräver att ursprungen definieras antingen via en offentlig IP-adress eller ett offentligt matchat DNS-värdnamn. Medlemmar i gruppen original kan vara mellan zoner, regioner eller till och med utanför Azure så länge de har offentliga anslutningar.

### <a name="what-regions-is-the-service-available-in"></a>Vilka regioner finns tjänsten i?

Azures front dörr är en global tjänst som inte är kopplad till någon enskild Azure-region. Den enda plats som du måste ange när du skapar en front dörr är för resurs gruppen. Den platsen anger i princip var metadata för resurs gruppen ska lagras. Resursen front dörr skapas som en global resurs och konfigurationen distribueras globalt till alla pop (punkt för närvaro). 

### <a name="what-are-the-pop-locations-for-azure-front-door"></a>Vilka är POP-platserna för Azures front dörr?

Azures front dörr har samma lista med POP-platser (punkt för närvaro) som Azure CDN från Microsoft. En fullständig lista över våra pop- [platser finns Azure CDN pop-platser från Microsoft](../../cdn/cdn-pop-locations.md).

### <a name="is-azure-front-door-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>Är Azure front dörr en dedikerad distribution för mitt program eller delas den mellan kunder?

Azures front dörr är en globalt distribuerad tjänst för flera innehavare. Infrastrukturen för front dörren delas i alla sina kunder. Genom att skapa en profil för en front dörr definierar du den specifika konfiguration som krävs för ditt program. Ändringar som görs i din front dörr påverkar inte andra konfigurationer för front dörren.

### <a name="is-http-https-redirection-supported"></a>Stöds HTTP->HTTPS-omdirigering?

Ja. Azure-front dörren stöder faktiskt värd, sökväg, omdirigering av frågesträng och en del av URL-omdirigering. Läs mer om [URL-omdirigering](concept-rule-set-url-redirect-and-rewrite.md). 

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door"></a>Hur gör jag för att du bara låsa åtkomsten till min server del till Azures front dörr?

Om du vill låsa ditt program så att det bara accepterar trafik från din specifika front dörr måste du konfigurera IP-ACL: er för Server delen. Begränsa sedan trafiken för Server delen till det angivna värdet för sidhuvudet "X-Azure-FDID" som skickas av front dörren. De här stegen beskrivs nedan:

* Konfigurera IP-ACLing för dina Server delar för att acceptera trafik från Azure-klientens Server dels IP-adressutrymme och Azures infrastruktur tjänster. Se IP-informationen nedan för att ACLing din server del:
 
    * Mer information finns i avsnittet om *AzureFrontDoor. backend* i [Azure IP-intervall och service märken](https://www.microsoft.com/download/details.aspx?id=56519) för IP-adressintervallet för IPv4-server delen i front dörren. Du kan också använda service tag- *AzureFrontDoor. backend* i dina [nätverks säkerhets grupper](../../virtual-network/network-security-groups-overview.md#security-rules).
    * Azures [grundläggande infrastruktur tjänster](../../virtual-network/network-security-groups-overview.md#azure-platform-considerations) via virtualiserade värd-IP-adresser: `168.63.129.16` och `169.254.169.254` .

    > [!WARNING]
    > Front dörrens IP-utrymme kan ändras senare, men vi kommer att se till att vi har integrerat med [Azure IP-intervall och service Taggar](https://www.microsoft.com/download/details.aspx?id=56519)innan det inträffar. Vi rekommenderar att du prenumererar på [Azure IP-intervall och service märken](https://www.microsoft.com/download/details.aspx?id=56519) för ändringar eller uppdateringar.

* Utför en GET-åtgärd på din front dörr med API-versionen `2020-01-01` eller högre. Leta efter fält i API-anropet `frontdoorID` . Filtrera på det inkommande huvudet "**X-Azure-FDID**" som skickas av front dörren till din server del med värdet i fältet `frontdoorID` . Du kan också hitta `Front Door ID` ett värde under avsnittet Översikt från Portal sidan front dörr. 

* Använd regel filtrering på backend-webbservern för att begränsa trafik baserat på det resulterande "X-Azure-FDID"-huvudets värde.

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

Klient delens anycast-IP för din front dörr bör normalt inte ändras och kan vara statisk för livs längden för front dörren. Det finns dock **inga garantier** för samma. Gör inga direkta beroenden av IP-adressen.

### <a name="does-azure-front-door-support-static-or-dedicated-ips"></a>Stöder Azures front dörr statiska eller dedikerade IP-adresser?

Nej, Azures front dörr stöder för närvarande inte statiska eller dedikerade anycast-IP: er för klient delen. 

### <a name="does-azure-front-door-support-x-forwarded-for-headers"></a>Stöder Azure front dörr x-vidarebefordrad – för rubriker?

Ja, Azure-frontend stöder de X-vidarebefordrade-för-, X-vidarebefordrade-värd-och X-forwarded-proto-sidhuvudena. För X-vidarebefordrad – för om huvudet redan fanns lägger front dörren till klientens socket-IP till den. Annars lägger den till rubriken med klientens socket-IP som värde. För X-vidarebefordrad-värd och X-forwarded-proto, åsidosätts värdet.  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-does-my-front-door-still-work-when-being-updated"></a>Hur lång tid tar det att distribuera en Azure-frontend-dörr? Fungerar min front dörr fortfarande när den uppdateras?

En ny front dörr skapas eller alla uppdateringar av en befintlig front dörr tar cirka 3 till 5 minuter för global distribution. Det innebär att när du använder 3 till 5 minuter, distribueras din konfiguration av din front dörr över alla dina pop globalt.

Obs! anpassade TLS/SSL-certifikat uppdateringar tar cirka 30 minuter att distribueras globalt.

Alla uppdateringar av vägar eller backend-pooler är sömlösa och kan orsaka noll avbrotts tid (om den nya konfigurationen är korrekt). Certifikat uppdateringar orsakar inga avbrott, såvida du inte byter från "Azure front dörr Managed" to "Använd ditt eget certifikat" eller det andra sättet runt.


## <a name="configuration"></a>Konfiguration

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>Kan Azures belastnings utjämning för front dörren eller dirigera trafik inom ett virtuellt nätverk?

Azures front dörr (AFD) kräver en offentlig IP-adress eller ett offentligt matchat DNS-namn för att dirigera trafik. Azures frontend-dörr kan inte dirigera direkt till resurser i ett virtuellt nätverk. Du kan lösa problemet genom att använda en Application Gateway eller ett Azure Load Balancer med en offentlig IP-adress.

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door"></a>Vilka är de olika tids gränserna och gränserna för Azures front dörr?

Lär dig mer om alla dokumenterade [tids gränser och begränsningar för Azures front dörr](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-front-door-service-limits).

### <a name="how-long-does-it-take-for-a-rule-to-take-effect-after-being-added-to-the-front-door-rules-engine"></a>Hur lång tid tar det innan en regel börjar gälla när den har lagts till i motorn för front dörrs regler?

Konfigurationen av regel motorn tar cirka 10 till 15 minuter att slutföra en uppdatering. Du kan se till att regeln börjar gälla så fort uppdateringen har slutförts. 

### <a name="can-i-configure-azure-cdn-behind-my-front-door-profile-or-front-door-behind-my-azure-cdn"></a>Kan jag konfigurera Azure CDN bakom min profil för front dörren eller front dörren bakom min Azure CDN?

Azures front dörr och Azure CDN kan inte konfigureras tillsammans eftersom båda tjänsterna använder samma Azure Edge-webbplatser när de svarar på begär Anden. 

## <a name="performance"></a>Prestanda

### <a name="how-does-azure-front-door-support-high-availability-and-scalability"></a>Hur stöder Azures front dörr hög tillgänglighet och skalbarhet?

Azures front dörr är en globalt distribuerad plattform för flera innehavare med stor kapacitet för att tillgodose ditt programs skalbarhets behov. Front dörren är en global belastnings Utjämnings funktion som gör att du kan redundansväxla hela programmet eller till och med enskilda mikrotjänster i regioner eller olika moln.

## <a name="tls-configuration"></a>TLS-konfiguration

### <a name="what-tls-versions-are-supported-by-azure-front-door"></a>Vilka TLS-versioner stöds av Azures frontend-dörr?

Alla profiler för front dörren som skapats efter september 2019 använder TLS 1,2 som standard minimum.

Frontend-dörren stöder TLS-versionerna 1,0, 1,1 och 1,2. TLS 1,3 stöds inte ännu.

### <a name="what-certificates-are-supported-on-azure-front-door"></a>Vilka certifikat stöds på Azures frontend-dörr?

Om du vill aktivera HTTPS-protokollet på en anpassad domän för en front dörr kan du välja ett certifikat som hanteras av Azures frontend-dörr eller använda ditt eget certifikat.
Alternativet front dörr Managed tillhandahåller ett TLS/SSL-standardcertifikat via DigiCert och lagras i front dörrens Key Vault. Om du väljer att använda ditt eget certifikat kan du publicera ett certifikat från en certifikat utfärdare som stöds och kan vara ett standard-TLS, ett utökat verifierings certifikat eller till och med ett jokertecken. Självsignerade certifikat stöds inte.

### <a name="does-front-door-support-autorotation-of-certificates"></a>Stöder front dörren autorotation av certifikat?

För alternativet klient som hanteras av den främre dörren, roteras certifikaten autoefter front dörren. Om du använder ett certifikat från en front dörr och ser att utgångs datumet för certifikatet är mindre än 60 dagar, File a Support Ticket.

Autorotation stöds inte för ditt eget anpassade TLS/SSL-certifikat. På samma sätt som du ställer in första gången för en viss anpassad domän måste du peka fram till rätt certifikat version i din Key Vault. Se till att tjänstens huvud namn för front dörren fortfarande har till gång till Key Vault. Den uppdaterade certifikat distributions åtgärden per front dörr orsakar inga produktions tider förutsatt att mottagar namnet eller SAN för certifikatet inte ändras.

### <a name="what-are-the-current-cipher-suites-supported-by-azure-front-door"></a>Vilka är de aktuella chiffersviter som stöds av Azures frontend-dörr?

Följande chiffersviter stöds för TLS 1.2: 

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256

Att använda anpassade domäner med TLS 1.0/1.1 aktiverat följande chiffersviter stöds:

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

Du kan konfigurera en lägsta TLS-version i Azure-frontend i den anpassade domänens HTTPS-inställningar med hjälp av Azure Portal eller [Azure REST API](/rest/api/frontdoorservice/frontdoor/frontdoors/createorupdate#minimumtlsversion). För närvarande kan du välja mellan 1,0 och 1,2.

### <a name="can-i-configure-front-door-to-only-support-specific-cipher-suites"></a>Kan jag konfigurera front dörren till att bara stödja vissa chiffersviter?

Nej, det finns inte stöd för att konfigurera front dörren för speciella chiffersviter. Du kan hämta ditt eget anpassade TLS/SSL-certifikat från din certifikat utfärdare (t. ex. VeriSign, Entrust eller DigiCert). Sedan har vissa chiffersviter marker ATS på certifikatet när du genererar det. 

### <a name="does-front-door-support-ocsp-stapling"></a>Har front dörr stöd för OCSP-häftnings funktioner?

Ja, OCSP-häftnings funktion stöds som standard av front dörren och ingen konfiguration krävs.

### <a name="does-azure-front-door-also-support-re-encryption-of-traffic-to-the-backend"></a>Stöder Azures front dörr återkryptering av trafik till Server delen?

Ja, Azure-frontend stöder TLS/SSL-avlastning och slut punkt till slut punkt TLS, som omkrypterar trafiken till Server delen. Eftersom anslutningarna till Server delen sker över den offentliga IP-adressen rekommenderar vi att du konfigurerar din front dörr att använda HTTPS som protokoll för vidarebefordran.

### <a name="does-front-door-support-self-signed-certificates-on-the-backend-for-https-connection"></a>Stöder front dörren självsignerade certifikat på Server delen för HTTPS-anslutningen?

Nej, självsignerade certifikat stöds inte på front dörren och begränsningen gäller för båda:

* Server **delar: du** kan inte använda självsignerade certifikat när du vidarebefordrar trafiken som https-eller https-hälsoavsökare eller fyller i cachen för från ursprung för routningsregler med cachelagring aktiverat.
* **Klient** del: du kan inte använda självsignerade certifikat när du använder ditt eget anpassade TLS/SSL-certifikat för att aktivera https på din anpassade domän.

### <a name="why-is-https-traffic-to-my-backend-failing"></a>Varför fungerar inte HTTPS-trafik till min server del?

Om du har lyckade HTTPS-anslutningar till din server del om det finns hälso avsökningar eller begär Anden om vidarebefordran kan det finnas två orsaker till varför HTTPS-trafik kan Miss lyckas:

* **Certifikatets ämnes namn matchar inte**: för HTTPS-anslutningar förväntar sig front dörren att Server delen visar certifikat från en giltig certifikat utfärdare med ämnes namn som matchar backend-värdnamnet. Exempel: om ditt Server namn är inställt på `myapp-centralus.contosonews.net` och det certifikat som din server dels visar under TLS-handskakningen inte har `myapp-centralus.contosonews.net` eller `*myapp-centralus*.contosonews.net` i ämnes namnet. Sedan kommer front dörren att neka anslutningen och resultera i ett fel. 
    * **Lösning**: det rekommenderas inte från ett kompatibilitetstillstånd, men du kan undvika det här felet genom att inaktivera kontroll av certifikatets ämnes namn för din front dörr. Du kan hitta det här alternativet under Inställningar i Azure Portal och under BackendPoolsSettings i API: et.
* **Server dels värd certifikat från ogiltig certifikat utfärdare**: endast certifikat från [giltiga certifikat utfärdare](troubleshoot-allowed-certificate-authority.md) kan användas på Server delen med front dörren. Certifikat från interna certifikat utfärdare eller självsignerade certifikat är inte tillåtna.

### <a name="can-i-use-clientmutual-authentication-with-azure-front-door"></a>Kan jag använda klient/ömsesidig autentisering med Azures frontend-dörr?

Nej. Även om Azures frontend-dörr stöder TLS 1,2, som introducerade klient-och ömsesidig autentisering i [RFC 5246](https://tools.ietf.org/html/rfc5246), stöder för närvarande inte klient/ömsesidig autentisering i Azures front dörr.

## <a name="diagnostics-and-logging"></a>Diagnostik och loggning

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door"></a>Vilka typer av mått och loggar är tillgängliga med Azures frontend-dörr?

Information om loggar och andra diagnostiska funktioner finns i övervaka mått och loggar för front dörren.

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>Vad är bevarande principen för diagnostikloggar?

Diagnostikloggar loggar till lagrings kontot för kunder och kunderna kan ange bevarande principen utifrån deras preferens. Diagnostikloggar kan också skickas till en Event Hub-eller Azure Monitor-loggar. Mer information finns i [Azures frontend-loggning](how-to-logs.md).

### <a name="how-do-i-get-audit-logs-for-azure-front-door"></a>Hur gör jag för att hämta gransknings loggar för Azures frontend-dörr?

Gransknings loggar är tillgängliga för Azures front dörr. I portalen väljer du **aktivitets logg** på Meny sidan för din front dörr för att få åtkomst till gransknings loggen. 

### <a name="can-i-set-alerts-with-azure-front-door"></a>Kan jag ställa in aviseringar med Azures front dörr?

Ja, Azures front dörr stöder aviseringar. Aviseringar har kon figurer ATS för mått. 

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [skapar en front dörrs standard/Premium](create-front-door-portal.md).
