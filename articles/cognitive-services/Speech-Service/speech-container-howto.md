---
title: Installera tal behållare – tal tjänst
titleSuffix: Azure Cognitive Services
description: Installera och kör tal behållare. Tal till text skickar ljud strömmar till text i real tid som dina program, verktyg eller enheter kan använda eller Visa. Text till tal konverterar inmatad text till mänskligt syntetiskt tal.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: aahi
ms.openlocfilehash: dc17c25a84c3d0af39bfa7a8902bdc1d93f201e8
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88518330"
---
# <a name="install-and-run-speech-service-containers-preview"></a>Installera och kör tal tjänst behållare (förhands granskning)

Med containrar kan du köra vissa Speech-tjänst-API:er i din egen miljö. Containrar är bra för specifika säkerhets- och datastyrningskrav. I den här artikeln får du lära dig om hur du laddar ned, installerar och kör en Speech-container.

Speech-containrar gör det möjligt för kunder att bygga en arkitektur för talprogram som är optimerad för både robusta molnfunktioner och gränslokalitet. Det finns fyra olika containrar tillgängliga. De två standard behållarna är **tal-till-text** och **text till tal**. De två anpassade behållarna är **Custom Speech till text** och **anpassad text till tal**. Tal behållare har samma [priser](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) som de molnbaserade Azure Speech-tjänsterna.

