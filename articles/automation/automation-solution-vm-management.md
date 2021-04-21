---
title: Azure Automation Starta/stoppa virtuella datorer när de inte används översikt
description: Den här artikeln beskriver Starta/stoppa virtuella datorer när de inte används funktion som startar eller stoppar virtuella datorer enligt ett schema och proaktivt övervakar dem från Azure Monitor Loggar.
services: automation
ms.subservice: process-automation
ms.date: 02/04/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b28367aa242d5fab71dc5046ff6188c634883f03
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834524"
---
# <a name="startstop-vms-during-off-hours-overview"></a>Starta/stoppa virtuella datorer när de inte används översikt

Funktionen Starta/stoppa virtuella datorer när de inte används startar eller stoppar aktiverade virtuella Azure-datorer. Den startar eller stoppar datorer enligt användardefinierade scheman, ger insikter via Azure Monitor loggar och skickar valfria e-postmeddelanden med hjälp av [åtgärdsgrupper](../azure-monitor/alerts/action-groups.md). Funktionen kan aktiveras på både virtuella Azure Resource Manager och klassiska för de flesta scenarier.

Den här funktionen [använder cmdleten Start-AzVm](/powershell/module/az.compute/start-azvm) för att starta virtuella datorer. Den använder [Stop-AzVM för](/powershell/module/az.compute/stop-azvm) att stoppa virtuella datorer.

> [!NOTE]
> Runbooks har uppdaterats för att använda de nya Cmdletarna för Azure Az-modulen, men de använder prefixalias för AzureRM.

> [!NOTE]
> Starta/stoppa virtuella datorer när de inte används har uppdaterats för att stödja de senaste versionerna av de Azure-moduler som är tillgängliga. Den uppdaterade versionen av den här funktionen, som finns på Marketplace, stöder inte AzureRM-moduler eftersom vi har migrerat från AzureRM till Az-moduler.

Funktionen ger ett decentraliserat automatiseringsalternativ till låg kostnad för användare som vill optimera sina kostnader för virtuella datorer. Du kan använda funktionen för att:

