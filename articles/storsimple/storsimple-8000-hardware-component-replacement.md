---
title: Utbyte av maskin varu komponenter i StorSimple 8000-serien | Microsoft Docs
description: Beskriver hur du på ett säkert sätt ersätter PCMs, batteri, Controller-moduler, EBOD-kontrollanter, disk enheter och chassin för en StorSimple-enhet.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/02/2017
ms.author: alkohli
ms.custom: ''
ms.openlocfilehash: 12ab5a9598cc0222f5a3e64985be2e2ea9e7e2fd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96014864"
---
# <a name="replace-a-hardware-component-on-your-storsimple-8000-series-device"></a>Ersätta en maskin varu komponent på din StorSimple 8000-serie enhet

## <a name="overview"></a>Översikt
Ersättnings självstudierna för maskin varu komponenter beskriver maskin varu komponenterna i din Microsoft Azure StorSimple 8000 serie enhet och de steg som krävs för att ta bort och ersätta dem. Den här artikeln beskriver säkerhets ikoner, ger pekare till de detaljerade självstudierna och visar en lista över de komponenter som är utbytbara.

> [!IMPORTANT]
> Innan du försöker ta bort eller ersätta någon StorSimple-komponent måste du kontrol lera att du går igenom [konventionerna för säkerhets ikoner](#safety-icon-conventions) och andra [säkerhets åtgärder](storsimple-8000-safety.md).

### <a name="safety-icon-conventions"></a>Säkerhets ikon konventioner

I följande tabell beskrivs de säkerhets ikoner som används i de här självstudierna. Var uppmärksam på dessa säkerhets ikoner när du går igenom stegen för att ta bort och ersätta enhets komponenter.

| Ikon | Text | Ytterligare information |
|:--- |:--- |:--- |
| ![Varningsikon](./media/storsimple-hardware-component-replacement/Warning.png) |**FARLIGA!** |Indikerar en farlig situation som, om de inte undviks, leder till dödsfall eller allvarlig skada. Det här signal ordet är begränsat till de mest extrema situationerna. |
| ![Varningsikon](./media/storsimple-hardware-component-replacement/Warning.png) |**HONOM!** |Indikerar en farlig situation som, om de inte undviks, kan leda till dödsfall eller allvarlig skada. |
| ![Varnings ikon](./media/storsimple-hardware-component-replacement/Caution.png) |**NOGA!** |Indikerar en farlig situation som, om de inte undviks, kan leda till mindre eller måttlig skada. |
| ![Meddelande ikon](./media/storsimple-hardware-component-replacement/NoticeIcon.png) |**INFORMATION** |Anger information som anses viktig, men inte risk relaterad. |
| ![Elektrisk stötande ikon](./media/storsimple-hardware-component-replacement/Electric.png) |**Risk för elektrisk stöt** |Indikerar hög spänning. |
| ![Ikon med tung vikt](./media/storsimple-hardware-component-replacement/Weight.png) |**Tung vikt** | |
| ![Ingen användar tjänst dels ikon](./media/storsimple-hardware-component-replacement/NoUserServiceableParts.png) |**Inga delar av användar tjänsten** |Få inte åtkomst om den inte är korrekt utbildad. |
| ![Ikonen Läs instruktioner](./media/storsimple-hardware-component-replacement/ReadInstructions.png) |**Läs alla instruktioner först** | |
| ![Varnings ikon för tips](./media/storsimple-hardware-component-replacement/TipHazard.png) |**Tips risk** | |

### <a name="before-you-begin"></a>Innan du börjar

Bekanta dig med säkerhets informationen om din enhet och dina säkerhets ikoner som används i den här självstudien. Gå till [säker installation och kör din StorSimple-enhet](storsimple-8000-safety.md) för fullständig information. Se till att granska [säkerhets varningarna](storsimple-8000-safety.md#handling-precautions) innan du hanterar din StorSimple-enhet.

Innan du försöker ersätta en komponent bör du tänka på följande information.

![Varnings ikon ](./media/storsimple-hardware-component-replacement/Warning.png) ![ varning om elektrisk stötande ikon ](./media/storsimple-hardware-component-replacement/Electric.png) **!**

* På egen hand genom att använda en statisk urladdning eller en antistatisk mat i hantering av moduler och komponenter i StorSimple-enheten.
* Vidrör inte någon krets. Använd de angivna referenserna och stöd linjerna när du hanterar komponenter som kan ha exponerade kretsar.

![Varnings ikon meddelande ](./media/storsimple-hardware-component-replacement/Warning.png) ![ ikon ](./media/storsimple-hardware-component-replacement/NoticeIcon.png) **meddelande:**

**Lämna aldrig ett tomt fack i höljets bak sida** när du ersätter en modul. Hämta en ersättnings-eller tom modul innan du tar bort problem delen.

## <a name="hardware-component-replacement-procedures"></a>Ersättnings procedurer för maskin varu komponenter

Din StorSimple 8000-serie enhet består av flera plugin-moduler i primär-och/eller EBOD-höljen. 8100 har en enda primär inne slutning, medan 8600 är en dubbel enhets enhet med en primär inne slutning och en EBOD hölje.

De viktigaste maskin varu komponenterna i enheten sammanfattas i följande tabeller. Klicka på länken i kolumnen **ersättnings procedur** för att gå till den associerade självstudien.

| Komponenter | Antal närvarande | Plugin-modul? | Ersättnings procedur |
|:--- |:--- |:--- |:--- |
| Chassi |1 |Inga |[Ersätt chassit på din StorSimple-enhet](storsimple-8000-chassis-replacement.md) |
| Primära kontrollanter |2 |Ja |[Ersätta en Controller-modul på din StorSimple-enhet](storsimple-8000-controller-replacement.md) |
| 764W Power and kylning-moduler (PCMs) |2 |Ja |[Ersätta en energi- och kylningsmodul på StorSimple-enheten](storsimple-8000-power-cooling-module-replacement.md) |
| Reserv batteri |2 |Ja |[Ersätta modulen för extrabatteriet på StorSimple-enheten](storsimple-8000-battery-replacement.md) |
| Diskenheter |12 |Ja |[Ersätta en disk enhet på din StorSimple-enhet](storsimple-8000-disk-drive-replacement.md) |

**Tabell 1** Maskin varu komponenter i den primära inne slutningen

Den primära inne slutningen och EBOD-kabinettet skiljer sig i sina I/O-moduler. Dessutom har PCMs olika watt. PCMs i den primära inne slutningen är 764 W, medan de i EBOD-kammaren är 580 W. PCMs i den primära inne slutningen innehåller också en modul för säkerhets kopierings batteri.

| Komponenter | Antal närvarande | Plugin-modul? | Ersättnings procedur |
|:--- |:--- |:--- |:--- |
| Chassi |1 |Inga |[Ersätt chassit på din StorSimple-enhet](storsimple-8000-chassis-replacement.md) |
| EBOD kontrollanter |2 |Ja |[Ersätta en EBOD-kontrollant på din StorSimple-enhet](storsimple-8000-ebod-controller-replacement.md) |
| 580W Power and kylning-moduler (PCMs) |2 |Ja |[Ersätta en energi- och kylningsmodul på StorSimple-enheten](storsimple-8000-power-cooling-module-replacement.md) |
| Diskenheter |12 |Ja |[Ersätta en disk enhet på din StorSimple-enhet](storsimple-8000-disk-drive-replacement.md) |

**Tabell 2** Maskin varu komponenter i EBOD-kabinettet

Plugin-modulerna på enheten är markerade i följande främre och bakre diagram. Du kan använda dessa diagram för att fastställa platsen för de olika plugin-modulerna om en ersättning krävs. Det främre diagrammet visar disk enheterna och de bakre diagrammen i EBOD-höljet och den primära inne slutningen visar plugin-modulerna.

![Enhetens front panel med disk enheter](./media/storsimple-hardware-component-replacement/IC741028.png)

**Bild 1** Enhetens fram sida

| Etikett | Beskrivning |
|:--- |:--- |
| 0 - 11 |Disk enheter (totalt 12) |

Både den primära inne slutningen och EBOD-kabinettet har enhets bärare. Chassit hus 12 3,5 "disk enheter ordnade i formatet 3 till 4.

![Bakplanering av enhetens primära inne slutnings moduler](./media/storsimple-hardware-component-replacement/IC740994.png)

**Bild 2** Tillbaka till den primära inne slutningen

| Etikett | Beskrivning |
|:--- |:--- |
| 1 |PCM 0 |
| 2 |PCM 1 |
| 3 |Kontrollant 0 |
| 4 |Kontrollant 1 |

![Bakplanering av enhetens EBOD hölje-moduler](./media/storsimple-hardware-component-replacement/IC769599.png)

**Bild 3** Bak sidan av EBOD-kabinettet

| Etikett | Beskrivning |
|:--- |:--- |
| 1 |PCM 0 |
| 2 |PCM 1 |
| 3 |EBOD Controller 0 |
| 4 |EBOD Controller 1 |

## <a name="field-replaceable-units"></a>Enheter som kan Replacements

Följande fält replacementable Units (FRUs) är tillgängliga för din StorSimple-enhet:

* Chassi (inklusive den integrerade drift panelen)
* 764 W AC-PCM
* 580 W AC-PCM
* Hård disk enhet med modul för enhets transport
* Controller-modul
* EBOD Controller-modul
* Modul för säkerhets kopierings batteri
* Rack montering järnvägs paket

[Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) om du vill beställa de här ersättnings enheterna.

## <a name="next-steps"></a>Nästa steg

Granska all [säkerhets information](storsimple-8000-safety.md) innan du försöker ersätta en StorSimple-maskin varu komponent.
