---
title: Vad är Azure Web Application-brandvägg på Azure CDN?
description: Lär dig hur Azure WebApplication-brandväggen i Azure CDN service skyddar dina webb program mot skadliga attacker.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: victorh
ms.openlocfilehash: cd1f21f150094094a6bf856b04a64a02963f35a8
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102521721"
---
# <a name="azure-web-application-firewall-on-azure-content-delivery-network-from-microsoft"></a>Brand vägg för Azure-webbprogram på Azure Content Delivery Network från Microsoft

Azure Web Application Firewall (WAF) på Azure Content Delivery Network (CDN) från Microsoft tillhandahåller centraliserat skydd för ditt webb innehåll. WAF skyddar dina webb tjänster mot vanliga sårbarheter och sårbarheter. Tjänsten håller din tjänst hög tillgänglig för dina användare och hjälper dig att uppfylla kraven för efterlevnad.

> [!IMPORTANT]
> WAF på Azure CDN från Microsoft är för närvarande en offentlig för hands version och tillhandahålls med ett service nivå avtal för för hands versioner. Vissa funktioner kanske inte stöds eller kan ha begränsad funktionalitet.  Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

WAF på Azure CDN är en global och centraliserad lösning. Den distribueras på Azures nätverks gräns platser runtom i världen. WAF stoppar skadliga attacker nära angrepps källorna innan de når ditt ursprung. Du får globalt skydd i skala utan att offra prestanda. 

En WAF-princip länkar enkelt till en CDN-slutpunkt i din prenumeration. Nya regler kan distribueras inom några minuter, så du kan snabbt svara på att ändra hot mönster.

![Azure-brandvägg för webbaserade program](../media/cdn-overview/waf-cdn-overview.png)

## <a name="waf-policy-and-rules"></a>WAF princip och regler

Du kan konfigurera en WAF-princip och associera principen till en eller flera CDN-slutpunkter för skydd. En WAF-princip består av två typer av säkerhets regler:

- anpassade regler som du kan skapa.

- hanterade regel uppsättningar som är en samling med Azure-hanterade förkonfigurerade regler.

När båda finns bearbetas anpassade regler innan reglerna bearbetas i en hanterad regel uppsättning. En regel består av ett matchnings villkor, en prioritet och en åtgärd. De åtgärds typer som stöds är: *Tillåt*, *blockera*, *Logga* och *omdirigera*. Du kan skapa en helt anpassad princip som uppfyller dina specifika krav för program skydd genom att kombinera de hanterade och anpassade reglerna.

Regler i en princip bearbetas i en prioritetsordning. Prioritet är ett unikt nummer som definierar ordningen på regler som ska bearbetas. Lägre siffror är högre prioritet och dessa regler utvärderas innan regler med ett större värde. När en regel har matchats tillämpas motsvarande åtgärd som definierats i regeln för begäran. När en sådan matchning bearbetas bearbetas inte regler med lägre prioritet ytterligare.

Ett webb program som finns på Azure CDN kan bara ha en associerad WAF-princip i taget. Du kan dock ha en CDN-slutpunkt utan några WAF-principer som är kopplade till den. Om det finns en WAF-princip replikeras den till alla våra gräns platser för att säkerställa konsekventa säkerhets principer över hela världen.

## <a name="waf-modes"></a>WAF-lägen

WAF-principen kan konfigureras att köras i följande två lägen:

- *Identifierings läge*: vid körning i identifierings läge tar WAF inte med andra åtgärder än övervakare och loggar begäran och dess MATCHAde WAF-regel till WAF-loggar. Du kan aktivera Logging Diagnostics för CDN. När du använder portalen går du till avsnittet **diagnostik** .

- *Skydds läge*: i skydds läge tar WAF den angivna åtgärden om en begäran matchar en regel. Om en matchning hittas utvärderas inga ytterligare regler med lägre prioritet. Alla matchade begär Anden loggas också i WAF-loggarna.

## <a name="waf-actions"></a>WAF-åtgärder

Du kan välja någon av följande åtgärder när en begäran matchar en regels villkor:

