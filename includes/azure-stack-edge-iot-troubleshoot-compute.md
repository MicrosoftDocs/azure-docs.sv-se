---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 01/21/2021
ms.openlocfilehash: 3defa62c55bb5ab042ade816f611ea45b39a0117
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2021
ms.locfileid: "98761550"
---
Använd IoT Edge-agentens körnings svar för att felsöka Compute-relaterade fel. Här är en lista över möjliga svar:

* 200 – OK
* 400-distributions konfigurationen är felaktig eller ogiltig.
* 417 – enheten har ingen distributions konfigurations uppsättning.
* 412-schema versionen i distributions konfigurationen är ogiltig.
* 406 – den IoT Edge enheten är offline eller skickar inte status rapporter.
* 500 – ett fel uppstod i IoT Edge Runtime.

Mer information finns i [IoT Edge agent](/azure/iot-edge/iot-edge-runtime?view=iotedge-2018-06&preserve-view=true#iot-edge-agent).