---
title: IoT Plug and Play-brygga | Microsoft Docs
description: Förstå IoT Plug and Play Bridge och hur du använder den för att ansluta befintliga enheter som är anslutna till en Windows-eller Linux-gateway som IoT Plug and Play-enheter.
author: usivagna
ms.author: ugans
ms.date: 1/20/2021
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: a45efd90043ecb4d457db7ed39651f1a9b5bbd4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98890615"
---
# <a name="iot-plug-and-play-bridge"></a>IoT Plug and Play-brygga

IoT Plug and Play Bridge är ett program med öppen källkod för att ansluta befintliga enheter som är anslutna till Windows eller Linux gateway som IoT Plug and Play-enheter. När du har installerat och konfigurerat programmet på din Windows-eller Linux-dator kan du använda det för att ansluta anslutna enheter till en IoT-hubb. Du kan använda bryggan för att mappa IoT Plug and Play-gränssnitt till den telemetri som de anslutna enheterna skickar, arbeta med enhets egenskaper och anropa kommandon.

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-high-level.png" alt-text="Till vänster finns det ett par befintliga sensorer anslutna (både kabelanslutna och trådlösa) till en Windows-eller Linux-dator som innehåller IoT Plug and Play Bridge. IoT Plug and Play Bridge ansluter sedan till en IoT-hubb på höger sida":::

IoT Plug and Play Bridge kan distribueras som en fristående körbar fil på alla IoT-enheter, industriella datorer, servrar eller gateway som kör Windows 10 eller Linux. Den kan också kompileras till din program kod. En enkel konfigurations-JSON-fil talar om IoT Plug and Play Bridge som anslutna enheter/kring utrustning ska exponeras på upp till Azure.

## <a name="supported-protocols-and-sensors"></a>Protokoll och sensorer som stöds

IoT Plug and Play Bridge stöder följande typer av kring utrustning som standard, med länkar till kort dokumentationen:

|Routrar|Windows|Linux|
|---------|---------|---------|
|[Bluetooth-sensor](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/bluetooth_sensor_adapter.md) ansluter identifierade Bluetooth låg energi-aktiverade sensorer.       |Ja|Inga|
|[Kamera styrenheten](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/camera_adapter.md) ansluter kameror på en Windows 10-enhet.               |Ja|Inga|
|[Modbus adapter](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/modbus_adapters.md) ansluter sensorer på en Modbus-enhet.              |Ja|Ja|
|[MQTT adapter](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/mqtt_adapter.md) ansluter enheter som använder en MQTT-Broker.                  |Ja|Ja|
|[SerialPnP adapter](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/serialpnp/Readme.md) ansluter enheter som kommunicerar via en seriell anslutning.               |Ja|Ja|
|[Windows USB-kringutrustning](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/coredevicehealth_adapter.md) använder en lista över enhets gränssnitts klasser som stöds för att ansluta enheter som har ett angivet maskinvaru-ID.  |Ja|Ej tillämpligt|

Information om hur du utökar IoT Plug and Play Bridge för att stödja ytterligare enhets protokoll finns i [utöka IoT plug and Play-bryggan](howto-author-pnp-bridge-adapter.md). Information om hur du skapar och distribuerar IoT Plug and Play Bridge finns i [skapa och distribuera iot plug and Play-bryggan](howto-build-deploy-extend-pnp-bridge.md).

## <a name="iot-plug-and-play-bridge-architecture"></a>Arkitektur för IoT Plug and Play Bridge

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-components.png" alt-text="Till vänster finns det flera rutor som visar olika kring utrustning som är kopplad till en Windows-eller Linux-dator som innehåller IoT Plug and Play Bridge. Överst visas en ruta med namnet konfigurations punkter mot bryggan. Bryggan ansluts sedan till en IoT-hubb på höger sida av diagrammet.":::

### <a name="iot-plug-and-play-bridge-adapters"></a>IoT Plug and Play Bridge-kort

IoT Plug and Play Bridge stöder en uppsättning IoT Plug and Play Bridge-kort för olika typer av enheter. Ett *kort manifest* definierar nätverkskorten i en brygga statiskt.

Bryggans adapter hanterare använder manifestet för att identifiera och anropa adapter-funktioner. Kort hanteraren anropar bara funktionen Create på de brygga kort som krävs av de gränssnitts komponenter som anges i konfigurations filen. En kort instans skapas för varje IoT Plug and Play-komponent.

Ett bro kort skapar och skaffar en digital, dubbel gränssnitts referens. Nätverkskortet använder den här referensen för att binda enhets funktionen till den digitala dubbla.

Med hjälp av informationen i konfigurations filen använder Bridge-kortet av följande tekniker för att möjliggöra fullständig enhet till digital dubbel kommunikation via bryggan:

