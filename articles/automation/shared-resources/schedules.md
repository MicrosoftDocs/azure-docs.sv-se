---
title: Hantera scheman i Azure Automation
description: Den här artikeln beskriver hur du skapar och arbetar med ett schema i Azure Automation.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/19/2021
ms.topic: conceptual
ms.openlocfilehash: a829cd946f36fb5996405ba00945e9f9cb65d162
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105544238"
---
# <a name="manage-schedules-in-azure-automation"></a>Hantera scheman i Azure Automation

Om du vill schemalägga en Runbook i Azure Automation starta vid en viss tid länkar du den till ett eller flera scheman. Ett schema kan konfigureras att antingen köras en gång eller på ett återkommande tim-eller dagligt schema för Runbooks i Azure Portal. Du kan också schemalägga dem för varje vecka, varje månad, specifika dagar i veckan eller dagar i månaden eller en viss dag i månaden. En runbook kan länkas till flera scheman och ett schema kan vara kopplat till flera runbooks.

> [!NOTE]
> Azure Automation stöder sommar tid och schemalägger den på lämpligt sätt för automatiserings åtgärder.

> [!NOTE]
> Scheman som för närvarande inte är aktiverade för Azure Automation DSC-konfigurationer.

## <a name="powershell-cmdlets-used-to-access-schedules"></a>PowerShell-cmdletar som används för att få åtkomst till scheman

