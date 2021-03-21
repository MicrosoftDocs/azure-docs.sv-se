---
title: Vanliga frågor och svar om tjänsten för tal tjänst behållare
titleSuffix: Azure Cognitive Services
description: Installera och kör tal behållare. tal till text skickar ljud strömmar till text i real tid som dina program, verktyg eller enheter kan använda eller Visa. Text till tal konverterar inmatad text till mänskligt syntetiskt tal.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2021
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 16158b4ecfb46ea9092fe9eeb31cc4dee259b1ab
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103573752"
---
# <a name="speech-service-containers-frequently-asked-questions-faq"></a>Vanliga frågor och svar om tjänsten för tal tjänst behållare

När du använder tal tjänsten med behållare, förlitar du dig på den här samlingen vanliga frågor innan du eskalerar till support. Den här artikeln fångar frågor varierande grad, från allmän till teknisk. Klicka på frågan för att expandera ett svar.

## <a name="general-questions"></a>Allmänna frågor

<details>
<summary>
<b>Hur fungerar tal behållare och hur ställer jag in dem?</b>
</summary>

**Svar:** När du ställer in produktions klustret finns det flera saker att tänka på. För det första måste du konfigurera ett enskilt språk, flera behållare på samma dator, inte vara ett stort problem. Om det uppstår problem kan det vara ett maskin varu problem – så vi skulle först titta på resursen, det vill säga. Specifikationer för processor och minne.

Ta en stund, `ja-JP` behållare och den senaste modellen. Den akustiska modellen är den mest krävande delen av processor, medan språk modellen kräver mest minne. När vi har förbrukat användningen tar det cirka 0,6 CPU-kärnor för att bearbeta en enda tal-till-text-begäran när ljudet flödar i real tid (som från mikrofonen). Om du matar in ljud snabbare än i real tid (t. ex. från en fil) kan användningen vara dubbel (1,2 x kärnor). Under tiden är minnet som anges nedan operativ minne för avkodning av tal. Den tar *inte* hänsyn till den faktiska fulla storleken på språk modellen, som kommer att finnas i fil-cachen. För det här `ja-JP` är ytterligare 2 GB. för `en-US` , kan det vara mer (6-7 GB).

Om du har en dator där minnet är begränsade och du försöker distribuera flera språk, är det möjligt att filcachen är full och att operativ systemet tvingas att placera modeller i och ut. För en pågående avskrift, som kan vara katastrofal, och kan leda till långsammare och andra prestanda konsekvenser.

