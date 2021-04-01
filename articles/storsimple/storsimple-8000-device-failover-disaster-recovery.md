---
title: Redundansväxling och haveri beredskap för StorSimple 8000-serien het
description: Lär dig hur du växlar över din StorSimple-enhet till sig själv, en annan fysisk enhet eller en moln installation.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/03/2017
ms.author: alkohli
ms.openlocfilehash: dffa059b18e159d04b5e3bb8555dabf801ede692
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017125"
---
# <a name="failover-and-disaster-recovery-for-your-storsimple-8000-series-device"></a>Växling vid fel och haveriberedskap för enhet i StorSimple 8000-serien

## <a name="overview"></a>Översikt

I den här artikeln beskrivs enhetens redundans funktion för StorSimple 8000-serien och hur den här funktionen kan användas för att återställa StorSimple-enheter om en katastrof inträffar. StorSimple använder enhets växling för att migrera data från en käll enhet i data centret till en annan mål enhet. Vägledningen i den här artikeln gäller för fysiska enheter i StorSimple 8000-serien och moln apparater som kör program varu versioner uppdatering 3 och senare.

StorSimple **använder enhets bladet** för att starta funktionen redundans i händelse av en katastrof. Det här bladet visar en lista över alla StorSimple-enheter som är anslutna till din StorSimple Enhetshanteraren-tjänst.

![Bladet enheter](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)


## <a name="disaster-recovery-dr-and-device-failover"></a>Haveri beredskap (DR) och enhets växling vid fel

I en haveri beredskap (DR) slutar den primära enheten att fungera. StorSimple använder den primära enheten som _källa_ och flyttar tillhör ande moln data till en annan _mål_ enhet. Den här processen kallas för *redundans*. Följande bild illustrerar processen för redundans.

![Vad händer i redundansväxlingen i enheten?](./media/storsimple-8000-device-failover-disaster-recovery/failover-dr-flow.png)

Mål enheten för en redundansväxling kan vara en fysisk enhet eller till och med en moln installation. Mål enheten kanske finns på samma eller en annan geografisk plats än käll enheten.

Under redundansväxlingen kan du välja volym behållare för migrering. StorSimple ändrar sedan ägarskapet för dessa volym behållare från käll enheten till mål enheten. När volym behållarna har ändrat ägarskapet, tar StorSimple bort dessa behållare från käll enheten. När borttagningen är klar kan du växla tillbaka till mål enheten. _Failback_ överför ägarskapet tillbaka till den ursprungliga käll enheten.

### <a name="cloud-snapshot-used-during-device-failover"></a>Moln ögonblicks bild som används vid redundansväxling av enhet

