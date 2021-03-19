---
title: StorSimple Snapshot Manager säkerhets kopierings katalog | Microsoft Docs
description: Beskriver hur du använder snapin-modulen StorSimple Snapshot Manager MMC för att visa och hantera säkerhets kopierings katalogen.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 6abdbfd2-22ce-45a5-aa15-38fae4c8f4ec
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 2efc35e65ca1db2b5241e1d3b2798e068880c87e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90055001"
---
# <a name="use-storsimple-snapshot-manager-to-manage-the-backup-catalog"></a>Använda StorSimple Snapshot Manager för att hantera säkerhets kopierings katalogen

## <a name="overview"></a>Översikt
Den primära funktionen i StorSimple Snapshot Manager är att du kan skapa programkonsekventa säkerhets kopior av StorSimple-volymer i form av ögonblicks bilder. Ögonblicks bilder visas sedan i en XML-fil som kallas för en *säkerhets kopierings katalog*. Säkerhets kopierings katalogen ordnar ögonblicks bilderna efter volym gruppen och sedan efter lokal ögonblicks bild eller moln ögonblicks bild.

I den här självstudien beskrivs hur du kan använda noden **Säkerhetskopiera katalog** för att utföra följande uppgifter:

* Återställa en volym
* Klona en volym eller volym grupp
* Ta bort en säkerhets kopia
* Återställa en fil
* Återställa StorSimple Snapshot Manager-databasen

Du kan visa säkerhets kopierings katalogen genom att expandera noden **säkerhets kopierings katalog** i fönstret **omfattning** och sedan expandera volym gruppen.

* Om du klickar på volym gruppens namn visas antalet lokala ögonblicks bilder och moln ögonblicks bilder som är tillgängliga för volym gruppen i **resultat** fönstret. 
* Om du klickar på **lokal ögonblicks bild** eller **moln ögonblicks bild** visar **resultat** fönstret följande information om varje ögonblicks bild av säkerhets kopian (beroende på dina **visnings** inställningar):
  
  * **Namn** – tidpunkten då ögonblicks bilden togs.
  * **Typ** – om detta är en lokal ögonblicks bild eller en ögonblicks bild av molnet.
  * **Owner** – innehålls ägaren. 
  * **Tillgängligt** – om ögonblicks bilden är tillgänglig för närvarande. **True** anger att ögonblicks bilden är tillgänglig och kan återställas. **False** anger att ögonblicks bilden inte längre är tillgänglig. 
  * **Importerad** – om säkerhets kopian har importer ATS. **True** anger att säkerhets kopian har importer ATS från tjänsten StorSimple Enhetshanteraren när enheten konfigurerades i StorSimple Snapshot Manager; **False** anger att den inte har importer ATS, men skapades av StorSimple Snapshot Manager. (Du kan enkelt identifiera en importerad volym grupp eftersom ett suffix läggs till som identifierar den enhet som volym gruppen importerades från.)
    
    ![Säkerhets kopierings katalog](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Backup_catalog.png)
* Om du expanderar en **lokal ögonblicks** bild eller en **ögonblicks bild av molnet** och klickar sedan på ett enskilt namn på ögonblicks bilden visar **resultat** fönstret följande information om den ögonblicks bild som du valde:
  
  * **Namn** – den volym som identifieras av enhets beteckningen. 
  * **Lokalt namn** – enhetens lokala namn (om tillgängligt). 
  * **Enhet** – namnet på den enhet som volymen finns på. 
  * **Tillgängligt** – om ögonblicks bilden är tillgänglig för närvarande. **True** anger att ögonblicks bilden är tillgänglig och kan återställas. **False** anger att ögonblicks bilden inte längre är tillgänglig. 

## <a name="restore-a-volume"></a>Återställa en volym
Använd följande procedur för att återställa en volym från en säkerhets kopia.

#### <a name="prerequisites"></a>Förutsättningar
Om du inte redan har gjort det skapar du en volym och volym grupp och tar sedan bort volymen. StorSimple Snapshot Manager säkerhetskopierar som standard en volym innan den tillåter att den tas bort. Den här försiktighets åtgärden kan förhindra data förlust om volymen tas bort oavsiktligt eller om data behöver återställas av någon anledning. 

StorSimple Snapshot Manager visar följande meddelande när säkerhets kopieringen skapas.

![Automatiskt ögonblicks bild meddelande](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Automatic_snap.png) 

> [!IMPORTANT]
> Du kan inte ta bort en volym som ingår i en volym grupp. Borttagnings alternativet är inte tillgängligt. <br>
> 
> 

