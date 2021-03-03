---
title: Felsöka problem med Azure percept-ljud och tal-moduler
description: Få fel söknings tips för några av de vanligaste problemen som påträffas under den här insikts upplevelsen
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 1e2ad920afb55066f07430568f976154f6d7cae1
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101680130"
---
# <a name="azure-percept-audio-and-speech-module-troubleshooting"></a>Fel sökning av Azure percept-ljud och tal-modul

Använd rikt linjerna nedan för att felsöka program problem med röst assistenten.

## <a name="collecting-speech-module-logs"></a>Samlar in talfunktioner loggar

Om du vill köra dessa kommandon [ansluter du till Azure PERCEPT DK Wi-Fi åtkomst punkten och ansluter till dev-paketet via SSH](./how-to-ssh-into-percept-dk.md) och anger kommandona i SSH-terminalen.

```console
 iotedge logs azureearspeechclientmodule
```

Om du vill omdirigera utdata till en txt-fil för ytterligare analys, använder du följande syntax:

```console
[command] > [file name].txt
```

När du har omdirigerat utdata till en txt-fil kopierar du filen till värddatorn via SCP:

```console
scp [remote username]@[IP address]:[remote file path]/[file name].txt [local host file path]
```

[lokal värd fil Sök väg] refererar till den plats på värddatorn som du vill kopiera. txt-filen till. [fjärranvändarnamn] är SSH-användarnamnet som valdes under [installationen](./quickstart-percept-dk-set-up.md). Om du inte har konfigurerat en SSH-inloggning under OOBE, är ditt fjärranvändarnamn rot.

## <a name="checking-runtime-status-of-the-speech-module"></a>Kontrollerar körnings status för modulen tal

Kontrol lera om körnings statusen för **azureearspeechclientmodule** visas som **körs**. Om du vill hitta körnings statusen för dina enhets moduler öppnar du [Azure Portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home) och navigerar till **alla resurser**  ->  **\<your IoT hub>**  ->  **IoT Edge**  ->  **\<your device ID>** . Klicka på fliken **moduler** om du vill se körnings status för alla installerade moduler.

:::image type="content" source="./media/troubleshoot-audio-accessory-speech-module/over-the-air-iot-edge-device-page.png" alt-text="Edge Device-sidan i Azure Portal.":::

Om körnings statusen för **azureearspeechclientmodule** inte visas i listan som **körs** klickar du på **Ange moduler**  ->  **azureearspeechclientmodule**. På sidan **Modulnamn** anger du **önskad status** för att **köra** och klickar på **Uppdatera**.

:::image type="content" source="./media/troubleshoot-audio-accessory-speech-module/firmware-desired-status-stopped.png" alt-text="Skärmen Ange moduler i Azure Portal.":::

## <a name="understanding-ear-som-led-indicators"></a>Förstå öron indikator indikatorer

Du kan använda indikator indikatorer för att förstå vilket tillstånd som enheten är i. Det tar vanligt vis ungefär 2 minuter för modulen att initiera efter *strömförsörjningen*. När du går igenom initierings stegen visas:

1. 1 vänster grön lampa – enheten är påslagen. 
2. 1 vänster grön ljus-och mitt punkt blinkar grönt-autentisering pågår. 
3. Alla tre lysdioder ändras till blått när enheten har autentiserats och är redo att användas.

|LEDande tillstånd                  |Öron som status            |
|----------------------------|---------------------------|
|1x, grön (vänster LED)         |slå på |
|1x, grön (vänster LED) <br> 1x blinkande grönt (centrerat) |autentisering pågår |
|3x av                      |initiering slutförd |
|3x, blå                     |redo att användas |
|3x blinkar blå            |nyckelordet känns igen |
|3x, racing blå              |bearbetning |
|3x röd                      |mikrofon |

## <a name="next-steps"></a>Nästa steg

I den [allmänna fel söknings guiden](./troubleshoot-dev-kit.md) finns mer information om hur du felsöker din Azure percept dk.