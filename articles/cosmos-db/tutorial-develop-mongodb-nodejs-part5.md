---
title: Anslut vinkel appen till Azure Cosmos DB s API för MongoDB med Mongoose
description: I den här självstudien beskrivs hur du skapar en Node.js-app med hjälp av Angular och Express för att hantera data som lagras i Cosmos DB. I den här delen använder du Mongoose till att ansluta till Azure Cosmos DB.
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jopapa
ms.custom: seodec18, devx-track-js
ms.reviewer: sngun
ms.openlocfilehash: a3097fa539f460ef5e8ffe73598fa5d55516717e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93097812"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---use-mongoose-to-connect-to-cosmos-db"></a>Skapa en Angular-app med Azure Cosmos DB:s API för MongoDB – Ansluta till Cosmos DB med hjälp av Mongoose
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Den här självstudien i flera delar visar hur du skapar en Node.js-app med Express och Angular, ansluter den till ditt [Cosmos-konto konfigurerat med Cosmos DB:s API för MongoDB](mongodb-introduction.md). Den här artikeln beskriver del 5 av självstudien och bygger på [del 4](tutorial-develop-mongodb-nodejs-part4.md).

I den här delen av självstudien får du göra följande:

> [!div class="checklist"]
> * Ansluta till Cosmos DB med hjälp av Mongoose.
> * Hämta Cosmos DB-anslutningssträngen.
> * Skapa Hero-modellen.
> * Skapa Hero-tjänsten för att hämta Hero-data.
> * Kör appen lokalt.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

* Innan du börjar med den här självstudien bör du slutföra stegen i [del 4](tutorial-develop-mongodb-nodejs-part4.md).

* Den här självstudien kräver att du kör Azure CLI lokalt. Du måste ha Azure CLI-version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera Azure CLI kan du läsa mer i [Installera Azure CLI 2.0](/cli/azure/install-azure-cli).

