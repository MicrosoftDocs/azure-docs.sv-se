---
title: Begär höjnings data med hjälp av Azure Maps höjnings tjänsten (för hands version)
description: Lär dig hur du begär höjnings data med hjälp av Azure Maps-höjnings tjänsten (för hands version).
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: d14eda84144105bf2e04f1238284bc58a91c4c03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98684063"
---
# <a name="request-elevation-data-using-the-azure-maps-elevation-service-preview"></a>Begär höjnings data med hjälp av Azure Maps höjnings tjänsten (för hands version)

> [!IMPORTANT]
> Tjänsten Azure Maps höjning är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Tjänsten Azure Maps [höjning](/rest/api/maps/elevation) innehåller API: er för att fråga höjnings data var som helst på jordens yta. Du kan begära exempel på höjnings data längs sökvägar, inom en definierad avgränsnings ruta eller vid vissa koordinater. Du kan också använda [rendera v2 – Hämta API för kart panel](/rest/api/maps/renderv2) för att hämta höjnings data i panel format. Panelerna levereras i GeoTIFF raster-format. Den här artikeln visar hur du använder Azure Maps-höjnings tjänsten och API: t för att hämta kart panel för att begära utökade data. Det går att begära höjnings data i både formaten interjson och GeoTiff.

## <a name="prerequisites"></a>Förutsättningar

