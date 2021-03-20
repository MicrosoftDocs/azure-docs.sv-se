---
title: Snabb start – Använd Node.js för att fråga från Azure Cosmos DB SQL API-konto
description: Använda Node.js för att skapa en app som ansluter till Azure Cosmos DB SQL API-konto och skickar frågor till data.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 09/22/2020
ms.author: anfeldma
ms.custom: devx-track-js
ms.openlocfilehash: b4ed27e1515e898a71fb503bb0f260c608ef9f6f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93090213"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-from-azure-cosmos-db-sql-api-account"></a>Snabb start: Använd Node.js för att ansluta och fråga efter data från Azure Cosmos DB SQL API-konto
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> - [.NET V3](create-sql-api-dotnet.md)
> - [.NET V4](create-sql-api-dotnet-V4.md)
> - [Java SDK v4](create-sql-api-java.md)
> * [Spring-data v3](create-sql-api-spring-data.md)
> - [Node.js](create-sql-api-nodejs.md)
> - [Python](create-sql-api-python.md)
> - [Xamarin](create-sql-api-xamarin-dotnet.md)

I den här snabb starten skapar och hanterar du ett Azure Cosmos DB SQL API-konto från Azure Portal och genom att använda en Node.js app som klonas från GitHub. Azure Cosmos DB är en databas tjänst med flera modeller som gör att du snabbt kan skapa och fråga dokument-, tabell-, nyckel värdes-och Graf-databaser med globala funktioner för distribution och horisontell skalning.

## <a name="walkthrough-video"></a>Genom gångs video

