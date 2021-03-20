---
title: Lägg till CRUD-funktioner i en vinkel-app med Azure Cosmos DB s API för MongoDB
description: Del 6 i självstudieserien om hur du skapar en MongoDB-app med Angular och Node i Azure Cosmos DB med exakt samma API:er som du använder för MongoDB.
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jopapa
ms.custom: seodec18, devx-track-js
ms.reviewer: sngun
ms.openlocfilehash: c8e2c707566b08219b495e76be7f6f6130d876ab
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93081322"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---add-crud-functions-to-the-app"></a>Skapa en Angular-app med Azure Cosmos DB:s API för MongoDB – Lägga till CRUD-funktioner i appen
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Den här självstudien i flera delar visar hur du skapar en ny app skriven i Node.js med Express och Angular och sedan ansluter den till ditt [Cosmos-konto konfigurerat med Cosmos DB:s API för MongoDB](mongodb-introduction.md). Del 6 av självstudiekursen bygger vidare på [del 5](tutorial-develop-mongodb-nodejs-part5.md) och består av följande uppgifter:

> [!div class="checklist"]
> * Skapa Post-, Put- och Delete-funktioner för hero-tjänsten
> * Kör appen

> [!VIDEO https://www.youtube.com/embed/Y5mdAlFGZjc]

## <a name="prerequisites"></a>Förutsättningar

Utför stegen i [del 5](tutorial-develop-mongodb-nodejs-part5.md) av självstudiekursen innan du påbörjar den här delen.

> [!TIP]
> Den här självstudiekursen beskriver steg för steg hur du skapar programmet. Om du vill hämta det färdiga projektet kan du ladda ned programmet från [angular-cosmosdb-databasen](https://github.com/Azure-Samples/angular-cosmosdb) på GitHub.

## <a name="add-a-post-function-to-the-hero-service"></a>Lägga till en Post-funktion till hero-tjänsten

1. I Visual Studio Code öppnar du **routes.js** och **hero.service.js** sida vid sida genom att trycka på knappen **dela redigeraren** :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part6/split-editor-button.png"::: .

    Notera att rad 7 i routes.js anropar funktionen `getHeroes` på rad 5 i **hero.service.js**.  Vi måste skapa samma koppling för post-, put- och delete-funktionerna. 

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part6/routes-heroservicejs.png" alt-text="routes.js och hero.service.js i Visual Studio Code":::
    
    Vi börjar med att lägga till kod till hero-tjänsten. 

2. Kopiera följande kod till **hero.service.js** efter `getHeroes`-funktionen och före `module.exports`. Den här koden:  
   * Använder hero-modellen för att publicera en ny hero.
   * Kontrollerar svaren för att se om det finns fel och returnerar statusvärdet 500.

   ```javascript
   function postHero(req, res) {
     const originalHero = { uid: req.body.uid, name: req.body.name, saying: req.body.saying };
     const hero = new Hero(originalHero);
     hero.save(error => {
       if (checkServerError(res, error)) return;
       res.status(201).json(hero);
       console.log('Hero created successfully!');
     });
   }

   function checkServerError(res, error) {
     if (error) {
       res.status(500).send(error);
       return error;
     }
   }
   ```

3. Uppdatera `module.exports` i **hero.service.js** så att den innehåller den nya `postHero`-funktionen. 

    ```javascript
    module.exports = {
      getHeroes,
      postHero
    };
    ```

4. I **routes.js** lägger du till ett router-element för funktionen `post` efter router-elementet för `get`. Det här router-elementet publicerar en hero i taget. Den här strukturen i router-filen visar tydligt alla tillgängliga API-slutpunkter och överlåter det riktiga arbetet åt **hero.service.js**-filen.

    ```javascript
    router.post('/hero', (req, res) => {
      heroService.postHero(req, res);
    });
    ```

5. Kontrollera att allt fungerar som det ska genom att köra appen. Spara alla ändringar i Visual Studio Code, Välj knappen **Felsök** :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part6/debug-button.png"::: på vänster sida och välj sedan knappen **Starta fel sökning** :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part6/start-debugging-button.png"::: .

6. Gå tillbaka till webbläsaren och öppna fliken Nätverk under Utvecklarverktyg. På de flesta datorer trycker du på F12 för att göra det. Gå till `http://localhost:3000` för att se alla anrop som gjorts i nätverket.

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part6/add-new-hero.png" alt-text="Fliken Nätverk i Chrome som visar nätverksaktivitet":::

7. Lägg till en ny hero genom att välja knappen **Add New Hero** (Lägg till ny hero). Ange ID = "999", name = "Fred" och saying = "Hello" och välj sedan **Spara**. På fliken Nätverk kan du se att du har skickat en POST-begäran om en ny hero. 

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part6/post-new-hero.png" alt-text="Fliken Nätverk i Chrome som visar nätverksaktivitet för Get- och Post-funktioner":::

    Nu ska vi gå tillbaka och lägga till Put- och Delete-funktionerna till appen.

## <a name="add-the-put-and-delete-functions"></a>Lägga till Put- och Delete-funktionerna

1. Lägg till router-elementen för `put` och `delete` efter router-elementet för post i **routes.js**.

    ```javascript
    router.put('/hero/:uid', (req, res) => {
      heroService.putHero(req, res);
    });

    router.delete('/hero/:uid', (req, res) => {
      heroService.deleteHero(req, res);
    });
    ```

2. Kopiera följande kod till **hero.service.js** efter `checkServerError`-funktionen. Den här koden:
   * Skapar `put`- och `delete`-funktionerna.
   * Kontrollerar om hero-komponenten hittades.
   * Utför felhantering. 

   ```javascript
   function putHero(req, res) {
     const originalHero = {
       uid: parseInt(req.params.uid, 10),
       name: req.body.name,
       saying: req.body.saying
     };
     Hero.findOne({ uid: originalHero.uid }, (error, hero) => {
       if (checkServerError(res, error)) return;
       if (!checkFound(res, hero)) return;

      hero.name = originalHero.name;
       hero.saying = originalHero.saying;
       hero.save(error => {
         if (checkServerError(res, error)) return;
         res.status(200).json(hero);
         console.log('Hero updated successfully!');
       });
     });
   }

   function deleteHero(req, res) {
     const uid = parseInt(req.params.uid, 10);
     Hero.findOneAndRemove({ uid: uid })
       .then(hero => {
         if (!checkFound(res, hero)) return;
         res.status(200).json(hero);
         console.log('Hero deleted successfully!');
       })
       .catch(error => {
         if (checkServerError(res, error)) return;
       });
   }

   function checkFound(res, hero) {
     if (!hero) {
       res.status(404).send('Hero not found.');
       return;
     }
     return hero;
   }
   ```

3. Exportera de nya modulerna i **hero.service.js**:

   ```javascript
    module.exports = {
      getHeroes,
      postHero,
      putHero,
      deleteHero
    };
    ```

4. Nu när vi har uppdaterat koden väljer du knappen **starta om** :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part6/restart-debugger-button.png"::: i Visual Studio Code.

5. Uppdatera sidan i webbläsaren och välj knappen **Add New Hero** (Lägg till ny hero). Lägg till en ny hero med ID = "9", name = "Starlord" och saying = "Hi". Välj den nya hero-komponenten genom att klicka på **Spara**.

6. Välj hero-komponenten **Starlord**, ändra saying från "Hi" till "Bye" och välj sedan knappen **Spara**. 

    Nu kan du visa nyttolasten genom att välja ID:t på fliken Nätverk. Som du ser i nyttolasten har saying nu värdet "Bye".

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part6/put-hero-function.png" alt-text="Hero-app och fliken Nätverk som visar nyttolasten"::: 

    Du kan också ta bort en av dessa hero-komponenter i användargränssnittet och se hur lång tid det tar att slutföra borttagningsåtgärden. Testa detta genom att välja knappen Delete för hero-komponenten med namnet Fred.

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part6/times.png" alt-text="Hero-app och fliken Nätverk som visar hur lång tid det tar att slutföra funktionerna"::: 

    Om du uppdaterar sidan visar fliken Nätverk hur lång tid det tar att hämta hero-komponenterna. Även om tiderna är korta beror mycket på var i världen dina data finns och på din möjlighet att geo-replikera dem i ett område nära användarna. Mer information om geo-replikering finns i nästa självstudiekurs, som kommer snart.

## <a name="next-steps"></a>Nästa steg

I den här delen av självstudiekursen har du gjort följande:

> [!div class="checklist"]
> * Lagt till Post-, Put- och Delete-funktioner till appen 

Håll utkik efter fler videor i den här självstudieserien.

