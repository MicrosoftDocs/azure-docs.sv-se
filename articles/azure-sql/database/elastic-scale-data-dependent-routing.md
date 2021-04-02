---
title: Databeroende routning
description: Använda klassen ShardMapManager i .NET-appar för data beroende routning, en funktion i shardade-databaser i Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 60e8b4b21a9e62279cd0eccfabbbed680183e2a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92787031"
---
# <a name="use-data-dependent-routing-to-route-a-query-to-an-appropriate-database"></a>Använd data beroende routning för att dirigera en fråga till en lämplig databas
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

**Data beroende routning** är möjligheten att använda data i en fråga för att dirigera begäran till en lämplig databas. Data beroende routning är ett grundläggande mönster när du arbetar med shardade-databaser. Kontexten för begäran kan också användas för att dirigera begäran, särskilt om horisontell partitionering-nyckeln inte är en del av frågan. Varje specifik fråga eller transaktion i ett program som använder data beroende routning är begränsad till åtkomst till en databas per begäran. För Azure SQL Database elastiska verktyg utförs denna routning med klassen **ShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)).

Programmet behöver inte spåra olika anslutnings strängar eller DB-platser som är kopplade till olika data sektorer i shardade-miljön. I stället öppnar [Shard Map Manager](elastic-scale-shard-map-management.md) anslutningar till rätt databaser vid behov, baserat på data i Shard-mappningen och värdet för horisontell partitionering-nyckeln som är målet för programmets begäran. Nyckeln är vanligt vis *customer_id*, *tenant_id*, *date_key* eller någon annan unik identifierare som är en grundläggande parameter för databas förfrågan.

Mer information finns i [skala ut SQL Server med Data-Dependent routing](/previous-versions/sql/sql-server-2005/administrator/cc966448(v=technet.10)).

## <a name="download-the-client-library"></a>Ladda ned klient biblioteket

Hämta:

* Java-versionen av biblioteket finns i [maven Central-lagringsplats](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools).
* .NET-versionen av biblioteket finns i [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## <a name="using-a-shardmapmanager-in-a-data-dependent-routing-application"></a>Använda en ShardMapManager i ett data beroende Dirigerings program

Program ska instansiera **ShardMapManager** under initieringen med hjälp av Factory-anropet **GetSQLShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)). I det här exemplet initieras både en **ShardMapManager** och en speciell **ShardMap** som den innehåller. I det här exemplet visas metoderna GetSqlShardMapManager och GetRangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getrangeshardmap), [.net](/previous-versions/azure/dn824173(v=azure.100))).

```Java
ShardMapManager smm = ShardMapManagerFactory.getSqlShardMapManager(connectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> rangeShardMap = smm.getRangeShardMap(Configuration.getRangeShardMapName(), ShardKeyType.Int32);
```

```csharp
ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> customerShardMap = smm.GetRangeShardMap<int>("customerMap"); 
```

### <a name="use-lowest-privilege-credentials-possible-for-getting-the-shard-map"></a>Använd lägsta möjliga behörighet för att hämta Shard-kartan

Om ett program inte ändrar själva Shard-kartan ska de autentiseringsuppgifter som används i fabriks metoden ha Läs behörighet för den **globala Shard Map** -databasen. Dessa autentiseringsuppgifter skiljer sig vanligt vis från autentiseringsuppgifter som används för att öppna anslutningar till Shard Map Manager. Se även [autentiseringsuppgifter som används för att få åtkomst till Elastic Database klient biblioteket](elastic-scale-manage-credentials.md).

## <a name="call-the-openconnectionforkey-method"></a>Anropa metoden OpenConnectionForKey

**Metoden ShardMap. OpenConnectionForKey** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkey), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey)) returnerar en anslutning som är klar för att utfärda kommandon till lämplig databas baserat på värdet för **nyckel** parametern. Shard-information cachelagras i programmet av **ShardMapManager**, så dessa begär Anden brukar inte inkludera en databas sökning mot den **globala Shard Map** -databasen.

```Java
// Syntax:
public Connection openConnectionForKey(Object key, String connectionString, ConnectionOptions options)
```

```csharp
// Syntax:
public SqlConnection OpenConnectionForKey<TKey>(TKey key, string connectionString, ConnectionOptions options)
```

* **Nyckel** parametern används som en uppslags nyckel i Shard-kartan för att fastställa lämplig databas för begäran.
* **ConnectionString** används för att skicka endast användarautentiseringsuppgifter för önskad anslutning. Inget databas namn eller server namn ingår i den här *ConnectionString* eftersom metoden bestämmer databasen och servern med hjälp av **ShardMap**.
* **ConnectionOptions** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.connectionoptions), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.connectionoptions)) måste anges till **connectionOptions. kontrol lera** om en miljö där Shard Maps kan ändras och rader kan flyttas till andra databaser som ett resultat av Split eller sammanslagnings åtgärder. Den här verifieringen innebär en kort fråga till den lokala Shard-kartan på mål databasen (inte till den globala Shard-mappningen) innan anslutningen skickas till programmet.

Om valideringen av den lokala Shard-kartan Miss lyckas (vilket indikerar att cacheminnet är felaktigt) frågar Shard Map Manager den globala Shard-mappningen för att hämta det nya korrekta värdet för sökningen, uppdaterar cachen och hämtar och returnerar lämplig databas anslutning.

