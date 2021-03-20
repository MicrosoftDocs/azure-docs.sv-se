---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 9447cec55c53861ca57d5416a91ffefd35fdd20b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91571932"
---
## <a name="repeatability-during-copy"></a>Repeterbarhet under kopiering
När du kopierar data till Azure SQL/SQL Server från andra data lager måste du ha repeterbarhet i åtanke för att undvika oönskade resultat. 

När du kopierar data till Azure SQL/SQL Server Database, kommer kopierings aktiviteten som standard att lägga till data uppsättningen i tabellen Sink som standard. Till exempel, när du kopierar data från en CSV-fil källa (kommaavgränsade värden) som innehåller två poster till Azure SQL/SQL Server Database, så är det här tabellen ser ut så här:

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

Anta att du har hittat fel i käll filen och uppdaterat mängden av röret från 2 till 4 i käll filen. Om du kör data sektorn igen för den perioden hittar du två nya poster som läggs till i Azure SQL/SQL Server Database. Nedan antas ingen av kolumnerna i tabellen ha Primary Key-begränsningen.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

För att undvika detta måste du ange UPSERT semantik genom att använda någon av nedanstående två mekanismer som anges nedan.

> [!NOTE]
> En sektor kan köras igen automatiskt i Azure Data Factory enligt principen för återförsök som angetts.
> 
> 

### <a name="mechanism-1"></a>Mekanism 1
Du kan använda egenskapen **sqlWriterCleanupScript** för att först utföra rensnings åtgärden när en sektor körs. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

Rensnings skriptet körs först under kopiering för en specifik sektor som skulle kunna ta bort data från SQL-tabellen som motsvarar den sektorn. Aktiviteten infogar sedan data i SQL-tabellen. 

Om sektorn nu körs igen, kommer du att se att antalet uppdateras på önskat sätt.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Anta att den platta tvätt posten tas bort från den ursprungliga CSV-filen. Sedan kan du köra sektorn på nytt som resulterar i följande resultat: 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
7     Down Tube    4            2015-05-01 00:00:00
```
Inget nytt behövde göras. Kopierings aktiviteten körde rensnings skriptet för att ta bort motsvarande data för den sektorn. Sedan läses indata från CSV-filen (som sedan endast innehåller 1 post) och infogas i tabellen. 

### <a name="mechanism-2"></a>Mekanism 2
> [!IMPORTANT]
> sliceIdentifierColumnName stöds inte för Azure Synapse Analytics just nu. 

En annan mekanism för att uppnå repeterbarhet är genom att ha en dedikerad kolumn (**sliceIdentifierColumnName**) i mål tabellen. Den här kolumnen används av Azure Data Factory för att säkerställa att källa och mål är synkroniserade. Den här metoden fungerar när det är flexibelt att ändra eller definiera mål SQL-tabellens schema. 

Den här kolumnen används av Azure Data Factory i upprepnings syfte och i processen Azure Data Factory görs inga schema ändringar i tabellen. Sätt att använda den här metoden:

1. Definiera en kolumn av typen Binary (32) i mål-SQL-tabellen. Det får inte finnas några begränsningar i den här kolumnen. Låt oss ge den här kolumnen namnet "ColumnForADFuseOnly" för det här exemplet.
2. Använd den i kopierings aktiviteten enligt följande:
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "ColumnForADFuseOnly"
    }
    ```

Azure Data Factory fyller i den här kolumnen efter behov för att se till att källan och målet är synkroniserat. Värdena i den här kolumnen ska inte användas utanför den här kontexten av användaren. 

På samma sätt som för mekanismen 1 rensar kopierings aktiviteten automatiskt data för den aktuella sektorn från mål-SQL-tabellen och kör sedan kopierings aktiviteten normalt för att infoga data från källa till mål för den sektorn. 

