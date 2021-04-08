---
title: Migrera befintliga databaser för att skala ut
description: Konvertera shardade-databaser för att använda Elastic Database-verktyg genom att skapa en Shard Map Manager
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
ms.openlocfilehash: c6ad8b4c80f4b9c2fdb3c1a14209dcf0febc89e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92787150"
---
# <a name="migrate-existing-databases-to-scale-out"></a>Migrera befintliga databaser för att skala ut
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Hantera enkelt dina befintliga utskalade shardade-databaser med hjälp av verktyg (till exempel [klient biblioteket för Elastic Database](elastic-database-client-library.md)). Konvertera först en befintlig uppsättning databaser till att använda [Shard Map Manager](elastic-scale-shard-map-management.md).

## <a name="overview"></a>Översikt

Så här migrerar du en befintlig shardade-databas:

1. Förbered [Shard Map Manager-databasen](elastic-scale-shard-map-management.md).
2. Skapa Shard-kartan.
3. Förbered de enskilda Shards.  
4. Lägg till mappningar i Shard-kartan.

Dessa metoder kan implementeras med hjälp av antingen [.NET Framework klient biblioteket](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)eller PowerShell-skripten som finns på [Azure SQL Database-Elastic Database verktyg skript](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db). Exemplen här använder PowerShell-skripten.

Mer information om ShardMapManager finns i [Shard Map Management](elastic-scale-shard-map-management.md). En översikt över Elastic Database-verktygen finns i [Översikt över Elastic Database funktioner](elastic-scale-introduction.md).

## <a name="prepare-the-shard-map-manager-database"></a>Förbered Shard Map Manager-databasen

Shard Map Manager är en särskild databas som innehåller data för att hantera utskalade databaser. Du kan använda en befintlig databas eller skapa en ny databas. En databas som fungerar som Shard Map Manager ska inte vara samma databas som en Shard. PowerShell-skriptet skapar inte databasen åt dig.

## <a name="step-1-create-a-shard-map-manager"></a>Steg 1: skapa en Shard Map Manager

```powershell
# Create a shard map manager
New-ShardMapManager -UserName '<user_name>' -Password '<password>' -SqlServerName '<server_name>' -SqlDatabaseName '<smm_db_name>'
#<server_name> and <smm_db_name> are the server name and database name
# for the new or existing database that should be used for storing
# tenant-database mapping information.
```

### <a name="to-retrieve-the-shard-map-manager"></a>Hämta Shard Map Manager

När du har skapat den kan du hämta Shard Map Manager med denna cmdlet. Det här steget krävs varje gång du behöver använda ShardMapManager-objektet.

```powershell
# Try to get a reference to the Shard Map Manager  
$ShardMapManager = Get-ShardMapManager -UserName '<user_name>' -Password '<password>' -SqlServerName '<server_name>' -SqlDatabaseName '<smm_db_name>'
```

## <a name="step-2-create-the-shard-map"></a>Steg 2: skapa Shard-kartan

Välj den typ av Shard-mappning som ska skapas. Valet beror på databas arkitekturen:

1. Enskild klient per databas (för villkor, se [ord](elastic-scale-glossary.md)listan.)
2. Flera klienter per databas (två typer):
   1. List mappning
   2. Intervall mappning

För en modell med en enda klient organisation skapar du en **list mappning** Shard-karta. Modellen med en enskild klient tilldelas en databas per klient. Det här är en effektiv modell för SaaS-utvecklare som fören klar hanteringen.

![List mappning][1]

Modellen för flera innehavare tilldelar flera klienter till en enskild databas (och du kan distribuera grupper av klienter i flera databaser). Använd den här modellen när du förväntar dig att varje klient ska ha små data behov. I den här modellen tilldelar du ett antal klienter till en databas med hjälp av **intervall mappning**.

![Intervall mappning][2]

Eller så kan du implementera en databas modell med flera klienter med en *list mappning* för att tilldela flera klienter till en enskild databas. Till exempel används DB1 för att lagra information om klient-ID 1 och 5, och DB2 lagrar data för klient 7 och klient 10.

![Flera klienter i en enskild databas][3]

**Välj något av följande alternativ baserat på ditt val:**

### <a name="option-1-create-a-shard-map-for-a-list-mapping"></a>Alternativ 1: skapa en Shard-karta för en List mappning

Skapa en Shard-karta med ShardMapManager-objektet.

```powershell
# $ShardMapManager is the shard map manager object
$ShardMap = New-ListShardMap -KeyType $([int]) -ListShardMapName 'ListShardMap' -ShardMapManager $ShardMapManager
```

