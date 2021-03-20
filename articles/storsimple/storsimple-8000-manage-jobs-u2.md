---
title: Visa och hantera jobb för StorSimple 8000-serien | Microsoft Docs
description: Beskriver bladet StorSimple Enhetshanteraren service Jobs och hur du använder det för att spåra senaste, aktuella och schemalagda säkerhets kopierings jobb.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: dc630021cbf93074119d4feedcb3a09fae1453e3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91949436"
---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-jobs-update-3-and-later"></a>Använd tjänsten StorSimple Enhetshanteraren för att visa och hantera jobb (uppdatering 3 och senare)

## <a name="overview"></a>Översikt
Bladet **jobb** innehåller en enda central Portal för att visa och hantera jobb som har startats på enheter som är anslutna till din StorSimple Enhetshanteraren-tjänst. Du kan visa schemalagda, pågående, avslutade, avbrutna och misslyckade jobb för flera enheter. Resultaten visas i tabell format.

![Bladet jobb](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

Du kan snabbt hitta de jobb som du är intresse rad av genom att filtrera efter fält som:

* **Status** – jobb kan pågå, lyckas, avbrytas, Miss lyckas, avbrytas eller lyckas med fel.
* **Tidsintervall** – jobb kan filtreras baserat på datum-och tidsintervallet. Intervallen är de senaste 1 timmarna, de senaste 24 timmarna, senaste 7 dagarna, senaste 30 dagarna, föregående år eller anpassat datum.
* **Typ** – jobb typen kan vara schemalagd säkerhets kopiering, manuell säkerhets kopiering, återställning av säkerhets kopia, klona volymer, redundansväxla volym behållare, skapa lokalt fästa volymer, ändra volym, installera uppdateringar, samla in support loggar och skapa en moln installation.
* **Enheter** – jobb initieras på en viss enhet som är ansluten till din tjänst.
  
De filtrerade jobben tabellen sedan enligt följande attribut:
  
* **Namn** – schemalagd säkerhets kopiering, manuell säkerhets kopiering, återställning av säkerhets kopia, klona volymer, redundansväxla volym behållare, skapa lokalt fäst volym, ändra volym, installera uppdateringar, samla in support loggar eller skapa en moln installation.
* **Status** – körs, slutfört, avbröts, misslyckades, avbröts eller slutfördes med fel.
* **Entitet** – jobben kan associeras med en volym, en säkerhets kopierings princip eller en enhet. Ett klonings jobb är till exempel associerat med en volym, medan ett schemalagt säkerhets kopierings jobb är associerat med en säkerhets kopierings princip. Ett enhets jobb skapas som ett resultat av en haveri beredskap (DR) eller en återställnings åtgärd.
* **Enhet** – namnet på enheten där jobbet startades.
* **Startades** – den tidpunkt då jobbet startades.
* **Duration** – den tid det tar att slutföra jobbet.

Listan över jobb uppdateras var 30: e sekund.

Du kan utföra följande jobb-relaterade åtgärder på den här sidan:

* Visa jobbinformation
* Avbryta ett jobb

## <a name="view-job-details"></a>Visa jobbinformation
Utför följande steg för att visa information om ett jobb.

#### <a name="to-view-job-details"></a>Visa jobb information
1. Gå till StorSimple Enhetshanteraren-tjänsten och klicka sedan på **jobb**.

2. På bladet **jobb** visar du de jobb som du är intresse rad av genom att köra en fråga med lämpliga filter. Du kan söka efter slutförda, pågående eller avbrutna jobb.

    ![Bladet jobb 2](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

2. Välj och klicka på ett jobb.

    ![Jobb blad 3](./media/storsimple-8000-manage-jobs-u2/jobs3.png)

3. På bladet jobb information kan du Visa status, information, tids statistik och data statistik.
   
    ![Information om jobb](./media/storsimple-8000-manage-jobs-u2/jobs4.png)

## <a name="cancel-a-job"></a>Avbryta ett jobb
Utför följande steg för att avbryta ett pågående jobb.

> [!NOTE]
> Vissa jobb, till exempel att ändra volym typ eller expandera en volym, kan inte avbrytas.


### <a name="to-cancel-a-job"></a>Avbryta ett jobb
1. På sidan **jobb** visar du de jobb som körs och som du vill avbryta genom att köra en fråga med lämpliga filter. Välj jobbet.

2. Högerklicka på det valda jobbet för att anropa snabb menyn och klicka på **Avbryt**.

    ![Jobb information 2](./media/storsimple-8000-manage-jobs-u2/jobs2.png)

3. Klicka på **Ja** när du uppmanas att bekräfta åtgärden. Det här jobbet har avbrutits.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [hanterar dina StorSimple säkerhets kopierings principer](storsimple-8000-manage-backup-policies-u2.md).
* Lär dig hur du [använder tjänsten StorSimple Enhetshanteraren för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

