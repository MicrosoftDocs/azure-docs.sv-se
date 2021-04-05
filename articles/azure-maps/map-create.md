---
title: Skapa en karta med Azure Maps | Microsoft Azure Maps
description: Ta reda på hur du lägger till kartor på webb sidor med hjälp av Azure Maps Web SDK. Lär dig mer om alternativ för animering, format, kamera, tjänster och användar interaktioner.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 833b6413cc5dfde1129075a286e5fe93a06e159f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92890929"
---
# <a name="create-a-map"></a>Skapa en karta

Den här artikeln visar hur du skapar en karta och animerar en karta.  

## <a name="loading-a-map"></a>Läser in en karta

Om du vill läsa in en karta skapar du en ny instans av [kart klassen](/javascript/api/azure-maps-control/atlas.map). När kartan initieras skickar du ett DIV-element-ID för att återge kartan och skicka en uppsättning alternativ som ska användas vid inläsning av kartan. Om standard information om autentisering inte anges i `atlas` namn området måste den här informationen anges i kart alternativen vid inläsning av kartan. Kartan läser in flera resurser asynkront för prestanda. Därför kan du när du har skapat kart instansen bifoga en `ready` eller- `load` händelse till kartan och sedan lägga till ytterligare kod som samverkar med kartan till händelse hanteraren. `ready`Händelsen utlöses så snart kartan har tillräckligt med resurser för att kunna interagera med program mässigt. `load`Händelsen utlöses efter att den ursprungliga kart visningen har lästs in helt. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Grundläggande kart belastning" src="//codepen.io/azuremaps/embed/rXdBXx/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Se den <a href='https://codepen.io/azuremaps/pen/rXdBXx/'>grundläggande kart inläsningen</a> från Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Du kan läsa in flera kartor på samma sida. Flera kartor på samma sida kan använda samma eller olika inställningar för autentisering och språk.

## <a name="show-a-single-copy-of-the-world"></a>Visa en enskild kopia av världen

När kartan zoomas ut på en bred skärm visas flera kopior av världen vågrätt. Det här alternativet är bra för vissa scenarier, men för andra program är det önskvärt att se en enda kopia av världen. Detta beteende implementeras genom att ställa in Maps- `renderWorldCopies` alternativet på `false` .

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="renderWorldCopies = falskt" src="//codepen.io/azuremaps/embed/eqMYpZ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Se Pen <a href='https://codepen.io/azuremaps/pen/eqMYpZ/'>renderWorldCopies = false</a> by Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="map-options"></a>Kart alternativ

När du skapar en karta finns det flera olika typer av alternativ som kan skickas för att anpassa hur kartan fungerar enligt listan nedan.

- [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions) och [CameraBoundOptions](/javascript/api/azure-maps-control/atlas.cameraboundsoptions) används för att ange det område som kartan ska visa.
- [ServiceOptions](/javascript/api/azure-maps-control/atlas.serviceoptions) används för att ange hur kartan ska interagera med tjänster som Power of the map.
- [StyleOptions](/javascript/api/azure-maps-control/atlas.styleoptions) används för att ange att kartan ska formateras och återges.
- [UserInteractionOptions](/javascript/api/azure-maps-control/atlas.userinteractionoptions) används för att ange hur kartan ska uppnås när användaren interagerar med kartan. 

Dessa alternativ kan också uppdateras när kartan har lästs in med hjälp av `setCamera` funktionerna,, `setServiceOptions` `setStyle` och `setUserInteraction` . 

## <a name="controlling-the-map-camera"></a>Styra kart kameran

Det finns två sätt att ange det område som visas i kartan med hjälp av kameran på en karta. Du kan ställa in kamera alternativen när du läser in kartan. Alternativt kan du anropa `setCamera` alternativet när kartan har lästs in för att program mässigt uppdatera Map-vyn.  

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>Ange kamera

Kart kameran styr vad som visas i visnings området för kart ytan. Kamera alternativ kan skickas till kart alternativen när de initieras eller skickas till Maps- `setCamera` funktionen.

```javascript
//Set the camera options when creating the map.
var map = new atlas.Map('map', {
    center: [-122.33, 47.6],
    zoom: 12

    //Additional map options.
};

//Update the map camera at anytime using setCamera function.
map.setCamera({
    center: [-110, 45],
    zoom: 5 
});
```

I följande kod skapas ett [kart objekt](/javascript/api/azure-maps-control/atlas.map) och alternativen för centrum och zoomning är inställda. Kart egenskaper, till exempel Center och zoomnings nivå, ingår i [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions).

