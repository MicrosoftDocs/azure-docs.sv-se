---
title: 'Självstudie: PHP-app med MySQL'
description: Lär dig hur du får igång en PHP-app i Azure med anslutning till en MySQL-databas i Azure. Laravel används i självstudien.
ms.assetid: 14feb4f3-5095-496e-9a40-690e1414bd73
ms.devlang: php
ms.topic: tutorial
ms.date: 06/15/2020
ms.custom: mvc, cli-validate, seodec18, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: e2a4d8635a1a793fd8a5d98b8957bea6b36cecda
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97934942"
---
# <a name="tutorial-build-a-php-and-mysql-app-in-azure-app-service"></a>Självstudie: bygga en PHP-och MySQL-app i Azure App Service

::: zone pivot="platform-windows"  

[Azure App Service](overview.md) ger en mycket skalbar och automatisk korrigering av webb värd tjänst med hjälp av Windows-operativsystemet. I den här självstudien visas hur du skapar en PHP-app i Azure och ansluter den till en MySQL-databas. När du är klar har du en [Laravel](https://laravel.com/) -app som körs på Azure App Service i Windows.

::: zone-end

::: zone pivot="platform-linux"

[Azure App Service](overview.md) ger en mycket skalbar och automatisk korrigering av webb värd tjänst som använder Linux-operativsystemet. I den här självstudien visas hur du skapar en PHP-app i Azure och ansluter den till en MySQL-databas. När du är klar har du en [Laravel](https://laravel.com/) -app som körs på Azure App Service på Linux.

::: zone-end

:::image type="content" source="./media/tutorial-php-mysql-app/complete-checkbox-published.png" alt-text="Skärm bild av ett exempel på en PHP-app med titeln uppgiftslista.":::

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en MySQL-databas i Azure
> * ansluta en PHP-app till MySQL
> * distribuera appen till Azure
> * uppdatera datamodellen och distribuera om appen
> * strömma diagnostikloggar från Azure
> * hantera appen i Azure-portalen.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

- [Installera Git](https://git-scm.com/)
- [Installera PHP 5.6.4 eller senare](https://php.net/downloads.php)
- [Installera Composer](https://getcomposer.org/doc/00-intro.md)
- Aktivera följande PHP-tillägg som behövs för Laravel: OpenSSL, PDO-MySQL, Mbstring, Tokenizer och XML
- [Installera och starta MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html)
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)] 

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

Använd kommandot `cd` för att komma till en arbetskatalog i terminalfönstret.

Klona exempellagringsplatsen med följande kommando.

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

![PHP ansluter till MySQL](./media/tutorial-php-mysql-app/mysql-connect-success.png)

Om du vill stoppa PHP skriver du `Ctrl + C` i terminalen.

## <a name="create-mysql-in-azure"></a>Skapa MySQL i Azure

I det här steget skapar du en MySQL-databas i [Azure Database for MySQL](../mysql/index.yml). Senare kommer du att konfigurera PHP-appen för att ansluta till den här databasen.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-mysql-server"></a>Skapa en MySQL-server

I Cloud Shell skapar du en server i Azure Database for MySQL med [`az mysql server create`](/cli/azure/mysql/server?view=azure-cli-latest&preserve-view=true#az-mysql-server-create) kommandot.

I följande kommando ersätter du ett unikt server namn för *\<mysql-server-name>* plats hållaren, ett användar namn för *\<admin-user>* och ett lösen ord för *\<admin-password>*  plats hållaren. Det här servernamnet används som en del av MySQL-slutpunkten (`https://<mysql-server-name>.mysql.database.azure.com`), så namnet måste vara unikt för alla servrar i Azure. Mer information om hur du väljer MySQL DB SKU finns i [skapa en Azure Database for MySQL-server](../mysql/quickstart-create-mysql-server-database-using-azure-cli.md#create-an-azure-database-for-mysql-server).

```azurecli-interactive
az mysql server create --resource-group myResourceGroup --name <mysql-server-name> --location "West Europe" --admin-user <admin-user> --admin-password <admin-password> --sku-name B_Gen5_1
```

När MySQL-servern skapas visar Azure CLI information som ser ut ungefär så här:

<pre>
{
  "administratorLogin": "&lt;admin-user&gt;",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "&lt;mysql-server-name&gt;.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/&lt;mysql-server-name&gt;",
  "location": "westeurope",
  "name": "&lt;mysql-server-name&gt;",
  "resourceGroup": "myResourceGroup",
  ...
  -  &lt; Output has been truncated for readability &gt;
}
</pre>

### <a name="configure-server-firewall"></a>Konfigurera serverbrandväggen

I Cloud Shell skapar du en brand Väggs regel för MySQL-servern för att tillåta klient anslutningar med hjälp av [`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule?view=azure-cli-latest&preserve-view=true#az-mysql-server-firewall-rule-create) kommandot. När både start-IP och slut-IP har angetts till 0.0.0.0 öppnas brandväggen endast för andra Azure-resurser. 

```azurecli-interactive
az mysql server firewall-rule create --name allAzureIPs --server <mysql-server-name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP] 
> Du kan begränsa brandväggsregeln ännu mer genom att [endast använda de utgående IP-adresser som används av din app](overview-inbound-outbound-ips.md#find-outbound-ips).
>

Kör kommandot igen i Cloud Shell för att tillåta åtkomst från den lokala datorn genom att ersätta *\<your-ip-address>* med [din lokala IPv4 IP-adress](https://www.whatsmyip.org/).

```azurecli-interactive
az mysql server firewall-rule create --name AllowLocalClient --server <mysql-server-name> --resource-group myResourceGroup --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address>
```

### <a name="connect-to-production-mysql-server-locally"></a>Ansluta lokalt till MySQL-produktionsservern

Anslut till MySQL-server i Azure via det lokala terminalfönstret. Använd värdet du angav tidigare för _&lt; admin-user>_ och _&lt; mysql-Server-Name>_. När du uppmanas att ange ett lösenord använder du lösenordet som du angav när du skapade databasen i Azure.

```bash
mysql -u <admin-user>@<mysql-server-name> -h <mysql-server-name>.mysql.database.azure.com -P 3306 -p
```

### <a name="create-a-production-database"></a>Skapa en produktionsdatabas

Skapa en databas i `mysql`-prompten.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Skapa en användare med behörigheter

Skapa en databasanvändare med namnet _phpappuser_ och ge användaren alla privilegier i `sampledb`-databasen. För enkelhetens skull använder du _MySQLAzure2017_ som lösen ord.

```sql
CREATE USER 'phpappuser' IDENTIFIED BY 'MySQLAzure2017'; 
GRANT ALL PRIVILEGES ON sampledb.* TO 'phpappuser';
```

Skriv `quit` för att avsluta serveranslutningen.

```sql
quit
```

## <a name="connect-app-to-azure-mysql"></a>Ansluta appen till Azure MySQL

I det här steget ansluter du PHP-programmet till MySQL-databasen som du skapade i Azure Database for MySQL.

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>Konfigurera databasanslutningen

Skapa en _.env.production_-fil i lagringsplatsens rot och kopiera in följande variabler i filen. Ersätt placeholder_ &lt; MySQL-server-name>_ i både *DB_HOST* och *DB_USERNAME*.

```
APP_ENV=production
APP_DEBUG=true
APP_KEY=

DB_CONNECTION=mysql
DB_HOST=<mysql-server-name>.mysql.database.azure.com
DB_DATABASE=sampledb
DB_USERNAME=phpappuser@<mysql-server-name>
DB_PASSWORD=MySQLAzure2017
MYSQL_SSL=true
```

Spara ändringarna.

> [!TIP]
> För att skydda din MySQL-anslutningsinformation är den här filen redan undantagen från Git-lagringsplatsen (se _.gitignore_ i lagringsplatsens rot). Senare får du lära dig hur du konfigurerar miljövariabler i App Service för att ansluta till din databas i Azure Database for MySQL. När du använder miljövariabler behöver du inte *.env*-filen i App Service.
>

### <a name="configure-tlsssl-certificate"></a>Konfigurera TLS/SSL-certifikat

Som standard tvingar Azure Database for MySQL TLS-anslutningar från klienter. För att ansluta till din MySQL-databas i Azure måste du använda [_.pem_-certifikatet som tillhandahålls av Azure Database for MySQL](../mysql/howto-configure-ssl.md).

Öppna _config/database.php_ och lägg till parametrarna `sslmode` och `options` i `connections.mysql`, som i följande kod.

::: zone pivot="platform-windows"  

```php
'mysql' => [
    ...
    'sslmode' => env('DB_SSLMODE', 'prefer'),
    'options' => (env('MYSQL_SSL')) ? [
        PDO::MYSQL_ATTR_SSL_KEY    => '/ssl/BaltimoreCyberTrustRoot.crt.pem', 
    ] : []
],
```

::: zone-end

::: zone pivot="platform-linux"

```php
'mysql' => [
    ...
    'sslmode' => env('DB_SSLMODE', 'prefer'),
    'options' => (env('MYSQL_SSL') && extension_loaded('pdo_mysql')) ? [
        PDO::MYSQL_ATTR_SSL_KEY    => '/ssl/BaltimoreCyberTrustRoot.crt.pem',
    ] : []
],
```

::: zone-end

I den här kursen finns certifikatet `BaltimoreCyberTrustRoot.crt.pem` på lagringsplatsen så att du enkelt kommer åt det. 

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

![PHP har anslutits till Azure Database for MySQL](./media/tutorial-php-mysql-app/mysql-connect-success.png)

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

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Skapa en App Service-plan

::: zone pivot="platform-windows"  

[!INCLUDE [Create app service plan no h](../../includes/app-service-web-create-app-service-plan-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Create app service plan no h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

::: zone-end

<a name="create"></a>
### <a name="create-a-web-app"></a>Skapa en webbapp

::: zone pivot="platform-windows"  

[!INCLUDE [Create web app no h](../../includes/app-service-web-create-web-app-php-no-h.md)] 

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-php-linux-no-h.md)] 

::: zone-end

### <a name="configure-database-settings"></a>Konfigurera databasinställningarna

I App Service ställer du in miljövariabler som _appinställningar_ med kommandot [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest&preserve-view=true#az-webapp-config-appsettings-set).

Följande kommando konfigurerar appinställningarna `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` och `DB_PASSWORD`. Ersätt plats hållarnas _&lt; namn>_ och _&lt; mysql-Server-Name>_.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DB_HOST="<mysql-server-name>.mysql.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="phpappuser@<mysql-server-name>" DB_PASSWORD="MySQLAzure2017" MYSQL_SSL="true"
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

I Cloud Shell anger du program nyckeln i App Service-appen med hjälp av [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest&preserve-view=true#az-webapp-config-appsettings-set) kommandot. Ersätt plats hållarna _&lt; App-Name>_ och _&lt; outputofphpartisankey: generate>_.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings APP_KEY="<output_of_php_artisan_key:generate>" APP_DEBUG="true"
```

`APP_DEBUG="true"` säger åt Laravel att returnera felsökningsinformation när den distribuerade appen påträffar fel. När du kör en produktionsapp anger du värdet `false`, vilket är säkrare.

### <a name="set-the-virtual-application-path"></a>Ange sökväg till virtuella program

::: zone pivot="platform-windows"  

Ange den virtuella sökvägen för appen. Det här steget är nödvändigt eftersom [Laravel-programmets livscykel](https://laravel.com/docs/5.4/lifecycle) börjar i den _offentliga_ katalogen i stället för programmets rotkatalog. Andra PHP-ramverk vilkas livscykel startar i rotkatalogen fungerar utan manuell konfiguration av den virtuella programsökvägen.

I Cloud Shell anger du den virtuella program Sök vägen med hjälp av [`az resource update`](/cli/azure/resource#az-resource-update) kommandot. Ersätt _&lt; appens namn>_ plats hållaren.

```azurecli-interactive
az resource update --name web --resource-group myResourceGroup --namespace Microsoft.Web --resource-type config --parent sites/<app_name> --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" --api-version 2015-06-01
```

Som standard pekar Azure App Service den virtuella rot katalogen för programmet ( _/_ ) till rot katalogen för de distribuerade programfilerna (_sites\wwwroot_).

::: zone-end

::: zone pivot="platform-linux"

[Laravel program livs cykel](https://laravel.com/docs/5.4/lifecycle) börjar i den _offentliga_ katalogen i stället för programmets rot Katalog. PHP Docker-standardavbildningen för App Service använder Apache, och låter dig inte anpassa `DocumentRoot` för Laravel. Däremot kan du använda `.htaccess` för att skriva om alla begäranden så att de pekar till _/public_ i stället för rotkatalogen. I lagringsplatsens rot har en `.htaccess` redan lagts till för detta ändamål. Därmed är Laravel-appen klar att distribueras.

Mer information finns i [ändra plats roten](configure-language-php.md#change-site-root).

::: zone-end

### <a name="push-to-azure-from-git"></a>Skicka till Azure från Git

::: zone pivot="platform-windows"  

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

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

> [!NOTE]
> Du kanske märker att distributionsprocessen installerar [Composer](https://getcomposer.org/)-paket i slutet. App Service kör inte dessa automatiseringar under standarddistributionen. Den här exempellagringsplatsen har tre extra filer i rotkatalogen för detta:
>
> - `.deployment` – Den här filen skickar ett meddelande till App Service om att köra `bash deploy.sh` som anpassat distributionsskript.
> - `deploy.sh` – Det anpassade distributionsskriptet. Om du granskar filen ser du att den kör `php composer.phar install` efter `npm install`.
> - `composer.phar` – Composer-pakethanteraren.
>
> Du kan använda den här metoden för att lägga till steg i den Git-baserade distributionen till App Service. Mer information finns i [Anpassat distributionsskript](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).
>

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

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

::: zone-end

### <a name="browse-to-the-azure-app"></a>Bläddra till Azure-appen

Bläddra till `http://<app-name>.azurewebsites.net` och lägg till några uppgifter i listan.

:::image type="content" source="./media/tutorial-php-mysql-app/php-mysql-in-azure.png" alt-text="Skärm bild av Azure App-exemplet med titeln uppgiftslista visar nya aktiviteter tillagda.":::

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

![Kryssruta lades till för uppgift](./media/tutorial-php-mysql-app/complete-checkbox.png)

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

![Modell- och databasändringar som är publicerade i Azure](media/tutorial-php-mysql-app/complete-checkbox-published.png)

Om du har lagt till några uppgifter finns de kvar i databasen. Uppdateringar i dataschemat påverkar inte befintliga data.

## <a name="stream-diagnostic-logs"></a>Strömma diagnostikloggar

::: zone pivot="platform-windows"  

När PHP-appen körs i Azure App Service kan du skicka konsolloggarna till din terminal. På så sätt kan du få samma diagnostikmeddelanden för att felsöka programfel.

Om du vill starta logg strömningen använder du [`az webapp log tail`](/cli/azure/webapp/log?view=azure-cli-latest&preserve-view=true#az-webapp-log-tail) kommandot i Cloud Shell.

```azurecli-interactive
az webapp log tail --name <app_name> --resource-group myResourceGroup
```

Uppdatera Azure-app i webbläsaren så hämtas webbtrafik när loggströmningen har startats. Du kan nu se konsolloggarna som skickas till terminalen. Om du inte ser konsolloggarna omedelbart kan du titta efter igen efter 30 sekunder.

Skriv `Ctrl`+`C` när som helst för att stoppa loggströmningen.

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end

> [!TIP]
> Ett PHP-program kan använda standarden [error_log()](https://php.net/manual/function.error-log.php) för att skapa utdata till konsolen. Exempelprogrammet använder den här metoden i _app/Http/routes.php_.
>
> [Laravel använder Monolog](https://laravel.com/docs/5.4/errors) som loggningsprovider i webbramverket. Information om hur du får Monolog att skicka meddelanden till konsolen finns i artikeln om [PHP och hur du använder monolog för att skicka loggar till konsolen (php://out)](https://stackoverflow.com/questions/25787258/php-how-to-use-monolog-to-log-to-console-php-out).
>
>

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
> * Skapa en MySQL-databas i Azure
> * ansluta en PHP-app till MySQL
> * distribuera appen till Azure
> * uppdatera datamodellen och distribuera om appen
> * strömma diagnostikloggar från Azure
> * hantera appen i Azure-portalen.

Gå vidare till nästa självstudie där du får lära dig att mappa ett anpassat DNS-namn till appen.

> [!div class="nextstepaction"]
> [Självstudie: mappa ett anpassat DNS-namn till din app](app-service-web-tutorial-custom-domain.md)

Eller kolla ut andra resurser:

> [!div class="nextstepaction"]
> [Konfigurera PHP-app](configure-language-php.md)
