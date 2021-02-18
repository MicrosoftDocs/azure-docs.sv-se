---
title: Exempel på Azure Monitor logg frågor | Microsoft Docs
description: Exempel på logg frågor i Azure Monitor att använda frågespråket i Kusto.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/16/2020
ms.openlocfilehash: 213437e68efb27c483c8013d209261408ab9d4ea
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100625739"
---
# <a name="azure-monitor-log-query-examples"></a>Exempel på Azure Monitor logg frågor
Den här artikeln innehåller olika exempel på [frågor](../log-query/log-query-overview.md) som använder [Kusto-frågespråket](/azure/kusto/query/) för att hämta olika typer av loggdata från Azure Monitor. Olika metoder används för att konsolidera och analysera data, så att du kan använda dessa exempel för att identifiera olika strategier som du kan använda för dina egna krav.  

Mer information om de olika nyckelord som används i de här exemplen finns i [språk referens för Kusto](/azure/kusto/query/) . Gå igenom en [lektion om hur du skapar frågor](../log-query/get-started-queries.md) om du är nybörjare på Azure Monitor.

## <a name="events"></a>Händelser

### <a name="search-application-level-events-described-as-cryptographic"></a>Sök efter program nivå händelser som beskrivs som "kryptografisk"
I det här exemplet genomsöks tabellen **Events** efter poster där **EventLog** är _Application_ och **RenderedDescription** innehåller _kryptografiskt_. Inkluderar poster från de senaste 24 timmarna.

```Kusto
Event
| where EventLog == "Application" 
| where TimeGenerated > ago(24h) 
| where RenderedDescription contains "cryptographic"
```

### <a name="search-events-related-to-unmarshaling"></a>Sök händelser relaterade till konvertering
Sök tabell **händelse** -och **SecurityEvents** efter poster som nämner _konvertering_.

```Kusto
search in (Event, SecurityEvent) "unmarshaling"
```

## <a name="heartbeat"></a>Pulsslag

### <a name="chart-a-week-over-week-view-of-the-number-of-computers-sending-data"></a>Visa en veckas över-Week-vy över antalet datorer som skickar data

I följande exempel visas antalet distinkta datorer som har skickat pulsslag, varje vecka.

```Kusto
Heartbeat
| where TimeGenerated >= startofweek(ago(21d))
| summarize dcount(Computer) by endofweek(TimeGenerated) | render barchart kind=default
```

### <a name="find-stale-computers"></a>Hitta inaktuella datorer

I följande exempel hittas datorer som var aktiva under den senaste dagen, men som inte skickade pulsslag under den senaste timmen.

```Kusto
Heartbeat
| where TimeGenerated > ago(1d)
| summarize LastHeartbeat = max(TimeGenerated) by Computer
| where isnotempty(Computer)
| where LastHeartbeat < ago(1h)
```

### <a name="get-the-latest-heartbeat-record-per-computer-ip"></a>Hämta den senaste pulsslags posten per dator-IP

Det här exemplet returnerar den sista pulsslags posten för varje dator-IP.
```Kusto
Heartbeat
| summarize arg_max(TimeGenerated, *) by ComputerIP
```

### <a name="match-protected-status-records-with-heartbeat-records"></a>Matcha skyddade status poster med pulsslags poster

Det här exemplet hittar relaterade skydds status poster och pulsslags poster, matchade på både dator och tid.
Observera att Time-fältet rundas av till närmaste minut. Vi använde beräkning av kör tids lager för att göra det: `round_time=bin(TimeGenerated, 1m)` .

```Kusto
let protection_data = ProtectionStatus
    | project Computer, DetectionId, round_time=bin(TimeGenerated, 1m);
let heartbeat_data = Heartbeat
    | project Computer, Category, round_time=bin(TimeGenerated, 1m);
protection_data | join (heartbeat_data) on Computer, round_time
```

### <a name="server-availability-rate"></a>Server tillgänglighets hastighet

Beräkna Server tillgänglighets frekvens baserat på pulsslags poster. Tillgänglighet definieras som "minst 1 pulsslag per timme".
Så om en server var tillgänglig 98 av 100 timmar är tillgänglighets hastigheten 98%.

```Kusto
let start_time=startofday(datetime("2018-03-01"));
let end_time=now();
Heartbeat
| where TimeGenerated > start_time and TimeGenerated < end_time
| summarize heartbeat_per_hour=count() by bin_at(TimeGenerated, 1h, start_time), Computer
| extend available_per_hour=iff(heartbeat_per_hour>0, true, false)
| summarize total_available_hours=countif(available_per_hour==true) by Computer 
| extend total_number_of_buckets=round((end_time-start_time)/1h)+1
| extend availability_rate=total_available_hours*100/total_number_of_buckets
```


