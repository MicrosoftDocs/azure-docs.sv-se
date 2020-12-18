---
title: 'Självstudie: läsa in multijson-data i Azure Maps Android SDK | Microsoft Azure Maps'
description: Själv studie kurs om hur du läser in data filen för en lokal JSON-fil i Azure Maps Android Map SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 12/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: b527cd7b3f841b6cb3dcf2dce6930f3bd9bcc184
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2020
ms.locfileid: "97681978"
---
# <a name="tutorial-load-geojson-data-into-azure-maps-android-sdk"></a>Självstudie: läsa in data i interjson till Azure Maps Android SDK

I den här självstudien får du stegvisa instruktioner för hur du importerar en multijson-fil med plats data till Azure Maps Android SDK. I de här självstudierna får du lära dig att

> [!div class="checklist"]
> * Lägg till Azure Maps i ett Android-program.
> * Skapa en data källa och läsa in en multijson-fil från en lokal fil eller webben.
> * Visa data på kartan.

## <a name="prerequisites"></a>Förutsättningar

1. Slutför [snabb starten: skapa en Android-app](quick-android-map.md). Den här självstudien kommer att utöka den kod som används i den snabb starten.
2. Ladda ned [exempel punkterna i Interest](https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/data/geojson/SamplePoiDataSet.json) -språkjson-filen.

### <a name="import-geojson-data-from-web-or-assets-folder"></a>Importera data för en webbdel från en webbplats eller till gångar

De flesta inkapslade JSON-filer omsluter alla data i en `FeatureCollection` . Om t. ex. de interjson-filerna läses in i programmet som en sträng, kan de skickas till funktions samlingens statiska `fromJson` metod som deserialiserar strängen till ett INTERjson- `FeatureCollection` objekt som kan läggas till i mappningen.

Följande steg visar hur du importerar en multijson-fil till programmet och deserialiserar den som ett interjson- `FeatureCollection` objekt.

1. Slutför [snabb starten: skapa en Android-app](quick-android-map.md) som följande steg bygger på det här programmet.
2. I panelen projekt i Android Studio högerklickar du på **app** -mappen och går till `New > Folder > Assets Folder` .
3. Dra och släpp [exempel punkterna i Interest](https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/data/geojson/SamplePoiDataSet.json) -språkjson-filen i mappen till gångar.
4. Skapa en ny fil med namnet **utils. java** och Lägg till följande kod i filen. Den här koden innehåller en statisk metod `importData` som kallas att asynkront importerar en fil från `assets` mappen i programmet eller från webben med en URL som en sträng och tillbaka tillbaka till UI-tråden med en enkel callback-metod.

    ```java
    //Modify the package name as needed to align with your application.
    package com.example.myapplication;
    
    import android.content.Context;
    import android.os.Handler;
    import android.os.Looper;
    import android.webkit.URLUtil;
    
    import java.io.BufferedReader;
    import java.io.IOException;
    import java.io.InputStream;
    import java.io.InputStreamReader;
    import java.net.URL;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;
    
    import javax.net.ssl.HttpsURLConnection;
    
    public class Utils {
    
        interface SimpleCallback {
            void notify(String result);
        }
    
        /**
         * Imports data from a web url or asset file name and returns it to a callback.
         * @param urlOrFileName A web url or asset file name that points to data to load.
         * @param context The context of the app.
         * @param callback The callback function to return the data to.
         */
        public static void importData(String urlOrFileName, Context context, SimpleCallback callback){
            importData(urlOrFileName, context, callback, null);
        }
        
        /**
         * Imports data from a web url or asset file name and returns it to a callback.
         * @param urlOrFileName A web url or asset file name that points to data to load.
         * @param context The context of the app.
         * @param callback The callback function to return the data to.
         * @param error A callback function to return errors to.
         */
        public static void importData(String urlOrFileName, Context context, SimpleCallback callback, SimpleCallback error){
            if(urlOrFileName != null && callback != null) {
                ExecutorService executor = Executors.newSingleThreadExecutor();
                Handler handler = new Handler(Looper.getMainLooper());
    
                executor.execute(() -> {
                    String data = null;
    
                    try {
    
                        if(URLUtil.isNetworkUrl(urlOrFileName)){
                            data = importFromWeb(urlOrFileName);
                        } else {
                            //Assume file is in assets folder.
                            data = importFromAssets(context, urlOrFileName);
                        }
    
                        final String result = data;
    
                        handler.post(() -> {
                            //Ensure the resulting data string is not null or empty.
                            if (result != null && !result.isEmpty()) {
                                callback.notify(result);
                            } else {
                                error.notify("No data imported.");
                            }
                        });
                    } catch(Exception e) {
                        if(error != null){
                            error.notify(e.getMessage());
                        }
                    }
                });
            }
        }
    
        /**
         * Imports data from an assets file as a string.
         * @param context The context of the app.
         * @param fileName The asset file name.
         * @return
         * @throws IOException
         */
        private static String importFromAssets(Context context, String fileName) throws IOException {
            InputStream stream = null;
    
            try {
                stream = context.getAssets().open(fileName);
    
                if(stream != null) {
                    return readStreamAsString(stream);
                }
            } catch (Exception e) {
                e.printStackTrace();
            } finally {
                // Close Stream and disconnect HTTPS connection.
                if (stream != null) {
                    stream.close();
                }
            }
    
            return null;
        }
    
        /**
         * Imports data from the web as a string.
         * @param url URL to the data.
         * @return
         * @throws IOException
         */
        private static String importFromWeb(String url) throws IOException {
            InputStream stream = null;
            HttpsURLConnection connection = null;
            String result = null;
    
            try {
                connection = (HttpsURLConnection) new URL(url).openConnection();
    
                //For this use case, set HTTP method to GET.
                connection.setRequestMethod("GET");
    
                //Open communications link (network traffic occurs here).
                connection.connect();
    
                int responseCode = connection.getResponseCode();
                if (responseCode != HttpsURLConnection.HTTP_OK) {
                    throw new IOException("HTTP error code: " + responseCode);
                }
    
                //Retrieve the response body as an InputStream.
                stream = connection.getInputStream();
    
                if (stream != null) {
                    return readStreamAsString(stream);
                }
            } catch (Exception e) {
                e.printStackTrace();
            } finally {
                // Close Stream and disconnect HTTPS connection.
                if (stream != null) {
                    stream.close();
                }
                if (connection != null) {
                    connection.disconnect();
                }
            }
    
            return result;
        }
    
        /**
         * Reads an input stream as a string.
         * @param stream Stream to convert.
         * @return
         * @throws IOException
         */
        private static String readStreamAsString(InputStream stream) throws IOException {
            //Convert the contents of an InputStream to a String.
            BufferedReader in = new BufferedReader(new InputStreamReader(stream, "UTF-8"));
    
            String inputLine;
            StringBuffer response = new StringBuffer();
    
            while ((inputLine = in.readLine()) != null) {
                response.append(inputLine);
            }
    
            in.close();
    
            return response.toString();
        }
    }
    ```

