---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 02/05/2021
ms.openlocfilehash: ad981264a99bd48e27f745a789ebe857b7f17d80
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750323"
---
Använd IoT Edge-agentens körnings svar för att felsöka Compute-relaterade fel. Här är en lista över möjliga svar:

* 200 – OK
* 400-distributions konfigurationen är felaktig eller ogiltig.
* 417 – enheten har ingen distributions konfigurations uppsättning.
* 412-schema versionen i distributions konfigurationen är ogiltig.
* 406 – den IoT Edge enheten är offline eller skickar inte status rapporter.
* 500 – ett fel uppstod i IoT Edge Runtime.

Mer information finns i [IoT Edge agent](../articles/iot-edge/iot-edge-runtime.md?preserve-view=true&view=iotedge-2018-06#iot-edge-agent).

Följande fel är relaterat till tjänsten IoT Edge på Azure Stack Edge Pro<!--/ Data Box Gateway--> enheter.

### <a name="compute-modules-have-unknown-status-and-cant-be-used"></a>Compute-moduler har okänd status och kan inte användas

#### <a name="error-description"></a>Felbeskrivning

Alla moduler på enheten visar okänd status och kan inte användas. Den okända statusen finns kvar i en omstart.<!--Original Support ticket relates to trying to deploy a container app on a Hub. Based on the work item, I assume the error description should not be that specific, and that the error applies to Azure Stack Edge Devices, which is the focus of this troubleshooting.-->

#### <a name="suggested-solution"></a>Föreslagen lösning

Ta bort IoT Edge tjänsten och distribuera sedan om modulerna. Mer information finns i [ta bort IoT Edge-tjänsten](../articles/databox-online/azure-stack-edge-j-series-manage-compute.md#remove-iot-edge-service).