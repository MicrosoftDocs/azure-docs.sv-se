---
title: Så här hanterar du IoT Plug and Play digital twins
description: Hantera IoT-Plug and Play med hjälp av DIGITAL Twin-API:er
author: prashmo
ms.author: prashmo
ms.date: 12/17/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: e68003878dc0e9275461100a59e0f45486c2978f
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739880"
---
# <a name="manage-iot-plug-and-play-digital-twins"></a>Hantera IoT Plug and Play digitala tvillingar

IoT Plug and Play stöd för **get digital twin och** Update digital **twin** operations för att hantera digitala tvillingar. Du kan använda [antingen REST-API:erna](/rest/api/iothub/service/digitaltwin) eller något av [tjänst-API:erna](libraries-sdks.md).

När detta skrivs är den digitala tvilling-API-versionen `2020-09-30` .

## <a name="update-a-digital-twin"></a>Uppdatera en digital tvilling

En IoT Plug and Play enhet implementerar en modell som [beskrivs av Digital Twins Definition Language v2 (DTDL).](https://github.com/Azure/opendigitaltwins-dtdl) Lösningsutvecklare kan använda **UPDATE Digital Twin API för** att uppdatera tillståndet för komponenten och egenskaperna för den digitala tvillingen.

IoT-Plug and Play som ett exempel i den här artikeln implementerar temperaturkontrollmodellen [med](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) [termostatkomponenter.](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json)

Följande kodfragment visar svaret på en **Get digital twin-begäran** formaterad som ett JSON-objekt. Mer information om digital twin-formatet finns i [Förstå IoT-Plug and Play digital twins:](./concepts-digital-twin.md#digital-twin-example)

```json
{
    "$dtId": "sample-device",
    "serialNumber": "alwinexlepaho8329",
    "thermostat1": {
        "maxTempSinceLastReboot": 25.3,
        "targetTemperature": 20.4,
        "$metadata": {
            "targetTemperature": {
                "desiredValue": 20.4,
                "desiredVersion": 4,
                "ackVersion": 4,
                "ackCode": 200,
                "ackDescription": "Successfully executed patch",
                "lastUpdateTime": "2020-07-17T06:11:04.9309159Z"
            },
            "maxTempSinceLastReboot": {
                "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
            }
        }
    },
    "$metadata": {
        "$model": "dtmi:com:example:TemperatureController;1",
        "serialNumber": {
            "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
        }
    }
}
```

Med digitala tvillingar kan du uppdatera en hel komponent eller egenskap med hjälp av [en JSON-korrigering.](http://jsonpatch.com/)

Du kan till exempel uppdatera egenskapen `targetTemperature` på följande sätt:

```json
[
    {
        "op": "add",
        "path": "/thermostat1/targetTemperature",
        "value": 21.4
    }
]
```

Föregående uppdatering anger önskat värde för en egenskap på motsvarande komponentnivå enligt följande `$metadata` kodfragment. IoT Hub uppdaterar önskad version av egenskapen:

```json
"thermostat1": {
    "targetTemperature": 20.4,
    "$metadata": {
        "targetTemperature": {
            "desiredValue": 21.4,
            "desiredVersion": 5,
            "ackVersion": 4,
            "ackCode": 200,
            "ackDescription": "Successfully executed patch",
            "lastUpdateTime": "2020-07-17T06:11:04.9309159Z"
        }
    }
}
```

### <a name="add-replace-or-remove-a-component"></a>Lägga till, ersätta eller ta bort en komponent

Åtgärder på komponentnivå kräver en tom `$metadata` objektmarkör i värdet.

En åtgärd för att lägga till eller ersätta en komponent anger önskade värden för alla angivna egenskaper. Den rensar också önskade värden för skrivbara egenskaper som inte medföljer uppdateringen.

Om du tar bort en komponent rensas de önskade värdena för alla skrivbara egenskaper som finns. En enhet synkroniserar slutligen borttagningen och slutar rapportera enskilda egenskaper. Komponenten tas sedan bort från den digitala tvillingen.

Följande JSON-korrigeringsexempel visar hur du lägger till, ersätter eller tar bort en komponent:

```json
[
    {
        "op": "add",
        "path": "/thermostat1",
        "value": {
            "targetTemperature": 21.4,
            "anotherWritableProperty": 42,
            "$metadata": {}
        }
    },
    {
        "op": "replace",
        "path": "/thermostat1",
        "value": {
            "targetTemperature": 21.4,
            "$metadata": {}
        }
    },
    {
        "op": "remove",
        "path": "/thermostat2"
    }
]
```

### <a name="add-replace-or-remove-a-property"></a>Lägga till, ersätta eller ta bort en egenskap

En åtgärd för att lägga till eller ersätta anger det önskade värdet för en egenskap. Enheten kan synkronisera tillstånd och rapportera en uppdatering av värdet tillsammans med `ack` en kod, version och beskrivning.

Om du tar bort en egenskap rensas det önskade värdet för egenskapen om den har angetts. Enheten kan sedan sluta rapportera den här egenskapen och den tas bort från komponenten. Om den här egenskapen är den sista i komponenten tas även komponenten bort.

Följande JSON-korrigeringsexempel visar hur du lägger till, ersätter eller tar bort en egenskap i en komponent:

```json
[
    {
        "op": "add",
        "path": "/thermostat1/targetTemperature",
        "value": 21.4
    },
    {
        "op": "replace",
        "path": "/thermostat1/anotherWritableProperty",
        "value": 42
    },
    {
        "op": "remove",
        "path": "/thermostat2/targetTemperature",
    }
]
```

### <a name="rules-for-setting-the-desired-value-of-a-digital-twin-property"></a>Regler för att ange önskat värde för en digital tvillingegenskap

**Namn**

Namnet på en komponent eller egenskap måste vara giltigt DTDL v2-namn.

Tillåtna tecken är a–z, A–Z, 0–9 (inte som första tecken) och understreck (inte som första eller sista tecknet).

Ett namn kan vara mellan 1 och 64 tecken långt.

**Egenskapsvärde**

Värdet måste vara en giltig [DTDL v2-egenskap](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#property).

Alla primitiva typer stöds. Inom komplexa typer stöds uppräkningar, kartor och objekt. Läs mer i [DTDL v2-scheman.](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#schemas)

Egenskaper stöder inte matriser eller komplexa scheman med en matris.

Ett maximalt djup på fem nivåer stöds för ett komplext objekt.

Alla fältnamn i komplexa objekt ska vara giltiga DTDL v2-namn.

Alla kartnycklar ska vara giltiga DTDL v2-namn.

## <a name="troubleshoot-update-digital-twin-api-errors"></a>Felsöka api-fel för uppdatering av Digital Twin

Api:et för digital twin 2006 000 000 000 000 000 000 0

`ErrorCode:ArgumentInvalid;'{propertyName}' exists within the device twin and is not digital twin conformant property. Please refer to aka.ms/dtpatch to update this to be conformant.`

Om du ser det här felet kontrollerar du att uppdateringskorrigeringen följer reglerna [för att ange önskat värde för en digital tvillingegenskap](#rules-for-setting-the-desired-value-of-a-digital-twin-property)

När du uppdaterar en komponent kontrollerar du att det [tomma objektet $metadata har angetts.](#add-replace-or-remove-a-component)

Uppdateringar kan misslyckas om en enhets rapporterade värden inte överensstämmer med [IoT Plug and Play-konventionerna](./concepts-convention.md#writable-properties).

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om digital twins finns här några ytterligare resurser:

- [Interagera med en enhet från din lösning](quickstart-service.md)
- [IoT Digital Twin REST API](/rest/api/iothub/service/digitaltwin)
- [Azure IoT Explorer](howto-use-iot-explorer.md)