<br/>

<iframe height='500' scrolling='no' title='Skapa en karta via CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan genom att <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>skapa en karta `CameraOptions` via </a>Azure Location Based Services ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>Ange kamerans gränser

En avgränsnings ruta kan användas för att uppdatera kart kameran. Om markerings rutan har beräknats från punkt data är det ofta användbart att även ange ett värde för pixel-utfyllnad i kamera alternativen för ikonens storlek. På så sätt kan du se till att punkterna inte faller utanför kanten på kart visnings området.

```javascript
map.setCamera({
    bounds: [-122.4, 47.6, -122.3, 47.7],
    padding: 10
});
```

I följande kod skapas ett [kart objekt](/javascript/api/azure-maps-control/atlas.map) via `new atlas.Map()` . Kart egenskaper som `CameraBoundsOptions` kan definieras via [setCamera](/javascript/api/azure-maps-control/atlas.map) -funktionen för kart klassen. Egenskaper för gränser och utfyllnad anges med hjälp av `setCamera` .

<br/>

<iframe height='500' scrolling='no' title='Skapa en karta via CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan genom att <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>skapa en karta `CameraBoundsOptions` via </a>Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="animate-map-view"></a>Animera Map-vy

När du ställer in kamera alternativ för kartan kan du också ställa in [animerings alternativ](/javascript/api/azure-maps-control/atlas.animationoptions) . De här alternativen anger vilken typ av animering och varaktighet det tar att flytta kameran.

```javascript
map.setCamera({
    center: [-122.33, 47.6],
    zoom: 12,
    duration: 1000,
    type: 'fly'  
});
```

I följande kod skapar det första kod blocket en karta och anger kart formaten Enter och zoom. I det andra kod blocket skapas en klick händelse hanterare för knappen animera. När du klickar på den här knappen `setCamera` anropas funktionen med några slumpmässiga värden för [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions) och [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions).

<br/>

<iframe height='500' scrolling='no' title='Animera Map-vy' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se Map- <a href='https://codepen.io/azuremaps/pen/WayvbO/'>vyn</a> för pen-animering efter Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="request-transforms"></a>Begär transformeringar

Ibland är det praktiskt att kunna ändra HTTP-begäranden som görs av kart kontrollen. Exempel:

- Lägg till ytterligare rubriker i panel begär Anden. Detta görs ofta för lösenordsskyddade tjänster.
- Ändra URL: er för att köra begär Anden via en proxy-tjänst.

[Tjänst alternativen](/javascript/api/azure-maps-control/atlas.serviceoptions) för kartan har en `transformRequest` som kan användas för att ändra alla begär Anden som görs av kartan innan de görs. `transformRequest`Alternativet är en funktion som tar i två parametrar, en sträng-URL och en resurs typs sträng som anger vad begäran används för. Den här funktionen måste returnera ett [RequestParameters](/javascript/api/azure-maps-control/atlas.requestparameters) -resultat.

```JavaScript
transformRequest: (url: string, resourceType: string) => RequestParameters
```

I följande exempel visas hur du använder detta för att ändra alla begär anden till storleken `https://example.com` genom att lägga till ett användar namn och ett lösen ord som rubriker i begäran.

```JavaScript
var map = new atlas.Map('myMap', {
    transformRequest: function (url, resourceType) {
        //Check to see if the request is to the specified endpoint.
        if (url.indexOf('https://examples.com') > -1) {
            //Add custom headers to the request.
            return {
                url: url,
                header: {
                    username: 'myUsername',
                    password: 'myPassword'
                }
            };
        }

        //Return the URL unchanged by default.
        return { url: url };
    },

    authOptions: {
        authType: 'subscriptionKey',
        subscriptionKey: '<Your Azure Maps Key>'
    }
});
```

## <a name="try-out-the-code"></a>Testa koden

Titta på kod exemplen. Du kan redigera JavaScript-koden inuti **fliken JS** och se ändringarna i Map-vyn på **fliken resultat**. Du kan också klicka på **Redigera på CodePen**, i det övre högra hörnet och ändra koden i CodePen.

<a id="relatedReference"></a>

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Karta](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions)

> [!div class="nextstepaction"]
> [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions)

Se kod exempel för att lägga till funktioner i din app:

> [!div class="nextstepaction"]
> [Ändra formatet för kartan](choose-map-style.md)

> [!div class="nextstepaction"]
> [Lägga till kontroller till kartan](map-add-controls.md)

> [!div class="nextstepaction"]
> [Kodexempel](/samples/browse/?products=azure-maps)