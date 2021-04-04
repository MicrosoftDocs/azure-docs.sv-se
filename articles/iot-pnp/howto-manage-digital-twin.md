---
title: Så här hanterar du IoT Plug and Play digitala dubbla
description: 'Hantera IoT Plug and Play-enheter med hjälp av digitala dubbla API: er'
author: prashmo
ms.author: prashmo
ms.date: 07/20/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: ddb8027c145f6a38bfcd953be66dae2943a20c3a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97654616"
---
# <a name="manage-iot-plug-and-play-digital-twins"></a>Hantera IoT Plug and Play digitala dubbla

IoT Plug and Play har stöd för att **skapa** digitala dubbla och **Uppdatera** digitala dubbla åtgärder för att hantera digitala dubbla. Du kan antingen använda [REST-API: erna](/rest/api/iothub/service/digitaltwin) eller något av [tjänst-SDK](libraries-sdks.md): erna.

Vid tidpunkten för skrivning är den digitala dubbla API-versionen `2020-09-30` .

## <a name="update-a-digital-twin"></a>Uppdatera en digital delad

En IoT Plug and Play-enhet implementerar en modell som beskrivs av [Digitals Definition Language v2 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl). Lösnings utvecklare kan använda uppdaterings tjänsten för att uppdatera det **digitala dubbla API: et** för att uppdatera komponentens tillstånd och egenskaperna för den digitala, dubbla.

IoT Plug and Play-enheten som används som ett exempel i den här artikeln implementerar [temperatur styrenhets modellen](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) med [termostat](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) -komponenter.

I följande kodfragment visas svaret på en **Get Digital** -begäran som är formaterad som ett JSON-objekt. Mer information om det digitala dubbla formatet finns i [förstå IoT plug and Play digitala dubbla](./concepts-digital-twin.md#digital-twin-example):

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

Med digitala dubbla, kan du uppdatera en hel komponent eller egenskap med en [JSON-korrigering](http://jsonpatch.com/).

Du kan till exempel uppdatera egenskapen på `targetTemperature` följande sätt:

```json
[
    {
        "op": "add",
        "path": "/thermostat1/targetTemperature",
        "value": 21.4
    }
]
```

I föregående uppdatering anges det önskade värdet för en egenskap på motsvarande komponent nivå `$metadata` enligt följande kod avsnitt. IoT Hub uppdaterar den önskade versionen av egenskapen:

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

Åtgärder på komponent nivå kräver en tom objekt `$metadata` markör i värdet.

En komponent åtgärd för att lägga till eller ersätta anger önskade värden för alla angivna egenskaper. Det tar också bort önskade värden för skrivbara egenskaper som inte finns med i uppdateringen.

Om du tar bort en komponent rensas de önskade värdena för alla skrivbara egenskaper. En enhet synkroniserar slutligen borttagningen och slutar rapportera de enskilda egenskaperna. Komponenten tas sedan bort från den digitala dubbla.

Följande exempel på JSON-korrigering visar hur du lägger till, ersätter eller tar bort en komponent:

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

En Lägg till eller ersätt-åtgärd anger det önskade värdet för en egenskap. Enheten kan synkronisera tillstånd och rapportera en uppdatering av värdet tillsammans med en `ack` kod, en version och en beskrivning.

Om du tar bort en egenskap raderas önskat egenskaps värde om det har angetts. Enheten kan sedan sluta rapportera den här egenskapen och tas bort från komponenten. Om den här egenskapen är den sista i komponenten, tas komponenten också bort.

Följande exempel på JSON-korrigering visar hur du lägger till, ersätter eller tar bort en egenskap i en komponent:

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

### <a name="rules-for-setting-the-desired-value-of-a-digital-twin-property"></a>Regler för att ställa in önskat värde för en digital, dubbel egenskap

**Namn**

Namnet på en komponent eller egenskap måste vara ett giltigt DTDL v2-namn.

Tillåtna tecken är a-z, A-Z, 0-9 (inte som det första tecknet) och under streck (inte som det första eller sista tecknet).

Ett namn kan vara 1-64 tecken långt.

**Egenskaps värde**

Värdet måste vara en giltig [DTDL v2-egenskap](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#property).

Alla primitiva typer stöds. I komplexa typer stöds uppräkningar, Maps och objekt. Mer information finns i [DTDL v2-scheman](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#schemas).

Egenskaper stöder inte matriser eller ett komplext schema med en matris.

Ett maximalt djup på fem nivåer stöds för ett komplext objekt.

Alla fält namn i komplexa objekt måste vara giltiga DTDL v2-namn.

Alla kart nycklar ska vara giltiga DTDL v2-namn.

## <a name="troubleshoot-update-digital-twin-api-errors"></a>Felsök uppdatering av digitala dubbla API-fel

Det digitala dubbla API: et genererar följande allmänna fel meddelande:

`ErrorCode:ArgumentInvalid;'{propertyName}' exists within the device twin and is not digital twin conformant property. Please refer to aka.ms/dtpatch to update this to be conformant.`

Om det här felet visas kontrollerar du att uppdaterings korrigeringen följer [reglerna för att ange önskat värde för en digital, dubbel egenskap](#rules-for-setting-the-desired-value-of-a-digital-twin-property)

När du uppdaterar en komponent kontrollerar du att den [tomma objekt $metadata markören](#add-replace-or-remove-a-component) har angetts.

Uppdateringar kan inte utföras om en enhets rapporterade värden inte följer [IoT-plug and Play-konventionerna](./concepts-convention.md#writable-properties).

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om digitala dubbla, finns här några ytterligare resurser:

- [Interagera med en enhet från din lösning](quickstart-service.md)
- [IoT Digital, dubbla REST API](/rest/api/iothub/service/digitaltwin)
- [Azure IoT Explorer](howto-use-iot-explorer.md)
