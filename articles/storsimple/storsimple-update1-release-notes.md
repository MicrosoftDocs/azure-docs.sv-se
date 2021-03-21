---
title: StorSimple 8000-serien uppdatering 1,2 viktig information | Microsoft Docs
description: Beskriver de nya funktionerna, problemen och lösningarna för StorSimple 8000-serien uppdatering 1,2.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 6c9aae87-6f77-44b8-b7fa-ebbdc9d8517c
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3a5ba4b27ae19999237edb75c50db36ef57a10f1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94956695"
---
# <a name="update-12-release-notes-for-your-storsimple-8000-series-device"></a>Uppdatering 1,2-viktig information för din enhet med StorSimple 8000-serien

## <a name="overview"></a>Översikt
I följande versions information beskrivs de nya funktionerna och de kritiska öppnings problemen för StorSimple 8000-serien uppdateras 1,2. De innehåller också en lista med de uppdateringar av program varan för StorSimple, driv rutiner och disk som ingår i den här versionen. 

Uppdatering 1,2 kan tillämpas på alla StorSimple-enheter som kör release (GA), uppdatera 0,1, uppdatera 0,2 eller uppdatera 0,3 program vara. Uppdatering 1,2 är inte tillgänglig om enheten kör uppdatering 1 eller uppdateringen 1,1. Om enheten kör utgåva (GA) [kontaktar du Microsoft Support](./storsimple-8000-contact-microsoft-support.md) för att hjälpa dig att installera den här uppdateringen.

I följande tabell visas de enhets program versioner som motsvarar uppdateringar 1, 1,1 och 1,2.

| Om du kör Update... | Detta är enhetens program varu version. |
| --- | --- |
| Uppdatering 1.2 |6.3.9600.17584 |
| Uppdatering 1.1 |6.3.9600.17521 |
| Uppdatering 1,0 |6.3.9600.17491 |

Granska informationen i versions anteckningarna innan du distribuerar uppdateringen i StorSimple-lösningen. Mer information finns i så här [installerar du uppdatering 1,2 på din StorSimple-enhet](./storsimple-8000-install-update-5.md). 

> [!IMPORTANT]
> * Det tar cirka 5-10 timmar att installera den här uppdateringen (inklusive Windows-uppdateringar). 
> * Uppdatering 1,2 har uppdateringar för program vara, LSI-drivrutin och disk-firmware. Installera genom att följa anvisningarna i [installera uppdatering 1,2 på din StorSimple-enhet](./storsimple-8000-install-update-5.md).
> * För nya versioner kanske du inte ser uppdateringar direkt eftersom vi gör en stegvis distribution av uppdateringarna. Sök efter uppdateringar på några dagar igen när de blir tillgängliga snart.
> 
> 

## <a name="whats-new-in-update-12"></a>Nyheter i uppdatering 1,2
De här funktionerna släpptes först med uppdatering 1 som gjorts tillgänglig för en begränsad uppsättning användare. Med uppdatering 1,2-versionen kan de flesta av StorSimple-användarna se följande nya funktioner och förbättringar:

* **Migrering från 5000-7000-serien till 8000-seriens enheter** – den här versionen introducerar en ny migrering som gör att användare av StorSimple 5000-7000-serien kan migrera sina data till en fysisk installation av StorSimple 8000-serien eller en virtuell installation. Migrerings funktionen har två nyckel värdes propositioner:                                                                  
  
  * **Affärs kontinuitet**, genom att aktivera migrering av befintliga data på 5000-7000-seriens enheter till 8000 Series-enheter.
  * **Förbättrade funktions erbjudanden i 8000 serie apparater**, till exempel effektiv centraliserad hantering av flera installationer via StorSimple Manager tjänst, bättre maskin vara och uppdaterad inbyggd program vara, virtuella apparater, data mobilitet och funktioner i den framtida översikten.
    
    I [migreringsguiden](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b) finns information om hur du migrerar en StorSimple 5000-7000-serie till en enhet med 8000-serien. 
