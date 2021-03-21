---
title: StorSimple Snapshot Manager och volymer | Microsoft Docs
description: Beskriver hur du använder snapin-modulen StorSimple Snapshot Manager MMC för att visa och hantera volymer och konfigurera säkerhets kopior.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 78896323-e57c-431e-bbe2-0cbde1cf43a2
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 04/18/2016
ms.author: alkohli
ms.openlocfilehash: 309fa85d0a4d877522a89dd8f1e6e71fb2074744
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96022946"
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-volumes"></a>Använd StorSimple Snapshot Manager för att visa och hantera volymer
## <a name="overview"></a>Översikt
Du kan använda noden StorSimple Snapshot Manager **volymer** (i fönstret **omfång** ) för att välja volymer och Visa information om dem. Volymerna presenteras som enheter som motsvarar de volymer som monteras av värden. Noden **volymer** visar lokala volymer och volym typer som stöds av StorSimple, inklusive volymer som identifieras genom användning av iSCSI och en enhet. 

Mer information om volymer som stöds finns i [stöd för flera volym typer](storsimple-what-is-snapshot-manager.md#support-for-multiple-volume-types).

![Volym lista i resultat fönstret](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Volume_node.png)

Med noden **volymer** kan du också genomsöka om eller ta bort volymer när StorSimple Snapshot Manager identifiera dem. 

I den här självstudien beskrivs hur du kan montera, initiera och formatera volymer och sedan använda StorSimple Snapshot Manager för att:

* Visa information om volymer 
* Ta bort volymer
* Genomsök volymer på nytt 
* Konfigurera en enkel volym och säkerhetskopiera den
* Konfigurera en dynamisk speglad volym och säkerhetskopiera den

> [!NOTE]
> Alla **noder i** noden är också tillgängliga i **Åtgärds** fönstret.
> 
> 

## <a name="mount-volumes"></a>Montera volymer
Använd följande procedur för att montera, initiera och formatera StorSimple-volymer. I den här proceduren används disk hantering, ett system verktyg för att hantera hård diskar och motsvarande volymer eller partitioner. Mer information om disk hantering finns i [disk hantering](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770943(v=ws.11)) på webbplatsen för Microsoft TechNet.

#### <a name="to-mount-volumes"></a>Montera volymer
1. Starta Microsoft iSCSI Initiator på värddatorn.
2. Ange en av gränssnitts-IP-adresserna som mål portal eller IP-adress för identifiering och Anslut till enheten. När enheten är ansluten kommer volymerna att vara tillgängliga för ditt Windows-system. Mer information om hur du använder Microsoft iSCSI-initieraren finns i avsnittet "ansluta till en iSCSI-målenhet" i [Installera och konfigurera Microsoft iSCSI Initiator][1].
3. Använd något av följande alternativ för att starta disk hantering:
   
   * Skriv diskmgmt. msc i rutan **Kör** .
   * Starta Serverhanteraren, expandera noden **lagring** och välj sedan **disk hantering**.
   * Starta **administrations verktyg**, expandera noden **dator hantering** och välj sedan **disk hantering**. 
     
     > [!NOTE]
     > Du måste använda administratörs behörighet för att köra disk hantering.
     > 
     > 
4. Ta volymerna online:
   
   1. I disk hantering högerklickar du på en volym som marker ATS som **offline**.
   2. Klicka på **Reaktivera disk**. Disken ska markeras som **online** när disken har återaktiverats.
5. Initiera volym (er):
   
   1. Högerklicka på de identifierade volymerna.
   2. På menyn väljer du **initiera disk**.
   3. I dialog rutan **initiera disk** väljer du de diskar som du vill initiera och klickar sedan på **OK**.
6. Formatera enkla volymer:
   
   1. Högerklicka på en volym som du vill formatera.
   2. På menyn väljer du **Ny enkel volym**.
   3. Använd guiden Ny enkel volym för att formatera volymen:
      
      * Ange volym storleken.
      * Ange en enhets beteckning.
      * Välj NTFS-filsystem.
      * Ange en storlek på allokeringsenheterna på 64 KB.
      * Utför en snabbformatering.
7. Formatera volymer med flera partitioner. Instruktioner finns i avsnittet "partitioner och volymer" i [implementera disk hantering](/previous-versions/tn-archive/dd163556(v=technet.10)).

## <a name="view-information-about-your-volumes"></a>Visa information om dina volymer
Använd följande procedur för att visa information om lokala och Azure StorSimple-volymer.

#### <a name="to-view-volume-information"></a>Visa volym information
1. Klicka på Skriv bords ikonen för att starta StorSimple Snapshot Manager. 
2. I fönstret **omfattning** klickar du på noden **volymer** . En lista över lokala och monterade volymer, inklusive alla Azure StorSimple-volymer, visas i **resultat** fönstret. Kolumnerna i **resultat** fönstret kan konfigureras. (Högerklicka på noden **volymer** , Välj **Visa** och välj sedan **Lägg till/ta bort kolumner**.)
   
    ![Konfigurera kolumnerna](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_View_volumes.png)
   
   | Resultat kolumn | Beskrivning |
   |:--- |:--- |
   |  Name |Kolumnen **namn** innehåller den enhets beteckning som tilldelats varje identifierad volym. |
   |  Enhet |Kolumnen **enhet** innehåller IP-adressen för enheten som är ansluten till värddatorn. |
   |  Enhetens volym namn |Kolumnen **enhetens volym namn** innehåller namnet på den enhets volym som den valda volymen tillhör. Detta är volym namnet som definierats i Azure Portal för den aktuella volymen. |
   |  Åtkomst Sök vägar |I kolumnen **åtkomst Sök vägar** visas åtkomst Sök vägen till volymen. Detta är enhets beteckningen eller monterings punkten där volymen är tillgänglig på värddatorn. |

## <a name="delete-a-volume"></a>Ta bort en volym
Använd följande procedur för att ta bort en volym från StorSimple Snapshot Manager.

> [!NOTE]
> Du kan inte ta bort en volym om den är en del av en volym grupp. (Borttagnings alternativet är inte tillgängligt för volymer som är medlemmar i en volym grupp.) Du måste ta bort hela volym gruppen för att ta bort volymen.

#### <a name="to-delete-a-volume"></a>Ta bort en volym
1. Klicka på Skriv bords ikonen för att starta StorSimple Snapshot Manager.
2. I fönstret **omfattning** klickar du på noden **volymer** . 
3. I **resultat** fönstret högerklickar du på den volym som du vill ta bort.
4. På menyn klickar du på **ta bort**. 
   
    ![Ta bort en volym](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Delete_volume.png) 
5. Dialog rutan **ta bort volym** visas. Skriv **Confirm** i text rutan och klicka sedan på **OK**.
6. StorSimple Snapshot Manager säkerhetskopierar som standard en volym innan den tas bort. Den här försiktighets åtgärden kan skydda dig mot data förlust om borttagningen var oavsiktlig. I StorSimple Snapshot Manager visas ett meddelande om **Automatisk ögonblicks bilds** förlopp medan volymen säkerhets kopie ras. 
   
    ![Automatiskt ögonblicks bild meddelande](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Automatic_snap.png) 

## <a name="rescan-volumes"></a>Genomsök volymer på nytt
Använd följande procedur för att skanna om de volymer som är anslutna till StorSimple Snapshot Manager.

#### <a name="to-rescan-the-volumes"></a>Så här genomsöker du om volymerna
1. Klicka på Skriv bords ikonen för att starta StorSimple Snapshot Manager.
2. I fönstret **omfattning** högerklickar du på **volymer** och klickar sedan på **Genomsök volymer igen**.
   
    ![Genomsök volymer på nytt](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Rescan_volumes.png)
   
    Den här proceduren synkroniserar volym listan med StorSimple Snapshot Manager. Eventuella ändringar, till exempel nya volymer eller borttagna volymer, visas i resultatet.

## <a name="configure-and-back-up-a-basic-volume"></a>Konfigurera och säkerhetskopiera en enkel volym
Använd följande procedur för att konfigurera en säkerhets kopia av en standard volym och starta sedan en säkerhets kopiering direkt eller skapa en princip för schemalagda säkerhets kopieringar.

### <a name="prerequisites"></a>Krav
Innan du börjar:

* Kontrol lera att StorSimple-enheten och värddatorn är korrekt konfigurerade. Mer information finns i [distribuera din lokala StorSimple-enhet](./storsimple-8000-deployment-walkthrough-u2.md).
* Installera och konfigurera StorSimple Snapshot Manager. Mer information finns i [distribuera StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).

#### <a name="to-configure-backup-of-a-basic-volume"></a>Så här konfigurerar du en säkerhets kopia av en standard volym
1. Skapa en standard volym på StorSimple-enheten.
2. Montera, initiera och formatera volymen enligt beskrivningen i [monterings volymer](#mount-volumes). 
3. Klicka på ikonen StorSimple Snapshot Manager på Skriv bordet. Fönstret StorSimple Snapshot Manager visas. 
4. I fönstret **omfattning** högerklickar du på noden **volymer** och väljer sedan **Genomsök volymer** på nytt. När genomsökningen är färdig ska en lista med volymer visas i **resultat** fönstret. 
5. I **resultat** fönstret högerklickar du på volymen och väljer sedan **Skapa volym grupp**. 
   
    ![Skapa volym grupp](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Create_volume_group.png) 
6. I dialog rutan **Skapa volym grupp** anger du ett namn för volym gruppen, tilldelar volymer till den och klickar sedan på **OK**.
7. Expandera noden **volym grupper** i rutan **omfattning** . Den nya volym gruppen bör visas under noden **volym grupper** . 
8. Högerklicka på volym gruppens namn.
   
   * För att starta ett interaktivt säkerhets kopierings jobb (på begäran) klickar du på **gör säkerhets kopiering**. 
   * Om du vill schemalägga en automatisk säkerhets kopiering klickar du på **skapa säkerhets kopierings princip**. På sidan **Allmänt** väljer du en volym grupp i listan. Ange schema information på sidan **schema** . Klicka på **OK** när du är klar. 
9. För att bekräfta att säkerhets kopierings jobbet har startat expanderar du noden **jobb** i fönstret **omfattning** och klickar sedan på noden som **körs** . Listan över jobb som körs för tillfället visas i **resultat** fönstret. 

## <a name="configure-and-back-up-a-dynamic-mirrored-volume"></a>Konfigurera och säkerhetskopiera en dynamisk speglad volym
Utför följande steg för att konfigurera säkerhets kopiering av en dynamisk speglad volym:

* Steg 1: Använd disk hantering för att skapa en dynamisk speglad volym. 
* Steg 2: Använd StorSimple Snapshot Manager för att konfigurera säkerhets kopiering.

### <a name="prerequisites"></a>Krav
Innan du börjar:

* Kontrol lera att StorSimple-enheten och värddatorn är korrekt konfigurerade. Mer information finns i [distribuera din lokala StorSimple-enhet](storsimple-8000-deployment-walkthrough-u2.md).
* Installera och konfigurera StorSimple Snapshot Manager. Mer information finns i [distribuera StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).
* Konfigurera två volymer på StorSimple-enheten. (I exemplen är tillgängliga volymer **disk 1** och **disk 2**.) 

### <a name="step-1-use-disk-management-to-create-a-dynamic-mirrored-volume"></a>Steg 1: Använd disk hantering för att skapa en dynamisk speglad volym
Disk hantering är ett system verktyg för att hantera hård diskar och de volymer eller partitioner som de innehåller. Mer information om disk hantering finns i [disk hantering](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770943(v=ws.11)) på webbplatsen för Microsoft TechNet.

#### <a name="to-create-a-dynamic-mirrored-volume"></a>Så här skapar du en dynamisk speglad volym
1. Använd något av följande alternativ för att starta disk hantering: 
   
   * Öppna rutan **Kör** , Skriv **diskmgmt. msc** och tryck på RETUR.
   * Starta Serverhanteraren, expandera noden **lagring** och välj sedan **disk hantering**. 
   * Starta **administrations verktyg**, expandera noden **dator hantering** och välj sedan **disk hantering**. 
2. Se till att du har två tillgängliga volymer på StorSimple-enheten. (I exemplet är tillgängliga volymer **disk 1** och **disk 2**.) 
3. I fönstret disk hantering i den högra kolumnen i det nedre fönstret högerklickar du på **disk 1** och väljer **ny speglad volym**. 
   
    ![Ny speglad volym](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_New_mirrored_volume.png) 
4. Klicka på **Nästa** på sidan **ny speglad volym** i guiden.
5. På sidan **Välj diskar** väljer du **disk 2** i den **valda** rutan, klickar på **Lägg till** och klickar sedan på **Nästa**. 
6. På sidan **tilldela enhets beteckning eller sökväg** accepterar du standardvärdena och klickar sedan på **Nästa**. 
7. På sidan **Formatera volym** i rutan storlek på **allokeringsenhet** väljer du **64K**. Markera kryss rutan **utför ett snabb format** och klicka sedan på **Nästa**. 
8. På sidan **Slutför den nya speglade volymen** granskar du inställningarna och klickar sedan på **Slutför**. 
9. Ett meddelande visas som anger att den grundläggande disken kommer att konverteras till en dynamisk disk. Klicka på **Ja**.
   
    ![Dynamiskt disk konverterings meddelande](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Disk_management_msg.png) 
10. I disk hantering kontrollerar du att disk 1 och disk 2 visas som dynamiska speglade volymer. (**Dynamisk** ska visas i kolumnen Status och kapacitets fältets färg ska ändras till röd, vilket indikerar en speglad volym.) 
    
    ![Disk hantering speglade dynamiska diskar](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Verify_dynamic_disks_2.png) 

### <a name="step-2-use-storsimple-snapshot-manager-to-configure-backup"></a>Steg 2: Använd StorSimple Snapshot Manager för att konfigurera säkerhets kopiering
Använd följande procedur för att konfigurera en dynamisk speglad volym och sedan antingen starta en säkerhets kopiering direkt eller skapa en princip för schemalagda säkerhets kopieringar.

#### <a name="to-configure-backup-of-a-dynamic-mirrored-volume"></a>Konfigurera säkerhets kopiering av en dynamisk speglad volym
1. Klicka på ikonen StorSimple Snapshot Manager på Skriv bordet. Fönstret StorSimple Snapshot Manager visas. 
2. I fönstret **omfattning** högerklickar du på noden **volymer** och väljer **Genomsök volymer** på nytt. När genomsökningen är färdig ska en lista med volymer visas i **resultat** fönstret. Den dynamiska speglade volymen visas som en enskild volym. 
3. I **resultat** fönstret högerklickar du på den dynamiska speglade volymen och klickar sedan på **Skapa volym grupp**. 
4. I dialog rutan **Skapa volym grupp** anger du ett namn för volym gruppen, tilldelar den dynamiska speglade volymen till den här gruppen och klickar sedan på **OK**. 
5. Expandera noden **volym grupper** i rutan **omfattning** . Den nya volym gruppen bör visas under noden  **volym grupper** . 
6. Högerklicka på volym gruppens namn. 
   
   * För att starta ett interaktivt säkerhets kopierings jobb (på begäran) klickar du på **gör säkerhets kopiering**. 
   * Om du vill schemalägga en automatisk säkerhets kopiering klickar du på **skapa säkerhets kopierings princip**. På sidan **Allmänt** väljer du volym gruppen i listan. Ange schema information på sidan **schema** . Klicka på **OK** när du är klar. 
7. Du kan övervaka säkerhets kopierings jobbet när det körs. I fönstret **omfattning** expanderar du noden **jobb** och klickar sedan på **Kör**. jobb informationen visas i **resultat** fönstret. När säkerhets kopierings jobbet är klart överförs informationen till de **senaste 24** timmarna jobb listan. 

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [använder StorSimple Snapshot Manager för att administrera din StorSimple-lösning](storsimple-snapshot-manager-admin.md).
* Lär dig hur du [använder StorSimple-Snapshot Manager för att skapa och hantera volym grupper](storsimple-snapshot-manager-manage-volume-groups.md).

<!--Reference links-->
[1]: /previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee338480(v=ws.10)