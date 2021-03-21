---
title: Självstudie – förbereda en webbapp för Azure Communication Services (Node.js)
titleSuffix: An Azure Communication Services tutorial
description: Lär dig hur du skapar ett webb program för bas linje som stöder Azure Communication Services
author: nmurav
services: azure-communication-services
ms.author: nmurav
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 7cfc8fc24aea938b997fead4ca762ce7178e3386
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103490627"
---
# <a name="tutorial-prepare-a-web-app-for-azure-communication-services-nodejs"></a>Självstudie: förbereda en webbapp för Azure Communication Services (Node.js)

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Du kan använda Azure Communication Services för att lägga till real tids kommunikation i dina program. I den här självstudien får du lära dig hur du konfigurerar ett webb program som stöder Azure Communication Services. Det här är en introduktion till nya utvecklare som vill komma igång med real tids kommunikation.

I slutet av den här självstudien har du ett webb program för bas linje som kon figurer ATS med klient bibliotek för Azure Communication Services. Du kan sedan använda programmet för att börja skapa din real tids kommunikations lösning.

Gå gärna till [Azure Communication Services GitHub-sidan](https://github.com/Azure/communication) för att ge feedback.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Konfigurera din utvecklings miljö.
> * Konfigurera en lokal webb server.
> * Lägg till Azure Communication Services-paketen på din webbplats.
> * Publicera din webbplats på Azures statiska webbplatser.

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. Mer information finns i [skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). Med det kostnads fria kontot får du $200 i Azure-krediter för att testa valfri kombination av tjänster.
- [Visual Studio Code](https://code.visualstudio.com/) för att redigera kod i din lokala utvecklings miljö.
- [WebPack](https://webpack.js.org/) för att paketera och lokalt vara värd för din kod.
- [Node.js](https://nodejs.org/en/) för att installera och hantera beroenden som klient bibliotek och WebPack för Azure Communication Services.
- [NVM och NPM](/windows/nodejs/setup-on-windows) för att hantera versions kontroll.
- [Azure Storage-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) för Visual Studio Code. Du behöver det här tillägget för att publicera ditt program i Azure Storage. [Läs mer om att vara värd för statiska webbplatser i Azure Storage](../../storage/blobs/storage-blob-static-website.md).
- [Azure App Service-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). Tillägget gör det möjligt att distribuera webbplatser med alternativet att konfigurera fullständigt hanterad kontinuerlig integrering och kontinuerlig leverans (CI/CD).
- [Azure Functions-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) för att bygga egna program utan server. Du kan till exempel vara värd för ditt autentiseringscertifikat i Azure Functions.
- En aktiv kommunikations tjänst resurs och anslutnings sträng. [Lär dig hur du skapar en kommunikations tjänst resurs](../quickstarts/create-communication-resource.md).
- En åtkomsttoken för användare. Instruktioner finns i snabb starten [för att skapa och hantera åtkomsttoken](../quickstarts/access-tokens.md?pivots=programming-language-javascript) eller [självstudien för att skapa en betrodd autentiseringstjänst](./trusted-service-tutorial.md).


## <a name="configure-your-development-environment"></a>Konfigurera utvecklings miljön

Din lokala utvecklings miljö kommer att konfigureras så här:

:::image type="content" source="./media/step-one-pic-one.png" alt-text="Diagram som illustrerar utvecklings miljöns arkitektur.":::


### <a name="install-nodejs-nvm-and-npm"></a>Installera Node.js, NVM och NPM

Vi använder Node.js för att hämta och installera olika beroenden som vi behöver för vårt program på klient sidan. Vi använder den för att generera statiska filer som vi sedan ska vara värd för i Azure, så du behöver inte oroa dig för att konfigurera den på servern.

Windows-utvecklare kan följa [den här Node.js själv studie kursen](/windows/nodejs/setup-on-windows) för att konfigurera Node, NVM och NPM. 

Den här självstudien baseras på LTS 12.20.0-versionen. När du har installerat NVM använder du följande PowerShell-kommando för att distribuera den version som du vill använda:

```PowerShell
nvm list available
nvm install 12.20.0
nvm use 12.20.0
```

:::image type="content" source="./media/step-one-pic-two.png" alt-text="Skärm bild som visar kommandon för att distribuera en Node-version.":::

### <a name="configure-visual-studio-code"></a>Konfigurera Visual Studio Code

Du kan hämta [Visual Studio Code](https://code.visualstudio.com/) på en av de [plattformar som stöds](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

### <a name="create-a-workspace-for-your-azure-communication-services-projects"></a>Skapa en arbets yta för Azure Communication Services-projekt

Skapa en mapp för att lagra dina projektfiler, t. ex `C:\Users\Documents\ACS\CallingApp` .:. I Visual Studio Code väljer du **Arkiv**  >  **Lägg till mapp på arbets ytan** och lägger till mappen i din arbets yta.

:::image type="content" source="./media/step-one-pic-three.png" alt-text="Skärm bild som visar markeringar för att lägga till en fil i en arbets yta.":::

Gå till **Utforskaren** i den vänstra rutan så visas din `CallingApp` mapp i den **namnlösa** arbets ytan.

:::image type="content" source="./media/step-one-pic-four.png" alt-text="Skärm bild som visar Utforskaren och den namnlösa arbets ytan.":::

Du kan uppdatera namnet på din arbets yta. Du kan verifiera din Node.js-version genom att högerklicka på `CallingApp` mappen och välja **Öppna i integrerad Terminal**.

:::image type="content" source="./media/step-one-pic-five.png" alt-text="Skärm bild som visar valet för att öppna en mapp i en integrerad Terminal.":::

I terminalen anger du följande kommando för att verifiera Node.js-versionen som är installerad i föregående steg:

```JavaScript
node --version
```

:::image type="content" source="./media/step-one-pic-six.png" alt-text="Skärm bild som visar hur du verifierar Node-versionen.":::

### <a name="install-azure-extensions-for-visual-studio-code"></a>Installera Azure-tillägg för Visual Studio Code

Installera [Azure Storage-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) antingen via Visual Studio Marketplace eller via Visual Studio Code (**Visa**  >  **tillägg**  >  **Azure Storage**).

:::image type="content" source="./media/step-one-pic-seven.png" alt-text="Skärm bild som visar knappen för att installera Azure Storage-tillägget.":::

Följ samma steg för [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) och [Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) tillägg.


## <a name="set-up-a-local-web-server"></a>Konfigurera en lokal webb server

### <a name="create-an-npm-package"></a>Skapa ett NPM-paket

I terminalen, från sökvägen till arbets ytans mapp, anger du:

``` console
npm init -y
```

Det här kommandot initierar ett nytt NPM-paket och lägger till `package.json` i rotmappen i projektet.

:::image type="content" source="./media/step-one-pic-eight.png" alt-text="Skärm bild som visar paketet J S O N.":::

Mer dokumentation om finns på `npm init` [sidan med NPM-dokument för det kommandot](https://docs.npmjs.com/cli/v6/commands/npm-init).

### <a name="install-webpack"></a>Installera WebPack

Du kan använda [WebPack](https://webpack.js.org/) för att paketera kod i statiska filer som du kan distribuera till Azure. Den har också en utvecklings server som du konfigurerar för användning med det anropande exemplet.

I terminalen anger du följande kommando för att installera WebPack:

``` Console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```

Den här självstudien har testats med de versioner som anges i föregående kommando. Att ange talar om för `-dev` paket hanteraren att detta beroende är i utvecklings syfte och bör inte tas med i koden som du distribuerar till Azure.

Du ser två nya paket som har lagts till i `package.json` filen som `devDependencies` . Paketen kommer att installeras i- `./CallingApp/node_modules/` katalogen.

:::image type="content" source="./media/step-one-pic-ten.png" alt-text="Skärm bild som visar WebPack-konfigurationen.":::

### <a name="configure-the-development-server"></a>Konfigurera utvecklings servern

Att köra ett statiskt program (t. ex. `index.html` filen) från webbläsaren använder `file://` protokollet. För att dina NPM-moduler ska fungera korrekt behöver du HTTP-protokollet genom att använda WebPack som lokal utvecklings Server.

Du skapar två konfigurationer: en för utveckling och den andra för produktion. Filer som förbereds för produktion blir minified, vilket innebär att du tar bort oanvända blank steg och tecken. Den här konfigurationen är lämplig för produktions scenarier där latens måste vara minimerat eller där kod ska vara fördunklade.

Du använder `webpack-merge` verktyget för att arbeta med [olika konfigurationsfiler för WebPack](https://webpack.js.org/guides/production/).

Vi börjar med utvecklings miljön. Först måste du installera `webpack merge` . Kör följande kommando i terminalen:

```Console
npm install --save-dev webpack-merge
```

I `package.json` filen kan du se ett mer beroende tillagt i `devDependencies` .

Skapa sedan en fil med namnet `webpack.common.js` och Lägg till följande kod:

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

Lägg sedan till ytterligare två filer, en för varje konfiguration:

* `webpack.dev.js`
* `webpack.prod.js`

Ändra nu `webpack.dev.js` filen genom att lägga till följande kod i den:

```JavaScript
const { merge } = require('webpack-merge');
const common = require('./webpack.common.js');

module.exports = merge(common, {
    mode: 'development',
    devtool: 'inline-source-map',
});
```
I den här konfigurationen importerar du vanliga parametrar från `webpack.common.js` , sammanfogar de två filerna, ställer in läget till `development` och konfigurerar käll kartan som `inline-source-map` .

I utvecklings läget anges att WebPack inte ska minimera filerna och inte producera optimerade produktions filer. Du hittar detaljerad dokumentation om WebPack-lägen på [webb sidan för WebPack-läge](https://webpack.js.org/configuration/mode/).

Käll mappnings alternativ visas på [webb sidan WebPack-DevTool](https://webpack.js.org/configuration/devtool/#root). Genom att ange käll kartan blir det enklare att felsöka i webbläsaren.

:::image type="content" source="./media/step-one-pic-11.png" alt-text="Skärm bild som visar koden för att konfigurera WebPack.":::

Om du vill köra utvecklings servern går du till `package.json` och lägger till följande kod under `scripts` :

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

:::image type="content" source="./media/step-one-pic-12.png" alt-text="Skärm bild som visar ändringen av package.jspå.":::

### <a name="test-the-development-server"></a>Testa utvecklings servern

 Skapa tre filer under projektet i Visual Studio Code:

* `index.html`
* `app.js`
* `app.css` (valfritt, för att formatera din app)

Klistra in den här koden i `index.html` :

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
:::image type="content" source="./media/step-one-pic-13.png" alt-text="Skärm bild som visar filen H T M L.":::

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
Glöm inte att spara! Filen som inte sparats anges med vita punkter bredvid fil namn i Utforskaren.

:::image type="content" source="./media/step-one-pic-14.png" alt-text="Skärm bild som visar App.js-filen med JavaScript-kod.":::

När du öppnar den här sidan bör du se att ditt meddelande visas med en avisering i webbläsarens konsol.

:::image type="content" source="./media/step-one-pic-15.png" alt-text="Skärm bild som visar filen app. CSS.":::

Använd följande Terminal-kommando för att testa utvecklings konfigurationen:

```Console
npm run build:dev
```

Konsolen visar var servern körs. Som standard är det `http://localhost:8080` . `build:dev`Kommandot är kommandot som du lade till i `package.json` tidigare.

:::image type="content" source="./media/step-one-pic-16.png" alt-text="Skärm bild som visar hur du startar en utvecklings Server.":::
 
Gå till adressen i webbläsaren så bör du se sidan och aviseringen som kon figurer ATS i föregående steg.
 
:::image type="content" source="./media/step-one-pic-17.png" alt-text="Skärm bild av sidan H T M L.":::
  
 
När servern körs kan du ändra koden och servern. HTML-sidan kommer att läsas in på nytt automatiskt. 

Gå sedan till `app.js` filen i Visual Studio Code och Delete `alert('Hello world alert!');` . Spara filen och kontrol lera att aviseringen försvinner från webbläsaren.

Om du vill stoppa servern kan du köra `Ctrl+C` i terminalen. Starta servern genom att ange `npm run build:dev` när som helst.

## <a name="add-the-azure-communication-services-packages"></a>Lägg till Azure Communication Services-paketen

Använd `npm install` kommandot för att installera Azure Communication Services som anropar klient bibliotek för Java Script.

```Console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```

Den här åtgärden lägger till vanliga Azure Communication Services-och anrops paket som beroenden för ditt paket. Du ser två nya paket som har lagts till i `package.json` filen. Du hittar mer information om `npm install` på [sidan NPM-dokument för det kommandot](https://docs.npmjs.com/cli/v6/commands/npm-install).

:::image type="content" source="./media/step-one-pic-nine.png" alt-text="Skärm bild som visar kod för att installera Azure Communication Services-paket.":::

De här paketen tillhandahålls av Azure Communication Services-teamet och inkluderar autentiserings-och anrops bibliotek. `--save`Kommandot signalerar att programmet är beroende av dessa paket för produktions användning och kommer att ingå i `devDependencies` `package.json` filen. När du bygger programmet för produktion inkluderas paketen i produktions koden.


## <a name="publish-your-website-to-azure-static-websites"></a>Publicera din webbplats på Azures statiska webbplatser

### <a name="create-a-configuration-for-production-deployment"></a>Skapa en konfiguration för produktions distribution

Lägg till följande kod i `webpack.prod.js`:

```JavaScript
const { merge } = require('webpack-merge');
const common = require('./webpack.common.js');

module.exports = merge(common, {
  mode: 'production',
});
```

Den här konfigurationen kommer att slås samman med `webpack.common.js` (där du angav indatafilen och var du vill lagra resultatet). Konfigurationen kommer även att ställa in läget på `production` .
 
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
    "@azure/communication-calling": "^1.0.0-beta.6",
    "@azure/communication-common": "^1.0.0"
  },
  "devDependencies": {
    "webpack": "^4.42.0",
    "webpack-cli": "^3.3.11",
    "webpack-dev-server": "^3.10.3",
    "webpack-merge": "^5.7.3"
  }
}
```

:::image type="content" source="./media/step-one-pic-20.png" alt-text="Skärm bild som visar konfigurerade filer.":::


I terminalen kör du:

```Console
npm run build:prod
```

Kommandot skapar en `dist` mapp och en `app.js` statisk fil för produktion som är klar. 

:::image type="content" source="./media/step-one-pic-21.png" alt-text="Skärm bild som visar produktions versionen.":::
 
 
### <a name="deploy-your-app-to-azure-storage"></a>Distribuera din app till Azure Storage

Kopiera `index.html` och `app.css` till `dist` mappen.

I `dist` mappen skapar du en fil och namnger den `404.html` . Kopiera följande markeringar till filen:

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

Högerklicka på `dist` mappen och välj **distribuera till statisk webbplats via Azure Storage**.

:::image type="content" source="./media/step-one-pic-22.png" alt-text="Skärm bild av de val som börjar distribueras till Azure.":::
 
Under **Välj prenumeration** väljer **du logga in på Azure** (eller så **skapar du ett kostnads fritt Azure-konto** om du inte har skapat någon prenumeration tidigare).
 
:::image type="content" source="./media/step-one-pic-23.png" alt-text="Skärm bild som visar val för att logga in på Azure.":::
 
Välj **Skapa nytt lagrings konto**  >  **Avancerat**.

:::image type="content" source="./media/step-one-pic-24.png" alt-text="Skärm bild som visar val för att skapa lagrings konto gruppen.":::
 
Ange namnet på lagrings gruppen.
 
:::image type="content" source="./media/step-one-pic-25.png" alt-text="Skärm bild som visar hur du lägger till ett namn för kontot.":::
 
Skapa en ny resurs grupp om det behövs.
 
:::image type="content" source="./media/step-one-pic-26.png" alt-text="Skärm bild som visar valet för att skapa en ny resurs grupp.":::
  
För **vill du aktivera statisk webbplats värd? väljer du** **Ja**.

:::image type="content" source="./media/step-one-pic-27.png" alt-text="Skärm bild som visar hur du väljer alternativet för att aktivera statisk webbplats värd.":::
  
För att **Ange index dokument namnet** accepterar du standard fil namnet. Du har redan skapat filen `index.html` .

Ange **404.html** för **att ange sökvägen till fel dokumentet för 404**.  
  
Välj platsen för programmet. Den plats du väljer definierar vilken Media processor som ska användas i ditt framtida anropande program i grupp anrop. 

Azure Communication Services väljer medie processor utifrån program platsen.

:::image type="content" source="./media/step-one-pic-28.png" alt-text="Skärm bild som visar en lista över platser.":::
  
Vänta tills resursen och webbplatsen har skapats. 
 
Välj **Bläddra till webbplatsen**.

:::image type="content" source="./media/step-one-pic-29.png" alt-text="Skärm bild som visar ett meddelande om att distributionen är klar, med knappen för att bläddra till en webbplats.":::
 
Från webbläsarens utvecklingsverktyg kan du kontrol lera källan och se filen som är för beredd för produktion.
 
:::image type="content" source="./media/step-one-pic-30.png" alt-text="Skärm bild av webbplats källan med fil.":::

Gå till [Azure Portal](https://portal.azure.com/#home), välj din resurs grupp och välj det program som du har skapat. Välj sedan **Inställningar**  >  **statisk webbplats**. Du kan se att statiska webbplatser är aktiverade. Observera den primära slut punkten, index dokumentets namn och sökvägen till fel dokumentet.

:::image type="content" source="./media/step-one-pic-31.png" alt-text="Skärm bild som visar val av statisk webbplats.":::

Välj **containrar** under **Blob Service**. I listan visas två behållare som skapats, en för loggar ( `$logs` ) och en för innehållet på din webbplats ( `$web` ).

:::image type="content" source="./media/step-one-pic-32.png" alt-text="Skärm bild som visar container konfigurationen.":::

Om du öppnar `$web` behållaren ser du de filer som du skapade i Visual Studio och distribuerade till Azure. 

:::image type="content" source="./media/step-one-pic-33.png" alt-text="Skärm bild som visar filer som distribuerats till Azure.":::

Du kan när som helst distribuera om programmet från Visual Studio Code.

Nu är du redo att bygga ditt första Azure Communication Services-webbprogram.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lägga till röstsamtal i din app](../quickstarts/voice-video-calling/getting-started-with-calling.md)

Du kanske också vill:

- [Lägga till chatt i din app](../quickstarts/chat/get-started.md)
- [Skapa åtkomsttoken för användare](../quickstarts/access-tokens.md)
- [Lär dig mer om klient-och server arkitektur](../concepts/client-and-server-architecture.md)
- [Läs mer om autentisering](../concepts/authentication.md)
