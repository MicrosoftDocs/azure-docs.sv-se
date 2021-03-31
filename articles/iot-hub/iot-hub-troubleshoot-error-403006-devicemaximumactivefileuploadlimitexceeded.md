---
title: Fel sökning av Azure IoT Hub-fel 403006 DeviceMaximumActiveFileUploadLimitExceeded
description: Förstå hur du åtgärdar fel 403006 DeviceMaximumActiveFileUploadLimitExceeded
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 1e3c05e4cc3ccf34573b55d3729aded16e26d66e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "76960846"
---
# <a name="403006-devicemaximumactivefileuploadlimitexceeded"></a>403006 DeviceMaximumActiveFileUploadLimitExceeded

I den här artikeln beskrivs orsaker och lösningar för **403006 DeviceMaximumActiveFileUploadLimitExceeded** -fel.

## <a name="symptoms"></a>Symtom

Din begäran om fil uppladdning Miss lyckas med felkoden **403006** och ett meddelande "antalet aktiva fil överförings begär Anden får inte överstiga 10".

## <a name="cause"></a>Orsak

Varje enhets klient är begränsad till [10 samtidiga fil överföringar](./iot-hub-devguide-quotas-throttling.md#other-limits). 

Du kan enkelt överskrida gränsen om enheten inte meddelar IoT Hub när fil överföringar har slutförts. Det här problemet orsakas ofta av ett instabilt nätverk på enhets sidan.

## <a name="solution"></a>Lösning

Se till att enheten omedelbart [meddelar IoT Hub fil uppladdningen har slutförts](./iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload). Försök sedan [att minska SAS-tokens TTL-värde för fil överförings konfigurationen](iot-hub-configure-file-upload.md).

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om fil överföringar kan du läsa [Ladda upp filer med IoT Hub](./iot-hub-devguide-file-upload.md) och [Konfigurera IoT Hub fil överföringar med hjälp av Azure Portal](./iot-hub-configure-file-upload.md).