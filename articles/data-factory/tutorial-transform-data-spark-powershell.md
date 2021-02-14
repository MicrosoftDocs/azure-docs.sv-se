---
title: 'Transformera data med Spark i Azure Data Factory '
description: Den här självstudiekursen innehåller stegvisa instruktioner för hur du transformerar data genom att använda en Spark-aktivitet i Azure Data Factory.
ms.service: data-factory
ms.topic: tutorial
ms.date: 01/22/2018
author: nabhishek
ms.author: abnarain
ms.openlocfilehash: cf425ecb1a8aebf594828bfba50e9aba8ca9dd7e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100377626"
---
# <a name="transform-data-in-the-cloud-by-using-spark-activity-in-azure-data-factory"></a>Transformera data i molnet genom att använda Spark-aktivitet i Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

I den här självstudien använder du Azure PowerShell för att skapa en Data Factory-pipeline som transformerar data med Spark-aktivitet och en länkad HDInsight-tjänst på begäran. I den här självstudiekursen får du göra följande:

> [!div class="checklist"]
> * Skapa en datafabrik. 
> * Skapa och distribuera länkade tjänster.
> * Skapa och distribuera en pipeline. 
> * Starta en pipelinekörning.
> * Övervaka pipelinekörningen.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Azure Storage konto**. Du skapar ett Python-skript och en indatafil och överför dem till Azure Storage. Spark-programmets utdata lagras på det här lagringskontot. Spark-klustret på begäran använder samma lagringskonto som den primära lagringen.  
* **Azure PowerShell**. Följ instruktionerna i [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/install-Az-ps).


### <a name="upload-python-script-to-your-blob-storage-account"></a>Överföra Python-skriptet till ditt Blob Storage-konto
1. Skapa en python-fil med namnet **WordCount_Spark. py** med följande innehåll: 

    ```python
    import sys
    from operator import add
    
    from pyspark.sql import SparkSession
    
    def main():
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
            
        lines = spark.read.text("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/inputfiles/minecraftstory.txt").rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' ')) \
            .map(lambda x: (x, 1)) \
            .reduceByKey(add)
        counts.saveAsTextFile("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/outputfiles/wordcount")
        
        spark.stop()
    
    if __name__ == "__main__":
        main()
    ```
2. Ersätt **&lt; storageAccountName &gt;** med namnet på ditt Azure Storage-konto. Spara sedan filen. 
3. Skapa en container med namnet **adftutorial** i Azure Blob Storage om den inte finns. 
4. Skapa en mapp med namnet **spark**.
5. Skapa en undermapp med namnet **script** under mappen **spark**. 
6. Överför filen **WordCount_Spark.py** till undermappen **script**. 


### <a name="upload-the-input-file"></a>Överföra indatafilen
1. Skapa en fil med namnet **minecraftstory.txt** med lite text. Spark-programmet räknar antalet ord i texten. 
2. Skapa en undermapp med namnet `inputfiles` i mappen `spark`. 
3. Överför `minecraftstory.txt` till mappen `inputfiles`. 

## <a name="author-linked-services"></a>Skapa länkade tjänster
Du har skapat två länkade tjänster i det här avsnittet: 
    
- Den länkade Azure Storage-tjänsten som länkar ditt Azure Storage-konto till datafabriken. Den här lagringen används av HDInsight-kluster på begäran. Den innehåller också Spark-skriptet som ska köras. 
- En på begäran länkad HDInsight-tjänst. Azure Data Factory skapar automatiskt ett HDInsight-kluster, kör Spark-programmet och tar sedan bort HDInsight-klustret när det har varit inaktivt under en förkonfigurerad tid. 

