---
title: Defender för IoT-ordlista
description: Den här ord listan innehåller en kort beskrivning av viktiga Defender för IoT-plattformens villkor och begrepp.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/09/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: 7896237b1f9e9e66659532422efb3449c41ede11
ms.sourcegitcommit: 5ef018fdadd854c8a3c360743245c44d306e470d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/01/2021
ms.locfileid: "97845290"
---
# <a name="defender-for-iot-glossary"></a>Defender för IoT-ordlista

Den här ord listan innehåller en kort beskrivning av viktiga termer och begrepp för Azure Defender för IoT-plattformen. Välj länken **Läs mer** om du vill gå till närliggande termer i ord listan. Detta hjälper dig att snabbt lära dig och använda produkt verktyg.

## <a name="a"></a>A

| Period | Beskrivning | Läs mer |
|--|--|--|
| **åtkomst grupp** | Stöd åtkomst krav för användare av stora organisationer genom att skapa åtkomst grupps regler.<br /><br />Med regler kan du kontrol lera vy-och konfigurations åtkomst till den lokala hanterings konsolen Defender för IoT för specifika användar roller i relevanta affär senheter, regioner, platser och zoner.<br /><br />Du kan till exempel tillåta säkerhets analyser från en Active Directory grupp för att komma åt västra Europeiska bil data, men förhindra åtkomst till data i Afrika. | **lokal hanterings konsol – <br /> <br /> affär senhet** |
| **åtkomsttoken** | Generera åtkomsttoken för att komma åt Defender för IoT-REST API. | **API** |
| **Bekräfta aviserings händelse** | Instruera Defender for IoT att dölja aviseringen en gång för den identifierade händelsen. Aviseringen utlöses igen om händelsen upptäcks igen. | **varnings meddelande om aviserings <br /> <br /> händelse <br /> <br /> av varnings händelse** |
| **varningar** | Ett meddelande om att en Defender för IoT-motor utlöser avvikelser från auktoriserat nätverks beteende, nätverks avvikelser eller misstänkt nätverks aktivitet och trafik. | **<br /> <br /> undantags regel <br /> <br /> system meddelanden för vidarebefordrings regel** |
| **aviserings kommentar** | Kommentarer som säkerhetsanalytiker och administratörer gör i aviserings meddelanden. En aviserings kommentar kan till exempel ge instruktioner om åtgärder som kan vidtas eller namn på personer som ska kontaktas angående händelsen.<br /><br />Användare som visar aviseringar kan välja den eller de kommentarer som bäst motsvarar händelse status eller steg som vidtagits för att undersöka aviseringen. | **varningar** |
| **avvikelse motor** | En Defender för IoT-motor som identifierar ovanlig kommunikation mellan maskin-till-dator (M2M) och beteende. Motorn kan till exempel upptäcka överdriven SMB-inloggnings försök. Avvikelse aviseringar utlöses när dessa händelser identifieras. | **Defender för IoT-motorer** |
| **API** | Tillåter externa system att komma åt data som identifieras av Defender för IoT och utföra åtgärder med hjälp av den externa REST API över SSL-anslutningar. | **åtkomsttoken** |
| **angrepps vektor rapport** | En grafisk representation i real tid av sårbarhets kedjor i utnyttjade slut punkter.<br /><br />Med rapporter kan du utvärdera effekterna av minsknings aktiviteter i angrepps ordningen för att fastställa. Du kan till exempel utvärdera om en system uppgradering stör angriparens sökväg genom att bryta angrepps kedjan eller om en alternativ attack Sök väg är kvar. Detta prioriterar reparations-och minsknings aktiviteter. | **riskbedömnings rapport** |

## <a name="b"></a>B

| Period | Beskrivning | Läs mer |
|--|--|--|
| **affär senhet** | En logisk organisation av ditt företag enligt specifika branscher.<br /><br />Ett globalt företag som innehåller glas fabriker och plast fabriker kan till exempel hanteras som två olika affär senheter. Du kan kontrol lera åtkomsten till Defender för IoT-användare till specifika affär senheter. | **<br /> <br /> <br /> <br /> plats <br /> <br /> zon för den lokala hanterings konsolens åtkomst grupp** |
| **planen** | Godkänd nätverks trafik, protokoll, kommandon och enheter. Defender för IoT identifierar avvikelser från nätverks bas linjen. Visa godkänd bas linje trafik genom att generera data utvinnings rapporter. | **<br /> <br /> inlärnings läge för Data utvinning** |

## <a name="c"></a>C

