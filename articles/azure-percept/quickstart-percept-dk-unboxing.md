---
title: Avpaketera och montera dina Azure percept DK-komponenter
description: Lär dig att avpaketera, ansluta och sätta igång din Azure percept DK
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: quickstart
ms.date: 02/16/2021
ms.custom: template-quickstart
ms.openlocfilehash: 40751401760d877fe3feab39f3fea1f2fbeee54b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101665624"
---
# <a name="quickstart-unbox-and-assemble-your-azure-percept-dk-components"></a>Snabb start: avpaketera och montera dina Azure percept DK-komponenter

När du har fått din Azure percept DK hänvisar du till den här guiden för information om hur du ansluter komponenterna och slår på enheten.

## <a name="prerequisites"></a>Förutsättningar

- Azure percept DK (devkit)
- P7 Screwdriver (valfritt, används för att skydda ström kabel anslutningen till kortet)

## <a name="unbox-and-assemble-your-device"></a>Avpaketera och sätt samman din enhet

1. Avpaketera för Azure Percepts DK-komponenter.

    Devkit innehåller en transportör, Azure percept vision som, tillbehörs ruta som innehåller antenner och kablar och ett välkomst kort med en hex-nyckel.

1. Anslut devkit-komponenterna.

    > [!NOTE]
    > Strömadapterns port finns på den högra sidan av frakt kortet. De återstående portarna (2x USB-A, 1x USB-C, 1x HDMI och 1x Ethernet) och återställnings knappen finns på vänster sida av frakt tavlan.

    1. Skruva skruven både Wi-Fi antenner i frakt tavlan.

    1. Anslut visionen till kortet USB-C-port med USB-C-kabeln.

    1. Anslut ström kabeln till strömadaptern.

    1. Ta bort eventuella återstående plast förpackningar från enheterna.

    1. Anslut strömadaptern/kabeln till frakt kortet och ett vägguttag. Om du vill skydda elkabelns koppling på ett säkert sätt kan du använda en P7-Screwdriver (som inte ingår i devkit) för att dra in kopplings skruvarna.

    1. När strömförsörjnings kabeln har kopplats till ett vägguttag aktive ras enheten automatiskt. Återställnings knappen till vänster på operatörs kortet kommer att belysas. Vänta lite tills enheten har startats.

        > [!NOTE]
        > Återställnings knappen är för att starta eller återställa enheten när den är ansluten till ett El uttag. I händelse av ett strömavbrott återställs enheten automatiskt och aktive ras igen.

## <a name="next-steps"></a>Nästa steg

Nu när din devkit är ansluten och påslagen kan du läsa mer i genom gången Azure percept DK setup Experience för att slutföra enhets konfigurationen. Med installations miljön kan du ansluta din devkit till ett Wi-Fi nätverk, konfigurera en SSH-inloggning, skapa en IoT Hub och etablera din devkit till ditt Azure-konto. När du har slutfört enhets konfigurationen är du redo att börja skapa prototyper.