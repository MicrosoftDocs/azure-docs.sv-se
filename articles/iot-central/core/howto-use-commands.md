---
title: Använda enhets kommandon i en Azure IoT Central-lösning
description: Använda enhets kommandon i Azure IoT Central-lösningen. Den här självstudien visar hur du, som enhets utvecklare, använder enhets kommandon i klient programmet till ditt Azure IoT Central-program.
author: dominicbetts
ms.author: dobett
ms.date: 01/07/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: dd05a4880bdf077f63dc58575828e6180e415260
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98122599"
---
# <a name="how-to-use-commands-in-an-azure-iot-central-solution"></a>Använda kommandon i en Azure IoT Central-lösning

Den här instruktions guiden visar hur du, som en enhets utvecklare, använder kommandon som är definierade i en enhets mall.

En operatör kan använda IoT Central användar gränssnitt för att anropa ett kommando på en enhet. Kommandon styr enhetens beteende. En operatör kan till exempel anropa ett kommando för att starta om en enhet eller samla in diagnostikdata.

En enhet kan:

* Svara på ett kommando omedelbart.
* Svara på IoT Central när den tar emot kommandot och meddela sedan IoT Central när det *långvariga kommandot* har slutförts.

Som standard förväntar sig en enhet vara ansluten och fungerar inte om enheten inte kan nås. Om du väljer alternativet **kö om offline** i användar gränssnittet för enhets mal len kan ett kommando placeras i kö tills en enhet är online. Dessa *offline-kommandon* beskrivs i ett separat avsnitt längre fram i den här artikeln.

## <a name="define-your-commands"></a>Definiera dina kommandon

Standard kommandon skickas till en enhet för att instruera enheten att göra något. Ett kommando kan innehålla parametrar med ytterligare information. Ett kommando för att öppna en ventil på en enhet kan till exempel ha en parameter som anger hur mycket du vill öppna ventilen. Kommandon kan också ta emot ett retur värde när enheten har slutfört kommandot. Ett kommando som ber en enhet att köra en viss diagnostik kan till exempel få en diagnostisk rapport som ett retur värde.

Kommandon definieras som en del av en enhets mall. Följande skärm bild visar kommando definitionen **hämta Max-Min rapport** i enhets mal len **termostat** . Det här kommandot har både parametrarna för begäran och svar: 

:::image type="content" source="media/howto-use-commands/command-definition.png" alt-text="Skärm bild som visar kommandot Hämta max min rapport i termostat enhets mall":::

I följande tabell visas konfigurations inställningarna för en kommando funktion:

| Fält             |Beskrivning|
|-------------------|-----------|
|Visningsnamn       |Det kommando värde som används på instrument paneler och formulär.|
| Name            | Kommandots namn. IoT Central genererar ett värde för det här fältet från visnings namnet, men du kan välja ett eget värde om det behövs. Det här fältet måste vara alfanumeriskt. Enhets koden använder detta **namn** värde.|
| Typ av kapacitet | Kommandoprompt.|
| Köa om offline | Om kommandot ska göras till *offline* . |
| Description     | En beskrivning av kommando funktionen.|
| Kommentar     | Eventuella kommentarer om kommando funktionen.|
| Förfrågan     | Enhets kommandots nytto Last.|
| Svarsåtgärder     | Nytto lasten för enhetens kommando svar.|

Följande fragment visar JSON-representationen av kommandot i enhets modellen. I det här exemplet är svar svärdet en komplex **objekt** typ med flera fält:

```json
{
  "@type": "Command",
  "name": "getMaxMinReport",
  "displayName": "Get Max-Min report.",
  "description": "This command returns the max, min and average temperature from the specified time to the current time.",
  "request": {
    "name": "since",
    "displayName": "Since",
    "description": "Period to return the max-min report.",
    "schema": "dateTime"
  },
  "response": {
    "name" : "tempReport",
    "displayName": "Temperature Report",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "maxTemp",
          "displayName": "Max temperature",
          "schema": "double"
        },
        {
          "name": "minTemp",
          "displayName": "Min temperature",
          "schema": "double"
        },
        {
          "name" : "avgTemp",
          "displayName": "Average Temperature",
          "schema": "double"
        },
        {
          "name" : "startTime",
          "displayName": "Start Time",
          "schema": "dateTime"
        },
        {
          "name" : "endTime",
          "displayName": "End Time",
          "schema": "dateTime"
        }
      ]
    }
  }
}
```

> [!TIP]
> Du kan exportera en enhets modell från sidan enhets mal len.

Du kan relatera den här kommando definitionen till skärm bilden för användar gränssnittet med hjälp av följande fält:

* `@type` Ange typ av funktion: `Command`
* `name` för Command-värdet.

Valfria fält, till exempel visnings namn och beskrivning, gör att du kan lägga till mer information i gränssnittet och funktionerna.

## <a name="standard-commands"></a>Standard kommandon

I det här avsnittet visas hur en enhet skickar ett svars värde så fort det tar emot kommandot.

Följande kodfragment visar hur en enhet kan svara på ett kommando som omedelbart skickar en lyckad kod:

> [!NOTE]
> Den här artikeln använder Node.js för enkelhetens skull. Andra språk exempel finns i själv studie kursen [skapa och ansluta ett klient program till Azure IoT Central Application](tutorial-connect-device.md) .

