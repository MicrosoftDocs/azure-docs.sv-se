---
title: Begär ande storleks gränser och undantags listor för WebApplication-brandvägg i Azure Application Gateway – Azure Portal
description: Den här artikeln innehåller information om konfiguration av storleks gränser och undantags listor för WebApplication-brandvägg i Application Gateway med Azure Portal.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 02/20/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 2d34641fdecfe334e84347efe1a2f64482cae74b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93040250"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists"></a>Begär ande storleks gränser och undantags listor för WebApplication-brandvägg

Brand väggen för webbaserade program (WAF) i Azure Application Gateway tillhandahåller skydd för webb program. I den här artikeln beskrivs konfiguration av WAF storleks gränser och undantags listor. De här inställningarna finns i WAF-principen som är kopplad till din Application Gateway. Mer information om WAF-principer finns i [brand väggen för Azure-webbprogram på Azure Application Gateway](ag-overview.md) och [skapa brand Väggs principer för webb program för Application Gateway](create-waf-policy-ag.md)

## <a name="waf-exclusion-lists"></a>Undantags listor för WAF

![Storleks begränsningar för begäran](../media/application-gateway-waf-configuration/waf-policy.png)

Med undantags listor för WAF kan du utelämna vissa begär ande attribut från en WAF-utvärdering. Ett vanligt exempel är Active Directory infogade token som används för autentiserings-eller lösen ords fält. Sådana attribut är känsliga att innehålla specialtecken som kan utlösa ett falskt positivt värde från WAF-reglerna. När ett attribut har lagts till i undantags listan för WAF beaktas det inte av någon konfigurerad och aktiv WAF-regel. Undantags listor är globala i definitions området.

Följande attribut kan läggas till i undantags listor efter namn. Värdena i det valda fältet utvärderas inte mot WAF-regler, men deras namn är fortfarande (se exempel 1 nedan) User-Agents huvudets värde undantas från WAF-utvärderingen). Undantags listorna tar bort kontrollen av fältets värde.

* Begärandehuvuden
* Begär cookies
* Attribut för begäran (argument) kan läggas till som ett undantags element, till exempel:

   * Formulär fält namn
   * JSON-entitet
   * Argument för URL-frågesträng

Du kan ange en exakt matchning av rubrik, brödtext, cookie eller frågesträng för begäran.  Du kan också ange partiella matchningar om du vill. Undantags regler är globala inom räckvidd och gäller för alla sidor och alla regler.

Följande är de matchnings villkor som stöds:

- **Är lika med**: den här operatorn används för en exakt matchning. För att välja ett sidhuvud med namnet **bearerToken** använder du till exempel operatorn Equals med Selector Set as **bearerToken**.
- **Börjar med**: den här operatorn matchar alla fält som börjar med det angivna värdet för väljaren.
- **Slutar med**: den här operatorn matchar alla begär ande fält som slutar med det angivna väljar värdet.
- **Contains**: den här operatorn matchar alla begär ande fält som innehåller det angivna värdet för väljaren.
- **Lika med alla**: den här operatorn matchar alla begär ande fält. * är väljar värde.

I samtliga fall är Skift läges okänsligt och reguljärt uttryck tillåts inte som väljare.

> [!NOTE]
> Mer information och fel söknings hjälp finns i [fel sökning av WAF](web-application-firewall-troubleshoot.md).

### <a name="examples"></a>Exempel

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

I följande exempel demonstreras användningen av undantag.

#### <a name="example-1"></a>Exempel 1

I det här exemplet vill du undanta huvudet för användar agenten. Rubriken för begäran om användar Agent innehåller en egenskaps sträng som tillåter att nätverks protokollets peer-datorer kan identifiera program typ, operativ system, program varu leverantör eller program varu version för den begärda program varans användar agent. Mer information finns i [User-Agent](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent).

Det kan finnas flera skäl att inaktivera utvärderingen av rubriken. Det kan finnas en sträng som WAF ser och antar att den är skadlig. Till exempel den klassiska SQL-attacken "x = x" i en sträng. I vissa fall kan detta vara legitim trafik. Du kan behöva undanta den här rubriken från WAF-utvärderingen.

Följande Azure PowerShell-cmdlet exkluderar användar agentens huvud från utvärderingen:

```azurepowershell
$exclusion1 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestHeaderNames" `
   -SelectorMatchOperator "Equals" `
   -Selector "User-Agent"
```
#### <a name="example-2"></a>Exempel 2

I det här exemplet exkluderas värdet i den *användar* parameter som skickas i begäran via URL: en. Anta till exempel att det är vanligt i din miljö för fältet användare att innehålla en sträng som WAF visar som skadligt innehåll, så att den blockerar den.  Du kan undanta användar parametern i det här fallet så att WAF inte utvärderar något i fältet.

Följande Azure PowerShell-cmdlet exkluderar användar parametern från utvärderingen:

```azurepowershell
$exclusion2 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestArgNames" `
   -SelectorMatchOperator "StartsWith" `
   -Selector "user"
```
Så om URL: en `http://www.contoso.com/?user%281%29=fdafdasfda` skickas till WAF utvärderas inte strängen **fdafdasfda**, men den kommer fortfarande att utvärdera parameter namn **användaren %281 %29**. 

## <a name="waf-request-size-limits"></a>Storleks gränser för WAF-begäran



Med brand vägg för webbaserade program kan du konfigurera storleks gränser för förfrågningar inom nedre och övre gränser. Det finns följande två storleks gränser för konfigurationer:

- Fältet Max storlek för begäran har angetts i kilobyte och kontrollerar den allmänna storleks gränsen för förfrågningar exklusive eventuella fil överföringar. Det här fältet har ett minsta värde på 1 KB och ett max värde på 128 KB. Standardvärdet för brödtext i begär ande storlek är 128 KB.
- Fältet för fil överförings gräns anges i MB och styr den högsta tillåtna storleken för fil uppladdning. Det här fältet kan ha ett minimivärde på 1 MB och följande max värden:

   - 100 MB för v1 mellan WAF-gatewayer
   - 500 MB för v1 stora WAF-gatewayer
   - 750 MB för v2 WAF-gatewayer 

 Standardvärdet för fil överförings gränsen är 100 MB.

WAF erbjuder också en konfigurerbar ratt för att aktivera eller inaktivera kontroll av begär ande brödtext. Som standard har granskning av begär ande texten Aktiver ATS. Om granskning av begär ande texten är inaktive rad utvärderas inte WAF innehåll i HTTP-meddelandets brödtext. I sådana fall fortsätter WAF att använda WAF-regler på huvuden, cookies och URI. Om granskningen av begär ande texten är inaktive rad är Max storleks fältet för begäran inte tillämpligt och kan inte anges. Om du inaktiverar granskning av begär ande brödtext kan meddelanden som är större än 128 KB skickas till WAF, men meddelande texten inspekteras inte för sårbarheter.

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat dina WAF-inställningar kan du lära dig hur du visar dina WAF-loggar. Mer information finns i [Application Gateway Diagnostics](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging).
