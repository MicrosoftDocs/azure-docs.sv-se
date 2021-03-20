---
ms.openlocfilehash: 882ba60e16f770651a1d9fe3b02b61be2b4c34c8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99531417"
---
Du använder live video analys i IoT Edge-modulen för att identifiera rörelser i inkommande video strömmar och skicka händelser till IoT Hub. Följ dessa steg om du vill se de här händelserna:

1. Öppna Explorer-fönstret i Visual Studio Code och leta efter Azure-IoT Hub i det nedre vänstra hörnet.
1. Expandera noden **enheter** .
1. Högerklicka på **lva-Sample-Device** och välj **starta övervakning inbyggd händelse slut punkt**.

    ![Starta övervakning av en inbyggd händelse slut punkt](../../../media/quickstarts/start-monitoring-iothub-events.png)

> [!NOTE]
> Du kan bli ombedd att ange information om inbyggd slut punkt för IoT Hub. Om du vill hämta den informationen går du till IoT Hub i Azure Portal och letar efter **inbyggda slut punkts** alternativ i det vänstra navigerings fönstret. Klicka där och leta efter den **Event Hub-kompatibla slut punkten** under avsnittet **händelsehubben-kompatibel slut punkt** . Kopiera och Använd texten i rutan. Slut punkten kommer att se ut ungefär så här:  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```
