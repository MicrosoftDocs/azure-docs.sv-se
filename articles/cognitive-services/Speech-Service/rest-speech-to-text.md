---
title: Tal-till-text API-referens (REST) – tal service
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder tal-till-text-REST API. I den här artikeln får du lära dig om behörighets alternativ, frågealternativ, hur du strukturerar en begäran och får svar.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/08/2021
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 24cc7773d281d98b6a0943f5e0a61b6f547991ee
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102032831"
---
# <a name="speech-to-text-rest-api"></a>REST API för tal-till-text

Tal till text har två olika REST-API: er. Varje API hanterar sitt särskilda syfte och använder olika uppsättningar av slut punkter.

REST-API: er från tal till text är:
- [Tal-till-text REST API v 3.0](#speech-to-text-rest-api-v30) används för [batch-avskrifter](batch-transcription.md) och [Custom Speech](custom-speech-overview.md). v 3.0 är en [efterföljande aktivitet för v 2.0](./migrate-v2-to-v3.md).
- [Tal-till-text-REST API för kort ljud](#speech-to-text-rest-api-for-short-audio) används för att avskrifta online som ett alternativ till [tal-SDK](speech-sdk.md). Begär Anden som använder detta API kan bara skicka upp till 60 sekunders ljud per begäran. 

## <a name="speech-to-text-rest-api-v30"></a>Tal till text REST API v 3.0

Tal-till-text REST API v 3.0 används för [batch-avskrifter](batch-transcription.md) och [Custom Speech](custom-speech-overview.md). Om du behöver kommunicera med OnLine-avskriften via REST använder [du tal-till-text-REST API för kort ljud](#speech-to-text-rest-api-for-short-audio).

Använd REST API v 3.0 för att:
- Kopiera modeller till andra prenumerationer om du vill att kollegor ska ha åtkomst till en modell som du har skapat, eller i de fall där du vill distribuera en modell till mer än en region
- Skriv över data från en behållare (Mass avskrift) och ange flera URL: er för ljud filen
- Ladda upp data från Azure Storage konton genom att använda SAS-URI
- Hämta loggar per slut punkt om loggarna har begärts för den slut punkten
- Begär manifestet för de modeller som du skapar, i syfte att konfigurera lokala behållare

REST API v 3.0 innehåller sådana funktioner som:
- **Meddelanden – Webhooks**– alla aktiva processer för tjänsten har nu stöd för webhook-aviseringar. REST API v 3.0 tillhandahåller anrop så att du kan registrera dina webhookar där meddelanden skickas
- **Uppdatera modeller bakom slut punkter** 
- **Modell anpassning med flera data uppsättningar**– anpassa en modell med hjälp av flera data uppsättnings kombinationer av ljud-, språk-och uttals data
- **Ta med din egen lagring**– Använd dina egna lagrings konton för loggar, avskrifts-filer och andra data

Se exempel på hur du använder REST API v 3.0 med batch-avskriften i [den här artikeln](batch-transcription.md).

Om du använder tal-till-text REST API v 2.0, se hur du kan migrera till v 3.0 i [den här guiden](./migrate-v2-to-v3.md).

Se den fullständiga referensen för tal-till-text REST API v 3.0 [här](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0).

## <a name="speech-to-text-rest-api-for-short-audio"></a>Tal till text REST API för kort ljud

Som ett alternativ till [tal-SDK](speech-sdk.md)gör röst tjänsten det möjligt för dig att konvertera tal till text med hjälp av en REST API. Varje tillgänglig slut punkt är associerad med en region. Ditt program kräver en prenumerations nyckel för den slut punkt som du planerar att använda. REST API för kort ljud är mycket begränsat och bör endast användas i fall där [tal-SDK: n](speech-sdk.md) inte kan användas.

Tänk på följande innan du använder tal-till-text-REST API för kort ljud:

* Begär Anden som använder REST API för kort ljud och sändning av ljud direkt får bara innehålla upp till 60 sekunders ljud.
* Tal-till-text-REST API för kort ljud returnerar bara slutgiltiga resultat. Ofullständiga resultat har inte angetts.

Om det är ett krav för ditt program att skicka längre ljud kan du överväga att använda [tal-SDK](speech-sdk.md) eller [tal-till-text REST API v 3.0](#speech-to-text-rest-api-v30).

> [!TIP]
> I [den här artikeln](sovereign-clouds.md) finns Azure Government-och Azure Kina-slutpunkter.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

### <a name="regions-and-endpoints"></a>Regioner och slut punkter

Slut punkten för REST API för kort ljud har följande format:

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

Ersätt `<REGION_IDENTIFIER>` med den identifierare som matchar regionen för din prenumeration från den här tabellen:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> Språk parametern måste läggas till i URL: en för att undvika att ett HTTP-4xx-fel tas emot. Till exempel är språket inställt på amerikansk engelska med slut punkten västra USA: `https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US` .

### <a name="query-parameters"></a>Frågeparametrar

Dessa parametrar kan ingå i frågesträngen för REST-begäran.

| Parameter | Beskrivning | Obligatorisk/valfri |
|-----------|-------------|---------------------|
| `language` | Identifierar det talade språk som identifieras. Se [vilka språk som stöds](language-support.md#speech-to-text). | Obligatorisk |
| `format` | Anger resultat formatet. Godkända värden är `simple` och `detailed` . Enkla resultat inkluderar `RecognitionStatus` , `DisplayText` , `Offset` och `Duration` . Detaljerade svar innehåller fyra olika representationer av visnings text. Standardinställningen är `simple`. | Valfritt |
| `profanity` | Anger hur du hanterar svordomar i igenkännings resultat. Godkända värden är `masked` , som ersätter svordomar med asterisker, `removed` som tar bort alla svordomar från resultatet, eller `raw` som innehåller svordomarna i resultatet. Standardinställningen är `masked`. | Valfritt |
| `cid` | När du använder [Custom Speech Portal](./custom-speech-overview.md) för att skapa anpassade modeller kan du använda anpassade modeller via deras **slut punkts-ID** som finns på **distributions** sidan. Använd **slut punkts-ID** som argument för `cid` parametern frågesträng. | Valfritt |

### <a name="request-headers"></a>Begärandehuvuden

I den här tabellen listas obligatoriska och valfria sidhuvuden för begäran om tal till text.

|Huvud| Beskrivning | Obligatorisk/valfri |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | Din prenumerations nyckel för röst tjänst. | Antingen den här rubriken eller `Authorization` krävs. |
| `Authorization` | En autentiseringstoken föregås av ordet `Bearer` . Mer information finns i [Autentisering](#authentication). | Antingen den här rubriken eller `Ocp-Apim-Subscription-Key` krävs. |
| `Pronunciation-Assessment` | Anger parametrar för visning av uttal i igenkännings resultat, som utvärderar uttal av tal ingångar, med indikatorer på precision, Fluency, fullständighet osv. Den här parametern är en Base64-kodad JSON som innehåller flera detaljerade parametrar. Se [uttal av bedömnings parametrar](#pronunciation-assessment-parameters) för hur du skapar den här rubriken. | Valfritt |
| `Content-type` | Beskriver formatet och codecen för de angivna ljud data. Godkända värden är `audio/wav; codecs=audio/pcm; samplerate=16000` och `audio/ogg; codecs=opus` . | Obligatorisk |
| `Transfer-Encoding` | Anger att segmenterade ljud data ska skickas i stället för en enda fil. Använd endast den här rubriken om du segmenterar ljuddata. | Valfritt |
| `Expect` | Skicka om du använder segmenterad överföring `Expect: 100-continue` . Tal tjänsten bekräftar den första begäran och väntar på ytterligare data.| Krävs om du skickar segmenterade ljud data. |
| `Accept` | Om det anges måste det vara `application/json` . Tal tjänsten ger resultat i JSON. Vissa ramverk för begäran tillhandahåller ett inkompatibelt standardvärde. Det är en bra idé att alltid inkludera `Accept` . | Valfritt, men rekommenderas. |

### <a name="audio-formats"></a>Ljud format

Ljud skickas i bröd texten i HTTP- `POST` begäran. Det måste vara i något av formaten i den här tabellen:

| Format | ADPCM | Bit hastighet | Samplings frekvens  |
|--------|-------|----------|--------------|
| WAV    | PCM   | 256 kbps | 16 kHz, mono |
| OGG    | OPUS  | 256 Kpbs | 16 kHz, mono |

>[!NOTE]
>Ovanstående format stöds via REST API för kort ljud och WebSocket i tal tjänsten. [Talet SDK](speech-sdk.md) stöder för närvarande WAV-formatet med PCM-kodek och [andra format](how-to-use-codec-compressed-audio-input-streams.md).

### <a name="pronunciation-assessment-parameters"></a>Parametrar för uttal-bedömning

I den här tabellen listas obligatoriska och valfria parametrar för uttal-utvärdering.

| Parameter | Beskrivning | Obligatoriskt? |
|-----------|-------------|---------------------|
| ReferenceText | Texten som uttalet kommer att utvärderas mot. | Obligatorisk |
| GradingSystem | Punkt systemet för resultat kalibrering. `FivePoint`Systemet ger ett flytt ALS värde på 0-5 och `HundredMark` ger en 0-100 flytt ALS poäng. Standard: `FivePoint`. | Valfritt |
| Precision | Utvärderings precisionen. Godkända värden är `Phoneme` , som visar poängen på den fullständiga text-, Word-och fonem-nivån, `Word` som visar poängen på den fullständiga text-och ord nivån, `FullText` som bara visar poängen på hela text nivån. Standardinställningen är `Phoneme`. | Valfritt |
| Dimension | Definierar kriterierna för utdata. Godkända värden är `Basic` , som bara visar noggrannhets poängen, `Comprehensive` visar poängen på fler dimensioner (t. ex. Fluency Poäng och total poäng på nivån full text nivå, fel typ på ord nivå). Kontrol lera [svars parametrar](#response-parameters) för att se definitioner av olika Poäng dimensioner och ord fel typer. Standardinställningen är `Basic`. | Valfritt |
| EnableMiscue | Aktiverar miscue-beräkning. Med det här alternativet kommer uttalade ord att jämföras med referens texten och markeras med utelämnanden/infogning baserat på jämförelsen. Godkända värden är `False` och `True` . Standardinställningen är `False`. | Valfritt |
| ScenarioId | Ett GUID som anger ett anpassat punkt system. | Valfritt |

Nedan visas ett exempel på en JSON som innehåller parametrarna för uttal-utvärdering:

```json
{
  "ReferenceText": "Good morning.",
  "GradingSystem": "HundredMark",
  "Granularity": "FullText",
  "Dimension": "Comprehensive"
}
```

Följande exempel kod visar hur du skapar uttal-bedömnings parametrarna i `Pronunciation-Assessment` rubriken:

```csharp
var pronAssessmentParamsJson = $"{{\"ReferenceText\":\"Good morning.\",\"GradingSystem\":\"HundredMark\",\"Granularity\":\"FullText\",\"Dimension\":\"Comprehensive\"}}";
var pronAssessmentParamsBytes = Encoding.UTF8.GetBytes(pronAssessmentParamsJson);
var pronAssessmentHeader = Convert.ToBase64String(pronAssessmentParamsBytes);
```

Vi rekommenderar starkt strömning (segmenterad) uppladdning när du skickar ljuddata, vilket kan minska svars tiden avsevärt. Se [exempel kod i olika programmeringsspråk](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/PronunciationAssessment) för att aktivera strömning.

>[!NOTE]
>Funktionen för ututtals bedömning är för närvarande bara tillgänglig på `en-US` språket.

### <a name="sample-request"></a>Exempel förfrågan

Exemplet nedan innehåller värd namnet och de huvuden som krävs. Det är viktigt att Observera att tjänsten också förväntar sig ljud data, som inte ingår i det här exemplet. Som nämnts tidigare rekommenderas Chunking, men det är inte obligatoriskt.

```HTTP
POST speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.stt.speech.microsoft.com
Transfer-Encoding: chunked
Expect: 100-continue
```

Om du vill aktivera uttal av uttal kan du lägga till under rubrik. Se [uttal av bedömnings parametrar](#pronunciation-assessment-parameters) för hur du skapar den här rubriken.

```HTTP
Pronunciation-Assessment: eyJSZWZlcm...
```

### <a name="http-status-codes"></a>HTTP-statuskoder

HTTP-statuskoden för varje svar visar att de lyckas eller vanliga fel.

| HTTP-statuskod | Beskrivning | Möjlig orsak |
|------------------|-------------|-----------------|
| `100` | Fortsätt | Den första begäran har accepterats. Fortsätt med att skicka resten av data. (Används med segmenterad överföring) |
| `200` | OK | Begäran lyckades. svars texten är ett JSON-objekt. |
| `400` | Felaktig begäran | Språk koden har inte angetts, inte ett språk som stöds, ogiltig ljudfil osv. |
| `401` | Behörighet saknas | Prenumerations nyckel eller autentiseringstoken är ogiltig i den angivna regionen eller ogiltig slut punkt. |
| `403` | Förbjudet | Prenumerations nyckel eller autentiseringstoken saknas. |

### <a name="chunked-transfer"></a>Segmenterad överföring

Segmenterad överföring ( `Transfer-Encoding: chunked` ) kan hjälpa till att minska svars tiden för igenkänning. Det gör att röst tjänsten kan börja bearbeta ljud filen medan den överförs. REST API för kort ljud ger inte partiella eller tillfälliga resultat.

I det här kod exemplet visas hur du skickar ljud i segment. Endast det första segmentet ska innehålla ljud filens rubrik. `request` är ett `HttpWebRequest` objekt som är kopplat till lämplig REST-slutpunkt. `audioFile` är sökvägen till en ljudfil på disk.

```csharp
var request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.Host = host;
request.ContentType = @"audio/wav; codecs=audio/pcm; samplerate=16000";
request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR_SUBSCRIPTION_KEY";
request.AllowWriteStreamBuffering = false;

using (var fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
{
    // Open a request stream and write 1024 byte chunks in the stream one at a time.
    byte[] buffer = null;
    int bytesRead = 0;
    using (var requestStream = request.GetRequestStream())
    {
        // Read 1024 raw bytes from the input audio file.
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        requestStream.Flush();
    }
}
```

### <a name="response-parameters"></a>Svars parametrar

Resultat tillhandahålls som JSON. `simple`Formatet innehåller dessa fält på den översta nivån.

| Parameter | Beskrivning  |
|-----------|--------------|
|`RecognitionStatus`|Status, till exempel `Success` för lyckad igenkänning. Se nästa tabell.|
|`DisplayText`|Den tolkade texten efter versaler, interpunktion, inverterad text normalisering (konvertering av talade text till kortare former, till exempel 200 för "200" eller "Dr. Smith" för "läkare Smith") och svordomar. Presentera bara på framgång.|
|`Offset`|Den tid (i 100 – nanosekunder) som det identifierade talet börjar i ljud strömmen.|
|`Duration`|Varaktigheten (i 100 – nanosekunder) av tal som identifieras i ljud strömmen.|

`RecognitionStatus`Fältet kan innehålla följande värden:

| Status | Beskrivning |
|--------|-------------|
| `Success` | Igenkänningen lyckades och `DisplayText` fältet finns. |
| `NoMatch` | Talet identifierades i ljud strömmen, men inga ord från mål språket matchades. Det innebär vanligt vis att tolknings språket är ett annat språk än det som användaren talar. |
| `InitialSilenceTimeout` | Starten av ljud strömmen innehöll bara tystnad, och tids gränsen för tjänsten nåddes i väntan på tal. |
| `BabbleTimeout` | Starten av ljud strömmen innehöll bara brus, och tids gränsen för tjänsten nåddes i väntan på tal. |
| `Error` | Ett internt fel inträffade i igenkännings tjänsten och det gick inte att fortsätta. Försök igen om möjligt. |

> [!NOTE]
> Om ljudet bara består av svordomar och `profanity` frågeparametern är inställt på `remove` , returnerar inte tjänsten ett tal resultat.

`detailed`Formatet innehåller ytterligare former av identifierade resultat.
När du använder `detailed` formatet `DisplayText` anges det som `Display` för varje resultat i `NBest` listan.

Objektet i `NBest` listan kan innehålla:

| Parameter | Beskrivning |
|-----------|-------------|
| `Confidence` | Förtroende poängen för posten från 0,0 (ingen exakthet) till 1,0 (full exakthet) |
| `Lexical` | Den tolkade textens lexikala text: de faktiska orden som identifieras. |
| `ITN` | Den tolkade textens ("kanoniska") form av den tolkade texten, med telefonnummer, siffror, förkortningar ("läkare Smith" till "Dr Smith") och andra transformeringar som tillämpas. |
| `MaskedITN` | REDUNDANSVÄXLINGAR-formuläret med anspråks mask tillämpas vid begäran. |
| `Display` | Visnings formatet för den tolkade texten, med interpunktion och Skift läge tillagda. Den här parametern är samma som `DisplayText` anges när format är inställt på `simple` . |
| `AccuracyScore` | Talts uttal. Noggrannhet anger hur nära fonem som matchar en ursprunglig talares uttal. Precisions poängen för ord och full text nivå sammanställs från precisions poängen på fonem nivå. |
| `FluencyScore` | Fluency för det tal som angetts. Fluency anger hur tätt talet ska matcha en inbyggd högtalares användning av tyst raster mellan ord. |
| `CompletenessScore` | Talet är klart och bestäms genom att beräkna förhållandet mellan uttalade ord och referenser till text ingångar. |
| `PronScore` | Totalt antal poäng som anger uttal av det tal som anges. Detta sammanställs från `AccuracyScore` `FluencyScore` och `CompletenessScore` med vikt. |
| `ErrorType` | Det här värdet anger om ett ord utelämnas, infogas eller blir dåligt uttalad jämfört med `ReferenceText` . Möjliga värden är `None` (vilket innebär inget fel på det här ordet), `Omission` `Insertion` och `Mispronunciation` . |

### <a name="sample-responses"></a>Exempel svar

Ett typiskt svar för `simple` igenkänning:

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Ett typiskt svar för `detailed` igenkänning:

```json
{
  "RecognitionStatus": "Success",
  "Offset": "1236645672289",
  "Duration": "1236645672289",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "remind me to buy five pencils",
        "ITN" : "remind me to buy 5 pencils",
        "MaskedITN" : "remind me to buy 5 pencils",
        "Display" : "Remind me to buy 5 pencils.",
      }
  ]
}
```

Ett typiskt svar för igenkänning av uttal av uttal:

```json
{
  "RecognitionStatus": "Success",
  "Offset": "400000",
  "Duration": "11000000",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "good morning",
        "ITN" : "good morning",
        "MaskedITN" : "good morning",
        "Display" : "Good morning.",
        "PronScore" : 84.4,
        "AccuracyScore" : 100.0,
        "FluencyScore" : 74.0,
        "CompletenessScore" : 100.0,
        "Words": [
            {
              "Word" : "Good",
              "AccuracyScore" : 100.0,
              "ErrorType" : "None",
              "Offset" : 500000,
              "Duration" : 2700000
            },
            {
              "Word" : "morning",
              "AccuracyScore" : 100.0,
              "ErrorType" : "None",
              "Offset" : 5300000,
              "Duration" : 900000
            }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>Nästa steg

- [Skapa ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/cognitive-services/)
- [Anpassa akustiska modeller](./how-to-custom-speech-train-model.md)
- [Anpassa språkmodeller](./how-to-custom-speech-train-model.md)
- [Bekanta dig med batch-avskriftering](batch-transcription.md)