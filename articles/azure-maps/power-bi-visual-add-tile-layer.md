---
title: Lägga till ett panel lager i Azure Maps Power BI visuella objektet | Microsoft Azure Maps
description: I den här artikeln får du lära dig hur du använder panel lagret i Microsoft Azure Maps-visualisering för Power BI.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 6ab99f32932f39d5ad140b7a16d16ceae30fff54
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92896216"
---
# <a name="add-a-tile-layer"></a>Lägga till ett panelskikt

Funktionen panel lager, till exempel funktionen referens skikt, gör att ytterligare data kan skrivas över på kartan för att ge mer sammanhang. Med panel lager kan du placera bilder ovanpå Azure Maps grundläggande kart paneler. Detta är ett bra sätt att täcka stora eller komplexa data uppsättningar, till exempel bilder från drönare eller miljon tals data rader.

> [!div class="mx-imgBorder"]
> ![En karta som visar ett bubbeldiagram ovanför ett panel lager som visar aktuella IR-väder data från Azure Maps](media/power-bi-visual/radar-tile-layer-with-bubbles.png)

Ett panel lager läses in i paneler från en server. Dessa bilder kan antingen förrenderas eller renderas dynamiskt. Förrenderade bilder lagras som en annan bild på en server med hjälp av en namngivnings konvention som panel lagret förstår. Dynamiskt återgivna bilder använder en tjänst för att läsa in bilderna nära real tid. Panel lager är ett bra sätt att visualisera stora data uppsättningar på kartan. Om du inte bara vill att ett panel lager ska genereras från en bild kan vektor data även återges som ett panel lager.

Begränsnings rutan och zoomnings intervallet där en panel tjänst är tillgänglig kan skickas som inställningar för att begränsa var paneler begärs. Detta är både prestanda förbättring för både visualiserings-och panel tjänsten. Nedan visas en översikt över alla inställningar som är tillgängliga i fönstret **format** som är tillgängliga i avsnittet **panel lager** .

| Inställning        | Beskrivning   |
|----------------|---------------|
| URL            | En formaterad URL som pekar på en panel tjänst.  |
| Panelstorlek      | Ett heltals värde som anger rutornas bredd och höjd-mått.   |
| Norra, obunden    | Norra latitud för den avgränsnings ruta där paneler är tillgängliga. |
| Södra gränser    | Södra latitud för den avgränsnings ruta där paneler är tillgängliga. |
| Östra gränser     | Östra longitud för den avgränsnings ruta där paneler är tillgängliga.  |
| Väst-bindning     | Västerländskt longitud för den avgränsnings ruta där paneler är tillgängliga.   |
| Transparens   | Transparens för panel lagret.   |
| Är TMS         | Panel kart tjänster, en specifikation som kastar om Y-koordinatens axel i panel lagret. |
| Minsta zoomning       | Minsta zoomnings nivå paneler är tillgängliga. |
| Max zoomning       | Övre zoomnings nivå panelerna är tillgängliga.  |
| Skikt position | Anger lagrets position i förhållande till andra kart skikt. |

## <a name="tile-url-formatting"></a>URL-formatering för panel

Det finns tre olika namngivnings konventioner för panel tjänster som stöds av Azure Maps visuella objekt:

-   **X, y, zoom notation** -X är kolumnen, y är panelens rad position i panel rutnätet och zoomningen formaterar ett värde baserat på zoomnivån.
-   **Quadkey-notation** – kombinerar x-, y-och zoomnings information till ett enda sträng värde. Detta sträng värde blir en unik identifierare för en enda panel.
-   **Avgränsnings ruta** – ange en bild i markerings rutans koordinater-format: `{west},{south},{east},{north}` . Det här formatet används ofta av [Web mappnings tjänster (WMS)](https://www.opengeospatial.org/standards/wms).

Panel-URL: en HTTPS-URL till en panels URL-mall som använder följande parametrar:

-   `{x}` -X position i rutan. Du måste också ha `{y}` och `{z}` .
-   `{y}` -Y position i panelen. Du måste också ha `{x}` och `{z}` .
-   `{z}` -Zoomnings nivå i panelen. Du måste också ha `{x}` och `{y}` .
-   `{quadkey}` -Panel `quadkey` identifierare baserad på Bing Maps-panelens system namngivnings konvention.
-   `{bbox-epsg-3857}` – En sträng med avgränsnings rutor med formatet `{west},{south},{east},{north}` i EPSG 3857 rums referens system.

Följande är ett exempel på en formaterad panel URL för [väder polär-panelen](/rest/api/maps/renderv2/getmaptilepreview) i Azure Maps. Observera att det `[subscription-key]` finns en plats hållare för din Azure Maps prenumerations nyckel.

> `https://atlas.microsoft.com/map/tile?zoom={z}&x={x}&y={y}&tilesetId=microsoft.weather.radar.main&api-version=2.0&subscription-key=[subscription-key]`

Mer information om hur du Azure Maps i systemet finns i [zoomnings nivåer och panel rutnät](zoom-levels-and-tile-grid.md).

## <a name="next-steps"></a>Nästa steg

Lägg till mer kontext till kartan:

> [!div class="nextstepaction"]
> [Visa trafik i realtid](power-bi-visual-show-real-time-traffic.md)