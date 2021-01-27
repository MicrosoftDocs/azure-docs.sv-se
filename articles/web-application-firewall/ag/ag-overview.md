---
title: Vad är Azure Web Application-brandvägg på Azure Application Gateway?
titleSuffix: Azure Web Application Firewall
description: Den här artikeln innehåller en översikt över brand vägg för webbaserade program (WAF) på Application Gateway
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 12/04/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 2de8a47b841a4a5c82aaeb92419ec3d9714268ea
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879435"
---
# <a name="what-is-azure-web-application-firewall-on-azure-application-gateway"></a>Vad är Azure Web Application-brandvägg på Azure Application Gateway?

Azure Web Application Firewall (WAF) på Azure Application Gateway tillhandahåller centraliserat skydd av dina webb program mot vanliga sårbarheter och sårbarheter. Webb program är alltmer riktade mot skadliga attacker som utnyttjar ofta kända sårbarheter. SQL-inmatning och Cross-Site-skript är bland de vanligaste angrepp.

WAF på Application Gateway baseras på [kärn regel uppsättningen (datoriserat system)](https://owasp.org/www-project-modsecurity-core-rule-set/) 3,1, 3,0 eller 2.2.9 från det öppna webb programmet säkerhets projekt (OWASP). WAF uppdateras automatiskt för att ta med skydd mot nya sårbarheter, utan ytterligare konfiguration som behövs. 

Alla WAF-funktioner i listan nedan finns i en WAF-princip. Du kan skapa flera principer och de kan associeras med en Application Gateway, till enskilda lyssnare eller till sökvägar baserade routningsregler på en Application Gateway. På så sätt kan du ha separata principer för varje plats bakom Application Gateway vid behov. Mer information om WAF-principer finns i [skapa en WAF-princip](create-waf-policy-ag.md).

![Application Gateway WAF-diagram](../media/ag-overview/waf1.png)

Application Gateway fungerar som en program leverans kontroll (ADC). Den erbjuder Transport Layer Security (TLS), tidigare känd som Secure Sockets Layer (SSL), avslutning, cookie-baserad sessionsgräns, resursallokering (Round Robin load distribution), innehålls baserad routning, möjlighet att vara värd för flera webbplatser och säkerhets förbättringar.

Application Gateway säkerhets förbättringar inkluderar TLS-princip hantering och stöd från slut punkt till slut punkt. Program säkerhet förstärks av WAF-integrering i Application Gateway. Kombinationen skyddar dina webb program mot vanliga sårbarheter. Och tillhandahåller en enkel-till-konfigurera central plats för hantering.

## <a name="benefits"></a>Fördelar

I det här avsnittet beskrivs de grundläggande fördelar som WAF i Application Gateway tillhandahåller.

### <a name="protection"></a>Skydd

* Skydda dina webb program från webb sårbarheter och attacker utan att ändra till backend-koden.

* Skydda flera webb program samtidigt. En instans av Application Gateway kan vara värd för upp till 40 webbplatser som skyddas av en brand vägg för webbaserade program.

* Skapa anpassade WAF-principer för olika platser bakom samma WAF 

* Skydda dina webb program från skadliga robotar med IP-rykte ruleset (för hands version)

### <a name="monitoring"></a>Övervakning

* Övervaka attacker mot dina webb program genom att använda en WAF-logg i real tid. Loggen är integrerad med [Azure Monitor](../../azure-monitor/overview.md) för att spåra WAF aviseringar och enkelt övervaka trender.

* Application Gateway WAF är integrerat med Azure Security Center. Security Center ger en central vy över säkerhets läget för alla dina Azure-resurser.

### <a name="customization"></a>Anpassning

* Anpassa WAF-regler och regel grupper så att de passar dina program krav och eliminera falska positiva identifieringar.

* Koppla en WAF-princip för varje plats bakom din WAF för att tillåta platsspecifika konfiguration

* Skapa anpassade regler som passar ditt programs behov

## <a name="features"></a>Funktioner

- Skydd mot SQL-injektering.
- Skript skydd över hela platsen.
- Skydd mot andra vanliga webb attacker, till exempel kommando inmatning, HTTP-begäran dold, delning av HTTP-svar och inkludering av fjärrfiler.
- Skydd mot HTTP-protokollfel.
- Skydd mot avvikelser i HTTP-protokollet, t. ex. saknade värd användar agenter och ta emot huvuden.
- Skydd mot robotar och skannrar.
- Identifiering av vanliga program konfigurationer (till exempel Apache och IIS).
- Konfigurerbara storleks gränser för begäran med nedre och övre gräns.
- Med undantags listor kan du utelämna vissa begär ande attribut från en WAF-utvärdering. Ett vanligt exempel är Active Directory-infogade tokens som används för autentiserings-eller lösen ords fält.
- Skapa anpassade regler som passar de olika behoven i dina program.
- Geo-filter-trafik för att tillåta eller blockera vissa länder/regioner från att få åtkomst till dina program. (förhandsversion)
- Skydda dina program från robotar med ruleset för bot. (förhandsversion)
- Granska JSON och XML i begär ande texten

## <a name="waf-policy-and-rules"></a>WAF princip och regler

Om du vill aktivera en brand vägg för webbaserade program på Application Gateway måste du skapa en WAF-princip. Den här principen är den plats där alla hanterade regler, anpassade regler, undantag och andra anpassningar, till exempel fil överförings gränsen finns.

Du kan konfigurera en WAF-princip och associera principen till en eller flera programgatewayer för skydd. En WAF-princip består av två typer av säkerhets regler:

- Anpassade regler som du skapar

- Hanterade regel uppsättningar som är en samling med Azure-hanterad förkonfigurerad uppsättning regler

När båda finns bearbetas anpassade regler innan reglerna bearbetas i en hanterad regel uppsättning. En regel består av ett matchnings villkor, en prioritet och en åtgärd. De åtgärds typer som stöds är: Tillåt, blockera och logga. Du kan skapa en helt anpassad princip som uppfyller dina specifika krav för program skydd genom att kombinera de hanterade och anpassade reglerna.

Regler i en princip bearbetas i en prioritetsordning. Prioritet är ett unikt heltal som definierar ordningen på regler som ska bearbetas. Mindre heltal anger en högre prioritet och dessa regler utvärderas innan regler med ett högre heltals värde. När en regel har matchats tillämpas motsvarande åtgärd som definierats i regeln för begäran. När en sådan matchning bearbetas bearbetas inte regler med lägre prioritet ytterligare.

Ett webb program som levereras av Application Gateway kan ha en WAF-princip kopplad till den på global nivå, på en nivå per webbplats eller per URI-nivå.

### <a name="core-rule-sets"></a>Core Rule Sets

Application Gateway stöder tre regel uppsättningar: datoriserade boknings system 3,1, BOKNINGs-3,0 och datoriserade boknings 2.2.9. Dessa regler skyddar dina webb program mot skadlig aktivitet.

Mer information finns i [regel grupper och regler för webb programs brand vägg](application-gateway-crs-rulegroups-rules.md).

### <a name="custom-rules"></a>Anpassade regler

Application Gateway stöder också anpassade regler. Med anpassade regler kan du skapa egna regler som utvärderas för varje begäran som passerar genom WAF. Dessa regler innehåller högre prioritet än resten av reglerna i de hanterade regel uppsättningarna. Om en uppsättning villkor är uppfyllda vidtas en åtgärd för att tillåta eller blockera. 

Operatorn för att passa är nu tillgänglig i offentlig för hands version för anpassade regler. Mer information finns i de [anpassade reglerna](custom-waf-rules-overview.md#geomatch-custom-rules-preview) för att matcha.

> [!NOTE]
> Operatorn för att uppfylla anpassade regler är för närvarande en offentlig för hands version och tillhandahålls med ett service nivå avtal för för hands versioner. Vissa funktioner kanske inte stöds eller kan ha begränsad funktionalitet. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Mer information om anpassade regler finns i [anpassade regler för Application Gateway.](custom-waf-rules-overview.md)

### <a name="bot-mitigation-preview"></a>Bot-minskning (för hands version)

En regel uppsättning för hanterad bot-skydd kan aktive ras för WAF för att blockera eller logga förfrågningar från kända skadliga IP-adresser, tillsammans med den hanterade ruleset. IP-adresserna hämtas från Microsoft Threat Intelligence-flödet. Microsoft Threat Intelligence bygger på Intelligent Security Graph och används av flera tjänster som Azure Security Center.

> [!NOTE]
> Regel uppsättningen för bot-skydd är för närvarande en offentlig för hands version och tillhandahålls med ett service nivå avtal för för hands versionen. Vissa funktioner kanske inte stöds eller kan ha begränsad funktionalitet. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Om bot-skydd är aktiverat loggas inkommande begär Anden som matchar skadlig robots klient-IP-adresser i brand Väggs loggen. mer information finns nedan. Du får åtkomst till WAF-loggar från lagrings kontot, händelsehubben eller Log Analytics. 

### <a name="waf-modes"></a>WAF-lägen

Application Gateway WAF kan konfigureras att köras i följande två lägen:

* **Identifierings läge**: övervakar och loggar alla hot aviseringar. Du aktiverar Logging Diagnostics för Application Gateway i avsnittet **diagnostik** . Du måste också kontrol lera att WAF-loggen är markerad och aktive rad. Brand väggen för webbaserade program blockerar inte inkommande förfrågningar när den körs i identifierings läge.
* **Skydds läge**: blockerar intrång och attacker som reglerna identifierar. Angriparen får ett undantag för "403 obehörig åtkomst" och anslutningen stängs. I skydds läget registreras sådana attacker i WAF-loggarna.

> [!NOTE]
> Vi rekommenderar att du kör en nyligen distribuerad WAF i identifierings läge under en kort tids period i en produktions miljö. Detta ger möjlighet att hämta [brand Väggs loggar](../../application-gateway/application-gateway-diagnostics.md#firewall-log) och uppdatera eventuella undantag eller [anpassade regler](./custom-waf-rules-overview.md) innan du övergår till förebyggande läge. Detta kan hjälpa till att minska förekomsten av oväntad blockerad trafik.

### <a name="anomaly-scoring-mode"></a>Avvikelse bedömnings läge

OWASP har två lägen för att bestämma om du vill blockera trafik: traditionellt läge och avvikande bedömnings läge.

I traditionellt läge betraktas trafik som matchar vilken regel som helst oberoende av andra regel matchningar. Det här läget är enkelt att förstå. Men avsaknad av information om hur många regler som matchar en speciell begäran är en begränsning. Därför introducerades avvikelser bedömnings läget. Det är standardinställningen för OWASP 3. *x*.

I avvikande bedömnings läge blockeras inte trafik som matchar någon regel direkt när brand väggen är i förebyggande läge. Reglerna har en viss allvarlighets grad: *kritisk*, *fel*, *Varning* eller *meddelande*. Allvarlighets graden påverkar ett numeriskt värde för begäran, vilket kallas för avvikelse poängen. En *varnings* regel matchar till exempel 3 till poängen. En *kritisk* regel matchar 5.

|Allvarlighetsgrad  |Värde  |
|---------|---------|
|Kritiskt     |5|
|Fel        |4|
|Varning      |3|
|anslagstavlan,       |2|

Det finns ett tröskelvärde på 5 för avvikelse poängen för att blockera trafik. En enda *viktig* regel matchning är därför tillräckligt för att Application Gateway WAF ska blockera en begäran, även i förebyggande läge. Men en *varnings* regel matchning ökar bara avvikelse poängen med 3, vilket inte är tillräckligt för att blockera trafiken.

> [!NOTE]
> Meddelandet som loggas när en WAF-regel matchar trafiken inkluderar åtgärd svärdet "blockerad". Men trafiken blockeras faktiskt bara för en avvikande poäng på 5 eller högre. Mer information finns i [Felsöka brand vägg för webbaserade program (WAF) för Azure Application Gateway](web-application-firewall-troubleshoot.md#understanding-waf-logs). 

### <a name="waf-monitoring"></a>WAF-övervakning

Det är viktigt att övervaka hälsotillståndet för Application Gateway. Övervakning av hälso tillståndet för dina WAF och de program som den skyddar stöds av integrering med Azure Security Center, Azure Monitor och Azure Monitor loggar.

![Diagram över Application Gateway WAF-diagnostik](../media/ag-overview/diagnostics.png)

#### <a name="azure-monitor"></a>Azure Monitor

Application Gateway loggar är integrerade i [Azure Monitor](../../azure-monitor/overview.md). På så sätt kan du spåra diagnostikinformation, inklusive WAF-aviseringar och loggar. Du kan komma åt den här funktionen på fliken **diagnostik** i Application Gateway resursen i portalen eller direkt via Azure Monitor. Mer information om hur du aktiverar loggar finns i [Application Gateway Diagnostics](../../application-gateway/application-gateway-diagnostics.md).

#### <a name="azure-security-center"></a>Azure Security Center

[Security Center](../../security-center/security-center-introduction.md) hjälper dig att förhindra, identifiera och svara på hot. Det ger ökad insyn i och kontroll över säkerheten för dina Azure-resurser. Application Gateway är [integrerat med Security Center](../../security-center/security-center-partner-integration.md#integrated-azure-security-solutions). Security Center söker igenom din miljö för att identifiera oskyddade webb program. Det kan rekommendera Application Gateway WAF för att skydda dessa sårbara resurser. Du skapar brand väggarna direkt från Security Center. Dessa WAF-instanser är integrerade med Security Center. De skickar aviseringar och hälso information till Security Center för rapportering.

![Fönstret Security Center översikt](../media/ag-overview/figure1.png)

#### <a name="azure-sentinel"></a>Azure Sentinel

Microsoft Azure Sentinel är en skalbar, molnbaserad, molnbaserad, SIEM (Security information Event Management) och SOAR-lösning (Security Orchestration autoresponse). Azure Sentinel ger intelligent säkerhets analys och hot information i hela företaget, vilket ger en enda lösning för aviserings identifiering, Hot synlighet, proaktiv jakt och hot svar.

Med den inbyggda arbets boken för Azure WAF Firewall-händelser kan du få en översikt över säkerhets händelserna på din WAF. Detta inkluderar händelser, matchade och blockerade regler och allt annat som loggas i brand Väggs loggarna. Läs mer om loggning nedan. 


![Arbets bok för Azure WAF Firewall-händelser](../media/ag-overview/sentinel.png)


#### <a name="azure-monitor-workbook-for-waf"></a>Azure Monitor arbets bok för WAF

Den här arbets boken möjliggör anpassad visualisering av säkerhets relevanta WAF-händelser över flera filter bara paneler. Det fungerar med alla WAF-typer, inklusive Application Gateway, front dörren och CDN, och kan filtreras baserat på WAF-typ eller en speciell WAF-instans. Importera via ARM-mallen eller Galleri mal len. Information om hur du distribuerar den här arbets boken finns i [WAF-arbetsbok](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20WAF/Azure%20Monitor%20Workbook).

#### <a name="logging"></a>Loggning

Application Gateway WAF innehåller detaljerad rapportering om varje hot som identifieras. Loggning är integrerat med Azure-diagnostik loggar. Aviseringar registreras i JSON-format. Dessa loggar kan integreras med [Azure Monitor loggar](../../azure-monitor/insights/azure-networking-analytics.md).

![Application Gateway diagnostikloggar loggar Windows](../media/ag-overview/waf2.png)

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupId}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{appGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
    {
      "instanceId": "ApplicationGatewayRole_IN_0",
      "clientIp": "52.161.109.145",
      "clientPort": "0",
      "requestUri": "/",
      "ruleSetType": "OWASP",
      "ruleSetVersion": "3.0",
      "ruleId": "920350",
      "ruleGroup": "920-PROTOCOL-ENFORCEMENT",
      "message": "Host header is a numeric IP address",
      "action": "Matched",
      "site": "Global",
      "details": {
        "message": "Warning. Pattern match \"^[\\\\d.:]+$\" at REQUEST_HEADERS:Host ....",
        "data": "127.0.0.1",
        "file": "rules/REQUEST-920-PROTOCOL-ENFORCEMENT.conf",
        "line": "791"
      },
      "hostname": "127.0.0.1",
      "transactionId": "16861477007022634343"
      "policyId": "/subscriptions/1496a758-b2ff-43ef-b738-8e9eb5161a86/resourceGroups/drewRG/providers/Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/globalWafPolicy",
      "policyScope": "Global",
      "policyScopeName": " Global "
    }
  }
} 

```

## <a name="application-gateway-waf-sku-pricing"></a>Priser för WAF SKU för programgatewayen

Pris modellerna är olika för WAF_v1 och WAF_v2 SKU: er. Mer information finns på sidan med [Application Gateway prissättning](https://azure.microsoft.com/pricing/details/application-gateway/) . 

## <a name="whats-new"></a>Nyheter

Information om vad som är nytt med Azure Web Application-brandväggen finns i [Azure updates](https://azure.microsoft.com/updates/?category=networking&query=Web%20Application%20Firewall).

## <a name="next-steps"></a>Nästa steg

- Läs mer om [WAF-hanterade regler](application-gateway-crs-rulegroups-rules.md)
- Läs mer om [anpassade regler](custom-waf-rules-overview.md)
- Lär dig mer om [brand vägg för webbaserade program på Azures front dörr](../afds/afds-overview.md)
