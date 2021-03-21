---
title: StorSimple Virtual Array Update 0,4 viktig information | Microsoft Docs
description: Beskriver kritiska öppna problem och lösningar för den virtuella StorSimple-matrisen som kör uppdatering 0,4.
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
ms.date: 04/05/2017
ms.author: alkohli
ms.openlocfilehash: 392e930dcb69392b6f9425abba0efa516d5c82b8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94967065"
---
# <a name="storsimple-virtual-array-update-04-release-notes"></a>StorSimple Virtual Array Update 0,4 viktig information

## <a name="overview"></a>Översikt

I följande versions information identifieras kritiska öppna problem och lösta problem för Microsoft Azure StorSimple virtuell mat ris uppdatering.

Viktig information uppdateras kontinuerligt och eftersom kritiska problem som kräver en lösning upptäcks, läggs de till. Läs igenom informationen i viktig information innan du distribuerar din virtuella StorSimple-matris.

Uppdatering 0,4 motsvarar program varu versionen **10.0.10289.0**.

> [!NOTE]
> Uppdateringar är störande och startar om enheten. Om I/O pågår förorsakar enheten avbrott.


## <a name="whats-new-in-the-update-04"></a>Nyheter i uppdateringen 0,4
Uppdatering 0,4 är i första hand en fel korrigerings version som är kopplad till några få förbättringar. I den här versionen har flera buggar som resulterade i säkerhets kopierings fel i den tidigare versionen åtgärd ATS. De viktigaste förbättringarna och fel korrigeringarna är följande:

- **Förbättringar av säkerhets kopierings prestanda** – den här versionen har gjort flera viktiga förbättringar för att förbättra säkerhets kopierings prestanda. Det innebär att säkerhets kopiorna som omfattar ett stort antal filer ser en betydande minskning av tiden för att slutföras för fullständiga och stegvisa säkerhets kopieringar.

- **Förbättrad återställning av prestanda** – den här versionen innehåller förbättringar som förbättrar återställnings prestanda avsevärt när du använder ett stort antal filer. Om du använder 2-4 miljoner filer rekommenderar vi att du etablerar en virtuell matris med 16 GB RAM-minne för att se förbättringarna. När du använder mindre än 2 000 000 filer fortsätter minimi kravet för den virtuella datorn att vara 8 GB RAM-minne.

- **Förbättringar av support paketet** – förbättringarna är bland annat loggning i statistik för disk, processor, minne, nätverk och moln i support paketet, vilket förbättrar processen för att diagnostisera eller felsöka enhets problem.

- **Begränsa lokalt fästa iSCSI-volymer till 200 GB** – för lokalt fästa volymer, rekommenderar vi att du begränsar till en 200 GB iSCSI-volym på din virtuella StorSimple-matris. Den lokala reservationen för skiktade volymer fortsätter att vara 10% av den etablerade volym storleken, men det är ett tak på 200 GB. 

- **Säkerhets kopierings-relaterade fel korrigeringar** – i tidigare versioner av program vara uppstod problem som rör säkerhets kopieringar som skulle orsaka säkerhets kopierings fel. Dessa buggar har åtgärd ATS i den här versionen.


## <a name="issues-fixed-in-the-update-04"></a>Problem som korrigeras i uppdateringen 0,4

Följande tabell innehåller en sammanfattning av problem som korrigeras i den här versionen.

| Nej. | Funktion | Problem |
| --- | --- | --- |
| 1 |Säkerhets kopierings prestanda|I de tidigare versionerna tar det lång tid för säkerhets kopieringar som involverar stora filer att slutföras (i antal dagar). I den här versionen ser både den fullständiga och de stegvisa säkerhets kopieringarna en betydande minskning av tiden för slut för ande. |
| 2 |Support paket|Statistik för disk, processor, minne, nätverk och moln är nu inloggad på de support loggar som gör att support paketen är mycket effektiva vid fel sökning av eventuella enhets problem.|
| 3 |Backup |I tidigare versioner kan långvariga säkerhets kopieringar resultera i ett utrymme på enheten som resulterar i säkerhets kopierings problem. Den här buggen åtgärdas i den här versionen genom att inte tillåta fler än fem säkerhets kopieringar i kö på en och samma tidpunkt.|
| 4 |iSCSI | I tidigare versioner var den lokala reservationen för fasta eller lokalt fästa volymer 10% av den etablerade volym storleken. I den här versionen är den lokala reservationen för alla iSCSI-volymer (lokalt fixerad eller skiktad) begränsad till 10% med högst upp till 200 GB (för volymer på nivå som är större än 2 TB), vilket frigör mer utrymme på den lokala disken. Vi rekommenderar att de lokalt fästa volymerna i den här versionen är begränsade till 200 GB.|


## <a name="known-issues-in-the-update-04"></a>Kända problem i uppdateringen 0,4

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
| **7,8.** |Använd kapacitet för resurser |Du kan se dela förbrukningen när det inte finns några data på resursen. Detta beror på att den använda kapaciteten för resurser innehåller metadata. | |
| **1.9.** |Haveriberedskap |Du kan bara utföra haveri beredskap för en fil server till samma domän som käll enheten. Haveri beredskap för en mål enhet i en annan domän stöds inte i den här versionen. |Detta implementeras i en senare version. |
| **10.** |Azure PowerShell |Det går inte att hantera de virtuella StorSimple-enheterna via Azure PowerShell i den här versionen. |All hantering av de virtuella enheterna bör göras via den klassiska Azure-portalen och det lokala webb gränssnittet. |
| **11.3.** |Lösenordsändring |Den virtuella matris enhets konsolen accepterar bara inmatade i ett-US-tangentbord. | |
| **12.5.** |CHAP |Det går inte att ta bort CHAP-autentiseringsuppgifter när den har skapats. Om du ändrar CHAP-autentiseringsuppgifterna måste du dessutom koppla från volymerna och sedan ta dem online för att ändringen ska börja gälla. |Det här problemet åtgärdas i en senare version. |
| **13.4.** |iSCSI-server |Det använda lagrings utrymmet som visas för en iSCSI-volym kan vara annorlunda i StorSimple Manager-tjänsten och iSCSI-värden. |ISCSI-värden har vyn filesystem.<br></br>Enheten ser de block som tilldelas när volymen hade maximal storlek. |
| **längre.** |Filserver |Om en fil i en mapp innehåller en alternativ data ström (ADS) som är associerad med den, säkerhets kopie ras inte ANNONSERna eller återställs med hjälp av haveri beredskap, kloning och återställning på objekt nivå. | |
| **15.4.** |Filserver |Symboliska länkar stöds inte. | |
| **16.** |Filserver |Filer som skyddas av Windows krypterande filsystem (EFS) när de kopieras över eller lagras på StorSimple virtuella array-filservern resulterar i en konfiguration som inte stöds.  | |

## <a name="next-step"></a>Nästa steg
[Installera uppdatering 0,4](storsimple-virtual-array-install-update-04.md) på din virtuella StorSimple-matris.

## <a name="references"></a>Referenser
Letar du efter en äldre versions anteckning? Gå till: 

* [StorSimple Virtual Array Update 0,3 viktig information](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array Update 0,1 och 0,2 viktig information](storsimple-ova-update-01-release-notes.md)
* [Viktig information om allmän tillgänglighet för StorSimple Virtual Array](./storsimple-virtual-array-update-06-release-notes.md)