1. [Skapa ett Azure Maps konto i pris nivån S1](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Hämta en primär prenumerations nyckel](quick-demo-map-app.md#get-the-primary-key-for-your-account), även kallat primär nyckel eller prenumerations nyckel.

Om du vill ha mer information om autentisering i Azure Maps [hanterar du autentisering i Azure Maps](how-to-manage-authentication.md).

I den här artikeln används [Postman](https://www.postman.com/) -programmet, men du kan välja en annan API utvecklings miljö.

## <a name="request-elevation-data-in-raster-tiled-format"></a>Begär höjnings data i raster sida vid sida

Om du vill begära höjnings data i raster panels format använder du [kart panels-API: t rendering v2-get](/rest/api/maps/renderv2). Om panelen kan hittas, returnerar API panelen som en GeoTIFF. Annars returnerar API 0. Alla raster paneler visas med jordnings läget för GeoID (Sea-nivå). I det här exemplet ska vi begära höjnings data för MT. Everest.

>[!TIP]
>Om du vill hämta en panel vid ett speciellt område på kartan måste du hitta rätt panel på lämplig zoomnivå. Observera också att WorldDEM täcker hela den globala landmass men omfattar inte havs.  Mer information finns i [zoomnings nivåer och panel rutnät](zoom-levels-and-tile-grid.md).

1. Öppna Postman-appen. Längst upp i Postman-appen väljer du **nytt**. I fönstret **Skapa nytt** väljer du **samling**.  Namnge samlingen och välj knappen **skapa** .

2. Välj **nytt** om du vill skapa en begäran. I fönstret **Skapa nytt** väljer du **begäran**. Ange ett **namn** för begäran. Välj den samling som du skapade i föregående steg och välj sedan **Spara**.

3. Välj metoden **Hämta** http på fliken Builder och ange följande URL för att begära raster panelen. För den här begäran och andra begär Anden som nämns i den här artikeln ersätter `{Azure-Maps-Primary-Subscription-key}` du med den primära prenumerations nyckeln.

    ```http
    https://atlas.microsoft.com/map/tile?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=2.0&tilesetId=microsoft.dem&zoom=13&x=6074&y=3432
    ```

4. Klicka på knappen **Skicka** . Du bör få raster panelen som innehåller höjnings data i GeoTIFF-format. Varje pixel i raster panelens rå data är av typen `float` . Värdet för varje pixel representerar höjd i meter.

## <a name="request-elevation-data-in-geojson-format"></a>Begär höjnings data i interjson-format

Använd API: erna för upphöjnings tjänsten (för hands version) för att begära höjnings data i det interjson-formatet. I det här avsnittet visas var och en av de tre API: erna:

* [Hämta data för punkter](/rest/api/maps/elevation/getdataforpoints)
* [Publicera data för Poäng](/rest/api/maps/elevation/postdataforpoints)
* [Hämta data för polyline](/rest/api/maps/elevation/getdataforpolyline)
* [Publicera data för polylinje](/rest/api/maps/elevation/postdataforpolyline)
* [Hämta data för markerings ram](/rest/api/maps/elevation/getdataforboundingbox)

>[!IMPORTANT]
> När inga data kan returneras returneras alla API: er `0` .

### <a name="request-elevation-data-for-points"></a>Begär höjnings data för punkter

I det här exemplet ska vi använda [API: t hämta data för punkter](/rest/api/maps/elevation/getdataforpoints) för att begära höjnings data vid Mt. Everest och Chamlang berg. Sedan kommer vi att använda [data för Poäng-API: et för](/rest/api/maps/elevation/postdataforpoints) att begära höjnings data med samma två punkter. Latituder och longituder i URL-adressen förväntas vara i WGS84 (Geodetic System) decimaler.

 >[!IMPORTANT]
 >På grund av längden på URL: en för tecken längd på 2048 går det inte att skicka fler än 100 koordinater som en avgränsad pipeline-sträng i en URL GET-begäran. Om du vill skicka fler än 100 koordinater som en avgränsad pipeline-sträng använder du POST-data för punkter.

1. Välj **nytt** om du vill skapa en begäran. I fönstret **Skapa nytt** väljer du **begäran**. Ange ett **namn** för begäran. Välj den samling som du skapade i föregående steg och välj sedan **Spara**.

2. Välj metoden **Hämta** http på fliken Builder och ange följande URL. För den här begäran och andra begär Anden som nämns i den här artikeln ersätter `{Azure-Maps-Primary-Subscription-key}` du med den primära prenumerations nyckeln.

    ```http
    https://atlas.microsoft.com/elevation/point/json?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&points=-73.998672,40.714728|150.644,-34.397
    ```

3. Klicka på knappen **Skicka** .  Du får följande JSON-svar:

    ```json
    {
    "data": [
        {
            "coordinate": {
                "latitude": 40.714728,
                "longitude": -73.998672
            },
            "elevationInMeter": 12.142355447638208
        },
        {
            "coordinate": {
                "latitude": -34.397,
                "longitude": 150.644
            },
            "elevationInMeter": 384.47041445517846
        }
    ]
    }
    ```

4. Nu ska vi anropa data [för Points-API: et](/rest/api/maps/elevation/postdataforpoints) för att få utökade data för samma två punkter. Välj metoden **post** http på fliken Builder och ange följande URL. För den här begäran och andra begär Anden som nämns i den här artikeln ersätter `{Azure-Maps-Primary-Subscription-key}` du med den primära prenumerations nyckeln.

    ```http
    https://atlas.microsoft.com/elevation/point/json?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0
    ```

5. Ange som i **rubrikerna** för **post** -begäran `Content-Type` `application/json` . I **bröd texten** anger du koordinat punkts informationen nedan. När du är klar klickar du på **Skicka**.

     ```json
    [
        {
            "lon": -73.998672,
            "lat": 40.714728
        },
        {
            "lon": 150.644,
            "lat": -34.397
        }
    ]
    ```

### <a name="request-elevation-data-samples-along-a-polyline"></a>Begär data exempel för utökade data längs en sammansatt linje

I det här exemplet ska vi använda [Hämta data för polylinje](/rest/api/maps/elevation/getdataforpolyline) för att begära fem jämnt fördelade exempel på höjnings data längs en rät linje mellan koordinaterna vid Mt. Everest och Chamlang berg. Båda koordinaterna måste definieras i formatet Long/lat. Om du inte anger något värde för `samples` parametern är antalet exempel standardvärdet 10. Det maximala antalet prover är 2 000.

Sedan kommer vi att använda hämta data för polylinje för att begära tre lika stora exempel på höjnings data längs en bana. Vi definierar den exakta platsen för exemplen genom att skicka tre långa/lat-koordinater.

Slutligen använder vi [post-data för polyline-API](/rest/api/maps/elevation/postdataforpolyline) för att begära höjnings data vid samma tre lika stora exempel.

Latituder och longituder i URL-adressen förväntas vara i WGS84 (Geodetic System) decimaler.

 >[!IMPORTANT]
 >På grund av längden på URL: en för tecken längd på 2048 går det inte att skicka fler än 100 koordinater som en avgränsad pipeline-sträng i en URL GET-begäran. Om du vill skicka fler än 100 koordinater som en avgränsad pipeline-sträng använder du POST-data för punkter.

1. Välj **Nytt**. I fönstret **Skapa nytt** väljer du **begäran**. Ange ett **namn på begäran** och välj en samling. Klicka på **Spara**.

2. Välj metoden **Hämta** http på fliken Builder och ange följande URL. För den här begäran och andra begär Anden som nämns i den här artikeln ersätter `{Azure-Maps-Primary-Subscription-key}` du med den primära prenumerations nyckeln.

   ```http
    https://atlas.microsoft.com/elevation/line/json?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&lines=-73.998672,40.714728|150.644,-34.397&samples=5
    ```

3. Klicka på knappen **Skicka** .  Du får följande JSON-svar:

    ```JSON
    {
        "data": [
            {
                "coordinate": {
                    "latitude": 40.714728,
                    "longitude": -73.998672
                },
                "elevationInMeter": 12.14236
            },
            {
                "coordinate": {
                    "latitude": 21.936796000000001,
                    "longitude": -17.838003999999998
                },
                "elevationInMeter": 0.0
            },
            {
                "coordinate": {
                    "latitude": 3.1588640000000012,
                    "longitude": 38.322664000000003
                },
                "elevationInMeter": 598.66943
            },
            {
                "coordinate": {
                    "latitude": -15.619067999999999,
                    "longitude": 94.483332000000019
                },
                "elevationInMeter": 0.0
            },
            {
                "coordinate": {
                    "latitude": -34.397,
                    "longitude": 150.644
                },
                "elevationInMeter": 384.47041
            }
        ]
    }
    ```

4. Nu ska vi begära tre exempel på höjnings data längs en väg mellan koordinaterna vid montering av Everest, Chamlang och Jannu berg. I avsnittet **params** , kopierar du följande koordinat mat ris för värdet för `lines` Frågeparametern.

    ```html
        86.9797222, 27.775|86.9252778, 27.9880556 | 88.0444444, 27.6822222
    ```

5. Ändra `samples` värdet för Frågeparametern till `3` .  Bilden nedan visar de nya värdena.

     :::image type="content" source="./media/how-to-request-elevation-data/get-elevation-samples.png" alt-text="Hämta tre utökade data exempel.":::

6. Klicka på knappen blå **sändning** . Du får följande JSON-svar:

    ```json
    {
        "data": [
            {
                "coordinate": {
                    "latitude": 27.775,
                    "longitude": 86.9797222
                },
                "elevationInMeter": 7116.0348851572589
            },
            {
                "coordinate": {
                    "latitude": 27.737403546316028,
                    "longitude": 87.411180791156454
                },
                "elevationInMeter": 1798.6945512521534
            },
            {
                "coordinate": {
                    "latitude": 27.682222199999998,
                    "longitude": 88.0444444
                },
                "elevationInMeter": 7016.9372013588072
            }
        ]
    }
    ```

7. Nu anropar vi post- [data för polylinje-API](/rest/api/maps/elevation/postdataforpolyline) för att få utökade data för samma tre punkter. Välj metoden **post** http på fliken Builder och ange följande URL. För den här begäran och andra begär Anden som nämns i den här artikeln ersätter `{Azure-Maps-Primary-Subscription-key}` du med den primära prenumerations nyckeln.

    ```http
    https://atlas.microsoft.com/elevation/line/json?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&samples=5
    ```

8. Ange som i **rubrikerna** för **post** -begäran `Content-Type` `application/json` . I **bröd texten** anger du koordinat punkts informationen nedan. När du är klar klickar du på **Skicka**.

     ```json
    [
        {
            "lon": 86.9797222,
            "lat": 27.775
        },
        {
            "lon": 86.9252778,
            "lat": 27.9880556
        },
        {
            "lon": 88.0444444,
            "lat": 27.6822222
        }
    ]
    ```

### <a name="request-elevation-data-by-bounding-box"></a>Begär höjnings data efter avgränsnings ruta

Nu ska vi använda [rutan hämta data för att binda](/rest/api/maps/elevation/getdataforboundingbox) till att begära höjnings data nära Mt. Rainier, WA. Höjnings data returneras på lika stora platser inom en markerings ram. Det avgränsnings område som definieras av (2) uppsättningar av Lat/Long-koordinater (södra latitud, västra longitud | Nord latitud, östra longitud) är indelade i rader och kolumner. Kanterna i avgränsnings Rute kontot för två (2) av raderna och två (2) av kolumnerna. Utökade höjder returneras för rutnäts hörnen som skapas på rad-och kolumn skärnings punkter. Upp till 2000-höjningar kan returneras i en enskild begäran.

I det här exemplet ska vi ange rader = 3 och kolumnerna = 6. 18 höjnings värden returneras i svaret. I följande diagram ordnas upphöjnings värden från och med sydvästra hörn och fortsätter sedan väst till öst och syd till norr.  Höjd punkterna är numrerade i den ordning som de returneras.

:::image type="content" source="./media/how-to-request-elevation-data/bounding-box.png" border="false" alt-text="Koordinater för avgränsnings rutor på NE-och SE-hörn.":::

1. Välj **Nytt**. I fönstret **Skapa nytt** väljer du **begäran**. Ange ett **namn på begäran** och välj en samling. Klicka på **Spara**.

2. Välj metoden **Hämta** http på fliken Builder och ange följande URL. För den här begäran och andra begär Anden som nämns i den här artikeln ersätter `{Azure-Maps-Primary-Subscription-key}` du med den primära prenumerations nyckeln.

    ```http
    https://atlas.microsoft.com/elevation/lattice/json?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&bounds=-121.66853362143818, 46.84646479863713,-121.65853362143818, 46.85646479863713&rows=2&columns=3
    ```

3. Klicka på knappen blå **sändning** . 18 utökade data exempel, en för varje hörn i rutnätet, returneras i svaret.

    ```json
    {
    "data": [
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2298.6581875651746
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2306.3980756609963
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2279.3385479564113
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2233.1549264690366
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2196.4485923541492
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 2133.1756767157253
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2345.3227848228803
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2292.2449195443587
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2270.5867788258074
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2296.8311427390604
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2266.0729430891065
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 2242.216346631234
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2378.460838833359
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2327.6761137260387
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2208.3782743402949
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2106.9526472760981
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2054.3270174034078
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 2030.6438331110671
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2318.753153399402
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2253.88875188271
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2136.6145845357587
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2073.6734467948486
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2042.994055784251
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 1988.3631481900356
        }
    ]
    }
    ```

## <a name="samples-use-elevation-service-preview-apis-in-azure-maps-control"></a>Exempel: Använd API: er för utöknings tjänsten (för hands version) i Azure Maps kontroll

### <a name="get-elevation-data-by-coordinate-position"></a>Hämta höjnings data efter koordinat position

På följande exempel webb sida ser du hur du använder kart kontrollen för att Visa höjnings data i en koordinat punkt. När användaren drar markören visas höjnings data i en popup-karta.

<br/>

<iframe height="500" style="width:100%;" scrolling="no" title="Erhåll höjning vid position" src="https://codepen.io/azuremaps/embed/c840b510e113ba7cb32809591d5f96a2?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Se pennan <a href='https://codepen.io/azuremaps/pen/c840b510e113ba7cb32809591d5f96a2'>tillhöjningen vid position</a> genom Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="get-elevation-data-by-bounding-box"></a>Hämta höjnings data efter avgränsnings ruta

På följande exempel webb sida ser du hur du använder kart kontrollen för att visa utökade data i en markerings ruta. Användaren definierar avgränsnings rutan genom att klicka på `square` ikonen i det övre vänstra hörnet och rita fyr kanten var som helst på kartan. Kart kontrollen återger sedan höjnings data i enlighet med de färger som anges i nyckeln som finns i det övre högra hörnet.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Höjningar efter avgränsnings ruta" src="https://codepen.io/azuremaps/embed/619c888c70089c3350a3e95d499f3e48?height=500&theme-id=default&default-tab=js,result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Se rutorna för pen- <a href='https://codepen.io/azuremaps/pen/619c888c70089c3350a3e95d499f3e48'>höjning genom</a> att Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="get-elevation-data-by-polyline-path"></a>Hämta höjnings data med en polyline-sökväg

På följande exempel webb sida ser du hur du använder kart kontrollen för att Visa höjnings data längs en sökväg. Användaren definierar sökvägen genom att klicka på `Polyline` ikonen i det övre vänstra hörnet och rita en polyline på kartan. Kart kontrollen återger sedan utöknings data i färger som anges i nyckeln i det övre högra hörnet.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Tonings toning" src="https://codepen.io/azuremaps/embed/7bee08e5cb13d05cb0a11636b60f14ca?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Se <a href='https://codepen.io/azuremaps/pen/7bee08e5cb13d05cb0a11636b60f14ca'>Toningens tonings toning</a> efter Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Nästa steg

För att ytterligare utforska API: erna för Azure Maps höjning (för hands version), se:

> [!div class="nextstepaction"]
> [Höjning (för hands version) – Hämta data för lat-långa koordinater](/rest/api/maps/elevation/getdataforpoints)

> [!div class="nextstepaction"]
> [Höjning (för hands version) – Hämta data för markerings ram](/rest/api/maps/elevation/getdataforboundingbox)

> [!div class="nextstepaction"]
> [Höjning (för hands version) – Hämta data för polyline](/rest/api/maps/elevation/getdataforpolyline)

> [!div class="nextstepaction"]
> [Rendera v2 – Hämta kart panel](/rest/api/maps/renderv2)

En fullständig lista över Azure Maps REST API: er finns i:

> [!div class="nextstepaction"]
> [Azure Maps REST-API: er](/rest/api/maps/)