---
title: Planera för att hantera kostnader för Azure Logic Apps
description: Lär dig att planera för och hantera kostnader för Azure Logic Apps genom att använda kostnads analys i Azure Portal
ms.service: logic-apps
ms.reviewer: estfan, logicappspm, azla
ms.topic: how-to
ms.custom: subject-cost-optimization
ms.date: 01/29/2021
ms.openlocfilehash: 58e12862cf00b500bced105d67fede8599c2a257
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99180545"
---
# <a name="plan-and-manage-costs-for-azure-logic-apps"></a>Planera och hantera kostnader för Azure Logic Apps

Den här artikeln hjälper dig att planera och hantera kostnader för Azure Logic Apps. Innan du skapar eller lägger till resurser med hjälp av den här tjänsten kan du beräkna dina kostnader med hjälp av pris Kalkylatorn för Azure. När du börjar använda Logic Apps-resurser kan du ställa in budgetar och övervaka kostnader med hjälp av [Azure Cost Management](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). För att identifiera områden där du kanske vill agera kan du också granska prognostiserade kostnader och övervaka utgifts trender.

Tänk på att kostnader för Logic Apps bara är en del av månads kostnaderna på din Azure-faktura. Även om den här artikeln förklarar hur du uppskattar och hanterar kostnader för Logic Apps debiteras du för alla Azure-tjänster och-resurser som används i din Azure-prenumeration, inklusive alla tjänster från tredje part. När du är bekant med att hantera kostnader för Logic Apps kan du använda liknande metoder för att hantera kostnader för alla Azure-tjänster som används i din prenumeration.

## <a name="prerequisites"></a>Förutsättningar

<!--Note for Azure service writer: This section covers prerequisites for the Cost Management's Cost Analysis feature. Add other prerequisites needed for your service after the Cost Management prerequisites. -->

