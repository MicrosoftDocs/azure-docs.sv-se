---
title: Visa och hantera aviseringar för virtuell StorSimple-matris
description: Beskriver StorSimple av aviserings villkor och allvarlighets grad för virtuella matriser och hur du använder tjänsten StorSimple Manager för att hantera aviseringar.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 97ee25a1-0ec3-4883-9a0a-54b722598462
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/12/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 12fcc9996697f3bbba35826d79bec238bfb0f8b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95993004"
---
# <a name="use-storsimple-device-manager-to-manage-alerts-for-the-storsimple-virtual-array"></a>Använd StorSimple Enhetshanteraren för att hantera aviseringar för den virtuella StorSimple-matrisen

## <a name="overview"></a>Översikt

Funktionen aviseringar i StorSimple Enhetshanteraren-tjänsten ger dig ett sätt att granska och rensa aviseringar som rör virtuella StorSimple-matriser i real tid. Du kan använda aviseringarna på bladet **tjänst Sammanfattning** för att centralt övervaka hälso problemen för dina virtuella StorSimple-matriser och den övergripande Microsoft Azure StorSimple lösningen.

I den här självstudien beskrivs hur du konfigurerar aviserings meddelanden, vanliga aviserings villkor, allvarlighets grader för aviseringar och hur du visar och spårar aviseringar. Dessutom innehåller den snabb referens tabeller för varningar, som gör att du snabbt kan hitta en speciell avisering och svara på lämpligt sätt.

![Sidan aviseringar](./media/storsimple-virtual-array-manage-alerts/alerts1.png)

## <a name="configure-alert-settings"></a>Konfigurera aviseringsinställningar

Du kan välja om du vill bli informerad via e-post om aviserings villkoren för var och en av dina virtuella StorSimple-matriser. Dessutom kan du identifiera andra aviserings mottagare genom att ange deras e-postadresser i rutan **ytterligare e-postmottagare** , avgränsade med semikolon.

> [!NOTE]
> Du kan ange högst 20 e-postadresser per virtuell matris.

När du har aktiverat e-postavisering för en virtuell matris kommer medlemmar i meddelande listan att få ett e-postmeddelande varje gången en kritisk varning inträffar. Meddelandena skickas från *StorSimple-Alerts-noreply \@ mail.windowsazure.com* och beskriver varnings villkoret. Mottagare kan klicka på **Avbryt** för att ta bort sig själva från listan med e-postaviseringar.

#### <a name="to-enable-email-notification-for-alerts"></a>Så här aktiverar du e-postavisering för aviseringar

1. Gå till StorSimple Enhetshanteraren-tjänsten och välj i avsnittet **hantering** och klicka på **enheter**. I listan med enheter som visas väljer du och klickar på din enhet.
   
    ![aviserings inställningar](./media/storsimple-virtual-array-manage-alerts/alerts2.png)
2. Då öppnas bladet **Inställningar** . I avsnittet **enhets inställningar** väljer du **Allmänt**. Då öppnas bladet **allmänna inställningar** .
   
    ![Skärm bild som visar fönstret enhets inställningar med avsnittet varnings inställningar som kallas för.](./media/storsimple-virtual-array-manage-alerts/alerts4.png)
