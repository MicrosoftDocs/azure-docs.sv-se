---
title: Kopiera flera tabeller stegvis med PowerShell
description: I den här självstudien skapar du en Azure Data Factory med en pipeline som läser in delta data från flera tabeller i en SQL Server databas till Azure SQL Database.
services: data-factory
ms.author: yexu
author: dearandyxu
manager: anandsub
ms.reviewer: douglasl, maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 06/10/2020
ms.openlocfilehash: 61a4119947b1412d3e874458e06748fd40a381b4
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97510268"
---
# <a name="incrementally-load-data-from-multiple-tables-in-sql-server-to-azure-sql-database-using-powershell"></a>Läs in data stegvis från flera tabeller i SQL Server till Azure SQL Database med PowerShell

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

I den här självstudien skapar du en Azure Data Factory med en pipeline som läser in delta data från flera tabeller i en SQL Server databas till Azure SQL Database.    

I den här självstudiekursen får du göra följande:

> [!div class="checklist"]
> * Förbereda käll- och måldatalager.
> * Skapa en datafabrik.
> * Skapa Integration Runtime med egen värd.
> * Installera Integration Runtime. 
> * Skapa länkade tjänster. 
> * Skapa datauppsättningar för källa, mottagare och vattenstämpel.
> * Skapa, köra och övervaka en pipeline.
> * Granska resultaten.
> * Lägga till eller uppdatera data i källtabeller.
> * Köra och övervaka pipelinen igen.
> * Granska de slutliga resultaten.

## <a name="overview"></a>Översikt
Här är några viktiga steg för att skapa den här lösningen: 

1. **Markera vattenstämpelkolumnen**.

    Välj en kolumn för varje tabell i käll data lagret, som du kan använda för att identifiera de nya eller uppdaterade posterna för varje körning. Vanligtvis ökar data i den markerade kolumnen (till exempel last_modify_time elle ID) när rader skapas eller uppdateras. Det maximala värdet i den här kolumnen används som vattenstämpel.

2. **Förbered datalagringen för att lagra värdet för vattenstämpeln**.

    I den här självstudien lagrar du storleksgränsen i en SQL-databas.

3. **Skapa en pipeline med följande aktiviteter**:
    
    a. Skapa en ForEach-aktivitet som upprepas över en lista med namn på källtabeller och som skickas som en parameter till pipelinen. För varje källtabell anropas följande aktiviteter som utför deltainläsningen för tabellen.

    b. Skapa två sökningsaktiviteter. Använd den första sökningsaktiviteten för att hämta det sista vattenstämpelvärdet. Använd den andra sökningsaktiviteten för att hämta det nya vattenstämpelvärdet. Vattenstämpelvärdena skickas till kopieringsaktiviteten.

    c. Skapa en {0}kopieringsaktivitet{0} som kopierar raderna från källdatalagringen med värdet för vattenstämpelkolumnen som är större än det gamla värdet och mindre än det nya. Sedan kopieras deltadata från källdatalagringen till Azure Blob-lagring som en ny fil.

    d. Skapa en StoredProcedure-aktivitet som uppdaterar vattenstämpelvärdet för den pipeline som körs nästa gång. 

    Här är det avancerade diagrammet: 

    ![Läsa in data stegvis](media/tutorial-incremental-copy-multiple-tables-powershell/high-level-solution-diagram.png)


Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="prerequisites"></a>Krav

* **SQL Server**. Du använder en SQL Server databas som käll data lager i den här självstudien. 
* **Azure SQL Database**. Du använder en databas i Azure SQL Database som data lager för mottagare. Om du inte har en SQL-databas kan du läsa [skapa en databas i Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md) för steg för att skapa en. 

### <a name="create-source-tables-in-your-sql-server-database"></a>Skapa källtabeller i din SQL Server-databas

1. Öppna [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) eller [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)och Anslut till SQL Server-databasen.

