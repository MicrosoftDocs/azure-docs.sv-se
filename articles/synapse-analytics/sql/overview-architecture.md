---
title: Synapse SQL-arkitektur
description: Lär dig hur Azure Synapse SQL kombinerar distribuerade fråge bearbetnings funktioner med Azure Storage för att uppnå höga prestanda och skalbarhet.
services: synapse-analytics
author: mlee3gsd
manager: rothja
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 28940272d39a08d790fe2cd913df808b02e7f426
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102441898"
---
# <a name="azure-synapse-sql-architecture"></a>Azure Synapse SQL-arkitektur 

I den här artikeln beskrivs arkitektur komponenterna i Synapse SQL.

## <a name="synapse-sql-architecture-components"></a>Komponenter för Synapse-arkitektur i SQL

Synapse SQL utnyttjar en skalbar arkitektur för att distribuera beräknings bearbetning av data över flera noder. Compute är separat från lagring, vilket gör att du kan skala beräkningarna oberoende av data i systemet. 

För dedikerad SQL-pool är enheten för skala en abstraktion av beräknings kraften som kallas för en [informations lager enhet](resource-consumption-models.md). 

För SQL-poolen utan server, som är utan server, görs skalning automatiskt för att hantera krav på resurs krav. När topologin ändras över tid genom att lägga till, ta bort noder eller redundans, anpassas till ändringar och kontrollerar att frågan har tillräckligt med resurser och har slutförts. Bilden nedan visar till exempel Server lös SQL-pool som använder 4 Compute-noder för att köra en fråga.

