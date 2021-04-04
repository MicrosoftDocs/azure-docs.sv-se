---
title: Anslutnings strängar – Azure Database for MariaDB
description: I det här dokumentet visas de anslutnings strängar som för närvarande stöds för program att ansluta till Azure Database for MariaDB, inklusive ADO.NET (C#), JDBC, Node.js, ODBC, PHP, python och Ruby.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
ms.openlocfilehash: 8119f0bfd1f9007cab0df93ad2c7ef22b4db2199
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98662185"
---
# <a name="how-to-connect-applications-to-azure-database-for-mariadb"></a>Så här ansluter du program till Azure Database for MariaDB
I det här avsnittet visas de typer av anslutnings strängar som stöds av Azure Database for MariaDB, tillsammans med mallar och exempel. Du kan ha olika parametrar och inställningar i anslutnings strängen.

- Information om hur du skaffar certifikatet finns i [så här konfigurerar du SSL](./howto-configure-ssl.md).
- {your_host} = [SERVERNAME]. MariaDB. Database. Azure. com
- {your_user} @ {servername} = userID-format för autentisering på rätt sätt.  Autentiseringen fungerar inte om du bara använder userID.

## <a name="adonet"></a>ADO.NET
```csharp
Server={your_host}; Port=3306; Database={your_database}; Uid={username@servername}; Pwd={your_password}; SslMode=Preferred;
```

I det här exemplet är Server namnet `mydemoserver` , databas namnet `wpdb` , användar namnet `WPAdmin` och lösen ordet `mypassword!2` . Därför bör anslutnings strängen vara:

```csharp
Server= "mydemoserver.mariadb.database.azure.com"; Port=3306; Database= "wpdb"; Uid= "WPAdmin@mydemoserver"; Pwd="mypassword!2"; SslMode=Required;
```

## <a name="jdbc"></a>JDBC
```java
String url ="jdbc:mariadb://{your_host}:3306/{your_database}?useSSL=true&trustServerCertificate=true"; myDbConn = DriverManager.getConnection(url, "{username@servername}", {your_password});
```

## <a name="nodejs"></a>Node.js
```javascript
var conn = mysql.createConnection({host: "{your_host}", user: "{your_username}", password: {your_password}, database: {your_database}, port: 3306, ssl:{ca:fs.readFileSync({ca-cert filename})}});
```

## <a name="odbc"></a>ODBC
```cpp
DRIVER={MARIADB ODBC 3.0 Driver}; Server="{your_host}"; Port=3306; Database={your_database}; Uid="{username@servername}"; Pwd={your_password}; sslca={ca-cert filename}; sslverify=1;
```

## <a name="php"></a>PHP
```php
$con=mysqli_init(); mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL); mysqli_real_connect($con, "{your_host}", "{username@servername}", {your_password}, {your_database}, 3306);
```

## <a name="python"></a>Python
```python
cnx = mysql.connector.connect(user="{username@servername}", password={your_password}, host="{your_host}", port=3306, database={your_database}, ssl_ca={ca-cert filename}, ssl_verify_cert=true)
```

## <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(username: "{username@servername}", password: {your_password}, database: {your_database}, host: "{your_host}", port: 3306, sslca:{ca-cert filename}, sslverify:false, sslcipher:'AES256-SHA')
```

## <a name="get-the-connection-string-details-from-the-azure-portal"></a>Hämta information om anslutnings strängen från Azure Portal
Gå till Azure Database for MariaDB-servern i [Azure Portal](https://portal.azure.com)och klicka sedan på **anslutnings strängar** för att hämta sträng listan för din instans: ![ fönstret anslutnings strängar i Azure Portal](./media/howto-connection-strings/connection-strings-on-portal.png)

Strängen innehåller information som driv rutin, server och andra databas anslutnings parametrar. Ändra de här exemplen om du vill använda egna parametrar, till exempel databas namn, lösen ord och så vidare. Du kan sedan använda den här strängen för att ansluta till servern från din kod och dina program.

<!-- 
## Next steps
- For more information about connection libraries, see [Concepts - Connection libraries](./concepts-connection-libraries.md).
- -->
