---
title: Sensorpartnerintegration
description: I den här artikeln beskrivs integrering av sensor partner.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.openlocfilehash: 8d1b8203fa50609daf59431c2cfecba68eba52b1
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102179756"
---
# <a name="sensor-partner-integration"></a>Sensorpartnerintegration

Den här artikeln innehåller information om Azure FarmBeats **Translator** -komponenten som möjliggör integrering av sensor partner.

Med den här komponenten kan partners integrera med FarmBeats med hjälp av FarmBeats Datahub-API: er och skicka kund enhets data och telemetri till FarmBeats Datahub. När data är tillgängliga i FarmBeats visualiseras de med FarmBeats-acceleratorn och kan användas för data fusion och för att skapa maskin inlärnings-och artificiell Intelligence-modeller.

## <a name="before-you-start"></a>Innan du börjar

För att utveckla Translator-komponenten behöver du följande autentiseringsuppgifter som ger åtkomst till FarmBeats-API: erna.

- API-slutpunkt
- Klient-ID:t
- Klient-ID
- Client Secret (Klienthemlighet)
- EventHub-anslutningssträng

Se det här avsnittet för att hämta ovanstående autentiseringsuppgifter: [Aktivera enhets integrering](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats)

## <a name="translator-development"></a>Translator-utveckling

**REST API-baserad integrering**

Sensor data integrerings funktionerna i FarmBeats exponeras via REST API. Funktionerna omfattar metadata-definition, enhets-och sensor etablering samt hantering av enheter och sensorer.

**Inmatning av telemetri**

Telemetri-data mappas till ett kanoniskt meddelande som publiceras på Azure Event Hubs för bearbetning. Azure Event Hubs är en tjänst som gör att du kan mata in real tids data (telemetri) från anslutna enheter och program.

**API-utveckling**

