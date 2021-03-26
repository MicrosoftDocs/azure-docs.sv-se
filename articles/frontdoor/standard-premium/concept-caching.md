---
title: 'Azure-front dörr: cachelagring'
description: Den här artikeln hjälper dig att förstå hur Azure-dörren är standard/Premium med regler för routning som har aktiverat cachelagring.
services: front-door
author: duongau
manager: KumudD
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 63ea252a4b4c673ae3028adb7ab793ac21fb2e99
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105564593"
---
# <a name="caching-with-azure-front-door-standardpremium-preview"></a>Cachelagring med Azure front dörr standard/Premium (för hands version)

> [!Note]
> Den här dokumentationen gäller för Azure frontend-standard/Premium (för hands version). Letar du efter information om Azures frontend-dörr? Visa [här](../front-door-overview.md).

I den här artikeln får du lära dig hur vägar och regel uppsättningar för front dörrens standard-/Premium funktioner fungerar när du har aktiverat cachelagring. Azures front dörr är en modern Content Delivery Network (CDN) med dynamisk webbplats acceleration och belastnings utjämning.

> [!IMPORTANT]
> Azure front dörr standard/Premium (för hands version) är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="request-methods"></a>Metoder för begäran

Endast metoden GET Request kan generera cachelagrat innehåll i Azures frontend-dörr. Alla andra metoder för begäran är alltid via proxy via nätverket.

## <a name="delivery-of-large-files"></a>Leverans av stora filer

Främre dörren standard/Premium (för hands version) levererar stora filer utan fil storlek. Front dörren använder en teknik som kallas objekt segment. När en stor fil begärs hämtar front dörren mindre delar av filen från ursprunget. När du har tagit emot en filbegäran med full eller byte Range begär den främre dörr miljön filen från ursprungen i segment om 8 MB.

När segmentet har fått till gång till den främre dörren är det cachelagrat och betjänas direkt för användaren. Front dörren sedan för att hämta nästa segment parallellt. Den här för hämtningen garanterar att innehållet förblir ett segment före användaren, vilket minskar svars tiden. Den här processen fortsätter tills hela filen laddas ned (om det krävs) eller klienten stänger anslutningen.

Mer information om byte Range-begäran finns i [RFC 7233](https://web.archive.org/web/20171009165003/http://www.rfc-base.org/rfc-7233.html).
Frontend-dörren cachelagrar alla segment när de tas emot, så att hela filen inte behöver cachelagras i cachen på frontend-dörren. Begär Anden om fil-eller byte-intervallen hanteras från cachen. Om segmenten inte är cachelagrade används för hämtning för att begära segment från Server delen. Den här optimeringen är beroende av ursprungets förmåga att stödja byte intervall begär Anden. Om ursprunget inte stöder byte range-begäranden är den här optimeringen inte effektiv.

## <a name="file-compression"></a>Filkomprimering

Se förbättra prestanda genom att komprimera filer i Azures front dörr.

## <a name="query-string-behavior"></a>Fråga om sträng beteende

Med front dörren kan du styra hur filer cachelagras för en webbegäran som innehåller en frågesträng. I en webbegäran med en frågesträng är frågesträngen den del av begäran som inträffar efter ett frågetecken (?). En frågesträng kan innehålla ett eller flera nyckel/värde-par, där fält namnet och dess värde skiljs åt av ett likhets tecken (=). Varje nyckel/värde-par avgränsas med ett et-tecken (&). Till exempel `http://www.contoso.com/content.mov?field1=value1&field2=value2`. Om det finns fler än ett nyckel/värde-par i en frågesträng i en begäran spelar deras ordning ingen roll.

* **Ignorera frågesträngar**: i det här läget skickar front dörren de frågesträngar från beställaren till ursprunget på den första begäran och cachelagrar till gången. Alla begär Anden om till gången som hanteras från Front dörrens miljö ignorerar frågesträngarna tills den cachelagrade till gången upphör att gälla.

* **Cachelagra varje unik URL**: i det här läget behandlas varje begäran med en unik URL, inklusive frågesträngen, som en unik till gång med sin egen cache. Till exempel, svaret från ursprunget för en begäran om `www.example.ashx?q=test1` cachelagras i front dörrens miljö och returneras för att följa cacheminnen med samma frågesträng. En begäran om `www.example.ashx?q=test2` cachelagras som en separat till gång med en egen tids-till-Live-inställning.
* Du kan också använda regel uppsättning för att ange en **nyckel för frågesträng** för att ta med eller undanta angivna parametrar när cache-nyckeln genereras. Standard nyckeln för cachen är till exempel:/foo/image/asset.html och exempel förfrågan är `https://contoso.com//foo/image/asset.html?language=EN&userid=100&sessionid=200` . Det finns en regel uppsättnings regel för att utesluta frågesträngen UserID. Sedan skulle cache-nyckeln för frågesträngen vara `/foo/image/asset.html?language=EN&sessionid=200` .

## <a name="cache-purge"></a>Rensning av cache

Se rensning av cache.

## <a name="cache-expiration"></a>Förfallo tid för cache
Följande ordning på rubriker används för att avgöra hur länge ett objekt ska lagras i vår cache:</br>
1. Cache-Control: s-MaxAge =\<seconds>
2. Cache-kontroll: max-ålder =\<seconds>
3. Upphör att gälla \<http-date>

Cache-Control svarshuvuden som indikerar att svaret inte cachelagras, t. ex. Cache-Control: Private, Cache-Control: no-cache och Cache-Control: No-Store.  Om det inte finns någon Cache-Control är standard beteendet att klient delen kommer att cachelagra resursen för X-tid. Där X blir slumpmässigt plockat mellan 1 och 3 dagar.

## <a name="request-headers"></a>Begärandehuvuden

Följande begärandehuvuden vidarebefordras inte till ett ursprung när cachelagring används.
* Innehålls längd
* Transfer-Encoding

## <a name="cache-duration"></a>Varaktighet för cache

Varaktigheten för cachen kan konfigureras i regel uppsättningen. Varaktigheten för cachen som angetts via regel uppsättningen är en True cache-åsidosättning. Det innebär att den kommer att använda override-värdet oavsett vad ursprungets svars rubrik är.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [matchnings villkor för regel uppsättning](concept-rule-set-match-conditions.md)
* Läs mer om [regel uppsättnings åtgärder](concept-rule-set-actions.md)