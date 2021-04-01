---
title: Återställa en volym från en säkerhets kopia på en StorSimple 8000-serien | Microsoft Docs
description: Förklarar hur du använder säkerhets kopierings katalogen StorSimple Enhetshanteraren-tjänst för att återställa en StorSimple-volym från en säkerhets kopia.
services: storsimple
documentationcenter: NA
author: alkohli
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.date: 07/15/2020
ms.author: alkohli
ms.openlocfilehash: ff70df225b5ed27960c96889d409d7005f0fbcc4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94950744"
---
# <a name="restore-a-storsimple-volume-from-a-backup-set"></a>Återställa en StorSimple-volym från en säkerhetskopia

## <a name="overview"></a>Översikt

I den här självstudien beskrivs återställnings åtgärden som utförs på en StorSimple 8000-serie enhet med en befintlig säkerhets kopia. Använd bladet **säkerhets kopierings katalog** för att återställa en volym från en lokal eller moln säkerhets kopia. Bladet **säkerhets kopierings katalog** visar alla säkerhets kopierings uppsättningar som skapas när manuella eller automatiserade säkerhets kopieringar görs. Återställnings åtgärden från en säkerhets kopia tar volymen online omedelbart medan data hämtas i bakgrunden.

## <a name="before-you-restore"></a>Innan du återställer

Innan du påbörjar en återställning bör du gå igenom följande varningar:

* **Du måste koppla från volymen** – ta volymen offline på både värden och enheten innan du påbörjar återställnings åtgärden. Även om återställnings åtgärden automatiskt tar volymen online på enheten, måste du manuellt ta enheten online på värden. Du kan ta volymen online på värden så snart volymen är online på enheten. (Du behöver inte vänta tills återställningen är slutförd.) För procedurer, gå till [ta en volym offline](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline).