> [!IMPORTANT]
> Alla tal behållare erbjuds för närvarande som en del av en [offentlig "gated"-förhands granskning](../cognitive-services-container-support.md#container-availability-in-azure-cognitive-services). Ett meddelande kommer att göras när tal behållare förloppet till allmän tillgänglighet (GA).

| Funktion | Funktioner | Nya |
|--|--|--|
| Tal till text | Analyserar sentiment och beskrivar kontinuerliga tal i real tid eller batch-ljudinspelningar med mellanliggande resultat.  | 2.3.1 |
| Custom Speech till text | Genom att använda en anpassad modell från [Custom Speech portalen](https://speech.microsoft.com/customspeech), kan du skriva över kontinuerliga tal i real tid eller köra ljud inspelningar i text med mellanliggande resultat. | 2.3.1 |
| Text till tal | Konverterar text till tal med naturligt ljud med text indata eller SSML (Speech syntes Markup Language). | 1.5.0 |
| Anpassad text till tal | Med hjälp av en anpassad modell från den [anpassade röst portalen](https://aka.ms/custom-voice-portal)konverteras text till tal med naturligt ljud med text-eller tal syntess språk (SSML). | 1.5.0 |

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/cognitive-services/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Följande krav gäller innan du använder tal behållare:

| Obligatorisk | Syfte |
|--|--|
| Docker-motorn | Du behöver Docker-motorn installerad på en [värddator](#the-host-computer). Docker innehåller paket som konfigurerar Docker-miljön på [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) och [Linux](https://docs.docker.com/engine/installation/#supported-platforms). En introduktion till grunderna för Docker och containrar finns i [Docker-översikt](https://docs.docker.com/engine/docker-overview/).<br><br> Docker måste konfigureras för att tillåta att behållarna ansluter till och skicka fakturerings data till Azure. <br><br> **I Windows**måste Docker också konfigureras för att stödja Linux-behållare.<br><br> |
| Bekant med Docker | Du bör ha grundläggande kunskaper om Docker-koncept, t. ex. register, databaser, behållare och behållar avbildningar, samt kunskaper om grundläggande `docker` kommandon. |
| Tal resurs | Du måste ha följande för att kunna använda dessa behållare:<br><br>En Azure _tal_ -resurs för att hämta tillhör ande API-nyckel och slut punkts-URI. Båda värdena är tillgängliga på Azure Portalens **tal** översikt och nycklar sidor. Båda krävs för att starta behållaren.<br><br>**{Api_key}**: en av de två tillgängliga resurs nycklarna på sidan **nycklar**<br><br>**{ENDPOINT_URI}**: slut punkten enligt vad som anges på sidan **Översikt** |

## <a name="request-access-to-the-container-registry"></a>Begär åtkomst till behållar registret

Fyll i och skicka [formuläret för begäran](https://aka.ms/cognitivegate) för att begära åtkomst till behållaren. 


[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

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

# <a name="speech-to-text"></a>[Tal till text](#tab/stt)

| Container | Minimum | Rekommenderas |
|-----------|---------|-------------|
| Tal till text | 2 kärnor, 2 GB minne | 4 kärnor, 4 GB minne |

# <a name="custom-speech-to-text"></a>[Custom Speech till text](#tab/cstt)

| Container | Minimum | Rekommenderas |
|-----------|---------|-------------|
| Custom Speech till text | 2 kärnor, 2 GB minne | 4 kärnor, 4 GB minne |

# <a name="text-to-speech"></a>[Text till tal](#tab/tts)

| Container | Minimum | Rekommenderas |
|-----------|---------|-------------|
| Text till tal | 1 kärna, 2 GB minne | 2 kärnor, 3 GB minne |

# <a name="custom-text-to-speech"></a>[Anpassad text till tal](#tab/ctts)

| Container | Minimum | Rekommenderas |
|-----------|---------|-------------|
| Anpassad text till tal | 1 kärna, 2 GB minne | 2 kärnor, 3 GB minne |

***

* Varje kärna måste vara minst 2,6 gigahertz (GHz) eller snabbare.

Core och minne motsvarar `--cpus` `--memory` inställningarna och som används som en del av `docker run` kommandot.

> [!NOTE]
> Minimum och Recommended baseras på Docker-gränser, *inte* värd datorns resurser. Till exempel används tal-till-text-behållare som minnes karta för en stor språk modell, och *vi rekommenderar* att hela filen passar i minnet, vilket är ytterligare 4-6 GB. Dessutom kan den första körningen av en behållare ta längre tid, eftersom modeller växlas till minnet.

## <a name="get-the-container-image-with-docker-pull"></a>Hämta behållar avbildningen med `docker pull`

Behållar avbildningar för tal finns i följande Container Registry.

# <a name="speech-to-text"></a>[Tal till text](#tab/stt)

| Container | Lagringsplats |
|-----------|------------|
| Tal till text | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest` |

# <a name="custom-speech-to-text"></a>[Custom Speech till text](#tab/cstt)

| Container | Lagringsplats |
|-----------|------------|
| Custom Speech till text | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest` |

# <a name="text-to-speech"></a>[Text till tal](#tab/tts)

| Container | Lagringsplats |
|-----------|------------|
| Text till tal | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest` |

# <a name="custom-text-to-speech"></a>[Anpassad text till tal](#tab/ctts)

| Container | Lagringsplats |
|-----------|------------|
| Anpassad text till tal | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest` |

***

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-speech-containers"></a>Docker-hämtning för tal behållarna

# <a name="speech-to-text"></a>[Tal till text](#tab/stt)

#### <a name="docker-pull-for-the-speech-to-text-container"></a>Docker-hämtning för den tal-till-text-behållare

Använd [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) -kommandot för att hämta en behållar avbildning från förhands gransknings registret för behållare.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest
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
2.3.1-amd64-en-us-preview
```

För alla språk som stöds av **tal-till-text-** behållaren, se [taggar till text-Taggar](../containers/container-image-tags.md#speech-to-text).

# <a name="custom-speech-to-text"></a>[Custom Speech till text](#tab/cstt)

#### <a name="docker-pull-for-the-custom-speech-to-text-container"></a>Docker-hämtning för Custom Speech-till-text-behållaren

Använd [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) -kommandot för att hämta en behållar avbildning från förhands gransknings registret för behållare.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest
```

> [!NOTE]
> `locale`Och `voice` för anpassade tal behållare bestäms av den anpassade modellen som matas in av behållaren.

# <a name="text-to-speech"></a>[Text till tal](#tab/tts)

#### <a name="docker-pull-for-the-text-to-speech-container"></a>Docker-hämtning för text till tal-behållaren

Använd [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) -kommandot för att hämta en behållar avbildning från förhands gransknings registret för behållare.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest
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
1.5.0-amd64-en-us-ariarus-preview
```

För alla språk som stöds och motsvarande röster för **text till tal** -behållaren, se [taggar för text till tal-bilder](../containers/container-image-tags.md#text-to-speech).

> [!IMPORTANT]
> När du skapar ett *standard text-till-tal* -http-post kräver [SSML-](speech-synthesis-markup.md) meddelandet ett- `voice` element med ett- `name` attribut. Värdet är motsvarande behållares nationella inställningar och röst, även kallat ["kort namn"](language-support.md#standard-voices). Taggen skulle till exempel `latest` ha röst namnet `en-US-AriaRUS` .

# <a name="custom-text-to-speech"></a>[Anpassad text till tal](#tab/ctts)

#### <a name="docker-pull-for-the-custom-text-to-speech-container"></a>Docker-hämtning för den anpassade text till tal-behållaren

Använd [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) -kommandot för att hämta en behållar avbildning från förhands gransknings registret för behållare.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest
```

> [!NOTE]
> `locale`Och `voice` för anpassade tal behållare bestäms av den anpassade modellen som matas in av behållaren.

***

## <a name="how-to-use-the-container"></a>Använda behållaren

När behållaren är på [värddatorn](#the-host-computer)använder du följande process för att arbeta med behållaren.

1. [Kör behållaren](#run-the-container-with-docker-run)med de fakturerings inställningar som krävs. Fler [exempel](speech-container-configuration.md#example-docker-run-commands) på `docker run` kommandot är tillgängliga.
1. [Fråga behållarens förutsägelse slut punkt](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Kör behållaren med `docker run`

Använd kommandot [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) för att köra behållaren. Läs om hur du [samlar in nödvändiga parametrar](#gathering-required-parameters) för information om hur du hämtar `{Endpoint_URI}` och- `{API_Key}` värden. Fler [exempel](speech-container-configuration.md#example-docker-run-commands) på `docker run` kommandot är också tillgängliga.

# <a name="speech-to-text"></a>[Tal till text](#tab/stt)

Kör följande kommando för att köra den *tal-till-text-* behållaren `docker run` .

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Det här kommandot:

* Kör en *tal-till-text-* behållare från behållar avbildningen.
* Allokerar 4 processor kärnor och 4 GB minne.
* Exponerar TCP-port 5000 och allokerar en pseudo-TTY för behållaren.
* Tar automatiskt bort behållaren när den har avslut ATS. Behållar avbildningen är fortfarande tillgänglig på värddatorn.


#### <a name="analyze-sentiment-on-the-speech-to-text-output"></a>Analysera sentiment för utdata från tal till text 

Från och med v-2.2.0 av den tal-till-text-behållaren kan du anropa [sentiment Analysis v3-API: et](../text-analytics/how-tos/text-analytics-how-to-sentiment-analysis.md) på utdata. Om du vill anropa sentiment-analysen behöver du en API för textanalys resurs slut punkt. Exempel: 
* `https://westus2.api.cognitive.microsoft.com/text/analytics/v3.0-preview.1/sentiment`
* `https://localhost:5000/text/analytics/v3.0-preview.1/sentiment`

Om du har åtkomst till en text analys-slutpunkt i molnet behöver du en nyckel. Om du kör Textanalys lokalt behöver du kanske inte ange detta.

Nyckeln och slut punkten skickas till tal behållaren som argument, som i följande exempel.

```bash
docker run -it --rm -p 5000:5000 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
CloudAI:SentimentAnalysisSettings:TextAnalyticsHost={TEXT_ANALYTICS_HOST} \
CloudAI:SentimentAnalysisSettings:SentimentAnalysisApiKey={SENTIMENT_APIKEY}
```

Det här kommandot:

* Utför samma steg som kommandot ovan.
* Lagrar en API för textanalys slut punkt och nyckel för att skicka sentiment analys begär Anden. 


# <a name="custom-speech-to-text"></a>[Custom Speech till text](#tab/cstt)

*Custom Speech-till-text-* behållaren är beroende av en anpassad tal modell. Den anpassade modellen måste ha [tränats](how-to-custom-speech-train-model.md) med hjälp av den [anpassade tal portalen](https://speech.microsoft.com/customspeech).

> [!IMPORTANT]
> Custom Speech modellen måste tränas från någon av följande modell versioner:
> * **20181201 (v 3.3 enhetligt)**
> * **20190520 (v 4.14 Unified)**
> * **20190701 (v 4.17 Unified)**<br>
> ![Custom Speech träna behållar modell](media/custom-speech/custom-speech-train-model-container-scoped.png)

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
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
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

# <a name="text-to-speech"></a>[Text till tal](#tab/tts)

Kör följande kommando för att köra en *text till tal* -behållare `docker run` .

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Det här kommandot:

* Kör en *text till tal* -behållare från behållar avbildningen.
* Allokerar 1 processor kärna och 2 GB minne.
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
containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech \
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

***

> [!IMPORTANT]
> `Eula`Alternativen, `Billing` och `ApiKey` måste anges för att köra behållaren, annars startar inte behållaren.  Mer information finns i [fakturering](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Köra frågor mot containerns förutsägelseslutpunkt

> [!NOTE]
> Använd ett unikt port nummer om du kör flera behållare.

| Containrar | SDK-värd-URL | Protokoll |
|--|--|--|
| Tal till text och Custom Speech till text | `ws://localhost:5000` | WS |
| Text till tal och anpassad text till tal | `http://localhost:5000` | HTTP |

Mer information om hur du använder WSS-och HTTPS-protokoll finns i [behållar säkerhet](../cognitive-services-container-support.md#azure-cognitive-services-container-security).

[!INCLUDE [Query Speech-to-text container endpoint](includes/speech-to-text-container-query-endpoint.md)]

#### <a name="analyze-sentiment"></a>Analysera sentiment

Om du har angett dina API för textanalys autentiseringsuppgifter [för behållaren](#analyze-sentiment-on-the-speech-to-text-output)kan du använda tal-SDK för att skicka tal igenkännings begär Anden med sentiment-analys. Du kan konfigurera API-svar för att använda antingen ett *enkelt* eller *detaljerat* format.

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

### <a name="text-to-speech-or-custom-text-to-speech"></a>Text till tal eller anpassad text till tal

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

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Sammanfattning

I den här artikeln har du lärt dig begrepp och arbets flöde för att ladda ned, installera och köra tal behållare. Sammanfattningsvis:

* Tal innehåller fyra Linux-behållare för Docker, vilket kapslar olika funktioner:
  * *Tal till text*
  * *Custom Speech till text*
  * *Text till tal*
  * *Anpassad text till tal*
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
