---
title: Anslut och fråga – enskild server MySQL
description: Länkar till snabb starter som visar hur du ansluter till din Azures SQL Database enda server och kör frågor.
services: mysql
ms.service: mysql
ms.topic: how-to
author: mksuni
ms.author: sumuth
ms.date: 09/22/2020
ms.openlocfilehash: 0414aaad5a1cf6edb9c2152eed70f8753946cca1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92546440"
---
# <a name="connect-and-query-overview-for-azure-database-for-mysql--single-server"></a>Ansluta och fråga översikt för Azure Database for MySQL – en server

Följande dokument innehåller länkar till exempel som visar hur du ansluter och frågar med Azure Database for MySQL enskild server. Den här guiden innehåller även TLS-rekommendationer och-bibliotek som du kan använda för att ansluta till servern i språk som stöds nedan.

## <a name="quickstarts"></a>Snabbstarter

| Snabbstart | Beskrivning |
|---|---|
|[MySQL Workbench](connect-workbench.md)|Den här snabb starten visar hur du använder MySQL Workbench-klienten för att ansluta till en databas. Sedan kan du använda MySQL-instruktioner för att fråga, infoga, uppdatera och ta bort data i databasen.|
|[Azure Cloud Shell](./quickstart-create-mysql-server-database-using-azure-cli.md#connect-to-azure-database-for-mysql-server-using-mysql-command-line-client)|Den här artikeln visar hur du kör **mysql.exe** i [Azure Cloud Shell](../cloud-shell/overview.md) för att ansluta till servern och sedan köra instruktioner för att fråga, infoga, uppdatera och ta bort data i databasen.|
|[MySQL med Visual Studio](https://www.mysql.com/why-mysql/windows/visualstudio)|Du kan använda MySQL för Visual Studio för att ansluta till MySQL-servern. MySQL för Visual Studio integreras direkt i Server Explorer gör det enkelt att konfigurera nya anslutningar och arbeta med databas objekt.|
|[PHP](connect-php.md)|Den här snabb starten visar hur du använder PHP för att skapa ett program för att ansluta till en databas och använda MySQL-uttryck för att fråga data.|
|[Java](connect-java.md)|Den här snabb starten visar hur du använder Java för att ansluta till en databas och sedan använder MySQL-uttryck för att fråga efter data.|
|[Node.js](connect-nodejs.md)|Den här snabb starten visar hur du använder Node.js för att skapa ett program för att ansluta till en databas och använda MySQL-uttryck för att fråga data.|
|[.NET (C#)](connect-csharp.md)|Den här snabb starten visar hur du use.NET (C#) för att skapa ett C#-program för att ansluta till en databas och använda MySQL-uttryck för att fråga data.|
|[Kör](connect-go.md)|Den här snabb starten visar hur du använder Go för att ansluta till en databas. Här visas också hur du använder Transact-SQL-uttryck för att köra frågor mot och ändra data.|
|[Python](connect-python.md)|Den här snabb starten visar hur du använder python för att ansluta till en databas och använda MySQL-uttryck för att fråga data. |
|[Ruby](connect-ruby.md)|Den här snabb starten visar hur du använder ruby för att skapa ett program för att ansluta till en databas och använda MySQL-uttryck för att fråga data.|
|[C++](connect-cpp.md)|Den här snabb starten visar hur du använder C++ + för att skapa ett program för att ansluta till en databas och använda frågedata.|

## <a name="tls-considerations-for-database-connectivity"></a>TLS-överväganden för databas anslutning

Transport Layer Security (TLS) används av alla driv rutiner som Microsoft tillhandahåller eller stöder för att ansluta till databaser i Azure Database for MySQL. Ingen särskild konfiguration krävs, men Använd TLS 1,2 för nyligen skapade servrar. Vi rekommenderar att du använder TLS 1,0 och 1,1 för att uppdatera TLS-versionen för dina servrar. Se [så här konfigurerar du TLS](howto-tls-configurations.md)

## <a name="libraries"></a>Bibliotek

Azure Database for MySQL använder världens mest populära community-version av MySQL-databasen. Därför är den kompatibel med en mängd olika programmeringsspråk och driv rutiner. Målet är att stödja de tre senaste versionerna av MySQL-drivrutinerna och ansträngningar med författare från communityn för öppen källkod för att ständigt förbättra funktionaliteten och användbarheten hos MySQL-drivrutinerna Fortsätt.

Se vilka [driv rutiner](concepts-compatibility.md) som är kompatibla med Azure Database for MySQL enskild server.

## <a name="next-steps"></a>Nästa steg

- [Migrera data med dumpa och Återställ](concepts-migrate-dump-restore.md)
- [Migrera data med import och export](concepts-migrate-import-export.md)