* **Volym typ efter återställning** – borttagna volymer återställs baserat på typen i ögonblicks bilden. det vill säga att volymer som har fästs lokalt återställs när lokalt fästa volymer och volymer som har flyttats återställs som nivå volymer.

    För befintliga volymer åsidosätter den aktuella användnings typen för volymen den typ som lagras i ögonblicks bilden. Om du till exempel återställer en volym från en ögonblicks bild som togs när volym typen skiktades och volym typen nu har fästs lokalt (på grund av en konverterings åtgärd som utfördes) återställs volymen som en lokalt fäst volym. Om en befintlig lokalt fäst volym expanderas och sedan återställs från en äldre ögonblicks bild som togs när volymen var mindre, behåller den återställda volymen den aktuella expanderade storleken.

    Det går inte att konvertera en volym från en nivå volym till en lokalt fäst volym eller från en lokalt fäst volym till en nivå volym medan volymen återställs. Vänta tills återställnings åtgärden har slutförts och konvertera sedan volymen till en annan typ. Om du vill ha mer information om hur du konverterar en volym går du till [ändra volym typ](storsimple-8000-manage-volumes-u2.md#change-the-volume-type). 

* **Volym storleken avspeglas i den återställda volymen** – det här är viktigt om du återställer en lokalt fäst volym som har tagits bort (eftersom lokalt fästa volymer är helt etablerade). Kontrol lera att du har tillräckligt med utrymme innan du försöker återställa en lokalt fäst volym som tidigare har tagits bort.

* Det **går inte att expandera en volym medan den återställs** – vänta tills återställningen har slutförts innan du försöker expandera volymen. Om du vill ha mer information om hur du expanderar en volym går du till [ändra en volym](storsimple-8000-manage-volumes-u2.md#modify-a-volume).

* **Du kan utföra en säkerhets kopia medan du återställer en lokal volym** – för procedurer går du till [använda tjänsten StorSimple Enhetshanteraren för att hantera säkerhets kopierings principer](storsimple-8000-manage-backup-policies-u2.md).

* **Du kan avbryta en återställnings åtgärd** – om du avbryter återställnings jobbet återställs volymen till det tillstånd den var i innan du påbörjade återställnings åtgärden. Om du vill ha procedurer går du till [Avbryt ett jobb](storsimple-8000-manage-jobs-u2.md#cancel-a-job).

## <a name="how-does-restore-work"></a>Hur fungerar återställning

För enheter som kör uppdatering 4 eller senare implementeras en termisk karta återställning. När värd begär Anden för att komma åt data når enheten spåras dessa begär Anden och en termisk karta skapas. Hög begär ande frekvens resulterar i data segment med högre värme, medan lägre frekvens för begäran översätts till segment med lägre värme. Du måste ha åtkomst till data minst två gånger för att markeras som _aktiva_. En fil som ändras markeras också som _aktiv_. När du har initierat återställningen sker proaktiva hydrering av data baserat på termisk karta. För versioner som är tidigare än uppdatering 4 laddas data ned under återställningen enbart baserat på åtkomst.

Följande villkor gäller för termisk karta-baserade återställningar:

* Termisk karta spårning är bara aktiverat för volymer med flera nivåer och lokalt fästa volymer stöds inte.

* Termisk karta-baserad återställning stöds inte när en volym klonas till en annan enhet. 

* Om det finns en återställning på plats och en lokal ögonblicks bild för volymen som ska återställas finns på enheten, kommer vi inte att återskapas (eftersom data redan finns lokalt). 

* Som standard initieras ÅTERUPPVÄCKNING-jobb som proaktivt reserverar data baserat på termisk karta när du återställer. 

I uppdatering 4 kan du använda Windows PowerShell-cmdlet: ar för att köra ÅTERUPPVÄCKNING-jobb, avbryta ett ÅTERUPPVÄCKNING-jobb och hämta status för ÅTERUPPVÄCKNING-jobbet.

* `Get-HcsRehydrationJob` -Denna cmdlet hämtar status för ÅTERUPPVÄCKNING-jobbet. Ett enskilt ÅTERUPPVÄCKNING-jobb utlöses för en volym.

* `Set-HcsRehydrationJob` – Med den här cmdleten kan du pausa, stoppa, återuppta ÅTERUPPVÄCKNING-jobbet när ÅTERUPPVÄCKNING pågår.

Mer information om ÅTERUPPVÄCKNING-cmdlets finns i [referens för Windows PowerShell-cmdlet för StorSimple](/powershell/module/hcs/?viewFallbackFrom=winserverr2-ps).

Med automatisk ÅTERUPPVÄCKNING förväntas högre höga Läs prestanda. Den faktiska storleken på förbättringarna beror på olika faktorer, till exempel åtkomst mönster, data omsättning och data typ. 

Om du vill avbryta ett ÅTERUPPVÄCKNING-jobb kan du använda PowerShell-cmdleten. [Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md)om du vill inaktivera ÅTERUPPVÄCKNING-jobb permanent för alla framtida återställningar.

## <a name="how-to-use-the-backup-catalog"></a>Använda säkerhets kopierings katalogen

Bladet **säkerhets kopierings katalog** innehåller en fråga som hjälper dig att begränsa valet av säkerhets kopierings uppsättning. Du kan filtrera de säkerhets kopior som hämtas baserat på följande parametrar:

* **Tidsintervall** – datum-och tidsintervallet när säkerhets kopian skapades.
* **Enhet** – enheten där säkerhets kopian skapades.
* **Säkerhets kopierings princip** eller **volym** – den säkerhets kopierings princip eller volym som är associerad med den här säkerhets kopian.

De filtrerade säkerhets kopiorna är sedan tabellen baserat på följande attribut:

* **Namn** – namnet på den säkerhets kopierings princip eller volym som är associerad med säkerhets kopian.
* **Typ** – säkerhets kopierings uppsättningar kan vara lokala ögonblicks bilder eller moln ögonblicks bilder. En lokal ögonblicks bild är en säkerhets kopia av alla dina volym data som lagras lokalt på enheten, medan en moln ögonblicks bild refererar till säkerhets kopian av volym data som finns i molnet. Lokala ögonblicks bilder ger snabbare åtkomst, medan moln ögonblicks bilder väljs för data återhämtning.
* **Size** – den faktiska storleken på säkerhets kopian.
* **Skapat** den – datum och tid då säkerhets kopieringarna skapades. 
* **Volymer** – antalet volymer som är associerade med säkerhets kopian.
* **Initierad** – säkerhets kopieringarna kan initieras automatiskt i enlighet med ett schema eller manuellt av en användare. (Du kan använda en säkerhets kopierings princip för att schemalägga säkerhets kopieringar. Du kan också använda alternativet för att **göra säkerhets kopiering** för att göra en interaktiv eller säkerhets kopiering på begäran.)

## <a name="how-to-restore-your-storsimple-volume-from-a-backup"></a>Återställa din StorSimple-volym från en säkerhets kopia

Du kan använda bladet **säkerhets kopierings katalog** för att återställa din StorSimple-volym från en speciell säkerhets kopia. Tänk dock på att om du återställer en volym återställs volymen till det tillstånd den var i när säkerhets kopieringen gjordes. Alla data som lades till efter säkerhets kopieringen går förlorade.

> [!WARNING]
> Om du återställer från en säkerhets kopia ersätts befintliga volymer från säkerhets kopian. Detta kan leda till förlust av data som har skrivits efter att säkerhets kopieringen vidtogs.


### <a name="to-restore-your-volume"></a>Så här återställer du volymen
1. Gå till StorSimple Enhetshanteraren-tjänsten och klicka sedan på **säkerhets kopierings katalog**.

2. Välj en säkerhets kopierings uppsättning enligt följande:
   
   1. Ange tidsintervallet.
   2. Välj lämplig enhet.
   3. I list rutan väljer du den volym eller säkerhets kopierings princip för säkerhets kopian som du vill välja.
   4. Klicka på **tillämpa** för att köra den här frågan.

      De säkerhets kopior som är associerade med den valda volymen eller säkerhets kopierings principen visas i listan över säkerhets kopior.
   
      ![Lista över säkerhets kopie uppsättningar](./media/storsimple-8000-restore-from-backup-set-u2/bucatalog.png)     
     
3. Expandera säkerhets kopian om du vill visa de associerade volymerna. Dessa volymer måste tas offline på värden och enheten innan du kan återställa dem. Öppna volymerna på bladet **volymer** på enheten och följ sedan stegen i [ta en volym offline](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline) för att ta dem offline.
   
   > [!IMPORTANT]
   > Se till att du först har tagit volymerna offline på värden innan du tar volymerna offline på enheten. Om du inte tar volymerna offline på värden kan det potentiellt leda till skadade data.
   
4. Gå tillbaka till fliken **säkerhets kopierings katalog** och välj en säkerhets kopierings uppsättning. Högerklicka på och välj sedan **Återställ** på snabb menyn.

    ![Lista över säkerhets kopiering 2](./media/storsimple-8000-restore-from-backup-set-u2/restorebu1.png)

5. Du uppmanas att bekräfta. Granska återställnings informationen och markera sedan kryss rutan Bekräfta.
   
    ![Bekräftelsesida](./media/storsimple-8000-restore-from-backup-set-u2/restorebu2.png)

7. Klicka på **Återställ**. Detta initierar ett återställnings jobb som du kan visa genom att öppna sidan **jobb** .

   ![Bekräftelse sida 2](./media/storsimple-8000-restore-from-backup-set-u2/restorebu5.png)

8. När återställningen är klar kontrollerar du att volymens innehåll ersätts av volymer från säkerhets kopian.


## <a name="if-the-restore-fails"></a>Om återställningen Miss lyckas

Du får en avisering om återställnings åtgärden Miss lyckas av någon anledning. Om detta inträffar uppdaterar du säkerhets kopierings listan för att kontrol lera att säkerhets kopian fortfarande är giltig. Om säkerhets kopieringen är giltig och du återställer från molnet kan problemen orsakas av anslutnings problem.

Slutför återställnings åtgärden genom att ta volymen offline på värden och försök utföra återställnings åtgärden igen. Observera att eventuella ändringar av volym data som utfördes under återställnings processen går förlorade.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [hanterar StorSimple-volymer](storsimple-8000-manage-volumes-u2.md).
* Lär dig hur du [använder tjänsten StorSimple Enhetshanteraren för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).