---
title: 'Självstudie: starta den fördjupade läsaren med Node.js'
titleSuffix: Azure Cognitive Services
description: I den här självstudien skapar du ett Node.js-program som startar den fördjupade läsaren.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 01/14/2020
ms.author: metan
ms.custom: devx-track-javascript
ms.openlocfilehash: a8cd8bacc749ef44ee23e98709f04f75757f5919
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88516341"
---
# <a name="tutorial-launch-the-immersive-reader-nodejs"></a>Självstudie: starta den fördjupade läsaren (Node.js)

I [översikten](./overview.md)har du lärt dig om vad den fördjupade läsaren är och hur den implementerar beprövade tekniker för att förbättra läsningen av förståelse för språkstuderande, nya läsare och studenter med inlärnings skillnader. Den här självstudien beskriver hur du skapar ett Node.js webb program som startar den fördjupade läsaren. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en Node.js-webbapp med Express
> * Hämta en åtkomsttoken
> * Starta den fördjupade läsaren med exempel innehåll
> * Ange språket för ditt innehåll
> * Ange språket för gränssnittet för avancerad läsare
> * Starta den fördjupade läsaren med matematik innehåll

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/cognitive-services/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

* En fördjupad läsar resurs som kon figurer ATS för Azure Active Directory autentisering. Följ [dessa instruktioner](./how-to-create-immersive-reader.md) för att konfigurera. Du behöver några av de värden som skapas här när du konfigurerar miljö egenskaperna. Spara utdata från sessionen i en textfil för framtida bruk.
* [Node.js](https://nodejs.org/) och [garn](https://yarnpkg.com)
* En IDE, till exempel [Visual Studio Code](https://code.visualstudio.com/)

## <a name="create-a-nodejs-web-app-with-express"></a>Skapa en Node.js-webbapp med Express

Skapa en Node.js webbapp med `express-generator` verktyget.

```bash
npm install express-generator -g
express --view=pug myapp
cd myapp
```

Installera garn beroenden och Lägg till beroenden `request` och `dotenv` , som kommer att användas senare i självstudien.

```bash
yarn
yarn add request
yarn add dotenv
```

## <a name="acquire-an-azure-ad-authentication-token"></a>Hämta en Azure AD-autentiseringstoken

Skriv sedan ett Server dels-API för att hämta en Azure AD-autentiseringstoken.

Du behöver vissa värden från det nödvändiga steget för Azure AD auth Configuration ovan för den här delen. Se tillbaka till text filen som du sparade i sessionen.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

När du har dessa värden skapar du en ny fil med namnet _. kuvert_och klistrar in följande kod i den och anger dina egna egenskaps värden ovan. Lägg inte till citat tecken eller tecknen "{" och "}".

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Se till att du inte utför den här filen i käll kontrollen eftersom den innehåller hemligheter som inte bör göras offentliga.

Öppna sedan _app.js_ och Lägg till följande överst i filen. Detta läser in egenskaperna som definierats i. kuvert-filen som miljövariabler i noden.

```javascript
require('dotenv').config();
```

Öppna _routes\index.js_ -filen och ersätt innehållet med följande kod.

Den här koden skapar en API-slutpunkt som hämtar en Azure AD-autentiseringstoken med ditt huvud namn för tjänsten. Den hämtar också under domänen. Den returnerar sedan ett objekt som innehåller token och under domänen.

```javascript
var request = require('request');
var express = require('express');
var router = express.Router();

router.get('/getimmersivereaderlaunchparams', function(req, res) {
    request.post ({
                headers: {
                    'content-type': 'application/x-www-form-urlencoded'
                },
                url: `https://login.windows.net/${process.env.TENANT_ID}/oauth2/token`,
                form: {
                    grant_type: 'client_credentials',
                    client_id: process.env.CLIENT_ID,
                    client_secret: process.env.CLIENT_SECRET,
                    resource: 'https://cognitiveservices.azure.com/'
                }
        },
        function(err, resp, tokenResponse) {
                if (err) {
                    return res.status(500).send('CogSvcs IssueToken error');
                }

                const token = JSON.parse(tokenResponse).access_token;
                const subdomain = process.env.SUBDOMAIN;
                return res.send({token: token, subdomain: subdomain});
        }
  );
});

/* GET home page. */
router.get('/', function(req, res, next) {
  res.render('index', { title: 'Express' });
});

module.exports = router;

