---
title: Hantera autentiseringsuppgifter i klient biblioteket för Elastic Database
description: Ange rätt nivå för autentiseringsuppgifter, admin till skrivskyddad, för Elastic Database-appar
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: 5b91986d4f94861b87e413c9ff781107c3ed04a3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92786606"
---
# <a name="credentials-used-to-access-the-elastic-database-client-library"></a>Autentiseringsuppgifter som används för att komma åt Elastic Database klient biblioteket
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

I [Elastic Database klient biblioteket](elastic-database-client-library.md) används tre olika typer av autentiseringsuppgifter för att få åtkomst till [Shard Map Manager](elastic-scale-shard-map-management.md). Beroende på behov använder du autentiseringsuppgiften med den lägsta möjliga åtkomst nivån.

* **Autentiseringsuppgifter för hantering**: för att skapa eller ändra en Shard Map Manager. (Se [ord](elastic-scale-glossary.md)listan.)
* **Autentiseringsuppgifter för åtkomst**: för att få åtkomst till en befintlig Shard Map Manager för att hämta information om Shards.
* **Autentiseringsuppgifter för anslutning**: för att ansluta till Shards.

Se även [Hantera databaser och inloggningar i Azure SQL Database](logins-create-manage.md).

## <a name="about-management-credentials"></a>Om autentiseringsuppgifter för hantering

Hanterings autentiseringsuppgifter används för att skapa ett **ShardMapManager** -objekt ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)) för program som manipulerar Shard-kartor. (Se till exempel [lägga till en Shard med hjälp av Elastic Database verktyg](elastic-scale-add-a-shard.md) och [data beroende routning](elastic-scale-data-dependent-routing.md)). Användaren av klient biblioteket för elastisk skalning skapar SQL-användare och SQL-inloggningar och ser till att varje beviljas Läs-/Skriv behörighet för den globala Shard Map-databasen och alla Shard-databaser. Dessa autentiseringsuppgifter används för att underhålla den globala Shard-kartan och de lokala Shard-kartorna när ändringar i Shard-mappningen utförs. Använd till exempel hanterings uppgifter för att skapa Shard Map Manager-objektet (med **GetSqlShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)):

```java
// Obtain a shard map manager.
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmAdminConnectionString,ShardMapManagerLoadPolicy.Lazy);
```

Variabeln **smmAdminConnectionString** är en anslutnings sträng som innehåller autentiseringsuppgifterna för hantering. Användar-ID och lösen ord ger Läs-/Skriv behörighet till både Shard Map-databas och enskilda Shards. Hanterings anslutnings strängen innehåller även Server namnet och databas namnet för att identifiera den globala Shard Map-databasen. Här är en typisk anslutnings sträng för det syftet:

```java
"Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;”
```

Använd inte värden i formatet " username@server ", istället för att använda värdet "username".  Detta beror på att autentiseringsuppgifterna måste fungera mot både Shard Map Manager-databasen och enskilda Shards, vilket kan finnas på olika servrar.

## <a name="access-credentials"></a>Autentiseringsuppgifter för åtkomst

När du skapar en Shard Map Manager i ett program som inte administrerar Shard Maps, använder du autentiseringsuppgifter som har Läs behörighet för den globala Shard-kartan. Informationen som hämtas från den globala Shard-kartan under dessa autentiseringsuppgifter används för [data beroende routning](elastic-scale-data-dependent-routing.md) och för att fylla i Shard Map-cachen på klienten. Autentiseringsuppgifterna tillhandahålls via samma anrops mönster till **GetSqlShardMapManager**:

```java
// Obtain shard map manager.
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmReadOnlyConnectionString, ShardMapManagerLoadPolicy.Lazy);  
```

Observera användningen av **smmReadOnlyConnectionString** för att återspegla användningen av olika autentiseringsuppgifter för den här åtkomsten för användare som **inte är administratörer** : dessa autentiseringsuppgifter ska inte ge Skriv behörighet till den globala Shard-kartan.

## <a name="connection-credentials"></a>Autentiseringsuppgifter för anslutning

Ytterligare autentiseringsuppgifter krävs när du använder metoden **OpenConnectionForKey**  ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkey), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey)) för att få åtkomst till en Shard som är associerad med en horisontell partitionering-nyckel. Dessa autentiseringsuppgifter måste ge behörighet för skrivskyddad åtkomst till de lokala Shard kart tabeller som finns på Shard. Detta krävs för att utföra anslutnings validering för data beroende routning på Shard. Det här kodfragmentet ger åtkomst till data i kontexten för data beroende Routning:

```csharp
using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>(targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate))
```

I det här exemplet innehåller **smmUserConnectionString** anslutnings strängen för användarens autentiseringsuppgifter. För Azure SQL Database är här en typisk anslutnings sträng för användarautentiseringsuppgifter:

```java
"User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;”  
```

Använd inte värden i formatet "" som med administratörs behörighet username@server . Använd istället "username".  Observera också att anslutnings strängen inte innehåller ett server namn och ett databas namn. Det beror på att **OpenConnectionForKey** -anropet automatiskt dirigerar anslutningen till rätt Shard baserat på nyckeln. Därför anges inte databas namnet och Server namnet.

## <a name="see-also"></a>Se även

[Hantera databaser och inloggningar i Azure SQL Database](logins-create-manage.md)

[Säkra din SQL Database](security-overview.md)

[Elastic Database-jobb](elastic-jobs-overview.md)

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]