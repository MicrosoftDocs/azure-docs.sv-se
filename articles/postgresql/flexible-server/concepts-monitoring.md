---
title: Övervakning och mått – Azure Database for PostgreSQL-flexibel Server
description: Den här artikeln beskriver övervaknings-och mått funktioner i Azure Database for PostgreSQL-flexibel Server.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 5e7063cd1ae560fa077bd0b1b1279e4515e70464
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100579027"
---
# <a name="monitor-metrics-on-azure-database-for-postgresql---flexible-server"></a>Övervaka mått på Azure Database for PostgreSQL-flexibel Server

> [!IMPORTANT]
> Azure Database for PostgreSQL-flexibel Server är i för hands version

Genom att övervaka data om dina servrar kan du felsöka och optimera för din arbets belastning. Azure Database for PostgreSQL innehåller olika övervaknings alternativ för att ge inblick i serverns beteende.

## <a name="metrics"></a>Mått
Azure Database for PostgreSQL tillhandahåller olika mått som ger inblick i funktionerna i resurserna som stöder PostgreSQL-servern. Varje mått genereras med en minuters frekvens och har upp till [93 dagars historik](../../azure-monitor/essentials/data-platform-metrics.md#retention-of-metrics). Du kan konfigurera aviseringar för måtten. Andra alternativ är att ställa in automatiserade åtgärder, utföra avancerad analys och lagrings historik. Mer information finns i [Översikt över Azure Metrics](../../azure-monitor/essentials/data-platform-metrics.md).

### <a name="list-of-metrics"></a>Lista över mått
Följande mått är tillgängliga för PostgreSQL-flexibla Server:


|Metric|Mått visnings namn|Enhet|Beskrivning|
|---|---|---|---|
| active_connections | Aktiva anslutningar | Antal | Antalet anslutningar till servern. | 
| backup_storage_used | Lagring av säkerhets kopior som används | Byte | Mängden lagring av säkerhets kopior som används. Det här måttet representerar summan av lagrings utrymme som förbrukas av alla fullständiga säkerhets kopior av databasen, differentiella säkerhets kopior och logg säkerhets kopior som bevaras baserat på den kvarhållna säkerhets kopie perioden som angetts för servern. Säkerhets kopierings frekvensen är hanterad av tjänsten. För Geo-redundant lagring är lagrings utrymmet för säkerhets kopiering två gånger för det lokalt redundanta lagrings utrymmet. |
| connections_failed | Misslyckade anslutningar | Antal | Misslyckade anslutningar. |
| connections_succeeded | Lyckade anslutningar | Antal | Lyckade anslutningar. |
| cpu_credits_consumed | Förbrukade CPU-krediter | Antal | Antal krediter som används av den flexibla servern. Gäller för burst-nivå. |
| cpu_credits_remaining | Återstående CPU-krediter | Antal | Antal krediter som är tillgängliga för Burst. Gäller för burst-nivå. |
| cpu_percent | CPU-procent | Procent | Procent andel CPU som används. | 
| disk_queue_depth | Disk-ködjup | Antal | Antal utestående I/O-åtgärder till data disken. |
| IOPS | IOPS | Antal | Antal I/O-åtgärder till disk per sekund. |
| maximum_used_transactionIDs | Högsta antal använda transaktions-ID: n | Antal | Högsta transaktions-ID som används. |
| memory_percent | Minnes procent | Procent | Procent andel minne som används. |
| network_bytes_egress | Nätverk – utgående | Byte | Mängden utgående nätverks trafik. |
| network_bytes_ingress | Nätverk – inkommande | Byte | Mängden inkommande nätverks trafik. |
| read_iops | Läs IOPS | Antal | Antal Läs åtgärder för data diskar I/O per sekund. |
| read_throughput | Läs data flöde | Byte | Lästa byte per sekund från disk. |
| storage_free | Ledigt lagrings utrymme | Byte | Mängden tillgängligt lagrings utrymme. |
| storage_percent | Lagrings procent | Procent | Procent av använt lagrings utrymme. Lagrings utrymmet som används av tjänsten kan omfatta databasfilerna, transaktions loggarna och Server loggarna.|
| storage_used | Använt lagrings utrymme | Byte | Procent av använt lagrings utrymme. Lagrings utrymmet som används av tjänsten kan omfatta databasfilerna, transaktions loggarna och Server loggarna. |
| txlogs_storage_used | Transaktions logg lagring används | Byte | Mängden lagrings utrymme som används av transaktions loggarna. | 
| write_throughput | Skriv data flöde | Byte | Skrivna byte per sekund till disk. |
| write_iops | Skriv IOPS | Antal | Antal Skriv åtgärder för data diskar I/O per sekund. |

## <a name="server-logs"></a>Serverloggar
Med Azure Database for PostgreSQL kan du konfigurera och komma åt postgres standard loggar. Mer information om loggar finns i avsnittet [Logging Concepts doc](concepts-logging.md).
