---
title: StorSimple Snapshot Manager MMC-meny åtgärder | Microsoft Docs
description: Beskriver hur du använder standard meny åtgärderna i Microsoft Management Console (MMC) i StorSimple Snapshot Manager.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 78ef81af-0d3a-4802-be54-ad192f9ac8a6
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 01064c3668b673baea7aedd9a65c92b03c48dc10
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90056011"
---
# <a name="use-the-mmc-menu-actions-in-storsimple-snapshot-manager"></a>Använd MMC-meny åtgärderna i StorSimple Snapshot Manager

## <a name="overview"></a>Översikt
I StorSimple Snapshot Manager visas följande åtgärder på alla åtgärds menyer och alla varianter av **Åtgärds** fönstret.

* Visa
* Nytt fönster från här 
* Uppdatera 
* Exportera lista 
* Hjälp 

De här åtgärderna ingår i Microsoft Management Console (MMC) och är inte bara för StorSimple Snapshot Manager. Den här självstudien beskriver de här åtgärderna och förklarar hur du använder dem i StorSimple Snapshot Manager.

## <a name="view"></a>Visa
Du kan använda alternativet **Visa** för att ändra **resultat** fönstrets vy och ändra konsol fönstrets vy. 

#### <a name="to-change-the-results-pane-view"></a>Ändra resultat fönstrets vy
1. Klicka på Skriv bords ikonen för att starta StorSimple Snapshot Manager.
2. I fönstret **omfattning** högerklickar du på en nod eller expanderar noden och högerklickar på ett objekt i **resultat** fönstret och klickar sedan på alternativet **Visa** . 
3. Om du vill lägga till eller ta bort kolumner som visas i **resultat** fönstret klickar du på **Lägg till/ta bort kolumner**. Dialog rutan **Lägg till/ta bort kolumner** visas.
   
    ![Lägg till eller ta bort kolumner från resultat fönstret](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Add_remove_columns.png) 
4. Fyll i formuläret enligt följande:
   
   * Välj objekt i listan **tillgängliga** kolumner och klicka på **Lägg** till för att lägga till dem i listan över **kolumner som visas** . 
   * Klicka på objekt i listan **kolumner som visas** och ta bort dem från listan genom att klicka på **ta bort** . 
   * Markera ett objekt i listan kolumner som **visas** och klicka på **Flytta upp** eller **Flytta ned** för att flytta objektet uppåt eller nedåt i listan. 
   * Klicka på **Återställ standardvärden** om du vill återgå till standard **resultat** fönstrets konfiguration. 
5. När du är färdig med dina val klickar du på **OK**. 

#### <a name="to-change-the-console-window-view"></a>Ändra konsol fönstrets vy
1. Klicka på Skriv bords ikonen för att starta StorSimple Snapshot Manager.
2. I fönstret **omfattning** högerklickar du på en nod, klickar på **Visa** och klickar sedan på **Anpassa**. Dialog rutan **Anpassa** visas.
   
    ![Anpassa konsol fönstret](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Customize.png) 
3. Markera eller avmarkera kryss rutorna för att visa eller dölja objekt i konsol fönstret. När du är färdig med dina val klickar du på **OK**.

## <a name="new-window-from-here"></a>Nytt fönster från här
Du kan använda det **nya fönstret härifrån** för att öppna ett nytt konsol fönster.

#### <a name="to-open-a-new-console-window"></a>Öppna ett nytt konsol fönster
1. Klicka på Skriv bords ikonen för att starta StorSimple Snapshot Manager.
2. I fönstret **omfattning** högerklickar du på en nod och klickar sedan på **nytt fönster** härifrån. 
   
    Ett nytt fönster visas med endast den omfattning som du har valt. Om du till exempel högerklickar på noden **säkerhets kopierings principer** visas bara noden **säkerhets kopierings principer** i fönstret **omfattning** och en lista över definierade säkerhets kopierings principer i **resultat** fönstret. Se följande exempel.
   
    ![Nytt fönster från här](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_NewWindow.png) 

## <a name="refresh"></a>Uppdatera
Du kan använda **uppdaterings** åtgärden för att uppdatera konsol fönstret.

#### <a name="to-update-the-console-window"></a>Så här uppdaterar du konsol fönstret
1. Klicka på Skriv bords ikonen för att starta StorSimple Snapshot Manager.
2. I fönstret **omfattning** högerklickar du på en nod eller expanderar noden och högerklickar på ett objekt i **resultat** fönstret och klickar sedan på **Uppdatera**. 

## <a name="export-list"></a>Exportera lista
Du kan använda åtgärden **Exportera lista** för att spara en lista i en fil med kommaavgränsade värden (CSV). Du kan till exempel exportera listan över säkerhets kopierings principer eller säkerhets kopierings katalogen. Du kan sedan importera CSV-filen till ett kalkyl blads program för analys.

#### <a name="to-save-a-list-in-a-comma-separated-value-csv-file"></a>Så här sparar du en lista i en fil med kommaavgränsade värden (CSV)
1. Klicka på Skriv bords ikonen för att starta StorSimple Snapshot Manager. 
2. I fönstret **omfattning** högerklickar du på en nod eller expanderar noden och högerklickar på ett objekt i **resultat** fönstret och klickar sedan på **Exportera lista**. 
3. Dialog rutan **Exportera lista** visas. Fyll i formuläret enligt följande: 
   
   1. I rutan **fil namn** skriver du ett namn på CSV-filen eller klickar på pilen för att välja i list rutan.
   2. Klicka på pilen i rutan fil **format** och välj en filtyp i list rutan.
   3. Om du bara vill spara markerade objekt markerar du raderna och klickar sedan på kryss rutan **Spara endast markerade rader** . Om du vill spara alla exporterade listor avmarkerar du kryss rutan **Spara endast markerade rader** .
   4. Klicka på **Spara**.
      
      ![Exportera lista som en fil med kommaavgränsade värden](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Export_List.png) 

## <a name="help"></a>Hjälp
Du kan använda **Hjälp** -menyn för att Visa tillgänglig Onlinehjälp för StorSimple Snapshot Manager och MMC.

#### <a name="to-view-available-online-help"></a>Visa tillgänglig onlinehjälp
1. Klicka på Skriv bords ikonen för att starta StorSimple Snapshot Manager.
2. I fönstret **omfattning** högerklickar du på en nod eller expanderar noden och högerklickar på ett objekt i **resultat** fönstret och klickar sedan på **Hjälp**. 

## <a name="next-steps"></a>Nästa steg
* Läs mer om [användar gränssnittet för StorSimple-Snapshot Manager](storsimple-use-snapshot-manager.md).
* Lär dig mer om hur du [använder StorSimple Snapshot Manager för att administrera din StorSimple-lösning](storsimple-snapshot-manager-admin.md).

