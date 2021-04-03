---
title: Recovery Manager att åtgärda problem med Shard Map
description: Använd klassen RecoveryManager för att lösa problem med Shard Maps
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: 91bcd998849c619a328a198c97bb8c977b9d8232
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92792233"
---
# <a name="using-the-recoverymanager-class-to-fix-shard-map-problems"></a>Korrigera shard-kartproblem med RecoveryManager-klassen
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[RecoveryManager](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager) -klassen ger ADO.NET-program möjlighet att enkelt upptäcka och korrigera eventuella inkonsekvenser mellan den globala Shard-mappningen (GSM) och den lokala Shard Map (LSM) i en shardade databas miljö.

GSM-och LSM spårar mappningen av varje databas i en shardade-miljö. Ibland sker en rast mellan GSM-och LSM. I så fall använder du klassen RecoveryManager för att identifiera och reparera rasten.

RecoveryManager-klassen är en del av [Elastic Database-klient biblioteket](elastic-database-client-library.md).

![Shard-karta][1]

För term definitioner, se [Elastic Database tools-ordlista](elastic-scale-glossary.md). Mer information om hur **ShardMapManager** används för att hantera data i en shardade-lösning finns i [Shard Map Management](elastic-scale-shard-map-management.md).

## <a name="why-use-the-recovery-manager"></a>Varför använda återställnings hanteraren

I en shardade Database-miljö finns det en klient per databas och många databaser per server. Det kan också finnas många servrar i miljön. Varje databas mappas i Shard-kartan så anrop kan dirigeras till rätt server och databas. Databaser spåras enligt en horisontell partitionering- **nyckel** och varje Shard tilldelas ett **intervall med nyckel värden**. Till exempel kan en horisontell partitionering-nyckel representera kundens namn från "D" till "F". Mappningen av alla Shards (även kallat databaser) och deras mappnings intervall finns i **Global Shard Map (GSM)**. Varje databas innehåller också en karta över de intervall som finns på den Shard som kallas för den **lokala Shard-kartan (LSM)**. När en app ansluter till en Shard cachelagras mappningen med appen för snabb hämtning. LSM används för att validera cachelagrade data.

GSM-och LSM kan bli osynkroniserade av följande orsaker:

1. Borttagning av en Shard vars intervall förväntas användas eller byter namn på en Shard. Om du tar bort ett Shard resulterar det i en **överbliven Shard-mappning**. På samma sätt kan en omdöpt databas orsaka en överblivna Shard-mappning. Beroende på syftet med ändringen kan Shard behöva tas bort eller så måste Shard-platsen uppdateras. Information om hur du återställer en borttagen databas finns i [återställa en borttagen](recovery-using-backups.md)databas.
2. En händelse för GEO-redundans inträffar. Om du vill fortsätta måste en uppdatera Server namnet och databas namnet för Shard Map Manager i programmet och sedan uppdatera Shard-mappnings informationen för alla Shards i en Shard-karta. Om det finns en geo-redundansväxling bör sådan återställnings logik automatiseras inom arbets flödet för redundansväxling. Automatisering av återställnings åtgärder möjliggör en friktions fri hanterbarhet för geo-aktiverade databaser och förhindrar manuella mänsklig åtgärder. Om du vill veta mer om alternativ för att återställa en databas om det uppstår avbrott i data centret kan du läsa mer i [verksamhets kontinuitet](business-continuity-high-availability-disaster-recover-hadr-overview.md) och [haveri beredskap](disaster-recovery-guidance.md).
3. Antingen en Shard eller ShardMapManager-databasen återställs till en tidigare tidpunkt. Om du vill veta mer om tidpunkts återställning med hjälp av säkerhets kopior, se [återställning med säkerhets kopiering](recovery-using-backups.md).

Mer information om Azure SQL Database Elastic Database verktyg, geo-replikering och återställning finns i följande avsnitt:

* [Översikt: affärs kontinuitet i molnet och haveri beredskap för databasen med SQL Database](business-continuity-high-availability-disaster-recover-hadr-overview.md)
* [Kom igång med elastiska databas verktyg](elastic-scale-get-started.md)  
* [Hantering av ShardMap](elastic-scale-shard-map-management.md)

## <a name="retrieving-recoverymanager-from-a-shardmapmanager"></a>Hämta RecoveryManager från en ShardMapManager

Det första steget är att skapa en RecoveryManager-instans. [GetRecoveryManager-metoden](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getrecoverymanager) returnerar återställnings hanteraren för den aktuella [ShardMapManager](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) -instansen. Om du vill åtgärda eventuella inkonsekvenser i Shard-kartan måste du först hämta RecoveryManager för den specifika Shard-kartan.

   ```java
    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnectionString,  
             ShardMapManagerLoadPolicy.Lazy);
             RecoveryManager rm = smm.GetRecoveryManager();
   ```