2. I **Server Explorer (SSMS)** eller i **fönstret anslutningar (Azure Data Studio)** högerklickar du på databasen och väljer **ny fråga**.

3. Kör följande SQL-kommando mot databasen för att skapa tabeller med namnen `customer_table` och `project_table`:

    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );
        
    INSERT INTO customer_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'John','9/1/2017 12:56:00 AM'),
    (2, 'Mike','9/2/2017 5:23:00 AM'),
    (3, 'Alice','9/3/2017 2:36:00 AM'),
    (4, 'Andy','9/4/2017 3:21:00 AM'),
    (5, 'Anny','9/5/2017 8:06:00 AM');
    
    INSERT INTO project_table
    (Project, Creationtime)
    VALUES
    ('project1','1/1/2015 0:00:00 AM'),
    ('project2','2/2/2016 1:23:00 AM'),
    ('project3','3/4/2017 5:16:00 AM');
    ```

### <a name="create-destination-tables-in-your-azure-sql-database"></a>Skapa mål tabeller i din Azure SQL Database

1. Öppna [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) eller [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)och Anslut till SQL Server-databasen.

2. I **Server Explorer (SSMS)** eller i **fönstret anslutningar (Azure Data Studio)** högerklickar du på databasen och väljer **ny fråga**.

3. Kör följande SQL-kommando mot databasen för att skapa tabeller med namnen `customer_table` och `project_table`:  

    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );
    ```

### <a name="create-another-table-in-azure-sql-database-to-store-the-high-watermark-value"></a>Skapa en annan tabell i Azure SQL Database för att lagra värdet för hög vatten märket

1. Kör följande SQL-kommando mot databasen för att skapa en tabell med namnet `watermarktable` för att lagra värdet för vattenstämpeln: 
    
    ```sql
    create table watermarktable
    (
    
        TableName varchar(255),
        WatermarkValue datetime,
    );
    ```
2. Infoga inledande värden för högvattenmärket för båda källtabellerna i vattenmärkestabellen.

    ```sql

    INSERT INTO watermarktable
    VALUES 
    ('customer_table','1/1/2010 12:00:00 AM'),
    ('project_table','1/1/2010 12:00:00 AM');
    
    ```

### <a name="create-a-stored-procedure-in-the-azure-sql-database"></a>Skapa en lagrad procedur i Azure SQL Database 

Kör följande kommando för att skapa en lagrad procedur i databasen. Den här lagrade proceduren uppdaterar vattenmärkets värde efter varje pipelinekörning. 

```sql
CREATE PROCEDURE usp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

UPDATE watermarktable
SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName

END

```

### <a name="create-data-types-and-additional-stored-procedures-in-azure-sql-database"></a>Skapa data typer och ytterligare lagrade procedurer i Azure SQL Database

Kör följande fråga för att skapa två lagrade procedurer och två data typer i databasen. De används för att slå samman data från källtabellerna till måltabellerna. 

För att göra resan lätt att börja med, använder vi direkt dessa lagrade procedurer som skickar delta data i via en tabell variabel och sedan sammanfogar dem till mål lagret. Var försiktig att det inte förväntar sig att ett "stort" antal delta rader (mer än 100) lagras i tabell variabeln.  

Om du behöver slå samman ett stort antal delta rader i mål lagret, rekommenderar vi att du använder kopierings aktivitet för att kopiera alla delta data till en tillfällig "mellanlagrings tabell" i mål lagret först och sedan skapa en egen lagrad procedur utan att använda tabell variabel för att sammanfoga dem från tabellen "mellanlagring" till den "slutgiltiga" tabellen. 


