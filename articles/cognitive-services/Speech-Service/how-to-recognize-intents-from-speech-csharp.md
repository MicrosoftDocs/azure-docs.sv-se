---
title: 'Identifiera avsikter från tal med hjälp av talet SDK C #'
titleSuffix: Azure Cognitive Services
description: I den här guiden får du lära dig att identifiera avsikter från tal med hjälp av tal-SDK för C#.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 93a3adf00203e317be912e3e72de7a3f7ca666c6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96001120"
---
# <a name="how-to-recognize-intents-from-speech-using-the-speech-sdk-for-c"></a>Identifiera avsikter från tal med hjälp av tal-SDK för C #

Cognitive Services [Speech SDK](speech-sdk.md) integreras med [language Understanding-tjänsten (Luis)](https://www.luis.ai/home) för att ge **avsikts igenkänning**. En avsikt är något som användaren vill göra: boka en flygning, titta på vädret eller ringa ett samtal. Användaren kan använda de termer som känns naturliga. Med Machine Learning kan LUIS mappa användar förfrågningar till de avsikter som du har definierat.

> [!NOTE]
> Ett LUIS-programmet definierar avsikter och entiteter som du vill identifiera. Det är separat från det C#-program som använder Speech-tjänsten. I den här artikeln betyder ”app” LUIS-appen medan ”program” innebär C#-koden.

I den här hand boken använder du talet SDK för att utveckla ett C#-konsol program som härleder intentor från användar yttranden via din enhets mikrofon. Du lär dig följande:

> [!div class="checklist"]
>
> - Skapa ett Visual Studio-projekt som refererar till Speech SDK NuGet-paketet
> - Skapa en tal konfiguration och få en avsikts igenkänning
> - Hämta modellen för LUIS-appen och lägga till de avsikter som du behöver
> - Ange språk för taligenkänning
> - Identifiera tal från en fil
> - Använd asynkron, händelsedriven kontinuerlig igenkänning

## <a name="prerequisites"></a>Förutsättningar

Se till att du har följande saker innan du börjar den här guiden:

- Ett LUIS-konto. Du kan skaffa ett kostnadsfritt via [LUIS-portalen](https://www.luis.ai/home).
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) (vilken utgåva som helst).

## <a name="luis-and-speech"></a>LUIS och tal

LUIS integreras med Speech-tjänsten för att identifiera avsikter från tal. Du behöver inte en Speech-tjänstprenumeration, bara LUIS.

LUIS använder tre typer av nycklar:

| Nyckeltyp  | Syfte                                               |
| --------- | ----------------------------------------------------- |
| Redigering | Gör att du kan skapa och ändra LUIS-appar program mässigt |
| Starter   | Gör att du kan testa ditt LUIS-program med enbart text   |
| Slutpunkt  | Auktoriserar åtkomst till en viss LUIS-app            |

Du behöver en slut punkts nyckel typ för den här guiden. Den här guiden använder LUIS-appen för hem automatisering, som du kan skapa genom att följa snabb starten för [välbyggda hem automatiserings program](../luis/luis-get-started-create-app.md) . Om du har skapat en LUIS app som du själv har skapat kan du använda den i stället.

När du skapar en LUIS-app genererar LUIS automatiskt en start nyckel så att du kan testa appen med hjälp av text frågor. Den här nyckeln aktiverar inte röst tjänst integrering och fungerar inte med den här guiden. Skapa en LUIS-resurs på Azure-instrumentpanelen och tilldela den till LUIS-appen. Du kan använda den kostnads fria prenumerations nivån för den här guiden.

När du har skapat LUIS-resursen på Azure-instrumentpanelen loggar du in på [Luis-portalen](https://www.luis.ai/home), väljer ditt program på sidan **Mina appar** och växlar sedan till appens **hanterings** sida. Slutligen väljer du **nycklar och slut punkter** i sido panelen.

![LUIS-portalens inställningar för nycklar och slutpunkter](media/sdk/luis-keys-endpoints-page.png)

På sidan **nycklar och inställningar för slut punkt** :

1. Rulla ned till avsnittet **resurser och nycklar** och välj **tilldela resurs**.
1. Gör följande ändringar i dialog rutan **tilldela en nyckel till din app** :

   - Under **klient** väljer du **Microsoft**.
   - Under **prenumerations namn** väljer du den Azure-prenumeration som innehåller den Luis-resurs som du vill använda.
   - Under **nyckel** väljer du den Luis-resurs som du vill använda med appen.

   Om en stund visas den nya prenumerationen i tabellen längst ned på sidan.

1. Välj ikonen bredvid en tangent för att kopiera den till Urklipp. (Du kan använda valfri nyckel.)

![Prenumerationsnycklar för LUIS-app](media/sdk/luis-keys-assigned.png)

## <a name="create-a-speech-project-in-visual-studio"></a>Skapa ett talprojekt i Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-the-code"></a>Lägga till koden

Sedan lägger du till kod i projektet.

1. Öppna filen **program. cs** från **Solution Explorer**.

1. Ersätt blocket med `using` instruktioner i början av filen med följande deklarationer:

   [!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#toplevel)]

1. Ersätt den angivna `Main()` metoden med följande asynkrona motsvarighet:

   ```csharp
   public static async Task Main()
   {
       await RecognizeIntentAsync();
       Console.WriteLine("Please press Enter to continue.");
       Console.ReadLine();
   }
   ```

1. Skapa en tom asynkron metod `RecognizeIntentAsync()` som visas här:

   ```csharp
   static async Task RecognizeIntentAsync()
   {
   }
   ```

1. I den här nya metodens brödtext lägger du till följande kod:

   [!code-csharp[Intent recognition by using a microphone](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithMicrophone)]

1. Ersätt platshållarna i den här metoden med din LUIS-prenumerationsnyckel, region och app-ID på följande sätt.

   | Platshållare | Ersätt med |
   | ----------- | ------------ |
   | `YourLanguageUnderstandingSubscriptionKey` | Din LUIS-slutpunktsnyckel. Återigen måste du hämta det här objektet från Azure-instrumentpanelen, inte en "Start nyckel". Du hittar den på sidan **nycklar och slut punkter** för appen (under **Hantera**) i [Luis-portalen](https://www.luis.ai/home). |
   | `YourLanguageUnderstandingServiceRegion` | Den korta identifieraren för den region som din LUIS-prenumeration är i, till exempel `westus` för USA, västra. Se [regioner](regions.md). |
   | `YourLanguageUnderstandingAppId` | LUIS-app-ID. Du hittar den på appens **inställnings** sida på Luis- [portalen](https://www.luis.ai/home). |

När de här ändringarna har gjorts kan du skapa (**CTRL + SHIFT + B**) och köra (**F5**) programmet. När du uppmanas kan du prova att säga "Stäng av ljuset" i DATORns mikrofon. Programmet visar resultatet i konsol fönstret.

Följande avsnitt innehåller en beskrivning av koden.

## <a name="create-an-intent-recognizer"></a>Skapa en avsiktsigenkännare

Först måste du skapa en tal konfiguration från LUIS slut punkts nyckel och region. Du kan använda tal konfigurationer för att skapa igenkänningar för de olika funktionerna i tal-SDK: n. Tal konfigurationen har flera olika sätt att ange den prenumeration som du vill använda. här använder vi `FromSubscription` , som tar prenumerations nyckeln och regionen.

> [!NOTE]
> Använd nyckel och region för din LUIS-prenumeration, inte en röst tjänst prenumeration.

Nästa steg är att skapa en avsiktsigenkännare med hjälp av `new IntentRecognizer(config)`. Eftersom konfigurationen redan vet vilken prenumeration som ska användas, behöver du inte ange prenumerations nyckeln och slut punkten igen när du skapar tolken.

## <a name="import-a-luis-model-and-add-intents"></a>Importera en LUIS-modell och lägga till avsikter

Nu importerar du modellen från LUIS-appen med hjälp av `LanguageUnderstandingModel.FromAppId()` och lägger till de LUIS-avsikter som du vill känna igen igenkännarens `AddIntent()`-metod. De här två stegen förbättrar taligenkänningens noggrannhet genom att ange ord som det är troligt att användaren använder i sina begäranden. Du behöver inte lägga till alla appens avsikter om du inte behöver känna igen dem i ditt program.

Om du vill lägga till avsikter måste du ange tre argument: LUIS-modellen (som har skapats och är namngiven `model` ), namnet på avsikten och ett avsikts-ID. Skillnaden mellan ID och namnet är som följer.

| `AddIntent()`&nbsp;påstående | Syfte |
| --------------------------- | ------- |
| `intentName` | Namnet på avsikten enligt vad som definierats i LUIS-appen. Det här värdet måste matcha namnet på LUIS-avsikten exakt. |
| `intentID` | Ett ID som tilldelas till en igenkänd avsikt av Speech SDK. Det här värdet kan vara vad du vill. Det behöver inte motsvara det namn på avsikten som definierats i LUIS-appen. Om flera avsikter till exempel hanteras av samma kod kan du använda samma ID för dem. |

LUIS-appen för hem automatisering har två avsikter: en för att aktivera en enhet och en annan för att stänga av en enhet. Raderna nedan lägger till dessa avsikter till igenkännaren. Ersätt de tre `AddIntent`-raderna i metoden `RecognizeIntentAsync()` med den här koden.

```csharp
recognizer.AddIntent(model, "HomeAutomation.TurnOff", "off");
recognizer.AddIntent(model, "HomeAutomation.TurnOn", "on");
```

I stället för att lägga till enskilda avsikter kan du också använda `AddAllIntents` metoden för att lägga till alla syften i en modell i identifieraren.

## <a name="start-recognition"></a>Starta igenkänning

När igenkännaren har skapats och avsikterna lagts till kan igenkänningen börja. Speech SDK stöder både engångsigenkänning och kontinuerlig igenkänning.

| Igenkänningsläge | Metoder att anropa | Resultat |
| ---------------- | --------------- | ------ |
| Engångsigenkänning | `RecognizeOnceAsync()` | Returnerar den igenkända avsikten, om sådan finns, efter ett yttrande. |
| Kontinuerlig igenkänning | `StartContinuousRecognitionAsync()`<br>`StopContinuousRecognitionAsync()` | Känner igen flera yttranden; genererar händelser (till exempel `IntermediateResultReceived` ) när resultatet är tillgängligt. |

Programmet använder ett enda bilds läge och anropar `RecognizeOnceAsync()` för att börja känna igen. Resultatet är ett `IntentRecognitionResult`-objekt som innehåller information om den igenkända avsikten. Du extraherar LUIS JSON-svaret med hjälp av följande uttryck:

```csharp
result.Properties.GetProperty(PropertyId.LanguageUnderstandingServiceResponse_JsonResult)
```

Programmet kan inte parsa JSON-resultatet. Den visar bara JSON-texten i konsol fönstret.

![Resultat för enkel LUIS-igenkänning](media/sdk/luis-results.png)

## <a name="specify-recognition-language"></a>Ange igenkänningsspråk

Som standard känner LUIS igen avsikter på amerikansk engelska (`en-us`). Genom att tilldela en kod för nationella inställningar till egenskapen `SpeechRecognitionLanguage` i talkonfigurationen kan du känna igen avsikter på andra språk. Du kan till exempel lägga till `config.SpeechRecognitionLanguage = "de-de";` i vårt program innan du skapar tolken för att identifiera avsikter på tyska. Mer information finns i [språk stöd för Luis](../LUIS/luis-language-support.md#languages-supported).

## <a name="continuous-recognition-from-a-file"></a>Kontinuerlig igenkänning från en fil

Följande kod illustrerar två ytterligare funktioner för avsiktsigenkänning med hjälp av Speech SDK. Den första, som nämnts tidigare, är kontinuerlig igenkänning, där igenkännaren genererar händelser när resultat finns tillgängliga. Dessa händelser kan sedan bearbetas av händelsehanterare som du anger. Med kontinuerlig igenkänning anropar du igenkännings `StartContinuousRecognitionAsync()` metoden för att starta igenkänning i stället för `RecognizeOnceAsync()` .

Den andra funktionen är att läsa det ljud som innehåller det tal som ska bearbetas från en WAV-fil. Implementeringen innebär att skapa en ljud konfiguration som kan användas när du skapar typen av avsikts igenkänning. Filen måste vara enkanalig (mono) med en samplingsfrekvens på 16 kHz.

Om du vill testa dessa funktioner tar du bort eller kommenterar bröd texten i `RecognizeIntentAsync()` metoden och lägger till följande kod i dess ställe.

[!code-csharp[Intent recognition by using events from a file](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentContinuousRecognitionWithFile)]

Ändra koden så att den innehåller din LUIS-slutpunktsnyckel, region och app-ID för att lägga till Home Automation-avsikterna som tidigare. Ändra `whatstheweatherlike.wav` till namnet på den inspelade ljud filen. Sedan kan du skapa, kopiera ljud filen till build-katalogen och köra programmet.

Om du t. ex. säger att "Stäng av ljuset", pausar och sedan "slå på lamporna" i din inspelade ljud fil, kan konsolens utdata som liknar följande visas:

![LUIS igenkännings resultat för ljud filen](media/sdk/luis-results-2.png)

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Sök efter koden från den här artikeln i mappen **samples/csharp/sharedcontent/Console** .

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Snabb start: identifiera tal från en mikrofon](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore)