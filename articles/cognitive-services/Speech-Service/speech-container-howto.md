---
title: 'Installera och kör Docker-behållare för tal tjänstens API: er'
titleSuffix: Azure Cognitive Services
description: Använd Docker-behållare för tal tjänsten för att utföra tal igenkänning, avskriftering, generering och mer lokalt.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: aahi
ms.custom: cog-serv-seo-aug-2020
keywords: lokal, Docker, behållare
ms.openlocfilehash: 1eb8e6d990b0b3e6212736036466be9f11d05b01
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102201132"
---
# <a name="install-and-run-docker-containers-for-the-speech-service-apis"></a>Installera och kör Docker-behållare för tal tjänstens API: er 

Med containrar kan du köra vissa Speech-tjänst-API:er i din egen miljö. Containrar är bra för specifika säkerhets- och datastyrningskrav. I den här artikeln får du lära dig om hur du laddar ned, installerar och kör en Speech-container.

Speech-containrar gör det möjligt för kunder att bygga en arkitektur för talprogram som är optimerad för både robusta molnfunktioner och gränslokalitet. Det finns flera behållare som är tillgängliga, som använder samma [priser](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) som de molnbaserade Azure Speech-tjänsterna.


> [!IMPORTANT]
> Följande tal behållare är nu allmänt tillgängliga:
> * Standard tal till text
> * Custom Speech till text
> * Standard text till tal
> * Neurala text till tal
>
> Följande tal behållare finns i för hands versionen av gated.
> * Anpassad text till tal
> * Tal Språkidentifiering 
>
> Om du vill använda tal behållarna måste du skicka in en online-begäran och godkänna den. Mer information finns i avsnittet om **godkännande av begäran till avsnittet kör behållare** nedan.

