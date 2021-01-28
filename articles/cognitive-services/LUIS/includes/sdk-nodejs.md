---
title: ta med fil
description: ta med fil
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 09/01/2020
ms.topic: include
ms.custom: include file, devx-track-js, cog-serv-seo-aug-2020
ms.openlocfilehash: f4b9c84480940889b0278129952bcf2918d9c835
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947198"
---
Använd Language Understanding (LUIS) klient biblioteken för Node.js för att:

* Skapa en app
* Lägg till en avsikt, en enhet som har lärts in, med ett exempel uttryck
* Träna och publicera appen
* CLR för frågeförutsägelse

[Referens dokumentation](/javascript/api/@azure/cognitiveservices-luis-authoring/)  |   [Redigera](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-authoring) och [förutsäga](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-runtime) bibliotekets käll kod | [Redigera](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring) och [förutsäga](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime) NPM | [Exempel](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/LUIS/sdk-3x/index.js)

## <a name="prerequisites"></a>Förutsättningar

* [Node.js](https://nodejs.org)
* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services)
* När du har en Azure-prenumeration [skapar du en language Understanding Authoring-resurs](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) i Azure Portal för att hämta din nyckel och slut punkt. Vänta tills den har distribuerats och klicka på knappen **gå till resurs** .
    * Du behöver nyckeln och slut punkten från den resurs som du [skapar](../luis-how-to-azure-subscription.md#create-luis-resources-in-the-azure-portal) för att ansluta ditt program till language Understanding redigering. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten. Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten.

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-new-javascript-application"></a>Skapa ett nytt JavaScript-program

1. I ett konsol fönster skapar du en ny katalog för programmet och flyttar till den katalogen.

    ```console
    mkdir quickstart-sdk && cd quickstart-sdk
    ```

1. Initiera katalogen som ett JavaScript-program genom att skapa en `package.json` fil.

    ```console
    npm init -y
    ```

1. Skapa en fil med namnet `index.js` för din JavaScript-kod.

    ```console
    touch index.js
    ```

### <a name="install-the-npm-libraries"></a>Installera NPM-biblioteken

I program katalogen installerar du beroendena med följande kommandon, utförde en rad i taget:

```console
npm install @azure/ms-rest-js
npm install @azure/cognitiveservices-luis-authoring
npm install @azure/cognitiveservices-luis-runtime
```

Din `package.json` bör se ut så här:

```json
{
  "name": "quickstart-sdk",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "@azure/cognitiveservices-luis-authoring": "^4.0.0-preview.3",
    "@azure/cognitiveservices-luis-runtime": "^5.0.0",
    "@azure/ms-rest-js": "^2.0.8"
  }
}
```

## <a name="authoring-object-model"></a>Redigera objekt modell

Redigerings klienten för Language Understanding (LUIS) är ett [LUISAuthoringClient](/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient) -objekt som autentiserar till Azure, som innehåller din redigerings nyckel.

## <a name="code-examples-for-authoring"></a>Kod exempel för redigering

När klienten har skapats använder du den här klienten för att få åtkomst till funktioner, inklusive:

* Appar- [Lägg till](/javascript/api/@azure/cognitiveservices-luis-authoring/apps#add-applicationcreateobject--msrest-requestoptionsbase-), [ta bort](/javascript/api/@azure/cognitiveservices-luis-authoring/apps#deletemethod-string--models-appsdeletemethodoptionalparams-), [publicera](/javascript/api/@azure/cognitiveservices-luis-authoring/apps#publish-string--applicationpublishobject--msrest-requestoptionsbase-)
* Exempel yttranden – [Lägg till via batch](/javascript/api/@azure/cognitiveservices-luis-authoring/examples#batch-string--string--examplelabelobject----msrest-requestoptionsbase-), [ta bort efter ID](/javascript/api/@azure/cognitiveservices-luis-authoring/examples#deletemethod-string--string--number--msrest-requestoptionsbase-)
* Funktioner – hantera [fras listor](/javascript/api/@azure/cognitiveservices-luis-authoring/features#addphraselist-string--string--phraselistcreateobject--msrest-requestoptionsbase-)
* Modell-hantera [avsikter](/javascript/api/@azure/cognitiveservices-luis-authoring/model#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-) och entiteter
* Mönster – hantera [mönster](/javascript/api/@azure/cognitiveservices-luis-authoring/pattern#addpattern-string--string--patternrulecreateobject--msrest-requestoptionsbase-)
* Träna – [träna](/javascript/api/@azure/cognitiveservices-luis-authoring/train#trainversion-string--string--msrest-requestoptionsbase-) appen och avsökningen efter [kurs status](/javascript/api/@azure/cognitiveservices-luis-authoring/train#getstatus-string--string--msrest-requestoptionsbase-)
* [Versioner](/javascript/api/@azure/cognitiveservices-luis-authoring/versions) – hantera med klona, exportera och ta bort

## <a name="prediction-object-model"></a>Förutsägelse objekt modell

Redigerings klienten för Language Understanding (LUIS) är ett [LUISAuthoringClient](/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient) -objekt som autentiserar till Azure, som innehåller din redigerings nyckel.

## <a name="code-examples-for-prediction-runtime"></a>Kod exempel för förutsägelse körning

När klienten har skapats använder du den här klienten för att få åtkomst till funktioner, inklusive:

* [Förutsägelse](/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-) efter `staging` eller `production` plats
* [Förutsägelse efter version](/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations#getversionprediction-string--string--predictionrequest--models-predictiongetversionpredictionoptionalparams-)

[!INCLUDE [Bookmark links to same article](sdk-code-examples.md)]

## <a name="add-the-dependencies"></a>Lägg till beroenden

Öppna `index.js` filen i önskat redigerings program eller IDE-namn och Lägg sedan till följande beroenden.

[!code-javascript[Add NPM libraries to code file](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=Dependencies)]

## <a name="add-boilerplate-code"></a>Lägg till en exempel kod

1. Lägg till- `quickstart` metoden och dess anrop. Den här metoden innehåller de flesta återstående koder. Den här metoden anropas i slutet av filen.

    ```javascript
    const quickstart = async () => {

        // add calls here


    }
    quickstart()
        .then(result => console.log("Done"))
        .catch(err => {
            console.log(`Error: ${err}`)
            })
    ```

1. Lägg till den återstående koden i snabb starts metoden om inget annat anges.

## <a name="create-variables-for-the-app"></a>Skapa variabler för appen

Skapa två uppsättningar variabler: den första uppsättningen som du ändrar, den andra anges i kod exemplet. 

1. Skapa variabler för att hålla dina redigerings nycklar och resurs namn.

    [!code-javascript[Variables you need to change](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=VariablesYouChange)]

1. Skapa variabler för att lagra dina slut punkter, app-namn, version och namn på avsikt.

    [!code-javascript[Variables you don't need to change](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=VariablesYouDontNeedToChangeChange)]

## <a name="authenticate-the-client"></a>Autentisera klienten

Skapa ett [CognitiveServicesCredentials](/javascript/api/@azure/ms-rest-js/apikeycredentials) -objekt med din nyckel och Använd den med slut punkten för att skapa ett [LUISAuthoringClient](/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient) -objekt.

[!code-javascript[Authenticate the client](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>Skapa en LUIS-app

En LUIS-app innehåller NLP-modellen (Natural Language Processing) inklusive avsikter, entiteter och exempel yttranden.

Skapa appen genom att skapa ett [AppsOperation](/javascript/api/@azure/cognitiveservices-luis-authoring/apps) -objekts [Add](/javascript/api/@azure/cognitiveservices-luis-authoring/apps) -metod. Namnet och språk kulturen är obligatoriska egenskaper.

[!code-javascript[Create a LUIS app](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AuthoringCreateApplication)]


## <a name="create-intent-for-the-app"></a>Skapa avsikt för appen
Det primära objektet i en LUIS-Apps modell är avsikten. Avsikten med en gruppering av användarens _uttryck._ En användare kan ställa en fråga eller göra en instruktion som söker efter ett särskilt _avsett_ svar från en robot (eller något annat klient program). Exempel på avsikt är att boka en flygning och fråga om väder i en destinations ort och fråga om kontakt information för kund tjänst.

Använd metoden [Model.add_intent](/javascript/api/@azure/cognitiveservices-luis-authoring/model#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-) med namnet på den unika avsikten och skicka appens ID, VERSIONS-ID och nytt namn för avsikten.

`intentName`Värdet hårdkodas till `OrderPizzaIntent` som en del av variablerna i avsnittet [Skapa variabler för appen](#create-variables-for-the-app) .

[!code-javascript[Create intent for the app](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AddIntent)]

## <a name="create-entities-for-the-app"></a>Skapa entiteter för appen

Även om entiteter inte krävs finns de i de flesta appar. Entiteten extraherar information från användar uttryck, som krävs för att fullfil användarens avsikt. Det finns flera typer av [färdiga](/javascript/api/@azure/cognitiveservices-luis-authoring/model#addcustomprebuiltentity-string--string--prebuiltdomainmodelcreateobject--msrest-requestoptionsbase-) och anpassade entiteter, var och en med sina egna DTO-modeller (data Transformation Object).  Vanliga fördefinierade entiteter som ska läggas till i din app är [Number](../luis-reference-prebuilt-number.md), [datetimeV2](../luis-reference-prebuilt-datetimev2.md), [geographyV2](../luis-reference-prebuilt-geographyv2.md), [ordinal](../luis-reference-prebuilt-ordinal.md).

Det är viktigt att veta att entiteter inte har marker ATS med ett avsikts syfte. De kan och används vanligt vis för många syften. Endast yttranden är markerade för ett visst, enskilt syfte.

Skapande metoder för entiteter ingår i [modell](/javascript/api/@azure/cognitiveservices-luis-authoring/model) klassen. Varje entitetstyp har sin egen DTO-modell (data Transformation Object).

Entitetens skapande kod skapar en enhet för maskin inlärning med underentiteter och funktioner som tillämpas på `Quantity` underentiteterna.

:::image type="content" source="../media/quickstart-sdk/machine-learned-entity.png" alt-text="En del skärm bild från portalen som visar att entiteten har skapats, en enhet för maskin inlärning med underentiteter och funktioner som används i underentiteterna &quot;kvantitet&quot;.":::

[!code-javascript[Create entities for the app](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AuthoringAddEntities)]

Placera följande metod ovanför `quickstart` -metoden för att hitta underentitetens antal underentiteter för att tilldela funktionerna till den underentiteten.

[!code-javascript[Find subentity id](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AuthoringSortModelObject)]

## <a name="add-example-utterance-to-intent"></a>Lägg till exempel uttryck till avsikt

Appen behöver exempel på yttranden för att kunna fastställa en uttryck för avsikt och extrahering av entiteter. Exemplen måste vara riktade mot en viss, enskild avsikt och ska markera alla anpassade entiteter. Fördefinierade entiteter behöver inte markeras.

Lägg till exempel yttranden genom att skapa en lista över [ExampleLabelObject](/javascript/api/@azure/cognitiveservices-luis-authoring/examplelabelobject) -objekt, ett objekt för varje exempel uttryck. Varje exempel bör markera alla entiteter med en ord lista med namn/värde-par för enhets namn och enhets värde. Enhet svärdet bör vara exakt så som det visas i texten i exemplet uttryck.

:::image type="content" source="../media/quickstart-sdk/labeled-example-machine-learned-entity.png" alt-text="En del skärm bild som visar den märkta uttryck i portalen. ":::

Anropa [exempel. Lägg till](/javascript/api/@azure/cognitiveservices-luis-authoring/examples) med app-ID, VERSIONS-ID och exemplet.

[!code-javascript[Add example utterance to intent](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AuthoringAddLabeledExamples)]

## <a name="train-the-app"></a>Träna appen

När modellen har skapats måste LUIS-appen tränas för den här versionen av modellen. En utbildad modell kan användas i en [behållare](../luis-container-howto.md)eller [publiceras](../luis-how-to-publish-app.md) på mellanlagrings-eller produkt platserna.

Metoden [Train. trainVersion](/javascript/api/@azure/cognitiveservices-luis-authoring/train#trainversion-string--string--msrest-requestoptionsbase-) kräver app-ID och VERSIONS-ID.

En mycket liten modell, till exempel den här snabb starten visar, kommer att träna mycket snabbt. Utbildning appen måste innehålla ett avsöknings anrop till [get_Status](/javascript/api/@azure/cognitiveservices-luis-authoring/train#getstatus-string--string--msrest-requestoptionsbase-) -metoden för att fastställa när utbildningen har genomförts för program på produktions nivå. Svaret är en lista över [ModelTrainingInfo](/javascript/api/@azure/cognitiveservices-luis-authoring/modeltraininginfo) -objekt med separat status för varje objekt. Alla objekt måste lyckas för att träningen ska anses vara slutförd.

[!code-javascript[Train the app](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=TrainAppVersion)]

## <a name="publish-app-to-production-slot"></a>Publicera appen till produktions platsen

Publicera LUIS-appen med metoden [app. Publish](/javascript/api/@azure/cognitiveservices-luis-authoring/apps#publish-string--applicationpublishobject--msrest-requestoptionsbase-) . Detta publicerar den aktuella utbildade versionen till den angivna platsen vid slut punkten. Klient programmet använder den här slut punkten för att skicka användarens yttranden för förutsägelse av avsikt och extrahering av enheter.

[!code-javascript[Publish app to production slot](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=PublishVersion)]


## <a name="authenticate-the-prediction-runtime-client"></a>Autentisera klienten för förutsägelse körning

Använd ett msRest. ApiKeyCredentials-objekt med din nyckel och Använd slut punkten för att skapa en [Luis. LUISRuntimeClient](/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient) -objekt.

[!INCLUDE [Caution about using authoring key](caution-authoring-key.md)]

[!code-javascript [Authenticate the prediction runtime client](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=PredictionCreateClient)]

## <a name="get-prediction-from-runtime"></a>Hämta förutsägelse från körning

Lägg till följande kod för att skapa begäran till förutsägelse körning. Användaren uttryck är en del av [predictionRequest](/javascript/api/@azure/cognitiveservices-luis-runtime/predictionrequest) -objektet.

Metoden **[luisRuntimeClient. förutsägelse. getSlotPrediction](/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-)** kräver flera parametrar, till exempel App-ID, plats namn och objektet förutsägelse begär Ande för att uppfylla begäran. De andra alternativen, till exempel verbose, Visa alla avsikter och loggen är valfria.

[!code-javascript [Get prediction from runtime](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=QueryPredictionEndpoint)]

[!INCLUDE [Prediction JSON response](sdk-json.md)]

## <a name="run-the-application"></a>Kör programmet

Kör programmet med `node index.js` kommandot på snabb starts filen.

```console
node index.js
```
