---
title: Distribuera GPU-modul på Azure Stack Edge Pro GPU-enhet | Microsoft Docs
description: Beskriver hur du aktiverar beräkning och gör din Azure Stack Edge Pro-enhet Compute-Ready via det lokala användar gränssnittet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: af44912edf3ce98ceb71bd34388543f7652c2181
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105568468"
---
# <a name="deploy-a-gpu-enabled-iot-module-on-azure-stack-edge-pro-gpu-device"></a>Distribuera en GPU-aktiverad IoT-modul på Azure Stack Edge Pro GPU-enhet

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

I den här artikeln beskrivs hur du distribuerar en GPU-aktiverad IoT Edge-modul på din Azure Stack Edge Pro GPU-enhet. 

I den här artikeln kan du se hur du:
  - Förbered Azure Stack Edge Pro för att köra en GPU-modul.
  - Hämta och installera exempel kod från en git-lagringsplats.
  - Skapa lösningen och generera ett distributions manifest.
  - Distribuera lösningen till Azure Stack Edge Pro-enhet.
  - Övervaka utdata från modulen.


## <a name="about-sample-module"></a>Om exempel modul

GPU-exemplet i den här artikeln innehåller PyTorch-och TensorFlow-kod för att testa processor mot GPU.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar ska du kontrollera att du har:

- Du har åtkomst till en GPU-aktiverad 1-Node Azure Stack Edge Pro-enhet. Enheten aktiveras med en resurs i Azure. Se [Aktivera enheten](azure-stack-edge-gpu-deploy-activate.md).
- Du har konfigurerat Compute på den här enheten. Följ stegen i [självstudien: Konfigurera Compute på din Azure Stack Edge Pro-enhet](azure-stack-edge-gpu-deploy-configure-compute.md).
- En Azure Container Registry (ACR). Gå till bladet **åtkomst nycklar** och anteckna ACR-inloggnings servern, användar namnet och lösen ordet. Mer information finns i [snabb start: skapa ett privat behållar register med hjälp av Azure Portal](../container-registry/container-registry-get-started-portal.md#create-a-container-registry).
- Följande utvecklings resurser på en Windows-klient:
    - [Azure CLI 2,0 eller senare](https://aka.ms/installazurecliwindows)
    - [Docker CE](https://store.docker.com/editions/community/docker-ce-desktop-windows). Du kan behöva skapa ett konto för att ladda ned och installera program varan.
    - [Visual Studio Code](https://code.visualstudio.com/)  
    - [Azure IoT Edge-tillägg för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).    
    - [Python-tillägg för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python)    
    - [Python 3](https://www.python.org/)    
    - PIP för att installera python-paket (som vanligt vis ingår i python-installationen)

## <a name="get-the-sample-code"></a>Hämta exempelkoden

1. Gå till [Azures smarta kant mönster i Azure-exempel](https://github.com/azure-samples/azure-intelligent-edge-patterns). Klona eller hämta zip-filen för kod. 

    ![Hämta zip-fil](media/azure-stack-edge-gpu-deploy-sample-module/download-zip-file-1.png)

    Extrahera filerna från zip-filen. Du kan också klona exemplen.

    ```json
    git clone https://github.com/Azure-Samples/azure-intelligent-edge-patterns.git
    ```

## <a name="build-and-deploy-module"></a>Bygga och distribuera modul

1. Öppna mappen **GpuReferenceModules** i Visual Studio Code.

    ![Öppna GPUReferenceModules i VS Code](media/azure-stack-edge-gpu-deploy-sample-module/open-folder-gpu-sample-1.png)

2. Öppna *deployment.template.js* och identifiera parametrarna som refererar till behållar registret. I följande fil används CONTAINER_REGISTRY_USERNAME, CONTAINER_REGISTRY_PASSWORD och CONTAINER_REGISTRY_NAME.

    ```json
        {
      "$schema-template": "2.0.0",
      "modulesContent": {
        "$edgeAgent": {
          "properties.desired": {
            "schemaVersion": "1.0",
            "runtime": {
              "type": "docker",
              "settings": {
                "minDockerVersion": "v1.25",
                "loggingOptions": "",
                "registryCredentials": {
                  "${CONTAINER_REGISTRY_NAME}":{
                  "username": "$CONTAINER_REGISTRY_USERNAME",
                  "password": "$CONTAINER_REGISTRY_PASSWORD",
                  "address": "${CONTAINER_REGISTRY_NAME}.azurecr.io"
                  }
                }
              }
            },
    ```
3. Skapa en ny fil. Fyll i värdena för dina behållar register parametrar (Använd de som identifierades i föregående steg) enligt följande: 

    ```json
    CONTAINER_REGISTRY_NAME=<YourContainerRegistryName>
    CONTAINER_REGISTRY_USERNAME=<YourContainerRegistryUserName>
    CONTAINER_REGISTRY_PASSWORD=<YourContainerRegistryPassword>
    ```
    En exempel- *. miljö* -fil visas nedan:
    
    ![Skapa och spara. kuvert-fil](media/azure-stack-edge-gpu-deploy-sample-module/create-save-env-file-1.png)

4. Spara filen som *. kuvert* i mappen **SampleSolution**

5. Logga in på Docker genom att ange följande kommando i den integrerade Terminal Studio-koden. 

    ```json
    docker login -u <CONTAINER_REGISTRY_USERNAME> -p <CONTAINER_REGISTRY_PASSWORD> <CONTAINER_REGISTRY_NAME>
    ```
    Gå till avsnittet **åtkomst nycklar** i behållar registret i Azure Portal. Kopiera och Använd register namnet, lösen ordet och inloggnings servern.

    ![Åtkomst nycklar i behållar registret](media/azure-stack-edge-gpu-deploy-sample-module/container-registry-access-keys-1.png)

    När autentiseringsuppgifterna har angetts slutförs inloggningen.

    ![Lyckad inloggning](media/azure-stack-edge-gpu-deploy-sample-module/successful-sign-in-1.png)

6. Push-överför avbildningen till Azure Container Registry. I VS Code-Utforskaren väljer du och högerklickar på **deployment.template.jspå** filen och väljer sedan **Build och push IoT Edge-lösning**. 

    ![Lösning för att bygga och push IoT Edge](media/azure-stack-edge-gpu-deploy-sample-module/build-push-iot-edge-solution-1.png)   

    Om python och python-tillägget inte är installerade installeras de när du skapar och push-överför lösningen. Detta skulle dock resultera i längre versions tider. 

    När det här steget har slutförts visas modulen i behållar registret.

    ![Modul i behållar registret](media/azure-stack-edge-gpu-deploy-sample-module/module-container-registry-1.png)    


7. Om du vill skapa ett distributions manifest högerklickar du på **deployment.template.js** och väljer sedan **skapa IoT Edge distributions manifest**. 

    ![Generera IoT Edge distributions manifest](media/azure-stack-edge-gpu-deploy-sample-module/generate-iot-edge-deployment-manifest-1.png)  

    Meddelandet informerar dig om sökvägen då distributions manifestet genererades. Manifestet är den `deployment.amd64.json` fil som genereras i mappen **config** . 

8. Välj **deployment.amd64.js** filen i mappen **config** och välj sedan **skapa distribution för en enskild enhet**. Använd inte filen **deployment.template.js** . 

    ![Skapa distribution för en enskild enhet](media/azure-stack-edge-gpu-deploy-sample-module/create-deployment-single-device-1.png)  

    I fönstret **utdata** bör du se ett meddelande om att distributionen har slutförts.

    ![Distributionen lyckades i utdata](media/azure-stack-edge-gpu-deploy-sample-module/deployment-succeeded-output-1.png) 

## <a name="monitor-the-module"></a>Övervaka modulen  

1. I kommandopaletten i VS Code kör du **Azure IoT Hub: Select IoT Hub** (Azure IoT Hub: Välj IoT Hub).

2. Välj den prenumeration och IoT-hubb som innehåller den IoT Edge-enhet som du vill konfigurera. I det här fallet väljer du den prenumeration som används för att distribuera Azure Stack Edge Pro-enheten och väljer IoT Edge enheten som skapats för din Azure Stack Edge Pro-enhet. Detta inträffar när du konfigurerar Compute via Azure Portal i föregående steg.

3. I VS Code-Utforskaren expanderar du avsnittet Azure IoT Hub. Under **enheter** bör du se IoT Edge enhet som motsvarar din Azure Stack Edge Pro-enhet. 

    1. Välj den enheten, högerklicka och välj **starta övervakning inbyggd händelse slut punkt**.
  
        ![Starta övervakning](media/azure-stack-edge-gpu-deploy-sample-module/monitor-builtin-event-endpoint-1.png)  

    2. Gå till **enheter > moduler** och se till att din **GPU-modul** körs.

        ![Modul i IoT Hub](media/azure-stack-edge-gpu-deploy-sample-module/module-iot-hub-1.png)  

    3. I VS Code-terminalen bör även IoT Hub händelser visas som övervaknings resultatet för din Azure Stack Edge Pro-enhet.

        ![Övervaka utdata](media/azure-stack-edge-gpu-deploy-sample-module/monitor-events-output-1.png) 

        Du kan se att den tid det tar att köra samma uppsättning åtgärder (5000 iterationer av form Transformation) av GPU är mycket mindre än för CPU.

## <a name="next-steps"></a>Nästa steg

- Läs mer om hur du [konfigurerar GPU för att använda en modul](./azure-stack-edge-gpu-configure-gpu-modules.md).