---
title: Synapse SQL-arkitektur
description: Lär dig Azure Synapse SQL kombinerar funktioner för distribuerad frågebearbetning med Azure Storage för att uppnå hög prestanda och skalbarhet.
services: synapse-analytics
author: mlee3gsd
manager: rothja
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: f342f39b62956cd85f269918e8e1ef1a2478a3d8
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566366"
---
# <a name="azure-synapse-sql-architecture"></a>Azure Synapse SQL-arkitektur 

I den här artikeln beskrivs arkitekturkomponenterna i Synapse SQL.

## <a name="synapse-sql-architecture-components"></a>Synapse SQL arkitekturkomponenter

Synapse SQL använder en utskalningsarkitektur för att distribuera beräkningsbearbetning av data över flera noder. Compute är separat från lagring, vilket gör att du kan skala beräkning oberoende av data i systemet. 

För dedikerad SQL-pool är skalningsenheten en abstraktion av beräkningskraft som kallas för [en informationslagerenhet](resource-consumption-models.md). 

För serverlös SQL-pool, som är serverlös, utförs skalning automatiskt för att tillgodose resurskrav för frågor. När topologin ändras med tiden genom att lägga till, ta bort noder eller redundans, anpassas den efter ändringar och ser till att din fråga har tillräckligt med resurser och slutförs korrekt. Bilden nedan visar till exempel en serverlös SQL-pool som använder 4 beräkningsnoder för att köra en fråga.

![Synapse SQL-arkitektur](./media//overview-architecture/sql-architecture.png)

Synapse SQL använder en nodbaserad arkitektur. Program ansluter och utfärdar T-SQL-kommandon till en kontrollnod, vilket är den enda startpunkten för Synapse SQL. 

Den Azure Synapse SQL Control-noden använder en distribuerad frågemotor för att optimera frågor för parallell bearbetning och skickar sedan åtgärder till beräkningsnoder för att göra sitt arbete parallellt. 

Noden serverlös SQL-poolkontroll använder DQP-motorn (Distributed Query Processing) för att optimera och samordna distribuerad körning av användarfrågor genom att dela upp den i mindre frågor som ska köras på beräkningsnoder. Varje liten fråga kallas uppgift och representerar en distribuerad körningsenhet. Den läser filer från lagring, ansluter resultat från andra uppgifter, grupper eller orderdata som hämtats från andra uppgifter. 

Beräkningsnoderna lagrar alla användardata i Azure Storage och kör de parallella frågorna. Data Movement Service (DMS) är en intern tjänst på systemnivå som flyttar data mellan noder efter behov för att köra frågor parallellt och returnera korrekta resultat. 

Med frikopplad lagring och beräkning kan Synapse SQL dra nytta av oberoende storleksändring av beräkningskraft oavsett dina lagringsbehov. För serverlös SQL-poolskalning sker skalning automatiskt, medan för dedikerad SQL-pool kan man:

* Öka eller minska beräkningskraften inom en dedikerad SQL-pool utan att flytta data.
* Pausa beräkningskapaciteten och lämna data intakta, så att du bara betalar för lagring.
* Återuppta beräkningskapacitet under driftstimmar.

## <a name="azure-storage"></a>Azure Storage

Synapse SQL utnyttjar Azure Storage för att skydda dina användardata. Eftersom dina data lagras och hanteras av Azure Storage debiteras lagringsförbrukningen separat. 

Med en serverlös SQL-pool kan du köra frågor mot dina datasjöfiler, medan du med dedikerad SQL-pool kan köra frågor mot och mata in data från dina Data Lake-filer. När data matas in i dedikerad SQL-pool partitioneras data i **distributioner** för att optimera systemets prestanda. Du kan välja vilket mönster för horisontell partitionering som ska användas för att distribuera data när du definierar tabellen. Dessa mönster för horisontell partitionering stöds:

* Hash
* Resursallokering (round robin)
* Replikera

## <a name="control-node"></a>Kontrollnoden

Kontrollnoden är hjärnan i arkitekturen. Det är den som är klientdelen som interagerar med alla program och anslutningar. 

I Synapse SQL körs den distribuerade frågemotorn på kontrollnoden för att optimera och koordinera parallella frågor. När du skickar en T-SQL-fråga till en dedikerad SQL-pool omvandlar kontrollnoden den till frågor som körs parallellt mot varje distribution.

I en serverlös SQL-pool körs DQP-motorn på kontrollnoden för att optimera och koordinera distribuerad körning av användarfrågor genom att dela upp den i mindre frågor som ska köras på beräkningsnoder. Den tilldelar också uppsättningar med filer som ska bearbetas av varje nod.

## <a name="compute-nodes"></a>Beräkningsnoder

Beräkningsnoderna ger dataresurser. 

I dedikerad SQL-pool mappar distributioner till beräkningsnoder för bearbetning. När du betalar för fler beräkningsresurser mappar poolen om distributionerna till de tillgängliga beräkningsnoderna. Antalet beräkningsnoder sträcker sig från 1 till 60 och bestäms av servicenivån för den dedikerade SQL-poolen. Varje beräkningsnod har ett nod-ID som visas i systemvyer. Du kan se Beräkningsnod-ID genom att leta efter node_id i systemvyer vars namn börjar med sys.pdw_nodes. En lista över dessa systemvyer finns i [Synapse SQL systemvyer.](/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=azure-sqldw-latest&preserve-view=true)

I serverlös SQL-pool tilldelas varje beräkningsnod uppgift och uppsättning filer som aktiviteten ska köras på. Uppgiften är en distribuerad frågekörningsenhet, som i själva verket är en del av den frågeanvändare som skickats in. Automatisk skalning används för att se till att tillräckligt med beräkningsnoder används för att köra användarfrågor.

## <a name="data-movement-service"></a>Data Movement Service

Data Movement Service (DMS) är datatransporttekniken i en dedikerad SQL-pool som samordnar dataflyttningen mellan beräkningsnoderna. Vissa frågor kräver dataförflyttning för att säkerställa att parallella frågor returnerar korrekta resultat. När dataförflyttning krävs ser DMS till att rätt data kommer till rätt plats.

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="distributions"></a>Distributioner

En distribution är den grundläggande lagringsenheten och bearbetningen för parallella frågor som körs på distribuerade data i dedikerad SQL-pool. När en dedikerad SQL-pool kör en fråga delas arbetet in i 60 mindre frågor som körs parallellt. 

Var och en av de 60 mindre frågorna körs på en av datadistributionerna. Varje beräkningsnod hanterar en eller flera av de 60 distributionerna. En dedikerad SQL-pool med maximalt antal beräkningsresurser har en distribution per beräkningsnod. En dedikerad SQL-pool med minsta beräkningsresurser har alla distributioner på en beräkningsnod. 

## <a name="hash-distributed-tables"></a>Hash-distribuerade tabeller
En hash-distribuerad tabell kan leverera högsta frågeprestanda för kopplingar och aggregeringar för stora tabeller. 

För att dela upp data i en hash-distribuerad tabell använder dedikerad SQL-pool en hash-funktion för att deterministiskt tilldela varje rad till en distribution. I tabelldefinitionen utses en av kolumnerna till distributionskolumnen. Hash-funktionen använder värdena i distributionskolumnen för att tilldela varje rad till en distribution.

Följande diagram illustrerar hur en fullständig (icke-distribuerad tabell) lagras som en hash-distribuerad tabell. 

![Distribuerad tabell](media//overview-architecture/hash-distributed-table.png "Distribuerad tabell") 

* Varje rad tillhör en distribution. 
* En deterministisk hash-algoritm tilldelar varje rad till en distribution. 
* Antalet tabellrader per distribution varierar beroende på de olika tabellstorlekarna.

Det finns prestandaöverväganden för valet av en distributionskolumn, till exempel distinkthet, dataskevhet och de typer av frågor som körs i systemet.

## <a name="round-robin-distributed-tables"></a>Distribuerade tabeller med resursallokering

En resursallokeringstabell är den enklaste tabellen för att skapa och leverera snabba prestanda när den används som mellanlagringstabell för belastningar.

En distribuerad tabell med resursallokering distribuerar data jämnt i tabellen men utan ytterligare optimering. En distribution väljs först slumpmässigt och sedan tilldelas buffertar med rader till distributioner sekventiellt. Det går snabbt att läsa in data i en resursallokeringstabell men frågeprestanda kan ofta vara bättre med hash-distribuerade tabeller. Kopplingar i resursallokeringstabeller kräver omsuffling av data, vilket tar ytterligare tid.

## <a name="replicated-tables"></a>Replikerade tabeller
En replikerad tabell ger snabbaste frågeprestanda för små tabeller.

En tabell som replikeras cachelagrar en fullständig kopia av tabellen på varje beräkningsnod. När du replikerar en tabell behöver du alltså inte överföra data mellan beräkningsnoder före en koppling eller aggregering. Replikerade tabeller används bäst med små tabeller. Extra lagring krävs och det tillkommer ytterligare kostnader vid skrivning av data, vilket gör stora tabeller opraktiska. 

Diagrammet nedan visar en replikerad tabell som cachelagras på den första distributionen på varje beräkningsnod. 

![Replikerad tabell](media/overview-architecture/replicated-table.png "Replikerad tabell") 

## <a name="next-steps"></a>Nästa steg

Nu när du vet lite om Synapse SQL kan du lära dig hur du snabbt skapar en dedikerad [SQL-pool](../quickstart-create-sql-pool-portal.md) och läser in [exempeldata](../sql-data-warehouse/sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) (./sql-data-warehouse-load-sample-databases.md). Eller börja [använda serverlös SQL-pool](../quickstart-sql-on-demand.md). Om du inte har erfarenhet av Azure kan [Azure-ordlistan](../../azure-glossary-cloud-terminology.md) vara till hjälp om du stöter på ny terminologi. 
