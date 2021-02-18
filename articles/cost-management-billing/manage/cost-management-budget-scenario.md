---
title: Budgetscenario för fakturering och kostnadshantering i Azure
description: Lär dig hur du använder Azure Automation för att stänga av virtuella datorer baserat på specifika budgettrösklar.
author: bandersmsft
ms.reviewer: adwise
tags: billing
ms.service: cost-management-billing
ms.subservice: cost-management
ms.topic: how-to
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 1706fd69f58e4dee4a7ed963fb22cb63aec026fa
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100593385"
---
# <a name="manage-costs-with-azure-budgets"></a>Hantera kostnader med Azure Budgets

Kostnadskontroll är en viktig komponent om du ska kunna få ut mesta möjliga av dina investeringar i molnet. Det finns flera scenarier där kostnadsinsyn, rapportering och kostnadsbaserad orkestrering är kritiska komponenter för affärsverksamheten. [Azure Cost Management-API:erna](/rest/api/consumption/) omfattar ett antal API:er som ger stöd för dessa scenarier. API:erna returnerar användningsdata så att du kan visa detaljerad kostnadsinformation på instansnivå.

Budgetar används ofta som en del av kostnadsstyrningen. I Azure kan du begränsa budgetomfånget. Du kan till exempel begränsa budgetvyn baserat på prenumeration, resursgrupper eller en samling resurser. Budget-API:et kan avisera dig via e-post när en budgettröskel nås, men du kan också använda [Azure Monitor-åtgärdsgrupper](../../azure-monitor/alerts/action-groups.md) för att utlösa orkestrerade åtgärder när en budgethändelse inträffar.

Ett exempel på ett vanligt budgetscenario för en kund som kör icke-kritiska arbetsbelastningar är när kunden vill hantera kostnaderna mot en budget och få en förutsägbar kostnad på månadsfakturan. Det här scenariot kräver viss kostnadsbaserad orkestrering av resurser som ingår i Azure-miljön. I det här scenariot är den månatliga budgeten 1 000 USD för prenumerationen. Meddelandetrösklar konfigureras också, som utlöser en del orkestreringar. Det här scenariot börjar med en kostnadströskel på 80 %, som gör att alla virtuella datorer i resursgruppen **Optional** (Valfri) stoppas. Därefter stoppas samtliga VM-instanser vid 100 %-kostnadströskeln.

För att konfigurera det här scenariot utför du följande åtgärder genom att följa stegen i de olika avsnitten i den här självstudien.

Genom att utföra åtgärderna som beskrivs i den här självstudien kan du:

- Skapa en Azure Automation-runbook för att stoppa virtuella datorer med hjälp av webhooks.
- Skapa en Azure-logikapp som ska utlösas baserat på budgettröskelns värde och anropa runbooken med rätt parametrar.
- Skapa en Azure Monitor-åtgärdsgrupp som konfigureras för att utlösa Azure-logikappen när budgettröskeln nås.
- Skapa Azure-budgeten med önskade tröskelvärden och koppla den till åtgärdsgruppen.

## <a name="create-an-azure-automation-runbook"></a>Skapa en Azure Automation-runbook

[Azure Automation](../../automation/automation-intro.md) är en tjänst som du kan använda för att skapa skript för de flesta resurshanteringsaktiviteter och köra dessa uppgifter som schemalagda eller på begäran. Som en del av det här scenariot ska du skapa en [Azure Automation-runbook](../../automation/automation-runbook-types.md) som ska användas för att stoppa virtuella datorer. Du ska använda den grafiska runbooken [Stop Azure V2 VMs](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) (Stoppa virtuella Azure V2-datorer) från [galleriet](../../automation/automation-runbook-gallery.md) för att utveckla det här scenariot. Genom att importera den här runbooken till ditt Azure-konto och publicera den, kan du stoppa virtuella datorer när ett budgettröskelvärde nås.

### <a name="create-an-azure-automation-account"></a>Skapa ett Azure Automation-konto

