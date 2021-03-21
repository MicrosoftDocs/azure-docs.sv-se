---
title: Byt ut batteri på Microsoft Azure StorSimple 8000-serien het
description: Beskriver hur du tar bort, ersätter och underhåller modulen för säkerhets kopierings batteri på din StorSimple-enhet.
author: alkohli
ms.service: storsimple
ms.topic: troubleshooting
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 17a6a07d117557cdee23634bd211a14cf90d49e3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96023779"
---
# <a name="replace-the-backup-battery-module-on-your-storsimple-device"></a>Ersätta modulen för extrabatteriet på StorSimple-enheten

## <a name="overview"></a>Översikt
Den primära inne slutnings-och kylnings modulen (PCM) på din Microsoft Azure StorSimple-enhet har ytterligare ett batteri paket. Det här paketet ger energi så att StorSimple-enheten kan spara data om det går att koppla från växel ström till den primära inne slutningen. Det här batteri paketet kallas *modulen backup Battery*. Modulen för säkerhets kopierings batteri finns bara för den primära inne slutningen på din StorSimple-enhet (EBOD-kabinettet innehåller ingen modul för säkerhets kopierings batteri).

I den här självstudien beskrivs hur du:

* Ta bort modulen för säkerhets kopierings batteri
* Installera en ny modul för säkerhets kopierings batteri
* Underhålla modulen för säkerhets kopierings batteri

> [!IMPORTANT]
> Innan du tar bort och ersätter en modul för säkerhets kopierings batteri bör du gå igenom säkerhets informationen i [introduktionen till StorSimple-maskin varu komponent](storsimple-8000-hardware-component-replacement.md).


## <a name="remove-the-backup-battery-module"></a>Ta bort modulen för säkerhets kopierings batteri
Modulen backup Battery för StorSimple-enheten är en enhet som kan bytas ut på ett fält. Innan den installeras i PCM bör modulen batteri lagras i sin ursprungliga förpackning. Utför följande steg för att ta bort säkerhets kopierings batteriet.

#### <a name="to-remove-the-backup-battery-module"></a>Ta bort modulen för säkerhets kopierings batteri
1. Gå till bladet StorSimple Enhetshanteraren service i Azure Portal. Gå till **enheter** och välj sedan enheten i listan med enheter. Gå till **övervaka**  >  **maskin varu hälsa**. Under **delade komponenter** tittar du på batteriets status.
2. Identifiera den PCM i vilken batteriet har misslyckats. Bild 1 visar bak sidan av StorSimple-enheten.
   
    ![Bakplanering av enhetens primära inne slutnings moduler](./media/storsimple-battery-replacement/IC740994.png)
   
    **Bild 1** Tillbaka till primär enhet som visar PCM-och Controller-moduler
   
   | Etikett | Beskrivning |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Kontrollant 0 |
   | 4 |Kontrollant 1 |
   
    Som du ser i siffran 3 i figur 2, ledde övervaknings indikatorn på PCM 0 som motsvarar **batteri fel** att vara tänd.
   
    ![Bakplanering av enhetens PCM för att övervaka indikatorer](./media/storsimple-battery-replacement/IC740992.png)
   
    **Bild 2** Bak sidan av PCM som visar övervaknings indikator lamporna
   
   | Etikett | Beskrivning |
   |:--- |:--- |
   | 1 |AC-strömavbrott |
   | 2 |Fläkt haveri |
   | 3 |Batteri fel |
   | 4 |PCM OK |
   | 5 |LIKSTRÖMs strömavbrott |
   | 6 |Batteriet är felfritt |
3. Om du vill ta bort PCM med ett havererat batteri följer du stegen i [ta bort en PCM](storsimple-8000-power-cooling-module-replacement.md#remove-a-pcm).
4. Med PCM borttagen, lyft och rotera modulen för batteri hantering uppåt som visas i följande figur och hämta den för att ta bort batteriet.
   
    ![Ta bort batteri från PCM](./media/storsimple-battery-replacement/IC741019.png)
   
    **Bild 3** Ta bort batteriet från PCM
5. Placera modulen i enhets förpackningen för fält som kan bytas ut.
6. Returnera den felaktiga enheten till Microsoft för korrekt service och hantering.

## <a name="install-a-new-backup-battery-module"></a>Installera en ny modul för säkerhets kopierings batteri
Utför följande steg för att installera modulen för ersättnings batteri i PCM i den primära kabinett filen för din StorSimple-enhet.

#### <a name="to-install-the-battery-module"></a>Så här installerar du modulen Battery
1. Placera modulen backup Battery i rätt orientering i PCM.
2. Tryck på batteri-modulen för att hantera anslutningen.
3. Ersätt PCM i den primära inne slutningen genom att följa rikt linjerna i [Ersätt en Power-och kylning-modul på din StorSimple-enhet](storsimple-8000-power-cooling-module-replacement.md).
4. När ersättningen är klar går du till enheten och går sedan till **övervaka**  >  **maskin varu hälsa** i Azure Portal. Kontrol lera batteriets status för att se till att installationen lyckades. En grön status indikerar att batteriet är felfritt.

## <a name="maintain-the-backup-battery-module"></a>Underhålla modulen för säkerhets kopierings batteri
I din StorSimple-enhet ger modulen säkerhets kopierings batteri kraft till kontrollanten under en strömavbrott händelse. Det gör att StorSimple-enheten kan spara viktiga data innan de stängs av på ett kontrollerat sätt. Med två helt fulladdade batterier i PCMs kan systemet hantera två löpande förlust händelser.

I Azure Portal visar **maskin varu hälsan** under **övervaknings** bladet om batteriet fungerar dåligt eller om livs längden närmar sig. Batteri status anges med **batteri i PCM 0** eller **batteri i PCM 1** under **delade komponenter**. Det här bladet visar ett **DEgraderat** tillstånd för livs cykeln och **kunde inte** slutdatumet uppnås.

> [!NOTE]
> Batteriet kan rapportera **fel** när det bara behöver debiteras.


Om **DEgraderat** tillstånd visas rekommenderar vi följande åtgärd:

* Systemet kan ha fått en senaste ström förlust eller också kan batterierna genomgå periodiskt underhåll. Observera systemet i 12 timmar innan du fortsätter.
  
  * Om statusen fortfarande **försämras** efter 12 timmars kontinuerlig anslutning till AC-kraften med de styrenheter och PCMS som körs, måste batteriet bytas ut. [Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) om du vill ha en ersättnings-modul för säkerhets kopiering.
  * Om statusen blir OK efter 12 timmar fungerar batteriet och behöver bara en underhålls avgift.
* Om det inte finns någon kopplad förlust av växel ström och PCM är påslaget och anslutet till strömförsörjning måste batteriet bytas ut. [Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) för att beställa en ersättnings-modul för säkerhets kopiering.

> [!IMPORTANT]
> Ta bort det havererade batteriet enligt nationella och regionala bestämmelser.

## <a name="next-steps"></a>Nästa steg
Läs mer om [StorSimple av maskin varu komponenter](storsimple-8000-hardware-component-replacement.md).

