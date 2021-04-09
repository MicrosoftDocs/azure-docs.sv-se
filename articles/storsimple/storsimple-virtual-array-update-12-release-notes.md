---
title: Microsoft Azure StorSimple versions anmärkningar för Virtual Array-uppdatering 1,2 | Microsoft Docs
description: Beskriver kritiska öppna problem och lösningar för den virtuella StorSimple-matrisen som kör uppdatering 1,2.
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 05/29/2019
ms.author: alkohli
ms.openlocfilehash: 1118dfcec67a49365f1f6e5e522e98b97694d052
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94960212"
---
# <a name="storsimple-virtual-array-update-12-release-notes"></a>StorSimple Virtual Array Update 1,2 viktig information

I följande versions information identifieras kritiska öppna problem och lösta problem för Microsoft Azure StorSimple virtuell mat ris uppdatering.

Viktig information uppdateras kontinuerligt. Allteftersom lösningar på de allvarliga problemen upptäcks, läggs de till. Läs igenom informationen i viktig information innan du distribuerar din virtuella StorSimple-matris.

Uppdatering 1,2 motsvarar program varu versionen **10.0.10311.0**.

> [!IMPORTANT]
> - Uppdateringar är störande och startar om enheten. Om I/O pågår förorsakar enheten avbrott. Detaljerade anvisningar om paket som används för att tillämpa den här uppdateringen finns i [Hämta uppdatering 1,2](#download-update-12).
> - Uppdatering 1,2 är bara tillgänglig via Azure Portal om enheten kör uppdatering 1,0 eller 1,1.

## <a name="whats-new-in-update-12"></a>Nyheter i uppdatering 1,2

Den här uppdateringen innehåller följande fel korrigeringar:

- Förbättrad återhämtning vid bearbetning av borttagna filer.
- Förbättrade hanterings undantag i kodens Start Sök väg som leder till minskad fel vid säkerhets kopiering, återställning, moln läsning och automatiserat utrymmes återtagande.

## <a name="download-update-12"></a>Hämta uppdatering 1,2

Om du vill hämta uppdateringen går du till [Microsoft Update katalog](https://www.catalog.update.microsoft.com/Home.aspx) Server och laddar ned KB4502035-paketet. Det här paketet innehåller följande paket:

 - **KB4493446** som innehåller kumulativa Windows-uppdateringar för 2012 R2 upp till april 2019. För mer information om vad som ingår i den här samlade uppdateringen, gå till [månatlig säkerhets](https://support.microsoft.com/help/4493446/windows-8-1-update-kb4493446)insamling i april.
 - **KB3011067** , som är en Microsoft Update fristående paket fil WINDOWSTH-KB3011067-x64. Den här filen används för att uppdatera enhetens program vara.

Hämta KB4502035 och följ de här anvisningarna för att [tillämpa uppdateringen via det lokala webb gränssnittet](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui).

## <a name="issues-fixed-in-update-12"></a>Problem som korrigeras i uppdatering 1,2

Följande tabell innehåller en sammanfattning av problem som korrigeras i den här versionen.

| Nej. | Funktion | Problem |
| --- | --- | --- |
| 1 |Borttagning| I tidigare versioner av program varan uppstod ett problem när användningen av enheten inte ändrades även när filerna togs bort. Det här problemet har åtgärd ATS i den här versionen. Nivå kod Sök vägen gjorde mer elastisk vid bearbetning av borttagna filer.|
| 2 |Undantagshantering| I tidigare versioner av program varan uppstod ett problem efter omstarten av systemet som kan leda till fel vid säkerhets kopiering, återställning, läsning från molnet och automatiserat utrymmes återtagning. Den här versionen innehåller ändringar för hur undantagen hanterades i Start Sök vägen.|

## <a name="known-issues-in-update-12"></a>Kända problem i uppdatering 1,2

Inga nya problem har frisläppts – anges i uppdatering 1,2. Alla problem med versions informationen överförs från tidigare versioner. Om du vill se en översikt över kända problem som ingår i tidigare versioner går du till [kända problem i uppdatering 1,1](storsimple-virtual-array-update-11-release-notes.md#known-issues-in-update-11).

## <a name="next-steps"></a>Nästa steg

Hämta KB4502035 och [Använd uppdateringen via det lokala webb gränssnittet](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui).

## <a name="references"></a>Referenser

Letar du efter en äldre versions anteckning? Gå till:
* [StorSimple Virtual Array Update 1,1 viktig information](storsimple-virtual-array-update-11-release-notes.md)
* [StorSimple Virtual Array Update 1,0 viktig information](storsimple-virtual-array-update-1-release-notes.md)
* [StorSimple Virtual Array Update 0,6 viktig information](storsimple-virtual-array-update-06-release-notes.md)
* [StorSimple Virtual Array Update 0,5 viktig information](storsimple-virtual-array-update-05-release-notes.md)
* [StorSimple Virtual Array Update 0,4 viktig information](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Virtual Array Update 0,3 viktig information](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array Update 0,1 och 0,2 viktig information](storsimple-ova-update-01-release-notes.md)
* [Viktig information om allmän tillgänglighet för StorSimple Virtual Array](./storsimple-virtual-array-update-06-release-notes.md)