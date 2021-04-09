---
title: Använda diagnostikinställningar för Recovery Services valv
description: Den här artikeln beskriver hur du använder de gamla och nya diagnostiska händelserna för Azure Backup.
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: b2130f06e17dd2b5cf8461d4e58342ee41c14f96
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100575420"
---
# <a name="use-diagnostics-settings-for-recovery-services-vaults"></a>Använda diagnostikinställningar för Recovery Services valv

Azure Backup skickar diagnostiska händelser som kan samlas in och användas i syfte att analysera, Avisera och rapportera.

Du kan konfigurera diagnostikinställningar för ett Recovery Services valv via Azure Portal genom att gå till valvet och välja **diagnostikinställningar**. Genom att välja **+ Lägg till diagnostisk inställning** kan du skicka en eller flera diagnostiska händelser till ett lagrings konto, en Event Hub eller en Log Analytics arbets yta.

![Fönstret Diagnostikinställningar](./media/backup-azure-diagnostics-events/diagnostics-settings-blade.png)

## <a name="diagnostics-events-available-for-azure-backup-users"></a>Diagnostiska händelser som är tillgängliga för Azure Backup användare

Azure Backup innehåller följande diagnostiska händelser. Varje händelse innehåller detaljerad information om en angiven uppsättning säkerhets kopierings artefakter:

* CoreAzureBackup
* AddonAzureBackupAlerts
* AddonAzureBackupProtectedInstance
* AddonAzureBackupJobs
* AddonAzureBackupPolicy
* AddonAzureBackupStorage