I det här exemplet initieras RecoveryManager från ShardMapManager. ShardMapManager som innehåller en ShardMap har också redan initierats.

Eftersom den här program koden ändrar själva Shard-kartan bör autentiseringsuppgifterna som används i fabriks metoden (i föregående exempel smmConnectionString) vara autentiseringsuppgifter som har Läs-och Skriv behörighet för den GSM-databas som anslutnings strängen refererar till. Dessa autentiseringsuppgifter skiljer sig vanligt vis från autentiseringsuppgifter som används för att öppna anslutningar för data beroende routning. Mer information finns i [använda autentiseringsuppgifter i den elastiska databas klienten](elastic-scale-manage-credentials.md).

## <a name="removing-a-shard-from-the-shardmap-after-a-shard-is-deleted"></a>Ta bort en Shard från ShardMap efter att en Shard har tagits bort

[Metoden DetachShard](/previous-versions/azure/dn842083(v=azure.100)) kopplar från den aktuella Shard från Shard-mappningen och tar bort mappningar som är associerade med Shard.  

* Parametern Location är Shard-platsen, särskilt Server namnet och databas namnet, för Shard som kopplas från.
* Parametern shardMapName är namnet på Shard-kartan. Detta krävs endast om flera Shard Maps hanteras av samma Shard Map Manager. Valfritt.

> [!IMPORTANT]
> Använd endast den här tekniken om du är säker på att intervallet för den uppdaterade mappningen är tomt. Metoderna ovan kontrollerar inte data för det område som flyttas, så det är bäst att inkludera kontroller i koden.

I det här exemplet tas Shards bort från Shard-kartan.

   ```java
   rm.DetachShard(s.Location, customerMap);
   ```

Shard-kartan visar Shard-platsen i GSM innan du tar bort Shard. Eftersom Shard har tagits bort antas detta vara avsiktligt, och horisontell partitionering nyckel intervallet används inte längre. Om inte, kan du köra återställning av tidpunkter. för att återställa Shard från en tidigare tidpunkt. (I så fall kan du läsa följande avsnitt för att identifiera Shard inkonsekvenser.) För att återställa, se tidpunkt [för återställning](recovery-using-backups.md).

Eftersom det antas att borttagningen av databasen var avsiktlig, är den slutliga administrativa Rensnings åtgärden att ta bort posten till Shard i Shard Map Manager. Detta förhindrar att programmet oavsiktligt skriver information till ett intervall som inte förväntas.

## <a name="to-detect-mapping-differences"></a>Identifiera mappnings skillnader

[Metoden DetectMappingDifferences](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.detectmappingdifferences) väljer och returnerar en av Shard Maps (antingen lokal eller global) som källa för sanningen och synkroniserar mappningar i båda Shard Maps (GSM och lsm).

   ```java
   rm.DetectMappingDifferences(location, shardMapName);
   ```

* *Platsen* anger Server namnet och databas namnet.
* Parametern *shardMapName* är namnet på Shard-kartan. Detta krävs endast om flera Shard Maps hanteras av samma Shard Map Manager. Valfritt.

## <a name="to-resolve-mapping-differences"></a>Lösa mappnings skillnader

[Metoden ResolveMappingDifferences](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.resolvemappingdifferences) väljer en av Shard Maps (antingen lokal eller global) som källa för sanningen och synkroniserar mappningar i båda Shard Maps (GSM och lsm).

   ```java
   ResolveMappingDifferences (RecoveryToken, MappingDifferenceResolution.KeepShardMapping);
   ```

* Parametern *RecoveryToken* räknar upp skillnaderna i mappningarna mellan GSM och lsm för den speciella Shard.
* [MappingDifferenceResolution-uppräkningen](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.mappingdifferenceresolution) används för att ange metoden för att lösa skillnaden mellan Shard-mappningarna.
* **MappingDifferenceResolution. KeepShardMapping** rekommenderas att när lsm innehåller korrekt mappning och därför ska mappningen i Shard användas. Detta är vanligt vis fallet om det finns en redundansväxling: Shard finns nu på en ny server. Eftersom Shard måste först tas bort från GSM (med hjälp av metoden RecoveryManager. DetachShard), finns det inte längre någon mappning i GSM. Därför måste LSM användas för att återupprätta Shard-mappningen.

## <a name="attach-a-shard-to-the-shardmap-after-a-shard-is-restored"></a>Koppla en Shard till ShardMap efter att en Shard har återställts

