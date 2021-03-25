---
title: Felsöka fråge prestanda – Azure Database for MySQL
description: Lär dig hur du använder förklaring för att felsöka fråge prestanda i Azure Database for MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: troubleshooting
ms.date: 3/18/2020
ms.openlocfilehash: 8a7a0f8754b0ed7981d0ec888552efc65f451ef9
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2021
ms.locfileid: "105109819"
---
# <a name="how-to-use-explain-to-profile-query-performance-in-azure-database-for-mysql"></a>Så här använder du förklaringar för att profilera frågor om prestanda i Azure Database for MySQL
[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

**Förklaring** är ett användbart verktyg för att optimera frågor. FÖRKLARINGs instruktionen kan användas för att hämta information om hur SQL-uttryck körs. Följande utdata visar ett exempel på körning av en FÖRKLARINGs instruktion.

```sql
mysql> EXPLAIN SELECT * FROM tb1 WHERE id=100\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 10.00
        Extra: Using where
```

Som du kan se i det här exemplet är värdet för *nyckeln* null. Den här utmatningen innebär att MySQL inte kan hitta några index som är optimerade för frågan och utför en fullständig tabells ökning. Vi optimerar den här frågan genom att lägga till ett index i kolumnen **ID** .

```sql
mysql> ALTER TABLE tb1 ADD KEY (id);
mysql> EXPLAIN SELECT * FROM tb1 WHERE id=100\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ref
possible_keys: id
          key: id
      key_len: 4
          ref: const
         rows: 1
     filtered: 100.00
        Extra: NULL
```

Den nya förklaringen visar att MySQL nu använder ett index för att begränsa antalet rader till 1, vilket i sin tur drastiskt förkortade Sök tiden.
 
## <a name="covering-index"></a>Täcker index
Ett täcker index består av alla kolumner i en fråga i indexet för att minska värde hämtningen från data tabeller. Här är en illustration i följande **Group by** -instruktion.
 
```sql
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using temporary; Using filesort
```

Som kan ses från utdata, använder MySQL inte några index eftersom inga lämpliga index är tillgängliga. Den visar också *användning av temporärt. Använda fil sortering*, vilket innebär att MySQL skapar en temporär tabell för att uppfylla **Group by** -satsen.
 
Att skapa ett index för enbart kolumnen **C2** gör ingen skillnad, och MySQL måste fortfarande skapa en temporär tabell:

```sql 
mysql> ALTER TABLE tb1 ADD KEY (c2);
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using temporary; Using filesort
```

I det här fallet kan ett **index som omfattas** både **C1** och **C2** skapas, genom att lägga till värdet **C2** direkt i indexet för att eliminera ytterligare data sökning.

```sql 
mysql> ALTER TABLE tb1 ADD KEY covered(c1,c2);
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: index
possible_keys: covered
          key: covered
      key_len: 108
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using index
```

Som beskrivs ovan använder MySQL nu det skyddade indexet och undviker att skapa en tillfällig tabell. 

## <a name="combined-index"></a>Kombinerat index
Ett kombinerat index består av värden från flera kolumner och kan betraktas som en matris med rader som sorteras efter sammanfogning av värden för de indexerade kolumnerna. Den här metoden kan vara användbar i en **Group by** -instruktion.

```sql
mysql> EXPLAIN SELECT c1, c2 from tb1 WHERE c2 LIKE '%100' ORDER BY c1 DESC LIMIT 10\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using filesort
```

MySQL utför en *fil sorterings* åtgärd som är ganska långsam, särskilt när den måste sortera många rader. För att optimera den här frågan kan ett kombinerat index skapas i båda kolumnerna som sorteras.

```sql 
mysql> ALTER TABLE tb1 ADD KEY my_sort2 (c1, c2);
mysql> EXPLAIN SELECT c1, c2 from tb1 WHERE c2 LIKE '%100' ORDER BY c1 DESC LIMIT 10\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: index
possible_keys: NULL
          key: my_sort2
      key_len: 108
          ref: NULL
         rows: 10
     filtered: 11.11
        Extra: Using where; Using index
```

FÖRKLARINGEN visar nu att MySQL kan använda kombinerat index för att undvika ytterligare sortering eftersom indexet redan har sorterats.
 
## <a name="conclusion"></a>Slutsats
 
Att använda förklaringar och olika typer av index kan öka prestandan avsevärt. Att ha ett index i tabellen innebär inte nödvändigt vis att MySQL kan använda det för dina frågor. Validera alltid dina antaganden med hjälp av förklara och optimera dina frågor med hjälp av index.


## <a name="next-steps"></a>Nästa steg
- Om du vill hitta peer-svar på dina mest aktuella frågor eller publicera en ny fråga/svar kan du besöka [Microsoft Q&en fråge sida](/answers/topics/azure-database-mysql.html) eller [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql).
