---
title: Viktig information om StorSimple 8000-serien uppdatering 2 | Microsoft Docs
description: Beskriver de nya funktionerna, problemen och lösningarna för StorSimple 8000-serien uppdatering 2.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: e2c8bffd-7fc5-4b77-b632-a4f59edacc3a
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: b68e338c2675a3aeefda390d12ab2d82ea1c7a9f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94956661"
---
# <a name="storsimple-8000-series-update-2-release-notes"></a>Viktig information om StorSimple 8000-serien uppdatering 2

## <a name="overview"></a>Översikt
I följande versions information beskrivs de nya funktionerna och de kritiska öppnings problemen för StorSimple 8000-serien uppdatering 2. De innehåller också en lista med de uppdateringar av program varan för StorSimple, driv rutin och disk som ingår i den här versionen. 

Uppdatering 2 kan tillämpas på alla StorSimple-enheter som kör release (GA) eller uppdatering 0,1 via uppdatering 1,2. Den enhets version som är associerad med uppdatering 2 är 6.3.9600.17673.

Granska informationen i versions anteckningarna innan du distribuerar uppdateringen i StorSimple-lösningen.

> [!IMPORTANT]
> * Det tar cirka 4-7 timmar att installera den här uppdateringen (inklusive Windows-uppdateringar). 
> * Uppdatering 2 har uppdateringar för program vara, LSI-drivrutin och SSD-programvara.
> * För nya versioner kanske du inte ser uppdateringar direkt eftersom vi gör en stegvis distribution av uppdateringarna. Vänta några dagar och Sök sedan efter uppdateringar igen eftersom de blir tillgängliga snart.
> 
> 

## <a name="whats-new-in-update-2"></a>Vad är nytt i uppdatering 2
Uppdatering 2 introducerar följande nya funktioner.

* **Lokalt fästa volymer** – i tidigare versioner av StorSimple 8000-serien skiktas data blocken till molnet baserat på användning. Det fanns inget sätt att garantera att block förblir lokalt. När du skapar en volym i uppdatering 2, kan du ange en volym som fästs lokalt och primär data från volymen kommer inte att skiktas i molnet. Ögonblicks bilder av lokalt fästa volymer kopieras fortfarande till molnet för säkerhets kopiering så att molnet kan användas för data rörlighet och haveri beredskap. Du kan också ändra volym typ (det vill säga konvertera volymer på nivå till lokalt fästa volymer och konvertera lokalt fästa volymer till skiktad). 
* **StorSimple-förbättringar för virtuella enheter** – tidigare placerade StorSimple 8000-serien den virtuella enheten som en haveri beredskap eller utvecklings-/test lösning. Det fanns bara en modell av den virtuella enheten (modell 1100). Uppdatering 2 introducerar två virtuella enhets modeller: 
  
  * 8010 (tidigare kallat 1100) – ingen ändring; har en kapacitet på 30 TB och använder Azure standard Storage.
  * 8020 – har en kapacitet på 64 TB och använder Azure Premium Storage för bättre prestanda.
    
    Det finns en enda virtuell hård disk för båda de virtuella enhets modellerna (8010/8020). Första gången du startar den virtuella enheten identifierar den plattforms parametrarna och tillämpar rätt modell version.
* **Nätverks förbättringar** – uppdatering 2 innehåller följande förbättringar i nätverket:
  
  * Flera nätverkskort kan aktive ras för molnet så att redundansväxlingen kan ske om ett nätverkskort kraschar.
  * Förbättringar av routning, med fasta mått för molnbaserade blockerade block.
  * Det gick inte att göra om misslyckade resurser på nytt innan redundansväxlingen.
  * Nya aviseringar för tjänst haverier.
* **Uppdatering av förbättringar** – i uppdatering 1,2 och tidigare har StorSimple 8000-serien uppdaterats via två kanaler: Windows Update för klustring, iSCSI och så vidare, och Microsoft Update för binärfiler och inbyggd program vara.
    Uppdatering 2 använder Microsoft Update för alla uppdaterings paket. Detta bör leda till mindre tids korrigeringar eller att göra redundanser. 
* **Uppdateringar av inbyggd program vara** – följande uppdateringar av inbyggd program vara ingår:
  
  * LSI: lsi_sas2.sys produkt version 2.00.72.10
  * Endast SSD (inga HDD-uppdateringar): XMGG, XGEG, KZ50, F6C2 och VR08
* **Proaktivt stöd** – uppdatering 2 gör att Microsoft kan hämta ytterligare diagnostikinformation från enheten. När vårt drift team identifierar enheter som har problem är vi bättre utrustade att samla in information från enheten och diagnostisera problem. **Genom att godkänna uppdatering 2 kan vi tillhandahålla det proaktiva stödet**.    

## <a name="issues-fixed-in-update-2"></a>Problem som korrigeras i uppdatering 2
Följande tabeller innehåller en sammanfattning av problem som har åtgärd ATS i uppdatering 2.    

