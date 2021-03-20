---
title: Skapa en avancerad simulerad enhets modell – Azure | Microsoft Docs
description: I den här instruktions guiden får du lära dig hur du skapar en avancerad enhets modell för användning med lösnings Accelerator för enhets simulering.
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
ms.openlocfilehash: 58891bdb4601744129c97454f43202d3ba07b709
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96852565"
---
# <a name="create-an-advanced-device-model"></a>Skapa en avancerad enhetsmodell

Den här instruktions guiden beskriver JSON-och JavaScript-filerna som definierar en anpassad enhets modell. Artikeln innehåller några exempel på enhets modell definitions filer och visar hur du överför dem till din enhets simulerings instans. Du kan skapa avancerade enhets modeller för att simulera mer realistiska enhets beteenden för dina tester.

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill följa stegen i den här instruktions guiden behöver du en distribuerad instans av enhets simulering i din Azure-prenumeration.

Om du inte redan har distribuerat enhets simuleringen kan du läsa [distribuera enhets simulering](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md) på GitHub.

### <a name="open-device-simulation"></a>Öppna enhetssimulering

Om du inte redan har distribuerat enhets simuleringen kan du läsa [distribuera enhets simulering](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md) på GitHub.

## <a name="device-models"></a>Enhetsmodeller

Varje simulerad enhet tillhör en speciell enhets modell som definierar simulerings beteendet. Det här beteendet omfattar hur ofta du skickar telemetri, vilken typ av meddelanden som ska skickas och vilka metoder som stöds.

Du definierar en enhets modell med hjälp av en definitions fil för JSON-enheter och en uppsättning JavaScript-filer. Dessa JavaScript-filer definierar simulerings beteendet, till exempel slumpmässig telemetri och metod logik.

En typisk enhets modell har:

* En JSON-fil för varje enhets modell (till exempel elevator.jspå).
* En skript fil med JavaScript-beteende för varje enhets modell (till exempel elevator-state.js)
* En skript fil för JavaScript-metoden för varje enhets metod (till exempel elevator-go-down.js)

> [!NOTE]
> Alla enhets modeller definierar inte metoder. Därför kan en enhets modell kanske inte ha metod skript. Alla enhets modeller måste dock ha ett beteende skript.

## <a name="device-definition-file"></a>Enhets definitions fil

Varje enhets definitions fil innehåller information om en simulerad enhets modell, inklusive följande information:

* Enhets modell namn: sträng.
* Protokoll: AMQP | MQTT | Inkommande.
* Den inledande enhetens tillstånd.
* Hur ofta du ska uppdatera enhets statusen.
* Vilken JavaScript-fil som ska användas för att uppdatera enhetens tillstånd.
* En lista över telemetri-meddelanden som ska skickas, var och en med en angiven frekvens.
* Schemat för telemetri-meddelanden som används av backend-programmet för att parsa telemetri tas emot.
* En lista över metoder som stöds och den JavaScript-fil som ska användas för att simulera varje metod.

### <a name="file-schema"></a>Filschema

Schema versionen är alltid "1.0.0" och är speciell för filens format:

```json
"SchemaVersion": "1.0.0"
```

### <a name="device-model-description"></a>Beskrivning av enhets modell

Följande egenskaper beskriver enhets modellen. Varje typ har en unik identifierare, en semantisk version, ett namn och en beskrivning:

```json
"Id": "chiller-01",
"Version": "0.0.1",
"Name": "Chiller",
"Description": "Chiller with external temperature and humidity sensors."
```

### <a name="iot-protocol"></a>IoT-protokoll

IoT-enheter kan ansluta med olika protokoll. Med simuleringen kan du använda antingen **AMQP**, **MQTT** eller **http**:

```json
"Protocol": "AMQP"
```

### <a name="simulated-device-state"></a>Simulerat enhets tillstånd

Varje simulerad enhet har ett internt tillstånd som måste definieras. Statusen definierar också de egenskaper som kan rapporteras i telemetri. Till exempel kan en kylning ha ett initialt tillstånd, till exempel:

```json
"InitialState": {
    "temperature": 50,
    "humidity": 40
},
```

En rörlig enhet med flera sensorer kan ha fler egenskaper, till exempel:

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

Enhetens tillstånd lagras i minnet av simulerings tjänsten och tillhandahålls som indata till JavaScript-funktionen. JavaScript-funktionen kan bestämma:

* Om du vill ignorera status och generera vissa slumpmässiga data.
* För att uppdatera enhets statusen på ett realistiskt sätt för ett visst scenario.

Funktionen som genererar statusen tas också emot som inmatade:

* Enhets-ID.
* Enhetsmodellen.
* Aktuell tid. Det här värdet gör det möjligt att generera olika data efter enhet och tid.

### <a name="generating-telemetry-messages"></a>Genererar telemetri-meddelanden

Simulerings tjänsten kan skicka flera typer av telemetri för varje enhet. Telemetri omfattar vanligt vis data från enhets tillstånd. Ett simulerat rum kan till exempel skicka information om temperatur och fuktighet var tionde sekund. Observera plats hållarna i följande kodfragment, som automatiskt ersätts med värden från enhets tillstånd:

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

Plats hållarna använder en speciell syntax **$ {Name}** där **namn** är en nyckel från det enhets tillstånds objekt som returneras av Java Script **main** -funktionen. Strängar ska vara citat tecken, medan tal inte borde.

