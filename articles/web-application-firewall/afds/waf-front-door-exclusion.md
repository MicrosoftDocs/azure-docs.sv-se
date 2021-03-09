---
title: Undantags listor för brand vägg för webbaserade program i Azures front dörr – Azure Portal
description: Den här artikeln innehåller information om konfiguration av undantags listor i Azure fram till Azure Portal.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/10/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: a92679bb3114c4a60870424f3ec68a8de7b303da
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102499925"
---
# <a name="web-application-firewall-waf-with-front-door-service-exclusion-lists"></a>Brand vägg för webbaserade program (WAF) med undantags listor för front dörr tjänsten 

Ibland kan brand vägg för webbaserade program (WAF) blockera en begäran som du vill tillåta för ditt program. Active Directory infogar till exempel tokens som används för autentisering. Dessa tokens kan innehålla specialtecken som kan utlösa ett falskt positivt värde från WAF-reglerna. Med undantags listor för WAF kan du utelämna vissa begär ande attribut från en WAF-utvärdering.  En undantags lista kan konfigureras med  [PowerShell](/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject), [Azure CLI](/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/exclusion#ext-front-door-az-network-front-door-waf-policy-managed-rules-exclusion-add), [REST API](/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)eller Azure Portal. I följande exempel visas Azure Portal-konfigurationen. 
## <a name="configure-exclusion-lists-using-the-azure-portal"></a>Konfigurera undantags listor med hjälp av Azure Portal
**Hantera undantag** är tillgängligt från WAF-portalen under **hanterade regler**

![Hantera undantag ](../media/waf-front-door-exclusion/exclusion1.png)
 ![ Hantera exclusion_add](../media/waf-front-door-exclusion/exclusion2.png)

 En exempel lista för undantag: ![ hantera exclusion_define](../media/waf-front-door-exclusion/exclusion3.png)

I det här exemplet exkluderas värdet i fältet *användar* huvud. En giltig begäran kan innehålla det *användar* fält som innehåller en sträng som utlöser en SQL-inmatnings regel. Du kan undanta *användar* parametern i det här fallet så att WAF-regeln inte utvärderar något i fältet.

Följande attribut kan läggas till i undantags listor efter namn. Värdena för de fält som du använder utvärderas inte mot WAF-regler, men deras namn utvärderas. Undantags listorna tar bort kontrollen av fältets värde.

* Namn på begär ande huvud
* Cookie-namn för begäran
* Argument namn för frågesträng
* Begär ande brödtext post argument namn

Du kan ange en exakt matchning av rubrik, brödtext, cookie eller frågesträng för begäran.  Du kan också ange partiella matchningar om du vill. Följande operatorer är de matchnings villkor som stöds:

- **Är lika med**: den här operatorn används för en exakt matchning. Om du till exempel vill välja ett sidhuvud med namnet **bearerToken** använder du operatorn Equals med Selector Set as **bearerToken**.
- **Börjar med**: den här operatorn matchar alla fält som börjar med det angivna värdet för väljaren.
- **Slutar med**: den här operatorn matchar alla begär ande fält som slutar med det angivna väljar värdet.
- **Contains**: den här operatorn matchar alla begär ande fält som innehåller det angivna värdet för väljaren.
- **Lika med alla**: den här operatorn matchar alla begär ande fält. * är väljar värde.

Rubrik-och cookie-namn är Skift läges känsliga.

Om ett rubrik värde, värde för cookie-värde, post argument eller fråge argument ger falska positiva identifieringar för vissa regler, kan du undanta den delen av begäran från att beaktas av regeln:


|matchVariableName från WAF-loggar  |Regel undantag i portalen  |
|---------|---------|
|CookieValue: SOME_NAME        |Namnet på cookien för begäran är lika med SOME_NAME|
|HeaderValue: SOME_NAME        |Namn på begär ande huvud är lika med SOME_NAME|
|PostParamValue: SOME_NAME     |Begär ande brödtext post argument namn är lika med SOME_NAME|
|QueryParamValue: SOME_NAME    |Argument namnet för frågesträngen är lika med SOME_NAME|


Vi stöder för närvarande endast regel undantag för ovanstående matchVariableNames i sina WAF-loggar. För alla andra matchVariableNames måste du antingen inaktivera regler som ger falska positiva identifieringar eller skapa en anpassad regel som uttryckligen tillåter dessa förfrågningar. I synnerhet när matchVariableName är CookieName, huvud, PostParamName eller QueryParamName, innebär det att själva namnet utlöser regeln. Regel undantag har inget stöd för dessa matchVariableNames för tillfället.


Om du undantar en begär ande brödtext post argument som heter *foo*, ska ingen regel Visa POSTPARAMVALUE: foo som matchVariableName i dina WAF-loggar. Du kan dock fortfarande se en regel med matchVariableName-InitialBodyContents som matchar värdet för post-param FOO eftersom post-param-värden är en del av InitialBodyContents.

Du kan använda undantags listor för alla regler i den hanterade regel uppsättningen, för regler för en viss regel grupp eller till en enskild regel som visas i föregående exempel.

## <a name="define-exclusion-based-on-web-application-firewall-logs"></a>Definiera undantag baserat på brand Väggs loggar för webb program
 [Övervakning och loggning av Azure Web Application-brandvägg](waf-front-door-monitor.md) visar matchad information om en blockerad begäran. Om ett värde för rubrik, cookie-värde, post argument eller fråge argument ger falska positiva identifieringar för vissa regler kan du undanta den delen av begäran från att beaktas av regeln. I följande tabell visas exempel värden från WAF-loggar och motsvarande undantags villkor.

|matchVariableName från WAF-loggar    |Regel undantag i portalen|
|--------|------|
|CookieValue: SOME_NAME  |Namnet på cookien för begäran är lika med SOME_NAME|
|HeaderValue: SOME_NAME  |Namn på begär ande huvud är lika med SOME_NAME|
|PostParamValue: SOME_NAME|  Begär ande brödtext post argument namn är lika med SOME_NAME|
|QueryParamValue: SOME_NAME| Argument namnet för frågesträngen är lika med SOME_NAME|


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat dina WAF-inställningar kan du läsa om hur du visar dina WAF-loggar. Mer information finns i [front dörr Diagnostics](../afds/waf-front-door-monitor.md).