| Nej. | Funktion | Problem | Gäller fysisk enhet | Gäller för virtuell enhet |
| --- | --- | --- | --- | --- |
| 1 |Nätverksgränssnitt |Efter en uppgradering till uppdatering 1 rapporterade StorSimple Manager tjänsten att portarna data2 och DATA3 misslyckades på en kontrollant. Det här problemet har åtgärd ATS. |Ja |Inga |
| 2 |Uppdateringar |Efter en uppgradering till uppdatering 1 inträffade akustiska larm aviseringar i den klassiska Azure-portalen på flera enheter. Det här problemet har åtgärd ATS. |Ja |Inga |
| 3 |OpenStack-autentisering |När du använder OpenStack som moln tjänst leverantör kan du få ett fel meddelande om att din Cloud Authentication-sträng var för lång. Problemet har åtgärdats. |Ja |Inga |

## <a name="known-issues-in-update-2"></a>Kända problem i uppdatering 2
Följande tabell innehåller en sammanfattning av kända problem i den här versionen.

| Nej. | Funktion | Problem | Kommentarer/lösning | Gäller fysisk enhet | Gäller för virtuell enhet |
| --- | --- | --- | --- | --- | --- |
| 1 |Disk-kvorum |I sällsynta fall, om majoriteten av diskarna i EBOD-kammaren för en 8600-enhet är frånkopplade, vilket leder till att det inte finns något disk kvorum, kommer lagringspoolen att kopplas från. Den förblir offline även om diskarna återansluts. |Du måste starta om enheten. Om problemet kvarstår kontaktar du Microsoft Support för nästa steg. |Ja |Inga |
| 2 |Felaktigt styrenhets-ID |När en kontroll enhets utbyte utförs kan det hända att styrenheten 0 visas som kontrollant 1. När-avbildningen läses in från peer-noden kan kontroll enhets-ID: t inlednings vis visas som peer-styrenhetens ID. I sällsynta fall kan det här beteendet också visas efter en omstart av systemet. |Ingen användar åtgärd krävs. Den här situationen kommer att lösa sig själv när Controller-ersättningen har slutförts. |Ja |Inga |
| 3 |Lagringskonton |Att använda lagrings tjänsten för att ta bort lagrings kontot är ett scenario som inte stöds. Detta leder till en situation där det inte går att hämta användar data. | |Ja |Ja |
| 4 |Redundans för enhet |Det finns inte stöd för flera växlingar av en volym behållare från samma käll enhet till olika mål enheter. Vid redundans från en enstaka död enhet till flera enheter blir volym behållarna på den första misslyckade enheten förlorad data ägarskap. Efter en sådan redundansväxling visas eller fungerar dessa volym behållare annorlunda när du visar dem i den klassiska Azure-portalen. | |Ja |Inga |
| 5 |Installation |Under StorSimple Adapter för SharePoint-installation måste du ange en enhets-IP för att installationen ska kunna slutföras. | |Ja |Inga |
| 6 |Webbproxy |Om webbproxy-konfigurationen har HTTPS som det angivna protokollet, kommer din enhets-till-tjänst-kommunikation att påverkas och enheten kopplas från. Support paketen skapas också i processen, vilket förbrukar betydande resurser på din enhet. |Kontrol lera att webbproxy-URL: en har HTTP som det angivna protokollet. Mer information finns i [Konfigurera en webbproxy för din enhet](./storsimple-8000-configure-web-proxy.md). |Ja |Inga |
| 7 |Webbproxy |Om du konfigurerar och aktiverar webbproxy på en registrerad enhet måste du starta om den aktiva styrenheten på enheten. | |Ja |Inga |
| 8 |Hög moln svars tid och hög I/O-arbetsbelastning |När din StorSimple-enhet påträffar en kombination av mycket hög moln fördröjning (antal sekunder) och hög I/O-arbetsbelastning, försätts enhets volymerna i ett degraderat tillstånd och i/O-systemet kan Miss lyckas med fel meddelandet "enheten är inte klar". |Du måste manuellt starta om enhets styrenheterna eller utföra en enhets växling för att återställa från den här situationen. |Ja |Inga |
| 9 |Azure PowerShell |När du använder StorSimple-cmdleten **Get-AzureStorSimpleStorageAccountCredential &#124; Select-Object – första 1 – vänta tills** du väljer det första objektet så att du kan skapa ett nytt **VolumeContainer** -objekt, så returnerar cmdleten alla objekt. |Bryt upp cmdleten inom parentes enligt följande: **(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object – första 1 – vänta** |Ja |Ja |
| 10 |Migrering |När flera volym behållare skickas för migreringen är ETA for den senaste säkerhets kopieringen bara korrekt för den första volym containern. Dessutom startar parallell migrering efter att de första fyra säkerhets kopieringarna i den första volym containern har migrerats. |Vi rekommenderar att du migrerar en volym behållare i taget. |Ja |Inga |
| 11 |Migrering |Efter återställningen läggs volymerna inte till i säkerhets kopierings principen eller den virtuella disk gruppen. |Du måste lägga till dessa volymer i en säkerhets kopierings princip för att kunna skapa säkerhets kopior. |Ja |Ja |
| 12 |Migrering |När migreringen är klar får enheten 5000/7000-serien inte komma åt de migrerade data behållarna. |Vi rekommenderar att du tar bort de migrerade data behållarna när migreringen är slutförd och bekräftad. |Ja |Inga |
| 13 |Klona och DR |En StorSimple-enhet som kör uppdatering 1 kan inte klona eller utföra haveri beredskap till en enhet som kör för program uppdatering 1. |Du måste uppdatera mål enheten för att uppdatera 1 för att tillåta dessa åtgärder |Ja |Ja |
| 14 |Migrering |Konfigurations säkerhets kopiering för migrering kan Miss lyckas på en 5000-7000-serie enhet om det finns volym grupper utan associerade volymer. |Ta bort alla tomma volym grupper utan tillhör ande volymer och försök sedan att säkerhetskopiera konfigurationen igen. |Ja |Inga |
| 15 |Azure PowerShell-cmdlets och lokalt fästa volymer |Du kan inte skapa en lokalt fäst volym via Azure PowerShell-cmdletar. (Alla volymer som du skapar via Azure PowerShell kommer att skiktas.) |Använd alltid tjänsten StorSimple Manager för att konfigurera lokalt fästa volymer. |Ja |Inga |
| 16 |Tillgängligt utrymme för lokalt fästa volymer |Om du tar bort en lokalt fäst volym kanske det tillgängliga utrymmet för nya volymer inte uppdateras omedelbart. Tjänsten StorSimple Manager uppdaterar det lokala utrymmet ungefär varje timme. |Vänta en timme innan du försöker skapa den nya volymen. |Ja |Inga |
| 17 |Lokalt fixerade volymer |Återställnings jobbet visar den tillfälliga ögonblicks säkerhets kopian i säkerhets kopierings katalogen, men endast under hela återställnings jobbet. Dessutom exponeras en virtuell disk grupp med prefixet **tmpCollection** på sidan **säkerhets kopierings principer** , men endast för varaktigheten för återställnings jobbet. |Detta kan inträffa om ditt återställnings jobb bara har lokalt fästa volymer eller en blandning av lokalt fästa och nivåbaserade volymer. Om återställnings jobbet bara innehåller skiktade volymer, utförs inte det här beteendet. Ingen åtgärd från användaren krävs. |Ja |Inga |
| 18 |Lokalt fixerade volymer |Om du avbryter ett återställnings jobb och en styrenhets fel inträffar omedelbart efteråt kommer återställnings **jobbet att visas i stället för** att **avbrytas**. Om ett återställnings jobb Miss lyckas och en styrenhets fel inträffar omedelbart efteråt visas återställnings jobbet **avbrutet** i stället för **misslyckat**. |Detta kan inträffa om ditt återställnings jobb bara har lokalt fästa volymer eller en blandning av lokalt fästa och nivåbaserade volymer. Om återställnings jobbet bara innehåller skiktade volymer, utförs inte det här beteendet. Ingen åtgärd från användaren krävs. |Ja |Inga |
| 19 |Lokalt fixerade volymer |Om du avbryter ett återställnings jobb eller om en återställning Miss lyckas och en styrenhets fel inträffar visas ytterligare ett återställnings jobb på sidan **jobb** . |Detta kan inträffa om ditt återställnings jobb bara har lokalt fästa volymer eller en blandning av lokalt fästa och nivåbaserade volymer. Om återställnings jobbet bara innehåller skiktade volymer, utförs inte det här beteendet. Ingen åtgärd från användaren krävs. |Ja |Inga |
| 20 |Lokalt fixerade volymer |Om du försöker konvertera en nivå volym (skapas och klonas med uppdatering 1,2 eller tidigare) till en lokalt fixerad volym och enheten börjar få slut på utrymme eller om det uppstår ett moln avbrott, kan klonerna skadas. |Det här problemet uppstår bara med volymer som har skapats och klon ATS med program vara för uppdatering 2. Detta bör vara ett ovanligt scenario. | | |
| 21 |Volym konvertering |Uppdatera inte den ACR: er som är kopplad till en volym medan en volym omvandling pågår (på nivå till lokalt fäst eller vice versa). Om du uppdaterar ACR: er kan det leda till skadade data. |Vid behov kan du uppdatera ACR: er före volym konverteringen och inte göra några ytterligare ACR-uppdateringar medan konverteringen pågår. | | |

## <a name="controller-and-firmware-updates-in-update-2"></a>Uppdateringar av styrenhet och inbyggd program vara i uppdatering 2
Den här versionen uppdaterar driv rutinen och diskens inbyggda program vara på enheten.

* Mer information om uppdatering för den inbyggda program varan finns i Microsoft Knowledge Base-artikeln 3121900. 
* Mer information om diskens inbyggda program uppdatering finns i Microsoft Knowledge Base-artikeln 3121899.

## <a name="virtual-device-updates-in-update-2"></a>Uppdateringar av virtuella enheter i uppdatering 2
Den här uppdateringen kan inte tillämpas på den virtuella enheten. Nya virtuella enheter måste skapas. 

## <a name="next-step"></a>Nästa steg
Lär dig hur du [installerar uppdatering 2](./storsimple-8000-install-update-5.md) på din StorSimple-enhet.