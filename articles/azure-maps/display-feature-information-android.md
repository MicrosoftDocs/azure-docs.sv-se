---
title: Visa funktions information i Android Maps | Microsoft Azure Maps
description: Lär dig hur du visar information när användare interagerar med kart funktioner. Använd Azure Maps Android SDK för att visa popup-meddelanden och andra typer av meddelanden.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: ea9ed2c74651a7719533340a24c4741d4e25b805
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105605730"
---
# <a name="display-feature-information"></a>Visa funktionsinformation

Spatialdata representeras ofta med punkter, linjer och polygoner. Informationen innehåller ofta information om metadata. En punkt kan till exempel representera platsen för en restaurang och metadata om att restaurangens namn, adress och typ av livsmedel fungerar. Du kan lägga till dessa metadata som egenskaper för en interjson `Feature` . Följande kod skapar en enkel punkt funktion med en `title` egenskap som har värdet "Hello World!"

::: zone pivot="programming-language-java-android"

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

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a point feature.
val feature = Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64))

//Add a property to the feature.
feature.addStringProperty("title", "Hello World!")

//Create a point feature, pass in the metadata properties, and add it to the data source.
source.add(feature)
```

::: zone-end

Se avsnittet [skapa en data källa](create-data-source-android-sdk.md) för att skapa och lägga till data till kartan.

När en användare interagerar med en funktion på kartan kan händelser användas för att reagera på dessa åtgärder. Ett vanligt scenario är att visa ett meddelande om metadata-egenskaperna för en funktion som användaren interagerar med. `OnFeatureClick`Händelsen är den huvudsakliga händelsen som används för att identifiera när användaren tryckte på en funktion på kartan. Det finns också en `OnLongFeatureClick` händelse. När du lägger till `OnFeatureClick` händelsen i kartan kan den begränsas till ett enda lager genom att skicka i ID: t för ett lager för att begränsa det till. Om inget lager-ID skickas i, kan du trycka på en funktion på kartan, oavsett vilket lager det finns i, så att den här händelsen utlöses. Följande kod skapar ett symbol lager för att återge punkt data på kartan och lägger sedan till en `OnFeatureClick` händelse och begränsar den till det här symbol skiktet.

::: zone pivot="programming-language-java-android"

```java
//Create a symbol and add it to the map.
SymbolLayer layer = new SymbolLayer(source);
map.layers.add(layer);

//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).getStringProperty("title");

    //Do something with the message.

    //Return a boolean indicating if event should be consumed or continue bubble up.
    return false;
}, layer.getId());    //Limit this event to the symbol layer.
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a symbol and add it to the map.
val layer = SymbolLayer(source)
map.layers.add(layer)

//Add a feature click event to the map.
map.events.add(OnFeatureClick { features: List<Feature> ->
    //Retrieve the title property of the feature as a string.
    val msg = features[0].getStringProperty("title")

    //Do something with the message.

    //Return a boolean indicating if event should be consumed or continue bubble up.
    return false
}, layer.getId()) //Limit this event to the symbol layer.
```

::: zone-end

## <a name="display-a-toast-message"></a>Visa ett popup-meddelande

Ett popup-meddelande är ett av de enklaste sätten att visa information till användaren och är tillgänglig i alla versioner av Android. Den har inte stöd för någon typ av användarindata och visas bara under en kort tids period. Om du snabbt vill låta användaren veta vad de tryckte på, kan ett popup-meddelande vara ett bra alternativ. Följande kod visar hur ett popup-meddelande kan användas med `OnFeatureClick` händelsen.

::: zone pivot="programming-language-java-android"

```java
//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).getStringProperty("title");

    //Display a toast message with the title information.
    Toast.makeText(this, msg, Toast.LENGTH_SHORT).show();

    //Return a boolean indicating if event should be consumed or continue bubble up.
    return false;
}, layer.getId());    //Limit this event to the symbol layer.
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Add a feature click event to the map.
map.events.add(OnFeatureClick { features: List<Feature> ->
    //Retrieve the title property of the feature as a string.
    val msg = features[0].getStringProperty("title")

    //Display a toast message with the title information.
    Toast.makeText(this, msg, Toast.LENGTH_SHORT).show()

    //Return a boolean indicating if event should be consumed or continue bubble up.
    return false
}, layer.getId()) //Limit this event to the symbol layer.
```

::: zone-end

![Animering av en funktion som knackas och ett popup-meddelande visas](media/display-feature-information-android/symbol-layer-click-toast-message.gif)

Förutom popup-meddelanden finns det många andra sätt att presentera egenskaperna för metadata för en funktion, till exempel:

- [Snackbar-widget](https://developer.android.com/training/snackbar/showing.html)  -  `Snackbars` ge lätt feedback om en åtgärd. De visar ett kort meddelande längst ned på skärmen på mobilt och lägre till större enheter. `Snackbars` visas ovanför alla andra element på skärmen och bara en kan visas i taget.
- [Dialog rutor](https://developer.android.com/guide/topics/ui/dialogs) – en dialog ruta är ett litet fönster som efterfrågar användaren att fatta ett beslut eller ange ytterligare information. En dialog ruta fyller inte på skärmen och används vanligt vis för modala händelser som kräver att användarna vidtar en åtgärd innan de kan fortsätta.
- Lägg till ett [fragment](https://developer.android.com/guide/components/fragments) i den aktuella aktiviteten.
- Navigera till en annan aktivitet eller vy.

## <a name="display-a-popup"></a>Visa ett popup-fönster

Azure Maps Android SDK är en `Popup` klass som gör det enkelt att skapa gränssnitts antecknings element som är förankrade till en placering på kartan. För popup-fönster måste du skicka i en vy med en relativ layout till `content` alternativet i popup-fönstret. Här är ett enkelt exempel på en layout som visar mörk text ovanpå en medan bakgrunden.

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:orientation="vertical"
    android:background="#ffffff"
    android:layout_margin="8dp"
    android:padding="10dp"

    android:layout_height="match_parent">

    <TextView
        android:id="@+id/message"
        android:layout_width="wrap_content"
        android:text=""
        android:textSize="18dp"
        android:textColor="#222"
        android:layout_height="wrap_content"
        android:width="200dp"/>

</RelativeLayout>
```

