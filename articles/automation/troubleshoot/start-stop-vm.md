---
title: Felsöka Azure Automation Starta/stoppa virtuella datorer när de inte används problem
description: Den här artikeln beskriver hur du felsöker och löser problem som uppstår när du använder funktionen Starta/stoppa virtuella datorer när de inte används.
services: automation
ms.subservice: process-automation
ms.date: 04/04/2019
ms.topic: troubleshooting
ms.openlocfilehash: f6d2696a68643f87de0fcaf2e723da9365d9953e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104953858"
---
# <a name="troubleshoot-startstop-vms-during-off-hours-issues"></a>Felsöka Starta/stoppa virtuella datorer när de inte används problem

Den här artikeln innehåller information om hur du felsöker och löser problem som uppstår när du distribuerar Azure Automation Starta/stoppa virtuella datorer när de inte används-funktionen på dina virtuella datorer. 

## <a name="scenario-startstop-vms-during-off-hours-fails-to-properly-deploy"></a><a name="deployment-failure"></a>Scenario: Starta/stoppa virtuella datorer när de inte används inte distribueras korrekt

### <a name="issue"></a>Problem

När du distribuerar [Starta/stoppa virtuella datorer när de inte används](../automation-solution-vm-management.md)får du ett av följande fel:

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

Distributioner kan inte utföras på grund av en av följande orsaker:

- Det finns redan ett Automation-konto med samma namn i den valda regionen.
- En princip tillåter inte distribution av Starta/stoppa virtuella datorer när de inte används.
- `Microsoft.OperationsManagement` `Microsoft.Insights` Resurs typen, eller `Microsoft.Automation` är inte registrerad.
- Din Log Analytics-arbetsyta är låst.
- Du har en inaktuell version av AzureRM-modulerna eller den Starta/stoppa virtuella datorer när de inte används funktionen.

### <a name="resolution"></a>Lösning

Läs följande korrigeringar för möjliga lösningar:

* Automation-konton måste vara unika inom en Azure-region, även om de är i olika resurs grupper. Kontrol lera dina befintliga Automation-konton i mål regionen.
* En befintlig princip förhindrar en resurs som krävs för att Starta/stoppa virtuella datorer när de inte används ska kunna distribueras. Gå till princip tilldelningarna i Azure Portal och kontrol lera om du har en princip tilldelning som inte tillåter distributionen av den här resursen. Mer information finns i [RequestDisallowedByPolicy-fel](../../azure-resource-manager/templates/error-policy-requestdisallowedbypolicy.md).
* För att distribuera Starta/stoppa virtuella datorer när de inte används måste prenumerationen registreras till följande Azure-resurs namnrum:

    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   Läs mer om fel när du registrerar leverantörer i [lösa fel för registrering av resurs leverantör](../../azure-resource-manager/templates/error-register-resource-provider.md).
