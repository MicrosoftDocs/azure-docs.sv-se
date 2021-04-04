---
title: Köra apache Sqoop-jobb med hjälp av .NET och HDInsight – Azure
description: Lär dig hur du använder HDInsight .NET SDK för att köra apache Sqoop import och export mellan ett Apache Hadoop kluster och ett Azure SQL Database.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, hdiseo17may2017, devx-track-csharp
ms.date: 01/14/2020
ms.openlocfilehash: a06326f5b4cf34a06475c10e368c6dcbb49ca9e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98928342"
---
# <a name="run-apache-sqoop-jobs-by-using-net-sdk-for-apache-hadoop-in-hdinsight"></a>Köra apache Sqoop-jobb med hjälp av .NET SDK för Apache Hadoop i HDInsight

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Lär dig hur du använder Azure HDInsight .NET SDK för att köra apache Sqoop-jobb i HDInsight för att importera och exportera mellan ett HDInsight-kluster och en Azure SQL Database-eller SQL Server-databas.

## <a name="prerequisites"></a>Förutsättningar

* Slutför [konfiguration av test miljö](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) från att [använda Apache Sqoop med Hadoop i HDInsight](./hdinsight-use-sqoop.md).

* [Visual Studio](https://visualstudio.microsoft.com/vs/community/).

* Bekant med Sqoop. Mer information finns i [användar handboken för Sqoop](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html).

## <a name="use-sqoop-on-hdinsight-clusters-with-the-net-sdk"></a>Använda Sqoop på HDInsight-kluster med .NET SDK

HDInsight .NET SDK innehåller .NET-klient bibliotek, så att det blir enklare att arbeta med HDInsight-kluster från .NET. I det här avsnittet skapar du ett C#-konsol program för att exportera `hivesampletable` till Azure SQL Database-tabellen som du skapade från kraven.

## <a name="set-up"></a>Konfigurera

1. Starta Visual Studio och skapa ett C#-konsol program.

1. Gå till **verktyg**  >  **NuGet Package Manager**  >  **Package Manager-konsolen** och kör följande kommando:

    ```
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

## <a name="sqoop-export"></a>Sqoop-export

Från Hive till SQL Server.  I det här exemplet exporteras data från Hive- `hivesampletable` tabellen till `mobiledata` tabellen i SQL Database.

1. Använd följande kod i filen program. cs. Redigera koden för att ange värden för `ExistingClusterName` och `ExistingClusterPassword` .

    ```csharp
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;
    
    namespace SubmitHDInsightJobDotNet
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;
    
            private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
            private const string ExistingClusterPassword = "<Cluster User Password>";
            private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
            private const string ExistingClusterUsername = "admin";
    
            static void Main(string[] args)
            {
                System.Console.WriteLine("The application is running ...");
    
                var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
    
                SubmitSqoopJob();
    
                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }
    
            private static void SubmitSqoopJob()
            {
                var sqlDatabaseServerName = ExistingClusterName + "dbserver";
                var sqlDatabaseLogin = "sqluser";
                var sqlDatabaseLoginPassword = ExistingClusterPassword;
                var sqlDatabaseDatabaseName = ExistingClusterName + "db";
    
                // Connection string for using Azure SQL Database; Comment if using SQL Server
                var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
    
                // Connection string for using SQL Server; Uncomment if using SQL Server
                // var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
    
                //sqoop start
                var tableName = "mobiledata";
    
                var parameters = new SqoopJobSubmissionParameters
                {
                     Command = "export --connect " + connectionString + " --table " + tableName + " --hcatalog-table hivesampletable"
                };
                //sqoop end
    
                System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
                var response = _hdiJobManagementClient.JobManagement.SubmitSqoopJob(parameters);
                System.Console.WriteLine("Validating that the response is as expected...");
                System.Console.WriteLine("Response status code is " + response.StatusCode);
                System.Console.WriteLine("Validating the response object...");
                System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
            }
        }
    }
    ```

1. Om du vill köra programmet väljer du **F5** -tangenten.

## <a name="sqoop-import"></a>Sqoop-import

Från SQL Server till Azure Storage. Det här exemplet är beroende av att exporten ovan har utförts.  Det här exemplet importerar data från `mobiledata` tabellen i SQL Database till `wasb:///tutorials/usesqoop/importeddata` katalogen på klustrets standard lagrings konto.

1. Ersätt koden ovan i `//sqoop start //sqoop end` blocket med följande kod:

    ```csharp
    var tableName = "mobiledata";
    var exportDir = "/tutorials/usesqoop/importeddata";
    
    var parameters = new SqoopJobSubmissionParameters
    {
        Command = "import --connect " + connectionString + " --table " + tableName + " --target-dir " +  exportDir + " --fields-terminated-by \\t --lines-terminated-by \\n -m 1"
    };
    ```

1. Om du vill köra programmet väljer du **F5** -tangenten.

## <a name="limitations"></a>Begränsningar

Linux-baserade HDInsight visar följande begränsningar:

* Mass export: Sqoop-anslutningen som används för att exportera data till Microsoft SQL Server eller Azure SQL Database stöder för närvarande inte Mass infogningar.

* Batching: med hjälp av `-batch` växeln utför Sqoop flera infogningar i stället för att batch-sätta in åtgärderna.

## <a name="next-steps"></a>Nästa steg

Nu har du lärt dig hur du använder Sqoop. Mer information finns i:

* [Använda Apache Oozie med HDInsight](../hdinsight-use-oozie-linux-mac.md): Använd Sqoop-åtgärd i ett Oozie-arbetsflöde.
* [Ladda upp data till HDInsight](../hdinsight-upload-data.md): hitta andra metoder för att ladda upp data till HDInsight eller Azure Blob Storage.
