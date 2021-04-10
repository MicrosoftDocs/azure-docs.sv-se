---
title: Så här använder du batching för att förbättra programmets prestanda
description: Avsnittet innehåller bevis på att batchering av databas åtgärder avsevärt förbättrar hastigheten och skalbarheten för dina Azure SQL Database och Azure SQL-hanterade instans program. Även om dessa batch-tekniker fungerar för alla SQL-databaser, är fokus för artikeln på Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: genemi
ms.date: 01/25/2019
ms.openlocfilehash: 7f45e7d1515f0d6fc4467b36d95242ef8697c75d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105641390"
---
# <a name="how-to-use-batching-to-improve-azure-sql-database-and-azure-sql-managed-instance-application-performance"></a>Använda batching för att förbättra Azure SQL Database och prestanda för Azure SQL-hanterad instans program
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Batching-åtgärder för att Azure SQL Database och Azure SQL-hanterad instans förbättrar avsevärt prestanda och skalbarhet för dina program. För att förstå fördelarna täcker den första delen av den här artikeln några exempel test resultat som jämför sekventiella och batch-begäranden till en databas i Azure SQL Database eller Azure SQL-hanterad instans. Resten av artikeln visar tekniker, scenarier och överväganden som hjälper dig att använda batching i dina Azure-program.

## <a name="why-is-batching-important-for-azure-sql-database-and-azure-sql-managed-instance"></a>Varför är batching viktig för Azure SQL Database och Azure SQL-hanterad instans?

Batching-anrop till en fjärrtjänst är en välkänd strategi för att öka prestanda och skalbarhet. Det finns fasta bearbetnings kostnader för alla interaktioner med en fjärrtjänst, till exempel serialisering, nätverks överföring och deserialisering. Genom att paketera flera separata transaktioner i en enda batch minimeras dessa kostnader.

I den här artikeln vill vi undersöka olika batching-strategier och scenarier. Även om dessa strategier också är viktiga för lokala program som använder SQL Server, finns det flera skäl för att markera användningen av batching för Azure SQL Database och Azure SQL-hanterad instans:

* Det finns potentiellt större nätverks fördröjning vid åtkomst till Azure SQL Database och Azure SQL-hanterad instans, särskilt om du använder Azure SQL Database eller Azure SQL-hanterad instans utanför samma Microsoft Azure Data Center.
* Egenskaperna för flera klient organisationer för Azure SQL Database och Azure SQL-hanterad instans innebär att effektiviteten hos data åtkomst lagret motsvarar den övergripande skalbarheten för databasen. Som svar på användning som överstiger de fördefinierade kvoterna kan Azure SQL Database och Azure SQL-hanterad instans minska genomflödet eller svara med begränsnings undantag. Effektivitet, till exempel batchbearbetning, gör att du kan utföra mer arbete innan du når dessa gränser.
* Satsvis kompilering gäller också för arkitekturer som använder flera databaser (horisontell partitionering). Effektiviteten i interaktionen med varje databas enhet är fortfarande en viktig faktor i din övergripande skalbarhet.

En av fördelarna med att använda Azure SQL Database eller Azure SQL-hanterad instans är att du inte behöver hantera de servrar som är värdar för-databasen. Den här hanterade infrastrukturen innebär dock också att du måste tänka på olika databas optimeringar. Du kan inte längre titta för att förbättra databasens maskin vara eller nätverks infrastruktur. Microsoft Azure styr dessa miljöer. Det huvudsakliga utrymmet som du kan styra är hur ditt program samverkar med Azure SQL Database och Azure SQL-hanterad instans. Satsvis kompilering är en av dessa optimeringar.

Den första delen av den här artikeln granskar olika batching-tekniker för .NET-program som använder Azure SQL Database eller Azure SQL-hanterad instans. De sista två avsnitten beskriver rikt linjer och scenarier för batchering.

## <a name="batching-strategies"></a>Batching-strategier

### <a name="note-about-timing-results-in-this-article"></a>Information om tidtagnings resultat i den här artikeln

> [!NOTE]
> Resultaten är inte benchmarks, men är avsedda att visa **relativa prestanda**. Tids inställningarna baseras på ett genomsnitt av minst 10 test körningar. Åtgärder infogas i en tom tabell. De här testerna mättes i förväg V12 och de motsvarar inte nödvändigt vis det data flöde som du kan uppleva i en V12-databas med hjälp av de nya [nivåerna för DTU-tjänsten](database/service-tiers-dtu.md) eller [vCore-tjänsten](database/service-tiers-vcore.md). Den relativa fördelen med batch-tekniken bör vara liknande.

### <a name="transactions"></a>Transaktioner

Det förefaller konstigt att påbörja en granskning av satsvis kompilering genom att diskutera transaktioner. Men användningen av transaktioner på klient sidan har en diskret kommando effekt på Server sidan som ger bättre prestanda. Och transaktioner kan läggas till med bara några rader med kod, så de ger ett snabbt sätt att förbättra prestanda för sekventiella åtgärder.

Överväg följande C#-kod som innehåller en sekvens av INSERT-och Update-åtgärder på en enkel tabell.

```csharp
List<string> dbOperations = new List<string>();
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 1");
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 2");
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 3");
dbOperations.Add("insert MyTable values ('new value',1)");
dbOperations.Add("insert MyTable values ('new value',2)");
dbOperations.Add("insert MyTable values ('new value',3)");
```

