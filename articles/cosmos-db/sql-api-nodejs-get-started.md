---
title: Självstudie om Node.js för SQL-API:et för Azure Cosmos DB
description: En Node.js-självstudiekurs som demonstrerar hur du ansluter och frågar Azure Cosmos DB med SQL-API:et
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 04/20/2020
ms.author: dech
ms.custom: devx-track-js
ms.openlocfilehash: 1115d7bb0d6857aa39f246743df54a6cf3fd3676
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93098671"
---
# <a name="tutorial-build-a-nodejs-console-app-with-the-javascript-sdk-to-manage-azure-cosmos-db-sql-api-data"></a>Självstudie: Skapa en Node.js-konsolapp med JavaScript SDK för att hantera data från SQL-API:et för Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](./create-sql-api-java.md)
> * [Async Java](./create-sql-api-java.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Som utvecklare kan du ha program som använder NoSQL-dokumentdata. Du kan använda ett SQL API-konto i Azure Cosmos DB till att lagra och komma åt dessa dokumentdata. Den här självstudien visar hur du skapar ett Node.js-konsolprogram som skapar Azure Cosmos DB-resurser och skickar frågor mot dem.

I de här självstudierna får du:

> [!div class="checklist"]
> * Skapa och ansluta till ett Azure Cosmos DB-konto.
> * Konfigurera ditt program.
> * Skapa en databas.
> * Skapa en container.
> * Lägga till objekt i containern.
> * Utföra grundläggande åtgärder på objekten, containern och databasen.

## <a name="prerequisites"></a>Förutsättningar 

Se till att du har följande resurser:

* Ett aktivt Azure-konto. Om du inte har ett kan du registrera dig för en [kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js](https://nodejs.org/) v6.0.0 eller högre.

## <a name="create-azure-cosmos-db-account"></a>Skapa Azure Cosmos DB-konto

Nu ska vi skapa ett Azure Cosmos DB-konto. Om du redan har ett konto som du vill använda kan du gå vidare till [Konfigurera Node.js-programmet](#SetupNode). Om du använder Azure Cosmos DB-emulatorn följer du stegen i artikeln om [Azure Cosmos DB-emulatorn](local-emulator.md) för att konfigurera emulatorn och gå vidare till [konfigurationen av Node.js-programmet](#SetupNode). 

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="set-up-your-nodejs-application"></a><a id="SetupNode"></a>Konfigurera Node.js-programmet

Innan du börjar skriva kod för att skapa programmet kan du skapa ramverket för din app. Kör följande steg för att konfigurera ditt Node.js-program som har ramverkskoden:

1. Öppna valfri terminal.
2. Leta reda på mappen eller katalogen där du vill spara Node.js-programmet.
3. Skapa tomma JavaScript-filer med följande kommandon:

   * Windows:
     * `fsutil file createnew app.js 0`
     * `fsutil file createnew config.js 0`
     * `md data`
     * `fsutil file createnew data\databaseContext.js 0`

   * Linux/OS X:
     * `touch app.js`
     * `touch config.js`
     * `mkdir data`
     * `touch data/databaseContext.js`

4. Skapa och initiera en `package.json`-fil. Ange följande kommando:
   * ```npm init -y```

5. Installera modulen @azure/cosmos via npm. Ange följande kommando:
   * ```npm install @azure/cosmos --save```

## <a name="set-your-apps-configurations"></a><a id="Config"></a>Ange appens konfigurationer

Nu när din app finns behöver du se till att den kan kommunicera med Azure Cosmos DB. Genom att uppdatera några konfigurationsinställningar, vilket visas i följande steg, kan du konfigurera din app att kommunicera med Azure Cosmos DB:

1. Öppna *config.js* -filen i din favorit text redigerare.

1. Kopiera och klistra in följande kodfragment i *config.js* -filen och ange egenskaperna `endpoint` och `key` till din Azure Cosmos DB slut punkts-URI och primär nyckel. Databasen, behållar namn anges till **uppgifter** och **objekt**. Den partitionsnyckel som du ska använda för det här programmet är **/Category**.

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/config.js":::

   Du hittar information om slut punkt och nyckel i rutan **nycklar** i [Azure Portal](https://portal.azure.com).

   :::image type="content" source="media/sql-api-nodejs-get-started/node-js-tutorial-keys.png" alt-text="Hämta nycklar från Azure-portalen, skärmbild":::

Java Script SDK använder *behållaren* för allmänna villkor och *objektet*. En container kan vara en samling, ett diagram eller en tabell. Ett objekt kan vara ett dokument, en kant/ett hörn eller en rad, och är innehållet i en container. I föregående kodfragment `module.exports = config;` används koden för att exportera config-objektet, så att du kan referera till det i *app.js* -filen.

## <a name="create-a-database-and-a-container"></a>Skapa en databas och en container

1. Öppna *databaseContext.js* -filen i din favorit text redigerare.

1. Kopiera och klistra in följande kod i *databaseContext.js* -filen. Den här koden definierar en funktion som skapar databasen "tasks", "Items" och behållaren om de inte redan finns i ditt Azure Cosmos-konto:

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/data/databaseContext.js" id="createDatabaseAndContainer":::

   En databas är en logisk container med objekt som är partitionerade över containrar. Du skapar en databas med hjälp av antingen `createIfNotExists` funktionen eller Create i **databas** klassen. En behållare består av objekt som i fallet med SQL API är JSON-dokument. Du skapar en behållare genom att använda antingen `createIfNotExists` funktionen eller Create från **containers** -klassen. När du har skapat en behållare kan du lagra och fråga data.

   > [!WARNING]
   > Att skapa en container påverkar prissättningen. Besök [prissättningssidan](https://azure.microsoft.com/pricing/details/cosmos-db/) så att du vet vad du kan förvänta dig.

## <a name="import-the-configuration"></a>Importera konfigurationen

1. Öppna *app.js* -filen i din favorit text redigerare.

1. Kopiera och klistra in koden nedan för att importera `@azure/cosmos` modulen, konfigurationen och databaseContext som du definierade i föregående steg. 

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="ImportConfiguration":::

## <a name="connect-to-the-azure-cosmos-account"></a>Anslut till Azure Cosmos-kontot

I *app.js* -filen kopierar du och klistrar in följande kod för att använda den tidigare sparade slut punkten och nyckeln för att skapa ett nytt CosmosClient-objekt.

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="CreateClientObjectDatabaseContainer":::

> [!Note]
> Om du ansluter till **Cosmos DB emulatorn** inaktiverar du TLS-verifiering för din nods process:
>   ```javascript
>   process.env.NODE_TLS_REJECT_UNAUTHORIZED = "0";
>   const client = new CosmosClient({ endpoint, key });
>   ```

Nu har du koden för att initiera Azure Cosmos DB-klienten och det är dags att gå vidare till att arbeta med Azure Cosmos DB-resurser.

## <a name="query-items"></a><a id="QueryItem"></a>Fråga efter objekt

Azure Cosmos DB stöder omfattande frågor mot JSON-objekt som lagras i varje behållare. Följande exempel kod visar en fråga som du kan köra mot objekten i din behållare. Du kan fråga objekten genom att använda funktionen Query i `Items` klassen. Lägg till följande kod i *app.js* -filen för att fråga efter objekt från ditt Azure Cosmos-konto:

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="QueryItems":::

## <a name="create-an-item"></a><a id="CreateItem"></a>Skapa ett objekt

Du kan skapa ett objekt med hjälp av klassens Create-funktion `Items` . När du använder SQL-API:et projiceras objekt som dokument, som är användardefinierat (godtyckligt) JSON-innehåll. I den här självstudien skapar du ett nytt objekt i databasen Tasks.

1. I app.js-filen definierar du definitionen för objektet:

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="DefineNewItem":::

1. Lägg till följande kod för att skapa det tidigare definierade objektet:

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="CreateItem":::

## <a name="update-an-item"></a><a id="ReplaceItem"></a>Uppdatera ett objekt

Azure Cosmos DB har stöd för att ersätta innehållet i objekt. Kopiera och klistra in följande kod i *app.js* -filen. Den här koden hämtar ett objekt från behållaren och uppdaterar fältet *isComplete* till sant.

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="UpdateItem":::

## <a name="delete-an-item"></a><a id="DeleteItem"></a>Ta bort ett objekt

Azure Cosmos DB har stöd för borttagning av JSON-objekt. Följande kod visar hur du hämtar ett objekt efter dess ID och tar bort det. Kopiera och klistra in följande kod för att *app.js* fil:

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="DeleteItem":::

## <a name="run-your-nodejs-application"></a><a id="Run"></a>Köra Node.js-programmet

Sammantaget bör koden se ut så här:

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js":::

Leta upp filen ```app.js``` i terminalen och kör kommandot:

```bash 
node app.js
```

Du bör nu se utdata från din kom-igång-app. Dina utdata bör motsvara exempeltexten nedan.

```
Created database:
Tasks

Created container:
Items

Querying container: Items
1 - Pick up apples and strawberries.

Created new item: 3 - Complete Cosmos DB Node.js Quickstart ⚡

Updated item: 3 - Complete Cosmos DB Node.js Quickstart ⚡
Updated isComplete to true

Deleted item with id: 3
```

## <a name="get-the-complete-nodejs-tutorial-solution"></a><a id="GetSolution"></a>Hämta den fullständiga lösningen till Node.js-självstudien

Om du inte har tid att slutföra stegen i den här självstudien eller bara vill hämta koden kan du hämta den från [GitHub](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started ).

För att köra den komma igång-lösning som innehåller all kod i den här artikeln behöver du följande:

* Ett [Azure Cosmos DB-konto][create-account].
* Lösningen [Komma igång](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started) är tillgänglig på GitHub.

Installera projektets beroenden via npm. Ange följande kommando:

* ```npm install``` 

```config.js```Uppdatera sedan värdena config. Endpoint och config. Key i filen enligt beskrivningen i [steg 3: Ange appens konfigurationer](#Config).  

Leta reda på fin ```app.js```-fil i terminalen och kör kommandot:  

```bash  
node app.js 
```

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort resursgruppen, Azure Cosmos DB-kontot och alla relaterade resurser när de inte längre behövs. Om du vill göra detta markerar du den resursgrupp som du använde för Azure Cosmos DB-kontot. Välj sedan **Ta bort** och bekräfta namnet på den resursgrupp som du vill ta bort.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Övervaka ett Azure Cosmos DB-konto](./monitor-cosmos-db.md)

[create-account]: create-sql-api-dotnet.md#create-account