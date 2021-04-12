---
title: Felsöka problem med Azure percept vision och vision-moduler
description: Lär dig mer om fel söknings tips för några av de vanligaste problemen som finns i den visionens prototyp upplevelser för AI
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/29/2021
ms.custom: template-how-to
ms.openlocfilehash: 78de5ef0ef77a181d4a2da91e4b468db1b47f208
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106074700"
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

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-device-modules-inline.png" alt-text="IoT Edge sidan för den valda enheten som visar fliken moduler." lightbox= "./media/vision-solution-troubleshooting/vision-device-modules.png":::

## <a name="delete-a-device"></a>Ta bort en enhet

1. Gå till [Azure-portalen](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home).

1. Klicka på **IoT Hub** -ikonen.

1. Välj IoT Hub som mål enheten är ansluten till.

1. Välj **IoT Edge** och markera kryss rutan bredvid ditt enhets-ID. Klicka på pappers korgs ikonen för att ta bort enheten.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-delete-device.png" alt-text="Ikonen Ta bort som marker ATS i IoT Edge start sidan.":::

## <a name="eye-module-troubleshooting-tips"></a>Fel söknings tips för ögon

### <a name="check-the-runtime-status-of-azureeyemodule"></a>Kontrol lera körnings status för azureeyemodule

Om det uppstår ett problem med **WebStreamModule** kontrollerar du att **azureeyemodule**, som hanterar visionens inferencing, körs. Om du vill kontrol lera körnings statusen går du till [Azure Portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home) och navigerar till **alla resurser**  ->  **\<your IoT hub>**  ->  **IoT Edge**  ->  **\<your device ID>** . Klicka på fliken **moduler** om du vill se körnings status för alla installerade moduler.

:::image type="content" source="./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page-inline.png" alt-text="Skärmen för körnings status i Device module." lightbox= "./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page.png":::

Om körnings statusen för **azureeyemodule** inte visas i listan som **körs** klickar du på **Ange moduler**  ->  **azureeyemodule**. På sidan **Modulnamn** anger du **önskad status** för att **köra** och klickar på **Uppdatera**.

 :::image type="content" source="./media/vision-solution-troubleshooting/firmware-desired-status-stopped.png" alt-text="Konfigurations skärm för modultyp.":::

### <a name="update-telemetryintervalneuralnetworkms"></a>Uppdatera TelemetryIntervalNeuralNetworkMs

Om du stöter på följande begränsnings fel måste TelemetryIntervalNeuralNetworkMs-värdet i azureeyemodule-modulens dubbla inställningar uppdateras.

|Felmeddelande|
|------|
|Det totala antalet meddelanden på IotHub ' xxxxxxxxx ' överskred den allokerade kvoten. Maximalt antal tillåtna meddelanden: "8000", Aktuellt antal meddelanden: "xxxx". Åtgärder för att skicka och ta emot blockeras för den här hubben fram till nästa UTC-dag. Överväg att öka kvoten genom att öka antalet enheter för den här hubben.|

TelemetryIntervalNeuralNetworkMs anger hur ofta meddelanden ska skickas (i millisekunder) från neurala-nätverket. Azure-prenumerationer har ett begränsat antal meddelanden per dag, beroende på din prenumerations nivå. Om du upptäcker att du har skapat för många meddelanden på egen hand kan du öka den till en högre siffra. 12000 (vilket innebär en gång var 12: e sekund) ger dig en bra runda 7200-meddelanden per dag, vilket ligger under gränsen för 8000-meddelanden för den kostnads fria prenumerationen.

Följ dessa steg för att uppdatera ditt TelemetryIntervalNeuralNetworkMs-värde:

1. Logga in på [Azure Portal](https://ms.portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod#home) och öppna **alla resurser**.

1. På sidan **alla resurser** klickar du på namnet på den IoT Hub som etablerades till din devkit under installationen.

1. Klicka på **IoT Edge** under **Automatisk enhets hantering** på vänster sida av IoT Hub sidan. På sidan IoT Edge enheter letar du reda på enhets-ID: t för din devkit. Klicka på enhets-ID: t för din devkit för att öppna dess IoT Edge enhets sida.

1. Välj **azureeyemodule** under fliken **moduler** .

1. Öppna **modulens identitet** på sidan azureeyemodule.

    :::image type="content" source="./media/vision-solution-troubleshooting/module-page-inline.png" alt-text="Skärm bild av sidan modul." lightbox= "./media/vision-solution-troubleshooting/module-page.png":::

1. Rulla ned till **Egenskaper**. Observera att egenskaperna "körs" och "loggning" inte är aktiva för tillfället.

    :::image type="content" source="./media/vision-solution-troubleshooting/module-identity-twin-inline.png" alt-text="Skärm bild av modulens dubbla egenskaper." lightbox= "./media/vision-solution-troubleshooting/module-identity-twin.png":::

1. Uppdatera **TelemetryIntervalNeuralNetworkMs** -värdet efter behov och klicka på ikonen **Spara** .

## <a name="view-device-rtsp-video-stream"></a>Visa enhets-RTSP video ström

Visa enhetens RTSP-videoström i [Azure percept Studio](./how-to-view-video-stream.md) eller [VLC Media Player](https://www.videolan.org/vlc/index.html).

Öppna RTSP-strömmen i VLC Media Player genom att gå till **Media**  ->  **Open Network Stream**  ->  **RTSP://[enhetens IP-adress]/result**.

## <a name="next-steps"></a>Nästa steg

I den [allmänna fel söknings guiden](./troubleshoot-dev-kit.md) finns mer information om hur du felsöker din Azure percept dk.