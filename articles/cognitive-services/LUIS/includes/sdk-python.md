---
title: ta med fil
description: ta med fil
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 09/01/2020
ms.topic: include
ms.custom: include file, cog-serv-seo-aug-2020
ms.openlocfilehash: b44fae4d2a4c8d0a60d751068e765932fed7e8fb
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98948425"
---
Använda klient biblioteken Language Understanding (LUIS) för python för att:

* Skapa en app
* Lägg till en avsikt, en enhet som har lärts in, med ett exempel uttryck
* Träna och publicera appen
* CLR för frågeförutsägelse

[Referens dokumentation](/python/api/azure-cognitiveservices-language-luis/index)  |  [Redigera](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-luis/azure/cognitiveservices/language/luis/authoring) och [förutsäga](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-luis/azure/cognitiveservices/language/luis/runtime) bibliotekets käll kod | [Paket (pypi)](https://pypi.org/project/azure-cognitiveservices-language-luis/)  |  [Exempel](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/sdk-3x/authoring_and_predict.py)

## <a name="prerequisites"></a>Förutsättningar

* Den aktuella versionen av [python 3. x](https://www.python.org/).
* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services)
* När du har en Azure-prenumeration [skapar du en language Understanding Authoring-resurs](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) i Azure Portal för att hämta din nyckel och slut punkt. Vänta tills den har distribuerats och klicka på knappen **gå till resurs** .
    * Du behöver nyckeln och slut punkten från den resurs som du [skapar](../luis-how-to-azure-subscription.md#create-luis-resources-in-the-azure-portal) för att ansluta ditt program till language Understanding redigering. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten. Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten.

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-new-python-application"></a>Skapa ett nytt Python-program

1. I ett konsol fönster skapar du en ny katalog för programmet och flyttar till den katalogen.

    ```console
    mkdir quickstart-sdk && cd quickstart-sdk
    ```

1. Skapa en fil med namnet `authoring_and_predict.py` för din python-kod.

    ```console
    touch authoring_and_predict.py
    ```

### <a name="install-the-client-library-with-pip"></a>Installera klient biblioteket med pip

I program katalogen installerar du klient biblioteket Language Understanding (LUIS) för python med följande kommando:

```console
pip install azure-cognitiveservices-language-luis
```

## <a name="authoring-object-model"></a>Redigera objekt modell

Redigerings klienten för Language Understanding (LUIS) är ett [LUISAuthoringClient](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.luisauthoringclient) -objekt som autentiserar till Azure, som innehåller din redigerings nyckel.

## <a name="code-examples-for-authoring"></a>Kod exempel för redigering

När klienten har skapats använder du den här klienten för att få åtkomst till funktioner, inklusive:

* Appar- [skapa](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations#add-application-create-object--custom-headers-none--raw-false----operation-config-), [ta bort](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations#delete-app-id--force-false--custom-headers-none--raw-false----operation-config-), [publicera](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations#publish-app-id--version-id-none--is-staging-false--custom-headers-none--raw-false----operation-config-)
* Exempel yttranden – [Lägg till via batch](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations#batch-app-id--version-id--example-label-object-array--custom-headers-none--raw-false----operation-config-), [ta bort efter ID](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations#delete-app-id--version-id--example-id--custom-headers-none--raw-false----operation-config-)
* Funktioner – hantera [fras listor](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.featuresoperations#add-phrase-list-app-id--version-id--phraselist-create-object--custom-headers-none--raw-false----operation-config-)
* Modell-hantera [avsikter](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations#add-intent-app-id--version-id--name-none--custom-headers-none--raw-false----operation-config-) och entiteter
* Mönster – hantera [mönster](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.patternoperations#add-pattern-app-id--version-id--pattern-none--intent-none--custom-headers-none--raw-false----operation-config-)
* Träna – [träna](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations#train-version-app-id--version-id--custom-headers-none--raw-false----operation-config-) appen och avsökningen efter [kurs status](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations#get-status-app-id--version-id--custom-headers-none--raw-false----operation-config-)
* [Versioner](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.versionsoperations) – hantera med klona, exportera och ta bort


## <a name="prediction-object-model"></a>Förutsägelse objekt modell

Den Language Understanding (LUIS) förutsägelse runtime-klienten är ett [LUISRuntimeClient](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.luisruntimeclient) -objekt som autentiserar till Azure, som innehåller din resurs nyckel.

## <a name="code-examples-for-prediction-runtime"></a>Kod exempel för förutsägelse körning

När klienten har skapats använder du den här klienten för att få åtkomst till funktioner, inklusive:

* Förutsägelse per [mellanlagrings-eller produktions plats](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations#get-slot-prediction-app-id--slot-name--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)
* Förutsägelse efter [version](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations#get-version-prediction-app-id--version-id--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)

[!INCLUDE [Bookmark links to same article](sdk-code-examples.md)]

## <a name="add-the-dependencies"></a>Lägg till beroenden

Lägg till klient biblioteken i python-filen.

[!code-python[Add python libraries to code file](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=Dependencies)]


## <a name="add-boilerplate-code"></a>Lägg till en exempel kod

1. Lägg till- `quickstart` metoden och dess anrop. Den här metoden innehåller de flesta återstående koder. Den här metoden anropas i slutet av filen.

    ```python
    def quickstart():

        # add calls here, remember to indent properly

    quickstart()
    ```

1. Lägg till den återstående koden i snabb starts metoden om inget annat anges.

## <a name="create-variables-for-the-app"></a>Skapa variabler för appen

Skapa två uppsättningar variabler: den första uppsättningen som du ändrar, den andra anges i kod exemplet. 

1. Skapa variabler för att hålla dina redigerings nycklar och resurs namn.

    [!code-python[Variables you need to change](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=VariablesYouChange)]

1. Skapa variabler för att lagra dina slut punkter, app-namn, version och namn på avsikt.

    [!code-python[Variables you don't need to change](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=VariablesYouDontNeedToChangeChange)]

## <a name="authenticate-the-client"></a>Autentisera klienten

Skapa ett [CognitiveServicesCredentials](/python/api/msrest/msrest.authentication.cognitiveservicescredentials) -objekt med din nyckel och Använd den med slut punkten för att skapa ett [LUISAuthoringClient](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.luisauthoringclient) -objekt.

[!code-python[Authenticate the client](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>Skapa en LUIS-app

En LUIS-app innehåller NLP-modellen (Natural Language Processing) inklusive avsikter, entiteter och exempel yttranden.

Skapa appen genom att skapa ett [AppsOperation](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations) -objekts [Add](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations#add-application-create-object--custom-headers-none--raw-false----operation-config-) -metod. Namnet och språk kulturen är obligatoriska egenskaper.

[!code-python[Create a LUIS app](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=AuthoringCreateApplication)]


## <a name="create-intent-for-the-app"></a>Skapa avsikt för appen
Det primära objektet i en LUIS-Apps modell är avsikten. Avsikten med en gruppering av användarens _uttryck._ En användare kan ställa en fråga eller göra en instruktion som söker efter ett särskilt _avsett_ svar från en robot (eller något annat klient program). Exempel på avsikt är att boka en flygning och fråga om väder i en destinations ort och fråga om kontakt information för kund tjänst.

Använd metoden [Model.add_intent](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations#add-intent-app-id--version-id--name-none--custom-headers-none--raw-false----operation-config-) med namnet på den unika avsikten och skicka appens ID, VERSIONS-ID och nytt namn för avsikten.

`intentName`Värdet hårdkodas till `OrderPizzaIntent` som en del av variablerna i avsnittet [Skapa variabler för appen](#create-variables-for-the-app) .

[!code-python[Create intent for the app](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=AddIntent)]

## <a name="create-entities-for-the-app"></a>Skapa entiteter för appen

Även om entiteter inte krävs finns de i de flesta appar. Entiteten extraherar information från användar uttryck, som krävs för att fullfil användarens avsikt. Det finns flera typer av [färdiga](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations#add-prebuilt-app-id--version-id--prebuilt-extractor-names--custom-headers-none--raw-false----operation-config-) och anpassade entiteter, var och en med sina egna DTO-modeller (data Transformation Object).  Vanliga fördefinierade entiteter som ska läggas till i din app är [Number](../luis-reference-prebuilt-number.md), [datetimeV2](../luis-reference-prebuilt-datetimev2.md), [geographyV2](../luis-reference-prebuilt-geographyv2.md), [ordinal](../luis-reference-prebuilt-ordinal.md).

Det är viktigt att veta att entiteter inte har marker ATS med ett avsikts syfte. De kan och används vanligt vis för många syften. Endast yttranden är markerade för ett visst, enskilt syfte.

Skapande metoder för entiteter ingår i [ModelOperations](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations) -klassen. Varje entitetstyp har sin egen DTO-modell (data Transformation Object).

Entitetens skapande kod skapar en enhet för maskin inlärning med underentiteter och funktioner som tillämpas på `Quantity` underentiteterna.

:::image type="content" source="../media/quickstart-sdk/machine-learned-entity.png" alt-text="En del skärm bild från portalen som visar att entiteten har skapats, en enhet för maskin inlärning med underentiteter och funktioner som används i underentiteterna &quot;kvantitet&quot;.":::

[!code-python[Create entities for the app](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=AuthoringAddEntities)]

Placera följande metod ovanför `quickstart` -metoden för att hitta underentitetens antal underentiteter för att tilldela funktionerna till den underentiteten.

[!code-python[Find subentity id](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=AuthoringSortModelObject)]

## <a name="add-example-utterance-to-intent"></a>Lägg till exempel uttryck till avsikt

Appen behöver exempel på yttranden för att kunna fastställa en uttryck för avsikt och extrahering av entiteter. Exemplen måste vara riktade mot en viss, enskild avsikt och ska markera alla anpassade entiteter. Fördefinierade entiteter behöver inte markeras.

Lägg till exempel yttranden genom att skapa en lista över [ExampleLabelObject](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.models.examplelabelobject) -objekt, ett objekt för varje exempel uttryck. Varje exempel bör markera alla entiteter med en ord lista med namn/värde-par för enhets namn och enhets värde. Enhet svärdet bör vara exakt så som det visas i texten i exemplet uttryck.

:::image type="content" source="../media/quickstart-sdk/labeled-example-machine-learned-entity.png" alt-text="En del skärm bild som visar den märkta uttryck i portalen. ":::

Anropa [exempel. Lägg till](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations) med app-ID, VERSIONS-ID och exemplet.

[!code-python[Add example utterance to intent](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=AuthoringAddLabeledExamples)]

## <a name="train-the-app"></a>Träna appen

När modellen har skapats måste LUIS-appen tränas för den här versionen av modellen. En utbildad modell kan användas i en [behållare](../luis-container-howto.md)eller [publiceras](../luis-how-to-publish-app.md) på mellanlagrings-eller produkt platserna.

Metoden [Train.train_version](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations#train-version-app-id--version-id--custom-headers-none--raw-false----operation-config-) behöver app-ID och VERSIONS-ID.

En mycket liten modell, till exempel den här snabb starten visar, kommer att träna mycket snabbt. Utbildning appen måste innehålla ett avsöknings anrop till [get_Status](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations#get-status-app-id--version-id--custom-headers-none--raw-false----operation-config-) -metoden för att fastställa när utbildningen har genomförts för program på produktions nivå. Svaret är en lista över [ModelTrainingInfo](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.models.modeltraininginfo) -objekt med separat status för varje objekt. Alla objekt måste lyckas för att träningen ska anses vara slutförd.

[!code-python[Train the app](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=TrainAppVersion)]

## <a name="publish-app-to-production-slot"></a>Publicera appen till produktions platsen

Publicera LUIS-appen med metoden [app. Publish](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations#publish-app-id--version-id-none--is-staging-false--custom-headers-none--raw-false----operation-config-) . Detta publicerar den aktuella utbildade versionen till den angivna platsen vid slut punkten. Klient programmet använder den här slut punkten för att skicka användarens yttranden för förutsägelse av avsikt och extrahering av enheter.

[!code-python[Publish app to production slot](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=PublishVersion)]

## <a name="authenticate-the-prediction-runtime-client"></a>Autentisera klienten för förutsägelse körning

Använd objektet autentiseringsuppgifter med din nyckel och Använd slut punkten för att skapa ett [LUISRuntimeClientConfiguration](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.luisruntimeclientconfiguration) -objekt.

[!INCLUDE [Caution about using authoring key](caution-authoring-key.md)]

[!code-python[Authenticate the prediction runtime client](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=PredictionCreateClient)]

## <a name="get-prediction-from-runtime"></a>Hämta förutsägelse från körning

Lägg till följande kod för att skapa begäran till förutsägelse körning.

Användaren uttryck är en del av [prediction_request](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.models.predictionrequest) -objektet.

Metoden **[get_slot_prediction](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations#get-slot-prediction-app-id--slot-name--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)** behöver flera parametrar, till exempel App-ID, plats namn och objektet förutsägelse begär Ande för att uppfylla begäran. De andra alternativen, till exempel verbose, Visa alla avsikter och loggen är valfria. Begäran returnerar ett [PredictionResponse](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.models.predictionresponse) -objekt.

[!code-python[Get prediction from runtime](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=QueryPredictionEndpoint)]

[!INCLUDE [Prediction JSON response](sdk-json.md)]

## <a name="run-the-application"></a>Kör programmet

Kör programmet med `python` kommandot på snabb starts filen.

```console
python authoring_and_predict.py
```
