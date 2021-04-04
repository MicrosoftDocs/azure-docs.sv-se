---
title: Skapa funktioner i SQL Server med SQL och python-team data science process
description: Generera funktioner för data som lagras i en SQL Server VM på Azure med SQL och python-del av team data science-processen.
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
ms.openlocfilehash: 3c20bf1c5856276c4c7ee0e37ed4ef2120d1d93d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "93322037"
---
# <a name="create-features-for-data-in-sql-server-using-sql-and-python"></a>Skapa funktioner för data i SQL Server med SQL och Python
Det här dokumentet visar hur du genererar funktioner för data som lagras i en SQL Server VM på Azure som hjälper algoritmer att lära sig effektivare från data. Du kan använda SQL eller ett programmeringsspråk som python för att utföra den här uppgiften. Båda metoderna visas här.

Den här uppgiften är ett steg i [TDSP (Team data science process)](./index.yml).

> [!NOTE]
> Ett praktiskt exempel är att du kan se [NYC taxi-datauppsättningen](https://www.andresmh.com/nyctaxitrips/) och REFERERA till IPNB- [NYC data datatransformering med hjälp av IPython Notebook och SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) för en genom gång från slut punkt till slut punkt.
> 
> 

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du har:

* Skapat ett Azure Storage-konto. Om du behöver instruktioner, se [skapa ett Azure Storage konto](../../storage/common/storage-account-create.md)
* Lagrade dina data i SQL Server. Om du inte har gjort det, se [Flytta data till en Azure SQL Database för Azure Machine Learning](move-sql-azure.md) för instruktioner om hur du flyttar data dit.

## <a name="feature-generation-with-sql"></a><a name="sql-featuregen"></a>Generering av funktioner med SQL
I det här avsnittet beskrivs hur du genererar funktioner med hjälp av SQL:  

* [Antal baserade funktions generationer](#sql-countfeature)
* [Generering av diskretisering-funktioner](#sql-binningfeature)
* [Distribuera funktionerna från en enda kolumn](#sql-featurerollout)

> [!NOTE]
> När du har genererat ytterligare funktioner kan du antingen lägga till dem som kolumner i den befintliga tabellen eller skapa en ny tabell med de ytterligare funktionerna och den primära nyckeln som kan kopplas till den ursprungliga tabellen.
> 
> 

### <a name="count-based-feature-generation"></a><a name="sql-countfeature"></a>Antal baserade funktions generationer
Det här dokumentet demonstrerar två sätt att skapa antal-funktioner. Den första metoden använder villkorlig summa och den andra metoden använder WHERE-satsen. De här nya funktionerna kan sedan anslutas till den ursprungliga tabellen (med primär nyckel kolumner) för att räkna funktioner tillsammans med ursprungliga data.

```sql
select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3>

select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename>
where <column_name3> = '<some_value>' group by <column_name1>,<column_name2>
```

### <a name="binning-feature-generation"></a><a name="sql-binningfeature"></a>Generering av diskretisering-funktioner
I följande exempel visas hur du skapar diskretiseras-funktioner med diskretisering (med fem lager platser) en numerisk kolumn som kan användas som en funktion i stället:

```sql
SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>
```


### <a name="rolling-out-the-features-from-a-single-column"></a><a name="sql-featurerollout"></a>Distribuera funktionerna från en enda kolumn
I det här avsnittet visar vi hur du distribuerar en enskild kolumn i en tabell för att generera ytterligare funktioner. Exemplet förutsätter att det finns en latitud-eller longitud-kolumn i den tabell som du försöker generera funktioner från.

Här är en kort introduktion till plats data för latitud/longitud (omursprungs från StackOverflow `https://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude` ). Här är några användbara saker du behöver veta om plats data innan du skapar funktioner från fältet:

* Tecknet anger om vi är norr eller syd, öst eller väst i hela världen.
* En hundratals siffra som inte är noll anger longitud, inte latitud.
* Med den här siffran får du en position på cirka 1 000 kilo meter. Den ger värdefull information om vad kontinent eller hav vi är på.
* Enhets siffran (en decimal grad) ger en position på upp till 111 kilo meter (60 nautiska mil, cirka 69 miles). Det indikerar, ungefär, vilket stort tillstånd eller land/region som vi befinner sig i.
* Den första decimalen är värd för upp till 11,1 km: det kan särskilja positionen för en stor stad från en intilliggande stor stad.
* Den andra decimalen är värd för upp till 1,1 km: den kan separeras av en Village från nästa.
* Den tredje decimalen är värd för upp till 110 m: det kan identifiera ett stort jordbruks fält eller institutionellt campus.
* Den fjärde decimalen är värt upp till 11 m: det kan identifiera ett skifte på marken. Den är jämförbar med den typiska precisionen för en icke-korrigerad GPS-enhet utan störningar.
* Den femte decimalen är värd för upp till 1,1 m: det skiljer träd från varandra. Noggrannhet på den här nivån med kommersiella GPS-enheter kan bara uppnås med differentiell korrigering.
* Den sjätte decimalen är värd för upp till 0,11 m: du kan använda den här nivån för att utforma strukturer i detalj, för att utforma landskap, bygga vägar. Den bör vara större än tillräckligt stor för att spåra förflyttningar av Glaciers och floder. Det här målet kan uppnås genom att vidta painstaking-åtgärder med GPS, till exempel Differentiellt korrigerat GPS.

Plats informationen kan bearbetas genom att avgränsa information om region, plats och ort. En gång kan också anropa en REST-slutpunkt, till exempel Bing Maps API (se `https://msdn.microsoft.com/library/ff701710.aspx` för att hämta region/distrikts information).

```sql
select
    <location_columnname>
    ,round(<location_columnname>,0) as l1        
    ,l2=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 1 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),1,1) else '0' end     
    ,l3=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 2 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),2,1) else '0' end     
    ,l4=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 3 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),3,1) else '0' end     
    ,l5=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 4 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),4,1) else '0' end     
    ,l6=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 5 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),5,1) else '0' end     
    ,l7=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 6 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),6,1) else '0' end     
from <tablename>
```

Dessa platsbaserade funktioner kan användas ytterligare för att generera fler antals funktioner enligt beskrivningen ovan.

> [!TIP]
> Du kan infoga posterna program mässigt med ditt eget språk. Du kan behöva infoga data i segment för att förbättra Skriv effektiviteten. [Här är ett exempel på hur du gör detta med hjälp av pyodbc](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python).
> Ett annat alternativ är att infoga data i databasen med hjälp av [BCP-verktyget](/sql/tools/bcp-utility)
> 
> 

### <a name="connecting-to-azure-machine-learning"></a><a name="sql-aml"></a>Ansluter till Azure Machine Learning
Den nyligen genererade funktionen kan läggas till som en kolumn i en befintlig tabell eller lagras i en ny tabell och kopplas till den ursprungliga tabellen för Machine Learning. Funktioner kan genereras eller nås om de redan har skapats, med hjälp av modulen [Importera data](/azure/machine-learning/studio-module-reference/import-data) i Azure ml som visas nedan:

![Azure ML-läsare](./media/sql-server-virtual-machine/reader_db_featurizedinput.png)

## <a name="using-a-programming-language-like-python"></a><a name="python"></a>Använda ett programmeringsspråk som python
Att använda python för att generera funktioner när data är i SQL Server liknar att bearbeta data i Azure Blob med hjälp av python. För jämförelse finns mer information [i bearbeta Azure blob-data i din data science-miljö](data-blob.md). Läs in data från databasen till en data ram i Pandas för att bearbeta den ytterligare. Processen för att ansluta till databasen och läsa in data i data ramen beskrivs i det här avsnittet.

Följande anslutnings sträng format kan användas för att ansluta till en SQL Server databas från python med hjälp av pyodbc (Ersätt servername, dbname, username och Password med dina egna värden):

```python
#Set up the SQL Azure connection
import pyodbc
conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')
```

[Pandas-biblioteket](https://pandas.pydata.org/) i python innehåller en omfattande uppsättning data strukturer och data analys verktyg för data manipulation för python-programmering. Följande kod läser de resultat som returneras från en SQL Server-databas till en Pandas data ram:

```python
# Query database and load the returned results in pandas data frame
data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)
```

Nu kan du arbeta med data ramen Pandas som beskrivs i avsnittet [skapa funktioner för Azure Blob Storage-data med hjälp av Panda](./explore-data-blob.md).