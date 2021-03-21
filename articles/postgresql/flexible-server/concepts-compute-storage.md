---
title: Beräknings-och lagrings alternativ – Azure Database for PostgreSQL-flexibel Server
description: I den här artikeln beskrivs beräknings-och lagrings alternativen i Azure Database for PostgreSQL-flexibel Server.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/19/2021
ms.openlocfilehash: b76b6ff788d3d7f44db33af96944d528282f0ac7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101712223"
---
# <a name="compute-and-storage-options-in-azure-database-for-postgresql---flexible-server"></a>Beräknings-och lagrings alternativ i Azure Database for PostgreSQL-flexibel Server

> [!IMPORTANT]
> Azure Database for PostgreSQL-flexibel Server är i för hands version

Du kan skapa en Azure Database for PostgreSQL server på någon av tre olika pris nivåer: överförings bara, Generell användning och Minnesoptimerade. Pris nivåerna åtskiljs av mängden data bearbetning i virtuella kärnor som kan tillhandahållas, minne per vCore och lagrings teknik som används för att lagra data. Alla resurser har allokerats på PostgreSQL server nivå. En server kan ha en eller flera databaser.

| Resurs/nivå | **Burstable** | **Generell användning** | **Minnesoptimerade** |
|:---|:----------|:--------------------|:---------------------|
| Virtuella kärnor | 1, 2 | 2, 4, 8, 16, 32, 48, 64 | 2, 4, 8, 16, 32, 48, 64 |
| Minne per vCore | Variabel | 4 GB | 6,75 till 8 GB |
| Lagrings storlek | 32 GB till 16 TB | 32 GB till 16 TB | 32 GB till 16 TB |
| Kvarhållningsperiod för databas säkerhets kopior | 7 till 35 dagar | 7 till 35 dagar | 7 till 35 dagar |

Om du vill välja en pris nivå använder du följande tabell som utgångs punkt.

| Prisnivå | Målbelastningar |
|:-------------|:-----------------|
| Burstable | Bäst för arbets belastningar som inte behöver full CPU kontinuerligt. |
| Generell användning | De flesta företags arbets belastningar som kräver balanserade beräkning och minne med skalbart I/O-genomflöde. Några exempel kan vara servrar som är värd för webb- och mobilappar och andra företagsprogram.|
| Minnesoptimerad | Databas arbets belastningar med höga prestanda som kräver minnes prestanda för snabbare bearbetning av transaktioner och högre samtidighet. Exempel på det är servrar för att bearbeta realtidsdata och transaktionsappar eller analysappar med höga prestanda.|

