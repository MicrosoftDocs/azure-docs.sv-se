---
title: Installera Read OCR Docker-containrar från Visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Använd Read OCR Docker-containrar från Visuellt innehåll för att extrahera text från avbildningar och dokument lokalt.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: aahi
ms.custom: seodec18, cog-serv-seo-aug-2020
keywords: lokalt, OCR, Docker, container
ms.openlocfilehash: dead48d7d449d1d403359c518eb842b32a54c634
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779101"
---
# <a name="install-read-ocr-docker-containers"></a>Installera Read OCR Docker-containrar

[!INCLUDE [container hosting on the Microsoft Container Registry](../containers/includes/gated-container-hosting.md)]

Med containrar kan du köra API:erna för Visuellt innehåll i din egen miljö. Containrar är bra för specifika säkerhets- och datastyrningskrav. I den här artikeln får du lära dig hur du laddar ned, installerar och kör Visuellt innehåll containrar.

Med *Read* OCR-containern kan du extrahera tryckt och handskriven text från bilder och dokument med stöd för filformaten JPEG, PNG, BMP, PDF och TIFF. Mer information finns i [läs-API:et i guiden](Vision-API-How-to-Topics/call-read-api.md).

## <a name="read-32-container"></a>Read 3.2-container

Read 3.2 OCR-containern innehåller:
* Nya modeller för förbättrad noggrannhet.
* Stöd för flera språk i samma dokument.
* Stöd för totalt 73 språk. Se den fullständiga listan över [OCR-språk som stöds.](./language-support.md#optical-character-recognition-ocr)
* En enda åtgärd för både dokument och bilder.
* Stöd för större dokument och bilder.
* Förtroendepoäng.
* Stöd för dokument med både utskrifts- och handskriven text.
* Möjlighet att extrahera text från endast valda sidor i ett dokument.
* Välj utdataordning för textrad från standard till en mer naturlig läsordning endast för latinska språk.
* Textradsklassificering som handskriven stil eller inte endast för latinska språk.

Om du använder Read 2.0-containrar idag kan du läsa [migreringsguiden för](read-container-migration-guide.md) att lära dig om ändringar i de nya versionerna.

## <a name="prerequisites"></a>Förutsättningar

Du måste uppfylla följande krav innan du använder containrarna:

|Obligatorisk|Syfte|
|--|--|
|Docker-motorn| Docker-motorn måste vara installerad på en [värddator.](#the-host-computer) Docker innehåller paket som konfigurerar Docker-miljön på [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) och [Linux](https://docs.docker.com/engine/installation/#supported-platforms). En introduktion till grunderna för Docker och containrar finns i [Docker-översikt](https://docs.docker.com/engine/docker-overview/).<br><br> Docker måste konfigureras så att containrarna kan ansluta till och skicka faktureringsdata till Azure. <br><br> **I Windows** måste Docker också konfigureras för att stödja Linux-containrar.<br><br>|
|Kunskaper om Docker | Du bör ha en grundläggande förståelse för Docker-begrepp som register, lagringsdatabaser, containrar och containeravbildningar, samt kunskaper om grundläggande `docker` kommandon.| 
|Visuellt innehåll resurs |För att kunna använda containern måste du ha:<br><br>En Azure **Visuellt innehåll resurs** och den associerade API-nyckeln slutpunktens URI. Båda värdena är tillgängliga på sidorna Översikt och Nycklar för resursen och krävs för att starta containern.<br><br>**{API_KEY}**: En av de två tillgängliga resursnycklarna på **sidan** Nycklar<br><br>**{ENDPOINT_URI}**: Slutpunkten som anges på **översiktssidan**|

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/cognitive-services/) innan du börjar.

## <a name="request-approval-to-run-the-container"></a>Begära godkännande för att köra containern

Fyll i och skicka formuläret [för begäran för](https://aka.ms/csgate) att begära godkännande för att köra containern. 

[!INCLUDE [Request access to run the container](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>Värddatorn

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Stöd för avancerat vektortillägg

**Värddatorn** är den dator som kör Docker-containern. Värden måste *ha stöd för* Advanced Vector [Extensions](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). Du kan söka efter AVX2-stöd på Linux-värdar med följande kommando:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```

> [!WARNING]
> Värddatorn krävs för *att* stödja AVX2. Containern *fungerar inte* korrekt utan STÖD för AVX2.

### <a name="container-requirements-and-recommendations"></a>Krav och rekommendationer för containrar

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="get-the-container-image-with-docker-pull"></a>Hämta containeravbildningen med `docker pull`

Containeravbildningar för Läsa är tillgängliga.

| Container | Container Registry/lagringsplats/avbildningsnamn |
|-----------|------------|
| Läs 2.0-förhandsgranskning | `mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview` |
| Läs 3.2 | `mcr.microsoft.com/azure-cognitive-services/vision/read:3.2` |

Använd kommandot [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) för att ladda ned en containeravbildning.

### <a name="docker-pull-for-the-read-ocr-container"></a>Docker-pull för Read OCR-containern

# <a name="version-32"></a>[Version 3.2](#tab/version-3-2)

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/vision/read:3.2
```

# <a name="version-20-preview"></a>[Version 2.0-preview](#tab/version-2)

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview
```

---

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Så här använder du containern

När containern finns på [värddatorn](#the-host-computer)använder du följande process för att arbeta med containern.

1. [Kör containern](#run-the-container-with-docker-run)med de faktureringsinställningar som krävs. Fler [exempel](computer-vision-resource-container-config.md) på `docker run` kommandot är tillgängliga. 
1. [Fråga containerns förutsägelseslutpunkt](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Kör containern med `docker run`

Använd kommandot [docker run](https://docs.docker.com/engine/reference/commandline/run/) för att köra containern. Information om [hur du hämtar värdena](#gathering-required-parameters) och finns i samla in obligatoriska `{ENDPOINT_URI}` `{API_KEY}` parametrar.

[Exempel](computer-vision-resource-container-config.md#example-docker-run-commands) på `docker run` kommandot är tillgängliga.

# <a name="version-32"></a>[Version 3.2](#tab/version-3-2)

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.2 \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Det här kommandot:

* Kör Read OCR-containern från containeravbildningen.
* Allokerar 8 processorkärnor och 18 GB minne.
* Exponerar TCP-port 5000 och allokerar en pseudo-TTY för containern.
* Tar automatiskt bort containern när den har avslutats. Containeravbildningen är fortfarande tillgänglig på värddatorn.

# <a name="version-20-preview"></a>[Version 2.0-preview](#tab/version-2)

```bash
docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Det här kommandot:

* Kör Read OCR-containern från containeravbildningen.
* Allokerar 8 processorkärnor och 16 GB minne.
* Exponerar TCP-port 5000 och allokerar en pseudo-TTY för containern.
* Tar automatiskt bort containern när den har avslutats. Containeravbildningen är fortfarande tillgänglig på värddatorn.

---


Fler [exempel](./computer-vision-resource-container-config.md#example-docker-run-commands) på `docker run` kommandot är tillgängliga. 

> [!IMPORTANT]
> Alternativen `Eula` `Billing` , och måste anges för att `ApiKey` köra containern. Annars startar inte containern.  Mer information finns i [Fakturering.](#billing)

Om du behöver högre dataflöde (till exempel vid bearbetning av filer med flera sidor) bör du överväga att distribuera flera containrar i ett [Kubernetes-kluster](deploy-computer-vision-on-premises.md)med hjälp [Azure Storage](../../storage/common/storage-account-create.md) och [Azure Queue](../../storage/queues/storage-queues-introduction.md).

Om du använder en Azure Storage för att lagra avbildningar för bearbetning kan du skapa en [anslutningssträng som](../../storage/common/storage-configure-connection-string.md) ska användas när du anropar containern.

Så här hittar du anslutningssträngen:

1. Gå till **Lagringskonton** på Azure Portal och leta reda på ditt konto.
2. Klicka på **Åtkomstnycklar** i den vänstra navigeringslistan.
3. Anslutningssträngen finns under **Anslutningssträng**

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Köra frågor mot containerns förutsägelseslutpunkt

Containern innehåller REST-baserade slutpunkts-API:er för frågeförutsägelse. 

# <a name="version-32"></a>[Version 3.2](#tab/version-3-2)

Använd värden, `http://localhost:5000`, för container-API:er. Du kan visa Swagger-sökvägen på: `http://localhost:5000/swagger/vision-v3.2-read/swagger.json` .

# <a name="version-20-preview"></a>[Version 2.0-preview](#tab/version-2)

Använd värden, `http://localhost:5000`, för container-API:er. Du kan visa Swagger-sökvägen på: `http://localhost:5000/swagger/vision-v2.0-preview-read/swagger.json` .

---

### <a name="asynchronous-read"></a>Asynkron läsning


# <a name="version-32"></a>[Version 3.2](#tab/version-3-2)

Du kan använda åtgärderna och tillsammans för att asynkront läsa en bild, på samma sätt som `POST /vision/v3.2/read/analyze` `GET /vision/v3.2/read/operations/{operationId}` Visuellt innehåll-tjänsten använder motsvarande REST-åtgärder. Den asynkrona POST-metoden returnerar `operationId` en som används som identifer till HTTP GET-begäran.


Från Swagger UI väljer du för `Analyze` att expandera det i webbläsaren. Välj sedan **Prova Välj**  >  **fil.** I det här exemplet använder vi följande bild:

![tabbar jämfört med blanksteg](media/tabs-vs-spaces.png)

När den asynkrona POST har körts returneras **statuskoden HTTP 202.** Som en del av svaret finns det en `operation-location` rubrik som innehåller resultatslutpunkten för begäran.

```http
 content-length: 0
 date: Fri, 04 Sep 2020 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v3.2/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

`operation-location`är den fullständigt kvalificerade URL:en och nås via en HTTP GET. Här är JSON-svaret från körning av `operation-location` URL:en från föregående bild:

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

# <a name="version-20-preview"></a>[Version 2.0-preview](#tab/version-2)

Du kan använda åtgärderna och tillsammans för att asynkront läsa en bild, på samma sätt som `POST /vision/v2.0/read/core/asyncBatchAnalyze` `GET /vision/v2.0/read/operations/{operationId}` Visuellt innehåll-tjänsten använder motsvarande REST-åtgärder. Den asynkrona POST-metoden returnerar `operationId` en som används som identifer till HTTP GET-begäran.

Från Swagger UI väljer du för `asyncBatchAnalyze` att expandera det i webbläsaren. Välj sedan **Prova Välj**  >  **fil.** I det här exemplet använder vi följande bild:

![tabbar jämfört med blanksteg](media/tabs-vs-spaces.png)

När den asynkrona POST har körts returneras **statuskoden HTTP 202.** Som en del av svaret finns det en `operation-location` rubrik som innehåller resultatslutpunkten för begäran.

```http
 content-length: 0
 date: Fri, 13 Sep 2019 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v2.0/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

`operation-location`är den fullständigt kvalificerade URL:en och nås via en HTTP GET. Här är JSON-svaret från körning av `operation-location` URL:en från föregående bild:

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
> Om du distribuerar flera Read OCR-containrar bakom en lastbalanserare, till exempel under Docker Compose eller Kubernetes, måste du ha en extern cache. Eftersom bearbetningscontainern och GET-begärandecontainern kanske inte är samma, lagrar ett externt cacheminne resultaten och delar dem mellan containrar. Mer information om cacheinställningar finns i Konfigurera [Visuellt innehåll Docker-containrar.](./computer-vision-resource-container-config.md)

### <a name="synchronous-read"></a>Synkron läsning

Du kan använda följande åtgärd för att läsa en bild synkront. 

# <a name="version-32"></a>[Version 3.2](#tab/version-3-2)

`POST /vision/v3.2/read/syncAnalyze` 

# <a name="version-20-preview"></a>[Version 2.0-preview](#tab/version-2)

`POST /vision/v2.0/read/core/Analyze`

---

När bilden läses i sin helhet returnerar API:et sedan ett JSON-svar. Det enda undantaget till detta är om ett fel inträffar. När ett fel inträffar returneras följande JSON:

```json
{
    "status": "Failed"
}
```

JSON-svarsobjektet har samma objektdiagram som den asynkrona versionen. Om du är JavaScript-användare och vill ha typsäkerhet bör du överväga att använda TypeScript för att typge JSON-svaret.

Ett exempel på ett användningsfall finns i <a href="https://aka.ms/ts-read-api-types" target="_blank" rel="noopener noreferrer">Sandbox-miljön för TypeScript här</a> och väljer **Kör** för att visualisera dess användarvänlighet.

## <a name="stop-the-container"></a>Stoppa containern

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Felsökning

Om du kör containern [](./computer-vision-resource-container-config.md#mount-settings) med en aktiverad montering och loggning av utdata genererar containern loggfiler som är användbara för att felsöka problem som inträffar när du startar eller kör containern.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Fakturering

Containern Cognitive Services faktureringsinformation till Azure med hjälp av motsvarande resurs på ditt Azure-konto.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Mer information om dessa alternativ finns i Konfigurera [containrar.](./computer-vision-resource-container-config.md) 

## <a name="summary"></a>Sammanfattning

I den här artikeln har du lärt dig begrepp och arbetsflöden för att ladda ned, installera och köra Visuellt innehåll containrar. Sammanfattningsvis:

* Visuellt innehåll tillhandahåller en Linux-container för Docker som kapslar in Läsning.
* Containeravbildningen read kräver att ett program kör den. 
* Containeravbildningar körs i Docker.
* Du kan använda antingen REST API sdk för att anropa åtgärder i Read OCR-containrar genom att ange värd-URI för containern.
* Du måste ange faktureringsinformation när du instansierar en container.

> [!IMPORTANT]
> Cognitive Services-containrar är inte licensierade att köras utan att vara anslutna till Azure för mätning. Kunder måste göra det möjligt för containrarna att alltid kommunicera faktureringsinformation med mätningstjänsten. Cognitive Services-containrar skickar inte kunddata (till exempel den bild eller text som analyseras) till Microsoft.

## <a name="next-steps"></a>Nästa steg

* Granska Konfigurera [containrar](computer-vision-resource-container-config.md) för konfigurationsinställningar
* Granska [OCR-översikten om](overview-ocr.md) du vill veta mer om igenkänning av tryckt och handskriven text
* Mer information om [de metoder som stöds](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) av containern finns i Api:et Läs.
* Se Vanliga [frågor och svar för att](FAQ.md) lösa problem som rör Visuellt innehåll funktioner.
* Använda fler [Cognitive Services containrar](../cognitive-services-container-support.md)
