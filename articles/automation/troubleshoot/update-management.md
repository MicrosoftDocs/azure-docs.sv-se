---
title: Felsöka Azure Automation Uppdateringshantering problem
description: Den här artikeln beskriver hur du felsöker och löser problem med Azure Automation Uppdateringshantering.
services: automation
ms.date: 01/13/2021
ms.topic: conceptual
ms.service: automation
ms.openlocfilehash: 55e58c92004f4f4cf4ba6a96620b4f037c80cdb4
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2021
ms.locfileid: "98246272"
---
# <a name="troubleshoot-update-management-issues"></a>Felsöka problem med Uppdateringshantering

Den här artikeln beskriver problem som du kan köra när du distribuerar Uppdateringshantering-funktionen på dina datorer. Det finns en agent fel sökare för Hybrid Runbook Worker agenten för att fastställa det underliggande problemet. Mer information om fel sökning finns i [Felsöka problem med Windows Update-agenten](update-agent-issues.md) och [Felsöka problem med problem med Linux-uppdaterings agenten](update-agent-issues-linux.md). Information om andra funktions distributions problem finns i [fel sökning av problem med funktions distribution](onboarding.md).

>[!NOTE]
>Om du stöter på problem när du distribuerar Uppdateringshantering på en Windows-dator öppnar du Windows Loggboken och kontrollerar **Operations Manager** händelse loggen under **program-och tjänst loggar** på den lokala datorn. Sök efter händelser med händelse-ID 4502 och händelse information som innehåller `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent` .

## <a name="scenario-linux-updates-shown-as-pending-and-those-installed-vary"></a><a name="updates-linux-installed-different"></a>Scenario: Linux-uppdateringar som visas som väntande och som är installerade varierar

### <a name="issue"></a>Problem

För Linux-datorn visar Uppdateringshantering vissa uppdateringar som är tillgängliga under klassificerings **säkerhet** och **andra**. Men när ett uppdaterings schema körs på datorn, till exempel för att installera endast uppdateringar som matchar **säkerhets** klassificeringen, är uppdateringarna som är installerade annorlunda än eller en del av de uppdateringar som visas tidigare och som matchar klassificeringen.

### <a name="cause"></a>Orsak

