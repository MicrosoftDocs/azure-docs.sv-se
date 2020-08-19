---
title: Installera och kör behållare – ansikte
titleSuffix: Azure Cognitive Services
description: Den här artikeln visar hur du hämtar, installerar och kör behållare för ansikte i den här själv studie kursen.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: aahi
ms.openlocfilehash: 766af570c959ff2c49256058dfbfffdd1021295f
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548471"
---
# <a name="install-and-run-face-containers-preview"></a>Installera och kör ansikts behållare (förhands granskning)

> [!IMPORTANT]
> Gränsen för antal användare av Ansiktsigenkänningscontainern har nåtts. Vi tar för närvarade inte emot fler ansökningar till Ansiktsigenkänningscontainern.

Azure Cognitive Services FACET tillhandahåller en standardiserad Linux-behållare för Docker som identifierar mänskliga ansikten i bilder. Den identifierar också attribut, bland annat ansikts landmärken, till exempel näsaer och ögon, kön, ålder och andra maskin förväntade ansikts funktioner. Förutom identifiering kan ansikte kontrol lera om två ansikten i samma bild eller olika bilder är desamma genom att använda en förtroende poäng. Ansikte kan också jämföra ansikten mot en databas för att se om ett liknande eller identiskt ansikte redan finns. Den kan också organisera liknande ansikten i grupper med hjälp av delade visuella egenskaper.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/cognitive-services/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Du måste uppfylla följande krav innan du använder behållare för ansikts tjänster.

|Obligatorisk|Syfte|
|--|--|
|Docker-motorn| Docker-motorn måste vara installerad på en [värddator](#the-host-computer). Docker innehåller paket som konfigurerar Docker-miljön på [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) och [Linux](https://docs.docker.com/engine/installation/#supported-platforms). En introduktion till grunderna för Docker och containrar finns i [Docker-översikt](https://docs.docker.com/engine/docker-overview/).<br><br> Docker måste konfigureras för att tillåta att behållarna ansluter till och skicka fakturerings data till Azure. <br><br> I Windows måste Docker också konfigureras för att stödja Linux-behållare.<br><br>|
|Bekant med Docker | Du behöver grundläggande förståelse för Docker-koncept, till exempel register, databaser, behållare och behållar avbildningar. Du behöver också kunskap om grundläggande `docker` kommandon.| 
|Ansikts resurs |Om du vill använda behållaren måste du ha:<br><br>En Azure- **ansikts** resurs och tillhör ande API-nyckel och slut punkts-URI. Båda värdena är tillgängliga på sidorna **Översikt** och **nycklar** för resursen. De måste starta behållaren.<br><br>**{Api_key}**: en av de två tillgängliga resurs nycklarna på sidan **nycklar**<br><br>**{ENDPOINT_URI}**: slut punkten enligt vad som anges på sidan **Översikt**

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>Värddatorn

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Krav och rekommendationer för behållare

I följande tabell beskrivs de minsta och rekommenderade processor kärnor och minne som ska allokeras för varje behållare för ansikts tjänster.

| Container | Minimum | Rekommenderas | Transaktioner per sekund<br>(Minimum, maximum)|
|-----------|---------|-------------|--|
|Ansikte | 1 kärna, 2 GB minne | 1 kärna, 4 GB minne |10, 20|

* Varje kärna måste vara minst 2,6 GHz eller snabbare.
* Transaktioner per sekund (TPS).

Core och minne motsvarar `--cpus` `--memory` inställningarna och som används som en del av `docker run` kommandot.

## <a name="get-the-container-image-with-docker-pull"></a>Hämta behållar avbildningen med Docker pull

Behållar avbildningar för Face service är tillgängliga. 

| Container | Lagringsplats |
|-----------|------------|
| Ansikte | `containerpreview.azurecr.io/microsoft/cognitive-services-face:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-face-container"></a>Docker-hämtning för ansikts behållaren

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

## <a name="use-the-container"></a>Använd behållaren

När behållaren är på [värddatorn](#the-host-computer)använder du följande process för att arbeta med behållaren.

1. [Kör behållaren](#run-the-container-with-docker-run) med de fakturerings inställningar som krävs. Fler [exempel](./face-resource-container-config.md#example-docker-run-commands) på `docker run` kommandot är tillgängliga. 
1. [Fråga behållarens förutsägelse slut punkt](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Kör behållaren med Docker-körning

Använd kommandot [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) för att köra behållaren. Läs om hur du [samlar in nödvändiga parametrar](#gathering-required-parameters) för information om hur du hämtar `{ENDPOINT_URI}` och- `{API_KEY}` värden.

[Exempel](face-resource-container-config.md#example-docker-run-commands) på `docker run` kommandot är tillgängliga.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-face \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Det här kommandot:

* Kör en ansikts behållare från behållar avbildningen.
* Allokerar en processor kärna och 4 GB minne.
* Exponerar TCP-port 5000 och allokerar en pseudo-TTY för behållaren.
* Tar automatiskt bort behållaren när den har avslut ATS. Behållar avbildningen är fortfarande tillgänglig på värddatorn. 

Fler [exempel](./face-resource-container-config.md#example-docker-run-commands) på `docker run` kommandot är tillgängliga. 

> [!IMPORTANT]
> `Eula`Alternativen, `Billing` och `ApiKey` måste anges för att köra behållaren eller så startar inte behållaren. Mer information finns i [fakturering](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>Köra frågor mot containerns förutsägelseslutpunkt

Containern innehåller REST-baserade slutpunkts-API:er för frågeförutsägelse. 

Använd värden, `http://localhost:5000`, för container-API:er.


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Stoppa containern

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Felsökning

Om du kör behållaren med en utgående [montering](./face-resource-container-config.md#mount-settings) och loggning har Aktiver ATS genererar behållaren loggfiler som är till hjälp vid fel sökning av problem som inträffar när du startar eller kör behållaren.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Fakturering

Face service-behållare skickar fakturerings information till Azure med hjälp av en ansikts resurs på ditt Azure-konto. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Mer information om dessa alternativ finns i [Configure containers](./face-resource-container-config.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Sammanfattning

I den här artikeln har du lärt dig begrepp och arbets flöde för att ladda ned, installera och köra Face service-behållare. Sammanfattningsvis:

* Behållar avbildningar hämtas från Azure Container Registry.
* Behållar avbildningar körs i Docker.
* Du kan använda antingen REST API eller SDK för att anropa åtgärder i ansikts tjänste behållare genom att ange behållarens värd-URI.
* Du måste ange fakturerings information när du instansierar en behållare.

> [!IMPORTANT]
> Cognitive Services behållare är inte licensierade att köras utan att vara anslutna till Azure för mätning. Kunderna måste göra det möjligt för behållarna att kommunicera fakturerings information med mät tjänsten hela tiden. Cognitive Services behållare skickar inte kund information, till exempel den bild eller text som analyseras, till Microsoft.

## <a name="next-steps"></a>Nästa steg

* Konfigurations inställningar finns i [Konfigurera behållare](face-resource-container-config.md).
* Mer information om hur du identifierar och identifierar ansikten finns i [Översikt över ansikts](Overview.md).
* Information om vilka metoder som stöds av behållaren finns i [ansikts-API](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).
* Om du vill använda fler Cognitive Services behållare, se [Cognitive Services behållare](../cognitive-services-container-support.md).
