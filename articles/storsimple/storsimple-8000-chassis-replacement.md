---
title: Ersätt chassi på StorSimple 8000-seriens enhet | Microsoft Docs
description: Beskriver hur du tar bort och ersätter chassit för din StorSimple primära inne slutning eller EBOD-hölje.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: de0f6299f35f9d76fdade976bf70456426e5ec51
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85513434"
---
# <a name="replace-the-chassis-on-your-storsimple-device"></a>Ersätt chassit på din StorSimple-enhet
## <a name="overview"></a>Översikt
I den här självstudien beskrivs hur du tar bort och ersätter ett chassi i en StorSimple 8000-serien het. StorSimple 8100-modellen är en enda kabinetten het (ett chassi), medan 8600 är en dubbel enhets enhet (två chassin). För en 8600-modell finns det potentiellt två chassin som kan ha misslyckats i enheten: chassit för den primära inne slutningen eller chassit för EBOD-höljet.

I båda fallen är det ersättnings chassit som levereras av Microsoft tomt. Inga Power-och kylnings moduler (PCMs), Controller-moduler, solid state disk-enheter (SSD), hård disk enheter (HDD) eller EBOD-moduler kommer att ingå.

> [!IMPORTANT]
> Innan du tar bort och ersätter chassit bör du gå igenom säkerhets informationen i [StorSimple för maskin varu komponenter](storsimple-8000-hardware-component-replacement.md).


## <a name="remove-the-chassis"></a>Ta bort chassit
Utför följande steg för att ta bort chassit på din StorSimple-enhet.

#### <a name="to-remove-a-chassis"></a>Ta bort ett chassi
1. Kontrol lera att StorSimple-enheten är avstängd och frånkopplad från alla ström källor.
2. Ta bort alla nätverks-och SAS-kablar, om tillämpligt.
3. Ta bort enheten från racket.
4. Ta bort var och en av enheterna och notera de platser som de tas bort från. Mer information finns i [ta bort disk enheten](storsimple-8000-disk-drive-replacement.md#remove-the-disk-drive).
5. Ta bort EBOD Controller-modulerna på EBOD-kabinettet (om det här är det chassi som misslyckades). Mer information finns i [ta bort en EBOD-kontrollant](storsimple-8000-ebod-controller-replacement.md#remove-an-ebod-controller).
   
    På den primära inne slutningen (om det här är det chassi som misslyckades) tar du bort kontroll enheterna och noterar de platser som de tas bort från. Mer information finns i [ta bort en kontrollant](storsimple-8000-controller-replacement.md#remove-a-controller).

## <a name="install-the-chassis"></a>Installera chassit
Utför följande steg för att installera chassit på din StorSimple-enhet.

#### <a name="to-install-a-chassis"></a>Installera ett chassi
1. Montera chassit i racket. Mer information finns i [rack montering av din StorSimple 8100-enhet](storsimple-8100-hardware-installation.md#rack-mount-your-storsimple-8100-device) eller [rack montering av StorSimple 8600-enheten](storsimple-8600-hardware-installation.md#rack-mount-your-storsimple-8600-device).
2. När chassit är monterat i racket installerar du Controller-modulerna på samma platser som de tidigare installerades i.
3. Installera enheterna på samma platser och platser som de tidigare har installerats i.
   
   > [!NOTE]
   > Vi rekommenderar att du installerar SSD på platserna först och sedan installerar hård diskar.
  
4. När enheten är monterad i racket och komponenterna är installerade ansluter du enheten till rätt ström källor och aktiverar enheten. Mer information finns i [kabel Your StorSimple 8100-enhet](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) eller [kabel Your StorSimple 8600-enhet](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

## <a name="next-steps"></a>Nästa steg
Läs mer om [StorSimple av maskin varu komponenter](storsimple-8000-hardware-component-replacement.md).

