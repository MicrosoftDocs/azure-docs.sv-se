---
title: 'Självstudie: kontroll av regelefterlevnad – Azure Security Center'
description: 'Självstudie: Lär dig hur du kan förbättra din efterlevnad med hjälp av Azure Security Center.'
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5f50c4dc-ea42-418d-9ea8-158ffeb93706
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/10/2021
ms.author: memildin
ms.openlocfilehash: fb8dc22c923b7b53a6263baa43046862af4d2f04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100370280"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Självstudie: Förbättra regelefterlevnaden

Azure Security Center hjälper till att effektivisera processen för att uppfylla krav på efterlevnad av efterlevnad, med hjälp av **instrument panelen för kontroll av efterlevnad**. 

Security Center kontinuerligt utvärderar din hybrid moln miljö för att analysera risk faktorer enligt de kontroller och bästa metoderna i de standarder som tillämpas på dina prenumerationer. Instrument panelen visar statusen för din efterlevnad av dessa standarder. 

När du aktiverar Security Center på en Azure-prenumeration tilldelas [Azures säkerhets mått](../security/benchmarks/introduction.md) automatiskt till den prenumerationen. Detta respekterade riktmärken bygger på kontrollerna från [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) och [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) med fokus på Cloud-inriktad säkerhet.

Instrument panelen för kontroll av efterlevnad visar status för alla utvärderingar i din miljö för de valda standarderna och reglerna. När du arbetar med rekommendationerna och minskar riskfaktorerna i din miljö förbättras position.

I den här kursen får du lära du dig att:

> [!div class="checklist"]
> * Utvärdera efterlevnaden av efterlevnaden med hjälp av instrument panelen för regelefterlevnad
> * Förbättra din efterlevnadsstatus genom att vidta åtgärder enligt rekommendationer
> * Konfigurera aviseringar vid ändringar i position för efterlevnad
> * Exportera dina efterlevnadsprinciper som en kontinuerlig data ström och som veckovis ögonblicks bilder

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att gå igenom de funktioner som beskrivs i den här självstudien:

- [Azure Defender](azure-defender.md) måste vara aktiverat. Du kan prova Azure Defender kostnads fritt i 30 dagar.
- Du måste vara inloggad med ett konto som har Läs behörighet till policyns efterlevnadsprinciper (**säkerhets läsaren** är inte tillräcklig). Rollen som **global läsare** för prenumerationen kommer att fungera. Du måste minst ha tilldelats rollerna **resurs princip deltagare** och **säkerhets administratör** .

##  <a name="assess-your-regulatory-compliance"></a>Utvärdera din regelefterlevnad

Instrument panelen för kontroll av efterlevnad visar dina valda efterlevnadsprinciper med alla krav, där kraven som stöds är mappade till tillämpliga säkerhets utvärderingar. Status för dessa utvärderingar visar att standarden är uppfyllt.

Använd instrument panelen för kontroll av efterlevnad för att fokusera på de luckor som följer de standarder och föreskrifter som du har valt. Den här fokuserade vyn gör det också möjligt för dig att kontinuerligt övervaka din efterlevnad över tid inom ett dynamiskt moln och i hybrid miljöer.

1. Från Security Center menyn väljer du regelefterlevnad **.**

    Längst upp på skärmen finns en instrument panel med en översikt över din kompatibilitetsstatus med en uppsättning regler för efterlevnad av stöd. Du ser det övergripande resultatet av efterlevnaden och antalet överförings-och utvärderings resultat som är associerade med varje standard.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="Instrument panel för regler för efterlevnad" lightbox="./media/security-center-compliance-dashboard/compliance-dashboard.png":::

1. Välj en flik för en efterlevnadsprincip som är relevant för dig (1). Du ser vilka prenumerationer som standarden tillämpas på (2) och listan över alla kontroller för den standarden (3). För tillämpliga kontroller kan du Visa information om att skicka och stoppa utvärderingar som är associerade med kontrollen (4) och antalet påverkade resurser (5). Vissa kontroller är nedtonade. De här kontrollerna har inga associerade Security Center-utvärderingar. Kontrol lera deras krav och utvärdera dem i din miljö. Vissa av dessa kan vara processbaserade och inte tekniska.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-drilldown.png" alt-text="Utforska informationen om kompatibilitet med en speciell standard":::

1. Välj **Hämta rapport** om du vill generera en PDF-rapport med en sammanfattning av din aktuella kompatibilitetsstatus för en viss standard.

    Rapporten ger en översikt över din kompatibilitetsstatus för den valda standarden baserat på Security Center utvärderings data. Rapportens ordning enligt kontrollerna i denna särskilda standard. Rapporten kan delas med relevanta intressenter och kan ge bevis på interna och externa revisorer.

    :::image type="content" source="./media/security-center-compliance-dashboard/download-report.png" alt-text="Hämta Kompatibilitetsrapport":::

