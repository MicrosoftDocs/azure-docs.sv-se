---
title: Skala ut en databas
description: Använda klient biblioteket ShardMapManager, Elastic Database
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 03bf92a2d77fb262ed6506bf18c0d27006e435a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103201184"
---
# <a name="scale-out-databases-with-the-shard-map-manager"></a>Skala ut databaser med mappnings hanteraren för Shard
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Använd Shard Map Manager för att enkelt skala ut databaser på Azure SQL Database. Shard Map Manager är en särskild databas som upprätthåller global mappnings information om alla Shards (databaser) i en Shard-uppsättning. Metadata gör att ett program kan ansluta till rätt databas baserat på värdet för **horisontell partitionering-nyckeln**. Dessutom innehåller alla Shard i uppsättningen kartor som spårar de lokala Shard-data (kallas **shardletar**).

![Karthantering för shard](./media/elastic-scale-shard-map-management/glossary.png)

Det är viktigt att förstå hur dessa kartor är konstruerade för att Shard kart hantering. Detta görs med ShardMapManager-klassen ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)), som finns i [Elastic Database klient biblioteket](elastic-database-client-library.md) för att hantera Shard Maps.  

## <a name="shard-maps-and-shard-mappings"></a>Shard Maps och Shard-mappningar

För varje Shard måste du välja vilken typ av Shard-mappning som ska skapas. Valet beror på databas arkitekturen:

1. Enskild klient per databas  
2. Flera klienter per databas (två typer):
   1. List mappning
   2. Intervall mappning

För en modell med en enda klient organisation skapar du en Shard-karta för **list mappning** . Modellen med en enskild klient tilldelas en databas per klient. Det här är en effektiv modell för SaaS-utvecklare som fören klar hanteringen av Shard-kartor.

![List mappning][1]

Modellen för flera innehavare tilldelar flera klienter till en enskild databas (och du kan distribuera grupper av klienter i flera databaser). Använd den här modellen när du förväntar dig att varje klient ska ha små data behov. I den här modellen tilldelar du ett antal klienter till en databas med hjälp av **intervall mappning**.

![Intervall mappning][2]

Eller så kan du implementera en databas modell med flera klienter med en *list mappning* för att tilldela flera klienter till en enskild databas. Till exempel används DB1 för att lagra information om klient-ID 1 och 5, och DB2 lagrar data för klient 7 och klient 10.

![Flera klienter i en enskild databas][3]

### <a name="supported-types-for-sharding-keys"></a>Typer som stöds för horisontell partitionering-nycklar

Elastisk skalning stöder följande typer som horisontell partitionering-nycklar:

| .NET | Java |
| --- | --- |
| heltal |heltal |
| long |long |
| guid |uuid |
| byte[]  |byte[] |
| datetime | timestamp |
| tidsintervall | varaktighet|
| datetimeoffset |offsetdatetime |

### <a name="list-and-range-shard-maps"></a>List-och intervall Shard Maps

Shard Maps kan konstrueras med hjälp **av listor med enskilda horisontell partitionering-nyckel värden**, eller så kan de konstrueras med hjälp av **intervall med horisontell partitionering-nyckel värden**.

### <a name="list-shard-maps"></a>Visa lista Shard Maps

**Shards** innehåller **shardletar** och mappningen av shardletar till Shards underhålls av en Shard-karta. En **list Shard-karta** är en associering mellan de enskilda nyckel värden som identifierar shardletar och databaserna som fungerar som Shards.  **List mappningar** är explicita och olika nyckel värden kan mappas till samma databas. Till exempel, nyckel värde 1 mappar till databas A, och nyckel värden 3 och 6 mappas båda till databas B.

| Nyckel | Shard-plats |
| --- | --- |
| 1 |Database_A |
| 3 |Database_B |
| 4 |Database_C |
| 6 |Database_B |
| ... |... |

### <a name="range-shard-maps"></a>Intervall Shard Maps

I en **Range Shard-karta** beskrivs nyckel intervallet av ett par **[lågt värde, högt värde)** där det *låga värdet* är den minsta nyckeln i intervallet, och det *höga värdet* är det första värdet som är högre än intervallet.

Till exempel innehåller **[0, 100)** alla heltal som är större än eller lika med 0 och mindre än 100. Observera att flera intervall kan peka på samma databas och åtskilda intervall stöds (till exempel [100 200) och [400 600) pekar båda på databas C i följande exempel.)

| Nyckel | Shard-plats |
| --- | --- |
| [1, 50) |Database_A |
| [50 100) |Database_B |
| [100 200) |Database_C |
| [400 600) |Database_C |
| ... |... |

Var och en av de tabeller som visas ovan är ett konceptuellt exempel på ett **ShardMap** -objekt. Varje rad är ett förenklat exempel på en enskild **PointMapping** (för List Shard Map) eller **RangeMapping** (för Range Shard Map)-objektet.

## <a name="shard-map-manager"></a>Karthanterare för shard

I klient biblioteket är Shard Map Manager en samling Shard Maps. De data som hanteras av en **ShardMapManager** -instans lagras på tre platser:

1. **GSM (global Shard Map)**: du anger en databas som ska användas som lagrings plats för alla dess Shard-kartor och mappningar. Särskilda tabeller och lagrade procedurer skapas automatiskt för att hantera informationen. Detta är vanligt vis en liten databas och lätt att komma åt och bör inte användas för andra programs behov. Tabellerna är i ett särskilt schema med namnet **__ShardManagement**.
2. **Lokal Shard Map (LSM)**: varje databas som du anger som en Shard ändras till att innehålla flera små tabeller och särskilda lagrade procedurer som innehåller och hanterar Shard kart information som är specifik för den Shard. Den här informationen är överflödig med informationen i GSM och gör det möjligt för programmet att verifiera cachelagrade Shard för kart information utan att placera någon belastning på GSMen. programmet använder LSM för att avgöra om en cachelagrad mappning fortfarande är giltig. Tabellerna som motsvarar LSM på varje Shard finns också i schemat **__ShardManagement**.
3. **Programcache**: varje program instans som använder ett **ShardMapManager** -objekt har en lokal minnes intern cache med mappningarna. Den innehåller routningsinformation som nyligen har hämtats.

## <a name="constructing-a-shardmapmanager"></a>Skapa en ShardMapManager

Ett **ShardMapManager** -objekt konstrueras med ett fabriks mönster ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory)). Metoden **ShardMapManagerFactory. GetSqlShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)) använder autentiseringsuppgifter (inklusive Server namnet och databas namnet som innehåller GSM) i form av en **ConnectionString** och returnerar en instans av en **ShardMapManager**.  

**Observera:** **ShardMapManager** ska endast instansieras en gång per app-domän, inom initierings koden för ett program. Skapandet av ytterligare instanser av ShardMapManager i samma app-domän resulterar i ökad minnes-och CPU-användning av programmet. En **ShardMapManager** kan innehålla valfritt antal Shard Maps. Även om en enda Shard-karta kan vara tillräcklig för många program, finns det tillfällen då olika uppsättningar databaser används för olika scheman eller för unika syfte. i dessa fall kan flera Shard Maps vara bättre.

I den här koden försöker ett program öppna en befintlig **ShardMapManager** med TryGetSqlShardMapManager ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.trygetsqlshardmapmanager), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) -metod. Om objekt som representerar en global **ShardMapManager** (GSM) ännu inte finns i databasen, skapar klient biblioteket dem med hjälp av metoden CreateSqlShardMapManager ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.createsqlshardmapmanager), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager)).

```Java
// Try to get a reference to the Shard Map Manager in the shardMapManager database.
// If it doesn't already exist, then create it.
ShardMapManager shardMapManager = null;
boolean shardMapManagerExists = ShardMapManagerFactory.tryGetSqlShardMapManager(shardMapManagerConnectionString,ShardMapManagerLoadPolicy.Lazy, refShardMapManager);
shardMapManager = refShardMapManager.argValue;

if (shardMapManagerExists) {
    ConsoleUtils.writeInfo("Shard Map %s already exists", shardMapManager);
}
else {
    // The Shard Map Manager does not exist, so create it
    shardMapManager = ShardMapManagerFactory.createSqlShardMapManager(shardMapManagerConnectionString);
    ConsoleUtils.writeInfo("Created Shard Map %s", shardMapManager);
}
```

```csharp
// Try to get a reference to the Shard Map Manager via the Shard Map Manager database.  
// If it doesn't already exist, then create it.
ShardMapManager shardMapManager;
bool shardMapManagerExists = ShardMapManagerFactory.TryGetSqlShardMapManager(
                                        connectionString,
                                        ShardMapManagerLoadPolicy.Lazy,
                                        out shardMapManager);

if (shardMapManagerExists)
{
    Console.WriteLine("Shard Map Manager already exists");
}
else
{
    // Create the Shard Map Manager.
    ShardMapManagerFactory.CreateSqlShardMapManager(connectionString);
    Console.WriteLine("Created SqlShardMapManager");

    shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(
            connectionString,
            ShardMapManagerLoadPolicy.Lazy);

// The connectionString contains server name, database name, and admin credentials for privileges on both the GSM and the shards themselves.
}
```

För .NET-versionen kan du använda PowerShell för att skapa en ny Shard Map Manager. Ett exempel finns [här](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

## <a name="get-a-rangeshardmap-or-listshardmap"></a>Hämta en RangeShardMap eller ListShardMap

När du har skapat en Shard Map Manager kan du hämta RangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) eller ListShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)) med TryGetRangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetrangeshardmap), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap)), TryGetListShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetlistshardmap), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap)) eller GetShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getshardmap) [, .net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap)).

```Java
// Creates a new Range Shard Map with the specified name, or gets the Range Shard Map if it already exists.
static <T> RangeShardMap<T> createOrGetRangeShardMap(ShardMapManager shardMapManager,
            String shardMapName,
            ShardKeyType keyType) {
    // Try to get a reference to the Shard Map.
    ReferenceObjectHelper<RangeShardMap<T>> refRangeShardMap = new ReferenceObjectHelper<>(null);
    boolean isGetSuccess = shardMapManager.tryGetRangeShardMap(shardMapName, keyType, refRangeShardMap);
    RangeShardMap<T> shardMap = refRangeShardMap.argValue;

    if (isGetSuccess && shardMap != null) {
        ConsoleUtils.writeInfo("Shard Map %1$s already exists", shardMap.getName());
    }
    else {
        // The Shard Map does not exist, so create it
        try {
            shardMap = shardMapManager.createRangeShardMap(shardMapName, keyType);
        }
        catch (Exception e) {
            e.printStackTrace();
        }
        ConsoleUtils.writeInfo("Created Shard Map %1$s", shardMap.getName());
    }

    return shardMap;
}
```

```csharp
// Creates a new Range Shard Map with the specified name, or gets the Range Shard Map if it already exists.
public static RangeShardMap<T> CreateOrGetRangeShardMap<T>(ShardMapManager shardMapManager, string shardMapName)
{
    // Try to get a reference to the Shard Map.
    RangeShardMap<T> shardMap;
    bool shardMapExists = shardMapManager.TryGetRangeShardMap(shardMapName, out shardMap);

    if (shardMapExists)
    {
        ConsoleUtils.WriteInfo("Shard Map {0} already exists", shardMap.Name);
    }
    else
    {
        // The Shard Map does not exist, so create it
        shardMap = shardMapManager.CreateRangeShardMap<T>(shardMapName);
        ConsoleUtils.WriteInfo("Created Shard Map {0}", shardMap.Name);
    }

    return shardMap;
}
```

### <a name="shard-map-administration-credentials"></a>Shard mappa autentiseringsuppgifter för administration

Program som administrerar och ändrar Shard Maps skiljer sig från de som använder Shard Maps för att dirigera anslutningar.

