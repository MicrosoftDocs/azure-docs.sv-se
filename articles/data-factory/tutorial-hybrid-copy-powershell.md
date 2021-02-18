---
title: Kopiera data från SQL Server till Blob Storage med hjälp av PowerShell
description: Lär dig hur du kopierar data från ett lokalt datalager till Azure-molnet med hjälp av en lokal Integration Runtime i Azure Data Factory.
author: nabhishek
ms.author: abnarain
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019, devx-track-azurepowershell
ms.date: 01/22/2018
ms.openlocfilehash: 6901df335dc20a032470a9cd6bda4108fe0cde36
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "101092239"
---
# <a name="tutorial-copy-data-from-a-sql-server-database-to-azure-blob-storage"></a>Självstudie: kopiera data från en SQL Server-databas till Azure Blob Storage

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

I den här självstudien använder du Azure PowerShell för att skapa en Data Factory-pipeline som kopierar data från en SQL Server-databas till Azure Blob Storage. Du skapar och använder en lokal installation av Integration Runtime som flyttar data mellan lokala datalager och datalager i molnet.

> [!NOTE]
> Den här artikeln ger inte någon detaljerad introduktion till Data Factory-tjänsten. Mer information finns i [Introduktion till Azure Data Factory](introduction.md).

I den här självstudien får du göra följande:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Skapa Integration Runtime med egen värd.
> * Skapa länkade tjänster för SQL Server och Azure Storage.
> * Skapa datauppsättningar för SQL Server och Azure Blob.
> * Skapa en pipeline med en kopieringsaktivitet för att flytta data.
> * Starta en pipelinekörning.
> * Övervaka pipelinekörningen.

## <a name="prerequisites"></a>Förutsättningar
### <a name="azure-subscription"></a>Azure-prenumeration
Om du inte redan har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

### <a name="azure-roles"></a>Azure-roller
Om du vill skapa Data Factory-instanser måste det användar konto som du använder för att logga in på Azure vara tilldelat en *deltagar* -eller *ägar* roll eller måste vara *administratör* för Azure-prenumerationen.

Om du vill visa vilka behörigheter du har i prenumerationen öppnar du Azure-portalen, väljer användarnamnet i det övre högra hörnet och sedan väljer du **Behörigheter**. Om du har åtkomst till flera prenumerationer väljer du rätt prenumeration. Exempel instruktioner om hur du lägger till en användare till en roll finns i avsnittet [tilldela Azure-roller med hjälp av Azure Portal](../role-based-access-control/role-assignments-portal.md) artikeln.

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 och 2017
I den här självstudien använder du en SQL Server-databas som *käll* data lager. Pipelinen i data fabriken som du skapar i den här självstudien kopierar data från den här SQL Server databasen (källa) till Azure Blob Storage (mottagare). Sedan skapar du en tabell med namnet **EMP** i din SQL Server-databas och infogar ett par exempel poster i tabellen.

1. Starta SQL Server Management Studio. Om det inte redan är installerat på datorn öppnar du [Ladda ner SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).

1. Anslut till SQL Server-instansen med hjälp av dina autentiseringsuppgifter.

1. Skapa en exempeldatabas. I trädvyn högerklickar du på **Databaser** och sedan väljer du **Ny databas**.

1. I fönstret **Ny databas** anger du ett namn för databasen och sedan väljer du **OK**.

