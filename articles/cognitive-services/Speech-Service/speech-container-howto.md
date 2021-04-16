---
title: Installera och köra Docker-containrar för Speech-tjänstens API:er
titleSuffix: Azure Cognitive Services
description: Använd Docker-containrar för Speech-tjänsten för att utföra taligenkänning, transkription, generering och mer lokalt.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: aahi
ms.custom: cog-serv-seo-aug-2020
keywords: lokalt, Docker, container
ms.openlocfilehash: cb99dc3c5e16ee117df46d7fda0caab9c57f0853
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388099"
---
# <a name="install-and-run-docker-containers-for-the-speech-service-apis"></a>Installera och köra Docker-containrar för Speech-tjänstens API:er 

Med containrar kan du köra vissa Speech-tjänst-API:er i din egen miljö. Containrar är bra för specifika säkerhets- och datastyrningskrav. I den här artikeln får du lära dig om hur du laddar ned, installerar och kör en Speech-container.

Speech-containrar gör det möjligt för kunder att bygga en arkitektur för talprogram som är optimerad för både robusta molnfunktioner och gränslokalitet. Det finns flera tillgängliga containrar som använder samma [priser](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) som de molnbaserade Azure Speech Services.


> [!IMPORTANT]
> Följande Speech-containrar är nu allmänt tillgängliga:
> * Standard tal till text
> * Anpassat tal till text
> * Standardtext till tal
> * Neural text till tal
>
> Följande speech-containrar finns i en gated preview (grindförhandsvisning).
> * Anpassad text till tal
> * Speech Språkidentifiering 
>
> Om du vill använda speech-containrarna måste du skicka en onlinebegäran och få den godkänd. Mer information **finns i avsnittet Begär godkännande för att köra containern** nedan.

