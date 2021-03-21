---
title: Webbläsare som stöds av Web SDK | Microsoft Azure Maps
description: Lär dig hur du kontrollerar om Azure Maps Web SDK stöder en webbläsare. Visa en lista över webbläsare som stöds. Lär dig hur du använder kart tjänster med äldre webbläsare.
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 6321b96cb3db570102f138dcfd949d9c32daedbc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100384477"
---
# <a name="web-sdk-supported-browsers"></a>Webbläsare som stöds av webb-SDK

Azure Maps Web SDK innehåller en hjälp funktion som kallas [Atlas. isSupported](/javascript/api/azure-maps-control/atlas#issupported-boolean-). Den här funktionen identifierar om en webbläsare har en minsta uppsättning WebGL-funktioner som krävs för att stödja inläsning och åter givning av kart kontrollen. Här är ett exempel på hur du använder funktionen:

```JavaScript
if (!atlas.isSupported()) {
    alert('Your browser is not supported by Azure Maps');
} else if (!atlas.isSupported(true)) {
    alert('Your browser is supported by Azure Maps, but may have major performance caveats.');
} else {
    // Your browser is supported. Add your map code here.
}
```

## <a name="desktop"></a>Skrivbord

Azure Maps Web SDK stöder följande Skriv bords webbläsare:

- Microsoft Edge (aktuell och föregående version)
- Google Chrome (aktuell och föregående version)
- Mozilla Firefox (aktuell och föregående version)
- Apple Safari (macOS X) (aktuell och tidigare version)

Se även [mål äldre webbläsare](#Target-Legacy-Browsers) längre fram i den här artikeln.

## <a name="mobile"></a>Mobilt

Azure Maps Web SDK stöder följande mobila webbläsare:

- Android
  - Aktuell version av Chrome på Android 6,0 och senare
  - Chrome webbvy på Android 6,0 och senare
- iOS
  - Mobile Safari på den aktuella och föregående huvud versionen av iOS
  - UIWebView och WKWebView på den aktuella och den tidigare huvud versionen av iOS
  - Aktuell version av Chrome för iOS

> [!TIP]
> Om du bäddar in en karta inuti ett mobilt program med hjälp av en WebView-kontroll, kanske du föredrar att använda [NPM-paketet i Azure Maps Web SDK](https://www.npmjs.com/package/azure-maps-control) i stället för att referera till den version av SDK som finns på Azure Content Delivery Network. Den här metoden minskar inläsnings tiden eftersom SDK redan finns på användarens enhet och inte behöver laddas ned vid körning.

## <a name="nodejs"></a>Node.js

Följande Web SDK-moduler stöds också i Node.js:

- Modulen tjänster ([dokumentation](how-to-use-services-module.md)  |  [NPM module](https://www.npmjs.com/package/azure-maps-rest))

## <a name="target-legacy-browsers"></a><a name="Target-Legacy-Browsers"></a>Fokusera på äldre webbläsare

Du kanske vill rikta in dig på äldre webbläsare som inte stöder WebGL eller som bara har begränsat stöd för den. I sådana fall rekommenderar vi att du använder Azure Maps Services tillsammans med en kart kontroll med öppen källkod som [broschyr](https://leafletjs.com/). Här är ett exempel som använder [plugin-programmet](https://github.com/azure-samples/azure-maps-leaflet)med öppen källkod Azure Maps.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Azure Maps + broschyr" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se pennan <a href='https://codepen.io/azuremaps/pen/GeLgyx/'>Azure Maps + broschyr</a> efter Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ytterligare kod exempel som använder Azure Maps i informations bladet hittar du [här](https://azuremapscodesamples.azurewebsites.net/?search=leaflet).

[Här](open-source-projects.md#third-part-map-control-plugins) följer några populära kart kontroller med öppen källkod som Azure Mapss teamet har skapat plugin-program för.

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Maps Web SDK:

[Kartkontroll](how-to-use-map-control.md)

[Tjänstmodul](how-to-use-services-module.md)