API: erna innehåller Swagger teknisk dokumentation. Mer information om API: er och deras motsvarande förfrågningar och svar finns i [Swagger](https://aka.ms/FarmBeatsSwagger).

**Autentisering**

FarmBeats använder Microsoft Azure Active Directory autentisering.Azure App Service tillhandahåller stöd för inbyggd autentisering och auktorisering.

Mer information finns i [Azure Active Directory](../../app-service/overview-authentication-authorization.md).

FarmBeats Datahub använder Bearer-autentisering, som behöver följande autentiseringsuppgifter:
   - Klient-ID
   - Klienthemlighet
   - Klient-ID:t

Med dessa autentiseringsuppgifter kan anroparen begära en åtkomsttoken. Token måste skickas i efterföljande API-begäranden i avsnittet rubrik enligt följande:

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

Följande exempel på python-kod ger åtkomst-token som kan användas för efterföljande API-anrop till FarmBeats.

```python
import requests
import json
import msal

# Your service principal App ID
CLIENT_ID = "<CLIENT_ID>"
# Your service principal password
CLIENT_SECRET = "<CLIENT_SECRET>"
# Tenant ID for your Azure subscription
TENANT_ID = "<TENANT_ID>"

AUTHORITY_HOST = 'https://login.microsoftonline.com'
AUTHORITY = AUTHORITY_HOST + '/' + TENANT_ID

ENDPOINT = "https://<yourfarmbeatswebsitename-api>.azurewebsites.net"
SCOPE = ENDPOINT + "/.default"

context = msal.ConfidentialClientApplication(CLIENT_ID, authority=AUTHORITY, client_credential=CLIENT_SECRET)
token_response = context.acquire_token_for_client(SCOPE)
# We should get an access token here
access_token = token_response.get('access_token')
```


**Rubriker för HTTP-begäran**

Här är de vanligaste begärandehuvuden som måste anges när du gör ett API-anrop till FarmBeats Datahub.


**Huvud** | **Beskrivning och exempel**
--- | ---
Content-Type | Formatet för begäran (Content-Type: Application/ <format> ). För FarmBeats Datahub-API: er är formatet JSON. Innehålls typ: Application/JSON
Auktorisering | Anger den åtkomsttoken som krävs för att göra ett API-anrop. Auktorisering: Bearer <Access-Token>
Acceptera | Svars formatet. För FarmBeats Datahub-API: er är formatet JSON. Acceptera: Application/JSON

**API-begäranden**

Om du vill göra en REST API-begäran kombinerar du HTTP-metoden (GET, POST eller tag), URL: en till API-tjänsten, Uniform Resource Identifier (URI) till en resurs att fråga, skicka data till, uppdatera eller ta bort och en eller flera HTTP-begärandehuvuden. API-tjänstens URL är den API-slutpunkt som du anger. Här är ett exempel: https:// \<yourdatahub-website-name> . azurewebsites.net

Alternativt kan du inkludera frågeparametrar på GET-anrop för att filtrera, begränsa storleken på och sortera data i svaren.

Följande exempel förfrågan är att hämta listan över enheter.

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>"
```
De flesta GET-, POST-och parkera-anrop kräver en text för JSON-begäran.

Följande exempel förfrågan är att skapa en enhet. (Det här exemplet har en ingångs-JSON med begär ande texten.)

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>Dataformat

JSON är ett gemensamt språk oberoende data format som ger en enkel text representation av godtyckliga data strukturer. Mer information finns i [JSON.org](http://json.org).

## <a name="metadata-specifications"></a>Specifikationer för metadata

FarmBeats Datahub har följande API: er som gör det möjligt för enhets partner att skapa och hantera metadata för enheter eller sensorer.

- /**DeviceModel**: DeviceModel motsvarar enhetens metadata, till exempel tillverkaren och typen av enhet, som antingen är gateway eller nod.
- /**Enhet**: enheten motsvarar en fysisk enhet som finns i Server gruppen.
- /**SensorModel**: SensorModel motsvarar sensorns metadata, till exempel tillverkaren, typen av sensor, som är antingen analog eller digital och sensor måttet, till exempel omgivnings temperatur och tryck.
- /**Sensor**: sensorn motsvarar en fysisk sensor som registrerar värden. En sensor är vanligt vis ansluten till en enhet med ett enhets-ID.

  DeviceModel | Beskrivning |
  --- | ---
  Typ (nod, Gateway)  | Typ av enhet-nod eller gateway |
  Tillverkare  | Tillverkarens namn |
  ProductCode  | Enhetens produkt kod eller modell namn eller nummer. Till exempel EnviroMonitor # 6800. |
  Portar  | Port namn och-typ, som är digital eller analog.  |
  Namn  | Namn för att identifiera resursen. Till exempel modell namn eller produkt namn. |
  Beskrivning  | Ange en meningsfull beskrivning av modellen. |
  Egenskaper  | Ytterligare egenskaper från tillverkaren. |
  **Enhet** | **Beskrivning** |
  DeviceModelId  |ID för associerad enhets modell. |
  HardwareId   |Unikt ID för enheten, till exempel en MAC-adress.  |
  ReportingInterval |Rapport intervall i sekunder. |
  Location    |Enhets-latitud (-90 till + 90), longitud (-180 till 180) och höjning (i meter). |
  ParentDeviceId | ID för den överordnade enhet som enheten är ansluten till. Om en nod till exempel är ansluten till en gateway har noden parentDeviceID som gateway. |
  Namn  | Namn för att identifiera resursen. Enhets partner måste skicka ett namn som stämmer överens med enhets namnet på enhets partner sidan. Om enhetens namn är användardefinierat på enhets partner sidan, ska samma användardefinierade namn spridas till FarmBeats.  |
  Beskrivning  | Ange en meningsfull beskrivning.  |
  Egenskaper  |Ytterligare egenskaper från tillverkaren.  |
  **SensorModel** | **Beskrivning** |
  Typ (analog, digital)  |Nämna analog eller digital sensor.|
  Tillverkare  | Tillverkarens namn. |
  ProductCode  | Produkt kod eller modell namn eller nummer. Till exempel RS-CO2-N01.  |
  SensorMeasures > namn  | Sensor måttets namn. Endast gemener stöds. Ange djupet för mått från olika djup. Till exempel soil_moisture_15cm. Det här namnet måste vara konsekvent med telemetri-data. |
  SensorMeasures > datatype  | Datatyp för telemetri. För närvarande stöds Double. |
  SensorMeasures > typ  | Typ av mått för sensorer för telemetri. Följande är systemdefinierade typer: AmbientTemperature, CO2, djup, ElectricalConductivity, LeafWetness, length, LiquidLevel, nitrat, O2, PH, fosfat, PointInTime, kalium, press, RainGauge, RelativeHumidity, salinity, SoilMoisture, SoilTemperature, SolarRadiation, State, TimeDuration, UVRadiation, UVIndex, volym, WindDirection, WindRun, WindSpeed, evapotranspiration, parivärde. Mer information finns i/ExtendedType-API: et.
  SensorMeasures > enhet | Enhet för data för sensor telemetri. Följande är systemdefinierade enheter: nounit, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, kvicksilver, PSI, MilliMeter, CentiMeter, meter, tum, fot, mil, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, examen, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, liter, VolumetricIonContent, sekunder, MilliLiter, UnixTimestamp och MicroMolPerMeterSquaredPerSecond. Mer information finns i/ExtendedType-API: et.
  SensorMeasures > AggregationType  | Antingen ingen, genomsnitt, högsta, lägsta eller StandardDeviation.
  SensorMeasures > djup  | Sensorns djup i centimeter. Till exempel mätningen av fukt 10 cm under marken.
  Beskrivning av SensorMeasures->  | Ge en meningsfull beskrivning av måttet.
  Namn  | Namn för att identifiera resursen. Till exempel modell namnet eller produkt namnet.
  Beskrivning  | Ange en meningsfull beskrivning av modellen.
  Egenskaper  | Ytterligare egenskaper från tillverkaren.
  **Mäta**  | **Beskrivning** |
  HardwareId  | Unikt ID för sensorn som anges av tillverkaren.
  SensorModelId  | ID för associerad sensor modell.
  Location  | Sensor Latitude (-90 till + 90), longitud (-180 till 180) och höjning (i meter).
  Port > namn  |Namn och typ för den port som sensorn är ansluten till på enheten. Det måste vara samma namn som definieras i enhets modellen.
  DeviceId  | ID för den enhet som sensorn är ansluten till.
  Namn  | Namn för att identifiera resursen. Till exempel sensor namn, produkt namn och modell nummer eller produkt kod.
  Beskrivning  | Ange en meningsfull beskrivning.
  Egenskaper  | Ytterligare egenskaper från tillverkaren.

 Information om varje objekt och deras egenskaper finns i [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

 > [!NOTE]
 > API: erna returnerar unika ID: n för varje instans som skapats. Detta ID måste behållas av Translator för enhets hantering och metadata-synkronisering.


**Synkronisering av metadata**

Translator ska skicka uppdateringar för metadata. Uppdaterings scenarier är till exempel ändring av enhets-eller sensor namn och ändring av enhets-eller sensor plats.

Översättaren bör kunna lägga till nya enheter eller sensorer som har installerats av användaren efter att du har länkat FarmBeats. Om en enhet eller sensor har uppdaterats av användaren, ska samma sak uppdateras i FarmBeats för motsvarande enhet eller sensor. Vanliga scenarier som kräver uppdatering av en enhet eller sensor är en förändring på en enhets plats eller tillägg av sensorer i en nod.


> [!NOTE]
> Borttagning stöds inte för enhets-eller sensor-metadata.
>
> Om du vill uppdatera metadata, är det obligatoriskt att anropa/get/{ID} på enheten eller sensorn, uppdatera de ändrade egenskaperna och sedan göra en/put/{ID} så att alla egenskaper som anges av användaren inte går förlorade.

### <a name="add-new-types-and-units"></a>Lägg till nya typer och enheter

FarmBeats har stöd för att lägga till nya typer av sensor mått och enheter. Mer information om/ExtendedType-API: et finns i [Swagger](https://aka.ms/FarmBeatsSwagger).

## <a name="telemetry-specifications"></a>Specifikationer för telemetri

Telemetri-data mappas till ett kanoniskt meddelande som publiceras på Azure Event Hubs för bearbetning. Azure Event Hubs är en tjänst som gör att du kan mata in real tids data (telemetri) från anslutna enheter och program.

## <a name="send-telemetry-data-to-farmbeats"></a>Skicka telemetridata till FarmBeats

Om du vill skicka telemetridata till FarmBeats skapar du en klient som skickar meddelanden till en Event Hub i FarmBeats. Mer information om telemetridata finns i [Skicka telemetri till en Event Hub](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md).

Här är ett exempel på en python-kod som skickar telemetri som en klient till en angiven Event Hub.

```python
import azure
from azure.eventhub import EventHubClient, Sender, EventData, Receiver, Offset
EVENTHUBCONNECTIONSTRING = "<EventHub Connection String provided by customer>"
EVENTHUBNAME = "<EventHub Name provided by customer>"

write_client = EventHubClient.from_connection_string(EVENTHUBCONNECTIONSTRING, eventhub=EVENTHUBNAME, debug=False)
sender = write_client.add_sender(partition="0")
write_client.run()
for i in range(5):
    telemetry = "<Canonical Telemetry message>"
    print("Sending telemetry: " + telemetry)
    sender.send(EventData(telemetry))
write_client.stop()

```

Det kanoniska meddelande formatet är följande:

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        }
      ]
    }
 ]
}
```
Alla nyckel namn i JSON för telemetri måste vara gemener. Exempel är DeviceID och sensordata.

Här är ett telemetri-meddelande:


```json
{
  "deviceid": "7f9b4b92-ba45-4a1d-a6ae-c6eda3a5bd12",
  "timestamp": "2019-06-22T06:55:02.7279559Z",
  "version" : "1",
  "sensors": [
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 15
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 42
        }
      ]
    },
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 20
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 89
        }
      ]
    }
  ]
}

```

> [!NOTE]
> Följande avsnitt är relaterade till andra ändringar (t. ex. Användar gränssnitt, fel hantering osv.) att sensor partnern kan referera till i utveckla Translator-komponenten.


## <a name="link-a-farmbeats-account"></a>Länka ett FarmBeats-konto

När kunderna har köpt och distribuerat enheter eller sensorer kan de komma åt enhets data och telemetri på enhets partnerns SaaS-portal (Software as a Service). Enhets partner kan göra det möjligt för kunder att länka sitt konto till sin FarmBeats-instans i Azure genom att tillhandahålla ett sätt att ange följande autentiseringsuppgifter:

   - Visnings namn (ett valfritt fält där användare kan definiera ett namn för den här integrationen)
   - API-slutpunkt
   - Klient-ID:t
   - Klient-ID
   - Klienthemlighet
   - EventHub-anslutningssträng
   - Startdatum

   > [!NOTE]
   > Start datumet aktiverar den historiska datafeeden, det vill säga data från det datum som anges av användaren.

## <a name="unlink-farmbeats"></a>Ta bort länk till FarmBeats

Enhets partner kan göra det möjligt för kunder att ta bort länkar till en befintlig FarmBeats-integrering. Borttagning av alla enhets-eller sensor-metadata som skapades i FarmBeats-Datahub tas inte bort när FarmBeats tas bort. Att ta bort länkar gör följande:

   - Stoppar telemetri-flöde.
   - Tar bort och raderar integrerings uppgifterna på enhets partnern.

## <a name="edit-farmbeats-integration"></a>Redigera FarmBeats-integrering

Med enhets partner kan kunderna redigera FarmBeats-integrerings inställningarna om klient hemligheten eller anslutnings strängen ändras. I det här fallet kan endast följande fält redige ras:

   - Visnings namn (om tillämpligt)
   - Klient hemlighet (ska visas i formatet "2x8 * * * * * * * * *" eller funktionen Visa/Dölj i stället för klartext)
   - Anslutnings sträng (ska visas i formaten "2x8 * * * * * * * * * *" eller Visa/Dölj i stället för klartext)

## <a name="view-the-last-telemetry-sent"></a>Visa senaste telemetri som skickats

Enhets partner kan göra det möjligt för kunder att Visa tidsstämpeln för den senaste telemetri som har skickats, som finns under **telemetri som skickas**. Detta är den tid då den senaste Telemetrin skickades till FarmBeats.

## <a name="troubleshooting-and-error-management"></a>Fel sökning och fel hantering

**Fel söknings alternativ eller support**

Om kunden inte kan ta emot enhets data eller telemetri i FarmBeats-instansen som angetts bör enhets partnern ge support och en mekanism för fel sökning.

**Data kvarhållning för telemetri**

Telemetridata bör också behållas under en fördefinierad tids period, så att den kan vara användbar vid fel sökning eller omsändning av telemetri om ett fel eller data förlust inträffar.

**Fel hantering eller fel meddelande**

Om ett fel påverkar enhetens eller sensorns metadata eller data flödet för data integrering eller telemetri i enhets partner systemet, ska kunden få ett meddelande. En mekanism för att lösa eventuella fel bör också utformas och implementeras.

**Check lista för anslutning**

Enhets tillverkare eller partner kan använda följande check lista för att säkerställa att de uppgifter som anges av kunden är korrekta:

   - Kontrol lera om en åtkomsttoken tas emot med de autentiseringsuppgifter som angavs.
   - Kontrol lera om ett API-anrop lyckas med den åtkomsttoken som togs emot.
   - Kontrol lera om EventHub-klientprogrammet har upprättats.

## <a name="next-steps"></a>Nästa steg

Mer information om REST API finns i [REST API](rest-api-in-azure-farmbeats.md).