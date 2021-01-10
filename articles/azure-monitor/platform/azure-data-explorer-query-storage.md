---
title: Fråga exporterade data från Azure Monitor med Azure Datautforskaren (förhands granskning)
description: Använd Azure Datautforskaren för att köra frågor mot data som har exporter ATS från din Log Analytics arbets yta till ett Azure Storage-konto.
ms.subservice: logs
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: 25452748e423cbbe819afa2618789b377027346b
ms.sourcegitcommit: 31cfd3782a448068c0ff1105abe06035ee7b672a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2021
ms.locfileid: "98060477"
---
# <a name="query-exported-data-from-azure-monitor-using-azure-data-explorer-preview"></a>Fråga exporterade data från Azure Monitor med Azure Datautforskaren (förhands granskning)
Export av data från Azure Monitor till ett Azure Storage-konto möjliggör låg kostnads kvarhållning och möjlighet att omfördela loggar till olika regioner. Använd Azure-Datautforskaren för att fråga data som har exporter ATS från dina Log Analytics arbets ytor. När den har kon figurer ATS kommer de tabeller som stöds som skickas från dina arbets ytor till ett Azure Storage-konto att vara tillgängliga som data källa för Azure-Datautforskaren.

Process flödet är följande: 

1.  Exportera data från Log Analytics arbets ytan till Azure Storage-kontot.
2.  Skapa en extern tabell i Azure Datautforskaren-klustret och mappa för data typerna.
3.  Fråga efter data från Azure Datautforskaren.

:::image type="content" source="media/azure-data-explorer-query-storage/exported-data-query.png" alt-text="Azure Datautforskaren exporterade data frågor om flöde.":::



## <a name="send-data-to-azure-storage"></a>Skicka data till Azure Storage
Azure Monitor loggar kan exporteras till ett Azure Storage konto med något av följande alternativ.

