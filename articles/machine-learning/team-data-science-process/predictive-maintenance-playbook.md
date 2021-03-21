---
title: Azure AI-guide för förebyggande underhålls lösningar – team data science process
description: En omfattande beskrivning av data vetenskap som skyddar förebyggande underhålls lösningar i flera vertikala branscher.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: previous-author=fboylu, previous-ms.author=fboylu
ms.openlocfilehash: 1e939b86eeadfee276378488cfcb40c07f28684d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98880666"
---
# <a name="azure-ai-guide-for-predictive-maintenance-solutions"></a>Azure AI-guide för förebyggande underhålls lösningar

## <a name="summary"></a>Sammanfattning

Förutsägande underhåll (**PdM**) är en populär tillämpning av förutsägelse analys som kan hjälpa företag i flera branscher att uppnå hög till gångs användning och besparingar i drifts kostnader. Den här guiden samlar in affärs-och analys rikt linjer och metod tips för att utveckla och distribuera PdM-lösningar med hjälp av [Microsoft Azure AI-plattforms](https://azure.microsoft.com/overview/ai-platform) teknik.

Den här guiden innehåller bransch specifika affärs scenarier och processen för att kvalificera dessa scenarier för PdM. Data krav och modellerings tekniker för att bygga PdM-lösningar tillhandahålls också. Huvud innehållet i guiden finns på data vetenskaps processen – inklusive stegen för förberedelse av data, funktions teknik, modell skapande och modell driftsättning. För att komplettera dessa viktiga begrepp visar den här guiden en uppsättning Solution-mallar som hjälper till att påskynda PdM program utveckling. Guiden pekar också på användbara utbildnings resurser som du kan använda för att lära dig mer om AI bakom data vetenskap. 

### <a name="data-science-guide-overview-and-target-audience"></a>Översikt över data vetenskaps guide och mål grupp
I den första hälften av den här guiden beskrivs vanliga affärs problem, fördelarna med att implementera PdM för att åtgärda dessa problem och listar några vanliga användnings fall. Besluts fattare för företag (BDMs) kommer att dra nytta av det här innehållet. Den andra hälften förklarar data vetenskap bakom PdM och innehåller en lista med PdM-lösningar som skapats med hjälp av de principer som beskrivs i den här hand boken. Det ger också inlärnings vägar och pekare för att träna material. Tekniska besluts fattare (TDMs) kommer att ha nytta av det här innehållet.

| Börja med... | Om du är... |
|:---------------|:---------------|
| [Affärs fall för förutsägande underhåll](#business-case-for-predictive-maintenance) |en organisations besluts fattare (BDM) som vill minska drift avbrott och drifts kostnader och förbättra användningen av utrustning |
| [Data vetenskap för förutsägande underhåll](#data-science-for-predictive-maintenance) |en teknisk besluts fattare (TDM) utvärderar PdM Technologies för att förstå de unika data bearbetnings-och AI-kraven för förutsägande underhåll |
| [Solution-mallar för förutsägande underhåll](#solution-templates-for-predictive-maintenance)|en program varu arkitekt eller AI-utvecklare som vill se upp en demonstration eller ett koncept bevis |
| [Utbildnings resurser för förutsägande underhåll](#training-resources-for-predictive-maintenance) | alla eller alla ovanstående och vill lära dig grunderna för data vetenskap, verktyg och tekniker.

### <a name="prerequisite-knowledge"></a>Förhandskunskaper
BDM-innehållet förväntar inte läsaren att ha någon tidigare data vetenskaps kunskap. För TDM-innehållet är grundläggande kunskaper om statistik och data vetenskap användbara. Kunskap om Azure data och AI-tjänster, python, R, XML och JSON rekommenderas. AI-tekniker implementeras i python-och R-paket. Solution templates implementeras med hjälp av Azure-tjänster, utvecklingsverktyg och SDK: er.

## <a name="business-case-for-predictive-maintenance"></a>Affärs fall för förutsägande underhåll

Företag kräver kritisk utrustning för att kunna köra hög effektivitet och användning för att ge avkastning på kapital investeringar. Dessa till gångar kan vara ett intervall från flyg Plans motorer, turbiner, hissar eller industriella kyl verktyg – som kostar miljon tals till vardags apparater som foto kopiatorer, kaffe maskiner eller vattenbaserade kyl apparater.
- Som standard förlitar sig de flesta företag på _rätt underhåll_, där delar byts ut som och när de inte fungerar. Korrigerings underhåll garanterar att delar används helt (vilket inte slösar ut komponentens livs längd), utan ger kostnader för drift avbrott, arbete och oplanerat underhåll (från timmar eller olämpliga platser).
- På nästa nivå kan företag öva på  _förebyggande underhåll_, där de fastställer den användbara livs längd för en del, och upprätthålla eller ersätta den innan ett problem uppstår. Vid förebyggande underhåll undviks oplanerade avbrott och katastrofer. Men höga kostnader för schemalagd stillestånds tid, underutnyttjande av komponenten under dess livs längd och arbete fortfarande kvarstår.
- Målet med _förutsägande underhåll_ är att optimera balansen mellan korrigerat och förebyggande underhåll genom att aktivera _just i tids_ byte av komponenter. Den här metoden ersätter bara komponenterna när de är nära ett haveri. Genom att utöka komponent lifespans (jämfört med förebyggande underhåll) och minska oplanerat underhåll och arbets kostnader (över korrigerat underhåll) kan företag få kostnads besparingar och konkurrens för delar.

## <a name="business-problems-in-pdm"></a>Affärs problem i PdM
Företag möter hög drifts risk på grund av oväntade fel och har begränsad insikt i rotor saken till problem i komplexa system. Några av viktiga affärs frågor är:

- Identifiera avvikelser i utrustning eller system prestanda eller funktioner.
- Förutsäg om en till gång kan Miss lyckas inom en snar framtid.
- Beräkna den återstående användbara livs längden för en till gång.
- Identifiera huvudsakliga orsaker till en till gångs fel.
- Identifiera vilka underhålls åtgärder som måste utföras när, på en till gång.

Typiska mål uttryck från PdM är:

- Minska drifts risken för verksamhets kritisk utrustning.
- Öka räntabiliteten för till gångar genom att förutsäga fel innan de inträffar.
- Kontrol lera kostnaden för underhåll genom att aktivera just-in-Time-underhåll.
- Lägre kund attrition, bättre varumärkes avbildning och förlorad försäljning.
- Lägre lager kostnader genom att minska lager nivåerna genom att förutsäga beställnings punkten.
- Identifiera mönster som är kopplade till olika underhålls problem.
- Ange KPI: er (nyckeltal) som hälso Poäng för till gångs villkor.
- Uppskatta återstående livs längd av till gångar.
- Rekommendera underhålls aktiviteter.
- Aktivera just-in-time-inventering genom att uppskatta order datum för utbyte av delar.

Dessa mål uttryck är start punkter för:

- _data_ tekniker för att analysera och lösa vissa förutsägelse problem.
- _moln arkitekter och utvecklare_ kan samla in en lösning från slut punkt till slut punkt.

## <a name="qualifying-problems-for-predictive-maintenance"></a>Kvalificera problem för förutsägande underhåll
Det är viktigt att betona att inte alla användnings fall eller affärs problem kan lösas effektivt av PdM. Det finns tre viktiga kvalificerings villkor som måste beaktas vid val av problem:

- Problemet måste vara förutsägbart. det vill säga det bör finnas ett mål eller ett resultat att förutsäga. Problemet bör också ha en tydlig åtgärds åtgärd för att förhindra fel när de upptäcks.
- Problemet bör ha en post i drift historiken för den utrustning som innehåller _både bra och dåliga resultat_. Den uppsättning åtgärder som vidtas för att minimera dåliga resultat bör också vara tillgängliga som en del av dessa poster. Fel rapporter, underhålls loggar för prestanda försämring, reparationer och ersättnings loggar är också viktiga. Dessutom är reparationer som vidtas för att förbättra dem, och ersättnings poster är också användbara.
- Den inspelade historiken bör avspeglas i _relevanta_ data som har tillräckligt med _tillräcklig_ kvalitet för att stödja användnings fallet. Mer information om data relevans och mer information finns i [data krav för förutsägande underhåll](#data-requirements-for-predictive-maintenance).
- Slutligen bör företaget ha domän experter som är klara att förstå problemet. De bör vara medvetna om de interna processerna och praxisen för att kunna hjälpa analytikern att förstå och tolka data. De bör också kunna göra nödvändiga ändringar i befintliga affärs processer för att hjälpa till att samla in rätt data för problemen, om det behövs.

## <a name="sample-pdm-use-cases"></a>Exempel på PdM användnings fall
Det här avsnittet fokuserar på en samling PdM användnings fall från flera branscher, till exempel Aerospace, verktyg och transport. Varje avsnitt börjar med ett affärs problem och diskuterar fördelarna med PdM, relevanta data som omger affärs problemet och slutligen fördelarna med en PdM-lösning.

| Affärs problem | Fördelar från PdM |
|:-----------------|-------------------|
|**Flygbranschen**      |                   |
|_Avgångs-och uppsägnings fördröjningar_ på grund av mekaniska problem. Fel som inte går att reparera i tid kan orsaka att flygningar avbryts och att avbrott och åtgärder avbryts. |PdM-lösningar kan förutsäga sannolikheten för att ett flyg plan försenas eller avbryts på grund av mekaniska haverier.|
|_Flyg Plans motor delar inte_: ersättningar för luft dels motorer är bland de vanligaste underhålls aktiviteterna inom flyg branschen. Underhålls lösningar kräver noggrann hantering av komponent tillgänglighet, leverans och planering|Att kunna samla in information om komponent tillförlitlighet leder till avsevärd minskning av investerings kostnaderna.|
|**Ekonomi** |                         |
|_ATM-fel_ är ett vanligt problem inom bank branschen. Problemet här är att rapportera sannolikheten att en transaktion för en ATM-likvid bryts på grund av ett pappers stopp eller ett del fel i kassa utdraget. Utifrån förutsägelser av transaktions haverier kan Network servas proaktivt för att förhindra att felen inträffar.| I stället för att tillåta att datorn växlar halvvägs genom en transaktion, är det önskvärda alternativet att program mera datorn för att neka service utifrån förutsägelsen.|
|**Energi** |                          |
|_Lindnings turbin-problem_: vind turbiner är den huvudsakliga energi källan i miljö ansvariga länder/regioner och innefattar höga kapital kostnader. En viktig komponent i lindnings turbiner är Generator-motorn, vars haveri återger den ineffektiva turbinen. Det är också mycket kostsamt att åtgärda problemet.|Förutsägelser av KPI: er, till exempel MTTF (Mean Time to Failure), kan hjälpa energi företagen att förhindra turbin-fel och kontrol lera minimal nedtid. Haverier kommer att informera tekniker om att övervaka turbiner som sannolikt kommer att Miss lyckas snart, och schemalägga tidsbaserade underhålls system. Förutsägande modeller ger insikter om olika faktorer som bidrar till fel, vilket hjälper tekniker bättre att förstå orsaken till problem.|
|_Krets brytar haverier_: distribution av el till hus och företag kräver att energi linjer fungerar hela tiden för att garantera energi leverans. Krets brytarna bidrar till att begränsa eller undvika skador på ström linjer vid överbelastning eller ogynnsamma väder förhållanden. Affärs problemet här är att förutsäga fel i krets brytaren.| PdM-lösningar hjälper till att minska reparations kostnaderna och öka livs längd för utrustning som krets brytare. De hjälper till att förbättra kvaliteten på Power Network genom att minska oväntade fel och avbrott i tjänsten.|
|**Transport och logistik** |    |
|_Problem med hiss dörr_: stora hiss företag tillhandahåller en full stack tjänst för miljon tals funktionella hissar runt om i världen. Ökad säkerhet, tillförlitlighet och drift tid är de viktigaste problemen för kunderna. Dessa företag spårar dessa och olika andra attribut via sensorer, för att hjälpa dem med korrigering och förebyggande underhåll. I en hiss är det mest framträdande kund problemet att du inte kan lösa ut problem med hiss dörrar. Affärs problemet i det här fallet är att tillhandahålla ett förutsägelse program för kunskaps basen som förutsäger möjliga orsaker till dörr fel.| Hissar är kapital investeringar för en 20-30 års livs längd. Varje potentiell försäljning kan därför vara mycket konkurrens kraftig. Därför är förväntningarna för tjänsten och supporten höga. Förebyggande underhåll kan ge företagen en fördel i sina konkurrenter i produkt-och tjänst erbjudanden.|
|_Hjul haverier_: arbets hjuls haverier för hälften av alla tränar och kostar miljarder till den globala järnvägs branschen. Hjul haverier kan också orsaka att räler försämras, vilket ibland orsakar att spåret bryts för tidigt. Järnväg bryts leder till oåterkalleliga händelser, till exempel dejärnvägar. För att undvika sådana instanser övervakar järnväg prestanda för hjul och ersätter dem på ett förebyggande sätt. Affärs problemet här är förutsägelsen för hjul fel.| Förutsägande underhåll av hjul hjälper till just-in-Time-utbyte av hjul |
|_Subway träna dörr haverier_: en stor orsak till fördröjningar i Subway-åtgärder är dörr haverier på tåg bilar. Det här är ett problem med att förutsäga dörr fel.|Tidig medvetenhet om ett dörr fel eller antalet dagar tills ett dörr fel uppstår hjälper verksamheten att optimera drifts scheman för dörr dörr.|

I nästa avsnitt får du information om hur du kan inse de PdM-fördelar som beskrivs ovan.

## <a name="data-science-for-predictive-maintenance"></a>Data vetenskap för förutsägande underhåll

Det här avsnittet innehåller allmänna rikt linjer för principer för data vetenskap och praxis för PdM. Det är avsett att hjälpa en TDM, lösnings arkitekt eller en utvecklare att förstå kraven och processen för att skapa AI-program från slut punkt till slut punkt för PdM. Du kan läsa det här avsnittet tillsammans med en granskning av de demonstrationer och proof-of-Concept-mallar som anges i [Solution templates för förutsägande underhåll](#solution-templates-for-predictive-maintenance). Du kan sedan använda dessa principer och bästa praxis för att implementera din PdM-lösning i Azure.

> [!NOTE]
> Den här guiden är inte avsedd att lära sig läsa data vetenskap. Det finns flera användbara källor för att läsa mer i avsnittet om [utbildnings resurser för förebyggande underhåll](#training-resources-for-predictive-maintenance). I de [Solution templates](#solution-templates-for-predictive-maintenance) i guiden visas några av de här AI-teknikerna för vissa PdM-problem.

## <a name="data-requirements-for-predictive-maintenance"></a>Data krav för förutsägande underhåll

Framgång för alla inlärningar beror på (a) kvaliteten på vad som är inlärts och (b) förmågan hos eleven. Förutsägande modeller lär sig mönster från historiska data och förutsäger framtida resultat med viss sannolikhet baserat på de observerade mönstren. En modells förutsägelse noggrannhet beror på relevanta, kvalitet och kvalitet för utbildningens och test data. De nya data som har värdet "Scores" som använder den här modellen ska ha samma funktioner och schema som utbildnings-/test data. Egenskaperna (typ, densitet, distribution osv.) för nya data bör överensstämma med data uppsättningarna utbildning och test. Fokus för det här avsnittet finns på sådana data krav.

### <a name="relevant-data"></a>Relevanta data

Först måste data vara _relevanta för problemet_. Överväg det användnings fall för _hjulet_ som beskrivs ovan – tränings data bör innehålla funktioner relaterade till hjul åtgärderna. Om problemet var att förutsäga fel i  _traktions systemet_, måste tränings data omfatta alla olika komponenter för traktions systemet. Det första fallet riktar sig till en speciell komponent, medan det andra fallet är en del av ett större del system. Den allmänna rekommendationen är att utforma förutsägelse system om vissa komponenter i stället för större del system, eftersom den senare kommer att ha mer spridda data. Domän experten (se [kvalificerings problem för förutsägande underhåll](#qualifying-problems-for-predictive-maintenance)) bör hjälpa till att välja de mest relevanta del mängderna av data för analysen. Relevanta data källor beskrivs i detalj i [förberedelse av data för förutsägande underhåll](#data-preparation-for-predictive-maintenance).

### <a name="sufficient-data"></a>Tillräckligt med data
Två frågor meddelas ofta med avseende på fel historik data: (1) "hur många fel händelser krävs för att träna en modell?" (2) "hur många poster betraktas som" tillräckligt "?" Det finns inga slutgiltiga svar, utan endast regler för tummen. För (1), mer antal felaktiga händelser, bättre modellen. För (2) och det exakta antalet fel händelser beror på data och kontexten för problemet som löses. Men på vänd sidan, om en dator Miss lyckas för ofta, kommer företaget att ersätta den, vilket minskar fel instanserna. Här igen är vägledningen från domän experten viktig. Det finns dock metoder för att hantera problemet med _sällsynta händelser_. De beskrivs i avsnittet [Hantera sambalanserade data](#handling-imbalanced-data).

### <a name="quality-data"></a>Kvalitets data
Data kvaliteten är kritiskt-varje värde för förutsägande attribut måste vara _korrekt_ tillsammans med värdet för mål variabeln. Data kvalitet är ett väl undersökande område i statistik och data hantering och kan därför inte omfattas av den här guiden.

> [!NOTE]
> Det finns flera resurser och företags produkter för att leverera kvalitets data. Det finns ett exempel på referenser nedan:
> - Dasu, T, Johnson, T. ex., prov data utvinning och data rensning, Wiley, 2003.
> - [Analys av prov data, wikipedia](https://en.wikipedia.org/wiki/Exploratory_data_analysis)
> - [Hellerstein, J, kvantitativ data rensning för stora databaser](http://db.cs.berkeley.edu/jmh/papers/cleaning-unece.pdf)
> - [de Jonge, E, van der Loo, M, introduktion till data rengöring med R](https://cran.r-project.org/doc/contrib/de_Jonge+van_der_Loo-Introduction_to_data_cleaning_with_R.pdf)

## <a name="data-preparation-for-predictive-maintenance"></a>Förberedelse av data för förutsägande underhåll

### <a name="data-sources"></a>Datakällor

Relevanta data källor för förutsägande underhåll inkluderar, men är inte begränsade till:
- Historik för haveri
- Underhåll/reparations historik
- Maskin drifts förhållanden
- Metadata för utrustning

#### <a name="failure-history"></a>Historik för haveri
Händelser som misslyckats är ovanliga i PdM-program. Men när du skapar förutsägelse modeller behöver algoritmen lära sig mer om en komponents normala operativa mönster, samt dess fel mönster. Därför bör tränings data innehålla tillräckligt många exempel från båda kategorierna. Underhålls poster och reserv dels ersättnings historik är användbara källor för att hitta felhändelser. Med hjälp av vissa domän kunskaper kan avvikelser i tränings data också definieras som fel.

#### <a name="maintenancerepair-history"></a>Underhåll/reparations historik
Underhålls historiken för en till gång innehåller information om komponenter som ersatts, reparations aktiviteter som utförs osv. Dessa händelser registrerar försämrings mönster. Avsaknad av denna viktiga information i tränings data kan leda till missvisande modell resultat. Fel historiken kan också finnas i underhålls historiken som särskilda felkoder eller som beställnings datum för delar. Ytterligare data källor som påverkar Felaktiga mönster bör undersökas och tillhandahållas av domän experter.

#### <a name="machine-operating-conditions"></a>Maskin drifts förhållanden
Sensorbaserade (eller andra) strömmande data för utrustning i drift är en viktig data källa. En viktig antagande i PdM är att datorns hälso status försämras med tiden under sin rutin åtgärd. Data förväntas innehålla tidsvarierade funktioner som fångar upp det här ålders mönstret och eventuella avvikelser som leder till försämring. Den temporala aspekten av data krävs för algoritmen för att lära sig felen och andra mönster som inte är felfelaktiga över tid. Algoritmen är baserad på dessa data punkter och lär sig att förutsäga hur många fler enheter som en dator kan fortsätta att arbeta innan den Miss lyckas.

#### <a name="static-feature-data"></a>Statisk funktions data
Statiska funktioner är metadata om utrustningen. Exempel är utrustnings fabrikat, modell, tillverknings datum, start datum för tjänsten, systemets placering och andra tekniska specifikationer.

Exempel på relevanta data för [exempel på PdM användnings fall](#sample-pdm-use-cases) är tabellen nedan:

| Användningsfall | Exempel på relevanta data |
|:---------|---------------------------|
|_Fördröjning och uppsägning av flygning_ | Flyg flödes information i form av flighta ben och sid loggar. Flyg benets data innehåller routningsinformation, till exempel avgångs-/ankomst datum, tid, flyg plats, layovers osv. Sid loggen innehåller en serie fel och underhålls koder som registrerats av personal underhålls personalen.|
|_Delar av flyg Plans motor delar_ | Data som samlas in från sensorer i flyg planet som innehåller information om villkoret för de olika delarna. Underhålls poster hjälper dig att identifiera när komponent fel inträffade och när de ersatts.|
|_ATM-problem_ | Sensor avläsningar för varje transaktion (deponering av kontanter/check) och kontant betalning. Information om avstånds mätning mellan anteckningar, antecknings tjock lek, anteckningens avstånd, kontrol lera attribut osv. Underhålls poster som innehåller felkoder, reparations information, senaste gången kassa utfyllnaden har fyllts i.|
|_Lindnings turbin-felkod_ | Sensorer övervakar turbin-villkor som temperatur, vridnings riktning, Power genered, generator hastighet osv. Data samlas in från flera lindnings turbiner från lindnings grupper som finns i olika regioner. Normalt kommer varje turbin att ha flera sensor avläsningar som vidarebefordrar måtten med ett fast tidsintervall.|
|_Krets brytar avbrott_ | Underhålls loggar som innehåller korrigerande, förebyggande och systematiska åtgärder. Drift data som innehåller automatiska och manuella kommandon som skickas till krets brytare, till exempel för att öppna och stänga åtgärder. Metadata för enheten, till exempel datum för tillverkning, plats, modell osv. Specifikationer för krets brytare, till exempel spännings nivåer, geolokalisering, omgivande förhållanden.|
|_Problem med hiss dörr_| Hiss-metadata, till exempel typ av hiss, tillverkande datum, underhålls frekvens, byggnads typ och så vidare. Drift information, till exempel antal dörr cyklar, genomsnittlig dörr stängnings tid. Fel historik med orsaker.|
|_Hjul haverier_ | Sensor data som mäter hjul acceleration, bromsnings instanser, fart avstånd, hastighet osv. Statisk information om hjul som tillverkare, tillverkar datum. Det gick inte att härleda data från en del ordnings databas som spårar order datum och kvantiteter.|
|_Subway träna dörr haverier_ | Öppnings-och stängnings tider för dörren, andra drift data, till exempel det aktuella tillståndet för ombordpersonalen. Statiska data inkluderar kolumner för till gångs identifierare, tid och villkors värde.|

### <a name="data-types"></a>Datatyper
Utifrån data källorna ovan är de två huvud data typerna som observerats i PdM-domänen:

- _Temporala data_: operationell telemetri, maskin villkor, arbets order typer, prioritets koder som har tidsstämplar vid tidpunkten för registrering. Det går inte att utföra underhåll/reparationer och användnings historiken har tidsstämplar som är kopplade till varje händelse.
- _Statiska data_: dator funktioner och operatörs funktioner i allmänhet är statiska eftersom de beskriver de tekniska specifikationerna för datorer eller operatörs attribut. Om dessa funktioner kan ändras med tiden bör de också ha tidsstämplar som är kopplade till dem.

Förutsägare och mål variabler bör förbearbetas/omvandlas till [numeriska, kategoriska och andra data typer](https://www.statsdirect.com/help/basics/measurement_scales.htm) beroende på vilken algoritm som används.

### <a name="data-preprocessing"></a>Data förbehandling
Som en förutsättning för _funktions teknik_ förbereder du data från olika strömmar för att skapa ett schema från vilket det är enkelt att skapa funktioner. Visualisera data först som en tabell med poster. Varje rad i tabellen representerar en utbildnings instans och kolumnerna representerar _förutsägande_ funktioner (kallas även oberoende attribut eller variabler). Organisera data så att den eller de sista kolumnerna är _målet_ (beroende variabel). För varje utbildnings instans tilldelar du en _etikett_ som värde för den här kolumnen.

För temporala data delar du varaktigheten för sensor data i tidsenheter. Varje post ska tillhöra en tidsenhet för en till gång _och bör ha distinkt information_. Tidsenheter definieras utifrån affärs behoven i multipler av sekunder, minuter, timmar, dagar, och så vidare. Tidsenheten _behöver inte vara samma som frekvensen för data insamling_. Om frekvensen är hög kan data inte visa någon viktig skillnad från en enhet till en annan. Anta till exempel att omgivnings temperaturen samlades in var 10: e sekund. Om du använder samma intervall för tränings data används bara antalet exempel utan ytterligare information. I det här fallet skulle en bättre strategi vara att använda Genomsnittligt data över 10 minuter eller en timme baserat på affärs justeringen.

För statiska data,
- _Underhålls poster_: rå data för underhåll har ett till gångs-ID och tidsstämpel med information om underhålls aktiviteter som har utförts vid en viss tidpunkt. Transformera underhålls aktiviteter till _kategoriska_ -kolumner där varje kategori Beskrivning unikt mappar till en viss underhålls åtgärd. Schemat för underhålls poster omfattar till gångs-ID, tid och underhålls åtgärder.

- _Fel poster_: fel eller fel orsaker kan registreras som specifika felkoder eller fel händelser som definieras av specifika affärs villkor. I de fall där utrustningen har flera felkoder bör domän experten hjälpa till att identifiera de som är relevanta för mål variabeln. Använd de återstående fel koderna eller villkoren för att skapa _förutsägbara_ funktioner som korrelerar med dessa fel. Schemat för felposter omfattar till gångs identifierare, tid, haveri eller felorsak – om det är tillgängligt.

- _Metadata för dator och operator_: slå samman dator-och operatörs data till ett schema för att associera en till gång med dess operatör, tillsammans med deras respektive attribut. Schemat för maskin villkor omfattar till gångs identifierare, till gångs funktioner, operator identifierare och operatörs funktioner.

Andra data bearbetnings steg omfattar _hantering av saknade värden_ och _normalisering_ av attributvärden. En detaljerad diskussion ligger utanför den här guidens omfattning – se nästa avsnitt för några användbara referenser.

Med ovanstående förbearbetade data källor på plats är den slutliga omvandlingen före funktions teknik att koppla ihop tabellerna ovan baserat på till gångs-ID och tidsstämpel. Den resulterande tabellen skulle ha null-värden för kolumnen misslyckades när datorn är i normal drift. Dessa null-värden kan tilldelas av en indikator för normal drift. Använd den här kolumnen för att skapa _etiketter för den förutsägande modellen_. Mer information finns i avsnittet om [modellerings tekniker för förutsägande underhåll](#modeling-techniques-for-predictive-maintenance).

## <a name="feature-engineering"></a>Funktionsframställning
Funktions teknik är det första steget innan du modellerar data. Dess roll i data vetenskaps processen [beskrivs här](./create-features.md). En _funktion_ är ett förutsägande attribut för modellen – till exempel temperatur, tryck, vibrationer och så vidare. För PdM involverar funktions teknikerna att sammanfatta en dators hälsa över historiska data som samlas in under en storleks tids period. I det här fallet skiljer den sig från sina motparter, t. ex. fjärrövervakning, avvikelse identifiering och fel identifiering. 

### <a name="time-windows"></a>Tids fönster
Fjärrövervakning innebär rapportering av händelser som inträffar vid _tidpunkter_. Avvikelse identifierings modeller utvärderar (score) inkommande data strömmar för att flagga avvikelser vid tidpunkter. Fel identifiering klassificerar fel att vara av olika typer när de inträffar tidpunkter. PdM omfattar däremot förutsägelser av problem under en _framtida tids period_, baserat på funktioner som representerar dator beteende under _historisk tids period_. För PdM är funktions data från enskilda tidpunkter för brus för att förutsäga. Därför måste data för varje funktion vara _smoothened_ genom att aggregera data punkter över tid Windows.

### <a name="lag-features"></a>Fördröjnings funktioner
Företags kraven definierar hur mycket modellen måste förutsäga i framtiden. I sin tur hjälper denna varaktighet att definiera hur långt tillbaka modellen måste se ut för att göra dessa förutsägelser. Den här perioden för att se bakåt kallas _fördröjningen_ och funktioner som har byggts över denna fördröjning kallas _fördröjnings funktioner_. I det här avsnittet beskrivs fördröjnings funktioner som kan konstrueras från data källor med tidsstämplar, och funktionen skapas från statiska data källor. Fördröjnings funktionerna är vanligt vis _numeriska_ i typ.

> [!IMPORTANT]
> Fönstrets storlek bestäms genom experimentering och bör slutföras med hjälp av en domän expert. Samma villkor gäller för urvalet och definitionen av fördröjnings funktioner, deras agg regeringar och typ av fönster.

#### <a name="rolling-aggregates"></a>Löpande mängder
För varje post i en till gång väljs ett rullande fönster med storleken "W" som antalet tidsenheter för att beräkna samlingarna. Fördröjnings funktionerna beräknas sedan med hjälp av W-perioder _före datumet_ för den posten. I bild 1 visar de blå linjerna de sensor värden som registrerats för en till gång för varje tidsenhet. De kännetecknar ett rullande medelvärde för funktions värden i ett fönster med storleken = 3. Det rullande medelvärdet beräknas över alla poster med tidsstämplar i intervallet t<sub>1</sub> (i orange) till t<sub>2</sub> (i grönt). Värdet för W är normalt på minuter eller timmar beroende på datans beskaffenhet. Men för vissa problem kan du välja ett stort W (t. ex. 12 månader) för att få hela historiken för en till gång fram till tiden för posten.

![Bild 1. Funktioner för rullande mängd](./media/predictive-maintenance-playbook/rolling-aggregate-features.png)

Bild 1. Funktioner för rullande mängd

Exempel på rullandea mängder över ett tidsfönster är Count, Average, CUMESUM (Kumulativ Sum) mått, min/max värden. Dessutom används ofta avvikelse, standard avvikelse och antalet avvikare än N standard avvikelser. Exempel på agg regeringar som kan användas för [användnings fall](#sample-pdm-use-cases) i den här hand boken visas nedan. 
- _Flyg fördröjning_: antal felkoder under den senaste dagen/veckan.
- _Del av flyg Plans motorn_: rullande medelvärde, standard avvikelse och Summa under den senaste dagen, veckan osv. Detta mått bör fastställas tillsammans med företags experten.
- _ATM-problem_: rullande medelvärden, median, intervall, standard avvikelser, antal avvikare utöver tre standard avvikelser, övre och nedre CUMESUM.
- _Subway träna dörr haverier_: antal händelser under den senaste dagen, veckan, två veckor osv.
- _Krets brytar haverier_: antal haverier under den senaste veckan, år, tre år osv.

En annan användbar teknik i PdM är att samla in trend ändringar, toppar och nivå ändringar med algoritmer som identifierar avvikelser i data.

#### <a name="tumbling-aggregates"></a>Rullande-mängder
För varje etikettad post för en till gång definieras ett fönster med storleken _w-<sub>k</sub>_ , där _k_ är antalet fönster i storlek _w_. Agg regeringar skapas sedan över _k_ _rullande Windows_ _W-k, w-<sub>(k-1)</sub>,..., w-<sub>2</sub>, w-<sub>1</sub>_ för perioderna före en posts tidstämpel. _k_ kan vara ett litet tal för att samla in kortsiktiga effekter eller ett stort antal för att samla in långsiktiga försämrings mönster. (se bild 2).

![Figur 2. Rullande mängd funktioner](./media/predictive-maintenance-playbook/tumbling-aggregate-features.png)

Figur 2. Rullande mängd funktioner

Till exempel kan fördröjnings funktioner för användnings fall av lindnings turbiner skapas med W = 1 och k = 3. De förväntar sig en fördröjning för var och en av de senaste tre månaderna med hjälp av topp-och Botteniga avvikare.

### <a name="static-features"></a>Statiska funktioner

Tekniska specifikationer för utrustning som datum för tillverkning, modell nummer, plats, är några exempel på statiska funktioner. De behandlas som _kategoriska_ -variabler för modellering. Några exempel på användnings fallet för krets brytare är spänning, ström kapacitet, transformator typ och ström källa. För hjul haverier är typ av Tire hjul (legering vs stål) ett exempel.

De data förberedelse ansträngningar som diskuteras hittills bör leda till att data ordnas enligt nedan. Inlärnings-, test-och verifierings data ska ha det här logiska schemat (det här exemplet visar tiden i antal dagar).

| Till gångs-ID | Tid | \<Feature Columns> | Etikett |
| ---- | ---- | --- | --- |
| A123 |Dag 1 | . . . | . |
| A123 |Dag 2 | . . . | . |
| ...  |...   | . . . | . |
| B234 |Dag 1 | . . . | . |
| B234 |Dag 2 | . . . | . |
| ...  |...   | . . . | . |

Det sista steget i funktions teknik är **märkningen** av mål variabeln. Den här processen är beroende av modellerings tekniken. Modellerings tekniken är i sin tur beroende av affärs problem och beskaffenheten hos tillgängliga data. Etiketteringen beskrivs i nästa avsnitt.

> [!IMPORTANT]
> Förberedelse av data och funktions teknik är lika viktiga som modellerings tekniker för att komma till lyckade PdM-lösningar. Domän experten och läkaren bör investera betydande tid för att komma till rätt funktioner och data för modellen. Ett litet exempel från många böcker om funktions teknik finns i listan nedan:
> - Pyle, D. data förberedelse för Data utvinning (Morgan Kaufmann-serien i Datahantering system), 1999
> - Zheng, A., Casari, A. funktions teknik för Machine Learning: principer och metoder för data tekniker, o ' Reilly, 2018.
> - Dong, G. LiU, H. (redigerare), funktions teknik för Machine Learning och data analys (Chapman & Hall/CRC-Data utvinning och identifierings serie för kunskap), CRC-tryck, 2018.

## <a name="modeling-techniques-for-predictive-maintenance"></a>Modellerings tekniker för förutsägande underhåll

I det här avsnittet beskrivs de huvudsakliga modellerings teknikerna för PdM-problem, tillsammans med deras egna metoder för etikett konstruktion. Observera att en enda modellerings teknik kan användas i olika branscher. Modellerings tekniken är länkad till data vetenskaps problemet, i stället för själva kontexten för de data som finns i handen.

> [!IMPORTANT]
> Val av etiketter för felen och märknings strategin  
> bör fastställas i samråd med domän experten.

### <a name="binary-classification"></a>Binära klassificering
Binära klassificering används för att _förutsäga sannolikheten för att en del av utrustningen kraschar inom en framtida tids period_ , kallad den _framtida Horisont perioden X_. X bestäms av affärs problemet och de data som finns till hands, i samråd med domän experten. Några exempel:
- _minsta ledtid_ som krävs för att ersätta komponenter, distribuera underhålls resurser, utföra underhåll för att undvika ett problem som sannolikt inträffar under den perioden.
- _minsta antal händelser_ som kan inträffa innan ett problem uppstår.

I den här tekniken identifieras två typer av inlärnings exempel. Ett positivt exempel _som indikerar ett haveri_ med etiketten = 1. Ett negativt exempel som anger normal drift, med etikett = 0. Mål variabeln, och därför är etikett värden _kategoriska_. Modellen ska identifiera varje nytt exempel som troligt vis kraschar eller fungerar normalt under nästa X tidsenhet.

#### <a name="label-construction-for-binary-classification"></a>Etikett konstruktion för binär klassificering
Frågan här är: "Vad är sannolikheten för att till gången kommer att Miss inaktive ras under de närmaste X-tidsenheterna?" För att svara på den här frågan, etiketter X poster innan en till gång har misslyckats som "om att Miss lyckas" (etikett = 1) och Märk alla andra poster som "normal" (etikett = 0). (se bild 3).

![Bild 3. Etiketter för binär klassificering](./media/predictive-maintenance-playbook/labelling-for-binary-classification.png)

Bild 3. Etiketter för binär klassificering

Exempel på märknings strategi för vissa av användnings fallen visas nedan.
- _Fördröjningar i flygning_: X kan väljas som en dag för att förutsäga fördröjningar under de närmaste 24 timmarna. Alla flygningar som ligger inom 24 timmar före felen märks som 1.
- _Problem med ATM-likviditeten_: ett mål kan vara att fastställa antalet lyckade försök för en transaktion inom en timme. I så fall är alla transaktioner som skedde under den senaste timmen av felen märkta som 1. Om du vill förutsäga felsannolikheten under nästa N valuta noteringar är alla anteckningar i de sista N anteckningarna märkta som 1.
- _Krets brytar haverier_: målet kan vara att förutsäga nästa krets brytar kommando haveri. I så fall väljs X som ett framtida kommando.
- _Träna dörr haverier_: X kan väljas som två dagar.
- _Lindnings turbin-problem_: X kan väljas som två månader.

### <a name="regression-for-predictive-maintenance"></a>Regression för förutsägande underhåll
Regressions modeller används för att _Beräkna den återstående användbara livs längden (RUL) för en till gång_. RUL definieras som den tids period som en till gång fungerar innan nästa fel inträffar. Varje övnings exempel är en post som tillhör en tidsenhet _ny_ för en till gång, där _n_ är multipel. Modellen ska beräkna RUL för varje nytt exempel som ett _kontinuerligt tal_. Det här talet anger den tids period som återstår före felet.

#### <a name="label-construction-for-regression"></a>Etikett konstruktion för regression
Frågan här är: "Vad är den återstående användbara livs längden (RUL) av utrustningen?" För varje post före felen beräknar du att etiketten ska vara antalet enheter som återstår före nästa haveri. I den här metoden är etiketter kontinuerliga variabler. (Se bild 4)

![Bild 4. Etiketter för regression](./media/predictive-maintenance-playbook/labelling-for-regression.png)

Bild 4. Etiketter för regression

För regression görs märkning med referens till en felpunkt. Beräkningen är inte möjlig utan att du vet hur länge till gången har överleva innan ett fel. Till skillnad från binära klassificeringar kan till gångar utan eventuella problem i data inte användas för modellering. Det här problemet beskrivs bäst av en annan statistisk teknik som kallas [överlevnads analys](https://en.wikipedia.org/wiki/Survival_analysis). Men potentiella komplikationer kan uppstå när du tillämpar den här metoden på PdM användnings fall som innefattar tidsvarierande data med frekventa intervall. Mer information om överlevnads analys finns i [den här One-pager](https://www.cscu.cornell.edu/news/news.php/stnews78.pdf).

### <a name="multi-class-classification-for-predictive-maintenance"></a>Klassificering i flera klasser för förutsägande underhåll
Klassificerings tekniker med flera klasser kan användas i PdM-lösningar för två scenarier:
- Förutsäg _två framtida_ resultat: det första resultatet är ett tidsintervall _som inte_ kan utföras för en till gång. Till gången tilldelas till en av flera möjliga tids perioder. Det andra resultatet är sannolikheten för fel i en framtida period på grund av _en av flera Rotors_ Aker. Den här förutsägelsen gör det möjligt för underhålls personalen att titta efter symptom och planera underhålls scheman.
- Förutsäg _den mest sannolika rotor saken_ till ett angivet fel. Det här resultatet rekommenderar rätt uppsättning underhålls åtgärder för att åtgärda ett haveri. En rangordnad lista över rotor orsaker och rekommenderade reparationer kan hjälpa tekniker att prioritera sina reparations åtgärder efter ett fel.

#### <a name="label-construction-for-multi-class-classification"></a>Etikett konstruktion för klassificering med flera klasser
Frågan här är: "Vad är sannolikheten att en till gång kommer att Miss Miss i nästa _NZ_ -tidsenhet där _n_ är antalet perioder?" Om du vill besvara den här frågan kan du märka nZ-poster före felet på en till gång med hjälp av tids perioderna (3Z, 2Z, Z). Märk alla andra poster som "normal" (etikett = 0). I den här metoden innehåller Target-variabeln _kategoriska_ -värden. (Se bild 5).

![Bild 5. Tids förutsägelse etiketter för klassificering av multiklass](./media/predictive-maintenance-playbook/labelling-for-multiclass-classification-for-failure-time-prediction.png)

Bild 5. Etiketter för klassificering av flera klasser för tids förutsägelse för krasch

Frågan här är: "Vad är sannolikheten för att till gången kommer att Miss förfaller inom de närmaste X tidsintervallen på grund av rotor saken/problem _P <sub>i</sub>_?" där _jag_ är antalet möjliga rotor orsaker. För att svara på den här frågan, etiketter X poster innan en till gång har misslyckats som "om att Miss lyckas på grund av rotor saken _P <sub>i</sub>_" (etikett = _P <sub>i</sub>_). Märk alla andra poster som "normal" (etikett = 0). I den här metoden är etiketter också kategoriska (se bild 6).

![Bild 6. Rotor Saks förutsägelse etiketter för klassificering av multiklass](./media/predictive-maintenance-playbook/labelling-for-multiclass-classification-for-root-cause-prediction.png)

Bild 6. Etiketter för klassificering med flera klasser för rotor Saks förutsägelse

Modellen tilldelar en fel sannolikhet på grund av varje _P <sub>i</sub>_ och sannolikheten att inget fel uppstår. Dessa sannolikheter kan sorteras efter omfattning för att kunna förutsäga de problem som troligen inträffar i framtiden.

Frågan här är: "vilka underhålls åtgärder rekommenderar du efter ett haveri?" För att svara på den här frågan _kräver inte etiketter att en framtida Horisont plockas_, eftersom modellen inte förutsäger felet i framtiden. Det förutsäger bara den mest sannolika rotor saken _när felet redan har inträffat_.

## <a name="training-validation-and-testing-methods-for-predictive-maintenance"></a>Metoder för utbildning, validering och testning för förutsägande underhåll
[Team data science-processen](./overview.md) ger fullständig täckning av modell träna-test-validate-cykeln. I det här avsnittet beskrivs aspekter som är unika för PdM.

### <a name="cross-validation"></a>Kors validering
Målet med [kors validering](https://en.wikipedia.org/wiki/Cross-validation_(statistics)) är att definiera en data uppsättning för att "testa" modellen i övnings fasen. Den här data uppsättningen kallas för _verifierings uppsättningen_. Den här tekniken hjälper till att begränsa problem som _övermontering_ och ger en inblick i hur modellen generaliserar till en oberoende data uppsättning. Det vill säga en okänd data uppsättning som kan vara från ett verkligt problem. Inlärnings-och test rutinen för PdM måste ta hänsyn till hur lång tid det tar att göra mer generalize på osett framtida data.

Många Machine Learning-algoritmer är beroende av ett antal grundparametrar som kan ändra modell prestanda avsevärt. De optimala värdena för dessa egenskaper beräknas inte automatiskt när du tränar modellen. De bör anges av data expert. Det finns flera sätt att hitta lämpliga värden för sina egenskaper.

Det vanligaste är en _n-vik kors validering_ som delar upp exemplen slumpmässigt i _n_ -vik. För varje uppsättning med biparameter värden kör du Learning-algoritmen _k_ gånger. Vid varje iteration använder du exemplen i den aktuella vikningen som en validerings uppsättning och resten av exemplen som en inlärnings uppsättning. Träna algoritmen över utbildnings exempel och beräkna prestanda måtten för verifierings exempel. I slutet av den här slingan beräknar du medelvärdet av _k_ prestanda mått. För varje uppsättning med egenskaper för båda värdena väljer du de som har bästa möjliga genomsnitts prestanda. Uppgiften att välja grundparametrar är ofta experimentellt.

I PdM-problem registreras data som en tids serie händelser som kommer från flera data källor. Dessa poster kan ordnas enligt tiden för märkning. Om data uppsättningen däremot är uppdelad _slumpmässigt_ i utbildning och validerings uppsättning, _kan några av inlärnings exemplen gå senare i tid än några av verifierings exemplen_. Framtida prestanda för de flesta parameter värden beräknas utifrån vissa data som anlänt _innan_ modellen tränades. Dessa uppskattningar kan vara alltför optimistiskt, särskilt om tids serien inte är stationär och utvecklas över tid. Därför kan de valda värdena för den här parametern vara under optimala.

Det rekommenderade sättet är att dela upp exemplen i utbildning och validerings uppsättning i ett _tidsberoende_ sätt, där alla verifierings exempel är senare i tid än alla inlärnings exempel. För varje uppsättning av biparameter värden tränar du algoritmen över tränings data uppsättningen. Mät modellens prestanda över samma validerings uppsättning. Välj de egenskaper för en parameter som visar bästa prestanda. De egenskaper för båda parametrarna som valts av funktionen träna/verifiera delning resulterar i bättre prestanda för modeller än med de värden som valts slumpmässigt vid kors validering.

Den slutliga modellen kan genereras genom att träna en Learning-algoritm över hela tränings data med hjälp av de bästa värdena i den här parametern.

### <a name="testing-for-model-performance"></a>Testa modell prestanda
När en modell har skapats krävs en uppskattning av dess framtida prestanda för nya data. En klar uppskattning är prestanda måttet för de värden som beräknas över validerings uppsättningen, eller ett genomsnittligt prestanda mått som beräknas från kors validering. Dessa uppskattningar är ofta alltför optimistiskt. Företaget kanske ofta har ytterligare rikt linjer för hur man vill testa modellen.

Det rekommenderade sättet för PdM är att dela upp exemplen i utbildning, verifiering och test av data uppsättningar på ett _tids beroende_ sätt. Alla testexempel bör vara senare i tid än alla inlärnings-och verifierings exempel. Efter delningen genererar du modellen och mäter dess prestanda enligt beskrivningen ovan.

När Time-serien är stationär och lätt att Förutsäg, genererar både slumpmässiga och tids beroende metoder liknande uppskattningar av framtida prestanda. Men när tids serier är icke-Station ära och/eller svåra att förutsäga genererar tids beroende metoden en mer realistisk uppskattning av framtida prestanda.

### <a name="time-dependent-split"></a>Tids beroende delning
Det här avsnittet beskriver metod tips för att implementera tids beroende delning. En tids beroende dubbelriktad uppdelning mellan utbildning och test uppsättningar beskrivs nedan.

Anta en data ström med tidsstämplade händelser, till exempel mätningar från olika sensorer. Definiera funktioner och etiketter för utbildnings-och test exempel över tids ramar som innehåller flera händelser. Till exempel, för binär klassificering, skapa funktioner baserade på tidigare händelser och skapa etiketter baserade på framtida händelser inom "X"-enheter i framtiden (se avsnitten om teknik för [teknik](#feature-engineering) och modellering). Därför kommer tids ramen för etiketten i ett exempel att visas senare än tids ramen för dess funktioner.

För tidsbaserad delning väljer du en _tränings period, T <sub>c</sub>_ , för att träna en modell, med grundparametrar som är justerade med historiska data upp till T <sub>c</sub>. För att förhindra läckage av framtida etiketter som ligger utanför T<sub>c</sub> i tränings data, väljer du den senaste tiden för att ge träna exempel till X-enheter före T<sub>c</sub>. I exemplet som visas i bild 7 representerar varje kvadrat en post i data uppsättningen där funktioner och etiketter beräknas enligt beskrivningen ovan. Bilden visar de poster som ska ingå i utbildnings-och test uppsättningar för X = 2 och W = 3:

![Bild 7. Tids beroende delning för binära klassificering](./media/predictive-maintenance-playbook/time-dependent-split-for-binary-classification.png)

Bild 7. Tids beroende delning för binära klassificering

De gröna fyr kanterna representerar poster som tillhör de tidsenheter som kan användas för utbildning. Varje övnings exempel genereras genom att ta hänsyn till de tre senaste perioderna för att skapa funktioner och två framtida perioder för etiketter före T. ex.<sub>c</sub>. När någon del av de två framtida perioderna är mer än T<sub>c</sub>, utesluter du det exemplet från träning-datauppsättningen eftersom ingen synlighet antas utöver T<sub>c</sub>.

De svarta fyr kanterna representerar posterna i den slutliga märkta data uppsättningen som inte ska användas i tränings data uppsättningen, med ovanstående begränsning. Dessa poster används inte heller för att testa data, eftersom de är före T<sub>c</sub>. Dessutom beror deras tidsintervaller för deras märkning delvis på tids ramen för utbildningen, vilket inte är idealiskt. Utbildnings-och test data bör ha separata tids ramar för etiketter för att förhindra läckage av etikett information.

Den teknik som diskuteras så att den kan överlappa varandra mellan utbildning och testning av exempel som har tidsstämplar nära T<sub>c</sub>. En lösning för att uppnå större separering är att utesluta exempel som ligger inom W Time-enheter om T<sub>c</sub> från test uppsättningen. Men sådan aggressiva delning beror på gott data tillgänglighet.

Regressions modeller som används för att förutsäga RUL är mer kraftigt påverkade av problemets läckage. Att använda den slumpmässiga delnings metoden leder till extrema överanpassning. Vid Regressions problem bör delningen vara sådan att de poster som hör till till gångar med fel<sub>innan T.</sub> ex. går till inlärnings uppsättningen. Poster med till gångar som har misslyckats efter att ha gått till test uppsättningen.

En annan metod för att dela data för utbildning och testning är att använda en uppdelning efter till gångs-ID. Delningen bör vara sådan att ingen av de till gångar som används i inlärnings uppsättningen används för att testa modellens prestanda. Med den här metoden har en modell en bättre chans att tillhandahålla mer realistiska resultat med nya till gångar.

### <a name="handling-imbalanced-data"></a>Hantera data som är balanserade
I klassificerings problem, om det finns fler exempel på en klass än de andra, så anses data uppsättningen vara _obalanserad_. Det bästa är att tillräckligt många företrädare för varje klass i tränings data är önskade för att möjliggöra differentiering mellan olika klasser. Om en klass är mindre än 10% av data anses data vara balanserade. Den underrefererade klassen kallas en _minoritets klass_.

Många PdM problem möter sådana data uppsättningar som är balanserade, där en klass är kraftigt underordnad jämfört med den andra klassen eller klasser. I vissa situationer kan minoritets klassen endast utgöra 0,001% av de totala data punkterna. Klass obalans är inte unikt för PdM. Andra domäner där fel och avvikelser inträffar sällsynt inträffar som ett liknande problem, till exempel, bedrägeri identifiering och intrång i nätverket. De här felen utgör exempel på minoritets klass.

Prestanda för de flesta standardalgoritmer för inlärning är komprometterade i klass data, eftersom de kan minimera den övergripande fel frekvensen. För en data uppsättning med 99% negativa och 1% positiva exempel kan en modell visas med 99% noggrannhet genom att etikettera alla instanser som negativa. Men modellen kommer att underklassificera alla positiva exempel. så även om dess noggrannhet är hög, är algoritmen inte en användbar. Därför räcker det inte med konventionella utvärderings mått, till exempel _den övergripande precisionen för fel frekvens_ för obalanserad inlärning. När de är riktade mot obalanserade data uppsättningar används andra mått för utvärdering av modeller:
- Precision
- Recall
- F1-Poäng
- Kostnads justerade ROC (egenskaper för mottagar drift)

Mer information om dessa mått finns i [modell utvärdering](#model-evaluation).

Det finns dock några metoder som hjälper till att lösa problem med klassen obalans. De två viktigaste är _samplings tekniker_ och _kostnads känslig inlärning_.

#### <a name="sampling-methods"></a>Samplings metoder
I balans kurser ingår användning av provtagnings metoder för att ändra tränings data uppsättningen till en bal anse rad data uppsättning. Det går inte att använda samplings metoder i test uppsättningen. Även om det finns flera samplings tekniker är de flesta enkla vidarebefordran _slumpmässiga översamplingar_ och _under sampling_.

_Slumpmässig översampling_ innebär att du väljer ett slumpmässigt exempel från en minoritets klass, som replikerar de här exemplen och lägger till dem i tränings data uppsättning. Det innebär att antalet exempel i en minoritets klass ökar, och kan till och med utjämna antalet exempel på olika klasser. En nackdel med översampling är att flera instanser av vissa exempel kan orsaka att klassificeraren blir för specifik, vilket leder till överanpassning. Modellen kan visa hög inlärnings precision, men prestandan för osett test data kan vara sämre.

_Slumpmässigt under samplingen_ är att välja ett slumpmässigt exempel från en majoritets klass och ta bort dessa exempel från tränings data uppsättning. Att ta bort exempel från majoritets klass kan dock orsaka att klassificeraren saknar viktiga begrepp som rör majoriteten av klassen. _Hybrid sampling_ där minoritets klass är över-sampel och majoritets klass är undersamplad på samma gång som en annan praktisk metod.

Det finns många avancerade samplings tekniker. Vilken teknik som väljs beror på data egenskaperna och resultaten av upprepade experiment med data expert.

#### <a name="cost-sensitive-learning"></a>Kostnads känslig inlärning
I PdM är felen som utgör minoritets klassen mer intressanta än vanliga exempel. Fokus ligger i huvudsak på algoritmens prestanda vid haverier. Felaktig förutsägelse av en positiv klass som en negativ klass kan kosta mer än vice versa. Den här situationen kallas ofta för ojämn förlust eller asymmetrisk kostnad för att klassificera element i olika klasser. Den perfekta klassificeraren bör ge hög förutsägelse noggrannhet över minoritets klassen, utan att kompromissa med noggrannheten för majoriteten av klassen.

Det finns flera sätt att åstadkomma saldot. För att minska risken för ojämn förlust, tilldelar du en hög kostnad till mis-klassificering av klassen minoritet och försöker minimera den totala kostnaden. Algoritmer som _SVMs (support Vector Machines)_ använder den här metoden i sin egen metod, genom att tillåta kostnad för positiva och negativa exempel som anges under utbildningen. På samma sätt visar de metoder som _utökat besluts träd_ vanligt vis prestanda med obalanserade data.

## <a name="model-evaluation"></a>Modellutvärdering
MIS-klassificering är ett betydande problem för PdM-scenarier där kostnaden för falska larm till verksamheten är hög. Till exempel kan ett beslut om flyg plan som baseras på en felaktig förutsägelse av motor fel störa scheman och rese planer. Att ta en dator offline från en sammansättnings rad kan leda till förlust av intäkter. Därför är modell utvärderingen med rätt prestanda mått mot nya test data kritiska.

Typiska prestanda mått som används för att utvärdera PdM-modeller beskrivs nedan:

- [Noggrannhet](https://en.wikipedia.org/wiki/Accuracy_and_precision) är det mest populära måttet som används för att beskriva en klassificerares prestanda. Men precisionen är känslig för data distributioner och är ett ineffektivt mått för scenarier med sambalanserade data uppsättningar. Andra mått används i stället. Verktyg som till exempel en [förvirrings mat ris](https://en.wikipedia.org/wiki/Confusion_matrix) används för att beräkna och motivera modellens noggrannhet.
- [Precisionen](https://en.wikipedia.org/wiki/Precision_and_recall) för PdM-modeller avser antalet falska larm. Den lägre precisionen för modellen motsvarar vanligt vis en högre hastighet av falska larm.
- [Åter kallelse](https://en.wikipedia.org/wiki/Precision_and_recall) frekvens anger hur många av felen i test uppsättningen som identifierades korrekt av modellen. Högre åter kallelse frekvenser innebär att modellen lyckas identifiera de sanna felen.
- [F1 Poäng](https://en.wikipedia.org/wiki/F1_score) är det harmoniska medelvärdet av precision och återkallande, med värdet mellan 0 (sämst) och 1 (bästa).

För binära klassificering,
- [Mottagar drifts kurvor (Roc)](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) är också ett populärt mått. I ROC-kurvor tolkas modell prestanda baserat på en fast drifts punkt på ROC.
- Men för PdM-problem är _decile-tabeller_ och lyft- _diagram_ mer informativa. De fokuserar bara på den positiva klassen (haverier) och ger en mer komplex bild av algoritmens prestanda än ROC kurvor.
  - _Decile-tabeller_ skapas med hjälp av test exempel i en fallande ordning av felsannolikheter. De beställda exemplen grupperas sedan i deciles (10% av exemplen med högsta sannolikhet, sedan 20%, 30% och så vidare). Förhållandet (sant positiv hastighet)/(slumpmässig bas linje) för varje decile hjälper till att beräkna algoritmens prestanda vid varje decile. Den slumpmässiga bas linjen tar på värdena 0,1, 0,2 och så vidare.
  - [Lyft diagram](http://www2.cs.uregina.ca/~dbd/cs831/notes/lift_chart/lift_chart.html) ritar decile sanna positiva positiva frekvens mot slumpmässig sann positiv kostnad för alla deciles. De första deciles är vanligt vis fokus på resultaten, eftersom de visar störst vinster. Den första deciles kan också ses som representativ för "i risk", när den används för PdM.

## <a name="model-operationalization-for-predictive-maintenance"></a>Modell-driftsättning för förutsägande underhåll

Fördelen med att data vetenskaps övningen endast realiseras när den tränade modellen görs drift. Det vill säga att modellen måste distribueras i affärs systemen för att göra förutsägelser baserade på nya tidigare osett data.  De nya data måste exakt följa _modellens signatur_ för den tränade modellen på två sätt:
- alla funktioner måste finnas i varje logisk instans (t. ex. en rad i en tabell) med nya data.
- nya data måste förbehandlas och var och en av de funktioner som har utformats på exakt samma sätt som tränings data.

Ovanstående process anges på många sätt i akademiska och bransch litteratur. Men alla följande uttryck betyder samma sak:
- _Poäng för nya data_ med modellen
- _Använd modellen_ för nya data
- _Operationalisera_ modellen
- _Distribuera_ modellen
- _Kör modellen_ mot nya data

Som tidigare nämnts skiljer sig modell-driftsättning för PdM från sina peer-datorer. Scenarier som rör avvikelse identifiering och fel identifiering implementerar vanligt vis _online_ -bedömning (även kallat _real tids poängsättning_). Här _visar modellen varje_ inkommande post och returnerar en förutsägelse. Vid avvikelse identifiering är förutsägelsen en indikation på att en avvikelse har uppstått (exempel: en klass SVM). Vid identifiering av identifierings problem skulle det vara typen eller klassen av felen.

PdM omfattar däremot batch- _Poäng_. För att följa modell signaturen måste funktionerna i de nya data vara utformade på samma sätt som tränings data. För stora data uppsättningar som är typiska för nya data sammanställs funktionerna över tiden Windows och poängen i batch. Batch-bedömning sker vanligt vis i distribuerade system som [Spark](https://spark.apache.org/) eller [Azure Batch](../../batch/batch-service-workflow-features.md). Det finns ett par alternativ – både under optimalt:
- Strömnings data motorer stöder agg regering över Windows i minnet. Det kan därför hävdas att de stöder direkt poäng. Men dessa system är lämpliga för att optimera data i smala fönster, eller sparse-element över bredare Windows. De kan inte skalas bra för att optimera data över bredare tidsfönster, som visas i PdM-scenarier.
- Om det inte finns någon batch-bedömning, är lösningen att anpassa online-poängen för att hantera nya data i små batchar i taget.

## <a name="solution-templates-for-predictive-maintenance"></a>Solution-mallar för förutsägande underhåll

Det sista avsnittet i den här hand boken innehåller en lista över PdM, självstudier och experiment som implementerats i Azure. Dessa PdM-program kan distribueras till en Azure-prenumeration inom några minuter i vissa fall. De kan användas som proof-of-Concept-demonstrationer, sand BOXR för att experimentera med alternativ eller acceleratorer för faktiska produktions implementeringar. Dessa mallar finns i [Azure AI Gallery](https://gallery.azure.ai) -eller [Azure-GitHub](https://github.com/Azure). Dessa olika exempel kommer att samlas in i den här lösnings mal len över tid.

| # | Rubrik | Beskrivning |
|--:|:------|-------------|
| 2 | [Mall för lösningar för förebyggande underhåll i Azure](https://github.com/Azure/AI-PredictiveMaintenance) | En lösnings mall med öppen källkod som demonstrerar Azure ML-modellering och en fullständig Azure-infrastruktur som kan stödja förebyggande underhålls scenarier i samband med IoT-fjärrövervakning. |
| 3 | [Djupinlärning för förutsägande underhåll](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance) | Azure Notebook med en demonstrations lösning för att använda LSTM-nätverk (Long Short-Term-minne) (en klass av aktuella neurala-nätverk) för förutsägande underhåll, med ett [blogg inlägg i det här exemplet](https://azure.microsoft.com/blog/deep-learning-for-predictive-maintenance).|
| 4 | [Förebyggande underhåll i Azure för Aerospace](https://gallery.azure.ai/Solution/Predictive-Maintenance-for-Aerospace-1) | En av de första PdM-mallarna baserat på Azure ML v 1.0 för underhåll av flyg plan. Den här guiden kommer från det här projektet. |
| 5 | [Azure AI Toolkit för IoT Edge](https://github.com/Azure/ai-toolkit-iot-edge) | AI i IoT Edge med TensorFlow; Toolkit-paket djup inlärnings modeller i Azure IoT Edge-kompatibla Docker-behållare och exponera dessa modeller som REST-API: er.
| 6 | [Förutsägande underhåll av Azure IoT](https://github.com/Azure/azure-iot-predictive-maintenance) | Azure IoT Suite datorer – förkonfigurerad lösning. PdM-mall för hantering av flyg plan med IoT Suite. [Ett annat dokument och en](/previous-versions/azure/iot-accelerators/about-iot-accelerators) [genom gång](/previous-versions/azure/iot-accelerators/iot-accelerators-predictive-walkthrough) som är relaterad till samma projekt. |
| 7 | [Mall för förutsägelse underhåll med SQL Server R Services](https://gallery.azure.ai/Tutorial/Predictive-Maintenance-Template-with-SQL-Server-R-Services-1) | Demo av återstående användbart livs cykel scenario baserat på R Services. |
| 8 | [Modellerings guide för förebyggande underhåll](https://gallery.azure.ai/Collection/Predictive-Maintenance-Modelling-Guide-1) | Data uppsättnings funktionen för flyg Plans underhåll som utformats med R med [experiment](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Experiment-1) och [data uppsättningar](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Data-Sets-1) och Azure Notebook och [experiment](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2) i azureml v 1.0|

## <a name="training-resources-for-predictive-maintenance"></a>Utbildnings resurser för förutsägande underhåll

Microsoft Azure erbjuder utbildnings vägar för grundläggande koncept bakom PdM-tekniker, förutom innehåll och utbildning för allmänna AI-koncept och praxis.

| Utbildnings resurs  | Tillgänglighet |
|:-------------------|--------------|
| [Utbildnings väg för PdM med träd och slumpmässig skog](https://aischool.microsoft.com/learning-paths/1H5vH5wAYcAy88CoQWQcA8) | Offentliga | 
| [Utbildnings väg för PdM med djup inlärning](https://aischool.microsoft.com/learning-paths/FSIXxYkOGcauo0eUO8qAS) | Offentliga |
| [AI-utvecklare på Azure](https://azure.microsoft.com/training/learning-paths/azure-ai-developer) | Offentliga |
| [Microsoft AI-skola](https://aischool.microsoft.com/learning-paths) | Offentliga |
| [Azure AI-utbildning från GitHub](https://github.com/Azure/connectthedots/blob/master/readme.md) | Offentliga |
| [LinkedIn Learning](https://www.linkedin.com/learning) | Offentliga |
| [Microsoft AI YouTube-webb seminarier](https://www.youtube.com/watch?v=NvrH7_KKzoM&t=4s) | Offentliga |
| [Microsoft AI show](https://channel9.msdn.com/Shows/AI-Show) | Offentliga |
| [LearnAI@MS](https://learnanalytics.microsoft.com) | Partner |
| [Microsoft Partner Network](https://partner.microsoft.com/training/training-center) | Partner |

Dessutom erbjuds kostnads fria MOOC (enorma öppna onlinekurser) på AI online av akademiska institutioner som Stanfords och MIT, och andra utbildnings företag.