När en utvärdering av OS-uppdateringar väntar på att din Linux-dator är [färdig, används](https://oval.mitre.org/) den distribution som tillhandahålls av Linux-leverantören av uppdateringshantering för klassificering. Kategorisering görs för Linux-uppdateringar som **säkerhet** eller **andra**, baserat på oval-filerna som gör att uppdateringar löser säkerhets problem eller sårbarheter. Men när uppdaterings schemat körs körs den på Linux-datorn med lämplig Package Manager som YUM, APT eller ZYPPER för att installera dem. Paket hanteraren för Linux-distribution kan ha en annan mekanism för att klassificera uppdateringar, där resultatet kan skilja sig från de som hämtas från OVAL filen genom att Uppdateringshantering.

### <a name="resolution"></a>Lösning

Du kan kontrol lera Linux-datorn manuellt, tillämpliga uppdateringar och deras klassificering per distribution paket hanterare. Du kan ta reda på vilka uppdateringar som klassificeras som **säkerhet** av din paket hanterare genom att köra följande kommandon.

För YUM returnerar följande kommando en icke-noll lista med uppdateringar kategoriserade som **säkerhet** av Red Hat. Observera att om du använder CentOS returneras alltid en tom lista och ingen säkerhets klassificering sker.

```bash
sudo yum -q --security check-update
```

För ZYPPER returnerar följande kommando en icke-noll lista över uppdateringar som är kategoriserade som **säkerhet** av SUSE.

```bash
sudo LANG=en_US.UTF8 zypper --non-interactive patch --category security --dry-run
```

För APT returnerar följande kommando en icke-noll lista över uppdateringar som är kategoriserade som **säkerhet** av kanoniskt läge för Ubuntu Linux distributioner.

```bash
sudo grep security /etc/apt/sources.list > /tmp/oms-update-security.list LANG=en_US.UTF8 sudo apt-get -s dist-upgrade -oDir::Etc::Sourcelist=/tmp/oms-update-security.list
```

I den här listan kör du kommandot `grep ^Inst` för att hämta alla väntande säkerhets uppdateringar.

## <a name="scenario-you-receive-the-error-failed-to-enable-the-update-solution"></a><a name="failed-to-enable-error"></a>Scenario: du får felet "Det gick inte att aktivera uppdaterings lösningen"

### <a name="issue"></a>Problem

När du försöker aktivera Uppdateringshantering i ditt Automation-konto får du följande fel meddelande:

```error
Error details: Failed to enable the Update solution
```

### <a name="cause"></a>Orsak

Det här felet kan inträffa av följande orsaker:

* Kraven på nätverks brand väggen för den Log Analytics agenten kanske inte är korrekt konfigurerade. Den här situationen kan orsaka att agenten inte kan köras när DNS-webbadresserna matchas.

* Uppdateringshantering mål är felkonfigurerad och datorn får inte uppdateringar som förväntat.

* Du kan också se att datorn visar statusen `Non-compliant` under **kompatibilitet**. Samtidigt rapporterar **agent Desktop Analytics** agenten som `Disconnected` .

### <a name="resolution"></a>Lösning

* Kör fel sökaren för [Windows](update-agent-issues.md#troubleshoot-offline) eller [Linux](update-agent-issues-linux.md#troubleshoot-offline), beroende på vilket operativ system du har.

* Gå till [nätverks konfiguration](../automation-hybrid-runbook-worker.md#network-planning) och lär dig om vilka adresser och portar som måste tillåtas för att uppdateringshantering ska fungera.  

* Sök efter problem med omfattnings konfigurationen. [Omfattnings konfigurationen](../update-management/scope-configuration.md) avgör vilka datorer som har kon figurer ats för uppdateringshantering. Om din dator visas i arbets ytan men inte i Uppdateringshantering måste du ange omfattnings konfigurationen som mål för datorerna. Mer information om omfattnings konfigurationen finns i [Aktivera datorer i arbets ytan](../update-management/enable-from-automation-account.md#enable-machines-in-the-workspace).

* Ta bort arbets konfigurationen genom att följa stegen i [ta bort hybrid Runbook Worker från en lokal Windows-dator](../automation-windows-hrw-install.md#remove-windows-hybrid-runbook-worker) eller [ta bort hybrid Runbook Worker från en lokal Linux-dator](../automation-linux-hrw-install.md#remove-linux-hybrid-runbook-worker).

## <a name="scenario-superseded-update-indicated-as-missing-in-update-management"></a>Scenario: ersatt uppdatering anges som saknas i Uppdateringshantering

### <a name="issue"></a>Problem

Gamla uppdateringar visas för ett Automation-konto som saknas, trots att de har ersatts. En ersatt uppdatering är en som du inte behöver installera eftersom en senare uppdatering som korrigerar samma sårbarhet är tillgänglig. Uppdateringshantering ignorerar den ersatta uppdateringen och gör den inte tillämplig till förmån för den ersatta uppdateringen. Information om ett relaterat problem finns i [Uppdatera ersätts](/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer).

### <a name="cause"></a>Orsak

Ersatta uppdateringar nekas inte i Windows Server Update Services (WSUS) så att de kan betraktas som ej tillämpliga.

### <a name="resolution"></a>Lösning

När en ersatt uppdatering blir 100 procent inte tillämplig, bör du ändra godkännande tillstånd för den uppdateringen till `Declined` i WSUS. Ändra godkännande tillstånd för alla uppdateringar:

1. I Automation-kontot väljer du **uppdateringshantering** för att Visa dator status. Se [Visa uppdaterings bedömningar](../update-management/view-update-assessments.md).

2. Kontrol lera den ersatta uppdateringen för att se till att den är 100 procent inte tillämplig.

3. På WSUS-servern som datorerna rapporterar till avvisar [du uppdateringen](/windows-server/administration/windows-server-update-services/manage/updates-operations#declining-updates).

4. Välj **datorer** och tvinga en ny sökning efter kompatibilitet i kolumnen **efterlevnad** . Se [Hantera uppdateringar för virtuella datorer](../update-management/manage-updates-for-vm.md).

5. Upprepa stegen ovan för andra ersatta uppdateringar.

6. För Windows Server Update Services (WSUS) rensar du alla ersatta uppdateringar för att uppdatera infrastrukturen med [guiden Rensa](/windows-server/administration/windows-server-update-services/manage/the-server-cleanup-wizard)WSUS-server.

7. Upprepa proceduren regelbundet för att korrigera visnings problemet och minimera mängden disk utrymme som används för uppdaterings hantering.

## <a name="scenario-machines-dont-show-up-in-the-portal-under-update-management"></a><a name="nologs"></a>Scenario: datorer visas inte i portalen under Uppdateringshantering

### <a name="issue"></a>Problem

Datorerna har följande symptom:

* Datorn visas `Not configured` från den uppdateringshantering vyn av en virtuell dator.

* Datorerna saknas i Uppdateringshanterings visning av ditt Azure Automation-konto.

* Du har datorer som visas som `Not assessed` **kompatibla**. Men du ser pulsslags data i Azure Monitor loggar för Hybrid Runbook Worker men inte för Uppdateringshantering.

### <a name="cause"></a>Orsak

Det här problemet kan orsakas av lokala konfigurations problem eller av en felaktigt konfigurerad omfattnings konfiguration. Möjliga orsaker är:

* Du kan behöva registrera om och installera om Hybrid Runbook Worker.

* Du kan ha definierat en kvot på din arbets yta som har nåtts och som förhindrar ytterligare data lagring.

### <a name="resolution"></a>Lösning

1. Kör fel sökaren för [Windows](update-agent-issues.md#troubleshoot-offline) eller [Linux](update-agent-issues-linux.md#troubleshoot-offline), beroende på vilket operativ system du har.

2. Kontrol lera att datorn rapporterar till rätt arbets yta. Anvisningar för hur du verifierar den här aspekten finns i [Verifiera agent anslutning till Azure Monitor](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-azure-monitor). Kontrol lera också att arbets ytan är länkad till ditt Azure Automation-konto. Bekräfta genom att gå till ditt Automation-konto och välja **länkad arbets yta** under **relaterade resurser**.

3. Se till att datorerna visas i Log Analytics arbets ytan som är länkad till ditt Automation-konto. Kör följande fråga i arbets ytan Log Analytics.

   ```kusto
   Heartbeat
   | summarize by Computer, Solutions
   ```

    Om du inte ser datorn i frågeresultatet har den nyligen checkats in. Det finns förmodligen ett lokalt konfigurations problem och du bör [installera om agenten](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows).

    Om din dator listas i frågeresultatet kontrollerar du under egenskapen **lösningar** som **uppdateras** . Detta verifierar att det är registrerat med Uppdateringshantering. Om det inte är det kontrollerar du om det finns problem med omfattnings konfigurationen. [Omfattnings konfigurationen](../update-management/scope-configuration.md) avgör vilka datorer som har kon figurer ats för uppdateringshantering. Information om hur du konfigurerar omfattnings konfigurationen för målet som datorn finns i [Aktivera datorer i arbets ytan](../update-management/enable-from-automation-account.md#enable-machines-in-the-workspace).

4. Kör den här frågan i din arbets yta.

   ```kusto
   Operation
   | where OperationCategory == 'Data Collection Status'
   | sort by TimeGenerated desc
   ```

   Om du får ett `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota` resultat har den kvot som definierats på din arbets yta nåtts, vilket har stoppat data från att sparas. I arbets ytan går du till **data volym hantering** under **användning och beräknade kostnader** och ändrar eller tar bort kvoten.

5. Om problemet fortfarande är olöst följer du stegen i [distribuera en Windows-hybrid Runbook Worker](../automation-windows-hrw-install.md) för att installera om hybrid Worker för Windows. För Linux följer du stegen i [distribuera ett Linux-hybrid Runbook Worker](../automation-linux-hrw-install.md).

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Scenario: det går inte att registrera Automation Resource Provider för prenumerationer

### <a name="issue"></a>Problem

När du arbetar med funktions distributioner i ditt Automation-konto inträffar följande fel:

```error
Error details: Unable to register Automation Resource Provider for subscriptions
```

### <a name="cause"></a>Orsak

Den automatiska resurs leverantören är inte registrerad i prenumerationen.

### <a name="resolution"></a>Lösning

Registrera automatiserings resurs leverantören genom att följa de här stegen i Azure Portal.

1. I listan Azure-tjänster längst ned i portalen väljer du **alla tjänster** och väljer sedan **prenumerationer** i gruppen allmän tjänst.

2. Välj din prenumeration.

3. Under **Inställningar** väljer du **resurs leverantörer**.

4. Kontrol lera att Microsoft. Automation Resource Provider är registrerad i listan över resurs leverantörer.

5. Om den inte visas registrerar du Microsoft. Automation-providern genom att följa stegen i [lösa fel för registrering av resurs leverantör](../../azure-resource-manager/templates/error-register-resource-provider.md).

## <a name="scenario-scheduled-update-with-a-dynamic-schedule-missed-some-machines"></a><a name="scheduled-update-missed-machines"></a>Scenario: schemalagd uppdatering med ett dynamiskt schemalagt schema missade vissa datorer

### <a name="issue"></a>Problem

Datorer som ingår i en för hands version av uppdateringen visas inte i listan över datorer som korrigeras under en schemalagd körning.

### <a name="cause"></a>Orsak

Det här problemet kan bero på någon av följande orsaker:

* De prenumerationer som har definierats i omfånget i en dynamisk fråga har inte kon figurer ATS för den registrerade Automation-providern.

* Datorerna var inte tillgängliga eller har inte rätt Taggar när schemat kördes.

### <a name="resolution"></a>Lösning

#### <a name="subscriptions-not-configured-for-registered-automation-resource-provider"></a>Prenumerationer har inte kon figurer ATS för registrerad Automation Resource Provider

Om din prenumeration inte har kon figurer ATS för Automation Resource Provider kan du inte fråga eller hämta information om datorer i den prenumerationen. Använd följande steg för att verifiera registreringen av prenumerationen.

1. I [Azure Portal](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)går du till listan över Azure-tjänster.

2. Välj **alla tjänster** och välj sedan **prenumerationer** i gruppen allmän tjänst.

3. Hitta den prenumeration som definierats i omfånget för din distribution.

4. Under **Inställningar** väljer du **resurs leverantörer**.

5. Verifiera att Microsoft. Automation Resource Provider är registrerad.

6. Om den inte visas registrerar du Microsoft. Automation-providern genom att följa stegen i [lösa fel för registrering av resurs leverantör](../../azure-resource-manager/templates/error-register-resource-provider.md).

#### <a name="machines-not-available-or-not-tagged-correctly-when-schedule-executed"></a>Datorerna är inte tillgängliga eller märkta korrekt när schemat körs

Använd följande procedur om din prenumeration har kon figurer ATS för Automation Resource Provider, men kör uppdaterings schemat med de angivna [dynamiska grupperna](../update-management/configure-groups.md) missade vissa datorer.

1. Öppna Automation-kontot i Azure Portal och välj **uppdateringshantering**.

2. Kontrol lera [uppdateringshantering historik](../update-management/deploy-updates.md#view-results-of-a-completed-update-deployment) för att fastställa den exakta tiden då uppdaterings distributionen kördes.

3. För datorer som du misstänker skulle ha missats av Uppdateringshantering använder du Azure Resource Graph (ARG) för att [hitta dator ändringar](../../governance/resource-graph/how-to/get-resource-changes.md#find-detected-change-events-and-view-change-details).

4. Sök efter ändringar under en avsevärd period, till exempel en dag, innan uppdaterings distributionen kördes.

5. Kontrol lera Sök resultaten för eventuella systemändrade ändringar, t. ex. borttagning eller uppdatering av ändringar, på datorerna under den här perioden. Dessa ändringar kan ändra maskin status eller Taggar så att datorerna inte är markerade i dator listan när uppdateringar distribueras.

6. Justera dator-och resurs inställningarna efter behov för att rätta till problem med maskin status eller tagg.

7. Kör uppdaterings schema igen för att säkerställa att distributionen med de angivna dynamiska grupperna innehåller alla datorer.

## <a name="scenario-expected-machines-dont-appear-in-preview-for-dynamic-group"></a><a name="machines-not-in-preview"></a>Scenario: förväntade datorer visas inte i för hands versionen för dynamisk grupp

### <a name="issue"></a>Problem

Virtuella datorer för valda omfång i en dynamisk grupp visas inte i listan Azure Portal för hands version. Den här listan består av alla datorer som hämtats av en ARG-fråga för de valda omfattningarna. Omfattningarna filtreras för datorer som har hybrid Runbook Worker installerade och för vilka du har åtkomst behörighet.

### <a name="cause"></a>Orsak

Här är möjliga orsaker till det här problemet:

* Du har inte rätt åtkomst till de valda omfattningarna.
* ARG-frågan hämtar inte de förväntade datorerna.
* Hybrid Runbook Worker är inte installerat på datorerna.

### <a name="resolution"></a>Lösning 

#### <a name="incorrect-access-on-selected-scopes"></a>Felaktig åtkomst för valda omfång

Azure Portal visar bara datorer för vilka du har skriv åtkomst i ett angivet omfång. Om du inte har rätt åtkomst till ett omfång, se [Självstudier: ge en användare åtkomst till Azure-resurser med hjälp av Azure Portal](../../role-based-access-control/quickstart-assign-role-user-portal.md).

#### <a name="arg-query-doesnt-return-expected-machines"></a>ARG-frågan returnerar inte förväntade datorer

Följ stegen nedan för att ta reda på om dina frågor fungerar som de ska.

1. Kör en ARG-fråga som visas nedan i bladet resurs diagram Utforskaren i Azure Portal. Den här frågan imiterar de filter som du valde när du skapade den dynamiska gruppen i Uppdateringshantering. Se [använda dynamiska grupper med uppdateringshantering](../update-management/configure-groups.md).

    ```kusto
    where (subscriptionId in~ ("<subscriptionId1>", "<subscriptionId2>") and type =~ "microsoft.compute/virtualmachines" and properties.storageProfile.osDisk.osType == "<Windows/Linux>" and resourceGroup in~ ("<resourceGroupName1>","<resourceGroupName2>") and location in~ ("<location1>","<location2>") )
    | project id, location, name, tags = todynamic(tolower(tostring(tags)))
    | where  (tags[tolower("<tagKey1>")] =~ "<tagValue1>" and tags[tolower("<tagKey2>")] =~ "<tagValue2>") // use this if "All" option selected for tags
    | where  (tags[tolower("<tagKey1>")] =~ "<tagValue1>" or tags[tolower("<tagKey2>")] =~ "<tagValue2>") // use this if "Any" option selected for tags
    | project id, location, name, tags
    ```

   Här är ett exempel:

    ```kusto
    where (subscriptionId in~ ("20780d0a-b422-4213-979b-6c919c91ace1", "af52d412-a347-4bc6-8cb7-4780fbb00490") and type =~ "microsoft.compute/virtualmachines" and properties.storageProfile.osDisk.osType == "Windows" and resourceGroup in~ ("testRG","withinvnet-2020-01-06-10-global-resources-southindia") and location in~ ("australiacentral","australiacentral2","brazilsouth") )
    | project id, location, name, tags = todynamic(tolower(tostring(tags)))
    | where  (tags[tolower("ms-resource-usage")] =~ "azure-cloud-shell" and tags[tolower("temp")] =~ "temp")
    | project id, location, name, tags
    ```

2. Kontrol lera om de datorer som du söker efter finns med i frågeresultatet.

3. Om datorerna inte finns med i listan beror det förmodligen på ett problem med det filter som har valts i den dynamiska gruppen. Justera grupp konfigurationen efter behov.

#### <a name="hybrid-runbook-worker-not-installed-on-machines"></a>Hybrid Runbook Worker inte installerat på datorer

Datorerna visas i argumenten ARG, men visas fortfarande inte i den dynamiska grupp förhands granskningen. I det här fallet kanske datorerna inte är utsedda som hybrid arbetare och kan därför inte köra Azure Automation och Uppdateringshantering jobb. För att se till att de datorer som du förväntar dig att se har kon figurer ATS som hybrid Runbook-arbetare:

1. I Azure Portal går du till Automation-kontot för en dator som inte visas korrekt.

2. Välj **hybrid Worker-grupper** under **process automatisering**.

3. Välj fliken **system hybrid Worker-grupper** .

4. Kontrol lera att hybrid Worker finns på den datorn.

5. Om datorn inte har kon figurer ATS som en hybrid Worker kan du göra justeringar med hjälp av anvisningar i [automatisera resurser i ditt data Center eller i molnet med hjälp av hybrid Runbook Worker](../automation-hybrid-runbook-worker.md).

6. Anslut datorn till Hybrid Runbook Workers gruppen.

7. Upprepa stegen ovan för alla datorer som inte har visats i förhands granskningen.

## <a name="scenario-update-management-components-enabled-while-vm-continues-to-show-as-being-configured"></a><a name="components-enabled-not-working"></a>Scenario: Uppdateringshantering-komponenter aktive ras, medan den virtuella datorn fortsätter att visas som konfigurerad

### <a name="issue"></a>Problem

Du kan fortsätta att se följande meddelande på en VM 15 minuter efter att distributionen har påbörjats:

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

### <a name="cause"></a>Orsak

Det här felet kan inträffa av följande orsaker:

* Kommunikation med Automation-kontot blockeras.

* Det finns dubbla dator namn med olika käll dator-ID. Det här scenariot inträffar när en virtuell dator med ett visst dator namn skapas i olika resurs grupper och rapporteras till samma logistik agent arbets yta i prenumerationen.

* Den VM-avbildning som distribueras kan komma från en klonad dator som inte har förberetts med system förberedelse (Sysprep) med Log Analytics agent för Windows installerat.

### <a name="resolution"></a>Lösning

Kör följande fråga på arbets ytan Log Analytics som är länkad till ditt Automation-konto för att få hjälp med att fastställa det exakta problemet med den virtuella datorn.

```
Update
| where Computer contains "fillInMachineName"
| project TimeGenerated, Computer, SourceComputerId, Title, UpdateState 
```

#### <a name="communication-with-automation-account-blocked"></a>Kommunikation med Automation-kontot blockerad

Gå till [nätverks planering](../update-management/overview.md#ports) och lär dig om vilka adresser och portar som måste tillåtas för att uppdateringshantering ska fungera.

#### <a name="duplicate-computer-name"></a>Duplicerat dator namn

Byt namn på de virtuella datorerna för att säkerställa unika namn i deras miljö.

#### <a name="deployed-image-from-cloned-machine"></a>Distribuerad avbildning från klonad dator

Om du använder en klonad bild har olika dator namn samma käll dator-ID. Om så är fallet:

1. I arbets ytan Log Analytics tar du bort den virtuella datorn från den sparade sökningen efter `MicrosoftDefaultScopeConfig-Updates` omfattnings konfigurationen om den visas. Sparade sökningar hittar du under **Allmänt** på arbets ytan.

2. Kör följande cmdlet.

    ```azurepowershell-interactive
    Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force
    ```

3. Kör `Restart-Service HealthService` för att starta om hälso tillstånds tjänsten. Den här åtgärden återskapar nyckeln och genererar en ny UUID.

4. Om den här metoden inte fungerar kör du Sysprep på avbildningen först och installerar sedan Log Analytics agent för Windows.

## <a name="scenario-you-receive-a-linked-subscription-error-when-you-create-an-update-deployment-for-machines-in-another-azure-tenant"></a><a name="multi-tenant"></a>Scenario: du får ett länkat prenumerations fel när du skapar en uppdaterings distribution för datorer i en annan Azure-klient

### <a name="issue"></a>Problem

Följande fel uppstår när du försöker skapa en uppdaterings distribution för datorer i en annan Azure-klient:

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

### <a name="cause"></a>Orsak

Det här felet uppstår när du skapar en uppdaterings distribution med virtuella Azure-datorer i en annan klient som ingår i en uppdaterings distribution.

### <a name="resolution"></a>Lösning

Använd följande lösning för att få de här objekten schemalagda. Du kan använda cmdleten [New-AzAutomationSchedule](/powershell/module/az.automation/new-azautomationschedule) med `ForUpdateConfiguration` parametern för att skapa ett schema. Använd sedan cmdleten [New-AzAutomationSoftwareUpdateConfiguration](/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration) och skicka datorerna i den andra klienten till- `NonAzureComputer` parametern. I följande exempel visas hur du gör detta:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="scenario-unexplained-reboots"></a><a name="node-reboots"></a>Scenario: avklarande omstarter

### <a name="issue"></a>Problem

Även om du har ställt in alternativet **Starta** om för att **aldrig starta om**, startar datorerna fortfarande om när uppdateringarna har installerats.

### <a name="cause"></a>Orsak

Windows Update kan ändras av flera register nycklar, vilken som helst kan ändra omstarts beteendet.

### <a name="resolution"></a>Lösning

Granska register nycklarna under [Konfigurera automatiska uppdateringar genom att redigera register](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) -och [register nycklar som används för att hantera omstart](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) för att kontrol lera att datorerna är korrekt konfigurerade.

## <a name="scenario-machine-shows-failed-to-start-in-an-update-deployment"></a><a name="failed-to-start"></a>Scenario: datorn visar att det inte gick att starta i en uppdaterings distribution

### <a name="issue"></a>Problem

En dator visar en `Failed to start` status. När du visar detaljerad information för datorn visas följande fel:

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

### <a name="cause"></a>Orsak

Felet kan uppstå på grund av någon av följande orsaker:

* Datorn finns inte längre.
* Datorn är inaktive rad och kan inte kontaktas.
* Datorn har ett problem med nätverks anslutningen och det går därför inte att komma åt hybrid Worker på datorn.
* En uppdatering har gjorts till den Log Analytics agenten som ändrade käll dator-ID: t.
* Uppdaterings körningen begränsades om du träffar gränsen på 200 samtidiga jobb i ett Automation-konto. Varje distribution betraktas som ett jobb, och varje dator i en uppdaterings distribution räknas som ett jobb. Alla andra automatiserings jobb eller uppdaterings distributioner som körs i ditt Automation-konto räknas mot gränsen för samtidiga jobb.

### <a name="resolution"></a>Lösning

Använd [dynamiska grupper](../update-management/configure-groups.md) för dina uppdaterings distributioner när det är tillämpligt. Dessutom kan du utföra följande steg.

1. Kontrol lera att datorn eller servern uppfyller [kraven](../update-management/overview.md#system-requirements).
2. Kontrol lera anslutningen till Hybrid Runbook Worker med hjälp av Hybrid Runbook Worker agent-felsökaren. Mer information om fel sökaren finns i [Felsöka problem med uppdaterings agenten](update-agent-issues.md).

## <a name="scenario-updates-are-installed-without-a-deployment"></a><a name="updates-nodeployment"></a>Scenario: uppdateringar installeras utan distribution

### <a name="issue"></a>Problem

När du registrerar en Windows-dator i Uppdateringshantering visas uppdateringar som installerats utan en distribution.

### <a name="cause"></a>Orsak

I Windows installeras uppdateringar automatiskt så snart de är tillgängliga. Det här beteendet kan orsaka förvirring om du inte schemalägger en uppdatering så att den distribueras till datorn.

### <a name="resolution"></a>Lösning

`HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU`Register nyckeln har standardinställningen 4: `auto download and install` .

För Uppdateringshantering-klienter rekommenderar vi att du anger den här nyckeln till 3: `auto download but do not auto install` .

Mer information finns i [Konfigurera automatiska uppdateringar](/windows/deployment/update/waas-wu-settings#configure-automatic-updates).

## <a name="scenario-machine-is-already-registered-to-a-different-account"></a><a name="machine-already-registered"></a>Scenario: datorn har redan registrerats till ett annat konto

### <a name="issue"></a>Problem

Du får följande fel meddelande:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>Orsak

Datorn har redan distribuerats till en annan arbets yta för Uppdateringshantering.

### <a name="resolution"></a>Lösning

1. Följ stegen under [datorer som inte visas i portalen under uppdateringshantering](#nologs) för att kontrol lera att datorn rapporterar till rätt arbets yta.
2. Rensa artefakter på datorn genom [att ta bort hybrid Runbook-gruppen](../automation-windows-hrw-install.md#remove-a-hybrid-worker-group)och försök sedan igen.

## <a name="scenario-machine-cant-communicate-with-the-service"></a><a name="machine-unable-to-communicate"></a>Scenario: datorn kan inte kommunicera med tjänsten

### <a name="issue"></a>Problem

Du får ett av följande fel meddelanden:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.Net.Http.HttpRequestException: An error occurred while sending the request. ---> System.Net.WebException: The underlying connection was closed: An unexpected error occurred on a receive. ---> System.ComponentModel.Win32Exception: The client and server can't communicate, because they do not possess a common algorithm
```

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value.
```

```error
The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.
```

```error
Access is denied. (Exception form HRESULT: 0x80070005(E_ACCESSDENIED))
```

### <a name="cause"></a>Orsak

En proxy, gateway eller brand vägg kan blockera nätverkskommunikation.

### <a name="resolution"></a>Lösning

Granska nätverket och kontrol lera att rätt portar och adresser är tillåtna. Se [nätverks krav](../automation-hybrid-runbook-worker.md#network-planning) för en lista över portar och adresser som krävs av uppdateringshantering och hybrid Runbook Worker.

## <a name="scenario-unable-to-create-self-signed-certificate"></a><a name="unable-to-create-selfsigned-cert"></a>Scenario: det går inte att skapa ett självsignerat certifikat

### <a name="issue"></a>Problem

Du får ett av följande fel meddelanden:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

### <a name="cause"></a>Orsak

Det gick inte att skapa ett självsignerat certifikat i Hybrid Runbook Worker.

### <a name="resolution"></a>Lösning

Kontrol lera att system-kontot har Läs behörighet till mappen **C:\ProgramData\Microsoft\Crypto\RSA** och försök igen.

## <a name="scenario-the-scheduled-update-failed-with-a-maintenancewindowexceeded-error"></a><a name="mw-exceeded"></a>Scenario: den schemalagda uppdateringen misslyckades med ett MaintenanceWindowExceeded-fel

### <a name="issue"></a>Problem

Standard underhålls perioden för uppdateringar är 120 minuter. Du kan öka underhålls perioden till högst 6 timmar eller 360 minuter.

### <a name="resolution"></a>Lösning

För att förstå varför detta inträffade under en uppdaterings körning när den har startats, [kontrollerar du jobbets utdata](../update-management/deploy-updates.md#view-results-of-a-completed-update-deployment) från den berörda datorn i körnings processen. Du kan hitta vissa fel meddelanden från dina datorer som du kan söka efter och vidta åtgärder för.  

Redigera eventuella misslyckade schemalagda uppdaterings distributioner och öka underhålls perioden.

Mer information om underhålls perioder finns i [Installera uppdateringar](../update-management/deploy-updates.md#schedule-an-update-deployment).

## <a name="scenario-machine-shows-as-not-assessed-and-shows-an-hresult-exception"></a><a name="hresult"></a>Scenario: datorn visas som "inte utvärderad" och visar ett HRESULT-undantag

### <a name="issue"></a>Problem

* Du har datorer som visas som `Not assessed` **kompatibla** och du ser ett undantags meddelande nedan.
* Du ser en HRESULT-felkod i portalen.

### <a name="cause"></a>Orsak

Uppdaterings agenten (Windows Update agenten i Windows, paket hanteraren för en Linux-distribution) är inte korrekt konfigurerad. Uppdateringshantering är beroende av datorns uppdaterings agent för att tillhandahålla de uppdateringar som behövs, status för korrigeringen och resultaten av distribuerade uppdateringar. Utan den här informationen kan Uppdateringshantering inte korrekt rapportera om de korrigeringar som behövs eller är installerade.

### <a name="resolution"></a>Lösning

Försök att utföra uppdateringar lokalt på datorn. Om den här åtgärden Miss lyckas innebär det vanligt vis att det finns ett konfigurations fel för uppdaterings agenten.

Det här problemet orsakas ofta av nätverks konfiguration och brand Väggs problem. Använd följande kontroller för att åtgärda problemet.

* För Linux kontrollerar du lämplig dokumentation för att se till att du kan komma åt nätverks slut punkten för din paket lagrings plats.

* För Windows kontrollerar du att agent konfigurationen som anges i [uppdateringar inte laddas ned från intranät slut punkten (WSUS/SCCM)](/windows/deployment/update/windows-update-troubleshooting#updates-arent-downloading-from-the-intranet-endpoint-wsussccm).

  * Om datorerna har kon figurer ATS för Windows Update kontrollerar du att du kan komma åt slut punkterna som beskrivs i [problem som rör http/proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy).
  * Om datorerna är konfigurerade för Windows Server Update Services (WSUS) ser du till att du kan ansluta till WSUS-servern som kon figurer ATS av [register nyckeln WUServer principvärdet](/windows/deployment/update/waas-wu-settings).

Om du ser ett HRESULT dubbelklickar du på undantaget som visas i rött för att se hela undantags meddelandet. Granska följande tabell för möjliga lösningar eller rekommenderade åtgärder.

|Undantag  |Lösning eller åtgärd  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Sök i fel kod listan i [Windows Update](https://support.microsoft.com/help/938205/windows-update-error-code-list) om du vill ha mer information om orsaken till undantaget.        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | Detta indikerar problem med nätverks anslutningen. Kontrol lera att datorn har nätverks anslutning till Uppdateringshantering. Se avsnittet [nätverks planering](../update-management/overview.md#ports) för en lista över nödvändiga portar och adresser.        |
|`0x8024001E`| Uppdaterings åtgärden slutfördes inte på grund av att tjänsten eller systemet stängdes av.|
|`0x8024002E`| Windows Updates tjänsten är inaktive rad.|
|`0x8024402C`     | Om du använder en WSUS-server kontrollerar du att register värden för `WUServer` och `WUStatusServer` under  `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` register nyckeln anger rätt WSUS-server.        |
|`0x80072EE2`|Det finns ett problem med nätverks anslutningen eller ett problem med att prata med en konfigurerad WSUS-server. Kontrol lera inställningarna för WSUS och se till att tjänsten är tillgänglig från klienten.|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Kontrol lera att Windows Updates tjänsten (wuauserv) körs och inte inaktive rad.        |
|`0x80070005`| Ett fel vid nekad åtkomst kan orsakas av något av följande:<br> Infekterad dator<br> Windows Update inställningarna har inte kon figurer ATS korrekt<br> Fil behörighets fel med mappen%WinDir%\SoftwareDistribution<br> Det finns inte tillräckligt med disk utrymme på system enheten (C:).
|Alla andra generiska undantag     | Kör en sökning på Internet för möjliga lösningar och arbeta med din lokala IT-support.         |

Att granska **%windir%\windowsupdate.log** -filen kan också hjälpa dig att avgöra möjliga orsaker. Mer information om hur du läser loggen finns i [så här läser du filen windowsupdate. log](https://support.microsoft.com/help/902093/how-to-read-the-windowsupdate-log-file).

Du kan också hämta och köra [Windows Update fel sökaren](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) för att söka efter problem med Windows Update på datorn.

> [!NOTE]
> [Windows Update fel söknings](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) dokumentationen visar att den används på Windows-klienter, men fungerar även på Windows Server.

## <a name="scenario-update-run-returns-failed-status-linux"></a>Scenario: uppdaterings körning returnerar misslyckad status (Linux)

### <a name="issue"></a>Problem

En uppdaterings körning startar men påträffar fel under körningen.

### <a name="cause"></a>Orsak

Möjliga orsaker:

* Paket hanteraren är inte felfri.
* Uppdaterings agent (WUA för Windows, distribution Package Manager för Linux) är felkonfigurerad.
* Vissa paket stör molnbaserad uppdatering.
* Datorn kan inte kontaktas.
* Uppdateringar hade beroenden som inte lösts.

### <a name="resolution"></a>Lösning

Om fel inträffar när en uppdatering körs när den har startats, [kontrollerar du jobbets utdata](../update-management/deploy-updates.md#view-results-of-a-completed-update-deployment) från den berörda datorn i körnings processen. Du kan hitta vissa fel meddelanden från dina datorer som du kan söka efter och vidta åtgärder för. Uppdateringshantering kräver att paket hanteraren är felfri för lyckade uppdaterings distributioner.

Om vissa korrigeringar, paket eller uppdateringar visas omedelbart innan jobbet Miss lyckas kan du prova att [utesluta](../update-management/deploy-updates.md#schedule-an-update-deployment) dessa objekt från nästa uppdaterings distribution. Information om hur du samlar in logg information från Windows Update finns i [Windows Update loggfiler](/windows/deployment/update/windows-update-logs).

Om du inte kan lösa ett uppdaterings problem gör du en kopia av **/var/opt/Microsoft/omsagent/Run/automationworker/omsupdatemgmt.log** -filen och bevarar den i fel söknings syfte innan nästa uppdaterings distribution startar.

## <a name="patches-arent-installed"></a>Korrigeringsfiler har inte installerats

### <a name="machines-dont-install-updates"></a>Datorerna installerar inte uppdateringar

Försök att köra uppdateringar direkt på datorn. Om datorn inte kan tillämpa uppdateringarna läser du [listan över eventuella fel i fel söknings guiden](#hresult).

Om uppdateringarna körs lokalt kan du försöka ta bort och installera om agenten på datorn genom att följa anvisningarna i [ta bort en virtuell dator från uppdateringshantering](../update-management/remove-vms.md).

### <a name="i-know-updates-are-available-but-they-dont-show-as-available-on-my-machines"></a>Jag vet att uppdateringar är tillgängliga, men de visas inte som tillgängliga på mina datorer

Detta händer ofta om datorerna har kon figurer ATS för att hämta uppdateringar från WSUS eller Microsoft Endpoint Configuration Manager, men WSUS och Configuration Manager inte har godkänt uppdateringarna.

Du kan kontrol lera om datorerna har kon figurer ATS för WSUS och SCCM genom `UseWUServer` att referera register nyckeln till register nycklarna i [Konfigurera automatiska uppdateringar genom att redigera register avsnittet i den här artikeln](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s).

Om uppdateringar inte har godkänts i WSUS installeras de inte. Du kan söka efter ej godkända uppdateringar i Log Analytics genom att köra följande fråga.

  ```kusto
  Update | where UpdateState == "Needed" and ApprovalSource == "WSUS" and Approved == "False" | summarize max(TimeGenerated) by Computer, KBID, Title
  ```

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>Uppdateringar visas som installerade, men jag hittar dem inte på datorn

Uppdateringar ersätts ofta av andra uppdateringar. Mer information finns i [Uppdatera ersätts](/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer) i Windows Update fel söknings guide.

### <a name="installing-updates-by-classification-on-linux"></a>Installera uppdateringar efter klassificering i Linux

Distributionen av uppdateringar till Linux efter klassificering (”kritiska uppdateringar och säkerhetsuppdateringar”) har viktiga förbehåll, särskilt för CentOS. Dessa begränsningar beskrivs på [sidan uppdateringshantering översikt](../update-management/overview.md#linux).

### <a name="kb2267602-is-consistently-missing"></a>KB2267602 saknas konsekvent

KB2267602 är [Windows Defender-definitionsuppdateringen](https://www.microsoft.com/wdsi/definitions). Den uppdateras dagligen.

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du prova någon av följande kanaler för ytterligare support.

* Få svar från Azure-experter via [Azure-forum](https://azure.microsoft.com/support/forums/).
* Anslut till [@AzureSupport](https://twitter.com/azuresupport) , det officiella Microsoft Azure kontot för att förbättra kund upplevelsen.
* Filen en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj **få support**.