Följande ADO.NET-kod utför dessa åtgärder sekventiellt.

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    conn.Open();

    foreach(string commandString in dbOperations)
    {
        SqlCommand cmd = new SqlCommand(commandString, conn);
        cmd.ExecuteNonQuery();
    }
}
```

Det bästa sättet att optimera den här koden är att implementera någon form av batching av klient sidan av dessa anrop. Men det finns ett enkelt sätt att öka prestandan för den här koden genom att helt enkelt omsluta sekvensen av anrop i en transaktion. Här är samma kod som använder en transaktion.

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    conn.Open();
    SqlTransaction transaction = conn.BeginTransaction();

    foreach (string commandString in dbOperations)
    {
        SqlCommand cmd = new SqlCommand(commandString, conn, transaction);
        cmd.ExecuteNonQuery();
    }

    transaction.Commit();
}
```

Transaktioner används faktiskt i båda dessa exempel. I det första exemplet är varje enskilt anrop en implicit transaktion. I det andra exemplet radbryts alla anrop i en explicit transaktion. I dokumentationen för [transaktions loggen för Skriv](/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide?view=sql-server-ver15&preserve-view=true#WAL)åtgärder rensas logg posterna till disken när transaktionen genomförs. Så genom att inkludera fler anrop i en transaktion kan Skriv till transaktions loggen skjuta tills transaktionen är genomförd. I praktiken aktiverar du batching för skrivningar till serverns transaktions logg.

I följande tabell visas några ad hoc-testnings resultat. Testerna utförde samma sekventiella infogningar med och utan transaktioner. För mer perspektiv kördes den första uppsättningen tester från en bärbar dator till databasen i Microsoft Azure. Den andra uppsättningen tester kördes från en moln tjänst och databas som båda finns i samma Microsoft Azure Data Center (västra USA). I följande tabell visas varaktigheten i millisekunder av sekventiella infogningar med och utan transaktioner.

**Lokalt till Azure**:

| Operations | Ingen transaktion (MS) | Transaktion (MS) |
| --- | --- | --- |
| 1 |130 |402 |
| 10 |1208 |1226 |
| 100 |12662 |10395 |
| 1000 |128852 |102917 |

**Azure till Azure (samma data Center)**:

| Operations | Ingen transaktion (MS) | Transaktion (MS) |
| --- | --- | --- |
| 1 |21 |26 |
| 10 |220 |56 |
| 100 |2145 |341 |
| 1000 |21479 |2756 |

> [!NOTE]
> Resultaten är inte benchmarks. Se [information om tidtagnings resultat i den här artikeln](#note-about-timing-results-in-this-article).

I och med föregående test resultat minskar prestandan med en enda åtgärd i en transaktion. Men när du ökar antalet åtgärder i en enda transaktion blir prestanda förbättringen mer markerad. Prestanda skillnaden är också mer märkbar när alla åtgärder utförs i Microsoft Azure Data centret. Den ökade svars tiden för att använda Azure SQL Database eller Azure SQL-hanterad instans utanför Microsoft Azure Data Center överskuggar prestanda vinsten för att använda transaktioner.

Även om användningen av transaktioner kan öka prestandan bör du fortsätta att [följa bästa praxis för transaktioner och anslutningar](/previous-versions/sql/sql-server-2008-r2/ms187484(v=sql.105)). Se till att transaktionen är så kort som möjligt och Stäng databas anslutningen när arbetet har slutförts. Instruktionen using i föregående exempel ser till att anslutningen stängs när det efterföljande kod blocket är klart.

Föregående exempel visar att du kan lägga till en lokal transaktion till valfri ADO.NET kod med två rader. Transaktioner är ett snabbt sätt att förbättra prestanda för kod som utför sekventiell infognings-, uppdaterings-och borttagnings åtgärder. Men för snabbaste prestanda bör du överväga att ändra koden så att du kan dra nytta av batching på klient sidan, till exempel tabell värdes parametrar.

Mer information om transaktioner i ADO.NET finns i [lokala transaktioner i ADO.net](/dotnet/framework/data/adonet/local-transactions).

### <a name="table-valued-parameters"></a>Tabellvärdesparametrar

Tabell värdes parametrar stöder användardefinierade tabell typer som parametrar i Transact-SQL-uttryck, lagrade procedurer och funktioner. Med den här batching-tekniken på klient sidan kan du skicka flera rader med data i tabell värdes parametern. Om du vill använda tabell värdes parametrar definierar du först en tabell typ. Följande Transact-SQL-instruktion skapar en tabell typ med namnet **MyTableType**.

```sql
    CREATE TYPE MyTableType AS TABLE
    ( mytext TEXT,
      num INT );
```

I kod skapar du en **DataTable** med exakt samma namn och typ av tabell typ. Skicka denna **DataTable** i en parameter i en text fråga eller ett lagrat procedur anrop. I följande exempel visas den här tekniken:

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    connection.Open();

    DataTable table = new DataTable();
    // Add columns and rows. The following is a simple example.
    table.Columns.Add("mytext", typeof(string));
    table.Columns.Add("num", typeof(int));
    for (var i = 0; i < 10; i++)
    {
        table.Rows.Add(DateTime.Now.ToString(), DateTime.Now.Millisecond);
    }

    SqlCommand cmd = new SqlCommand(
        "INSERT INTO MyTable(mytext, num) SELECT mytext, num FROM @TestTvp",
        connection);

    cmd.Parameters.Add(
        new SqlParameter()
        {
            ParameterName = "@TestTvp",
            SqlDbType = SqlDbType.Structured,
            TypeName = "MyTableType",
            Value = table,
        });

    cmd.ExecuteNonQuery();
}
```

I föregående exempel infogar objektet **SqlCommand** rader från en tabell värdes parameter, **\@ TestTvp**. Det tidigare skapade **DataTable** -objektet har tilldelats till den här parametern med metoden **SqlCommand. Parameters. Add** . Batching av infogningar i ett anrop ökar markant prestanda över sekventiella infogningar.

Om du vill förbättra det tidigare exemplet ytterligare använder du en lagrad procedur i stället för ett textbaserat kommando. Följande Transact-SQL-kommando skapar en lagrad procedur som tar **SimpleTestTableType** tabell värdes parameter.

```sql
CREATE PROCEDURE [dbo].[sp_InsertRows]
@TestTvp as MyTableType READONLY
AS
BEGIN
INSERT INTO MyTable(mytext, num)
SELECT mytext, num FROM @TestTvp
END
GO
```

Ändra sedan **SqlCommand** -objektets deklaration i föregående kod exempel till följande.

```csharp
SqlCommand cmd = new SqlCommand("sp_InsertRows", connection);
cmd.CommandType = CommandType.StoredProcedure;
```

I de flesta fall har tabell värdes parametrar motsvarande eller bättre prestanda än andra batching-tekniker. Tabell värdes parametrar är ofta bättre, eftersom de är mer flexibla än andra alternativ. Andra tekniker, till exempel SQL Mass kopiering, tillåter bara infogning av nya rader. Men med tabell värdes parametrar kan du använda logik i den lagrade proceduren för att avgöra vilka rader som är uppdateringar och som infogas. Tabell typen kan också ändras till att innehålla en "operation"-kolumn som anger om den angivna raden ska infogas, uppdateras eller tas bort.

I följande tabell visas ad hoc-testresultat för användning av tabell värdes parametrar i millisekunder.

| Operations | Lokalt till Azure (MS) | Azure-samma data Center (MS) |
| --- | --- | --- |
| 1 |124 |32 |
| 10 |131 |25 |
| 100 |338 |51 |
| 1000 |2615 |382 |
| 10000 |23830 |3586 |

> [!NOTE]
> Resultaten är inte benchmarks. Se [information om tidtagnings resultat i den här artikeln](#note-about-timing-results-in-this-article).

Prestanda ökningen från batching är direkt uppenbar. I föregående sekventiella test tog 1000 åtgärder 129 sekunder utanför data centret och 21 sekunder inifrån data centret. Men med tabell värdes parametrar tar 1000 åtgärder bara 2,6 sekunder utanför data centret och 0,4 sekunder i data centret.

Mer information om tabell värdes parametrar finns i [tabell värdes parametrar](/sql/relational-databases/tables/use-table-valued-parameters-database-engine).

### <a name="sql-bulk-copy"></a>SQL Mass kopiering

SQL Mass kopiering är ett annat sätt att infoga stora mängder data i en mål databas. .NET-program kan använda **SqlBulkCopy** -klassen för att utföra Mass infognings åtgärder. **SqlBulkCopy** liknar kommando rads verktyget **Bcp.exe** eller Transact-SQL-instruktionen **bulk INSERT**. I följande kod exempel visas hur du kopierar raderna i käll- **DataTable**-tabellen till mål tabellen, tabellen.

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    connection.Open();

    using (SqlBulkCopy bulkCopy = new SqlBulkCopy(connection))
    {
        bulkCopy.DestinationTableName = "MyTable";
        bulkCopy.ColumnMappings.Add("mytext", "mytext");
        bulkCopy.ColumnMappings.Add("num", "num");
        bulkCopy.WriteToServer(table);
    }
}
```

Det finns vissa fall där Mass kopiering föredras över tabell värdes parametrar. Se jämförelse tabellen för Table-Valued parametrar jämfört med BULK INSERT åtgärder i artikelns [tabell värdes parametrar](/sql/relational-databases/tables/use-table-valued-parameters-database-engine).

Följande ad hoc-testresultat visar prestanda för batch-körning med **SqlBulkCopy** i millisekunder.

| Operations | Lokalt till Azure (MS) | Azure-samma data Center (MS) |
| --- | --- | --- |
| 1 |433 |57 |
| 10 |441 |32 |
| 100 |636 |53 |
| 1000 |2535 |341 |
| 10000 |21605 |2737 |

> [!NOTE]
> Resultaten är inte benchmarks. Se [information om tidtagnings resultat i den här artikeln](#note-about-timing-results-in-this-article).

I mindre batch-storlekar utförde Table-Value-parametrarna **SqlBulkCopy** -klassen. **SqlBulkCopy** utförde dock 12-31% snabbare än tabell värdes parametrar för test av 1 000-och 10 000-rader. Precis som tabell värdes parametrar är **SqlBulkCopy** ett bra alternativ för batch-infogade infogningar, särskilt i jämförelse med prestanda för icke-batch-åtgärder.

Mer information om Mass kopiering i ADO.NET finns i [Mass kopierings åtgärder](/dotnet/framework/data/adonet/sql/bulk-copy-operations-in-sql-server).

### <a name="multiple-row-parameterized-insert-statements"></a>INSERT-satser med parametrar med flera rader

Ett alternativ för små batchar är att skapa en stor parameter INSERT-instruktion som infogar flera rader. I följande kod exempel demonstreras den här tekniken.

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    connection.Open();

    string insertCommand = "INSERT INTO [MyTable] ( mytext, num ) " +
        "VALUES (@p1, @p2), (@p3, @p4), (@p5, @p6), (@p7, @p8), (@p9, @p10)";

    SqlCommand cmd = new SqlCommand(insertCommand, connection);

    for (int i = 1; i <= 10; i += 2)
    {
        cmd.Parameters.Add(new SqlParameter("@p" + i.ToString(), "test"));
        cmd.Parameters.Add(new SqlParameter("@p" + (i+1).ToString(), i));
    }

    cmd.ExecuteNonQuery();
}
```

Det här exemplet är tänkt att visa det grundläggande konceptet. Ett mer realistiskt scenario skulle gå igenom de entiteter som krävs för att skapa frågesträngen och kommando parametrarna samtidigt. Du är begränsad till totalt 2100 frågeparametrar, så detta begränsar det totala antalet rader som kan bearbetas på det här sättet.

Följande ad hoc-testresultat visar prestandan för den här typen av INSERT-instruktion i millisekunder.

| Operations | Tabell värdes parametrar (MS) | Infoga en sats (MS) |
| --- | --- | --- |
| 1 |32 |20 |
| 10 |30 |25 |
| 100 |33 |51 |

> [!NOTE]
> Resultaten är inte benchmarks. Se [information om tidtagnings resultat i den här artikeln](#note-about-timing-results-in-this-article).

Den här metoden kan vara något snabbare för batchar som är mindre än 100 rader. Även om förbättringen är liten är den här metoden ett annat alternativ som kanske fungerar bra i ditt specifika program scenario.

### <a name="dataadapter"></a>DataAdapter

Med klassen **DataAdapter** kan du ändra ett **data mängds** objekt och sedan skicka ändringarna som INSERT-, Update-och Delete-åtgärder. Om du använder **dataadaptern** på det här sättet är det viktigt att notera att separata anrop görs för varje distinkt åtgärd. Förbättra prestanda genom att använda egenskapen **UpdateBatchSize** till antalet åtgärder som ska grupperas i taget. Mer information finns i [utföra batch-åtgärder med DataAdapters](/dotnet/framework/data/adonet/performing-batch-operations-using-dataadapters).

### <a name="entity-framework"></a>Entity Framework

[Entity Framework Core](/ef/efcore-and-ef6/#saving-data) stöder batching.

### <a name="xml"></a>XML

För klar Ande är det viktigt att prata om XML som en batch-strategi. Användningen av XML har dock inga fördelar jämfört med andra metoder och flera nack delar. Metoden liknar tabell värdes parametrar, men en XML-fil eller sträng skickas till en lagrad procedur i stället för en användardefinierad tabell. Den lagrade proceduren tolkar kommandona i den lagrade proceduren.

Det finns flera nack delar med den här metoden:

* Att arbeta med XML kan vara besvärligt och fel känsligt.
* Att parsa XML-koden i databasen kan vara processor intensiv.
* I de flesta fall är den här metoden långsammare än tabell värdes parametrar.

Av dessa skäl rekommenderas inte användning av XML för batch-frågor.

## <a name="batching-considerations"></a>Batch-överväganden

I följande avsnitt finns mer information om hur du använder batching i Azure SQL Database och Azure SQL-hanterade instans program.

### <a name="tradeoffs"></a>Avvägningar

Beroende på arkitekturen kan batchbearbetningen medföra en kompromiss mellan prestanda och återhämtning. Anta till exempel scenariot där rollen inte förväntas stängas av. Om du tappar bort en datarad är effekten mindre än effekten av att förlora en stor grupp med rader som inte har skickats. Det finns en större risk när du buffrar rader innan du skickar dem till databasen i en angiven tids period.

På grund av den här kompromissen ska du utvärdera den typ av åtgärder som du batchar. Batch mer aggressivt (större batchar och längre tid Windows) med data som är mindre viktiga.

### <a name="batch-size"></a>Batchstorlek

I våra tester var det vanligt vis ingen fördel med att dela upp stora partier i mindre segment. I själva verket resulterade denna del delning ofta i sämre prestanda än att skicka in en enda stor batch. Anta till exempel ett scenario där du vill infoga 1000 rader. I följande tabell visas hur lång tid det tar att använda tabell värdes parametrar för att infoga 1000 rader när de delas upp i mindre batchar.

| Batchstorlek | Iterationer | Tabell värdes parametrar (MS) |
| --- | --- | --- |
| 1000 |1 |347 |
| 500 |2 |355 |
| 100 |10 |465 |
| 50 |20 |630 |

> [!NOTE]
> Resultaten är inte benchmarks. Se [information om tidtagnings resultat i den här artikeln](#note-about-timing-results-in-this-article).

Du ser att den bästa prestandan för 1000 rader är att skicka alla samtidigt. I andra tester (visas inte här) fanns det en liten prestanda vinst för att bryta en 10000-rad-batch i två batchar med 5000. Men tabell schemat för de här testerna är relativt enkelt, så du bör utföra tester på dina speciella data-och batchstorleken för att verifiera dessa resultat.

En annan faktor är att om den totala batchen blir för stor kan Azure SQL Database eller Azure SQL-hanterad instans begränsa och neka för att genomföra batchen. Testa ditt speciella scenario för att avgöra om det finns en idealisk batchstorlek för bästa möjliga resultat. Gör batchstorleken konfigurerbar vid körning för att möjliggöra snabba justeringar baserat på prestanda eller fel.

Slutligen kan du balansera storleken på batchen med de risker som är kopplade till batchen. Om det uppstår tillfälliga fel eller om rollen Miss lyckas, bör du överväga konsekvenserna av att försöka utföra åtgärden igen eller förlora data i batchen.

### <a name="parallel-processing"></a>Parallellbearbetning

Vad händer om du använde metoden för att minska batchstorleken men använda flera trådar för att köra jobbet? Vi har återigen visat att flera mindre flertrådade batchar vanligt vis utfördes sämre än en enda större grupp. Följande test försöker infoga 1000 rader i en eller flera parallella batchar. Det här testet visar hur fler samtidiga batchar faktiskt försämrade prestanda.

| Batchstorlek [iterationer] | Två trådar (MS) | Fyra trådar (MS) | Sex trådar (MS) |
| --- | --- | --- | --- |
| 1000 [1] |277 |315 |266 |
| 500 [2] |548 |278 |256 |
| 250 [4] |405 |329 |265 |
| 100 [10] |488 |439 |391 |

> [!NOTE]
> Resultaten är inte benchmarks. Se [information om tidtagnings resultat i den här artikeln](#note-about-timing-results-in-this-article).

Det finns flera möjliga orsaker till försämringen av prestanda på grund av parallellitet:

* Det finns flera samtidiga nätverks anrop i stället för ett.
* Flera åtgärder mot en enskild tabell kan leda till konkurrens och blockering.
* Det finns överhuvuder som är associerade med multitrådning.
* Kostnaden för att öppna flera anslutningar drar nytta av fördelarna med parallell bearbetning.

Om du använder olika tabeller eller databaser, är det möjligt att se vissa prestanda vinster i den här strategin. Databasens horisontell partitionering eller federationer är ett scenario för den här metoden. Horisontell partitionering använder flera databaser och dirigerar olika data till varje databas. Om varje liten batch kommer till en annan databas, kan det vara mer effektivt att utföra åtgärderna parallellt. Prestanda ökningen är dock inte tillräckligt betydande för att kunna användas som grund för ett beslut att använda databas horisontell partitionering i din lösning.

I vissa konstruktioner kan parallell körning av mindre batchar leda till förbättrat data flöde för begär anden i ett system som läses in. I det här fallet, trots att det går snabbare att bearbeta en enda större grupp, kan det vara mer effektivt att bearbeta flera batchar parallellt.

Om du använder parallell körning bör du överväga att kontrol lera det högsta antalet arbets trådar. Ett mindre antal kan leda till mindre konkurrens och snabbare körnings tid. Överväg även den ytterligare belastning som finns på mål databasen både i anslutningar och transaktioner.

### <a name="related-performance-factors"></a>Relaterade prestanda faktorer

En typisk vägledning om databas prestandan påverkar även batching. Till exempel minskas prestanda för tabeller som har en stor primär nyckel eller många grupperade index.

Om tabell värdes parametrar använder en lagrad procedur kan du använda kommandot **NOCOUNT i** i början av proceduren. Den här instruktionen förhindrar att antalet påverkade rader returneras i proceduren. I våra tester hade du dock ingen effekt eller minskad prestanda om användningen av **set NOcount** saknas. Den lagrade proceduren för testet var enkel med ett enda **insert** -kommando från tabell värdes parametern. Det är möjligt att mer komplexa lagrade procedurer kan dra nytta av den här instruktionen. Men anta inte att lägga till **SET NOCOUNT på** den lagrade proceduren förbättrar prestandan automatiskt. Om du vill förstå resultatet testar du den lagrade proceduren med och utan **SET NOCOUNT ON** -instruktionen.

## <a name="batching-scenarios"></a>Batching-scenarier

I följande avsnitt beskrivs hur du använder tabell värdes parametrar i tre program scenarier. Det första scenariot visar hur buffring och batchbearbetning kan fungera tillsammans. Det andra scenariot förbättrar prestanda genom att utföra Master-detail-åtgärder i ett enda lagrat procedur anrop. Det slutliga scenariot visar hur du använder tabell värdes parametrar i en "UPSERT"-åtgärd.

### <a name="buffering"></a>Buffrar

Även om det finns några scenarier som är uppenbara för batching, finns det många scenarier som kan dra nytta av satsvis kompilering genom fördröjd bearbetning. Fördröjd bearbetning medför dock också en större risk att data förloras vid ett oväntat fel. Det är viktigt att förstå den här risken och ta hänsyn till konsekvenserna.

Anta till exempel ett webb program som spårar navigerings historiken för varje användare. På varje webbdelsbegäran kan programmet göra ett databas anrop för att registrera användarens sid visning. Men högre prestanda och skalbarhet kan uppnås genom att buffra användarnas navigerings aktiviteter och sedan skicka dessa data till databasen i batchar. Du kan utlösa databas uppdateringen efter förfluten tid och/eller buffertstorlek. En regel kan till exempel ange att batchen ska bearbetas efter 20 sekunder eller när bufferten når 1000 objekt.

I följande kod exempel används [reactive Extensions-RX](/previous-versions/dotnet/reactive-extensions/hh242985(v=vs.103)) för att bearbeta buffrade händelser som skapats av en övervaknings klass. När buffertens fyllningar eller tids gräns uppnås, skickas batchen med användar data till databasen med en tabell värdes parameter.

I följande NavHistoryData-klass modelleras information om användar navigering. Den innehåller grundläggande information, till exempel användar-ID, URL: en och åtkomst tiden.

```csharp
public class NavHistoryData
{
    public NavHistoryData(int userId, string url, DateTime accessTime)
    { UserId = userId; URL = url; AccessTime = accessTime; }
    public int UserId { get; set; }
    public string URL { get; set; }
    public DateTime AccessTime { get; set; }
}
```

NavHistoryDataMonitor-klassen ansvarar för att buffra användar navigerings data till-databasen. Den innehåller en metod, RecordUserNavigationEntry, som svarar genom att höja en **OnAdded** -händelse. Följande kod visar den konstruktormetod som använder RX för att skapa en observerbar samling baserat på händelsen. Den prenumererar sedan på den observerbara samlingen med metoden buffer. Överlagringen anger att bufferten ska skickas var 20: e sekund eller 1000 poster.

```csharp
public NavHistoryDataMonitor()
{
    var observableData =
        Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");

    observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);
}
```

Hanteraren konverterar alla buffrade objekt till en tabell värdes typ och skickar sedan den här typen till en lagrad procedur som bearbetar batchen. Följande kod visar den fullständiga definitionen för både NavHistoryDataEventArgs-och NavHistoryDataMonitor-klasserna.

```csharp
public class NavHistoryDataEventArgs : System.EventArgs
{
    public NavHistoryDataEventArgs(NavHistoryData data) { Data = data; }
    public NavHistoryData Data { get; set; }
}

