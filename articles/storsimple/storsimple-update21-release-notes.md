---
title: StorSimple 8000-serien uppdatering 2,2 viktig information | Microsoft Docs
description: Beskriver de nya funktionerna, problemen och lösningarna för StorSimple 8000-serien uppdatering 2,2.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 5cf03ea8-2a0f-4552-b6dc-7ea517783d7b
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 73b9ecd03875b60ed2d9b9d4c8e8a3a0c8de3cfa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94956610"
---
# <a name="storsimple-8000-series-update-22-release-notes"></a>StorSimple 8000-serien uppdatering 2,2 viktig information

## <a name="overview"></a>Översikt
I följande versions information beskrivs de nya funktionerna och de kritiska öppnings problemen för StorSimple 8000-serien uppdateras 2,2. De innehåller också en lista över de StorSimple program uppdateringar som ingår i den här versionen.

Uppdatering 2,2 kan tillämpas på alla StorSimple-enheter som kör release (GA) eller uppdatering 0,1 via uppdatering 2,1. Den enhets version som är associerad med uppdatering 2,2 är 6.3.9600.17708.

Granska informationen i versions anteckningarna innan du distribuerar uppdateringen i StorSimple-lösningen.

> [!IMPORTANT]
> * Uppdatering 2,2 har endast uppdateringar för program vara. Det tar cirka 1,5 – 2 timmar att installera den här uppdateringen. 
> * Om du kör uppdatering 2,1 rekommenderar vi att du installerar uppdatering 2,2 så snart som möjligt.
> * För nya versioner kanske du inte ser uppdateringar direkt eftersom vi gör en stegvis distribution av uppdateringarna. Vänta några dagar och Sök sedan efter uppdateringar igen eftersom de blir tillgängliga snart.
> 
> 

## <a name="whats-new-in-update-22"></a>Nyheter i uppdatering 2,2
Följande viktiga förbättringar har gjorts i uppdatering 2,2.

* **Automatiserad optimering av utrymmes regenerering** – när data tas bort på tunt allokerade volymer måste de oanvända lagrings blocken frigöras. Den här versionen har förbättrat utrymmes återtagnings processen från molnet, vilket innebär att det outnyttjade utrymmet blir tillgängligt snabbare jämfört med tidigare versioner.
* **Förbättringar av ögonblicks bilder** – uppdatering 2,2 har förbättrat tiden för att bearbeta en moln ögonblicks bild i vissa scenarier där stora volymer används och det finns minimalt med data omsättning. Ett scenario som kan dra nytta av den här förbättringen är Arkiv volymerna.
* **Härdning av stöd pakets insamling** – det har gjorts förbättringar av hur support paketet samlas in och överförs i den här versionen. 
* **Uppdatering av tillförlitlighet** – den här versionen har fel korrigeringar som resulterar i en förbättrad uppdaterings tillförlitlighet.

## <a name="issues-fixed-in-update-22"></a>Problem som korrigeras i uppdatering 2,2
Följande tabeller innehåller en sammanfattning av problem som korrigerades i updates 2,2 och 2,1.    

| Nej | Visning av aktuellt objekt | Problem | Gäller fysisk enhet | Gäller för virtuell enhet |
| --- | --- | --- | --- | --- |
| 1 |Värdprestanda |I den tidigare versionen observerades prestanda problem på värd sidan under skapandet av en lokalt fäst volym och under konverteringen av en nivå volym till en lokalt fäst volym. De här problemen korrigeras i den här versionen vilket leder till en förbättring av värd prestandan vid skapande och konvertering av volymer. |Ja |Inga |
| 2 |Lokalt fixerade volymer |I sällsynta fall kraschar systemet när en lokalt fäst volym skapas. Den här buggen har åtgärd ATS i den här versionen. |Ja |Inga |
| 3 |Nivåer |Sporadiskt kraschar när metadata för StorSimple-molnet (8010 och 8020) på nivå av molnet. Det här problemet åtgärdas i den här versionen. |Inga |Ja |
| 4 |Skapa ögonblicks bild |Det fanns problem som rör skapandet av stegvisa ögonblicks bilder i scenarier med stora volymer och minimal till ingen data omsättning. De här problemen korrigeras i den här versionen. |Ja |Ja |
| 5 |OpenStack-autentisering |När du använder OpenStack som moln tjänst leverantör skulle användaren kunna köra ett ovanligt fel som är relaterat till autentiseringen där JSON-parsern resulterade i en krasch. Den här buggen har åtgärd ATS i den här versionen. |Ja |Inga |
| 6 |Kopia på värd Sidan |I tidigare versioner av program vara setts ett ovanligt fel som är relaterat till ODX-tidsinställningen när data kopierades från en volym till en annan. Detta skulle leda till en kontrollants redundans och systemet kan gå i återställnings läge. Den här buggen har åtgärd ATS i den här versionen. |Ja |Inga |
| 7 |Windows Management Instrumentation (WMI) |I tidigare versioner av program vara fanns det flera instanser av webbproxy-fel med undantaget " \<ManagementException> providerns inläsnings fel". Den här buggen skrevs till en WMI-minnes läcka och har nu åtgärd ATS. |Ja |Inga |
| 8 |Uppdatera |I vissa sällsynta instanser i tidigare versioner av program vara fick användaren ett "CisPowershellHcsscripterror" vid försök att söka efter eller installera uppdateringar. Det här problemet åtgärdas i den här versionen. |Ja |Ja |
| 9 |Support paket |I den här versionen har det gjorts förbättringar av hur support paketet samlas in och överförs. |Ja |Ja |

## <a name="known-issues-in-update-22"></a>Kända problem i uppdatering 2,2
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
| 20 |Lokalt fixerade volymer |Om du försöker konvertera en nivå volym (skapas och klonas med uppdatering 1,2 eller tidigare) till en lokalt fixerad volym och enheten börjar få slut på utrymme eller om det uppstår ett moln avbrott, kan klonerna skadas. |Det här problemet uppstår bara med volymer som har skapats och klon ATS med program vara för uppdatering 2,1. Detta bör vara ett ovanligt scenario. | | |
| 21 |Volym konvertering |Uppdatera inte den ACR: er som är kopplad till en volym medan en volym omvandling pågår (på nivå till lokalt fäst eller vice versa). Om du uppdaterar ACR: er kan det leda till skadade data. |Vid behov kan du uppdatera ACR: er före volym konverteringen och inte göra några ytterligare ACR-uppdateringar medan konverteringen pågår. | | |

## <a name="controller-and-firmware-updates-in-update-22"></a>Uppdateringar av styrenhet och inbyggd program vara i uppdatering 2,2
Den här versionen har endast program uppdateringar. Men om du uppdaterar från en version före uppdatering 2 måste du installera driv rutin, Storport, Spaceport och (i vissa fall) uppdateringar av inbyggd disk på enheten.

Mer information om hur du installerar uppdateringar för driv rutin, Storport, Spaceport och disk inbyggd program vara finns i [installera uppdatering 2,2](./storsimple-8000-install-update-5.md) på StorSimple-enheten.

## <a name="virtual-device-updates-in-update-22"></a>Uppdateringar av virtuella enheter i uppdatering 2,2
Den här uppdateringen kan inte tillämpas på den virtuella enheten. Nya virtuella enheter måste skapas. 

## <a name="next-step"></a>Nästa steg
Lär dig hur du [installerar uppdatering 2,2](./storsimple-8000-install-update-5.md) på din StorSimple-enhet.