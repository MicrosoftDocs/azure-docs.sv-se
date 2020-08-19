---
title: Installera och köra behållare för formulär tolken
titleSuffix: Azure Cognitive Services
description: Den här artikeln beskriver hur du använder Azure Cognitive Services formulär igenkännings behållare för att tolka formulär-och tabell data.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: aahi
ms.openlocfilehash: cdcf4182f098affba0a18be5846ae6f6e52d7229
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88547366"
---
# <a name="install-and-run-form-recognizer-containers-preview"></a>Installera och kör formulär igenkännings behållare (förhands granskning)

[!INCLUDE [Form Recognizer containers limit](includes/container-limit.md)]

Azure formulär tolken använder Machine Learning-teknik för att identifiera och extrahera nyckel/värde-par och tabeller från formulär. Den associerar värden och tabell poster med nyckel/värde-par och matar sedan ut strukturerade data som innehåller relationerna i original filen. 

För att minska komplexiteten och enkelt integrera en anpassad formulär igenkännings modell i automatiserings processen för arbets flödet eller ett annat program, kan du anropa modellen med hjälp av en enkel REST API. Endast fem formulär dokument (eller ett tomt formulär och två ifyllda formulär) behövs, så att du kan få resultat snabbt, exakt och skräddarsys efter ditt eget innehåll. Ingen kraftig manuell åtgärd eller omfattande data vetenskaps expertis krävs. Och det kräver inte data etiketter eller data anteckningar.

| Funktion | Funktioner |
|----------|----------|
| Formigenkänning | <li>Bearbetar PDF-, PNG-och JPG-filer<li>Tågen anpassade modeller med minst fem former av samma layout <li>Extraherar nyckel/värde-par och tabell information <li>Använder Identifiera text funktionen Azure Cognitive Services API för visuellt innehåll för att identifiera och extrahera utskriven text från bilder i formulär<li>Kräver inte anteckningar eller etiketter |

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/cognitive-services/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Innan du använder formulär igenkännings behållare måste du uppfylla följande krav:

| Obligatorisk | Syfte |
|----------|---------|
| Docker-motorn | Du behöver Docker-motorn installerad på en [värddator](#the-host-computer). Docker innehåller paket som konfigurerar Docker-miljön på [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) och [Linux](https://docs.docker.com/engine/installation/#supported-platforms). En introduktion till grunderna för Docker och containrar finns i [Docker-översikt](https://docs.docker.com/engine/docker-overview/).<br><br> Docker måste konfigureras för att tillåta att behållarna ansluter till och skicka fakturerings data till Azure. <br><br> I Windows måste Docker också konfigureras för att stödja Linux-behållare.<br><br> |
| Bekant med Docker | Du bör ha grundläggande kunskaper om Docker-koncept, till exempel register, databaser, behållare och behållar avbildningar samt kunskaper om grundläggande `docker` kommandon. |
| Azure CLI | Installera [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) på värden. |
| API för visuellt innehåll resurs | Om du vill bearbeta skannade dokument och avbildningar behöver du en Visuellt innehåll-resurs. Du kan komma åt Identifiera text-funktionen antingen som en Azure-resurs (REST API eller SDK) eller en *kognitiv-tjänster-Recognizer-text* - [behållare](../Computer-vision/computer-vision-how-to-install-containers.md#get-the-container-image-with-docker-pull). De vanliga fakturerings avgifterna gäller. <br><br>Skicka både API-nyckeln och slut punkterna för din Visuellt innehåll resurs (Azure-moln eller Cognitive Services-behållare). Använd den här API-nyckeln och slut punkten som **{COMPUTER_VISION_API_KEY}** och **{COMPUTER_VISION_ENDPOINT_URI}**.<br><br> Om du använder funktionen *kognitiv-Services-recognize-text* , se till att:<br><br>Din Visuellt innehåll nyckel för formulär tolkens behållare är den nyckel som anges i kommandot Visuellt innehåll `docker run` för filen *kognitiv-Services-recognize-text* .<br>Din fakturerings slut punkt är behållarens slut punkt (till exempel `http://localhost:5000` ). Om du använder både Visuellt innehåll container-och formulär igenkännings behållare på samma värd, kan de inte startas både med standard porten *5000*. |
| Formulär igenkännings resurs | Om du vill använda dessa behållare måste du ha:<br><br>En Azure **formulär igenkännings** resurs för att hämta den associerade API-nyckeln och slut punkts-URI: n. Båda värdena är tillgängliga på sidorna **igenkännings** översikt och nycklar för Azure Portal formulär och båda värdena krävs för att starta behållaren.<br><br>**{FORM_RECOGNIZER_API_KEY}**: en av de två tillgängliga resurs nycklarna på sidan nycklar<br><br>**{FORM_RECOGNIZER_ENDPOINT_URI}**: slut punkten enligt vad som anges på sidan Översikt |

> [!NOTE]
> Visuellt innehåll resurs namnet måste vara ett enda ord, utan bindestreck `-` eller andra specialtecken. Den här begränsningen är på plats för att säkerställa att formulär tolkare och Identifiera text behållar kompatibilitet.

## <a name="gathering-required-parameters"></a>Nödvändiga parametrar samlas in

Det finns tre primära parametrar för alla Cognitive Services behållare som krävs. Licens avtalet för slutanvändare (EULA) måste vara närvarande med värdet `accept` . Dessutom behövs både en slut punkts-URL och API-nyckel.

### <a name="endpoint-uri-computer_vision_endpoint_uri-and-form_recognizer_endpoint_uri"></a>Slut punkts-URI `{COMPUTER_VISION_ENDPOINT_URI}` och `{FORM_RECOGNIZER_ENDPOINT_URI}`

URI-värdet för **slut punkten** är tillgängligt på sidan Azure Portal *Översikt* för motsvarande kognitiva tjänst resurs. Gå till sidan *Översikt* , Hovra över slut punkten och en `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> ikon visas. Kopiera och använd vid behov.

![Samla in slut punkts-URI för senare användning](../containers/media/overview-endpoint-uri.png)

### <a name="keys-computer_vision_api_key-and-form_recognizer_api_key"></a>Nycklar `{COMPUTER_VISION_API_KEY}` och `{FORM_RECOGNIZER_API_KEY}`

Den här nyckeln används för att starta behållaren och är tillgänglig på sidan Azure Portals nycklar för motsvarande kognitiva tjänst resurser. Gå till sidan *nycklar* och klicka på `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> ikonen.

![Hämta en av de två nycklarna för senare användning](../containers/media/keys-copy-api-key.png)

> [!IMPORTANT]
> Dessa prenumerations nycklar används för att få åtkomst till ditt kognitiva tjänst-API. Dela inte dina nycklar. Lagra dem på ett säkert sätt, till exempel med hjälp av Azure Key Vault. Vi rekommenderar också att du återskapar nycklarna regelbundet. Endast en nyckel krävs för att göra ett API-anrop. När du återskapar den första nyckeln kan du använda den andra nyckeln för fortsatt åtkomst till tjänsten.

## <a name="the-host-computer"></a>Värddatorn

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Krav och rekommendationer för behållare

De minsta och rekommenderade processor kärnor och minne som ska allokeras för varje formulär igenkännings behållare beskrivs i följande tabell:

| Container | Minimum | Rekommenderas |
|-----------|---------|-------------|
| Formigenkänning | 2 kärnor, 4 GB minne | 4 kärnor, 8 GB minne |
| Identifiera text | 1 kärna, 8 GB minne | 2 kärnor, 8 GB minne |

* Varje kärna måste vara minst 2,6 gigahertz (GHz) eller snabbare.
* Core och minne motsvarar `--cpus` `--memory` inställningarna och som används som en del av `docker run` kommandot.

> [!Note]
> De lägsta och rekommenderade värdena baseras på Docker-gränser och *inte* värd datorns resurser.

## <a name="get-the-container-images-with-the-docker-pull-command"></a>Hämta behållar avbildningarna med kommandot Docker pull

Behållar avbildningar för både **formulär tolken** och **identifiera text** erbjudanden är tillgängliga i följande behållar register:

| Container | Fullständigt kvalificerat avbildnings namn |
|-----------|------------|
| Formigenkänning | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest` |
| Identifiera text | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest` |

Du behöver båda behållarna, Observera att text behållaren för **tolkning** är [detaljerad utanför den här artikeln.](../Computer-vision/computer-vision-how-to-install-containers.md#get-the-container-image-with-docker-pull)

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-form-recognizer-container"></a>Docker-hämtning för formulär igenkännings behållare

#### <a name="form-recognizer"></a>Formigenkänning

Använd följande kommando för att hämta formulär tolkens behållare:

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest
```
### <a name="docker-pull-for-the-recognize-text-container"></a>Docker-hämtning för Identifiera text container

#### <a name="recognize-text"></a>Identifiera text

Använd följande kommando för att hämta Identifiera text container:

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

## <a name="how-to-use-the-container"></a>Använda behållaren

När behållaren är på [värddatorn](#the-host-computer)använder du följande process för att arbeta med behållaren.

1. [Kör behållaren](#run-the-container-by-using-the-docker-run-command)med de fakturerings inställningar som krävs. Fler [exempel](form-recognizer-container-configuration.md#example-docker-run-commands) på `docker run` kommandot är tillgängliga.
1. [Fråga behållarens förutsägelse slut punkt](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-by-using-the-docker-run-command"></a>Kör behållaren med kommandot Docker Run

Använd kommandot [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) för att köra behållaren. Läs om hur du [samlar in obligatoriska parametrar](#gathering-required-parameters) för information om hur du hämtar `{COMPUTER_VISION_ENDPOINT_URI}` `{COMPUTER_VISION_API_KEY}` värdena, `{FORM_RECOGNIZER_ENDPOINT_URI}` och `{FORM_RECOGNIZER_API_KEY}` .

[Exempel](form-recognizer-container-configuration.md#example-docker-run-commands) på `docker run` kommandot är tillgängliga.

### <a name="form-recognizer"></a>Formigenkänning

> [!NOTE]
> Katalogerna som används `--mount` i de här exemplen är sökvägar i Windows-katalogen. Om du använder Linux eller macOS ändrar du parametern för din miljö. 

```bash
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

Det här kommandot:

* Kör en formulär tolks behållare från behållar avbildningen.
* Allokerar 2 processor kärnor och 8 GB minne.
* Exponerar TCP-port 5000 och allokerar en pseudo-TTY för behållaren.
* Tar automatiskt bort behållaren när den har avslut ATS. Behållar avbildningen är fortfarande tillgänglig på värddatorn.
* Monterar en/input och en/output-volym till behållaren.

[!INCLUDE [Running multiple containers on the same host H2](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

### <a name="run-separate-containers-as-separate-docker-run-commands"></a>Kör separata behållare som separata Docker-körnings kommandon

Använd följande två exempel på Docker CLI-kommandon för formulär igenkännings-och text igenkännings kombinationen som finns lokalt på samma värd:

Kör den första behållaren på port 5000. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY}
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

Kör den andra behållaren på port 5001.

```bash 
docker run --rm -it -p 5001:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
Eula=accept \
Billing={COMPUTER_VISION_ENDPOINT_URI} \
ApiKey={COMPUTER_VISION_API_KEY}
```
Varje efterföljande behållare bör finnas på en annan port. 

### <a name="run-separate-containers-with-docker-compose"></a>Kör separata behållare med Docker Compose

För formulär tolken och text igenkännings kombinationen som finns lokalt på samma värd, se följande exempel Docker Compose YAML-fil. Text tolken `{COMPUTER_VISION_API_KEY}` måste vara densamma för både `formrecognizer` `ocr` behållaren och. `{COMPUTER_VISION_ENDPOINT_URI}`Används endast i `ocr` behållaren, eftersom `formrecognizer` behållaren använder `ocr` namnet och porten. 

```docker
version: '3.3'
services:   
  ocr:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text"
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 8g
        reservations:
          cpus: '1'
          memory: 4g
    environment:
      eula: accept
      billing: "{COMPUTER_VISION_ENDPOINT_URI}"
      apikey: "{COMPUTER_VISION_API_KEY}"

  formrecognizer:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer"
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 8g
        reservations:
          cpus: '1'
          memory: 4g
    environment:
      eula: accept
      billing: "{FORM_RECOGNIZER_ENDPOINT_URI}"
      apikey: "{FORM_RECOGNIZER_API_KEY}"
      FormRecognizer__ComputerVisionApiKey: {COMPUTER_VISION_API_KEY}
      FormRecognizer__ComputerVisionEndpointUri: "http://ocr:5000"
      FormRecognizer__SyncProcessTaskCancelLimitInSecs: 75
    links:
      - ocr
    volumes:
      - type: bind
        source: c:\output
        target: /output
      - type: bind
        source: c:\input
        target: /input
    ports:
      - "5000:5000"
```

> [!IMPORTANT]
> `Eula`, `Billing` , Och `ApiKey` , samt `FormRecognizer:ComputerVisionApiKey` `FormRecognizer:ComputerVisionEndpointUri` alternativen och måste anges för att köra behållaren, annars startar inte behållaren. Mer information finns i [fakturering](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Köra frågor mot containerns förutsägelseslutpunkt

|Container|Slutpunkt|
|--|--|
|formulär-tolk|http://localhost:5000

### <a name="form-recognizer"></a>Formigenkänning

Behållaren innehåller WebSocket-baserade frågor om slut punkts-API: er som du kommer åt via [dokumentation för formulär igenkännings tjänstens SDK](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/).

Som standard använder formulär igenkännings-SDK onlinetjänster. Om du vill använda behållaren måste du ändra initierings metoden. Se exemplen nedan.

#### <a name="for-c"></a>För C #

Ändra från att använda det här Azure-moln initierings anropet:

```csharp
var config =
    FormRecognizerConfig.FromSubscription(
        "YourSubscriptionKey",
        "YourServiceRegion");
```
till det här anropet, som använder behållarens slut punkt:

```csharp
var config =
    FormRecognizerConfig.FromEndpoint(
        "ws://localhost:5000/formrecognizer/v1.0-preview/custom",
        "YourSubscriptionKey");
```

#### <a name="for-python"></a>För python

Ändra från att använda det här Azure-moln initierings anropet:

```python
formrecognizer_config =
    formrecognizersdk.FormRecognizerConfig(
        subscription=formrecognizer_key, region=service_region)
```

till det här anropet, som använder behållarens slut punkt:

```python
formrecognizer_config = 
    formrecognizersdk.FormRecognizerConfig(
        subscription=formrecognizer_key,
        endpoint="ws://localhost:5000/formrecognizer/v1.0-preview/custom"
```

### <a name="form-recognizer"></a>Formigenkänning

Behållaren innehåller API: er för REST-slutpunkt, som du hittar på sidan [API för formulär igenkänning](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeWithCustomModel) .


[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>Stoppa containern

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Felsökning

Om du kör behållaren med en utgående [montering](form-recognizer-container-configuration.md#mount-settings) och loggning aktive rad genererar behållaren loggfiler som är till hjälp vid fel sökning av problem som inträffar när du startar eller kör behållaren.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Fakturering

Formulär identifierarens behållare skickar fakturerings information till Azure med hjälp av en _formulär igenkännings_ resurs på ditt Azure-konto.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Mer information om dessa alternativ finns i [Configure containers](form-recognizer-container-configuration.md).

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Sammanfattning

I den här artikeln har du lärt dig begrepp och arbets flöde för att ladda ned, installera och köra formulär igenkännings behållare. Sammanfattningsvis:

* Formulär tolken innehåller en Linux-behållare för Docker.
* Behållar avbildningar laddas ned från det privata behållar registret i Azure.
* Behållar avbildningar körs i Docker.
* Du kan använda antingen REST API eller REST SDK för att anropa åtgärder i formulär igenkännings behållare genom att ange behållarens värd-URI.
* Du måste ange fakturerings information när du instansierar en behållare.

> [!IMPORTANT]
>  Cognitive Services behållare är inte licensierade att köras utan att vara anslutna till Azure för mätning. Kunderna behöver göra det möjligt för behållarna att kommunicera fakturerings information med mät tjänsten hela tiden. Cognitive Services behållare skickar inte kund information (till exempel den bild eller text som analyseras) till Microsoft.

## <a name="next-steps"></a>Nästa steg

* Granska [Konfigurera behållare](form-recognizer-container-configuration.md) för konfigurations inställningar.
* Använd fler [Cognitive Services behållare](../cognitive-services-container-support.md).
