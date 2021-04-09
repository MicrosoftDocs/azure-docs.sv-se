---
title: Använda Azures främre dörr standard/Premium med resurs delning mellan ursprung
description: Lär dig hur du använder Azures frontend-dörr (AFD) med resurs delning mellan ursprung (CORS).
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: ee8f19aca62d2e331fcf59551d47c2dac93783b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101099372"
---
# <a name="using-azure-front-door-standardpremium-with-cross-origin-resource-sharing-cors"></a>Använda Azures främre dörr standard/Premium med resurs delning mellan ursprung (CORS)

> [!Note]
> Den här dokumentationen gäller för Azure frontend-standard/Premium (för hands version). Letar du efter information om Azures frontend-dörr? Visa [här](../front-door-overview.md).

## <a name="what-is-cors"></a>Vad är CORS?

CORS (Cross Origin Resource Sharing) är en HTTP-funktion som gör det möjligt för ett webbprogram som körs i en domän att komma åt resurser i en annan domän. För att minska risken för skript angrepp över flera webbplatser, implementerar alla moderna webbläsare en säkerhets begränsning som kallas [samma-Origin-princip](https://www.w3.org/Security/wiki/Same_Origin_Policy). Detta förhindrar att en webb sida anropar API: er i en annan domän.  CORS är ett säkert sätt att tillåta ett ursprung (ursprungs domänen) att anropa API: er i ett annat ursprung.

> [!IMPORTANT]
> Azure front dörr standard/Premium (för hands version) är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-it-works"></a>Så här fungerar det

Det finns två typer av CORS-begäranden, *enkla begär Anden* och *komplexa begär Anden.*

### <a name="for-simple-requests"></a>För enkla begär Anden:

1. Webbläsaren skickar CORS-begäran med en annan **källa** för http-begäran. Värdet för den här rubriken är ursprunget som hanterade den överordnade sidan, som definieras som kombinationen av *protokoll,* *domän* och *port.*  När en sida från https \: //www.contoso.com försöker komma åt en användares data i Fabrikam.com ursprung, skickas följande begär ande huvud till fabrikam.com:

   `Origin: https://www.contoso.com`

2. Servern kan svara med något av följande:

   * En **Access-Control-Allow-Origin** -rubrik i sitt svar som anger vilken ursprungs plats som tillåts. Exempel:

     `Access-Control-Allow-Origin: https://www.contoso.com`

   * En HTTP-felkod, till exempel 403, om servern inte tillåter Cross-Origin-begäran efter kontroll av käll rubriken

   * En **Access-Control-Allow-Origin** -rubrik med ett jokertecken som tillåter alla ursprung:

     `Access-Control-Allow-Origin: *`

### <a name="for-complex-requests"></a>För komplexa begär Anden:

En komplex begäran är en CORS-begäran där webbläsaren krävs för att skicka en *preflight-begäran* (det vill säga en preliminär avsökning) innan den aktuella CORS-begäran skickas. Preflight-begäran ber Server behörigheten om den ursprungliga CORS-begäran kan fortsätta och är en `OPTIONS` begäran till samma URL.

> [!TIP]
> Mer information om CORS-flöden och vanliga fall GRO par finns i [guiden till CORS för REST-API: er](https://www.moesif.com/blog/technical/cors/Authoritative-Guide-to-CORS-Cross-Origin-Resource-Sharing-for-REST-APIs/).
>
>

## <a name="wildcard-or-single-origin-scenarios"></a>Scenarier med jokertecken eller enstaka ursprung
CORS på Azures front dörr fungerar automatiskt utan extra konfiguration när rubriken **Access-Control-Allow-Origin** är inställd på jokertecken (*) eller ett enda ursprung.  Azures front dörr cachelagrar det första svaret och den kommande begäran kommer att använda samma rubrik.

Om förfrågningar redan har gjorts till Azures front dörr innan CORS har angetts för ditt ursprung måste du rensa innehållet i slut punkts innehållet för att läsa in innehållet igen med **Access-Control-Allow-Origin-** rubriken.

## <a name="multiple-origin-scenarios"></a>Scenarier med flera ursprung
Om du behöver tillåta en speciell lista över ursprung som ska tillåtas för CORS kan saker bli lite mer komplicerat. Problemet uppstår när CDN cachelagrar rubriken **Access-Control-Allow-Origin** för det första CORS-ursprunget.  När ett annat CORS-ursprung gör en annan begäran, kommer CDN att betjäna det cachelagrade **Access-Control-Allow-Origin** -huvudet, som inte matchar. Det finns flera sätt att åtgärda det här problemet.

### <a name="azure-front-door-rule-set"></a>Regel uppsättning för Azures front dörr

På Azures front dörr kan du skapa en regel i [reglerna](concept-rule-set.md) för Azures frontend-dörr för att kontrol lera **käll** rubriken på begäran. Om det är ett giltigt ursprung anger din regel **Access-Control-Allow-Origin-** huvudet med rätt värde. I det här fallet ignoreras rubriken för **Access-Control-Allow-Origin** från filens ursprungs Server och AFD-regel motorn hanterar de tillåtna CORS-ursprungen fullständigt.

:::image type="content" source="../media/troubleshooting-cross-origin-resource-sharing/cross-origin-resource.png" alt-text="Skärm bild av regel exempel med regel uppsättning.":::

> [!TIP]
> Du kan lägga till ytterligare åtgärder i regeln för att ändra ytterligare svarshuvuden, till exempel **Access-Control-Allow-Methods**.
> 

## <a name="next-steps"></a>Nästa steg

* Läs hur du [skapar en Front Door](create-front-door-portal.md).
