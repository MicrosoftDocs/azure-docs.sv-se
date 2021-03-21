---
title: 'Snabb start: bygga en webbapp med hjälp av Azure Cosmos DB API för mongo DB och Java SDK'
description: Lär dig att bygga ett Java-kod exempel som du kan använda för att ansluta till och fråga med hjälp av Azure Cosmos DBs API för MongoDB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: java
ms.topic: quickstart
ms.date: 12/26/2018
ms.custom: seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: f91cf5103fa8bf324fe372d2f12736a426fe8aed
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101656541"
---
# <a name="quickstart-create-a-console-app-with-java-and-the-mongodb-api-in-azure-cosmos-db"></a>Snabb start: skapa en konsol app med Java och MongoDB-API: et i Azure Cosmos DB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](./mongodb-introduction.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

I den här snabb starten skapar du och hanterar en Azure Cosmos DB för MongoDB-API-konto från Azure Portal och lägger till data med hjälp av en Java SDK-app som klonas från GitHub. Azure Cosmos DB är en databas tjänst med flera modeller som gör att du snabbt kan skapa och fråga dokument-, tabell-, nyckel värdes-och Graf-databaser med globala funktioner för distribution och horisontell skalning.

## <a name="prerequisites"></a>Förutsättningar
- Ett Azure-konto med en aktiv prenumeration. [Skapa ett kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Eller [prova Azure Cosmos DB kostnads fritt](https://azure.microsoft.com/try/cosmosdb/) utan en Azure-prenumeration. Du kan också använda [Azure Cosmos DB emulatorn](https://aka.ms/cosmosdb-emulator) med anslutnings strängen `.mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true` .
- [Java Development Kit (JDK) version 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). 
- [Maven](https://maven.apache.org/download.cgi). Eller kör `apt-get install maven` för att installera Maven.
- [Git](https://git-scm.com/downloads). 

## <a name="create-a-database-account"></a>Skapa ett databaskonto

[!INCLUDE [mongodb-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="add-a-collection"></a>Lägga till en samling

Namnge din **nya databas databas och din** nya samlings **Coll**.

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-mongodb-create-collection.md)] 

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi klona en app från GitHub, ange anslutningssträngen och köra appen. Du kommer att se hur lätt det är att arbeta med data programmässigt. 

1. Öppna en kommandotolk, skapa en ny mapp som heter git-samples och stäng sedan kommandotolken.

    ```bash
    md "C:\git-samples"
    ```

2. Öppna ett git-terminalfönster, t.ex. git bash, och använd kommandot `cd` för att ändra till den nya mappen där du vill installera exempelappen.

    ```bash
    cd "C:\git-samples"
    ```

3. Klona exempellagringsplatsen med följande kommando. Detta kommando skapar en kopia av exempelappen på din dator.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-getting-started.git
    ```

3. Öppna sedan koden i det redigeringsprogram du föredrar. 

## <a name="review-the-code"></a>Granska koden

Det här är valfritt. Om du vill lära dig hur databasresurserna skapas i koden kan du granska följande kodavsnitt. Annars kan du gå vidare till [Uppdatera din anslutningssträng](#update-your-connection-string). 

Följande kodfragment är alla hämtade från filen *program. java* .

Den här konsol programmet använder [Java-drivrutinen MongoDB](https://docs.mongodb.com/ecosystem/drivers/java/). 

* DocumentClient initieras.

    ```java
    MongoClientURI uri = new MongoClientURI("FILLME");`

    MongoClient mongoClient = new MongoClient(uri);            
    ```

* En ny databas och samling skapas.

    ```java
    MongoDatabase database = mongoClient.getDatabase("db");

    MongoCollection<Document> collection = database.getCollection("coll");
    ```

* Några dokument infogas med `MongoCollection.insertOne`

    ```java
    Document document = new Document("fruit", "apple")
    collection.insertOne(document);
    ```

* Några frågor körs med `MongoCollection.find`

    ```java
    Document queryResult = collection.find(Filters.eq("fruit", "apple")).first();
    System.out.println(queryResult.toJson());       
    ```

## <a name="update-your-connection-string"></a>Uppdatera din anslutningssträng

Gå nu tillbaka till Azure Portal för att hämta information om din anslutningssträng och kopiera den till appen.

1. Från ditt Azure Cosmos DB konto väljer du **Snabbstart**, väljer **Java** och kopierar sedan anslutnings strängen till Urklipp.

2. Öppna filen *program. java* och ersätt argumentet till MongoClientURI-konstruktorn med anslutnings strängen. Du har nu uppdaterat appen med all information som behövs för kommunikation med Azure Cosmos DB. 
    
## <a name="run-the-console-app"></a>Kör konsolappen

1. Kör `mvn package` i en terminal för att installera de npm-moduler som krävs

2. Kör `mvn exec:java -D exec.mainClass=GetStarted.Program` i en terminal för att starta Java-programmet.

Nu kan du använda [Robomongo](mongodb-robomongo.md)  /  [Studio-3T](mongodb-mongochef.md) för att fråga, ändra och arbeta med dessa nya data.

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure-portalen

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skapar ett Azure Cosmos DB-API för mongo DB-konto, lägger till en databas och behållare med Datautforskaren och lägger till data med hjälp av en Java-konsol-app. Du kan nu importera ytterligare data till din Cosmos-databas. 

> [!div class="nextstepaction"]
> [Importera MongoDB-data till Azure Cosmos DB](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)