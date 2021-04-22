---
title: Anslutningsbibliotek – Azure Database for PostgreSQL – enskild server
description: I den här artikeln beskrivs flera bibliotek och drivrutiner som du kan använda när du kodar program för att ansluta Azure Database for PostgreSQL – enskild server.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: b8a1526605195b5eb24d8044f42b70ca5336bf7c
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107878316"
---
# <a name="connection-libraries-for-azure-database-for-postgresql---single-server"></a>Anslutningsbibliotek för Azure Database for PostgreSQL – enskild server
Den här artikeln innehåller bibliotek och drivrutiner som utvecklare kan använda för att utveckla program för att ansluta till och fråga Azure Database for PostgreSQL.

## <a name="client-interfaces"></a>Klientgränssnitt
De flesta språkklientbibliotek som används för att ansluta till PostgreSQL-servern är externa projekt och distribueras oberoende. Biblioteken i listan stöds på plattformarna Windows, Linux och Mac för att ansluta till Azure Database for PostgreSQL. Flera snabbstartsexempel visas i avsnittet Nästa steg.

| **Språk** | **Klientgränssnitt** | **Ytterligare information** | **Ladda ned** |
|--------------|----------------------------------------------------------------|-------------------------------------|--------------------------------------------------------------------|
| Python | [Psycopg](http://initd.org/psycopg/) | DB API 2.0-kompatibel | [Ladda ned](http://initd.org/psycopg/download/) |
| PHP | [php-pgsql](https://secure.php.net/manual/en/book.pgsql.php) | Databastillägg | [Installera](https://secure.php.net/manual/en/pgsql.installation.php) |
| Node.js | [Pg npm-paket](https://www.npmjs.com/package/pg) | Pure JavaScript-klient som inte är blockerande | [Installera](https://www.npmjs.com/package/pg) |
| Java | [JDBC](https://jdbc.postgresql.org/) | Typ 4 JDBC-drivrutin | [Ladda ned](https://jdbc.postgresql.org/download.html)  |
| Ruby | [Pg gem](https://deveiate.org/code/pg/) | Ruby-gränssnitt | [Ladda ned](https://rubygems.org/downloads/pg-0.20.0.gem) |
| Go | [Paketera pq](https://godoc.org/github.com/lib/pq) | Pure Go postgres-drivrutin | [Installera](https://github.com/lib/pq/blob/master/README.md) |
| \#C/.NET | [Npgsql](https://www.npgsql.org/) | ADO.NET dataprovider | [Ladda ned](https://dotnet.microsoft.com/download) |
| ODBC | [Psqlodbc](https://odbc.postgresql.org/) | ODBC-drivrutin | [Ladda ned](https://www.postgresql.org/ftp/odbc/versions/) |
| C | [Libpq](https://www.postgresql.org/docs/9.6/static/libpq.html) | Primärt C-språkgränssnitt | Ingår |
| C++ | [Libpqxx](http://pqxx.org/) | Nytt C++-gränssnitt | [Ladda ned](http://pqxx.org/download/software/) |

## <a name="next-steps"></a>Nästa steg
Läs de här snabbstarterna om hur du ansluter till och Azure Database for PostgreSQL frågor med hjälp av det språk du väljer:

[Python](./connect-python.md)  |  [Node.JS](./connect-nodejs.md)  |  [Java](./connect-java.md)  |  [Ruby](./connect-ruby.md)  |  [PHP](./connect-php.md)  |  [.NET (C#)](./connect-csharp.md)  |  [Kör](./connect-go.md)
