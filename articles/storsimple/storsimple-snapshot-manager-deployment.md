---
title: Distribuera StorSimple Snapshot Manager | Microsoft Docs
description: Lär dig hur du hämtar och installerar StorSimple-Snapshot Manager, en MMC-snapin-modul för hantering av StorSimple-funktioner för data skydd och säkerhets kopiering.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: f0128f57-519e-49ec-9187-23575809cdbe
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 5f94564c5e769d3cf0e0abbe92a309a1ee2117a1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96003879"
---
# <a name="deploy-the-storsimple-snapshot-manager-mmc-snap-in"></a>Distribuera snapin-modulen StorSimple Snapshot Manager MMC

## <a name="overview"></a>Översikt
StorSimple Snapshot Manager är en snapin-modul för Microsoft Management Console (MMC) som fören klar data skydd och säkerhets kopierings hantering i en Microsoft Azure StorSimple miljö. Med StorSimple Snapshot Manager kan du hantera Microsoft Azure StorSimple lokalt och i molnet lagrings utrymme som om det vore ett fullständigt integrerat lagrings system, vilket fören klar säkerhets kopierings-och återställnings processer och minskar kostnaderna. 

I den här självstudien beskrivs konfigurations krav samt procedurer för att installera, ta bort och uppgradera StorSimple Snapshot Manager.

> [!NOTE]
> * Du kan inte använda StorSimple Snapshot Manager för att hantera Microsoft Azure StorSimple virtuella matriser (även kallade StorSimple lokala virtuella enheter).
> * Om du planerar att installera StorSimple uppdatering 2 på din StorSimple-enhet måste du ladda ned den senaste versionen av StorSimple Snapshot Manager och installera den **innan du installerar StorSimple uppdatering 2**. Den senaste versionen av StorSimple Snapshot Manager är bakåtkompatibel och fungerar med alla utgivna versioner av Microsoft Azure StorSimple. Om du använder den tidigare versionen av StorSimple-Snapshot Manager måste du uppdatera den (du behöver inte avinstallera den tidigare versionen innan du installerar den nya versionen).


## <a name="storsimple-snapshot-manager-installation"></a>StorSimple Snapshot Manager installation
StorSimple Snapshot Manager kan installeras på datorer som kör operativ systemet Windows Server 2008 R2 SP1, Windows Server 2012 eller Windows Server 2012 R2. På servrar som kör Windows 2008 R2 måste du också installera Windows Server 2008 SP1 och Windows Management Framework 3,0.

Innan du installerar eller uppgraderar snapin-modulen StorSimple Snapshot Manager för Microsoft Management Console (MMC) kontrollerar du att Microsoft Azure StorSimple enheten och värd servern är korrekt konfigurerade.

## <a name="configure-prerequisites"></a>Konfigurera krav
Följande steg ger en översikt över konfigurations åtgärder som du måste utföra innan du installerar StorSimple-Snapshot Manager. Fullständig Microsoft Azure StorSimple konfigurations-och installations information, inklusive system krav och stegvisa anvisningar finns i [distribuera din lokala StorSimple-enhet](storsimple-8000-deployment-walkthrough-u2.md).

