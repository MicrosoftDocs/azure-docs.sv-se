---
title: 'Självstudie: Bygg en PHP-app (Laravel) med Azure Database for MySQL flexibel Server'
description: I den här självstudien beskrivs hur du skapar en PHP-app med flexibel Server.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: tutorial
ms.devlang: php
ms.date: 9/21/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 93e605cb20d593750100ec8e340a7ad74c4dd385
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97587901"
---
# <a name="tutorial-build-a-php-laravel-and-mysql-flexible-server-preview-app-in-azure-app-service"></a>Självstudie: bygga en PHP-app (Laravel) och MySQL-flexibel Server (för hands version) i Azure App Service


:::image type="content" source="media/tutorial-php-database-app/complete-checkbox-published.png" alt-text="PHP-webbapp i Azure med flexibel Server":::

[Azure App Service](../../app-service/overview.md) ger en mycket skalbar och automatisk korrigering av webb värd tjänst som använder Linux-operativsystemet. I den här självstudien visas hur du skapar en PHP-app i Azure och ansluter den till en MySQL-databas. När du är klar har du en [Laravel](https://laravel.com/) -app som körs på Azure App Service på Linux.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Konfigurera en PHP-app (Laravel) med lokal MySQL
> * Skapa en MySQL-flexibel Server (för hands version)
> * Ansluta en PHP-app till MySQL-flexibel Server (för hands version)
> * Distribuera appen till Azure App Service
> * uppdatera datamodellen och distribuera om appen
> * hantera appen i Azure-portalen.

Om du inte har en [Azure-prenumeration](../../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

1. [Installera Git](https://git-scm.com/)
2. [Installera PHP 5.6.4 eller senare](https://php.net/downloads.php)
3. [Installera Composer](https://getcomposer.org/doc/00-intro.md)
4. Aktivera följande PHP-tillägg som behövs för Laravel: OpenSSL, PDO-MySQL, Mbstring, Tokenizer och XML
5. [Installera och starta MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html)

## <a name="prepare-local-mysql"></a>Förbereda lokal MySQL

I det här steget skapar du en databas för självstudien på din lokala MySQL-server.

### <a name="connect-to-local-mysql-server"></a>Ansluta till en lokal MySQL-server

Anslut till din lokala MySQL-server via ett terminalfönster. Du kan använda det här terminalfönstret för att köra alla kommandon i den här självstudien.

```bash
mysql -u root -p
```

Om du uppmanas att ange ett lösenord anger du lösenordet för `root`-kontot. Se [MySQL: Återställa rotlösenordet](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html) om du inte kommer ihåg rotlösenordet för ditt konto.

MySQL-servern är igång om kommandot körs utan problem. Om inte, kontrollerar du att den lokala MySQL-servern är igång genom att följa [anvisningarna efter installation av MySQL](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html).

### <a name="create-a-database-locally"></a>Skapa en lokal databas

Skapa en databas i `mysql`-prompten.

```sql
CREATE DATABASE sampledb;
```

Skriv `quit` för att avsluta serveranslutningen.

```sql
quit
```

<a name="step2"></a>

## <a name="create-a-php-app-locally"></a>Skapa en PHP-app lokalt
I det här steget hämtar du en Laravel-exempelapp, konfigurerar dess databasanslutning och kör den lokalt.

### <a name="clone-the-sample"></a>Klona exemplet

I terminalfönstret navigerar du till en tom katalog där du kan klona exempel programmet.  Klona exempellagringsplatsen med följande kommando.

```bash
git clone https://github.com/Azure-Samples/laravel-tasks
```

`cd` till den klonade katalogen.
Installera de paket som behövs.

```bash
cd laravel-tasks
composer install
```

### <a name="configure-mysql-connection"></a>Konfigurera MySQL-anslutningen

Skapa en fil med namnet *.env* i lagringsplatsens rot. Kopiera in följande variabler i *.env*-filen. Ersätt plats hållaren för _&lt; root_password>_ med MySQL-rot användarens lösen ord.

```txt
APP_ENV=local
APP_DEBUG=true
APP_KEY=

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_DATABASE=sampledb
DB_USERNAME=root
DB_PASSWORD=<root_password>
```

Mer information om hur Laravel använder _.env_-filen finns i [Laravel Environment Configuration](https://laravel.com/docs/5.4/configuration#environment-configuration) (Konfiguration av Laravel-miljö).

### <a name="run-the-sample-locally"></a>Köra exemplet lokalt

Kör [Laravel-databasmigreringar](https://laravel.com/docs/5.4/migrations) för att skapa de tabeller som behövs för appen. Du kan se vilka tabeller som skapas i migreringarna i katalogen _database/migrations_ (databas/migreringar) på Git-lagringsplatsen.

```bash
php artisan migrate
```

Generera en ny Laravel-programnyckel.

```bash
php artisan key:generate
```

Kör appen.

```bash
php artisan serve
```

Gå till `http://localhost:8000` i en webbläsare. Lägg till några uppgifter på sidan.

:::image type="content" source="media/tutorial-php-database-app/mysql-connect-success.png" alt-text="PHP ansluter till MySQL":::

Om du vill stoppa PHP skriver du `Ctrl + C` i terminalen.

## <a name="create-a-mysql-flexible-server-preview"></a>Skapa en MySQL-flexibel Server (för hands version)
I det här steget skapar du en MySQL-databas i [Azure Database for MySQL flexibel Server](../index.yml) som är i offentlig för hands version. Senare kommer du att konfigurera PHP-appen för att ansluta till den här databasen. I [Azure Cloud Shell](../../cloud-shell/overview.md)skapar du en server i med [`az flexible-server create`](/cli/azure/mysql/server#az-mysql-flexible-server-create) kommandot.

```azurecli-interactive
az mysql flexible-server create  --resource-group myResourceGroup --public-access <IP-Address>
```

> [!IMPORTANT]
> - Anteckna **Server namn** och **anslutnings sträng** för att använda det i nästa steg för att ansluta och köra Laravel datamigrering.
> - För **IP-adress**  argument anger du IP-adressen för klient datorn. Servern är låst när den skapas och du måste tillåta åtkomst till klient datorn för att hantera servern lokalt.

### <a name="configure-server-firewall-to-allow-web-app-to-connect-to-the-server"></a>Konfigurera Server brand väggen så att webb programmet kan ansluta till servern

I Cloud Shell skapar du en brand Väggs regel för MySQL-servern för att tillåta klient anslutningar med hjälp av kommandot AZ MySQL server Firewall-Rule Create. När både Start-IP och slut-IP är inställt på ```0.0.0.0``` , öppnas brand väggen bara för andra Azure-tjänster som inte har en statisk IP-adress för att ansluta till servern.

```azurecli
az mysql flexible-server firewall-rule create --name allanyAzureIPs --server <mysql-server-name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

### <a name="connect-to-production-mysql-server-locally"></a>Ansluta lokalt till MySQL-produktionsservern

Anslut till MySQL-server i Azure via det lokala terminalfönstret. Använd det värde som du angav tidigare för ```<admin-user>``` och ```<mysql-server-name>``` . När du uppmanas att ange ett lösenord använder du lösenordet som du angav när du skapade databasen i Azure.

```bash
mysql -u <admin-user> -h <mysql-server-name>.mysql.database.azure.com -P 3306 -p
```

### <a name="create-a-production-database"></a>Skapa en produktionsdatabas

Skapa en databas i `mysql`-prompten.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Skapa en användare med behörigheter

Skapa en databasanvändare med namnet _phpappuser_ och ge användaren alla privilegier i `sampledb`-databasen. För enkelhetens skull använder du _MySQLAzure2020_ som lösen ord.

```sql
CREATE USER 'phpappuser' IDENTIFIED BY 'MySQLAzure2020';
GRANT ALL PRIVILEGES ON sampledb.* TO 'phpappuser';
```

Skriv `quit` för att avsluta serveranslutningen.

```sql
quit
```

## <a name="connect-app-to-mysql-flexible-server"></a>Anslut appen till MySQL-flexibel Server

I det här steget ansluter du PHP-programmet till MySQL-databasen som du skapade i Azure Database for MySQL.

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>Konfigurera databasanslutningen

Skapa en _.env.production_-fil i lagringsplatsens rot och kopiera in följande variabler i filen. Ersätt plats hållaren _&lt; MySQL-Server-Name>_ både i *DB_HOST* och *DB_USERNAME*.

```
APP_ENV=production
APP_DEBUG=true
APP_KEY=

DB_CONNECTION=mysql
DB_HOST=<mysql-server-name>.mysql.database.azure.com
DB_DATABASE=sampledb
DB_USERNAME=phpappuser
DB_PASSWORD=MySQLAzure2017
MYSQL_SSL=true
```

Spara ändringarna.

> [!TIP]
> För att skydda din MySQL-anslutningsinformation är den här filen redan undantagen från Git-lagringsplatsen (se _.gitignore_ i lagringsplatsens rot). Senare får du lära dig hur du konfigurerar miljövariabler i App Service för att ansluta till din databas i Azure Database for MySQL. När du använder miljövariabler behöver du inte *.env*-filen i App Service.
>

### <a name="configure-tlsssl-certificate"></a>Konfigurera TLS/SSL-certifikat

Som standard tvingar MySQL-flexibla servrar TLS-anslutningar från klienter. Om du vill ansluta till MySQL-databasen i Azure måste du använda [ _. pem_ -certifikatet som tillhandahålls av Azure Database for MySQL flexibel Server](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem). Hämta [det här certifikatet](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)) och placera det i mappen **SSL** i den lokala kopian av exempel appens lagrings plats.

Öppna _config/database.php_ och lägg till parametrarna `sslmode` och `options` i `connections.mysql`, som i följande kod.

```php
'mysql' => [
    ...
    'sslmode' => env('DB_SSLMODE', 'prefer'),
    'options' => (env('MYSQL_SSL') && extension_loaded('pdo_mysql')) ? [
        PDO::MYSQL_ATTR_SSL_KEY    => '/ssl/DigiCertGlobalRootCA.crt.pem',
    ] : []
],
```

### <a name="test-the-application-locally"></a>Testa appen lokalt

Kör Laravel-databasmigreringar med _.env.production_ som miljöfil för att skapa tabellerna i din MySQL-databas i Azure Database for MySQL. Tänk på att anslutningsinformationen till din MySQL-databas i Azure finns i _.env.production_.

```bash
php artisan migrate --env=production --force
```

_.env.production_ innehåller inte någon giltig programnyckel ännu. Generera en ny nyckel för den i terminalen.

```bash
php artisan key:generate --env=production --force
```

Kör exempelprogrammet med _.env.production_ som miljöfil.

```bash
php artisan serve --env=production
```

Navigera till `http://localhost:8000`. Om sidan läses in utan fel ansluter PHP-appen till MySQL-databasen i Azure.

Lägg till några uppgifter på sidan.

:::image type="content" source="media/tutorial-php-database-app/mysql-connect-success.png" alt-text="PHP har anslutits till Azure Database for MySQL":::

Om du vill stoppa PHP skriver du `Ctrl + C` i terminalen.

### <a name="commit-your-changes"></a>Genomföra ändringarna

Kör följande Git-kommandon för att genomföra ändringarna:

```bash
git add .
git commit -m "database.php updates"
```

Din app är klar att distribuera.

## <a name="deploy-to-azure"></a>Distribuera till Azure

I det här steget distribuerar du din MySQL-anslutna PHP-app till Azure App Service.

### <a name="configure-a-deployment-user"></a>Konfigurera en distributionsanvändare

FTP och lokal git kan distribueras till en Azure-webbapp med hjälp av en distributions användare. När du har konfigurerat din distributions användare kan du använda den för alla dina Azure-distributioner. Ditt användar namn och lösen ord för distribution på konto nivå skiljer sig från dina autentiseringsuppgifter för Azure-prenumerationen.

Konfigurera distributions användaren genom att köra kommandot [AZ webapp Deployment User set](/cli/azure/webapp/deployment/user#az-webapp-deployment-user-set) i Azure Cloud Shell. Ersätt _&lt; username>_ och _&lt; Password>_ med ditt användar namn och lösen ord för distributionen.

Användar namnet måste vara unikt inom Azure, och för lokala git-push-meddelanden får inte innehålla symbolen @.
Lösen ordet måste innehålla minst åtta tecken, med två av följande tre element: bokstäver, siffror och symboler.

```bash
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku F1 --is-linux
```

JSON-utdata visar lösen ordet som null. Om du får en konflikt. Information: 409-fel, ändra användar namnet. Om du får en "felaktig begäran". Information: 400-fel, Använd ett starkare lösen ord. **Registrera ditt användar namn och lösen ord som ska användas för att distribuera dina webb program.**

### <a name="create-an-app-service-plan"></a>Skapa en App Service-plan

I Cloud Shell skapar du ett App Service plan i resurs gruppen med kommandot [AZ AppService plan Create](/cli/azure/appservice/plan#az-appservice-plan-create) . I följande exempel skapas en App Service plan med namnet myAppServicePlan i den kostnads fria pris nivån (--SKU F1) och i en Linux-behållare (--är-Linux).

AZ AppService plan Create--Name myAppServicePlan--resurs-Group myResourceGroup--SKU F1--är-Linux

<a name="create"></a>

### <a name="create-a-web-app"></a>Skapa en webbapp

Skapa en [webbapp](../../app-service/overview.md#app-service-on-linux) i myAppServicePlan-App Service plan.

I Cloud Shell kan du använda kommandot [AZ webapp Create](/cli/azure/webapp#az-webapp-create) . I följande exempel ersätter du _&lt; app-name->_ med ett globalt unikt app-namn (giltiga tecken är `a-z` , `0-9` och `-` ). Körningen har angetts till `PHP|7.0`. Om du vill se alla körningar som stöds kör du [AZ webapp List-Runtimes--Linux](/cli/azure/webapp#az-webapp-list-runtimes).

```bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.3" --deployment-local-git
```

När webbappen har skapats visar Azure CLI utdata liknande den i följande exempel:

```
Local git is configured with url of 'https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app-name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

Du har skapat en tom ny webbapp med git-distribution aktiverad.

> [!NOTE]
> URL: en för git-fjärrdatabasen visas i egenskapen deploymentLocalGitUrl med formatet https:// <username> @ <app-name>. scm.azurewebsites.net/<app-name>. git. Spara den här URL:en, eftersom du behöver den senare.

### <a name="configure-database-settings"></a>Konfigurera databasinställningarna

I App Service ställer du in miljövariabler som _appinställningar_ med kommandot [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set).

Följande kommando konfigurerar appinställningarna `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` och `DB_PASSWORD`. Ersätt plats hållarnas _&lt; namn>_ och _&lt; mysql-Server-Name>_.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DB_HOST="<mysql-server-name>.mysql.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="phpappuser" DB_PASSWORD="MySQLAzure2017" MYSQL_SSL="true"
```

Du kan komma åt inställningarna med PHP-metoden [getenv](https://www.php.net/manual/en/function.getenv.php). I Laravel-koden används en [env](https://laravel.com/docs/5.4/helpers#method-env)-omslutning över PHP `getenv`. MySQL-konfigurationen i _config/database.php_ ser till exempel ut som följande kod:

```php
'mysql' => [
    'driver'    => 'mysql',
    'host'      => env('DB_HOST', 'localhost'),
    'database'  => env('DB_DATABASE', 'forge'),
    'username'  => env('DB_USERNAME', 'forge'),
    'password'  => env('DB_PASSWORD', ''),
    ...
],
```

### <a name="configure-laravel-environment-variables"></a>Konfigurera Laravel-miljövariabler

Laravel måste ha en programnyckel i App Service. Du kan konfigurera den med appinställningar.

Gå till det lokala terminalfönstret och använd `php artisan` för att generera en ny programnyckel utan att spara den i _.env_.

```bash
php artisan key:generate --show
```

I Cloud Shell anger du program nyckeln i App Service-appen med hjälp av [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) kommandot. Ersätt plats hållarna _&lt; App-Name>_ och _&lt; outputofphpartisankey: generate>_.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings APP_KEY="<output_of_php_artisan_key:generate>" APP_DEBUG="true"
```

`APP_DEBUG="true"` säger åt Laravel att returnera felsökningsinformation när den distribuerade appen påträffar fel. När du kör en produktionsapp anger du värdet `false`, vilket är säkrare.

### <a name="set-the-virtual-application-path"></a>Ange sökväg till virtuella program

[Laravel program livs cykel](https://laravel.com/docs/5.4/lifecycle) börjar i den _offentliga_ katalogen i stället för programmets rot Katalog. PHP Docker-standardavbildningen för App Service använder Apache, och låter dig inte anpassa `DocumentRoot` för Laravel. Däremot kan du använda `.htaccess` för att skriva om alla begäranden så att de pekar till _/public_ i stället för rotkatalogen. I lagringsplatsens rot har en `.htaccess` redan lagts till för detta ändamål. Därmed är Laravel-appen klar att distribueras.

Mer information finns i [ändra plats roten](../../app-service/configure-language-php.md?pivots=platform-linux#change-site-root).

### <a name="push-to-azure-from-git"></a>Skicka till Azure från Git

I det lokala terminalfönstret kan du lägga till en Azure-fjärrdatabas till din lokala Git-databas. Ersätt _&lt; deploymentLocalGitUrl-från-Create-Step>_ med URL: en för den git-fjärrdatabas som du sparade från [skapa en webbapp](#create-a-web-app).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Skicka till Azure-fjärrdatabasen för att distribuera appen med följande kommando. När git Credential Manager uppmanas att ange autentiseringsuppgifter, se till att du anger de autentiseringsuppgifter som du skapade i **Konfigurera en distributions användare**, inte de autentiseringsuppgifter som du använder för att logga in på Azure Portal.

```bash
git push azure main
```

Det kan ett par minuter att köra kommandot. Medan det körs visas information liknande den i följande exempel:

<pre>
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 291 bytes | 0 bytes/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Updating branch 'main'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'a5e076db9c'.
remote: Running custom deployment command...
remote: Running deployment command...
...
&lt; Output has been truncated for readability &gt;
</pre>

### <a name="browse-to-the-azure-app"></a>Bläddra till Azure-appen

Bläddra till `http://<app-name>.azurewebsites.net` och lägg till några uppgifter i listan.

:::image type="content" source="media/tutorial-php-database-app/php-mysql-in-azure.png" alt-text="PHP-webbapp i Azure":::

Grattis! Du kör en datadriven PHP-app i Azure App Service.

## <a name="update-model-locally-and-redeploy"></a>Uppdatera modell lokalt och distribuera om

I det här steget gör du en enkel ändring i `task`-datamodellen och webbappen och publicerar sedan uppdateringen till Azure.

För uppgiftsscenariot ändrar du programmet så att du kan markera en uppgift som slutförd.

### <a name="add-a-column"></a>Lägga till en kolumn

Visa det lokala terminalfönstret och gå till roten för Git-lagringsplatsen.

Generera en ny databasmigrering för `tasks`-tabellen:

```bash
php artisan make:migration add_complete_column --table=tasks
```

Det här kommandot visar namnet på migreringsfilen som genereras. Leta reda på filen i _database/migrations_ och öppna den.

Ersätt metoden `up` med följande kod:

```php
public function up()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->boolean('complete')->default(False);
    });
}
```

Föregående kod lägger till en boolesk kolumn i `tasks`-tabellen med namnet `complete`.

Ersätt metoden `down` med följande kod för återställningsåtgärden:

```php
public function down()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->dropColumn('complete');
    });
}
```

Visa det lokala terminalfönstret och kör Laravel-databasemigreringar för att göra ändringen i den lokala databasen.

```bash
php artisan migrate
```

Modellen `Task` (se _app/Task.php_) mappar till `tasks`-tabellen som standard, baserat på [Laravel-namngivningskonventionen](https://laravel.com/docs/5.4/eloquent#defining-models).

### <a name="update-application-logic"></a>Uppdatera programlogik

Öppna filen *routes/web.php*. Här definieras programmets vägar och affärslogik.

I slutet av filen lägger du till en väg med följande kod:

```php
/**
 * Toggle Task completeness
 */
Route::post('/task/{id}', function ($id) {
    error_log('INFO: post /task/'.$id);
    $task = Task::findOrFail($id);

    $task->complete = !$task->complete;
    $task->save();

    return redirect('/');
});
```

Föregående kod gör en enkel uppdatering till datamodellen genom att ändra värdet för `complete`.

### <a name="update-the-view"></a>Uppdatera vyn

Öppna filen *resources/views/tasks.blade.php*. Leta reda på starttaggen `<tr>` och ersätt den med:

```html
<tr class="{{ $task->complete ? 'success' : 'active' }}" >
```

Föregående kod ändrar färg på raden beroende på om uppgiften är slutförd.

På nästa rad finns följande kod:

```html
<td class="table-text"><div>{{ $task->name }}</div></td>
```

Ersätt hela raden med följande kod:

```html
<td>
    <form action="{{ url('task/'.$task->id) }}" method="POST">
        {{ csrf_field() }}

        <button type="submit" class="btn btn-xs">
            <i class="fa {{$task->complete ? 'fa-check-square-o' : 'fa-square-o'}}"></i>
        </button>
        {{ $task->name }}
    </form>
</td>
```

Föregående kod lägger till knappen Skicka som refererar till den väg som du angav tidigare.

### <a name="test-the-changes-locally"></a>Testa ändringarna lokalt

Visa det lokala terminalfönstret och kör utvecklingsservern från rotkatalogen på Git-lagringsplatsen.

```bash
php artisan serve
```

Om du vill se ändringen i uppgiftsstatusen går du till `http://localhost:8000` och markerar kryssrutan.

:::image type="content" source="media/tutorial-php-database-app/complete-checkbox.png" alt-text="Kryssruta lades till för uppgift":::

Om du vill stoppa PHP skriver du `Ctrl + C` i terminalen.

### <a name="publish-changes-to-azure"></a>Publicera ändringar till Azure

Gå till det lokala terminalfönstret och kör Laravel-databasmigreringar med produktionsanslutningssträngen för att genomföra ändringen i Azure-databasen.

```bash
php artisan migrate --env=production --force
```

Genomför alla ändringar på Git och skicka sedan kodändringarna till Azure.

```bash
git add .
git commit -m "added complete checkbox"
git push azure main
```

När `git push` har slutförts så kan du gå till Azure-appen och prova de nya funktionerna.

:::image type="content" source="media/tutorial-php-database-app/complete-checkbox-published.png" alt-text="Modell- och databasändringar som är publicerade i Azure":::

Om du har lagt till några uppgifter finns de kvar i databasen. Uppdateringar i dataschemat påverkar inte befintliga data.

## <a name="clean-up-resources"></a>Rensa resurser
I de föregående stegen skapade du Azure-resurser i en resursgrupp. Om du inte tror att du behöver dessa resurser i framtiden tar du bort resursgruppen genom att köra följande kommando i Cloud Shell:

```bash
az group delete --name myResourceGroup
```

<a name="next"></a>

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hantera dina resurser i Azure Portal](../../azure-resource-manager/management/manage-resources-portal.md) <br/>
> [!div class="nextstepaction"]
> [Så här hanterar du servern](how-to-manage-server-cli.md)
