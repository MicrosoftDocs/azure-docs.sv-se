---
title: Anslut och fråga – PostgreSQL för en server
description: Länkar till snabb starter som visar hur du ansluter till din Azure Database for PostgreSQL enskild server och kör frågor.
services: postgresql
ms.service: postgresql
ms.topic: how-to
author: mksuni
ms.author: sumuth
ms.date: 09/21/2020
ms.openlocfilehash: 413f5fc1f6579102b62042c1470816470c2e1449
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92546559"
---
# <a name="connect-and-query-overview-for-azure-database-for-postgresql--single-server"></a>Översikt av anslutning och frågekörning för Azure Database for PostgreSQL – enskild server

Följande dokument innehåller länkar till exempel som visar hur du ansluter och frågar med Azure Database for PostgreSQL enskild server. Den här guiden innehåller även TLS-rekommendationer och tillägg som du kan använda för att ansluta till servern i språk som stöds nedan.

## <a name="quickstarts"></a>Snabbstarter

| Snabbstart | Beskrivning |
|---|---|
|[PgAdmin](https://www.pgadmin.org/)|Du kan använda pgAdmin för att ansluta till servern och fören klar skapandet, underhållet och användningen av databas objekt.|
|[psql i Azure Cloud Shell](quickstart-create-server-database-azure-cli.md#connect-to-the-azure-database-for-postgresql-server-by-using-psql)|Den här artikeln visar hur du kör [**psql**](https://www.postgresql.org/docs/current/static/app-psql.html) i [Azure Cloud Shell](../cloud-shell/overview.md) för att ansluta till servern och sedan köra instruktioner för att fråga, infoga, uppdatera och ta bort data i databasen. Du kan köra **psql** om det är installerat i utvecklings miljön|
|[PostgreSQL med VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)|Med tillägget Azure-databaser för VS Code (för hands version) kan du bläddra i och fråga din PostgreSQL-Server både lokalt och i molnet med hjälp av klipp bok med omfattande IntelliSense. |
|[PHP](connect-php.md)|Den här snabb starten visar hur du använder PHP för att skapa ett program för att ansluta till en databas och använda arbeta med databas objekt för att fråga data.|
|[Java](connect-java.md)|Den här snabb starten visar hur du använder Java för att ansluta till en databas och sedan använder arbeta med databas objekt för att fråga efter data.|
|[Node.js](connect-nodejs.md)|Den här snabb starten visar hur du använder Node.js för att skapa ett program för att ansluta till en databas och använda arbeta med databas objekt för att fråga data.|
|[.NET (C#)](connect-csharp.md)|Den här snabb starten visar hur du use.NET (C#) för att skapa ett C#-program för att ansluta till en databas och använda arbeta med databas objekt för att fråga data.|
|[Kör](connect-go.md)|Den här snabb starten visar hur du använder Go för att ansluta till en databas. Här visas också hur du använder Transact-SQL-uttryck för att köra frågor mot och ändra data.|
|[Python](connect-python.md)|Den här snabb starten visar hur du använder python för att ansluta till en databas och hur du använder arbeta med databas objekt för att fråga efter data. |
|[Ruby](connect-ruby.md)|Den här snabb starten visar hur du använder ruby för att skapa ett program för att ansluta till en databas och använda arbeta med databas objekt för att fråga efter data.|

## <a name="tls-considerations-for-database-connectivity"></a>TLS-överväganden för databas anslutning

Transport Layer Security (TLS) används av alla driv rutiner som Microsoft tillhandahåller eller stöder för att ansluta till databaser i Azure Database for PostgreSQL. Ingen särskild konfiguration krävs, men Använd TLS 1,2 för nyligen skapade servrar. Vi rekommenderar att du använder TLS 1,0 och 1,1 för att uppdatera TLS-versionen för dina servrar. Se [så här konfigurerar du TLS](howto-tls-configurations.md)

## <a name="postgresql-extensions"></a>PostgreSQL-tillägg

PostgreSQL ger möjlighet att utöka funktionerna i databasen med hjälp av tillägg. Tillägg samlar flera SQL-objekt i ett enda paket som kan läsas in eller tas bort från databasen med ett enda kommando. När tilläggen har lästs in i databasen fungerar de som inbyggda funktioner.

- [Postgres 11-tillägg](./concepts-extensions.md#postgres-11-extensions)
- [Postgres 10-tillägg](./concepts-extensions.md#postgres-10-extensions)
- [Postgres 9,6-tillägg](./concepts-extensions.md#postgres-96-extensions)
- [Postgres 9,5-tillägg](./concepts-extensions.md#postgres-95-extensions)

Mer information finns i [så här använder du postgresql-tillägg på en enskild server](concepts-extensions.md).

## <a name="next-steps"></a>Nästa steg

- [Migrera data med dumpa och Återställ](howto-migrate-using-dump-and-restore.md)
- [Migrera data med import och export](howto-migrate-using-export-and-import.md)