## <a name="multiple-data-types"></a>Flera data typer

### <a name="chart-the-record-count-per-table"></a>Diagrammets antal poster per tabell
Följande exempel samlar in alla poster från alla tabeller från de senaste fem timmarna och räknar hur många poster som fanns i varje tabell. Resultaten visas i en timechart.

```Kusto
union withsource=sourceTable *
| where TimeGenerated > ago(5h) 
| summarize count() by bin(TimeGenerated,10m), sourceTable
| render timechart
```

### <a name="count-all-logs-collected-over-the-last-hour-by-type"></a>Räkna alla loggar som samlats in under den senaste timmen efter typ
I följande exempel genomsöker allting som rapporter ATS under den senaste timmen och räknar posterna i varje tabell efter **typ**. Resultaten visas i ett stapeldiagram.

```Kusto
search *
| where TimeGenerated > ago(1h) 
| summarize CountOfRecords = count() by Type
| render barchart
```

## <a name="azurediagnostics"></a>AzureDiagnostics

### <a name="count-azure-diagnostics-records-per-category"></a>Räkna Azure Diagnostic-poster per kategori
Det här exemplet räknar alla Azure Diagnostic-poster för varje unik kategori.

```Kusto
AzureDiagnostics 
| where TimeGenerated > ago(1d)
| summarize count() by Category
```

### <a name="get-a-random-record-for-each-unique-category"></a>Hämta en slumpmässig post för varje unik kategori
I det här exemplet får du en enda slumpmässig Azure-diagnostik för varje unik kategori.

```Kusto
AzureDiagnostics
| where TimeGenerated > ago(1d) 
| summarize any(*) by Category
```

### <a name="get-the-latest-record-per-category"></a>Hämta den senaste posten per kategori
Det här exemplet hämtar den senaste Azure Diagnostics-posten i varje unik kategori.

```Kusto
AzureDiagnostics
| where TimeGenerated > ago(1d) 
| summarize arg_max(TimeGenerated, *) by Category
```

## <a name="network-monitoring"></a>Nätverksövervakning

### <a name="computers-with-unhealthy-latency"></a>Datorer med felaktig svars tid
I det här exemplet skapas en lista med distinkta datorer med en svars tid som inte är felfria.

```Kusto
NetworkMonitoring 
| where LatencyHealthState <> "Healthy" 
| where Computer != "" 
| distinct Computer
```

## <a name="performance"></a>Prestanda

### <a name="join-computer-perf-records-to-correlate-memory-and-cpu"></a>Anslut datorns perf-poster för att korrelera minne och CPU
Det här exemplet motsvarar en viss dators **prestanda** poster och skapar två tids diagram, genomsnittlig CPU och maximalt minne.

```Kusto
let StartTime = now()-5d;
let EndTime = now()-4d;
Perf
| where CounterName == "% Processor Time"  
| where TimeGenerated > StartTime and TimeGenerated < EndTime
| project TimeGenerated, Computer, cpu=CounterValue 
| join kind= inner (
   Perf
    | where CounterName == "% Used Memory"  
    | where TimeGenerated > StartTime and TimeGenerated < EndTime
    | project TimeGenerated , Computer, mem=CounterValue 
) on TimeGenerated, Computer
| summarize avgCpu=avg(cpu), maxMem=max(mem) by TimeGenerated bin=30m  
| render timechart
```

### <a name="perf-cpu-utilization-graph-per-computer"></a>Diagram över prestanda processor användning per dator
Det här exemplet beräknar och diagramerar processor användningen för datorer som börjar med _contoso_.

```Kusto
Perf
| where TimeGenerated > ago(4h)
| where Computer startswith "Contoso" 
| where CounterName == @"% Processor Time"
| summarize avg(CounterValue) by Computer, bin(TimeGenerated, 15m) 
| render timechart
```

## <a name="protection-status"></a>Skydds status

### <a name="computers-with-non-reporting-protection-status-duration"></a>Datorer med status varaktighet för icke-rapporterings skydd
I det här exemplet visas datorer som har en skydds status som _inte rapporterar_  och den varaktighet som de hade i denna status.

```Kusto
ProtectionStatus
| where ProtectionStatus == "Not Reporting"
| summarize count(), startNotReporting = min(TimeGenerated), endNotReporting = max(TimeGenerated) by Computer, ProtectionStatusDetails
| join ProtectionStatus on Computer
| summarize lastReporting = max(TimeGenerated), startNotReporting = any(startNotReporting), endNotReporting = any(endNotReporting) by Computer
| extend durationNotReporting = endNotReporting - startNotReporting
```