1. Skapa tabellen **emp** och infoga lite exempeldata i den genom att köra följande frågeskript mot databasen. I trädvyn högerklickar du på databasen du skapade och sedan väljer du **Ny fråga**.

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    INSERT INTO emp (FirstName, LastName) VALUES ('John', 'Doe')
    INSERT INTO emp (FirstName, LastName) VALUES ('Jane', 'Doe')
    GO
    ```


### <a name="azure-storage-account"></a>Azure-lagringskonto
I den här självstudien använder du ett allmänt Azure Storage-konto (Azure Blob Storage för att vara exakt) som datalager för destination/mottagare. Om du inte har något allmänt Azure Storage-konto kan du läsa [Skapa ett lagringskonto](../storage/common/storage-account-create.md). Pipelinen i data fabriken som du skapar i den här självstudien kopierar data från den SQL Server databasen (källa) till Azure Blob Storage (mottagare). 

#### <a name="get-storage-account-name-and-account-key"></a>Hämta lagringskontots namn och åtkomstnyckel
Du använder namnet och nyckeln för Azure Storage-kontot i den här självstudien. Hämta namnet och nyckeln till lagringskontot genom att göra följande:

1. Logga in på [Azure Portal](https://portal.azure.com) med användarnamnet och lösenordet för Azure.

1. I det vänstra fönstret väljer du **Fler tjänster**, filtrerar genom att använda nyckelordet **Lagring** och sedan väljer du **Lagringskonton**.

    ![Sök efter lagringskontot](media/doc-common-process/search-storage-account.png)

1. Filtrera på ditt lagringskonto (om det behövs) i listan med lagringskonton och välj sedan ditt lagringskonto.

1. I fönstret **Lagringskonto** väljer du **Åtkomstnycklar**.

1. I rutorna **Lagringskontonamn** och **key1** kopierar du värdena och klistrar sedan in dem i Anteckningar eller annat redigeringsprogram så att du har dem när du behöver dem senare i självstudien.

#### <a name="create-the-adftutorial-container"></a>Skapa containern adftutorial
I det här avsnittet skapar du en blobcontainer med namnet **adftutorial** i Azure Blob Storage.

1. I fönstret **Lagringskonto** växlar du till **Översikt** och klickar sedan på **Blobar**.

    ![Alternativet Välj blobar](media/tutorial-hybrid-copy-powershell/select-blobs.png)

1. I fönstret **Blobtjänst** väljer du **Container**.

1. I fönstret **Ny container**, i rutan **Namn**, anger du **adftutorial** och väljer **OK**.

    ![Ange namn på container](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)

1. Välj **adftutorial** i listan över containrar.  

1. Låt **containerfönstret** för **adftutorial** vara öppet. Du kommer att använda den för att bekräfta utdata i slutet av självstudien. Data Factory skapar automatiskt utdatamappen i den här containern, så du behöver inte skapa en.


### <a name="windows-powershell"></a>Windows PowerShell

#### <a name="install-azure-powershell"></a>Installera Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Installera den senaste versionen av Azure PowerShell om du inte redan har den på din dator. Mer detaljerade anvisningar finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/install-Az-ps).

#### <a name="log-in-to-powershell"></a>Logga in på PowerShell

1. Starta PowerShell på datorn och låt det vara öppet tills den här självstudien har slutförts. Om du stänger och öppnar det igen behöver du köra dessa kommandon en gång till.

1. Kör följande kommando och ange sedan användarnamnet och lösenordet för Azure som du använder för att logga in på Azure-portalen:

    ```powershell
    Connect-AzAccount
    ```        

1. Om du har flera Azure-prenumerationer kör du följande kommando för att välja den prenumeration du vill arbeta med. Ersätt **SubscriptionId** med ID:t för din Azure-prenumeration:

    ```powershell
    Select-AzSubscription -SubscriptionId "<SubscriptionId>"    
    ```

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. Definiera en variabel för resursgruppens namn som du kan använda senare i PowerShell-kommandon. Kopiera följande kommando till PowerShell, ange ett namn för [Azure-resursgruppen](../azure-resource-manager/management/overview.md) (sätt dubbla citattecken omkring namnet; till exempel `"adfrg"`) och kör sedan kommandot. 
   
    ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup"
    ```

1. Kör följande kommando för att skapa en Azure-resursgrupp:

    ```powershell
    New-AzResourceGroup $resourceGroupName -location 'East US'
    ```

    Om resursgruppen redan finns behöver du kanske inte skriva över den. Ge variabeln `$resourceGroupName` ett annat värde och kör kommandot igen.

1. Definiera en variabel för datafabrikens namn som du kan använda senare i PowerShell-kommandon. Namnet måste börja med en bokstav eller en siffra och får bara innehålla bokstäver, siffror och bindestreck (-).

    > [!IMPORTANT]
    >  Uppdatera datafabrikens namn med ett unikt globalt namn. Ett exempel är ADFTutorialFactorySP1127.

    ```powershell
    $dataFactoryName = "ADFTutorialFactory"
    ```

1. Definiera en variabel för datafabrikens plats:

    ```powershell
    $location = "East US"
    ```  

1. Skapa datafabriken genom att köra följande `Set-AzDataFactoryV2`-cmdlet:

    ```powershell       
    Set-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Location $location -Name $dataFactoryName
    ```

