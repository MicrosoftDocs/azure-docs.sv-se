---
title: Använd egenskaper i en Azure IoT Central-lösning
description: Lär dig hur du använder skrivskyddade och skrivbara egenskaper i en Azure IoT Central-lösning.
author: dominicbetts
ms.author: dobett
ms.date: 11/06/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 36329987e510372ff286a10584a115ea259afc60
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98119092"
---
# <a name="use-properties-in-an-azure-iot-central-solution"></a>Använd egenskaper i en Azure IoT Central-lösning

Den här instruktions guiden visar hur du, som enhets utvecklare, använder enhets egenskaper som definieras i en enhets mall i ditt Azure IoT Central-program.

Egenskaperna representerar tidpunkts värden. En enhet kan till exempel använda en egenskap för att rapportera mål temperaturen som den försöker nå. Som standard är enhets egenskaperna skrivskyddade i IoT Central. Med skrivbara egenskaper kan du synkronisera tillstånd mellan enheten och ditt Azure IoT Central-program.

Du kan också definiera moln egenskaper i ett Azure IoT Central-program. Moln egenskaps värden utbyts aldrig med en enhet och omfattas inte av omfånget för den här artikeln.

## <a name="define-your-properties"></a>Definiera dina egenskaper

Egenskaper är data fält som representerar enhetens tillstånd. Använd egenskaper för att representera enhetens varaktiga tillstånd, t. ex. på-/av-tillstånd för en enhet. Egenskaper kan också representera grundläggande enhets egenskaper, till exempel program varu versionen av enheten. Du deklarerar egenskaper som skrivskyddade eller skrivbara.

Följande skärm bild visar en egenskaps definition i ett Azure IoT Central-program.

:::image type="content" source="media/howto-use-properties/property-definition.png" alt-text="Skärm bild som visar en egenskaps definition i ett Azure IoT Central-program.":::

I följande tabell visas konfigurations inställningarna för en egenskaps funktion.

| Fält           | Beskrivning                                                                                                                                                                                                                        |
|-----------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Visningsnamn    | Visnings namnet för egenskap svärdet som används på instrument paneler och formulär.                                                                                                                                                              |
| Name            | Egenskapens namn. Azure IoT Central genererar ett värde för det här fältet från visnings namnet, men du kan välja ett eget värde om det behövs. Det här fältet måste vara alfanumeriskt.  Enhets koden använder detta **namn** värde.           |
| Funktions typ | Immaterialrätt.                                                                                                                                                                                                                          |
| Semantiktyp   | Den semantiska typen för egenskapen, till exempel temperatur, tillstånd eller händelse. Valet av semantisk typ avgör vilka av följande fält som är tillgängliga.                                                                       |
| Schema          | Egenskaps data typen, t. ex. Double, String eller Vector. De tillgängliga alternativen bestäms av semantisk typ. Schemat är inte tillgängligt för semantiska typer av händelse och tillstånd.                                               |
| Skrivbar       | Om egenskapen inte är skrivbar kan enheten rapportera egenskaps värden till Azure IoT Central. Om egenskapen är skrivbar kan enheten rapportera egenskaps värden till Azure IoT Central. Sedan kan Azure IoT Central skicka egenskaps uppdateringar till enheten. |
| Allvarlighetsgrad        | Endast tillgängligt för den semantiska händelse typen. Allvarlighets graderna är **fel**, **information** eller **Varning**.                                                                                                                         |
| Tillstånds värden    | Endast tillgängligt för semantisk typ av tillstånd. Definiera möjliga tillstånds värden, som var och en har visnings namn, namn, uppräknings typ och värde.                                                                                   |
| Enhet            | En enhet för egenskap svärdet, till exempel **mph**, **%** eller **&deg; C**.                                                                                                                                                              |
| Visa enhet    | En visnings enhet för användning på instrument paneler och formulär.                                                                                                                                                                                    |
| Kommentar         | Kommentarer om egenskaps funktionen.                                                                                                                                                                                        |
| Beskrivning     | En beskrivning av egenskaps funktionen.                                                                                                                                                                                          |

