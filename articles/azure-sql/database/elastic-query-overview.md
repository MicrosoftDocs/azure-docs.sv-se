---
title: Översikt över elastiska frågor
description: Elastisk fråga gör det möjligt att köra en Transact-SQL-fråga som sträcker sig över flera databaser.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: overview
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 12/05/2019
ms.openlocfilehash: cac17bbac96d44d8d9bfce2e168de4ea6d4c5c08
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100364961"
---
# <a name="azure-sql-database-elastic-query-overview-preview"></a>Översikt över Azure SQL Database elastisk fråga (för hands version)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Funktionen för elastisk fråga (i förhandsversion) gör att du kan köra en Transact-SQL-fråga som sträcker sig över flera databaser i Azure SQL Database. Det gör att du kan utföra frågor över flera databaser för att få åtkomst till fjärrtabeller och ansluta Microsoft och verktyg från tredje part (Excel, Power BI, Tableau osv.) för att fråga över data nivåer med flera databaser. Med den här funktionen kan du skala ut frågor till omfattande datanivåer och visualisera resultaten i BI-rapporter (Business Intelligence).

## <a name="why-use-elastic-queries"></a>Varför ska man använda elastiska frågor

### <a name="azure-sql-database"></a>Azure SQL Database

Fråga över databaser i Azure SQL Database helt i T-SQL. Detta möjliggör skrivskyddad fråga i fjärrdatabaser och ger ett alternativ för nuvarande SQL Server-kunder att migrera program med hjälp av tre-och fyra delar eller en länkad server till SQL Database.

### <a name="available-on-standard-tier"></a>Tillgängligt på standard nivån

Elastiska frågor stöds på tjänst nivåerna standard och Premium. I avsnittet om förhands gransknings begränsningar nedan finns prestanda begränsningar för lägre tjänst nivåer.

### <a name="push-parameters-to-remote-databases"></a>Push-parametrar till fjärrdatabaser

Elastiska frågor kan nu skicka SQL-parametrar till fjärrdatabaserna för körning.

### <a name="stored-procedure-execution"></a>Körning av lagrad procedur

Kör fjärranslutna procedur anrop eller fjärrfunktioner med hjälp av [SP \_ execute \_ Remote](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database).

### <a name="flexibility"></a>Flexibilitet

Externa tabeller med elastisk fråga kan referera till fjärrtabeller med ett annat schema-eller tabell namn.

## <a name="elastic-query-scenarios"></a>Elastiska fråge scenarier

Målet är att under lätta frågor i scenarier där flera databaser bidrar med rader till ett enda övergripande resultat. Frågan kan antingen bestå av användaren eller programmet direkt eller indirekt via verktyg som är anslutna till databasen. Detta är särskilt användbart när du skapar rapporter med hjälp av kommersiella BI-eller data integrerings verktyg eller program som inte kan ändras. Med en elastisk fråga kan du fråga över flera databaser med hjälp av den välbekanta SQL Server anslutnings upplevelsen i verktyg som Excel, Power BI, Tableau eller Cognos.
En elastisk fråga ger enkel åtkomst till en hel samling databaser via frågor som utfärdats av SQL Server Management Studio eller Visual Studio och underlättar frågor från flera databaser från Entity Framework eller andra ORM-miljöer. Bild 1 visar ett scenario där ett befintligt moln program (som använder [klient biblioteket för elastiska databaser](elastic-database-client-library.md)) bygger på en skalad data nivå och en elastisk fråga används för rapportering mellan databaser.

**Bild 1** Elastisk fråga som används för utskalade data nivåer

![Elastisk fråga som används för utskalade data nivåer][1]

Kund scenarier för elastiska frågor kännetecknas av följande topologier:

* **Vertikal partitionering-frågor över flera databaser** (topologi 1): data partitioneras lodrätt mellan ett antal databaser i en data nivå. Vanligt vis finns olika uppsättningar med tabeller i olika databaser. Det innebär att schemat är olika i olika databaser. Till exempel är alla tabeller för inventering i en databas medan alla redovisnings tabeller finns på en andra databas. Vanliga användnings fall med den här topologin kräver att en fråga över eller kompilerar rapporter över tabeller i flera databaser.
* **Horisontell partitionering-horisontell partitionering** (topologi 2): data är partitionerade vågrätt för att distribuera rader över en utskalad data nivå. Med den här metoden är schemat identiskt på alla deltagande databaser. Den här metoden kallas även för "horisontell partitionering". Horisontell partitionering kan utföras och hanteras med hjälp av (1) de elastiska databas verktyg biblioteken eller (2) Self-horisontell partitionering. En elastisk fråga används för att fråga eller kompilera rapporter över flera Shards. Shards är vanligt vis databaser inom en elastisk pool. Du kan tänka på elastisk fråga som ett effektivt sätt att fråga alla databaser i elastisk pool på en gång, så länge databaserna delar det gemensamma schemat.

