---
title: Konfigurera PHP-appar
description: Lär dig hur du konfigurerar en PHP-app i interna Windows-instanser eller i en fördefinierad PHP-behållare i Azure App Service. I artikeln visas de vanligaste konfigurationsåtgärderna.
ms.devlang: php
ms.topic: article
ms.date: 06/02/2020
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: afac8273b5729bcf5470be471145214426dc7dab
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90055307"
---
# <a name="configure-a-php-app-for-azure-app-service"></a>Konfigurera en PHP-app för Azure App Service

Den här guiden visar hur du konfigurerar dina PHP-webbappar, mobila Server delar och API-appar i Azure App Service.

Den här guiden innehåller viktiga begrepp och instruktioner för PHP-utvecklare som distribuerar appar till App Service. Om du aldrig har använt Azure App Service kan du följa självstudien för [php snabb start](quickstart-php.md) och [php med MySQL](tutorial-php-mysql-app.md) först.

## <a name="show-php-version"></a>Visa PHP-version

::: zone pivot="platform-windows"  

Om du vill visa den aktuella PHP-versionen kör du följande kommando i [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query phpVersion
```

Om du vill visa alla PHP-versioner som stöds kör du följande kommando i [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes | grep php
```

::: zone-end

::: zone pivot="platform-linux"

Om du vill visa den aktuella PHP-versionen kör du följande kommando i [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Om du vill visa alla PHP-versioner som stöds kör du följande kommando i [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

::: zone-end

## <a name="set-php-version"></a>Ange PHP-version

::: zone pivot="platform-windows"  

Kör följande kommando i [Cloud Shell](https://shell.azure.com) för att ställa in PHP-versionen på 7,4:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --php-version 7.4
```

::: zone-end

::: zone pivot="platform-linux"

Kör följande kommando i [Cloud Shell](https://shell.azure.com) för att ställa in PHP-versionen på 7,2:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "PHP|7.2"
```

::: zone-end

::: zone pivot="platform-windows"  

## <a name="run-composer"></a>Kör Composer

Om du vill App Service att köra [Composer](https://getcomposer.org/) vid distributions tillfället är det enklaste sättet att inkludera Composer i din lagrings plats.

I ett lokalt terminalfönster ändrar du katalogen till lagrings platsens rot och följer anvisningarna i [Hämta Composer](https://getcomposer.org/download/) för att ladda ned *Composer. Phar* till katalog roten.

Kör följande kommandon (du behöver [NPM](https://www.npmjs.com/get-npm) installerat):

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

Din databas rot har nu två ytterligare filer: *. distribution* och *Deploy.sh*.

Öppna *Deploy.sh* och hitta `Deployment` avsnittet som ser ut så här:

```bash
##################################################################################################################################
# Deployment
# ----------
```

Lägg till kod avsnittet du måste köra det nödvändiga verktyget *i slutet* av `Deployment` avsnittet:

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

Genomför alla ändringar och distribuera din kod med git eller zip Deploy med build Automation aktiverat. Composer ska nu köras som en del av distributions automatiseringen.

## <a name="run-gruntbowergulp"></a>Kör grunt/Bower/Gulp

Om du vill App Service köra populära automatiserings verktyg vid distributions tillfället, till exempel grunt, Bower eller Gulp, måste du ange ett [anpassat distributions skript](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script). App Service kör det här skriptet när du distribuerar med git, eller med [zip-distribution](deploy-zip.md) med build-automatisering aktiverat. 

Om du vill göra det möjligt för lagrings platsen att köra dessa verktyg måste du lägga till dem i beroenden i *package.jspå.* Exempel:

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

I ett lokalt terminalfönster ändrar du katalogen till lagrings platsens rot och kör följande kommandon (du behöver [NPM](https://www.npmjs.com/get-npm) installerat):

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

Din databas rot har nu två ytterligare filer: *. distribution* och *Deploy.sh*.

Öppna *Deploy.sh* och hitta `Deployment` avsnittet som ser ut så här:

```bash
##################################################################################################################################
# Deployment
# ----------
```

Avsnittet slutar med att köras `npm install --production` . Lägg till kod avsnittet du måste köra det nödvändiga verktyget *i slutet* av `Deployment` avsnittet:

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

Se ett [exempel i MEAN.js-exemplet](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135)där distributions skriptet också kör ett anpassat `npm install` kommando.

### <a name="bower"></a>Bower

Det här kodfragmentet körs `bower install` .

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

Det här kodfragmentet körs `gulp imagemin` .

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

Det här kodfragmentet körs `grunt` .

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

## <a name="customize-build-automation"></a>Anpassa Bygg automatisering

Om du distribuerar din app med hjälp av git-eller zip-paket med build-automatisering aktiverat, App Service bygga automatiserings steg i följande ordning:

1. Kör anpassat skript om det anges av `PRE_BUILD_SCRIPT_PATH` .
1. Kör `php composer.phar install`.
1. Kör anpassat skript om det anges av `POST_BUILD_SCRIPT_PATH` .

`PRE_BUILD_COMMAND` och `POST_BUILD_COMMAND` är miljövariabler som är tomma som standard. Definiera för att köra kommandon för att skapa för bygge `PRE_BUILD_COMMAND` . Definiera för att köra kommandon efter kompilering `POST_BUILD_COMMAND` .

I följande exempel anges de två variablerna för en serie kommandon, avgränsade med kommatecken.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Ytterligare miljövariabler för att anpassa Bygg automatisering finns i [Oryx-konfiguration](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

Mer information om hur App Service kör och skapar PHP-appar i Linux finns i [Oryx-dokumentation: så här identifieras och skapas php-appar](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/php.md).

## <a name="customize-start-up"></a>Anpassa start

Som standard kör den inbyggda PHP-behållaren apache-servern. Den körs vid start `apache2ctl -D FOREGROUND"` . Om du vill kan du köra ett annat kommando vid start genom att köra följande kommando i [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

::: zone-end

## <a name="access-environment-variables"></a>Få åtkomst till miljövariabler

I App Service kan du [ange appinställningar](configure-common.md#configure-app-settings) utanför din appkod. Sedan kan du komma åt dem med hjälp av standard mönstret för [getenv ()](https://secure.php.net/manual/function.getenv.php) . Om du till exempel vill få åtkomst till en appinställning med namnet `DB_HOST` använder du följande kod:

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>Ändra plats rot

::: zone pivot="platform-windows"  

Det webb ramverk som du väljer kan använda en under katalog som plats rot. Till exempel [Laravel](https://laravel.com/), använder den *offentliga/* under katalogen som plats roten.

Om du vill anpassa plats roten anger du den virtuella program Sök vägen för appen med hjälp av [`az resource update`](/cli/azure/resource#az-resource-update) kommandot. I följande exempel anges plats roten till den *offentliga/* under katalogen i din lagrings plats. 

```azurecli-interactive
az resource update --name web --resource-group <group-name> --namespace Microsoft.Web --resource-type config --parent sites/<app-name> --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" --api-version 2015-06-01
```

Som standard pekar Azure App Service den virtuella rot katalogen för programmet ( _/_ ) till rot katalogen för de distribuerade programfilerna (_sites\wwwroot_).

::: zone-end

::: zone pivot="platform-linux"

Det webb ramverk som du väljer kan använda en under katalog som plats rot. Till exempel, [Laravel](https://laravel.com/), använder under `public/` katalogen som plats rot.

Standard PHP-avbildningen för App Service använder Apache, och det låter dig inte anpassa plats roten för din app. Undvik den här begränsningen genom att lägga till en *. htaccess* -fil till lagrings platsens rot med följande innehåll:

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

Om du behöver göra ändringar i PHP-installationen kan du ändra något av [php.ini direktiven](https://www.php.net/manual/ini.list.php) genom att följa dessa steg.

> [!NOTE]
> Det bästa sättet att se PHP-versionen och den aktuella *php.ini* konfigurationen är att anropa [phpinfo ()](https://php.net/manual/function.phpinfo.php) i din app.
>

### <a name="customize-non-php_ini_system-directives"></a><a name="Customize-non-PHP_INI_SYSTEM directives"></a>Anpassa – icke-PHP_INI_SYSTEM direktiv

::: zone pivot="platform-windows"  

Om du vill anpassa PHP_INI_USER, PHP_INI_PERDIR och PHP_INI_ALL direktiv (se [php.ini direktiv](https://www.php.net/manual/ini.list.php)) lägger du till en `.user.ini` fil i appens rot Katalog.

Lägg till konfigurations inställningar till `.user.ini` filen med samma syntax som du skulle använda i en `php.ini` fil. Om du till exempel vill aktivera `display_errors` inställningen och ange `upload_max_filesize` inställningen till 10 miljoner `.user.ini` skulle filen innehålla den här texten:

```
 ; Example Settings
 display_errors=On
 upload_max_filesize=10M

 ; Write errors to d:\home\LogFiles\php_errors.log
 ; log_errors=On
```

Distribuera om din app med ändringarna och starta om den.

Som ett alternativ till att använda en `.user.ini` fil kan du använda [ini_set ()](https://www.php.net/manual/function.ini-set.php) i appen för att anpassa de här icke-PHP_INI_SYSTEM direktiven.

::: zone-end

::: zone pivot="platform-linux"

Om du vill anpassa PHP_INI_USER, PHP_INI_PERDIR och PHP_INI_ALL direktiv (se [php.ini direktiv](https://www.php.net/manual/ini.list.php)) lägger du till en *. htaccess* -fil i rot katalogen för din app.

I *htaccess* -filen lägger du till direktiven med hjälp av `php_value <directive-name> <value>` syntaxen. Exempel:

```
php_value upload_max_filesize 1000M
php_value post_max_size 2000M
php_value memory_limit 3000M
php_value max_execution_time 180
php_value max_input_time 180
php_value display_errors On
php_value upload_max_filesize 10M
```

Distribuera om din app med ändringarna och starta om den. Om du distribuerar den med kudu (till exempel med hjälp av [git](deploy-local-git.md)) startas den om automatiskt efter distributionen.

Som ett alternativ till att använda *. htaccess* kan du använda [ini_set ()](https://www.php.net/manual/function.ini-set.php) i appen för att anpassa de här icke-PHP_INI_SYSTEM direktiven.

::: zone-end

### <a name="customize-php_ini_system-directives"></a><a name="customize-php_ini_system-directives"></a>Anpassa PHP_INI_SYSTEM direktiv

::: zone pivot="platform-windows"  

Om du vill anpassa PHP_INI_SYSTEM direktiv (se [php.ini direktiv](https://www.php.net/manual/ini.list.php)) kan du inte använda metoden *. htaccess* . App Service ger en separat mekanism som använder `PHP_INI_SCAN_DIR` appens inställning.

Kör först följande kommando i [Cloud Shell](https://shell.azure.com) för att lägga till en app-inställning som heter `PHP_INI_SCAN_DIR` :

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="d:\home\site\ini"
```

Navigera till kudu-konsolen ( `https://<app-name>.scm.azurewebsites.net/DebugConsole` ) och navigera till `d:\home\site` .

Skapa en katalog i `d:\home\site` anropad `ini` och skapa sedan en *ini* -fil i `d:\home\site\ini` katalogen (till exempel *settings.ini)* med de direktiv som du vill anpassa. Använd samma syntax som du skulle använda i en *php.ini* -fil. 

Om du till exempel vill ändra värdet för [expose_php](https://php.net/manual/ini.core.php#ini.expose-php) kör du följande kommandon:

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

Starta om appen för att ändringarna ska börja gälla.

::: zone-end

::: zone pivot="platform-linux"

Om du vill anpassa PHP_INI_SYSTEM direktiv (se [php.ini direktiv](https://www.php.net/manual/ini.list.php)) kan du inte använda metoden *. htaccess* . App Service ger en separat mekanism som använder `PHP_INI_SCAN_DIR` appens inställning.

Kör först följande kommando i [Cloud Shell](https://shell.azure.com) för att lägga till en app-inställning som heter `PHP_INI_SCAN_DIR` :

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

`/usr/local/etc/php/conf.d` är standard katalogen där *php.ini* finns. `/home/site/ini` är den anpassade katalogen där du lägger till en anpassad *ini* -fil. Du skiljer värdena med en `:` .

Navigera till webbssh-sessionen med Linux-containern ( `https://<app-name>.scm.azurewebsites.net/webssh/host` ).

Skapa en katalog i `/home/site` anropad `ini` och skapa sedan en *ini* -fil i `/home/site/ini` katalogen (till exempel *settings.ini)* med de direktiv som du vill anpassa. Använd samma syntax som du skulle använda i en *php.ini* -fil. 

> [!TIP]
> I de inbyggda Linux-behållarna i App Service används */Home* som Sparad delad lagring. 
>

Om du till exempel vill ändra värdet för [expose_php](https://php.net/manual/ini.core.php#ini.expose-php) kör du följande kommandon:

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

Starta om appen för att ändringarna ska börja gälla.

::: zone-end

## <a name="enable-php-extensions"></a>Aktivera PHP-tillägg

::: zone pivot="platform-windows"  

De inbyggda PHP-installationerna innehåller de oftast använda tilläggen. Du kan aktivera ytterligare tillägg på samma sätt som du [anpassar php.ini direktiv](#customize-php_ini_system-directives).

> [!NOTE]
> Det bästa sättet att se PHP-versionen och den aktuella *php.ini* konfigurationen är att anropa [phpinfo ()](https://php.net/manual/function.phpinfo.php) i din app.
>

Följ dessa steg om du vill aktivera ytterligare tillägg:

Lägg till en `bin` katalog i rot katalogen för din app och Lägg till `.dll` tilläggsfiler i den (till exempel *mongodb.dll*). Kontrol lera att tilläggen är kompatibla med PHP-versionen i Azure och att de är VC9-kompatibla och icke-tråd säkra (nter)-kompatibla.

Distribuera dina ändringar.

Följ stegen i [anpassa PHP_INI_SYSTEM direktiv](#customize-php_ini_system-directives), Lägg till tilläggen i den anpassade *ini* -filen med [fil namns tillägget](https://www.php.net/manual/ini.core.php#ini.extension) eller [zend_extension](https://www.php.net/manual/ini.core.php#ini.zend-extension) -direktiven.

```
extension=d:\home\site\wwwroot\bin\mongodb.dll
zend_extension=d:\home\site\wwwroot\bin\xdebug.dll
```

Starta om appen för att ändringarna ska börja gälla.

::: zone-end

::: zone pivot="platform-linux"

De inbyggda PHP-installationerna innehåller de oftast använda tilläggen. Du kan aktivera ytterligare tillägg på samma sätt som du [anpassar php.ini direktiv](#customize-php_ini_system-directives).

> [!NOTE]
> Det bästa sättet att se PHP-versionen och den aktuella *php.ini* konfigurationen är att anropa [phpinfo ()](https://php.net/manual/function.phpinfo.php) i din app.
>

Följ dessa steg om du vill aktivera ytterligare tillägg:

Lägg till en `bin` katalog i rot katalogen för din app och Lägg till `.so` tilläggsfiler i den (till exempel *MongoDB.so*). Kontrol lera att tilläggen är kompatibla med PHP-versionen i Azure och att de är VC9-kompatibla och icke-tråd säkra (nter)-kompatibla.

Distribuera dina ändringar.

Följ stegen i [anpassa PHP_INI_SYSTEM direktiv](#customize-php_ini_system-directives), Lägg till tilläggen i den anpassade *ini* -filen med [fil namns tillägget](https://www.php.net/manual/ini.core.php#ini.extension) eller [zend_extension](https://www.php.net/manual/ini.core.php#ini.zend-extension) -direktiven.

```ini
extension=/home/site/wwwroot/bin/mongodb.so
zend_extension=/home/site/wwwroot/bin/xdebug.so
```

Starta om appen för att ändringarna ska börja gälla.

::: zone-end

## <a name="access-diagnostic-logs"></a>Få åtkomst till diagnostikloggar

::: zone pivot="platform-windows"  

Använd standard verktyget för [error_log ()](https://php.net/manual/function.error-log.php) för att göra så att dina diagnostikloggar visas i Azure App Service.

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

::: zone-end

## <a name="troubleshooting"></a>Felsökning

Prova följande när en fungerande PHP-app fungerar annorlunda i App Service eller innehåller fel:

- [Åtkomst till logg strömmen](#access-diagnostic-logs).
- Testa appen lokalt i produktions läge. App Service kör din app i produktions läge, så du måste se till att projektet fungerar som förväntat i produktions läge lokalt. Exempel:
    - Beroende på din *composer.js* kan olika paket installeras i produktions läge ( `require` vs. `require-dev` ).
    - Vissa webb ramverk kan distribuera statiska filer på ett annat sätt i produktions läge.
    - Vissa webb ramverk kan använda anpassade Start skript när de körs i produktions läge.
- Kör appen i App Service i fel söknings läge. I [Laravel](https://meanjs.org/)kan du till exempel konfigurera appen så att den utvärderar fel söknings meddelanden i produktion genom [att ställa in `APP_DEBUG` appens inställning på `true` ](configure-common.md#configure-app-settings).

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

