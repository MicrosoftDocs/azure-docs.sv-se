---
title: Översikt över Azure percept-ljudenhet
description: Läs mer om Azure percept-ljud
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: 85eb4ed0832c9384fcd05154833bc21f83f4adf2
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102426356"
---
# <a name="introduction-to-azure-percept-audio"></a>Introduktion till Azure percept-ljud

Azure percept Audio är en tillbehörs enhet som lägger till tal AI-funktioner i Azure percept DK. Den innehåller en förkonfigurerad ljud processor och en linjär matris med fyra mikrofoner, så att du kan använda röst kommando, nyckelords upptäcka och långt fält tal för lokala lyssnings enheter med Azure Cognitive Services. Azure percept Audio gör det möjligt för enhets tillverkare att utöka Azure percept DK bortom visionens funktioner till nya, smart Voice-aktiverade enheter. Den är integrerad med Azure percept DK, Azure percept Studio och andra Azure Edge Management-tjänster. Den kan köpas på [Microsoft Online Store](https://go.microsoft.com/fwlink/p/?LinkId=2155270).

:::image type="content" source="./media/overview-azure-percept-audio/percept-audio.png" alt-text="Azure percept Audio-enhet.":::

## <a name="azure-percept-audio-components"></a>Ljud komponenter för Azure percept

Azure percept-ljudet innehåller följande huvud komponenter:

- Produktions klar Azure percept Audio-enhet (som) med fyra mikrofon linjär matris och ljud bearbetning som utförs av en XMOS-codec
- Developer-tavlan (innehåller 2x-knappar, 3x-lampor, mikrousb och 3,5 mm-ljuduttag)
- Nödvändiga kablar: FPC-kabel, USB micro typ-B till USB-A
- Välkomst kort
- Mekanisk monterings plåt med integrerad 80/20 1010 serie montering

## <a name="compute-capabilities"></a>Compute-funktioner 

Azure percept-ljud skickar ljud indata via tal stacken som körs på transport örens operatörs kort för Azure percept DK i ett hybrid gräns-moln sätt. Därför kräver Azure percept Audio en operatör med ett operativ system som stöder tal stacken för att kunna utföra. 

Bearbetningen görs på följande sätt: 

- Azure percept-ljud: fångar och konverterar ljudet och skickar det till DK-och audio-uttaget.

- Azure percept DK: tal stacken utför ljuset och avbryter och bearbetar det inkommande ljudet för att optimera för tal. Sedan utförs nyckelordet upptäcka.

- Cloud: bearbetar naturliga språk kommandon och-fraser, nyckelords verifiering och omträning. 

- Offline: om enheten är offline identifierar den nyckelorden och fångar telemetri för Internet anslutnings status. En ökad false-acceptans för nyckelordet upptäcka kan observeras eftersom nyckelords verifiering i molnet inte kan utföras. 

## <a name="getting-started"></a>Komma igång

- [Sätt samman din Azure percept DK](./quickstart-percept-dk-unboxing.md)
- [Slutför installations upplevelsen för Azure percept DK](./quickstart-percept-dk-set-up.md)
- [Anslut din Azure percept-ljudenhet till din devkit](./quickstart-percept-audio-setup.md)

## <a name="build-a-no-code-prototype"></a>Skapa en prototyp utan kod

Bygg en [icke-kodad tal lösning](./tutorial-no-code-speech.md) i [Azure percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) med hjälp av Azure percept Voice Assistant-mallar för service, sjukvård, inventering och bil scenarier.

### <a name="manage-your-no-code-speech-solution"></a>Hantera din tal lösning med ingen kod

- [Konfigurera din röst assistent i IoT Hub](./how-to-manage-voice-assistant.md)
- [Konfigurera din röst assistent i Azure percept Studio](./how-to-configure-voice-assistant.md)
- [Fel sökning av Azure percept-ljud](./troubleshoot-audio-accessory-speech-module.md)

## <a name="additional-technical-information"></a>Ytterligare teknisk information

- [Data blad för Azure percept-ljud](./azure-percept-audio-datasheet.md)
- [Knapp-och LED beteende](./audio-button-led-behavior.md)

## <a name="next-steps"></a>Nästa steg

Beställ en Azure percept Audio-enhet på [Microsoft Online Store](https://go.microsoft.com/fwlink/p/?LinkId=2155270).
