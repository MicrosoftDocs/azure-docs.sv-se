---
title: Vad är en brand vägg för Azure Web Application på Azures front dörr?
description: Lär dig hur Azure-webbprograms brand vägg på Azures frontend-tjänst skyddar dina webb program mot skadliga attacker.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 05/22/2020
ms.author: victorh
ms.openlocfilehash: 66ca039a449e355e47563b689f5f44cced164fea
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101724157"
---
# <a name="azure-web-application-firewall-on-azure-front-door"></a>Azure Web Application-brandvägg i Azures front dörr

Azure Web Application Firewall (WAF) på Azure-frontend tillhandahåller centraliserat skydd för dina webb program. WAF skyddar dina webb tjänster mot vanliga sårbarheter och sårbarheter. Tjänsten håller din tjänst hög tillgänglig för dina användare och hjälper dig att uppfylla kraven för efterlevnad.

WAF på front dörren är en global och centraliserad lösning. Den distribueras på Azures nätverks gräns platser runtom i världen. WAF-aktiverade webb program inspekterar varje inkommande begäran som levererats av front dörren vid nätverks kanten. 

WAF förhindrar skadliga attacker nära angrepps källorna innan de anger ditt virtuella nätverk. Du får globalt skydd i skala utan att offra prestanda. En WAF-princip länkar enkelt till en profil för klient del dörren i din prenumeration. Nya regler kan distribueras inom några minuter, så du kan snabbt svara på att ändra hot mönster.

![Azure-brandvägg för webbaserade program](../media/overview/wafoverview.png)