public class NavHistoryDataMonitor
{
    public event EventHandler<NavHistoryDataEventArgs> OnAdded;

    public NavHistoryDataMonitor()
    {
        var observableData =
            Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");

        observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);
    }
```

Hanteraren konverterar alla buffrade objekt till en tabell värdes typ och skickar sedan den här typen till en lagrad procedur som bearbetar batchen. Följande kod visar den fullständiga definitionen för både NavHistoryDataEventArgs-och NavHistoryDataMonitor-klasserna.

```csharp
    public class NavHistoryDataEventArgs : System.EventArgs
    {
        if (OnAdded != null)
            OnAdded(this, new NavHistoryDataEventArgs(data));
    }

    protected void Handler(IList<EventPattern<NavHistoryDataEventArgs>> items)
    {
        DataTable navHistoryBatch = new DataTable("NavigationHistoryBatch");
        navHistoryBatch.Columns.Add("UserId", typeof(int));
        navHistoryBatch.Columns.Add("URL", typeof(string));
        navHistoryBatch.Columns.Add("AccessTime", typeof(DateTime));
        foreach (EventPattern<NavHistoryDataEventArgs> item in items)
        {
            NavHistoryData data = item.EventArgs.Data;
            navHistoryBatch.Rows.Add(data.UserId, data.URL, data.AccessTime);
        }

        using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
        {
            connection.Open();

            SqlCommand cmd = new SqlCommand("sp_RecordUserNavigation", connection);
            cmd.CommandType = CommandType.StoredProcedure;

            cmd.Parameters.Add(
                new SqlParameter()
                {
                    ParameterName = "@NavHistoryBatch",
                    SqlDbType = SqlDbType.Structured,
                    TypeName = "NavigationHistoryTableType",
                    Value = navHistoryBatch,
                });

            cmd.ExecuteNonQuery();
        }
    }
}
```

Om du vill använda den här bufferten skapar programmet ett statiskt NavHistoryDataMonitor-objekt. Varje gång en användare kommer åt en sida anropar programmet metoden NavHistoryDataMonitor. RecordUserNavigationEntry. Buffertens logik fortsätter att skicka dessa poster till databasen i batchar.

### <a name="master-detail"></a>Huvud information

Tabell värdes parametrar är användbara för enkla INFOGNINGs scenarier. Det kan dock vara mer utmanande att gruppera infogningar som omfattar mer än en tabell. Scenariot "Master/Detail" är ett användbart exempel. Huvud tabellen identifierar den primära entiteten. En eller flera detalj tabeller lagrar mer data om entiteten. I det här scenariot tvingar sekundär nyckel relationer relationen mellan information till en unik huvud organisation. Överväg en förenklad version av en PurchaseOrder-tabell och dess tillhör ande OrderDetail-tabell. Följande Transact-SQL skapar tabellen PurchaseOrder med fyra kolumner: Ordernr, order datum, Kundnr och status.

```sql
CREATE TABLE [dbo].[PurchaseOrder](
[OrderID] [int] IDENTITY(1,1) NOT NULL,
[OrderDate] [datetime] NOT NULL,
[CustomerID] [int] NOT NULL,
[Status] [nvarchar](50) NOT NULL,
CONSTRAINT [PrimaryKey_PurchaseOrder]
PRIMARY KEY CLUSTERED ( [OrderID] ASC ))
```

Varje order innehåller ett eller flera produkt inköp. Den här informationen samlas in i tabellen PurchaseOrderDetail. Följande Transact-SQL skapar tabellen PurchaseOrderDetail med fem kolumner: Ordernr, OrderDetailID, ProductID, enhets pris och OrderQty.

```sql
CREATE TABLE [dbo].[PurchaseOrderDetail](
[OrderID] [int] NOT NULL,
[OrderDetailID] [int] IDENTITY(1,1) NOT NULL,
[ProductID] [int] NOT NULL,
[UnitPrice] [money] NULL,
[OrderQty] [smallint] NULL,
CONSTRAINT [PrimaryKey_PurchaseOrderDetail] PRIMARY KEY CLUSTERED
( [OrderID] ASC, [OrderDetailID] ASC ))
```

Kolumnen Ordernr i tabellen PurchaseOrderDetail måste referera till en order från PurchaseOrder-tabellen. Följande definition av en sekundär nyckel tillämpar den här begränsningen.

```sql
ALTER TABLE [dbo].[PurchaseOrderDetail]  WITH CHECK ADD
CONSTRAINT [FK_OrderID_PurchaseOrder] FOREIGN KEY([OrderID])
REFERENCES [dbo].[PurchaseOrder] ([OrderID])
```

Du måste ha en användardefinierad tabell typ för varje mål tabell för att kunna använda tabell värdes parametrar.

```sql
CREATE TYPE PurchaseOrderTableType AS TABLE
( OrderID INT,
    OrderDate DATETIME,
    CustomerID INT,
    Status NVARCHAR(50) );