#### <a name="to-restore-a-volume"></a>Återställa en volym
1. Klicka på Skriv bords ikonen för att starta StorSimple Snapshot Manager. 
2. I fönstret **omfattning** expanderar du noden **säkerhets kopierings katalog** , expanderar en volym grupp och klickar sedan på **lokala ögonblicks bilder** eller **moln ögonblicks bilder**. En lista över ögonblicks bilder av säkerhets kopior visas i **resultat** fönstret.
3. Leta upp den säkerhets kopia som du vill återställa, högerklicka på och klicka sedan på **Återställ**.
   
    ![Återställa säkerhets kopierings katalog](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_BU_catalog.png) 
4. På sidan bekräftelse granskar du informationen, skriver **Bekräfta** och klickar sedan på **OK**. StorSimple Snapshot Manager använder säkerhets kopian för att återställa volymen.
   
    ![Bekräftelse meddelande för återställning](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_volume_msg.png) 
5. Du kan övervaka återställnings åtgärden när den körs. I fönstret **omfattning** expanderar du noden **jobb** och klickar sedan på **Kör**. Jobb informationen visas i **resultat** fönstret. När återställnings jobbet är klart överförs jobb informationen till listan **senaste 24 timmarna** .

## <a name="clone-a-volume-or-volume-group"></a>Klona en volym eller volym grupp
Använd följande procedur för att skapa en dubblett (klon) av en volym eller volym grupp.

