---
title: Migrera en-till-få relationsdata till sql-API Azure Cosmos DB
description: Lär dig hur du hanterar komplex datamigrering för en-till-få relationer till SQL API
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 12/12/2019
ms.author: thvankra
ms.openlocfilehash: 7dbb162749e0a2f84140b8e9394934198d096eac
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589626"
---
# <a name="migrate-one-to-few-relational-data-into-azure-cosmos-db-sql-api-account"></a>Migrera en-till-få relationsdata till ett SQL API Azure Cosmos DB konto
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

För att kunna migrera från en relationsdatabas till Azure Cosmos DB SQL API kan det vara nödvändigt att göra ändringar i datamodellen för optimering.

En vanlig transformering är att avmalisera data genom att bädda in relaterade undernät i ett JSON-dokument. Här tittar vi på några alternativ för detta med hjälp av Azure Data Factory eller Azure Databricks. Allmänna riktlinjer för datamodellering för Cosmos DB finns i [Datamodellering i Azure Cosmos DB](modeling-data.md).  

## <a name="example-scenario"></a>Exempel på ett scenario

Anta att vi har följande två tabeller i vår SQL-databas, Orders och OrderDetails.


:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/orders.png" alt-text="Skärmbild som visar tabellerna Orders och OrderDetails i SQL-databasen." border="false" :::

Vi vill kombinera den här en-till-få-relationen i ett JSON-dokument under migreringen. För att göra detta kan vi skapa en T-SQL-fråga med hjälp av "FOR JSON" enligt nedan:

```sql
SELECT
  o.OrderID,
  o.OrderDate,
  o.FirstName,
  o.LastName,
  o.Address,
  o.City,
  o.State,
  o.PostalCode,
  o.Country,
  o.Phone,
  o.Total,
  (select OrderDetailId, ProductId, UnitPrice, Quantity from OrderDetails od where od.OrderId = o.OrderId for json auto) as OrderDetails
FROM Orders o;
```

Resultatet av den här frågan skulle se ut så här: 

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/for-json-query-result.png" alt-text="Orderinformation" lightbox="./media/migrate-relational-to-cosmos-sql-api/for-json-query-result.png":::

Vi rekommenderar att du använder en enda Azure Data Factory-kopieringsaktivitet (ADF) för att fråga SQL-data som källa och skriva utdata direkt till Azure Cosmos DB mottagare som rätt JSON-objekt. För närvarande går det inte att utföra den nödvändiga JSON-transformeringen i en kopieringsaktivitet. Om vi försöker kopiera resultatet av ovanstående fråga till en Azure Cosmos DB SQL API-container ser vi fältet OrderDetails som en strängegenskap för vårt dokument, i stället för den förväntade JSON-matrisen.

Vi kan komma runt den här aktuella begränsningen på något av följande sätt:

* **Använd Azure Data Factory med två kopieringsaktiviteter:** 
  1. Hämta JSON-formaterade data från SQL till en textfil på en mellanliggande bloblagringsplats och 
  2. Läs in data från JSON-textfilen till en container i Azure Cosmos DB.

* **Använd Azure Databricks att läsa från SQL och skriva till Azure Cosmos DB** – vi kommer att presentera två alternativ här.


Nu ska vi titta närmare på dessa metoder:

## <a name="azure-data-factory"></a>Azure Data Factory

Vi kan inte bädda in OrderDetails som en JSON-matris i Cosmos DB-dokumentet, men vi kan komma runt problemet med hjälp av två separata kopieringsaktiviteter.

### <a name="copy-activity-1-sqljsontoblobtext"></a>Kopiera aktivitetslogg #1: SqlJsonToBlobText

För källdata använder vi en SQL-fråga för att hämta resultatuppsättningen som en enda kolumn med ett JSON-objekt (som representerar Order) per rad med hjälp av funktionerna SQL Server OPENJSON och FOR JSON PATH:

```sql
SELECT [value] FROM OPENJSON(
  (SELECT
    id = o.OrderID,
    o.OrderDate,
    o.FirstName,
    o.LastName,
    o.Address,
    o.City,
    o.State,
    o.PostalCode,
    o.Country,
    o.Phone,
    o.Total,
    (select OrderDetailId, ProductId, UnitPrice, Quantity from OrderDetails od where od.OrderId = o.OrderId for json auto) as OrderDetails
   FROM Orders o FOR JSON PATH)
)
```

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf1.png" alt-text="ADF-kopia":::


Som mottagare för kopieringsaktiviteten SqlJsonToBlobText väljer vi "Avgränsad text" och pekar den till en specifik mapp i Azure Blob Storage med ett dynamiskt genererat unikt filnamn (till exempel " @concat (pipeline(). RunId,'.json').
Eftersom vår textfil egentligen inte är "avgränsad" och vi inte vill att den ska parsas i separata kolumner med kommatecken och vill bevara dubbla citattecken ("), anger vi "Kolumnavgränsare" till en flik ("\t") – eller något annat tecken som inte förekommer i data – och "Citattecken" till "Inget citattecken".

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf2.png" alt-text="Skärmbild som visar inställningarna för kolumnavgränsare och citattecken.":::

### <a name="copy-activity-2-blobjsontocosmos"></a>Kopiera aktivitetsloggar #2: BlobJsonToCosmos

Sedan ändrar vi vår ADF-pipeline genom att lägga till den andra kopieringsaktiviteten som söker i Azure Blob Storage efter textfilen som skapades av den första aktiviteten. Den bearbetar den som "JSON"-källa för att infoga Cosmos DB en mottagare som ett dokument per JSON-rad som finns i textfilen.

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf3.png" alt-text="Skärmbild som visar fälten JSON-källfil och Filsökväg.":::

Alternativt kan vi också lägga till en "Ta bort"-aktivitet i pipelinen så att den tar bort alla tidigare filer som finns kvar i mappen /Orders/före varje körning. Vår ADF-pipeline ser nu ut ungefär så här:

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf4.png" alt-text="Skärmbild som visar aktiviteten Ta bort.":::

När vi har utlöst pipelinen ovan ser vi en fil som skapats i vår mellanliggande Azure Blob Storage som innehåller ett JSON-objekt per rad:

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf5.png" alt-text="Skärmbild som visar den skapade filen som innehåller JSON-objekten.":::

Vi ser även Beställningar-dokument med korrekt inbäddade OrderDetails infogade i vår Cosmos DB samling:

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf6.png" alt-text="Skärmbild som visar orderinformationen som en del av det Cosmos DB dokumentet":::


## <a name="azure-databricks"></a>Azure Databricks

Vi kan också använda Spark [i Azure Databricks](https://azure.microsoft.com/services/databricks/) för att kopiera data från vår SQL Database-källa till Azure Cosmos DB-målet utan att skapa mellanliggande text-/JSON-filer i Azure Blob Storage. 

> [!NOTE]
> För tydlighetens och enkelhetens skull innehåller kodfragmenten nedan uttryckligen infogade dummydatabaslösenord, men du bör alltid använda Azure Databricks hemligheter.
>

Först skapar och kopplar vi nödvändiga [SQL-anslutningsappar och](/connectors/sql/) [Azure Cosmos DB-anslutningsbibliotek](https://docs.databricks.com/data/data-sources/azure/cosmosdb-connector.html) till vårt Azure Databricks kluster. Starta om klustret för att kontrollera att biblioteken har lästs in.

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks1.png" alt-text="Skärmbild som visar var du skapar och kopplar nödvändiga SQL-anslutningsappar och Azure Cosmos DB-anslutningsbibliotek till vårt Azure Databricks kluster.":::

Därefter presenterar vi två exempel för Scala och Python. 

### <a name="scala"></a>Scala
Här hämtar vi resultatet av SQL-frågan med "FOR JSON"-utdata till en DataFrame:

```scala
// Connect to Azure SQL /connectors/sql/
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._
val configSql = Config(Map(
  "url"          -> "xxxx.database.windows.net",
  "databaseName" -> "xxxx",
  "queryCustom"  -> "SELECT o.OrderID, o.OrderDate, o.FirstName, o.LastName, o.Address, o.City, o.State, o.PostalCode, o.Country, o.Phone, o.Total, (SELECT OrderDetailId, ProductId, UnitPrice, Quantity FROM OrderDetails od WHERE od.OrderId = o.OrderId FOR JSON AUTO) as OrderDetails FROM Orders o",
  "user"         -> "xxxx", 
  "password"     -> "xxxx" // NOTE: For clarity and simplicity, this example includes secrets explicitely as a string, but you should always use Databricks secrets
))
// Create DataFrame from Azure SQL query
val orders = sqlContext.read.sqlDB(configSql)
display(orders)
```

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks2.png" alt-text="Skärmbild som visar SQL-frågeutdata i en DataFrame.":::

Därefter ansluter vi till vår Cosmos DB databas och samling:

```scala
// Connect to Cosmos DB https://docs.databricks.com/data/data-sources/azure/cosmosdb-connector.html
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.CosmosDBSpark
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.apache.spark.sql.functions._
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.CosmosDBSpark
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.apache.spark.sql.functions._
val configMap = Map(
  "Endpoint" -> "https://xxxx.documents.azure.com:443/",
  // NOTE: For clarity and simplicity, this example includes secrets explicitely as a string, but you should always use Databricks secrets
  "Masterkey" -> "xxxx",
  "Database" -> "StoreDatabase",
  "Collection" -> "Orders")
val configCosmos = Config(configMap)
```

Slutligen definierar vi vårt schema och använder from_json för att tillämpa DataFrame innan du sparar det i CosmosDB-samlingen.

```scala
// Convert DataFrame to proper nested schema
import org.apache.spark.sql.types._
val orderDetailsSchema = ArrayType(StructType(Array(
    StructField("OrderDetailId",IntegerType,true),
    StructField("ProductId",IntegerType,true),
    StructField("UnitPrice",DoubleType,true),
    StructField("Quantity",IntegerType,true)
  )))
val ordersWithSchema = orders.select(
  col("OrderId").cast("string").as("id"),
  col("OrderDate").cast("string"),
  col("FirstName").cast("string"),
  col("LastName").cast("string"),
  col("Address").cast("string"),
  col("City").cast("string"),
  col("State").cast("string"),
  col("PostalCode").cast("string"),
  col("Country").cast("string"),
  col("Phone").cast("string"),
  col("Total").cast("double"),
  from_json(col("OrderDetails"), orderDetailsSchema).as("OrderDetails")
)
display(ordersWithSchema)
// Save nested data to Cosmos DB
CosmosDBSpark.save(ordersWithSchema, configCosmos)
```

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks3.png" alt-text="Skärmbild som visar rätt matris för att spara i en Cosmos DB samling.":::


### <a name="python"></a>Python

Alternativt kan du behöva köra JSON-transformationer i Spark (om källdatabasen inte stöder "FOR JSON" eller en liknande åtgärd), eller så kanske du vill använda parallella åtgärder för en mycket stor datauppsättning. Här presenterar vi ett PySpark-exempel. Börja med att konfigurera käll- och måldatabasanslutningarna i den första cellen:

```python
import uuid
import pyspark.sql.functions as F
from pyspark.sql.functions import col
from pyspark.sql.types import StringType,DateType,LongType,IntegerType,TimestampType
 
#JDBC connect details for SQL Server database
jdbcHostname = "jdbcHostname"
jdbcDatabase = "OrdersDB"
jdbcUsername = "jdbcUsername"
jdbcPassword = "jdbcPassword"
jdbcPort = "1433"
 
connectionProperties = {
  "user" : jdbcUsername,
  "password" : jdbcPassword,
  "driver" : "com.microsoft.sqlserver.jdbc.SQLServerDriver"
}
jdbcUrl = "jdbc:sqlserver://{0}:{1};database={2};user={3};password={4}".format(jdbcHostname, jdbcPort, jdbcDatabase, jdbcUsername, jdbcPassword)
 
#Connect details for Target Azure Cosmos DB SQL API account
writeConfig = {
    "Endpoint": "Endpoint",
    "Masterkey": "Masterkey",
    "Database": "OrdersDB",
    "Collection": "Orders",
    "Upsert": "true"
}
```

Sedan frågar vi källdatabasen (i det här fallet SQL Server) efter både order- och orderinformationsposterna och lägger resultatet i Spark Dataframes. Vi skapar också en lista som innehåller alla order-ID:er och en trådpool för parallella åtgärder:

```python
import json
import ast
import pyspark.sql.functions as F
import uuid
import numpy as np
import pandas as pd
from functools import reduce
from pyspark.sql import SQLContext
from pyspark.sql.types import *
from pyspark.sql import *
from pyspark.sql.functions import exp
from pyspark.sql.functions import col
from pyspark.sql.functions import lit
from pyspark.sql.functions import array
from pyspark.sql.types import *
from multiprocessing.pool import ThreadPool
 
#get all orders
orders = sqlContext.read.jdbc(url=jdbcUrl, table="orders", properties=connectionProperties)
 
#get all order details
orderdetails = sqlContext.read.jdbc(url=jdbcUrl, table="orderdetails", properties=connectionProperties)
 
#get all OrderId values to pass to map function 
orderids = orders.select('OrderId').collect()
 
#create thread pool big enough to process merge of details to orders in parallel
pool = ThreadPool(10)
```

Skapa sedan en funktion för att skriva Beställningar till SQL API-målsamlingen. Den här funktionen filtrerar all orderinformation för det angivna order-ID:t, konverterar dem till en JSON-matris och infogar matrisen i ett JSON-dokument som vi ska skriva till SQL API-målsamlingen för den ordningen:

```python
def writeOrder(orderid):
  #filter the order on current value passed from map function
  order = orders.filter(orders['OrderId'] == orderid[0])
  
  #set id to be a uuid
  order = order.withColumn("id", lit(str(uuid.uuid1())))
  
  #add details field to order dataframe
  order = order.withColumn("details", lit(''))
  
  #filter order details dataframe to get details we want to merge into the order document
  orderdetailsgroup = orderdetails.filter(orderdetails['OrderId'] == orderid[0])
  
  #convert dataframe to pandas
  orderpandas = order.toPandas()
  
  #convert the order dataframe to json and remove enclosing brackets
  orderjson = orderpandas.to_json(orient='records', force_ascii=False)
  orderjson = orderjson[1:-1] 
  
  #convert orderjson to a dictionaory so we can set the details element with order details later
  orderjsondata = json.loads(orderjson)
  
  
  #convert orderdetailsgroup dataframe to json, but only if details were returned from the earlier filter
  if (orderdetailsgroup.count() !=0):
    #convert orderdetailsgroup to pandas dataframe to work better with json
    orderdetailsgroup = orderdetailsgroup.toPandas()
    
    #convert orderdetailsgroup to json string
    jsonstring = orderdetailsgroup.to_json(orient='records', force_ascii=False)
    
    #convert jsonstring to dictionary to ensure correct encoding and no corrupt records
    jsonstring = json.loads(jsonstring)
    
    #set details json element in orderjsondata to jsonstring which contains orderdetailsgroup - this merges order details into the order 
    orderjsondata['details'] = jsonstring
 
  #convert dictionary to json
  orderjsondata = json.dumps(orderjsondata)
 
  #read the json into spark dataframe
  df = spark.read.json(sc.parallelize([orderjsondata]))
  
  #write the dataframe (this will be a single order record with merged many-to-one order details) to cosmos db using spark the connector
  #https://docs.microsoft.com/azure/cosmos-db/spark-connector
  df.write.format("com.microsoft.azure.cosmosdb.spark").mode("append").options(**writeConfig).save()
```

Slutligen anropar vi ovanstående med hjälp av en map-funktion i trådpoolen för att köra parallellt och skicka listan med order-ID:er som vi skapade tidigare:

```python
#map order details to orders in parallel using the above function
pool.map(writeOrder, orderids)
```
I båda endera metoden bör vi i slutet få korrekt sparade inbäddade OrderDetails i varje Order-dokument i Cosmos DB samling:

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks4.png" alt-text="Databricks":::

## <a name="next-steps"></a>Nästa steg
* Lär dig [mer om datamodellering i Azure Cosmos DB](./modeling-data.md)
* Lär [dig hur du modellerar och partitionerar data på Azure Cosmos DB](./how-to-model-partition-example.md)
