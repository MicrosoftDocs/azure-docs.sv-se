---
title: StorSimple Virtual Array Update 0,3 viktig information
description: Beskriver kritiska öppna problem och lösningar för den virtuella StorSimple-matrisen som kör uppdatering 0,3.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: b197651a-3c40-4185-b23d-4c8f22cfa8f4
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/15/2016
ms.author: alkohli
ms.openlocfilehash: 8301b45fe778bd3df7fc665db2662ba81d3e644f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94966113"
---
# <a name="storsimple-virtual-array-update-03-release-notes"></a>StorSimple Virtual Array Update 0,3 viktig information
## <a name="overview"></a>Översikt
I följande versions information identifieras kritiska öppna problem och lösta problem för Microsoft Azure StorSimple virtuell mat ris uppdatering.

Viktig information uppdateras kontinuerligt och eftersom kritiska problem som kräver en lösning upptäcks, läggs de till. Läs igenom informationen i viktig information innan du distribuerar din virtuella StorSimple-matris.

Uppdatering 0,3 motsvarar program varu versionen **10.0.10288.0**.

> [!NOTE]
> Uppdateringar är störande och startar om enheten. Om I/O pågår förorsakar enheten avbrott.
> 
> 

## <a name="whats-new-in-the-update-03"></a>Nyheter i uppdateringen 0,3
Uppdatering 0,3 är i första hand en bugg-korrigerings version. I den här versionen har flera buggar som resulterade i säkerhets kopierings fel i den tidigare versionen åtgärd ATS.

## <a name="issues-fixed-in-the-update-03"></a>Problem som korrigeras i uppdateringen 0,3
Följande tabell innehåller en sammanfattning av problem som korrigeras i den här versionen.

| Nej. | Funktion | Problem |
| --- | --- | --- |
| 1 |Säkerhetskopior |Ett problem upptäcktes i den tidigare versionen där säkerhets kopieringarna inte kunde slutföras för en fil resurs. Om det här problemet uppstår skulle säkerhets kopierings jobbet inte utföras och en kritisk varning har Aktiver ATS på StorSimple Manager tjänsten för att meddela användaren. Det här problemet påverkar inte data på resurserna eller åtkomsten till data. Rotor saken identifierades och korrigerades i den här versionen. <br></br> Korrigeringen gäller inte retroaktivt för resurser som redan ser det här problemet. Kunder som ser det här problemet bör först tillämpa uppdatering 0,3 och sedan kontakta Microsoft Support för att utföra en fullständig säkerhets kopiering av systemet för att åtgärda problemet. I stället för att kontakta Microsoft Support kan kunder även återställa till en ny resurs från en felfri säkerhets kopia för de berörda resurserna. |
| 2 |iSCSI |Ett problem upptäcktes i den tidigare versionen där volymerna skulle försvinna när data kopierades till en volym på den virtuella StorSimple-matrisen. Det här problemet har åtgärd ATS i den här versionen. <br></br> Korrigeringarna träder i kraft endast på nyligen skapade volymer. De här korrigeringarna gäller inte retroaktivt för volymer som redan ser det här problemet. Kunderna uppmanas att ta med de berörda volymerna online via den klassiska Azure-portalen, säkerhetskopiera dessa volymer och sedan återställa volymerna till nya volymer. |

## <a name="known-issues-in-the-update-03"></a>Kända problem i uppdateringen 0,3
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

## <a name="next-step"></a>Nästa steg
[Installera uppdatering 0,3](./storsimple-virtual-array-install-update-06.md) på din virtuella StorSimple-matris.

## <a name="references"></a>Referenser
Letar du efter en äldre versions anteckning? Gå till: 

* [StorSimple Virtual Array Update 0,1 och 0,2 viktig information](storsimple-ova-update-01-release-notes.md)
* [Viktig information om allmän tillgänglighet för StorSimple Virtual Array](./storsimple-virtual-array-update-06-release-notes.md)