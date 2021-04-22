---
title: Undantagslistor för brandväggar för webbaserade program Azure Front Door – Azure Portal
description: Den här artikeln innehåller information om konfiguration av undantagslistor i Azure Front med Azure Portal.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/10/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 83baf03c414d9b0f7acb6a93db03794a539a3c58
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107860841"
---
# <a name="web-application-firewall-waf-with-front-door-service-exclusion-lists"></a>Web Application Firewall (WAF) med Front Door undantagslistor för tjänsten 

Ibland Web Application Firewall (WAF) blockera en begäran som du vill tillåta för ditt program. Active Directory infogar till exempel token som används för autentisering. Dessa token kan innehålla specialtecken som kan utlösa en falsk positiv från WAF-reglerna. Med WAF-undantagslistor kan du utelämna vissa begärandeattribut från en WAF-utvärdering.  En undantagslista kan konfigureras med  [hjälp av PowerShell,](/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject) [Azure CLI,](/cli/azure/network/front-door/waf-policy/managed-rules/exclusion#az_network_front_door_waf_policy_managed_rules_exclusion_add) [Rest API](/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)eller Azure Portal. I följande exempel visas Azure Portal konfiguration. 
## <a name="configure-exclusion-lists-using-the-azure-portal"></a>Konfigurera undantagslistor med hjälp av Azure Portal
**Hantera undantag är** tillgängligt från WAF-portalen under **Hanterade regler**

![Hantera undantag ](../media/waf-front-door-exclusion/exclusion1.png)
 ![ Hantera exclusion_add](../media/waf-front-door-exclusion/exclusion2.png)

 En exempel på undantagslista: ![ Hantera exclusion_define](../media/waf-front-door-exclusion/exclusion3.png)

Det här exemplet utesluter värdet i *användarhuvudfältet.* En giltig begäran kan innehålla *användarfältet som* innehåller en sträng som utlöser en SQL- injectionsregel. Du kan undanta *användarparametern* i det här fallet så att WAF-regeln inte utvärderar något i fältet.

Följande attribut kan läggas till i undantagslistor efter namn. Värdena för de fält som du använder utvärderas inte mot WAF-regler, men deras namn utvärderas. Undantagslistorna tar bort kontrollen av fältets värde.

* Namn på begärandehuvud
* Namn på begärandecookie
* Frågesträng argsnamn
* Begärandetext efter args-namn

Du kan ange en exakt begärandehuvud, brödtext, cookie eller frågesträngsattributmatchning.  Du kan också ange partiella matchningar. Följande operatorer är de matchningsvillkor som stöds:

- **Är lika** med: Den här operatorn används för en exakt matchning. Om du till exempel vill välja en rubrik med namnet **bearerToken** använder du operatorn equals med väljaren inställd som **bearerToken**.
- **Börjar med**: Den här operatorn matchar alla fält som börjar med det angivna väljarvärdet.
- **Slutar med**: Den här operatorn matchar alla fält för begäran som slutar med det angivna väljarvärdet.
- **Contains**: Den här operatorn matchar alla fält för begäran som innehåller det angivna väljarvärdet.
- **Är lika med alla**: Den här operatorn matchar alla fält för begäran. * är väljarvärdet.

Huvud- och cookienamn är okänsliga.

Om ett rubrikvärde, cookie-värde, postargumentvärde eller frågeargumentvärde ger falska positiva resultat för vissa regler kan du utesluta den delen av begäran från regeln:


|matchVariableName från WAF-loggar  |Regel uteslutning i portalen  |
|---------|---------|
|CookieValue:SOME_NAME        |Begärandecookienamnet är lika med SOME_NAME|
|HeaderValue:SOME_NAME        |Namnet på begärandehuvudet är SOME_NAME|
|PostParamValue:SOME_NAME     |Begärandetext efter args-namn är lika med SOME_NAME|
|QueryParamValue:SOME_NAME    |Frågesträngens argsnamn är lika med SOME_NAME|


Vi stöder för närvarande endast regel uteslutningar för ovanstående matchVariableNames i sina WAF-loggar. För andra matchVariableNames måste du antingen inaktivera regler som ger falska positiva resultat eller skapa en anpassad regel som uttryckligen tillåter dessa begäranden. När matchVariableName är CookieName, HeaderName, PostParamName eller QueryParamName innebär det att själva namnet utlöser regeln. Regel exkludering har inget stöd för dessa matchVariableNames just nu.


Om du undantar begärandetexten efter args med namnet *FOO* ska ingen regel visa PostParamValue:FOO som matchVariableName i dina WAF-loggar. Du kan dock fortfarande se en regel med matchVariableName InitialBodyContents som matchar värdet för postparamens FOO eftersom postparam-värden är en del av InitialBodyContents.

Du kan tillämpa undantagslistor på alla regler i den hanterade regeluppsättningen, på regler för en specifik regelgrupp eller på en enskild regel som visas i föregående exempel.

## <a name="define-exclusion-based-on-web-application-firewall-logs"></a>Definiera exkludering baserat Web Application Firewall loggar
 [Azure Web Application Firewall övervakning och loggning](waf-front-door-monitor.md) visar matchande information om en blockerad begäran. Om ett rubrikvärde, cookie-värde, efterargumentvärde eller frågeargumentvärde genererar falska positiva resultat för vissa regler kan du undanta den delen av begäran från att beaktas av regeln. I följande tabell visas exempelvärden från WAF-loggar och motsvarande undantagsvillkor.

|matchVariableName från WAF-loggar    |Regel exkludering i portalen|
|--------|------|
|CookieValue:SOME_NAME  |Begärandecookienamn är lika med SOME_NAME|
|HeaderValue:SOME_NAME  |Namnet på begärandehuvudet är lika med SOME_NAME|
|PostParamValue:SOME_NAME|  Begärandetext efter args-namn är lika med SOME_NAME|
|QueryParamValue:SOME_NAME| Frågesträngens argsnamn är lika med SOME_NAME|


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat WAF-inställningarna kan du läsa om hur du visar WAF-loggarna. Mer information finns i [Front Door diagnostik](../afds/waf-front-door-monitor.md).