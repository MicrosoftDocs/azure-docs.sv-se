---
title: Anslutnings strängar – Azure Database for MySQL
description: I det här dokumentet visas de anslutnings strängar som för närvarande stöds för program att ansluta till Azure Database for MySQL, inklusive ADO.NET (C#), JDBC, Node.js, ODBC, PHP, python och Ruby.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-python, devx-track-js
ms.openlocfilehash: 40af2660f0052a876ef9310bc2426295ba67558b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94541495"
---
# <a name="how-to-connect-applications-to-azure-database-for-mysql"></a>Så ansluter du program till Azure Database for MySQL
I det här avsnittet visas de typer av anslutnings strängar som stöds av Azure Database for MySQL, tillsammans med mallar och exempel. Du kan ha olika parametrar och inställningar i anslutnings strängen.

- Information om hur du skaffar certifikatet finns i [så här konfigurerar du SSL](./howto-configure-ssl.md).
- {your_host} = \<servername> . mysql.Database.Azure.com
- {your_user} @ {servername} = userID-format för autentisering på rätt sätt.  Autentiseringen fungerar inte om du bara använder userID.

## <a name="adonet"></a>ADO.NET
```ado.net
Server={your_host};Port={your_port};Database={your_database};Uid={username@servername};Pwd={your_password};[SslMode=Required;]
```

I det här exemplet är Server namnet `mydemoserver` , databas namnet `wpdb` , användar namnet `WPAdmin` och lösen ordet `mypassword!2` . Därför bör anslutnings strängen vara:

```ado.net
Server= "mydemoserver.mysql.database.azure.com"; Port=3306; Database= "wpdb"; Uid= "WPAdmin@mydemoserver"; Pwd="mypassword!2"; SslMode=Required;
```

## <a name="jdbc"></a>JDBC
```jdbc
String url ="jdbc:mysql://%s:%s/%s[?verifyServerCertificate=true&useSSL=true&requireSSL=true]",{your_host},{your_port},{your_database}"; myDbConn = DriverManager.getConnection(url, {username@servername}, {your_password}";
```

## <a name="nodejs"></a>Node.js
```node.js
var conn = mysql.createConnection({host: {your_host}, user: {username@servername}, password: {your_password}, database: {your_database}, Port: {your_port}[, ssl:{ca:fs.readFileSync({ca-cert filename})}}]);
```

## <a name="odbc"></a>ODBC
```odbc
DRIVER={MySQL ODBC 5.3 UNICODE Driver};Server={your_host};Port={your_port};Database={your_database};Uid={username@servername};Pwd={your_password}; [sslca={ca-cert filename}; sslverify=1; Option=3;]
```

## <a name="php"></a>PHP
```php
$con=mysqli_init(); [mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL);] mysqli_real_connect($con, {your_host}, {username@servername}, {your_password}, {your_database}, {your_port});
```

## <a name="python"></a>Python
```python
cnx = mysql.connector.connect(user={username@servername}, password={your_password}, host={your_host}, port={your_port}, database={your_database}[, ssl_ca={ca-cert filename}, ssl_verify_cert=true])
```

## <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(username: {username@servername}, password: {your_password}, database: {your_database}, host: {your_host}, port: {your_port}[, sslca:{ca-cert filename}, sslverify:false, sslcipher:'AES256-SHA'])
```

## <a name="get-the-connection-string-details-from-the-azure-portal"></a>Hämta information om anslutnings strängen från Azure Portal
Gå till Azure Database for MySQL-servern i [Azure Portal](https://portal.azure.com)och klicka sedan på **anslutnings strängar** för att hämta sträng listan för din instans: :::image type="content" source="./media/howto-connection-strings/connection-strings-on-portal.png" alt-text="fönstret anslutnings strängar i Azure Portal":::

Strängen innehåller information som driv rutin, server och andra databas anslutnings parametrar. Ändra de här exemplen om du vill använda egna parametrar, till exempel databas namn, lösen ord och så vidare. Du kan sedan använda den här strängen för att ansluta till servern från din kod och dina program.

## <a name="next-steps"></a>Nästa steg
- Mer information om anslutnings bibliotek finns i [begrepp – anslutnings bibliotek](./concepts-connection-libraries.md).
