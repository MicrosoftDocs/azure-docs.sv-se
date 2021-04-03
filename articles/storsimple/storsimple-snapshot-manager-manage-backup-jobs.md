---
title: StorSimple Snapshot Manager säkerhets kopierings jobb | Microsoft Docs
description: Beskriver hur du använder snapin-modulen StorSimple Snapshot Manager MMC för att visa och hantera schemalagda, pågående och slutförda säkerhets kopierings jobb.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: bf4dcff6-c819-4766-b9d9-9922831cb200
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 3c26a84e32a17cba83b5ca895f146e561072fa62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95998200"
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-backup-jobs"></a>Använd StorSimple Snapshot Manager för att visa och hantera säkerhets kopierings jobb

## <a name="overview"></a>Översikt
Noden **jobb** i fönstret **omfattning** visar de **schemalagda**, **senaste 24 timmarna** och **pågående** säkerhets kopierings aktiviteter som du initierade interaktivt eller av en konfigurerad princip. 

I den här självstudien beskrivs hur du kan använda noden **jobb** för att visa information om schemalagda, senaste och pågående säkerhets kopierings jobb. (Listan över jobb och motsvarande information visas i **resultat** fönstret.) Du kan också högerklicka på ett listat jobb och se en snabb meny som visar tillgängliga åtgärder.

## <a name="view-scheduled-jobs"></a>Visa schemalagda jobb
Använd följande procedur för att Visa schemalagda säkerhets kopierings jobb.

#### <a name="to-view-scheduled-jobs"></a>Visa schemalagda jobb
1. Klicka på Skriv bords ikonen för att starta StorSimple Snapshot Manager. 
2. I fönstret **omfattning** expanderar du noden **jobb** och klickar på **schemalagd**. Följande information visas i **resultat** fönstret:
   
   * **Namn** – namnet på den schemalagda ögonblicks bilden
   * **Nästa körning** – datum och tid för nästa schemalagda ögonblicks bild
   * **Senaste körning** – datum och tid för senaste schemalagda ögonblicks bild
     
     > [!NOTE]
     > Vid enstaka ögonblicks bilder är **nästa körning** och **senaste körning** detsamma.
     
     ![Schemalagda säkerhets kopierings jobb](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_scheduled.png) 
3. Om du vill utföra ytterligare åtgärder för ett särskilt jobb högerklickar du på jobb namnet i **resultat** fönstret och väljer från meny alternativen.

## <a name="view-recent-jobs"></a>Visa senaste jobb
Använd följande procedur för att Visa säkerhets kopierings-och återställnings jobb som har slutförts under de senaste 24 timmarna.

#### <a name="to-view-recent-jobs"></a>Visa senaste jobb
1. Klicka på Skriv bords ikonen för att starta StorSimple Snapshot Manager.
2. Expandera noden **jobb** i fönstret **omfattning** och klicka på de **senaste 24 timmarna**. **Resultat** fönstret visar säkerhets kopierings jobb för de senaste 24 timmarna (till högst 64 jobb). Följande information visas i **resultat** fönstret, beroende på vilka **visnings** alternativ du anger:
   
   * **Namn** – namnet på den schemalagda ögonblicks bilden.
   * **Startade** – datum och tid då ögonblicks bilden började.
   * **Stoppad** – datum och tid då ögonblicks bilden slutfördes eller avslutades.
   * **Förfluten** – tiden mellan **Start** -och **stopp** tiderna.
   * **Status** – tillståndet för det senaste slutförda jobbet. **Lyckades** anger att säkerhets kopian har skapats. **Misslyckades** anger att jobbet inte har körts.
   * **Information** – orsaken till det här problemet.
   * **Bearbetade byte (MB)** – mängden data från volym gruppen som bearbetades (i MB). 
     
     ![Jobb som kördes under de senaste 24 timmarna](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_Last_24_hours.png) 
3. Om du vill utföra ytterligare åtgärder för ett särskilt jobb högerklickar du på jobb namnet i **resultat** fönstret och väljer från meny alternativen.
   
    ![Ta bort ett jobb](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Delete_backup.png)

## <a name="view-currently-running-jobs"></a>Visa jobb som körs för tillfället
Använd följande procedur för att visa jobb som körs för tillfället.

#### <a name="to-view-currently-running-jobs"></a>Visa jobb som körs för tillfället
1. Klicka på Skriv bords ikonen för att starta StorSimple Snapshot Manager.
2. I fönstret **omfattning** expanderar du noden **jobb** och klickar på **Kör**. Beroende på vilka **visnings** alternativ du anger visas följande information i **resultat** fönstret:
   
   * **Namn** – namnet på den schemalagda ögonblicks bilden.
   * **Startade** – datum och tid då ögonblicks bilden började.
   * **Checkpoint** – den aktuella åtgärden för säkerhets kopieringen.
   * **Status** – procent andelen slutfört.
   * **Förfluten** – den tid som har passerat sedan säkerhets kopieringen påbörjades. 
   * **Genomsnittligt data flöde (MB)** – förhållandet mellan totalt antal byte för bearbetade data till den totala tid som tagits för bearbetning (MB).
   * **Bearbetade byte (MB)** – totalt antal byte bearbetade data (i MB).
   * **Skrivna byte (MB)** – totalt antal byte skrivna med data (i MB). Den innehåller både data och metadata och är därför vanligt vis större än de byte som bearbetas.
     
     ![Jobb som körs för närvarande](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_running.png)
3. Om du vill utföra ytterligare åtgärder för ett särskilt jobb högerklickar du på jobb namnet i **resultat** fönstret och väljer från meny alternativen.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [använder StorSimple Snapshot Manager för att administrera din StorSimple-lösning](storsimple-snapshot-manager-admin.md).
* Lär dig hur du [använder StorSimple Snapshot Manager för att hantera säkerhets kopierings katalogen](storsimple-snapshot-manager-manage-backup-catalog.md).

