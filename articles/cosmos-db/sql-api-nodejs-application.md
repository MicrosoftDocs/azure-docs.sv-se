---
title: 'Självstudie: Bygg en Node.js-webbapp med Azure Cosmos DB JavaScript SDK för att hantera SQL API-data'
description: I den här självstudien för Node.js visar vi hur du använder Microsoft Azure Cosmos DB till att lagra och komma åt data från ett Node.js Express-webbprogram i Web Apps-funktionen i Microsoft Azure App Service.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: sngun
ms.custom: devx-track-js
ms.openlocfilehash: 49cf54bda985f7d97b2db6a3ada7859aee829cff
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97359548"
---
# <a name="tutorial-build-a-nodejs-web-app-using-the-javascript-sdk-to-manage-a-sql-api-account-in-azure-cosmos-db"></a>Självstudie: Bygg en Node.js-webbapp med hjälp av Java Script SDK för att hantera ett SQL API-konto i Azure Cosmos DB 
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](./create-sql-api-python.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

Som utvecklare kan du ha program som använder NoSQL-dokumentdata. Du kan använda ett SQL API-konto i Azure Cosmos DB till att lagra och komma åt dessa dokumentdata. I självstudien om Node.js beskrivs hur du lagrar och kommer åt data från ett SQL API-konto i Azure Cosmos DB genom att använda ett Node.js Express-program som finns i Web Apps-funktionen i Microsoft Azure App Service. I självstudien skapar du ett webbaserat program (en att göra-app) där du kan skapa, hämta och slutföra uppgifter. Uppgifterna lagras som JSON-dokument i Azure Cosmos DB. 

Självstudien visar hur du skapar ett SQL API-konto i Azure Cosmos DB med hjälp av Azure Portal. Du skapar och kör sedan en webbapp som bygger på Node.js SDK för att skapa en databas och en container, samt lägga till objekt i containern. I den här självstudien används Java Script SDK version 3,0.

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Skapa ett Azure Cosmos DB-konto
> * Skapa ett nytt Node.js-program
> * Ansluta programmet till Azure Cosmos DB
> * Köra och distribuera programmet till Azure

## <a name="prerequisites"></a><a name="_Toc395783176"></a>Förutsättningar

Innan du följer anvisningarna i den här artikeln bör du se till att du har följande resurser:

* Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js][Node.js] version 6.10 eller senare.
* [Express generator](https://www.expressjs.com/starter/generator.html) (du kan installera Express via `npm install express-generator -g`)
* Installera [Git][Git] på den lokala arbetsstationen.

## <a name="create-an-azure-cosmos-db-account"></a><a name="_Toc395637761"></a>Skapa ett Azure Cosmos DB-konto
Vi ska börja med att skapa ett Azure Cosmos DB-konto. Om du redan har ett konto eller om du använder Azure Cosmos DB-emulatorn för den här kursen kan du gå vidare till [Steg 2: Skapa ett nytt Node.js-program](#_Toc395783178).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [cosmos-db-keys](../../includes/cosmos-db-keys.md)]

## <a name="create-a-new-nodejs-application"></a><a name="_Toc395783178"></a>Skapa ett nytt Node.js-program
Nu ska vi skapa ett grundläggande Hello World Node.js-projekt med Express-ramverket.

1. Öppna din favoritterminal, till exempel Node.js-kommandotolken.

1. Navigera till den katalog där du vill lagra det nya programmet.

1. Skapa ett nytt program kallat **todo** med hjälp av Express Generator.

   ```bash
   express todo
   ```

1. Öppna **todo**-katalogen och installera beroenden.

   ```bash
   cd todo
   npm install
   ```

1. Kör det nya programmet.

   ```bash
   npm start
   ```

1. Du kan visa det nya programmet genom att öppna `http://localhost:3000` i webbläsaren.
   
   :::image type="content" source="./media/sql-api-nodejs-application/cosmos-db-node-js-express.png" alt-text="Lär dig använda Node.js – Skärmdump av programmet Hello World i ett webbläsarfönster":::

   Stoppa programmet genom att använda CTRL + C i terminalfönstret och välj **y** för att avbryta batch-jobbet.

## <a name="install-the-required-modules"></a><a name="_Toc395783179"></a>Installera de moduler som krävs

Filen **package.json** är en av filerna som skapas i projektets rot. Den här filen innehåller en lista över ytterligare moduler som krävs för Node.js-programmet. När du distribuerar det här programmet till Azure används den här filen för att avgöra vilka moduler som ska installeras på Azure för att stödja ditt program. Installera ytterligare två paket för den här självstudien.

1. Installera **\@ Azure/Cosmos** -modulen via NPM. 

   ```bash
   npm install @azure/cosmos
   ```

## <a name="connect-the-nodejs-application-to-azure-cosmos-db"></a><a name="_Toc395783180"></a>Ansluta Node.js-programmet till Azure Cosmos DB
Nu när du har slutfört den första installationen och konfigurationen kommer du att skriva kod som krävs av att göra-programmet för att kommunicera med Azure Cosmos DB.

### <a name="create-the-model"></a>Skapa modellen
1. Skapa en ny katalog med namnet **modeller** i roten i projekt katalogen.  

2. I katalogen **models** skapar du en ny fil med namnet **taskDao.js**. Den här filen innehåller den kod som krävs för att skapa databasen och containern. Den definierar även metoder för att läsa, uppdatera, skapa och hitta aktiviteter i Azure Cosmos DB. 

3. Kopiera följande kod till **taskDao.js** -filen:

   ```javascript
    // @ts-check
    const CosmosClient = require('@azure/cosmos').CosmosClient
    const debug = require('debug')('todo:taskDao')

    // For simplicity we'll set a constant partition key
    const partitionKey = undefined
    class TaskDao {
      /**
       * Manages reading, adding, and updating Tasks in Cosmos DB
       * @param {CosmosClient} cosmosClient
       * @param {string} databaseId
       * @param {string} containerId
       */
      constructor(cosmosClient, databaseId, containerId) {
        this.client = cosmosClient
        this.databaseId = databaseId
        this.collectionId = containerId

        this.database = null
        this.container = null
      }

      async init() {
        debug('Setting up the database...')
        const dbResponse = await this.client.databases.createIfNotExists({
          id: this.databaseId
        })
        this.database = dbResponse.database
        debug('Setting up the database...done!')
        debug('Setting up the container...')
        const coResponse = await this.database.containers.createIfNotExists({
          id: this.collectionId
        })
        this.container = coResponse.container
        debug('Setting up the container...done!')
      }

      async find(querySpec) {
        debug('Querying for items from the database')
        if (!this.container) {
          throw new Error('Collection is not initialized.')
        }
        const { resources } = await this.container.items.query(querySpec).fetchAll()
        return resources
      }

      async addItem(item) {
        debug('Adding an item to the database')
        item.date = Date.now()
        item.completed = false
        const { resource: doc } = await this.container.items.create(item)
        return doc
      }

      async updateItem(itemId) {
        debug('Update an item in the database')
        const doc = await this.getItem(itemId)
        doc.completed = true

        const { resource: replaced } = await this.container
          .item(itemId, partitionKey)
          .replace(doc)
        return replaced
      }

      async getItem(itemId) {
        debug('Getting an item from the database')
        const { resource } = await this.container.item(itemId, partitionKey).read()
        return resource
      }
    }

    module.exports = TaskDao
   ```
4. Spara och stäng filen **taskDao.js**.  

### <a name="create-the-controller"></a>Skapa styrningen

1. I projektets **routes**-katalog skapar du en ny fil med namnet **tasklist.js**.  

2. Lägg till följande kod i **tasklist.js**. Den här koden läser in modulerna CosmosClient och async, som används av **tasklist.js**. Koden definierar även klassen **TaskList**, som skickas som en instans av det **TaskDao**-objekt som vi definierade tidigare:
   
   ```javascript
    const TaskDao = require("../models/TaskDao");
    
    class TaskList {
      /**
       * Handles the various APIs for displaying and managing tasks
       * @param {TaskDao} taskDao
       */
      constructor(taskDao) {
        this.taskDao = taskDao;
      }
      async showTasks(req, res) {
        const querySpec = {
          query: "SELECT * FROM root r WHERE r.completed=@completed",
          parameters: [
            {
              name: "@completed",
              value: false
            }
          ]
        };

        const items = await this.taskDao.find(querySpec);
        res.render("index", {
          title: "My ToDo List ",
          tasks: items
        });
      }

      async addTask(req, res) {
        const item = req.body;

        await this.taskDao.addItem(item);
        res.redirect("/");
      }

      async completeTask(req, res) {
        const completedTasks = Object.keys(req.body);
        const tasks = [];

        completedTasks.forEach(task => {
          tasks.push(this.taskDao.updateItem(task));
        });

        await Promise.all(tasks);

        res.redirect("/");
      }
    }

    module.exports = TaskList;
   ```

3. Spara och stäng filen **tasklist.js**.

### <a name="add-configjs"></a>Lägg till config.js

1. Vid roten av projektkatalogen skapar du en ny fil med namnet **config.js**. 

2. Lägg till följande kod i filen **config.js**. Den här koden definierar konfigurationsinställningar och värden som behövs i appen.
   
   ```javascript
   const config = {};

   config.host = process.env.HOST || "[the endpoint URI of your Azure Cosmos DB account]";
   config.authKey =
     process.env.AUTH_KEY || "[the PRIMARY KEY value of your Azure Cosmos DB account";
   config.databaseId = "ToDoList";
   config.containerId = "Items";

   if (config.host.includes("https://localhost:")) {
     console.log("Local environment detected");
     console.log("WARNING: Disabled checking of self-signed certs. Do not have this code in production.");
     process.env.NODE_TLS_REJECT_UNAUTHORIZED = "0";
     console.log(`Go to http://localhost:${process.env.PORT || '3000'} to try the sample.`);
   }

   module.exports = config;
   ```

3. I filen **config.js** uppdaterar du värdena för HOST och AUTH_KEY med hjälp av värdena på sidan Nycklar i Azure Cosmos DB-kontot på [Azure Portal](https://portal.azure.com). 

4. Spara och stäng filen **config.js**.

### <a name="modify-appjs"></a>Ändra app.js

1. Öppna filen **app.js** i projektkatalogen. Den här filen skapades tidigare när Express-webbappen skapades.  

2. Lägg till följande kod i filen **app.js**. Den här koden definierar den konfigurationsfil som ska användas och läser in värdena till några variabler som du använder i kommande avsnitt. 
   
   ```javascript
    const CosmosClient = require('@azure/cosmos').CosmosClient
    const config = require('./config')
    const TaskList = require('./routes/tasklist')
    const TaskDao = require('./models/taskDao')

    const express = require('express')
    const path = require('path')
    const logger = require('morgan')
    const cookieParser = require('cookie-parser')
    const bodyParser = require('body-parser')

    const app = express()

    // view engine setup
    app.set('views', path.join(__dirname, 'views'))
    app.set('view engine', 'jade')

    // uncomment after placing your favicon in /public
    //app.use(favicon(path.join(__dirname, 'public', 'favicon.ico')));
    app.use(logger('dev'))
    app.use(bodyParser.json())
    app.use(bodyParser.urlencoded({ extended: false }))
    app.use(cookieParser())
    app.use(express.static(path.join(__dirname, 'public')))

    //Todo App:
    const cosmosClient = new CosmosClient({
      endpoint: config.host,
      key: config.authKey
    })
    const taskDao = new TaskDao(cosmosClient, config.databaseId, config.containerId)
    const taskList = new TaskList(taskDao)
    taskDao
      .init(err => {
        console.error(err)
      })
      .catch(err => {
        console.error(err)
        console.error(
          'Shutting down because there was an error settinig up the database.'
        )
        process.exit(1)
      })

    app.get('/', (req, res, next) => taskList.showTasks(req, res).catch(next))
    app.post('/addtask', (req, res, next) => taskList.addTask(req, res).catch(next))
    app.post('/completetask', (req, res, next) =>
      taskList.completeTask(req, res).catch(next)
    )
    app.set('view engine', 'jade')

    // catch 404 and forward to error handler
    app.use(function(req, res, next) {
      const err = new Error('Not Found')
      err.status = 404
      next(err)
    })

    // error handler
    app.use(function(err, req, res, next) {
      // set locals, only providing error in development
      res.locals.message = err.message
      res.locals.error = req.app.get('env') === 'development' ? err : {}

      // render the error page
      res.status(err.status || 500)
      res.render('error')
    })

    module.exports = app
   ```

3. Avsluta med att spara och stänga filen **app.js**.

## <a name="build-a-user-interface"></a><a name="_Toc395783181"></a>Skapa ett användargränssnitt

Nu ska vi bygga användar gränssnittet så att en användare kan interagera med programmet. Det Express-program som vi skapade i föregående avsnitt använder **Jade** som visningsmotor.

1. Filen **layout.jade** i katalogen **views** används som en global mall för andra **.jade**-filer. I det här steget ändrar du den så att den använder Twitter Bootstrap, vilket är en verktygslåda som används för att utforma webbplatser.  

2. Öppna filen **layout.jade** i mappen **views** och ersätt innehållet med följande kod:

   ```html
   doctype html
   html
     head
       title= title
       link(rel='stylesheet', href='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/css/bootstrap.min.css')
       link(rel='stylesheet', href='/stylesheets/style.css')
     body
       nav.navbar.navbar-inverse.navbar-fixed-top
         div.navbar-header
           a.navbar-brand(href='#') My Tasks
       block content
       script(src='//ajax.aspnetcdn.com/ajax/jQuery/jquery-1.11.2.min.js')
       script(src='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/bootstrap.min.js')
   ```

    Koden instruerar **Jade**-motorn att rendera HTML för vårt program. Dessutom skapas ett **block** med namnet **content**, där vi kan ha layouten för våra innehållssidor. Spara och stäng filen **layout.jade**.

3. Öppna nu filen **index.jade**, den vy som ska användas av vårt program, och ersätt innehållet i filen med följande kod:

   ```html
   extends layout
   block content
        h1 #{title}
        br
        
        form(action="/completetask", method="post")
         table.table.table-striped.table-bordered
            tr
              td Name
              td Category
              td Date
              td Complete
            if (typeof tasks === "undefined")
              tr
                td
            else
              each task in tasks
                tr
                  td #{task.name}
                  td #{task.category}
                  - var date  = new Date(task.date);
                  - var day   = date.getDate();
                  - var month = date.getMonth() + 1;
                  - var year  = date.getFullYear();
                  td #{month + "/" + day + "/" + year}
                  td
                   if(task.completed) 
                    input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
                   else
                    input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
          button.btn.btn-primary(type="submit") Update tasks
        hr
        form.well(action="/addtask", method="post")
          label Item Name:
          input(name="name", type="textbox")
          label Item Category:
          input(name="category", type="textbox")
          br
          button.btn(type="submit") Add item
   ```

Den här koden utökar layouten och ger innehåll för platshållaren **content** som vi såg i filen **layout.jade**. I den här layouten skapade vi två HTML-formulär.

Det första formuläret innehåller en tabell för dina data och en knapp som gör att du kan uppdatera objekt genom att publicera till metoden **/completeTask** i kontrollanten.
    
Det andra formuläret innehåller två inmatningsfält och en knapp som gör att du kan skapa ett nytt objekt genom att publicera till metoden **/addtask** i kontrollanten. Det är allt vi behöver för att programmet ska fungera.

## <a name="run-your-application-locally"></a><a name="_Toc395783181"></a>Köra ditt program lokalt

Nu när du har skapat programmet kan du köra det lokalt genom att använda följande steg:  

1. Om du vill testa programmet på din lokala dator kör du `npm start` i terminalen för att starta programmet. Uppdatera sedan `http://localhost:3000`-webbläsarsidan. Sidan bör ser ut så som på följande skärmbild:
   
    :::image type="content" source="./media/sql-api-nodejs-application/cosmos-db-node-js-localhost.png" alt-text="Skärmdump av programmet MyTodo List i ett webbläsarfönster":::

    > [!TIP]
    > Om du får ett felmeddelande om indrag i layout.jade- eller index.jade-filen, kontrollerar du att de första två raderna i båda filerna är vänsterjusterade, utan blanksteg. Om det finns blanksteg före de två första raderna tar du bort dem. Spara filerna och uppdatera sedan webbläsarfönstret. 

2. Använd fälten objekt, objekt namn och kategori för att ange en ny uppgift och välj sedan **Lägg till objekt**. Då skapas ett dokument i Azure Cosmos DB med dessa egenskaper. 

3. Sidan bör uppdateras och visa det nya objektet i ToDo-listan.
   
    :::image type="content" source="./media/sql-api-nodejs-application/cosmos-db-node-js-added-task.png" alt-text="Skärmdump av programmet med ett nytt objekt i ToDo-listan":::

4. Du slutför en aktivitet genom att markera kryssrutan i kolumnen Slutför. Klicka sedan på **Uppdatera uppgifter**. Då uppdateras det dokument som du redan har skapat och tas bort från vyn.

5. För att stoppa programmet trycker du på CTRL+C i terminalfönstret och väljer sedan **Y** för att avbryta batch-jobbet.

## <a name="deploy-your-application-to-web-apps"></a><a name="_Toc395783182"></a>Distribuera programmet till Web Apps

När ditt program fungerar lokalt, kan du distribuera det till Azure med hjälp av följande steg:

1. Om du inte redan gjort det aktiverar du en git-lagringsplats för Web Apps-programmet.

2. Lägg till ditt Web Apps-program som en fjärransluten git.
   
   ```bash
   git remote add azure https://username@your-azure-website.scm.azurewebsites.net:443/your-azure-website.git
   ```

3. Distribuera programmet genom att push-överföra det till fjärranslutningen.
   
   ```bash
   git push azure main
   ```

4. Efter några sekunder publiceras webbappen och startas i en webbläsare.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort resursgruppen, Azure Cosmos DB-kontot och alla relaterade resurser när de inte längre behövs. Om du vill göra detta markerar du den resursgrupp som du använde för Azure Cosmos DB-kontot. Välj sedan **Ta bort** och bekräfta namnet på den resursgrupp som du vill ta bort.

## <a name="next-steps"></a><a name="_Toc395637775"></a>Nästa steg

> [!div class="nextstepaction"]
> [Bygga mobilappar med Xamarin och Azure Cosmos DB](mobile-apps-with-xamarin.md)


[Node.js]: https://nodejs.org/
[Git]: https://git-scm.com/
[GitHub]: https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-todo-app