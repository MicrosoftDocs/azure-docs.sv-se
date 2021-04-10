---
title: Övervaka Azure Site Recovery med Azure Monitor loggar
description: Lär dig hur du övervakar Azure Site Recovery med Azure Monitors loggar (Log Analytics)
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: raynew
ms.openlocfilehash: 308e1bcf042feb15179d32844d8c569af6166619
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100571674"
---
# <a name="monitor-site-recovery-with-azure-monitor-logs"></a>Övervaka Site Recovery med Azure Monitor-loggar

Den här artikeln beskriver hur du övervakar datorer som replikeras av Azure [Site Recovery](site-recovery-overview.md), med hjälp av [Azure Monitor loggar](../azure-monitor/logs/data-platform-logs.md)och [Log Analytics](../azure-monitor/logs/log-query-overview.md).

Azure Monitor loggar tillhandahåller en logg data plattform som samlar in aktivitets-och resurs loggar, tillsammans med andra övervaknings data. I Azure Monitor loggar använder du Log Analytics för att skriva och testa logg frågor och för att interaktivt analysera loggdata. Du kan visualisera och fråga logg resultat och konfigurera aviseringar för att vidta åtgärder baserat på övervakade data.

För Site Recovery kan du använda Azure Monitors loggar för att hjälpa dig att göra följande:

- **Övervaka Site Recovery hälsa och status**. Du kan till exempel övervaka replikeringsstatus, redundanstest, Site Recovery händelser, återställnings punkt mål (återställnings punkter) för skyddade datorer och ändrings takt för disk/data.
- **Konfigurera aviseringar för Site Recovery**. Du kan till exempel konfigurera aviseringar för maskin hälso status, redundanstest eller Site Recovery jobb status.

Användning av Azure Monitor-loggar med Site Recovery stöds för **Azure till Azure** -replikering och **VMware VM/fysisk server till Azure** -replikering.

> [!NOTE]
> För att hämta omsättnings data loggar och överförings takt loggar för VMware och fysiska datorer måste du installera en Microsoft Monitoring Agent på processervern. Den här agenten skickar loggarna för de replikerande datorerna till arbets ytan. Den här funktionen är endast tillgänglig för 9,30-versionen av mobilitets agenten.

## <a name="before-you-start"></a>Innan du börjar

Du behöver det här:

- Minst en dator som skyddas i ett Recovery Services-valv.
- En Log Analytics arbets yta för att lagra Site Recovery loggar. [Lär dig mer om](../azure-monitor/logs/quick-create-workspace.md) att konfigurera en arbets yta.
- En grundläggande förståelse för hur du skriver, kör och analyserar logg frågor i Log Analytics. [Läs mer](../azure-monitor/logs/log-analytics-tutorial.md).

Vi rekommenderar att du läser igenom [vanliga övervaknings frågor](monitoring-common-questions.md) innan du börjar.

## <a name="configure-site-recovery-to-send-logs"></a>Konfigurera Site Recovery för att skicka loggar

1. I valvet klickar du på **diagnostiska inställningar**  >  **Lägg till diagnostisk inställning**.

    ![Skärm bild som visar alternativet Lägg till diagnostisk inställning.](./media/monitoring-log-analytics/add-diagnostic.png)

2. I **diagnostikinställningar** anger du ett namn och markerar kryss rutan **Skicka till Log Analytics**.
3. Välj prenumerationen Azure Monitor loggar och Log Analytics arbets ytan.
4. Välj **Azure-diagnostik** i växla.
5. I listan logg väljer du alla loggar med prefixet **AzureSiteRecovery**. Klicka sedan på **OK**.

    ![Skärm bild av skärmen diagnostik inställning.](./media/monitoring-log-analytics/select-workspace.png)

Site Recovery loggar börjar mata in i en tabell (**AzureDiagnostics**) i den valda arbets ytan.

## <a name="configure-microsoft-monitoring-agent-on-the-process-server-to-send-churn-and-upload-rate-logs"></a>Konfigurera Microsoft Monitoring Agent på processervern för sändning av omsättnings-och överförings takt loggar

Du kan samla in information om data omsättnings taxa och information om överförings hastighet för dina VMware/fysiska datorer lokalt. För att aktivera detta måste en Microsoft Monitoring Agent installeras på processervern.

