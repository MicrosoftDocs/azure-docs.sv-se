---
title: Krypterad anslutning med TLS/SSL i Azure Database for MySQL-flexibel Server
description: Instruktioner och information om hur du ansluter med TLS/SSL i Azure Database for MySQL-flexibel Server.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 24a8dd4d21cb6ab6edeb985db4e6e6a1349a758d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90942045"
---
# <a name="encrypted-connectivity-using-transport-layer-security-tls-12-in-azure-database-for-mysql---flexible-server"></a>Krypterad anslutning med Transport Layer Security (TLS 1,2) i Azure Database for MySQL-flexibel Server

> [!IMPORTANT]
> Azure Database for MySQL flexibel Server är för närvarande en offentlig för hands version

Azure Database for MySQL flexibla servern stöder anslutning av klient program till MySQL-tjänsten med hjälp av Transport Layer Security (TLS), tidigare kallat Secure Sockets Layer (SSL). TLS är ett bransch standard protokoll som garanterar krypterade nätverks anslutningar mellan din databas server och klient program, så att du kan följa kraven för efterlevnad.

Azure Database for MySQL flexibla servern stöder bara krypterade anslutningar med Transport Layer Security (TLS 1,2) och alla inkommande anslutningar med TLS 1,0 och TLS 1,1 kommer att nekas. För alla flexibla servrar är tvingande av TLS-anslutningar aktiverat och du kan inte inaktivera TLS/SSL för anslutning till flexibel Server.

## <a name="applications-that-require-certificate-verification-for-tlsssl-connectivity"></a>Program som kräver certifikat verifiering för TLS/SSL-anslutning
I vissa fall kräver program en lokal certifikat fil som genereras från en certifikat utfärdare för betrodda certifikat utfärdare för att ansluta säkert. Azure Database for MySQL flexibel Server använder *DigiCert global rot certifikat utfärdare*. Hämta det här certifikatet som krävs för att kommunicera via SSL från [DigiCert global rot certifikat utfärdare](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) och spara certifikat filen på önskad plats. I den här självstudien används till exempel `c:\ssl` .

### <a name="connect-using-mysql-command-line-client-with-tlsssl"></a>Anslut med MySQL kommando rads klient med TLS/SSL

Om du har skapat din flexibla server med *privat åtkomst (VNet-integrering)* måste du ansluta till servern från en resurs i samma VNet som servern. Du kan skapa en virtuell dator och lägga till den i VNet som skapats med din flexibla Server.

Om du har skapat din flexibla server med *offentlig åtkomst (tillåtna IP-adresser)* kan du lägga till din lokala IP-adress i listan över brand Väggs regler på servern.

Du kan välja antingen [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) eller [MySQL Workbench](./connect-workbench.md)--> för att ansluta till servern från den lokala miljön. 

I följande exempel visas hur du ansluter till servern med hjälp av kommando rads gränssnittet MySQL. Använd `--ssl-mode=REQUIRED` inställningen för anslutnings strängen för att framtvinga verifiering av TLS/SSL-certifikat. Överför sökvägen till den lokala certifikat filen till- `--ssl-ca` parametern. Ersätt värden med det faktiska Server namnet och lösen ordet. 

```bash
 mysql.exe -h mydemoserver.mysql.database.azure.com -u myadmin -p --ssl-mode=REQUIRED --ssl-ca=c:\ssl\DigiCertGlobalRootCA.crt.pem
```
> [!Note]
> Bekräfta att värdet som skickas till `--ssl-ca` matchar fil Sök vägen för det certifikat som du har sparat.

### <a name="verify-the-tlsssl-connection"></a>Verifiera TLS/SSL-anslutningen

Kör kommandot MySQL **status** för att kontrol lera att du har anslutit till MySQL-servern med TLS/SSL:

```dos
mysql> status
```
Bekräfta att anslutningen är krypterad genom att granska utdata som ska visas:  **SSL: chiffrering som används är AES256-SHA**. Den här chiffersviter visar ett exempel och baserat på klienten, kan du se en annan cipher Suite.

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Se till att ditt program eller ramverk stöder TLS-anslutningar

Vissa program ramverk som använder MySQL för sina databas tjänster aktiverar inte TLS som standard under installationen. MySQL-servern framtvingar TLS-anslutningar, men om programmet inte har kon figurer ATS för TLS kan det hända att programmet inte kan ansluta till din databas server. Mer information om hur du aktiverar TLS-anslutningar finns i programmets dokumentation.

## <a name="sample-code"></a>Exempelkod
Anslutnings strängar som är fördefinierade på sidan "anslutnings strängar" som är tillgängliga för servern i Azure Portal innehåller de parametrar som krävs för vanliga språk för att ansluta till din databas server med TLS/SSL. TLS/SSL-parametern varierar beroende på anslutningen. Till exempel "useSSL = true", "sslmode = required" eller "ssl_verify_cert = true" och andra variationer.

