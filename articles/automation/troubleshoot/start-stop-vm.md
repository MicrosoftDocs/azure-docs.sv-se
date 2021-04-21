---
title: Felsöka Azure Automation Starta/stoppa virtuella datorer när de inte används problem
description: Den här artikeln beskriver hur du felsöker och löser problem som uppstår under användningen Starta/stoppa virtuella datorer när de inte används funktionen.
services: automation
ms.subservice: process-automation
ms.date: 04/04/2019
ms.topic: troubleshooting
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b03481b41e879acac4c3b193d72594454f650525
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831194"
---
# <a name="troubleshoot-startstop-vms-during-off-hours-issues"></a>Felsöka Starta/stoppa virtuella datorer när de inte används problem

Den här artikeln innehåller information om felsökning och lösning av problem som uppstår när du distribuerar Azure Automation Starta/stoppa virtuella datorer när de inte används på dina virtuella datorer. 

## <a name="scenario-startstop-vms-during-off-hours-fails-to-properly-deploy"></a><a name="deployment-failure"></a>Scenario: Starta/stoppa virtuella datorer när de inte används inte distribueras korrekt

### <a name="issue"></a>Problem

När du [Starta/stoppa virtuella datorer när de inte används](../automation-solution-vm-management.md)får du något av följande fel:

```error
Account already exists in another resourcegroup in a subscription. ResourceGroupName: [MyResourceGroup].
```

```error
Resource 'StartStop_VM_Notification' was disallowed by policy. Policy identifiers: '[{\\\"policyAssignment\\\":{\\\"name\\\":\\\"[MyPolicyName]".
```

```error
The subscription is not registered to use namespace 'Microsoft.OperationsManagement'.
```

```error
The subscription is not registered to use namespace 'Microsoft.Insights'.
```

```error
The scope '/subscriptions/000000000000-0000-0000-0000-00000000/resourcegroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>/views/StartStopVMView' cannot perform write operation because following scope(s) are locked: '/subscriptions/000000000000-0000-0000-0000-00000000/resourceGroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>/views/StartStopVMView'. Please remove the lock and try again
```

```error
A parameter cannot be found that matches parameter name 'TagName'
```

```error
Start-AzureRmVm : Run Login-AzureRmAccount to login
```

### <a name="cause"></a>Orsak

Distributioner kan misslyckas på grund av någon av följande orsaker:

- Det finns redan ett Automation-konto med samma namn i den valda regionen.
- En princip för att inte tillåta distribution av Starta/stoppa virtuella datorer när de inte används.
- Resurstypen `Microsoft.OperationsManagement` , eller är inte `Microsoft.Insights` `Microsoft.Automation` registrerad.
- Log Analytics-arbetsytan är låst.
- Du har en inaktuell version av AzureRM-modulerna eller Starta/stoppa virtuella datorer när de inte används funktion.

### <a name="resolution"></a>Lösning

Granska följande korrigeringar för möjliga lösningar:

* Automation-konton måste vara unika i en Azure-region, även om de finns i olika resursgrupper. Kontrollera dina befintliga Automation-konton i målregionen.
* En befintlig princip förhindrar att en resurs som krävs för Starta/stoppa virtuella datorer när de inte används distribueras. Gå till dina principtilldelningar i Azure Portal och kontrollera om du har en principtilldelning som inte ger någon distribution av den här resursen. Mer information finns i [RequestDisallowedByPolicy-felet](../../azure-resource-manager/templates/error-policy-requestdisallowedbypolicy.md).
* Om du Starta/stoppa virtuella datorer när de inte används måste din prenumeration vara registrerad i följande Azure-resursnamnrymder:

    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   Mer information om fel när du registrerar providers finns i [Lösa fel för registrering av resursprovider.](../../azure-resource-manager/templates/error-register-resource-provider.md)
