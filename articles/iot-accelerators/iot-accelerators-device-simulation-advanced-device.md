---
title: Skapa en avancerad simulerad enhetsmodell – Azure| Microsoft Docs
description: I den här guiden får du lära dig hur du skapar en avancerad enhetsmodell för användning med lösningsacceleratorn för enhetssimulering.
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: troyhop
ms.custom:
- mvc
- amqp
- mqtt
- devx-track-js
ms.openlocfilehash: dc2eac4e784d4224581078348f12b231befe1f35
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714118"
---
# <a name="create-an-advanced-device-model"></a>Skapa en avancerad enhetsmodell

I den här guiden beskrivs JSON- och JavaScript-filer som definierar en anpassad enhetsmodell. Artikeln innehåller några exempel på definitionsfiler för enhetsmodeller och visar hur du laddar upp dem till din instans av enhetssimulering. Du kan skapa avancerade enhetsmodeller för att simulera mer realistiska enhetsbeteenden för testning.

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill följa stegen i den här instruktionerna behöver du en distribuerad instans av enhetssimulering i din Azure-prenumeration.

Om du inte har distribuerat enhetssimuleringen än kan du gå till [Distribution av enhetssimulering](https://github.com/Azure/azure-iot-pcs-device-simulation/blob/master/README.md) på GitHub.

### <a name="open-device-simulation"></a>Öppna enhetssimulering

Om du inte har distribuerat enhetssimuleringen än kan du gå till [Distribution av enhetssimulering](https://github.com/Azure/azure-iot-pcs-device-simulation/blob/master/README.md) på GitHub.

## <a name="device-models"></a>Enhetsmodeller

Varje simulerad enhet tillhör en specifik enhetsmodell som definierar simuleringsbeteendet. Det här beteendet omfattar hur ofta telemetri ska skickas, vilken typ av meddelanden som ska skickas och vilka metoder som stöds.

Du definierar en enhetsmodell med hjälp av en JSON-enhetsdefinitionsfil och en uppsättning JavaScript-filer. Dessa JavaScript-filer definierar simuleringsbeteendet, till exempel slumpmässig telemetri och metodlogiken.

En typisk enhetsmodell har:

* En JSON-fil för varje enhetsmodell (till exempel elevator.jspå).
* En JavaScript-beteendeskriptfil för varje enhetsmodell (till exempel elevator-state.js)
* En skriptfil för JavaScript-metoden för varje enhetsmetod (till exempel elevator-go-down.js)

> [!NOTE]
> Det är inte alla enhetsmodeller som definierar metoder. Därför kan det hända att en enhetsmodell inte har metodskript. Alla enhetsmodeller måste dock ha ett beteendeskript.

## <a name="device-definition-file"></a>Enhetsdefinitionsfil

Varje enhetsdefinitionsfil innehåller information om en simulerad enhetsmodell, inklusive följande information:

* Enhetsmodellnamn: sträng.
* Protokoll: AMQP-| MQTT-| HTTP.
* Det ursprungliga enhetstillståndet.
* Hur ofta enhetens tillstånd ska uppdateras.
* Vilken JavaScript-fil som ska användas för att uppdatera enhetens tillstånd.
* En lista över telemetrimeddelanden som ska skickas, vart och ett med en viss frekvens.
* Schemat för telemetrimeddelanden som används av backend-programmet för att parsa den telemetri som tas emot.
* En lista över metoder som stöds och JavaScript-filen som ska användas för att simulera varje metod.

### <a name="file-schema"></a>Filschema

Schemaversionen är alltid "1.0.0" och är specifik för formatet för den här filen:

```json
"SchemaVersion": "1.0.0"
```

### <a name="device-model-description"></a>Beskrivning av enhetsmodell

Följande egenskaper beskriver enhetsmodellen. Varje typ har en unik identifierare, en semantisk version, ett namn och en beskrivning:

```json
"Id": "chiller-01",
"Version": "0.0.1",
"Name": "Chiller",
"Description": "Chiller with external temperature and humidity sensors."
```

### <a name="iot-protocol"></a>IoT-protokoll

IoT-enheter kan ansluta med olika protokoll. Med simuleringen kan du använda **antingen AMQP,** **MQTT** eller **HTTP:**

```json
"Protocol": "AMQP"
```

### <a name="simulated-device-state"></a>Simulerat enhetstillstånd

Varje simulerad enhet har ett internt tillstånd som måste definieras. Tillståndet definierar också de egenskaper som kan rapporteras i telemetri. Ett kylaggregat kan till exempel ha ett ursprungligt tillstånd, till exempel:

```json
"InitialState": {
    "temperature": 50,
    "humidity": 40
},
```

En enhet som flyttas med flera sensorer kan ha fler egenskaper, till exempel:

```json
"InitialState": {
    "latitude": 47.445301,
    "longitude": -122.296307,
    "speed": 30.0,
    "speed_unit": "mph",
    "cargotemperature": 38.0,
    "cargotemperature_unit": "F"
}
```

Enhetstillstånd sparas i minnet av simuleringstjänsten och tillhandahålls som indata till JavaScript-funktionen. JavaScript-funktionen kan bestämma:

* Ignorera tillståndet och generera slumpmässiga data.
* För att uppdatera enhetens tillstånd på något realistiskt sätt för ett visst scenario.

Funktionen som genererar tillståndet tar även emot som indata:

* Enhets-ID:t.
* Enhetsmodellen.
* Aktuell tid. Det här värdet gör det möjligt att generera olika data per enhet och efter tid.

### <a name="generating-telemetry-messages"></a>Generera telemetrimeddelanden

Simuleringstjänsten kan skicka flera telemetrityper för varje enhet. Telemetri innehåller vanligtvis data från enhetens tillstånd. Ett simulerat rum kan till exempel skicka information om temperatur och luftfuktighet var 10:e sekund. Observera platshållarna i följande kodfragment, som automatiskt ersätts med värden från enhetens tillstånd:

```json
"Telemetry": [
    {
        "Interval": "00:00:10",
        "MessageTemplate":
            "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\",\"humidity\":\"${humidity}\"}",
        "MessageSchema": {
            "Name": "RoomComfort;v1",
            "Format": "JSON",
            "Fields": {
                "temperature": "double",
                "temperature_unit": "text",
                "humidity": "integer"
            }
        }
    }
],
```

Platshållarna använder en särskild syntax **${NAME}** där **NAME** är en nyckel från enhetstillståndsobjektet som returneras av **JavaScript-huvudfunktionen.** Strängar bör citeras, medan siffror inte bör det.

#### <a name="message-schema"></a>Meddelandeschema

Varje meddelandetyp måste ha ett väldefinierat schema. Meddelandeschemat publiceras också till IoT Hub, så att serverprogram kan återanvända informationen för att tolka inkommande telemetri.

Schemat stöder JSON-format, vilket möjliggör enkel parsning, transformering och analys i flera system och tjänster.

Fälten som anges i schemat kan vara av följande typer:

* Objekt – serialiserat med JSON
* Binär – serialiserad med base64
* Text
* Boolesk
* Integer
* Double
* DateTime

### <a name="supported-methods"></a>Metoder som stöds

Simulerade enheter kan också reagera på metod-anrop, i vilket fall de kör logik och ger svar. Precis som simuleringen lagras metodlogiken i en JavaScript-fil och kan interagera med enhetstillståndet. Exempel:

```json
"CloudToDeviceMethods": {
    "Start": {
        "Type": "JavaScript",
        "Path": "release-pressure.js"
    }
}
```

## <a name="create-a-device-definition-file"></a>Skapa en enhetsdefinitionsfil

I den här guiden får du se hur du skapar en enhetsmodell för en drönare. Drönaren kommer slumpmässigt att gå runt en inledande uppsättning koordinater som ändrar plats och höjd.

Kopiera följande JSON till en textredigerare och spara den som **drone.jspå**.

### <a name="device-definition-json-example"></a>JSON-exempel för enhetsdefinition

```json
{
  "SchemaVersion": "1.0.0",
  "Id": "drone",
  "Version": "0.0.1",
  "Name": "Drone",
  "Description": "Simple drone.",
  "Protocol": "AMQP",
  "Simulation": {
    "InitialState": {
      "velocity": 0.0,
      "velocity_unit": "mm/sec",
      "acceleration": 0.0,
      "acceleration_unit": "mm/sec^2",
      "latitude": 47.476075,
      "longitude": -122.192026,
      "altitude": 0.0
    },
    "Interval": "00:00:05",
    "Scripts": [{
      "Type": "JavaScript",
      "Path": "drone-state.js"
    }]
  },
  "Properties": {
    "Type": "Drone",
    "Firmware": "1.0",
    "Model": "P-96"
  },
  "Tags": {
    "Owner": "Contoso"
  },
  "Telemetry": [{
      "Interval": "00:00:05",
      "MessageTemplate": "{\"velocity\":\"${velocity}\",\"acceleration\":\"${acceleration}\",\"position\":\"${latitude}|${longitude}|${altitude}\"}",
      "MessageSchema": {
        "Name": "drone-event-sensor;v1",
        "Format": "JSON",
        "Fields": {
          "velocity": "double",
          "velocity_unit": "text",
          "acceleration": "double",
          "acceleration_unit": "text",
          "latitude": "double",
          "longitude": "double",
          "altitude": "double"
        }
      }
    }
  ],
    "CloudToDeviceMethods": {
        "RecallDrone": {
            "Type": "JavaScript",
            "Path": "droneRecall-method.js"
        }
    }
}
```

## <a name="behavior-script-files"></a>Beteendeskriptfiler

Koden i beteendeskriptfilen flyttar drönaren. Skriptet ändrar drönarens höjning och plats genom att manipulera enhetens minnestillstånd.

JavaScript-filerna måste ha **en huvudfunktion** som accepterar två parametrar:

* Ett **kontextobjekt** som innehåller tre egenskaper:
    * **currentTime** som en sträng med formatet **yyyy-MM-dd'T'T'HH:mm:sszzz**.
    * **deviceId**. Till exempel **Simulated.Elevator.123**.
    * **deviceModel**. Till exempel **Hiss**.
* Ett **tillståndsobjekt** som är det värde som returnerades av funktionen i föregående anrop. Det här enhetstillståndet underhålls av simuleringstjänsten och används för att generera telemetrimeddelanden.

**Main-funktionen** returnerar det nya enhetstillståndet. Exempel:

```JavaScript
function main(context, state) {

    // Use context if the simulation depends on
    // time or device details.
    // Execute some logic, updating 'state'

    return state;
}
```

## <a name="create-a-behavior-script-file"></a>Skapa en beteendeskriptfil

Kopiera följande JavaScript till en textredigerare och spara det som **drone-state.js**.

### <a name="device-model-javascript-simulation-example"></a>Exempel på enhetsmodellsimulering med JavaScript

```JavaScript
"use strict";

// Position control
const DefaultLatitude = 47.476075;
const DefaultLongitude = -122.192026;
const DistanceVariation = 10;

// Altitude control
const DefaultAltitude = 0.0;
const AverageAltitude = 499.99;
const AltitudeVariation = 5;

// Velocity control
const AverageVelocity = 60.00;
const MinVelocity = 20.00;
const MaxVelocity = 120.00;
const VelocityVariation = 5;

// Acceleration control
const AverageAcceleration = 2.50;
const MinAcceleration = 0.01;
const MaxAcceleration = 9.99;
const AccelerationVariation = 1;

// Display control for position and other attributes
const GeoSpatialPrecision = 6;
const DecimalPrecision = 2;

// Default state
var state = {
    velocity: 0.0,
    velocity_unit: "mm/sec",
    acceleration: 0.0,
    acceleration_unit: "mm/sec^2",
    latitude: DefaultLatitude,
    longitude: DefaultLongitude,
    altitude: DefaultAltitude
};

// Default device properties
var properties = {};

/**
 * Restore the global state using data from the previous iteration.
 *
 * @param previousState device state from the previous iteration
 * @param previousProperties device properties from the previous iteration
 */
function restoreSimulation(previousState, previousProperties) {
    // If the previous state is null, force a default state
    if (previousState) {
        state = previousState;
    } else {
        log("Using default state");
    }

    if (previousProperties) {
        properties = previousProperties;
    } else {
        log("Using default properties");
    }
}

/**
 * Simple formula generating a random value around the average
 * in between min and max
 */
function vary(avg, percentage, min, max) {
    var value = avg * (1 + ((percentage / 100) * (2 * Math.random() - 1)));
    value = Math.max(value, min);
    value = Math.min(value, max);
    return value;
}

/**
 * Entry point function called by the simulation engine.
 * Returns updated simulation state.
 * Device property updates must call updateProperties() to persist.
 *
 * @param context             The context contains current time, device model and id
 * @param previousState       The device state since the last iteration
 * @param previousProperties  The device properties since the last iteration
 */
/*jslint unparam: true*/
function main(context, previousState, previousProperties) {

    // Restore the global state before generating the new telemetry, so that
    // the telemetry can apply changes using the previous function state.
    restoreSimulation(previousState, previousProperties);

    state.acceleration = vary(AverageAcceleration, AccelerationVariation, MinAcceleration, MaxAcceleration).toFixed(DecimalPrecision);
    state.velocity = vary(AverageVelocity, VelocityVariation, MinVelocity, MaxVelocity).toFixed(DecimalPrecision);

    // Use the last coordinates to calculate the next set with a given variation
    var coords = varylocation(Number(state.latitude), Number(state.longitude), DistanceVariation);
    state.latitude = Number(coords.latitude).toFixed(GeoSpatialPrecision);
    state.longitude = Number(coords.longitude).toFixed(GeoSpatialPrecision);

    // Fluctuate altitude between given variation constant by more or less
    state.altitude = vary(AverageAltitude, AltitudeVariation, AverageAltitude - AltitudeVariation, AverageAltitude + AltitudeVariation).toFixed(DecimalPrecision);

    return state;
}

/**
 * Generate a random geolocation at some distance (in miles)
 * from a given location
 */
function varylocation(latitude, longitude, distance) {
    // Convert to meters, use Earth radius, convert to radians
    var radians = (distance * 1609.344 / 6378137) * (180 / Math.PI);
    return {
        latitude: latitude + radians,
        longitude: longitude + radians / Math.cos(latitude * Math.PI / 180)
    };
}
```

## <a name="create-a-method-script-file"></a>Skapa en metodskriptfil

Metodskript liknar beteendeskript. De definierar enhetsbeteendet när en specifik moln till enhet-metod anropas.

Skriptet för drönarkallning anger drönarens koordinater till en fast punkt för att simulera att drönaren återvänder hem.

Kopiera följande JavaScript till en textredigerare och spara det som **droneRecall-method.js**.

### <a name="device-model-javascript-simulation-example"></a>Exempel på enhetsmodellsimulering med JavaScript

```JavaScript
"use strict";

// Default state
var state = {
    velocity: 0.0,
    velocity_unit: "mm/sec",
    acceleration: 0.0,
    acceleration_unit: "mm/sec^2",
    latitude: 0.0,
    longitude: 0.0,
    altitude: 0.0
};

// Default device properties
var properties = {};

/**
 * Restore the global state using data from the previous iteration.
 *
 * @param previousState device state from the previous iteration
 * @param previousProperties device properties from the previous iteration
 */
function restoreSimulation(previousState, previousProperties) {
    // If the previous state is null, force a default state
    if (previousState) {
        state = previousState;
    } else {
        log("Using default state");
    }

    if (previousProperties) {
        properties = previousProperties;
    } else {
        log("Using default properties");
    }
}

/**
 * Entry point function called by the simulation engine.
 *
 * @param context        The context contains current time, device model and id, not used
 * @param previousState  The device state since the last iteration, not used
 * @param previousProperties  The device properties since the last iteration
 */
/*jslint unparam: true*/
function main(context, previousState, previousProperties) {

    // Restore the global device properties and the global state before
    // generating the new telemetry, so that the telemetry can apply changes
    // using the previous function state.
    restoreSimulation(previousState, previousProperties);

    //simulate the behavior of a drone when recalled
  state.latitude = 47.476075;
  state.longitude = -122.192026;
  return state;
}
```

## <a name="debugging-script-files"></a>Felsöka skriptfiler

Du kan inte koppla en felsökare till en beteendefil som körs, men det är möjligt att skriva information till tjänstloggen med hjälp av **loggfunktionen.** Vid syntaxfel misslyckas tolken och skriver information om undantaget till loggen.

Loggningsexempel:

```JavaScript
function main(context, state) {

    log("This message will appear in the service logs.");

    log(context.deviceId);

    if (typeof(state) !== "undefined" && state !== null) {
        log("Previous value: " + state.temperature);
    }

    // ...

    return updateState;
}
```

## <a name="deploy-an-advanced-device-model"></a>Distribuera en avancerad enhetsmodell

Om du vill distribuera din avancerade enhetsmodell laddar du upp filerna som din enhetssimuleringsinstans har:

Välj **Enhetsmodeller** på menyraden. På **sidan Enhetsmodeller** visas de enhetsmodeller som är tillgängliga i den här instansen av enhetssimuleringen:

![Enhetsmodeller](media/iot-accelerators-device-simulation-advanced-device/devicemodelnav.png)

Klicka på **+ Lägg till enhetsmodeller** i det övre högra hörnet på sidan:

![Lägga till enhetsmodell](media/iot-accelerators-device-simulation-advanced-device/devicemodels.png)

Klicka **på Avancerat** för att öppna fliken Avancerad enhetsmodell:

![Fliken Avancerat](media/iot-accelerators-device-simulation-advanced-device/advancedtab.png)

Klicka **på** Bläddra och välj de JSON- och JavaScript-filer som du skapade. Se till att markera alla tre filerna. Om någon fil saknas misslyckas verifieringen:

![Bläddra bland filer](media/iot-accelerators-device-simulation-advanced-device/browse.png)

Om filerna har valideras klickar du **på Spara** så är enhetsmodellen redo att användas i en simulering. Annars kan du åtgärda eventuella fel och ladda upp filerna igen:

![Spara](media/iot-accelerators-device-simulation-advanced-device/validated.png)

## <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig om enhetsmodellfilerna som används i enhetssimuleringen och hur du skapar en avancerad enhetsmodell. Därefter kanske du vill utforska hur du använder Time Series Insights för att visualisera telemetri som skickas från [lösningsacceleratorn för enhetssimulering.](./iot-accelerators-device-simulation-time-series-insights.md)