---
title: Visa och hantera StorSimple virtuella mat ris jobb | Microsoft Docs
description: Beskriver sidan StorSimple Enhetshanteraren service Jobs och hur du använder den för att spåra de senaste och aktuella jobben för den virtuella StorSimple-matrisen.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 31879821-b599-4609-a7f4-d4b0f658a933
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 11/11/2016
ms.author: alkohli
ms.openlocfilehash: d806d8a04dc1bd8547808d20c77bfec310f7dd06
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95992987"
---
# <a name="use-the-storsimple-device-manager-service-to-view-jobs-for-the-storsimple-virtual-array"></a>Använd tjänsten StorSimple Enhetshanteraren för att visa jobb för den virtuella StorSimple-matrisen
## <a name="overview"></a>Översikt
Bladet **jobb** innehåller en enda central Portal för att visa och hantera jobb som har startats på virtuella matriser som är anslutna till din StorSimple Enhetshanteraren-tjänst. Du kan visa körnings-, slutförda och misslyckade jobb för flera virtuella enheter. Resultaten visas i tabell format.

![Bladet jobb](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)

Du kan snabbt hitta de jobb som du är intresse rad av genom att filtrera efter fält som:

* **Tidsintervall** – jobb kan filtreras baserat på datum-och tidsintervallet.
* **Enheter** – jobb initieras på en speciell enhet som är ansluten till din tjänst. De filtrerade jobben är sedan tabellen baserat på följande attribut:
  
  * **Namn** – jobb namnet kan vara **alla**, **säkerhetskopiera**, **klona**, **redundansväxla**, **Hämta uppdateringar** eller **Installera uppdateringar**.
  * **Status** – jobb kan vara **alla**, **pågående**, **lyckade** eller **misslyckade** eller **avbrutna**.
  * **Entitet** – jobben kan associeras med en volym, en resurs eller en enhet.
  * **Enhet** – namnet på enheten där jobbet startades.
  * **Startades** – den tidpunkt då jobbet startades.
  * **Varaktighet** – varaktigheten för då jobbet kördes.
* **Status** – du kan söka efter alla, pågående, slutförda eller misslyckade jobb.
* **Jobbtyp** – jobb typen kan vara alla, säkerhets kopiering, återställning, redundans, hämtning av uppdateringar eller installation av uppdateringar.

Listan över jobb uppdateras var 30: e sekund.

## <a name="view-job-details"></a>Visa jobbinformation
Utför följande steg för att visa information om ett jobb.

#### <a name="to-view-job-details"></a>Visa jobb information
1. På bladet **jobb** visar du det eller de jobb som du är intresse rad av genom att köra en fråga med lämpliga filter. Du kan söka efter slutförda eller pågående jobb.
2. Välj ett jobb i tabell listan med jobb.
   
    ![Bladet jobb](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)
3. Klicka på **information** längst ned på sidan.
4. I dialog rutan **information** kan du Visa status, information och tids statistik. Följande bild visar ett exempel på dialog rutan **information om säkerhets kopierings jobb** .
   
    ![Information om jobb](./media/storsimple-virtual-array-manage-jobs/ova-jobs-details.png)

#### <a name="job-failures-when-the-virtual-machine-is-paused-in-the-hypervisor"></a>Jobb haverier när den virtuella datorn har pausats i hypervisorn
När ett jobb pågår på den virtuella StorSimple-matrisen och enheten (virtuell dator som har skapats i hypervisor) pausas i mer än 15 minuter, Miss lyckas jobbet. Detta beror på att den virtuella StorSimple-matrisen inte är synkroniserad med Microsoft Azure tiden. 

Följande fel meddelande visas: "din enhets tid är inte synkroniserad med Microsoft Azure tiden med mer än 15 minuter. Kontrol lera att hypervisorn och enhets tiderna är synkroniserade med en NTP-server. Kontrol lera att det inte finns några anslutnings problem. Du kan felsöka anslutnings problem genom att köra diagnostiska tester från det lokala webb gränssnittet för den virtuella enheten. "

Dessa fel gäller för säkerhets kopierings-, återställnings-, uppdaterings-och redundans-jobb. Om din virtuella dator är etablerad i Hyper-V, synkroniserar datorn slutligen tiden med din hypervisor. När det händer kan du starta om jobbet.

## <a name="next-steps"></a>Nästa steg
[Lär dig hur du använder det lokala webb gränssnittet för att administrera din virtuella StorSimple-matris](storsimple-ova-web-ui-admin.md).

