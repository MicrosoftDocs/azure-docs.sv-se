---
title: 'Självstudie: koppla sensor data med väder prognos data med hjälp av Azure Notebooks (python) med Microsoft Azure Maps'
description: Självstudie om hur du kopplar sensor data med väder prognos data från Microsoft Azure mappar väder tjänster med Azure Notebooks (python).
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc, devx-track-python
ms.openlocfilehash: 8ab3458003366416e10588d3f2edb29b51619ecf
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257644"
---
# <a name="tutorial-join-sensor-data-with-weather-forecast-data-by-using-azure-notebooks-python"></a>Självstudie: koppla sensor data med väder prognos data med hjälp av Azure Notebooks (python)

Vind styrkan är en alternativ energi källa för fossila bränslen för att bekämpa klimat förändringar. Eftersom lindningen inte är konsekvent med natur måste du använda en motor för att bygga Machine Learning-modeller (ML) för att förutsäga energi kapaciteten. Denna förutsägelse är nödvändig för att möta El efter frågan och se till att rutnätets stabilitet är stabil. I den här självstudien går vi igenom hur Azure Maps data för väder prognoser kombineras med demonstrations data för väder läsningar. Väder prognos data begärs genom att anropa Azure Maps väder tjänster.

I de här självstudierna får du:

