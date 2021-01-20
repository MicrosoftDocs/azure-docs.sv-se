---
title: Planera och hantera kostnader för Azure SQL Database
description: Lär dig att planera för och hantera kostnader för Azure SQL Database genom att använda kostnads analys i Azure Portal.
author: stevestein
ms.author: sstein
ms.custom: subject-cost-optimization
ms.service: sql-database
ms.topic: how-to
ms.date: 01/15/2021
ms.openlocfilehash: 8d36166a67e683dd7f15eef18d589562b39ba568
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2021
ms.locfileid: "98600624"
---
# <a name="plan-and-manage-costs-for-azure-sql-database"></a>Planera och hantera kostnader för Azure SQL Database

I den här artikeln beskrivs hur du planerar för och hanterar kostnader för Azure SQL Database. Först använder du pris Kalkylatorn för Azure för att lägga till Azure-resurser och granska de uppskattade kostnaderna. När du har börjat använda Azure SQL Database resurser använder du Cost Management funktioner för att ställa in budgetar och övervaka kostnader. Du kan också granska prognostiserade kostnader och identifiera utgifts trender för att identifiera områden där du kanske vill handla. Kostnaderna för Azure SQL Database är bara en del av månads kostnaderna på din Azure-faktura. Även om den här artikeln förklarar hur du planerar för och hanterar kostnader för Azure SQL Database debiteras du för alla Azure-tjänster och-resurser som används i din Azure-prenumeration, inklusive alla tjänster från tredje part.


## <a name="prerequisites"></a>Förutsättningar