```sql
CREATE TYPE DataTypeforCustomerTable AS TABLE(
    PersonID int,
    Name varchar(255),
    LastModifytime datetime
);

GO

CREATE PROCEDURE usp_upsert_customer_table @customer_table DataTypeforCustomerTable READONLY
AS

BEGIN
  MERGE customer_table AS target
  USING @customer_table AS source
  ON (target.PersonID = source.PersonID)
  WHEN MATCHED THEN
      UPDATE SET Name = source.Name,LastModifytime = source.LastModifytime
  WHEN NOT MATCHED THEN
      INSERT (PersonID, Name, LastModifytime)
      VALUES (source.PersonID, source.Name, source.LastModifytime);
END

GO

CREATE TYPE DataTypeforProjectTable AS TABLE(
    Project varchar(255),
    Creationtime datetime
);

GO

CREATE PROCEDURE usp_upsert_project_table @project_table DataTypeforProjectTable READONLY
AS

BEGIN
  MERGE project_table AS target
  USING @project_table AS source
  ON (target.Project = source.Project)
  WHEN MATCHED THEN
      UPDATE SET Creationtime = source.Creationtime
  WHEN NOT MATCHED THEN
      INSERT (Project, Creationtime)
      VALUES (source.Project, source.Creationtime);
END
```

### <a name="azure-powershell"></a>Azure PowerShell

