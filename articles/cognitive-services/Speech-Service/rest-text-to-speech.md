---
title: Text till tal-API-referens (REST) – tal service
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder text-till-tal-REST API. I den här artikeln får du lära dig om behörighets alternativ, frågealternativ, hur du strukturerar en begäran och får svar.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/08/2021
ms.author: trbye
ms.custom: references_regions
ms.openlocfilehash: f33b72a25fc748f8320be8903903f817914733aa
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2021
ms.locfileid: "102455697"
---
# <a name="text-to-speech-rest-api"></a>Text-till-tal (REST API)

Med röst tjänsten kan du [konvertera text till syntetiskt tal](#convert-text-to-speech) och [Hämta en lista över röster som stöds](#get-a-list-of-voices) för en region med en uppsättning REST API: er. Varje tillgänglig slut punkt är associerad med en region. En prenumerations nyckel för den slut punkt/region som du planerar att använda måste anges.

Text till tal-REST API stöder neurala och standard text till tal-röster, som var och en har stöd för ett särskilt språk och dialekt, som identifieras av locale.

* En fullständig lista över röster finns i [språk stöd](language-support.md#text-to-speech).
* Information om regional tillgänglighet finns i [regioner](regions.md#text-to-speech).

> [!IMPORTANT]
> Kostnaderna varierar för standard-, anpassad-och neurala-röster. Mer information finns i [prissättning](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

Innan du använder det här API: et, förstå:

* Text till tal-REST API kräver ett Authorization-huvud. Det innebär att du måste slutföra ett token Exchange för att få åtkomst till tjänsten. Mer information finns i [Autentisering](#authentication).

> [!TIP]
> I [den här artikeln](sovereign-clouds.md) finns Azure Government-och Azure Kina-slutpunkter.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="get-a-list-of-voices"></a>Hämta en lista över röster

Med `voices/list` slut punkten kan du få en fullständig lista över röster för en bestämd region eller slut punkt.

### <a name="regions-and-endpoints"></a>Regioner och slut punkter

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
> [Röster i för hands versionen](language-support.md#neural-voices-in-preview) är endast tillgängliga i dessa tre regioner: östra USA, västeuropa och Sydostasien.

### <a name="request-headers"></a>Begärandehuvuden

I den här tabellen listas obligatoriska och valfria rubriker för text till tal-begäranden.

| Huvud | Beskrivning | Obligatorisk/valfri |
|--------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | Din prenumerations nyckel för röst tjänst. | Antingen den här rubriken eller `Authorization` krävs. |
| `Authorization` | En autentiseringstoken föregås av ordet `Bearer` . Mer information finns i [Autentisering](#authentication). | Antingen den här rubriken eller `Ocp-Apim-Subscription-Key` krävs. |



### <a name="request-body"></a>Begärandetext

En brödtext krävs inte för `GET` begär anden till den här slut punkten.

### <a name="sample-request"></a>Exempel förfrågan

Den här begäran kräver bara ett Authorization-huvud.

```http
GET /cognitiveservices/voices/list HTTP/1.1

Host: westus.tts.speech.microsoft.com
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
```

### <a name="sample-response"></a>Exempelsvar

Svaret har trunkerats för att illustrera ett svars struktur.

> [!NOTE]
> Röst tillgänglighet varierar beroende på region/slut punkt.

```json
[
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
]
```

### <a name="http-status-codes"></a>HTTP-statuskoder

HTTP-statuskoden för varje svar visar att de lyckas eller vanliga fel.

| HTTP-statuskod | Beskrivning | Möjlig orsak |
|------------------|-------------|-----------------|
| 200 | OK | Begäran lyckades. |
| 400 | Felaktig begäran | En obligatorisk parameter saknas, är tom eller null. Eller, värdet som skickas till antingen en obligatorisk eller valfri parameter är ogiltigt. Ett vanligt problem är ett sidhuvud som är för långt. |
| 401 | Behörighet saknas | Begäran är inte auktoriserad. Kontrol lera att din prenumerations nyckel eller token är giltig och i rätt region. |
| 429 | För många begär Anden | Du har överskridit kvoten eller frekvensen för begär Anden som tillåts för din prenumeration. |
| 502 | Felaktig gateway    | Problem med nätverks-eller Server sidan. Kan också indikera ogiltiga huvuden. |


## <a name="convert-text-to-speech"></a>Konvertera text-till-tal

Med `v1` slut punkten kan du konvertera text till tal med [SSML (Speech syntes Markup Language)](speech-synthesis-markup.md).

### <a name="regions-and-endpoints"></a>Regioner och slut punkter

Dessa regioner stöds för text till tal med hjälp av REST API. Se till att du väljer den slut punkt som matchar din prenumerations region.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

### <a name="request-headers"></a>Begärandehuvuden

I den här tabellen listas obligatoriska och valfria rubriker för text till tal-begäranden.

| Huvud | Beskrivning | Obligatorisk/valfri |
|--------|-------------|---------------------|
| `Authorization` | En autentiseringstoken föregås av ordet `Bearer` . Mer information finns i [Autentisering](#authentication). | Obligatorisk |
| `Content-Type` | Anger innehålls typen för den angivna texten. Accepterat värde: `application/ssml+xml` . | Obligatorisk |
| `X-Microsoft-OutputFormat` | Anger formatet för ljud uppspelning. En fullständig lista över godkända värden finns i [ljud utmatningar](#audio-outputs). | Obligatorisk |
| `User-Agent` | Programnamnet. Det tillhandahållna värdet måste vara mindre än 255 tecken. | Obligatorisk |

### <a name="audio-outputs"></a>Ljud utmatningar

Det här är en lista över de ljud format som stöds och som skickas i varje begäran som `X-Microsoft-OutputFormat` rubrik. Vart och ett omfattar en bit hastighet och kodnings typ. Tal tjänsten har stöd för 24 kHz-, 16 kHz-och 8 kHz-ljudutdata.

```output
raw-16khz-16bit-mono-pcm            raw-8khz-8bit-mono-mulaw
riff-8khz-8bit-mono-alaw            riff-8khz-8bit-mono-mulaw
riff-16khz-16bit-mono-pcm           audio-16khz-128kbitrate-mono-mp3
audio-16khz-64kbitrate-mono-mp3     audio-16khz-32kbitrate-mono-mp3
raw-24khz-16bit-mono-pcm            riff-24khz-16bit-mono-pcm
audio-24khz-160kbitrate-mono-mp3    audio-24khz-96kbitrate-mono-mp3
audio-24khz-48kbitrate-mono-mp3     ogg-24khz-16bit-mono-opus
raw-48khz-16bit-mono-pcm            riff-48khz-16bit-mono-pcm
audio-48khz-96kbitrate-mono-mp3     audio-48khz-192kbitrate-mono-mp3
```

> [!NOTE]
> Om det valda röst-och utmatnings formatet har olika bit hastigheter samplas ljudet om vid behov. OGG-24khz-bitarsläge-mono-Opus kan avkodas med [Opus-codec](https://opus-codec.org/downloads/)

### <a name="request-body"></a>Begärandetext

Bröd texten i varje `POST` begäran skickas som [SSML (Speech syntes Markup Language)](speech-synthesis-markup.md). Med SSML kan du välja röst och språk för det syntetiskt tal som returneras av text till tal-tjänsten. En fullständig lista över vilka röster som stöds finns i [språk stöd](language-support.md#text-to-speech).

> [!NOTE]
> Om du använder en anpassad röst kan bröd texten i en begäran skickas som oformaterad text (ASCII eller UTF-8).

### <a name="sample-request"></a>Exempel förfrågan

Denna HTTP-begäran använder SSML för att ange röst och språk. Om text längden är lång och det resulterande ljudet överskrider 10 minuter, trunkeras det till 10 minuter. Med andra ord får ljud längden inte överskrida 10 minuter.

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

HTTP-statuskoden för varje svar visar att de lyckas eller vanliga fel.

| HTTP-statuskod | Beskrivning | Möjlig orsak |
|------------------|-------------|-----------------|
| 200 | OK | Begäran lyckades. svars texten är en ljudfil. |
| 400 | Felaktig begäran | En obligatorisk parameter saknas, är tom eller null. Eller, värdet som skickas till antingen en obligatorisk eller valfri parameter är ogiltigt. Ett vanligt problem är ett sidhuvud som är för långt. |
| 401 | Behörighet saknas | Begäran är inte auktoriserad. Kontrol lera att din prenumerations nyckel eller token är giltig och i rätt region. |
| 415 | Medie typen stöds inte | Det är möjligt att fel `Content-Type` har angetts. `Content-Type` ska anges till `application/ssml+xml` . |
| 429 | För många begär Anden | Du har överskridit kvoten eller frekvensen för begär Anden som tillåts för din prenumeration. |
| 502 | Felaktig gateway    | Problem med nätverks-eller Server sidan. Kan också indikera ogiltiga huvuden. |

Om HTTP-statusen är `200 OK` , innehåller bröd texten i svaret en ljudfil i det begärda formatet. Den här filen kan spelas upp när den överförs, sparas i en buffert eller sparas i en fil.

## <a name="next-steps"></a>Nästa steg

- [Skapa ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/cognitive-services/)
- [Asynkron syntes för lång Forms ljud](./long-audio-api.md)
- [Komma igång med Custom Voice](how-to-custom-voice.md)