- *Tillåt*: begäran passerar genom WAF och vidarebefordras till Server delen. Inga ytterligare regler för lägre prioritet kan blockera den här begäran.
- *Blockera*: begäran är blockerad och WAF skickar ett svar till klienten utan att vidarebefordra begäran till Server delen.
- *Log*: begäran loggas i WAF-loggarna och WAF fortsätter att utvärdera lägre prioritets regler.
- *Omdirigera*: WAF omdirigerar begäran till angiven URI. Den angivna URI: n är en princip nivå inställning. När den har kon figurer ATS skickas alla begär Anden som matchar *omdirigeringen* till denna URI.

## <a name="waf-rules"></a>WAF-regler

En WAF-princip kan bestå av två typer av säkerhets regler:

- *anpassade regler*: regler som du skapar själv 
- *hanterade regel uppsättningar*: Azure Managed förkonfigurerad uppsättning regler

### <a name="custom-rules"></a>Anpassade regler

Anpassade regler kan ha matchnings regler och kontroll regler för hastighet.

Du kan konfigurera följande anpassade matchnings regler:

- Lista över *tillåtna och blockerade IP-* adresser: du kan styra åtkomsten till dina webb program baserat på en lista över klient-IP-adresser eller IP-adressintervall. Både IPv4-och IPv6-adress typer stöds. Den här listan kan konfigureras för att antingen blockera eller tillåta de förfrågningar där käll-IP: en motsvarar en IP-adress i listan.

- *Geografisk åtkomst kontroll*: du kan styra åtkomsten till dina webb program baserat på den landskod som är associerad med KLIENTens IP-adress.

- *Http-parameter-baserad åtkomst kontroll*: du kan basera regler på sträng matchningar i http/https-begäran parametrar.  Till exempel frågesträngar, POST-argument, begär ande-URI, begär ande rubrik och brödtext för begäran.

- *Metod-baserad åtkomst kontroll*: du baserar regler på http request-metoden för begäran. Till exempel GET, tag eller HEAD.

- *Storleks begränsning*: du kan basera regler på längden på vissa delar av en begäran, till exempel frågesträng, URI eller brödtext i begäran.

En hastighets kontroll regel begränsar onormalt hög trafik från alla klient-IP-adresser.

- *Hastighets begränsnings regler*: du kan konfigurera ett tröskelvärde för antalet webb förfrågningar som tillåts från en KLIENTs IP-adress under en varaktighet på en minut. Den här regeln skiljer sig från en anpassad regel för Tillåt/blockera en IP-lista som antingen tillåter alla eller blockerar all begäran från en klients IP-adress. Hastighets begränsningar kan kombineras med ytterligare matchnings villkor, t. ex. HTTP (S)-parameter matchningar för detaljerad hastighets kontroll.

### <a name="azure-managed-rule-sets"></a>Azure-hanterade regel uppsättningar

Azure-hanterade regel uppsättningar ger ett enkelt sätt att distribuera skydd mot en gemensam uppsättning säkerhetshot. Eftersom dessa rulesets hanteras av Azure uppdateras reglerna vid behov för att skydda mot nya attack-signaturer. Den Azure-hanterade standard regel uppsättningen inkluderar regler mot följande hot kategorier:

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
Standard regel uppsättningen är aktive rad som standard i *identifierings* läge i dina WAF-principer. Du kan inaktivera eller aktivera enskilda regler inom standard regel uppsättningen för att uppfylla dina program krav. Du kan också ange specifika åtgärder (Tillåt/blockera/OMDIRIGERA/logga) per regel. Standard åtgärden för den hanterade standard regel uppsättningen är *blockera*.

Anpassade regler tillämpas alltid innan regler i standard regel uppsättningen utvärderas. Om en begäran matchar en anpassad regel tillämpas motsvarande regel åtgärd. Begäran är antingen blockerad eller passerad till Server delen. Inga andra anpassade regler eller reglerna i standard regel uppsättningen bearbetas. Du kan också ta bort standard regel uppsättningen från dina WAF-principer.

## <a name="configuration"></a>Konfiguration

Du kan konfigurera och distribuera alla typer av WAF med hjälp av Azure Portal, REST-API: er, Azure Resource Manager mallar och Azure PowerShell.

## <a name="monitoring"></a>Övervakning

Övervakning av WAF med CDN är integrerat med Azure Monitor för att spåra aviseringar och enkelt övervaka trafik trender.

## <a name="next-steps"></a>Nästa steg

- [Självstudie: skapa en WAF-princip med Azure CDN med hjälp av Azure Portal](waf-cdn-create-portal.md)
