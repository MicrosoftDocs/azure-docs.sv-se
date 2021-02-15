---
title: Skapa datapipelines med hjälp av Azure .NET SDK
description: Lär dig att program mässigt skapa, övervaka och hantera Azure-datafabriker med hjälp av Data Factory SDK.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 1b7f5d1ad671b6d97bd6597386c714b026c02232
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100376861"
---
# <a name="create-monitor-and-manage-azure-data-factories-using-azure-data-factory-net-sdk"></a>Skapa, övervaka och hantera Azure-datafabriker med Azure Data Factory .NET SDK
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Läs [copy activity tutorial in version 2 documentation](../quickstart-create-data-factory-dot-net.md) (kopiera aktivitetssjälvstudien i dokumentationen för version 2) om du använder den aktuella versionen av Data Factory-tjänsten. 

## <a name="overview"></a>Översikt
Du kan skapa, övervaka och hantera Azure-datafabriker via programmering med Data Factory .NET SDK. Den här artikeln innehåller en genom gång som du kan följa för att skapa ett exempel på ett .NET-konsol program som skapar och övervakar en data fabrik. 

> [!NOTE]
> Den här artikeln beskriver inte hela .NET-API:et för Data Factory. Läs [Data Factory .NET API-referens](/dotnet/api/overview/azure/data-factory) för omfattande dokumentation om .net api för Data Factory. 

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* Visual Studio 2012, 2013 eller 2015
* Hämta och installera [Azure .NET SDK](https://azure.microsoft.com/downloads/).
* Azure PowerShell. Följ instruktionerna i artikeln [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/) för att installera Azure PowerShell på datorn. Du kan använda Azure PowerShell för att skapa ett Azure Active Directory-program.

### <a name="create-an-application-in-azure-active-directory"></a>Skapa ett program i Azure Active Directory
Skapa ett Azure Active Directory-program, skapa ett tjänstobjektnamn för programmet och tilldela det rollen som **Data Factory-deltagare**.

1. Starta **PowerShell**.
2. Kör följande kommando och ange användarnamnet och lösenordet som du använder för att logga in på Azure-portalen.

    ```powershell
    Connect-AzAccount
    ```
3. Kör följande kommando för att visa alla prenumerationer för det här kontot.

    ```powershell
    Get-AzSubscription
    ```
4. Kör följande kommando för att välja den prenumeration som du vill arbeta med. Ersätt **&lt; NameOfAzureSubscription** &gt; med namnet på din Azure-prenumeration.

    ```powershell
    Get-AzSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzContext
    ```

   > [!IMPORTANT]
   > Anteckna **ID** och **TenantId** i kommandots utdata.

5. Skapa en Azure-resurs grupp med namnet **ADFTutorialResourceGroup** genom att köra följande kommando i PowerShell.

    ```powershell
    New-AzResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```

    Om resursgruppen redan finns anger du om du vill uppdatera den (Y) eller lämna den som den är (N).

    Om du använder en annan resursgrupp måste du använda namnet på din resursgrupp i stället för ADFTutorialResourceGroup i den här självstudiekursen.
6. Skapa ett Azure Active Directory-program.

    ```powershell
    $azureAdApplication = New-AzADApplication -DisplayName "ADFDotNetWalkthroughApp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.adfdotnetwalkthroughapp.org/example" -Password "Pass@word1"
    ```

    Om följande fel returneras anger du en annan URL och kör kommandot igen.
    
    ```powershell
    Another object with the same value for property identifierUris already exists.
    ```
7. Skapa AD-tjänstobjektet.

    ```powershell
    New-AzADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId
    ```
8. Lägg till tjänstobjektet till rollen som **Data Factory-deltagare**.

    ```powershell
    New-AzRoleAssignment -RoleDefinitionName "Data Factory Contributor" -ServicePrincipalName $azureAdApplication.ApplicationId.Guid
    ```
9. Hämta program-ID:t.

    ```powershell
    $azureAdApplication    
    ```
    Anteckna program-ID:t (applicationID) från utdata.

Du bör nu ha tillgång till följande fyra värden efter de här stegen:

* Klient-ID:t
* Prenumerations-ID:t
* Program-ID
* Lösenord (anges i det första kommandot)

## <a name="walkthrough"></a>Genomgång
I genom gången skapar du en data fabrik med en pipeline som innehåller en kopierings aktivitet. Kopierings aktiviteten kopierar data från en mapp i Azure Blob Storage till en annan mapp i samma blob-lagring. 

Kopieringsaktiviteten utför dataflyttningen i Azure Data Factory. Aktiviteten drivs av en globalt tillgänglig tjänst som kan kopiera data mellan olika datalager på ett säkert, tillförlitligt och skalbart sätt. Se artikeln [Dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) för information om kopieringsaktiviteten.

1. Skapa ett C# .NET-konsolprogram med hjälp av Visual Studio 2012/2013/2015.
   1. Starta **Visual Studio** 2012/2013/2015.
   2. Klicka på **Arkiv**, peka på **Nytt** och klicka på **Projekt**.
   3. Expandera **Mallar** och välj **Visual C#**. I den här genomgången ska du använda C#, men du kan använda valfritt .NET-språk.
   4. Välj **Konsolprogram** i listan över projekttyper till höger.
   5. Ange **DataFactoryAPITestApp** som namn.
   6. Välj **C:\ADFGetStarted** som plats.
   7. Klicka på **OK** för att skapa projektet.
2. Klicka på **verktyg**, peka på **NuGet Package Manager** och klicka på **Package Manager-konsolen**.
3. I **Package Manager Console** gör du följande steg:
   1. Kör följande kommando för att installera Data Factory-paketet: `Install-Package Microsoft.Azure.Management.DataFactories`
   2. Kör följande kommando för att installera Azure Active Directory-paketet (du använder Active Directory-API i koden): `Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213`
4. Ersätt innehållet i **App.config** -filen i projektet med följande innehåll: 
    
    ```xml
    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
        <appSettings>
            <add key="ActiveDirectoryEndpoint" value="https://login.microsoftonline.com/" />
            <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
            <add key="WindowsManagementUri" value="https://management.core.windows.net/" />

            <add key="ApplicationId" value="your application ID" />
            <add key="Password" value="Password you used while creating the AAD application" />
            <add key="SubscriptionId" value= "Subscription ID" />
            <add key="ActiveDirectoryTenantId" value="Tenant ID" />
        </appSettings>
    </configuration>
    ```
5. I App.Config-filen uppdaterar du värden för **&lt; program- &gt; ID**, **&lt; lösen ord &gt;**, **&lt; prenumerations-ID &gt;** och **&lt; klient-ID &gt;** med dina egna värden.
6. Lägg till följande **using** -uttryck i **program.cs** -filen i projektet.

    ```csharp
    using System.Configuration;
    using System.Collections.ObjectModel;
    using System.Threading;
    using System.Threading.Tasks;

    using Microsoft.Azure;
    using Microsoft.Azure.Management.DataFactories;
    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Common.Models;

    using Microsoft.IdentityModel.Clients.ActiveDirectory;

    ```
6. Lägg till följande kod som skapar en instans av **DataPipelineManagementClient**-klassen till **Main**-metoden. Du kan använda det här objektet för att skapa en datafabrik, en länkad tjänst, in- och utdatauppsättningar och en pipeline. Du kan också använda det här objektet om du vill övervaka sektorer för en datauppsättning vid körningstillfället.

    ```csharp
    // create data factory management client

    //IMPORTANT: specify the name of Azure resource group here
    string resourceGroupName = "ADFTutorialResourceGroup";

    //IMPORTANT: the name of the data factory must be globally unique.
    // Therefore, update this value. For example:APITutorialFactory05122017
    string dataFactoryName = "APITutorialFactory";

    TokenCloudCredentials aadTokenCredentials = new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader().Result);

    Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);
    ```

   > [!IMPORTANT]
   > Ersätt värdet för **resourceGroupName** med namnet på Azure-resursgruppen. Du kan skapa en resurs grupp med hjälp av cmdleten [New-AzureResourceGroup](/powershell/module/az.resources/new-azresourcegroup) .
   >
   > Uppdatera namnet på datafabriken (dataFactoryName) så att det är unikt. Namnet på datafabriken måste vara unikt globalt. Se artikeln [Data Factory – namnregler](data-factory-naming-rules.md) för namnregler för Data Factory-artefakter.
7. Lägg till följande kod som skapar en **datafabrik** till **Main**-metoden.

    ```csharp
    // create a data factory
    Console.WriteLine("Creating a data factory");
    client.DataFactories.CreateOrUpdate(resourceGroupName,
        new DataFactoryCreateOrUpdateParameters()
        {
            DataFactory = new DataFactory()
            {
                Name = dataFactoryName,
                Location = "westus",
                Properties = new DataFactoryProperties()
            }
        }
    );
    ```
8. Lägg till följande kod som skapar en **länkad Azure Storage-tjänst** till **Main**-metoden.

   > [!IMPORTANT]
   > Ersätt **storageaccountname** och **accountkey** med namnet och nyckeln för ditt Azure Storage-konto.

    ```csharp
    // create a linked service for input data store: Azure Storage
    Console.WriteLine("Creating Azure Storage linked service");
    client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new LinkedServiceCreateOrUpdateParameters()
        {
            LinkedService = new LinkedService()
            {
                Name = "AzureStorageLinkedService",
                Properties = new LinkedServiceProperties
                (
                    new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<accountkey>")
                )
            }
        }
    );
    ```
9. Lägg till följande kod som skapar **in- och utdatauppsättningar** till **Main**-metoden.

    **FolderPath** för indata-bloben anges till **adftutorial/** där **adftutorial** är namnet på behållaren i blob-lagringen. Om den här behållaren inte finns i Azure Blob Storage skapar du en behållare med det här namnet: **adftutorial** och laddar upp en textfil till behållaren.

    FolderPath för utgående BLOB är inställt på: **adftutorial/apifactoryoutput/{slice}** där **sektorn** beräknas dynamiskt baserat på värdet för **SliceStart** (start datum och-tid för varje sektor)

    ```csharp
    // create input and output datasets
    Console.WriteLine("Creating input and output datasets");
    string Dataset_Source = "DatasetBlobSource";
    string Dataset_Destination = "DatasetBlobDestination";
    
    client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
    new DatasetCreateOrUpdateParameters()
    {
        Dataset = new Dataset()
        {
            Name = Dataset_Source,
            Properties = new DatasetProperties()
            {
                LinkedServiceName = "AzureStorageLinkedService",
                TypeProperties = new AzureBlobDataset()
                {
                    FolderPath = "adftutorial/",
                    FileName = "emp.txt"
                },
                External = true,
                Availability = new Availability()
                {
                    Frequency = SchedulePeriod.Hour,
                    Interval = 1,
                },
    
                Policy = new Policy()
                {
                    Validation = new ValidationPolicy()
                    {
                        MinimumRows = 1
                    }
                }
            }
        }
    });
    
    client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
    new DatasetCreateOrUpdateParameters()
    {
        Dataset = new Dataset()
        {
            Name = Dataset_Destination,
            Properties = new DatasetProperties()
            {
    
                LinkedServiceName = "AzureStorageLinkedService",
                TypeProperties = new AzureBlobDataset()
                {
                    FolderPath = "adftutorial/apifactoryoutput/{Slice}",
                    PartitionedBy = new Collection<Partition>()
                    {
                        new Partition()
                        {
                            Name = "Slice",
                            Value = new DateTimePartitionValue()
                            {
                                Date = "SliceStart",
                                Format = "yyyyMMdd-HH"
                            }
                        }
                    }
                },
    
                Availability = new Availability()
                {
                    Frequency = SchedulePeriod.Hour,
                    Interval = 1,
                },
            }
        }
    });
    ```
10. Lägg till följande kod som **skapar och aktiverar en pipeline** till **Main**-metoden. Den här pipelinen har en **CopyActivity** som använder **BlobSource** som källa och **BlobSink** som mottagare.

    Kopieringsaktiviteten utför dataflyttningen i Azure Data Factory. Aktiviteten drivs av en globalt tillgänglig tjänst som kan kopiera data mellan olika datalager på ett säkert, tillförlitligt och skalbart sätt. Se artikeln [Dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) för information om kopieringsaktiviteten.

    ```csharp
    // create a pipeline
    Console.WriteLine("Creating a pipeline");
    DateTime PipelineActivePeriodStartTime = new DateTime(2014, 8, 9, 0, 0, 0, 0, DateTimeKind.Utc);
    DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
    string PipelineName = "PipelineBlobSample";
    
    client.Pipelines.CreateOrUpdate(resourceGroupName, dataFactoryName,
    new PipelineCreateOrUpdateParameters()
    {
        Pipeline = new Pipeline()
        {
            Name = PipelineName,
            Properties = new PipelineProperties()
            {
                Description = "Demo Pipeline for data transfer between blobs",
    
                // Initial value for pipeline's active period. With this, you won't need to set slice status
                Start = PipelineActivePeriodStartTime,
                End = PipelineActivePeriodEndTime,
    
                Activities = new List<Activity>()
                {
                    new Activity()
                    {
                        Name = "BlobToBlob",
                        Inputs = new List<ActivityInput>()
                        {
                            new ActivityInput()
                {
                                Name = Dataset_Source
                            }
                        },
                        Outputs = new List<ActivityOutput>()
                        {
                            new ActivityOutput()
                            {
                                Name = Dataset_Destination
                            }
                        },
                        TypeProperties = new CopyActivity()
                        {
                            Source = new BlobSource(),
                            Sink = new BlobSink()
                            {
                                WriteBatchSize = 10000,
                                WriteBatchTimeout = TimeSpan.FromMinutes(10)
                            }
                        }
                    }
    
                },
            }
        }
    });
    ```
12. Lägg till följande kod till **Main**-metoden för att hämta statusen för en datasektor i utdatauppsättningen. Det finns bara en sektor som förväntas i det här exemplet.

    ```csharp
    // Pulling status within a timeout threshold
    DateTime start = DateTime.Now;
    bool done = false;
    
    while (DateTime.Now - start < TimeSpan.FromMinutes(5) && !done)
    {
        Console.WriteLine("Pulling the slice status");
        // wait before the next status check
        Thread.Sleep(1000 * 12);
    
        var datalistResponse = client.DataSlices.List(resourceGroupName, dataFactoryName, Dataset_Destination,
            new DataSliceListParameters()
            {
                DataSliceRangeStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString(),
                DataSliceRangeEndTime = PipelineActivePeriodEndTime.ConvertToISO8601DateTimeString()
            });
    
        foreach (DataSlice slice in datalistResponse.DataSlices)
        {
            if (slice.State == DataSliceState.Failed || slice.State == DataSliceState.Ready)
            {
                Console.WriteLine("Slice execution is done with status: {0}", slice.State);
                done = true;
                break;
            }
            else
            {
                Console.WriteLine("Slice status is: {0}", slice.State);
            }
        }
    }
    ```
13. **(valfritt)** Lägg till följande kod för att hämta körnings information för en data sektor till **main** -metoden.

    ```csharp
    Console.WriteLine("Getting run details of a data slice");
    
    // give it a few minutes for the output slice to be ready
    Console.WriteLine("\nGive it a few minutes for the output slice to be ready and press any key.");
    Console.ReadKey();
    
    var datasliceRunListResponse = client.DataSliceRuns.List(
        resourceGroupName,
        dataFactoryName,
        Dataset_Destination,
        new DataSliceRunListParameters()
        {
            DataSliceStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString()
        });
    
    foreach (DataSliceRun run in datasliceRunListResponse.DataSliceRuns)
    {
        Console.WriteLine("Status: \t\t{0}", run.Status);
        Console.WriteLine("DataSliceStart: \t{0}", run.DataSliceStart);
        Console.WriteLine("DataSliceEnd: \t\t{0}", run.DataSliceEnd);
        Console.WriteLine("ActivityId: \t\t{0}", run.ActivityName);
        Console.WriteLine("ProcessingStartTime: \t{0}", run.ProcessingStartTime);
        Console.WriteLine("ProcessingEndTime: \t{0}", run.ProcessingEndTime);
        Console.WriteLine("ErrorMessage: \t{0}", run.ErrorMessage);
    }
    
    Console.WriteLine("\nPress any key to exit.");
    Console.ReadKey();
    ```
14. Lägg till följande helper-metod som används av **Main**-metoden i **Program**-klassen. Den här metoden visar en dialog ruta där du kan ange **användar namn** och **lösen ord** som du använder för att logga in på Azure Portal.

    ```csharp
    public static async Task<string> GetAuthorizationHeader()
    {
        AuthenticationContext context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
        ClientCredential credential = new ClientCredential(
            ConfigurationManager.AppSettings["ApplicationId"],
            ConfigurationManager.AppSettings["Password"]);
        AuthenticationResult result = await context.AcquireTokenAsync(
            resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
            clientCredential: credential);

        if (result != null)
            return result.AccessToken;

        throw new InvalidOperationException("Failed to acquire token");
    }
    ```

15. I Solution Explorer expanderar du projektet: **DataFactoryAPITestApp**, högerklickar på **referenser** och klickar på **Lägg till referens**. Markera kryss rutan för `System.Configuration` sammansättning och klicka på **OK**.
15. Skapa konsolprogrammet. Klicka på **Skapa** på menyn och klicka sedan på **Build Solution** (Skapa lösning).
16. Kontrollera att det finns minst en fil i adftutorial-containern i Azure-bloblagringen. Om inte skapar du Emp.txt-filen i Anteckningar med följande innehåll och laddar upp den till adftutorial-containern.

    ```
    John, Doe
    Jane, Doe
    ```
17. Kör exemplet genom att klicka på **Felsök**  ->  **Starta fel sökning** på menyn. När du ser **Getting run details of a data slice** (Hämta körningsdata för en datorsektor) väntar du några minuter och trycker sedan på **Retur**.
18. Använd Azure-portalen och kontrollera att datafabriken **APITutorialFactory** har skapats med följande artefakter:
    * Länkad tjänst: **AzureStorageLinkedService**
    * Datauppsättning: **DatasetBlobSource** och **DatasetBlobDestination**.
    * Pipeline: **PipelineBlobSample**
19. Kontrol lera att en utdatafil har skapats i mappen **apifactoryoutput** i behållaren **adftutorial** .

## <a name="get-a-list-of-failed-data-slices"></a>Hämta en lista över misslyckade data sektorer 

```csharp
// Parse the resource path
var ResourceGroupName = "ADFTutorialResourceGroup";
var DataFactoryName = "DataFactoryAPITestApp";

var parameters = new ActivityWindowsByDataFactoryListParameters(ResourceGroupName, DataFactoryName);
parameters.WindowState = "Failed";
var response = dataFactoryManagementClient.ActivityWindows.List(parameters);
do
{
    foreach (var activityWindow in response.ActivityWindowListResponseValue.ActivityWindows)
    {
        var row = string.Join(
            "\t",
            activityWindow.WindowStart.ToString(),
            activityWindow.WindowEnd.ToString(),
            activityWindow.RunStart.ToString(),
            activityWindow.RunEnd.ToString(),
            activityWindow.DataFactoryName,
            activityWindow.PipelineName,
            activityWindow.ActivityName,
            string.Join(",", activityWindow.OutputDatasets));
        Console.WriteLine(row);
    }

    if (response.NextLink != null)
    {
        response = dataFactoryManagementClient.ActivityWindows.ListNext(response.NextLink, parameters);
    }
    else
    {
        response = null;
    }
}
while (response != null);
```

## <a name="next-steps"></a>Nästa steg
Se följande exempel för att skapa en pipeline med .NET SDK som kopierar data från en Azure Blob Storage till Azure SQL Database: 

- [Skapa en pipeline för att kopiera data från Blob Storage till SQL Database](data-factory-copy-activity-tutorial-using-dotnet-api.md)