> [!NOTE]
> Elastisk fråga fungerar bäst för rapporterings scenarier där merparten av bearbetningen (filtrering, agg regering) kan utföras på den externa käll sidan. Det är inte lämpligt för ETL-åtgärder där stora mängder data överförs från fjärrdatabaser. För tung rapportering av arbets belastningar eller data lager scenarier med mer komplexa frågor kan du även överväga att använda [Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics).
>  

## <a name="vertical-partitioning---cross-database-queries"></a>Vertikal partitionering-frågor mellan databaser

För att börja koda, se [komma igång med kors databas fråga (lodrät partitionering)](elastic-query-getting-started-vertical.md).

En elastisk fråga kan användas för att göra data som finns i en databas i SQL Database tillgängliga för andra databaser i SQL Database. Detta gör att frågor från en databas kan referera till tabeller i andra fjärrdatabaser i SQL Database. Det första steget är att definiera en extern data källa för varje fjärran sluten databas. Den externa data källan definieras i den lokala databasen från vilken du vill få åtkomst till tabeller som finns i fjärrdatabasen. Inga ändringar krävs för fjärrdatabasen. För vanliga lodräta partitionerings scenarier där olika databaser har olika scheman, kan elastiska frågor användas för att implementera vanliga användnings fall som till exempel åtkomst till referens data och kors databas frågor.

> [!IMPORTANT]
> Du måste ha behörighet för att ändra en extern DATA källa. Den här behörigheten ingår i ALTER DATABASE-behörigheten. ÄNDRA behörigheter för en extern DATA källa krävs för att referera till den underliggande data källan.
>

**Referens data**: topologin används för referens data hantering. I bilden nedan sparas två tabeller (T1 och T2) med referens data i en dedikerad databas. Med en elastisk fråga kan du nu komma åt tabeller T1 och T2 via fjärr anslutning från andra databaser, som du ser i bilden. Använd topologi 1 om referens tabeller är små eller fjärranslutna frågor till referens tabellen har selektiva predikat.

**Bild 2** Vertikal partitionering-använda elastisk fråga för att fråga referens data

![Vertikal partitionering-använda elastisk fråga för att fråga referens data][3]

**Fråga** i flera databaser: elastiska frågor möjliggör användnings fall som kräver frågor över flera databaser i SQL Database. Bild 3 visar fyra olika databaser: CRM, Inventory, HR och Products. Frågor som utförs i en av databaserna måste också ha åtkomst till en eller alla andra databaser. Med en elastisk fråga kan du konfigurera databasen för det här fallet genom att köra några enkla DDL-uttryck på var och en av de fyra databaserna. Efter den här konfigurationen är det lika enkelt att komma åt en fjärrtabell som hänvisar till en lokal tabell från T-SQL-frågorna eller från dina BI-verktyg. Den här metoden rekommenderas om fjärrfrågor inte returnerar stora resultat.

**Bild 3** Vertikal partitionering-använda elastisk fråga för att fråga mellan olika databaser

![Vertikal partitionering-använda elastisk fråga för att fråga mellan olika databaser][4]

Följande steg konfigurerar elastiska databas frågor för lodräta partitionerings scenarier som kräver åtkomst till en tabell som finns på fjärrdatabaser i SQL Database med samma schema:

* [Skapa huvud nyckel](/sql/t-sql/statements/create-master-key-transact-sql) mymasterkey
* [Skapa databasens begränsade](/sql/t-sql/statements/create-database-scoped-credential-transact-sql) autentiseringsuppgifter för databasen
* [Skapa/släpp extern data källa](/sql/t-sql/statements/create-external-data-source-transact-sql) av typen **RDBMS**
* [Skapa/släpp extern tabell tabell](/sql/t-sql/statements/create-external-table-transact-sql)

När du har kört DDL-instruktionerna kan du komma åt fjärrtabellen "untable" som om den vore en lokal tabell. Azure SQL Database öppnar automatiskt en anslutning till fjärrdatabasen, bearbetar din begäran på fjärrdatabasen och returnerar resultatet.