| Container | Funktioner | Senast |
|--|--|--|
| Tal till text | Analyserar sentiment och transkriberar kontinuerliga tal- eller batchljudinspelningar i realtid med mellanliggande resultat.  | 2.11.0 |
| Anpassat tal till text | Med hjälp av en anpassad modell [från Custom Speech-portalen](https://speech.microsoft.com/customspeech)transkriberar du kontinuerliga talinspelningar i realtid eller batchljudinspelningar till text med mellanliggande resultat. | 2.11.0 |
| Text till tal | Konverterar text till naturligt ljudande tal med oformaterad textinmatning eller Speech Synthesis Markup Language (SSML). | 1.13.0 |
| Anpassad text till tal | Med en anpassad modell från [Anpassad röst portalen](https://aka.ms/custom-voice-portal)konverteras text till naturligt tal med oformaterad textinmatning eller SSML (Speech Synthesis Markup Language). | 1.13.0 |
| Speech Språkidentifiering | Identifiera språket som talas i ljudfiler. | 1.0 |
| Neural text till tal | Konverterar text till naturligt ljudande tal med djup neural nätverksteknik, vilket möjliggör mer naturligt syntetiserat tal. | 1.5.0 |

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/cognitive-services/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Följande krav måste vara uppfyllda innan du använder Speech-containrar:

| Obligatorisk | Syfte |
|--|--|
| Docker-motorn | Docker-motorn måste vara installerad på en [värddator.](#the-host-computer) Docker innehåller paket som konfigurerar Docker-miljön på [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) och [Linux](https://docs.docker.com/engine/installation/#supported-platforms). En introduktion till grunderna för Docker och containrar finns i [Docker-översikt](https://docs.docker.com/engine/docker-overview/).<br><br> Docker måste konfigureras så att containrarna kan ansluta till och skicka faktureringsdata till Azure. <br><br> **I Windows** måste Docker också konfigureras för att stödja Linux-containrar.<br><br> |
| Kunskaper om Docker | Du bör ha en grundläggande förståelse för Docker-begrepp som register, lagringsdatabaser, containrar och containeravbildningar, samt kunskaper om grundläggande `docker` kommandon. |
| Speech-resurs | För att kunna använda dessa containrar måste du ha:<br><br>En Azure _Speech-resurs_ för att hämta den associerade API-nyckeln och slutpunkts-URI:n. Båda värdena är tillgängliga på Azure Portal på sidorna **Talöversikt** och Nycklar. Båda krävs för att starta containern.<br><br>**{API_KEY}**: En av de två tillgängliga resursnycklarna på **sidan** Nycklar<br><br>**{ENDPOINT_URI}**: Slutpunkten som anges på **översiktssidan** |

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>Värddatorn

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Stöd för avancerat vektortillägg

Värden **är** den dator som kör Docker-containern. Värden måste *ha stöd för* Advanced Vector [Extensions](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). Du kan söka efter AVX2-stöd på Linux-värdar med följande kommando:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> Värddatorn krävs för *att* stödja AVX2. Containern *fungerar inte* korrekt utan STÖD för AVX2.

### <a name="container-requirements-and-recommendations"></a>Krav och rekommendationer för containrar

I följande tabell beskrivs den lägsta och rekommenderade allokeringen av resurser för varje Speech-container.

| Container | Minimum | Rekommenderas |
|-----------|---------|-------------|
| Tal till text | 2 kärnor, 2 GB minne | 4 kärnor, 4 GB minne |
| Anpassat tal till text | 2 kärnor, 2 GB minne | 4 kärnor, 4 GB minne |
| Text till tal | 1 kärna, 2 GB minne | 2 kärnor, 3 GB minne |
| Anpassad text till tal | 1 kärna, 2 GB minne | 2 kärnor, 3 GB minne |
| Speech Språkidentifiering | 1 kärna, 1 GB minne | 1 kärna, 1 GB minne |
| Neural text till tal | 6 kärnor, 12 GB minne | 8 kärnor, 16 GB minne |

* Varje kärna måste vara minst 2,6 gigahertz (GHz) eller snabbare.

Kärna och minne motsvarar inställningarna `--cpus` och , som används som en del av kommandot `--memory` `docker run` .

> [!NOTE]
> Den lägsta och rekommenderade gränsen baseras på Docker-gränser, *inte värddatorresurserna.* Till exempel mappar minnesmappning för tal till text-containrar delar  av en stor språkmodell, och vi rekommenderar att hela filen får plats i minnet, vilket är ytterligare 4–6 GB. Dessutom kan den första körningen av någon av containrar ta längre tid, eftersom modeller är indelade i minnet.

## <a name="request-approval-to-the-run-the-container"></a>Begära godkännande för att köra containern

Fyll i och skicka formuläret [för begäran för](https://aka.ms/csgate) att begära åtkomst till containern. 

[!INCLUDE [Request access to public preview](../../../includes/cognitive-services-containers-request-access.md)]


## <a name="get-the-container-image-with-docker-pull"></a>Hämta containeravbildningen med `docker pull`

Containeravbildningar för Speech finns i följande Container Registry.

# <a name="speech-to-text"></a>[Tal till text](#tab/stt)

| Container | Lagringsplats |
|-----------|------------|
| Tal till text | `mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text:latest` |

# <a name="custom-speech-to-text"></a>[Anpassat tal till text](#tab/cstt)

| Container | Lagringsplats |
|-----------|------------|
| Anpassat tal till text | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text:latest` |

# <a name="text-to-speech"></a>[Text till tal](#tab/tts)

| Container | Lagringsplats |
|-----------|------------|
| Text till tal | `mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech:latest` |

# <a name="neural-text-to-speech"></a>[Neural text till tal](#tab/ntts)

| Container | Lagringsplats |
|-----------|------------|
| Neural text till tal | `mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech:latest` |

# <a name="custom-text-to-speech"></a>[Anpassad text till tal](#tab/ctts)

| Container | Lagringsplats |
|-----------|------------|
| Anpassad text till tal | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech:latest` |

# <a name="speech-language-detection"></a>[Speech Språkidentifiering](#tab/lid)

> [!TIP]
> För att få de mest användbara resultaten rekommenderar vi att du använder talspråksidentifieringscontainern med tal till text- eller anpassade tal till text-containrar. 

| Container | Lagringsplats |
|-----------|------------|
| Speech Språkidentifiering | `mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection:latest` |

***

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-speech-containers"></a>Docker-pull för Speech-containrarna

# <a name="speech-to-text"></a>[Tal till text](#tab/stt)

#### <a name="docker-pull-for-the-speech-to-text-container"></a>Docker-pull för tal till text-containern

Använd kommandot [docker pull för](https://docs.docker.com/engine/reference/commandline/pull/) att ladda ned en containeravbildning från Microsoft Container Registry.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text:latest
```

> [!IMPORTANT]
> Taggen `latest` hämtar `en-US` språk. Ytterligare språk finns i [Språk för tal till text.](#speech-to-text-locales)

#### <a name="speech-to-text-locales"></a>Tal till text-språk

Alla taggar, förutom `latest` är i följande format och är fallkänsliga:

```
<major>.<minor>.<patch>-<platform>-<locale>-<prerelease>
```

Följande tagg är ett exempel på formatet:

```
2.6.0-amd64-en-us
```

Alla språk som stöds för tal **till text-containern** finns i [Bildtaggar för](../containers/container-image-tags.md#speech-to-text)tal till text.

# <a name="custom-speech-to-text"></a>[Anpassat tal till text](#tab/cstt)

#### <a name="docker-pull-for-the-custom-speech-to-text-container"></a>Docker-pull för den anpassade tal till text-containern

Använd kommandot [docker pull för](https://docs.docker.com/engine/reference/commandline/pull/) att ladda ned en containeravbildning från Microsoft Container Registry.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text:latest
```

> [!NOTE]
> och `locale` för `voice` anpassade Speech-containrar bestäms av den anpassade modellen som matas in av containern.

# <a name="text-to-speech"></a>[Text till tal](#tab/tts)

#### <a name="docker-pull-for-the-text-to-speech-container"></a>Docker-pull för text till tal-containern

Använd kommandot [docker pull för](https://docs.docker.com/engine/reference/commandline/pull/) att ladda ned en containeravbildning från Microsoft Container Registry.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech:latest
```

> [!IMPORTANT]
> Taggen `latest` hämtar språk och `en-US` `ariarus` röst. Ytterligare språk finns i [Text-till-tal-språk](#text-to-speech-locales).

#### <a name="text-to-speech-locales"></a>Språk för text till tal

Alla taggar, förutom `latest` är i följande format och är fallkänsliga:

```
<major>.<minor>.<patch>-<platform>-<locale>-<voice>-<prerelease>
```

Följande tagg är ett exempel på formatet:

```
1.8.0-amd64-en-us-ariarus
```

Alla språk som stöds och motsvarande röster i **text till tal-containern** finns i [Bildtaggar](../containers/container-image-tags.md#text-to-speech)för text till tal.

> [!IMPORTANT]
> När du skapar ett *SSML-meddelande (Text till* tal HTTP POST) kräver [SSML-meddelandet (Speech Synthesis Markup Language)](speech-synthesis-markup.md) ett `voice` -element med ett `name` -attribut. Värdet är motsvarande språk och röst för containern, även kallat ["kortnamn".](language-support.md#standard-voices) Taggen skulle `latest` till exempel ha röstnamnet `en-US-AriaRUS` .

# <a name="neural-text-to-speech"></a>[Neural text till tal](#tab/ntts)

#### <a name="docker-pull-for-the-neural-text-to-speech-container"></a>Docker-pull för den neurala text till tal-containern

Använd kommandot [docker pull för](https://docs.docker.com/engine/reference/commandline/pull/) att ladda ned en containeravbildning från Microsoft Container Registry.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech:latest
```

> [!IMPORTANT]
> Taggen `latest` hämtar språk och `en-US` `arianeural` röst. Ytterligare språk finns i [Neural Text-to-Speech locales (Neurala text till tal-språk).](#neural-text-to-speech-locales)

#### <a name="neural-text-to-speech-locales"></a>Språk för neural text till tal

Alla taggar, förutom `latest` är i följande format och är fallkänsliga:

```
<major>.<minor>.<patch>-<platform>-<locale>-<voice>
```

Följande tagg är ett exempel på formatet:

```
1.3.0-amd64-en-us-arianeural
```

Alla språk som stöds och motsvarande röster i **den neurala text till tal-containern** finns i [Bildtaggar](../containers/container-image-tags.md#neural-text-to-speech)för neural text till tal.

> [!IMPORTANT]
> När du skapar *ett SSML-meddelande (Neural Text-to-Speech* HTTP POST) kräver [SSML-meddelandet (Speech Synthesis Markup Language)](speech-synthesis-markup.md) `voice` ett -element med ett `name` -attribut. Värdet är motsvarande språk och röst för containern, även kallat ["kortnamn".](language-support.md#neural-voices) Taggen skulle `latest` till exempel ha röstnamnet `en-US-AriaNeural` .

# <a name="custom-text-to-speech"></a>[Anpassad text till tal](#tab/ctts)

#### <a name="docker-pull-for-the-custom-text-to-speech-container"></a>Docker-pull för den anpassade text till tal-containern

Använd kommandot [docker pull för](https://docs.docker.com/engine/reference/commandline/pull/) att ladda ned en containeravbildning från Microsoft Container Registry.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech:latest
```

> [!NOTE]
> och `locale` för `voice` anpassade Speech-containrar bestäms av den anpassade modellen som matas in av containern.

# <a name="speech-language-detection"></a>[Speech Språkidentifiering](#tab/lid)

#### <a name="docker-pull-for-the-speech-language-detection-container"></a>Docker-pull för Speech Språkidentifiering containern

Använd kommandot [docker pull för](https://docs.docker.com/engine/reference/commandline/pull/) att ladda ned en containeravbildning från Microsoft Container Registry.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection:latest
```

***

## <a name="how-to-use-the-container"></a>Så här använder du containern

När containern finns på [värddatorn](#the-host-computer)använder du följande process för att arbeta med containern.

1. [Kör containern](#run-the-container-with-docker-run)med de faktureringsinställningar som krävs. Fler [exempel](speech-container-configuration.md#example-docker-run-commands) på `docker run` kommandot är tillgängliga.
1. [Fråga containerns förutsägelseslutpunkt](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Kör containern med `docker run`

Använd kommandot [docker run](https://docs.docker.com/engine/reference/commandline/run/) för att köra containern. Information om [hur du hämtar värdena](#gathering-required-parameters) och finns i samla in obligatoriska `{Endpoint_URI}` `{API_Key}` parametrar. Ytterligare [exempel](speech-container-configuration.md#example-docker-run-commands) på `docker run` kommandot är också tillgängliga.

# <a name="speech-to-text"></a>[Tal till text](#tab/stt)

Kör *standardcontainern tal till text* genom att köra följande `docker run` kommando.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Det här kommandot:

* Kör en *tal till text-container* från containeravbildningen.
* Allokerar 4 PROCESSORkärnor och 4 GB minne.
* Exponerar TCP-port 5000 och allokerar en pseudo-TTY för containern.
* Tar automatiskt bort containern när den har avslutats. Containeravbildningen är fortfarande tillgänglig på värddatorn.

> [!NOTE]
> Containrar stöder komprimerade ljudindata till Speech SDK med hjälp av GStreamer.
> Om du vill installera GStreamer i en container följer du Linux-instruktionerna för GStreamer i Använda [codec-komprimerade ljudindata med Speech SDK.](how-to-use-codec-compressed-audio-input-streams.md)

#### <a name="diarization-on-the-speech-to-text-output"></a>Diarisering av tal till text-utdata
Diarisering är aktiverat som standard. om du vill få diarisering i svaret använder du `diarize_speech_config.set_service_property` .

1. Ange formatet för frasutdata till `Detailed` .
2. Ange diariseringsläge. Lägena som stöds `Identity` är och `Anonymous` .
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
> "Identitetsläge" returnerar `"SpeakerId": "Customer"` eller `"SpeakerId": "Agent"` .
> Läget "Anonym" returnerar `"SpeakerId": "Speaker 1"` eller `"SpeakerId": "Speaker 2"`


#### <a name="analyze-sentiment-on-the-speech-to-text-output"></a>Analysera sentiment i tal till text-utdata 
Från och med v2.6.0 i tal-till-text-containern bör du använda TextAnalytics 3.0 API-slutpunkten i stället för förhandsversionen. Till exempel
* `https://westus2.api.cognitive.microsoft.com/text/analytics/v3.0/sentiment`
* `https://localhost:5000/text/analytics/v3.0/sentiment`

> [!NOTE]
> API:Textanalys `v3.0` är inte bakåtkompatibel med Textanalys `v3.0-preview.1` . För att få det senaste stödet för sentimentfunktionen använder `v2.6.0` du containeravbildningen tal till text och Textanalys `v3.0` .

Från och med v2.2.0 i tal till text-containern kan du anropa API:et [för attitydanalys v3](../text-analytics/how-tos/text-analytics-how-to-sentiment-analysis.md) för utdata. Om du vill anropa attitydanalys behöver du en Textanalys API-resursslutpunkt. Exempel: 
* `https://westus2.api.cognitive.microsoft.com/text/analytics/v3.0-preview.1/sentiment`
* `https://localhost:5000/text/analytics/v3.0-preview.1/sentiment`

Om du använder en slutpunkt för textanalys i molnet behöver du en nyckel. Om du kör en Textanalys lokalt kanske du inte behöver ange detta.

Nyckeln och slutpunkten skickas till Speech-containern som argument, som i följande exempel.

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
* Lagrar en Textanalys API-slutpunkt och nyckel för att skicka sentimentanalysbegäranden. 

#### <a name="phraselist-v2-on-the-speech-to-text-output"></a>Phraselist v2 på tal till text-utdata 

Från och med v2.6.0 i tal till text-containern kan du hämta utdata med dina egna fraser – antingen hela meningen eller fraser i mitten. Till exempel *den långa människan* i följande mening:

* "Det här är en **mening, den långa människan,** det här är en annan mening."

Om du vill konfigurera en fraslista måste du lägga till egna fraser när du gör anropet. Exempel:

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

Om du har flera fraser att lägga till anropar du `.addPhrase()` för varje fras för att lägga till den i fraslistan. 


# <a name="custom-speech-to-text"></a>[Anpassat tal till text](#tab/cstt)

Containern *För anpassat tal till text* förlitar sig på en anpassad talmodell. Den anpassade modellen måste ha tränats [med hjälp](how-to-custom-speech-train-model.md) av den [anpassade talportalen](https://speech.microsoft.com/customspeech).

**Modell-ID:t för anpassat** tal krävs för att köra containern. Du hittar den på sidan **Utbildning i** den anpassade talportalen. Från den anpassade talportalen går du **till sidan** Träning och väljer modellen.
<br>

![Anpassad talträningssida](media/custom-speech/custom-speech-model-training.png)

Hämta **modell-ID:t** som ska användas som argument `ModelId` till -parametern för `docker run` kommandot.
<br>

![Information om anpassad talmodell](media/custom-speech/custom-speech-model-details.png)

Följande tabell representerar de olika `docker run` parametrarna och deras motsvarande beskrivningar:

| Parameter | Beskrivning |
|---------|---------|
| `{VOLUME_MOUNT}` | Värddatorns [volymmontering](https://docs.docker.com/storage/volumes/), som docker använder för att bevara den anpassade modellen. Till exempel *C:\CustomSpeech* där *C-enheten* finns på värddatorn. |
| `{MODEL_ID}` | Id:t **för anpassad talmodell** från **sidan** Träning i den anpassade talportalen. |
| `{ENDPOINT_URI}` | Slutpunkten krävs för mätning och fakturering. Mer information finns i samla [in obligatoriska parametrar.](#gathering-required-parameters) |
| `{API_KEY}` | API-nyckeln krävs. Mer information finns i samla [in obligatoriska parametrar.](#gathering-required-parameters) |

Kör *containern För anpassat tal till text* genom att köra följande `docker run` kommando:

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

* Kör en *anpassad tal till text-container* från containeravbildningen.
* Allokerar 4 PROCESSORkärnor och 4 GB minne.
* Läser in *den anpassade tal till text-modellen* från volymens indatamontering, till exempel *C:\CustomSpeech*.
* Exponerar TCP-port 5000 och allokerar en pseudo-TTY för containern.
* Laddar ned den modell som anges `ModelId` (om den inte hittas på volymmonteringen).
* Om den anpassade modellen tidigare har hämtats `ModelId` ignoreras .
* Tar automatiskt bort containern när den har avslutats. Containeravbildningen är fortfarande tillgänglig på värddatorn.


#### <a name="base-model-download-on-the-custom-speech-to-text-container"></a>Ladda ned basmodell på den anpassade tal till text-containern  
Från och med v2.6.0 i containern custom-speech-to-text kan du hämta tillgänglig grundläggande modellinformation med hjälp av alternativet `BaseModelLocale=<locale>` . Med det här alternativet får du en lista över tillgängliga basmodeller på de lokala alternativen under ditt faktureringskonto. Exempel:

```bash
docker run --rm -it \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text \
BaseModelLocale={LOCALE} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Det här kommandot:

* Kör en *anpassad tal till text-container* från containeravbildningen.
* Kontrollera och returnera de tillgängliga basmodellerna för målets språk.

Utdata ger dig en lista över basmodeller med informationsinformationens språk, modell-ID och skapandedatumtid. Du kan använda modell-ID:t för att ladda ned och använda den specifika basmodell som du föredrar. Exempel:
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

#### <a name="custom-pronunciation-on-the-custom-speech-to-text-container"></a>Anpassat uttal i den anpassade tal till text-containern 
Från och med v2.5.0 i containern custom-speech-to-text kan du få ett anpassat uttalsresultat i utdata. Allt du behöver göra är att ha egna anpassade uttalsregler konfigurerade i din anpassade modell och montera modellen i containern "tal till text".


# <a name="text-to-speech"></a>[Text till tal](#tab/tts)

Kör text till *tal-standardcontainern* genom att köra följande `docker run` kommando.

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Det här kommandot:

* Kör en text *till tal-standardcontainer* från containeravbildningen.
* Allokerar 1 PROCESSORkärna och 2 GIGABYTE (GB) minne.
* Exponerar TCP-port 5000 och allokerar en pseudo-TTY för containern.
* Tar automatiskt bort containern när den avslutas. Containeravbildningen är fortfarande tillgänglig på värddatorn.

# <a name="neural-text-to-speech"></a>[Neural text till tal](#tab/ntts)

Kör *containern Neural Text till tal* genom att köra följande `docker run` kommando.

```bash
docker run --rm -it -p 5000:5000 --memory 12g --cpus 6 \
mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Det här kommandot:

* Kör en *neural text till tal-container* från containeravbildningen.
* Allokerar 6 PROCESSORkärnor och 12 gigabyte (GB) minne.
* Exponerar TCP-port 5000 och allokerar en pseudo-TTY för containern.
* Tar automatiskt bort containern när den avslutas. Containeravbildningen är fortfarande tillgänglig på värddatorn.

# <a name="custom-text-to-speech"></a>[Anpassad text till tal](#tab/ctts)

Den *anpassade text till tal-containern* förlitar sig på en anpassad röstmodell. Den anpassade modellen måste ha tränats [med hjälp](how-to-custom-voice-create-voice.md) av den [anpassade röstportalen.](https://aka.ms/custom-voice-portal) **Modell-ID:t för anpassad** röst krävs för att köra containern. Du hittar den på sidan **Utbildning i** den anpassade röstportalen. Från den anpassade röstportalen går du **till sidan** Träning och väljer modellen.
<br>

![Anpassad sida för röstträning](media/custom-voice/custom-voice-model-training.png)

Hämta **modell-ID:t** som ska användas som argument `ModelId` till parametern för kommandot docker run.
<br>

![Information om anpassad röstmodell](media/custom-voice/custom-voice-model-details.png)

Följande tabell representerar de olika `docker run` parametrarna och deras motsvarande beskrivningar:

| Parameter | Beskrivning |
|---------|---------|
| `{VOLUME_MOUNT}` | Värddatorns [volymmontering](https://docs.docker.com/storage/volumes/), som docker använder för att bevara den anpassade modellen. Till exempel *C:\CustomSpeech* där *C-enheten* finns på värddatorn. |
| `{MODEL_ID}` | Id:t **för anpassad talmodell** från **sidan** Träning i den anpassade röstportalen. |
| `{ENDPOINT_URI}` | Slutpunkten krävs för mätning och fakturering. Mer information finns i samla [in obligatoriska parametrar.](#gathering-required-parameters) |
| `{API_KEY}` | API-nyckeln krävs. Mer information finns i samla [in obligatoriska parametrar.](#gathering-required-parameters) |

Kör *containern Anpassad text till tal* genom att köra följande `docker run` kommando:

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

* Kör en *anpassad text till tal-container* från containeravbildningen.
* Allokerar 1 processorkärna och 2 GB minne.
* Läser in *den anpassade text till tal-modellen* från volymens indatamontering, till exempel *C:\CustomVoice*.
* Exponerar TCP-port 5000 och allokerar en pseudo-TTY för containern.
* Laddar ned den modell som anges `ModelId` (om den inte hittas på volymmonteringen).
* Om den anpassade modellen tidigare har hämtats `ModelId` ignoreras .
* Tar automatiskt bort containern när den avslutas. Containeravbildningen är fortfarande tillgänglig på värddatorn.

# <a name="speech-language-detection"></a>[Speech Språkidentifiering](#tab/lid)

Kör speech  Språkidentifiering containern genom att köra följande `docker run` kommando.

```bash
docker run --rm -it -p 5003:5003 --memory 1g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Det här kommandot: 

* Kör en språkidentifieringscontainer för tal från containeravbildningen. För närvarande debiteras du inte för att köra den här avbildningen.
* Allokerar 1 PROCESSORkärna och 1 GIGABYTE (GB) minne.
* Exponerar TCP-port 5003 och allokerar en pseudo-TTY för containern.
* Tar automatiskt bort containern när den avslutas. Containeravbildningen är fortfarande tillgänglig på värddatorn.

Om du bara skickar Speech Språkidentifiering-begäranden måste du ange Speech-klientens `phraseDetection` värde till `None` .  

```python
speech_config.set_service_property(
      name='speechcontext-phraseDetection.Mode',
      value='None',
      channel=speechsdk.ServicePropertyChannel.UriQueryParameter
   )
```

Om du vill köra den här containern med tal till text-containern kan du använda den här [Docker-avbildningen](https://hub.docker.com/r/antsu/on-prem-client). När båda containrarna har startats använder du det här Docker Run-kommandot för att köra `speech-to-text-with-languagedetection-client` .

```Docker
docker run --rm -v ${HOME}:/root -ti antsu/on-prem-client:latest ./speech-to-text-with-languagedetection-client ./audio/LanguageDetection_en-us.wav --host localhost --lport 5003 --sport 5000
```

> [!NOTE]
> Att öka antalet samtidiga anrop kan påverka tillförlitligheten och svarstiden. För språkidentifiering rekommenderar vi högst 4 samtidiga anrop med 1 CPU och 1 GB minne. För värdar med 2 processorer och 2 GB minne rekommenderar vi högst 6 samtidiga anrop.

***

> [!IMPORTANT]
> Alternativen `Eula` `Billing` , och måste anges för att `ApiKey` köra containern. Annars startar inte containern.  Mer information finns i [Fakturering](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Köra frågor mot containerns förutsägelseslutpunkt

> [!NOTE]
> Använd ett unikt portnummer om du kör flera containrar.

| Containers | URL för SDK-värd | Protokoll |
|--|--|--|
| Standard tal till text och anpassat tal till text | `ws://localhost:5000` | WS |
| Text till tal (inklusive standard, anpassad och neural), talspråksidentifiering | `http://localhost:5000` | HTTP |

Mer information om hur du använder WSS- och HTTPS-protokoll finns i [containersäkerhet.](../cognitive-services-container-support.md#azure-cognitive-services-container-security)

### <a name="speech-to-text-standard-and-custom"></a>Tal till text (standard och anpassad)

[!INCLUDE [Query Speech-to-text container endpoint](includes/speech-to-text-container-query-endpoint.md)]

#### <a name="analyze-sentiment"></a>Analysera sentiment

Om du har angett Textanalys API-autentiseringsuppgifter till [containern](#analyze-sentiment-on-the-speech-to-text-output)kan du använda Speech SDK för att skicka taligenkänningsbegäranden med attitydanalys. Du kan konfigurera API-svaren så att de använder *antingen ett enkelt* eller *detaljerat* format.
> [!NOTE]
> v1.13 av Speech Service Python SDK har ett identifierat problem med attitydanalys. Använd v1.12.x eller tidigare om du använder attitydanalys i Speech Service Python SDK.

# <a name="simple-format"></a>[Enkelt format](#tab/simple-format)

Om du vill konfigurera Speech-klienten så att den använder ett enkelt format lägger `"Sentiment"` du till som ett värde för `Simple.Extensions` . Om du vill välja en specifik Textanalys-modellversion ersätter `'latest'` du i `speechcontext-phraseDetection.sentimentAnalysis.modelversion` egenskapskonfigurationen.

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

`Simple.Extensions` returnerar sentimentresultatet i svarsrotskiktet.

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

Om du vill konfigurera Speech-klienten att använda ett detaljerat format lägger `"Sentiment"` du till som ett värde för , eller `Detailed.Extensions` `Detailed.Options` båda. Om du vill välja en specifik Textanalys-modellversion ersätter `'latest'` du i `speechcontext-phraseDetection.sentimentAnalysis.modelversion` egenskapskonfigurationen.

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

`Detailed.Extensions` ger sentimentresultat i svarsrotskiktet. `Detailed.Options` ger resultatet i `NBest` svarslagret. De kan användas separat eller tillsammans.

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

Om du vill inaktivera attitydanalys helt lägger du till `false` ett värde i `sentimentanalysis.enabled` .

```python
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentanalysis.enabled',
    value='false',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

### <a name="text-to-speech-standard-neural-and-custom"></a>Text till tal (standard, neural och anpassad)

[!INCLUDE [Query Text-to-speech container endpoint](includes/text-to-speech-container-query-endpoint.md)]

### <a name="run-multiple-containers-on-the-same-host"></a>Köra flera containrar på samma värd

Om du tänker köra flera containrar med exponerade portar måste du köra varje container med en annan exponerad port. Kör till exempel den första containern på port 5000 och den andra containern på port 5001.

Du kan ha den här containern och en Azure Cognitive Services som körs på VÄRDEN tillsammans. Du kan också ha flera containrar av samma Cognitive Services container som körs.

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Stoppa containern

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Felsökning

När du startar eller kör containern kan det uppstå problem. Använd en [utdatamontering](speech-container-configuration.md#mount-settings) och aktivera loggning. Om du gör det kan containern generera loggfiler som är användbara vid felsökning av problem.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Fakturering

Speech-containrar skickar faktureringsinformation till Azure med hjälp av en *Speech-resurs* på ditt Azure-konto.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Mer information om dessa alternativ finns i Konfigurera [containrar.](speech-container-configuration.md)

## <a name="summary"></a>Sammanfattning

I den här artikeln har du lärt dig begrepp och arbetsflöden för att ladda ned, installera och köra Speech-containrar. Sammanfattningsvis:

* Speech tillhandahåller fyra Linux-containrar för Docker som kapslar in olika funktioner:
  * *Tal till text*
  * *Anpassat tal till text*
  * *Text till tal*
  * *Anpassad text till tal*
  * *Neural text till tal*
  * *Speech Språkidentifiering*
* Containeravbildningar laddas ned från containerregistret i Azure.
* Containeravbildningar körs i Docker.
* Om du använder REST API (endast text till tal) eller SDK (tal till text eller text till tal) anger du värd-URI för containern. 
* Du måste ange faktureringsinformation när du instansierar en container.

> [!IMPORTANT]
>  Cognitive Services-containrar är inte licensierade att köras utan att vara anslutna till Azure för mätning. Kunder måste göra det möjligt för containrarna att alltid kommunicera faktureringsinformation med mätningstjänsten. Cognitive Services-containrar skickar inte kunddata (t.ex. den bild eller text som analyseras) till Microsoft.

## <a name="next-steps"></a>Nästa steg

* Granska [konfigurationsinställningarna för](speech-container-configuration.md) containrar
* Lär dig hur du [använder Speech Service-containrar med Kubernetes och Helm](speech-container-howto-on-premises.md)
* Använda fler [Cognitive Services containrar](../cognitive-services-container-support.md)
