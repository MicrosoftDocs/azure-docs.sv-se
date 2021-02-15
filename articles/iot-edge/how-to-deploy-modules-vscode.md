---
title: Distribuera moduler från Visual Studio Code-Azure IoT Edge
description: Använd Visual Studio Code med Azure IoT-verktyg för att skicka en IoT Edge modul från din IoT Hub till din IoT Edge enhet, enligt konfigurationen i ett distributions manifest.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/13/2020
ms.topic: conceptual
ms.reviewer: ''
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6a6415aa55a67b37d9564398eb77dacb48cf16f0
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100378119"
---
# <a name="deploy-azure-iot-edge-modules-from-visual-studio-code"></a>Distribuera Azure IoT Edge moduler från Visual Studio Code

När du skapar IoT Edge-moduler med din affärs logik vill du distribuera dem till dina enheter så att de körs på gränsen. Om du har flera moduler som arbetar tillsammans för att samla in och bearbeta data kan du distribuera dem samtidigt och deklarera de routningsregler som ansluter dem.

Den här artikeln visar hur du skapar ett JSON-distributions manifest och använder sedan filen för att skicka distributionen till en IoT Edge enhet. Information om hur du skapar en distribution som riktar sig till flera enheter baserat på deras delade taggar finns i [distribuera IoT Edge moduler i skala med Visual Studio Code](how-to-deploy-vscode-at-scale.md).

## <a name="prerequisites"></a>Förutsättningar

* En [IoT-hubb](../iot-hub/iot-hub-create-through-portal.md) i din Azure-prenumeration.
* En IoT Edge enhet

  Om du inte har konfigurerat en IoT Edge enhet kan du skapa en på en virtuell Azure-dator. Följ stegen i en av snabb starts artiklarna för att [skapa en virtuell Linux-enhet](quickstart-linux.md) eller [skapa en virtuell Windows-enhet](quickstart.md).

* [Visual Studio Code](https://code.visualstudio.com/).
* [Azure IoT-verktyg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview) för Visual Studio Code.

## <a name="configure-a-deployment-manifest"></a>Konfigurera ett distributions manifest

Ett distributions manifest är ett JSON-dokument som beskriver vilka moduler som ska distribueras, hur data flödar mellan moduler och önskade egenskaper för modulen. Mer information om hur distributions manifest fungerar och hur du skapar dem finns i [förstå hur IoT Edge moduler kan användas, konfigureras och återanvändas](module-composition.md).

Om du vill distribuera moduler med Visual Studio Code sparar du distributions manifestet lokalt som en. JSON-fil. Du kommer att använda fil Sök vägen i nästa avsnitt när du kör kommandot för att tillämpa konfigurationen på enheten.

Här är ett grundläggande distributions manifest med en modul som exempel:

>[!NOTE]
>Det här exempel distributions manifestet använder schema version 1,1 för IoT Edge agent och hubb. Schema version 1,1 släpptes tillsammans med IoT Edge version 1.0.10, och möjliggör funktioner som start ordning och väg prioritering.

   ```json
   {
     "modulesContent": {
       "$edgeAgent": {
         "properties.desired": {
           "schemaVersion": "1.1",
           "runtime": {
             "type": "docker",
             "settings": {
               "minDockerVersion": "v1.25",
               "loggingOptions": "",
               "registryCredentials": {}
             }
           },
           "systemModules": {
             "edgeAgent": {
               "type": "docker",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-agent:1.1",
                 "createOptions": "{}"
               }
             },
             "edgeHub": {
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
                 "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
               }
             }
           },
           "modules": {
             "SimulatedTemperatureSensor": {
               "version": "1.0",
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                 "createOptions": "{}"
               }
             }
           }
         }
       },
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.1",
           "routes": {
               "route": "FROM /messages/* INTO $upstream"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       },
       "SimulatedTemperatureSensor": {
         "properties.desired": {}
       }
     }
   }
   ```

## <a name="sign-in-to-access-your-iot-hub"></a>Logga in för att få åtkomst till din IoT Hub

Du kan använda Azure IoT-tillägg för Visual Studio Code för att utföra åtgärder med IoT Hub. För att dessa åtgärder ska fungera måste du logga in på ditt Azure-konto och välja den IoT-hubb som du arbetar med.

1. Öppna vyn **Utforskare** i Visual Studio Code.

1. Expandera avsnittet **Azure IoT Hub** längst ned i Utforskaren.

   ![Avsnittet Expandera Azure IoT Hub](./media/how-to-deploy-modules-vscode/azure-iot-hub-devices.png)

1. Klicka på **...** i avsnitts rubriken för **Azure IoT Hub** . Om du inte ser ellipsen hovrar du över sidhuvudet.

1. Välj **välj IoT Hub**.

1. Om du inte är inloggad på ditt Azure-konto följer du anvisningarna för att göra det.

1. Välj din Azure-prenumeration.

1. Välj din IoT-hubb.

## <a name="deploy-to-your-device"></a>Distribuera till din nya enhet

Du distribuerar moduler till din enhet genom att använda distributions manifestet som du konfigurerade med informationen i modulen.

1. I vyn Visual Studio Code Explorer expanderar du avsnittet **Azure IoT Hub** och expanderar sedan noden **enheter** .

1. Högerklicka på den IoT Edge enhet som du vill konfigurera med distributions manifestet.

    > [!TIP]
    > För att bekräfta att enheten du har valt är en IoT Edge enhet, väljer du den för att expandera listan över moduler och kontrollerar förekomsten av **$edgeHub** och **$edgeAgent**. Varje IoT Edge enhet innehåller dessa två moduler.

1. Välj **skapa distribution för en enskild enhet**.

1. Navigera till den distributions manifest-JSON-fil som du vill använda och klicka på **Välj gräns distributions manifest**.

   ![Välj gräns distributions manifest](./media/how-to-deploy-modules-vscode/select-deployment-manifest.png)

Resultatet av distributionen skrivs ut i VS-kodens utdata. Lyckade distributioner tillämpas inom några minuter om mål enheten körs och är ansluten till Internet.

## <a name="view-modules-on-your-device"></a>Visa moduler på enheten

När du har distribuerat moduler till din enhet kan du Visa alla i **Azure IoT Hub** -avsnittet. Välj pilen bredvid IoT Edge-enheten för att expandera den. Alla moduler som körs för tillfället visas.

Om du nyligen har distribuerat nya moduler till en enhet hovrar du över avsnittet om **Azure IoT Hub-enheter** och väljer uppdaterings ikonen för att uppdatera vyn.

Högerklicka på namnet på en modul för att visa och redigera modulen.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [distribuerar och övervakar IoT Edge moduler i skala med Visual Studio Code](how-to-deploy-at-scale.md)
