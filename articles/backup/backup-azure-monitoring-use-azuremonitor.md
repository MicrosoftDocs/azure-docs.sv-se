---
title: Övervaka Azure Backup med Azure Monitor
description: Övervaka Azure Backup arbets belastningar och skapa anpassade aviseringar med Azure Monitor.
ms.topic: conceptual
ms.date: 06/04/2019
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: 1800771bfff0afbcec8440383536734246ea8f5c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100580734"
---
# <a name="monitor-at-scale-by-using-azure-monitor"></a>Övervaka i skala med hjälp av Azure Monitor

Azure Backup tillhandahåller [inbyggda övervaknings-och aviserings funktioner](backup-azure-monitoring-built-in-monitor.md) i ett Recovery Services-valv. Dessa funktioner är tillgängliga utan ytterligare hanteringsinfrastruktur. Men den här inbyggda tjänsten är begränsad i följande scenarier:

- Om du övervakar data från flera Recovery Services-valv över prenumerationer
- Om den önskade aviserings kanalen *inte* är e-post
- Om användarna vill ha aviseringar för fler scenarier
- Om du vill visa information från en lokal komponent, till exempel System Center Data Protection Manager i Azure, som portalen inte visas i [**säkerhets kopierings jobb**](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault) eller [**säkerhets kopierings aviseringar**](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault)

## <a name="using-log-analytics-workspace"></a>Använda Log Analytics arbets yta

### <a name="create-alerts-by-using-log-analytics"></a>Skapa aviseringar med hjälp av Log Analytics

I Azure Monitor kan du skapa egna aviseringar i en Log Analytics arbets yta. I arbets ytan använder du *Azures åtgärds grupper* för att välja önskad meddelande funktion.

