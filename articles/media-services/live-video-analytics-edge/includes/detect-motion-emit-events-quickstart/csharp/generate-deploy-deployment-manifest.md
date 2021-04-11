---
ms.openlocfilehash: 94044e95e83742487a0d4d650814a5324f07011a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101750424"
---
Distributions manifestet definierar vilka moduler som distribueras till en Edge-enhet. Den definierar också konfigurations inställningar för dessa moduler. 

Följ de här stegen för att generera manifestet från mallfilen och distribuera den sedan till gräns enheten.

1. Öppna Visual Studio Code.
1. Bredvid fönstret **Azure IoT Hub** väljer du ikonen **fler åtgärder** för att ange IoT Hub anslutnings sträng. Du kan kopiera strängen från filen *src/Cloud-to-Device-console-app/appsettings.jsi* filen. 

    ![Ange IOT-anslutningssträng](../../../media/quickstarts/set-iotconnection-string.png)

> [!NOTE]
> Du kan bli ombedd att ange information om inbyggd slut punkt för IoT Hub. Om du vill hämta den informationen går du till IoT Hub i Azure Portal och letar efter **inbyggda slut punkts** alternativ i det vänstra navigerings fönstret. Klicka där och leta efter den **Event Hub-kompatibla slut punkten** under avsnittet **händelsehubben-kompatibel slut punkt** . Kopiera och Använd texten i rutan. Slut punkten kommer att se ut ungefär så här:  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```

1. Högerklicka på **src/Edge/deployment.template.jspå** och välj **generera IoT Edge distributions manifest**.

    ![Generera distributions manifestet för IoT Edge](../../../media/quickstarts/generate-iot-edge-deployment-manifest.png)

    Den här åtgärden ska skapa en manifest fil med namnet *deployment.amd64.jspå* i mappen *src/Edge/config* .
1. Högerklicka på **src/Edge/config/deployment.amd64.jspå**, Välj **skapa distribution för en enskild enhet** och välj sedan namnet på din Edge-enhet.

    ![Skapa en distribution för en enskild enhet](../../../media/quickstarts/create-deployment-single-device.png)

1. När du uppmanas att välja en IoT Hub enhet väljer du **lva – exempel-Device** på den nedrullningsbara menyn.
1. Efter cirka 30 sekunder, i det nedre vänstra hörnet i fönstret, uppdaterar du Azure IoT Hub. Gräns enheten visar nu följande distribuerade moduler:

    * Video analys i real tid för IoT Edge (Modulnamn `lvaEdge` )
    * RTSP-Simulator (Real-Time Streaming Protocol) (modulens namn `rtspsim` )

RTSP Simulator-modulen simulerar en real tids video ström med hjälp av en videofil som kopierades till din Edge-enhet när du körde [installations skriptet för Live Video Analytics-resurser](https://github.com/Azure/live-video-analytics/tree/master/edge/setup). 

> [!NOTE]
> Om du använder en egen Edge-enhet i stället för den som har skapats av vårt installations skript, går du till din Edge-enhet och kör följande kommandon med **administratörs behörighet** för att hämta och lagra exempel video filen som används för den här snabb starten:  

```
mkdir /home/lvaedgeuser/samples      
mkdir /home/lvaedgeuser/samples/input    
curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaedgeuser/samples/input/camera-300s.mkv  
chown -R lvalvaedgeuser:localusergroup /home/lvaedgeuser/samples/  
```
I det här skedet distribueras modulerna men inga medie diagram är aktiva.
