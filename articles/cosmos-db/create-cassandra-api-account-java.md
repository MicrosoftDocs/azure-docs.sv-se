---
title: 'Självstudie: skapa Java-app för att skapa Azure Cosmos DB API för Cassandra konto'
description: Den här självstudien visar hur du skapar ett API för Cassandra-konto, samt lägger till en databas (kallas även ett nyckelutrymme) och en tabell i kontot med hjälp av ett Java-program.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 12/06/2018
ms.custom: seodec18, devx-track-java
ms.openlocfilehash: ca1fdbd9aa2c98358489d91fe0839c98adec293b
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102212713"
---
# <a name="tutorial-create-a-cassandra-api-account-in-azure-cosmos-db-by-using-a-java-application-to-store-keyvalue-data"></a>Självstudie: skapa ett API för Cassandra konto i Azure Cosmos DB genom att använda ett Java-program för att lagra nyckel/värde-data
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Som utvecklare kan du ha program som använder nyckel/värde-par. Du kan använda ett API för Cassandra-konto i Azure Cosmos DB för att lagra nyckel/värde-data. Den här självstudien beskriver hur du använder ett Java-program för att skapa ett API för Cassandra-konto i Azure Cosmos DB, samt lägga till en databas (kallas även ett nyckelutrymme) och en tabell. Java-programmet använder [Java-drivrutinen](https://github.com/datastax/java-driver) för att skapa en användardatabas med information som användar-ID, användarnamn och användarens stad.  

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Skapa ett Cassandra-databaskonto
> * Hämta kontoanslutningssträngen
> * Skapa ett Maven-projekt och beroenden
> * Lägga till en databas och en tabell
> * Kör appen

## <a name="prerequisites"></a>Krav 

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar. 

* Skaffa den senaste versionen av [Java Development Kit (JDK)](/java/azure/jdk/). 

* [Ladda ned](https://maven.apache.org/download.cgi) och [installera](https://maven.apache.org/install.html)[Maven](https://maven.apache.org/)-binärarkivet. 
  - I Ubuntu kan du köra `apt-get install maven` för att installera Maven. 

## <a name="create-a-database-account"></a>Skapa ett databaskonto 

1. Logga in på [Azure-portalen](https://portal.azure.com/). 

2. Välj **skapa en resurs**  >  **databas**  >  **Azure Cosmos DB**. 

3. I fönstret **Nytt konto** anger du inställningarna för det nya Azure Cosmos-kontot. 

   |Inställning   |Föreslaget värde  |Description  |
   |---------|---------|---------|
   |ID   |   Ange ett unikt namn    | Ange ett unikt namn som identifierar Azure Cosmos-kontot. <br/><br/>Eftersomcassandra.cosmosdb.azure.com läggs till det ID du anger för att skapa din kontaktpunkt bör du använda ett unikt men identifierbart ID.         |
   |API    |  Cassandra   |  API:et avgör vilken typ av konto som skapas. <br/> Välj **Cassandra**, eftersom du i den här artikeln skapar en databas med breda kolumner som kan frågas med hjälp av CQL-syntaxen (Cassandra Query Language).  |
   |Prenumeration    |  Din prenumeration        |  Välj den Azure-prenumeration som ska användas för Azure Cosmos-kontot.        |
   |Resursgrupp   | Ange ett namn    |  Välj **Skapa ny** och ange sedan ett nytt resursgruppnamn för ditt konto. För enkelhetens skull kan du använda samma namn som för ditt ID.    |
   |Location    |  Välj den region som är närmast dina användare    |  Välj den geografiska plats som ska vara värd för ditt Azure Cosmos-konto. Använd den plats som är närmast dina användare så att de får så snabb åtkomst till data som möjligt.    |

   :::image type="content" source="./media/create-cassandra-api-account-java/create-account.png" alt-text="Skapa konto med portalen":::

4. Välj **Skapa**. <br/>Det tar några minuter att skapa kontot. När resursen har skapats visas meddelandet **Distributionen lyckades** till höger i portalen.

## <a name="get-the-connection-details-of-your-account"></a>Hämta anslutningsinformationen för ditt konto  

Hämta information om anslutningssträngen från Azure Portal och kopiera den till Java-konfigurationsfilen. Anslutningssträngen gör det möjligt för appen att kommunicera med den värdbaserade databasen. 

1. Från [Azure Portal](https://portal.azure.com/)går du till ditt Azure Cosmos-konto. 

2. Öppna fönstret **anslutnings sträng** .  

3. Kopiera värdena **KONTAKTPUNKT**, **PORT**, **ANVÄNDARNAMN** och **PRIMÄRT LÖSENORD** för användning i nästa steg.

## <a name="create-the-project-and-the-dependencies"></a>Skapa projektet och dess beroenden 

Det Java-exempelprojekt som du använder i den här artikeln finns i GitHub. Du kan köra stegen i det här dokumentet eller ladda ned exemplet från lagringsplatsen [azure-cosmos-db-cassandra-java-getting-started](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started). 

När du har laddat ned filerna uppdaterar du informationen för anslutningssträngen i filen `java-examples\src\main\resources\config.properties` och kör den.  

```java
cassandra_host=<FILLME_with_CONTACT POINT> 
cassandra_port = 10350 
cassandra_username=<FILLME_with_USERNAME> 
cassandra_password=<FILLME_with_PRIMARY PASSWORD> 
```

Använd följande steg för att skapa exemplet från början: 

1. Från terminalen eller kommandotolken skapar du ett nytt Maven-projekt med namnet Cassandra-demo. 

   ```bash
   mvn archetype:generate -DgroupId=com.azure.cosmosdb.cassandra -DartifactId=cassandra-demo -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false 
   ```
 
2. Leta upp mappen `cassandra-demo`. Använd en textredigerare och öppna den `pom.xml`-fil som genererades. 

   Lägg till Cassandra-beroenden och plugin-program som krävs av projektet enligt det som visas i filen [pom.xml](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/main/pom.xml).  

3. I mappen `cassandra-demo\src\main` skapar du en ny mapp med namnet `resources`.  I resursmappen lägger du till filerna config.properties och log4j.properties:

   - I filen [config.properties](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/main/src/main/resources/config.properties) lagras anslutningens slutpunkt och nyckelvärden för API för Cassandra-kontot. 
   
   - Filen [log4j.properties](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/main/src/main/resources/log4j.properties) definierar den loggningsnivå som krävs när du interagerar med API för Cassandra.  

4. Bläddra till mappen `src/main/java/com/azure/cosmosdb/cassandra/`. I Cassandra-mappen skapar du en till mapp med namnet `utils`. Den nya mappen lagrar de verktygsklasser som krävs för att ansluta till Cassandra API-kontot. 

   Lägg till klassen [CassandraUtils](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/main/src/main/java/com/azure/cosmosdb/cassandra/util/CassandraUtils.java) för att skapa klustret och för att öppna och stänga Cassandra-sessioner. Klustret ansluter till API för Cassandra kontot i Azure Cosmos DB och returnerar en session för åtkomst. Använd klassen [Configurations](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/main/src/main/java/com/azure/cosmosdb/cassandra/util/Configurations.java) (Konfigurationer) för att läsa information om anslutningssträng från filen config.properties. 

5. Java-exemplet skapar en databas med användarinformation som användarnamn, användar-ID och användarens stad. Du måste definiera get- och set-metoder för att komma åt användarinformation i main-funktionen.
 
   Skapa en [User.java](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/main/src/main/java/com/azure/cosmosdb/cassandra/examples/UserProfile.java)-klass i mappen `src/main/java/com/azure/cosmosdb/cassandra/` med get- och set-metoder. 

## <a name="add-a-database-and-a-table"></a>Lägga till en databas och en tabell  

Det här avsnittet beskriver hur du lägger till en databas (nyckelutrymme) och en tabell med hjälp av CQL.

1. I mappen `src\main\java\com\azure\cosmosdb\cassandra` skapar du en ny mapp med namnet `repository`. 

2. Skapa Java-klassen `UserRepository` och lägg till följande kod i den: 

   ```java
   package com.azure.cosmosdb.cassandra.repository; 
   import java.util.List; 
   import com.datastax.driver.core.BoundStatement; 
   import com.datastax.driver.core.PreparedStatement; 
   import com.datastax.driver.core.Row; 
   import com.datastax.driver.core.Session; 
   import org.slf4j.Logger; 
   import org.slf4j.LoggerFactory; 
   
   /** 
    * Create a Cassandra session 
    */ 
   public class UserRepository { 
   
       private static final Logger LOGGER = LoggerFactory.getLogger(UserRepository.class); 
       private Session session; 
       public UserRepository(Session session) { 
           this.session = session; 
       } 
   
       /** 
       * Create keyspace uprofile in cassandra DB 
        */ 
   
       public void createKeyspace() { 
            final String query = "CREATE KEYSPACE IF NOT EXISTS uprofile WITH REPLICATION = { 'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1 }"; 
           session.execute(query); 
           LOGGER.info("Created keyspace 'uprofile'"); 
       } 
   
       /** 
        * Create user table in cassandra DB 
        */ 
   
       public void createTable() { 
           final String query = "CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)"; 
           session.execute(query); 
           LOGGER.info("Created table 'user'"); 
       } 
   } 
   ```

3. Leta upp mappen `src\main\java\com\azure\cosmosdb\cassandra` och skapa en ny undermapp med namnet `examples`.

4. Skapa Java-klassen `UserProfile`. Den här klassen innehåller huvudmetoden som anropar metoderna createKeyspace och createTable som du definierade tidigare: 

   ```java
   package com.azure.cosmosdb.cassandra.examples; 
   import java.io.IOException; 
   import com.azure.cosmosdb.cassandra.repository.UserRepository; 
   import com.azure.cosmosdb.cassandra.util.CassandraUtils; 
   import com.datastax.driver.core.PreparedStatement; 
   import com.datastax.driver.core.Session; 
   import org.slf4j.Logger; 
   import org.slf4j.LoggerFactory; 
   
   /** 
    * Example class which will demonstrate following operations on Cassandra Database on CosmosDB 
    * - Create Keyspace 
    * - Create Table 
    * - Insert Rows 
    * - Select all data from a table 
    * - Select a row from a table 
    */ 
   
   public class UserProfile { 
   
       private static final Logger LOGGER = LoggerFactory.getLogger(UserProfile.class); 
       public static void main(String[] s) throws Exception { 
           CassandraUtils utils = new CassandraUtils(); 
           Session cassandraSession = utils.getSession(); 
   
           try { 
               UserRepository repository = new UserRepository(cassandraSession); 
               //Create keyspace in cassandra database 
               repository.createKeyspace(); 
               //Create table in cassandra database 
               repository.createTable(); 
   
           } finally { 
               utils.close(); 
               LOGGER.info("Please delete your table after verifying the presence of the data in portal or from CQL"); 
           } 
       } 
   } 
   ```
 
## <a name="run-the-app"></a>Kör appen 

1. Öppna en kommandotolk eller ett terminalfönster. Klistra in följande kodblock. 

   Den här koden ändrar katalogen (cd) till sökvägen till mappen där du skapade projektet. Sedan kör den kommandot `mvn clean install` för att generera filen `cosmosdb-cassandra-examples.jar` i målmappen. Slutligen kör den Java-programmet.

   ```bash
   cd cassandra-demo

   mvn clean install 

   java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile 
   ```

   Terminalfönstret visar aviseringar om att keyspace och tabell har skapats. 
   
2. I Azure-portalen öppnar du nu **Datautforskaren** för att bekräfta att nyckelutrymmet och tabellen skapades.

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att skapa ett API för Cassandra-konto i Azure Cosmos DB, en databas och en tabell med hjälp av ett Java-program. Nu kan du fortsätta till nästa artikel:

> [!div class="nextstepaction"]
> [Läsa in exempeldata i tabellen för Cassandra-API:et](cassandra-api-load-data.md).
