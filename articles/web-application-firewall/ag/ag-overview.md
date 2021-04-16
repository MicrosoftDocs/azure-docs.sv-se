---
title: Vad är Azure Web Application Firewall på Azure Application Gateway?
titleSuffix: Azure Web Application Firewall
description: Den här artikeln innehåller en översikt över Web Application Firewall (WAF) på Application Gateway
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 12/04/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: babd628280ebaee408d44dfacfaf6a5e14f57019
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481831"
---
# <a name="what-is-azure-web-application-firewall-on-azure-application-gateway"></a>Vad är Azure Web Application Firewall på Azure Application Gateway?

Azure Web Application Firewall (WAF) på Azure Application Gateway ett centraliserat skydd för dina webbprogram mot vanliga kryphål och sårbarheter. Webbprogram riktas i allt större utsträckning mot skadliga attacker som utnyttjar kända sårbarheter. SQL- injection och skript över flera webbplatser är bland de vanligaste attackerna.

WAF på Application Gateway baseras på [Core Rule Set (CRS)](https://owasp.org/www-project-modsecurity-core-rule-set/) 3.1, 3.0 eller 2.2.9 från Open Web Application Security Project (OWASP). BRANDVÄGGen för brandväggen för brandväggen uppdateras automatiskt för att inkludera skydd mot nya säkerhetsrisker, utan att ytterligare konfiguration krävs. 

Alla WAF-funktioner som anges nedan finns i en WAF-princip. Du kan skapa flera principer och de kan associeras med en Application Gateway, enskilda lyssnare eller till sökvägsbaserade routningsregler på en Application Gateway. På så sätt kan du ha separata principer för varje webbplats bakom din Application Gateway om det behövs. Mer information om WAF-principer finns i [Skapa en WAF-princip.](create-waf-policy-ag.md)

![Application Gateway WAF-diagram](../media/ag-overview/waf1.png)

Application Gateway fungerar som en ADC (Application Delivery Controller). Den erbjuder Transport Layer Security (TLS), tidigare kallat Secure Sockets Layer (SSL), avslutning, cookiebaserad sessionstillhörighet, resursallokeringsbelastningsdistribution, innehållsbaserad routning, möjlighet att vara värd för flera webbplatser och säkerhetsförbättringar.

Application Gateway säkerhetsförbättringarna omfattar TLS-principhantering och TLS-stöd från slutet till slut. Programsäkerheten förstärks av WAF-integrering i Application Gateway. Kombinationen skyddar dina webbprogram mot vanliga sårbarheter. Och det ger en lättkonfigurerad central plats att hantera.

## <a name="benefits"></a>Fördelar

I det här avsnittet beskrivs de viktigaste fördelarna med WAF på Application Gateway tillhandahåller.

### <a name="protection"></a>Skydd

* Skydda dina webbprogram mot webbsårbarheter och attacker utan ändringar i backend-koden.

* Skydda flera webbprogram på samma gång. En instans av Application Gateway kan vara värd för upp till 40 webbplatser som skyddas av en brandvägg för webbaserade program.

* Skapa anpassade WAF-principer för olika webbplatser bakom samma WAF 

* Skydda dina webbprogram från skadliga robotar med IP Reputation-regeluppsättningen (förhandsversion)

### <a name="monitoring"></a>Övervakning

* Övervaka attacker mot dina webbprogram med hjälp av en WAF-logg i realtid. Loggen är integrerad med Azure Monitor [för att](../../azure-monitor/overview.md) spåra WAF-aviseringar och enkelt övervaka trender.

* Den Application Gateway WAF är integrerad med Azure Security Center. Security Center en central vy över säkerhetstillståndet för alla dina Azure-resurser.

### <a name="customization"></a>Anpassning

* Anpassa WAF-regler och regelgrupper så att de passar dina programkrav och eliminera falska positiva resultat.

* Associera en WAF-princip för varje plats bakom din WAF för att tillåta platsspecifik konfiguration

* Skapa anpassade regler som passar behoven i ditt program

## <a name="features"></a>Funktioner

- SQL- injection-skydd.
- Skriptskydd mellan webbplatser.
- Skydd mot andra vanliga webbattacker, till exempel kommandoinjektion, HTTP-begärandeattack, delning av HTTP-svar och fjärrfilsinkludering.
- Skydd mot http-protokollöverträdelser.
- Skydd mot HTTP-protokollavvikelser, till exempel att värdens användaragent saknas och att huvudena accepteras.
- Skydd mot crawlers och skannrar.
- Identifiering av vanliga programfelkonfigurationer (till exempel Apache och IIS).
- Konfigurerbara storleksgränser för förfrågningar med lägre och övre gränser.
- Med undantagslistor kan du utelämna vissa begärandeattribut från en WAF-utvärdering. Ett vanligt exempel är Active Directory-infogade token som används för autentiserings- eller lösenordsfält.
- Skapa anpassade regler som passar de specifika behoven i dina program.
- Geofiltreringstrafik för att tillåta eller blockera vissa länder/regioner från att få åtkomst till dina program. (förhandsversion)
- Skydda dina program från robotar med regeluppsättningen för robotminskning. (förhandsversion)
- Granska JSON och XML i begärandetexten

## <a name="waf-policy-and-rules"></a>WAF-princip och regler

Om du vill aktivera Web Application Firewall på Application Gateway måste du skapa en WAF-princip. Den här principen är den plats där alla hanterade regler, anpassade regler, undantag och andra anpassningar, till exempel filuppladdningsgräns, finns.

Du kan konfigurera en WAF-princip och associera principen med en eller flera programgatewayer för skydd. En WAF-princip består av två typer av säkerhetsregler:

- Anpassade regler som du skapar

- Hanterade regeluppsättningar som är en samling azure-hanterade förkonfigurerade regeluppsättningar

När båda finns bearbetas anpassade regler innan de bearbetas i en hanterad regeluppsättning. En regel består av ett matchningsvillkor, en prioritet och en åtgärd. Åtgärdstyper som stöds är: ALLOW, BLOCK och LOG. Du kan skapa en helt anpassad princip som uppfyller dina specifika programskyddskrav genom att kombinera hanterade och anpassade regler.

Regler i en princip bearbetas i en prioritetsordning. Prioritet är ett unikt heltal som definierar ordningen på de regler som ska bearbetas. Mindre heltalsvärde anger en högre prioritet och dessa regler utvärderas före regler med ett högre heltalsvärde. När en regel matchas tillämpas motsvarande åtgärd som definierades i regeln på begäran. När en sådan matchning har bearbetats bearbetas inte regler med lägre prioritet ytterligare.

Ett webbprogram som levereras av Application Gateway kan ha en WAF-princip kopplad till den på global nivå, på nivån per webbplats eller på en per URI-nivå.

### <a name="core-rule-sets"></a>Core Rule Sets

Application Gateway stöder tre regeluppsättningar: CRS 3.1, CRS 3.0 och CRS 2.2.9. De här reglerna skyddar dina webbprogram mot skadlig aktivitet.

Mer information finns i [CRS-regelgrupper och -regler för brandväggen för webbaserade program.](application-gateway-crs-rulegroups-rules.md)

### <a name="custom-rules"></a>Anpassade regler

Application Gateway har även stöd för anpassade regler. Med anpassade regler kan du skapa egna regler som utvärderas för varje begäran som passerar genom WAF. Dessa regler har högre prioritet än resten av reglerna i de hanterade regeluppsättningarna. Om en uppsättning villkor uppfylls vidtas en åtgärd för att tillåta eller blockera. 

Geomatch-operatorn är nu tillgänglig i offentlig förhandsversion för anpassade regler. Mer information [finns i anpassade regler för geomatchning.](custom-waf-rules-overview.md#geomatch-custom-rules-preview)

> [!NOTE]
> Geomatch-operatorn för anpassade regler är för närvarande i offentlig förhandsversion och tillhandahålls med ett servicenivåavtal för förhandsversionen. Vissa funktioner kanske inte stöds eller kan ha begränsad funktionalitet. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Mer information om anpassade regler finns i [Anpassade regler för Application Gateway.](custom-waf-rules-overview.md)

### <a name="bot-mitigation-preview"></a>Bot Mitigation (förhandsversion)

En hanterad regeluppsättning för robotskydd kan aktiveras så att din WAF blockerar eller loggar begäranden från kända skadliga IP-adresser, tillsammans med den hanterade regeluppsättningen. IP-adresserna hämtas från Microsoft Threat Intelligence-flödet. Microsoft Threat Intelligence bygger på Intelligent Security Graph och används av flera tjänster som Azure Security Center.

> [!NOTE]
> Regeluppsättningen för robotskydd är för närvarande i offentlig förhandsversion och tillhandahålls med ett servicenivåavtal för förhandsversionen. Vissa funktioner kanske inte stöds eller kan ha begränsad funktionalitet. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Om Bot Protection är aktiverat loggas inkommande begäranden som matchar klient-IP-adresser för den skadliga roboten i brandväggsloggen. Mer information finns nedan. Du kan komma åt WAF-loggar från lagringskonto, händelsehubb eller logganalys. 

### <a name="waf-modes"></a>WAF-lägen

Den Application Gateway WAF kan konfigureras för att köras i följande två lägen:

* **Identifieringsläge:** Övervakar och loggar alla hotaviseringar. Du aktiverar loggningsdiagnostik för Application Gateway i **avsnittet** Diagnostik. Du måste också se till att WAF-loggen är markerad och aktiverad. Brandväggen för webbaserade program blockerar inte inkommande begäranden när den används i identifieringsläge.
* **Skyddsläge:** Blockerar intrång och attacker som identifieras av reglerna. Angriparen får undantaget "403 obehörig åtkomst" och anslutningen stängs. Skyddsläge registrerar sådana attacker i WAF-loggarna.

> [!NOTE]
> Vi rekommenderar att du kör en nyligen distribuerad WAF i identifieringsläge under en kort tidsperiod i en produktionsmiljö. Detta ger möjlighet att hämta [brandväggsloggar](../../application-gateway/application-gateway-diagnostics.md#firewall-log) och uppdatera eventuella undantag [eller anpassade regler](./custom-waf-rules-overview.md) före övergången till skyddsläge. Detta kan minska förekomsten av oväntad blockerad trafik.

### <a name="anomaly-scoring-mode"></a>Läget för avvikelsebedömning

OWASP har två lägen för att bestämma om trafik ska blockeras: Traditionellt läge och avvikelsebedömningsläge.

I traditionellt läge anses trafik som matchar en regel oberoende av andra regelmatchning. Det här läget är enkelt att förstå. Men bristen på information om hur många regler som matchar en specifik begäran är en begränsning. Därför introducerades läget för avvikelsebedömning. Det är standardinställningen för OWASP 3. *x*.

I läget för avvikelsebedömning blockeras inte trafik som matchar någon regel omedelbart när brandväggen är i skyddsläge. Regler har en viss allvarlighetsgrad: *Kritisk,* *Fel,* *Varning* eller *Observera*. Allvarlighetsgraden påverkar ett numeriskt värde för begäran, vilket kallas avvikelsepoäng. Till exempel bidrar *en varningsregelmatchning* med 3 till poängen. En *kritisk* regelmatchning bidrar med 5.

|Allvarlighetsgrad  |Värde  |
|---------|---------|
|Kritiskt     |5|
|Fel        |4|
|Varning      |3|
|anslagstavlan,       |2|

Det finns ett tröskelvärde på 5 för att avvikelsepoängen ska blockera trafik. Därför räcker det med *en enda* kritisk regelmatchning för att Application Gateway WAF ska blockera en begäran, även i skyddsläge. Men  en varningsregelmatchning ökar bara avvikelsepoängen med 3, vilket inte räcker för att blockera trafiken.

> [!NOTE]
> Meddelandet som loggas när en WAF-regel matchar trafik innehåller åtgärdsvärdet "Blockerad". Men trafiken blockeras i själva verket bara för en avvikelsepoäng på 5 eller högre. Mer information finns i [Felsöka Web Application Firewall (WAF) för Azure Application Gateway](web-application-firewall-troubleshoot.md#understanding-waf-logs). 

### <a name="waf-monitoring"></a>WAF-övervakning

Det är viktigt att övervaka hälsotillståndet för Application Gateway. Övervakning av hälsotillståndet för din WAF och de program som den skyddar stöds av integrering med Azure Security Center, Azure Monitor och Azure Monitor loggar.

![Diagram över Application Gateway WAF-diagnostik](../media/ag-overview/diagnostics.png)

#### <a name="azure-monitor"></a>Azure Monitor

Application Gateway-loggar är integrerade med [Azure Monitor](../../azure-monitor/overview.md). På så sätt kan du spåra diagnostikinformation, inklusive WAF-aviseringar och loggar. Du kan komma åt den här funktionen **på fliken** Diagnostik i Application Gateway resurs i portalen eller direkt via Azure Monitor. Mer information om hur du aktiverar loggar finns i [Application Gateway diagnostik](../../application-gateway/application-gateway-diagnostics.md).

#### <a name="azure-security-center"></a>Azure Security Center

[Security Center](../../security-center/security-center-introduction.md) hjälper dig att förhindra, identifiera och svara på hot. Det ger bättre insyn i och kontroll över säkerheten för dina Azure-resurser. Application Gateway är [integrerad med Security Center](../../security-center/security-center-partner-integration.md#integrated-azure-security-solutions). Security Center genomsöker din miljö för att identifiera oskyddade webbprogram. Det kan rekommendera Application Gateway WAF för att skydda dessa sårbara resurser. Du skapar brandväggarna direkt från Security Center. Dessa WAF-instanser är integrerade med Security Center. De skickar aviseringar och hälsoinformation till Security Center för rapportering.

![Security Center översiktsfönstret](../media/ag-overview/figure1.png)

#### <a name="azure-sentinel"></a>Azure Sentinel

Microsoft Azure Sentinel är en skalbar, molnbaserad händelsehanteringslösning för säkerhetsinformation (SIEM) och soAR-lösning (Security Orchestration Automated Response). Azure Sentinel intelligent säkerhetsanalys och hotinformation i hela företaget, vilket ger en enda lösning för aviseringsidentifiering, hotsynlighet, proaktiv jakt och hotsvar.

Med den inbyggda arbetsboken för Azure WAF-brandväggshändelser kan du få en översikt över säkerhetshändelserna i brandväggen för waf. Detta inkluderar händelser, matchade och blockerade regler och allt annat som loggas i brandväggsloggarna. Mer information om loggning finns nedan. 


![Arbetsbok för Azure WAF-brandväggshändelser](../media/ag-overview/sentinel.png)


#### <a name="azure-monitor-workbook-for-waf"></a>Azure Monitor arbetsbok för WAF

Den här arbetsboken möjliggör anpassad visualisering av säkerhetsrelevabla WAF-händelser över flera filtrerbara paneler. Det fungerar med alla WAF-typer, inklusive Application Gateway, Front Door och CDN, och kan filtreras baserat på WAF-typ eller en specifik WAF-instans. Importera via ARM-mall eller gallerimall. Information om hur du distribuerar den här arbetsboken finns [i WAF-arbetsbok .](https://aka.ms/AzWAFworkbook)

#### <a name="logging"></a>Loggning

Application Gateway WAF innehåller detaljerad rapportering om varje hot som identifieras. Loggning är integrerat med Azure Diagnostics loggar. Aviseringar registreras i .json-format. Dessa loggar kan integreras med Azure Monitor [loggar](../../azure-monitor/insights/azure-networking-analytics.md).

![Application Gateway för diagnostikloggar](../media/ag-overview/waf2.png)

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

Prissättningsmodellerna skiljer sig för WAF_v1 och WAF_v2 SKU:er. Mer information [finns Application Gateway prissidan.](https://azure.microsoft.com/pricing/details/application-gateway/) 

## <a name="whats-new"></a>Nyheter

Information om vad som är nytt med Azure Web Application Firewall finns i [Azure-uppdateringar.](https://azure.microsoft.com/updates/?category=networking&query=Web%20Application%20Firewall)

## <a name="next-steps"></a>Nästa steg

- Läs mer om [WAF-hanterade regler](application-gateway-crs-rulegroups-rules.md)
- Läs mer om [anpassade regler](custom-waf-rules-overview.md)
- Läs mer [om Web Application Firewall på Azure Front Door](../afds/afds-overview.md)
