---
title: Kom igång med Azure percept-ljud
description: Lär dig hur du ansluter din Azure percept Audio-enhet till din Azure percept DK
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: quickstart
ms.date: 02/18/2021
ms.custom: template-quickstart
ms.openlocfilehash: 588ebde85b6012ddbfb88ca8305fc735b7a0ba41
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102098000"
---
# <a name="azure-percept-audio-setup"></a>Installation av Azure percept-ljud

Azure percept-ljudet fungerar direkt med Azure percept DK. Ingen unik installation krävs.

## <a name="prerequisites"></a>Förutsättningar

- Azure percept DK (devkit)
- Azure percept-ljud
- [Azure-prenumeration](https://azure.microsoft.com/free/)
- [Installations upplevelse för Azure PERCEPT DK](./quickstart-percept-dk-set-up.md): du anslöt din devkit till ett Wi-Fi nätverk, skapat ett IoT Hub och anslöt din devkit till IoT Hub
- Högtalare eller hörlurar som kan ansluta till 3,5 mm-ljud uttag (valfritt)

## <a name="connecting-your-devices"></a>Ansluta dina enheter

1. Anslut Azure percept-ljudenheten till Azure Percepts DK-tavlan med mikrousb till USB-typ – en kabel. Anslut mikrousb-slutet av kabeln till interattitydr-tavlan (Developer) och typ A-änden till percept DK-tavlan.
1. (Valfritt) Anslut din högtalare eller hörlurar till ditt Azure percept-ljud via ljud uttaget, som är märkt "line ut". Det gör att du kan höra röst assistentens ljud svar. Om du inte ansluter en talare eller hörlurar kommer du fortfarande att kunna se svaren som text i demonstrations fönstret. 

1. Starta devkit. INDIKATORn för L02 på samordnings kortet ändras till blinkande vit för att indikera att enheten var påslagen och att ljudet som autentiseras.

1. Vänta tills autentiseringen har slutförts – det kan ta upp till 3 minuter.

1. Du är redo att börja prototyper när du ser något av följande:

    - LEDDE L02 ändras till heldragen vit. Detta anger att autentiseringen är klar och att devkit inte har kon figurer ATS med ett nyckelord än.
    - Alla tre lamporna blir blå. Detta anger att autentiseringen är klar och att devkit har kon figurer ATS med ett nyckelord.

## <a name="next-steps"></a>Nästa steg

Skapa en [tal lösning utan kod](./tutorial-no-code-speech.md).
