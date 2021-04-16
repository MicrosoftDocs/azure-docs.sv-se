---
title: Microsoft Azure StorSimple information om Virtual Array Update 1.3 | Microsoft Docs
description: Beskriver kritiska öppna problem och lösningar för Azure StorSimple Virtual Array som kör uppdatering 1.3.
ms.service: storsimple
author: v-dalc
ms.topic: article
ms.date: 04/13/2021
ms.author: alkohli
ms.openlocfilehash: 498e3d11d8188850a918c67a9a88643d15c134c5
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389527"
---
# <a name="storsimple-virtual-array-update-13-release-notes"></a>Versionsanteckningar för StorSimple Virtual Array Update 1.3

Följande viktiga information identifierar viktiga öppna problem och lösta problem för Microsoft Azure StorSimple Virtual Array-uppdateringar.

Den här informationen uppdateras kontinuerligt. Allteftersom lösningar på de allvarliga problemen upptäcks, läggs de till. Innan du distribuerar StorSimple Virtual Array bör du noga granska informationen i viktig information.

Uppdatering 1.3 motsvarar programvaruversion 10.0.10319.0.

> [!IMPORTANT]
> - Uppdatering 1.3 är en viktig uppdatering. Vi rekommenderar starkt att du installerar det så snart som möjligt.
> - Du kan bara installera Uppdatering 1.3 på enheter som kör Uppdatering 1.2.
> - Uppdateringar är störande och startar om enheten. Om I/O pågår medför enheten driftstopp. Detaljerade anvisningar om de paket som används för att tillämpa den här uppdateringen finns i [Ladda ned uppdatering 1.3.](#download-update-13)

## <a name="whats-new-in-update-13"></a>Vad är nytt i Uppdatering 1.3

Den här uppdateringen innehåller följande förbättringar:KB4540725

- Transport Layer Security (TLS) 1.2 är en obligatorisk uppdatering och måste installeras. Från och med den här versionen blir TLS 1.2 standardprotokollet för all Azure Portal kommunikation.
  
   Om du ser följande varning måste du uppdatera programvaran på enheten innan du fortsätter:

   En eller flera StorSimple-enheter kör en äldre programvaruversion. Den senaste tillgängliga uppdateringen för TLS 1.2 är en obligatorisk uppdatering och bör installeras omedelbart på dessa enheter. TLS 1.2 används för all Azure Portal-kommunikation och utan den här uppdateringen kan enheten inte kommunicera med StorSimple-tjänsten.

- Felkorrigeringar för skräpinsamling förbättrar prestandan för skräpinsamlingscykeln när enheten och lagringskontot finns i två avlägsna regioner.
- Korrigering för säkerhetskopieringsfel på grund av blob-timeouter.
- Uppdaterade säkerhetskorrigeringar för OS/.NET Framework:
  - [KB4540725:](https://support.microsoft.com/topic/servicing-stack-update-for-windows-8-1-rt-8-1-and-server-2012-r2-march-10-2020-cfa082a3-0b58-a8a3-7dc7-ab424de91b86)Mars 2020 SSU (Servicing Stack Update)
  - [KB4565541:](https://support.microsoft.com/topic/july-14-2020-kb4565541-monthly-rollup-fed6b2b1-3d23-5981-34df-9215a8d8ce01)samlad juli 2020
  - [KB4565622:](https://support.microsoft.com/topic/security-and-quality-rollup-for-net-framework-4-6-4-6-1-4-6-2-4-7-4-7-1-4-7-2-for-windows-8-1-rt-8-1-and-windows-server-2012-r2-kb4565622-b7320848-1889-a624-da01-719f55ee8a00)juli 2020 .NET Framework uppdatering

## <a name="download-update-13"></a>Ladda ned uppdatering 1.3

Om du vill ladda ned den här [uppdateringen går du Microsoft Update serverkatalogen](https://www.catalog.update.microsoft.com/Home.aspx) och laddar ned KB4575898-paketet. Det här paketet innehåller följande paket. Installera paketen i den här ordningen:

1. **KB4540725**, som innehåller kumulativa Windows-uppdateringar för 2012 R2 fram till mars 2020. Mer information om vad som ingår i den här sammanslagningen finns i Mars [månatliga samlade säkerhet.](https://support.microsoft.com/help/4540725)
1. **KB4565541**, som innehåller kumulativa Windows-uppdateringar för 2012 R2 fram till juli 2020. Mer information om vad som ingår i den här sammanslagningen finns i [Månadssäkerhetsuppslag för juli.](https://support.microsoft.com/help/4565541)
1. **KB4565622**, som innehåller cumulative.NET Framework-uppdateringar fram till juli 2020. Mer information om vad som ingår i den här sammanslagningen finns i [KB4565622.](https://support.microsoft.com/help/4565622)<!--The Help link opens the KB. I can't find a monthly rollup. I updated the link text to accurately describe what opens.-->
1. **KB3011067**, som innehåller programuppdateringar för enheter.

Ladda ned KB4575898 och följ dessa anvisningar för [att tillämpa uppdateringen via det lokala webbgränssnittet.](./storsimple-virtual-array-install-update-11.md#use-the-local-web-ui)

## <a name="known-issues-in-update-13"></a>Kända problem i Uppdatering 1.3
Inga nya problem har släppts i Uppdatering 1.3. Alla problem som anges i versionen förs över från tidigare versioner. Om du vill se en sammanfattning av kända problem som ingår i tidigare versioner går du [till Kända problem i Uppdatering 1.2.](./storsimple-virtual-array-update-12-release-notes.md#known-issues-in-update-12)

## <a name="next-steps"></a>Nästa steg
Ladda ned KB4575898 [och tillämpa uppdateringen via lokalt webbgränssnitt.](./storsimple-virtual-array-install-update-1.md#use-the-local-web-ui)

## <a name="references"></a>Referenser
Letar du efter en äldre version? Gå till:

- [StorSimple Virtual Array Update 1.2 Release Notes](./storsimple-virtual-array-update-12-release-notes.md)
- [StorSimple Virtual Array Update 1.0 Release Notes](./storsimple-virtual-array-update-1-release-notes.md)
- [StorSimple Virtual Array Update 0.6 Release Notes](./storsimple-virtual-array-update-06-release-notes.md)
- [Versionsanteckningar för StorSimple Virtual Array Update 0.5](./storsimple-virtual-array-update-05-release-notes.md)
- [Versionsanteckningar för StorSimple Virtual Array Update 0.4](./storsimple-virtual-array-update-04-release-notes.md)
- [Versionsanteckningar för StorSimple Virtual Array Update 0.3](./storsimple-ova-update-03-release-notes.md)
- [Versionsanteckningar för StorSimple Virtual Array Update 0.1 och 0.2](./storsimple-ova-update-01-release-notes.md)
- [Information om allmän tillgänglighet för StorSimple Virtual Array](./storsimple-virtual-array-update-06-release-notes.md)