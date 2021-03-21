---
title: Vanliga frågor om StorSimple-lokalt fästa volymer | Microsoft Docs
description: Innehåller svar på vanliga frågor om StorSimple lokalt fästa volymer.
services: storsimple
documentationcenter: NA
author: manuaery
manager: syadav
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/26/2017
ms.author: manuaery
ms.openlocfilehash: 483fa81b409e1bd740af85b431a86b6c814831e6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96002722"
---
# <a name="storsimple-locally-pinned-volumes-frequently-asked-questions-faq"></a>StorSimple lokalt fästa volymer: vanliga frågor och svar
## <a name="overview"></a>Översikt
Följande är frågor och svar som du kan ha när du skapar en StorSimple lokalt fästa volym, konverterar en nivå volym till en lokalt fixerad volym (och vice versa) eller säkerhetskopierar och återställer en lokalt fäst volym.

Frågor och svar är ordnade i följande kategorier

* Skapa en lokalt fäst volym
* Säkerhetskopiera lokalt fästa
* Konvertera en nivå volym till en lokalt fäst volym
* Återställa en lokalt fäst volym
* Redundansväxla en lokalt fäst volym

## <a name="questions-about-creating-a-locally-pinned-volume"></a>Frågor om att skapa en lokalt fäst volym
**C.** Vilken är den största storleken på en lokalt fixerad volym som jag kan skapa på 8000 serie enheter?

**På enheter** som kör StorSimple 8000-serien uppdatering 3,0 kan du etablera lokalt fästa volymer upp till 8,5 TB eller skiktade volymer upp till 200 tb på 8100-enheten. På den större 8600-enheten kan du etablera lokalt fästa volymer upp till 22,5 TB, eller nivåindelade volymer upp till 500 TB.

**C.** Jag har nyligen uppgraderat min 8100-enhet till uppdatering 3,0 och när jag försöker skapa en lokalt fäst volym är den största tillgängliga storleken bara 6 TB och inte 8,5 TB. Varför kan jag inte skapa en 8,5 TB-volym?

**Om din** enhet kör uppdatering 3,0 kan du etablera lokalt fästa volymer upp till 8,5 TB eller skiktade volymer upp till 200 TB på 8100-enheten. Om din enhet redan har skiktade volymer, kommer utrymmet som är tillgängligt för att skapa en lokalt fäst volym att vara proportionellt lägre än den maximala gränsen. Om t. ex. cirka 106 TB av skiktade volymer redan har etablerats på din 8100-enhet (som är hälften av nivån kapacitet), så minskas den maximala storleken för en lokal volym som du kan skapa på 8100-enheten till 4 TB (ungefär hälften av den högsta fasta volym kapaciteten lokalt).

Eftersom en del lokalt utrymme på enheten används som värd för arbets uppsättningen med skiktade volymer, minskas det tillgängliga utrymmet för att skapa en lokalt fäst volym om enheten har skiktade volymer. Att skapa en lokalt fäst volym minskar proportionellt det tillgängliga utrymmet för volymer på nivå. Följande tabeller sammanfattar den tillgängliga nivå kapaciteten på 8100-och 8600-enheter när lokalt fästa volymer skapas.

#### <a name="update-30"></a>Uppdatering 3,0 

| Lokalt fästa volymer med etablerad kapacitet | Tillgänglig kapacitet som ska tillhandahållas för skiktade volymer – 8100 | Tillgänglig kapacitet som ska tillhandahållas för skiktade volymer – 8600 |
| --- | --- | --- |
| 0 |200 TB |500 TB |
| 1 TB |176,5 TB |477,8 TB |
| 4 TB |105,9 TB |411,1 TB |
| 8,5 TB |0 TB |311,1 TB |
| 10 TB |NA |277,8 TB |
| 15 TB |NA |166,7 TB |
| 22,5 TB |NA |0 TB |

**C.** Varför skapas den lokalt fästa volymen för att skapa en tids krävande åtgärd?

**En.** Lokalt fästa volymer är tjockt allokerat. För att det ska gå att skapa utrymme på enhetens lokala nivåer kan vissa data från befintliga volymer på nivå överföras till molnet under etablerings processen. Och eftersom detta beror på storleken på den volym som har allokerats, befintliga data på enheten och den tillgängliga bandbredden till molnet, kan den tid det tar att skapa en lokal volym vara flera timmar.

**C.** Hur lång tid tar det att skapa en lokalt fäst volym?

