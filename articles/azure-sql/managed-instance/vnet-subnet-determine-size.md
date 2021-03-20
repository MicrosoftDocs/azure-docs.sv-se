---
title: Bestäm nödvändig under näts storlek & intervall
titleSuffix: Azure SQL Managed Instance
description: I det här avsnittet beskrivs hur du beräknar storleken på det undernät där den hanterade Azure SQL-instansen ska distribueras.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 02/22/2019
ms.openlocfilehash: 156a4c74eea24b20c28df88be85cb32c0ebe2981
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96012468"
---
# <a name="determine-required-subnet-size--range-for-azure-sql-managed-instance"></a>Bestäm nödvändig under näts storlek & intervall för Azure SQL-hanterad instans
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL-hanterad instans måste distribueras i ett [virtuellt Azure-nätverk (VNet)](../../virtual-network/virtual-networks-overview.md).

Antalet hanterade instanser som kan distribueras i under nätet för ett VNet beror på under nätets storlek (under nätets intervall).

När du skapar en hanterad instans allokerar Azure ett antal virtuella datorer beroende på vilken nivå du valde under etableringen. Eftersom de virtuella datorerna är associerade med ditt undernät kräver de IP-adresser. För att säkerställa hög tillgänglighet vid vanliga drift-och tjänst underhåll kan Azure allokera ytterligare virtuella datorer. Därför är antalet begärda IP-adresser i ett undernät större än antalet hanterade instanser i det under nätet.

En hanterad instans behöver minst 32 IP-adresser i ett undernät. Därför kan du använda en minsta undernätsmask på /27 när du definierar undernätets IP-intervall. Vi rekommenderar att du noggrant planerar undernätets storlek för dina distributioner av hanterade instanser. Indata som bör beaktas under planeringen är:

- Antal hanterade instanser, inklusive följande instans parametrar:
  - tjänst nivå
  - maskin varu generering
  - antal virtuella kärnor
- Planerar att skala upp/ned eller ändra tjänst nivå

> [!IMPORTANT]
> Med en under näts storlek med 16 IP-adresser (nätmask/28) kan du distribuera hanterade instanser i den, men den bör endast användas för att distribuera en enskild instans som används för utvärdering eller i utvecklings-och test scenarier där skalnings åtgärder inte kommer att utföras.

## <a name="determine-subnet-size"></a>Bestäm under näts storlek

Ändra storlek på ditt undernät enligt framtida distribution och skalnings behov. Följande parametrar kan hjälpa dig att bilda en beräkning:

- Azure använder fem IP-adresser i under nätet för sina egna behov
- Varje virtuellt kluster allokerar ytterligare antal adresser 
- Varje hanterad instans använder antalet adresser som är beroende av pris nivån och maskin varu generering

> [!IMPORTANT]
> Det går inte att ändra undernätets adressintervall om det finns en resurs i undernätet. Det går inte heller att flytta hanterade instanser från ett undernät till ett annat. När det är möjligt bör du överväga att använda större undernät i stället för mindre för att förhindra problem i framtiden.

GP = generell användning; BC = affärs kritisk; VC = virtuellt kluster

| **Maskin gen** | **Prisnivå** | **Azure-användning** | **VC-användning** | **Instans användning** | **Totalt*** |
| --- | --- | --- | --- | --- | --- |
| Gen4 | GP | 5 | 1 | 5 | 11 |
| Gen4 | BC | 5 | 1 | 5 | 11 |
| Gen5 | GP | 5 | 6 | 3 | 14 |
| Gen5 | BC | 5 | 6 | 5 | 16 |

  \* Kolumn summan visar antalet adresser som ska vidtas när en instans distribueras i under nätet. Varje ytterligare instans i under nätet lägger till antalet adresser som representeras med kolumnen instans användning. Adresser som representeras med Azure Usage-kolumnen delas över flera virtuella kluster, medan adresser som representeras med VC Usage-kolumnen delas mellan instanser som placeras i det virtuella klustret.

Uppdaterings åtgärden kräver vanligt vis storleks ändring av virtuellt kluster. Under vissa omständigheter kräver uppdaterings åtgärden att virtuella kluster skapas (mer information om [hanterings åtgärder](sql-managed-instance-paas-overview.md#management-operations)för mer information). Om det virtuella klustret skapas, är antalet ytterligare adresser som krävs lika med antalet adresser som representeras av kolumnen VC-användning summerade med adresser som krävs för instanser som placerats i det virtuella klustret (instans användnings kolumnen).

**Exempel 1**: du planerar att ha en allmän hanterad instans (Gen4 maskin vara) och en verksamhets kritisk hanterad instans (Gen5 maskin vara). Det innebär att du behöver minst 5 + 1 + 1 * 5 + 6 + 1 * 5 = 22 IP-adresser för att kunna distribuera. Eftersom IP-intervall definieras i potens av 2 kräver ditt undernät minst IP-intervallet 32 (2 ^ 5) för den här distributionen.<br><br>
Som nämnts ovan kräver uppdaterings åtgärden att virtuella kluster skapas. Det innebär att, som ett exempel, om en uppdatering av den Gen5 affärs kritiska hanterade instansen som kräver ett virtuellt kluster skapas, måste du ha ytterligare 6 + 5 = 11 IP-adresser tillgängliga. Eftersom du redan använder 22 av 32-adresserna finns det inga tillgängliga adresser för den här åtgärden. Därför måste du reservera under nätet med nät masken/26 (64 adresser).

**Exempel 2**: du planerar att ha tre generella användnings områden (Gen5 maskin vara) och två affärs kritiska hanterade instanser (Gen5 maskin vara) som placeras i samma undernät. Det innebär att du behöver 5 + 6 + 3 * 3 + 2 * 5 = 30 IP-adresser. Därför måste du reservera under nätet med nät masken/26. Om du väljer en under nät mask på/27 skulle det återstående antalet adresser vara 2 (32 – 30). Detta förhindrar uppdaterings åtgärder för alla instanser eftersom ytterligare adresser krävs i undernät för att utföra instans skalning.

**Exempel 3**: du planerar att ha en hanterings instans för generell användning (Gen5 maskin vara) och utföra virtuella kärnor uppdaterings åtgärder från tid till gång. Det innebär att du behöver 5 + 6 + 1 * 3 + 3 = 17 IP-adresser. Eftersom IP-intervall definieras med 2 måste du ha IP-adresser för IP-intervallet 32 (2 ^ 5). Därför måste du reservera under nätet med nät masken/27.

### <a name="address-requirements-for-update-scenarios"></a>Adress krav för uppdaterings scenarier

Vid skalnings åtgärds instanser kräver tillfälligt ytterligare IP-kapacitet som är beroende av pris nivå och maskin varu generering

| **Maskin gen** | **Prisnivå** | **Scenario** | **Ytterligare adresser*** |
| --- | --- | --- | --- |
| Gen4 | GP eller BC | Skalar virtuella kärnor | 5 |
| Gen4 | GP eller BC | Skala lagring | 5 |
| Gen4 | GP eller BC | Växla från GP till BC eller BC till GP | 5 |
| Gen4 | GP | Växlar till Gen5 * | 9 |
| Gen4 | BC | Växlar till Gen5 * | 11 |
| Gen5 | GP | Skalar virtuella kärnor | 3 |
| Gen5 | GP | Skala lagring | 0 |
| Gen5 | GP | Växlar till BC | 5 |
| Gen5 | BC | Skalar virtuella kärnor | 5 |
| Gen5 | BC | Skala lagring | 5 |
| Gen5 | BC | Växlar till GP | 3 |

  \* Gen4-maskinvaran föråldras och är inte längre tillgänglig för nya distributioner. Uppdatera maskin varu generering från Gen4 till Gen5 för att dra nytta av de funktioner som är speciella för Gen5 maskin varu generering.

## <a name="next-steps"></a>Nästa steg

- En översikt finns i [Vad är en Azure SQL-hanterad instans?](sql-managed-instance-paas-overview.md).
- Lär dig mer om [anslutnings arkitektur för SQL-hanterad instans](connectivity-architecture-overview.md).
- Se så här [skapar du ett VNet där du kommer att distribuera SQL-hanterad instans](virtual-network-subnet-create-arm-template.md).
- Information om DNS-problem finns i [Konfigurera en anpassad DNS](custom-dns-configure.md).
