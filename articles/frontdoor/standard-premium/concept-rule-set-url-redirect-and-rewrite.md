---
title: URL-omdirigering och URL-omskrivning med Azure front dörr standard/Premium (för hands version)
description: Den här artikeln hjälper dig att förstå hur Azures front dörr stöder URL-omdirigering och URL-omskrivning med Azures frontend-regel uppsättning.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 382a4c040c7a519462ee3e35119b9471031e0724
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "101100501"
---
# <a name="url-redirect-and-url-rewrite-with-azure-front-door-standardpremium-preview"></a>URL-omdirigering och URL-omskrivning med Azure front dörr standard/Premium (för hands version)

> [!Note]
> Den här dokumentationen gäller för Azure frontend-standard/Premium (för hands version). Letar du efter information om Azures frontend-dörr? Visa [här](../front-door-overview.md).

Den här artikeln hjälper dig att förstå hur Azure-front dörren standard/Premium stöder URL-omdirigering och URL-omskrivning som används i en regel uppsättning.

> [!IMPORTANT]
> Azure front dörr standard/Premium (för hands version) är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="url-redirect"></a>URL-omdirigering

Azures frontend-dörr kan omdirigera trafik på var och en av följande nivåer: protokoll, värdnamn, sökväg, frågesträng och fragment. Dessa funktioner kan konfigureras för enskilda Micro-tjänster eftersom omdirigeringen är sökväg-baserad. Med URL-omdirigering kan du förenkla program konfigurationen genom att optimera resursanvändningen och har stöd för nya omdirigerings scenarier, inklusive global och Sök vägs baserad omdirigering.

Du kan konfigurera URL-omdirigering via regel uppsättning.

:::image type="content" source="../media/concept-url-redirect-and-rewrite/front-door-url-redirect.png" alt-text="Skärm bild av att skapa URL-omdirigering med regel uppsättning." lightbox="../media/concept-url-redirect-and-rewrite/front-door-url-redirect-expanded.png":::

### <a name="redirection-types"></a>Typer av omdirigering
En typ av omdirigering anger svars status koden för klienterna för att förstå syftet med omdirigeringen. Följande typer av omdirigering stöds:

* **301 (flyttad permanent)**: anger att mål resursen har tilldelats en ny permanent URI. Alla framtida referenser till den här resursen kommer att använda en av de omslutna URI: erna. Använd 301 status kod för omdirigering av HTTP till HTTPS.
* **302 (hittades)**: anger att mål resursen är tillfälligt under en annan URI. Eftersom omdirigeringen kan ändras vid ett tillfälle bör klienten fortsätta att använda den effektiva begär ande-URI: n för framtida begär Anden.
* **307 (tillfällig omdirigering)**: anger att mål resursen är tillfälligt under en annan URI. Användar agenten får inte ändra metoden Request om den gör en automatisk omdirigering till denna URI. Eftersom omdirigeringen kan ändras med tiden bör klienten fortsätta att använda den ursprungliga effektiva begär ande-URI: n för framtida begär Anden.
* **308 (permanent omdirigering)**: anger att mål resursen har tilldelats en ny permanent URI. Alla framtida referenser till den här resursen bör använda en av de omslutna URI: erna.

### <a name="redirection-protocol"></a>Protokoll för omdirigering
Du kan ange det protokoll som ska användas för omdirigering. De vanligaste användnings fallen för funktionen omdirigera är att ställa in HTTP till HTTPS-omdirigering.

* **Endast https**: Ange protokollet till endast https, om du vill dirigera om trafiken från http till https. Azures front dörr rekommenderar att du alltid ställer in omdirigeringen till endast HTTPS.
* **Endast http**: omdirigerar inkommande begäran till http. Använd bara det här värdet om du vill behålla ditt trafik-HTTP-värde som inte är krypterat.
* **Matchnings förfrågan**: det här alternativet behåller protokollet som används av den inkommande begäran. En HTTP-begäran är alltså fortfarande HTTP och en HTTPS-begäran är kvar som omdirigering av HTTPS-post.