Cmdletarna i följande tabell skapar och hanterar Automation-scheman med PowerShell. De levereras som en del av [AZ-modulerna](modules.md#az-modules).

| Cmdletar | Beskrivning |
|:--- |:--- |
| [Get-AzAutomationSchedule](/powershell/module/Az.Automation/Get-AzAutomationSchedule) |Hämtar ett schema. |
| [Get-AzAutomationScheduledRunbook](/powershell/module/az.automation/get-azautomationscheduledrunbook) |Hämtar schemalagda Runbooks. |
| [New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule) |Skapar ett nytt schema. |
| [Registrera – AzAutomationScheduledRunbook](/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook) |Associerar en Runbook med ett schema. |
| [Remove-AzAutomationSchedule](/powershell/module/Az.Automation/Remove-AzAutomationSchedule) |Tar bort ett schema. |
| [Set-AzAutomationSchedule](/powershell/module/Az.Automation/Set-AzAutomationSchedule) |Anger egenskaperna för ett befintligt schema. |
| [Avregistrera-AzAutomationScheduledRunbook](/powershell/module/Az.Automation/Unregister-AzAutomationScheduledRunbook) |Kopplar bort en Runbook från ett schema. |

## <a name="create-a-schedule"></a>Skapa ett schema

Du kan skapa ett nytt schema för dina runbooks från Azure Portal, med PowerShell eller med en Azure Resource Manager ARM-mall (ARM). För att undvika att dina runbooks påverkar dina runbooks och de processer de automatiserar bör du först testa alla Runbooks som har länkade scheman med ett Automation-konto dedikerat för testning. Ett test verifierar att dina schemalagda Runbooks fortfarande fungerar korrekt. Om du ser ett problem kan du felsöka och tillämpa eventuella ändringar som krävs innan du migrerar den uppdaterade Runbook-versionen till produktionen.

> [!NOTE]
> Ditt Automation-konto får inte automatiskt några nya versioner av moduler om du inte har uppdaterat dem manuellt genom att välja alternativet [Uppdatera Azure-moduler](../automation-update-azure-modules.md) från **moduler**. Azure Automation använder de senaste modulerna i ditt Automation-konto när ett nytt schemalagt jobb körs. 

### <a name="create-a-new-schedule-in-the-azure-portal"></a>Skapa ett nytt schema i Azure Portal

1. Från ditt Automation-konto väljer du **scheman** under **delade resurser** i den vänstra rutan.
2. På sidan **scheman** väljer du **Lägg till ett schema**.
3. Ange ett namn på sidan **nytt schema** och ange en beskrivning för det nya schemat.

    >[!NOTE]
    >Automation-scheman stöder inte för närvarande specialtecken i schema namnet.
    >

4. Välj om schemat körs en gång eller om schemat ska köras genom att välja **en gång** eller **återkommande**. Om du väljer en **gång**, anger du en start tid och väljer sedan **skapa**. Om du väljer **återkommande** anger du en start tid. För **Upprepa varje** väljer du hur ofta du vill att runbooken ska upprepas. Välj per timme, dag, vecka eller månad.

    * Om du väljer **vecka** visas vecko dagarna som du kan välja bland. Välj så många dagar som du vill. Den första körningen av ditt schema sker på den första dagen som väljs efter start tiden. Om du till exempel vill välja ett helg schema väljer du lördag och söndag.

    ![Inställning av återkommande helg schema](../media/schedules/week-end-weekly-recurrence.png)

    * Om du väljer **månad** får du olika alternativ. Välj antingen **månads dagar** eller **vecko dagar** för alternativet **månatlig förekomst** . Om du väljer **månads dagar** visas en kalender så att du kan välja så många dagar som du vill. Om du väljer ett datum, till exempel 31 som inte inträffar under den aktuella månaden, körs inte schemat. Om du vill att schemat ska köras den senaste dagen väljer du **Ja** under **kör på sista dagen i månaden**. Om du väljer **vecko dagar** visas alternativet **Upprepa varje** . Välj **första**, **andra**, **tredje**, **fjärde** eller **sista**. Slutligen väljer du en dag som ska upprepas.

    ![Månads schema på första, femtonde och sista dagen i månaden](../media/schedules/monthly-first-fifteenth-last.png)

5. När du är klar väljer du **skapa**.

### <a name="create-a-new-schedule-with-powershell"></a>Skapa ett nytt schema med PowerShell

Använd cmdleten [New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule) för att skapa scheman. Du anger start tiden för schemat och hur ofta den ska köras. I följande exempel visas hur du skapar många olika schema scenarier.

>[!NOTE]
>Automation-scheman stöder inte för närvarande specialtecken i schema namnet.
>

#### <a name="create-a-one-time-schedule"></a>Skapa ett engångs schema

I följande exempel skapas ett eng ång slö schema.

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>Skapa ett återkommande schema

I följande exempel visas hur du skapar ett återkommande schema som körs varje dag vid 1:00 PM under ett år.

```azurepowershell-interactive
$StartTime = Get-Date "13:00:00"
$EndTime = $StartTime.AddYears(1)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule02" -StartTime $StartTime -ExpiryTime $EndTime -DayInterval 1 -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule"></a>Skapa ett veckovis återkommande schema

I följande exempel visas hur du skapar ett vecko schema som bara körs på vardagar.

```azurepowershell-interactive
$StartTime = (Get-Date "13:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekDays = @([System.DayOfWeek]::Monday..[System.DayOfWeek]::Friday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule03" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule-for-weekends"></a>Skapa ett veckovis återkommande schema för helger

I följande exempel visas hur du skapar ett vecko schema som bara körs på helger.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-the-first-fifteenth-and-last-days-of-the-month"></a>Skapa ett återkommande schema för de första, femtonde och sista dagarna i månaden

I följande exempel visas hur du skapar ett återkommande schema som körs på den första, femtonde och sista dagen i en månad.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="create-a-schedule-with-a-resource-manager-template"></a>Skapa ett schema med en Resource Manager-mall

I det här exemplet använder vi en ARM-mall (Automation Resource Manager) som skapar ett nytt jobb schema. Allmän information om den här mallen för att hantera automatiserings jobb scheman finns i [referens för Microsoft. Automation automationAccounts/jobSchedules Template](/azure/templates/microsoft.automation/2015-10-31/automationaccounts/jobschedules#quickstart-templates).

Kopiera den här mallfilen till en text redigerare:

```json
{
  "name": "5d5f3a05-111d-4892-8dcc-9064fa591b96",
  "type": "Microsoft.Automation/automationAccounts/jobSchedules",
  "apiVersion": "2015-10-31",
  "properties": {
    "schedule": {
      "name": "scheduleName"
    },
    "runbook": {
      "name": "runbookName"
    },
    "runOn": "hybridWorkerGroup",
    "parameters": {}
  }
}
```

Redigera följande parameter värden och spara mallen som en JSON-fil:

* Objekt namn för jobb schema: en GUID (globalt unik identifierare) används som namn på objektet jobb schema.

   >[!IMPORTANT]
   > För varje jobb schema som distribueras med en ARM-mall måste GUID: t vara unikt. Även om du schemalägger om ett befintligt schema måste du ändra GUID. Detta gäller även om du tidigare har tagit bort ett befintligt jobb schema som skapats med samma mall. Att återanvända samma GUID resulterar i en misslyckad distribution.</br></br>
   > Det finns tjänster online som kan generera ett nytt GUID åt dig, till exempel den här [kostnads fria online-GUID-generatorn](https://guidgenerator.com/).

* Schema namn: representerar namnet på det Automation Job-schema som ska länkas till angiven Runbook.
* Runbook-namn: representerar namnet på Automation-runbooken som jobbschemat ska kopplas till.

När filen har sparats kan du skapa ett jobb schema för Runbook med följande PowerShell-kommando. Kommandot använder `TemplateFile` parametern för att ange sökväg och fil namn för mallen.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "<path>\RunbookJobSchedule.json"
```

## <a name="link-a-schedule-to-a-runbook"></a>Länka ett schema till en Runbook

En runbook kan länkas till flera scheman och ett schema kan vara kopplat till flera runbooks. Om en Runbook har parametrar, kan du ange värden för dem. Du måste ange värden för alla obligatoriska parametrar och du kan även ange värden för eventuella valfria parametrar. Dessa värden används varje gång som Runbook startas med det här schemat. Du kan koppla samma Runbook till ett annat schema och ange olika parameter värden.

### <a name="link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Länka ett schema till en Runbook med Azure Portal

1. I Azure Portal, från ditt Automation-konto, väljer du **Runbooks** under **process automatisering**.
1. Välj namnet på den Runbook som ska schemaläggas.
1. Om runbooken inte är länkad till ett schema, erbjuds du alternativet att skapa ett nytt schema eller länka till ett befintligt schema.
1. Om runbook har parametrar, kan du välja alternativet **ändra körnings inställningar (standard: Azure)** och fönstret **parametrar** visas. Du kan ange parameter information här.

### <a name="link-a-schedule-to-a-runbook-with-powershell"></a>Länka ett schema till en Runbook med PowerShell

Använd cmdleten [register-AzAutomationScheduledRunbook](/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook) för att länka ett schema. Du kan ange värden för runbookens parametrar med parametern Parametrar . Mer information om hur du anger parameter värden finns [i starta en Runbook i Azure Automation](../start-runbooks.md).
I följande exempel visas hur du länkar ett schema till en Runbook med hjälp av en Azure Resource Manager-cmdlet med parametrar.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="schedule-runbooks-to-run-more-frequently"></a>Schemalägga Runbooks att köras oftare

Det vanligaste intervallet som ett schema i Azure Automation kan konfigureras för är en timme. Om du kräver att scheman körs oftare än, finns det två alternativ:

* Skapa en [webhook](../automation-webhooks.md) för runbooken och Använd [Azure Logic Apps](../../logic-apps/logic-apps-overview.md) för att anropa webhooken. Azure Logic Apps ger mer detaljerade granularitet för att definiera ett schema.

* Skapa fyra scheman som alla börjar inom 15 minuter från varandra och kör en gång i timmen. Det här scenariot gör att runbooken kan köras var 15: e minut med olika scheman.

## <a name="disable-a-schedule"></a>Inaktivera ett schema

När du inaktiverar ett schema körs inte längre någon Runbook som är länkad till den schemat. Du kan manuellt inaktivera ett schema eller ange en förfallo tid för scheman med en frekvens när du skapar dem. När förfallo tiden uppnås inaktive ras schemat.

### <a name="disable-a-schedule-from-the-azure-portal"></a>Inaktivera ett schema från Azure Portal

1. I ditt Automation-konto väljer du **scheman** under **delade resurser** i den vänstra rutan.
1. Välj namnet på ett schema för att öppna informations fönstret.
1. Ändringen har **Aktiver ATS** till **Nej**.

> [!NOTE]
> Om du vill inaktivera ett schema som har en start tid tidigare, måste du ändra start datumet till en gång i framtiden innan du sparar det.

### <a name="disable-a-schedule-with-powershell"></a>Inaktivera ett schema med PowerShell

Använd cmdleten [set-AzAutomationSchedule](/powershell/module/Az.Automation/Set-AzAutomationSchedule) för att ändra egenskaperna för ett befintligt schema. Om du vill inaktivera schemat anger du falskt för `IsEnabled` parametern.

I följande exempel visas hur du inaktiverar ett schema för en Runbook med hjälp av en Azure Resource Manager-cmdlet.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="remove-a-schedule"></a>Ta bort ett schema

När du är redo att ta bort dina scheman kan du antingen använda Azure Portal eller PowerShell. Kom ihåg att du bara kan ta bort ett schema som har inaktiverats enligt beskrivningen i föregående avsnitt.

### <a name="remove-a-schedule-using-the-azure-portal"></a>Ta bort ett schema med hjälp av Azure Portal

1. I ditt Automation-konto väljer du **scheman** under **delade resurser** i den vänstra rutan.
2. Välj namnet på ett schema för att öppna informations fönstret.
3. Klicka på **Ta bort**.

### <a name="remove-a-schedule-with-powershell"></a>Ta bort ett schema med PowerShell

Du kan använda `Remove-AzAutomationSchedule` cmdleten som visas nedan om du vill ta bort ett befintligt schema.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Remove-AzAutomationSchedule -AutomationAccountName $automationAccountName `
-Name $scheduleName -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>Nästa steg

* Mer information om de cmdletar som används för att få åtkomst till scheman finns [i hantera moduler i Azure Automation](modules.md).
* Allmän information om Runbooks finns [i Runbook-körning i Azure Automation](../automation-runbook-execution.md).