Installera de senaste Azure PowerShell-modulerna enligt instruktionerna i [Installera och konfigurera Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. Definiera en variabel för resursgruppens namn som du kan använda senare i PowerShell-kommandon. Kopiera följande kommandotext till PowerShell, ange ett namn för [Azure-resursgruppen](../azure-resource-manager/management/overview.md), sätt dubbla citattecken omkring namnet och kör sedan kommandot. Ett exempel är `"adfrg"`.

    ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Om resursgruppen redan finns behöver du kanske inte skriva över den. Ge variabeln `$resourceGroupName` ett annat värde och kör kommandot igen.

2. Definiera en variabel för datafabrikens plats. 

    ```powershell
    $location = "East US"
    ```
3. Kör följande kommando för att skapa en Azure-resursgrupp: 

    ```powershell
    New-AzResourceGroup $resourceGroupName $location
    ``` 
    Om resursgruppen redan finns behöver du kanske inte skriva över den. Ge variabeln `$resourceGroupName` ett annat värde och kör kommandot igen.

4. Definiera en variabel för datafabrikens namn. 

    > [!IMPORTANT]
    >  Uppdateringen av datafabrikens namn för att göra det unikt globalt. Ett exempel är ADFIncMultiCopyTutorialFactorySP1127. 

    ```powershell
    $dataFactoryName = "ADFIncMultiCopyTutorialFactory";
    ```
5. Skapa data fabriken genom att köra följande **set-AzDataFactoryV2-** cmdlet: 
    
    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Location $location -Name $dataFactoryName 
    ```

Observera följande punkter:

* Namnet på datafabriken måste vara globalt unikt. Om du får följande felmeddelande ändrar du namnet och försöker igen:

    ```powershell
    Set-AzDataFactoryV2 : HTTP Status Code: Conflict
    Error Code: DataFactoryNameInUse
    Error Message: The specified resource name 'ADFIncMultiCopyTutorialFactory' is already in use. Resource names must be globally unique.
    ```

* Om du vill skapa Data Factory-instanser måste det användarkonto du använder för att logga in på Azure vara medlem av rollerna deltagare eller ägare, eller vara administratör för Azure-prenumerationen.

* Om du vill se en lista med Azure-regioner där Data Factory är tillgängligt för närvarande markerar du de regioner du är intresserad av på följande sida. Expandera sedan **Analytics** och leta rätt på **Data Factory**: [Tillgängliga produkter per region](https://azure.microsoft.com/global-infrastructure/services/). Data lag ren (Azure Storage, SQL Database, SQL-hanterad instans osv.) och beräkningarna (Azure HDInsight osv.) som används av data fabriken kan finnas i andra regioner.

[!INCLUDE [data-factory-create-install-integration-runtime](../../includes/data-factory-create-install-integration-runtime.md)]

## <a name="create-linked-services"></a>Skapa länkade tjänster

Du kan skapa länkade tjänster i en datafabrik för att länka ditt datalager och beräkna datafabrik-tjänster. I det här avsnittet skapar du länkade tjänster till din SQL Server-databas och din databas i Azure SQL Database. 

### <a name="create-the-sql-server-linked-service"></a>Skapa länkad tjänst till SQL Server

I det här steget länkar du SQL Server-databasen till data fabriken.

1. Skapa en JSON-fil med namnet **SqlServerLinkedService.js** i mappen mappen c:\adftutorials\inccopymultitabletutorial (skapa de lokala mapparna om de inte redan finns) med följande innehåll. Välj rätt avsnitt baserat på vilken autentisering du använder när du ansluter till SQL Server.  

    > [!IMPORTANT]
    > Välj rätt avsnitt baserat på vilken autentisering du använder när du ansluter till SQL Server.

    Om du använder SQL-autentisering kopierar du följande JSON-definition:

    ```json
    {  
        "name":"SqlServerLinkedService",
        "properties":{  
            "annotations":[  
    
            ],
            "type":"SqlServer",
            "typeProperties":{  
                "connectionString":"integrated security=False;data source=<servername>;initial catalog=<database name>;user id=<username>;Password=<password>"
            },
            "connectVia":{  
                "referenceName":"<integration runtime name>",
                "type":"IntegrationRuntimeReference"
            }
        }
    } 
   ```    
    Om du använder Windows-autentisering kopierar du följande JSON-definition:

    ```json
    {  
        "name":"SqlServerLinkedService",
        "properties":{  
            "annotations":[  
    
            ],
            "type":"SqlServer",
            "typeProperties":{  
                "connectionString":"integrated security=True;data source=<servername>;initial catalog=<database name>",
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
    > - Välj rätt avsnitt baserat på vilken autentisering du använder när du ansluter till SQL Server.
    > - Ersätt &lt;integration runtime-namn > med namnet på din Integration Runtime.
    > - Ersätt &lt;servername>&lt;, databasename>, &lt;username>&lt; och password> med värdena för din SQL Serverdatabas innan du sparar filen.
    > - Om du behöver använda ett snedstreck (`\`) i ditt användarkonto eller användarnamn använder du escape-tecknet (`\`). Ett exempel är `mydomain\\myuser`.

2. I PowerShell kör du följande cmdlet för att växla till mappen mappen c:\adftutorials\inccopymultitabletutorial.

    ```powershell
    Set-Location 'C:\ADFTutorials\IncCopyMultiTableTutorial'
    ```

3. Kör cmdleten **set-AzDataFactoryV2LinkedService** för att skapa den länkade tjänsten AzureStorageLinkedService. I följande exempel skickar du värden för parametrarna *ResourceGroupName* och *DataFactoryName* : 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SqlServerLinkedService" -File ".\SqlServerLinkedService.json"
    ```

    Här är exempel på utdata:

    ```console
    LinkedServiceName : SqlServerLinkedService
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerLinkedService
    ```

### <a name="create-the-sql-database-linked-service"></a>Skapa den länkade tjänsten SQL Database

1. Skapa en JSON-fil med namnet **AzureSQLDatabaseLinkedService.jspå** i mappen c:\adftutorials\inccopymultitabletutorial-mappen med följande innehåll. (Skapa mappen ADF om den inte redan finns.) Ersätt &lt; servername &gt; , &lt; databasens namn &gt; , &lt; användar namn &gt; och &lt; lösen ord &gt; med namnet på din SQL Server databas, namnet på din databas, ditt användar namn och lösen ord innan du sparar filen. 

    ```json
    {  
        "name":"AzureSQLDatabaseLinkedService",
        "properties":{  
            "annotations":[  
    
            ],
            "type":"AzureSqlDatabase",
            "typeProperties":{  
                "connectionString":"integrated security=False;encrypt=True;connection timeout=30;data source=<servername>.database.windows.net;initial catalog=<database name>;user id=<user name>;Password=<password>;"
            }
        }
    }
    ```
2. I PowerShell kör du cmdleten **set-AzDataFactoryV2LinkedService** för att skapa den länkade tjänsten AzureSQLDatabaseLinkedService. 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSQLDatabaseLinkedService" -File ".\AzureSQLDatabaseLinkedService.json"
    ```

    Här är exempel på utdata:

    ```console
    LinkedServiceName : AzureSQLDatabaseLinkedService
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ```

## <a name="create-datasets"></a>Skapa datauppsättningar

I det här steget skapar du datauppsättningar som representerar datakällan, datamålet och platsen för vattenstämpeln.

### <a name="create-a-source-dataset"></a>Skapa en källdatauppsättning

1. Skapa en JSON-fil med namnet **SourceDataset.js** i samma mapp med följande innehåll: 

    ```json
    {  
        "name":"SourceDataset",
        "properties":{  
            "linkedServiceName":{  
                "referenceName":"SqlServerLinkedService",
                "type":"LinkedServiceReference"
            },
            "annotations":[  
    
            ],
            "type":"SqlServerTable",
            "schema":[  
    
            ]
        }
    }
   
    ```

    Kopieringsaktivitet i pipelinen använder en SQL-fråga till att läsa in data, snarare än att läsa in hela tabellen.

2. Kör cmdleten **set-AzDataFactoryV2Dataset** för att skapa data uppsättningen SourceDataset.
    
    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SourceDataset" -File ".\SourceDataset.json"
    ```

    Här är exempel på utdata från cmdleten:
    
    ```json
    DatasetName       : SourceDataset
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerTableDataset
    ```

### <a name="create-a-sink-dataset"></a>Skapa en källdatauppsättning

1. Skapa en JSON-fil med namnet **SinkDataset.js** i samma mapp med följande innehåll. Elementet tableName har angetts av pipelinen dynamiskt vid körning. ForEach-aktiviteten i pipelinen upprepas över en lista med tabellnamn och skickar tabellnamnet till datamängden i varje iteration. 

    ```json
    {  
        "name":"SinkDataset",
        "properties":{  
            "linkedServiceName":{  
                "referenceName":"AzureSQLDatabaseLinkedService",
                "type":"LinkedServiceReference"
            },
            "parameters":{  
                "SinkTableName":{  
                    "type":"String"
                }
            },
            "annotations":[  
    
            ],
            "type":"AzureSqlTable",
            "typeProperties":{  
                "tableName":{  
                    "value":"@dataset().SinkTableName",
                    "type":"Expression"
                }
            }
        }
    }
    ```

2. Kör cmdleten **set-AzDataFactoryV2Dataset** för att skapa data uppsättningen SinkDataset.
    
    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SinkDataset" -File ".\SinkDataset.json"
    ```

    Här är exempel på utdata från cmdleten:
    
    ```json
    DatasetName       : SinkDataset
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-dataset-for-a-watermark"></a>Skapa en datauppsättning för en vattenstämpel

I det här steget skapar du en datauppsättning för att lagra ett värde för ett högvattenmärke. 

1. Skapa en JSON-fil med namnet **WatermarkDataset.js** i samma mapp med följande innehåll: 

    ```json
    {
        "name": " WatermarkDataset ",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "watermarktable"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }    
    ```
2. Kör cmdleten **set-AzDataFactoryV2Dataset** för att skapa data uppsättningen WatermarkDataset.
    
    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "WatermarkDataset" -File ".\WatermarkDataset.json"
    ```

    Här är exempel på utdata från cmdleten:
    
    ```json
    DatasetName       : WatermarkDataset
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset    
    ```

## <a name="create-a-pipeline"></a>Skapa en pipeline

Den här pipelinen tar en lista med tabellnamn som en parameter. Den **förgrunds aktiviteten** itererar igenom listan med tabell namn och utför följande åtgärder: 

1. Använd **Lookup-aktiviteten** för att hämta det gamla värdet för vatten märket (det första värdet eller det som användes i den senaste iterationen).

2. Använd **Lookup-aktiviteten** för att hämta det nya värdet för vatten märket (max värdet för kolumnen vattenstämpel i käll tabellen).

3. Använd **kopierings aktiviteten** för att kopiera data mellan de två värdena för vatten märket från käll databasen till mål databasen.

4. Använd **StoredProcedure-aktiviteten** för att uppdatera det gamla värdet för vatten märket som ska användas i det första steget i nästa iteration. 

### <a name="create-the-pipeline"></a>Skapa pipelinen

1. Skapa en JSON-fil med namnet **IncrementalCopyPipeline.js** i samma mapp med följande innehåll: 

    ```json
    {  
        "name":"IncrementalCopyPipeline",
        "properties":{  
            "activities":[  
                {  
                    "name":"IterateSQLTables",
                    "type":"ForEach",
                    "dependsOn":[  
    
                    ],
                    "userProperties":[  
    
                    ],
                    "typeProperties":{  
                        "items":{  
                            "value":"@pipeline().parameters.tableList",
                            "type":"Expression"
                        },
                        "isSequential":false,
                        "activities":[  
                            {  
                                "name":"LookupOldWaterMarkActivity",
                                "type":"Lookup",
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
                                        "type":"AzureSqlSource",
                                        "sqlReaderQuery":{  
                                            "value":"select * from watermarktable where TableName  =  '@{item().TABLE_NAME}'",
                                            "type":"Expression"
                                        }
                                    },
                                    "dataset":{  
                                        "referenceName":"WatermarkDataset",
                                        "type":"DatasetReference"
                                    }
                                }
                            },
                            {  
                                "name":"LookupNewWaterMarkActivity",
                                "type":"Lookup",
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
                                        "type":"SqlServerSource",
                                        "sqlReaderQuery":{  
                                            "value":"select MAX(@{item().WaterMark_Column}) as NewWatermarkvalue from @{item().TABLE_NAME}",
                                            "type":"Expression"
                                        }
                                    },
                                    "dataset":{  
                                        "referenceName":"SourceDataset",
                                        "type":"DatasetReference"
                                    },
                                    "firstRowOnly":true
                                }
                            },
                            {  
                                "name":"IncrementalCopyActivity",
                                "type":"Copy",
                                "dependsOn":[  
                                    {  
                                        "activity":"LookupOldWaterMarkActivity",
                                        "dependencyConditions":[  
                                            "Succeeded"
                                        ]
                                    },
                                    {  
                                        "activity":"LookupNewWaterMarkActivity",
                                        "dependencyConditions":[  
                                            "Succeeded"
                                        ]
                                    }
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
                                        "type":"SqlServerSource",
                                        "sqlReaderQuery":{  
                                            "value":"select * from @{item().TABLE_NAME} where @{item().WaterMark_Column} > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and @{item().WaterMark_Column} <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'",
                                            "type":"Expression"
                                        }
                                    },
                                    "sink":{  
                                        "type":"AzureSqlSink",
                                        "sqlWriterStoredProcedureName":{  
                                            "value":"@{item().StoredProcedureNameForMergeOperation}",
                                            "type":"Expression"
                                        },
                                        "sqlWriterTableType":{  
                                            "value":"@{item().TableType}",
                                            "type":"Expression"
                                        },
                                        "storedProcedureTableTypeParameterName":{  
                                            "value":"@{item().TABLE_NAME}",
                                            "type":"Expression"
                                        },
                                        "disableMetricsCollection":false
                                    },
                                    "enableStaging":false
                                },
                                "inputs":[  
                                    {  
                                        "referenceName":"SourceDataset",
                                        "type":"DatasetReference"
                                    }
                                ],
                                "outputs":[  
                                    {  
                                        "referenceName":"SinkDataset",
                                        "type":"DatasetReference",
                                        "parameters":{  
                                            "SinkTableName":{  
                                                "value":"@{item().TABLE_NAME}",
                                                "type":"Expression"
                                            }
                                        }
                                    }
                                ]
                            },
                            {  
                                "name":"StoredProceduretoWriteWatermarkActivity",
                                "type":"SqlServerStoredProcedure",
                                "dependsOn":[  
                                    {  
                                        "activity":"IncrementalCopyActivity",
                                        "dependencyConditions":[  
                                            "Succeeded"
                                        ]
                                    }
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
                                    "storedProcedureName":"[dbo].[usp_write_watermark]",
                                    "storedProcedureParameters":{  
                                        "LastModifiedtime":{  
                                            "value":{  
                                                "value":"@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}",
                                                "type":"Expression"
                                            },
                                            "type":"DateTime"
                                        },
                                        "TableName":{  
                                            "value":{  
                                                "value":"@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}",
                                                "type":"Expression"
                                            },
                                            "type":"String"
                                        }
                                    }
                                },
                                "linkedServiceName":{  
                                    "referenceName":"AzureSQLDatabaseLinkedService",
                                    "type":"LinkedServiceReference"
                                }
                            }
                        ]
                    }
                }
            ],
            "parameters":{  
                "tableList":{  
                    "type":"array"
                }
            },
            "annotations":[  
    
            ]
        }
    }
    ```
2. Kör cmdleten **set-AzDataFactoryV2Pipeline** för att skapa pipelinen IncrementalCopyPipeline.
    
   ```powershell
   Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IncrementalCopyPipeline" -File ".\IncrementalCopyPipeline.json"
   ``` 

   Här är exempel på utdata: 

   ```console
    PipelineName      : IncrementalCopyPipeline
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Activities        : {IterateSQLTables}
    Parameters        : {[tableList, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```
 
## <a name="run-the-pipeline"></a>Köra en pipeline

1. Skapa en parameter fil med namnet **Parameters.js** i samma mapp med följande innehåll:

    ```json
    {
        "tableList": 
        [
            {
                "TABLE_NAME": "customer_table",
                "WaterMark_Column": "LastModifytime",
                "TableType": "DataTypeforCustomerTable",
                "StoredProcedureNameForMergeOperation": "usp_upsert_customer_table"
            },
            {
                "TABLE_NAME": "project_table",
                "WaterMark_Column": "Creationtime",
                "TableType": "DataTypeforProjectTable",
                "StoredProcedureNameForMergeOperation": "usp_upsert_project_table"
            }
        ]
    }
    ```
2. Kör pipeline-IncrementalCopyPipeline med cmdleten **Invoke-AzDataFactoryV2Pipeline** . Ersätt platshållarna med din egen grupp och datafabrikens namn.

    ```powershell
    $RunId = Invoke-AzDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup $resourceGroupName -dataFactoryName $dataFactoryName -ParameterFile ".\Parameters.json"        
    ``` 

## <a name="monitor-the-pipeline"></a>Övervaka pipeline

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Välj **Alla tjänster**, sök med nyckelordet *Datafabriker* och välj **Datafabriker**. 

3. Sök efter din datafabrik i listan med datafabriker och välj den så att du öppnar sidan **Datafabrik**. 

4. På sidan **data fabrik** väljer du **Redigera & övervakare** för att starta Azure Data Factory på en separat flik.

5. På sidan **nu sätter vi igång** väljer du **övervaka** till vänster. 
![Skärm bild som visar sidan för att komma igång med Azure Data Factory.](media/doc-common-process/get-started-page-monitor-button.png)    

6. Du kan se alla pipelinekörningar och deras status. Lägg i följande exempel märke till att statusen för pipelinekörningen är **Lyckades**. Du kan kontrollera parametrarna som skickats till pipelinen genom att klicka på länken i kolumnen **Parametrar**. Om det uppstod ett fel ser du en länk i kolumnen **Fel**.

    ![Skärm bild som visar pipeline-körningar för en data fabrik, inklusive din pipeline.](media/tutorial-incremental-copy-multiple-tables-powershell/monitor-pipeline-runs-4.png)    
7. När du väljer länken i kolumnen **åtgärder** visas alla aktivitets körningar för pipelinen. 

8. Om du vill gå tillbaka till vyn **pipeline-körningar** väljer du **alla pipeline-körningar**. 

## <a name="review-the-results"></a>Granska resultaten

Kör följande frågor mot SQL-måldatabasen i SQL Server Management Studio för att verifiera att data har kopierats från källtabellerna till måltabellerna: 

**Query** 
```sql
select * from customer_table
```

**Resultat**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           Alice   2017-09-03 02:36:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

**Query**

```sql
select * from project_table
```

**Resultat**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
```

**Query**

```sql
select * from watermarktable
```

**Resultat**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-05 08:06:00.000
project_table   2017-03-04 05:16:00.000
```

Observera att vattenstämpelvärdena för båda tabellerna har uppdaterats. 

## <a name="add-more-data-to-the-source-tables"></a>Lägga till mer data i källtabellerna

Kör följande fråga mot SQL Servers källdatabas för att uppdatera en befintlig rad i customer_table. Infoga en ny rad i project_table. 

```sql
UPDATE customer_table
SET [LastModifytime] = '2017-09-08T00:00:00Z', [name]='NewName' where [PersonID] = 3

INSERT INTO project_table
(Project, Creationtime)
VALUES
('NewProject','10/1/2017 0:00:00 AM');
``` 

## <a name="rerun-the-pipeline"></a>Kör pipelinen igen

1. Kör nu pipelinen igen genom att köra följande PowerShell-kommando:

    ```powershell
    $RunId = Invoke-AzDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup $resourceGroupname -dataFactoryName $dataFactoryName -ParameterFile ".\Parameters.json"
    ```
2. Övervaka pipelinekörningarna genom att följa anvisningarna i avsnittet [Övervaka pipelinen](#monitor-the-pipeline). När status för pipelinen **pågår**, ser du en annan åtgärds länk under **åtgärder** för att avbryta pipeline-körningen. 

3. Välj **Uppdatera** om du vill uppdatera listan tills pipelinekörningen lyckas. 

4. Om du vill kan du välja **Visa aktivitetskörningar** under **Åtgärder** om du vill se alla aktivitetskörningar som är associerade med den här pipelinekörningen. 

## <a name="review-the-final-results"></a>Granska de slutliga resultaten

Kör följande frågor mot måldatabasen i SQL Server Management Studio för att verifiera att nya/uppdaterade data har kopierats från källtabellerna till måltabellerna. 

**Query** 
```sql
select * from customer_table
```

**Resultat**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           NewName 2017-09-08 00:00:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

Lägg märke till de nya värdena för **Name** och **LastModifytime** för **PersonID** för nummer 3. 

**Query**

```sql
select * from project_table
```

**Resultat**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
NewProject  2017-10-01 00:00:00.000
```

Observera att posten **NewProject** har lagts till i project_table. 

**Query**

```sql
select * from watermarktable
```

**Resultat**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-08 00:00:00.000
project_table   2017-10-01 00:00:00.000
```

Observera att vattenstämpelvärdena för båda tabellerna har uppdaterats.

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen fick du: 

> [!div class="checklist"]
> * Förbereda käll- och måldatalager.
> * Skapa en datafabrik.
> * Skapa Integration Runtime (IR) med egen värd.
> * Installera Integration Runtime.
> * Skapa länkade tjänster. 
> * Skapa datauppsättningar för källa, mottagare och vattenstämpel.
> * Skapa, köra och övervaka en pipeline.
> * Granska resultaten.
> * Lägga till eller uppdatera data i källtabeller.
> * Köra och övervaka pipelinen igen.
> * Granska de slutliga resultaten.

Fortsätt till följande självstudie och lär dig att transformera data med ett Spark-kluster på Azure:

> [!div class="nextstepaction"]
>[Läs in data stegvis från Azure SQL Database till Azure Blob Storage med ändringsspårningsteknik](tutorial-incremental-copy-change-tracking-feature-powershell.md)
