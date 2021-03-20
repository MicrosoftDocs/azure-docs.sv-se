---
title: Förgrening i Azure Data Factory pipeline
description: Lär dig hur du styr flödet av data i Azure Data Factory genom branchning och kedjesammansättning av aktiviteter.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 9/27/2019
ms.openlocfilehash: 111fff6e35bed9c2af9c638549362039f25def12
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100386024"
---
# <a name="branching-and-chaining-activities-in-a-data-factory-pipeline"></a>Branchning och kedjesammansättning av aktiviteter i en Data Factory-pipeline

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

I den här självstudien skapar du en Data Factory pipeline som demonstrerar vissa kontroll flödes funktioner. Den här pipelinen kopierar från en behållare i Azure Blob Storage till en annan behållare i samma lagrings konto. Om kopierings aktiviteten lyckas skickar pipelinen information om den lyckade kopierings åtgärden i ett e-postmeddelande. Den informationen kan omfatta mängden data som skrivs. Om kopierings aktiviteten Miss lyckas skickar den information om kopierings felet, till exempel fel meddelandet, i ett e-postmeddelande. I självstudiekursen visas olika exempel på hur du skickar parametrar.

Den här bilden ger en översikt över scenariot:

![Diagrammet visar Azure-Blob Storage, som är målet för en kopia, som vid lyckad skickar ett e-postmeddelande med information eller, vid fel, skickar ett e-postmeddelande med fel information.](media/tutorial-control-flow/overview.png)

Den här självstudien visar hur du utför följande uppgifter:

> [!div class="checklist"]
> * Skapa en datafabrik
> * Skapa en länkad Azure Storage-tjänst
> * Skapa en Azure Blob-datauppsättning
> * Skapa en pipeline som innehåller en kopieringsaktivitet och en webbaktivitet
> * Skicka utdata för aktiviteter till efterföljande aktiviteter
> * Använda parameter Passing och systemvariabler
> * Starta en pipelinekörning
> * Övervaka pipelinen och aktivitetskörningar

I den här självstudiekursen används .NET SDK. Du kan använda andra metoder för att interagera med Azure Data Factory. För Data Factory snabb starter, se [fem minuters snabb starter](./quickstart-create-data-factory-portal.md).

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

