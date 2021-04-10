---
title: Säkerhets guide för Azure Stack Edge Mini R | Microsoft Docs
description: Beskriver säkerhets konventioner, rikt linjer, överväganden och förklarar hur du på ett säkert sätt installerar och använder din Azure Stack Edge Mini R-enhet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: alkohli
ms.openlocfilehash: eb42a9a77927d8577dfec3c9167294eb8f809fec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100382640"
---
# <a name="azure-stack-edge-mini-r-safety-instructions"></a>Säkerhets anvisningar för Azure Stack Edge Mini R

![Varnings ikon 1 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ Läs säkerhets meddelande ikonen ](./media/azure-stack-edge-mini-r-safety/icon-safety-read-all-instructions.png)
 **Läs säkerhet och hälso information**

Läs all säkerhets information i den här artikeln innan du använder din Azure Stack Edge Mini R-enhet, en sammansättning av ett batteri paket, en AC/DC-ansluten strömförsörjning, en modul för strömadapter och en server-modul. Om du inte följer anvisningarna kan det leda till brand, elektrisk stöt, skador eller skada på dina egenskaper. Läs all säkerhets information nedan innan du använder Azure Stack Edge Mini R.

## <a name="safety-icon-conventions"></a>Säkerhets ikon konventioner

Följande signal ord för varnings signaler är:

| Ikon | Beskrivning |
|:--- |:--- |
| ![Varnings symbol](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)| **Risk:** Indikerar en farlig situation som, om de inte undviks, leder till dödsfall eller allvarlig skada. <br> **Varning:** Indikerar en farlig situation som, om de inte undviks, kan leda till dödsfall eller allvarlig skada. <br> **Varning:** Indikerar en farlig situation som, om de inte undviks, kan leda till mindre eller måttlig skada.|
|

Följande farlighets ikoner ska observeras när du konfigurerar och kör din Azure Stack Edge Mini R-enhet:

| Ikon | Beskrivning |
|:--- |:--- |
| ![Läs alla instruktioner först](./media/azure-stack-edge-mini-r-safety/icon-safety-read-all-instructions.png) | Läs alla instruktioner först |
| ![Meddelande Ikons ](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **meddelande:** | Anger information som anses viktig, men inte risk relaterad. |
| ![Varnings symbol](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) | Varnings symbol |
| ![Elektrisk stötande ikon](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) | Fara för elektrisk stöt |
| ![Endast i bruk](./media/azure-stack-edge-mini-r-safety/icon-safety-indoor-use-only.png) | Endast i bruk |
| ![Ingen användar tjänst dels ikon](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) | Inga delar av användar tjänsten. Få inte åtkomst om den inte är korrekt utbildad. |
|

## <a name="handling-precautions-and-site-selection"></a>Hantera förebyggande åtgärder och val av webbplatser

Azure Stack Edge Mini R-enheten har följande hanterings villkor och urvalskriterier för webbplatser:

![Varnings ikon 2 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ elstöts ikon det går ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png)
 ![ inte att använda en ikon för användarens tjänst delar ](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) **Varning:**

* Kontrol lera att enheten *tas emot* för skador. Om enhets kabinettet är skadat kan du [kontakta Microsoft Support](azure-stack-edge-placeholder.md) för att få en ny ersättning. Försök inte att använda enheten.
* Om du misstänker att enheten inte fungerar kan du [kontakta Microsoft Support](azure-stack-edge-placeholder.md) för att få en ny ersättning. Försök inte att betjäna enheten.
* Enheten innehåller inga delar som är baserade på användar tjänster. Farliga spännings-, ström-och energi nivåer finns i. Öppna inte. Returnera enheten till Microsoft för underhåll.

![Varnings ikon 3 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **Varning:**

Vi rekommenderar att du använder systemet:

* Bort från värme källor, inklusive direkt solljus och radiatorer.
* I platser som inte exponeras för fukt eller regn.
* Finns på ett utrymme som minimerar vibrationer och fysisk stöt.  Systemet är utformat för stötande och vibrationer enligt MIL-STD-810G.
* Isolerade från starka elektro magnetiska fält producerade av elektriska enheter.
* Tillåt inte eventuell vätska eller främmande objekt att ange systemet. Placera inte drycker eller andra flytande behållare på eller nära systemet.

![Varnings ikon 4 Det går ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ inte att varnings ikonen för användar tjänst delar ](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) **:**

* Den här utrustningen innehåller ett litium batteri. Försök inte att underhålla batteri paketet. Batterier i denna utrustning är inte användar tjänst. Explosions risk om batteriet ersätts av en felaktig typ.

![Varnings ikon 5 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **Varning:**

Debitera bara batteri paketet när det är en del av den Azure Stack Edge-Mini R-enheten, debitera inte som en separat enhet.

![Varnings ikon 6 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **Varning:**

* PÅ/av-växeln i batteri paketet är för att ladda ned batteriet till Server-modulen. Om strömadaptern är ansluten till batteri paketet skickas ström till-Server-modulen även om växeln är på plats.

![Varnings ikon 7 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **Varning:**

* Skriv inte över eller korts enkrets till batteri paketet. Den måste återvinnas eller kasseras korrekt.

![Varnings ikon 8 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **Varning:**

* I stället för att använda den tillhandahållna AC/DC-strömförsörjningen har det här systemet även möjlighet att använda ett fält som har typen 2590 batteri. I detta fall måste slutanvändaren kontrol lera att den uppfyller alla tillämpliga säkerhets-, transport-, miljö-och andra nationella/lokala bestämmelser.
* När du arbetar med systemet med typ 2590-batteri använder du batteriet i de användnings villkor som anges av batteri tillverkaren.

![Varnings ikon 9 varning ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **:**

Den här enheten har två SFP + portar, som kan användas med optiska Sänd tagare. Undvik farlig laser strålning genom att endast använda Sänd tagare i klass 1.

## <a name="electrical-precautions"></a>Elektriska försiktighets åtgärder

Azure Stack Edge Mini R-enheten har följande elektriska försiktighets åtgärder:

![Varnings ikon 10 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) ![ elektrisk stöt ikon ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) **Varning:**

När det används med strömförsörjnings kortet:

* Tillhandahålla en säker elektrisk jordnings anslutning till strömförsörjnings sladden. Den växlade ström sladden (AC) har ett uttag av tre ledare (en plugg som har en PIN-kod för bas). Den här kontakten passar bara ett jordat växel uttag. Manipulation inte syftet med fäst punkten.
* Med tanke på att kontakten på strömförsörjnings sladden är den primära från kopplings enheten, se till att socket-uttagen är placerade nära enheten och är lätt att komma åt.
* Koppla från ström sladdarna (s) (genom att hämta kontakten, inte kabeln) och koppla från alla kablar om något av följande villkor föreligger:

  * Ström sladden eller pluggen blir sliten eller skadas på annat sätt.
  * Enheten exponeras för regn, överskotts fukt eller andra vätskor.
  * Enheten har släppts och enhetens Skift läge har skadats.
  * Du misstänker att enheten behöver tjänst eller reparation.
* Koppla bort enheten permanent innan du flyttar den eller om du tror att den har skadats på något sätt.

* Tillhandahålla en lämplig ström källa med skydd mot elektrisk överbelastning för att uppfylla följande energi krav:

* Spänning: 100 – 240 volt AC
* Aktuell: 1,7 amperes
* Frekvens: 50 till 60 Hz

![Varnings ikon 11 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ elektrisk stötande ikon ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) **Varning:**

* Försök inte att ändra eller Använd andra AC-kablar än de som medföljer utrustningen.

![Varnings ikon 12, ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ elektrisk stöt ikon ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png)
 ![ endast ](./media/azure-stack-edge-mini-r-safety/icon-safety-indoor-use-only.png) **Varning:**

* Strömförsörjning som är märkt med den här symbolen klassificeras endast för användning i inomhus.

## <a name="regulatory-information"></a>Reglerande information

Följande innehåller reglerande information för Azure Stack Edge Mini R-enhet, regel modell nummer: TMA01.

Azure Stack Edge Mini R-enheten är avsedd att användas med NRTL (UL, CSA, ETL osv.) och IEC/EN 60950-1-eller IEC/EN 62368-1-kompatibel (CE-märkta) informations tekniks utrustning.

I andra länder än USA och Kanada får nätverks kablar (som inte medföljer utrustningen) inte installeras med den här utrustningen om deras längd är större än 3 meter.

Utrustningen är utformad för att användas i följande miljöer:

| Miljö | Specifikationer |
|:---  |:--- |
| System temperaturs-specifikationer | <ul><li>Lagrings temperatur: – 20 &deg; c – 50 &deg; c (– 4 &deg; F-122 &deg; F)</li><li>Kontinuerlig åtgärd: 0 &deg; c – 40 &deg; C (32 &deg; F – 104 &deg; f)</li></ul> |
| Specifikationer för relativ fuktighet (RH) | <ul><li>Lagring: 5% till 95% relativ fuktighet</li><li>Drift: 10% till 90% relativ fuktighet</li></ul>|
| Maximala höjd specifikationer | <ul><li>Drift: 15 000 m (4 572 meter)</li><li>Ej drift: 40 000 m (12 192 meter)</li></ul>|

> ![Meddelande ikon – 2 ](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **meddelande:** &nbsp; ändringar eller ändringar som gjorts i utrustning som inte uttryckligen godkänns av Microsoft kan säga till att användarens myndighet kan använda utrustningen.

#### <a name="canada-and-usa"></a>Kanada och USA:

> ![Meddelande ikon – 3 ](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **meddelande:** &nbsp; denna utrustning har testats och visat sig uppfylla gränserna för en klass med en digital enhet, i enlighet med del 15 i FCC-reglerna. Dessa gränser är utformade för att ge ett rimligt skydd mot skadlig störning när utrustningen drivs i en kommersiell miljö. Den här utrustningen genererar, använder och kan stråla radio frekvens energi och, om de inte är installerade och används i enlighet med instruktions hand boken, kan orsaka skadlig störning av radio kommunikation. Driften av den här utrustningen i ett bostads område kan förmodligen orsaka skadlig störning, vilket innebär att användaren måste korrigera störningen vid egna utgifter.

Netgear A6150 WiFi USB-adaptern som medföljer denna utrustning är avsedd att manövreras nära människo kroppen testas för all information som rörs av en speciell absorptions frekvens (SAR). Den SAR-gräns som anges av FCC är 1,6 W/kg i genomsnitt över 1 g vävnad. När du bär produkten eller använder den samtidigt som du använder den, behåller du ett avstånd på 10 mm från bröd texten för att säkerställa efterlevnaden av krav på RF-exponering.

Netgear A6150 WiFi USB-adaptern uppfyller ANSI/IEEE C 95.1-1999 och har testats i enlighet med de mät metoder och procedurer som anges i OET Bulletin 65 supplement C.

Netgear A6150 Specific Absorption Rate (SAR): 1,18 W/kg genomsnitt över 1 g vävnad

Netgear A6150 WiFi USB-adaptern kan endast användas med godkända antenner. Den här enheten och dess antenner får inte vara samplacerade eller fungerar tillsammans med andra antenner eller sändare, förutom i enlighet med FCC: s multisändnings produkt procedurer. För produkter som är tillgängliga på marknaden i USA kan endast kanal 1 ~ 11 köras. Det går inte att välja andra kanaler.

Åtgärden i banden 5150 – 5250 MHz är endast för inomhus bruk för att minska risken för skadlig störning till kanalbaserade mobil satellit system.

![Varning om reglerande information – inomhus bruk](./media/azure-stack-edge-mini-r-safety/regulatory-information-indoor-use-only.png)

Användarna uppmanas att använda Högströms polärdiagram som primära användare (prioriterade användare) av banden 5250 – 5350 MHz och 5650 – 5850 MHz, och dessa Radarer kan orsaka störningar och/eller skada till LE-LAN-enheter.

Den här utrustningen genererar, använder och kan utgå från Radio frekvens energi och, om de inte installeras och används i enlighet med anvisningarna, kan orsaka skadlig störning av radio kommunikation. Det finns dock ingen garanti för att störningar inte sker i en viss installation.

Om den här utrustningen orsakar skadlig störning i radio-eller TV-mottagning, som kan fastställas genom att stänga av eller sätta på utrustningen, uppmanas användaren att försöka korrigera störningen med en eller flera av följande åtgärder:

- Orientera om eller flytta mottagar antennen.
- Öka separationen mellan utrustning och mottagare.
- Anslut utrustningen till ett uttag på en annan krets än den som mottagaren är ansluten till.
- Kontakta åter försäljaren eller en erfaren radio-/TV-tekniker för att få hjälp.

Mer information om problem med störningar finns på webbplatsen för FCC på [fcc.gov/cgb/consumerfacts/interference.html](https://www.fcc.gov/consumers/guides/interference-radio-tv-and-telephone-signals). Du kan också kalla FCC på 1-888-ring FCC för att begära att fakta blad om störningar och samtals störningar.

Mer information om radiofrequency-säkerhet hittar du på webbplatsen för FCC på webbplatsen för [https://www.fcc.gov/general/radio-frequency-safety-0](https://www.fcc.gov/general/radio-frequency-safety-0) webbplatsen för branschen på [http://www.ic.gc.ca/eic/site/smt-gst.nsf/eng/sf01904.html](http://www.ic.gc.ca/eic/site/smt-gst.nsf/eng/sf01904.html) .

Den här produkten har demonstrerat EMC-kompatibilitet under villkor som omfattade användningen av kompatibla kring utrustnings enheter och avskärmade kablar mellan system komponenter. Det är viktigt att du använder kompatibla kring utrustnings enheter och avskärmade kablar mellan system komponenter för att minska risken för radio-och TV-apparater och andra elektroniska enheter.

Den här enheten uppfyller kraven i del 15 i FCC-reglerna och bransch licens licenser – undantagna RSS-standarder (s). Åtgärden omfattas av följande två villkor: (1) den här enheten kan inte orsaka skadlig störning och (2) den här enheten måste acceptera eventuella störningar som tas emot, inklusive störningar som kan orsaka oönskad åtgärd på enheten.

![Regel informations varning 1](./media/azure-stack-edge-mini-r-safety/regulatory-information-1.png)

KAN ICES-3 (A)/NMB-3 (A)

Microsoft Corporation, One Microsoft Way, Redmond, WA 98052, USA

USA: (800) 426-9400

Kanada: (800) 933-4750

Netgear A6150 WiFi USB adapter FCC-ID: PY318300429
 
Netgear A6150 WiFi USB-kort KI-ID: 4054A-18300429

Netgear A6150 WiFi USB-adaptern som medföljer denna utrustning är kompatibel med SAR för allmän befolkning/okontrollerade exponerings gränser i IC RSS-102 och har testats i enlighet med de mät metoder och procedurer som anges i IEEE 1528. Bevara minst 10 mm-avstånd för Body-villkor.

Netgear A6150 WiFi USB-adaptern uppfyller kraven för den bärbara datorns bärbara RF-gräns som anges för en okontrollerad miljö och är säker för avsedd drift enligt beskrivningen i hand boken. Ytterligare minskning av RF-exponering kan uppnås genom att hålla produkten så långt som möjligt från din brödtext eller genom att ställa in enheten till en lägre uteffekt om en sådan funktion är tillgänglig.

En tabell med den särskilda absorptions frekvensen (SAR) som är genomsnittlig över 1 g för varje produkt kan ses i avsnittet USA ovan.

![Regel information Varning 2](./media/azure-stack-edge-mini-r-safety/regulatory-information-2.png)

#### <a name="european-union"></a>EUROPEISKA UNIONEN:

Begära en kopia av EU: s försäkran om överensstämmelse för denna utrustning. Skicka ett e-postmeddelande till [CSI_Compliance@microsoft.com](mailto:CSI_Compliance@microsoft.com).

Netgear A6150 WiFi USB-adaptern som medföljer denna utrustning är kompatibel med direktiv 2014/53/EU och kan också tillhandahållas på begäran.

![Varnings ikon 13 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **Varning:**  

Detta är en produkt i en klass. I en inhemsk miljö kan den här produkten orsaka radio störningar, vilket innebär att användaren kan behöva vidta lämpliga åtgärder.

Omhändertagande av avfalls batterier och elektriska och elektroniska produkter:

![Varnings ikon 14](./media/azure-stack-edge-mini-r-safety/icon-ewaste-disposal.png)

Den här symbolen på produkten eller dess batterier eller dess förpackning innebär att den här produkten och de batterier som den innehåller inte får tas bort med ditt hushålls avfall. I stället är det ditt ansvar att gå över till en tillämplig samlings plats för att återvinna batterier och elektrisk och elektronisk utrustning. Denna separata samling och återvinning bidrar till att spara naturresurser och förhindra negativa följder för människors hälsa och miljön på grund av möjlig förekomst av farliga ämnen i batterier och elektrisk och elektronisk utrustning, vilket kan orsakas av olämpligt omhändertagande. Om du vill ha mer information om var du än använder batterier och elektriska och elektroniska avgaser kontaktar du din lokala stad/kommun kontor, din hushålls tjänst för avfalls hantering eller handla där du köpte produkten. Kontakta erecycle@microsoft.com om du vill ha mer information om WEEE.

Den här produkten innehåller mynt batteri (er).

Netgear A6150 WiFi USB-adaptern som medföljer denna utrustning är avsedd att manövreras nära människo kroppen och testas för text driven Specific Specific Absorption Rate (SAR)-efterlevnad (se nedan). När du bär produkten eller använder den samtidigt som du använder den, behåller du ett avstånd från 10mm från bröd texten för att säkerställa efterlevnaden av krav på RF-exponering.

**Netgear A6150 Specific Absorption Rate (SAR):** 0,54 W/kg genomsnitt av vävnads vävnad

 
Den här enheten kan användas i alla medlems stater i EU. Iaktta nationella och lokala bestämmelser där enheten används. Den här enheten är begränsad till inomhus användning endast vid användning i frekvens intervallet 5150-5350 MHz i följande länder:  

![EU-länder som kräver enbart i bruk](./media/azure-stack-edge-mini-r-safety/mini-r-safety-eu-indoor-use-only.png)

I enlighet med artikel 10,8 a och 10,8 b i den röda artikeln innehåller följande tabell information om frekvens banden som används och maximal RF-överförings kraft för Netgear trådlösa produkter för försäljning i EU:

**WiFi**

| Frekvens intervall (MHz) | Kanaler som används | Maximal överförings kraft (dBm/mW) |
| --------------------- | ------------- | --------------------------- |
| 2400 – 2483.5 | 1-13    | ODFM: 19,9 dBm (97,7 mW) <br> CCK: 17,9 dBm (61,7 mW) |
| 5150-5320   | 36-48   | 22,9 dBm (195 mW) |
| 5250-5350   | 52-64   | 22,9 dBm (195 mW) med TPC <br> 19,9 dBm (97,7 mW) icke-TPC |
| 5470-5725   | 100-140 | 29,9 dBm (977 mW) med TPC <br> 29,6 dBm (490 mW) icke-TPC |

Microsoft Ireland Sandyford IND Est Dublin D18 KX32 IRL

Telefonnummer: + 353 1 295 3826

Fax nummer: + 353 1 706 4110

#### <a name="singapore"></a>Singapore:

Netgear A6150 WiFi USB-adaptern som medföljer denna utrustning uppfyller IMDA-standarden.


## <a name="next-steps"></a>Nästa steg

- [Förbered för att distribuera Azure Stack Edge Mini R](azure-stack-edge-mini-r-deploy-prep.md)
