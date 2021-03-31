---
title: StorSimple Virtual Array Update 1,0 viktig information
description: Beskriver kritiska öppna problem och lösningar för den virtuella StorSimple-matrisen som kör uppdatering 1,0.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: alkohli
ms.openlocfilehash: 2ec88e4e97d45b27c0226198491b3adec6448496
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96000903"
---
# <a name="storsimple-virtual-array-update-10-release-notes"></a>StorSimple Virtual Array Update 1,0 viktig information

## <a name="overview"></a>Översikt

I följande versions information identifieras kritiska öppna problem och lösta problem för Microsoft Azure StorSimple virtuell mat ris uppdatering.

Viktig information uppdateras kontinuerligt och eftersom kritiska problem som kräver en lösning upptäcks, läggs de till. Läs igenom informationen i viktig information innan du distribuerar din virtuella StorSimple-matris.

Uppdatering 1,0 motsvarar program varu versionen **10.0.10296.0**.

> [!IMPORTANT]
> - Uppdateringar är störande och startar om enheten. Om I/O pågår förorsakar enheten avbrott. Detaljerade anvisningar om hur du installerar uppdateringen finns i [installera uppdatering 1,0](storsimple-virtual-array-install-update-1.md).
>
> - Uppdatering 1 är bara tillgänglig via Azure Portal om enheten kör uppdatering 0,6.

## <a name="whats-new-in-update-10"></a>Nyheter i uppdatering 1,0

**Uppdatering 1,0 innehåller ändringar som rör autentisering av StorSimple Enhetshanteraren-tjänsten och bör distribueras tidigast.** Den här uppdateringen innehåller följande förbättringar och fel korrigeringar:

 - **Användning av Azure Active Directory (AAD) för att autentisera med StorSimple Enhetshanteraren-tjänsten** – från uppdatering 1,0 och senare används Azure Active Directory för att autentisera med StorSimple Enhetshanteraren-tjänsten. Den gamla mekanismen för autentisering kommer att föråldras med 2017 december. Alla användare måste inkludera de nya autentiserings-URL: erna i brand Väggs reglerna. Mer information finns i URL: erna för autentiseringar som anges i [nätverks kraven för din virtuella StorSimple-matris](storsimple-ova-system-requirements.md).
 
    Om URL: en för autentisering inte ingår i brand Väggs reglerna, ser användarna en kritisk varning om att deras StorSimple-enhet inte kunde autentiseras med tjänsten. Om användarna ser den här aviseringen måste de inkludera den nya autentiserings-URL: en. Mer information finns i [StorSimple Network Alerts](storsimple-virtual-array-manage-alerts.md).

 - **Prestanda förbättring** – flera fel korrigeringar har gjorts för att förbättra hastigheterna för moln läsningar, nivåer och nivåer. Därför har både säkerhets kopierings-och återställnings prestanda förbättrats för iSCSI-och fil Server enheter.

 - **Förbättring av skräp insamling** – den här versionen har fel korrigeringar som förbättrar prestandan för skräp insamlings cykeln när enhets-och lagrings kontot finns i två regioner.

 - **Loggnings förbättring** – den här versionen innehåller förbättringar av loggning som rör skräp insamling och I/O-sökväg.


## <a name="issues-fixed-in-update-10"></a>Problem som korrigeras i uppdatering 1,0

Följande tabell innehåller en sammanfattning av problem som korrigeras i den här versionen.

| Nej. | Funktion | Problem |
| --- | --- | --- |
| 1 |AAD-baserad autentisering| Den här versionen innehåller ändringar som gör att AAD kan autentisera med StorSimple-Enhetshanteraren.|
| 2 |Skräp insamling| Det här problemet rapporterades på en kund plats där enhets-och lagrings kontona finns i olika regioner och kunden rapporterade tillfälliga nätverks fel som därmed påverkar faktureringen. I den här versionen har det här problemet åtgärd ATS. |
| 3 |Prestanda| Den här versionen innehåller ändringar som resulterar i återställning/moln läsningar/nivå i/nivå av prestanda förbättring.|
| 4 |Uppdatera| Det uppstod ett problem med uppdateringen i den tidigare versionen som resulterade i säkerhets kopierings fel på en kunds webbplats. Det här problemet åtgärdas i den här versionen.|

## <a name="known-issues-in-update-10"></a>Kända problem i uppdatering 1,0

Följande tabell innehåller en sammanfattning av kända problem för den virtuella StorSimple-matrisen och innehåller problem versionen – anges i tidigare versioner.