```

**Getimmersivereaderlaunchparams** API-slutpunkten bör skyddas bakom någon form av autentisering (till exempel [OAuth](https://oauth.net/2/)) för att hindra obehöriga användare från att hämta token som ska användas mot tjänsten för avancerad läsare och fakturering. Detta arbete ligger utanför den här självstudiens omfattning.

## <a name="launch-the-immersive-reader-with-sample-content"></a>Starta den fördjupade läsaren med exempel innehåll

1. Öppna _views\layout.pug_och Lägg till följande kod under `head` taggen innan `body` taggen. Dessa `script` taggar läser in SDK-och jQuery för [Avancerad läsare](https://github.com/microsoft/immersive-reader-sdk) .

    ```pug
    script(src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.2.js')
    script(src='https://code.jquery.com/jquery-3.3.1.min.js')
    ```

2. Öppna _views\index.pug_och ersätt innehållet med följande kod. Den här koden fyller sidan med visst exempel innehåll och lägger till en knapp som startar den fördjupade läsaren.

    ```pug
    extends layout

    block content
          h2(id='title') Geography
          p(id='content') The study of Earth's landforms is called physical geography. Landforms can be mountains and valleys. They can also be glaciers, lakes or rivers.
          div(class='immersive-reader-button' data-button-style='iconAndText' data-locale='en-US' onclick='launchImmersiveReader()')
          script.

            function getImmersiveReaderLaunchParamsAsync() {
                    return new Promise((resolve, reject) => {
                        $.ajax({
                                url: '/getimmersivereaderlaunchparams',
                                type: 'GET',
                                success: data => {
                                        resolve(data);
                                },
                                error: err => {
                                        console.log('Error in getting token and subdomain!', err);
                                        reject(err);
                                }
                        });
                    });
            }

            async function launchImmersiveReader() {
                    const content = {
                            title: document.getElementById('title').innerText,
                            chunks: [{
                                    content: document.getElementById('content').innerText + '\n\n',
                                    lang: 'en'
                            }]
                    };

                    const launchParams = await getImmersiveReaderLaunchParamsAsync();
                    const token = launchParams.token;
                    const subdomain = launchParams.subdomain;

                    ImmersiveReader.launchAsync(token, subdomain, content);
            }
    ```

3. Vår webbapp är nu klar. Starta appen genom att köra:

    ```bash
    npm start
    ```

4. Öppna webbläsaren och gå till _http://localhost:3000_ . Du bör se innehållet ovan på sidan. Klicka på knappen **fördjupad läsare** för att starta den fördjupade läsaren med ditt innehåll.

## <a name="specify-the-language-of-your-content"></a>Ange språket för ditt innehåll

Den fördjupade läsaren har stöd för många olika språk. Du kan ange språket för ditt innehåll genom att följa stegen nedan.

1. Öppna _views\index.pug_ och Lägg till följande kod under den `p(id=content)` tagg som du lade till i föregående steg. Den här koden lägger till innehåll spanska innehåll på din sida.

    ```pug
    p(id='content-spanish') El estudio de las formas terrestres de la Tierra se llama geografía física. Los accidentes geográficos pueden ser montañas y valles. También pueden ser glaciares, lagos o ríos.
    ```

2. I JavaScript-koden lägger du till följande ovanför anropet till `ImmersiveReader.launchAsync` . Den här koden skickar det spanska innehållet till den fördjupade läsaren.

    ```pug
    content.chunks.push({
      content: document.getElementById('content-spanish').innerText + '\n\n',
      lang: 'es'
    });
    ```

3. Navigera till _http://localhost:3000_ igen. Du bör se den spanska texten på sidan och när du klickar på **Avancerad läsare**visas den även i den fördjupade läsaren.

## <a name="specify-the-language-of-the-immersive-reader-interface"></a>Ange språket för gränssnittet för avancerad läsare

Som standard matchar språket i gränssnittet för avancerad läsare webbläsarens språk inställningar. Du kan också ange språket för gränssnittet för avancerad läsare med följande kod.

1. I _views\index.pug_ersätter du anropet till `ImmersiveReader.launchAsync(token, subdomain, content)` med koden nedan.

    ```javascript
    const options = {
        uiLang: 'fr',
    }
    ImmersiveReader.launchAsync(token, subdomain, content, options);
    ```

2. Navigera till _http://localhost:3000_ . När du startar den fördjupade läsaren visas gränssnittet på franska.

## <a name="launch-the-immersive-reader-with-math-content"></a>Starta den fördjupade läsaren med matematik innehåll

Du kan inkludera matematik innehåll i den fördjupade läsaren med hjälp av [mathml](https://developer.mozilla.org/en-US/docs/Web/MathML).

1. Ändra _views\index.pug_ för att inkludera följande kod ovanför anropet till `ImmersiveReader.launchAsync` :

    ```javascript
    const mathML = '<math xmlns="https://www.w3.org/1998/Math/MathML" display="block"> \
      <munderover> \
        <mo>∫</mo> \
        <mn>0</mn> \
        <mn>1</mn> \
      </munderover> \
      <mrow> \
        <msup> \
          <mi>x</mi> \
          <mn>2</mn> \
        </msup> \
        <mo>ⅆ</mo> \
        <mi>x</mi> \
      </mrow> \
    </math>';

    content.chunks.push({
      content: mathML,
      mimeType: 'application/mathml+xml'
    });
    ```

2. Navigera till _http://localhost:3000_ . När du startar den fördjupade läsaren och bläddrar längst ned visas den matematiska formeln.

## <a name="next-steps"></a>Nästa steg

* Utforska SDK: [n för avancerad läsare](https://github.com/microsoft/immersive-reader-sdk) och [Avancerad läsare SDK-referens](./reference.md)
* Visa kod exempel på [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/advanced-csharp)