## <a name="horizontal-partitioning---sharding"></a>Horisontell partitionering – horisontell partitionering

Om du använder elastiska frågor för att utföra rapporterings uppgifter över en shardade, det vill säga vågrätt partitionerat, kräver data nivån en [elastisk databas Shard-karta](elastic-scale-shard-map-management.md) för att representera data skiktets databaser. Normalt används endast en enda Shard-karta i det här scenariot och en dedikerad databas med elastiska fråge funktioner (Head Node) fungerar som start punkt för rapporterings frågor. Endast den här dedikerade databasen behöver åtkomst till Shard-kartan. Bild 4 illustrerar den här topologin och dess konfiguration med den Elastic Query-databasen och Shard-kartan. Mer information om klient biblioteket för Elastic Database och hur du skapar Shard Maps finns i [Shard Map Management](elastic-scale-shard-map-management.md).

**Bild 4** Horisontell partitionering-använda elastisk fråga för rapportering över shardade data nivåer

![Horisontell partitionering-använda elastisk fråga för rapportering över shardade data nivåer][5]

> [!NOTE]
> Elastic Query-databas (Head Node) kan vara en separat databas, eller så kan den vara samma databas som är värd för Shard-kartan.
> Oavsett vilken konfiguration du väljer kontrollerar du att tjänst nivå och beräknings storlek för databasen är tillräckligt högt för att hantera den förväntade mängden inloggnings-/fråge förfrågningar.

Följande steg konfigurerar elastiska databas frågor för horisontella partitionerings scenarier som kräver åtkomst till en uppsättning tabeller som finns på (vanligt vis) flera fjärrdatabaser i SQL Database:

* [Skapa huvud nyckel](/sql/t-sql/statements/create-master-key-transact-sql) mymasterkey
* [Skapa databasens begränsade](/sql/t-sql/statements/create-database-scoped-credential-transact-sql) autentiseringsuppgifter för databasen
* Skapa en [Shard-karta](elastic-scale-shard-map-management.md) som representerar data nivån med klient biblioteket för Elastic Database.
* [Skapa/släpp extern data källa källa](/sql/t-sql/statements/create-external-data-source-transact-sql) av typen **SHARD_MAP_MANAGER**
* [Skapa/släpp extern tabell tabell](/sql/t-sql/statements/create-external-table-transact-sql)

När du har utfört de här stegen kan du komma åt den vågrätt partitionerade tabellen "Tabell" som om den vore en lokal tabell. Azure SQL Database öppnar automatiskt flera parallella anslutningar till fjärrdatabaserna där tabellerna lagras fysiskt, bearbetar begär anden i fjärrdatabaserna och returnerar resultaten.
Mer information om de steg som krävs för det horisontella partitionerings scenariot finns i [elastisk fråga för horisontell partitionering](elastic-query-horizontal-partitioning.md).

För att börja koda, se [komma igång med elastisk fråga för horisontell partitionering (horisontell partitionering)](elastic-query-getting-started.md).

> [!IMPORTANT]
> Lyckad körning av elastiska frågor över en stor uppsättning databaser är beroende av tillgängligheten för varje databas under frågekörningen. Om en av databaserna inte är tillgänglig, kommer hela frågan att Miss förvänta. Om du planerar att fråga hundratals eller tusentals databaser samtidigt, kontrollerar du att ditt klient program har omslags logik inbäddat, eller funderar över [Elastic Database jobb](./job-automation-overview.md) (för hands version) och frågar efter mindre del mängder av databaser, konsoliderar resultat från varje fråga till ett enda mål.

## <a name="t-sql-querying"></a>T-SQL-fråga

När du har definierat dina externa data källor och dina externa tabeller kan du använda vanliga SQL Server anslutnings strängar för att ansluta till databaserna där du definierade dina externa tabeller. Du kan sedan köra T-SQL-uttryck över dina externa tabeller på den anslutningen med de begränsningar som beskrivs nedan. Du hittar mer information och exempel på T-SQL-frågor i dokumentations avsnitten för [horisontell partitionering](elastic-query-horizontal-partitioning.md) och [vertikal partitionering](elastic-query-vertical-partitioning.md).

## <a name="connectivity-for-tools"></a>Anslutning för verktyg

