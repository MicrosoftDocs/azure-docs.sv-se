---
title: Konfigurera Azure percept-ljud
description: Lär dig hur du ansluter din Azure percept Audio-enhet till din Azure percept DK
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: quickstart
ms.date: 03/25/2021
ms.openlocfilehash: fa3dad8cdd38e6db621d8194cc9472430c7c5008
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105605798"
---
# <a name="azure-percept-audio-setup"></a>Installation av Azure percept-ljud

Azure percept-ljudet fungerar direkt med Azure percept DK. Ingen unik installation krävs.

## <a name="prerequisites"></a>Förutsättningar

- Azure percept DK (devkit)
- Azure percept-ljud
- [Azure-prenumeration](https://azure.microsoft.com/free/)
- [Installations upplevelse för Azure PERCEPT DK](./quickstart-percept-dk-set-up.md): du anslöt din devkit till ett Wi-Fi nätverk, skapat ett IoT Hub och anslöt din devkit till IoT Hub
- Högtalare eller hörlurar som kan ansluta till ett 3,5 mm-ljud uttag (valfritt)

## <a name="connecting-your-devices"></a>Ansluta dina enheter

1. Anslut Azure percept-ljudenheten till Azure Percepts DK-tavlan med mikrousb till USB-typ – en kabel. Anslut mikrousb-slutet av kabeln till ljud-och videoplans tavlan (Developer) och typ A-änden till percept DK-tavlan.

1. (Valfritt) Anslut din högtalare eller hörlurar till din Azure percept-ljudenhet via ljud uttaget, som är märkt "line ut". Det gör att du kan höra ljud svar.

1. Starta devkit. LAMPAn L02 på ljudets överliggande panel ändras till blinkande vit för att indikera att enheten var påslagen och att ljudet som autentiserar sig.

1. Vänta tills autentiseringen har slutförts – det kan ta upp till 3 minuter.

1. Du är redo att börja prototyper när du ser något av följande:

    - INDIKATORn L02 ändras till heldragen vit: Detta visar att autentiseringen är klar och att devkit inte har kon figurer ATS med ett nyckelord än.
    - Alla tre lamporna blir blå: det betyder att autentiseringen är klar och devkit har kon figurer ATS med ett nyckelord.

## <a name="next-steps"></a>Nästa steg

Skapa en [tal lösning utan kod](./tutorial-no-code-speech.md) i [Azure percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819).
