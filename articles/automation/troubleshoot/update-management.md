---
title: Felsöka Azure Automation Uppdateringshantering problem
description: Den här artikeln beskriver hur du felsöker och löser problem med Azure Automation Uppdateringshantering.
services: automation
ms.subservice: update-management
ms.date: 04/16/2021
ms.topic: troubleshooting
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 36bfd2185cb7a192ce0113ee0722395c8a4ee928
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830312"
---
# <a name="troubleshoot-update-management-issues"></a>Felsöka problem med Uppdateringshantering

Den här artikeln beskriver problem som du kan få problem med när du distribuerar Uppdateringshantering på dina datorer. Det finns en felsökare för agenten Hybrid Runbook Worker att fastställa det underliggande problemet. Mer information om felsökaren finns i Felsöka problem med [Windows Update-agenten](update-agent-issues.md) och [Felsöka problem med Linux-uppdateringsagenten.](update-agent-issues-linux.md) Information om andra problem med funktionsdistribution finns i [Felsöka problem med funktionsdistribution.](onboarding.md)

>[!NOTE]
>Om du får problem när du distribuerar Uppdateringshantering på en Windows-dator öppnar du Windows Loggboken och kontrollerar **händelseloggen för Operations Manager** under **Program- och** tjänstloggar på den lokala datorn. Leta efter händelser med händelse-ID 4502 och händelseinformation som innehåller `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent` .

## <a name="scenario-linux-updates-shown-as-pending-and-those-installed-vary"></a><a name="updates-linux-installed-different"></a>Scenario: Linux-uppdateringar som visas som väntande och de installerade varierar

### <a name="issue"></a>Problem

För din Linux-dator visar Uppdateringshantering specifika uppdateringar som är tillgängliga under klassificeringen **Säkerhet** med **mera.** Men när ett uppdateringsschema körs på datorn, till exempel  för att endast installera uppdateringar som matchar säkerhetsklassificeringen, skiljer sig de installerade uppdateringarna från eller en delmängd av uppdateringarna som visades tidigare som matchar klassificeringen.

### <a name="cause"></a>Orsak

