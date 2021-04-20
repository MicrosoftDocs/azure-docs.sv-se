---
title: 'Självstudie: Kontroller av regelefterlevnad – Azure Security Center'
description: 'Självstudie: Lär dig hur du förbättrar regelefterlevnaden med hjälp Azure Security Center.'
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
ms.openlocfilehash: 284a7f532ed918397fe1cfcf3458bbc6fb0bdd32
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739016"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Självstudie: Förbättra regelefterlevnaden

Azure Security Center hjälper till att effektivisera processen för att uppfylla regelefterlevnadskrav med hjälp av **instrumentpanelen för regelefterlevnad.** 

Security Center utvärderar kontinuerligt din hybridmolnmiljö för att analysera riskfaktorerna enligt kontrollerna och bästa praxis i de standarder som tillämpas på dina prenumerationer. Instrumentpanelen visar statusen för din efterlevnad av dessa standarder. 

När du aktiverar Security Center på en Azure-prenumeration tilldelas [Azure Security Benchmark](https://docs.microsoft.com/security/benchmark/azure/introduction) automatiskt till den prenumerationen. Det här allmänt respekterade benchmark-måttet bygger på kontrollerna från [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) och National Institute of Standards and Technology [(NIST)](https://www.nist.gov/) med fokus på molncentrerad säkerhet.

Instrumentpanelen för regelefterlevnad visar status för alla utvärderingar i din miljö för dina valda standarder och föreskrifter. När du agerar på rekommendationerna och minskar riskfaktorer i din miljö förbättras din efterlevnadsstatus.

I den här kursen får du lära du dig att:

> [!div class="checklist"]
> * Utvärdera din regelefterlevnad med hjälp av instrumentpanelen för regelefterlevnad
> * Förbättra din efterlevnadsstatus genom att vidta åtgärder enligt rekommendationer
> * Konfigurera aviseringar om ändringar i din efterlevnadsstatus
> * Exportera dina efterlevnadsdata som en kontinuerlig dataström och som veckovisa ögonblicksbilder

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Så här går du igenom funktionerna i den här självstudien:

- [Azure Defender](azure-defender.md) måste vara aktiverat. Du kan prova Azure Defender kostnadsfritt i 30 dagar.
- Du måste vara inloggad med ett konto som har läsbehörighet till principefterlevnadsdata **(Säkerhetsläsaren** är inte tillräcklig). Rollen global **läsare för** prenumerationen kommer att fungera. Du måste minst ha rollerna Resursprincipdeltagare **och** **Säkerhetsadministratör** tilldelade.

##  <a name="assess-your-regulatory-compliance"></a>Utvärdera din regelefterlevnad

Instrumentpanelen för regelefterlevnad visar dina valda efterlevnadsstandarder med alla deras krav, där kraven som stöds mappas till tillämpliga säkerhetsutvärderingar. Statusen för dessa utvärderingar återspeglar din efterlevnad av standarden.

Använd instrumentpanelen för regelefterlevnad för att fokusera din uppmärksamhet på brister i efterlevnaden av dina valda standarder och föreskrifter. Med den här fokuserade vyn kan du även kontinuerligt övervaka din efterlevnad över tid i dynamiska moln- och hybridmiljöer.

1. På Security Center på menyn väljer du **Regelefterlevnad.**

    Överst på skärmen finns en instrumentpanel med en översikt över din efterlevnadsstatus med en uppsättning efterlevnadsregler som stöds. Du ser din övergripande efterlevnadspoäng och antalet godkännanden jämfört med misslyckade utvärderingar som är associerade med varje standard.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="Instrumentpanel för regelefterlevnad" lightbox="./media/security-center-compliance-dashboard/compliance-dashboard.png":::

1. Välj en flik för en efterlevnadsstandard som är relevant för dig (1). Du ser vilka prenumerationer som standarden tillämpas på (2) och listan över alla kontroller för den standarden (3). För tillämpliga kontroller kan du visa information om att skicka och misslyckas med utvärderingar som är associerade med kontrollen (4) och antalet berörda resurser (5). Vissa kontroller är nedtonade. De här kontrollerna har inga associerade Security Center utvärderingar. Kontrollera deras krav och utvärdera dem i din miljö. Vissa av dessa kan vara processrelaterade och inte tekniska.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-drilldown.png" alt-text="Utforska information om kompatibilitet med en specifik standard":::

1. Om du vill generera en PDF-rapport med en sammanfattning av din aktuella efterlevnadsstatus för en viss standard väljer du **Ladda ned rapport.**

    Rapporten innehåller en översikt över din efterlevnadsstatus för den valda standarden baserat på Security Center utvärderingsdata. Rapporten är ordnad enligt den specifika standardens kontroller. Rapporten kan delas med relevanta intressenter och kan ge bevis till interna och externa granskare.

    :::image type="content" source="./media/security-center-compliance-dashboard/download-report.png" alt-text="Ladda ned efterlevnadsrapport":::

## <a name="improve-your-compliance-posture"></a>Förbättra din efterlevnadsstatus

Med hjälp av informationen i instrumentpanelen för regelefterlevnad kan du förbättra din efterlevnadsstatus genom att matcha rekommendationer direkt på instrumentpanelen.

1.  Välj någon av de misslyckade utvärderingarna som visas på instrumentpanelen för att visa information om rekommendationen. Varje rekommendation innehåller en uppsättning reparationssteg för att lösa problemet.

1.  Välj en viss resurs om du vill visa mer information och lösa rekommendationen för den resursen. <br>I till exempel **Azure CIS 1.1.0-standarden** väljer du rekommendationen **Diskkryptering ska tillämpas på virtuella datorer.**

    :::image type="content" source="./media/security-center-compliance-dashboard/sample-recommendation.png" alt-text="Om du väljer en rekommendation från en standard leder det direkt till sidan med rekommendationsinformation":::

1. När du i det  här exemplet väljer Vidta åtgärd på sidan med rekommendationsinformation kommer du till sidorna för virtuella Azure-datorer i Azure Portal, där du kan aktivera kryptering från **fliken** Säkerhet:

    :::image type="content" source="./media/security-center-compliance-dashboard/encrypting-vm-disks.png" alt-text="Knappen Vidta åtgärd på sidan med rekommendationsinformation leder till reparationsalternativen":::

    Mer information om hur du utför rekommendationerna finns i artikeln [Utföra säkerhetsrekommendationerna i Azure Security Center](security-center-recommendations.md).

1.  När du har vidta åtgärder för att lösa rekommendationer visas resultatet i rapporten för instrumentpanelen för efterlevnad eftersom din kompatibilitetspoäng förbättras.

    > [!NOTE]
    > Utvärderingar körs ungefär var 12:e timme, så du ser effekten på dina efterlevnadsdata först efter nästa körning av den relevanta utvärderingen.


## <a name="export-your-compliance-status-data"></a>Exportera dina efterlevnadsstatusdata

Om du vill spåra din efterlevnadsstatus med andra övervakningsverktyg i din miljö Security Center en exportmekanism för att göra detta enkelt. Konfigurera **kontinuerlig export för** att skicka utvalda data till en Azure Event Hub eller en Log Analytics-arbetsyta.

Använd kontinuerlig export av data till en Azure Event Hub eller en Log Analytics-arbetsyta:

- Exportera alla regelefterlevnadsdata i en **kontinuerlig dataström:**

    :::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-stream.png" alt-text="Exportera kontinuerligt en dataström med regelefterlevnadsdata" lightbox="media/security-center-compliance-dashboard/export-compliance-data-stream.png":::

- Exportera **veckovisa ögonblicksbilder** av dina regelefterlevnadsdata:

    :::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-snapshot.png" alt-text="Exportera kontinuerligt en veckovis ögonblicksbild av regelefterlevnadsdata" lightbox="media/security-center-compliance-dashboard/export-compliance-data-snapshot.png":::

Du kan också exportera en **PDF/CSV-rapport av** dina efterlevnadsdata direkt från instrumentpanelen för regelefterlevnad:

:::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-report.png" alt-text="Exportera dina regelefterlevnadsdata som en PDF- eller CSV-rapport" lightbox="media/security-center-compliance-dashboard/export-compliance-data-report.png":::

Läs mer i [Exportera data Security Center kontinuerligt.](continuous-export.md)


## <a name="run-workflow-automations-when-there-are-changes-to-your-compliance"></a>Kör arbetsflödesautomation när det finns ändringar i din efterlevnad

Security Center funktionen för arbetsflödesautomatisering kan utlösa Logic Apps när en av dina utvärderingar av regelefterlevnad ändras.

Du kanske till exempel vill att Security Center att skicka ett e-postmeddelande till en specifik användare när en efterlevnadsutvärdering misslyckas. Du måste först skapa logikappen (med [Azure Logic Apps](../logic-apps/logic-apps-overview.md)) och sedan konfigurera utlösaren i en ny arbetsflödesautomation enligt förklaringen i Automatisera svar [på Security Center utlösare](workflow-automation.md).

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="Använda ändringar i utvärderingar av regelefterlevnad för att utlösa en arbetsflödesautomation" lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::




## <a name="faq---regulatory-compliance-dashboard"></a>Vanliga frågor och svar om instrumentpanelen för regelefterlevnad

- [Vilka standarder stöds i instrumentpanelen för efterlevnad?](#what-standards-are-supported-in-the-compliance-dashboard)
- [Varför visas vissa kontroller som nedtonade?](#why-do-some-controls-appear-grayed-out)
- [Hur tar jag bort en inbyggd standard, till exempel PCI-DSS, ISO 27001 eller SOC2 TSP från instrumentpanelen?](#how-can-i-remove-a-built-in-standard-like-pci-dss-iso-27001-or-soc2-tsp-from-the-dashboard)
- [Jag gjorde den föreslagna ändringar baserat på rekommendationen, men den visas inte på instrumentpanelen](#i-made-the-suggested-changed-based-on-the-recommendation-yet-it-isnt-being-reflected-in-the-dashboard)
- [Vilka behörigheter behöver jag för att få åtkomst till instrumentpanelen för efterlevnad?](#what-permissions-do-i-need-to-access-the-compliance-dashboard)
- [Instrumentpanelen för regelefterlevnad läses inte in för mig](#the-regulatory-compliance-dashboard-isnt-loading-for-me)
- [Hur visar jag en rapport om att skicka och misslyckas med kontroller per standard på min instrumentpanel?](#how-can-i-view-a-report-of-passing-and-failing-controls-per-standard-in-my-dashboard)
- [Hur kan jag ladda ned en rapport med efterlevnadsdata i ett annat format än PDF?](#how-can-i-download-a-report-with-compliance-data-in-a-format-other-than-pdf)
- [Hur kan jag skapa undantag för vissa av principerna i instrumentpanelen för regelefterlevnad?](#how-can-i-create-exceptions-for-some-of-the-policies-in-the-regulatory-compliance-dashboard)
- [Vilka Azure Defender-planer eller licenser behöver jag använda instrumentpanelen för regelefterlevnad?](#what-azure-defender-plans-or-licenses-do-i-need-to-use-the-regulatory-compliance-dashboard)

### <a name="what-standards-are-supported-in-the-compliance-dashboard"></a>Vilka standarder stöds i instrumentpanelen för efterlevnad?
Som standard visar instrumentpanelen för regelefterlevnad Azure Security Benchmark. Azure Security Benchmark är Microsofts författade, Azure-specifika riktlinjer för säkerhet och bästa praxis för efterlevnad baserat på vanliga ramverk för efterlevnad. Läs mer i [introduktionen till Azure Security Benchmark.](../security/benchmarks/introduction.md)

Om du vill spåra din efterlevnad med andra standarder måste du uttryckligen lägga till dem på instrumentpanelen.
 
Du kan lägga till standarder som Azure CIS 1.1.0 (ny), NIST SP 800-53 R4, NIST SP 800-171 R2, SWIFT CSP CSCF-v2020, UK Official och UK NHS, HIPAA HITRUST, Canada Federal PBMM, ISO 27001, SOC2-TSP och PCI-DSS 3.2.1.  
 
Fler standarder läggs till på instrumentpanelen och ingår i informationen i Anpassa uppsättningen standarder på instrumentpanelen för [regelefterlevnad.](update-regulatory-compliance-packages.md)

### <a name="why-do-some-controls-appear-grayed-out"></a>Varför visas vissa kontroller som nedtonade?
För varje efterlevnadsstandard på instrumentpanelen finns det en lista över standardkontrollerna. För tillämpliga kontroller kan du visa information om att skicka och misslyckas med utvärderingar.

Vissa kontroller är nedtonade. De här kontrollerna har inga associerade Security Center utvärderingar. Vissa kan vara procedur- eller processrelaterade och kan därför inte verifieras av Security Center. Vissa har inga automatiserade principer eller utvärderingar implementerade ännu, men kommer att ha det i framtiden. Och vissa kontroller kan vara plattformsansvaret enligt förklaringen [i Delat ansvar i molnet](../security/fundamentals/shared-responsibility.md). 

### <a name="how-can-i-remove-a-built-in-standard-like-pci-dss-iso-27001-or-soc2-tsp-from-the-dashboard"></a>Hur tar jag bort en inbyggd standard som PCI-DSS, ISO 27001 eller SOC2 TSP från instrumentpanelen? 
Om du vill anpassa instrumentpanelen för regelefterlevnad och endast fokusera på de standarder som gäller för dig kan du ta bort alla regelstandarder som visas och som inte är relevanta för din organisation. Om du vill ta bort en standard följer du anvisningarna i [Ta bort en standard från instrumentpanelen](update-regulatory-compliance-packages.md#remove-a-standard-from-your-dashboard).

### <a name="i-made-the-suggested-changed-based-on-the-recommendation-yet-it-isnt-being-reflected-in-the-dashboard"></a>Jag har gjort den föreslagna ändringar baserat på rekommendationen, men den visas inte på instrumentpanelen
När du har åtgärdat rekommendationerna väntar du i 12 timmar för att se ändringarna i dina efterlevnadsdata. Utvärderingar körs ungefär var 12:e timme, så du ser effekten på dina efterlevnadsdata först när utvärderingarna har körts.
 
### <a name="what-permissions-do-i-need-to-access-the-compliance-dashboard"></a>Vilka behörigheter behöver jag för att få åtkomst till instrumentpanelen för efterlevnad?
Om du vill visa efterlevnadsdata måste du minst ha **läsaråtkomst** till principefterlevnadsdata också. så enbart säkerhetsläsare räcker inte. Om du är global läsare i prenumerationen räcker det också.

Den minsta uppsättningen roller för åtkomst till instrumentpanelen och hanteringsstandarder är **Resursprincipdeltagare och** **Säkerhetsadministratör.**


### <a name="the-regulatory-compliance-dashboard-isnt-loading-for-me"></a>Instrumentpanelen för regelefterlevnad läses inte in för mig
Om du vill använda instrumentpanelen för regelefterlevnad måste Azure Security Center ha Azure Defender aktiverat på prenumerationsnivå. Om instrumentpanelen inte läses in korrekt kan du prova följande steg:

1. Rensa webbläsarens cacheminne.
1. Prova en annan webbläsare.
1. Försök att öppna instrumentpanelen från en annan nätverksplats.


### <a name="how-can-i-view-a-report-of-passing-and-failing-controls-per-standard-in-my-dashboard"></a>Hur visar jag en rapport om att skicka och misslyckas med kontroller per standard på min instrumentpanel?
På huvudinstrumentpanelen kan du se en rapport om att skicka och misslyckas med kontroller för (1) de "översta 4" lägsta efterlevnadsstandarderna på instrumentpanelen. Om du vill se status för alla kontroller som passerar/misslyckas väljer du (2) Visa alla ***x*** (där x är antalet standarder som du spårar). Ett kontextplan visar kompatibilitetsstatus för var och en av dina spårade standarder.

:::image type="content" source="media/security-center-compliance-dashboard/summaries-of-compliance-standards.png" alt-text="Sammanfattningsavsnittet på instrumentpanelen för regelefterlevnad":::


### <a name="how-can-i-download-a-report-with-compliance-data-in-a-format-other-than-pdf"></a>Hur kan jag ladda ned en rapport med efterlevnadsdata i ett annat format än PDF?
När du väljer **Ladda ned rapport** väljer du standard och format (PDF eller CSV). Den resulterande rapporten visar den aktuella uppsättningen prenumerationer som du har valt i portalens filter.

- PDF-rapporten visar en sammanfattningsstatus för den standard som du har valt
- CSV-rapporten ger detaljerade resultat per resurs, eftersom den relaterar till principer som är associerade med varje kontroll

För närvarande finns det inget stöd för att ladda ned en rapport för en anpassad princip. endast för de tillhandahållna regelstandarderna.


### <a name="how-can-i-create-exceptions-for-some-of-the-policies-in-the-regulatory-compliance-dashboard"></a>Hur kan jag skapa undantag för vissa principer i instrumentpanelen för regelefterlevnad?
För principer som är inbyggda i Security Center och ingår i säkerhetspoängen kan du skapa undantag för en eller flera resurser direkt i portalen enligt förklaringen i Undanta resurser och rekommendationer från din [säkerhetspoäng](exempt-resource.md).

För andra principer kan du skapa ett undantag direkt i själva principen genom att följa anvisningarna i [Azure Policy undantagsstruktur](../governance/policy/concepts/exemption-structure.md).


### <a name="what-azure-defender-plans-or-licenses-do-i-need-to-use-the-regulatory-compliance-dashboard"></a>Vilka Azure Defender eller licenser behöver jag använda instrumentpanelen för regelefterlevnad?
Om du har aktiverat något av Azure Defender-paketen för någon av dina Azure-resurstyper har du åtkomst till instrumentpanelen för regelefterlevnad med alla dess data i Security Center.





## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig Security Center instrumentpanelen för regelefterlevnad för att:

> [!div class="checklist"]
> * Visa och övervaka din efterlevnadsstatus för de standarder och föreskrifter som är viktiga för dig.
> * Förbättra din kompatibilitetsstatus genom att utföra relevanta rekommendationer och se dina kompatibilitetspoäng öka.

Instrumentpanelen för regelefterlevnad kan förenkla efterlevnadsprocessen avsevärt och avsevärt minska den tid som krävs för att samla in efterlevnadsbevis för din Azure-, hybrid- och multimolnmiljö.

Mer information finns på dessa relaterade sidor:

- [Anpassa uppsättningen standarder på instrumentpanelen för regelefterlevnad](update-regulatory-compliance-packages.md) – Lär dig hur du väljer vilka standarder som ska visas på instrumentpanelen för regelefterlevnad. 
- [Hantera säkerhetsrekommendationer i Azure Security Center – Lär](security-center-recommendations.md) dig hur du använder rekommendationer i Security Center att skydda dina Azure-resurser.