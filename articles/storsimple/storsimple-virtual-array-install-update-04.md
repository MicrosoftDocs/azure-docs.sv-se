---
title: Installera uppdateringar på virtuell StorSimple-matris | Microsoft Docs
description: Beskriver hur du använder StorSimple-webbgränssnittet för virtuella matriser för att tillämpa uppdatering 0,4 med hjälp av metoden Azure Portal och snabb korrigering.
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
ms.date: 02/07/2017
ms.author: alkohli
ms.openlocfilehash: cc458f91a9b67ddd54e706575d29b8fc8618a2f7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94534304"
---
# <a name="install-update-04-on-your-storsimple-virtual-array"></a>Installera Uppdatering 0,4 på din virtuella StorSimple-matris

## <a name="overview"></a>Översikt

I den här artikeln beskrivs de steg som krävs för att installera uppdatering 0,4 på din virtuella StorSimple-matris via det lokala webb gränssnittet och via Azure Portal. Du måste tillämpa program uppdateringar eller hotfixar för att hålla din StorSimple virtuella matris uppdaterad. 

Kom ihåg enheten startas om när du installerar en uppdatering eller korrigering. Med tanke på att den virtuella StorSimple-matrisen är en enskild Node-enhet avbryts alla i/O-åtgärder och enheten upplever drift stopp. 

Innan du installerar en uppdatering rekommenderar vi att du tar volymerna eller resurserna offline på värden först och sedan enheten. Detta minskar risken för skadade data.

> [!IMPORTANT]
> Om du kör uppdatering 0,1 eller GA-program versioner måste du använda snabb korrigerings metoden via det lokala webb gränssnittet för att installera uppdatering 0,3. Om du kör uppdatering 0,2 eller senare, rekommenderar vi att du installerar uppdateringarna via Azure Portal.
 

## <a name="use-the-local-web-ui"></a>Använd det lokala webb gränssnittet

Det finns två steg när du använder det lokala webb gränssnittet:

* Hämta uppdateringen eller snabb korrigeringen
* Installera uppdateringen eller snabb korrigeringen

### <a name="download-the-update-or-the-hotfix"></a>Hämta uppdateringen eller snabb korrigeringen

Utför följande steg för att hämta programuppdateringen från Microsoft Update Catalog.

#### <a name="to-download-the-update-or-the-hotfix"></a>Hämta uppdateringen eller snabb korrigeringen

1. Starta Internet Explorer och gå till [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com) .

2. Om det här är första gången du använder Microsoft Update Catalog på den här datorn klickar du på **Installera** när du uppmanas att installera tillägget för Microsoft Update Catalog.

3. I rutan Sök i Microsoft Update-katalogen anger du Knowledge Base-numret för den snabb korrigering som du vill ladda ned. Ange **3216577** för uppdatering 0,4 och klicka sedan på **Sök**.
   
    Listan med snabb korrigeringar visas, till exempel **StorSimple Virtual Array uppdatering 0,4**.
   
    ![Sökkatalog](./media/storsimple-virtual-array-install-update-04/download1.png)

4. Klicka på **Lägg till**. Uppdateringen läggs till i korgen.

5. Klicka på **View Basket** (Visa korg).

6. Klicka på **Hämta**. Ange eller **Bläddra** till en lokal plats där du vill att nedladdningarna ska läggas. Uppdateringarna hämtas till den angivna platsen och placeras i en undermapp med samma namn som uppdateringen. Mappen kan också kopieras till en nätverksresurs som kan nås från enheten.

7. Öppna den kopierade mappen. du bör se en Microsoft Update fristående paket fil `WindowsTH-KB3011067-x64` . Den här filen används för att installera uppdateringen eller snabb korrigeringen.

### <a name="install-the-update-or-the-hotfix"></a>Installera uppdateringen eller snabb korrigeringen

Innan uppdateringen eller snabb korrigeringen installerades kontrollerar du att uppdateringen eller snabb korrigeringen laddats ned lokalt på värden eller kan nås via en nätverks resurs. 

Använd den här metoden för att installera uppdateringar på en enhet som kör GA eller uppdatering 0,1 program varu versioner. Den här proceduren tar mindre än två minuter att slutföra. Utför följande steg för att installera uppdateringen eller snabb korrigeringen.

#### <a name="to-install-the-update-or-the-hotfix"></a>Installera uppdateringen eller snabb korrigeringen

1. I det lokala webb gränssnittet går du till **Underhåll**  >  **program uppdatering**.
   
    ![Skärm bild som visar den program uppdatering som valts på menyn underhåll.](./media/storsimple-virtual-array-install-update/update1m.png)

2. I **Uppdatera fil Sök väg** anger du fil namnet för uppdateringen eller snabb korrigeringen. Du kan också bläddra till installations filen för uppdateringen eller hotfixen om den placeras på en nätverks resurs. Klicka på **Applicera**.
   
    ![Skärm bild som visar text rutan Sökväg till uppdaterings fil på sidan program uppdatering.](./media/storsimple-virtual-array-install-update/update2m.png)

3. En varning visas. Eftersom det här är en enskild nod-enhet, när uppdateringen har tillämpats, startar enheten om och det finns avbrott. Klicka på kryss ikonen.
   
   ![Skärm bild som visar en dialog ruta med en varning om nedtid.](./media/storsimple-virtual-array-install-update/update3m.png)

4. Uppdateringen startar. När enheten har uppdaterats startas den om. Det lokala användar gränssnittet är inte tillgängligt under denna varaktighet.
   
    ![Skärm bild som visar ett meddelande om att uppdateringen har slutförts.](./media/storsimple-virtual-array-install-update/update5m.png)

5. När omstarten är klar tas du till **inloggnings** sidan. Kontrol lera att enhetens program vara har uppdaterats genom att gå till **Underhåll**  >  **program uppdatering** i det lokala webb gränssnittet. Den program varu version som visas ska vara **10.0.0.0.0.10289.0** för uppdatering 0,4.
   
   > [!NOTE]
   > Vi rapporterar program versioner på ett något annorlunda sätt i det lokala webb gränssnittet och Azure Portal. Till exempel kan de lokala Web UI-rapporterna **10.0.0.0.0.10289** och Azure Portal rapporterar **10.0.10289.0** för samma version.
   
    ![Skärm bild som visar sidan program uppdatering med den aktuella program varu versionen.](./media/storsimple-virtual-array-install-update/update6m.png)

## <a name="use-the-azure-portal"></a>Använda Azure-portalen

Om du kör uppdatering 0,2 och senare, rekommenderar vi att du installerar uppdateringar via Azure Portal. Portal proceduren kräver att användaren skannar, laddar ned och sedan installerar uppdateringarna. Den här proceduren tar cirka 7 minuter att slutföra. Utför följande steg för att installera uppdateringen eller snabb korrigeringen.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-04.md)]

När installationen är klar (enligt vad som anges i jobb status på 100%) går du till StorSimple Enhetshanteraren-tjänsten. Välj **enheter** och välj sedan och klicka på den enhet som du vill uppdatera i listan över enheter som är anslutna till den här tjänsten. I bladet **Inställningar** går du till avsnittet **Hantera** och väljer **enhets uppdateringar**. Den program varu version som visas ska vara **10.0.10289.0**.


## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur [du administrerar din virtuella StorSimple-matris](storsimple-ova-web-ui-admin.md).