### <a name="match-protected-status-records-with-heartbeat-records"></a>Matcha skyddade status poster med pulsslags poster
I det här exemplet hittas relaterade skydds status poster och pulsslags poster som matchar både dator och tid.
Time-fältet avrundas till närmaste minut med hjälp av **bin**.

```Kusto
let protection_data = ProtectionStatus
    | project Computer, DetectionId, round_time=bin(TimeGenerated, 1m);
let heartbeat_data = Heartbeat
    | project Computer, Category, round_time=bin(TimeGenerated, 1m);
protection_data | join (heartbeat_data) on Computer, round_time
```


## <a name="security-records"></a>Säkerhets poster

### <a name="count-security-events-by-activity-id"></a>Räkna säkerhets händelser per aktivitets-ID


Det här exemplet använder den fasta strukturen i **aktivitets** kolumnen: \<ID\> - \<Name\> .
Det tolkar **aktivitets** värdet i två nya kolumner och räknar förekomsten av varje **activityID**.

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m) 
| project Activity 
| parse Activity with activityID " - " activityDesc
| summarize count() by activityID
```

### <a name="count-security-events-related-to-permissions"></a>Räkna säkerhets händelser relaterade till behörigheter
I det här exemplet visas antalet **securityEvent** -poster, där **aktivitets** kolumnen innehåller hela term _behörigheterna_. Frågan gäller poster som skapats under de senaste 30 minuterna.

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m)
| summarize EventCount = countif(Activity has "Permissions")
```

### <a name="find-accounts-that-failed-to-log-in-from-computers-with-a-security-detection"></a>Hitta konton som inte kunde logga in från datorer med en säkerhets identifiering
Det här exemplet hittar och räknar konton som inte kunde logga in från datorer där vi identifierar en säkerhets identifiering.

```Kusto
let detections = toscalar(SecurityDetection
| summarize makeset(Computer));
SecurityEvent
| where Computer in (detections) and EventID == 4624
| summarize count() by Account
```

### <a name="is-my-security-data-available"></a>Är mina säkerhets data tillgängliga?
Att starta data utforskningen börjar ofta med data tillgänglighets kontroll. I det här exemplet visas antalet **SecurityEvent** -poster under de senaste 30 minuterna.

```Kusto
SecurityEvent 
| where TimeGenerated  > ago(30m) 
| count
```

### <a name="parse-activity-name-and-id"></a>Namn och ID för parse-aktivitet
De två exemplen nedan förlitar sig på den fasta strukturen i **aktivitets** kolumnen: \<ID\> - \<Name\> . I det första exemplet används **parse** -operatorn för att tilldela värden till två nya kolumner: **activityID** och **activityDesc**.

```Kusto
SecurityEvent
| take 100
| project Activity 
| parse Activity with activityID " - " activityDesc
```

I det här exemplet används operatorn **Split** för att skapa en matris med separata värden
```Kusto
SecurityEvent
| take 100
| project Activity 
| extend activityArr=split(Activity, " - ") 
| project Activity , activityArr, activityId=activityArr[0]
```

### <a name="explicit-credentials-processes"></a>Explicita autentiseringsuppgifter-processer
I följande exempel visas ett cirkel diagram med processer som använde explicita autentiseringsuppgifter under den senaste veckan

```Kusto
SecurityEvent
| where TimeGenerated > ago(7d)
// filter by id 4648 ("A logon was attempted using explicit credentials")
| where EventID == 4648
| summarize count() by Process
| render piechart 
```

### <a name="top-running-processes"></a>Vanligaste processer som körs

I följande exempel visas en tids linje med aktiviteter för de fem vanligaste processerna under de tre senaste dagarna.

```Kusto
// Find all processes that started in the last three days. ID 4688: A new process has been created.
let RunProcesses = 
    SecurityEvent
    | where TimeGenerated > ago(3d)
    | where EventID == "4688";
// Find the 5 processes that were run the most
let Top5Processes =
RunProcesses
| summarize count() by Process
| top 5 by count_;
// Create a time chart of these 5 processes - hour by hour
RunProcesses 
| where Process in (Top5Processes) 
| summarize count() by bin (TimeGenerated, 1h), Process
| render timechart
```


### <a name="find-repeating-failed-login-attempts-by-the-same-account-from-different-ips"></a>Hitta upprepade misslyckade inloggnings försök av samma konto från olika IP-adresser

I följande exempel hittas misslyckade inloggnings försök av samma konto från fler än fem olika IP-adresser under de senaste sex timmarna.

