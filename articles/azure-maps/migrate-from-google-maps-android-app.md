---
title: Självstudie – migrera en Android-app | Microsoft Azure Maps
description: Självstudie om hur du migrerar en Android-app från Google Maps till Microsoft Azure Maps
author: rbrundritt
ms.author: richbrun
ms.date: 08/19/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: f4b0642ce54b862b4d4c7b9663cf10e74b206281
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680483"
---
# <a name="tutorial-migrate-an-android-app-from-google-maps"></a>Självstudie: Migrera en Android-app från Google Maps

Azure Maps Android SDK har ett API-gränssnitt som liknar webb-SDK: n. Om du har utvecklat en av dessa SDK: er gäller många av samma koncept, bästa praxis och arkitekturer. I den här självstudien får du lära dig hur man:

> [!div class="checklist"]
> * Läsa in en karta
> * Lokalisera en karta
> * Lägg till markörer, polystreck och polygoner.
> * Täcka över ett panel lager
> * Visa trafikdata

Azure Maps Android SDK stöder en lägsta Android-version av API 21: Android-5.0.0 (Lollipop).

Alla exempel finns i Java. Du kan dock använda Kotlin med Azure Maps Android SDK.

Mer information om hur du utvecklar med Android SDK genom att Azure Maps finns i [instruktions guiderna för Azure Maps Android SDK](how-to-use-android-map-control-library.md).

## <a name="prerequisites"></a>Förutsättningar

