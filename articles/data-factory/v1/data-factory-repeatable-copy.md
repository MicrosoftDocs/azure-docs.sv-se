---
title: Upprepnings bar kopia i Azure Data Factory
description: Lär dig hur du undviker dubbletter även om en sektor som kopierar data körs mer än en gång.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: c427cd90412121e896738ca43f4c66dd24b096dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100387095"
---
# <a name="repeatable-copy-in-azure-data-factory"></a>Upprepnings bar kopia i Azure Data Factory

## <a name="repeatable-read-from-relational-sources"></a>Repeterbar läsning från Relations källor
När du kopierar data från Relations data lager bör du ha repeterbarhet i åtanke för att undvika oönskade resultat. I Azure Data Factory kan du köra om ett segment manuellt. Du kan också konfigurera principer för återförsök för en data uppsättning så att en sektor körs igen när ett fel uppstår. När en sektor körs på annat sätt måste du se till att samma data är lästa oavsett hur många gånger en sektor körs.  
 
> [!NOTE]
> Följande exempel gäller för Azure SQL men gäller för alla data lager som stöder rektangulära data uppsättningar. Du kanske måste ändra **typ** av källa och egenskapen **fråga** (till exempel: fråga i stället för sqlReaderQuery) för data lagret.   

När du läser från relationella butiker vill du vanligt vis läsa data som motsvarar den sektorn. Ett sätt att göra detta med hjälp av systemvariablerna WindowStart och WindowEnd finns i Azure Data Factory. Läs om variabler och funktioner i Azure Data Factory här i artikeln [Azure Data Factory-Functions och system Variables](data-factory-functions-variables.md) . Exempel: 

```json
"source": {
    "type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm\\'', WindowStart, WindowEnd)"
},
```
Den här frågan läser data som faller inom intervallet för sektorns varaktighet (WindowStart-> WindowEnd) från tabellen tabell tabell. Att köra om den här sektorn ser alltid till att samma data är lästa. 

I andra fall kanske du vill läsa hela tabellen och definiera sqlReaderQuery enligt följande:

```json
"source": 
{            
    "type": "SqlSource",
    "sqlReaderQuery": "select * from MyTable"
},
```

## <a name="repeatable-write-to-sqlsink"></a>Repeterbar skrivning till SqlSink
När du kopierar data till **Azure SQL/SQL Server** från andra data lager, måste du ha repeterbarhet i åtanke för att undvika oönskade resultat. 

När du kopierar data till Azure SQL/SQL Server Database lägger kopierings aktiviteten till data i tabellen mottagare som standard. Anta att du kopierar data från en CSV-fil (kommaavgränsade värden) som innehåller två poster till följande tabell i en Azure SQL/SQL Server-databas. När ett segment körs kopieras de två posterna till SQL-tabellen. 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

Anta att du har hittat fel i käll filen och uppdaterat mängden av röret från 2 till 4. Om du kör om data sektorn manuellt, hittar du två nya poster som läggs till i Azure SQL/SQL Server Database. I det här exemplet förutsätts att ingen av kolumnerna i tabellen har begränsningen Primary Key.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

För att undvika det här beteendet måste du ange UPSERT semantik genom att använda någon av följande två mekanismer:

### <a name="mechanism-1-using-sqlwritercleanupscript"></a>Mekanism 1: använda sqlWriterCleanupScript
Du kan använda egenskapen **sqlWriterCleanupScript** för att rensa data från tabellen Sink innan du infogar data när en sektor körs. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

När en sektor körs körs rensnings skriptet först för att ta bort data som motsvarar sektorn från SQL-tabellen. Kopierings aktiviteten infogar sedan data i SQL-tabellen. Om sektorn körs om, uppdateras kvantiteten efter behov.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Anta att den platta tvätt posten tas bort från den ursprungliga CSV-filen. Sedan kan du köra sektorn följande resultat: 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
7     Down Tube    4            2015-05-01 00:00:00
```

Kopierings aktiviteten körde rensnings skriptet för att ta bort motsvarande data för den sektorn. Sedan läses indata från CSV-filen (som sedan endast innehåller en post) och infogas i tabellen. 

### <a name="mechanism-2-using-sliceidentifiercolumnname"></a>Mekanism 2: använda sliceIdentifierColumnName
> [!IMPORTANT]
> SliceIdentifierColumnName stöds för närvarande inte för Azure Synapse Analytics. 

Den andra mekanismen för att uppnå repeterbarhet är genom att ha en dedikerad kolumn (sliceIdentifierColumnName) i mål tabellen. Den här kolumnen används av Azure Data Factory för att säkerställa att källa och mål är synkroniserade. Den här metoden fungerar när det är flexibelt att ändra eller definiera mål SQL-tabellens schema. 

Den här kolumnen används av Azure Data Factory i upprepnings syfte och i processen Azure Data Factory görs inga schema ändringar i tabellen. Sätt att använda den här metoden:

1. Definiera en kolumn av typen **Binary (32)** i mål-SQL-tabellen. Det får inte finnas några begränsningar i den här kolumnen. Låt oss ge den här kolumnen namnet AdfSliceIdentifier i det här exemplet.


    Käll tabell:

    ```sql
    CREATE TABLE [dbo].[Student](
       [Id] [varchar](32) NOT NULL,
       [Name] [nvarchar](256) NOT NULL
    )
    ```

    Mål tabell: 

    ```sql
    CREATE TABLE [dbo].[Student](
       [Id] [varchar](32) NOT NULL,
       [Name] [nvarchar](256) NOT NULL,
       [AdfSliceIdentifier] [binary](32) NULL
    )
    ```

1. Använd den i kopierings aktiviteten enligt följande:
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "AdfSliceIdentifier"
    }
    ```

Azure Data Factory fyller i den här kolumnen efter behov för att se till att källan och målet är synkroniserat. Värdena i den här kolumnen ska inte användas utanför den här kontexten. 

På samma sätt som för mekanism 1 rensar kopierings aktiviteten automatiskt data för den aktuella sektorn från mål-SQL-tabellen. Den infogar sedan data från källa i till mål tabellen. 

## <a name="next-steps"></a>Nästa steg
Läs följande artiklar om koppling för fullständiga JSON-exempel: 

- [Azure SQL Database](data-factory-azure-sql-connector.md)
- [Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)
