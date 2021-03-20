---
title: Hantera din StorSimple backup-katalog | Microsoft Docs
description: Förklarar hur du använder sidan StorSimple Enhetshanteraren service backup Catalog för att visa, välja och ta bort säkerhets kopior.
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
ms.openlocfilehash: 6ab4694159c6269f2f72d3d591147d43aef34a0a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017380"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-backup-catalog"></a>Använd tjänsten StorSimple Enhetshanteraren för att hantera din säkerhets kopierings katalog
## <a name="overview"></a>Översikt
Bladet StorSimple Enhetshanteraren service **backup-katalog** visar alla säkerhets kopierings uppsättningar som skapas när manuella eller schemalagda säkerhets kopieringar görs. Du kan använda den här sidan om du vill visa alla säkerhets kopior för en säkerhets kopierings princip eller en volym, välja eller ta bort säkerhets kopior eller använda en säkerhets kopia för att återställa eller klona en volym.

I den här självstudien beskrivs hur du visar, väljer och tar bort en säkerhets kopia. Om du vill lära dig hur du återställer enheten från en säkerhets kopia går du till [återställa enheten från en säkerhets kopia](storsimple-8000-restore-from-backup-set-u2.md). Om du vill lära dig hur du klonar en volym går du till [klona en StorSimple volym](storsimple-8000-clone-volume-u2.md).

![Säkerhets kopierings katalog](./media/storsimple-8000-manage-backup-catalog/bucatalog.png) 

Bladet **säkerhets kopierings katalog** innehåller en fråga som begränsar valet av säkerhets kopierings uppsättning. Du kan filtrera de säkerhets kopior som hämtas baserat på följande parametrar:

* **Enhet** – enheten där säkerhets kopian skapades.
* **Säkerhets kopierings princip eller volym** – den säkerhets kopierings princip eller volym som är associerad med den här säkerhets kopian.
* **Från och till** – datum och tid då säkerhets kopian skapades.

De filtrerade säkerhets kopiorna är sedan tabellen baserat på följande attribut:

* **Namn** – namnet på den säkerhets kopierings princip eller volym som är associerad med säkerhets kopian.
* **Size** – den faktiska storleken på säkerhets kopian.
* **Skapat** den – datum och tid då säkerhets kopieringarna skapades. 
* **Typ** – säkerhets kopierings uppsättningar kan vara lokala ögonblicks bilder eller moln ögonblicks bilder. En lokal ögonblicks bild är en säkerhets kopia av alla dina volym data som lagras lokalt på enheten, medan en moln ögonblicks bild refererar till säkerhets kopian av volym data som finns i molnet. Lokala ögonblicks bilder ger snabbare åtkomst, medan moln ögonblicks bilder väljs för data återhämtning.
* **Initierad av** – säkerhets kopieringarna kan initieras automatiskt av ett schema eller manuellt av en användare. Du kan använda en säkerhets kopierings princip för att schemalägga säkerhets kopieringar. Du kan också använda alternativet **gör säkerhets kopiering** för att göra en manuell säkerhets kopiering.

## <a name="list-backup-sets-for-a-backup-policy"></a>Visa lista över säkerhets kopior för en säkerhets kopierings princip
Utför följande steg för att visa en lista över alla säkerhets kopior för en säkerhets kopierings princip.

#### <a name="to-list-backup-sets"></a>Visa säkerhets kopior
1. Gå till StorSimple Enhetshanteraren-tjänsten och klicka på **säkerhets kopierings katalog**.