1. Skapa ett Azure Maps-konto genom att logga in på [Azure Portal](https://portal.azure.com). Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
2. [Skapa ett Azure Maps konto](quick-demo-map-app.md#create-an-azure-maps-account)
3. [Hämta en primär prenumerations nyckel](quick-demo-map-app.md#get-the-primary-key-for-your-account), även kallat primär nyckel eller prenumerations nyckel. Mer information om autentisering i Azure Maps finns i [hantera autentisering i Azure Maps](how-to-manage-authentication.md).

## <a name="load-a-map"></a>Läsa in en karta

Att läsa in en karta i en Android-app med Google eller Azure Maps består av liknande steg. När du använder något av SDK: n måste du:

* Hämta en API-eller prenumerations nyckel för att få åtkomst till någon av plattformarna.
* Lägg till en del XML i en aktivitet för att ange var kartan ska återges och hur den ska anges.
* Åsidosätt alla livs cykel metoder från aktiviteten som innehåller kart visningen till motsvarande metoder i kart klassen. I synnerhet måste du åsidosätta följande metoder:
  * `onCreate(Bundle)`
  * `onStart()`
  * `onResume()`
  * `onPause()`
  * `onStop()`
  * `onDestroy()`
  * `onSaveInstanceState(Bundle)`
  * `onLowMemory()`
* Vänta tills kartan är klar innan du försöker komma åt och program mera.

### <a name="before-google-maps"></a>Före: Google Maps

Om du vill visa en karta med Google Maps SDK för Android gör du så här:

1. Se till att Google Play-tjänsterna är installerade.
2. Lägg till ett beroende för Google Maps-tjänsten till modulens  **gradle. Build** -fil:

    `implementation 'com.google.android.gms:play-services-maps:17.0.0'`

3. Lägg till en Google Maps API-nyckel i program avsnittet i  **Google \_ Maps \_api.xml** -filen:

    ```xml
    <meta-data android:name="com.google.android.geo.API_KEY" android:value="YOUR_GOOGLE_MAPS_KEY"/>
    ```

4. Lägg till ett kart fragment i huvud aktiviteten:

    ```xml
    <com.google.android.gms.maps.MapView
            android:id="@+id/myMap"
            android:layout_width="match_parent"
            android:layout_height="match_parent"/>
    ```

5. I **MainActivity. java** -filen måste du importera Google Maps SDK. Vidarebefordra alla livs cykel metoder från den aktivitet som innehåller kart visningen till motsvarande i Map-klassen. Hämta en `MapView` instans från kart fragmentet med hjälp av `getMapAsync(OnMapReadyCallback)` metoden. `MapView`Automatiskt initierar Maps-systemet och vyn. Redigera filen **MainActivity. java** enligt följande:

    ```java
    import com.google.android.gms.maps.GoogleMap;
    import com.google.android.gms.maps.MapView;
    import com.google.android.gms.maps.OnMapReadyCallback;
 
    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    
    public class MainActivity extends AppCompatActivity implements OnMapReadyCallback {
    
        MapView mapView;
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mapView = findViewById(R.id.myMap);
    
            mapView.onCreate(savedInstanceState);
            mapView.getMapAsync(this);
        }
    
        @Override
    
        public void onMapReady(GoogleMap map) {
            //Add your post map load code here.
        }
    
        @Override
        public void onResume() {
            super.onResume();
            mapView.onResume();
        }
    
        @Override
        protected void onStart(){
            super.onStart();
            mapView.onStart();
        }
    
        @Override
        public void onPause() {
            super.onPause();
            mapView.onPause();
        }
    
        @Override
        public void onStop() {
            super.onStop();
            mapView.onStop();
        }
    
        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapView.onLowMemory();
        }
    
        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapView.onDestroy();
        }
    
        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapView.onSaveInstanceState(outState);
        }
    }
    ```

När du kör ett program läses kart kontrollen in som i följande bild.

![Enkla Google Maps](media/migrate-google-maps-android-app/simple-google-maps.png)

### <a name="after-azure-maps"></a>Efter: Azure Maps

Om du vill visa en karta som använder Azure Maps SDK för Android måste du utföra följande steg:

1. Öppna filen **build. gradle** på den översta nivån och Lägg till följande kod i avsnittet **alla projekt** block:

    ```java
    maven {
        url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Uppdatera **appen/build. gradle** och Lägg till följande kod i den:

    1. Kontrol lera att projektets **minSdkVersion** är i API 21 eller högre.

    2. Lägg till följande kod i Android-avsnittet:

        ```java
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```

    3. Uppdatera beroende blocket. Lägg till en ny implementerings beroende linje för den senaste Azure Maps Android SDK: n:

        ```Java
        implementation "com.microsoft.azure.maps:mapcontrol:0.6"
        ```

        > [!Note]
        > Du kan ange versions numret till "0 +" för att koden alltid ska peka på den senaste versionen.

    4. Gå till **filen** i verktygsfältet och klicka sedan på **Synkronisera projekt med Gradle-filer**.

3. Lägg till ett kart fragment i huvud aktiviteten (resurser PWD- \> layout \> aktivitet \_main.xml):

    ```XML
    <?xml version="1.0" encoding="utf-8"?>
    <FrameLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        >

        <com.microsoft.azure.maps.mapcontrol.MapControl
            android:id="@+id/mapcontrol"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            />
    </FrameLayout>
    ```

4. I filen **MainActivity. java** måste du:

    * Importerar Azure Maps SDK
    * Ange information om Azure Maps-autentisering
    * Hämta kart kontroll instansen i **onCreate** -metoden

     Ange autentiseringsinformationen i `AzureMaps` klassen med hjälp av `setSubscriptionKey` `setAadProperties` metoderna eller. Den här globala uppdateringen, se till att du lägger till autentiseringsinformation i alla vyer.

    Kart kontrollen innehåller sina egna livs cykel metoder för att hantera Android: s OpenGL-livscykel. Dessa metoder måste anropas direkt från den inneslutna aktiviteten. För att kunna anropa kart kontrollens livs cykel metoder måste du åsidosätta följande livs cykel metoder i den aktivitet som innehåller kart kontrollen. Anropa respektive kart kontroll metod.

    * `onCreate(Bundle)`
    * `onStart()`
    * `onResume()`
    * `onPause()`
    * `onStop()`
    * `onDestroy()`
    * `onSaveInstanceState(Bundle)`
    * `onLowMemory()`

    Redigera filen **MainActivity. java** enligt följande:

    ```Java
    package com.example.myapplication;
    
    //For older versions use: import android.support.v7.app.AppCompatActivity; 
    import androidx.appcompat.app.AppCompatActivity;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.options.MapStyle;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;
    
    public class MainActivity extends AppCompatActivity {
    
    static {
        AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");

        //Alternatively use Azure Active Directory authenticate.
        //AzureMaps.setAadProperties("<Your aad clientId>", "<Your aad AppId>", "<Your aad Tenant>");
    }

    MapControl mapControl;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mapControl = findViewById(R.id.mapcontrol);

        mapControl.onCreate(savedInstanceState);

        //Wait until the map resources are ready.
        mapControl.onReady(map -> {
            //Add your post map load code here.

        });
    }

    @Override
    public void onResume() {
        super.onResume();
        mapControl.onResume();
    }

    @Override
    protected void onStart(){
        super.onStart();
        mapControl.onStart();
    }

    @Override
    public void onPause() {
        super.onPause();
        mapControl.onPause();
    }

    @Override
    public void onStop() {
        super.onStop();
        mapControl.onStop();
    }

    @Override
    public void onLowMemory() {
        super.onLowMemory();
        mapControl.onLowMemory();
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        mapControl.onDestroy();
    }

    @Override
    protected void onSaveInstanceState(Bundle outState) {
        super.onSaveInstanceState(outState);
        mapControl.onSaveInstanceState(outState);
    }}
    ```

Om du kör programmet kommer kart kontrollen att läsas in som i följande bild.

![Enkel Azure Maps](media/migrate-google-maps-android-app/simple-azure-maps.png)

Observera att Azure Maps kontrollen har stöd för att zooma ut mer och innehåller mer av en världs visning.

> [!TIP]
> Om du använder en Android-emulator på en Windows-dator kan kartan inte återges på grund av konflikter med OpenGL och accelererad bild åter givning i program vara. Följande har fungerat, för vissa personer, för att lösa det här problemet. Öppna AVD Manager och välj den virtuella enhet som ska redige ras. Rulla ned på panelen **Verifiera konfiguration** . I avsnittet **emulerade prestanda** ställer du in alternativet **Graphics** på **maskin vara**.

## <a name="localizing-the-map"></a>Lokalisera kartan

Lokalisering är viktigt om mål gruppen är uppdelad i flera länder/regioner eller talar olika språk.

### <a name="before-google-maps"></a>Före: Google Maps

Lägg till följande kod i- `onCreate` metoden för att ange kartans språk. Du måste lägga till koden innan du anger kartans sammanhang. Språk koden "fr" begränsar språket till franska.

```java
String languageToLoad = "fr";
Locale locale = new Locale(languageToLoad);
Locale.setDefault(locale);
Configuration config = new Configuration();
config.locale = locale;
getBaseContext().getResources().updateConfiguration(config,
        getBaseContext().getResources().getDisplayMetrics());
```

Här är ett exempel på Google Maps med språket inställt på "fr".

![Översättning av Google Maps](media/migrate-google-maps-android-app/google-maps-localization.png)

### <a name="after-azure-maps"></a>Efter: Azure Maps

Azure Maps tillhandahåller tre olika sätt att ange språket och den regionala vyn för kartan. Det första alternativet är att skicka information om språket och den regionala vyn till- `AzureMaps` klassen. Det här alternativet använder de `setLanguage` statiska `setView` metoderna och metoderna globalt. Standard språket och den regionala vyn ställs in över alla Azure Maps kontroller som läses in i din app. I det här exemplet anges franska med språk koden "fr-FR".

```java
static {
    //Set your Azure Maps Key.
    AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

    //Set the language to be used by Azure Maps.
    AzureMaps.setLanguage("fr-FR");

    //Set the regional view to be used by Azure Maps.
    AzureMaps.setView("Auto");
}
```

Det andra alternativet är att skicka språket och Visa information till kart kontrollens XML-kod.

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="Auto"
    />
```

Det tredje alternativet är att program mera språket och den nationella kart visningen med hjälp av Maps- `setStyle` metoden. Med det här alternativet uppdateras språket och den regionala vyn när koden körs.

```java
mapControl.onReady(map -> {
    map.setStyle(
        language("fr-FR"),
        view("Auto")
    );
});
```

Här är ett exempel på Azure Maps med språket inställt på "fr-FR".

![Azure Maps lokalisering](media/migrate-google-maps-android-app/azure-maps-localization.png)

Granska den fullständiga listan över [språk som stöds](supported-languages.md).

## <a name="setting-the-map-view"></a>Ställa in Map-vyn

Dynamiska kartor i både Azure Maps och Google Maps kan flyttas program mässigt till nya geografiska platser genom att anropa lämpliga metoder. Låt oss göra kartan Visa satellit antenn bilder, centrera kartan över en plats med koordinater och ändra zoomnings nivån. I det här exemplet använder vi Latitude: 35,0272, longitud:-111,0225 och zoomnings nivån 15.

### <a name="before-google-maps"></a>Före: Google Maps

Kameran med Google Maps Map-kontrollen kan flyttas program mässigt med `moveCamera` metoden. Med `moveCamera` metoden kan du ange kartans centrum och zoomnings nivå. `setMapType`Metoden ändrar vilken typ av karta som ska visas.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.moveCamera(CameraUpdateFactory.newLatLngZoom(new LatLng(35.0272, -111.0225), 15));
    mapView.setMapType(GoogleMap.MAP_TYPE_SATELLITE);
}
```

![Google Maps set-vy](media/migrate-google-maps-android-app/google-maps-set-view.png)

> [!NOTE]
> Google Maps använder paneler som är 256 pixlar i dimensioner medan Azure Maps använder en större 512 pixel panel. Detta minskar antalet nätverks begär Anden som krävs av Azure Maps för att läsa in samma mappnings område som Google Maps. För att nå samma visnings område som en karta i Google Maps, måste du ta bort den zoomnings nivå som används i Google Maps med en när du använder Azure Maps.

### <a name="after-azure-maps"></a>Efter: Azure Maps

Som tidigare nämnts, för att nå samma visnings bara yta i Azure Maps subtraherar du den zoomnings nivå som används i Google Maps med ett. I det här fallet använder du en zoomnings nivå på 14.

Den inledande kart visningen kan anges i XML-attribut på kart kontrollen.

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_cameraLat="35.0272"
    app:mapcontrol_cameraLng="-111.0225"
    app:mapcontrol_zoom="14"
    app:mapcontrol_style="satellite"
    />
```

Map-vyn kan programmeras med hjälp av Maps `setCamera` och `setStyle` metoder.

```java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(Point.fromLngLat(-111.0225, 35.0272)), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

![Azure Maps ange vy](media/migrate-google-maps-android-app/azure-maps-set-view.png)

**Ytterligare resurser:**

* [Mappningsformat som stöds](supported-map-styles.md)

## <a name="adding-a-marker"></a>Lägga till en markör

Punkt data återges ofta med hjälp av en bild på kartan. Dessa bilder kallas för markörer, kartnålar, stift eller symboler. I följande exempel återges punkt data som markörer på kartan på latitud: 51,5, longitud:-0,2.

### <a name="before-google-maps"></a>Före: Google Maps

Med Google Maps läggs markörerna till med hjälp av Maps- `addMarker` metoden.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33)));
}
```

![Google Maps-markör](media/migrate-google-maps-android-app/google-maps-marker.png)

### <a name="after-azure-maps"></a>Efter: Azure Maps

I Azure Maps återger du punkt data på kartan genom att först lägga till data i en data källa. Sedan ansluter den data källan till ett symbol lager. Data källan optimerar hanteringen av spatialdata i kart kontrollen. Symbol lagret anger hur punkt data ska återges med hjälp av som en bild eller text.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create a point feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Create a symbol layer and add it to the map.
    map.layers.add(new SymbolLayer(dataSource));
});
```

![Azure Maps markör](media/migrate-google-maps-android-app/azure-maps-marker.png)

## <a name="adding-a-custom-marker"></a>Lägga till en anpassad markör

Anpassade bilder kan användas för att representera punkter på en karta. Kartan i exemplen nedan använder en anpassad bild för att visa en punkt på kartan. Punkten är på latitud: 51,5 och longitud:-0,2. Ankar punkten förskjutnings positionen för markören så att ikonen för kartnålen justeras med rätt placering på kartan.

![gul kartnåls bild](media/migrate-google-maps-web-app/yellow-pushpin.png)<br/>
yellow-pushpin.png

I båda exemplen läggs bilden ovan till i den skrivbara mappen i Apps-resurserna.

### <a name="before-google-maps"></a>Före: Google Maps

Med Google Maps kan anpassade avbildningar användas för markörer. Läs in anpassade avbildningar med markerings `icon` alternativet. Använd alternativet för att justera bildens punkt till koordinaten `anchor` . Fäst punkten är i förhållande till bildens dimensioner. I det här fallet är fäst punkten 0,2 enheter bred och 1 enhet hög.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33))
    .icon(BitmapDescriptorFactory.fromResource(R.drawable.yellow-pushpin))
    .anchor(0.2f, 1f));
}
```