### <a name="destination-host"></a>Målvärd
Som en del av konfigureringen av en Omdirigerad routning kan du också ändra värdnamn eller domän för omdirigerings förfrågan. Du kan ange det här fältet om du vill ändra värd namnet i URL: en för omdirigeringen eller på annat sätt bevara värd namnet från den inkommande begäran. Därför kan du använda det här fältet för att dirigera om alla förfrågningar `https://www.contoso.com/*` som skickas till `https://www.fabrikam.com/*` .

### <a name="destination-path"></a>Målsökväg
I de fall där du vill ersätta Sök vägs segmentet för en URL som en del av omdirigeringen kan du ange det här fältet med värdet för ny sökväg. Annars kan du välja att bevara värdet för sökvägen som en del av omdirigeringen. Därför kan du använda det här fältet för att omdirigera alla begär Anden som skickas till `https://www.contoso.com/\*` till  `https://www.contoso.com/redirected-site` .

### <a name="query-string-parameters"></a>Parametrar för frågesträng
Du kan även ersätta Frågesträngens parametrar i den omdirigerade URL: en. Om du vill ersätta en befintlig frågesträng från URL: en för inkommande begäran anger du fältet till Ersätt och anger sedan lämpligt värde. Annars kan du behålla den ursprungliga uppsättningen frågesträngar genom att ange fältet till "Preserve". Som exempel kan du använda det här fältet för att omdirigera all trafik som skickas till `https://www.contoso.com/foo/bar` till `https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F` . 

### <a name="destination-fragment"></a>Målcachen
Målcachen är den del av URL: en som finns efter "#", som används av webbläsaren för att använda en viss del av en webb sida. Du kan ange det här fältet om du vill lägga till ett fragment i omdirigerings-URL: en.

## <a name="url-rewrite"></a>URL-omskrivning

Azures front dörr stöder URL-omskrivning för att skriva om sökvägen för en begäran som är en väg till ditt ursprung. Med URL-omskrivning kan du lägga till villkor för att se till att URL: en eller de angivna rubrikerna bara får skrivas om när vissa villkor uppfylls. Dessa villkor baseras på informationen för begäran och svar.

Med den här funktionen kan du omdirigera användare till olika ursprung baserat på scenario, enhets typ och typ av begärd fil.

Du kan konfigurera URL-omdirigering via regel uppsättning.

:::image type="content" source="../media/concept-url-redirect-and-rewrite/front-door-url-rewrite.png" alt-text="Skärm bild av skapa URL-omskrivning med regel uppsättning." lightbox="../media/concept-url-redirect-and-rewrite/front-door-url-rewrite-expanded.png":::

### <a name="source-pattern"></a>Käll mönster

Käll mönster är URL-sökvägen i käll förfrågan som ska ersättas. För närvarande använder käll mönstret en prefix-baserad matchning. Om du vill matcha alla URL-sökvägar använder du ett snedstreck (/) som käll mönster värde.

### <a name="destination"></a>Mål

Du kan definiera mål Sök vägen som ska användas vid omskrivning. Mål Sök vägen skriver över käll mönstret.

### <a name="preserve-unmatched-path"></a>Bevara omatchad sökväg

Bevara omatchad sökväg låter dig lägga till den återstående sökvägen efter käll mönstret till den nya sökvägen.

Om jag till exempel anger **bevara omatchad sökväg till Ja**.
* Om den inkommande begäran är `www.contoso.com/sub/1.jpg` , ställs käll mönstret in på `/` , mål hämtningen ställs in på `/foo/` och innehållet hämtas från `/foo/sub/1` . jpg från ursprunget.

* Om den inkommande begäran är `www.contoso.com/sub/image/1.jpg` , ställs käll mönstret in på `/sub/` , mål hämtnings uppsättningen till `/foo/` , innehållet hämtas från `/foo/image/1.jpg` ursprunget.

Om jag till exempel anger **bevara omatchad sökväg till Nej**.
* Om den inkommande begäran är är `www.contoso.com/sub/image/1.jpg` käll mönstret inställt på `/sub/` , mål hämtningen till `/foo/2.jpg` , kommer innehållet alltid att betjänas från `/foo/2.jpg` ursprunget oavsett vilka sökvägar som följs `wwww.contoso.com/sub/` .

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [Standard-/Premium regel uppsättningen för Azures front dörr](concept-rule-set.md).