> [!IMPORTANT]
> Innan du börjar granskar du [Check lista för distributions konfiguration](storsimple-8000-deployment-walkthrough-u2.md#deployment-configuration-checklist) och och [distributions krav](storsimple-8000-deployment-walkthrough-u2.md#deployment-prerequisites) i [distribuera din lokala StorSimple-enhet](storsimple-8000-deployment-walkthrough-u2.md).
> <br>
> 
> 

### <a name="before-you-install-storsimple-snapshot-manager"></a>Innan du installerar StorSimple Snapshot Manager
1. Packa upp, montera och Anslut Microsoft Azure StorSimple-enheten enligt beskrivningen i [installera din StorSimple 8100-enhet](storsimple-8100-hardware-installation.md) eller [installera din StorSimple 8600-enhet](storsimple-8600-hardware-installation.md).
2. Kontrol lera att värddatorn kör något av följande operativ system:
   
   * Windows Server 2008 R2 (på servrar som kör Windows 2008 R2 måste du också installera Windows Server 2008 SP1 och Windows Management Framework 3,0)
   * Windows Server 2012
   * Windows Server 2012 R2
     
     För en virtuell StorSimple-enhet måste värden vara en Microsoft Azure virtuell dator.
3. Se till att du uppfyller alla Microsoft Azure StorSimple konfigurations krav. Mer information finns i [krav för distribution](storsimple-8000-deployment-walkthrough-u2.md#deployment-prerequisites).
4. Anslut enheten till värden och utför den inledande konfigurationen. Mer information finns i [distributions steg](storsimple-8000-deployment-walkthrough-u2.md#deployment-steps).
5. Skapa volymer på enheten, tilldela dem till värden och kontrol lera att värden kan monteras och använda dem. StorSimple Snapshot Manager stöder följande typer av volymer:
   
   * Standard volymer
   * Enkla volymer
   * Dynamiska volymer
   * Speglade dynamiska volymer (RAID 1)
   * Klusterdelade volymer
     
     Information om hur du skapar volymer på StorSimple-enheten eller den virtuella StorSimple-enheten finns i [steg 6: skapa en volym](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume), i [distribuera din lokala StorSimple-enhet](storsimple-8000-deployment-walkthrough-u2.md).

## <a name="install-a-new-storsimple-snapshot-manager"></a>Installera en ny StorSimple-Snapshot Manager
Innan du installerar StorSimple Snapshot Manager ska du kontrol lera att volymerna som du har skapat på StorSimple-enheten eller StorSimple virtuella enhet monteras, initieras och formateras enligt beskrivningen i [Konfigurera förutsättningar](#configure-prerequisites).

> [!IMPORTANT]
> * För en virtuell StorSimple-enhet måste värden vara en Microsoft Azure virtuell dator. 
> * Värden måste köra Windows 2008 R2, Windows Server 2012 eller Windows Server 2012 R2. Om servern kör Windows Server 2008 R2 måste du också installera Windows Server 2008 SP1 och Windows Management Framework 3,0.
> * Du måste konfigurera en iSCSI-anslutning från värden till StorSimple-enheten innan du kan ansluta enheten till StorSimple Snapshot Manager.

Följ dessa steg för att slutföra en ny installation av StorSimple Snapshot Manager. Om du installerar en uppgradering går du till [uppgradera eller installera om StorSimple Snapshot Manager](#upgrade-or-reinstall-storsimple-snapshot-manager).

* Steg 1: installera StorSimple Snapshot Manager 
* Steg 2: Anslut StorSimple Snapshot Manager till en enhet 
* Steg 3: kontrol lera anslutningen till enheten 

### <a name="step-1-install-storsimple-snapshot-manager"></a>Steg 1: installera StorSimple Snapshot Manager
Använd följande steg för att installera StorSimple Snapshot Manager.

#### <a name="to-install-storsimple-snapshot-manager"></a>Så här installerar du StorSimple Snapshot Manager
1. Hämta StorSimple Snapshot Manager Software (gå till [StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220) i Microsoft Download Center) och spara det lokalt på värden.
2. I Utforskaren högerklickar du på den komprimerade mappen och klickar sedan på **extrahera alla**.
3. I fönstret **extrahera komprimerade (zippade) mappar** i rutan **Välj ett mål och extrahera filer** skriver eller bläddrar du till den sökväg där du vill att filen ska extraheras.
   
    > [!IMPORTANT]
    > Du måste installera StorSimple Snapshot Manager på enhet C:.
    
4. Markera kryss rutan **Visa extraherade filer när du är klar** och klicka sedan på **extrahera**.
   
    ![Dialog rutan Extrahera filer](./media/storsimple-snapshot-manager-deployment/HCS_SSM_extract_files.png) 
5. När extraheringen är färdig öppnas målmappen. Dubbelklicka på ikonen för program installationen som visas i målmappen.
6. När meddelandet **installationen har slutförts** visas klickar du på **Stäng**. Du bör se ikonen StorSimple Snapshot Manager på Skriv bordet.
   
    ![Skriv bords ikon](./media/storsimple-snapshot-manager-deployment/HCS_SSM_desktop_icon.png) 

### <a name="step-2-connect-storsimple-snapshot-manager-to-a-device"></a>Steg 2: Anslut StorSimple Snapshot Manager till en enhet
Använd följande steg för att ansluta StorSimple-Snapshot Manager till en StorSimple-enhet.

#### <a name="to-connect-storsimple-snapshot-manager-to-a-device"></a>Ansluta StorSimple-Snapshot Manager till en enhet
1. Klicka på ikonen StorSimple Snapshot Manager på Skriv bordet. Fönstret StorSimple Snapshot Manager visas. Fönstret innehåller ett **definitions** fönster, ett **resultat** fönster och ett **Åtgärds** fönster. 
   
    ![StorSimple Snapshot Manager användar gränssnitt](./media/storsimple-snapshot-manager-deployment/HCS_SSM_gui_panes.png)
   
   * Fönstret **omfattning** (det vänstra fönstret) innehåller en lista över noder som är ordnade i en träd struktur. Du kan expandera vissa noder för att välja en vy eller vissa data som är relaterade till noden. Klicka på pilen för att expandera eller komprimera en nod. Högerklicka på ett objekt i rutan **omfattning** om du vill se en lista över tillgängliga åtgärder för objektet.
   * **Resultat** fönstret (fönstret i mitten) innehåller detaljerad statusinformation om den nod, vy eller de data som du har valt i fönstret **omfång** .
   * I fönstret **åtgärder** visas de åtgärder som du kan utföra på den nod, vy eller data som du har valt i fönstret **omfång** .
     
     En fullständig beskrivning av StorSimple Snapshot Manager User Interface finns i [StorSimple Snapshot Manager User Interface](storsimple-use-snapshot-manager.md).
2. I fönstret **omfattning** högerklickar du på noden **enheter** och klickar sedan på **Konfigurera en enhet**. Dialog rutan **Konfigurera en enhet** visas.
   
    ![Konfigurera en enhet](./media/storsimple-snapshot-manager-deployment/HCS_SSM_config_device.png) 
3. I list rutan **enhet** väljer du IP-adressen för Microsoft Azure StorSimple enhet eller virtuell enhet. I text rutan **lösen ord** skriver du StorSimple Snapshot Manager lösen ordet som du skapade för enheten i Azure Portal. Klicka på **OK**.
4. StorSimple Snapshot Manager söker efter den enhet som du har identifierat. Om enheten är tillgänglig StorSimple Snapshot Manager lägga till en anslutning. Du kan [kontrol lera anslutningen till enheten](#to-verify-the-connection) för att bekräfta att anslutningen har lagts till.
   
    Om enheten inte är tillgänglig av någon anledning returnerar StorSimple Snapshot Manager ett fel meddelande. Stäng fel meddelandet genom att klicka på **OK** och klicka sedan på **Avbryt** för att stänga dialog rutan **Konfigurera en enhet** .
5. När den ansluter till en enhet importerar StorSimple Snapshot Manager varje volym grupp som kon figurer ATS för enheten, förutsatt att volym gruppen har associerade säkerhets kopior. Volym grupper som inte har tillhör ande säkerhets kopior importeras inte. Dessutom importeras inte säkerhets kopierings principer som har skapats för en volym grupp. Om du vill se de importerade grupperna högerklickar du på noden översta **volym grupper** i fönstret **omfattning** och klickar på **Växla importerade grupper**.

### <a name="step-3-verify-the-connection-to-the-device"></a>Steg 3: kontrol lera anslutningen till enheten
Använd följande steg för att kontrol lera att StorSimple Snapshot Manager är ansluten till StorSimple-enheten.

#### <a name="to-verify-the-connection"></a>Så här kontrollerar du anslutningen
1. I fönstret **omfattning** klickar du på noden **enheter** .
   
    ![StorSimple Snapshot Manager enhets status](./media/storsimple-snapshot-manager-deployment/HCS_SSM_Device_status.png) 
2. Kontrol lera **resultat** fönstret: 
   
   * Om en grön indikator visas på enhets ikonen och **tillgänglig** visas i kolumnen **status** , är enheten ansluten. 
   * Om en röd indikator visas på enhets ikonen och inte är tillgänglig visas den i kolumnen **status** . enheten är inte ansluten. 
   * Om **uppdateringen** visas i kolumnen **status** , hämtar StorSimple Snapshot Manager volym grupper och tillhör ande säkerhets kopior för en ansluten enhet.

## <a name="upgrade-or-reinstall-storsimple-snapshot-manager"></a>Uppgradera eller installera om StorSimple Snapshot Manager
Du bör avinstallera StorSimple Snapshot Manager helt innan du uppgraderar eller installerar om program varan. 

Innan du installerar om StorSimple Snapshot Manager säkerhetskopierar du den befintliga StorSimple Snapshot Manager-databasen på värddatorn. Detta sparar säkerhets kopierings principer och konfigurations information så att du enkelt kan återställa dessa data från säkerhets kopian.

Följ dessa steg om du uppgraderar eller ominstallerar StorSimple Snapshot Manager:

* Steg 1: Avinstallera StorSimple Snapshot Manager 
* Steg 2: säkerhetskopiera StorSimple Snapshot Manager-databasen 
* Steg 3: installera om StorSimple Snapshot Manager och Återställ databasen 

### <a name="step-1-uninstall-storsimple-snapshot-manager"></a>Steg 1: Avinstallera StorSimple Snapshot Manager
Använd följande steg för att avinstallera StorSimple Snapshot Manager.

#### <a name="to-uninstall-storsimple-snapshot-manager"></a>Så här avinstallerar du StorSimple Snapshot Manager
1. Öppna **kontroll panelen** på värddatorn, klicka på **program** och klicka sedan på **program och funktioner**.
2. Klicka på **Avinstallera eller ändra ett program** i det vänstra fönstret.
3. Högerklicka på **StorSimple Snapshot Manager** och klicka sedan på **Avinstallera**.
4. Detta startar installations programmet för StorSimple-Snapshot Manager. Klicka på **ändra installations programmet** och klicka sedan på **Avinstallera**.
   
   > [!NOTE]
   > Om det finns MMC-processer som körs i bakgrunden, t. ex. StorSimple Snapshot Manager eller disk hantering, Miss lyckas avinstallationen och du får ett meddelande om att stänga alla instanser av MMC innan du försöker avinstallera programmet. Välj **Stäng program automatiskt och försök att starta om dem när installationen är klar** och klicka sedan på **OK**.
   > 
   > 
5. När avinstallationen är klar visas ett meddelande om att **installationen har slutförts** . Klicka på **Stäng**.

### <a name="step-2-back-up-the-storsimple-snapshot-manager-database"></a>Steg 2: säkerhetskopiera StorSimple Snapshot Manager-databasen
Använd följande steg för att skapa och spara en kopia av StorSimple Snapshot Manager Database.

#### <a name="to-back-up-the-database"></a>Säkerhetskopiera databasen
1. Stoppa Microsoft StorSimple Management Service:
   
   1. Starta Serverhanteraren.
   2. På instrument panelen för Serverhanteraren väljer du **tjänster** på **verktyg** -menyn.
   3. På sidan **tjänster** väljer du **Microsoft StorSimple Management Service**.
   4. Klicka på **stoppa tjänsten** under **Microsoft StorSimple Management Service** i den högra rutan.
      
        ![Stoppa tjänsten StorSimple Enhetshanteraren](./media/storsimple-snapshot-manager-deployment/HCS_SSM_stop_service.png)
2. Bläddra till C:\ProgramData\Microsoft\StorSimple\BACatalog. 
   
   > [!NOTE]
   > ProgramData är en dold mapp.
  
3. Hitta XML-filen för katalogen, kopiera filen och lagra kopian på en säker plats eller i molnet.
   
    ![StorSimple säkerhets kopierings katalog fil](./media/storsimple-snapshot-manager-deployment/HCS_SSM_bacatalog.png)
4. Starta om Microsoft StorSimple Management-tjänsten: 
   
   1. På instrument panelen för Serverhanteraren väljer du **tjänster** på **verktyg** -menyn.
   2. På sidan **tjänster** väljer du **hanterings tjänsten för Microsoft-StorSimple**.
   3. Klicka på **starta om tjänsten** under **Microsoft StorSimple Management Service** i den högra rutan. 

### <a name="step-3-reinstall-storsimple-snapshot-manager-and-restore-the-database"></a>Steg 3: installera om StorSimple Snapshot Manager och Återställ databasen
Om du vill installera om StorSimple Snapshot Manager följer du stegen i [installera en ny StorSimple Snapshot Manager](#install-a-new-storsimple-snapshot-manager). Använd sedan följande procedur för att återställa StorSimple Snapshot Manager-databasen.

#### <a name="to-restore-the-database"></a>Så här återställer du databasen
1. Stoppa Microsoft StorSimple Management Service:
   
   1. Starta Serverhanteraren.
   2. På instrument panelen för Serverhanteraren väljer du **tjänster** på **verktyg** -menyn.
   3. På sidan **tjänster** väljer du **Microsoft StorSimple Management Service**.
   4. Klicka på **stoppa tjänsten** under **Microsoft StorSimple Management Service** i den högra rutan.
2. Bläddra till C:\ProgramData\Microsoft\StorSimple\BACatalog.
   
   > [!NOTE]
   > ProgramData är en dold mapp.
   > 
   > 
3. Ta bort XML-filen för katalogen och ersätt den med den version som du sparade tidigare.
4. Starta om Microsoft StorSimple Management-tjänsten: 
   
   1. På instrument panelen för Serverhanteraren väljer du **tjänster** på **verktyg** -menyn.
   2. På sidan **tjänster** väljer du **Microsoft StorSimple Management Service**.
   3. Klicka på **starta om tjänsten** under **Microsoft StorSimple Management Service** i den högra rutan.

## <a name="next-steps"></a>Nästa steg
* Om du vill veta mer om StorSimple Snapshot Manager går du till [StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md).
* Mer information om StorSimple-Snapshot Manager användar gränssnitt finns i [StorSimple Snapshot Manager User Interface](storsimple-use-snapshot-manager.md).
* Om du vill veta mer om hur du använder StorSimple Snapshot Manager går du till [Använd StorSimple Snapshot Manager för att administrera din StorSimple-lösning](storsimple-snapshot-manager-admin.md).