## <a name="improve-your-compliance-posture"></a>Förbättra din efterlevnadsstatus

Med hjälp av informationen i instrument panelen för regelefterlevnad kan du förbättra position genom att lösa rekommendationer direkt på instrument panelen.

1.  Välj någon av de misslyckade utvärderingarna som visas på instrument panelen för att visa information om den rekommendationen. Varje rekommendation innehåller en uppsättning reparations steg för att lösa problemet.

1.  Välj en viss resurs om du vill visa mer information och lösa rekommendationerna för resursen. <br>Till exempel, i **Azure CIS-1.1.0** standard, väljer du rekommendations **disk kryptering ska tillämpas på virtuella datorer**.

    :::image type="content" source="./media/security-center-compliance-dashboard/sample-recommendation.png" alt-text="Välja en rekommendation från en standard leads direkt till rekommendations informations Sidan":::

1. I det här exemplet kommer du att komma åt den virtuella Azure-datorns sidor i Azure Portal, där du kan aktivera kryptering från fliken **säkerhet** , när du väljer **vidta åtgärd** från sidan med rekommendations information:

    :::image type="content" source="./media/security-center-compliance-dashboard/encrypting-vm-disks.png" alt-text="Knappen för att vidta åtgärder på sidan med rekommendations information leder till reparations alternativen":::

    Mer information om hur du utför rekommendationerna finns i artikeln [Utföra säkerhetsrekommendationerna i Azure Security Center](security-center-recommendations.md).

1.  När du har åtgärdat för att lösa rekommendationer ser du resultatet i rapporten instrument panel för efterlevnad, eftersom ditt prispoäng ökar.

    > [!NOTE]
    > Utvärderingarna körs ungefär var 12: e timme, så du kommer att se påverkan på dina efterlevnadsprinciper endast efter nästa körning av den relevanta utvärderingen.


## <a name="export-your-compliance-status-data"></a>Exportera dina status data för efterlevnad

Om du vill spåra din kompatibilitetsstatus med andra övervaknings verktyg i din miljö, innehåller Security Center en export metod som gör det enkelt. Konfigurera **kontinuerlig export** för att skicka urvals data till en Azure Event Hub-eller Log Analytics-arbetsyta.

Använd kontinuerliga export data till en Azure Event Hub eller en Log Analytics arbets yta:

- Exportera alla regler för regelefterlevnad i en **kontinuerlig data ström**:

    :::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-stream.png" alt-text="Exportera en data ström med regler för regelefterlevnad kontinuerligt" lightbox="media/security-center-compliance-dashboard/export-compliance-data-stream.png":::

- Exportera **vecko Visa ögonblicks bilder** av dina regler för regelefterlevnad:

    :::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-snapshot.png" alt-text="Exportera regelbundet en veckovis ögonblicks bild av regelefterlevnad" lightbox="media/security-center-compliance-dashboard/export-compliance-data-snapshot.png":::

Du kan också exportera en **PDF/CSV-rapport** för dina efterlevnadsprinciper direkt från instrument panelen för kontroll av efterlevnad:

:::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-report.png" alt-text="Exportera dina uppgifter för regelefterlevnad som en PDF-eller CSV-rapport" lightbox="media/security-center-compliance-dashboard/export-compliance-data-report.png":::

Lär dig mer om att [kontinuerligt exportera Security Center data](continuous-export.md).


## <a name="run-workflow-automations-when-there-are-changes-to-your-compliance"></a>Kör automatisering av arbets flöde när det finns ändringar i din efterlevnad

Security Center s funktion för arbets flödes automatisering kan utlösa Logic Apps när någon av de ändrade tillstånden för regelefterlevnad.

Till exempel kanske du vill Security Center e-posta en speciell användare när en kompatibilitetskontroll Miss lyckas. Du måste först skapa Logic app (med [Azure Logic Apps](../logic-apps/logic-apps-overview.md)) och sedan konfigurera utlösaren i en ny arbets flödes automatisering som beskrivs i [automatisera svar på Security Center utlösare](workflow-automation.md).

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="Använda ändringar i regelefterlevnad för att utlösa en arbets flödes automatisering" lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::




## <a name="faq---regulatory-compliance-dashboard"></a>Vanliga frågor och svar om instrumentpanelen för regelefterlevnad

