---
title: Översikt över Azure Automation Starta/stoppa virtuella datorer när de inte används
description: I den här artikeln beskrivs Starta/stoppa virtuella datorer när de inte används funktionen som startar eller stoppar virtuella datorer enligt ett schema och övervakar dem proaktivt från Azure Monitor loggar.
services: automation
ms.subservice: process-automation
ms.date: 02/04/2020
ms.topic: conceptual
ms.openlocfilehash: e58f63b6ed7fb26a4e3b3069773810c5e5b7cdc3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101732283"
---
# <a name="startstop-vms-during-off-hours-overview"></a>Översikt över Starta/stoppa virtuella datorer när de inte används

Starta/stoppa virtuella datorer när de inte används funktionen startar eller stoppar aktiverade virtuella Azure-datorer. Den startar eller stoppar datorer enligt användardefinierade scheman, ger insikter via Azure Monitor loggar och skickar valfria e-postmeddelanden med hjälp av [Åtgärds grupper](../azure-monitor/alerts/action-groups.md). Funktionen kan aktive ras på både Azure Resource Manager och klassiska virtuella datorer för de flesta scenarier. 

Den här funktionen använder cmdleten [Start-AzVm](/powershell/module/az.compute/start-azvm) för att starta virtuella datorer. Den använder [Stop-AzVM](/powershell/module/az.compute/stop-azvm) för att stoppa virtuella datorer.

> [!NOTE]
> Medan Runbooks har uppdaterats för att använda de nya cmdletarna för Azure AZ-modulen använder de AzureRM-prefixet.

> [!NOTE]
> Starta/stoppa virtuella datorer när de inte används har uppdaterats till att stödja de senaste versionerna av de Azure-moduler som är tillgängliga. Den uppdaterade versionen av den här funktionen, som är tillgänglig i Marketplace, stöder inte AzureRM-moduler eftersom vi har migrerat från AzureRM till AZ-moduler.

Funktionen ger ett decentraliserat alternativ för låg kostnads automatisering för användare som vill optimera sina VM-kostnader. Du kan använda funktionen för att:

- [Schemalägg virtuella datorer för start och stopp](automation-solution-vm-management-config.md#schedule).
- Schemalägg virtuella datorer för att starta och stoppa i stigande ordning med [hjälp av Azure-Taggar](automation-solution-vm-management-config.md#tags). Den här aktiviteten stöds inte för klassiska virtuella datorer.
- Stoppa virtuella datorer för virtuella datorer baserat på [låg CPU-användning](automation-solution-vm-management-config.md#cpuutil).

Följande är begränsningar med den aktuella funktionen:

- Den hanterar virtuella datorer i vilken region som helst, men kan bara användas i samma prenumeration som ditt Azure Automation-konto.
- Den är tillgänglig i Azure och Azure Government för alla regioner som stöder en Log Analytics arbets yta, ett Azure Automation-konto och aviseringar. Azure Government regioner stöder för närvarande inte e-postfunktioner.

## <a name="prerequisites"></a>Förutsättningar

- Runbooks för funktionen starta/stoppa virtuella datorer under låg tid fungerar med ett [Kör som-konto i Azure](./automation-security-overview.md#run-as-accounts). Kör som-kontot är den föredragna autentiseringsmetoden eftersom den använder certifikatautentisering i stället för ett lösen ord som kan gå ut eller ändras ofta.

- En [Azure Monitor Log Analytics arbets yta](../azure-monitor/logs/design-logs-deployment.md) som lagrar Runbook-jobbets loggar och jobb Ströms resultat i en arbets yta för att fråga och analysera. Automation-kontot kan länkas till en ny eller befintlig Log Analytics-arbetsyta och båda resurserna måste finnas i samma resurs grupp.

Vi rekommenderar att du använder ett separat Automation-konto för att arbeta med virtuella datorer som är aktiverade för Starta/stoppa virtuella datorer när de inte används funktionen. Azure-modulernas versioner uppgraderas ofta och deras parametrar kan ändras. Funktionen uppgraderas inte på samma takt och fungerar kanske inte med nyare versioner av de cmdletar som används. Innan du importerar de uppdaterade modulerna till dina produktions Automation-konton rekommenderar vi att du importerar dem till ett test Automation-konto för att kontrol lera att det inte finns några kompatibilitetsproblem.

## <a name="permissions"></a>Behörigheter

Du måste ha vissa behörigheter för att aktivera virtuella datorer för den Starta/stoppa virtuella datorer när de inte används funktionen. Behörigheterna skiljer sig åt beroende på om funktionen använder ett befintligt Automation-konto och Log Analytics arbets ytan eller skapar ett nytt konto och en arbets yta.

Du behöver inte konfigurera behörigheter om du är deltagare i prenumerationen och en global administratör i din Azure Active Directory (AD)-klient. Om du inte har dessa rättigheter eller behöver konfigurera en anpassad roll kontrollerar du att du har de behörigheter som beskrivs nedan.

### <a name="permissions-for-pre-existing-automation-account-and-log-analytics-workspace"></a>Behörigheter för redan befintligt Automation-konto och Log Analytics arbets yta

Om du vill aktivera virtuella datorer för Starta/stoppa virtuella datorer när de inte används funktionen med ett befintligt Automation-konto och Log Analytics arbets yta, behöver du följande behörigheter för resurs grupps omfånget. Mer information om roller finns i [Azure-anpassade roller](../role-based-access-control/custom-roles.md).

| Behörighet | Omfång|
| --- | --- |
| Microsoft. Automation/automationAccounts/Read | Resursgrupp |
| Microsoft. Automation/automationAccounts/variabler/Write | Resursgrupp |
| Microsoft. Automation/automationAccounts/-scheman/-skrivning | Resursgrupp |
| Microsoft. Automation/automationAccounts/Runbooks/Write | Resursgrupp |
| Microsoft. Automation/automationAccounts/Connections/Write | Resursgrupp |
| Microsoft. Automation/automationAccounts/certificates/Write | Resursgrupp |
| Microsoft. Automation/automationAccounts/modules/-skrivning | Resursgrupp |
| Microsoft. Automation/automationAccounts/modules/läsa | Resursgrupp |
| Microsoft. Automation/automationAccounts/jobSchedules/Write | Resursgrupp |
| Microsoft. Automation/automationAccounts/Jobs/Write | Resursgrupp |
| Microsoft. Automation/automationAccounts/Jobs/Read | Resursgrupp |
| Microsoft. OperationsManagement/lösningar/Skriv | Resursgrupp |
| Microsoft. OperationalInsights/arbets ytor/* | Resursgrupp |
| Microsoft. Insights/diagnosticSettings/Write | Resursgrupp |
| Microsoft. Insights/ActionGroups/Write | Resursgrupp |
| Microsoft. Insights/ActionGroups/Read | Resursgrupp |
| Microsoft. Resources/Subscriptions/resourceGroups/Read | Resursgrupp |
| Microsoft. Resources/Deployments/* | Resursgrupp |

### <a name="permissions-for-new-automation-account-and-new-log-analytics-workspace"></a>Behörigheter för det nya Automation-kontot och arbets ytan ny Log Analytics

Du kan aktivera virtuella datorer för Starta/stoppa virtuella datorer när de inte används-funktionen med ett nytt Automation-konto och Log Analytics arbets yta. I det här fallet behöver du de behörigheter som definierats i föregående avsnitt samt de behörigheter som definierats i det här avsnittet. Du behöver också följande roller:

- Co-Administrator prenumerationen. Den här rollen krävs för att skapa det klassiska kör som-kontot om du ska hantera klassiska virtuella datorer. [Klassiska kör som-konton](automation-create-standalone-account.md#create-a-classic-run-as-account) skapas inte längre som standard.
- Medlemskap i rollen [Azure AD](../active-directory/roles/permissions-reference.md) Application Developer. Mer information om hur du konfigurerar kör som-konton finns i [behörighet att konfigurera kör som-konton](automation-security-overview.md#permissions).
- Deltagare i prenumerationen eller följande behörigheter.

| Behörighet |Omfång|
| --- | --- |
| Microsoft. Authorization/Operations/Read | Prenumeration|
| Microsoft. Authorization/Permissions/Read |Prenumeration|
| Microsoft. Authorization/roleAssignments/Read | Prenumeration |
| Microsoft.Authorization/roleAssignments/write | Prenumeration |
| Microsoft. Authorization/roleAssignments/Delete | Prenumeration || Microsoft. Automation/automationAccounts/Connections/Read | Resursgrupp |
| Microsoft. Automation/automationAccounts/certifikat/läsa | Resursgrupp |
| Microsoft. Automation/automationAccounts/Write | Resursgrupp |
| Microsoft. OperationalInsights/arbets ytor/Skriv | Resursgrupp |

## <a name="components"></a>Komponenter

I den Starta/stoppa virtuella datorer när de inte används funktionen ingår förkonfigurerade Runbooks, scheman och integrering med Azure Monitor loggar. Du kan använda dessa element för att skräddarsy Start och avstängning av dina virtuella datorer så att de passar dina affärs behov.

### <a name="runbooks"></a>Runbooks

I följande tabell visas de Runbooks som funktionen distribuerar till ditt Automation-konto. Gör inte ändringar i Runbook-koden. Skriv i stället din egen Runbook för nya funktioner.

> [!IMPORTANT]
> Kör inte någon Runbook med det **underordnade** tillägget till sitt namn direkt.

Alla överordnade Runbooks inkluderar `WhatIf` parametern. När värdet är true, stöder parametern information om det exakta beteende som Runbook tar när den körs utan parametern och kontrollerar att rätt virtuella datorer är riktade. En Runbook utför bara sina definierade åtgärder när `WhatIf` parametern har angetts till false.

|Runbook | Parametrar | Beskrivning|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | Anropas från den överordnade runbooken. Denna Runbook skapar aviseringar per resurs för det automatiska stopp scenariot.|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf: true eller false  | Skapar eller uppdaterar Azures aviserings regler på virtuella datorer i mål prenumerationen eller resurs grupperna. <br> `VMList` är en kommaavgränsad lista över virtuella datorer (utan blank steg), till exempel `vm1,vm2,vm3` .<br> `WhatIf` aktiverar validering av Runbook-logik utan att köra.|
|AutoStop_Disable | Inget | Inaktiverar automatiska stopp-aviseringar och standard schema.|
|AutoStop_VM_Child | WebHookData | Anropas från den överordnade runbooken. Aviserings regler anropar denna Runbook för att stoppa en klassisk virtuell dator.|
|AutoStop_VM_Child_ARM | WebHookData |Anropas från den överordnade runbooken. Aviserings regler anropar denna Runbook för att stoppa en virtuell dator.  |
|ScheduledStartStop_Base_Classic | CloudServiceName<br> Åtgärd: starta eller stoppa<br> VMList  | Utför åtgärden starta eller stoppa i den klassiska VM-gruppen genom att Cloud Services. |
|ScheduledStartStop_Child | VMName <br> Åtgärd: starta eller stoppa <br> ResourceGroupName | Anropas från den överordnade runbooken. Kör en start-eller stopp åtgärd för det schemalagda steget.|
|ScheduledStartStop_Child_Classic | VMName<br> Åtgärd: starta eller stoppa<br> ResourceGroupName | Anropas från den överordnade runbooken. Kör en start-eller stopp åtgärd för det schemalagda stoppet för klassiska virtuella datorer. |
|ScheduledStartStop_Parent | Åtgärd: starta eller stoppa <br>VMList <br> WhatIf: true eller false | Startar eller stoppar alla virtuella datorer i prenumerationen. Redigera variablerna `External_Start_ResourceGroupNames` och `External_Stop_ResourceGroupNames` för att endast köra på dessa mål resurs grupper. Du kan också undanta vissa virtuella datorer genom att uppdatera `External_ExcludeVMNames` variabeln.|
|SequencedStartStop_Parent | Åtgärd: starta eller stoppa <br> WhatIf: true eller false<br>VMList| Skapar taggar med namnet **sequencestart** och **sequencestop** på varje virtuell dator för vilken du vill starta/stoppa aktivitet. Dessa taggnamn är Skift läges känsliga. Värdet för taggen ska vara en lista med positiva heltal, till exempel `1,2,3` , som motsvarar den ordning som du vill starta eller stoppa. <br>**Obs!** virtuella datorer måste finnas i resurs grupper som har definierats i `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` `External_ExcludeVMNames` variablerna, och. De måste ha lämpliga taggar för att åtgärder ska börja gälla.|

### <a name="variables"></a>Variabler

I följande tabell visas variablerna som skapas i ditt Automation-konto. Ändra endast variabler som föregås av `External` . Om du ändrar variabler med prefixet får du `Internal` oönskade effekter.

> [!NOTE]
> Begränsningar i VM-namn och resurs grupp är i stort sett resultatet av varierande storlek. Se [variabel till gångar i Azure Automation](./shared-resources/variables.md).

|Variabel | Beskrivning|
|---------|------------|
|External_AutoStop_Condition | Den villkorliga operator som krävs för att konfigurera villkoret innan en avisering utlöses. Godkända värden är `GreaterThan` , `GreaterThanOrEqual` , `LessThan` och `LessThanOrEqual` .|
|External_AutoStop_Description | Aviseringen för att stoppa den virtuella datorn om PROCESSORns procents ATS överstiger tröskelvärdet.|
|External_AutoStop_Frequency | Utvärderings frekvensen för regeln. Den här parametern accepterar inmatade i TimeSpan-format. Möjliga värden är mellan 5 och 6 timmar. |
|External_AutoStop_MetricName | Namnet på det prestanda mått som Azure-aviserings regeln ska konfigureras för.|
|External_AutoStop_Severity | Allvarlighets grad för måttets avisering, som kan vara mellan 0 och 4. |
|External_AutoStop_Threshold | Tröskelvärdet för den Azure Alert-regel som anges i variabeln `External_AutoStop_MetricName` . Procent värden sträcker sig från 1 till 100.|
|External_AutoStop_TimeAggregationOperator | Tids mängds operatorn som används för den valda fönster storleken för att utvärdera villkoret. Godkända värden är `Average` ,,, `Minimum` `Maximum` `Total` och `Last` .|
|External_AutoStop_TimeWindow | Storleken på fönstret där Azure analyserar valda mått för att utlösa en avisering. Den här parametern accepterar inmatade i TimeSpan-format. Möjliga värden är mellan 5 och 6 timmar.|
|External_EnableClassicVMs| Värde som anger om de klassiska virtuella datorerna är mål för funktionen. Standardvärdet är true. Ställ in den här variabeln på falskt för Azure Cloud Solution Provider (CSP)-prenumerationer. Klassiska virtuella datorer kräver ett [klassiskt kör som-konto](automation-create-standalone-account.md#create-a-classic-run-as-account).|
|External_ExcludeVMNames | Kommaavgränsad lista över VM-namn som ska undantas, begränsade till 140 virtuella datorer. Om du lägger till fler än 140 virtuella datorer i listan kan virtuella datorer som anges som undantag oavsiktligt startas eller stoppas.|
|External_Start_ResourceGroupNames | Kommaavgränsad lista över en eller flera resurs grupper som är avsedda för start åtgärder.|
|External_Stop_ResourceGroupNames | Kommaavgränsad lista över en eller flera resurs grupper som är avsedda för stopp åtgärder.|
|External_WaitTimeForVMRetrySeconds |Vänte tiden i sekunder för de åtgärder som ska utföras på de virtuella datorerna för **SequencedStartStop_Parent** Runbook. Med den här variabeln kan runbooken vänta på underordnade åtgärder under ett angivet antal sekunder innan nästa åtgärd fortsätter. Maximal vänte tid är 10800 eller tre timmar. Standardvärdet är 2100 sekunder.|
|Internal_AutomationAccountName | Anger namnet på Automation-kontot.|
|Internal_AutoSnooze_ARM_WebhookURI | Webhook-URI: n anropade för scenariot för autostopp för virtuella datorer.|
|Internal_AutoSnooze_WebhookUri | Webhook-URI: n anropade för scenariot för autostop för klassiska virtuella datorer.|
|Internal_AzureSubscriptionId | ID för Azure-prenumerationen.|
|Internal_ResourceGroupName | Namnet på resurs gruppen för Automation-kontot.|

>[!NOTE]
>`External_WaitTimeForVMRetryInSeconds`Standardvärdet har uppdaterats från 600 till 2100 för variabeln.

I alla scenarier, variablerna `External_Start_ResourceGroupNames` ,  `External_Stop_ResourceGroupNames` och `External_ExcludeVMNames` är nödvändiga för att rikta in virtuella datorer, förutom de kommaavgränsade VM-listorna för **AutoStop_CreateAlert_Parent**, **SequencedStartStop_Parent** och **ScheduledStartStop_Parent** Runbooks. Det innebär att de virtuella datorerna måste tillhöra mål resurs grupper för att start-och stopp åtgärder ska inträffa. Logiken fungerar ungefär som Azure Policy, i så att du kan rikta prenumerationen eller resurs gruppen och ha åtgärder som ärvts av nyskapade virtuella datorer. Den här metoden gör att du inte behöver ha ett separat schema för varje virtuell dator och hantera startar och stoppas i stor skala.

### <a name="schedules"></a>Scheman

I följande tabell visas alla standard scheman som skapats i ditt Automation-konto. Du kan ändra dem eller skapa egna anpassade scheman. Som standard inaktive ras alla scheman förutom **Scheduled_StartVM** och **Scheduled_StopVM** scheman.

Aktivera inte alla scheman, eftersom detta kan skapa överlappande schema åtgärder. Det är bäst att bestämma vilka optimeringar du vill göra och ändra dem på lämpligt sätt. I exempel scenarierna i översikts avsnittet finns ytterligare förklaring.

|Schema namn | Frekvens | Beskrivning|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | Var 8:e timme | Kör **AutoStop_CreateAlert_Parent** Runbook var 8: e timme, vilket i sin tur stoppar de VM-baserade värdena i `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` `External_ExcludeVMNames` variablerna, och. Alternativt kan du ange en kommaavgränsad lista över virtuella datorer med hjälp av- `VMList` parametern.|
|Scheduled_StopVM | Användardefinierad, daglig | Kör **ScheduledStopStart_Parent** Runbook med en parameter på `Stop` varje dag vid den angivna tiden. Stoppar automatiskt alla virtuella datorer som uppfyller reglerna som definierats av variabel till gångar. Aktivera det **schemalagda schemat för schemalagda StartVM**.|
|Scheduled_StartVM | Användardefinierad, daglig | Kör **ScheduledStopStart_Parent** Runbook med ett parameter värde på `Start` varje dag vid den angivna tiden. Startar automatiskt alla virtuella datorer som uppfyller reglerna som definierats av variabel till gångar. Aktivera det **schemalagda schemat för schemalagda StopVM**.|
|Sequenced-StopVM | 1:00 AM (UTC), varje fredag | Kör **Sequenced_StopStop_Parent** Runbook med ett parameter värde på `Stop` varje fredag vid den angivna tiden. I tur och ordning stoppas alla virtuella datorer med en tagg **SequenceStop** som definieras av lämpliga variabler. Mer information om tagg värden och till gångs variabler finns i [Runbooks](#runbooks). Aktivera relaterat schema, **sekvenserat-StartVM**.|
|Sequenced-StartVM | 1:00 PM (UTC), varje måndag | Kör **SequencedStopStart_Parent** Runbook med ett parameter värde på `Start` varje måndag vid den angivna tiden. Sekventiellt (fallande) startar alla virtuella datorer med en tagg för **SequenceStart** som definieras av lämpliga variabler. Mer information om tagg värden och variabel till gångar finns i [Runbooks](#runbooks). Aktivera relaterat schema, **sekvenserat-StopVM**.

## <a name="use-the-feature-with-classic-vms"></a>Använda funktionen med klassiska virtuella datorer

Om du använder Starta/stoppa virtuella datorer när de inte används funktionen för klassiska virtuella datorer, bearbetar Automation alla virtuella datorer sekventiellt per moln tjänst. Virtuella datorer bearbetas fortfarande parallellt över olika moln tjänster. 

För att kunna använda funktionen med klassiska virtuella datorer behöver du ett klassiskt kör som-konto, som inte skapas som standard. Anvisningar om hur du skapar ett klassiskt kör som-konto finns i [skapa ett klassiskt kör som-konto](automation-create-standalone-account.md#create-a-classic-run-as-account).

Om du har fler än 20 virtuella datorer per moln tjänst är här några rekommendationer:

* Skapa flera scheman med den överordnade Runbook- **ScheduledStartStop_Parent** och ange 20 virtuella datorer per schema.
* I schema egenskaper använder du `VMList` parametern för att ange namn på virtuella datorer som en kommaavgränsad lista (inga blank steg).

Annars, om automatiserings jobbet för den här funktionen körs mer än tre timmar, tas det tillfälligt bort eller stoppas enligt den [verkliga delnings](automation-runbook-execution.md#fair-share) gränsen.

Azure CSP-prenumerationer stöder endast Azure Resource Managers modellen. Icke-Azure Resource Manager tjänster är inte tillgängliga i programmet. När Starta/stoppa virtuella datorer när de inte används funktionen körs kan du få fel eftersom den innehåller cmdlets för att hantera klassiska resurser. Läs mer om CSP i [tillgängliga tjänster i CSP-prenumerationer](/azure/cloud-solution-provider/overview/azure-csp-available-services). Om du använder en CSP-prenumeration ska du ange [External_EnableClassicVMs](#variables) variabeln till falskt efter distributionen.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="view-the-feature"></a>Visa funktionen

Använd någon av följande metoder för att få åtkomst till den aktiverade funktionen:

* Från ditt Automation-konto väljer du **Starta/stoppa virtuell dator** under **relaterade resurser**. På sidan Starta/stoppa virtuell dator väljer du **Hantera lösningen** under **Hantera lösningar för att starta/stoppa virtuella datorer**.

* Navigera till den Log Analytics arbets ytan som är länkad till ditt Automation-konto. När du har valt arbets ytan väljer du **lösningar** i det vänstra fönstret. På sidan lösningar väljer du **Start-Stop-VM [arbets yta]** i listan.  

Om du väljer funktionen visas sidan **Start-Stop-VM [Workspace]** . Här kan du granska viktig information, till exempel informationen på **StartStopVM** -panelen. Precis som i arbets ytan Log Analytics visar den här panelen ett antal och en grafisk representation av Runbook-jobben för funktionen som har startat och har slutförts.

![Sidan Automation Uppdateringshantering](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

Du kan utföra ytterligare analyser av jobb posterna genom att klicka på Ring panelen. Instrument panelen visar jobb historik och fördefinierade logg Sök frågor. Växla till logganalys-portalen i Log Analytics om du vill söka utifrån dina Sök frågor.

## <a name="update-the-feature"></a>Uppdatera funktionen

Om du har distribuerat en tidigare version av Starta/stoppa virtuella datorer när de inte används tar du bort den från ditt konto innan du distribuerar en uppdaterad version. Följ stegen för att [ta bort funktionen](automation-solution-vm-management-remove.md#delete-the-feature) och följ sedan stegen för att [Aktivera den](automation-solution-vm-management-enable.md).

## <a name="next-steps"></a>Nästa steg

Om du vill aktivera funktionen på virtuella datorer i din miljö, se [aktivera starta/stoppa virtuella datorer när de inte används](automation-solution-vm-management-enable.md).