För att upprätta en krypterad anslutning till din flexibla Server över TLS/SSL från programmet, se följande kod exempel:

### <a name="php"></a>PHP

```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/DigiCertGlobalRootCA.crt.pem", NULL, NULL);
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```

### <a name="php-using-pdo"></a>PHP (med sub)

```phppdo
$options = array(
    PDO::MYSQL_ATTR_SSL_CA => '/var/www/html/DigiCertGlobalRootCA.crt.pem'
);
$db = new PDO('mysql:host=mydemoserver.mysql.database.azure.com;port=3306;dbname=databasename', 'myadmin', 'yourpassword', $options);
```

### <a name="python-mysqlconnector-python"></a>Python (MySQLConnector python)

```python
try:
    conn = mysql.connector.connect(user='myadmin',
                                   password='yourpassword',
                                   database='quickstartdb',
                                   host='mydemoserver.mysql.database.azure.com',
                                   ssl_ca='/var/www/html/DigiCertGlobalRootCA.crt.pem')
except mysql.connector.Error as err:
    print(err)
```

### <a name="python-pymysql"></a>Python (PyMySQL)

```python
conn = pymysql.connect(user='myadmin',
                       password='yourpassword',
                       database='quickstartdb',
                       host='mydemoserver.mysql.database.azure.com',
                       ssl={'ca': '/var/www/html/DigiCertGlobalRootCA.crt.pem'})
```

### <a name="django-pymysql"></a>Django (PyMySQL)

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'quickstartdb',
        'USER': 'myadmin',
        'PASSWORD': 'yourpassword',
        'HOST': 'mydemoserver.mysql.database.azure.com',
        'PORT': '3306',
        'OPTIONS': {
            'ssl': {'ca': '/var/www/html/DigiCertGlobalRootCA.crt.pem'}
        }
    }
}
```

### <a name="ruby"></a>Ruby

```ruby
client = Mysql2::Client.new(
        :host     => 'mydemoserver.mysql.database.azure.com',
        :username => 'myadmin',
        :password => 'yourpassword',
        :database => 'quickstartdb',
        :sslca => '/var/www/html/DigiCertGlobalRootCA.crt.pem'
    )
```

### <a name="golang"></a>Golang

```go
rootCertPool := x509.NewCertPool()
pem, _ := ioutil.ReadFile("/var/www/html/DigiCertGlobalRootCA.crt.pem")
if ok := rootCertPool.AppendCertsFromPEM(pem); !ok {
    log.Fatal("Failed to append PEM.")
}
mysql.RegisterTLSConfig("custom", &tls.Config{RootCAs: rootCertPool})
var connectionString string
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin' , 'yourpassword', 'mydemoserver.mysql.database.azure.com', 'quickstartdb')
db, _ := sql.Open("mysql", connectionString)
```

### <a name="java-mysql-connector-for-java"></a>Java (MySQL Connector för Java)

```java
# generate truststore and keystore in code
String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " +
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " +
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore
System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mysql://%s/%s?serverTimezone=UTC&useSSL=true", 'mydemoserver.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="java-mariadb-connector-for-java"></a>Java (MariaDB Connector för Java)

```java
# generate truststore and keystore in code
String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " +
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " +
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore
System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mariadb://%s/%s?useSSL=true&trustServerCertificate=true", 'mydemoserver.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="net-mysqlconnector"></a>.NET (MySqlConnector)

```csharp
var builder = new MySqlConnectionStringBuilder
{
    Server = "mydemoserver.mysql.database.azure.com",
    UserID = "myadmin",
    Password = "yourpassword",
    Database = "quickstartdb",
    SslMode = MySqlSslMode.VerifyCA,
    SslCa = "DigiCertGlobalRootCA.crt.pem",
};
using (var connection = new MySqlConnection(builder.ConnectionString))
{
    connection.Open();
}
```

## <a name="next-steps"></a>Nästa steg
- [Använd MySQL Workbench för att ansluta och fråga efter data i Azure Database for MySQL flexibel Server](./connect-workbench.md)
- [Använda PHP för att ansluta och fråga efter data i Azure Database for MySQL flexibel Server](./connect-php.md)
- [Skapa och hantera Azure Database for MySQL flexibla Server virtuella nätverk med Azure CLI](./how-to-manage-virtual-network-cli.md).
- Lär dig mer om [nätverk i Azure Database for MySQL flexibel Server](./concepts-networking.md)
- Lär dig mer om [Azure Database for MySQL flexibla Server brand Väggs regler](./concepts-networking.md#public-access-allowed-ip-addresses)
