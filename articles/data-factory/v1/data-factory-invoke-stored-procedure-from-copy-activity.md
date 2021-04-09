---
title: Anropa lagrad procedur från Azure Data Factory kopierings aktivitet
description: Lär dig att anropa en lagrad procedur i Azure SQL Database eller SQL Server från en Azure Data Factory kopierings aktivitet.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 6f06b84ac0807a37c7adc603a557894be85a4cea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100374974"
---
# <a name="invoke-stored-procedure-from-copy-activity-in-azure-data-factory"></a>Anropa lagrad procedur från kopierings aktivitet i Azure Data Factory
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av tjänsten Data Factory, se [transformera data med hjälp av lagrade procedur aktiviteter i Data Factory](../transform-data-using-stored-procedure.md).


När du kopierar data till [SQL Server](data-factory-sqlserver-connector.md) eller [Azure SQL Database](data-factory-azure-sql-connector.md)kan du konfigurera **SqlSink** i kopierings aktiviteten för att anropa en lagrad procedur. Du kanske vill använda den lagrade proceduren för att utföra ytterligare bearbetning (sammanfoga kolumner, leta upp värden, infoga i flera tabeller osv.) krävs innan du infogar data i mål tabellen. Den här funktionen utnyttjar [tabell värdes parametrar](/dotnet/framework/data/adonet/sql/table-valued-parameters). 

Följande exempel visar hur du anropar en lagrad procedur i en SQL Server databas från en Data Factory pipeline (kopierings aktivitet):  

## <a name="output-dataset-json"></a>JSON för utgående data mängd
I JSON för utgående data uppsättning anger du **typen** till: **SqlServerTable**. Ange den som **AzureSqlTable** som ska användas med Azure SQL Database. Värdet för egenskapen **TableName** måste matcha namnet på den första parametern för den lagrade proceduren.  

```json
{
  "name": "SqlOutput",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlLinkedService",
    "typeProperties": {
      "tableName": "Marketing"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

## <a name="sqlsink-section-in-copy-activity-json"></a>SqlSink-avsnittet i kopierings aktivitets-JSON
Definiera avsnittet **SqlSink** i JSON-filen för kopierings aktiviteten enligt följande. Om du vill anropa en lagrad procedur och infoga data i mottagare/mål-databasen anger du värden för egenskaperna **SqlWriterStoredProcedureName** och **SqlWriterTableType** . Beskrivningar av dessa egenskaper finns i [avsnittet SqlSink i artikeln SQL Server Connector](data-factory-sqlserver-connector.md#sqlsink).

```json
"sink":
{
    "type": "SqlSink",
    "SqlWriterTableType": "MarketingType",
    "SqlWriterStoredProcedureName": "spOverwriteMarketing", 
    "storedProcedureParameters":
            {
                "stringData": 
                {
                    "value": "str1"     
                }
            }
}
```

## <a name="stored-procedure-definition"></a>Definition av lagrad procedur 
I databasen definierar du den lagrade proceduren med samma namn som **SqlWriterStoredProcedureName**. Den lagrade proceduren hanterar indata från käll data lagret och infogar data i en tabell i mål databasen. Namnet på den första parametern för den lagrade proceduren måste matcha det tableName som definierats i data uppsättnings-JSON (Marketing).

```sql
CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @stringData varchar(256)
AS
BEGIN
    DELETE FROM [dbo].[Marketing] where ProfileID = @stringData
    INSERT [dbo].[Marketing](ProfileID, State)
    SELECT * FROM @Marketing
END
```

## <a name="table-type-definition"></a>Definition av tabell typ
I databasen definierar du tabell typen med samma namn som **SqlWriterTableType**. Schemat för tabell typen måste matcha schemat för data uppsättningen för indata.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL
)
```

## <a name="next-steps"></a>Nästa steg
Läs följande artiklar om koppling för fullständiga JSON-exempel: 

- [Azure SQL Database](data-factory-azure-sql-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)