#### <a name="to-clone-a-volume-or-volume-group"></a>Klona en volym eller volym grupp
1. Klicka på Skriv bords ikonen för att starta StorSimple Snapshot Manager.
2. I fönstret **omfattning** expanderar du noden **säkerhets kopierings katalog** , expanderar en volym grupp och klickar sedan på **moln ögonblicks bilder**. En lista över säkerhets kopior visas i **resultat** fönstret.
3. Hitta den volym eller volym grupp som du vill klona, högerklicka på volymen eller volym gruppens namn och klicka på **klona**. Dialog rutan **klona moln ögonblicks bilder** visas.
   
    ![Klona en moln ögonblicks bild](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 
4. Slutför dialog rutan **klona moln ögonblicks bilder** enligt följande: 
   
   1. I text rutan **namn** anger du ett namn för den klonade volymen. Det här namnet visas i noden **volymer** . 
   2. (Valfritt) Välj **enhet** och välj sedan en enhets beteckning i list rutan.
   3. (Valfritt) Välj **mapp (NTFS)** och ange en mappsökväg eller klicka på Bläddra och välj en plats för mappen. 
   4. Klicka på **Skapa**.
5. När klonings processen är färdig måste du initiera den klonade volymen. Starta Serverhanteraren och starta disk hantering. Detaljerade anvisningar finns i [montera volymer](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). När den har initierats visas volymen under noden **volymer** i fönstret **omfång** . Om du inte ser den volym som visas i listan uppdaterar du listan med volymer (Högerklicka på noden **volymer** och klicka sedan på **Uppdatera**).

## <a name="delete-a-backup"></a>Ta bort en säkerhets kopia
Använd följande procedur för att ta bort en ögonblicks bild från säkerhets kopierings katalogen. 

> [!NOTE]
> Om du tar bort en ögonblicks bild tas säkerhetskopierade data som är kopplade till ögonblicks bilden bort. Processen med att rensa data från molnet kan dock ta lite tid.<br>


#### <a name="to-delete-a-backup"></a>Ta bort en säkerhets kopia
1. Klicka på Skriv bords ikonen för att starta StorSimple Snapshot Manager.
2. I fönstret **omfattning** expanderar du noden **säkerhets kopierings katalog** , expanderar en volym grupp och klickar sedan på **lokala ögonblicks bilder** eller **moln ögonblicks bilder**. En lista över ögonblicks bilder visas i **resultat** fönstret.
3. Högerklicka på den ögonblicks bild som du vill ta bort och klicka sedan på **ta bort**.
4. När bekräftelse meddelandet visas klickar du på **OK**.

## <a name="recover-a-file"></a>Återställa en fil
Om en fil tas bort av misstag från en volym, kan du återställa filen genom att hämta en ögonblicks bild som i förväg tar bort den, med hjälp av ögonblicks bilden för att skapa en klon av volymen och sedan kopiera filen från den klonade volymen till den ursprungliga volymen.

#### <a name="prerequisites"></a>Förutsättningar
Innan du börjar ska du kontrol lera att du har en aktuell säkerhets kopia av volym gruppen. Ta sedan bort en fil som lagrats på en av volymerna i den volym gruppen. Använd slutligen följande steg för att återställa den borttagna filen från säkerhets kopian. 

#### <a name="to-recover-a-deleted-file"></a>Återställa en borttagen fil
1. Klicka på ikonen StorSimple Snapshot Manager på Skriv bordet. Fönstret StorSimple Snapshot Manager-konsol visas. 
2. I fönstret **omfattning** expanderar du noden **säkerhets kopierings katalog** och bläddrar till en ögonblicks bild som innehåller den borttagna filen. Normalt bör du välja en ögonblicks bild som skapats precis före borttagningen.
3. Leta upp den volym som du vill klona, högerklicka på och klicka på **klona**. Dialog rutan **klona moln ögonblicks bilder** visas.
   
    ![Klona en moln ögonblicks bild](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 
4. Slutför dialog rutan **klona moln ögonblicks bilder** enligt följande: 
   
   1. I text rutan **namn** anger du ett namn för den klonade volymen. Det här namnet visas i noden **volymer** . 
   2. Valfritt Välj **enhet** och välj sedan en enhets beteckning i list rutan. 
   3. Valfritt Välj **mapp (NTFS)** och ange en mappsökväg eller klicka på **Bläddra** och välj en plats för mappen. 
   4. Klicka på **Skapa**. 
5. När klonings processen är färdig måste du initiera den klonade volymen. Starta Serverhanteraren och starta disk hantering. Detaljerade anvisningar finns i [montera volymer](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). När den har initierats visas volymen under noden **volymer** i fönstret **omfång** . 
   
    Om du inte ser den volym som visas i listan uppdaterar du listan med volymer (Högerklicka på noden **volymer** och klicka sedan på **Uppdatera**).
6. Öppna NTFS-mappen som innehåller den klonade volymen, expandera noden **volymer** och öppna sedan den klonade volymen. Hitta filen som du vill återställa och kopiera den till den primära volymen.
7. När du har återställt filen kan du ta bort NTFS-mappen som innehåller den klonade volymen.

## <a name="restore-the-storsimple-snapshot-manager-database"></a>Återställa StorSimple Snapshot Manager-databasen
Du bör regelbundet säkerhetskopiera StorSimple Snapshot Manager-databasen på värddatorn. Om en katastrof inträffar eller värddatorn Miss lyckas av någon anledning kan du sedan återställa den från säkerhets kopian. Att skapa säkerhets kopian av databasen är en manuell process.

#### <a name="to-back-up-and-restore-the-database"></a>Säkerhetskopiera och återställa databasen
1. Stoppa Microsoft StorSimple Management Service:
   
   1. Starta Serverhanteraren.
   2. På instrument panelen för Serverhanteraren väljer du **tjänster** på **verktyg** -menyn.
   3. I fönstret **tjänster** väljer du **hanterings tjänsten för Microsoft-StorSimple**.
   4. Klicka på **stoppa tjänsten** under **Microsoft StorSimple Management Service** i den högra rutan.
2. På värddatorn bläddrar du till C:\ProgramData\Microsoft\StorSimple\BACatalog. 
   
   > [!NOTE]
   > ProgramData är en dold mapp.
   > 
   > 
3. Hitta XML-filen för katalogen, kopiera filen och lagra kopian på en säker plats eller i molnet. Om värden Miss lyckas kan du använda den här säkerhets kopierings filen för att återställa de säkerhets kopierings principer som du skapade i StorSimple Snapshot Manager.
   
    ![Katalog fil för Azure StorSimple backup](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_bacatalog.png)
4. Starta om Microsoft StorSimple Management-tjänsten: 
   
   1. På instrument panelen för Serverhanteraren väljer du **tjänster** på **verktyg** -menyn.
   2. I fönstret **tjänster** väljer du **hanterings tjänsten för Microsoft-StorSimple**.
   3. Klicka på **starta om tjänsten** under **Microsoft StorSimple Management Service** i den högra rutan.
5. På värddatorn bläddrar du till C:\ProgramData\Microsoft\StorSimple\BACatalog. 
6. Ta bort XML-filen för katalogen och ersätt den med den version av säkerhets kopian som du skapade. 
7. Starta StorSimple Snapshot Manager genom att klicka på ikonen Skriv bords StorSimple Snapshot Manager. 

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om hur du [använder StorSimple Snapshot Manager för att administrera din StorSimple-lösning](storsimple-snapshot-manager-admin.md).
* Läs mer om [StorSimple Snapshot Manager uppgifter och arbets flöden](storsimple-snapshot-manager-admin.md#storsimple-snapshot-manager-tasks-and-workflows).

