---
title: Kompatibilitet för driv rutiner och verktyg – Azure Database for MariaDB
description: I den här artikeln beskrivs de MariaDB-drivrutiner och hanterings verktyg som är kompatibla med Azure Database for MariaDB.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 49fd2c8da3ba0a44f4901bc6c7a2241e9e20df4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98659877"
---
# <a name="mariadb-drivers-and-management-tools-compatible-with-azure-database-for-mariadb"></a>MariaDB-drivrutiner och hanterings verktyg som är kompatibla med Azure Database for MariaDB

I den här artikeln beskrivs de driv rutiner och hanterings verktyg som är kompatibla med Azure Database for MariaDB.

## <a name="mariadb-drivers"></a>MariaDB-drivrutiner

Azure Database for MariaDB använder community-versionen av MariaDB-servern. Därför är den kompatibel med en mängd olika programmeringsspråk och driv rutiner. API och protokoll för MariaDB är kompatibla med de som används av MySQL. Det innebär att anslutningar som fungerar med MySQL även ska fungera med MariaDB.

Målet är att stödja de tre senaste versionerna av MariaDB-drivrutinerna och ansträngningar med författare från communityn för öppen källkod för att ständigt förbättra funktionaliteten och användbarheten hos MariaDB-drivrutiner. En lista med driv rutiner som har testats och som är kompatibla med Azure Database for MariaDB 10,2 finns i följande tabell:

**Drivrutinen** | **Länkar** | **Kompatibla versioner** | **Inkompatibla versioner** | **Kommentarer**
---|---|---|---|---
PHP | https://secure.php.net/downloads.php | 5,5, 5,6, 7. x | 5.3 | För PHP 7,0-anslutning med SSL MySQLi lägger du till MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT i anslutnings strängen. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> SUB set: ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` alternativ till false.
.NET | [MySqlConnector på GitHub](https://github.com/mysql-net/MySqlConnector) <br> [Installations paket från NuGet](https://www.nuget.org/packages/MySqlConnector/) | 0,27 och efter | 0.26.5 och före |
MySQL-anslutning/NET | [MySQL-anslutning/NET](https://github.com/mysql/mysql-connector-net) | 8,0, 7,0, 6,10 |  | Ett kodnings fel kan orsaka att anslutningar Miss lyckas på vissa icke-UTF8-Windows-system.
Node.js |  [MySQLjs på GitHub](https://github.com/mysqljs/mysql/) <br> Installations paket från NPM:<br> Kör `npm install mysql` från NPM | 2.15 | 2.14.1 och före
GO | https://github.com/go-sql-driver/mysql/releases | 1,3, 1,4 | 1,2 och före | Använd `allowNativePasswords=true` i anslutnings strängen för version 1,3. Version 1,4 innehåller en korrigering och `allowNativePasswords=true` krävs inte längre.
Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2,0, 2,1, 2,2 | 1.2.2 och före |
Java | https://downloads.mariadb.org/connector-java/ | 2,1, 2,0, 1,6 | 1.5.5 och före |

## <a name="management-tools"></a>Hanteringsverktyg

Fördelen med kompatibilitet är också till för databas hanterings verktyg. Dina befintliga verktyg bör fortsätta att fungera med Azure Database for MariaDB, så länge databas hanteringen fungerar inom de olika användar behörigheterna. Tre vanliga verktyg för databas hantering som har testats och visat sig vara kompatibla med Azure Database for MariaDB 10,2 visas i följande tabell:

| Action | **MySQL Workbench 6. x och uppåt** | **Navicat 12** | **PHPMyAdmin 4. x och uppåt**
---|---|---|---
Skapa, uppdatera, läsa, skriva, ta bort | X | X | X
SSL-anslutning | X | X | X
Automatisk komplettering av SQL-fråga | X | X |
Importera och exportera data | X | X | X
Exportera till flera format | X | X | X
Säkerhetskopiera och återställ |  | X |
Visa Server parametrar | X | X | X
Visa klient anslutningar | X | X | X

## <a name="next-steps"></a>Nästa steg

- [Felsöka anslutningsproblem till Azure Database for MariaDB](howto-troubleshoot-common-connection-issues.md)