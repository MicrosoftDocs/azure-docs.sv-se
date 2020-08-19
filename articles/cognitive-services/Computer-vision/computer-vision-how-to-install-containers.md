---
title: Installera och köra behållare – Visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Hämta, installera och kör behållare för Visuellt innehåll i den här själv studie kursen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: aahi
ms.custom: seodec18
ms.openlocfilehash: 70cbb21430253dc9683cd3803f2a09ef8bb858cb
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88545649"
---
# <a name="install-and-run-read-containers-preview"></a>Installera och köra Läs behållare (förhands granskning)

Med containrar kan du köra API:erna för Visuellt innehåll i din egen miljö. Containrar är bra för specifika säkerhets- och datastyrningskrav. I den här artikeln får du lära dig om hur du laddar ned, installerar och kör en Visuellt innehåll-container.

En enda Docker-behållare, *Läs*, är tillgänglig för visuellt innehåll. Med *Läs* behållaren kan du identifiera och extrahera *utskriven text* från bilder av olika objekt med olika ytor och bakgrunder, till exempel kvitton, affischer och visitkort. Dessutom identifierar *läsnings* behållaren *handskriven text* i bilder och innehåller PDF-, TIFF-och fil stöd för flera sidor. Mer information finns i [Read](concept-recognizing-text.md#read-api) API-dokumentationen.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/cognitive-services/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Du måste uppfylla följande krav innan du använder behållarna:

|Obligatorisk|Syfte|
|--|--|
|Docker-motorn| Du behöver Docker-motorn installerad på en [värddator](#the-host-computer). Docker innehåller paket som konfigurerar Docker-miljön på [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) och [Linux](https://docs.docker.com/engine/installation/#supported-platforms). En introduktion till grunderna för Docker och containrar finns i [Docker-översikt](https://docs.docker.com/engine/docker-overview/).<br><br> Docker måste konfigureras för att tillåta att behållarna ansluter till och skicka fakturerings data till Azure. <br><br> **I Windows**måste Docker också konfigureras för att stödja Linux-behållare.<br><br>|
|Bekant med Docker | Du bör ha grundläggande kunskaper om Docker-koncept, t. ex. register, databaser, behållare och behållar avbildningar, samt kunskaper om grundläggande `docker` kommandon.| 
|Visuellt innehåll resurs |För att du ska kunna använda behållaren måste du ha:<br><br>En Azure **visuellt innehåll** -resurs och den tillhör ande API-nyckeln slut punkts-URI. Båda värdena är tillgängliga på sidorna översikt och nycklar för resursen och krävs för att starta behållaren.<br><br>**{Api_key}**: en av de två tillgängliga resurs nycklarna på sidan **nycklar**<br><br>**{ENDPOINT_URI}**: slut punkten enligt vad som anges på sidan **Översikt**|

## <a name="request-access-to-the-private-container-registry"></a>Begär åtkomst till registret för privat behållare

Fyll i och skicka [formuläret för begäran](https://aka.ms/cognitivegate) för att begära åtkomst till behållaren. 

[!INCLUDE [Request access to public preview](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>Värddatorn

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Stöd för avancerad Vector-tillägg

**Värddatorn** är den dator som kör Docker-behållaren. Värden *måste ha stöd* för [Advanced Vector Extensions](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). Du kan söka efter AVX2-stöd på Linux-värdar med följande kommando:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> Värddatorn *krävs* för att stödja AVX2. Containern fungerar *inte* korrekt utan AVX2-stöd.

### <a name="container-requirements-and-recommendations"></a>Krav och rekommendationer för behållare

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="get-the-container-image-with-docker-pull"></a>Hämta behållar avbildningen med `docker pull`

Behållar avbildningar för läsning är tillgängliga.

| Container | Container Registry/namn på lagrings plats/avbildning |
|-----------|------------|
| Läsa | `containerpreview.azurecr.io/microsoft/cognitive-services-read:latest` |

Använd [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) kommandot för att ladda ned en behållar avbildning.

### <a name="docker-pull-for-the-read-container"></a>Docker pull för Läs behållaren

```bash
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-read:latest
```

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Använda behållaren

När behållaren är på [värddatorn](#the-host-computer)använder du följande process för att arbeta med behållaren.

1. [Kör behållaren](#run-the-container-with-docker-run)med de fakturerings inställningar som krävs. Fler [exempel](computer-vision-resource-container-config.md) på `docker run` kommandot är tillgängliga. 
1. [Fråga behållarens förutsägelse slut punkt](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Kör behållaren med `docker run`

Använd kommandot [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) för att köra behållaren. Läs om hur du [samlar in nödvändiga parametrar](#gathering-required-parameters) för information om hur du hämtar `{ENDPOINT_URI}` och- `{API_KEY}` värden.

[Exempel](computer-vision-resource-container-config.md#example-docker-run-commands) på `docker run` kommandot är tillgängliga.

```bash
docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
containerpreview.azurecr.io/microsoft/cognitive-services-read \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Det här kommandot:

* Kör Läs containern från behållar avbildningen.
* Allokerar 8 processor kärnor och 16 GB minne.
* Exponerar TCP-port 5000 och allokerar en pseudo-TTY för behållaren.
* Tar automatiskt bort behållaren när den har avslut ATS. Behållar avbildningen är fortfarande tillgänglig på värddatorn.

Fler [exempel](./computer-vision-resource-container-config.md#example-docker-run-commands) på `docker run` kommandot är tillgängliga. 

> [!IMPORTANT]
> `Eula`Alternativen, `Billing` och `ApiKey` måste anges för att köra behållaren, annars startar inte behållaren.  Mer information finns i [fakturering](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Köra frågor mot containerns förutsägelseslutpunkt

Containern innehåller REST-baserade slutpunkts-API:er för frågeförutsägelse. 

Använd värden, `http://localhost:5000`, för container-API:er.

### <a name="asynchronous-read"></a>Asynkron läsning

Du kan använda- `POST /vision/v2.0/read/core/asyncBatchAnalyze` och `GET /vision/v2.0/read/operations/{operationId}` -åtgärderna i samförstånd för att läsa en avbildning asynkront, på liknande sätt som visuellt innehåll tjänsten använder motsvarande rest-åtgärder. Metoden asynkron POST returnerar en `operationId` som används som identifierare till HTTP GET-begäran.

I Swagger-ANVÄNDARGRÄNSSNITTET väljer du alternativet `asyncBatchAnalyze` för att expandera det i webbläsaren. Välj sedan **testa den**  >  **Välj fil**. I det här exemplet ska vi använda följande bild:

![tabbar eller blank steg](media/tabs-vs-spaces.png)

När det asynkrona inlägget har körts returneras en status kod för **HTTP 202** . Som en del av svaret finns ett `operation-location` huvud som innehåller slut punkten för resultatet.

```http
 content-length: 0
 date: Fri, 13 Sep 2019 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v2.0/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

`operation-location`Är den fullständigt kvalificerade URL: en och nås via en HTTP Get. Här är JSON-svaret från att köra `operation-location` URL: en från föregående bild:

```json
{
  "status": "Succeeded",
  "recognitionResults": [
    {
      "page": 1,
      "clockwiseOrientation": 2.42,
      "width": 502,
      "height": 252,
      "unit": "pixel",
      "lines": [
        {
          "boundingBox": [ 56, 39, 317, 50, 313, 134, 53, 123 ],
          "text": "Tabs VS",
          "words": [
            {
              "boundingBox": [ 90, 43, 243, 53, 243, 123, 94, 125 ],
              "text": "Tabs",
              "confidence": "Low"
            },
            {
              "boundingBox": [ 259, 55, 313, 62, 313, 122, 259, 123 ],
              "text": "VS"
            }
          ]
        },
        {
          "boundingBox": [ 221, 148, 417, 146, 417, 206, 227, 218 ],
          "text": "Spaces",
          "words": [
            {
              "boundingBox": [ 230, 148, 416, 141, 419, 211, 232, 218 ],
              "text": "Spaces"
            }
          ]
        }
      ]
    }
  ]
}
```

### <a name="synchronous-read"></a>Synkron läsning

Du kan använda `POST /vision/v2.0/read/core/Analyze` åtgärden för att synkront läsa en avbildning. När bilden har lästs in helt och hållet returnerar API: et ett JSON-svar. Det enda undantaget är om ett fel inträffar. När ett fel inträffar returneras följande JSON:

```json
{
    status: "Failed"
}
```

Objektet JSON Response har samma objekt diagram som den asynkrona versionen. Om du är en JavaScript-användare och vill ha typ säkerhet kan följande typer användas för att omvandla JSON-svaret som ett `AnalyzeResult` objekt.

```typescript
export interface AnalyzeResult {
    status: Status;
    recognitionResults?: RecognitionResult[] | null;
}

export enum Status {
    NotStarted = 0,
    Running = 1,
    Failed = 2,
    Succeeded = 3
}

export enum Unit {
    Pixel = 0,
    Inch = 1
}

export interface RecognitionResult {
    page?: number | null;
    clockwiseOrientation?: number | null;
    width?: number | null;
    height?: number | null;
    unit?: Unit | null;
    lines?: Line[] | null;
}

export interface Line {
    boundingBox?: number[] | null;
    text: string;
    words?: Word[] | null;
}

export enum Confidence {
    High = 0,
    Low = 1
}

export interface Word {
  boundingBox?: number[] | null;
  text: string;
  confidence?: Confidence | null;
}
```

Ett exempel på användnings fall finns i <a href="https://aka.ms/ts-read-api-types" target="_blank" rel="noopener noreferrer">sand Box typescript- <span class="docon docon-navigate-external x-hidden-focus"></span> sandbox här</a> och välj **Kör** för att visualisera den lättanvända användningen.

## <a name="stop-the-container"></a>Stoppa containern

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Felsökning

Om du kör behållaren med en utgående [montering](./computer-vision-resource-container-config.md#mount-settings) och loggning aktive rad genererar behållaren loggfiler som är till hjälp vid fel sökning av problem som inträffar när du startar eller kör behållaren.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Fakturering

Cognitive Services behållare skickar fakturerings information till Azure med motsvarande resurs på ditt Azure-konto.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Mer information om dessa alternativ finns i [Configure containers](./computer-vision-resource-container-config.md).

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Sammanfattning

I den här artikeln har du lärt dig begrepp och arbets flöde för att ladda ned, installera och köra Visuellt innehåll behållare. Sammanfattningsvis:

* Visuellt innehåll tillhandahåller en Linux-behållare för Docker, inkapsling av läsa.
* Behållar avbildningar laddas ned från behållar förhands gransknings registret i Azure.
* Behållar avbildningar körs i Docker.
* Du kan använda antingen REST API eller SDK för att anropa åtgärder i Read containers genom att ange värd-URI för behållaren.
* Du måste ange fakturerings information när du instansierar en behållare.

> [!IMPORTANT]
> Cognitive Services behållare är inte licensierade att köras utan att vara anslutna till Azure för mätning. Kunderna behöver göra det möjligt för behållarna att kommunicera fakturerings information med mät tjänsten hela tiden. Cognitive Services behållare skickar inte kund information (till exempel den bild eller text som analyseras) till Microsoft.

## <a name="next-steps"></a>Nästa steg

* Granska [Konfigurera behållare](computer-vision-resource-container-config.md) för konfigurations inställningar
* Läs [visuellt innehåll översikt](Home.md) och lär dig mer om att känna igen utskrift och handskriven text
* Mer information om de metoder som stöds av behållaren finns i [API för visuellt innehåll](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) .
* Läs vanliga [frågor och svar (FAQ)](FAQ.md) för att lösa problem som rör visuellt innehåll-funktioner.
* Använd fler [Cognitive Services behållare](../cognitive-services-container-support.md)
