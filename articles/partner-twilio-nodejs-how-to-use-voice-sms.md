---
title: Använda Twilio för röst-, VoIP-och SMS-meddelanden i Azure
description: Lär dig att ringa ett telefonsamtal och skicka ett SMS-meddelande med Twilio-API-tjänsten på Azure. Kod exempel som skrivits i Node.js.
documentationcenter: nodejs
author: georgewallace
ms.assetid: f558cbbd-13d2-416f-b9b1-33a99c426af9
ms.service: multiple
ms.devlang: nodejs
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: c4b1c506ff0e78e5e304ceb1682c73f9cd784c95
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88080549"
---
# <a name="using-twilio-for-voice-voip-and-sms-messaging-in-azure"></a>Använda Twilio för röst-, VoIP-och SMS-meddelanden i Azure
Den här guiden visar hur du skapar appar som kommunicerar med Twilio och node.js på Azure.

<a name="whatis"></a>

## <a name="what-is-twilio"></a>Vad är Twilio?
Twilio är en API-plattform som gör det enkelt för utvecklare att ringa och ta emot telefonsamtal, skicka och ta emot SMS och bädda in VoIP-anrop till webbläsarbaserade och interna mobila program. Låt oss gå igenom hur det fungerar innan du simhopp i.

### <a name="receiving-calls-and-text-messages"></a>Ta emot samtal och textmeddelanden
Med Twilio kan utvecklare [köpa programmerbara telefonnummer][purchase_phone] som kan användas för att både skicka och ta emot samtal och textmeddelanden. När ett Twilio-nummer tar emot ett inkommande samtal eller en text, skickar Twilio ditt webb program till ett HTTP-inlägg eller GET-begäran, där du uppmanas att ge instruktioner om hur du hanterar anropet eller texten. Servern kommer att svara på Twilio HTTP-begäran med [TwiML][twiml], en enkel uppsättning XML-taggar som innehåller instruktioner om hur du hanterar ett samtal eller en text. Vi kommer att se exempel på TwiML på bara en stund.

### <a name="making-calls-and-sending-text-messages"></a>Göra samtal och skicka SMS
Genom att göra HTTP-förfrågningar till Twilio-webbtjänst-API: t kan utvecklare skicka SMS eller initiera utgående telefonsamtal. För utgående samtal måste utvecklaren även ange en URL som returnerar TwiML-instruktioner för hur du hanterar det utgående anropet när det är anslutet.

### <a name="embedding-voip-capabilities-in-ui-code-javascript-ios-or-android"></a>Bädda in VoIP-funktioner i UI-kod (Java Script, iOS eller Android)
Twilio tillhandahåller ett SDK för klient sidan som kan förvandla valfri desktop-webbläsare, iOS-app eller Android-app till en VoIP-telefon. I den här artikeln kommer vi att fokusera på hur du använder VoIP-samtal i webbläsaren. Förutom *Twilio Java Script SDK* som körs i webbläsaren, måste ett program på Server sidan (vårt node.js program) användas för att utfärda en "Capability token" till JavaScript-klienten. Du kan läsa mer om att använda VoIP med node.js [på Twilio dev-bloggen][voipnode].

<a name="signup"></a>

## <a name="sign-up-for-twilio-microsoft-discount"></a>Registrera dig för Twilio (Microsoft Discount)
Innan du använder Twilio Services måste du först [Registrera dig för ett konto][signup]. Microsoft Azure kunder får en särskild rabatt – [var noga med att registrera dig här][signup]!

<a name="azuresite"></a>

## <a name="create-and-deploy-a-nodejs-azure-website"></a>Skapa och distribuera en node.js Azure-webbplats
Därefter måste du skapa en node.js webbplats som körs på Azure. [Den officiella dokumentationen för att göra detta finns här][azure_new_site]. På hög nivå gör du följande:

* Registrera dig för ett Azure-konto om du inte redan har en
* Använda Azures administratörs konsol för att skapa en ny webbplats
* Lägga till stöd för käll kontroll (vi antar att du har använt git)
* Skapa en fil `server.js` med ett enkelt node.js-webbprogram
* Distribuera det här enkla programmet till Azure

<a name="twiliomodule"></a>

