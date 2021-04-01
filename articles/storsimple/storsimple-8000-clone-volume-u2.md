---
title: Klona en volym på StorSimple 8000-serien | Microsoft Docs
description: Beskriver de olika klonings typerna och användningen och förklarar hur du kan använda en säkerhets kopia för att klona en enskild volym på en StorSimple 8000-serie enhet.
services: storsimple
documentationcenter: NA
author: alkohli
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.date: 07/15/2020
ms.author: alkohli
ms.openlocfilehash: 5ebb7f1d9440bc7a8b75afe64403762532213c1d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91767573"
---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-clone-a-volume"></a>Använda StorSimple Device Manager-tjänsten på Azure-portalen för att klona en volym

## <a name="overview"></a>Översikt

I den här självstudien beskrivs hur du kan använda en säkerhets kopia för att klona en enskild volym via bladet **säkerhets kopierings katalog** . Det förklarar också skillnaden mellan *tillfälliga* och *permanenta* kloner. Vägledningen i den här självstudien gäller för alla enheter i StorSimple 8000-serien som kör uppdatering 3 eller senare.

Bladet StorSimple Enhetshanteraren service **backup-katalog** visar alla säkerhets kopior som skapas när manuella eller automatiserade säkerhets kopieringar görs. Du kan sedan välja en volym i en säkerhets kopierings uppsättning att klona.

 ![Lista över säkerhets kopie uppsättningar](./media/storsimple-8000-clone-volume-u2/bucatalog.png)

## <a name="considerations-for-cloning-a-volume"></a>Att tänka på när du klonar en volym

Överväg följande information när du klonar en volym.

- En klon fungerar på samma sätt som en vanlig volym. Varje åtgärd som är möjlig på en volym är tillgänglig för klonen.

- Övervakning och standard säkerhets kopiering inaktive ras automatiskt på en klonad volym. Du måste konfigurera en klonad volym för säkerhets kopieringar.

