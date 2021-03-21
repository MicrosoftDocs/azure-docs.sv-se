---
title: Felsöka fråge prestanda – Azure Database for MariaDB
description: Lär dig hur du använder förklaring för att felsöka fråge prestanda i Azure Database for MariaDB.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: troubleshooting
ms.date: 3/18/2020
ms.openlocfilehash: 2b7491723ffcff73e4b243fe54ef18608167d636
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98664723"
---
# <a name="how-to-use-explain-to-profile-query-performance-in-azure-database-for-mariadb"></a>Så här använder du förklaringar för att profilera frågor om prestanda i Azure Database for MariaDB
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

Som du kan se i det här exemplet är värdet för *nyckeln* null. Den här utmatningen innebär att MariaDB inte kan hitta några index som är optimerade för frågan och den utför en fullständig tabells ökning. Vi optimerar den här frågan genom att lägga till ett index i kolumnen **ID** .

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

Den nya förklaringen visar att MariaDB nu använder ett index för att begränsa antalet rader till 1, vilket i sin tur förkortade Sök tiden dramatiskt.
 
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

Som kan ses från utdata använder MariaDB inte några index eftersom det inte finns några lämpliga index. Den visar också *användning av temporärt. Att använda fil sortering*, vilket innebär att MariaDB skapar en temporär tabell för att uppfylla **Group by** -satsen.
 
Att skapa ett index för enbart Column **C2** gör ingen skillnad, och MariaDB måste fortfarande skapa en tillfällig tabell:

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

Som beskrivs ovan, använder MariaDB nu det skyddade indexet och undviker att skapa en temporär tabell. 

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

MariaDB utför en *fil sorterings* åtgärd som är ganska långsam, särskilt när den måste sortera många rader. För att optimera den här frågan kan ett kombinerat index skapas i båda kolumnerna som sorteras.

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

FÖRKLARINGEN visar nu att MariaDB kan använda kombinerat index för att undvika ytterligare sortering eftersom indexet redan har sorterats.
 
## <a name="conclusion"></a>Slutsats
 
Att använda förklaringar och olika typer av index kan öka prestandan avsevärt. Att ha ett index i tabellen innebär inte nödvändigt vis att MariaDB skulle kunna använda den för dina frågor. Validera alltid dina antaganden med hjälp av förklara och optimera dina frågor med hjälp av index.

## <a name="next-steps"></a>Nästa steg
- Om du vill hitta peer-svar på dina mest aktuella frågor eller publicera en ny fråga/svar kan du besöka [Microsoft Q&en fråge sida](/answers/topics/azure-database-mariadb.html) eller [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mariadb).