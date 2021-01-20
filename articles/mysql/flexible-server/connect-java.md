---
title: 'Snabb start: använda Java och JDBC med Azure Database för MySQLFlexible-Server'
description: Lär dig hur du använder Java och JDBC med en Azure Database for MySQL flexibel Server databas.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.custom: mvc, devcenter, devx-track-azurecli
ms.topic: quickstart
ms.devlang: java
ms.date: 01/16/2021
ms.openlocfilehash: 93a8a7644c7ca729f0e4fd201b88e995576d4bda
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2021
ms.locfileid: "98606166"
---
# <a name="quickstart-use-java-and-jdbc-with-azure-database-for-mysql-flexible-server"></a>Snabb start: Använd Java och JDBC med Azure Database for MySQL flexibel Server

Det här avsnittet visar hur du skapar ett exempel program som använder Java och [JDBC](https://en.wikipedia.org/wiki/Java_Database_Connectivity) för att lagra och hämta information i [Azure Database for MySQL flexibel Server](./index.yml).

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto. Om du inte har någon kan du [få en kostnads fri utvärderings version](https://azure.microsoft.com/free/).
- [Azure Cloud Shell](../../cloud-shell/quickstart.md) eller [Azure CLI](/cli/azure/install-azure-cli). Vi rekommenderar Azure Cloud Shell så att du loggas in automatiskt och har åtkomst till alla verktyg du behöver.
- En [Java Development Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support)som stöds, version 8 (ingår i Azure Cloud Shell).
- Verktyget [Apache maven](https://maven.apache.org/) build.

## <a name="prepare-the-working-environment"></a>Förbereda arbetsmiljön

Vi kommer att använda miljövariabler för att begränsa skrivfel och för att göra det enklare för dig att anpassa följande konfiguration efter dina egna behov.

Konfigurera miljövariablerna med hjälp av följande kommandon:

```bash
AZ_RESOURCE_GROUP=database-workshop
AZ_DATABASE_NAME= flexibleserverdb
AZ_LOCATION=<YOUR_AZURE_REGION>
AZ_MYSQL_USERNAME=demo
AZ_MYSQL_PASSWORD=<YOUR_MYSQL_PASSWORD>
AZ_LOCAL_IP_ADDRESS=<YOUR_LOCAL_IP_ADDRESS>
```

Ersätt platshållarna med följande värden, som används i hela artikeln:

- `<YOUR_DATABASE_NAME>`: Namnet på MySQL-servern. Det ska vara unikt i Azure.
- `<YOUR_AZURE_REGION>`: Den Azure-region som du ska använda. Du kan använda `eastus` som standard, men vi rekommenderar att du konfigurerar den region som är närmast dig. Du kan ha en fullständig lista över tillgängliga regioner genom att ange `az account list-locations` .
- `<YOUR_MYSQL_PASSWORD>`: Lösen ordet för din MySQL Database-Server. Lösenordet måste innehålla minst åtta tecken. Tecknen måste komma från tre av följande kategorier: Engelska versaler, engelska gemener, siffror (0–9) och specialtecken (som !, $, # eller %).
- `<YOUR_LOCAL_IP_ADDRESS>`: IP-adressen för den lokala datorn som du kör Java-programmet från. Ett bekvämt sätt att hitta det är att peka din webbläsare på [whatismyip.Akamai.com](http://whatismyip.akamai.com/).

Skapa sedan en resursgrupp:

```azurecli
az group create \
    --name $AZ_RESOURCE_GROUP \
    --location $AZ_LOCATION \
  	| jq
```

> [!NOTE]
> Vi använder verktyget `jq` som är installerat i [Azure Cloud Shell](https://shell.azure.com/) till att visa JSON-data och göra informationen enklare att läsa.
> Om du inte gillar det här verktyget kan du ta bort `| jq` i alla kommandon.

## <a name="create-an-azure-database-for-mysql-instance"></a>Skapa en Azure Database for MySQL-instans

Det första steget är att skapa en hanterad MySQL-server.

> [!NOTE]
> Du kan läsa mer om att skapa MySQL-servrar i [Skapa en Azure Database for MySQL-server i Azure-portalen](./quickstart-create-server-portal.md).

Kör följande skript i [Azure Cloud Shell](https://shell.azure.com/):

```azurecli
az mysql flexible-server create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME \
    --location $AZ_LOCATION \
    --sku-name Standard_B1ms \
    --storage-size 5120 \
    --admin-user $AZ_MYSQL_USERNAME \
    --admin-password $AZ_MYSQL_PASSWORD \
    --public-access $AZ_LOCAL_IP_ADDRESS
  	| jq
```

Se till att ange <IP-adressen> för att få åtkomst till servern från den lokala datorn. Med det här kommandot skapar du en MySQL-flexibel server med burst-nivå som är lämplig för utveckling.

MySQL-servern som du skapade har en tom databas med namnet **flexibleserverdb**. Vi kommer att använda den här databasen för den här artikeln.

[Har du några problem? Berätta för oss.](https://github.com/MicrosoftDocs/azure-docs/issues)

### <a name="create-a-new-java-project"></a>Skapa ett nytt Java-projekt

Använd din favorit-IDE, skapa ett nytt Java-projekt och Lägg till en `pom.xml` fil i dess rot Katalog:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.example</groupId>
    <artifactId>demo</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>demo</name>

    <properties>
        <java.version>1.8</java.version>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
    </properties>

    <dependencies>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.20</version>
        </dependency>
    </dependencies>
</project>
```

Den här filen är en [Apache-maven](https://maven.apache.org/) som konfigurerar vårt projekt att använda:

- Java 8
- En senaste MySQL-drivrutin för Java

### <a name="prepare-a-configuration-file-to-connect-to-azure-database-for-mysql"></a>Förbered en konfigurations fil för att ansluta till Azure Database for MySQL

Skapa filen *src/main/Resources/Application. Properties* och Lägg till:

```properties
url=jdbc:mysql://$AZ_DATABASE_NAME.mysql.database.azure.com:3306/demo?serverTimezone=UTC
user=demo
password=$AZ_MYSQL_PASSWORD
```

- Ersätt de två `$AZ_DATABASE_NAME` variablerna med det värde som du konfigurerade i början av den här artikeln.
- Ersätt `$AZ_MYSQL_PASSWORD` variabeln med det värde som du konfigurerade i början av den här artikeln.

> [!NOTE]
> Lägg till `?serverTimezone=UTC` i konfigurationsegenskapen `url` så att JDBC-drivrutinen använder datumformatet UTC (koordinerad universell tid) vid anslutning till databasen. Annars skulle inte Java-servern använda samma datumformat som databasen, vilket ger ett fel.

### <a name="create-an-sql-file-to-generate-the-database-schema"></a>Skapa en SQL-fil för att generera databasschemat

Vi kommer att använda en *src/main/- `schema.sql` resurs/-* fil för att skapa ett databas schema. Skapa filen med följande innehåll:

```sql
DROP TABLE IF EXISTS todo;
CREATE TABLE todo (id SERIAL PRIMARY KEY, description VARCHAR(255), details VARCHAR(4096), done BOOLEAN);
```

## <a name="code-the-application"></a>Koda appen

### <a name="connect-to-the-database"></a>Ansluta till databasen

Lägg sedan till den Java-kod som ska använda JDBC för att lagra och hämta data från MySQL-servern.

Skapa en *src/main/Java/DemoApplication. java* -fil som innehåller:

```java
package com.example.demo;

import com.mysql.cj.jdbc.AbandonedConnectionCleanupThread;

import java.sql.*;
import java.util.*;
import java.util.logging.Logger;

public class DemoApplication {

    private static final Logger log;

    static {
        System.setProperty("java.util.logging.SimpleFormatter.format", "[%4$-7s] %5$s %n");
        log =Logger.getLogger(DemoApplication.class.getName());
    }

    public static void main(String[] args) throws Exception {
        log.info("Loading application properties");
        Properties properties = new Properties();
        properties.load(DemoApplication.class.getClassLoader().getResourceAsStream("application.properties"));

        log.info("Connecting to the database");
        Connection connection = DriverManager.getConnection(properties.getProperty("url"), properties);
        log.info("Database connection test: " + connection.getCatalog());

        log.info("Create database schema");
        Scanner scanner = new Scanner(DemoApplication.class.getClassLoader().getResourceAsStream("schema.sql"));
        Statement statement = connection.createStatement();
        while (scanner.hasNextLine()) {
            statement.execute(scanner.nextLine());
        }

        /*
        Todo todo = new Todo(1L, "configuration", "congratulations, you have set up JDBC correctly!", true);
        insertData(todo, connection);
        todo = readData(connection);
        todo.setDetails("congratulations, you have updated data!");
        updateData(todo, connection);
        deleteData(todo, connection);
        */

        log.info("Closing database connection");
        connection.close();
        AbandonedConnectionCleanupThread.uncheckedShutdown();
    }
}
```

[Har du några problem? Berätta för oss.](https://github.com/MicrosoftDocs/azure-docs/issues)

Den här Java-koden använder *programmet. Properties* och *schema. SQL* -filerna som vi skapade tidigare, för att ansluta till MySQL-servern och skapa ett schema som lagrar våra data.

I den här filen kan du se att vi har kommenterat metoderna för att infoga, läsa, uppdatera och ta bort data: vi kommer att koda dessa metoder i resten av den här artikeln och du kommer att kunna ta bort kommentarer om dem efter varandra.

> [!NOTE]
> Autentiseringsuppgifterna för databasen lagras i egenskaperna *användare* och *lösen ord* för filen *Application. Properties* . Dessa autentiseringsuppgifter används vid körning `DriverManager.getConnection(properties.getProperty("url"), properties);` , eftersom egenskaps filen skickas som ett argument.

> [!NOTE]
> `AbandonedConnectionCleanupThread.uncheckedShutdown();`Raden i slutet är ett kommando för MySQL-drivrutin som kan förstöra en intern tråd när programmet stängs av.
> Den kan ignoreras.

Nu kan du köra den här huvud klassen med ditt favorit verktyg:

- Med hjälp av din IDE bör du kunna Högerklicka på klassen *DemoApplication* och köra den.
- Med maven kan du köra programmet genom att köra: `mvn exec:java -Dexec.mainClass="com.example.demo.DemoApplication"` .

Programmet bör ansluta till Azure Database for MySQL, skapa ett databas schema och sedan stänga anslutningen, som du bör se i konsol loggarna:

```
[INFO   ] Loading application properties
[INFO   ] Connecting to the database
[INFO   ] Database connection test: demo
[INFO   ] Create database schema
[INFO   ] Closing database connection
```

### <a name="create-a-domain-class"></a>Skapa en domän klass

Skapa en ny `Todo` Java-klass bredvid- `DemoApplication` klassen och Lägg till följande kod:

```java
package com.example.demo;

public class Todo {

    private Long id;
    private String description;
    private String details;
    private boolean done;

    public Todo() {
    }

    public Todo(Long id, String description, String details, boolean done) {
        this.id = id;
        this.description = description;
        this.details = details;
        this.done = done;
    }

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getDescription() {
        return description;
    }

    public void setDescription(String description) {
        this.description = description;
    }

    public String getDetails() {
        return details;
    }

    public void setDetails(String details) {
        this.details = details;
    }

    public boolean isDone() {
        return done;
    }

    public void setDone(boolean done) {
        this.done = done;
    }

    @Override
    public String toString() {
        return "Todo{" +
                "id=" + id +
                ", description='" + description + '\'' +
                ", details='" + details + '\'' +
                ", done=" + done +
                '}';
    }
}
```

Den här klassen är en domän modell som är mappad till `todo` tabellen som du skapade när du körde *schema. SQL* -skriptet.

### <a name="insert-data-into-azure-database-for-mysql"></a>Infoga data i Azure Database for MySQL

I filen *src/main/Java/DemoApplication. java* efter huvud metoden lägger du till följande metod för att infoga data i databasen:

```java
private static void insertData(Todo todo, Connection connection) throws SQLException {
    log.info("Insert data");
    PreparedStatement insertStatement = connection
            .prepareStatement("INSERT INTO todo (id, description, details, done) VALUES (?, ?, ?, ?);");

    insertStatement.setLong(1, todo.getId());
    insertStatement.setString(2, todo.getDescription());
    insertStatement.setString(3, todo.getDetails());
    insertStatement.setBoolean(4, todo.isDone());
    insertStatement.executeUpdate();
}
```

Du kan nu ta bort kommentarer till de två följande raderna i- `main` metoden:

```java
Todo todo = new Todo(1L, "configuration", "congratulations, you have set up JDBC correctly!", true);
insertData(todo, connection);
```

Att köra huvud klassen bör nu generera följande utdata:

```
[INFO   ] Loading application properties
[INFO   ] Connecting to the database
[INFO   ] Database connection test: demo
[INFO   ] Create database schema
[INFO   ] Insert data
[INFO   ] Closing database connection
```

### <a name="reading-data-from-azure-database-for-mysql"></a>Läser data från Azure Database for MySQL

Nu ska vi läsa de data som infogats tidigare, för att verifiera att vår kod fungerar som den ska.

I filen *src/main/Java/DemoApplication. java* , efter `insertData` metoden, lägger du till följande metod för att läsa data från databasen:

```java
private static Todo readData(Connection connection) throws SQLException {
    log.info("Read data");
    PreparedStatement readStatement = connection.prepareStatement("SELECT * FROM todo;");
    ResultSet resultSet = readStatement.executeQuery();
    if (!resultSet.next()) {
        log.info("There is no data in the database!");
        return null;
    }
    Todo todo = new Todo();
    todo.setId(resultSet.getLong("id"));
    todo.setDescription(resultSet.getString("description"));
    todo.setDetails(resultSet.getString("details"));
    todo.setDone(resultSet.getBoolean("done"));
    log.info("Data read from the database: " + todo.toString());
    return todo;
}
```

Du kan nu ta bort kommentaren till följande rad i- `main` metoden:

```java
todo = readData(connection);
```

Att köra huvud klassen bör nu generera följande utdata:

```
[INFO   ] Loading application properties
[INFO   ] Connecting to the database
[INFO   ] Database connection test: demo
[INFO   ] Create database schema
[INFO   ] Insert data
[INFO   ] Read data
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have set up JDBC correctly!', done=true}
[INFO   ] Closing database connection
```

[Har du några problem? Berätta för oss.](https://github.com/MicrosoftDocs/azure-docs/issues)

### <a name="updating-data-in-azure-database-for-mysql"></a>Uppdatera data i Azure Database for MySQL

Nu ska vi uppdatera de data som vi infogade tidigare.

Lägg fortfarande till följande metod i filen *src/main/Java/DemoApplication. java* och `readData` Lägg till följande metod för att uppdatera data i databasen efter-metoden:

```java
private static void updateData(Todo todo, Connection connection) throws SQLException {
    log.info("Update data");
    PreparedStatement updateStatement = connection
            .prepareStatement("UPDATE todo SET description = ?, details = ?, done = ? WHERE id = ?;");

    updateStatement.setString(1, todo.getDescription());
    updateStatement.setString(2, todo.getDetails());
    updateStatement.setBoolean(3, todo.isDone());
    updateStatement.setLong(4, todo.getId());
    updateStatement.executeUpdate();
    readData(connection);
}
```

Du kan nu ta bort kommentarer till de två följande raderna i- `main` metoden:

```java
todo.setDetails("congratulations, you have updated data!");
updateData(todo, connection);
```

Att köra huvud klassen bör nu generera följande utdata:

```
[INFO   ] Loading application properties
[INFO   ] Connecting to the database
[INFO   ] Database connection test: demo
[INFO   ] Create database schema
[INFO   ] Insert data
[INFO   ] Read data
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have set up JDBC correctly!', done=true}
[INFO   ] Update data
[INFO   ] Read data
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have updated data!', done=true}
[INFO   ] Closing database connection
```

### <a name="deleting-data-in-azure-database-for-mysql"></a>Ta bort data i Azure Database for MySQL

Slutligen tar vi bort de data som vi infogade tidigare.

I filen *src/main/Java/DemoApplication. java* , efter `updateData` metoden, lägger du till följande metod för att ta bort data i databasen:

```java
private static void deleteData(Todo todo, Connection connection) throws SQLException {
    log.info("Delete data");
    PreparedStatement deleteStatement = connection.prepareStatement("DELETE FROM todo WHERE id = ?;");
    deleteStatement.setLong(1, todo.getId());
    deleteStatement.executeUpdate();
    readData(connection);
}
```

Du kan nu ta bort kommentaren till följande rad i- `main` metoden:

```java
deleteData(todo, connection);
```

Att köra huvud klassen bör nu generera följande utdata:

```
[INFO   ] Loading application properties
[INFO   ] Connecting to the database
[INFO   ] Database connection test: demo
[INFO   ] Create database schema
[INFO   ] Insert data
[INFO   ] Read data
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have set up JDBC correctly!', done=true}
[INFO   ] Update data
[INFO   ] Read data
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have updated data!', done=true}
[INFO   ] Delete data
[INFO   ] Read data
[INFO   ] There is no data in the database!
[INFO   ] Closing database connection
```

## <a name="clean-up-resources"></a>Rensa resurser

Grattis! Du har skapat ett Java-program som använder JDBC för att lagra och hämta data från Azure Database for MySQL.

Om du vill rensa alla resurser som används under den här snabb starten tar du bort resurs gruppen med hjälp av följande kommando:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Migrera MySQL-databasen till Azure Database för MySQL med säkerhetskopiering och återställning](../concepts-migrate-dump-restore.md)