Om du fortfarande använder den [äldre Event](#legacy-event) AzureBackupReport rekommenderar vi att du växlar till att använda händelserna ovan.

Mer information finns i [data modell för Azure Backup Diagnostics-händelser](./backup-azure-reports-data-model.md).

Data för de här händelserna kan skickas till ett lagrings konto, en Log Analytics-arbetsyta eller en Event Hub. Om du skickar dessa data till en Log Analytics arbets yta väljer du den **resurs information** som ska växlas på skärmen **diagnostikinställningar** . Mer information finns i följande avsnitt.

## <a name="use-diagnostics-settings-with-log-analytics"></a>Använd diagnostikinställningar med Log Analytics

Du kan nu använda Azure Backup för att skicka valvets diagnostikdata till dedikerade Log Analytics tabeller för säkerhets kopiering. Dessa tabeller kallas [resursbaserade tabeller](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace).

Så här skickar du dina valv diagnostikdata till Log Analytics:

1. Gå till valvet och välj **diagnostikinställningar**. Välj **+ Lägg till diagnostisk inställning**.
1. Ange ett namn på den diagnostiska inställningen.
1. Markera kryss rutan **Skicka till Log Analytics** och välj en Log Analytics arbets yta.
1. Välj **resurs Special** i växlingen och välj följande sex händelser: **CoreAzureBackup**, **AddonAzureBackupJobs**, **AddonAzureBackupAlerts**, **AddonAzureBackupPolicy**, **AddonAzureBackupStorage** och **AddonAzureBackupProtectedInstance**.
1. Välj **Spara**.

   ![Resurs-/regionsspecifika läge](./media/backup-azure-diagnostics-events/resource-specific-blade.png)

När data har flödat till arbets ytan Log Analytics skapas dedikerade tabeller för var och en av dessa händelser på arbets ytan. Du kan fråga någon av dessa tabeller direkt. Du kan också utföra kopplingar eller unioner mellan dessa tabeller om det behövs.

> [!IMPORTANT]
> De sex händelserna, nämligen CoreAzureBackup, AddonAzureBackupJobs, AddonAzureBackupAlerts, AddonAzureBackupPolicy, AddonAzureBackupStorage och AddonAzureBackupProtectedInstance, stöds *bara* i det resursbaserade läget i [säkerhets kopierings rapporter](./configure-reports.md). *Om du försöker skicka data för de här sex händelserna i Azure Diagnostics-läget visas inga data i säkerhets kopierings rapporter.*

## <a name="legacy-event"></a>Äldre händelse

Traditionellt fanns alla säkerhetskopierade diagnostikdata för ett valv i en enskild händelse med namnet AzureBackupReport. De sex händelser som beskrivs här är i själva verket en nedbrytning av alla data som finns i AzureBackupReport.

För närvarande fortsätter vi att stödja AzureBackupReport-händelsen för bakåtkompatibilitet i fall där användare har befintliga anpassade frågor om den här händelsen. Exempel är anpassade logg aviseringar och anpassade visualiseringar. *Vi rekommenderar att du flyttar till de [nya händelserna](#diagnostics-events-available-for-azure-backup-users) så tidigt som möjligt*. Nya händelser:

* Gör det mycket enklare att arbeta med data i logg frågor.
* Ge bättre identifiering av scheman och deras struktur.
* Förbättra prestandan för både svars tid och fråge tider.

*Den äldre händelsen i Azure Diagnostics-läge kommer att bli föråldrad. Att välja nya händelser kan hjälpa dig att undvika komplexa migreringar vid ett senare tillfälle*. Vår [rapporterings lösning](./configure-reports.md) som använder Log Analytics kommer också att stoppa stöd data från den äldre händelsen.

### <a name="steps-to-move-to-new-diagnostics-settings-for-a-log-analytics-workspace"></a>Steg för att flytta till nya diagnostikinställningar för en Log Analytics arbets yta

1. Identifiera vilka valv som skickar data till Log Analytics arbets ytor med hjälp av den äldre händelsen och de prenumerationer som de tillhör. Kör följande fråga på varje arbets yta för att identifiera dessa valv och prenumerationer.

    ````Kusto
    let RangeStart = startofday(ago(3d));
    let VaultUnderAzureDiagnostics = (){
        AzureDiagnostics
        | where TimeGenerated >= RangeStart | where Category == "AzureBackupReport" and OperationName == "Vault" and SchemaVersion_s == "V2"
        | summarize arg_max(TimeGenerated, *) by ResourceId
        | project ResourceId, Category};
    let VaultUnderResourceSpecific = (){
        CoreAzureBackup
        | where TimeGenerated >= RangeStart | where OperationName == "Vault"
        | summarize arg_max(TimeGenerated, *) by ResourceId
        | project ResourceId, Category};
        // Some Workspaces will not have AzureDiagnostics Table, so you need to use isFuzzy
    let CombinedVaultTable = (){
        union isfuzzy = true
        (VaultUnderAzureDiagnostics() ),
        (VaultUnderResourceSpecific() )
        | distinct ResourceId, Category};
    CombinedVaultTable | where Category == "AzureBackupReport"
    | join kind = leftanti (
    CombinedVaultTable | where Category == "CoreAzureBackup"
    ) on ResourceId
    | parse ResourceId with * "SUBSCRIPTIONS/" SubscriptionId:string "/RESOURCEGROUPS" * "MICROSOFT.RECOVERYSERVICES/VAULTS/" VaultName:string
    | project ResourceId, SubscriptionId, VaultName
    ````

    Nedan visas en skärm bild av den fråga som körs i en av arbets ytorna:

    ![Fråga för arbets yta](./media/backup-azure-diagnostics-events/workspace-query.png)

2. Använd de [inbyggda Azure policy definitionerna](./azure-policy-configure-diagnostics.md) i Azure Backup för att lägga till en ny inställning för diagnostik för alla valv i en angiven omfattning. Den här principen lägger till en ny diagnostisk inställning till valv som antingen inte har någon diagnostik-inställning eller som bara har en äldre diagnostisk inställning. Den här principen kan tilldelas till en hel prenumeration eller resurs grupp i taget. Du måste ha ägar åtkomst till varje prenumeration som principen är tilldelad.

Du kan välja att ha separata diagnostikinställningar för AzureBackupReport och de sex nya händelserna tills du har migrerat alla anpassade frågor för att använda data från de nya tabellerna. Följande bild visar ett exempel på ett valv som har två diagnostiska inställningar. Den första inställningen med namnet **Setting1** skickar data från en AzureBackupReport-händelse till en Log Analytics arbets yta i Azure Diagnostics-läge. Den andra inställningen, med namnet **Setting2**, skickar data från de sex nya Azure Backup händelser till en Log Analytics arbets yta i det resursbaserade läget.

![Två inställningar](./media/backup-azure-diagnostics-events/two-settings-example.png)

> [!IMPORTANT]
> AzureBackupReport-händelsen stöds *bara* i Azure Diagnostics-läge. *Om du försöker skicka data för den här händelsen i det resursbaserade läget kommer inga data att flöda till Log Analytics arbets ytan.*

> [!NOTE]
> Växlingen för **Azure Diagnostics** eller **Resource Specific** visas bara om användaren väljer **Skicka till Log Analytics**. Om du vill skicka data till ett lagrings konto eller en Event Hub väljer användaren det begärda målet och markerar kryss rutorna för alla önskade händelser, utan ytterligare indata. Vi rekommenderar att du inte väljer den äldre Event-AzureBackupReport som skickas framåt.

## <a name="send-azure-site-recovery-events-to-log-analytics"></a>Skicka Azure Site Recovery händelser till Log Analytics

Azure Backup och Azure Site Recovery händelser skickas från samma Recovery Services-valv. Azure Site Recovery är för närvarande inte tillgängligt för resursbaserade tabeller. Användare som vill skicka Azure Site Recovery händelser till Log Analytics hänvisas till att *endast* använda Azure Diagnostics-läge, som visas i avbildningen. Om *du väljer det resursbaserade läget för Azure Site Recovery händelser så förhindras att nödvändiga data skickas till arbets ytan Log Analytics*.

![Site Recovery händelser](./media/backup-azure-diagnostics-events/site-recovery-settings.png)

Sammanfattningsvis:

* Om du redan har Log Analytics diagnostik som har kon figurer ATS med Azure-diagnostik och har skrivna anpassade frågor ovanpå den, bör du behålla inställningen *intakt* tills du migrerar dina frågor till att använda data från de nya händelserna.
* Om du också vill publicera på nya tabeller, som vi rekommenderar, skapar du en **ny** inställning för diagnostik, väljer **resurs information** och väljer de sex nya händelserna.
* Om du för närvarande skickar Azure Site Recovery händelser till Log Analytics ska du *inte* välja det resurs-/regionsspecifika läget för dessa händelser. Annars flödar inte data för dessa händelser till din Log Analytics-arbetsyta. Skapa i stället en ytterligare diagnostisk inställning, Välj **Azure Diagnostics** och välj relevanta Azure Site Recovery händelser.

Följande bild visar ett exempel på en användare som har tre diagnostikinställningar för ett valv. Den första inställningen, med namnet **Setting1**, skickar data från en AzureBackupReport-händelse till en Log Analytics arbets yta i Azure Diagnostics-läge. Den andra inställningen, med namnet **Setting2**, skickar data från de sex nya Azure Backup händelser till en Log Analytics arbets yta i det resursbaserade läget. Den tredje inställningen med namnet **Setting3** skickar data från Azure Site Recovery händelser till en Log Analytics arbets yta i Azure Diagnostics-läge.

![Tre inställningar](./media/backup-azure-diagnostics-events/three-settings-example.png)

## <a name="next-steps"></a>Nästa steg

[Lär dig mer om Log Analytics data modell för diagnostiska händelser](./backup-azure-reports-data-model.md)