[Metoden AttachShard](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.attachshard) bifogar den aktuella Shard till Shard-kartan. Den identifierar sedan alla Shard Map-inkonsekvenser och uppdaterar mappningarna för att matcha Shard vid tidpunkten för återställningen av Shard. Det förutsätts att databasen också har fått ett nytt namn för att återspegla det ursprungliga databas namnet (innan Shard återställdes), eftersom återställnings tiden för tidpunkten återställs som standard till en ny databas som lagts till med tidsstämpeln.

   ```java
   rm.AttachShard(location, shardMapName)
   ```

* *Plats* parametern är Server namnet och databas namnet för den Shard som bifogas.
* Parametern *shardMapName* är namnet på Shard-kartan. Detta krävs endast om flera Shard Maps hanteras av samma Shard Map Manager. Valfritt.

Det här exemplet lägger till en Shard till Shard-kartan som nyligen har återställts från en tidigare tidpunkt. Eftersom Shard (dvs mappningen för Shard i LSM) har återställts, kan den vara inkonsekvent med Shard-posten i GSM. Utanför den här exempel koden återställdes Shard och bytt namn till databasens ursprungliga namn. Eftersom den återställdes antas det att mappningen i LSM är den betrodda mappningen.

   ```java
   rm.AttachShard(s.Location, customerMap);
   var gs = rm.DetectMappingDifferences(s.Location);
      foreach (RecoveryToken g in gs)
       {
       rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping);
       }
   ```

## <a name="updating-shard-locations-after-a-geo-failover-restore-of-the-shards"></a>Uppdatera Shard-platser efter en GEO-redundans (återställning) av Shards

Om det finns en geo-redundansväxling, blir den sekundära databasen skrivbar och blir den nya primära databasen. Namnet på servern och eventuellt databasen (beroende på din konfiguration) kan skilja sig från den ursprungliga primära servern. Därför måste mappnings posterna för Shard i GSM-och LSM åtgärdas. På samma sätt kan detta orsaka inkonsekvenser i Shard Maps om databasen återställs till ett annat namn eller en annan plats, eller till en tidigare tidpunkt. Shard Map Manager hanterar distributionen av öppna anslutningar till rätt databas. Fördelningen baseras på data i Shard-kartan och värdet för horisontell partitionering-nyckeln som är målet för programbegäran. Efter en geo-redundansväxling måste den här informationen uppdateras med rätt server namn, databas namn och Shard mappning för den återställda databasen.

## <a name="best-practices"></a>Bästa praxis

GEO-redundans och återställning hanteras vanligt vis av en moln administratör i programmet som avsiktligen använder Azure SQL Database funktioner för affärs kontinuitet. Planering av affärs kontinuitet kräver processer, procedurer och åtgärder för att säkerställa att affärs åtgärder kan fortsätta utan avbrott. Metoderna som är tillgängliga som en del av RecoveryManager-klassen ska användas i det här arbets flödet för att se till att GSM-och LSM hålls uppdaterade baserat på den återställnings åtgärd som vidtagits. Det finns fem grundläggande steg för att säkerställa att GSM-och LSM motsvarar korrekt information efter en redundansväxling. Program koden för att utföra dessa steg kan integreras i befintliga verktyg och arbets flöden.

1. Hämta RecoveryManager från ShardMapManager.
2. Koppla från den gamla Shard från Shard-kartan.
3. Koppla den nya Shard till Shard-kartan, inklusive den nya Shard-platsen.
4. Identifiera inkonsekvenser i mappningen mellan GSM-och LSM.
5. Lös skillnaderna mellan GSM-och LSM, och lita på LSM.

Det här exemplet utför följande steg:

1. Tar bort Shards från Shard-kartan som återspeglar Shard platser före redundansväxlingen.
2. Bifogar Shards till Shard-kartan som återspeglar de nya Shard-platserna (parametern "Configuration. SecondaryServer" är det nya Server namnet men samma databas namn).
3. Hämtar återställnings-token genom att identifiera mappnings skillnader mellan GSM-och LSM för varje Shard.
4. Löser inkonsekvenser genom att lita på mappningen från LSM för varje Shard.

   ```java
   var shards = smm.GetShards();
   foreach (shard s in shards)
   {
     if (s.Location.Server == Configuration.PrimaryServer)
         {
          ShardLocation slNew = new ShardLocation(Configuration.SecondaryServer, s.Location.Database);
          rm.DetachShard(s.Location);
          rm.AttachShard(slNew);
          var gs = rm.DetectMappingDifferences(slNew);
          foreach (RecoveryToken g in gs)
            {
               rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping);
            }
        }
    }
   ```

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/elastic-database-recovery-manager/recovery-manager.png