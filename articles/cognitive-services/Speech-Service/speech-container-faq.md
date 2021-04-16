---
title: Vanliga frågor och svar om Speech Service-containrar
titleSuffix: Azure Cognitive Services
description: Installera och köra Speech-containrar. tal till text transkriberar ljudströmmar till text i realtid som dina program, verktyg eller enheter kan använda eller visa. Text till tal konverterar indatatext till människoliknande syntetiserat tal.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2021
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 28a044f42d0774d940521964b68b38a0f35bcdbb
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107387963"
---
# <a name="speech-service-containers-frequently-asked-questions-faq"></a>Vanliga frågor och svar om Speech Service-containrar

När du använder Speech-tjänsten med containrar måste du förlita dig på den här samlingen med vanliga frågor innan du eskalerar till supporten. Den här artikeln innehåller frågor med varierande grad, från allmän till teknisk. Om du vill expandera ett svar klickar du på frågan.

## <a name="general-questions"></a>Allmänna frågor

<details>
<summary>
<b>Hur fungerar Speech-containrar och hur ställer jag in dem?</b>
</summary>

**Svar:** När du ställer in produktionsklustret finns det flera saker att tänka på. För det första bör det inte vara något stort problem att konfigurera ett enda språk, flera containrar på samma dator. Om du har problem kan det vara ett maskinvarurelaterat problem, så vi tittar först på resursen, det vill säga: Specifikationer för processor och minne.

Överväg en stund, `ja-JP` containern och den senaste modellen. Den akustiska modellen är den mest krävande biten CPU-vis, medan språkmodellen kräver mest minne. När vi har benchmarkat användningen tar det cirka 0,6 CPU-kärnor för att bearbeta en enskild tal till text-begäran när ljud flödar in i realtid (t.ex. från mikrofonen). Om du matar in ljud snabbare än realtid (t.ex. från en fil) kan användningen fördubblas (1,2 x kärnor). Under tiden är det minne som anges nedan driftminne för avkodning av tal. Den tar *inte* hänsyn till den faktiska fullständiga storleken på språkmodellen, som kommer att finnas i filcachen. För `ja-JP` det är ytterligare 2 GB, för kan det vara mer `en-US` (6–7 GB).

Om du har en dator där minnet är begränsade och du försöker distribuera flera språk på den, är det möjligt att filcachen är full och operativsystemet tvingas att sidin- och utser modeller. För en transkription som körs kan det vara dåligt och kan leda till långsammare och andra prestandakonsekvenser.

