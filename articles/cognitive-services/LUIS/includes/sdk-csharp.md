---
title: ta med fil
description: ta med fil
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 09/01/2020
ms.topic: include
ms.custom: include file, devx-track-dotnet, cog-serv-seo-aug-2020
ms.openlocfilehash: fd47d5df053931c343fd811fe4d93b66f080f225
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98948653"
---
Använd klient biblioteken Language Understanding (LUIS) för .NET för att:
* Skapa en app
* Lägg till en avsikt, en enhet som har lärts in, med ett exempel uttryck
* Träna och publicera appen
* CLR för frågeförutsägelse

[Referens dokumentation](/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding)  |  [Redigera](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Authoring) och [förutsäga](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Runtime) bibliotekets käll kod | [Redigera](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/) och [förutsäga](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/) NuGet | [C#-exempel](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/LanguageUnderstanding/sdk-3x//Program.cs)

## <a name="prerequisites"></a>Förutsättningar

* Den aktuella versionen av [.net Core](https://dotnet.microsoft.com/download/dotnet-core) och [.net Core CLI](/dotnet/core/tools/).
* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services)
* När du har en Azure-prenumeration [skapar du en language Understanding Authoring-resurs](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) i Azure Portal för att hämta din nyckel och slut punkt. Vänta tills den har distribuerats och klicka på knappen **gå till resurs** .
    * Du behöver nyckeln och slut punkten från den resurs som du [skapar](../luis-how-to-azure-subscription.md#create-luis-resources-in-the-azure-portal) för att ansluta ditt program till language Understanding redigering. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten. Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten.

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-new-c-application"></a>Skapa ett nytt C#-program

Skapa ett nytt .NET Core-program i önskat redigerings program eller IDE.

1. I ett konsol fönster (till exempel cmd, PowerShell eller bash) använder du `new` kommandot dotNet för att skapa en ny konsol app med namnet `language-understanding-quickstart` . Det här kommandot skapar ett enkelt "Hello World" C#-projekt med en enda käll fil: `Program.cs` .

    ```dotnetcli
    dotnet new console -n language-understanding-quickstart
    ```

1. Ändra katalogen till mappen nyligen skapade appar.

    ```dotnetcli
    cd language-understanding-quickstart
    ```

1. Du kan bygga programmet med:

    ```dotnetcli
    dotnet build
    ```

    Build-utdata får inte innehålla varningar eller fel.

    ```console
    ...
    Build succeeded.
     0 Warning(s)
     0 Error(s)
    ...
    ```

### <a name="install-the-nuget-libraries"></a>Installera NuGet-biblioteken

I program katalogen installerar du klient biblioteken Language Understanding (LUIS) för .NET med följande kommandon:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring --version 3.2.0-preview.3
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime --version 3.1.0-preview.1
```

## <a name="authoring-object-model"></a>Redigera objekt modell

Redigerings klienten för Language Understanding (LUIS) är ett [LUISAuthoringClient](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient) -objekt som autentiserar till Azure, som innehåller din redigerings nyckel.

## <a name="code-examples-for-authoring"></a>Kod exempel för redigering

När klienten har skapats använder du den här klienten för att få åtkomst till funktioner, inklusive:

* Appar- [skapa](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync), [ta bort](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.deleteasync), [publicera](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync)
* Exempel yttranden – [Lägg till](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions), [ta bort med ID](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.deleteasync)
* Funktioner – hantera [fras listor](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.featuresextensions.addphraselistasync)
* Modell-hantera [avsikter](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions) och entiteter
* Mönster – hantera [mönster](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.patternextensions)
* Träna – [träna](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.trainversionasync) appen och avsökningen efter [kurs status](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync)
* [Versioner](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.versionsextensions) – hantera med klona, exportera och ta bort

## <a name="prediction-object-model"></a>Förutsägelse objekt modell

Den Language Understanding (LUIS) förutsägelse runtime-klienten är ett [LUISRuntimeClient](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient) -objekt som autentiserar till Azure, som innehåller din resurs nyckel.

## <a name="code-examples-for-prediction-runtime"></a>Kod exempel för förutsägelse körning

När klienten har skapats använder du den här klienten för att få åtkomst till funktioner, inklusive:

* Förutsägelse per [mellanlagrings-eller produktions plats](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getslotpredictionasync)
* Förutsägelse efter [version](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getversionpredictionasync)


[!INCLUDE [Bookmark links to same article](sdk-code-examples.md)]

## <a name="add-the-dependencies"></a>Lägg till beroenden

Från projekt katalogen öppnar du *program.cs* -filen i önskat redigerings program eller IDE. Ersätt den befintliga `using` koden med följande `using` direktiv:

[!code-csharp[Add NuGet libraries to code file](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=Dependencies)]

## <a name="add-boilerplate-code"></a>Lägg till en exempel kod

1. Ändra signaturen för `Main` metoden så att asynkrona anrop tillåts:

    ```csharp
    public static async Task Main()
    ```

1. Lägg till resten av koden i- `Main` metoden för `Program` klassen om inget annat anges.

## <a name="create-variables-for-the-app"></a>Skapa variabler för appen

Skapa två uppsättningar variabler: den första uppsättningen som du ändrar, den andra anges i kod exemplet. 

1. Skapa variabler för att hålla dina redigerings nycklar och resurs namn.

    [!code-csharp[Variables you need to change](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=VariablesYouChange)]

1. Skapa variabler för att lagra dina slut punkter, app-namn, version och namn på avsikt.

    [!code-csharp[Variables you don't need to change](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=VariablesYouDontNeedToChangeChange)]

## <a name="authenticate-the-client"></a>Autentisera klienten

Skapa ett [ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.apikeyserviceclientcredentials) -objekt med din nyckel och Använd den med slut punkten för att skapa ett [LUISAuthoringClient](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient) -objekt.

[!code-csharp[Authenticate the client](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>Skapa en LUIS-app

En LUIS-app innehåller NLP-modellen (Natural Language Processing) inklusive avsikter, entiteter och exempel yttranden.

Skapa en [ApplicationCreateObject](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.applicationcreateobject). Namnet och språk kulturen är obligatoriska egenskaper. Anropa metoden [Apps. AddAsync](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync) . Svaret är app-ID: t.

[!code-csharp[Create a LUIS app](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=AuthoringCreateApplication)]

## <a name="create-intent-for-the-app"></a>Skapa avsikt för appen
Det primära objektet i en LUIS-Apps modell är avsikten. Avsikten med en gruppering av användarens uttryck- _avsikter_. En användare kan ställa en fråga eller göra en instruktion som söker efter ett särskilt _avsett_ svar från en robot (eller något annat klient program). Exempel på avsikt är att boka en flygning och fråga om väder i en destinations ort och fråga om kontakt information för kund tjänst.

Skapa en [ModelCreateObject](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modelcreateobject) med namnet på den unika avsikten och skicka sedan app-ID, VERSIONS-ID och ModelCreateObject till metoden [Model. AddIntentAsync](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addintentasync) . Svaret är det ID för avsikten.

`intentName`Värdet hårdkodas till `OrderPizzaIntent` som en del av variablerna i avsnittet [Skapa variabler för appen](#create-variables-for-the-app) .

[!code-csharp[Create intent for the app](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=AddIntent)]

## <a name="create-entities-for-the-app"></a>Skapa entiteter för appen

Även om entiteter inte krävs finns de i de flesta appar. Entiteten extraherar information från användar uttryck, som krävs för att fullfil användarens avsikt. Det finns flera typer av [färdiga](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addprebuiltasync) och anpassade entiteter, var och en med sina egna DTO-modeller (data Transformation Object).  Vanliga fördefinierade entiteter som ska läggas till i din app är [Number](../luis-reference-prebuilt-number.md), [datetimeV2](../luis-reference-prebuilt-datetimev2.md), [geographyV2](../luis-reference-prebuilt-geographyv2.md), [ordinal](../luis-reference-prebuilt-ordinal.md).

Det är viktigt att veta att entiteter inte har marker ATS med ett avsikts syfte. De kan och används vanligt vis för många syften. Endast det yttranden som är markerat för en viss enskild avsikt.

Skapande metoder för entiteter ingår i [modell](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions) klassen. Varje entitetstyp har en egen DTO-modell (data Transformation Object) som vanligt vis innehåller ordet `model` i namn området [modeller](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models) .

Entitetens skapande kod skapar en enhet för maskin inlärning med underentiteter och funktioner som tillämpas på `Quantity` underentiteterna.

:::image type="content" source="../media/quickstart-sdk/machine-learned-entity.png" alt-text="En del skärm bild från portalen som visar att entiteten har skapats, en enhet för maskin inlärning med underentiteter och funktioner som används i underentiteterna &quot;kvantitet&quot;.":::

[!code-csharp[Create entities for the app](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=AuthoringAddEntities)]

Använd följande metod för att hitta underentitetens antal underentiteter för att tilldela funktionerna till den underentiteten.

[!code-csharp[Find subentity id](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=AuthoringSortModelObject)]

## <a name="add-example-utterance-to-intent"></a>Lägg till exempel uttryck till avsikt

Appen behöver exempel på yttranden för att kunna fastställa en uttryck för avsikt och extrahering av entiteter. Exemplen måste vara riktade mot en viss, enskild avsikt och ska markera alla anpassade entiteter. Fördefinierade entiteter behöver inte markeras.

Lägg till exempel yttranden genom att skapa en lista över [ExampleLabelObject](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.examplelabelobject) -objekt, ett objekt för varje exempel uttryck. Varje exempel bör markera alla entiteter med en ord lista med namn/värde-par för enhets namn och enhets värde. Enhet svärdet bör vara exakt så som det visas i texten i exemplet uttryck.

:::image type="content" source="../media/quickstart-sdk/labeled-example-machine-learned-entity.png" alt-text="En del skärm bild som visar den märkta uttryck i portalen. ":::

Anropa [exempel. AddAsync](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions) med app-ID, VERSIONS-ID och exempel.

[!code-csharp[Add example utterance to intent](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=AuthoringAddLabeledExamples)]

## <a name="train-the-app"></a>Träna appen

När modellen har skapats måste LUIS-appen tränas för den här versionen av modellen. En utbildad modell kan användas i en [behållare](../luis-container-howto.md)eller [publiceras](../luis-how-to-publish-app.md) på mellanlagrings-eller produkt platserna.

Metoden [Train. TrainVersionAsync](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions) kräver app-ID och VERSIONS-ID.

En mycket liten modell, till exempel den här snabb starten visar, kommer att träna mycket snabbt. Utbildning appen måste innehålla ett avsöknings anrop till [GetStatusAsync](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync#Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_TrainExtensions_GetStatusAsync_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_ITrain_System_Guid_System_String_System_Threading_CancellationToken_) -metoden för att fastställa när en utbildning har genomförts för program på produktions nivå. Svaret är en lista över [ModelTrainingInfo](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modeltraininginfo) -objekt med separat status för varje objekt. Alla objekt måste lyckas för att träningen ska anses vara slutförd.

[!code-csharp[Train the app](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=TrainAppVersion)]

## <a name="publish-app-to-production-slot"></a>Publicera appen till produktions platsen

Publicera LUIS-appen med [PublishAsync](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync) -metoden. Detta publicerar den aktuella utbildade versionen till den angivna platsen vid slut punkten. Klient programmet använder den här slut punkten för att skicka användarens yttranden för förutsägelse av avsikt och extrahering av enheter.

[!code-csharp[Publish app to production slot](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=PublishVersion)]

## <a name="authenticate-the-prediction-runtime-client"></a>Autentisera klienten för förutsägelse körning

Använd ett [ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.apikeyserviceclientcredentials) -objekt med din nyckel och Använd den med slut punkten för att skapa ett [LUISRuntimeClient](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient) -objekt.

[!INCLUDE [Caution about using authoring key](caution-authoring-key.md)]

[!code-csharp[Authenticate the prediction runtime client](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=PredictionCreateClient)]


## <a name="get-prediction-from-runtime"></a>Hämta förutsägelse från körning

Lägg till följande kod för att skapa begäran till förutsägelse körning.

Användaren uttryck är en del av [PredictionRequest](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.models.predictionrequest) -objektet.

Metoden **GetSlotPredictionAsync** kräver flera parametrar, till exempel App-ID, plats namn, objekt för förutsägelse begär Ande för att uppfylla begäran. De andra alternativen, till exempel verbose, Visa alla avsikter och loggen är valfria.

[!code-csharp[Get prediction from runtime](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=QueryPredictionEndpoint)]

[!INCLUDE [Prediction JSON response](sdk-json.md)]

## <a name="run-the-application"></a>Kör programmet

Kör programmet med `dotnet run` kommandot från program katalogen.

```dotnetcli
dotnet run
```
