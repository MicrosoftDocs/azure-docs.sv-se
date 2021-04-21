---
title: Planera för att hantera kostnader för Azure Logic Apps
description: Lär dig hur du planerar och hanterar kostnader för Azure Logic Apps med hjälp av kostnadsanalys i Azure Portal
ms.service: logic-apps
ms.reviewer: estfan, logicappspm, azla
ms.topic: how-to
ms.custom: subject-cost-optimization
ms.date: 03/24/2021
ms.openlocfilehash: ec2e1098df4c21704ee7c17852b893630cd3fd27
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107761825"
---
# <a name="plan-and-manage-costs-for-azure-logic-apps"></a>Planera och hantera kostnader för Azure Logic Apps

Den här artikeln hjälper dig att planera och hantera kostnader för Azure Logic Apps. Innan du skapar eller lägger till resurser med den här tjänsten ska du beräkna dina kostnader med hjälp av priskalkylatorn för Azure. När du har börjat Logic Apps resurser kan du ange budgetar och övervaka kostnader med hjälp av [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Om du vill identifiera områden där du kanske vill agera kan du även granska prognostiserade kostnader och övervaka utgiftstrender.

Tänk på att kostnader för Logic Apps endast är en del av månadskostnaden på din Azure-faktura. Även om den här artikeln förklarar hur du beräknar och hanterar kostnader för Logic Apps debiteras du för alla Azure-tjänster och -resurser som används i din Azure-prenumeration, inklusive tjänster från tredje part. När du är bekant med att hantera kostnader för Logic Apps kan du använda liknande metoder för att hantera kostnader för alla Azure-tjänster som används i din prenumeration.

## <a name="prerequisites"></a>Förutsättningar

<!--Note for Azure service writer: This section covers prerequisites for the Cost Management's Cost Analysis feature. Add other prerequisites needed for your service after the Cost Management prerequisites. -->

[Azure Cost Management stöder de](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) flesta Typer av Azure-konton. Om du vill visa alla typer av konton som stöds kan [du Cost Management förstå data](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Om du vill visa kostnadsdata behöver du minst läsbehörighet för ditt Azure-konto.

Mer information om hur du får åtkomst till Azure Cost Management finns i [Tilldela åtkomst till data](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

<!--Note for Azure service writer: If you have other prerequisites for your service, add them here -->

<a name="understand-billing-model"></a>

## <a name="understand-the-billing-model"></a>Förstå faktureringsmodellen

Azure Logic Apps körs på Azure-infrastruktur som [ackumulerar kostnader](https://azure.microsoft.com/pricing/details/logic-apps/) när du distribuerar nya resurser. Se till att du förstår faktureringsmodellen [för Logic Apps-tjänsten](logic-apps-pricing.md)tillsammans med relaterade Azure-resurser och hantera kostnader på grund av dessa beroenden när du gör ändringar i distribuerade resurser.

<a name="typical-costs"></a>

### <a name="costs-that-typically-accrue-with-azure-logic-apps"></a>Kostnader som vanligtvis ackumuleras med Azure Logic Apps

Tjänsten Logic Apps olika prissättningsmodeller, baserat på de resurser som du skapar och använder:

* Logikappresurser som du skapar och kör i tjänsten för Logic Apps klientorganisation använder en [förbrukningsprismodell.](../logic-apps/logic-apps-pricing.md#consumption-pricing)

* Logikappresurser som du skapar och kör i en [integrationstjänstmiljö (ISE) använder](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) en [fast prismodell.](../logic-apps/logic-apps-pricing.md#fixed-pricing)

Här är andra resurser som medför kostnader när du skapar dem för användning med logikappar:

* Ett [integrationskonto](../logic-apps/logic-apps-pricing.md#integration-accounts) är en separat resurs som du skapar och länkar till logikappar för att skapa B2B-integreringar. Integrationskonton använder [en fast prismodell](../logic-apps/logic-apps-pricing.md#integration-accounts) där priset baseras på den typ av integrationskonto *eller nivå* som du använder.

* En [ISE](../logic-apps/logic-apps-pricing.md#fixed-pricing) är en separat resurs som du skapar som en distributionsplats för logikappar som behöver direktåtkomst till resurser i ett virtuellt nätverk. ISE:er [använder en fast prismodell](../logic-apps/logic-apps-pricing.md#fixed-pricing) där priset baseras på ise-SKU:n som du skapar och andra inställningar.

* En [anpassad anslutningsapp](../logic-apps/logic-apps-pricing.md#consumption-pricing) är en separat resurs som du skapar för en REST API som inte har någon förbyggd anslutningsapp som du kan använda i dina logikappar. Körningar av anpassade anslutningsappar använder [en förbrukningsprismodell](../logic-apps/logic-apps-pricing.md#consumption-pricing) förutom när du använder dem i en ISE.

* I tjänsten för flera Logic Apps ackumuleras [kostnader för datalagring](../logic-apps/logic-apps-pricing.md#data-retention) och lagringsförbrukning med hjälp av [en fast prismodell.](../logic-apps/logic-apps-pricing.md#fixed-pricing) Till exempel sparas indata och utdata från körningshistoriken i bakgrunden, vilket skiljer sig från lagringsresurser som du skapar, hanterar och får åtkomst till separat från logikappen.

  I en ISE medför datalagring och lagringsförbrukning inga kostnader.

<a name="costs-after-resource-deletion"></a>

### <a name="costs-might-accrue-after-resource-deletion"></a>Kostnader kan tillkomma efter resursborttagning

<!--Note to Azure service writer: You might need to sync with your product team to identify resources that continue to exist after those ones for your service are deleted. If you're certain that no resources can exist after those for your service are deleted, then omit this section. -->

När du har tagit bort en logikapp Logic Apps tjänsten inte skapa eller köra nya arbetsflödesinstanser. Alla pågående och väntande körningar fortsätter dock tills de slutförs. Beroende på antalet körningar kan den här processen ta lite tid. Mer information finns i Hantera [logikappar.](manage-logic-apps-with-azure-portal.md#delete-logic-apps)

Om du har dessa resurser när du har tagit bort en logikapp fortsätter dessa resurser att finnas och ackumulera kostnader tills du tar bort dem:

* Azure-resurser som du skapar och hanterar oberoende av logikappen som ansluter till dessa resurser, till exempel Azure-funktionsappar, event hubs, event grids och så vidare

* Integrationskonton

* Integration Service Environment (ISE)

  Om du [tar bort en ISE](ise-manage-integration-service-environment.md#delete-ise)fortsätter det associerade virtuella Azure-nätverket, undernäten och andra relaterade resurser att finnas kvar. När du har tagit bort ISE kan du behöva vänta i upp till ett visst antal timmar innan du kan prova att ta bort det virtuella nätverket eller undernäten.

### <a name="using-monetary-credit-with-azure-logic-apps"></a>Använda penningkredit med Azure Logic Apps

Du kan betala för Azure Logic Apps med din EA-kredit för ekonomiskt åtagande. Du kan dock inte använda EA-betalningsåtagandekredit för att betala avgifter för produkter och tjänster från tredje part, inklusive dem från Azure Marketplace.

<a name="estimate-costs"></a>

## <a name="estimate-costs"></a>Uppskatta kostnader

Innan du skapar resurser med Azure Logic Apps bör du beräkna dina kostnader med hjälp av [priskalkylatorn för Azure.](https://azure.microsoft.com/pricing/calculator/) Mer information finns i [Prissättningsmodell för Azure Logic Apps](../logic-apps/logic-apps-pricing.md).

1. På sidan [priskalkylator för Azure](https://azure.microsoft.com/pricing/calculator/)väljer du Integration Azure Logic Apps på den  >  **vänstra menyn.**

   ![Skärmbild som visar priskalkylatorn för Azure med "Azure Logic Apps" valt.](./media/plan-manage-costs/add-azure-logic-apps-pricing-calculator.png)

1. Rulla nedåt på sidan tills du kan visa Azure Logic Apps priskalkylatorn. I de olika avsnitten för Azure-resurser som är direkt relaterade till Azure Logic Apps anger du antalet resurser som du planerar att använda och hur många intervall som du kan använda dessa resurser över.

   Den här skärmbilden visar ett exempel på en kostnadsuppskattning med hjälp av kalkylatorn:

   ![Exempel som visar beräknad kostnad i priskalkylatorn för Azure](./media/plan-manage-costs/example-logic-apps-pricing-calculator.png)

1. Om du vill uppdatera dina kostnadsuppskattningar när du skapar och använder nya relaterade resurser återgår du till den här kalkylatorn och uppdaterar dessa resurser här.

<a name="create-budgets-alerts"></a>

## <a name="create-budgets-and-alerts"></a>Skapa budgetar och aviseringar

För att hjälpa dig att proaktivt hantera kostnader [](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) för [](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) ditt Azure-konto eller din Prenumeration kan du skapa [budgetar](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) och aviseringar med hjälp Azure Cost Management and Billing tjänsten och funktionerna.  Budgetar och aviseringar skapas för Azure-prenumerationer och resursgrupper, så de är användbara som en del av en övergripande kostnadsövervakningsstrategi.

Baserat på utgifter jämfört med budget- och kostnadströsklar meddelar aviseringar automatiskt intressenter om utgiftsavvikelser och risker med överkostnader. Om du vill ha mer kornighet i övervakningen kan du också skapa budgetar som använder filter för specifika resurser eller tjänster i Azure. Filter hjälper till att se till att du inte oavsiktligt skapar nya resurser som kostar extra pengar. Mer information om filteralternativen finns i [Alternativ för gruppering och filter.](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

<a name="monitor-costs"></a>

## <a name="monitor-costs"></a>Övervaka kostnader

Kostnaderna för resursanvändningsenhet varierar efter tidsintervall, till exempel sekunder, minuter, timmar och dagar, eller efter enhetsanvändning, till exempel byte, megabyte och så vidare. Några exempel är per dag, aktuell och föregående månad och år. Om du växlar till längre vyer över tid kan du identifiera utgiftstrender. När du använder kostnadsanalysfunktionerna kan du visa kostnader som grafer och tabeller över olika tidsintervall. Om du har skapat budgetar och kostnadsprognoser kan du också enkelt se var budgetar har överskridits och överberäkningar kan ha inträffat.

När du har börjat medföra kostnader för resurser som skapar eller börjar använda i Azure kan du granska och övervaka dessa kostnader på följande sätt:

* [Övervaka logikappkörningar och lagringsförbrukning](#monitor-billing-metrics) med hjälp av Azure Monitor

* Köra [kostnadsanalys](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) med [hjälp av Azure Cost Management and Billing](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

<a name="monitor-billing-metrics"></a>

### <a name="monitor-logic-app-executions-and-storage-consumption"></a>Övervaka logikappkörningar och lagringsförbrukning

Med Azure Monitor kan du visa dessa mått för en specifik logikapp:

* Fakturerbara åtgärdskörningar
* Fakturerbara utlösarkörningar
* Faktureringsanvändning för interna åtgärdskörningar
* Faktureringsanvändning för standardkörningar av anslutningsappar
* Faktureringsanvändning för lagringsförbrukning
* Totalt antal fakturerbara körningar

<a name="execution-storage-metrics"></a>

#### <a name="view-execution-and-storage-consumption-metrics"></a>Visa mått för körning och lagringsförbrukning

1. I Azure Portal du och öppnar logikappen. Välj Mått under Övervakning på **logikappens** **meny.**

1. I den högra rutan under **Diagramrubrik** i måttfältet öppnar du listan **Mått** och väljer det mått som du vill använda.

   > [!NOTE]
   > Lagringsförbrukningen mäts som det antal lagringsenheter (GB) som logikappen använder och faktureras. Körningar som använder mindre än 500 MB i lagring kanske inte visas i övervakningsvyn, men de debiteras fortfarande.

   ![Skärmbild som visar fönstret Mått med den öppna listan "Mått".](./media/logic-apps-pricing/select-metric.png)

1. I fönstrets övre högra hörn väljer du den tidsperiod som du vill använda.

1. Följ dessa steg om du vill visa andra lagringsförbrukningsdata, särskilt åtgärdsstorlekar för indata och utdata i logikappens [körningshistorik.](#view-input-output-sizes)

<a name="view-input-output-sizes"></a>

#### <a name="view-action-input-and-output-sizes-in-run-history"></a>Visa åtgärdsstorlekar för indata och utdata i körningshistoriken

1. I Azure Portal du och öppnar logikappen.

1. På logikappens meny väljer du **Översikt.**

1. I rutan till höger under **Körningshistorik väljer** du den körning som har de indata och utdata som du vill visa.

1. Under **Logic app run (Logikappkörning)** väljer du Run Details **(Kör information).**

1. I fönstret **Information om logikappkörning** väljer du den åtgärd som du vill visa i åtgärdstabellen, som visar status och varaktighet för varje åtgärd.

1. I **åtgärdsfönstret Logikapp** hittar du storlekarna för åtgärdens indata och utdata. Under **länken Indata** och **länken Utdata** hittar du länkarna till dessa indata och utdata.

   > [!NOTE]
   > För loopar visar endast åtgärderna på den översta nivån storlekar för deras indata och utdata. För åtgärder i kapslade loopar visar indata och utdata noll storlek och inga länkar.

<a name="run-cost-analysis"></a>

### <a name="run-cost-analysis-by-using-azure-cost-management-and-billing"></a>Köra kostnadsanalys med hjälp av Azure Cost Management and Billing

Om du vill granska kostnader för Logic Apps-tjänsten baserat på ett specifikt omfång, [](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) till exempel en Azure-prenumeration, kan du använda funktionerna för kostnadsanalys [i Azure Cost Management and Billing](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

1. I Azure Portal du det omfång som du vill använda, till exempel din Azure-prenumeration. I den vänstra menyn, under **Cost Management** väljer du **Kostnadsanalys**.

   När du öppnar fönstret för kostnadsanalys första gången visar det översta diagrammet de faktiska och prognostiserade användningskostnaderna för alla tjänster i prenumerationen för den aktuella månaden.

   ![Skärmbild som visar Azure Portal och kostnadsanalys med exempel på faktiska och prognostiserade kostnader i en prenumeration.](./media/plan-manage-costs/cost-analysis-total-actual-forecasted-costs.png)

   > [!TIP]
   > Om du vill ändra omfång går **du till fönstret** Kostnadsanalys och väljer Omfångsfilter i **filterfältet.** I fönstret **Välj omfång** växlar du till det omfång som du vill använda.

   Nedanför visar ringdiagrammen de aktuella kostnaderna per Azure-tjänster, per Azure-region (plats) och efter resursgrupp.

   ![Skärmbild som visar Azure Portal och kostnadsanalys med exempeldiagram för tjänster, regioner och resursgrupper.](./media/plan-manage-costs/cost-analysis-donut-charts.png)

1. Om du vill filtrera diagrammet till ett visst område, till exempel en tjänst eller resurs, väljer du Lägg till filter i **filterfältet.**

1. I listan till vänster väljer du filtertypen, till exempel **Tjänstnamn.** I listan till höger väljer du filtret, till exempel **logic apps**. När du är klar väljer du den gröna bockmarkeringen.

   ![Skärmbild som visar Azure Portal och kostnadsanalys med filterval.](./media/plan-manage-costs/cost-analysis-add-service-name-filter.png)

   Här är till exempel resultatet för Logic Apps tjänsten:

   ![Skärmbild som visar Azure Portal och kostnadsanalys med resultat filtrerade på "logikappar".](./media/plan-manage-costs/cost-analysis-total-actual-costs-service.png)

### <a name="export-cost-data"></a>Exportera kostnadsdata

När du behöver göra mer dataanalys av kostnader kan du exportera [kostnadsdata till](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) ett lagringskonto. Ett ekonomiteam kan till exempel analysera dessa data med hjälp av Excel eller Power BI. Du kan exportera dina kostnader enligt ett schema för varje dag, vecka eller månad och ange ett anpassat datumintervall. Att exportera kostnadsdata är det rekommenderade sättet att hämta kostnadsdatauppsättningar.

## <a name="other-ways-to-manage-and-reduce-costs"></a>Andra sätt att hantera och minska kostnader

<!-- Note to Azure service writer: This section is optional. Other than using the Cost Management methods above, your service probably has other specific ways to minimize costs. For example, you might have best practice advice or specific ways to reduce costs that are specific to your service. If so, try to add that guidance here or at least summarize key points. Try to be as prescriptive as possible. If you have more comprehensive content, add links to your other published articles or sections here.

Add a statement that discusses any recommended settings for your service that might help keep the charges minimal if a service isn't being actively used by the customer. For example: Will turning off a VM help to get no charges for the specific VM resource?

Otherwise, if no other cost-saving recommendations or best practices exist to reduce costs, cut this section.
-->

Prova följande alternativ för att minska kostnaderna för dina logik-APS och relaterade resurser:

* Använd om möjligt [inbyggda utlösare och](../connectors/built-in.md)åtgärder , som kostar mindre att köra per körning än utlösare och åtgärder [för hanterade anslutningsappar.](../connectors/managed.md)

  Du kan till exempel minska kostnaderna vid åtkomst till andra resurser med hjälp av [HTTP-åtgärden](../connectors/connectors-native-http.md) eller genom att anropa en funktion som du har skapat med [hjälp av Azure Functions-tjänsten](../azure-functions/functions-overview.md) och med hjälp av [den inbyggda Azure Functions-åtgärden](../logic-apps/logic-apps-azure-functions.md). Men att Azure Functions också medför kostnader, så se till att du jämför dina alternativ.

* [Ange exakta utlösarvillkor](logic-apps-workflow-actions-triggers.md#trigger-conditions) för att köra ett arbetsflöde.

  Du kan till exempel ange att en utlösare endast ska utlösas när målwebbplatsen returnerar ett internt serverfel. I utlösarens JSON-definition använder du egenskapen `conditions` för att ange ett villkor som refererar till utlösarens statuskod.

* Om en utlösare har en avsökningsversion och en webhook-version kan du prova webhook-versionen, som väntar på att den angivna händelsen ska inträffa innan den utlöses, i stället för att regelbundet söka efter händelsen.

* Anropa logikappen via en annan tjänst så att utlösaren endast utlöses när arbetsflödet ska köras.

  Du kan till exempel anropa logikappen från en funktion som du skapar och kör med hjälp av Azure Functions tjänsten. Se till exempel Anropa [eller utlösa logikappar med hjälp av Azure Functions och Azure Service Bus](logic-apps-scenario-function-sb-trigger.md).

* [Inaktivera logikappar](manage-logic-apps-with-azure-portal.md#disable-or-enable-logic-apps) som inte behöver köras hela tiden eller ta [bort logikappar](manage-logic-apps-with-azure-portal.md#delete-logic-apps) som du inte längre behöver alls. Om möjligt kan du inaktivera andra resurser som du inte behöver hela tiden.

## <a name="next-steps"></a>Nästa steg

* [Optimera din molninvestering med Azure Cost Management](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Hantera kostnader med hjälp av kostnadsanalys](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Undvika oväntade kostnader](../cost-management-billing/understand/analyze-unexpected-charges.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* Gå den [Cost Management guidade](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) inlärningskursen