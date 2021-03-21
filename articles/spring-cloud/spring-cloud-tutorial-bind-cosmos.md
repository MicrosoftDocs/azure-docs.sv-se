---
title: Binda en Azure Cosmos DB till din Azure Spring Cloud-app
description: Lär dig hur du binder Azure Cosmos DB till ditt Azure våren Cloud-program
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/06/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 20926988d554d562f36587734bd99c34b4731e69
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92088728"
---
# <a name="bind-an-azure-cosmos-db-database-to-your-azure-spring-cloud-application"></a>Bind en Azure Cosmos DB-databas till ditt Azure våren Cloud-program

**Den här artikeln gäller för:** ✔️ Java

I stället för att manuellt konfigurera dina våren Boot-program kan du automatiskt binda utvalda Azure-tjänster till dina program med hjälp av Azure våren Cloud. Den här artikeln visar hur du binder ditt program till en Azure Cosmos DB-databas.

Krav:

* En distribuerad Azure våren Cloud-instans. Kom igång [genom att följa snabb starten för att distribuera via Azure CLI](spring-cloud-quickstart.md) .
* Ett Azure Cosmos DB konto med en lägsta behörighets nivå för deltagare.

## <a name="bind-azure-cosmos-db"></a>Bind Azure Cosmos DB

Azure Cosmos DB har fem olika API-typer som stöder bindning. Följande procedur visar hur du använder dem:

1. Skapa en Azure Cosmos DB-databas. Se snabb starten för att [skapa en databas](../cosmos-db/create-cosmosdb-resources-portal.md) för hjälp. 

1. Anteckna namnet på databasen. I den här proceduren är databas namnet **testdb**.

1. Lägg till något av följande beroenden i Azure våren Cloud-programmets pom.xml-fil. Välj det beroende som är lämpligt för din API-typ.

    * API-typ: Core (SQL)

      ```xml
      <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
          <version>2.1.6</version>
      </dependency>
      ```

    * API-typ: MongoDB

      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-mongodb</artifactId>
      </dependency>
      ```

    * API-typ: Cassandra

      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-cassandra</artifactId>
      </dependency>
      ```

    * API-typ: Gremlin (graf)

      ```xml
      <dependency>
          <groupId>com.microsoft.spring.data.gremlin</groupId>
          <artifactId>spring-data-gremlin</artifactId>
          <version>2.1.7</version>
      </dependency>
      ```

    * API-typ: Azure-tabell

      ```xml
      <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-storage-spring-boot-starter</artifactId>
          <version>2.0.5</version>
      </dependency>
      ```

1. Använd `az spring-cloud app update` för att uppdatera den aktuella distributionen eller Använd `az spring-cloud app deployment create` för att skapa en ny distribution. Dessa kommandon antingen uppdaterar eller skapar programmet med det nya beroendet.

1. Gå till sidan Azure våren Cloud service i Azure Portal. Gå till **instrument panelen för program** och välj det program som du vill binda till Azure Cosmos dB. Det här programmet är samma som du uppdaterade eller distribuerade i föregående steg.

1. Välj **tjänst bindning** och välj **skapa tjänst bindning**. Om du vill fylla i formuläret väljer du:
   * Värdet för **bindnings typen** **Azure Cosmos DB**.
   * API-typ.
   * Ditt databas namn.
   * Azure Cosmos DB kontot.

    > [!NOTE]
    > Om du använder Cassandra kan du använda ett nyckel utrymme för databas namnet.

1. Starta om programmet genom att välja **Starta** om på program sidan.

1. För att säkerställa att tjänsten är korrekt bunden väljer du bindnings namnet och kontrollerar dess information. `property`Fältet bör likna följande exempel:

    ```
    azure.cosmosdb.uri=https://<some account>.documents.azure.com:443
    azure.cosmosdb.key=abc******
    azure.cosmosdb.database=testdb
    ```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du binder ditt Azure våren Cloud-program till en Azure Cosmos DB-databas. Mer information om hur du kopplar tjänster till ditt program finns i [binda till en Azure-cache för Redis-cache](spring-cloud-tutorial-bind-redis.md).