| Period | Beskrivning | Läs mer |
|--|--|--|
| **lokal hanterings konsol** | Den lokala hanterings konsolen innehåller en centraliserad vy och hantering av enheter och hot som Defender för distributioner av IoT-sensorer identifierar i din organisation. | **Defender för IoT Platform- <br /> <br /> sensor** |
| **CLI-kommandon** | CLI-alternativ (kommando rads gränssnitt) för Defender för IoT Administrator-användare. CLI-kommandon är tillgängliga för funktioner som inte går att komma åt från Defender för IoT-konsoler. | - |


## <a name="d"></a>D

| Period | Beskrivning | Läs mer |
|--|--|--|
| **Data utvinning** | Generera omfattande och detaljerade rapporter om dina nätverks enheter:<br /><br />- **SOC incident svar**: rapporter i real tid för att hjälpa dig att hantera omedelbara incident åtgärder. En rapport kan till exempel Visa en lista över enheter som kan behöva korrigeras.<br /><br />- **data utredning**: rapporter som baseras på historiska data för utrednings rapporter.<br /><br />- **IT-nätverks integritet**: rapporter som hjälper till att förbättra den övergripande nätverks säkerheten. En rapport kan till exempel Visa en lista över enheter med svaga autentiseringsuppgifter.<br /><br />- **synlighet**: rapporter som beskriver alla sökobjekt för att visa alla bas linje parametrar i nätverket.<br /><br />Spara data utvinnings rapporter för skrivskyddade användare att visa. | **bas linje <br /> <br /> rapporter** |
| **Defender för IoT-motorer** | Självstudiens analys motorer i Defender för IoT eliminerar behovet av att uppdatera signaturer eller definiera regler. Motorerna använder ICS-baserad beteende analys och data vetenskap för att kontinuerligt analysera nätverks trafik med avvikelser, skadlig kod, drift problem, protokoll överträdelser och avvikelser från bas linje nätverks aktivitet.<br /><br />När en motor identifierar en avvikelse utlöses en avisering. Aviseringar kan visas och hanteras från **aviserings** skärmen eller från en Siem. | **varningar** |
| **Defender för IoT-plattform** | Defender for IoT-lösningen som är installerad på Defender för IoT-sensorer och den lokala hanterings konsolen. | **sensor <br /> <br /> för lokal hanterings konsol** |
| **enhets karta** | En grafisk representation av nätverks enheter som Defender för IoT identifierar. Det visar anslutningarna mellan enheter och information om varje enhet. Använd kartan för att:<br /><br />– Hämta och kontrol lera viktig enhets information.<br /><br />– Analysera nätverks segment.<br /><br />– Exportera enhets information och sammanfattningar. | **Purdue-skikt grupp** |
| **enhets inventering-sensor** | Enhets inventeringen visar ett stort antal enhets attribut som identifieras av Defender för IoT. Alternativen är tillgängliga för att:<br /><br />– Filtrera visad information.<br /><br />– Exportera den här informationen till en CSV-fil.<br /><br />– Importera information om Windows-registret. | **Gruppera enhets inventering – lokal hanterings konsol** |
| **enhets inventering – lokal hanterings konsol** | Enhets information från anslutna sensorer kan visas från den lokala hanterings konsolen i enhets inventeringen. Detta ger användare av den lokala hanterings konsolen en omfattande vy över all nätverksinformation. | **enhets inventering-sensor <br /> <br /> enhets inventering-Data Integrator** |
| **enhets inventering-Data Integrator** | Med data integrerings funktionerna i den lokala hanterings konsolen kan du förbättra data i enhets inventeringen med information från andra företags resurser. Exempel på resurser är CMDBs, DNS, brand väggar och webb-API: er. | **enhets inventering – lokal hanterings konsol** |

## <a name="e"></a>E

| Period | Beskrivning | Läs mer |
|--|--|--|
| **Enterprise-vy** | En global karta som visar affär senheter, webbplatser och zoner där Defender för IoT-sensorer är installerade. Visa geografiska platser för skadliga varningar, operativa aviseringar med mera. | **<br /> <br /> plats <br /> <br /> zon för affär senhet** |
| **händelse tids linje** | En tids linje för aktivitet som har identifierats i nätverket, inklusive:<br /><br />-Aviseringar utlösta.<br /><br />– Nätverks händelser (information).<br /><br />– Användar åtgärder, till exempel inloggning, borttagning av användare och skapande av användare, samt aviserings hanterings åtgärder, till exempel tyst, lära och bekräfta. Tillgängligt i sensor konsolerna. | - |
| **exkluderings regel** | Instruera Defender for IoT att ignorera aviserings utlösare baserat på tids period, enhets adress och aviserings namn eller av en viss sensor.<br /><br />Om du till exempel vet att alla enheter som övervakas av en viss sensor går genom en underhålls procedur mellan 6:30 och 10:15 i morgon, kan du ange en regel för undantag som anger att sensorn inte ska skicka några varningar i den fördefinierade perioden. | **avisering om <br /> <br /> varnings händelsen** |

