---
title: Referens för text till tal-API (REST) – Taltjänst
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder text till tal-REST API. I den här artikeln får du lära dig mer om auktoriseringsalternativ, frågealternativ, hur du strukturerar en begäran och tar emot ett svar.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/08/2021
ms.author: trbye
ms.custom: references_regions
ms.openlocfilehash: 0065b6f4a7039e2883bca6acd5cf659be7b71069
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717683"
---
# <a name="text-to-speech-rest-api"></a>Text-till-tal (REST API)

Med speech-tjänsten kan du [konvertera](#convert-text-to-speech) text [](#get-a-list-of-voices) till syntetiserat tal och hämta en lista över röster som stöds för en region med hjälp av en uppsättning REST-API:er. Varje tillgänglig slutpunkt är associerad med en region. En prenumerationsnyckel för den slutpunkt/region som du planerar att använda krävs.

Text till tal-REST API neurala och standardröster för text till tal, som var och en stöder ett visst språk och en specifik dialekt som identifieras av språket.

* En fullständig lista över röster finns i [språkstöd.](language-support.md#text-to-speech)
* Information om regional tillgänglighet finns i [regioner.](regions.md#text-to-speech)

> [!IMPORTANT]
> Kostnaderna varierar för standardröster, anpassade röster och neurala röster. Mer information finns i [Prissättning.](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)

Innan du använder det här API:et bör du förstå:

* Text till tal-REST API auktoriseringsrubrik. Det innebär att du måste slutföra ett tokenutbyte för att få åtkomst till tjänsten. Mer information finns i [Autentisering](#authentication).

> [!TIP]
> I [den här artikeln](sovereign-clouds.md) finns Azure Government slutpunkter för Azure och Kina.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="get-a-list-of-voices"></a>Hämta en lista över röster

Med `voices/list` slutpunkten kan du hämta en fullständig lista över röster för en specifik region/slutpunkt.

### <a name="regions-and-endpoints"></a>Regioner och slutpunkter

| Region | Slutpunkt |
|--------|----------|
| Australien, östra | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Brasilien, södra | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Kanada, centrala | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Central US | `https://centralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Asien, östra | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| East US | `https://eastus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA, östra 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Frankrike, centrala | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Indien, centrala | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Japan, östra | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Sydkorea, centrala | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA, norra centrala | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Europa, norra | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Sydafrika, norra | `https://southafricanorth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA, södra centrala | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Sydostasien | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Storbritannien, södra | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA, västra centrala | `https://westcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Europa, västra | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA, västra | `https://westus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA, västra 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |

> [!TIP]
> [Röster i förhandsversion](language-support.md#neural-voices-in-preview) är endast tillgängliga i dessa tre regioner: USA, östra, Europa, västra och Sydostasien.

### <a name="request-headers"></a>Begärandehuvuden

I den här tabellen visas obligatoriska och valfria rubriker för text till tal-begäranden.

| Huvud | Description | Obligatoriskt/valfritt |
|--------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | Din prenumerationsnyckel för Speech-tjänsten. | Antingen det här huvudet `Authorization` eller krävs. |
| `Authorization` | En auktoriseringstoken som föregås av ordet `Bearer` . Mer information finns i [Autentisering](#authentication). | Antingen det här huvudet `Ocp-Apim-Subscription-Key` eller krävs. |



### <a name="request-body"></a>Begärandetext

En brödtext krävs inte för begäranden `GET` till den här slutpunkten.

### <a name="sample-request"></a>Exempelbegäran

Den här begäran kräver bara ett auktoriseringshuvud.

```http
GET /cognitiveservices/voices/list HTTP/1.1

Host: westus.tts.speech.microsoft.com
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
```

### <a name="sample-response"></a>Exempelsvar

Det här svaret har trunkerats för att illustrera strukturen för ett svar.

> [!NOTE]
> Rösttillgänglighet varierar beroende på region/slutpunkt.

```json
[

    {
    "Name": "Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)",
    "DisplayName": "Aria",
    "LocalName": "Aria",
    "ShortName": "en-US-AriaNeural",
    "Gender": "Female",
    "Locale": "en-US",
    "StyleList": [
      "chat",
      "customerservice",
      "newscast-casual",
      "newscast-formal",
      "cheerful",
      "empathetic"
    ],
    "SampleRateHertz": "24000",
    "VoiceType": "Neural",
    "Status": "GA"
  },

  ...

     {
    "Name": "Microsoft Server Speech Text to Speech Voice (ga-IE, OrlaNeural)",
    "DisplayName": "Orla",
    "LocalName": "Orla",
    "ShortName": "ga-IE-OrlaNeural",
    "Gender": "Female",
    "Locale": "ga-IE",
    "SampleRateHertz": "24000",
    "VoiceType": "Neural",
    "Status": "Preview"
  },

  ...

   {
    "Name": "Microsoft Server Speech Text to Speech Voice (zh-CN, YunxiNeural)",
    "DisplayName": "Yunxi",
    "LocalName": "云希",
    "ShortName": "zh-CN-YunxiNeural",
    "Gender": "Male",
    "Locale": "zh-CN",
    "StyleList": [
      "Calm",
      "Fearful",
      "Cheerful",
      "Disgruntled",
      "Serious",
      "Angry",
      "Sad",
      "Depressed",
      "Embarrassed"
    ],
    "SampleRateHertz": "24000",
    "VoiceType": "Neural",
    "Status": "Preview"
  },

    ...

   {
    "Name": "Microsoft Server Speech Text to Speech Voice (ar-EG, Hoda)",
    "DisplayName": "Hoda",
    "LocalName": "هدى",
    "ShortName": "ar-EG-Hoda",
    "Gender": "Female",
    "Locale": "ar-EG",
    "SampleRateHertz": "16000",
    "VoiceType": "Standard",
    "Status": "GA"
  },

...

]
```

### <a name="http-status-codes"></a>HTTP-statuskoder

HTTP-statuskoden för varje svar anger lyckade eller vanliga fel.

| HTTP-statuskod | Beskrivning | Möjlig orsak |
|------------------|-------------|-----------------|
| 200 | OK | Begäran lyckades. |
| 400 | Felaktig begäran | En obligatorisk parameter saknas, är tom eller null. Eller så är värdet som skickas till antingen en obligatorisk eller valfri parameter ogiltig. Ett vanligt problem är en rubrik som är för lång. |
| 401 | Behörighet saknas | Begäran har inte auktoriserats. Kontrollera att din prenumerationsnyckel eller token är giltig och i rätt region. |
| 429 | För många begäranden | Du har överskridit kvoten eller frekvensen för begäranden som tillåts för din prenumeration. |
| 502 | Felaktig gateway    | Problem på nätverks- eller serversidan. Kan också tyda på ogiltiga huvuden. |


## <a name="convert-text-to-speech"></a>Konvertera text-till-tal

Med `v1` slutpunkten kan du konvertera text till tal med hjälp av [Speech Synthesis Markup Language (SSML).](speech-synthesis-markup.md)

### <a name="regions-and-endpoints"></a>Regioner och slutpunkter

Dessa regioner stöds för text till tal med hjälp av REST API. Se till att du väljer den slutpunkt som matchar din prenumerationsregion.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

### <a name="request-headers"></a>Begärandehuvuden

I den här tabellen visas obligatoriska och valfria rubriker för text till tal-begäranden.

| Huvud | Description | Obligatorisk/valfri |
|--------|-------------|---------------------|
| `Authorization` | En auktoriseringstoken som föregås av ordet `Bearer` . Mer information finns i [Autentisering](#authentication). | Obligatorisk |
| `Content-Type` | Anger innehållstypen för den angivna texten. Godkänt värde: `application/ssml+xml` . | Obligatorisk |
| `X-Microsoft-OutputFormat` | Anger ljudutdataformatet. En fullständig lista över godkända värden finns i [ljudutdata.](#audio-outputs) | Obligatorisk |
| `User-Agent` | Programnamnet. Det angivna värdet måste vara mindre än 255 tecken. | Obligatorisk |

### <a name="audio-outputs"></a>Ljudutdata

Det här är en lista över ljudformat som stöds och som skickas i varje begäran som `X-Microsoft-OutputFormat` rubrik. Var och en innehåller bithastighet och kodningstyp. Taltjänsten stöder ljudutdata på 24 kHz, 16 kHz och 8 kHz.

```output
raw-16khz-16bit-mono-pcm            riff-16khz-16bit-mono-pcm
raw-24khz-16bit-mono-pcm            riff-24khz-16bit-mono-pcm
raw-48khz-16bit-mono-pcm            riff-48khz-16bit-mono-pcm
raw-8khz-8bit-mono-mulaw            riff-8khz-8bit-mono-mulaw
raw-8khz-8bit-mono-alaw             riff-8khz-8bit-mono-alaw
audio-16khz-32kbitrate-mono-mp3     audio-16khz-64kbitrate-mono-mp3
audio-16khz-128kbitrate-mono-mp3    audio-24khz-48kbitrate-mono-mp3
audio-24khz-96kbitrate-mono-mp3     audio-24khz-160kbitrate-mono-mp3
audio-48khz-96kbitrate-mono-mp3     audio-48khz-192kbitrate-mono-mp3
raw-16khz-16bit-mono-truesilk       raw-24khz-16bit-mono-truesilk
webm-16khz-16bit-mono-opus          webm-24khz-16bit-mono-opus
ogg-16khz-16bit-mono-opus           ogg-24khz-16bit-mono-opus
ogg-48khz-16bit-mono-opus
```

> [!NOTE]
> Om det valda röst- och utdataformatet har olika bithastigheter, omsamplades ljudet efter behov.
> ogg-24khz-16bit-mono-band kan avkodas med [codec](https://opus-codec.org/downloads/)

### <a name="request-body"></a>Begärandetext

Brödtexten i varje `POST` begäran skickas som Speech Synthesis [Markup Language (SSML).](speech-synthesis-markup.md) Med SSML kan du välja röst och språk för det syntetiserade tal som returneras av text till tal-tjänsten. En fullständig lista över röster som stöds finns i [språkstöd.](language-support.md#text-to-speech)

> [!NOTE]
> Om du använder en anpassad röst kan brödtexten i en begäran skickas som oformaterad text (ASCII eller UTF-8).

### <a name="sample-request"></a>Exempelbegäran

Den här HTTP-begäran använder SSML för att ange röst och språk. Om brödtexten är lång och det resulterande ljudet överskrider 10 minuter trunkeras den till 10 minuter. Ljudlängden får med andra ord inte överskrida 10 minuter.

```http
POST /cognitiveservices/v1 HTTP/1.1

X-Microsoft-OutputFormat: raw-24khz-16bit-mono-pcm
Content-Type: application/ssml+xml
Host: westus.tts.speech.microsoft.com
Content-Length: 225
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female'
    name='en-US-AriaNeural'>
        Microsoft Speech Service Text-to-Speech API
</voice></speak>
```

### <a name="http-status-codes"></a>HTTP-statuskoder

HTTP-statuskoden för varje svar anger lyckade eller vanliga fel.

| HTTP-statuskod | Beskrivning | Möjlig orsak |
|------------------|-------------|-----------------|
| 200 | OK | Begäran lyckades. svarstexten är en ljudfil. |
| 400 | Felaktig begäran | En obligatorisk parameter saknas, är tom eller null. Eller så är värdet som skickas till antingen en obligatorisk eller valfri parameter ogiltig. Ett vanligt problem är en rubrik som är för lång. |
| 401 | Behörighet saknas | Begäran har inte auktoriserats. Kontrollera att din prenumerationsnyckel eller token är giltig och i rätt region. |
| 415 | Medietyp som inte stöds | Det är möjligt att fel `Content-Type` har angetts. `Content-Type` ska vara inställt på `application/ssml+xml` . |
| 429 | För många begäranden | Du har överskridit kvoten eller frekvensen för begäranden som tillåts för din prenumeration. |
| 502 | Felaktig gateway    | Problem på nätverks- eller serversidan. Kan också tyda på ogiltiga huvuden. |

Om HTTP-statusen `200 OK` är innehåller brödtexten i svaret en ljudfil i det begärda formatet. Den här filen kan spelas upp när den överförs, sparas i en buffert eller sparas i en fil.

## <a name="next-steps"></a>Nästa steg

- [Skapa ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/cognitive-services/)
- [Asynkron syntes för långt ljud](./long-audio-api.md)
- [Komma igång med Custom Voice](how-to-custom-voice.md)