- Upprättar en kommunikations kanal direkt.
- Skapar en enhets Övervakare som väntar på att en kommunikations kanal ska bli tillgänglig.

### <a name="configuration-file"></a>Konfigurationsfil

IoT Plug and Play-bryggan använder en JSON-baserad konfigurations fil som anger:

- Så här ansluter du till en IoT-hubb eller ett IoT Central program: alternativen inkluderar anslutnings strängar, autentiseringsmetoder eller enhets etablerings tjänst (DPS).
- Platsen för IoT Plug and Play kapacitets modeller som bryggan använder. Modellen definierar funktionerna hos en IoT Plug and Play-enhet och är statisk och oföränderlig.
- En lista med IoT Plug and Play gränssnitts komponenter och följande information för varje komponent:
- Gränssnitts-ID och komponent namn.
- Det bro kort som krävs för att interagera med komponenten.
- Enhets information som bryggan behöver för att upprätta kommunikation med enheten. Till exempel maskinvaru-ID eller speciell information för ett kort, ett gränssnitt eller ett protokoll.
- En valfri typ av brygga eller gränssnitts konfiguration om kortet stöder flera kommunikations typer med liknande enheter. Exemplet visar hur en komponent för Bluetooth-sensor kan konfigureras:

    ```json
    {
      "_comment": "Component BLE sensor",
      "pnp_bridge_component_name": "blesensor1",
      "pnp_bridge_adapter_id": "bluetooth-sensor-pnp-adapter",
      "pnp_bridge_adapter_config": {
        "bluetooth_address": "267541100483311",
        "blesensor_identity" : "Blesensor1"
      }
    }
    ```

- En valfri lista med parametrar för global bro kort. Till exempel har Bluetooth sensor Bridge-kortet en lista med konfigurationer som stöds. En gränssnitts komponent som kräver Bluetooth-sensor kan välja en av följande konfigurationer `blesensor_identity` :

    ```json
    {
      "pnp_bridge_adapter_global_configs": {
        "bluetooth-sensor-pnp-adapter": {
          "Blesensor1" : {
            "company_id": "0x499",
            "endianness": "big",
            "telemetry_descriptor": [
              {
                "telemetry_name": "humidity",
                "data_parse_type": "uint8",
                "data_offset": 1,
                "conversion_bias": 0,
                "conversion_coefficient": 0.5
              },
              {
                "telemetry_name": "temperature",
                "data_parse_type": "int8",
                "data_offset": 2,
                "conversion_bias": 0,
                "conversion_coefficient": 1.0
              },
              {
                "telemetry_name": "pressure",
                "data_parse_type": "int16",
                "data_offset": 4,
                "conversion_bias": 0,
                "conversion_coefficient": 1.0
              },
              {
                "telemetry_name": "acceleration_x",
                "data_parse_type": "int16",
                "data_offset": 6,
                "conversion_bias": 0,
                "conversion_coefficient": 0.00980665
              },
              {
                "telemetry_name": "acceleration_y",
                "data_parse_type": "int16",
                "data_offset": 8,
                "conversion_bias": 0,
                "conversion_coefficient": 0.00980665
              },
              {
                "telemetry_name": "acceleration_z",
                "data_parse_type": "int16",
                "data_offset": 10,
                "conversion_bias": 0,
                "conversion_coefficient": 0.00980665
              }
            ]
          }
        }
      }
    }
    ```

## <a name="download-iot-plug-and-play-bridge"></a>Hämta IoT Plug and Play Bridge

Du kan ladda ned en fördefinierad version av bryggan med stödda kort i [IoT plug and Play Bridge-versioner](https://github.com/Azure/iot-plug-and-play-bridge/releases) och expandera listan med till gångar för den senaste versionen. Hämta den senaste versionen av programmet för ditt operativ system.

Du kan också hämta och Visa käll koden för [IoT plug and Play Bridge på GitHub](https://github.com/Azure/iot-plug-and-play-bridge).

## <a name="next-steps"></a>Nästa steg

Nu när du har en översikt över arkitekturen i IoT Plug and Play Bridge är nästa steg att lära dig mer om:

- [Så här ansluter du ett IoT Plug and Play Bridge-exempel som körs på Linux eller Windows till IoT Hub](./howto-use-iot-pnp-bridge.md)
- [Skapa och distribuera IoT Plug and Play-brygga](howto-build-deploy-extend-pnp-bridge.md)
- [Utöka IoT Plug and Play Bridge](howto-author-pnp-bridge-adapter.md)
- [IoT Plug and Play-brygga på GitHub](https://github.com/Azure/iot-plug-and-play-bridge)
