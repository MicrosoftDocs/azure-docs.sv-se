---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2020
ms.author: trbye
ms.openlocfilehash: 7b1bc00e54abed70d4cbb769e15ff0f304c62b85
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108887"
---
I den här snabb starten lär du dig vanliga design mönster för att skapa text till tal-Sammanfattning med hjälp av tal-SDK. Du börjar med att utföra grundläggande konfiguration och syntes och går vidare till mer avancerade exempel för anpassad program utveckling, inklusive:

* Få svar som minnes strömmar
* Anpassa samplings frekvens och bit hastighet för utdata
* Skicka syntes begär Anden med SSML (tal syntes Markup Language)
* Använda neurala-röster

## <a name="skip-to-samples-on-github"></a>Hoppa till exempel på GitHub

Om du vill hoppa över direkt till exempel kod, se [snabb starts exemplen för C++](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows/text-to-speech) på GitHub.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du har ett Azure-konto och en röst tjänst prenumeration. Om du inte har ett konto och en prenumeration kan du [prova att använda tal tjänsten kostnads fritt](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Installera Speech SDK

Innan du kan göra något måste du installera talet SDK. Använd följande instruktioner, beroende på plattform:

* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-cpp&tabs=linux" target="_blank">Linux </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-cpp&tabs=macos" target="_blank">macOS </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-cpp&tabs=windows" target="_blank">Aktivitets </a>

## <a name="import-dependencies"></a>Importera beroenden

Om du vill köra exemplen i den här artikeln inkluderar du följande import-och- `using` instruktioner överst i skriptet.

```cpp
#include <iostream>
#include <fstream>
#include <string>
#include <speechapi_cxx.h>

using namespace std;
using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Audio;
```

## <a name="create-a-speech-configuration"></a>Skapa en tal konfiguration

Om du vill anropa tal tjänsten med hjälp av tal-SDK måste du skapa en [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig) . Den här klassen innehåller information om din prenumeration, till exempel din nyckel och tillhör ande region, slut punkt, värd eller token för auktorisering.

> [!NOTE]
> Oavsett om du utför tal igenkänning, tal syntes, översättning eller avsikts igenkänning, skapar du alltid en konfiguration.

Det finns några sätt som du kan initiera en [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig) :

* Med en prenumeration: skicka i en nyckel och tillhör ande region.
* Med en slut punkt: skicka i en röst tjänst slut punkt. En nyckel eller autentiseringstoken är valfri.
* Med en värd: skicka in en värd adress. En nyckel eller autentiseringstoken är valfri.
* Med en autentiseringstoken: skicka in en autentiseringstoken och den associerade regionen.

I det här exemplet skapar du en [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig) med en prenumerations nyckel och en region. Hämta dessa autentiseringsuppgifter genom att följa stegen i [testa tal tjänsten kostnads fritt](../../../overview.md#try-the-speech-service-for-free). Du kan också skapa en grundläggande exempel kod som du kan använda för resten av den här artikeln, som du ändrar för olika anpassningar.

```cpp
int wmain()
{
    try
    {
        synthesizeSpeech();
    }
    catch (exception e)
    {
        cout << e.what();
    }
    return 0;
}

void synthesizeSpeech()
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
}
```

## <a name="synthesize-speech-to-a-file"></a>Syntetisera tal till en fil

Därefter skapar du ett [`SpeechSynthesizer`](/cpp/cognitive-services/speech/speechsynthesizer) -objekt som kör text till tal-konverteringar och-utdata till högtalare, filer eller andra utgående strömmar. Det [`SpeechSynthesizer`](/cpp/cognitive-services/speech/speechsynthesizer) accepterar som param [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig) -objektet som skapades i föregående steg och ett [`AudioConfig`](/cpp/cognitive-services/speech/audio-audioconfig) objekt som anger hur resultat ska hanteras.

Starta genom att skapa en `AudioConfig` för att automatiskt skriva utdata till en `.wav` fil med hjälp av `FromWavFileOutput()` funktionen.

```cpp
void synthesizeSpeech()
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    auto audioConfig = AudioConfig::FromWavFileOutput("path/to/write/file.wav");
}
```

Sedan instansierar `SpeechSynthesizer` du ett, skickar ditt `config` objekt och `audioConfig` objektet som parametrar. Sedan är det lika enkelt att köra tal syntes och skrivning till en fil som att köra `SpeakTextAsync()` med en text sträng.

```cpp
void synthesizeSpeech()
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    auto audioConfig = AudioConfig::FromWavFileOutput("path/to/write/file.wav");
    auto synthesizer = SpeechSynthesizer::FromConfig(config, audioConfig);
    auto result = synthesizer->SpeakTextAsync("A simple test to write to a file.").get();
}
```

Kör programmet och en syntetisk `.wav` fil skrivs till den plats som du har angett. Detta är ett användbart exempel på den mest grundläggande användningen, men nästa du tittar på anpassningen av utdata och hantering av utdata som en minnes intern ström för att arbeta med anpassade scenarier.

## <a name="synthesize-to-speaker-output"></a>Syntetisera till talare-utdata

I vissa fall kanske du vill direkt mata ut syntetiskt tal direkt till en talare. Det gör du genom att utelämna `AudioConfig` parametern när du skapar `SpeechSynthesizer` i exemplet ovan. Den här syntetiseran till den aktuella aktiva utmatnings enheten.

```cpp
void synthesizeSpeech()
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    auto synthesizer = SpeechSynthesizer::FromConfig(config);
    auto result = synthesizer->SpeakTextAsync("Synthesizing directly to speaker output.").get();
}
```

## <a name="get-result-as-an-in-memory-stream"></a>Få resultat som en minnes intern ström

För många scenarier i tal program utveckling behöver du förmodligen resulterande ljud data som en minnes intern ström i stället för att skriva direkt till en fil. Detta gör att du kan bygga anpassade funktioner, inklusive:

* Abstrakt den resulterande byte mat ris som en sökbar data ström för anpassade underordnade tjänster.
* Integrera resultatet med andra API: er eller tjänster.
* Ändra ljud data, skriv anpassade `.wav` rubriker osv.

Det är enkelt att göra den här ändringen från föregående exempel. Ta först bort `AudioConfig` , eftersom du kommer att hantera utmatnings beteendet manuellt från den här punkten och därefter för ökad kontroll. Pass sedan `NULL` efter `AudioConfig` i `SpeechSynthesizer` konstruktorn.

> [!NOTE]
> `NULL`Att skicka till `AudioConfig` , i stället för att utesluta det som i exemplet ovan, spelar inte upp ljudet som standard på den aktuella aktiva utmatnings enheten.

Den här gången sparar du resultatet i en [`SpeechSynthesisResult`](/cpp/cognitive-services/speech/speechsynthesisresult) variabel. Get-filen `GetAudioData` returnerar en `byte []` av utdata. Du kan arbeta med detta `byte []` manuellt, eller så kan du använda- [`AudioDataStream`](/cpp/cognitive-services/speech/audiodatastream) klassen för att hantera minnes minnes minnes strömmar. I det här exemplet använder du den `AudioDataStream.FromResult()` statiska funktionen för att hämta en ström från resultatet.

```cpp
void synthesizeSpeech()
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    auto synthesizer = SpeechSynthesizer::FromConfig(config, NULL);

    auto result = synthesizer->SpeakTextAsync("Getting the response as an in-memory stream.").get();
    auto stream = AudioDataStream::FromResult(result);
}
```

Härifrån kan du implementera anpassade beteenden med det resulterande `stream` objektet.

## <a name="customize-audio-format"></a>Anpassa ljud format

I följande avsnitt visas hur du anpassar attribut för ljud uppspelning, inklusive:

* Ljud fils typ
* Samplings frekvens
* Bitdjup

Om du vill ändra ljud formatet använder du `SetSpeechSynthesisOutputFormat()` funktionen på `SpeechConfig` objektet. Den här funktionen förväntar sig en `enum` typ [`SpeechSynthesisOutputFormat`](/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#speechsynthesisoutputformat) som du kan använda för att välja utdataformatet. Se referens dokumenten för en [lista över tillgängliga ljud format](/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#speechsynthesisoutputformat) .

Det finns olika alternativ för olika filtyper beroende på dina krav. Observera att RAW-format, t `Raw24Khz16BitMonoPcm` . ex., inte innehåller ljud rubriker enligt definitionen. Använd RAW-format endast när du vet att den underordnade implementeringen kan avkoda en RAW-Bitstream, eller om du planerar att skapa rubriker manuellt baserat på bitdjup, samplings frekvens, antal kanaler osv.

I det här exemplet anger du ett RIFF-format med hög åter givning `Riff24Khz16BitMonoPcm` genom att ange `SpeechSynthesisOutputFormat` på- `SpeechConfig` objektet. Som liknar exemplet i föregående avsnitt, använder du [`AudioDataStream`](/cpp/cognitive-services/speech/audiodatastream) för att hämta en minnes intern ström i resultatet och sedan skriva den till en fil.

```cpp
void synthesizeSpeech()
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    config->SetSpeechSynthesisOutputFormat(SpeechSynthesisOutputFormat::Riff24Khz16BitMonoPcm);

    auto synthesizer = SpeechSynthesizer::FromConfig(config, NULL);
    auto result = synthesizer->SpeakTextAsync("A simple test to write to a file.").get();

    auto stream = AudioDataStream::FromResult(result);
    stream->SaveToWavFileAsync("path/to/write/file.wav").get();
}
```

Om du kör programmet igen skrivs en `.wav` fil till den angivna sökvägen.

## <a name="use-ssml-to-customize-speech-characteristics"></a>Använda SSML för att anpassa tal egenskaper

Med SSML (Speech syntes Markup Language) kan du finjustera bredden, uttal, tal igenkänning, volym och fler text till tal-utdata genom att skicka in dina begär Anden från ett XML-schema. I det här avsnittet visas ett exempel på hur du ändrar rösten, men en mer detaljerad guide finns i [artikeln SSML instruktion](../../../speech-synthesis-markup.md).

Om du vill börja använda SSML för anpassning gör du en enkel ändring som byter rösten.
Börja med att skapa en ny XML-fil för SSML-konfigurationen i rot katalogen för projektet i det här exemplet `ssml.xml` . Rot elementet är alltid `<speak>` och rad brytnings texten i ett `<voice>` element gör att du kan ändra rösten med hjälp av `name` param. Se den [fullständiga listan](../../../language-support.md#neural-voices) över **neurala** röster som stöds.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-US-AriaNeural">
    When you're on the freeway, it's a good idea to use a GPS.
  </voice>
</speak>
```

Sedan måste du ändra begäran om tal syntes för att referera till XML-filen. Begäran är i huvudsak samma, men i stället för att använda `SpeakTextAsync()` funktionen används `SpeakSsmlAsync()` . Den här funktionen förväntar sig en XML-sträng, så du måste först läsa in SSML-konfigurationen som en sträng. Härifrån är resultat-objektet exakt detsamma som i föregående exempel.

```cpp
void synthesizeSpeech()
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    auto synthesizer = SpeechSynthesizer::FromConfig(config, NULL);

    std::ifstream file("./ssml.xml");
    std::string ssml, line;
    while (std::getline(file, line))
    {
        ssml += line;
        ssml.push_back('\n');
    }
    auto result = synthesizer->SpeakSsmlAsync(ssml).get();

    auto stream = AudioDataStream::FromResult(result);
    stream->SaveToWavFileAsync("path/to/write/file.wav").get();
}
```

> [!NOTE]
> Om du vill ändra rösten utan att använda SSML kan du ställa in egenskapen på med `SpeechConfig` hjälp av `SpeechConfig.SetSpeechSynthesisVoiceName("en-US-AriaNeural")`

## <a name="get-facial-pose-events"></a>Hämta händelser för ansikts attityd

Tal kan vara ett bra sätt att köra animeringen av ansikts uttryck.
[Visemes](../../../how-to-speech-synthesis-viseme.md) används ofta för att representera viktiga faktorer i observerade tal, till exempel positionen för läppar, Jaw och tunga när de skapar en viss fonem.
Du kan prenumerera på händelsen viseme i tal-SDK.
Sedan kan du använda viseme-händelser för att animera FACET för ett Character som tal ljud spelas upp.
Lär dig [hur du hämtar viseme-händelser](../../../how-to-speech-synthesis-viseme.md#get-viseme-events-with-the-speech-sdk).
