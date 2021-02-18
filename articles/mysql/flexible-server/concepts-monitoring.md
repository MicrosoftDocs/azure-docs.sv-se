---
title: Övervakning – Azure Database for MySQL – flexibel Server
description: Den här artikeln beskriver måtten för övervakning och avisering för Azure Database for MySQL flexibel Server, inklusive CPU, lagring och anslutnings statistik.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 9/21/2020
ms.openlocfilehash: c2883a2ddba9a322c09a318aa9f232de5f4704b4
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100595165"
---
# <a name="monitor-azure-database-for-mysql-flexible-servers-with-built-in-metrics"></a>Övervaka Azure Database for MySQL flexibla servrar med inbyggda mått

> [!IMPORTANT] 
> Azure Database for MySQL-flexibel Server är för närvarande en offentlig för hands version.

Azure Database for MySQL flexibel Server tillhandahåller övervakning av servrar via Azure Monitor. Mått är numeriska värden som beskriver viss aspekt av serverns resurser vid en viss tidpunkt. Genom att övervaka serverns resurser kan du felsöka och optimera arbets belastningen genom att göra det möjligt för dig att övervaka vad som är mest viktigt för dig. Genom att övervaka rätt mått kan du hålla prestanda, tillförlitlighet och tillgänglighet för din server och dina program.

I den här artikeln får du lära dig mer om de olika mått som är tillgängliga för din flexibla server som ger inblick i serverns beteende.

## <a name="available-metrics"></a>Tillgängliga mått

Azure Database for MySQL flexibel Server ger olika mått för att förstå hur arbets belastningen presterar och baseras på dessa data, så att du kan förstå påverkan på servern och programmet. I flexibel Server kan du till exempel övervaka **värd processor procent**, **aktiva anslutningar**, IO- **procent** och **värdens minnes procent** för att identifiera när prestanda påverkas. Därifrån kan du behöva optimera arbets belastningen, skala lodrätt genom att ändra beräknings nivåer eller skala vågrätt med hjälp av Läs replik.

Alla Azure-mått har en frekvens på en minut och varje mått ger 30 dagars historik. Du kan konfigurera aviseringar för måtten. Steg-för-steg-anvisningar finns i [så här konfigurerar du aviseringar](./how-to-alert-on-metric.md). Andra uppgifter är att ställa in automatiserade åtgärder, utföra avancerad analys och lagrings historik. Mer information finns i [Översikt över Azure Metrics](../../azure-monitor/data-platform.md).

### <a name="list-of-metrics"></a>Lista över mått
De här måtten är tillgängliga för Azure Database for MySQL:

|Mått visnings namn|Metric|Enhet|Description|
|---|---|---|---|
|Värd processor procent|cpu_percent|Procent|Procent andelen av processor användningen på servern, inklusive processor användning från både kund arbets belastning och Azure MySQL-processer|
|Värd nätverk i |network_bytes_ingress|Byte|Inkommande nätverks trafik på servern, inklusive trafik från både kund databas och Azure MySQL-funktioner som replikering, övervakning, loggar osv.|
|Värd Nätverk – utgående|network_bytes_egress|Byte|Utgående nätverks trafik på servern, inklusive trafik från både kund databas och Azure MySQL-funktioner som replikering, övervakning, loggar osv.|
|Fördröjning för replikering|replication_lag|Sekunder|Tiden sedan den senaste återspelade transaktionen. Det här måttet är endast tillgängligt för replik servrar.|
|Aktiva anslutningar|active_connection|Antal|Antalet aktiva anslutningar till servern.|
|Lagring av säkerhets kopior som används|backup_storage_used|Byte|Mängden lagring av säkerhets kopior som används.|
|I/o procent|io_consumption_percent|Procent|Procent andelen av IO som används.|
|Värdens minnes procent|memory_percent|Procent|Procent andelen minne som används på servern, inklusive minnes användning från både kund arbets belastning och Azure MySQL-processer|
|Lagrings gräns|storage_limit|Byte|Det maximala lagrings utrymmet för den här servern.|
|Lagrings procent|storage_percent|Procent|Procent andelen lagring som används av serverns högsta värde.|
|Använt lagrings utrymme|storage_used|Byte|Mängden lagring som används. Lagrings utrymmet som används av tjänsten kan omfatta databasfilerna, transaktions loggarna och Server loggarna.|
|Totalt antal anslutningar|total_connections|Antal|Antalet totala anslutningar till servern|
|Avbrutna anslutningar|aborted_connections|Antal|Antalet misslyckade försök att ansluta till MySQL, till exempel misslyckad anslutning på grund av felaktiga autentiseringsuppgifter.|
|Frågor|skickar|Antal|Antal frågor per sekund|

## <a name="next-steps"></a>Nästa steg
- Se [hur du ställer in aviseringar](./how-to-alert-on-metric.md) för vägledning om hur du skapar en avisering på ett mått.
- Lär dig mer om hur du [skalar IOPS](./concepts/../concepts-compute-storage.md#iops) för att förbättra prestanda.