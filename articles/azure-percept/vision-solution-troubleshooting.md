---
title: Felsöka problem med Azure percept vision och vision-moduler
description: Lär dig mer om fel söknings tips för några av de vanligaste problemen som finns i den visionens prototyp upplevelser för AI
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: e7351079e7325aa7750dc0d10f0923bc847ccc3c
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101664036"
---
# <a name="vision-solution-troubleshooting"></a>Fel sökning av visioner

I följande vägledning hittar du information om fel sökning av lösningar utan kod vision i Azure percept Studio.

## <a name="delete-a-vision-project"></a>Ta bort ett syn projekt

1. Gå till https://www.customvision.ai/projects.

1. Hovra över projektet som du vill ta bort och klicka på pappers korgs ikonen för att ta bort projektet.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-delete-project.png" alt-text="Sidan projekt i Custom Vision med ikonen Ta bort markerat.":::

## <a name="check-which-modules-are-on-a-device"></a>Kontrol lera vilka moduler som finns på en enhet

1. Gå till [Azure-portalen](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home).

1. Klicka på **IoT Hub** -ikonen.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-hub-2-inline.png" alt-text="Azure Portal start sida med IoT Hub-ikonen markerad." lightbox= "./media/vision-solution-troubleshooting/vision-iot-hub-2.png":::

1. Välj IoT Hub som mål enheten är ansluten till.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-hub.png" alt-text="Lista över IoT-hubbar.":::

1. Välj **IoT Edge** och klicka på din enhet under fliken **enhets-ID** .

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-edge.png" alt-text="IoT Edge start sida.":::

1. Dina enhets moduler visas på fliken **moduler** .

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-device-modules-inline.png" alt-text="IoT Edge-sidan för den valda enheten visar fliken moduler." lightbox= "./media/vision-solution-troubleshooting/vision-device-modules.png":::

## <a name="delete-a-device"></a>Ta bort en enhet

1. Gå till [Azure-portalen](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home).

1. Klicka på **IoT Hub** -ikonen.

1. Välj IoT Hub som mål enheten är ansluten till.

1. Välj **IoT Edge** och markera kryss rutan bredvid ditt enhets-ID. Klicka på pappers korgs ikonen för att ta bort enheten.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-delete-device.png" alt-text="Ikonen Ta bort som marker ATS i IoT Edge-startsidan.":::

## <a name="eye-module-troubleshooting-tips"></a>Fel söknings tips för ögon

Om det uppstår ett problem med **WebStreamModule** kontrollerar du att **azureeyemodule**, som gör att inferencing för visioner körs. Om du vill kontrol lera körnings statusen går du till [Azure Portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home) och navigerar till **alla resurser**  ->  **\<your IoT hub>**  ->  **IoT Edge**  ->  **\<your device ID>** . Klicka på fliken **moduler** om du vill se körnings status för alla installerade moduler.

:::image type="content" source="./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page-inline.png" alt-text="Skärmen för körnings status i Device module." lightbox= "./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page.png":::

Om körnings statusen för **azureeyemodule** inte visas i listan som **körs** klickar du på **Ange moduler**  ->  **azureeyemodule**. På sidan **Modulnamn** anger du **önskad status** för att **köra** och klickar på **Uppdatera**.

 :::image type="content" source="./media/vision-solution-troubleshooting/firmware-desired-status-stopped.png" alt-text="Konfigurations skärm för modultyp.":::

## <a name="view-device-rtsp-video-stream"></a>Visa enhets-RTSP video ström

Visa enhetens RTSP-videoström i [Azure percept Studio](./how-to-view-video-stream.md) eller [VLC Media Player](https://www.videolan.org/vlc/index.html).

Öppna RTSP-strömmen i VLC Media Player genom att gå till **Media**  ->  **Open Network Stream**  ->  **RTSP://[enhetens IP-adress]/result**.

## <a name="next-steps"></a>Nästa steg

I den [allmänna fel söknings guiden](./troubleshoot-dev-kit.md) finns mer information om hur du felsöker din Azure percept dk.