**En.** Eftersom lokalt fästa volymer är tjockt allokerat, kan vissa befintliga data från volymer i nivå överföras till molnet under etablerings processen. Den tid det tar att skapa en lokalt fäst volym beror därför på flera faktorer, inklusive volymens storlek, data på enheten och den tillgängliga bandbredden. På en nyligen installerad enhet som inte har några volymer är tiden för att skapa en lokalt fäst volym cirka 10 minuter per terabyte data. Att skapa lokala volymer kan dock ta flera timmar baserat på de faktorer som beskrivs ovan på en enhet som används.

**C.** Jag vill skapa en lokalt fäst volym. Finns det några bästa metoder jag behöver känna till?

**En.** Lokalt fästa volymer är lämpliga för arbets belastningar som kräver lokala garantier för data och som är känsliga för moln fördröjningar. Tänk på följande när du överväger användningen av lokala volymer för någon av dina arbets belastningar:

* Lokalt fästa volymer är tjockt allokerade och när lokala volymer skapas påverkar det tillgängliga utrymmet för volymer med flera nivåer. Därför rekommenderar vi att du börjar med mindre volymer och skalar upp när ditt lagrings behov ökar.
* Etablering av lokala volymer är en tids krävande åtgärd som kan innebära att befintliga data från skiktade volymer skickas till molnet. Därför kan det uppstå sämre prestanda på dessa volymer.
* Etablering av lokala volymer är en tids krävande åtgärd. Den faktiska tiden beror på flera faktorer: storleken på den volym som ska tillhandahållas, data på enheten och tillgänglig bandbredd. Om du inte har säkerhetskopierat dina befintliga volymer till molnet går det långsammare att skapa volymer. Vi föreslår att du tar moln ögonblicks bilder av dina befintliga volymer innan du etablerar en lokal volym.
* Du kan konvertera befintliga skiktade volymer till lokalt fästa volymer, och den här konverteringen innebär etablering av utrymme på enheten för den resulterande lokalt fästa volymen (förutom att ta bort data på nivå av data från molnet). Det här är en tids krävande åtgärd som är beroende av faktorer som vi har diskuterat ovan. Vi rekommenderar att du säkerhetskopierar dina befintliga volymer före konverteringen eftersom processen blir ännu långsammare om befintliga volymer inte säkerhets kopie ras. Enheten kan också få sämre prestanda under den här processen.

