---
title: 'Självstudie: Node.js app med MongoDB'
description: Lär dig hur du får en Node.js-app att fungera i Azure, med anslutning till en MongoDB-databas i Azure (Cosmos DB). MEAN.js används i självstudien.
ms.assetid: 0b4d7d0e-e984-49a1-a57a-3c0caa955f0e
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 06/16/2020
ms.custom: mvc, cli-validate, seodec18, devx-track-js, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: b1dcd413f301f25460cb29f1bb20e67a37ac6ebb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767332"
---
# <a name="tutorial-build-a-nodejs-and-mongodb-app-in-azure"></a>Självstudie: Skapa en Node.js- och MongoDB-app i Azure

::: zone pivot="platform-windows"  

[Azure App Service](overview.md) en mycket skalbar och självkorrigeringstjänst för webbvärdtjänster. Den här självstudien visar hur du skapar Node.js en app App Service i Windows och ansluter den till en MongoDB-databas. När du är klar har du ett MEAN-program (MongoDB, Express, AngularJS och Node.js) som körs i [Azure App Service](overview.md). För enkelhetens skull använder exempelprogrammet [MEAN.js-webbramverket](https://meanjs.org/).

::: zone-end

::: zone pivot="platform-linux"


[Azure App Service](overview.md) en mycket skalbar och självkorrigeringstjänst för webbvärdtjänster med Linux-operativsystemet. Den här självstudien visar hur du skapar en Node.js-app i App Service på Linux, ansluter den lokalt till en MongoDB-databas och sedan distribuerar den till en databas i Azure Cosmos DB:s API för MongoDB. När du är klar har du ett MEAN-program (MongoDB, Express, AngularJS och Node.js) som körs i App Service på Linux. För enkelhetens skull använder exempelprogrammet [MEAN.js-webbramverket](https://meanjs.org/).

::: zone-end

![MEAN.js-app som körs i Azure App Service](./media/tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

Detta får du får lära dig:

> [!div class="checklist"]
> * skapa en MongoDB-databas i Azure
> * ansluta en Node.js-app till MongoDB
> * distribuera appen till Azure
> * uppdatera datamodellen och distribuera om appen
> * strömma diagnostikloggar från Azure
> * hantera appen i Azure-portalen.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

- [Installera Git](https://git-scm.com/)
- [Installera Node.js och NPM](https://nodejs.org/)
- [Installera Bower](https://bower.io/) (krävs för [MEAN.js](https://meanjs.org/docs/0.5.x/#getting-started))
- [Installera Gulp.js](https://gulpjs.com/) (krävs för [MEAN.js](https://meanjs.org/docs/0.5.x/#getting-started))
- [Installera och köra MongoDB Community Edition](https://docs.mongodb.com/manual/administration/install-community/)
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)] 

## <a name="test-local-mongodb"></a>Testa lokal MongoDB

Öppna terminalfönstret och `cd` till `bin`-katalogen i MongoDB-installationen. Du kan använda det här terminalfönstret för att köra alla kommandon i den här självstudien.

Kör `mongo` i terminalen för att ansluta till din lokala MongoDB-server.

```bash
mongo
```

Om anslutningen lyckas körs redan MongoDB-databasen. Om inte ska du kontrollera att din lokala MongoDB-databas startar genom att följa stegen i [Installera MongoDB Community Edition](https://docs.mongodb.com/manual/administration/install-community/). Ofta installeras MongoDB, men du måste ändå starta det genom att köra `mongod`. 

När du har testat MongoDB-databasen skriver du `Ctrl+C` i terminalen. 

## <a name="create-local-nodejs-app"></a>Skapa lokal Node.js-app

I det här steget konfigurerar du det lokala Node.js-projektet.

### <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Använd kommandot `cd` för att komma till en arbetskatalog i terminalfönstret.  

Klona exempellagringsplatsen med följande kommando. 

```bash
git clone https://github.com/Azure-Samples/meanjs.git
```

Den här exempellagringsplatsen innehåller en kopia av [MEAN.js-lagringsplatsen](https://github.com/meanjs/mean). Den ändras för att köras på App Service (mer information finns [filen Viktigt](https://github.com/Azure-Samples/meanjs/blob/master/README.md) för MEAN.js-lagringsplatsen).

### <a name="run-the-application"></a>Kör programmet

Kör följande kommandon för att installera de nödvändiga paketen och starta programmet.

```bash
cd meanjs
npm install
npm start
```

Ignorera config.domain-varningen. När appen har lästs in helt ser du något som liknar följande meddelande:

<pre>
--
MEAN.JS – Utvecklingsmiljö

Miljö: utvecklingsserver:          http://0.0.0.0:3000 Databas: mongodb://localhost/mean-dev appversion: 0.5.0 MEAN.JS version: 0.5.0 --
</pre>

Gå till `http://localhost:3000` i en webbläsare. Klicka på alternativet för att **registrera** på den översta menyn och skapa en testanvändare. 

MEAN.js-exempelprogrammet lagrar användardata i databasen. Om du lyckas skapa en användare och logga in skriver appen data till den lokala MongoDB-databasen.

![MEAN.js ansluter till MongoDB](./media/tutorial-nodejs-mongodb-app/mongodb-connect-success.png)

Välj **Administratör > Hantera artiklar** för att lägga till några artiklar.

Du kan när som helst stoppa Node.js genom att trycka på `Ctrl+C` i terminalen. 

## <a name="create-production-mongodb"></a>Skapa produktions-MongoDB

I det här steget skapar du en MongoDB-databas i Azure. När appen har distribuerats till Azure används den här molndatabasen.

För MongoDB använder den här självstudien [Azure Cosmos DB](/azure/documentdb/). Cosmos DB stöder MongoDB-klientanslutningar.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-cosmos-db-account"></a>Skapa ett Cosmos DB-konto

> [!NOTE]
> Det finns en kostnad för att skapa Azure Cosmos DB-databaser i den här självstudien i din egen Azure-prenumeration. Om du vill använda ett kostnadsfritt Azure Cosmos DB-konto i sju dagar kan du använda funktionen [Testa Azure Cosmos DB kostnadsfritt](https://azure.microsoft.com/try/cosmosdb/). Klicka bara på knappen **Skapa** i MongoDB-panelen för att skapa en kostnadsfri MongoDB-databas på Azure. När databasen har skapats går du till **Anslutningssträng** i portalen och hämtar din Azure Cosmos DB-anslutningssträng för användning senare i självstudien.
>

I Cloud Shell du ett Cosmos DB konto med [`az cosmosdb create`](/cli/azure/cosmosdb#az_cosmosdb_create) kommandot .

I följande kommando ersätter du platshållaren Cosmos DB unika *\<cosmosdb-name>* namn. Det här namnet används som en del av Cosmos DB-slutpunkten `https://<cosmosdb-name>.documents.azure.com/`, så namnet måste vara unikt för alla Cosmos DB-konton i Azure. Namnet får endast innehålla gemener, siffror och bindestreck och måste vara mellan 3 och 50 tecken långt.

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

Parametern *--kind MongoDB* aktiverar MongoDB-klientanslutningar.

När Cosmos DB-kontot har skapats, visar Azure CLI information liknande följande exempel:

<pre>
{
  "consistencyPolicy":
  {
    "defaultConsistencyLevel": "Session",
    "maxIntervalInSeconds": 5,
    "maxStalenessPrefix": 100
  },
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://&lt;cosmosdb-name&gt;.documents.azure.com:443/",
  "failoverPolicies": 
  ...
  &lt; Output truncated for readability &gt;
}
</pre>

## <a name="connect-app-to-production-mongodb"></a>Ansluta en app till produktions-MongoDB

I det här steget, ansluter du ditt MEAN.js-exempelprogram till en Cosmos DB-databas som du just skapade med en MongoDB-anslutningssträng. 

### <a name="retrieve-the-database-key"></a>Hämta databasnyckeln

För att ansluta till en Cosmos DB-databas behöver du databasnyckeln. I Cloud Shell du kommandot [`az cosmosdb list-keys`](/cli/azure/cosmosdb#az_cosmosdb_list_keys) för att hämta den primära nyckeln.

```azurecli-interactive
az cosmosdb list-keys --name <cosmosdb-name> --resource-group myResourceGroup
```

Azure CLI visar information som liknar följande exempel:

<pre>
{
  "primaryMasterKey": "RS4CmUwzGRASJPMoc0kiEvdnKmxyRILC9BWisAYh3Hq4zBYKr0XQiSE4pqx3UchBeO4QRCzUt1i7w0rOkitoJw==",
  "primaryReadonlyMasterKey": "HvitsjIYz8TwRmIuPEUAALRwqgKOzJUjW22wPL2U8zoMVhGvregBkBk9LdMTxqBgDETSq7obbwZtdeFY7hElTg==",
  "secondaryMasterKey": "Lu9aeZTiXU4PjuuyGBbvS1N9IRG3oegIrIh95U6VOstf9bJiiIpw3IfwSUgQWSEYM3VeEyrhHJ4rn3Ci0vuFqA==",
  "secondaryReadonlyMasterKey": "LpsCicpVZqHRy7qbMgrzbRKjbYCwCKPQRl0QpgReAOxMcggTvxJFA94fTi0oQ7xtxpftTJcXkjTirQ0pT7QFrQ=="
}
</pre>

Kopiera värdet för `primaryMasterKey`. Du behöver den här informationen i nästa steg.

<a name="devconfig"></a>
### <a name="configure-the-connection-string-in-your-nodejs-application"></a>Konfigurera anslutningssträngen i ditt Node.js-program

I din lokala MEAN.js-lagringsplats skapar du en fil som heter _local-production.js_ i mappen _config/env/_. _.gitignore har_ redan konfigurerats för att hålla den här filen utanför lagringsplatsen. 

Kopiera följande kod till den. Ersätt de två platshållarna med namnet Cosmos DB databasen och ersätt platshållaren med den nyckel som du kopierade *\<cosmosdb-name>* *\<primary-master-key>* i föregående steg.

```javascript
module.exports = {
  db: {
    uri: 'mongodb://<cosmosdb-name>:<primary-master-key>@<cosmosdb-name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false'
  }
};
```

Alternativet `ssl=true` krävs eftersom [Cosmos DB kräver SSL](../cosmos-db/connect-mongodb-account.md#connection-string-requirements). 

Spara ändringarna.

### <a name="test-the-application-in-production-mode"></a>Testa programmet i produktionsläge 

I ett lokalt terminalfönster kör du följande kommando för att minimera och paketera skript för produktionsmiljön. Den här processen genererar de filer som behövs i produktionsmiljön.

```bash
gulp prod
```

I ett lokalt terminalfönster kör du följande kommando för att använda anslutningssträngen du konfigurerade i _config/env/local-production.js_. Ignorera certifikatfelet och config.domain-varningen.

```bash
# Bash
NODE_ENV=production node server.js

# Windows PowerShell
$env:NODE_ENV = "production" 
node server.js
```

`NODE_ENV=production` ställer in miljövariabeln som anvisar Node.js att köra i produktionsmiljön.  `node server.js` startar Node.js-servern med `server.js` i roten för lagringsplatsen. Så här läses Node.js-programmet in i Azure. 

När appen är inläst ska du kontrollera att den körs i produktionsmiljön:

<pre>
--
MEAN.JS

Miljö: http://0.0.0.0:8443 &lt; produktionsserver: Databas: mongodb:// cosmosdb-name: &gt; &lt; primary-master-key &gt; @ &lt; cosmosdb-name &gt; .documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false App version: 0.5.0 MEAN.JS version: 0.5.0
</pre>

Gå till `http://localhost:8443` i en webbläsare. Klicka på alternativet för att **registrera** på den översta menyn och skapa en testanvändare. Om du lyckas skapa en användare och logga in skriver appen data till Cosmos DB-databasen i Azure. 

I terminalen stoppar du Node.js genom att skriva `Ctrl+C`. 

## <a name="deploy-app-to-azure"></a>Distribuera app till Azure

I det här steget distribuerar du ditt MongoDB-anslutna Node.js-program till Azure App Service.

### <a name="configure-a-deployment-user"></a>Konfigurera en distributionsanvändare

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Skapa en App Service-plan

::: zone pivot="platform-windows"  

[!INCLUDE [Create app service plan no h](../../includes/app-service-web-create-app-service-plan-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

::: zone-end

<a name="create"></a>
### <a name="create-a-web-app"></a>Skapa en webbapp

::: zone pivot="platform-windows"  

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-nodejs-no-h.md)] 

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-nodejs-linux-no-h.md)] 

::: zone-end

### <a name="configure-an-environment-variable"></a>Konfigurera en miljövariabel

Som standard håller MEAN.js-projektet _config/env/local-production.js_ utanför Git-lagringsplatsen. Så för Azure-appen använder du appinställningar för att definiera MongoDB-anslutningssträngen.

Om du vill ange appinställningar använder [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) du kommandot i Cloud Shell. 

I följande exempel konfigureras appinställningen `MONGODB_URI` i Azure-appen. Ersätt *\<app-name>* *\<cosmosdb-name>* platshållarna , *\<primary-master-key>* och .

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings MONGODB_URI="mongodb://<cosmosdb-name>:<primary-master-key>@<cosmosdb-name>.documents.azure.com:10250/mean?ssl=true"
```

I Node.js kod kommer du åt [den här appinställningen](configure-language-nodejs.md#access-environment-variables) med `process.env.MONGODB_URI` , precis som du skulle komma åt alla miljövariabler. 

På din lokala MEAN.js-lagringsplats öppnar du _config/env/production.js_ (inte _config/env/local-production.js_), som har en specifik konfiguration för produktion-miljö. MEAN.js-standardappen är redan konfigurerade för att använda `MONGODB_URI`-miljövariabeln du har skapat.

```javascript
db: {
  uri: ... || process.env.MONGODB_URI || ...,
  ...
},
```

### <a name="push-to-azure-from-git"></a>Skicka till Azure från Git

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

<pre>
Counting objects: 5, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (5/5), done.
Writing objects: 100% (5/5), 489 bytes | 0 bytes/s, done.
Total 5 (delta 3), reused 0 (delta 0)
remote: Updating branch 'main'.
remote: Updating submodules.
remote: Preparing deployment for commit id '6c7c716eee'.
remote: Running custom deployment command...
remote: Running deployment command...
remote: Handling node.js deployment.
.
.
.
remote: Deployment successful.
To https://&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git
 * [new branch]      main -> main
</pre>

Du kanske märker att distributionsprocessen kör [Gulp](https://gulpjs.com/) efter `npm install`. App Service kör inte Gulp- eller Grunt-uppgifter under distributionen. Den här exempellagringsplatsen har två extra filer i rotkatalogen för detta: 

- _.deployment_ – Den här filen skickar ett meddelande till App Service om att köra `bash deploy.sh` som anpassat distributionsskript.
- _deploy.sh_ – Det anpassade distributionsskriptet. Om du granskar filen ser du att den kör `gulp prod` efter `npm install` och `bower install`. 

Du kan använda den här metoden för att lägga till steg i din Git-baserade distributionen. När som helst när du startar om Azure-appen kör inte App Service om dessa automatiserade uppgifter. Mer information finns i [Kör Grunt/Bower/Gulp.](configure-language-nodejs.md#run-gruntbowergulp)

### <a name="browse-to-the-azure-app"></a>Bläddra till Azure-appen 

Bläddra till den distribuerade appen i webbläsaren. 

```bash 
http://<app-name>.azurewebsites.net 
``` 

Klicka på **Sign Up** (Registrera) på den översta menyn och skapa en låtsasanvändare. 

Om du lyckas och appen loggar in automatiskt till den skapade användaren har din MEAN.jsapp i Azure anslutning till MongoDB- databasen (Cosmos DB). 

![MEAN.js-app som körs i Azure App Service](./media/tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

Välj **Administratör > Hantera artiklar** för att lägga till några artiklar. 

**Grattis!** Du kör en datadriven Node.js-app i Azure App Service.

## <a name="update-data-model-and-redeploy"></a>Uppdatera datamodell och distribuera om

I det här steget ändrar du datamodellen `article` och publicerar din ändring i Azure.

### <a name="update-the-data-model"></a>Uppdatera datamodellen

I din lokala MEAN.js-katalog öppnar du _modules/articles/server/models/article.server.model.js_.

I `ArticleSchema` lägger du till `String`-typ med namnet `comment`. När du är klar bör schemakoden se ut så här:

```javascript
const ArticleSchema = new Schema({
  ...,
  user: {
    type: Schema.ObjectId,
    ref: 'User'
  },
  comment: {
    type: String,
    default: '',
    trim: true
  }
});
```

### <a name="update-the-articles-code"></a>Uppdatera artiklarnas kod

Uppdatera resten av din `articles`-kod för att använda `comment`.

Det finns fem filer som du måste ändra: serverkontrollanten och de fyra klientvyerna. 

Öppna _modules/articles/server/controllers/articles.server.controller.js_.

I funktionen `update` lägger du till en uppgift för `article.comment`. Följande kod visar den slutförda `update`-funktionen:

```javascript
exports.update = function (req, res) {
  let article = req.article;

  article.title = req.body.title;
  article.content = req.body.content;
  article.comment = req.body.comment;

  ...
};
```

Öppna _modules/articles/client/views/view-article.client.view.html_.

Strax ovanför den avslutande `</section>`-taggen lägger du till följande rad för att visa `comment` tillsammans med resterande artikeldata:

```html
<p class="lead" ng-bind="vm.article.comment"></p>
```

Öppna _modules/articles/client/views/list-articles.client.view.html_.

Strax ovanför den avslutande `</a>`-taggen lägger du till följande rad för att visa `comment` tillsammans med resterande artikeldata:

```html
<p class="list-group-item-text" ng-bind="article.comment"></p>
```

Öppna _modules/articles/client/views/admin/list-articles.client.view.html_.

I elementet `<div class="list-group">` och strax ovanför den avslutande `</a>`-taggen lägger du till följande rad för att visa `comment` tillsammans med resterande artikeldata:

```html
<p class="list-group-item-text" data-ng-bind="article.comment"></p>
```

Öppna _modules/articles/client/views/admin/form-article.client.view.html_.

Leta reda på elementet `<div class="form-group">` som innehåller skickaknappen som ser ut så här:

```html
<div class="form-group">
  <button type="submit" class="btn btn-default">{{vm.article._id ? 'Update' : 'Create'}}</button>
</div>
```

Precis ovanför taggen lägger du till ytterligare ett `<div class="form-group">`-element som gör att människor kan redigera fältet `comment`. Det nya elementet bör se ut ungefär så här:

```html
<div class="form-group">
  <label class="control-label" for="comment">Comment</label>
  <textarea name="comment" data-ng-model="vm.article.comment" id="comment" class="form-control" cols="30" rows="10" placeholder="Comment"></textarea>
</div>
```

### <a name="test-your-changes-locally"></a>Testa ändringarna lokalt

Spara alla ändringar.

Gå till det lokala terminalfönstret och testa dina ändringar i produktionsläget igen.

```bash
# Bash
gulp prod
NODE_ENV=production node server.js

# Windows PowerShell
gulp prod
$env:NODE_ENV = "production" 
node server.js
```

Gå till `http://localhost:8443` i en webbläsare och kontrollera att du är inloggad.

Välj **Administratör > Hantera artiklar** och lägg sedan till en artikel genom att välja knappen **+**.

Nu ser du den nya textrutan `Comment`.

![Tillagda kommentarsfält till artiklar](./media/tutorial-nodejs-mongodb-app/added-comment-field.png)

I terminalen stoppar du Node.js genom att skriva `Ctrl+C`. 

### <a name="publish-changes-to-azure"></a>Publicera ändringar till Azure

I det lokala terminalfönstret sparar du ändringarna i Git och push-överför sedan kodändringarna till Azure.

```bash
git commit -am "added article comment"
git push azure main
```

När `git push` har slutförts kan du gå till Azure-appen och prova att använda de nya funktionerna.

![Modell- och databasändringar som är publicerade i Azure](media/tutorial-nodejs-mongodb-app/added-comment-field-published.png)

Om du lade till några artiklar tidigare kan du fortfarande se dem. Befintliga data i din Cosmos DB förloras inte. Dina uppdateringar till dataschemat håller dessutom dina befintliga data intakta.

## <a name="stream-diagnostic-logs"></a>Strömma diagnostikloggar 

::: zone pivot="platform-windows"  

När Node.js-appen körs i Azure App Service kan du skicka konsolloggarna till din terminal. På så sätt kan du få samma diagnostikmeddelanden för att felsöka programfel.

Starta loggströmningen med kommandot [`az webapp log tail`](/cli/azure/webapp/log#az_webapp_log_tail) i Cloud Shell.

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group myResourceGroup
``` 

Uppdatera Azure-appen i webbläsaren så hämtas webbtrafik när loggströmningen har startats. Nu ser du konsolloggarna som skickas till terminalen.

Stoppa loggströmningen när som helst genom att skriva `Ctrl+C`. 

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end

## <a name="manage-your-azure-app"></a>Hantera din Azure-app

Gå till [Azure-portalen](https://portal.azure.com) om du vill se den app du skapade.

Klicka på **App Services** på menyn till vänster och klicka sedan på din Azure-apps namn.

![Portalnavigering till Azure-app](./media/tutorial-nodejs-mongodb-app/access-portal.png)

Portalen visar som standard dina webbappar på sidan **Översikt**. På den här sidan får du en översikt över hur det går för appen. Här kan du också utföra grundläggande hanteringsåtgärder som att bläddra, stoppa, starta, starta om och ta bort. På flikarna till vänster på sidan kan du se olika konfigurationssidor som du kan öppna.

![App Service-sidan på Azure Portal](./media/tutorial-nodejs-mongodb-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>Nästa steg

Vad du lärt dig:

> [!div class="checklist"]
> * skapa en MongoDB-databas i Azure
> * ansluta en Node.js-app till MongoDB
> * distribuera appen till Azure
> * uppdatera datamodellen och distribuera om appen
> * strömma loggar från Azure till terminalen
> * hantera appen i Azure-portalen.

Gå vidare till nästa självstudie där du får lära dig att mappa ett anpassat DNS-namn till appen.

> [!div class="nextstepaction"] 
> [Mappa ett befintligt anpassat DNS-namn till Azure App Service](app-service-web-tutorial-custom-domain.md)

Eller så kan du kolla in andra resurser:

> [!div class="nextstepaction"]
> [Konfigurera Node.js appen](configure-language-nodejs.md)