3. I bladet **allmänna inställningar** går du till avsnittet **aviserings inställningar** och anger följande:
   
   1. I fältet **Aktivera e-postavisering** väljer du **Ja**.
   2. I fältet **e-posttjänst administratörer** väljer du **Ja** om du vill att tjänst administratören och alla medadministratörer ska få aviserings meddelanden.
   3. I fältet **ytterligare e-postmottagare** anger du e-postadresserna till alla andra mottagare som ska få aviserings meddelanden. Ange namn i formatet *någon \@ Somewhere.com*. Använd semikolon för att avgränsa e-postadresserna. Du kan konfigurera högst 20 e-postadresser per virtuell enhet.
      
       ![Skärm bild som visar information om aviserings inställningar med de inställningar som beskrivs i det här steget.](./media/storsimple-virtual-array-manage-alerts/alerts6.png)
   4. Skicka ett test-e-postmeddelande genom att klicka på **Skicka test-e-post**. Tjänsten StorSimple Enhetshanteraren visar status meddelanden när det vidarebefordrar test meddelandet.
      
       ![Skärm bild som visar en informations dialog ruta som verifierar test-e-postmeddelandet.](./media/storsimple-virtual-array-manage-alerts/alerts7.png)
      
      > [!NOTE]
      > Om test aviserings meddelandet inte kan skickas visar tjänsten StorSimple Enhetshanteraren ett lämpligt meddelande. Klicka på **OK**, vänta några minuter och försök sedan att skicka ett test meddelande igen.
      >
      >
   5. Klicka på **Spara** längst ned på sidan för att spara konfigurationen. Klicka på **Ja** när du uppmanas att bekräfta åtgärden.
      
      ![Skärm bild som visar fönstret inställningar med knappen Spara vald.](./media/storsimple-virtual-array-manage-alerts/alerts10.png)

## <a name="common-alert-conditions"></a>Vanliga aviserings villkor

Din virtuella StorSimple-matris genererar aviseringar som svar på olika villkor. Följande är de vanligaste typerna av aviserings villkor:

* **Anslutnings problem** – dessa aviseringar inträffar när det är svårt att överföra data. Kommunikations problem kan uppstå under överföring av data till och från Azure Storage-kontot eller på grund av bristande anslutning mellan de virtuella enheterna och tjänsten StorSimple Enhetshanteraren. Kommunikations problem är några av de svåra att åtgärda eftersom det finns så många fel punkter. Först bör du först kontrol lera att nätverks anslutningen och Internet åtkomst är tillgängliga innan du fortsätter till mer avancerad fel sökning. Information om portar och brand Väggs inställningar finns i [system krav för StorSimple Virtual Array](storsimple-ova-system-requirements.md). Om du behöver hjälp med fel sökning går du till [Felsöka med cmdleten Test-Connection](./storsimple-8000-troubleshoot-deployment.md).
* **Prestanda problem** – de här aviseringarna orsakas när systemet inte fungerar optimalt, till exempel när det är under en tung belastning.

Dessutom kan du se aviseringar relaterade till säkerhet, uppdateringar eller jobb haverier.

## <a name="alert-severity-levels"></a>Allvarlighets nivåer för aviseringar

Aviseringar har olika allvarlighets grader, beroende på vilken effekt som aviserings situationen kommer att ha och behovet av svar på aviseringen. Allvarlighetsgraderna är:

* **Kritisk** – den här aviseringen är som svar på ett villkor som påverkar systemets prestanda. Åtgärd krävs för att säkerställa att StorSimple-tjänsten inte avbryts.
* **Varning** – det här tillståndet kan bli kritiskt om det inte är löst. Du bör undersöka situationen och vidta alla åtgärder som krävs för att ta bort problemet.
* **Information** – den här aviseringen innehåller information som kan vara användbar vid spårning och hantering av systemet.

## <a name="view-and-track-alerts"></a>Visa och spåra aviseringar

Bladet StorSimple Enhetshanteraren tjänst Sammanfattning ger dig en snabb överblick över antalet aviseringar på dina virtuella enheter, ordnat efter allvarlighets grad.

![Instrument panel för aviseringar](./media/storsimple-virtual-array-manage-alerts/alerts14.png)

Klicka på allvarlighets grad för att öppna bladet **aviseringar** . Resultaten innehåller bara de aviseringar som matchar allvarlighets graden.

![Aviserings rapport begränsad till aviserings typ](./media/storsimple-virtual-array-manage-alerts/alerts15.png)

Klicka på en avisering i listan om du vill ha mer information om aviseringen, inklusive den senaste gången aviseringen rapporterades, antalet förekomster av aviseringen på enheten och den rekommenderade åtgärden för att lösa aviseringen.

![Aviserings lista och information](./media/storsimple-virtual-array-manage-alerts/alerts16.png)

Du kan kopiera aviserings informationen till en textfil om du behöver skicka informationen till Microsoft Support. När du har följt rekommendationen och löst aviserings tillståndet lokalt bör du ta bort aviseringen från listan. Välj aviseringen i listan och klicka sedan på **Rensa**. Om du vill ta bort flera aviseringar markerar du varje avisering, klickar på en kolumn förutom kolumnen **avisering** och klickar sedan på **Rensa** när du har valt att alla aviseringar ska rensas.

När du klickar på **Rensa** har du möjlighet att ange kommentarer om aviseringen och de steg som du har vidtagit för att lösa problemet.

![aviserings kommentarer](./media/storsimple-virtual-array-manage-alerts/alerts17.png)

Vissa händelser raderas av systemet om en annan händelse utlöses med ny information.

## <a name="sort-and-review-alerts"></a>Sortera och granska aviseringar

På bladet **aviseringar** kan du visa upp till 250 aviseringar. Om du har överskridit det antalet aviseringar visas inte alla aviseringar i standardvyn. Du kan kombinera följande fält för att anpassa vilka aviseringar som visas:

* **Status** – du kan visa antingen **aktiva** eller **rensade** aviseringar. Aktiva aviseringar aktive ras fortfarande i systemet, medan rensade aviseringar antingen har rensats manuellt av en administratör eller program mässigt rensas eftersom systemet har uppdaterat varnings villkoret med ny information.
* **Allvarlighets grad** – du kan visa aviseringar för alla allvarlighets nivåer (kritisk, varning, information) eller bara en viss allvarlighets grad, till exempel endast kritiska aviseringar.
* **Källa** – du kan visa aviseringar från alla källor eller begränsa aviseringarna till de som kommer från antingen tjänsten eller en eller flera virtuella enheter.
* **Tidsintervall** – genom att ange **från** -och **till** -datum och tidsstämplar kan du titta på aviseringar under den tids period som du är intresse rad av.

## <a name="alerts-quick-reference"></a>Snabb referens för varningar

I följande tabeller visas några av de StorSimple-aviseringar som du kan stöta på, samt ytterligare information och rekommendationer som är tillgängliga. StorSimple virtuella array-aviseringar finns i någon av följande kategorier:

