---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/27/2019
ms.author: tamram
ms.openlocfilehash: 9a60c624b181a1efd2f6deebd349daa82214a8a4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "67187018"
---
<!--created by Robin Shahan to go in the articles for table storage w/powershell.
    There is one for Azure Table Storage and one for Azure Cosmos DB Table API -->

## <a name="managing-table-entities"></a>Hantera tabell enheter

Nu när du har en tabell kan vi titta på hur du hanterar entiteter eller rader i tabellen. 

Entiteter kan ha upp till 255 egenskaper, inklusive tre system egenskaper: **PartitionKey**, **RowKey** och **timestamp**. Du är ansvarig för att infoga och uppdatera värdena för **PartitionKey** och **RowKey**. Servern hanterar värdet för **tidsstämpel**, som inte kan ändras. Tillsammans identifierar **PartitionKey** och **RowKey** varje entitet i en tabell.

* **PartitionKey**: bestämmer partitionen som entiteten lagras i.
* **RowKey**: entiteten identifieras unikt i partitionen.

Du kan definiera upp till 252 anpassade egenskaper för en entitet. 

### <a name="add-table-entities"></a>Lägg till tabell enheter

Lägg till entiteter i en tabell med **Add-AzTableRow**. I de här exemplen används partitionsalternativ med värden `partition1` och `partition2` rad nycklar som är lika med delstats förkortningar. Egenskaperna i varje entitet är `username` och `userid` . 

```powershell
$partitionKey1 = "partition1"
$partitionKey2 = "partition2"

# add four rows 
Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey1 `
    -rowKey ("CA") -property @{"username"="Chris";"userid"=1}

Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey2 `
    -rowKey ("NM") -property @{"username"="Jessie";"userid"=2}

Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey1 `
    -rowKey ("WA") -property @{"username"="Christine";"userid"=3}

Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey2 `
    -rowKey ("TX") -property @{"username"="Steven";"userid"=4}
```

### <a name="query-the-table-entities"></a>Fråga tabellens entiteter

Du kan fråga entiteterna i en tabell med hjälp av kommandot **Get-AzTableRow** .

> [!NOTE]
> Cmdletarna **Get-AzureStorageTableRowAll**, **Get-AzureStorageTableRowByPartitionKey**, **Get-AzureStorageTableRowByColumnName** och **Get-AzureStorageTableRowByCustomFilter** är föråldrade och tas bort i en framtida versions uppdatering.

#### <a name="retrieve-all-entities"></a>Hämta alla entiteter

```powershell
Get-AzTableRow -table $cloudTable | ft
```

Det här kommandot ger resultat som liknar följande tabell:

| userid | användarnamn | partitionstabellen | rowkey |
|----|---------|---------------|----|
| 1 | Chris | partition1 | CA |
| 3 | Christine | partition1 | WA |
| 2 | Jessie | Partition2 | NM |
| 4 | Stefan | Partition2 | TX |

#### <a name="retrieve-entities-for-a-specific-partition"></a>Hämta entiteter för en speciell partition

```powershell
Get-AzTableRow -table $cloudTable -partitionKey $partitionKey1 | ft
```

Resultatet ser ut ungefär som i följande tabell:

| userid | användarnamn | partitionstabellen | rowkey |
|----|---------|---------------|----|
| 1 | Chris | partition1 | CA |
| 3 | Christine | partition1 | WA |

#### <a name="retrieve-entities-for-a-specific-value-in-a-specific-column"></a>Hämta entiteter för ett angivet värde i en speciell kolumn

```powershell
Get-AzTableRow -table $cloudTable `
    -columnName "username" `
    -value "Chris" `
    -operator Equal
```

Den här frågan hämtar en post.

|fält|värde|
|----|----|
| userid | 1 |
| användarnamn | Chris |
| PartitionKey | partition1 |
| RowKey      | CA |

#### <a name="retrieve-entities-using-a-custom-filter"></a>Hämta entiteter med ett anpassat filter 

```powershell
Get-AzTableRow `
    -table $cloudTable `
    -customFilter "(userid eq 1)"
```

Den här frågan hämtar en post.

|fält|värde|
|----|----|
| userid | 1 |
| användarnamn | Chris |
| PartitionKey | partition1 |
| RowKey      | CA |

### <a name="updating-entities"></a>Uppdaterar entiteter 

Det finns tre steg för att uppdatera entiteter. Börja med att hämta entiteten som ska ändras. Sedan gör du ändringen. För det tredje, genomför du ändringen med **Update-AzTableRow**.

Uppdatera entiteten med username = ' Jessie ' till username = ' Jessie2 '. Det här exemplet visar också ett annat sätt att skapa ett anpassat filter med hjälp av .NET-typer.

```powershell
# Create a filter and get the entity to be updated.
[string]$filter = `
    [Microsoft.Azure.Cosmos.Table.TableQuery]::GenerateFilterCondition("username",`
    [Microsoft.Azure.Cosmos.Table.QueryComparisons]::Equal,"Jessie")
$user = Get-AzTableRow `
    -table $cloudTable `
    -customFilter $filter

# Change the entity.
$user.username = "Jessie2"

# To commit the change, pipe the updated record into the update cmdlet.
$user | Update-AzTableRow -table $cloudTable

# To see the new record, query the table.
Get-AzTableRow -table $cloudTable `
    -customFilter "(username eq 'Jessie2')"
```

Resultaten visar Jessie2-posten.

|fält|värde|
|----|----|
| userid | 2 |
| användarnamn | Jessie2 |
| PartitionKey | Partition2 |
| RowKey      | NM |

### <a name="deleting-table-entities"></a>Tar bort tabell enheter

Du kan ta bort en entitet eller alla entiteter i tabellen.

#### <a name="deleting-one-entity"></a>Tar bort en entitet

Om du vill ta bort en enskild entitet hämtar du en referens till den entiteten och rör den i **Remove-AzTableRow**.

```powershell
# Set filter.
[string]$filter = `
  [Microsoft.Azure.Cosmos.Table.TableQuery]::GenerateFilterCondition("username",`
  [Microsoft.Azure.Cosmos.Table.QueryComparisons]::Equal,"Jessie2")

# Retrieve entity to be deleted, then pipe it into the remove cmdlet.
$userToDelete = Get-AzTableRow `
    -table $cloudTable `
    -customFilter $filter
$userToDelete | Remove-AzTableRow -table $cloudTable

# Retrieve entities from table and see that Jessie2 has been deleted.
Get-AzTableRow -table $cloudTable | ft
```

#### <a name="delete-all-entities-in-the-table"></a>Ta bort alla entiteter i tabellen

Om du vill ta bort alla entiteter i tabellen hämtar du dem och rör resultatet i Remove-cmdleten. 

```powershell
# Get all rows and pipe the result into the remove cmdlet.
Get-AzTableRow `
    -table $cloudTable | Remove-AzTableRow -table $cloudTable 

# List entities in the table (there won't be any).
Get-AzTableRow -table $cloudTable | ft
```
