---
title: Felsök problem med Azure percept-ljud och modulen tal
description: Få fel söknings tips för Azure percept Audio och azureearspeechclientmodule
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: template-how-to
ms.openlocfilehash: c4fc7d7564ecd30326fbec832639b2a81d55e6d5
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105605662"
---
# <a name="azure-percept-audio-and-speech-module-troubleshooting"></a>Fel sökning av Azure percept-ljud och tal-modul

Använd rikt linjerna nedan för att felsöka program problem med röst assistenten.

## <a name="collecting-speech-module-logs"></a>Samlar in talfunktioner loggar

Kör dessa kommandon genom att använda [ssh i dev-paketet](./how-to-ssh-into-percept-dk.md) och ange kommandona i SSH-klientens prompt.

Samla in talfunktioner loggar:

```console
sudo iotedge logs azureearspeechclientmodule
```

Om du vill dirigera om utdata till en. txt-fil för ytterligare analys, använder du följande syntax:

```console
sudo [command] > [file name].txt
```

Ändra behörigheter för txt-filen så att den kan kopieras:

```console
sudo chmod 666 [file name].txt
```

När du har omdirigerat utdata till en txt-fil kopierar du filen till värddatorn via SCP:

```console
scp [remote username]@[IP address]:[remote file path]/[file name].txt [local host file path]
```

[lokal värd fil Sök väg] refererar till den plats på värddatorn som du vill kopiera. txt-filen till. [fjärranvändarnamn] är SSH-användarnamnet som valdes under [installationen](./quickstart-percept-dk-set-up.md).

## <a name="checking-runtime-status-of-the-speech-module"></a>Kontrollerar körnings status för modulen tal

Kontrol lera om körnings statusen för **azureearspeechclientmodule** visas som **körs**. Om du vill hitta körnings status för dina enhets moduler öppnar du [Azure Portal](https://portal.azure.com/) och navigerar till **alla resurser**  ->  **[din IoT Hub]**  ->  **IoT Edge**  ->  **[ditt enhets-ID]**. Klicka på fliken **moduler** om du vill se körnings status för alla installerade moduler.

:::image type="content" source="./media/troubleshoot-audio-accessory-speech-module/over-the-air-iot-edge-device-page.png" alt-text="Edge Device-sidan i Azure Portal.":::

Om körnings statusen för **azureearspeechclientmodule** inte visas i listan som **körs** klickar du på **Ange moduler**  ->  **azureearspeechclientmodule**. På sidan **Modulnamn** anger du **önskad status** för att **köra** och klickar på **Uppdatera**.

## <a name="understanding-ear-som-led-indicators"></a>Förstå öron indikator indikatorer

Du kan använda indikator indikatorer för att förstå vilket tillstånd som enheten är i. Det tar vanligt vis ungefär 2 minuter för modulen att initieras fullständigt när enheten har Aktiver gjort. När du går igenom initierings stegen visas:

1. Centrera vit lampa på (statisk): enheten är påslagen.
2. Centrera vit lampa (blinkar): autentisering pågår.
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
