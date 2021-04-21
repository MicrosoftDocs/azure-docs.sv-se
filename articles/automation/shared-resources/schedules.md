---
title: Hantera scheman i Azure Automation
description: Den här artikeln beskriver hur du skapar och arbetar med ett schema i Azure Automation.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/29/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f119c15cbbfd9586bdb06fdffad0b12c9a441eea
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832652"
---
# <a name="manage-schedules-in-azure-automation"></a>Hantera scheman i Azure Automation

Om du vill schemalägga en runbook Azure Automation att starta vid en angiven tidpunkt länkar du den till ett eller flera scheman. Ett schema kan konfigureras att antingen köras en gång eller enligt ett återkommande schema per timme eller dagligen för runbooks i Azure Portal. Du kan också schemalägga dem för veckovisa, månatliga, specifika dagar i veckan eller dagar i månaden, eller en viss dag i månaden. En runbook kan länkas till flera scheman och ett schema kan vara kopplat till flera runbooks.

> [!NOTE]
> Azure Automation stöder sommartid och schemalägger det på lämpligt sätt för automatiseringsåtgärder.

> [!NOTE]
> Scheman är för närvarande inte aktiverade för Azure Automation DSC-konfigurationer.

## <a name="powershell-cmdlets-used-to-access-schedules"></a>PowerShell-cmdlets som används för att komma åt scheman

