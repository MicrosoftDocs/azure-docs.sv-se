---
title: Vad är Device templates i Azure IoT Central | Microsoft Docs
description: Med Azure IoT Central Device templates kan du ange beteendet för de enheter som är anslutna till ditt program. En enhets mall anger telemetri, egenskaper och kommandon som enheten måste implementera. En enhets mall definierar också användar gränssnittet för enheten i IoT Central, till exempel formulär och instrument paneler som en operatör använder.
author: dominicbetts
ms.author: dobett
ms.date: 12/19/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: 04c2330ffee396f5fc30b85640e992df77c08263
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/28/2020
ms.locfileid: "97795436"
---
# <a name="what-are-device-templates"></a>Vad är enhetsmallar?

_Den här artikeln gäller enhets utvecklare och Solution Builder._

En enhets mall i Azure IoT Central är en skiss som definierar egenskaper och beteenden för en typ av enhet som ansluter till ditt program. Till exempel definierar enhets mal len den telemetri som en enhet skickar så att IoT Central kan skapa visualiseringar som använder rätt enheter och data typer.

En Solution Builder lägger till enhets mallar i ett IoT Central-program. En enhets utvecklare skriver enhets koden som implementerar de beteenden som definierats i enhets mal len.

En enhets mall innehåller följande avsnitt:

- _En enhets modell_. Den här delen av enhets mal len definierar hur enheten interagerar med ditt program. En enhets utvecklare implementerar beteenden som definierats i modellen.
    - _Standard komponent_. Varje enhets modell har en standard komponent. Standard komponentens gränssnitt beskriver funktioner som är speciella för enhets modellen.
    - _Komponenter_. En enhets modell kan innehålla komponenter förutom standard komponenten för att beskriva enhets funktioner. Varje komponent har ett gränssnitt som beskriver komponentens funktioner. Komponent gränssnitt kan återanvändas i andra enhets modeller. Till exempel kan flera telefon enhets modeller använda samma kamera gränssnitt.
    - _Ärvda gränssnitt_. En enhets modell innehåller ett eller flera gränssnitt som utökar funktionerna i standard komponenten.
- _Moln egenskaper_. I den här delen av enhets mal len kan utvecklare av lösningar ange alla enhets-metadata som ska lagras. Moln egenskaper synkroniseras aldrig med enheter och finns bara i programmet. Moln egenskaper påverkar inte den kod som en enhets utvecklare skriver för att implementera enhets modellen.
- _Anpassningar_. Den här delen av enhets mal len gör att lösningen-utvecklare kan åsidosätta några av definitionerna i enhets modellen. Anpassningar är användbara om lösnings utvecklaren vill förfina hur programmet hanterar ett värde, t. ex. ändra visnings namnet för en egenskap eller färgen som används för att visa ett telemetri-värde. Anpassningar påverkar inte den kod som en enhets utvecklare skriver för att implementera enhets modellen.
- _Vyer_. Den här delen av enhets mal len gör att lösnings utvecklaren kan definiera visualiseringar för att visa data från enheten och formulär för att hantera och kontrol lera en enhet. I vyerna används enhets modell, moln egenskaper och anpassningar. Vyer påverkar inte den kod som en enhets utvecklare skriver för att implementera enhets modellen.

## <a name="device-models"></a>Enhetsmodeller

En enhets modell definierar hur en enhet samverkar med ditt IoT Central-program. Enhets utvecklaren måste se till att enheten implementerar de beteenden som definierats i enhets modellen så att IoT Central kan övervaka och hantera enheten. En enhets modell består av ett eller flera _gränssnitt_, och varje gränssnitt kan definiera en samling typer av _telemetri_ , _enhets egenskaper_ och _kommandon_. En lösnings utvecklare kan importera en JSON-fil som definierar enhets modellen i en enhets mall eller använda webb gränssnittet i IoT Central för att skapa eller redigera en enhets modell. Ändringar i en enhets modell som skapats med webb gränssnittet kräver att [enhets mal len har versions hantering](./howto-version-device-template.md).

En lösnings utvecklare kan också exportera en JSON-fil som innehåller enhets modellen. En enhets utvecklare kan använda det här JSON-dokumentet för att förstå hur enheten ska kommunicera med IoT Central-programmet.

JSON-filen som definierar enhets modellen använder den [digitala DTDL (Digital Definition Language) v2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md). IoT Central förväntar sig att JSON-filen ska innehålla enhets modellen med de gränssnitt som definierats i stället för separata filer.

En typisk IoT-enhet består av:

- Anpassade delar, vilka är de saker som gör din enhet unik.
- Standard delar, som är saker som är gemensamma för alla enheter.

Dessa delar kallas _gränssnitt_ i en enhets modell. Gränssnitt definierar information om varje del din enhet implementerar. Gränssnitt är återanvändbara i enhets modeller. I DTDL refererar en komponent till ett gränssnitt som definierats i en separat DTDL-fil.