GO

CREATE TYPE PurchaseOrderDetailTableType AS TABLE
( OrderID INT,
    ProductID INT,
    UnitPrice MONEY,
    OrderQty SMALLINT );
GO
```

Definiera sedan en lagrad procedur som accepterar tabeller av de här typerna. Den här proceduren gör det möjligt för ett program att lokalt gruppera en uppsättning beställningar och beställnings information i ett enda anrop. Följande Transact-SQL innehåller den fullständiga lagrade procedur deklarationen för den här inköps order exemplet.

```sql
CREATE PROCEDURE sp_InsertOrdersBatch (
@orders as PurchaseOrderTableType READONLY,
@details as PurchaseOrderDetailTableType READONLY )
AS
SET NOCOUNT ON;

-- Table that connects the order identifiers in the @orders
-- table with the actual order identifiers in the PurchaseOrder table
DECLARE @IdentityLink AS TABLE (
SubmittedKey int,
ActualKey int,
RowNumber int identity(1,1)
);

-- Add new orders to the PurchaseOrder table, storing the actual
-- order identifiers in the @IdentityLink table
INSERT INTO PurchaseOrder ([OrderDate], [CustomerID], [Status])
OUTPUT inserted.OrderID INTO @IdentityLink (ActualKey)
SELECT [OrderDate], [CustomerID], [Status] FROM @orders ORDER BY OrderID;

