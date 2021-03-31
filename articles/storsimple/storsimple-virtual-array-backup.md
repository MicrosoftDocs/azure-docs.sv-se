---
title: Självstudie för säkerhets kopiering av Microsoft Azure StorSimple virtuell matris | Microsoft Docs
description: Skapa schemalagda och manuella säkerhets kopieringar för Microsoft Azure StorSimple virtuell matris och utför återställning på objekt nivå för att återställa en borttagen fil på den virtuella matrisen.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: e3cdcd9e-33b1-424d-82aa-b369d934067e
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 99c7f4ab92e4cce3937a933fec97008c281a0eae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96018298"
---
# <a name="back-up-shares-or-volumes-on-your-storsimple-virtual-array"></a>Säkerhetskopiera resurser eller volymer på din virtuella StorSimple-matris

## <a name="overview"></a>Översikt

Den virtuella StorSimple-matrisen är en lokal virtuell enhet med hybrid moln lagring som kan konfigureras som en fil server eller en iSCSI-server. Med den virtuella matrisen kan användaren skapa schemalagda och manuella säkerhets kopieringar av alla resurser eller volymer på enheten. När den konfigureras som en fil server, tillåter den också återställning på objekt nivå. I den här självstudien beskrivs hur du skapar schemalagda och manuella säkerhets kopieringar och utför återställning på objekt nivå för att återställa en borttagen fil på den virtuella matrisen.

Den här självstudien gäller endast för virtuella StorSimple-matriser. Information om 8000-serien finns i [skapa en säkerhets kopia för enhet med 8000-serien](./storsimple-8000-manage-backup-policies-u2.md)

## <a name="back-up-shares-and-volumes"></a>Säkerhetskopiera resurser och volymer

Säkerhets kopieringar ger tidpunkts skydd, förbättrar återställnings möjligheterna och minimerar återställnings tider för resurser och volymer. Du kan säkerhetskopiera en resurs eller volym på din StorSimple-enhet på två sätt: **schemalagd** eller **manuell**. Var och en av metoderna beskrivs i följande avsnitt.

## <a name="change-the-backup-start-time"></a>Ändra start tid för säkerhets kopiering

> [!NOTE]
> I den här versionen skapas schemalagda säkerhets kopieringar av en standard princip som körs dagligen vid en viss tidpunkt och säkerhetskopierar alla resurser eller volymer på enheten. Det går inte att skapa anpassade principer för schemalagda säkerhets kopieringar just nu.