```javascript
client.onDeviceMethod('getMaxMinReport', commandHandler);

// ...

const commandHandler = async (request, response) => {
  switch (request.methodName) {
  case 'getMaxMinReport': {
    console.log('MaxMinReport ' + request.payload);
    await sendCommandResponse(request, response, 200, deviceTemperatureSensor.getMaxMinReportObject());
    break;
  }
  default:
    await sendCommandResponse(request, response, 404, 'unknown method');
    break;
  }
};

const sendCommandResponse = async (request, response, status, payload) => {
  try {
    await response.send(status, payload);
    console.log('Response to method \'' + request.methodName +
              '\' sent successfully.' );
  } catch (err) {
    console.error('An error ocurred when sending a method response:\n' +
              err.toString());
  }
};
```

Anropet för att `onDeviceMethod` ställa in `commandHandler` metoden. Denna kommando hanterare:

1. Kontrollerar kommandots namn.
1. För `getMaxMinReport` kommandot anropar det för `getMaxMinReportObject` att hämta värdena som ska tas med i returvärdet.
1. Anropar `sendCommandResponse` för att skicka tillbaka svaret till IoT Central. Det här svaret innehåller `200` svars koden för att indikera att det lyckades.

Följande skärm bild visar hur slutfört kommando svar visas i IoT Central gränssnittet:

:::image type="content" source="media/howto-use-commands/simple-command-ui.png" alt-text="Skärm bild som visar hur du visar en kommando nytto last för ett standard kommando":::

## <a name="long-running-commands"></a>Tidskrävande kommandon

I det här avsnittet visas hur en enhet kan försena sändning av en bekräftelse på att kommandot har konkurrerat.

Följande kodfragment visar hur en enhet kan implementera ett långvarigt kommando:

> [!NOTE]
> Den här artikeln använder Node.js för enkelhetens skull. Andra språk exempel finns i själv studie kursen [skapa och ansluta ett klient program till Azure IoT Central Application](tutorial-connect-device.md) .

```javascript
client.onDeviceMethod('rundiagnostics', commandHandler);

// ...

const commandHandler = async (request, response) => {
  switch (request.methodName) {
  case 'rundiagnostics': {
    console.log('Starting long-running diagnostics run ' + request.payload);
    await sendCommandResponse(request, response, 202, 'Diagnostics run started');

    // Long-running operation here
    // ...

    const patch = {
      rundiagnostics: {
        value: 'Diagnostics run complete at ' + new Date().toLocaleString()
      }
    };

    deviceTwin.properties.reported.update(patch, function (err) {
      if (err) throw err;
      console.log('Properties have been reported for component');
    });
    break;
  }
  default:
    await sendCommandResponse(request, response, 404, 'unknown method');
    break;
  }
};
```

Anropet för att `onDeviceMethod` ställa in `commandHandler` metoden. Denna kommando hanterare:

1. Kontrollerar kommandots namn.
1. Anropar `sendCommandResponse` för att skicka tillbaka svaret till IoT Central. Det här svaret innehåller `202` svars koden för att indikera väntande resultat.
1. Slutför den tids krävande åtgärden.
1. Använder en rapporterad egenskap med samma namn som kommandot för att se IoT Central att kommandot har slutförts.

Följande skärm bild visar hur kommando svaret visas i IoT Central användar gränssnittet när det får 202-svars koden:

:::image type="content" source="media/howto-use-commands/long-running-start.png" alt-text="Skärm bild som visar omedelbart svar från enheten":::

Följande skärm bild visar IoT Central gränssnittet när den tar emot egenskaps uppdateringen som anger att kommandot är slutfört:

:::image type="content" source="media/howto-use-commands/long-running-finish.png" alt-text="Skärm bild som visar långvarigt kommando klart":::

## <a name="offline-commands"></a>Offline-kommandon

I det här avsnittet visas hur en enhet hanterar ett offline-kommando. Om en enhet är online kan den hantera offline-kommandot så snart det tas emot. Om en enhet är offline hanterar den offline-kommandot när nästa ansluter till IoT Central. Enheter kan inte skicka ett retur värde som svar på ett offline-kommando.

Följande kodfragment visar hur en enhet kan implementera ett offline-kommando:

> [!NOTE]
> Den här artikeln använder Node.js för enkelhetens skull. Andra språk exempel finns i själv studie kursen [skapa och ansluta ett klient program till Azure IoT Central Application](tutorial-connect-device.md) .

Följande skärm bild visar ett offline-kommando som kallas **GenerateDiagnostics**. Parametern request är ett objekt med egenskapen datetime som kallas **StartTime** och en heltals uppräknings egenskap som heter **Bank**:

:::image type="content" source="media/howto-use-commands/offline-command.png" alt-text="Skärm bild som visar användar gränssnittet för ett offline-kommando":::

Följande kodfragment visar hur en klient kan lyssna efter offline-kommandon och visa meddelandets innehåll:

```javascript
client.on('message', function (msg) {
  console.log('Body: ' + msg.data);
  console.log('Properties: ' + JSON.stringify(msg.properties));
  client.complete(msg, function (err) {
    if (err) {
      console.error('complete error: ' + err.toString());
    } else {
      console.log('complete sent');
    }
  });
});
```

Utdata från föregående kodfragment visar nytto lasten med **StartTime** -och **Bank** värden. Egenskaps listan innehåller kommando namnet i list objekt med **metod namn** :

```output
Body: {"StartTime":"2021-01-06T06:00:00.000Z","Bank":2}
Properties: {"propertyList":[{"key":"iothub-ack","value":"none"},{"key":"method-name","value":"GenerateDiagnostics"}]}
```

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder kommandon i ditt Azure IoT Central-program, se [nytto laster](concepts-telemetry-properties-commands.md) för att lära dig mer om kommando parametrar och [skapa och ansluta ett klient program till ditt Azure IoT Central-program](tutorial-connect-device.md) för att se fullständiga kod exempel på olika språk.
