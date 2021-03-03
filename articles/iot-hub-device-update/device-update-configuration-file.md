---
title: Förstå enhets uppdatering för Azure IoT Hub konfigurations fil | Microsoft Docs
description: Förstå enhets uppdatering för Azure IoT Hub konfigurations filen.
author: ValOlson
ms.author: valls
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 39ecd9d6d0deec942d5c8cce9357c779a4b328d3
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101663828"
---
# <a name="device-update-for-iot-hub-configuration-file"></a>Enhets uppdatering för IoT Hub konfigurations fil

"adu-conf.txt" är en valfri fil som kan skapas för att hantera följande konfigurationer.

* AzureDeviceUpdateCore: 4. ClientMetadata: 4. deviceProperties ["tillverkare"]
* AzureDeviceUpdateCore: 4. ClientMetadata: 4. deviceProperties ["modell"]
* DeviceInformation. tillverkare
* DeviceInformation. Model
* Enhets anslutnings sträng (om den inte är känd av enhets uppdaterings agenten).

## <a name="purpose"></a>Syfte
Enhets uppdaterings agenten kommer först att försöka hämta `manufacturer` och- `model` värden från enheten som ska användas för [gränssnitts skiktet](device-update-agent-overview.md#the-interface-layer). Om detta Miss lyckas kommer enhets uppdaterings agenten att leta efter "adu-conf.txt"-filen och använda värdena från där. Om båda försöken inte lyckas använder enhets uppdaterings agenten [standardvärden](https://github.com/Azure/iot-hub-device-update/blob/main/CMakeLists.txt) .

Läs mer om [ADU Core Interface](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/adu_core_interface) och [Device information Interface](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/device_info_interface).

## <a name="file-location"></a>Fil Sök väg

I Linux-systemet, i partitionen eller disken `adu` , skapar du en textfil med namnet "adu-conf.txt" med följande fält.

## <a name="list-of-fields"></a>Lista med fält

|Namn|Beskrivning|
|-----------|--------------------|
|connection_string|Företablerad anslutnings sträng som enheten kan använda för att ansluta till IoT Hub. Obs! krävs inte om du konfigurerar agenten för enhets uppdatering via [Azure IoT Identity service](https://azure.github.io/iot-identity-service/)|
|aduc_manufacturer|Rapporteras av `AzureDeviceUpdateCore:4.ClientMetadata:4` gränssnittet för att klassificera enheten för att rikta uppdaterings distributionen.|
|aduc_model|Rapporteras av `AzureDeviceUpdateCore:4.ClientMetadata:4` gränssnittet för att klassificera enheten för att rikta uppdaterings distributionen.|
|manufacturer|Rapporteras av enhets uppdaterings agenten som en del av `DeviceInformation` gränssnittet.|
|modell|Rapporteras av enhets uppdaterings agenten som en del av `DeviceInformation` gränssnittet.|

## <a name="example-adu-conftxt-file-contents"></a>Exempel på fil innehåll för "adu-conf.txt"

```markdown

connection_string = `HostName=<yourIoTHubName>;DeviceId=<yourDeviceId>;SharedAccessKey=<yourSharedAccessKey>`
aduc_manufacturer = <value to send through `AzureDeviceUpdateCore:4.ClientMetadata:4.deviceProperties["manufacturer"]`
aduc_model = <value to send through `AzureDeviceUpdateCore:4.ClientMetadata:4.deviceProperties["model"]`
manufacturer = <value to send through `DeviceInformation.manufacturer`
model = <value to send through `DeviceInformation.manufacturer`
```