> [!IMPORTANT]
> Information om kostnaden för att skapa den här frågan finns [Azure Monitor prissättning](https://azure.microsoft.com/pricing/details/monitor/).

Öppna avsnittet **loggar** i Log Analytics arbets ytan och skapa en fråga för dina egna loggar. När du väljer **ny varnings regel** öppnas sidan Azure Monitor aviserings skapande som visas i följande bild.

![Skapa en avisering i en Log Analytics arbets yta](media/backup-azure-monitoring-laworkspace/custom-alert.png)

Här är resursen redan markerad som Log Analytics arbets yta och integration av åtgärds gruppen.

![Sidan Log Analytics aviserings skapande](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

#### <a name="alert-condition"></a>Aviserings villkor

Den definiering av egenskaperna för en avisering är dess Utlös ande villkor. Välj **villkor** om du vill läsa in Kusto-frågan automatiskt på sidan **loggar** som du ser i följande bild. Här kan du redigera villkoret så att det passar dina behov. Mer information finns i [exempel på Kusto-frågor](#sample-kusto-queries).

![Konfigurera ett varnings villkor](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

Om det behövs kan du redigera frågan Kusto. Välj ett tröskelvärde, en period och en frekvens. Tröskelvärdet avgör när aviseringen ska höjas. Perioden är tiden då frågan körs. Om tröskelvärdet till exempel är större än 0, är perioden 5 minuter och frekvensen är 5 minuter, kör regeln frågan var 5: e minut, och granska de senaste 5 minuterna. Om antalet resultat är större än 0 får du ett meddelande via den valda åtgärds gruppen.

> [!NOTE]
> Om du vill köra varnings regeln en gång per dag, ändrar du värdet för både "period" och "frekvens" till 1440, vilket är 24 timmar.

#### <a name="alert-action-groups"></a>Aviserings åtgärds grupper

Använd en åtgärds grupp för att ange en meddelande kanal. Om du vill se tillgängliga aviserings mekanismer väljer du **Skapa nytt** under **Åtgärds grupper**.

![Tillgängliga aviserings mekanismer i fönstret Lägg till åtgärds grupp](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

Du kan uppfylla alla varnings-och övervaknings krav från Log Analytics ensamma, eller så kan du använda Log Analytics för att komplettera inbyggda meddelanden.

Mer information finns i [skapa, Visa och hantera logg aviseringar med hjälp av Azure Monitor](../azure-monitor/alerts/alerts-log.md) och [skapa och hantera åtgärds grupper i Azure Portal](../azure-monitor/alerts/action-groups.md).

### <a name="sample-kusto-queries"></a>Exempel på Kusto-frågor

Standard diagrammen ger dig Kusto frågor om grundläggande scenarier som du kan använda för att skapa aviseringar. Du kan också ändra frågorna för att hämta de data som du vill bli aviserad om. Klistra in följande exempel på Kusto frågor på sidan **loggar** och skapa sedan aviseringar för frågorna:

- Alla slutförda säkerhets kopierings jobb

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
    | where JobStatus=="Completed"
    ````

- Alla misslyckade säkerhets kopierings jobb

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
    | where JobStatus=="Failed"
    ````

- Alla lyckade säkerhets kopierings jobb för virtuella Azure-datorer

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="VM" and BackupManagementType=="IaaSVM"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- Alla lyckade säkerhets kopierings jobb för SQL-logg

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup" and JobOperationSubType=="Log"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="SQLDataBase" and BackupManagementType=="AzureWorkload"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- Alla lyckade Azure Backup Agent jobb

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="FileFolder" and BackupManagementType=="MAB"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- Säkerhets kopiering förbrukad lagring per säkerhets kopierings objekt

    ````Kusto
    CoreAzureBackup
    //Get all Backup Items
    | where OperationName == "BackupItem"
    //Get distinct Backup Items
    | distinct BackupItemUniqueId, BackupItemFriendlyName
    | join kind=leftouter
    (AddonAzureBackupStorage
    | where OperationName == "StorageAssociation"
    //Get latest record for each Backup Item
    | summarize arg_max(TimeGenerated, *) by BackupItemUniqueId
    | project BackupItemUniqueId , StorageConsumedInMBs)
    on BackupItemUniqueId
    | project BackupItemUniqueId , BackupItemFriendlyName , StorageConsumedInMBs
    | sort by StorageConsumedInMBs desc
    ````

### <a name="diagnostic-data-update-frequency"></a>Uppdaterings frekvens för diagnostikdata

Diagnostikdata från valvet pumpas till Log Analytics arbets ytan med en fördröjning. Varje händelse anländer till Log Analytics arbets ytan *20 till 30 minuter* efter att den har överförts från Recovery Services-valvet. Här är mer information om fördröjningen:

- I alla lösningar skickas säkerhets kopierings tjänstens inbyggda aviseringar så snart de har skapats. De visas vanligt vis i arbets ytan Log Analytics efter 20 till 30 minuter.
- I alla lösningar skickas säkerhets kopierings jobb på begäran och återställnings jobb så fort de är *klara*.
- För alla lösningar utom SQL-säkerhetskopiering skickas schemalagda säkerhets kopierings jobb så snart de är *klara*.
- För SQL-säkerhetskopiering, eftersom logg säkerhets kopieringar kan ske var 15: e minut, är information för alla schemalagda säkerhets kopierings jobb, inklusive loggar, batch och push-överförd var 6: e timme.
- Alla lösningar, annan information som säkerhets kopierings objekt, princip, återställnings punkter, lagring och så vidare, överförs minst *en gång per dag.*
- En ändring i konfigurationen för säkerhets kopiering (till exempel att ändra princip eller redigera princip) utlöser en push av all relaterad säkerhets kopierings information.

## <a name="using-the-recovery-services-vaults-activity-logs"></a>Använda aktivitets loggarna i Recovery Services Vault

> [!CAUTION]
> Följande steg gäller endast för *säkerhets kopiering av virtuella Azure-datorer.* Du kan inte använda de här stegen för lösningar som Azure Backup Agent, SQL-säkerhetskopieringar i Azure eller Azure Files.

Du kan också använda aktivitets loggar för att få meddelanden om händelser som till exempel säkerhets kopieringen lyckades. Börja med att följa dessa steg:

1. Logga in på Azure Portal.
2. Öppna det relevanta Recovery Services-valvet.
3. I valvets egenskaper öppnar du avsnittet **aktivitets logg** .

Identifiera lämplig logg och skapa en avisering:

1. Kontrol lera att du har fått aktivitets loggar för lyckade säkerhets kopieringar genom att använda de filter som visas i följande bild. Ändra **TimeSpan** -värdet vid behov för att visa poster.

   ![Filtrera för att hitta aktivitets loggar för virtuella Azure-säkerhetskopieringar](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

2. Välj ett åtgärds namn för att se relevant information.
3. Välj **ny varnings regel** för att öppna sidan **Skapa regel** .
4. Skapa en avisering genom att följa stegen i [skapa, Visa och hantera aktivitets logg aviseringar med hjälp av Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md).

   ![Ny varnings regel](media/backup-azure-monitoring-laworkspace/new-alert-rule.png)

Här är resursen själva Recovery Services själva valvet. Upprepa samma steg för alla valv där du vill bli meddelad via aktivitets loggarna. Villkoret har ingen tröskel, period eller frekvens eftersom den här aviseringen baseras på händelser. Så snart den relevanta aktivitets loggen har genererats, utlöses aviseringen.

## <a name="using-log-analytics-to-monitor-at-scale"></a>Använda Log Analytics för att övervaka i skala

Du kan visa alla aviseringar som skapats från aktivitets loggar och Log Analytics arbets ytor i Azure Monitor. Öppna bara **aviserings** rutan till vänster.

Även om du kan få aviseringar via aktivitets loggar rekommenderar vi starkt att du använder Log Analytics snarare än aktivitets loggar för övervakning i stor skala. Skälet är följande:

- **Begränsade scenarier**: meddelanden via aktivitets loggar gäller endast för virtuella Azure-säkerhetskopieringar. Meddelandena måste konfigureras för varje Recovery Services valv.
- **Definitions passning**: den schemalagda säkerhets kopierings aktiviteten passar inte med den senaste definitionen av aktivitets loggarna. I stället justeras det med [resurs loggar](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace). Den här justeringen orsakar oväntade effekter när de data som flödar genom aktivitets loggs kanalen ändras.
- **Problem med aktivitets logg kanalen**: i Recovery Services valv följer aktivitets loggar som pumpas från Azure Backup en ny modell. Den här ändringen påverkar tyvärr genereringen av aktivitets loggar i Azure Government, Azure Germany och Azure Kina 21Vianet. Om användarna av dessa moln tjänster skapar eller konfigurerar aviseringar från aktivitets loggar i Azure Monitor utlöses inte aviseringarna. I alla offentliga Azure-regioner visas även dessa loggar om en användare [samlar in Recovery Services aktivitets loggar till en Log Analytics arbets yta](../azure-monitor/essentials/activity-log.md).

Använd en Log Analytics arbets yta för övervakning och avisering i skala för alla arbets belastningar som skyddas av Azure Backup.

## <a name="next-steps"></a>Nästa steg

Information om hur du skapar anpassade frågor finns i [Log Analytics data modell](backup-azure-reports-data-model.md).