Om du antar att ovanstående layout är lagrad i en fil som kallas `popup_text.xml` i `res -> layout` mappen för en app, skapar följande kod en popup-meny, lägger till den i kartan. När användaren klickar på en funktion `title` visas egenskapen med hjälp av `popup_text.xml` layouten och längst ned i mitten av layouten till den angivna placeringen på kartan.

::: zone pivot="programming-language-java-android"

```java
//Create a popup and add it to the map.
Popup popup = new Popup();
map.popups.add(popup);

map.events.add((OnFeatureClick)(feature) -> {
    //Get the first feature and it's properties.
    Feature f = feature.get(0);
    JsonObject props = f.properties();

    //Retrieve the custom layout for the popup.
    View customView = LayoutInflater.from(this).inflate(R.layout.popup_text, null);

    //Access the text view within the custom view and set the text to the title property of the feature.
    TextView tv = customView.findViewById(R.id.message);
    tv.setText(props.get("title").getAsString());

    //Get the coordinates from the clicked feature and create a position object.
    List<Double> c = ((Point)(f.geometry())).coordinates();
    Position pos = new Position(c.get(0), c.get(1));

    //Set the options on the popup.
    popup.setOptions(
        //Set the popups position.
        position(pos),

        //Set the anchor point of the popup content.
        anchor(AnchorType.BOTTOM),

        //Set the content of the popup.
        content(customView)

        //Optionally, hide the close button of the popup.
        //, closeButton(false)
    );

    //Open the popup.
    popup.open();

    //Return a boolean indicating if event should be consumed or continue bubble up.
    return false;
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a popup and add it to the map.
val popup = Popup()
map.popups.add(popup)

map.events.add(OnFeatureClick { feature: List<Feature> ->
    //Get the first feature and it's properties.
    val f = feature[0]
    val props = f.properties()

    //Retrieve the custom layout for the popup.
    val customView: View = LayoutInflater.from(this).inflate(R.layout.popup_text, null)

    //Access the text view within the custom view and set the text to the title property of the feature.
    val tv: TextView = customView.findViewById(R.id.message)
    tv.text = props!!["title"].asString

    //Get the coordinates from the clicked feature and create a position object.
    val c: List<Double> = (f.geometry() as Point?).coordinates()
    val pos = Position(c[0], c[1])

    //Set the options on the popup.
    popup.setOptions( 
        //Set the popups position.
        position(pos),  

        //Set the anchor point of the popup content.
        anchor(AnchorType.BOTTOM),  

        //Set the content of the popup.
        content(customView) 

        //Optionally, hide the close button of the popup.
        //, closeButton(false)
    )

    //Open the popup.
    popup.open()

    //Return a boolean indicating if event should be consumed or continue bubble up.
    return false
})
```

::: zone-end

I följande skärm bild visas popup-fönster som visas när du klickar på funktioner och som ligger kvar på den angivna platsen på kartan när den flyttas.

![Animering av en popup som visas och kartan har flyttats med ett popup-fönster till en position på kartan](./media/display-feature-information-android/android-popup.gif)

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
