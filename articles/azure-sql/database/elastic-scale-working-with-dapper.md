---
title: Använda klient biblioteket för Elastic Database med dapper
description: Använda klient biblioteket för Elastic Database med dapper.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: d660e62ea293bd3cc377b95612cfaf41a9f1cd6a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92793372"
---
# <a name="using-the-elastic-database-client-library-with-dapper"></a>Använda klient biblioteket för Elastic Database med dapper
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Det här dokumentet är för utvecklare som är beroende av dapper för att skapa program, men som även vill använda [elastiska databas verktyg](elastic-scale-introduction.md) för att skapa program som implementerar horisontell partitionering för att skala ut data nivån.  Det här dokumentet illustrerar ändringarna i dapper-baserade program som behövs för att integrera med Elastic Database-verktyg. Vårt fokus är att skapa en elastisk databas Shard hantering och data beroende routning med dapper. 

**Exempel kod**: [elastiska databas verktyg för Azure SQL Database-dapper-integrering](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-e19fc77f).

Det är enkelt att integrera **dapper** och **DapperExtensions** med klient biblioteket för Elastic Database för Azure SQL Database. Dina program kan använda data beroende routning genom att ändra skapandet och öppningen av nya [SQLConnection](/dotnet/api/system.data.sqlclient.sqlconnection) -objekt för att använda [OpenConnectionForKey](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) -anropet från [klient biblioteket](/previous-versions/azure/dn765902(v=azure.100)). Detta begränsar ändringar i programmet till endast de nya anslutningar som skapas och öppnas. 

## <a name="dapper-overview"></a>Översikt över dapper
**Dapper** är en objekt Relations mappning. Den mappar .NET-objekt från ditt program till en Relations databas (och vice versa). Den första delen av exempel koden illustrerar hur du kan integrera klient biblioteket för Elastic Database med dapper-baserade program. Den andra delen av exempel koden illustrerar hur du integrerar när du använder både dapper och DapperExtensions.  

Funktionen Mapper i dapper ger tilläggs metoder för databas anslutningar som fören klar sändning av T-SQL-uttryck för att köra eller fråga databasen. Till exempel gör dapper det enkelt att mappa mellan dina .NET-objekt och parametrarna för SQL-uttryck för att **köra** anrop, eller för att använda resultatet av dina SQL-frågor i .net-objekt med hjälp av **fråga** -anrop från dapper. 

När du använder DapperExtensions behöver du inte längre ange SQL-uttryck. Utöknings metoder som **GetList** eller **insert** över databas anslutningen skapar SQL-uttryck bakom scenerna.

En annan fördel med dapper och även DapperExtensions är att programmet styr skapandet av databas anslutningen. Detta hjälper till att interagera med klient biblioteket för Elastic Database som utlöser databas anslutningar baserat på mappningen av shardletar till databaser.