Använd **connectionOptions. None** när Shard mappnings ändringar inte förväntas när ett program är online. I så fall kan de cachelagrade värdena alltid vara korrekta och det extra verifierings anropet för tur och retur till mål databasen kan hoppas över på ett säkert sätt. Detta minskar databas trafiken. **ConnectionOptions** kan också ställas in via ett värde i en konfigurations fil för att indikera om horisontell partitionering-ändringar förväntas eller inte under en viss tids period.  

I det här exemplet används värdet för en heltals nyckel som **CustomerID** med hjälp av ett **ShardMap** -objekt med namnet **customerShardMap**.  

```Java
int customerId = 12345;
int productId = 4321;
// Looks up the key in the shard map and opens a connection to the shard
try (Connection conn = shardMap.openConnectionForKey(customerId, Configuration.getCredentialsConnectionString())) {
    // Create a simple command that will insert or update the customer information
    PreparedStatement ps = conn.prepareStatement("UPDATE Sales.Customer SET PersonID = ? WHERE CustomerID = ?");

    ps.setInt(1, productId);
    ps.setInt(2, customerId);
    ps.executeUpdate();
} catch (SQLException e) {
    e.printStackTrace();
}
```

```csharp
int customerId = 12345;
int newPersonId = 4321;

// Connect to the shard for that customer ID. No need to call a SqlConnection
// constructor followed by the Open method.
using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId, Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate))
{
    // Execute a simple command.
    SqlCommand cmd = conn.CreateCommand();
    cmd.CommandText = @"UPDATE Sales.Customer
                        SET PersonID = @newPersonID WHERE CustomerID = @customerID";

    cmd.Parameters.AddWithValue("@customerID", customerId);cmd.Parameters.AddWithValue("@newPersonID", newPersonId);
    cmd.ExecuteNonQuery();
}  
```

Metoden **OpenConnectionForKey** returnerar en ny anslutning som redan är öppen till rätt databas. Anslutningar som används på det här sättet kommer fortfarande att dra full nytta av anslutningspoolen.

**OpenConnectionForKeyAsync-metoden** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkeyasync), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkeyasync)) är också tillgänglig om programmet använder asynkron programmering.

## <a name="integrating-with-transient-fault-handling"></a>Integrera med tillfälligt fel hantering

En bra idé att utveckla data åtkomst program i molnet är att se till att tillfälliga fel fångas av appen och att åtgärderna provas flera gånger innan ett fel utlöses. Tillfälligt fel hantering för moln program diskuteras vid hantering av tillfälliga fel ([Java](/java/api/com.microsoft.azure.elasticdb.core.commons.transientfaulthandling), [.net](/previous-versions/msp-n-p/dn440719(v=pandp.60))).

Hantering av tillfälliga fel kan fungera naturligt med mönster för Data-Dependent routning. Nyckel kravet är att försöka köra hela data åtkomst förfrågan på nytt, inklusive det **använda** block som hämtade den databaserade routningslänken. Föregående exempel kunde skrivas om på följande sätt.

### <a name="example---data-dependent-routing-with-transient-fault-handling"></a>Exempel – data beroende routning med tillfälligt fel hantering

```Java
int customerId = 12345;
int productId = 4321;
try {
    SqlDatabaseUtils.getSqlRetryPolicy().executeAction(() -> {
        // Looks up the key in the shard map and opens a connection to the shard
        try (Connection conn = shardMap.openConnectionForKey(customerId, Configuration.getCredentialsConnectionString())) {
            // Create a simple command that will insert or update the customer information
            PreparedStatement ps = conn.prepareStatement("UPDATE Sales.Customer SET PersonID = ? WHERE CustomerID = ?");

            ps.setInt(1, productId);
            ps.setInt(2, customerId);
            ps.executeUpdate();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    });
} catch (Exception e) {
    throw new StoreException(e.getMessage(), e);
}
```

```csharp
int customerId = 12345;
int newPersonId = 4321;

Configuration.SqlRetryPolicy.ExecuteAction(() -> {

    // Connect to the shard for a customer ID.
    using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId, Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate))
    {
        // Execute a simple command
        SqlCommand cmd = conn.CreateCommand();

        cmd.CommandText = @"UPDATE Sales.Customer
                            SET PersonID = @newPersonID
                            WHERE CustomerID = @customerID";

        cmd.Parameters.AddWithValue("@customerID", customerId);
        cmd.Parameters.AddWithValue("@newPersonID", newPersonId);
        cmd.ExecuteNonQuery();

        Console.WriteLine("Update completed");
    }
});
```

Paket som behövs för att implementera tillfälligt fel hantering hämtas automatiskt när du skapar ett exempel program för elastisk databas.

## <a name="transactional-consistency"></a>Transaktionell konsekvens

Transaktions egenskaper garanteras för alla åtgärder lokalt i en Shard. Till exempel körs transaktioner som skickas via data beroende routning inom omfånget för mål-Shard för anslutningen. För närvarande finns det inga funktioner för att ange flera anslutningar i en transaktion, och därför finns det inga transaktions garantier för åtgärder som utförs i Shards.

## <a name="next-steps"></a>Nästa steg

Om du vill koppla från en Shard, eller om du vill återansluta en Shard, se [använda RecoveryManager-klassen för att åtgärda Shard mappnings problem](elastic-database-recovery-manager.md).

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]