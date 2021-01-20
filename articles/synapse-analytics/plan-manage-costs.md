---
title: Planera för att hantera kostnader för Azure Synapse Analytics
description: Lär dig att planera för och hantera kostnader för Azure Synapse Analytics genom att använda kostnads analys i Azure Portal.
author: julieMSFT
ms.author: jrasnick
ms.custom: subject-cost-optimization
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 12/09/2020
ms.openlocfilehash: c7a0be6f1d402cc994532ab4bc5a5d0ea39bc8b7
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2021
ms.locfileid: "98599041"
---
# <a name="plan-and-manage-costs-for-azure-synapse-analytics"></a>Planera och hantera kostnader för Azure Synapse Analytics

Den här artikeln beskriver hur du planerar för och hanterar kostnader för Azure Synapse Analytics. Först använder du pris Kalkylatorn för Azure för att planera för Azure Synapse-kostnader innan du lägger till några resurser för tjänsten för att beräkna kostnaderna. När du lägger till Azure Synapse-resurser kan du granska de uppskattade kostnaderna.

När du har börjat använda Azure Synapse-resurser använder du Cost Management funktioner för att ställa in budgetar och övervaka kostnader. Du kan också granska prognostiserade kostnader och identifiera utgifts trender för att identifiera områden där du kanske vill handla. Kostnaderna för Azure Synapse är bara en del av månads kostnaderna på din Azure-faktura. Även om den här artikeln förklarar hur du planerar för och hanterar kostnader för Azure-Synapse debiteras du för alla Azure-tjänster och-resurser som används i din Azure-prenumeration, inklusive tjänster från tredje part.

## <a name="prerequisites"></a>Förutsättningar