* **Tillgängligheten i Azure Government Portal** – StorSimple är nu tillgänglig i Azure Government portalen. Se så här [distribuerar du en StorSimple-enhet i Azure Government-portalen](./index.yml).
* **Stöd för andra moln tjänst leverantörer** – andra moln tjänst leverantörer som stöds är Amazon S3, Amazon S3 med RR, HP och OpenStack (beta).
* **Uppdatera till de senaste lagrings-API: erna** – med den här versionen har StorSimple uppdaterats till de senaste API: erna för Azure Storage tjänsten. StorSimple 8000-seriens enheter som kör äldre versioner av 1 program vara (version, 0,1, 0,2 och 0,3) använder versioner av Azure Storage tjänst-API: er som är äldre än 17 juli 2009. Som anges i det uppdaterade [meddelandet om borttagning av lagrings tjänst versioner](/archive/blogs/windowsazurestorage/microsoft-azure-storage-service-version-removal-update-extension-to-2016), senast den 1 augusti 2016, kommer dessa API: er att vara inaktuella. Det är absolut nödvändigt att använda StorSimple 8000-serien uppdatering 1 före den 1 augusti 2016. Om du inte gör det kommer StorSimple-enheter att sluta fungera korrekt.
* **Stöd för Zone-redundant lagring (ZRS)** – med uppgraderingen till den senaste versionen av lagrings-API: erna kommer StorSimple 8000-serien att ha stöd för zon redundant lagring (ZRS) förutom lokalt redundant lagring (LRS) och Geo-redundant lagring (GRS). Läs den här [artikeln om Azure Storage redundans alternativ](../storage/common/storage-redundancy.md) för ZRS-information.
* **Förbättrad inledande distribution och uppdaterings upplevelse** – i den här versionen har installations-och uppdaterings processerna förbättrats. Installationen via installations guiden har förbättrats för att ge feedback till användaren om nätverks konfigurationen och brand Väggs inställningarna är felaktiga. Ytterligare diagnostiska cmdlets har angetts för att hjälpa dig att felsöka nätverk av enheten. Mer information om de nya diagnostiska cmdletar som används för fel sökning finns i [artikeln fel söknings distribution](./storsimple-8000-troubleshoot-deployment.md) .

## <a name="issues-fixed-in-update-12"></a>Problem som korrigeras i uppdatering 1,2
Följande tabell innehåller en sammanfattning av problem som korrigerades i updates 1,2, 1,1 och 1.    

| Nej. | Funktion | Problem | Korrigerad i uppdatering | Gäller fysisk enhet | Gäller för virtuell enhet |
| --- | --- | --- | --- | --- | --- |
| 1 |Windows PowerShell för StorSimple |När en användare fjärråtkomst fjärråtkomst till StorSimple-enheten med hjälp av Windows PowerShell för StorSimple och sedan startade installations guiden, påträffades en krasch så fort data 0 IP var indata. Den här buggen korrigeras nu i uppdatering 1. |Uppdatering 1 |Ja |Ja |
| 2 |Fabriksåterställning |I vissa fall, när du utförde en fabriks återställning, har StorSimple-enheten fastnat och visat meddelandet: **återställning till fabrik pågår (fas 8)**. Detta hände om du tryckte på CTRL + C medan cmdleten pågår. Den här buggen är nu åtgärdad. |Uppdatering 1 |Ja |Inga |
| 3 |Fabriksåterställning |Efter en misslyckad fabriks återställning med dubbla styrenheter tillät du att fortsätta med enhets registreringen. Detta resulterade i en system konfiguration som inte stöds. I uppdatering 1 visas ett fel meddelande och registreringen blockeras på en enhet som har en misslyckad fabriks återställning. |Uppdatering 1 |Ja |Inga |
| 4 |Fabriksåterställning |I vissa fall har falska positiva positiva matchnings varningar Aktiver ATS. Felaktiga aviseringar om fel matchning kommer inte längre att genereras på enheter som kör uppdatering 1. |Uppdatering 1 |Ja |Inga |
| 5 |Fabriksåterställning |Om en fabriks återställning avbröts före slutförandet, har enheten angett återställnings läge och inte gett dig åtkomst till Windows PowerShell för StorSimple. Den här buggen är nu åtgärdad. |Uppdatering 1 |Ja |Inga |
| 6 |Haveriberedskap |Ett fel i haveri beredskap (DR) har åtgärd ATS där DR-fel skulle uppstå under identifieringen av säkerhets kopior på mål enheten. |Uppdatering 1 |Ja |Ja |
| 7 |Övervaknings indikatorer |I vissa fall angavs ingen korrekt status i övervaknings lamporna på bak sidan av installationen. Den blå INDIKATORn stängdes av. Lysdioderna för DATA 0 och DATA 1 blinkar även om dessa gränssnitt inte har kon figurer ATS. Problemet har åtgärd ATS och övervaknings lamporna visar nu rätt status. |Uppdatering 1 |Ja |Inga |
| 8 |Övervaknings indikatorer |I vissa fall, efter att ha använt uppdatering 1, är det blå ljuset på den aktiva styrenheten inaktiverat så att det blir svårt att identifiera den aktiva styrenheten. Det här problemet har åtgärd ATS i den här korrigerings versionen. |Uppdatering 1.2 |Ja |Inga |
| 9 |Nätverksgränssnitt |I tidigare versioner kan en StorSimple-enhet som kon figurer ATS med en icke-dirigerbart Gateway bli offline. I den här versionen har routing-måttet för data 0 gjorts lägsta; Det innebär att även om andra nätverks gränssnitt är molnbaserade, kommer all moln trafik från enheten att dirigeras via data 0. |Uppdatering 1 |Ja |Ja |
| 10 |Säkerhetskopior |Ett fel i uppdatering 1 som gjorde att säkerhets kopieringen misslyckades efter 24 dagar har åtgärd ATS i uppdatering 1,1 för korrigerings versionen. |Uppdatering 1.1 |Ja |Ja |
| 11 |Säkerhetskopior |En bugg i tidigare versioner ledde till dåliga prestanda för moln ögonblicks bilder med låga ändrings hastigheter. Den här buggen har åtgärd ATS i denna korrigerings version. |Uppdatering 1.2 |Ja |Ja |
| 12 |Uppdateringar |Ett fel i uppdatering 1 som rapporterade en misslyckad uppgradering och gjorde att styrenheterna hamnade i återställnings läge har åtgärd ATS i den här korrigerings versionen. |Uppdatering 1.2 |Ja |Ja |