- [Schemalägg virtuella datorer att starta och stoppa](automation-solution-vm-management-config.md#schedule).
- Schemalägg virtuella datorer att starta och stoppa i stigande ordning med hjälp [av Azure-taggar](automation-solution-vm-management-config.md#tags). Den här aktiviteten stöds inte för klassiska virtuella datorer.
- Automatisk allokering av virtuella datorer baserat på [låg CPU-användning.](automation-solution-vm-management-config.md#cpuutil)

Följande begränsningar gäller för den aktuella funktionen:

- Den hanterar virtuella datorer i valfri region, men kan bara användas i samma prenumeration som ditt Azure Automation konto.
- Den är tillgänglig i Azure och Azure Government för alla regioner som stöder en Log Analytics-arbetsyta, ett Azure Automation-konto och aviseringar. Azure Government har för närvarande inte stöd för e-postfunktioner.

> [!NOTE]
> Innan du installerar den här versionen vill vi att du känner till [nästa version](https://github.com/microsoft/startstopv2-deployments), som är i förhandsversion just nu.  Den här nya versionen (V2) har samma funktioner som den här, men är utformad för att dra nytta av nyare teknik i Azure. Den lägger till några av de vanliga begärda funktionerna från kunder, till exempel stöd för flera prenumerationer från en enda Start/Stop-instans.

## <a name="prerequisites"></a>Förutsättningar

- Runbooks för funktionen Starta/stoppa virtuella datorer utanför arbetstid fungerar med ett [Kör som-konto i Azure.](./automation-security-overview.md#run-as-accounts) Kör som-kontot är den önskade autentiseringsmetoden eftersom den använder certifikatautentisering i stället för ett lösenord som kan upphöra att gälla eller ändras ofta.

- En [Azure Monitor Log Analytics-arbetsyta](../azure-monitor/logs/design-logs-deployment.md) som lagrar runbook-jobbloggarna och jobbströmmen resulterar i en arbetsyta för att fråga och analysera. Automation-kontot kan länkas till en ny eller befintlig Log Analytics-arbetsyta och båda resurserna måste finnas i samma resursgrupp.

Vi rekommenderar att du använder ett separat Automation-konto för att arbeta med virtuella datorer som är aktiverade Starta/stoppa virtuella datorer när de inte används funktionen. Azure-modulversioner uppgraderas ofta och deras parametrar kan ändras. Funktionen uppgraderas inte i samma takt och kanske inte fungerar med nyare versioner av de cmdlets som den använder. Innan du importerar de uppdaterade modulerna till dina Automation-produktionskonto rekommenderar vi att du importerar dem till ett Automation-testkonto för att kontrollera att det inte finns några kompatibilitetsproblem.

## <a name="permissions"></a>Behörigheter

Du måste ha vissa behörigheter för att aktivera virtuella datorer för Starta/stoppa virtuella datorer när de inte används-funktionen. Behörigheterna är olika beroende på om funktionen använder ett i förväg skapat Automation-konto och En Log Analytics-arbetsyta eller skapar ett nytt konto och en ny arbetsyta.

Du behöver inte konfigurera behörigheter om du är deltagare i prenumerationen och global administratör i din Azure Active Directory klientorganisation (AD). Om du inte har dessa rättigheter eller behöver konfigurera en anpassad roll kontrollerar du att du har de behörigheter som beskrivs nedan.

### <a name="permissions-for-pre-existing-automation-account-and-log-analytics-workspace"></a>Behörigheter för befintligt Automation-konto och Log Analytics-arbetsyta

Om du vill aktivera virtuella datorer för Starta/stoppa virtuella datorer när de inte används-funktionen med ett befintligt Automation-konto och En Log Analytics-arbetsyta behöver du följande behörigheter för resursgruppsomfånget. Mer information om roller finns i [Anpassade Azure-roller.](../role-based-access-control/custom-roles.md)

| Behörighet | Omfång|
| --- | --- |
| Microsoft.Automation/automationAccounts/read | Resursgrupp |
| Microsoft.Automation/automationAccounts/variables/write | Resursgrupp |
| Microsoft.Automation/automationAccounts/schedules/write | Resursgrupp |
| Microsoft.Automation/automationAccounts/runbooks/write | Resursgrupp |
| Microsoft.Automation/automationAccounts/connections/write | Resursgrupp |
| Microsoft.Automation/automationAccounts/certificates/write | Resursgrupp |
| Microsoft.Automation/automationAccounts/modules/write | Resursgrupp |
| Microsoft.Automation/automationAccounts/modules/read | Resursgrupp |
| Microsoft.automation/automationAccounts/jobSchedules/write | Resursgrupp |
| Microsoft.Automation/automationAccounts/jobs/write | Resursgrupp |
| Microsoft.Automation/automationAccounts/jobs/read | Resursgrupp |
| Microsoft.OperationsManagement/solutions/write | Resursgrupp |
| Microsoft.OperationalInsights/workspaces/* | Resursgrupp |
| Microsoft.Insights/diagnosticSettings/write | Resursgrupp |
| Microsoft.Insights/ActionGroups/Write | Resursgrupp |
| Microsoft.Insights/ActionGroups/read | Resursgrupp |
| Microsoft.Resources/subscriptions/resourceGroups/read | Resursgrupp |
| Microsoft.Resources/deployments/* | Resursgrupp |

### <a name="permissions-for-new-automation-account-and-new-log-analytics-workspace"></a>Behörigheter för nytt Automation-konto och ny Log Analytics-arbetsyta

Du kan aktivera virtuella datorer för funktionen Starta/stoppa virtuella datorer när de inte används med ett nytt Automation-konto och en Log Analytics-arbetsyta. I det här fallet behöver du de behörigheter som definierats i föregående avsnitt samt de behörigheter som definierats i det här avsnittet. Du behöver också följande roller:

- Co-Administrator prenumeration. Den här rollen krävs för att skapa det klassiska Kör som-kontot om du ska hantera klassiska virtuella datorer. [Klassiska Kör som-konton](automation-create-standalone-account.md#create-a-classic-run-as-account) skapas inte längre som standard.
- Medlemskap i [azure AD-programutvecklarrollen.](../active-directory/roles/permissions-reference.md) Mer information om hur du konfigurerar Kör som-konton finns i [Behörigheter för att konfigurera Kör som-konton.](automation-security-overview.md#permissions)
- Deltagare i prenumerationen eller följande behörigheter.

| Behörighet |Omfång|
| --- | --- |
| Microsoft.Authorization/Operations/read | Prenumeration|
| Microsoft.Authorization/permissions/read |Prenumeration|
| Microsoft.Authorization/roleAssignments/read | Prenumeration |
| Microsoft.Authorization/roleAssignments/write | Prenumeration |
| Microsoft.Authorization/roleAssignments/delete | Prenumeration |
| Microsoft.Automation/automationAccounts/connections/read | Resursgrupp |
| Microsoft.Automation/automationAccounts/certificates/read | Resursgrupp |
| Microsoft.Automation/automationAccounts/write | Resursgrupp |
| Microsoft.OperationalInsights/workspaces/write | Resursgrupp |

## <a name="components"></a>Komponenter

Funktionen Starta/stoppa virtuella datorer när de inte används omfattar förkonfigurerade runbooks, scheman och integrering med Azure Monitor loggar. Du kan använda de här elementen för att skräddarsy start och avstängning av dina virtuella datorer så att de passar dina affärsbehov.

### <a name="runbooks"></a>Runbooks

I följande tabell visas de runbooks som funktionen distribuerar till ditt Automation-konto. Gör INTE ändringar i runbook-koden. Skriv i stället en egen runbook för nya funktioner.

> [!IMPORTANT]
> Kör inte någon runbook direkt med **underordnad** tillagd i namnet.

Alla överordnade runbooks innehåller `WhatIf` parametern . När det är inställt på True (Sant) stöder parametern information om det exakta beteende som runbooken tar vid körning utan parametern och verifierar att rätt virtuella datorer är mål. En runbook utför bara sina definierade åtgärder när `WhatIf` parametern är inställd på False.

|Runbook | Parametrar | Description|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | Anropas från den överordnade runbooken. Den här runbooken skapar aviseringar per resurs för scenariot Auto-Stop.|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf: True eller False  | Skapar eller uppdaterar Azure-aviseringsregler på virtuella datorer i målprenumerationen eller resursgrupperna. <br> `VMList` är en kommaavgränsad lista över virtuella datorer (utan blanksteg), till exempel `vm1,vm2,vm3` .<br> `WhatIf` aktiverar validering av runbook-logik utan att köra.|
|AutoStop_Disable | Inget | Inaktiverar aviseringar för automatiskt stopp och standardschema.|
|AutoStop_VM_Child | WebHookData | Anropas från den överordnade runbooken. Aviseringsregler anropar denna runbook för att stoppa en klassisk virtuell dator.|
|AutoStop_VM_Child_ARM | WebHookData |Anropas från den överordnade runbooken. Aviseringsregler anropar denna runbook för att stoppa en virtuell dator.  |
|ScheduledStartStop_Base_Classic | CloudServiceName<br> Åtgärd: Starta eller stoppa<br> VMList  | Utför åtgärden starta eller stoppa i klassisk VM-grupp genom att Cloud Services. |
|ScheduledStartStop_Child | VMName <br> Åtgärd: Starta eller stoppa <br> ResourceGroupName | Anropas från den överordnade runbooken. Kör en start- eller stoppåtgärd för det schemalagda stoppet.|
|ScheduledStartStop_Child_Classic | VMName<br> Åtgärd: Starta eller stoppa<br> ResourceGroupName | Anropas från den överordnade runbooken. Kör en start- eller stoppåtgärd för det schemalagda stoppet för klassiska virtuella datorer. |
|ScheduledStartStop_Parent | Åtgärd: Starta eller stoppa <br>VMList <br> WhatIf: True eller False | Startar eller stoppar alla virtuella datorer i prenumerationen. Redigera variablerna `External_Start_ResourceGroupNames` och så att de endast körs på dessa `External_Stop_ResourceGroupNames` målresursgrupper. Du kan också undanta specifika virtuella datorer genom att uppdatera `External_ExcludeVMNames` variabeln.|
|SequencedStartStop_Parent | Åtgärd: Starta eller stoppa <br> WhatIf: True eller False<br>VMList| Skapar taggar med **namnet sequencestart** **och sequencestop** på varje virtuell dator som du vill sekvensa start-/stoppaktivitet för. Dessa taggnamn är fallkänsliga. Värdet för taggen ska vara en lista över positiva heltal, till exempel , som motsvarar den ordning som `1,2,3` du vill starta eller stoppa. <br>**Obs!** Virtuella datorer måste finnas i resursgrupper som `External_Start_ResourceGroupNames` definierats i `External_Stop_ResourceGroupNames` variablerna , `External_ExcludeVMNames` och . De måste ha lämpliga taggar för att åtgärder ska gälla.|

### <a name="variables"></a>Variabler

I följande tabell visas variablerna som skapats i ditt Automation-konto. Ändra endast variabler med prefixet `External` . Att ändra variabler med prefixet `Internal` orsakar oönskade effekter.

> [!NOTE]
> Begränsningar för namn på virtuella datorer och resursgrupper beror till stor del på variabelstorleken. Se [Variabeltillgångar i Azure Automation](./shared-resources/variables.md).

|Variabel | Beskrivning|
|---------|------------|
|External_AutoStop_Condition | Villkorsoperatorn som krävs för att konfigurera villkoret innan en avisering utlöses. Godkända värden är `GreaterThan` , `GreaterThanOrEqual` , och `LessThan` `LessThanOrEqual` .|
|External_AutoStop_Description | Aviseringen om att stoppa den virtuella datorn om CPU-procenten överskrider tröskelvärdet.|
|External_AutoStop_Frequency | Utvärderingsfrekvensen för regeln. Den här parametern accepterar indata i tidsspannformat. Möjliga värden är från 5 minuter till 6 timmar. |
|External_AutoStop_MetricName | Namnet på prestandamåttet som Azure-aviseringsregeln ska konfigureras för.|
|External_AutoStop_Severity | Allvarlighetsgraden för måttaviseringen, som kan vara mellan 0 och 4. |
|External_AutoStop_Threshold | Tröskelvärdet för Azure-aviseringsregeln som anges i variabeln `External_AutoStop_MetricName` . Procentvärdena sträcker sig från 1 till 100.|
|External_AutoStop_TimeAggregationOperator | Tidsaggregeringsoperatorn som tillämpas på den valda fönsterstorleken för att utvärdera villkoret. Godkända värden är `Average` , , , och `Minimum` `Maximum` `Total` `Last` .|
|External_AutoStop_TimeWindow | Storleken på fönstret där Azure analyserar valda mått för att utlösa en avisering. Den här parametern accepterar indata i tidsspannformat. Möjliga värden är från 5 minuter till 6 timmar.|
|External_EnableClassicVMs| Värde som anger om klassiska virtuella datorer är mål för funktionen. Standardvärdet är True. Ange den här variabeln till False för Azure-molnlösningsleverantör prenumerationer (CSP). Klassiska virtuella datorer kräver ett [klassiskt Kör som-konto.](automation-create-standalone-account.md#create-a-classic-run-as-account)|
|External_ExcludeVMNames | Kommaavgränsad lista över VM-namn som ska undantas, begränsat till 140 virtuella datorer. Om du lägger till fler än 140 virtuella datorer i listan kan virtuella datorer som angetts för exkludering startas eller stoppas oavsiktligt.|
|External_Start_ResourceGroupNames | Kommaavgränsad lista över en eller flera resursgrupper som är mål för startåtgärder.|
|External_Stop_ResourceGroupNames | Kommaavgränsad lista över en eller flera resursgrupper som är mål för stoppåtgärder.|
|External_WaitTimeForVMRetrySeconds |Väntetiden i sekunder för de åtgärder som ska  utföras på de virtuella datorerna för SequencedStartStop_Parent runbook. Med den här variabeln kan runbooken vänta på underordnade åtgärder i ett angivet antal sekunder innan nästa åtgärd fortsätter. Den maximala väntetiden är 1 0800 eller tre timmar. Standardvärdet är 2 100 sekunder.|
|Internal_AutomationAccountName | Anger namnet på Automation-kontot.|
|Internal_AutoSnooze_ARM_WebhookURI | Webhook-URI:en som anropas för AutoStop-scenariot för virtuella datorer.|
|Internal_AutoSnooze_WebhookUri | Webhook-URI:en som anropas för AutoStop-scenariot för klassiska virtuella datorer.|
|Internal_AzureSubscriptionId | Id:t för Azure-prenumerationen.|
|Internal_ResourceGroupName | Resursgruppsnamnet för Automation-kontot.|

>[!NOTE]
>För `External_WaitTimeForVMRetryInSeconds` variabeln har standardvärdet uppdaterats från 600 till 2100.

I alla scenarier är variablerna , och nödvändiga för att rikta in sig på virtuella datorer, förutom de kommaavgränsade VM-listorna för `External_Start_ResourceGroupNames`  `External_Stop_ResourceGroupNames` `External_ExcludeVMNames` **AutoStop_CreateAlert_Parent,** **SequencedStartStop_Parent** **och ScheduledStartStop_Parent** runbooks. Det innebär att dina virtuella datorer måste tillhöra målresursgrupper för att start- och stoppåtgärder ska kunna ske. Logiken fungerar ungefär som Azure Policy eftersom du kan rikta in dig på prenumerationen eller resursgruppen och har åtgärder som ärvs av nyligen skapade virtuella datorer. Med den här metoden behöver du inte ha ett separat schema för varje virtuell dator och hantera start och stopp i skala.

### <a name="schedules"></a>Scheman

I följande tabell visas alla standardscheman som skapats i ditt Automation-konto. Du kan ändra dem eller skapa egna anpassade scheman. Som standard är alla scheman inaktiverade förutom **Scheduled_StartVM** och **Scheduled_StopVM** scheman.

Aktivera inte alla scheman eftersom det kan skapa överlappande schemaåtgärder. Det är bäst att bestämma vilka optimeringar du vill göra och ändra dem därefter. Se exempelscenarierna i översiktsavsnittet för ytterligare förklaring.

|Schemanamn | Frekvens | Description|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | Var 8:e timme | Kör **AutoStop_CreateAlert_Parent** runbook var 8:e timme, vilket i sin tur stoppar de VM-baserade värdena `External_Start_ResourceGroupNames` i `External_Stop_ResourceGroupNames` variablerna , `External_ExcludeVMNames` och . Du kan också ange en kommaavgränsad lista över virtuella datorer med hjälp av `VMList` parametern .|
|Scheduled_StopVM | Användardefinierad, daglig | Kör **ScheduledStopStart_Parent** runbook med en parameter `Stop` för varje dag vid den angivna tidpunkten. Stoppar automatiskt alla virtuella datorer som uppfyller reglerna som definierats av variabeltillgångar. Aktivera det relaterade schemat **Scheduled-StartVM**.|
|Scheduled_StartVM | Användardefinierad, daglig | Kör **ScheduledStopStart_Parent** runbook med parametervärdet `Start` varje dag vid den angivna tidpunkten. Startar automatiskt alla virtuella datorer som uppfyller de regler som definieras av variabeltillgångar. Aktivera det relaterade schemat **Scheduled-StopVM**.|
|Sequenced-StopVM | 01:00 (UTC), varje fredag | Kör **Sequenced_StopStop_Parent** runbook med parametervärdet `Stop` varje fredag vid den angivna tidpunkten. Sekventiellt (stigande) stoppar alla virtuella datorer med taggen **SequenceStop** som definieras av lämpliga variabler. Mer information om taggvärden och tillgångsvariabler finns i [Runbooks](#runbooks). Aktivera det relaterade schemat **Sequenced-StartVM**.|
|Sequenced-StartVM | 13:00 (UTC), varje måndag | Kör **SequencedStopStart_Parent** runbook med parametervärdet `Start` varje måndag vid den angivna tidpunkten. Sekventiellt (fallande) startar alla virtuella datorer med taggen **SequenceStart som** definieras av lämpliga variabler. Mer information om taggvärden och variabeltillgångar finns i [Runbooks](#runbooks). Aktivera det relaterade schemat **Sequenced-StopVM**.|

## <a name="use-the-feature-with-classic-vms"></a>Använda funktionen med klassiska virtuella datorer

Om du använder funktionen Starta/stoppa virtuella datorer när de inte används klassiska virtuella datorer bearbetar Automation alla dina virtuella datorer sekventiellt per molntjänst. Virtuella datorer bearbetas fortfarande parallellt i olika molntjänster. 

För att kunna använda funktionen med klassiska virtuella datorer behöver du ett klassiskt Kör som-konto, som inte skapas som standard. Anvisningar om hur du skapar ett klassiskt Kör som-konto finns [i Skapa ett klassiskt Kör som-konto.](automation-create-standalone-account.md#create-a-classic-run-as-account)

Här är några rekommendationer om du har fler än 20 virtuella datorer per molntjänst:

* Skapa flera scheman med den överordnade **runbook-ScheduledStartStop_Parent** och ange 20 virtuella datorer per schema.
* I schemaegenskaperna använder du `VMList` parametern för att ange vm-namn som en kommaavgränsad lista (inga blanksteg).

Om Automation-jobbet för den här funktionen körs i mer än tre timmar tas det annars tillfälligt bort eller stoppas enligt gränsen [för tidsbegränsade](automation-runbook-execution.md#fair-share) delningar.

Azure CSP-prenumerationer stöder endast Azure Resource Manager modellen. Icke-Azure Resource Manager tjänster är inte tillgängliga i programmet. När Starta/stoppa virtuella datorer när de inte används körs kan du få fel eftersom den har cmdlets för att hantera klassiska resurser. Mer information om CSP finns i [Tillgängliga tjänster i CSP-prenumerationer.](/azure/cloud-solution-provider/overview/azure-csp-available-services) Om du använder en CSP-prenumeration bör du ange [variabeln External_EnableClassicVMs](#variables) till Falskt efter distributionen.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="view-the-feature"></a>Visa funktionen

Använd någon av följande metoder för att få åtkomst till den aktiverade funktionen:

* Från ditt Automation-konto väljer du **Starta/stoppa virtuell dator** under **Relaterade resurser.** På sidan Starta/stoppa virtuell dator väljer du **Hantera lösningen** under Hantera **start/stopp av VM-lösningar.**

* Gå till Log Analytics-arbetsytan som är länkad till ditt Automation-konto. När du har valt arbetsytan väljer du **Lösningar** i det vänstra fönstret. På sidan Lösningar väljer du **Start-Stop-VM[workspace]** i listan.  

Om du väljer funktionen visas **sidan Start-Stop-VM[workspace].** Här kan du granska viktig information, till exempel informationen i **panelen StartStopVM.** Precis som på Log Analytics-arbetsytan visar den här panelen antal och en grafisk representation av runbook-jobben för funktionen som har startats och har slutförts.

![Sidan automations-Uppdateringshantering](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

Du kan utföra ytterligare analys av jobbposterna genom att klicka på ringdiagramspanelen. Instrumentpanelen visar jobbhistorik och fördefinierade loggsökningsfrågor. Växla till den avancerade Portalen för Log Analytics för att söka baserat på dina sökfrågor.

## <a name="update-the-feature"></a>Uppdatera funktionen

Om du har distribuerat en tidigare version av Starta/stoppa virtuella datorer när de inte används ta bort den från ditt konto innan du distribuerar en uppdaterad version. Följ stegen för att [ta bort funktionen](automation-solution-vm-management-remove.md#delete-the-feature) och följ sedan stegen för att aktivera [den.](automation-solution-vm-management-enable.md)

## <a name="next-steps"></a>Nästa steg

Information om hur du aktiverar funktionen på virtuella datorer i din miljö finns [i Aktivera Starta/stoppa virtuella datorer när de inte används](automation-solution-vm-management-enable.md).