> [!NOTE]
>
> * Namnet på datafabriken måste vara globalt unikt. Om du får följande felmeddelande ändrar du namnet och försöker igen.
>    ```
>    The specified data factory name 'ADFv2TutorialDataFactory' is already in use. Data factory names must be globally unique.
>    ```
> * Om du vill skapa Data Factory-instanser måste det användarkonto du använder för att logga in på Azure vara tilldelad en *deltagare* eller *ägare*, eller vara en *administratör* för Azure-prenumerationen.
> * Om du vill se en lista med Azure-regioner där Data Factory är tillgängligt för närvarande markerar du de regioner du är intresserad av på följande sida. Expandera sedan **Analytics** och leta rätt på **Data Factory**: [Tillgängliga produkter per region](https://azure.microsoft.com/global-infrastructure/services/). Datalagren (Azure Storage, Azure SQL Database osv.) och beräkningarna (Azure HDInsight osv.) som används i Data Factory kan finnas i andra regioner.
>
>

## <a name="create-a-self-hosted-integration-runtime"></a>Skapa en lokal Integration Runtime

I det här avsnittet kan du skapa en lokal Integration Runtime och koppla den till en lokal dator med SQL Server-databasen. En lokal Integration Runtime är den komponent som kopierar data från SQL Server-databasen på din dator till Azure Blob Storage.

1. Skapa en variabel för namnet på din Integration Runtime. Använd ett unikt namn och notera namnet. Du använder det senare i den här självstudien.

    ```powershell
   $integrationRuntimeName = "ADFTutorialIR"
    ```

1. Skapa Integration Runtime med egen värd.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $integrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```

    Här är exempel på utdata:

    ```console
    Name              : ADFTutorialIR
    Type              : SelfHosted
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Description       : selfhosted IR description
    Id                : /subscriptions/<subscription ID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/integrationruntimes/<integrationRuntimeName>
    ```

1. Kör följande kommando för att hämta statusen för din skapade Integration Runtime:

    ```powershell
   Get-AzDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
    ```

    Här är exempel på utdata:

    ```console
    State                     : NeedRegistration
    Version                   :
    CreateTime                : 9/10/2019 3:24:09 AM
    AutoUpdate                : On
    ScheduledUpdateDate       :
    UpdateDelayOffset         :
    LocalTimeZoneOffset       :
    InternalChannelEncryption :
    Capabilities              : {}
    ServiceUrls               : {eu.frontend.clouddatahub.net}
    Nodes                     : {}
    Links                     : {}
    Name                      : <Integration Runtime name>
    Type                      : SelfHosted
    ResourceGroupName         : <resourceGroup name>
    DataFactoryName           : <dataFactory name>
    Description               : selfhosted IR description
    Id                        : /subscriptions/<subscription ID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/integrationruntimes/<integrationRuntimeName>
    ```

1. Kör följande kommando för att hämta *autentiseringsnycklarna* för att registrera en lokal Integration Runtime med Data Factory-tjänsten i molnet. Kopiera en av nycklarna (uteslut de dubbla citattecknen) för att registrera den lokala installation av Integration Runtime som du installerar på datorn i nästa steg.

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
    ```

    Här är exempel på utdata:

    ```json
    {
        "AuthKey1":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx=",
        "AuthKey2":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy="
    }
    ```

## <a name="install-the-integration-runtime"></a>Installera Integration Runtime
1. Ladda ned [Azure Data Factory Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717) på en lokal Windows-dator och kör sedan installationen.

1. På **välkomstskärmen till installationsguiden för Microsoft Integration Runtime** klickar du på **Nästa**.  

1. I fönstret med **licensavtalet** godkänner du villkoren och licensavtalet och väljer **Nästa**.

1. I fönstret **Målmapp** väljer du **Nästa**.

1. I fönstret **Klar att installera Microsoft Integration Runtime** väljer du **Installera**.

1. I **installationsguiden för Microsoft Integration Runtime** väljer du **Slutför**.

1. Klistra in den nyckel som du sparade i föregående avsnitt i fönstret **Registrera Integration Runtime (lokal)** och klicka sedan på **Registrera**.

    ![Registrera Integration Runtime](media/tutorial-hybrid-copy-powershell/register-integration-runtime.png)

1. I fönstret **ny integration Runtime (lokal installation) nod** väljer du **Slutför**.

    ![Fönstret Ny nod för Integration Runtime](media/tutorial-hybrid-copy-powershell/new-integration-runtime-node-page.png)

 1. När den lokala installationen av Integration Runtime har registrerats ser du följande meddelande:

    ![Registered successfully (Registrerat)](media/tutorial-hybrid-copy-powershell/registered-successfully.png)

1. I fönstret **Registrera Integration Runtime (lokal)** väljer du **Starta Konfigurationshanteraren**.

1. Följande meddelande visas när noden är ansluten till molntjänsten:

    ![Node is connected (Noden är ansluten)](media/tutorial-hybrid-copy-powershell/node-is-connected.png)

1. Testa anslutningen till din SQL Server-databas genom att göra följande:

    a. I fönstret **Configuration Manager** växlar du till fliken **Diagnostik**.

    b. I rutan **Typ av datakälla** väljer du **SqlServer**.

    c. Ange servernamnet.

    d. Ange namnet på databasen.

    e. Välj autentiseringsläge.

    f. Ange användarnamnet.

    ex. Ange lösenordet som är kopplat till användarnamnet.

    h. Klicka på **Test** för att bekräfta att Integration Runtime kan ansluta till SQL Server.  
    ![Anslutningen lyckades](media/tutorial-hybrid-copy-powershell/config-manager-diagnostics-tab.png)

    Du ser en grön bockmarkering om anslutningen är upprättad. Annars ser du ett felmeddelande som är kopplat till felet. Åtgärda eventuella problem och se till att Integration Runtime kan ansluta till din SQL Server-instans.

    Notera alla föregående värden. Du behöver dem senare i den här självstudien.

## <a name="create-linked-services"></a>Skapa länkade tjänster
Skapa länkade tjänster i datafabriken för att länka dina datalager och beräkningstjänster till datafabriken. I den här självstudien länkar du ditt Azure Storage-konto och SQL Server instans till data lagret. De länkade tjänsterna har anslutningsinformationen som Data Factory-tjänsten använder vid körning för att ansluta till dem.

### <a name="create-an-azure-storage-linked-service-destinationsink"></a>Skapa en länkad Azure Storage-tjänst (destination/mottagare)
I det här steget länkar du ditt Azure Storage-konto till datafabriken.

1. Skapa en JSON-fil med namnet *AzureStorageLinkedService.json* i mappen *C:\ADFv2Tutorial* med följande kod. Skapa mappen *ADFv2Tutorial* om den inte redan finns.  

    > [!IMPORTANT]
    > Innan du sparar filen ersätter du \<accountName> och \<accountKey> med namnet och nyckeln för ditt Azure Storage-konto. Du noterade dem i avsnittet [Krav](#get-storage-account-name-and-account-key).

   ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "annotations": [],
            "type": "AzureBlobStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>;EndpointSuffix=core.windows.net"
            }
        }
    }
   ```

1. I PowerShell växlar du till mappen *C:\ADFv2Tutorial*.
   ```powershell
   Set-Location 'C:\ADFv2Tutorial'    
   ```

1. För att skapa den länkade tjänsten, AzureStorageLinkedService, kör du följande `Set-AzDataFactoryV2LinkedService`-cmdlet:

   ```powershell
   Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
   ```

   Här är exempel på utdata:

    ```console
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroup name>
    DataFactoryName   : <dataFactory name>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobStorageLinkedService
    ```

    Om du får felmeddelandet ”Filen hittades inte” kontrollerar du att filen finns genom att köra `dir`-kommandot. Om filnamnet har tillägget *.txt* (till exempel AzureStorageLinkedService.json.txt) tar du bort den och kör sedan PowerShell-kommandot igen.

### <a name="create-and-encrypt-a-sql-server-linked-service-source"></a>Skapa och kryptera en SQL Server som är länkad tjänst (källa)
I det här steget länkar du din SQL Server-instans till data fabriken.

1. Skapa en JSON-fil med namnet *SqlServerLinkedService.json* i mappen *C:\ADFv2Tutorial* genom att använda följande kod:

    > [!IMPORTANT]
    > Välj rätt avsnitt baserat på vilken autentisering du använder när du ansluter till SQL Server.

    **Anslut med SQL-autentisering (sa):**

    ```json
    {  
        "name":"SqlServerLinkedService",
        "type":"Microsoft.DataFactory/factories/linkedservices",
        "properties":{  
            "annotations":[  

            ],
            "type":"SqlServer",
            "typeProperties":{  
                "connectionString":"integrated security=False;data source=<serverName>;initial catalog=<databaseName>;user id=<userName>;password=<password>"
            },
            "connectVia":{  
                "referenceName":"<integration runtime name> ",
                "type":"IntegrationRuntimeReference"
            }
        }
    }
   ```    

    **Använd Windows-autentisering:**

    ```json
    {  
        "name":"SqlServerLinkedService",
        "type":"Microsoft.DataFactory/factories/linkedservices",
        "properties":{  
            "annotations":[  

            ],
            "type":"SqlServer",
            "typeProperties":{  
                "connectionString":"integrated security=True;data source=<serverName>;initial catalog=<databaseName>",
                "userName":"<username> or <domain>\\<username>",
                "password":{  
                    "type":"SecureString",
                    "value":"<password>"
                }
            },
            "connectVia":{  
                "referenceName":"<integration runtime name>",
                "type":"IntegrationRuntimeReference"
            }
        }
    }
    ```

    > [!IMPORTANT]
    > - Välj avsnitt baserat på vilken autentisering du använder när du ansluter till din SQL Server-instans.
    > - Ersätt  **\<integration runtime name>** med namnet på din integration Runtime.
    > - Innan du sparar filen ersätter du, **\<servername>** , **\<databasename>** **\<username>** och **\<password>** med värdena för din SQL Server-instans.
    > - Om du behöver använda ett omvänt snedstreck (\\) i ditt användarkonto eller servernamn infogar du escape-tecknet framför det (\\). Använd till exempel min *domän- \\ \\ användare*.

1. Kör `New-AzDataFactoryV2LinkedServiceEncryptedCredential`-cmdlet för att kryptera känsliga data (användarnamn, lösenord och så vidare).  
    Den här krypteringen ser till att autentiseringsuppgifterna krypteras med Data Protection Application Programming Interface (DPAPI). Krypterade autentiseringsuppgifter lagras lokalt på en IR-nod med egen värd (lokal dator). Nyttolasten i utdata kan omdirigeras till en annan JSON-fil (i det här fallet *encryptedLinkedService.json*) som innehåller krypterade autentiseringsuppgifter.

   ```powershell
   New-AzDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -IntegrationRuntimeName $integrationRuntimeName -File ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
   ```

1. Kör följande kommando, vilket skapar EncryptedSqlServerLinkedService:

   ```powershell
   Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -File ".\encryptedSqlServerLinkedService.json"
   ```


## <a name="create-datasets"></a>Skapa datauppsättningar
I det här steget skapar du datauppsättningar för indata och utdata. De representerar indata och utdata för kopierings åtgärden, som kopierar data från SQL Server-databasen till Azure Blob Storage.

### <a name="create-a-dataset-for-the-source-sql-server-database"></a>Skapa en datauppsättning för SQL Server-databaskällan
I det här steget definierar du en datauppsättning som representerar data i SQL Server-databasen. Datauppsättningen är av typen SqlServerTable. Den refererar till den SQL Server-länkade tjänst som du skapade i föregående steg. De länkade tjänsterna har anslutningsinformation som Data Factory-tjänsten använder för att ansluta till din SQL Server-instans vid körning. Den här datauppsättningen anger den SQL-tabell i databasen som innehåller data. I den här självstudien är det tabellen **emp** som innehåller datakällan.

1. Skapa en JSON-fil med namnet *SqlServerDataset.json* i mappen *C:\ADFv2Tutorial* med följande kod:  
    ```json
    {  
        "name":"SqlServerDataset",
        "properties":{  
            "linkedServiceName":{  
                "referenceName":"EncryptedSqlServerLinkedService",
                "type":"LinkedServiceReference"
            },
            "annotations":[  

            ],
            "type":"SqlServerTable",
            "schema":[  

            ],
            "typeProperties":{  
                "schema":"dbo",
                "table":"emp"
            }
        }
    }
    ```

1. För att skapa datauppsättningen SqlServerDataset kör du `Set-AzDataFactoryV2Dataset`-cmdlet.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SqlServerDataset" -File ".\SqlServerDataset.json"
    ```

    Här är exempel på utdata:

    ```console
    DatasetName       : SqlServerDataset
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerTableDataset
    ```