1. Gå till arbets ytan Log Analytics och klicka på **Avancerade inställningar**.
2. Klicka på sidan **anslutna källor** och välj ytterligare **Windows-servrar**.
3. Hämta Windows-agenten (64-bitars) på processervern. 
4. [Hämta arbetsyte-ID och nyckel](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key)
5. [Konfigurera agenten att använda TLS 1,2](../azure-monitor/agents/agent-windows.md#configure-agent-to-use-tls-12)
6. [Slutför Agent installationen](../azure-monitor/agents/agent-windows.md#install-agent-using-setup-wizard) genom att ange ID och nyckel för den hämtade arbets ytan.
7. När installationen är klar går du till Log Analytics arbets yta och klickar på **Avancerade inställningar**. Gå till sidan **data** och klicka på Windows- **prestandaräknare**. 
8. Klicka på **+** om du vill lägga till följande två räknare med exempel intervallet 300 sekunder:

    - ASRAnalytics(*)\SourceVmChurnRate
    - ASRAnalytics(*)\SourceVmThrpRate

Data omsättningen och överföringshastigheten börjar mata in på arbets ytan.


## <a name="query-the-logs---examples"></a>Skicka frågor till loggarna – exempel

Du hämtar data från loggar med hjälp av logg frågor som skrivits med [Kusto-frågespråket](../azure-monitor/logs/get-started-queries.md). Det här avsnittet innehåller några exempel på vanliga frågor som du kan använda för att Site Recovery övervakning.

> [!NOTE]
> Några av exemplen använder **replicationProviderName_s** inställt på **A2A**. Detta hämtar virtuella Azure-datorer som replikeras till en sekundär Azure-region med hjälp av Site Recovery. I de här exemplen kan du ersätta **A2A** med **InMageAzureV2**, om du vill hämta lokala virtuella VMware-datorer eller fysiska servrar som replikeras till Azure med hjälp av Site Recovery.


### <a name="query-replication-health"></a>Hälso tillstånd för fråga

Den här frågan ritar ett cirkel diagram för den aktuella replikeringsstatus för alla skyddade virtuella Azure-datorer, uppdelat i tre tillstånd: normal, varning eller kritisk.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , replicationHealth_s  
| summarize count() by replicationHealth_s  
| render piechart   
```
### <a name="query-mobility-service-version"></a>Fråga mobilitets tjänst version

Den här frågan ritar ett cirkel diagram för virtuella Azure-datorer som replikeras med Site Recovery, uppdelade efter den version av mobilitets agenten som de kör.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , agentVersion_s  
| summarize count() by agentVersion_s  
| render piechart 
```

### <a name="query-rpo-time"></a>Återställnings tid för fråga

Den här frågan ritar ett stapeldiagram med virtuella Azure-datorer som replikeras med Site Recovery, uppdelade efter återställnings punkt mål (återställnings punkt mål): mindre än 15 minuter, mellan 15-30 minuter, mer än 30 minuter.

```
AzureDiagnostics 
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| extend RPO = case(rpoInSeconds_d <= 900, "<15Min",   
rpoInSeconds_d <= 1800, "15-30Min", ">30Min")  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , RPO  
| summarize Count = count() by RPO  
| render barchart 
```

![Skärm bild som visar ett stapeldiagram med virtuella Azure-datorer som replikeras med Site Recovery.](./media/monitoring-log-analytics/example1.png)

### <a name="query-site-recovery-jobs"></a>Fråga Site Recovery jobb

Den här frågan hämtar alla Site Recovery jobb (för alla katastrof återställnings scenarier) som utlöses under de senaste 72 timmarna och deras slut för ande tillstånd.

```
AzureDiagnostics  
| where Category == "AzureSiteRecoveryJobs"  
| where TimeGenerated >= ago(72h)   
| project JobName = OperationName , VaultName = Resource , TargetName = affectedResourceName_s, State = ResultType  
```

### <a name="query-site-recovery-events"></a>Fråga Site Recovery händelser

Den här frågan hämtar alla Site Recovery händelser (för alla katastrof återställnings scenarier) som skapats under de senaste 72 timmarna, tillsammans med deras allvarlighets grad. 

```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryEvents"   
| where TimeGenerated >= ago(72h)   
| project AffectedObject=affectedResourceName_s , VaultName = Resource, Description_s = healthErrors_s , Severity = Level  
```

### <a name="query-test-failover-state-pie-chart"></a>Tillstånd för redundanstest (cirkel diagram)

Den här frågan ritar ett cirkel diagram för redundanstest för virtuella Azure-datorer som replikeras med Site Recovery.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| where isnotempty(failoverHealth_s) and isnotnull(failoverHealth_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , Resource, failoverHealth_s  
| summarize count() by failoverHealth_s  
| render piechart 
```

### <a name="query-test-failover-state-table"></a>Tillstånd för redundanstest (tabell)

Den här frågan ritar en tabell för redundanstest för virtuella Azure-datorer som replikeras med Site Recovery.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where isnotempty(failoverHealth_s) and isnotnull(failoverHealth_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , VaultName = Resource , TestFailoverStatus = failoverHealth_s 
```

### <a name="query-machine-rpo"></a>Fråga datorns återställnings punkt

Den här frågan ritar ett trend diagram som spårar återställningen av en enskild virtuell Azure-dator (ContosoVM123) under de senaste 72 timmarna.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where TimeGenerated > ago(72h)  
| where isnotempty(name_s) and isnotnull(name_s)   
| where name_s == "ContosoVM123"  
| project TimeGenerated, name_s , RPO_in_seconds = rpoInSeconds_d   
| render timechart 
```
![Skärm bild av ett trend diagram som spårar återställningen av en enskild virtuell Azure-dator.](./media/monitoring-log-analytics/example2.png)

### <a name="query-data-change-rate-churn-and-upload-rate-for-an-azure-vm"></a>Fråga om data ändrings takt (omsättning) och uppladdnings takt för en virtuell Azure-dator

Den här frågan ritar ett trend diagram för en viss virtuell Azure-dator (ContosoVM123) som representerar data ändrings takten (skrivna byte per sekund) och data överförings takten. 

```
AzureDiagnostics   
| where Category in ("AzureSiteRecoveryProtectedDiskDataChurn", "AzureSiteRecoveryReplicationDataUploadRate")   
| extend CategoryS = case(Category contains "Churn", "DataChurn",   
Category contains "Upload", "UploadRate", "none")  
| extend InstanceWithType=strcat(CategoryS, "_", InstanceName_s)   
| where TimeGenerated > ago(24h)   
| where InstanceName_s startswith "ContosoVM123"   
| project TimeGenerated , InstanceWithType , Churn_MBps = todouble(Value_s)/1048576   
| render timechart  
```
![skärm bild av ett trend diagram för en angiven virtuell Azure-dator.](./media/monitoring-log-analytics/example3.png)

### <a name="query-data-change-rate-churn-and-upload-rate-for-a-vmware-or-physical-machine"></a>Fråga om data ändrings frekvens (omsättning) och uppladdnings takt för en VMware-eller fysisk dator

> [!Note]
> Se till att konfigurera övervaknings agenten på processervern för att hämta dessa loggar. Se [steg för att konfigurera övervaknings agenten](#configure-microsoft-monitoring-agent-on-the-process-server-to-send-churn-and-upload-rate-logs).

Den här frågan ritar ett trend diagram för en viss disk **disk0** av ett replikerat objekt **Win-9r7sfh9qlru**, som representerar data ändrings takten (skrivna byte per sekund) och data överförings takt. Du kan hitta disk namnet på **disk** bladet för det replikerade objektet i Recovery Services-valvet. Instans namnet som ska användas i frågan är DNS-namnet på datorn följt av _ och disk namnet som i det här exemplet.

```
Perf
| where ObjectName == "ASRAnalytics"
| where InstanceName contains "win-9r7sfh9qlru_disk0"
| where TimeGenerated >= ago(4h) 
| project TimeGenerated ,CounterName, Churn_MBps = todouble(CounterValue)/5242880 
| render timechart
```
Processervern pushrar dessa data var 5: e minut till arbets ytan Log Analytics. Dessa data punkter representerar det genomsnitt som beräknas i 5 minuter.

### <a name="query-disaster-recovery-summary-azure-to-azure"></a>Sammanfattning av förfrågningar om haveri beredskap (Azure till Azure)

Den här frågan ritar en sammanfattnings tabell för virtuella Azure-datorer som replikeras till en sekundär Azure-region.  Den visar VM-namn, replikering och skydds status, återställnings status för redundanstest, Mobility agent-version, eventuella aktiva replikeringsfel och käll platsen.

```
AzureDiagnostics 
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, SourceLocation = primaryFabricName_s 
```

### <a name="query-disaster-recovery-summary-vmwarephysical-servers"></a>Översikt över förfrågningar om haveri beredskap (VMware/fysiska servrar)

Den här frågan ritar en sammanfattnings tabell för virtuella VMware-datorer och fysiska servrar som replikeras till Azure.  Det visar dator namn, replikering och skydds status, återställnings status för redundanstest, Mobility agent-version, eventuella aktiva replikeringsfel och relevant processerver.

```
AzureDiagnostics  
| where replicationProviderName_s == "InMageAzureV2"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, ProcessServer = processServerName_g  
```

## <a name="set-up-alerts---examples"></a>Konfigurera aviseringar – exempel

Du kan ställa in Site Recovery aviseringar baserat på Azure Monitor data. [Läs mer](../azure-monitor/alerts/alerts-log.md#create-a-log-alert-rule-with-the-azure-portal) om hur du konfigurerar logg aviseringar. 

> [!NOTE]
> Några av exemplen använder **replicationProviderName_s** inställt på **A2A**. Detta ställer in aviseringar för virtuella Azure-datorer som replikeras till en sekundär Azure-region. I de här exemplen kan du ersätta **A2A** med **InMageAzureV2** om du vill ställa in aviseringar för lokala virtuella VMware-datorer eller fysiska servrar som replikeras till Azure.

### <a name="multiple-machines-in-a-critical-state"></a>Flera datorer i ett kritiskt tillstånd

Konfigurera en avisering om fler än 20 replikerade virtuella Azure-datorer hamnar i ett kritiskt tillstånd.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count() 
```
Ange **tröskelvärdet** till 20 för aviseringen.

### <a name="single-machine-in-a-critical-state"></a>En enskild dator med kritiskt tillstånd

Konfigurera en avisering om en enskild replikerad virtuell Azure-dator hamnar i ett kritiskt tillstånd.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where name_s == "ContosoVM123"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
Ange **tröskelvärdet** till 1 för aviseringen.

### <a name="multiple-machines-exceed-rpo"></a>Flera datorer överskrider återställnings punkt

Konfigurera en avisering om återställningen för fler än 20 virtuella Azure-datorer överstiger 30 minuter.
```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where rpoInSeconds_d > 1800  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project name_s , rpoInSeconds_d   
| summarize count()  
```
Ange **tröskelvärdet** till 20 för aviseringen.

### <a name="single-machine-exceeds-rpo"></a>En enskild dator överskrider återställnings punkt

Konfigurera en avisering om återställningen för en enskild virtuell Azure-dator överskrider 30 minuter.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where name_s == "ContosoVM123"  
| where rpoInSeconds_d > 1800  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project name_s , rpoInSeconds_d   
| summarize count()  
```
Ange **tröskelvärdet** till 1 för aviseringen.

### <a name="test-failover-for-multiple-machines-exceeds-90-days"></a>Redundanstest för flera datorer överskrider 90 dagar

Ställ in en avisering om det senaste lyckade redundanstestning var mer än 90 dagar, för fler än 20 virtuella datorer. 

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where Category == "AzureSiteRecoveryReplicatedItems"  
| where isnotempty(name_s) and isnotnull(name_s)   
| where lastSuccessfulTestFailoverTime_t <= ago(90d)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
Ange **tröskelvärdet** till 20 för aviseringen.

### <a name="test-failover-for-single-machine-exceeds-90-days"></a>Redundanstest för en enskild dator överskrider 90 dagar

Ställ in en avisering om det senaste lyckade redundanstest för en angiven virtuell dator var mer än 90 dagar sedan.
```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where Category == "AzureSiteRecoveryReplicatedItems"  
| where isnotempty(name_s) and isnotnull(name_s)   
| where lastSuccessfulTestFailoverTime_t <= ago(90d)   
| where name_s == "ContosoVM123"  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
Ange **tröskelvärdet** till 1 för aviseringen.

### <a name="site-recovery-job-fails"></a>Site Recovery jobbet Miss lyckas

Konfigurera en avisering om ett Site Recovery jobb (i det här fallet återskydds jobb) Miss lyckas för ett Site Recovery scenario under den senaste dagen. 
```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryJobs"   
| where OperationName == "Reprotect"  
| where ResultType == "Failed"  
| summarize count()  
```

För aviseringen anger du **tröskelvärdet** till 1 och **period** till 1440 minuter för att kontrol lera felen under den senaste dagen.

## <a name="next-steps"></a>Nästa steg

[Lär dig mer om](site-recovery-monitor-and-troubleshoot.md) inbyggd Site Recovery övervakning.