* Azure Storage konto. Du använder Blob Storage som käll data lager. Om du inte har ett Azure Storage-konto kan du läsa [skapa ett lagrings konto](../storage/common/storage-account-create.md).
* Azure Storage Explorer. Information om hur du installerar det här verktyget finns [Azure Storage Explorer](https://storageexplorer.com/).
* Azure SQL Database. Du använder databasen som mottagare för datalagringen. Om du inte har en databas i Azure SQL Database kan du läsa [skapa en databas i Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md).
* Visual Studio. I den här artikeln används Visual Studio 2019.
* Azure .NET SDK. Hämta och installera [Azure .NET SDK](https://azure.microsoft.com/downloads/).

En lista över Azure-regioner där Data Factory för närvarande är tillgänglig finns i [produkt tillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/). Data lager och beräkningar kan finnas i andra regioner. Butikerna innehåller Azure Storage och Azure SQL Database. Beräkningarna inkluderar HDInsight, som Data Factory använder.

Skapa ett program enligt beskrivningen i [skapa ett Azure Active Directory-program](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal). Tilldela programmet till **deltagar** rollen genom att följa anvisningarna i samma artikel. Du behöver flera värden för senare delar av den här självstudien, till exempel **program-ID** och **katalog (klient)-ID**.

### <a name="create-a-blob-table"></a>Skapa en BLOB-tabell

1. Öppna en textredigerare. Kopiera följande text och spara den lokalt som *input.txt*.

   ```
   Ethel|Berg
   Tamika|Walsh
   ```

1. Öppna Azure Storage Explorer. Expandera ditt lagrings konto. Högerklicka på **Blobcontainrar** och välj **Skapa blobcontainer**.
1. Namnge den nya behållaren *adfv2branch* och välj **Ladda upp** för att lägga till *input.txt* -filen i behållaren.

## <a name="create-visual-studio-project"></a>Skapa Visual Studio-projekt<a name="create-visual-studio-project"></a>

Skapa ett C# .NET-konsol program:

1. Starta Visual Studio och välj **skapa ett nytt projekt**.
1. I **skapa ett nytt projekt** väljer du **konsol program (.NET Framework)** för C# och väljer **sedan nästa**.
1. Ge projektet namnet *ADFv2BranchTutorial*.
1. Välj **.NET version 4.5.2** eller senare och välj sedan **skapa**.

### <a name="install-nuget-packages"></a>Installera NuGet-paket

1. Välj **Verktyg** > **NuGet-pakethanteraren** > **Pakethanterarkonsolen**.
1. Kör följande kommandon i **Package Manager Console** för att installera paket. Mer information finns i [Microsoft. Azure. Management. DataFactory NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactory/) .

   ```powershell
   Install-Package Microsoft.Azure.Management.DataFactory
   Install-Package Microsoft.Azure.Management.ResourceManager -IncludePrerelease
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```

### <a name="create-a-data-factory-client"></a>Skapa en datafabriksklient

1. Öppna *program. cs* och Lägg till följande-uttryck:

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Linq;
   using Microsoft.Rest;
   using Microsoft.Azure.Management.ResourceManager;
   using Microsoft.Azure.Management.DataFactory;
   using Microsoft.Azure.Management.DataFactory.Models;
   using Microsoft.IdentityModel.Clients.ActiveDirectory;
   ```

1. Lägg till dessa statiska variabler i- `Program` klassen. Ersätt platshållarna med dina egna värden.

   ```csharp
   // Set variables
   static string tenantID = "<tenant ID>";
   static string applicationId = "<application ID>";
   static string authenticationKey = "<Authentication key for your application>";
   static string subscriptionId = "<Azure subscription ID>";
   static string resourceGroup = "<Azure resource group name>";

   static string region = "East US";
   static string dataFactoryName = "<Data factory name>";

   // Specify the source Azure Blob information
   static string storageAccount = "<Azure Storage account name>";
   static string storageKey = "<Azure Storage account key>";
   // confirm that you have the input.txt file placed in th input folder of the adfv2branch container.
   static string inputBlobPath = "adfv2branch/input";
   static string inputBlobName = "input.txt";
   static string outputBlobPath = "adfv2branch/output";
   static string emailReceiver = "<specify email address of the receiver>";

   static string storageLinkedServiceName = "AzureStorageLinkedService";
   static string blobSourceDatasetName = "SourceStorageDataset";
   static string blobSinkDatasetName = "SinkStorageDataset";
   static string pipelineName = "Adfv2TutorialBranchCopy";

   static string copyBlobActivity = "CopyBlobtoBlob";
   static string sendFailEmailActivity = "SendFailEmailActivity";
   static string sendSuccessEmailActivity = "SendSuccessEmailActivity";
   ```

1. Lägg till följande kod i metoden `Main`. Den här koden skapar en instans av `DataFactoryManagementClient` klassen. Du använder sedan det här objektet för att skapa Data Factory, länkad data uppsättning och pipeline. Du kan också använda det här objektet för att övervaka körnings informationen för pipelinen.

   ```csharp
   // Authenticate and create a data factory management client
   var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
   ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
   AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
   ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
   var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
   ```

### <a name="create-a-data-factory"></a>Skapa en datafabrik

1. Lägg till en `CreateOrUpdateDataFactory` metod i filen *program. cs* :

   ```csharp
   static Factory CreateOrUpdateDataFactory(DataFactoryManagementClient client)
   {
       Console.WriteLine("Creating data factory " + dataFactoryName + "...");
       Factory resource = new Factory
       {
           Location = region
       };
       Console.WriteLine(SafeJsonConvert.SerializeObject(resource, client.SerializationSettings));

       Factory response;
       {
           response = client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, resource);
       }

       while (client.Factories.Get(resourceGroup, dataFactoryName).ProvisioningState == "PendingCreation")
       {
           System.Threading.Thread.Sleep(1000);
       }
       return response;
   }
   ```

1. Lägg till följande rad i- `Main` metoden som skapar en data fabrik:

   ```csharp
   Factory df = CreateOrUpdateDataFactory(client);
   ```

## <a name="create-an-azure-storage-linked-service"></a>Skapa en länkad Azure Storage-tjänst

1. Lägg till en `StorageLinkedServiceDefinition` metod i filen *program. cs* :

   ```csharp
   static LinkedServiceResource StorageLinkedServiceDefinition(DataFactoryManagementClient client)
   {
      Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");
      AzureStorageLinkedService storageLinkedService = new AzureStorageLinkedService
      {
          ConnectionString = new SecureString("DefaultEndpointsProtocol=https;AccountName=" + storageAccount + ";AccountKey=" + storageKey)
      };
      Console.WriteLine(SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings));
      LinkedServiceResource linkedService = new LinkedServiceResource(storageLinkedService, name:storageLinkedServiceName);
      return linkedService;
   }
   ```

1. Lägg till följande rad i `Main` -metoden som skapar en Azure Storage länkad tjänst:

   ```csharp
   client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
   ```

Mer information om vilka egenskaper som stöds och information finns i [länkade tjänst egenskaper](connector-azure-blob-storage.md#linked-service-properties).

## <a name="create-datasets"></a>Skapa datauppsättningar

I det här avsnittet skapar du två data uppsättningar, en för källan och en för mottagaren.

### <a name="create-a-dataset-for-a-source-azure-blob"></a>Skapa en data uppsättning för en Azure Blob-källa

Lägg till en metod som skapar en *Azure Blob-datauppsättning*. Mer information om vilka egenskaper och information som stöds finns i [Egenskaper för Azure Blob-datauppsättningar](connector-azure-blob-storage.md#dataset-properties).

Lägg till en `SourceBlobDatasetDefinition` metod i filen *program. cs* :

```csharp
static DatasetResource SourceBlobDatasetDefinition(DataFactoryManagementClient client)
{
    Console.WriteLine("Creating dataset " + blobSourceDatasetName + "...");
    AzureBlobDataset blobDataset = new AzureBlobDataset
    {
        FolderPath = new Expression { Value = "@pipeline().parameters.sourceBlobContainer" },
        FileName = inputBlobName,
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        }
    };
    Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
    DatasetResource dataset = new DatasetResource(blobDataset, name:blobSourceDatasetName);
    return dataset;
}
```

Du definierar en datauppsättning som representerar källdata i Azure Blob. Denna BLOB-datauppsättning refererar till den Azure Storage länkade tjänsten som stöds i föregående steg. BLOB-datauppsättningen beskriver platsen för blobben att kopiera från: *FolderPath* och *filename*.

Observera att parametrarna för *FolderPath* används. `sourceBlobContainer` är namnet på parametern och uttrycket ersätts med värdena som skickas i pipeline-körningen. Syntaxen för att definiera parametrar är `@pipeline().parameters.<parameterName>`

### <a name="create-a-dataset-for-a-sink-azure-blob"></a>Skapa en data uppsättning för en mottagar Azure-Blob

1. Lägg till en `SourceBlobDatasetDefinition` metod i filen *program. cs* :

   ```csharp
   static DatasetResource SinkBlobDatasetDefinition(DataFactoryManagementClient client)
   {
       Console.WriteLine("Creating dataset " + blobSinkDatasetName + "...");
       AzureBlobDataset blobDataset = new AzureBlobDataset
       {
           FolderPath = new Expression { Value = "@pipeline().parameters.sinkBlobContainer" },
           LinkedServiceName = new LinkedServiceReference
           {
               ReferenceName = storageLinkedServiceName
           }
       };
       Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
       DatasetResource dataset = new DatasetResource(blobDataset, name: blobSinkDatasetName);
       return dataset;
   }
   ```

1. Lägg till följande kod i- `Main` metoden som skapar både Azure Blob Source och Sink-datauppsättningar.

   ```csharp
   client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));

   client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));
   ```

## <a name="create-a-c-class-emailrequest"></a>Skapa en C#-klass: EmailRequest

Skapa en klass med namnet i C#-projektet `EmailRequest` . Den här klassen definierar vilka egenskaper som pipelinen skickar i Body-begäran när ett e-postmeddelande skickas. I den här självstudiekursen skickar pipelinen fyra egenskaper från pipelinen till e-postmeddelandet:

* Meddelande. E-postmeddelandets brödtext. För en lyckad kopiering innehåller den här egenskapen mängden data som skrivits. För en misslyckad kopia innehåller den här egenskapen information om felet.
* Data fabriks namn. Namnet på data fabriken.
* Pipeline-namn. Namnet på pipeline.
* Mottagare. Parameter som passerar genom. Den här egenskapen anger mottagaren av e-postmeddelandet.

```csharp
    class EmailRequest
    {
        [Newtonsoft.Json.JsonProperty(PropertyName = "message")]
        public string message;

        [Newtonsoft.Json.JsonProperty(PropertyName = "dataFactoryName")]
        public string dataFactoryName;

        [Newtonsoft.Json.JsonProperty(PropertyName = "pipelineName")]
        public string pipelineName;

        [Newtonsoft.Json.JsonProperty(PropertyName = "receiver")]
        public string receiver;

        public EmailRequest(string input, string df, string pipeline, string receiverName)
        {
            message = input;
            dataFactoryName = df;
            pipelineName = pipeline;
            receiver = receiverName;
        }
    }
