---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 02/10/2021
ms.author: trbye
ms.custom: devx-track-js
ms.openlocfilehash: 3fa47935721ccfccdfe18d60a66d5cc480582e7d
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102428258"
---
I den här snabb starten lär du dig vanliga design mönster för att skapa text till tal-Sammanfattning med hjälp av tal-SDK. Du börjar med att utföra grundläggande konfiguration och syntes och går vidare till mer avancerade exempel för anpassad program utveckling, inklusive:

* Få svar som minnes strömmar
* Anpassa samplings frekvens och bit hastighet för utdata
* Skicka syntes begär Anden med SSML (tal syntes Markup Language)
* Använda neurala-röster

## <a name="skip-to-samples-on-github"></a>Hoppa till exempel på GitHub

Om du vill hoppa över direkt till exempel kod kan du läsa [snabb starts exemplen för Java Script](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/text-to-speech) på GitHub.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du har ett Azure-konto och en tjänst resurs för tal. Om du inte har ett konto och en resurs kan du [prova röst tjänsten kostnads fritt](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Installera Speech SDK

Innan du kan göra något måste du installera <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">Speech SDK för Java Script </a>. Använd följande instruktioner, beroende på plattform:
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Webbläsare </a>

Beroende på mål miljön använder du dessutom något av följande:

# <a name="script"></a>[skript](#tab/script)

Hämta och extrahera <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">talet SDK för java script</a> *microsoft.cognitiveservices.speech.sdk.bundle.js* -filen och placera den i en mapp som är tillgänglig för HTML-filen.

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> Om du är mål för en webbläsare och använder `<script>` taggen, `sdk` behövs inte prefixet. `sdk`Prefixet är ett alias som används för att namnge `require` modulen.

# <a name="import"></a>[export](#tab/import)

```javascript
import * as sdk from "microsoft-cognitiveservices-speech-sdk";
```

Mer information finns i `import` <a href="https://javascript.info/import-export" target="_blank">Exportera och importera </a>.

# <a name="require"></a>[innebära](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

Mer information om `require` finns i <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">Vad kräver? </a>.

---


## <a name="create-a-speech-configuration"></a>Skapa en tal konfiguration

Om du vill anropa tal tjänsten med hjälp av tal-SDK måste du skapa en [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) . Den här klassen innehåller information om din resurs, till exempel din nyckel och tillhör ande region, slut punkt, värd eller token för auktorisering.

> [!NOTE]
> Oavsett om du utför tal igenkänning, tal syntes, översättning eller avsikts igenkänning, skapar du alltid en konfiguration.

Det finns några sätt som du kan initiera en [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) :

* Med en resurs: skicka i en nyckel och tillhör ande region.
* Med en slut punkt: skicka i en röst tjänst slut punkt. En nyckel eller autentiseringstoken är valfri.
* Med en värd: skicka in en värd adress. En nyckel eller autentiseringstoken är valfri.
* Med en autentiseringstoken: skicka in en autentiseringstoken och den associerade regionen.

I det här exemplet skapar du en [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) med hjälp av en resurs nyckel och en region. Hämta dessa autentiseringsuppgifter genom att följa stegen i [testa tal tjänsten kostnads fritt](../../../overview.md#try-the-speech-service-for-free). Du kan också skapa en grundläggande exempel kod som du kan använda för resten av den här artikeln, som du ändrar för olika anpassningar.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
}

synthesizeSpeech();
```

## <a name="synthesize-speech-to-a-file"></a>Syntetisera tal till en fil

Därefter skapar du ett [`SpeechSynthesizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer) -objekt som kör text till tal-konverteringar och-utdata till högtalare, filer eller andra utgående strömmar. Det [`SpeechSynthesizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer) accepterar som param [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) -objektet som skapades i föregående steg och ett [`AudioConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig) objekt som anger hur resultat ska hanteras.