-- Match the passed-in order identifiers with the actual identifiers
-- and complete the @IdentityLink table for use with inserting the details
WITH OrderedRows As (
SELECT OrderID, ROW_NUMBER () OVER (ORDER BY OrderID) As RowNumber
FROM @orders
)
UPDATE @IdentityLink SET SubmittedKey = M.OrderID
FROM @IdentityLink L JOIN OrderedRows M ON L.RowNumber = M.RowNumber;

-- Insert the order details into the PurchaseOrderDetail table,
-- using the actual order identifiers of the master table, PurchaseOrder
INSERT INTO PurchaseOrderDetail (
[OrderID],
[ProductID],
[UnitPrice],
[OrderQty] )
SELECT L.ActualKey, D.ProductID, D.UnitPrice, D.OrderQty
FROM @details D
JOIN @IdentityLink L ON L.SubmittedKey = D.OrderID;
GO
```

I det här exemplet lagrar den lokalt definierade @IdentityLink tabellen faktiska Ordernr-värden från de nyligen infogade raderna. Dessa order identifierare skiljer sig från de tillfälliga Ordernr-värdena i @orders parametrarna och @details tabell värden. Därför ansluter tabellen värdet för @IdentityLink Ordernr från @orders parametern till de faktiska Ordernr-värdena för de nya raderna i tabellen PurchaseOrder. Efter det här steget @IdentityLink kan tabellen under lätta infogningen av beställnings informationen med det faktiska Ordernr som uppfyller sekundär nyckel begränsningen.

Den här lagrade proceduren kan användas från kod eller från andra Transact-SQL-anrop. I avsnittet tabell värdes parametrar i det här dokumentet finns ett kod exempel. Följande Transact-SQL visar hur du anropar sp_InsertOrdersBatch.

```sql
declare @orders as PurchaseOrderTableType
declare @details as PurchaseOrderDetailTableType