När en utvärdering av väntande OS-uppdateringar för din Linux-dator är klar används OVAL-filer [(Open Vulnerability and Assessment Language)](https://oval.mitre.org/) som tillhandahålls av Linux-distroleverantören av Uppdateringshantering för klassificering. Kategorisering görs för Linux-uppdateringar som säkerhet eller **andra,** baserat på OVAL-filer som innehåller uppdateringar som adresserar säkerhetsproblem eller sårbarheter.  Men när uppdateringsschemat körs körs det på Linux-datorn med lämplig pakethanterare som YUM, APT eller ZYPPER för att installera dem. Pakethanteraren för Linux-distributionen kan ha en annan mekanism för att klassificera uppdateringar, där resultaten kan skilja sig från dem som hämtas från OVAL-filer Uppdateringshantering.

### <a name="resolution"></a>Lösning

Du kan manuellt kontrollera Linux-datorn, tillämpliga uppdateringar och deras klassificering enligt distributionens pakethanterare. Kör följande kommandon för att förstå **vilka uppdateringar** som klassificeras som säkerhet av din pakethanterare.

För YUM returnerar följande kommando en lista över uppdateringar som inte är noll kategoriserade som **Security** by Red Hat. Observera att när det gäller CentOS returnerar den alltid en tom lista och ingen säkerhetsklassificering sker.

```bash
sudo yum -q --security check-update
```

För ZYPPER returnerar följande kommando en lista över uppdateringar som inte är noll kategoriserade som **Security** by SUSE.

```bash
sudo LANG=en_US.UTF8 zypper --non-interactive patch --category security --dry-run
```

För APT returnerar följande kommando en lista över uppdateringar som inte är noll kategoriserade som **Security** av Canonical för Ubuntu Linux distributioner.

```bash
sudo grep security /etc/apt/sources.list > /tmp/oms-update-security.list LANG=en_US.UTF8 sudo apt-get -s dist-upgrade -oDir::Etc::Sourcelist=/tmp/oms-update-security.list
```

Från den här listan kör du sedan kommandot `grep ^Inst` för att hämta alla väntande säkerhetsuppdateringar.

## <a name="scenario-you-receive-the-error-failed-to-enable-the-update-solution"></a><a name="failed-to-enable-error"></a>Scenario: Du får felmeddelandet "Det gick inte att aktivera uppdateringslösningen"

### <a name="issue"></a>Problem

När du försöker aktivera Uppdateringshantering i ditt Automation-konto visas följande fel:

```error
Error details: Failed to enable the Update solution
```

### <a name="cause"></a>Orsak

Det här felet kan inträffa av följande skäl:

* Kraven för nätverksbrandväggen för Log Analytics-agenten kanske inte är korrekt konfigurerade. Den här situationen kan göra att agenten misslyckas när DNS-URL:erna matchas.

* Uppdateringshantering mål är felkonfigurerad och datorn tar inte emot uppdateringar som förväntat.

* Du kanske också märker att datorn visar statusen `Non-compliant` Under **Efterlevnad**. Samtidigt rapporterar **agenten Desktop Analytics** agenten som `Disconnected` .

### <a name="resolution"></a>Lösning

* Kör felsökaren för [Windows](update-agent-issues.md#troubleshoot-offline) eller [Linux](update-agent-issues-linux.md#troubleshoot-offline), beroende på operativsystemet.

* Gå till [Nätverkskonfiguration om](../automation-hybrid-runbook-worker.md#network-planning) du vill veta vilka adresser och portar som måste tillåtas Uppdateringshantering ska fungera.  

* Sök efter omfångskonfigurationsproblem. [Omfångskonfigurationen](../update-management/scope-configuration.md) avgör vilka datorer som är konfigurerade för Uppdateringshantering. Om datorn visas på arbetsytan men inte i Uppdateringshantering måste du ange omfångskonfigurationen så att den riktar in sig på datorerna. Mer information om omfångskonfigurationen finns [i Aktivera datorer på arbetsytan](../update-management/enable-from-automation-account.md#enable-machines-in-the-workspace).

* Ta bort arbetskonfigurationen genom att följa stegen i Ta bort Hybrid Runbook Worker från en lokal [Windows-dator](../automation-windows-hrw-install.md#remove-windows-hybrid-runbook-worker) eller Ta bort Hybrid Runbook Worker från en lokal [Linux-dator](../automation-linux-hrw-install.md#remove-linux-hybrid-runbook-worker).

## <a name="scenario-superseded-update-indicated-as-missing-in-update-management"></a>Scenario: Ersatt uppdatering anges som saknad i Uppdateringshantering

### <a name="issue"></a>Problem

Gamla uppdateringar visas för ett Automation-konto som saknade även om de har ersatts. En ersatt uppdatering är en uppdatering som du inte behöver installera eftersom det finns en senare uppdatering som korrigerar samma säkerhetsrisk. Uppdateringshantering ignorerar den ersatta uppdateringen och gör den inte tillämplig till förmån för den ersatta uppdateringen. Information om ett relaterat problem [finns i Uppdatera ersätts](/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer).

### <a name="cause"></a>Orsak

Ersatta uppdateringar nekas inte i Windows Server Update Services (WSUS) så att de inte kan anses vara tillämpliga.

### <a name="resolution"></a>Lösning

När en ersatt uppdatering blir 100 procent inte tillämplig bör du ändra godkännandetillståndet för uppdateringen till `Declined` i WSUS. Så här ändrar du godkännandetillståndet för alla dina uppdateringar:

1. I Automation-kontot väljer du Uppdateringshantering **för att** visa datorstatus. Se [Visa uppdateringsutvärderingar.](../update-management/view-update-assessments.md)

2. Kontrollera den ersatta uppdateringen för att se till att den är 100 procent inte tillämplig.

3. På WSUS-servern rapporterar datorerna till och [avböjer uppdateringen](/windows-server/administration/windows-server-update-services/manage/updates-operations#declining-updates).

4. Välj **Datorer** och tvinga fram en ny **genomsökning** för kompatibilitet i kolumnen Efterlevnad. Se [Hantera uppdateringar för virtuella datorer.](../update-management/manage-updates-for-vm.md)

5. Upprepa stegen ovan för andra ersatta uppdateringar.

6. För Windows Server Update Services (WSUS) rensar du alla ersatta uppdateringar för att uppdatera infrastrukturen med hjälp av rensningsguiden [för WSUS-servern.](/windows-server/administration/windows-server-update-services/manage/the-server-cleanup-wizard)

7. Upprepa den här proceduren regelbundet för att åtgärda visningsproblemet och minimera mängden diskutrymme som används för uppdateringshantering.

## <a name="scenario-machines-dont-show-up-in-the-portal-under-update-management"></a><a name="nologs"></a>Scenario: Datorer visas inte i portalen under Uppdateringshantering

### <a name="issue"></a>Problem

Dina datorer har följande symptom:

* Datorn visas `Not configured` från den Uppdateringshantering för en virtuell dator.

* Datorerna saknas i den Uppdateringshantering vyn för ditt Azure Automation konto.

* Du har datorer som visas som `Not assessed` under **Efterlevnad**. Du ser dock pulsslagsdata i Azure Monitor för Hybrid Runbook Worker men inte för Uppdateringshantering.

### <a name="cause"></a>Orsak

Det här problemet kan orsakas av lokala konfigurationsproblem eller felaktigt konfigurerad omfångskonfiguration. Möjliga specifika orsaker är:

* Du kan behöva registrera om och installera om Hybrid Runbook Worker.

* Du kan ha definierat en kvot på din arbetsyta som har nåtts och som förhindrar ytterligare datalagring.

### <a name="resolution"></a>Lösning

1. Kör felsökaren för [Windows](update-agent-issues.md#troubleshoot-offline) eller [Linux](update-agent-issues-linux.md#troubleshoot-offline), beroende på operativsystemet.

2. Kontrollera att datorn rapporterar till rätt arbetsyta. Vägledning om hur du verifierar den här aspekten finns i [Verifiera agentanslutning till Azure Monitor](../../azure-monitor/agents/agent-windows.md#verify-agent-connectivity-to-azure-monitor). Kontrollera också att den här arbetsytan är länkad till ditt Azure Automation konto. Bekräfta genom att gå till ditt Automation-konto och välja **Länkad arbetsyta** under **Relaterade resurser.**

3. Kontrollera att datorerna visas på Log Analytics-arbetsytan som är länkad till ditt Automation-konto. Kör följande fråga på Log Analytics-arbetsytan.

   ```kusto
   Heartbeat
   | summarize by Computer, Solutions
   ```

    Om du inte ser datorn i frågeresultatet har den inte checkats in nyligen. Det finns förmodligen ett lokalt konfigurationsproblem och du bör [installera om agenten](../../azure-monitor/vm/quick-collect-windows-computer.md#install-the-agent-for-windows).

    Om datorn visas i frågeresultatet kontrollerar du att  uppdateringar visas under **egenskapen** Lösningar. Detta verifierar att den har registrerats med Uppdateringshantering. Om det inte är det kontrollerar du om det finns problem med omfångskonfigurationen. [Omfångskonfigurationen](../update-management/scope-configuration.md) avgör vilka datorer som är konfigurerade för Uppdateringshantering. Information om hur du konfigurerar omfångskonfigurationen för måldatorn finns [i Aktivera datorer på arbetsytan](../update-management/enable-from-automation-account.md#enable-machines-in-the-workspace).

4. Kör den här frågan på din arbetsyta.

   ```kusto
   Operation
   | where OperationCategory == 'Data Collection Status'
   | sort by TimeGenerated desc
   ```

   Om du får ett `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota` resultat har kvoten som definierats för din arbetsyta nåtts, vilket har hindrat data från att sparas. Gå till datavolymhantering under **Användning och uppskattade** kostnader på **arbetsytan** och ändra eller ta bort kvoten.

5. Om problemet fortfarande är olöst följer du stegen i Distribuera en [Windows-Hybrid Runbook Worker](../automation-windows-hrw-install.md) för att installera om Hybrid Worker för Windows. För Linux följer du stegen i Distribuera [en Linux-Hybrid Runbook Worker](../automation-linux-hrw-install.md).

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Scenario: Det går inte att registrera Automation-resursprovidern för prenumerationer

### <a name="issue"></a>Problem

När du arbetar med funktionsdistributioner i ditt Automation-konto uppstår följande fel:

```error
Error details: Unable to register Automation Resource Provider for subscriptions
```

### <a name="cause"></a>Orsak

Automation-resursprovidern är inte registrerad i prenumerationen.

### <a name="resolution"></a>Lösning

Registrera Automation-resursprovidern genom att följa dessa steg i Azure Portal.

1. I listan över Azure-tjänster längst ned i portalen väljer du **Alla** tjänster och sedan **Prenumerationer** i gruppen Allmän tjänst.

2. Välj din prenumeration.

3. Under **Inställningar** väljer du **Resursproviders.**

4. I listan över resursproviders kontrollerar du att resursprovidern Microsoft.Automation är registrerad.

5. Om den inte visas registrerar du Microsoft.Automation-providern genom att följa stegen i Åtgärda [fel vid registrering av resursprovider.](../../azure-resource-manager/templates/error-register-resource-provider.md)

## <a name="scenario-scheduled-update-did-not-patch-some-machines"></a><a name="scheduled-update-missed-machines"></a>Scenario: Schemalagd uppdatering korrigerade inte vissa datorer

### <a name="issue"></a>Problem

Alla datorer som ingår i en uppdateringsförhandsvisning visas inte i listan över datorer som korrigerats under en schemalagd körning, eller så visas inte virtuella datorer för valda omfång för en dynamisk grupp i listan med förhandsversioner av uppdateringar i portalen.

Listan med förhandsversioner av uppdateringar består av alla datorer som hämtats [av Azure Resource Graph](../../governance/resource-graph/overview.md) fråga för de valda omfången. Omfången filtreras för datorer som har ett system som Hybrid Runbook Worker installerat och som du har åtkomstbehörighet för.

### <a name="cause"></a>Orsak

Det här problemet kan ha någon av följande orsaker:

* Prenumerationerna som definierats i omfånget i en dynamisk fråga har inte konfigurerats för den registrerade Automation-resursprovidern.

* Datorerna var inte tillgängliga eller hade inte lämpliga taggar när schemat kördes.

* Du har inte rätt åtkomst till de valda omfången.

* Den Azure Resource Graph frågan hämtar inte de förväntade datorerna.

* Systemadministratören Hybrid Runbook Worker är inte installerad på datorerna.

### <a name="resolution"></a>Lösning

#### <a name="subscriptions-not-configured-for-registered-automation-resource-provider"></a>Prenumerationer som inte har konfigurerats för den registrerade Automation-resursprovidern

Om din prenumeration inte har konfigurerats för Automation-resursprovidern kan du inte fråga efter eller hämta information på datorer i prenumerationen. Använd följande steg för att verifiera registreringen för prenumerationen.

1. Gå till [Azure Portal](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)Azure-tjänstlistan i listan med azure-tjänster.

2. Välj **Alla tjänster** och välj sedan **Prenumerationer** i tjänstgruppen Allmänt.

3. Hitta prenumerationen som definierats i omfånget för distributionen.

4. Under **Inställningar** väljer du **Resursproviders.**

5. Kontrollera att resursprovidern Microsoft.Automation är registrerad.

6. Om den inte visas registrerar du Microsoft.Automation-providern genom att följa stegen i [Lösa fel vid registrering av resursprovider.](../../azure-resource-manager/templates/error-register-resource-provider.md)

#### <a name="machines-not-available-or-not-tagged-correctly-when-schedule-executed"></a>Datorer är inte tillgängliga eller taggas inte korrekt när schemat körs

Använd följande procedur om din prenumeration är konfigurerad för Automation-resursprovidern, men om du kör uppdateringsschemat med de angivna [dynamiska grupperna missade](../update-management/configure-groups.md) du några datorer.

1. I Azure Portal du Automation-kontot och väljer **Uppdateringshantering**.

2. Kontrollera [Uppdateringshantering för](../update-management/deploy-updates.md#view-results-of-a-completed-update-deployment) att fastställa den exakta tiden när uppdateringsdistributionen har körts.

3. För datorer som du misstänker har missats av Uppdateringshantering använder du arg Azure Resource Graph (ARG) för att [hitta datorändringar.](../../governance/resource-graph/how-to/get-resource-changes.md#find-detected-change-events-and-view-change-details)

4. Sök efter ändringar under en längre tid, till exempel en dag, innan uppdateringsdistributionen körs.

5. Kontrollera sökresultaten för eventuella systemförändringar, till exempel borttagnings- eller uppdateringsändringar, på datorerna under den här perioden. Dessa ändringar kan ändra datorstatus eller taggar så att datorer inte väljs i datorlistan när uppdateringar distribueras.

6. Justera dator- och resursinställningarna efter behov för att åtgärda problem med datorstatus eller taggar.

7. Kör uppdateringsschemat igen för att säkerställa att distributionen med de angivna dynamiska grupperna innehåller alla datorer.

#### <a name="incorrect-access-on-selected-scopes"></a>Felaktig åtkomst för valda omfång

I Azure Portal endast datorer som du har skrivbehörighet för i ett visst omfång. Om du inte har rätt åtkomst för ett omfång kan du gå till [Självstudie: Bevilja](../../role-based-access-control/quickstart-assign-role-user-portal.md)en användare åtkomst till Azure-resurser med hjälp av Azure Portal .

#### <a name="resource-graph-query-doesnt-return-expected-machines"></a>Resource Graph frågan returnerar inte förväntade datorer

Följ stegen nedan för att ta reda på om dina frågor fungerar korrekt.

1. Kör en Azure Resource Graph-fråga formaterad enligt nedan i Resource Graph Explorer-bladet i Azure Portal. Om du är nybörjare på Azure Resource Graph den här [snabbstarten](../../governance/resource-graph/first-query-portal.md) för att lära dig hur du arbetar med Resource Graph explorer. Den här frågan efterliknar de filter som du valde när du skapade den dynamiska gruppen i Uppdateringshantering. Se [Använda dynamiska grupper med Uppdateringshantering](../update-management/configure-groups.md).

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

2. Kontrollera om de datorer som du letar efter visas i frågeresultatet.

3. Om datorerna inte visas finns det förmodligen ett problem med filtret som valts i den dynamiska gruppen. Justera gruppkonfigurationen efter behov.

#### <a name="hybrid-runbook-worker-not-installed-on-machines"></a>Hybrid Runbook Worker inte installerat på datorer

Datorer visas i Azure Resource Graph frågeresultat, men visas fortfarande inte i den dynamiska gruppförhandsvisningen. I det här fallet är det inte möjligt att datorerna anges som hybrid runbook-systemarbetare och därför inte kan köra Azure Automation och Uppdateringshantering jobb. För att säkerställa att de datorer som du förväntar dig att se är konfigurerade som systemet Hybrid Runbook Workers:

1. I Azure Portal du till Automation-kontot för en dator som inte visas korrekt.

2. Välj **Hybrid Worker-grupper** under **Processautomatisering.**

3. Välj fliken **Hybrid Worker-systemgrupper.**

4. Kontrollera att Hybrid Worker finns för den datorn.

5. Om datorn inte har ställts in som Hybrid Runbook Worker systemadministratör läser du metoderna för att aktivera datorn i avsnittet [Aktivera Uppdateringshantering](../update-management/overview.md#enable-update-management) i artikeln Uppdateringshantering Översikt. Metoden för att aktivera baseras på den miljö som datorn körs i.

6. Upprepa stegen ovan för alla datorer som inte har visats i förhandsversionen.

## <a name="scenario-update-management-components-enabled-while-vm-continues-to-show-as-being-configured"></a><a name="components-enabled-not-working"></a>Scenario: Uppdateringshantering-komponenter aktiverade, medan den virtuella datorn fortsätter att visas som konfigurerad

### <a name="issue"></a>Problem

Du fortsätter att se följande meddelande på en virtuell dator 15 minuter efter att distributionen börjar:

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

### <a name="cause"></a>Orsak

Det här felet kan inträffa av följande skäl:

* Kommunikationen med Automation-kontot blockeras.

* Det finns ett duplicerat datornamn med olika källdator-ID:er. Det här scenariot inträffar när en virtuell dator med ett visst datornamn skapas i olika resursgrupper och rapporterar till samma logistikagentarbetsyta i prenumerationen.

* Vm-avbildningen som distribueras kan komma från en klonad dator som inte förbereddes med systemförberedelse (sysprep) med Log Analytics-agenten för Windows installerad.

### <a name="resolution"></a>Lösning

För att fastställa det exakta problemet med den virtuella datorn kör du följande fråga på Log Analytics-arbetsytan som är länkad till ditt Automation-konto.

```
Update
| where Computer contains "fillInMachineName"
| project TimeGenerated, Computer, SourceComputerId, Title, UpdateState 
```

#### <a name="communication-with-automation-account-blocked"></a>Kommunikation med Blockerat Automation-konto

Gå till [Nätverksplanering för](../update-management/overview.md#ports) att lära dig vilka adresser och portar som måste tillåtas Uppdateringshantering ska fungera.

#### <a name="duplicate-computer-name"></a>Duplicerat datornamn

Byt namn på dina virtuella datorer för att säkerställa unika namn i deras miljö.

#### <a name="deployed-image-from-cloned-machine"></a>Distribuerad avbildning från klonad dator

Om du använder en klonad avbildning har olika datornamn samma källdator-ID. Om så är fallet:

1. I Log Analytics-arbetsytan tar du bort den virtuella datorn från den sparade sökningen efter `MicrosoftDefaultScopeConfig-Updates` omfångskonfigurationen om den visas. Sparade sökningar finns under **Allmänt på** din arbetsyta.

2. Kör följande cmdlet.

    ```azurepowershell-interactive
    Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force
    ```

3. Kör `Restart-Service HealthService` för att starta om hälsotjänsten. Den här åtgärden återskapar nyckeln och genererar ett nytt UUID.

4. Om den här metoden inte fungerar kör du sysprep på avbildningen först och installerar sedan Log Analytics-agenten för Windows.

## <a name="scenario-you-receive-a-linked-subscription-error-when-you-create-an-update-deployment-for-machines-in-another-azure-tenant"></a><a name="multi-tenant"></a>Scenario: Du får ett länkat prenumerationsfel när du skapar en uppdateringsdistribution för datorer i en annan Azure-klientorganisation

### <a name="issue"></a>Problem

Du får följande fel när du försöker skapa en uppdateringsdistribution för datorer i en annan Azure-klientorganisation:

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

### <a name="cause"></a>Orsak

Det här felet uppstår när du skapar en uppdateringsdistribution som har virtuella Azure-datorer i en annan klientorganisation som ingår i en uppdateringsdistribution.

### <a name="resolution"></a>Lösning

Använd följande lösning för att schemalägga dessa objekt. Du kan använda cmdleten [New-AzAutomationSchedule](/powershell/module/az.automation/new-azautomationschedule) med `ForUpdateConfiguration` parametern för att skapa ett schema. Använd sedan [cmdleten New-AzAutomationSoftwareUpdateConfiguration](/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration) och skicka datorerna i den andra klientorganisationen till `NonAzureComputer` parametern . I följande exempel visas hur du gör detta:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="scenario-unexplained-reboots"></a><a name="node-reboots"></a>Scenario: Oförklarliga omstarter

### <a name="issue"></a>Problem

Även om du har ställt in **alternativet Omstartskontroll** på **Starta aldrig om** startar datorerna fortfarande om efter att uppdateringarna har installerats.

### <a name="cause"></a>Orsak

Windows Update kan ändras av flera registernycklar, som alla kan ändra omstartsbeteendet.

### <a name="resolution"></a>Lösning

Granska registernycklarna som anges under Konfigurera [](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) automatiska [uppdateringar](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) genom att redigera register- och registernycklarna som används för att hantera omstart för att kontrollera att datorerna är korrekt konfigurerade.

## <a name="scenario-machine-shows-failed-to-start-in-an-update-deployment"></a><a name="failed-to-start"></a>Scenario: Datorn visar "Det gick inte att starta" i en uppdateringsdistribution

### <a name="issue"></a>Problem

En dator visar en `Failed to start` status. När du visar den specifika informationen för datorn visas följande fel:

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

### <a name="cause"></a>Orsak

Felet kan uppstå på grund av någon av följande orsaker:

* Datorn finns inte längre.
* Datorn är avstängd och kan inte nås.
* Datorn har problem med nätverksanslutningen och därför går det inte att nå Hybrid Worker på datorn.
* Det gjordes en uppdatering av Log Analytics-agenten som ändrade källdatorns ID.
* Uppdateringskörningen begränsades om du har gränsen på 200 samtidiga jobb i ett Automation-konto. Varje distribution betraktas som ett jobb och varje dator i en uppdateringsdistribution räknas som ett jobb. Alla andra automationsjobb eller uppdateringsdistributioner som för närvarande körs i ditt Automation-konto räknas mot gränsen för samtidiga jobb.

### <a name="resolution"></a>Lösning

Använd dynamiska grupper [för dina uppdateringsdistributioner](../update-management/configure-groups.md) när det är tillämpligt. Dessutom kan du göra följande.

1. Kontrollera att datorn eller servern uppfyller [kraven](../update-management/overview.md#system-requirements).
2. Verifiera anslutningen till Hybrid Runbook Worker med hjälp Hybrid Runbook Worker felsökaren för agenten. Mer information om felsökaren finns i Felsöka [problem med uppdateringsagenten.](update-agent-issues.md)

## <a name="scenario-updates-are-installed-without-a-deployment"></a><a name="updates-nodeployment"></a>Scenario: Uppdateringar installeras utan distribution

### <a name="issue"></a>Problem

När du registrerar en Windows-dator i Uppdateringshantering visas uppdateringar som installerats utan distribution.

### <a name="cause"></a>Orsak

I Windows installeras uppdateringar automatiskt så snart de är tillgängliga. Det här beteendet kan orsaka förvirring om du inte har schemaläggt en uppdatering som ska distribueras till datorn.

### <a name="resolution"></a>Lösning

Registernyckeln  `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU` har som standard inställningen 4: `auto download and install` .

För Uppdateringshantering-klienter rekommenderar vi att du anger den här nyckeln till 3: `auto download but do not auto install` .

Mer information finns i Konfigurera [automatiska uppdateringar.](/windows/deployment/update/waas-wu-settings#configure-automatic-updates)

## <a name="scenario-machine-is-already-registered-to-a-different-account"></a><a name="machine-already-registered"></a>Scenario: Datorn är redan registrerad på ett annat konto

### <a name="issue"></a>Problem

Du får följande felmeddelande:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>Orsak

Datorn har redan distribuerats till en annan arbetsyta för Uppdateringshantering.

### <a name="resolution"></a>Lösning

1. Följ stegen under [Datorer visas inte i portalen under](#nologs) Uppdateringshantering för att kontrollera att datorn rapporterar till rätt arbetsyta.
2. Rensa artefakter på datorn genom att [ta bort Hybrid Runbook-gruppen](../automation-windows-hrw-install.md#remove-a-hybrid-worker-group)och försök sedan igen.

## <a name="scenario-machine-cant-communicate-with-the-service"></a><a name="machine-unable-to-communicate"></a>Scenario: Datorn kan inte kommunicera med tjänsten

### <a name="issue"></a>Problem

Du får något av följande felmeddelanden:

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

En proxy, gateway eller brandvägg kan blockera nätverkskommunikationen.

### <a name="resolution"></a>Lösning

Granska nätverket och kontrollera att lämpliga portar och adresser tillåts. Se [nätverkskrav](../automation-hybrid-runbook-worker.md#network-planning) för en lista över portar och adresser som krävs av Uppdateringshantering och Hybrid Runbook Workers.

## <a name="scenario-unable-to-create-self-signed-certificate"></a><a name="unable-to-create-selfsigned-cert"></a>Scenario: Det går inte att skapa ett själv signerat certifikat

### <a name="issue"></a>Problem

Du får något av följande felmeddelanden:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

### <a name="cause"></a>Orsak

Den Hybrid Runbook Worker kunde inte generera ett själv signerat certifikat.

### <a name="resolution"></a>Lösning

Kontrollera att systemkontot har läsbehörighet till **mappen C:\ProgramData\Microsoft\Crypto\RSA** och försök igen.

## <a name="scenario-the-scheduled-update-failed-with-a-maintenancewindowexceeded-error"></a><a name="mw-exceeded"></a>Scenario: Den schemalagda uppdateringen misslyckades med ett MaintenanceWindowExceeded-fel

### <a name="issue"></a>Problem

Standardunderhållsfönstret för uppdateringar är 120 minuter. Du kan öka underhållsfönstret till högst 6 timmar eller 360 minuter.

### <a name="resolution"></a>Lösning

För att förstå varför detta inträffade under en uppdateringskörning efter att den har startats kontrollerar du [jobbutdata](../update-management/deploy-updates.md#view-results-of-a-completed-update-deployment) från den berörda datorn i körningen. Du kan hitta specifika felmeddelanden från dina datorer som du kan undersöka och vidta åtgärder för.  

Redigera eventuella misslyckade schemalagda uppdateringsdistributioner och öka underhållsfönstret.

Mer information om underhållsfönster finns i [Installera uppdateringar.](../update-management/deploy-updates.md#schedule-an-update-deployment)

## <a name="scenario-machine-shows-as-not-assessed-and-shows-an-hresult-exception"></a><a name="hresult"></a>Scenario: Datorn visas som "Ej utvärderad" och visar ett HRESULT-undantag

### <a name="issue"></a>Problem

* Du har datorer som visas `Not assessed` som under **Efterlevnad** och du ser ett undantagsmeddelande under dem.
* Du ser en HRESULT-felkod i portalen.

### <a name="cause"></a>Orsak

Uppdateringsagenten (Windows Update Agent i Windows, pakethanteraren för en Linux-distribution) är inte korrekt konfigurerad. Uppdateringshantering förlitar sig på datorns uppdateringsagent för att tillhandahålla de uppdateringar som behövs, status för korrigeringen och resultatet av distribuerade korrigeringar. Utan den här informationen Uppdateringshantering inte rapportera om de korrigeringar som behövs eller installeras korrekt.

### <a name="resolution"></a>Lösning

Försök att utföra uppdateringar lokalt på datorn. Om den här åtgärden misslyckas innebär det vanligtvis att det finns ett konfigurationsfel för uppdateringsagenten.

Det här problemet orsakas ofta av problem med nätverkskonfiguration och brandvägg. Använd följande kontroller för att åtgärda problemet.

* För Linux kontrollerar du att du kan nå nätverksslutpunkten för paketdatabasen i relevant dokumentation.

* För Windows kontrollerar du agentkonfigurationen som anges i Uppdateringar laddas inte ned från intranätets slutpunkt [(WSUS/SCCM).](/windows/deployment/update/windows-update-troubleshooting#updates-arent-downloading-from-the-intranet-endpoint-wsussccm)

  * Om datorerna är konfigurerade för Windows Update kontrollerar du att du kan nå de slutpunkter som beskrivs i [Problem som rör HTTP/proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy).
  * Om datorerna är konfigurerade för Windows Server Update Services (WSUS) kontrollerar du att du kan nå WSUS-servern som konfigurerats av [registernyckeln WUServer](/windows/deployment/update/waas-wu-settings).

Om du ser ett HRESULT dubbelklickar du på undantaget som visas i rött för att se hela undantagsmeddelandet. Granska följande tabell för potentiella lösningar eller rekommenderade åtgärder.

|Undantag  |Lösning eller åtgärd  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Sök efter relevant felkod i [listan med Windows Update-felkoder](https://support.microsoft.com/help/938205/windows-update-error-code-list) för att hitta ytterligare information om orsaken till undantaget.        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | Dessa indikerar problem med nätverksanslutningen. Kontrollera att datorn har nätverksanslutning till Uppdateringshantering. En lista [över portar och](../update-management/overview.md#ports) adresser som krävs finns i avsnittet om nätverksplanering.        |
|`0x8024001E`| Uppdateringsåtgärden slutfördes inte eftersom tjänsten eller systemet stängdes av.|
|`0x8024002E`| Windows Update-tjänsten är inaktiverad.|
|`0x8024402C`     | Om du använder en WSUS-server kontrollerar du att registervärdena för och under registernyckeln `WUServer` `WUStatusServer` anger rätt  `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` WSUS-server.        |
|`0x80072EE2`|Det finns ett problem med nätverksanslutningen eller ett problem med att prata med en konfigurerad WSUS-server. Kontrollera WSUS-inställningarna och se till att tjänsten är tillgänglig från klienten.|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Kontrollera att Windows Update (wuauserv) körs och inte är inaktiverad.        |
|`0x80070005`| Ett fel om nekad åtkomst kan orsakas av något av följande:<br> Infekterad dator<br> Windows Update har inte konfigurerats korrekt<br> Filbehörighetsfel med mappen %WinDir%\SoftwareDistribution<br> Otillräckligt diskutrymme på systemenheten (C:).
|Annat allmänt undantag     | Kör en sökning på Internet efter möjliga lösningar och arbeta med din lokala IT-support.         |

Granskning av **filen %Windir%\Windowsupdate.log** kan också hjälpa dig att fastställa möjliga orsaker. Mer information om hur du läser loggen finns i [Så här läser du filen Windowsupdate.log.](https://support.microsoft.com/help/902093/how-to-read-the-windowsupdate-log-file)

Du kan också ladda ned och [köra Windows Update felsökaren](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) för att söka efter eventuella problem Windows Update på datorn.

> [!NOTE]
> [Felsökningsdokumentationen Windows Update](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) anger att den ska användas på Windows-klienter, men att den även fungerar på Windows Server.

## <a name="scenario-update-run-returns-failed-status-linux"></a>Scenario: Uppdateringskörningen returnerar misslyckad status (Linux)

### <a name="issue"></a>Problem

En uppdateringskörning startar men påträffar fel under körningen.

### <a name="cause"></a>Orsak

Möjliga orsaker:

* Pakethanteraren är inte feltillstånd.
* Update Agent (WUA för Windows, distro-specifik pakethanterare för Linux) är felkonfigurerad.
* Vissa paket stör molnbaserade korrigeringar.
* Datorn kan inte nås.
* Uppdateringar hade beroenden som inte löstes.

### <a name="resolution"></a>Lösning

Om fel uppstår under en uppdateringskörning efter att den har startats kontrollerar [du jobbutdata](../update-management/deploy-updates.md#view-results-of-a-completed-update-deployment) från den berörda datorn i körningen. Du kan hitta specifika felmeddelanden från dina datorer som du kan undersöka och vidta åtgärder för. Uppdateringshantering kräver att pakethanteraren är felfri för lyckade uppdateringsdistributioner.

Om specifika korrigeringar, paket eller uppdateringar visas omedelbart innan jobbet [](../update-management/deploy-updates.md#schedule-an-update-deployment) misslyckas kan du försöka att utesluta dessa objekt från nästa uppdateringsdistribution. Information om hur du samlar in logginformation Windows Update finns [i Windows Update loggfiler](/windows/deployment/update/windows-update-logs).

Om du inte kan lösa ett korrigeringsproblem gör du en kopia av **filen /var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log** och bevarar den i felsökningssyfte innan nästa uppdateringsdistribution startar.

## <a name="patches-arent-installed"></a>Korrigeringar har inte installerats

### <a name="machines-dont-install-updates"></a>Datorerna installerar inte uppdateringar

Försök att köra uppdateringar direkt på datorn. Om datorn inte kan tillämpa uppdateringarna kan du läsa listan [över potentiella fel i felsökningsguiden](#hresult).

Om uppdateringar körs lokalt kan du försöka ta bort och installera om agenten på datorn genom att följa riktlinjerna i Ta [bort en virtuell dator från Uppdateringshantering](../update-management/remove-vms.md).

### <a name="i-know-updates-are-available-but-they-dont-show-as-available-on-my-machines"></a>Jag vet att uppdateringar är tillgängliga, men de visas inte som tillgängliga på mina datorer

Detta inträffar ofta om datorer är konfigurerade för att hämta uppdateringar från WSUS eller Microsoft Endpoint Configuration Manager men WSUS och Konfigurationshanteraren inte har godkänt uppdateringarna.

Du kan kontrollera om datorerna är konfigurerade för WSUS och SCCM genom att korsreferensa registernyckeln till registernycklarna i avsnittet Konfigurera automatiska uppdateringar genom att redigera registret i den `UseWUServer` [här artikeln.](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s)

Om uppdateringar inte godkänns i WSUS installeras de inte. Du kan söka efter ej godkända uppdateringar i Log Analytics genom att köra följande fråga.

  ```kusto
  Update | where UpdateState == "Needed" and ApprovalSource == "WSUS" and Approved == "False" | summarize max(TimeGenerated) by Computer, KBID, Title
  ```

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>Uppdateringar visas som installerade, men jag hittar dem inte på datorn

Uppdateringar ersätts ofta av andra uppdateringar. Mer information finns i [Uppdatera ersätts i Windows Update](/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer) felsökningsguiden.

### <a name="installing-updates-by-classification-on-linux"></a>Installera uppdateringar efter klassificering i Linux

Distributionen av uppdateringar till Linux efter klassificering (”kritiska uppdateringar och säkerhetsuppdateringar”) har viktiga förbehåll, särskilt för CentOS. Dessa begränsningar dokumenteras på [Uppdateringshantering översiktssidan](../update-management/overview.md#linux).

### <a name="kb2267602-is-consistently-missing"></a>KB2267602 saknas konsekvent

KB2267602 är [Windows Defender-definitionsuppdateringen](https://www.microsoft.com/wdsi/definitions). Den uppdateras dagligen.

## <a name="next-steps"></a>Nästa steg

Om du inte ser ditt problem eller inte kan lösa problemet kan du prova någon av följande kanaler för ytterligare support.

* Få svar från Azure-experter via [Azure-forumen.](https://azure.microsoft.com/support/forums/)
* Anslut med [@AzureSupport](https://twitter.com/azuresupport) , det officiella Microsoft Azure för att förbättra kundupplevelsen.
* Skapa en azure-supportincident. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/) och välj **Få support.**
