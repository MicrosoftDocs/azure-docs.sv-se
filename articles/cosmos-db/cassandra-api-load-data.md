---
title: 'Självstudie: Java-app för att läsa in exempel data i en API för Cassandra tabell i Azure Cosmos DB'
description: Den här självstudien beskriver hur du läser in exempelanvändardata till en Cassandra API-tabell i Azure Cosmos DB med hjälp av ett Java-program.
author: kanshiG
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: ad3dfc8c86d22e58377ebfbdb94b2beb004ded56
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93092474"
---
# <a name="tutorial-load-sample-data-into-a-cassandra-api-table-in-azure-cosmos-db"></a>Självstudie: Läsa in exempeldata till en Cassandra API-tabell i Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Som utvecklare kan du ha program som använder nyckel/värde-par. Du kan använda ett konto för Cassandra-API:et i Azure Cosmos DB för att lagra och hantera nyckel/värde-data. Den här självstudien beskriver hur du läser in exempelanvändardata i ett konto för Cassandra-API:et i Azure Cosmos DB med hjälp av ett Java-program. Java-programmet använder [Java-drivrutinen](https://github.com/datastax/java-driver) och läser in användardata såsom användar-ID, användarnamn och användarens stad. 

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Läsa in data till en Cassandra-tabell
> * Kör appen

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

* Den här artikeln tillhör en självstudiekursen i flera delar. Innan du börjar med det här dokumentet ska du se till att [skapa API för Cassandra konto, tecken utrymme och tabell](create-cassandra-api-account-java.md).   

## <a name="load-data-into-the-table"></a>Läsa in data i tabellen

Använd följande steg för att läsa in data till ditt Cassandra-API för tabell:

1. Öppna filen ”UserRepository.java” under mappen ”src\main\java\com\azure\cosmosdb\cassandra” och lägg till koden för att infoga fälten user_id, user_name och user_bcity i tabellen:

   ```java
   /**
   * Insert a row into user table
   *
   * @param id   user_id
   * @param name user_name
   * @param city user_bcity
   */
   public void insertUser(PreparedStatement statement, int id, String name, String city) {
        BoundStatement boundStatement = new BoundStatement(statement);
        session.execute(boundStatement.bind(id, name, city));
   }

   /**
   * Create a PrepareStatement to insert a row to user table
   *
   * @return PreparedStatement
   */
   public PreparedStatement prepareInsertStatement() {
      final String insertStatement = "INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (?,?,?)";
   return session.prepare(insertStatement);
   }
   ```
 
2. Öppna filen ”UserProfile.java” under mappen ”src\main\java\com\azure\cosmosdb\cassandra”. Den här klassen innehåller huvudmetoden som anropar metoderna createKeyspace och createTable som du definierade tidigare. Lägg till följande kod för att infoga lite exempeldata i tabellen för Cassandra-API:et.

   ```java
   //Insert rows into user table
   PreparedStatement preparedStatement = repository.prepareInsertStatement();
     repository.insertUser(preparedStatement, 1, "JohnH", "Seattle");
     repository.insertUser(preparedStatement, 2, "EricK", "Spokane");
     repository.insertUser(preparedStatement, 3, "MatthewP", "Tacoma");
     repository.insertUser(preparedStatement, 4, "DavidA", "Renton");
     repository.insertUser(preparedStatement, 5, "PeterS", "Everett");
   ```

## <a name="run-the-app"></a>Kör appen

Öppna en kommandotolk eller ett terminalfönster och ändra mappsökvägen till den plats där du skapade projektet. Kör kommandot ”mvn clean install” för att generera filen cosmosdb-cassandra-examples.jar i målmappen och köra programmet. 

```bash
cd "cassandra-demo"

mvn clean install

java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
```

Nu kan du öppna Datautforskaren på Azure-portalen och bekräfta att användarinformationen har lagts till i tabellen.
    
## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att läsas in exempeldata till ett konto för Cassandra-API:et i Azure Cosmos DB. Nu kan du fortsätta till nästa artikel:

> [!div class="nextstepaction"]
> [Fråga efter data från kontot för Cassandra-API:et](cassandra-api-query-data.md)