[Azure Cost Management](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) stöder de flesta typer av Azure-konton. Om du vill visa alla konto typer som stöds, se [förstå Cost Management data](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Om du vill visa kostnadsdata behöver du minst läsbehörighet för ditt Azure-konto.

Mer information om hur du får åtkomst till Azure Cost Management finns i [Tilldela åtkomst till data](https://docs.microsoft.com/azure/cost-management/assign-access-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

<!--Note for Azure service writer: If you have other prerequisites for your service, add them here -->

<a name="understand-billing-model"></a>

## <a name="understand-the-billing-model"></a>Förstå fakturerings modellen

Azure Logic Apps körs på Azure-infrastruktur som [periodiserar kostnader](https://azure.microsoft.com/pricing/details/logic-apps/) när du distribuerar nya resurser. Se till att du förstår [fakturerings modellen för Logic Appss tjänsten tillsammans med relaterade Azure-resurser](logic-apps-pricing.md)och hantera kostnader på grund av dessa beroenden när du gör ändringar i distribuerade resurser.

<a name="typical-costs"></a>

### <a name="costs-that-typically-accrue-with-azure-logic-apps"></a>Kostnader som vanligt vis påförs med Azure Logic Apps

Logic Appss tjänsten använder olika pris modeller, baserat på de resurser som du skapar och använder:

* Logic app-resurser som du skapar och kör i Logic Apps tjänsten för flera innehavare använder en [pris modell för förbrukning](../logic-apps/logic-apps-pricing.md#consumption-pricing).

* Logic app-resurser som du skapar och kör i en [integrerings tjänst miljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) använder en [fast pris modell](../logic-apps/logic-apps-pricing.md#fixed-pricing).

Här följer andra resurser som kostar kostnader när du skapar dem för användning med Logic Apps:

* Ett [integrations konto](../logic-apps/logic-apps-pricing.md#integration-accounts) är en separat resurs som du skapar och länkar till logi Kap par för att skapa B2B-integrationer. Integrations konton använder en [fast pris modell](../logic-apps/logic-apps-pricing.md#integration-accounts) där priset baseras på integrations konto typen eller- *nivån* som du använder.

* En [ISE](../logic-apps/logic-apps-pricing.md#fixed-pricing) är en separat resurs som du skapar som en distributions plats för logi Kap par som behöver direkt åtkomst till resurser i ett virtuellt nätverk. ISEs använder en [fast pris modell](../logic-apps/logic-apps-pricing.md#fixed-pricing) där priset baseras på den ISE SKU som du skapar och andra inställningar.

* En [anpassad anslutning](../logic-apps/logic-apps-pricing.md#consumption-pricing) är en separat resurs som du skapar för en REST API som inte har någon fördefinierad koppling som du kan använda i dina Logic Apps. Anpassade anslutnings program använder en [modell för användnings priser](../logic-apps/logic-apps-pricing.md#consumption-pricing) förutom när du använder dem i en ISE.

* I Logic Apps tjänsten för flera innehavare debiterar [data kvarhållning och lagrings förbrukning](../logic-apps/logic-apps-pricing.md#data-retention) kostnader med en [fast pris modell](../logic-apps/logic-apps-pricing.md#fixed-pricing). Till exempel lagras indata och utdata från körnings historiken i lagrings platsen – i bakgrunden, vilket skiljer sig från lagrings resurser som du själv skapar, hanterar och kommer åt från din Logic app.

  I en ISE debiteras inte kostnader för data lagring och lagrings förbrukning.

<a name="costs-after-resource-deletion"></a>

### <a name="costs-might-accrue-after-resource-deletion"></a>Kostnader kan uppstå efter borttagning av resurs

<!--Note to Azure service writer: You might need to sync with your product team to identify resources that continue to exist after those ones for your service are deleted. If you're certain that no resources can exist after those for your service are deleted, then omit this section. -->

När du har tagit bort en Logic app skapas eller körs inte nya arbets flödes instanser i Logic Appss tjänsten. Alla pågående och väntande körningar fortsätter dock tills de är klara. Den här processen kan ta lite tid beroende på hur många körningar som körs. Mer information finns i [Hantera Logic Apps](manage-logic-apps-with-azure-portal.md#delete-logic-apps).

Om du har de här resurserna efter att du har tagit bort en Logic-app, fortsätter dessa resurser att existera och debiteras innan du tar bort dem:

* Azure-resurser som du skapar och hanterar oberoende av den logiska appen som ansluter till dessa resurser, till exempel Azure Function-appar, händelse nav, händelse rutnät och så vidare

* Integrationskonton

* Integrerings tjänst miljöer (ISEs)

  Om du [tar bort en ISE](ise-manage-integration-service-environment.md#delete-ise)fortsätter det associerade virtuella Azure-nätverket, undernät och andra relaterade resurser att finnas kvar. När du har tagit bort ISE kan du behöva vänta upp till ett angivet antal timmar innan du kan försöka ta bort det virtuella nätverket eller undernät.

### <a name="using-monetary-credit-with-azure-logic-apps"></a>Använda monetär kredit med Azure Logic Apps

Du kan betala för Azure Logic Apps avgifter med din kredit för ditt EA-belopp. Du kan dock inte använda EA-krediter för att betala för kostnader för produkter och tjänster från tredje part, inklusive de från Azure Marketplace.

<a name="estimate-costs"></a>

## <a name="estimate-costs"></a>Uppskatta kostnader

Innan du skapar resurser med Azure Logic Apps kan du beräkna dina kostnader med hjälp av [pris Kalkylatorn för Azure](https://azure.microsoft.com/pricing/calculator/). Mer information finns [i pris modell för Azure Logic Apps](../logic-apps/logic-apps-pricing.md).

1. På [sidan pris kalkylator för Azure](https://azure.microsoft.com/pricing/calculator/)väljer du **integration**  >  **Azure Logic Apps** på menyn till vänster.

   ![Skärm bild som visar pris Kalkylatorn för Azure med "Azure Logic Apps" vald.](./media/plan-manage-costs/add-azure-logic-apps-pricing-calculator.png)

1. Rulla ned på sidan tills du kan se pris Kalkylatorn för Azure Logic Apps. I de olika avsnitten för Azure-resurser som är direkt relaterade till Azure Logic Apps anger du antalet resurser som du planerar att använda och hur många intervall du kan använda resurserna.

   Den här skärm bilden visar ett exempel på kostnads uppskattning med Kalkylatorn:

   ![Exempel som visar uppskattad kostnad i pris Kalkylatorn för Azure](./media/plan-manage-costs/example-logic-apps-pricing-calculator.png)

1. Om du vill uppdatera dina kostnads beräkningar när du skapar och använder nya relaterade resurser går du tillbaka till den här kalkylatorn och uppdaterar resurserna här.

<a name="create-budgets-alerts"></a>

## <a name="create-budgets-and-alerts"></a>Skapa budgetar och aviseringar

För att hjälpa dig att proaktivt hantera kostnader för ditt Azure-konto eller-prenumeration kan du skapa [budgetar](https://docs.microsoft.com/azure/cost-management/tutorial-acm-create-budgets?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) och [aviseringar](https://docs.microsoft.com/azure/cost-management/cost-mgt-alerts-monitor-usage-spending?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) med hjälp av [Azure Cost Management-och fakturerings](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) tjänsten och-funktionerna.  Budgetar och aviseringar skapas för Azure-prenumerationer och resurs grupper, så de är användbara som en del av en övergripande kostnads övervaknings strategi.

Baserat på utgifter jämfört med budget-och kostnads trösklar meddelar aviseringar automatiskt intressenter om utgifts avvikelser och överförbrukning av risker. Om du vill ha mer detaljerad information om övervakningen kan du också skapa budgetar som använder filter för vissa resurser eller tjänster i Azure. Med filter kan du se till att du inte av misstag skapar nya resurser som kostar extra pengar. Mer information om filter alternativ finns i alternativ för [grupper och filter](https://docs.microsoft.com/azure/cost-management-billing/costs/group-filter?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

<a name="monitor-costs"></a>

## <a name="monitor-costs"></a>Övervaka kostnader

Kostnader för resursanvändning varierar beroende på tidsintervaller, till exempel sekunder, minuter, timmar och dagar, eller per enhets användning, till exempel byte, megabyte och så vidare. Några exempel är per dag, innevarande och föregående månad och år. Att växla till längre vyer över tid kan hjälpa dig att identifiera utgifts trender. När du använder funktionerna för kostnads analys kan du Visa kostnader som diagram och tabeller under olika tidsintervall. Om du har skapat budgetar och kostnads prognoser kan du också enkelt hitta var budgetarna har överskridits och överförbrukningen kan ha inträffat.

När du har påbörjat kostnader för resurser som skapar eller börjar använda i Azure, kan du granska och övervaka dessa kostnader på följande sätt:

* [Övervaka körning av Logic app och lagrings förbrukning](#monitor-billing-metrics) med Azure Monitor

* Kör [kostnads analys](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) med [Azure Cost Management och fakturering](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

<a name="monitor-billing-metrics"></a>

### <a name="monitor-logic-app-executions-and-storage-consumption"></a>Övervaka körningar av Logic app och lagrings förbrukning

Med hjälp av Azure Monitor kan du visa dessa mått för en speciell Logic-app:

* Fakturerbara åtgärds körningar
* Fakturerbara Utlösar-körningar
* Fakturerings användning för intern åtgärds körningar
* Fakturerings användning för standard kopplings körningar
* Fakturerings användning för lagrings förbrukning
* Totalt antal fakturerbara körningar

<a name="execution-storage-metrics"></a>

#### <a name="view-execution-and-storage-consumption-metrics"></a>Visa användnings mått för körning och lagring

1. Leta upp och öppna din Logic app i Azure Portal. På din Logic Apps-meny går du till **övervakning** och väljer **mått**.

1. I fönstret till höger, under **diagram rubrik**, i mått fältet, öppnar du **mått** listan och väljer det mått som du vill använda.

   > [!NOTE]
   > Lagrings förbrukningen mäts som antalet lagrings enheter (GB) som din Logic app använder och faktureras. Körningar som använder mindre än 500 MB i lagringen kanske inte visas i vyn övervakning, men de faktureras fortfarande.

   ![Skärm bild som visar fönstret mått med den öppnade "Metric"-listan.](./media/logic-apps-pricing/select-metric.png)

1. I fönstrets övre högra hörn väljer du den tids period som du vill använda.

1. [Följ dessa steg](#view-input-output-sizes)om du vill visa andra lagrings förbruknings data, särskilt åtgärder för indata och utdata i din Logic Apps körnings historik.

<a name="view-input-output-sizes"></a>

#### <a name="view-action-input-and-output-sizes-in-run-history"></a>Visa åtgärder för indata och utdata i körnings historiken

1. Leta upp och öppna din Logic app i Azure Portal.

1. På din Logic Apps-meny väljer du **Översikt**.

1. I den högra rutan under **körnings historik** väljer du den körning som har de indata och utdata som du vill visa.

1. Under **Logic app-körning** väljer du **Kör information**.

1. I fönstret för **körning av logiska appar** , i tabellen åtgärder, som listar varje åtgärds status och varaktighet väljer du den åtgärd som du vill visa.

1. I fönstret **åtgärd för Logic app-åtgärd** hittar du storlekarna för åtgärdens indata och utdata. Leta upp länkarna till dessa indata och utdata under **indata-länken** och **utdata-länken**.

   > [!NOTE]
   > För slingor är det bara de toppnivå åtgärderna som visar storlekarna för indata och utdata. För åtgärder i kapslade slingor visar indata och utdata noll storlek och inga länkar.

<a name="run-cost-analysis"></a>

### <a name="run-cost-analysis-by-using-azure-cost-management-and-billing"></a>Kör kostnads analys med Azure Cost Management och fakturering

Om du vill granska kostnaderna för Logic Apps tjänst baserat på ett särskilt omfång, till exempel en Azure-prenumeration, kan du använda funktionerna för [kostnads analys](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) i [Azure Cost Management och fakturering](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

1. I Azure Portal öppnar du den omfattning som du vill ha, till exempel din Azure-prenumeration. På den vänstra menyn, under **Cost Management** väljer du **kostnads analys**.

   Första gången du öppnar fönstret kostnads analys visar det översta diagrammet de faktiska och prognostiserade användnings kostnaderna för alla tjänster i prenumerationen för den aktuella månaden.

   ![Skärm bild som visar Azure Portal-och kostnads analys fönster med exempel på faktiska och prognostiserade kostnader i en prenumeration.](./media/plan-manage-costs/cost-analysis-total-actual-forecasted-costs.png)

   > [!TIP]
   > Om du vill ändra omfång går du till fönstret **kostnads analys** och väljer **omfångs** filtret i fältet filter. I fönstret **Välj omfång** växlar du till den omfattning som du vill använda.

   Ring diagrammet visar de aktuella kostnaderna med Azure-tjänster, efter Azure-region (plats) och per resurs grupp.

   ![Skärm bild som visar Azure Portal-och kostnads analys fönster med exempel ring diagram för tjänster, regioner och resurs grupper.](./media/plan-manage-costs/cost-analysis-donut-charts.png)

1. Om du vill filtrera diagrammet till ett särskilt utrymme, till exempel en tjänst eller resurs, går du till filter fältet och väljer **Lägg till filter**.

1. I listan till vänster väljer du filter typ, till exempel **tjänst namn**. Från den högra listan väljer du filtret, till exempel **Logic Apps**. När du är klar väljer du den gröna bock markeringen.

   ![Skärm bild som visar Azure Portal-och kostnads analys fönster med filter val.](./media/plan-manage-costs/cost-analysis-add-service-name-filter.png)

   Här är till exempel resultatet för tjänsten Logic Apps:

   ![Skärm bild som visar Azure Portal-och kostnads analys fönster med resultat som filtrerats på "Logic Apps".](./media/plan-manage-costs/cost-analysis-total-actual-costs-service.png)

### <a name="export-cost-data"></a>Exportera kostnadsdata

När du behöver göra mer data analyser för kostnader kan du [Exportera kostnads data](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) till ett lagrings konto. Ett ekonomi team kan till exempel analysera dessa data med hjälp av Excel eller Power BI. Du kan exportera dina kostnader enligt ett dags-, vecko-eller månads schema och ange ett anpassat datum intervall. Att exportera kostnads data är det rekommenderade sättet att hämta kostnads data uppsättningar.

## <a name="other-ways-to-manage-and-reduce-costs"></a>Andra sätt att hantera och minska kostnaderna

<!-- Note to Azure service writer: This section is optional. Other than using the Cost Management methods above, your service probably has other specific ways to minimize costs. For example, you might have best practice advice or specific ways to reduce costs that are specific to your service. If so, try to add that guidance here or at least summarize key points. Try to be as prescriptive as possible. If you have more comprehensive content, add links to your other published articles or sections here.

Add a statement that discusses any recommended settings for your service that might help keep the charges minimal if a service isn't being actively used by the customer. For example: Will turning off a VM help to get no charges for the specific VM resource?

Otherwise, if no other cost-saving recommendations or best practices exist to reduce costs, cut this section.
-->

Prova följande alternativ för att hjälpa dig att minska kostnaderna för dina Logic APS och relaterade resurser:

* Använd om möjligt [inbyggda utlösare och åtgärder](../connectors/apis-list.md#built-in), vilket kostar mindre att köra per körning än [utlösare och åtgärder för hanterade anslutningar](../connectors/apis-list.md#managed-connectors).

  Du kanske till exempel kan minska kostnaderna vid åtkomst till andra resurser genom att använda [http-åtgärden](../connectors/connectors-native-http.md) eller genom att anropa en funktion som du har skapat med hjälp av [Azure Functions tjänsten](../azure-functions/functions-overview.md) och använda den [inbyggda Azure Functions åtgärden](../logic-apps/logic-apps-azure-functions.md). Men om du använder Azure Functions också kostnader, så se till att du jämför dina alternativ.

* [Ange exakta utlösnings villkor](logic-apps-workflow-actions-triggers.md#trigger-conditions) för att köra ett arbets flöde.

  Du kan till exempel ange att en utlösare endast utlöses när mål webbplatsen returnerar ett internt Server fel. I utlösarens JSON-definition använder du `conditions` egenskapen för att ange ett villkor som refererar till utlösarens status kod.

* Om en utlösare har en avsöknings version och en webhook-version, försöker du med webhook-versionen, som väntar på att den angivna händelsen ska ske innan den utlöses, i stället för att regelbundet söka efter händelsen.

* Anropa din Logic-app via en annan tjänst så att utlösaren endast utlöses när arbets flödet ska köras.

  Du kan till exempel anropa din Logic app från en funktion som du skapar och kör med hjälp av tjänsten Azure Functions. Se till exempel [anropa eller utlös Logi Kap par genom att använda Azure Functions och Azure Service Bus](logic-apps-scenario-function-sb-trigger.md).

* [Inaktivera](manage-logic-apps-with-azure-portal.md#disable-or-enable-logic-apps) Logi Kap par som inte behöver köras kontinuerligt eller [ta bort Logic Apps](manage-logic-apps-with-azure-portal.md#delete-logic-apps) som du inte längre behöver alls. Om möjligt kan du inaktivera alla andra resurser som du inte behöver ständigt aktiva.

## <a name="next-steps"></a>Nästa steg

* [Optimera din molninvestering med Azure Cost Management](https://docs.microsoft.com/azure/cost-management-billing/costs/cost-mgt-best-practices?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Hantera kostnader med kostnads analys](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Undvika oväntade kostnader](https://docs.microsoft.com/azure/cost-management-billing/understand/analyze-unexpected-charges?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* Ta den [Cost Management](https://docs.microsoft.com/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) guidade inlärnings kursen


