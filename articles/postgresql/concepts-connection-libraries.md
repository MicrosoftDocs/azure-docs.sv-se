---
title: Anslutnings bibliotek – Azure Database for PostgreSQL-enskild server
description: I den här artikeln beskrivs flera bibliotek och driv rutiner som du kan använda när du kodar program för att ansluta och fråga Azure Database for PostgreSQL-enskild server.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 45081c6ba161686498398f2c4ccae8b4cff4c0d1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91704318"
---
# <a name="connection-libraries-for-azure-database-for-postgresql---single-server"></a>Anslutnings bibliotek för Azure Database for PostgreSQL-enskild server
Den här artikeln innehåller bibliotek och driv rutiner som utvecklare kan använda för att utveckla program för att ansluta till och fråga Azure Database for PostgreSQL.

## <a name="client-interfaces"></a>Klient gränssnitt
De flesta språkklientbibliotek som används för att ansluta till PostgreSQL-servern är externa projekt och distribueras oberoende. De bibliotek som visas i listan stöds på Windows-, Linux-och Mac-plattformar för att ansluta till Azure Database for PostgreSQL. Flera snabb starts exempel visas i avsnittet Nästa steg.

| **Språk** | **Klient gränssnitt** | **Ytterligare information** | **Ladda ned** |
|--------------|----------------------------------------------------------------|-------------------------------------|--------------------------------------------------------------------|
| Python | [psycopg](http://initd.org/psycopg/) | DB API 2,0-kompatibel | [Ladda ned](http://initd.org/psycopg/download/) |
| PHP | [php – pgsql](https://secure.php.net/manual/en/book.pgsql.php) | Databas tillägg | [Installera](https://secure.php.net/manual/en/pgsql.installation.php) |
| Node.js | [PG NPM-paket](https://www.npmjs.com/package/pg) | Ren JavaScript-icke-blockerande klient | [Installera](https://www.npmjs.com/package/pg) |
| Java | [JDBC](https://jdbc.postgresql.org/) | Typ 4 JDBC-drivrutin | [Ladda ned](https://jdbc.postgresql.org/download.html)  |
| Ruby | [PG-gem](https://deveiate.org/code/pg/) | Ruby-gränssnitt | [Ladda ned](https://rubygems.org/downloads/pg-0.20.0.gem) |
| Go | [Paketets PQ](https://godoc.org/github.com/lib/pq) | Ren go postgres-drivrutin | [Installera](https://github.com/lib/pq/blob/master/README.md) |
| C \# /.net | [Npgsql](https://www.npgsql.org/) | ADO.NET data leverantör | [Ladda ned](https://www.microsoft.com/net/) |
| ODBC | [psqlODBC](https://odbc.postgresql.org/) | ODBC-drivrutin | [Ladda ned](https://www.postgresql.org/ftp/odbc/versions/) |
| C | [libpq](https://www.postgresql.org/docs/9.6/static/libpq.html) | Primärt språk gränssnitt för C | Ingår |
| C++ | [libpqxx](http://pqxx.org/) | New-Style C++-gränssnitt | [Ladda ned](http://pqxx.org/download/software/) |

## <a name="next-steps"></a>Nästa steg
Läs de här snabb starterna om hur du ansluter till och frågar Azure Database for PostgreSQL med hjälp av ditt val av språk:

[Python](./connect-python.md)  |  [Node.JS](./connect-nodejs.md)  |  [Java](./connect-java.md)  |  [Ruby](./connect-ruby.md)  |  [Php](./connect-php.md)  |  [.net (C#)](./connect-csharp.md)  |  [Gå](./connect-go.md)
