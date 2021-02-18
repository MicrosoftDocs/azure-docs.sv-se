---
title: Konfigurera Node.js appar
description: Lär dig hur du konfigurerar en Node.js-app i de interna Windows-instanserna eller i en fördefinierad Linux-behållare i Azure App Service. I artikeln visas de vanligaste konfigurationsåtgärderna.
ms.custom: devx-track-js, devx-track-azurecli
ms.devlang: nodejs
ms.topic: article
ms.date: 06/02/2020
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 6a6f782768db12c2ce75f5cf1e66100222f24446
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "101095211"
---
# <a name="configure-a-nodejs-app-for-azure-app-service"></a>Konfigurera en Node.js app för Azure App Service

Node.js appar måste distribueras med alla nödvändiga NPM-beroenden. App Service distributions motorn körs automatiskt `npm install --production` åt dig när du distribuerar en [git-lagringsplats](deploy-local-git.md)eller ett [zip-paket](deploy-zip.md) med build-automatisering aktiverat. Om du distribuerar dina filer med [FTP/S](deploy-ftp.md)måste du dock ladda upp de nödvändiga paketen manuellt.

Den här guiden innehåller viktiga begrepp och instruktioner för Node.js utvecklare som distribuerar till App Service. Om du aldrig har använt Azure App Service följer du själv studie kursen [Node.js snabb start](quickstart-nodejs.md) och [Node.js med MongoDB](tutorial-nodejs-mongodb-app.md) .

## <a name="show-nodejs-version"></a>Visa Node.js version

::: zone pivot="platform-windows"  