- [Vilka standarder stöds i instrument panelen för efterlevnad?](#what-standards-are-supported-in-the-compliance-dashboard)
- [Varför visas vissa kontroller nedtonade?](#why-do-some-controls-appear-grayed-out)
- [Hur kan jag ta bort en inbyggd standard, som PCI-DSS, ISO 27001 eller SOC2 TSP från instrument panelen?](#how-can-i-remove-a-built-in-standard-like-pci-dss-iso-27001-or-soc2-tsp-from-the-dashboard)
- [Jag gjorde den föreslagna ändringen baserat på rekommendationen, men den visas inte på instrument panelen](#i-made-the-suggested-changed-based-on-the-recommendation-yet-it-isnt-being-reflected-in-the-dashboard)
- [Vilka behörigheter behöver jag för att få åtkomst till instrument panelen för efterlevnad?](#what-permissions-do-i-need-to-access-the-compliance-dashboard)
- [Instrument panelen för kontroll av efterlevnad har inte lästs in åt mig](#the-regulatory-compliance-dashboard-isnt-loading-for-me)
- [Hur visar jag en rapport om att skicka och stoppa kontroller per standard i min instrument panel?](#how-can-i-view-a-report-of-passing-and-failing-controls-per-standard-in-my-dashboard)
- [Hur kan jag hämta en rapport med efterlevnadsprinciper i ett annat format än PDF?](#how-can-i-download-a-report-with-compliance-data-in-a-format-other-than-pdf)
- [Hur kan jag skapa undantag för några av principerna på instrument panelen för kontroll av efterlevnad?](#how-can-i-create-exceptions-for-some-of-the-policies-in-the-regulatory-compliance-dashboard)
- [Vilka Azure Defender-planer eller licenser behöver jag för att använda instrument panelen för kontroll av efterlevnad?](#what-azure-defender-plans-or-licenses-do-i-need-to-use-the-regulatory-compliance-dashboard)

### <a name="what-standards-are-supported-in-the-compliance-dashboard"></a>Vilka standarder stöds i instrument panelen för efterlevnad?
Som standard visas instrument panelen för övervakning av Azures säkerhets mått. Azures säkerhets Benchmark är de Microsoft-baserade, Azure-/regionsspecifika rikt linjerna för säkerhet och metod tips som baseras på vanliga ramverk för efterlevnad. Läs mer i [Introduktion till Azures säkerhet](../security/benchmarks/introduction.md).

Om du vill spåra din efterlevnad av någon annan standard måste du uttryckligen lägga till dem på din instrument panel.
 
Du kan lägga till standarder som Azure CIS-1.1.0 (ny), NIST SP 800-53 R4, NIST SP 800-171 R2, SWIFT CSP CSCF-v2020, Storbritannien och Storbritannien NHS, HIPAA HITRUST, Kanada Federal PBMM, ISO 27001, SOC2-TSP och PCI-DSS 3.2.1.  
 
Fler standarder läggs till i instrument panelen och ingår i informationen om hur [du anpassar uppsättningen med standarder på instrument panelen för](update-regulatory-compliance-packages.md)regelefterlevnad.

### <a name="why-do-some-controls-appear-grayed-out"></a>Varför visas vissa kontroller nedtonade?
För varje efterlevnadsprincip i instrument panelen finns det en lista över standard kontrollerna. För tillämpliga kontroller kan du Visa information om att skicka och stoppa utvärderingar.

Vissa kontroller är nedtonade. De här kontrollerna har inga associerade Security Center-utvärderingar. Vissa kan vara procedurer eller process-relaterade och kan därför inte verifieras av Security Center. Några automatiska principer eller utvärderingar har inte implementerats ännu, men kommer att ha i framtiden. Och vissa kontroller kan vara plattforms ansvar som förklaras i [det delade ansvaret i molnet](../security/fundamentals/shared-responsibility.md). 

### <a name="how-can-i-remove-a-built-in-standard-like-pci-dss-iso-27001-or-soc2-tsp-from-the-dashboard"></a>Hur kan jag ta bort en inbyggd standard, som PCI-DSS, ISO 27001 eller SOC2 TSP från instrument panelen? 
Om du vill anpassa instrument panelen för reglering av efterlevnad och bara fokusera på de standarder som gäller för dig, kan du ta bort någon av de regler som visas som inte är relevanta för din organisation. Ta bort en standard genom att följa anvisningarna i [ta bort en standard från din instrument panel](update-regulatory-compliance-packages.md#remove-a-standard-from-your-dashboard).

### <a name="i-made-the-suggested-changed-based-on-the-recommendation-yet-it-isnt-being-reflected-in-the-dashboard"></a>Jag gjorde den föreslagna ändringen baserat på rekommendationen, men den visas inte på instrument panelen
Efter att du vidtar åtgärder för att lösa rekommendationerna väntar du i 12 timmar för att se ändringarna i dina efterlevnadsprinciper. Utvärderingarna körs ungefär var 12: e timme, så du kommer att se effekterna på dina Compliance-data endast efter att utvärderingarna har körts.
 
### <a name="what-permissions-do-i-need-to-access-the-compliance-dashboard"></a>Vilka behörigheter behöver jag för att få åtkomst till instrument panelen för efterlevnad?
Om du vill visa kompatibilitetsinformation måste du ha minst **läsar** åtkomst till policyns efterlevnadsprinciper även. så att säkerhets läsaren inte räcker till. Om du är en global läsare i prenumerationen så är det tillräckligt.

Den minsta uppsättningen roller för åtkomst till instrument panelen och hanterings standarder är **resurs princip deltagare** och **säkerhets administratör**.


### <a name="the-regulatory-compliance-dashboard-isnt-loading-for-me"></a>Instrument panelen för kontroll av efterlevnad har inte lästs in åt mig
Om du vill använda instrument panelen för kontroll av efterlevnad måste Azure Security Center ha Azure Defender aktiverat på prenumerations nivån. Om instrument panelen inte läses in på rätt sätt kan du prova följande steg:

1. Rensa webbläsarens cacheminne.
1. Försök med en annan webbläsare.
1. Försök att öppna instrument panelen från en annan nätverks plats.


### <a name="how-can-i-view-a-report-of-passing-and-failing-controls-per-standard-in-my-dashboard"></a>Hur visar jag en rapport om att skicka och stoppa kontroller per standard i min instrument panel?
På huvud instrument panelen kan du se en rapport över att skicka och stoppa kontroller för (1) de lägsta 4 lägsta kraven för efterlevnad på instrument panelen. Välj (2) **Visa alla *x*** (där x är antalet standarder som du spårar) om du vill se alla status inställningar för att skicka/stoppa. Ett kontext plan visar kompatibilitetsstatus för var och en av dina spårade standarder.

:::image type="content" source="media/security-center-compliance-dashboard/summaries-of-compliance-standards.png" alt-text="Sammanfattnings avsnittet på instrument panelen för regler för efterlevnad":::


### <a name="how-can-i-download-a-report-with-compliance-data-in-a-format-other-than-pdf"></a>Hur kan jag hämta en rapport med efterlevnadsprinciper i ett annat format än PDF?
När du väljer **Hämta rapport** väljer du standard och formatet (PDF eller CSV). Den resulterande rapporten visar den aktuella uppsättningen prenumerationer som du har valt i portalens filter.

- I PDF-rapporten visas en sammanfattnings status för den standard som du har valt
- CSV-rapporten innehåller detaljerade resultat per resurs, som den är relaterad till principer som är associerade med varje kontroll

För närvarande finns det inget stöd för att ladda ned en rapport för en anpassad princip. endast för de angivna reglerings standarderna.


### <a name="how-can-i-create-exceptions-for-some-of-the-policies-in-the-regulatory-compliance-dashboard"></a>Hur kan jag skapa undantag för några av principerna på instrument panelen för kontroll av efterlevnad?
För principer som är inbyggda i Security Center och ingår i säkra poäng, kan du skapa undantag för en eller flera resurser direkt i portalen enligt beskrivningen i [undanta resurser och rekommendationer från dina säkra Poäng](exempt-resource.md).

För andra principer kan du skapa ett undantag direkt i själva principen genom att följa anvisningarna i [Azure policy undantags struktur](../governance/policy/concepts/exemption-structure.md).


### <a name="what-azure-defender-plans-or-licenses-do-i-need-to-use-the-regulatory-compliance-dashboard"></a>Vilka Azure Defender-planer eller licenser behöver jag för att använda instrument panelen för kontroll av efterlevnad?
Om du har något av Azure Defender-paketen aktiverade på någon av dina Azure-resurs typer har du åtkomst till instrument panelen för kontroll av efterlevnad, med alla dess data, i Security Center.





## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att använda Security Centers instrument panel för kontroll av efterlevnad för att:

> [!div class="checklist"]
> * Visa och övervaka din position för efterlevnad av de standarder och föreskrifter som är viktiga för dig.
> * Förbättra din kompatibilitetsstatus genom att utföra relevanta rekommendationer och se dina kompatibilitetspoäng öka.

Instrument panelen för kontroll av efterlevnad kan förenkla processen för efterlevnad och avsevärt minska den tid som krävs för att samla in kompatibiliteten för din Azure-, hybrid-och miljö med flera moln.

Läs mer i följande relaterade sidor:

- [Anpassa uppsättningen med standarder på instrument panelen för](update-regulatory-compliance-packages.md) regelefterlevnad – lär dig hur du väljer vilka standarder som visas på instrument panelen för kontroll av efterlevnad. 
- [Hantera säkerhets rekommendationer i Azure Security Center](security-center-recommendations.md) – lär dig hur du använder rekommendationer i Security Center för att skydda dina Azure-resurser.