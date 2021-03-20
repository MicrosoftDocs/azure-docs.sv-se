---
title: Kompatibilitet för driv rutiner och verktyg – Azure Database for MySQL
description: I den här artikeln beskrivs de MySQL-drivrutiner och hanterings verktyg som är kompatibla med Azure Database for MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: c2faf8e8b6a814989fa9e1693fddeedb525df13a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100006861"
---
# <a name="mysql-drivers-and-management-tools-compatible-with-azure-database-for-mysql"></a>MySQL-drivrutiner och hanterings verktyg som är kompatibla med Azure Database for MySQL
I den här artikeln beskrivs de driv rutiner och hanterings verktyg som är kompatibla med Azure Database for MySQL.

## <a name="mysql-drivers"></a>MySQL-drivrutiner
Azure Database for MySQL använder världens mest populära community-version av MySQL-databasen. Därför är den kompatibel med en mängd olika programmeringsspråk och driv rutiner. Målet är att stödja de tre senaste versionerna av MySQL-drivrutinerna och ansträngningar med författare från communityn för öppen källkod för att ständigt förbättra funktionaliteten och användbarheten hos MySQL-drivrutinerna Fortsätt. En lista med driv rutiner som har testats och visat sig vara kompatibla med Azure Database for MySQL 5,6 och 5,7 finns i följande tabell:

| **Programmeringsspråk** | **Drivrutinen** | **Länkar** | **Kompatibla versioner** | **Inkompatibla versioner** | **Kommentarer** |
| :----------------------- | :--------- | :-------- | :---------------------- | :------------------------ | :-------- |
| PHP | mysqli, pdo_mysql, mysqlnd | https://secure.php.net/downloads.php | 5,5, 5,6, 7. x | 5.3 | För PHP 7,0-anslutning med SSL MySQLi lägger du till MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT i anslutnings strängen. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> SUB set: ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` alternativ till false.|
| .NET | Asynkron MySQL-koppling för .NET | https://github.com/mysql-net/MySqlConnector <br> [Installations paket från NuGet](https://www.nuget.org/packages/MySqlConnector/) | 0,27 och efter | 0.26.5 och före | |
| .NET | MySQL-anslutning/NET | https://github.com/mysql/mysql-connector-net | 6.6.3, 7,0, 8,0 |  | Ett kodnings fel kan orsaka att anslutningar Miss lyckas på vissa icke-UTF8-Windows-system. |
| Node.js | mysqljs | https://github.com/mysqljs/mysql/ <br> Installations paket från NPM:<br> Kör `npm install mysql` från NPM | 2.15 | 2.14.1 och före | |
| Node.js | Node-mysql2 | https://github.com/sidorares/node-mysql2 | 1.3.4 + | | |
| Go | Go MySQL-drivrutin | https://github.com/go-sql-driver/mysql/releases | 1,3, 1,4 | 1,2 och före | Använd `allowNativePasswords=true` i anslutnings strängen för version 1,3. Version 1,4 innehåller en korrigering och `allowNativePasswords=true` krävs inte längre. |
| Python | MySQL Connector/python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2,0, 2,1, 2,2, Använd 8.0.16 + med MySQL 8,0  | 1.2.2 och före | |
| Python | PyMySQL | https://pypi.org/project/PyMySQL/ | 0.7.11, 0.8.0, 0.8.1, 0.9.3 + | 0.9.0 – 0.9.2 (regression i web2py) | |
| Java | MariaDB-anslutning/J | https://downloads.mariadb.org/connector-java/ | 2,1, 2,0, 1,6 | 1.5.5 och före | | 
| Java | MySQL Connector/J | https://github.com/mysql/mysql-connector-j | 5.1.21 +, Använd 8.0.17 + med MySQL 8,0 | 5.1.20 och under | |
| C | MySQL Connector/C (libmysqlclient) | https://dev.mysql.com/doc/refman/5.7/en/c-api-implementations.html | 6.0.2 + | | |
| C | MySQL Connector/ODBC (myodbc) | https://github.com/mysql/mysql-connector-odbc | 3.51.29 + | | |
| C++ | MySQL Connector/C++ | https://github.com/mysql/mysql-connector-cpp | 1.1.9 + | 1.1.3 och under | | 
| C++ | MySQL + +| https://github.com/tangentsoft/mysqlpp | 3.2.3 + | | |
| Ruby | mysql2 | https://github.com/brianmario/mysql2 | 0.4.10 + | | |
| R | RMySQL | https://github.com/rstats-db/RMySQL | 0.10.16 + | | |
| Swift | MySQL – Swift | https://github.com/novi/mysql-swift | 0.7.2 + | | |
| Swift | Vapor/mysql | https://github.com/vapor/mysql-kit | 2.0.1 + | | |

## <a name="management-tools"></a>Hanteringsverktyg
Fördelen med kompatibilitet är också till för databas hanterings verktyg. Dina befintliga verktyg bör fortsätta att fungera med Azure Database for MySQL, så länge databas hanteringen fungerar inom de olika användar behörigheterna. Tre vanliga verktyg för databas hantering som har testats och visat sig vara kompatibla med Azure Database for MySQL 5,6 och 5,7 visas i följande tabell:

|                                     | **MySQL Workbench 6. x och uppåt** | **Navicat 12** | **PHPMyAdmin 4. x och uppåt** | **dbForge Studio för MySQL 9,0** |
| :---------------------------------- | :----------------------------- | :------------- | :-------------------------| :------------------------------- |
| **Skapa, uppdatera, läsa, skriva, ta bort** | X | X | X | X |
| **SSL-anslutning** | X | X | X | X |
| **Automatisk komplettering av SQL-fråga** | X | X |  | X |
| **Importera och exportera data** | X | X | X | X |
| **Exportera till flera format** | X | X | X | X |
| **Säkerhets kopiering och återställning** |  | X |  | X |
| **Visa Server parametrar** | X | X | X | X |
| **Visa klient anslutningar** | X | X | X | X |

## <a name="next-steps"></a>Nästa steg

- [Felsöka anslutningsproblem till Azure Database for MySQL](howto-troubleshoot-common-connection-issues.md)