```

## <a name="create-email-workflow-endpoints"></a>Skapa slutpunkter för e-postarbetsflödet

För att utlösa utskicket av ett e-postmeddelande använder du [Logic Apps](../logic-apps/logic-apps-overview.md) för att definiera arbetsflödet. Mer information om hur du skapar ett arbets flöde för Logic Apps finns i [så här skapar du en logisk app](../logic-apps/quickstart-create-first-logic-app-workflow.md).

### <a name="success-email-workflow"></a>Lyckat e-postarbetsflöde

Skapa ett Logic Apps-arbetsflöde med namnet *CopySuccessEmail* i [Azure Portal](https://portal.azure.com). Definiera arbets flödes utlösaren som `When an HTTP request is received` . För begärandeutlösaren fyller du i `Request Body JSON Schema` med följande JSON:

```json
{
    "properties": {
        "dataFactoryName": {
            "type": "string"
        },
        "message": {
            "type": "string"
        },
        "pipelineName": {
            "type": "string"
        },
        "receiver": {
            "type": "string"
        }
    },
    "type": "object"
}
```

Ditt arbets flöde ser ut ungefär som i följande exempel:

![Lyckat e-postarbetsflöde](media/tutorial-control-flow/success-email-workflow-trigger.png)

Det här JSON-innehållet överensstämmer med `EmailRequest` klassen som du skapade i föregående avsnitt.

Lägg till en åtgärd för `Office 365 Outlook – Send an email` . För åtgärden **Skicka ett e-postmeddelande** anpassar du hur du vill formatera e-postmeddelandet med hjälp av egenskaperna som skickas i begär ande **textens** JSON-schema. Här är ett exempel:

![Logic App Designer – åtgärden skicka e-post](media/tutorial-control-flow/customize-send-email-action.png)

När du har sparat arbets flödet kopierar du och sparar värdet **http post URL** från utlösaren.

## <a name="fail-email-workflow"></a>Arbetsflöde för e-postmeddelande om misslyckad kopiering

Klona **CopySuccessEmail** som ett annat Logic Apps arbets flöde med namnet *CopyFailEmail*. `Request Body JSON schema` är samma i begärandeutlösaren. Ändra formatet för ditt e-postmeddelande som `Subject` för att skapa ett e-postmeddelande om att kopieringen misslyckats. Här är ett exempel:

![Logic App Designer – arbets flöde för misslyckad e-post](media/tutorial-control-flow/fail-email-workflow.png)

När du har sparat arbets flödet kopierar du och sparar värdet **http post URL** från utlösaren.

Du bör nu ha två arbets flödes-URL: er, som i följande exempel:

```csharp
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000

