---
title: 'Installera och köra behållare för att använda API: t för avvikelse detektor'
titleSuffix: Azure Cognitive Services
description: Använd de avancerade algoritmerna för avvikelse detektorns API för att identifiera avvikelser i dina tids serie data.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: aahi
ms.openlocfilehash: ee742f09f3fcc1bd283efbc346fea6a040e53f48
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548539"
---
# <a name="install-and-run-anomaly-detector-containers-preview"></a>Installera och kör avvikelse detektor behållare (förhands granskning)

Avvikelseidentifiering har följande containerfunktioner:

| Funktion | Funktioner |
|--|--|
| Avvikelse detektor | <li> Identifierar avvikelser när de inträffar i real tid. <li> Identifierar avvikelser i alla data uppsättningar som en batch. <li> Härleder det förväntade normala intervallet av dina data. <li> Stöder känslighets justering för avvikelse identifiering så att den bättre passar dina data. |

Detaljerad information om API: er finns i:
* [Läs mer om API-tjänsten för avvikelse detektor](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/cognitive-services/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Du måste uppfylla följande krav innan du använder behållare för avvikelse detektor:

|Obligatorisk|Syfte|
|--|--|
|Docker-motorn| Du behöver Docker-motorn installerad på en [värddator](#the-host-computer). Docker innehåller paket som konfigurerar Docker-miljön på [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) och [Linux](https://docs.docker.com/engine/installation/#supported-platforms). En introduktion till grunderna för Docker och containrar finns i [Docker-översikt](https://docs.docker.com/engine/docker-overview/).<br><br> Docker måste konfigureras för att tillåta att behållarna ansluter till och skicka fakturerings data till Azure. <br><br> **I Windows**måste Docker också konfigureras för att stödja Linux-behållare.<br><br>|
|Bekant med Docker | Du bör ha grundläggande kunskaper om Docker-koncept, t. ex. register, databaser, behållare och behållar avbildningar, samt kunskaper om grundläggande `docker` kommandon.|
|Avvikelse detektor resurs |Du måste ha följande för att kunna använda dessa behållare:<br><br>En Azure- _avvikelses detektor_ resurs för att hämta tillhör ande API-nyckel och slut punkts-URI. Båda värdena är tillgängliga på Azure Portalens **avvikelse detektor** översikt och nycklar sidor och krävs för att starta behållaren.<br><br>**{Api_key}**: en av de två tillgängliga resurs nycklarna på sidan **nycklar**<br><br>**{ENDPOINT_URI}**: slut punkten enligt vad som anges på sidan **Översikt**|

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>Värddatorn

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

<!--* [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/). For instructions of deploying Anomaly Detector module in IoT Edge, see [How to deploy Anomaly Detector module in IoT Edge](how-to-deploy-anomaly-detector-module-in-iot-edge.md).-->

### <a name="container-requirements-and-recommendations"></a>Krav och rekommendationer för behållare

I följande tabell beskrivs de minsta och rekommenderade processor kärnor och minne som ska allokeras för avvikelse detektor behållare.

| FRÅGOR per sekund (frågor per sekund) | Minimum | Rekommenderas |
|-----------|---------|-------------|
| 10 FRÅGOR PER SEKUND | 4 kärnor, 1 GB minne | 8 kärnor, 2 GB minne |
| 20 FRÅGOR PER SEKUND | 8 kärnor, 2 GB minne | 16 kärnor 4 GB minne |

Varje kärna måste vara minst 2,6 gigahertz (GHz) eller snabbare.

Core och minne motsvarar `--cpus` `--memory` inställningarna och som används som en del av `docker run` kommandot.

## <a name="get-the-container-image-with-docker-pull"></a>Hämta behållar avbildningen med `docker pull`

Använd [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) kommandot för att ladda ned en behållar avbildning.

| Container | Lagringsplats |
|-----------|------------|
| kognitiva tjänster – avvikelser-detektor | `mcr.microsoft.com/azure-cognitive-services/anomaly-detector:latest` |

<!--
For a full description of available tags, such as `latest` used in the preceding command, see [anomaly-detector](https://go.microsoft.com/fwlink/?linkid=2083827&clcid=0x409) on Docker Hub.
-->
[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-anomaly-detector-container"></a>Docker-hämtning för avvikelse detektor behållaren

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/anomaly-detector:latest
```

## <a name="how-to-use-the-container"></a>Använda behållaren

När behållaren är på [värddatorn](#the-host-computer)använder du följande process för att arbeta med behållaren.

1. [Kör behållaren](#run-the-container-with-docker-run)med de fakturerings inställningar som krävs. Fler [exempel](anomaly-detector-container-configuration.md#example-docker-run-commands) på `docker run` kommandot är tillgängliga.
1. [Fråga behållarens förutsägelse slut punkt](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Kör behållaren med `docker run`

Använd kommandot [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) för att köra behållaren. Läs om hur du [samlar in nödvändiga parametrar](#gathering-required-parameters) för information om hur du hämtar `{ENDPOINT_URI}` och- `{API_KEY}` värden.

[Exempel](anomaly-detector-container-configuration.md#example-docker-run-commands) på `docker run` kommandot är tillgängliga.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/anomaly-detector:latest \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Det här kommandot:

* Kör en avvikelse detektor behållare från behållar avbildningen
* Allokerar en processor kärna och 4 GB minne
* Exponerar TCP-port 5000 och allokerar en pseudo-TTY för behållaren
* Tar automatiskt bort behållaren när den har avslut ATS. Behållar avbildningen är fortfarande tillgänglig på värddatorn.

> [!IMPORTANT]
> `Eula`Alternativen, `Billing` och `ApiKey` måste anges för att köra behållaren, annars startar inte behållaren.  Mer information finns i [fakturering](#billing).

### <a name="running-multiple-containers-on-the-same-host"></a>Köra flera behållare på samma värd

Om du tänker köra flera behållare med exponerade portar ska du se till att köra varje behållare med en annan port. Kör till exempel den första behållaren på port 5000 och den andra behållaren på port 5001.

Ersätt `<container-registry>` och `<container-name>` med värdena för de behållare som du använder. De behöver inte vara samma behållare. Du kan ha en avvikande detektor behållare och LUIS-behållaren som körs på värden tillsammans eller så kan du ha flera avvikande detektor behållare som kör.

Kör den första behållaren på port 5000.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Kör den andra behållaren på port 5001.


```bash
docker run --rm -it -p 5000:5001 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Varje efterföljande behållare bör finnas på en annan port.

## <a name="query-the-containers-prediction-endpoint"></a>Köra frågor mot containerns förutsägelseslutpunkt

Containern innehåller REST-baserade slutpunkts-API:er för frågeförutsägelse.

Använd värden, http://localhost:5000, för container-API:er.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Stoppa containern

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Felsökning

Om du kör behållaren med en utgående [montering](anomaly-detector-container-configuration.md#mount-settings) och loggning aktive rad genererar behållaren loggfiler som är till hjälp vid fel sökning av problem som inträffar när du startar eller kör behållaren.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Fakturering

Avvikelse detektor behållare skickar fakturerings information till Azure med hjälp av en _avvikelse_ resurs på ditt Azure-konto.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Mer information om dessa alternativ finns i [Configure containers](anomaly-detector-container-configuration.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Sammanfattning

I den här artikeln har du lärt dig begrepp och arbets flöde för att ladda ned, installera och köra avvikelser som behållare. Sammanfattningsvis:

* Avvikelse detektor tillhandahåller en Linux-behållare för Docker, inkapsling av avvikelse identifiering med batch vs streaming, förväntat intervalls-härledning och känslighets justering.
* Behållar avbildningar laddas ned från ett privat Azure Container Registry dedikerat för för hands version.
* Behållar avbildningar körs i Docker.
* Du kan använda antingen REST API eller SDK för att anropa åtgärder i avvikande detektor behållare genom att ange behållarens värd-URI.
* Du måste ange fakturerings information när du instansierar en behållare.

> [!IMPORTANT]
> Cognitive Services behållare är inte licensierade att köras utan att vara anslutna till Azure för mätning. Kunderna behöver göra det möjligt för behållarna att kommunicera fakturerings information med mät tjänsten hela tiden. Cognitive Services behållare skickar inte kund information (t. ex. tids serie data som analyseras) till Microsoft.

## <a name="next-steps"></a>Nästa steg

* Granska [Konfigurera behållare](anomaly-detector-container-configuration.md) för konfigurations inställningar
* [Distribuera en avvikelse detektor behållare till Azure Container Instances](how-to/deploy-anomaly-detection-on-container-instances.md)
* [Läs mer om API-tjänsten för avvikelse detektor](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