Din virtuella StorSimple-matris har en standard princip för säkerhets kopiering som börjar vid en angiven tidpunkt (22:30) och säkerhetskopierar alla resurser eller volymer på enheten en gång om dagen. Du kan ändra tiden då säkerhets kopieringen startar, men frekvensen och kvarhållning (som anger antalet säkerhets kopior som ska behållas) kan inte ändras. Under dessa säkerhets kopieringar säkerhets kopie ras hela den virtuella enheten. Detta kan påverka enhetens prestanda och påverkar de arbets belastningar som har distribuerats på enheten. Därför rekommenderar vi att du schemalägger dessa säkerhets kopieringar för tider med låg belastning.

 Om du vill ändra standard start tiden för säkerhets kopiering utför du följande steg i [Azure Portal](https://portal.azure.com/).

#### <a name="to-change-the-start-time-for-the-default-backup-policy"></a>Ändra start tiden för standard säkerhets kopierings principen

1. Gå till **enheter**. Listan över enheter som är registrerade i StorSimple Enhetshanteraren-tjänsten visas. 
   
    ![Navigera till enheter](./media/storsimple-virtual-array-backup/changebuschedule1.png)

2. Markera och klicka på din enhet. Bladet **Inställningar** visas. Gå till **Hantera principer för > säkerhets kopiering**.
   
    ![Välj din enhet](./media/storsimple-virtual-array-backup/changebuschedule2.png)

3. På bladet **säkerhets kopierings principer** är standard start tiden 22:30. Du kan ange den nya start tiden för det dagliga schemat i enhetens tidszon.
   
    ![Navigera till säkerhets kopierings principer](./media/storsimple-virtual-array-backup/changebuschedule5.png)

4. Klicka på **Spara**.

### <a name="take-a-manual-backup"></a>Gör en manuell säkerhets kopiering

Förutom schemalagda säkerhets kopieringar kan du när som helst göra en manuell säkerhets kopiering av enhets data (på begäran).

#### <a name="to-create-a-manual-backup"></a>Så här skapar du en manuell säkerhetskopia

1. Gå till **enheter**. Välj din enhet och högerklicka **...** längst till höger på den markerade raden. I snabb menyn väljer du **gör säkerhets kopiering**.
   
    ![Navigera för att göra en säkerhets kopia](./media/storsimple-virtual-array-backup/takebackup1m.png)

2. På bladet **ta säkerhets kopia** klickar du på **gör säkerhets kopiering**. Då säkerhets kopie ras alla resurser på fil servern eller alla volymer på iSCSI-servern. 
   
    ![säkerhets kopieringen startar](./media/storsimple-virtual-array-backup/takebackup2m.png)
   
    En säkerhets kopiering på begäran startar och du ser att en säkerhets kopierings jobb har startat.
   
    ![säkerhets kopierings start 2](./media/storsimple-virtual-array-backup/takebackup3m.png) 
   
    När jobbet har slutförts meddelas du igen. Säkerhets kopierings processen startas sedan.
   
    ![säkerhets kopierings jobbet har skapats](./media/storsimple-virtual-array-backup/takebackup4m.png)

3. Du kan följa förloppet för säkerhets kopieringarna och titta på jobb informationen genom att klicka på meddelandet. Då går du till  **jobb information**.
   
     ![information om säkerhets kopierings jobb](./media/storsimple-virtual-array-backup/takebackup5m.png)

4. När säkerhets kopieringen är klar går du till **hantering > säkerhets kopierings katalog**. Du kommer att se en moln ögonblicks bild av alla resurser (eller volymer) på enheten.
   
    ![Slutförd säkerhets kopiering](./media/storsimple-virtual-array-backup/takebackup19m.png) 

## <a name="view-existing-backups"></a>Visa befintliga säkerhets kopior
Om du vill visa befintliga säkerhets kopior utför du följande steg i Azure Portal.

#### <a name="to-view-existing-backups"></a>Så här visar du befintliga säkerhets kopior

1. Gå till **enhets** bladet. Markera och klicka på din enhet. I bladet **Inställningar** går du till **hantering > säkerhets kopierings katalog**.
   
    ![Navigera till säkerhets kopierings katalog](./media/storsimple-virtual-array-backup/viewbackups1.png)
2. Ange följande kriterier som ska användas för filtrering:
   
   - **Tidsintervall** – kan vara **de senaste 1 timmarna**, **de senaste 24 timmarna**, **senaste 7 dagarna**, **senaste 30 dagarna**, **senaste året** och **anpassat datum**.
    
   - **Enheter** – Välj i listan över fil servrar eller iSCSI-servrar som registrerats med din StorSimple Enhetshanteraren-tjänst.
   
   - **Initierad** – kan **schemaläggas** automatiskt (av en säkerhets kopierings princip) eller startas **manuellt** (av dig).
   
     ![Filtrera säkerhets kopior](./media/storsimple-virtual-array-backup/viewbackups2.png)

3. Klicka på **Applicera**. Den filtrerade listan med säkerhets kopior visas på bladet **säkerhets kopierings katalog** . Observera att endast 100 säkerhets kopierings element kan visas vid en specifik tidpunkt.
   
    ![Uppdaterad säkerhets kopierings katalog](./media/storsimple-virtual-array-backup/viewbackups3.png)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur [du administrerar din virtuella StorSimple-matris](storsimple-ova-web-ui-admin.md).