INSERT @orders
([OrderID], [OrderDate], [CustomerID], [Status])
VALUES(1, '1/1/2013', 1125, 'Complete'),
(2, '1/13/2013', 348, 'Processing'),
(3, '1/12/2013', 2504, 'Shipped')

INSERT @details
([OrderID], [ProductID], [UnitPrice], [OrderQty])
VALUES(1, 10, $11.50, 1),
(1, 12, $1.58, 1),
(2, 23, $2.57, 2),
(3, 4, $10.00, 1)

exec sp_InsertOrdersBatch @orders, @details
```

Den här lösningen gör att varje batch kan använda en uppsättning av Ordernr-värden som börjar vid 1. Dessa tillfälliga Ordernr-värden beskriver relationerna i batchen, men de faktiska värdet för Ordernr fastställs vid tidpunkten för infognings åtgärden. Du kan köra samma instruktioner i föregående exempel upprepade gånger och generera unika order i databasen. Därför bör du överväga att lägga till mer kod eller databas logik som förhindrar dubbla beställningar när du använder den här batch-tekniken.

Det här exemplet visar att ännu mer komplexa databas åtgärder, till exempel Master-detail-åtgärder, kan grupperas med tabell värdes parametrar.

### <a name="upsert"></a>UPSERT

Ett annat batching-scenario innebär att samtidigt uppdatera befintliga rader och infoga nya rader. Den här åtgärden kallas ibland för en "UPSERT"-åtgärd (uppdatering + Insert). I stället för att göra separata anrop för att infoga och uppdatera, är MERGE-instruktionen bäst anpassad för den här uppgiften. MERGE-instruktionen kan utföra både INSERT-och Update-åtgärder i ett enda anrop.

Tabell värdes parametrar kan användas med MERGE-instruktionen för att utföra uppdateringar och infogningar. Anta till exempel en förenklad medarbetar tabell som innehåller följande kolumner: Anställningsnr, FirstName, LastName, SocialSecurityNumber:

```sql
CREATE TABLE [dbo].[Employee](
[EmployeeID] [int] IDENTITY(1,1) NOT NULL,
[FirstName] [nvarchar](50) NOT NULL,
[LastName] [nvarchar](50) NOT NULL,
[SocialSecurityNumber] [nvarchar](50) NOT NULL,
CONSTRAINT [PrimaryKey_Employee] PRIMARY KEY CLUSTERED
([EmployeeID] ASC ))
```

I det här exemplet kan du använda det faktum att SocialSecurityNumber är unikt för att utföra en sammanslagning av flera anställda. Börja med att skapa en användardefinierad tabell typ:

```sql
CREATE TYPE EmployeeTableType AS TABLE
( Employee_ID INT,
    FirstName NVARCHAR(50),
    LastName NVARCHAR(50),
    SocialSecurityNumber NVARCHAR(50) );