### <a name="create-a-dataset-for-azure-blob-storage-sink"></a>Skapa en datauppsättning för Azure Blob Storage (mottagare)
I det här steget definierar du en datauppsättning som representerar data som ska kopieras till Azure Blob Storage. Datauppsättningen är av typen AzureBlob. Den refererar till den Azure Storage-länkade tjänst som du skapade tidigare i den här självstudien.

Den länkade tjänsten har anslutningsinformationen som datafabriken använder vid körning för att ansluta till ditt Azure Storage-konto. Den här datauppsättningen anger den mapp i Azure-lagringen till vilken data kopieras från SQL Server-databasen. I den här självstudien är mappen *adftutorial/fromonprem*, där `adftutorial` är blobcontainern och `fromonprem` är mappen.

1. Skapa en JSON-fil med namnet *AzureBlobDataset.json* i mappen *C:\ADFv2Tutorial* med följande kod:

    ```json
    {  
        "name":"AzureBlobDataset",
        "properties":{  
            "linkedServiceName":{  
                "referenceName":"AzureStorageLinkedService",
                "type":"LinkedServiceReference"
            },
            "annotations":[  

            ],
            "type":"DelimitedText",
            "typeProperties":{  
                "location":{  
                    "type":"AzureBlobStorageLocation",
                    "folderPath":"fromonprem",
                    "container":"adftutorial"
                },
                "columnDelimiter":",",
                "escapeChar":"\\",
                "quoteChar":"\""
            },
            "schema":[  

            ]
        },
        "type":"Microsoft.DataFactory/factories/datasets"
    }
    ```