* Om du har ett lås på Log Analytics arbets ytan går du till din arbets yta i Azure Portal och tar bort eventuella lås på resursen.
* Om de här lösningarna inte löser problemet följer du anvisningarna under [uppdatera funktionen](../automation-solution-vm-management.md#update-the-feature) för att distribuera om starta/stoppa virtuella datorer när de inte används.

## <a name="scenario-all-vms-fail-to-start-or-stop"></a><a name="all-vms-fail-to-startstop"></a>Scenario: alla virtuella datorer kan inte startas eller stoppas

### <a name="issue"></a>Problem

Du har konfigurerat Starta/stoppa virtuella datorer när de inte används, men det startar eller stoppar inte alla virtuella datorer.

### <a name="cause"></a>Orsak

Felet kan bero på någon av följande orsaker:

- Ett schema har inte kon figurer ATS korrekt.
- Kör som-kontot kanske inte har kon figurer ATS korrekt.
- En Runbook kan ha stött på fel.
- De virtuella datorerna kan ha uteslutits.

### <a name="resolution"></a>Lösning

Läs följande lista för möjliga lösningar:

* Kontrol lera att du har konfigurerat ett schema korrekt för Starta/stoppa virtuella datorer när de inte används. Information om hur du konfigurerar ett schema finns i [scheman](../shared-resources/schedules.md).

* Kontrol lera [jobb strömmarna](../automation-runbook-execution.md#job-statuses) för att leta efter eventuella fel. Sök efter jobb från någon av följande Runbooks:

  * **AutoStop_CreateAlert_Child**
  * **AutoStop_CreateAlert_Parent**
  * **AutoStop_Disable**
  * **AutoStop_VM_Child**
  * **ScheduledStartStop_Base_Classic**
  * **ScheduledStartStop_Child_Classic**
  * **ScheduledStartStop_Child**
  * **ScheduledStartStop_Parent**
  * **SequencedStartStop_Parent**

* Kontrol lera att ditt [Kör som-konto](../automation-security-overview.md#run-as-accounts) har rätt behörigheter till de virtuella datorer som du försöker starta eller stoppa. Information om hur du kontrollerar behörigheter för en resurs finns i [snabb start: Visa roller tilldelade till en användare med hjälp av Azure Portal](../../role-based-access-control/check-access.md). Du måste ange program-ID för tjänstens huvud namn som används av kör som-kontot. Du kan hämta det här värdet genom att gå till ditt Automation-konto i Azure Portal. Välj **Kör som-konton** under **konto inställningar** och välj lämpligt kör som-konto.

* Det är inte säkert att de virtuella datorerna startas eller stoppas om de uttryckligen utesluts. Undantagna virtuella datorer anges i `External_ExcludeVMNames` variabeln i Automation-kontot som funktionen har distribuerats till. I följande exempel visas hur du kan fråga det värdet med PowerShell.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="scenario-some-of-my-vms-fail-to-start-or-stop"></a><a name="some-vms-fail-to-startstop"></a>Scenario: vissa av mina virtuella datorer kan inte startas eller stoppas

### <a name="issue"></a>Problem

Du har konfigurerat Starta/stoppa virtuella datorer när de inte används, men det startar eller stoppar inte några av de virtuella datorerna som kon figurer ATS.

### <a name="cause"></a>Orsak

Felet kan bero på någon av följande orsaker:

- I sekvens-scenariot kan en tagg saknas eller vara felaktig.
- Den virtuella datorn kan vara Exkluderad.
- Kör som-kontot kanske inte har tillräcklig behörighet på den virtuella datorn.
- Den virtuella datorn kan ha ett problem som hindrade den från att starta eller stoppa.

### <a name="resolution"></a>Lösning

Läs följande lista för möjliga lösningar:

* När du använder [sekvens scenariot](../automation-solution-vm-management.md) för starta/stoppa virtuella datorer när de inte används måste du se till att varje virtuell dator som du vill starta eller stoppa har rätt tagg. Se till att de virtuella datorer som du vill starta har `sequencestart` taggen och att de virtuella datorer som du vill stoppa har `sequencestop` taggen. Båda taggarna kräver ett positivt heltals värde. Du kan använda en fråga som liknar följande exempel för att söka efter alla virtuella datorer med taggarna och deras värden.

  ```powershell-interactive
  Get-AzResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* Det är inte säkert att de virtuella datorerna startas eller stoppas om de uttryckligen utesluts. Undantagna virtuella datorer anges i `External_ExcludeVMNames` variabeln i Automation-kontot som funktionen har distribuerats till. I följande exempel visas hur du kan fråga det värdet med PowerShell.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* För att starta och stoppa virtuella datorer måste kör som-kontot för Automation-kontot ha nödvändig behörighet för den virtuella datorn. Information om hur du kontrollerar behörigheter för en resurs finns i [snabb start: Visa roller tilldelade till en användare med hjälp av Azure Portal](../../role-based-access-control/check-access.md). Du måste ange program-ID för tjänstens huvud namn som används av kör som-kontot. Du kan hämta det här värdet genom att gå till ditt Automation-konto i Azure Portal. Välj **Kör som-konton** under **konto inställningar** och välj lämpligt kör som-konto.
* Om den virtuella datorn har problem med att starta eller att allokera kan det finnas ett problem på den virtuella datorn. Exempel är en uppdatering som tillämpas när den virtuella datorn försöker stängas av, en tjänst som låser sig och mer. Gå till den virtuella dator resursen och kontrol lera **aktivitets loggarna** för att se om det finns några fel i loggarna. Du kan också försöka logga in på den virtuella datorn för att se om det finns några fel i händelse loggarna. Mer information om hur du felsöker din virtuella dator finns i [Felsöka Azure Virtual Machines](/troubleshoot/azure/virtual-machines/welcome-virtual-machines).
* Kontrol lera [jobb strömmarna](../automation-runbook-execution.md#job-statuses) för att leta efter eventuella fel. I portalen går du till ditt Automation-konto och väljer **jobb** under **process automatisering**.

## <a name="scenario-my-custom-runbook-fails-to-start-or-stop-my-vms"></a><a name="custom-runbook"></a>Scenario: min anpassade Runbook kan inte starta eller stoppa mina virtuella datorer

### <a name="issue"></a>Problem

Du har skapat en anpassad Runbook eller hämtat en från PowerShell-galleriet och fungerar inte som den ska.

### <a name="cause"></a>Orsak

Det kan finnas många orsaker till det här problemet. Gå till ditt Automation-konto i Azure Portal och välj **jobb** under **process automatisering**. På sidan **jobb** letar du efter jobb från din Runbook för att visa eventuella jobb haverier.

### <a name="resolution"></a>Lösning

Vi rekommenderar att du gör följande:

* Använd [Starta/stoppa virtuella datorer när de inte används](../automation-solution-vm-management.md) för att starta och stoppa virtuella datorer i Azure Automation. 
* Tänk på att Microsoft inte stöder anpassade Runbooks. Du kan hitta en lösning för din anpassade Runbook i [Felsöka Runbook-problem](runbooks.md). Kontrol lera [jobb strömmarna](../automation-runbook-execution.md#job-statuses) för att leta efter eventuella fel. 

## <a name="scenario-vms-dont-start-or-stop-in-the-correct-sequence"></a><a name="dont-start-stop-in-sequence"></a>Scenario: virtuella datorer startar eller stoppas inte i rätt ordning

### <a name="issue"></a>Problem

De virtuella datorer som du har aktiverat för funktionen startar eller stoppas inte i rätt ordning.

### <a name="cause"></a>Orsak

Det här problemet orsakas av felaktig taggning på de virtuella datorerna.

### <a name="resolution"></a>Lösning

Följ dessa steg för att se till att funktionen är korrekt aktive rad:

1. Se till att alla virtuella datorer som ska startas eller stoppas har en `sequencestart` or `sequencestop` -tagg, beroende på din situation. Taggarna måste ha ett positivt heltal som värde. De virtuella datorerna bearbetas i stigande ordning baserat på det här värdet.
1. Se till att resurs grupperna för de virtuella datorer som ska startas eller stoppas finns i `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` variablerna eller, beroende på din situation.
1. Testa ändringarna genom att köra **SequencedStartStop_Parent** Runbook med `WHATIF` parametern inställt på sant för att förhandsgranska ändringarna.

## <a name="scenario-startstop-vms-during-off-hours-job-fails-with-403-forbidden-error"></a><a name="403"></a>Scenario: Starta/stoppa virtuella datorer när de inte används jobb Miss lyckas med ett otillåtet 403-fel

### <a name="issue"></a>Problem

Du hittar jobb som misslyckades med ett `403 forbidden` fel för starta/stoppa virtuella datorer när de inte används Runbooks.

### <a name="cause"></a>Orsak

Det här problemet kan orsakas av ett felaktigt konfigurerat eller utgånget kör som-konto. Det kan också bero på otillräcklig behörighet till VM-resurserna av kör som-kontot.

### <a name="resolution"></a>Lösning

Kontrol lera att kör som-kontot är korrekt konfigurerat genom att gå till ditt Automation-konto i Azure Portal och välja **Kör som-konton** under **konto inställningar**. Om ett Kör som-konto har kon figurer ATS felaktigt eller har upphört att gälla, visar status villkoret.

Om ditt kör som-konto är felkonfigurerat kan du ta bort och återskapa kör som-kontot. Mer information finns i [Azure Automation kör som-konton](../automation-security-overview.md#run-as-accounts).

Om certifikatet har gått ut för ditt kör som-konto följer du stegen i [självsignerat certifikat förnyelse](../manage-runas-account.md#cert-renewal) för att förnya certifikatet.

Om behörigheter saknas, se [snabb start: Visa roller tilldelade till en användare med hjälp av Azure Portal](../../role-based-access-control/check-access.md). Du måste ange program-ID för tjänstens huvud namn som används av kör som-kontot. Du kan hämta det här värdet genom att gå till ditt Automation-konto i Azure Portal. Välj **Kör som-konton** under **konto inställningar** och välj lämpligt kör som-konto.

## <a name="scenario-my-problem-isnt-listed-here"></a><a name="other"></a>Scenario: mitt problem visas inte här

### <a name="issue"></a>Problem

Du får ett problem eller oväntat resultat när du använder Starta/stoppa virtuella datorer när de inte används som inte visas på den här sidan.

### <a name="cause"></a>Orsak

Många gånger kan fel orsakas av att använda en gammal och inaktuell version av funktionen.

> [!NOTE]
> Starta/stoppa virtuella datorer när de inte används funktionen har testats med Azure-modulerna som importeras till ditt Automation-konto när du distribuerar funktionen på virtuella datorer. Funktionen fungerar för närvarande inte med nyare versioner av Azure-modulen. Den här begränsningen påverkar bara det Automation-konto som du använder för att köra Starta/stoppa virtuella datorer när de inte används. Du kan fortfarande använda nyare versioner av Azure-modulen i andra Automation-konton, enligt beskrivningen i [Update Azure PowerShell-moduler](../automation-update-azure-modules.md).

### <a name="resolution"></a>Lösning

Ta bort och [uppdatera starta/stoppa virtuella datorer när de inte används](../automation-solution-vm-management.md#update-the-feature)för att lösa många fel. Du kan också kontrol lera [jobb strömmar](../automation-runbook-execution.md#job-statuses) för att leta efter eventuella fel. 

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet här eller om du inte kan lösa problemet kan du prova någon av följande kanaler för ytterligare support:

* Få svar från Azure-experter via [Azure-forum](https://azure.microsoft.com/support/forums/).
* Anslut till [@AzureSupport](https://twitter.com/azuresupport) , det officiella Microsoft Azure kontot för att förbättra kund upplevelsen. Azure-support ansluter Azure-communityn till svar, support och experter.
* Filen en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/)och välj **få support**.