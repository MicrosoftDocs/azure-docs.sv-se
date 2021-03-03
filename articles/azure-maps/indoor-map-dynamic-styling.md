---
title: Implementera dynamisk formatering för Azure Maps skapare (förhands granskning) inomhus Maps
description: Lär dig hur du implementerar dynamisk formatering för skapare (för hands version) i inomhus Maps
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: a23c492d4a81703c0dc6612928a56b5b31d52cae
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101726327"
---
# <a name="implement-dynamic-styling-for-creator-preview-indoor-maps"></a>Implementera dynamisk formatering för skapare (förhands granskning) inomhus Maps

> [!IMPORTANT]
> Azure Maps Creator-tjänster finns för närvarande i en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Med Azure Maps skapare [funktion tillstånds tjänst](/rest/api/maps/featurestate) kan du använda formatmallar som baseras på de dynamiska egenskaperna för inlednings data funktioner i kartan.  Du kan till exempel återge anläggningarnas Mötes rum med en speciell färg för att avspegla inslags status. I den här artikeln visar vi hur du dynamiskt återger inaktuella kart funktioner med [funktions tillstånds tjänsten](/rest/api/maps/featurestate) och den inaktuella [webbmodulen](how-to-use-indoor-module.md).

## <a name="prerequisites"></a>Förutsättningar

1. [Skapa ett Azure Maps-konto](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Hämta en primär prenumerations nyckel](quick-demo-map-app.md#get-the-primary-key-for-your-account), även kallat primär nyckel eller prenumerations nyckel.
3. [Skapa en skapare (förhands granskning) resurs](how-to-manage-creator.md)
4. Ladda ned [exempel ritnings paketet](https://github.com/Azure-Samples/am-creator-indoor-data-examples).
5. [Skapa en](tutorial-creator-indoor-maps.md) ingångs karta för att få en `tilesetId` och `statesetId` .
6. Bygg ett webb program genom att följa stegen i [använda Map-modulen](how-to-use-indoor-module.md).

I den här självstudien används [Postman](https://www.postman.com/) -programmet, men du kan välja en annan API utvecklings miljö.

## <a name="implement-dynamic-styling"></a>Implementera dynamisk formatering

När du har slutfört kraven bör du ha ett enkelt webb program konfigurerat med din prenumerations nyckel, `tilesetId` och `statesetId` .

### <a name="select-features"></a>Välja funktioner

För att implementera dynamisk formatering måste en funktion, till exempel ett möte eller konferens rum, refereras till av sin funktion `id` . Du använder funktionen `id` för att uppdatera den dynamiska egenskapen eller *statusen* för den funktionen. Om du vill visa de funktioner som definierats i en data uppsättning kan du använda någon av följande metoder:

* WFS-API (Web Feature service). Data uppsättningar kan frågas med hjälp av WFS-API: et. WFS följer Open Geospatial Consortium API-funktioner. WFS-API: et är användbart för att fråga efter funktioner i en data uppsättning. Du kan till exempel använda WFS för att hitta alla Mötes rum i mitten av en specifik anläggning och golv nivå.

* Implementera anpassad kod som låter en användare välja funktioner på en karta med ditt webb program. I den här artikeln kommer vi att använda det här alternativet.  

Följande skript implementerar händelsen mus klickning. Koden hämtar funktionen `id` baserat på den klickade punkten. I ditt program kan du infoga koden under ditt kod block för inmatnings hanteraren. Kör programmet och kontrol lera konsolen för att hämta funktionen `id` för den klickade punkten.

```javascript
/* Upon a mouse click, log the feature properties to the browser's console. */
map.events.add("click", function(e){

    var features = map.layers.getRenderedShapes(e.position, "indoor");

    features.forEach(function (feature) {
        if (feature.layer.id == 'indoor_unit_office') {
            console.log(feature);
        }
    });
});
```

Självstudien [skapa en](tutorial-creator-indoor-maps.md) över gångs karta konfigurerade funktionen stateset för att godkänna tillstånds uppdateringar för `occupancy` .

I nästa avsnitt anger vi användnings *status* för Office `UNIT26` till `true` . även om Office är `UNIT27` inställt på `false` .

### <a name="set-occupancy-status"></a>Ange status för besittning

 Vi kommer nu att uppdatera status för de två kontoren `UNIT26` och `UNIT27` :

1. I Postman-programmet väljer du **nytt**. I fönstret **Skapa nytt** väljer du **begäran**. Ange ett **namn på begäran** och välj en samling. Klicka på **Spara**

2. Använd [API: t för funktions uppdaterings tillstånd](/rest/api/maps/featurestate/updatestatespreview) för att uppdatera tillståndet. Skicka stateset-ID: t och `UNIT26` för en av de två enheterna. Lägg till din Azure Maps prenumerations nyckel. Här är URL: en för en **post** -begäran om att uppdatera status:

    ```http
    https://atlas.microsoft.com/featureState/state?api-version=1.0&statesetID={statesetId}&featureID=UNIT26&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Ange som i **rubrikerna** för **post** -begäran `Content-Type` `application/json` . I **bröd texten** i **post** -begäran skriver du följande RAW JSON med funktions uppdateringarna. Uppdateringen sparas endast om den bokförda tidsstämpeln är efter tidsstämpeln som användes i tidigare uppdaterings begär Anden för funktions tillstånd för samma funktion `ID` . Skicka "upptagen" `keyName` för att uppdatera värdet.

    ```json
    {
        "states": [
            {
                "keyName": "occupied",
                "value": true,
                "eventTimestamp": "2019-11-14T17:10:20"
            }
        ]
    }
    ```

4. Gör om steg 2 och 3 med `UNIT27` följande JSON.

    ``` json
    {
        "states": [
            {
                "keyName": "occupied",
                "value": false,
                "eventTimestamp": "2019-11-14T17:10:20"
            }
        ]
    }
    ```

### <a name="visualize-dynamic-styles-on-a-map"></a>Visualisera dynamiska stilar på en karta

Det webb program som du tidigare öppnade i en webbläsare bör nu avspegla det uppdaterade läget för kart funktionerna. `UNIT27`(142) ska vara grönt och `UNIT26` (143) ska vara röd.

![Ledigt utrymme i grönt och hårt belastat utrymme i rött](./media/indoor-map-dynamic-styling/room-state.png)

[Se live demo](https://azuremapscodesamples.azurewebsites.net/?sample=Creator%20indoor%20maps)

## <a name="next-steps"></a>Nästa steg

Läs mer genom att läsa:

> [!div class="nextstepaction"]
> [Skapare (för hands version) för från koppling till inomhus](creator-indoor-maps.md)

Se referenserna för de API: er som nämns i den här artikeln:

> [!div class="nextstepaction"]
> [Data uppladdning](creator-indoor-maps.md#upload-a-drawing-package)

> [!div class="nextstepaction"]
> [Data konvertering](creator-indoor-maps.md#convert-a-drawing-package)

> [!div class="nextstepaction"]
> [Data uppsättning](creator-indoor-maps.md#datasets)

> [!div class="nextstepaction"]
> [Tileset](creator-indoor-maps.md#tilesets)

> [!div class="nextstepaction"]
> [Funktions tillstånds uppsättning](creator-indoor-maps.md#feature-statesets)

> [!div class="nextstepaction"]
> [WFS-tjänst](creator-indoor-maps.md#web-feature-service-api)