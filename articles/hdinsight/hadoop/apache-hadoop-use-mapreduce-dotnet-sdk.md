---
title: Skicka MapReduce-jobb med HDInsight .NET SDK – Azure
description: Lär dig hur du skickar MapReduce-jobb till Azure HDInsight Apache Hadoop med hjälp av HDInsight .NET SDK.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 01/15/2020
ms.openlocfilehash: 8fbcb66be11c7c77a9bfaf0e6ec790622dcbbda7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98932177"
---
# <a name="run-mapreduce-jobs-using-hdinsight-net-sdk"></a>Köra MapReduce-jobb med HDInsight .NET SDK

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Lär dig hur du skickar MapReduce-jobb med HDInsight .NET SDK. HDInsight-kluster levereras med en jar-fil med vissa MapReduce-exempel. Jar-filen är `/example/jars/hadoop-mapreduce-examples.jar` .  Ett av exemplen är **WORDCOUNT**. Du utvecklar ett C#-konsol program för att skicka ett WORDCOUNT-jobb.  Jobbet läser `/example/data/gutenberg/davinci.txt` filen och ger ut resultatet till `/example/data/davinciwordcount` .  Om du vill köra programmet igen måste du rensa mappen utdata.

> [!NOTE]  
> Stegen i den här artikeln måste utföras från en Windows-klient. För information om hur du använder en Linux-, OS X-eller UNIX-klient för att arbeta med Hive, använder du tabbväljaren som visas överst i artikeln.

## <a name="prerequisites"></a>Förutsättningar

* Ett Apache Hadoop kluster i HDInsight. Se [skapa Apache Hadoop kluster med hjälp av Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md).

* [Visual Studio](https://visualstudio.microsoft.com/vs/community/).

## <a name="submit-mapreduce-jobs-using-hdinsight-net-sdk"></a>Skicka MapReduce-jobb med HDInsight .NET SDK

HDInsight .NET SDK innehåller .NET-klient bibliotek, vilket gör det enklare att arbeta med HDInsight-kluster från .NET.

1. Starta Visual Studio och skapa ett C#-konsol program.

1. Gå till **verktyg**  >  **NuGet Package Manager**  >  **Package Manager-konsolen** och ange följande kommando:

    ```   
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

1. Kopiera koden nedan till **program. cs**. Redigera sedan koden genom att ange värdena för: `existingClusterName` , `existingClusterPassword` ,, `defaultStorageAccountName` `defaultStorageAccountKey` och `defaultStorageContainerName` .

    ```csharp
    using System.Collections.Generic;
    using System.IO;
    using System.Text;
    using System.Threading;
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    
    namespace SubmitHDInsightJobDotNet
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;
    
            private const string existingClusterName = "<Your HDInsight Cluster Name>";
            private const string existingClusterPassword = "<Cluster User Password>";
            private const string defaultStorageAccountName = "<Default Storage Account Name>"; 
            private const string defaultStorageAccountKey = "<Default Storage Account Key>";
            private const string defaultStorageContainerName = "<Default Blob Container Name>";
    
            private const string existingClusterUsername = "admin";
            private const string existingClusterUri = existingClusterName + ".azurehdinsight.net";
            private const string sourceFile = "/example/data/gutenberg/davinci.txt";
            private const string outputFolder = "/example/data/davinciwordcount";
    
            static void Main(string[] args)
            {
                System.Console.WriteLine("The application is running ...");
    
                var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = existingClusterUsername, Password = existingClusterPassword };
                _hdiJobManagementClient = new HDInsightJobManagementClient(existingClusterUri, clusterCredentials);
    
                SubmitMRJob();
    
                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }
    
            private static void SubmitMRJob()
            {
                List<string> args = new List<string> { { "/example/data/gutenberg/davinci.txt" }, { "/example/data/davinciwordcount" } };
    
                var paras = new MapReduceJobSubmissionParameters
                {
                    JarFile = @"/example/jars/hadoop-mapreduce-examples.jar",
                    JarClass = "wordcount",
                    Arguments = args
                };
    
                System.Console.WriteLine("Submitting the MR job to the cluster...");
                var jobResponse = _hdiJobManagementClient.JobManagement.SubmitMapReduceJob(paras);
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
                System.Console.WriteLine("Job output is: ");
                var storageAccess = new AzureStorageAccess(defaultStorageAccountName, defaultStorageAccountKey,
                    defaultStorageContainerName);
    
                if (jobDetail.ExitValue == 0)
                {
                    // Create the storage account object
                    CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=" +
                        defaultStorageAccountName +
                        ";AccountKey=" + defaultStorageAccountKey);
    
                    // Create the blob client.
                    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
                    // Retrieve reference to a previously created container.
                    CloudBlobContainer container = blobClient.GetContainerReference(defaultStorageContainerName);
    
                    CloudBlockBlob blockBlob = container.GetBlockBlobReference(outputFolder.Substring(1) + "/part-r-00000");
    
                    using (var stream = blockBlob.OpenRead())
                    {
                        using (StreamReader reader = new StreamReader(stream))
                        {
                            while (!reader.EndOfStream)
                            {
                                System.Console.WriteLine(reader.ReadLine());
                            }
                        }
                    }
                }
                else
                {
                    // fetch stderr output in case of failure
                    var output = _hdiJobManagementClient.JobManagement.GetJobErrorLogs(jobId, storageAccess);
    
                    using (var reader = new StreamReader(output, Encoding.UTF8))
                    {
                        string value = reader.ReadToEnd();
                        System.Console.WriteLine(value);
                    }
    
                }
            }
        }
    }

    ```

1. Tryck på **F5** för att köra programmet.

Om du vill köra jobbet igen måste du ändra mappnamnet för utdata, i det här exemplet `/example/data/davinciwordcount` .

När jobbet har slutförts, skriver programmet ut innehållet i utdatafilen `part-r-00000` .

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig flera olika sätt att skapa ett HDInsight-kluster. Mer information finns i följande artiklar:

* Information om hur du skickar ett Hive-jobb finns i [köra apache Hive frågor med HDInsight .NET SDK](apache-hadoop-use-hive-dotnet-sdk.md).
* Information om hur du skapar HDInsight-kluster finns i [skapa Linux-baserade Apache Hadoop kluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Information om hur du hanterar HDInsight-kluster finns i [hantera Apache Hadoop kluster i HDInsight](../hdinsight-administer-use-portal-linux.md).
* Information om hur du lär dig av HDInsight .NET SDK finns i [referens för HDInsight .NET SDK](/dotnet/api/overview/azure/hdinsight).
* Information om icke-interaktiv autentisering till Azure finns i [skapa icke-interaktiv autentisering .net HDInsight-program](../hdinsight-create-non-interactive-authentication-dotnet-applications.md).
