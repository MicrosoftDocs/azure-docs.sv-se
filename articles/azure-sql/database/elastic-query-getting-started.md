---
title: Rapport över utskalade moln databaser
description: Använd databas frågor mellan databaser för att rapportera över flera databaser.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 10/10/2019
ms.openlocfilehash: 586dad7439cc57ed2c863ee5f6692e12f7a78c50
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92781234"
---
# <a name="report-across-scaled-out-cloud-databases-preview"></a>Rapport över utskalade moln databaser (förhands granskning)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Du kan skapa rapporter från flera databaser från en enda anslutnings punkt med en [elastisk fråga](elastic-query-overview.md). Databaserna måste vara vågrätt partitionerade (även kallade "shardade").

Om du har en befintlig databas, se [migrera befintliga databaser för att skala ut databaser](elastic-convert-to-use-elastic-tools.md).

Information om vilka SQL-objekt som krävs för att fråga finns i [fråga över vågrätt partitionerade databaser](elastic-query-horizontal-partitioning.md).

## <a name="prerequisites"></a>Förutsättningar

Hämta och kör [exemplet komma igång med Elastic Database verktyg](elastic-scale-get-started.md).

## <a name="create-a-shard-map-manager-using-the-sample-app"></a>Skapa en Shard Map Manager med hjälp av exempel appen
Här kommer du att skapa en Shard Map Manager tillsammans med flera Shards, följt av infogning av data i Shards. Om du redan har Shards-installationen med shardade-data kan du hoppa över följande steg och gå vidare till nästa avsnitt.

