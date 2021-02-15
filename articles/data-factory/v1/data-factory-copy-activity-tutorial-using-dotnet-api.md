---
title: 'Självstudiekurs: Skapa en pipeline med en kopieringsaktivitet med hjälp av .NET-API:et '
description: I den här självstudiekursen ska du skapa en Azure Data Factory-pipeline med en kopieringsaktivitet med hjälp av .NET-API:et.
author: linda33wj
ms.service: data-factory
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 8c2baf4da4e7893b650e1f885fb66be835c4b736
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100377082"
---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-net-api"></a>Självstudiekurs: Skapa en pipeline med en kopieringsaktivitet med hjälp av .NET-API:et
> [!div class="op_single_selector"]
> * [Översikt och förutsättningar](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Guiden Kopiera](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager-mall](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST-API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET-API](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Läs [copy activity tutorial in version 2 documentation](../quickstart-create-data-factory-dot-net.md) (kopiera aktivitetssjälvstudien i dokumentationen för version 2) om du använder den aktuella versionen av Data Factory-tjänsten. 

I den här artikeln får du lära dig hur du använder [.NET API](https://portal.azure.com) för att skapa en data fabrik med en pipeline som kopierar data från en Azure Blob storage till Azure SQL Database. Om du inte har använt Azure Data Factory, bör du läsa igenom artikeln [Introduktion till Azure Data Factory](data-factory-introduction.md) innan du genomför den här självstudien.   

I den här självstudien får du skapa en pipeline i en aktivitet: kopieringsaktivitet. Kopieringsaktiviteten kopierar data från källans datalager till mottagarens datalager. En lista över datakällor som stöds som källor och mottagare finns i [datalager som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Aktiviteten drivs av en globalt tillgänglig tjänst som kan kopiera data mellan olika datalager på ett säkert, tillförlitligt och skalbart sätt. Mer information om kopierings aktiviteten finns i [aktiviteter för data förflyttning](data-factory-data-movement-activities.md).

En pipeline kan ha fler än en aktivitet. Du kan länka två aktiviteter (köra en aktivitet efter en annan) genom att ställa in datauppsättningen för utdata för en aktivitet som den inkommande datauppsättningen för den andra aktiviteten. Mer information finns i [flera aktiviteter i en pipeline](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline). 

> [!NOTE] 
> Fullständig dokumentation för .NET-API för Data Factory finns [Data Factory .NET API-referens](/dotnet/api/overview/azure/data-factory).
> 
> Datapipelinen i den här självstudien kopierar data från ett källdatalager till ett måldatalager. Om du vill se en självstudie som visar hur du omvandlar data med Azure Data Factory går du till [Tutorial: Build a pipeline to transform data using Hadoop cluster](data-factory-build-your-first-pipeline.md) (Självstudie: Bygg en pipeline för att omvandla data med Hadoop-kluster).

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* Gå igenom [Översikt och förutsättningar](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för att få en översikt av självstudierna och slutför de **nödvändiga** stegen.
* Visual Studio 2012, 2013 eller 2015
* Ladda ned och installera [Azure .NET SDK](https://azure.microsoft.com/downloads/)
* Azure PowerShell. Följ instruktionerna i artikeln [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/install-Az-ps) för att installera Azure PowerShell på datorn. Du kan använda Azure PowerShell för att skapa ett Azure Active Directory-program.

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
    $azureAdApplication = New-AzADApplication -DisplayName "ADFCopyTutotiralApp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.adfcopytutorialapp.org/example" -Password "Pass@word1"
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
4. Lägg till följande **appSetttings**-avsnitt i filen **App.config**. De här inställningarna används av helper-metoden: **GetAuthorizationHeader**.

    Ersätt värden för **&lt; program- &gt; ID**, **&lt; lösen ord &gt;**, **&lt; prenumerations-ID &gt;** och **&lt; klient-ID &gt;** med dina egna värden.

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

5. Lägg till följande **genom att använda** instruktioner till källfilen (Program.cs) i projektet.

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
    string resourceGroupName = "ADFTutorialResourceGroup";
    string dataFactoryName = "APITutorialFactory";

    TokenCloudCredentials aadTokenCredentials = new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader().Result);

    Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);
    ```

   > [!IMPORTANT]
   > Ersätt värdet för **resourceGroupName** med namnet på Azure-resursgruppen.
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

    En datafabrik kan ha en eller flera pipelines. En pipeline kan innehålla en eller flera aktiviteter. Det kan exempelvis vara en kopieringsaktivitet som kopierar data från en källa till ett måldataarkiv och en HDInsight Hive-aktivitet som kör Hive-skript för att transformera indata till produktutdata. Låt oss börja med att skapa datafabriken i det här steget.
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

    Du kan skapa länkade tjänster i en datafabrik för att länka ditt datalager och beräkna datafabrik-tjänster. I den här självstudiekursen kommer använder du inte någon beräkningstjänst, till exempel Azure HDInsight eller Azure Data Lake Analytics. Du använder två datalager av typen Azure Storage (källa) och Azure SQL Database (mål). 

    Därför kan du skapa två länkade tjänster som heter AzureStorageLinkedService och AzureSqlLinkedService av typerna: AzureStorage och AzureSqlDatabase.  

    AzureStorageLinkedService länkar ditt Azure Storage-konto till datafabriken. Använd det lagringskonto i vilket du skapade en container och laddade upp data under [förberedelsestegen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
9. Lägg till följande kod som skapar en **länkad Azure SQL-tjänst** till **Main**-metoden.

   > [!IMPORTANT]
   > Ersätt **servername**, **databasename**, **username** och **Password** med namnen på din server, databas, användare och lösen ord.

    ```csharp
    // create a linked service for output data store: Azure SQL Database
    Console.WriteLine("Creating Azure SQL Database linked service");
    client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new LinkedServiceCreateOrUpdateParameters()
        {
            LinkedService = new LinkedService()
            {
                Name = "AzureSqlLinkedService",
                Properties = new LinkedServiceProperties
                (
                    new AzureSqlDatabaseLinkedService("Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30")
                )
            }
        }
    );
    ```

    AzureSqlLinkedService länkar Azure SQL Database till data fabriken. Data som kopieras från blob-lagringen sparas i den här databasen. Du har skapat den tomma tabellen i den här databasen som en del av [förhandskraven](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
10. Lägg till följande kod som skapar **in- och utdatauppsättningar** till **Main**-metoden.

    ```csharp
    // create input and output datasets
    Console.WriteLine("Creating input and output datasets");
    string Dataset_Source = "InputDataset";
    string Dataset_Destination = "OutputDataset";

    Console.WriteLine("Creating input dataset of type: Azure Blob");
    client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,

    new DatasetCreateOrUpdateParameters()
    {
        Dataset = new Dataset()
        {
            Name = Dataset_Source,
            Properties = new DatasetProperties()
            {
                Structure = new List<DataElement>()
                {
                    new DataElement() { Name = "FirstName", Type = "String" },
                    new DataElement() { Name = "LastName", Type = "String" }
                },
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

    Console.WriteLine("Creating output dataset of type: Azure SQL");
    client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new DatasetCreateOrUpdateParameters()
        {
            Dataset = new Dataset()
            {
                Name = Dataset_Destination,
                Properties = new DatasetProperties()
                {
                    Structure = new List<DataElement>()
                    {
                        new DataElement() { Name = "FirstName", Type = "String" },
                        new DataElement() { Name = "LastName", Type = "String" }
                    },
                    LinkedServiceName = "AzureSqlLinkedService",
                    TypeProperties = new AzureSqlTableDataset()
                    {
                        TableName = "emp"
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
    
    I föregående steg skapade du länkade tjänster för att länka ditt Azure Storage-konto och Azure SQL Database till din data fabrik. I det här steget definierar du två datauppsättningar – InputDataset och OutputDataset – som visar in- och utdata som lagras i de datalager som refereras till av AzureStorageLinkedService och AzureSqlLinkedService.

    Den länkade Azure storage-tjänsten anger anslutningssträngen som Data Factory-tjänsten använder vid körning för att ansluta till ditt Azure storage-konto. Och en indatauppsättning anger vilken blobcontainer och mapp som innehåller indata.  

    På samma sätt anger den länkade tjänsten Azure SQL Database den anslutnings sträng som Data Factory tjänsten använder vid körning för att ansluta till databasen. Och utdatauppsättningen (OutputDataset) för SQL-tabellen anger tabellen i databasen som data kopieras till från blob-lagringen.

    I det här steget skapar du en datauppsättning med namnet InputDataset som pekar på en blobfil (emp.ext) i rotmappen i en blobcontainer (adftutorial) i Azure Storage som representeras av den länkade tjänsten AzureStorageLinkedService. Om du inte anger ett värde för filnamnet (eller hoppar över det), kommer data från alla blobbar i indatamappen att kopieras till målet. I den här kursen anger du ett värde för filnamnet.    

    I det här steget ska du skapa en utdatauppsättning med namnet **OutputDataset**. Den här data uppsättningen pekar på en SQL-tabell i databasen som representeras av **AzureSqlLinkedService**.
11. Lägg till följande kod som **skapar och aktiverar en pipeline** till **Main**-metoden. I det här steget ska du skapa en pipeline med en **kopieringsaktivitet** som använder **InputDataset** som indata och **OutputDataset** som utdata.

    ```csharp
    // create a pipeline
    Console.WriteLine("Creating a pipeline");
    DateTime PipelineActivePeriodStartTime = new DateTime(2017, 5, 11, 0, 0, 0, 0, DateTimeKind.Utc);
    DateTime PipelineActivePeriodEndTime = new DateTime(2017, 5, 12, 0, 0, 0, 0, DateTimeKind.Utc);
    string PipelineName = "ADFTutorialPipeline";

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
                            Name = "BlobToAzureSql",
                            Inputs = new List<ActivityInput>()
                            {
                                new ActivityInput() {
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
                    }
                }
            }
        });
    ```

    Observera följande punkter:
   
    - I avsnittet Aktiviteter finns det bara en aktivitet vars **typ** anges till **Kopia**. Se artikeln [Dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) för information om kopieringsaktiviteten. I Data Factory-lösningar, kan du också använda [datatransformeringsaktiviteter](data-factory-data-transformation-activities.md).
    - Indata för aktiviteten är inställd på **InputDataset** och utdata för aktiviteten är inställd på **OutputDataset**. 
    - I avsnittet för **typeProperties** har **BlobSource** angetts som källtyp och **SqlSink** har angetts som mottagartyp. En fullständig lista över datakällor som stöds av kopieringsaktiviteten som källor och mottagare finns i [Datalager som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Klicka på länken i tabellen om du vill veta hur du använder ett visst datalager som stöds som källa/mottagare.  
   
    Schemat styrs för närvarande av utdatamängd. I den här självstudiekursen är datamängden för utdata konfigurerad för att skapa ett segment en gång i timmen. Pipelinen har en starttid och sluttid som är en dag från varandra, vilket är 24 timmar. Därför produceras 24 segment för utdatauppsättningen av pipeline.
12. Lägg till följande kod till **Main**-metoden för att hämta statusen för en datasektor i utdatauppsättningen. Endast en sektor förväntas i det här exemplet.

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

13. Lägg till följande kod för att hämta körningsinformation för en datasektor till **Main**-metoden.

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
            }
        );

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

14. Lägg till följande helper-metod som används av **Main**-metoden i **Program**-klassen.

    > [!NOTE] 
    > När du kopierar och klistrar in följande kod, se till att den kopierade koden är på samma nivå som huvudmetoden.

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

15. Expandera projektet i Solution Explorer (DataFactoryAPITestApp), högerklicka på **Referenser** och klicka på **Lägg till referens**. Markera kryssrutan för **System.Configuration**-sammansättningen. och klicka på **OK**.
16. Skapa konsolprogrammet. Klicka på **Skapa** på menyn och klicka sedan på **Build Solution** (Skapa lösning).
17. Kontrollera att det finns minst en fil i **adftutorial**-containern i Azure-bloblagringen. Om inte skapar du **Emp.txt**-filen i Anteckningar med följande innehåll och laddar upp den till adftutorial-containern.

    ```
    John, Doe
    Jane, Doe
    ```
18. Kör exemplet genom att klicka på **Felsök**  ->  **Starta fel sökning** på menyn. När du ser **Getting run details of a data slice** (Hämta körningsdata för en datorsektor) väntar du några minuter och trycker sedan på **Retur**.
19. Använd Azure-portalen och kontrollera att datafabriken **APITutorialFactory** har skapats med följande artefakter:
    * Länkad tjänst: **LinkedService_AzureStorage**
    * DataSet: **InputDataset** och **OutputDataset**.
    * Pipeline: **PipelineBlobSample**
20. Kontrol lera att de två medarbetar posterna skapas i tabellen **EMP** i den angivna databasen.

## <a name="next-steps"></a>Nästa steg
Fullständig dokumentation för .NET-API för Data Factory finns [Data Factory .NET API-referens](/dotnet/api/overview/azure/data-factory).

I den här självstudien använde du Azure Blob Storage som käll data lager och Azure SQL Database som mål data lager i en kopierings åtgärd. Följande tabell innehåller en lista över datalager som stöds som källor och mål av kopieringsaktiviteten: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

För mer information om hur du kopierar data till/från ett datalager klickar du på länken för datalagret i tabellen.

