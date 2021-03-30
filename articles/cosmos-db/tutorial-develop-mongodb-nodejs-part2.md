---
title: Skapa Node.js Express-app med Azure Cosmos DB s API för MongoDB (part2)
description: Del 2 i självstudieserien om hur du skapar en MongoDB-app med Angular och Node i Azure Cosmos DB med exakt samma API:er som du använder för MongoDB.
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jopapa
ms.custom: seodec18
ms.reviewer: sngun
ms.openlocfilehash: e3af713d6e120e66a0cde5116f4556bb8caedf55
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93097863"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---create-a-nodejs-express-app"></a>Skapa en Angular-app med Azure Cosmos DB:s API för MongoDB – skapa en Node.js Express-app
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Den här självstudien i flera delar visar hur du skapar en ny app skriven i Node.js med Express och Angular och sedan ansluter den till ditt [Cosmos-konto konfigurerat med Cosmos DB:s API för MongoDB](mongodb-introduction.md).

Del 2 av kursen bygger vidare på [introduktionen](tutorial-develop-mongodb-nodejs.md) och består av följande uppgifter:

> [!div class="checklist"]
> * Installera Angular CLI och TypeScript
> * Skapa ett nytt projekt med hjälp av Angular
> * Utveckla appen med hjälp av Express-ramverket
> * Testa appen i Postman

## <a name="video-walkthrough"></a>Videogenomgång

> [!VIDEO https://www.youtube.com/embed/lIwJIYcGSUg]

## <a name="prerequisites"></a>Förutsättningar

Titta på [introduktionsvideon](tutorial-develop-mongodb-nodejs.md) innan du påbörjar den här delen av självstudiekursen.

Den här kursen kräver även följande: 
* [Node.js](https://nodejs.org/) version 8.4.0 eller senare.
* [Postman](https://www.getpostman.com/)
* [Visual Studio Code](https://code.visualstudio.com/) eller valfri kodredigerare.

> [!TIP]
> Den här självstudiekursen beskriver steg för steg hur du skapar programmet. Om du vill hämta det färdiga projektet kan du ladda ned programmet från [angular-cosmosdb-databasen](https://github.com/Azure-Samples/angular-cosmosdb) på GitHub.

## <a name="install-the-angular-cli-and-typescript"></a>Installera Angular CLI och TypeScript

1. Öppna en kommandotolk i Windows eller ett terminalfönster på Mac och installera Angular CLI.

    ```bash
    npm install -g @angular/cli
    ```

2. Installera TypeScript genom att skriva följande kommando i kommandotolken. 

    ```bash
    npm install -g typescript
    ```

## <a name="use-the-angular-cli-to-create-a-new-project"></a>Skapa ett nytt projekt med hjälp av Angular CLI

1. I kommandotolken byter du till den mapp där du vill skapa det nya projektet och kör följande kommando. Det här kommandot skapar en ny mapp och ett projekt med namnet angular-cosmosdb och installerar Angular-komponenterna som krävs för en ny app. Kommandot kör en minimal installation (--minimal) och anger att projektet använder Sass (en CSS-liknande syntax med flaggan --style scss).

    ```bash
    ng new angular-cosmosdb --minimal --style scss
    ```

2. När kommandot har slutförts byter du katalog till mappen src/client.

    ```bash
    cd angular-cosmosdb
    ```

3. Öppna sedan mappen i Visual Studio Code.

    ```bash
    code .
    ```

## <a name="build-the-app-using-the-express-framework"></a>Utveckla appen med hjälp av Express-ramverket

1. Högerklicka på mappen **src** i **Explorer**-fönstret i Visual Studio Code, klicka på **Ny mapp** och ge den nya mappen namnet *server*.

2. Högerklicka på mappen **server** i **Explorer**-fönstret, klicka på **Ny fil** och ge den nya filen namnet *index.js*.

3. Tillbaka i kommandotolken installerar du body-parser genom att köra följande kommando. På så sätt kan appen parsa JSON-data som skickas via API:erna.

    ```bash
    npm i express body-parser --save
    ```

4. I Visual Studio Code kopierar du följande kod till filen index.js. Den här koden:
    * Refererar till Express
    * Hämtar body-parser för läsning av JSON-data i innehållet i begäranden
    * Använder en inbyggd funktion kallad path
    * Anger rotvariabler så att det blir lättare att se var koden finns
    * Konfigurerar en port
    * Sätter igång Express
    * Anger hur appen ska använda mellanprogrammet som vi ska använda för att leverera innehåll till servern
    * Tillhandahåller allt som finns i dist-mappen, som är det statiska innehållet
    * Visar programmet, och visar index.html för GET-begäranden som inte hittas på servern (för djuplänkar)
    * Startar servern med app.listen
    * Använder en pil-funktion för att logga att porten är aktiv
    
   ```node
   const express = require('express');
   const bodyParser = require('body-parser');
   const path = require('path');
   const routes = require('./routes');

   const root = './';
   const port = process.env.PORT || '3000';
   const app = express();

   app.use(bodyParser.json());
   app.use(bodyParser.urlencoded({ extended: false }));
   app.use(express.static(path.join(root, 'dist/angular-cosmosdb')));
   app.use('/api', routes);
   app.get('*', (req, res) => {
     res.sendFile('dist/angular-cosmosdb/index.html', {root});
   });

   app.listen(port, () => console.log(`API running on localhost:${port}`));
   ```

5. Högerklicka på mappen **server** i **Explorer**-fönstret i Visual Studio Code och klicka på **Ny fil**. Ge den nya filen namnet *routes.js*. 

6. Kopiera följande kod till **routes.js**. Den här koden:
   * Refererar till Express-routern
   * Hämtar heroes-komponenterna
   * Skickar tillbaka JSON för en definierad hero-komponent

   ```node
   const express = require('express');
   const router = express.Router();

   router.get('/heroes', (req, res) => {
    res.send(200, [
       {"id": 10, "name": "Starlord", "saying": "oh yeah"}
    ])
   });

   module.exports=router;
   ```

7. Sparar alla ändrade filer 

8. Klicka på knappen **Felsök** i Visual Studio Code :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part2/debug-button.png"::: , klicka på kugg hjuls knappen :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part2/gear-button.png"::: . Den nya launch.json-filen öppnas i Visual Studio Code. 

8. Ändra `"${workspaceFolder}\\server"` till `"program": "${workspaceRoot}/src/server/index.js"` på rad 11 i launch.json och spara filen.

9. Klicka på knappen **Starta fel sökning** :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part2/start-debugging-button.png"::: för att köra appen.

    Appen bör köras utan fel.

## <a name="use-postman-to-test-the-app"></a>Testa appen med hjälp av Postman

1. Öppna Postman och ange `http://localhost:3000/api/heroes` i rutan GET. 

2. Klicka på **Skicka** så returneras json-svaret från appen. 

    Svaret visar att appen är aktiv och körs lokalt. 

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part2/azure-cosmos-db-postman.png" alt-text="Postman som visar begäran och svaret":::


## <a name="next-steps"></a>Nästa steg

I den här delen av självstudiekursen har du gjort följande:

> [!div class="checklist"]
> * Skapat ett Node.js-projekt med hjälp av Angular CLI
> * Testat appen med hjälp av Postman

Fortsätt till nästa del av självstudiekursen och skapa användargränssnittet.

> [!div class="nextstepaction"]
> [Skapa användargränssnittet med Angular](tutorial-develop-mongodb-nodejs-part3.md)