När du har skapat en server kan beräknings nivån, antalet virtuella kärnor och lagrings storlek ändras upp eller ned inom några sekunder. Du kan också självständigt justera säkerhets kopierings perioden upp eller ned. Mer information finns i avsnittet [Scale Resources](#scale-resources) .

## <a name="compute-tiers-vcores-and-server-types"></a>Beräknings nivåer, virtuella kärnor och Server typer

Beräknings resurser kan väljas baserat på nivån, virtuella kärnor och minnes storleken. Virtuella kärnor representerar den underliggande maskin varans logiska processor.

De detaljerade specifikationerna för de tillgängliga Server typerna är följande:

| SKU-namn             | Virtuella kärnor | Minnes storlek | Högsta antal IOPS som stöds | Maximal I/O-bandbredd som stöds |
|----------------------|--------|-------------|--------------------|-----------------------------|
| **Burstable**        |        |             |                    |                             |
| B1ms                 | 1      | 2 GiB       | 640                | 15 MiB/SEK                  |
| B2s                  | 2      | 4 GiB       | 1280               | 15 MiB/SEK                  |
| **Generell användning**  |        |             |                    |                             |
| D2s_v3               | 2      | 8 GiB       | 3200               | 48 MiB/SEK                  |
| D4s_v3               | 4      | 16 GiB      | 6400               | 96 MiB/SEK                  |
| D8s_v3               | 8      | 32 GiB      | 12800              | 192 MiB/SEK                 |
| D16s_v3              | 16     | 64 GiB      | 18000              | 384 MiB/SEK                 |
| D32s_v3              | 32     | 128 GiB     | 18000              | 750 MiB/SEK                 |
| D48s_v3              | 48     | 192 GiB     | 18000              | 750 MiB/SEK                 |
| D64s_v3              | 64     | 256 GiB     | 18000              | 750 MiB/SEK                 |
| **Minnesoptimerade** |        |             |                    |                             |
| E2s_v3               | 2      | 16 GiB      | 3200               | 48 MiB/SEK                  |
| E4s_v3               | 4      | 32 GiB      | 6400               | 96 MiB/SEK                  |
| E8s_v3               | 8      | 64 GiB      | 12800              | 192 MiB/SEK                 |
| E16s_v3              | 16     | 128 GiB     | 18000              | 384 MiB/SEK                 |
| E32s_v3              | 32     | 256 GiB     | 18000              | 750 MiB/SEK                 |
| E48s_v3              | 48     | 384 GiB     | 18000              | 750 MiB/SEK                 |
| E64s_v3              | 64     | 432 GiB     | 18000              | 750 MiB/SEK                 |

## <a name="storage"></a>Storage

Lagrings utrymmet du tillhandahåller är mängden lagrings kapacitet som är tillgänglig för din Azure Database for PostgreSQL-Server. Lagrings utrymmet används för databasfilerna, temporära filer, transaktions loggar och PostgreSQL-Server loggarna. Den totala mängden lagrings utrymme som du tillhandahåller definierar också den I/O-kapacitet som är tillgänglig för servern.

Storage är tillgängligt i följande fasta storlekar:

| Diskstorlek | IOPS |
|:---|:---|
| 32 GiB | Etablerade 120, upp till 3 500 |
| 64 GiB | Etablerade 240, upp till 3 500 |
| 128 GiB | Etablerade 500, upp till 3 500 |
| 256 GiB | Etablerade 1100, upp till 3 500 |
| 512 GiB | Etablerade 2300, upp till 3 500 |
| 1 TiB | 5 000 |
| 2 TiB | 7 500 |
| 4 TiB | 7 500 |
| 8 TiB | 16 000 |
| 16 TiB | 18 000 |

Observera att IOPS också begränsas av din VM-typ. Även om du kan välja valfri lagrings storlek oberoende av Server typen, kanske du inte kan använda alla IOPS som lagringen tillhandahåller, särskilt när du väljer en server med ett litet antal virtuella kärnor.

Du kan lägga till ytterligare lagrings kapacitet under och efter att servern har skapats.

>[!NOTE]
> Lagringen kan bara skalas upp, inte nedåt.

Du kan övervaka i/O-förbrukningen i Azure Portal eller genom att använda Azure CLI-kommandon. De relevanta måtten för övervakning är [lagrings gränser, lagrings utrymme, lagring och i/o procent](concepts-monitoring.md).

### <a name="maximum-iops-for-your-configuration"></a>Högsta IOPS för konfigurationen

|SKU-namn            |Lagrings storlek i GiB                       |32 |64 |128 |256 |512  |1 024|2 048|4 096|8 192 |16 384|
|--------------------|------------------------------------------|---|---|----|----|-----|-----|-----|-----|------|------|
|                    |Högsta IOPS                              |120|240|500 |1100|2 300 |5000 |7 500 |7 500 |16000 |18000 |
|**Burstable**       |                                          |   |   |    |    |     |     |     |     |      |      |
|B1ms                |640 IOPS                                  |120|240|500 |640 *|640 * |640 * |640 * |640 * |640 *  |640 *  |
|B2s                 |1280 IOPS                                 |120|240|500 |1100|1280 *|1280 *|1280 *|1280 *|1280 * |1280 * |
|**Generell användning** |                                          |   |   |    |    |     |     |     |     |      |      |
|D2s_v3              |3200 IOPS                                 |120|240|500 |1100|2 300 |3200 *|3200 *|3200 *|3200 * |3200 * |
|D4s_v3              |6 400 IOPS                                |120|240|500 |1100|2 300 |5000 |6400 *|6400 *|6400 * |6400 * |
|D8s_v3              |12 800 IOPS                               |120|240|500 |1100|2 300 |5000 |7 500 |7 500 |12800 *|12800 *|
|D16s_v3             |18 000 IOPS                               |120|240|500 |1100|2 300 |5000 |7 500 |7 500 |16000 |18000 |
|D32s_v3             |18 000 IOPS                               |120|240|500 |1100|2 300 |5000 |7 500 |7 500 |16000 |18000 |
|D48s_v3             |18 000 IOPS                               |120|240|500 |1100|2 300 |5000 |7 500 |7 500 |16000 |18000 |
|D64s_v3             |18 000 IOPS                               |120|240|500 |1100|2 300 |5000 |7 500 |7 500 |16000 |18000 |
|**Minnesoptimerade**|                                          |   |   |    |    |     |     |     |     |      |      |
|E2s_v3              |3200 IOPS                                 |120|240|500 |1100|2 300 |3200 *|3200 *|3200 *|3200 * |3200 * |
|E4s_v3              |6 400 IOPS                                |120|240|500 |1100|2 300 |5000 |6400 *|6400 *|6400 * |6400 * |
|E8s_v3              |12 800 IOPS                               |120|240|500 |1100|2 300 |5000 |7 500 |7 500 |12800 *|12800 *|
|E16s_v3             |18 000 IOPS                               |120|240|500 |1100|2 300 |5000 |7 500 |7 500 |16000 |18000 |
|E32s_v3             |18 000 IOPS                               |120|240|500 |1100|2 300 |5000 |7 500 |7 500 |16000 |18000 |
|E48s_v3             |18 000 IOPS                               |120|240|500 |1100|2 300 |5000 |7 500 |7 500 |16000 |18000 |
|E64s_v3             |18 000 IOPS                               |120|240|500 |1100|2 300 |5000 |7 500 |7 500 |16000 |18000 |

När det markeras med en \* begränsas IOPS av den VM-typ som du har valt. Annars begränsas IOPS av den valda lagrings storleken.

>[!NOTE]
> Du kan se högre IOPS i måtten på grund av disk nivå överföring. Mer information finns i [dokumentationen](https://docs.microsoft.com/azure/virtual-machines/disk-bursting#disk-level-bursting) . 

### <a name="maximum-io-bandwidth-mibsec-for-your-configuration"></a>Maximal I/O-bandbredd (MiB/s) för din konfiguration

|SKU-namn            |Lagrings storlek, GiB                             |32 |64 |128 |256 |512  |1 024|2 048|4 096|8 192 |16 384|
|--------------------|----------------------------------------------|---|---|----|----|-----|-----|-----|-----|------|------|
|                    |**Lagrings bandbredd, MiB/s**                |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|**Burstable**       |                                              |   |   |    |    |     |     |     |     |      |      |
|B1ms                |10 MiB/SEK                                    |10|10|10 |10 |10  |10  |10  |10  |10   |10   |
|B2s                 |15 MiB/SEK                                    |15.4|15.4|15.4 |15.4 |15.4  |15.4  |15.4  |15.4  |15.4   |15.4   |
|**Generell användning** |                                              |   |   |    |    |     |     |     |     |      |      |
|D2s_v3              |48 MiB/SEK                                    |25 |48 *|48 * |48 * |48 *  |48 *  |48 *  |48 *  |48 *   |48 *   |
|D4s_v3              |96 MiB/SEK                                    |25 |50 |96 * |96 * |96 *  |96 *  |96 *  |96 *  |96 *   |96 *   |
|D8s_v3              |192 MiB/SEK                                   |25 |50 |100 |125 |150  |192 * |192 * |192 * |192 *  |192 *  |
|D16s_v3             |384 MiB/SEK                                   |25 |50 |100 |125 |150  |200  |250  |250  |384 *  |384 *  |
|D32s_v3             |750 MiB/SEK                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|D48s_v3             |750 MiB/SEK                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|D64s_v3             |750 MiB/SEK                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|**Minnesoptimerade**|                                              |   |   |    |    |     |     |     |     |      |      |
|E2s_v3              |48 MiB/SEK                                    |25 |48 *|48 * |48 * |48 *  |48 *  |48 *  |48 *  |48 *   |48 *   |
|E4s_v3              |96 MiB/SEK                                    |25 |50 |96 * |96 * |96 *  |96 *  |96 *  |96 *  |96 *   |96 *   |
|E8s_v3              |192 MiB/SEK                                   |25 |50 |100 |125 |150  |192 * |192 * |192 * |192 *  |192 *  |
|E16s_v3             |384 MiB/SEK                                   |25 |50 |100 |125 |150  |200  |250  |250  |384 *  |384 *  |
|E32s_v3             |750 MiB/SEK                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|E48s_v3             |750 MiB/SEK                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|E64s_v3             |750 MiB/SEK                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |

När du markerar med en \* begränsas i/O-bandbredden av den VM-typ som du har valt. Annars är I/O-bandbredden begränsad av den valda lagrings storleken.

### <a name="reaching-the-storage-limit"></a>Lagringsgränsen är nådd

När du når lagrings gränsen börjar servern att returnera fel och förhindra ytterligare ändringar. Detta kan också orsaka problem med andra operativa aktiviteter, t. ex. säkerhets kopiering och WAL arkivering.

För att undvika den här situationen, när lagrings användningen når 95% eller om den tillgängliga kapaciteten är mindre än 5 GiB, växlas servern automatiskt till **skrivskyddat läge**.

Vi rekommenderar att du aktivt övervakar disk utrymmet som används och ökar disk storleken före eventuell slut på lagrings situationen. Du kan ställa in en avisering för att meddela dig när Server lagringen närmar sig disk utrymme så att du kan undvika eventuella problem med att disk utrymmet tar slut. Mer information finns i dokumentationen om [hur du konfigurerar en avisering](howto-alert-on-metrics.md).


### <a name="storage-auto-grow"></a>Utöka lagring automatiskt

Automatisk storleks ökning för lagring är inte tillgängligt ännu för flexibel Server.

## <a name="backup"></a>Backup

Tjänsten tar automatiskt säkerhets kopior av servern. Du kan välja en kvarhållningsperiod från mellan 7 och 35 dagar. Läs mer om säkerhets kopieringar i [artikeln begrepp](concepts-backup-restore.md).

## <a name="scale-resources"></a>Skala resurser

När du har skapat servern kan du ändra virtuella kärnor, beräknings nivån, mängden lagring och lagrings perioden för säkerhets kopior. Antalet virtuella kärnor kan skalas upp eller ned. Kvarhållningsperioden för säkerhets kopior kan skalas upp eller ned från 7 till 35 dagar. Det går bara att öka lagrings storleken. Skalning av resurserna kan göras via portalen eller Azure CLI.

> [!NOTE]
> Det går bara att öka lagrings storleken. Du kan inte gå tillbaka till en mindre lagrings storlek efter ökningen.

När du ändrar antalet virtuella kärnor eller beräknings nivån startas servern om för att den nya server typen ska börja gälla. Under tiden då systemet växlar över till den nya servern kan inga nya anslutningar upprättas, och transaktioner som inte allokerats återställs. Hur lång tid detta tar varierar, men i de flesta fall tar det mindre än en minut. Skalning av lagringen fungerar på samma sätt och kräver även en kort omstart.

Att ändra kvarhållningsperioden för säkerhets kopior är en online-åtgärd.

## <a name="pricing"></a>Priser

Den senaste pris informationen finns på [sidan med pris](https://azure.microsoft.com/pricing/details/PostgreSQL/)information för tjänsten. Om du vill se kostnaden för den konfiguration du vill ha, visar [Azure Portal](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) månads kostnaden på fliken **pris nivå** baserat på de alternativ du väljer. Om du inte har någon Azure-prenumeration kan du använda pris Kalkylatorn för Azure för att få ett uppskattat pris. På webbplatsen för [Azures pris kalkylator](https://azure.microsoft.com/pricing/calculator/) väljer du **Lägg till objekt**, expanderar kategorin **databaser** och väljer **Azure Database for PostgreSQL** för att anpassa alternativen.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [skapar en postgresql-server i portalen](how-to-manage-server-portal.md).
- Läs mer om [tjänst begränsningar](concepts-limits.md).
