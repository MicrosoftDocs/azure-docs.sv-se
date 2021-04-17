---
title: Planera för att hantera kostnader för Azure Synapse Analytics
description: Lär dig hur du planerar och hanterar kostnader för Azure Synapse Analytics med hjälp av kostnadsanalys i Azure Portal.
author: julieMSFT
ms.author: jrasnick
ms.custom: subject-cost-optimization
ms.service: synapse-analytics
ms.subservice: overview
ms.topic: how-to
ms.date: 12/09/2020
ms.openlocfilehash: 15ac6ce6a1a49bbbd15849adec373dd0fcd42c10
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568530"
---
# <a name="plan-and-manage-costs-for-azure-synapse-analytics"></a>Planera och hantera kostnader för Azure Synapse Analytics

Den här artikeln beskriver hur du planerar för och hanterar kostnader för Azure Synapse Analytics. Först använder du priskalkylatorn för Azure för att planera för Azure Synapse innan du lägger till resurser för tjänsten för att beräkna kostnader. När du sedan lägger till Azure Synapse bör du granska de uppskattade kostnaderna.

När du har börjat använda Azure Synapse resurser kan du använda Cost Management för att ange budgetar och övervaka kostnader. Du kan också granska prognostiserade kostnader och identifiera utgiftstrender för att identifiera områden där du kanske vill agera. Kostnader för Azure Synapse är bara en del av de månatliga kostnaderna på din Azure-faktura. Även om den här artikeln förklarar hur du planerar för och hanterar kostnader för Azure Synapse debiteras du för alla Azure-tjänster och -resurser som används i din Azure-prenumeration, inklusive tjänster från tredje part.

## <a name="prerequisites"></a>Förutsättningar

