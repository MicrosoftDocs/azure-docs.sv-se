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
ms.openlocfilehash: 3a70c11fd4f2a0549370933c300f431a03ffcf1d
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061306"
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