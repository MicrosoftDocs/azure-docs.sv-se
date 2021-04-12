---
title: Utforska data i en SQL Server virtuell dator – team data science process
description: Utforska + bearbeta data och generera funktioner med python eller SQL i en SQL Server virtuell dator på Azure.
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
ms.openlocfilehash: 8be878cf40967356d68e9be0765e898c81b5ba0a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "93314621"
---
# <a name="process-data-in-sql-server-virtual-machine-on-azure"></a><a name="heading"></a>Bearbeta data i en virtuell dator med SQL Server på Azure
Det här dokumentet beskriver hur du utforskar data och genererar funktioner för data som lagras i en SQL Server VM på Azure. Det här målet kan utföras av data datatransformering med SQL eller genom att använda ett programmeringsspråk som python.

> [!NOTE]
> SQL-uttrycken i det här dokumentet förutsätter att data finns i SQL Server. Om så inte är fallet kan du läsa mer i Cloud data science process Map för att lära dig hur du flyttar dina data till SQL Server.
> 
> 

## <a name="using-sql"></a><a name="SQL"></a>Använda SQL
Vi beskriver följande data datatransformering uppgifter i det här avsnittet med hjälp av SQL:

1. [Data utforskning](#sql-dataexploration)
2. [Generering av funktioner](#sql-featuregen)

### <a name="data-exploration"></a><a name="sql-dataexploration"></a>Data utforskning
Här följer några exempel på SQL-skript som kan användas för att utforska data lager i SQL Server.

> [!NOTE]
> I ett praktiskt exempel kan du använda [NYC taxi-datauppsättningen](https://www.andresmh.com/nyctaxitrips/) och REFERERA till IPNB- [NYC data datatransformering med hjälp av IPython Notebook och SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) för en slut punkt till slut punkt.
> 
> 

1. Få antalet observationer per dag
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. Hämta nivåerna i en kategoriska-kolumn
   
    `select  distinct <column_name> from <databasename>`
3. Hämta antalet nivåer i kombination av två kategoriska-kolumner 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. Hämta fördelningen för numeriska kolumner
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

### <a name="feature-generation"></a><a name="sql-featuregen"></a>Generering av funktioner
I det här avsnittet beskrivs hur du genererar funktioner med hjälp av SQL:  

1. [Antal baserade funktions generationer](#sql-countfeature)
2. [Generering av diskretisering-funktioner](#sql-binningfeature)
3. [Distribuera funktionerna från en enda kolumn](#sql-featurerollout)

> [!NOTE]
> När du har genererat ytterligare funktioner kan du antingen lägga till dem som kolumner i den befintliga tabellen eller skapa en ny tabell med de ytterligare funktionerna och den primära nyckeln som kan kopplas till den ursprungliga tabellen. 
> 
> 

### <a name="count-based-feature-generation"></a><a name="sql-countfeature"></a>Antal baserade funktions generationer
I följande exempel demonstreras två sätt att skapa Count-funktioner. Den första metoden använder villkorlig summa och den andra metoden använder WHERE-satsen. De här resultaten kan sedan kopplas till den ursprungliga tabellen (med primär nyckel kolumner) för att räkna funktioner tillsammans med ursprungliga data.

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

Här är en kort introduktion till plats data för latitud/longitud (omursprungs från StackOverflow [hur du mäter precisionen för latitud och longitud?](https://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude)). Den här vägledningen är användbar för att förstå innan du inkluderar platsen som en eller flera funktioner:

* Tecknet visar om vi är norr eller syd, öst eller väst i hela världen.
* En hundratals siffra som inte är noll anger att vi använder longitud, inte Latitude!
* Med den här siffran får du en position på cirka 1 000 kilo meter. Den ger oss värdefull information om vad kontinent eller hav vi är på.
* Enhets siffran (en decimal grad) ger en position på upp till 111 kilo meter (60 nautiska mil, cirka 69 miles). Det kan berätta ungefär vilket tillstånd, land eller region du befinner dig i.
* Den första decimalen är värd för upp till 11,1 km: det kan särskilja positionen för en stor stad från en intilliggande stor stad.
* Den andra decimalen är värd för upp till 1,1 km: den kan separeras av en Village från nästa.
* Den tredje decimalen är värd för upp till 110 m: det kan identifiera ett stort jordbruks fält eller institutionellt campus.
* Den fjärde decimalen är värt upp till 11 m: det kan identifiera ett skifte på marken. Den är jämförbar med den typiska precisionen för en icke-korrigerad GPS-enhet utan störningar.
* Den femte decimalen är värd för upp till 1,1 m: det skiljer träd från varandra. Noggrannhet på den här nivån med kommersiella GPS-enheter kan bara uppnås med differentiell korrigering.
* Den sjätte decimalen är värd för upp till 0,11 m: du kan använda det för att utforma strukturer i detalj, för att utforma landskap, skapa vägar. Den bör vara större än tillräckligt stor för att spåra förflyttningar av Glaciers och floder. Detta kan uppnås genom att vidta painstaking-åtgärder med GPS, till exempel Differentiellt korrigerad GPS.

Plats informationen kan bearbetas på följande sätt, separera information om region, plats och ort. Du kan också anropa en REST-slutpunkt, till exempel Bing Maps-API: n, som finns på [hitta en plats genom att peka](/bingmaps/rest-services/locations/find-a-location-by-point) på Hämta region/distrikts information.

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
> Du kan infoga posterna program mässigt med ditt eget språk. Du kan behöva infoga data i segment för att förbättra Skriv effektiviteten (ett exempel på hur du gör detta med hjälp av pyodbc finns i [ett HelloWorld-exempel för att få åtkomst till SQLServer med python](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python)). Ett annat alternativ är att infoga data i databasen med hjälp av [BCP-verktyget](/sql/tools/bcp-utility).
> 
> 

### <a name="connecting-to-azure-machine-learning"></a><a name="sql-aml"></a>Ansluter till Azure Machine Learning
Den nyligen genererade funktionen kan läggas till som en kolumn i en befintlig tabell eller lagras i en ny tabell och kopplas till den ursprungliga tabellen för Machine Learning. Funktioner kan genereras eller nås om de redan har skapats, med hjälp av modulen [Importera data][import-data] i Azure Machine Learning som visas nedan:

![azureml-läsare][1] 

## <a name="using-a-programming-language-like-python"></a><a name="python"></a>Använda ett programmeringsspråk som python
Använda python för att utforska data och generera funktioner när data är i SQL Server liknar bearbetning av data i Azure Blob med hjälp av python som dokumenterade i [bearbeta Azure blob-data i din data vetenskaps miljö](data-blob.md). Läs in data från databasen till en Pandas data ram för mer bearbetning. Vi dokumenterar processen för att ansluta till databasen och läsa in data till data ramen i det här avsnittet.

Följande anslutnings sträng format kan användas för att ansluta till en SQL Server databas från python med hjälp av pyodbc (Ersätt servername, dbname, username och Password med dina egna värden):

```python
#Set up the SQL Azure connection
import pyodbc    
conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')
```

[Pandas-biblioteket](https://pandas.pydata.org/) i python innehåller en omfattande uppsättning data strukturer och data analys verktyg för data manipulation för python-programmering. Koden nedan läser de resultat som returneras från en SQL Server-databas till en Pandas data ram:

```python
# Query database and load the returned results in pandas data frame
data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)
```

Nu kan du arbeta med data ramen Pandas som beskrivs i artikeln [bearbeta Azure blob-data i din data science-miljö](data-blob.md).

## <a name="azure-data-science-in-action-example"></a>Azure Data Science i åtgärds exempel
Ett exempel på en fullständig genom gång av Azure Data Science-processen med hjälp av en offentlig data uppsättning finns [i Azure Data Science process](sql-walkthrough.md).

[1]: ./media/sql-server-virtual-machine/reader_db_featurizedinput.png


<!-- Module References -->
[import-data]: /azure/machine-learning/studio-module-reference/import-data