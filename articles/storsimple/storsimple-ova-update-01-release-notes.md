---
title: StorSimple Virtual Array uppdatering 0,2 & 0,1 viktig information
description: Beskriver kritiska öppna problem och lösningar för den virtuella StorSimple-matrisen som kör uppdatering 0,2 och 0,1.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 3993864d-2ddd-4302-a2f1-8d737fba6eab
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/16/2016
ms.author: alkohli
ms.openlocfilehash: 67d3fafb9b4b93ae52bdb09d673bf65cad30b816
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94966164"
---
# <a name="storsimple-virtual-array-update-02-and-01-release-notes"></a>StorSimple Virtual Array Update 0,2 och 0,1 viktig information
## <a name="overview"></a>Översikt
I följande versions information identifieras kritiska öppna problem och lösta problem för Microsoft Azure StorSimple virtuell mat ris uppdatering. (Microsoft Azure StorSimple virtuell matris kallas även för den lokala virtuella enheten StorSimple eller den virtuella StorSimple-enheten.) 

Viktig information uppdateras kontinuerligt och eftersom kritiska problem som kräver en lösning upptäcks, läggs de till. Innan du distribuerar den virtuella StorSimple-enheten bör du läsa igenom informationen i viktig information.

Uppdatering 0,2 motsvarar program varu versionen **10.0.10280.0**; Uppdatering 0,1 är version **10.0.10279.0**. I avsnitten nedan visas ändringarna för varje uppdatering. 

> [!NOTE]
> Uppdateringar är störande och startar om enheten. Om I/O pågår kommer enheten att ådra sig avbrott.
> 
> 

## <a name="issues-fixed-in-the-update-02"></a>Problem som korrigeras i uppdateringen 0,2
Uppdatering 0,2 innehåller alla ändringar från uppdatering 0,1 utöver den korrigering som beskrivs i följande tabell:

| Funktion | Problem |
| --- | --- |
| Uppdateringar |I den senaste versionen identifierades inte uppdateringar automatiskt i den klassiska Azure-portalen, så du måste använda det lokala webb gränssnittet för att installera uppdateringar. Det här problemet åtgärdas i den här versionen. När du har installerat uppdatering 0,2 kan du installera framtida uppdateringar med hjälp av den klassiska Azure-portalen. |

## <a name="whats-new-in-the-update-01"></a>Nyheter i uppdateringen 0,1
Uppdatering 0,1 innehåller följande fel korrigeringar och förbättringar. 

* **Förbättrad återhämtning för moln avbrott**: den här versionen har flera fel korrigeringar kring haveri beredskap, säkerhets kopiering, återställning och nivåer i händelse av ett avbrott i moln anslutningen. 
* **Förbättrade återställnings prestanda**: den här versionen har fel korrigeringar som har minskat klart tiden för återställnings jobben.
* **Optimering av automatiserad utrymmes** återtagning: när data tas bort på tunt allokerade volymer måste de oanvända lagrings blocken frigöras. Den här versionen har förbättrat utrymmes återtagnings processen från molnet, vilket innebär att det outnyttjade utrymmet blir tillgängligt snabbare jämfört med tidigare versioner.
* **Nya virtuella disk avbildningar**: nya virtuella hård diskar, VHDX och VMDK är nu tillgängliga via den klassiska Azure-portalen. Du kan hämta de här avbildningarna för att etablera nya uppdatering 0,1-enheter.
* **Förbättra precisionen för jobb status i portalen**: i den tidigare versionen av program vara var jobb status rapporteringen i portalen inte detaljerad. Det här problemet har lösts i den här versionen.
* **Domän anslutning**: fel korrigeringar relaterade till domän anslutning och namnbyte på enheten.

## <a name="issues-fixed-in-the-update-01"></a>Problem som korrigeras i uppdateringen 0,1
Följande tabell innehåller en sammanfattning av problem som korrigeras i den här versionen.

| Nej. | Funktion | Problem |
| --- | --- | --- |
| 1 |VMDK |I vissa VMware-versioner visade sig OS-disken som en sparse-orsak till varningar och avbrott i normala åtgärder. Detta har åtgärd ATS i den här versionen. |
| 2 |iSCSI-server |I den senaste versionen var användaren tvungen att ange en gateway för varje aktiverat nätverks gränssnitt för den virtuella StorSimple-enheten. Detta beteende har ändrats i den här versionen så att användaren måste konfigurera minst en gateway för alla aktiverade nätverks gränssnitt. |
| 3 |Support paket |I den tidigare versionen av program vara misslyckades paket samlingen när paket storlekarna var större än 1 GB. Det här problemet åtgärdas i den här versionen. |
| 4 |Åtkomst till molnet |I den senaste versionen, om den virtuella matrisen för StorSimple inte hade någon nätverks anslutning och startats om, skulle det lokala användar gränssnittet ha anslutnings problem. Det här problemet åtgärdas i den här versionen. |
| 5 |Övervaknings diagram |I den tidigare versionen, efter en enhets växling, visade moln kapacitets användnings diagram felaktiga värden i den klassiska Azure-portalen. Detta åtgärdas i den aktuella versionen. |

