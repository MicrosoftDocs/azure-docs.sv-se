---
title: 'Azure-front dörr: regel uppsättning'
description: Den här artikeln innehåller en översikt över regel uppsättnings funktionen för standard/Premium i Azure-dörren.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 8e6ceebc9e92dabe66baeb9aeff0ae9692e2bdad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101100488"
---
# <a name="what-is-a-rule-set-for-azure-front-door-standardpremium-preview"></a>Vad är en regel uppsättning för Azure front dörr standard/Premium (för hands version)?

> [!Note]
> Den här dokumentationen gäller för Azure frontend-standard/Premium (för hands version). Letar du efter information om Azures frontend-dörr? Visa [här](../front-door-overview.md).

En regel uppsättning är en anpassad regel motor som grupperar en kombination av regler i en enda uppsättning som du kan associera med flera vägar. Med regel uppsättningen kan du anpassa hur begär Anden ska bearbetas i kanten och hur Azures front dörr hanterar dessa förfrågningar.

> [!IMPORTANT]
> Azure front dörr standard/Premium (för hands version) är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="common-supported-scenarios"></a>Vanliga scenarier som stöds

* Implementera säkerhets rubriker för att förhindra webbläsarbaserade sårbarheter som HTTP Strict-Transport-Security (HSTS), X-XSS-Protection, Content-Security-Policy, X-Frame-Options och Access-Control-Allow-Origin-huvuden för cross-origin Resource Sharing (CORS)-scenarier. Säkerhetsbaserade attribut kan också definieras med cookies.

* Dirigera begär anden till mobila eller Station ära versioner av programmet baserat på klientens enhets typ.

* Att använda omdirigerings funktioner för att returnera 301, 302, 307 och 308 omdirigerar till klienten för att dirigera dem till nya värdnamn, sökvägar, frågesträngar eller protokoll.

* Ändra konfigurationen för cachelagring för din väg dynamiskt baserat på inkommande begär Anden.

* Skriv sökvägen till URL: en för begäran och vidarebefordra begäran till rätt ursprung i den konfigurerade ursprungs gruppen.

* Lägg till, ändra eller ta bort begäran/svars huvud för att dölja känslig information eller samla in viktig information via huvuden.

* Stöd för servervariabler för att dynamiskt ändra begär ande-/svarshuvuden eller URL-omskrivna sökvägar/frågesträngar, till exempel när en ny sid inläsning eller när ett formulär publiceras. Server variabeln stöds för närvarande endast för **[regel uppsättnings åtgärder](concept-rule-set-actions.md)** .

## <a name="architecture"></a>Arkitektur

Regel uppsättningen hanterar begär Anden vid gränsen. När en begäran anländer till din Azures främre dörr standard/Premium-slutpunkt körs WAF först, följt av inställningarna som kon figurer ATS i Route. De här inställningarna inkluderar regel uppsättningen som är kopplad till vägen. Regel uppsättningar bearbetas uppifrån och ned i vägen. Samma gäller för regler inom en regel uppsättning. För att alla åtgärder i varje regel ska kunna utföras måste alla matchnings villkor i en regel vara uppfyllda. Om en begäran inte matchar något av villkoren i din regel uppsättnings konfiguration utförs endast konfigurationer i väg.

Om alternativet **stoppa utvärdering av återstående regler** är markerat körs inte alla återstående regel uppsättningar som är associerade med vägen.  

### <a name="example"></a>Exempel

I följande diagram körs WAF-principer först. En regel uppsättning konfigureras för att lägga till ett svars huvud. Sedan ändrar rubriken max ålder för cache-kontrollen om matchnings villkoret uppfylls.

:::image type="content" source="../media/concept-rule-set/front-door-rule-set-architecture-1.png" alt-text="Diagram som visar arkitekturen för regel uppsättning." lightbox="../media/concept-rule-set/front-door-rule-set-architecture-1-expanded.png":::

## <a name="terminology"></a>Terminologi

Med regel uppsättningen för Azures front dörr kan du skapa en kombination av regel uppsättnings konfiguration, som var och en består av en uppsättning regler. Följande rader är några användbara terminologier som du kommer att komma åt när du konfigurerar regel uppsättningen.

Mer kvot gräns finns i [prenumerations-och tjänst begränsningar, kvoter och begränsningar för Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md).

* *Regel uppsättning*: en uppsättning regler som associeras med en eller flera [vägar](concept-route.md). Varje konfiguration är begränsad till 25 regler. Du kan skapa upp till 10 konfigurationer.

* *Regel uppsättnings regel*: en regel som består av upp till 10 matchnings villkor och 5 åtgärder. Regler är lokala i en regel uppsättning och kan inte exporteras för användning i regel uppsättningar. Användare kan skapa samma regel i flera regel uppsättningar.

* *Matchnings villkor*: det finns många matchnings villkor som kan användas för att analysera inkommande begär Anden. En regel kan innehålla upp till 10 matchnings villkor. Matchnings villkor utvärderas med operatorn **och** . *Reguljärt uttryck stöds i villkor*. En fullständig lista över matchnings villkor finns i [villkoret regel uppsättning](concept-rule-set-match-conditions.md).

* *Åtgärd*: åtgärder styr hur AFD hanterar inkommande begär Anden utifrån matchnings villkoren. Du kan ändra cachelagring-beteenden, ändra begärandehuvuden/svarshuvuden, göra URL-omskrivning och omdirigering av URL. *Servervariabler stöds på åtgärd*. En regel kan innehålla upp till 10 matchnings villkor. En fullständig lista med åtgärder hittar du i [regel uppsättnings åtgärder](concept-rule-set-actions.md).

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [skapar en front dörrs standard/Premium](create-front-door-portal.md).
* Lär dig hur du konfigurerar din första [regel uppsättning](how-to-configure-rule-set.md).
 