- En lokalt fäst volym klonas som en nivå volym. Om du behöver en klonad volym som fästs lokalt kan du konvertera klonen till en lokalt fäst volym när klonings åtgärden har slutförts. Information om hur du konverterar en nivå volym till en lokalt fäst volym finns i [ändra volym typ](storsimple-8000-manage-volumes-u2.md#change-the-volume-type).

- Om du försöker konvertera en klonad volym från nivå till lokalt fäst omedelbart efter kloning (när den fortfarande är en tillfällig klon), Miss lyckas konverteringen med följande fel meddelande:

    `Unable to modify the usage type for volume {0}. This can happen if the volume being modified is a transient clone and hasn’t been made permanent. Take a cloud snapshot of this volume and then retry the modify operation.`

    Det här felet tas emot endast om du klonar på en annan enhet. Du kan konvertera volymen till lokalt fäst om du först konverterar den tillfälliga klonen till en permanent klon. Ta en moln ögonblicks bild av den tillfälliga klonen för att konvertera den till en permanent klon.

## <a name="create-a-clone-of-a-volume"></a>Skapa en klon av en volym

Du kan skapa en klon på samma enhet, en annan enhet eller till och med en moln installation genom att använda en lokal eller molnbaserad ögonblicks bild.

I proceduren nedan beskrivs hur du skapar en klon från säkerhets kopierings katalogen.  

Utför följande steg för att skapa en klon av din volym från säkerhets kopierings katalogen.

#### <a name="to-clone-a-volume"></a>Klona en volym

1. Gå till StorSimple Enhetshanteraren-tjänsten och klicka sedan på **säkerhets kopierings katalog**.

2. Välj en säkerhets kopierings uppsättning enligt följande:
   
   1. Välj lämplig enhet.
   2. I list rutan väljer du den volym eller säkerhets kopierings princip för säkerhets kopian som du vill välja.
   3. Ange tidsintervallet.
   4. Klicka på **tillämpa** för att köra den här frågan.

      De säkerhets kopior som är associerade med den valda volymen eller säkerhets kopierings principen visas i listan över säkerhets kopior.
   
      ![Lista över säkerhets kopiering 2](./media/storsimple-8000-clone-volume-u2/bucatalog.png)
     
3. Expandera säkerhets kopian om du vill visa den associerade volymen och välj en volym i en säkerhets kopia. Högerklicka och välj sedan **klona** på snabb menyn.

   ![Lista över säkerhets kopior 3](./media/storsimple-8000-clone-volume-u2/clonevol3b.png) 

3. Utför följande steg på bladet **klona** :
   
   1. Identifiera en målenhet. Detta är den plats där klonen kommer att skapas. Du kan välja samma enhet eller ange en annan enhet.

      > [!NOTE]
      > Kontrol lera att kapaciteten som krävs för klonen är lägre än den kapacitet som är tillgänglig på mål enheten.
       
   2. Ange ett unikt volym namn för klonen. Namnet måste innehålla mellan 3 och 127 tecken.
      
       > [!NOTE]
       > Fältet **klona volym som** visas i **nivå** även om du klonar en lokalt fäst volym. Du kan inte ändra den här inställningen. men om du vill att den klonade volymen ska fästa lokalt kan du konvertera klonen till en lokalt fäst volym när du har skapat klonen. Information om hur du konverterar en nivå volym till en lokalt fäst volym finns i [ändra volym typ](storsimple-8000-manage-volumes-u2.md#change-the-volume-type).
          
   3. Under **anslutna värdar** anger du en åtkomst kontroll post (ACR) för klonen. Du kan lägga till en ny ACR eller välja från den befintliga listan. ACR avgör vilka värdar som har åtkomst till den här klonen.
      
       ![Lista över säkerhets kopior 4](./media/storsimple-8000-clone-volume-u2/clonevol3a.png) 

   4. Slutför åtgärden genom att klicka på **klona** .

4. Ett klonings jobb initieras och du får ett meddelande när klonen har skapats. Klicka på arbets aviseringen eller gå till bladet **jobb** för att övervaka klonings jobbet.

    ![Lista över säkerhets kopior 5](./media/storsimple-8000-clone-volume-u2/clonevol5.png)

7. När klonings jobbet är klart går du till din enhet och klickar sedan på **volymer**. I listan över volymer bör du se den klon som precis har skapats i samma volym behållare som har käll volymen.

    ![Lista över säkerhets kopie uppsättningar 6](./media/storsimple-8000-clone-volume-u2/clonevol6.png)

En klon som skapas på det här sättet är en tillfällig klon. Mer information om klonings typer finns i [tillfälliga och permanenta kloner](#transient-vs-permanent-clones).


## <a name="transient-vs-permanent-clones"></a>Tillfälliga eller permanenta kloner
Tillfälliga kloner skapas bara när du klonar till en annan enhet. Du kan klona en särskild volym från en säkerhets kopia till en annan enhet som hanteras av StorSimple-Enhetshanteraren. Den tillfälliga klonen har referenser till data på den ursprungliga volymen och använder dessa data för att läsa och skriva lokalt på mål enheten.

När du tar en moln ögonblicks bild av en tillfällig klon är den resulterande klonen en *permanent* klon. Under den här processen skapas en kopia av data i molnet och tiden för att kopiera dessa data bestäms av storleken på data och Azure-fördröjningar (detta är en Azure-till-Azure-kopia). Den här processen kan ta flera dagar till veckor. Den tillfälliga klonen blir en permanent klon och har inga referenser till de ursprungliga volym data som den klonades från.

## <a name="scenarios-for-transient-and-permanent-clones"></a>Scenarier för tillfälliga och permanenta kloner
I följande avsnitt beskrivs exempel situationer där tillfälliga och permanenta kloner kan användas.

### <a name="item-level-recovery-with-a-transient-clone"></a>Återställning på objekt nivå med en tillfällig klon
Du måste återställa en en-årets gamla presentations fil i Microsoft PowerPoint. IT-administratören identifierar den aktuella säkerhets kopian från den tiden och filtrerar sedan volymen. Administratören klonar sedan volymen, letar upp den fil som du letar efter och ger dig den. I det här scenariot används en tillfällig klon.

### <a name="testing-in-the-production-environment-with-a-permanent-clone"></a>Testa i produktions miljön med en permanent klon
Du måste verifiera en test-bugg i produktions miljön. Du skapar en klon av volymen i produktions miljön och tar en moln ögonblicks bild av den här klonen för att skapa en oberoende klonad volym. I det här scenariot används en permanent klon.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [återställer en StorSimple-volym från en säkerhets kopia](storsimple-8000-restore-from-backup-set-u2.md).
* Lär dig hur du [använder tjänsten StorSimple Enhetshanteraren för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

