---
title: Säkerhet för din Azure Data Box | Microsoft Docs
description: Beskriver säkerhets konventioner, rikt linjer och överväganden och förklarar hur du kan installera och använda din Azure Data Box på ett säkert sätt.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 08/13/2019
ms.author: alkohli
ms.openlocfilehash: f4a0739eb1ab14155a98b17e2427c5b08c9db788
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96020678"
---
# <a name="safely-install-and-operate-your-azure-data-box"></a>Installera och använda din Azure Data Box på ett säkert sätt
![Varnings ikon läsa ](./media/data-box-safety/warning_icon.png)
 ![ säkerhets meddelande ikon ](./media/data-box-safety/read_safety_and_health_information_icon.png) **läsa säkerhet och hälso information**

Läs all säkerhets information i den här artikeln innan du använder Azure Data Box. Om du inte följer anvisningarna kan det leda till brand, elektrisk stötande eller andra skador eller skada på egenskaperna.

## <a name="safety-icon-conventions"></a>Säkerhets ikon konventioner
Här följer de ikoner som du hittar när du granskar de säkerhets åtgärder som ska observeras när du konfigurerar och kör din Data Box-enhet.

| Ikon | Beskrivning |
|:--- |:--- |
| ![Varnings ikon ](./media/data-box-safety/warning_icon.png) **!** |Indikerar en farlig situation som, om de inte undviks, leder till dödsfall eller allvarlig skada. Den här signalen är begränsad till de mest extrema situationerna. |
| ![Varnings ikon ](./media/data-box-safety/warning_icon.png) **Varning!** |Indikerar en farlig situation som, om de inte undviks, kan leda till dödsfall eller allvarlig skada. |
| ![Varnings ikon varning ](./media/data-box-safety/warning_icon.png) **!** |Indikerar en farlig situation som, om de inte undviks, kan leda till mindre eller måttlig skada. |
| ![Meddelande Ikons ](./media/data-box-safety/notice_icon.png) **meddelande:** |Anger information som anses viktig, men inte risk relaterad. |
| ![Elektrisk stötande stöt vid ](./media/data-box-safety/electrical_shock_hazard_icon.png) **fara** |Hög spänning. |
| ![](./media/data-box-safety/heavy_weight_hazard_icon.png)  Extrafet vikt ikon | |
| ![Ingen User serviceable parts-ikon ](./media/data-box-safety/no_user_serviceable_parts_icon.png) **saknar användar tjänst delar** |Få inte åtkomst om den inte är korrekt utbildad. |
| ![Läs säkerhets meddelande ikonen ](./media/data-box-safety/read_safety_and_health_information_icon.png) **Läs alla instruktioner först** | |
| ![Tips risk ikon ](./media/data-box-safety/tip_hazard_icon.png) **tips** | |

## <a name="handling-precautions"></a>Hantering av försiktighets åtgärder

![Varnings ikon för ](./media/data-box-safety/warning_icon.png) ![ elektriska stöter ikoner ](./media/data-box-safety/electrical_shock_hazard_icon.png)![ ingen användar tjänst dels ikon ](./media/data-box-safety/no_user_serviceable_parts_icon.png) **Varning** 

* Kontrol lera att enheten *tas emot* för skador. Om enhets kabinettet är skadat kan du [kontakta Microsoft Support](data-box-disk-contact-microsoft-support.md) för att få en ny ersättning. Försök inte att använda enheten. 
* Enheten är utrustad med Manipulerings skruvar. Om du misstänker att enheten inte fungerar kan du [kontakta Microsoft Support](data-box-disk-contact-microsoft-support.md) för att få en ny ersättning. Försök inte att betjäna enheten. 
* Enheten innehåller inga delar som är baserade på användar tjänster. Farliga spännings-, ström-och energi nivåer finns i. Öppna inte. Returnera enheten till Microsoft för underhåll.

![Varnings ikon ](./media/data-box-safety/warning_icon.png) ![ Varning vid hög vikts ikon ](./media/data-box-safety/heavy_weight_hazard_icon.png) **!** 

* En fullständigt konfigurerad hölje kan väga upp till 22,7 kg (50 kg). Försök inte att lyfta upp den själv.
* Innan du flyttar kammaren måste du alltid se till att två personer är tillgängliga för att hantera vikten. Tänk på att en person som försöker lyfta upp den här vikten kan upprätthålla skador.


![Varnings ikon ](./media/data-box-safety/warning_icon.png) ![ tips ](./media/data-box-safety/tip_hazard_icon.png) **Varning!**
* Placera enheten på en platt, hård och stabil yta för att undvika risk för risk.
* Rack monterings utrustning får inte användas som hyllor eller arbets utrymmen. Placera inte Data Box-enhet ovanpå utrustning som ställts i rack montering. Genom att lägga till alla typer av belastning till en utökad rackmonterad enhet kan du skapa potentiella Tip-risker som kan leda till skador, dödsfall eller produkt skador.

![Varnings ikon ](./media/data-box-safety/warning_icon.png) **Varning!**

* Konfigurera enheten i en arbets plats så att den har tillräcklig luft cirkulation runt enheten.
* Installera enheten i en temperatur-kontrollerad inomhus yta som är fri från mellanliggande föroreningar och möjliggör tillräcklig luft cirkulation runt enheten.
* Håll enheten borta från källor till flytande och kraftigt fuktiga miljöer.


## <a name="electrical-precautions"></a>Elektriska försiktighets åtgärder

![Varnings ikon ](./media/data-box-safety/warning_icon.png) ![ varning om elektrisk stötande ikon ](./media/data-box-safety/electrical_shock_hazard_icon.png) **!**

* Tillhandahålla en säker elektrisk jordnings anslutning till strömförsörjnings sladden. AC-kabeln har ett uttag för tre ledare (en plugg som har en PIN-kod för mark). Den här kontakten passar bara ett jordat växel uttag. Manipulation inte syftet med fäst punkten.
* Med tanke på att kontakten på strömförsörjnings sladden är den primära från kopplings enheten, se till att socket-uttagen är placerade nära enheten och är lätt att komma åt.
* Koppla från ström sladden (genom att dra kontakten, inte kabeln) och koppla från alla kablar om något av följande villkor föreligger:

    - Ström sladden eller pluggen blir sliten eller skadas på annat sätt.
    - Du spiller något i enhetens hölje.
    - Enheten exponeras för regn eller överskotts fukt.
    - Enheten släpptes och enhetens Skift läge är skadat.
    - Du misstänker att enheten behöver tjänst eller reparation.
* Koppla bort enheten permanent innan du flyttar den eller om du tror att den har skadats på något sätt.
* Tillhandahålla en lämplig ström källa med skydd mot elektrisk överbelastning för att uppfylla följande energi krav:

    - Spänning: 100 V AC till 240 V AC
    - Nuvarande: 6 A, max
    - Frekvens: 50 Hz till 60 Hz

![Varnings ikon varning ](./media/data-box-safety/warning_icon.png) **:**

* Den här enheten innehåller mynt batterier. Försök inte att betjäna enheten. Batterier i den här enheten är inte användar tjänst. 
* **Endast för tjänste personal**: explosions risk om batteriet ersätts av en felaktig typ. Ta bort de batterier som används enligt instruktionerna.

![Meddelande Ikons ](./media/data-box-safety/notice_icon.png) **meddelande:**

För att enheten ska fungera korrekt och för att förhindra produkt skada:

* Se till att front-och back dörrarna är helt öppna när enheten körs.

## <a name="regulatory-information"></a>Reglerande information

Det här avsnittet innehåller information om Azure Data Box, regel modell nummer DB010.

Den här enheten är:

- Utvärderas som information Technology Equipment (amn) och är utformad för att användas i en typisk data rums miljö. Den här produktens lämplighet för andra miljöer kan kräva ytterligare utvärdering.
- Utformad för användning med NRTL listad (UL, CSA, ETL osv.) och IEC/EN 60950-1-eller IEC/EN 62368-1-kompatibel (CE-märkta) informations teknik utrustning.
- Utformad för att användas i följande miljö. 
    - Drift temperatur: 50 ° till 95 ° F (10 ° till 35 ° C)
    - Lagrings temperatur:-4 ° till 122 ° F (-20 ° till 50 ° C)
    - Relativ fuktighet: 15% till 85% (icke-kondensning) 
    - Drift höjd: testat upp till 6500 meter (0 meter till 2000 meter)

För klassificeringar för elektrisk försörjning, se enhets klassificerings etiketten som medföljer enheten. 

![Meddelande Ikons ](./media/data-box-safety/notice_icon.png) **meddelande:** 

Ändringar eller ändringar av enheten som inte uttryckligen godkänns av Microsoft kan säga till användarens behörighet att använda enheten.

**Kanada och USA:**

![Meddelande Ikons ](./media/data-box-safety/notice_icon.png) **meddelande:** 

Den här utrustningen har testats och visat sig uppfylla gränserna för en klass med en digital enhet, i enlighet med del 15 i FCC-reglerna. Dessa gränser är utformade för att ge ett rimligt skydd mot skadlig störning när utrustningen drivs i en kommersiell miljö. Den här utrustningen genererar, använder och kan stråla radio frekvens energi och, om de inte är installerade och används i enlighet med instruktions hand boken, kan orsaka skadlig störning av radio kommunikation. Driften av den här utrustningen i ett bostads område kan förmodligen orsaka skadlig störning, vilket innebär att användaren måste korrigera störningen vid egna utgifter.

Den här enheten uppfyller kraven i del 15 i FCC-reglerna och bransch licens licenser – undantagna RSS-standarder (s). Åtgärden omfattas av följande två villkor: (1) den här enheten kan inte orsaka skadlig störning och (2) den här enheten måste acceptera eventuella störningar som tas emot, inklusive störningar som kan orsaka oönskad åtgärd på enheten.

![Skärm bild som visar ett meddelande som krävs för Kanada.](./media/data-box-safety/canada.png)

KAN ICES-3 (A)/NMB-3 (A)

Microsoft Corporation, One Microsoft Way, Redmond, WA 98052, USA.

USA: (800) 426-9400

Kanada: (800) 933-4750

**EUROPEISKA UNIONEN:**

[Begära en kopia av EU: s försäkran om överensstämmelse](mailto:CSI_Compliance@microsoft.com).

![Varnings Ikons ](./media/data-box-safety/warning_icon.png) **Varning:** 

Detta är en produkt i en klass. I en inhemsk miljö kan den här produkten orsaka radio störningar, vilket innebär att användaren kan behöva vidta lämpliga åtgärder.

**Omhändertagande av avfalls batterier och elektriska och elektroniska produkter:**

![Ikon för batteri kasse ring](./media/data-box-safety/battery_disposal_icon.png)

Den här symbolen på produkten eller dess batterier eller dess förpackning innebär att den här produkten och de batterier som den innehåller inte får tas bort med ditt hushålls avfall. I stället är det ditt ansvar att gå över till en tillämplig samlings plats för att återvinna batterier och elektrisk och elektronisk utrustning. Denna separata samling och återvinning bidrar till att spara naturresurser och förhindra negativa följder för människors hälsa och miljön på grund av möjlig förekomst av farliga ämnen i batterier och elektrisk och elektronisk utrustning, vilket kan orsakas av olämpligt omhändertagande. Om du vill ha mer information om var du än använder batterier och elektriska och elektroniska avgaser kontaktar du din lokala stad/kommun kontor, din hushålls tjänst för avfalls hantering eller handla där du köpte produkten. Kontakta *erecycle \@ Microsoft.com* om du vill ha mer information om WEEE.

Den här produkten innehåller mynt batteri (er).

Microsoft Ireland Sandyford IND Est Dublin D18 KX32 IRL

Telefonnummer: + 353 1 295 3826

Fax nummer: + 353 1 706 4110 

**Japan**

![Skärm bild som visar ett meddelande som krävs för Japan.](./media/data-box-safety/japan.png)

<!--**South Korea**

![South Korea](./media/data-box-safety/south-korea.png)

**China RoHS Hazardous Substances Table**

![China RoHS hazardous substances](./media/data-box-safety/south-korea.png)

**Taiwan**

![Taiwan](./media/data-box-safety/taiwan.png)-->

När du har granskat dessa säkerhets meddelanden kan du konfigurera och kabelansluta enheten.

## <a name="next-steps"></a>Nästa steg

* [Kabel och Anslut din Data Box-enhet](data-box-deploy-set-up.md)


