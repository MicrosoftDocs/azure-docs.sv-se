---
title: Konfigurera SSL – Azure Database for MySQL
description: Instruktioner för hur du konfigurerar Azure Database for MySQL och associerade program korrekt för att använda SSL-anslutningar
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 07/08/2020
ms.custom: devx-track-python, devx-track-csharp
ms.openlocfilehash: 1c3e5a44e01f3fa43b82644103066f5a03684ad2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94591588"
---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mysql"></a>Konfigurera SSL-anslutning i ditt program för att säkert ansluta till Azure Database for MySQL

Azure Database for MySQL stöder anslutning av Azure Database for MySQL-servern till klient program med hjälp av Secure Sockets Layer (SSL). Framtvingande av SSL-anslutningar mellan databasservern och klientprogrammen hjälper till att skydda mot ”man in the middle”-attacker genom att kryptera dataströmmen mellan servern och programmet.

## <a name="step-1-obtain-ssl-certificate"></a>Steg 1: Hämta SSL-certifikat

Hämta det certifikat som krävs för att kommunicera via SSL med din Azure Database for MySQL [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) -Server från och spara certifikat filen på den lokala enheten (i den här självstudien används c:\ssl till exempel).
**För Microsoft Internet Explorer och Microsoft Edge:** När hämtningen är klar byter du namn på certifikatet till BaltimoreCyberTrustRoot. CRT. pem.

>[!NOTE]
> Baserat på feedback från kunder har vi utökat rot certifikatets utfasning för vår befintliga Baltimore rot certifikat utfärdare till 15 februari 2021 (02/15/2021).

> [!IMPORTANT] 
> SSL-rotcertifikat är inställt på att upphöra att gälla den 15 februari 2021 (02/15/2021). Uppdatera ditt program för att använda det [nya certifikatet](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem). Mer information finns i avsnittet om [planerade certifikat uppdateringar](concepts-certificate-rotation.md)

Se följande länkar för certifikat för servrar i suveräna moln: [Azure Government](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem), [Azure Kina](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)och [Azure Germany](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt).

## <a name="step-2-bind-ssl"></a>Steg 2: bind SSL