```Kusto
SecurityEvent 
| where AccountType == "User" and EventID == 4625 and TimeGenerated > ago(6h) 
| summarize IPCount = dcount(IpAddress), makeset(IpAddress)  by Account
| where IPCount > 5
| sort by IPCount desc
```

### <a name="find-user-accounts-that-failed-to-log-in"></a>Sök efter användar konton som inte kunde logga in 
I följande exempel identifieras användar konton som inte kunde logga in mer än fem gånger under den senaste dagen, och när de senast försökte logga in.

```Kusto
let timeframe = 1d;
SecurityEvent
| where TimeGenerated > ago(1d)
| where AccountType == 'User' and EventID == 4625 // 4625 - failed log in
| summarize failed_login_attempts=count(), latest_failed_login=arg_max(TimeGenerated, Account) by Account 
| where failed_login_attempts > 5
| project-away Account1
```

Med hjälp av **Join** och **let** -satser kan vi kontrol lera om samma misstänkta konton senare kunde logga in.

```Kusto
let timeframe = 1d;
let suspicious_users = 
    SecurityEvent
    | where TimeGenerated > ago(timeframe)
    | where AccountType == 'User' and EventID == 4625 // 4625 - failed login
    | summarize failed_login_attempts=count(), latest_failed_login=arg_max(TimeGenerated, Account) by Account 
    | where failed_login_attempts > 5
    | project-away Account1;
let suspicious_users_that_later_logged_in = 
    suspicious_users 
    | join kind=innerunique (
        SecurityEvent
        | where TimeGenerated > ago(timeframe)
        | where AccountType == 'User' and EventID == 4624 // 4624 - successful login,
        | summarize latest_successful_login=arg_max(TimeGenerated, Account) by Account
    ) on Account
    | extend was_login_after_failures = iif(latest_successful_login>latest_failed_login, 1, 0)
    | where was_login_after_failures == 1
;
suspicious_users_that_later_logged_in
```

## <a name="usage"></a>Användning

`Usage`Data typen kan användas för att spåra den inmatade data volymen efter lösning eller datatyp. Det finns andra metoder för att undersöka inmatade data volymer av [dator](../platform/manage-cost-storage.md#data-volume-by-computer) eller [Azure-prenumeration, resurs grupp eller resurs](../platform/manage-cost-storage.md#data-volume-by-azure-resource-resource-group-or-subscription).

#### <a name="data-volume-by-solution"></a>Datavolym per lösning

Frågan som används för att visa den fakturerbara data volymen per lösning under den senaste månaden (exklusive den sista del dagen) är:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), Solution | render barchart
```

Observera att-satsen `where IsBillable = true` filtrerar bort data typer från vissa lösningar som det inte finns någon inmatnings avgift för.  Även-satsen med `TimeGenerated` är att se till att frågans upplevelse i Azure Portal kommer att se tillbaka utöver standard 24 timmarna. När du använder data typen användning `StartTime` och representerar de tidsgrupper `EndTime` som resultat visas för. 

#### <a name="data-volume-by-type"></a>Data volym efter typ

Du kan öka detalj nivån för att se data trender för data typen:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), DataType | render barchart
```

Eller för att se en tabell efter lösning och typ för den senaste månaden

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by Solution, DataType
| sort by Solution asc, DataType asc
```

> [!NOTE]
> Några av fälten i användnings data typen, men fortfarande i schemat, är inaktuella och de kommer inte längre att fyllas i. Dessa är både **datorer** och fält som rör inmatning (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**, **BatchesCapped** och **AverageProcessingTimeMs**.

## <a name="updates"></a>Uppdateringar

### <a name="computers-still-missing-updates"></a>Datorer som fortfarande saknar uppdateringar
Det här exemplet visar en lista över datorer som saknade en eller flera viktiga uppdateringar några dagar sedan och som fortfarande saknar uppdateringar.

```Kusto
let ComputersMissingUpdates3DaysAgo = Update
| where TimeGenerated between (ago(30d)..ago(1h))
| where Classification !has "Critical" and UpdateState =~ "Needed"
| summarize makeset(Computer);
Update
| where TimeGenerated > ago(1d)
| where Classification has "Critical" and UpdateState =~ "Needed"
| where Computer in (ComputersMissingUpdates3DaysAgo)
| summarize UniqueUpdatesCount = dcount(Product) by Computer, OSType
```


## <a name="next-steps"></a>Nästa steg

- Mer information om språket finns i [språk referens för Kusto](/azure/kusto/query) .
- Gå igenom en [lektion om hur du skriver logg frågor i Azure Monitor](../log-query/get-started-queries.md).
