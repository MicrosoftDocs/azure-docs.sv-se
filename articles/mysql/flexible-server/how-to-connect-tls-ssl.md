---
title: Krypterad anslutning med TLS/SSL i Azure Database for MySQL-flexibel Server
description: Instruktioner och information om hur du ansluter med TLS/SSL i Azure Database for MySQL-flexibel Server.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 399cf8087d39f78184cfdae4b9f0e34efecaea66
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491629"
---
# <a name="connect-to-azure-database-for-mysql---flexible-server-with-encrypted-connections"></a>Ansluta till Azure Database for MySQL-flexibel server med krypterade anslutningar

> [!IMPORTANT]
> Azure Database for MySQL flexibel Server är för närvarande en offentlig för hands version

Azure Database for MySQL flexibel Server stöder anslutning av klient program till MySQL-servern med hjälp av Secure Sockets Layer (SSL) med kryptering med Transport Layer Security (TLS). TLS är ett bransch standard protokoll som garanterar krypterade nätverks anslutningar mellan din databas server och klient program, så att du kan följa kraven för efterlevnad.

Azure Database for MySQL flexibel Server stöder krypterade anslutningar med Transport Layer Security (TLS 1,2) som standard och alla inkommande anslutningar med TLS 1,0 och TLS 1,1 kommer att nekas som standard. Konfigurationen av den krypterade anslutningen eller TLS-versionen på din flexibla Server kan ändras enligt beskrivningen i den här artikeln. 

Följande är de olika konfigurationerna av SSL-och TLS-inställningar som du kan använda för din flexibla Server:

| Scenario   | Inställningar för Server parameter      | Beskrivning                                    |
|------------|--------------------------------|------------------------------------------------|
|Inaktivera SSL (krypterade anslutningar) | require_secure_transport = av |Om det äldre programmet inte har stöd för krypterade anslutningar till MySQL server kan du inaktivera tvång av krypterade anslutningar till din flexibla Server genom att ange require_secure_transport = av.|
|Framtvinga SSL med TLS-version < 1,2 | require_secure_transport = ON och tls_version = TLSV1 eller TLSV 1.1| Om ditt äldre program stöder krypterade anslutningar men kräver TLS-version < 1,2, kan du aktivera krypterade anslutningar men konfigurera den flexibla servern så att den tillåter anslutningar med TLS-versionen (v 1.0 eller v 1.1) som stöds av ditt program|
|Framtvinga SSL med TLS-version = 1.2 (standard konfiguration)|require_secure_transport = på och tls_version = TLSV 1.2| Detta är den rekommenderade och standard konfigurationen för flexibel Server.|
|Framtvinga SSL med TLS-version = 1.3 (stöds med MySQL v 8.0 och senare)| require_secure_transport = på och tls_version = TLSV 1.3| Detta är användbart och rekommenderas för nya program utveckling|

> [!Note]
> Ändringar av SSL-chiffer på en flexibel Server stöds inte. FIPS cipher-paket tillämpas som standard när tls_version har angetts till TLS version 1,2. För andra TLS-versioner än version 1,2 anges SSL-chiffer till standardinställningar som medföljer MySQL-programinstallationen.

I den här artikeln får du lära dig att:
* Konfigurera din flexibla Server 
  * Med SSL inaktiverat 
  * Med SSL-framtvingad med TLS-version < 1,2
* Ansluta till din flexibla server med mysql-kommandoraden 
  * Med krypterade anslutningar inaktiverade
  * Med krypterade anslutningar aktiverade
* Verifiera krypterings status för anslutningen
* Ansluta till din flexibla server med krypterade anslutningar med olika program ramverk

## <a name="disable-ssl-on-your-flexible-server"></a>Inaktivera SSL på din flexibla Server
Om klient programmet inte stöder krypterade anslutningar måste du inaktivera tvingande anslutningar för krypterade anslutningar på din flexibla Server. Om du vill inaktivera tvingande av krypterade anslutningar måste du ange require_secure_transport Server parameter till av som visas i skärm bilden och spara Server parameter konfigurationen för att den ska börja gälla. require_secure_transport är en **dynamisk Server-parameter** som börjar gälla omedelbart och inte kräver att servern startas om.

> :::image type="content" source="./media/how-to-connect-tls-ssl/disable-ssl.png" alt-text="Skärm bild som visar hur du inaktiverar SSL med Azure Database for MySQL flexibel Server.":::

### <a name="connect-using-mysql-command-line-client-with-ssl-disabled"></a>Anslut med MySQL kommando rads klient med SSL inaktiverat

I följande exempel visas hur du ansluter till servern med hjälp av kommando rads gränssnittet MySQL. Använd `--ssl-mode=DISABLED` inställningen anslutnings sträng för att inaktivera TLS/SSL-anslutning från mysql-klienten. Ersätt värden med det faktiska Server namnet och lösen ordet. 

```bash
 mysql.exe -h mydemoserver.mysql.database.azure.com -u myadmin -p --ssl-mode=DISABLED 
```
Det är viktigt att Observera att inställningen require_secure_transport inte av innebär att krypterade anslutningar inte stöds på Server sidan. Om du anger require_secure_transport på flexibel server men om klienten ansluter med en krypterad anslutning, kommer den fortfarande att accepteras. Följande anslutning som använder mysql-klienten till en flexibel server som kon figurer ATS med require_secure_transport = OFF fungerar också som visas nedan.