Egenskaperna kan också definieras i ett gränssnitt i en enhets mall som visas här:

``` json
{
  "@type": [
    "Property",
    "Temperature"
  ],
  "name": "targetTemperature",
  "schema": "double",
  "displayName": "Target Temperature",
  "description": "Allows to remotely specify the desired target temperature.",
  "unit" : "degreeCelsius",
  "writable": true
},
{
  "@type": [
    "Property",
    "Temperature"
  ],
  "name": "maxTempSinceLastReboot",
  "schema": "double",
  "unit" : "degreeCelsius",
  "displayName": "Max temperature since last reboot.",
  "description": "Returns the max temperature since last device reboot."
}
```

I det här exemplet visas två egenskaper. Dessa egenskaper relaterar till egenskaps definitionen i användar gränssnittet:

* `@type` anger typen av funktion: `Property` . I föregående exempel visas även den semantiska typen `Temperature` för båda egenskaperna.
* `name` för egenskapen.
* `schema` anger egenskapens datatyp. Det här värdet kan vara en primitiv typ, till exempel Double, Integer, Boolean eller String. Komplexa objekt typer och Maps stöds också.
* `writable` Som standard är egenskaperna skrivskyddade. Du kan markera en egenskap som skrivbar genom att använda det här fältet.

Valfria fält, till exempel visnings namn och beskrivning, gör att du kan lägga till mer information i gränssnittet och funktionerna.

När du skapar en egenskap kan du ange komplexa schema typer som **objekt** och **Enum**.

![Skärm bild som visar hur du lägger till en funktion.](./media/howto-use-properties/property.png)

När du väljer det komplexa **schemat**, till exempel **objekt**, måste du definiera objektet.

:::image type="content" source="media/howto-use-properties/object.png" alt-text="Skärm bild som visar hur du definierar ett objekt":::

Följande kod visar definitionen av en objekt egenskaps typ. Det här objektet har två fält med typerna sträng och heltal.

``` json
{
  "@type": "Property",
  "displayName": {
    "en": "ObjectProperty"
  },
  "name": "ObjectProperty",
  "schema": {
    "@type": "Object",
    "displayName": {
      "en": "Object"
    },
    "fields": [
      {
        "displayName": {
          "en": "Field1"
        },
        "name": "Field1",
        "schema": "integer"
      },
      {
        "displayName": {
          "en": "Field2"
        },
        "name": "Field2",
        "schema": "string"
      }
    ]
  }
}
```

## <a name="implement-read-only-properties"></a>Implementera skrivskyddade egenskaper

Som standard är egenskaperna skrivskyddade. Med skrivskyddade egenskaper kan du uppdatera egenskaps värden för enhets rapporter till ditt Azure IoT Central-program. Ditt Azure IoT Central-program kan inte ange värdet för en skrivskyddad egenskap.

Azure IoT Central använder enheten för att synkronisera egenskaps värden mellan enheten och Azure IoT Central-programmet. Enhetens egenskaps värden använder enhetens dubbla rapporterade egenskaper. Mer information finns i [enhets uppflätade](../../iot-hub/tutorial-device-twins.md).

Följande kodfragment från en enhets modell visar definitionen av en skrivskyddad egenskaps typ:

``` json
{
  "name": "model",
  "displayName": "Device model",
  "schema": "string",
  "comment": "Device model name or ID. Ex. Surface Book 2."
}
```

Egenskaps uppdateringar skickas av en enhet som en JSON-nyttolast. Mer information finns i [nytto laster](./concepts-telemetry-properties-commands.md).

Du kan använda Azure IoT-enhetens SDK för att skicka en egenskaps uppdatering till ditt Azure IoT Central-program.

Enhetens dubbla egenskaper kan skickas till ditt Azure IoT Central-program med hjälp av följande funktion:

``` javascript
hubClient.getTwin((err, twin) => {
    const properties = {
        model: 'environmentalSensor1.0'
    };
    twin.properties.reported.update(properties, (err) => {
        console.log(err ? `error: ${err.toString()}` : `status: success` )
    });
});
```

Den här artikeln använder Node.js för enkelhetens skull. Andra språk exempel finns i själv studie kursen [skapa och ansluta ett klient program till Azure IoT Central Application](tutorial-connect-device.md) .

I följande vy i Azure IoT Central-programmet visas de egenskaper som du kan se. I vyn blir **enhets modell** egenskapen till en _skrivskyddad enhets egenskap_.

:::image type="content" source="media/howto-use-properties/read-only.png" alt-text="Skärm bild som visar en skrivskyddad egenskaps visning":::

## <a name="implement-writable-properties"></a>Implementera skrivbara egenskaper

Skrivbara egenskaper anges av en operator i Azure IoT Central-programmet i ett formulär. Azure IoT Central skickar egenskapen till enheten. Azure IoT Central förväntar sig en bekräftelse från enheten.

Följande kodfragment från en enhets modell visar definitionen av en skrivbar egenskaps typ:

``` json
{
  "@type": "Property",
  "displayName": "Brightness Level",
  "description": "The brightness level for the light on the device. Can be specified as 1 (high), 2 (medium), 3 (low)",
  "name": "brightness",
  "writable": true,
  "schema": "long"
}
```

Om du vill definiera och hantera de skrivbara egenskaper som enheten svarar på kan du använda följande kod:

``` javascript
hubClient.getTwin((err, twin) => {
    twin.on('properties.desired.brightness', function(desiredBrightness) {
        console.log( `Received setting: ${desiredBrightness.value}` );
        var patch = {
            brightness: {
                value: desiredBrightness.value,
                ad: 'success',
                ac: 200,
                av: desiredBrightness.$version
            }
        }
        twin.properties.reported.update(patch, (err) => {
            console.log(err ? `error: ${err.toString()}` : `status: success` )
        });
    });
});
```

Svars meddelandet ska innehålla `ac` fälten och `av` . Fältet `ad` är valfritt. Se följande kodfragment för exempel:

* `ac` är ett numeriskt fält som använder värdena i följande tabell.
* `av` är versions numret som skickas till enheten.
* `ad` är en beskrivning av alternativ strängen.

| Värde | Etikett | Beskrivning |
| ----- | ----- | ----------- |
| `'ac': 200` | Slutförd | Egenskaps ändrings åtgärden har slutförts. |
| `'ac': 202` eller `'ac': 201` | Väntar | Egenskaps ändrings åtgärden väntar eller pågår. |
| `'ac': 4xx` | Fel | Den begärda egenskaps ändringen var inte giltig eller innehöll ett fel. |
| `'ac': 5xx` | Fel | Ett oväntat fel uppstod i enheten vid bearbetning av den begärda ändringen. |

Mer information om enheter finns i [Konfigurera dina enheter från en backend-tjänst](../../iot-hub/tutorial-device-twins.md).

När operatorn ställer in en skrivbar egenskap i Azure IoT Central-programmet använder programmet en enhet med dubbla önskade egenskaper för att skicka värdet till enheten. Enheten svarar sedan med en enhets dubbla rapporterad egenskap. När Azure IoT Central tar emot det rapporterade egenskap svärdet uppdaterar det egenskaps läget med statusen **accepterad**.

I följande vy visas skrivbara egenskaper. När du anger värdet och väljer **Spara**, är den ursprungliga statusen **väntande**. När enheten accepterar ändringen ändras statusen till **accepterad**.

![Skärm bild som visar väntande status.](./media/howto-use-properties/status-pending.png)

![Skärm bild som visar en accepterad egenskap.](./media/howto-use-properties/accepted.png)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder egenskaper i ditt Azure IoT Central-program, se:

* [Nytto laster](concepts-telemetry-properties-commands.md)
* [Skapa och ansluta ett klient program till ditt Azure IoT Central-program](tutorial-connect-device.md)