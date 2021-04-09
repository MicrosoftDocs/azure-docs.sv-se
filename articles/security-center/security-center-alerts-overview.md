---
title: Säkerhets aviseringar och incidenter i Azure Security Center
description: Lär dig hur Azure Security Center genererar säkerhets aviseringar och korrelerar dem till incidenter.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 02/25/2021
ms.author: memildin
ms.openlocfilehash: 9d917b3682d3b503743e706e8c952854e86fbc5e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105727050"
---
# <a name="security-alerts-and-incidents-in-azure-security-center"></a>Säkerhets aviseringar och incidenter i Azure Security Center

Security Center genererar aviseringar för resurser som har distribuerats i Azure, lokala miljöer och hybrid moln miljöer.

Säkerhets aviseringar utlöses av avancerade identifieringar och är bara tillgängliga med Azure Defender. Du kan uppgradera från sidan **priser & inställningar** , enligt beskrivningen i [snabb start: Aktivera Azure Defender](enable-azure-defender.md). En kostnads fri 30-dagars utvärderings version är tillgänglig. Information om priser i din valuta och enligt din region finns [Security Center priser](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="what-are-security-alerts-and-security-incidents"></a>Vad är säkerhets aviseringar och säkerhets incidenter? 

**Aviseringar** är de meddelanden som Security Center genererar när de identifierar hot på dina resurser. Security Center prioriterar och listar aviseringarna, tillsammans med den information som behövs för att snabbt undersöka problemet. Security Center ger även rekommendationer för hur du kan åtgärda ett angrepp.

**En säkerhets incident** är en samling relaterade aviseringar i stället för att lista varje avisering individuellt. Security Center använder [Cloud Smart Alert-korrelation](#cloud-smart-alert-correlation-in-azure-security-center-incidents) för att korrelera olika aviseringar och låg åter givning av signaler till säkerhets incidenter.

Med hjälp av incidenter ger Security Center en enda vy över en angrepps kampanj och alla relaterade aviseringar. Med den här vyn kan du snabbt förstå vilka åtgärder som angriparen tog och vilka resurser som påverkades.

## <a name="respond-to-todays-threats"></a>Svara på dagens hot

Hoten i dag ser helt annorlunda ut mot för 20 år sedan. Tidigare hade företag vanligt vis bara bekymrat sig om att det finns en webbplats för enskilda angripare som främst hade intresse av att se vad de kunde göra. Dagens angripare är mycket mer sofistikerade och organiserade. De har ofta specifika ekonomiska och strategiska mål. De har också till gång till fler resurser, eftersom de kan finansieras av Nations stater eller organiserad brottslighet.

Dessa förändringar av verklighet har lett till en oöverträffad nivå av yrkesutbildning i angrepps rang. Dagens angripare är inte intresserade av att bara vanställa en webbplats. De är nu intresserade av att stjäla information, finansiella konton och privata data – alla som de kan använda för att generera kontanter på den öppna marknaden eller för att utnyttja ett visst affärs-, politisk-eller militärt läge. Ännu mer oroande än angripare med ekonomiska mål är de som bryter sig in i nätverk för att skada infrastruktur och individer.

För att skydda sig mot dessa situationer distribuerar dagens organisationer ofta flera punktlösningar, som fokuserar på att skydda företagets nätverk eller slutpunkter genom att leta efter kända attacksignaturer. Dessa lösningar genererar ofta stora volymer av relativt otillförlitliga varningar, som en säkerhetsanalytiker måste testa och undersöka. De flesta organisationer har inte den tid och kunskap som krävs för att agera på dessa varningar, vilket gör att många av dem inte åtgärdas.  

Dessutom har angripare utvecklats sina metoder för att kringgå många signaturer och [anpassa sig till moln miljöer](https://azure.microsoft.com/blog/detecting-threats-with-azure-security-center/). Nya metoder krävs för att snabbt identifiera nya hot och påskynda identifieringen och företagets agerande.


## <a name="continuous-monitoring-and-assessments"></a>Kontinuerlig övervakning och bedömningar

Azure Security Center fördelarna med att ha säkerhets forskning och data vetenskaps team i hela Microsoft som kontinuerligt övervakar för förändringar i hotets landskap. Bland annat kan följande projekt nämnas:

* **Övervakning av hot information**: Hot information innehåller mekanismer, indikatorer, effekter och åtgärds bara råd om befintliga eller nya hot. Den här informationen delas i säkerhets-communityn och Microsoft övervakar feeds om hotinformation från interna och externa källor.
* **Signal delning**: insikter från säkerhets team i Microsofts breda portfölj med moln tjänster och lokala tjänster, servrar och klient slut punkts enheter delas och analyseras.
* **Microsofts säkerhetsexperter**: Kontinuerligt arbete i team inom hela Microsoft som arbetar inom specialiserade säkerhetsområden, exempelvis datautredning och identifiering av webbattacker.
* **Identifieringsjustering**: Algoritmer körs mot kundens verkliga datauppsättningar och säkerhetsanalytiker arbetar med kunden för att granska resultaten. Sann och falsk positiv identifiering används för att förfina maskininlärningsalgoritmerna.

Dessa kombinerade ansträngningar resulterar i nya och förbättrade identifieringar, som du kan dra nytta av direkt – det finns ingen åtgärd att ta.

## <a name="how-does-security-center-detect-threats"></a>Hur identifierar Security Center hot? <a name="detect-threats"> </a>

Microsofts säkerhetsforskare söker hela tiden efter hot. På grund av vår globala närvaro i molnet och lokalt har vi till gång till en mycket uppsättning telemetri. Med den breda samlingen av data uppsättningar kan vi upptäcka nya angrepps mönster och trender i alla lokala konsument-och företags produkter, samt vår onlinetjänster. Detta gör i sin tur att identifieringsalgoritmerna i Security Center snabbt kan uppdateras allt eftersom angripare hittar nya och alltmer sofistikerade sätt att utnyttja systemen. Och för dig som kund innebär det att du kan hålla jämna steg med dagens snabbt föränderliga hotmiljö.

Security Center samlar in, analyserar och integrerar loggdata från dina Azure-resurser och nätverket för att identifiera verkliga hot och minska antalet falska positiva identifieringar. Den fungerar även med anslutna partner lösningar som brand Väggs-och slut punkts skydds lösningar. Security Center analyserar informationen, som ofta korrelerar information från flera källor, för att identifiera hot.

![Datainsamling och datapresentation i Security Center](./media/security-center-alerts-overview/security-center-detection-capabilities.png)

Security Center använder avancerade säkerhetsanalyser, som går mycket längre än signaturbaserade lösningar. Framsteg inom stordata- och [maskininlärningstekniker](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) utnyttjas för att utvärdera händelser i hela molninfrastrukturen, vilket gör det möjligt att upptäcka hot som skulle vara omöjliga att identifiera med manuella metoder, samtidigt som det blir lättare att förutsäga utvecklingen av nya attacker. Dessa säkerhetsanalyser omfattar:

- **Integrerad Hot information**: Microsoft har en stor mängd global Hot information. Telemetri flödar från flera källor, till exempel Azure, Microsoft 365, Microsoft CRM Online, Microsoft Dynamics AX, outlook.com, MSN.com, Microsoft Digital brottslighet Unit (DCU) och Microsoft Security Response Center (MSRC). Forskare får också information om hot information som delas mellan större leverantörer av moln tjänster och feeds från andra tredje parter. Azure Security Center kan använda den här informationen för att varna dig om hot från kända illvilliga aktörer.

- **Beteende analys**: beteende analys är en teknik som analyserar och jämför data med en samling kända mönster. Dessa mönster är dock inte enkla signaturer. De fastställs genom komplexa maskininlärningsalgoritmer som tillämpas på enorma datamängder, och av expertanalytiker genom noggrann analys av skadliga beteenden. Azure Security Center kan använda beteende analys för att identifiera komprometterade resurser baserat på analyser av loggar för virtuella datorer, loggar för virtuella nätverks enheter, infrastruktur loggar och andra källor.

- **Avvikelse identifiering**: Azure Security Center använder också avvikelse identifiering för att identifiera hot. Till skillnad från beteende analyser (som är beroende av kända mönster härledda från stora data mängder) är avvikelse identifieringen mer personligt anpassad och fokuserar på de bas linjer som är speciella för dina distributioner. Maskininlärning tillämpas för att fastställa om aktiviteten i dina distributioner är normal. Sedan genereras regler som definierar avvikande förhållanden som kan representera en säkerhetshändelse.

## <a name="how-are-alerts-classified"></a>Hur klassificeras aviseringar?

Security Center tilldelar en allvarlighets grad till aviseringar, som hjälper dig att prioritera i vilken ordning du deltar i varje avisering, så att när en resurs komprometteras kan du komma åt den direkt. Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig i att söka efter eller det analytiska som används för att utfärda aviseringen samt vilken konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen.

> [!NOTE]
> Aviseringens allvarlighets grad visas på ett annat sätt i portalen och versioner av den REST API som är dagens datum 01-01-2019. Om du använder en äldre version av API: t uppgraderar du för den konsekventa upplevelsen som beskrivs nedan.

| Allvarlighetsgrad          | Rekommenderat svar      |
|---|---|
| **Hög**          | Det finns en hög sannolikhet för att din resurs komprometteras. Du bör titta på det direkt. Security Center har hög exakthet i både den skadliga avsikten och i de resultat som används för att utfärda aviseringen. Till exempel en avisering som identifierar körningen av ett känt skadligt verktyg, till exempel Mimikatz, ett vanligt verktyg som används för stöld av autentiseringsuppgifter.                                                                                                               |
| **Medel**        | Detta är förmodligen en misstänkt aktivitet som kan tyda på att en resurs komprometteras. Security Center tillförlitlighet i analys eller sökning är medel och säkerheten för den skadliga avsikten är medel hög till hög. Detta brukar vara maskin inlärning eller avvikande identifieringar. Till exempel ett inloggnings försök från en avvikande plats.                                                                                                                |
| **Låg**           | Detta kan vara ett ofarligt positivt eller blockerat angrepp. Security Center är inte tillräckligt säker på att avsikten är skadlig och att aktiviteten kan vara Innocent. Logg rensning är till exempel en åtgärd som kan inträffa när en angripare försöker dölja sina spår, men i många fall är en rutin åtgärd som utförs av administratörer. Security Center meddelar dig normalt inte när attacker blockeras, om det inte är ett intressant ärende som vi föreslår att du tittar på. |
| **Information** | Endast informations aviseringar visas när du ökar detalj nivån i en säkerhets incident, eller om du använder REST API med ett angivet aviserings-ID. En incident består vanligt vis av ett antal aviseringar, varav vissa kan visas på egen hand som endast information, men i samband med de andra aviseringarna kan det vara betrodd av en närmare titt.                                                                                                                         |

## <a name="export-alerts"></a>Exportera aviseringar

Du har flera alternativ för att visa aviseringar utanför Security Center, inklusive:

- **Hämta CSV-rapport** på instrument panelen för aviseringar innehåller en export till CSV-filen vid ett tillfälle.
- Med **kontinuerlig export** från pris &s inställningar kan du konfigurera strömmar av säkerhets aviseringar och rekommendationer till Log Analytics arbets ytor och Event Hubs. [Läs mer om kontinuerlig export](continuous-export.md)
- **Azure Sentinel Connector** strömmar säkerhets aviseringar från Azure Security Center till Azure Sentinel. [Lär dig mer om att ansluta Azure Security Center med Azure Sentinel](../sentinel/connect-azure-security-center.md)

## <a name="cloud-smart-alert-correlation-in-azure-security-center-incidents"></a>Moln Smart aviserings korrelation i Azure Security Center (incidenter)

Azure Security Center kontinuerligt analyserar hybrid moln arbets belastningar med hjälp av avancerad analys och hot information för att varna dig om skadlig aktivitet.

Risken för hot täckning växer. Behovet av att identifiera även den lättaste kompromissen är viktigt och det kan vara svårt att prioritering de olika aviseringarna och identifiera ett faktiskt angrepp. Security Center hjälper till att analysera den här aviseringen. Den hjälper till att diagnostisera attacker när de sker, genom att korrelera olika aviseringar och låg åter givning i säkerhets incidenter.

Fusions analys är den teknik och den analytiska Server delen som ger Security Center incidenter, vilket gör det möjligt att korrelera olika aviseringar och kontextuella signaler tillsammans. Fusion tittar på de olika signaler som rapporteras i en prenumeration över resurserna. Fusion hittar mönster som avslöjar angrepps förlopp eller signaler med delad sammanhangsbaserad information, vilket indikerar att du bör använda en enhetlig svars procedur för dem.

Fusions analys kombinerar säkerhets domänens kunskap med AI för att analysera aviseringar, identifiera nya angrepps mönster när de inträffar. 

Security Center utnyttjar MITRE-attackens matris för att koppla aviseringar till deras uppfattade avsikt, vilket underlättar formalisera säkerhets domän kunskap. Genom att använda den information som samlats in för varje steg i en attack kan Security Center dessutom utesluta aktiviteter som verkar vara steg för en attack, men egentligen inte.

Eftersom angrepp ofta sker över olika klienter kan Security Center kombinera AI-algoritmer för att analysera angrepps sekvenser som rapporteras för varje prenumeration. Den här tekniken identifierar angrepps sekvenser som vanliga aviserings mönster, i stället för att bara vara incidenter som är associerade med varandra.

Under en undersökning av en incident behöver analytiker ofta extra kontext för att uppnå en bedömning om hotets beskaffenhet och hur det kan minimeras. Till exempel även om en nätverks avvikelse identifieras, utan att förstå vad som händer i nätverket eller med avseende på mål resursen, är det svårt att förstå vilka åtgärder som ska vidtas härnäst. För att under lätta kan en säkerhets incident innehålla artefakter, relaterade händelser och information. Den ytterligare information som är tillgänglig för säkerhets incidenter varierar beroende på typ av hot som upptäckts och konfigurationen av din miljö. 

> [!TIP]
> En lista över säkerhets incident aviseringar som kan skapas av fusions analysen finns i [referens tabellen för aviseringar](alerts-reference.md#alerts-fusion).

:::image type="content" source="./media/security-center-alerts-cloud-smart/security-incident.png" alt-text="Skärm bild av identifierad rapport för säkerhets incident":::

Information om hur du hanterar säkerhets incidenter finns i [hantera säkerhets incidenter i Azure Security Center](security-center-incident.md).

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om de olika typerna av aviseringar som är tillgängliga i Security Center. Mer information finns i:

- [Säkerhets aviseringar i Azure aktivitets logg](https://go.microsoft.com/fwlink/?linkid=2114113) – förutom att vara tillgängliga i Azure Portal eller program mässigt, granskas säkerhets aviseringar och incidenter som händelser i Azure aktivitets logg
- [Referens tabell för Azure Defender-aviseringar](alerts-reference.md)
- [Åtgärda säkerhetsaviseringar](security-center-managing-and-responding-alerts.md#respond-to-security-alerts)