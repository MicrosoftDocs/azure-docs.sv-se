---
title: Delad databas
description: Azure Synapse Analytics tillhandahåller en delad metadata modell där du skapar en databas i en server lös Apache Spark pool kommer att vara tillgänglig från sin server utan SQL-pool och SQL-pooler.
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: 93602e522338166abac98c3e4a198e1aff392d21
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97934976"
---
# <a name="azure-synapse-analytics-shared-database"></a>Delad Azure Synapse Analytics-databas

Med Azure Synapse Analytics kan de olika data behandlings arbets ytans motorer dela databaser och tabeller. För närvarande delas databaserna och Parquet-tabellerna som skapas på Apache Spark pooler automatiskt med den serverbaserade SQL-adresspoolen.

En databas som skapats med ett Spark-jobb visas med samma namn för alla aktuella och framtida Spark-pooler i arbets ytan, inklusive SQL-poolen utan server. Du kan inte lägga till anpassade objekt (externa tabeller, vyer, procedurer) direkt i den här replikerade databasen med hjälp av SQL-poolen utan server.

Spark-standarddatabasen, `default` som anropas, visas också i den serverbaserade SQL-poolen kontext som en databas som kallas `default` .

Eftersom databaserna är synkroniserade med en server lös SQL-pool asynkront kommer det att finnas en fördröjning tills de visas.

## <a name="manage-a-spark-created-database"></a>Hantera en spark-skapad databas

Använd Spark för att hantera Spark-skapade databaser. Du kan t. ex. ta bort den via ett Spark-jobb och skapa tabeller i det från Spark.

Om du skapar objekt i en spark-skapad databas med SQL-pool utan server eller försöker släppa databasen, lyckas åtgärden. Men den ursprungliga Spark-databasen ändras inte.

## <a name="how-name-conflicts-are-handled"></a>Hur namn konflikter hanteras

Om namnet på en spark-databas är i konflikt med namnet på en befintlig databas för server utan SQL-pool, läggs ett suffix till i Spark-databasen i SQL-poolen utan server. Suffixet i SQL-poolen utan server är `_<workspace name>-ondemand-DefaultSparkConnector` .

Om t. ex. en spark-databas som heter `mydb` skapas i Azure DataSynapses-arbetsytan `myws` och det redan finns en databas för SQL-poolen utan server med det namnet, måste Spark-databasen i SQL-poolen utan server refereras med namnet `mydb_myws-ondemand-DefaultSparkConnector` .

> [!CAUTION]
> Varning: du bör inte ta ett beroende på detta beteende.

## <a name="security-model"></a>Säkerhetsmodell

Spark-databaser och-tabeller, tillsammans med deras synkroniserade representationer i SQL-motorn, kommer att skyddas på den underliggande lagrings nivån.

Det säkerhetsobjekt som skapar en databas anses vara ägare av den databasen och har alla rättigheter till databasen och dess objekt.

Om du vill ge ett säkerhets objekt, till exempel en användare eller en säkerhets grupp, åtkomst till en databas, anger du rätt POSIX-mapp och fil behörigheter för de underliggande mapparna och filerna i `warehouse` katalogen. 

För att ett säkerhets objekt ska kunna läsa en tabell i en databas måste till exempel alla mappar som börjar på databasmappen i `warehouse` katalogen ha `X` och `R` behörighet som tilldelas till säkerhets objekt. Dessutom kräver filer (till exempel tabellens underliggande datafiler) `R` behörigheter. 

Om ett säkerhets objekt kräver möjlighet att skapa objekt eller släppa objekt i en databas, krävs ytterligare `W` behörigheter för mapparna och filerna i `warehouse` mappen.

## <a name="examples"></a>Exempel

### <a name="create-and-connect-to-spark-database-with-serverless-sql-pool"></a>Skapa och Anslut till Spark-databasen med en server lös SQL-pool

Skapa först en ny Spark-databas `mytestdb` som heter med ett Spark-kluster som du redan har skapat i din arbets yta. Du kan till exempel använda en spark C#-anteckningsbok med följande .NET for Spark-instruktion:

```csharp
spark.Sql("CREATE DATABASE mytestdb")
```

Efter en kort fördröjning kan du se databasen från Server lös SQL-pool. Kör till exempel följande uttryck från SQL-poolen utan server.

```sql
SELECT * FROM sys.databases;
```

Kontrol lera att `mytestdb` ingår i resultaten.

## <a name="next-steps"></a>Nästa steg

- [Läs mer om delade metadata för Azure Synapse Analytics](overview.md)
- [Läs mer om delade metadata-tabeller för Azure Synapse Analytics](table.md)
