---
title: Kom igång med Azure percept-ljud
description: Lär dig hur du ansluter din Azure percept Audio-enhet till din Azure percept DK
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: quickstart
ms.date: 02/18/2021
ms.custom: template-quickstart
ms.openlocfilehash: 575107859f56df742ab41a299269c250511022b3
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101665627"
---
# <a name="azure-percept-audio-setup"></a>Installation av Azure percept-ljud

Azure percept-ljudet fungerar direkt med Azure percept DK. Ingen unik installation krävs.

## <a name="prerequisites"></a>Förutsättningar

- Azure percept DK (devkit)
- Azure percept-ljud
- [Azure-prenumeration](https://azure.microsoft.com/free/)
- [Installations upplevelse för Azure PERCEPT DK](./quickstart-percept-dk-set-up.md): du anslöt din devkit till ett Wi-Fi nätverk, skapat ett IoT Hub och anslöt din devkit till IoT Hub

## <a name="connecting-your-devices"></a>Ansluta dina enheter

1. Anslut Azure percept-ljudenheten till Azure Percepts DK-tavlan med USB micro Type-B till USB-typ – en kabel. Anslut Micro Type-B-kabeln till ljud som och typ A-änden till percept DK-tavlan.

1. Starta devkit.

    - INDIKATORn l01 på ljudet som ändras till solid grönt för att indikera att enheten var påslagen.
    - LEDDE L02 ändras till blinkande grönt för att indikera att ljudet som autentiseras.

1. Vänta tills autentiseringen har slutförts – det kan ta upp till 3 minuter.

1. Du är redo att börja prototyper när du ser något av följande:

    - LAMPAn l01 stängs av och L02 blir vit. Detta anger att autentiseringen är klar och att devkit inte har kon figurer ATS med ett nyckelord än.
    - Alla tre lamporna blir blå. Detta anger att autentiseringen är klar och att devkit har kon figurer ATS med ett nyckelord.

    > [!NOTE]
    > Kontakta support om din devkit inte autentiserar sig.

## <a name="next-steps"></a>Nästa steg

Skapa en [tal lösning utan kod](./tutorial-no-code-speech.md).