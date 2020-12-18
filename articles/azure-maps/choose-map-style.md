---
title: Ändra formatet på Azure Maps webb Kartkontroll
description: Lär dig hur du ändrar en kartas stil och alternativ. Se hur du lägger till en stil väljar kontroll till en karta i Azure Maps så att användarna kan växla mellan olika format.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 556e265cc0d1aae33823185ec98d23f191ed1694
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680032"
---
# <a name="change-the-style-of-the-map"></a>Ändra formatet för kartan

Kart kontrollen stöder flera olika alternativ för kart [stil](/javascript/api/azure-maps-control/atlas.styleoptions) och [grundläggande kart stilar](supported-map-styles.md). Alla format kan anges när kart kontrollen initieras. Du kan också ställa in format med kart kontrollens `setStyle` funktion. Den här artikeln visar hur du använder de här stil alternativen för att anpassa kartans utseende. Du får också lära dig hur du implementerar format väljar kontrollen i kartan. Med format väljar kontrollen kan användaren växla mellan olika bas format.

## <a name="set-map-style-options"></a>Ange alternativ för kart stil

Stil alternativ kan anges under initiering av webb kontroll. Du kan också uppdatera stil alternativ genom att anropa kart kontrollens `setStyle` funktion. För att se alla tillgängliga stil alternativ, se [format alternativ](/javascript/api/azure-maps-control/atlas.styleoptions).

```javascript
//Set the style options when creating the map.
var map = new atlas.Map('map', {
    renderWorldCopies: false,
    showBuildingModels: false,
    showLogo: true,
    showFeedbackLink: true,
    style: 'road'

    //Additional map options.
};

//Update the style options at anytime using `setStyle` function.
map.setStyle({
    renderWorldCopies: true,
    showBuildingModels: true,
    showLogo: false,
    showFeedbackLink: false
});
```

Följande verktyg visar hur de olika stil alternativen ändrar hur kartan återges. Om du vill se 3D-byggnaderna zoomar du in nära en större stad.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Alternativ för kart stil" src="https://codepen.io/azuremaps/embed/eYNMjPb?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se <a href='https://codepen.io/azuremaps/pen/eYNMjPb'>stil alternativen</a> för pen-kartor genom Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="set-a-base-map-style"></a>Ange ett bas kart format

Du kan också initiera kart kontrollen med en av de [grundläggande kart formaten](supported-map-styles.md) som är tillgängliga i webb-SDK: n. Du kan sedan använda `setStyle` funktionen för att uppdatera bas formatet med en annan kart stil.

### <a name="set-a-base-map-style-on-initialization"></a>Ange ett bas kart format vid initiering

Du kan ange grundläggande stilar för kart kontrollen under initieringen. I följande kod `style` är alternativet för kart kontrollen inställd på [ `grayscale_dark` bas kart formatet](supported-map-styles.md#grayscale_dark).  

```javascript
var map = new atlas.Map('map', {
    style: 'grayscale_dark',

    //Additional map options
);
```

<br/>

<iframe height='500' scrolling='no' title='Ange formatet på kart inläsning' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan som <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>anger formatet på kart inläsning</a> av Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="update-the-base-map-style"></a>Uppdatera bas kart formatet

Det grundläggande kart formatet kan uppdateras med hjälp av `setStyle` funktionen och ställer in `style` alternativet för att antingen byta till en annan typ av grundläggande karta eller lägga till ytterligare stil alternativ.

```javascript
map.setStyle({ style: 'satellite' });
```

I följande kod, efter att en kart instans har lästs in, uppdateras kart formatet från `grayscale_dark` till `satellite` med hjälp av funktionen [setStyle](/javascript/api/azure-maps-control/atlas.map#setstyle-styleoptions-) .

<br/>

<iframe height='500' scrolling='no' title='Uppdaterar formatet' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan som <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>uppdaterar formatet</a> genom att Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-the-style-picker-control"></a>Lägg till format väljar kontrollen

Format väljar kontrollen är en lätt att använda-knapp med utfällbar panel som kan användas av slutanvändaren för att växla mellan grundläggande stilar.

Format väljaren har två olika layoutalternativ: `icon` och `list` . Med stil Väljaren kan du också välja två olika typer av format väljare för kontroller `style` : `light` och `dark` . I det här exemplet använder format väljaren `icon` layouten och visar en urvals lista med grundläggande kart format i form av ikoner. Format kontroll väljaren innehåller följande grundläggande uppsättning format: `["road", "grayscale_light", "grayscale_dark", "night", "road_shaded_relief"]` . Mer information om stil väljar kontroll alternativ finns i [alternativ för stil kontroll](/javascript/api/azure-maps-control/atlas.stylecontroloptions).

Bilden nedan visar den stil väljar kontroll som visas i `icon` layouten.

:::image type="content" source="./media/choose-map-style/style-picker-icon-layout.png" alt-text="Ikon layout för format väljare":::

Bilden nedan visar den stil väljar kontroll som visas i `list` layouten.

:::image type="content" source="./media/choose-map-style/style-picker-list-layout.png" alt-text="Layout för format väljare":::

> [!IMPORTANT]
> Som standard visar format väljar kontrollen alla formatmallar som är tillgängliga under S0 pris nivå för Azure Maps. Om du vill minska antalet formatmallar i den här listan skickar du en matris med de format som du vill ska visas i listan till `mapStyle` alternativet för format väljaren. Om du använder S1 och vill visa alla tillgängliga stilar väljer du `mapStyles` alternativet för format väljare till `"all"` .

Följande kod visar hur du åsidosätter standard `mapStyles` mal listan. I det här exemplet ska vi ställa in `mapStyles` alternativet för att visa vilka grund format vi vill ska visas av format väljar kontrollen.

<br/>

<iframe height='500' scrolling='no' title='Lägga till format väljare' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan genom <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>att lägga till format väljaren</a> per Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Karta](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [StyleOptions](/javascript/api/azure-maps-control/atlas.styleoptions)

> [!div class="nextstepaction"]
> [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol)

> [!div class="nextstepaction"]
> [StyleControlOptions](/javascript/api/azure-maps-control/atlas.stylecontroloptions)

Se följande artiklar för fler kod exempel som du kan lägga till i dina kartor:

> [!div class="nextstepaction"]
> [Lägg till kartkontroller](map-add-controls.md)

> [!div class="nextstepaction"]
> [Lägga till ett symbolskikt](map-add-pin.md)

> [!div class="nextstepaction"]
> [Lägga till ett bubbelskikt](map-add-bubble-layer.md)
