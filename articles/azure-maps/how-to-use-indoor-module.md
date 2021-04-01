---
title: Använd modulen Azure Maps inomhus Maps med Microsoft Creator Services (för hands version)
description: Lär dig hur du använder modulen Microsoft Azure Maps inomhus Maps för att återge Maps genom att bädda in modulens JavaScript-bibliotek.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/20/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: e527cf5fa6a7caaeaf56ea19d684dd0830d5ca8a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101708687"
---
# <a name="use-the-azure-maps-indoor-maps-module"></a>Använd modulen Azure Maps inomhus Maps

> [!IMPORTANT]
> Azure Maps Creator-tjänster finns för närvarande i en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Maps Web SDK innehåller *Azure Maps* inliggande modul. I modulen  *Azure Maps inomhus* kan du rendera inliggande Maps som skapats i Azure Maps Creator Services (för hands version) 

## <a name="prerequisites"></a>Förutsättningar

1. [Skapa ett Azure Maps konto](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Skapa en skapare (förhands granskning) resurs](how-to-manage-creator.md)
3. [Hämta en primär prenumerations nyckel](quick-demo-map-app.md#get-the-primary-key-for-your-account), även kallat primär nyckel eller prenumerations nyckel.
4. Kom igång `tilesetId` `statesetId` med [självstudierna för att skapa inomhus Maps](tutorial-creator-indoor-maps.md).
 Du måste använda dessa identifierare för att rendera inliggande kartor med modulen Azure Maps inomhus Maps.

## <a name="embed-the-indoor-maps-module"></a>Bädda in modulen inomhus Maps

Du kan installera och bädda in modulen *Azure Maps inomhus* på ett av två sätt.

Om du vill använda den globalt värdbaserade Azure Content Delivery Network-versionen av *Azure Maps* ingångs modul refererar du till följande JavaScript-och Formatmallslänkar i `<head>` HTML-filens element:

```html
<link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.css" type="text/css"/>
<script src="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.js"></script>
```

 Du kan också hämta *Azure Maps inomhus* -modulen. Den *Azure Maps inomhus* modulen innehåller ett klient bibliotek för åtkomst till Azure Maps-tjänster. Följ stegen nedan för att installera och läsa in modulen *inomhus* i webb programmet.  
  
  1. Installera [Azure-Maps-inomhus-paketet](https://www.npmjs.com/package/azure-maps-indoor).
  
      ```powershell
      >npm install azure-maps-indoor
      ```

  2. Referera till JavaScript-och format mal len *Azure Maps inomhus* modul i `<head>` HTML-filens element:

      ```html
      <link rel="stylesheet" href="node_modules/azure-maps-drawing-tools/dist/atlas-indoor.min.css" type="text/css" />
      <script src="node_modules/azure-maps-drawing-tools/dist/atlas-indoor.min.js"></script>
      ```

## <a name="instantiate-the-map-object"></a>Instansiera objektet Map

Skapa först ett *kart objekt*. *Map-objektet* kommer att användas i nästa steg för att skapa en instans av objektet *inomhus Manager* .  Koden nedan visar hur du instansierar Map- *objektet*:

```javascript
const subscriptionKey = "<Your Azure Maps Primary Subscription Key>";

const map = new atlas.Map("map-id", {
  //use your facility's location
  center: [-122.13203, 47.63645],
  //or, you can use bounds: [# west, # south, # east, # north] and replace # with your map's bounds
  style: "blank",
  view: 'Auto',
  authOptions: {
      authType: 'subscriptionKey',
      subscriptionKey: subscriptionKey
  },
  zoom: 19,
});
```

## <a name="instantiate-the-indoor-manager"></a>Instansiera inomhus Manager

Om du vill läsa in panelernas tilesets och kart stil måste du instansiera den *inomhus Manager*. Instansiera den *inomhus Manager* genom att tillhandahålla *Map-objektet* och motsvarande `tilesetId` . Om du vill ha stöd för [dynamisk Map-formatering](indoor-map-dynamic-styling.md)måste du skicka `statesetId` . `statesetId`Variabel namnet är Skift läges känsligt. Koden bör likna JavaScript-koden nedan.

```javascript
const tilesetId = "<tilesetId>";
const statesetId = "<statesetId>";

const indoorManager = new atlas.indoor.IndoorManager(map, {
    tilesetId: tilesetId,
    statesetId: statesetId // Optional
});
```

Om du vill aktivera avsökning av tillstånds data du anger måste du ange `statesetId` och anropa `indoorManager.setDynamicStyling(true)` . Med avsöknings tillstånds data kan du dynamiskt uppdatera tillståndet för dynamiska egenskaper eller *tillstånd*. Till exempel kan en funktion som till exempel rum ha en dynamisk egenskap (*State*) som kallas `occupancy` . Programmet kanske vill avsöka efter *status* ändringar för att avspegla ändringen i den visuella kartan. Koden nedan visar hur du aktiverar tillstånds avsökning:

```javascript
const tilesetId = "<tilesetId>";
const statesetId = "<statesetId>";

const indoorManager = new atlas.indoor.IndoorManager(map, {
    tilesetId: tilesetId,
    statesetId: statesetId // Optional
});

if (statesetId.length > 0) {
    indoorManager.setDynamicStyling(true);
}
```

## <a name="indoor-level-picker-control"></a>Väljare för kontroll över inomhus-nivå

 Med kontrollen över-på-sidan- *väljare* kan du ändra nivån på den renderade kartan. Du kan välja att initiera kontroll av *väljare på inomhus-nivå* via den *inomhus Manager*. Här är koden för att initiera kontroll väljaren för nivå:

```javascript
const levelControl = new atlas.control.LevelControl({ position: "top-right" });
indoorManager.setOptions({ levelControl });
```

## <a name="indoor-events"></a>Inomhus-händelser

 *Azure Maps inomhus* -modulen har stöd för *mappnings objekt* händelser. Händelse lyssnarna i *Map-objektet* anropas när en nivå eller funktion har ändrats. Om du vill köra kod när en nivå eller en funktion har ändrats placerar du koden inuti händelse lyssnaren. Koden nedan visar hur händelse avlyssningar kan läggas till i *objektet Map*.

```javascript
map.events.add("levelchanged", indoorManager, (eventData) => {

  //code that you want to run after a level has been changed
  console.log("The level has changed: ", eventData);
});

map.events.add("facilitychanged", indoorManager, (eventData) => {

  //code that you want to run after a facility has been changed
  console.log("The facility has changed: ", eventData);
});
```

`eventData`Variabeln innehåller information om nivån eller funktionen som anropade respektive `levelchanged` `facilitychanged` -händelsen. När en nivå ändras `eventData` innehåller objektet `facilityId` , de nya `levelNumber` och andra metadata. När en funktion ändras `eventData` kommer objektet att innehålla nya `facilityId` , nya `levelNumber` och andra metadata.

## <a name="example-use-the-indoor-maps-module"></a>Exempel: Använd modulen inomhus Maps

Det här exemplet visar hur du använder modulen *Azure Maps inomhus* i ditt webb program. Även om exemplet är begränsat till omfånget, täcker det grunderna för vad du behöver för att komma igång med hjälp av modulen *Azure Maps inomhus* . Den fullständiga HTML-koden är under de här stegen.

1. Använd Azure Content Delivery Network- [alternativet](#embed-the-indoor-maps-module) för att installera *Azure Maps inomhus* -modulen.

2. Skapa en ny HTML-fil

3. I HTML-huvudet refererar du till JavaScript-och Style Sheet-formaten i *Azure Maps inomhus* -modulen.

4. Initiera ett *kart objekt*. *Map-objektet* har stöd för följande alternativ:
    - `Subscription key` är din Azure Maps primära prenumerations nyckel.
    - `center` definierar en latitud och en longitud för platsen för kartan Center. Ange ett värde för `center` om du inte vill ange ett värde för `bounds` . Formatet ska visas som `center` : [-122,13315, 47,63637].
    - `bounds` är den minsta rektangulära formen som innesluter TILESET-kart data. Ange ett värde för `bounds` om du inte vill ange ett värde för `center` . Du kan hitta mappnings gränserna genom att anropa [TILESET List-API: et](/rest/api/maps/tileset/listpreview). TILESET List-API: et returnerar `bbox` , som du kan parsa och tilldela till `bounds` . Formatet ska visas som `bounds` : [# väst, # syd, # öst, # Nord].
    - `style` gör att du kan ange bakgrunds färgen. Om du vill visa en vit bakgrund definierar du `style` som "Tom".
    - `zoom` gör att du kan ange lägsta och högsta zoomnings nivå för kartan.

5. Skapa sedan modulen *inomhus Manager* . Tilldela *Azure Maps i inomhus* `tilesetId` och Lägg eventuellt till `statesetId` .

6. Skapa en instans av *väljar* kontrollen på inomhus-nivå.

7. Lägg till händelse lyssnare för *kart objekt* .  

Filen bör nu se ut ungefär som i HTML-koden nedan.

  ```html
  <!DOCTYPE html>
  <html lang="en">
    <head>
      <meta charset="utf-8" />
      <meta name="viewport" content="width=device-width, user-scalable=no" />
      <title>Indoor Maps App</title>
      
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.css" type="text/css"/>

      <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
      <script src="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.js"></script>
        
      <style>
        html,
        body {
          width: 100%;
          height: 100%;
          padding: 0;
          margin: 0;
        }

        #map-id {
          width: 100%;
          height: 100%;
        }
      </style>
    </head>

    <body>
      <div id="map-id"></div>
      <script>
        const subscriptionKey = "<Your Azure Maps Primary Subscription Key>";
        const tilesetId = "<your tilesetId>";
        const statesetId = "<your statesetId>";

        const map = new atlas.Map("map-id", {
          //use your facility's location
          center: [-122.13315, 47.63637],
          //or, you can use bounds: [# west, # south, # east, # north] and replace # with your Map bounds
          style: "blank",
          view: 'Auto',
          authOptions: { 
              authType: 'subscriptionKey',
              subscriptionKey: subscriptionKey
          },
          zoom: 19,
        });

        const levelControl = new atlas.control.LevelControl({
          position: "top-right",
        });

        const indoorManager = new atlas.indoor.IndoorManager(map, {
          levelControl: levelControl, //level picker
          tilesetId: tilesetId,
          statesetId: statesetId // Optional
        });

        if (statesetId.length > 0) {
          indoorManager.setDynamicStyling(true);
        }

        map.events.add("levelchanged", indoorManager, (eventData) => {
          //put code that runs after a level has been changed
          console.log("The level has changed:", eventData);
        });

        map.events.add("facilitychanged", indoorManager, (eventData) => {
          //put code that runs after a facility has been changed
          console.log("The facility has changed:", eventData);
        });
      </script>
    </body>
  </html>
  ```

Om du vill se din inaktuella karta kan du läsa in den i en webbläsare. Den bör se ut som på bilden nedan. Om du klickar på Stairwell-funktionen visas *nivå väljaren* i det övre högra hörnet.

  ![bild av inomhus karta](media/how-to-use-indoor-module/indoor-map-graphic.png)

[Se live demo](https://azuremapscodesamples.azurewebsites.net/?sample=Creator%20indoor%20maps)

## <a name="next-steps"></a>Nästa steg

Läs om de API: er som är relaterade till *Azure Maps inomhus* modul:

> [!div class="nextstepaction"]
> [Krav för ritningspaket](drawing-requirements.md)

>[!div class="nextstepaction"]
> [Skapare (för hands version) för inomhus kartor](creator-indoor-maps.md)

Lär dig mer om hur du lägger till mer data i kartan:

> [!div class="nextstepaction"]
> [Dynamisk formatering för inomhus-kartor](indoor-map-dynamic-styling.md)

> [!div class="nextstepaction"]
> [Kodexempel](/samples/browse/?products=azure-maps)