#### <a name="message-schema"></a>Meddelande schema

Varje meddelande typ måste ha ett väldefinierat schema. Meddelande schemat publiceras också till IoT Hub, så att backend-programmen kan återanvända informationen för att tolka inkommande telemetri.

Schemat stöder JSON-format, som möjliggör enkel parsning, transformering och analys, i flera system och tjänster.

Fälten som visas i schemat kan vara av följande typer:

* Objekt – serialiserat med JSON
* Binärt serialiserat med base64
* Text
* Boolesk
* Integer
* Double
* DateTime

### <a name="supported-methods"></a>Metoder som stöds

Simulerade enheter kan även reagera på metod anrop, i vilket fall de kör en viss logik och ger svar på några svar. På samma sätt som simuleringen lagras metod logiken i en JavaScript-fil och kan samverka med enhetens tillstånd. Exempel:

```json
"CloudToDeviceMethods": {
    "Start": {
        "Type": "JavaScript",
        "Path": "release-pressure.js"
    }
}
```

## <a name="create-a-device-definition-file"></a>Skapa en enhets definitions fil

I den här instruktions guiden kan du se hur du skapar en enhets modell för en drönare. Drönare kommer slumpmässigt att flyga runt en inledande uppsättning koordinater som ändrar plats och höjd.

Kopiera följande JSON till en text redigerare och spara den som **drone.jspå**.

### <a name="device-definition-json-example"></a>JSON-exempel för enhets definition

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

## <a name="behavior-script-files"></a>Skript fil för beteende

Koden i beteende skript filen flyttar drönare. Skriptet ändrar drönare-höjningen och platsen genom att ändra enhetens minnes status.

JavaScript-filerna måste ha en **huvud** funktion som accepterar två parametrar:

* Ett **kontext** objekt som innehåller tre egenskaper:
    * **currentTime** som en sträng med formatet **åååå-mm-dd'T'HH: mm: sszzz**.
    * **deviceId**. Till exempel **simulerad. hiss. 123**.
    * **deviceModel**. Till exempel en **hiss**.
* Ett **tillstånds** objekt som är det värde som returneras av funktionen i föregående anrop. Enhetens tillstånd underhålls av simulerings tjänsten och används för att generera telemetri-meddelanden.

**Main** -funktionen returnerar det nya enhets läget. Exempel:

```JavaScript
function main(context, state) {

    // Use context if the simulation depends on
    // time or device details.
    // Execute some logic, updating 'state'

    return state;
}
```

## <a name="create-a-behavior-script-file"></a>Skapa en beteende skript fil

Kopiera följande JavaScript-skript till en text redigerare och spara det som **drone-state.js**.

### <a name="device-model-javascript-simulation-example"></a>Exempel på enhets modellens JavaScript-simulering

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

## <a name="create-a-method-script-file"></a>Skapa en metod skript fil

Metod skript liknar beteende skript. De definierar enhetens beteende när en speciell moln till enhet-metod anropas.

Drönare återkalla skript anger drönares koordinater till en fast punkt för att simulera drönare som returnerar start.

Kopiera följande JavaScript-skript till en text redigerare och spara det som **droneRecall-method.js**.

### <a name="device-model-javascript-simulation-example"></a>Exempel på enhets modellens JavaScript-simulering

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

Det går inte att koppla en fel sökare till en beteende fil som körs, men det går att skriva information till tjänst loggen med hjälp av funktionen **log** . Vid syntaxfel Miss lyckas tolken och skriver information om undantaget till loggen.

Loggnings exempel:

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

## <a name="deploy-an-advanced-device-model"></a>Distribuera en avancerad enhets modell

Om du vill distribuera en avancerad enhets modell laddar du upp filerna i enhets simulerings instansen:

Välj **Enhetsmodeller** på menyraden. Sidan **enhets modeller** listar de enhets modeller som är tillgängliga i den här instansen av enhets simulering:

![Enhetsmodeller](media/iot-accelerators-device-simulation-advanced-device/devicemodelnav.png)

Klicka på **+ Lägg till enhetsmodeller** i det övre högra hörnet på sidan:

![Lägga till enhetsmodell](media/iot-accelerators-device-simulation-advanced-device/devicemodels.png)

Klicka på **Avancerat** för att öppna fliken Avancerad enhets modell:

![Fliken Avancerat](media/iot-accelerators-device-simulation-advanced-device/advancedtab.png)

Klicka på **Bläddra** och välj de JSON-och JavaScript-filer som du skapade. Se till att markera alla tre filerna. Om en fil saknas Miss lyckas verifieringen:

![Bläddra bland filer](media/iot-accelerators-device-simulation-advanced-device/browse.png)

Om filerna klarar verifieringen klickar du på **Spara** och din enhets modell är redo att användas i en simulering. Annars korrigerar du eventuella fel och laddar om filerna:

![Spara](media/iot-accelerators-device-simulation-advanced-device/validated.png)

## <a name="next-steps"></a>Nästa steg

I den här instruktions guiden har du lärt dig om enhets modellens filer som används i enhets simuleringen och hur du skapar en avancerad enhets modell. Härnäst kanske du vill utforska hur du [använder Time Series Insights för att visualisera telemetri som skickas från Device simulering Solution Accelerator](./iot-accelerators-device-simulation-time-series-insights.md).