---
title: Konfigurationsalternativ – Hyperskala (Citus) – Azure Database for PostgreSQL
description: Alternativ för en Hyperskala (Citus) servergrupp, inklusive nodbearbetning, lagring och regioner.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.custom: references_regions
ms.date: 04/07/2021
ms.openlocfilehash: 1dd0666c2946896ed324fb3986bb7946890b73de
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388711"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-configuration-options"></a>Azure Database for PostgreSQL – Hyperskala (Citus) konfigurationsalternativ

## <a name="compute-and-storage"></a>Beräkning och lagring
 
Du kan välja beräknings- och lagringsinställningar separat för arbetsnoder och koordinatornoden i en Hyperskala (Citus)-servergrupp.  Beräkningsresurser tillhandahålls som virtuella kärnor som representerar den underliggande maskinvarans logiska processor. Lagringsstorleken för etablering avser den kapacitet som är tillgänglig för koordinatorn och arbetsnoderna i Hyperskala (Citus) servergruppen. Lagringen omfattar databasfiler, temporära filer, transaktionsloggar och Postgres-serverloggar.

### <a name="standard-tier"></a>Standard-nivå
 
| Resurs              | Arbetsnod           | Koordinatornod      |
|-----------------------|-----------------------|-----------------------|
| Beräkning, virtuella kärnor       | 4, 8, 16, 32, 64      | 4, 8, 16, 32, 64      |
| Minne per vCore, GiB | 8                     | 4                     |
| Lagringsstorlek, TiB     | 0.5, 1, 2             | 0.5, 1, 2             |
| Lagringstyp          | Generell användning (SSD) | Generell användning (SSD) |
| IOPS                  | Upp till 3 IOPS/GiB      | Upp till 3 IOPS/GiB      |

Den totala mängden RAM-minne Hyperskala (Citus) en enskild nod baseras på det valda antalet virtuella kärnor.

| Virtuella kärnor | En arbetsnod, GiB RAM | Koordinatornod, GiB RAM |
|--------|--------------------------|---------------------------|
| 4      | 32                       | 16                        |
| 8      | 64                       | 32                        |
| 16     | 128                      | 64                        |
| 32     | 256                      | 128                       |
| 64     | 432                      | 256                       |

Den totala mängden lagringsutrymme som du etablerar definierar även den I/O-kapacitet som är tillgänglig för varje arbetsnod och koordinatornod.

| Lagringsstorlek, TiB | Högsta IOPS |
|-------------------|--------------|
| 0,5               | 1,536        |
| 1                 | 3,072        |
| 2                 | 6,148        |

För hela Hyperskala (Citus) fungerar den aggregerade IOPS ut till följande värden:

| Arbetsnoder | 0,5 TiB, totalt IOPS | 1 TiB, totalt IOPS | 2 TiB, totalt IOPS |
|--------------|---------------------|-------------------|-------------------|
| 2            | 3,072               | 6,144             | 12,296            |
| 3            | 4,608               | 9,216             | 18,444            |
| 4            | 6,144               | 12,288            | 24,592            |
| 5            | 7,680               | 15,360            | 30,740            |
| 6            | 9,216               | 18,432            | 36,888            |
| 7            | 10,752              | 21,504            | 43,036            |
| 8            | 12,288              | 24,576            | 49,184            |
| 9            | 13,824              | 27,648            | 55,332            |
| 10           | 15,360              | 30,720            | 61,480            |
| 11           | 16,896              | 33,792            | 67,628            |
| 12           | 18,432              | 36,864            | 73,776            |
| 13           | 19,968              | 39,936            | 79,924            |
| 14           | 21,504              | 43,008            | 86,072            |
| 15           | 23,040              | 46,080            | 92,220            |
| 16           | 24,576              | 49,152            | 98,368            |
| 17           | 26,112              | 52,224            | 104,516           |
| 18           | 27,648              | 55,296            | 110,664           |
| 19           | 29,184              | 58,368            | 116,812           |
| 20           | 30,720              | 61,440            | 122,960           |

### <a name="basic-tier-preview"></a>Basic-nivå (förhandsversion)

> [!IMPORTANT]
> Den Hyperskala (Citus) Basic-nivån är för närvarande i förhandsversion.  Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
>
> Du kan se en fullständig lista över andra nya funktioner i [förhandsversionsfunktioner för Hyperskala (Citus)](hyperscale-preview-features.md).

Den Hyperskala (Citus) [basic-nivån](concepts-hyperscale-tiers.md) är en servergrupp med bara en nod.  Eftersom det inte finns någon skillnad mellan koordinator- och arbetsnoder är det mindre komplicerat att välja beräknings- och lagringsresurser.

| Resurs              | Tillgängliga alternativ     |
|-----------------------|-----------------------|
| Beräkning, virtuella kärnor       | 2, 4, 8               |
| Minne per vCore, GiB | 4                     |
| Lagringsstorlek, GiB     | 128, 256, 512         |
| Lagringstyp          | Generell användning (SSD) |
| IOPS                  | Upp till 3 IOPS/GiB      |

Den totala mängden RAM-minne Hyperskala (Citus) en enskild nod baseras på det valda antalet virtuella kärnor.

| Virtuella kärnor | GiB RAM |
|--------|---------|
| 2      | 8       |
| 4      | 16      |
| 8      | 32      |

Den totala mängden lagringsutrymme som du etablerar definierar även I/O-kapaciteten som är tillgänglig för noden på basic-nivån.

| Lagringsstorlek, GiB | Högsta IOPS |
|-------------------|--------------|
| 128               | 384          |
| 256               | 768          |
| 512               | 1,536        |

## <a name="regions"></a>Regioner
Hyperskala (Citus) servergrupper är tillgängliga i följande Azure-regioner:

* Americas:
    * Brasilien, södra
    * Kanada, centrala
    * Central US
    * USA, östra *
    * USA, östra 2
    * USA, norra centrala
    * USA, västra 2
* Asien och stillahavsområdet:
    * Australien, östra
    * Japan, östra
    * Sydkorea, centrala
    * Sydostasien
* Europa:
    * Frankrike, centrala
    * Norra Europa
    * Storbritannien, södra
    * Europa, västra

( \* = stöder [förhandsgranskningsfunktioner](hyperscale-preview-features.md))

Vissa av dessa regioner kanske inte först aktiveras på alla Azure-prenumerationer. Om du vill använda en region från listan ovan och inte ser den i din prenumeration, eller om du vill använda en region som inte finns med i listan, öppnar du en [supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="pricing"></a>Priser
Den senaste prisinformationen finns på sidan med [priser för tjänster.](https://azure.microsoft.com/pricing/details/postgresql/)
Om du vill se kostnaden för den konfiguration Azure Portal visar  [Azure Portal](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) månadskostnaden på fliken Konfigurera baserat på de alternativ du väljer. Om du inte har någon Azure-prenumeration kan du använda priskalkylatorn för Azure för att få ett beräknat pris. På webbplatsen [för Priskalkylatorn](https://azure.microsoft.com/pricing/calculator/) för Azure  väljer du **Lägg** till objekt, **expanderar** kategorin Databaser och väljer Azure Database for PostgreSQL – Hyperskala (Citus) för att anpassa alternativen.
 
## <a name="next-steps"></a>Nästa steg
Lär dig hur [du skapar Hyperskala (Citus) en servergrupp i portalen](quickstart-create-hyperscale-portal.md).
