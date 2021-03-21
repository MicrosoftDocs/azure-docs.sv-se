---
title: Övervaka XTP minnes lagring
description: Uppskatta och övervaka XTP minnes användning, kapacitet; Lös kapacitets fel 41823
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 5717c2479c1d894117bae44826a814c3cfeaa98a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96493397"
---
# <a name="monitor-in-memory-oltp-storage-in-azure-sql-database-and-azure-sql-managed-instance"></a>Övervaka In-Memory OLTP-lagring i Azure SQL Database och Azure SQL-hanterad instans
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

När du använder [minnes intern OLTP](in-memory-oltp-overview.md)finns data i minnesoptimerade tabeller och tabell variabler i In-Memory OLTP-lagring.

## <a name="determine-whether-data-fits-within-the-in-memory-oltp-storage-cap"></a>Ta reda på om data passar i In-Memory OLTP-lagrings höljet

Ta reda på lagrings nivåerna för de olika tjänst nivåerna. Varje Premium-och Affärskritisk tjänst nivå har en maximal In-Memory OLTP-lagrings storlek.

- [DTU-baserade resurs gränser – enskild databas](database/resource-limits-dtu-single-databases.md)
- [DTU-baserade resurs gränser-elastiska pooler](database/resource-limits-dtu-elastic-pools.md)
- [vCore resurs gränser – enskilda databaser](database/resource-limits-vcore-single-databases.md)
- [vCore resurs gränser – elastiska pooler](database/resource-limits-vcore-elastic-pools.md)
- [vCore resurs gränser – hanterad instans](managed-instance/resource-limits.md)

Att uppskatta minnes kraven för en minnesoptimerade tabell fungerar på samma sätt för SQL Server som i Azure SQL Database och Azure SQL-hanterad instans. Ta några minuter för att granska [kraven på uppskattnings minne](/sql/relational-databases/in-memory-oltp/estimate-memory-requirements-for-memory-optimized-tables).

Tabell-och tabell variabel rader, samt index, räknas mot Max storleken för användar data. Dessutom behöver ALTER TABLE tillräckligt med utrymme för att skapa en ny version av hela tabellen och dess index.

När den här gränsen har överskridits kan INSERT-och Update-åtgärder Miss lyckas med fel 41823 för enskilda databaser i Azure SQL Database och databaser i Azure SQL-hanterad instans och fel 41840 för elastiska pooler i Azure SQL Database. Vid det här skedet måste du antingen ta bort data för att frigöra minne eller uppgradera tjänst nivån eller databasens beräknings storlek.

## <a name="monitoring-and-alerting"></a>Övervakning och avisering

Du kan övervaka minnes lagring i minnet i procent av lagrings utrymmet för din beräknings storlek i [Azure Portal](https://portal.azure.com/):

1. På bladet databas letar du reda på resurs användnings rutan och klickar på Redigera.
2. Välj måttet `In-Memory OLTP Storage percentage` .
3. Om du vill lägga till en avisering klickar du på rutan resursanvändning för att öppna bladet mått och klickar sedan på Lägg till avisering.

Eller Använd följande fråga för att Visa lagrings belastningen i minnet:

```sql
    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats
```

## <a name="correct-out-of-in-memory-oltp-storage-situations---errors-41823-and-41840"></a>Åtgärda inaktuella minnes fel i OLTP-lagring-fel 41823 och 41840

Genom att trycka på In-Memory OLTP-lagrings gränsen i databas resultaten i INSERT-, UPDATE-, ALTER-och CREATE-åtgärder som inte fungerar med fel meddelandet 41823 (för enskilda databaser) eller fel 41840 (för elastiska pooler). Båda felen gör att den aktiva transaktionen avbryts.

Fel meddelandena 41823 och 41840 anger att de minnesoptimerade tabellerna och tabellattribut i databasen eller poolen har nått maximal lagrings storlek In-Memory OLTP.

För att lösa det här felet, antingen:

- Ta bort data från de minnesoptimerade tabellerna och eventuellt avlasta data till traditionella, diskbaserade tabeller. eller
- Uppgradera tjänst nivån till en med tillräckligt minnes intern lagring för de data du behöver behålla i minnesoptimerade tabeller.

> [!NOTE]
> I sällsynta fall kan fel 41823 och 41840 vara tillfälliga, vilket innebär att det finns tillräckligt med tillgänglig In-Memory OLTP-lagring och försök igen. Vi rekommenderar därför att både övervaka den övergripande tillgängliga In-Memory OLTP-lagring och att försöka igen när du först påträffar fel 41823 eller 41840. Mer information om logik för omprövning finns i [konflikt identifiering och omprövnings logik med In-Memory OLTP](/sql/relational-databases/In-memory-oltp/transactions-with-memory-optimized-tables#conflict-detection-and-retry-logic).

## <a name="next-steps"></a>Nästa steg

Övervaknings vägledning finns i [övervaka med hjälp av vyer för dynamisk hantering](database/monitoring-with-dmvs.md).