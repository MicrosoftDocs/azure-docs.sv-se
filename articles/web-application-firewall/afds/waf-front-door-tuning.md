---
title: Justera Web Application Firewall (WAF) för Azure Front Door
description: I den här artikeln får du lära dig hur du finjusterar WAF för Front Door.
services: web-application-firewall
author: mohitkusecurity
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: mohitku
ms.reviewer: tyao
ms.openlocfilehash: c0879edc0e3fbd6cf6bcadc26dd862f95ecf4fd4
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872361"
---
# <a name="tuning-web-application-firewall-waf-for-azure-front-door"></a>Justera Web Application Firewall (WAF) för Azure Front Door
 
Den Azure-hanterade standardregeluppsättningen baseras på [OWASP Core Rule Set (CRS)](https://github.com/SpiderLabs/owasp-modsecurity-crs/tree/v3.1/dev) och är utformad för att vara strikt från start. Det förväntas ofta att WAF-regler måste justeras så att de passar de specifika behoven i programmet eller organisationen med waf. Detta uppnås vanligtvis genom att definiera regel uteslutningar, skapa anpassade regler och till och med inaktivera regler som kan orsaka problem eller falska positiva resultat. Det finns några saker du kan göra om begäranden som ska passera genom Web Application Firewall (WAF) blockeras.

Kontrollera först att du har läst översikten [Front Door WAF](afds-overview.md) och [WAF-principen för Front Door](waf-front-door-create-portal.md) dokument. Kontrollera också att du har aktiverat [WAF-övervakning och loggning.](waf-front-door-monitor.md) De här artiklarna förklarar hur WAF fungerar, hur WAF-regeluppsättningar fungerar och hur du kommer åt WAF-loggar.
 
## <a name="understanding-waf-logs"></a>Förstå WAF-loggar
 
Syftet med WAF-loggar är att visa varje begäran som matchas eller blockeras av WAF. Det är en samling med alla utvärderade begäranden som matchas eller blockeras. Om du märker att WAF blockerar en begäran som den inte borde (en falsk positiv) kan du göra några saker. Börja med att begränsa och hitta den specifika begäran. Om du vill kan du [konfigurera ett anpassat svarsmeddelande](./waf-front-door-configure-custom-response-code.md) så att fältet inkluderas för att enkelt identifiera händelsen och utföra en `trackingReference` loggfråga på det specifika värdet. Titta igenom loggarna för att hitta den specifika URI:en, tidsstämpeln eller klient-IP-adressen för begäran. När du hittar relaterade loggposter kan du börja agera på falska positiva resultat. 
 
Säg till exempel att du har en legitim trafik som innehåller `1=1` strängen som du vill passera genom din WAF. Så här ser begäran ut:

```
POST http://afdwafdemosite.azurefd.net/api/Feedbacks HTTP/1.1
Host: afdwafdemosite.azurefd.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 55

UserId=20&captchaId=7&captchaId=15&comment="1=1"&rating=3
```

Om du provar begäran blockerar WAF trafik som innehåller *strängen 1=1* i valfri parameter eller fält. Det här är en sträng som ofta är associerad med en SQL-ktionsattack. Du kan titta igenom loggarna och se tidsstämpeln för begäran och de regler som blockerade/matchade.
 
I följande exempel utforskar vi en logg som `FrontdoorWebApplicationFirewallLog` genereras på grund av en regelmatchning. Följande Log Analytics-fråga kan användas för att hitta begäranden som har blockerats under de senaste 24 timmarna:

```kusto
AzureDiagnostics
| where Category == 'FrontdoorWebApplicationFirewallLog'
| where TimeGenerated > ago(1d)
| where action_s == 'Block'

```
 
I `requestUri` fältet kan du se att begäran har gjorts `/api/Feedbacks/` till specifikt. Längre fram hittar vi `942110` regel-ID:t i `ruleName` fältet . Om du känner till regel-ID:t kan du gå till den officiella lagringsplatsen för [OWASP ModSecurity Core Rule Set och](https://github.com/coreruleset/coreruleset) söka efter det regel-ID:t för att granska koden och förstå exakt vad den här regeln matchar. [](https://github.com/coreruleset/coreruleset/blob/v3.1/dev/rules/REQUEST-942-APPLICATION-ATTACK-SQLI.conf) 
 
Genom att kontrollera fältet ser vi sedan att den här regeln är inställd på att blockera begäranden vid matchning, och vi bekräftar att begäran i själva verket blockerades av WAF eftersom är inställd `action` `policyMode` på `prevention` . 
 
Nu ska vi kontrollera informationen i `details` fältet . Det är här du kan se `matchVariableName` och `matchVariableValue` informationen. Vi lär oss att den här regeln utlöstes eftersom någon anger *1 = 1* `comment` i fältet för webbappen.
 
```json
{
    "time": "2020-09-24T16:43:04.5422943Z",
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.NETWORK/FRONTDOORS/AFDWAFDEMOSITE",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoor/WebApplicationFirewallLog/Write",
    "properties": {
        "clientIP": "1.1.1.1",
        "clientPort": "53566",
        "socketIP": "1.1.1.1",
        "requestUri": "http://afdwafdemosite.azurefd.net:80/api/Feedbacks/",
        "ruleName": "DefaultRuleSet-1.0-SQLI-942110",
        "policy": "AFDWAFDemoPolicy",
        "action": "Block",
        "host": "afdwafdemosite.azurefd.net",
        "trackingReference": "0mMxsXwAAAABEalekYeI4S55qpi5R7R0/V1NURURHRTA4MTIAZGI4NGQzZDgtNWQ5Ny00ZWRkLTg2ZGYtZDJjNThlMzI2N2I4",
        "policyMode": "prevention",
        "details": {
            "matches": [
                {
                    "matchVariableName": "PostParamValue:comment",
                    "matchVariableValue": "\"1=1\""
                }
            ],
            "msg": "SQL Injection Attack: Common Injection Testing Detected",
            "data": "Matched Data: \"1=1\" found within PostParamValue:comment: \"1=1\""
        }
    }
}
```
 
Det finns också värde i att kontrollera åtkomstloggarna för att utöka dina kunskaper om en viss WAF-händelse. Nedan granskar vi `FrontdoorAccessLog` loggen som genererades som ett svar på händelsen ovan.
 
Du kan se att dessa är relaterade loggar baserat på `trackingReference` att värdet är detsamma. Bland olika fält som ger allmänna insikter, till exempel `userAgent` och `clientIP` , uppmärksammar vi fälten och `httpStatusCode` `httpStatusDetails` . Här kan vi bekräfta att klienten har fått ett HTTP 403-svar, vilket absolut bekräftar att begäran nekades och blockerades. 
 
```json
{
    "time": "2020-09-24T16:43:04.5430764Z",
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.NETWORK/FRONTDOORS/AFDWAFDEMOSITE",
    "category": "FrontdoorAccessLog",
    "operationName": "Microsoft.Network/FrontDoor/AccessLog/Write",
    "properties": {
        "trackingReference": "0mMxsXwAAAABEalekYeI4S55qpi5R7R0/V1NURURHRTA4MTIAZGI4NGQzZDgtNWQ5Ny00ZWRkLTg2ZGYtZDJjNThlMzI2N2I4",
        "httpMethod": "POST",
        "httpVersion": "1.1",
        "requestUri": "http://afdwafdemosite.azurefd.net:80/api/Feedbacks/",
        "requestBytes": "2160",
        "responseBytes": "324",
        "userAgent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/85.0.4183.83 Safari/537.36",
        "clientIp": "1.1.1.1",
        "socketIp": "1.1.1.1",
        "clientPort": "53566",
        "timeToFirstByte": "0.01",
        "timeTaken": "0.011",
        "securityProtocol": "",
        "routingRuleName": "DemoBERoutingRule",
        "rulesEngineMatchNames": [],
        "backendHostname": "13.88.65.130:3000",
        "isReceivedFromClient": true,
        "httpStatusCode": "403",
        "httpStatusDetails": "403",
        "pop": "WST",
        "cacheStatus": "CONFIG_NOCACHE"
    }
}
```

## <a name="resolving-false-positives"></a>Lösa falska positiva resultat
 
För att kunna fatta ett välgrundat beslut om att hantera falska positiva resultat är det viktigt att du bekantar dig med de tekniker som programmet använder. Säg till exempel att det inte finns någon SQL-server i din teknikstack och att du får falska positiva resultat relaterade till dessa regler. Att inaktivera dessa regler innebär inte nödvändigtvis att din säkerhet försämras. 

Med den här informationen, och vetskapen om att regel 942110 är den som matchade strängen i vårt exempel, kan vi göra några saker för att förhindra att denna legitima begäran `1=1` blockeras:
 
* Använda undantagslistor
  * Se [Web Application Firewall (WAF) med undantagslistor Front Door-tjänsten](waf-front-door-exclusion.md) för mer information om undantagslistor. 
* Ändra WAF-åtgärder
  * Se [WAF-åtgärder](afds-overview.md#waf-actions) för mer information om vilka åtgärder som kan vidtas när en begäran matchar en regels villkor.
* Använda anpassade regler
  * Se [Anpassade regler för Web Application Firewall med Azure Front Door](waf-front-door-custom-rules.md) mer information om anpassade regler.
* Inaktivera regler 

> [!TIP]
> När du väljer en metod för att tillåta legitima begäranden via WAF bör du försöka göra detta så smalt som möjligt. Det är till exempel bättre att använda en undantagslista än att helt inaktivera en regel.

### <a name="using-exclusion-lists"></a>Använda undantagslistor

En fördel med att använda en undantagslista är att endast matchningsvariabeln som du väljer att exkludera inte längre kontrolleras för den angivna begäran. Det innebär att du kan välja mellan specifika begärandehuvuden, begärandecookies, frågesträngargument eller postargument för begärandetexten som ska undantas om ett visst villkor uppfylls, i stället för att utesluta hela begäran från att granskas. De andra icke-angivna variablerna i begäran inspekteras fortfarande normalt.
 
Det är viktigt att tänka på att undantag är en global inställning. Det innebär att det konfigurerade undantaget gäller för all trafik som passerar genom din WAF, inte bara en specifik webbapp eller URI. Detta kan till exempel vara ett problem om *1 = 1* är en giltig begäran i brödtexten för en viss webbapp, men inte för andra under samma WAF-princip. Om det är klokt att använda olika undantagslistor för olika program bör du överväga att använda olika WAF-principer för varje program och tillämpa dem på varje programs frontend.
 
När du konfigurerar undantagslistor för hanterade regler kan du välja att undanta alla regler i en regeluppsättning, alla regler i en regelgrupp eller en enskild regel. En undantagslista kan konfigureras med [hjälp av PowerShell,](/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject) [Azure CLI,](/cli/azure/network/front-door/waf-policy/managed-rules/exclusion#az_network_front_door_waf_policy_managed_rules_exclusion_add) [Rest API](/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)eller Azure Portal.

* Undantag på regelnivå
  * Att tillämpa undantag på regelnivå innebär att de angivna undantagen inte analyseras enbart mot den enskilda regeln, medan den fortfarande analyseras av alla andra regler i regeluppsättningen. Det här är den mest detaljerade nivån för undantag och kan användas för att finjustera den hanterade regeluppsättningen baserat på den information du hittar i WAF-loggarna när du felsöker en händelse.
* Undantag på regelgruppsnivå
  * Att tillämpa undantag på regelgruppsnivå innebär att de angivna undantagen inte analyseras mot den specifika uppsättningen regeltyper. Att till exempel välja *SQLI* som en undantagen regelgrupp anger att de definierade begärande undantagen inte skulle kontrolleras av någon av de SQLI-specifika reglerna, men det skulle fortfarande kontrolleras av regler i andra grupper, till exempel *PHP,* *RFI* eller *XSS*. Den här typen av undantag kan vara användbar när vi är säkra på att programmet inte är sårbart för specifika typer av attacker. Ett program som inte har några SQL-databaser kan till exempel undanta alla *SQLI-regler* utan att det skadar säkerhetsnivån.
* Undantag på regeluppsättningsnivå 
  * Att tillämpa undantag på en regeluppsättningsnivå innebär att de angivna undantagen inte analyseras mot någon av de säkerhetsregler som är tillgängliga i den regeluppsättningen. Det här är ett omfattande undantag, så det bör användas noggrant.

I det här exemplet utför vi ett undantag på den mest detaljerade nivån (tillämpar exkludering på en enskild regel) och vi vill undanta matchningsvariabeln Begärandetext **efter args-namn** som innehåller `comment` . Detta är uppenbart eftersom du kan se information om matchningsvariabler i brandväggsloggen: `"matchVariableName": "PostParamValue:comment"` . Attributet är `comment` . Du kan också hitta det här attributnamnet på några andra sätt. Mer information finns i [Hitta namn på begärandeattribut.](#finding-request-attribute-names)

![Undantagsregler](../media/waf-front-door-tuning/exclusion-rules.png)

![Regel exkludering för specifik regel](../media/waf-front-door-tuning/exclusion-rule.png)

Ibland finns det fall där specifika parametrar skickas till waf på ett sätt som kanske inte är intuitivt. Det finns till exempel en token som skickas vid autentisering med hjälp av Azure Active Directory. Denna `__RequestVerificationToken` token, , skickas vanligtvis som en cookie för begäran. I vissa fall där cookies är inaktiverade skickas denna token också som ett begärande efter argument. Därför måste du se till att läggs till i undantagslistan för både och för att åtgärda falska positiva Azure `__RequestVerificationToken` `RequestCookieNames` AD-token. `RequestBodyPostArgsNames`

Undantag i ett fältnamn *(väljare)* innebär att värdet inte längre utvärderas av WAF. Själva fältnamnet fortsätter dock att utvärderas och i sällsynta fall kan det matcha en WAF-regel och utlösa en åtgärd.

![Regel uteslutning för regeluppsättning](../media/waf-front-door-tuning/exclusion-rule-selector.png)

### <a name="changing-waf-actions"></a>Ändra WAF-åtgärder

Ett annat sätt att hantera beteendet för WAF-regler är att välja vilken åtgärd som ska vidtas när en begäran matchar en regels villkor. De tillgängliga åtgärderna är: [Tillåt, Blockera, Logga och Omdirigera](afds-overview.md#waf-actions).

I det här exemplet har vi ändrat standardåtgärden *Blockera* till *loggåtgärden* på regel 942110. Detta gör att WAF loggar begäran och fortsätter att utvärdera samma begäran mot återstående regler med lägre prioritet.

![WAF-åtgärder](../media/waf-front-door-tuning/actions.png)

När vi har utfört samma begäran kan vi referera tillbaka till loggarna och vi kommer att se att begäran var en matchning för regel-ID 942110 och att fältet nu anger Logg i stället för `action_s` *Blockera*.  Vi expanderade sedan loggfrågan för att inkludera `trackingReference_s` informationen och se vad mer som har hänt med den här begäran.

![Logg som visar flera regelmatchning](../media/waf-front-door-tuning/actions-log.png)

Intressant nog ser vi att en annan SQLI-regelmatchning inträffar millisekunder efter att regel-ID 942110 har bearbetats. Samma begäran matchade för regel-ID 942310 och den här gången utlöstes *standardåtgärden* Blockera.

En annan fördel med att använda *loggåtgärden* vid WAF-justering eller felsökning är att du kan identifiera om flera regler i en viss regelgrupp matchar och blockerar en viss begäran. Du kan sedan skapa dina undantag på lämplig nivå, dvs. på regel- eller regelgruppsnivå. 

### <a name="using-custom-rules"></a>Använda anpassade regler

När du har identifierat vad som orsakar en WAF-regelmatchning kan du använda anpassade regler för att justera hur WAF svarar på händelsen. Anpassade regler bearbetas före hanterade regler, de kan innehålla fler än ett villkor och deras åtgärder kan vara [Tillåt, Neka, Logga eller Omdirigera.](afds-overview.md#waf-actions) När det finns en regelmatchning stoppar WAF-motorn bearbetningen. Det innebär att andra anpassade regler med lägre prioritet och hanterade regler inte längre körs.

I exemplet nedan skapade vi en anpassad regel med två villkor. Det första villkoret söker efter värdet `comment` i begärandetexten. Det andra villkoret söker efter värdet `/api/Feedbacks/` i URI:en för begäran.

Med en anpassad regel kan du vara den mest detaljerade när du finjusterar WAF-reglerna och hanterar falska positiva resultat. I det här fallet vidtar vi inte bara åtgärder baserat på begärandetextens värde, som kan finnas på flera webbplatser eller appar `comment` under samma WAF-princip. Genom att inkludera ett annat villkor som även ska matcha en viss begärande-URI ser vi till att den här anpassade regeln verkligen gäller för det här explicita användningsfallet som `/api/Feedbacks/` vi har granskat. Detta säkerställer att samma attack, om den utförs mot olika villkor, fortfarande skulle inspekteras och förhindras av WAF-motorn.

![Loggas](../media/waf-front-door-tuning/custom-rule.png)

När du utforskar loggen kan du se att fältet innehåller det namn som `ruleName_s` ges till den anpassade regel som vi skapade: `redirectcomment` . I fältet `action_s` kan du se att omdirigeringsåtgärden har vidtagits för den här händelsen.  I fältet `details_matches_s` kan vi se att informationen för båda villkoren matchades.

### <a name="disabling-rules"></a>Inaktivera regler

Ett annat sätt att komma runt en falsk positiv är att inaktivera regeln som matchade på de indata som WAF trodde var skadliga. Eftersom du har parsat WAF-loggarna och har begränsat regeln till 942110 kan du inaktivera den i Azure Portal. Se [Anpassa Web Application Firewall regler med hjälp av Azure Portal](../ag/application-gateway-customize-waf-rules-portal.md#disable-rule-groups-and-rules).
 
Att inaktivera en regel är en fördel när du är säker på att alla begäranden som uppfyller det specifika villkoret i själva verket är legitima begäranden, eller när du är säker på att regeln helt enkelt inte gäller för din miljö (t.ex. inaktivering av en SQL- injectionsregel eftersom du har icke-SQL-servermiljöer). 
 
Inaktivering av en regel är dock en global inställning som gäller för alla frontend-värdar som är associerade med WAF-principen. När du väljer att inaktivera en regel kan du låta sårbarheter exponeras utan skydd eller identifiering för andra frontend-värdar som är associerade med WAF-principen.
 
Om du vill använda Azure PowerShell för att inaktivera en hanterad regel kan du läsa [`PSAzureManagedRuleOverride`](/powershell/module/az.frontdoor/new-azfrontdoorwafmanagedruleoverrideobject) objektdokumentationen. Om du vill använda Azure CLI kan du läsa [`az network front-door waf-policy managed-rules override`](/cli/azure/network/front-door/waf-policy/managed-rules/override) dokumentationen.

![WAF-regler](../media/waf-front-door-tuning/waf-rules.png)

> [!TIP]
> Det är en bra idé att dokumentera eventuella ändringar som du gör i WAF-principen. Inkludera exempelbegäranden för att illustrera identifieringen av falska positiva identifieringar och förklara tydligt varför du har lagt till en anpassad regel, inaktiverat en regel eller regeluppsättning eller lagt till ett undantag. Den här dokumentationen kan vara användbar om du gör om ditt program i framtiden och behöver kontrollera att ändringarna fortfarande är giltiga. Det kan också vara till hjälp om du någon gång granskas eller behöver motivera varför du har konfigurerat om WAF-principen från dess standardinställningar.

## <a name="finding-request-fields"></a>Hitta fält för begäran

Med hjälp av en [webbläsarproxy som Fiddler](https://www.telerik.com/fiddler)kan du inspektera enskilda begäranden och avgöra vilka specifika fält på en webbsida som anropas. Detta är användbart när vi behöver undanta vissa fält från inspektion med undantagslistor i WAF.

### <a name="finding-request-attribute-names"></a>Hitta namn på begärandeattribut
 
I det här exemplet kan du se att fältet där `1=1` strängen har angetts kallas `comment` . Dessa data skickades i brödtexten i en POST-begäran.

![Fiddler-begäran som visar brödtext](../media/waf-front-door-tuning/fiddler-request-attribute-name.png)

Det här är ett fält som du kan undanta. Mer information om undantagslistor finns i [Undantagslistor för brandvägg för webbaserade program.](./waf-front-door-exclusion.md) Du kan utesluta utvärderingen i det här fallet genom att konfigurera följande undantag:

![Undantagsregel](../media/waf-front-door-tuning/fiddler-request-attribute-name-exclusion.png)

Du kan också granska brandväggsloggarna för att få information om vad du behöver lägga till i undantagslistan. Om du vill aktivera [loggning kan du se Övervakningsmått och loggar i Azure Front Door](./waf-front-door-monitor.md).

Granska brandväggsloggen `PT1H.json` i filen under den timme som begäran som du vill granska inträffade. `PT1H.json` -filer är tillgängliga i de lagringskontocontainrar `FrontDoorWebApplicationFirewallLog` där `FrontDoorAccessLog` diagnostikloggarna och lagras.

I det här exemplet kan du se regeln som blockerade begäran (med samma transaktionsreferens) och inträffade exakt samtidigt:

```json
{
    "time": "2020-09-24T16:43:04.5422943Z",
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.NETWORK/FRONTDOORS/AFDWAFDEMOSITE",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoor/WebApplicationFirewallLog/Write",
    "properties": {
        "clientIP": "1.1.1.1",
        "clientPort": "53566",
        "socketIP": "1.1.1.1",
        "requestUri": "http://afdwafdemosite.azurefd.net:80/api/Feedbacks/",
        "ruleName": "DefaultRuleSet-1.0-SQLI-942110",
        "policy": "AFDWAFDemoPolicy",
        "action": "Block",
        "host": "afdwafdemosite.azurefd.net",
        "trackingReference": "0mMxsXwAAAABEalekYeI4S55qpi5R7R0/V1NURURHRTA4MTIAZGI4NGQzZDgtNWQ5Ny00ZWRkLTg2ZGYtZDJjNThlMzI2N2I4",
        "policyMode": "prevention",
        "details": {
            "matches": [
                {
                    "matchVariableName": "PostParamValue:comment",
                    "matchVariableValue": "\"1=1\""
                }
            ],
            "msg": "SQL Injection Attack: Common Injection Testing Detected",
            "data": "Matched Data: \"1=1\" found within PostParamValue:comment: \"1=1\""
        }
    }
}
```

När du vet hur de Azure-hanterade regeluppsättningarna fungerar (se [Web Application Firewall på Azure Front Door](afds-overview.md)) vet du att regeln med *åtgärden: Blockera* egenskapen blockerar baserat på de data som matchas i begärandetexten. Du kan se i informationen att det matchade ett mönster ( `1=1` ), och fältet heter `comment` . Följ samma föregående steg för att undanta begärandetexten efter args-namnet som innehåller `comment` .

### <a name="finding-request-header-names"></a>Hitta namn på begärandehuvuden

Fiddler är ett användbart verktyg igen för att hitta namn på begärandehuvuden. I följande skärmbild kan du se huvudena för den här GET-begäran, som innehåller Innehållstyp, Användaragent och så vidare. Du kan också använda begärandehuvuden för att skapa undantag och anpassade regler i WAF.

![Fiddler-begäran som visar sidhuvud](../media/waf-front-door-tuning/fiddler-request-header-name.png)

Ett annat sätt att visa begärande- och svarshuvuden är att titta i utvecklarverktygen i webbläsaren, till exempel Edge eller Chrome. Du kan trycka på F12 eller högerklicka på -> **Inspect** Utvecklarverktyg och välja fliken Nätverk. Läs in en webbsida och klicka på den begäran som  ->  du vill granska. 

![Begäran om nätverkskontroll](../media/waf-front-door-tuning/network-inspector-request.png)

### <a name="finding-request-cookie-names"></a>Hitta namn på begärandecookies

Om begäran innehåller cookies kan du välja fliken Cookies för att visa dem i Fiddler. Cookieinformation kan också användas för att skapa undantag eller anpassade regler i WAF.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azures brandvägg för webbaserade program](../overview.md).
- Läs hur du [skapar en Front Door](../../frontdoor/quickstart-create-front-door.md).
