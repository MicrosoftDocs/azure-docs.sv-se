---
title: 'Självstudie: Linux Ruby-app med postgres'
description: Lär dig hur du skaffar en Linux Ruby-app som fungerar i Azure App Service, med anslutning till en PostgreSQL-databas i Azure. Räler används i självstudien.
ms.devlang: ruby
ms.topic: tutorial
ms.date: 06/18/2020
ms.custom: mvc, cli-validate, seodec18, devx-track-azurecli
ms.openlocfilehash: f565fcef60b2cb4726b180eb67e6ac1fcaefc24b
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97347854"
---
# <a name="build-a-ruby-and-postgres-app-in-azure-app-service-on-linux"></a>Skapa en Ruby- och Postgres-app i Azure App Service på Linux

[Azure App Service](overview.md) ger en mycket skalbar och automatisk korrigering av webb värd tjänst. I den här självstudien visas hur du skapar en Ruby-app och ansluter den till en PostgreSQL-databas. När du är klar har du en [Ruby on Rails](https://rubyonrails.org/)-app som körs i App Service på Linux.

:::image type="content" source="./media/tutorial-ruby-postgres-app/complete-checkbox-published.png" alt-text="Skärm bild av ett exempel på en Ruby on-app med titeln uppgifter.":::

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en PostgreSQL-databas i Azure
> * Ansluta en Ruby on Rails-app till PostgreSQL
> * distribuera appen till Azure
> * uppdatera datamodellen och distribuera om appen
> * strömma diagnostikloggar från Azure
> * hantera appen i Azure-portalen.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

- [Installera Git](https://git-scm.com/)
- [Installera ruby 2,6](https://www.ruby-lang.org/en/documentation/installation/)
- [Installera Ruby on Rails 5.1](https://guides.rubyonrails.org/v5.1/getting_started.html)
- [Installera och kör PostgreSQL](https://www.postgresql.org/download/)
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="prepare-local-postgres"></a>Förbereda lokala Postgres

I det här steget skapar du en databas för självstudien på din lokala Postgres-server.

### <a name="connect-to-local-postgres-server"></a>Anslut till den lokala Postgres-servern

Öppna terminalfönstret och kör `psql` för att ansluta till din lokala Postgres-server.

```bash
sudo -u postgres psql
```

Om anslutningen lyckas körs Postgres-databasen. Om inte, kontrollerar du att den lokala Postgres-databasen har startats genom att följa stegen i [Hämtningar – PostgreSQL Core Distribution](https://www.postgresql.org/download/).

Skriv `\q` om du vill avsluta Postgres-klienten. 

Skapa en Postgres-användare som kan skapa databaser genom att köra följande kommando, med ditt inloggade Linux-användarnamn.

```bash
sudo -u postgres createuser -d <signed-in-user>
```

<a name="step2"></a>

## <a name="create-a-ruby-on-rails-app-locally"></a>Skapa en Ruby on Rails-app lokalt
I det här steget hämtar du en Ruby on Rails-exempelapp, konfigurerar dess databasanslutning och kör den lokalt. 

### <a name="clone-the-sample"></a>Klona exemplet

Använd kommandot `cd` för att komma till en arbetskatalog i terminalfönstret.

Klona exempellagringsplatsen med följande kommando.

```bash
git clone https://github.com/Azure-Samples/rubyrails-tasks.git
```

`cd` till den klonade katalogen. Installera de paket som behövs.

```bash
cd rubyrails-tasks
bundle install --path vendor/bundle
```

### <a name="run-the-sample-locally"></a>Köra exemplet lokalt

Kör [Rails-migreringen](https://guides.rubyonrails.org/active_record_migrations.html#running-migrations) för att skapa de tabeller som behövs för appen. Du kan se vilka tabeller som skapas i migreringarna i katalogen _db/migrations_ på Git-lagringsplatsen.

```bash
rake db:create
rake db:migrate
```

Kör appen.

```bash
rails server
```

Gå till `http://localhost:3000` i en webbläsare. Lägg till några uppgifter på sidan.

![Ruby on Rails ansluter till Postgres](./media/tutorial-ruby-postgres-app/postgres-connect-success.png)

Om du vill stoppa Rails-servern skriver du `Ctrl + C` i terminalen.

## <a name="create-postgres-in-azure"></a>Skapa Postgres i Azure

I det här steget skapar du en Postgres-databas i [Azure Database for PostgreSQL](../postgresql/index.yml). Senare kommer du att konfigurera Ruby on Rails-appen för att ansluta till den här databasen.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

## <a name="create-postgres-database-in-azure"></a>Skapa postgres-databas i Azure

<!-- > [!NOTE]
> Before you create an Azure Database for PostgreSQL server, check which [compute generation](../postgresql/concepts-pricing-tiers.md#compute-generations-and-vcores) is available in your region. If your region doesn't support Gen4 hardware, change *--sku-name* in the following command line to a value that's supported in your region, such as B_Gen4_1.  -->

I det här avsnittet skapar du en Azure Database for PostgreSQL-Server och-databas. Starta genom att installera `db-up` tillägget med följande kommando:

```azurecli
az extension add --name db-up
```

Skapa postgres-databasen i Azure med [`az postgres up`](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) kommandot, som du ser i följande exempel. Ersätt *\<postgresql-name>* med ett *unikt* namn (Server slut punkten är *https:// \<postgresql-name> . postgres.Database.Azure.com*). För *\<admin-username>* och *\<admin-password>* anger du autentiseringsuppgifter för att skapa en administratörs användare för den här postgres-servern.

<!-- Issue: without --location -->
```azurecli
az postgres up --resource-group myResourceGroup --location westeurope --server-name <postgresql-name> --database-name sampledb --admin-user <admin-username> --admin-password <admin-password> --ssl-enforcement Enabled
```

Det här kommandot kan ta en stund eftersom det gör följande:

- Skapar en [resurs grupp](../azure-resource-manager/management/overview.md#terminology) med namnet `myResourceGroup` , om den inte finns. Varje Azure-resurs måste vara i någon av dessa. `--resource-group` är valfritt.
- Skapar en postgres-server med den administrativa användaren.
- Skapar en `sampledb` databas.
- Tillåter åtkomst från den lokala IP-adressen.
- Tillåter åtkomst från Azure-tjänster.
- Skapa en databas användare med åtkomst till `sampledb` databasen.

Du kan utföra alla steg separat med andra `az postgres` kommandon och `psql` , men `az postgres up` alla är i ett steg åt dig.

När kommandot har slutförts söker du efter de utmatnings rader som ska visas `Ran Database Query:` . De visar databas användaren som skapas åt dig, med användar namn `root` och lösen ord `Sampledb1` . Du kommer att använda dem senare för att ansluta din app till-databasen.

<!-- not all locations support az postgres up -->
> [!TIP]
> `--location <location-name>`, kan ställas in på en av [Azure-regionerna](https://azure.microsoft.com/global-infrastructure/regions/). Du kan hämta de regioner som är tillgängliga för din prenumeration med [`az account list-locations`](/cli/azure/account#az-account-list-locations) kommandot. För produktion av appar sätter du din databas och din app på samma plats.

## <a name="connect-app-to-azure-postgres"></a>Anslut appen till Azure Postgres

I det här steget ansluter du Ruby on Rails-appen till Postgres-databasen du skapade i Azure Database för PostgreSQL.

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>Konfigurera databasanslutningen

På lagringsplatsen öppnar du _config/database.yml_. Ersätt produktionsvariablerna med följande kod längst ned i filen. 

```txt
production:
  <<: *default
  host: <%= ENV['DB_HOST'] %>
  database: <%= ENV['DB_DATABASE'] %>
  username: <%= ENV['DB_USERNAME'] %>
  password: <%= ENV['DB_PASSWORD'] %>
```

Spara ändringarna.

### <a name="test-the-application-locally"></a>Testa appen lokalt

Tillbaka i den lokala terminalen anger du följande miljövariabler:

```bash
export DB_HOST=<postgres-server-name>.postgres.database.azure.com
export DB_DATABASE=sampledb 
export DB_USERNAME=root@<postgres-server-name>
export DB_PASSWORD=Sampledb1
```

Kör Rails-databasmigreringar med de produktionsvärden du just konfigurerade för att skapa tabellerna i din Postgres-databas i Azure Database for PostgreSQL.

```bash
rake db:migrate RAILS_ENV=production
```

När Rails-programmet körs i produktionsmiljön behöver det förkompilerade tillgångar. Generera de tillgångar som krävs med följande kommando:

```bash
rake assets:precompile
```

Rails-produktionsmiljön använder också hemligheter för att hantera säkerhet. Generera en hemlig nyckel.

```bash
rails secret
```

Spara den hemliga nyckeln för respektive variabler som används av Rails-produktionsmiljön. För enkelhetens skull använder du samma nyckel för de båda variablerna.

```bash
export RAILS_MASTER_KEY=<output-of-rails-secret>
export SECRET_KEY_BASE=<output-of-rails-secret>
```

Aktivera Rails-produktionsmiljön för att hantera JavaScript- och CSS-filer.

```bash
export RAILS_SERVE_STATIC_FILES=true
```

Kör exempelprogrammet i produktionsmiljön.

```bash
rails server -e production
```

Navigera till `http://localhost:3000`. Om sidan läses in utan fel ansluter Ruby on Rails-appen till Postgres-databasen i Azure.

Lägg till några uppgifter på sidan.

![Ruby on Rails ansluter till Azure Database för PostgreSQL](./media/tutorial-ruby-postgres-app/azure-postgres-connect-success.png)

Om du vill stoppa Rails-servern skriver du `Ctrl + C` i terminalen.

### <a name="commit-your-changes"></a>Genomföra ändringarna

Kör följande Git-kommandon för att genomföra ändringarna:

```bash
git add .
git commit -m "database.yml updates"
```

Din app är klar att distribuera.

## <a name="deploy-to-azure"></a>Distribuera till Azure

I det här steget, distribuerar du ditt Postgres-anslutna Rails-program till Azure App Service.

### <a name="configure-a-deployment-user"></a>Konfigurera en distributionsanvändare

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Skapa en App Service-plan

[!INCLUDE [Create app service plan no h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Skapa en webbapp

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-ruby-linux-no-h.md)] 

### <a name="configure-database-settings"></a>Konfigurera databasinställningarna

I App Service ställer du in miljövariabler som _appinställningar_ med kommandot [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest&preserve-view=true#az-webapp-config-appsettings-set) i Cloud Shell.

Följande Cloud Shell-kommando konfigurerar appinställningarna `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` och `DB_PASSWORD`. Ersätt plats hållarna _&lt; APPNAME>_ och _&lt; postgres-Server-Name>_.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DB_HOST="<postgres-server-name>.postgres.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="root@<postgres-server-name>" DB_PASSWORD="Sampledb1"
```

### <a name="configure-rails-environment-variables"></a>Konfigurera Rails-miljövariabler

I den lokala terminalen [genererar du en ny hemlighet](configure-language-ruby.md#set-secret_key_base-manually) för produktions miljön för räl i Azure.

```bash
rails secret
```

Konfigurera variablerna som krävs för Rails-produktionsmiljön.

I följande Cloud Shell-kommando ersätter du plats hållarna två _&lt; utdata-of-räler-Secret>_ med den nya hemliga nyckeln som du genererade i den lokala terminalen.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings RAILS_MASTER_KEY="<output-of-rails-secret>" SECRET_KEY_BASE="<output-of-rails-secret>" RAILS_SERVE_STATIC_FILES="true" ASSETS_PRECOMPILE="true"
```

`ASSETS_PRECOMPILE="true"` uppmanar Ruby-standardcontainern att förkompilera tillgångar vid varje Git-distribution. Mer information finns i [förkompilera till gångar](configure-language-ruby.md#precompile-assets) och [betjäna statiska till gångar](configure-language-ruby.md#serve-static-assets).

### <a name="push-to-azure-from-git"></a>Skicka till Azure från Git

I den lokala terminalen kan du lägga till en Azure-fjärrdatabas till din lokala Git-databas.

```bash
git remote add azure <paste-copied-url-here>
```

Skicka till Azure-fjärrdatabasen för att distribuera Ruby on Rails-appen. Du uppmanas att ange lösenordet du angav tidigare. Det behövs för att skapa distributionsanvändaren.

```bash
git push azure master
```

Under distributionen meddelar Azure App Service förloppet till Git.

<pre>
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 291 bytes | 0 bytes/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'a5e076db9c'.
remote: Running custom deployment command...
remote: Running deployment command...
...
&lt; Output has been truncated for readability &gt;
</pre>

### <a name="browse-to-the-azure-app"></a>Bläddra till Azure-appen

Bläddra till `http://<app-name>.azurewebsites.net` och lägg till några uppgifter i listan.

:::image type="content" source="./media/tutorial-ruby-postgres-app/ruby-postgres-in-azure.png" alt-text="Skärm bild av Azure App-exemplet med rubriken uppgifter som visar aktiviteter som lagts till i listan.":::

Grattis! Du kör en datadriven Ruby on Rails-app i Azure App Service.

## <a name="update-model-locally-and-redeploy"></a>Uppdatera modell lokalt och distribuera om

I det här steget gör du en enkel ändring i `task`-datamodellen och webbappen och publicerar sedan uppdateringen till Azure.

För uppgiftsscenariot ändrar du programmet så att du kan markera en uppgift som slutförd.

### <a name="add-a-column"></a>Lägga till en kolumn

I terminalen går du till roten för Git-lagringsplatsen.

Generera en ny migrering som lägger till en boolesk kolumn med namnet `Done` i tabellen `Tasks`:

```bash
rails generate migration AddDoneToTasks Done:boolean
```

Det här kommandot genererar en ny migrationsfil i katalogen _db/migrate_.


I terminalen kör du Rails-databasemigreringar för att göra ändringen i den lokala databasen.

```bash
rake db:migrate
```

### <a name="update-application-logic"></a>Uppdatera programlogik

Öppna filen *app/controllers/tasks_controller.rb*. Lägg till följande rad i slutet av filen:

```rb
params.require(:task).permit(:Description)
```

Ändra raden så att den inkluderar den nya parametern `Done`.

```rb
params.require(:task).permit(:Description, :Done)
```

### <a name="update-the-views"></a>Uppdatera vyerna

Öppna filen *app/views/tasks/_form.html.erb*, som är i redigeringsform.

Leta reda på raden `<%=f.error_span(:Description) %>` och infoga följande kod direkt under den:

```erb
<%= f.label :Done, :class => 'control-label col-lg-2' %>
<div class="col-lg-10">
  <%= f.check_box :Done, :class => 'form-control' %>
</div>
```

Öppna filen *app/views/tasks/show.html.erb*, som är den enskilda postens visningssida. 

Leta reda på raden `<dd><%= @task.Description %></dd>` och infoga följande kod direkt under den:

```erb
  <dt><strong><%= model_class.human_attribute_name(:Done) %>:</strong></dt>
  <dd><%= check_box "task", "Done", {:checked => @task.Done, :disabled => true}%></dd>
```

Öppna filen *app/views/tasks/index.html.erb*, som är indexsidan för alla poster.

Leta reda på raden `<th><%= model_class.human_attribute_name(:Description) %></th>` och infoga följande kod direkt under den:

```erb
<th><%= model_class.human_attribute_name(:Done) %></th>
```

I samma fil letar du reda på raden `<td><%= task.Description %></td>` och infogar följande kod direkt under den:

```erb
<td><%= check_box "task", "Done", {:checked => task.Done, :disabled => true} %></td>
```

### <a name="test-the-changes-locally"></a>Testa ändringarna lokalt

Kör Rails-servern i den lokala terminalen.

```bash
rails server
```

Om du vill se ändringen i uppgiftsstatusen går du till `http://localhost:3000` och lägger till eller redigerar objekt.

![Kryssruta lades till för uppgift](./media/tutorial-ruby-postgres-app/complete-checkbox.png)

Om du vill stoppa Rails-servern skriver du `Ctrl + C` i terminalen.

### <a name="publish-changes-to-azure"></a>Publicera ändringar till Azure

I terminalen kör du Rails-databasmigreringar för produktionsmiljön för att genomföra ändringen i Azure-databasen.

```bash
rake db:migrate RAILS_ENV=production
```

Genomför alla ändringar på Git och skicka sedan kodändringarna till Azure.

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

När `git push` har slutförts så kan du gå till Azure-appen och prova de nya funktionerna.

![Modell- och databasändringar som är publicerade i Azure](media/tutorial-ruby-postgres-app/complete-checkbox-published.png)

Om du har lagt till några uppgifter finns de kvar i databasen. Uppdateringar i dataschemat påverkar inte befintliga data.

## <a name="stream-diagnostic-logs"></a>Strömma diagnostikloggar

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="manage-the-azure-app"></a>Hantera Azure-appen

Gå till [Azure-portalen](https://portal.azure.com) för att hantera den app som du skapade.

I den vänstra menyn, klickar du på **App Services** och därefter på namnet på din Azure-app.

![Portalnavigering till Azure-app](./media/tutorial-php-mysql-app/access-portal.png)

Nu visas översiktssidan för din app. Här kan du utföra grundläggande hanteringsåtgärder som att stoppa, starta, starta om, bläddra och ta bort.

Menyn till vänster innehåller sidor för att konfigurera appen.

![App Service-sidan på Azure Portal](./media/tutorial-php-mysql-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en Postgres-databas i Azure
> * Ansluta en Ruby on Rails-app till Postgres
> * distribuera appen till Azure
> * uppdatera datamodellen och distribuera om appen
> * strömma diagnostikloggar från Azure
> * hantera appen i Azure-portalen.

Gå vidare till nästa självstudie där du får lära dig att mappa ett anpassat DNS-namn till appen.

> [!div class="nextstepaction"]
> [Självstudie: mappa ett anpassat DNS-namn till din app](app-service-web-tutorial-custom-domain.md)

Eller kolla ut andra resurser:

> [!div class="nextstepaction"]
> [Konfigurera Ruby-app](configure-language-ruby.md)
