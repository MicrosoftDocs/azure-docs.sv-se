---
title: 'Självstudie: Skapa en ETL-pipeline från slutet till slut för att härleda försäljningsinsikter i Azure HDInsight'
description: Lär dig hur du skapar ETL-pipelines med Azure HDInsight för att härleda insikter från försäljningsdata med hjälp av Spark-kluster på begäran och Power BI.
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive, devx-track-azurecli
ms.date: 04/15/2020
ms.openlocfilehash: 140709a0ddb548c126ceca208fdeef2db77616bf
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107761807"
---
# <a name="tutorial-create-an-end-to-end-data-pipeline-to-derive-sales-insights-in-azure-hdinsight"></a>Självstudie: Skapa en datapipeline från slutet till slut för att härleda försäljningsinsikter i Azure HDInsight

I den här självstudien skapar du en datapipeline från slutet som utför ETL-åtgärder (extrahering, transformering och inläsning). Pipelinen använder [Apache Spark](./spark/apache-spark-overview.md) och Apache Hive som körs på Azure HDInsight för att köra frågor mot och manipulera data. Du kommer också att använda tekniker som Azure Data Lake Storage Gen2 för datalagring och Power BI för visualisering.

Den här datapipelinen kombinerar data från olika lager, tar bort oönskade data, lägger till nya data och läser in allt detta i din lagring för att visualisera affärsinsikter. Läs mer om ETL-pipelines i [Extrahera, transformera och läsa in (ETL) i stor skala.](./hadoop/apache-hadoop-etl-at-scale.md)

:::image type="content" source="./media/hdinsight-sales-insights-etl/architecture.png" alt-text="ETL-arkitektur" border="false":::

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

* Azure CLI – minst version 2.2.0. Se [Installera Azure CLI.](/cli/azure/install-azure-cli)

* jq, en JSON-processor på kommandoraden.  Se [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/) .

* En medlem i [den inbyggda Azure-rollen – ägare](../role-based-access-control/built-in-roles.md).

* Om du använder PowerShell för Data Factory-pipelinen behöver du [Az-modulen](/powershell/azure/).

