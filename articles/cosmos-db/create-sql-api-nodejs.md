---
title: Snabbstart – Använda en Node.js för att fråga från Azure Cosmos DB SQL API-konto
description: Hur du använder Node.js för att skapa en app som ansluter till Azure Cosmos DB SQL API-konto och frågar data.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 03/07/2021
ms.author: anfeldma
ms.custom: devx-track-js
ms.openlocfilehash: ead4004813cf6415dfa1c7da3d308d93ea49a773
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365918"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-from-azure-cosmos-db-sql-api-account"></a>Snabbstart: Använd Node.js för att ansluta och fråga efter data från Azure Cosmos DB SQL API-konto
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> - [.NET V3](create-sql-api-dotnet.md)
> - [.NET V4](create-sql-api-dotnet-V4.md)
> - [Java SDK v4](create-sql-api-java.md)
> * [Spring-data v3](create-sql-api-spring-data.md)
> * [Spark v3-anslutningsapp](create-sql-api-spark.md)
> - [Node.js](create-sql-api-nodejs.md)
> - [Python](create-sql-api-python.md)
> - [Xamarin](create-sql-api-xamarin-dotnet.md)

I den här snabbstarten skapar och hanterar du ett Azure Cosmos DB SQL API-konto från Azure Portal och med hjälp av en Node.js-app som klonas från GitHub. Azure Cosmos DB är en databastjänst med flera modeller som gör att du snabbt kan skapa och fråga dokument-, tabell-, nyckelvärdes- och grafdatabaser med global distribution och horisontella skalningsfunktioner.

## <a name="walkthrough-video"></a>Genomgångsvideo

