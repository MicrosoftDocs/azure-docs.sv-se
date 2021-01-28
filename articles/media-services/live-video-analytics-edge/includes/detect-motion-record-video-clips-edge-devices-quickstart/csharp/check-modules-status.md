---
ms.openlocfilehash: 53052097fa6616f889b710c58488a9f7a616168d
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98956369"
---
I steget [generera och distribuera IoT Edge distributions manifestet](../../../detect-motion-emit-events-quickstart.md#generate-and-deploy-the-deployment-manifest) i Visual Studio Code, expanderar du noden **lva-Sample-Device** i **Azure IoT Hub** (i det nedre vänstra avsnittet). Du bör se följande moduler distribuerade:

* Modulen live video analys med namnet `lvaEdge`
* `rtspsim`Modulen, som simulerar en RTSP-server som fungerar som källa för en Live-videofeed

  ![Moduler](../../../media/quickstarts/lva-sample-device-node.png)

> [!NOTE]
> Ovanstående steg förutsätter att du använder den virtuella datorn som skapats av installations skriptet. Om du använder en egen Edge-enhet i stället går du till din Edge-enhet och kör följande kommandon med **administratörs behörighet** för att hämta och lagra exempel video filen som används för den här snabb starten:  

```
mkdir /home/lvaadmin/samples
mkdir /home/lvaadmin/samples/input    
curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaadmin/samples/input/camera-300s.mkv  
chown -R lvaadmin /home/lvaadmin/samples/  
```