![Google Maps-anpassad markör](media/migrate-google-maps-android-app/google-maps-custom-marker.png)

### <a name="after-azure-maps"></a>Efter: Azure Maps

Symbol lager i Azure Maps stöder anpassade bilder, men först måste bilden läsas in på kart resurserna och tilldelas ett unikt ID. Sedan måste symbol skiktet referera till detta ID. Förskjuter symbolen så att den överensstämmer med rätt punkt på bilden med `iconOffset` alternativet. Ikonens förskjutning är i bild punkter. Som standard är förskjutningen relativ till längst ned i mitten av bilden, men detta förskjutnings värde kan justeras med `iconAnchor` alternativet. I det här exemplet anges `iconAnchor` alternativet till `"center"` . Den använder en ikon förskjutning för att flytta bilden fem pixlar till höger och 15 bild punkter upp för att passa med punkten på den kartnåla bilden.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create a point feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Load the custom image icon into the map resources.
    map.images.add("my-yellow-pin", R.drawable.yellow_pushpin);

    //Create a symbol that uses the custom image icon and add it to the map.
    map.layers.add(new SymbolLayer(dataSource,
        iconImage("my-yellow-pin"),
        iconAnchor(AnchorType.CENTER
        iconOffset(new Float[]{5f, -15f})));
});
```

![Azure Maps anpassad markör](media/migrate-google-maps-android-app/azure-maps-custom-marker.png)

## <a name="adding-a-polyline"></a>Lägga till en polyline

Polylinjer används för att representera en linje eller bana på kartan. I följande exempel visas hur du skapar en streckad polyline på kartan.

### <a name="before-google-maps"></a>Före: Google Maps

Med Google Maps återger du en polyline med hjälp av `PolylineOptions` klassen. Lägg till polylinje i kartan med hjälp av `addPolyline` metoden. Ställ in linje färgen med `color` alternativet. Ange linje bredden med `width` alternativet. Lägg till en streck mat ris med `pattern` alternativet.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the polyline.
    PolylineOptions lineOptions = new PolylineOptions()
            .add(new LatLng(46, -123))
            .add(new LatLng(49, -122))
            .add(new LatLng(46, -121))
            .color(Color.RED)
            .width(10f)
            .pattern(Arrays.<PatternItem>asList(
                    new Dash(30f), new Gap(30f)));

    //Add the polyline to the map.
    Polyline polyline = mapView.addPolyline(lineOptions);
}
```