Om du vill administrera Shard Maps (Lägg till eller ändra Shards, Shard Maps, Shard mappningar osv.) måste du instansiera **ShardMapManager** med **autentiseringsuppgifter som har Läs-/Skriv behörighet för både GSM-databasen och på varje databas som fungerar som en Shard**. Autentiseringsuppgifterna måste tillåta skrivningar mot tabellerna i både GSM-och LSM som Shard-kart information anges eller ändras, samt för att skapa LSM-tabeller på nya Shards.  

Se [autentiseringsuppgifter som används för att få åtkomst till Elastic Database klient biblioteket](elastic-scale-manage-credentials.md).

### <a name="only-metadata-affected"></a>Endast metadata påverkas

Metoder som används för att fylla i eller ändra **ShardMapManager** -data ändrar inte de användar data som lagras i själva Shards. Till exempel kan metoder som **CreateShard**, **DeleteShard**, **UpdateMapping** osv, påverka endast Shard Map-metadata. De tar inte bort, lägger till eller ändrar användar data som finns i Shards. Dessa metoder är i stället utformade för att användas tillsammans med separata åtgärder som du utför för att skapa eller ta bort faktiska databaser, eller som flyttar rader från en Shard till en annan för att balansera om en shardade-miljö.  ( **Delnings** verktyget som ingår i Elastic Database-verktyg använder dessa API: er tillsammans med att dirigera den faktiska data förflyttningen mellan Shards.) Se [skalning med Elastic Database dela-merge-verktyget](elastic-scale-overview-split-and-merge.md).

## <a name="data-dependent-routing"></a>Databeroende routning

Mappnings hanteraren för Shard används i program som kräver databas anslutningar för att utföra de programaktuella data åtgärderna. Anslutningarna måste vara kopplade till rätt databas. Detta kallas för **data beroende routning**. För dessa program instansierar du ett Shard Map Manager-objekt från fabriken med autentiseringsuppgifter som har skrivskyddad åtkomst till GSM-databasen. Enskilda förfrågningar för senare anslutningar anger autentiseringsuppgifter som krävs för att ansluta till lämplig Shard-databas.

Observera att dessa program (som använder **ShardMapManager** som öppnas med skrivskyddade autentiseringsuppgifter) inte kan göra ändringar i Maps eller mappningar. För dessa behov skapar du administrativa-/regionsspecifika program eller PowerShell-skript som ger högre privilegierade autentiseringsuppgifter enligt beskrivningen ovan. Se [autentiseringsuppgifter som används för att få åtkomst till Elastic Database klient biblioteket](elastic-scale-manage-credentials.md).

Mer information finns i [data beroende routning](elastic-scale-data-dependent-routing.md).

## <a name="modifying-a-shard-map"></a>Ändra en Shard-karta

En Shard-karta kan ändras på olika sätt. Alla följande metoder ändrar de metadata som beskriver Shards och deras mappningar, men de ändrar inte fysiskt data i Shards och skapar eller tar inte bort de faktiska databaserna.  Några av åtgärderna på Shard-kartan som beskrivs nedan kan behöva samordnas med administrativa åtgärder som fysiskt flyttar data eller som lägger till och tar bort databaser som fungerar som Shards.

Dessa metoder fungerar tillsammans som de bygg block som är tillgängliga för att ändra den övergripande distributionen av data i din shardade-databas miljö.  

* För att lägga till eller ta bort Shards: Använd **CreateShard** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.createshard), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard)) och **DeleteShard** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.deleteshard), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.deleteshard)) för klassen shardmap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap)).
  
    Servern och databasen som representerar mål-Shard måste redan finnas för att dessa åtgärder ska kunna köras. Dessa metoder påverkar inte själva databaserna, bara för metadata i Shard-kartan.
* För att skapa eller ta bort punkter eller intervall som är mappade till Shards: Använd **CreateRangeMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.createrangemapping), [.net](/previous-versions/azure/dn841993(v=azure.100))), **DeleteMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.deletemapping), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) för RangeShardMapping-klassen ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) och **CreatePointMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap.createpointmapping), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)) för klassen ListShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap), .net). [](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)
  
    Många olika punkter eller intervall kan mappas till samma Shard. Dessa metoder påverkar endast metadata – de påverkar inte data som redan finns i Shards. Om data behöver tas bort från databasen för att kunna användas konsekvent med **DeleteMapping** -åtgärder, utför du dessa åtgärder separat, men tillsammans med dessa metoder.  
