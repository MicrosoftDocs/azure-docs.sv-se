---
title: Förstå enhets uppdatering för Azure IoT Hub-agenten | Microsoft Docs
description: Förstå enhets uppdatering för Azure IoT Hub-agenten.
author: ValOlson
ms.author: valls
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: efe5d0171463668bda19a0d0445fc67f3734aaee
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105561244"
---
# <a name="device-update-for-iot-hub-agent-overview"></a>Översikt av enhetsuppdatering för IoT Hub agent

Enhets uppdaterings agenten består av två konceptuella lager:

* Gränssnitts skiktet bygger ovanpå [Azure IoT plug and Play (PNP)](../iot-pnp/overview-iot-plug-and-play.md) som gör det möjligt att skicka meddelanden mellan enhets uppdaterings agenten och enhets uppdaterings tjänsterna.
* Plattforms skiktet ansvarar för uppdaterings åtgärder på hög nivå för att ladda ned, installera och tillämpa som kan vara plattform, eller enhets information.

:::image type="content" source="media/understand-device-update/client-agent-reference-implementations.png" alt-text="Agent implementeringar." lightbox="media/understand-device-update/client-agent-reference-implementations.png":::

## <a name="the-interface-layer"></a>Gränssnitts skiktet

Gränssnitts lagret består av [ADU Core-gränssnittet](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/adu_core_interface) och [enhets informations gränssnittet](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/device_info_interface).

Dessa gränssnitt förlitar sig på en konfigurations fil för standardvärden. Standardvärdena är aduc_manufacturer och aduc_model för AzureDeviceUpdateCore-gränssnittet och-modellen och-tillverkaren för DeviceInformation-gränssnittet. [Läs mer](device-update-configuration-file.md) i konfigurations filen.

### <a name="adu-core-interface"></a>ADU Core-gränssnitt

Gränssnittet "ADU Core" är den primära kommunikations kanalen mellan enhets uppdaterings agenten och tjänsterna. [Läs mer](device-update-plug-and-play.md#adu-core-interface) om det här gränssnittet.

### <a name="device-information-interface"></a>Enhets informations gränssnitt

Enhets informations gränssnittet används för att implementera `Azure IoT PnP DeviceInformation` gränssnittet. [Läs mer](device-update-plug-and-play.md#device-information-interface) om det här gränssnittet.

## <a name="the-platform-layer"></a>Plattforms skiktet

Det finns två implementeringar av plattforms skiktet. Simulator Platform Layer har en snabb implementering av uppdaterings åtgärderna och används för att snabbt testa och utvärdera enhets uppdateringar för IoT Hub tjänster och installation. När enhets uppdaterings agenten har skapats med Simulator Platform Layer, hänvisar vi till den som enhets uppdaterings Simulator agent eller bara Simulator. [Läs mer](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) om hur du använder Simulator-agenten. Linux Platform Layer integreras med [leverans optimering](https://github.com/microsoft/do-client) för hämtningar och används i vår Raspberry Pi-referens avbildning och alla klienter som körs på Linux-system.

### <a name="simulator-platform-layer"></a>Simulator Platform Layer

Simulator Platform Layer-implementeringen finns i `src/platform_layers/simulator_platform_layer` och kan användas för att testa och utvärdera enhets uppdatering för IoT Hub.  Många av åtgärderna i implementeringen "Simulator" är nedreducerade för att minska de fysiska ändringarna för att experimentera med enhets uppdatering för IoT Hub.  En "simulerad" uppdatering från slut punkt till slut punkt kan utföras med det här plattforms skiktet. [Läs mer](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) om att köra Simulator-agenten.

### <a name="linux-platform-layer"></a>Plattforms lager för Linux

Linux Platform Layer-implementeringen finns i `src/platform_layers/linux_platform_layer` och integreras med [leverans optimerings klienten](https://github.com/microsoft/do-client/releases) för hämtningar och används i vår Raspberry Pi-referens avbildning och alla klienter som körs på Linux-system.

Det här lagret kan integreras med olika uppdaterings hanterare för att implementera installations programmet. Till exempel `SWUpdate` anropar uppdaterings hanteraren ett gränssnitts skript för att anropa den `SWUpdate` körbara filen för att utföra en uppdatering.

## <a name="update-handlers"></a>Uppdatera hanterare

Uppdaterings hanterare är komponenter som hanterar innehåll eller installations bara delar av uppdateringen. Implementeringar av uppdaterings hanterare är i `src/content_handlers` .

### <a name="simulator-update-handler"></a>Uppdaterings hanterare för Simulator

Uppdaterings hanteraren för Simulator används av simulator Platform Layer och kan användas med Linux-plattforms skiktet för att falska interaktioner med en innehålls hanterare. Simulatorns uppdaterings hanterare implementerar API: erna för uppdaterings hanterare med det mesta av ingen-Ops. Du kan hitta implementeringen av Simulatorns uppdaterings hanterare nedan:
* [Avbildnings uppdaterings Simulator](https://github.com/Azure/iot-hub-device-update/blob/main/src/content_handlers/swupdate_handler/inc/aduc/swupdate_simulator_handler.hpp)
* [Paket uppdatering apt Simulator](https://github.com/Azure/iot-hub-device-update/blob/main/src/content_handlers/apt_handler/inc/aduc/apt_simulator_handler.hpp)

Obs! InstalledCriteria-fältet i PnP-gränssnittet för AzureDeviceUpdateCore ska vara SHA256-hashen för innehållet. Detta är samma hash som finns i [objektet importera manifest](import-update.md#create-device-update-import-manifest). [Läs mer](device-update-plug-and-play.md) om `installedCriteria` och `AzureDeviceUpdateCore` gränssnittet.

### <a name="swupdate-update-handler"></a>`SWUpdate` Uppdaterings hanterare

`SWUpdate`Uppdaterings hanteraren "integreras med den `SWUpdate` körbara kommando raden och andra Shell-kommandon för att implementera A/B-uppdateringar specifikt för referens avbildningen Raspberry Pi. Hitta den senaste Raspberry Pi-referens avbildningen [här](https://github.com/Azure/iot-hub-device-update/releases). `SWUpdate`Uppdaterings hanteraren implementeras i [src/content_handlers/swupdate_content_handler](https://github.com/Azure/iot-hub-device-update/tree/main/src/content_handlers/swupdate_handler).

### <a name="apt-update-handler"></a>APT uppdaterings hanterare

Uppdaterings hanteraren för APT bearbetar ett APT uppdaterings manifest och anropar APT för att installera eller uppdatera de angivna Debian-paketen.

## <a name="self-update-device-update-agent"></a>Enhets uppdaterings agent själv uppdatering

Enhets uppdaterings agenten och dess beroenden kan uppdateras genom enhets uppdateringen för IoT Hub pipelinen. Om du använder en avbildningsbaserad uppdatering inkluderar du den senaste enhets uppdaterings agenten i den nya avbildningen. Om du använder en paket-baserad uppdatering inkluderar du enhets uppdaterings agenten och den önskade versionen i apt-manifestet som ett annat paket. [Läs mer](device-update-apt-manifest.md) om apt-manifestet. Du kan kontrol lera den installerade versionen av enhets uppdaterings agenten och leverans optimerings agenten i avsnittet enhets egenskaper på din [IoT-enhet](../iot-hub/iot-hub-devguide-device-twins.md). [Läs mer om enhets egenskaper under ADU Core Interface](device-update-plug-and-play.md#device-properties).

## <a name="next-steps"></a>Nästa steg
[Förstå konfigurations filen för enhets uppdaterings agenten](device-update-configuration-file.md)