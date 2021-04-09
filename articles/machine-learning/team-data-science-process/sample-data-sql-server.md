---
title: Exempel data i SQL Server på Azure-team data science process
description: Exempel data som lagras i SQL Server på Azure med SQL eller python-programmeringsspråket, och sedan flyttar det till Azure Machine Learning.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 7ac1fc5688dad3406041f36ff858e6fd27c7272f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "93321875"
---
# <a name="sample-data-in-sql-server-on-azure"></a><a name="heading"></a>Exempeldata i SQL Server på Azure

Den här artikeln visar hur du kan sampla data som lagras i SQL Server på Azure med SQL eller python-programmeringsspråket. Det visar också hur du flyttar exempel data till Azure Machine Learning genom att spara dem till en fil, ladda upp den till en Azure-blob och sedan läsa den i Azure Machine Learning Studio.

Python-samplingen använder [pyodbc](https://code.google.com/p/pyodbc/) ODBC-biblioteket för att ansluta till SQL Server på Azure och [Pandas](https://pandas.pydata.org/) -biblioteket för att utföra samplingen.

> [!NOTE]
> Exempel-SQL-koden i det här dokumentet förutsätter att data finns i en SQL Server på Azure. Om så inte är fallet kan du läsa mer i avsnittet [Flytta data till SQL Server i Azure](move-sql-server-virtual-machine.md) för instruktioner om hur du flyttar dina data till SQL Server på Azure.
> 
> 

**Varför ska du testa dina data?**
Om data uppsättningen som du planerar att analysera är stor är det vanligt vis en bra idé att stänga av data för att minska den till en mindre men representativ och mer hanterbar storlek. Sampling underlättar data förståelse, utforskning och funktions teknik. Dess roll i [TDSP (Team data science process)](./index.yml) är att möjliggöra snabb prototyper av data bearbetnings funktioner och maskin inlärnings modeller.

Den här samplings aktiviteten är ett steg i [TDSP (Team data science process)](./index.yml).

## <a name="using-sql"></a><a name="SQL"></a>Använda SQL
I det här avsnittet beskrivs flera metoder som använder SQL för att utföra enkla slumpmässiga samplingar mot data i databasen. Välj en metod baserat på din data storlek och dess distribution.

Följande två objekt visar hur du använder `newid` i SQL Server för att utföra samplingen. Vilken metod du väljer beror på hur slumpmässiga du vill att exemplet ska vara (pk_id i följande exempel kod antas vara en automatiskt genererad primär nyckel).

1. Mindre strikt slumpmässigt exempel

    ```sql
    select  * from <table_name> where <primary_key> in 
    (select top 10 percent <primary_key> from <table_name> order by newid())
    ```

2. Fler slumpmässiga exempel 

    ```sql
    SELECT * FROM <table_name>
    WHERE 0.1 >= CAST(CHECKSUM(NEWID(), <primary_key>) & 0x7fffffff AS float)/ CAST (0x7fffffff AS int)
    ```

Tablesample kan också användas för att sampla data. Det här alternativet kan vara ett bättre tillvägagångs sätt om din data storlek är stor (förutsatt att data på olika sidor inte är korrelerade) och att frågan slutförs inom rimlig tid.

```sql
SELECT *
FROM <table_name> 
TABLESAMPLE (10 PERCENT)
```

> [!NOTE]
> Du kan utforska och generera funktioner från dessa exempel data genom att lagra dem i en ny tabell
> 
> 

### <a name="connecting-to-azure-machine-learning"></a><a name="sql-aml"></a>Ansluter till Azure Machine Learning
Du kan direkt använda exempel frågorna ovan i modulen Azure Machine Learning [Importera data][import-data] för att nedåt – sampla data i farten och ta dem i ett Azure Machine Learning experiment. En skärm bild av hur du använder modulen läsare för att läsa exempel data visas här:

![läsa SQL][1]

## <a name="using-the-python-programming-language"></a><a name="python"></a>Använda python-programmeringsspråk
Det här avsnittet visar hur du använder [pyodbc-biblioteket](https://code.google.com/p/pyodbc/) för att upprätta en ODBC-anslutning till en SQL Server-databas i python. Databas anslutnings strängen ser ut så här: (Ersätt servername, dbname, username och Password med din konfiguration):

```python
#Set up the SQL Azure connection
import pyodbc    
conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')
```

[Pandas](https://pandas.pydata.org/) -biblioteket i python innehåller en omfattande uppsättning data strukturer och data analys verktyg för data manipulation för python-programmering. Följande kod läser ett 0,1% exempel av data från en tabell i Azure SQL Database till en Pandas-data:

```python
import pandas as pd

# Query database and load the returned results in pandas data frame
data_frame = pd.read_sql('''select column1, column2... from <table_name> tablesample (0.1 percent)''', conn)
```

Nu kan du arbeta med exempel data i Pandas data-ram. 

### <a name="connecting-to-azure-machine-learning"></a><a name="python-aml"></a>Ansluter till Azure Machine Learning
Du kan använda följande exempel kod för att spara de nedsamplade data till en fil och överföra dem till en Azure-blob. Data i blobben kan läsas direkt i ett Azure Machine Learning experiment med hjälp av modulen [Importera data][import-data] . Stegen är följande: 

1. Skriva data ramen Pandas till en lokal fil

    ```python
    dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
    ```

2. Ladda upp lokal fil till Azure-Blob

    ```python
    from azure.storage import BlobService
    import tables

    STORAGEACCOUNTNAME= <storage_account_name>
    LOCALFILENAME= <local_file_name>
    STORAGEACCOUNTKEY= <storage_account_key>
    CONTAINERNAME= <container_name>
    BLOBNAME= <blob_name>

    output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
    localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory

    try:

    #perform upload
    output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)

    except:            
        print ("Something went wrong with uploading blob:"+BLOBNAME)
    ```

3. Läs data från Azure Blob med Azure Machine Learning [Importera][import-data] datamodul som visas på följande skärm bild:

![läsar BLOB][2]

## <a name="the-team-data-science-process-in-action-example"></a>Team data science-processen i åtgärds exempel
Om du vill gå igenom ett exempel på en grupp data vetenskaps process a med en offentlig data uppsättning, se [team data science process i praktiken: använda SQL Server](sql-walkthrough.md).

[1]: ./media/sample-sql-server-virtual-machine/reader_database.png
[2]: ./media/sample-sql-server-virtual-machine/reader_blob.png

[import-data]: /azure/machine-learning/studio-module-reference/import-data