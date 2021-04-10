---
title: Säkerhets guide för Azure Stack Edge Pro R | Microsoft Docs
description: Beskriver säkerhets konventioner, rikt linjer, överväganden och förklarar hur du på ett säkert sätt installerar och använder din Azure Stack Edge Pro R-enhet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: alkohli
ms.openlocfilehash: dacc9ecc28ffa482b60d1e48735fe3620b5b7558
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100363074"
---
# <a name="azure-stack-edge-pro-r-safety-instructions"></a>Instruktioner för Azure Stack Edge Pro R-säkerhet

![Varnings ikon läsa ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ säkerhets meddelande ikon ](./media/azure-stack-edge-pro-r-safety/icon-safety-read-all-instructions.png)
 **läsa säkerhet och hälso information**

Läs all säkerhets information i den här artikeln innan du använder din Azure Stack Edge Pro R-enhet. Om du inte följer anvisningarna kan det leda till brand, elektrisk stöt, skador eller skada på dina egenskaper. Läs all säkerhets information nedan innan du använder Azure Stack Edge Pro R.

## <a name="safety-icon-conventions"></a>Säkerhets ikon konventioner

Följande signal ord för varnings signaler är:

| Ikon | Beskrivning |
|:--- |:--- |
| ![Varnings symbol](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)| **Risk:** Indikerar en farlig situation som, om de inte undviks, leder till dödsfall eller allvarlig skada. <br> **Varning:** Indikerar en farlig situation som, om de inte undviks, kan leda till dödsfall eller allvarlig skada. <br> **Varning:** Indikerar en farlig situation som, om de inte undviks, kan leda till mindre eller måttlig skada.|
|

Följande farlighets ikoner ska observeras när du konfigurerar och kör din Azure Stack Edge Pro R Edge-enhet:

