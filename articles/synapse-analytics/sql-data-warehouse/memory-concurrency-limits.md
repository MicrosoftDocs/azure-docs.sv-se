---
title: Minnes-och samtidiga gränser
description: Visa minnes-och samtidiga gränser som tilldelats de olika prestanda nivåerna och resurs klasserna för dedikerad SQL-pool i Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: fb0ad93fb4a1269b4cca02b114c0427f0c44a31b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96455344"
---
# <a name="memory-and-concurrency-limits-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Minnes-och samtidiga gränser för dedikerad SQL-pool i Azure Synapse Analytics

Visa minnes-och samtidiga gränser som tilldelats de olika prestanda nivåerna och resurs klasserna i Azure Synapse Analytics.  

## <a name="data-warehouse-capacity-settings"></a>Kapacitets inställningar för informations lager

I följande tabeller visas den maximala kapaciteten för data lagret på olika prestanda nivåer. Information om hur du ändrar prestanda nivån finns i [Scale Compute-Portal](quickstart-scale-compute-portal.md).

### <a name="service-levels"></a>Service nivåer

Tjänst nivåerna sträcker sig från DW100c till DW30000c.

| Prestanda nivå | Beräkningsnoder | Distributioner per Compute-nod | Minne per informations lager (GB) |
|:-----------------:|:-------------:|:------------------------------:|:------------------------------:|
| DW100c            | 1             | 60                             |    60                          |
| DW200c            | 1             | 60                             |   120                          |
| DW300c            | 1             | 60                             |   180                          |
| DW400c            | 1             | 60                             |   240                          |
| DW500c            | 1             | 60                             |   300                          |
| DW1000c           | 2             | 30                             |   600                          |
| DW1500c           | 3             | 20                             |   900                          |
| DW2000c           | 4             | 15                             |  1200                          |
| DW2500c           | 5             | 12                             |  1500                          |
| DW3000c           | 6             | 10                             |  1800                          |
| DW5000c           | 10            | 6                              |  3000                          |
| DW6000c           | 12            | 5                              |  3600                          |
| DW7500c           | 15            | 4                              |  4500                          |
| DW10000c          | 20            | 3                              |  6000                          |
| DW15000c          | 30            | 2                              |  9000                          |
| DW30000c          | 60            | 1                              | 18000                          |

Den högsta service nivån är DW30000c, som har 60 Compute-noder och en distribution per Compute-nod. Till exempel, ett 600 TB informations lager på DW30000c bearbetar cirka 10 TB per Compute-nod.

## <a name="concurrency-maximums-for-workload-groups"></a>Concurrency-maximum för arbets belastnings grupper

Med introduktionen av [arbets belastnings grupper](sql-data-warehouse-workload-isolation.md)gäller inte längre begreppet samtidiga platser.  Resurser per begäran tilldelas i procent och anges i definitionen av arbets belastnings gruppen.  Men även om du tar bort samtidiga platser, finns det minimala mängder resurser som krävs per fråga baserat på service nivå.  Tabellen nedan definierar den minsta mängd resurser som krävs per fråga över tjänst nivåer och den associerade samtidigheten som kan uppnås.

|Servicenivå|Maximalt antal samtidiga frågor|Lägsta% som stöds för REQUEST_MIN_RESOURCE_GRANT_PERCENT|
|---|---|---|
|DW100c|4|25 %|
|DW200c|8|12,5%|
|DW300c|12|8 %|
|DW400c|16|6.25%|
|DW500c|20|5 %|
|DW1000c|32|3 %|
|DW1500c|32|3 %|
|DW2000c|48|2 %|
|DW2500c|48|2 %|
|DW3000c|64|1,5%|
|DW5000c|64|1,5%|
|DW6000c|128|0,75 %|
|DW7500c|128|0,75 %|
|DW10000c|128|0,75 %|
|DW15000c|128|0,75 %|
|DW30000c|128|0,75 %|
||||

## <a name="concurrency-maximums-for-resource-classes"></a>Concurrency-maximum för resurs klasser

För att säkerställa att varje fråga har tillräckligt med resurser för att köras effektivt spårar Synapse-SQL resursutnyttjande genom att tilldela samtidiga platser till varje fråga. Systemet placerar frågor i en kö baserat på prioritets-och samtidiga platser. Frågor väntar i kön tills det finns tillräckligt med tillgängliga samtidighets fack. [Prioritets](sql-data-warehouse-workload-importance.md) -och samtidiga platser fastställer processor prioritet. Mer information finns i [analysera din arbets belastning](analyze-your-workload.md)

