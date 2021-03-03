---
title: Översikt över Azure percept-ljudenhet
description: Läs mer om Azure percept-ljud
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: a63f471498667f58fc80f89323ad93b0feb8bc95
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101664092"
---
# <a name="introduction-to-azure-percept-audio"></a>Introduktion till Azure percept-ljud

Azure percept Audio är en tillbehörs enhet som lägger till tal AI-funktioner i Azure percept DK. Den innehåller en förkonfigurerad rösts AI-Accelerator och en linjär matris med fyra mikrofoner, så att du kan använda anpassade kommandon, nyckelords upptäcka och långt fält tal till lokala lyssnings enheter. Med Azure percept Audio kan enhets tillverkare utöka Azure percept DK-funktioner till nya, smart Voice-aktiverade enheter. Den är integrerad med Azure percept DK, Azure percept Studio och andra Azure Edge Management-tjänster. Den kan köpas på [Microsoft Online Store](https://go.microsoft.com/fwlink/p/?LinkId=2155270).

![Azure percept Audio-enhet.](./media/overview-azure-percept-audio/percept-audio.png)


## <a name="azure-percept-audio-components"></a>Ljud komponenter för Azure percept

Azure percept-ljudet innehåller följande huvud komponenter:

- Produktions klar Azure percept Audio-enhet (som) med fyra mikrofon linjär matris
- Developer Board (innehåller 2x knappar, tre gånger lampor, USB micro och 3,5 mm ljud uttag)
- Nödvändiga kablar: Flex-kabel, USB micro-typ-B till USB-A
- Välkomst kort
- Mekanisk monterings plåt med integrerad 80/20 1010 serie montering


<!---

## How it works

Azure Percept Audio passes the audio input to the Azure Percept DK carrier board in a hybrid edge-cloud manner. Specifically,

- The Azure Percept Audio device: processes the incoming speech input to the clearest format by executing beam forming and echo cancellation befor sending the input to the Azure Percept DK. 
- The Azure Percept DK uses edge processing to perform keyword spotting and then sends the relevant inputs to Azure speech services.
- Cloud: Processing of natural language commands and phrases, in addition to keyword verification and retraining.
- Offline: If the device is offline it will detect the keyword and capture telemetry that there is no internet connection at the time of the command. It will not be able to weed out false accepts since it cannot perform keyword verification.

-->

## <a name="getting-started"></a>Komma igång

- [Sätt samman din Azure percept DK](./quickstart-percept-dk-unboxing.md)
- [Slutför installations upplevelsen för Azure percept DK](./quickstart-percept-dk-set-up.md)
- [Anslut din Azure percept-ljudenhet till din devkit](./quickstart-percept-audio-setup.md)

### <a name="build-a-no-code-prototype"></a>Skapa en prototyp utan kod

Bygg en [tal igenkännings lösning](./tutorial-no-code-speech.md) med Azure percept Voice Assistant för service, hälso vårds-, inventerings-och bil scenarier.

## <a name="additional-technical-information"></a>Ytterligare teknisk information

- [Data blad för Azure percept-ljud](./azure-percept-audio-datasheet.md)

## <a name="next-steps"></a>Nästa steg

Beställ en Azure percept Audio-enhet på [Microsoft Online Store](https://go.microsoft.com/fwlink/p/?LinkId=2155270).