//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

## <a name="create-a-pipeline"></a>Skapa en pipeline

Gå tillbaka till projektet i Visual Studio. Nu ska vi lägga till koden som skapar en pipeline med en kopierings aktivitet och `DependsOn` egenskap. I den här självstudien innehåller pipelinen en aktivitet, en kopierings aktivitet, som tar i BLOB-datauppsättningen som en källa och en annan BLOB-datauppsättning som en mottagare. Om kopierings aktiviteten lyckas eller Miss lyckas anropas olika e-postuppgifter.

I denna pipeline kan du använda följande funktioner:

* Parametrar
* Webb aktivitet
* Aktivitetsberoende
* Använda utdata från en aktivitet som indata för en annan aktivitet

1. Lägg till den här metoden i projektet. Följande avsnitt innehåller mer information.

    ```csharp
    static PipelineResource PipelineDefinition(DataFactoryManagementClient client)
            {
                Console.WriteLine("Creating pipeline " + pipelineName + "...");
                PipelineResource resource = new PipelineResource
                {
                    Parameters = new Dictionary<string, ParameterSpecification>
                    {
                        { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
                        { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
                        { "receiver", new ParameterSpecification { Type = ParameterType.String } }

                    },
                    Activities = new List<Activity>
                    {
                        new CopyActivity
                        {
                            Name = copyBlobActivity,
                            Inputs = new List<DatasetReference>
                            {
                                new DatasetReference
                                {
                                    ReferenceName = blobSourceDatasetName
                                }
                            },
                            Outputs = new List<DatasetReference>
                            {
                                new DatasetReference
                                {
                                    ReferenceName = blobSinkDatasetName
                                }
                            },
                            Source = new BlobSource { },
                            Sink = new BlobSink { }
                        },
                        new WebActivity
                        {
                            Name = sendSuccessEmailActivity,
                            Method = WebActivityMethod.POST,
                            Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/00000000000000000000000000000000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=0000000000000000000000000000000000000000000000",
                            Body = new EmailRequest("@{activity('CopyBlobtoBlob').output.dataWritten}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
                            DependsOn = new List<ActivityDependency>
                            {
                                new ActivityDependency
                                {
                                    Activity = copyBlobActivity,
                                    DependencyConditions = new List<String> { "Succeeded" }
                                }
                            }
                        },
                        new WebActivity
                        {
                            Name = sendFailEmailActivity,
                            Method =WebActivityMethod.POST,
                            Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/000000000000000000000000000000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=0000000000000000000000000000000000000000000",
                            Body = new EmailRequest("@{activity('CopyBlobtoBlob').error.message}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
                            DependsOn = new List<ActivityDependency>
                            {
                                new ActivityDependency
                                {
                                    Activity = copyBlobActivity,
                                    DependencyConditions = new List<String> { "Failed" }
                                }
                            }
                        }
                    }
                };
                Console.WriteLine(SafeJsonConvert.SerializeObject(resource, client.SerializationSettings));
                return resource;
            }
    ```

