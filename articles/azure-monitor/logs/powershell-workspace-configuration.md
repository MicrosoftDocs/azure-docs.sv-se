---
title: Skapa & konfigurera Log Analytics med PowerShell
description: Log Analytics arbets ytor i Azure Monitor lagra data från servrar i din lokala infrastruktur eller i molnet. Du kan samla in maskin data från Azure Storage när de genereras av Azure Diagnostics.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/26/2020
ms.openlocfilehash: d876a380bfc2d318cddc0964266cc3f0a870aa16
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102050995"
---
# <a name="create-and-configure-a-log-analytics-workspace-in-azure-monitor-using-powershell"></a>Skapa och konfigurera en Log Analytics arbets yta i Azure Monitor med PowerShell
Den här artikeln innehåller två kod exempel som visar hur du skapar och konfigurerar en Log Analytics arbets yta i Azure Monitor.  


> [!NOTE]
> Log Analytics kallades tidigare Operational Insights, vilket är anledningen till att det är det namn som används i cmdletarna.


## <a name="prerequisites"></a>Förutsättningar
Dessa exempel fungerar med version 1.0.0 eller senare av modulen AZ. OperationalInsights.

## <a name="create-workspace"></a>Skapa arbetsyta
Följande exempel skript skapar en arbets yta utan konfiguration av data källor. 

```powershell
$ResourceGroup = "my-resource-group"
$WorkspaceName = "log-analytics-" + (Get-Random -Maximum 99999) # workspace names need to be unique across all Azure subscriptions - Get-Random helps with this for the example code
$Location = "westeurope"

# Create the resource group if needed
try {
    Get-AzResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzResourceGroup -Name $ResourceGroup -Location $Location
}

# Create the workspace
New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup
```

## <a name="create-workspace-and-configure-data-sources"></a>Skapa arbets yta och konfigurera data källor

Följande exempel skript skapar en arbets yta och konfigurerar flera data källor. Dessa data källor krävs endast om du övervakar virtuella datorer med hjälp av [Log Analytics agenten](../agents/log-analytics-agent.md).

Det här skriptet utför följande funktioner:

1. Skapa en arbetsyta
2. Visa en lista över tillgängliga lösningar
3. Lägg till lösningar i arbets ytan
4. Importera sparade sökningar
5. Exportera sparade sökningar
6. Skapa en dator grupp
7. Aktivera insamling av IIS-loggar från datorer där Windows-agenten är installerad
8. Samla in prestanda räknare för logiska diskar från Linux-datorer (% använt noder i procent; Lediga megabyte; Använt utrymme i procent. Disk överföringar/SEK; Disk läsningar/s; Disk skrivningar/s)
9. Samla in Syslog-händelser från Linux-datorer
10. Samla in fel-och varnings händelser från program händelse loggen från Windows-datorer
11. Samla in tillgängligt minne i megabyte för prestanda räknare från Windows-datorer
12. Samla in en anpassad logg

