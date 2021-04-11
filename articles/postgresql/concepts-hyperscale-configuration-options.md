---
title: Konfigurations alternativ – storskalig (citus)-Azure Database for PostgreSQL
description: Alternativ för en citus-Server (), inklusive Node Compute, Storage och regions.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.custom: references_regions
ms.date: 04/07/2021
ms.openlocfilehash: ae416c9acd03b3ee239a858aae550fb87293465a
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012793"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-configuration-options"></a>Konfigurations alternativ för Azure Database for PostgreSQL – citus-storlek

## <a name="compute-and-storage"></a>Beräkning och lagring
 
Du kan välja beräknings-och lagrings inställningar oberoende för arbetsnoder och koordinator-noden i en citus-Server grupp.  Beräknings resurser tillhandahålls som virtuella kärnor, som representerar den underliggande maskin varans logiska processor. Lagrings storleken för etablering avser kapaciteten som är tillgänglig för koordinatorn och arbetsnoder i citus-servergruppen (). Lagrings utrymmet inkluderar databasfiler, temporära filer, transaktions loggar och postgres-Server loggarna.

### <a name="standard-tier"></a>Standard-nivå
 
| Resurs              | Arbetsnoden           | Koordinator-nod      |
|-----------------------|-----------------------|-----------------------|
| Compute, virtuella kärnor       | 4, 8, 16, 32, 64      | 4, 8, 16, 32, 64      |
| Minne per vCore, GiB | 8                     | 4                     |
| Lagrings storlek, TiB     | 0,5, 1, 2             | 0,5, 1, 2             |
| Lagringstyp          | Generell användning (SSD) | Generell användning (SSD) |
| IOPS                  | Upp till 3 IOPS/GiB      | Upp till 3 IOPS/GiB      |

Den totala mängden RAM-minne i en citus-nod (Single-Scale) baseras på det valda antalet virtuella kärnor.

| Virtuella kärnor | En arbetsnode, GiB RAM | Koordinator-nod, GiB RAM |
|--------|--------------------------|---------------------------|
| 4      | 32                       | 16                        |
| 8      | 64                       | 32                        |
| 16     | 128                      | 64                        |
| 32     | 256                      | 128                       |
| 64     | 432                      | 256                       |

Den totala mängden lagrings utrymme som du tillhandahåller definierar också den I/O-kapacitet som är tillgänglig för varje anställd och koordinator-nod.

| Lagrings storlek, TiB | Högsta IOPS |
|-------------------|--------------|
| 0,5               | 1,536        |
| 1                 | 3,072        |
| 2                 | 6 148        |

För hela superscale-klustret (citus) kan aggregerade IOPS fungera till följande värden:

| Arbetsnoder | 0,5 TiB, total IOPS | 1 TiB, total IOPS | 2 TiB, total IOPS |
|--------------|---------------------|-------------------|-------------------|
| 2            | 3,072               | 6,144             | 12 296            |
| 3            | 4 608               | 9 216             | 18 444            |
| 4            | 6,144               | 12 288            | 24 592            |
| 5            | 7 680               | 15 360            | 30 740            |
| 6            | 9 216               | 18 432            | 36 888            |
| 7            | 10 752              | 21 504            | 43 036            |
| 8            | 12 288              | 24 576            | 49 184            |
| 9            | 13 824              | 27 648            | 55 332            |
| 10           | 15 360              | 30 720            | 61 480            |
| 11           | 16 896              | 33 792            | 67 628            |
| 12           | 18 432              | 36 864            | 73 776            |
| 13           | 19 968              | 39 936            | 79 924            |
| 14           | 21 504              | 43 008            | 86 072            |
| 15           | 23 040              | 46 080            | 92 220            |
| 16           | 24 576              | 49 152            | 98 368            |
| 17           | 26 112              | 52 224            | 104 516           |
| 18           | 27 648              | 55 296            | 110 664           |
| 19           | 29 184              | 58 368            | 116 812           |
| 20           | 30 720              | 61 440            | 122 960           |

### <a name="basic-tier-preview"></a>Basic-nivå (för hands version)

> [!IMPORTANT]
> Nivån för den storskaliga (citus) Basic-nivån är för närvarande en för hands version.  Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
>
> Du kan se en fullständig lista över andra nya funktioner i [för hands versions funktioner för skalning (citus)](hyperscale-preview-features.md).

Den storskaliga (citus) [Basic-nivån](concepts-hyperscale-tiers.md) är en Server grupp med bara en nod.  Eftersom det inte finns någon skillnad mellan koordinator-och arbetsnoder, är det mindre komplicerat att välja beräknings-och lagrings resurser.

| Resurs              | Tillgängliga alternativ     |
|-----------------------|-----------------------|
| Compute, virtuella kärnor       | 2, 4, 8               |
| Minne per vCore, GiB | 4                     |
| Lagrings storlek, GiB     | 128, 256, 512         |
| Lagringstyp          | Generell användning (SSD) |
| IOPS                  | Upp till 3 IOPS/GiB      |

Den totala mängden RAM-minne i en citus-nod (Single-Scale) baseras på det valda antalet virtuella kärnor.

| Virtuella kärnor | GiB RAM |
|--------|---------|
| 2      | 8       |
| 4      | 16      |
| 8      | 32      |

Den totala mängden lagrings utrymme som du tillhandahåller definierar också den I/O-kapacitet som är tillgänglig för noden för Basic-nivån.

| Lagrings storlek, GiB | Högsta IOPS |
|-------------------|--------------|
| 128               | 384          |
| 256               | 768          |
| 512               | 1,536        |

## <a name="regions"></a>Regioner
Citus-Server grupper är tillgängliga i följande Azure-regioner:

* Amerika
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
* Östeuropa
    * Europa, norra
    * Storbritannien, södra
    * Europa, västra

( \* = stöder för [hands versions funktioner](hyperscale-preview-features.md))

Vissa av dessa regioner kanske inte inlednings vis aktive ras på alla Azure-prenumerationer. Om du vill använda en region från listan ovan och inte ser den i din prenumeration, eller om du vill använda en region som inte finns med i listan, öppnar du en [supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="pricing"></a>Priser
Den senaste pris informationen finns på [sidan med pris](https://azure.microsoft.com/pricing/details/postgresql/)information för tjänsten.
Om du vill se kostnaden för den konfiguration du vill använda visar [Azure Portal](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) månads kostnaden på fliken **Konfigurera** baserat på de alternativ du väljer. Om du inte har någon Azure-prenumeration kan du använda pris Kalkylatorn för Azure för att få ett uppskattat pris. På webbplatsen för [Azures pris kalkylator](https://azure.microsoft.com/pricing/calculator/) väljer du **Lägg till objekt**, expanderar kategorin **databaser** och väljer **Azure Database for PostgreSQL – citus)** för att anpassa alternativen.
 
## <a name="next-steps"></a>Nästa steg
Lär dig hur du [skapar en citus-Server (för skalning) i portalen](quickstart-create-hyperscale-portal.md).