![Google Maps-sammansatt linje](media/migrate-google-maps-android-app/google-maps-polyline.png)

### <a name="after-azure-maps"></a>Efter: Azure Maps

I Azure Maps kallas polystreck `LineString` eller `MultiLineString` objekt. Lägg till dessa objekt i en data källa och återge dem med hjälp av ett linje lager. Ange linje bredden med `strokeWidth` alternativet. Lägg till en streck mat ris med `strokeDashArray` alternativet.

Måtten för linje bredd och streck mat ris "pixel" i Azure Maps Web SDK är samma som i Google Maps-tjänsten. Båda accepterar samma värden för att producera samma resultat.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create an array of points.
    List<Point> points = Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46));

    //Create a LineString feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(LineString.fromLngLats(points)));

    //Create a line layer and add it to the map.
    map.layers.add(new LineLayer(dataSource,
        strokeColor("red"),
        strokeWidth(4f),
        strokeDashArray(new Float[]{3f, 3f})));
});
```

![Azure Maps polylinje](media/migrate-google-maps-android-app/azure-maps-polyline.png)

## <a name="adding-a-polygon"></a>Lägga till en polygon

Polygoner används för att representera ett område på kartan. I nästa exempel visas hur du skapar en polygon. Den här polygonen utgör en triangel som baseras på kartans centrum-koordinat.

### <a name="before-google-maps"></a>Före: Google Maps

Med Google Maps återger du en polygon med hjälp av `PolygonOptions` klassen. Lägg till polygonen till kartan med `addPolygon` metoden. Ange färgerna för fyllning och linje med `fillColor` alternativen respektive `strokeColor` . Ange linje bredden med `strokeWidth` alternativet.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the polygon.
    PolygonOptions polygonOptions = new PolygonOptions()
            .add(new LatLng(46, -123))
            .add(new LatLng(49, -122))
            .add(new LatLng(46, -121))
            .add(new LatLng(46, -123))  //Close the polygon.
            .fillColor(Color.argb(128, 0, 128, 0))
            .strokeColor(Color.RED)
            .strokeWidth(5f);

    //Add the polygon to the map.
    Polygon polygon = mapView.addPolygon(polygonOptions);
}
```

