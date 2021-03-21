---
title: Ansluta till en tjänst för webb funktions tjänsten (WFS) | Microsoft Azure Maps
description: Lär dig hur du ansluter till en WFS-tjänst och frågar WFS-tjänsten med hjälp av Azure Maps Web SDK och den spatiala IO-modulen.
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 75a7da41e4f0d5356ca135eb3cb4cbe5f29e8116
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92891469"
---
# <a name="connect-to-a-wfs-service"></a>Ansluta till en WFS-tjänst

En webb funktions tjänst (WFS) är en webb tjänst för att fråga spatialdata som har ett standardiserat API som definieras av Open Geospatial Consortium (OGC). `WfsClient`-Klassen i den spatiala IO-modulen låter utvecklare ansluta till en WFS-tjänst och fråga efter data från tjänsten.

Följande funktioner stöds av `WfsClient` klassen:

- Versioner som stöds: `1.0.0` , `1.1.0` , och `2.0.0`
- Filter operatorer som stöds: binära jämförelser, logik, matematik, värde och `bbox` .
- Begär Anden görs `HTTP GET` endast med.
- Åtgärder som stöds:

    | Åtgärd | Beskrivning |
    | :-- | :-- |
    | GetCapabilities | Genererar ett Metadatadokumentet med giltiga WFS-åtgärder och-parametrar |
    | GetFeature | Returnerar ett urval av funktioner från en data Källa |
    | DescribeFeatureType | Returnerar de funktions typer som stöds |

## <a name="using-the-wfs-client"></a>Använda WFS-klienten

`atlas.io.ogc.WfsClient`-Klassen i den spatiala i/o-modulen gör det enkelt att fråga en WFS-tjänst och konvertera Svaren till geospatiala JSON-objekt. Detta interjson-objekt kan sedan användas för andra mappnings syfte.

Följande kod frågar en WFS-tjänst och återger de returnerade funktionerna på kartan.

<br/>

<iframe height='700' scrolling='no' title='Enkelt WFS-exempel' src='//codepen.io/azuremaps/embed/MWwvVYY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se det <a href='https://codepen.io/azuremaps/pen/MWwvVYY/'>enkla WFS-exemplet</a> genom att Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="supported-filters"></a>Filter som stöds

Specifikationen för WFS-standarden använder OGC-filter. Filtren nedan stöds av WFS-klienten, förutsatt att den tjänst som anropas också stöder dessa filter. Anpassade filter strängar kan skickas till `CustomFilter` klassen.

**Logiska operatorer**

- `And`
- `Or`
- `Not`

**Värde operatorer**

- `GmlObjectId`
- `ResourceId`

**Matematik operatorer**

- `Add`
- `Sub`
- `Mul`
- `Div`

**Jämförelseoperatorer**

- `PropertyIsEqualTo`
- `PropertyIsNotEqualTo`
- `PropertyIsLessThan`
- `PropertyIsGreaterThan`
- `PropertyIsLessThanOrEqualTo`
- `PropertyIsGreaterThanOrEqualTo`
- `PropertyIsLike`
- `PropertyIsNull`
- `PropertyIsNil`
- `PropertyIsBetween`

Följande kod visar hur du använder olika filter med WFS-klienten.

<br/>

<iframe height='500' scrolling='no' title= 'WFS filter exempel' src='//codepen.io/azuremaps/embed/NWqvYrV/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se <a href='https://codepen.io/azuremaps/pen/NWqvYrV/'>exemplen för WFS filter</a> genom att Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="wfs-service-explorer"></a>WFS service Explorer

I följande kod används WFS-klienten för att utforska WFS Services. Välj ett egenskaps typ lager i tjänsten och se den associerade förklaringen.

<br/>

<iframe height='700' style='width: 100%;' scrolling='no' title= 'WFS service Explorer' src='//codepen.io/azuremaps/embed/bGdrvmG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/bGdrvmG/'>WFS service Explorer</a> genom Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

För att komma åt WFS-tjänster som finns på icke-CORS-aktiverade slut punkter, kan en CORS-aktiverad proxy-tjänst överföras till `proxyService` alternativet för WFS-klienten enligt nedan. 

```JavaScript
//Create the WFS client to access the service and use the proxy service settings
client = new atlas.io.ogc.WfsClient({
    url: url,
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
});
```

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [WfsClient](/JavaScript/api/azure-maps-spatial-io/atlas.io.ogc.wfsclient)

> [!div class="nextstepaction"]
> [WfsServiceOptions](/JavaScript/api/azure-maps-spatial-io/atlas.wfsserviceoptions)

Se följande artiklar för fler kod exempel som du kan lägga till i dina kartor:

> [!div class="nextstepaction"]
> [Använda kärnåtgärder](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Information om dataformat som stöds](spatial-io-supported-data-format-details.md)