## <a name="configure-the-twilio-module"></a>Konfigurera Twilio-modulen
Därefter börjar vi skriva ett enkelt node.js program som använder sig av Twilio-API: et. Innan vi börjar måste vi konfigurera våra Twilio-kontoautentiseringsuppgifter.

### <a name="configuring-twilio-credentials-in-system-environment-variables"></a>Konfigurera Twilio-autentiseringsuppgifter i systemmiljövariabler
För att kunna göra autentiserade begär Anden mot Twilio-serverns Server del behöver vi vårt konto-SID och auth-token, som fungerar som användar namn och lösen ord som angetts för vårt Twilio-konto. Det säkraste sättet att konfigurera dessa för användning med Node-modulen i Azure är via systemmiljövariabler, som du kan ställa in direkt i Azure-administratörskonsolen.

Välj din node.js webbplats och klicka på länken "Konfigurera".  Om du rullar ned en bit visas ett fält där du kan ange konfigurations egenskaper för programmet.  Ange dina autentiseringsuppgifter för Twilio-kontot ([finns i Twilio-konsolen][twilio_console]) som visas – se till att namnge dem `TWILIO_ACCOUNT_SID` respektive `TWILIO_AUTH_TOKEN` :

![Administratörs konsol för Azure][azure-admin-console]

När du har konfigurerat dessa variabler startar du om ditt program i Azure-konsolen.

### <a name="declaring-the-twilio-module-in-packagejson"></a>Deklarera modulen Twilio i package.jspå
Sedan måste vi skapa en package.jspå för att kunna hantera de underordnade noderna i Node-modulen via [NPM]. `server.js`Skapa en fil med namnet på samma nivå som filen du skapade i självstudien för *Azure/node.js* `package.json` .  I den här filen placerar du följande:

```json
{
  "name": "application-name",
  "version": "0.0.1",
  "private": true,
  "scripts": {
    "start": "node server"
  },
  "dependencies": {
    "body-parser": "^1.16.1",
    "ejs": "^2.5.5",
    "errorhandler": "^1.5.0",
    "express": "^4.14.1",
    "morgan": "^1.8.1",
    "twilio": "^2.11.1"
  }
}
```

Detta deklarerar Twilio-modulen som ett beroende, samt det populära [Express-webbramverket][express] och EJS Template Engine.  Nu är vi allt set-vi skriver lite kod!

<a name="makecall"></a>

## <a name="make-an-outbound-call"></a>Gör ett utgående samtal
Nu ska vi skapa ett enkelt formulär som kommer att placera ett samtal till ett tal som vi väljer. Öppna `server.js` och ange följande kod. Observera att det står "CHANGE_ME" – Lägg till namnet på din Azure-webbplats där:

```javascript
// Module dependencies
const express = require('express');
const path = require('path');
const http = require('http');
const twilio = require('twilio');
const logger = require('morgan');
const bodyParser = require('body-parser');
const errorHandler = require('errorhandler');
const accountSid = process.env.TWILIO_ACCOUNT_SID;
const authToken = process.env.TWILIO_AUTH_TOKEN;
// Create Express web application
const app = express();

// Express configuration
app.set('port', process.env.PORT || 3000);
app.set('views', __dirname + '/views');
app.set('view engine', 'ejs');
app.use(logger('tiny'));
app.use(bodyParser.urlencoded({ extended: false }))
app.use(bodyParser.json())
app.use(express.static(path.join(__dirname, 'public')));

if (app.get('env') !== 'production') {
  app.use(errorHandler());
}

// Render an HTML user interface for the application's home page
app.get('/', (request, response) => response.render('index'));

// Handle the form POST to place a call
app.post('/call', (request, response) => {
  var client = twilio(accountSid, authToken);

  client.makeCall({
    // make a call to this number
    to:request.body.number,

    // Change to a Twilio number you bought - see:
    // https://www.twilio.com/console/phone-numbers/incoming
    from:'+15558675309',

    // A URL in our app which generates TwiML
    // Change "CHANGE_ME" to your app's name
    url:'https://CHANGE_ME.azurewebsites.net/outbound_call'
  }, () => {
      // Go back to the home page
      response.redirect('/');
  });
});

// Generate TwiML to handle an outbound call
app.post('/outbound_call', (request, response) => {
  var twiml = new twilio.TwimlResponse();

  // Say a message to the call's receiver
  twiml.say('hello - thanks for checking out Twilio and Azure', {
      voice:'woman'
  });

  response.set('Content-Type', 'text/xml');
  response.send(twiml.toString());
});

// Start server
app.listen(app.get('port'), function(){
  console.log(`Express server listening on port ${app.get('port')}`);
});
```

