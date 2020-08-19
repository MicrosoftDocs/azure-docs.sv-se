---
title: 'Självstudie: integrera flera fördjupade läsare resurser'
titleSuffix: Azure Cognitive Services
description: I den här självstudien skapar du ett Node.js-program som startar den fördjupade läsaren med flera fördjupade läsares resurser.
author: skamal
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 01/14/2020
ms.author: skamal
ms.custom: devx-track-javascript
ms.openlocfilehash: 1abd1052c33346d949809d7c35a99ef97bde2c03
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88516290"
---
# <a name="tutorial-integrate-multiple-immersive-reader-resources"></a>Självstudie: integrera flera fördjupade läsare resurser

I [översikten](./overview.md)har du lärt dig om vad den fördjupade läsaren är och hur den implementerar beprövade tekniker för att förbättra läsningen av förståelse för språkstuderande, nya läsare och studenter med inlärnings skillnader. I snabb starten för [Node.js](./quickstarts/client-libraries.md?pivots=programming-language-nodejs)har du lärt dig hur du använder avancerad läsare med en enda resurs. I den här självstudien beskrivs hur du integrerar flera fördjupade Reader-resurser i samma program. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa flera fördjupad läsar resurs under en befintlig resurs grupp
> * Starta den fördjupade läsaren med flera resurser

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/cognitive-services/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

* Följ [snabb](./quickstarts/client-libraries.md?pivots=programming-language-nodejs) starten för att skapa en webbapp som startar den fördjupade läsaren med NodeJS. I den här snabb starten konfigurerar du en enskild fördjupad läsare-resurs. Vi kommer att bygga ovanpå det i den här självstudien.

## <a name="create-the-immersive-reader-resources"></a>Skapa fördjupade läsar resurser

Följ [de här anvisningarna](./how-to-create-immersive-reader.md) för att skapa varje fördjupad läsar resurs. **Create-ImmersiveReaderResource** -skriptet har `ResourceName` , `ResourceSubdomain` , och `ResourceLocation` som parametrar. Dessa bör vara unika för varje resurs som skapas. De återstående parametrarna bör vara samma som det som du använde när du installerade din första fördjupade läsare-resurs. På så sätt kan varje resurs länkas till samma Azure-resurs grupp och Azure AD-program.

Exemplet nedan visar hur du skapar två resurser, en i väster och en annan i öster. Observera unika värden för `ResourceName` , `ResourceSubdomain` och `ResourceLocation` .

```azurepowershell-interactive
Create-ImmersiveReaderResource
  -SubscriptionName <SUBSCRIPTION_NAME> `
  -ResourceName Resource_name_wus `
  -ResourceSubdomain resource-subdomain-wus `
  -ResourceSKU <RESOURCE_SKU> `
  -ResourceLocation westus `
  -ResourceGroupName <RESOURCE_GROUP_NAME> `
  -ResourceGroupLocation <RESOURCE_GROUP_LOCATION> `
  -AADAppDisplayName <AAD_APP_DISPLAY_NAME> `
  -AADAppIdentifierUri <AAD_APP_IDENTIFIER_URI> `
  -AADAppClientSecret <AAD_APP_CLIENT_SECRET>

Create-ImmersiveReaderResource
  -SubscriptionName <SUBSCRIPTION_NAME> `
  -ResourceName Resource_name_eus `
  -ResourceSubdomain resource-subdomain-eus `
  -ResourceSKU <RESOURCE_SKU> `
  -ResourceLocation eastus `
  -ResourceGroupName <RESOURCE_GROUP_NAME> `
  -ResourceGroupLocation <RESOURCE_GROUP_LOCATION> `
  -AADAppDisplayName <AAD_APP_DISPLAY_NAME> `
  -AADAppIdentifierUri <AAD_APP_IDENTIFIER_URI> `
  -AADAppClientSecret <AAD_APP_CLIENT_SECRET>
```

## <a name="add-resources-to-environment-configuration"></a>Lägg till resurser i miljö konfigurationen

I snabb starten skapade du en miljö konfigurations fil som innehåller `TenantId` parametrarna, `ClientId` , `ClientSecret` och `Subdomain` . Eftersom alla resurser använder samma Azure AD-program kan vi använda samma värden för `TenantId` , `ClientId` , och `ClientSecret` . Den enda ändring som behöver göras är att lista varje under domän för varje resurs.

Din nya __. kuvert__ -fil bör nu se ut ungefär så här:

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN_WUS={YOUR_WESTUS_SUBDOMAIN}
SUBDOMAIN_EUS={YOUR_EASTUS_SUBDOMAIN}
```

Se till att du inte utför den här filen i käll kontrollen eftersom den innehåller hemligheter som inte bör göras offentliga.

Nu ska vi ändra  _routes\index.js_ -filen som vi har skapat för att stödja våra flera resurser. Ersätt dess innehåll med följande kod.

Som tidigare skapar den här koden en API-slutpunkt som skaffar en Azure AD-autentiseringstoken med ditt huvud namn för tjänsten. Den här gången kan användaren ange en resurs plats och skicka den som en frågeparameter. Den returnerar sedan ett-objekt som innehåller token och motsvarande under domän.

```javascript
var express = require('express');
var router = express.Router();
var request = require('request');

/* GET home page. */
router.get('/', function(req, res, next) {
  res.render('index', { title: 'Express' });
});

