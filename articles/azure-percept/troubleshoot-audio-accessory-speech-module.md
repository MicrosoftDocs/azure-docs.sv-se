---
title: Felsök problem med Azure percept-ljud och modulen tal
description: Få fel söknings tips för Azure percept Audio och azureearspeechclientmodule
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: f34013bdb14481bfe872a9b3c4234d603bc2d7ec
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "102635577"
---
# <a name="azure-percept-audio-and-speech-module-troubleshooting"></a>Fel sökning av Azure percept-ljud och tal-modul

Använd rikt linjerna nedan för att felsöka program problem med röst assistenten.

## <a name="collecting-speech-module-logs"></a>Samlar in talfunktioner loggar

Om du vill köra dessa kommandon [ansluter du till Azure PERCEPT DK Wi-Fi åtkomst punkten och ansluter till dev-paketet via SSH](./how-to-ssh-into-percept-dk.md) och anger kommandona i SSH-terminalen.

```console
sudo iotedge logs azureearspeechclientmodule
```

Om du vill omdirigera utdata till en txt-fil för ytterligare analys, använder du följande syntax:

```console
sudo [command] > [file name].txt
```

När du har omdirigerat utdata till en txt-fil kopierar du filen till värddatorn via SCP:

```console
scp [remote username]@[IP address]:[remote file path]/[file name].txt [local host file path]
```

[lokal värd fil Sök väg] refererar till den plats på värddatorn som du vill kopiera. txt-filen till. [fjärran sluten användar namn] är SSH-användarnamnet som väljs under den här [insikts upplevelsen](./quickstart-percept-dk-set-up.md). Om du inte har konfigurerat en SSH-inloggning under den inspelade versionen av Azure percept DK, är ditt fjärranvändarnamn rot.

## <a name="checking-runtime-status-of-the-speech-module"></a>Kontrollerar körnings status för modulen tal

Kontrol lera om körnings statusen för **azureearspeechclientmodule** visas som **körs**. Om du vill hitta körnings statusen för dina enhets moduler öppnar du [Azure Portal](https://portal.azure.com/) och navigerar till **alla resurser**  ->  **\<your IoT hub>**  ->  **IoT Edge**  ->  **\<your device ID>** . Klicka på fliken **moduler** om du vill se körnings status för alla installerade moduler.

:::image type="content" source="./media/troubleshoot-audio-accessory-speech-module/over-the-air-iot-edge-device-page.png" alt-text="Edge Device-sidan i Azure Portal.":::

Om körnings statusen för **azureearspeechclientmodule** inte visas i listan som **körs** klickar du på **Ange moduler**  ->  **azureearspeechclientmodule**. På sidan **Modulnamn** anger du **önskad status** för att **köra** och klickar på **Uppdatera**.

## <a name="understanding-ear-som-led-indicators"></a>Förstå öron indikator indikatorer

Du kan använda indikator indikatorer för att förstå vilket tillstånd som enheten är i. Det tar vanligt vis ungefär 2 minuter för modulen att initiera efter *strömförsörjningen*. När du går igenom initierings stegen visas:

1. 1 centrera vit lampa – enheten är påslagen.
2. 1 mitten vit lampa blinkar-autentisering pågår.
3. Alla tre lysdioder ändras till blått när enheten har autentiserats och är redo att användas.

|SPOLNING|LEDande tillstånd|Öron som status|
|---|---------|--------------|
|L02|1x-vit, statisk på|Slå på |
|L02|1x-vit, 0,5 Hz blinkar|Autentisering pågår |
|L01 & L02 & L03|3x, blå, statisk på|Väntar på nyckelord|
|L01 & L02 & L03|Indikator för blinkande matris, 20fps |Lyssna eller tala|
|L01 & L02 & L03|LED mat ris racing, 20fps|Tänka|
|L01 & L02 & L03|3x röd, statisk på |Mikrofon|

## <a name="next-steps"></a>Nästa steg

I den [allmänna fel söknings guiden](./troubleshoot-dev-kit.md) finns mer information om hur du felsöker din Azure percept dk.
