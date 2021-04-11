---
title: Analysera schema med matriser och kapslade strukturer
description: Analysera matriser och kapslade strukturer med Apache Spark och SQL
services: synapse-analytics
author: Rodrigossz
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: spark
ms.date: 06/15/2020
ms.author: rosouz
ms.reviewer: jrasnick
ms.openlocfilehash: d4004887a4806a5b8e50429031fb204f4eec5000
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105627973"
---
# <a name="analyze-complex-data-types-in-azure-synapse-analytics"></a>Analysera komplexa data typer i Azure Synapse Analytics

Den här artikeln är relevant för Parquet-filer och behållare i [Azure Synapse-länken för Azure Cosmos DB](.\synapse-link\how-to-connect-synapse-link-cosmos-db.md). Du kan använda Spark eller SQL för att läsa eller transformera data med komplexa scheman, till exempel matriser eller kapslade strukturer. Följande exempel har slutförts med ett enda dokument, men det kan enkelt skala till miljarder dokument med Spark eller SQL. Koden som ingår i den här artikeln använder PySpark (python).

## <a name="use-case"></a>Användningsfall

Komplexa data typer är allt vanligare och representerar en utmaning för data tekniker. Analys av nästlat schema och matriser kan omfatta tids krävande och komplexa SQL-frågor. Dessutom kan det vara svårt att byta namn på eller omvandla data typen kapslade kolumner. När du arbetar med djupt kapslade objekt kan du också stöta på prestanda problem.

Data tekniker måste förstå hur man effektivt bearbetar komplexa data typer och gör dem lättillgängliga för alla. I följande exempel använder du spark i Azure Synapse Analytics för att läsa och transformera objekt till en platt struktur genom data ramar. Du använder den serverbaserade modellen av SQL i Azure Synapse Analytics för att fråga efter objekt direkt och returnera resultaten som en vanlig tabell.

## <a name="what-are-arrays-and-nested-structures"></a>Vad är matriser och kapslade strukturer?

Följande objekt kommer från [Application Insights](../azure-monitor/app/app-insights-overview.md). I det här objektet finns kapslade strukturer och matriser som innehåller kapslade strukturer.

```json
{
    "id": "66532691-ab20-11ea-8b1d-936b3ec64e54",
    "context": {
        "data": {
            "eventTime": "2020-06-10T13:43:34.553Z",
            "samplingRate": "100.0",
            "isSynthetic": "false"
        },
        "session": {
            "isFirst": "false",
            "id": "38619c14-7a23-4687-8268-95862c5326b1"
        },
        "custom": {
            "dimensions": [
                {
                    "customerInfo": {
                        "ProfileType": "ExpertUser",
                        "RoomName": "",
                        "CustomerName": "diamond",
                        "UserName": "XXXX@yahoo.com"
                    }
                },
                {
                    "customerInfo": {
                        "ProfileType": "Novice",
                        "RoomName": "",
                        "CustomerName": "topaz",
                        "UserName": "XXXX@outlook.com"
                    }
                }
            ]
        }
    }
}
```

### <a name="schema-example-of-arrays-and-nested-structures"></a>Schema exempel på matriser och kapslade strukturer
När du skriver ut schemat för objektets data ram (som kallas **DF**) med kommandot `df.printschema` visas följande åter givning:

* Gult representerar kapslade strukturer.
* Grönt representerar en matris med två element.

