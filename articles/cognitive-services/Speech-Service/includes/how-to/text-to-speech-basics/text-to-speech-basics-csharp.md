---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: b1161fdcbed7933c7a8dd0dccadd2e896966b728
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104719835"
---
I den här snabb starten lär du dig vanliga design mönster för att skapa text till tal-Sammanfattning med hjälp av tal-SDK. Du börjar med att utföra grundläggande konfiguration och syntes och går vidare till mer avancerade exempel för anpassad program utveckling, inklusive:

* Få svar som minnes strömmar
* Anpassa samplings frekvens och bit hastighet för utdata
* Skicka syntes begär Anden med SSML (tal syntes Markup Language)
* Använda neurala-röster

## <a name="skip-to-samples-on-github"></a>Hoppa till exempel på GitHub

Om du vill hoppa över direkt till exempel kod, se [snabb starts exemplen för C#](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/text-to-speech) på GitHub.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du har ett Azure-konto och en röst tjänst prenumeration. Om du inte har ett konto och en prenumeration kan du [prova att använda tal tjänsten kostnads fritt](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Installera Speech SDK

Innan du kan göra något måste du installera talet SDK. Använd följande instruktioner, beroende på plattform:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnet&pivots=programming-language-csharp" target="_blank">.NET Framework </a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnetcore&pivots=programming-language-csharp" target="_blank">.NET Core </a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=unity&pivots=programming-language-csharp" target="_blank">Unity </a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=uwps&pivots=programming-language-csharp" target="_blank">UWP </a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=xaml&pivots=programming-language-csharp" target="_blank">Xamarin </a>

## <a name="import-dependencies"></a>Importera beroenden

Om du vill köra exemplen i den här artikeln inkluderar du följande `using` instruktioner överst i skriptet.

```csharp
using System;
using System.IO;
using System.Text;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
```

## <a name="create-a-speech-configuration"></a>Skapa en tal konfiguration

Om du vill anropa tal tjänsten med hjälp av tal-SDK måste du skapa en [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig) . Den här klassen innehåller information om din prenumeration, till exempel din nyckel och tillhör ande region, slut punkt, värd eller token för auktorisering.

> [!NOTE]
> Oavsett om du utför tal igenkänning, tal syntes, översättning eller avsikts igenkänning, skapar du alltid en konfiguration.

Det finns några sätt som du kan initiera en [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig) :

* Med en prenumeration: skicka i en nyckel och tillhör ande region.
* Med en slut punkt: skicka i en röst tjänst slut punkt. En nyckel eller autentiseringstoken är valfri.
* Med en värd: skicka in en värd adress. En nyckel eller autentiseringstoken är valfri.
* Med en autentiseringstoken: skicka in en autentiseringstoken och den associerade regionen.

I det här exemplet skapar du en [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig) med en prenumerations nyckel och en region. Hämta dessa autentiseringsuppgifter genom att följa stegen i [testa tal tjänsten kostnads fritt](../../../overview.md#try-the-speech-service-for-free). Du kan också skapa en grundläggande exempel kod som du kan använda för resten av den här artikeln, som du ändrar för olika anpassningar.

```csharp
public class Program
{
    static async Task Main()
    {
        await SynthesizeAudioAsync();
    }

    static async Task SynthesizeAudioAsync()
    {
        var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    }
}
```

## <a name="synthesize-speech-to-a-file"></a>Syntetisera tal till en fil

Därefter skapar du ett [`SpeechSynthesizer`](/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesizer) -objekt som kör text till tal-konverteringar och-utdata till högtalare, filer eller andra utgående strömmar. Det [`SpeechSynthesizer`](/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesizer) accepterar som param [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig) -objektet som skapades i föregående steg och ett [`AudioConfig`](/dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig) objekt som anger hur resultat ska hanteras.

Starta genom att skapa en `AudioConfig` för att automatiskt skriva utdata till en `.wav` fil, använda `FromWavFileOutput()` funktionen och instansiera den med en `using` instruktion. En `using` instruktion i den här kontexten kan automatiskt ta bort ohanterade resurser och gör att objektet hamnar utanför omfånget efter avyttringen.

```csharp
static async Task SynthesizeAudioAsync()
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    using var audioConfig = AudioConfig.FromWavFileOutput("path/to/write/file.wav");
}
```

Sedan instansierar du en `SpeechSynthesizer` med en annan `using` instruktion. Skicka `config` objektet och `audioConfig` objektet som parametrar. Sedan är det lika enkelt att köra tal syntes och skrivning till en fil som att köra `SpeakTextAsync()` med en text sträng.

```csharp
static async Task SynthesizeAudioAsync()
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    using var audioConfig = AudioConfig.FromWavFileOutput("path/to/write/file.wav");
    using var synthesizer = new SpeechSynthesizer(config, audioConfig);
    await synthesizer.SpeakTextAsync("A simple test to write to a file.");
}
```

Kör programmet och en syntetisk `.wav` fil skrivs till den plats som du har angett. Detta är ett användbart exempel på den mest grundläggande användningen, men nästa du tittar på anpassningen av utdata och hantering av utdata som en minnes intern ström för att arbeta med anpassade scenarier.

## <a name="synthesize-to-speaker-output"></a>Syntetisera till talare-utdata

I vissa fall kanske du vill direkt mata ut syntetiskt tal direkt till en talare. Det gör du genom att utelämna `AudioConfig` parametern när du skapar `SpeechSynthesizer` i exemplet ovan. Den här syntetiseran till den aktuella aktiva utmatnings enheten.

```csharp
static async Task SynthesizeAudioAsync()
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    using var synthesizer = new SpeechSynthesizer(config);
    await synthesizer.SpeakTextAsync("Synthesizing directly to speaker output.");
}
```

## <a name="get-result-as-an-in-memory-stream"></a>Få resultat som en minnes intern ström

För många scenarier i tal program utveckling behöver du förmodligen resulterande ljud data som en minnes intern ström i stället för att skriva direkt till en fil. Detta gör att du kan bygga anpassade funktioner, inklusive:

* Abstrakt den resulterande byte mat ris som en sökbar data ström för anpassade underordnade tjänster.
* Integrera resultatet med andra API: er eller tjänster.
* Ändra ljud data, skriv anpassade `.wav` rubriker osv.

Det är enkelt att göra den här ändringen från föregående exempel. Ta först bort `AudioConfig` blocket, eftersom du kommer att hantera utmatnings beteendet manuellt från den här punkten och därefter för ökad kontroll. Pass sedan `null` efter `AudioConfig` i `SpeechSynthesizer` konstruktorn.

> [!NOTE]
> `null`Att skicka till `AudioConfig` , i stället för att utesluta det som i exemplet ovan, spelar inte upp ljudet som standard på den aktuella aktiva utmatnings enheten.

Den här gången sparar du resultatet i en [`SpeechSynthesisResult`](/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisresult) variabel. `AudioData`Egenskapen innehåller en `byte []` av utdata. Du kan arbeta med detta `byte []` manuellt, eller så kan du använda- [`AudioDataStream`](/dotnet/api/microsoft.cognitiveservices.speech.audiodatastream) klassen för att hantera minnes minnes minnes strömmar. I det här exemplet använder du den `AudioDataStream.FromResult()` statiska funktionen för att hämta en ström från resultatet.

```csharp
static async Task SynthesizeAudioAsync()
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    using var synthesizer = new SpeechSynthesizer(config, null);

    var result = await synthesizer.SpeakTextAsync("Getting the response as an in-memory stream.");
    using var stream = AudioDataStream.FromResult(result);
}
```

Härifrån kan du implementera anpassade beteenden med det resulterande `stream` objektet.

## <a name="customize-audio-format"></a>Anpassa ljud format

I följande avsnitt visas hur du anpassar attribut för ljud uppspelning, inklusive:

* Ljud fils typ
* Samplings frekvens
* Bitdjup

Om du vill ändra ljud formatet använder du `SetSpeechSynthesisOutputFormat()` funktionen på `SpeechConfig` objektet. Den här funktionen förväntar sig en `enum` typ [`SpeechSynthesisOutputFormat`](/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisoutputformat) som du kan använda för att välja utdataformatet. Se referens dokumenten för en [lista över tillgängliga ljud format](/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisoutputformat) .

Det finns olika alternativ för olika filtyper beroende på dina krav. Observera att RAW-format, t `Raw24Khz16BitMonoPcm` . ex., inte innehåller ljud rubriker enligt definitionen. Använd RAW-format endast när du vet att den underordnade implementeringen kan avkoda en RAW-Bitstream, eller om du planerar att skapa rubriker manuellt baserat på bitdjup, samplings frekvens, antal kanaler osv.

> [!NOTE]
> Rösterna **en-US-AriaRUS** och **en-US-GuyRUS** skapas från exempel som kodats i `Riff24Khz16BitMonoPcm` samplings frekvensen.

I det här exemplet anger du ett RIFF-format med hög åter givning `Riff24Khz16BitMonoPcm` genom att ange `SpeechSynthesisOutputFormat` på- `SpeechConfig` objektet. Som liknar exemplet i föregående avsnitt, använder du [`AudioDataStream`](/dotnet/api/microsoft.cognitiveservices.speech.audiodatastream) för att hämta en minnes intern ström i resultatet och sedan skriva den till en fil.

```csharp
static async Task SynthesizeAudioAsync()
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    config.SetSpeechSynthesisOutputFormat(SpeechSynthesisOutputFormat.Riff24Khz16BitMonoPcm);

    using var synthesizer = new SpeechSynthesizer(config, null);
    var result = await synthesizer.SpeakTextAsync("Customizing audio output format.");

    using var stream = AudioDataStream.FromResult(result);
    await stream.SaveToWaveFileAsync("path/to/write/file.wav");
}
```

Om du kör programmet igen skrivs en `.wav` fil till den angivna sökvägen.

## <a name="use-ssml-to-customize-speech-characteristics"></a>Använda SSML för att anpassa tal egenskaper

Med SSML (Speech syntes Markup Language) kan du finjustera bredden, uttal, tal igenkänning, volym och fler text till tal-utdata genom att skicka in dina begär Anden från ett XML-schema. I det här avsnittet visas några praktiska exempel på användnings exempel, men en mer detaljerad guide finns i [artikeln SSML instruktion](../../../speech-synthesis-markup.md).

Om du vill börja använda SSML för anpassning gör du en enkel ändring som byter rösten.
Börja med att skapa en ny XML-fil för SSML-konfigurationen i rot katalogen för projektet i det här exemplet `ssml.xml` . Rot elementet är alltid `<speak>` och rad brytnings texten i ett `<voice>` element gör att du kan ändra rösten med hjälp av `name` param. Det här exemplet ändrar rösten till en amerikansk engelska (UK) röst. Observera att den här rösten är en **standard** röst, som har olika priser och tillgänglighet än **neurala** röster. Se den [fullständiga listan](../../../language-support.md#standard-voices) över **standard** röster som stöds.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    When you're on the motorway, it's a good idea to use a sat-nav.
  </voice>
</speak>
```

Sedan måste du ändra begäran om tal syntes för att referera till XML-filen. Begäran är i huvudsak samma, men i stället för att använda `SpeakTextAsync()` funktionen används `SpeakSsmlAsync()` . Den här funktionen förväntar sig en XML-sträng, så du måste först läsa in SSML-konfigurationen som en sträng med hjälp av `File.ReadAllText()` . Härifrån är resultat-objektet exakt detsamma som i föregående exempel.

> [!NOTE]
> Om du använder Visual Studio kommer din build config antagligen inte att hitta XML-filen som standard. Åtgärda detta genom att högerklicka på XML-filen och välja **Egenskaper**. Ändra **Bygg åtgärden** till *innehåll* och ändra *alltid* genom att ändra **kopia till utdatakatalogen** .

```csharp
public static async Task SynthesizeAudioAsync()
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    using var synthesizer = new SpeechSynthesizer(config, null);

    var ssml = File.ReadAllText("./ssml.xml");
    var result = await synthesizer.SpeakSsmlAsync(ssml);

    using var stream = AudioDataStream.FromResult(result);
    await stream.SaveToWaveFileAsync("path/to/write/file.wav");
}
```

Utdata fungerar, men det finns några enkla ytterligare ändringar som du kan göra för att hjälpa dem att bli mer naturliga. Den övergripande tal hastigheten är lite för snabb, så vi lägger till en `<prosody>` tagg och minskar hastigheten till **90%** av standard priset. Dessutom är pausen efter det att kommatecken i meningen är lite för kort och inte naturligt. Du kan åtgärda det här problemet genom att lägga till en `<break>` tagg för att skjuta upp talet och ange tids parametern till **200ms**. Kör sammanfattningen igen för att se hur dessa anpassningar påverkar utdata.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    <prosody rate="0.9">
      When you're on the motorway,<break time="200ms"/> it's a good idea to use a sat-nav.
    </prosody>
  </voice>
</speak>
```

## <a name="neural-voices"></a>Neurala-röster

Neurala-röster är tal syntes-algoritmer som drivs av djup neurala nätverk. När du använder en neurala-röst är syntetiskt tal nästan lätt att skilja från de mänskliga inspelningarna. Med den humana naturliga prosody och tydligare utsättande av ord, minskar neurala-röster avsevärt lyssnings utmattning när användarna interagerar med AI-system.

Om du vill växla till en neurala röst ändrar `name` du till ett av [röst alternativen för neurala](../../../language-support.md#neural-voices). Lägg sedan till ett XML-namnområde för `mstts` och Radbryt texten i `<mstts:express-as>` taggen. Använd `style` param för att anpassa tal formatet. Det här exemplet använder `cheerful` , men försök att ställa in det på `customerservice` eller `chat` för att se skillnaden i tal formatet.

> [!IMPORTANT]
> Neurala-röster stöds **bara** för tal resurser som skapats i regionerna *östra USA*, Asien, sydöstra *och Västeuropa.* 

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
  <voice name="en-US-AriaNeural">
    <mstts:express-as style="cheerful">
      This is awesome!
    </mstts:express-as>
  </voice>
</speak>
```
## <a name="get-facial-pose-events"></a>Hämta händelser för ansikts attityd

Tal kan vara ett bra sätt att köra animeringen av ansikts uttryck.
[Visemes](../../../how-to-speech-synthesis-viseme.md) används ofta för att representera viktiga faktorer i observerade tal, till exempel positionen för läppar, Jaw och tunga när de skapar en viss fonem.
Du kan prenumerera på händelsen viseme i tal-SDK.
Sedan kan du använda viseme-händelser för att animera FACET för ett Character som tal ljud spelas upp.
Lär dig [hur du hämtar viseme-händelser](../../../how-to-speech-synthesis-viseme.md#get-viseme-events-with-the-speech-sdk).
