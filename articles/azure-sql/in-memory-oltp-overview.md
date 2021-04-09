---
title: InMemory-teknik
description: Minnes intern teknik förbättrar prestandan för transaktionella och analys arbets belastningar i Azure SQL Database och Azure SQL-hanterad instans.
services: sql-database
ms.service: sql-db-mi
ms.subservice: ''
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/19/2019
ms.openlocfilehash: 48b74a5507eb4a1d48b7bf70133e476a30fe8169
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92779959"
---
# <a name="optimize-performance-by-using-in-memory-technologies-in-azure-sql-database-and-azure-sql-managed-instance"></a>Optimera prestanda genom att använda minnes intern teknik i Azure SQL Database och Azure SQL-hanterad instans
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Med minnes intern teknik kan du förbättra programmets prestanda och eventuellt minska kostnaderna för din databas.

## <a name="when-to-use-in-memory-technologies"></a>När du ska använda minnes intern teknik

Genom att använda minnes intern teknik kan du uppnå prestanda förbättringar med olika arbets belastningar:

- **Transaktionell** (online transaktions bearbetning (OLTP)) där de flesta begär Anden läser eller uppdaterar mindre data uppsättningar (till exempel CRUD-åtgärder).
- **Analytisk** (online ANALYTICAL Processing (OLAP)) där de flesta av frågorna har komplexa beräkningar i rapporterings syfte, med ett visst antal frågor som läser in och lägger till data i befintliga tabeller (så kallade Mass inläsning) eller tar bort data från tabellerna.
- **Blandat** (hybrid transaktion/analys bearbetning (HTAP)) där både OLTP-och OLAP-frågor körs på samma uppsättning data.

Minnes intern teknik kan förbättra prestandan för dessa arbets belastningar genom att lagra de data som ska bearbetas i minnet, med hjälp av intern kompilering av frågorna eller avancerad bearbetning, till exempel batchbearbetning och SIMD-instruktioner som är tillgängliga på den underliggande maskin varan.

## <a name="overview"></a>Översikt

Azure SQL Database och Azure SQL-hanterad instans har följande inbyggda minnes tekniker:

- *[InMemory OLTP](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)* ökar antalet transaktioner per sekund och minskar svars tiden för transaktions bearbetning. Scenarier som har nytta av In-Memory OLTP är: transaktions bearbetning med stora data flöden, till exempel handel och spel, data inmatning från händelser eller IoT-enheter, cachelagring, data inläsning och temporära tabell-och tabell variabel scenarier.
- *Grupperade columnstore-index* minskar dina lagrings utrymmen (upp till 10 gånger) och förbättrar prestanda för rapporterings-och analys frågor. Du kan använda det med fakta tabeller i dina datamarter för att få plats med mer data i databasen och förbättra prestandan. Du kan också använda den med historiska data i den operativa databasen för att arkivera och kunna skicka frågor till upp till 10 gånger mer data.
- *Grupperade columnstore-index* för HTAP hjälper dig att få insikter i real tid i ditt företag genom att fråga den operativa databasen direkt, utan att behöva köra en dyr process för extrahering, transformering och inläsning (ETL) och vänta tills data lagret har fyllts i. Grupperade columnstore-index möjliggör snabb körning av analys frågor på OLTP-databasen, samtidigt som påverkan på drift belastningen minskar.
- Med *minnesoptimerade grupperade columnstore-index* för HTAP kan du utföra snabb transaktions bearbetning och *samtidigt* köra analys frågor snabbt på samma data.

Båda columnstore-indexen och In-Memory OLTP är en del av SQL Server produkten sedan 2012 respektive 2014. Azure SQL Database, Azure SQL-hanterad instans och SQL Server dela samma implementering av minnes intern tekniker.

## <a name="benefits-of-in-memory-technology"></a>Fördelar med minnes intern teknik

På grund av den effektivare frågan och transaktions bearbetningen hjälper intern teknik teknik också till att minska kostnaderna. Du behöver vanligt vis inte uppgradera pris nivån för databasen för att uppnå prestanda vinster. I vissa fall kan du till och med minska pris nivån, samtidigt som du ser prestanda förbättringar med minnes intern teknik.