![Google Maps-polygon](media/migrate-google-maps-android-app/google-maps-polygon.png)

### <a name="after-azure-maps"></a>Efter: Azure Maps

I Azure Maps lägger du till `Polygon` och `MultiPolygon` objekt i en data källa och återger dem på kartan med hjälp av lager. Återge ytan i en polygon i ett polygon lager. Återge konturen för en polygon med ett linje lager. Ange linje färg och bredd med `strokeColor` `strokeWidth` alternativen och.

Måtten för linje bredd och streck mat ris "pixel" i Azure Maps Web SDK överensstämmer med respektive enheter i Google Maps. Båda accepterar samma värden och ger samma resultat.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create an array of point arrays to create polygon rings.
    List<List<Point>> rings = Arrays.asList(Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46),
        Point.fromLngLat(-123, 46)));

    //Create a Polygon feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Polygon.fromLngLats(rings)));

    //Add a polygon layer for rendering the polygon area.
    map.layers.add(new PolygonLayer(dataSource,
        fillColor("green"),
        fillOpacity(0.5f)));

    //Add a line layer for rendering the polygon outline.
    map.layers.add(new LineLayer(dataSource,
        strokeColor("red"),
        strokeWidth(2f)));
});
```

![Azure Maps polygon](media/migrate-google-maps-android-app/azure-maps-polygon.png)

## <a name="overlay-a-tile-layer"></a>Täcka över ett panel lager

 Använd panel lager för att täcka över lager bilder som har delats upp i mindre klickbara bilder, som överensstämmer med Kartornas placerings system. Den här metoden är ett vanligt sätt att täcka lager bilder eller stora data mängder. Panel lager kallas bild överlägg i Google Maps.

I följande exempel överlappar ett väder radar panels lager från Iowa Environment-Mesonet för Iowa State University. Panelerna har storleken 256 bild punkter.

### <a name="before-google-maps"></a>Före: Google Maps

Med Google Maps kan ett panel lager överlappa ovanpå kartan. Använd- `TileOverlayOptions` klassen. Lägg till panel skiktet till kartan med `addTileLauer` metoden. För att göra brickorna halv genomskinliga `transparency` är alternativet inställt på 0,2 eller 20% transparent.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the tile layer.
    TileOverlayOptions tileLayer = new TileOverlayOptions()
        .tileProvider(new UrlTileProvider(256, 256) {
            @Override
            public URL getTileUrl(int x, int y, int zoom) {

                try {
                    //Define the URL pattern for the tile images.
                    return new URL(String.format("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/%d/%d/%d.png", zoom, x, y));
                }catch (MalformedURLException e) {
                    throw new AssertionError(e);
                }
            }
        }).transparency(0.2f);

    //Add the tile layer to the map.
    mapView.addTileOverlay(tileLayer);
}
```

