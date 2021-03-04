---
title: Lägg till ett symbol lager till Android Maps | Microsoft Azure Maps
description: Lär dig hur du lägger till en markör till en karta. Se ett exempel som använder Azure Maps Android SDK för att lägga till ett symbol lager som innehåller punktbaserade data från en data källa.
author: rbrundritt
ms.author: richbrun
ms.date: 12/08/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 1706b60a61bd3b507d9fbcf555e478b388f51168
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102047578"
---
# <a name="add-a-symbol-layer-android-sdk"></a>Lägg till ett symbol lager (Android SDK)

Den här artikeln visar hur du återger punkt data från en data källa som ett symbol lager på en karta med hjälp av Azure Maps Android SDK. Symbol lager återger punkter som en bild och text på kartan.

> [!TIP]
> Symbol lager som standard återger koordinaterna för alla Geometries i en data källa. Om du vill begränsa lagret så att det bara återger punkt geometri funktioner, anger du `filter` alternativet för lagret till `eq(geometryType(), "Point")` . Om du vill inkludera MultiPoint-funktioner anger du även `filter` alternativet för skiktet `any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint"))` .

## <a name="prerequisites"></a>Förutsättningar

Se till att slutföra stegen i snabb starten [: skapa ett Android-appaket](quick-android-map.md) . Kodblock i den här artikeln kan infogas i `onReady` händelse hanteraren Maps.

## <a name="add-a-symbol-layer"></a>Lägga till ett symbolskikt

Innan du kan lägga till ett symbol lager till kartan måste du utföra några steg. Börja med att skapa en data källa och Lägg till den på kartan. Skapa ett symbol lager. Sedan skickar du data källan till symbol lagret för att hämta data från data källan. Slutligen lägger du till data i data källan så att det finns något att återge.

Koden nedan visar vad som ska läggas till i kartan när den har lästs in. Det här exemplet återger en enda punkt på kartan med ett symbol lager.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point and add it to the data source.
source.add(Point.fromLngLat(0, 0));

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source);

//Add the layer to the map.
map.layers.add(layer);
```

Det finns tre olika typer av punkt data som kan läggas till i kartan:

- Geometrisk punkt geometri – det här objektet innehåller bara en koordinat för en punkt och inget annat. Den `Point.fromLngLat` statiska metoden kan användas för att enkelt skapa dessa objekt.
- Real JSON MultiPoint-geometri – det här objektet innehåller koordinaterna för flera punkter och inget annat. Skicka en matris med punkter i `MultiPoint` klassen för att skapa dessa objekt.
- Funktionen för geometriskt JSON – det här objektet består av valfri geometrisk geometri och en uppsättning egenskaper som innehåller metadata som är kopplade till geometrin.

Mer information finns i avsnittet [skapa ett data käll](create-data-source-android-sdk.md) dokument för att skapa och lägga till data till kartan.

Följande kod exempel skapar en geometrisk punkt geometri och skickar den till funktionen för geometriska JSON och har ett `title` värde som har lagts till i dess egenskaper. `title`Egenskapen visas som text ovanför symbol ikonen på kartan.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(0, 0));

//Add a property to the feature.
feature.addStringProperty("title", "Hello World!");

//Add the feature to the data source.
source.add(feature);

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source, 
    //Get the title property of the feature and display it on the map.
    textField(get("title"))
);

//Add the layer to the map.
map.layers.add(layer);
```

Följande skärm bild visar ovanstående kod Rending en punkt funktion med hjälp av en ikon och text etikett med ett symbol lager.

![Karta med punkt åter givning med ett symbol lager som visar en ikon och text etikett för en punkt funktion](media/how-to-add-symbol-to-android-map/android-map-pin.png)

> [!TIP]
> Som standard optimerar symbol lager åter givningen av symboler genom att dölja symboler som överlappar varandra. När du zoomar in blir de dolda symbolerna synliga. Om du vill inaktivera den här funktionen och återge alla symboler hela tiden ställer du in `iconAllowOverlap` `textAllowOverlap` alternativen och `true` .

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Lägga till en anpassad ikon i ett symbol lager

Symbol lager återges med WebGL. Alla resurser, till exempel ikon bilder, måste läsas in i WebGL-kontexten. Det här exemplet visar hur du lägger till en anpassad ikon i kart resurserna. Den här ikonen används sedan för att återge punkt data med en anpassad symbol på kartan. För `textField` symbol skiktets egenskap krävs att ett uttryck anges. I det här fallet vill vi återge temperatur egenskapen. Eftersom temperatur är ett tal måste det konverteras till en sträng. Dessutom vill vi lägga till "°F". Ett uttryck kan användas för att utföra den här sammanfogningen. `concat(Expression.toString(get("temperature")), literal("°F"))`.