Du kan använda vanliga SQL Server anslutnings strängar för att ansluta dina program och BI-eller data integrerings verktyg till databaser som har externa tabeller. Kontrol lera att SQL Server stöds som data källa för ditt verktyg. När du har anslutit kan du se den elastiska fråge databasen och de externa tabellerna i databasen på samma sätt som du gör med andra SQL Server-databaser som du ansluter till med ditt verktyg.

> [!IMPORTANT]
> Autentisering med Azure Active Directory med elastiska frågor stöds inte för närvarande.

## <a name="cost"></a>Cost

Elastisk fråga ingår i kostnaden för Azure SQL Database. Observera att topologier där fjärrdatabaserna finns i ett annat data Center än den elastiska fråge slut punkten stöds, men utgående data från fjärrdatabaser debiteras regelbundet med [Azure-priser](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="preview-limitations"></a>Begränsningar för förhandsversion

* Det kan ta upp till några minuter att köra din första elastiska fråga på standard tjänst nivån. Den här tiden är nödvändig för att läsa in funktioner för elastiska frågor. inläsning av prestanda förbättras med högre tjänst nivåer och beräknings storlekar.
* Det finns ännu inte stöd för skript för externa data källor eller externa tabeller från SSMS eller SSDT.
* Import/export för SQL Database stöder ännu inte externa data källor och externa tabeller. Om du behöver använda import/export ska du ta bort dessa objekt innan du exporterar och sedan återskapa dem efter att du har importerat.
* Elastisk fråga stöder för närvarande endast skrivskyddad åtkomst till externa tabeller. Du kan dock använda fullständiga T-SQL-funktioner i databasen där den externa tabellen definieras. Detta kan vara användbart för, t. ex. Spara temporära resultat med, till exempel välja <column_list> i <local_table> eller definiera lagrade procedurer på den elastiska Frågeparametern som refererar till externa tabeller.
* Med undantag för nvarchar (max) stöds inte LOB-typer (inklusive spatiala typer) i externa tabell definitioner. Som en lösning kan du skapa en vy på den fjärranslutna databasen som överför LOB-typen till nvarchar (max), definiera den externa tabellen över vyn i stället för bas tabellen och sedan omvandla den till den ursprungliga LOB-typen i dina frågor.
* Kolumner av typen nvarchar (max) i resultat uppsättning inaktivera avancerad batching-Technics som används i elastiska fråge implementeringar och kan påverka prestanda för frågor i en storleksordning, eller till och med två storleksordningar i icke-kanoniska användnings fall där stora mängder icke-aggregerade data överförs som ett resultat av en fråga.
* Kolumn statistik över externa tabeller stöds inte för närvarande. Tabell statistik stöds, men du måste skapa den manuellt.
* Elastisk fråga fungerar endast med Azure SQL Database. Du kan inte använda den för att skicka frågor till en SQL Server instans.

## <a name="share-your-feedback"></a>Dela din feedback

Dela feedback om din upplevelse med elastiska frågor med oss nedan, i MSDN-forumen eller på Stack Overflow. Vi är intresserade av alla typer av feedback om tjänsten (fel, grova kanter, funktions luckor).

## <a name="next-steps"></a>Nästa steg

* En lodrät partitionerings guide finns i [komma igång med kors databas fråga (lodrät partitionering)](elastic-query-getting-started-vertical.md).
* För syntax och exempel frågor för lodrätt partitionerade data, se [fråga lodrätt partitionerade data)](elastic-query-vertical-partitioning.md)
* En självstudie för horisontell partitionering (horisontell partitionering) finns i [komma igång med elastisk fråga för horisontell partitionering (horisontell partitionering)](elastic-query-getting-started.md).
* För syntax och exempel frågor för vågrätt partitionerade data, se [fråga efter vågrätt partitionerade data)](elastic-query-horizontal-partitioning.md)
* Se [SP \_ execute \_ Remote](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database) för en lagrad procedur som kör ett Transact-SQL-uttryck på en enskild fjärr Azure SQL Database eller uppsättning databaser som fungerar som Shards i ett schema med vågrät partitionering.

<!--Image references-->
[1]: ./media/elastic-query-overview/overview.png
[2]: ./media/elastic-query-overview/topology1.png
[3]: ./media/elastic-query-overview/vertpartrrefdata.png
[4]: ./media/elastic-query-overview/verticalpartitioning.png
[5]: ./media/elastic-query-overview/horizontalpartitioning.png

<!--anchors-->