## <a name="f"></a>F

| Period | Beskrivning | Läs mer |
|--|--|--|
| **vidarebefordrings regel** | Regler för vidarebefordran instruerar Defender for IoT att skicka aviserings information till partner leverantörer eller system.<br /><br />Skicka till exempel aviserings information till en Splunk-Server eller en Syslog-server. | **varningar** |

## <a name="g"></a>G

| Period | Beskrivning | Läs mer |
|--|--|--|
| **grupp** | Fördefinierade eller anpassade grupper av enheter som innehåller vissa attribut, till exempel enheter som utförde programmerings aktiviteter eller enheter som finns i ett speciellt undernät. Använd grupper som hjälper dig att visa enheter och analysera enheter i Defender för IoT.<br /><br />Grupper kan visas i och skapas från enhets kartan och enhets inventeringen. | **enhets karta <br /> <br /> enhets inventering** |

## <a name="h"></a>H

| Period | Beskrivning | Läs mer |
|--|--|--|
| **Öppen utvecklings miljö för Horisont** | Skydda IoT-och ICS-enheter som kör tillverkarspecifika och anpassade protokoll eller protokoll som avviker från valfri standard. Använd ODE-SDK: n (Open Development Environment) för att utveckla insticks program som avkodar nätverks trafik baserat på definierade protokoll. Defender för IoT-tjänster analyserar trafik för att tillhandahålla fullständig övervakning, avisering och rapportering.<br /><br />Använd horisont för att:<br /><br />- **Expandera** synlighet och kontroll utan att behöva uppgradera Defender för IoT Platform-versioner.<br /><br />- **Skydda** tillverkarspecifik information genom att utveckla på plats som ett externt plugin-program.<br /><br />- **Lokalisera** text för aviseringar, händelser och protokoll parametrar.<br /><br />Kontakta kundens framgångar om du vill ha mer information. | **protokoll stöd <br /> <br /> lokalisering** |
| **Anpassad avisering om Horisont** | Förbättra aviserings hanteringen i företaget genom att utlösa anpassade aviseringar för alla protokoll (baserat på trafik-och arbets sektorer i Horisont Framework).<br /><br />Dessa aviseringar kan användas för att kommunicera information:<br /><br />– Om trafik identifieringar baserat på protokoll och underliggande protokoll i ett eget Horisont-plugin-program.<br /><br />– Om en kombination av protokoll fält från alla protokoll lager. | **protokoll stöd** |

## <a name="i"></a>I

| Period | Beskrivning | Läs mer |
|--|--|--|
| **integreringar** | Expandera Defender för IoT-funktioner genom att dela enhets information med partner system. Organisationer kan överbrygga tidigare silod säkerhet, NAC, incident hantering och enhets hanterings lösningar för att påskynda systemomfattande svar och snabbare minska riskerna. | **vidarebefordrings regel** |
| **internt undernät** | Under näts konfigurationerna som definieras av Defender för IoT. I vissa fall, till exempel miljöer som använder offentliga intervall som interna intervall, kan du instruera Defender för IoT att lösa alla undernät som interna undernät. Undernät visas i kartan och i olika Defender för IoT-rapporter. | **undernät** |

## <a name="l"></a>L

| Period | Beskrivning | Läs mer |
|--|--|--|
| **Lär dig aviserings händelse** | Instruera Defender for IoT att auktorisera trafiken som identifieras i en varnings händelse. | **varnings meddelande om varnings <br /> <br /> händelse <br /> <br /> av avisering** |
| **inlärnings läge** | Läget som används när Defender för IoT lär sig din nätverks aktivitet. Den här aktiviteten blir din nätverks bas linje. Defender för IoT är kvar i läget för en fördefinierad period efter installationen. Aktivitet som avviker från den inlärda aktiviteten efter den här perioden utlöser Defender för IoT-aviseringar. | **Smart IT-utbildning, <br /> <br /> bas linje** |
| **lokalisering** | Lokalisering av text för aviseringar, händelser och protokoll parametrar för insektor-plugin-program som har utvecklats av Horisont. | **Öppen utvecklings miljö för Horisont** |

## <a name="m"></a>M

| Period | Beskrivning | Läs mer |
|--|--|--|
| **Inaktivera varnings händelse** | Instruera Defender for IoT att kontinuerligt ignorera aktivitet med identiska enheter och jämförbar trafik. | **varnings regel för varnings <br /> <br /> undantags regel <br /> <br /> bekräftelse <br /> <br /> händelse** |

## <a name="n"></a>N

| Period | Beskrivning | Läs mer |
|--|--|--|
| **ansökningar** | Information om nätverks ändringar eller olösta enhets egenskaper. Det finns alternativ för att uppdatera enhets-och nätverks information med nya data som identifieras. Om du svarar på meddelanden berikas enhets inventering, kartor och olika rapporter. Tillgängligt på sensor konsoler. | **aviserings <br /> <br /> system meddelanden** |

