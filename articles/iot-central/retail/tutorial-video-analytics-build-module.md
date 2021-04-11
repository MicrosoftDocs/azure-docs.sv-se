---
title: Självstudie – Ändra Azure IoT Edge Live Video Analytics-moduler
description: I den här självstudien får du se hur du ändrar och bygger de Live Video Analytics Gateway-moduler som används av program mal len video analys – objekt och rörelse identifiering.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: cbff2e774fae1f71624aa3cdbd83bfb21e3a9586
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101721471"
---
# <a name="tutorial-modify-and-build-the-live-video-analytics-gateway-modules"></a>Självstudie: ändra och bygga Live Video Analytics Gateway-moduler

I den här självstudien får du se hur du ändrar koden för IoT Edge-modulen för LVA-modulerna (Live Video Analytics).

De tidigare självstudierna använder färdiga avbildningar av modulerna.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra stegen i den här självstudien behöver du:


> [!div class="checklist"]
> * [Node.js](https://nodejs.org/en/download/) v10 eller senare
> * [Visual Studio Code](https://code.visualstudio.com/Download) med [TSLint](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-typescript-tslint-plugin) -tillägget installerat
> * [Docker](https://www.docker.com/products/docker-desktop) -motor
> * En [Azure Container Registry](../../container-registry/index.yml) som är värd för dina versioner av modulerna.
> * Ett [Azure Media Services](../../media-services/index.yml) konto. Om du har slutfört de tidigare självstudierna kan du återanvända den som du skapade tidigare.

## <a name="clone-the-repository"></a>Klona lagringsplatsen

Om du inte redan har klonat lagrings platsen använder du följande kommando för att klona den till en lämplig plats på den lokala datorn:

```cmd
git clone https://github.com/Azure/live-video-analytics
```

Öppna den lokala mappen *Live-Video-Analytics* med vs Code.

## <a name="edit-the-deploymentamd64json-file"></a>Redigera deployment.amd64.jspå filen

1. Om du inte redan har gjort det, skapar du en mapp som heter *Ref-Apps/lva-IoT-IoT-Central-Gateway/Storage* i den lokala kopian av **lva-Gateway** -lagringsplatsen. Den här mappen ignoreras av Git för att förhindra att du råkar kontrol lera konfidentiell information av misstag.
1. Kopiera filen *deployment.amd64.js* *från installationsmapp till* *lagringsmappen* .
1. Öppna filen *Storage/deployment.amd64.js* i vs Code.
1. Redigera `registryCredentials` avsnittet om du vill lägga till dina Azure Container Registry autentiseringsuppgifter.
1. Redigera `LvaEdgeGatewayModule` modul-avsnittet om du vill lägga till namnet på din avbildning och ditt AMS-kontonamn i `env:amsAccountName:value` .
1. Redigera `lvaYolov3` avsnittet modul och Lägg till namnet på din bild.
1. Redigera `lvaEdge` avsnittet modul och Lägg till namnet på din bild.
1. I avsnittet [skapa ett video analys program i Azure IoT Central](tutorial-video-analytics-create-app-yolo-v3.md) finns mer information om hur du slutför konfigurationen.

## <a name="build-the-code"></a>Skapa koden

1. Innan du försöker skapa koden för första gången använder du VS Code-terminalen för att köra `npm install` kommandot. Det här kommandot installerar de nödvändiga paketen och kör installations skripten.

    > [!TIP]
    > Om du hämtar en nyare version av lagrings platsen GitHub lagrings platsen tar du bort mappen *node_modules* och kör `npm install` igen.

1. Redigera filen *./setup/imageConfig.jspå* filen för att uppdatera avbildningen med namnet baserat på ditt behållar register namn:

    ```json
    {
        "arch": "amd64",
        "imageName": "[Server].azurecr.io/lva-edge-gateway"
    }
    ```

1. Använd VS Code-terminalen för att köra `docker login [your server].azurecr.io` kommandot. Använd samma autentiseringsuppgifter som du angav i distributions manifestet för modulerna.

1. Använd VS Code-terminalen för att köra kommandot **NPM version patch** . Det här build-skriptet distribuerar avbildningarna till behållar registret. Utdata i fönstret VS Code-terminalen visar om bygget lyckades.

1. Versionen av **LvaEdgeGatewayModule** -avbildningen ökar varje gång bygget slutförs. Du måste använda den här versionen i distributions manifest filen.

## <a name="clean-up-resources"></a>Rensa resurser

Om du är klar med programmet kan du ta bort alla resurser som du har skapat på följande sätt:

1. I IoT Central programmet navigerar du till sidan **program** i avsnittet **Administration** . Välj sedan **Ta bort**.
1. Ta bort resurs gruppen **lva-RG** i Azure Portal.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om program mal len video analys – objekt och rörelse identifiering och LVA IoT Edge moduler, är det föreslagna nästa steg att lära dig mer om:

> [!div class="nextstepaction"]
> [Skapa lösningar för detaljhandeln med Azure IoT Central](overview-iot-central-retail.md)