I följande exempel visas en översikt över enhets modellen för en temperatur styrenhets enhet. Standard komponenten inkluderar definitioner för `workingSet` , `serialNumber` och `reboot` . Enhets modellen omfattar även `thermostat` `deviceInformation` gränssnitten och:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "description": "Device with two thermostats and remote reboot.",
  "contents": [
    {
      "@type": [
        "Telemetry", "DataSize"
      ],
      "name": "workingSet",
      "displayName": "Working Set",
      "description": "Current working set of the device memory in KiB.",
      "schema": "double",
      "unit" : "kibibyte"
    },
    {
      "@type": "Property",
      "name": "serialNumber",
      "displayName": "Serial Number",
      "description": "Serial number of the device.",
      "schema": "string"
    },
    {
      "@type": "Command",
      "name": "reboot",
      "displayName": "Reboot",
      "description": "Reboots the device after waiting the number of seconds specified.",
      "request": {
        "name": "delay",
        "displayName": "Delay",
        "description": "Number of seconds to wait before rebooting the device.",
        "schema": "integer"
      }
    },
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat",
      "displayName": "Thermostat",
      "description": "Thermostat One."
    },
    {
      "@type": "Component",
      "schema": "dtmi:azure:DeviceManagement:DeviceInformation;1",
      "name": "deviceInformation",
      "displayName": "Device Information interface",
      "description": "Optional interface with basic device hardware information."
    }
  ]
}
```

Ett gränssnitt har vissa obligatoriska fält:

- `@id`: ett unikt ID i form av ett enkelt enhetligt resurs namn.
- `@type`: deklarerar att objektet är ett gränssnitt.
- `@context`: anger den DTDL-version som används för gränssnittet.
- `contents`: visar de egenskaper, telemetri och kommandon som utgör din enhet. Funktionerna kan definieras i flera gränssnitt.

Det finns några valfria fält som du kan använda för att lägga till mer information i kapacitets modellen, till exempel visnings namn och beskrivning.

Varje post i listan över gränssnitt i avsnittet implementeringar har en:

- `name`: gränssnittets programmerings namn.
- `schema`: gränssnittets funktions modell implementeras.

## <a name="interfaces"></a>Gränssnitt

Med DTDL kan du beskriva enhetens funktioner. Relaterade funktioner grupperas i gränssnitt. Gränssnitt beskriver egenskaper, telemetri och kommandon som en del av din enhet implementerar:

- `Properties`. Egenskaper är data fält som representerar enhetens tillstånd. Använd egenskaper för att representera enhetens varaktiga tillstånd, till exempel vid inaktive ring av en kyl pump. Egenskaper kan också representera grundläggande enhets egenskaper, till exempel enhetens inbyggda program vara. Du kan deklarera egenskaper som skrivskyddade eller skrivbara. Endast enheter kan uppdatera värdet för en skrivskyddad egenskap. En operatör kan ange värdet för en skrivbar egenskap som ska skickas till en enhet.
- `Telemetry`. Telemetridata representerar mått från sensorer. När din enhet tar ett sensor mått bör den skicka en telemetri-händelse som innehåller sensor data.
- `Commands`. Kommandon representerar metoder som användarna av enheten kan köra på enheten. Till exempel ett återställnings kommando eller ett kommando för att aktivera eller inaktivera en fläkt.

I följande exempel visas definitionen av termostat-gränssnittet:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "Temperature"
      ],
      "name": "temperature",
      "displayName" : "Temperature",
      "description" : "Temperature in degrees Celsius.",
      "schema": "double",
      "unit": "degreeCelsius"
    },
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
    },
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
  ]
}
```

I det här exemplet visas två egenskaper (en skrivskyddad och en skrivbar), en typ av telemetri och ett kommando. En minimal fält Beskrivning har:

- `@type` för att ange typen av funktion: `Telemetry` , `Property` , eller `Command` .  I vissa fall innehåller typen en semantisk typ som gör det möjligt för IoT Central att göra vissa antaganden om hur värdet ska hanteras.
- `name` för telemetri-värdet.
- `schema` för att ange data typen för Telemetrin eller egenskapen. Det här värdet kan vara en primitiv typ, till exempel Double, Integer, Boolean eller String. Komplexa objekt typer och Maps stöds också.

Valfria fält, till exempel visnings namn och beskrivning, gör att du kan lägga till mer information i gränssnittet och funktionerna.

## <a name="properties"></a>Egenskaper

Som standard är egenskaperna skrivskyddade. Skrivskyddade egenskaper innebär att enhets rapportens egenskaps värde uppdateras till ditt IoT Central-program. Ditt IoT Central program kan inte ange värdet för en skrivskyddad egenskap.

Du kan också markera en egenskap som skrivbar i ett gränssnitt. En enhet kan ta emot en uppdatering av en skrivbar egenskap från ditt IoT Central-program samt att rapportera egenskaps värde uppdateringar till ditt program.