### <a name="option-2-create-a-shard-map-for-a-range-mapping"></a>Alternativ 2: skapa en Shard-karta för en intervall mappning

För att kunna använda detta mappnings mönster måste klient-ID-värdena vara kontinuerliga intervall och det är acceptabelt att ha luckor i intervallen genom att hoppa över intervallet när databaserna skapas.

```powershell
# $ShardMapManager is the shard map manager object
# 'RangeShardMap' is the unique identifier for the range shard map.  
$ShardMap = New-RangeShardMap -KeyType $([int]) -RangeShardMapName 'RangeShardMap' -ShardMapManager $ShardMapManager
```

### <a name="option-3-list-mappings-on-an-individual-database"></a>Alternativ 3: lista mappningar i en enskild databas

När du ställer in det här mönstret måste du också skapa en List karta som visas i steg 2, alternativ 1.

## <a name="step-3-prepare-individual-shards"></a>Steg 3: förbereda enskilda Shards

Lägg till varje Shard (databas) till Shard Map Manager. Detta förbereder de enskilda databaserna för lagring av mappnings information. Kör den här metoden på varje Shard.

```powershell
Add-Shard -ShardMap $ShardMap -SqlServerName '<shard_server_name>' -SqlDatabaseName '<shard_database_name>'
# The $ShardMap is the shard map created in step 2.
```

## <a name="step-4-add-mappings"></a>Steg 4: Lägg till mappningar

Tillägget av mappningar beror på vilken typ av Shard-karta du har skapat. Om du har skapat en List karta lägger du till List mappningar. Om du har skapat en intervall karta lägger du till intervall mappningar.

### <a name="option-1-map-the-data-for-a-list-mapping"></a>Alternativ 1: mappa data för en List mappning

Mappa data genom att lägga till en List mappning för varje klient.  

```powershell
# Create the mappings and associate it with the new shards
Add-ListMapping -KeyType $([int]) -ListPoint '<tenant_id>' -ListShardMap $ShardMap -SqlServerName '<shard_server_name>' -SqlDatabaseName '<shard_database_name>'
```

### <a name="option-2-map-the-data-for-a-range-mapping"></a>Alternativ 2: mappa data för en intervall mappning

Lägg till intervall mappningar för alla klient-ID-intervall – databas associationer:

```powershell
# Create the mappings and associate it with the new shards
Add-RangeMapping -KeyType $([int]) -RangeHigh '5' -RangeLow '1' -RangeShardMap $ShardMap -SqlServerName '<shard_server_name>' -SqlDatabaseName '<shard_database_name>'
```

### <a name="step-4-option-3-map-the-data-for-multiple-tenants-on-an-individual-database"></a>Steg 4 alternativ 3: mappa data för flera klienter i en enskild databas

Kör Add-ListMapping (alternativ 1) för varje klient.

## <a name="checking-the-mappings"></a>Kontrollerar mappningarna

Information om befintliga Shards och mappningar som är associerade med dem kan frågas med hjälp av följande kommandon:  

```powershell
# List the shards and mappings
Get-Shards -ShardMap $ShardMap
Get-Mappings -ShardMap $ShardMap
```

## <a name="summary"></a>Sammanfattning

När du har slutfört installationen kan du börja använda Elastic Database klient biblioteket. Du kan också använda [data beroende routning](elastic-scale-data-dependent-routing.md) och [Shard fråga](elastic-scale-multishard-querying.md).

## <a name="next-steps"></a>Nästa steg

Hämta PowerShell-skripten från [Azure SQL Database-Elastic Database tools-skript](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

Verktygen finns också på GitHub: [Azure/Elastic-DB-tools](https://github.com/Azure/elastic-db-tools).

Använd verktyget Dela och slå samman för att flytta data till eller från en modell med flera klienter till en enda klient modell. Se [verktyget Dela sammanslagning](elastic-scale-get-started.md).

## <a name="additional-resources"></a>Ytterligare resurser

Information om vanliga mönster för dataarkitekturen i SaaS-databasprogram (Software-as-a-Service) med flera klienter finns i [Designmönster för SaaS-program med flera klienter i Azure SQL Database](saas-tenancy-app-design-patterns.md).

## <a name="questions-and-feature-requests"></a>Frågor och funktions begär Anden

Om du har frågor kan du använda [sidan Microsoft Q&en fråga för SQL Database](/answers/topics/azure-sql-database.html) och för funktions förfrågningar kan du lägga till dem i den [SQL Database feedback-forumet](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/elastic-convert-to-use-elastic-tools/listmapping.png
[2]: ./media/elastic-convert-to-use-elastic-tools/rangemapping.png
[3]: ./media/elastic-convert-to-use-elastic-tools/multipleonsingledb.png