```bash
 mysql.exe -h mydemoserver.mysql.database.azure.com -u myadmin -p --ssl-mode=REQUIRED
```
```output
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 17
Server version: 5.7.29-log MySQL Community Server (GPL)

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> show global variables like '%require_secure_transport%';
+--------------------------+-------+
| Variable_name            | Value |
+--------------------------+-------+
| require_secure_transport | OFF   |
+--------------------------+-------+
1 row in set (0.02 sec)
```

I sammanfattning require_secure_transport = OFF-inställningen sänker tvången av krypterade anslutningar på en flexibel Server och tillåter okrypterade anslutningar till servern från klienten förutom de krypterade anslutningarna.

## <a name="enforce-ssl-with-tls-version--12"></a>Framtvinga SSL med TLS-version < 1,2

Om ditt program stöder anslutningar till MySQL-servern med SSL, men har stöd för TLS-version < 1,2, måste du ange Server parametern för TLS-versioner på den flexibla servern. Om du vill ställa in TLS-versioner som du vill att din flexibla Server ska stödja måste du ange tls_version Server parameter till TLSV1, TLSV 1.1 eller TLSV1 och TLSV 1.1 som visas i skärm bilden och spara Server parameter konfigurationen för att den ska börja gälla. tls_version är en **statisk Server parameter** som kräver att servern startas om för att parametern ska börja gälla.

> :::image type="content" source="./media/how-to-connect-tls-ssl/tls-version.png" alt-text="Skärm bild som visar hur du ställer in TLS-version för en Azure Database for MySQL flexibel Server.":::

## <a name="connect-using-mysql-command-line-client-with-tlsssl"></a>Anslut med MySQL kommando rads klient med TLS/SSL

### <a name="download-the-public-ssl-certificate"></a>Ladda ned det offentliga SSL-certifikatet
Om du vill använda krypterade anslutningar med dina klient program måste du ladda ned det [offentliga SSL-certifikatet](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) som också är tillgängligt i Azure Portal nätverks bladet som visas på skärm bilden nedan.

> :::image type="content" source="./media/how-to-connect-tls-ssl/download-ssl.png" alt-text="Skärm bild som visar hur du hämtar ett offentligt SSL-certifikat från Azure Portal.":::

Spara certifikat filen på önskad plats. I den här självstudien används till exempel `c:\ssl` eller `\var\www\html\bin` i din lokala miljö eller i den klient miljö där programmet finns. Detta gör att program kan ansluta säkert till databasen via SSL.

Om du har skapat din flexibla server med *privat åtkomst (VNet-integrering)* måste du ansluta till servern från en resurs i samma VNet som servern. Du kan skapa en virtuell dator och lägga till den i VNet som skapats med din flexibla Server.

Om du har skapat din flexibla server med *offentlig åtkomst (tillåtna IP-adresser)* kan du lägga till din lokala IP-adress i listan över brand Väggs regler på servern.

Du kan välja antingen [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) eller [MySQL Workbench](./connect-workbench.md)--> för att ansluta till servern från den lokala miljön. 

I följande exempel visas hur du ansluter till servern med hjälp av kommando rads gränssnittet MySQL. Använd `--ssl-mode=REQUIRED` inställningen för anslutnings strängen för att framtvinga verifiering av TLS/SSL-certifikat. Överför sökvägen till den lokala certifikat filen till- `--ssl-ca` parametern. Ersätt värden med det faktiska Server namnet och lösen ordet. 

```bash
sudo apt-get install mysql-client
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl-mode=REQUIRED --ssl-ca=DigiCertGlobalRootCA.crt.pem
```
> [!Note]
> Bekräfta att värdet som skickas till `--ssl-ca` matchar fil Sök vägen för det certifikat som du har sparat.

Om du försöker ansluta till servern med okrypterade anslutningar visas fel meddelande om att anslutningar med hjälp av osäker transport är förbjudna enligt en nedan:

```output
ERROR 3159 (HY000): Connections using insecure transport are prohibited while --require_secure_transport=ON.
```

## <a name="verify-the-tlsssl-connection"></a>Verifiera TLS/SSL-anslutningen

Kör kommandot MySQL **status** för att kontrol lera att du har anslutit till MySQL-servern med TLS/SSL:

```dos
mysql> status
```
Bekräfta att anslutningen är krypterad genom att granska utdata, vilket ska visa: * * SSL: chiffrering som används är * *. Den här chiffersviter visar ett exempel och baserat på klienten, kan du se en annan cipher Suite.

## <a name="connect-to-your-flexible-server-with-encrypted-connections-using-various-application-frameworks"></a>Ansluta till din flexibla server med krypterade anslutningar med olika program ramverk

Anslutnings strängar som är fördefinierade på sidan "anslutnings strängar" som är tillgängliga för servern i Azure Portal innehåller de parametrar som krävs för vanliga språk för att ansluta till din databas server med TLS/SSL. TLS/SSL-parametern varierar beroende på anslutningen. Till exempel "useSSL = true", "sslmode = required" eller "ssl_verify_cert = true" och andra variationer.

För att upprätta en krypterad anslutning till din flexibla Server över TLS/SSL från programmet, se följande kod exempel:

### <a name="wordpress"></a>WordPress
Hämta det [offentliga SSL-certifikatet](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) och Lägg till följande rader i wp-config. php efter raden ```// ** MySQL settings - You can get this info from your web host ** //``` .

```php
//** Connect with SSL** //
define('MYSQL_CLIENT_FLAGS', MYSQLI_CLIENT_SSL);
//** SSL CERT **//
define('MYSQL_SSL_CERT','/FULLPATH/on-client/to/DigiCertGlobalRootCA.crt.pem');
```

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
