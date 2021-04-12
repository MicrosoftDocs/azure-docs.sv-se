---
title: Hämta data från former på en karta | Microsoft Azure Maps
description: I den här artikeln lär du dig hur du får form data som ritas på en karta med hjälp av Microsoft Azure Maps-webbsdk.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: ddb8009e544ede82d1c56d112950ff247a87380c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92890708"
---
# <a name="get-shape-data"></a>Hämta formdata

Den här artikeln visar hur du hämtar data för former som ritas på kartan. Vi använder funktionen **drawingManager. GetSource ()** i [Drawing Manager](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager#getsource--). Det finns olika scenarier när du vill extrahera polyjson-data för en ritad form och använda den någon annan stans.  


## <a name="get-data-from-drawn-shape"></a>Hämta data från den ritade formen

Följande funktion hämtar den ritade formens källdata och matar ut den till skärmen. 

```javascript
function getDrawnShapes() {
    var source = drawingManager.getSource();

    document.getElementById('CodeOutput').value = JSON.stringify(source.toJson(), null, '    ');
}
```

Nedan visas det fullständiga kod exemplet, där du kan rita en form för att testa funktionerna:

<br/>

<iframe height="686" title="Hämta formdata" src="//codepen.io/azuremaps/embed/xxKgBVz/?height=265&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>Se hur pennan <a href='https://codepen.io/azuremaps/pen/xxKgBVz/'>får form data</a> genom att Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Nästa steg

Lär dig hur du använder ytterligare funktioner i modulen rit verktyg:

> [!div class="nextstepaction"]
> [Reagera på rithändelser](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [Interaktionstyper och kortkommandon](drawing-tools-interactions-keyboard-shortcuts.md)

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Karta](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [Drawing Manager](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager)

> [!div class="nextstepaction"]
> [Verktygsfältet Rita](/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar)