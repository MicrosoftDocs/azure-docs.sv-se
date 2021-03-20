---
title: 'Snabb start: Anslut med PHP-Azure Database for MySQL'
description: I den här snabbstarten finns ett kodexempel i PHP som du kan använda för att ansluta till och fråga efter data från Azure Database för MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 10/28/2020
ms.openlocfilehash: bf62eca26d846cc529df43b519b1377f88c8aede
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98132910"
---
# <a name="quickstart-use-php-to-connect-and-query-data-in-azure-database-for-mysql"></a>Snabb start: använda PHP för att ansluta och fråga efter data i Azure Database for MySQL
Den här snabbstarten visar hur du ansluter till en Azure Database för MySQL med hjälp av ett [PHP](https://secure.php.net/manual/intro-whatis.php)-program. Den visar hur du använder SQL-instruktioner för att fråga, infoga, uppdatera och ta bort data i databasen.

## <a name="prerequisites"></a>Förutsättningar
För den här snabb starten behöver du:

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free).
- Skapa en Azure Database for MySQL enskild server med [Azure Portal](./quickstart-create-mysql-server-database-using-azure-portal.md) <br/> eller [Azure CLI](./quickstart-create-mysql-server-database-using-azure-cli.md) om du inte har något.
- Baserat på om du använder offentlig eller privat åtkomst utför du **en** av åtgärderna nedan för att aktivera anslutningen.

    |Action| Anslutningsmetod|Instruktionsguide|
    |:--------- |:--------- |:--------- |
    | **Konfigurera brandväggsregler** | Offentliga | [Portal](./howto-manage-firewall-using-portal.md) <br/> [CLI](./howto-manage-firewall-using-cli.md)|
    | **Konfigurera tjänstens slut punkt** | Offentliga | [Portal](./howto-manage-vnet-using-portal.md) <br/> [CLI](./howto-manage-vnet-using-cli.md)|
    | **Konfigurera privat länk** | Privat | [Portal](./howto-configure-privatelink-portal.md) <br/> [CLI](./howto-configure-privatelink-cli.md) |

- [Skapa en databas och icke-administratörs användare](./howto-create-users.md?tabs=single-server)
- Installera den senaste PHP-versionen för ditt operativ system
    - [PHP på macOS](https://secure.php.net/manual/install.macosx.php)
    - [PHP på Linux](https://secure.php.net/manual/install.unix.php)
    - [PHP på Windows](https://secure.php.net/manual/install.windows.php)

> [!NOTE]
> Vi använder [mysqli](https://www.php.net/manual/en/book.mysqli.php) -biblioteket för att hantera anslutning och fråga servern i den här snabb starten.

## <a name="get-connection-information"></a>Hämta anslutningsinformation
Du kan hämta anslutnings informationen för databas servern från Azure Portal genom att följa dessa steg:

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Gå till sidan Azure-databaser för MySQL. Du kan söka efter och välja **Azure Database for MySQL**.
:::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/find-azure-mysql-in-portal.png" alt-text="Hitta Azure Database for MySQL":::

2. Välj MySQL-server (t. ex. **mydemoserver**).
3. På sidan **Översikt** kopierar du det fullständigt kvalificerade Server namnet bredvid **Server namn** och administratörs användar namnet bredvid **inloggnings namnet för Server administratören**. Om du vill kopiera servernamnet eller värdnamnet hovrar du över det och väljer ikonen **Kopiera**.

> [!IMPORTANT]
> - Om du har glömt ditt lösen ord kan du [återställa lösen ordet](./howto-create-manage-server-portal.md#update-admin-password).
> - Ersätt **värden, användar namn, lösen ord** och **DB_NAME** parametrar med dina egna värden * *

## <a name="step-1-connect-to-the-server"></a>Steg 1: Anslut till servern
SSL är aktiverat som standard. Du kan behöva hämta [DIGICERTGLOBALROOTG2 SSL-certifikatet](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) för att kunna ansluta från den lokala miljön. Den här koden anropar:
- [mysqli_init](https://secure.php.net/manual/mysqli.init.php) att initiera mysqli.
- [mysqli_ssl_set](https://www.php.net/manual/en/mysqli.ssl-set.php) att peka på SSL-certifikatets sökväg. Detta krävs för din lokala miljö men krävs inte för App Service webbappar eller virtuella Azure-datorer.
- [mysqli_real_connect](https://secure.php.net/manual/mysqli.real-connect.php) att ansluta till MySQL.
- [mysqli_close](https://secure.php.net/manual/mysqli.close.php) att stänga anslutningen.


```php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin@mydemoserver';
$password = 'your_password';
$db_name = 'your_database';

//Initializes MySQLi
$conn = mysqli_init();

mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/DigiCertGlobalRootG2.crt.pem", NULL, NULL);

// Establish the connection
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin@mydemoserver', 'yourpassword', 'quickstartdb', 3306, NULL, MYSQLI_CLIENT_SSL);

//If connection failed, show the error
if (mysqli_connect_errno($conn))
{
    die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```
[Har du problem? Berätta för oss](https://aka.ms/mysql-doc-feedback)

## <a name="step-2-create-a-table"></a>Steg 2: skapa en tabell
Använd följande kod för att ansluta. Den här koden anropar:
- [mysqli_query](https://secure.php.net/manual/mysqli.query.php) att köra frågan.
```php
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
```

## <a name="step-3-insert-data"></a>Steg 3: infoga data
Använd följande kod för att infoga data med en **insert** -SQL-instruktion. I den här koden används metoderna:
- [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php) att skapa en för beredd INSERT-instruktion
- [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php) att binda parametrarna för varje infogat kolumn värde.
- [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php)
- [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php) att stänga instruktionen med hjälp av metoden


```php
//Create an Insert prepared statement and run it
$product_name = 'BrandNewProduct';
$product_color = 'Blue';
$product_price = 15.5;
if ($stmt = mysqli_prepare($conn, "INSERT INTO Products (ProductName, Color, Price) VALUES (?, ?, ?)"))
{
    mysqli_stmt_bind_param($stmt, 'ssd', $product_name, $product_color, $product_price);
    mysqli_stmt_execute($stmt);
    printf("Insert: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
    mysqli_stmt_close($stmt);
}

```

## <a name="step-4-read-data"></a>Steg 4: läsa data
Använd följande kod för att läsa data med hjälp av ett **Select** SQL-uttryck.  I koden används metoden:
- [mysqli_query](https://secure.php.net/manual/mysqli.query.php) köra **urvals** frågan
- [mysqli_fetch_assoc](https://secure.php.net/manual/mysqli-result.fetch-assoc.php) att hämta de resulterande raderna.

```php
//Run the Select query
printf("Reading data from table: \n");
$res = mysqli_query($conn, 'SELECT * FROM Products');
while ($row = mysqli_fetch_assoc($res))
 {
    var_dump($row);
 }

```


## <a name="step-5-delete-data"></a>Steg 5: ta bort data
Använd följande kod ta bort rader med en **Delete** -SQL-instruktion. I koden används metoderna:
- [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php) att skapa en för beredd Delete-instruktion
- [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php) binder parametrarna
- [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php) kör instruktionen för för beredda borttagningar
- [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php) stänger instruktionen

```php
//Run the Delete statement
$product_name = 'BrandNewProduct';
if ($stmt = mysqli_prepare($conn, "DELETE FROM Products WHERE ProductName = ?")) {
mysqli_stmt_bind_param($stmt, 's', $product_name);
mysqli_stmt_execute($stmt);
printf("Delete: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
mysqli_stmt_close($stmt);
}
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa alla resurser som används under den här snabb starten tar du bort resurs gruppen med hjälp av följande kommando:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Hantera Azure Database for MySQL server med hjälp av portalen](./howto-create-manage-server-portal.md)<br/>

> [!div class="nextstepaction"]
> [Hantera Azure Database for MySQL server med CLI](./how-to-manage-single-server-cli.md)

[Hittar du inte det du letar efter? Berätta för oss.](https://aka.ms/mysql-doc-feedback)