För specifika anslutnings strängar för programmeringsspråk, se [exempel koden](howto-configure-ssl.md#sample-code) nedan.

### <a name="connecting-to-server-using-mysql-workbench-over-ssl"></a>Ansluta till servern med MySQL Workbench över SSL

Konfigurera MySQL Workbench för att ansluta säkert över SSL.

1. I dialog rutan skapa ny anslutning navigerar du till fliken **SSL** .

1. Uppdatera fältet **Använd SSL** till "kräver".

1. I fältet **SSL-ca-fil:** anger du sökvägen till filen **BaltimoreCyberTrustRoot. CRT. pem**.

   :::image type="content" source="./media/howto-configure-ssl/mysql-workbench-ssl.png" alt-text="Spara SSL-konfiguration":::

För befintliga anslutningar kan du binda SSL genom att högerklicka på anslutnings ikonen och välja Redigera. Gå sedan till fliken **SSL** och bind certifikat filen.

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>Ansluta till servern med hjälp av MySQL CLI över SSL

Ett annat sätt att binda SSL-certifikatet är att använda kommando rads gränssnittet MySQL genom att köra följande kommandon.

```bash
mysql.exe -h mydemoserver.mysql.database.azure.com -u Username@mydemoserver -p --ssl-mode=REQUIRED --ssl-ca=c:\ssl\BaltimoreCyberTrustRoot.crt.pem
```

> [!NOTE]
> När du använder kommando rads gränssnittet MySQL i Windows kan du få ett fel meddelande `SSL connection error: Certificate signature check failed` . Om detta inträffar ersätter du `--ssl-mode=REQUIRED --ssl-ca={filepath}` parametrarna med `--ssl` .

## <a name="step-3--enforcing-ssl-connections-in-azure"></a>Steg 3: tvinga SSL-anslutningar i Azure

### <a name="using-the-azure-portal"></a>Använda Azure Portal

Använd Azure Portalen, gå till Azure Database for MySQL-servern och klicka sedan på **anslutnings säkerhet**. Använd växlings knappen för att aktivera eller inaktivera inställningen **tvinga SSL-anslutning** och klicka sedan på **Spara**. Microsoft rekommenderar att alltid aktivera inställningen **tvinga SSL-anslutning** för förbättrad säkerhet.

:::image type="content" source="./media/howto-configure-ssl/enable-ssl.png" alt-text="Skärm bild av Azure Portal att använda SSL-anslutningar i Azure Database for MySQL":::

### <a name="using-azure-cli"></a>Använda Azure CLI

Du kan aktivera eller inaktivera **SSL-tvångs** parametern genom att använda aktiverade eller inaktiverade värden i Azure CLI.

```azurecli-interactive
az mysql server update --resource-group myresource --name mydemoserver --ssl-enforcement Enabled
```

## <a name="step-4-verify-the-ssl-connection"></a>Steg 4: kontrol lera SSL-anslutningen

Kör kommandot MySQL **status** för att kontrol lera att du har anslutit till MySQL-servern med hjälp av SSL:

```dos
mysql> status
```

Bekräfta att anslutningen är krypterad genom att granska utdata som ska visas:  **SSL: chiffrering som används är AES256-SHA**

## <a name="sample-code"></a>Exempelkod

Om du vill upprätta en säker anslutning till Azure Database for MySQL via SSL från ditt program, se följande kod exempel:

Se listan över [kompatibla driv rutiner](concepts-compatibility.md) som stöds av tjänsten Azure Database for MySQL.

### <a name="php"></a>PHP

```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/BaltimoreCyberTrustRoot.crt.pem", NULL, NULL);
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin@mydemoserver', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```

### <a name="php-using-pdo"></a>PHP (med sub)

```phppdo
$options = array(
    PDO::MYSQL_ATTR_SSL_CA => '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'
);
$db = new PDO('mysql:host=mydemoserver.mysql.database.azure.com;port=3306;dbname=databasename', 'username@mydemoserver', 'yourpassword', $options);
```

### <a name="python-mysqlconnector-python"></a>Python (MySQLConnector python)

```python
try:
    conn = mysql.connector.connect(user='myadmin@mydemoserver',
                                   password='yourpassword',
                                   database='quickstartdb',
                                   host='mydemoserver.mysql.database.azure.com',
                                   ssl_ca='/var/www/html/BaltimoreCyberTrustRoot.crt.pem')
except mysql.connector.Error as err:
    print(err)
```

### <a name="python-pymysql"></a>Python (PyMySQL)

```python
conn = pymysql.connect(user='myadmin@mydemoserver',
                       password='yourpassword',
                       database='quickstartdb',
                       host='mydemoserver.mysql.database.azure.com',
                       ssl={'ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'})
```

### <a name="django-pymysql"></a>Django (PyMySQL)

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'quickstartdb',
        'USER': 'myadmin@mydemoserver',
        'PASSWORD': 'yourpassword',
        'HOST': 'mydemoserver.mysql.database.azure.com',
        'PORT': '3306',
        'OPTIONS': {
            'ssl': {'ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}
        }
    }
}
```

### <a name="ruby"></a>Ruby

```ruby
client = Mysql2::Client.new(
        :host     => 'mydemoserver.mysql.database.azure.com',
        :username => 'myadmin@mydemoserver',
        :password => 'yourpassword',
        :database => 'quickstartdb',
        :sslca => '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'
    )
```

### <a name="golang"></a>Golang

```go
rootCertPool := x509.NewCertPool()
pem, _ := ioutil.ReadFile("/var/www/html/BaltimoreCyberTrustRoot.crt.pem")
if ok := rootCertPool.AppendCertsFromPEM(pem); !ok {
    log.Fatal("Failed to append PEM.")
}
mysql.RegisterTLSConfig("custom", &tls.Config{RootCAs: rootCertPool})
var connectionString string
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin@mydemoserver' , 'yourpassword', 'mydemoserver.mysql.database.azure.com', 'quickstartdb')
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
properties.setProperty("user", 'myadmin@mydemoserver');
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
properties.setProperty("user", 'myadmin@mydemoserver');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="net-mysqlconnector"></a>.NET (MySqlConnector)

```csharp
var builder = new MySqlConnectionStringBuilder
{
    Server = "mydemoserver.mysql.database.azure.com",
    UserID = "myadmin@mydemoserver",
    Password = "yourpassword",
    Database = "quickstartdb",
    SslMode = MySqlSslMode.VerifyCA,
    SslCa = "BaltimoreCyberTrustRoot.crt.pem",
};
using (var connection = new MySqlConnection(builder.ConnectionString))
{
    connection.Open();
}
```

## <a name="next-steps"></a>Nästa steg

Granska olika alternativ för kopplings anslutning efter [Azure Database for MySQL anslutnings bibliotek för](concepts-connection-libraries.md)