Titta på den här videon för en fullständig genomgång av innehållet i den här artikeln.

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Quickstart-Use-Nodejs-to-connect-and-query-data-from-Azure-Cosmos-DB-SQL-API-account/player]

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett utan kostnad.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Eller [prova Azure Cosmos DB utan en](https://azure.microsoft.com/try/cosmosdb/) Azure-prenumeration. Du kan också använda [Azure Cosmos DB emulatorn](https://aka.ms/cosmosdb-emulator) med en URI `https://localhost:8081` för och nyckeln `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==` .
- [Node.js 6.0.0+](https://nodejs.org/).
- [Git](https://www.git-scm.com/downloads).

## <a name="create-an-azure-cosmos-account"></a>Skapa ett Azure Cosmos-konto

I det här snabbstartssyftet kan du använda [alternativet prova Azure Cosmos DB kostnadsfritt](https://azure.microsoft.com/try/cosmosdb/) för att skapa ett Azure Cosmos-konto.

1. Gå till sidan [prova Azure Cosmos DB utan](https://azure.microsoft.com/try/cosmosdb/) kostnad.

1. Välj **SQL** API-kontot och välj **Skapa.** Logga in med din Microsoft-konto.

1. När inloggningen är klar bör ditt Azure Cosmos-konto vara klart. Välj **Öppna i Azure Portal för** att öppna det nyligen skapade kontot.

Alternativet "prova Azure Cosmos DB kostnadsfritt" kräver inte någon Azure-prenumeration och du får ett Azure Cosmos-konto under en begränsad period på 30 dagar. Om du vill använda Azure Cosmos-kontot under en längre period bör du i stället [skapa kontot i](create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account) din Azure-prenumeration.

## <a name="add-a-container"></a>Lägga till en container

Nu kan du använda Datautforskaren i Azure Portal för att skapa en databas och container.

1. Välj **Datautforskaren**  >  **Ny container**.

   Området **Lägg till** container visas längst till höger. Du kan behöva rulla åt höger för att se det.

   :::image type="content" source="./media/create-sql-api-nodejs/azure-cosmosdb-data-explorer.png" alt-text="Datautforskaren på Azure-portalen, fönstret Lägg till container":::

2. På sidan **Lägg till container** anger du inställningarna för den nya containern.

   | Inställning           | Föreslaget värde | Beskrivning                                                                                                                                                                                                                                                                                                                                                                           |
   | ----------------- | --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
   | **Databas-ID**   | Uppgifter           | Ange _Uppgifter_ som namn på den nya databasen. Databasnamn måste innehålla 1–255 tecken och får inte innehålla `/, \\, #, ?`, eller avslutande blanksteg. Markera alternativet **Etablera databasens dataflöde.** Med det kan du dela dataflödet som etablerats till databasen över alla containrar i databasen. Det här alternativet hjälper också till med kostnadsbesparingar. |
   | **Dataflöde**    | 400             | Lämna dataflödet på 400 enheter för begäran per sekund (RU/s). Du kan skala upp dataflödet senare om du vill minska svarstiden.                                                                                                                                                                                                                                                    |
   | **Container-ID**  | Poster           | Ange _Objekt_ som namn på den nya containern. För container-ID:n gäller samma teckenkrav som för databasnamn.                                                                                                                                                                                                                                                               |
   | **Partitionsnyckel** | /category       | Exemplet som beskrivs i den här artikeln använder _/category_ som partitionsnyckel.                                                                                                                                                                                                                                                                                                           |

   Förutom de föregående inställningarna kan du även lägga till **Unika nycklar** för containern. Vi lämnar fältet tomt i det här exemplet. Unika nycklar ger utvecklarna möjlighet att lägga till ett lager med dataintegritet till databasen. Genom att skapa en unik nyckelprincip när du skapar en container ser du till att ett eller flera värden per partitionsnyckel är unika. Läs mer i artikeln om [unika nycklar i Azure Cosmos DB](unique-keys.md).

   Välj **OK**. Datautforskaren visar den nya databasen och containern.

## <a name="add-sample-data"></a>Lägga till exempeldata

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Fråga dina data

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi klona en Node.js från GitHub, ange anslutningssträngen och köra den.

1. Klona exempellagringsplatsen med följande kommando. Detta kommando skapar en kopia av exempelappen på din dator.

   ```bash
   git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started.git
   ```

## <a name="review-the-code"></a>Granska koden

Det här är valfritt. Om du är intresserad av att lära dig hur Azure Cosmos-databasresurserna skapas i koden kan du granska följande kodavsnitt. Annars kan du gå vidare till [Uppdatera din anslutningssträng](#update-your-connection-string).

Om du är bekant med den tidigare versionen av SQL JavaScript SDK kan du vara van att se termerna _samling_ och _dokument_. Eftersom Azure Cosmos DB stöder flera [API-modeller](introduction.md)använder [version 2.0+](https://www.npmjs.com/package/@azure/cosmos) av JavaScript SDK de allmänna termerna container  , som kan vara en samling, graf eller tabell och objekt för att beskriva innehållet i _containern._

Den Cosmos DB JavaScript SDK kallas " @azure/cosmos " och kan installeras från npm...

```bash
npm install @azure/cosmos
```

Följande kodfragment är alla hämtade från filen _app.js_.

- `CosmosClient`importeras från `@azure/cosmos` npm-paketet.

  ```javascript
  const CosmosClient = require("@azure/cosmos").CosmosClient;
  ```

- Ett nytt `CosmosClient` objekt initieras.

  ```javascript
  const client = new CosmosClient({ endpoint, key });
  ```

- Välj databasen "Uppgifter".

  ```javascript
  const database = client.database(databaseId);
  ```

- Välj containern/samlingen "Objekt".

  ```javascript
  const container = database.container(containerId);
  ```

- Markera alla objekt i containern "Items".

  ```javascript
  // query to return all items
  const querySpec = {
    query: "SELECT * from c"
  };

  const { resources: items } = await container.items
    .query(querySpec)
    .fetchAll();
  ```

- Skapa ett nytt objekt

  ```javascript
  const { resource: createdItem } = await container.items.create(newItem);
  ```

- Uppdatera ett objekt

  ```javascript
  const { id, category } = createdItem;

  createdItem.isComplete = true;
  const { resource: updatedItem } = await container
    .item(id, category)
    .replace(createdItem);
  ```

- Ta bort ett objekt

  ```javascript
  const { resource: result } = await container.item(id, category).delete();
  ```

> [!NOTE]
> I båda metoderna "update" och "delete" måste objektet väljas från databasen genom att anropa `container.item()` . De två parametrar som skickas är ID:t för objektet och objektets partitionsnyckel. I det här fallet är paritionsnyckeln värdet för fältet "kategori".

## <a name="update-your-connection-string"></a>Uppdatera din anslutningssträng

Gå nu tillbaka till Azure Portal att hämta information om anslutningssträngen för ditt Azure Cosmos-konto. Kopiera anslutningssträngen till appen så att den kan ansluta till databasen.

1. I ditt Azure Cosmos DB konto [i Azure Portal](https://portal.azure.com/)väljer du **Nycklar** i det vänstra navigeringsfönstret och sedan **Läs-skrivnycklar.** Använd kopieringsknapparna till höger på skärmen för att kopiera URI:en och primärnyckeln _tillapp.js_ i nästa steg.

   :::image type="content" source="./media/create-sql-api-dotnet/keys.png" alt-text="Visa och kopiera en åtkomstnyckel i Azure Portal, bladet Nycklar":::

2. I Öppna _config.js_ fil.

3. Kopiera ditt URI-värde från portalen (med kopieringsknappen) och gör det till värdet för slutpunktsnyckeln _iconfig.js_.

   `endpoint: "<Your Azure Cosmos account URI>"`

4. Kopiera sedan värdet för PRIMÄRNYCKEL från portalen och gör det till värdet för `config.key` i _config.js_. Du har nu uppdaterat appen med all information som behövs för kommunikation med Azure Cosmos DB.

   `key: "<Your Azure Cosmos account key>"`

## <a name="run-the-app"></a>Kör appen

1. Kör `npm install` i en terminal för att installera " " @azure/cosmos npm-paketet

2. Kör `node app.js` i en terminal för att starta nodprogrammet.

3. De två objekt som du skapade tidigare i den här snabbstarten visas. Ett nytt objekt skapas. Flaggan "isComplete" för objektet uppdateras till "true" och slutligen tas objektet bort.

Du kan fortsätta att experimentera med det här exempelprogrammet eller gå tillbaka till Datautforskaren, ändra och arbeta med dina data.

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure-portalen

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du skapar ett Azure Cosmos DB-konto, skapar en container med Datautforskaren och kör en Node.js app. Du kan nu importera ytterligare data till ditt Azure Cosmos DB-konto.

> [!div class="nextstepaction"]
> [importera data till azure cosmos db](import-data.md)