Om du vill visa den aktuella Node.js versionen kör du följande kommando i [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings list --name <app-name> --resource-group <resource-group-name> --query "[?name=='WEBSITE_NODE_DEFAULT_VERSION'].value"
```

Om du vill visa alla Node.js-versioner som stöds kör du följande kommando i [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes | grep node
```

::: zone-end

::: zone pivot="platform-linux"

Om du vill visa den aktuella Node.js versionen kör du följande kommando i [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Om du vill visa alla Node.js-versioner som stöds kör du följande kommando i [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep NODE
```

::: zone-end

## <a name="set-nodejs-version"></a>Ange Node.js version

::: zone pivot="platform-windows"  

Om du vill ställa in din app på en [Node.js-version som stöds](#show-nodejs-version)kör du följande kommando i [Cloud Shell](https://shell.azure.com) för att ange `WEBSITE_NODE_DEFAULT_VERSION` en version som stöds:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_NODE_DEFAULT_VERSION="10.15"
```

Den här inställningen anger vilken Node.js-version som ska användas, både vid körning och under automatisk återställning av paketet under App Service skapa automatisering.

> [!NOTE]
> Du bör ange Node.js versionen i projektets `package.json` . Distributions motorn körs i en separat process som innehåller alla Node.js-versioner som stöds.

::: zone-end

::: zone pivot="platform-linux"

Om du vill ställa in appen till en [Node.js-version som stöds](#show-nodejs-version)kör du följande kommando i [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "NODE|10.14"
```

Den här inställningen anger vilken Node.js-version som ska användas, både vid körning och vid automatisk återställning av paketet i kudu.

> [!NOTE]
> Du bör ange Node.js versionen i projektets `package.json` . Distributions motorn körs i en separat behållare som innehåller alla Node.js-versioner som stöds.

::: zone-end

## <a name="get-port-number"></a>Hämta port nummer

Du Node.js appen måste lyssna på rätt port för att ta emot inkommande begär Anden.

::: zone pivot="platform-windows"  

I App Service på Windows finns Node.js appar med [IISNode](https://github.com/Azure/iisnode)och din Node.js app bör lyssna på den port som anges i `process.env.PORT` variabeln. I följande exempel visas hur du gör det i en enkel Express-app:

::: zone-end

::: zone pivot="platform-linux"  

App Service anger miljövariabeln `PORT` i Node.js containern och vidarebefordrar inkommande begär anden till din behållare på port numret. För att ta emot begär Anden ska din app Lyssna på den porten med hjälp av `process.env.PORT` . I följande exempel visas hur du gör det i en enkel Express-app:

::: zone-end

```javascript
const express = require('express')
const app = express()
const port = process.env.PORT || 3000

app.get('/', (req, res) => {
  res.send('Hello World!')
})

app.listen(port, () => {
  console.log(`Example app listening at http://localhost:${port}`)
})
```

::: zone pivot="platform-linux"

## <a name="customize-build-automation"></a>Anpassa Bygg automatisering

Om du distribuerar din app med hjälp av git-eller zip-paket med build-automatisering aktiverat, App Service bygga automatiserings steg i följande ordning:

1. Kör anpassat skript om det anges av `PRE_BUILD_SCRIPT_PATH` .
1. Kör `npm install` utan några flaggor, som innehåller NPM `preinstall` och `postinstall` skript och som också installeras `devDependencies` .
1. Kör `npm run build` om ett build-skript anges i *package.jspå*.
1. Kör `npm run build:azure` om en version: Azure-skript anges i *package.jspå*.
1. Kör anpassat skript om det anges av `POST_BUILD_SCRIPT_PATH` .

> [!NOTE]
> Som beskrivs i [NPM-dokument](https://docs.npmjs.com/misc/scripts), skript som heter `prebuild` och `postbuild` körs före och efter `build` , om de anges. `preinstall` och `postinstall` Kör före respektive efter `install` .

`PRE_BUILD_COMMAND` och `POST_BUILD_COMMAND` är miljövariabler som är tomma som standard. Definiera för att köra kommandon för att skapa för bygge `PRE_BUILD_COMMAND` . Definiera för att köra kommandon efter kompilering `POST_BUILD_COMMAND` .

I följande exempel anges de två variablerna för en serie kommandon, avgränsade med kommatecken.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Ytterligare miljövariabler för att anpassa Bygg automatisering finns i [Oryx-konfiguration](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

Mer information om hur App Service kör och skapar Node.js appar i Linux finns i [Oryx-dokumentation: hur Node.js appar identifieras och skapas](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/nodejs.md).

## <a name="configure-nodejs-server"></a>Konfigurera Node.js Server

Node.js behållare levereras med [PM2](https://pm2.keymetrics.io/), en produktions process hanterare. Du kan konfigurera appen så att den startar med PM2, eller med NPM, eller med ett anpassat kommando.

- [Kör anpassat kommando](#run-custom-command)
- [Kör NPM-start](#run-npm-start)
- [Kör med PM2](#run-with-pm2)

### <a name="run-custom-command"></a>Kör anpassat kommando

App Service kan starta din app med ett anpassat kommando, till exempel en körbar fil som *Run.sh*. Om du till exempel vill köra kör du `npm run start:prod` följande kommando i [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "npm run start:prod"
```

### <a name="run-npm-start"></a>Kör NPM-start

För att starta din app med `npm start` , se bara till att `start` det finns ett skript i *package.jspå* filen. Exempel:

```json
{
  ...
  "scripts": {
    "start": "gulp",
    ...
  },
  ...
}
```

Om du vill använda en anpassad *package.js* i projektet kör du följande kommando i [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filename>.json"
```

### <a name="run-with-pm2"></a>Kör med PM2

Behållaren startar automatiskt appen med PM2 när en av de vanliga Node.js-filerna finns i projektet:

- *bin/www*
- *server.js*
- *app.js*
- *index.js*
- *hostingstart.js*
- En av följande [PM2-filer](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file): *process.jspå* och *ecosystem.config.js*

Du kan också konfigurera en anpassad start fil med följande fil namns tillägg:

- En *. js* -fil
- En [PM2-fil](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file) med fil namns tillägget *. JSON*, *.config.js*, *. yaml* eller *. yml*

Om du vill lägga till en anpassad start fil kör du följande kommando i [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filname-with-extension>"
```

## <a name="debug-remotely"></a>Felsöka via en fjärranslutning

> [!NOTE]
> Fjärrfelsökning är för närvarande en för hands version.

Du kan felsöka Node.js-appen via fjärr anslutning i [Visual Studio Code](https://code.visualstudio.com/) om du konfigurerar den att [köras med PM2](#run-with-pm2), förutom när du kör den med hjälp av * .config.js, *. yml eller *. yaml*.

I de flesta fall krävs ingen extra konfiguration för din app. Om din app körs med en *process.jspå* fil (standard eller anpassad), måste den ha en `script` egenskap i JSON-roten. Exempel:

```json
{
  "name"        : "worker",
  "script"      : "./index.js",
  ...
}
```

Om du vill konfigurera Visual Studio Code för fjärrfelsökning, installerar du [App Service-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). Följ instruktionerna på sidan anknytning och logga in på Azure i Visual Studio Code.

I Azure Explorer letar du reda på den app som du vill felsöka, högerklickar på den och väljer **Starta fjärrfelsökning**. Klicka på **Ja** för att aktivera den för din app. App Service startar en tunnel-proxy för dig och kopplar fel söknings programmet. Du kan sedan göra förfrågningar till appen och se fel söknings verktyget pausas vid Bryt punkter.

När du är färdig med fel sökningen stoppar du fel sökningen genom att välja **Koppla från**. När du uppmanas bör du klicka på **Ja** om du vill inaktivera fjärrfelsökning. Om du vill inaktivera det senare högerklickar du på din app igen i Azure Explorer och väljer **inaktivera fjärrfelsökning**.

::: zone-end

## <a name="access-environment-variables"></a>Få åtkomst till miljövariabler

I App Service kan du [ange appinställningar](configure-common.md) utanför din appkod. Sedan kan du komma åt dem med standard Node.jss mönstret. Om du till exempel vill få åtkomst till en appinställning med namnet `NODE_ENV` använder du följande kod:

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>Kör grunt/Bower/Gulp

App Service Bygg automatisering körs som standard `npm install --production` när den identifierar en Node.js-app distribueras via git eller zip-distribution med funktionen för att bygga automatisering aktive rad. Om din app kräver något av de populära automatiserings verktygen, till exempel grunt, Bower eller Gulp, måste du ange ett [anpassat distributions skript](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) för att köra det.

Om du vill göra det möjligt för lagrings platsen att köra dessa verktyg måste du lägga till dem i beroenden i *package.jspå.* Exempel:

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

I ett lokalt terminalfönster ändrar du katalogen till lagrings platsens rot och kör följande kommandon:

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

## <a name="detect-https-session"></a>Identifiera HTTPS-sessionen

I App Service sker [SSL-avslutning](https://wikipedia.org/wiki/TLS_termination_proxy) på lastbalanserare för nätverk, så alla HTTPS-begäranden når din app som okrypterade HTTP-begäranden. Om din applogik behöver kontrollera om användarbegäranden är krypterade eller inte kan du kontrollera `X-Forwarded-Proto`-rubriken.

Med populära ramverk får du åtkomst till `X-Forwarded-*` information i standardappens mönster. I [Express](https://expressjs.com/)kan du använda [betrodda proxyservrar](https://expressjs.com/guide/behind-proxies.html). Exempel:

```javascript
app.set('trust proxy', 1)
...
if (req.secure) {
  // Do something when HTTPS is used
}
```

## <a name="access-diagnostic-logs"></a>Få åtkomst till diagnostikloggar

::: zone pivot="platform-windows"  

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

::: zone-end


::: zone pivot="platform-linux"

## <a name="monitor-with-application-insights"></a>Övervaka med Application Insights

Med Application Insights kan du övervaka programmets prestanda, undantag och användning utan att göra några kod ändringar. Om du vill koppla App Insights-agenten går du till webbappen i portalen och väljer **Application Insights** under **Inställningar** och väljer sedan **Aktivera Application Insights**. Välj sedan en befintlig App Insights-resurs eller skapa en ny. Välj slutligen **Använd** längst ned. Om du vill instrumentera din webbapp med hjälp av PowerShell kan du läsa [följande instruktioner](../azure-monitor/app/azure-web-apps.md?tabs=netcore#enabling-through-powershell)

Den här agenten övervakar Node.js programmet på Server sidan. Om du vill övervaka Java Script på klient sidan [lägger du till Java Script SDK i projektet](../azure-monitor/app/javascript.md). 

Mer information finns i [versions anteckningar för Application Insights Extension](../azure-monitor/app/web-app-extension-release-notes.md).

::: zone-end

## <a name="troubleshooting"></a>Felsökning

Prova följande när en fungerande Node.js-app fungerar annorlunda i App Service eller innehåller fel:

- [Åtkomst till logg strömmen](#access-diagnostic-logs).
- Testa appen lokalt i produktions läge. App Service kör dina Node.js appar i produktions läge, så du måste se till att projektet fungerar som förväntat i produktions läge lokalt. Exempel:
    - Beroende på din *package.js* kan olika paket installeras i produktions läge ( `dependencies` vs. `devDependencies` ).
    - Vissa webb ramverk kan distribuera statiska filer på ett annat sätt i produktions läge.
    - Vissa webb ramverk kan använda anpassade Start skript när de körs i produktions läge.
- Kör din app i App Service i utvecklings läge. I [MEAN.js](https://meanjs.org/)kan du till exempel ställa in appen i utvecklings läge i körningen genom [att ställa in `NODE_ENV` appens inställning](configure-common.md).

::: zone pivot="platform-linux"

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: Node.js app med MongoDB](tutorial-nodejs-mongodb-app.md)

::: zone pivot="platform-linux"

> [!div class="nextstepaction"]
> [Vanliga frågor och svar om App Service på Linux](faq-app-service-linux.md)

::: zone-end
