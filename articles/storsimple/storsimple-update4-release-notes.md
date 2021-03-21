---
title: Viktig information om StorSimple 8000-serien uppdatering 4 | Microsoft Docs
description: Beskriver de nya funktionerna, problemen och lösningarna för StorSimple 8000-serien uppdatering 4.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: 060a52b61dcd43fe0d05b2a8ef594c0ab9f2cc9e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94954060"
---
# <a name="storsimple-8000-series-update-4-release-notes"></a>Viktig information om StorSimple 8000-seriens uppdatering 4

## <a name="overview"></a>Översikt

I följande versions information beskrivs de nya funktionerna och de kritiska öppnings problemen för StorSimple 8000-serien uppdatering 4. De innehåller också en lista över de StorSimple program uppdateringar som ingår i den här versionen. 

Uppdatering 4 kan tillämpas på alla StorSimple-enheter som kör release (GA) eller uppdatering 0,1 via uppdatering 3,1. Den enhets version som är associerad med uppdatering 4 är 6.3.9600.17820.

Granska informationen i versions anteckningarna innan du distribuerar uppdateringen i StorSimple-lösningen.

> [!IMPORTANT]
> * Uppdatering 4 har enhets program vara, USM POLICYEGENSKAPER inbyggd program vara, LSI-drivrutin och inbyggd program vara, inbyggd disk-firmware, Storport och Spaceport, säkerhet och andra OS-uppdateringar. Det tar cirka 4 timmar att installera den här uppdateringen. Uppdatering av inbyggd disk program vara är en störnings uppdatering och resulterar i en stillestånds tid för enheten. Vi rekommenderar att du installerar uppdatering 4 för att hålla enheten uppdaterad. 
> * För nya versioner kanske du inte ser uppdateringar direkt eftersom vi gör en stegvis distribution av uppdateringarna. Vänta några dagar och Sök sedan efter uppdateringar igen eftersom de blir tillgängliga snart.

## <a name="whats-new-in-update-4"></a>Nyheter i uppdatering 4

Följande viktiga förbättringar och fel korrigeringar har gjorts i uppdatering 4.

* **Smartare utrymme för automatisk utrymmes** återtagning – i uppdatering 4 förbättras algoritmerna för automatisk utrymmes återtagning för att justera utrymmes återtagnings cyklerna baserat på det förväntade återkrävda utrymmet i molnet. 

* **Prestanda förbättringar för lokalt fästa volymer** – uppdatering 4 har förbättrat prestanda för lokalt fästa volymer i scenarier med hög data inmatning (data som är jämförbara med volym storleken).

* **Termisk karta-baserad återställning** – i tidigare versioner, efter en haveri beredskap (Dr), återställdes data från molnet baserat på åtkomst mönster som resulterade i långsamma prestanda. 

    En ny funktion implementeras i uppdatering 4 som spårar data som används ofta för att skapa en termisk karta när enheten används före DR (de mest använda data segmenten har hög värme medan mindre använda segment har låg värme). Efter DR använder StorSimple termisk karta för att automatiskt återställa och dehydratisera data från molnet. 

    Alla återställningar är nu termisk karta-baserade återställningar. Mer information om hur du frågar och avbryter termisk karta-baserade återställnings-och ÅTERUPPVÄCKNING-jobb finns i [Windows PowerShell för StorSimple cmdlet-referens](/powershell/module/hcs/?viewFallbackFrom=winserverr2-ps).

* **StorSimple Diagnostics Tool** – i uppdatering 4 släpps ett StorSimple-diagnostiktest för att möjliggöra enkel diagnostisering och fel sökning av problem som rör system-, nätverks-, prestanda-och maskin varu komponent hälsa. Verktyget körs via Windows PowerShell för StorSimple. Mer information finns i [fel sökning med verktyget StorSimple-diagnostik](storsimple-8000-diagnostics.md).

* **UI-baserat StorSimple-Migreringsverktyg** – före den här versionen krävde migrering av data från 5000-7000-serien användarna att köra en del av migreringen av arbets flödet med hjälp av Azure PowerShell-gränssnittet. I den här versionen görs ett lättanvänt UI-baserat StorSimple-Migreringsverktyg för support för att under lätta samma arbets flöde för migrering. Det här verktyget möjliggör även konsolidering av återställnings buckets. 