### <a name="azure-storage-linked-service"></a>Länkad Azure-lagringstjänst
Skapa en JSON-fil med önskat redigeringsprogram, kopiera följande JSON-definition för en länkad Azure Storage-tjänst och spara filen som **MyStorageLinkedService.json**.  

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<storageAccountName>;AccountKey=<storageAccountKey>"
      }
    }
}
```
Uppdatera &lt;storageAccountName&gt; och &lt;storageAccountKey&gt; med namnet på och nyckeln för ditt Azure Storage-konto. 


### <a name="on-demand-hdinsight-linked-service"></a>På begäran länkad HDInsight-tjänst
Skapa en JSON-fil med önskat redigeringsprogram, kopiera följande JSON-definition för en länkad Azure HDInsight-tjänst och spara filen som **MyOnDemandSparkLinkedService.json**.  

```json
{
    "name": "MyOnDemandSparkLinkedService",
    "properties": {
      "type": "HDInsightOnDemand",
      "typeProperties": {
        "clusterSize": 2,
        "clusterType": "spark",
        "timeToLive": "00:15:00",
        "hostSubscriptionId": "<subscriptionID> ",
        "servicePrincipalId": "<servicePrincipalID>",
        "servicePrincipalKey": {
          "value": "<servicePrincipalKey>",
          "type": "SecureString"
        },
        "tenant": "<tenant ID>",
        "clusterResourceGroup": "<resourceGroupofHDICluster>",
        "version": "3.6",
        "osType": "Linux",
        "clusterNamePrefix":"ADFSparkSample",
        "linkedServiceName": {
          "referenceName": "MyStorageLinkedService",
          "type": "LinkedServiceReference"
        }
      }
    }
}
```
Uppdatera värden för följande egenskaper i definitionen för den länkade tjänsten: 

- **hostSubscriptionId**. Ersätt &lt;subscriptionId&gt; med ID:t för din Azure-prenumeration. Klustret HDInsight på begäran skapas i den här prenumerationen. 
- **klient**. Ersätt &lt;tenantID&gt; med Azure-klientens ID. 
- **servicePrincipalId**, **servicePrincipalKey**. Ersätt &lt;servicePrincipalID&gt; och &lt;servicePrincipalKey&gt; med ID och nyckel för tjänstens huvudnamn i Azure Active Directory. Tjänstens huvudnamn måste vara medlem av prenumerationens deltagarrollen eller resursgruppen som klustret har skapats i. Mer information finns i [create Azure Active Directory application and service principal](../active-directory/develop/howto-create-service-principal-portal.md) (skapa Azure Active Directory-program och ett huvudnamn för tjänsten). **Tjänstens huvud namn-ID** motsvarar *program-ID:* t och en **nyckel för tjänstens huvud namn** motsvarar värdet för en *klient hemlighet*.
- **clusterResourceGroup**. Ersätt &lt;resourceGroupOfHDICluster&gt; med namnet på resursgruppen som HDInsight-klustret ska skapas i. 

> [!NOTE]
> Azure HDInsight har en begränsning för hur många kärnor du kan använda i varje Azure-region som stöds. För den HDInsight-länkade tjänsten på begäran skapas HDInsight-klustret på samma plats som Azure Storage använde som primär lagring. Se till att du har tillräckligt med kärnkvoter så att klustret kan skapats på rätt sätt. Mer information finns i [Set up clusters in HDInsight with Hadoop, Spark, Kafka, and more](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) (Konfigurera kluster i HDInsight med Hadoop, Spark, Kafka med mera). 


## <a name="author-a-pipeline"></a>Skapa en pipeline 
I det här steget kan du skapa en ny pipeline med en Spark-aktivitet. Aktiviteten använder exemplet **ordräkning**. Hämta innehållet från den här platsen om du inte redan gjort det.

Skapa en JSON-fil med önskat redigeringsprogram, kopiera följande JSON-definition för en pipelinedefinition och spara filen som **MySparkOnDemandPipeline.json**. 

```json
{
  "name": "MySparkOnDemandPipeline",
  "properties": {
    "activities": [
      {
        "name": "MySparkActivity",
        "type": "HDInsightSpark",
        "linkedServiceName": {
            "referenceName": "MyOnDemandSparkLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
          "rootPath": "adftutorial/spark",
          "entryFilePath": "script/WordCount_Spark.py",
          "getDebugInfo": "Failure",
          "sparkJobLinkedService": {
            "referenceName": "MyStorageLinkedService",
            "type": "LinkedServiceReference"
          }
        }
      }
    ]
  }
}
```

Observera följande punkter: 

- rootPath pekar på Spark-mappen i containern adftutorial. 
- entryFilePath pekar på filen WordCount_Spark.py i skriptets undermapp i Spark-mappen. 


## <a name="create-a-data-factory"></a>Skapa en datafabrik 
Du har skapat definitioner för länkad tjänst och pipeline i JSON-filer. Nu ska vi skapa en data fabrik och distribuera de länkade tjänsterna och pipelinens JSON-filer med hjälp av PowerShell-cmdletar. Kör följande PowerShell-kommandon ett i taget: 

1. Ange variabler en i taget.

    **Namnet på resursgruppen**
    ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup" 
    ```

    **Data Factory namn. Måste vara globalt unikt** 
    ```powershell
    $dataFactoryName = "MyDataFactory09102017"
    ```
    
    **Namn på pipeline**
    ```powershell
    $pipelineName = "MySparkOnDemandPipeline" # Name of the pipeline
    ```