Titta på den här videon om du vill ha en fullständig genom gång av innehållet i den här artikeln.

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Quickstart-Use-Nodejs-to-connect-and-query-data-from-Azure-Cosmos-DB-SQL-API-account/player]

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Eller [prova Azure Cosmos DB kostnads fritt](https://azure.microsoft.com/try/cosmosdb/) utan en Azure-prenumeration. Du kan också använda [Azure Cosmos DB emulatorn](https://aka.ms/cosmosdb-emulator) med en URI `https://localhost:8081` och nyckeln `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==` .
- [Node.js 6.0.0 +](https://nodejs.org/).
- [Git](https://www.git-scm.com/downloads).

## <a name="create-an-azure-cosmos-account"></a>Skapa ett Azure Cosmos-konto

I det här snabb starts syftet kan du använda alternativet [prova Azure Cosmos DB kostnads fritt](https://azure.microsoft.com/try/cosmosdb/) för att skapa ett Azure Cosmos-konto.

1. Gå till sidan [prova Azure Cosmos DB kostnads fritt](https://azure.microsoft.com/try/cosmosdb/) .

1. Välj **SQL** API-konto och välj **skapa**. Logga in med din Microsoft-konto.

1. När inloggningen är klar bör ditt Azure Cosmos-konto vara klart. Öppna det nyligen skapade kontot genom att välja **Öppna i Azure Portal** .

Alternativet "prova Azure Cosmos DB gratis" kräver ingen Azure-prenumeration och du får ett Azure Cosmos-konto för en begränsad period på 30 dagar. Om du vill använda Azure Cosmos-kontot under en längre period bör du i stället [skapa kontot](create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account) i din Azure-prenumeration.

## <a name="add-a-container"></a>Lägga till en container

Du kan nu använda Datautforskaren-verktyget i Azure Portal för att skapa en databas och behållare.

1. Välj **datautforskaren**  >  **ny behållare**.

   Avsnittet **Lägg till behållare** visas längst till höger. du kan behöva rulla åt höger för att se det.

   :::image type="content" source="./media/create-sql-api-nodejs/azure-cosmosdb-data-explorer.png" alt-text="Datautforskaren på Azure-portalen, fönstret Lägg till container":::

2. På sidan **Lägg till behållare** anger du inställningarna för den nya behållaren.

   | Inställning           | Föreslaget värde | Beskrivning                                                                                                                                                                                                                                                                                                                                                                           |
   | ----------------- | --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
   | **Databas-ID**   | Uppgifter           | Ange _Uppgifter_ som namn på den nya databasen. Databasnamn måste innehålla 1–255 tecken och får inte innehålla `/, \\, #, ?`, eller avslutande blanksteg. Kontrol lera **data flödes alternativet etablera databas** så att du kan dela det data flöde som har etablerats till databasen över alla behållare i databasen. Det här alternativet hjälper också till med kostnads besparingar. |
   | **Dataflöde**    | 400             | Lämna data flödet på 400 enheter för programbegäran per sekund (RU/s). Du kan skala upp dataflödet senare om du vill minska svarstiden.                                                                                                                                                                                                                                                    |
   | **Container-ID**  | Poster           | Ange _objekt_ som namn på den nya behållaren. För container-ID:n gäller samma teckenkrav som för databasnamn.                                                                                                                                                                                                                                                               |
   | **Partitionsnyckel** | /category       | Exemplet som beskrivs i den här artikeln använder _/Category_ som partitionsnyckel.                                                                                                                                                                                                                                                                                                           |

   Förutom de föregående inställningarna kan du också lägga till **unika nycklar** för behållaren. Vi lämnar fältet tomt i det här exemplet. Unika nycklar ger utvecklarna möjlighet att lägga till ett lager med dataintegritet till databasen. Genom att skapa en unik nyckel princip när du skapar en behållare, säkerställer du att ett eller flera värden är unika per partitionsnyckel. Läs mer i artikeln om [unika nycklar i Azure Cosmos DB](unique-keys.md).

   Välj **OK**. Datautforskaren visar den nya databasen och containern.

## <a name="add-sample-data"></a>Lägga till exempeldata

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Fråga dina data

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi klona en Node.js app från GitHub, ange anslutnings strängen och köra den.

1. Klona exempellagringsplatsen med följande kommando. Detta kommando skapar en kopia av exempelappen på din dator.

   ```bash
   git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started.git
   ```

## <a name="review-the-code"></a>Granska koden

Det här är valfritt. Om du är intresse rad av att lära dig hur Azure Cosmos Database-resurserna skapas i koden kan du läsa följande kodfragment. Annars kan du gå vidare till [Uppdatera din anslutningssträng](#update-your-connection-string).

Om du är bekant med den tidigare versionen av SQL JavaScript SDK kan du använda den för att Visa villkors _samlingen_ och _dokumentet_. Eftersom Azure Cosmos DB stöder [flera API-modeller](introduction.md), använder [version 2.0 + av JavaScript SDK](https://www.npmjs.com/package/@azure/cosmos) en _behållare_ för generiska termer, som kan vara en samling, Graf eller tabell och _objekt_ för att beskriva behållarens innehåll.

Cosmos DB JavaScript SDK kallas " @azure/cosmos " och kan installeras från NPM...

```bash
npm install @azure/cosmos
```

Följande kodfragment är alla hämtade från filen _app.js_.

- `CosmosClient`Importeras från `@azure/cosmos` NPM-paketet.

  ```javascript
  const CosmosClient = require("@azure/cosmos").CosmosClient;
  ```

- Ett nytt `CosmosClient` objekt har initierats.

  ```javascript
  const client = new CosmosClient({ endpoint, key });
  ```

- Välj databasen "tasks".

  ```javascript
  const database = client.database(databaseId);
  ```

- Välj behållare/samling för "objekt".

  ```javascript
  const container = database.container(containerId);
  ```

- Markera alla objekt i behållaren "Items".

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
> I både metoderna "Update" och "Delete" måste objektet väljas från databasen genom att anropa `container.item()` . De två parametrarna som skickas in är ID för objektet och objektets partitionsnyckel. I det här fallet är partitionera-nyckeln värdet för fältet "kategori".

## <a name="update-your-connection-string"></a>Uppdatera din anslutningssträng

Gå tillbaka till Azure Portal för att hämta information om anslutnings strängen för ditt Azure Cosmos-konto. Kopiera anslutnings strängen till appen så att den kan ansluta till databasen.

1. I ditt Azure Cosmos DB konto i [Azure Portal](https://portal.azure.com/)väljer du **nycklar** i det vänstra navigerings fältet och väljer sedan **Läs-och skriv nycklar**. Använd kopierings knapparna till höger på skärmen för att kopiera URI: n och primär nyckeln till _app.js_ -filen i nästa steg.

   :::image type="content" source="./media/create-sql-api-dotnet/keys.png" alt-text="Visa och kopiera en åtkomstnyckel i Azure Portal, bladet Nycklar":::

2. I öppnar du _config.js_ -filen.

3. Kopiera ditt URI-värde från portalen (med kopierings knappen) och gör det till värdet för slut punkts nyckeln i _config.js_.

   `endpoint: "<Your Azure Cosmos account URI>"`

4. Kopiera sedan värdet för primär nyckeln från portalen och gör det till värdet för `config.key` i _config.js_. Du har nu uppdaterat appen med all information som behövs för kommunikation med Azure Cosmos DB.

   `key: "<Your Azure Cosmos account key>"`

## <a name="run-the-app"></a>Kör appen

1. Kör `npm install` i en Terminal för att installera @azure/cosmos NPM-paketet

2. Kör `node app.js` i en terminal för att starta nodprogrammet.

3. De två objekt som du skapade tidigare i den här snabb starten visas i listan. Ett nytt objekt skapas. Flaggan "isComplete" för objektet uppdateras till "true" och slutligen tas objektet bort.

Du kan fortsätta att experimentera med det här exempel programmet eller gå tillbaka till Datautforskaren, ändra och arbeta med dina data.

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure-portalen

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skapar ett Azure Cosmos DB-konto, skapar en behållare med hjälp av Datautforskaren och kör en Node.js-app. Du kan nu importera ytterligare data till ditt Azure Cosmos DB-konto.

> [!div class="nextstepaction"]
> [Importera data till Azure Cosmos DB](import-data.md)