Azures front dörr introducerar [två nya SKU: er i för hands versionen](https://docs.microsoft.com/azure/frontdoor/standard-premium/overview): främre dörren och den främre dörrens Premium-SKU. WAF är inbyggt integrerat med SKU: n för front dörr Premium med fullständiga funktioner. För standard-SKU: n för front dörren stöds endast [anpassade regler](https://docs.microsoft.com/azure/web-application-firewall/afds/afds-overview#custom-authored-rules) .

## <a name="waf-policy-and-rules"></a>WAF princip och regler

Du kan konfigurera en [WAF-princip](waf-front-door-create-portal.md) och associera principen till en eller flera front dörrs frontend-sidor för skydd. En WAF-princip består av två typer av säkerhets regler:

- anpassade regler som har skapats av kunden.

- hanterade regel uppsättningar som är en samling med Azure-hanterad förkonfigurerad uppsättning regler.

När båda finns bearbetas anpassade regler innan reglerna bearbetas i en hanterad regel uppsättning. En regel består av ett matchnings villkor, en prioritet och en åtgärd. De åtgärds typer som stöds är: Tillåt, blockera, logga och OMDIRIGERA. Du kan skapa en helt anpassad princip som uppfyller dina specifika krav för program skydd genom att kombinera de hanterade och anpassade reglerna.

Regler i en princip bearbetas i en prioritetsordning. Prioritet är ett unikt heltal som definierar ordningen på regler som ska bearbetas. Mindre heltal anger en högre prioritet och dessa regler utvärderas innan regler med ett högre heltals värde. När en regel har matchats tillämpas motsvarande åtgärd som definierats i regeln för begäran. När en sådan matchning bearbetas bearbetas inte regler med lägre prioritet ytterligare.

Ett webb program som levereras av front dörren kan bara ha en WAF-princip kopplad till sig i taget. Du kan dock ha en konfiguration för en front dörr utan några WAF-principer. Om det finns en WAF-princip replikeras den till alla våra gräns platser för att säkerställa konsekventa säkerhets principer över hela världen.

## <a name="waf-modes"></a>WAF-lägen

WAF-principen kan konfigureras att köras i följande två lägen:

- **Identifierings läge:** Vid körning i identifierings läge tar WAF inte med andra åtgärder än övervakare och loggar begäran och dess matchade WAF-regel till WAF-loggar. Du kan aktivera loggning av diagnostik för front dörren. När du använder portalen går du till avsnittet **diagnostik** .

- **Skydds läge:** I förebyggande läge tar WAF den angivna åtgärden om en begäran matchar en regel. Om en matchning hittas utvärderas inga ytterligare regler med lägre prioritet. Alla matchade begär Anden loggas också i WAF-loggarna.

## <a name="waf-actions"></a>WAF-åtgärder

WAF-kunder kan välja att köra från en av åtgärderna när en begäran matchar en regels villkor:

- **Tillåt:**  Begäran passerar genom WAF och vidarebefordras till Server delen. Inga ytterligare regler för lägre prioritet kan blockera den här begäran.
- **Blockera:** Begäran blockeras och WAF skickar ett svar till klienten utan att vidarebefordra begäran till Server delen.
- **Logg:**  Begäran loggas i WAF-loggarna och WAF fortsätter att utvärdera lägre prioritets regler.
- **Omdirigera:** WAF omdirigerar begäran till angiven URI. Den angivna URI: n är en princip nivå inställning. När den har kon figurer ATS skickas alla förfrågningar som matchar **omdirigeringen** till denna URI.

## <a name="waf-rules"></a>WAF-regler

En WAF-princip kan bestå av två typer av säkerhets regler – anpassade regler, som har skapats av kunden och hanterade rulesets, Azure-hanterad uppsättning regler.

### <a name="custom-authored-rules"></a>Anpassade skapade regler

Du kan konfigurera anpassade regler WAF på följande sätt:

- Lista **över tillåtna IP-listor och blockerade listor:** Du kan styra åtkomsten till dina webb program baserat på en lista över klient-IP-adresser eller IP-adressintervall. Både IPv4-och IPv6-adress typer stöds. Den här listan kan konfigureras för att antingen blockera eller tillåta de förfrågningar där käll-IP: en motsvarar en IP-adress i listan.

- **Geografisk baserad åtkomst kontroll:** Du kan styra åtkomsten till dina webb program baserat på den landskod som är associerad med klientens IP-adress.

- **Http-parameter-baserad åtkomst kontroll:** Du kan basera regler på sträng matchningar i parametrarna för HTTP/HTTPS-begäran.  Till exempel frågesträngar, POST-argument, begär ande-URI, begär ande rubrik och brödtext för begäran.

- **Begär metod baserad åtkomst kontroll:** Du baserade regler för HTTP-begäran i begäran. Till exempel GET, tag eller HEAD.

- **Storleks begränsning:** Du kan basera regler på längden på vissa delar av en begäran, till exempel frågesträng, URI eller brödtext i begäran.

- **Hastighets begränsnings regler:** En hastighets kontroll regel är att begränsa onormal hög trafik från alla klient-IP-adresser. Du kan konfigurera ett tröskelvärde för antalet webb förfrågningar som tillåts från en klient-IP under en minuts varaktighet. Den här regeln är distinkt från en lista över tillåtna/blockerade IP-adresser som antingen tillåter alla eller blockerar all begäran från en klient-IP. Hastighets begränsningar kan kombineras med ytterligare matchnings villkor, t. ex. HTTP (S)-parameter matchningar för detaljerad hastighets kontroll.

### <a name="azure-managed-rule-sets"></a>Azure-hanterade regel uppsättningar

Azure-hanterade regel uppsättningar ger ett enkelt sätt att distribuera skydd mot en gemensam uppsättning säkerhetshot. Eftersom sådana rulesets hanteras av Azure uppdateras reglerna vid behov för att skydda mot nya attack-signaturer. Azure-hanterad standard regel uppsättning innehåller regler mot följande hot kategorier:

- Skriptkörning över flera webbplatser
- Java-attacker
- Lokal fil inkludering
- Angrepp för PHP-injektering
- Fjärrkörning av kommando
- Införande av fjärrfil
- Sessions-bindning
- Skydd mot SQL-inmatning
- Protokoll angripare

Versions numret för standard regel uppsättningen ökar när nya angrepps signaturer läggs till i regel uppsättningen.
Standard regel uppsättningen är aktive rad som standard i identifierings läge i dina WAF-principer. Du kan inaktivera eller aktivera enskilda regler inom standard regel uppsättningen för att uppfylla dina program krav. Du kan också ange specifika åtgärder (Tillåt/blockera/OMDIRIGERA/logga) per regel.

Ibland kan du behöva utelämna vissa begär ande attribut från en WAF-utvärdering. Ett vanligt exempel är Active Directory-infogade tokens som används för autentisering. Du kan konfigurera en undantags lista för en hanterad regel, regel grupp eller för hela regel uppsättningen.  

Standard åtgärden är att blockera. Dessutom kan anpassade regler konfigureras i samma WAF-princip om du vill kringgå någon av de förkonfigurerade reglerna i standard regel uppsättningen.

Anpassade regler tillämpas alltid innan regler i standard regel uppsättningen utvärderas. Om en begäran matchar en anpassad regel tillämpas motsvarande regel åtgärd. Begäran är antingen blockerad eller passerad till Server delen. Inga andra anpassade regler eller reglerna i standard regel uppsättningen bearbetas. Du kan också ta bort standard regel uppsättningen från dina WAF-principer.

### <a name="bot-protection-rule-set-preview"></a>Regel uppsättning för bot skydd (för hands version)

Du kan aktivera en skydds regel för hanterad bot för att vidta anpassade åtgärder för förfrågningar från kända robot kategorier. 

Det finns tre robot kategorier som stöds: dåligt, bra och okänd. Robot-signaturer hanteras och uppdateras dynamiskt av WAF-plattformen.

Felaktiga robotar omfattar robotar från skadliga IP-adresser och robotar som har förfalskat sina identiteter. Skadliga IP-adresser skickas från Microsoft Threat Intelligence-flödet och uppdateras varje timme. [Intelligent Security graphs](https://www.microsoft.com/security/operations/intelligence) befogenheter Microsoft Threat intelligence och används av flera tjänster, inklusive Azure Security Center.

Robotar är godkända sökmotorer. Okända kategorier innehåller ytterligare bot-grupper som har identifierats som robotar. Till exempel marknads analys, flödes hämtning och data insamlings agenter. 

Okända robotar klassificeras via publicerade användar agenter utan ytterligare verifiering. Du kan ange anpassade åtgärder för att blockera, tillåta, logga eller omdirigera för olika typer av robotar.

![Regel uppsättning för bot-skydd](../media/afds-overview/botprotect2.png)

> [!IMPORTANT]
> Regel uppsättningen för bot-skydd är för närvarande en offentlig för hands version och tillhandahålls med ett service nivå avtal för för hands versionen. Vissa funktioner kanske inte stöds eller kan ha begränsad funktionalitet.  Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Om bot-skydd är aktiverat loggas inkommande begär Anden som matchar bot-regler i FrontdoorWebApplicationFirewallLog-loggen. Du får åtkomst till WAF-loggar från ett lagrings konto, Event Hub eller Log Analytics.

## <a name="configuration"></a>Konfiguration

Du kan konfigurera och distribuera alla typer av WAF med hjälp av Azure Portal, REST-API: er, Azure Resource Manager mallar och Azure PowerShell.

## <a name="monitoring"></a>Övervakning

Övervakning av WAF i front dörren är integrerat med Azure Monitor för att spåra aviseringar och enkelt övervaka trafik trender.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [brand vägg för webbaserade program på Azure Application Gateway](../ag/ag-overview.md)