2. Filtrera inställningarna enligt följande:
   
   1. Ange tidsintervallet.
   2. Välj lämplig enhet.
   3. Filtrera efter **säkerhets kopierings policy** för att visa motsvarande säkerhets kopior.
   3. I list rutan säkerhets kopierings princip väljer du **alla** för att visa alla säkerhets kopior på den valda enheten.
   4. Klicka på **tillämpa** för att köra den här frågan.
      
      De säkerhets kopior som är associerade med den valda säkerhets kopierings principen visas i listan över säkerhets kopior.

      ![Gå till säkerhets kopierings katalog](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

## <a name="select-a-backup-set"></a>Välj en säkerhets kopierings uppsättning
Utför följande steg för att välja en säkerhets kopia för en volym eller säkerhets kopierings princip.

#### <a name="to-select-a-backup-set"></a>Så här väljer du en säkerhets kopia
1. Gå till StorSimple Enhetshanteraren-tjänsten och klicka på **säkerhets kopierings katalog**.
2. Filtrera inställningarna enligt följande:
   
   1. Ange tidsintervallet. 
   2. Välj lämplig enhet. 
   3. Filtrera efter volym eller säkerhets kopierings princip för den säkerhets kopia som du vill välja.
   4. Klicka på **tillämpa** för att köra den här frågan.
      
      De säkerhets kopior som är associerade med den valda volymen eller säkerhets kopierings principen visas i listan över säkerhets kopior.

      ![Gå till säkerhets kopierings katalog 2](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

3. Välj och expandera en säkerhets kopia. Nu kan du se säkerhets kopie uppsättningarna uppdelade efter de volymer som den innehåller. Alternativen för att **återställa** och **ta bort** är tillgängliga via snabb menyn (högerklicka) för säkerhets kopian. Du kan utföra någon av dessa åtgärder på den säkerhets kopia som du har valt.

    ![Gå till säkerhets kopierings katalog 3](./media/storsimple-8000-manage-backup-catalog/bucatalog2.png)

## <a name="delete-a-backup-set"></a>Ta bort en säkerhets kopia
Ta bort en säkerhets kopia när du inte längre vill behålla de data som är kopplade till den. Utför följande steg för att ta bort en säkerhets kopia.

#### <a name="to-delete-a-backup-set"></a>Ta bort en säkerhets kopia
 Gå till StorSimple Enhetshanteraren-tjänsten och klicka på **säkerhets kopierings katalog**.
1. Filtrera inställningarna enligt följande:
   
   1. Ange tidsintervallet. 
   2. Välj lämplig enhet. 
   3. Filtrera efter volym eller säkerhets kopierings princip för den säkerhets kopia som du vill välja.
   4. Klicka på **tillämpa** för att köra den här frågan.
      
      De säkerhets kopior som är associerade med den valda volymen eller säkerhets kopierings principen visas i listan över säkerhets kopior.

      ![Gå till säkerhets kopierings katalog 4](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

1. Välj och expandera en säkerhets kopia. Nu kan du se säkerhets kopie uppsättningarna uppdelade efter de volymer som den innehåller. Alternativen för att **återställa** och **ta bort** är tillgängliga via snabb menyn (högerklicka) för säkerhets kopian. Högerklicka på den markerade säkerhets kopian och välj **ta bort** på snabb menyn.

    ![Gå till säkerhets kopierings katalog 5](./media/storsimple-8000-manage-backup-catalog/bucatalog3.png)

1. När du uppmanas att bekräfta, granskar du den information som visas och klickar på **ta bort**. Den valda säkerhets kopian tas bort permanent.

    ![Gå till säkerhets kopierings katalog 6](./media/storsimple-8000-manage-backup-catalog/bucatalog4.png)  

1. Du får ett meddelande när borttagningen pågår och när den har slutförts. Uppdatera frågan på den här sidan när borttagningen är färdig. Den borttagna säkerhets kopian visas inte längre i listan över säkerhets kopior.

    ![Gå till säkerhets kopierings katalog 7](./media/storsimple-8000-manage-backup-catalog/bucatalog7.png)

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [använder säkerhets kopierings katalogen för att återställa enheten från en säkerhets kopia](storsimple-8000-restore-from-backup-set-u2.md).
* Lär dig hur du [använder tjänsten StorSimple Enhetshanteraren för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

