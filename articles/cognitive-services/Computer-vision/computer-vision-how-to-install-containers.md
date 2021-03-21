---
title: Installera Läs OCR Docker-behållare från Visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Använd de Läs OCR Docker-behållare från Visuellt innehåll för att extrahera text från bilder och dokument, lokalt.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: aahi
ms.custom: seodec18, cog-serv-seo-aug-2020
keywords: lokal, OCR, Docker, container
ms.openlocfilehash: 1c9e681e3c02cb65b2a54070cc778051a0e7ac53
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102432561"
---
# <a name="install-read-ocr-docker-containers-preview"></a>Installera Läs OCR Docker-behållare (förhands granskning) 

[!INCLUDE [container hosting on the Microsoft Container Registry](../containers/includes/gated-container-hosting.md)]

Med containrar kan du köra API:erna för Visuellt innehåll i din egen miljö. Containrar är bra för specifika säkerhets- och datastyrningskrav. I den här artikeln får du lära dig hur du laddar ned, installerar och kör Visuellt innehåll behållare.

Med *Read* OCR-behållaren kan du extrahera utskrift och handskriven text från bilder och dokument med stöd för JPEG-, PNG-, BMP-, PDF-och TIFF-filformat. Mer information finns i [Read API-dokumentationen](concept-recognizing-text.md#read-api).

## <a name="read-32-preview-container"></a>Läsa 3,2 – för hands versions behållare

> [!NOTE]
> Behållaren Read 3,0-Preview är föråldrad. 

Behållaren Read 3,2-Preview OCR innehåller:
* Nya modeller för förbättrad noggrannhet.
* Stöd för flera språk i samma dokument.
* Stöd för totalt 73 språk. Se den fullständiga listan över [språk som stöds av OCR](./language-support.md#optical-character-recognition-ocr).
* En enda åtgärd för både dokument och bilder.
* Stöd för större dokument och bilder.
* Förtroende poäng.
* Stöd för dokument med både utskrift och handskriven text.
* Möjlighet att extrahera text från endast valda sidor i ett dokument.
* Välj text rads utmatnings ordning från standard till en mer naturlig Läs ordning för latinska språk.
* Text linje klassificering som handskriven stil eller inte enbart för latinska språk.

Om du använder läsa 2,0-behållare idag kan du läsa mer om ändringar i de nya versionerna i [migreringsguiden](read-container-migration-guide.md) .

## <a name="prerequisites"></a>Förutsättningar

Du måste uppfylla följande krav innan du använder behållarna:

|Obligatorisk|Syfte|
|--|--|
|Docker-motorn| Du behöver Docker-motorn installerad på en [värddator](#the-host-computer). Docker innehåller paket som konfigurerar Docker-miljön på [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) och [Linux](https://docs.docker.com/engine/installation/#supported-platforms). En introduktion till grunderna för Docker och containrar finns i [Docker-översikt](https://docs.docker.com/engine/docker-overview/).<br><br> Docker måste konfigureras för att tillåta att behållarna ansluter till och skicka fakturerings data till Azure. <br><br> **I Windows** måste Docker också konfigureras för att stödja Linux-behållare.<br><br>|
|Bekant med Docker | Du bör ha grundläggande kunskaper om Docker-koncept, t. ex. register, databaser, behållare och behållar avbildningar, samt kunskaper om grundläggande `docker` kommandon.| 
|Visuellt innehåll resurs |För att du ska kunna använda behållaren måste du ha:<br><br>En Azure **visuellt innehåll** -resurs och den tillhör ande API-nyckeln slut punkts-URI. Båda värdena är tillgängliga på sidorna översikt och nycklar för resursen och krävs för att starta behållaren.<br><br>**{Api_key}**: en av de två tillgängliga resurs nycklarna på sidan **nycklar**<br><br>**{ENDPOINT_URI}**: slut punkten enligt vad som anges på sidan **Översikt**|

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/cognitive-services/) innan du börjar.

## <a name="request-approval-to-run-the-container"></a>Begär godkännande för att köra behållaren

Fyll i och skicka [formuläret för begäran](https://aka.ms/csgate) för att begära godkännande för att köra behållaren. 

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
| Läs 2,0 – för hands version | `mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview` |
| Läs 3,2 – för hands version | `mcr.microsoft.com/azure-cognitive-services/vision/read:3.2-preview.2` |

Använd [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) kommandot för att ladda ned en behållar avbildning.

### <a name="docker-pull-for-the-read-container"></a>Docker pull för Läs behållaren

# <a name="version-32-preview"></a>[Version 3,2 – för hands version](#tab/version-3-2)

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/vision/read:3.2-preview.2
```

# <a name="version-20-preview"></a>[Version 2,0 – för hands version](#tab/version-2)

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview
```

---

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Använda behållaren

När behållaren är på [värddatorn](#the-host-computer)använder du följande process för att arbeta med behållaren.

1. [Kör behållaren](#run-the-container-with-docker-run)med de fakturerings inställningar som krävs. Fler [exempel](computer-vision-resource-container-config.md) på `docker run` kommandot är tillgängliga. 
1. [Fråga behållarens förutsägelse slut punkt](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Kör behållaren med `docker run`

Använd kommandot [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) för att köra behållaren. Läs om hur du [samlar in nödvändiga parametrar](#gathering-required-parameters) för information om hur du hämtar `{ENDPOINT_URI}` och- `{API_KEY}` värden.

[Exempel](computer-vision-resource-container-config.md#example-docker-run-commands) på `docker run` kommandot är tillgängliga.

# <a name="version-32-preview"></a>[Version 3,2 – för hands version](#tab/version-3-2)

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.2-preview.2 \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Det här kommandot:

* Kör Läs containern från behållar avbildningen.
* Allokerar 8 processor kärnor och 18 GB minne.
* Exponerar TCP-port 5000 och allokerar en pseudo-TTY för behållaren.
* Tar automatiskt bort behållaren när den har avslut ATS. Behållar avbildningen är fortfarande tillgänglig på värddatorn.

# <a name="version-20-preview"></a>[Version 2,0 – för hands version](#tab/version-2)

```bash
docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Det här kommandot:

* Kör Läs containern från behållar avbildningen.
* Allokerar 8 processor kärnor och 16 GB minne.
* Exponerar TCP-port 5000 och allokerar en pseudo-TTY för behållaren.
* Tar automatiskt bort behållaren när den har avslut ATS. Behållar avbildningen är fortfarande tillgänglig på värddatorn.

---


Fler [exempel](./computer-vision-resource-container-config.md#example-docker-run-commands) på `docker run` kommandot är tillgängliga. 

> [!IMPORTANT]
> `Eula`Alternativen, `Billing` och `ApiKey` måste anges för att köra behållaren, annars startar inte behållaren.  Mer information finns i [fakturering](#billing).

Om du behöver högre data flöde (till exempel när du bearbetar filer på flera sidor) bör du överväga att distribuera flera behållare [i ett Kubernetes-kluster](deploy-computer-vision-on-premises.md)med hjälp av [Azure Storage](../../storage/common/storage-account-create.md) och [Azure Queue](../../storage/queues/storage-queues-introduction.md).

Om du använder Azure Storage för att lagra avbildningar för bearbetning kan du skapa en [anslutnings sträng](../../storage/common/storage-configure-connection-string.md) som ska användas när du anropar behållaren.

Så här hittar du anslutnings strängen:

1. Gå till **lagrings konton** på Azure Portal och hitta ditt konto.
2. Klicka på **åtkomst nycklar** i den vänstra navigerings listan.
3. Anslutnings strängen kommer att finnas under **anslutnings strängen**

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Köra frågor mot containerns förutsägelseslutpunkt

Containern innehåller REST-baserade slutpunkts-API:er för frågeförutsägelse. 

# <a name="version-32-preview"></a>[Version 3,2 – för hands version](#tab/version-3-2)

Använd värden, `http://localhost:5000`, för container-API:er. Du kan visa Swagger-sökvägen på: `http://localhost:5000/swagger/vision-v3.2-preview-read/swagger.json` .

# <a name="version-20-preview"></a>[Version 2,0 – för hands version](#tab/version-2)

Använd värden, `http://localhost:5000`, för container-API:er. Du kan visa Swagger-sökvägen på: `http://localhost:5000/swagger/vision-v2.0-preview-read/swagger.json` .

---

### <a name="asynchronous-read"></a>Asynkron läsning


# <a name="version-32-preview"></a>[Version 3,2 – för hands version](#tab/version-3-2)

Du kan använda- `POST /vision/v3.2/read/analyze` och `GET /vision/v3.2/read/operations/{operationId}` -åtgärderna i samförstånd för att läsa en avbildning asynkront, på liknande sätt som visuellt innehåll tjänsten använder motsvarande rest-åtgärder. Metoden asynkron POST returnerar en `operationId` som används som identifierare till HTTP GET-begäran.


I Swagger-ANVÄNDARGRÄNSSNITTET väljer du alternativet `Analyze` för att expandera det i webbläsaren. Välj sedan **testa den**  >  **Välj fil**. I det här exemplet ska vi använda följande bild:

![tabbar eller blank steg](media/tabs-vs-spaces.png)

När det asynkrona inlägget har körts returneras en status kod för **HTTP 202** . Som en del av svaret finns ett `operation-location` huvud som innehåller slut punkten för resultatet.

```http
 content-length: 0
 date: Fri, 04 Sep 2020 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v3.2/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

`operation-location`Är den fullständigt kvalificerade URL: en och nås via en HTTP Get. Här är JSON-svaret från att köra `operation-location` URL: en från föregående bild:

```json
{
  "status": "succeeded",
  "createdDateTime": "2021-02-04T06:32:08.2752706+00:00",
  "lastUpdatedDateTime": "2021-02-04T06:32:08.7706172+00:00",
  "analyzeResult": {
    "version": "3.2.0",
    "readResults": [
      {
        "page": 1,
        "angle": 2.1243,
        "width": 502,
        "height": 252,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              58,
              42,
              314,
              59,
              311,
              123,
              56,
              121
            ],
            "text": "Tabs vs",
            "appearance": {
              "style": {
                "name": "handwriting",
                "confidence": 0.96
              }
            },
            "words": [
              {
                "boundingBox": [
                  68,
                  44,
                  225,
                  59,
                  224,
                  122,
                  66,
                  123
                ],
                "text": "Tabs",
                "confidence": 0.933
              },
              {
                "boundingBox": [
                  241,
                  61,
                  314,
                  72,
                  314,
                  123,
                  239,
                  122
                ],
                "text": "vs",
                "confidence": 0.977
              }
            ]
          },
          {
            "boundingBox": [
              286,
              171,
              415,
              165,
              417,
              197,
              287,
              201
            ],
            "text": "paces",
            "appearance": {
              "style": {
                "name": "handwriting",
                "confidence": 0.746
              }
            },
            "words": [
              {
                "boundingBox": [
                  286,
                  179,
                  404,
                  166,
                  405,
                  198,
                  290,
                  201
                ],
                "text": "paces",
                "confidence": 0.938
              }
            ]
          }
        ]
      }
    ]
  }
}
```

# <a name="version-20-preview"></a>[Version 2,0 – för hands version](#tab/version-2)

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

---

> [!IMPORTANT]
> Om du distribuerar flera Läs behållare bakom en belastningsutjämnare, till exempel, under Docker Compose eller Kubernetes, måste du ha ett externt cacheminne. Eftersom bearbetnings behållaren och behållaren GET Request inte kan vara samma, lagrar ett externt cacheminne resultaten och delar över behållare. Mer information om cacheinställningar finns i [konfigurera visuellt innehåll Docker-behållare](./computer-vision-resource-container-config.md).

### <a name="synchronous-read"></a>Synkron läsning

Du kan använda följande åtgärd för att synkront läsa en avbildning. 

# <a name="version-32-preview"></a>[Version 3,2 – för hands version](#tab/version-3-2)

`POST /vision/v3.2/read/syncAnalyze` 

# <a name="version-20-preview"></a>[Version 2,0 – för hands version](#tab/version-2)

`POST /vision/v2.0/read/core/Analyze`

---

När bilden har lästs in helt och hållet returnerar API: et ett JSON-svar. Det enda undantaget är om ett fel inträffar. När ett fel inträffar returneras följande JSON:

```json
{
    "status": "Failed"
}
```

Objektet JSON Response har samma objekt diagram som den asynkrona versionen. Om du är en JavaScript-användare och vill ha typ säkerhet bör du överväga att använda TypeScript för att omvandla JSON-svaret.

Ett exempel på användnings fall finns i <a href="https://aka.ms/ts-read-api-types" target="_blank" rel="noopener noreferrer">sand Box typescript-sandbox här</a> och välj **Kör** för att visualisera den lättanvända användningen.

## <a name="stop-the-container"></a>Stoppa containern

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Felsökning

Om du kör behållaren med en utgående [montering](./computer-vision-resource-container-config.md#mount-settings) och loggning aktive rad genererar behållaren loggfiler som är till hjälp vid fel sökning av problem som inträffar när du startar eller kör behållaren.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Fakturering

Cognitive Services behållare skickar fakturerings information till Azure med motsvarande resurs på ditt Azure-konto.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Mer information om dessa alternativ finns i [Configure containers](./computer-vision-resource-container-config.md). 

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
* Läs [visuellt innehåll översikt](overview.md) och lär dig mer om att känna igen utskrift och handskriven text
* Mer information om de metoder som stöds av behållaren finns i [API för visuellt innehåll](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) .
* Läs vanliga [frågor och svar (FAQ)](FAQ.md) för att lösa problem som rör visuellt innehåll-funktioner.
* Använd fler [Cognitive Services behållare](../cognitive-services-container-support.md)
