---
title: Övervaka en Azure-datafabrik via programmering
description: 'Lär dig hur du övervakar en pipeline i en data fabrik med hjälp av olika SDK: er (Software Development Kits).'
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/16/2018
author: dcstwh
ms.author: weetok
ms.custom: devx-track-python
ms.openlocfilehash: 6c913c7c623c77baea0c575d06d2c44709af43fa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101740451"
---
# <a name="programmatically-monitor-an-azure-data-factory"></a>Övervaka en Azure-datafabrik via programmering

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Den här artikeln beskriver hur du övervakar en pipeline i en data fabrik med hjälp av olika SDK: er (Software Development Kits). 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="data-range"></a>Data intervall

Data Factory lagrar endast pipeline-körnings data i 45 dagar. När du frågar program mässigt efter data om Data Factory pipelines körs, till exempel med PowerShell-kommandot, `Get-AzDataFactoryV2PipelineRun` finns det inga maximala datum för valfria-och- `LastUpdatedAfter` `LastUpdatedBefore` parametrarna. Men om du frågar efter data för det senaste året får du till exempel inte ett fel, utan endast pipelines kör data från de senaste 45 dagarna.

Om du vill behålla pipeline-körningar i mer än 45 dagar konfigurerar du din egen diagnostiska loggning med [Azure Monitor](monitor-using-azure-monitor.md).

## <a name="pipeline-run-information"></a>Körnings information för pipeline

För körnings egenskaper för pipeline, se [PIPELINERUN API-referens](/rest/api/datafactory/pipelineruns/get#pipelinerun). En pipeline-körning har olika status under livs cykeln, och möjliga värden för körnings status visas nedan:

* I kö
* Pågår
* Lyckades
* Misslyckad
* Avbryter
* Avbrutna

## <a name="net"></a>.NET
En fullständig genom gång av hur du skapar och övervakar en pipeline med hjälp av .NET SDK finns i [skapa en data fabrik och pipeline med hjälp av .net](quickstart-create-data-factory-dot-net.md).

1. Lägg till följande kod för att kontinuerligt kontrol lera status för pipeline-körningen tills den har slutfört kopieringen av data.

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, runResponse.RunId);
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress" || pipelineRun.Status == "Queued")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

2. Lägg till följande kod i som hämtar kopierings aktivitetens körnings information, till exempel storlek på lästa/skrivna data.

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");
   
    List<ActivityRun> activityRuns = client.ActivityRuns.ListByPipelineRun(
    resourceGroup, dataFactoryName, runResponse.RunId, DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)).ToList(); 
    if (pipelineRun.Status == "Succeeded")
        Console.WriteLine(activityRuns.First().Output);
    else
        Console.WriteLine(activityRuns.First().Error);
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

Fullständig dokumentation om .NET SDK finns i [Data Factory .NET SDK-referens](/dotnet/api/microsoft.azure.management.datafactory).

## <a name="python"></a>Python
En fullständig genom gång av hur du skapar och övervakar en pipeline med python SDK finns i [skapa en data fabrik och pipeline med python](quickstart-create-data-factory-python.md).

Lägg till följande kod för att övervaka pipeline-körningen:

```python
# Monitor the pipeline run
time.sleep(30)
pipeline_run = adf_client.pipeline_runs.get(
    rg_name, df_name, run_response.run_id)
print("\n\tPipeline run status: {}".format(pipeline_run.status))
activity_runs_paged = list(adf_client.activity_runs.list_by_pipeline_run(
    rg_name, df_name, pipeline_run.run_id, datetime.now() - timedelta(1),  datetime.now() + timedelta(1)))
print_activity_run_details(activity_runs_paged[0])
```

Fullständig dokumentation om python SDK finns [Data Factory python SDK-referens](/python/api/overview/azure/datafactory).

## <a name="rest-api"></a>REST-API
En fullständig genom gång av hur du skapar och övervakar en pipeline med hjälp av REST API finns i [skapa en data fabrik och pipeline med hjälp av REST API](quickstart-create-data-factory-rest-api.md).
 
1. Kör följande skript för att kontinuerligt kontroller pipelinekörningens status tills kopieringen av data är klar.

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}?api-version=${apiVersion}"
    while ($True) {
        $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
        Write-Host  "Pipeline run status: " $response.Status -foregroundcolor "Yellow"

        if ( ($response.Status -eq "InProgress") -or ($response.Status -eq "Queued") ) {
            Start-Sleep -Seconds 15
        }
        else {
            $response | ConvertTo-Json
            break
        }
    }
    ```
2. Kör följande skript för att hämta körningsinformation för kopieringsaktiviteten, till exempel storleken på data som lästs/skrivits.

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}/activityruns?api-version=${apiVersion}&startTime="+(Get-Date).ToString('yyyy-MM-dd')+"&endTime="+(Get-Date).AddDays(1).ToString('yyyy-MM-dd')+"&pipelineName=Adfv2QuickStartPipeline"
    $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
    $response | ConvertTo-Json
    ```

Fullständig dokumentation om REST API finns i [Data Factory REST API referens](/rest/api/datafactory/).

## <a name="powershell"></a>PowerShell
En fullständig genom gång av hur du skapar och övervakar en pipeline med hjälp av PowerShell finns i [skapa en data fabrik och pipeline med hjälp av PowerShell](quickstart-create-data-factory-powershell.md).

1. Kör följande skript för att kontinuerligt kontroller pipelinekörningens status tills kopieringen av data är klar.

    ```powershell
    while ($True) {
        $run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

        if ($run) {
            if ( ($run.Status -ne "InProgress") -and ($run.Status -ne "Queued") ) {
                Write-Output ("Pipeline run finished. The status is: " +  $run.Status)
                $run
                break
            }
            Write-Output ("Pipeline is running...status: " + $run.Status)
        }

        Start-Sleep -Seconds 30
    }
    ```
2. Kör följande skript för att hämta körningsinformation för kopieringsaktiviteten, till exempel storleken på data som lästs/skrivits.

    ```powershell
    Write-Host "Activity run details:" -foregroundcolor "Yellow"
    $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $result
    
    Write-Host "Activity 'Output' section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"
    
    Write-Host "\nActivity 'Error' section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n"
    ```

Fullständig dokumentation om PowerShell-cmdlets finns i [referens för Data Factory PowerShell-cmdlet](/powershell/module/az.datafactory).

## <a name="next-steps"></a>Nästa steg
Se [övervaka pipelines med hjälp av Azure Monitor](monitor-using-azure-monitor.md) artikel om du vill lära dig mer om att använda Azure Monitor för att övervaka Data Factory pipelines.