1. Lägg till följande rad i- `Main` metoden som skapar pipelinen:

   ```csharp
   client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));
   ```

### <a name="parameters"></a>Parametrar

Det första avsnittet av vår pipeline-kod definierar parametrar.

* `sourceBlobContainer`. Käll-BLOB-datauppsättningen använder den här parametern i pipelinen.
* `sinkBlobContainer`. Sink-BLOB-datauppsättningen använder den här parametern i pipelinen.
* `receiver`. De två webb aktiviteterna i pipelinen som skickar lyckade eller misslyckade e-postmeddelanden till mottagaren använder den här parametern.

```csharp
Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "receiver", new ParameterSpecification { Type = ParameterType.String } }
    },
```

### <a name="web-activity"></a>Webb aktivitet

Webb aktiviteten tillåter ett anrop till alla REST-slutpunkter. Mer information om aktiviteten finns [i webb aktivitet i Azure Data Factory](control-flow-web-activity.md). Den här pipelinen använder en webb aktivitet för att anropa Logic Apps e-postarbetsflöde. Du skapar två webb aktiviteter: en som anropar `CopySuccessEmail` arbets flödet och en som anropar `CopyFailWorkFlow` .

```csharp
        new WebActivity
        {
            Name = sendCopyEmailActivity,
            Method = WebActivityMethod.POST,
            Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/12345",
            Body = new EmailRequest("@{activity('CopyBlobtoBlob').output.dataWritten}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
            DependsOn = new List<ActivityDependency>
            {
                new ActivityDependency
                {
                    Activity = copyBlobActivity,
                    DependencyConditions = new List<String> { "Succeeded" }
                }
            }
        }
```

I `Url` egenskapen klistrar du in **URL-** slutpunkterna för HTTP POST från dina Logic Apps-arbetsflöden. `Body`Skicka en instans av klassen i egenskapen `EmailRequest` . E-postbegäran innehåller följande egenskaper:

* Meddelande. Skickar värdet för `@{activity('CopyBlobtoBlob').output.dataWritten` . Får åtkomst till en egenskap för den tidigare kopierings aktiviteten och skickar värdet `dataWritten` . Vid ett fel skickas felutdata i stället för `@{activity('CopyBlobtoBlob').error.message`.
* Namn på datafabrik. Med värdet för `@{pipeline().DataFactory}` den här system variabeln kan du komma åt motsvarande data fabriks namn. En lista över systemvariabler finns i [Systemvariabler](control-flow-system-variables.md).
* Pipeline-namn. Skickar värdet för `@{pipeline().Pipeline}` . Med den här system variabeln kan du komma åt motsvarande pipelines namn.
* Mottagare. Skickar värdet för `"@pipeline().parameters.receiver"` . Åtkomst till pipeline-parametrarna.

Den här koden skapar ett nytt aktivitets beroende som är beroende av föregående kopierings aktivitet.

## <a name="create-a-pipeline-run"></a>Skapa en pipelinekörning

Lägg till följande kod till `Main` metoden som utlöser en pipeline-körning.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> arguments = new Dictionary<string, object>
{
    { "sourceBlobContainer", inputBlobPath },
    { "sinkBlobContainer", outputBlobPath },
    { "receiver", emailReceiver }
};

CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, arguments).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="main-class"></a>Main-klass

Den slutliga `Main` metoden bör se ut så här.

```csharp
// Authenticate and create a data factory management client
var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };

Factory df = CreateOrUpdateDataFactory(client);

client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));

client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));

Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> arguments = new Dictionary<string, object>
{
    { "sourceBlobContainer", inputBlobPath },
    { "sinkBlobContainer", outputBlobPath },
    { "receiver", emailReceiver }
};

CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, arguments).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

Skapa och kör programmet för att utlösa en pipelinekörning!

## <a name="monitor-a-pipeline-run"></a>Övervaka en pipelinekörning

1. Lägg till följande kod i `Main`-metoden:

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, runResponse.RunId);
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

    Den här koden kontrollerar kontinuerligt status för körningen tills den har slutfört kopieringen av data.

1. Lägg till följande kod till `Main` metoden som hämtar körnings information om kopierings aktiviteten, till exempel storleken på data som läses/skrivs:

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");

    List<ActivityRun> activityRuns = client.ActivityRuns.ListByPipelineRun(
    resourceGroup, dataFactoryName, runResponse.RunId, DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)).ToList();

    if (pipelineRun.Status == "Succeeded")
    {
        Console.WriteLine(activityRuns.First().Output);
        //SaveToJson(SafeJsonConvert.SerializeObject(activityRuns.First().Output, client.SerializationSettings), "ActivityRunResult.json", folderForJsons);
    }
    else
        Console.WriteLine(activityRuns.First().Error);

    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>Kör koden

Skapa och starta programmet och kontrollera sedan pipelinekörningen.

Programmet visar förloppet för att skapa Data Factory, länkad tjänst, data uppsättningar, pipeline och pipeline-körning. Sedan kontrolleras status för pipelinekörningen. Vänta tills du ser information om körningen av kopieringsaktiviteten med storlek för lästa/skrivna data. Använd sedan verktyg som Azure Storage Explorer för att kontrol lera att blobben har kopierats till *outputBlobPath* från *inputBlobPath* som du angav i variabler.

Dina utdata bör likna följande exempel:

```json
Creating data factory DFTutorialTest...
{
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "type": "AzureStorage",
  "typeProperties": {
    "connectionString": "DefaultEndpointsProtocol=https;AccountName=***;AccountKey=***"
  }
}
Creating dataset SourceStorageDataset...
{
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "type": "Expression",
      "value": "@pipeline().parameters.sourceBlobContainer"
    },
    "fileName": "input.txt"
  },
  "linkedServiceName": {
    "type": "LinkedServiceReference",
    "referenceName": "AzureStorageLinkedService"
  }
}
Creating dataset SinkStorageDataset...
{
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "type": "Expression",
      "value": "@pipeline().parameters.sinkBlobContainer"
    }
  },
  "linkedServiceName": {
    "type": "LinkedServiceReference",
    "referenceName": "AzureStorageLinkedService"
  }
}
Creating pipeline Adfv2TutorialBranchCopy...
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
            "type": "DatasetReference",
            "referenceName": "SourceStorageDataset"
          }
        ],
        "outputs": [
          {
            "type": "DatasetReference",
            "referenceName": "SinkStorageDataset"
          }
        ],
        "name": "CopyBlobtoBlob"
      },
      {
        "type": "WebActivity",
        "typeProperties": {
          "method": "POST",
          "url": "https://xxxx.eastus.logic.azure.com:443/workflows/... ",
          "body": {
            "message": "@{activity('CopyBlobtoBlob').output.dataWritten}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
          }
        },
        "name": "SendSuccessEmailActivity",
        "dependsOn": [
          {
            "activity": "CopyBlobtoBlob",
            "dependencyConditions": [
              "Succeeded"
            ]
          }
        ]
      },
      {
        "type": "WebActivity",
        "typeProperties": {
          "method": "POST",
          "url": "https://xxx.eastus.logic.azure.com:443/workflows/... ",
          "body": {
            "message": "@{activity('CopyBlobtoBlob').error.message}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
          }
        },
        "name": "SendFailEmailActivity",
        "dependsOn": [
          {
            "activity": "CopyBlobtoBlob",
            "dependencyConditions": [
              "Failed"
            ]
          }
        ]
      }
    ],
    "parameters": {
      "sourceBlobContainer": {
        "type": "String"
      },
      "sinkBlobContainer": {
        "type": "String"
      },
      "receiver": {
        "type": "String"
      }
    }
  }
}
Creating pipeline run...
Pipeline run ID: 00000000-0000-0000-0000-0000000000000
Checking pipeline run status...
Status: InProgress
Status: InProgress
Status: Succeeded
Checking copy activity run details...
{
  "dataRead": 20,
  "dataWritten": 20,
  "copyDuration": 4,
  "throughput": 0.01,
  "errors": [],
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
}
{}

Press any key to exit...
```

## <a name="next-steps"></a>Nästa steg

Du har följande uppgifter i den här självstudien:

> [!div class="checklist"]
> * Skapa en datafabrik
> * Skapa en länkad Azure Storage-tjänst
> * Skapa en Azure Blob-datauppsättning
> * Skapa en pipeline som innehåller en kopieringsaktivitet och en webbaktivitet
> * Skicka utdata för aktiviteter till efterföljande aktiviteter
> * Använda parameter Passing och systemvariabler
> * Starta en pipelinekörning
> * Övervaka pipelinen och aktivitetskörningar

Nu kan du fortsätta till avsnittet begrepp för mer information om Azure Data Factory.
> [!div class="nextstepaction"]
>[Pipelines och aktiviteter](concepts-pipelines-activities.md)