Kostnads analys stöder de flesta typer av Azure-konton, men inte alla. Om du vill se hela listan med kontotyper som stöds kan du läsa [Förstå Cost Management-data](../../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Om du vill visa kostnads data måste du ha minst Läs behörighet för ett Azure-konto. 

Mer information om hur du får åtkomst till Azure Cost Management finns i [Tilldela åtkomst till data](../../cost-management/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).


## <a name="sql-database-initial-cost-considerations"></a>SQL Database inledande kostnads överväganden

När du arbetar med Azure SQL Database finns det flera kostnads besparingar att tänka på:


### <a name="vcore-or-dtu-purchasing-models"></a>vCore-eller DTU-inköps modeller 

Azure SQL Database stöder två inköps modeller: vCore och DTU. Hur du får debiteras varierar mellan inköps modellerna, så det är viktigt att förstå vilken modell som passar bäst för din arbets belastning när du planerar och överväger kostnader. Information om vCore-och DTU-inköps modeller finns i [Välj mellan vCore-och DTU-inköps modellerna](purchasing-models.md).


### <a name="provisioned-or-serverless"></a>Etablerad eller utan Server

I vCore inköps modell stöder Azure SQL Database också två typer av beräknings nivåer: etablerade data flöden och Server lös. Hur du debiteras för varje beräknings nivå varierar så det är viktigt att förstå vad som fungerar bäst för din arbets belastning när du planerar och överväger kostnader. Mer information finns i [Översikt över vCore-modeller – beräknings nivåer](service-tiers-vcore.md#compute-tiers).

I den etablerade beräknings nivån för den vCore-baserade inköps modellen kan du byta ut dina befintliga licenser för rabatterat pris. Mer information finns i [Azure Hybrid-förmån (AHB)](../azure-hybrid-benefit.md).

### <a name="elastic-pools"></a>Elastiska pooler

För miljöer med flera databaser med varierande och oförutsägbara användnings krav kan elastiska pooler ge kostnads besparingar jämfört med att tillhandahålla samma mängd enkla databaser. Mer information finns i [elastiska pooler](elastic-pool-overview.md).

## <a name="estimate-azure-sql-database-costs"></a>Uppskatta Azure SQL Database kostnader

Använd [pris Kalkylatorn för Azure](https://azure.microsoft.com/pricing/calculator/) för att beräkna kostnader för olika Azure SQL Database konfigurationer. Informationen och priserna i följande bild är till exempel endast syfte:

:::image type="content" source="media/cost-management/pricing-calc.png" alt-text="Exempel på pris kalkylator Azure SQL Database":::

Du kan också uppskatta hur olika bevarande princip alternativ påverkar kostnaden. Informationen och priserna i följande bild är till exempel endast syfte:

:::image type="content" source="media/cost-management/backup-storage.png" alt-text="Exempel på Azure SQL Database pris kalkylator för lagring":::


## <a name="understand-the-full-billing-model-for-azure-sql-database"></a>Förstå den fullständiga fakturerings modellen för Azure SQL Database

Azure SQL Database körs på Azure-infrastruktur som periodiserar kostnader tillsammans med Azure SQL Database när du distribuerar den nya resursen. Det är viktigt att förstå att ytterligare infrastruktur kan periodisera kostnader. Du måste hantera den kostnaden när du gör ändringar i distribuerade resurser. 


Azure SQL Database (med undantag för Server lös) faktureras enligt ett förutsägbart Tim pris. Om SQL-databasen är aktiv i mindre än en timme debiteras du för varje timme som databasen finns med den högsta tjänst nivån som valts, etablerad lagring och IO som tillämpades under den timmen, oavsett användning eller om databasen var aktiv i mindre än en timme.


### <a name="using-monetary-credit-with-azure-sql-database"></a>Använda monetär kredit med Azure SQL Database

Du kan betala för Azure SQL Database avgifter med din Azure-förskotts betalning (tidigare kallat betalnings åtagande) kredit. Du kan dock inte använda Azures förskotts kredit för att betala för avgifter för produkter och tjänster från tredje part, inklusive de som finns på Azure Marketplace.

## <a name="review-estimated-costs-in-the-azure-portal"></a>Gå igenom kostnadsuppskattningarna på Azure-portalen

När du går igenom processen med att skapa en Azure SQL Database kan du se de beräknade kostnaderna under konfigurationen av beräknings nivån. 

För att få åtkomst till den här skärmen väljer du **Konfigurera databas** på fliken **grundläggande** på sidan **skapa SQL Database** . Informationen och priserna i följande bild är till exempel endast syfte:

  :::image type="content" source="media/cost-management/cost-estimate.png" alt-text="Exempel på hur kostnads uppskattning visas i Azure Portal":::



Om din Azure-prenumeration har en utgifts gräns hindrar Azure dig från att behöva spendera på ditt kredit belopp. När du skapar och använder Azure-resurser används dina krediter. När du når din kredit gräns inaktive ras de resurser som du har distribuerat för resten av fakturerings perioden. Du kan inte ändra kredit gränsen, men du kan ta bort den. Mer information om utgifts gränser finns i [utgifts gränsen för Azure](https://docs.microsoft.com/azure/billing/billing-spending-limit).

## <a name="monitor-costs"></a>Övervaka kostnader

När du börjar använda Azure SQL Database kan du se de uppskattade kostnaderna i portalen. Använd följande steg för att granska kostnads uppskattningen:

1. Logga in på Azure Portal och gå till resurs gruppen för Azure SQL-databasen. Du hittar resurs gruppen genom att gå till din databas och välja **resurs grupp** i avsnittet **Översikt** .
1. I menyn väljer du **kostnads analys**.
1. Visa **ackumulerade kostnader** och ange diagrammet längst ned till **tjänst namn**. Det här diagrammet visar en uppskattning av dina nuvarande SQL Database-kostnader. Om du vill begränsa kostnaderna för hela sidan till Azure SQL Database väljer du **Lägg till filter** och väljer sedan **Azure SQL Database**. Informationen och priserna i följande bild är till exempel endast syfte:

   :::image type="content" source="media/cost-management/cost-analysis.png" alt-text="Exempel som visar ackumulerade kostnader i Azure Portal":::

Härifrån kan du utforska kostnaderna på egen hand. Mer information om de olika inställningarna för kostnads analys finns i [Starta analys av kostnader](../../cost-management/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="create-budgets"></a>Skapa budgetar

<!-- Note to Azure service writer: Modify the following as needed for your service. -->

Du kan skapa [budgetar](../../cost-management/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) för att hantera kostnader och skapa [aviseringar](../../cost-management/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) som automatiskt meddelar mottagarna om kostnadsavvikelser och risker för överförbrukning. Aviseringar baseras på utgifter jämfört med budget- och kostnadströsklar. Budgetar och aviseringar skapas för Azure-prenumerationer och resurs grupper, så de är användbara som en del av en övergripande kostnads övervaknings strategi. 

Budgetar kan skapas med filter för vissa resurser eller tjänster i Azure om du vill ha mer detaljerad information i din övervakning. Filter hjälper till att se till att du inte av misstag skapar nya resurser som kostar dig ytterligare pengar. Mer information om filter alternativen när du skapar en budget finns i alternativ för [grupper och filter](../../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Exportera kostnadsdata

Du kan också [Exportera dina kostnads data](../../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) till ett lagrings konto. Detta är användbart när du behöver eller andra för att utföra ytterligare data analyser för kostnader. Ett ekonomi team kan till exempel analysera data med hjälp av Excel eller Power BI. Du kan exportera dina kostnader enligt ett dags-, vecko-eller månads schema och ange ett anpassat datum intervall. Att exportera kostnads data är det rekommenderade sättet att hämta kostnads data uppsättningar.


## <a name="other-ways-to-manage-and-reduce-costs-for-azure-sql-database"></a>Andra sätt att hantera och minska kostnaderna för Azure SQL Database

Med Azure SQL Database kan du även skala upp eller ned resurser för att kontrol lera kostnaderna baserat på dina program behov. Mer information finns i [dynamisk skalning av databas resurser](scale-resources.md).

Spara pengar genom att bekräfta till en reservation för beräknings resurser i en till tre år. Mer information finns i [Spara kostnader för resurser med reserverad kapacitet](reserved-capacity-overview.md).


## <a name="next-steps"></a>Nästa steg

- Lär dig [hur du optimerar din moln investering med Azure Cost Management](../../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Lär dig mer om att hantera kostnader med [kostnads analys](../../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Lär dig mer om att [förhindra oväntade kostnader](../../cost-management-billing/manage/getting-started.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Ta den [Cost Management](https://docs.microsoft.com/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) guidade utbildningen.
