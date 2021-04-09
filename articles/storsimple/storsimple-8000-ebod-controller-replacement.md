---
title: Ersätta en StorSimple 8600 EBOD-styrenhet | Microsoft Docs
description: Förklarar hur du tar bort och ersätter en eller båda EBOD-styrenheter på en StorSimple 8600-enhet.
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
ms.openlocfilehash: bdafb4288bf818928aba43d09f15d4dd75092b24
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96014847"
---
# <a name="replace-an-ebod-controller-on-your-storsimple-device"></a>Ersätta en EBOD-kontrollant på din StorSimple-enhet

## <a name="overview"></a>Översikt
I den här självstudien beskrivs hur du ersätter en trasig EBOD-kontrollant på din Microsoft Azure StorSimple-enhet. Om du vill ersätta en EBOD Controller-modul måste du:

* Ta bort den felande EBOD-kontrollanten
* Installera en ny EBOD-kontrollant

Överväg följande information innan du börjar:

* Tomma EBOD-moduler måste infogas i alla oanvända platser. Kabinett filen kommer inte att kylas korrekt om en plats lämnas öppen.
* EBOD-styrenheten kan växlas och kan tas bort eller ersättas. Ta inte bort en misslyckad modul förrän du har ersatt. När du startar ersättnings processen måste du avsluta den inom 10 minuter.

> [!IMPORTANT]
> Innan du försöker ta bort eller ersätta någon StorSimple-komponent måste du kontrol lera att du går igenom [konventionerna för säkerhets ikoner](storsimple-8000-safety.md#safety-icon-conventions) och andra [säkerhets åtgärder](storsimple-8000-safety.md).

## <a name="remove-an-ebod-controller"></a>Ta bort en EBOD-kontrollant
Innan du ersätter modulen EBOD i StorSimple-enheten kontrollerar du att den andra EBOD Controller-modulen är aktiv och körs. I följande procedur och tabell förklaras hur du tar bort modulen EBOD Controller.

#### <a name="to-remove-an-ebod-module"></a>Ta bort en EBOD-modul
1. Öppna Azure-portalen.
2. Gå till din enhet och gå till **Inställningar**  >  **maskin varu hälsa** och kontrol lera att status för lysdioden för modulen Active EBOD Controller är grön och att indikatorn för modulen för EBOD Controller är röd.
3. Leta reda på modulen för den felande EBOD på bak sidan av enheten.
4. Ta bort de kablar som ansluter modulen EBOD Controller till kontrollanten innan du tar EBOD-modulen ur systemet.
5. Anteckna den exakta SAS-porten för EBOD Controller-modulen som var ansluten till kontrollanten. Du kommer att behöva återställa systemet till den här konfigurationen när du har bytt ut EBOD-modulen.
   
   > [!NOTE]
   > Detta är vanligt vis port A, som är märkt som **värd i** följande diagram.
   
    ![Bakplanering av EBOD-kontrollanten](./media/storsimple-ebod-controller-replacement/IC741049.png)
   
     **Bild 1** Tillbaka till EBOD-modulen
   
   | Etikett | Beskrivning |
   |:--- |:--- |
   | 1 |Indikator för fel |
   | 2 |Ström indikator |
   | 3 |SAS-anslutningar |
   | 4 |SAS-indikatorer |
   | 5 |Seriella portar för fabriks användning |
   | 6 |Port A (värd i) |
   | 7 |Port B (utgående värd) |
   | 8 |Port C (endast fabriks användning) |

## <a name="install-a-new-ebod-controller"></a>Installera en ny EBOD-kontrollant
I följande procedur och tabell förklaras hur du installerar en EBOD Controller-modul i din StorSimple-enhet.

#### <a name="to-install-an-ebod-controller"></a>Så här installerar du en EBOD-kontrollant
1. Kontrol lera EBOD-enheten för skada, särskilt gränssnitts anslutningen. Installera inte den nya EBOD-kontrollanten om några stift är böjda.
2. Med lås i den öppna positionen drar du modulen till inne slutningen tills spärren är i bruk.
   
    ![Installerar EBOD-kontrollant](./media/storsimple-ebod-controller-replacement/IC741050.png)
   
    **Bild 2**  Installera modulen EBOD Controller
3. Stäng spärren. Du bör höra ett klick när spärren är engagerande.
   
    ![Släpper EBOD-spärren](./media/storsimple-ebod-controller-replacement/IC741047.png)
   
    **Bild 3**  Stänger EBOD-modulens spärr
4. Återanslut kablarna. Använd den exakta konfigurationen som fanns före ersättningen. Se följande diagram och tabell för information om hur du ansluter kablarna.
   
    ![Kabelanslut din 4U-enhet för strömförsörjning](./media/storsimple-ebod-controller-replacement/IC770723.png)
   
    **Bild 4**. Återansluter kablar
   
   | Etikett | Beskrivning |
   |:--- |:--- |
   | 1 |Primärt kabinett |
   | 2 |PCM 0 |
   | 3 |PCM 1 |
   | 4 |Kontrollant 0 |
   | 5 |Kontrollant 1 |
   | 6 |EBOD Controller 0 |
   | 7 |EBOD Controller 1 |
   | 8 |EBOD-hölje |
   | 9 |Enheter för energi distribution |

## <a name="next-steps"></a>Nästa steg
Läs mer om [StorSimple av maskin varu komponenter](storsimple-8000-hardware-component-replacement.md).