Cmdletarna i följande tabell skapar och hanterar Automation-scheman med PowerShell. De levereras som en del av [Az-modulerna](modules.md#az-modules).

| Cmdletar | Description |
|:--- |:--- |
| [Get-AzAutomationSchedule](/powershell/module/Az.Automation/Get-AzAutomationSchedule) |Hämtar ett schema. |
| [Get-AzAutomationScheduledRunbook](/powershell/module/az.automation/get-azautomationscheduledrunbook) |Hämtar schemalagda runbooks. |
| [New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule) |Skapar ett nytt schema. |
| [Register-AzAutomationScheduledRunbook](/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook) |Associerar en runbook med ett schema. |
| [Remove-AzAutomationSchedule](/powershell/module/Az.Automation/Remove-AzAutomationSchedule) |Tar bort ett schema. |
| [Set-AzAutomationSchedule](/powershell/module/Az.Automation/Set-AzAutomationSchedule) |Anger egenskaperna för ett befintligt schema. |
| [Unregister-AzAutomationScheduledRunbook](/powershell/module/Az.Automation/Unregister-AzAutomationScheduledRunbook) |Tar bort en runbook från ett schema. |

## <a name="create-a-schedule"></a>Skapa ett schema

Du kan skapa ett nytt schema för dina runbooks från Azure Portal, med PowerShell eller med hjälp av en arm Azure Resource Manager mall (Azure Resource Manager). För att undvika att påverka dina runbooks och de processer som de automatiserar bör du först testa alla runbooks som har länkade scheman med ett Automation-konto som är dedikerat för testning. Ett test verifierar att dina schemalagda runbooks fortsätter att fungera korrekt. Om du ser ett problem kan du felsöka och tillämpa de ändringar som krävs innan du migrerar den uppdaterade runbook-versionen till produktion.

> [!NOTE]
> Automation-kontot får inte automatiskt några nya versioner av moduler om du inte har uppdaterat dem manuellt genom att välja alternativet Uppdatera [Azure-moduler](../automation-update-azure-modules.md) från **Moduler.** Azure Automation använder de senaste modulerna i ditt Automation-konto när ett nytt schemalagt jobb körs. 

### <a name="create-a-new-schedule-in-the-azure-portal"></a>Skapa ett nytt schema i Azure Portal

1. Välj Scheman under Delade resurser i det vänstra fönstret **på** ditt **Automation-konto.**
2. På sidan **Scheman** väljer du Lägg **till ett schema.**
3. På sidan **Nytt schema** anger du ett namn och kan även ange en beskrivning för det nya schemat.

    >[!NOTE]
    >Automation-scheman stöder för närvarande inte användning av specialtecken i schemanamnet.
    >

4. Välj om schemat körs en gång eller enligt ett återkommande schema genom att välja **En gång** eller **Återkommande.** Om du väljer **En gång** anger du en starttid och väljer **sedan Skapa.** Om du väljer **Återkommande** anger du en starttid. För **Upprepa varje väljer** du hur ofta du vill att runbooken ska upprepas. Välj efter timme, dag, vecka eller månad.

    * Om du **väljer** Vecka visas de veckodagar som du kan välja mellan. Välj så många dagar som du vill. Den första körningen av schemat sker den första dagen som väljs efter starttiden. Om du till exempel vill välja ett helgschema väljer du Lördag och Söndag.

    ![Ställa in återkommande schema för helger](../media/schedules/week-end-weekly-recurrence.png)

    * Om du **väljer** Månad får du olika alternativ. För alternativet **Månatliga förekomster** väljer du antingen **Dagar i månaden** eller Dagar i **veckan.** Om du väljer **Dagar i** månaden visas en kalender så att du kan välja så många dagar som du vill. Om du väljer ett datum, till exempel den 31:a som inte inträffar under den aktuella månaden, körs inte schemat. Om du vill att schemat ska köras den sista dagen väljer **du Ja** under Kör den sista dagen **i månaden.** Om du **väljer Vecka** dagar visas alternativet **Recur every** (Upprepas varje). Välj **Första,** **Andra,** **Tredje,** **Fjärde** eller **Sista**. Välj slutligen en dag att upprepa.

    ![Månadsschema första, 15:e och sista dagen i månaden](../media/schedules/monthly-first-fifteenth-last.png)

5. När du är klar väljer du **Skapa**.

### <a name="create-a-new-schedule-with-powershell"></a>Skapa ett nytt schema med PowerShell

Använd [cmdleten New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule) för att skapa scheman. Du anger starttiden för schemat och hur ofta det ska köras. I följande exempel visas hur du skapar många olika schemascenarier.

>[!NOTE]
>Automation-scheman stöder för närvarande inte användning av specialtecken i schemanamnet.
>

#### <a name="create-a-one-time-schedule"></a>Skapa ett schema för en gång

I följande exempel skapas ett schema för en gång.

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>Skapa ett återkommande schema

I följande exempel visas hur du skapar ett återkommande schema som körs varje dag kl. 13:00 under ett år.

```azurepowershell-interactive
$StartTime = Get-Date "13:00:00"
$EndTime = $StartTime.AddYears(1)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule02" -StartTime $StartTime -ExpiryTime $EndTime -DayInterval 1 -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule"></a>Skapa ett veckovis återkommande schema

I följande exempel visas hur du skapar ett veckoschema som endast körs på vardagar.

```azurepowershell-interactive
$StartTime = (Get-Date "13:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekDays = @([System.DayOfWeek]::Monday..[System.DayOfWeek]::Friday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule03" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule-for-weekends"></a>Skapa ett veckovis återkommande schema för helger

I följande exempel visas hur du skapar ett veckoschema som endast körs på helger.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-the-first-fifteenth-and-last-days-of-the-month"></a>Skapa ett återkommande schema för den första, den 15:e och den sista dagen i månaden

I följande exempel visas hur du skapar ett återkommande schema som körs den första, 15:e och sista dagen i en månad.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="create-a-schedule-with-a-resource-manager-template"></a>Skapa ett schema med en Resource Manager mall

I det här exemplet använder vi en Automation Resource Manager(ARM)-mall som skapar ett nytt jobbschema. Allmän information om den här mallen för att hantera Automation-jobbscheman finns i Mallreferens för [Microsoft.Automation AutomationAccounts/jobSchedules.](/azure/templates/microsoft.automation/2015-10-31/automationaccounts/jobschedules#quickstart-templates)

Kopiera mallfilen till en textredigerare:

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

Redigera följande parametervärden och spara mallen som en JSON-fil:

* Jobbschemaobjektnamn: Ett GUID (globalt unik identifierare) används som namn på jobbschemaobjektet.

   >[!IMPORTANT]
   > Guid måste vara unikt för varje jobbschema som distribueras med en ARM-mall. Även om du schemalägger om ett befintligt schema måste du ändra GUID. Detta gäller även om du tidigare har tagit bort ett befintligt jobbschema som har skapats med samma mall. Om du återanvänder samma GUID resulterar det i en misslyckad distribution.</br></br>
   > Det finns tjänster online som kan generera ett nytt GUID åt dig, till exempel den här [kostnadsfria GUID-generatorn online.](https://guidgenerator.com/)

* Schemanamn: Representerar namnet på automationsjobbschemat som ska länkas till den angivna runbooken.
* Runbook-namn: Representerar namnet på automations-runbooken som jobbschemat ska associeras med.

När filen har sparats kan du skapa runbook-jobbschemat med följande PowerShell-kommando. Kommandot använder `TemplateFile` parametern för att ange mallens sökväg och filnamn.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "<path>\RunbookJobSchedule.json"
```

## <a name="link-a-schedule-to-a-runbook"></a>Länka ett schema till en runbook

En runbook kan länkas till flera scheman och ett schema kan vara kopplat till flera runbooks. Om en runbook har parametrar kan du ange värden för dem. Du måste ange värden för alla obligatoriska parametrar och du kan även ange värden för valfria parametrar. Dessa värden används varje gång runbooken startas enligt det här schemat. Du kan koppla samma runbook till ett annat schema och ange olika parametervärden.

### <a name="link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Länka ett schema till en runbook med Azure Portal

1. I Azure Portal automation-kontot väljer du **Runbooks** under **Processautomatisering.**
1. Välj namnet på den runbook som ska schemaläggas.
1. Om runbooken för närvarande inte är länkad till ett schema kan du välja att skapa ett nytt schema eller länka till ett befintligt schema.
1. Om runbooken har parametrar kan du välja alternativet **Ändra körningsinställningar (Standard:Azure)** så **visas** fönstret Parametrar. Du kan ange parameterinformation här.

### <a name="link-a-schedule-to-a-runbook-with-powershell"></a>Länka ett schema till en runbook med PowerShell

Använd [cmdleten Register-AzAutomationScheduledRunbook](/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook) för att länka ett schema. Du kan ange värden för runbookens parametrar med parametern Parametrar . Mer information om hur du anger parametervärden finns i [Starting a Runbook in Azure Automation](../start-runbooks.md).
I följande exempel visas hur du länkar ett schema till en runbook med hjälp Azure Resource Manager en cmdlet med parametrar.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzAutomationScheduledRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ScheduleName $scheduleName -Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="schedule-runbooks-to-run-more-frequently"></a>Schemalägga att runbooks körs oftare

Det vanligaste intervallet som ett schema i Azure Automation kan konfigureras är en timme. Om du behöver att scheman ska köras oftare än så finns det två alternativ:

* Skapa en [webhook](../automation-webhooks.md) för runbooken och använd Azure Logic Apps [för](../../logic-apps/logic-apps-overview.md) att anropa webhooken. Azure Logic Apps ger mer detaljerad kornighet för att definiera ett schema.

* Skapa fyra scheman som alla startar inom 15 minuter från varandra och körs en gång i timmen. I det här scenariot kan runbooken köras var 15:e minut med olika scheman.

## <a name="disable-a-schedule"></a>Inaktivera ett schema

När du inaktiverar ett schema körs inte längre alla runbooks som är länkade till det enligt det schemat. Du kan inaktivera ett schema manuellt eller ange en förfallotid för scheman med en frekvens när du skapar dem. När förfallotiden nås inaktiveras schemat.

### <a name="disable-a-schedule-from-the-azure-portal"></a>Inaktivera ett schema från Azure Portal

1. Välj Scheman under Delade resurser i det vänstra fönstret **i** **ditt Automation-konto.**
1. Välj namnet på ett schema för att öppna informationsfönstret.
1. Ändra **Aktiverad** till **Nej.**

> [!NOTE]
> Om du vill inaktivera ett schema som har en tidigare starttid måste du ändra startdatumet till en tid i framtiden innan du sparar det.

### <a name="disable-a-schedule-with-powershell"></a>Inaktivera ett schema med PowerShell

Använd [cmdleten Set-AzAutomationSchedule](/powershell/module/Az.Automation/Set-AzAutomationSchedule) för att ändra egenskaperna för ett befintligt schema. Om du vill inaktivera schemat anger du False för `IsEnabled` parametern .

I följande exempel visas hur du inaktiverar ett schema för en runbook med hjälp av Azure Resource Manager cmdlet.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzAutomationSchedule -AutomationAccountName $automationAccountName `
-Name $scheduleName -IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="remove-a-schedule"></a>Ta bort ett schema

När du är redo att ta bort dina scheman kan du antingen använda Azure Portal eller PowerShell. Kom ihåg att du bara kan ta bort ett schema som har inaktiverats enligt beskrivningen i föregående avsnitt.

### <a name="remove-a-schedule-using-the-azure-portal"></a>Ta bort ett schema med hjälp av Azure Portal

1. I ditt Automation-konto väljer du Scheman under **Delade resurser** i det **vänstra fönstret.**
2. Välj namnet på ett schema för att öppna informationsfönstret.
3. Klicka på **Ta bort**.

### <a name="remove-a-schedule-with-powershell"></a>Ta bort ett schema med PowerShell

Du kan använda `Remove-AzAutomationSchedule` cmdleten som visas nedan för att ta bort ett befintligt schema.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Remove-AzAutomationSchedule -AutomationAccountName $automationAccountName `
-Name $scheduleName -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>Nästa steg

* Mer information om de cmdlets som används för att komma åt scheman finns [i Hantera moduler i Azure Automation](modules.md).
* Allmän information om runbooks finns i [Runbook-körning i Azure Automation](../automation-runbook-execution.md).
