---
title: Ändra enhets läge för StorSimple | Microsoft Docs
description: Beskriver enhets lägena i StorSimple och förklarar hur du använder Windows PowerShell för StorSimple för att ändra enhets läget.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: c7b0ea489c1d70ab86d677aad666ea6728fa76b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017091"
---
# <a name="change-the-device-mode-on-your-storsimple-device"></a>Ändra enhets läge på din StorSimple-enhet

Den här artikeln innehåller en kort beskrivning av de olika lägena som din StorSimple-enhet kan använda. Din StorSimple-enhet kan fungera i tre lägen: normal, underhåll och återställning.

När du har läst den här artikeln kan du se:

* Vad StorSimple-enhets lägena är
* Så här avbildar du vilket läge som StorSimple-enheten finns i
* Ändra från normal till underhålls läge och *vice versa*

Ovanstående hanterings aktiviteter kan bara utföras via Windows PowerShell-gränssnittet på din StorSimple-enhet.

## <a name="about-storsimple-device-modes"></a>Om enhets lägen för StorSimple

Din StorSimple-enhet kan användas i normal läge, underhåll eller återställnings läge. Vart och ett av dessa lägen beskrivs kortfattat nedan.

### <a name="normal-mode"></a>Normalt läge

Detta definieras som normalt drift läge för en helt konfigurerad StorSimple-enhet. Enheten bör som standard vara i normal läge.

### <a name="maintenance-mode"></a>Underhållsläge

Ibland kan StorSimple-enheten behöva placeras i underhålls läge. Med det här läget kan du utföra underhåll på enheten och installera störande uppdateringar, till exempel de som är relaterade till inbyggd disk.

Du kan endast försätta systemet i underhålls läge via Windows PowerShell för StorSimple. Alla I/O-begäranden pausas i det här läget. Tjänster som icke-flyktigt RAM-minne (Random Access Memory) eller kluster tjänsten stoppas också. Båda styrenheterna startas om när du anger eller avslutar det här läget. När du avslutar underhålls läget kommer alla tjänster att återupptas och bör vara felfria. Det kan ta några minuter.

> [!NOTE]
> **Underhålls läget stöds endast på en korrekt fungerande enhet. Det finns inte stöd för en enhet där en eller båda styrenheterna inte fungerar.**


### <a name="recovery-mode"></a>Återställnings läge

Återställnings läget kan beskrivas som "Windows fel säkert läge med nätverks stöd". Återställnings läget engagerar Microsoft Support teamet och gör det möjligt för dem att utföra diagnostik i systemet. Det primära målet för återställnings läge är att hämta system loggarna.

Om systemet går in i återställnings läge bör du kontakta Microsoft Support för nästa steg. Mer information finns på [kontakt Microsoft Support](storsimple-8000-contact-microsoft-support.md).

> [!NOTE]
> **Du kan inte placera enheten i återställnings läge. Om enheten är i ett felaktigt tillstånd försöker återställnings läget få enheten i ett tillstånd där Microsoft Support personal kan undersöka den.**

## <a name="determine-storsimple-device-mode"></a>Identifiera StorSimple-enhetsläget

#### <a name="to-determine-the-current-device-mode"></a>Så här fastställer du det aktuella enhets läget

1. Logga in på enhetens serie konsol genom att följa stegen i [använda SparaTillFil för att ansluta till enhetens serie konsol](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. Titta på informations meddelandet i den seriella konsol menyn på enheten. Det här meddelandet anger uttryckligen om enheten är i underhålls läge eller återställnings läge. Om meddelandet inte innehåller någon speciell information som rör system läget är enheten i normalt läge.

## <a name="change-the-storsimple-device-mode"></a>Ändra enhets läget för StorSimple

Du kan placera StorSimple-enheten i underhålls läge (från normalt läge) för att utföra underhåll eller installera uppdatering av underhålls läge. Utför följande procedurer för att ange eller avsluta underhålls läget.

> [!IMPORTANT]
> Innan du går in i underhålls läge kontrollerar du att båda styrenheterna är felfria genom att komma åt **enhets inställningarna > maskin varu hälsa** för enheten i Azure Portal. Om en eller båda kontroll enheterna inte är felfria kontaktar du Microsoft Support för nästa steg. Mer information finns på [kontakt Microsoft Support](storsimple-8000-contact-microsoft-support.md).
 

#### <a name="to-enter-maintenance-mode"></a>Ange underhålls läge

1. Logga in på enhetens serie konsol genom att följa stegen i [använda SparaTillFil för att ansluta till enhetens serie konsol](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. I menyn serie konsol väljer du alternativ 1, **loggar in med fullständig åtkomst**. Ange **enhetens administratörs lösen ord** när du uppmanas att göra det. Standard lösen ordet är: `Password1` .
3. Skriv i kommando tolken 
   
    `Enter-HcsMaintenanceMode`
4. Ett varnings meddelande visas som talar om att underhålls läget avbryter alla I/O-begäranden och ger anslutningen till Azure Portal och du uppmanas att bekräfta. Skriv **Y** för att ange underhålls läge.
5. Båda styrenheterna kommer att startas om. När omstarten är klar visar den seriella konsolens banderoll att enheten är i underhålls läge. Ett exempel på utdata visas nedan.

```
    ---------------------------------------------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0 - Passive
    ---------------------------------------------------------------

    Controller0>Enter-HcsMaintenanceMode
    Checking device state...

    In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
    [Y] Yes [N] No (Default is "Y"): Y

    <BOTH CONTROLLERS RESTART>

    -----------------------MAINTENANCE MODE------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0 - Passive
    ---------------------------------------------------------------

    Serial Console Menu
    [1] Log in with full access
    [2] Log into peer controller with full access
    [3] Connect with limited access
    [4] Change language
    Please enter your choice>

```

#### <a name="to-exit-maintenance-mode"></a>Avsluta underhålls läget

1. Logga in på enhetens serie konsol. Verifiera från det informations meddelande som enheten är i underhålls läge.
2. Skriv följande i kommandotolken:
   
    `Exit-HcsMaintenanceMode`
3. Ett varnings meddelande och ett bekräftelse meddelande visas. Avsluta underhålls läget genom att skriva **Y** .
4. Båda styrenheterna kommer att startas om. När omstarten är klar visar serie konsolens banderoll att enheten är i normalt läge. Ett exempel på utdata visas nedan.

```
    -----------------------MAINTENANCE MODE------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0
    ---------------------------------------------------------------

    Controller0>Exit-HcsMaintenanceMode
    Checking device state...

    Before exiting maintenance mode, ensure that any updates that are required on both controllers have been applied. Failure to install on each controller could result in data corruption. Exiting maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to exit maintenance mode?
    [Y] Yes [N] No (Default is "Y"): Y

    <BOTH CONTROLLERS RESTART>

    ---------------------------------------------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0 - Active
    ---------------------------------------------------------------

    Serial Console Menu
    [1] Log in with full access
    [2] Log into peer controller with full access
    [3] Connect with limited access
    [4] Change language
    Please enter your choice>
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [använder uppdateringar för normala och underhålls läge](storsimple-update-device.md) på din StorSimple-enhet.

