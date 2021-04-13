---
title: Hög tillgänglighet i Azure Cosmos DB
description: Den här artikeln beskriver hur Azure Cosmos DB ger hög tillgänglighet
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: ac1e77d99707cdaa34ef42eb9b327a62f4e864c0
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365390"
---
# <a name="how-does-azure-cosmos-db-provide-high-availability"></a>Hur ger Azure Cosmos DB hög tillgänglighet
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB ger hög tillgänglighet på två huvudsakliga sätt. Först Azure Cosmos DB data mellan regioner som konfigurerats i ett Cosmos-konto. För det Azure Cosmos DB 4 repliker av data inom en region.

Azure Cosmos DB är en globalt distribuerad databastjänst och är en grundläggande tjänst som är tillgänglig i [alla regioner där Azure är tillgängligt.](https://azure.microsoft.com/global-infrastructure/services/?products=cosmos-db&regions=all) Du kan associera val av antal Azure-regioner med ditt Azure Cosmos-konto så replikeras dina data automatiskt och transparent. Du kan lägga till eller ta bort en region till ditt Azure Cosmos-konto när som helst. Cosmos DB finns i alla fem olika Azure-molnmiljöer som är tillgängliga för kunder:

* **Offentligt** Azure-moln, som är tillgängligt globalt.

* **Azure China 21Vianet** är tillgängligt via ett unikt partnerskap mellan Microsoft och 21Vianet, en av världens största Internetleverantörer i Kina.

* **Azure Tyskland** tillhandahåller tjänster enligt en datavalsmodell som säkerställer att kunddata förblir i Tyskland under kontroll av T-Systems International GmbH, ett dotterbolag till Telekom, som fungerar som den tyska databolaget.

* **Azure Government** är tillgänglig i fyra regioner i USA för amerikanska myndigheter och deras partner.

* **Azure Government för Usa:s försvarsdepartement (DoD)** finns i två regioner i USA usa:s försvarsdepartement.

Inom en region Azure Cosmos DB du fyra kopior av dina data som repliker inom fysiska partitioner enligt följande bild:

:::image type="content" source="./media/high-availability/cosmosdb-data-redundancy.png" alt-text="Fysisk partitionering" border="false":::

* Data i Azure Cosmos-containrar [är horisontellt partitionerade](partitioning-overview.md).

* En partitionsuppsättning är en samling med flera replikuppsättningar. I varje region skyddas varje partition av en replikuppsättning där alla skrivningar replikeras och durably har utförts av en majoritet av replikerna. Repliker distribueras över så många som 10–20 feldomäner.

* Varje partition i alla regioner replikeras. Varje region innehåller alla datapartitioner i en Azure Cosmos-container och kan hantera läsningar samt hantera skrivningar när skrivningar i flera regioner har aktiverats.  

Om ditt Azure Cosmos-konto distribueras över *N* Azure-regioner finns det minst *N* x 4 kopior av alla dina data. Ett Azure Cosmos-konto i fler än 2 regioner förbättrar tillgängligheten för ditt program och ger korta svarstider i de associerade regionerna.

## <a name="slas-for-availability"></a>Serviceavtal för tillgänglighet

Azure Cosmos DB tillhandahåller omfattande serviceavtal som omfattar dataflöde, svarstid vid den 99:e percentilen, konsekvens och hög tillgänglighet. Tabellen nedan visar garantierna för hög tillgänglighet som tillhandahålls av Azure Cosmos DB för konton med en eller flera regioner. För högre skrivtillgänglighet konfigurerar du ditt Azure Cosmos-konto så att det har flera skrivregioner.

|Åtgärdstyp  | En region |Flera regioner (skrivningar i en region)|Flera regioner (skrivningar i flera regioner) |
|---------|---------|---------|-------|
|Skrivningar    | 99,99    |99,99   |99,999|
|Läsningar     | 99,99    |99,999  |99,999|

> [!NOTE]
> I praktiken är den faktiska skrivtillgängligheten för modeller med begränsad föråldhet, session, konsekvent prefix och slutlig konsekvens betydligt högre än de publicerade serviceavtalen. Den faktiska lästillgängligheten för alla konsekvensnivåer är betydligt högre än de publicerade serviceavtalen.

## <a name="high-availability-with-azure-cosmos-db-in-the-event-of-regional-outages"></a>Hög tillgänglighet med Azure Cosmos DB i händelse av regionala avbrott

I de sällsynta fall av regionalt avbrott ser Azure Cosmos DB till att databasen alltid har hög tillgänglig. Följande information samlar Azure Cosmos DB under ett avbrott, beroende på din Azure Cosmos-kontokonfiguration:

* Med Azure Cosmos DB- och skrivåtgärden bekräftas för klienten, bekräftas datan genom ett kvorum med repliker inom regionen som accepterar skrivåtgärderna. Mer information finns i [Konsekvensnivåer och dataflöde](./consistency-levels.md#consistency-levels-and-throughput)

* Konton i flera regioner som konfigurerats med flera skrivregioner har hög tillgång till både skrivningar och läsningar. Regionala redundans identifieras och hanteras i Azure Cosmos DB klienten. De sker också omedelbart och kräver inte några ändringar från programmet.

* Konton i en region kan förlora tillgängligheten efter ett regionalt avbrott. Du bör alltid konfigurera minst två regioner **(helst** minst två skrivregioner) med ditt Azure Cosmos-konto för att säkerställa hög tillgänglighet hela tiden.

> [!IMPORTANT]
> När du använder SQL-API:er är det nödvändigt att konfigurera Cosmos DB SDK för att använda alla angivna läsregioner för att dra nytta av den ökade tillgängligheten. Mer information [finns i](troubleshoot-sdk-availability.md) den här artikeln.

### <a name="multi-region-accounts-with-a-single-write-region-write-region-outage"></a>Konton i flera regioner med en enda skrivningsregion (avbrott i skrivregionen)

* Under ett avbrott i en skrivregion befordrar Azure Cosmos-kontot automatiskt en  sekundär region till den nya primära skrivregionen när aktivera automatisk redundans har konfigurerats på Azure Cosmos-kontot. När den är aktiverad sker redundansen till en annan region i den ordning som du har angett för regionprioritet.

* Observera att manuell redundans inte ska utlösas och inte lyckas vid ett avbrott i käll- eller målregionen. Detta beror på en konsekvenskontroll som krävs av redundansproceduren som kräver anslutning mellan regionerna.

* När den region som påverkades tidigare är online igen blir alla skrivdata som inte replikerades när regionen misslyckades tillgängliga via [konfliktflödet](how-to-manage-conflicts.md#read-from-conflict-feed). Program kan läsa konfliktflödet, lösa konflikter baserat på den programspecifika logiken och skriva tillbaka uppdaterade data till Azure Cosmos-containern efter behov.

* När den tidigare påverkade skrivregionen återställs blir den automatiskt tillgänglig som en läsregion. Du kan växla tillbaka till den återställda regionen som skrivregion. Du kan växla mellan regionerna med [hjälp av PowerShell, Azure CLI eller Azure Portal](how-to-manage-database-account.md#manual-failover). Det finns **ingen data- eller tillgänglighetsförlust** före, under eller efter att du byter skrivregion och programmet fortsätter att ha hög tillgänglighet.

> [!IMPORTANT]
> Vi rekommenderar starkt att du konfigurerar Azure Cosmos-konton som används för produktionsarbetsbelastningar för **att aktivera automatisk redundans.** Detta gör Cosmos DB kan redundans redundans för kontodatabaserna till tillgänglighetsregioner automatiskt. Om den här konfigurationen saknas förlorar kontot skrivtillgängligheten under hela avbrottet i skrivregionen, eftersom manuell redundans inte lyckas på grund av bristande regionanslutning.

### <a name="multi-region-accounts-with-a-single-write-region-read-region-outage"></a>Konton i flera regioner med en region med en enda skrivning (avbrott i läsregionen)

* Under ett avbrott i läsregionen är Azure Cosmos-konton med valfri konsekvensnivå eller stark konsekvens med tre eller flera läsregioner fortfarande mycket tillgängliga för läsningar och skrivningar.

* Azure Cosmos-konton som använder stark konsekvens med tre regioner (en skrivning, två läsningar) behåller skrivtillgänglighet under ett avbrott i läsregionen. För konton med två regioner och automatisk redundans aktiverad slutar kontot att acceptera skrivningar tills regionen har markerats som misslyckad och automatisk redundans inträffar.

* Den berörda regionen kopplas automatiskt från och markeras som offline. De [Azure Cosmos DB-SDK:erna](sql-api-sdk-dotnet.md) omdirigerar lässamtal till nästa tillgängliga region i listan över föredragna regioner.

* Om ingen av regionerna i listan över föredragna regioner är tillgänglig kan återgår anrop automatiskt till den aktuella skrivregionen.

* Inga ändringar krävs i programkoden för att hantera avbrott i läsregionen. När den berörda läsregionen är online igen synkroniseras den automatiskt med den aktuella skrivregionen och blir tillgänglig igen för att hantera läsbegäranden.

* Efterföljande läsningar omdirigeras till den återställda regionen utan att det krävs några ändringar i din programkod. Under både redundans och återanslutning till en region som misslyckats tidigare fortsätter garantier för läskonsekvens att respekteras av Azure Cosmos DB.

* Även i sällsynta och tyvärr fall när Azure-regionen är permanent oåterkallelig går det inte att återställa data om ditt Azure Cosmos-konto för flera regioner har konfigurerats med *stark* konsekvens. I händelse av en permanent oåterkallelig skrivregion, ett Azure Cosmos-konto i flera regioner som konfigurerats med konsekvens med begränsad föråldhet, begränsas fönstret för potentiell dataförlust till föråldrhetsfönstret *(K* eller *T)* där K = 100 000 uppdateringar eller T = 5 minuter, vilket någonsin sker först. För sessions-, konsekvensprefix- och slutlig konsekvensnivå är fönstret för potentiell dataförlust begränsat till högst 15 minuter. Mer information om RTO- och RPO-mål för Azure Cosmos DB finns i [Konsekvensnivåer och databeständighet](./consistency-levels.md#rto)

## <a name="availability-zone-support"></a>Stöd för tillgänglighetszoner

Förutom återhämtning mellan regioner stöder Azure Cosmos DB zonredundans i regioner som stöds när du väljer en region som ska associeras med ditt Azure Cosmos-konto. 

Med stöd för tillgänglighetszoner (AZ) Azure Cosmos DB repliker placeras över flera zoner inom en viss region för att ge hög tillgänglighet och återhämtning vid zonindelade fel. Tillgänglighetszoner ett serviceavtal med 99,995 % tillgänglighet utan ändringar i svarstiden. Vid fel i en zon ger zonredundans fullständig datatillförlitlighet med RPO=0 och tillgänglighet med RTO=0. Zonredundans är en kompletterande funktion för regional replikering. Enbart zonredundans kan inte användas för att uppnå regional återhämtning.

Zonredundans kan bara konfigureras när du lägger till en ny region till ett Azure Cosmos-konto. För befintliga regioner kan zonredundans aktiveras genom att ta bort regionen och sedan lägga till den igen med zonredundans aktiverad. För ett konto för en enskild region kräver detta att du lägger till ytterligare en region att tillfälligt redundans till och sedan ta bort och lägga till önskad region med zonredundans aktiverad.

När du konfigurerar skrivningar i flera regioner för ditt Azure Cosmos-konto kan du välja zonredundans utan extra kostnad. Annars kan du se tabellen nedan om priser för stöd för zonredundans. En lista över regioner där tillgänglighetszoner är tillgängliga finns i [Tillgänglighetszoner.](../availability-zones/az-region.md)

I följande tabell sammanfattas funktionen för hög tillgänglighet i olika kontokonfigurationer:

|KPI|En enda region utan AZs|En enda region med AZs|Skrivningar i flera regioner och en region med AZ|Skrivningar i flera regioner med AZ|
|---------|---------|---------|---------|---------|
|Serviceavtal för skrivtillgänglighet | 99,99 % | 99.995% | 99.995% | 99,999 % |
|Läs tillgänglighets-SLA  | 99,99 % | 99.995% | 99.995% | 99,999 % |
|Zonfel – dataförlust | Dataförluster | Ingen dataförlust | Ingen dataförlust | Ingen dataförlust |
|Zonfel – tillgänglighet | Tillgänglighetsförlust | Ingen tillgänglighetsförlust | Ingen tillgänglighetsförlust | Ingen tillgänglighetsförlust |
|Regionalt avbrott – dataförlust | Dataförluster |  Dataförluster | Beroende på konsekvensnivå. Mer [information finns i Kompromisser för konsekvens,](./consistency-levels.md) tillgänglighet och prestanda. | Beroende på konsekvensnivå. Mer [information finns i Kompromisser för konsekvens,](./consistency-levels.md) tillgänglighet och prestanda.
|Regionalt avbrott – tillgänglighet | Tillgänglighetsförlust | Tillgänglighetsförlust | Ingen tillgänglighetsförlust vid fel i läsregionen, tillfällig för skrivregionsfel | Ingen tillgänglighetsförlust |
|Pris (***1** _) | Ej tillämpligt | Etablerade RU/s x 1,25-hastighet | Etablerade RU/s x 1,25-hastighet (_*_2_**) | Skrivfrekvens för flera regioner |

***1*** För serverlösa konton multipliceras enheter för programbegäran (RU) med faktorn 1,25.

***2*** 1,25-priset tillämpas endast på de regioner där AZ är aktiverat.

Tillgänglighetszoner kan aktiveras via:

* [Azure Portal](how-to-manage-database-account.md#addremove-regions-from-your-database-account)

* [Azure PowerShell](manage-with-powershell.md#create-account)

* [Azure CLI](manage-with-cli.md#add-or-remove-regions)

* [Azure Resource Manager-mallar](./manage-with-templates.md)

## <a name="building-highly-available-applications"></a>Skapa program med hög kapacitet

* Granska det förväntade [beteendet för Azure Cosmos-SDK:erna](troubleshoot-sdk-availability.md) under dessa händelser och vilka konfigurationer som påverkar den.

* För att säkerställa hög skriv- och lästillgänglighet konfigurerar du ditt Azure Cosmos-konto så att det omfattar minst två regioner med flera skrivregioner. Den här konfigurationen ger högsta tillgänglighet, kortast svarstid och bästa skalbarhet för både läsningar och skrivningar som backas upp av serviceavtal. Mer information finns i konfigurera ditt [Azure Cosmos-konto med flera skrivregioner.](tutorial-global-distribution-sql-api.md)

* För Azure Cosmos-konton i flera regioner som är konfigurerade med en enda skrivningsregion aktiverar du automatisk redundans med hjälp av [Azure CLI eller Azure Portal](how-to-manage-database-account.md#automatic-failover). När du har aktivera automatisk redundans när det finns en regional katastrof Cosmos DB automatiskt redundans för ditt konto.  

* Även om ditt Azure Cosmos-konto har hög tillgång kanske ditt program inte är korrekt utformat för att behålla hög tillgänglig. Du kan testa programmets höga tillgänglighet från start till slut genom att tillfälligt inaktivera automatisk redundans för kontot under programtestning eller haveriberedskap, anropa manuell redundans med hjälp av [PowerShell, Azure CLI](how-to-manage-database-account.md#manual-failover)eller Azure Portal och sedan övervaka programmets redundans. När det är klart kan du växla tillbaka till den primära regionen och återställa automatisk redundans för kontot.

> [!IMPORTANT]
> Anropa inte manuell redundans under ett Cosmos DB avbrott i käll- eller målregionerna, eftersom det kräver att regionsanslutningen upprätthåller datakonsekvensen och inte lyckas.

* I en globalt distribuerad databasmiljö finns det en direkt relation mellan konsekvensnivån och datavarigheten vid ett regionomfattande avbrott. När du utvecklar din plan för affärskontinuhet måste du förstå den längsta godtagbara tiden innan programmet återställs helt efter en störande händelse. Den tid som krävs för att ett program ska återställas helt kallas mål för återställningstid (RTO). Du måste också förstå den maximala perioden för senaste datauppdateringar som programmet kan tolerera att förlora vid återställning efter en störande händelse. Tidsperioden för uppdateringar som du kan ha råd att förlora kallas mål för återställningspunkt (RPO). Information om RPO och RTO för Azure Cosmos DB finns i [Konsekvensnivåer och datatillförlitlighet](./consistency-levels.md#rto)

## <a name="what-to-expect-during-a-cosmos-db-region-outage"></a>Vad som händer under ett avbrott Cosmos DB en region

För konton i en region förlorar klienterna läs- och skrivtillgänglighet.

Konton i flera regioner har olika beteenden beroende på följande tabell.

| Skrivregioner | Automatisk redundans | Vad du kan förvänta dig | Vad bör jag göra |
| -- | -- | -- | -- |
| Region för enskild skrivning | Inte aktiverat | Vid avbrott i en läsregion omdirigeras alla klienter till andra regioner. Ingen läs- eller skrivtillgänglighetsförlust. Ingen dataförlust. <p/> Vid ett avbrott i skrivregionen kommer klienterna att uppleva skrivtillgänglighetsförlust. Om stark konsekvensnivå inte har valts kanske vissa data inte har replikerats till de återstående aktiva regionerna. Detta beror på vilken consistenvy-nivå som valts enligt beskrivningen i [det här avsnittet](consistency-levels.md#rto). Om den berörda regionen drabbas av permanent dataförlust kan oreplikerade data gå förlorade. <p/> Cosmos DB återställs skrivtillgänglighet automatiskt när avbrottet upphör. | Under avbrottet ser du till att det finns tillräckligt med etablerade RU:er i de återstående regionerna för att stödja lästrafik. <p/> *Utlösnings* inte en manuell redundans under avbrottet, eftersom den inte lyckas. <p/> När avbrottet är över justerar du etablerade RU:er efter behov. |
| Region för enskild skrivning | Enabled | Vid avbrott i en läsregion omdirigeras alla klienter till andra regioner. Ingen läs- eller skrivtillgänglighetsförlust. Ingen dataförlust. <p/> Vid ett avbrott i skrivregionen kommer klienterna att uppleva skrivtillgänglighetsförlust tills Cosmos DB automatiskt väljer en ny region som den nya skrivregionen enligt dina önskemål. Om stark konsekvensnivå inte har valts kanske vissa data inte har replikerats till de återstående aktiva regionerna. Detta beror på vilken consistenvy-nivå som valts enligt beskrivningen i [det här avsnittet](consistency-levels.md#rto). Om den berörda regionen drabbas av permanent dataförlust kan oreplikerade data gå förlorade. | Under avbrottet ser du till att det finns tillräckligt med etablerade RU:er i de återstående regionerna för att stödja lästrafik. <p/> *Utlöst* inte manuell redundans under avbrottet eftersom det inte lyckas. <p/> När avbrottet är över kan du flytta tillbaka skrivregionen till den ursprungliga regionen och justera om etablerade RU:er efter behov. Konton som använder SQL-API:er kan också återställa icke-replikerade data i den misslyckade regionen från [konfliktflödet](how-to-manage-conflicts.md#read-from-conflict-feed). |
| Flera skrivregioner | Inte tillämpligt | Ingen tillgänglighetsförlust för läsning eller skrivning. <p/> Nyligen uppdaterade data i den misslyckade regionen kan vara ovilliga i de återstående aktiva regionerna. De slutliga, konsekventa prefix- och sessionskonsekvensnivåerna garanterar en föråldrighet <15 minuter. Bunden föråldrering garanterar mindre än K uppdateringar eller T sekunder, beroende på konfigurationen. Om den berörda regionen drabbas av permanent dataförlust kan oreplikerade data gå förlorade. | Under avbrottet ser du till att det finns tillräckligt med etablerade RU:er i de återstående regionerna för att stödja ytterligare trafik. <p/> När avbrottet är över kan du justera etablerade RU:er efter behov. Om möjligt Cosmos DB automatiskt icke-replikerade data i den misslyckade regionen med hjälp av den konfigurerade konfliktlösningsmetoden för SQL API-konton och Senaste skrivning vinner för konton som använder andra API:er. |

## <a name="next-steps"></a>Nästa steg

Därefter kan du läsa följande artiklar:

* [Kompromisser mellan tillgänglighet och prestanda för olika konsekvensnivåer](./consistency-levels.md)

* [Skala etablerat dataflöde globalt](./request-units.md)

* [Global distribution](global-dist-under-the-hood.md)

* [Konsekvensnivåer i Azure Cosmos DB](consistency-levels.md)

* [Så här konfigurerar du ditt Cosmos-konto med flera skrivregioner](how-to-multi-master.md)

* [SDK-beteende i miljöer med flera regionala miljöer](troubleshoot-sdk-availability.md)