* **FIPS-relaterade ändringar** – den här versionen kommer sedan att aktive ras som standard på alla StorSimple 8000-serie enheter för både Microsoft Azure Government-och Azures offentliga moln konton.

* **Uppdatera ändringar** – i den här versionen har buggar som rör uppdaterings fel åtgärd ATS.

* **Avisering om diskfel** – en ny avisering som varnar användaren om att de väntande disk felen läggs till i den här versionen. Om du stöter på den här aviseringen kontaktar du Microsoft Support för att leverera en ersättnings disk. Mer information finns i [maskin varu aviseringar på din StorSimple-enhet](storsimple-8000-manage-alerts.md#hardware-alerts).

* **Ändringar av styrenhetens ersättning** – en cmdlet som gör det möjligt för användaren att fråga om status för kontrollantens ersättnings process läggs till i den här versionen. Mer information finns på [cmdleten för att ställa frågor om ersättnings status för kontrollanten](/powershell/module/hcs/?viewFallbackFrom=winserverr2-ps).


## <a name="issues-fixed-in-update-4"></a>Problem som korrigeras i uppdatering 4

Följande tabell innehåller en sammanfattning av problem som korrigerades i uppdatering 4.    

| Nej | Visning av aktuellt objekt | Problem | Gäller fysisk enhet | Gäller för virtuell enhet |
| --- | --- | --- | --- | --- |
| 1 |Redundans |I den tidigare versionen, efter redundansväxlingen, uppstod ett problem med rensningen som observerats på kund webbplatsen. Det här problemet åtgärdas i den här versionen. |Ja |Ja |
| 2 |Lokalt fixerade volymer |I den tidigare versionen fick du ett problem med att skapa en relaterad volym för lokalt fästa volymer som skulle resultera i att volymen skapas fel. Det här problemet var rot-orsakade och korrigerade i den här versionen. |Ja |Inga |
| 3 |Support paket |I tidigare versioner fanns det problem som rör ett support paket som skulle resultera i ett system. OutOfMemory-undantag eller andra fel som resulterade i ett fel när ett support paket skapas. Dessa buggar åtgärdas i den här versionen. |Ja |Ja |
| 4 |Övervakning |I tidigare versioner fick du ett problem som rör övervaknings diagram för lokalt fästa volymer där förbrukningen visades i EB. Den här buggen har lösts i den här versionen. |Ja |Ja |
| 5 |Migrering |I tidigare versioner fanns det flera problem som rör tillförlitligheten vid migrering från 5000-7000-serien till 8000-seriens enheter. De här problemen har åtgärd ATS i den här versionen. |Ja |Ja |
| 6 |Uppdatera |Om det uppstod ett uppdaterings problem i tidigare versioner skulle styrenheterna gå in i återställnings läge och användaren kan därför inte fortsätta med uppdateringen och måste kontakta Microsoft Support. <br> Detta beteende har ändrats i den här versionen. Om användaren har ett uppdaterings problem när båda styrenheterna har kört samma version (uppdatering 4), går det inte att använda kontroll enheterna i återställnings läge. Om användaren stöter på det här felet, rekommenderar vi att de väntar på en bit och försöker sedan uppdatera igen. Försöket kunde inte utföras. Om försöket Miss lyckas bör det kontaktas Microsoft Support. |Ja |Ja |


## <a name="known-issues-in-update-4-from-previous-releases"></a>Kända problem i uppdatering 4 från tidigare versioner

Det finns inga nya kända problem i uppdatering 4. En lista över problem som överförts till uppdatering 4 från tidigare versioner finns i [viktig information om uppdatering 3](storsimple-update3-release-notes.md#known-issues-in-update-3).

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-4"></a>Serial-Attached SCSI (SAS) och uppdateringar av inbyggd program vara i uppdatering 4

Den här versionen har SAS-styrenheter och uppdateringar för inbyggd driv rutin och inbyggd program vara. Mer information om hur du installerar de här uppdateringarna finns i [installera uppdatering 4](./storsimple-8000-install-update-4.md) på din StorSimple-enhet.

## <a name="virtual-device-updates-in-update-4"></a>Uppdateringar av virtuella enheter i uppdatering 4

Den här uppdateringen kan inte tillämpas på StorSimple Cloud Appliance (kallas även den virtuella enheten). Nya virtuella enheter måste skapas. 

## <a name="next-step"></a>Nästa steg

Lär dig hur du [installerar uppdatering 4](./storsimple-8000-install-update-4.md) på din StorSimple-enhet.