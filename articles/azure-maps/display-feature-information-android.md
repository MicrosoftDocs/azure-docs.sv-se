---
title: Visa funktions information i Android Maps | Microsoft Azure Maps
description: Lär dig hur du visar information när användare interagerar med kart funktioner. Använd Azure Maps Android SDK för att visa popup-meddelanden och andra typer av meddelanden.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 37b5ab1c144ed81d995da40b87edeaccdcad7253
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680011"
---
# <a name="display-feature-information"></a>Visa funktionsinformation

Spatialdata representeras ofta med punkter, linjer och polygoner. Informationen innehåller ofta information om metadata. En punkt kan till exempel representera platsen för en restaurang och metadata om att restaurangens namn, adress och typ av livsmedel fungerar. Du kan lägga till dessa metadata som egenskaper för en interjson `Feature` . Följande kod skapar en enkel punkt funktion med en `title` egenskap som har värdet "Hello World!"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64));

//Add a property to the feature.
feature.addStringProperty("title", "Hello World!");

//Create a point feature, pass in the metadata properties, and add it to the data source.
source.add(feature);
```

Se avsnittet [skapa en data källa](create-data-source-android-sdk.md) för att skapa och lägga till data till kartan.

När en användare interagerar med en funktion på kartan kan händelser användas för att reagera på dessa åtgärder. Ett vanligt scenario är att visa ett meddelande om metadata-egenskaperna för en funktion som användaren interagerar med. `OnFeatureClick`Händelsen är den huvudsakliga händelsen som används för att identifiera när användaren tryckte på en funktion på kartan. Det finns också en `OnLongFeatureClick` händelse. När du lägger till `OnFeatureClick` händelsen i kartan kan den begränsas till ett enda lager genom att skicka i ID: t för ett lager för att begränsa det till. Om inget lager-ID skickas i, kan du trycka på en funktion på kartan, oavsett vilket lager det finns i, så att den här händelsen utlöses. Följande kod skapar ett symbol lager för att återge punkt data på kartan och lägger sedan till en `OnFeatureClick` händelse och begränsar den till det här symbol skiktet.

```java
//Create a symbol and add it to the map.
SymbolLayer layer = new SymbolLayer(source);
map.layers.add(layer);

//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).getStringProperty("title");

    //Do something with the message.
}, layer.getId());    //Limit this event to the symbol layer.
```

## <a name="display-a-toast-message"></a>Visa ett popup-meddelande

Ett popup-meddelande är ett av de enklaste sätten att visa information till användaren och är tillgänglig i alla versioner av Android. Den har inte stöd för någon typ av användarindata och visas bara under en kort tids period. Om du snabbt vill låta användaren veta vad de tryckte på, kan ett popup-meddelande vara ett bra alternativ. Följande kod visar hur ett popup-meddelande kan användas med `OnFeatureClick` händelsen.

```java
//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).getStringProperty("title");

    //Display a toast message with the title information.
    Toast.makeText(this, msg, Toast.LENGTH_SHORT).show();
}, layer.getId());    //Limit this event to the symbol layer.
```

![Animering av en funktion som knackas och ett popup-meddelande visas](./media/display-feature-information-android/symbol-layer-click-toast-message.gif)

Förutom popup-meddelanden finns det många andra sätt att presentera egenskaperna för metadata för en funktion, till exempel:

- [Snackbar-widget](https://developer.android.com/training/snackbar/showing.html)  -  `Snackbars` ge lätt feedback om en åtgärd. De visar ett kort meddelande längst ned på skärmen på mobilt och lägre till större enheter. `Snackbars` visas ovanför alla andra element på skärmen och bara en kan visas i taget.
- [Dialog rutor](https://developer.android.com/guide/topics/ui/dialogs) – en dialog ruta är ett litet fönster som efterfrågar användaren att fatta ett beslut eller ange ytterligare information. En dialog ruta fyller inte på skärmen och används vanligt vis för modala händelser som kräver att användarna vidtar en åtgärd innan de kan fortsätta.
- Lägg till ett [fragment](https://developer.android.com/guide/components/fragments) i den aktuella aktiviteten.
- Navigera till en annan aktivitet eller vy.

## <a name="next-steps"></a>Nästa steg

Lägga till mer data i kartan:

> [!div class="nextstepaction"]
> [Reagera på mappa händelser](android-map-events.md)

> [!div class="nextstepaction"]
> [Skapa en datakälla](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Lägga till ett symbolskikt](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Lägga till ett bubbelskikt](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Lägg till ett linjeskikt](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Lägg till ett polygonskikt](how-to-add-shapes-to-android-map.md)