Dessutom paketerar vi körbara filer i förväg för datorer med [instruktionsuppsättningen advanced vector extension (AVX2).](speech-container-howto.md#advanced-vector-extension-support) En dator med AVX512-instruktionsuppsättningen kräver kodgenerering för målet, och om du startar 10 containrar för 10 språk kan CPU-användningen tillfälligt ta slut. Ett meddelande som det här visas i Docker-loggarna:

```console
2020-01-16 16:46:54.981118943 
[W:onnxruntime:Default, tvm_utils.cc:276 LoadTVMPackedFuncFromCache]
Cannot find Scan4_llvm__mcpu_skylake_avx512 in cache, using JIT...
```

Du kan ange antalet avkodare som du vill ha i en *enda* container med hjälp av `DECODER MAX_COUNT` variabeln . I princip bör vi därför börja med din SKU (CPU/minne) och vi kan föreslå hur du får ut det bästa av den. En bra startpunkt är att referera till de rekommenderade resursspecifikationerna för värddatorn.

<br>
</details>

<details>
<summary>
<b>Kan du hjälpa till med kapacitetsplanering och kostnadsuppskattning av lokala tal till text-containrar?</b>
</summary>

**Svar:** För containerkapacitet i batchbearbetningsläge kan varje avkodare hantera 2–3x i realtid, med två PROCESSORkärnor, för en enda igenkänning. Vi rekommenderar inte att du behåller fler än två samtidiga igenkänningar per containerinstans, men rekommenderar att du kör fler instanser av containrar av tillförlitlighets-/tillgänglighetsskäl bakom en lastbalanserare.

Vi skulle kunna köra varje containerinstans med fler avkodare. Vi kan till exempel konfigurera 7 avkodare per containerinstans på en åttakärnig dator (vid mer än 2 x vardera), vilket ger 15 x dataflöde. Det finns en parameter `DECODER_MAX_COUNT` som du bör känna till. I extrema fall uppstår problem med tillförlitlighet och svarstider, där dataflödet ökar avsevärt. För en mikrofon är den i 1 x realtid. Den totala användningen bör vara ungefär en kärna för en enda igenkänning.

För scenariot med bearbetning 1 000 timmar/dag i batchbearbetningsläge kan 3 virtuella datorer i extrema fall hantera det inom 24 timmar men inte garanteras. För att hantera toppdagar, redundans, uppdatering och minsta säkerhetskopiering/BCP rekommenderar vi 4–5 datorer i stället för 3 per kluster och med 2+ kluster.

För maskinvara använder vi den virtuella Azure-standarddatorn som referens (varje kärna måste vara `DS13_v2` 2,6 GHz eller bättre, med AVX2-instruktionsuppsättning aktiverad).

| Instans  | vCPU:er | RAM    | Temporär lagring | Betala i taget med AHB | 1 års reserv med AHB (% besparingar) | Tre års reserverad med AHB (% besparingar) |
|-----------|---------|--------|--------------|------------------------|-------------------------------------|--------------------------------------|
| `DS13 v2` | 8       | 56 GiB | 112 GiB      | 0,598 USD/timme            | 0,3528 USD/timme (~41 %)                 | 0,2333 USD/timme (~61 %)                  |

Baserat på designreferensen (två kluster med 5 virtuella datorer för att hantera 1 000 timmar/dag för bearbetning av ljudbatch) blir kostnaden för 1 års maskinvara:

> 2 (kluster) * 5 (virtuella datorer per kluster) * 0,3528 USD/timme * 365 (dagar) * 24 (timmar) = 31 000 USD/år

Vid mappning till fysisk dator är en allmän uppskattning 1 virtuell processor = 1 fysisk processorkärna. I verkligheten är 1vCPU kraftfullare än en enda kärna.

För den som är på plats spelar alla dessa ytterligare faktorer in:

- På vilken typ den fysiska processorn är och hur många kärnor den har
- Hur många processorer som körs tillsammans på samma box/dator
- Hur virtuella datorer konfigureras
- Hur hypertrådning/flertråding används
- Så här delas minne
- Operativsystemet osv.

Normalt är den inte lika väl inställd som Azure-miljön. Med tanke på andra kostnader skulle jag säga att en säker uppskattning är 10 fysiska CPU-kärnor = 8 virtuella Azure-processorer. Även om populära processorer bara har åtta kärnor. Med en distribution på plats blir kostnaden högre än för virtuella Azure-datorer. Ta även hänsyn till avskrivningstakten.

Tjänstkostnaden är samma som onlinetjänsten: 1 USD/timme för tal till text. Taltjänstens kostnad är:

> 1 USD * 1 000 * 365 = 365 000 USD

Underhållskostnaden som betalas till Microsoft beror på tjänstens servicenivå och innehåll. Det kan vara från 29,99 USD/månad för grundläggande nivå till hundratusentals om tjänsten på plats ingår. Ett ungefärligt tal är 300 USD/timme för tjänst/underhåll. Kostnaden för personer ingår inte. Andra infrastrukturkostnader (till exempel lagring, nätverk och lastbalanserare) ingår inte.

<br>
</details>

<details>
<summary>
<b>Varför saknas skiljetecken i transkriptionen?</b>
</summary>

**Svar:** `speech_recognition_language=<YOUR_LANGUAGE>` bör uttryckligen konfigureras i begäran om de använder Carbon-klienten.

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
<b>Kan jag använda en anpassad akustisk modell och språkmodell med Speech-container?</b>
</summary>

För närvarande kan vi bara skicka ett modell-ID, antingen en anpassad språkmodell eller en anpassad akustisk modell.

**Svar:** Beslutet att inte *stödja* både akustiska modeller och språkmodeller samtidigt togs. Detta gäller tills en enhetlig identifierare skapas för att minska API-avbrott. Detta stöds tyvärr inte just nu.

<br>
</details>

<details>
<summary>
<b>Kan du förklara de här felen från den anpassade tal till text-containern?</b>
</summary>

**Fel 1:**

```cmd
Failed to fetch manifest: Status: 400 Bad Request Body:
{
    "code": "InvalidModel",
    "message": "The specified model is not supported for endpoint manifests."
}
```

**Svar 1:** Om du tränar med den senaste anpassade modellen stöder vi för närvarande inte det. Om du tränar med en äldre version bör det vara möjligt att använda. Vi arbetar fortfarande med att stödja de senaste versionerna.

De anpassade containrarna stöder i princip inte Halogenide eller ONNX-baserade akustiska modeller (vilket är standard i den anpassade träningsportalen). Detta beror på att anpassade modeller inte krypteras och vi vill dock inte exponera ONNX-modeller. språkmodeller fungerar bra. Kunden måste uttryckligen välja en äldre icke-ONNX-modell för anpassad utbildning. Noggrannheten påverkas inte. Modellstorleken kan vara större (med 100 MB).

> Stödmodell > 20190220 (v4.5 Unified)

**Fel 2:**

```cmd
HTTPAPI result code = HTTPAPI_OK.
HTTP status code = 400.
Reason:  Synthesis failed.
StatusCode: InvalidArgument,
Details: Voice does not match.
```

**Svar 2:** Du måste ange rätt röstnamn i begäran, vilket är ärendekänsligt. Se den fullständiga tjänstnamnmappningen.

**Fel 3:**

```json
{
    "code": "InvalidProductId",
    "message": "The subscription SKU \"CognitiveServices.S0\" is not supported in this service instance."
}
```

**Svar 3:** Du skapade för att skapa en Speech-resurs, inte en Cognitive Services resurs.


<br>
</details>

<details>
<summary>
<b>Vilka API-protokoll stöds, REST eller WS?</b>
</summary>

**Svar:** För tal till text-containrar och anpassade tal till text-containrar stöder vi för närvarande endast websocket-baserat protokoll. SDK stöder endast anrop i WS men inte REST. Det finns en plan för att lägga till REST-stöd, men inte ETA för tillfället. Läs alltid den officiella dokumentationen. Se slutpunkter [för frågeförutsägelse.](speech-container-howto.md#query-the-containers-prediction-endpoint)

<br>
</details>

<details>
<summary>
<b>Stöds CentOS för Speech-containrar?</b>
</summary>

**Svar:** CentOS 7 stöds inte av Python SDK ännu, och Ubuntu 19.04 stöds inte.

Python Speech SDK-paketet är tillgängligt för dessa operativsystem:
- **Windows** – x64 och x86
- **Mac** – macOS X version 10.12 eller senare
- **Linux** – Ubuntu 16.04, Ubuntu 18.04, Debian 9 på x64

Mer information om miljökonfiguration finns i [Python-plattformskonfiguration.](quickstarts/setup-platform.md?pivots=programming-language-python) För tillfället är Ubuntu 18.04 den rekommenderade versionen.

<br>
</details>

<details>
<summary>
<b>Varför får jag fel när jag försöker anropa LUIS-förutsägelseslutpunkter?</b>
</summary>

Jag använder LUIS-containern i en IoT Edge-distribution och försöker anropa LUIS-slutpunkten för förutsägelse från en annan container. LUIS-containern lyssnar på port 5001 och url:en jag använder är följande:

```csharp
var luisEndpoint =
    $"ws://192.168.1.91:5001/luis/prediction/v3.0/apps/{luisAppId}/slots/production/predict";
var config = SpeechConfig.FromEndpoint(new Uri(luisEndpoint));
```

Felet jag får är:

```cmd
WebSocket Upgrade failed with HTTP status code: 404 SessionId: 3cfe2509ef4e49919e594abf639ccfeb
```

Jag ser begäran i LUIS-containerloggarna och meddelandet säger:

```cmd
The request path /luis//predict" does not match a supported file type.
```

Vad betyder detta? Vad saknas? Jag följde exemplet för Speech SDK, [härifrån](https://github.com/Azure-Samples/cognitive-services-speech-sdk). Scenariot är att vi identifierar ljudet direkt från datorns mikrofon och försöker fastställa avsikten baserat på DEN LUIS-app som vi tränade. Det exempel som jag har länkat till gör exakt det. Och det fungerar bra med den molnbaserade LUIS-tjänsten. Med hjälp av Speech SDK verkade det vara så att vi inte behöver göra ett separat explicit anrop till API:et för tal till text och sedan ett andra anrop till LUIS.

Allt jag försöker göra är att växla från scenariot med att använda LUIS i molnet till att använda LUIS-containern. Jag kan inte föreställa mig om Speech SDK fungerar för den ena, den fungerar inte för den andra.

**Svar:** Speech SDK bör inte användas mot en LUIS-container. För att använda LUIS-containern bör LUIS SDK eller LUIS REST API användas. Speech SDK ska användas mot en speech-container.

Ett moln skiljer sig från en container. Ett moln kan bestå av flera aggregerade containrar (kallas ibland mikrotjänster). Det finns alltså en LUIS-container och sedan finns det en Speech-container – Två separata containrar. Speech-containern gör bara tal. LUIS-containern gör bara LUIS. Eftersom båda containrarna är kända för att distribueras i molnet och det är dåliga prestanda för en fjärrklient att gå till molnet, tala, komma tillbaka och sedan gå till molnet igen och göra LUIS, tillhandahåller vi en funktion som gör att klienten kan gå till Tal, stanna kvar i molnet, gå till LUIS och sedan gå tillbaka till klienten. Även i det här scenariot går Speech SDK till Speech-molncontainern med ljud och sedan pratar Speech-molncontainern med LUIS-molncontainern med text. LUIS-containern har inget koncept för att acceptera ljud (det skulle inte vara meningsfullt för LUIS-containern att acceptera strömmande ljud – LUIS är en textbaserad tjänst). Med lokal distribution har vi ingen säkerhet som vår kund har distribuerat båda containrarna, vi förutsätter inte att de orkestreras mellan containrar i våra kunders lokaler, och om båda containrarna distribueras lokalt, eftersom de är mer lokala för klienten, är det inte en belastning att gå SR först, tillbaka till klienten och be kunden sedan ta den texten och gå till LUIS.

<br>
</details>

<details>
<summary>
<b>Varför får vi fel med macOS, Speech-containern och Python SDK?</b>
</summary>

När vi skickar *en WAV-fil* som ska transkriberas kommer resultatet tillbaka med:

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

Vi vet att websocket har ställts in korrekt.

**Svar:** Om så är fallet kan du se det [här GitHub-problemet.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310) Vi har en arbetsyt som [föreslås här.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310#issuecomment-527542722)

Kol har åtgärdat detta i version 1.8.


<br>
</details>

<details>
<summary>
<b>Vilka skillnader finns det i speech-containerslutpunkterna?</b>
</summary>

Kan du hjälpa till att fylla i följande testmått, inklusive vilka funktioner som ska testas och hur du testar SDK och REST API:er? I synnerhet skillnader i "interaktiv" och "konversation", som jag inte såg i befintligt dokument/exempel.

| Slutpunkt                                                | Funktionellt test                                                   | SDK | REST-API |
|---------------------------------------------------------|-------------------------------------------------------------------|-----|----------|
| `/speech/synthesize/cognitiveservices/v1`               | Syntetisera text (text till tal)                                  |     | Ja      |
| `/speech/recognition/dictation/cognitiveservices/v1`    | Cognitive Services websocket-slutpunkt för on-prem dictation v1        | Ja | Inga       |
| `/speech/recognition/interactive/cognitiveservices/v1`  | Den Cognitive Services interaktiva v1-websocket-slutpunkten  |     |          |
| `/speech/recognition/conversation/cognitiveservices/v1` | Websocket-slutpunkten för cognitive services on-prem conversation v1 |     |          |

**Svar:** Det här är en blandning av:
- Personer som provar dikteringsslutpunkten för containrar (jag vet inte hur de fick den URL:en)
- Den första<sup>partslutpunkten</sup> är den i en container.
- Slutpunkten<sup>1:a</sup> part returnerar speech.fragment-meddelanden i stället för meddelandena som slutpunkterna i 3:e delen returnerar `speech.hypothesis` <sup></sup> för dikteringsslutpunkten.
- Carbon-snabbstarter använder alla `RecognizeOnce` (interaktivt läge)
- Kol som har en kontroll att `speech.fragment` för meddelanden som kräver att de inte returneras i interaktivt läge.
- Kol som ser till att fire in release-versioner (avlivar processen).

Lösningen är att antingen växla till att använda kontinuerlig igenkänning i koden eller (snabbare) ansluta till antingen de interaktiva eller kontinuerliga slutpunkterna i containern.
För din kod anger du slutpunkten till `host:port` /speech/recognition/interactive/cognitiveservices/v1

Information om de olika lägena finns i Tallägen – se nedan:

## <a name="speech-modes---interactive-conversation-dictation"></a>Tallägen – interaktiv, konversation, diktering

[!INCLUDE [speech-modes](includes/speech-modes.md)]

Rätt korrigering kommer med SDK 1.8, som har stöd för lokala enheter (väljer rätt slutpunkt, så vi blir inte sämre än onlinetjänsten). Under tiden finns det ett urval för kontinuerlig igenkänning. Varför pekar vi inte på det?

https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/6805d96bf69d9e95c9137fe129bc5d81e35f6309/samples/python/console/speech_sample.py#L196

<br>
</details>

<details>
<summary>
<b>Vilket läge ska jag använda för olika ljudfiler?</b>
</summary>

**Svar:** Här är en [snabbstart med Python](./get-started-speech-to-text.md?pivots=programming-language-python). Du hittar de andra språken som är länkade på dokumentwebbplatsen.

Bara för att klargöra för den interaktiva konversationen och diktering; Det här är ett avancerat sätt att ange på vilket sätt tjänsten ska hantera talbegäran. För de lokala containrarna måste vi tyvärr ange den fullständiga URI:n (eftersom den innehåller en lokal dator), så den här informationen läckte ut från abstraktionen. Vi arbetar med SDK-teamet för att göra detta mer användbart i framtiden.

<br>
</details>

<details>
<summary>
<b>Hur kan vi mäta ett ungefärligt mått på transaktioner/sekund/kärna?</b>
</summary>

**Svar:** Här är några av de ungefärliga talen att förvänta sig från den befintliga modellen (kommer att ändras till det bättre i den som vi kommer att leverera i GA):

- För filer kommer begränsningen att finnas i speech SDK med 2x. De första fem sekunderna av ljud begränsas inte. Avkodaren kan göra ungefär 3 x realtid. För detta kommer den totala CPU-användningen att vara nära 2 kärnor för en enda igenkänning.
- För mic är det i 1 x realtid. Den totala användningen bör vara på cirka 1 kärna för en enda igenkänning.

Allt detta kan verifieras från Docker-loggarna. Vi dumpar faktiskt raden med sessions- och fras-/tayttrandestatistik, och den innehåller RTF-talen.

<br>
</details>

<details>
<summary>
<b>Hur gör jag för att att flera containrar körs på samma värd?</b>
</summary>

Dokumentet säger att man ska exponera en annan port, vilket jag gör, men LUIS-containern lyssnar fortfarande på port 5000?

**Svar:** Prova `-p <outside_unique_port>:5000` . Till exempel `-p 5001:5000`.


<br>
</details>

## <a name="technical-questions"></a>Tekniska frågor

<details>
<summary>
<b>Hur kan jag få ICKE-batch-API:er att hantera &lt; ljud 15 sekunder långt?</b>
</summary>

**Svar:** `RecognizeOnce()` i interaktivt läge bearbetar endast upp till 15 sekunders ljud, eftersom läget är avsett för talkommandon där yttranden förväntas vara korta. Om du använder `StartContinuousRecognition()` för diktering eller konversation finns det ingen gräns på 15 sekunder.


<br>
</details>

<details>
<summary>
<b>Vilka är de rekommenderade resurserna, CPU och RAM- för 50 samtidiga begäranden?</b>
</summary>

Hur många samtidiga begäranden kommer en 4-kärnig, 4 GB RAM-minne att hantera? Hur många kärnor och RAM-minne rekommenderas om vi till exempel ska kunna skicka 50 samtidiga begäranden?

**Svar:** I realtid 8 med vår senaste , så `en-US` vi rekommenderar att du använder fler Docker-containrar utöver 6 samtidiga begäranden. Den blir mer eller mindre än 16 kärnor och blir nuMA-nodkänslig (icke-enhetlig minnesåtkomst). I följande tabell beskrivs den lägsta och rekommenderade allokeringen av resurser för varje Speech-container.

# <a name="speech-to-text"></a>[Tal till text](#tab/stt)

| Container      | Minimum             | Rekommenderas         |
|----------------|---------------------|---------------------|
| Tal till text | 2 kärnor, 2 GB minne | 4 kärnor, 4 GB minne |

# <a name="custom-speech-to-text"></a>[Anpassat tal till text](#tab/cstt)

| Container             | Minimum             | Rekommenderas         |
|-----------------------|---------------------|---------------------|
| Anpassat tal till text | 2 kärnor, 2 GB minne | 4 kärnors, 4 GB minne |

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
- För filer finns begränsningen i Speech SDK, 2 gånger (de första 5 sekunderna av ljud begränsas inte).
- Avkodaren kan göra ungefär 2–3 x realtid. För detta kommer den totala CPU-användningen att vara nära två kärnor för en enda igenkänning. Därför rekommenderar vi inte att du behåller fler än två aktiva anslutningar per containerinstans. Den extrema sidan skulle vara att placera cirka 10 avkodare i 2 x realtid i en dator med åtta kärnor som `DS13_V2` . För containerversion 1.3 och senare finns det en parameter som du kan prova att ange `DECODER_MAX_COUNT=20` .
- För mikrofon är den i 1 x realtid. Den övergripande användningen bör vara på ungefär en kärna för en enda igenkänning.

Ta hänsyn till det totala antalet timmar med ljud som du har. Om antalet är stort för att förbättra tillförlitligheten/tillgängligheten rekommenderar vi att du kör fler instanser av containrar, antingen på en enda ruta eller i flera rutor, bakom en lastbalanserare. Orkestrering kan göras med Kubernetes (K8S) och Helm, eller med Docker Compose.

För att hantera 1 000 timmar/24 timmar har vi till exempel försökt konfigurera 3–4 virtuella datorer, med 10 instanser/avkodare per virtuell dator.

<br>
</details>

<details>
<summary>
<b>Stöder Speech-containern skiljetecken?</b>
</summary>

**Svar:** Vi har tillgång till versaler (ITN) i den on-prem-containern. Skiljetecken är språkberoende och stöds inte för vissa språk, däribland kinesiska och japanska.

Vi *har* implicita och grundläggande skiljeteckensstöd för befintliga containrar, men det är `off` som standard. Det innebär att du kan hämta tecknet `.` i ditt exempel, men inte `。` tecknet . Om du vill aktivera den här implicita logiken är här ett exempel på hur du gör det i Python med hjälp av vår Speech SDK (det skulle vara liknande på andra språk):

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
<b>Varför får jag 404-fel när jag försöker skicka data till tal till text-container?</b>
</summary>

Här är ett exempel på HTTP POST:

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

**Svar:** Vi stöder inte REST API i någon av tal till text-containrarna, vi stöder bara WebSockets via Speech SDK. Läs alltid den officiella dokumentationen. Se slutpunkter [för frågeförutsägelse.](speech-container-howto.md#query-the-containers-prediction-endpoint)

<br>
</details>


<details>
<summary>
<b> Varför körs containern som en icke-rotanvändare? Vilka problem kan uppstå på grund av detta?</b>
</summary>

**Svar:** Observera att standardanvändaren i containern är en icke-rotanvändare. Detta ger skydd mot processer som undantagstillstånd för containern och att få eskalerade behörigheter på värdnoden. Som standard gör vissa plattformar som OpenShift Container Platform redan detta genom att köra containrar med ett godtyckligt tilldelat användar-ID. För dessa plattformar måste icke-rotanvändaren ha behörighet att skriva till en externt mappad volym som kräver skrivningar. Till exempel en loggningsmapp eller en nedladdningsmapp för en anpassad modell.
<br>
</details>

<details>
<summary>
<b>Varför får jag det här felet när jag använder tal till text-tjänsten?</b>
</summary>

```cmd
Error in STT call for file 9136835610040002161_413008000252496:
{
    "reason": "ResultReason.Canceled",
    "error_details": "Due to service inactivity the client buffer size exceeded. Resetting the buffer. SessionId: xxxxx..."
}
```

**Svar:** Detta inträffar vanligtvis när du matar in ljudet snabbare än containern för taligenkänning kan ta den. Klientbuffertar fylls och annulleringen utlöses. Du måste styra samtidigheten och RTF där du skickar ljudet.

<br>
</details>

<details>
<summary>
<b>Kan du förklara dessa text-till-tal-containerfel från C++-exemplen?</b>
</summary>

**Svar:** Om containerversionen är äldre än 1.3 ska den här koden användas:

```cpp
const auto endpoint = "http://localhost:5000/speech/synthesize/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

Äldre containrar har inte den slutpunkt som krävs för att Carbon ska fungera med `FromHost` API:et. Om containrarna som används för version 1.3 ska den här koden användas:

```cpp
const auto host = "http://localhost:5000";
auto config = SpeechConfig::FromHost(host);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

Nedan visas ett exempel på hur du använder `FromEndpoint` API:et:

```cpp
const auto endpoint = "http://localhost:5000/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text2}}}").get();
```

 Funktionen `SetSpeechSynthesisVoiceName` anropas eftersom containrarna med en uppdaterad text till tal-motor kräver röstnamnet.

<br>
</details>

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Cognitive Services containrar](speech-container-howto.md)