| Ikon | Beskrivning |
|:--- |:--- |
| ![Läs alla instruktioner först](./media/azure-stack-edge-pro-r-safety/icon-safety-read-all-instructions.png) | Läs alla instruktioner först |
| ![Meddelande Ikons ](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **meddelande:** | Anger information som anses viktig, men inte risk relaterad. || ![Varnings symbol](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) | Varnings symbol |
| ![Varnings ikon för tips](./media/azure-stack-edge-pro-r-safety/icon-safety-tip-hazard.png)  | Tips risk|
| ![Ikon med tung vikt](./media/azure-stack-edge-pro-r-safety/icon-safety-heavy-weight.png)  | Risk med tung vikt|
| ![Elektrisk stötande ikon](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) | Fara för elektrisk stöt |
| ![Ingen användar tjänst dels ikon](./media/azure-stack-edge-pro-r-safety/icon-safety-do-not-access.png) | Inga delar av användar tjänsten. Få inte åtkomst om den inte är korrekt utbildad. |
| ![Ikon för flera ström källor](./media/azure-stack-edge-pro-r-safety/icon-safety-disconnect-all-power.png)  | Flera ström källor. Koppla bort alla ström sladdar för att ta bort all ström från utrustningen. |
| ![Ikon för att dra ihop punkter](./media/azure-stack-edge-pro-r-safety/icon-pinching-points.png)  | Det finns andra omdrana punkter. |
| ![Ikoner för aktiva komponenter eller ytor](./media/azure-stack-edge-pro-r-safety/icon-hot-component-surface.png)  | Anger aktiva komponenter eller ytor. |
|

## <a name="handling-precautions-and-site-selection"></a>Hantera förebyggande åtgärder och val av webbplatser

![Varnings Ikons ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) **Varning:**

* Lämplig utrustning (t. ex. ett uttag för lastpall) och personlig skyddsutrustning (personal skyddsutrustning), till exempel, måste användas för att flytta och hantera enheten som levereras.

![Varnings ikon ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)![ tips ](./media/azure-stack-edge-pro-r-safety/icon-safety-tip-hazard.png) **Varning:**

* Placera utrustningen på en platt, hårt och stabil yta för att undvika en potentiell spets eller explosions risk.
* Stacka inte fler än två enheter på varandra.
* Se till att systemet är säkert innan du stackar.
* Flytta inte upp eller flytta staplade enheter.
* Stacka inte enheter som är kraftfulla med externa kablar.
* Se till att ström sladdarna inte krossas eller skadas under stackning.
* Enheter ska inte användas som tabeller eller arbets ytor.

![Varnings ikon ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ elektrisk stöt ikon utan varnings ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png)
 ![ ikon för användar tjänstens delar ](./media/azure-stack-edge-pro-r-safety/icon-safety-do-not-access.png) 

* Kontrol lera att enheten *tas emot* för skador. Om enhets kabinettet är skadat kan du [kontakta Microsoft Support](azure-stack-edge-contact-microsoft-support.md) för att få en ny ersättning. Försök inte att använda enheten.
* Om du misstänker att enheten inte fungerar kan du [kontakta Microsoft Support](azure-stack-edge-contact-microsoft-support.md) för att få en ny ersättning. Försök inte att betjäna enheten.
* Enheten innehåller inga delar som är baserade på användar tjänster. Farliga spännings-, ström-och energi nivåer finns i. Öppna inte. Returnera enheten till Microsoft för underhåll.

![Varnings ikon varnings ikon med ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![ tung vikt ](./media/azure-stack-edge-pro-r-safety/icon-safety-heavy-weight.png) **:**

* Om du tar bort Power Supply-modulen i UPS exponeras kraftfulla-delar i UPS-enheten. Infoga inte externa objekt inuti utrymmet för Power Supply-modulen.
* Försök inte att lyfta upp en Azure Stack Edge Pro R Edge-enhet. En inne slutning kan väga upp till mellan 52 kg och 93 kg (115 kg och 205 kg). Använd mekanisk hjälp eller annan lämplig hjälp vid flytt och lyft av utrustning. Följa lokala hälso krav och säkerhets krav när du flyttar och lyfter utrustning.
* Försök inte att lyfta utrustningen utan lämpligt mekaniskt stöd. Tänk på att försök att lyfta upp den här vikten kan orsaka allvarliga skador.

![Varnings Ikons ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) **Varning:**  

* Systemet är utformat för att köras i en kontrollerad miljö. Välj en plats som är:
  * Väl ventilerat och bort från värme källor, inklusive direkt solljus och radiatorer.
  * Inte exponerad för fukt eller regn.
  * Finns på ett utrymme som minimerar vibrationer och fysisk stöt.  Systemet är utformat för stötande och vibrationer enligt MIL-STD-810G.
  * Isolerade från starka elektro magnetiska fält producerade av elektriska enheter.
  * Medföljer med korrekt jordade uttag.
  * Tillhandahålls tillräckligt med utrymme för att få åtkomst till strömförsörjnings kablarna, eftersom de fungerar som produktens huvudsakliga strömförsörjnings anslutning.
* Ethernet-kablar medföljer inte produkten. För att minska elektro magnetisk interferens rekommenderar vi att Cat 6 skärmade STP-kablar (skärmade tvinnad hop parkabel) används.
* Konfigurera utrustningen i ett arbets utrymme som möjliggör tillräcklig luft cirkulation runt utrustningen. Se till att fram-och bak sidan tas bort helt medan enheten körs.
* Ethernet-kablar medföljer inte produkten. För att minska elektro magnetisk interferens rekommenderar vi att Cat 6 skärmade kablar (STP) används.
* Installera utrustningen i temperatur-kontrollerat utrymme utan att utföra främmande ämnen och tillåta tillräcklig luft cirkulation runt utrustningen.
* Hålla utrustningen borta från källor till flytande och överdriven fukt miljö.
* Tillåt inte eventuell vätska eller främmande objekt att ange systemet. Placera inte drycker eller andra flytande behållare på eller nära systemet.

## <a name="heater-precautions"></a>Värme förebyggande åtgärder

![Varnings ikon frekventa ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![ komponenter eller ytor ikon 1 ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **Varning:** 

* Automatisk värme åtgärd medan systemet är påslaget kan skapa en berörings risk på grund av hög yttemperatur på värme paketets hölje. Vidrör inte den här ytan när systemet är påslaget. Tillåt en låg frekvent period på 10 minuter efter att systemet är avstängt.

![Varnings ikon för att dra av ikonen ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![ punkter ikon 1 ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **Varning:** 

* När systemet är påslaget kan den automatiska ansättningen av bakre plenum-dörren skapa en risk för att skapa en kopplings punkt. Fortsätt att ta bort det här avsnittet när systemet är påslaget.

## <a name="electrical-precautions"></a>Elektriska försiktighets åtgärder

![Varnings ikon varnings ikon för ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![ elektrisk stöt vid fel ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **:**

* Tillhandahålla en säker elektrisk jordnings anslutning till strömförsörjnings sladden. Den växlade ström sladden (AC) har ett uttag av tre ledare (en plugg som har en PIN-kod för bas). Den här kontakten passar bara ett jordat växel uttag. Manipulation inte syftet med fäst punkten.
* Med tanke på att kontakten på strömförsörjnings sladden är den primära från kopplings enheten, se till att socket-uttagen är placerade nära enheten och är lätt att komma åt.
* Koppla från ström sladdarna (s) (genom att hämta kontakten, inte kabeln) och koppla från alla kablar om något av följande villkor föreligger:

  * Ström sladden eller pluggen blir sliten eller skadas på annat sätt.
  * Du spiller något i enhetens hölje.
  * Enheten exponeras för regn eller överskotts fukt.
  * Enheten släpptes och enhetens Skift läge är skadat.
  * Du misstänker att enheten behöver tjänst eller reparation.
* Koppla bort enheten permanent innan du flyttar den eller om du tror att den har skadats på något sätt.
* För att förhindra hög läckage är det viktigt att när ett enda överförings fall har mer än en oavbruten ström källa (UPS), rekommenderas att varje UPS är ansluten till en oberoende gren. I händelse av att en PDU-enhet (Power Distribution Unit) eller annan enhet används där varje UPS är beroende av en enda matnings ledare i PDU: n, måste jord uttaget på den yttre av varje UPS också användas med en kompletterande byggnads bas ledare.

  > [!NOTE]
  > Om en PDU används som redan har en kompletterande grund ledare, krävs ingen ytterligare jord-Terminal i UPS-enheten.

* Tillhandahålla en lämplig ström källa med skydd mot elektrisk överbelastning för att uppfylla följande energi krav:

  * Spänning: 100 till 240 volt AC
  * Nuvarande: 20 A, högsta per ström sladd. Det finns en eller flera ström kablar.
  * Frekvens: 50 till 60 Hz

![Varnings ikon ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ elektrisk stöt ikon med ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png)
 ![ flera ström källor ](./media/azure-stack-edge-pro-r-safety/icon-safety-disconnect-all-power.png) **Varning:**

* För system utan en oavbruten strömförsörjning (UPS) avkopplar du alla AC-kablar för att helt ta bort AC-strömmen från utrustningen.
* För system med UPS kopplar du från alla AC-sladdar och använder UPS-Förstärk för att ta bort systemet. UPS innehåller farliga nät och DC-spänningar.
* Om ett system innehåller en UPS, tillhandahölls UPS-enheten med en skärmad ström kabel för indata. Du måste använda skärm kabeln för skärmade inströms läge, Ersätt eller ändra inte kabeln.

![Varnings ikon varnings ikon för ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ elektrisk stöt vid fel ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **:**

* För system som är utrustade med UPS, innebär AC och/eller DC-spänning alltid en potentiell risk för LIKSTRÖMs spänning vid UPS-utdata som genereras från antingen batterier eller verktyg. För att undvika maskin skada eller personlig skada förutsätter vi alltid att det kan finnas en spänning vid UPS-utdata, även om den är frånkopplad från den primära ström källan.
* För system som är utrustade med UPS är alla UPS-kraftfulla externa anslutningar hondjur. Ta inte bort ärendet eller infoga något i dessa eller några kopplingar på UPS-enheten.

![Varnings ikon varnings ikon för ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ elektrisk stöt vid fel ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **:**

* Försök inte att ändra eller Använd andra AC-kablar än de som medföljer utrustningen.

![Varnings ikon det går inte att varnings ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ ikonen för användar tjänst delar ](./media/azure-stack-edge-pro-r-safety/icon-safety-do-not-access.png) **:**

* Den här utrustningen innehåller litium-och/eller litium järniga fosfat batterier. Försök inte att underhålla utrustningen. Batterier i denna utrustning är inte användar tjänst. Explosions risk om batteriet ersätts av en felaktig typ.
* Om du tar bort modulen för UPS exponeras kraftfulla-delar i UPS-enheten. Infoga inte externa objekt i batteriets modul-fack.

## <a name="regulatory-information"></a>Reglerande information

Det här avsnittet innehåller information om Azure Stack Edge Pro R-enhet, regel modell nummer: Azure Stack Edge Pro R.

Den Azure Stack Edge Pro R Edge-enheten är utformad för användning med NRTL (UL, CSA, ETL osv.) och IEC/EN 60950-1-eller IEC/EN 62368-1-kompatibel (CE-märkta) informations tekniks utrustning.

Enheten är utformad för att användas i följande miljöer:

| Miljö | Specifikationer |
|:--- |:--- |
|Temperatur specifikationer | <ul><li>Lagrings temperatur: – 33 &deg; C – 63 &deg; C (– 28 &deg; f-145 &deg; F) </li><li>Kontinuerlig åtgärd: 5 &deg; c – 43 &deg; C (41 &deg; F – 110 &deg; f)</li><li>Maximal temperatur toning (drift och lagring): 20 &deg; C/h (68 &deg; F/h)</li></ul> |
|Specifikationer för relativ fuktighet | <ul><li>Lagring: 5% till 95% RH med 33 &deg; C (91 &deg; F) maximal dagg punkt. Omgivningen måste vara icke-kondenserande hela tiden.</li><li>Drift: 5% till 85% relativ fuktighet med 29 &deg; C (84,2 &deg; F) maximal dagg punkt</li></ul> |
| Maximala höjd specifikationer | <ul><li>Drift (utan UPS): 15 000 ft (4 572 meter)</li><li>Drift (med UPS): 10 000 ft (3 048 meter)</li><li>Lagring: 40 000 ft (12 192 meter)</li></ul> |

<!--|Standard operating temperature specifications | <ul>Continuous operation (for altitude less than 950 m or 3117 ft): +5&deg;C–45&deg;C (41&deg;F–113&deg;F) with no direct sunlight on the equipment</ui>
|Expanded operating temperature specifications | <ul><li>Continuous operation: 5&deg;C to 45&deg;C at 5% to 85% RH with 29&deg;C dew point.</li><li></= 1% of annual operating hours: –5&deg;C to 55&deg;C at 5% to 90% RH with 29&deg;C dew point. |
| Expanded operating temperature restrictions | <ul><li>Do not perform cold start below -15&deg;C according to IEC 60945.</li><li>The operating temperature specified is for a maximum altitude of 950 meters.</li></ul> |
| Gaseous contamination specifications | <ul><li>Copper coupon corrosion rate: < 300 &Aring;/month per Class G1 as defined by ANSI/ISA71.04-1985.</li><li>Silver coupon corrosion rate: < 200 &Aring;/month as defined by AHSRAE TC9.9.</li></ul>|

> [!NOTE]
> Outside the standard operating temperature (10&deg;C to 35&deg;C):
> - The system can operate continuously in temperatures as low as 5&deg;C and as high as 45&deg;C. For temperatures between 35&deg;C and 45&deg;C, de-rate maximum allowable temperature by 1&deg;C per 175 m above 950 m (1&deg;F per 319 ft).
> - The system can operate down to –5&deg;C or up to 55&deg;C for a maximum of 1% of its annual operating hours. For temperatures between 45&deg;C and 55&deg;C, de-rate maximum allowable temperature by 1&deg;C per 125 m above 950 m (1&deg;F per 228 ft).
>
> When operating in the expanded temperature range:
> - System performance may be impacted.
> - Ambient temperature warnings may be reported in the System Event Log.     
>
> Maximum corrosive contaminant levels measured at &lt;/= 50% relative humidity. 


A device that has a UPS installed is designed to operate in the following environments:

| Environment | Specifications |
|:---  |:--- |
|Temperature specifications | <ul><li>Storage temperature: –40&deg;C–70&deg;C (–40&deg;F-158&deg;F) per Mil-Std 810G Method 501.5, Proc 1</li><li>Continuous operation (for altitude less than 950 m or 3117 ft): 5&deg;C–45&deg;C (41&deg;F–113&deg;F), with no direct sunlight on the equipment</li><li>Excursion temperature: 50&deg;C per Mil-Std 810G</li><li>Maximum temperature gradient (operating and storage): 20&deg;C/h (68&deg;F/h)</li></ul> |
|Relative humidity specifications | <ul><li>Storage: 5% to 95% RH with 33&deg;C (91&deg;F) maximum dew point. Atmosphere must be non-condensing at all times.</li><li>Operating: 5% to 85% relative humidity with 29&deg;C (84.2&deg;F) maximum dew point</li></ul>|
|Maximum altitude specifications | <ul><li>Storage: Mil-Std 810G method 500.5, Proc. I, 40,000 ft for 1 hour after stabilization </li><li>Operating: Mil-Std 810G method 500.5, Proc. II, air carriage, 15,000 ft for 1 hour after stabilization</li></ul>|
|Standard operating temperature specifications |<ul><li>Continuous operation (for altitude less than 950 m or 3117 ft)</li><li>+5&deg;C–45&deg;C (41&deg;F–113&deg;F) with no direct sunlight on the equipment</li></ul>|
|Expanded operating temperature specifications | <ul><li>Continuous operation: 5&deg;C to 45&deg;C at 5% to 85% RH with 29&deg;C dew point.</li><li>&lt;/= 1% of annual operating hours: 0&deg;C to 50&deg;C at 5% to 90% RH with 29&deg;C dew point.</li></ul>|
|Expanded operating temperature restrictions | <ul><li>Do not perform cold start below -15C Per IEC 60945</li><li>The operating temperature specified is for a maximum altitude of 950 meters</li></ul> |
|Gaseous contamination specifications | <ul><li>Copper coupon corrosion rate: < 300 &Aring;/month per Class G1 as defined by ANSI/ISA71.04-1985.</li><li>Silver coupon corrosion rate: < 200 &Aring;/month as defined by AHSRAE TC9.9.|-->

<!--
> [!NOTE]
> Outside the standard operating temperature (10&deg;C to 35&deg;C):
> - The system can operate continuously in temperatures as low as 5&deg;C and as high as 45&deg;C. For temperatures between 35&deg;C and 45&deg;C, de-rate maximum allowable temperature by 1&deg;C per 175 m above 950 m (1&deg;F per 319 ft).
> - The system can operate down to 0&deg;C or up to 50&deg;C for a maximum of 1% of its annual operating hours. For temperatures between 45&deg;C and 55&deg;C, de-rate maximum allowable temperature by 1&deg;C per 125 m above 950 m (1&deg;F per 228 ft).
> 
> When operating in the expanded temperature range:
> - System performance may be impacted.
> - Ambient temperature warnings may be reported in the System Event Log.
>
> Maximum corrosive contaminant levels measured at &lt;/= 50% relative humidity. --> 


> ![Meddelande ikon – 2 ](./media/azure-stack-edge-pro-r-safety/icon-safety-notice.png) **meddelande:** &nbsp; ändringar eller ändringar som gjorts i utrustning som inte uttryckligen godkänns av Microsoft kan säga till att användarens myndighet kan använda utrustningen.

#### <a name="canada-and-usa"></a>Kanada och USA:

> ![Meddelande ikon – 2 ](./media/azure-stack-edge-pro-r-safety/icon-safety-notice.png) **meddelande:** &nbsp; denna utrustning har testats och visat sig uppfylla gränserna för en klass med en digital enhet, i enlighet med del 15 i FCC-reglerna. Dessa gränser är utformade för att ge ett rimligt skydd mot skadlig störning när utrustningen drivs i en kommersiell miljö. Den här utrustningen genererar, använder och kan stråla radio frekvens energi och, om de inte är installerade och används i enlighet med instruktions hand boken, kan orsaka skadlig störning av radio kommunikation. Driften av den här utrustningen i ett bostads område kan förmodligen orsaka skadlig störning, vilket innebär att användaren måste korrigera störningen vid egna utgifter.

Den här enheten uppfyller kraven i del 15 i FCC-reglerna och bransch licens licenser – undantagna RSS-standarder (s). Åtgärden omfattas av följande två villkor: (1) den här enheten kan inte orsaka skadlig störning och (2) den här enheten måste acceptera eventuella störningar som tas emot, inklusive störningar som kan orsaka oönskad åtgärd på enheten.

![Regel informations varning 1](./media/azure-stack-edge-mini-r-safety/regulatory-information-1.png)


KAN ICES-3 (A)/NMB-3 (A) Microsoft Corporation, One Microsoft Way, Redmond, WA 98052, USA USA: (800) 426-9400 Kanada: (800) 933-4750

#### <a name="european-union"></a>EUROPEISKA UNIONEN:

Begära en kopia av EU: s försäkran om överensstämmelse. Skicka ett e-postmeddelande till [CSI_Compliance@microsoft.com](mailto:CSI_Compliance@microsoft.com).

![Varnings ikon ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) **Varning!**

Detta är en produkt i en klass. I en inhemsk miljö kan den här produkten orsaka radio störningar, vilket innebär att användaren kan behöva vidta lämpliga åtgärder.

Omhändertagande av avfalls batterier och elektriska och elektroniska produkter:

![Varnings ikon 14](./media/azure-stack-edge-mini-r-safety/icon-ewaste-disposal.png)

Den här symbolen på produkten eller dess batterier eller dess förpackning innebär att den här produkten och de batterier som den innehåller inte får tas bort med ditt hushålls avfall. I stället är det ditt ansvar att gå över till en tillämplig samlings plats för att återvinna batterier och elektrisk och elektronisk utrustning. Denna separata samling och återvinning bidrar till att spara naturresurser och förhindra negativa följder för människors hälsa och miljön på grund av möjlig förekomst av farliga ämnen i batterier och elektrisk och elektronisk utrustning, vilket kan orsakas av olämpligt omhändertagande. Om du vill ha mer information om var du än använder batterier och elektriska och elektroniska avgaser kontaktar du din lokala stad/kommun kontor, din hushålls tjänst för avfalls hantering eller handla där du köpte produkten. Kontakta erecycle@microsoft.com om du vill ha mer information om WEEE.

Den här produkten innehåller mynt batteri (er).

Microsoft Ireland Sandyford IND Est Dublin D18 KX32 IRL telefonnumret: + 353 1 295 3826 Fax nummer: + 353 1 706 4110


## <a name="next-steps"></a>Nästa steg

- [Förbered för att distribuera Azure Stack Edge Pro R Edge](azure-stack-edge-pro-r-deploy-prep.md)