Dessutom förpaketerar vi körbara filer för datorer med instruktions uppsättningen [Avancerad Vector-anknytning (AVX2)](speech-container-howto.md#advanced-vector-extension-support) . En dator med AVX512-instruktionen kräver kodgenerering för det målet och start 10-behållare för 10 språk kan tillfälligt ha slut på processor kraft. Ett meddelande som det här visas i Docker-loggarna:

```console
2020-01-16 16:46:54.981118943 
[W:onnxruntime:Default, tvm_utils.cc:276 LoadTVMPackedFuncFromCache]
Cannot find Scan4_llvm__mcpu_skylake_avx512 in cache, using JIT...
```

Du kan ange antalet avkodare som du vill ha i en *enda* behållare med hjälp av `DECODER MAX_COUNT` variabeln. Vi bör därför börja med SKU (CPU/minne) och vi kan föreslå hur du får ut så mycket som möjligt. En bra utgångs punkt refererar till de rekommenderade värd datorernas resurs specifikationer.

<br>
</details>

<details>
<summary>
<b>Kan du hjälpa till med kapacitets planering och kostnads uppskattning av lokal-behållare från tal till text?</b>
</summary>

**Svar:** För container kapacitet i batch-bearbetnings läge kan varje avkodare hantera 2-3x i real tid, med två processor kärnor, för ett enda igenkännings resultat. Vi rekommenderar inte att du behåller fler än två samtidiga igenkänningar per behållar instans, men rekommenderar att du kör fler instanser av behållare för Tillförlitlighets-/tillgänglighets skäl, bakom en belastningsutjämnare.

Även om vi kan ha varje behållar instans som körs med fler avkodare. Vi kan till exempel konfigurera 7 avkodare per behållar instans på en åtta kärnor (på mer än 2x var), vilket ger 15x-genomflöde. Det finns en param `DECODER_MAX_COUNT` som är medveten om. För det extrema fallet uppstår Tillförlitlighets-och latens problem, och data flödet ökade avsevärt. För en mikrofon kommer den att vara i 1x real tid. Den övergripande användningen bör vara på ungefär en kärna för en enda igenkänning.

För scenario för bearbetning av 1 K timmar/dag i batchbearbetnings läge kan tre virtuella datorer hantera dem inom 24 timmar men inte garanterade. För att hantera insamling av insamlade dagar, redundans, uppdatering och för att ange lägsta säkerhets kopiering/BCP, rekommenderar vi 4-5 datorer i stället för 3 per kluster och med 2 kluster.

För maskin vara använder vi standard Azure VM `DS13_v2` som en referens (varje kärna måste vara 2,6 GHz eller bättre, med AVX2-instruktions uppsättningen aktive rad).

| Instans  | vCPU (s) | RAM    | Temp-lagring | Betala per användning med AHB | 1 års reserv med AHB (besparingar) | tre år reserverat med AHB (besparingar) |
|-----------|---------|--------|--------------|------------------------|-------------------------------------|--------------------------------------|
| `DS13 v2` | 8       | 56 GiB | 112 GiB      | $0.598/timme            | $0.3528/timme (~ 41%)                 | $0.2333/timme (~ 61%)                  |

Baserat på design referensen (två kluster på 5 virtuella datorer för att hantera 1 K timmar/dag ljud grupps bearbetning), blir 1 års maskin varu kostnad:

> 2 (kluster) * 5 (virtuella datorer per kluster) * $0.3528/timme * 365 (dagar) * 24 (timmar) = $31K/år

Vid mappning till en fysisk dator är en allmän uppskattning 1 vCPU = 1 fysisk processor kärna. I verkligheten är 1vCPU mer kraftfullare än en enda kärna.

För lokal kommer alla dessa ytterligare faktorer att komma i spel:

- På vilken typ den fysiska processorn är och hur många kärnor det är
- Hur många processorer som körs tillsammans i samma ruta/dator
- Hur virtuella datorer konfigureras
- Hur Hyper-Threading och multi-threading används
- Hur minnet delas
- Operativ systemet, osv.

Normalt är det inte lika bra som i Azure-miljön. När jag överväger andra kostnader är det en säker uppskattning som är 10 fysiska processor kärnor = 8 Azure-vCPU. Även om populära processorer bara har åtta kärnor. Med lokal distribution är kostnaden högre än att använda virtuella Azure-datorer. Överväg också avskrivnings takten.

Service kostnaden är samma som online tjänsten: $1/timme för tal till text. Tal service kostnaden är:

> $1 * 1000 * 365 = $365K

De underhålls kostnader som betalas ut till Microsoft beror på tjänst nivå och innehåll i tjänsten. Den är olika från $29.99/månad för grundläggande nivå till hundratals tusen om tjänsten är inblandad. Ett grovt tal är $300/timme för service/kvarhållning. Kostnad för personer ingår inte. Andra infrastruktur kostnader (till exempel lagring, nätverk och belastnings utjämning) ingår inte.

<br>
</details>

<details>
<summary>
<b>Varför saknas skiljetecken i avskriften?</b>
</summary>

**Svar:** `speech_recognition_language=<YOUR_LANGUAGE>` Ska konfigureras explicit i begäran om de använder en kol-klient.

Exempel:

```python
if not recognize_once(
    speechsdk.SpeechRecognizer(
        speech_config=speechsdk.SpeechConfig(
            endpoint=template.format("interactive"),
            speech_recognition_language="ja-JP"),
            audio_config=audio_config)):

    print("Failed interactive endpoint")
    exit(1)
```
Här är utdata:

```cmd
RECOGNIZED: SpeechRecognitionResult(
    result_id=2111117c8700404a84f521b7b805c4e7, 
    text="まだ早いまだ早いは猫である名前はまだないどこで生まれたかとんと見当を検討をなつかぬ。
    何でも薄暗いじめじめした所でながら泣いていた事だけは記憶している。
    まだは今ここで初めて人間と言うものを見た。
    しかも後で聞くと、それは書生という人間中で一番同額同額。",
    reason=ResultReason.RecognizedSpeech)
```

<br>
</details>

<details>
<summary>
<b>Kan jag använda en anpassad akustisk modell och en språk modell med tal behållare?</b>
</summary>

Vi kan för närvarande bara skicka ett modell-ID, antingen en anpassad språk modell eller en anpassad akustisk modell.

**Svar:** Beslutet att *inte* stödja både akustiska och språk modeller gjordes samtidigt. Detta gäller även tills en enhetlig identifierare har skapats för att minska API-avbrott. Därför stöds det tyvärr inte just nu.

<br>
</details>

<details>
<summary>
<b>Kan du förklara felen från den anpassade tal-till-text-behållaren?</b>
</summary>

**Fel 1:**

```cmd
Failed to fetch manifest: Status: 400 Bad Request Body:
{
    "code": "InvalidModel",
    "message": "The specified model is not supported for endpoint manifests."
}
```

**Svar 1:** Om du har utbildning med den senaste anpassade modellen stöder vi för närvarande inte det. Om du tränar med en äldre version bör det vara möjligt att använda. Vi arbetar fortfarande med att stödja de senaste versionerna.

I stort sett stöder inte de anpassade behållarna Halide eller ONNX akustiska akustiska modeller (som är standard i den anpassade utbildnings portalen). Detta beror på att anpassade modeller inte krypteras och att vi inte vill exponera ONNX-modeller, men språk modeller är fina. Kunden måste uttryckligen välja en äldre icke-ONNX modell för anpassad utbildning. Noggrannhet kommer inte att påverkas. Modell storleken kan vara större (med 100 MB).

> Support modell > 20190220 (v 4.5 Unified)

**Fel 2:**

```cmd
HTTPAPI result code = HTTPAPI_OK.
HTTP status code = 400.
Reason:  Synthesis failed.
StatusCode: InvalidArgument,
Details: Voice does not match.
```

**Svar 2:** Du måste ange rätt röst namn i begäran, vilket är Skift läges känsligt. Läs den fullständiga tjänst namns mappningen.

**Fel 3:**

```json
{
    "code": "InvalidProductId",
    "message": "The subscription SKU \"CognitiveServices.S0\" is not supported in this service instance."
}
```

**Svar 3:** Du Reed för att skapa en tal resurs, inte en Cognitive Services-resurs.


<br>
</details>

<details>
<summary>
<b>Vilka API-protokoll stöds, REST eller WS?</b>
</summary>

**Svar:** För tal-till-text-och anpassade tal-till-text-behållare stöder vi för närvarande endast WebSocket-baserat protokoll. SDK: n stöder bara anrop i WS men inte REST. Det finns en plan för att lägga till REST-support, men inte ETA för tillfället. Läs alltid den officiella dokumentationen i [fråga förutsägelse-slutpunkter](speech-container-howto.md#query-the-containers-prediction-endpoint).

<br>
</details>

<details>
<summary>
<b>Stöds CentOS av tal behållare?</b>
</summary>

**Svar:** CentOS 7 stöds inte av python SDK än, även om Ubuntu 19,04 inte stöds.

Python Speech SDK-paketet är tillgängligt för dessa operativsystem:
- **Windows** -x64 och x86
- **Mac** -MacOS X version 10,12 eller senare
- **Linux** -Ubuntu 16,04, Ubuntu 18,04, Debian 9 på x64

Mer information om installations miljön finns i [installations programmet för python-plattformen](quickstarts/setup-platform.md?pivots=programming-language-python). Ubuntu 18,04 är nu den rekommenderade versionen.

<br>
</details>

<details>
<summary>
<b>Varför får jag fel meddelanden när jag försöker anropa LUIS förutsägelse slut punkter?</b>
</summary>

Jag använder LUIS-behållaren i en IoT Edge distribution och försöker anropa LUIS förutsägelse-slutpunkten från en annan behållare. LUIS-behållaren lyssnar på port 5001 och den URL jag använder är följande:

```csharp
var luisEndpoint =
    $"ws://192.168.1.91:5001/luis/prediction/v3.0/apps/{luisAppId}/slots/production/predict";
var config = SpeechConfig.FromEndpoint(new Uri(luisEndpoint));
```

Felet jag får är:

```cmd
WebSocket Upgrade failed with HTTP status code: 404 SessionId: 3cfe2509ef4e49919e594abf639ccfeb
```

Jag ser begäran i LUIS-behållar loggarna och meddelandet som säger:

```cmd
The request path /luis//predict" does not match a supported file type.
```

Vad betyder detta? Vad saknas? Jag följer exemplet på tal-SDK: n [här](https://github.com/Azure-Samples/cognitive-services-speech-sdk). Scenariot är att vi identifierar ljudet direkt från PC-mikrofonen och försöker bestämma avsikten, baserat på LUIS-appen vi tränade. Det exempel jag länkade till gör exakt det. Och fungerar bra med den molnbaserade tjänsten LUIS. Genom att använda tal-SDK verkade du spara oss från att behöva göra ett separat explicit anrop till API: et för tal till text och sedan ett andra anrop till LUIS.

Allt jag försöker göra är att byta från scenariot med att använda LUIS i molnet för att använda LUIS-behållaren. Jag kan inte tänka på om tal-SDK fungerar för en sådan, men det fungerar inte för det andra.

**Svar:** Tal-SDK bör inte användas mot en LUIS-behållare. För att använda LUIS-behållaren ska LUIS SDK-eller LUIS-REST API användas. Tal-SDK ska användas mot en tal behållare.

Ett moln skiljer sig från en behållare. Ett moln kan bestå av flera aggregerade behållare (kallas ibland Micro-tjänster). Det finns därför en LUIS-behållare och det finns en tal behållare – två separata behållare. Tal behållaren är bara tal. LUIS-behållaren innehåller bara LUIS. I molnet, eftersom båda behållarna är kända för att distribueras och det är dåliga prestanda för en fjärran sluten klient att gå till molnet, gör tal, kom tillbaka och gå sedan till molnet igen och gör LUIS, vi tillhandahåller en funktion som gör att klienten kan gå till tal, stanna kvar i molnet, gå till LUIS och sedan gå tillbaka till klienten. Till och med i det här scenariot går tal-SDK: n till tal Cloud container med ljud och sedan talar tal Cloud container om till LUIS Cloud container med text. LUIS-behållaren har inget begrepp för att acceptera ljud (det skulle inte vara meningen för att LUIS-behållaren ska acceptera strömmande ljud-LUIS är en textbaserad tjänst). Med lokal, vi har ingen säkerhet för att våra kunder har distribuerat båda behållarna, vi förutsätter inte att dirigera mellan behållare i våra kunders lokaler, och om båda behållarna har distribuerats på lokal, med tanke på att de är mer lokala för klienten, är det inte ett krav att gå tillbaka till SR först, tillbaka till klienten och låta kunden ta den texten och gå till LUIS.

<br>
</details>

<details>
<summary>
<b>Varför får vi fel med macOS, tal behållaren och python SDK?</b>
</summary>

När vi skickar en *. wav* -fil som ska skrivas tillbaka kommer resultatet att bli tillbaka med:

```cmd
recognition is running....
Speech Recognition canceled: CancellationReason.Error
Error details: Timeout: no recognition result received.
When creating a websocket connection from the browser a test, we get:
wb = new WebSocket("ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1")
WebSocket
{
    url: "ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1",
    readyState: 0,
    bufferedAmount: 0,
    onopen: null,
    onerror: null,
    ...
}
```

Vi vet att WebSocket har kon figurer ATS korrekt.

**Svar:** I så fall, se [detta GitHub-problem](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310). Vi har ett arbete runt, som [föreslås här](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310#issuecomment-527542722).

Koldioxid som korrigeras detta i version 1,8.


<br>
</details>

<details>
<summary>
<b>Vilka är skillnaderna i tal behållarens slut punkter?</b>
</summary>

Kan du fylla i följande test mått, inklusive vilka funktioner som ska testas och hur du testar SDK och REST-API: er? Särskilt skillnader i "interaktiva" och "konversation", som jag inte ser från ett befintligt dokument/exempel.

| Slutpunkt                                                | Funktionellt test                                                   | SDK | REST-API |
|---------------------------------------------------------|-------------------------------------------------------------------|-----|----------|
| `/speech/synthesize/cognitiveservices/v1`               | Syntetisera text (text till tal)                                  |     | Ja      |
| `/speech/recognition/dictation/cognitiveservices/v1`    | Cognitive Services lokal Diktering v1 WebSocket-slutpunkt        | Ja | Inga       |
| `/speech/recognition/interactive/cognitiveservices/v1`  | Den Cognitive Services lokal interaktiva v1 WebSocket-slutpunkten  |     |          |
| `/speech/recognition/conversation/cognitiveservices/v1` | Kognitiva tjänster på lokal-konversation v1 WebSocket-slutpunkt |     |          |

**Svar:** Detta är en fusion av:
- Personer som försöker köra dikteringens slut punkt för behållare (jag är inte säker på hur de får den URL: en)
- Slut punkten för 1<sup>St</sup> -parten som är en i en behållare.
- Slut punkten för 1<sup>St</sup> -parten returnerar tal. fragment-meddelanden i stället för `speech.hypothesis` e-<sup></sup> postmeddelandena för att returnera slut punkten för slut punkterna för slutpunkten.
- Snabb starter alla använder `RecognizeOnce` (interaktivt läge)
- Kol har en kontroll att för `speech.fragment` meddelanden som kräver att de inte returneras i interaktivt läge.
- Kol som har utgångs punkt i versions utlösare (avslutar processen).

Lösningen är antingen växlad till att använda kontinuerlig igenkänning i koden eller (snabbare) ansluter till antingen de interaktiva eller kontinuerliga slut punkterna i behållaren.
För din kod ställer du in slut punkten till `host:port` /Speech/Recognition/Interactive/cognitiveservices/v1

För olika lägen, se tallägen – se nedan:

## <a name="speech-modes---interactive-conversation-dictation"></a>Tallägen – interaktiv, konversation, Diktering

[!INCLUDE [speech-modes](includes/speech-modes.md)]

Den rätta korrigeringen kommer med SDK 1,8, som har stöd för lokal (kommer att välja rätt slut punkt, så vi kommer inte att vara lägre än online tjänsten). Under tiden finns det ett exempel på kontinuerlig igenkänning, varför ska vi inte peka på det?

https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/6805d96bf69d9e95c9137fe129bc5d81e35f6309/samples/python/console/speech_sample.py#L196

<br>
</details>

<details>
<summary>
<b>Vilket läge ska jag använda för olika ljudfiler?</b>
</summary>

**Svar:** Här är en [snabb start med python](./get-started-speech-to-text.md?pivots=programming-language-python). Du kan hitta de andra språken som är länkade på webbplatsen för dokument.

Bara för att klargöra för interaktiva, konversationer och diktering, Det här är ett avancerat sätt att ange på vilket sätt tjänsten ska hantera talfunktionerna. För lokal-behållare måste vi tyvärr ange hela URI (eftersom den innehåller en lokal dator), så den här informationen läcker från abstraktionen. Vi samarbetar med SDK-teamet för att göra detta mer användbart i framtiden.

<br>
</details>

<details>
<summary>
<b>Hur kan vi mäta ett grovt mått på transaktioner/sekund/kärna?</b>
</summary>

**Svar:** Här följer några av de hårda siffror som förväntas från befintlig modell (kommer att ändras för den som vi kommer att leverera i GA):

- För filer visas begränsningen i tal-SDK: n i 2x. De första fem sekunderna ljudet begränsas inte. Avkodaren kan göra ungefär 3x i real tid. För detta är den totala processor användningen nära 2 kärnor för ett enda igenkännings resultat.
- För MIC är det klockan 1x real tid. Den övergripande användningen bör vara ungefär 1 kärna för en enda igenkänning.

Detta kan alla verifieras från Docker-loggarna. Vi har faktiskt dumpat raden med sessions-och fras-/uttryck-statistik och innehåller RTF-nummer.

<br>
</details>

<details>
<summary>
<b>Hur gör jag för att vill du köra flera behållare på samma värd?</b>
</summary>

Dokumentet säger att du vill exponera en annan port, vilket jag gör, men LUIS-behållaren lyssnar fortfarande på port 5000?

**Svar:** Försök `-p <outside_unique_port>:5000` . Till exempel `-p 5001:5000`.


<br>
</details>

## <a name="technical-questions"></a>Tekniska frågor

<details>
<summary>
<b>Hur kan jag få API: er som inte är batch-grupper för att hantera ljud &lt; 15 sekunder längre?</b>
</summary>

**Svar:** `RecognizeOnce()` i interaktivt läge körs bara upp till 15 sekunders ljud, eftersom läget är avsett för tal kommando, där yttranden förväntas vara korta. Om du använder `StartContinuousRecognition()` för diktering eller konversation finns det ingen gräns på 15 sekunder.


<br>
</details>

<details>
<summary>
<b>Vilka är de rekommenderade resurserna, processorn och RAM-minnet; för 50 samtidiga förfrågningar?</b>
</summary>

Hur många samtidiga begär Anden kommer 4 kärnor, 4 GB RAM-referens? Om vi till exempel måste betjäna 50 samtidiga förfrågningar, hur många kärnor och RAM-minne rekommenderas?

**Svar:** I real tid `en-US` rekommenderar vi att du använder fler Docker-behållare utöver 6 samtidiga begär Anden. Den får Crazier bortom 16 kärnor och den blir icke-enhetlig, icke-enhetlig minnes åtkomst (NUMA)-nod känslig. I följande tabell beskrivs den lägsta och rekommenderade fördelningen av resurser för varje tal behållare.

# <a name="speech-to-text"></a>[Tal till text](#tab/stt)

| Container      | Minimum             | Rekommenderas         |
|----------------|---------------------|---------------------|
| Tal till text | 2 kärnor, 2 GB minne | 4 kärnor, 4 GB minne |

# <a name="custom-speech-to-text"></a>[Custom Speech till text](#tab/cstt)

| Container             | Minimum             | Rekommenderas         |
|-----------------------|---------------------|---------------------|
| Custom Speech till text | 2 kärnor, 2 GB minne | 4 kärnor, 4 GB minne |

# <a name="text-to-speech"></a>[Text till tal](#tab/tts)

| Container      | Minimum             | Rekommenderas         |
|----------------|---------------------|---------------------|
| Text till tal | 1 kärna, 2 GB minne | 2 kärnor, 3 GB minne |

# <a name="custom-text-to-speech"></a>[Anpassad text till tal](#tab/ctts)

| Container             | Minimum             | Rekommenderas         |
|-----------------------|---------------------|---------------------|
| Anpassad text till tal | 1 kärna, 2 GB minne | 2 kärnor, 3 GB minne |

***

- Varje kärna måste vara minst 2,6 GHz eller snabbare.
- För filer är begränsningen i talet SDK, i 2x (de första 5 sekunderna ljudet är inte begränsat).
- Avkodaren kan utföra cirka 2 – tre gånger i real tid. För detta är den totala processor användningen nära två kärnor för ett enda igenkänning. Därför rekommenderar vi inte att du behåller fler än två aktiva anslutningar per behållar instans. Den yttersta sidan skulle vara att lagra 10 avkodare i 2x real tid på en åtta kärnor som `DS13_V2` . För container version 1,3 och senare finns det en param som du kan prova att ställa in `DECODER_MAX_COUNT=20` .
- För mikrofonen är den på 1x real tid. Den övergripande användningen bör vara på ungefär en kärna för en enda igenkänning.

Ta hänsyn till det totala antalet timmar av ljudet som du har. Om talet är stort, rekommenderar vi att du kör fler instanser av behållare, antingen i en enda ruta eller i flera rutor, bakom en belastningsutjämnare. Dirigering kan utföras med hjälp av Kubernetes (K8S) och Helm, eller med Docker Compose.

Som exempel, för att hantera 1000 timmar/24 timmar, har vi försökt konfigurera 3-4 virtuella datorer med 10 instanser/avkodare per virtuell dator.

<br>
</details>

<details>
<summary>
<b>Stöder tal containern skiljetecken?</b>
</summary>

**Svar:** Vi har versaler (REDUNDANSVÄXLINGAR) tillgängliga i lokal-behållaren. Interpunktion är språk beroende och stöds inte för vissa språk, inklusive kinesiska och japanska.

Vi *har* stöd för implicit och grundläggande interpunktion för befintliga behållare, men är `off` som standard. Det innebär att du kan hämta `.` specialtecknet i ditt exempel, men inte på- `。` tecknen. För att aktivera den här implicita logiken är här ett exempel på hur du gör i python med vårt tal-SDK (det skulle vara detsamma på andra språk):

```python
speech_config.set_service_property(
    name='punctuation',
    value='implicit',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

<br>
</details>

<details>
<summary>
<b>Varför får jag 404 fel vid försök att skicka data till en behållare med tal till text?</b>
</summary>

Här är ett exempel på ett HTTP-inlägg:

```http
POST /speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Transfer-Encoding: chunked
User-Agent: PostmanRuntime/7.18.0
Cache-Control: no-cache
Postman-Token: xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Host: 10.0.75.2:5000
Accept-Encoding: gzip, deflate
Content-Length: 360044
Connection: keep-alive
HTTP/1.1 404 Not Found
Date: Tue, 22 Oct 2019 15:42:56 GMT
Server: Kestrel
Content-Length: 0
```

**Svar:** Vi stöder inte REST API i en "tal-till-text"-behållare, vi har bara stöd för WebSockets via talet SDK. Läs alltid den officiella dokumentationen i [fråga förutsägelse-slutpunkter](speech-container-howto.md#query-the-containers-prediction-endpoint).

<br>
</details>


<details>
<summary>
<b> Varför körs behållaren som en användare som inte är en rot? Vilka problem kan uppstå på grund av detta?</b>
</summary>

**Svar:** Observera att standard användaren i behållaren är en icke-rot användare. Detta ger skydd mot processer som hoppar över behållaren och hämtar eskalerade behörigheter på noden värd. Som standard gör vissa plattformar som OpenShift container Platform redan detta genom att köra behållare med ett godtyckligt tilldelat användar-ID. För dessa plattformar måste den icke-rot användaren ha behörighet att skriva till en externt mappad volym som kräver skrivningar. Till exempel en mapp för loggning eller en anpassad modell hämtnings mapp.
<br>
</details>

<details>
<summary>
<b>Varför får jag det här felet när jag använder tal-till-text-tjänsten?</b>
</summary>

```cmd
Error in STT call for file 9136835610040002161_413008000252496:
{
    "reason": "ResultReason.Canceled",
    "error_details": "Due to service inactivity the client buffer size exceeded. Resetting the buffer. SessionId: xxxxx..."
}
```

**Svar:** Detta inträffar vanligt vis när du matar in ljudet snabbare än genom att använda tal igenkännings behållaren. Klientens buffertar fyller upp och annulleringen utlöses. Du måste kontrol lera samtidigheten och RTF-filen där du skickar ljudet.

<br>
</details>

<details>
<summary>
<b>Kan du förklara dessa text till tal-behållar fel från C++-exemplen?</b>
</summary>

**Svar:** Om behållar versionen är äldre än 1,3 ska den här koden användas:

```cpp
const auto endpoint = "http://localhost:5000/speech/synthesize/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

Äldre behållare har inte den nödvändiga slut punkten för att kol ska fungera med `FromHost` API: et. Om de behållare som används för version 1,3 ska den här koden användas:

```cpp
const auto host = "http://localhost:5000";
auto config = SpeechConfig::FromHost(host);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

Nedan visas ett exempel på hur du använder `FromEndpoint` API: et:

```cpp
const auto endpoint = "http://localhost:5000/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text2}}}").get();
```

 `SetSpeechSynthesisVoiceName`Funktionen anropas eftersom det krävs röst namnet för behållarna med en uppdaterad text till tal-motor.

<br>
</details>

<details>
<summary>
<b>Hur kan jag använda v 1.7 av tal-SDK: n med en tal behållare?</b>
</summary>

**Svar:** Det finns tre slut punkter i tal behållaren för olika användnings områden, de definieras som tallägen – se nedan:

## <a name="speech-modes"></a>Tallägen

[!INCLUDE [speech-modes](includes/speech-modes.md)]

De är av olika syfte och används på olika sätt.

Python- [exempel](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py):
- För enkel igenkänning (interaktivt läge) med en anpassad slut punkt (det vill säga `SpeechConfig` med en slut punkt parameter), se `speech_recognize_once_from_file_with_custom_endpoint_parameters()` .
- För kontinuerlig igenkänning (konversations läge) och bara ändra för att använda en anpassad slut punkt som ovan, se `speech_recognize_continuous_from_file()` .
- Om du vill aktivera diktering i exempel som ovan (endast om du verkligen behöver det), högerklickar du efter att du `speech_config` har skapat, lagt till kod `speech_config.enable_dictation()` .

I C# för att aktivera diktering anropar du `SpeechConfig.EnableDictation()` funktionen.

### <a name="fromendpoint-apis"></a>`FromEndpoint` N
| Språk | API-information |
|----------|:------------|
| C++ | <a href="https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#fromendpoint" target="_blank">`SpeechConfig::FromEndpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| C# | <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromendpoint" target="_blank">`SpeechConfig.FromEndpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Java | <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.fromendpoint" target="_blank">`SpeechConfig.fromendpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Objective-C | <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithendpoint" target="_blank">`SPXSpeechConfiguration:initWithEndpoint;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Python | <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig" target="_blank">`SpeechConfig;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| JavaScript | Stöds inte för närvarande eller är inte planerat. |

<br>
</details>

<details>
<summary>
<b>Hur kan jag använda v 1.8 i tal-SDK: n med en tal behållare?</b>
</summary>

**Svar:** Det finns ett nytt `FromHost` API. Detta ersätter eller ändrar inte några befintliga API: er. Det lägger bara till ett alternativt sätt att skapa en tal konfiguration med hjälp av en anpassad värd.

### <a name="fromhost-apis"></a>`FromHost` N

| Språk | API-information |
|--|:-|
| C# | <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost" target="_blank">`SpeechConfig.FromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| C++ | <a href="https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#fromhost" target="_blank">`SpeechConfig::FromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Java | <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.fromhost" target="_blank">`SpeechConfig.fromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Objective-C | <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithhost" target="_blank">`SPXSpeechConfiguration:initWithHost;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Python | <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig" target="_blank">`SpeechConfig;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| JavaScript | Stöds för närvarande inte |

> Parametrar: Host (obligatorisk), prenumerations nyckel (valfritt, om du kan använda tjänsten utan den).

Formatet för värden är `protocol://hostname:port` där `:port` är det valfria (se nedan):
- Om behållaren körs lokalt är värd namnet `localhost` .
- Om behållaren körs på en fjärrserver använder du värd namnet eller IPv4-adressen för den servern.

Värd parameter exempel för tal till text:
- `ws://localhost:5000` -osäker anslutning till en lokal behållare med port 5000
- `ws://some.host.com:5000` -osäker anslutning till en behållare som körs på en fjärrserver

Python-exempel från ovan, men Använd `host` parameter i stället för `endpoint` :

```python
speech_config = speechsdk.SpeechConfig(host="ws://localhost:5000")
```

<br>
</details>

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Cognitive Services behållare](speech-container-howto.md)