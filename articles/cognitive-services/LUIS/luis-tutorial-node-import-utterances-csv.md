---
title: Importera yttranden med Node.js-LUIS
titleSuffix: Azure Cognitive Services
description: Lär dig hur du skapar en LUIS-app program mässigt från befintliga data i CSV-format med hjälp av LUIS Authoring-API.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18, devx-track-javascript
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: 3df1ccadb69f02272c44a0ba574805c816151dc7
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88520948"
---
# <a name="build-a-luis-app-programmatically-using-nodejs"></a>Bygg en LUIS-app program mässigt med hjälp av Node.js

LUIS tillhandahåller ett programmerings-API som gör allt som [Luis](luis-reference-regions.md) -webbplatsen gör. Det kan spara tid när du har befintliga data och det skulle bli snabbare att skapa en LUIS app program mässigt än genom att ange information manuellt.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="prerequisites"></a>Förutsättningar

* Logga in på [Luis](luis-reference-regions.md) -webbplatsen och hitta [redigerings nyckeln](luis-how-to-azure-subscription.md#authoring-key) i konto inställningar. Du använder den här nyckeln för att anropa redigerings-API: erna.
* Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/cognitive-services/) innan du börjar.
* Den här artikeln börjar med en CSV-fil för ett hypotetiskt företags loggfiler för användar förfrågningar. Ladda ned den [här](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/IoT.csv).
* Installera den senaste Node.js med NPM. Ladda ned det [här](https://nodejs.org/en/download/).
* **[Rekommenderas]** Visual Studio Code för IntelliSense och fel sökning kan du ladda ned dem [kostnads fritt](https://code.visualstudio.com/) .

All kod i den här artikeln är tillgänglig i [Azure-samples language Understanding GitHub-lagringsplatsen](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/examples/build-app-programmatically-csv).

## <a name="map-preexisting-data-to-intents-and-entities"></a>Mappa befintliga data till avsikter och entiteter
Även om du har ett system som inte har skapats med LUIS i åtanke, om det innehåller text data som mappar till olika saker som användare vill göra, kan du komma igång med en mappning från de befintliga kategorierna med användarindata till inmatningar i LUIS. Om du kan identifiera viktiga ord eller fraser i vad användarna är, kan dessa ord mappas till entiteter.

Öppna [`IoT.csv`](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/IoT.csv) filen. Den innehåller en logg med användar frågor till en hypotetisk start automatiserings tjänst, inklusive hur de kategoriserades, vad användaren sade och vissa kolumner med användbar information som hämtas från dem.

![CSV-fil för redan befintliga data](./media/luis-tutorial-node-import-utterances-csv/csv.png)

Du ser att kolumnen **RequestType** kan vara avsikter och kolumnen **Request** visar ett exempel på uttryck. De andra fälten kan vara entiteter om de förekommer i uttryck. Eftersom det finns avsikter, entiteter och exempel yttranden, har du kraven för en enkel, exempel-App.

## <a name="steps-to-generate-a-luis-app-from-non-luis-data"></a>Steg för att skapa en LUIS-app från icke-LUIS data
Så här skapar du en ny LUIS-app från CSV-filen:

* Parsa data från CSV-filen:
    * Konvertera till ett format som du kan överföra till LUIS med redigerings-API: et.
    * Samla in information om avsikter och entiteter från parsade data.
* Gör redigering av API-anrop till:
    * Skapa appen.
    * Lägg till avsikter och entiteter som har samlats in från parsade data.
    * När du har skapat LUIS-appen kan du lägga till exempel yttranden från parsade data.

Du kan se det här program flödet i den sista delen av `index.js` filen. Kopiera eller [Ladda ned](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/index.js) koden och spara den i `index.js` .

   [!code-javascript[Node.js code for calling the steps to build a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/index.js)]


## <a name="parse-the-csv"></a>Parsa CSV

Kolumn posterna som innehåller yttranden i CSV-filen måste parsas till ett JSON-format som LUIS kan förstå. Det här JSON-formatet måste innehålla ett `intentName` fält som identifierar syftet med uttryck. Det måste också innehålla ett `entityLabels` fält som kan vara tomt om det inte finns några entiteter i uttryck.

Posten "slå på ljuset" mappar till exempel till följande JSON:

```json
        {
            "text": "Turn on the lights",
            "intentName": "TurnOn",
            "entityLabels": [
                {
                    "entityName": "Operation",
                    "startCharIndex": 5,
                    "endCharIndex": 6
                },
                {
                    "entityName": "Device",
                    "startCharIndex": 12,
                    "endCharIndex": 17
                }
            ]
        }
```

I det här exemplet `intentName` kommer användar förfrågningen från den **begär ande** kolumn rubriken i CSV-filen och `entityName` kommer från de andra kolumnerna med viktig information. Om det till exempel finns en post för **åtgärden** eller **enheten**, och den strängen även förekommer i den faktiska begäran, kan den märkas som en entitet. Följande kod visar den här parsing-processen. Du kan kopiera eller [Ladda ned](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_parse.js) den och spara den i `_parse.js` .

   [!code-javascript[Node.js code for parsing a CSV file to extract intents, entities, and labeled utterances](~/samples-luis/examples/build-app-programmatically-csv/_parse.js)]



## <a name="create-the-luis-app"></a>Skapa LUIS-appen
När data har parsats i JSON lägger du till dem i en LUIS-app. Följande kod skapar LUIS-appen. Kopiera eller [Ladda ned](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_create.js) den och spara den i `_create.js` .

   [!code-javascript[Node.js code for creating a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/_create.js)]


## <a name="add-intents"></a>Lägg till avsikter
När du har en app måste du göra det. Följande kod skapar LUIS-appen. Kopiera eller [Ladda ned](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_intents.js) den och spara den i `_intents.js` .

   [!code-javascript[Node.js code for creating a series of intents](~/samples-luis/examples/build-app-programmatically-csv/_intents.js)]


## <a name="add-entities"></a>Lägg till entiteter
Följande kod lägger till entiteterna i LUIS-appen. Kopiera eller [Ladda ned](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_entities.js) den och spara den i `_entities.js` .

   [!code-javascript[Node.js code for creating entities](~/samples-luis/examples/build-app-programmatically-csv/_entities.js)]



## <a name="add-utterances"></a>Lägg till yttranden
När entiteter och avsikter har definierats i LUIS-appen kan du lägga till yttranden. Följande kod använder [Utterances_AddBatch](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09) API, som gör att du kan lägga till upp till 100 yttranden i taget.  Kopiera eller [Ladda ned](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_upload.js) den och spara den i `_upload.js` .

   [!code-javascript[Node.js code for adding utterances](~/samples-luis/examples/build-app-programmatically-csv/_upload.js)]


## <a name="run-the-code"></a>Kör koden


### <a name="install-nodejs-dependencies"></a>Installera Node.js beroenden
Installera Node.js beroenden från NPM på Terminal-/kommando raden.

```console
> npm install
```

### <a name="change-configuration-settings"></a>Ändra konfigurations inställningar
För att kunna använda det här programmet måste du ändra värdena i index.js-filen till din egen slut punkts nyckel och ange det namn som du vill att appen ska ha. Du kan också ställa in appens kultur eller ändra versions numret.

Öppna filen index.js och ändra värdena överst i filen.


```javascript
// Change these values
const LUIS_programmaticKey = "YOUR_AUTHORING_KEY";
const LUIS_appName = "Sample App";
const LUIS_appCulture = "en-us";
const LUIS_versionId = "0.1";
```

### <a name="run-the-script"></a>Kör skriptet
Kör skriptet från en Terminal/kommando-rad med Node.js.

```console
> node index.js
```

eller

```console
> npm start
```

### <a name="application-progress"></a>Program förlopp
När programmet körs visar kommando raden förloppet. Kommando rads resultatet innehåller formatet på svaren från LUIS.

```console
> node index.js
intents: ["TurnOn","TurnOff","Dim","Other"]
entities: ["Operation","Device","Room"]
parse done
JSON file should contain utterances. Next step is to create an app with the intents and entities it found.
Called createApp, created app with ID 314b306c-0033-4e09-92ab-94fe5ed158a2
Called addIntents for intent named TurnOn.
Called addIntents for intent named TurnOff.
Called addIntents for intent named Dim.
Called addIntents for intent named Other.
Results of all calls to addIntent = [{"response":"e7eaf224-8c61-44ed-a6b0-2ab4dc56f1d0"},{"response":"a8a17efd-f01c-488d-ad44-a31a818cf7d7"},{"response":"bc7c32fc-14a0-4b72-bad4-d345d807f965"},{"response":"727a8d73-cd3b-4096-bc8d-d7cfba12eb44"}]
called addEntity for entity named Operation.
called addEntity for entity named Device.
called addEntity for entity named Room.
Results of all calls to addEntity= [{"response":"6a7e914f-911d-4c6c-a5bc-377afdce4390"},{"response":"56c35237-593d-47f6-9d01-2912fa488760"},{"response":"f1dd440c-2ce3-4a20-a817-a57273f169f3"}]
retrying add examples...

Results of add utterances = [{"response":[{"value":{"UtteranceText":"turn on the lights","ExampleId":-67649},"hasError":false},{"value":{"UtteranceText":"turn the heat on","ExampleId":-69067},"hasError":false},{"value":{"UtteranceText":"switch on the kitchen fan","ExampleId":-3395901},"hasError":false},{"value":{"UtteranceText":"turn off bedroom lights","ExampleId":-85402},"hasError":false},{"value":{"UtteranceText":"turn off air conditioning","ExampleId":-8991572},"hasError":false},{"value":{"UtteranceText":"kill the lights","ExampleId":-70124},"hasError":false},{"value":{"UtteranceText":"dim the lights","ExampleId":-174358},"hasError":false},{"value":{"UtteranceText":"hi how are you","ExampleId":-143722},"hasError":false},{"value":{"UtteranceText":"answer the phone","ExampleId":-69939},"hasError":false},{"value":{"UtteranceText":"are you there","ExampleId":-149588},"hasError":false},{"value":{"UtteranceText":"help","ExampleId":-81949},"hasError":false},{"value":{"UtteranceText":"testing the circuit","ExampleId":-11548708},"hasError":false}]}]
upload done
```




## <a name="open-the-luis-app"></a>Öppna appen LUIS
När skriptet har slutförts kan du logga in på [Luis](luis-reference-regions.md) och se Luis-appen som du skapade under **Mina appar**. Du bör kunna se yttranden som du har lagt till i **TurnON**, **TurnOff**och **none** -avsikter.

![TurnOn avsikt](./media/luis-tutorial-node-import-utterances-csv/imported-utterances-661.png)


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Testa och träna din app på LUIS webbplats](luis-interactive-test.md)

## <a name="additional-resources"></a>Ytterligare resurser

Det här exempel programmet använder följande LUIS-API: er:
- [Skapa app](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)
- [Lägg till avsikter](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0c)
- [Lägg till entiteter](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0e)
- [Lägg till yttranden](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09)