```powershell
$ResourceGroup = "my-resource-group"
$WorkspaceName = "log-analytics-" + (Get-Random -Maximum 99999) # workspace names need to be unique across all Azure subscriptions - Get-Random helps with this for the example code
$Location = "westeurope"

# Create the resource group if needed
try {
    Get-AzResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzResourceGroup -Name $ResourceGroup -Location $Location
}

# Create the workspace
New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup

# List of solutions to enable
$Solutions = "Security", "Updates", "SQLAssessment"

# Saved Searches to import
$ExportedSearches = @"
[
    {
        "Category":  "My Saved Searches",
        "DisplayName":  "WAD Events (All)",
        "Query":  "Type=Event SourceSystem:AzureStorage ",
        "Version":  1
    },
    {
        "Category":  "My Saved Searches",
        "DisplayName":  "Current Disk Queue Length",
        "Query":  "Perf | where ObjectName == "LogicalDisk" and CounterName == "Current Disk Queue Length" and InstanceName == "C:"",
        "Version":  1
    }
]
"@ | ConvertFrom-Json

# Custom Log to collect
$CustomLog = @"
{
    "customLogName": "sampleCustomLog1",
    "description": "Example custom log datasource",
    "inputs": [
        {
            "location": {
            "fileSystemLocations": {
                "windowsFileTypeLogPaths": [ "e:\\iis5\\*.log" ],
                "linuxFileTypeLogPaths": [ "/var/logs" ]
                }
            },
        "recordDelimiter": {
            "regexDelimiter": {
                "pattern": "\\n",
                "matchIndex": 0,
                "matchIndexSpecified": true,
                "numberedGroup": null
                }
            }
        }
    ],
    "extractions": [
        {
            "extractionName": "TimeGenerated",
            "extractionType": "DateTime",
            "extractionProperties": {
                "dateTimeExtraction": {
                    "regex": null,
                    "joinStringRegex": null
                    }
                }
            }
        ]
    }
"@

# Create the resource group if needed
try {
    Get-AzResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzResourceGroup -Name $ResourceGroup -Location $Location
}

# Create the workspace
New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup

# List all solutions and their installation status
Get-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Add solutions
foreach ($solution in $Solutions) {
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true
}

# List enabled solutions
(Get-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Where({($_.enabled -eq $true)})

# Import Saved Searches
foreach ($search in $ExportedSearches) {
    $id = $search.Category + "|" + $search.DisplayName
    New-AzOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId $id -DisplayName $search.DisplayName -Category $search.Category -Query $search.Query -Version $search.Version
}

# Export Saved Searches
(Get-AzOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Value.Properties | ConvertTo-Json

# Create Computer Group based on a query
New-AzOperationalInsightsComputerGroup -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId "My Web Servers" -DisplayName "Web Servers" -Category "My Saved Searches" -Query "Computer=""web*"" | distinct Computer" -Version 1

# Enable IIS Log Collection using agent
Enable-AzOperationalInsightsIISLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Perf
New-AzOperationalInsightsLinuxPerformanceObjectDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Logical Disk" -InstanceName "*"  -CounterNames @("% Used Inodes", "Free Megabytes", "% Used Space", "Disk Transfers/sec", "Disk Reads/sec", "Disk Writes/sec") -IntervalSeconds 20  -Name "Example Linux Disk Performance Counters"
Enable-AzOperationalInsightsLinuxPerformanceCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Syslog
New-AzOperationalInsightsLinuxSyslogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -Facility "kern" -CollectEmergency -CollectAlert -CollectCritical -CollectError -CollectWarning -Name "Example kernel syslog collection"
Enable-AzOperationalInsightsLinuxSyslogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Windows Event
New-AzOperationalInsightsWindowsEventDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -EventLogName "Application" -CollectErrors -CollectWarnings -Name "Example Application Event Log"

# Windows Perf
New-AzOperationalInsightsWindowsPerformanceCounterDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Memory" -InstanceName "*" -CounterName "Available MBytes" -IntervalSeconds 20 -Name "Example Windows Performance Counter"

# Custom Logs

New-AzOperationalInsightsCustomLogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -CustomLogRawJson "$CustomLog" -Name "Example Custom Log Collection"

```

> [!NOTE]
> Formatet för **CustomLogRawJson** -parametern som definierar konfigurationen för en anpassad logg kan vara komplext. Använd [Get-AzOperationalInsightsDataSource](/powershell/module/az.operationalinsights/get-azoperationalinsightsdatasource) för att hämta konfigurationen för en befintlig anpassad logg. Egenskapen **Properties** är den konfiguration som krävs för parametern **CustomLogRawJson** .

I exemplet ovan har regexDelimiter definierats som " \\ n" för ny rad. Logg avgränsaren kan också vara en tidsstämpel.  Följande format stöds:

| Format | JSON RegEx-formatet använder två `\\` för varje `\` i ett standard-regex, så om testning i en regex-app minskar `\\` till `\` |
| --- | --- |
| `YYYY-MM-DD HH:MM:SS` | `((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]` |
| `M/D/YYYY HH:MM:SS AM/PM` | `(([0-1]\\d)|[0-9])/(([0-3]\\d)|(\\d))/((\\d{2})|(\\d{4}))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]\\s(AM|PM|am|pm)` |
| `dd/MMM/yyyy HH:MM:SS` | `(([0-2][1-9]|[3][0-1])\\/(Jan|Feb|Mar|May|Apr|Jul|Jun|Aug|Oct|Sep|Nov|Dec|jan|feb|mar|may|apr|jul|jun|aug|oct|sep|nov|dec)\\/((19|20)[0-9][0-9]))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9])` |
| `MMM dd yyyy HH:MM:SS` | `(((?:Jan(?:uary)?|Feb(?:ruary)?|Mar(?:ch)?|Apr(?:il)?|May|Jun(?:e)?|Jul(?:y)?|Aug(?:ust)?|Sep(?:tember)?|Sept|Oct(?:ober)?|Nov(?:ember)?|Dec(?:ember)?)).*?((?:(?:[0-2]?\\d{1})|(?:[3][01]{1})))(?![\\d]).*?((?:(?:[1]{1}\\d{1}\\d{1}\\d{1})|(?:[2]{1}\\d{3})))(?![\\d]).*?((?:(?:[0-1][0-9])|(?:[2][0-3])|(?:[0-9])):(?:[0-5][0-9])(?::[0-5][0-9])?(?:\\s?(?:am|AM|pm|PM))?))` |
| `yyMMdd HH:mm:ss` | `([0-9]{2}([0][1-9]|[1][0-2])([0-2][0-9]|[3][0-1])\\s\\s?([0-1]?[0-9]|[2][0-3]):[0-5][0-9]:[0-5][0-9])` |
| `ddMMyy HH:mm:ss` | `(([0-2][0-9]|[3][0-1])([0][1-9]|[1][0-2])[0-9]{2}\\s\\s?([0-1]?[0-9]|[2][0-3]):[0-5][0-9]:[0-5][0-9])` |
| `MMM d HH:mm:ss` | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s\\s?([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0-1]?[0-9]|[2][0-3]):([0-5][0-9]):([0-5][0-9])` |
| `MMM  d HH:mm:ss` <br> två blank steg efter MMM | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s\\s([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])` |
| `MMM d HH:mm:ss` | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])` |
| `dd/MMM/yyyy:HH:mm:ss +zzzz` <br> där + är + eller a- <br> där zzzz tids förskjutning | `(([0-2][1-9]|[3][0-1])\\/(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\/((19|20)[0-9][0-9]):([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])\\s[\\+|\\-][0-9]{4})` |
| `yyyy-MM-ddTHH:mm:ss` <br> T är en literal bokstav T | `((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))T((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]` |

## <a name="troubleshooting"></a>Felsökning
När du skapar en arbets yta som har tagits bort under de senaste 14 dagarna och i [läget för mjuk borttagning](../logs/delete-workspace.md#soft-delete-behavior)kan åtgärden ha olika resultat beroende på konfigurationen för arbets ytan:
1. Om du anger samma namn på arbets ytan, resurs gruppen, prenumerationen och regionen som i den borttagna arbets ytan återställs din arbets yta, inklusive dess data, konfiguration och anslutna agenter.
2. Om du använder samma arbets ytans namn, men en annan resurs grupp, prenumeration eller region, får du ett fel meddelande om att arbets ytans namn *är inte unikt* eller *i konflikt*. Om du vill åsidosätta den mjuka borttagningen och permanent ta bort arbets ytan och skapa en ny arbets yta med samma namn, följer du dessa steg för att återställa arbets ytan och utföra permanent borttagning:
   * [Återställa](../logs/delete-workspace.md#recover-workspace) din arbetsyta
   * [Ta bort arbetsytan permanent](../logs/delete-workspace.md#permanent-workspace-delete)
   * Skapa en ny arbets yta med samma arbets ytans namn


## <a name="next-steps"></a>Nästa steg
* [Granska Log Analytics PowerShell-cmdletar](/powershell/module/az.operationalinsights/) för ytterligare information om hur du använder PowerShell för att konfigurera Log Analytics.
