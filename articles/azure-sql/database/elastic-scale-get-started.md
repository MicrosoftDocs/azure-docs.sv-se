---
title: Kom igång med Elastic Database verktyg
description: Grundläggande förklaring av Elastic Database tools-funktionen i Azure SQL Database, inklusive en exempel-app som är lätt att köra.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: anumjs
ms.author: anjangsh
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 74343b2f05bb4a59e475449c87524ff66cdd605d
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98919552"
---
# <a name="get-started-with-elastic-database-tools"></a>Kom igång med Elastic Database verktyg
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

I det här dokumentet får du en introduktion till utvecklaren för [klient biblioteket för Elastic Database](elastic-database-client-library.md) genom att hjälpa dig att köra en exempel App. Exempel appen skapar ett enkelt shardade-program och utforskar viktiga funktioner i Elastic Database tools-funktionen i Azure SQL Database. Den fokuserar på användnings fall för [Shard Map Management](elastic-scale-shard-map-management.md), [data beroende routning](elastic-scale-data-dependent-routing.md)och [multi-Shard-frågor](elastic-scale-multishard-querying.md). Klient biblioteket är tillgängligt för .NET och Java.

## <a name="elastic-database-tools-for-java"></a>Elastic Database verktyg för Java

### <a name="prerequisites"></a>Förutsättningar