- Om du vill exportera alla data från din Log Analytics-arbetsyta till ett Azure Storage-konto eller en händelsehubben, använder du data export funktionen Log Analytics arbets yta för Azure Monitor loggar. Se [Log Analytics data export för arbets ytan i Azure Monitor (för hands version)](logs-data-export.md)
- Schemalagd export från en logg fråga med hjälp av en Logic app. Detta liknar data export funktionen, men du kan skicka filtrerade eller aggregerade data till Azure Storage. Den här metoden kan vara beroende av [loggnings frågans gränser](../service-limits.md#log-analytics-workspaces)  se [arkivera data från Log Analytics arbets yta till Azure Storage med hjälp av Logic app](logs-export-logic-app.md).
- Exportera med hjälp av en logisk app. Se [Azure Monitor logs Connector för Logic Apps och energi automatisering](logicapp-flow-connector.md).
- Exportera till en lokal dator med hjälp av PowerShell-skript. Se [Invoke-AzOperationalInsightsQueryExport](https://www.powershellgallery.com/packages/Invoke-AzOperationalInsightsQueryExport).

> [!TIP]
> Du kan använda ett befintligt Azure Datautforskaren-kluster eller skapa ett nytt dedikerat kluster med de konfigurationer som behövs.

## <a name="create-an-external-table-located-in-azure-blob-storage"></a>Skapa en extern tabell som finns i Azure Blob Storage
Använd [externa tabeller](/azure/data-explorer/kusto/query/schema-entities/externaltables) för att länka azure-datautforskaren till ett Azure Storage-konto. En extern tabell är en Kusto som refererar till data som lagras utanför en Kusto-databas. Precis som tabeller har en extern tabell ett väldefinierat schema. Till skillnad från tabeller lagras och hanteras data utanför ett Kusto-kluster. Exporterade data från föregående avsnitt sparas i JSON-linjer.

Om du vill skapa en referens behöver du schemat för den exporterade tabellen. Använd [Get schema](/azure/data-explorer/kusto/query/getschemaoperator) -operatorn från Log Analytics för att hämta den här informationen som innehåller tabellens kolumner och deras data typer.

:::image type="content" source="media\azure-data-explorer-query-storage\exported-data-map-schema.jpg" alt-text="Log Analytics tabell schema.":::

Du kan nu använda utdata för att skapa en Kusto-fråga för att skapa den externa tabellen.
Följ rikt linjerna i [skapa och ändra externa tabeller i Azure Storage eller Azure Data Lake](/azure/data-explorer/kusto/management/external-tables-azurestorage-azuredatalake), skapa en extern tabell i ett JSON-format och kör sedan frågan från Azure-datautforskaren-databasen.

>[!NOTE]
>Den externa tabellen har skapats från två processer. Det första skapar den externa tabellen, medan den andra skapar mappningen.

Följande PowerShell-skript kommer att skapa [create](/azure/data-explorer/kusto/management/external-tables-azurestorage-azuredatalake#create-external-table-mapping) -kommandon för tabellen och mappningen.

```powershell
PARAM(
    $resourcegroupname, #The name of the Azure resource group
    $TableName, # The Log Analytics table you wish to convert to external table
    $MapName, # The name of the map
    $subscriptionId, #The ID of the subscription
    $WorkspaceId, # The Log Analytics WorkspaceId
    $WorkspaceName, # The Log Analytics workspace name
    $BlobURL, # The Blob URL where the data is saved
    $ContainerAccessKey, # The blob container Access Key (Option to add a SAS url)
    $ExternalTableName = $null # The External Table name, null to use the same name
)

if($null -eq $ExternalTableName) {
    $ExternalTableName = $TableName
}

$query = $TableName + ' | getschema | project ColumnName, DataType'

$output = (Invoke-AzOperationalInsightsQuery -WorkspaceId $WorkspaceId -Query $query).Results

$FirstCommand = @()
$SecondCommand = @()

foreach ($record in $output) {
    if ($record.DataType -eq 'System.DateTime') {
        $dataType = 'datetime'
    } else {
        $dataType = 'string'
    }
    $FirstCommand += $record.ColumnName + ":" + "$dataType" + ","
    $SecondCommand += "{`"column`":" + "`"" + $record.ColumnName + "`"," + "`"datatype`":`"$dataType`",`"path`":`"$." + $record.ColumnName + "`"},"
}
$schema = ($FirstCommand -join '') -replace ',$'
$mapping = ($SecondCommand -join '') -replace ',$'

$CreateExternal = @'
.create external table {0} ({1})
kind=blob
partition by (TimeGeneratedPartition:datetime = bin(TimeGenerated, 1min))
pathformat = (datetime_pattern("'y='yyyy'/m='MM'/d='dd'/h='HH'/m='mm", TimeGeneratedPartition))
dataformat=multijson
(
   h@'{2}/WorkspaceResourceId=/subscriptions/{4}/resourcegroups/{6}/providers/microsoft.operationalinsights/workspaces/{5};{3}'
)
with
(
   docstring = "Docs",
   folder = "ExternalTables"
)
'@ -f $TableName, $schema, $BlobURL, $ContainerAccessKey, $subscriptionId, $WorkspaceName, $resourcegroupname,$WorkspaceId

$createMapping = @'
.create external table {0} json mapping "{1}" '[{2}]'
'@ -f $ExternalTableName, $MapName, $mapping

Write-Host -ForegroundColor Red 'Copy and run the following commands (one by one), on your Azure Data Explorer cluster query window to create the external table and mappings:'
write-host -ForegroundColor Green $CreateExternal
Write-Host -ForegroundColor Green $createMapping
```

Följande bild visar och exempel på utdata.

:::image type="content" source="media/azure-data-explorer-query-storage/external-table-create-command-output.png" alt-text="ExternalTable skapar kommandoutdata.":::

[![Exempel på utdata](media/azure-data-explorer-query-storage/external-table-create-command-output.png)](media/azure-data-explorer-query-storage/external-table-create-command-output.png#lightbox)

>[!TIP]
>* Kopiera, klistra in och kör sedan utdata från skriptet i Azure Datautforskaren client-verktyget för att skapa tabellen och mappningen.
>* Om du vill använda alla data i behållaren ändrar du skriptet och ändrar URL: en till ' https://your.blob.core.windows.net/containername ; SecKey'

## <a name="query-the-exported-data-from-azure-data-explorer"></a>Fråga exporterade data från Azure Datautforskaren 

När du har konfigurerat mappningen kan du fråga exporterade data från Azure Datautforskaren. Frågan kräver [external_table](/azure/data-explorer/kusto/query/externaltablefunction) -funktionen, som i följande exempel.

```kusto
external_table("HBTest","map") | take 10000
```

[![Fråga Log Analytics exporterade data](media/azure-data-explorer-query-storage/external-table-query.png)](media/azure-data-explorer-query-storage/external-table-query.png#lightbox)

## <a name="next-steps"></a>Nästa steg

- Lär dig att [skriva frågor i Azure datautforskaren](/azure/data-explorer/write-queries)