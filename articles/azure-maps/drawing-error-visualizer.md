---
title: Använda Azure Maps ritnings fel visualiserare
description: I den här artikeln får du lära dig mer om hur du visualiserar varningar och fel som returneras av skapare (för hands version) konverterings-API.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: db88e347e12783205ea8c31fed0bb374fccb4736
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96903589"
---
# <a name="using-the-azure-maps-drawing-error-visualizer-with-creator-preview"></a>Använda Azure Maps Rita fel visualiserare med skapare (för hands version)

> [!IMPORTANT]
> Azure Maps Creator-tjänster finns för närvarande i en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Ritnings fel Visualiseraren är ett fristående webb program som visar [varningar från ritnings paket och fel som](drawing-conversion-error-codes.md) upptäckts under konverterings processen. Webb programmet för fel visualiserare består av en statisk sida som du kan använda utan att ansluta till Internet.  Du kan använda fel Visualiseraren för att åtgärda fel och varningar i enlighet med [kraven för ritnings paket](drawing-requirements.md). [Azure Maps Conversion API](/rest/api/maps/conversion) returnerar bara ett svar med en länk till fel visualiseraren endast när ett fel upptäcks.

## <a name="prerequisites"></a>Förutsättningar

Innan du kan ladda ned ritnings fel Visualiseraren måste du:

1. [Skapa ett Azure Maps-konto](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Hämta en primär prenumerations nyckel](quick-demo-map-app.md#get-the-primary-key-for-your-account), även kallat primär nyckel eller prenumerations nyckel.
3. [Skapa en skapare (förhands granskning) resurs](how-to-manage-creator.md)

I den här självstudien används [Postman](https://www.postman.com/) -programmet, men du kan välja en annan API utvecklings miljö.

## <a name="download"></a>Ladda ned

1. Ladda upp ditt Drawing-paket till tjänsten Azure Maps Creator (för hands version) för att hämta ett `udid` för det överförda paketet. Anvisningar om hur du laddar upp ett paket finns i [Ladda upp ett ritnings paket](tutorial-creator-indoor-maps.md#upload-a-drawing-package).

2. Nu när ritnings paketet har överförts använder vi `udid` för det överförda paketet för att konvertera paketet till kartdata. Anvisningar om hur du konverterar ett paket finns i [konvertera ett ritnings paket](tutorial-creator-indoor-maps.md#convert-a-drawing-package).

    >[!NOTE]
    >Om konverterings processen lyckas får du inte någon länk till fel visualisera-verktyget.

3. Under fliken svars **rubriker** i Postman-programmet, letar du efter `diagnosticPackageLocation` egenskapen som returnerades av konverterings-API: et. Svaret bör se ut som följande JSON:

    ```json
    {
        "operationId": "77dc9262-d3b8-4e32-b65d-74d785b53504",
        "created": "2020-04-22T19:39:54.9518496+00:00",
        "status": "Failed",
        "resourceLocation": "https://atlas.microsoft.com/conversion/{conversionId}?api-version=1.0",
        "properties": {
            "diagnosticPackageLocation": "https://atlas.microsoft.com/mapData/ce61c3c1-faa8-75b7-349f-d863f6523748?api-version=1.0"
        }
    }
    ```

4. Hämta fel Visualiseraren för ritnings paket genom att göra en `HTTP-GET` begäran på URL: en `diagnosticPackageLocation` .

## <a name="setup"></a>Konfiguration

I det hämtade zippade paketet från `diagnosticPackageLocation` länken hittar du två filer.

* _VisualizationTool.zip_: innehåller käll koden, mediet och webb sidan för ritnings fel visualiseraren.
* _ConversionWarningsAndErrors.jspå_: innehåller en formaterad lista med varningar, fel och ytterligare information som används av fel visualiseraren för ritning.

Zippa upp _VisualizationTool.zip_ -mappen. Den innehåller följande objekt:

* mappen _till gångar_ : innehåller bilder och mediefiler
* _statisk_ mapp: käll kod
* _index.html_ -fil: webb programmet.

Öppna _index.html_ -filen med någon av Webbläsarena nedan, med respektive versions nummer. Du kan använda en annan version, om versionen har ett likvärdigt kompatibelt beteende som den listade versionen.

* Microsoft Edge 80
* Safari 13
* Chrome 80
* Firefox 74

## <a name="using-the-drawing-error-visualizer-tool"></a>Använda verktyget Drawing Error visualiserare

När du har startat verktyget Rita fel visualiserare visas sidan uppladdning. Överförings sidan innehåller en dra och släpp-ruta. List rutan dra & fungerar också som en knapp som öppnar en dialog ruta i Utforskaren.

:::image type="content" source="./media/drawing-errors-visualizer/start-page.png" alt-text="App för att rita fel visualiserare – start sida":::

_ConversionWarningsAndErrors.jspå_ filen har placerats i roten för den hämtade katalogen. Om du vill läsa in _ConversionWarningsAndErrors.jspå_ kan du antingen dra & släppa filen i rutan eller klicka på rutan, leta upp filen i dialog rutan för Utforskaren och sedan ladda upp filen.

:::image type="content" source="./media/drawing-errors-visualizer/loading-data.gif" alt-text="Att rita fel visualisera appar – dra och släpp för att läsa in data":::

När _ConversionWarningsAndErrors.js_ när filen har lästs in visas en lista över fel och varningar i ritnings paketet. Varje fel eller varning anges av lagret, nivån och ett detaljerat meddelande. Om du vill visa detaljerad information om ett fel eller en varning klickar du på länken **information** . Ett avsnitt som kan vara störande visas sedan under listan. Du kan nu navigera till varje fel för att lära dig mer om hur du löser problemet.

:::image type="content" source="./media/drawing-errors-visualizer/errors.png" alt-text="Drawing Error-program-fel och varningar":::

## <a name="next-steps"></a>Nästa steg

När ditt [Draw-paket uppfyller kraven](drawing-requirements.md)kan du använda [tjänsten Azure Maps data uppsättning](/rest/api/maps/conversion) för att konvertera ritnings paketet till en data uppsättning. Sedan kan du använda webb modulen inomhus Maps för att utveckla ditt program. Läs mer i följande artiklar:

> [!div class="nextstepaction"]
> [Fel koder för ritnings konvertering](drawing-conversion-error-codes.md)

> [!div class="nextstepaction"]
> [Skapare (för hands version) för inomhus kartor](creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Använda modulen inomhus Maps](how-to-use-indoor-module.md)

> [!div class="nextstepaction"]
> [Implementera dynamisk formatering i inomhus-karta](indoor-map-dynamic-styling.md)