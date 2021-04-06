---
title: Kör avvikelse detektor på IoT Edge
titleSuffix: Azure Cognitive Services
description: Distribuera modulen avvikelse detektor till IoT Edge.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 12/03/2020
ms.author: mbullwin
ms.openlocfilehash: b4153b07b153a9ee0b16dc032ab5e7810e236d7d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "98936272"
---
# <a name="deploy-an-anomaly-detector-module-to-iot-edge"></a>Distribuera en avvikelse detektor modul till IoT Edge

Lär dig hur du distribuerar modulen Cognitive Services [avvikelse detektor](../anomaly-detector-container-howto.md) till en IoT Edge enhet. När den har distribuerats till IoT Edge körs modulen i IoT Edge tillsammans med andra moduler som behållar instanser. Den exponerar exakt samma API: er som en instans av en avvikelse detektor behållare som körs i en standard Docker-behållar miljö. 

## <a name="prerequisites"></a>Förutsättningar

* Använd en Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free) innan du börjar.
* Installera [Azure CLI](/cli/azure/install-azure-cli).
* En [IoT Hub](../../../iot-hub/iot-hub-create-through-portal.md) och en [IoT Edge](../../../iot-edge/quickstart-linux.md) enhet.

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

## <a name="deploy-the-anomaly-detection-module-to-the-edge"></a>Distribuera modulen för avvikelse identifiering till kanten

1. I Azure Portal anger du **avvikelse detektor på IoT Edge** i sökningen och öppnar resultatet av Azure Marketplace.
2. Det tar dig till Azure Portalens [mål enheter för sidan IoT Edge modul](https://portal.azure.com/#create/azure-cognitive-service.edge-anomaly-detector). Ange följande nödvändiga information.

    1. Välj din prenumeration.

    1. Välj din IoT Hub.

    1. Välj **Sök efter enhet** och hitta en IoT Edge enhet.

3. Välj knappen **Skapa**.

4. Välj modulen **AnomalyDetectoronIoTEdge** .

    :::image type="content" source="../media/deploy-anomaly-detection-on-iot-edge/iot-edge-modules.png" alt-text="Bild av IoT Edge modulers användar gränssnitt med AnomalyDetectoronIoTEdge-länk markerad med en röd ruta för att visa att det är det objekt som ska väljas.":::

5. Navigera till **Miljövariabler** och ange följande information.

    1.  Behåll värdet accept för **Eula**.

    1. Fyll i **Fakturering** med din Cognitive Services-slutpunkt.

    1. Fyll i **ApiKey** med din API-nyckel för Cognitive Services.

    :::image type="content" source="../media/deploy-anomaly-detection-on-iot-edge/environment-variables.png" alt-text="Miljövariabler med röda rutor runt de områden som behöver värden som ska fyllas i för slut punkt och API-nyckel":::

6. Välj **Uppdatera**

7. Välj **Nästa: vägar** för att definiera din väg. Du definierar att alla meddelanden från alla moduler ska gå till Azure IoT Hub.

8. Välj **Nästa: Granska + skapa**. Du kan förhandsgranska den JSON-fil som definierar alla moduler som distribueras till din IoT Edge-enhet.
    
9. Starta moduldistributionen genom att välja **Skapa**.

10. När du har slutfört moduldistributionen går du tillbaka till sidan IoT Edge i IoT Hub. Välj din enhet i listan över IoT Edge-enheter för att se dess information.

11. Rulla nedåt för att se de moduler som visas. Kontrol lera att körnings statusen körs för den nya modulen. 

Information om hur du felsöker IoT Edge enhetens körnings status finns i [fel söknings guiden](../../../iot-edge/troubleshoot.md)

## <a name="test-anomaly-detector-on-an-iot-edge-device"></a>Testa avvikelse detektor på en IoT Edge enhet

Du kommer att göra ett HTTP-anrop till den Azure IoT Edge-enhet där Azure Cognitive Services-containern körs. Behållaren tillhandahåller REST-baserade slut punkts-API: er. Använd värden, `http://<your-edge-device-ipaddress>:5000` för API: er för modul.

Om din Edge-enhet inte redan tillåter inkommande kommunikation på port 5000 måste du skapa en ny **regel för inkommande portar**. 

För en virtuell Azure-dator kan detta ställas in under Inställningar för **virtuell dator**  >    >  **nätverk**  >  **inkommande port** regel  >  **Lägg till regel för inkommande port**.

Det finns flera sätt att verifiera att modulen körs. Leta reda på den *externa IP-* adressen och den exponerade porten för gräns enheten i fråga och öppna din favorit webbläsare. Använd de olika URL: erna för begäran nedan för att verifiera att behållaren körs. URL: erna för exempel begär Anden som anges nedan är `http://<your-edge-device-ipaddress:5000` , men din speciella behållare kan variera. Tänk på att du behöver använda din Edge-enhets *externa IP* -adress.

| URL för begäran | Syfte |
|:-------------|:---------|
| `http://<your-edge-device-ipaddress>:5000/` | Containern tillhandahåller en startsida. |
| `http://<your-edge-device-ipaddress>:5000/status` | Begärd med GET, kontrollerar detta om den API-nyckel som används för att starta behållaren är giltig utan att orsaka en slut punkts fråga. Den här begäran kan användas för Kubernetes [Live och beredskaps avsökningar](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/). |
| `http://<your-edge-device-ipaddress>:5000/swagger` | Containern tillhandahåller en fullständig uppsättning dokumentation för slutpunkterna samt en **Prova**-funktion. Med den här funktionen kan du ange dina inställningar i ett webbaserat HTML-formulär och göra frågan utan att behöva skriva någon kod. När frågan returnerar visas ett exempel på ett spiral kommando som visar de HTTP-rubriker och det text format som krävs. |

![Behållarens start sida](../../../../includes/media/cognitive-services-containers-api-documentation/container-webpage.png)

## <a name="next-steps"></a>Nästa steg

* Granska [installations-och körnings behållare](../anomaly-detector-container-configuration.md) för att hämta behållar avbildningen och köra behållaren
* Granska [Konfigurera behållare](../anomaly-detector-container-configuration.md) för konfigurations inställningar
* [Läs mer om API-tjänsten för avvikelse detektor](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
