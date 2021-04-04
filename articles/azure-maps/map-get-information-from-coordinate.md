---
title: Visa information om en koordinat på en karta | Microsoft Azure Maps
description: Lär dig hur du visar information om en adress på kartan när en användare väljer en koordinat.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 31e4004379340912051204786da592fe33a5bd63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92890759"
---
# <a name="get-information-from-a-coordinate"></a>Hämta information från en koordinat

Den här artikeln visar hur du gör en omvänd adresss ökning som visar adressen till en klickad popup-plats.

Det finns två sätt att göra en omvänd adresss ökning. Ett sätt är att fråga [Azure Maps API för omvänd adresss ökning](/rest/api/maps/search/getsearchaddressreverse) via en service-modul. Det andra sättet är att använda [API:](https://fetch.spec.whatwg.org/) et för att skapa en begäran till [Azure Maps omvänd Address Search-API](/rest/api/maps/search/getsearchaddressreverse) för att hitta en adress. Båda sätten undersökts nedan.

## <a name="make-a-reverse-search-request-via-service-module"></a>Gör en omvänd search-begäran via service module

<iframe height='500' scrolling='no' title='Hämta information från en koordinat (service Module)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>Hämta information från en koordinat (service Module)</a> genom Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) i <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan konstruerar det första blocket ett kart objekt och anger autentiseringsmekanismen för att använda åtkomsttoken. Du kan se [skapa en karta](./map-create.md) för instruktioner.

Det andra kod blocket skapar ett `TokenCredential` för att autentisera HTTP-begäranden för att Azure Maps med åtkomsttoken. Den skickar sedan `TokenCredential` till `atlas.service.MapsURL.newPipeline()` och skapar en [pipeline](/javascript/api/azure-maps-rest/atlas.service.pipeline) -instans. `searchURL`Visar en URL som Azure Maps [Sök](/rest/api/maps/search) åtgärder.

Det tredje kod blocket uppdaterar stilen för mus markören till en pekare och skapar ett [popup](/javascript/api/azure-maps-control/atlas.popup#open) -objekt. Du kan se [Lägg till ett popup-fönster på kartan](./map-add-popup.md) för instruktioner.

Det fjärde blocket kod lägger till en mus klicknings [händelse lyssnare](/javascript/api/azure-maps-control/atlas.map#events). När den utlöses skapas en Sök fråga med koordinaterna för den klickade punkten. Den använder sedan metoden [getSearchAddressReverse](/javascript/api/azure-maps-rest/atlas.service.searchurl#searchaddressreverse-aborter--geojson-position--searchaddressreverseoptions-)för att skicka frågor till kommandot [Get search Address reversed API](/rest/api/maps/search/getsearchaddressreverse) för adresserna för koordinaterna. En insamling av en interjson-funktion extraheras sedan med hjälp av `geojson.getFeatures()` metoden från svaret.

Det femte blocket med kod konfigurerar HTML-popup-innehållet för att Visa svars adressen för den klickade koordinatens position.

Ändringen av markör, popup-objektet och händelsen Klickning skapas i kartans [inläsnings händelse lyssnare](/javascript/api/azure-maps-control/atlas.map#events). Den här kod strukturen säkerställer att kartan laddas fullständigt innan du hämtar koordinaternas information.

## <a name="make-a-reverse-search-request-via-fetch-api"></a>Gör en omvänd search-begäran via Hämta API

Klicka på kartan för att göra en omvänd polycode-begäran för den platsen med Hämta.

<iframe height='500' scrolling='no' title='Hämta information från en koordinat' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>Hämta information från en koordinat</a> efter Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan konstruerar det första blocket kod ett kart objekt och ställer in autentiseringsmekanismen för att använda åtkomsttoken. Du kan se [skapa en karta](./map-create.md) för instruktioner.

Det andra blocket kod uppdaterar stilen för mus markören till en visare. Den instansierar ett [popup](/javascript/api/azure-maps-control/atlas.popup#open) -objekt. Du kan se [Lägg till ett popup-fönster på kartan](./map-add-popup.md) för instruktioner.

Det tredje blocket kod lägger till en händelse lyssnare för mus klickningar. När du klickar på musen används [hämtnings-API](https://fetch.spec.whatwg.org/) : t för att fråga [Azure Maps API för omvänd adresss ökning](/rest/api/maps/search/getsearchaddressreverse) för den klickade koordinatens adress. För ett lyckat svar samlar det in adressen för den klickade platsen. Den definierar popup-innehåll och placering med hjälp av [setOptions](/javascript/api/azure-maps-control/atlas.popup#setoptions-popupoptions-) -funktionen i popup-klassen.

Ändringen av markör, popup-objektet och händelsen Klickning skapas i kartans [inläsnings händelse lyssnare](/javascript/api/azure-maps-control/atlas.map#events). Den här kod strukturen säkerställer att kartan laddas fullständigt innan du hämtar koordinaternas information.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Metod tips för att använda Sök tjänsten](how-to-use-best-practices-for-search.md)

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Karta](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [Popup](/javascript/api/azure-maps-control/atlas.popup)

I följande artiklar finns fullständiga kod exempel:

> [!div class="nextstepaction"]
> [Visa anvisningar från A till B](./map-route.md)

> [!div class="nextstepaction"]
> [Visa trafik](./map-show-traffic.md)