[![Koda med gul och grön markering, visar schema ursprung](./media/how-to-complex-schema/schema-origin.png)](./media/how-to-complex-schema/schema-origin.png#lightbox)

`_rid`, `_ts` och `_etag` har lagts till i systemet när dokumentet matades in i Azure Cosmos DB transaktions lager.

Föregående data ramar räknas bara för 5 kolumner och 1 rad. Efter omvandling har den granskade data ramen 13 kolumner och 2 rader i tabell format.

## <a name="flatten-nested-structures-and-explode-arrays"></a>Förenkla inkapslade strukturer och expandera matriser

Med Spark i Azure Synapse Analytics är det enkelt att transformera kapslade strukturer till kolumner och mat ris element till flera rader. Använd följande steg för implementeringen.

[![Flödes schema som visar steg för Spark-omvandlingar](./media/how-to-complex-schema/spark-transform-steps.png)](./media/how-to-complex-schema/spark-transform-steps.png#lightbox)

### <a name="define-a-function-to-flatten-the-nested-schema"></a>Definiera en funktion för att förenkla det kapslade schemat

Du kan använda den här funktionen utan att ändra. Skapa en cell i en [PySpark-anteckningsbok](quickstart-apache-spark-notebook.md) med följande funktion:

```python
from pyspark.sql.functions import col

def flatten_df(nested_df):
    stack = [((), nested_df)]
    columns = []

    while len(stack) > 0:
        parents, df = stack.pop()

        flat_cols = [
            col(".".join(parents + (c[0],))).alias("_".join(parents + (c[0],)))
            for c in df.dtypes
            if c[1][:6] != "struct"
        ]

        nested_cols = [
            c[0]
            for c in df.dtypes
            if c[1][:6] == "struct"
        ]

        columns.extend(flat_cols)

        for nested_col in nested_cols:
            projected_df = df.select(nested_col + ".*")
            stack.append((parents + (nested_col,), projected_df))

    return nested_df.select(columns)
```

### <a name="use-the-function-to-flatten-the-nested-schema"></a>Använd funktionen för att förenkla det kapslade schemat

I det här steget ska du förenkla det kapslade schemat för data ramen (**DF**) till en ny data ram ( `df_flat` ):

```python
from pyspark.sql.types import StringType, StructField, StructType
df_flat = flatten_df(df)
display(df_flat.limit(10))
```

Funktionen display ska returnera 10 kolumner och 1 rad. Matrisen och dess kapslade element finns fortfarande där.

### <a name="transform-the-array"></a>Transformera matrisen

Här transformerar du matrisen, `context_custom_dimensions` i data fältet, i `df_flat` en ny data ram `df_flat_explode` . I följande kod definierar du också vilken kolumn du vill välja:

```python
from pyspark.sql.functions import explode
from pyspark.sql.functions import flatten
from pyspark.sql.functions import arrays_zip
df_flat_explode = df_flat.select("_rid","_ts","id","_etag",explode(df_flat.context_custom_dimensions),"context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")\
.select("_rid","_ts","id","_etag","col.*","context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")
display(df_flat_explode.limit(10))

```

Funktionen display ska returnera 10 kolumner och 2 rader. Nästa steg är att förenkla inkapslade scheman med funktionen som definieras i steg 1.

### <a name="use-the-function-to-flatten-the-nested-schema"></a>Använd funktionen för att förenkla det kapslade schemat

Slutligen använder du funktionen för att förenkla det kapslade schemat i data fältet `df_flat_explode` till en ny data ram `df_flat_explode_flat` :
```python
df_flat_explode_flat = flatten_df(df_flat_explode)
display(df_flat_explode_flat.limit(10))
```

Funktionen display ska Visa 13 kolumner och 2 rader.

`printSchema`Data ramens funktion `df_flat_explode_flat` returnerar följande resultat:

[![Kod som visar det slutliga schemat](./media/how-to-complex-schema/schema-final.png)](./media/how-to-complex-schema/schema-final.png#lightbox)

## <a name="read-arrays-and-nested-structures-directly"></a>Läs matriser och kapslade strukturer direkt

Med den serverbaserade modellen av SQL kan du fråga efter och skapa vyer och tabeller över sådana objekt.

För det första, beroende på hur data har lagrats, ska användarna använda följande taxonomi. Allting som visas i versaler är bara för ditt användnings fall:

| Bulk | Format |
| ------ | ------ |
| 'https://ACCOUNTNAME.dfs.core.windows.net/FILESYSTEM/PATH/FINENAME.parquet' |' Parquet ' (ADLSg2)|
| N'endpoint = https://ACCOUNTNAME.documents-staging.windows-ppe.net:443/ ; konto = ACCOUNTNAME; databas = databasename; samling = samlings namn; region = REGIONTOQUERY ", Secret =" YOURSECRET " |' CosmosDB ' (Azure Synapse-länk)|


Ersätt varje fält enligt följande:
* "Din samling ovan" är anslutnings strängen för den data källa som du ansluter till.
* "Din typ ovanför" är det format som du använder för att ansluta till källan.

```sql
select *
FROM
openrowset(
    BULK 'YOUR BULK ABOVE',
    FORMAT='YOUR TYPE ABOVE'
)
with (id varchar(50),
        contextdataeventTime varchar(50) '$.context.data.eventTime',
        contextdatasamplingRate varchar(50) '$.context.data.samplingRate',
        contextdataisSynthetic varchar(50) '$.context.data.isSynthetic',
        contextsessionisFirst varchar(50) '$.context.session.isFirst',
        contextsessionid varchar(50) '$.context.session.id',
        contextcustomdimensions varchar(max) '$.context.custom.dimensions'
) as q 
cross apply openjson (contextcustomdimensions) 
with ( ProfileType varchar(50) '$.customerInfo.ProfileType',
            RoomName varchar(50) '$.customerInfo.RoomName',
            CustomerName varchar(50) '$.customerInfo.CustomerName',
            UserName varchar(50) '$.customerInfo.UserName'
    )
```

Det finns två olika typer av åtgärder:

- Den första åtgärds typen anges i följande kodrad, som definierar den kolumn `contextdataeventTime` som kallas som refererar till det kapslade elementet `Context.Data.eventTime` . 
  ```sql
  contextdataeventTime varchar(50) '$.context.data.eventTime'
  ```

  Den här raden definierar den kolumn `contextdataeventTime` som kallas som refererar till det kapslade elementet `Context>Data>eventTime` .

- Den andra åtgärds typen använder `cross apply` för att skapa nya rader för varje element under matrisen. Därefter definieras varje kapslat objekt. 
  ```sql
  cross apply openjson (contextcustomdimensions) 
  with ( ProfileType varchar(50) '$.customerInfo.ProfileType', 
  ```

  Om matrisen hade 5 element med 4 kapslade strukturer, returnerar den serverbaserade modellen av SQL 5 rader och 4 kolumner. Den serverbaserade modellen av SQL kan fråga på plats, mappa matrisen i 2 rader och Visa alla kapslade strukturer i kolumner.

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du frågar Synapse-länk för Azure Cosmos DB med Spark](./synapse-link/how-to-query-analytical-store-spark.md)
* [Efterfråga kapslade Parquet-typer](./sql/query-parquet-nested-types.md)