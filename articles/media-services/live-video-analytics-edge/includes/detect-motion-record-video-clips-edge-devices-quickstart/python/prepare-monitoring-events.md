---
ms.openlocfilehash: 3196d377c09ca0cce24093710bc4be13fb21d2e3
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99531113"
---
Se till att du har slutfört stegen för [att förbereda övervakningen av händelser](../../../detect-motion-emit-events-quickstart.md#prepare-to-monitor-events).

![Starta övervakning av inbyggd händelse slut punkt](../../../media/quickstarts/start-monitoring-iothub-events.png)

> [!NOTE]
> Du kan bli ombedd att ange information om inbyggd slut punkt för IoT Hub. Om du vill hämta den informationen går du till IoT Hub i Azure Portal och letar efter **inbyggda slut punkts** alternativ i det vänstra navigerings fönstret. Klicka där och leta efter den **Event Hub-kompatibla slut punkten** under avsnittet **händelsehubben-kompatibel slut punkt** . Kopiera och Använd texten i rutan. Slut punkten kommer att se ut ungefär så här:  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```
