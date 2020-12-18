---
title: Skapa Azure Data Factory med .NET SDK
description: Skapa en Azure Data Factory och pipeline med .NET SDK för att kopiera data från en plats i Azure Blob Storage till en annan plats.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 12/18/2020
ms.author: jingwang
ms.openlocfilehash: c5e35fb8ab6a782ec79f10b1099f6781062c1d7c
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2020
ms.locfileid: "97678864"
---
# <a name="quickstart-create-a-data-factory-and-pipeline-using-net-sdk"></a>Snabbstart: Skapa en datafabrik och pipeline med .NET SDK

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Aktuell version](quickstart-create-data-factory-dot-net.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

I den här snabb starten beskrivs hur du använder .NET SDK för att skapa en Azure Data Factory. Den pipeline du skapar i den här datafabriken **kopierar** data från en mapp till en annan mapp i Azure Blob Storage. Om du vill se en självstudie som visar hur du **omvandlar** data med Azure Data Factory går du till [Tutorial: Transform data using Spark](tutorial-transform-data-spark-portal.md) (Självstudie: Omvandla data med Spark).

> [!NOTE]
> Den här artikeln ger inte någon detaljerad introduktion till Azure Data Factory-tjänsten. En introduktion till Azure Data Factory-tjänsten finns i [Introduktion till Azure Data Factory](introduction.md).

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

### <a name="visual-studio"></a>Visual Studio

Genom gången i den här artikeln används Visual Studio 2019. Procedurerna för Visual Studio 2013, 2015 eller 2017 skiljer sig något.

### <a name="azure-net-sdk"></a>SDK för Azure .NET

Ladda ned och installera [Azure .NET SDK](https://azure.microsoft.com/downloads/) på datorn.

## <a name="create-an-application-in-azure-active-directory"></a>Skapa ett program i Azure Active Directory

Från avsnitten i *How to: Använd portalen för att skapa ett Azure AD-program och tjänstens huvud namn som kan komma åt resurser*, följer du anvisningarna för att utföra dessa uppgifter:

1. Skapa ett program i [skapa ett Azure Active Directory-program](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)som representerar det .NET-program som du skapar i den här självstudien. För inloggnings-URL kan du ange en låtsas-URL enligt artikeln (`https://contoso.org/exampleapp`).
2. I [Hämta värden för att logga in](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in), hämtar du **program-ID** och **klient-ID** och noterar de här värdena som du använder senare i den här självstudien. 
3. I [certifikat och hemligheter](../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options), Hämta **autentiseringsnyckel** och anteckna det här värdet som du använder senare i den här självstudien.
4. I [tilldela programmet till en roll](../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application)tilldelar du programmet rollen **deltagare** på prenumerations nivå så att programmet kan skapa data fabriker i prenumerationen.

## <a name="create-a-visual-studio-project"></a>Skapa ett Visual Studio-projekt

Skapa sedan ett C# .NET-konsol program i Visual Studio:

1. Starta **Visual Studio**.
2. I fönstret Starta väljer du **skapa en ny projekt**  >  **konsol app (.NET Framework)**. .NET version 4.5.2 eller senare krävs.
3. Skriv **ADFv2QuickStart** i **projekt namn**.
4. Välj **Skapa** för att skapa projektet.

## <a name="install-nuget-packages"></a>Installera NuGet-paket

1. Välj **verktyg**  >  **NuGet Package Manager**  >  **Package** Manager-konsolen.
2. I fönstret **Package Manager-konsol** kör du följande kommandon för att installera paket. Mer information finns i [Microsoft. Azure. Management. DataFactory NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactory/).

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactory
    Install-Package Microsoft.Azure.Management.ResourceManager -IncludePrerelease
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

## <a name="create-a-data-factory-client"></a>Skapa en datafabriksklient

1. Öppna **Program.cs**, lägg till följande instruktioner för att lägga till referenser till namnområden.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Rest;
    using Microsoft.Rest.Serialization;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataFactory;
    using Microsoft.Azure.Management.DataFactory.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

2. Lägg till följande kod till **Main**-metoden som anger variablerna. Ersätt plats hållarna med dina egna värden. Om du vill se en lista med Azure-regioner där Data Factory är tillgängligt för närvarande markerar du de regioner du är intresserad av på följande sida. Expandera sedan **Analytics** och leta rätt på **Data Factory**: [Tillgängliga produkter per region](https://azure.microsoft.com/global-infrastructure/services/). Data lag ren (Azure Storage, Azure SQL Database och mer) och beräkningarna (HDInsight och andra) som används av Data Factory kan finnas i andra regioner.

   ```csharp
   // Set variables
   string tenantID = "<your tenant ID>";
   string applicationId = "<your application ID>";
   string authenticationKey = "<your authentication key for the application>";
   string subscriptionId = "<your subscription ID where the data factory resides>";
   string resourceGroup = "<your resource group where the data factory resides>";
   string region = "<the location of your resource group>";
   string dataFactoryName = 
       "<specify the name of data factory to create. It must be globally unique.>";
   string storageAccount = "<your storage account name to copy data>";
   string storageKey = "<your storage account key>";
   // specify the container and input folder from which all files 
   // need to be copied to the output folder. 
   string inputBlobPath =
       "<path to existing blob(s) to copy data from, e.g. containername/inputdir>";
   //specify the contains and output folder where the files are copied
   string outputBlobPath =
       "<the blob path to copy data to, e.g. containername/outputdir>";

   // name of the Azure Storage linked service, blob dataset, and the pipeline
   string storageLinkedServiceName = "AzureStorageLinkedService";
   string blobDatasetName = "BlobDataset";
   string pipelineName = "Adfv2QuickStartPipeline";
   ```

3. Lägg till följande kod i **main** -metoden som skapar en instans av klassen **klassen datafactorymanagementclient** . Du kan använda det här objektet för att skapa en datafabrik, en länkad tjänst, datauppsättningar och en pipeline. Du kan också använda det här objektet för att övervaka information om pipelinekörning.

   ```csharp
   // Authenticate and create a data factory management client
   var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
   ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
   AuthenticationResult result = context.AcquireTokenAsync(
       "https://management.azure.com/", cc).Result;
   ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
   var client = new DataFactoryManagementClient(cred) {
       SubscriptionId = subscriptionId };
   ```

## <a name="create-a-data-factory"></a>Skapa en datafabrik

Lägg till följande kod som skapar en **datafabrik** till **Main**-metoden. 

```csharp
// Create a data factory
Console.WriteLine("Creating data factory " + dataFactoryName + "...");
Factory dataFactory = new Factory
{
    Location = region,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(dataFactory, client.SerializationSettings));

while (client.Factories.Get(resourceGroup, dataFactoryName).ProvisioningState ==
       "PendingCreation")
{
    System.Threading.Thread.Sleep(1000);
}
```

## <a name="create-a-linked-service"></a>Skapa en länkad tjänst

Lägg till följande kod i **Main**-metoden som skapar en **länkad Azure Storage-tjänst**.

Du kan skapa länkade tjänster i en datafabrik för att länka ditt datalager och beräkna datafabrik-tjänster. I den här snabb starten behöver du bara skapa en Azure Storage länkad tjänst för både kopierings källan och mottagar lagret. den heter "AzureStorageLinkedService" i exemplet.

```csharp
// Create an Azure Storage linked service
Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");

LinkedServiceResource storageLinkedService = new LinkedServiceResource(
    new AzureStorageLinkedService
    {
        ConnectionString = new SecureString(
            "DefaultEndpointsProtocol=https;AccountName=" + storageAccount +
            ";AccountKey=" + storageKey)
    }
);
client.LinkedServices.CreateOrUpdate(
    resourceGroup, dataFactoryName, storageLinkedServiceName, storageLinkedService);
Console.WriteLine(SafeJsonConvert.SerializeObject(
    storageLinkedService, client.SerializationSettings));
```

## <a name="create-a-dataset"></a>Skapa en datauppsättning

Lägg till följande kod i **main** -metoden som skapar en **Azure Blob-datauppsättning**.

Du definierar en datauppsättning som representerar data som ska kopieras från en källa till en mottagare. I det här exemplet refererar denna blob-datauppsättning till den Azure Storage-länkade tjänst som du skapade i föregående steg. Datauppsättningen tar en parameter vars värde anges i en aktivitet som förbrukar datauppsättningen. Parametern används för att konstruera "folderPath" som pekar på var data finns/lagras.

```csharp
// Create an Azure Blob dataset
Console.WriteLine("Creating dataset " + blobDatasetName + "...");
DatasetResource blobDataset = new DatasetResource(
    new AzureBlobDataset
    {
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        },
        FolderPath = new Expression { Value = "@{dataset().path}" },
        Parameters = new Dictionary<string, ParameterSpecification>
        {
            { "path", new ParameterSpecification { Type = ParameterType.String } }
        }
    }
);
client.Datasets.CreateOrUpdate(
    resourceGroup, dataFactoryName, blobDatasetName, blobDataset);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
```

## <a name="create-a-pipeline"></a>Skapa en pipeline

Lägg till följande kod som skapar och **aktiverar en pipeline** till **Main**-metoden.

I det här exemplet innehåller den här pipelinen en aktivitet och använder två parametrar: sökväg för indata-blob och utgående BLOB-sökväg. Värdena för dessa parametrar anges när pipeline utlöses/körs. Kopieringsaktiviteten refererar till samma blobdatauppsättning som skapats i föregående steg som indata och utdata. När datauppsättningen används som indatauppsättning anges indatasökvägen. Och när datauppsättningen används som utdatauppsättning anges utdatasökvägen. 

```csharp
// Create a pipeline with a copy activity
Console.WriteLine("Creating pipeline " + pipelineName + "...");
PipelineResource pipeline = new PipelineResource
{
    Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "inputPath", new ParameterSpecification { Type = ParameterType.String } },
        { "outputPath", new ParameterSpecification { Type = ParameterType.String } }
    },
    Activities = new List<Activity>
    {
        new CopyActivity
        {
            Name = "CopyFromBlobToBlob",
            Inputs = new List<DatasetReference>
            {
                new DatasetReference()
                {
                    ReferenceName = blobDatasetName,
                    Parameters = new Dictionary<string, object>
                    {
                        { "path", "@pipeline().parameters.inputPath" }
                    }
                }
            },
            Outputs = new List<DatasetReference>
            {
                new DatasetReference
                {
                    ReferenceName = blobDatasetName,
                    Parameters = new Dictionary<string, object>
                    {
                        { "path", "@pipeline().parameters.outputPath" }
                    }
                }
            },
            Source = new BlobSource { },
            Sink = new BlobSink { }
        }
    }
};
client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, pipeline);
Console.WriteLine(SafeJsonConvert.SerializeObject(pipeline, client.SerializationSettings));
```

## <a name="create-a-pipeline-run"></a>Skapa en pipelinekörning

Lägg till följande kod i **Main**-metoden för att **utlösa en pipelinekörning**.

Den här koden anger också värden för parametrarna **inputPath** och **outputPath** som anges i pipelinen med de faktiska värdena för käll-och mottagar-BLOB-sökvägar.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> parameters = new Dictionary<string, object>
{
    { "inputPath", inputBlobPath },
    { "outputPath", outputBlobPath }
};
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(
    resourceGroup, dataFactoryName, pipelineName, parameters: parameters
).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="monitor-a-pipeline-run"></a>Övervaka en pipelinekörning

1. Lägg till följande kod i **Main**-metoden för att kontinuerligt kontrollera status tills den har slutat att kopiera data.

   ```csharp
   // Monitor the pipeline run
   Console.WriteLine("Checking pipeline run status...");
   PipelineRun pipelineRun;
   while (true)
   {
       pipelineRun = client.PipelineRuns.Get(
           resourceGroup, dataFactoryName, runResponse.RunId);
       Console.WriteLine("Status: " + pipelineRun.Status);
       if (pipelineRun.Status == "InProgress" || pipelineRun.Status == "Queued")
           System.Threading.Thread.Sleep(15000);
       else
           break;
   }
   ```

2. Lägg till följande kod i **main** -metoden som hämtar körnings information om kopierings aktiviteten, till exempel storleken på de data som läses eller skrivs.

   ```csharp
   // Check the copy activity run details
   Console.WriteLine("Checking copy activity run details...");

   RunFilterParameters filterParams = new RunFilterParameters(
       DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10));
   ActivityRunsQueryResponse queryResponse = client.ActivityRuns.QueryByPipelineRun(
       resourceGroup, dataFactoryName, runResponse.RunId, filterParams);
   if (pipelineRun.Status == "Succeeded")
       Console.WriteLine(queryResponse.Value.First().Output);
   else
       Console.WriteLine(queryResponse.Value.First().Error);
   Console.WriteLine("\nPress any key to exit...");
   Console.ReadKey();
   ```

## <a name="run-the-code"></a>Kör koden

Skapa och starta programmet och kontrollera sedan pipelinekörningen.

Konsolen skriver ut förloppet för skapandet av datafabriken, den länkade tjänsten, datauppsättningar, pipeline och pipelinekörning. Sedan kontrolleras status för pipelinekörningen. Vänta tills du ser körnings informationen för kopierings aktiviteten med storleken på Läs/Skriv-data. Använd sedan verktyg som [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) för att kontrol lera att bloben (erna) har kopierats till "outputBlobPath" från "inputBlobPath" som du har angett i variablerna.

### <a name="sample-output"></a>Exempelutdata

```json
Creating data factory SPv2Factory0907...
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": {
        "value": "DefaultEndpointsProtocol=https;AccountName=<storageAccountName>;AccountKey=<storageAccountKey>",
        "type": "SecureString"
      }
    }
  }
}
Creating dataset BlobDataset...
{
  "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": {
        "value": "@{dataset().path}",
        "type": "Expression"
      }
    },
    "linkedServiceName": {
      "referenceName": "AzureStorageLinkedService",
      "type": "LinkedServiceReference"
    },
    "parameters": {
      "path": {
        "type": "String"
      }
    }
  }
}
Creating pipeline Adfv2QuickStartPipeline...
{
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "inputs": [
          {
            "referenceName": "BlobDataset",
            "parameters": {
              "path": "@pipeline().parameters.inputPath"
            },
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "BlobDataset",
            "parameters": {
              "path": "@pipeline().parameters.outputPath"
            },
            "type": "DatasetReference"
          }
        ],
        "name": "CopyFromBlobToBlob"
      }
    ],
    "parameters": {
      "inputPath": {
        "type": "String"
      },
      "outputPath": {
        "type": "String"
      }
    }
  }
}
Creating pipeline run...
Pipeline run ID: 308d222d-3858-48b1-9e66-acd921feaa09
Checking pipeline run status...
Status: InProgress
Status: InProgress
Checking copy activity run details...
{
    "dataRead": 331452208,
    "dataWritten": 331452208,
    "copyDuration": 23,
    "throughput": 14073.209,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)",
    "usedDataIntegrationUnits": 2,
    "billedDuration": 23
}

Press any key to exit...
```

## <a name="verify-the-output"></a>Verifiera utdata

Pipelinen skapar automatiskt mappen utdata i BLOB-behållaren **adftutorial** . Sedan kopieras **emp.txt** -filen från mappen indata till mappen utdata. 

1. På sidan för **adftutorial** -behållaren som du avbröt i i [Lägg till en mapp för indata och filen för BLOB-behållaren](#add-an-input-folder-and-file-for-the-blob-container) ovan väljer du **Uppdatera** för att visa mappen utdata. Azure Portal 
2. Välj **utdata** i mapplistan.
3. Kontrollera att **emp.txt** har kopierats till utdatamappen. 

## <a name="clean-up-resources"></a>Rensa resurser

För att program mässigt ta bort data fabriken lägger du till följande rader kod i programmet: 

```csharp
Console.WriteLine("Deleting the data factory");
client.Factories.Delete(resourceGroup, dataFactoryName);
```

## <a name="next-steps"></a>Nästa steg

Pipeline i det här exemplet kopierar data från en plats till en annan i Azure Blob Storage. Gå igenom [självstudiekurserna](tutorial-copy-data-dot-net.md) om du vill lära dig hur du använder Data Factory i fler scenarier. 
