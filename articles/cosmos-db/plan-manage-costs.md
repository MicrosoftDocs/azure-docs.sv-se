---
title: Planera och hantera kostnader för Azure Cosmos DB
description: Lär dig att planera för och hantera kostnader för Azure Cosmos DB genom att använda kostnads analys i Azure Portal.
author: SnehaGunda
ms.author: sngun
ms.custom: subject-cost-optimization
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/05/2021
ms.openlocfilehash: 98e849791acd71ea8bf3ac9cb1949da9f562e749
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490836"
---
# <a name="plan-and-manage-costs-for-azure-cosmos-db"></a>Planera och hantera kostnader för Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

I den här artikeln beskrivs hur du kan planera och hantera kostnader för Azure Cosmos DB. Först använder du Azure Cosmos DB kapacitets Kalkylatorn för att beräkna din arbets belastnings kostnad innan du skapar några resurser. Senare kan du granska den uppskattade kostnaden och börja skapa dina resurser.

När du har börjat använda Azure Cosmos DB resurser använder du Cost Management funktionerna för att ställa in budgetar och övervaka kostnader. Du kan också granska prognostiserade kostnader och identifiera utgifts trender för att identifiera områden där du kanske vill handla. Kostnaderna för Azure Cosmos DB är bara en del av månads kostnaderna på din Azure-faktura. Även om den här artikeln förklarar hur du planerar för och hanterar kostnader för Azure Cosmos DB debiteras du för alla Azure-tjänster och-resurser som används i din Azure-prenumeration, inklusive tjänster från tredje part.

## <a name="prerequisites"></a>Förutsättningar

### <a name="provisioned-throughput-or-serverless"></a>Allokerat data flöde eller Server lös

Azure Cosmos DB stöder två typer av kapacitets lägen: [etablerade data flöden](set-throughput.md) och [Server](serverless.md)lös. Hur du debiteras för Azure Cosmos DB användningen varierar mycket mellan dessa två lägen, så det är viktigt att välja det som fungerar bäst för din arbets belastning. Mer information och rekommendationer om hur du gör detta finns i artikeln om [hur du väljer mellan etablerade data flöde och](throughput-serverless.md) en artikel utan server.

### <a name="cost-analysis"></a>Kostnadsanalys

