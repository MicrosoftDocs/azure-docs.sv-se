---
title: Använda plats data i en Azure IoT Central-lösning
description: Lär dig hur du använder plats data som skickas från en enhet som är ansluten till ditt IoT Central-program. Rita plats data på en karta eller skapa regler för avgränsnings regler.
author: dominicbetts
ms.author: dobett
ms.date: 01/08/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: c909fd1438488e3625f3674dd26f959cf6fad79f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98128053"
---
# <a name="use-location-data-in-an-azure-iot-central-solution"></a>Använda plats data i en Azure IoT Central-lösning

Den här artikeln visar hur du använder plats data i ett IoT Central-program. En enhet som är ansluten till IoT Central kan skicka plats data som telemetri-Stream eller använda en enhets egenskap för att rapportera plats data.

Ett Solution Builder kan använda plats data för att:

* Rita den rapporterade platsen på en karta.
* Kartlägg plats historiken för telemetri om en karta.
* Skapa regler för avgränsning för att meddela en operatör när en enhet går in eller lämnar ett angivet utrymme.

## <a name="add-location-capabilities-to-a-device-template"></a>Lägga till plats funktioner i en enhets mall

Följande skärm bild visar en enhets mall med exempel på en enhets egenskap och telemetri som använder plats data. Definitionerna använder den semantiska **plats** typen och **schema typen för geolokalisering:**

:::image type="content" source="media/howto-use-location-data/location-device-template.png" alt-text="Skärm bild som visar definition för plats egenskap i enhets mal len":::

För referens ser de [digitala DTDL-definitionerna (Endefinierade definitions språk)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) för dessa funktioner ut som i följande kodfragment:

```json
{
  "@type": [
    "Property",
    "Location"
  ],
  "displayName": {
    "en": "DeviceLocation"
  },
  "name": "DeviceLocation",
  "schema": "geopoint",
  "writable": false
},
{
  "@type": [
    "Telemetry",
    "Location"
  ],
  "displayName": {
    "en": "Tracking"
  },
  "name": "Tracking",
  "schema": "geopoint"
}
```

> [!NOTE]
> Schema **typen för den** här typen är inte en del av [DTDL-specifikationen](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md). IoT Central stöder för närvarande **schema typen för** geolokalisering och den semantiska **plats** typen för bakåtkompatibilitet.

## <a name="send-location-data-from-a-device"></a>Skicka plats data från en enhet

När en enhet skickar data för egenskapen **DeviceLocation** som visas i föregående avsnitt, ser nytto lasten ut som följande JSON-kodfragment:

```json
{
  "DeviceLocation": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

När en enhet skickar data för **spårnings** telemetri som visas i föregående avsnitt, ser nytto lasten ut som följande JSON-kodfragment:

```json
{
  "Tracking": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

## <a name="display-device-location"></a>Visa enhets plats

Du kan visa plats data på flera platser i IoT Central-programmet. Till exempel för vyer som är kopplade till en enskild enhet eller på instrument paneler.

När du skapar en vy för en enhet kan du välja att rita platsen på en karta eller Visa de enskilda värdena:

:::image type="content" source="media/howto-use-location-data/location-views.png" alt-text="Skärm bild som visar exempel visning med plats data":::

Du kan lägga till kart paneler på en instrument panel för att rita platsen för en eller flera enheter. När du lägger till en kart panel för att Visa telemetri för platser kan du rita upp platsen under en tids period. Följande skärm bild visar platsen som rapporteras av en simulerad enhet under de senaste 30 minuterna:

:::image type="content" source="media/howto-use-location-data/location-dashboard.png" alt-text="Skärm bild som visar exempel instrument panel med plats data":::

## <a name="create-a-geofencing-rule"></a>Skapa en regel för avgränsning

Du kan använda telemetri för att skapa en regel för en avgränsning som genererar en avisering när en enhet flyttas in i eller ut ur ett rektangulärt område. Följande skärm bild visar en regel som använder fyra villkor för att definiera ett rektangulärt område med hjälp av latitud-och longitud-värden. Regeln genererar ett e-postmeddelande när enheten flyttas till det rektangulära utrymmet:

:::image type="content" source="media/howto-use-location-data/geofence-rule.png" alt-text="Skärm bild som visar en regel definition för en inhägnad":::

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder egenskaper i ditt Azure IoT Central-program, se:

* [Nytto laster](concepts-telemetry-properties-commands.md)
* [Skapa och ansluta ett klient program till ditt Azure IoT Central-program](tutorial-connect-device.md)