**Statiska resurs klasser**

Följande tabell visar maximalt antal samtidiga frågor och samtidiga platser för varje [statisk resurs klass](resource-classes-for-workload-management.md).  

| Servicenivå | Maximalt antal samtidiga frågor | Tillgängliga samtidiga platser | Platser som används av staticrc10 | Platser som används av staticrc20 | Platser som används av staticrc30 | Platser som används av staticrc40 | Platser som används av staticrc50 | Platser som används av staticrc60 | Platser som används av staticrc70 | Platser som används av staticrc80 |
|:-------------:|:--------------------------:|:---------------------------:|:---------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|
| DW100c        |  4                         |    4                        | 1         | 2          | 4          | 4          | 4         |  4         |  4         |  4         |
| DW200c        |  8                         |    8                        | 1         | 2          | 4          | 8          |  8         |  8         |  8         |  8        |
| DW300c        | 12                         |   12                        | 1         | 2          | 4          | 8          |  8         |  8         |  8         |   8        |
| DW400c        | 16                         |   16                        | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW500c        | 20                         |   20                        | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW1000c       | 32                         |   40                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1500c       | 32                         |   60                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW2000c       | 48                         |   80                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW2500c       | 48                         |  100                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW3000c       | 64                         |  120                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW5000c       | 64                         |  200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW6000c       | 128                        |  240                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW7500c       | 128                        |  300                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW10000c      | 128                        |  400                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW15000c      | 128                        |  600                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW30000c      | 128                        | 1200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |

**Dynamiska resurs klasser**

I följande tabell visas maximalt antal samtidiga frågor och samtidiga platser för varje [dynamisk resurs klass](resource-classes-for-workload-management.md). Dynamiska resurs klasser använder en procent andel av 3-10-22-70-minne för små och medel stora XLarge resurs klasser på alla service nivåer.

| Servicenivå | Maximalt antal samtidiga frågor | Tillgängliga samtidiga platser | Platser som används av smallrc | Platser som används av mediumrc | Platser som används av largerc | Platser som används av xlargerc |
|:-------------:|:--------------------------:|:---------------------------:|:---------------------:|:----------------------:|:---------------------:|:----------------------:|
| DW100c        |  4                         |    4                        | 1                     |  1                     |  1                    |   2                    |
| DW200c        |  8                         |    8                        | 1                     |  1                     |  1                    |   5                    |
| DW300c        | 12                         |   12                        | 1                     |  1                     |  2                    |   8                    |
| DW400c        | 16                         |   16                        | 1                     |  1                     |  3                    |  11                    |
| DW500c        | 20                         |   20                        | 1                     |  2                     |  4                    |  14                    |
| DW1000c       | 32                         |   40                        | 1                     |  4                     |  8                    |  28                    |
| DW1500c       | 32                         |   60                        | 1                     |  6                     |  13                   |  42                    |
| DW2000c       | 32                         |   80                        | 2                     |  8                     |  17                   |  56                    |
| DW2500c       | 32                         |  100                        | 3                     | 10                     |  22                   |  70                    |
| DW3000c       | 32                         |  120                        | 3                     | 12                     |  26                   |  84                    |
| DW5000c       | 32                         |  200                        | 6                     | 20                     |  44                   | 140                    |
| DW6000c       | 32                         |  240                        | 7                     | 24                     |  52                   | 168                    |
| DW7500c       | 32                         |  300                        | 9                     | 30                     |  66                   | 210                    |
| DW10000c      | 32                         |  400                        | 12                    | 40                     |  88                   | 280                    |
| DW15000c      | 32                         |  600                        | 18                    | 60                     | 132                   | 420                    |
| DW30000c      | 32                         | 1200                        | 36                    | 120                    | 264                   | 840                    |

När det inte finns tillräckligt många lediga platser för att starta frågekörningen, placeras frågor i kö och körs utifrån prioritet.  Om det finns motsvarande prioritet körs frågor på en första, först ut-grunden.  När en fråga slutförs och antalet frågor och platser faller under gränserna, släpps köade frågor i Azure Synapse Analytics.

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om hur du utnyttjar resurs klasser för att optimera arbets belastningen ytterligare kan du läsa följande artiklar:

* [Resurs klasser för hantering av arbets belastning](resource-classes-for-workload-management.md)
* [Analysera din arbets belastning](analyze-your-workload.md)
