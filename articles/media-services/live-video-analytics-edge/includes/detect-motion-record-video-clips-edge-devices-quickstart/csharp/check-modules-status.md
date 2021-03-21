---
ms.openlocfilehash: 3f92bae608284c8b619be34a0e08f15e831bf88e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101750342"
---
I steget [generera och distribuera IoT Edge distributions manifestet](../../../detect-motion-emit-events-quickstart.md#generate-and-deploy-the-deployment-manifest) i Visual Studio Code, expanderar du noden **lva-Sample-Device** i **Azure IoT Hub** (i det nedre vänstra avsnittet). Du bör se följande moduler distribuerade:

* Modulen live video analys med namnet `lvaEdge`
* `rtspsim`Modulen, som simulerar en RTSP-server som fungerar som källa för en Live-videofeed

  ![Moduler](../../../media/quickstarts/lva-sample-device-node.png)

> [!NOTE]
> Ovanstående steg förutsätter att du använder den virtuella datorn som skapats av installations skriptet. Om du använder en egen Edge-enhet i stället går du till din Edge-enhet och kör följande kommandon med **administratörs behörighet** för att hämta och lagra exempel video filen som används för den här snabb starten:  

```
mkdir /home/lvaedgeuser/samples
mkdir /home/lvaedgeuser/samples/input    
curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaedgeuser/samples/input/camera-300s.mkv  
chown -R lvalvaedgeuser:localusergroup /home/lvaedgeuser/samples/  
```