* Den här självstudien beskriver steg för steg hur du skapar programmet. Om du vill hämta det färdiga projektet kan du ladda ned programmet från [angular-cosmosdb-databasen](https://github.com/Azure-Samples/angular-cosmosdb) på GitHub.

## <a name="use-mongoose-to-connect"></a>Ansluta med hjälp av Mongoose

Mongoose är ett bibliotek för modellering av objektdata (ODM) för MongoDB och Node.js. Du kan använda Mongoose till att ansluta till ditt Azure Cosmos DB-konto. Installera Mongoose och anslut till Azure Cosmos DB med hjälp av följande steg:

1. Installera mongoose npm-modulen, som är ett API som används för att kommunicera med MongoDB.

    ```bash
    npm i mongoose --save
    ```

1. I mappen **server** skapar du en fil med namnet **mongo.js**. Du lägger till anslutningsinformationen för Azure Cosmos DB-kontot i den här filen.

1. Kopiera följande kod till filen **mongo.js**. Koden innehåller följande funktioner:

   * Kräver Mongoose.
   * Åsidosätter Mongo-löftet att använda det grundläggande löftet som är inbyggt i ES6/ES2015 och senare versioner.
   * Anropar en env-fil som du kan använda för att konfigurera vissa saker beroende på om du arbetar med mellanlagring, produktion eller utveckling. Du skapar den filen i nästa avsnitt.
   * Inkluderar MongoDB-anslutningssträngen, som anges i env-filen.
   * Skapar en anslutningsfunktion som anropar Mongoose.

     ```javascript
     const mongoose = require('mongoose');
     /**
     * Set to Node.js native promises
     * Per https://mongoosejs.com/docs/promises.html
     */
     mongoose.Promise = global.Promise;

     const env = require('./env/environment');

     // eslint-disable-next-line max-len
     const mongoUri = `mongodb://${env.accountName}:${env.key}@${env.accountName}.documents.azure.com:${env.port}/${env.databaseName}?ssl=true`;

     function connect() {
     mongoose.set('debug', true);
     return mongoose.connect(mongoUri, { useMongoClient: true });
     }

     module.exports = {
     connect,
     mongoose
     };
     ```
    
1. I Explorer-fönstret, under **server**, skapar du en mapp med namnet **environment**. I mappen **environment** skapar du en fil med namnet **environment.js**.

1. Från filen mongo.js måste vi inkludera värden för parametrarna `dbName`, `key` och `cosmosPort`. Kopiera följande kod i filen **environment.js**:

    ```javascript
    // TODO: replace if yours are different
    module.exports = {
      accountName: 'your-cosmosdb-account-name-goes-here',
      databaseName: 'admin', 
      key: 'your-key-goes-here',
      port: 10255
    };
    ```

## <a name="get-the-connection-string"></a>Hämta anslutningssträngen

För att kunna ansluta din app till Azure Cosmos DB måste du uppdatera konfigurationsinställningarna för appen. Uppdatera inställningarna med hjälp av följande steg: 

1. I Azure-portalen hämtar du portnummer, Azure Cosmos DB-kontots namn och primärnyckelvärden för ditt Azure Cosmos DB-konto.

1. I filen **environment.js** ändrar du värdet för `port` till 10255. 

    ```javascript
    const port = 10255;
    ```

1. I filen **environment.js** ändrar du värdet för `accountName` till namnet på det Azure Cosmos DB-konto du skapade i [del 4](tutorial-develop-mongodb-nodejs-part4.md) av självstudien. 

1. Hämta primärnyckeln för Azure Cosmos DB-kontot genom att köra följande kommando i kommandoradsgränssnittet i terminalfönstret: 

    ```azurecli-interactive
    az cosmosdb list-keys --name <cosmosdb-name> -g myResourceGroup
    ```    
    
    \<cosmosdb-name> är namnet på det Azure Cosmos DB konto som du skapade i [del 4](tutorial-develop-mongodb-nodejs-part4.md) av själv studie kursen.

1. Kopiera primärnyckeln till filen **environment.js** som värdet för `key`.

Nu har appen all nödvändig information för att ansluta till Azure Cosmos DB. 

## <a name="create-a-hero-model"></a>Skapa en Hero-modell

Som nästa steg måste du definiera schemat för data som ska lagras i Azure Cosmos DB genom att definiera en modellfil. Skapa med hjälpa av följande steg en _Hero-modell_ som definierar schemat för data:

1. I Explorer-fönstret, under mappen **server** skapar du en fil med namnet **hero.model.js**.

1. Kopiera följande kod till filen **hero.model.js**. Koden innehåller följande funktioner:

   * Kräver Mongoose.
   * Skapar ett nytt schema med ett id, ett namn och en fras.
   * Skapar en modell med hjälp av schemat.
   * Exporterar modellen. 
   * Ge samlingen namnet **Heroes** (i stället för **Heros**, som är standardnamnet för samlingen baserat på reglerna för namn i plural i Mongoose).

   ```javascript
   const mongoose = require('mongoose');

   const Schema = mongoose.Schema;

   const heroSchema = new Schema(
     {
       id: { type: Number, required: true, unique: true },
       name: String,
       saying: String
     },
     {
       collection: 'Heroes'
     }
   );

   const Hero = mongoose.model('Hero', heroSchema);

   module.exports = Hero;
   ```

## <a name="create-a-hero-service"></a>Skapa en Hero-tjänst

När du har skapat Hero-modellen måste du definiera en tjänst för att läsa data och utföra åtgärderna list, create, delete och update. Skapa med hjälp av följande steg en _Hero-tjänst_ som frågar efter data från Azure Cosmos DB:

1. I Explorer-fönstret, under mappen **server** skapar du en fil med namnet **hero.service.js**.

1. Kopiera följande kod till filen **hero.service.js**. Koden innehåller följande funktioner:

   * Hämtar modellen du har skapat.
   * Ansluter till databasen.
   * Skapar en `docquery`-variabel som använder metoden `hero.find` till att definiera en fråga som returnerar alla heroes-komponenter.
   * Kör en fråga med funktionen `docquery.exec` med ett löfte om att hämta en lista över alla heroes-komponenter, där svarsstatusen är 200. 
   * Returnerar felmeddelandet om statusen är 500.
   * Hämtar heroes-komponenterna eftersom vi använder moduler. 

   ```javascript
   const Hero = require('./hero.model');

   require('./mongo').connect();

   function getHeroes() {
     const docquery = Hero.find({});
     docquery
       .exec()
       .then(heroes => {
         res.status(200).json(heroes);
       })
       .catch(error => {
         res.status(500).send(error);
         return;
       });
   }

   module.exports = {
     getHeroes
   };
   ```

## <a name="configure-routes"></a>Konfigurera vägar

Som nästa steg måste du konfigurera väger för att hantera URL:erna för begärandena get, create, read och delete. Routningsmetoderna anger återanropsfunktioner (s.k. _hanterarfunktioner_). Dessa funktioner anropas när appen tar emot en begäran till den angivna slutpunkten och HTTP-metoden. Lägg till Hero-tjänsten och definiera vägarna med hjälp av följande steg:

1. I Visual Studio Code, i filen **routes.js**, kommenterar du ut funktionen `res.send` som skickar exemplen på hero-data. Lägg till en rad för att istället anropa funktionen `heroService.getHeroes`.

    ```javascript
    router.get('/heroes', (req, res) => {
      heroService.getHeroes(req, res);
    //  res.send(200, [
    //      {"id": 10, "name": "Starlord", "saying": "oh yeah"}
    //  ])
    });
    ```

1. I filen **routes.js** anger du `require` för hero-tjänsten:

    ```javascript
    const heroService = require('./hero.service'); 
    ```

1. I filen **hero.service.js** uppdaterar du funktionen `getHeroes` så att den tar parametrarna `req` och `res` enligt följande:

    ```javascript
    function getHeroes(req, res) {
    ```

Nu ska vi ägna en minut åt att gå igenom den tidigare koden. Först kommer vi till filen index.js, som konfigurerar nodservern. Observera att den konfigurerar och definierar vägarna. Sedan kommunicerar routes.js-filen med hero-tjänsten och beordrar den att hämta dina funktioner, som **getHeroes**, och att skicka begäran och svaret. Filen hero.service.js hämtar modellen och ansluter till Mongo. Sedan körs **getHeroes** när vi anropar den och returnerar svaret 200. 

## <a name="run-the-app"></a>Kör appen

Kör sedan appen med hjälp av följande steg:

1. Spara alla ändringar i Visual Studio Code. Välj knappen **Felsök** till vänster :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part5/debug-button.png"::: och välj sedan knappen **Starta fel sökning** :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part5/start-debugging-button.png"::: .

1. Byt nu till webbläsaren. Öppna **utvecklarverktyg** och **fliken nätverk**. Gå till så `http://localhost:3000` ser du vårt program.

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part5/azure-cosmos-db-heroes-app.png" alt-text="Nytt Azure Cosmos DB-konto på Azure-portalen":::

Det finns ännu inga heroes-komponenter lagrade i appen. I nästa del av den här självstudien lägger vi till funktioner put, push och delete. Sedan kan vi lägga till, uppdatera och ta bort hjältar från användar gränssnittet genom att använda Mongoose-anslutningar till vår Azure Cosmos-databas. 

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort resursgruppen, Azure Cosmos DB-kontot och alla relaterade resurser när de inte längre behövs. Ta bort resursgruppen med hjälp av följande steg:

 1. Gå till den resursgrupp där du har skapat Azure Cosmos DB-kontot.
 1. Välj **Ta bort resursgrupp**.
 1. Bekräfta namnet på den resursgrupp som ska tas bort och välj sedan **Ta bort**.

## <a name="next-steps"></a>Nästa steg

Fortsätt till del 6 av självstudien för att lägga till Post-, Put- och Delete-funktioner i appen:

> [!div class="nextstepaction"]
> [Del 6: Lägg till post-, placerings-och borttagnings funktioner i appen](tutorial-develop-mongodb-nodejs-part6.md)