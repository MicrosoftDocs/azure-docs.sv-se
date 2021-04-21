---
title: Konfigurera PHP-appar
description: Lär dig hur du konfigurerar en PHP-app i de interna Windows-instanserna eller i en förbyggd PHP-container i Azure App Service. I artikeln visas de vanligaste konfigurationsåtgärderna.
ms.devlang: php
ms.topic: article
ms.date: 06/02/2020
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: c40bc01553b9e848d668c0a699e9dcc9929f079e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779335"
---
# <a name="configure-a-php-app-for-azure-app-service"></a>Konfigurera en PHP-app för Azure App Service

Den här guiden visar hur du konfigurerar php-webbappar, mobila backend-datorer och API-appar i Azure App Service.

Den här guiden innehåller viktiga begrepp och instruktioner för PHP-utvecklare som distribuerar appar till App Service. Om du aldrig har använt Azure App Service följer du [självstudiekursen PHP-snabbstart](quickstart-php.md) och [PHP med MySQL](tutorial-php-mysql-app.md) först.

## <a name="show-php-version"></a>Visa PHP-version

::: zone pivot="platform-windows"  

Om du vill visa den aktuella PHP-versionen kör du följande kommando i [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query phpVersion
```

Om du vill visa alla PHP-versioner som stöds kör du följande kommando [i Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes | grep php
```

::: zone-end

::: zone pivot="platform-linux"

Om du vill visa den aktuella PHP-versionen kör du följande kommando i [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Om du vill visa alla PHP-versioner som stöds kör du följande kommando [i Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

::: zone-end

## <a name="set-php-version"></a>Ange PHP-version

::: zone pivot="platform-windows"  

Kör följande kommando i Cloud Shell [att](https://shell.azure.com) ange PHP-versionen till 7.4:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --php-version 7.4
```

::: zone-end

::: zone pivot="platform-linux"

Kör följande kommando i Cloud Shell [för](https://shell.azure.com) att ange PHP-versionen till 7.2:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "PHP|7.2"
```

::: zone-end

::: zone pivot="platform-windows"  

## <a name="run-composer"></a>Kör Composer

Om du vill App Service att köra [Composer](https://getcomposer.org/) vid distributionen är det enklaste sättet att inkludera Composer i din lagringsplats.

Från ett lokalt terminalfönster ändrar du katalogen till lagringsplatsens rot och följer anvisningarna i Ladda ned [Composer](https://getcomposer.org/download/) för att ladda ned *composer.phar* till katalogroten.

Kör följande kommandon (du behöver [npm installerat):](https://www.npmjs.com/get-npm)

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

Lagringsplatsens rot har nu två ytterligare filer: *.deployment* *och deploy.sh*.

Öppna *deploy.sh* och leta reda på `Deployment` avsnittet, som ser ut så här:

```bash
##################################################################################################################################
# Deployment
# ----------
```

Lägg till det kodavsnitt som du behöver för att köra *det verktyg som krävs i* slutet av `Deployment` avsnittet:

```bash
# 4. Use composer
echo "$DEPLOYMENT_TARGET"
if [ -e "$DEPLOYMENT_TARGET/composer.json" ]; then
  echo "Found composer.json"
  pushd "$DEPLOYMENT_TARGET"
  php composer.phar install $COMPOSER_ARGS
  exitWithMessageOnError "Composer install failed"
  popd
fi
```

Genomför alla ändringar och distribuera din kod med Hjälp av Git eller Zip-distribution med byggautomatisering aktiverat. Composer bör nu köras som en del av distributionsautomationen.

## <a name="run-gruntbowergulp"></a>Kör Grunt/Bower/Gulp

Om du App Service köra populära automatiseringsverktyg vid distributionen, till exempel Grunt, Bower eller Gulp, måste du ange ett anpassat [distributionsskript.](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) App Service kör det här skriptet när du distribuerar med Git eller med [Zip-distribution med](deploy-zip.md) build-automatisering aktiverat. 

Om du vill att lagringsplatsen ska kunna köra dessa verktyg måste du lägga till dem i beroendena i *package.jspå.* Exempel:

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

Från ett lokalt terminalfönster ändrar du katalogen till lagringsplatsens rot och kör följande kommandon (du behöver [npm installerat):](https://www.npmjs.com/get-npm)

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

Lagringsplatsens rot har nu två ytterligare filer: *.deployment* *och deploy.sh*.

Öppna *deploy.sh* och leta upp `Deployment` avsnittet, som ser ut så här:

```bash
##################################################################################################################################
# Deployment
# ----------
```

Det här avsnittet slutar med att köra `npm install --production` . Lägg till det kodavsnitt som du behöver för att köra *det verktyg som krävs i* slutet av `Deployment` avsnittet:

- [Bower](#bower)
- [Klunk](#gulp)
- [Grunt](#grunt)

Se ett [exempel i MEAN.js ,](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135)där distributionsskriptet även kör ett anpassat `npm install` kommando.

### <a name="bower"></a>Bower

Det här kodfragmentet kör `bower install` .

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Klunk

Det här kodfragmentet kör `gulp imagemin` .

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

Det här kodfragmentet kör `grunt` .

```bash
if [ -e "$DEPLOYMENT_TARGET/Gruntfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/grunt
  exitWithMessageOnError "Grunt failed"
  cd - > /dev/null
fi
```

::: zone-end

::: zone pivot="platform-linux"

## <a name="customize-build-automation"></a>Anpassa byggautomatisering

Om du distribuerar din app med Git- eller zip-paket där byggautomatisering är aktiverat går App Service i följande sekvens:

1. Kör anpassat skript om det anges av `PRE_BUILD_SCRIPT_PATH` .
1. Kör `php composer.phar install`.
1. Kör anpassat skript om det anges av `POST_BUILD_SCRIPT_PATH` .

`PRE_BUILD_COMMAND` och `POST_BUILD_COMMAND` är miljövariabler som är tomma som standard. Om du vill köra förbyggkommandon definierar du `PRE_BUILD_COMMAND` . Om du vill köra kommandon efter kom build definierar du `POST_BUILD_COMMAND` .

I följande exempel anges de två variablerna till en serie kommandon, avgränsade med kommatecken.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Ytterligare miljövariabler för att anpassa byggautomatisering finns i [Oryx-konfiguration.](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md)

Mer information om hur App Service kör och skapar PHP-appar i Linux finns i [Oryx-dokumentationen:](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/php.md)Så här identifieras och byggs PHP-appar.

## <a name="customize-start-up"></a>Anpassa start

Som standard kör den inbyggda PHP-containern Apache-servern. Vid start körs `apache2ctl -D FOREGROUND"` . Om du vill kan du köra ett annat kommando vid start genom att köra följande kommando i [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

::: zone-end

## <a name="access-environment-variables"></a>Få åtkomst till miljövariabler

I App Service kan du [ange appinställningar](configure-common.md#configure-app-settings) utanför din appkod. Sedan kan du komma åt dem med hjälp av [standardmönstret getenv().](https://secure.php.net/manual/function.getenv.php) Om du till exempel vill få åtkomst till en appinställning med namnet `DB_HOST` använder du följande kod:

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>Ändra platsrot

::: zone pivot="platform-windows"  

Det webbramverk du väljer kan använda en -underkatalog som platsrot. [Laravel använder till exempel](https://laravel.com/) *underkatalogen public/som* platsrot.

Om du vill anpassa platsroten anger du den virtuella programsökvägen för appen med hjälp av [`az resource update`](/cli/azure/resource#az_resource_update) kommandot . I följande exempel anges platsroten till *underkatalogen public/i* din lagringsplats. 

```azurecli-interactive
az resource update --name web --resource-group <group-name> --namespace Microsoft.Web --resource-type config --parent sites/<app-name> --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" --api-version 2015-06-01
```

Som standard Azure App Service den virtuella rotsökvägen ( ) till rotkatalogen för de distribuerade programfilerna _/_ (_sites\wwwroot_).

::: zone-end

::: zone pivot="platform-linux"

Det webbramverk du väljer kan använda en -underkatalog som platsrot. [Laravel använder till exempel](https://laravel.com/) `public/` -underkatalogen som platsrot.

PHP-standardavbildningen för App Service använder Apache, och du kan inte anpassa webbplatsroten för din app. Du kan komma runt den här begränsningen genom att lägga till *en .htaccess-fil* i lagringsplatsens rot med följande innehåll:

```
<IfModule mod_rewrite.c>
    RewriteEngine on
    RewriteCond %{REQUEST_URI} ^(.*)
    RewriteRule ^(.*)$ /public/$1 [NC,L,QSA]
</IfModule>
```

Om du inte vill använda *.htaccess*-omskrivning kan du distribuera din Laravel-app med en [anpassad Docker-avbildning](quickstart-custom-container.md) i stället.

::: zone-end

## <a name="detect-https-session"></a>Identifiera HTTPS-sessionen

I App Service sker [SSL-avslutning](https://wikipedia.org/wiki/TLS_termination_proxy) på lastbalanserare för nätverk, så alla HTTPS-begäranden når din app som okrypterade HTTP-begäranden. Om din applogik behöver kontrollera om användarbegäranden är krypterade eller inte kan du kontrollera `X-Forwarded-Proto`-rubriken.

```php
if (isset($_SERVER['HTTP_X_FORWARDED_PROTO']) && $_SERVER['HTTP_X_FORWARDED_PROTO'] === 'https') {
// Do something when HTTPS is used
}
```

Med populära ramverk får du åtkomst till `X-Forwarded-*` information i standardappens mönster. I [CodeIgniter](https://codeigniter.com/) kontrollerar [is_https()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) värdet för `X_FORWARDED_PROTO` som standard.

## <a name="customize-phpini-settings"></a>Anpassa php.ini inställningar

Om du behöver göra ändringar i PHP-installationen kan du ändra något av [php.ini-direktiv genom](https://www.php.net/manual/ini.list.php) att följa dessa steg.

> [!NOTE]
> Det bästa sättet att se PHP-versionen och den *php.ini* konfigurationen är att anropa [phpinfo()](https://php.net/manual/function.phpinfo.php) i din app.
>

### <a name="customize-non-php_ini_system-directives"></a><a name="Customize-non-PHP_INI_SYSTEM directives"></a>Anpassa icke-PHP_INI_SYSTEM direktiv

::: zone pivot="platform-windows"  

Om du PHP_INI_USER, PHP_INI_PERDIR och PHP_INI_ALL (se [php.ini-direktiv](https://www.php.net/manual/ini.list.php)) lägger du till en fil i `.user.ini` appens rotkatalog.

Lägg till konfigurationsinställningar `.user.ini` i filen med samma syntax som du använder i en `php.ini` fil. Om du till exempel vill aktivera inställningen och ange inställningen till `display_errors` `upload_max_filesize` 10M innehåller `.user.ini` filen den här texten:

```
 ; Example Settings
 display_errors=On
 upload_max_filesize=10M

 ; Write errors to d:\home\LogFiles\php_errors.log
 ; log_errors=On
```

Distribuera om appen med ändringarna och starta om den.

Som ett alternativ till att använda en fil kan du använda ini_set() i din app för att anpassa dessa `.user.ini` icke-PHP_INI_SYSTEM [](https://www.php.net/manual/function.ini-set.php) direktiv.

::: zone-end

::: zone pivot="platform-linux"

Om du PHP_INI_USER, PHP_INI_PERDIR och PHP_INI_ALL (se [php.ini-direktiv](https://www.php.net/manual/ini.list.php)) lägger du till *en .htaccess-fil* i appens rotkatalog.

I *.htaccess-filen* lägger du till -direktivet med hjälp av `php_value <directive-name> <value>` -syntaxen. Exempel:

```
php_value upload_max_filesize 1000M
php_value post_max_size 2000M
php_value memory_limit 3000M
php_value max_execution_time 180
php_value max_input_time 180
php_value display_errors On
php_value upload_max_filesize 10M
```

Distribuera om appen med ändringarna och starta om den. Om du distribuerar den med Kudu (till exempel [med Git](deploy-local-git.md)) startas den om automatiskt efter distributionen.

Som ett alternativ till *att använda .htaccess* kan [du använda ini_set()](https://www.php.net/manual/function.ini-set.php) i din app för att anpassa dessa icke-PHP_INI_SYSTEM-direktiv.

::: zone-end

### <a name="customize-php_ini_system-directives"></a><a name="customize-php_ini_system-directives"></a>Anpassa PHP_INI_SYSTEM direktiv

::: zone pivot="platform-windows"  

Om du PHP_INI_SYSTEM (se [php.ini direktiv](https://www.php.net/manual/ini.list.php)) kan du inte använda *.htaccess-metoden.* App Service tillhandahåller en separat mekanism med hjälp av `PHP_INI_SCAN_DIR` appinställningen.

Kör först följande kommando i kommandot Cloud Shell [lägga](https://shell.azure.com) till en appinställning med namnet `PHP_INI_SCAN_DIR` :

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="d:\home\site\ini"
```

Gå till Kudu-konsolen ( `https://<app-name>.scm.azurewebsites.net/DebugConsole` ) och gå till `d:\home\site` .

Skapa en katalog i med namnet och skapa sedan en `d:\home\site` `ini` *.ini-fil* i katalogen `d:\home\site\ini` (till *exempelsettings.ini)* med de direktiv som du vill anpassa. Använd samma syntax som du använder i en *php.ini* fil. 

Om du till exempel vill ändra värdet [för expose_php](https://php.net/manual/ini.core.php#ini.expose-php) kör du följande kommandon:

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

Starta om appen för att ändringarna ska börja gälla.

::: zone-end

::: zone pivot="platform-linux"

Om du PHP_INI_SYSTEM -direktiv [(sephp.ini-direktiv](https://www.php.net/manual/ini.list.php)) kan du inte använda *.htaccess-metoden.* App Service en separat mekanism med hjälp av `PHP_INI_SCAN_DIR` appinställningen.

Kör först följande kommando i kommandot Cloud Shell [lägga till](https://shell.azure.com) en appinställning med namnet `PHP_INI_SCAN_DIR` :

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

`/usr/local/etc/php/conf.d` är standardkatalogen där *php.ini* finns. `/home/site/ini`är den anpassade katalogen där du lägger till en anpassad *INI-fil.* Du separerar värdena med `:` en .

Gå till webb-SSH-sessionen med din Linux-container ( `https://<app-name>.scm.azurewebsites.net/webssh/host` ).

Skapa en katalog i med namnet och skapa sedan en `/home/site` `ini` *.ini-fil* i katalogen `/home/site/ini` (till *exempelsettings.ini)* med de direktiv som du vill anpassa. Använd samma syntax som du använder i en *php.ini* fil. 

> [!TIP]
> I de inbyggda Linux-containrarna i App Service *används /home* som bestående delad lagring. 
>

Om du till exempel vill ändra värdet [för expose_php](https://php.net/manual/ini.core.php#ini.expose-php) kör du följande kommandon:

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

Starta om appen för att ändringarna ska börja gälla.

::: zone-end

## <a name="enable-php-extensions"></a>Aktivera PHP-tillägg

::: zone pivot="platform-windows"  

De inbyggda PHP-installationerna innehåller de vanligaste tilläggen. Du kan aktivera ytterligare tillägg på samma sätt som du anpassar [php.ini direktiv](#customize-php_ini_system-directives).

> [!NOTE]
> Det bästa sättet att se PHP-versionen och den *php.ini* konfigurationen är att anropa [phpinfo()](https://php.net/manual/function.phpinfo.php) i din app.
>

Så här aktiverar du ytterligare tillägg:

Lägg till `bin` en katalog i appens rotkatalog och placera `.dll` tilläggsfilerna i den (till exempel *mongodb.dll*). Kontrollera att tilläggen är kompatibla med PHP-versionen i Azure och att de är VC9- och icke-trådsäkra (nts).

Distribuera dina ändringar.

Följ stegen i [Anpassa PHP_INI_SYSTEM direktiv](#customize-php_ini_system-directives), lägg till tilläggen i [](https://www.php.net/manual/ini.core.php#ini.extension) den anpassade *INI-filen* med tillägget [eller zend_extension-direktiv.](https://www.php.net/manual/ini.core.php#ini.zend-extension)

```
extension=d:\home\site\wwwroot\bin\mongodb.dll
zend_extension=d:\home\site\wwwroot\bin\xdebug.dll
```

Starta om appen för att ändringarna ska börja gälla.

::: zone-end

::: zone pivot="platform-linux"

De inbyggda PHP-installationerna innehåller de vanligaste tilläggen. Du kan aktivera ytterligare tillägg på samma sätt som du anpassar [php.ini direktiv](#customize-php_ini_system-directives).

> [!NOTE]
> Det bästa sättet att se PHP-versionen och den *php.ini* konfigurationen är att anropa [phpinfo()](https://php.net/manual/function.phpinfo.php) i din app.
>

Så här aktiverar du ytterligare tillägg:

Lägg till `bin` en katalog i appens rotkatalog och placera `.so` tilläggsfilerna i den (till exempel *mongodb.so*). Kontrollera att tilläggen är kompatibla med PHP-versionen i Azure och att de är VC9- och icke-trådsäkra (nts).

Distribuera dina ändringar.

Följ stegen i [Anpassa PHP_INI_SYSTEM direktiv](#customize-php_ini_system-directives), lägg till tilläggen i [](https://www.php.net/manual/ini.core.php#ini.extension) den anpassade *INI-filen* med tillägget [eller zend_extension-direktiv.](https://www.php.net/manual/ini.core.php#ini.zend-extension)

```ini
extension=/home/site/wwwroot/bin/mongodb.so
zend_extension=/home/site/wwwroot/bin/xdebug.so
```

Starta om appen för att ändringarna ska börja gälla.

::: zone-end

## <a name="access-diagnostic-logs"></a>Få åtkomst till diagnostikloggar

::: zone pivot="platform-windows"  

Använd standardverktyget [error_log()](https://php.net/manual/function.error-log.php) för att göra så att dina diagnostikloggar visas i Azure App Service.

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

::: zone-end

## <a name="troubleshooting"></a>Felsökning

När en fungerande PHP-app beter sig annorlunda i App Service har fel kan du prova följande:

- [Åtkomst till loggströmmen](#access-diagnostic-logs).
- Testa appen lokalt i produktionsläge. App Service kör din app i produktionsläge, så du måste se till att projektet fungerar som förväntat i produktionsläge lokalt. Exempel:
    - Beroende på *dincomposer.jskan* olika paket installeras för produktionsläge ( jämfört med `require` `require-dev` ).
    - Vissa webbramverk kan distribuera statiska filer på olika sätt i produktionsläge.
    - Vissa webbramverk kan använda anpassade startskript när de körs i produktionsläge.
- Kör appen i App Service i felsökningsläge. I [Laravel kan du till](https://meanjs.org/)exempel konfigurera din app för att mata ut felsökningsmeddelanden i produktion genom [att ange `APP_DEBUG` appinställningen till `true` ](configure-common.md#configure-app-settings).

::: zone pivot="platform-linux"

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: PHP-app med MySQL](tutorial-php-mysql-app.md)

::: zone pivot="platform-linux"

> [!div class="nextstepaction"]
> [Vanliga frågor och svar om App Service på Linux](faq-app-service-linux.md)

::: zone-end