## <a name="known-issues-in-update-12"></a>Kända problem i uppdatering 1,2
Följande tabell innehåller en sammanfattning av kända problem i den här versionen.

| Nej. | Funktion | Problem | Kommentarer/lösning | Gäller fysisk enhet | Gäller för virtuell enhet |
| --- | --- | --- | --- | --- | --- |
| 1 |Disk-kvorum |I sällsynta fall, om majoriteten av diskarna i EBOD-kammaren för en 8600-enhet är frånkopplade, vilket innebär att lagringspoolen är offline. Den förblir offline även om diskarna återansluts. |Du måste starta om enheten. Om problemet kvarstår kontaktar du Microsoft Support för nästa steg. |Ja |Inga |
| 2 |Felaktigt styrenhets-ID |När en kontroll enhets utbyte utförs kan det hända att styrenheten 0 visas som kontrollant 1. När-avbildningen läses in från peer-noden kan kontroll enhets-ID: t inlednings vis visas som peer-styrenhetens ID. I sällsynta fall kan det här beteendet också visas efter en omstart av systemet. |Ingen användar åtgärd krävs. Den här situationen kommer att lösa sig själv när Controller-ersättningen har slutförts. |Ja |Inga |
| 3 |Lagringskonton |Att använda lagrings tjänsten för att ta bort lagrings kontot är ett scenario som inte stöds. Detta leder till en situation där det inte går att hämta användar data. |Ja |Ja | |
| 4 |Redundans för enhet |Det finns inte stöd för flera växlingar av en volym behållare från samma käll enhet till olika mål enheter. Vid redundansväxling från en enstaka död enhet till flera enheter blir volym behållarna på den första misslyckade enheten förlorad data ägarskap. Efter en sådan redundansväxling visas eller fungerar dessa volym behållare annorlunda när du visar dem i den klassiska Azure-portalen. | |Ja |Inga |
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

## <a name="physical-device-updates-in-update-12"></a>Uppdateringar av fysiska enheter i uppdatering 1,2
Om korrigerings uppdatering 1,2 används på en fysisk enhet (som kör versioner före uppdatering 1), kommer program versionen att ändras till 6.3.9600.17584.

## <a name="controller-and-firmware-updates-in-update-12"></a>Uppdateringar av styrenhet och inbyggd program vara i uppdatering 1,2
Den här versionen uppdaterar driv rutinen och diskens inbyggda program vara på enheten.

* Mer information om uppdatering för SAS-styrenheten finns [i uppdatering 1 för LSI SAS-styrenheter i Microsoft Azure StorSimple](https://support.microsoft.com/kb/3043005)-installationen. 
* Mer information om diskens inbyggda program uppdatering finns i [disk firmware uppdatering 1 för Microsoft Azure StorSimple](https://support.microsoft.com/kb/3063416)-installationen.

## <a name="virtual-device-updates-in-update-12"></a>Uppdateringar av virtuella enheter i uppdatering 1,2
Den här uppdateringen kan inte tillämpas på den virtuella enheten. Nya virtuella enheter måste skapas. 

## <a name="next-steps"></a>Nästa steg
* [Installera uppdatering 1,2 på enheten](./storsimple-8000-install-update-5.md).