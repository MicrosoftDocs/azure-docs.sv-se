---
title: Klona en säkerhets kopia av en StorSimple-virtuell matris | Microsoft Docs
description: Lär dig hur du klonar en säkerhets kopia och återställer en fil från din virtuella StorSimple-matris.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: af6e979c-55e3-477c-b53e-a76a697f80c9
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2016
ms.author: alkohli
ms.openlocfilehash: b6cc89cb082cd0ed32abd88e3a6683c60a27ba90
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96023048"
---
# <a name="clone-from-a-backup-of-your-storsimple-virtual-array"></a>Klona från en säkerhets kopia av din virtuella StorSimple-matris

## <a name="overview"></a>Översikt

Den här artikeln beskriver steg för steg hur du klonar en säkerhets kopia av dina resurser eller volymer på din Microsoft Azure StorSimple virtuella matrisen. Den klonade säkerhets kopian används för att återställa en borttagen eller förlorad fil. Artikeln innehåller också detaljerade steg för att utföra en återställning på objekt nivå på din virtuella StorSimple-matris som kon figurer ATS som en fil server.

## <a name="clone-shares-from-a-backup-set"></a>Klona resurser från en uppsättning säkerhetskopior

**Innan du försöker klona resurser måste du kontrol lera att det finns tillräckligt med utrymme på enheten för att slutföra den här åtgärden.** Om du vill klona från en säkerhets kopia går du till [Azure Portal](https://portal.azure.com/)och utför följande steg.

#### <a name="to-clone-a-share"></a>Klona en resurs

1. Bläddra till **enhets** bladet. Markera och klicka på enheten och sedan på **resurser**. Välj den resurs som du vill klona, högerklicka på resursen för att anropa snabb menyn. Välj **Klona**.
   
   ![Klona en säkerhets kopia](./media/storsimple-virtual-array-clone/cloneshare1.png)
2. På bladet **klona** klickar du på **säkerhets kopierings > väljer** och gör sedan följande: 
   
   a.    Filtrera en säkerhets kopia på den här enheten baserat på tidsintervallet. Du kan välja mellan **de senaste 7 dagarna**, **senaste 30 dagarna** och **föregående år**.
   
   b.    I listan över filtrerade säkerhets kopior som visas väljer du en säkerhets kopia att klona från.
   
   c.    Klicka på **OK**.
   
   ![Klona en säkerhets kopia 2](./media/storsimple-virtual-array-clone/cloneshare3.png)
3. Klicka på **mål inställningar** på bladet **klona** och gör sedan följande:
   
   a.    Ange ett resurs namn. Resurs namnet måste innehålla 3-127 tecken.
   
   b.    Du kan också ange en beskrivning av den klonade resursen.
   
   c.    Du kan inte ändra typ av resurs som du återställer till. En nivå resurs klonas som en nivå och en lokalt fäst resurs som lokalt fäst.
   
   d.    Kapaciteten anges som lika med storleken på den resurs som du klonar från.
   
   e.    Tilldela administratörerna för den här resursen. Du kommer att kunna ändra resurs egenskaperna via Utforskaren när kloningen har slutförts.
   
   f.    Klicka på **OK**.
   
   ![Klona en säkerhets kopia 3](./media/storsimple-virtual-array-clone/cloneshare6.png)

4. Starta ett klonings jobb genom att klicka på **klona** . När jobbet har slutförts startar klonings åtgärden och du meddelas. Om du vill övervaka förloppet för kloningen går du till bladet **jobb** och klickar på jobbet för att visa jobb information.
5. När klonen har skapats går du tillbaka till bladet **resurser** på enheten.
6. Nu kan du Visa den nya klonade resursen i listan över resurser på enheten. En nivå resurs klonas som en nivå och en lokalt fäst resurs som en lokalt fäst resurs.
   
   ![Klona en säkerhets kopia 4](./media/storsimple-virtual-array-clone/cloneshare10.png)

## <a name="clone-volumes-from-a-backup-set"></a>Klona volymer från en uppsättning säkerhetskopior

Om du vill klona från en säkerhets kopia måste du i Azure Portal utföra steg som liknar dem när du klonar en resurs. Klonings åtgärden klonar säkerhets kopian till en ny volym på samma virtuella enhet. Det går inte att klona till en annan enhet.

#### <a name="to-clone-a-volume"></a>Klona en volym

1. Bläddra till **enhets** bladet. Markera och klicka på enheten och sedan på **volymer**. Välj den volym som du vill klona, högerklicka på volymen för att anropa snabb menyn. Välj **Klona**.
   
   ![Klona en volym](./media/storsimple-virtual-array-clone/clonevolume1.png)
2. På bladet **klona** klickar du på **säkerhets kopiering** och gör sedan följande: 
   
   a.    Filtrera en säkerhets kopia på den här enheten baserat på tidsintervallet. Du kan välja mellan **de senaste 7 dagarna**, **senaste 30 dagarna** och **föregående år**. 
   
   b.    I listan över filtrerade säkerhets kopior som visas väljer du en säkerhets kopia att klona från.
   
   c.    Klicka på **OK**.
   
   ![Klona en volym 2](./media/storsimple-virtual-array-clone/clonevolume3.png)
3. På bladet **klona** klickar du på **mål volym inställningar** och gör sedan följande::
   
   a. Enhets namnet fylls i automatiskt.
   
   b. Ange ett volym namn för den **klonade volymen**. Volym namnet måste innehålla mellan 3 och 127 tecken.
   
   c. Volym typen ställs automatiskt in på den ursprungliga volymen. En nivå volym klonas i nivå och en lokalt fäst volym som fästs lokalt.
   
   d. För de **anslutna värdarna** klickar du på **Välj**.
   
   ![Klona en volym 3](./media/storsimple-virtual-array-clone/clonevolume4.png)
4. På bladet **anslutna värdar** väljer du från en befintlig ACR eller lägger till en ny ACR. Om du vill lägga till en ny ACR måste du ange ett ACR-namn och värd-IQN. Klicka på **Välj**.
   
   ![Klona en volym 4](./media/storsimple-virtual-array-clone/clonevolume5.png)
5. Klicka på **klona** för att starta ett klonings jobb.
   
   ![Klona en volym 5](./media/storsimple-virtual-array-clone/clonevolume6.png)  
6. När klonings jobbet har skapats startar kloningen. När klonen har skapats visas den på bladet volymer på enheten. Observera att en nivå volym klonas i nivå och att en lokalt fäst volym klonas som en lokalt fäst volym.
   
   ![Klona en volym 6](./media/storsimple-virtual-array-clone/clonevolume8.png)
7. När volymen visas online i listan över volymer, är volymen tillgänglig för användning. Uppdatera listan över mål i fönstret Egenskaper för iSCSI-initierare på iSCSI-initierarens värd. Ett nytt mål som innehåller det klonade volym namnet ska visas som "inaktiv" i kolumnen Status.
8. Välj mål och klicka på **Anslut**. När initieraren är ansluten till målet ska statusen ändras till **ansluten**.
9. I fönstret **disk hantering** visas monterade volymer på det sätt som visas i följande bild. Högerklicka på den identifierade volymen (klicka på diskens namn) och klicka sedan på **Online**.

> [!IMPORTANT]
> När du försöker klona en volym eller en resurs från en säkerhets kopia kan en målvolym eller resurs fortfarande skapas i portalen om klonings jobbet Miss lyckas. Det är viktigt att du tar bort den här mål volymen eller resursen i portalen för att minimera eventuella framtida problem som uppstår på grund av det här elementet.
> 
> 

## <a name="item-level-recovery-ilr"></a>Återställning på objektnivå (ILR)

I den här versionen introduceras återställningen på objekt nivå (ILR) på en StorSimple virtuell matris som kon figurer ATS som en fil server. Med funktionen kan du göra en detaljerad återställning av filer och mappar från en moln säkerhets kopia av alla resurser på StorSimple-enheten. Du kan hämta borttagna filer från de senaste säkerhets kopiorna med en självbetjänings modell.

Varje resurs har en *. backups* -mapp som innehåller de senaste säkerhets kopiorna. Du kan navigera till önskad säkerhets kopia, kopiera relevanta filer och mappar från säkerhets kopian och återställa dem. Den här funktionen eliminerar anrop till administratörer för att återställa filer från säkerhets kopior.

1. När du utför ILR kan du se säkerhets kopiorna via Utforskaren. Klicka på den resurs som du vill titta på säkerhets kopian för. Du kommer att se en *. backups* -mapp som skapats under resursen som lagrar alla säkerhets kopior. Expandera mappen *. backups* för att Visa säkerhets kopiorna. Mappen visar den expanderade vyn för hela den säkerhetskopierade hierarkin. Den här vyn skapas på begäran och tar vanligt vis bara några sekunder att skapa.
   
   De sista fem säkerhets kopiorna visas på det här sättet och kan användas för att utföra en återställning på objekt nivå. De fem senaste säkerhets kopiorna omfattar både standard schemalagda och manuella säkerhets kopieringar.
   
   * **Schemalagda säkerhets kopior** som heter &lt; enhets namn &gt; DAILYSCHEDULE-ÅÅÅÅMMDD-HHMMSS-UTC.
   * **Manuella säkerhets kopior** som heter ad hoc-ÅÅÅÅMMDD-HHMMSS-UTC.
     
     ![Skärm bild av Utforskaren som visar mappen. Backups. I den mappen väljs mappen manuella säkerhets kopieringar.](./media/storsimple-virtual-array-clone/image14.png)

2. Identifiera säkerhets kopian som innehåller den senaste versionen av den borttagna filen. Även om mappnamnet innehåller en UTC-tidsstämpel i vart och ett av de föregående fallen, är tiden då mappen skapades den faktiska enhets tiden när säkerhets kopieringen startades. Använd mappens tidstämpel för att hitta och identifiera säkerhets kopiorna.

3. Leta upp mappen eller filen som du vill återställa i säkerhets kopian som du identifierade i föregående steg. Obs! Du kan bara visa de filer eller mappar som du har behörighet för. Om du inte kan komma åt vissa filer eller mappar kan du kontakta en resurs administratör. Administratören kan använda Utforskaren för att redigera resurs behörigheter och ge dig åtkomst till den angivna filen eller mappen. Det är en rekommenderad metod att resurs administratören är en användar grupp i stället för en enskild användare.

4. Kopiera filen eller mappen till lämplig resurs på StorSimple-filservern.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur du [administrerar den virtuella StorSimple-matrisen med hjälp av det lokala webb gränssnittet](storsimple-ova-web-ui-admin.md).