För att hämta dapper-sammansättningarna, se [dapper dot net](https://www.nuget.org/packages/Dapper/). För dapper-tilläggen, se [DapperExtensions](https://www.nuget.org/packages/DapperExtensions).

## <a name="a-quick-look-at-the-elastic-database-client-library"></a>En snabb titt på klient biblioteket för Elastic Database
Med klient biblioteket för Elastic Database definierar du partitioner för dina program data som kallas *shardletar*, mappar dem till databaser och identifierar dem med *horisontell partitionering-nycklar*. Du kan ha så många databaser som du behöver och distribuera shardletar över dessa databaser. Mappningen av horisontell partitionering-nyckel värden till databaserna lagras av en Shard-mappning från bibliotekets API: er. Den här funktionen kallas **Shard Map Management**. Shard-kartan fungerar också som koordinator för databas anslutningar för begär Anden som har en horisontell partitionering-nyckel. Den här funktionen kallas för **data beroende routning**.

![Shard Maps och data beroende routning][1]

Shard Map Manager skyddar användare från inkonsekventa vyer till shardlet-data som kan uppstå när samtidiga shardlet hanterings åtgärder sker i databaserna. För att göra det, Shard Maps-tjänsten databas anslutningarna för ett program som skapats med biblioteket. När Shard hanterings åtgärder kan påverka shardlet, tillåter detta att funktionen Shard mappar automatiskt avdöda en databas anslutning. 

I stället för att använda det traditionella sättet att skapa anslutningar för dapper, måste du använda [OpenConnectionForKey-metoden](/previous-versions/azure/dn824099(v=azure.100)). Detta säkerställer att alla verifieringar sker och att anslutningar hanteras korrekt när data flyttas mellan Shards.

### <a name="requirements-for-dapper-integration"></a>Krav för dapper-integrering
När du arbetar med både klient biblioteket för Elastic Database och dapper-API: erna, vill du behålla följande egenskaper:

* **Skala ut**: vi vill lägga till eller ta bort databaser från data nivån i shardade-programmet vid behov för programmets kapacitets krav. 
* **Konsekvens**: eftersom programmet skalas ut med horisontell partitionering måste du utföra data beroende routning. Vi vill använda de data beroende Dirigerings funktionerna i biblioteket. I synnerhet vill du behålla de verifierings-och konsekvens garantier som tillhandahålls av anslutningar som har frigörs genom Shard Map Manager för att undvika fel eller felaktiga frågeresultat. Detta säkerställer att anslutningar till en specifik shardlet avvisas eller stoppas om shardlet för närvarande flyttas till en annan Shard med hjälp av API: er för delad/sammanslagning.
* **Objekt mappning**: vi vill behålla bekvämligheten för de mappningar som tillhandahålls av dapper för att översätta mellan klasser i programmet och de underliggande databas strukturerna. 

Följande avsnitt innehåller vägledning för de här kraven för program som baseras på **dapper** och **DapperExtensions**.

## <a name="technical-guidance"></a>Teknisk vägledning
### <a name="data-dependent-routing-with-dapper"></a>Data beroende routning med dapper
Med dapper ansvarar programmet vanligt vis för att skapa och öppna anslutningarna till den underliggande databasen. Med en typ T för programmet returnerar dapper frågeresultat som .NET-samlingar av typen T. dapper utför mappningen från T-SQL-resultat raderna till objekt av typen T. På samma sätt mappar dapper .NET-objekt till SQL-värden eller parametrar för DML-uttryck (Data Manipulation Language). Dapper erbjuder den här funktionen via tilläggs metoder på det vanliga [SQLConnection](/dotnet/api/system.data.sqlclient.sqlconnection) -objektet från ADO .net SQL-klientens bibliotek. SQL-anslutningen som returneras av de elastiska skalnings-API: erna för DDR är också vanliga [SQLConnection](/dotnet/api/system.data.sqlclient.sqlconnection) -objekt. Detta gör att vi kan använda dapper-tillägg direkt via den typ som returneras av klient bibliotekets DDR-API, eftersom det också är en enkel SQL-klient anslutning.

Dessa observationer gör det enkelt att använda anslutningar som har utjämnas av klient biblioteket för Elastic Database för dapper.

Det här kod exemplet (från det medföljande exemplet) illustrerar hur horisontell partitionering-nyckeln tillhandahålls av programmet till biblioteket för att tillämpa anslutningen till rätt Shard.   

```csharp
    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                     key: tenantId1,
                     connectionString: connStrBldr.ConnectionString,
                     options: ConnectionOptions.Validate))
    {
        var blog = new Blog { Name = name };
        sqlconn.Execute(@"
                      INSERT INTO
                            Blog (Name)
                            VALUES (@name)", new { name = blog.Name }
                        );
    }
```

Anropet till [OpenConnectionForKey](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) -API: et ersätter standard skapandet och öppningen av en SQL-klient anslutning. [OpenConnectionForKey](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) -anropet tar de argument som krävs för data beroende Routning: 

* Shard-kartan för att komma åt data beroende routningsgränssnitt
* Horisontell partitionering-nyckeln för att identifiera shardlet
* Autentiseringsuppgifterna (användar namn och lösen ord) för att ansluta till Shard

Shard Map-objektet skapar en anslutning till Shard som innehåller shardlet för den aktuella horisontell partitionering-nyckeln. Klient-API: erna för Elastic Database tagga också anslutningen för att implementera konsekvens garantier. Eftersom anropet till [OpenConnectionForKey](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) returnerar ett objekt av vanlig SQL-klient är det efterföljande anropet till metoden för att **köra** tillägg från dapper följande standard-dapper-praxis.

Frågor fungerar mycket på samma sätt – du först öppnar anslutningen med [OpenConnectionForKey](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) från klient-API: et. Sedan använder du de vanliga dapper-tilläggs metoderna för att mappa resultatet av din SQL-fråga till .NET-objekt:

```csharp
    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId1,
                    connectionString: connStrBldr.ConnectionString,
                    options: ConnectionOptions.Validate ))
    {
           // Display all Blogs for tenant 1
           IEnumerable<Blog> result = sqlconn.Query<Blog>(@"
                                SELECT *
                                FROM Blog
                                ORDER BY Name");

           Console.WriteLine("All blogs for tenant id {0}:", tenantId1);
           foreach (var item in result)
           {
                Console.WriteLine(item.Name);
            }
    }
```

Observera att det **använda** blocket med DDR-anslutningen omfattar alla databas åtgärder i blocket till den Shard där tenantId1 hålls. Frågan returnerar bara Bloggar lagrade på den aktuella Shard, men inte de som lagras på andra Shards. 

## <a name="data-dependent-routing-with-dapper-and-dapperextensions"></a>Data beroende routning med dapper och DapperExtensions
Dapper levereras med ett eko system med ytterligare tillägg som kan ge ytterligare bekvämlighet och abstraktion från databasen när du utvecklar databas program. DapperExtensions är ett exempel. 

Om du använder DapperExtensions i ditt program ändras inte hur databas anslutningar skapas och hanteras. Det är fortfarande programmets ansvar att öppna anslutningar och vanliga SQL-klientanslutningar förväntas av tilläggs metoderna. Vi kan lita på [OpenConnectionForKey](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) enligt beskrivningen ovan. Som följande kod exempel visar är den enda ändringen att du inte längre behöver skriva T-SQL-uttryck:

```csharp
    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2,
                    connectionString: connStrBldr.ConnectionString,
                    options: ConnectionOptions.Validate))
    {
           var blog = new Blog { Name = name2 };
           sqlconn.Insert(blog);
    }
```

Här är kod exemplet för frågan: 

```csharp
    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2,
                    connectionString: connStrBldr.ConnectionString,
                    options: ConnectionOptions.Validate))
    {
           // Display all Blogs for tenant 2
           IEnumerable<Blog> result = sqlconn.GetList<Blog>();
           Console.WriteLine("All blogs for tenant id {0}:", tenantId2);
           foreach (var item in result)
           {
               Console.WriteLine(item.Name);
           }
    }
```

### <a name="handling-transient-faults"></a>Hantera tillfälliga fel
Microsoft patterns & Practices-teamet publicerade det [tillfälliga fel hanterings program blocket](/previous-versions/msp-n-p/hh680934(v=pandp.50)) för att hjälpa programutvecklare att minimera vanliga tillfälliga fel tillstånd när de körs i molnet. Mer information finns i [perseverance, hemlighet för alla Triumphs: använda program blocket för den tillfälliga fel hanteringen](/previous-versions/msp-n-p/dn440719(v=pandp.60)).

Kod exemplet förlitar sig på det tillfälliga fel biblioteket för att skydda mot tillfälliga fel. 

```csharp
    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
    {
       using (SqlConnection sqlconn =
          shardingLayer.ShardMap.OpenConnectionForKey(tenantId2, connStrBldr.ConnectionString, ConnectionOptions.Validate))
          {
              var blog = new Blog { Name = name2 };
              sqlconn.Insert(blog);
          }
    });
```

**SqlDatabaseUtils. SqlRetryPolicy** i koden ovan definieras som en **SqlDatabaseTransientErrorDetectionStrategy** med ett antal återförsök på 10 och 5 sekunders vänte tid mellan återförsök. Om du använder transaktioner ska du se till att omfånget för återförsök återgår till början av transaktionen om det rör sig om ett tillfälligt fel.

## <a name="limitations"></a>Begränsningar
De metoder som beskrivs i det här dokumentet medför några begränsningar:

* Exempel koden för det här dokumentet visar inte hur du hanterar schemat i Shards.
* Vi förutsätter att all databas bearbetning finns i en enda Shard som identifieras av horisontell partitionering-nyckeln som anges i begäran. Detta antagande är dock inte alltid kvar, till exempel när det inte går att göra en horisontell partitionering-nyckel tillgänglig. Klient biblioteket för Elastic Database inkluderar [MultiShardQuery-klassen](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardexception). Klassen implementerar en anslutnings abstraktion för frågor över flera Shards. Användning av MultiShardQuery i kombination med dapper ligger utanför det här dokumentets omfattning.

## <a name="conclusion"></a>Slutsats
Program som använder dapper och DapperExtensions kan enkelt dra nytta av elastiska databas verktyg för Azure SQL Database. Genom de steg som beskrivs i det här dokumentet kan dessa program använda verktygets funktion för data beroende routning genom att ändra skapandet och öppningen av nya [SQLConnection](/dotnet/api/system.data.sqlclient.sqlconnection) -objekt för att använda [OpenConnectionForKey](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) -anropet för klient biblioteket för Elastic Database. Detta begränsar de program ändringar som krävs för de platser där nya anslutningar skapas och öppnas. 

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/elastic-scale-working-with-dapper/dapperimage1.png