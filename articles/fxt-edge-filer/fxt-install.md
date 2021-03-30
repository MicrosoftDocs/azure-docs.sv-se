---
title: 'Självstudie: installera en fysisk enhet för Azure FXT Edge-filer'
description: Packa upp, racka och kablar för den fysiska enhets komponenten i Microsoft Azure FXT Edge-filer hybrid Storage cache
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.openlocfilehash: 48ee6de28bcd76d4c484b77c981062bad1a3754d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92219759"
---
# <a name="tutorial-install-azure-fxt-edge-filer"></a>Självstudie: installera Azure FXT Edge-filer

I den här självstudien beskrivs hur du installerar en maskinvarubaserad nod för Azure FXT Edge-filer hybrid Storage cache. Du måste installera minst tre maskinvarukonfigurationer för att skapa ett Azure FXT Edge-kluster.

Installations proceduren omfattar att packa upp och racka montera enheten och koppla CMA (kabel Management arm) och front panel. En separat själv studie kurs beskriver hur du kopplar samman nätverks kablar och ansluter till strömförsörjningen.

Det tar cirka en timme att installera en Azure FXT Edge-nod.

I den här självstudien ingår följande installations steg:

> [!div class="checklist"]
>
> * Packa upp enheten
> * Montera enheten i ett rack
> * Installera Front Panel (valfritt)

## <a name="installation-prerequisites"></a>Installationskrav

Innan du börjar ska du kontrol lera att data Center och rack som du kommer att använda har följande funktioner:

* En tillgänglig 1U-plats på racket där du tänker montera enheten.
* AC-strömförsörjning och kylnings system som uppfyller behoven hos Azure FXT Edge-filer. (Läs [energi-och termiska specifikationer](fxt-specs.md#power-and-thermal-specifications) för hjälp med att planera och ändra storlek på installationen.)  

  > [!NOTE]
  > Om du vill dra full nytta av de två redundanta strömförsörjnings enheterna (PSUs) använder du enheter för Power distribution på två olika förgrenings kretsar när du kopplar nätström. Läs [Anslut ström kablar](fxt-network-power.md#connect-power-cables) för mer information.  

## <a name="unpack-the-hardware-node"></a>Packa upp noden för maskin vara

Varje Azure FXT Edge-nod skickas i en enda ruta. Slutför de här stegen för att packa upp en enhet.

1. Placera lådan på en plan, jämn yta.

2. Kontrollera lådan och förpackningsskummet för att se om det förekommer krosskador, skärningar, vattenskador eller andra uppenbara skador. Om lådan eller förpackningen är allvarligt skadad ska du inte öppna den. Kontakta Microsoft Support för att få hjälp att bedöma om enheten är i gott skick.

3. Packa upp lådan. Kontrol lera att den innehåller följande objekt:
   * En enda kabinett FXT-enhet
   * Två strömkablar
   * En front panel och en nyckel
   * En järn vägs pakets sammansättning
   * En kabel hanterings arm (CMA)
   * CMA installations anvisningar broschyr
   * Installations anvisningar för rack
   * Broschyr om säkerhet, miljö och reglerande information

Om du inte har fått alla objekt i listan kontaktar du enhets leverantören för support.

Kontrol lera att enheten har haft tillräckligt med tid för att uppnå samma temperatur som rummet innan du installerar den eller slår på den. Om du upptäcker att en del av enheten är kondenserad kan du vänta minst 24 timmar innan du installerar.

Nästa steg är att rackmontera enheten.

## <a name="rack-the-device"></a>Rackmontera enheten

Azure FXT Edge-enheten måste installeras på en standardiserad 19-tums rack.

Azure FXT Edge-filer hybrid Storage cache består av tre eller flera Azure FXT Edge-filer. Upprepa installations stegen för varje enhet som ingår i systemet.

### <a name="rack-install-prerequisites"></a>Krav för rack installation

* Innan du börjar läser du säkerhets anvisningarna i broschyren säkerhet, miljö och information om regler som levererades med din enhet.

  > [!NOTE]
  > Använd alltid två personer när du lyfter noden, inklusive när du installerar den i ett rack eller tar bort den från racket.

* Identifiera vilken typ av räl-installation som används med utrustnings racket.
  * För snap-in-och Round håls-näthanterings rack följer du anvisningarna för verktygs lösa räl.
  * För gängade håls ställningar följer du anvisningarna för de verktyg som visas.
  
    För konfigurationen av konfiguration av järnvägar måste du ange åtta skruvar, skriva 10-32, 12-24, M5 eller M6. Skruvens huvud diameter måste vara mindre än 10 mm (0,4 ").

### <a name="identify-the-rail-kit-contents"></a>Identifiera innehållet i järnvägs paketet

Leta upp komponenterna för installation av järnvägs kit-sammansättningen:

1. Två A7 Dell ReadyRails II glidande järnvägs-sammansättningar (1)
1. Två Hook-och loop-remmar (2)

![Numrerad ritning över järn vägs paket innehåll](media/fxt-install/identify-rail-kit-contents-400.png)

### <a name="rail-assembly---tool-less-rails-square-hole-or-round-hole-racks"></a>Järn vägs sammansättning-verktyg med mindre räler (håls hål eller runda hål)

Följ den här proceduren för att sätta samman och installera räler för rack med en kvadratisk eller runda hålen.

1. Placera den vänstra och högra delen av järnvägarna som är märkta **fram** och inåt. Placera varje slut punkt så att den placeras på hålen på fram sidan av de lodräta rackets fläns. 81.1

2. Justera varje slut punkt i det nedre och övre hål i racket i det utrymme du vill montera det. (2)

3. Engagera bak sidan av järnvägen tills den är helt placerade på den lodräta rack flänsen och att Lås klickar på plats. Upprepa de här stegen för att placera och placera platsen för fram sidan på den lodräta rackets fläns. 3

> [!TIP]
> Ta bort räler genom att hämta knappen Lås version i slut punktens mitt punkt (4) och ta bort den till varje järnväg.

![Diagram över installation och borttagning av verktygs lösa spår med steg numrerad lista](media/fxt-install/installing-removing-tool-less-rails-400.png)

### <a name="rail-assembly---tooled-rails-threaded-hole-racks"></a>Spårbundna verktyg för räl (trådade håls ställningar)

För rack med gängade hål följer du stegen nedan för att sätta samman och installera räler.

1. Ta bort PIN-märkena från front-och bakre monterings hakparenteser med en flat-lutad Screwdriver. 81.1
1. Ta bort och rotera del sammansättningarna för järnvägs lås för att ta bort dem från monterings hakparenteserna. (2)
1. Koppla de vänstra och högra monterings skenorna till de främre lodräta racken med två par skruvar. 3
1. Dra tillbaka vänster och höger hak paren tes framåt mot de bakre lodräta rack flänsarna och fäst dem med två par skruvar. 4

![Diagram över installation och borttagning av verktygsbaserade spår med steg numrerad](media/fxt-install/installing-removing-tooled-rails-400.png)

### <a name="install-the-system-in-the-rack"></a>Installera systemet i racket

Följ de här stegen för att montera Azure FXT Edge-enheten i racket.

1. Hämta de inre bildskärmarna från racket tills de låser sig. 81.1
1. Hitta den bakre järnvägs Standoff på varje sida av enheten och sänk dem till bak sidan av bild sammansättningarna. (2)
1. Rotera enheten nedåt tills alla räl-standoffs har placerats på J-facken. 3
1. Push-enheten inåt tills lås spaken klickar på plats.
1. Tryck på knapparna för att frigöra lås på båda spåren (4) och skjut enheten i racket.

![Installera system i ett rack diagram med steg numrerad](media/fxt-install/installing-system-rack-400.png)

### <a name="remove-the-system-from-the-rack"></a>Ta bort systemet från racket

Följ den här proceduren om du vill ta bort enheten från racket.

1. Leta upp Lås spaken på sidorna av de inre räler (1).
2. Lås upp varje spaken genom att rotera den upp till dess versions position (2).
3. Grepp sidorna i systemet ordentligt och hämta det framåt tills järnvägs standoffs är längst fram i J-facken. Lyft upp och bort systemet från racket och placera det på en nivå yta (3).

![Bild som visar hur du tar bort ett system från racket, med steg numrerad](media/fxt-install/removing-system-rack-400.png)

### <a name="engage-the-slam-latch"></a>Engagera slam-spärren

1. Placera slam-spärren (1) på någon av systemets sida.
2. Spärren sker automatiskt när systemet är i racket.

Om du vill frigöra spärren när du tar bort systemet kan du hämta dem (2).

Tillvals skruvar för hård montering är till för att skydda systemet till racket för leverans eller i andra instabila miljöer. Hitta skruven under varje lås och dra dem till ett #2 Phillips Screwdriver (3).

![En numrerad illustration av att engagera och släppa slam-låset](media/fxt-install/engaging-releasing-slam-latch-400.png)

### <a name="install-the-cable-management-arm"></a>Installera kabel hanterings arm

En valfri kabel hanterings arm (CMA) finns i FXT Edge-filer. De utskrivna instruktionerna för att installera det finns i paketet.

1. Packa upp och identifiera komponenterna i kabel hanterings arm-paketet:
   * CMA-fack (1)
   * CMA (2)
   * I nylon-kabeln binder (3)
   * CMA-hakparenteser (4)
   * Status indikator kabel (5)

   > [!TIP]
   > Om du vill skydda CMA för försändelse i racket kan du göra det i racket runt båda korgarna och facket och lätt dem ordentligt. Att skydda CMA på det här sättet skyddar också systemet i instabila miljöer.

   ![Bild av CMA-delar](media/fxt-install/cma-kit-400.png)

2. Installera CMA-facket.

   CMA-facket ger stöd och fungerar som en kvarhållare för CMA.

   1. Justera och placera varje sida av facket med mottagarens hakparenteser på spårens inre kanter.
   1. Push-överför facket tills det klickar på plats. 81.1
   1. Om du vill ta bort facket, kan du överföra spärr knapparna mot mitten och dra ut facket från mottagar hakparenteserna (2).

   ![Bild av CMA Tray-installation](media/fxt-install/cma-tray-install-400.png)

3. Installera CMA-hakparenteserna för bilagor.

   > [!NOTE]
   >
   > * Du kan koppla CMA till antingen höger eller vänster monterings järnväg, beroende på hur du tänker skicka kablar från systemet.
   > * För enkelhetens skull kan du montera CMA på sidan mittemot strömförsörjningen (sida A). Om den är monterad på sidan B måste CMA vara frånkopplad för att den yttre strömförsörjningen ska kunna tas bort.
   > * Ta alltid bort facket innan du tar bort ström försörjningen.

   ![Bild av CMA Haking-installation](media/fxt-install/cma-bracket-l-r-install-400.png)

   1. Välj lämplig CMA för den sida där du vill montera CMA (sida B eller sida A).
   1. Installera CMA-textklammern med motsvarande sida A-eller B B-markering längst bak i bild spåret.
   1. Justera hålen på hakparentesen med stiften på bild spåret. Placera parentesen tills den låser sig.

4. Installera CMA.

   1. På bak sidan av systemet anpassar du spärren på klient delen av CMA på den innersta hakparentesen för bild sammansättningen tills spärren (1) är klar.
   1. Anpassa det andra spärret i slutet av den yttre hakparentesen tills spärren (2) är klar.
   1. Om du vill ta bort CMA tar du bort båda låsen genom att trycka på CMA-release-knapparna överst i de inre och yttre låsen (3).

   ![Bild av Main CMA-installation](media/fxt-install/cma-install-400.png)

   CMA kan roteras bort från systemet för åtkomst och tjänst. Lyft CMA bort från facket i gångjärns änden (1). När det är inplacerat från facket, så sväng CMA bort från systemet (2).

   ![Bild av CMA roterad öppen för tjänsten](media/fxt-install/cma-swing-over-tray-400.png)

## <a name="install-the-front-bezel-optional"></a>Installera Front Panel (valfritt)

I det här avsnittet beskrivs hur du installerar och tar bort Front Panel (Faceplate) för maskin varan för Azure FXT Edge-filer.

Så här installerar du Front Panel:

1. Leta upp och ta bort panel nyckeln, som finns i panel paketet.
1. Justera ramen mot chassits fram sida och infoga stiften på höger sida av ramen i hålen på nodens högra rack monterings fläns.
1. Anpassa den vänstra änden av ramen till chassit. Tryck på ramen tills knappen till vänster hamnar på plats.
1. Lås ramen med nyckeln.

Ta bort Front Panel:

1. Lås upp ramen med hjälp av panel nyckeln.
1. Tryck på knappen släpp på vänster sida och dra bort den vänstra änden av ramen från chassit.
1. Koppla bort den högra änden och ta bort ramen.

   ![Bild som visar knappen släpp till vänster på ramen och hur du tar bort den genom att dra utåt från vänster sida](media/fxt-install/remove-bezel-edited-600.png)

## <a name="next-steps"></a>Nästa steg

När du har packat upp och beställt enheten fortsätter du installationen genom att ansluta nätverks kablar och ansluta AC till Azure FXT Edge-filer.

> [!div class="nextstepaction"]
> [Kabelanslut nätverks portarna och tillhandahålla strömförsörjning](fxt-network-power.md)
