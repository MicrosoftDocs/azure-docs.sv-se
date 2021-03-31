---
title: Viktig information om StorSimple 8000 Series Update 3
description: Beskriver de nya funktionerna, problemen och lösningarna för StorSimple 8000-serien uppdatering 3.
author: alkohli
ms.assetid: 2158aa7a-4ac3-42ba-8796-610d1adb984d
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e081acc357c1b8872dd3a25c552d4cbe9785e212
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96007077"
---
# <a name="update-3-release-notes-for-your-storsimple-8000-series-device"></a>Uppdatering 3 viktig information för din StorSimple 8000-serie enhet

## <a name="overview"></a>Översikt
I följande versions information beskrivs de nya funktionerna och de kritiska öppnings problemen för StorSimple 8000-serien uppdatering 3. De innehåller också en lista över de StorSimple program uppdateringar som ingår i den här versionen. 

Uppdatering 3 kan tillämpas på alla StorSimple-enheter som kör release (GA) eller uppdatering 0,1 via uppdatering 2,2. Den enhets version som är associerad med uppdatering 3 är 6.3.9600.17759.

Granska informationen i versions anteckningarna innan du distribuerar uppdateringen i StorSimple-lösningen.

> [!IMPORTANT]
> * Uppdatering 3 har enhets program vara, LSI-drivrutin och inbyggd program vara, samt Storport-och Spaceport-uppdateringar. Det tar cirka 1,5 – 2 timmar att installera den här uppdateringen. 
> * För nya versioner kanske du inte ser uppdateringar direkt eftersom vi gör en stegvis distribution av uppdateringarna. Vänta några dagar och Sök sedan efter uppdateringar igen eftersom de blir tillgängliga snart.
> 
> 

## <a name="whats-new-in-update-3"></a>Nyheter i uppdatering 3
Följande viktiga förbättringar och fel korrigeringar har gjorts i uppdatering 3.

* **Automatisk ändring av utrymmes** återtagning – genom att starta uppdatering 3 körs algoritmerna för utrymmes återtagning på systemets vänte läge på systemets vilo läge, vilket ger snabbare körning. Mer information om vilka portar som krävs för att arbeta med utrymmes återtagning finns i [StorSimple nätverks krav](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device).
* **Prestanda förbättringar** – uppdatering 3 har förbättrat Läs-och skriv prestanda för molnet.
* **Migrerings-relaterade förbättringar** – i den här versionen har flera fel korrigeringar och förbättringar gjorts för migreringen från 5000/7000-seriens enheter till 8000-seriens enheter. Om du vill ha mer information om hur du använder funktionen migrering går du till [migrering från 5000/7000-seriens enhet till 8000 serien het](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b). 
* **Övervaka relaterade korrigeringar** – i den här versionen har buggar som rör övervaknings diagram, instrument panel och instrument panel åtgärd ATS.

## <a name="issues-fixed-in-update-3"></a>Problem som korrigeras i uppdatering 3
Följande tabeller innehåller en sammanfattning av problem som korrigerades i uppdatering 3.    

| Nej | Visning av aktuellt objekt | Problem | Gäller fysisk enhet | Gäller för virtuell enhet |
| --- | --- | --- | --- | --- |
| 1 |Datamigrering på värd Sidan |I den tidigare versionen förslöts StorSimple Cloud Applianceen offline under en datamigrering på värd sidan. Det här problemet åtgärdas i den här versionen. |Inga |Ja |
| 2 |Lokalt fixerade volymer |I den tidigare versionen fanns det problem som rör i/O-fel, volym konverterings fel och Datapath-fel för lokalt fästa volymer. De här problemen var rot-orsakade och korrigerade i den här versionen. |Ja |Inga |
| 3 |Övervakning |Det fanns flera problem som rör rapporterings enheter och övervakning samt enhets instrument panel diagram där felaktig information visades för lokalt fästa volymer. De här problemen korrigeras i den här versionen. |Ja |Inga |
| 4 |I/O för tung skrivningar |När du använder StorSimple för arbets belastningar som involverar tunga skrivningar, skulle användaren kunna köra ett ovanligt fel där arbets minnet var i molnet. Den här buggen har åtgärd ATS i den här versionen. |Ja |Ja |
| 5 |Backup |I vissa sällsynta instanser, i tidigare versioner av program vara, när användaren använde en säkerhets kopia av en fjärrklonering, skulle de kunna köras i moln fel och åtgärden skulle då bli fel. I den här versionen är problemet löst och åtgärden slutförs. |Ja |Ja |
| 6 |Säkerhetskopieringsprincip |I vissa sällsynta instanser, i tidigare versioner av program vara, uppstod ett fel som rör borttagningen av säkerhets kopierings principen. Det här problemet åtgärdas i den här versionen. |Ja |Ja |

