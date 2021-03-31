---
title: Utforska data i SQL Server virtuell dator – team data science process
description: Utforska data som lagras i en SQL Server VM på Azure med hjälp av SQL eller ett programmeringsspråk som python.
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
ms.openlocfilehash: 33b55afb7796b197f7130ec9288abb01cc115651
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "86085658"
---
# <a name="explore-data-in-sql-server-virtual-machine-on-azure"></a>Utforska data i en virtuell dator med SQL Server på Azure

Den här artikeln beskriver hur du utforskar data som lagras i en SQL Server VM på Azure. Använd SQL eller python för att granska data.

Den här uppgiften är ett steg i [processen för team data vetenskap](overview.md).

> [!NOTE]
> SQL-uttrycken i det här dokumentet förutsätter att data finns i SQL Server. Om så inte är fallet kan du läsa mer i Cloud data science process Map för att lära dig hur du flyttar dina data till SQL Server.
> 
> 

## <a name="explore-sql-data-with-sql-scripts"></a><a name="sql-dataexploration"></a>Utforska SQL-data med SQL-skript
Här följer några exempel på SQL-skript som kan användas för att utforska data lager i SQL Server.

1. Få antalet observationer per dag
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. Hämta nivåerna i en kategoriska-kolumn
   
    `select  distinct <column_name> from <databasename>`
3. Hämta antalet nivåer i kombination av två kategoriska-kolumner 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. Hämta fördelningen för numeriska kolumner
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

> [!NOTE]
> I ett praktiskt exempel kan du använda [NYC taxi-datauppsättningen](https://www.andresmh.com/nyctaxitrips/) och REFERERA till IPNB- [NYC data datatransformering med hjälp av IPython Notebook och SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) för en slut punkt till slut punkt.
> 
> 

## <a name="explore-sql-data-with-python"></a><a name="python"></a>Utforska SQL-data med python
Använda python för att utforska data och generera funktioner när data är i SQL Server liknar bearbetning av data i Azure Blob med hjälp av python, enligt beskrivningen i [bearbeta Azure blob-data i din data vetenskaps miljö](data-blob.md). Läs in data från databasen till en Pandas-DataFrame och sedan kan du bearbeta dem ytterligare. Vi dokumenterar processen för att ansluta till databasen och läsa in data i DataFrame i det här avsnittet.

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

Nu kan du arbeta med Pandas-DataFrame som beskrivs i artikeln [bearbeta Azure blob-data i din data vetenskaps miljö](data-blob.md).

## <a name="the-team-data-science-process-in-action-example"></a>Team data science-processen i åtgärds exempel
Ett exempel på en fullständig genom gång av Cortana Analytics-processen med hjälp av en offentlig data uppsättning finns [i processen team data science i praktiken: använda SQL Server](sql-walkthrough.md).