* [Aviseringar om moln anslutning](#cloud-connectivity-alerts)
* [Konfigurations aviseringar](#configuration-alerts)
* [Aviseringar om jobb haveri](#job-failure-alerts)
* [Prestanda varningar](#performance-alerts)
* [Säkerhetsaviseringar](#security-alerts)

### <a name="cloud-connectivity-alerts"></a>Aviseringar om moln anslutning

| Aviserings text | Händelse | Mer information/rekommenderade åtgärder |
|:--- |:--- |:--- |
| Enhets <*enhets namnet*> är inte anslutet till molnet. |Den namngivna enheten kan inte ansluta till molnet. |Det gick inte att ansluta till molnet. Detta kan bero på något av följande:<ul><li>Det kan finnas ett problem med nätverks inställningarna på enheten.</li><li>Det kan finnas ett problem med lagrings kontots autentiseringsuppgifter.</li></ul>Mer information om fel sökning av anslutnings problem finns i enhetens [lokala webb gränssnitt](storsimple-ova-web-ui-admin.md) . |

### <a name="configuration-alerts"></a>Konfigurations aviseringar

| Aviserings text | Händelse | Mer information/rekommenderade åtgärder |
|:--- |:--- |:--- |
| Konfiguration av lokal virtuell enhet stöds inte. |Långsamma prestanda. |Den aktuella konfigurationen kan resultera i försämrade prestanda. Kontrol lera att servern uppfyller minimi kraven för konfiguration. Mer information finns i krav för [StorSimple Virtual Array](storsimple-ova-system-requirements.md). |
| Du håller på att ta slut på det allokerade disk utrymmet på <*enhets namnet* \> . |Varning om disk utrymme. |Det allokerade disk utrymmet håller på att ta slut. För att frigöra utrymme bör du överväga att flytta arbets belastningar till en annan volym eller dela eller ta bort data. |

### <a name="job-failure-alerts"></a>Aviseringar om jobb haveri

| Aviserings text | Händelse | Mer information/rekommenderade åtgärder |
|:--- |:--- |:--- |
| Det gick inte att slutföra säkerhets kopieringen av <*enhets namnet* \> . |Säkerhets kopierings jobbet kunde inte utföras. |Det gick inte att skapa en säkerhets kopia. Överväg något av följande:<ul><li>Anslutnings problem kan hindra säkerhets kopieringen från att slutföras. Se till att det inte finns några anslutnings problem. Mer information om fel sökning av anslutnings problem finns i det [lokala webb gränssnittet](storsimple-ova-web-ui-admin.md) för den virtuella enheten.</li><li>Du har nått den tillgängliga lagrings gränsen. Överväg att ta bort eventuella säkerhets kopior som inte längre behövs för att frigöra utrymme.</li></ul> Lös problemen, rensa aviseringen och försök igen. |
| Det gick inte att utföra kloningen av <*enhets namnet* \> . |Det gick inte att klona jobbet. |Det gick inte att skapa en klon. Överväg något av följande:<ul><li>Säkerhets kopierings listan kanske inte är giltig. Uppdatera listan för att kontrol lera att den fortfarande är giltig.</li><li>Anslutnings problem kan hindra klonings åtgärden från att slutföras. Se till att det inte finns några anslutnings problem.</li><li>Du har nått den tillgängliga lagrings gränsen. Överväg att ta bort eventuella säkerhets kopior som inte längre behövs för att frigöra utrymme.</li></ul>Lös problemen, rensa aviseringen och försök igen. |

### <a name="networking-alerts"></a>Nätverks aviseringar

| Aviserings text | Händelse | Mer information/rekommenderade åtgärder |
|:--- |:--- |:--- |
| Det gick inte att ansluta till Autentiseringstjänsten. |Datapath-fel |Det går inte att hitta den URL som används för autentisering. Se till att brand Väggs reglerna innehåller de URL-mönster som anges för StorSimple-enheten. Mer information om URL-mönster i Azure Portal finns i [StorSimple Virtual Array Network Requirements](storsimple-ova-system-requirements.md#url-patterns-for-firewall-rules).|

### <a name="performance-alerts"></a>Prestanda varningar

| Aviserings text | Händelse | Mer information/rekommenderade åtgärder |
|:--- |:--- |:--- |
| Du upplever oväntade fördröjningar i data överföringen. |Långsam data överföring. |Begränsnings fel inträffar när du överskrider skalbarhets målen för en lagrings tjänst. Lagrings tjänsten gör detta för att säkerställa att ingen enskild klient eller klient organisation kan använda tjänsten på bekostnad av andra. Mer information om hur du felsöker ditt Azure Storage-konto finns i [övervaka, diagnostisera och felsöka Microsoft Azure Storage](../storage/common/storage-monitoring-diagnosing-troubleshooting.md). |
| Du har ont om lokalt reservations disk utrymme på <*enhets namnet* \> . |Långsam svars tid. |10% av den totala etablerade storleken för <*enhets namnet* \> är reserverat på den lokala enheten och du börjar nu ta slut på det reserverade utrymmet. Arbets belastningen på <*enhets namnet* \> genererar en högre andel omsättning eller så kanske du nyligen har migrerat en stor mängd data. Detta kan resultera i försämrade prestanda. Överväg följande åtgärder för att lösa problemet:<ul><li>Öka moln bandbredden till den här enheten.</li><li>Minska eller flytta arbets belastningar till en annan volym eller resurs.</li></ul> |

### <a name="security-alerts"></a>Säkerhetsaviseringar

| Aviserings text | Händelse | Mer information/rekommenderade åtgärder |
|:--- |:--- |:--- |
| Lösen ordet för <*enhets namnet* \> upphör att gälla om <*nummer* \> dagar. |Lösen ords varning. |Ditt lösen ord upphör att gälla om <*nummer* \> dagar. Överväg att ändra ditt lösen ord. Mer information finns i [ändra StorSimple för den virtuella matris enheten](storsimple-virtual-array-change-device-admin-password.md). |

## <a name="next-steps"></a>Nästa steg

* [Lär dig mer om den virtuella StorSimple-matrisen](storsimple-ova-overview.md).