Efter en DR används den senaste moln säkerhets kopian för att återställa data till mål enheten. Mer information om moln ögonblicks bilder finns i [använda tjänsten StorSimple Enhetshanteraren för att utföra en manuell säkerhets kopiering](storsimple-8000-manage-backup-policies-u2.md#take-a-manual-backup).

I en StorSimple 8000-serien associeras säkerhets kopierings principer med säkerhets kopior. Om det finns flera säkerhets kopierings principer för samma volym väljer StorSimple säkerhets kopierings principen med det största antalet volymer. StorSimple använder sedan den senaste säkerhets kopieringen från den valda säkerhets kopierings principen för att återställa data på mål enheten.

Anta att det finns två säkerhets kopierings principer, *defaultPol* och *customPol*:

* *defaultPol*: en volym, *VOL1*, körs dagligen med början 10:30 PM.
* *customPol*: fyra volymer, *VOL1*, *VOL2*, *vol3*, *VOL4*, körs dagligen från 10:00 PM.

I det här fallet prioriterar StorSimple för krasch konsekvens och använder *customPol* som har fler volymer. Den senaste säkerhets kopieringen från den här principen används för att återställa data. Mer information om hur du skapar och hanterar säkerhets kopierings principer finns i [använda tjänsten StorSimple Enhetshanteraren för att hantera säkerhets kopierings principer](storsimple-8000-manage-backup-policies-u2.md).

## <a name="common-considerations-for-device-failover"></a>Vanliga överväganden för redundansväxling av enhet

Innan du växlar över en enhet bör du läsa följande information:

* Innan en enhets växling vid fel startar måste alla volymer i volym behållarna vara offline. I en oplanerad redundansväxling kommer StotSimple-volymer automatiskt att kopplas från. Men om du utför en planerad redundansväxling (för att testa DR) måste du koppla från alla volymer.
* Endast de volym behållare som har en associerad moln ögonblicks bild listas för DR. Det måste finnas minst en volym behållare med en associerad moln ögonblicks bild för att återställa data.
* Om det finns moln ögonblicks bilder som sträcker sig över flera volym behållare, växlar StorSimple över dessa volym behållare som en uppsättning. I en sällsynt instans, om det finns lokala ögonblicks bilder som sträcker sig över flera volym behållare, men associerade ögonblicks bilder av molnet inte gör det, växlar StorSimple över de lokala ögonblicks bilderna och lokala data går förlorade efter DR.
* Tillgängliga mål enheter för DR är enheter som har tillräckligt med utrymme för att rymma de valda volym containrarna. Enheter som inte har tillräckligt med utrymme visas inte som mål enheter.
* Efter en DR (under en begränsad tid) kan data åtkomst prestanda påverkas avsevärt eftersom enheten behöver komma åt data från molnet och lagra den lokalt.

#### <a name="device-failover-across-software-versions"></a>Enhets växling över flera program versioner

En StorSimple Enhetshanteraren tjänst i en distribution kan ha flera enheter, både fysiska och moln, som kör olika program varu versioner.

Använd följande tabell för att avgöra om du kan redundansväxla eller återställa till en annan enhet som kör en annan program varu version och hur volym typerna beter sig under DR.

#### <a name="failover-and-failback-across-software-versions"></a>Redundansväxling och återställning efter fel i program varu versioner

| Redundansväxla/redundansväxla tillbaka från | Fysisk enhet | Molninstallation |
| --- | --- | --- |
| Uppdatering 3 till uppdatering 4 |Skiktade volymer växlar över till nivå. <br></br>Lokalt fästa volymer växlar över som lokalt fästa. <br></br> Efter en redundansväxling när du tar en ögonblicks bild på Update 4-enheten används [termisk karta-baserad spårning](storsimple-update4-release-notes.md#whats-new-in-update-4) i. |Lokalt fästa volymer växlar över till nivå. |
| Uppdatering 4 till uppdatering 3 |Skiktade volymer växlar över till nivå. <br></br>Lokalt fästa volymer växlar över som lokalt fästa. <br></br> Säkerhets kopior som används för att återställa bevara termisk karta-metadata. <br></br>Termisk karta-baserad spårning är inte tillgänglig i uppdatering 3 efter en återställning efter fel. |Lokalt fästa volymer växlar över till nivå. |


## <a name="device-failover-scenarios"></a>Failover-scenarier för enheter

Om det finns en katastrof kan du välja att Redundansväxla din StorSimple-enhet:

* [Till en fysisk enhet](storsimple-8000-device-failover-physical-device.md).
* [Till sig själv](storsimple-8000-device-failover-same-device.md).
* [Till en moln](storsimple-8000-device-failover-cloud-appliance.md)installation.

I föregående artiklar finns detaljerade anvisningar för var och en av ovanstående fall av redundans.


## <a name="failback"></a>Återställning efter fel

För uppdatering 3 och senare versioner stöder StorSimple också återställning efter fel. Failback är bara den omvända redundansväxlingen, målet blir källan och den ursprungliga käll enheten under redundansväxlingen är nu mål enheten. 

Vid återställning efter fel synkroniserar StorSimple tillbaka data till den primära platsen, stoppar I/O-och program aktiviteten och övergår tillbaka till den ursprungliga platsen.

När en redundansväxling har slutförts utför StorSimple följande åtgärder:

* StorSimple rengör volym behållarna som har redundansväxlats från käll enheten.
* StorSimple initierar ett bakgrunds jobb per volym behållare (redundansväxlats) på käll enheten. Om du försöker återställa medan jobbet pågår får du ett meddelande om detta. Vänta tills jobbet är klart för att starta återställning efter fel.
* Hur lång tid det tar att slutföra borttagningen av volym behållare beror på olika faktorer, till exempel mängden data, Age of data, antal säkerhets kopior och den nätverks bandbredd som är tillgänglig för åtgärden.

Om du planerar redundanstest eller testar förlita, rekommenderar vi att du testar volym behållare med mindre data (GB). Vanligt vis kan du starta failback 24 timmar efter att redundansväxlingen är slutförd.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

F. **Vad händer om DR Miss lyckas eller har delvis lyckats?**

A. Om DR Miss lyckas rekommenderar vi att du försöker igen. Den andra enheten redundansväxlingen är medveten om förloppet för det första jobbet och börjar från och med den punkten.

F. **Kan jag ta bort en enhet när redundansväxlingen är pågående?**

A. Det går inte att ta bort en enhet medan en återställning pågår. Du kan bara ta bort enheten när DR-filen har slutförts. Du kan övervaka förloppet för enhetens redundans på bladet **jobb** .

F. **När startar skräp insamlingen på käll enheten så att lokala data på käll enheten tas bort?**

A. Skräp insamling är bara aktiverat på käll enheten när enheten har rensats helt. Rensningen innehåller rensning av objekt som har redundansväxlats från käll enheten, till exempel volymer, säkerhets kopierings objekt (inte data), volym behållare och principer.

F. **Vad händer om borttagnings jobbet som är kopplat till volym behållarna på käll enheten Miss lyckas?**

A.  Om borttagnings jobbet Miss lyckas kan du ta bort volym containrarna manuellt. Välj din käll enhet på bladet **enheter** och klicka på **volym behållare**. Välj de volym behållare som du har växlat över och klicka på **ta bort** längst ned på bladet. När du har tagit bort alla felaktiga volymer över volym containrar på käll enheten kan du starta återställningen. Mer information finns i [ta bort en volym behållare](storsimple-8000-manage-volume-containers.md#delete-a-volume-container).

## <a name="business-continuity-disaster-recovery-bcdr"></a>Katastrof återställning av affärs kontinuitet (BCDR)

Ett BCDR-scenario (verksamhets kontinuitet haveri beredskap) inträffar när hela Azure-datacenter slutar fungera. Det här scenariot kan påverka din StorSimple-Enhetshanteraren-tjänst och tillhör ande StorSimple-enheter.

Om en StorSimple-enhet har registrerats precis innan en katastrof inträffade, kan den här enheten behöva genomgå en fabriks återställning. Efter katastrofen visas StorSimple-enheten i Azure Portal som offline. Den här enheten måste tas bort från portalen. Återställ enheten till fabriks inställningarna och registrera den igen med tjänsten.

## <a name="next-steps"></a>Nästa steg

Om du är redo att utföra en enhets växling vid fel väljer du något av följande scenarier för detaljerade instruktioner:

- [Redundansväxla till en annan fysisk enhet](storsimple-8000-device-failover-physical-device.md)
- [Redundansväxla till samma enhet](storsimple-8000-device-failover-same-device.md)
- [Redundansväxla till StorSimple Cloud Appliance](storsimple-8000-device-failover-cloud-appliance.md)

Om du har växlat över enheten väljer du något av följande alternativ:

* [Inaktivera eller ta bort din StorSimple-enhet](storsimple-8000-deactivate-and-delete-device.md).
* [Använd tjänsten StorSimple Enhetshanteraren för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

