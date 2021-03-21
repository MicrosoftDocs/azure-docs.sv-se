---
title: Fånga Azure Kinect Device-synkronisering
description: Lär dig hur du synkroniserar Azure Kinect Capture-enheter med hjälp av Azure Kinect sensor SDK.
author: xthexder
ms.author: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, sensor, SDK, djup, RGB, intern, extern, synkronisering, seriekopplade-kedja, fas förskjutning
ms.openlocfilehash: ce0c72d3d708d5696a9775b3885f278f0c23cac1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102034326"
---
# <a name="capture-azure-kinect-device-synchronization"></a>Fånga Azure Kinect Device-synkronisering

Azure Kinect-maskinvaran kan justera hämtnings tiden för färg-och djup avbildningar. Justering mellan kameror på samma enhet är **intern synkronisering**. Hämtnings tids justering över flera anslutna enheter är **extern synkronisering**. Mikrofonens matris fungerar oberoende av kamerans färg och djup.

## <a name="device-internal-synchronization"></a>Intern enhets synkronisering

Bild hämtning mellan enskilda kameror synkroniseras i maskin vara. I varje [k4a_capture_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__capture__t.html) som innehåller bilder från både färg-och djup sensorn justeras avbildningens tidsstämplar baserat på maskin varans drift läge. Som standard är avbildningarna i en bild centrerade exponerings justering. Den relativa tids inställningen för djup-och färg dum par kan justeras med hjälp av `depth_delay_off_color_usec` [k4a_device_configuration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html).

## <a name="device-external-synchronization"></a>Extern enhets synkronisering

Se [Konfigurera extern synkronisering](https://support.microsoft.com/help/4494429/sync-multiple-azure-kinect-dk-devices) för maskin varu installation.

Program varan för varje ansluten enhet måste konfigureras för att köras i ett **huvud** läge eller **underordnat** läge. Den här inställningen konfigureras på [k4a_device_configuration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html).

När du använder extern synkronisering bör underordnade kameror alltid startas innan bakgrunden för tidsstämplarna är korrekt justerade.

### <a name="subordinate-mode"></a>Underordnat läge

```C
k4a_device_configuration_t deviceConfig;
deviceConfig.wired_sync_mode = K4A_WIRED_SYNC_MODE_SUBORDINATE
```

### <a name="master-mode"></a>Huvud läge

```C
k4a_device_configuration_t deviceConfig;
deviceConfig.wired_sync_mode = K4A_WIRED_SYNC_MODE_MASTER;
```

### <a name="retrieving-synchronization-jack-state"></a>Hämtar status för synkroniseringsprogram

Använd funktionen [k4a_device_get_sync_jack](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga0209ac87bfd055163677321b0304e962.html#ga0209ac87bfd055163677321b0304e962) för att program mässigt hämta det aktuella läget för inmatnings-och synkronisering av utdata.

## <a name="next-steps"></a>Nästa steg

Nu vet du hur du aktiverar och avbildar synkronisering av enheter. Du kan också granska hur du använder 

>[!div class="nextstepaction"]
>[Azure Kinect sensor SDK-post och uppspelnings-API](record-playback-api.md)