> [!div class="checklist"]
> * Arbeta med datafiler i [Azure Notebooks](https://notebooks.azure.com) i molnet.
> * Läs in demo data från fil.
> * Anropa Azure Maps REST-API: er i python.
> * Återge plats data på kartan.
> * Förbättra demonstrations data med Azure Maps [dagliga data för dagliga prognoser](/rest/api/maps/weather/getdailyforecast) .
> * Rita prognos data i grafer.


## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien måste du först:

1. Skapa en Azure Maps konto prenumeration på S0-pris nivån genom att följa instruktionerna i [skapa ett konto](quick-demo-map-app.md#create-an-azure-maps-account).
2. Hämta den primära prenumerations nyckeln för ditt konto genom att följa anvisningarna i [Hämta primär nyckel](quick-demo-map-app.md#get-the-primary-key-for-your-account).


Mer information om autentisering i Azure Maps finns i [hantera autentisering i Azure Maps](./how-to-manage-authentication.md).

Om du vill bekanta dig med Azures antecknings böcker och vet hur du kommer igång, följer du anvisningarna [skapa en Azure-anteckningsbok](./tutorial-ev-routing.md#create-an-azure-notebooks-project).

> [!Note]
> Jupyter Notebook-filen för det här projektet kan hämtas från [väder kartor Jupyter Notebook-lagringsplatsen](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/tree/master/AzureMapsJupyterSamples/Tutorials/Analyze%20Weather%20Data).

## <a name="load-the-required-modules-and-frameworks"></a>Läs in de nödvändiga modulerna och ramverken

Kör följande skript för att läsa in alla nödvändiga moduler och ramverk:

```python
import pandas as pd
import datetime
from IPython.display import Image, display
!pip install aiohttp
import aiohttp
```

## <a name="import-weather-data"></a>Importera väder data

För den här självstudien använder vi väder data läsningar från sensorer som är installerade på fyra olika lindnings turbiner. Exempel data består av 30 dagars väder läsningar. Dessa avläsningar samlas in från väder Data Center nära varje turbin-plats. Demonstrations data innehåller data avläsningar för temperatur, vridnings hastighet och riktning. Du kan ladda ned demonstrations data [härifrån](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/tree/master/AzureMapsJupyterSamples/Tutorials/Analyze%20Weather%20Data/data). Skriptet nedan importerar demonstrations data till Azure-anteckningsboken.

```python
df = pd.read_csv("./data/weather_dataset_demo.csv")
```

## <a name="request-daily-forecast-data"></a>Begär dagliga prognos data

I vårt scenario skulle vi vilja begära dagliga prognoser för varje sensor plats. Följande skript anropar [API: et för dagliga prognoser](/rest/api/maps/weather/getdailyforecast) för Azure Maps väder tjänster. Detta API returnerar väder prognoser för varje lindnings turbin, under de närmaste 15 dagarna från dagens datum.

```python
subscription_key = "Your Azure Maps key"

# Get a lists of unique station IDs and their coordinates 
station_ids = pd.unique(df[['StationID']].values.ravel())
coords = pd.unique(df[['latitude','longitude']].values.ravel())

years,months,days = [],[],[]
dates_check=set()
wind_speeds, wind_direction = [], []

# Call azure maps Weather services to get daily forecast data for 15 days from current date
session = aiohttp.ClientSession()
j=-1
for i in range(0, len(coords), 2):
    wind_speeds.append([])
    wind_direction.append([])
    
    query = str(coords[i])+', '+str(coords[i+1])
    forecast_response = await(await session.get("https://atlas.microsoft.com/weather/forecast/daily/json?query={}&api-version=1.0&subscription-key={}&duration=15".format(query, subscription_key))).json()
    j+=1
    for day in range(len(forecast_response['forecasts'])):
            date = forecast_response['forecasts'][day]['date'][:10]
            wind_speeds[j].append(forecast_response['forecasts'][day]['day']['wind']['speed']['value'])
            wind_direction[j].append(forecast_response['forecasts'][day]['day']['windGust']['direction']['degrees'])
            
            if date not in dates_check:
                year,month,day= date.split('-')
                years.append(year)
                months.append(month)
                days.append(day)
                dates_check.add(date)
            
await session.close()
```

Skriptet nedan återger turbin-platserna på kartan genom att anropa Azure Maps [Hämta avbildnings tjänsten](/rest/api/maps/render/getmapimage).

```python
# Render the turbine locations on the map by calling the Azure Maps Get Map Image service
session = aiohttp.ClientSession()

pins="default|la-25+60|ls12|lc003C62|co9B2F15||'Location A'{} {}|'Location B'{} {}|'Location C'{} {}|'Location D'{} {}".format(coords[1],coords[0],coords[3],coords[2],coords[5],coords[4], coords[7],coords[6])

image_response = "https://atlas.microsoft.com/map/static/png?subscription-key={}&api-version=1.0&layer=basic&style=main&zoom=6&center={},{}&pins={}".format(subscription_key,coords[7],coords[6],pins)

static_map_response = await session.get(image_response)

poi_range_map = await static_map_response.content.read()

await session.close()

display(Image(poi_range_map))
```

![Turbin-platser](./media/weather-service-tutorial/location-map.png)


Vi ska gruppera prognos data med demonstrations data baserat på Station-ID. Stations-ID: t är för väder data centret. Den här grupperingen förstärker demonstrations data med prognos data.

```python
# Group forecasted data for all locations
df = df.reset_index(drop=True)
forecast_data = pd.DataFrame(columns=['StationID','latitude','longitude','Year','Month','Day','DryBulbCelsius','WetBulbFarenheit','WetBulbCelsius','DewPointFarenheit','DewPointCelsius','RelativeHumidity','WindSpeed','WindDirection'])

for i in range(len(station_ids)):
    loc_forecast = pd.DataFrame({'StationID':station_ids[i], 'latitude':coords[0], 'longitude':coords[1], 'Year':years, 'Month':months, 'Day':days, 'WindSpeed':wind_speeds[i], 'WindDirection':wind_direction[i]})
    forecast_data = pd.concat([forecast_data,loc_forecast], axis=0, sort=False)
    
combined_weather_data = pd.concat([df,forecast_data])
grouped_weather_data = combined_weather_data.groupby(['StationID'])
```

I följande tabell visas de kombinerade historiska och prognos data för en av turbin-platserna.

```python
# Display data for first location
grouped_weather_data.get_group(station_ids[0]).reset_index()
```

<center>

![Grupperade data](./media/weather-service-tutorial/grouped-data.png)</center>

## <a name="plot-forecast-data"></a>Rita prognos data

Vi ska rita upp de prognostiserade värdena mot de dagar som de är prognostiserade för. I det här området kan vi se hastigheten och riktnings ändringar i vridningen under de närmaste 15 dagarna.

```python
# Plot wind speed
curr_date = datetime.datetime.now().date()
windsPlot_df = pd.DataFrame({ 'Location A': wind_speeds[0], 'Location B': wind_speeds[1], 'Location C': wind_speeds[2], 'Location D': wind_speeds[3]}, index=pd.date_range(curr_date,periods=15))
windsPlot = windsPlot_df.plot.line()
windsPlot.set_xlabel("Date")
windsPlot.set_ylabel("Wind speed")
```

```python
#Plot wind direction 
windsPlot_df = pd.DataFrame({ 'Location A': wind_direction[0], 'Location B': wind_direction[1], 'Location C': wind_direction[2], 'Location D': wind_direction[3]}, index=pd.date_range(curr_date,periods=15))
windsPlot = windsPlot_df.plot.line()
windsPlot.set_xlabel("Date")
windsPlot.set_ylabel("Wind direction")
```

Diagrammen nedan visualiserar prognos data. För att ändra vridnings hastigheten, se det vänstra diagrammet. För ändring i vridnings riktningen, se det högra diagrammet. Dessa data är förutsägelse för nästföljande 15 dagar från den dag då data begärs.

<center>

![Vridnings hastigheten Rita ](./media/weather-service-tutorial/speed-date-plot.png) ![ vridnings riktningen](./media/weather-service-tutorial/direction-date-plot.png)</center>

I den här självstudien får du lära dig att anropa Azure Maps REST-API: er för att hämta väder prognos data. Du har också lärt dig hur du visualiserar data i grafer.

Om du vill veta mer om hur du anropar Azure Maps REST-API: er i Azure Notebooks, se [EV-routning med Azure Notebooks](./tutorial-ev-routing.md).

Om du vill utforska de Azure Maps-API: er som används i den här självstudien, se:

* [Daglig prognos](/rest/api/maps/weather/getdailyforecast)
* [Rendera-Hämta kart bild](/rest/api/maps/render/getmapimage)

En fullständig lista över Azure Maps REST API: er finns i [Azure Maps REST API: er](./consumption-model.md).

## <a name="clean-up-resources"></a>Rensa resurser

Det finns inga resurser som kräver rensning.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Notebooks finns i

> [!div class="nextstepaction"]
> [Azure Notebooks](https://notebooks.azure.com)