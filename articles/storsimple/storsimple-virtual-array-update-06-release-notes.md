---
title: StorSimple Virtual Array Update 0,6 viktig information | Microsoft Docs
description: Beskriver kritiska öppna problem och lösningar för den virtuella StorSimple-matrisen som kör uppdatering 0,6.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/24/2017
ms.author: alkohli
ms.openlocfilehash: 24bddb47a32a7b8ad4ea659d52c2d5b2730be9ad
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96000920"
---
# <a name="storsimple-virtual-array-update-06-release-notes"></a>StorSimple Virtual Array Update 0,6 viktig information

## <a name="overview"></a>Översikt

I följande versions information identifieras kritiska öppna problem och lösta problem för Microsoft Azure StorSimple virtuell mat ris uppdatering.

Viktig information uppdateras kontinuerligt och eftersom kritiska problem som kräver en lösning upptäcks, läggs de till. Läs igenom informationen i viktig information innan du distribuerar din virtuella StorSimple-matris.

Uppdatering 0,6 motsvarar program varu versionen **10.0.10293.0**.

> [!IMPORTANT]
> - Uppdateringar är störande och startar om enheten. Om I/O pågår förorsakar enheten avbrott. Detaljerade anvisningar om hur du installerar uppdateringen finns i [installera uppdatering 0,6](storsimple-virtual-array-install-update-06.md).
>
> - Vi rekommenderar starkt att du installerar uppdatering 0,6 omedelbart eftersom den innehåller viktiga säkerhets korrigeringar.


## <a name="whats-new-in-the-update-06"></a>Nyheter i uppdateringen 0,6
Uppdatering 0,6 är en viktig uppdatering och bör distribueras omedelbart. Den här uppdateringen innehåller följande korrigeringar: 