Enheter behöver inte vara anslutna för att ange egenskaps värden. De uppdaterade värdena överförs när enheten sedan ansluter till programmet. Det här beteendet gäller både skrivskyddade och skrivbara egenskaper.

Använd inte egenskaper för att skicka telemetri från enheten. En ReadOnly-egenskap, till exempel, `temperatureSetting=80` ska betyda att enhetens temperatur har angetts till 80 och att enheten försöker komma till eller stanna vid den här temperaturen.

För skrivbara egenskaper, returnerar enhets programmet en önskad status kod, version och beskrivning för att indikera om den har tagit emot och applicerat egenskap svärdet.

## <a name="telemetry"></a>Telemetri

Med IoT Central kan du Visa telemetri om instrument paneler och diagram och använda regler för att utlösa åtgärder när tröskelvärden uppnås. IoT Central använder informationen i enhets modellen, t. ex. data typer, enheter och visnings namn, för att avgöra hur telemetridata ska visas.

Du kan använda funktionen IoT Central data export för att strömma telemetri till andra destinationer, till exempel lagring eller Event Hubs.

## <a name="commands"></a>Kommandon

Ett kommando måste köras inom 30 sekunder som standard och enheten måste vara ansluten när kommandot anländer. Om enheten svarar i tid, eller om enheten inte är ansluten, Miss lyckas kommandot.

Kommandon kan ha parametrar för begäran och returnera ett svar.

### <a name="offline-commands"></a>Offline-kommandon

Du kan välja köa kommandon om en enhet för närvarande är offline genom att aktivera **kön om alternativet offline** för ett kommando i enhets mal len.

Offline-kommandon är enkelriktade meddelanden till enheten från din lösning. Offline-kommandon kan ha parametrar för begäran men returnerar inte något svar.

> [!NOTE]
> Det här alternativet är endast tillgängligt i IoT Central webb gränssnitt. Den här inställningen tas inte med om du exporterar en modell eller ett gränssnitt från enhets mal len.

## <a name="cloud-properties"></a>Molnegenskaper

Moln egenskaper tillhör enhets mal len, men är inte en del av enhets modellen. Moln egenskaper gör att lösnings utvecklaren anger alla metadata för enheten som ska lagras i IoT Central-programmet. Moln egenskaper påverkar inte den kod som en enhets utvecklare skriver för att implementera enhets modellen.

En lösnings utvecklare kan lägga till moln egenskaper till instrument paneler och vyer, tillsammans med enhets egenskaper för att göra det möjligt för en operatör att hantera de enheter som är anslutna till programmet. En lösnings utvecklare kan också använda moln egenskaper som en del av en regel definition för att göra ett tröskel värde redigerbart av en operator.

## <a name="customizations"></a>Anpassningar

Anpassningar ingår i enhets mal len, men är inte en del av enhets modellen. Anpassningar låter Solution Developer förbättra eller åsidosätta några av definitionerna i enhets modellen. En lösnings utvecklare kan till exempel ändra visnings namnet för en typ av telemetri eller egenskap. En lösnings utvecklare kan också använda anpassningar för att lägga till verifiering, till exempel en minimal eller maximal längd för en sträng enhets egenskap.

Anpassningar kan påverka koden som en enhets utvecklare skriver för att implementera enhets modellen. En anpassning kan till exempel ange lägsta och högsta sträng längd eller lägsta och högsta numeriska värden för telemetri.

## <a name="views"></a>Vyer

En lösnings utvecklare skapar vyer som låter operatörer övervaka och hantera anslutna enheter. Vyer är en del av enhets mal len, så en vy är kopplad till en speciell enhets typ. En vy kan innehålla:

- Diagram för att rita telemetri.
- Paneler för att visa skrivskyddade enhets egenskaper.
- Paneler så att operatören kan redigera skrivbara enhets egenskaper.
- Paneler så att operatören kan redigera moln egenskaper.
- Paneler där operatören anropar kommandon, inklusive kommandon som förväntar sig en nytto Last.
- Paneler för att visa etiketter, bilder eller markdown text.

Telemetri, egenskaper och kommandon som du kan lägga till i en vy bestäms av enhets modellen, moln egenskaperna och anpassningarna i enhets mal len.

## <a name="next-steps"></a>Nästa steg

Som enhets utvecklare är det nu viktigt att du har lärt dig om enhets mallar, ett förslag till nästa steg är att läsa för [telemetri, egenskaper och kommando nytto laster](./concepts-telemetry-properties-commands.md) för att lära dig mer om de data som en enhet utbyter med IoT Central.

Som en lösnings utvecklare är ett förslag till nästa steg att läsa [definiera en ny IoT-enhets typ i ditt Azure IoT Central-program](./howto-set-up-template.md) för att lära dig mer om hur du skapar en enhets mall.