* Java Developer Kit (JDK), version 1,8 eller senare
* [Maven](https://maven.apache.org/download.cgi)
* SQL Database eller en lokal SQL Server instans

### <a name="download-and-run-the-sample-app"></a>Hämta och kör exempel appen

Gör följande för att skapa JAR-filerna och komma igång med exempelprojektet:

1. Klona [GitHub-lagringsplatsen](https://github.com/Microsoft/elastic-db-tools-for-java) som innehåller klient biblioteket, tillsammans med exempel appen.

2. Redigera _./Sample/src/main/Resources/Resource.Properties_ -filen för att ange följande:
    * TEST_CONN_USER
    * TEST_CONN_PASSWORD
    * TEST_CONN_SERVER_NAME

3. Om du vill skapa exempelprojektet i katalogen _./Sample_ kör du följande kommando:

    ```
    mvn install
    ```

4. Starta exempelprojektet genom att köra följande kommando i _/Sample_ -katalogen:

    ```
    mvn -q exec:java "-Dexec.mainClass=com.microsoft.azure.elasticdb.samples.elasticscalestarterkit.Program"
    ```

5. Om du vill veta mer om klient biblioteks funktionerna kan du experimentera med de olika alternativen. Titta gärna på koden för att lära dig mer om exempel på implementering av appen.

    ![Förlopp – Java][5]

Grattis! Du har skapat och kört ditt första shardade-program genom att använda Elastic Database verktyg på Azure SQL Database. Använd Visual Studio eller SQL Server Management Studio för att ansluta till databasen och ta en titt på Shards som exemplet skapade. Du kommer att märka nya exempel på Shard-databaser och en Shard Map Manager-databas som exemplet har skapat.

Om du vill lägga till klient biblioteket i ditt eget Maven-projekt lägger du till följande beroende i din POM-fil:

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>elastic-db-tools</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="elastic-database-tools-for-net"></a>Elastic Database verktyg för .NET

### <a name="prerequisites"></a>Förutsättningar

* Visual Studio 2012 eller senare med C#. Ladda ned en kostnads fri version vid [hämtning av Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
* NuGet 2,7 eller senare. För att hämta den senaste versionen, se [Installera NuGet](https://docs.nuget.org/docs/start-here/installing-nuget).

### <a name="download-and-run-the-sample-app"></a>Hämta och kör exempel appen

Om du vill installera biblioteket går du till [Microsoft. Azure. SqlDatabase. ElasticScale. client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Biblioteket installeras med exempel appen som beskrivs i följande avsnitt.

Följ dessa steg om du vill hämta och köra exemplet:

1. Hämta de [elastiska DB-verktygen för Azure SQL-komma igång-exemplet](https://github.com/Azure/elastic-db-tools). Packa upp exemplet på en plats som du väljer.

2. Om du vill skapa ett projekt öppnar du lösningen *ElasticDatabaseTools. SLN* från den *elastiska-DB-tools-Master* -katalogen. 

3. Ange *ElasticScaleStarterKit* -projektet som start projekt.

4. I *ElasticScaleStarterKit* -projektet öppnar du *App.config* -filen. Följ sedan anvisningarna i filen för att lägga till Server namnet och inloggnings informationen (användar namn och lösen ord).

5. Skapa och kör programmet. När du uppmanas till det aktiverar du Visual Studio för att återställa NuGet-paketen för lösningen. Den här åtgärden hämtar den senaste versionen av klient biblioteket för Elastic Database från NuGet.

6. Om du vill veta mer om klient biblioteks funktionerna kan du experimentera med de olika alternativen. Observera de steg som programmet tar i konsolens utdata och Känn dig kostnads fritt för att utforska koden bakom kulisserna.

   ![Förlopp][4]

Grattis! Du har skapat och kört ditt första shardade-program genom att använda Elastic Database verktyg på SQL Database. Använd Visual Studio eller SQL Server Management Studio för att ansluta till databasen och ta en titt på Shards som exemplet skapade. Du kommer att märka nya exempel på Shard-databaser och en Shard Map Manager-databas som exemplet har skapat.

> [!IMPORTANT]
> Vi rekommenderar att du alltid använder den senaste versionen av Management Studio så att du förblir synkroniserad med uppdateringar till Azure och SQL Database. [Uppdatera SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).

## <a name="key-pieces-of-the-code-sample"></a>Huvud delar av kod exemplet

* **Hantera Shards-och Shard Maps**: koden visar hur du arbetar med Shards, intervall och mappningar i *ShardManagementUtils.cs* -filen. Mer information finns i [skala ut databaser med Shard Map Manager](https://go.microsoft.com/?linkid=9862595).  

* **Data beroende routning**: routning av transaktioner till rätt Shard visas i *DataDependentRoutingSample.cs* -filen. Mer information finns i [data beroende routning](https://go.microsoft.com/?linkid=9862596).

* **Fråga över flera Shards**: frågor över Shards illustreras i *MultiShardQuerySample.cs* -filen. Mer information finns i [multi-Shard-frågor](https://go.microsoft.com/?linkid=9862597).

* **Lägger till Tom Shards**: upprepnings tillägg av ny tom Shards utförs av koden i *CreateShardSample.cs* -filen. Mer information finns i [skala ut databaser med Shard Map Manager](https://go.microsoft.com/?linkid=9862595).

## <a name="other-elastic-scale-operations"></a>Andra elastiska skalnings åtgärder

* Dela **en befintlig Shard**: funktionen för att dela Shards tillhandahålls av verktyget Dela och slå samman. Mer information finns i [Flytta data mellan utskalade moln databaser](elastic-scale-overview-split-and-merge.md).

* **Sammanfogar befintliga Shards**: Shard-sammanslagningar utförs också med hjälp av verktyget för att dela och slå samman. Mer information finns i [Flytta data mellan utskalade moln databaser](elastic-scale-overview-split-and-merge.md).

## <a name="cost"></a>Cost

Elastic Database tools-biblioteket är kostnads fritt. När du använder Elastic Database verktyg debiteras du inga ytterligare avgifter utöver kostnaden för din Azure-användning.

Exempel programmet skapar till exempel nya databaser. Kostnaden för den här funktionen beror på vilken SQL Database-utgåva du väljer och Azure-användningen av ditt program.

Information om priser finns i [SQL Database pris information](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Nästa steg

Mer information om Elastic Database-verktyg finns i följande artiklar:

* Kod exempel:
  * Elastic Database verktyg ([.net](https://github.com/Azure/elastic-db-tools), [Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22))
  * [Elastic Database verktyg för Azure SQL-Entity Framework-integrering](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
  * [Shard Elastiskhet i Script Center](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
* Blogg: [meddelande om elastisk skalning](https://azure.microsoft.com/blog/20../../introducing-elastic-scale-preview-for-azure-sql-database/)
* Kanal 9: [Översikt över elastisk skalning](https://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale)
* Diskussions forum: [Microsoft Q&en fråge sida för Azure SQL Database](/answers/topics/azure-sql-database.html)
* För att mäta prestanda: [prestanda räknare för Shard Map Manager](elastic-database-client-library.md)

<!--Anchors-->
[The Elastic Scale Sample Application]: #The-Elastic-Scale-Sample-Application
[Download and Run the Sample App]: #Download-and-Run-the-Sample-App
[Cost]: #Cost
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/elastic-scale-get-started/newProject.png
[2]: ./media/elastic-scale-get-started/click-online.png
[3]: ./media/elastic-scale-get-started/click-CSharp.png
[4]: ./media/elastic-scale-get-started/output2.png
[5]: ./media/elastic-scale-get-started/java-client-library.PNG