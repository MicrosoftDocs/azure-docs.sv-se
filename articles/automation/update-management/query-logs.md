---
title: Fråga Azure Automation Uppdateringshantering loggar
description: Den här artikeln beskriver hur du skickar frågor till loggarna för Uppdateringshantering i Log Analytics-arbetsytan.
services: automation
ms.subservice: update-management
ms.date: 09/24/2020
ms.topic: conceptual
ms.openlocfilehash: 5eb0c7d72896cc9a27907743b1b9c3d5a40614dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100592870"
---
# <a name="query-update-management-logs"></a>Fråga Uppdateringshanteringsloggar

Utöver de uppgifter som anges under Uppdateringshantering distribution kan du söka i loggarna som lagras i Log Analytics arbets ytan. Om du vill söka i loggarna från ditt Automation-konto väljer du **uppdaterings hantering** och öppnar Log Analytics arbets ytan som är kopplad till din distribution.

Du kan också anpassa logg frågorna eller använda dem från olika klienter. Se [Log Analytics Search API-dokumentation](https://dev.loganalytics.io/).

## <a name="query-update-records"></a>Fråga efter uppdaterings poster

Uppdateringshantering samlar in poster för virtuella Windows-och Linux-datorer och de data typer som visas i loggs öknings resultat. I följande avsnitt beskrivs dessa poster.

### <a name="query-required-updates"></a>Fråga krävs uppdateringar

En post med en typ av `RequiredUpdate` skapas som representerar uppdateringar som krävs av en dator. Dessa poster har egenskaperna i följande tabell:

| Egenskap | Beskrivning |
|----------|-------------|
| Dator | Fullständigt kvalificerat domän namn för rapporterings datorn. |
| KBID | Kunskaps bas artikel-ID för Windows Update. |
| ManagementGroupName | Namnet på den Operations Manager hanterings gruppen eller Log Analytics arbets ytan. |
| Produkt | De produkter som uppdateringen gäller för. |
| PublishDate | Det datum då uppdateringen är redo att laddas ned och installeras från Windows Update. |
| Server | | 
| SourceHealthServiceId | Unik identifierare som representerar Log Analytics Windows agent-ID. |
| SourceSystem | *OperationsManager* |
| TenantId | Unik identifierare som representerar organisations instansen av Azure Active Directory. |
| TimeGenerated | Datum och tid då posten skapades. |
| Typ | *Uppdatera* |
| UpdateClassification | Anger vilken typ av uppdateringar som kan tillämpas. För Windows:<br> *Kritiska uppdateringar*<br> *Säkerhets uppdateringar*<br> *Samlade uppdateringar*<br> *Funktionspaket*<br> *Service Pack*<br> *Definitionsuppdateringar*<br> *Verktyg*<br> *Uppdateringar*. För Linux:<br> *Kritiska uppdateringar och säkerhetsuppdateringar*<br> *Övrigt* |
| UpdateSeverity | Allvarlighets grad för säkerhets problemet. Värden är:<br> *Kritiskt*<br> *Viktigt*<br> *Medel*<br> *Låg* |
| UpdateTitle | Uppdateringens rubrik.|

### <a name="query-update-record"></a>Uppdatera post för fråga

En post med en typ av `Update` skapas som representerar uppdateringar som är tillgängliga och deras installations status för en dator. Dessa poster har egenskaperna i följande tabell:

| Egenskap | Beskrivning |
|----------|-------------|
| ApprovalSource | Gäller endast för Windows-operativsystem. Källa för godkännande av posten. Värdet är Microsoft Update. |
| Godkända | Sant om posten är godkänd eller falskt annars. |
| Klassificering | Klassificering av godkännande. Värdet är uppdateringar. |
| Dator | Fullständigt kvalificerat domän namn för rapporterings datorn. |
| ComputerEnvironment | Miljö. Möjliga värden är Azure eller icke-Azure. |
| MSRCBulletinID | ID-nummer för säkerhetsbulletin. |
| MSRCSeverity | Allvarlighets grad för säkerhets problemet. Värden är:<br> Kritiskt<br> Viktigt<br> Medel<br> Låg |  
| KBID | Kunskaps bas artikel-ID för Windows Update. |
| ManagementGroupName | Namnet på Operations Manager hanterings gruppen eller arbets ytan Log Analytics. |
| UpdateID | Unikt ID för program uppdateringen. |
| RevisionNumber | Revisions numret för en bestämd revision av en uppdatering. |
| Valfritt | Sant om posten är valfri eller falskt annars. |
| RebootBehavior | Omstarts beteendet efter installation/avinstallation av en uppdatering. |
| _ResourceId | Unikt ID för den resurs som är associerad med posten. |
| Typ | Posttyp. Värdet är Update. |
| VMUUID | Unik identifierare för den virtuella datorn. |
| MG | Unik identifierare för hanterings gruppen eller Log Analytics arbets ytan. |
| TenantId | Unik identifierare som representerar organisationens instans av Azure Active Directory. |
| SourceSystem | Käll systemet för posten. Värdet är `OperationsManager`. |
| TimeGenerated | Datum och tid då posten skapades. |
| SourceComputerId | Unik identifierare som representerar käll datorn. |
| Rubrik | Uppdateringens rubrik. |
| PublishedDate (UTC) | Det datum då uppdateringen är redo att laddas ned och installeras från Windows Update.  |
| UpdateState | Uppdateringens aktuella tillstånd. |
| Produkt | De produkter som uppdateringen gäller. |
| SubscriptionId | Unik identifierare för Azure-prenumerationen. |
| ResourceGroup | Namnet på resurs gruppen som resursen tillhör. |
| ResourceProvider | Resurs leverantören. |
| Resurs | Namn på resursen. |
| ResourceType | Resurs typen. |

### <a name="query-update-agent-record"></a>Uppdatera agent posten för fråga

En post med en typ av `UpdateAgent` skapas som innehåller information om uppdaterings agenten på datorn. Dessa poster har egenskaperna i följande tabell:

| Egenskap | Beskrivning |
|----------|-------------|
| AgeofOldestMissingRequiredUpdate | |
| AutomaticUpdateEnabled | |
| Dator | Fullständigt kvalificerat domän namn för rapporterings datorn. |
| DaySinceLastUpdateBucket | |
| ManagementGroupName | Namnet på den Operations Manager hanterings gruppen eller Log Analytics arbets ytan. |
| OSVersion | Operativ systemets version. |
| Server | |
| SourceHealthServiceId | Unik identifierare som representerar Log Analytics Windows agent-ID. |
| SourceSystem | Käll systemet för posten. Värdet är `OperationsManager`. |
| TenantId | Unik identifierare som representerar organisationens instans av Azure Active Directory. |
| TimeGenerated | Datum och tid då posten skapades. |
| Typ | Posttyp. Värdet är Update. |
| WindowsUpdateAgentVersion | Windows Update agentens version. |
| WSUSServer | Fel om Windows Update Agent har problem, för att under lätta fel sökningen. |

### <a name="query-update-deployment-status-record"></a>Uppdatera distributions status post för fråga

En post med en typ av `UpdateRunProgress` skapas som tillhandahåller uppdaterings distributions status för en schemalagd distribution per dator. Dessa poster har egenskaperna i följande tabell:

| Egenskap | Beskrivning |
|----------|-------------|
| Dator | Fullständigt kvalificerat domän namn för rapporterings datorn. |
| ComputerEnvironment | Miljö. Värdena är Azure eller icke-Azure. |
| CorrelationId | Unik identifierare för Runbook-jobbet som körs för uppdateringen. |
| EndTime | Tiden då synkroniseringsprocessen avslutades. *Den här egenskapen används inte för närvarande. Se TimeGenerated.* |
| ErrorResult | Windows Update felkod som genereras om en uppdatering inte kan installeras. |
| Updaterunprogress | Eventuella installations tillstånd för en uppdatering på klient datorn.<br> `NotStarted` -jobbet har inte utlösts ännu.<br> `FailedToStart` -Det gick inte att starta jobbet på datorn.<br> `Failed` -jobbet startade men misslyckades med ett undantag.<br> `InProgress` – pågående jobb.<br> `MaintenanceWindowExceeded` – om körningen var återstående men underhålls periodens intervall har uppnåtts.<br> `Succeeded` -jobbet har slutförts.<br> `InstallFailed` -Det gick inte att installera uppdateringen.<br> `NotIncluded`<br> `Excluded` |
| KBID | Kunskaps bas artikel-ID för Windows Update. |
| ManagementGroupName | Namnet på den Operations Manager hanterings gruppen eller Log Analytics arbets ytan. |
| OSType | Typ av operativ system. Värdena är Windows eller Linux. |
| Produkt | De produkter som uppdateringen gäller. |
| Resurs | Namn på resursen. |
| ResourceId | Unikt ID för den resurs som är associerad med posten. |
| ResourceProvider | Resurs leverantören. |
| ResourceType | Resurstyp. |
| SourceComputerId | Unik identifierare som representerar käll datorn. |
| SourceSystem | Käll system för posten. Värdet är `OperationsManager`. |
| StartTime | Tid när uppdateringen har schemalagts för installation. *Den här egenskapen används inte för närvarande. Se TimeGenerated.* |
| SubscriptionId | Unik identifierare för Azure-prenumerationen. |
| SucceededOnRetry | Värde som anger om uppdaterings körningen misslyckades för det första försöket och den aktuella åtgärden är ett försök att försöka igen. |
| TimeGenerated | Datum och tid då posten skapades. |
| Rubrik | Uppdateringens rubrik. |
| Typ | Typ av uppdatering. Värdet är `UpdateRunProgress`. |
| UpdateId | Unikt ID för program uppdateringen. |
| VMUUID | Unik identifierare för den virtuella datorn. |
| ResourceId | Unikt ID för den resurs som är associerad med posten. |

### <a name="query-update-summary-record"></a>Samlings post för uppdatering av fråga

En post med en typ av `UpdateSummary` skapas som tillhandahåller uppdaterings Sammanfattning per dator. Dessa poster har egenskaperna i följande tabell:

| Egenskap | Beskrivning |
|----------|-------------|
| Dator | Fullständigt kvalificerat domän namn för rapporterings datorn. |
| ComputerEnvironment | Miljö. Värdena är Azure eller icke-Azure. |
| CriticalUpdatesMissing | Antalet tillämpliga kritiska uppdateringar som saknas. |
| ManagementGroupName | Namnet på den Operations Manager hanterings gruppen eller Log Analytics arbets ytan. |
| NETRuntimeVersion | Versionen av .NET Framework installerad på Windows-datorn. |
| OldestMissingSecurityUpdateBucket | Specificeraren för den äldsta säkerhets Bucket som saknas. Värden är:<br> Senaste om värdet är mindre än 30 dagar<br> för 30 dagar sedan<br> för 60 dagar sedan<br> för 90 dagar sedan<br> för 120 dagar sedan<br> för 150 dagar sedan<br> för 180 dagar sedan<br> Äldre när värdet är större än 180 dagar. |
| OldestMissingSecurityUpdateInDays | Totalt antal dagar för den äldsta uppdateringen som har identifierats som tillämpligt och som inte har installerats. |
| OsVersion | Operativ systemets version. |
| OtherUpdatesMissing | Antal identifierade uppdateringar som saknas. |
| Resurs | Namnet på resurs posten. |
| ResourceGroup | Namnet på den resurs grupp som innehåller resursen. |
| ResourceId | Unikt ID för den resurs som är associerad med posten. |
| ResourceProvider | Resurs leverantören. |
| ResourceType | Resurstyp. |
| RestartPending | Sant om en omstart väntar eller annars FALSE. |
| SecurityUpdatesMissing | Antal saknade säkerhets uppdateringar som är tillämpliga.|
| SourceComputerId | Unik identifierare för den virtuella datorn. |
| SourceSystem | Käll system för posten. Värdet är `OpsManager`. |
| SubscriptionId | Unik identifierare för Azure-prenumerationen. |
| TimeGenerated | Datum och tid då posten skapades. |
| TotalUpdatesMissing | Totalt antal saknade uppdateringar som är tillämpliga. |
| Typ | Posttyp. Värdet är `UpdateSummary`. |
| VMUUID | Unik identifierare för den virtuella datorn. |
| WindowsUpdateAgentVersion | Windows Update agentens version. |
| WindowsUpdateSetting | Status för Windows Update agenten. Möjliga värden:<br> `Scheduled installation`<br> `Notify before installation`<br> `Error returned from unhealthy WUA agent` |
| WSUSServer | Fel om Windows Update Agent har problem, för att under lätta fel sökningen. |
| _ResourceId | Unikt ID för den resurs som är associerad med posten. |

## <a name="sample-queries"></a>Exempelfrågor

I följande avsnitt finns exempel på logg frågor för uppdaterings poster som samlas in för Uppdateringshantering.

### <a name="confirm-that-non-azure-machines-are-enabled-for-update-management"></a>Bekräfta att icke-Azure-datorer är aktiverade för Uppdateringshantering

Du kan kontrol lera att direktanslutna datorer kommunicerar med Azure Monitor loggar genom att köra någon av följande loggs ökningar.

#### <a name="linux"></a>Linux

```loganalytics
Heartbeat
| where OSType == "Linux" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

#### <a name="windows"></a>Windows

```loganalytics
Heartbeat
| where OSType == "Windows" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

På en Windows-dator kan du granska följande information för att verifiera agent anslutningen med Azure Monitor loggar:

1. Öppna **Microsoft Monitoring Agent** i kontroll panelen. På fliken **Azure-Log Analytics** visar agenten följande meddelande: **Microsoft Monitoring Agent har anslutit till Log Analytics**.

1. Öppna händelse loggen i Windows. Gå till **program-och tjänst loggar \ Operations Manager** och Sök efter händelse-ID 3000 och händelse-ID 5002 från käll **tjänst anslutningen**. Dessa händelser anger att datorn har registrerats på Log Analytics-arbetsytan och tar emot konfigurationen.

Om agenten inte kan kommunicera med Azure Monitor loggar och agenten är konfigurerad för att kommunicera med Internet via en brand vägg eller proxyserver, kontrollerar du att brand väggen eller proxyservern har kon figurer ATS korrekt. Information om hur du verifierar att brand väggen eller proxyservern har kon figurer ATS korrekt finns i [nätverks konfiguration för Windows-agent](../../azure-monitor/agents/agent-windows.md) eller [nätverks konfiguration för Linux-agenten](../../azure-monitor/vm/quick-collect-linux-computer.md).

> [!NOTE]
> Om Linux-systemen har kon figurer ATS för att kommunicera med en proxy eller Log Analytics gateway och du aktiverar Uppdateringshantering, uppdaterar du `proxy.conf` behörigheterna för att ge behörigheten omiuser Group Läs behörighet för filen genom att använda följande kommandon:
>
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

Nyligen tillagda Linux-agenter visar statusen **uppdaterad** när en utvärdering har utförts. Den här processen kan ta upp till 6 timmar.

För att bekräfta att en Operations Manager hanterings grupp kommunicerar med Azure Monitor loggar, se [verifiera Operations Manager integrering med Azure Monitor loggar](../../azure-monitor/agents/om-agents.md#validate-operations-manager-integration-with-azure-monitor).

### <a name="single-azure-vm-assessment-queries-windows"></a>Enskilda utvärderings frågor för Azure VM (Windows)

Ersätt VMUUID-värdet med VM-GUID för den virtuella dator som du frågar. Du kan hitta VMUUID som ska användas genom att köra följande fråga i Azure Monitor loggar: `Update | where Computer == "<machine name>" | summarize by Computer, VMUUID`

#### <a name="missing-updates-summary"></a>Sammanfattning av saknade uppdateringar

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"b08d5afa-1471-4b52-bd95-a44fea6e4ca8"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="missing-updates-list"></a>Lista med uppdateringar som saknas

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"8bf1ccc6-b6d3-4a0b-a643-23f346dfdf82"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

### <a name="single-azure-vm-assessment-queries-linux"></a>Enkla Azure VM Assessment-frågor (Linux)

För vissa Linux-distributioner, [finns det en felaktig matchning](https://en.wikipedia.org/wiki/Endianness) med VMUUID-värdet som kommer från Azure Resource Manager och vad som lagras i Azure Monitor loggar. Följande fråga söker efter en matchning på antingen endian. Ersätt VMUUID-värdena med big-endian-och lite-endian-formatet för GUID för att returnera resultaten korrekt. Du kan hitta VMUUID som ska användas genom att köra följande fråga i Azure Monitor loggar: `Update | where Computer == "<machine name>"
| summarize by Computer, VMUUID`

#### <a name="missing-updates-summary"></a>Sammanfattning av saknade uppdateringar

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="missing-updates-list"></a>Lista med uppdateringar som saknas

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl

```

### <a name="multi-vm-assessment-queries"></a>Frågor om utvärdering av flera virtuella datorer

#### <a name="computers-summary"></a>Sammanfattning av datorer

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Approved, Optional, Classification) by SourceComputerId, UpdateID
    | distinct SourceComputerId, Classification, UpdateState, Approved, Optional
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed" and (Optional==false or Classification has "Critical" or Classification has "Security") and Approved!=false, iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity
| union (Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by SourceComputerId, Product, ProductArch
    | distinct SourceComputerId, Classification, UpdateState
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed", iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity)
| summarize assessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity>-1), notAssessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==-1), computersNeedCriticalUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==4), computersNeedSecurityUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==2), computersNeedOtherUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==1), upToDateComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==0)
| summarize assessedComputersCount=sum(assessedComputersCount), computersNeedCriticalUpdatesCount=sum(computersNeedCriticalUpdatesCount),  computersNeedSecurityUpdatesCount=sum(computersNeedSecurityUpdatesCount), computersNeedOtherUpdatesCount=sum(computersNeedOtherUpdatesCount), upToDateComputersCount=sum(upToDateComputersCount), notAssessedComputersCount=sum(notAssessedComputersCount)
| extend allComputersCount=assessedComputersCount+notAssessedComputersCount
```

#### <a name="missing-updates-summary"></a>Sammanfattning av saknade uppdateringar

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| union (Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification )
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="computers-list"></a>Lista med datorer

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Product, Computer, ComputerEnvironment) by SourceComputerId, Product, ProductArch
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed"), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed"), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed"), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2)
| union(Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, Optional, Approved, Computer, ComputerEnvironment) by Computer, SourceComputerId, UpdateID
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed" and Approved!=false), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed" and Approved!=false), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed" and Optional==false and Approved!=false), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2) )
| order by ComplianceOrder asc, missingCriticalUpdatesCount desc, missingSecurityUpdatesCount desc, missingOtherUpdatesCount desc, displayName asc
| project-away ComplianceOrder
```

#### <a name="missing-updates-list"></a>Lista med uppdateringar som saknas

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| union(Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2)
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

## <a name="next-steps"></a>Nästa steg

* Mer information om Azure Monitor-loggar finns i [Azure Monitor loggar](../../azure-monitor/logs/log-query-overview.md).
* Hjälp med aviseringar finns i [Konfigurera aviseringar](configure-alerts.md).