Kostnads analys i Cost Management stöder de flesta typer av Azure-konton, men inte alla. Om du vill se hela listan med kontotyper som stöds kan du läsa [Förstå Cost Management-data](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Om du vill visa kostnads data måste du ha minst Läs behörighet för ett Azure-konto. Mer information om hur du får åtkomst till Azure Cost Management finns i [Tilldela åtkomst till data](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="estimate-costs-before-using-azure-synapse-analytics"></a>Beräkna kostnader innan du använder Azure Synapse Analytics

Använd [pris Kalkylatorn för Azure](https://azure.microsoft.com/pricing/calculator/) för att beräkna kostnader innan du lägger till Azure Synapse Analytics.

Azure Synapse har olika resurser som har olika kostnader som visas i kostnads beräkningen nedan. 

![Exempel som visar uppskattad kostnad i pris Kalkylatorn för Azure](./media/plan-manage-costs/cost-estimate.png)

## <a name="understand-the-full-billing-model-for-azure-synapse-analytics"></a>Förstå den fullständiga fakturerings modellen för Azure Synapse Analytics

Azure Synapse körs på Azure-infrastruktur som periodiserar kostnader tillsammans med Azure Synapse när du distribuerar den nya resursen. Det är viktigt att förstå att ytterligare infrastruktur kan periodisera kostnader. Du måste hantera den kostnaden när du gör ändringar i distribuerade resurser. 

### <a name="costs-that-typically-accrue-with-azure-synapse-analytics"></a>Kostnader som vanligt vis periodiseras med Azure Synapse Analytics

När du skapar resurser för Azure-Synapse skapas även resurser för andra Azure-tjänster. De omfattar:

- Data Lake Storage Gen2

 ### <a name="costs-might-accrue-after-resource-deletion"></a>Kostnader kan uppstå efter borttagning av resurs

När du har tagit bort Azure Synapse-resurser kan följande resurser fortsätta att existera. De fortsätter att Periodisera kostnader tills du tar bort dem.

- Data Lake Storage Gen2

### <a name="using-azure-prepayment-credit-with-azure-synapse"></a>Använda Azures förskotts kredit med Azure Synapse 

Du kan betala för Azure Synapse-avgifter med din Azure-förskotts betalning (tidigare kallat betalnings åtagande) kredit. Du kan dock inte använda Azures förskotts kredit för att betala för avgifter för produkter och tjänster från tredje part, inklusive de som finns på Azure Marketplace.

## <a name="review-estimated-costs-in-the-azure-portal"></a>Gå igenom kostnadsuppskattningarna på Azure-portalen

När du skapar resurser för Azure Synapse Analytics visas uppskattade kostnader. En arbets yta har en server lös SQL-pool som skapats med arbets ytan. SQL-poolen utan server debiteras inte förrän du kör frågor. Andra resurser, till exempel dedikerade SQL-pooler och Server lös Apache Spark pooler, måste skapas i arbets ytan.

Så här skapar du ett <ResourceName> och visar det beräknade priset:

1. Navigera till tjänsten i Azure Portal.
2. Skapa resursen.
3. Granska det beräknade priset som visas i sammanfattningen.
4. Slutför skapandet av resursen.

![Exempel som visar uppskattade kostnader när du skapar en resurs](./media/plan-manage-costs/create-workspace-cost.png)


Om din Azure-prenumeration har en utgifts gräns hindrar Azure dig från att behöva spendera på ditt kredit belopp. När du skapar och använder Azure-resurser används dina krediter. När du når din kredit gräns inaktive ras de resurser som du har distribuerat för resten av fakturerings perioden. Du kan inte ändra kredit gränsen, men du kan ta bort den. Mer information om utgifts gränser finns i [utgifts gränsen för Azure](../cost-management-billing/manage/spending-limit.md).

## <a name="monitor-costs"></a>Övervaka kostnader

När du använder Azure Synapse-resurser debiteras du kostnader. Kostnaderna för Azure Resource Usage varierar med tidsintervall (sekunder, minuter, timmar och dagar) eller per enhets användning (byte, megabyte osv.) Så snart du börjar använda resurser i Azure-Synapse uppstår kostnader och du kan se kostnaderna i [kostnads analys](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

När du använder kostnads analys kan du Visa kostnader för Azure Synapse Analytics i grafer och tabeller för olika tidsintervall. Några exempel är per dag, innevarande och föregående månad och år. Du ser också kostnader för budget och prognostiserade kostnader. Att växla till längre vyer över tid kan hjälpa dig att identifiera utgifts trender. Och du ser var överförbrukning kan ha inträffat. Om du har skapat budgetar kan du också enkelt se var de överskreds.

Så här visar du kostnader för Azure Synapse i kostnads analys:

1. Logga in på Azure-portalen.
2. Öppna omfattningen, antingen prenumerationen eller resurs gruppen, i Azure Portal och välj **kostnads analys** i menyn. Gå till exempel till **prenumerationer**, Välj en prenumeration i listan och välj sedan  **kostnads analys** i menyn. Välj **omfång** för att växla till ett annat omfång i kostnads analys.
3. Som standard visas kostnaden för tjänster i det första Ring diagrammet. Välj ett av de områden i diagrammet som är märkt med Azure Synapse.

De faktiska månads kostnaderna visas när du först öppnar kostnads analys. Här är ett exempel som visar alla månatliga användnings kostnader.

![Exempel som visar ackumulerade kostnader för en prenumeration](./media/plan-manage-costs/actual-monthly-costs.png)

- Om du vill begränsa kostnaderna för en enskild tjänst, till exempel Azure Synapse, väljer du **Lägg till filter** och väljer sedan **tjänst namn**. Välj sedan **Azure Synapse Analytics**.

Här är ett exempel som visar kostnader för bara Azure-Synapse.

![Exempel som visar ackumulerade kostnader för ServiceName](./media/plan-manage-costs/filtered-monthly-costs.png)

I föregående exempel visas den aktuella kostnaden för tjänsten. Kostnader per Azure-regioner (platser) och Azure Synapse-kostnader per resurs grupp visas också. Härifrån kan du utforska kostnaderna på egen hand.

## <a name="create-budgets"></a>Skapa budgetar

Du kan skapa [budgetar](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) för att hantera kostnader och skapa [aviseringar](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) som automatiskt meddelar mottagarna om kostnadsavvikelser och risker för överförbrukning. Aviseringar baseras på utgifter jämfört med budget- och kostnadströsklar. Budgetar och aviseringar skapas för Azure-prenumerationer och resurs grupper, så de är användbara som en del av en övergripande kostnads övervaknings strategi. 

Budgetar kan skapas med filter för vissa resurser eller tjänster i Azure om du vill ha mer detaljerad information i din övervakning. Filter hjälper till att se till att du inte av misstag skapar nya resurser som kostar dig ytterligare pengar. Mer information om filter alternativen när du skapar en budget finns i [alternativ för grupper och filter](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Exportera kostnadsdata

Du kan också [Exportera dina kostnads data](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) till ett lagrings konto. Detta är användbart när du behöver eller andra för att utföra ytterligare data analyser för kostnader. Ett ekonomi team kan till exempel analysera data med hjälp av Excel eller Power BI. Du kan exportera dina kostnader enligt ett dags-, vecko-eller månads schema och ange ett anpassat datum intervall. Att exportera kostnads data är det rekommenderade sättet att hämta kostnads data uppsättningar.


## <a name="other-ways-to-manage-and-reduce-costs-for-azure-synapse"></a>Andra sätt att hantera och minska kostnaderna för Azure Synapse 

### <a name="serverless-sql-pool"></a>Serverlös SQL-pool

Om du vill veta mer om kostnader för Server lös SQL-pool se [kostnads hantering för Server lös SQL-pool i Azure Synapse Analytics](./sql/data-processed.md)

### <a name="dedicated-sql-pool"></a>Dedikerad SQL-pool

Du kan kontrol lera kostnaderna för en dedikerad SQL-pool genom att pausa resursen när den inte används. Om du till exempel inte använder databasen under natten och på helger, kan du pausa den under dessa tider och återuppta den under dagen. Mer information finns i [pausa och återuppta beräkning i dedikerad SQL-pool via Azure Portal](./sql-data-warehouse/pause-and-resume-compute-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

<!-- ### Serverless Apache Spark pool -->

### <a name="data-integration---pipelines-and-data-flows"></a>Data integrering – pipelines och data flöden 

Mer information om kostnader för data integrering finns i [planera och hantera kostnader för Azure Data Factory](../data-factory/plan-manage-costs.md)

## <a name="next-steps"></a>Nästa steg

- Lär dig [hur du optimerar din moln investering med Azure Cost Management](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Lär dig mer om att hantera kostnader med [kostnads analys](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Lär dig mer om att [förhindra oväntade kostnader](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Ta den [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) guidade utbildningen.
- Lär dig mer om planering och hantering av kostnader för [Azure Machine Learning](../machine-learning/concept-plan-manage-cost.md)