![Panel lager för Google Maps](media/migrate-google-maps-android-app/google-maps-tile-layer.png)

### <a name="after-azure-maps"></a>Efter: Azure Maps

Ett panel lager kan läggas till i kartan på samma sätt som andra lager. En formaterad URL med plats hållare för x, y och zoomning; `{x}`, `{y}` `{z}` används för att ange skiktet för att få åtkomst till panelerna. Panel lager i Azure Maps stöd `{quadkey}` , `{bbox-epsg-3857}` och `{subdomain}` plats hållare. Om du vill göra panel lagret halv genomskinligt används värdet 0,8 för opacitet. Ogenomskinlighet och genomskinlighet, men Använd inverterade värden. Om du vill konvertera mellan båda alternativen subtraherar du värdet från siffran ett.

> [!TIP]
> I Azure Maps är det praktiskt att återge lager under andra lager, inklusive bas kart skikt. Dessutom är det ofta önskvärt att återge panel lager under kart etiketterna så att de är lätta att läsa. `map.layers.add`Metoden tar en andra parameter som är ID: t för det lager där det nya lagret ska infogas. Följande kod kan användas för att infoga ett panel lager under kart etiketterna: `map.layers.add(myTileLayer, "labels");`

```java
mapControl.onReady(map -> {
    //Add a tile layer to the map, below the map labels.
    map.layers.add(new TileLayer(
        tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
        opacity(0.8f),
        tileSize(256)
    ), "labels");
});
```