## <a name="known-issues-in-update-3"></a>Kända problem i uppdatering 3
Följande tabell innehåller en sammanfattning av kända problem i den här versionen.

| Nej. | Funktion | Problem | Kommentarer/lösning | Gäller fysisk enhet | Gäller för virtuell enhet |
| --- | --- | --- | --- | --- | --- |
| 1 |Disk-kvorum |I sällsynta fall, om majoriteten av diskarna i EBOD-kammaren för en 8600-enhet är frånkopplade, vilket leder till att det inte finns något disk kvorum, kommer lagringspoolen att kopplas från. Den förblir offline även om diskarna återansluts. |Du måste starta om enheten. Om problemet kvarstår kontaktar du Microsoft Support för nästa steg. |Ja |Inga |
| 2 |Felaktigt styrenhets-ID |När en kontroll enhets utbyte utförs kan det hända att styrenheten 0 visas som kontrollant 1. När-avbildningen läses in från peer-noden kan kontroll enhets-ID: t inlednings vis visas som peer-styrenhetens ID. I sällsynta fall kan det här beteendet också visas efter en omstart av systemet. |Ingen användar åtgärd krävs. Den här situationen kommer att lösa sig själv när Controller-ersättningen har slutförts. |Ja |Inga |
| 3 |Lagringskonton |Att använda lagrings tjänsten för att ta bort lagrings kontot är ett scenario som inte stöds. Detta leder till en situation där det inte går att hämta användar data. | |Ja |Ja |
| 4 |Redundans för enhet |Det finns inte stöd för flera växlingar av en volym behållare från samma käll enhet till olika mål enheter. Vid redundans från en enstaka död enhet till flera enheter blir volym behållarna på den första misslyckade enheten förlorad data ägarskap. Efter en sådan redundansväxling visas eller fungerar dessa volym behållare annorlunda när du visar dem i den klassiska Azure-portalen. | |Ja |Inga |
| 5 |Installation |Under StorSimple Adapter för SharePoint-installation måste du ange en enhets-IP för att installationen ska kunna slutföras. | |Ja |Inga |
| 6 |Webbproxy |Om webbproxy-konfigurationen har HTTPS som det angivna protokollet, kommer din enhets-till-tjänst-kommunikation att påverkas och enheten kopplas från. Support paketen skapas också i processen, vilket förbrukar betydande resurser på din enhet. |Kontrol lera att webbproxy-URL: en har HTTP som det angivna protokollet. Mer information finns i [Konfigurera en webbproxy för din enhet](storsimple-8000-configure-web-proxy.md). |Ja |Inga |
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
| 22 |Uppdateringar |När du tillämpar uppdatering 3, visar **underhålls** sidan i den klassiska Azure-portalen följande meddelande som är relaterade till uppdatering 2 – "StorSimple 8000-serien uppdatering 2 innehåller möjligheten för Microsoft att proaktivt samla in logg information från enheten när vi identifierar potentiella problem". Detta är vilseledande eftersom det visar att enheten uppdateras till uppdatering 2. När enheten har uppdaterats till uppdatering 3 försvinner meddelandet. |Det här beteendet kommer att åtgärdas i en framtida version. |Ja |Inga |

## <a name="controller-and-firmware-updates-in-update-3"></a>Uppdateringar för styrenhet och inbyggd program vara i uppdatering 3
I den här versionen har du uppdatering av LSI-drivrutin och inbyggd program vara. Mer information om hur du installerar LSI-drivrutinen och uppdateringar av inbyggd program vara finns i [installera uppdatering 3](./index.yml) på din StorSimple-enhet.

## <a name="virtual-device-updates-in-update-3"></a>Uppdateringar av virtuella enheter i uppdatering 3
Den här uppdateringen kan inte tillämpas på StorSimple Cloud Appliance (kallas även den virtuella enheten). Nya virtuella enheter måste skapas. 

## <a name="next-step"></a>Nästa steg
Lär dig hur du [installerar uppdatering 3](./index.yml) på din StorSimple-enhet.