---
title: Tjänst nivåer – DTU-baserad inköps modell
description: Lär dig mer om tjänst nivåer i den DTU-baserade inköps modellen för Azure SQL Database att tillhandahålla beräknings-och lagrings storlekar.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: references_regions
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.date: 10/15/2020
ms.reviewer: ''
ms.openlocfilehash: 19178359d1eeb935499a01828f7c53b123e17571
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92793185"
---
# <a name="service-tiers-in-the-dtu-based-purchase-model"></a>Tjänstnivåer i en DTU-baserad inköpsmodell
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Tjänst nivåer i den DTU-baserade inköps modellen särskiljs med en mängd beräknings storlekar med en fast mängd av lagrings utrymme, fast kvarhållningsperiod för säkerhets kopieringar och fast pris. Alla tjänst nivåer i den DTU-baserade inköps modellen ger flexibilitet i att ändra beräknings storlekar med minimal [stillestånds](https://azure.microsoft.com/support/legal/sla/sql-database/v1_2/)tid. Det finns dock en växel över tid där anslutningen förloras till databasen under en kort tids period, vilket kan minskas med logiken för omprövning. Enkla databaser och elastiska pooler faktureras per timme baserat på tjänst nivå och beräknings storlek.

> [!IMPORTANT]
> Den [hanterade Azure SQL-instansen](../managed-instance/sql-managed-instance-paas-overview.md) stöder inte en DTU-baserad inköps modell. 


> [!NOTE]
> Information om vCore-baserade tjänst nivåer finns i [vCore-baserade tjänst nivåer](service-tiers-vcore.md). Information om differentiering av DTU-baserade tjänst nivåer och vCore-baserade tjänst nivåer finns i [inköps modeller](purchasing-models.md).

## <a name="compare-the-dtu-based-service-tiers"></a>Jämför de DTU-baserade tjänst nivåerna

Att välja en tjänst nivå beror främst på verksamhets kontinuitet, lagring och prestanda krav.

||Basic|Standard|Premium|
| :-- | --: |--:| --:|
|**Mål arbets belastning**|Utveckling och produktion|Utveckling och produktion|Utveckling och produktion|
|**SLA för drift tid**|99,99 %|99,99 %|99,99 %|
|**Högsta kvarhållning av säkerhets kopior**|7 dagar|35 dagar|35 dagar|
|**Processor**|Låg|Låg, medel, hög|Medel, hög|
|**IOPS (ungefärlig)**\* |1-4 IOPS per DTU| 1-4 IOPS per DTU | 25 IOPS per DTU|
|**I/o-latens (ungefärligt)**|5 ms (läsning), 10 ms (Skriv)|5 ms (läsning), 10 ms (Skriv)|2 ms (Läs/skriv)|
|**Columnstore-indexering** |Ej tillämpligt|S3 och högre|Stöds|
|**Minnes intern OLTP**|Saknas|Saknas|Stöds|

\* Alla Läs-och skriv-IOPS mot datafiler, inklusive Background IO (Checkpoint och Lazy Writer)

> [!IMPORTANT]
> Målen Basic, S0, S1 och S2 tillhandahåller mindre än en vCore (CPU).  För CPU-intensiva arbets belastningar rekommenderas ett tjänst mål på S3 eller högre. 
>
> I de grundläggande, S0-och S1-tjänst målen lagras databasfiler i Azure standard Storage, som använder hård disk (HDD) lagrings medier. Dessa tjänst mål passar bäst för utveckling, testning och andra arbets belastningar som inte används ofta, vilket är mindre känsligt för prestanda variationer.
>

> [!TIP]
> Om du vill se faktiska [resurs styrnings](resource-limits-logical-server.md#resource-governance) gränser för en databas eller elastisk pool, frågar du vyn [sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) .

> [!NOTE]
> Du kan få en kostnads fri databas i Azure SQL Database på tjänst nivån Basic tillsammans med ett kostnads fritt Azure-konto för att utforska Azure. Mer information finns i [skapa en hanterad moln databas med ditt kostnads fria Azure-konto](https://azure.microsoft.com/free/services/sql-database/).

## <a name="single-database-dtu-and-storage-limits"></a>Gränser för DTU och lagring för enkla databaser

Beräknings storlekar uttrycks i databas transaktions enheter (DTU: er) för enskilda databaser och elastiska databas transaktions enheter (eDTU: er) för elastiska pooler. Mer information om DTU: er och eDTU: er finns i [DTU-baserad inköps modell](purchasing-models.md#dtu-based-purchasing-model).

||Basic|Standard|Premium|
| :-- | --: | --: | --: |
| **Maximal lagrings storlek** | 2 GB | 1 TB | 4 TB  |
| **Maximalt DTU: er** | 5 | 3000 | 4000 |

> [!IMPORTANT]
> Under vissa omständigheter kan du behöva krympa en databas för att frigöra utrymme som inte används. Mer information finns i [Hantera fil utrymme i Azure SQL Database](file-space-manage.md).

## <a name="elastic-pool-edtu-storage-and-pooled-database-limits"></a>Gränser för eDTU-, lagrings-och databas i elastisk pool

|| **Basic** | **Standard** | **Premium** |
| :-- | --: | --: | --: |
| **Maximal lagrings storlek per databas**  | 2 GB | 1 TB | 1 TB |
| **Maximal lagrings storlek per pool** | 156 GB | 4 TB | 4 TB |
| **Maximalt antal eDTU: er per databas** | 5 | 3000 | 4000 |
| **Maximalt antal eDTU: er per pool** | 1600 | 3000 | 4000 |
| **Maximalt antal databaser per pool** | 500  | 500 | 100 |

> [!IMPORTANT]
> Mer än 1 TB lagrings utrymme på Premium-nivån är för närvarande tillgängligt i alla regioner utom: Kina, östra, Kina, norra, Tyskland, centrala och Tyskland nordöstra. I dessa regioner är det maximala lagringsutrymmet på Premium-nivån begränsat till 1 TB.  Mer information finns i [Aktuella begränsningar för P11–P15](single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).  
> [!IMPORTANT]
> Under vissa omständigheter kan du behöva krympa en databas för att frigöra utrymme som inte används. Mer information finns i [Hantera fil utrymme i Azure SQL Database](file-space-manage.md).

## <a name="dtu-benchmark"></a>Prestandatest för DTU

Fysiska egenskaper (CPU, minne, IO) kopplade till varje DTU-mått kalibreras med hjälp av ett riktmärke som simulerar databas arbets belastningen i verkligheten.

### <a name="correlating-benchmark-results-to-real-world-database-performance"></a>Korrelation av benchmark-resultat till verkliga prestanda i världs databas

Det är viktigt att förstå att alla benchmarks är representativa och endast preliminära. De transaktions priser som uppnås med benchmark-programmet är inte samma som de som kan uppnås med andra program. Benchmark omfattar en samling olika transaktions typer som körs mot ett schema som innehåller en rad tabeller och data typer. Även om benchmark-övningarna samma grundläggande åtgärder som är gemensamma för alla OLTP-arbetsbelastningar, representerar den inte någon specifik klass av databas eller program. Syftet med benchmarkn är att tillhandahålla en rimlig vägledning för den relativa prestandan hos en databas som kan förväntas när du skalar upp eller ned mellan beräknings storlekarna. I verkligheten är databaser av olika storlekar och komplexitet, utökar olika blandningar av arbets belastningar och kommer att svara på olika sätt. Ett i/o-intensiva program kan till exempel nå ut i/o-tröskelvärden snart, eller ett processor intensivt program kan ha nått processor gränserna tidigare. Det finns ingen garanti för att en viss databas kommer att skalas på samma sätt som benchmark under ökande belastning.

Benchmark och dess metoder beskrivs i detalj nedan.

### <a name="benchmark-summary"></a>Benchmark-Sammanfattning

Benchmark mäter prestanda för en blandning av grundläggande databas åtgärder som inträffar oftast i OLTP-arbetsbelastningar (Online Transaction Processing). Även om Benchmark är utformat med molnbaserad data behandling, har databasschemat, data populationen och transaktioner utformats för att vara i stort sett representativt för de grundläggande element som vanligt vis används i OLTP-arbetsbelastningar.

### <a name="schema"></a>Schema

Schemat har utformats för att ha tillräckligt många sorts och komplexitet för att stödja en mängd olika åtgärder. Benchmark körs mot en databas som består av sex tabeller. Tabellerna hamnar i tre kategorier: fast storlek, skalning och tillväxt. Det finns två tabeller med fast storlek. tre skalnings tabeller; och en växande tabell. Tabeller med fast storlek har ett konstant antal rader. Skalnings tabeller har en kardinalitet som är proportionell till databasens prestanda, men som inte ändras under benchmark. Den växande tabellen är en storlek som en skalnings tabell vid inledande belastning, men sedan ändras kardinalitet i takt med att du kör det benchmark som rader infogas och tas bort.

Schemat innehåller en blandning av data typer, inklusive heltal, siffror, tecken och datum/tid. Schemat inkluderar primära och sekundära nycklar, men inte några främmande nycklar, det finns inga begränsningar för referens integritet mellan tabeller.

Ett data Genererings program genererar data för den inledande databasen. Heltal och numeriska data genereras med olika strategier. I vissa fall distribueras värden slumpmässigt över ett intervall. I andra fall är en uppsättning värden slumpmässigt permuted för att säkerställa att en speciell fördelning upprätthålls. Textfält genereras från en viktad lista med ord för att producera realistiska data.

Databasen är storleks beroende av en "skalnings faktor". Skalnings faktorn (förkortat som SF) bestämmer kardinalitet för skalnings-och växande tabeller. Som beskrivs nedan i avsnittet användare och hastighets styrningen, databasens storlek, antal användare och högsta prestanda skala i förhållande till varandra.

### <a name="transactions"></a>Transaktioner

Arbets belastningen består av nio transaktions typer som visas i tabellen nedan. Varje transaktion är utformad för att markera en viss uppsättning system egenskaper i databas motorn och systemets maskin vara, med hög kontrast från de andra transaktionerna. Den här metoden gör det enklare att utvärdera påverkan av olika komponenter till den totala prestandan. Till exempel ger transaktionen "Read tung" ett stort antal Läs åtgärder från disken.

| Transaktionstyp | Beskrivning |
| --- | --- |
| Läs lite |Select minnes intern; skrivskyddad |
| Läs medium |Select främst i minnet. skrivskyddad |
| Läs tung |Select främst inte i minnet; skrivskyddad |
| Uppdatera lite |UPPDATERA minnes intern; Läs-och skriv åtgärd |
| Uppdatera tung |UPPDATERA främst inte i minnet; Läs-och skriv åtgärd |
| Infoga lite |MATNING minnes intern; Läs-och skriv åtgärd |
| Infoga tung |MATNING främst inte i minnet; Läs-och skriv åtgärd |
| Ta bort |TA bort blandning av minne i minnet och inte i minnet. Läs-och skriv åtgärd |
| Hög CPU |Select minnes intern; relativt tung CPU-belastning; skrivskyddad |

### <a name="workload-mix"></a>Arbets belastnings mix

Transaktioner väljs slumpmässigt från en viktad distribution med följande övergripande kombination. Den övergripande mixen har en Läs/skriv-kvot på ungefär 2:1.

| Transaktionstyp | % av mix |
| --- | --- |
| Läs lite |35 |
| Läs medium |20 |
| Läs tung |5 |
| Uppdatera lite |20 |
| Uppdatera tung |3 |
| Infoga lite |3 |
| Infoga tung |2 |
| Ta bort |2 |
| Hög CPU |10 |

### <a name="users-and-pacing"></a>Användare och hastighets styrningen

Benchmark-arbetsbelastningen drivs från ett verktyg som skickar transaktioner över en uppsättning anslutningar för att simulera beteendet för ett antal samtidiga användare. Även om alla anslutningar och transaktioner är maskin skapade, är det enklare att se dessa anslutningar som "användare". Även om varje användare fungerar oberoende av alla andra användare utför alla användare samma cykel steg som visas nedan:

1. Upprätta en databas anslutning.
2. Upprepa tills signalen har avslut ATS:
   - Välj en transaktion slumpmässigt (från en viktad distribution).
   - Utför den valda transaktionen och mäta svars tiden.
   - Vänta en hastighets styrningen fördröjning.
3. Stäng databas anslutningen.
4. Programmet.

Hastighets styrningen fördröjningen (i steg 2C) väljs slumpmässigt, men med en distribution som har ett genomsnitt på 1,0 sekund. Varje användare kan därför, i genomsnitt, generera högst en transaktion per sekund.

### <a name="scaling-rules"></a>Skalnings regler

Antalet användare bestäms av databasens storlek (i Scale-Factor units). Det finns en användare för var femte enhets faktor. På grund av hastighets styrningen-fördröjningen kan en användare generera högst en transaktion per sekund i genomsnitt.

Till exempel kommer en skalnings faktor på 500 (SF = 500)-databasen ha 100-användare och få en högsta hastighet på 100-TPS. För att kunna köra en högre TPS-hastighet krävs fler användare och en större databas.

### <a name="measurement-duration"></a>Varaktighet för mätning

En giltig benchmark-körning kräver en varaktighet på stabilt tillstånd på minst en timme.

### <a name="metrics"></a>Mått

Nyckel måtten i Benchmark är data flöde och svars tid.

- Data flödet är det viktigaste prestanda måttet i benchmark. Data flödet rapporteras i transaktioner per enhets tid och räknar alla transaktions typer.
- Svars tiden är ett mått på förutsägbar prestanda. Svars tids begränsningen varierar beroende på tjänst klass, med högre service klasser med en mer strängare svars tids krav, som visas nedan.

| Tjänst klass | Data flödes mått | Svars tids krav |
| --- | --- | --- |
| Premium |Transaktioner per sekund |95 percentil vid 0,5 sekunder |
| Standard |Transaktioner per minut |90: e percentilen vid 1,0 sekunder |
| Grundläggande |Transaktioner per timma |80th percentil vid 2,0 sekunder |

## <a name="next-steps"></a>Nästa steg

- Mer information om de olika beräknings storlekarna och de lagrings storlekar som är tillgängliga för enskilda databaser finns i [SQL Database DTU-baserade resurs gränser för enskilda databaser](resource-limits-dtu-single-databases.md#single-database-storage-sizes-and-compute-sizes).
- Mer information om de olika beräknings storlekarna och de lagrings storlekar som är tillgängliga för elastiska pooler finns i [SQL Database DTU-baserade resurs gränser](resource-limits-dtu-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes).