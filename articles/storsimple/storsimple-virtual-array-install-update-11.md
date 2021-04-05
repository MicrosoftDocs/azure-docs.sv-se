---
title: Installera uppdatering 1,1 på StorSimple virtuell matris | Microsoft Docs
description: Beskriver hur du tillämpar uppdateringar med hjälp av Azure Portal och lokalt webb gränssnitt för StorSimple virtuella matris
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/18/2018
ms.author: alkohli
ms.openlocfilehash: 709794dcccf9b100cbb11381ac34cfb86238b22b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94533999"
---
# <a name="install-update-11-on-your-storsimple-virtual-array"></a>Installera uppdatering 1,1 på din virtuella StorSimple-matris

## <a name="overview"></a>Översikt

I den här artikeln beskrivs de steg som krävs för att installera uppdatering 1,1 på din virtuella StorSimple-matris via det lokala webb gränssnittet och via Azure Portal.

Du tillämpar program uppdateringarna eller hotfixarna för att hålla din StorSimple virtuella matris uppdaterad. Innan du installerar en uppdatering rekommenderar vi att du tar volymerna eller resurserna offline på värden först och sedan enheten. Detta minskar risken för skadade data. När volymerna eller resurserna är offline bör du också göra en manuell säkerhets kopiering av enheten.

> [!IMPORTANT]
> - Uppdatering 1,1 motsvarar **10.0.10307.0** program varu version på enheten. Information om vad som är nytt i den här uppdateringen finns i [viktig information om uppdatering 1,1](storsimple-virtual-array-update-11-release-notes.md).
>
> - Kom ihåg enheten startas om när du installerar en uppdatering eller korrigering. Med tanke på att den virtuella StorSimple-matrisen är en enskild Node-enhet avbryts alla i/O-åtgärder och enheten upplever drift stopp.
>
> - Uppdatering 1,1 finns bara i Azure Portal om den virtuella matrisen kör uppdatering 1. För virtuella matriser som kör uppdatering 0,6-versioner måste du först installera uppdatering 1,0 och sedan använda uppdatering 1,1.

## <a name="use-the-azure-portal"></a>Använda Azure-portalen

Om du kör uppdatering 0,2 och senare, rekommenderar vi att du installerar uppdateringar via Azure Portal. Portal proceduren kräver att användaren skannar, laddar ned och sedan installerar uppdateringarna. Beroende på vilken program version som den virtuella matrisen körs på, är det en annan att tillämpa uppdatering via Azure Portal.

 - Om den virtuella matrisen kör uppdatering 1 installerar Azure Portal direkt uppdatering 1,1 (10.0.10307.0) på enheten. Den här proceduren tar cirka 10 minuter att slutföra.
 - Om den virtuella matrisen kör uppdatering 0,6 görs uppdateringen i två steg. Azure Portal installerar först uppdatering 1,0 (10.0.10296.0) på enheten. Den virtuella matrisen startas om och portalen installerar sedan uppdatering 1,1 (10.0.10307.0) på enheten. Den här proceduren tar cirka 15 minuter att slutföra.


[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-11.md)]

När installationen är klar går du till StorSimple Enhetshanteraren-tjänsten. Välj **enheter** och välj sedan och klicka på enheten som du precis har uppdaterat. Gå till **inställningar > hantera > enhets uppdateringar**. Den program varu version som visas ska vara **10.0.10307.0**.

![Program varu version efter uppdatering](./media/storsimple-virtual-array-install-update-11/azupdate17m2.png)

## <a name="use-the-local-web-ui"></a>Använd det lokala webb gränssnittet

Det finns två steg när du använder det lokala webb gränssnittet:

* Hämta uppdateringen eller snabb korrigeringen
* Installera uppdateringen eller snabb korrigeringen

> [!IMPORTANT] 
> **Fortsätt bara med uppdateringen om du kör uppdatering 1 (10.0.10296.0). Om du kör uppdatering 0,6 installerar du [uppdatering 1](storsimple-virtual-array-install-update-1.md) på enheten först och installerar sedan uppdatering 1,1.**

### <a name="download-the-update-or-the-hotfix"></a>Hämta uppdateringen eller snabb korrigeringen

Utför följande steg för att ladda ned uppdatering 1,1 från Microsoft Update katalogen.

#### <a name="to-download-the-update-or-the-hotfix"></a>Hämta uppdateringen eller snabb korrigeringen

1. Starta Internet Explorer och gå till [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com) .

2. Om du använder Microsoft Update katalogen för första gången på datorn klickar du på **Installera** när du uppmanas att installera Microsoft Update Catalog-tillägget.