GO
```

Skapa sedan en lagrad procedur eller Skriv kod som använder MERGE-instruktionen för att utföra uppdateringen och infoga. I följande exempel används MERGE-instruktionen på en tabell värdes parameter, @employees av typen EmployeeTableType. Innehållet i @employees tabellen visas inte här.

```sql
MERGE Employee AS target
USING (SELECT [FirstName], [LastName], [SocialSecurityNumber] FROM @employees)
AS source ([FirstName], [LastName], [SocialSecurityNumber])
ON (target.[SocialSecurityNumber] = source.[SocialSecurityNumber])
WHEN MATCHED THEN
UPDATE SET
target.FirstName = source.FirstName,
target.LastName = source.LastName
WHEN NOT MATCHED THEN
    INSERT ([FirstName], [LastName], [SocialSecurityNumber])
    VALUES (source.[FirstName], source.[LastName], source.[SocialSecurityNumber]);
```

Mer information finns i dokumentationen och exemplen för MERGE-instruktionen. Även om samma arbete kan utföras i ett process anrop med flera steg med separata INFOGNINGs-och UPPDATERINGs åtgärder, är MERGE-instruktionen mer effektiv. Databas kod kan också konstruera Transact-SQL-anrop som använder MERGE-instruktionen direkt utan att kräva två databas anrop för INSERT och UPDATE.

## <a name="recommendation-summary"></a>Rekommendations Sammanfattning

I följande lista finns en sammanfattning av de grupp rekommendationer som beskrivs i den här artikeln:

* Använd buffring och batching för att öka prestanda och skalbarhet för Azure SQL Database och Azure SQL-hanterade instans program.
* Förstå kompromisserna mellan satsvis kompilering och buffring och återhämtning. Vid ett roll haveri kan risken att förlora en icke bearbetad batch med affärs kritiska data överväger prestanda fördelarna med batchering.
* Försök att behålla alla anrop till databasen inom ett och samma data Center för att minska svars tiden.
* Om du väljer en enda batch-teknik ger tabell värdes parametrar bästa möjliga prestanda och flexibilitet.
* Om du vill ha snabbaste infognings prestanda följer du dessa allmänna rikt linjer men testar ditt scenario:
  * Använd ett enda parameter INSERT-kommando för < 100-rader.
  * Använd tabell värdes parametrar för < 1000-rader.
  * Använd SqlBulkCopy för att >= 1000 rader.
* För uppdaterings-och borttagnings åtgärder använder du tabell värdes parametrar med lagrad procedur logik som fastställer rätt åtgärd för varje rad i tabell parametern.
* Rikt linjer för batchstorlek:
  * Använd de största batch-storlekarna som passar dina program-och affärs behov.
  * Balansera prestanda vinsten för stora batchar med riskerna med tillfälliga eller oåterkalleliga haverier. Vad är resultatet av återförsök eller förlust av data i batchen?
  * Testa den största batchstorleken för att verifiera att Azure SQL Database eller Azure SQL-hanterad instans inte avvisar den.
  * Skapa konfigurations inställningar som styr batchbearbetning, t. ex. batch-storlek eller tids period för buffring. De här inställningarna ger flexibilitet. Du kan ändra batching-beteendet i produktion utan att omdistribuera moln tjänsten.
* Undvik parallell körning av batchar som körs på en enskild tabell i en databas. Om du väljer att dela upp en enskild grupp över flera arbets trådar kan du köra tester för att fastställa det idealiska antalet trådar. Efter ett ospecificerat tröskelvärde minskar fler trådar prestanda i stället för att öka det.
* Överväg att buffra i storlek och tid som ett sätt att implementera batching för fler scenarier.

## <a name="next-steps"></a>Nästa steg

Den här artikeln fokuserar på hur databas design och kodnings tekniker som är relaterade till batchbearbetning kan förbättra programmets prestanda och skalbarhet. Men det är bara en faktor i din övergripande strategi. Fler sätt att förbättra prestanda och skalbarhet finns i [databas prestanda vägledning](database/performance-guidance.md) och [pris-och prestanda överväganden för en elastisk pool](database/elastic-pool-overview.md).