1. För att skapa datauppsättningen AzureBlobDataset kör du `Set-AzDataFactoryV2Dataset`-cdmlet.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureBlobDataset" -File ".\AzureBlobDataset.json"
    ```

    Här är exempel på utdata:

    ```console
    DatasetName       : AzureBlobDataset
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.DelimitedTextDataset
    ```

## <a name="create-a-pipeline"></a>Skapa en pipeline
I den här självstudien kan du skapa en pipeline med en kopieringsaktivitet. Kopieringsaktiviteten använder SqlServerDataset som inkommande datamängd och AzureBlobDataset som utgående datamängd. Källtypen är inställd på *SqlSource* och mottagartypen är inställd på *BlobSink*.

1. Skapa en JSON-fil med namnet *SqlServerToBlobPipeline.json* i mappen *C:\ADFv2Tutorial* med följande kod:

    ```json
    {  
        "name":"SqlServerToBlobPipeline",
        "properties":{  
            "activities":[  
                {  
                    "name":"CopySqlServerToAzureBlobActivity",
                    "type":"Copy",
                    "dependsOn":[  

                    ],
                    "policy":{  
                        "timeout":"7.00:00:00",
                        "retry":0,
                        "retryIntervalInSeconds":30,
                        "secureOutput":false,
                        "secureInput":false
                    },
                    "userProperties":[  

                    ],
                    "typeProperties":{  
                        "source":{  
                            "type":"SqlServerSource"
                        },
                        "sink":{  
                            "type":"DelimitedTextSink",
                            "storeSettings":{  
                                "type":"AzureBlobStorageWriteSettings"
                            },
                            "formatSettings":{  
                                "type":"DelimitedTextWriteSettings",
                                "quoteAllText":true,
                                "fileExtension":".txt"
                            }
                        },
                        "enableStaging":false
                    },
                    "inputs":[  
                        {  
                            "referenceName":"SqlServerDataset",
                            "type":"DatasetReference"
                        }
                    ],
                    "outputs":[  
                        {  
                            "referenceName":"AzureBlobDataset",
                            "type":"DatasetReference"
                        }
                    ]
                }
            ],
            "annotations":[  

            ]
        }
    }
    ```

1. Kör `Set-AzDataFactoryV2Pipeline`-cmdlet för att skapa pipelinen SqlServerToBlobPipeline.

    ```powershell
    Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SQLServerToBlobPipeline" -File ".\SQLServerToBlobPipeline.json"
    ```

    Här är exempel på utdata:

    ```console
    PipelineName      : SQLServerToBlobPipeline
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {CopySqlServerToAzureBlobActivity}
    Parameters        :  
    ```

## <a name="create-a-pipeline-run"></a>Skapa en pipelinekörning
Starta en pipelinekörning för pipelinen SQLServerToBlobPipeline och avbilda pipelinekörningens ID för framtida övervakning.

```powershell
$runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'SQLServerToBlobPipeline'
```

## <a name="monitor-the-pipeline-run"></a>Övervaka pipelinekörningen

1. Kör följande skript i PowerShell för att kontinuerligt kontrollera körningsstatusen för pipelinen SQLServerToBlobPipeline, och skriv ut slutresultatet:

    ```powershell
    while ($True) {
        $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

        if (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
            Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
            Start-Sleep -Seconds 30
        }
        else {
            Write-Host "Pipeline 'SQLServerToBlobPipeline' run finished. Result:" -foregroundcolor "Yellow"
            $result
            break
        }
    }
    ```

    Här är utdata för exempelkörningen:

    ```console
    ResourceGroupName    : <resourceGroupName>
    DataFactoryName      : <dataFactoryName>
    ActivityRunId        : 24af7cf6-efca-4a95-931d-067c5c921c25
    ActivityName         : CopySqlServerToAzureBlobActivity
    ActivityType         : Copy
    PipelineRunId        : 7b538846-fd4e-409c-99ef-2475329f5729
    PipelineName         : SQLServerToBlobPipeline
    Input                : {source, sink, enableStaging}
    Output               : {dataRead, dataWritten, filesWritten, sourcePeakConnections...}
    LinkedServiceName    :
    ActivityRunStart     : 9/11/2019 7:10:37 AM
    ActivityRunEnd       : 9/11/2019 7:10:58 AM
    DurationInMs         : 21094
    Status               : Succeeded
    Error                : {errorCode, message, failureType, target}
    AdditionalProperties : {[retryAttempt, ], [iterationHash, ], [userProperties, {}], [recoveryStatus, None]...}
    ```

1. Du kan få körnings-ID:t för pipelinen SQLServerToBlobPipeline, och kontrollera aktivitetens detaljerade körningsresultat genom att köra följande kommando:

    ```powershell
    Write-Host "Pipeline 'SQLServerToBlobPipeline' run result:" -foregroundcolor "Yellow"
    ($result | Where-Object {$_.ActivityName -eq "CopySqlServerToAzureBlobActivity"}).Output.ToString()
    ```

    Här är utdata för exempelkörningen:

    ```json
    {  
        "dataRead":36,
        "dataWritten":32,
        "filesWritten":1,
        "sourcePeakConnections":1,
        "sinkPeakConnections":1,
        "rowsRead":2,
        "rowsCopied":2,
        "copyDuration":18,
        "throughput":0.01,
        "errors":[  

        ],
        "effectiveIntegrationRuntime":"ADFTutorialIR",
        "usedParallelCopies":1,
        "executionDetails":[  
            {  
                "source":{  
                    "type":"SqlServer"
                },
                "sink":{  
                    "type":"AzureBlobStorage",
                    "region":"CentralUS"
                },
                "status":"Succeeded",
                "start":"2019-09-11T07:10:38.2342905Z",
                "duration":18,
                "usedParallelCopies":1,
                "detailedDurations":{  
                    "queuingDuration":6,
                    "timeToFirstByte":0,
                    "transferDuration":5
                }
            }
        ]
    }
    ```

## <a name="verify-the-output"></a>Verifiera utdata
Pipelinen skapar automatiskt utdatamappen med namnet *fromonprem* i `adftutorial`-blobcontainern. Bekräfta att filen *dbo.emp.txt* finns i utdatamappen.

1. I containerfönstret **adftutorial** i Azure-portalen väljer du **Uppdatera** för att se utdatamappen.
1. Välj `fromonprem` i listan över mappar.
1. Bekräfta att du ser en fil med namnet `dbo.emp.txt`.

    ![Utdatafil](media/tutorial-hybrid-copy-powershell/fromonprem-file.png)


## <a name="next-steps"></a>Nästa steg
Pipelinen i det här exemplet kopierar data från en plats till en annan i Azure Blob Storage. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Skapa Integration Runtime med egen värd.
> * Skapa länkade tjänster för SQL Server och Azure Storage.
> * Skapa datauppsättningar för SQL Server och Azure Blob.
> * Skapa en pipeline med en kopieringsaktivitet för att flytta data.
> * Starta en pipelinekörning.
> * Övervaka pipelinekörningen.

Se [Datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) för att få en lista över datalager som stöds av datafabriken.

Fortsätt till följande självstudie för att lära dig att masskopiera data från en källa till ett mål:

> [!div class="nextstepaction"]
>[Masskopiera data](tutorial-bulk-copy.md)