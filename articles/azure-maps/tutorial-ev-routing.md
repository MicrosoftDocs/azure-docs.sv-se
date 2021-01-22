---
title: 'Självstudie: dirigera elektriska fordon med hjälp av Azure Notebooks (python) med Microsoft Azure Maps'
description: 'Självstudie om hur du dirigerar elektriska fordon med Microsoft Azure mappar API: er för Routning och Azure Notebooks'
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc, devx-track-python
ms.openlocfilehash: 7341d1f07e8814edcad7b84f6b3b46c7bece3159
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98680339"
---
# <a name="tutorial-route-electric-vehicles-by-using-azure-notebooks-python"></a>Självstudie: dirigera elektriska bilar med Azure Notebooks (python)

Azure Maps är en portfölj med geospatiala tjänst-API: er som är internt integrerade i Azure. Dessa API: er gör det möjligt för utvecklare, företag och ISV att utveckla plats medveten appar, IoT, mobilitet, logistik och till gångs spårnings lösningar. 

Azure Maps REST-API: er kan anropas från språk som python och R för att aktivera geospatiala data analyser och maskin inlärnings scenarier. Azure Maps erbjuder en robust uppsättning [routnings-API: er](/rest/api/maps/route) som gör att användarna kan beräkna vägar mellan flera data punkter. Beräkningarna baseras på olika villkor, till exempel fordons typ eller nåbart utrymme. 

I den här självstudien får du hjälp med att gå igenom en driv rutin vars batteri batteri är låg. Driv rutinen måste hitta den närmaste möjliga laddnings stationen från fordonets plats.

I de här självstudierna får du:

> [!div class="checklist"]
> * Skapa och kör en Jupyter Notebook-fil på [Azure Notebooks](https://notebooks.azure.com) i molnet.
> * Anropa Azure Maps REST-API: er i python.
> * Sök efter ett nåbart intervall baserat på den elektriska fordons förbruknings modellen.
> * Sök efter elektriska fordons uttags stationer inom det nåbara intervallet eller isochrone.
> * Återge gränsen för det nåbara intervallet och debitera stationerna på en karta.
> * Hitta och visualisera en väg till närmaste elektriska fordons laddnings Station baserat på enhets tid.


## <a name="prerequisites"></a>Förutsättningar 

För att slutföra den här självstudien måste du först skapa ett Azure Maps konto och hämta din primär nyckel (prenumerations nyckel). 

Om du vill skapa en Azure Maps konto prenumeration följer du instruktionerna i [skapa ett konto](quick-demo-map-app.md#create-an-azure-maps-account). Du behöver en Azure Maps konto prenumeration med pris nivån S1. 

Följ anvisningarna i [Hämta primär nyckel](quick-demo-map-app.md#get-the-primary-key-for-your-account)för att hämta den primära prenumerations nyckeln för ditt konto.

Mer information om autentisering i Azure Maps finns i [hantera autentisering i Azure Maps](./how-to-manage-authentication.md).

## <a name="create-an-azure-notebooks-project"></a>Skapa ett Azure Notebooks-projekt

Om du vill följa med i den här själv studie kursen måste du skapa ett Azure Notebooks-projekt och hämta och köra Jupyter Notebook-filen. Jupyter Notebook-filen innehåller python-kod som implementerar scenariot i den här självstudien. Gör så här om du vill skapa ett Azure Notebooks-projekt och överföra Jupyter Notebook dokumentet till det:

1. Gå till [Azure Notebooks](https://notebooks.azure.com) och logga in. Mer information finns i [snabb start: Logga in och ange ett användar-ID](https://notebooks.azure.com).
1. Välj **Mina projekt** högst upp på den offentliga profil sidan.

    ![Knappen mina projekt](./media/tutorial-ev-routing/myproject.png)

1. På sidan **Mina projekt** väljer du **nytt projekt**.
 
   ![Knappen nytt projekt](./media/tutorial-ev-routing/create-project.png)

1. I fönstret **Skapa nytt projekt** anger du ett projekt namn och projekt-ID.
 
    ![Fönstret Skapa nytt projekt](./media/tutorial-ev-routing/create-project-window.png)

1. Välj **Skapa**.

1. När projektet har skapats kan du ladda ned den här [Jupyter Notebook dokument filen](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/EVrouting.ipynb) från [Azure Maps Jupyter Notebook-lagringsplatsen](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook).

1. I listan projekt på sidan **Mina projekt** väljer du ditt projekt och väljer sedan **Ladda upp** för att ladda upp Jupyter Notebook dokument filen. 

    ![Ladda upp Jupyter Notebook](./media/tutorial-ev-routing/upload-notebook.png)

1. Ladda upp filen från datorn och välj sedan **slutförd**.

1. När överföringen har slutförts visas filen på projekt sidan. Dubbelklicka på filen för att öppna den som en Jupyter Notebook.

Försök att förstå de funktioner som implementeras i Jupyter Notebook-filen. Kör koden i Jupyter Notebook-filen, en cell i taget. Du kan köra koden i varje cell genom att klicka på knappen **Kör** överst i Jupyter Notebook-appen.

  ![Knappen Kör](./media/tutorial-ev-routing/run.png)

## <a name="install-project-level-packages"></a>Installera paket för projekt nivå

Om du vill köra koden i Jupyter Notebook installerar du paket på projekt nivå genom att utföra följande steg:

1. Ladda ned [*requirements.txt*](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/requirements.txt) -filen från [Azure Maps Jupyter Notebook-lagringsplatsen](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook)och ladda sedan upp den till projektet.
1. På instrument panelen för projektet väljer du **projekt inställningar**. 
1. I fönstret **projekt inställningar** väljer du fliken **miljö** och väljer sedan **Lägg till**.
1. Under **miljö konfigurations steg** gör du följande:   
    a. I den första List rutan väljer du **Requirements.txt**.  
    b. Välj din *requirements.txt* -fil i den andra List rutan.  
    c. I den tredje List rutan väljer du **python Version 3,6** som version.
1. Välj **Spara**.

    ![Installera paket](./media/tutorial-ev-routing/install-packages.png)

## <a name="load-the-required-modules-and-frameworks"></a>Läs in de nödvändiga modulerna och ramverken

Om du vill läsa in alla nödvändiga moduler och ramverk kör du följande skript.

```Python
import time
import aiohttp
import urllib.parse
from IPython.display import Image, display
```

## <a name="request-the-reachable-range-boundary"></a>Begär det nåbara Intervallets gränser

Ett paket leverans företag har vissa elektriska fordon i sin flotta. Under dagen måste elektriska fordon debiteras igen utan att behöva gå tillbaka till lagret. Varje gång den återstående avgiften sjunker till mindre än en timme söker du efter en uppsättning med debiterings stationer som ligger inom ett nåbart intervall. I princip kan du söka efter en laddnings Station när batteriet har låg belastning. Och du får information om gränserna för det intervallet av debiterings stationer. 

Eftersom företaget föredrar att använda vägar som kräver en balans mellan ekonomi och hastighet, är det begärda routeType ett *eko* kort. Följande skript anropar [API: t för att hämta väg intervall](/rest/api/maps/route/getrouterange) för Azure Maps-routningstjänsten. Den använder parametrar för fordonets förbruknings modell. Skriptet tolkar sedan svaret för att skapa ett polygon-objekt i det interjson-format som representerar Car: s maximala räckvidd.

Du kan fastställa gränserna för det tillgängliga intervallet för det elektriska fordonet genom att köra skriptet i följande cell:

```python
subscriptionKey = "Your Azure Maps key"
currentLocation = [34.028115,-118.5184279]
session = aiohttp.ClientSession()

# Parameters for the vehicle consumption model 
travelMode = "car"
vehicleEngineType = "electric"
currentChargeInkWh=45
maxChargeInkWh=80
timeBudgetInSec=550
routeType="eco"
constantSpeedConsumptionInkWhPerHundredkm="50,8.2:130,21.3"


# Get boundaries for the electric vehicle's reachable range.
routeRangeResponse = await (await session.get("https://atlas.microsoft.com/route/range/json?subscription-key={}&api-version=1.0&query={}&travelMode={}&vehicleEngineType={}&currentChargeInkWh={}&maxChargeInkWh={}&timeBudgetInSec={}&routeType={}&constantSpeedConsumptionInkWhPerHundredkm={}"
                                              .format(subscriptionKey,str(currentLocation[0])+","+str(currentLocation[1]),travelMode, vehicleEngineType, currentChargeInkWh, maxChargeInkWh, timeBudgetInSec, routeType, constantSpeedConsumptionInkWhPerHundredkm))).json()

polyBounds = routeRangeResponse["reachableRange"]["boundary"]

for i in range(len(polyBounds)):
    coordList = list(polyBounds[i].values())
    coordList[0], coordList[1] = coordList[1], coordList[0]
    polyBounds[i] = coordList

polyBounds.pop()
polyBounds.append(polyBounds[0])

boundsData = {
               "geometry": {
                 "type": "Polygon",
                 "coordinates": 
                   [
                      polyBounds
                   ]
                }
             }
```

## <a name="search-for-electric-vehicle-charging-stations-within-the-reachable-range"></a>Sök efter elektriska fordons uttags stationer inom det nåbara intervallet

När du har bestämt räckvidden för nåbarhet (isochrone) för det elektriska fordonet kan du söka efter debitering av stationer inom intervallet. 

Följande skript anropar Azure Maps [efter sökning i GEOMETRY API](/rest/api/maps/search/postsearchinsidegeometry). Den söker efter avgifts stationer för elektriska fordon, inom gränserna för den maximalt tillgängliga räckvidden för bilen. Sedan parsar skriptet svaret till en matris med platser som kan kommas åt.

Om du vill söka efter elektriska fordons uttags stationer inom det nåbara intervallet kör du följande skript:

```python
# Search for electric vehicle stations within reachable range.
searchPolyResponse = await (await session.post(url = "https://atlas.microsoft.com/search/geometry/json?subscription-key={}&api-version=1.0&query=electric vehicle station&idxSet=POI&limit=50".format(subscriptionKey), json = boundsData)).json() 

reachableLocations = []
for loc in range(len(searchPolyResponse["results"])):
                location = list(searchPolyResponse["results"][loc]["position"].values())
                location[0], location[1] = location[1], location[0]
                reachableLocations.append(location)
```

## <a name="upload-the-reachable-range-and-charging-points-to-azure-maps-data-service-preview"></a>Ladda upp det nåbara intervallet och debitera Points för Azure Maps data service (för hands version)

På en karta vill du visualisera debiterings stationerna och gränsen för det elektriska fordonets maximala räckvidd. Det gör du genom att ladda upp data om gränser och debitera Stations data som JSON-objekt för att Azure Maps data tjänst (för hands version). Använd [API för data uppladdning](/rest/api/maps/data/uploadpreview). 

Om du vill överföra data för gränser och debiterings plats till Azure Maps data tjänst kör du följande två celler:

```python
rangeData = {
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "properties": {},
      "geometry": {
        "type": "Polygon",
        "coordinates": [
          polyBounds
        ]
      }
    }
  ]
}

# Upload the range data to Azure Maps Data service (Preview).
uploadRangeResponse = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = rangeData)

rangeUdidRequest = uploadRangeResponse.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    getRangeUdid = await (await session.get(rangeUdidRequest)).json()
    if 'udid' in getRangeUdid:
        break
    else:
        time.sleep(0.2)
rangeUdid = getRangeUdid["udid"]
```

```python
poiData = {
    "type": "FeatureCollection",
    "features": [
      {
        "type": "Feature",
        "properties": {},
        "geometry": {
            "type": "MultiPoint",
            "coordinates": reachableLocations
        }
    }
  ]
}

# Upload the electric vehicle charging station data to Azure Maps Data service (Preview).
uploadPOIsResponse = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = poiData)

poiUdidRequest = uploadPOIsResponse.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    getPoiUdid = await (await session.get(poiUdidRequest)).json()
    if 'udid' in getPoiUdid:
        break
    else:
        time.sleep(0.2)
poiUdid = getPoiUdid["udid"]
```

## <a name="render-the-charging-stations-and-reachable-range-on-a-map"></a>Återge debiterings stationer och nåbart intervall på en karta

När du har överfört data till data tjänsten anropar du Azure Maps [Hämta avbildnings tjänst](/rest/api/maps/render/getmapimage). Den här tjänsten används för att återge debiterings punkterna och den maximalt tillgängliga gränsen på den statiska kart bilden genom att köra följande skript:

```python
# Get boundaries for the bounding box.
def getBounds(polyBounds):
    maxLon = max(map(lambda x: x[0], polyBounds))
    minLon = min(map(lambda x: x[0], polyBounds))

    maxLat = max(map(lambda x: x[1], polyBounds))
    minLat = min(map(lambda x: x[1], polyBounds))
    
    # Buffer the bounding box by 10 percent to account for the pixel size of pins at the ends of the route.
    lonBuffer = (maxLon-minLon)*0.1
    minLon -= lonBuffer
    maxLon += lonBuffer

    latBuffer = (maxLat-minLat)*0.1
    minLat -= latBuffer
    maxLat += latBuffer
    
    return [minLon, maxLon, minLat, maxLat]

minLon, maxLon, minLat, maxLat = getBounds(polyBounds)

path = "lcff3333|lw3|la0.80|fa0.35||udid-{}".format(rangeUdid)
pins = "custom|an15 53||udid-{}||https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/images/icons/ev_pin.png".format(poiUdid)

encodedPins = urllib.parse.quote(pins, safe='')

# Render the range and electric vehicle charging points on the map.
staticMapResponse =  await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&pins={}&path={}&bbox={}&zoom=12".format(subscriptionKey,encodedPins,path,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

poiRangeMap = await staticMapResponse.content.read()

display(Image(poiRangeMap))
```

![En karta som visar plats intervallet](./media/tutorial-ev-routing/location-range.png)


## <a name="find-the-optimal-charging-station"></a>Hitta den optimala laddnings stationen

Först vill du fastställa alla potentiella avgifts stationer inom det nåbara intervallet. Sedan vill du veta vilken av dem som kan nås inom kort tid. 

Följande skript anropar API: [et för Azure Maps mat ris routning](/rest/api/maps/route/postroutematrix). Den returnerar den angivna fordons platsen, res tiden och avståndet till varje laddnings Station. Skriptet i nästa cell tolkar svaret för att hitta den närmast tillgängliga avgifts stationen med avseende på tid.

Kör skriptet i följande cell för att hitta den närmaste tillgängliga avgifts stationen som kan nås i minst tids period:

```python
locationData = {
            "origins": {
              "type": "MultiPoint",
              "coordinates": [[currentLocation[1],currentLocation[0]]]
            },
            "destinations": {
              "type": "MultiPoint",
              "coordinates": reachableLocations
            }
         }

# Get the travel time and distance to each specified charging station.
searchPolyRes = await (await session.post(url = "https://atlas.microsoft.com/route/matrix/json?subscription-key={}&api-version=1.0&routeType=shortest&waitForResults=true".format(subscriptionKey), json = locationData)).json()

distances = []
for dist in range(len(reachableLocations)):
    distances.append(searchPolyRes["matrix"][0][dist]["response"]["routeSummary"]["travelTimeInSeconds"])

minDistLoc = []
minDistIndex = distances.index(min(distances))
minDistLoc.extend([reachableLocations[minDistIndex][1], reachableLocations[minDistIndex][0]])
closestChargeLoc = ",".join(str(i) for i in minDistLoc)
```

## <a name="calculate-the-route-to-the-closest-charging-station"></a>Beräkna vägen till närmaste avgifts Station

Nu när du har hittat den närmaste laddnings stationen kan du anropa [API: et för att hämta väg riktningar](/rest/api/maps/route/getroutedirections) för att begära den detaljerade vägen från det elektriska fordonets aktuella plats till laddnings stationen.

Kör skriptet i följande cell för att få vägen till laddnings stationen och analysera svaret för att skapa ett interjson-objekt som representerar vägen.

```python
# Get the route from the electric vehicle's current location to the closest charging station. 
routeResponse = await (await session.get("https://atlas.microsoft.com/route/directions/json?subscription-key={}&api-version=1.0&query={}:{}".format(subscriptionKey, str(currentLocation[0])+","+str(currentLocation[1]), closestChargeLoc))).json()

route = []
for loc in range(len(routeResponse["routes"][0]["legs"][0]["points"])):
                location = list(routeResponse["routes"][0]["legs"][0]["points"][loc].values())
                location[0], location[1] = location[1], location[0]
                route.append(location)

routeData = {
         "type": "LineString",
         "coordinates": route
     }
```

## <a name="visualize-the-route"></a>Visualisera vägen

För att visualisera vägen laddar du först upp flödes data som ett interjson-objekt för att Azure Maps data tjänst (för hands version). Det gör du med hjälp av API: et för Azure Maps [data överföring](/rest/api/maps/data/uploadpreview). Anropa sedan åter givnings tjänsten, [Hämta avbildnings-API](/rest/api/maps/render/getmapimage)för att rendera vägen på kartan och visualisera den.

Kör följande skript för att hämta en avbildning för den återgivna vägen på kartan:

```python
# Upload the route data to Azure Maps Data service (Preview).
routeUploadRequest = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = routeData)

udidRequestURI = routeUploadRequest.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    udidRequest = await (await session.get(udidRequestURI)).json()
    if 'udid' in udidRequest:
        break
    else:
        time.sleep(0.2)

udid = udidRequest["udid"]

destination = route[-1]

destination[1], destination[0] = destination[0], destination[1]

path = "lc0f6dd9|lw6||udid-{}".format(udid)
pins = "default|codb1818||{} {}|{} {}".format(str(currentLocation[1]),str(currentLocation[0]),destination[1],destination[0])


# Get boundaries for the bounding box.
minLat, maxLat = (float(destination[0]),currentLocation[0]) if float(destination[0])<currentLocation[0] else (currentLocation[0], float(destination[0]))
minLon, maxLon = (float(destination[1]),currentLocation[1]) if float(destination[1])<currentLocation[1] else (currentLocation[1], float(destination[1]))

# Buffer the bounding box by 10 percent to account for the pixel size of pins at the ends of the route.
lonBuffer = (maxLon-minLon)*0.1
minLon -= lonBuffer
maxLon += lonBuffer

latBuffer = (maxLat-minLat)*0.1
minLat -= latBuffer
maxLat += latBuffer

# Render the route on the map.
staticMapResponse = await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&&path={}&pins={}&bbox={}&zoom=16".format(subscriptionKey,path,pins,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

staticMapImage = await staticMapResponse.content.read()

await session.close()
display(Image(staticMapImage))
```

![En karta som visar vägen](./media/tutorial-ev-routing/route.png)

I den här självstudien lärde du dig att anropa Azure Maps REST-API: er direkt och visualisera Azure Maps data med hjälp av python.

Om du vill utforska de Azure Maps-API: er som används i den här självstudien, se:

* [Hämta flödes intervall](/rest/api/maps/route/getrouterange)
* [Publicera sökning i geometri](/rest/api/maps/search/postsearchinsidegeometry)
* [Data uppladdning](/rest/api/maps/data/uploadpreview)
* [Rendera-Hämta kart bild](/rest/api/maps/render/getmapimage)
* [Publicera väg mat ris](/rest/api/maps/route/postroutematrix)
* [Hämta väg riktningar](/rest/api/maps/route/getroutedirections)
* [Azure Maps REST-API: er](./consumption-model.md)

## <a name="clean-up-resources"></a>Rensa resurser

Det finns inga resurser som kräver rensning.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Notebooks finns i

> [!div class="nextstepaction"]
> [Azure Notebooks](https://notebooks.azure.com)