```java
//Load a custom icon image into the image sprite of the map.
map.images.add("my-custom-icon", R.drawable.showers);

//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-73.985708, 40.75773));

//Add a property to the feature.
feature.addNumberProperty("temperature", 64);

//Add the feature to the data source.
source.add(feature);

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source,
    iconImage("my-custom-icon"),
    iconSize(0.5f),

    //Get the title property of the feature and display it on the map.
    textField(concat(Expression.toString(get("temperature")), literal("°F"))),
    textOffset(new Float[]{0f, -1.5f})
);
```

I det här exemplet lästes följande bild in i appens drawable-mapp.

| ![Väder ikonens bild av regn duschar](media/how-to-add-symbol-to-android-map/showers.png)|
|:-----------------------------------------------------------------------:|
| showers.png                                                  |

Följande skärm bild visar ovanstående kod Rending en punkt funktion med hjälp av en anpassad ikon och formaterad text etikett med ett symbol lager.

![Karta med punkt åter givning med ett symbol lager som visar en anpassad ikon och formaterad text etikett för en punkt funktion](media/how-to-add-symbol-to-android-map/android-custom-symbol-layer.png)

> [!TIP]
> Om du bara vill rendera text med ett symbol lager kan du dölja ikonen genom `iconImage` att ange egenskapen för ikon alternativen till `"none"` .

## <a name="modify-symbol-colors"></a>Ändra symbol färger

Azure Maps Android SDK levereras med en uppsättning fördefinierade färg variationer för standard markör ikonen. Kan till exempel `marker-red` skickas till `iconImage` alternativet för ett symbol lager för att återge en röd version av markörens ikon i lagret. 

```java
SymbolLayer layer = new SymbolLayer(source,
    iconImage("marker-red")
);
```

I tabellen nedan visas alla avbildnings namn för inbyggda ikoner som är tillgängliga. Alla dessa märken hämtar sina färger från färg resurser som du kan åsidosätta. Förutom att åsidosätta huvud fyllnings färgen för den här markören. Observera dock att åsidosätta färgen på en av dessa markörer gäller dock för alla lager som använder den ikon bilden.

| Ikon bildens namn | Färg resurs namn |
|-----------------|---------------------|
| `marker-default` | `mapcontrol_marker_default` |
| `marker-black` | `mapcontrol_marker_black` |
| `marker-blue` | `mapcontrol_marker_blue` |
| `marker-darkblue` | `mapcontrol_marker_darkblue` |
| `marker-red` | `mapcontrol_marker_red` |
| `marker-yellow` | `mapcontrol_marker_yellow` |

Du kan även åsidosätta kant linje färgen för alla markörer med hjälp av `mapcontrol_marker_border` färg resurs namnet. Färgerna på dessa markörer kan åsidosättas genom att du lägger till en färg med samma namn i `colors.xml` appens fil. Följande fil skulle till exempel `colors.xml` göra standard markör färgen ljus grönt.

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <color name="mapcontrol_marker_default">#00FF00</color>
</resources>
```

Följande är en ändrad version av standard markör vektorn XML som du kan ändra för att skapa ytterligare anpassade versioner av standard markören. Den ändrade versionen kan läggas till i `drawable` mappen i din app och läggas till i Maps-bildspriten med hjälp av `map.images.add` och sedan användas med ett symbol lager.

```xml
<vector xmlns:android="http://schemas.android.com/apk/res/android"
    android:width="24.5dp"
    android:height="36.5dp"
    android:viewportWidth="24.5"
    android:viewportHeight="36.5">
    <path
        android:pathData="M12.25,0.25a12.2543,12.2543 0,0 0,-12 12.4937c0,6.4436 6.4879,12.1093 11.059,22.5641 0.5493,1.2563 1.3327,1.2563 1.882,0C17.7621,24.8529 24.25,19.1857 24.25,12.7437A12.2543,12.2543 0,0 0,12.25 0.25Z"
        android:strokeWidth="0.5"
        android:fillColor="@color/mapcontrol_marker_default"
        android:strokeColor="@color/mapcontrol_marker_border"/>
</vector>
```

## <a name="next-steps"></a>Nästa steg

Se följande artiklar för fler kod exempel som du kan lägga till i dina kartor:

> [!div class="nextstepaction"]
> [Skapa en datakälla](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Lägga till ett bubbelskikt](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Använda datadrivna formatuttryck](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Visa funktionsinformation](display-feature-information-android.md)