| Nej. | Funktion | Problem | Lösning/kommentarer |
| --- | --- | --- | --- |
| **1.** |Uppdateringar |De virtuella matriserna som skapats i för hands versionen kan inte uppdateras till en allmän tillgänglighets version som stöds. |Dessa virtuella matriser måste växlas över för den allmänna tillgänglighets versionen med hjälp av ett katastrof återställnings arbets flöde (DR). |
| **2.** |Etablerade data diskar |När du har allokerat en datadisk med en viss angiven storlek och skapat motsvarande virtuella StorSimple-matris, får du inte expandera eller krympa data disken. Om du försöker göra resultat i förlust av alla data på enhetens lokala nivåer. | |
| **3.** |Grupprincip |När en enhet är ansluten till en domän kan du använda en grup princip för att påverka enhets åtgärden negativt. |Se till att din virtuella matris finns i en egen organisationsenhet (OU) för Active Directory och inga grup princip objekt (GPO) tillämpas på den. |
| **4.** |Lokalt webb gränssnitt |Om utökade säkerhetsfunktioner är aktiverade i Internet Explorer (IE ESC), kanske vissa lokala webb GRÄNSSNITTs sidor, till exempel fel sökning eller underhåll, inte fungerar korrekt. Knappar på dessa sidor kanske inte heller fungerar. |Inaktivera förbättrade säkerhetsfunktioner i Internet Explorer. |
| **5.** |Lokalt webb gränssnitt |I en virtuell Hyper-V-dator visas nätverks gränssnitten i webb gränssnittet som 10 Gbit/s-gränssnitt. |Det här beteendet är en reflektion av Hyper-V. Hyper-V visar alltid 10 Gbit/s för virtuella nätverkskort. |
| **3-6.** |Skiktade volymer eller resurser |Byte intervall låsning för program som fungerar med StorSimple-skiktade volymer stöds inte. Om låsning av byte intervall är aktiverat fungerar inte StorSimple-skiktning. |Rekommenderade mått är: <br></br>Inaktivera byte intervall låsning i program logiken.<br></br>Välj att ange data för det här programmet i lokalt fästa volymer i stället för volymer på nivå.<br></br>*Varningar*: när du använder lokalt fästa volymer och låsning av byte intervall är aktiverat, kan den lokalt fästa volymen vara online även innan återställningen är klar. Om en återställning pågår i sådana fall måste du vänta tills återställningen har slutförts. |
| **3,7.** |Nivå resurser |Att arbeta med stora filer kan resultera i långsamma nivåer. |När du arbetar med stora filer rekommenderar vi att den största filen är mindre än 3% av resursens storlek. |
| **7,8.** |Använd kapacitet för resurser |Du kan se dela förbrukningen när det inte finns några data på resursen. Den här förbrukningen beror på att den använda kapaciteten för resurser innehåller metadata. | |
| **1.9.** |Haveriberedskap |Du kan bara utföra haveri beredskap för en fil server till samma domän som käll enheten. Haveri beredskap för en mål enhet i en annan domän stöds inte i den här versionen. |Detta implementeras i en senare version. Mer information finns i [redundans och haveri beredskap för din virtuella StorSimple-matris](storsimple-virtual-array-failover-dr.md) |
| **10.** |Azure PowerShell |Det går inte att hantera virtuella StorSimple-matriser via Azure PowerShell i den här versionen. |All hantering av de virtuella enheterna bör göras via Azure Portal och det lokala webb gränssnittet. |
| **11.3.** |Lösenordsändring |Den virtuella matris enhets konsolen accepterar bara inmatade i ett-US-tangentbord. | |
| **12.5.** |CHAP |Det går inte att ta bort CHAP-autentiseringsuppgifter när den har skapats. Om du ändrar CHAP-autentiseringsuppgifterna måste du dessutom koppla från volymerna och sedan ta dem online för att ändringen ska börja gälla. |Det här problemet åtgärdas i en senare version. |
| **13.4.** |iSCSI-server |Det använda lagrings utrymmet som visas för en iSCSI-volym kan vara annorlunda i StorSimple-Enhetshanteraren tjänsten och iSCSI-värden. |ISCSI-värden har vyn filesystem.<br></br>Enheten ser de block som tilldelas när volymen hade maximal storlek. |
| **längre.** |Filserver |Om en fil i en mapp innehåller en alternativ data ström (ADS) som är associerad med den, säkerhets kopie ras inte ANNONSERna eller återställs med hjälp av haveri beredskap, kloning och återställning på objekt nivå. | |
| **15.4.** |Filserver |Symboliska länkar stöds inte. | |
| **16.** |Filserver |Filer som skyddas av Windows krypterande filsystem (EFS) när de kopieras över eller lagras på StorSimple virtuella array-filservern resulterar i en konfiguration som inte stöds.  | |
| **43.** |Uppdateringar |Om du ser felkod: 2359302 (hex 0x240006) vid försök att installera en snabb korrigering via det lokala användar gränssnittet innebär detta att snabb korrigeringen redan är installerad på enheten.   | |
| **arton.** |Uppdateringar |Om du använder det lokala webb gränssnittet för att installera uppdatering 1 på den virtuella matrisen måste du kontrol lera att du kör uppdatering 0,6. Om du kör en version som är lägre än uppdatering 0,6 måste du först installera uppdatering 0,6 och sedan använda uppdatering 1. Om du installerar uppdatering 1,0 direkt från en 0,6-version för tidigare uppdateringar kommer du att missa vissa uppdateringar och övervaknings diagrammen fungerar inte.   | |


## <a name="next-steps"></a>Nästa steg
[Installera uppdatering 1,0](storsimple-virtual-array-install-update-1.md) på din virtuella StorSimple-matris.

## <a name="references"></a>Referenser
Letar du efter en äldre versions anteckning? Gå till:
*  [StorSimple Virtual Array Update 0,6 viktig information](storsimple-virtual-array-update-06-release-notes.md)
* [StorSimple Virtual Array Update 0,5 viktig information](storsimple-virtual-array-update-05-release-notes.md)
* [StorSimple Virtual Array Update 0,4 viktig information](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Virtual Array Update 0,3 viktig information](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array Update 0,1 och 0,2 viktig information](storsimple-ova-update-01-release-notes.md)
* [Viktig information om allmän tillgänglighet för StorSimple Virtual Array](./storsimple-virtual-array-update-06-release-notes.md)