* Om du har ett lås på Log Analytics-arbetsytan går du till arbetsytan i Azure Portal och tar bort eventuella lås på resursen.
* Om dessa lösningar inte löser problemet följer du anvisningarna under [Uppdatera funktionen för att](../automation-solution-vm-management.md#update-the-feature) distribuera om Starta/stoppa virtuella datorer när de inte används.

## <a name="scenario-all-vms-fail-to-start-or-stop"></a><a name="all-vms-fail-to-startstop"></a>Scenario: Alla virtuella datorer kan inte starta eller stoppas

### <a name="issue"></a>Problem

Du har konfigurerat Starta/stoppa virtuella datorer när de inte används, men den startar eller stoppar inte alla virtuella datorer.

### <a name="cause"></a>Orsak

Det här felet kan orsakas av någon av följande orsaker:

- Ett schema är inte korrekt konfigurerat.
- Kör som-kontot kanske inte är korrekt konfigurerat.
- En runbook kan ha körts på fel.
- De virtuella datorerna kan ha undantagits.

### <a name="resolution"></a>Lösning

Granska följande lista för potentiella lösningar:

* Kontrollera att du har konfigurerat ett schema för Starta/stoppa virtuella datorer när de inte används. Information om hur du konfigurerar ett schema finns [i Scheman.](../shared-resources/schedules.md)

* Kontrollera [jobbströmmarna för](../automation-runbook-execution.md#job-statuses) att leta efter eventuella fel. Leta efter jobb från någon av följande runbooks:

  * **AutoStop_CreateAlert_Child**
  * **AutoStop_CreateAlert_Parent**
  * **AutoStop_Disable**
  * **AutoStop_VM_Child**
  * **ScheduledStartStop_Base_Classic**
  * **ScheduledStartStop_Child_Classic**
  * **ScheduledStartStop_Child**
  * **ScheduledStartStop_Parent**
  * **SequencedStartStop_Parent**

* Kontrollera att Kör [som-kontot](../automation-security-overview.md#run-as-accounts) har rätt behörigheter till de virtuella datorer som du försöker starta eller stoppa. Information om hur du kontrollerar behörigheter för en resurs finns i [Snabbstart: Visa](../../role-based-access-control/check-access.md)roller som tilldelats till en användare med hjälp av Azure Portal . Du måste ange program-ID:t för tjänstens huvudnamn som används av Kör som-kontot. Du kan hämta det här värdet genom att gå till ditt Automation-konto i Azure Portal. Välj **Kör som-konton** **under Kontoinställningar** och välj lämpligt Kör som-konto.

* Virtuella datorer kanske inte startas eller stoppas om de uttryckligen undantas. Exkluderade virtuella datorer anges i `External_ExcludeVMNames` variabeln i Automation-kontot som funktionen distribueras till. I följande exempel visas hur du kan köra frågor mot det värdet med PowerShell.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="scenario-some-of-my-vms-fail-to-start-or-stop"></a><a name="some-vms-fail-to-startstop"></a>Scenario: Vissa av mina virtuella datorer kan inte starta eller stoppas

### <a name="issue"></a>Problem

Du har konfigurerat Starta/stoppa virtuella datorer när de inte används, men den startar eller stoppar inte några av de konfigurerade virtuella datorerna.

### <a name="cause"></a>Orsak

Det här felet kan orsakas av någon av följande orsaker:

- I sekvensscenariot kan en tagg saknas eller vara felaktig.
- Den virtuella datorn kan uteslutas.
- Kör som-kontot kanske inte har tillräcklig behörighet på den virtuella datorn.
- Den virtuella datorn kan ha ett problem som hindrade den från att starta eller stoppa.

### <a name="resolution"></a>Lösning

Granska följande lista för potentiella lösningar:

* När du använder [sekvensscenariot](../automation-solution-vm-management.md) för Starta/stoppa virtuella datorer när de inte används måste du se till att varje virtuell dator som du vill starta eller stoppa har rätt tagg. Kontrollera att de virtuella datorer som du vill starta har `sequencestart` taggen och de virtuella datorer som du vill stoppa har `sequencestop` taggen . Båda taggarna kräver ett positivt heltalsvärde. Du kan använda en fråga som liknar följande exempel för att leta efter alla virtuella datorer med taggarna och deras värden.

  ```powershell-interactive
  Get-AzResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* Virtuella datorer kanske inte startas eller stoppas om de uttryckligen undantas. Exkluderade virtuella datorer anges i `External_ExcludeVMNames` variabeln i Automation-kontot som funktionen distribueras till. I följande exempel visas hur du kan köra frågor mot det värdet med PowerShell.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* Om du vill starta och stoppa virtuella datorer måste Kör som-kontot för Automation-kontot ha rätt behörigheter till den virtuella datorn. Information om hur du kontrollerar behörigheter för en resurs finns i [Snabbstart: Visa](../../role-based-access-control/check-access.md)roller som tilldelats till en användare med hjälp av Azure Portal . Du måste ange program-ID:t för tjänstens huvudnamn som används av Kör som-kontot. Du kan hämta det här värdet genom att gå till ditt Automation-konto i Azure Portal. Välj **Kör som-konton** **under Kontoinställningar** och välj lämpligt Kör som-konto.
* Om den virtuella datorn har problem med att starta eller avallokera kan det finnas ett problem på själva den virtuella datorn. Exempel är en uppdatering som tillämpas när den virtuella datorn försöker stängas av, en tjänst som låser sig och mycket mer. Gå till vm-resursen och kontrollera **aktivitetsloggarna** för att se om det finns några fel i loggarna. Du kan också försöka logga in på den virtuella datorn för att se om det finns några fel i händelseloggarna. Mer information om hur du felsöker den virtuella datorn finns [i Felsöka virtuella Azure-datorer.](/troubleshoot/azure/virtual-machines/welcome-virtual-machines)
* Kontrollera [jobbströmmarna för](../automation-runbook-execution.md#job-statuses) att leta efter eventuella fel. I portalen går du till ditt Automation-konto och väljer **Jobb** under **Processautomatisering.**

## <a name="scenario-my-custom-runbook-fails-to-start-or-stop-my-vms"></a><a name="custom-runbook"></a>Scenario: Min anpassade runbook kan inte starta eller stoppa mina virtuella datorer

### <a name="issue"></a>Problem

Du har skapat en anpassad runbook eller laddat ned en från PowerShell-galleriet och den fungerar inte korrekt.

### <a name="cause"></a>Orsak

Det kan finnas många orsaker till felet. Gå till ditt Automation-konto i Azure Portal och välj **Jobb** under **Processautomatisering.** På sidan **Jobb** letar du efter jobb från din runbook för att visa eventuella jobbfel.

### <a name="resolution"></a>Lösning

Vi rekommenderar att du gör följande:

* Använd [Starta/stoppa virtuella datorer när de inte används för](../automation-solution-vm-management.md) att starta och stoppa virtuella datorer i Azure Automation. 
* Tänk på att Microsoft inte stöder anpassade runbooks. Du kan hitta en lösning för din anpassade runbook i [Felsöka runbook-problem.](runbooks.md) Kontrollera [jobbströmmarna och](../automation-runbook-execution.md#job-statuses) leta efter fel. 

## <a name="scenario-vms-dont-start-or-stop-in-the-correct-sequence"></a><a name="dont-start-stop-in-sequence"></a>Scenario: Virtuella datorer startar eller stoppar inte i rätt ordning

### <a name="issue"></a>Problem

De virtuella datorer som du har aktiverat för funktionen startar eller stoppar inte i rätt ordning.

### <a name="cause"></a>Orsak

Det här problemet orsakas av felaktig taggning på de virtuella datorerna.

### <a name="resolution"></a>Lösning

Följ dessa steg för att säkerställa att funktionen är korrekt aktiverad:

1. Se till att alla virtuella datorer som ska startas eller stoppas har `sequencestart` en - `sequencestop` eller -tagg, beroende på din situation. De här taggarna behöver ett positivt heltal som värde. Virtuella datorer bearbetas i stigande ordning baserat på det här värdet.
1. Kontrollera att resursgrupperna för de virtuella datorerna som ska startas eller stoppas finns i variablerna eller `External_Start_ResourceGroupNames` , beroende på din `External_Stop_ResourceGroupNames` situation.
1. Testa ändringarna genom att köra SequencedStartStop_Parent **runbook** med `WHATIF` parametern inställd på Sant för att förhandsgranska ändringarna.

## <a name="scenario-startstop-vms-during-off-hours-job-fails-with-403-forbidden-error"></a><a name="403"></a>Scenario: Starta/stoppa virtuella datorer när de inte används jobb misslyckas med felet 403 – förbjuden

### <a name="issue"></a>Problem

Du hittar jobb som misslyckades med `403 forbidden` ett fel för Starta/stoppa virtuella datorer när de inte används runbooks.

### <a name="cause"></a>Orsak

Det här problemet kan orsakas av ett felaktigt konfigurerat eller utgånget Kör som-konto. Det kan också vara på grund av otillräcklig behörighet till VM-resurserna av Kör som-kontot.

### <a name="resolution"></a>Lösning

Kontrollera att Kör som-kontot är korrekt konfigurerat genom att gå till ditt Automation-konto i Azure Portal och välja **Kör som-konton** under **Kontoinställningar**. Om ett Kör som-konto är felaktigt konfigurerat eller har upphört att gälla visas villkoret i statusen.

Om Ditt Kör som-konto är felkonfigurerat tar du bort och skapar om ditt Kör som-konto. Mer information finns i Azure Automation [Kör som-konton](../automation-security-overview.md#run-as-accounts).

Om certifikatet har upphört att gälla för ditt Kör som-konto följer du stegen i [Själv signerad certifikatförnyelse för](../manage-runas-account.md#cert-renewal) att förnya certifikatet.

Om det saknas behörigheter kan du läsa [Snabbstart: Visa roller som tilldelats till en användare med hjälp av Azure Portal](../../role-based-access-control/check-access.md). Du måste ange program-ID:t för tjänstens huvudnamn som används av Kör som-kontot. Du kan hämta det här värdet genom att gå till ditt Automation-konto i Azure Portal. Välj **Kör som-konton** **under Kontoinställningar** och välj lämpligt Kör som-konto.

## <a name="scenario-my-problem-isnt-listed-here"></a><a name="other"></a>Scenario: Mitt problem visas inte här

### <a name="issue"></a>Problem

Du får ett problem eller oväntat resultat när du använder Starta/stoppa virtuella datorer när de inte används som inte visas på den här sidan.

### <a name="cause"></a>Orsak

Många gånger kan fel orsakas av att en gammal och inaktuell version av funktionen används.

> [!NOTE]
> Funktionen Starta/stoppa virtuella datorer när de inte används har testats med de Azure-moduler som importeras till ditt Automation-konto när du distribuerar funktionen på virtuella datorer. Funktionen fungerar för närvarande inte med nyare versioner av Azure-modulen. Den här begränsningen påverkar bara det Automation-konto som du använder för att köra Starta/stoppa virtuella datorer när de inte används. Du kan fortfarande använda nyare versioner av Azure-modulen i dina andra Automation-konton, enligt beskrivningen i [Uppdatera Azure PowerShell moduler](../automation-update-azure-modules.md).

### <a name="resolution"></a>Lösning

Lös många fel genom att ta bort och [uppdatera Starta/stoppa virtuella datorer när de inte används](../automation-solution-vm-management.md#update-the-feature). Du kan också kontrollera [jobbströmmarna för](../automation-runbook-execution.md#job-statuses) att leta efter eventuella fel. 

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet här eller om du inte kan lösa problemet kan du prova någon av följande kanaler för ytterligare support:

* Få svar från Azure-experter via [Azure-forumen.](https://azure.microsoft.com/support/forums/)
* Anslut med [@AzureSupport](https://twitter.com/azuresupport) , det officiella Microsoft Azure konto för att förbättra kundupplevelsen. Azure Support ansluter Azure-communityn till svar, support och experter.
* Skapa en Azure-supportincident. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/)och välj **Få support.**