Skapa sedan en katalog som heter `views` -inuti den här katalogen, skapa en fil med namnet `index.ejs` med följande innehåll:

```html
<!DOCTYPE html>
<html>
<head>
  <title>Twilio Test</title>
  <style>
    input { height:20px; width:300px; font-size:18px; margin:5px; padding:5px; }
  </style>
</head>
<body>
  <h1>Twilio Test</h1>
  <form action="/call" method="POST">
      <input placeholder="Enter a phone number" name="number"/>
      <br/>
      <input type="submit" value="Call the number above"/>
  </form>
</body>
</html>
```

Nu ska du distribuera din webbplats till Azure och öppna din start sida. Du bör kunna ange ditt telefonnummer i textfältet och ta emot ett samtal från din Twilio-siffra!

<a name="sendmessage"></a>

## <a name="send-an-sms-message"></a>Skicka ett SMS-meddelande
Nu ska vi ställa in en logik för användar gränssnitt och formulär hantering för att skicka ett textmeddelande. Öppna `server.js` och Lägg till följande kod efter det senaste anropet till `app.post` :

```javascript
app.post('/sms', (request, response) => {
  const client = twilio(accountSid, authToken);

  client.sendSms({
      // send a text to this number
      to:request.body.number,

      // A Twilio number you bought - see:
      // https://www.twilio.com/console/phone-numbers/incoming
      from:'+15558675309',

      // The body of the text message
      body: request.body.message

  }, () => {
      // Go back to the home page
      response.redirect('/');
  });
});
```

I `views/index.ejs` lägger du till ett annat formulär under det första för att skicka in ett tal och ett SMS:

```html
<form action="/sms" method="POST">
  <input placeholder="Enter a phone number" name="number"/>
  <br/>
  <input placeholder="Enter a message to send" name="message"/>
  <br/>
  <input type="submit" value="Send text to the number above"/>
</form>
```

Distribuera ditt program till Azure på nytt och du bör nu kunna skicka formuläret och skicka dig själv (eller något av dina närmaste vänner) till ett textmeddelande!

<a name="nextsteps"></a>

## <a name="next-steps"></a>Nästa steg
Nu har du lärt dig grunderna för att använda node.js-och Twilio för att bygga appar som kommunicerar. Men de här exemplen ligger i grunden på ytan på vad som är möjligt med Twilio och node.js. Mer information om hur du använder Twilio med node.js finns i följande resurser:

* [Dokumentation om officiella moduler][docs]
* [Självstudie om VoIP med node.js program][voipnode]
* [Votr – ett SMS-röstnings program i real tid med node.js och CouchDB (tre delar)][votr]
* [Para ihop programmering i webbläsaren med node.js][pair]

Vi hoppas att du älskar hackning node.js och Twilio på Azure!

[purchase_phone]: https://www.twilio.com/console/phone-numbers/search
[twiml]: https://www.twilio.com/docs/api/twiml
[signup]: https://ahoy.twilio.com/azure
[azure_new_site]: app-service/quickstart-nodejs.md
[twilio_console]: https://www.twilio.com/console
[npm]: https://npmjs.org
[express]: https://expressjs.com
[voipnode]: https://www.twilio.com/blog/2013/04/introduction-to-twilio-client-with-node-js.html
[docs]: https://www.twilio.com/docs/libraries/reference/twilio-node/
[votr]: https://www.twilio.com/blog/2012/09/building-a-real-time-sms-voting-app-part-1-node-js-couchdb.html
[pair]: https://www.twilio.com/blog/2013/06/pair-programming-in-the-browser-with-twilio.html
[azure-admin-console]: ./media/partner-twilio-nodejs-how-to-use-voice-sms/twilio_1.png