## <a name="o"></a>O

| Period | Beskrivning | Läs mer |
|--|--|--|
| **drift avisering** | Aviseringar som behandlar drift nätverks problem, till exempel en enhet som misstänks vara frånkopplad från nätverket. | **aviserings <br /> <br /> säkerhets avisering** |

## <a name="p"></a>P

| Period | Beskrivning | Läs mer |
|--|--|--|
| **Purdue-skikt** | Visar samtrafik och beroenden av huvud komponenterna i en typisk ICS på kartan. |  |
| **protokoll stöd** | Förutom stöd för inbäddade protokoll kan du skydda IoT-och ICS-enheter som kör patentskyddade och anpassade protokoll, eller protokoll som avviker från vilken standard som helst, genom att använda en horisont öppen utvecklings miljö SDK. | **Öppen utvecklings miljö för Horisont** |

## <a name="r"></a>R

| Period | Beskrivning | Läs mer |
|--|--|--|
| **nationella** | En logisk division av en global organisation i geografiska regioner. Exempel är Nordamerika, Västeuropa och östra Europa.<br /><br />Nordamerika kan ha fabriker från olika affär senheter. | **åtkomst till grupp <br /> <br /> <br /> <br /> <br /> <br /> plats <br /> <br /> zon för affär senhets lokal hanterings konsol** |
| **rapporter** | Rapporter reflekterar information som genereras av resultat från data utvinnings frågor. Detta inkluderar standard resultat för Data utvinning som är tillgängliga i vyn **rapporter** . Administratörer och säkerhetsanalytiker kan också skapa anpassade frågor för Data utvinning och spara dem som rapporter. Dessa rapporter är också tillgängliga för skrivskyddade användare. | **Data utvinning** |
| **riskbedömnings rapport** | Med riskbedömning kan du generera en säkerhets Poäng för varje nätverks enhet, tillsammans med en övergripande nätverks säkerhets poäng. Den totala poängen visar procent andelen 100 procent av säkerheten. I rapporten finns några rekommendationer som hjälper dig att förbättra den aktuella säkerhets poängen. | - |

## <a name="s"></a>S

| Period | Beskrivning | Läs mer |
|--|--|--|
| **säkerhets avisering** | Aviseringar som hanterar säkerhets problem, till exempel överdriven SMB-inloggnings försök eller identifieringar av skadlig kod. | **avisering om aviserings <br /> <br /> funktion** |
| **selektiv avsökning** | Defender för IoT tar passivt in den och avviker från trafik och identifierar relevant information om enheter, deras attribut, deras beteende och annat. I vissa fall kanske viss information inte syns i passiva nätverks analyser.<br /><br />I så fall kan du använda de säkra, detaljerade sökverktygen i Defender för IoT för att identifiera viktig information om tidigare enheter som inte kan kommas åt. | - |
| **sensor** | Den fysiska eller virtuella dator där Defender for IoT-plattformen är installerad. | **lokal hanterings konsol** |
| **plats** | En plats som en fabrik eller en annan enhet. Platsen ska innehålla en zon eller flera zoner där en sensor är installerad. | **fraktzon** |
| **Plats hantering** | Den lokala hanterings konsolens alternativ som låter dig hantera företags sensorer. | - |
| **Smart IT-utbildning** | När inlärnings perioden är klar och inlärnings läget är inaktiverat kan Defender för IoT identifiera en ovanligt hög nivå av bas linje ändringar som är resultatet av normal IT-aktivitet, till exempel DNS-och HTTP-begäranden. Den här trafiken kan utlösa onödiga varningar om princip överträdelse och system meddelanden. För att minska dessa aviseringar och aviseringar kan du aktivera smart IT-utbildning. | **<br /> <br /> bas linje för utbildnings läge** |
| **undernät** | För att aktivera fokus på enheter med samma enhet sammanställs IT-enheter automatiskt av under nätet i enhets kartan. Varje undernät presenteras som en enda enhet på kartan, inklusive en interaktiv komprimerings-eller utöknings möjlighet att fokusera på ett IT-undernät och tillbaka. | **enhets karta** |
| **system meddelanden** | Meddelanden från den lokala hanterings konsolens omklassificering:<br /><br />– Sensor anslutnings status.<br /><br />– Fjärranslutna säkerhets kopierings problem. | **avisering om aviseringar <br /> <br />** |

## <a name="z"></a>Z

| Period | Beskrivning | Läs mer |
|--|--|--|
| **fraktzon** | Ett område inom en plats där en sensor eller sensorer är installerade. | **plats <br /> <br /> affär senhets <br /> <br /> region** |
