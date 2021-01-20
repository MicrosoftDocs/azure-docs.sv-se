---
title: Om talet för tal-SDK – tal
titleSuffix: Azure Cognitive Services
description: Talfunktionerna Software Development Kit (SDK) visar många av funktionerna i tal tjänsten, vilket gör det enklare att utveckla tal aktiverade program.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: 597a7c3954912a3a86b5db9f92ba75e8aaee63f6
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2021
ms.locfileid: "98601823"
---
# <a name="about-the-speech-sdk"></a>Om Speech SDK

Talfunktionerna Software Development Kit (SDK) visar många av funktionerna i tal tjänsten, så att du kan utveckla talbaserade program. Talet SDK är tillgängligt i många programmeringsspråk och på alla plattformar.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

## <a name="scenario-capabilities"></a>Scenario funktioner

Tal-SDK: n visar många funktioner från tal tjänsten, men inte alla. Funktionerna i tal-SDK: n är ofta kopplade till scenarier. Tal-SDK: n är idealisk för både real tids scenarier och icke-real tids scenarier, med hjälp av lokala enheter, filer, Azure Blob Storage och till och med indata och utgående data strömmar. När det inte går att nå ett scenario med talet SDK kan du leta efter ett REST API alternativ.

### <a name="speech-to-text"></a>Tal till text

[Tal till text](speech-to-text.md) (kallas även *tal igenkänning*) drar av ljud strömmar till text som dina program, verktyg eller enheter kan använda eller Visa. Använd tal-till-text med [language Understanding (Luis)](../luis/index.yml) för att härleda användar avsikter från inställt tal och agera på röst kommandon. Använd [tal översättning](speech-translation.md) för att översätta tal ingångar till ett annat språk med ett enda anrop. Mer information finns i [grunderna för tal till text](./get-started-speech-to-text.md).

**Tal igenkänning (SR), fras lista, avsikt, översättning och lokala behållare** är tillgängliga på följande plattformar:

  - C++/Windows & Linux & macOS
  - C# (Framework & .NET Core)/Windows & UWP & Unity & Xamarin & Linux & macOS
  - Java (JRE och Android)
  - Java Script (webbläsaren och NodeJS)
  - Python
  - Swift
  - Objective-C  
  - Gå till (endast SR)

### <a name="text-to-speech"></a>Text till tal