1. Skapa och kör exempel programmet för att **komma igång med Elastic Database verktyg** genom att följa stegen i artikel avsnittet [Hämta och köra exempel appen](elastic-scale-get-started.md#download-and-run-the-sample-app-1). När du har slutfört alla steg visas följande kommando tolk:

    ![kommando tolk][1]
2. Skriv "1" i kommando fönstret och tryck på **RETUR**. Detta skapar Shard Map Manager och lägger till två Shards på servern. Skriv "3" och tryck på **RETUR**; Upprepa åtgärden fyra gånger. Detta infogar exempel data rader i din Shards.
3. [Azure Portal](https://portal.azure.com) ska visa tre nya databaser på servern:

   ![Visual Studio-bekräftelse][2]

   I det här läget stöds frågor mellan databaser via Elastic Database klient bibliotek. Använd till exempel alternativ 4 i kommando fönstret. Resultatet från en multi-Shard-fråga är alltid en **union** av alla resultat från alla Shards.

   I nästa avsnitt skapar vi en exempel databas slut punkt som stöder omfattande frågor om data i Shards.

## <a name="create-an-elastic-query-database"></a>Skapa en elastisk fråga-databas

1. Öppna [Azure Portal](https://portal.azure.com) och logga in.
2. Skapa en ny databas i Azure SQL Database på samma server som din Shard-installation. Ge databasen namnet "ElasticDBQuery".

    ![Azure Portal-och pris nivå][3]

    > [!NOTE]
    > Du kan använda en befintlig databas. Om du kan göra det får det inte vara en av Shards som du vill köra dina frågor på. Den här databasen kommer att användas för att skapa Metadataobjektet för en elastisk databas fråga.
    >

## <a name="create-database-objects"></a>Skapa databas objekt
### <a name="database-scoped-master-key-and-credentials"></a>Huvud nyckel och autentiseringsuppgifter för databas omfattning
Dessa används för att ansluta till Shard Map Manager och Shards:

1. Öppna SQL Server Management Studio eller SQL Server Data Tools i Visual Studio.
2. Anslut till ElasticDBQuery-databasen och kör följande T-SQL-kommandon:

    ```tsql
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<master_key_password>';

    CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
    WITH IDENTITY = '<username>',
    SECRET = '<password>';
    ```

    "username" och "Password" måste vara samma som inloggnings information som används i steg 3 i avsnittet [Ladda ned och köra exempel appen](elastic-scale-get-started.md#download-and-run-the-sample-app) i artikeln **komma igång med Elastic Database verktyg** .

### <a name="external-data-sources"></a>Externa datakällor
Om du vill skapa en extern data källa kör du följande kommando på ElasticDBQuery-databasen:

```tsql
CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
    (TYPE = SHARD_MAP_MANAGER,
    LOCATION = '<server_name>.database.windows.net',
    DATABASE_NAME = 'ElasticScaleStarterKit_ShardMapManagerDb',
    CREDENTIAL = ElasticDBQueryCred,
    SHARD_MAP_NAME = 'CustomerIDShardMap'
) ;
```    

 "CustomerIDShardMap" är namnet på Shard-kartan om du har skapat Shard Map och Shard Map Manager med hjälp av verktyget elastiska databas verktyg. Men om du har använt den anpassade konfigurationen för det här exemplet bör det vara det Shard som du valde i ditt program.

### <a name="external-tables"></a>Externa tabeller
Skapa en extern tabell som matchar tabellen kunder på Shards genom att köra följande kommando på ElasticDBQuery Database:

```tsql
CREATE EXTERNAL TABLE [dbo].[Customers]
( [CustomerId] [int] NOT NULL,
    [Name] [nvarchar](256) NOT NULL,
    [RegionId] [int] NOT NULL)
WITH
( DATA_SOURCE = MyElasticDBQueryDataSrc,
    DISTRIBUTION = SHARDED([CustomerId])
) ;
```

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Köra ett exempel på en elastisk databas T-SQL-fråga
När du har definierat din externa data källa och dina externa tabeller kan du nu använda fullständig T-SQL över dina externa tabeller.

Kör den här frågan på ElasticDBQuery-databasen:

```tsql
select count(CustomerId) from [dbo].[Customers]
```

Du kommer att märka att frågan sammanställer resultat från alla Shards och ger följande utdata:

![Information om utdata][4]

## <a name="import-elastic-database-query-results-to-excel"></a>Importera resultat från Elastic Database-fråga till Excel
 Du kan importera resultaten från en fråga till en Excel-fil.

1. Starta Excel 2013.
2. Navigera till menyfliksområdet **data** .
3. Klicka på **från andra källor** och klicka på **från SQL Server**.

   ![Excel-import från andra källor][5]
4. I **guiden data anslutning** anger du Server namnet och inloggnings uppgifterna. Klicka på **Nästa**.
5. Välj den **databas som innehåller de data du vill använda** i dialog rutan och välj **ElasticDBQuery** -databasen.
6. Välj tabellen **kunder** i listvyn och klicka på **Nästa**. Klicka sedan på **Slutför**.
7. I formuläret **Importera data** under **Välj hur du vill visa data i din arbets bok väljer du** **tabell** och klickar på **OK**.

Alla rader från tabellen **kunder** , lagrade i olika Shards, fyller i Excel-bladet.

Nu kan du använda Excels kraftfulla funktioner för data visualisering. Du kan använda anslutnings strängen med Server namnet, databas namnet och autentiseringsuppgifterna för att ansluta dina BI-och data integrerings verktyg till Elastic Query-databasen. Kontrol lera att SQL Server stöds som data källa för ditt verktyg. Du kan referera till Elastic Query-databasen och externa tabeller precis som andra SQL Server databas-och SQL Server tabeller som du ansluter till med ditt verktyg.

### <a name="cost"></a>Cost
Det kostar inget extra att använda funktionen Elastic Database fråga.

Pris information finns [SQL Database pris information](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Nästa steg

* En översikt över elastisk fråga finns i [Översikt över elastiska frågor](elastic-query-overview.md).
* En lodrät partitionerings guide finns i [komma igång med kors databas fråga (lodrät partitionering)](elastic-query-getting-started-vertical.md).
* För syntax och exempel frågor för lodrätt partitionerade data, se [fråga lodrätt partitionerade data)](elastic-query-vertical-partitioning.md)
* För syntax och exempel frågor för vågrätt partitionerade data, se [fråga efter vågrätt partitionerade data)](elastic-query-horizontal-partitioning.md)
* Se [SP \_ execute \_ Remote](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database) för en lagrad procedur som kör ett Transact-SQL-uttryck på en enskild fjärr Azure SQL Database eller uppsättning databaser som fungerar som Shards i ett schema med vågrät partitionering.


<!--Image references-->
[1]: ./media/elastic-query-getting-started/cmd-prompt.png
[2]: ./media/elastic-query-getting-started/portal.png
[3]: ./media/elastic-query-getting-started/tiers.png
[4]: ./media/elastic-query-getting-started/details.png
[5]: ./media/elastic-query-getting-started/exel-sources.png
<!--anchors-->