- **Windows-säkerhetskorrigeringsfiler** – den här utgåvan har **Windows-kritiska säkerhets korrigeringar**. Läs följande säkerhets uppdateringar om du vill ha mer information om säkerhets problemen och de associerade korrigeringarna:
    - [Kvalitets uppdatering december 2016-säkerhet för Windows 8,1 och Windows Server 2012 R2](https://support.microsoft.com/help/3205400/december-2016-security-only-quality-update-for-windows-8.1-and-windows-server-2012-r2)
    - [Kvalitets uppdatering mars 2017-säkerhet för Windows 8,1 och Windows Server 2012 R2](https://support.microsoft.com/help/4012213/march-2017-security-only-quality-update-for-windows-8-1-and-windows-server-2012-r23)
    - [9 maj 2017 – KB4019213 (endast säkerhets uppdatering)](https://support.microsoft.com/help/4019213/windows-8-update-kb4019213)

- **Restore Fix** – i tidigare versioner uppstod ett fel som skulle hindra återställningen från att slutföras. Den här buggen har åtgärd ATS i den här versionen.


## <a name="issues-fixed-in-the-update-06"></a>Problem som korrigeras i uppdateringen 0,6

Följande tabell innehåller en sammanfattning av problem som korrigeras i den här versionen.

| Nej. | Funktion | Problem |
| --- | --- | --- |
| 1 |Säkerhet| Den här versionen innehåller viktiga Windows-säkerhetsuppdateringar. Vi rekommenderar att du installerar den här uppdateringen direkt.|
| 2 |Återställ| Under en återställning inträffade ett konkurrens tillstånd som förhindrar att återställnings jobbet slutförs. Fel korrigeringen löser detta konkurrens tillstånd.|


## <a name="known-issues-in-the-update-06"></a>Kända problem i uppdateringen 0,6

Följande tabell innehåller en sammanfattning av kända problem för den virtuella StorSimple-matrisen och innehåller problem versionen – anges i tidigare versioner.

| Nej. | Funktion | Problem | Lösning/kommentarer |
| --- | --- | --- | --- |
| **1.** |Uppdateringar |Det går inte att uppdatera de virtuella enheter som skapats i för hands versionen till en allmän version som stöds. |De här virtuella enheterna måste växlas över för den allmänna tillgänglighets versionen med hjälp av en katastrof återställning (DR)-arbets flöde. |
| **2.** |Etablerade data diskar |När du har skapat en datadisk med en viss angiven storlek och skapat motsvarande virtuella StorSimple-enhet, måste du inte expandera eller krympa data disken. Om du försöker göra resultat i förlust av alla data på enhetens lokala nivåer. | |
| **3.** |Grupprincip |När en enhet är ansluten till en domän kan du använda en grup princip för att påverka enhets åtgärden negativt. |Se till att din virtuella matris finns i en egen organisationsenhet (OU) för Active Directory och inga grup princip objekt (GPO) tillämpas på den. |
| **4.** |Lokalt webb gränssnitt |Om utökade säkerhetsfunktioner är aktiverade i Internet Explorer (IE ESC), kanske vissa lokala webb GRÄNSSNITTs sidor, till exempel fel sökning eller underhåll, inte fungerar korrekt. Knappar på dessa sidor kanske inte heller fungerar. |Inaktivera förbättrade säkerhetsfunktioner i Internet Explorer. |
| **5.** |Lokalt webb gränssnitt |I en virtuell Hyper-V-dator visas nätverks gränssnitten i webb gränssnittet som 10 Gbit/s-gränssnitt. |Det här beteendet är en reflektion av Hyper-V. Hyper-V visar alltid 10 Gbit/s för virtuella nätverkskort. |
| **3-6.** |Skiktade volymer eller resurser |Byte intervall låsning för program som fungerar med StorSimple-skiktade volymer stöds inte. Om låsning av byte intervall är aktiverat fungerar inte StorSimple-skiktning. |Rekommenderade mått är: <br></br>Inaktivera byte intervall låsning i program logiken.<br></br>Välj att ange data för det här programmet i lokalt fästa volymer i stället för volymer på nivå.<br></br>*Varningar*: när du använder lokalt fästa volymer och låsning av byte intervall är aktiverat, kan den lokalt fästa volymen vara online även innan återställningen är klar. Om en återställning pågår i sådana fall måste du vänta tills återställningen har slutförts. |
| **3,7.** |Nivå resurser |Att arbeta med stora filer kan resultera i långsamma nivåer. |När du arbetar med stora filer rekommenderar vi att den största filen är mindre än 3% av resursens storlek. |
| **7,8.** |Använd kapacitet för resurser |Du kan se dela förbrukningen när det inte finns några data på resursen. Den här förbrukningen beror på att den använda kapaciteten för resurser innehåller metadata. | |
| **1.9.** |Haveriberedskap |Du kan bara utföra haveri beredskap för en fil server till samma domän som käll enheten. Haveri beredskap för en mål enhet i en annan domän stöds inte i den här versionen. |Detta implementeras i en senare version. Mer information finns i [redundans och haveri beredskap för din virtuella StorSimple-matris](storsimple-virtual-array-failover-dr.md) |
| **10.** |Azure PowerShell |Det går inte att hantera de virtuella StorSimple-enheterna via Azure PowerShell i den här versionen. |All hantering av de virtuella enheterna bör göras via Azure Portal och det lokala webb gränssnittet. |
| **11.3.** |Lösenordsändring |Den virtuella matris enhets konsolen accepterar bara inmatade i ett-US-tangentbord. | |
| **12.5.** |CHAP |Det går inte att ta bort CHAP-autentiseringsuppgifter när den har skapats. Om du ändrar CHAP-autentiseringsuppgifterna måste du dessutom koppla från volymerna och sedan ta dem online för att ändringen ska börja gälla. |Det här problemet åtgärdas i en senare version. |
| **13.4.** |iSCSI-server |Det använda lagrings utrymmet som visas för en iSCSI-volym kan vara annorlunda i StorSimple-Enhetshanteraren tjänsten och iSCSI-värden. |ISCSI-värden har vyn filesystem.<br></br>Enheten ser de block som tilldelas när volymen hade maximal storlek. |
| **längre.** |Filserver |Om en fil i en mapp innehåller en alternativ data ström (ADS) som är associerad med den, säkerhets kopie ras inte ANNONSERna eller återställs med hjälp av haveri beredskap, kloning och återställning på objekt nivå. | |
| **15.4.** |Filserver |Symboliska länkar stöds inte. | |
| **16.** |Filserver |Filer som skyddas av Windows krypterande filsystem (EFS) när de kopieras över eller lagras på StorSimple virtuella array-filservern resulterar i en konfiguration som inte stöds.  | |
| **43.** |Uppdateringar |Om du ser felkod: 2359302 (hex 0x240006) vid försök att installera en snabb korrigering via det lokala användar gränssnittet innebär detta att snabb korrigeringen redan är installerad på enheten.   | |

## <a name="next-step"></a>Nästa steg
[Installera uppdatering 0,6](storsimple-virtual-array-install-update-06.md) på din virtuella StorSimple-matris.

## <a name="references"></a>Referenser
Letar du efter en äldre versions anteckning? Gå till:

* [StorSimple Virtual Array Update 0,5 viktig information](storsimple-virtual-array-update-05-release-notes.md)
* [StorSimple Virtual Array Update 0,4 viktig information](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Virtual Array Update 0,3 viktig information](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array Update 0,1 och 0,2 viktig information](storsimple-ova-update-01-release-notes.md)
* [Viktig information om allmän tillgänglighet för StorSimple Virtual Array]()