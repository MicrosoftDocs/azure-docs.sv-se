---
title: Hög tillgänglighet i Azure Cosmos DB
description: Den här artikeln beskriver hur Azure Cosmos DB ger hög tillgänglighet
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/18/2021
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: d827011c4f831433a7446c90eed0c30c7b1e94d7
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2021
ms.locfileid: "98600555"
---
# <a name="how-does-azure-cosmos-db-provide-high-availability"></a>Hur ger Azure Cosmos DB hög tillgänglighet
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB ger hög tillgänglighet på två huvudsakliga sätt. För det första replikerar Azure Cosmos DB data över regioner som kon figurer ATS inom ett Cosmos-konto. För det andra behåller Azure Cosmos DB 4 repliker av data inom en region.

Azure Cosmos DB är en globalt distribuerad databas tjänst och är en grundläggande tjänst som är tillgänglig i [alla regioner där Azure är tillgängligt](https://azure.microsoft.com/global-infrastructure/services/?products=cosmos-db&regions=all). Du kan associera ett valfritt antal Azure-regioner med ditt Azure Cosmos-konto så kommer dina data automatiskt att replikeras. Du kan när som helst lägga till eller ta bort en region i ditt Azure Cosmos-konto. Cosmos DB är tillgängligt i alla fem olika Azure Cloud-miljöer som är tillgängliga för kunder:

* **Offentligt Azure** -moln, som är tillgängligt globalt.

* **Azure Kina 21Vianet** är tillgängligt via ett unikt partnerskap mellan Microsoft och 21Vianet, en av landets största Internet leverantörer i Kina.

* **Azure Germany** tillhandahåller tjänster under en data förvaltar modell, som säkerställer att kunddata förblir i Tyskland under kontroll av T-Systems International GmbH, ett dotter bolag till tyska telekom, som fungerar som den tyska data förvaltaren.

* **Azure Government** finns i fyra regioner i USA till myndigheter i myndigheter och deras partner.

* **Azure Government för DoD (Department of försvar)** är tillgänglig i två regioner i USA till USA: s försvars departement.

I en region behåller Azure Cosmos DB fyra kopior av dina data som repliker i fysiska partitioner enligt följande bild:

:::image type="content" source="./media/high-availability/cosmosdb-data-redundancy.png" alt-text="Fysisk partitionering" border="false":::

* Data i Azure Cosmos-behållare är [vågrätt partitionerade](partitioning-overview.md).

* En partitionsuppsättning är en samling med flera replik uppsättningar. I varje region skyddas varje partition av en replik uppsättning med alla skrivningar som replikeras och varaktigt som allokeras av en majoritet av repliker. Repliker distribueras i så många som 10-20 fel domäner.

* Varje partition i alla regioner replikeras. Varje region innehåller alla datapartitioner för en Azure Cosmos-behållare och kan hantera läsningar samt betjäna skrivningar när flera regioner har Aktiver ATS.  

Om ditt Azure Cosmos-konto är fördelat i *N* Azure-regioner finns det minst *n* x 4 kopior av alla dina data. Att ha ett Azure Cosmos-konto i fler än två regioner förbättrar tillgängligheten för ditt program och ger låg latens i de associerade regionerna.

## <a name="slas-for-availability"></a>Service avtal för tillgänglighet

Azure Cosmos DB tillhandahåller omfattande service avtal som omfattar data flöde, svars tid vid 99 percentil, konsekvens och hög tillgänglighet. I tabellen nedan visas garantier för hög tillgänglighet som tillhandahålls av Azure Cosmos DB för enkla och flera region konton. För högre Skriv tillgänglighet konfigurerar du ditt Azure Cosmos-konto så att det har flera Skriv regioner.

|Åtgärdstyp  | En region |Flera regioner (skrivningar i en region)|Flera regioner (skrivningar i flera regioner) |
|---------|---------|---------|-------|
|Skrivningar    | 99,99    |99,99   |99,999|
|Läsningar     | 99,99    |99,999  |99,999|

> [!NOTE]
> I praktiken är den faktiska Skriv tillgängligheten för begränsat inaktuellitet, session, konsekvent prefix och eventuella konsekvens modeller betydligt högre än den publicerade service avtal. Den faktiska Läs tillgängligheten för alla konsekvens nivåer är betydligt högre än den publicerade service avtal.

## <a name="high-availability-with-azure-cosmos-db-in-the-event-of-regional-outages"></a>Hög tillgänglighet med Azure Cosmos DB i händelse av regionala avbrott

I sällsynta fall av regionala avbrott ser Azure Cosmos DB till att databasen alltid är hög tillgänglig. Följande information inhämtas Azure Cosmos DB under ett avbrott, beroende på din Azure Cosmos-konto konfiguration:

* Med Azure Cosmos DB, innan en Skriv åtgärd bekräftas till klienten, så är data varaktigt allokerade av ett kvorum av repliker inom den region som accepterar Skriv åtgärder. Mer information finns i [konsekvens nivåer och data flöde](./consistency-levels.md#consistency-levels-and-throughput)

* Konton med flera regioner som kon figurer ATS med flera-Write-regioner är hög tillgängliga för både skrivningar och läsningar. Regional redundans identifieras och hanteras i Azure Cosmos DB-klienten. De är också omedelbara och kräver inte några ändringar från programmet.

* Konton med en region kan förlora tillgänglighet efter ett regionalt avbrott. Vi rekommenderar alltid att du konfigurerar **minst två regioner** (helst, minst två Skriv regioner) med ditt Azure Cosmos-konto för att säkerställa hög tillgänglighet hela tiden.

### <a name="multi-region-accounts-with-a-single-write-region-write-region-outage"></a>Konton för flera regioner med en enkel skrivnings region (Skriv åtgärds avbrott)

* Under ett avbrott i Skriv region befordrar Azure Cosmos-kontot automatiskt en sekundär region som är den nya primära Skriv regionen när **aktivering av automatisk redundans** har kon figurer ATS på Azure Cosmos-kontot. När den är aktive rad kommer redundansväxlingen att ske till en annan region i ordningen för regionens prioritet som du har angett.

* När den tidigare påverkade regionen är online igen görs eventuella Skriv data som inte har repliker ATS när regionen misslyckades, görs tillgängliga via [feeden för konflikter](how-to-manage-conflicts.md#read-from-conflict-feed). Program kan läsa konflikten, lösa konflikterna baserat på programspecifik logik och skriva uppdaterade data tillbaka till Azure Cosmos-behållaren efter behov.

* När den tidigare påverkade Skriv regionen återställs blir den automatiskt tillgänglig som en Läs region. Du kan växla tillbaka till den återställda regionen som Skriv region. Du kan byta region med hjälp av [PowerShell, Azure CLI eller Azure Portal](how-to-manage-database-account.md#manual-failover). Det finns **Ingen förlust av data eller tillgänglighet** innan, under eller efter att du byter Skriv region och programmet fortfarande har hög tillgänglighet.

> [!IMPORTANT]
> Vi rekommenderar starkt att du konfigurerar de Azure Cosmos-konton som används för produktions arbets belastningar för att **Aktivera automatisk redundans**. Manuell redundans kräver anslutning mellan sekundär och primär Skriv region för att slutföra en konsekvens kontroll för att säkerställa att inga data går förlorade under redundansväxlingen. Om den primära regionen inte är tillgänglig kan konsekvens kontrollen inte slutföras och den manuella redundansväxlingen Miss lyckas, vilket leder till förlust av Skriv tillgänglighet under den tid det regionala avbrottet varar.

### <a name="multi-region-accounts-with-a-single-write-region-read-region-outage"></a>Konton med flera regioner och en enkel-eller region (Läs regions avbrott)

* Under ett avbrott i läsnings området är Azure Cosmos-konton som använder en konsekvens nivå eller en stark konsekvens med tre eller flera Läs regioner fortfarande hög tillgängliga för läsning och skrivning.

* Azure Cosmos-konton som använder stark konsekvens med två eller färre Läs regioner (som omfattar Skriv regionen läsning &) förlorar läsnings skrivnings tillgänglighet under ett Läs regions avbrott.

* Den berörda regionen kopplas från automatiskt och kommer att markeras som offline. [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) : er omdirigerar Läs anrop till nästa tillgängliga region i listan över önskade regioner.

* Om ingen av regionerna i listan över föredragna regioner är tillgänglig kan återgår anrop automatiskt till den aktuella skrivregionen.

* Inga ändringar krävs i din program kod för att hantera avbrott i läsnings området. När den berörda läsnings regionen är online synkroniseras den automatiskt med den aktuella Skriv regionen och kommer att vara tillgänglig igen för att hantera Läs begär Anden.

* Efterföljande läsningar omdirigeras till den återställda regionen utan att det krävs några ändringar i din programkod. Vid både redundans och åter anslutning av en tidigare misslyckad region, kan Läs konsekvens garantier fortsätta att följas av Azure Cosmos DB.

* Även i en sällsynt och olycklig-händelse när Azure-regionen är permanent oåterkalleligt, sker ingen data förlust om ditt Azure Cosmos-konto i flera regioner har kon figurer ATS med *stark* konsekvens. I händelse av en permanent oåterkalleligt Skriv region, ett Azure Cosmos-konto med flera regioner som kon figurer ATS med begränsad föråldrad konsekvens, är den potentiella data förlust perioden begränsad till inaktuella fönster (*k* eller *t*) där k = 100000 uppdateringar eller T = 5 minuter, vilket någonsin sker först. För session, konsekvent prefix och eventuell konsekvens, är den potentiella data förlust perioden begränsad till högst 15 minuter. Mer information om RTO och mål för Azure Cosmos DB finns i [konsekvens nivåer och data hållbarhet](./consistency-levels.md#rto)

## <a name="availability-zone-support"></a>Support för tillgänglighets zon

Förutom återhämtning i flera regioner stöder Azure Cosmos DB också **zon redundans** i regioner som stöds när du väljer en region som ska associeras med ditt Azure Cosmos-konto.

Med stöd för tillgänglighets zoner (AZ) ser Azure Cosmos DB till att repliker placeras i flera zoner inom en bestämd region för att ge hög tillgänglighet och återhämtning till zonindelade-problem. Tillgänglighetszoner ge ett service avtal på 99,995% tillgänglighet utan ändringar i svars tiden. I händelse av ett enskilt zon haveri ger zon redundans fullständig data hållbarhet med återställnings punkt = 0 och tillgänglighet med RTO = 0. Zon redundans är en kompletterande funktion för regional replikering. Zon redundans kan inte förlita sig på att uppnå regional återhämtning.

Zon redundans kan bara konfigureras när du lägger till en ny region i ett Azure Cosmos-konto. För befintliga regioner kan zon redundans aktive ras genom att ta bort regionen och sedan lägga till den igen med zon redundansen aktive rad. För ett enda region konto kräver detta att du lägger till ytterligare en region för att tillfälligt redundansväxla till och sedan ta bort och lägga till den önskade regionen med zon redundans aktive rad.

När du konfigurerar flera regions skrivningar för ditt Azure Cosmos-konto kan du välja zon redundans utan extra kostnad. I annat fall kan du läsa tabellen nedan om priser för zon-redundans-stöd. En lista över regioner där tillgänglighets zoner är tillgängliga finns i [tillgänglighets zoner](../availability-zones/az-region.md).

I följande tabell sammanfattas funktionen för hög tillgänglighet för olika konto konfigurationer:

|KPI|En region utan AZs|En region med AZs|Skrivningar med flera regioner med AZs|Flera regioner, flera region skrivningar med AZs|
|---------|---------|---------|---------|---------|
|SLA för Skriv tillgänglighet | 99,99 % | 99,995% | 99,995% | 99,999 % |
|SLA för Läs tillgänglighet  | 99,99 % | 99,995% | 99,995% | 99,999 % |
|Zon haverier – data förlust | Dataförluster | Ingen data förlust | Ingen data förlust | Ingen data förlust |
|Zon haverier – tillgänglighet | Tillgänglighets förlust | Ingen förlust av tillgänglighet | Ingen förlust av tillgänglighet | Ingen förlust av tillgänglighet |
|Regionalt avbrott – data förlust | Dataförluster |  Dataförluster | Beroende av konsekvens nivå. Mer information finns i [konsekvens, tillgänglighet och prestanda kompromisser](consistency-levels-tradeoffs.md) . | Beroende av konsekvens nivå. Mer information finns i [konsekvens, tillgänglighet och prestanda kompromisser](consistency-levels-tradeoffs.md) .
|Regionalt avbrott – tillgänglighet | Tillgänglighets förlust | Tillgänglighets förlust | Det gick inte att förlora tillgänglighet för Läs regions problem, temporärt för Skriv regions problem | Ingen förlust av tillgänglighet |
|Pris (**_1_* _) | Ej tillämpligt | Etablerade RU/s x 1,25-hastighet | Etablerade RU/s x 1,25-hastighet (_*_2_*_) | Skriv frekvens för flera regioner |

_*_1_*_ för Server lös konton för begär ande enheter (ru) multipliceras med en faktor på 1,25.

_*_2_*_ 1,25-frekvens tillämpas endast på de regioner där AZ är aktiverat.

Tillgänglighetszoner kan aktive ras via:

_ [Azure Portal](how-to-manage-database-account.md#addremove-regions-from-your-database-account)

* [Azure PowerShell](manage-with-powershell.md#create-account)

* [Azure CLI](manage-with-cli.md#add-or-remove-regions)

* [Azure Resource Manager-mallar](./manage-with-templates.md)

## <a name="building-highly-available-applications"></a>Skapa program med hög tillgänglighet

* Granska det förväntade [beteendet för Azure Cosmos-SDK](troubleshoot-sdk-availability.md) : er under dessa händelser och som är de konfigurationer som påverkar den.

* För att säkerställa hög Skriv-och Läs tillgänglighet konfigurerar du ditt Azure Cosmos-konto så att det omfattar minst två regioner med flera-Write-regioner. Med den här konfigurationen får du högsta tillgänglighet, lägsta svars tid och bästa skalbarhet för både läsningar och skrivningar som backas upp av service avtal. Mer information finns i så här [konfigurerar du ditt Azure Cosmos-konto med flera Write-regioner](tutorial-global-distribution-sql-api.md).

* För Azure Cosmos-konton med flera regioner som kon figurer ATS med en enda skrivbar region [aktiverar du automatisk redundans med hjälp av Azure CLI eller Azure Portal](how-to-manage-database-account.md#automatic-failover). När du har aktiverat automatisk redundans när det uppstår en regional katastrof, kan Cosmos DB automatiskt redundansväxla ditt konto.  

* Även om ditt Azure Cosmos-konto har hög tillgänglighet är ditt program kanske inte korrekt utformat för att hålla hög tillgänglighet. Om du vill testa hög tillgänglighet från slut punkt till slut punkt för ditt program, som en del av dina program testnings-eller haveri beredskap (DR), kan du tillfälligt inaktivera automatisk redundans för kontot, anropa den [manuella redundansväxlingen med hjälp av PowerShell, Azure CLI eller Azure Portal](how-to-manage-database-account.md#manual-failover)och sedan övervaka programmets redundans. När du är klar kan du växla tillbaka till den primära regionen och återställa automatisk redundans för kontot.

* I en globalt distribuerad databas miljö finns det ett direkt förhållande mellan konsekvens nivån och data hållbarhet i närvaro av ett områdes omfattande avbrott. När du utvecklar din verksamhets kontinuitets plan måste du förstå hur lång tid det tar innan programmet återställs fullständigt efter en störnings händelse. Tiden som krävs för att ett program ska återställas fullständigt kallas för återställnings tids mål (RTO). Du måste också förstå hur lång tid det tar för nya data uppdateringar som programmet kan tolerera vid återställning efter en störnings händelse. Tidsperioden för uppdateringar som du kan ha råd att förlora kallas mål för återställningspunkt (RPO). Om du vill se återställnings-och RTO för Azure Cosmos DB, se [konsekvens nivåer och data hållbarhet](./consistency-levels.md#rto)

## <a name="next-steps"></a>Nästa steg

Härnäst kan du läsa följande artiklar:

* [Tillgänglighets-och prestanda kompromisser för olika konsekvens nivåer](./consistency-levels.md)

* [Skala etablerat dataflöde globalt](./request-units.md)

* [Global distribution](global-dist-under-the-hood.md)

* [Konsekvensnivåer i Azure Cosmos DB](consistency-levels.md)

* [Så här konfigurerar du ditt Cosmos-konto med flera Skriv regioner](how-to-multi-master.md)

* [SDK-beteende i miljöer med flera regioner](troubleshoot-sdk-availability.md)