1. Logga in på [Azure Portal](https://portal.azure.com/) med autentiseringsuppgifterna för ditt Azure-konto.
2. Välj knappen **Skapa en resurs** längst upp till vänster i Azure.
3. Välj **Hanteringsverktyg** > **Automation**.
   > [!NOTE]
   > Om du inte har ett Azure-konto kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/).
4. Ange din kontoinformation. För **Skapa Kör som-konto i Azure** väljer du **Ja** för att automatiskt aktivera de inställningar som krävs för att förenkla autentiseringen till Azure.
5. När du är färdig väljer du **Skapa** för att starta distributionen av Automation-kontot.

### <a name="import-the-stop-azure-v2-vms-runbook"></a>Importera runbooken Stop Azure V2 VMs (Stoppa virtuella Azure V2-datorer)

Du använder en [Azure Automation-runbook](../../automation/automation-runbook-types.md) för att importera den grafiska runbooken [Stop Azure v2 VMs](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) (Stoppa virtuella Azure V2-datorer) från galleriet.

1. Logga in på [Azure Portal](https://portal.azure.com/) med autentiseringsuppgifterna för ditt Azure-konto.
1. Öppna ditt Automation-konto genom att välja **Alla tjänster** > **Automation-konton**. Välj sedan ditt Automation-konto.
1. Välj **Runbook-galleri** i avsnittet **Processautomatisering**.
1. Välj **Skriptcenter** för **Gallerikälla** och välj sedan **OK**.
1. Leta upp och välj gallerialternativet [Stop Azure V2 VMs](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) (Stoppa virtuella Azure V2-datorer) på Azure-portalen.
1. Välj **Importera** för att visa ytan **Importera** och välj **OK**. Översiktsytan för runbooken visas.
1. När runbooken har importerats väljer du **Redigera** för att visa den grafiska runbook-redigeraren och publiceringsalternativet.  
    ![Azure – Redigera grafisk runbook](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-01.png)
1. Välj **Publicera** för att publicera runbooken och välj sedan **Ja** när du uppmanas att göra det. När du publicerar en runbook åsidosätter du den befintliga publicerade versionen med utkastversionen. I detta fall finns det ingen publicerad version eftersom du har skapat runbooken.
    Mer information om hur du publicerar en runbook finns i [Skapa en grafisk runbook](../../automation/learn/automation-tutorial-runbook-graphical.md).

## <a name="create-webhooks-for-the-runbook"></a>Skapa webhooks för runbooken

Med hjälp av den grafiska runbooken [Stop Azure V2 VMs](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) (Stoppa virtuella Azure V2-datorer) skapar du två webhooks så att runbooken startar i Azure Automation via en enda HTTP-begäran. Den första webhooken anropar runbooken vid en budgettröskel på 80 % med resursgruppens namn som en parameter, så att de valfria virtuella datorerna kan stoppas. Därefter anropar den andra webhooken runbooken utan parametrar (vid 100 %), vilket gör att alla återstående VM-instanser stoppas.

1. På sidan **Runbooks** på [Azure-portalen](https://portal.azure.com/) väljer du runbooken **StopAzureV2Vm** som visar runbookens översiktsyta.
1. Välj **Webhook** längst upp på sidan för att öppna området **Lägg till webhook**.
1. Öppna området **Skapa en ny webhook** genom att välja **Skapa ny webhook**.
1. Ange **Optional** (Valfri) som **namn** för webhooken. Egenskapen **Aktiverat** måste vara **Ja**. Du behöver inte ändra värdet **Upphör**. Mer information om webhook-egenskaper finns i avsnittet om [webhook-egenskaper](../../automation/automation-webhooks.md#webhook-properties).
1. Välj kopieringsikonen bredvid URL-värdet för att kopiera webhookens URL.
   > [!IMPORTANT]
   > Spara URL:en för webhooken med namnet **Optional** (Valfri) på ett säkert ställe. Du ska använda URL-adressen senare i den här självstudien. Av säkerhetsskäl kan du inte visa eller hämta URL:en igen när du har skapat webhooken.
1. Skapa den nya webhooken genom att välja **OK**.
1. Välj **Konfigurera parametrar och körningsinställningar** för att visa parametervärden för runbooken.
   > [!NOTE]
   > Om runbooken har obligatoriska parametrar kan du inte skapa webhooken utan att ange värdena.
1. Acceptera parametervärdena för webhooken genom att välja **OK**.
1. Skapa webhooken genom att välja **Skapa**.
1. Följ sedan stegen ovan för att skapa en andra webhook med namnet **Complete**.
    > [!IMPORTANT]
    > Spara båda webhook-URL:erna. Du ska använda dem senare i den här självstudien. Av säkerhetsskäl kan du inte visa eller hämta URL:en igen när du har skapat webhooken.

Nu bör du ha två konfigurerade webhooks som är tillgängliga med de URL:er som du har sparat.

![Webhook – Optional (Valfri) och Complete (Fullständig)](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-02.png)

Nu är Azure Automation-konfigurationen klar. Du kan testa webhookarna med ett enkelt Postman-test för att kontrollera att webhooken fungerar. Nu måste du skapa logikappen för orkestrering.

## <a name="create-an-azure-logic-app-for-orchestration"></a>Skapa en Azure-logikapp för orkestrering

Med Logic Apps kan du skapa, schemalägga och automatisera processer som arbetsflöden så att du kan integrera appar, data, system och tjänster i företag i organisationer. I det här scenariot gör [logikappen](../../logic-apps/index.yml) som du skapar lite mer än att bara anropa Automation-webhooken som du skapade.

Budgetar kan konfigureras att utlösa ett meddelande när ett angivet tröskelvärde nås. Du kan ange flera tröskelvärden då du vill aviseras. Logikappen ger dig möjlighet att utföra olika åtgärder baserat på tröskelvärdet. I det här exemplet ska du skapa ett scenario där du får ett par aviseringar. Det första meddelandet får du när 80 % av budgeten har nåtts, och det andra meddelandet skickas när 100 % av budgeten har nåtts. Logikappen används för att stänga av alla virtuella datorer i resursgruppen. Först nås tröskelvärdet **Optional** (Valfri) vid 80 %. Sedan nås det andra tröskelvärdet, vilket gör att alla virtuella datorer i prenumerationen stoppas.

Med logikappar kan du ange ett exempelschema för HTTP-utlösaren, men du måste ange **Content-Type**-huvudet. Eftersom åtgärdsgruppen inte har anpassade sidhuvuden för webhooken, måste du parsa nyttolasten i ett separat steg. Du ska använda åtgärden **Parsa** med en exempelnyttolast.

### <a name="create-the-logic-app"></a>Skapa logikappen

Logikappen ska utföra flera åtgärder. Följande lista är en översikt över de åtgärder som logikappen ska utföra:

- Identifiera när en HTTP-begäran tas emot
- Parsa överförda JSON-data för att fastställa tröskelvärdet som har nåtts
- Använd en villkorlig instruktion för att kontrollera om tröskelbeloppet har nått 80 % eller mer av budgetintervallet, men inte mer än eller lika med 100 %.
  - Skicka en HTTP POST med webhooken **Optional** (Valfri) om det här tröskelbeloppet har nåtts. Den här åtgärden stänger av de virtuella datorerna i gruppen ”Optional” (Valfri).
- Använd en villkorlig instruktion för att kontrollera om tröskelbeloppet har nått eller överskridit 100 % av budgetvärdet.
  - Skicka en HTTP POST med webhooken **Complete** (Fullständig) om det här tröskelvärdet har nåtts. Den här åtgärden stänger av alla återstående virtuella datorer.

Följande steg krävs för att skapa logikappen som ska utföra stegen ovan:

1. På [Azure-portalen](https://portal.azure.com/) väljer du **Skapa en resurs** > **Integrering** > **Logikapp**.  
    ![Azure – Välja logikappresursen](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-03.png)
1. På området **Skapa logikapp** anger du informationen som behövs för att skapa din logikapp. Välj **Fäst på instrumentpanelen** och välj sedan **Skapa**.  
    ![Azure – Skapa en logikapp](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-03a.png)

När Azure har distribuerat din app öppnas **Logic Apps Designer**. Ett område med en introduktionsvideo och vanliga utlösare visas.

### <a name="add-a-trigger"></a>Lägga till en utlösare

Varje logikapp måste börja med en utlösare som utlöses när en specifik händelse sker eller när ett särskilt villkor uppfylls. Varje gång utlösaren körs skapar Logic Apps-motorn en logikappinstans som startar och kör ditt arbetsflöde. Åtgärder är alla steg som sker efter utlösaren.

1. Välj **Tom logikapp** under **Mallar** på området **Logic Apps Designer**.
1. Lägg till en [utlösare](../../logic-apps/logic-apps-overview.md#logic-app-concepts) genom att ange ”http request” i sökrutan **Logic Apps Designer** och leta upp och välj utlösaren med namnet **Begäran – När en HTTP-förfrågan tas emot**.  
    ![Azure – Logikapp – HTTP-utlösare](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-04.png)
1. Välj **Nytt steg** > **Lägg till en åtgärd**.  
    ![Azure – Nytt steg – Lägg till en åtgärd](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-05.png)
1. Sök efter ”Parsa JSON” i sökrutan i **Logic Apps Designer** och leta upp och välj **åtgärden** [Dataåtgärder – Parsa JSON](../../logic-apps/logic-apps-overview.md#logic-app-concepts).  
    ![Azure – Logikapp – Lägg till åtgärden Parsa JSON](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-06.png)
1. Ange ”Payload” (nyttolast) som **innehållsnamn** för nyttolasten Parsa JSON eller använd taggen ”Body” (innehåll) från dynamiskt innehåll.
1. Välj alternativet **Generera schemat genom att använda en exempelnyttolast** i rutan **Parsa JSON**.  
    ![Azure – Logikapp – Generera schema med hjälp av JSON-exempeldata](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-07.png)
1. Klistra in följande JSON-exempelnyttolast i textrutan: `{"schemaId":"AIP Budget Notification","data":{"SubscriptionName":"CCM - Microsoft Azure Enterprise - 1","SubscriptionId":"<GUID>","SpendingAmount":"100","BudgetStartDate":"6/1/2018","Budget":"50","Unit":"USD","BudgetCreator":"email@contoso.com","BudgetName":"BudgetName","BudgetType":"Cost","ResourceGroup":"","NotificationThresholdAmount":"0.8"}}`
   Textrutan ser ut så här:  
    ![Azure – Logikapp – JSON-exempelnyttolasten](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-08.png)
1. Välj **Done** (Klar).

### <a name="add-the-first-conditional-action"></a>Lägga till den första villkorliga åtgärden

Använd en villkorlig instruktion för att kontrollera om tröskelbeloppet har nått 80 % eller mer av budgetintervallet, men inte mer än eller lika med 100 %. Skicka en HTTP POST med webhooken **Optional** (Valfri) om det här tröskelbeloppet har nåtts. Den här åtgärden stänger av de virtuella datorerna i gruppen **Optional** (Valfri).

1. Välj **Nytt steg** > **Lägg till ett villkor**.  
    ![Azure – Logikapp – Lägg till ett villkor](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-09.png)
1. Visa en lista med tillgängliga värden genom att välja textrutan som innehåller `Choose a value` i rutan **Villkor**.  
    ![Azure – Logikapp – Rutan Villkor](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-10.png)
1. Välj **Uttryck** högst upp i listan och ange följande uttryck i uttrycksredigeraren: `float()`  
    ![Azure – Logikapp – Flyttalsuttryck](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-11.png)
1. Välj **Dynamiskt innehåll**, placera markören inuti parentesen () och välj **NotificationThresholdAmount** i listan för att fylla i det fullständiga uttrycket.
   Uttrycket blir då:<br>
    `float(body('Parse_JSON')?['data']?['NotificationThresholdAmount'])`
1. Bekräfta uttrycket genom att välja **OK**.
1. Välj **Större än eller lika med** i listrutan för **villkoret**.
1. I rutan **Välj ett värde** för villkoret anger du `.8`.  
    ![Skärmbild som visar dialogrutan Villkor med valda värden.](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-12.png)
1. Lägg till en till del i villkoret genom att välja **Lägg till** > **Lägg till rad** i rutan Villkor.
1. Välj textrutan som innehåller `Choose a value` i rutan **Villkor**.
1. Välj **Uttryck** högst upp i listan och ange följande uttryck i uttrycksredigeraren:  `float()`
1. Välj **Dynamiskt innehåll**, placera markören inuti parentesen () och välj **NotificationThresholdAmount** i listan för att fylla i det fullständiga uttrycket.
1. Bekräfta uttrycket genom att välja **OK**.
1. Välj **Är mindre än** i listrutan för **villkoret**.
1. I rutan **Välj ett värde** för villkoret anger du `1`.  
    ![Skärmbild som visar dialogrutan Villkor med två villkor.](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-13.png)
1. I rutan **Om sant** väljer du **Lägg till en åtgärd**. Du ska lägga till en HTTP POST-åtgärd som stänger av valfria virtuella datorer.  
    ![Azure – Logikapp – Lägg till en åtgärd](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-14.png)
1. Ange **HTTP** för att söka efter HTTP-åtgärden och välj åtgärden **HTTP – HTTP**.  
    ![Azure – Logikapp – Lägg till HTTP-åtgärd](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-15.png)
1. Välj **Post** som värde för **Metod**.
1. Ange URL:en för webhooken med namnet **Optional** (Valfri), som du skapade tidigare i den här självstudien, som **URI**-värdet.  
    ![Azure – Logikapp – URI för HTTP-åtgärd](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-16.png)
1. Välj **Lägg till en åtgärd** i rutan **Om sant**. Du ska lägga till en e-poståtgärd som skickar ett e-postmeddelande som meddelar mottagaren att de valfria virtuella datorerna har stängts av.
1. Sök efter ”skicka e-postmeddelande” och välj en åtgärd av typen *Skicka e-postmeddelande* baserat på den e-posttjänst som du använder.  
    ![Azure – Logikapp – Åtgärden Skicka e-postmeddelande](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-17.png)

    För personliga Microsoft-konton väljer du **Outlook.com**. För Azure arbets- eller skolkonto väljer du **Office 365 Outlook**. Om du inte har någon anslutning ombeds du logga in på ditt e-postkonto. Logic Apps skapar en anslutning till ditt e-postkonto.
   Du måste tillåta att logikappen får åtkomst till din e-postinformation.  
    ![Azure – Logikapp – Åtkomstmeddelande](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-18.png)
1. Lägg till texten i **Till**, **Ämne** och **Brödtext** för e-postmeddelandet som meddelar mottagaren att de valfria virtuella datorerna har stängts av. Fyll i ämnes- och brödtextfälten med det dynamiska **BudgetName**- och **NotificationThresholdAmount**-innehållet. 
    ![Azure – Logikapp – E-postinformation](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-19.png)

### <a name="add-the-second-conditional-action"></a>Lägga till den andra villkorliga åtgärden

Använd en villkorlig instruktion för att kontrollera om tröskelbeloppet har nått eller överskridit 100 % av budgetvärdet. Skicka en HTTP POST med webhooken **Complete** (Fullständig) om det här tröskelvärdet har nåtts. Den här åtgärden stänger av alla återstående virtuella datorer.

1. Välj **Nytt steg** > **Lägg till ett villkor**.  
    ![Skärmbild som visar dialogrutan If true (Om sant) med Lägg till en åtgärd markerat.](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-20.png)
1. Visa en lista med tillgängliga värden genom att välja textrutan som innehåller `Choose a value` i rutan **Villkor**.
1. Välj **Uttryck** högst upp i listan och ange följande uttryck i uttrycksredigeraren:  `float()`
1. Välj **Dynamiskt innehåll**, placera markören inuti parentesen () och välj **NotificationThresholdAmount** i listan för att fylla i det fullständiga uttrycket.
   Uttrycket liknar då följande:<br>
    `float(body('Parse_JSON')?['data']?['NotificationThresholdAmount'])`
1. Bekräfta uttrycket genom att välja **OK**.
1. Välj **Större än eller lika med** i listrutan för **villkoret**.
1. I rutan **Välj ett värde** för villkoret anger du `1`.  
    ![Azure – Logikapp – Ange villkorsvärde](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-21.png)
1. I rutan **Om sant** väljer du **Lägg till en åtgärd**. Du ska lägga till en HTTP POST-åtgärd som stänger av alla återstående virtuella datorer.  
    ![Skärmbild som visar dialogrutan If true (Om sant) med det område där du kan lägga till en HTTP POST-åtgärd.](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-22.png)
1. Ange **HTTP** för att söka efter HTTP-åtgärden och välj åtgärden **HTTP – HTTP**.
1. Välj **Post** som värde för **Metod**.
1. Ange URL:en för webhooken med namnet **Complete** (Fullständig), som du skapade tidigare i den här självstudien, som **URI**-värdet.  
    ![Skärmbild som visar dialogrutan HTTP, där du kan ange URL-värdet.](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-23.png)
1. Välj **Lägg till en åtgärd** i rutan **Om sant**. Du ska lägga till en e-poståtgärd som skickar ett e-postmeddelande som meddelar mottagaren att de återstående virtuella datorerna har stängts av.
1. Sök efter ”skicka e-postmeddelande” och välj en åtgärd av typen *Skicka e-postmeddelande* baserat på den e-posttjänst som du använder.
1. Lägg till texten i **Till**, **Ämne** och **Brödtext** för e-postmeddelandet som meddelar mottagaren att de valfria virtuella datorerna har stängts av. Fyll i ämnes- och brödtextfälten med det dynamiska **BudgetName**- och **NotificationThresholdAmount**-innehållet.  
    ![Azure – Logikapp – Information för ”Skicka e-postmeddelande”](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-24.png)
1. Välj **Spara** längst upp på området **Logic App Designer**.

### <a name="logic-app-summary"></a>Sammanfattning av logikappen

Så här ser logikappen ut när du är klar. I de flesta scenarier där du inte behöver tröskelbaserad orkestrering kan du anropa Automation-skriptet direkt från **Monitor** och hoppa över **logikappsteget**.

![Azure – Logikapp – vyn Complete (Fullständig)](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-25.png)

När du sparade logikappen genererades en URL som du kan anropa. Du ska använda den här URL:en i nästa del av den här självstudien.

## <a name="create-an-azure-monitor-action-group"></a>Skapa en Azure Monitor-åtgärdsgrupp

En åtgärdsgrupp är en samling aviseringsinställningar som du definierar. När en avisering utlöses kan en speciell åtgärdsgrupp meddelas och ta emot aviseringen. En Azure-avisering genererar proaktivt ett meddelande baserat på vissa villkor och ger möjlighet att vidta åtgärder. En avisering kan använda data från flera källor, inklusive mått och loggar.

Åtgärdsgrupper är den enda slutpunkten som du ska integrera med din budget. Du kan ställa in aviseringar i ett antal kanaler, men i det här scenariot ska du fokusera på logikappen som du skapade tidigare i den här självstudien.

### <a name="create-an-action-group-in-azure-monitor"></a>Skapa en åtgärdsgrupp i Azure Monitor

När du skapar åtgärdsgruppen pekar du på logikappen som du skapade tidigare i den här självstudien.

1. Om du inte redan är inloggad på [Azure-portalen](https://portal.azure.com/) loggar du in och väljer **Alla tjänster** > **Övervaka**.
1. Välj **Aviseringar** och sedan **Hantera åtgärder**.
1. Välj **Lägg till en åtgärdsgrupp** på området **Åtgärdsgrupper**.
1. Lägg till och verifiera följande objekt:
    - Namn på åtgärdsgrupp
    - Kort namn
    - Prenumeration
    - Resursgrupp  
    ![Azure – Logikapp – Lägg till en åtgärdsgrupp](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-26.png)
1. Lägg till en LogicApp-åtgärd i fönstret **Lägg till åtgärdsgrupp**. Ge åtgärden namnet **Budget-BudgetLA**. Välj **prenumerationen** och **resursgruppen** i rutan **Logikapp**. Välj sedan **logikappen** som du skapade tidigare i den här självstudien.
1. Ange logikappen genom att välja **OK**. Skapa åtgärdsgruppen genom att välja **OK** i rutan **Lägg till åtgärdsgrupp**.

Du är klar med alla stödkomponenter som krävs för att effektivt orkestrera din budget. Nu behöver du bara skapa budgeten och konfigurera den så att den använder den åtgärdsgrupp som du skapat.

## <a name="create-the-azure-budget"></a>Skapa Azure-budgeten

Du kan skapa en budget på Azure-portalen med hjälp av [budgetfunktionen](../costs/tutorial-acm-create-budgets.md) i Cost Management. Du kan också skapa en budget med hjälp av REST-API:er, PowerShell-cmdlets eller CLI. I följande procedur används REST-API:et. Innan du anropar REST-API:et behöver du en autentiseringstoken. Du kan skapa en autentiseringstoken genom att använda [ARMClient](https://github.com/projectkudu/ARMClient)-projektet. Med **ARMClient** kan du autentisera dig själv mot Azure Resource Manager och hämta en token för att anropa API:erna.

### <a name="create-an-authentication-token"></a>Skapa en autentiseringstoken

1. Navigera till [ARMClient](https://github.com/projectkudu/ARMClient)-projektet på GitHub.
1. Klona lagringsplatsen för att få en lokal kopia.
1. Öppna projektet i Visual Studio och skapa det.
1. När kompileringen är klar bör den körbara filen finnas i mappen *\bin\debug*.
1. Kör ARMClient. Öppna en kommandotolk och navigera till mappen *\bin\debug* från projektroten.
1. Logga in och autentisera genom att ange följande kommando i kommandotolken:<br>
    `ARMClient login prod`
1. Kopiera **prenumerationens GUID** från kommandots utdata.
1. Du kan kopiera en autentiseringstoken till Urklipp genom att ange följande kommando i kommandotolken, men var noga med att använda det kopierade prenumerations-ID:t från steget ovan: <br>
    `ARMClient token <subscription GUID from previous step>`

    När du har utfört steget ovan visas följande:<br>
    **Token har kopierats till Urklipp**.
1. Spara token. Den ska användas i steg i nästa del av den här självstudien.

### <a name="create-the-budget"></a>Skapa budgeten

Nu ska du konfigurera **Postman** för att skapa en budget genom att anropa REST-API:erna för Azure-förbrukning. Postman är en API-utvecklingsmiljö. Du ska importera miljö- och samlingsfiler till Postman. Samlingen innehåller grupperade definitioner av HTTP-begäranden som anropar Azure REST-API:er. Miljöfilen innehåller variabler som används av samlingen.

1. Ladda ned och öppna [REST-klienten för Postman](https://www.getpostman.com/) för att köra REST-API:erna.
1. Skapa en ny begäran i Postman.  
    ![Postman – Skapa en ny begäran](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-27.png)
1. Spara den nya begäran som en samling så att den inte innehåller något.  
    ![Postman – Spara den nya begäran](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-28.png)
1. Ändra begäran från en `Get`- till en `Put`-åtgärd.
1. Ändra följande URL genom att ersätta `{subscriptionId}` med **prenumerations-ID:t** som du använde i föregående avsnitt i den här självstudien. Ändra också URL:en så att den innehåller ”SampleBudget” som värde för `{budgetName}`: `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/budgets/{budgetName}?api-version=2018-03-31`
1. Välj fliken **Sidhuvuden** i Postman.
1. Lägg till en ny **nyckel** med namnet ”Authorization” (Auktorisering).
1. Ange **Värde** till den token som skapades med ArmClient sist i det förra avsnittet.
1. Välj fliken **Body** (Brödtext) i Postman.
1. Välj knappalternativet **raw** (rådata).
1. I textrutan klistrar du in exempelbudgetdefinitionen nedan. Ersätt parametrarna `subscriptionID`, `resourcegroupname` och `actiongroupname` med ditt prenumerations-ID, ett unikt namn för din resursgrupp och åtgärdsgruppsnamnet som du skapade både i URL:en och begärandetexten:

    ```
        {
            "properties": {
                "category": "Cost",
                "amount": 100.00,
                "timeGrain": "Monthly",
                "timePeriod": {
                "startDate": "2018-06-01T00:00:00Z",
                "endDate": "2018-10-31T00:00:00Z"
                },
                "filters": {},
            "notifications": {
                "Actual_GreaterThan_80_Percent": {
                    "enabled": true,
                    "operator": "GreaterThan",
                    "threshold": 80,
                    "contactEmails": [],
                    "contactRoles": [],
                    "contactGroups": [
                        "/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/microsoft.insights/actionGroups/{actiongroupname}"
                    ]
                },
               "Actual_EqualTo_100_Percent": {
                       "operator": "EqualTo",
                       "threshold": 100,
                       "contactGroups": [
                           "/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/microsoft.insights/actionGroups/{actiongroupname}"
                       ]
                   }
               }
            }
        }
    ```
1. Skicka begäran genom att klicka på **Skicka**.

Nu har du alla delar som du behöver för att anropa [API:et för budgetar](/rest/api/consumption/budgets). Referensen för API:et för budgetar har ytterligare information om de specifika begärandena, inklusive:

- **budgetName** – Flera budgetar stöds.  Budgetnamnen måste vara unika.
- **category** – Måste vara antingen **Cost** eller **Usage**. API:et stöder både kostnads- och användningsbudgetar.
- **timeGrain** – En månads-, kvartals- eller årsbudget. Mängden återställs i slutet av perioden.
- **filters** – Filter gör att du kan begränsa budgeten till en specifik uppsättning resurser i det valda omfånget. Ett filter kan till exempel vara en samling resursgrupper för en budget på en prenumerationsnivå.
- **notifications** – Bestämmer meddelandeinformationen och tröskelvärdena. Du kan ställa in flera tröskelvärden och ange en e-postadress eller en åtgärdsgrupp för att få aviseringar.

## <a name="summary"></a>Sammanfattning

Med den här självstudien har du lärt dig:

- Hur du skapar en Azure Automation-runbook för att stoppa virtuella datorer.
- Hur du skapar en logikapp i Azure som utlöses baserat på budgettrösklar och hur du anropar den relaterade runbooken med rätt parametrar.
- Hur du skapar en Azure Monitor-åtgärdsgrupp som konfigurerats för att utlösa Azure-logikappen när budgettröskeln nås.
- Hur du skapar Azure-budgeten med önskade tröskelvärden och kopplar den till åtgärdsgruppen.

Nu har du en helt fungerande budget för din prenumeration, som stänger av dina virtuella datorer när de konfigurerade budgettrösklarna nås.

## <a name="next-steps"></a>Nästa steg

- Mer information om faktureringsscenarier i Azure finns i [Automatiseringsscenarier för fakturerings- och kostnadshantering](cost-management-automation-scenarios.md).