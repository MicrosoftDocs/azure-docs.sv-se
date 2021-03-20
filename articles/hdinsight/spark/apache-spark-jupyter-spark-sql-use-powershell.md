---
title: 'Snabb start: skapa Apache Spark kluster på Azure HDInsight med Azure PowerShell'
description: Den här snabbstarten visar hur du använder Azure PowerShell för att skapa ett Apache Spark-kluster i Azure HDInsight och köra en enkel Spark SQL-fråga.
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/12/2019
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 7a04d65d5032e37433fb61c73e5f64fe89bb2d2c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98930502"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-powershell"></a>Snabb start: skapa Apache Spark kluster i Azure HDInsight med hjälp av PowerShell

I den här snabb starten använder du Azure PowerShell för att skapa ett Apache Spark kluster i Azure HDInsight. Sedan skapar du en Jupyter Notebook och använder den för att köra Spark SQL-frågor mot Apache Hive tabeller. Azure HDInsight är en hanterad analystjänst med fullständigt spektrum med öppen källkod för företag. Apache Spark Framework för Azure HDInsight möjliggör snabb data analys och kluster data behandling med minnes intern bearbetning. Med Jupyter Notebook kan du interagera med dina data, kombinera kod med markdown text och göra enkla visualiseringar.

[Översikt: Apache Spark på Azure HDInsight](apache-spark-overview.md)  |  [Apache Spark](https://spark.apache.org/)  |  [Apache Hive](https://hive.apache.org/)  |  [Jupyter Notebook](https://jupyter.org/)

Om du använder flera kluster tillsammans, vill du skapa ett virtuellt nätverk och om du använder ett Spark-kluster vill du också använda Hive-lagrets koppling. Mer information finns i [planera ett virtuellt nätverk för Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md) och [integrera Apache Spark och Apache Hive med Hive-lager kopplingen](../interactive-query/apache-hive-warehouse-connector.md).

## <a name="prerequisite"></a>Förutsättning

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [PowerShell-modulen AZ](/powershell/azure/install-az-ps).

## <a name="create-an-apache-spark-cluster-in-hdinsight"></a>Skapa ett Apache Spark-kluster i HDInsight

> [!IMPORTANT]  
> Fakturering för HDInsight-kluster sker proportionerligt per minut, oavsett om du använder dem eller inte. Se till att du tar bort dina kluster när du är klar med dem. Mer information finns i avsnittet [Rensa resurser](#clean-up-resources) i den här artikeln.

Att skapa ett HDInsight-kluster innefattar att skapa följande Azure-objekt och -resurser:

- En Azure-resursgrupp. En Azure-resursgrupp är en container för Azure-resurser.
- Ett Azure Storage-konto eller Azure Data Lake Storage.  Varje HDInsight-kluster kräver en beroende datalagring. I den här snabb starten skapar du ett kluster som använder Azure Storage blobbar som kluster lagring. Mer information om att använda Data Lake Storage Gen2 finns i [Snabbstart: Konfigurera kluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
- Ett kluster av olika kluster typer i HDInsight.  I den här snabbstarten skapar du ett Spark 2.3-kluster.

Du använder ett PowerShell-skript för att skapa resurserna. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

När du kör PowerShell-skriptet uppmanas du att ange följande värden:

|Parameter|Värde|
|------|------|
|Azure-resursgruppsnamn | Ange ett unikt namn för resursgruppen.|
|Location| Ange Azure-regionen, t.ex. USA, centrala. |
|Namn på standardlagringskonto | Ange ett unikt namn för lagringskontot. |
|Klusternamn | Ange ett unikt namn för HDInsight-klustret.|
|Autentiseringsuppgifter för klusterinloggning | Du kan använda det här kontot för att ansluta till klusterinstrumentpanelen senare i snabbstarten.|
|SSH-användaruppgifter | SSH-klienter kan användas för att skapa en fjärrsession med kluster i HDInsight.|

1. Välj **testa det** i det övre högra hörnet för att öppna [Azure Cloud Shell](../../cloud-shell/overview.md)och följ sedan anvisningarna för att ansluta till Azure.

2. Kopiera och klistra in följande PowerShell-skript i Cloud Shell.

    ```azurepowershell-interactive
    ### Create a Spark 2.3 cluster in Azure HDInsight

    # Default cluster size (# of worker nodes), version, and type
    $clusterSizeInNodes = "1"
    $clusterVersion = "3.6"
    $clusterType = "Spark"

    # Create the resource group
    $resourceGroupName = Read-Host -Prompt "Enter the resource group name"
    $location = Read-Host -Prompt "Enter the Azure region to create resources in, such as 'Central US'"
    $defaultStorageAccountName = Read-Host -Prompt "Enter the default storage account name"

    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create an Azure storage account and container
    # Note: Storage account kind BlobStorage can only be used as secondary storage for HDInsight clusters.
    New-AzStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Location $location `
        -SkuName Standard_LRS `
        -Kind StorageV2 `
        -EnableHttpsTrafficOnly 1

    $defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value

    $defaultStorageContext = New-AzStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $defaultStorageAccountKey

    # Create a Spark 2.3 cluster
    $clusterName = Read-Host -Prompt "Enter the name of the HDInsight cluster"

    # Cluster login is used to secure HTTPS services hosted on the cluster
    $httpCredential = Get-Credential -Message "Enter Cluster login credentials" -UserName "admin"

    # SSH user is used to remotely connect to the cluster using SSH clients
    $sshCredentials = Get-Credential -Message "Enter SSH user credentials" -UserName "sshuser"

    # Set the storage container name to the cluster name
    $defaultBlobContainerName = $clusterName

    # Create a blob container. This holds the default data store for the cluster.
    New-AzStorageContainer `
        -Name $clusterName `
        -Context $defaultStorageContext

    $sparkConfig = New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"
    $sparkConfig.Add("spark", "2.3")

    # Create the cluster in HDInsight
    New-AzHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName `
        -Location $location `
        -ClusterSizeInNodes $clusterSizeInNodes `
        -ClusterType $clusterType `
        -OSType "Linux" `
        -Version $clusterVersion `
        -ComponentVersion $sparkConfig `
        -HttpCredential $httpCredential `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $clusterName `
        -SshCredential $sshCredentials

    Get-AzHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName
    ```

   Det tar cirka 20 minuter att skapa klustret. Klustret måste skapas innan du kan fortsätta till nästa session.

Om du stöter på ett problem med att skapa HDInsight-kluster kan det bero på att du inte har rätt behörighet. Mer information finns i [åtkomstkravkontrollen](../hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="create-a-jupyter-notebook"></a>Skapa en Jupyter Notebook

[Jupyter Notebook](https://jupyter.org/) är en interaktiv Notebook-miljö som har stöd för olika programmeringsspråk. Du kan använda anteckningsboken för att interagera med dina data, kombinera kod med markdown-text och utföra enkla visualiseringar.

1. Sök efter och välj **HDInsight-kluster** i [Azure Portal](https://portal.azure.com).
   
   ![Skärm bild som visar Azure Portal Sök för H D insikter.](./media/apache-spark-jupyter-spark-sql-use-powershell/azure-portal-search-hdinsight-cluster.png)
   
1. I listan väljer du det kluster som du skapade.
   
   ![Skärm bild som visar H D Insight-kluster med det kluster som du har skapat.](./media/apache-spark-jupyter-spark-sql-use-powershell/azure-portal-open-hdinsight-cluster.png)
   
1. På sidan kluster **Översikt** väljer du **kluster instrument paneler** och väljer sedan **Jupyter Notebook**. Ange autentiseringsuppgifterna för klustret om du uppmanas att göra det.

   ![Öppna Jupyter Notebook för att köra interaktiv Spark SQL-fråga](./media/apache-spark-jupyter-spark-sql-use-powershell/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Öppna Jupyter Notebook för att köra interaktiv Spark SQL-fråga")

1. Välj **ny**  >  **PySpark** för att skapa en antecknings bok.

   ![Skapa en Jupyter Notebook för att köra interaktiv Spark SQL-fråga](./media/apache-spark-jupyter-spark-sql-use-powershell/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Skapa en Jupyter Notebook för att köra interaktiv Spark SQL-fråga")

   En ny anteckningsbok skapas och öppnas med namnet Untitled(Untitled.pynb).

## <a name="run-apache-spark-sql-statements"></a>Kör Apache Spark SQL-uttryck

SQL (Structured Query Language) är det vanligaste språket för frågor och definition av data. Spark SQL fungerar som ett tillägg till Apache Spark för bearbetning av strukturerade data med den välbekanta SQL-syntaxen.

1. Verifiera att kerneln är klar. Kerneln är klar när du ser en tom cirkel bredvid kernelnamnet i den bärbara datorn. En fylld cirkel anger att kerneln är upptagen.

    ![kernel-status](./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png "kernel-status")

    När du startar den bärbara datorn för första gången utför kerneln några uppgifter i bakgrunden. Vänta tills kerneln är klar. 
1. Klistra in följande kod i en tom cell och tryck sedan på **SKIFT+RETUR** för att köra koden. Kommandot listar Hive-tabellerna i klustret:

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    När du använder en Jupyter Notebook med ditt Spark-kluster i HDInsight får du en `sqlContext` för inställning som du kan använda för att köra Hive-frågor med Spark SQL. `%%sql` anger att Jupyter Notebook ska använda den förinställda `sqlContext` när Hive-frågan ska köras. Frågan hämtar de översta 10 raderna från en Hive-tabell (**hivesampletable**) som medföljer alla HDInsight-kluster som standard. Det tar ungefär 30 sekunder att få resultatet. Utdata ser ut så här:

    ![Apache Hive fråga i Spark på HDInsight](./media/apache-spark-jupyter-spark-sql-use-powershell/hdinsight-spark-get-started-hive-query.png "Hive-fråga i HDInsight Spark")

    Varje gång du kör en fråga i Jupyter visar fönsterrubriken i webbläsaren statusen **(Upptagen)** tillsammans med anteckningsbokens titel. Du ser även en fylld cirkel bredvid **PySpark**-texten i det övre högra hörnet.

1. Kör ytterligare en fråga för att visa data i `hivesampletable`.

    ```PySpark
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```

    Skärmen bör uppdateras så att frågeresultatet visas.

    ![Utdata från Hive-fråga i HDInsight](./media/apache-spark-jupyter-spark-sql-use-powershell/hdinsight-spark-get-started-hive-query-output.png "Utdata från Hive-fråga i HDInsight")

1. Välj **Stäng och stoppa** på anteckningsbokens **Arkiv**-meny. När du stänger anteckningsboken frigörs klusterresurserna.

## <a name="clean-up-resources"></a>Rensa resurser

HDInsight sparar dina data i Azure Storage eller Azure Data Lake Storage så att du på ett säkert sätt kan ta bort ett kluster när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är flera gånger större än avgifterna för lagring är det ekonomiskt sett bra att ta bort kluster när de inte används. Om du planerar att arbeta med självstudierna i [Nästa steg](#next-steps) direkt, kan du vilja behålla klustret.

Växla tillbaka till Azure Portal och välj **Ta bort**.

![Azure Portal ta bort ett HDInsight-kluster](./media/apache-spark-jupyter-spark-sql-use-powershell/hdinsight-azure-portal-delete-cluster.png "Ta bort HDInsight-kluster")

Du kan också välja resursgruppnamnet för att öppna resursgruppsidan. Välj sedan **Ta bort resursgrupp**. Genom att ta bort resurs gruppen tar du bort både HDInsight-klustret och standard lagrings kontot.

### <a name="piecemeal-clean-up-with-powershell-az-module"></a>Stycke vis rensa med PowerShell AZ-modulen

```powershell
# Removes the specified HDInsight cluster from the current subscription.
Remove-AzHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $clusterName

# Removes the specified storage container.
Remove-AzStorageContainer `
    -Name $clusterName `
    -Context $defaultStorageContext

# Removes a Storage account from Azure.
Remove-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName

# Removes a resource group.
Remove-AzResourceGroup `
    -Name $resourceGroupName
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skapar ett Apache Spark kluster i HDInsight och kör en grundläggande Spark SQL-fråga. Gå vidare till nästa självstudie och lär dig hur du använder ett HDInsight-kluster för att köra interaktiva frågor på exempel data.

> [!div class="nextstepaction"]
> [Köra interaktiva frågor i Apache Spark](./apache-spark-load-data-run-query.md)