[Text till tal](text-to-speech.md) (kallas även *tal syntes*) konverterar text till humant syntetiskt tal. Inmatad text är antingen sträng litteraler eller med [SSML (Speech syntes Markup Language)](speech-synthesis-markup.md). Mer information om standard-och neurala-röster finns i [text-till-tal-språk och röst stöd](language-support.md#text-to-speech).

**Text till tal (TTS)** är tillgänglig på följande plattformar:

  - C++/Windows & Linux
  - C#/Windows & UWP & Unity
  - Java (JRE och Android)
  - Python
  - Swift
  - Objective-C
  - TTS-REST API kan användas i alla andra situationer.

### <a name="voice-assistants"></a>Röstassistenter

[Röst assistenter](voice-assistants.md) som använder tal-SDK gör det möjligt för utvecklare att skapa naturliga, mänskliga konversations gränssnitt för sina program och upplevelser. Röst assistents tjänsten ger snabb och tillförlitlig interaktion mellan en enhet och en assistent. I implementeringen används bot Framework: s direkta rad tal kanal eller den integrerade anpassade kommando tjänsten för att slutföra uppgiften. Dessutom kan röst assistenter använda anpassade röster som skapats i den [anpassade röst portalen](https://aka.ms/customvoice) för att lägga till en unik röst utmatnings upplevelse.

**Röst assistenter** är tillgängliga på följande plattformar:

  - C++/Windows & Linux & macOS
  - C#/Windows
  - Java/Windows & Linux & macOS & Android (tal enheter SDK)

#### <a name="keyword-spotting"></a>Nyckelord upptäcka

Konceptet för [nyckelordet upptäcka](./custom-keyword-basics.md) stöds i tal-SDK: n. Nyckelordet upptäcka är en handling som identifierar ett nyckelord i tal, följt av en åtgärd för att höra nyckelordet. Till exempel "Hej Cortana" aktiverar Cortana-assistenten.

**Nyckelordet upptäcka (KWS)** är tillgängligt på följande plattformar:

  - C++/Windows & Linux
  - C#/Windows & Linux
  - Python/Windows & Linux
  - Java/Windows & Linux & Android (Speech-enheter SDK)
  - Nyckelordet upptäcka (KWS) fungerar med valfri mikrofon typ, officiell KWS support, men är för närvarande begränsad till de mikrofoner som finns i Azure Kinect DK-maskinvara eller tal enheter SDK

### <a name="meeting-scenarios"></a>Mötes scenarier

Tal-SDK är perfekt för att skriva över Mötes scenarier, oavsett om det är en enskild enhet eller en konversation med flera enheter.

#### <a name="conversation-transcription"></a>Samtalstranskription

[Konversations avskrift](conversation-transcription.md) möjliggör real tids (och asynkron) tal igenkänning, högtalar identifiering och menings tilldelning till varje talare (kallas även *diarization*). Det är perfekt för att skriva över person möten med möjlighet att skilja på högtalare.

**Konversations avskrift** är tillgängligt på följande plattformar:

  - C++/Windows & Linux
  - C# (Framework & .NET Core)/Windows & UWP & Linux
  - Java/Windows & Linux & Android (Speech-enheter SDK)

#### <a name="multi-device-conversation"></a>Konversation med flera enheter

Med en [konversation med flera enheter](multi-device-conversation.md)ansluter du flera enheter eller klienter i en konversation för att skicka talbaserade eller textbaserade meddelanden, med enkel support för avskrifter och översättning.

**Konversation med flera enheter** är tillgängligt på följande plattformar:

  - C++-/Windows
  - C# (Framework & .NET Core)/Windows

### <a name="custom--agent-scenarios"></a>Anpassade/agent-scenarier

Tal-SDK: n kan användas för att skriva över samtals Center scenarier där telefoni data genereras.

#### <a name="call-center-transcription"></a>Avskrifter av kundtjänst

[Avskrift av samtals Center](call-center-transcription.md) är ett vanligt scenario för tal till text för att skriva över stora volymer av telefoni data som kan komma från olika system, till exempel interaktivt röst svar (IVR). De senaste tal igenkännings modellerna från tal tjänsten Excel vid inmatning av dessa telefoni data, även i fall då det är svårt för en mänsklig att förstå.

**Svars Center-avskriften** är tillgänglig via batch-tjänsten för tal trafik via den REST API och kan användas i alla situationer.

### <a name="codec-compressed-audio-input"></a>Codec-komprimerad ljud inspelning

Flera av programmeringsspråken för tal-SDK stöder codec-komprimerade ljud inspelnings strömmar. Mer information finns i <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams" target="_blank">använda komprimerade ljud inspelnings <span class="docon docon-navigate-external x-hidden-focus"></span> format </a>.

**Codec-komprimerade ljud indata** är tillgängliga på följande plattformar:

  - C++-/Linux
  - C#-/Linux
  - Java/Linux, Android och iOS

## <a name="rest-api"></a>REST-API

Även om tal-SDK: n täcker många funktioner i tal tjänsten kan du använda REST API för vissa scenarier.

### <a name="batch-transcription"></a>Batch-transkription

Med [batch-avskrifter](batch-transcription.md) kan du använda asynkrona tal-till-text-avskrifter av stora data volymer. Batch-avskrift är bara möjlig från REST API. Förutom att konvertera tal ljud till text, tillåter batch-tal-till-text också diarization och sentiment-analys.

## <a name="customization"></a>Anpassning

Tal tjänsten ger fantastiska funktioner med sina standard modeller i tal-till-text, text till tal och tal översättning. Ibland kanske du vill öka bas linje prestandan så att den fungerar ännu bättre med ditt unika användnings fall. Tal tjänsten har en rad olika anpassnings verktyg som gör det enkelt och ger dig möjlighet att skapa en konkurrens för del med anpassade modeller som baseras på dina egna data. Dessa modeller är bara tillgängliga för dig och din organisation.

### <a name="custom-speech-to-text"></a>Custom Speech till text

När du använder tal-till-text för igenkänning och avskriftering i en unik miljö kan du skapa och träna anpassade ljud-, språk-och uttals modeller för att hantera omgivande brus eller branschspecifika vokabulär. Det går inte att skapa och hantera Custom Speechs modeller utan kod på [Custom Speech portalen](https://aka.ms/customspeech). När Custom Speechs modellen har publicerats kan den användas av talet SDK.

### <a name="custom-text-to-speech"></a>Anpassad text till tal

Anpassad text till tal, även kallat anpassad röst är en uppsättning online-verktyg som du kan använda för att skapa en identifierbar och en-av-en-röst för ditt varumärke. Det går inte att skapa och hantera anpassade röst modeller som är tillgängliga via den [anpassade röst portalen](https://aka.ms/customvoice). När den anpassade röst modellen har publicerats kan den förbrukas av talet SDK.

## <a name="get-the-speech-sdk"></a>Hämta tal-SDK

# <a name="windows"></a>[Windows](#tab/windows)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-windows.md)]

# <a name="linux"></a>[Linux](#tab/linux)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-linux.md)]

# <a name="ios"></a>[iOS](#tab/ios)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-ios.md)]

# <a name="macos"></a>[macOS](#tab/macos)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-macos.md)]

# <a name="android"></a>[Android](#tab/android)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-android.md)]

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

[!INCLUDE [Get the Node.js Speech SDK](includes/get-speech-sdk-nodejs.md)]

# <a name="browser"></a>[Webbläsare](#tab/browser)

[!INCLUDE [Get the Browser Speech SDK](includes/get-speech-sdk-browser.md)]

---

[!INCLUDE [License notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

[!INCLUDE [Sample source code](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Nästa steg

* [Skapa ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/cognitive-services/)
* [Se känna igen tal i C #](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnet)