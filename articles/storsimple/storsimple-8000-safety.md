---
title: Installera & att köra StorSimple 8000-serien het
description: Beskriver säkerhets konventioner, rikt linjer och överväganden och förklarar hur du kan installera och använda din StorSimple-enhet på ett säkert sätt.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/04/2017
ms.author: alkohli
ms.openlocfilehash: 3adbd292d42fd6fd2a0e149e103c17968a6f084b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94950762"
---
# <a name="safely-install-and-operate-your-storsimple-device"></a>Installera och använda din StorSimple-enhet på ett säkert sätt
![Varnings ikon läsa ](./media/storsimple-safety/IC740879.png)
 ![ säkerhets meddelande ikon ](./media/storsimple-safety/IC740885.png) **läsa säkerhet och hälso information**

Läs all information om säkerhet och hälsa i den här artikeln som gäller för din Microsoft Azure StorSimple-enhet. Se till att alla utskrivna guider levereras med din StorSimple-enhet för framtida bruk. Om du inte följer anvisningarna och har konfigurerat, använder och bryr sig om den här produkten kan du öka risken för allvarlig skada eller dödsfall eller skada enheten eller enheterna. Det finns även en [nedladdnings bar version av den här guiden](https://www.microsoft.com/download/details.aspx?id=44233) .

## <a name="safety-icon-conventions"></a>Säkerhets ikon konventioner
Här följer de ikoner som du hittar när du granskar de säkerhets åtgärder som ska observeras när du konfigurerar och kör din Microsoft Azure StorSimple-enhet.

| Ikon | Beskrivning |
|:--- |:--- |
| ![Varnings ikon ](./media/storsimple-safety/IC740879.png) **!** |Indikerar en farlig situation som, om de inte undviks, leder till dödsfall eller allvarlig skada. Den här signalen är begränsad till de mest extrema situationerna. |
| ![Varnings ikon ](./media/storsimple-safety/IC740879.png) **Varning!** |Indikerar en farlig situation som, om de inte undviks, kan leda till dödsfall eller allvarlig skada. |
| ![Varnings ikon varning ](./media/storsimple-safety/IC740879.png) **!** |Indikerar en farlig situation som, om de inte undviks, kan leda till mindre eller måttlig skada. |
| ![Meddelande Ikons ](./media/storsimple-safety/IC740881.png) **meddelande:** |Anger information som anses viktig, men inte risk relaterad. |
| ![Elektrisk stötande stöt vid ](./media/storsimple-safety/IC740882.png) **fara** |Hög spänning |
| ![](./media/storsimple-safety/IC740883.png)  Extrafet vikt ikon | |
| ![Ingen User serviceable parts-ikon ](./media/storsimple-safety/IC740879.png) **saknar användar tjänst delar** |Få inte åtkomst om den inte är korrekt utbildad. |
| ![Läs säkerhets meddelande ikonen](./media/storsimple-safety/IC740885.png)**Läs alla instruktioner först** | |
| ![Tips risk ikon ](./media/storsimple-safety/IC740886.png) **tips** | |

## <a name="handling-precautions"></a>Hantering av försiktighets åtgärder
![Varnings ikon ](./media/storsimple-safety/IC740879.png) ![ Varning vid hög vikts ikon ](./media/storsimple-safety/IC740883.png) **!** 

För att minska risken för skada:

* En fullständigt konfigurerad hölje kan väga upp till 32 kg (70 kg). Försök inte att lyfta upp den själv.
* Innan du flyttar kammaren måste du alltid se till att två personer är tillgängliga för att hantera vikten. Tänk på att en person som försöker lyfta upp den här vikten kan upprätthålla skador.
* Lyft inte upp höljet av handtagen på Power and kylning-modulerna (PCMs) som finns längst bak i enheten. Dessa är inte utformade för att ta vikt.

## <a name="connection-precautions"></a>Anslutnings förebyggande åtgärder
![Varnings ikon ](./media/storsimple-safety/IC740879.png) ![ varning om elektrisk stötande ikon ](./media/storsimple-safety/IC740882.png) **!**

Minska sannolikheten för skada, elektrisk stötande eller dödsfall:

* När du drivs av flera AC-källor kopplar du från all försörjnings kraft för fullständig isolering.
* Koppla bort enheten permanent innan du flyttar den eller om du tror att den har skadats på något sätt.
* Tillhandahålla en säker elektrisk jordnings anslutning till strömförsörjnings kablarna. Kontrol lera att höljet uppfyller de nationella och lokala kraven innan du använder energi.
* Se till att ström anslutningen alltid kopplas från innan du tar bort en PCM från kabinett filen.
* Med tanke på att kontakten på strömförsörjnings sladden är den primära från kopplings enheten, se till att socket-uttagen är placerade nära utrustningen och är lätt att komma åt.

![Varnings ikon ](./media/storsimple-safety/IC740879.png) ![ varning om elektrisk stötande ikon ](./media/storsimple-safety/IC740882.png) **!**

För att minska sannolikheten för överhettning eller brand påverkan från de elektriska anslutningarna:

* Tillhandahålla en lämplig ström källa med skydd mot elektrisk överbelastning för att uppfylla kraven som beskrivs i den tekniska specifikationen.
* Använd inte bifurcated-elkablar ("Y"-leads).
* För att uppfylla tillämpliga krav på säkerhet, utsläpp och värme måste inga försättsblad tas bort och alla fack måste fyllas i med plugin-moduler eller enhets tomma.
* Se till att utrustningen används på det sätt som anges av tillverkaren. Om denna utrustning används på ett sätt som inte anges av tillverkaren, kan det skydd som tillhandahålls av utrustningen försämras.

![Meddelande Ikons ](./media/storsimple-safety/IC740881.png) **meddelande:**

För att din utrustning ska fungera korrekt och för att förhindra produkt skada:

* RJ45-portarna på bak sidan av enheten är bara för en Ethernet-anslutning. De får inte vara anslutna till ett telekommunikations nätverk.
* Se till att installera enheten i ett rack som kan hantera en kyl-till-bak-design.
* Alla plugin-moduler och tomma plåtar ingår i system omslutningen. De får bara tas bort när en ersättning omedelbart kan läggas till. Systemet får inte köras utan att alla moduler eller blank steg är på plats.

## <a name="rack-system-precautions"></a>Försiktighets åtgärder för rack system
Följande säkerhets krav måste beaktas när du monterar enheten i ett rack skåp.

![Varnings ikon ](./media/storsimple-safety/IC740879.png) ![ tips ](./media/storsimple-safety/IC740886.png) **Varning!**

För att minska sannolikheten för skada från ett tips över:

* Rack designen bör ha stöd för den totala vikten av de installerade omslutningarna och bör omfatta stabiliserings funktioner som är lämpliga för att förhindra att racket lutar eller skickas över under installationen eller normal användning.
* När du läser in ett rack fyller du i racket från nedre kanten uppåt och tomt från toppen.
* Dra inte mer än en inne slutning i racket i taget för att undvika risken för rack Toppling över.

![Varnings ikon ](./media/storsimple-safety/IC740879.png) ![ varning om elektrisk stötande ikon ](./media/storsimple-safety/IC740882.png) **!**

Minska sannolikheten för skada, elektrisk stötande eller dödsfall:

* Racket ska ha ett säkert El distributions system. Det måste tillhandahålla överdrivet skydd för inne slutningen och får inte överbelastas med det totala antalet installerade bilagor. Den elektriska energi förbruknings klassificering som visas på nameplate bör observeras.
* Det elektriska distributions systemet måste tillhandahålla ett tillförlitligt underlag för varje hölje i racket.
* Designen av det elektriska distributions systemet måste ta hänsyn till det totala mark läckage som är aktuellt från alla strömförsörjningar i alla höljen. Observera att varje ström försörjning i varje hölje har ett mark läckage som är aktuellt 1,0 mA högsta vid 60 Hz, 264 volt. Racket kan kräva etiketter med "hög läckage aktuellt. Mark anslutning (Earth) är nödvändig innan du ansluter en försörjning. "
* Racket som konfigureras med höljen måste uppfylla säkerhets kraven för UL 60950-1 och IEC 60950-1/EN 60950-1.

![Meddelande Ikons ](./media/storsimple-safety/IC740881.png) **meddelande:**

För rätt kylning av rack systemet:

* Se till att rack designen tar hänsyn till den högsta inne slutnings omgivnings temperaturen på 35 grader Celsius (95 grader Fahrenheit). Behåll rummet där rack systemet är coolt och kontrol lera att det finns tillräckligt med luft flöde från nätventilationen i data centret.
* Systemet drivs med låg belastning, uppfödnings installation (mottryck som skapats av rack dörrar och hinder som inte överstiger 5 Pascal [0,5 mm vatten mätare]).

## <a name="power-cooling-module-pcm-precautions"></a>Försiktighets åtgärder för Power kylning module (PCM)

Enheten är utformad för att användas med två PCMs. Varje PCMs har en strömförsörjning och en fläkt med dubbla axlar. Vid ett kritiskt tillstånd tillåter systemet ett fel på en strömförsörjning medan du fortsätter med normala åtgärder. Två PCMs-enheter (och därmed strömförsörjning) måste alltid vara installerade. En enskild PCM ger inte redundant strömförsörjning. Det innebär att det inte ens en PCM kan leda till avbrott eller möjliga data förluster.

![Varnings ikon ](./media/storsimple-safety/IC740879.png) ![ varning om elektrisk stötande ikon ](./media/storsimple-safety/IC740882.png) **!**

Minska sannolikheten för skada, elektrisk stötande eller dödsfall:

* Ta inte bort höljen från PCM. Det finns en risk för elektrisk stöt i. [Kontakta Microsoft Support](./storsimple-8000-contact-microsoft-support.md)om du vill returnera PCM och få en ersättning.

![Meddelande Ikons ](./media/storsimple-safety/IC740881.png) **meddelande:**

För att din utrustning ska fungera korrekt och för att förhindra produkt skada:

* Du måste ersätta det misslyckade PCM inom 24 timmar. När en PCM har tagits bort för ersättning måste ersättningen slutföras inom 10 minuter efter borttagningen.
* Ta inte bort en PCM om inte en ersättning kan installeras omedelbart. Kabinett filen får inte köras utan alla moduler på plats.

## <a name="electrostatic-discharge-esd-precautions"></a>Skydd mot Elektro statisk urladdning (ESD)

![Meddelande Ikons ](./media/storsimple-safety/IC740881.png) **meddelande:**

Observera följande ESD-relaterade försiktighets åtgärder.

* Kontrol lera att du har installerat och kontrollerat en lämplig antistatisk hand leds-eller Ankle-band.
* Observera alla konventionella ESDa försiktighets åtgärder vid hantering av moduler och komponenter.
* Undvik kontakt med att disponera komponenter och modul kopplingar.
* ESD-skador omfattas inte av garanti.

## <a name="battery-disposal-precautions"></a>Säkerhets åtgärder för batteri kasse ring

Strömförsörjningen använder ett särskilt batteri för att skydda innehållet i minnet under tillfälliga, kortsiktiga drift avbrott. Batteriet är isatt i PCM. Tänk på följande information om batteriet.

![Varnings ikon ](./media/storsimple-safety/IC740879.png) **Varning!**

För att minska risken för kort, brand, explosion, skada eller dödsfall:

* Avyttring av begagnade batterier i enlighet med nationella/regionala bestämmelser.
* Använd inte disassemblering, krossa eller hetta över 60 grader Celsius (140 grader Fahrenheit) eller förbränning. Ersätt PCM-batteriet med endast ett batteri. Användningen av ett annat batteri kan utgöra en risk för brand eller explosion.
* Använd skyddande sluts ändar på batterierna om de tas bort från strömförsörjningen.

![Meddelande Ikons ](./media/storsimple-safety/IC740881.png) **meddelande:**

När batterierna transporteras eller på annat sätt transporteras till luft följer du rikt linjerna för IATA litium batteri som finns på [https://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx](https://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx)

När du har granskat dessa säkerhets meddelanden, är nästa steg att packa upp, racka och kabelansluta enheten.

## <a name="next-steps"></a>Nästa steg

* För en 8100-enhet går du till [installera din StorSimple 8100-enhet](storsimple-8100-hardware-installation.md).
* För en 8600-enhet går du till [installera din StorSimple 8600-enhet](storsimple-8600-hardware-installation.md).