![Azure Maps panel lager](media/migrate-google-maps-android-app/azure-maps-tile-layer.png)

## <a name="show-traffic"></a>Visa trafik

Både Azure Maps och Google Maps har alternativ för att täcka trafik data.

### <a name="before-google-maps"></a>Före: Google Maps

Med Google Maps kan trafik flödes data översättas ovanpå kartan genom att skicka värdet sant till kartans `setTrafficEnabled` metod.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.setTrafficEnabled(true);
}
```

![Google Maps-trafik](media/migrate-google-maps-android-app/google-maps-traffic.png)

### <a name="after-azure-maps"></a>Efter: Azure Maps

Azure Maps tillhandahåller flera olika alternativ för att Visa trafik. Trafik incidenter, till exempel väg stängningar och haverier kan visas som ikoner på kartan. Trafik flödes-och färgkodade vägar kan översättas på kartan. Färgerna kan ändras så att de visas i förhållande till den angivna hastighets gränsen, i förhållande till den normala förväntade fördröjningen eller den absoluta fördröjningen. Incident data i Azure Maps uppdateras varje minut och Flow-data uppdateras varannan minut.

```java
mapControl.onReady(map -> {
    map.setTraffic(
        incidents(true),
        flow(TrafficFlow.RELATIVE));
});
```

![Azure Maps trafik](media/migrate-google-maps-android-app/azure-maps-traffic.png)

## <a name="clean-up-resources"></a>Rensa resurser

Det gick inte att rensa några resurser.

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du migrerar Azure Maps:

> [!div class="nextstepaction"]
> [Migrera en Android-app](migrate-from-google-maps-android-app.md)
