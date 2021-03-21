---
title: Flytta ett Azure Cosmos DB-konto till en annan region
description: Lär dig hur du flyttar ett Azure Cosmos DB-konto till en annan region.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 09/12/2020
ms.author: mjbrown
ms.openlocfilehash: 29b5c5d3cf55cd11fe505c0d9ab9b894dc2ad267
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93342036"
---
# <a name="move-an-azure-cosmos-db-account-to-another-region"></a>Flytta ett Azure Cosmos DB-konto till en annan region
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

I den här artikeln beskrivs hur du antingen:

- Flytta en region där data replikeras i Azure Cosmos DB.
- Migrera konto (Azure Resource Manager) metadata och data från en region till en annan.

## <a name="move-data-from-one-region-to-another"></a>Flytta data från en region till en annan

Azure Cosmos DB stöder datareplikering internt, så det är enkelt att flytta data från en region till en annan. Du kan göra det med hjälp av Azure Portal, Azure PowerShell eller Azure CLI. Det omfattar följande steg:

1. Lägg till en ny region i kontot.

    Om du vill lägga till en ny region i ett Azure Cosmos DB konto, se [Lägg till/ta bort regioner i ett Azure Cosmos DB konto](how-to-manage-database-account.md#addremove-regions-from-your-database-account).

1. Utför en manuell redundansväxling till den nya regionen.

    När den region som tas bort för närvarande är Skriv region för kontot, måste du starta en redundansväxling till den nya regionen som lades till i föregående steg. Detta är en åtgärd utan avbrott. Om du flyttar en Läs region i ett konto med flera regioner kan du hoppa över det här steget. 
    
    Information om hur du startar en redundansväxling finns i [utföra manuell redundans på ett Azure Cosmos-konto](how-to-manage-database-account.md#manual-failover).

1. Ta bort den ursprungliga regionen.

    Om du vill ta bort en region från ett Azure Cosmos DB konto, se [Lägg till/ta bort regioner från ditt Azure Cosmos DB-konto](how-to-manage-database-account.md#addremove-regions-from-your-database-account).

## <a name="migrate-azure-cosmos-db-account-metadata"></a>Migrera metadata för Azure Cosmos DB-konto

Azure Cosmos DB stöder inte internt migrering av konto metadata från en region till en annan. Om du vill migrera både metadata för kontot och kund informationen från en region till en annan måste du skapa ett nytt konto i önskad region och sedan kopiera data manuellt. 

En migrering vid ett nära noll-avbrott för SQL-API kräver att den [ändrings-feed](change-feed.md) eller det verktyg som använder det används. Om du migrerar MongoDB-API: et, API för Cassandra eller något annat API, eller om du vill lära dig mer om alternativ för att migrera data mellan konton, se [alternativ för att migrera dina lokala eller molnbaserade data till Azure Cosmos DB](cosmosdb-migrationchoices.md). 

Följande steg visar hur du migrerar ett Azure Cosmos DB-konto för SQL-API: et och dess data från en region till en annan:

1. Skapa ett nytt Azure Cosmos DB-konto i önskad region.

    Om du vill skapa ett nytt konto via Azure Portal, PowerShell eller Azure CLI, se [skapa ett Azure Cosmos DB konto](how-to-manage-database-account.md#create-an-account).

1. Skapa en ny databas och behållare.

    Information om hur du skapar en ny databas och behållare finns i [skapa en Azure Cosmos-behållare](how-to-create-container.md).

1. Migrera data med hjälp av Azure Cosmos DB Live data Migrator-verktyget.

    Information om hur du migrerar data med nästan noll stillestånd finns i [Azure Cosmos DB Live data Migrator-verktyget](https://github.com/Azure-Samples/azure-cosmosdb-live-data-migrator).

1. Uppdatera program anslutnings strängen.

    Med verktyget live data Migrator fortfarande igång uppdaterar du anslutnings informationen i den nya distributionen av ditt program. Du kan hämta slut punkter och nycklar för ditt program från Azure Portal.

    :::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-portal.png" alt-text="Åtkomst kontroll i Azure Portal som demonstrerar NoSQL-databasens säkerhet.":::

1. Omdirigera begär anden till det nya programmet.

    När det nya programmet är anslutet till Azure Cosmos DB kan du omdirigera klient begär anden till din nya distribution.

1. Ta bort alla resurser som du inte längre behöver.

    Med begär Anden som omdirigeras fullständigt till den nya instansen kan du ta bort det gamla Azure Cosmos DB-kontot och verktyget live data Migrator.

## <a name="next-steps"></a>Nästa steg

Mer information och exempel på hur du hanterar Azure Cosmos-kontot samt databaser och behållare finns i följande artiklar:

* [Hantera ett Azure Cosmos-konto](how-to-manage-database-account.md)
* [Ändra feed i Azure Cosmos DB](change-feed.md)
