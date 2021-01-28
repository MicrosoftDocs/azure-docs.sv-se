---
title: Hantera Apache Hadoop kluster med PowerShell – Azure HDInsight
description: Lär dig hur du utför administrativa uppgifter för Apache Hadoop kluster i HDInsight med Azure PowerShell.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/13/2020
ms.openlocfilehash: c8581c2288eacb47fb9475a3b0a584434c8604f6
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942808"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>Hantera Apache Hadoop kluster i HDInsight med Azure PowerShell

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Azure PowerShell kan användas för att styra och automatisera distributionen och hanteringen av dina arbets belastningar i Azure. I den här artikeln får du lära dig hur du hanterar [Apache Hadoop](https://hadoop.apache.org/) kluster i Azure HDInsight med hjälp av modulen Azure PowerShell AZ. En lista med PowerShell-cmdletar för HDInsight finns i [referens för AZ. HDInsight](/powershell/module/az.hdinsight).

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

PowerShell- [modulen för AZ](/powershell/azure/) är installerad.

## <a name="create-clusters"></a>Skapa kluster

Se [skapa Linux-baserade kluster i HDInsight med hjälp av Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)

## <a name="list-clusters"></a>Lista kluster

Använd följande kommando för att lista alla kluster i den aktuella prenumerationen:

```powershell
Get-AzHDInsightCluster
```

## <a name="show-cluster"></a>Visa kluster

Använd följande kommando för att visa information om ett särskilt kluster i den aktuella prenumerationen:

```powershell
Get-AzHDInsightCluster -ClusterName <Cluster Name>
```

## <a name="delete-clusters"></a>Ta bort kluster

Använd följande kommando för att ta bort ett kluster:

```powershell
Remove-AzHDInsightCluster -ClusterName <Cluster Name>
```

Du kan också ta bort ett kluster genom att ta bort resurs gruppen som innehåller klustret. Om du tar bort en resurs grupp tas alla resurser i gruppen bort, inklusive standard lagrings kontot.

```powershell
Remove-AzResourceGroup -Name <Resource Group Name>
```

## <a name="scale-clusters"></a>Skala kluster

Med funktionen för kluster skalning kan du ändra antalet arbetsnoder som används av ett kluster som körs i Azure HDInsight utan att behöva skapa klustret på nytt. Om du vill ändra kluster storleken för Hadoop genom att använda Azure PowerShell kör du följande kommando från en klient dator:

```powershell
Set-AzHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
```

 Mer information om hur du skalar kluster finns i [skala HDInsight-kluster](./hdinsight-scaling-best-practices.md).

## <a name="update-http-user-credentials"></a>Uppdatera HTTP-användarautentiseringsuppgifter

[Set-AzHDInsightGatewayCredential](/powershell/module/az.hdinsight/set-azhdinsightgatewaycredential) anger gatewayens http-autentiseringsuppgifter för ett Azure HDInsight-kluster.

```powershell
$clusterName = "CLUSTERNAME"
$credential = Get-Credential -Message "Enter the HTTP username and password:" -UserName "admin"

Set-AzHDInsightGatewayCredential -ClusterName $clusterName -HttpCredential $credential
```

## <a name="find-the-default-storage-account"></a>Hitta standard lagrings kontot

Följande PowerShell-skript visar hur du hämtar standard namnet för lagrings kontot och relaterad information:

```powershell
#Connect-AzAccount
$clusterName = "<HDInsight Cluster Name>"

$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$storageInfo = $clusterInfo.DefaultStorageAccount.split('.')
$defaultStoreageType = $storageInfo[1]
$defaultStorageName = $storageInfo[0]

echo "Default Storage account name: $defaultStorageName"
echo "Default Storage account type: $defaultStoreageType"

if ($defaultStoreageType -eq "blob")
{
    $defaultBlobContainerName = $cluster.DefaultStorageContainer
    $defaultStorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey

    echo "Default Blob container name: $defaultBlobContainerName"
    echo "Default Storage account key: $defaultStorageAccountKey"
}
```

## <a name="find-the-resource-group"></a>Hitta resurs gruppen

I Resource Manager-läge tillhör varje HDInsight-kluster en Azure-resurs grupp.  Så här hittar du resurs gruppen:

```powershell
$clusterName = "<HDInsight Cluster Name>"

$cluster = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroupName = $cluster.ResourceGroup
```

## <a name="submit-jobs"></a>Skicka jobb

**Så här skickar du MapReduce-jobb**

Se [köra MapReduce-exemplen som ingår i HDInsight](hadoop/apache-hadoop-run-samples-linux.md).

**Skicka Apache Hive jobb**

Se [kör Apache Hive frågor med PowerShell](hadoop/apache-hadoop-use-hive-powershell.md).

**Skicka Apache Sqoop-jobb**

Se [använda Apache Sqoop med HDInsight](hadoop/hdinsight-use-sqoop.md).

**Skicka Apache Oozie-jobb**

Se [använda Apache Oozie med Apache Hadoop för att definiera och köra ett arbets flöde i HDInsight](hdinsight-use-oozie-linux-mac.md).

## <a name="upload-data-to-azure-blob-storage"></a>Ladda upp data till Azure Blob Storage

Mer information finns i [Överföra data till HDInsight](hdinsight-upload-data.md).

## <a name="see-also"></a>Se även

* [AZ. HDInsight-cmdletar](/powershell/module/az.hdinsight/#hdinsight)
* [Hantera Apache Hadoop kluster i HDInsight med hjälp av Azure Portal](hdinsight-administer-use-portal-linux.md)
* [Administrera HDInsight med ett kommando rads gränssnitt](hdinsight-administer-use-command-line.md)
* [Skapa HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md)
* [Skicka Apache Hadoop jobb program mässigt](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Kom igång med Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)