2. Starta **PowerShell**. Låt Azure PowerShell vara öppet tills du är klar med snabbstarten. Om du stänger och öppnar det igen måste du köra kommandona en gång till. Om du vill se en lista med Azure-regioner där Data Factory är tillgängligt för närvarande markerar du de regioner du är intresserad av på följande sida. Expandera sedan **Analytics** och leta rätt på **Data Factory**: [Tillgängliga produkter per region](https://azure.microsoft.com/global-infrastructure/services/). Datalagren (Azure Storage, Azure SQL Database osv.) och beräkningarna (HDInsight osv.) som används i Data Factory kan finnas i andra regioner.

    Kör följande kommando och ange det användarnamn och lösenord som du använder för att logga in i Azure Portal:
        
    ```powershell
    Connect-AzAccount
    ```        
    Kör följande kommando för att visa alla prenumerationer för det här kontot:

    ```powershell
    Get-AzSubscription
    ```
    Kör följande kommando för att välja den prenumeration som du vill arbeta med. Ersätt **SubscriptionId** med ID:t för din Azure-prenumeration:

    ```powershell
    Select-AzSubscription -SubscriptionId "<SubscriptionId>"    
    ```  
3. Skapa resursgruppen: ADFTutorialResourceGroup. 

    ```powershell
    New-AzResourceGroup -Name $resourceGroupName -Location "East Us" 
    ```
4. Skapa datafabriken. 

    ```powershell
     $df = Set-AzDataFactoryV2 -Location EastUS -Name $dataFactoryName -ResourceGroupName $resourceGroupName
    ```

    Kör följande kommando för att se utdata: 

    ```powershell
    $df
    ```
5. Växla till den mapp där du skapade JSON-filerna och kör följande kommando för att distribuera en länkad Azure Storage-tjänst: 
       
    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "MyStorageLinkedService" -File "MyStorageLinkedService.json"
    ```
6. Kör följande kommando för att distribuera en länkad Spark-tjänst på begäran: 
       
    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "MyOnDemandSparkLinkedService" -File "MyOnDemandSparkLinkedService.json"
    ```
7. Kör följande kommando för att distribuera en pipeline: 
       
    ```powershell
    Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name $pipelineName -File "MySparkOnDemandPipeline.json"
    ```
    
## <a name="start-and-monitor-a-pipeline-run"></a>Starta och övervaka en pipelinekörning  

1. Starta en pipelinekörning. Den samlar även in pipelinekörningens ID för kommande övervakning.

    ```powershell
    $runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName  
    ```
2. Kör följande skript för att kontinuerligt kontrollera pipelinekörningens status tills den är klar.

    ```powershell
    while ($True) {
        $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    
        if(!$result) {
            Write-Host "Waiting for pipeline to start..." -foregroundcolor "Yellow"
        }
        elseif (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
            Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
        }
        else {
            Write-Host "Pipeline '"$pipelineName"' run finished. Result:" -foregroundcolor "Yellow"
            $result
            break
        }
        ($result | Format-List | Out-String)
        Start-Sleep -Seconds 15
    }

    Write-Host "Activity `Output` section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"

    Write-Host "Activity `Error` section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n" 
    ```  
3. Här är utdata för exempelkörningen: 

    ```
    Pipeline run status: In Progress
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : 
    ActivityName      : MySparkActivity
    PipelineRunId     : 94e71d08-a6fa-4191-b7d1-cf8c71cb4794
    PipelineName      : MySparkOnDemandPipeline
    Input             : {rootPath, entryFilePath, getDebugInfo, sparkJobLinkedService}
    Output            : 
    LinkedServiceName : 
    ActivityRunStart  : 9/20/2017 6:33:47 AM
    ActivityRunEnd    : 
    DurationInMs      : 
    Status            : InProgress
    Error             :
    …
    
    Pipeline ' MySparkOnDemandPipeline' run finished. Result:
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : MyDataFactory09102017
    ActivityName      : MySparkActivity
    PipelineRunId     : 94e71d08-a6fa-4191-b7d1-cf8c71cb4794
    PipelineName      : MySparkOnDemandPipeline
    Input             : {rootPath, entryFilePath, getDebugInfo, sparkJobLinkedService}
    Output            : {clusterInUse, jobId, ExecutionProgress, effectiveIntegrationRuntime}
    LinkedServiceName : 
    ActivityRunStart  : 9/20/2017 6:33:47 AM
    ActivityRunEnd    : 9/20/2017 6:46:30 AM
    DurationInMs      : 763466
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    Activity Output section:
    "clusterInUse": "https://ADFSparkSamplexxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.azurehdinsight.net/"
    "jobId": "0"
    "ExecutionProgress": "Succeeded"
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
    Activity Error section:
    "errorCode": ""
    "message": ""
    "failureType": ""
    "target": "MySparkActivity"
    ```
4. Bekräfta att en mapp med namnet `outputfiles` har skapats i mappen `spark` i adftutorial-containern med utdata från Spark-programmet. 


## <a name="next-steps"></a>Nästa steg
Pipeline i det här exemplet kopierar data från en plats till en annan i Azure Blob Storage. Du har lärt dig att: 

> [!div class="checklist"]
> * Skapa en datafabrik. 
> * Skapa och distribuera länkade tjänster.
> * Skapa och distribuera en pipeline. 
> * Starta en pipelinekörning.
> * Övervaka pipelinekörningen.

Gå vidare till nästa självstudiekurs för att lära dig hur du transformerar data genom att köra Hive-skript på ett Azure HDInsight-kluster som är i ett virtuellt nätverk. 

> [!div class="nextstepaction"]
> [Självstudie: transformera data med Hive i Azure Virtual Network](tutorial-transform-data-hive-virtual-network.md).