* [Power BI Desktop att](https://aka.ms/pbiSingleInstaller) visualisera affärsinsikter i slutet av den här självstudien.

## <a name="create-resources"></a>Skapa resurser

### <a name="clone-the-repository-with-scripts-and-data"></a>Klona lagringsplatsen med skript och data

1. Logga in på din Azure-prenumeration. Om du planerar att Azure Cloud Shell väljer du **Prova i** det övre högra hörnet av kodblocket. Annars anger du kommandot nedan:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

1. Kontrollera att du är medlem [](../role-based-access-control/built-in-roles.md)i Azure-rollägaren . Ersätt `user@contoso.com` med ditt konto och ange sedan kommandot:

    ```azurecli
    az role assignment list \
    --assignee "user@contoso.com" \
    --role "Owner"
    ```

    Om ingen post returneras är du inte medlem och kan inte slutföra den här självstudien.

1. Hämta data och skript för den här självstudien från [HDInsight Sales Insights ETL-lagringsplatsen](https://github.com/Azure-Samples/hdinsight-sales-insights-etl). Ange följande kommando:

    ```bash
    git clone https://github.com/Azure-Samples/hdinsight-sales-insights-etl.git
    cd hdinsight-sales-insights-etl
    ```

1. Se `salesdata scripts templates` till att har skapats. Kontrollera med följande kommando:

   ```bash
   ls
   ```

### <a name="deploy-azure-resources-needed-for-the-pipeline"></a>Distribuera Azure-resurser som behövs för pipelinen

1. Lägg till körningsbehörigheter för alla skript genom att ange:

    ```bash
    chmod +x scripts/*.sh
    ````

1. Ange variabel för resursgrupp. Ersätt `RESOURCE_GROUP_NAME` med namnet på en befintlig eller ny resursgrupp och ange sedan kommandot:

    ```bash
    resourceGroup="RESOURCE_GROUP_NAME"
    ```

1. Kör skriptet. Ersätt `LOCATION` med ett önskat värde och ange sedan kommandot:

    ```bash
    ./scripts/resources.sh $resourceGroup LOCATION
    ```

    Om du inte är säker på vilken region du ska ange kan du hämta en lista över regioner som stöds för din prenumeration med [kommandot az account list-locations.](/cli/azure/account#az_account_list_locations)

    Kommandot distribuerar följande resurser:

    * Ett Azure Blob Storage-konto. Det här kontot kommer att innehålla företagets försäljningsdata.
    * Ett Azure Data Lake Storage Gen2 konto. Det här kontot fungerar som lagringskonto för båda HDInsight-klustren. Läs mer om HDInsight och Data Lake Storage Gen2 i [Azure HDInsight integrering med Data Lake Storage Gen2](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/).
    * En användar tilldelad hanterad identitet. Det här kontot ger HDInsight-kluster åtkomst till Data Lake Storage Gen2 konto.
    * Ett Apache Spark kluster. Det här klustret används för att rensa och transformera rådata.
    * Ett Apache Hive [Interaktiv fråga](./interactive-query/apache-interactive-query-get-started.md) kluster. Med det här klustret kan du köra frågor mot försäljningsdata och visualisera dem med Power BI.
    * Ett virtuellt Azure-nätverk som stöds av regler för nätverkssäkerhetsgrupp (NSG). Med det här virtuella nätverket kan klustren kommunicera och skydda sin kommunikation.

Det kan ta cirka 20 minuter att skapa kluster.

Standardlösenordet för SSH-åtkomst till klustren är `Thisisapassword1` . Om du vill ändra lösenordet går du till `./templates/resourcesparameters_remainder.json` filen och ändrar lösenordet för `sparksshPassword` parametrarna , , och `sparkClusterLoginPassword` `llapClusterLoginPassword` `llapsshPassword` .

### <a name="verify-deployment-and-collect-resource-information"></a>Verifiera distributionen och samla in resursinformation

1. Om du vill kontrollera statusen för distributionen går du till resursgruppen på Azure Portal. Under **Inställningar** väljer **du Distributioner** och sedan distributionen. Här kan du se de resurser som har distribuerats och de resurser som fortfarande pågår.

1. Om du vill visa namnen på klustren anger du följande kommando:

    ```bash
    sparkClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.sparkClusterName.value')
    llapClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.llapClusterName.value')

    echo "Spark Cluster" $sparkClusterName
    echo "LLAP cluster" $llapClusterName
    ```

1. Om du vill visa Azure Storage-kontot och åtkomstnyckeln anger du följande kommando:

    ```azurecli
    blobStorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.blobStorageName.value')

    blobKey=$(az storage account keys list \
        --account-name $blobStorageName \
        --resource-group $resourceGroup \
        --query [0].value -o tsv)

    echo $blobStorageName
    echo $blobKey
    ```

1. Om du vill Data Lake Storage Gen2 och åtkomstnyckeln anger du följande kommando:

    ```azurecli
    ADLSGen2StorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.adlsGen2StorageName.value')

    adlsKey=$(az storage account keys list \
        --account-name $ADLSGen2StorageName \
        --resource-group $resourceGroup \
        --query [0].value -o tsv)

    echo $ADLSGen2StorageName
    echo $adlsKey
    ```

### <a name="create-a-data-factory"></a>Skapa en datafabrik

Azure Data Factory är ett verktyg som hjälper dig att automatisera Azure Pipelines. Det är inte det enda sättet att utföra dessa uppgifter, men det är ett bra sätt att automatisera processerna. Mer information om Azure Data Factory finns i [Azure Data Factory dokumentationen](https://azure.microsoft.com/services/data-factory/).

Den här datafabriken har en pipeline med två aktiviteter:

* Den första aktiviteten kopierar data från Azure Blob Storage till Data Lake Storage Gen 2-lagringskontot för att efterlikna datainmatning.
* Den andra aktiviteten transformerar data i Spark-klustret. Skriptet transformerar data genom att ta bort oönskade kolumner. Den lägger också till en ny kolumn som beräknar de intäkter som en enskild transaktion genererar.

Kör kommandot nedan för Azure Data Factory konfigurera din pipeline.  Du bör fortfarande vara i `hdinsight-sales-insights-etl` katalogen .

```bash
blobStorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.blobStorageName.value')
ADLSGen2StorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.adlsGen2StorageName.value')

./scripts/adf.sh $resourceGroup $ADLSGen2StorageName $blobStorageName
```

Det här skriptet gör följande:

1. Skapar ett huvudnamn för tjänsten `Storage Blob Data Contributor` med behörigheter Data Lake Storage Gen2 lagringskontot.
1. Hämtar en autentiseringstoken för att auktorisera [POST-begäranden till Data Lake Storage Gen2-filsystemet REST API](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/filesystem/create).
1. Fyller i det faktiska namnet på ditt Data Lake Storage Gen2 lagringskonto i `sparktransform.py` `query.hql` filerna och .
1. Hämtar lagringsnycklar för Data Lake Storage Gen2- och Blob Storage-konton.
1. Skapar en annan resursdistribution för att skapa en Azure Data Factory pipeline med tillhörande länkade tjänster och aktiviteter. Den skickar lagringsnycklarna som parametrar till mallfilen så att de länkade tjänsterna kan komma åt lagringskontona på rätt sätt.

## <a name="run-the-data-pipeline"></a>Köra datapipelinen

### <a name="trigger-the-data-factory-activities"></a>Utlösa Data Factory aktiviteter

Den första aktiviteten i Data Factory som du har skapat flyttar data från Blob Storage till Data Lake Storage Gen2. Den andra aktiviteten tillämpar Spark-transformeringen på data och sparar de transformerade CSV-filerna på en ny plats. Det kan ta några minuter att slutföra hela pipelinen.

Om du vill Data Factory namnet anger du följande kommando:

```azurecli
cat resourcesoutputs_adf.json | jq -r '.properties.outputs.factoryName.value'
```

Om du vill utlösa pipelinen kan du antingen:

* Utlösningspipeline Data Factory i PowerShell. Ersätt `RESOURCEGROUP` , och med lämpliga värden och kör sedan följande `DataFactoryName` kommandon:

    ```powershell
    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

    $resourceGroup="RESOURCEGROUP"
    $dataFactory="DataFactoryName"

    $pipeline =Invoke-AzDataFactoryV2Pipeline `
        -ResourceGroupName $resourceGroup `
        -DataFactory $dataFactory `
        -PipelineName "IngestAndTransform"

    Get-AzDataFactoryV2PipelineRun `
        -ResourceGroupName $resourceGroup  `
        -DataFactoryName $dataFactory `
        -PipelineRunId $pipeline
    ```

    Kör om `Get-AzDataFactoryV2PipelineRun` vid behov för att övervaka förloppet.

    Eller

* Öppna datafabriken och välj **Författare & Övervaka.** Utlösning `IngestAndTransform` av pipelinen från portalen. Information om hur du utlöser pipelines via portalen finns i [Skapa Apache Hadoop-kluster](hdinsight-hadoop-create-linux-clusters-adf.md#trigger-a-pipeline)på begäran i HDInsight med hjälp av Azure Data Factory .

Om du vill kontrollera att pipelinen har körts kan du göra något av följande:

* Gå till **avsnittet Övervaka** i din datafabrik via portalen.
* I Azure Storage Explorer du till ditt Data Lake Storage Gen 2-lagringskonto. Gå till `files` filsystemet och gå sedan till mappen `transformed` och kontrollera dess innehåll för att se om pipelinen lyckades.

Andra sätt att transformera data med HDInsight finns i den [här artikeln om hur du använder Jupyter Notebook](/azure/hdinsight/spark/apache-spark-load-data-run-query).

### <a name="create-a-table-on-the-interactive-query-cluster-to-view-data-on-power-bi"></a>Skapa en tabell på Interaktiv fråga för att visa data på Power BI

1. Kopiera filen `query.hql` till LLAP-klustret med hjälp av SCP. Ange kommandot:

    ```bash
    llapClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.llapClusterName.value')
    scp scripts/query.hql sshuser@$llapClusterName-ssh.azurehdinsight.net:/home/sshuser/
    ```

    Påminnelse: Standardlösenordet är `Thisisapassword1` .

1. Använd SSH för att få åtkomst till LLAP-klustret. Ange kommandot:

    ```bash
    ssh sshuser@$llapClusterName-ssh.azurehdinsight.net
    ```

1. Kör skriptet med följande kommando:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f query.hql
    ```

    Det här skriptet skapar en hanterad tabell på Interaktiv fråga som du kan komma åt från Power BI.

### <a name="create-a-power-bi-dashboard-from-sales-data"></a>Skapa en Power BI instrumentpanel från försäljningsdata

1. Öppna Power BI Desktop.

1. Från menyn går du till **Hämta data**  >  **Mer...**  >  **Azure**  >  **HDInsight Interaktiv fråga**.

1. Välj **Anslut**.

1. Från **dialogrutan HDInsight Interaktiv fråga** dialogrutan:
    1. I **textrutan** Server anger du namnet på ditt LLAP-kluster i formatet `https://LLAPCLUSTERNAME.azurehdinsight.net` .
    1. I **textrutan** för databasen anger du `default` .
    1. Välj **OK**.

1. Från **dialogrutan AzureHive:**
    1. I **textrutan Användarnamn** anger du `admin` .
    1. I **textrutan** Lösenord anger du `Thisisapassword1` .
    1. Välj **Anslut**.

1. Från **Navigatör** `sales` väljer du och/eller `sales_raw` för att förhandsgranska data. När data har lästs in kan du experimentera med den instrumentpanel som du vill skapa. Se följande länkar för att komma igång med Power BI instrumentpaneler:

* [Introduktion till instrumentpaneler för Power BI-designers](https://docs.microsoft.com/power-bi/service-dashboards)
* [Självstudie: Kom igång med Power BI-tjänsten](https://docs.microsoft.com/power-bi/service-get-started)

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte tänker fortsätta att använda det här programmet tar du bort alla resurser med hjälp av följande kommando så att du inte debiteras för dem.

1. Om du vill ta bort resursgruppen anger du kommandot:

    ```azurecli
    az group delete -n $resourceGroup
    ```

1. Om du vill ta bort tjänstens huvudnamn anger du kommandona:

    ```azurecli
    servicePrincipal=$(cat serviceprincipal.json | jq -r '.name')
    az ad sp delete --id $servicePrincipal
    ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Extrahera, transformera och läsa in (ETL) i stor skala](./hadoop/apache-hadoop-etl-at-scale.md)