5. Gå till filen **MainActivity. java** och Lägg till följande kod i återanropet för `mapControl.onReady` händelsen, så finns den i- `onCreate` metoden. I den här koden används import verktyget för att läsa **SamplePoiDataSet.jspå** filen som en sträng, och sedan deserialiseras den som en funktions samling med hjälp av `fromJson` `FeatureCollection` klassens statiska metod. Den här koden beräknar också avgränsnings rutans område för alla data i funktions samlingen och använder detta för att ställa in kartans kamera så att den fokuserar på data.

    ```java
    //Create a data source and add it to the map.
    DataSource source = new DataSource();
    map.sources.add(source);
    
    //Import the GeoJSON data and add it to the data source.
    Utils.importData("SamplePoiDataSet.json",
        this,
        (String result) -> {
            //Parse the data as a GeoJSON Feature Collection.
            FeatureCollection fc = FeatureCollection.fromJson(result);
    
            //Add the feature collection to the data source.
            source.add(fc);
    
            //Optionally, update the maps camera to focus in on the data.
    
            //Calculate the bounding box of all the data in the Feature Collection.
            BoundingBox bbox = MapMath.fromData(fc);
    
            //Update the maps camera so it is focused on the data.
            map.setCamera(
                bounds(bbox),

                //Padding added to account for pixel size of rendered points.
                padding(20)
            );
        });
    ```

6. Nu när det finns kod för att läsa in de lokala JSON-data till kartan med en data källa måste vi ange hur data ska visas på kartan. Det finns flera olika åter givnings skikt för punkt data. [Bubbeldiagram](map-add-bubble-layer-android.md), [symbol lagret](how-to-add-symbol-to-android-map.md)och [värme kart skiktet](map-add-heat-map-layer-android.md) är de vanligaste lagren. Lägg till följande kod för att rendera data i ett Bubble-lager i motringningen för `mapControl.onReady` händelsen efter koden för att importera data.

    ```java
    //Create a layer and add it to the map.
    BubbleLayer layer = new BubbleLayer(source);
    map.layers.add(layer);
    ```

7. Kör appen. En karta kommer att visas fokuserat över USA, med cirklar som är placerade för varje plats i den riktade JSON-filen.

    ![Karta över USA med data från en injson-fil som visas](media/tutorial-load-geojson-file-android/android-import-geojson.png)


## <a name="clean-up-resources"></a>Rensa resurser

Vidta följande steg för att rensa resurserna från den här självstudien:

1. Stäng Android Studio och ta bort det program som du har skapat.
2. Om du har testat programmet på en extern enhet avinstallerar du programmet från den enheten.

## <a name="next-steps"></a>Nästa steg

Fler kodexempel och en interaktiv kodupplevelse:

> [!div class="nextstepaction"]
> [Använda datadrivna formatuttryck](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Visa funktionsinformation](display-feature-information-android.md)

> [!div class="nextstepaction"]
> [Lägga till ett symbolskikt](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Lägg till ett linjeskikt](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Lägg till ett polygonskikt](how-to-add-shapes-to-android-map.md)