3. I rutan Sök i Microsoft Update-katalogen anger du Knowledge Base-numret för den snabb korrigering som du vill ladda ned. Ange **4337628** för uppdatering 1,1 och klicka sedan på **Sök**.
   
    Listan med snabb korrigeringar visas, till exempel **StorSimple Virtual Array uppdatering 1,1**.
   
    ![Sökkatalog](./media/storsimple-virtual-array-install-update-11/download1.png)

4. Klicka på **Hämta**.

5. Ladda ned de två filerna till en mapp. Du kan också kopiera mappen till en nätverks resurs som kan kontaktas från enheten.

6. Öppna mappen där filerna finns.

    ![Filer i paketet](./media/storsimple-virtual-array-install-update-11/update01folder.png)

    Du ser två filer:
    -  En Microsoft Update fristående paket fil `WindowsTH-KB3011067-x64` . Den här filen används för att uppdatera enhetens program vara.
    - En fil som innehåller ackumulerade uppdateringar för juni `Windows8.1-KB4284815-x64` . För mer information om vad som ingår i den här samlade uppdateringen, gå till [månatlig säkerhets](https://support.microsoft.com/help/4284815/windows-81-update-kb4284815)insamling i juni.

### <a name="install-the-update-or-the-hotfix"></a>Installera uppdateringen eller snabb korrigeringen

Innan du installerar uppdateringen eller hotfixen bör du kontrol lera att:

 - Du har uppdateringen eller snabb korrigeringen som hämtats antingen lokalt på värden eller kan nås via en nätverks resurs.
 - Den virtuella matrisen kör uppdatering 1 (10.0.10296.0). Om du kör uppdatering 0,6 installerar du [uppdatering 1](storsimple-virtual-array-install-update-1.md) först och installerar sedan uppdatering 1,1.

Den här proceduren tar cirka 4 minuter att slutföra. Utför följande steg för att installera uppdateringen eller snabb korrigeringen.

#### <a name="to-install-the-update-or-the-hotfix"></a>Installera uppdateringen eller snabb korrigeringen

1. I det lokala webb gränssnittet går du till **Underhåll**  >  **program uppdatering**. Anteckna den program varu version som du kör. **Fortsätt bara med uppdateringen om du kör uppdatering 1 (10.0.10296.0). Om du kör uppdatering 0,6 installerar du [uppdatering 1](storsimple-virtual-array-install-update-1.md) på enheten först och installerar sedan uppdatering 1,1.**
   
    ![Skärm bild som visar den program uppdatering som valts på menyn underhåll.](./media/storsimple-virtual-array-install-update-11/update1m.png)

2. I **Uppdatera fil Sök väg** anger du fil namnet för uppdateringen eller snabb korrigeringen. Du kan också bläddra till installations filen för uppdateringen eller hotfixen om den placeras på en nätverks resurs. Klicka på **Applicera**.
   
    ![Skärm bild som visar text rutan Sökväg till uppdaterings fil på sidan program uppdatering.](./media/storsimple-virtual-array-install-update-11/update2m.png)

3. En varning visas. Om den virtuella matrisen är en enskild nod enhet startar enheten om och det uppstår avbrott när uppdateringen har tillämpats. Klicka på kryss ikonen.
   
   ![Skärm bild som visar en dialog ruta med en varning om nedtid.](./media/storsimple-virtual-array-install-update-11/update3m.png)

4. Uppdateringen startar. När enheten har uppdaterats startas den om. Det lokala användar gränssnittet är inte tillgängligt under denna varaktighet.
   
    ![Skärm bild som visar ett meddelande om att uppdateringen har slutförts.](./media/storsimple-virtual-array-install-update-11/update5m.png)

5. När omstarten är klar tas du till **inloggnings** sidan. Kontrol lera att enhetens program vara har uppdaterats genom att gå till **Underhåll**  >  **program uppdatering** i det lokala webb gränssnittet. Den program varu version som visas ska vara **10.0.0.0.0.10307** för uppdatering 1,1.
   
   > [!NOTE]
   > Vi rapporterar program versioner på ett något annorlunda sätt i det lokala webb gränssnittet och Azure Portal. Till exempel kan de lokala Web UI-rapporterna **10.0.0.0.0.10307** och Azure Portal rapporterar **10.0.10307.0** för samma version.
   
    ![Skärm bild som visar sidan program uppdatering med den aktuella program varu versionen.](./media/storsimple-virtual-array-install-update-11/update6m.png)

6. Upprepa steg 2-4 för att installera säkerhets korrigeringen för Windows med hjälp av fil `Windows8.1-KB4284815-x64` . Den virtuella matrisen startas om efter installationen och du måste logga in på det lokala webb gränssnittet.


## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur [du administrerar din virtuella StorSimple-matris](storsimple-ova-web-ui-admin.md).