router.get('/GetTokenAndSubdomain', function(req, res) {
    try {
        request.post({
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
        function(err, resp, tokenResult) {
            if (err) {
                console.log(err);
                return res.status(500).send('CogSvcs IssueToken error');
            }

            var tokenResultParsed = JSON.parse(tokenResult);

            if (tokenResultParsed.error) {
                console.log(tokenResult);
                return res.send({error :  "Unable to acquire Azure AD token. Check the debugger for more information."})
            }

            var token = tokenResultParsed.access_token;

            var subdomain = "";
            var region = req.query && req.query.region;
            switch (region) {
                case "eus":
                    subdomain = process.env.SUBDOMAIN_EUS
                    break;
                case "wus":
                default:
                    subdomain = process.env.SUBDOMAIN_WUS
            }

            return res.send({token, subdomain});
        });
    } catch (err) {
        console.log(err);
        return res.status(500).send('CogSvcs IssueToken error');
    }
});

module.exports = router;
```

**Getimmersivereaderlaunchparams** API-slutpunkten bör skyddas bakom någon form av autentisering (till exempel [OAuth](https://oauth.net/2/)) för att hindra obehöriga användare från att hämta token som ska användas mot tjänsten för avancerad läsare och fakturering. Detta arbete ligger utanför den här självstudiens omfattning.

## <a name="launch-the-immersive-reader-with-sample-content"></a>Starta den fördjupade läsaren med exempel innehåll

1. Öppna _views\index.pug_och ersätt innehållet med följande kod. Den här koden fyller sidan med visst exempel innehåll och lägger till två knappar som startar den fördjupade läsaren. En för att starta en avancerad läsare för den östra resursen och en annan för den västra resursen.

    ```pug
    doctype html
    html
        head
            title Immersive Reader Quickstart Node.js

            link(rel='stylesheet', href='https://stackpath.bootstrapcdn.com/bootstrap/3.4.1/css/bootstrap.min.css')

            // A polyfill for Promise is needed for IE11 support.
            script(src='https://cdn.jsdelivr.net/npm/promise-polyfill@8/dist/polyfill.min.js')

            script(src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.1.0.0.js')
            script(src='https://code.jquery.com/jquery-3.3.1.min.js')

            style(type="text/css").
                .immersive-reader-button {
                background-color: white;
                margin-top: 5px;
                border: 1px solid black;
                float: right;
                }
        body
            div(class="container")
                button(class="immersive-reader-button" data-button-style="icon" data-locale="en" onclick='handleLaunchImmersiveReader("wus")') WestUS Immersive Reader
                button(class="immersive-reader-button" data-button-style="icon" data-locale="en" onclick='handleLaunchImmersiveReader("eus")') EastUS Immersive Reader

                h1(id="ir-title") About Immersive Reader
                div(id="ir-content" lang="en-us")
                p Immersive Reader is a tool that implements proven techniques to improve reading comprehension for emerging readers, language learners, and people with learning differences. The Immersive Reader is designed to make reading more accessible for everyone. The Immersive Reader

                    ul
                        li Shows content in a minimal reading view
                        li Displays pictures of commonly used words
                        li Highlights nouns, verbs, adjectives, and adverbs
                        li Reads your content out loud to you
                        li Translates your content into another language
                        li Breaks down words into syllables

                h3 The Immersive Reader is available in many languages.

                p(lang="es-es") El Lector inmersivo está disponible en varios idiomas.
                p(lang="zh-cn") 沉浸式阅读器支持许多语言
                p(lang="de-de") Der plastische Reader ist in vielen Sprachen verfügbar.
                p(lang="ar-eg" dir="rtl" style="text-align:right") يتوفر \"القارئ الشامل\" في العديد من اللغات.

    script(type="text/javascript").
    function getTokenAndSubdomainAsync(region) {
            return new Promise(function (resolve, reject) {
                $.ajax({
                    url: "/GetTokenAndSubdomain",
                    type: "GET",
                    data: {
                        region: region
                    },
                    success: function (data) {
                        if (data.error) {
                            reject(data.error);
                        } else {
                            resolve(data);
                        }
                    },
                    error: function (err) {
                        reject(err);
                    }
                });
            });
        }

        function handleLaunchImmersiveReader(region) {
            getTokenAndSubdomainAsync(region)
                .then(function (response) {
                    const token = response["token"];
                    const subdomain = response["subdomain"];
                    // Learn more about chunk usage and supported MIME types https://docs.microsoft.com/azure/cognitive-services/immersive-reader/reference#chunk
                    const data = {
                        title: $("#ir-title").text(),
                        chunks: [{
                            content: $("#ir-content").html(),
                            mimeType: "text/html"
                        }]
                    };
                    // Learn more about options https://docs.microsoft.com/azure/cognitive-services/immersive-reader/reference#options
                    const options = {
                        "onExit": exitCallback,
                        "uiZIndex": 2000
                    };
                    ImmersiveReader.launchAsync(token, subdomain, data, options)
                        .catch(function (error) {
                            alert("Error in launching the Immersive Reader. Check the console.");
                            console.log(error);
                        });
                })
                .catch(function (error) {
                    alert("Error in getting the Immersive Reader token and subdomain. Check the console.");
                    console.log(error);
                });
        }

        function exitCallback() {
            console.log("This is the callback function. It is executed when the Immersive Reader closes.");
        }
    ```

3. Vår webbapp är nu klar. Starta appen genom att köra:

    ```bash
    npm start
    ```

4. Öppna webbläsaren och gå till `http://localhost:3000` . Du bör se innehållet ovan på sidan. Klicka på antingen knappen för **fördjupad läsare** eller knappen för **fördjupad** läsare för att starta den fördjupade läsaren med dessa respektive resurser.

## <a name="next-steps"></a>Nästa steg

* Utforska SDK: [n för avancerad läsare](https://github.com/microsoft/immersive-reader-sdk) och [Avancerad läsare SDK-referens](./reference.md)
* Visa kod exempel på [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/advanced-csharp)