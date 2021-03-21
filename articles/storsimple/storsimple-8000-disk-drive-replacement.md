---
title: Ersätta en disk enhet på en StorSimple 8000-serien het | Microsoft Docs
description: Förklarar hur du ersätter en disk enhet på ett StorSimple primärt hölje eller ett EBOD hölje.
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
ms.date: 8/25/2017
ms.author: alkohli
ms.openlocfilehash: a5a7a31c174271f963bb005f10b8376a59133d36
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96014881"
---
# <a name="replace-a-disk-drive-on-your-storsimple-8000-series-device"></a>Ersätta en diskenhet på en enhet i StorSimple 8000-serien

## <a name="overview"></a>Översikt
I den här självstudien beskrivs hur du kan ta bort och ersätta en hård disk som inte fungerar eller inte är trasig på en Microsoft Azure StorSimple enhet. Om du vill ersätta en disk enhet måste du:

* Intrångs låset
* Ta bort disk enheten
* Installera ersättnings disk enheten

> [!IMPORTANT]
> Innan du tar bort och ersätter en disk enhet bör du gå igenom säkerhets informationen i [StorSimple för maskin varu komponenter](storsimple-8000-hardware-component-replacement.md).
 

## <a name="disengage-the-antitamper-lock"></a>Intrångs låset
I den här proceduren förklaras hur du kan använda skydd mot manipulering av StorSimple-enheter när du ersätter disk enheterna. De olika skydds låsen är monterade i enhetens hanterings enheter och de nås via en liten bländare i spärr avsnittet i referensen. Enheter levereras med lås som är inställda på den låsta positionen.

#### <a name="to-unlock-the-antitamper-lock"></a>Så här låser du upp skyddet mot manipulering
1. Infoga noggrant tangenten lock (a "tamperproof" T10 Screwdriver som Microsoft tillhandahöll) i öppningen i handtaget och i dess sockel. 
   
   Om skydds låset är aktiverat visas den röda indikatorn i bländaren.
  
    ![Låst disk enhet](./media/storsimple-disk-drive-replacement/IC741056.png)
   
    **Bild 1** Skydd mot manipulering
   
   | Etikett | Beskrivning |
   |:--- |:--- |
   | 1 |Indikator öppning |
   | 2 |Skydd mot manipulering |
2. Rotera nyckeln i en moturs riktning tills den röda indikatorn inte visas i bländaren ovanför nyckeln.
3. Ta bort nyckeln.
   
    ![Olåst disk enhet](./media/storsimple-disk-drive-replacement/IC741057.png)
   
    **Bild 2** Olåst disk enhet
4. Nu kan disk enheten tas bort.

Följ stegen i omvänt för att engagera låset.

## <a name="remove-the-disk-drive"></a>Ta bort disk enheten
Din StorSimple-enhet har stöd för en RAID 10-liknande konfiguration av lagrings utrymmen. Detta innebär att den fungerar normalt med en felande disk, SSD (Solid-State Drive) eller hård disk (HDD).

> [!IMPORTANT]
> * Om systemet har fler än en havererad disk tar du inte bort fler än en SSD eller hård disk från systemet vid någon tidpunkt. Detta kan leda till förlust av data.
> * Se till att du placerar en ersättnings SSD på en plats som tidigare hade en SSD. På samma sätt kan du placera en ersättnings hård disk på en plats som tidigare hade en hård disk.
> * I Azure Portal är platser numrerade från 0 – 11. Om portalen visar att en disk i fack 2 har misslyckats, letar du efter den felande disken på den tredje platsen från det övre vänstra hörnet på enheten.
> 
> 

Enheter kan tas bort och ersättas medan systemet fungerar.

#### <a name="to-remove-a-drive"></a>Ta bort en enhet
1. Om du vill identifiera den felande disken går du till enhets **inställningarna > maskin varu hälso tillstånd** i Azure Portal. Eftersom en disk kan krascha i den primära inne slutningen och/eller i en EBOD-kabinett (om du använder en 8600-modell) tittar du på status för diskarna under **delade komponenter** och under **delade komponenter i EBOD**. En havererad disk i någon av de två kabinetten visas med röd status.
2. Leta upp enheterna överst i den primära inne slutningen eller EBOD-höljet. 
3. Om disken är olåst går du vidare till nästa steg. Om disken är låst kan du låsa upp den genom att följa anvisningarna i avsnittet om att stoppa skyddet mot [manipulering](#disengage-the-antitamper-lock).
4. Tryck på den svarta spärren på modulen enhets transport och hämta enhetens bärvåg och ta bort den från fronten på chassit.
   
    ![Frigör disk enhets referens](./media/storsimple-disk-drive-replacement/IC741051.png)
   
    **Bild 3** Frigör enhets referensen
5. När enhetens fraktande referens är helt utökad drar du ut enhetens bärvåg från chassit. 
   
    ![Glidande disk disk disk](./media/storsimple-disk-drive-replacement/IC741052.png)
   
    **Bild 4** Skjuta upp disk enheten från operatören

## <a name="install-the-replacement-disk-drive"></a>Installera ersättnings disk enheten
När en enhet har misslyckats i StorSimple-enheten och du har tagit bort den, så följ den här proceduren för att ersätta den med en ny enhet.

#### <a name="to-insert-a-drive"></a>Så här infogar du en enhet
1. Se till att enhetens bärvåg är helt utökad, vilket visas i följande bild.
   
    ![Disk enhet med Hantera utökad](./media/storsimple-disk-drive-replacement/IC741044.png)
   
    **Figur 5** Enhet med utökad referens
2. Dra enhets operatören hela vägen till chassit.
   
    ![Glidande disk till disk enhetens bärvåg](./media/storsimple-disk-drive-replacement/IC741045.png)
   
    **Bild 6**  Dra enhets transporten till chassit
3. När enhetens bärvåg har satts in stänger du enhetens frakt-handtag samtidigt som du fortsätter att push-överföra enhetens bärvåg till chassit tills enhetens frakt handtag fästs på en låst plats.
4. Använd lås nyckeln som tillhandahölls av Microsoft (tamperproof Torx Screwdriver) för att skydda bär Vågs handtaget på plats genom att sätta på Lås skruven ett kvartal moturs.
5. Kontrol lera att ersättningen lyckades och att enheten fungerar. Öppna Azure Portal och navigera till **enhets inställningar**  >  **maskin varu hälsa**. Under **delade komponenter** eller **EBOD delade komponenter** ska enhetens status vara grön, vilket indikerar att den är felfri.

   
   > [!NOTE]
   > Det kan ta flera timmar innan disk statusen blir grön efter ersättningen.
  
## <a name="next-steps"></a>Nästa steg
Läs mer om [StorSimple av maskin varu komponenter](storsimple-8000-hardware-component-replacement.md).