Kostnads analys i Cost Management stöder de flesta typer av Azure-konton, men inte alla. Om du vill se hela listan med kontotyper som stöds kan du läsa [Förstå Cost Management-data](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Om du vill visa kostnads data måste du ha minst Läs behörighet för ett Azure-konto. Mer information om hur du får åtkomst till Azure Cost Management finns i [Tilldela åtkomst till data](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="estimate-costs-before-using-azure-cosmos-db"></a>Beräkna kostnader innan du använder Azure Cosmos DB

Azure Cosmos DB finns i två olika kapacitets lägen: etablerade data flöden och Server lös. Du kan utföra exakt samma databas åtgärder i båda lägena, men det sätt som du debiteras för dessa åtgärder skiljer sig åt.

### <a name="estimate-provisioned-throughput-costs"></a>Beräkna etablerade data flödes kostnader

Om du planerar att använda Azure Cosmos DB i det etablerade data flödes läget använder du [Azure Cosmos DB kapacitets kalkylatorn](https://cosmos.azure.com/capacitycalculator/) för att beräkna kostnader innan du skapar resurserna i ett Azure Cosmos-konto. Kapacitets kalkylatorn används för att få en uppskattning av det nödvändiga data flödet och kostnaden för din arbets belastning. Det är viktigt att konfigurera dina Azure Cosmos-databaser och behållare med rätt mängd etablerade data flöde eller [enheter för programbegäran (ru/s)](request-units.md)för din arbets belastning för att optimera kostnaderna och prestandan. Du måste ange information som t. ex. API-typ, antal regioner, objekt storlek, Läs-/skriv förfrågningar per sekund, totalt antal data som lagras för att få en kostnads uppskattning. Mer information om kapacitets kalkylatorn finns i artikeln om [uppskattning](estimate-ru-with-capacity-planner.md) .

Följande skärm bild visar data flödet och kostnads uppskattningen med hjälp av kapacitets Kalkylatorn:

:::image type="content" source="./media/plan-manage-costs/capacity-calculator-cost-estimate.png" alt-text="Kostnads uppskattning i Azure Cosmos DB kapacitets kalkylator":::

### <a name="estimate-serverless-costs"></a><a id="estimating-serverless-costs"></a> Beräkna kostnader utan Server

Om du planerar att använda Azure Cosmos DB i Server lös läge måste du uppskatta hur många enheter för [programbegäran](request-units.md) och GB lagrings utrymme som du kan använda varje månad. Du kan beräkna den nödvändiga mängden enheter för programbegäran genom att utvärdera antalet databas åtgärder som ska utfärdas under en månad och multiplicera deras belopp med motsvarande RU-kostnad. I följande tabell visas de uppskattade RU-avgifterna för vanliga databas åtgärder:

| Åtgärd | Beräknad kostnad | Kommentarer |
| --- | --- | --- |
| Skapa ett objekt | 5 ru: er | Genomsnittlig kostnad för ett 1 KB-objekt med färre än 5 egenskaper att indexera |
| Uppdatera ett objekt | 10 RU:er | Genomsnittlig kostnad för ett 1 KB-objekt med färre än 5 egenskaper att indexera |
| Läsa ett enskilt objekt med dess ID och partitionsnyckel (punkt-Läs) | 1 RU | Genomsnittlig kostnad för 1 KB-artikel |
| Ta bort ett objekt | 5 ru: er | |
| Köra en fråga | 10 RU:er | Genomsnittlig kostnad för en fråga som tar full nytta av [indexering](index-overview.md) och returnerar 100 resultat eller mindre |

> [!IMPORTANT] 
> Var uppmärksam på anteckningarna från tabellen ovan. För en mer exakt uppskattning av de faktiska kostnaderna för dina åtgärder kan du använda Azure Cosmos DB- [emulatorn](local-emulator.md) och [mäta den exakta ru-kostnaden för dina åtgärder](find-request-unit-charge.md). Även om Azure Cosmos DB emulatorn inte har stöd för Server lös, rapporterar den en standard avgift för RU för databas åtgärder och kan användas för denna uppskattning.

När du har beräknat det totala antalet enheter för programbegäran och GB lagrings utrymme som du sannolikt kommer att förbruka under en månad, returnerar följande formel din kostnads uppskattning: **([antal enheter för programbegäran]/1 000 000 * $0,25) + ([GB lagrings utrymme] * $0,25)**.

> [!NOTE]
> Kostnaderna som visas i föregående exempel är endast i demonstrations syfte. På [sidan med priser](https://azure.microsoft.com/pricing/details/cosmos-db/) finns den senaste pris informationen.

## <a name="understand-the-full-billing-model"></a>Förstå den fullständiga fakturerings modellen

Azure Cosmos DB körs på Azure-infrastruktur som periodiserar kostnader när du distribuerar nya resurser. Det är viktigt att förstå att det kan finnas andra ytterligare infrastruktur kostnader som kan uppstå.

### <a name="how-youre-charged-for-azure-cosmos-db"></a>Hur du debiteras för Azure Cosmos DB

När du skapar eller använder Azure Cosmos DB resurser kan du debiteras för följande mätare:

* **Databas åtgärder** – du debiteras för den baserat på de enheter för programbegäran (ru/s) som har allokerats eller för bruk ATS:
  * Standard (manuellt) tillhandahållet data flöde – du faktureras per timme för RU/s-etableringen på din behållare eller databas.
  * Autoskalning av allokerat data flöde – du faktureras baserat på det maximala antalet RU/s systemet skalas upp till per timme.

* **Förbrukad lagring** – du debiteras för IT baserat på den totala mängden lagrings utrymme (i GB) som används av dina data och index för en specifik timme.

Det finns ytterligare en avgift om du använder Azure Cosmos DB funktioner som säkerhets kopierings lagring, analys lagring, tillgänglighets zoner och skrivningar i flera regioner. I slutet av din fakturerings period summeras avgifterna för varje mätare. Din faktura eller faktura visar ett avsnitt för alla Azure Cosmos DB kostnader. Det finns ett separat rad objekt för varje mätare. Mer information finns i artikeln om [prissättnings modell](how-pricing-works.md) .

### <a name="using-azure-prepayment"></a>Använda Azure-förskotts betalning

Du kan betala för Azure Cosmos DB avgifter med din kredit för förskotts betalning i Azure. Du kan dock inte använda Azures förskotts kredit för att betala för avgifter för produkter och tjänster från tredje part, inklusive de som finns på Azure Marketplace.

## <a name="review-estimated-costs-in-the-azure-portal"></a>Gå igenom kostnadsuppskattningarna på Azure-portalen

När du börjar använda Azure Cosmos DB resurser från Azure Portal kan du se de uppskattade kostnaderna. Använd följande steg för att granska kostnads uppskattningen:

1. Logga in på Azure Portal och gå till ditt Azure Cosmos-konto.
1. Gå till **översikts** avsnittet.
1. Kontrol lera **kostnads** diagrammet längst ned. Det här diagrammet visar en uppskattning av din aktuella kostnad under en konfigurerbar tids period:
1. Skapa en ny behållare, till exempel en graf-behållare.
1. Mata in det data flöde som krävs för din arbets belastning, till exempel 400 RU/s. När du har matat in data flöde svärdet kan du se pris beräkningen så som visas på följande skärm bild:

   :::image type="content" source="./media/plan-manage-costs/cost-estimate-portal.png" alt-text="Kostnads uppskattning i Azure Portal":::

Om din Azure-prenumeration har en utgifts gräns hindrar Azure dig från att behöva spendera på ditt kredit belopp. När du skapar och använder Azure-resurser används dina krediter. När du når din kredit gräns inaktive ras de resurser som du har distribuerat för resten av fakturerings perioden. Du kan inte ändra kredit gränsen, men du kan ta bort den. Mer information om utgifts gränser finns i [utgifts gränsen för Azure](../cost-management-billing/manage/spending-limit.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

Du kan betala för Azure Cosmos DB avgifter med din Azure-förskotts betalning (tidigare kallat betalnings åtagande) kredit. Du kan dock inte använda Azures förskotts kredit för att betala för avgifter för produkter och tjänster från tredje part, inklusive de som finns på Azure Marketplace.

## <a name="monitor-costs"></a>Övervaka kostnader

När du använder resurser med Azure Cosmos DB debiteras du kostnader. Kostnader för resursanvändning varierar beroende på tidsintervaller (sekunder, minuter, timmar och dagar) eller genom att använda enhets användning. Så snart som användningen av Azure Cosmos DB startar kan kostnader uppstå och du kan se dem i fönstret [kostnads analys](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) i Azure Portal.

När du använder kostnads analys kan du Visa Azure Cosmos DB kostnader i grafer och tabeller i olika tidsintervall. Några exempel är per dag, aktuell, föregående månad och år. Du kan också Visa kostnader för budgetar och prognostiserade kostnader. Om du växlar till längre vyer över tid kan du identifiera utgifts trender och se var överförbrukningen kan ha inträffat. Om du har skapat budgetar kan du också enkelt se var de överskreds.

Så här visar du Azure Cosmos DB kostnader i kostnads analys:

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Öppna omfånget i Azure Portal och välj **kostnads analys** i menyn. Gå till exempel till **prenumerationer**, Välj en prenumeration i listan och välj sedan  **kostnads analys** i menyn. Välj **omfång** för att växla till ett annat omfång i kostnads analys.

1. Som standard visas kostnaden för alla tjänster i det första Ring diagrammet. Markera ytan i diagrammet med etiketten "Azure Cosmos DB".

1. Om du vill begränsa kostnaderna för en enskild tjänst, till exempel Azure Cosmos DB, väljer du **Lägg till filter** och väljer sedan **tjänst namn**. Välj sedan **Azure Cosmos DB** i listan. Här är ett exempel som visar kostnader för just Azure Cosmos DB:

   :::image type="content" source="./media/plan-manage-costs/cost-analysis-pane.png" alt-text="Övervaka kostnader med fönstret kostnads analys":::

I föregående exempel visas den aktuella kostnaden för Azure Cosmos DB för månadens månad. Diagrammen innehåller också Azure Cosmos DB kostnader per plats och per resurs grupp.

## <a name="create-budgets"></a>Skapa budgetar

Du kan skapa [budgetar](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) för att hantera kostnader och skapa [aviseringar](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) som automatiskt meddelar mottagarna om kostnadsavvikelser och risker för överförbrukning. Aviseringar baseras på utgifter jämfört med budget- och kostnadströsklar. Budgetar och aviseringar skapas för Azure-prenumerationer och resurs grupper, så de är användbara som en del av en övergripande kostnads övervaknings strategi. 

Budgetar kan skapas med filter för vissa resurser eller tjänster i Azure om du vill ha mer detaljerad information i din övervakning. Filter hjälper till att se till att du inte av misstag skapar nya resurser som kostar dig ytterligare pengar. Mer information om filter alternativen när du skapar en budget finns i alternativ för [grupper och filter](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Exportera kostnadsdata

Du kan också [Exportera dina kostnads data](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) till ett lagrings konto. Detta är användbart när du behöver eller andra för att utföra ytterligare data analyser för kostnader. Ett ekonomi team kan till exempel analysera data med hjälp av Excel eller Power BI. Du kan exportera dina kostnader enligt ett dags-, vecko-eller månads schema och ange ett anpassat datum intervall. Att exportera kostnads data är det rekommenderade sättet att hämta kostnads data uppsättningar.

## <a name="other-ways-to-manage-and-reduce-costs"></a>Andra sätt att hantera och minska kostnaderna

Här följer några metod tips som du kan använda för att minska kostnaderna:

* Optimera insamlad [data flödes kostnad](optimize-cost-throughput.md) – den här artikeln beskriver metod tipsen för att optimera dina data flödes kostnader. Den beskriver när du ska etablera data flöde på behållar nivån jämfört med din arbets belastnings typ, beroende på databas nivå.

* [Optimera begär ande kostnad](optimize-cost-reads-writes.md) – den här artikeln beskriver hur Läs-och skriv förfrågningar översätts till enheter för programbegäran och hur du optimerar kostnaden för dessa begär Anden.

* [Optimera lagrings kostnad](optimize-cost-storage.md) – lagrings kostnaden debiteras enligt förbrukningen. Lär dig hur du optimerar lagrings kostnaden med objekt storlek, indexerings princip med hjälp av funktioner som att ändra feed och tid till Live.

* [Optimera kostnader för flera regioner](optimize-cost-regions.md) – om du har en eller flera underutnyttjade Läs regioner kan du vidta åtgärder för att göra den maximala användningen av ru: er i Läs regioner genom att använda ändra feed från läsnings regionen eller flytta den till en annan sekundär om överutnyttjad.

* [Optimera utvecklings-/test kostnad](optimize-dev-test.md) – lär dig hur du optimerar din utvecklings kostnad genom att använda den lokala emulatorn, den Azure Cosmos DB kostnads fria nivån, Azures kostnads fria konto och några andra alternativ.

* [Optimera kostnader med reserverad kapacitet](cosmos-db-reserved-capacity.md) – lär dig hur du använder reserverad kapacitet för att spara pengar genom att göra en reservation för Azure Cosmos DB resurser i ett år eller tre år.

## <a name="next-steps"></a>Nästa steg

I följande artiklar finns mer information om hur priser fungerar i Azure Cosmos DB:

* [Prismodell i Azure Cosmos DB](how-pricing-works.md)
* Lär dig [hur du optimerar din moln investering med Azure Cost Management](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
* Lär dig mer om att hantera kostnader med [kostnads analys](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
* Lär dig mer om att [förhindra oväntade kostnader](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
* Ta den [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) guidade utbildningen.