Mer information om hur du [skapar en lokalt fäst volym](storsimple-8000-manage-volumes-u2.md#add-a-volume)

**C.** Kan jag skapa flera lokalt fästa volymer samtidigt?

**En.** Ja, men alla lokalt fästa volym skapande-och expansions jobb behandlas sekventiellt.

Lokalt fästa volymer är tjockt allokerade och detta kräver att det lokala utrymmet på enheten skapas (vilket kan leda till att befintliga data från skiktade volymer flyttas till molnet under etablerings processen). Det innebär att om ett etablerings jobb pågår placeras andra jobb för att skapa lokala volymer i kö tills jobbet har slutförts.

Om en befintlig lokal volym expanderas eller en nivå volym konverteras till en lokalt fäst volym, placeras skapandet av en ny lokalt fixerad volym i kö tills det tidigare jobbet har slutförts. Om du expanderar storleken på en lokalt fixerad volym, innebär det att du kan utöka det befintliga lokala utrymmet för volymen. Konvertering från en nivå till lokalt fixerad volym innebär också att du kan skapa lokalt utrymme för den resulterande lokalt fästa volymen. I båda dessa åtgärder är skapandet eller expansionen av lokalt utrymme ett långvarigt jobb.

Du kan visa dessa jobb på bladet **jobb** i StorSimple Enhetshanteraren-tjänsten. Jobbet som bearbetas aktivt uppdateras kontinuerligt för att avspegla förloppet för utrymmes etablering. Återstående lokalt fästa volym jobb markeras som körs, men deras förlopp stoppas och de plockas i den ordning som de står i kö.

**C.** Jag har tagit bort en lokalt fäst volym. Varför visas inte det återvunnet utrymmet på det tillgängliga utrymmet när jag försöker skapa en ny volym?

**En.** Om du tar bort en lokalt fäst volym kanske det tillgängliga utrymmet för nya volymer inte uppdateras omedelbart. StorSimple-Enhetshanteraren tjänsten uppdaterar det lokala utrymmet ungefär varje timme. Vi föreslår att du väntar en timme innan du försöker skapa den nya volymen.

**C.** Stöds lokalt fästa volymer på moln installationen?

**En.** Lokalt fästa volymer stöds inte på moln installationen (8010 och 8020 enheter som tidigare kallades för den virtuella StorSimple-enheten).

**C.** Kan jag använda Azure PowerShell-cmdletar för att skapa och hantera lokalt fästa volymer?

**En.** Nej, du kan inte skapa lokalt fästa volymer via Azure PowerShell-cmdletar (vilken volym du skapar via Azure PowerShell är i nivå av). Vi föreslår också att du inte använder Azure PowerShell-cmdlet: ar för att ändra egenskaper för en lokalt fäst volym, eftersom den har den oönskade påverkan av att ändra volym typen till skiktad.

## <a name="questions-about-backing-up-a-locally-pinned-volume"></a>Frågor om att säkerhetskopiera en lokalt fäst volym
**C.** Stöds lokala ögonblicks bilder av lokalt fästa volymer?

**En.** Ja, du kan ta lokala ögonblicks bilder av dina lokalt fästa volymer. Vi rekommenderar dock starkt att du regelbundet säkerhetskopierar dina lokalt fästa volymer med moln ögonblicks bilder för att säkerställa att dina data skyddas vid en katastrof.

Observera att lokala ögonblicks bilder av lokalt fästa volymer också kan gå ut till molnet och inte garanteras för att stanna kvar i enhetens lokala nivå.

**C.** Finns det några rikt linjer för att hantera lokala ögonblicks bilder för lokalt fästa volymer?

**En.** Frekventa lokala ögonblicks bilder tillsammans med hög data omsättning på den lokalt fästa volymen kan leda till att det lokala utrymmet på enheten förbrukas snabbt och resulterar i data från skiktade volymer som skickas till molnet. Vi rekommenderar därför att du minimerar antalet lokala ögonblicks bilder.

**C.** Jag har fått en varning om att mina lokala ögonblicks bilder av lokalt fästa volymer kan bli ogiltiga. När kan detta ske?

**En.** Frekventa lokala ögonblicks bilder tillsammans med hög data omsättning i den lokalt fästa volymen kan leda till att det lokala utrymmet på enheten förbrukas snabbt. Om den lokala nivån av enheten används mycket, kan ett utökat moln avbrott leda till att enheten blir full och inkommande skrivningar till volymen kan resultera i invaliditet av ögonblicks bilderna (eftersom det inte finns något utrymme för att uppdatera ögonblicks bilderna för att referera till äldre block med data som har skrivits över). I sådana fall fortsätter skrivningen till volymen, men de lokala ögonblicks bilderna kan vara ogiltiga. Dina befintliga moln ögonblicks bilder påverkas inte.

Varnings varningen är att meddela dig att en sådan situation kan uppstå och se till att du befinner dig i rätt tid genom att granska dina lokala ögonblicks bilder och ta mindre frekventa lokala ögonblicks bilder eller ta bort äldre lokala ögonblicks bilder som inte längre behövs.

Om de lokala ögonblicks bilderna inte är validerade får du en informations avisering som meddelar dig att de lokala ögonblicks bilderna för den aktuella säkerhets kopierings principen har gjorts inaktiverat i listan över tidsstämplar för de lokala ögonblicks bilder som har ogiltig förklarats. Dessa ögonblicks bilder tas bort automatiskt och du kommer inte längre att kunna se dem på bladet **säkerhets kopierings kataloger** i Azure Portal.

## <a name="questions-about-converting-a-tiered-volume-to-a-locally-pinned-volume"></a>Frågor om att konvertera en nivå volym till en lokalt fäst volym
**C.** Jag har en viss långsam enhet vid konvertering av en nivå volym till en lokalt fäst volym. Varför sker detta?

**En.** Konverterings processen omfattar två steg:

1. Etablering av utrymme på enheten för den plats som är närmast konverteras lokalt.
2. Att hämta data på nivå av data från molnet för att säkerställa lokala garantier.

Båda stegen är långvariga åtgärder som är beroende av storleken på den volym som konverteras, data på enheten och tillgänglig bandbredd. Eftersom vissa data från befintliga skiktade volymer kan spilla i molnet som en del av etablerings processen kan enheten försämra prestandan under den här tiden. Dessutom kan konverterings processen vara långsammare om:

* Befintliga volymer har inte säkerhetskopierats till molnet. Vi föreslår att du säkerhetskopierar dina volymer innan du påbörjar en konvertering.
* Bandbredds begränsnings principer har tillämpats, vilket kan begränsa den tillgängliga bandbredden till molnet. Vi rekommenderar därför att du har en dedikerad 40 Mbit/s eller mer anslutning till molnet.
* Konverterings processen kan ta flera timmar på grund av de flera faktorer som beskrivs ovan. Därför rekommenderar vi att du utför den här åtgärden under tider med låg belastning eller på en helg för att undvika påverkan på slut konsumenter.

Mer information om hur du [konverterar en nivå volym till en lokalt fäst volym](storsimple-8000-manage-volumes-u2.md#change-the-volume-type)

**C.** Kan jag avbryta volym konverterings åtgärden?

**En.** Nej, du kan inte avbryta konverterings åtgärden när den har initierats. Som vi beskrivit i föregående fråga bör du vara medveten om de potentiella prestanda problem som du kan stöta på under processen och följa de metod tips som anges ovan när du planerar din konvertering.

**C.** Vad händer med min volym om konverteringen Miss lyckas?

**En.** Volym konverteringen kan inte utföras på grund av problem med moln anslutningen. Enheten kan slutligen stoppa konverterings processen efter att en serie misslyckade försök att ta bort data från molnet. I ett sådant scenario kommer volym typen att fortsätta vara käll volym typ före konverteringen och:

* En kritisk varning utlöses för att meddela dig om volym konverterings problemet. Mer information om [aviseringar relaterade till lokalt fästa volymer](storsimple-8000-manage-alerts.md#locally-pinned-volume-alerts)
* Om du konverterar en nivå till en lokalt fäst volym fortsätter volymen att visa egenskaper för en nivå volym eftersom data fortfarande kan finnas i molnet. Vi rekommenderar att du löser problemen och försöker sedan utföra konverterings åtgärden igen.
* När konverteringen från ett lokalt fäst på en nivå volym Miss lyckas, även om volymen kommer att markeras som en lokalt fäst volym, kommer den att fungera som en nivå volym (eftersom data kan ha spill i molnet). Det kommer dock att fortsätta att uppta utrymme på enhetens lokala nivåer. Utrymmet är inte tillgängligt för andra lokalt fästa volymer. Vi rekommenderar att du gör om åtgärden för att säkerställa att volym konverteringen är klar och att det lokala utrymmet på enheten kan återtas.

## <a name="questions-about-restoring-a-locally-pinned-volume"></a>Frågor om att återställa en lokalt fäst volym
**C.** Återställs lokalt fästa volymer direkt?

**En.** Ja, lokalt fästa volymer återställs direkt. Så snart metadata-informationen för volymen hämtas från molnet som en del av återställnings åtgärden, är volymen online och kan nås av värden. Lokala garantier för volym data kommer dock inte att finnas kvar förrän alla data har hämtats från molnet, och du kan uppleva sämre prestanda på dessa volymer under återställnings tiden.

**C.** Hur lång tid tar det att återställa en lokalt fäst volym?

**En.** Lokalt fästa volymer återställs direkt och tas online så snart informationen om volymens metadata hämtas från molnet, medan volym data fortsätter att laddas ned i bakgrunden. Denna senare del av återställnings åtgärden – hämtar lokala garantier för volym data – är en tids krävande åtgärd och det kan ta flera timmar innan alla data blir lokala igen. Hur lång tid det tar att slutföra beror på flera faktorer, till exempel storleken på den volym som återställs och den tillgängliga bandbredden. Om den ursprungliga volymen som återställs har tagits bort tas ytterligare tid att skapa det lokala utrymmet på enheten som en del av återställnings åtgärden.

**C.** Jag behöver återställa min befintliga lokalt fästa volym till en äldre ögonblicks bild (tas när volymen har skiktas). Kommer volymen att återställas på samma nivå i det här fallet?

**En.** Nej, volymen kommer att återställas som en lokalt fäst volym. Även om ögonblicks bilden är i nivå av den tidpunkt då volymen skapades, medan befintliga volymer återställs, använder StorSimple alltid typen av volym på disken som den finns för närvarande.

**C.** Jag har utökat min lokalt fästa volym nyligen, men nu måste jag återställa data till en tidpunkt då volymen var mindre. Återställer storleken på den aktuella volymen och måste jag utöka storleken på volymen när återställningen har slutförts?

**En.** Ja, återställningen ändrar storlek på volymen och du måste utöka storleken på volymen när återställningen har slutförts.

**C.** Kan jag ändra typ av volym under återställningen?

**En.** Nej, du kan inte ändra volym typen under återställningen.

* Volymer som har tagits bort återställs som den typ som lagras i ögonblicks bilden.
* Befintliga volymer återställs baserat på den aktuella typen, oavsett typ som lagras i ögonblicks bilden (se föregående två frågor).

**C.** Jag behöver återställa min lokalt fästa volym, men jag har valt en felaktig ögonblicks bild av tidpunkten. Kan jag avbryta den aktuella återställnings åtgärden?

**En.** Ja, du kan avbryta en pågående återställnings åtgärd. Volymens tillstånd kommer att återställas till statusen i början av återställningen. Alla skrivningar som har gjorts till volymen medan återställningen pågår kommer dock att gå förlorade.

**C.** Jag påbörjade en återställnings åtgärd på en av mina lokalt fästa volymer och nu ser jag en ögonblicks bild i katalogen för efter släpning som jag inte samlar in igen. Vad används detta för?

**En.** Det här är den tillfälliga ögonblicks bild som skapas före återställnings åtgärden och används för återställning om återställningen avbryts eller Miss lyckas. Ta inte bort den här ögonblicks bilden. den tas bort automatiskt när återställningen är klar. Detta kan inträffa om ditt återställnings jobb bara har lokalt fästa volymer eller en blandning av lokalt fästa och nivåbaserade volymer. Om återställnings jobbet bara innehåller skiktade volymer, utförs inte det här beteendet.

**C.** Kan jag klona en lokalt fäst volym?

**En.** Ja, det kan du. Den lokalt fästa volymen kommer dock att klonas som standard som en nivå volym. Mer information om hur du [klonar en lokalt fäst volym](storsimple-8000-clone-volume-u2.md)

## <a name="questions-about-failing-over-a-locally-pinned-volume"></a>Frågor om att redundansväxla en lokalt fäst volym
**C.** Jag behöver redundansväxla enheten till en annan fysisk enhet. Kommer mina lokalt fästa volymer att växlas över lokalt, lokalt eller på nivå?

**En.** Lokalt fästa volymer har redundansväxlats lokalt, om mål enheten kör StorSimple 8000-serien uppdatering 3 eller senare.

Mer information om [redundans och Dr för lokalt fästa volymer över versioner](storsimple-8000-device-failover-disaster-recovery.md#device-failover-across-software-versions)

**C.** Återställs lokalt volymer som återställs direkt under haveri beredskap (DR)?

**En.** Ja, lokalt fästa volymer återställs direkt under redundansväxling. Så snart metadata-informationen för volymen hämtas från molnet som en del av redundansväxlingen, tas volymen online på mål enheten och kan nås av värden. Under tiden fortsätter volym data att laddas ned i bakgrunden och du kan uppleva sämre prestanda på dessa volymer under den tid som redundansväxlingen sker.

**C.** Jag ser att redundansväxlingen är slutfört, hur kan jag spåra förloppet för lokalt fästa volym som återställs på mål enheten?

**En.** Vid en redundansväxling markeras redundansväxlingen som slutfört när alla volymer i redundansväxlingen har återställts direkt och är online på mål enheten. Detta inkluderar alla lokalt fästa volymer som kan ha redundansväxlats. lokala garantier för data är dock bara tillgängliga när alla data för volymen har laddats ned. Du kan följa förloppet för varje lokalt fixerad volym som har redundansväxlats genom att övervaka motsvarande återställnings jobb som skapas som en del av redundansväxlingen. De enskilda återställnings jobben skapas bara för lokalt fästa volymer.

**C.** Kan jag ändra typ av volym under redundans?

**En.** Nej, du kan inte ändra volym typen under en redundansväxling. Om du växlar över till en annan fysisk enhet som kör StorSimple 8000-serien uppdatering 3, redundansväxlas volymerna på grund av den volym typ som lagras i ögonblicks bilden.

**C.** Kan jag redundansväxla en volym behållare med lokalt fästa volymer till moln installationen?

**En.** Ja, det kan du. De lokalt fästa volymerna kommer att växlas över som nivåer av volymer. Mer information om [redundans och Dr för lokalt fästa volymer över versioner](storsimple-8000-device-failover-disaster-recovery.md#common-considerations-for-device-failover)

