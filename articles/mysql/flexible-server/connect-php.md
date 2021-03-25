---
title: 'Snabb start: ansluta med PHP-Azure Database for MySQL – flexibel Server'
description: Den här snabb starten innehåller flera exempel på PHP-kod som du kan använda för att ansluta och fråga efter data från Azure Database for MySQL-flexibel Server.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 9/21/2020
ms.openlocfilehash: baba4d373d4a79ab0c339aac00bb9ab48de9262b
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2021
ms.locfileid: "105109615"
---
# <a name="quickstart-use-php-to-connect-and-query-data-in-azure-database-for-mysql---flexible-server"></a>Snabb start: använda PHP för att ansluta och fråga efter data i Azure Database for MySQL-flexibel Server

> [!IMPORTANT]
> Azure Database for MySQL-flexibel Server är för närvarande en offentlig för hands version.

Den här snabb starten visar hur du ansluter till en Azure Database for MySQL flexibel server med ett [php](https://secure.php.net/manual/intro-whatis.php) -program. Den visar hur du använder SQL-instruktioner för att fråga, infoga, uppdatera och ta bort data i databasen. Den här artikeln förutsätter att du är van att utveckla med PHP och att du inte har arbetat med Azure Database for MySQL flexibel Server.

## <a name="prerequisites"></a>Förutsättningar
I den här snabbstarten används de resurser som skapades i någon av följande guider som utgångspunkt:

- [Skapa en Azure Database for MySQL flexibel server med Azure Portal](./quickstart-create-server-portal.md)
- [Skapa en Azure Database for MySQL flexibel server med Azure CLI](./quickstart-create-server-cli.md)

## <a name="preparing-your-client-workstation"></a>Förbereda klient arbets stationen
1. Om du har skapat din flexibla server med *privat åtkomst (VNet-integrering)* måste du ansluta till servern från en resurs i samma VNet som servern. Du kan skapa en virtuell dator och lägga till den i VNet som skapats med din flexibla Server. Se [skapa och hantera Azure Database for MySQL flexibla Server virtuella nätverk med Azure CLI](./how-to-manage-virtual-network-cli.md).

2. Om du har skapat din flexibla server med *offentlig åtkomst (tillåtna IP-adresser)* kan du lägga till din lokala IP-adress i listan över brand Väggs regler på servern. Se [skapa och hantera Azure Database for MySQL flexibla Server brand Väggs regler med hjälp av Azure CLI](./how-to-manage-firewall-cli.md).

### <a name="install-php"></a>Installera PHP

Installera PHP på din server, eller skapa en Azure-[webbapp](../../app-service/overview.md) som innehåller PHP.  Se [skapa och hantera brand Väggs regler](./how-to-manage-firewall-portal.md) för att lära dig hur du skapar brand Väggs regler.

#### <a name="macos"></a>macOS

1. Hämta [PHP 7.1.4](https://secure.php.net/downloads.php).
2. Installera PHP och se php- [handboken](https://secure.php.net/manual/install.macosx.php) för ytterligare konfiguration.

#### <a name="linux-ubuntu"></a>Linux (Ubuntu)

1. Hämta [php-7.1.4 (x64) version som inte är tråd säker (x64)](https://secure.php.net/downloads.php).
2. Installera PHP och se php- [handboken](https://secure.php.net/manual/install.unix.php) för ytterligare konfiguration.

#### <a name="windows"></a>Windows

1. Hämta [php-7.1.4 (x64) version som inte är tråd säker (x64)](https://windows.php.net/download#php-7.1).
2. Installera PHP och se php- [handboken](https://secure.php.net/manual/install.windows.php) för ytterligare konfiguration.

## <a name="get-connection-information"></a>Hämta anslutningsinformation

Hämta anslutnings informationen som behövs för att ansluta till den Azure Database for MySQL flexibla servern. Du behöver det fullständigt kvalificerade Server namnet och inloggnings uppgifterna.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. På den vänstra menyn i Azure Portal väljer du **alla resurser** och söker sedan efter den server som du har skapat (till exempel **mydemoserver**).
3. Välj servernamnet.
4. På serverpanelen **Översikt** antecknar du **Servernamn** och **Inloggningsnamn för serveradministratören**. Om du glömmer lösenordet kan du även återställa det på den här panelen.
 <!---:::image type="content" source="./media/connect-php/1_server-overview-name-login.png" alt-text="Azure Database for MySQL Flexible Server name":::--->

## <a name="connecting-to-flexible-server-using-tlsssl-in-php"></a>Ansluta till flexibel server med TLS/SSL i PHP

För att upprätta en krypterad anslutning till din flexibla Server över TLS/SSL från programmet, se följande kod exempel. Du kan hämta det certifikat som krävs för att kommunicera via TLS/SSL från [https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)

```php using SSL
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/DigiCertGlobalRootCA.crt.pem", NULL, NULL);
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```

## <a name="connect-and-create-a-table"></a>Ansluta och skapa en tabell

Använd följande kod för att ansluta och skapa en tabell med hjälp av SQL-instruktionen **CREATE TABLE**.

Koden använder klassen **MySQL Improved extension** (mysqli) som ingår i PHP. Metoderna [mysqli_init](https://secure.php.net/manual/mysqli.init.php) och [mysqli_real_connect](https://secure.php.net/manual/mysqli.real-connect.php) anropas för att ansluta till MySQL. Sedan anropas metoden [mysqli_query](https://secure.php.net/manual/mysqli.query.php) för att köra frågan. Sedan anropas metoden [mysqli_close](https://secure.php.net/manual/mysqli.close.php) för att stänga anslutningen.

Ersätt parametrarna host, username, password och db_name med dina egna värden.

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

// Run the create table query
if (mysqli_query($conn, '
CREATE TABLE Products (
`Id` INT NOT NULL AUTO_INCREMENT ,
`ProductName` VARCHAR(200) NOT NULL ,
`Color` VARCHAR(50) NOT NULL ,
`Price` DOUBLE NOT NULL ,
PRIMARY KEY (`Id`)
);
')) {
printf("Table created\n");
}

//Close the connection
mysqli_close($conn);
?>
```

## <a name="insert-data"></a>Infoga data

Använd följande kod för att ansluta och infoga data med en **INSERT**-SQL-instruktion.

Koden använder klassen **MySQL Improved extension** (mysqli) som ingår i PHP. I koden används metoden [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php) för att skapa en förberedd insert-instruktion och sedan binds parametrarna för varje infogat kolumnvärde med metoden [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php). Sedan körs instruktionen med metoden [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php) och efteråt stängs instruktionen med metoden [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php).

Ersätt parametrarna host, username, password och db_name med dina egna värden.

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Create an Insert prepared statement and run it
$product_name = 'BrandNewProduct';
$product_color = 'Blue';
$product_price = 15.5;
if ($stmt = mysqli_prepare($conn, "INSERT INTO Products (ProductName, Color, Price) VALUES (?, ?, ?)")) {
mysqli_stmt_bind_param($stmt, 'ssd', $product_name, $product_color, $product_price);
mysqli_stmt_execute($stmt);
printf("Insert: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
mysqli_stmt_close($stmt);
}

// Close the connection
mysqli_close($conn);
?>
```

## <a name="read-data"></a>Läsa data

Använd följande kod för att ansluta och läsa data med en **SELECT**-SQL-instruktion.  Koden använder klassen **MySQL Improved extension** (mysqli) som ingår i PHP. Metoden [mysqli_query](https://secure.php.net/manual/mysqli.query.php) används för att utföra sql-frågan och metoden [mysqli_fetch_assoc](https://secure.php.net/manual/mysqli-result.fetch-assoc.php) används för att hämta de resulterande raderna.

Ersätt parametrarna host, username, password och db_name med dina egna värden.

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Run the Select query
printf("Reading data from table: \n");
$res = mysqli_query($conn, 'SELECT * FROM Products');
while ($row = mysqli_fetch_assoc($res)) {
var_dump($row);
}

//Close the connection
mysqli_close($conn);
?>
```

## <a name="update-data"></a>Uppdatera data

Använd följande kod för att ansluta och uppdatera data med en **UPDATE**-SQL-instruktion.

Koden använder klassen **MySQL Improved extension** (mysqli) som ingår i PHP. Metoden [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php) används för att skapa en förberedd update-instruktion och sedan binds parametrarna för varje uppdaterat kolumnvärde med metoden [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php). Sedan körs instruktionen med metoden [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php) och efteråt stängs instruktionen med metoden [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php).

Ersätt parametrarna host, username, password och db_name med dina egna värden.

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Run the Update statement
$product_name = 'BrandNewProduct';
$new_product_price = 15.1;
if ($stmt = mysqli_prepare($conn, "UPDATE Products SET Price = ? WHERE ProductName = ?")) {
mysqli_stmt_bind_param($stmt, 'ds', $new_product_price, $product_name);
mysqli_stmt_execute($stmt);
printf("Update: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));

//Close the connection
mysqli_stmt_close($stmt);
}

mysqli_close($conn);
?>
```


## <a name="delete-data"></a>Ta bort data
Använd följande kod för att ansluta och läsa data med en **DELETE**-SQL-instruktion.

Koden använder klassen **MySQL Improved extension** (mysqli) som ingår i PHP. Metoden [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php) används för att skapa en förberedd delete-instruktion och sedan binds parametrarna för where-satsen med metoden [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php). Sedan körs instruktionen med metoden [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php) och efteråt stängs instruktionen med metoden [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php).

Ersätt parametrarna host, username, password och db_name med dina egna värden.

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Run the Delete statement
$product_name = 'BrandNewProduct';
if ($stmt = mysqli_prepare($conn, "DELETE FROM Products WHERE ProductName = ?")) {
mysqli_stmt_bind_param($stmt, 's', $product_name);
mysqli_stmt_execute($stmt);
printf("Delete: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
mysqli_stmt_close($stmt);
}

//Close the connection
mysqli_close($conn);
?>
```
## <a name="next-steps"></a>Nästa steg
- [Krypterad anslutning med Transport Layer Security (TLS 1,2) i Azure Database for MySQL-flexibel Server](./how-to-connect-tls-ssl.md).
- Lär dig mer om [nätverk i Azure Database for MySQL flexibel Server](./concepts-networking.md).
- [Skapa och hantera Azure Database for MySQL flexibla Server brand Väggs regler med hjälp av Azure Portal](./how-to-manage-firewall-portal.md).
- [Skapa och hantera Azure Database for MySQL flexibla Server virtuella nätverk med Azure Portal](./how-to-manage-virtual-network-portal.md).