| Container | Funktioner | Senast |
|--|--|--|
| Tal till text | Analyserar sentiment och beskrivar kontinuerliga tal i real tid eller batch-ljudinspelningar med mellanliggande resultat.  | 2.10.0 |
| Custom Speech till text | Genom att använda en anpassad modell från [Custom Speech portalen](https://speech.microsoft.com/customspeech), kan du skriva över kontinuerliga tal i real tid eller köra ljud inspelningar i text med mellanliggande resultat. | 2.10.0 |
| Text till tal | Konverterar text till tal med naturligt ljud med text indata eller SSML (Speech syntes Markup Language). | 1.12.0 |
| Anpassad text till tal | Med hjälp av en anpassad modell från den [anpassade röst portalen](https://aka.ms/custom-voice-portal)konverteras text till tal med naturligt ljud med text-eller tal syntess språk (SSML). | 1.12.0 |
| Tal Språkidentifiering | Identifiera det språk som talas i ljudfiler. | 1.0 |
| Neurala text till tal | Konverterar text till naturligt ljuds tal med djup neurala nätverks teknik, vilket ger mer naturliga syntetiskt syntetiskt tal. | 1.4.0 |

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/cognitive-services/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Följande krav gäller innan du använder tal behållare:

| Obligatorisk | Syfte |
|--|--|
| Docker-motorn | Du behöver Docker-motorn installerad på en [värddator](#the-host-computer). Docker innehåller paket som konfigurerar Docker-miljön på [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) och [Linux](https://docs.docker.com/engine/installation/#supported-platforms). En introduktion till grunderna för Docker och containrar finns i [Docker-översikt](https://docs.docker.com/engine/docker-overview/).<br><br> Docker måste konfigureras för att tillåta att behållarna ansluter till och skicka fakturerings data till Azure. <br><br> **I Windows** måste Docker också konfigureras för att stödja Linux-behållare.<br><br> |
| Bekant med Docker | Du bör ha grundläggande kunskaper om Docker-koncept, t. ex. register, databaser, behållare och behållar avbildningar, samt kunskaper om grundläggande `docker` kommandon. |
| Tal resurs | Du måste ha följande för att kunna använda dessa behållare:<br><br>En Azure _tal_ -resurs för att hämta tillhör ande API-nyckel och slut punkts-URI. Båda värdena är tillgängliga på Azure Portalens **tal** översikt och nycklar sidor. Båda krävs för att starta behållaren.<br><br>**{Api_key}**: en av de två tillgängliga resurs nycklarna på sidan **nycklar**<br><br>**{ENDPOINT_URI}**: slut punkten enligt vad som anges på sidan **Översikt** |

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>Värddatorn

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Stöd för avancerad Vector-tillägg

**Värden** är den dator som kör Docker-behållaren. Värden *måste ha stöd* för [Advanced Vector Extensions](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). Du kan söka efter AVX2-stöd på Linux-värdar med följande kommando:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> Värddatorn *krävs* för att stödja AVX2. Containern fungerar *inte* korrekt utan AVX2-stöd.

### <a name="container-requirements-and-recommendations"></a>Krav och rekommendationer för behållare

I följande tabell beskrivs den lägsta och rekommenderade fördelningen av resurser för varje tal behållare.

| Container | Minimum | Rekommenderas |
|-----------|---------|-------------|
| Tal till text | 2 kärnor, 2 GB minne | 4 kärnor, 4 GB minne |
| Custom Speech till text | 2 kärnor, 2 GB minne | 4 kärnor, 4 GB minne |
| Text till tal | 1 kärna, 2 GB minne | 2 kärnor, 3 GB minne |
| Anpassad text till tal | 1 kärna, 2 GB minne | 2 kärnor, 3 GB minne |
| Tal Språkidentifiering | 1 kärna, 1 GB minne | 1 kärna, 1 GB minne |
| Neurala text till tal | 6 kärnor, 12 GB minne | 8 kärnor, 16 GB minne |

* Varje kärna måste vara minst 2,6 gigahertz (GHz) eller snabbare.

Core och minne motsvarar `--cpus` `--memory` inställningarna och som används som en del av `docker run` kommandot.

> [!NOTE]
> Minimum och Recommended baseras på Docker-gränser, *inte* värd datorns resurser. Till exempel används tal-till-text-behållare som minnes karta för en stor språk modell, och *vi rekommenderar* att hela filen passar i minnet, vilket är ytterligare 4-6 GB. Dessutom kan den första körningen av en behållare ta längre tid, eftersom modeller växlas till minnet.

## <a name="request-approval-to-the-run-the-container"></a>Begär godkännande till kör containern

Fyll i och skicka [formuläret för begäran](https://aka.ms/csgate) för att begära åtkomst till behållaren. 

[!INCLUDE [Request access to public preview](../../../includes/cognitive-services-containers-request-access.md)]


## <a name="get-the-container-image-with-docker-pull"></a>Hämta behållar avbildningen med `docker pull`

Behållar avbildningar för tal finns i följande Container Registry.

# <a name="speech-to-text"></a>[Tal till text](#tab/stt)

| Container | Lagringsplats |
|-----------|------------|
| Tal till text | `mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text:latest` |

# <a name="custom-speech-to-text"></a>[Custom Speech till text](#tab/cstt)

| Container | Lagringsplats |
|-----------|------------|
| Custom Speech till text | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text:latest` |

# <a name="text-to-speech"></a>[Text till tal](#tab/tts)

| Container | Lagringsplats |
|-----------|------------|
| Text till tal | `mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech:latest` |

# <a name="neural-text-to-speech"></a>[Neurala text till tal](#tab/ntts)

| Container | Lagringsplats |
|-----------|------------|
| Neurala text till tal | `mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech:latest` |

# <a name="custom-text-to-speech"></a>[Anpassad text till tal](#tab/ctts)

| Container | Lagringsplats |
|-----------|------------|
| Anpassad text till tal | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech:latest` |

# <a name="speech-language-detection"></a>[Tal Språkidentifiering](#tab/lid)

> [!TIP]
> För att få de mest användbara resultaten rekommenderar vi att du använder en behållare för tal igenkänning med tal-till-text-eller anpassade tal-till-text-behållare. 

| Container | Lagringsplats |
|-----------|------------|
| Tal Språkidentifiering | `mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection:latest` |

***

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-speech-containers"></a>Docker-hämtning för tal behållarna

# <a name="speech-to-text"></a>[Tal till text](#tab/stt)

#### <a name="docker-pull-for-the-speech-to-text-container"></a>Docker-hämtning för den tal-till-text-behållare

Använd kommandot [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) för att ladda ned en behållar avbildning från Microsoft container Registry.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text:latest
```

> [!IMPORTANT]
> `latest`Taggen hämtar `en-US` språkvarianten. För ytterligare språk, se [tal-till-text-språk](#speech-to-text-locales).

#### <a name="speech-to-text-locales"></a>Tal till text-språk

Alla Taggar, förutom `latest` i, är i följande format och är Skift läges känsliga:

```
<major>.<minor>.<patch>-<platform>-<locale>-<prerelease>
```

Följande tagg är ett exempel på formatet:

```
2.6.0-amd64-en-us
```

För alla språk som stöds av **tal-till-text-** behållaren, se [taggar till text-Taggar](../containers/container-image-tags.md#speech-to-text).

# <a name="custom-speech-to-text"></a>[Custom Speech till text](#tab/cstt)

#### <a name="docker-pull-for-the-custom-speech-to-text-container"></a>Docker-hämtning för Custom Speech-till-text-behållaren

Använd kommandot [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) för att ladda ned en behållar avbildning från Microsoft container Registry.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text:latest
```

> [!NOTE]
> `locale`Och `voice` för anpassade tal behållare bestäms av den anpassade modellen som matas in av behållaren.

# <a name="text-to-speech"></a>[Text till tal](#tab/tts)

#### <a name="docker-pull-for-the-text-to-speech-container"></a>Docker-hämtning för text till tal-behållaren

Använd kommandot [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) för att ladda ned en behållar avbildning från Microsoft container Registry.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech:latest
```

> [!IMPORTANT]
> `latest`Taggen hämtar `en-US` språket och `ariarus` rösten. För ytterligare språk, se [text till tal-språk](#text-to-speech-locales).

#### <a name="text-to-speech-locales"></a>Text till tal-språk

Alla Taggar, förutom `latest` i, är i följande format och är Skift läges känsliga:

```
<major>.<minor>.<patch>-<platform>-<locale>-<voice>-<prerelease>
```

Följande tagg är ett exempel på formatet:

```
1.8.0-amd64-en-us-ariarus
```

För alla språk som stöds och motsvarande röster för **text till tal** -behållaren, se [taggar för text till tal-bilder](../containers/container-image-tags.md#text-to-speech).

> [!IMPORTANT]
> När ett *text-till-tal* -http-inlägg konstrueras, kräver [SSML-](speech-synthesis-markup.md) meddelandet ett- `voice` element med ett- `name` attribut. Värdet är motsvarande behållares nationella inställningar och röst, även kallat ["kort namn"](language-support.md#standard-voices). Taggen skulle till exempel `latest` ha röst namnet `en-US-AriaRUS` .

# <a name="neural-text-to-speech"></a>[Neurala text till tal](#tab/ntts)

#### <a name="docker-pull-for-the-neural-text-to-speech-container"></a>Docker-hämtning för neurala text till tal-behållaren

Använd kommandot [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) för att ladda ned en behållar avbildning från Microsoft container Registry.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech:latest
```

> [!IMPORTANT]
> `latest`Taggen hämtar `en-US` språket och `arianeural` rösten. Ytterligare språk finns i [neurala text-till-tal-språk](#neural-text-to-speech-locales).

#### <a name="neural-text-to-speech-locales"></a>Neurala text till tal-språk

Alla Taggar, förutom `latest` i, är i följande format och är Skift läges känsliga:

```
<major>.<minor>.<patch>-<platform>-<locale>-<voice>
```

Följande tagg är ett exempel på formatet:

```
1.3.0-amd64-en-us-arianeural
```

För alla språk som stöds och motsvarande röster i **neurala text till tal** -behållaren, se [neurala text till tal-bildtaggar](../containers/container-image-tags.md#neural-text-to-speech).

> [!IMPORTANT]
> När du skapar ett *neurala text-till-tal* -http-inlägg kräver [SSML-](speech-synthesis-markup.md) meddelandet ett- `voice` element med ett- `name` attribut. Värdet är motsvarande behållares nationella inställningar och röst, även kallat ["kort namn"](language-support.md#neural-voices). Taggen skulle till exempel `latest` ha röst namnet `en-US-AriaNeural` .

# <a name="custom-text-to-speech"></a>[Anpassad text till tal](#tab/ctts)

#### <a name="docker-pull-for-the-custom-text-to-speech-container"></a>Docker-hämtning för den anpassade text till tal-behållaren

Använd kommandot [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) för att ladda ned en behållar avbildning från Microsoft container Registry.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech:latest
```

> [!NOTE]
> `locale`Och `voice` för anpassade tal behållare bestäms av den anpassade modellen som matas in av behållaren.

# <a name="speech-language-detection"></a>[Tal Språkidentifiering](#tab/lid)

#### <a name="docker-pull-for-the-speech-language-detection-container"></a>Docker-hämtning för tal Språkidentifiering container

Använd kommandot [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) för att ladda ned en behållar avbildning från Microsoft container Registry.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection:latest
```

***

## <a name="how-to-use-the-container"></a>Använda behållaren

När behållaren är på [värddatorn](#the-host-computer)använder du följande process för att arbeta med behållaren.

1. [Kör behållaren](#run-the-container-with-docker-run)med de fakturerings inställningar som krävs. Fler [exempel](speech-container-configuration.md#example-docker-run-commands) på `docker run` kommandot är tillgängliga.
1. [Fråga behållarens förutsägelse slut punkt](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Kör behållaren med `docker run`

Använd kommandot [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) för att köra behållaren. Läs om hur du [samlar in nödvändiga parametrar](#gathering-required-parameters) för information om hur du hämtar `{Endpoint_URI}` och- `{API_Key}` värden. Fler [exempel](speech-container-configuration.md#example-docker-run-commands) på `docker run` kommandot är också tillgängliga.

# <a name="speech-to-text"></a>[Tal till text](#tab/stt)

Kör följande kommando för att köra standard *-behållaren för tal till text* `docker run` .

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Det här kommandot:

* Kör en *tal-till-text-* behållare från behållar avbildningen.
* Allokerar 4 processor kärnor och 4 GB minne.
* Exponerar TCP-port 5000 och allokerar en pseudo-TTY för behållaren.
* Tar automatiskt bort behållaren när den har avslut ATS. Behållar avbildningen är fortfarande tillgänglig på värddatorn.

> [!NOTE]
> Behållare stöder komprimerade ljud indata till tal-SDK med hjälp av GStreamer.
> Om du vill installera GStreamer i en behållare följer du Linux-instruktionerna för GStreamer i [använda codec-komprimerad ljud inspelning med talet SDK](how-to-use-codec-compressed-audio-input-streams.md).

#### <a name="diarization-on-the-speech-to-text-output"></a>Diarization för utdata från tal till text
Diarization är aktiverat som standard. Använd om du vill hämta diarization i ditt svar `diarize_speech_config.set_service_property` .

1. Ange utmatnings formatet för frasen till `Detailed` .
2. Ange läget för diarization. De lägen som stöds är `Identity` och `Anonymous` .
```python
diarize_speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Format',
    value='Detailed',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)

diarize_speech_config.set_service_property(
    name='speechcontext-phraseDetection.speakerDiarization.mode',
    value='Identity',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```
> [!NOTE]
> "Identity"-läget returnerar `"SpeakerId": "Customer"` eller `"SpeakerId": "Agent"` .
> Läget "Anonym" returnerar `"SpeakerId": "Speaker 1"` eller `"SpeakerId": "Speaker 2"`


#### <a name="analyze-sentiment-on-the-speech-to-text-output"></a>Analysera sentiment för utdata från tal till text 
Från och med v 2.6.0 av den tal-till-text-behållare ska du använda TextAnalytics 3,0 API-slutpunkten i stället för för hands versionen. Till exempel
* `https://westus2.api.cognitive.microsoft.com/text/analytics/v3.0/sentiment`
* `https://localhost:5000/text/analytics/v3.0/sentiment`

> [!NOTE]
> Textanalys- `v3.0` API: t är inte bakåtkompatibel med textanalys `v3.0-preview.1` . För att få den senaste stöd för sentiment-funktioner använder `v2.6.0` du behållar avbildningen tal-till-text och textanalys `v3.0` .

Från och med v-2.2.0 av den tal-till-text-behållaren kan du anropa [sentiment Analysis v3-API: et](../text-analytics/how-tos/text-analytics-how-to-sentiment-analysis.md) på utdata. Om du vill anropa sentiment-analysen behöver du en API för textanalys resurs slut punkt. Exempel: 
* `https://westus2.api.cognitive.microsoft.com/text/analytics/v3.0-preview.1/sentiment`
* `https://localhost:5000/text/analytics/v3.0-preview.1/sentiment`

Om du har åtkomst till en text analys-slutpunkt i molnet behöver du en nyckel. Om du kör Textanalys lokalt behöver du kanske inte ange detta.

Nyckeln och slut punkten skickas till tal behållaren som argument, som i följande exempel.

```bash
docker run -it --rm -p 5000:5000 \
mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text:latest \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
CloudAI:SentimentAnalysisSettings:TextAnalyticsHost={TEXT_ANALYTICS_HOST} \
CloudAI:SentimentAnalysisSettings:SentimentAnalysisApiKey={SENTIMENT_APIKEY}
```

Det här kommandot:

* Utför samma steg som kommandot ovan.
* Lagrar en API för textanalys slut punkt och nyckel för att skicka sentiment analys begär Anden. 

#### <a name="phraselist-v2-on-the-speech-to-text-output"></a>Phraselist v2 för utdata från tal till text 

Från och med v-2.6.0 av den tal-till-text-behållaren kan du hämta utdata med egna fraser – antingen hela meningen eller fraser i mitten. Till exempel *hög man* i följande mening:

* "Det här är en mening som är den **största man** är en mening."

Om du vill konfigurera en fras lista måste du lägga till egna fraser när du gör anropet. Exempel:

```python
    phrase="the tall man"
    recognizer = speechsdk.SpeechRecognizer(
        speech_config=dict_speech_config,
        audio_config=audio_config)
    phrase_list_grammer = speechsdk.PhraseListGrammar.from_recognizer(recognizer)
    phrase_list_grammer.addPhrase(phrase)
    
    dict_speech_config.set_service_property(
        name='setflight',
        value='xonlineinterp',
        channel=speechsdk.ServicePropertyChannel.UriQueryParameter
    )
```

Om du har flera fraser att lägga till ska du anropa `.addPhrase()` för varje fras för att lägga till den i fras listan. 


# <a name="custom-speech-to-text"></a>[Custom Speech till text](#tab/cstt)

*Custom Speech-till-text-* behållaren är beroende av en anpassad tal modell. Den anpassade modellen måste ha [tränats](how-to-custom-speech-train-model.md) med hjälp av den [anpassade tal portalen](https://speech.microsoft.com/customspeech).

Det anpassade tal **modells-ID: t** krävs för att köra behållaren. Du hittar den på sidan **utbildning** i den anpassade tal portalen. Från den anpassade tal portalen navigerar du till sidan **utbildning** och väljer modellen.
<br>

![Inlärnings sidan för anpassat tal](media/custom-speech/custom-speech-model-training.png)

Hämta **modell-ID: t** som ska användas som argument till `ModelId` `docker run` kommandots parameter.
<br>

![Information om anpassad tal modell](media/custom-speech/custom-speech-model-details.png)

Följande tabell representerar de olika `docker run` parametrarna och deras motsvarande beskrivningar:

| Parameter | Beskrivning |
|---------|---------|
| `{VOLUME_MOUNT}` | Värd datorns [volym montering](https://docs.docker.com/storage/volumes/), som docker använder för att bevara den anpassade modellen. Till exempel, *C:\CustomSpeech* där *C-enheten* finns på värddatorn. |
| `{MODEL_ID}` | Custom Speech **modell-ID: t** från sidan **utbildning** i den anpassade tal portalen. |
| `{ENDPOINT_URI}` | Slut punkten krävs för mätning och fakturering. Mer information finns i avsnittet om att [samla in obligatoriska parametrar](#gathering-required-parameters). |
| `{API_KEY}` | API-nyckeln måste anges. Mer information finns i avsnittet om att [samla in obligatoriska parametrar](#gathering-required-parameters). |

Kör följande kommando för att köra behållaren *Custom Speech-till-text* `docker run` :

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Det här kommandot:

* Kör en *Custom Speech-till-text-* behållare från behållar avbildningen.
* Allokerar 4 processor kärnor och 4 GB minne.
* Läser in *Custom Speech-till-text-* modellen från monteringen av volym indatakälla, till exempel *C:\CustomSpeech*.
* Exponerar TCP-port 5000 och allokerar en pseudo-TTY för behållaren.
* Laddar ned modellen `ModelId` (om den inte finns på volym monteringen).
* Om den anpassade modellen tidigare har hämtats, `ModelId` ignoreras.
* Tar automatiskt bort behållaren när den har avslut ATS. Behållar avbildningen är fortfarande tillgänglig på värddatorn.


#### <a name="base-model-download-on-the-custom-speech-to-text-container"></a>Bas modell hämtning av den anpassade tal-till-text-behållaren  
Från och med v 2.6.0 av den anpassade-till-text-behållaren kan du hämta den tillgängliga bas modell informationen med hjälp av alternativet `BaseModelLocale=<locale>` . Med det här alternativet får du en lista över tillgängliga bas modeller på det aktuella språket under ditt fakturerings konto. Exempel:

```bash
docker run --rm -it \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text \
BaseModelLocale={LOCALE} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Det här kommandot:

* Kör en *Custom Speech-till-text-* behållare från behållar avbildningen.
* Kontrol lera och returnera tillgängliga bas modeller för mål språket.

Utdata ger dig en lista över bas modeller med information språk, modell-ID och datum/tid för skapande. Du kan använda modell-ID: t för att hämta och använda den aktuella bas modell som du föredrar. Exempel:
```
Checking available base model for en-us
2020/10/30 21:54:20 [Info] Searching available base models for en-us
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2016-11-04T08:23:42Z, Id: a3d8aab9-6f36-44cd-9904-b37389ce2bfa
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2016-11-04T12:01:02Z, Id: cc7826ac-5355-471d-9bc6-a54673d06e45
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2017-08-17T12:00:00Z, Id: a1f8db59-40ff-4f0e-b011-37629c3a1a53
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-04-16T11:55:00Z, Id: c7a69da3-27de-4a4b-ab75-b6716f6321e5
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-09-21T15:18:43Z, Id: da494a53-0dad-4158-b15f-8f9daca7a412
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-10-19T11:28:54Z, Id: 84ec130b-d047-44bf-a46d-58c1ac292ca7
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-11-26T07:59:09Z, Id: ee5c100f-152f-4ae5-9e9d-014af3c01c56
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-11-26T09:21:55Z, Id: d04959a6-71da-4913-9997-836793e3c115
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2019-01-11T10:04:19Z, Id: 488e5f23-8bc5-46f8-9ad8-ea9a49a8efda
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2019-02-18T14:37:57Z, Id: 0207b3e6-92a8-4363-8c0e-361114cdd719
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2019-03-03T17:34:10Z, Id: 198d9b79-2950-4609-b6ec-f52254074a05
2020/10/30 21:54:21 [Fatal] Please run this tool again and assign --modelId '<one above base model id>'. If no model id listed above, it means currently there is no available base model for en-us
```

#### <a name="custom-pronunciation-on-the-custom-speech-to-text-container"></a>Anpassat uttal av den anpassade tal-till-text-behållaren 
Från och med v 2.5.0 av den anpassade-till-text-behållaren kan du hämta anpassade uttal-resultat i utdata. Allt du behöver göra är att låta dina egna anpassade uttal-regler ställas in i din anpassade modell och montera modellen till en egen text-till-text-behållare.


# <a name="text-to-speech"></a>[Text till tal](#tab/tts)

Kör följande kommando för att köra standard *text till tal* -behållaren `docker run` .

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Det här kommandot:

* Kör en standard *text till tal* -behållare från behållar avbildningen.
* Allokerar 1 processor kärna och 2 GB minne.
* Exponerar TCP-port 5000 och allokerar en pseudo-TTY för behållaren.
* Tar automatiskt bort behållaren när den har avslut ATS. Behållar avbildningen är fortfarande tillgänglig på värddatorn.

# <a name="neural-text-to-speech"></a>[Neurala text till tal](#tab/ntts)

Kör följande kommando för att köra *neurala-behållaren för text till tal* `docker run` .

```bash
docker run --rm -it -p 5000:5000 --memory 12g --cpus 6 \
mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Det här kommandot:

* Kör en *neurala text till tal* -behållare från behållar avbildningen.
* Allokerar 6 processor kärnor och 12 gigabyte (GB) minne.
* Exponerar TCP-port 5000 och allokerar en pseudo-TTY för behållaren.
* Tar automatiskt bort behållaren när den har avslut ATS. Behållar avbildningen är fortfarande tillgänglig på värddatorn.

# <a name="custom-text-to-speech"></a>[Anpassad text till tal](#tab/ctts)

Den *anpassade text till tal* -behållaren är beroende av en anpassad röst modell. Den anpassade modellen måste ha [tränats](how-to-custom-voice-create-voice.md) med hjälp av den [anpassade röst portalen](https://aka.ms/custom-voice-portal). ID för anpassad röst **modell** krävs för att köra behållaren. Du hittar den på sidan **utbildning** i den anpassade röst portalen. Från den anpassade röst portalen går du till sidan **utbildning** och väljer modellen.
<br>

![Sidan anpassad röst utbildning](media/custom-voice/custom-voice-model-training.png)

Hämta **modell-ID: t** som ska användas som argument till `ModelId` parametern för kommandot Docker Run.
<br>

![Information om anpassad röst modell](media/custom-voice/custom-voice-model-details.png)

Följande tabell representerar de olika `docker run` parametrarna och deras motsvarande beskrivningar:

| Parameter | Beskrivning |
|---------|---------|
| `{VOLUME_MOUNT}` | Värd datorns [volym montering](https://docs.docker.com/storage/volumes/), som docker använder för att bevara den anpassade modellen. Till exempel, *C:\CustomSpeech* där *C-enheten* finns på värddatorn. |
| `{MODEL_ID}` | Custom Speech **modell-ID: t** från sidan **utbildning** i den anpassade röst portalen. |
| `{ENDPOINT_URI}` | Slut punkten krävs för mätning och fakturering. Mer information finns i avsnittet om att [samla in obligatoriska parametrar](#gathering-required-parameters). |
| `{API_KEY}` | API-nyckeln måste anges. Mer information finns i avsnittet om att [samla in obligatoriska parametrar](#gathering-required-parameters). |

Kör följande kommando för att köra den *anpassade text till tal* -behållaren `docker run` :

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Det här kommandot:

* Kör en *anpassad text till tal* -behållare från behållar avbildningen.
* Allokerar 1 processor kärna och 2 GB minne.
* Läser in den *anpassade text till tal-* modellen från monteringen av volym indataport, till exempel *C:\CustomVoice*.
* Exponerar TCP-port 5000 och allokerar en pseudo-TTY för behållaren.
* Laddar ned modellen `ModelId` (om den inte finns på volym monteringen).
* Om den anpassade modellen tidigare har hämtats, `ModelId` ignoreras.
* Tar automatiskt bort behållaren när den har avslut ATS. Behållar avbildningen är fortfarande tillgänglig på värddatorn.

# <a name="speech-language-detection"></a>[Tal Språkidentifiering](#tab/lid)

Kör följande kommando för att köra *tal språkidentifiering* containern `docker run` .

```bash
docker run --rm -it -p 5003:5003 --memory 1g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Det här kommandot: 

* Kör en språk identifierings behållare för tal från behållar avbildningen. För närvarande kommer du inte att debiteras för att köra den här avbildningen.
* Allokerar 1 processor kärnor och 1 GB minne.
* Exponerar TCP-port 5003 och allokerar en pseudo-TTY för behållaren.
* Tar automatiskt bort behållaren när den har avslut ATS. Behållar avbildningen är fortfarande tillgänglig på värddatorn.

Om du bara skickar tal Språkidentifiering begär Anden måste du ange tal klientens `phraseDetection` värde till `None` .  

```python
speech_config.set_service_property(
      name='speechcontext-phraseDetection.Mode',
      value='None',
      channel=speechsdk.ServicePropertyChannel.UriQueryParameter
   )
```

Om du vill köra den här behållaren med en "tal-till-text"-behållare kan du använda den här [Docker-avbildningen](https://hub.docker.com/r/antsu/on-prem-client). När båda behållarna har startats använder du kommandot Docker Run för att köra `speech-to-text-with-languagedetection-client` .

```Docker
docker run --rm -v ${HOME}:/root -ti antsu/on-prem-client:latest ./speech-to-text-with-languagedetection-client ./audio/LanguageDetection_en-us.wav --host localhost --lport 5003 --sport 5000
```

> [!NOTE]
> Att öka antalet samtidiga anrop kan påverka tillförlitlighet och latens. För språk identifiering rekommenderar vi högst 4 samtidiga anrop med 1 processor med och 1 GB minne. För värdar med 2 processorer och 2 GB minne rekommenderar vi högst 6 samtidiga anrop.

***

> [!IMPORTANT]
> `Eula`Alternativen, `Billing` och `ApiKey` måste anges för att köra behållaren, annars startar inte behållaren.  Mer information finns i [fakturering](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Köra frågor mot containerns förutsägelseslutpunkt

> [!NOTE]
> Använd ett unikt port nummer om du kör flera behållare.

| Containers | SDK-värd-URL | Protokoll |
|--|--|--|
| Standard tal-till-text och Custom Speech-till-text | `ws://localhost:5000` | WS |
| Text till tal (inklusive standard, anpassad och neurala), igenkänning av tal språk | `http://localhost:5000` | HTTP |

Mer information om hur du använder WSS-och HTTPS-protokoll finns i [behållar säkerhet](../cognitive-services-container-support.md#azure-cognitive-services-container-security).

### <a name="speech-to-text-standard-and-custom"></a>Tal till text (standard och anpassad)

[!INCLUDE [Query Speech-to-text container endpoint](includes/speech-to-text-container-query-endpoint.md)]

#### <a name="analyze-sentiment"></a>Analysera sentiment

Om du har angett dina API för textanalys autentiseringsuppgifter [för behållaren](#analyze-sentiment-on-the-speech-to-text-output)kan du använda tal-SDK för att skicka tal igenkännings begär Anden med sentiment-analys. Du kan konfigurera API-svar för att använda antingen ett *enkelt* eller *detaljerat* format.
> [!NOTE]
> v-1.13 av Speech service python SDK har ett identifierat problem med sentiment-analys. Använd v-1.12. x eller tidigare om du använder sentiment-analys i röst tjänsten python SDK.

# <a name="simple-format"></a>[Enkelt format](#tab/simple-format)

Om du vill konfigurera tal klienten för att använda ett enkelt format, lägger du till `"Sentiment"` som ett värde för `Simple.Extensions` . Om du vill välja en speciell Textanalys modell version ersätter `'latest'` du i `speechcontext-phraseDetection.sentimentAnalysis.modelversion` egenskaps konfigurationen.

```python
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Simple.Extensions',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentAnalysis.modelversion',
    value='latest',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

`Simple.Extensions` Returnerar sentiment-resultatet i svarets rot lager.

```json
{
   "DisplayText":"What's the weather like?",
   "Duration":13000000,
   "Id":"6098574b79434bd4849fee7e0a50f22e",
   "Offset":4700000,
   "RecognitionStatus":"Success",
   "Sentiment":{
      "Negative":0.03,
      "Neutral":0.79,
      "Positive":0.18
   }
}
```

# <a name="detailed-format"></a>[Detaljerat format](#tab/detailed-format)

Om du vill konfigurera tal klienten så att den använder ett detaljerat format, lägger du till `"Sentiment"` som ett värde för `Detailed.Extensions` , `Detailed.Options` eller båda. Om du vill välja en speciell Textanalys modell version ersätter `'latest'` du i `speechcontext-phraseDetection.sentimentAnalysis.modelversion` egenskaps konfigurationen.

```python
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Detailed.Options',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Detailed.Extensions',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentAnalysis.modelversion',
    value='latest',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

`Detailed.Extensions` ger sentiment resultat i svarets rot lager. `Detailed.Options` ger resultatet i `NBest` lagret av svaret. De kan användas separat eller tillsammans.

```json
{
   "DisplayText":"What's the weather like?",
   "Duration":13000000,
   "Id":"6a2aac009b9743d8a47794f3e81f7963",
   "NBest":[
      {
         "Confidence":0.973695,
         "Display":"What's the weather like?",
         "ITN":"what's the weather like",
         "Lexical":"what's the weather like",
         "MaskedITN":"What's the weather like",
         "Sentiment":{
            "Negative":0.03,
            "Neutral":0.79,
            "Positive":0.18
         }
      },
      {
         "Confidence":0.9164971,
         "Display":"What is the weather like?",
         "ITN":"what is the weather like",
         "Lexical":"what is the weather like",
         "MaskedITN":"What is the weather like",
         "Sentiment":{
            "Negative":0.02,
            "Neutral":0.88,
            "Positive":0.1
         }
      }
   ],
   "Offset":4700000,
   "RecognitionStatus":"Success",
   "Sentiment":{
      "Negative":0.03,
      "Neutral":0.79,
      "Positive":0.18
   }
}
```

---

Om du vill inaktivera sentiment-analysen helt lägger du till ett `false` värde i `sentimentanalysis.enabled` .

```python
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentanalysis.enabled',
    value='false',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

### <a name="text-to-speech-standard-neural-and-custom"></a>Text till tal (standard, neurala och anpassad)

[!INCLUDE [Query Text-to-speech container endpoint](includes/text-to-speech-container-query-endpoint.md)]

### <a name="run-multiple-containers-on-the-same-host"></a>Kör flera behållare på samma värd

Om du tänker köra flera behållare med exponerade portar ska du se till att köra varje behållare med en annan exponerad port. Kör till exempel den första behållaren på port 5000 och den andra behållaren på port 5001.

Du kan ha den här behållaren och en annan Azure Cognitive Services-behållare som körs på värden tillsammans. Du kan också ha flera behållare av samma Cognitive Services-behållare som kör.

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Stoppa containern

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Felsökning

När du startar eller kör behållaren kan det uppstå problem. Använd en utmatnings [montering](speech-container-configuration.md#mount-settings) och aktivera loggning. På så sätt kan behållaren generera loggfiler som kan vara till hjälp vid fel sökning av problem.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Fakturering

Tal behållarna skickar fakturerings information till Azure med hjälp av en *tal* resurs på ditt Azure-konto.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Mer information om dessa alternativ finns i [Configure containers](speech-container-configuration.md).

## <a name="summary"></a>Sammanfattning

I den här artikeln har du lärt dig begrepp och arbets flöde för att ladda ned, installera och köra tal behållare. Sammanfattningsvis:

* Tal innehåller fyra Linux-behållare för Docker, vilket kapslar olika funktioner:
  * *Tal till text*
  * *Custom Speech till text*
  * *Text till tal*
  * *Anpassad text till tal*
  * *Neurala text till tal*
  * *Tal Språkidentifiering*
* Behållar avbildningar hämtas från behållar registret i Azure.
* Behållar avbildningar körs i Docker.
* Om du använder REST API (endast text till tal) eller SDK (tal-till-text eller text till tal) anger du värd-URI för behållaren. 
* Du måste ange fakturerings information när du instansierar en behållare.

> [!IMPORTANT]
>  Cognitive Services behållare är inte licensierade att köras utan att vara anslutna till Azure för mätning. Kunderna behöver göra det möjligt för behållarna att kommunicera fakturerings information med mät tjänsten hela tiden. Cognitive Services behållare skickar inte kund information (t. ex. den bild eller text som analyseras) till Microsoft.

## <a name="next-steps"></a>Nästa steg

* Granska [Konfigurera behållare](speech-container-configuration.md) för konfigurations inställningar
* Lär dig hur du [använder tal tjänst behållare med Kubernetes och Helm](speech-container-howto-on-premises.md)
* Använd fler [Cognitive Services behållare](../cognitive-services-container-support.md)