![Synapse SQL-arkitektur](./media//overview-architecture/sql-architecture.png)

Synapse SQL använder en Node-baserad arkitektur. Program ansluter och utfärdar T-SQL-kommandon till en Control-nod, vilket är den enda punkten i posten för Synapse SQL. 

Azure Synapse SQL Control-noden använder en distribuerad frågemotor för att optimera frågor för parallell bearbetning och skickar sedan åtgärder till Compute-noder för att utföra sitt arbete parallellt. 

Noden Server lös SQL-adresspool använder DQP-motorn (Distributed Query Processing) för att optimera och dirigera distribuerad körning av användar fråga genom att dela upp den i mindre frågor som ska utföras på Compute-noder. Varje liten fråga kallas aktivitet och representerar distribuerad körnings enhet. Den läser fil (er) från lagringen, ger resultat från andra aktiviteter, grupper eller order data som hämtats från andra uppgifter. 

Beräkningsnoderna lagrar alla användardata i Azure Storage och kör de parallella frågorna. Data Movement Service (DMS) är en intern tjänst på systemnivå som flyttar data mellan noder efter behov för att köra frågor parallellt och returnera korrekta resultat. 

När du använder Synapse SQL kan du med fristående lagrings utrymme och data bearbetning dra nytta av oberoende storleks ändring av beräknings kraften oavsett dina lagrings behov. För automatisk skalning i SQL-poolen görs automatiskt, medan för en dedikerad SQL-pool kan:

* Öka eller minska beräknings kraften, inom en dedikerad SQL-pool, utan att flytta data.
* Pausa beräkningskapaciteten och lämna data intakta, så att du bara betalar för lagring.
* Återuppta beräkningskapacitet under driftstimmar.

## <a name="azure-storage"></a>Azure Storage

Synapse SQL utnyttjar Azure Storage för att skydda dina användar data. Eftersom dina data lagras och hanteras av Azure Storage, finns det en separat avgift för lagrings användningen. 

Med Server lös SQL-poolen kan du fråga dina data Lake-filer, medan en dedikerad SQL-pool gör det möjligt att fråga efter och mata in data från dina data Lake-filer. När data matas in i en dedikerad SQL-pool, är data shardade i **distributioner** för att optimera systemets prestanda. Du kan välja vilket mönster för horisontell partitionering som ska användas för att distribuera data när du definierar tabellen. Dessa horisontell partitionering-mönster stöds:

* Hash
* Resursallokering (round robin)
* Replikera

## <a name="control-node"></a>Kontrollnoden

Kontrollnoden är hjärnan i arkitekturen. Det är den som är klientdelen som interagerar med alla program och anslutningar. 

I Synapse SQL körs den distribuerade frågespråket på noden kontroll för att optimera och koordinera parallella frågor. När du skickar en T-SQL-fråga till en dedikerad SQL-pool, omvandlas kontroll noden till frågor som körs mot varje distribution parallellt.

I en server lös SQL-pool körs DQP-motorn på Control-noden för att optimera och koordinera distribuerad körning av användar frågan genom att dela upp den i mindre frågor som ska utföras på Compute-noder. Det tilldelar också uppsättningar av filer som ska bearbetas av varje nod.

## <a name="compute-nodes"></a>Beräkningsnoder

Beräkningsnoderna ger dataresurser. 

I dedikerad SQL-pool mappas distributioner till datornoder för bearbetning. När du betalar för fler beräknings resurser mappar poolen om distributionerna till de tillgängliga datornoderna. Antalet datornoder sträcker sig från 1 till 60 och bestäms av Service nivån för den dedikerade SQL-poolen. Varje Compute-nod har ett nod-ID som visas i systemvyer. Du kan se Compute Node ID genom att leta efter kolumnen node_id i systemvyer vars namn börjar med sys.pdw_nodes. En lista över dessa system visningar finns i [SYNAPSE SQL system views](/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=azure-sqldw-latest&preserve-view=true).

I SQL-poolen utan server tilldelas varje datornod en aktivitet och en uppsättning filer att köra aktiviteten på. Uppgiften är distribuerad frågekörning, som faktiskt är en del av frågan som användaren skickat. Automatisk skalning används för att se till att tillräckligt många datornoder används för att köra användar frågor.

## <a name="data-movement-service"></a>Data Movement Service

Data flyttnings tjänsten (DMS) är data transport teknik i dedikerad SQL-pool som samordnar data förflyttningen mellan Compute-noderna. Vissa frågor kräver data förflyttning för att säkerställa att de parallella frågorna returnerar korrekta resultat. När data förflyttning krävs säkerställer DMS att rätt data får rätt plats.

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="distributions"></a>Distributioner

En distribution är den grundläggande enheten för lagring och bearbetning av parallella frågor som körs på distribuerade data i en dedikerad SQL-pool. När en dedikerad SQL-pool kör en fråga delas arbetet upp i 60 mindre frågor som körs parallellt. 

Var och en av de 60 mindre frågorna körs på en av data distributionerna. Varje Compute-nod hanterar en eller flera av 60-distributionerna. En dedikerad SQL-pool med maximala beräknings resurser har en distribution per Compute-nod. En dedikerad SQL-pool med minsta beräknings resurser har alla distributioner på en Compute-nod. 

## <a name="hash-distributed-tables"></a>Hash-distribuerade tabeller
En hash-distribuerad tabell kan leverera högsta frågeprestanda för kopplingar och aggregeringar för stora tabeller. 

För att Shard data till en hash-distribuerad tabell använder dedikerade SQL-poolen en hash-funktion för att deterministiskt tilldela varje rad till en distribution. I tabelldefinitionen utses en av kolumnerna till distributionskolumnen. Hash-funktionen använder värdena i distributionskolumnen för att tilldela varje rad till en distribution.

Följande diagram illustrerar hur en fullständig (icke-distribuerad tabell) lagras som en hash-distribuerad tabell. 

![Distribuerad tabell](media//overview-architecture/hash-distributed-table.png "Distribuerad tabell") 

* Varje rad tillhör en distribution. 
* En deterministisk hash-algoritm tilldelar varje rad till en distribution. 
* Antalet tabell rader per distribution varierar så som de visas i olika tabell storlekar.

Det finns prestanda överväganden för valet av en distributions kolumn, till exempel distinkthet, data skevning och de typer av frågor som körs i systemet.

## <a name="round-robin-distributed-tables"></a>Distribuerade tabeller med resursallokering

En Round Robin-tabell är den enklaste tabellen för att skapa och leverera snabba prestanda när den används som en mellanlagrings tabell för belastningar.

En distribuerad tabell med resursallokering distribuerar data jämnt i tabellen men utan ytterligare optimering. En distribution väljs först slumpmässigt och sedan är buffertar för rader kopplade till distributioner sekventiellt. Det går snabbt att läsa in data i en resursallokeringstabell men frågeprestanda kan ofta vara bättre med hash-distribuerade tabeller. Kopplingar i Round-Robin-tabeller kräver reshuffling-data, vilket tar ytterligare tid.

## <a name="replicated-tables"></a>Replikerade tabeller
En replikerad tabell ger snabbaste frågeprestanda för små tabeller.

En tabell som replikeras cachelagrar en fullständig kopia av tabellen på varje Compute-nod. Därför tar replikerade tabeller bort behovet av att överföra data mellan datornoder före en koppling eller agg regering. Replikerade tabeller används bäst med små tabeller. Extra lagring krävs och det finns ytterligare kostnader som uppstår när du skriver data, vilket gör stora tabeller opraktiska. 

Diagrammet nedan visar en replikerad tabell som cachelagras på den första distributionen på varje Compute-nod. 

![Replikerad tabell](media/overview-architecture/replicated-table.png "Replikerad tabell") 

## <a name="next-steps"></a>Nästa steg

Nu när du vet lite om Synapse SQL, lär dig hur du snabbt [skapar en dedikerad SQL-pool](../quickstart-create-sql-pool-portal.md) och [läser in exempel data](../sql-data-warehouse/sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) (./SQL-Data-Warehouse-load-Sample-databases.MD). Eller börja [använda SQL-pool utan server](../quickstart-sql-on-demand.md). Om du inte har erfarenhet av Azure kan [Azure-ordlistan](../../azure-glossary-cloud-terminology.md) vara till hjälp om du stöter på ny terminologi. 
