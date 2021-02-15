---
title: Självstudie – förbereda en webbapp för Azure Communication Services (Node.js)
titleSuffix: An Azure Communication Services tutorial
description: Lär dig hur du skapar ett webb program för bas linje som stöder Azure Communication Services
author: nmurav
services: azure-communication-services
ms.author: nmurav
ms.date: 01/03/2012
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: db59a9e7693190582736b9460658f629f4f1e555
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100369636"
---
# <a name="tutorial-prepare-a-web-app-for-azure-communication-services-nodejs"></a>Självstudie: förbereda en webbapp för Azure Communication Services (Node.js)

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Med Azure Communication Services kan du lägga till real tids kommunikation i dina program. I den här självstudien får du lära dig hur du konfigurerar ett webb program som stöder Azure Communication Services. Det här är en introduktions kurs som är avsedd för nya utvecklare som vill komma igång med real tids kommunikation.

I slutet av den här självstudien har du ett webb program för bas linje som kon figurer ATS med klient bibliotek för Azure Communication Services som du kan använda för att börja skapa din kommunikations lösning i real tid.

Gå gärna till [Azure Communication Services GitHub](https://github.com/Azure/communication) -sidan för att ge feedback.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Konfigurera utvecklings miljön
> * Konfigurera en lokal webserver
> * Lägg till Azure Communication Services-paketen på din webbplats
> * Publicera din webbplats på Azures statiska webbplatser

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. Mer information finns i [skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). Observera att det kostnads fria kontot ger dig $200 i Azure-krediter för att testa valfri kombination av tjänster.
- [Visual Studio Code](https://code.visualstudio.com/): vi använder detta för att redigera kod i din lokala utvecklings miljö.
- [WebPack](https://webpack.js.org/): Detta används för att paketera och lokalt vara värd för din kod.
- [Node.js](https://nodejs.org/en/): Detta används för att installera och hantera beroenden som klient bibliotek och WebPack för Azure Communication Services.
- [NVM och NPM](https://docs.microsoft.com/windows/nodejs/setup-on-windows) för att hantera versions kontroll.
- [Azure Storage-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) för Visual Studio Code. Det här tillägget krävs för att publicera ditt program i Azure Storage. [Läs mer om att vara värd för statiska webbplatser i Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website)
- [Azure App Service-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). Tillägget gör det möjligt att distribuera webbplatser (liknar föregående), men med alternativet att konfigurera den fullständigt hanterade kontinuerliga integrationen och den kontinuerliga leveransen (CI/CD).
- [Azure Function-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) för att bygga egna program utan server. Du kan till exempel vara värd för ditt autentiseringscertifikat i Azure Functions.
- En aktiv kommunikations tjänst resurs och anslutnings sträng. [Skapa en kommunikations tjänst resurs](../quickstarts/create-communication-resource.md).
- En åtkomsttoken för användare. Anvisningar finns i [själv studie kursen](https://docs.microsoft.com/azure/communication-services/tutorials/trusted-service-tutorial) om [åtkomst-token](https://docs.microsoft.com/azure/communication-services/quickstarts/access-tokens?pivots=programming-language-javascript) eller den betrodda tjänsten.


## <a name="configure-your-development-environment"></a>Konfigurera utvecklings miljön

Din lokala utvecklings miljö kommer att konfigureras så här:

:::image type="content" source="./media/step-one-pic-one.png" alt-text="Arkitektur för utvecklings miljö":::


### <a name="install-nodejs-nvm-and-npm"></a>Installera Node.js, NVM och NPM

Vi använder Node.js för att hämta och installera olika beroenden som vi behöver för vårt program på klient sidan. Vi använder den för att generera statiska filer som vi sedan ska vara värd för i Azure, så du behöver inte oroa dig för att konfigurera den på servern.

Windows-utvecklare kan följa [den här NodeJS-självstudien](https://docs.microsoft.com/windows/nodejs/setup-on-windows) för att konfigurera Node, NVM och NPM. 

Vi har testat den här självstudien med LTS 12.20.0-versionen. När du har installerat NVM använder du följande PowerShell-kommando för att distribuera den version som du vill använda:

```PowerShell
nvm list available
nvm install 12.20.0
nvm use 12.20.0
```

:::image type="content" source="./media/step-one-pic-two.png" alt-text="Arbeta med NVM för att distribuera Node.js":::

### <a name="configure-visual-studio-code"></a>Konfigurera Visual Studio Code

Du kan hämta [Visual Studio Code](https://code.visualstudio.com/) på en av de [plattformar som stöds](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

### <a name="create-a-workspace-for-your-azure-communication-services-projects"></a>Skapa en arbets yta för Azure Communication Services-projekt

Skapa en ny mapp för att lagra dina projektfiler, t. ex `C:\Users\Documents\ACS\CallingApp` .:. I Visual Studio Code klickar du på "Arkiv", "Lägg till mapp i arbets ytan" och lägger till mappen i din arbets yta.

:::image type="content" source="./media/step-one-pic-three.png" alt-text="Skapar ny arbets plats":::

Gå till "Explorer" i Visual Studio Code i den vänstra rutan och se mappen "CallingApp" i arbets ytan "namnlös".

:::image type="content" source="./media/step-one-pic-four.png" alt-text="Utforskarvyn":::

Du kan uppdatera namnet på din arbets yta. Du kan verifiera din Node.js-version genom att högerklicka på mappen "CallingApp" och välja "öppna i integrerad Terminal".

:::image type="content" source="./media/step-one-pic-five.png" alt-text="Öppna en Terminal":::

I terminalen skriver du följande kommando för att verifiera node.js-versionen som är installerad i föregående steg:

```JavaScript
node --version
```

:::image type="content" source="./media/step-one-pic-six.png" alt-text="Verifierar Node.js version":::

### <a name="install-azure-extensions-for-visual-studio-code"></a>Installera Azure-tillägg för Visual Studio Code

Installera [Azure Storage-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) antingen via Visual Studio Marketplace eller med Visual Studio Code (Visa > tillägg > Azure Storage).

:::image type="content" source="./media/step-one-pic-seven.png" alt-text="Installerar Azure Storage tillägg 1":::

Följ samma steg för [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) och [Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) tillägg.


## <a name="set-up-a-local-webserver"></a>Konfigurera en lokal webserver

### <a name="create-a-new-npm-package"></a>Skapa ett nytt NPM-paket

I terminalen, från sökvägen till din arbets yta, skriver du:

``` console
npm init -y
```

Det här kommandot initierar ett nytt NPM-paket och lägger till `package.json` i rotmappen i projektet.

:::image type="content" source="./media/step-one-pic-eight.png" alt-text="Paket-JSON":::

Ytterligare dokumentation om NPM init-kommandot hittar du [här](https://docs.npmjs.com/cli/v6/commands/npm-init)

### <a name="install-webpack"></a>Installera WebPack

med [WebPack](https://webpack.js.org/) kan du paketera kod i statiska filer som du kan distribuera till Azure. Den har också en utvecklings server som vi ska konfigurera för att använda med det anropande exemplet.

I terminalen skriver du följande för att installera WebPack:

``` Console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```

Den här självstudien har testats med ovanstående angivna versioner. Att ange talar om för `-dev` paket hanteraren att detta beroende är i utvecklings syfte och bör inte tas med i koden som vi distribuerar till Azure.

Du ser två nya paket som har lagts till i `package.json` filen som "devDependencies". Paketen kommer att installeras i `./CallingApp/node_modules/` katalogen.

:::image type="content" source="./media/step-one-pic-ten.png" alt-text="konfiguration av WebPack":::

### <a name="configure-the-development-server"></a>Konfigurera utvecklings servern

Att köra ett statiskt program (t. ex. `index.html` filen) från webbläsaren använder `file://` protokollet. För att dina NPM-moduler ska fungera korrekt behöver vi HTTP-protokollet genom att använda WebPack som lokal utvecklings Server.

Vi ska skapa två konfigurationer: en för utveckling och den andra för produktion. Filer som förbereds för produktion blir minified, vilket innebär att vi tar bort oanvända blank steg och tecken. Detta är lämpligt för produktions scenarier där svars tiden bör minimeras eller där kod ska vara fördunklade.

Vi använder `webpack-merge` verktyget för att arbeta med [olika konfigurationsfiler för WebPack](https://webpack.js.org/guides/production/)

Vi börjar med utvecklings miljön. Först måste vi installera `webpack merge` . Kör följande i terminalen:

```Console
npm install --save-dev webpack-merge
```

I `package.json` filen kan du se ett mer beroende tillagt i "devDependencies".

I nästa steg måste vi skapa en ny fil `webpack.common.js` och lägga till följande kod:

```JavaScript
const path = require('path');
module.exports ={
    entry: './app.js',
    output: {
        filename:'app.js',
        path: path.resolve(__dirname, 'dist'),
    }     
}
```

Vi lägger sedan till ytterligare två filer, en för varje konfiguration:

* webpack.dev.js
* webpack.prod.js

I nästa steg måste vi ändra `webpack.dev.js` filen. Lägg till följande kod i filen:

```JavaScript
const { merge } = require('webpack-merge');
const common = require('./webpack.common.js');

module.exports = merge(common, {
    mode: 'development',
    devtool: 'inline-source-map',
});
```
I den här konfigurationen importerar vi vanliga parametrar från `webpack.common.js` , sammanfogar de två filerna, ställer in läget på "utveckling" och konfigurerar SourceMap som "inline-Source-Map".

I utvecklings läget anges att WebPack inte ska minimera filerna och inte producera optimerade produktions filer. Detaljerad dokumentation om WebPack-lägen hittar du [här](https://webpack.js.org/configuration/mode/).

Käll mappnings alternativ visas [här](https://webpack.js.org/configuration/devtool/#root). Genom att ange käll kartan blir det enklare att felsöka i webbläsaren.

:::image type="content" source="./media/step-one-pic-11.png" alt-text="Konfigurerar WebPack":::

Om du vill köra utvecklings servern går du till `package.json` och lägger till följande kod under skript:

```JavaScript
    "build:dev": "webpack-dev-server --config webpack.dev.js"
```

Filen bör nu se ut så här:

```JavaScript
{
  "name": "CallingApp",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build:dev": "webpack-dev-server --config webpack.dev.js"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "webpack": "^4.42.0",
    "webpack-cli": "^3.3.11",
    "webpack-dev-server": "^3.10.3"
  }
}
```

Du har lagt till kommandot som kan användas från NPM. 

:::image type="content" source="./media/step-one-pic-12.png" alt-text="Ändra package.jspå":::

### <a name="testing-the-development-server"></a>Testa utvecklings servern

 Skapa tre filer under projektet i Visual Studio Code:

* `index.html`
* `app.js`
* `app.css` (valfritt, det gör att du kan formatera din app)

Klistra in det i `index.html` :

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My first ACS application</title>
    <link rel="stylesheet" href="./app.css"/>
    <script src="./app.js" defer></script>
</head>
<body>
    <h1>Hello from ACS!</h1>
</body>
</html>
```
:::image type="content" source="./media/step-one-pic-13.png" alt-text="HTML-fil":::

Lägg till följande kod i `app.js`:

```JavaScript
alert('Hello world alert!');
console.log('Hello world console!');
```
Lägg till följande kod i `app.css`:

```CSS
html {
    font-family: sans-serif;
  }
```

 :::image type="content" source="./media/step-one-pic-14.png" alt-text="App.js fil med JS-kod":::

När du öppnar den här sidan bör du se meddelandet som visas med en avisering och i webbläsarens konsol.

:::image type="content" source="./media/step-one-pic-15.png" alt-text="App. CSS-fil":::

Använd följande Terminal-kommando för att testa utvecklings konfigurationen:

```Console
npm run build:dev
```

I-konsolen visas var servern körs. Som standard är det `http://localhost:8080` . Kommandot build: dev är kommandot som vi lade till `package.json` tidigare.

 :::image type="content" source="./media/step-one-pic-16.png" alt-text="Starta en utvecklings Server":::
 
 Navigera till adressen i webbläsaren så bör du se sidan och aviseringen som kon figurer ATS i föregående steg.
 
  :::image type="content" source="./media/step-one-pic-17.png" alt-text="HTML-sida":::
  
 
När servern körs kan du ändra koden, så kommer servern och HTML-sidan att läsas in automatiskt på nytt. 

Gå sedan till `app.js` filen i Visual Studio Code och Delete `alert('Hello world alert!');` . Spara filen och kontrol lera att aviseringen försvinner från webbläsaren.

Om du vill stoppa servern kan du köra `Ctrl+C` i terminalen. Starta servern genom att skriva `npm run build:dev` när som helst.

## <a name="add-the-azure-communication-services-packages"></a>Lägg till Azure Communication Services-paketen

Använd `npm install` kommandot för att installera Azure Communication Services som anropar klient bibliotek för Java Script.

```Console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```

Den här åtgärden lägger till vanliga och anropande paket för Azure Communication Services som beroenden för ditt paket. Du ser två nya paket som har lagts till i `package.json` filen. Mer information om kommandot hittar du `npm install` [här](https://docs.npmjs.com/cli/v6/commands/npm-install).

:::image type="content" source="./media/step-one-pic-nine.png" alt-text="Installera Azure Communication Services-paket":::

De här paketen tillhandahålls av Azure Communication Services-teamet och inkluderar autentiserings-och anrops bibliotek. Kommandot "--Save" signalerar att vårt program är beroende av dessa paket för produktions användning och kommer att ingå i `dependencies` vår `package.json` fil. När vi bygger programmet för produktion ingår paketen i produktions koden.


## <a name="publish-your-website-to-azure-static-websites"></a>Publicera din webbplats på Azures statiska webbplatser

### <a name="create-a-configuration-for-production-deployment"></a>Skapa en konfiguration för produktions distribution

Lägg till följande kod i `webpack.prod.js` :

```JavaScript
const { merge } = require('webpack-merge');
 const common = require('./webpack.common.js');

 module.exports = merge(common, {
   mode: 'production',
 });
 ```

OBS! den här konfigurationen kommer att slås samman med webpack.common.js (där vi angav indatafilen och var resultatet ska lagras) och anger läget till "produktion".
 
I `package.json` lägger du till följande kod:

```JavaScript
"build:prod": "webpack --config webpack.prod.js" 
```

Filen bör se ut så här:

```JavaScript
{
  "name": "CallingApp",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build:dev": "webpack-dev-server --config webpack.dev.js",
    "build:prod": "webpack --config webpack.prod.js" 
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "@azure/communication-calling": "^1.0.0-beta.3",
    "@azure/communication-common": "^1.0.0-beta.3"
  },
  "devDependencies": {
    "webpack": "^4.42.0",
    "webpack-cli": "^3.3.11",
    "webpack-dev-server": "^3.10.3",
    "webpack-merge": "^5.7.3"
  }
}
```

 :::image type="content" source="./media/step-one-pic-20.png" alt-text="Konfigurerade filer":::


I terminalfönstret kör du:

```Console
npm run build:prod
```

Kommandot skapar en `dist` statisk fil för en mapp och en produktions miljö som är klar `app.js` . 

 :::image type="content" source="./media/step-one-pic-21.png" alt-text="Produktions version":::
 
 
### <a name="deploy-your-app-to-azure-storage"></a>Distribuera din app till Azure Storage
 
Kopiera `index.html` och `app.css` till `dist` mappen.

`dist`Skapa en ny fil i mappen och ge den namnet `404.html` . Kopiera följande markeringar till filen:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="./app.css"/>
    <title>Document</title>
</head>
<body>
    <h1>The page does not exists.</h1>
</body>
</html>
```

Spara filen (Ctrl + S).

Högerklicka och välj Distribuera till statisk webbplats via Azure Storage.

:::image type="content" source="./media/step-one-pic-22.png" alt-text="Börja distribuera till Azure":::
 
I `Select subscription` fältet väljer du "logga in på Azure (eller" skapa ett kostnads fritt Azure-konto "om du inte har skapat någon prenumeration tidigare)
 
:::image type="content" source="./media/step-one-pic-23.png" alt-text="Logga in på Azure":::
 
Välj `Create new Storage Account`  >  `Advanced` :

 :::image type="content" source="./media/step-one-pic-24.png" alt-text="Skapar lagrings konto gruppen":::
 
 Ange namnet på lagrings gruppen:
 
 :::image type="content" source="./media/step-one-pic-25.png" alt-text="Lägga till ett namn för kontot":::
 
Skapa en ny resurs grupp om det behövs:
 
  :::image type="content" source="./media/step-one-pic-26.png" alt-text="Skapar ny grupp":::
  
  Besvara "Ja" om du vill aktivera statisk webbplats värd? "
  
  :::image type="content" source="./media/step-one-pic-27.png" alt-text="Välja alternativ för att aktivera statisk webbplats värd":::
  
Godkänn standard fil namnet i "Ange index dokument namnet" som vi skapade filen `index.html` .

Skriv `404.html` "Ange sökvägen till fel dokumentet för 404".  
  
Välj platsen för programmet. Den plats du väljer definierar vilken Media processor som ska användas i ditt framtida anropande program i grupp anrop. 

Azure Communication Services väljer medie processor utifrån program platsen.

:::image type="content" source="./media/step-one-pic-28.png" alt-text="Välj plats":::
  
Vänta tills resursen och webbplatsen har skapats. 
 
Klicka på Bläddra till webbplats:

:::image type="content" source="./media/step-one-pic-29.png" alt-text="Distributionen har slutförts":::
 
Från webbläsarens utvecklingsverktyg kan du kontrol lera källan och se vår fil, för beredd för produktion.
 
:::image type="content" source="./media/step-one-pic-30.png" alt-text="Webbplatsen":::

Gå till [Azure Portal](https://portal.azure.com/#home), välj din resurs grupp, Välj det program som du skapade och gå till `Settings`  >  `Static website` . Du kan se att statiska webbplatser är aktiverade och noterar den primära slut punkten, index dokumentet och dokument filen med fel Sök vägar.

:::image type="content" source="./media/step-one-pic-31.png" alt-text="Val av statisk webbplats":::

Under "Blob Service" väljer du "behållare" och du ser två behållare som skapats, en för loggar ($logs) och innehållet på din webbplats ($web)

:::image type="content" source="./media/step-one-pic-32.png" alt-text="Container konfiguration":::

Om du går till `$web` ser du dina filer som du skapade i Visual Studio och distribuerade till Azure. 

:::image type="content" source="./media/step-one-pic-33.png" alt-text="Distribution":::

Du kan när som helst distribuera om programmet från Visual Studio Code.

Nu är du redo att bygga ditt första Azure Communication Services-webbprogram.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lägga till röstsamtal i din app](../quickstarts/voice-video-calling/getting-started-with-calling.md)

Du kanske också vill:

- [Lägga till chatt i din app](../quickstarts/chat/get-started.md)
- [Skapa token för användar åtkomst](../quickstarts/access-tokens.md)
- [Lär dig mer om klient-och server arkitektur](../concepts/client-and-server-architecture.md)
- [Läs mer om autentisering](../concepts/authentication.md)
