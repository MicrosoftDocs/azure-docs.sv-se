---
title: Anpassad regel för brand vägg för webbaserade program för Azures front dörr
description: Lär dig att använda anpassade regler för brand vägg för webbaserade program (WAF) som skyddar dina webb program mot skadliga attacker.
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 09/05/2019
ms.author: victorh
ms.openlocfilehash: 158bfe30bf48ee420be8efb9ff32fff0e555d9e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "79475832"
---
#  <a name="custom-rules-for-web-application-firewall-with-azure-front-door"></a>Anpassade regler för brand vägg för webb program med Azures front dörr

Med hjälp av Azure WebApplication-brandväggen (WAF) med front dörr kan du styra åtkomsten till dina webb program baserat på de villkor som du definierar. En anpassad WAF-regel består av ett prioritets nummer, regel typ, matchnings villkor och en åtgärd. Det finns två typer av anpassade regler: matchnings regler och hastighets begränsnings regler. En matchnings regel styr åtkomst baserat på en uppsättning matchnings villkor medan en hastighets begränsnings regel styr åtkomst baserat på matchnings villkor och frekvensen av inkommande begär Anden. Du kan inaktivera en anpassad regel för att förhindra att den utvärderas, men behåll konfigurationen fortfarande. 

## <a name="priority-match-conditions-and-action-types"></a>Prioritet, matchnings villkor och åtgärds typer

Du kan kontrol lera åtkomst med en anpassad WAf-regel som definierar ett prioritets nummer, en regeltyp, en matris med matchnings villkor och en åtgärd. 

- **Prioritet:** är ett unikt heltal som beskriver ordningen för utvärdering av WAF-regler. Regler med lägre prioritets värden utvärderas före regler med högre värden. Prioritets nummer måste vara unika bland alla anpassade regler.

- **Åtgärd:** definierar hur en begäran ska dirigeras om en WAF-regel matchas. Du kan välja en av nedanstående åtgärder som ska tillämpas när en begäran matchar en anpassad regel.

    - *Allow* -WAF vidarebefordrar fråge änden till Server delen och loggar en post i WAF-loggar och avslutas.
    - *Blockering av* begäran blockeras, WAF skickar svar till klienten utan att vidarebefordra begäran till Server delen. WAF loggar en post i WAF-loggar.
    - *Log* -WAF loggar en post i WAF-loggar och fortsätter att utvärdera nästa regel.
    - *Redirect* -WAF omdirigerar begäran till en angiven URI, loggar en post i WAF-loggar och avslutas.

- **Matchnings villkor:** definierar en match variabel, en operator och ett matchnings värde. Varje regel kan innehålla flera matchnings villkor. Ett matchnings villkor kan baseras på Geo-plats, klient-IP-adresser (CIDR), storlek eller sträng matchning. Sträng matchning kan vara mot en lista över matchande variabler.
  - **Matcha variabel:**
    - RequestMethod
    - QueryString
    - PostArgs
    - RequestUri
    - RequestHeader
    - RequestBody
    - Cookies
  - **Operator**
    - Any: används ofta för att definiera standard åtgärder om inga regler matchas. Alla är en match alla-operator.
    - Lika med
    - Innehåller
    - LessThan: storleks begränsning
    - GreaterThan: storleks begränsning
    - LessThanOrEqual: storleks begränsning
    - GreaterThanOrEqual: storleks begränsning
    - BeginsWith
    - EndsWith
    - Verifiering
  
  - **Regex** stöder inte följande åtgärder: 
    - Referens och inhämtning av under uttryck
    - Godtyckliga kontroller med noll bredd
    - Subrutin-referenser och rekursiva mönster
    - Villkors mönster
    - Bakspårning-verb
    - Single-byte-direktivet \c
    - Direktivet \Rs rad träff
    - \K början av matchnings reset-direktivet
    - Bild texter och inbäddad kod
    - Atomiska grupperingar och possessive-kvantifierare

  - **Negation [valfritt]:** Du kan ställa in *negationen* på sant om resultatet av ett villkor ska vara negationt.
      
  - **Transformering [valfritt]:** En lista med strängar med namn på omvandlingar som ska utföras innan matchningen görs. Dessa omvandlingar kan vara följande:
     - Vers 
     - Gemener
     - Trim
     - RemoveNulls
     - UrlDecode
     - UrlEncode
     
   - **Matchnings värde:** Värden för HTTP-begäran som stöds är:
     - GET
     - POST
     - PUT
     - HEAD
     - DELETE
     - SKRIVLÅS
     - OLÅST
     - PROFIL
     - SÄTT
     - PROPFIND
     - PROPPATCH
     - MKCOL
     - EXEMPLAR
     - FART

## <a name="examples"></a>Exempel

### <a name="waf-custom-rules-example-based-on-http-parameters"></a>WAF anpassade regel exempel baserat på http-parametrar

Här är ett exempel som visar konfigurationen av en anpassad regel med två matchnings villkor. Begär Anden kommer från en angiven plats som definieras av referent, och frågesträngen innehåller inte "Password".

```
# http rules example
{
  "name": "AllowFromTrustedSites",
  "priority": 1,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestHeader",
      "selector": "Referer",
      "operator": "Equal",
      "negateCondition": false,
      "matchValue": [
        "www.mytrustedsites.com/referpage.html"
      ]
    },
    {
      "matchVariable": "QueryString",
      "operator": "Contains",
      "matchValue": [
        "password"
      ],
      "negateCondition": true
    }
  ],
  "action": "Allow",
  "transforms": []
}

```
En exempel konfiguration för blockering av "placering"-metoden visas som nedan:

``` 
# http Request Method custom rules
{
  "name": "BlockPUT",
  "priority": 2,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestMethod",
      "selector": null,
      "operator": "Equal",
      "negateCondition": false,
      "matchValue": [
        "PUT"
      ]
    }
  ],
  "action": "Block",
  "transforms": []
}
```

### <a name="size-constraint"></a>Storleks begränsning

Du kan bygga en anpassad regel som anger storleks begränsningen för en del av en inkommande begäran. Till exempel blockerar regel en URL som är längre än 100 tecken.

```
# http parameters size constraint
{
  "name": "URLOver100",
  "priority": 5,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestUri",
      "selector": null,
      "operator": "GreaterThanOrEqual",
      "negateCondition": false,
      "matchValue": [
        "100"
      ]
    }
  ],
  "action": "Block",
  "transforms": []
}
```

## <a name="next-steps"></a>Nästa steg
- [Konfigurera en brand Väggs princip för webb program med hjälp av Azure PowerShell](waf-front-door-custom-rules-powershell.md) 
- Lär dig mer om [brand vägg för webbaserade program med front dörr](afds-overview.md)
- Läs hur du [skapar en Front Door](../../frontdoor/quickstart-create-front-door.md).

