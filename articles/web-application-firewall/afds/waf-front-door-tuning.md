---
title: Justera brand vägg för webbaserade program (WAF) för Azure-front dörr
description: I den här artikeln får du lära dig hur du finjusterar WAF för front dörren.
services: web-application-firewall
author: mohitkusecurity
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: mohitku
ms.reviewer: tyao
ms.openlocfilehash: b2f551257fb6869d5dec47014be3a8522b61b9fa
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102506641"
---
# <a name="tuning-web-application-firewall-waf-for-azure-front-door"></a>Justera brand vägg för webbaserade program (WAF) för Azure-front dörr
 
Den Azure-hanterade standard regel uppsättningen är baserad på [OWASP Core regel set (datoriserat)](https://github.com/SpiderLabs/owasp-modsecurity-crs/tree/v3.1/dev) och är utformad för att vara strikt ut ur kartongen. Det förväntas ofta att WAF regler måste vara justerade för att passa de specifika behoven hos programmet eller organisationen med hjälp av WAF. Detta uppnås ofta genom att definiera regel undantag, skapa anpassade regler och till och med inaktivera regler som kan orsaka problem eller falska positiva identifieringar. Det finns några saker du kan göra om förfrågningar som ska passera genom brand väggen för webbaserade program (WAF) är blockerade.

Se först till att du har läst [WAF-översikten för front dörren](afds-overview.md) och [WAF-principen för front dörr](waf-front-door-create-portal.md) dokument. Kontrol lera också att du har aktiverat [övervakning och loggning av WAF](waf-front-door-monitor.md). De här artiklarna förklarar hur WAF-funktionerna fungerar, hur WAF-regel uppsättningarna fungerar och hur du får åtkomst till WAF-loggar.
 
## <a name="understanding-waf-logs"></a>Förstå WAF-loggar
 
Syftet med WAF-loggar är att visa varje begäran som matchas eller blockeras av WAF. Det är en samling av alla utvärderade begär Anden som matchas eller blockeras. Om du märker att WAF blockerar en begäran om att den inte ska (ett falskt positivt) kan du göra några saker. Först, begränsa och hitta en specifik begäran. Om du vill kan du [Konfigurera ett anpassat svarsmeddelande](./waf-front-door-configure-custom-response-code.md) så att det innehåller `trackingReference` fältet för att enkelt identifiera händelsen och utföra en logg fråga på det aktuella värdet. Leta igenom loggarna för att hitta URI, tidsstämpel eller klient-IP för begäran. När du hittar de relaterade logg posterna kan du börja arbeta med falska positiva identifieringar. 
 
Anta till exempel att du har en legitim trafik som innehåller den sträng `1=1` som du vill skicka genom din WAF. Så här ser begäran ut:

```
POST http://afdwafdemosite.azurefd.net/api/Feedbacks HTTP/1.1
Host: afdwafdemosite.azurefd.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 55

UserId=20&captchaId=7&captchaId=15&comment="1=1"&rating=3
```

Om du testar begäran blockerar WAF trafik som innehåller din *1 = 1* -sträng i valfri parameter eller fält. Detta är en sträng som ofta är kopplad till en SQL-attack. Du kan titta igenom loggarna och se tidsstämpeln för begäran och reglerna som har blockerats/matchats.
 
I följande exempel utforskar vi en `FrontdoorWebApplicationFirewallLog` logg som genererats på grund av en regel matchning. Följande Log Analytics fråga kan användas för att söka efter begär Anden som har blockerats under de senaste 24 timmarna:

```kusto
AzureDiagnostics
| where Category == 'FrontdoorWebApplicationFirewallLog'
| where TimeGenerated > ago(1d)
| where action_s == 'Block'

```
 
I `requestUri` fältet kan du se att begäran har gjorts `/api/Feedbacks/` specifikt. Nu hittar vi regel-ID: t `942110` i `ruleName` fältet. Genom att känna till regel-ID: t kan du gå till [OWASP ModSecurity Core Rule uppsättnings officiella lagrings plats](https://github.com/coreruleset/coreruleset) och söka utifrån [regel-ID: t](https://github.com/coreruleset/coreruleset/blob/v3.1/dev/rules/REQUEST-942-APPLICATION-ATTACK-SQLI.conf) för att granska koden och förstå exakt vad den här regeln matchar. 
 
När du sedan kontrollerar `action` fältet ser vi att den här regeln är inställd på blockera begär Anden vid matchning, och vi bekräftar att begäran faktiskt blockeras av WAF eftersom `policyMode` är inställt på `prevention` . 
 
Nu ska vi kontrol lera informationen i `details` fältet. Här kan du se `matchVariableName` och `matchVariableValue` informationen. Vi lär oss att den här regeln utlöstes på grund av att någon inmatade *1 = 1* i `comment` fältet i webbappen.
 
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
 
Det finns också ett värde för att kontrol lera åtkomst loggarna för att utöka dina kunskaper om en specifik WAF-händelse. Nedan granskar vi `FrontdoorAccessLog` loggen som genererades som ett svar på händelsen ovan.
 
Du kan se dessa är relaterade loggar baserat på `trackingReference` värdet som är samma. Bland olika fält som ger allmän insikt, till exempel `userAgent` och `clientIP` , kommer vi att uppmärksamma `httpStatusCode` fälten och `httpStatusDetails` . Här kan vi bekräfta att klienten har tagit emot ett HTTP 403-svar, vilket absolut bekräftar att denna begäran nekades och blockerats. 
 
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

## <a name="resolving-false-positives"></a>Lösa falska positiva identifieringar
 
För att fatta ett informerat beslut om att hantera ett falskt positivt är det viktigt att bekanta dig med de tekniker som används i programmet. Anta till exempel att det inte finns någon SQL-Server i din tekniks tack och att du får falska positiva identifieringar som är relaterade till dessa regler. Att inaktivera dessa regler innebär inte nödvändigt vis att säkerheten minskas. 

Med den här informationen, och kunskapen om regel 942110 är den som matchade `1=1` strängen i vårt exempel, kan vi göra några saker för att stoppa den här legitima begäran från att blockeras:
 
* Använda undantags listor
  * Se [brand vägg för webbaserade program (WAF) med undantags listor för frontend-tjänsten](waf-front-door-exclusion.md) för mer information om undantags listor. 
* Ändra WAF-åtgärder
  * Se [WAF-åtgärder](afds-overview.md#waf-actions) för mer information om vilka åtgärder som kan vidtas när en begäran matchar en regels villkor.
* Använd anpassade regler
  * Se [anpassade regler för brand vägg för webbaserade program med Azures front dörr](waf-front-door-custom-rules.md) för mer information om anpassade regler.
* Inaktivera regler 

> [!TIP]
> När du väljer en metod för att tillåta legitima begär Anden via WAF, försöker du göra detta så smalt som möjligt. Till exempel är det bättre att använda en undantags lista än att inaktivera en regel helt.

### <a name="using-exclusion-lists"></a>Använda undantags listor

En fördel med att använda en undantags lista är att bara den matchnings variabel som du väljer att undanta inte längre kommer att kontrol leras för den aktuella begäran. Det innebär att du kan välja mellan specifika begärandehuvuden, begära cookies, argument för frågesträng eller begär ande text argument som ska uteslutas om ett visst villkor uppfylls, i stället för att undanta hela begäran från att inspekteras. De övriga icke-specificerade variablerna i begäran kommer fortfarande att inspekteras som vanligt.
 
Det är viktigt att tänka på att undantag är en global inställning. Det innebär att den konfigurerade undantagen gäller för all trafik som passerar genom din WAF, inte bara en speciell webbapp eller URI. Detta kan till exempel vara ett problem om *1 = 1* är en giltig begäran i bröd texten för en viss webbapp, men inte för andra i samma WAF-princip. Om det är bra att använda olika undantags listor för olika program, bör du överväga att använda olika WAF-principer för varje program och tillämpa dem på varje program klient del.
 
När du konfigurerar exkluderings listor för hanterade regler kan du välja att undanta alla regler inom en regel uppsättning, alla regler inom en regel grupp eller en enskild regel. En undantags lista kan konfigureras med [PowerShell](/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject), [Azure CLI](/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/exclusion#ext_front_door_az_network_front_door_waf_policy_managed_rules_exclusion_add), [REST API](/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)eller Azure Portal.

* Undantag på regel nivå
  * Att tillämpa undantag på en regel nivå innebär att de angivna undantagen inte analyseras mot den enskilda regeln, medan de fortfarande analyseras av alla andra regler i regel uppsättningen. Detta är den mest detaljerade nivån för undantag och kan användas för att finjustera den hanterade regel uppsättningen baserat på den information som du hittar i WAF-loggarna när du felsöker en händelse.
* Undantag på regel grupp nivå
  * Att tillämpa undantag på en regel grupp nivå innebär att de angivna undantagen inte kommer att analyseras mot den specifika uppsättningen regel typer. Om du till exempel väljer *SQLI* som en undantagen regel grupp anger de definierade undantagen för begäran inte att kontrol leras av någon av de SQLI reglerna, men den kommer fortfarande att kontrol leras av regler i andra grupper, t. ex. *php*, *RFI* eller *XSS*. Den här typen av undantag kan vara användbar när vi är säker på att programmet inte är sårbart för specifika typer av attacker. Till exempel kan ett program som inte har några SQL-databaser ha alla *SQLI* -regler exkluderade utan att det är skadligt för säkerhets nivån.
* Undantag på regel uppsättnings nivå 
  * Att tillämpa undantag på en regel uppsättnings nivå innebär att de angivna undantagen inte kommer att analyseras mot någon av de säkerhets regler som är tillgängliga i regel uppsättningen. Detta är ett omfattande undantag, så den bör användas noggrant.

I det här exemplet kommer vi att utföra ett undantag på den mest detaljerade nivån (tillämpa exkludering på en enskild regel) och vi vill utesluta det matchande variabeln **brödtext post namn** som innehåller `comment` . Detta är uppenbart eftersom du kan se matchnings informationen i brand Väggs loggen: `"matchVariableName": "PostParamValue:comment"` . Attributet är `comment` . Du kan också hitta det här attributnamnet på några andra sätt, se [hitta attribut namn för begäran](#finding-request-attribute-names).

![Undantags regler](../media/waf-front-door-tuning/exclusion-rules.png)

![Regel undantag för en speciell regel](../media/waf-front-door-tuning/exclusion-rule.png)

Ibland finns det fall där vissa parametrar får skickas till WAF på ett sätt som inte är intuitivt. Det finns till exempel en token som skickas när du autentiserar med hjälp av Azure Active Directory. Denna token `__RequestVerificationToken` skickas vanligt vis som en cookie för begäran. Men i vissa fall där cookies är inaktiverat, skickas denna token också i som ett argument för Request-inlägget. Därför måste du kontrol lera att `__RequestVerificationToken` har lagts till i undantags listan för både och för att adressera Azure AD-token med falska positiva identifieringar `RequestCookieNames` `RequestBodyPostArgsNames` .

Undantag i ett fält namn (*väljare*) innebär att värdet inte längre kommer att utvärderas av WAF. Men fält namnet fortsätter att utvärderas och i sällsynta fall kan det matcha en WAF-regel och utlösa en åtgärd.

![Regel undantag för regel uppsättning](../media/waf-front-door-tuning/exclusion-rule-selector.png)

### <a name="changing-waf-actions"></a>Ändra WAF-åtgärder

Ett annat sätt att hantera beteendet för WAF-regler är genom att välja den åtgärd som ska vidtas när en begäran matchar en regels villkor. Tillgängliga åtgärder är: [Tillåt, blockera, logga och omdirigera](afds-overview.md#waf-actions).

I det här exemplet har vi ändrat standard åtgärds *block* till *logg* åtgärden för regel 942110. Detta gör att WAF loggar begäran och fortsätter att utvärdera samma begäran mot de återstående reglerna för lägre prioritet.

![WAF-åtgärder](../media/waf-front-door-tuning/actions.png)

Efter att ha genomfört samma begäran kan vi referera till loggarna och vi ser att denna begäran var en matchning för regel-ID 942110 och att `action_s` fältet nu indikerar *logg* i stället för *block*. Vi expanderar sedan logg frågan för att inkludera `trackingReference_s` informationen och se vad mer har hänt med den här begäran.

![Logg som visar flera regel matchningar](../media/waf-front-door-tuning/actions-log.png)

Vi kan se en annan SQLI regel matchning inträffar i millisekunder efter att regel-ID 942110 bearbetats. Samma begäran matchade på regel-ID 942310 och den här gången är standard åtgärds *block* utlösta.

En annan fördel med att använda *logg* åtgärden vid WAF-justering eller fel sökning är att du kan identifiera om flera regler i en specifik regel grupp matchar och blockerar en angiven begäran. Du kan sedan skapa dina undantag på lämplig nivå, t. ex. på regel-eller regel grupps nivå. 

### <a name="using-custom-rules"></a>Använda anpassade regler

När du har identifierat vad som orsakar en regel matchning i WAF kan du använda anpassade regler för att justera hur WAF svarar på händelsen. Anpassade regler bearbetas före hanterade regler, de kan innehålla fler än ett villkor och deras åtgärder kan vara [Tillåt, neka, logga eller omdirigera](afds-overview.md#waf-actions). När det finns en regel matchning stoppar WAF-motorn bearbetning. Det innebär att andra anpassade regler med lägre prioritet och hanterade regler inte längre körs.

I exemplet nedan har vi skapat en anpassad regel med två villkor. Det första villkoret är att leta efter `comment` värdet i begär ande texten. Det andra villkoret söker efter `/api/Feedbacks/` värdet i URI: n för begäran.

Med hjälp av en anpassad regel kan du vara mest detaljerad när du finjusterar dina WAF-regler och hanterar falska positiva identifieringar. I det här fallet vidtar vi inte åtgärder baserat på värdet för `comment` begär ande texten, som kan finnas på flera webbplatser eller i appar i samma WAF-princip. Genom att inkludera ett annat villkor som också matchar i en viss begär ande-URI `/api/Feedbacks/` , ser vi till att den här anpassade regeln verkligen gäller för det explicita användnings fall som vi testats ut. Detta säkerställer att samma attack, om det utförs mot olika villkor, fortfarande kommer att inspekteras och förhindras av WAF-motorn.

![Loggas](../media/waf-front-door-tuning/custom-rule.png)

När du utforskar loggen kan du se att `ruleName_s` fältet innehåller det namn som angavs för den anpassade regel som vi skapade: `redirectcomment` . I `action_s` fältet kan du se att *omdirigerings* åtgärden vidtogs för den här händelsen. I `details_matches_s` fältet kan vi se information om båda villkoren matchades.

### <a name="disabling-rules"></a>Inaktivera regler

Ett annat sätt att komma runt ett falskt positivt är att inaktivera regeln som matchade den angivna invärdet. WAF trodde var skadlig. Eftersom du har analyserat WAF-loggarna och har begränsat regeln till 942110 kan du inaktivera den i Azure Portal. Se [Anpassa brand Väggs regler för webb program med hjälp av Azure Portal](../ag/application-gateway-customize-waf-rules-portal.md#disable-rule-groups-and-rules).
 
Att inaktivera en regel är en fördel när du är säker på att alla begär Anden som uppfyller villkoret verkligen är legitima begär Anden, eller när du är säker på att regeln helt enkelt inte gäller för din miljö (t. ex. genom att inaktivera en SQL-inmatnings regel eftersom du har icke-SQL-StartSlut). 
 
Att inaktivera en regel är dock en global inställning som gäller för alla klient dels värdar som är kopplade till WAF-principen. När du väljer att inaktivera en regel kan du lämna sårbarheter som exponeras utan skydd eller identifiering för andra klient dels värdar som är kopplade till WAF-principen.
 
Om du vill använda Azure PowerShell för att inaktivera en hanterad regel, se [`PSAzureManagedRuleOverride`](/powershell/module/az.frontdoor/new-azfrontdoorwafmanagedruleoverrideobject) objekt dokumentationen. Om du vill använda Azure CLI kan du läsa mer i [`az network front-door waf-policy managed-rules override`](/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/override) dokumentationen.

![WAF-regler](../media/waf-front-door-tuning/waf-rules.png)

> [!TIP]
> Det är en bra idé att dokumentera eventuella ändringar som du gör i WAF-principen. Inkludera exempel begär Anden för att illustrera falsk positiv identifiering och klart förklara varför du har lagt till en anpassad regel, inaktiverat en regel eller ruleset eller lagt till ett undantag. Den här dokumentationen kan vara till hjälp om du omdesignerar programmet i framtiden och behöver kontrol lera att ändringarna fortfarande är giltiga. Det kan också hjälpa om du någonsin har granskat eller behöver motivera att du har konfigurerat om WAF-principen från standardinställningarna.

## <a name="finding-request-fields"></a>Hitta begär ande fält

Med hjälp av en webbläsare som [Fiddler](https://www.telerik.com/fiddler)kan du kontrol lera enskilda förfrågningar och fastställa vilka specifika fält på en webb sida som anropas. Detta är användbart när vi behöver undanta vissa fält från inspektion med undantags listor i WAF.

### <a name="finding-request-attribute-names"></a>Söker efter attribut för begäran
 
I det här exemplet kan du se fältet där `1=1` strängen har angetts kallas `comment` . Dessa data skickades i bröd texten i en POST-begäran.

![Fiddler-begäran som visar bröd texten](../media/waf-front-door-tuning/fiddler-request-attribute-name.png)

Det här är ett fält som du kan undanta. Mer information om undantags listor finns i [undantags listor för brand vägg för webb program](./waf-front-door-exclusion.md). Du kan undanta utvärderingen i det här fallet genom att konfigurera följande undantag:

![Exkluderings regel](../media/waf-front-door-tuning/fiddler-request-attribute-name-exclusion.png)

Du kan också granska brand Väggs loggarna för att hämta information för att se vad du behöver lägga till i undantags listan. Information om hur du aktiverar loggning finns [i övervaka mått och loggar i Azures front dörr](./waf-front-door-monitor.md).

Granska brand Väggs loggen i `PT1H.json` filen under den timme som den begäran du vill kontrol lera har genomförts. `PT1H.json` filerna är tillgängliga i lagrings konto behållarna där `FrontDoorWebApplicationFirewallLog` och `FrontDoorAccessLog` diagnostiska loggar lagras.

I det här exemplet kan du se regeln som blockerade begäran (med samma transaktions referens) och inträffade på exakt samma gång:

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

Med dina kunskaper om hur de Azure-hanterade regel uppsättningarna fungerar (se [brand vägg för webbaserade program på Azures front dörr](afds-overview.md)) vet du att regeln med egenskapen *åtgärd: block* blockeras baserat på de data som matchas i begär ande texten. Du kan se den information som den matchade ett mönster ( `1=1` ) och fältet heter `comment` . Följ samma föregående steg för att undanta begär ande texten efter argument namnet som innehåller `comment` .

### <a name="finding-request-header-names"></a>Söker efter begärans huvud namn

Fiddler är ett användbart verktyg en gång till för att hitta rubrik namn för begäran. I följande skärm bild kan du se huvudena för GET-begäran, som innehåller innehålls typ, användar agent och så vidare. Du kan också använda begärandehuvuden för att skapa undantag och anpassade regler i WAF.

![Fiddler-begäran som visar rubrik](../media/waf-front-door-tuning/fiddler-request-header-name.png)

Ett annat sätt att Visa begäran och svarshuvuden är att titta inuti utvecklarverktyg i webbläsaren, till exempel Edge eller Chrome. Du kan trycka på F12 eller högerklicka – > **inspektera**  ->  **utvecklarverktyg** och välj fliken **nätverk** . Läs in en webb sida och klicka på den begäran som du vill granska.

![Begäran om nätverks kontroll](../media/waf-front-door-tuning/network-inspector-request.png)

### <a name="finding-request-cookie-names"></a>Söker efter cookie-namn för begäran

Om begäran innehåller cookies kan fliken cookies väljas för att visa dem i Fiddler. Cookie-information kan också användas för att skapa undantag eller anpassade regler i WAF.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Azure WebApplication-brandväggen](../overview.md).
- Läs hur du [skapar en Front Door](../../frontdoor/quickstart-create-front-door.md).
