---
title: Använd PHP för att fråga
description: Hur du använder PHP för att skapa ett program som ansluter till en databas i Azure SQL Database eller en hanterad Azure SQL-instans och frågar den med hjälp av T-SQL-uttryck.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: php
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 05/29/2020
ms.custom: sqldbrb=2
ms.openlocfilehash: e0ad8e9e71a0ee2c23412d535fe79955edb39ba8
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576658"
---
# <a name="quickstart-use-php-to-query-a-database-in-azure-sql-database"></a>Snabb start: använda PHP för att fråga en databas i Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Den här artikeln visar hur du använder [php](https://php.net/manual/en/intro-whatis.php) för att ansluta till en databas i Azure SQL Database eller en hanterad Azure SQL-instans. Du kan sedan använda T-SQL-instruktioner för att köra frågor mot data.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här snabbstarten behöver du:

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- En databas i Azure SQL Database eller Azure SQL-hanterad instans. Du kan använda någon av följande snabb starter för att skapa och konfigurera en databas:

  | Action | SQL Database | SQL-hanterad instans | SQL Server på virtuella Azure-datorer |
  |:--- |:--- |:---|:---|
  | Skapa| [Portal](single-database-create-quickstart.md) | [Portal](../managed-instance/instance-create-quickstart.md) | [Portal](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [CLI](scripts/create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | Konfigurera | [IP-brandväggsregel på servernivå](firewall-create-server-level-portal-quickstart.md)| [Anslutning från en virtuell dator](../managed-instance/connect-vm-instance-configure.md)|
  |||[Anslutning från lokal plats](../managed-instance/point-to-site-p2s-configure.md) | [Ansluta till en SQL Server-instans](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |Läsa in data|AdventureWorks som lästs in per snabbstart|[Återställa Wide World Importers](../managed-instance/restore-sample-database-quickstart.md) | [Återställa Wide World Importers](../managed-instance/restore-sample-database-quickstart.md) |
  |||Återställa eller importera Adventure Works från en [BACPAC](database-import.md) -fil från [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)| Återställa eller importera Adventure Works från en [BACPAC](database-import.md) -fil från [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||




  > [!IMPORTANT]
  > Skripten i den här artikeln skrivs för att använda Adventure Works-databasen. Med en SQL-hanterad instans måste du antingen importera Adventure Works-databasen till en instans databas eller ändra skripten i den här artikeln för att använda Wide World imports-databasen.

- PHP-relaterad programvara installerad för ditt operativsystem:

  - **MacOS**, installera php, ODBC-drivrutinen och installera php-drivrutinen för SQL Server. Se [Steg 1, 2 och 3](/sql/connect/php/installation-tutorial-linux-mac).

  - **Linux**, installera PHP, ODBC-drivrutinen och därefter PHP-drivrutinen för SQL Server. Se [Steg 1, 2 och 3](/sql/connect/php/installation-tutorial-linux-mac).

  - **Windows**, installera php-och php-drivrutiner och installera sedan ODBC-drivrutinen och SQLCMD. Se [steg 1.2 och 1.3](https://www.microsoft.com/sql-server/developer-get-started/php/windows/).

## <a name="get-server-connection-information"></a>Hämta information om Server anslutning

Hämta anslutnings informationen du behöver för att ansluta till databasen i Azure SQL Database. Du behöver det fullständiga servernamnet eller värdnamnet, databasnamnet och inloggningsinformationen för de kommande procedurerna.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Gå till sidan **SQL-databaser**  eller **SQL-hanterade instanser** .

3. På sidan **Översikt** granskar du det fullständigt kvalificerade Server namnet bredvid **Server namnet** för en databas i Azure SQL Database eller det fullständigt kvalificerade Server namnet (eller IP-adressen) bredvid **värd** för en Azure SQL-hanterad instans eller SQL Server i en virtuell Azure-dator. Om du vill kopiera servernamnet eller värdnamnet hovrar du över det och väljer ikonen **Kopiera**.

> [!NOTE]
> Anslutnings information för SQL Server på den virtuella Azure-datorn finns i [Anslut till en SQL Server instans](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server).

## <a name="add-code-to-query-the-database"></a>Lägg till kod för att fråga databasen

1. Skapa en ny fil, *sqltest.php*, i valfri textredigerare.  

1. Ersätt dess innehåll med följande kod. Lägg sedan till lämpliga värden för servern, databas, användare och lösenord.

   ```PHP
   <?php
       $serverName = "your_server.database.windows.net"; // update me
       $connectionOptions = array(
           "Database" => "your_database", // update me
           "Uid" => "your_username", // update me
           "PWD" => "your_password" // update me
       );
       //Establishes the connection
       $conn = sqlsrv_connect($serverName, $connectionOptions);
       $tsql= "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
            FROM [SalesLT].[ProductCategory] pc
            JOIN [SalesLT].[Product] p
            ON pc.productcategoryid = p.productcategoryid";
       $getResults= sqlsrv_query($conn, $tsql);
       echo ("Reading data from table" . PHP_EOL);
       if ($getResults == FALSE)
           echo (sqlsrv_errors());
       while ($row = sqlsrv_fetch_array($getResults, SQLSRV_FETCH_ASSOC)) {
        echo ($row['CategoryName'] . " " . $row['ProductName'] . PHP_EOL);
       }
       sqlsrv_free_stmt($getResults);
   ?>
   ```

## <a name="run-the-code"></a>Kör koden

1. Kör appen i kommandotolken.

   ```bash
   php sqltest.php
   ```

1. Kontrollera att de 20 översta raderna returneras och stäng appfönstret.

## <a name="next-steps"></a>Nästa steg

- [Utforma din första databas i Azure SQL Database](design-first-database-tutorial.md)
- [Microsoft PHP-drivrutiner för SQL Server](https://github.com/Microsoft/msphpsql/)
- [Rapportera problem eller ställ frågor](https://github.com/Microsoft/msphpsql/issues)
- [Exempel på logik för omprövning: ansluta elastiskt till Azure SQL med PHP](/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)
