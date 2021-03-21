---
title: Anpassa Azure HDInsight-klusterkonfigurationer med start
description: Lär dig hur du anpassar konfiguration av HDInsight-kluster program mässigt med hjälp av .NET, PowerShell och Resource Manager-mallar.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 04/01/2020
ms.openlocfilehash: c73cb99d62295312980fe5f1b248d23efa2eb0c5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102432391"
---
# <a name="customize-hdinsight-clusters-using-bootstrap"></a>Anpassa HDInsight-kluster med hjälp av bootstrap

Med Bootstrap-skript kan du installera och konfigurera komponenter i Azure HDInsight program mässigt.

Det finns tre metoder för att ange inställningar för konfigurations fil när HDInsight-klustret skapas:

* Använda Azure PowerShell
* Använda .NET SDK
* Använd Azure Resource Manager mall

Med dessa programmerings metoder kan du till exempel konfigurera alternativ i följande filer:

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred – plats
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml
* Server. Properties (Kafka-Broker-konfiguration)

Information om hur du installerar ytterligare komponenter i HDInsight-kluster under skapande tiden finns i [Anpassa HDInsight-kluster med skript åtgärd (Linux)](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="prerequisites"></a>Förutsättningar

* Om du använder PowerShell behöver du AZ- [modulen](/powershell/azure/).

## <a name="use-azure-powershell"></a>Använda Azure PowerShell

Följande PowerShell-kod anpassar en [Apache Hive](https://hive.apache.org/) -konfiguration:

> [!IMPORTANT]  
> Parametern `Spark2Defaults` kan behöva användas med [Add-AzHDInsightConfigValue](/powershell/module/az.hdinsight/add-azhdinsightconfigvalue). Du kan skicka tomma värden till parametern som visas i kod exemplet nedan.

```powershell
# hive-site.xml configuration
$hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90s" }

$config = New-AzHDInsightClusterConfig `
    | Set-AzHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzHDInsightConfigValue `
        -HiveSite $hiveConfigValues `
        -Spark2Defaults @{}

New-AzHDInsightCluster `
    -ResourceGroupName $existingResourceGroupName `
    -ClusterName $clusterName `
    -Location $location `
    -ClusterSizeInNodes $clusterSizeInNodes `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version "3.6" `
    -HttpCredential $httpCredential `
    -Config $config
```

Du hittar ett fullständigt fungerande PowerShell-skript i [bilagan](#appendix-powershell-sample).

**Så här kontrollerar du ändringen:**

1. Navigera till `https://CLUSTERNAME.azurehdinsight.net/` där `CLUSTERNAME` är namnet på klustret.
1. I den vänstra menyn navigerar du till **Hive**-  >  **konfigurationer**  >  **Avancerat**.
1. Expandera **Avancerad Hive-plats**.
1. Leta upp **Hive. metaarkiv. client. socket. timeout** och bekräfta att värdet är **90s**.

Några fler exempel på hur du anpassar andra konfigurationsfiler:

```xml
# hdfs-site.xml configuration
$HdfsConfigValues = @{ "dfs.blocksize"="64m" } #default is 128MB in HDI 3.0 and 256MB in HDI 2.1

# core-site.xml configuration
$CoreConfigValues = @{ "ipc.client.connect.max.retries"="60" } #default 50

# mapred-site.xml configuration
$MapRedConfigValues = @{ "mapreduce.task.timeout"="1200000" } #default 600000

# oozie-site.xml configuration
$OozieConfigValues = @{ "oozie.service.coord.normal.default.timeout"="150" }  # default 120
```

## <a name="use-net-sdk"></a>Använda .NET SDK

Se [Azure HDInsight SDK för .net](/dotnet/api/overview/azure/hdinsight).

## <a name="use-resource-manager-template"></a>Använda Resource Manager-mallar

Du kan använda bootstrap i Resource Manager-mallen:

```json
"configurations": {
    "hive-site": {
        "hive.metastore.client.connect.retry.delay": "5",
        "hive.execution.engine": "mr",
        "hive.security.authorization.manager": "org.apache.hadoop.hive.ql.security.authorization.DefaultHiveAuthorizationProvider"
    }
}
```

![Hadoop anpassar start Azure Resource Manager mall för kluster](./media/hdinsight-hadoop-customize-cluster-bootstrap/hdinsight-customize-cluster-bootstrap-arm.png)

Exempel på en Resource Manager-mall för att växla konfiguration i spark2 – standardvärdet för att regelbundet rensa händelse loggar från lagringen.  

```json
"configurations": {
    "spark2-defaults": {
        "spark.history.fs.cleaner.enabled": "true",
        "spark.history.fs.cleaner.interval": "7d",
        "spark.history.fs.cleaner.maxAge": "90d"
    }
}
```

## <a name="see-also"></a>Se även

* [Skapa Apache Hadoop kluster i HDInsight](hdinsight-hadoop-provision-linux-clusters.md) innehåller instruktioner om hur du skapar ett HDInsight-kluster med hjälp av andra anpassade alternativ.
* [Utveckla skript åtgärds skript för HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Installera och använda Apache Spark på HDInsight-kluster](spark/apache-spark-jupyter-spark-sql-use-portal.md)
* [Installera och Använd Apache Giraph i HDInsight-kluster](./hdinsight-hadoop-hue-linux.md).

## <a name="appendix-powershell-sample"></a>Bilaga: PowerShell-exempel

Det här PowerShell-skriptet skapar ett HDInsight-kluster och anpassar en Hive-inställning. Se till att ange värden för `$nameToken` , `$httpPassword` och `$sshPassword` .

```powershell
####################################
# Set these variables
####################################
#region - used for creating Azure service names
$nameToken = "<ENTER AN ALIAS>"
#endregion

#region - cluster user accounts
$httpUserName = "admin"  #HDInsight cluster username
$httpPassword = '<ENTER A PASSWORD>'

$sshUserName = "sshuser" #HDInsight ssh user name
$sshPassword = '<ENTER A PASSWORD>'
#endregion

####################################
# Service names and varialbes
####################################
#region - service names
$namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

$resourceGroupName = $namePrefix + "rg"
$hdinsightClusterName = $namePrefix + "hdi"
$defaultStorageAccountName = $namePrefix + "store"
$defaultBlobContainerName = $hdinsightClusterName

$location = "East US"
#endregion


####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

#endregion

#region - Create an HDInsight cluster
####################################
# Create dependent components
####################################
Write-Host "Creating a resource group ..." -ForegroundColor Green
New-AzResourceGroup `
    -Name  $resourceGroupName `
    -Location $location

Write-Host "Creating the default storage account and default blob container ..."  -ForegroundColor Green
New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableHttpsTrafficOnly 1

# Note: Storage account kind BlobStorage cannot be used as primary storage.

$defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value

$defaultStorageContext = New-AzStorageContext `
                                -StorageAccountName $defaultStorageAccountName `
                                -StorageAccountKey $defaultStorageAccountKey

New-AzStorageContainer `
    -Name $defaultBlobContainerName `
    -Context $defaultStorageContext #use the cluster name as the container name

####################################
# Create a configuration object
####################################
$hiveConfigValues = @{"hive.metastore.client.socket.timeout"="90s"}

$config = New-AzHDInsightClusterConfig `
    | Set-AzHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzHDInsightConfigValue `
        -HiveSite $hiveConfigValues `
        -Spark2Defaults @{}

####################################
# Create an HDInsight cluster
####################################
$httpPW = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$httpPW)

$sshPW = ConvertTo-SecureString -String $sshPassword -AsPlainText -Force
$sshCredential = New-Object System.Management.Automation.PSCredential($sshUserName,$sshPW)

New-AzHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -Location $location `
    -ClusterSizeInNodes 1 `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version "3.6" `
    -HttpCredential $httpCredential `
    -SshCredential $sshCredential `
    -Config $config

####################################
# Verify the cluster
####################################
Get-AzHDInsightCluster `
    -ClusterName $hdinsightClusterName

#endregion
```