* Om du vill dela upp befintliga intervall i två eller sammanfoga intilliggande områden till ett: Använd **SplitMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.splitmapping), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) och **MergeMappings** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.mergemappings), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)).  
  
    Observera att delnings-och sammanfognings åtgärder **inte ändrar Shard till vilka nyckel värden mappas**. En Split delar upp ett befintligt intervall i två delar, men låter båda vara mappade till samma Shard. En sammanslagning fungerar på två intilliggande intervall som redan har mappats till samma Shard och kombinerar dem till ett enda intervall.  Flytt av punkter eller intervall mellan Shards måste samordnas med hjälp av **UpdateMapping** tillsammans med den faktiska data flytten.  Du kan använda **delnings-/sammanslagnings** tjänsten som är en del av elastiska databas verktyg för att koordinera Shard kart ändringar med data förflyttning, vid behov.
* Om du vill mappa om (eller flytta) enskilda punkter eller intervall till olika Shards: använda **UpdateMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.updatemapping), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)).  
  
    Eftersom data kan behöva flyttas från en Shard till en annan för att kunna användas konsekvent med **UpdateMapping** -åtgärder, måste du utföra den förflyttningen separat men tillsammans med dessa metoder.

* För att göra mappningar online och offline: Använd **MarkMappingOffline** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.markmappingoffline), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) och **MarkMappingOnline** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.markmappingonline), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) för att styra online-tillstånd för en mappning.
  
    Vissa åtgärder för Shard-mappningar tillåts bara när en mappning är i läget "offline", inklusive **UpdateMapping** och **DeleteMapping**. När en mappning är offline returnerar en data beroende begäran som baseras på en nyckel som ingår i mappningen ett fel. När ett intervall försätts i offlineläge stoppas dessutom alla anslutningar till de berörda Shard automatiskt för att förhindra inkonsekventa eller ofullständiga resultat för frågor riktade mot intervall som ändras.

Mappningar är oföränderliga objekt i .NET.  Alla metoder ovan som ändrings mappningar gör det också ogiltig att validera eventuella referenser till dem i din kod. För att göra det enklare att utföra sekvenser av åtgärder som ändrar en mappnings status, returnerar alla metoder som ändrar en mappning en ny mappnings referens, så att åtgärder kan kedjas. Om du till exempel vill ta bort en befintlig mappning i shardmap SM som innehåller nyckeln 25 kan du köra följande:

```
    sm.DeleteMapping(sm.MarkMappingOffline(sm.GetMappingForKey(25)));
```

## <a name="adding-a-shard"></a>Lägga till en Shard

Program behöver ofta lägga till nya Shards för att hantera data som förväntas från nya nycklar eller nyckel intervall, för en Shard-karta som redan finns. Ett program shardade efter klient-ID kan till exempel behöva etablera en ny Shard för en ny klient, annars kan data shardade kräva en ny Shard som etableras innan varje ny månad börjar.

Om det nya intervallet av nyckel värden inte redan är en del av en befintlig mappning och ingen data förflyttning krävs är det enkelt att lägga till den nya Shard och koppla den nya nyckeln eller intervallet till Shard. Mer information om hur du lägger till nya Shards finns i [lägga till en ny Shard](elastic-scale-add-a-shard.md).

För scenarier som kräver data flytt krävs dock verktyget Dela-merge för att dirigera data förflyttningen mellan Shards i kombination med de nödvändiga Shard Map-uppdateringarna. Mer information om hur du använder verktyget för delad sammanslagning finns i [Översikt över delad sammanslagning](elastic-scale-overview-split-and-merge.md)

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/elastic-scale-shard-map-management/listmapping.png
[2]: ./media/elastic-scale-shard-map-management/rangemapping.png
[3]: ./media/elastic-scale-shard-map-management/multipleonsingledb.png
