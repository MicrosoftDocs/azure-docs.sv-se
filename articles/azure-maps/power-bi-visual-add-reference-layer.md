---
title: Lägg till ett referens skikt i Azure Maps Power BI visuella objektet | Microsoft Azure Maps
description: I den här artikeln får du lära dig hur du använder referens skiktet i Microsoft Azure Maps-visualisering för Power BI.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: eb6c9fed42f263ca6cfaa1ea975d31cb8f4a75cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86262161"
---
# <a name="add-a-reference-layer"></a>Lägg till ett referenslager

Med funktionen referens skikt kan en sekundär spatialdata laddas upp till det visuella objektet och överlappa kartan för att ge ytterligare kontext. Den här data uppsättningen är värd för Power BI och måste vara en [INTERjson-fil](https://wikipedia.org/wiki/GeoJSON) med `.json` `.geojson` fil namns tillägget eller.

Om du vill lägga till en **injson** -fil som ett referens lager går du till fönstret **format** , expanderar avsnittet **referens skikt** och trycker på knappen **+ Lägg till lokal fil** .

När en polyjson-fil har lagts till i referens skiktet visas namnet på filen i stället för knappen **+ Lägg till lokal fil** med ett **X** bredvid sig. Tryck på **X** om du vill ta bort data från det visuella objektet och ta bort den från Power BI.

Följande karta visar [2016 räknings spårningar för Colorado](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Common/data/geojson), färgade efter population.

> [!div class="mx-imgBorder"]
> ![En karta som visar 2016 räknings spårningar för Colorado, färgade efter population som ett referens lager](media/power-bi-visual/reference-layer-CO-census-tract.png)

Följande är alla inställningar i rutan **format** som är tillgängliga i avsnittet **referens skikt** .

| Inställning              | Beskrivning   |
|----------------------|---------------|
| Referens skikts data | Data-och JSON-filen som ska överföras till det visuella objektet som ett extra lager i kartan. Knappen **+ Lägg till lokal fil** öppnar en fil dialog ruta som användaren kan använda för att välja en interjson-fil som har `.json` `.geojson` fil namns tillägget eller. |

> [!NOTE]
> I den här förhands granskningen av det Azure Maps visuella objektet kommer referens lagret bara att läsa in de första 5 000-form funktionerna till kartan. Den här gränsen kommer att höjas i en framtida uppdatering.

## <a name="styling-data-in-a-reference-layer"></a>Formatera data i ett referens skikt

Egenskaper kan läggas till i varje funktion i den lokala JSON-filen för att anpassa hur den är formaterad på kartan. Den här funktionen använder funktionen för enkel data lager i Azure Maps Web SDK. Mer information finns i det här dokumentet om [stil egenskaper som stöds](spatial-io-add-simple-data-layer.md#default-supported-style-properties). Anpassade ikon bilder stöds inte i Azure Maps visualiseringen som en säkerhets åtgärd.

Följande är ett exempel på en funktion för en interjson-punkt som anger att den visade färgen är röd.

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": {
        "color": "red"
    }
}
```
## <a name="next-steps"></a>Nästa steg

Lägg till mer kontext till kartan:

> [!div class="nextstepaction"]
> [Lägga till ett panelskikt](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [Visa trafik i realtid](power-bi-visual-show-real-time-traffic.md)