## <a name="known-issues-in-the-update-01"></a>Kända problem i uppdateringen 0,1
Följande tabell innehåller en sammanfattning av kända problem för den virtuella StorSimple-matrisen och innehåller problem versionen – anges i tidigare versioner. **De problem versioner som anges i den här versionen är markerade med en asterisk. Nästan alla problem i den här listan har överförts från GA-versionen av StorSimple Virtual Array.**

| Nej. | Funktion | Problem | Lösning/kommentarer |
| --- | --- | --- | --- |
| **1.** |Uppdateringar |Det går inte att uppdatera de virtuella enheter som skapats i för hands versionen till en allmän version som stöds. |De här virtuella enheterna måste växlas över för den allmänna tillgänglighets versionen med hjälp av en katastrof återställning (DR)-arbets flöde. |
| **2.** |Etablerade data diskar |När du har skapat en datadisk med en viss angiven storlek och skapat motsvarande virtuella StorSimple-enhet, måste du inte expandera eller krympa data disken. Om du försöker göra detta leder det till att alla data i enhetens lokala nivåer går förlorade. | |
| **3.** |Grupprincip |När en enhet är ansluten till en domän kan du använda en grup princip för att påverka enhets åtgärden negativt. |Se till att din virtuella matris finns i en egen organisationsenhet (OU) för Active Directory och inga grup princip objekt (GPO) tillämpas på den. |
| **4.** |Lokalt webb gränssnitt |Om utökade säkerhetsfunktioner är aktiverade i Internet Explorer (IE ESC), kanske vissa lokala webb GRÄNSSNITTs sidor, till exempel fel sökning eller underhåll, inte fungerar korrekt. Knappar på dessa sidor kanske inte heller fungerar. |Inaktivera förbättrade säkerhetsfunktioner i Internet Explorer. |
| **5.** |Lokalt webb gränssnitt |I en virtuell Hyper-V-dator visas nätverks gränssnitten i webb gränssnittet som 10 Gbit/s-gränssnitt. |Det här beteendet är en reflektion av Hyper-V. Hyper-V visar alltid 10 Gbit/s för virtuella nätverkskort. |
| **3-6.** |Skiktade volymer eller resurser |Byte intervall låsning för program som fungerar med StorSimple-skiktade volymer stöds inte. Om låsning av byte intervall är aktiverat fungerar inte StorSimple-skiktning. |Rekommenderade mått är: <br></br>Inaktivera byte intervall låsning i program logiken.<br></br>Välj att ange data för det här programmet i lokalt fästa volymer i stället för volymer på nivå.<br></br>*Varningar*: om du använder lokalt fästa volymer och låsning av byte intervall är aktiverat, bör du vara medveten om att den lokalt fästa volymen kan vara online även innan återställningen är klar. Om en återställning pågår i sådana fall måste du vänta tills återställningen har slutförts. |
| **3,7.** |Nivå resurser |Att arbeta med stora filer kan resultera i långsamma nivåer. |När du arbetar med stora filer rekommenderar vi att den största filen är mindre än 3% av resursens storlek. |
| **7,8.** |Använd kapacitet för resurser |Du kan se dela förbrukningen om inga data finns på resursen. Detta beror på att den använda kapaciteten för resurser innehåller metadata. | |
| **1.9.** |Haveriberedskap |Du kan bara utföra haveri beredskap för en fil server till samma domän som käll enheten. Haveri beredskap för en mål enhet i en annan domän stöds inte i den här versionen. |Detta kommer att implementeras i en senare version. |
| **10.** |Azure PowerShell |Det går inte att hantera de virtuella StorSimple-enheterna via Azure PowerShell i den här versionen. |All hantering av de virtuella enheterna bör göras via den klassiska Azure-portalen och det lokala webb gränssnittet. |
| **11.3.** |Lösenordsändring |Den virtuella matris enhets konsolen accepterar bara inmatade i ett-US-tangentbord. | |
| **12.5.** |CHAP |Det går inte att ta bort CHAP-autentiseringsuppgifter när den har skapats. Om du ändrar CHAP-autentiseringsuppgifterna måste du dessutom koppla från volymerna och sedan ta dem online för att ändringen ska börja gälla. |De kommer att åtgärdas i en senare version. |
| **13.4.** |iSCSI-server |Det använda lagrings utrymmet som visas för en iSCSI-volym kan vara annorlunda i StorSimple Manager-tjänsten och iSCSI-värden. |ISCSI-värden har vyn filesystem.<br></br>Enheten ser de block som tilldelas när volymen hade maximal storlek. |
| **längre.** |Fil Server * |Om en fil i en mapp innehåller en alternativ data ström (ADS) som är associerad med den, säkerhets kopie ras inte ANNONSERna eller återställs med hjälp av haveri beredskap, kloning och återställning på objekt nivå. | |

## <a name="next-step"></a>Nästa steg
[Installera uppdateringar](./storsimple-virtual-array-install-update-06.md) på din virtuella StorSimple-matris.