Starta genom att skapa en `AudioConfig` för att automatiskt skriva utdata till en `.wav` fil med hjälp av den `fromAudioFileOutput()` statiska funktionen.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromAudioFileOutput("path/to/file.wav");
}
```

Sedan instansierar `SpeechSynthesizer` du en överföring av ditt `speechConfig` objekt och `audioConfig` objektet som params. Sedan är det lika enkelt att köra tal syntes och skrivning till en fil som att köra `speakTextAsync()` med en text sträng. Resultatet av återanrop är en bra plats för att ringa `synthesizer.close()` , i själva verket krävs det här anropet för att syntesen ska fungera korrekt.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromAudioFileOutput("path-to-file.wav");

    const synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.speakTextAsync(
        "A simple test to write to a file.",
        result => {
            synthesizer.close();
            if (result) {
                // return result as stream
                return fs.createReadStream("path-to-file.wav");
            }
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

Kör programmet och en syntetisk `.wav` fil skrivs till den plats som du har angett. Detta är ett användbart exempel på den mest grundläggande användningen, men nästa du tittar på anpassningen av utdata och hantering av utdata som en minnes intern ström för att arbeta med anpassade scenarier.

## <a name="synthesize-to-speaker-output"></a>Syntetisera till talare-utdata

I vissa fall kanske du vill direkt mata ut syntetiskt tal direkt till en talare. Det gör du genom att instansiera `AudioConfig` med den `fromDefaultSpeakerOutput()` statiska funktionen. Dessa utdata till den aktuella aktiva utmatnings enheten.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromDefaultSpeakerOutput();

    const synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.speakTextAsync(
        "Synthesizing directly to speaker output.",
        result => {
            if (result) {
                synthesizer.close();
                return result.audioData;
            }
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

## <a name="get-result-as-an-in-memory-stream"></a>Få resultat som en minnes intern ström

För många scenarier i tal program utveckling behöver du förmodligen resulterande ljud data som en minnes intern ström i stället för att skriva direkt till en fil. Detta gör att du kan bygga anpassade funktioner, inklusive:

* Abstrakt den resulterande byte mat ris som en sökbar data ström för anpassade underordnade tjänster.
* Integrera resultatet med andra API: er eller tjänster.
* Ändra ljud data, skriv anpassade `.wav` rubriker osv.

Det är enkelt att göra den här ändringen från föregående exempel. Ta först bort `AudioConfig` blocket, eftersom du kommer att hantera utmatnings beteendet manuellt från den här punkten och därefter för ökad kontroll. Pass sedan `undefined` efter `AudioConfig` i `SpeechSynthesizer` konstruktorn. 

> [!NOTE]
> `undefined`Att skicka till `AudioConfig` , i stället för att utesluta det som i exemplet ovan, spelar inte upp ljudet som standard på den aktuella aktiva utmatnings enheten.

Den här gången sparar du resultatet i en [`SpeechSynthesisResult`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisresult) variabel. `SpeechSynthesisResult.audioData`Egenskapen returnerar en `ArrayBuffer` av utdata, standard typen för webbläsarens data ström. För Server kod konverterar du arrayBuffer till en buffert. 

Följande kod fungerar för kod på klient sidan. 

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const synthesizer = new sdk.SpeechSynthesizer(speechConfig);

    synthesizer.speakTextAsync(
        "Getting the response as an in-memory stream.",
        result => {
            synthesizer.close();
            return result.audioData;
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

Härifrån kan du implementera anpassade beteenden med det resulterande `ArrayBuffer` objektet. ArrayBuffer är en vanlig typ som kan tas emot i en webbläsare och spelas från det här formatet. 

Om du behöver arbeta med data som en data ström i stället för en ArrayBuffer måste du konvertera objektet till en data ström för all serverbaserad kod. 

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const synthesizer = new sdk.SpeechSynthesizer(speechConfig);

    synthesizer.speakTextAsync(
        "Getting the response as an in-memory stream.",
        result => {
            const { audioData } = result;

            synthesizer.close();

            // convert arrayBuffer to stream
            // return stream
            const bufferStream = new PassThrough();
            bufferStream.end(Buffer.from(audioData));
            return bufferStream;
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

## <a name="customize-audio-format"></a>Anpassa ljud format

I följande avsnitt visas hur du anpassar attribut för ljud uppspelning, inklusive:

* Ljud fils typ
* Samplings frekvens
* Bitdjup

Om du vill ändra ljud formatet använder du `speechSynthesisOutputFormat` egenskapen på `SpeechConfig` objektet. Den här egenskapen förväntar sig en `enum` typ [`SpeechSynthesisOutputFormat`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat) , som du kan använda för att välja utdataformatet. Se referens dokumenten för en [lista över tillgängliga ljud format](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat) .

Det finns olika alternativ för olika filtyper beroende på dina krav. Observera att RAW-format, t `Raw24Khz16BitMonoPcm` . ex., inte innehåller ljud rubriker enligt definitionen. Använd RAW-format endast när du vet att den underordnade implementeringen kan avkoda en RAW-Bitstream, eller om du planerar att skapa rubriker manuellt baserat på bitdjup, samplings frekvens, antal kanaler osv.

> [!NOTE]
> Rösterna **en-US-AriaRUS** och **en-US-GuyRUS** skapas från exempel som kodats i `Riff24Khz16BitMonoPcm` samplings frekvensen.

I det här exemplet anger du ett RIFF-format med hög åter givning `Riff24Khz16BitMonoPcm` genom att ange `speechSynthesisOutputFormat` på- `SpeechConfig` objektet. Liknar exemplet i föregående avsnitt och hämta `ArrayBuffer` ljuddata och interagera med den.

```javascript
function synthesizeSpeech() {
    const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

    // Set the output format
    speechConfig.speechSynthesisOutputFormat = SpeechSynthesisOutputFormat.Riff24Khz16BitMonoPcm;

    const synthesizer = new sdk.SpeechSynthesizer(speechConfig, undefined);
    synthesizer.speakTextAsync(
        "Customizing audio output format.",
        result => {
            // Interact with the audio ArrayBuffer data
            const audioData = result.audioData;
            console.log(`Audio data byte size: ${audioData.byteLength}.`)

            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
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

Sedan måste du ändra begäran om tal syntes för att referera till XML-filen. Begäran är i huvudsak samma, men i stället för att använda `speakTextAsync()` funktionen används `speakSsmlAsync()` . Den här funktionen förväntar sig en XML-sträng, så först skapar du en funktion för att läsa in en XML-fil och returnera den som en sträng.

```javascript
function xmlToString(filePath) {
    const xml = readFileSync(filePath, "utf8");
    return xml;
}
```

Mer information finns i `readFileSync` <a href="https://nodejs.org/api/fs.html#fs_fs_readlinksync_path_options" target="_blank">Node.js fil system</a>. Härifrån är resultat-objektet exakt detsamma som i föregående exempel.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const synthesizer = new sdk.SpeechSynthesizer(speechConfig, undefined);

    const ssml = xmlToString("ssml.xml");
    synthesizer.speakSsmlAsync(
        ssml,
        result => {
            if (result.errorDetails) {
                console.error(result.errorDetails);
            } else {
                console.log(JSON.stringify(result));
            }

            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
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
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
    xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
  <voice name="en-US-AriaNeural">
    <mstts:express-as style="cheerful">
      This is awesome!
    </mstts:express-as>
  </voice>
</speak>
```