Här följer två exempel på hur In-Memory OLTP hjälpte till att förbättra prestanda avsevärt:

- Med hjälp av In-Memory OLTP [kunde kvorum affärs lösningar ha dubbla sina arbets belastningar och förbättra DTU: er med 70%](https://resources.quorumsoftware.com/case-studies/quorum-doubles-key-database-s-workload-while-lowering-dtu).
- Följande videoklipp visar betydande förbättringar i resursförbrukning med en exempel arbets belastning: [minnes intern OLTP-video](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB). Mer information finns i blogg inlägget: [minnes intern OLTP](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

> [!NOTE]  
> InMemory-tekniker är tillgängliga på nivån Premium och Affärskritisk.

I följande videoklipp förklaras potentiella prestanda vinster med minnes intern teknik. Kom ihåg att prestanda vinsten som du ser alltid är beroende av många faktorer, inklusive typen av arbets belastning och data, åtkomst mönster för databasen och så vidare.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-In-Memory-Technologies/player]
>

I den här artikeln beskrivs aspekter av In-Memory OLTP-och columnstore-index som är speciella för Azure SQL Database och Azure SQL-hanterad instans, och även innehåller exempel:

- Du får se effekten av dessa tekniker på lagrings-och data storleks gränser.
- Du lär dig hur du hanterar flyttningen av databaser som använder dessa tekniker mellan olika pris nivåer.
- Du ser två exempel som illustrerar användningen av In-Memory OLTP, samt columnstore-index.

Mer information om minnes intern SQL Server finns i:

- [Översikt över minnes intern OLTP och användnings scenarier](/sql/relational-databases/in-memory-oltp/overview-and-usage-scenarios) (innehåller referenser till kund fallstudier och information för att komma igång)
- [Dokumentation för In-Memory OLTP](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)
- [Guide för columnstore-index](/sql/relational-databases/indexes/columnstore-indexes-overview)
- Hybrid transaktions-/analys bearbetning (HTAP), även kallat [drift analys i real tid](/sql/relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics)

## <a name="in-memory-oltp"></a>Minnesintern OLTP

In-Memory OLTP-tekniken ger mycket snabba data åtkomst åtgärder genom att behålla alla data i minnet. Den använder också specialiserade index, ursprunglig kompilering av frågor och låsning – kostnads fri data åtkomst för att förbättra prestandan hos OLTP-arbetsbelastningen. Det finns två sätt att organisera In-Memory OLTP-data:

- **Minnesoptimerade rowstore** -format där varje rad är ett separat minnes objekt. Detta är ett klassiskt In-Memory OLTP-format som är optimerat för OLTP-arbetsbelastningar med höga prestanda. Det finns två typer av minnesoptimerade tabeller som kan användas i det minnesoptimerade rowstore-formatet:

  - *Varaktiga tabeller* (SCHEMA_AND_DATA) där de rader som placeras i minnet bevaras efter omstart av servern. Den här typen av tabeller fungerar som en traditionell rowstore-tabell med ytterligare fördelar med minnes optimeringar.
  - *Icke-varaktiga tabeller* (SCHEMA_ONLY) där raderna inte bevaras efter omstart. Den här typen av tabell är utformad för temporära data (till exempel ersättning av temporära tabeller) eller tabeller där du snabbt behöver läsa in data innan du flyttar dem till en bestående tabell (så kallade mellanlagrings tabeller).

- **Minnesoptimerade columnstore** -format där data är ordnade i kolumn format. Den här strukturen är utformad för HTAP-scenarier där du behöver köra analys frågor på samma data struktur där OLTP-arbetsbelastningen körs.

> [!Note]
> In-Memory OLTP-teknik är utformad för de data strukturer som kan finnas i minnet helt och hållet i minnet. Eftersom det inte går att avlasta InMemory-data till disk kontrollerar du att du använder en databas som har tillräckligt med minne. Se [data storlek och lagrings gräns för In-Memory OLTP](#data-size-and-storage-cap-for-in-memory-oltp) för mer information.

En snabb introduktion på In-Memory OLTP: [snabb start 1: In-Memory OLTP-teknik för snabbare T-SQL-prestanda](/sql/relational-databases/in-memory-oltp/survey-of-initial-areas-in-in-memory-oltp) (en annan artikel som hjälper dig att komma igång)

Djupgående videor om teknikerna:

- [Minnes intern OLTP](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB) (som innehåller en demonstration av prestanda fördelarna och steg för att återskapa dessa resultat själv)
- [InMemory OLTP-videor: vad det är och när/hur den används](/archive/blogs/sqlserverstorageengine/in-memory-oltp-video-what-it-is-and-whenhow-to-use-it)

Det finns ett programmerings sätt för att förstå om en specifik databas stöder In-Memory OLTP. Du kan köra följande Transact-SQL-fråga:

```sql
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```

Om frågan returnerar **1**, stöds In-Memory OLTP i den här databasen. Följande frågor identifierar alla objekt som måste tas bort innan en databas kan nedgraderas till Generell användning, standard eller Basic:

```sql
SELECT * FROM sys.tables WHERE is_memory_optimized=1
SELECT * FROM sys.table_types WHERE is_memory_optimized=1
SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
```

### <a name="data-size-and-storage-cap-for-in-memory-oltp"></a>Data storlek och lagrings hölje för In-Memory OLTP

In-Memory OLTP innehåller minnesoptimerade tabeller som används för att lagra användar data. De här tabellerna krävs för att få plats i minnet. Eftersom du hanterar minne direkt i SQL Database har vi begreppet en kvot för användar data. Den här idén kallas för *minnes intern OLTP-lagring*.

Varje pris nivå för en enskild databas som stöds och varje pris nivå för elastisk pool innehåller en viss mängd In-Memory OLTP-lagring.

- [DTU-baserade resurs gränser – enskild databas](database/resource-limits-dtu-single-databases.md)
- [DTU-baserade resurs gränser-elastiska pooler](database/resource-limits-dtu-elastic-pools.md)
- [vCore resurs gränser – enskilda databaser](database/resource-limits-vcore-single-databases.md)
- [vCore resurs gränser – elastiska pooler](database/resource-limits-vcore-elastic-pools.md)
- [vCore resurs gränser – hanterad instans](managed-instance/resource-limits.md)

Följande objekt räknas mot In-Memory OLTP-lagrings höljet:

- Aktiva användar data rader i minnesoptimerade tabeller och tabell variabler. Observera att gamla rad versioner inte räknas mot taket.
- Index för minnesoptimerade tabeller.
- Drifts kostnader för ALTER TABLE-åtgärder.

Om du når Cap får du ett fel som inte är i kvoten och du kan inte längre infoga eller uppdatera data. Du kan åtgärda det här felet genom att ta bort data eller öka pris nivån för databasen eller poolen.

Mer information om övervakning In-Memory OLTP-lagring och konfiguration av aviseringar när du nästan når Cap finns [i övervaka minnes lagring](in-memory-oltp-monitor-space.md).

#### <a name="about-elastic-pools"></a>Om elastiska pooler

Med elastiska pooler delas In-Memory OLTP-lagringen över alla databaser i poolen. Därför kan användningen i en databas eventuellt påverka andra databaser. Två begränsningar för detta är:

- Konfigurera en `Max-eDTU` eller `MaxvCore` för databaser som är lägre än eDTU-eller vCore-antalet för poolen som helhet. Detta största värde är In-Memory OLTP-lagrings användningen, i vilken databas som helst i poolen, till den storlek som motsvarar antalet eDTU: er.
- Konfigurera en `Min-eDTU` eller `MinvCore` som är större än 0. Detta minimi krav garanterar att varje databas i poolen har den mängd tillgänglig In-Memory OLTP-lagring som motsvarar den konfigurerade `Min-eDTU` eller `vCore` .

### <a name="changing-service-tiers-of-databases-that-use-in-memory-oltp-technologies"></a>Ändra tjänst nivåer för databaser som använder In-Memory OLTP-teknik

Du kan alltid uppgradera din databas eller instans till en högre nivå, till exempel från Generell användning till Affärskritisk (eller från standard till Premium). De tillgängliga funktionerna och resurserna ökar bara.

Men nedgradering av nivån kan påverka databasen negativt. Påverkan är särskilt uppenbar när du nedgraderar från Affärskritisk till Generell användning (eller Premium till standard eller Basic) när databasen innehåller In-Memory OLTP-objekt. Minnesoptimerade tabeller är inte tillgängliga efter nedgradering (även om de fortfarande visas). Samma överväganden gäller när du sänker pris nivån för en elastisk pool eller flyttar en databas med minnes intern teknik till en Generell användning, standard eller en enkel elastisk pool.

> [!Important]
> In-Memory OLTP stöds inte på nivån Generell användning, standard eller Basic. Därför går det inte att flytta en databas som har In-Memory OLTP-objekt till någon av dessa nivåer.

Innan du nedgradera databasen till Generell användning, standard eller Basic, tar du bort alla minnesoptimerade tabeller och tabell typer, samt alla internt kompilerade T-SQL-moduler.

Att *skala upp resurser i affärskritisk nivå*: data i minnesoptimerade tabeller måste rymmas i den In-Memory OLTP-lagring som är kopplad till nivån för databasen eller den hanterade instansen eller den är tillgänglig i den elastiska poolen. Om du försöker skala ned nivån eller flytta databasen till en pool som inte har tillräckligt med tillgänglig In-Memory OLTP-lagring, Miss lyckas åtgärden.

## <a name="in-memory-columnstore"></a>Minnes intern columnstore

InMemory columnstore-teknik gör att du kan lagra och skicka frågor till en stor mängd data i tabellerna. Columnstore-tekniken använder kolumnbaserade data lagrings format och batchbearbetning för att uppnå upp till 10 gånger frågans prestanda i OLAP-arbetsbelastningar över traditionell orienterad lagring. Du kan också få upp till 10 gånger data komprimeringen över den okomprimerade data storleken.
Det finns två typer av columnstore-modeller som du kan använda för att organisera dina data:

- **Grupperat columnstore** -objekt där alla data i tabellen är ordnade i kolumn formatet. I den här modellen placeras alla rader i tabellen i kolumn format som komprimerar data mycket och gör att du kan köra snabba analytiska frågor och rapporter i tabellen. Beroende på vilken typ av data det handlar om kan storleken på dina data minskas med 10X-100x. Grupperad columnstore-modell möjliggör också snabb inmatning av stora mängder data (Mass inläsning) eftersom stora batchar med data som är större än 100 000 rader komprimeras innan de lagras på disken. Den här modellen är ett bra alternativ för de klassiska data lager scenarierna.
- **Icke-grupperat columnstore-** data där data lagras i traditionell rowstore-tabell och det finns ett index i columnstore-formatet som används för analys frågorna. Den här modellen möjliggör hybrid Transactional-Analytic bearbetning (HTAP): möjlighet att köra analys i real tid på en transaktions arbets belastning. OLTP-frågor körs på rowstore-tabellen som är optimerad för åtkomst till en liten uppsättning rader, medan OLAP-frågor körs på columnstore-index som är bättre för genomsökningar och analyser. Fråga optimering väljer dynamiskt rowstore-eller columnstore-format baserat på frågan. Icke-grupperade columnstore-index minskar inte storleken på data eftersom den ursprungliga data uppsättningen sparas i den ursprungliga rowstore-tabellen utan några ändringar. Storleken på ytterligare columnstore-index ska dock vara i mindre storlek än motsvarande B-träd index.

> [!Note]
> InMemory columnstore-teknik bevarar bara de data som behövs för bearbetning i minnet, medan data som inte får plats i minnet lagras på disk. Därför kan mängden data i columnstore-strukturer i minnet överskrida mängden tillgängligt minne.

Djupgående video om tekniken:

- [Columnstore-index: videor in-memory Analytics från antändning 2016](/archive/blogs/sqlserverstorageengine/columnstore-index-in-memory-analytics-i-e-columnstore-index-videos-from-ignite-2016)

### <a name="data-size-and-storage-for-columnstore-indexes"></a>Data storlek och lagring för columnstore-index

Columnstore-index krävs inte för att få plats i minnet. Därför är den enda gränsen för index storleken den maximala övergripande databas storleken, som dokumenteras i den [DTU-baserade inköps modellen](database/service-tiers-dtu.md) och [vCore-baserade inköps modell](database/service-tiers-vcore.md) artiklar.

När du använder grupperade columnstore-index används kolumn komprimering för bas tabellens lagring. Den här komprimeringen kan avsevärt minska lagrings utrymmet för dina användar data, vilket innebär att du kan få mer data i databasen. Och komprimeringen kan öka ytterligare med [kolumn lagrings komprimering](/sql/relational-databases/data-compression/data-compression#using-columnstore-and-columnstore-archive-compression). Mängden komprimering som du kan uppnå beror på data typen, men 10 gånger komprimeringen är inte ovanligt.

Om du till exempel har en databas med en maximal storlek på 1 terabyte (TB) och du uppnår 10 gånger komprimeringen med hjälp av columnstore-index, kan du anpassa totalt 10 TB användar data i databasen.

När du använder icke-klustrade columnstore-index lagras bas tabellen fortfarande i det traditionella rowstore-formatet. Lagrings besparingarna är därför inte lika viktiga som med grupperade columnstore-index. Men om du ersätter ett antal traditionella icke-grupperade index med ett enda columnstore-index kan du fortfarande se en övergripande besparingar i lagrings utrymmet för tabellen.

### <a name="changing-service-tiers-of-databases-containing-columnstore-indexes"></a>Ändra tjänst nivåer för databaser som innehåller columnstore-index

Det kanske inte är möjligt *att nedgradera enkla databaser till Basic eller standard* om mål nivån är lägre än S3. Columnstore-index stöds endast på pris nivån Affärskritisk/Premium och på standard nivån, S3 och högre, och inte på Basic-nivån. När du nedgradera databasen till en nivå eller nivå som inte stöds blir ditt columnstore-index otillgängligt. Systemet upprätthåller ditt columnstore-index, men det utnyttjar aldrig indexet. Om du senare uppgraderar till en nivå eller nivå som stöds, är ditt columnstore-index omedelbart redo att utnyttjas igen.

Om du har ett **grupperat** columnstore-index blir hela tabellen otillgänglig efter nedgradering. Vi rekommenderar därför att du tar bort alla *klustrade* columnstore-index innan du nedgradera databasen till en nivå eller nivå som inte stöds.

> [!Note]
> SQL-hanterad instans stöder columnstore-index på alla nivåer.

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

## <a name="next-steps"></a>Nästa steg

- [Snabb start 1: In-Memory OLTP-teknik för snabbare T-SQL-prestanda](/sql/relational-databases/in-memory-oltp/survey-of-initial-areas-in-in-memory-oltp)
- [Använda In-Memory OLTP i ett befintligt Azure SQL-program](in-memory-oltp-configure.md)
- [Övervaka In-Memory OLTP-lagring](in-memory-oltp-monitor-space.md) för In-Memory OLTP
- [Testa funktioner i minnet](in-memory-sample.md)

## <a name="additional-resources"></a>Ytterligare resurser

### <a name="deeper-information"></a>Djupare information

- [Lär dig hur kvorum dubblerar nyckel databasens arbets belastning och sänker DTU med 70% med In-Memory OLTP i SQL Database](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)
- [Blogg inlägg i Memory OLTP](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)
- [Lär dig mer om In-Memory OLTP](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)
- [Lär dig mer om columnstore-index](/sql/relational-databases/indexes/columnstore-indexes-overview)
- [Lär dig mer om operativa analyser i real tid](/sql/relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics)
- Se [vanliga arbets belastnings mönster och överväganden vid migrering](/previous-versions/dn673538(v=msdn.10)) (som beskriver arbets belastnings mönster där In-Memory OLTP ofta ger avsevärda prestanda vinster)

### <a name="application-design"></a>Programdesign

- [Minnes intern OLTP (minnes intern optimering)](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)
- [Använda In-Memory OLTP i ett befintligt Azure SQL-program](in-memory-oltp-configure.md)

### <a name="tools"></a>Verktyg

- [Azure-portalen](https://portal.azure.com/)
- [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms)
- [SQL Server Data Tools (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt)