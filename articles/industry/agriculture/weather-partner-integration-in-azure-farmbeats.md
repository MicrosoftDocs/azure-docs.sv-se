---
title: Väderpartnerintegration
description: Lär dig mer om hur en väder data leverantör kan integreras med FarmBeats.
author: sunasing
ms.topic: article
ms.date: 07/09/2020
ms.author: sunasing
ms.openlocfilehash: f0fbd93e2a5f4e92089e10e75dc17e304ff80bf6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93147087"
---
# <a name="weather-partner-integration-with-farmbeats"></a>Väder partner integrering med FarmBeats

Den här artikeln innehåller information om Azure FarmBeats Connector Docker-komponenten. Som en väder data leverantör kan du använda anslutnings Docker för att integrera med FarmBeats. Använd dess API: er för att skicka väder data till FarmBeats. I FarmBeats kan data användas för data fusion och för att skapa maskin inlärnings modeller eller artificiella informations modeller.

 > [!NOTE]
 > I den här artikeln använder vi en [referens implementering](https://github.com/azurefarmbeats/noaa_docker) som skapats med hjälp av Azures öppna data uppsättningar och väder data från National ensamt atmosfärisk administration (NOAA). Vi använder även motsvarande [Docker-avbildning](https://hub.docker.com/r/azurefarmbeats/farmbeats-noaa).

Du måste ange en [lämplig Docker-avbildning eller ett program](#docker-specifications) och vara värd för Docker-avbildningen i ett behållar register som kunder kan komma åt. Ange följande information för dina kunder:

- Docker-avbildnings-URL
- Docker-bildtagg
- Nycklar eller autentiseringsuppgifter för att komma åt Docker-avbildningen
- Kundspecifika API-nycklar eller autentiseringsuppgifter för att komma åt data från systemet
- Information om VM-SKU (ange den här informationen om din Docker-avbildning har särskilda krav för virtuella datorer. Annars kan kunderna välja mellan stödda VM SKU: er i Azure.)

Kunder använder den här Docker-informationen för att registrera en väder partner i sin FarmBeats-instans. Mer information om hur kunder kan använda Docker för att mata in väder data i FarmBeats finns i [Hämta data från väder partner](./get-weather-data-from-weather-partner.md).

## <a name="connector-docker-development"></a>Ansluta Docker-utveckling

**REST API-baserad integrering**

FarmBeats-API: erna innehåller teknisk dokumentation för Swagger. Mer information om API: er och deras motsvarande förfrågningar och svar finns i [FarmBeats-Swagger](https://aka.ms/farmbeatsswagger). 

Om du redan har installerat FarmBeats kan du komma åt FarmBeats-Swagger på `https://yourfarmbeatswebsitename-api.azurewebsites.net/swagger`

Observera att *-API: t* läggs till i FarmBeats-webbplatsens namn. API-slutpunkten är `https://yourfarmbeatswebsitename-api.azurewebsites.net`

### <a name="datahub-lib"></a>Datahub-lib

FarmBeats innehåller en lib som du kan använda. Lib är för närvarande tillgänglig som [en del av referens implementeringen](https://github.com/azurefarmbeats/noaa_docker/tree/master/datahub_lib). Senare är den tillgänglig som ett SDK för flera språk.

### <a name="authentication"></a>Autentisering

**Autentisering med FarmBeats-API: er**

FarmBeats använder Bearer-autentisering. Du kan komma åt API: erna genom att tillhandahålla en åtkomsttoken i huvud avsnittet i begäran. Här är ett exempel:

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

Du kan begära åtkomsttoken från en Azure Functions-app som körs på kundens FarmBeats-instans. Azure Functions-URL: en tillhandahålls till Docker-programmet som ett argument. Du kan hämta åtkomsttoken genom att göra en `GET` begäran på URL: en. Svaret från webb adressen innehåller åtkomsttoken. 

Hämta åtkomsttoken med hjälp av funktionerna i Datahub-lib-lib. Mer information finns på [sidan om GitHub för NOAA Docker-avbildningen](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_auth_helper.py).

Åtkomsttoken är bara giltig i några timmar. När den går ut måste du begära det igen.

**Autentisering med API: er på partner sidan**

För att autentisera med API: er på partner sidan medan Docker-jobbet körs måste kunderna ange autentiseringsuppgifterna under partner registreringen. Här är ett exempel:

```json
{
 "partnerCredentials": {
   "key1": "value1",
   "key2": "value2"
   }
}
```
API-tjänsten serialiserar den här dikteringen och lagrar den i ett [nyckel valv](../../key-vault/general/basic-concepts.md).

[Azure Data Factory](../../data-factory/introduction.md) används för att dirigera väder jobb. Det ökar resurser för att köra Docker-koden. Data Factory tillhandahåller också en mekanism för att skicka data säkert till den virtuella dator där Docker-jobbet körs. API-autentiseringsuppgifterna lagras sedan säkert i nyckel valvet. 

Autentiseringsuppgifterna läses som säkra strängar från nyckel valvet. De tillhandahålls som utökade egenskaper i arbets katalogen i Docker-behållaren. Deras fil Sök väg är */mnt/working_dir/activity.jspå*. 

Docker-koden kan läsa autentiseringsuppgifterna från *activity.jsvid* kör tid för att få åtkomst till API: er för partner sidan för kunden. I JSON-filen ser autentiseringsuppgifterna ut som följande kod exempel:

```json
{ 
   "typeProperties" : {
      "extendedProperties" : { 
         "partnerCredentials": "" } 
   } 
}
```
`partnerCredentials`Autentiseringsuppgiften är tillgänglig på det sätt som kunden angav under partner registreringen.

FarmBeats-lib innehåller hjälp funktioner. Använd dessa funktioner för att läsa autentiseringsuppgifterna från aktivitets egenskaperna. Mer information finns på [sidan om GitHub för NOAA Docker-avbildningen](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_adf_helper.py).

Filen används endast när Docker-koden körs. När koden har slutförts tas filen bort.

Mer information om hur Data Factory pipelines och aktiviteter fungerar finns i [schema och data typs mappning](../../data-factory/copy-activity-schema-and-type-mapping.md).

**Rubriker för HTTP-begäran**

I följande tabell visas de vanligaste begärandehuvuden som du måste ange när du gör ett API-anrop till FarmBeats.

Huvud | Beskrivning och exempel
--- | ---
Content-Type | Formatet för begäran. Exempel: `Content-Type: application/<format>` <br/>För FarmBeats Datahub-API: er är formatet JSON. Exempel: ` Content-Type: application/json`
Auktorisering | Den åtkomsttoken som krävs för att göra ett API-anrop. Exempel: `Authorization: Bearer <Access-Token>`
Acceptera | Svars formatet. För FarmBeats Datahub-API: er är formatet JSON. Exempel: `Accept: application/json`

## <a name="data-format"></a>Dataformat

JSON är ett gemensamt språk oberoende data format som ger en enkel text representation av godtyckliga data strukturer. Mer information finns i [JSON.org](https://json.org).

## <a name="docker-specifications"></a>Docker-specifikationer

Docker-programmet behöver två komponenter: Start och jobb. Programmet kan ha mer än ett jobb.

### <a name="bootstrap"></a>Bootstrap

Start komponenten ska köras när kunden startar Docker-registreringen på FarmBeats. Följande argument ( `arg1` och `arg2` ) skickas till programmet:

- **FARMBEATS API-slutpunkt**: FarmBeats API-slutpunkt för API-begäranden. Den här slut punkten gör API-anrop till FarmBeats-distributionen.
- **Azure Functions-URL**: en egen slut punkt. Den här URL: en ger din åtkomsttoken för FarmBeats-API: er. Du kan anropa den `GET` här URL: en för att hämta åtkomsttoken.

Start programmet skapar de metadata som användarna behöver för att köra dina jobb för att få väder data. Mer information finns i [referens implementeringen](https://github.com/azurefarmbeats/noaa_docker). 

Om du anpassar *bootstrap_manifest.jspå* filen skapar referens programmet start program de metadata som krävs åt dig. Bootstrap-programmet skapar följande metadata: 

 > [!NOTE]
 > Om du uppdaterar *bootstrap_manifest.jspå* filen eftersom [referens implementeringen](https://github.com/azurefarmbeats/noaa_docker) beskriver, behöver du inte skapa följande metadata. Start programmet kommer att använda manifest filen för att skapa nödvändiga metadata.

- /**WeatherDataModel**: WeatherDataModel metadata representerar väder data. Det motsvarar data uppsättningar som källan tillhandahåller. Till exempel kan en DailyForecastSimpleModel tillhandahålla genomsnittlig temperatur, fuktighet och utfällnings information en gång om dagen. En DailyForecastAdvancedModel kan däremot ge mycket mer information om varje timme. Du kan skapa valfritt antal data modeller för väder.
- /**JobType**: FarmBeats har ett utöknings Bart jobb hanterings system. Som väder data leverantör har du olika data uppsättningar och API: er (till exempel GetDailyForecasts). Du kan aktivera dessa data uppsättningar och API: er i FarmBeats genom att använda JobType. När en jobbtyp har skapats kan en kund utlösa jobb av den typen för att hämta väder data för deras plats eller deras intresse grupp. Mer information finns i JobType och jobb-API: er i [FarmBeats-Swagger](https://aka.ms/farmbeatsswagger).

### <a name="jobs"></a>Jobb

Jobb komponenten anropas varje gång en FarmBeats-användare kör ett jobb av/JobType som du skapade som en del av start processen. Kommandot Docker kör för jobbet definieras som en del av/JobType som du skapade.

Jobbet hämtar data från källan och skickar dem till FarmBeats. Under start processen bör parametrarna som krävs för att hämta data definieras som en del av/JobType.

Som en del av jobbet måste programmet skapa en/WeatherDataLocation baserat på/WeatherDataModel som skapades under start processen. /WeatherDataLocation motsvarar en plats (latitud-och longitud-koordinater) som användaren angav som en parameter för jobbet.

### <a name="object-details"></a>Objekt information

WeatherDataModel | Beskrivning |
--- | ---
Name  | Namnet på väder data modellen. |
Beskrivning  | En meningsfull beskrivning av modellen. |
Egenskaper  | Ytterligare egenskaper som definieras av data leverantören. |
weatherMeasures > namn  | Namnet på väder måttet. Till exempel humidity_max. |
weatherMeasures > datatype  | Antingen Double eller Enum. Om uppräkningen krävs måste measureEnumDefinition anges. |
weatherMeasures > measureEnumDefinition  | Krävs endast om datatype är Enum. Till exempel `{ "NoRain": 0, "Snow": 1, "Drizzle": 2, "Rain": 3 }` |
weatherMeasures > typ  | Typ av data för väder telemetri. Till exempel RelativeHumidity. Systemdefinierade typer är AmbientTemperature, nounit, CO2, djup, ElectricalConductivity, LeafWetness, length, LiquidLevel, nitrat, O2, PH, fosfat, PointInTime, kalium, press, RainGauge, RelativeHumidity, salinity, SoilMoisture, SoilTemperature, SolarRadiation, State, TimeDuration, UVRadiation, UVIndex, volym, WindDirection, WindRun, WindSpeed, evapotranspiration och parivärde. Om du vill lägga till fler typer går du [till avsnittet Lägg till ExtendedType](#add-extendedtype) i den här artikeln.
weatherMeasures > enhet | Enhet för väder informations data. Systemdefinierade enheter är nounit, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, kvicksilver, PSI, MilliMeter, CentiMeter, meter, tum, fot, mil, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, examen, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, PartsPerMillion, MicroMole, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, liter, VolumetricIonContent, sekunder, MilliLiter, UnixTimestamp och MicroMolePerMeterSquaredPerSecond. Om du vill lägga till fler enheter går du [till avsnittet Lägg till ExtendedType](#add-extendedtype) i den här artikeln.
weatherMeasures > AggregationType  | Typ av agg regering. Möjliga värden är none, Average, maximum, minimum, StandardDeviation, sum och total.
weatherMeasures > djup  | Sensorns djup i centimeter. Till exempel mätningen av fukt 10 cm under marken.
Beskrivning av weatherMeasures->  | En meningsfull beskrivning av måttet. 

JobType | Beskrivning |
--- | ---
Name  | Jobbets namn. Till exempel Get_Daily_Forecast. Kunden kommer att köra jobbet för att få väder data.|
pipelineDetails > parametrar > namn  | Parameterns namn. |
pipelineDetails > parametrar > typ | Parameter typen. Möjliga värden är String, int, Float, bool och array. |
pipelineDetails > parametrar > parametrarna isrequired | Parameterns booleska värde. Värdet är true om parametern är obligatorisk. Annars är värdet FALSKT. Standardvärdet är true. |
pipelineDetails > parametrar > defaultValue | Standardvärdet för parametern. |
pipelineDetails > parametrar > Beskrivning | Beskrivning av parametern. |
Egenskaper  | Ytterligare egenskaper från tillverkaren.
Egenskaper > programRunCommand | Docker Run-kommandot. Det här kommandot körs när kunden kör väder jobbet. |

WeatherDataLocation | Beskrivning |
--- | ---
weatherDataModelId  | ID för motsvarande WeatherDataModel som skapades under start processen.|
location  | Latitud, longitud och höjning. |
Name | Objektets namn. |
Beskrivning | Beskrivning av väder data platsen. |
farmId | Valfritt ID för Server gruppen. Kunden tillhandahåller detta ID som en del av jobb parametern. |
Egenskaper  | Ytterligare egenskaper från tillverkaren.

Mer information om objekten och deras egenskaper finns i [FarmBeats-Swagger](https://aka.ms/FarmBeatsSwagger).

> [!NOTE]
> API: erna returnerar unika ID: n för varje instans som skapas. Translator för enhets hantering och synkronisering av metadata måste behålla detta ID.

**Synkronisering av metadata**

Docker-komponenten för Connector ska kunna skicka uppdateringar för metadata. Den bör till exempel skicka uppdateringar när väder leverantören lägger till nya parametrar till en data uppsättning eller när nya funktioner, till exempel en ny 30-dagars prognos, läggs till.

> [!NOTE]
> Borttagning stöds inte för metadata i väder data modellen.
>
> Om du vill uppdatera metadata måste du anropa `/Get/{ID}` data modellen väder. Uppdatera de ändrade egenskaperna och gör sedan en `/Put/{ID}` för att behålla alla egenskaper som användaren anger.

## <a name="weather-data-telemetry-specifications"></a>Specifikationer för väder data (telemetri)

Väder data mappas till ett kanoniskt meddelande som skickas till en Azure Event Hub för bearbetning. Azure Event Hubs är en tjänst som gör att du kan mata in real tids data (telemetri) från anslutna enheter och program. 

Om du vill skicka väder data till FarmBeats skapar du en klient som skickar meddelanden till en Event Hub i FarmBeats. Mer information finns i [Skicka telemetri till en Event Hub](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md).

Följande exempel på python-kod skickar telemetri som en klient till en angiven Event Hub.

```python
import azure
from azure.eventhub import EventHubClient, Sender, EventData, Receiver, Offset
EVENTHUBCONNECTIONSTRING = "<EventHub connection string provided by customer>"
EVENTHUBNAME = "<EventHub name provided by customer>"

write_client = EventHubClient.from_connection_string(EVENTHUBCONNECTIONSTRING, eventhub=EVENTHUBNAME, debug=False)
sender = write_client.add_sender(partition="0")
write_client.run()
for i in range(5):
    telemetry = "<Canonical telemetry message>"
    print("Sending telemetry: " + telemetry)
    sender.send(EventData(telemetry))
write_client.stop()

```

Här är det kanoniska meddelande formatet:

```json
{
   "weatherstations": [
   {
   "id": "ID of the WeatherDataLocation.",
   "weatherdata": [
   {
     "timestamp": "Timestamp of the data. For historical purposes, this is the time for which the observations are sent. For forecast, this is the time for which data is forecasted. Format is ISO 8601. Default time zone is UTC.",
     "predictiontimestamp": "Timestamp on which the forecast data is predicted. I.e., the time of prediction. Required only for forecast data. Format is ISO 8601. Default time zone is UTC. ",
     "weathermeasurename1": <value>,
     "weathermeasurename2": <value>
     }
     ]
    }
   ]
}
```

Här är ett exempel på ett telemetri-meddelande:

```json
{
   "weatherstations": [
   {
     "id": "5e4b34e7-bf9e-4f39-xxxx-f3c06d0366ea",
     "weatherdata": [
     {
      "timestamp": "2019-07-10T00:00:00Z",
      "predictiontimestamp": "2019-07-05T00:00:00Z",
      "PrecipitationTotalLiquidEquivalent": 0,
      "AvgPressure": 0,
      "AvgRelativeHumidity": 72
     }
    ] 
  }
 ]
}

```

## <a name="troubleshooting-and-error-management"></a>Fel sökning och fel hantering

### <a name="error-logging"></a>Fel loggning

Partner jobbet körs i det befintliga jobb ramverket. Därför loggas felen på samma sätt som fel för andra befintliga FarmBeats-jobb (t. ex. GetFarmData och SensorPlacement). Den Data Factory aktivitet som körs i Data Factory pipeline loggar både `STDERR` och `STDOUT` . Båda filerna är tillgängliga i `datahublogs-xxx` lagrings kontot i resurs gruppen FarmBeats.

Datahub-lib tillhandahåller hjälp funktioner för att aktivera loggning som en del av de övergripande Datahub-loggarna. Mer information finns på [sidan om GitHub för NOAA Docker-avbildningen](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/framework/logger.py).

### <a name="troubleshooting-and-support"></a>Fel sökning och support

Om kunden inte kan ta emot väder data i FarmBeats-instansen kan du ge support och en mekanism för att felsöka problemet.

## <a name="add-extendedtype"></a>Lägg till ExtendedType

FarmBeats har stöd för att lägga till nya typer av sensor mått och enheter. Du kan lägga till nya enheter eller typer genom att uppdatera *bootstrap_manifest.jspå* filen i [referens implementeringen](https://github.com/azurefarmbeats/noaa_docker).

Följ dessa steg om du vill lägga till en ny WeatherMeasure-typ, till exempel PrecipitationDepth.

1. Gör en `GET` begäran på/ExtendedType med hjälp av frågan `filter - key = WeatherMeasureType` .
2. Notera ID: t för det returnerade objektet.
3. Lägg till den nya typen i listan i det returnerade objektet. Gör en `PUT` begäran på/ExtendedType{ID} med följande nya lista. Den angivna nytto lasten ska vara samma som det svar som du fick tidigare. Den nya enheten ska läggas till i slutet av listan med värden.

Mer information om/ExtendedType-API: et finns i [FarmBeats-Swagger](https://aka.ms/FarmBeatsSwagger).

## <a name="next-steps"></a>Nästa steg

Nu har du en kopplings Docker-komponent som kan integreras med FarmBeats. Ta sedan reda på hur du kan [få väder data](get-weather-data-from-weather-partner.md) med hjälp av Docker-avbildningen i FarmBeats. 