Kostnadsanalys i Cost Management stöder de flesta Typer av Azure-konton, men inte alla. Om du vill se hela listan med kontotyper som stöds kan du läsa [Förstå Cost Management-data](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Om du vill visa kostnadsdata behöver du minst läsbehörighet för ett Azure-konto. Mer information om hur du får åtkomst till Azure Cost Management finns i [Tilldela åtkomst till data](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="estimate-costs-before-using-azure-synapse-analytics"></a>Beräkna kostnader innan du använder Azure Synapse Analytics

Använd [priskalkylatorn för Azure för](https://azure.microsoft.com/pricing/calculator/) att beräkna kostnader innan du lägger till Azure Synapse Analytics.

Azure Synapse har olika resurser som har olika avgifter som visas i kostnadsuppskattningen nedan. 

![Exempel som visar beräknad kostnad i priskalkylatorn för Azure](./media/plan-manage-costs/cost-estimate.png)

## <a name="understand-the-full-billing-model-for-azure-synapse-analytics"></a>Förstå den fullständiga faktureringsmodellen för Azure Synapse Analytics

Azure Synapse körs på Azure-infrastruktur som ackumulerar kostnader tillsammans med Azure Synapse när du distribuerar den nya resursen. Det är viktigt att förstå att ytterligare infrastruktur kan ackumulera kostnader. Du måste hantera den kostnaden när du gör ändringar i distribuerade resurser. 

### <a name="costs-that-typically-accrue-with-azure-synapse-analytics"></a>Kostnader som vanligtvis ackumuleras med Azure Synapse Analytics

När du skapar resurser för Azure Synapse skapas även resurser för andra Azure-tjänster. De omfattar:

- Data Lake Storage Gen2

 ### <a name="costs-might-accrue-after-resource-deletion"></a>Kostnader kan tillkomma efter resursborttagning

När du Azure Synapse bort resurserna kan följande resurser finnas kvar. De fortsätter att ackumulera kostnader tills du tar bort dem.

- Data Lake Storage Gen2

### <a name="using-azure-prepayment-credit-with-azure-synapse"></a>Använda Azure-förskottsbetalningskredit med Azure Synapse 

Du kan betala för Azure Synapse med din Azure-förskottsbetalningskredit (kallades tidigare för ekonomiskt åtagande). Du kan dock inte använda Azure-förskottsbetalningskrediten för att betala för avgifter för produkter och tjänster från tredje part, inklusive de från Azure Marketplace.

## <a name="review-estimated-costs-in-the-azure-portal"></a>Gå igenom kostnadsuppskattningarna på Azure-portalen

När du skapar resurser för Azure Synapse Analytics ser du uppskattade kostnader. En arbetsyta har en serverlös SQL-pool som skapats med arbetsytan. Serverlös SQL-pool debiteras inte förrän du kör frågor. Andra resurser, till exempel dedikerade SQL-pooler och serverlösa Apache Spark-pooler, måste skapas i arbetsytan.

Så här skapar du <ResourceName> en och visar det uppskattade priset:

1. Gå till tjänsten i Azure Portal.
2. Skapa resursen.
3. Granska det uppskattade priset som visas i sammanfattningen.
4. Slutför skapandet av resursen.

![Exempel som visar uppskattade kostnader när du skapar en resurs](./media/plan-manage-costs/create-workspace-cost.png)


Om din Azure-prenumeration har en utgiftsgräns förhindrar Azure dig från att spendera mer än ditt kreditbelopp. När du skapar och använder Azure-resurser används dina krediter. När du når din kreditgräns inaktiveras de resurser som du har distribuerat under resten av faktureringsperioden. Du kan inte ändra kreditgränsen, men du kan ta bort den. Mer information om utgiftsgränser finns i [Utgiftsgräns i Azure.](../cost-management-billing/manage/spending-limit.md)

## <a name="monitor-costs"></a>Övervaka kostnader

När du använder Azure Synapse resurser medför det kostnader. Kostnaderna för Azure-resursanvändningsenhet varierar efter tidsintervall (sekunder, minuter, timmar och dagar) eller efter enhetsanvändning (byte, megabyte och så vidare.) Så snart du börjar använda resurser i Azure Synapse uppstår kostnader och du kan se kostnaderna i [kostnadsanalysen](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

När du använder kostnadsanalys visar du kostnader för Azure Synapse Analytics i grafer och tabeller för olika tidsintervall. Några exempel är per dag, aktuell och föregående månad och år. Du kan också visa kostnader mot budgetar och prognostiserade kostnader. Om du växlar till längre vyer över tid kan du identifiera utgiftstrender. Och du ser var överspending kan ha inträffat. Om du har skapat budgetar kan du också enkelt se var de har överskridits.

Så här visar Azure Synapse kostnader i kostnadsanalys:

1. Logga in på Azure-portalen.
2. Öppna omfånget, antingen prenumerationen eller resursgruppen, i Azure Portal **och välj Kostnadsanalys** på menyn. Du kan till exempel **gå till** Prenumerationer, välja en prenumeration i listan och sedan  **välja Kostnadsanalys** i menyn. Välj **Omfång** för att växla till ett annat omfång i kostnadsanalysen.
3. Som standard visas kostnaden för tjänster i det första ringdiagrammet. Välj området i diagrammet med etiketten Azure Synapse.

Faktiska månadskostnader visas när du först öppnar kostnadsanalysen. Här är ett exempel som visar alla månatliga användningskostnader.

![Exempel som visar ackumulerade kostnader för en prenumeration](./media/plan-manage-costs/actual-monthly-costs.png)

- Om du vill begränsa kostnaderna för en enskild tjänst, till Azure Synapse väljer du **Lägg till filter** och sedan **Tjänstnamn.** Välj sedan **Azure Synapse Analytics**.

Här är ett exempel som visar kostnader för Azure Synapse.

![Exempel som visar ackumulerade kostnader för ServiceName](./media/plan-manage-costs/filtered-monthly-costs.png)

I föregående exempel visas den aktuella kostnaden för tjänsten. Kostnader per Azure-regioner (platser) Azure Synapse kostnader per resursgrupp visas också. Härifrån kan du utforska kostnader på egen hand.

## <a name="create-budgets"></a>Skapa budgetar

Du kan skapa [budgetar](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) för att hantera kostnader och skapa [aviseringar](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) som automatiskt meddelar mottagarna om kostnadsavvikelser och risker för överförbrukning. Aviseringar baseras på utgifter jämfört med budget- och kostnadströsklar. Budgetar och aviseringar skapas för Azure-prenumerationer och resursgrupper, så de är användbara som en del av en övergripande strategi för kostnadsövervakning. 

Budgetar kan skapas med filter för specifika resurser eller tjänster i Azure om du vill ha mer kornighet i övervakningen. Filter hjälper till att säkerställa att du inte oavsiktligt skapar nya resurser som kostar ytterligare pengar. Mer information om filteralternativen när du skapar en budget finns i [Alternativ för gruppering och filter.](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

## <a name="export-cost-data"></a>Exportera kostnadsdata

Du kan också [exportera kostnadsdata till](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) ett lagringskonto. Detta är användbart när du behöver eller andra för att göra ytterligare dataanalys för kostnader. Ett ekonomiteam kan till exempel analysera data med hjälp av Excel eller Power BI. Du kan exportera dina kostnader enligt ett schema för varje dag, vecka eller månad och ange ett anpassat datumintervall. Att exportera kostnadsdata är det rekommenderade sättet att hämta kostnadsdatauppsättningar.


## <a name="other-ways-to-manage-and-reduce-costs-for-azure-synapse"></a>Andra sätt att hantera och minska kostnader för Azure Synapse 

### <a name="serverless-sql-pool"></a>Serverlös SQL-pool

Mer information om kostnader för serverlös SQL-pool finns i [Kostnadshantering för serverlös SQL-pool i Azure Synapse Analytics](./sql/data-processed.md)

### <a name="dedicated-sql-pool"></a>Dedikerad SQL-pool

Du kan kontrollera kostnaderna för en dedikerad SQL-pool genom att pausa resursen när den inte används. Om du till exempel inte kommer att använda databasen under natten och på helger kan du pausa den under dessa tider och återuppta den under dagen. Mer information finns i [Pausa och återuppta beräkning i dedikerad SQL-pool via Azure Portal](./sql-data-warehouse/pause-and-resume-compute-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

### <a name="serverless-apache-spark-pool"></a>Serverlös Apache Spark pool

Du kan kontrollera kostnaderna för din serverlösa Apache Spark-pool genom att aktivera funktionen för Apache Spark pausa utan server och ange ett tidsgränsvärde.  När du Synapse Studio för din utveckling skickar studio ett keep alive-meddelande för att hålla sessionen aktiv, vilket också kan konfigureras, så ange ett kort timeout-värde för automatisk paus.  När du är klar stänger du sessionen så pausar Apache Spark automatiskt när tidsgränsvärdet nås.
 
Under utvecklingen skapar du flera Apache Spark-pooldefinitioner av olika storlekar.  Att Apache Spark en pooldefinition är kostnadsfritt och du debiteras bara för användning.  Apache Spark användning i Azure Synapse debiteras per timme med virtuella kärnor och per minut.  Använd till exempel små poolstorlekar för kodutveckling och -verifiering när du använder större poolstorlekar för prestandatestning.


### <a name="data-integration---pipelines-and-data-flows"></a>Dataintegrering – pipelines och dataflöden 

Mer information om kostnader för dataintegrering finns [i Planera och hantera kostnader för Azure Data Factory](../data-factory/plan-manage-costs.md)

## <a name="next-steps"></a>Nästa steg

- Lär [dig hur du optimerar din molninvestering med Azure Cost Management](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Läs mer om att hantera kostnader med [kostnadsanalys.](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
- Lär dig mer om hur [du förhindrar oväntade kostnader.](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
- Gå den [guidade Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) kursen.
- Lär dig mer om att planera och hantera [kostnader för Azure Machine Learning](../machine-learning/concept-plan-manage-cost.md)