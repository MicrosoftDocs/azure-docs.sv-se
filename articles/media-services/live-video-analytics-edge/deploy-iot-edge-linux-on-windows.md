---
title: Distribuera till en IoT Edge för Linux på Windows – Azure
description: Den här artikeln innehåller rikt linjer för hur du distribuerar till en IoT Edge för Linux på Windows-enheter.
ms.topic: how-to
ms.date: 02/18/2021
ms.openlocfilehash: 9ec28c62ca804137ede3cd60d1980e55fbaa2807
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102618142"
---
# <a name="deploy-to-an-iot-edge-for-linux-on-windows-eflow-device"></a>Distribuera till en IoT Edge för Linux på Windows-enheten (EFLOW)

I den här artikeln får du lära dig hur du distribuerar direktsända video analyser på en Edge-enhet som har [IoT Edge för Linux på Windows (EFLOW)](https://docs.microsoft.com/azure/iot-edge/iot-edge-for-linux-on-windows). När du har gått igenom stegen i det här dokumentet kommer du att kunna köra ett [medie diagram](media-graph-concept.md) som identifierar rörelser i en video och avger sådana händelser till IoT Hub i molnet. Du kan sedan växla ut medie grafen för avancerade scenarier och ta kraften i direktsända video analyser till din Windows-baserade IoT Edge-enhet.

## <a name="prerequisites"></a>Förutsättningar 

* Ett Azure-konto som har en aktiv prenumeration. [Skapa ett konto utan kostnad](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) om du inte redan har ett.

    > [!NOTE]
    > Du behöver en Azure-prenumeration med behörigheter för att skapa tjänstens huvud namn (**ägar rollen** ger detta). Om du inte har rätt behörigheter kan du kontakta konto administratören för att ge dig rätt behörigheter.
* [Visual Studio Code](https://code.visualstudio.com/) på din utvecklings dator. Kontrol lera att du har [tillägget Azure IoT-verktyg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* Läs [Vad är EFLOW](https://aka.ms/AzEFLOW-docs).

## <a name="deployment-steps"></a>Distributionssteg

Följande visar det övergripande flödet av dokumentet och i fem enkla steg som du bör vara konfigurerad för att köra real tids analys på en Windows-enhet som har EFLOW:

:::image type="content" source="./media/deploy-iot-edge-linux-on-windows/eflow.png" alt-text="IoT Edge för Linux på Windows-diagram (EFLOW)":::

1. [Installera EFLOW](https://aka.ms/AzEFLOW-install) på din Windows-enhet. 

    1. Om du använder Windows-datorn kommer du att se en lokal värd anslutning som representerar den dator där du kör Windows administrations Center på Start sidan i [Windows administrations Center](https://docs.microsoft.com/windows-server/manage/windows-admin-center/overview) under listan över anslutningar. 
    1. Eventuella ytterligare servrar, datorer eller kluster som du hanterar visas också här.
    1. Du kan använda Windows administrations Center för att installera och hantera Azure-EFLOW på en lokal enhet eller fjärrstyrda enheter. I den här hand boken hanteras lokal värd anslutning som mål enhet för distribution av Azure IoT Edge för Linux i Windows. Därför visas även localhost som en IoT Edge enhet.

    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/windows-admin-center.png" alt-text="Steg i distributioner – Windows administrations Center":::
1. Klicka på den IoT Edge enheten för att ansluta till den och du bör se fliken Översikt och fliken kommando gränssnitt. På fliken kommando tolk kan du skicka kommandon till din Edge-enhet.
 
    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/azure-iot-edge-manager.png" alt-text="Distributions steg – Azure IoT Edge Manager":::
1. Gå till kommando tolken och skriv följande kommando:
    
    ```bash
    bash -c "$(curl -sL https://aka.ms/lva-edge/prep_device)"
    ```

    Video analys modulen i real tid körs på gräns enheten med icke-privilegierade [lokala användar konton](deploy-iot-edge-device.md#create-and-use-local-user-account-for-deployment). Dessutom behöver den [vissa lokala mappar](deploy-iot-edge-device.md#granting-permissions-to-device-storage) för att lagra program konfigurations data. Slutligen, för den här instruktions guiden, använder vi en [RTSP-Simulator](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) som vidarebefordrar ett video flöde i real tid till lva-modulen för analys. Den här simulatorn tar som inspelade videofiler i inspelade kataloger från en indatafil. 
    
    PREPARE-Device-skriptet som används ovan automatiserar dessa uppgifter, så att du kan köra ett kommando och ha alla relevanta indata och konfigurationsfiler, videofiler och användar konton med privilegier som skapats sömlöst. När kommandot har slutförts bör du se följande mappar som skapats på din Edge-enhet. 
    
    * `/home/lvaedgeuser/samples`
    * `/home/lvaedgeuser/samples/input`
    * `/var/lib/azuremediaservices`
    * `/var/media`
    
    Anteckna videofilerna (*. mkv) i mappen/Home/lvaedgeuser/samples/input som fungerar som indatafiler som ska analyseras. 
    
    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/azure-iot-edge-manager-analysis.png" alt-text="Analys":::
1. Nu när Edge-enheten har kon figurer ATS, registrerats på hubben och fungerar med rätt mappstruktur, är nästa steg att konfigurera följande ytterligare Azure-resurser och distribuera LVA-modulen. 

    * Lagringskonto
    * Azure Media Services konto

    För detta rekommenderar vi att du använder [installations skriptet Live Video Analytics-resurser](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) för att distribuera de nödvändiga resurserna i din Azure-prenumeration. Det gör du på följande sätt:

    1. Öppna [Azure Cloud Shell](https://ms.portal.azure.com/#cloudshell/).

        :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/azure-resources-cloud-shell.png" alt-text="Cloud Shell":::
    1. Om du använder Cloud Shell för första gången uppmanas du att välja en prenumeration för att skapa ett lagrings konto och en Microsoft Azure fil resurs. Välj **skapa lagring** för att skapa ett lagrings konto för din Cloud Shell sessionsinformation. Det här lagrings kontot är skilt från det konto som skriptet kommer att skapa för att använda med ditt Azure Media Services-konto.
    1. I den nedrullningsbara menyn på vänster sida av Cloud Shells fönstret väljer du bash som din miljö.

        :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/bash.png" alt-text="Bash-miljö":::
    1. Kör följande kommando.

        ```bash
        bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
        ```
        
        Se till att välja **Y** när du uppmanas att välja en egen gräns enhet som en IoT Edge enhet sedan du skapade både enheten och IoT Hub tidigare. Du kommer också att uppmanas att ange ditt IoT Hub namn och IoT Edge enhets-ID. Du kan få båda genom att logga in på Azure Portal och klicka på din IoT Hub och sedan gå till alternativet IoT Edge på bladet IoT Hub Portal.

        :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/iot-edge-devices.png" alt-text="Se IoT Edge enheter":::

    När du är färdig kan du logga tillbaka till IoT Edge enhetens kommando gränssnitt och köra följande kommando.
    
    `sudo iotedge list`
    
    Du bör se följande fyra moduler som distribueras och körs på din Edge-enhet. Observera att skriptet för att skapa resurser distribuerar LVA-modulen tillsammans med IoT Edge modules (edgeAgent och edgeHub) och en RTSP Simulator-modul för att tillhandahålla den simulerade RTSP-videofeeden.
    
    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/running.png" alt-text="Se status":::
1. När modulerna har distribuerats och kon figurer ATS är du redo att köra ditt första LVA-medie diagram på EFLOW. Du kan köra ett enkelt rörelse identifierings diagram som nedan och visualisera resultaten genom att köra följande steg:

    :::image type="content" source="./media/analyze-live-video/motion-detection.svg" alt-text="Diagram över rörelse identifiering":::

    1. [Konfigurera](get-started-detect-motion-emit-events-quickstart.md#configure-the-azure-iot-tools-extension) tillägget Azure IoT-verktyg.
    
        > [!Note]
        > Använd följande sökväg: `~/clouddrive/lva_byod/appsettings.json. - instead of ~/clouddrive/lva-sample/appsettings.json` .
    1. Ange topologin, instansiera en graf och aktivera den via dessa [direkta metod anrop](get-started-detect-motion-emit-events-quickstart.md#use-direct-method-calls).
    1. [Observera resultaten](get-started-detect-motion-emit-events-quickstart.md#observe-results) på hubben.
    1. Anropa [rensnings metoder](get-started-detect-motion-emit-events-quickstart.md#invoke-graphinstancedeactivate).
    1. Ta bort dina resurser om de inte behövs.

        > [!IMPORTANT]
        > Resurser som inte har tagits bort kan fortfarande vara aktiva och debiteras Azure-kostnader.
    
## <a name="next-steps"></a>Nästa steg

* Testa rörelse identifiering tillsammans med att spela in relevanta videor i molnet. Följ stegen från avsnittet [identifiera rörelse, spela in videoklipp för att Media Services](detect-motion-record-video-clips-media-services-quickstart.md#review-the-sample-video) snabb start.
* Kör [AI på live video](use-your-model-quickstart.md#overview) (du kan hoppa över den nödvändiga installationen eftersom den redan har gjorts ovan)
* Använd vårt [vs Code-tillägg](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.live-video-analytics-edge) för att visa fler medie diagram.
* Använd en [IP-kamera](https://en.wikipedia.org/wiki/IP_camera)  som stöder RTSP i stället för att använda RTSP-simulatorn. Du hittar IP-kameror som har stöd för RTSP på sidan [ONVIF-produkter](https://www.onvif.org/conformant-products/) . Sök efter enheter som uppfyller profilerna G, S eller T.

