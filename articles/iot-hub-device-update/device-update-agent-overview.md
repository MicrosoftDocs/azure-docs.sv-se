---
title: Förstå enhetsuppdatering för Azure IoT Hub Agent| Microsoft Docs
description: Förstå enhetsuppdatering för Azure IoT Hub agent.
author: ValOlson
ms.author: valls
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 0d97287657b1e1fe7d540e8811c90794aaa5fece
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739574"
---
# <a name="device-update-for-iot-hub-agent-overview"></a>Översikt av enhetsuppdatering för IoT Hub agent

Enhetsuppdateringsagenten består av två konceptuella lager:

* Gränssnittslagret bygger på [Azure IoT Plug and Play (PnP)](../iot-pnp/overview-iot-plug-and-play.md) så att meddelanden kan flöda mellan enhetsuppdateringsagenten och enhetsuppdateringstjänsterna.
* Plattformslagret ansvarar för de högnivåuppdateringsåtgärder för Ladda ned, Installera och Tillämpa som kan vara plattforms- eller enhetsspecifik.

:::image type="content" source="media/understand-device-update/client-agent-reference-implementations.png" alt-text="Agentimplementering." lightbox="media/understand-device-update/client-agent-reference-implementations.png":::

## <a name="the-interface-layer"></a>Gränssnittsskiktet

Gränssnittslagret består av [ADU Core Interface och](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/adu_core_interface) [Device Information Interface.](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/device_info_interface)

Dessa gränssnitt förlitar sig på en konfigurationsfil för standardvärden. Standardvärdena omfattar aduc_manufacturer och aduc_model AzureDeviceUpdateCore-gränssnittet och modell och tillverkare för gränssnittet DeviceInformation. [Läs mer](device-update-configuration-file.md) om konfigurationsfilen.

### <a name="adu-core-interface"></a>ADU Core Interface

ADU Core-gränssnittet är den primära kommunikationskanalen mellan enhetsuppdateringsagenten och tjänsterna. [Läs mer om](device-update-plug-and-play.md#adu-core-interface) det här gränssnittet.

### <a name="device-information-interface"></a>Device Information Interface

Device Information Interface används för att implementera `Azure IoT PnP DeviceInformation` -gränssnittet. [Läs mer om](device-update-plug-and-play.md#device-information-interface) det här gränssnittet.

## <a name="the-platform-layer"></a>Plattformsskiktet

Det finns två implementeringar av plattformsskiktet. Simulatorplattformsskiktet har en trivial implementering av uppdateringsåtgärderna och används för att snabbt testa och utvärdera enhetsuppdatering för IoT Hub tjänster och installation. När enhetsuppdateringsagenten har skapats med simulatorplattformslagret refererar vi till den som simulatoragent för enhetsuppdatering eller bara simulator. [Läs mer](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) om hur du använder simulatoragenten. Linux Platform Layer integreras med [Leveransoptimering](https://github.com/microsoft/do-client) för nedladdningar och används i vår Raspberry Pi-referensavbildning och alla klienter som körs på Linux-system.

### <a name="simulator-platform-layer"></a>Simulatorplattformslager

Implementeringen av simulatorplattformslagret finns i och kan användas för att testa `src/platform_layers/simulator_platform_layer` och utvärdera enhetsuppdatering för IoT Hub.  Många av åtgärderna i implementeringen av "simulatorn" modelleras för att minska fysiska ändringar för att experimentera med Enhetsuppdatering för IoT Hub.  En "simulerad" uppdatering från slut till slut kan utföras med hjälp av det här plattformsskiktet. [Läs mer om](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) att köra simulatoragenten.

### <a name="linux-platform-layer"></a>Linux-plattformslager

Implementeringen av Linux Platform Layer finns i och integreras med Leveransoptimering Client för nedladdningar och används i vår Raspberry Pi-referensavbildning och alla klienter som körs på `src/platform_layers/linux_platform_layer` Linux-system. [](https://github.com/microsoft/do-client/releases)

Det här lagret kan integreras med olika uppdateringshanterare för att implementera installationsprogrammet. Uppdateringshanteraren `SWUpdate` anropar till exempel ett shell-skript för att anropa den `SWUpdate` körbara filen för att utföra en uppdatering.

## <a name="update-handlers"></a>Uppdateringshanterare

Uppdateringshanterare är komponenter som hanterar innehåll eller installationsprogramspecifika delar av uppdateringen. Implementeringar av uppdateringshanteraren finns i `src/content_handlers` .

### <a name="simulator-update-handler"></a>Simulatoruppdateringshanterare

Simulatoruppdateringshanteraren används av simulatorplattformslagret och kan användas med Linux-plattformsskiktet för falska interaktioner med en innehållshanterare. Simulatoruppdateringshanteraren implementerar API:er för uppdateringshanterare med mestadels inga åtgärder. Implementeringen av simulatoruppdateringshanteraren finns nedan:
* [Bilduppdateringssimulator](https://github.com/Azure/iot-hub-device-update/blob/main/src/content_handlers/swupdate_handler/inc/aduc/swupdate_simulator_handler.hpp)
* [Apt-simulator för paketuppdatering](https://github.com/Azure/iot-hub-device-update/blob/main/src/content_handlers/apt_handler/inc/aduc/apt_simulator_handler.hpp)

Obs! Fältet InstalledCriteria i PnP-gränssnittet AzureDeviceUpdateCore ska vara sha256-hashen för innehållet. Det här är samma hash som finns i [importmanifestobjektet](import-update.md#create-a-device-update-import-manifest). [Läs mer](device-update-plug-and-play.md) `installedCriteria` om och `AzureDeviceUpdateCore` -gränssnittet.

### <a name="swupdate-update-handler"></a>`SWUpdate` Uppdateringshanterare

Uppdateringshanteraren" integreras med den körbara kommandoraden och andra shell-kommandon för att implementera `SWUpdate` `SWUpdate` A/B-uppdateringar specifikt för Raspberry Pi-referensbilden. Hitta den senaste Raspberry Pi-referensbilden [här.](https://github.com/Azure/iot-hub-device-update/releases) `SWUpdate`Uppdateringshanteraren implementeras i [src/content_handlers/swupdate_content_handler](https://github.com/Azure/iot-hub-device-update/tree/main/src/content_handlers/swupdate_handler).

### <a name="apt-update-handler"></a>APT-uppdateringshanterare

APT-uppdateringshanteraren bearbetar ett APT-specifikt uppdateringsmanifest och anropar APT för att installera eller uppdatera de angivna Debian-paketen.

## <a name="self-update-device-update-agent"></a>Uppdatera enhetsuppdateringsagenten själv

Enhetsuppdateringsagenten och dess beroenden kan uppdateras via enhetsuppdateringen för IoT Hub pipeline. Om du använder en avbildningsbaserad uppdatering ska du inkludera den senaste enhetsuppdateringsagenten i den nya avbildningen. Om du använder en paketbaserad uppdatering ska du inkludera enhetsuppdateringsagenten och dess önskade version i apt-manifestet precis som andra paket. [Läs mer om](device-update-apt-manifest.md) apt-manifest. Du kan kontrollera den installerade versionen av enhetsuppdateringsagenten och Leveransoptimering i avsnittet Enhetsegenskaper för [din IoT-enhetstvilling.](../iot-hub/iot-hub-devguide-device-twins.md) [Läs mer om enhetsegenskaper under ADU Core Interface](device-update-plug-and-play.md#device-properties).

## <a name="next-steps"></a>Nästa steg
[Förstå konfigurationsfilen för enhetsuppdateringsagenten](device-update-configuration-file.md)