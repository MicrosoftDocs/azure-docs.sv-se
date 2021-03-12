---
title: Självstudie – Skapa och hantera Azure-budgetar
description: Den här självstudien hjälper dig att planera och ta hänsyn till kostnaderna för de Azure-tjänster som du använder.
author: bandersmsft
ms.author: banders
ms.date: 03/09/2021
ms.topic: tutorial
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.custom: seodec18
ms.openlocfilehash: 2c0853b68e432fa94e661e68486b9e58b21aa845
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103012455"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>Självstudier: Skapa och hantera Azure-budgetar

Budgetar i Cost Management hjälper dig att planera och öka organisationsansvar. De hjälper dig att informera andra om sina utgifter för att proaktivt hantera kostnader och för att övervaka hur utgifter fortskrider över tid. Du kan konfigurera aviseringar baserat på din faktiska kostnad eller prognostiserad kostnad för att se till att dina utgifter ligger inom din organisations utgifts gräns. När de budgettrösklar som du har skapat har överskridits utlöses bara meddelanden. Ingen av dina resurser påverkas och förbrukningen stoppas inte. Du kan använda budgetar för att jämföra och spåra utgifter när du analyserar kostnader.

Information om kostnader och användning är normalt tillgänglig inom 8 till 24 timmar, och budgetar utvärderas mot dessa kostnader var 24:e timme. Se till att du läser och förstår informationen i [Uppdateringar av kostnader och användningsdata](./understand-cost-mgt-data.md#cost-and-usage-data-updates-and-retention). När en budgettröskel nås skickas normalt e-postaviseringar inom en timme från utvärderingen.

Budgetar återställs automatiskt i slutet av en period (månadsvis, kvartalsvis eller årsvis) för samma budgetbelopp när du väljer ett förfallodatum i framtiden. Eftersom de återställs med samma budgetbelopp måste du skapa separata budgetar när budgeterade valutabelopp skiljer sig åt för framtida perioder. När en budget går ut tas den bort automatiskt.

Exemplen i den här självstudien vägleder dig genom att skapa och redigera en budget för en prenumeration på ett Azure Enterprise-avtal (EA).

Titta på videon [Apply budgets to subscriptions using the Azure portal](https://www.youtube.com/watch?v=UrkHiUx19Po) (Använda budgetar för prenumerationer med Azure-portalen) för att se hur du kan skapa budgetar i Azure för att övervaka utgifterna. Om du vill titta på andra videor går du till [YouTube-kanalen för Cost Management](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/UrkHiUx19Po]

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en budget i Azure-portalen
> * Skapa och redigera budgetar med PowerShell
> * Skapa en budget med en Azure Resource Manager-mall

## <a name="prerequisites"></a>Krav

Budgetar stöds för följande typer av Azure-konton och omfång:

- Azure RBAC-omfång (rollbaserad åtkomstkontroll)
    - Hanteringsgrupper
    - Prenumeration
- Omfång för Enterprise-avtal
    - Faktureringskonto
    - Avdelning
    - Registreringskonto
- Enskilda avtal
    - Faktureringskonto
- Omfång för Microsoft-kundavtal
    - Faktureringskonto
    - Faktureringsprofil
    - Fakturaavsnitt
    - Kund
- AWS-omfång
    - Externt konto
    - Extern prenumeration


Om du vill visa budgetar behöver du minst läsbehörighet för ditt Azure-konto.

Om du har en ny prenumeration kan du inte skapa en budget eller använda Cost Management-funktioner direkt. Det kan ta upp till 48 timmar innan du kan använda alla Cost Management-funktioner.

För Azure EA-prenumerationer måste du ha läsbehörighet för att visa budgetar. Du måste ha deltagarbehörighet för att skapa och hantera budgetar.

Följande Azure-behörigheter, eller -omfång, stöds per prenumeration för budgetar efter användare och grupp. Mer information om omfång finns i [Förstå och arbeta med omfång](understand-work-scopes.md).

- Ägare – Kan skapa, ändra och ta bort budgetar för en prenumeration.
- Deltagare och Cost Management-deltagare – Kan skapa, ändra och ta bort sina egna budgetar. Kan ändra budgetbeloppet för budgetar som skapats av andra.
- Läsare och Cost Management-läsare – Kan visa budgetar som de har behörighet till.

Mer information om hur du tilldelar åtkomst till Cost Management-data finns i [Tilldela åtkomst till Cost Management-data](./assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure

- Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-budget-in-the-azure-portal"></a>Skapa en budget i Azure-portalen

Du kan skapa en prenumerationsbudget för Azure per månad, kvartal eller år.

Om du vill skapa eller Visa en budget öppnar du ett omfång i Azure Portal och väljer **budgetar** i menyn. Du kan till exempel navigera till **Prenumerationer**, välja en prenumeration i listan och sedan välja **Budgetar** på menyn. Använd **Omfång** om du vill byta till ett annat omfång, exempelvis en hanteringsgrupp, i Budgetar. Mer information om omfång finns i [Förstå och arbeta med omfång](understand-work-scopes.md).

När du har skapat budgetar visas en enkel vy över dina aktuella utgifter.

Välj **Lägg till**.

:::image type="content" source="./media/tutorial-acm-create-budgets/budgets-cost-management.png" alt-text="Screenthost visar en lista över budgetar som redan har skapats." lightbox="./media/tutorial-acm-create-budgets/budgets-cost-management.png" :::

Kontrollera att det angivna definitionsområdet är korrekt i fönstret **Skapa budget**. Välj de filter som du vill lägga till. Med filter kan du skapa budgetar för vissa kostnader, till exempel resursgrupper i en prenumeration eller tjänst som virtuella datorer. Alla filter som du kan använda i kostnadsanalyser kan också tillämpas på en budget.

När du har identifierat ditt omfång och filter anger du ett budget namn. Välj sedan en återställningsperiod för budgeten – varje månad, kvartal eller år. Återställnings perioden bestämmer tids perioden som analyseras av budgeten. Kostnaden som utvärderas av budgeten börjar vid noll i början av varje ny period. När du skapar en kvartalsbudget fungerar den på samma sätt som en månatlig budget. Skillnaden är att budgetbeloppet för kvartalet delas jämnt mellan de tre månaderna i kvartalet. Ett årligt budgetbelopp delas jämnt mellan alla 12 månader på kalenderåret.

Om du har en prenumeration enligt principen betala per användning, MSDN eller Visual Studio kan faktureringsperioden inte justeras till den kalendermånaden. För dessa prenumerationstyper och resursgrupper kan du skapa en budget som är justerad till din fakturaperiod eller till kalendermånader. Om du vill skapa en budget som är anpassad till din fakturaperiod väljer du en återställningsperiod, antingen **faktureringsmånad**, **faktureringskvartal** eller **faktureringsår**. Om du vill skapa en budget som är anpassad efter kalendermånaden väljer du återställningsperioden **Månadsvis**, **Varje kvartal** eller **Varje år**.

Sedan identifierar du förfallodatumet då budgeten blir ogiltig och slutar utvärdera dina kostnader.

Baserat på de fält som valts i budgeten så långt visas ett diagram som hjälper dig att välja ett tröskelvärde som ska användas för din budget. Den föreslagna budgeten baseras på den högsta prognostiserade kostnaden som du kan ådra dig i framtida perioder. Du kan ändra budgetbeloppet.

:::image type="content" source="./media/tutorial-acm-create-budgets/create-monthly-budget.png" alt-text="Skärm bild som visar hur budgeten skapas med månads kostnads data." lightbox="./media/tutorial-acm-create-budgets/create-monthly-budget.png" :::

När du har konfigurerat budget beloppet väljer du **Nästa** för att konfigurera budget aviseringar för faktiska kostnader och prognostiserade budget aviseringar.

## <a name="configure-actual-costs-budget-alerts"></a>Konfigurera budget aviseringar för faktiska kostnader

Budgetar kräver minst en kostnadströskel (% av budgeten) och en motsvarande e-postadress. Du kan också använda upp till fem tröskelvärden och fem e-postadresser i en enda budget. När en budgettröskel nås skickas normalt e-postaviseringar inom en timme från utvärderingen. Faktiska kostnader för budget aviseringar skapas för den faktiska kostnad som du har periodiserat i förhållande till de angivna budget tröskelvärdena.

## <a name="configure-forecasted-budget-alerts"></a>Konfigurera prognostiserade budget aviseringar

Med prognostiserade aviseringar får du ett avancerat meddelande om att dina utgifts trender skulle överskrida budgeten. Aviseringarna använder [prognostiserade kostnads förutsägelser](quick-acm-cost-analysis.md#understand-forecast). Aviseringar genereras när den prognostiserade kostnads projektionen överskrider det angivna tröskelvärdet. Du kan konfigurera ett beräknat tröskelvärde (% av budget). När ett beräknat budget tröskelvärde är uppfyllt skickas meddelanden vanligt vis inom en timme från utvärderingen.

Om du vill växla mellan att konfigurera en faktisk kontra prognostiserad kostnads avisering använder du `Type` fältet när du konfigurerar aviseringen som visas i följande bild.

Om du vill få e-postmeddelanden lägger du till azure-noreply@microsoft.com i din lista med godkända avsändare så att meddelandena inte hamnar i skräppostmappen. Mer information om händelseaviseringar finns i [Använda kostnadsaviseringar](./cost-mgt-alerts-monitor-usage-spending.md).

I följande exempel skapas en e-postavisering när 90% av budgeten nås. Om du skapar en budget med budget-API:et kan du även tilldela roller till personer som ska ta emot aviseringar. Det finns inte stöd för att tilldela roller till personer i Azure-portalen. Mer information om API:er för Azure-budgetar finns i [API för budgetar](/rest/api/consumption/budgets). Se artikeln [Nationella inställningar som stöds för budgetavisering via e-post](manage-automation.md#supported-locales-for-budget-alert-emails) om du vill att en e-postavisering ska skickas på ett annat språk.

Aviserings gränser har stöd för ett intervall på 0,01% till 1000% av den budget tröskel som du har angett.

:::image type="content" source="./media/tutorial-acm-create-budgets/budget-set-alert.png" alt-text="Skärm bild som visar aviserings villkor." lightbox="./media/tutorial-acm-create-budgets/budget-set-alert.png" :::

När du har skapat en budget visas den i kostnadsanalysen. Att visa din budget i förhållande till din utgiftstrend är ett av de första stegen när du börjar [analysera dina kostnader och utgifter](./quick-acm-cost-analysis.md).

:::image type="content" source="./media/tutorial-acm-create-budgets/cost-analysis.png" alt-text="Skärm bild som visar en exempel budget med utgifter som visas i kostnads analys." lightbox="./media/tutorial-acm-create-budgets/cost-analysis.png" :::

I föregående exempel skapade du en budget för en prenumeration. Du kan också skapa en budget för en resursgrupp. Om du vill skapa en budget för en resursgrupp går du till **Kostnadshantering + fakturering** &gt; **Prenumerationer** &gt; väljer en prenumeration > **Resursgrupper** > väljer en resursgrupp > **Budgetar** > och därefter **Lägg till** en budget.

### <a name="create-a-budget-for-combined-azure-and-aws-costs"></a>Skapa en budget för kombinerade Azure- och AWS-kostnader

Du kan gruppera dina kostnader för Azure och AWS tillsammans genom att tilldela en hanteringsgrupp till din anslutning tillsammans med dess konsoliderade och länkade konton. Tilldela dina Azure-prenumerationer till samma hanteringsgrupp. Skapa sedan en budget för de kombinerade kostnaderna.

1. I Cost Management väljer du **Budgetar**.
1. Välj **Lägg till**.
1. Välj **Change scope** (Ändra omfång) och välj sedan hanteringsgruppen.
1. Fortsätt att skapa budgeten tills den är klar.

## <a name="costs-in-budget-evaluations"></a>Kostnader i budgetutvärderingar

Utvärderingar av budgetkostnader inkluderar nu reserverad instans och inköpsdata. Om avgifterna gäller för dig kan du få aviseringar när avgifterna ingår i utvärderingarna. Logga in på [Azure Portal](https://portal.azure.com) för att kontrol lera att budget trösklarna är korrekt konfigurerade för att kunna redovisa nya kostnader. Dina debiterade Azure-avgifter ändras inte. Budgetarna utvärderas nu mot en mer fullständig uppsättning kostnader. Om avgifterna inte gäller för dig är ditt budgetbeteende oförändrat.

Lägg till följande filter i budgeten om du vill filtrera nya kostnader så att budgetar endast utvärderas mot avgifter för Azure-resursförbrukning från första part:

- Typ av utgivare: Azure
- Kostnadstyp: Användning

Utvärderingar av budgetkostnader baseras på faktiska kostnader. De omfattar inte amortering. Mer information om vilka filtreringsalternativ som är tillgängliga i budgetarna finns [Förstå alternativ för gruppering och filtrering](group-filter.md).

## <a name="trigger-an-action-group"></a>Utlös en åtgärdsgrupp

När du skapar eller redigerar en budget för en prenumeration eller resursgruppsomfång kan du konfigurera den så att den anropar en åtgärdsgrupp. Åtgärdsgruppen kan utföra olika åtgärder när ditt budgettröskelvärde är uppfyllt. 

Åtgärdsgrupper stöds för närvarande endast för prenumerations- och resursgruppsomfång. Mer information om hur du skapar åtgärds grupper finns i [Konfigurera grundläggande inställningar för åtgärds grupper](../../azure-monitor/alerts/action-groups.md#configure-basic-action-group-settings). 

Mer information om hur du använder budgetbaserad automatisering med åtgärdsgrupper finns i [Hantera kostnader med Azure Budgets](../manage/cost-management-budget-scenario.md).

Om du vill skapa eller uppdatera åtgärds grupper väljer du **Hantera åtgärds grupp** när du skapar eller redigerar en budget.

:::image type="content" source="./media/tutorial-acm-create-budgets/trigger-action-group.png" alt-text="Skärm bild som visar ett exempel på hur du skapar en budget för att Visa hantera åtgärds grupper." lightbox="./media/tutorial-acm-create-budgets/trigger-action-group.png" :::

Välj sedan **Lägg till åtgärdsgrupp** och skapa åtgärdsgruppen.

Budgetintegrering med åtgärdsgrupper fungerar bara för åtgärdsgrupper som har det gemensamma aviseringsschemat inaktiverat. Mer information om hur du inaktiverar schemat finns i [How do I enable the common alert schema?](../../azure-monitor/alerts/alerts-common-schema.md#how-do-i-enable-the-common-alert-schema) (Hur gör jag för att aktivera det vanliga aviseringsschemat?)

## <a name="create-and-edit-budgets-with-powershell"></a>Skapa och redigera budgetar med PowerShell

Om du är EA-kund kan du skapa och redigera budgetar program mässigt med hjälp av Azure PowerShell-modulen. Om du vill hämta den senaste versionen av Azure PowerShell kör du följande kommando:

```azurepowershell-interactive
install-module -name Az
```

Med följande exempelkommandon skapas en budget.

```azurepowershell-interactive
#Sign into Azure Powershell with your account

Connect-AzAccount

#Select a subscription to to monitor with a budget

select-AzSubscription -Subscription "Your Subscription"

#Create an action group email receiver and corresponding action group

$email1 = New-AzActionGroupReceiver -EmailAddress test@test.com -Name EmailReceiver1
$ActionGroupId = (Set-AzActionGroup -ResourceGroupName YourResourceGroup -Name TestAG -ShortName TestAG -Receiver $email1).Id

#Create a monthly budget that sends an email and triggers an Action Group to send a second email. Make sure the StartDate for your monthly budget is set to the first day of the current month. Note that Action Groups can also be used to trigger automation such as Azure Functions or Webhooks.

New-AzConsumptionBudget -Amount 100 -Name TestPSBudget -Category Cost -StartDate 2020-02-01 -TimeGrain Monthly -EndDate 2022-12-31 -ContactEmail test@test.com -NotificationKey Key1 -NotificationThreshold 0.8 -NotificationEnabled -ContactGroup $ActionGroupId
```

## <a name="create-a-budget-with-an-azure-resource-manager-template"></a>Skapa en budget med en Azure Resource Manager-mall

Du kan skapa en budget med hjälp av en Azure Resource Manager-mall. Information om hur du använder mallen finns i [Skapa en budget med en Azure Resource Manager-mall](quick-create-budget-template.md).

## <a name="clean-up-resources"></a>Rensa resurser

Om du har skapat en budget och inte längre behöver den kan du visa informationen om den och ta bort den.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en budget i Azure-portalen
> * Skapa och redigera budgetar med PowerShell
> * Skapa en budget med en Azure Resource Manager-mall

Gå vidare till nästa självstudie för att skapa en återkommande export för dina kostnadshanteringsdata.

> [!div class="nextstepaction"]
> [Skapa och hantera exporterade data](tutorial-export-acm-data.md)