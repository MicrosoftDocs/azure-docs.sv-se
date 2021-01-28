---
title: Kör Apache Hive frågor med HDInsight .NET SDK – Azure
description: Lär dig hur du skickar Apache Hadoop jobb till Azure HDInsight Apache Hadoop med hjälp av HDInsight .NET SDK.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 12/24/2019
ms.openlocfilehash: 17264fc2a7d0a51de545e329ccf12ad45ad49d46
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98927245"
---
# <a name="run-apache-hive-queries-using-hdinsight-net-sdk"></a>Köra Apache Hive frågor med HDInsight .NET SDK

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Lär dig hur du skickar Apache Hive frågor med hjälp av HDInsight .NET SDK. Du kan skriva ett C#-program för att skicka en Hive-fråga om att Visa Hive-tabeller och visa resultatet.

> [!NOTE]  
> Stegen i den här artikeln måste utföras från en Windows-klient. För information om hur du använder en Linux-, OS X-eller UNIX-klient för att arbeta med Hive, använder du tabbväljaren som visas överst i artikeln.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar den här artikeln måste du ha följande objekt:

* Ett Apache Hadoop kluster i HDInsight. Se [Kom igång med Linux-baserat Hadoop i HDInsight](apache-hadoop-linux-tutorial-get-started.md).

    > [!IMPORTANT]  
    > Från och med den 15 september 2017 stöder HDInsight .NET SDK bara returnerade Hive-frågeresultat från Azure Storage-konton. Om du använder det här exemplet med ett HDInsight-kluster som använder Azure Data Lake Storage som primär lagring, kan du inte hämta Sök resultat med hjälp av .NET SDK.

* [Visual Studio](https://visualstudio.microsoft.com/vs/community/) 2013 och senare. Minst arbets belastning **.net Desktop-utveckling** bör installeras.

## <a name="run-a-hive-query"></a>Köra en Hive-fråga

HDInsight .NET SDK innehåller .NET-klient bibliotek, vilket gör det enklare att arbeta med HDInsight-kluster från .NET.

1. Skapa ett C#-konsol program i Visual Studio.

1. Kör följande kommando från NuGet Package Manager-konsolen:

    ```console
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

1. Redigera koden nedan för att initiera värdena för variablerna: `ExistingClusterName, ExistingClusterUsername, ExistingClusterPassword,DefaultStorageAccountName,DefaultStorageAccountKey,DefaultStorageContainerName` . Använd sedan den ändrade koden som hela innehållet i **program.cs** i Visual Studio.

    ```csharp
    using System.Collections.Generic;
    using System.IO;
    using System.Text;
    using System.Threading;
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;

    namespace SubmitHDInsightJobDotNet
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;

            private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
            private const string ExistingClusterUsername = "<Cluster Username>";
            private const string ExistingClusterPassword = "<Cluster User Password>";

            // Only Azure Storage accounts are supported by the SDK
            private const string DefaultStorageAccountName = "<Default Storage Account Name>";
            private const string DefaultStorageAccountKey = "<Default Storage Account Key>";
            private const string DefaultStorageContainerName = "<Default Blob Container Name>";

            private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";

            static void Main(string[] args)
            {
                System.Console.WriteLine("The application is running ...");

                var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);

                SubmitHiveJob();

                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }

            private static void SubmitHiveJob()
            {
                Dictionary<string, string> defines = new Dictionary<string, string> { { "hive.execution.engine", "tez" }, { "hive.exec.reducers.max", "1" } };
                List<string> args = new List<string> { { "argA" }, { "argB" } };
                var parameters = new HiveJobSubmissionParameters
                {
                    Query = "SHOW TABLES",
                    Defines = defines,
                    Arguments = args
                };

                System.Console.WriteLine("Submitting the Hive job to the cluster...");
                var jobResponse = _hdiJobManagementClient.JobManagement.SubmitHiveJob(parameters);
                var jobId = jobResponse.JobSubmissionJsonResponse.Id;
                System.Console.WriteLine("Response status code is " + jobResponse.StatusCode);
                System.Console.WriteLine("JobId is " + jobId);

                System.Console.WriteLine("Waiting for the job completion ...");

                // Wait for job completion
                var jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                while (!jobDetail.Status.JobComplete)
                {
                    Thread.Sleep(1000);
                    jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                }

                // Get job output
                var storageAccess = new AzureStorageAccess(DefaultStorageAccountName, DefaultStorageAccountKey,
                    DefaultStorageContainerName);
                var output = (jobDetail.ExitValue == 0)
                    ? _hdiJobManagementClient.JobManagement.GetJobOutput(jobId, storageAccess) // fetch stdout output in case of success
                    : _hdiJobManagementClient.JobManagement.GetJobErrorLogs(jobId, storageAccess); // fetch stderr output in case of failure

                System.Console.WriteLine("Job output is: ");

                using (var reader = new StreamReader(output, Encoding.UTF8))
                {
                    string value = reader.ReadToEnd();
                    System.Console.WriteLine(value);
                }
            }
        }
    }
    ```

1. Tryck på **F5** för att köra programmet.

Utdata från programmet bör likna följande:

![HDInsight Hadoop Hive-jobb utdata](./media/apache-hadoop-use-hive-dotnet-sdk/hdinsight-hadoop-use-hive-net-sdk-output.png)

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du skickar Apache Hive frågor med hjälp av HDInsight .NET SDK. Mer information finns i följande artiklar:

* [Kom igång med Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md)
* [Skapa Apache Hadoop kluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
* [HDInsight .NET SDK-referens](/dotnet/api/overview/azure/hdinsight)
* [Använda Apache Sqoop med HDInsight](apache-hadoop-use-sqoop-mac-linux.md)
* [Skapa .NET HDInsight-program med icke-interaktiv autentisering](../hdinsight-create-non-interactive-authentication-dotnet-applications.md)
