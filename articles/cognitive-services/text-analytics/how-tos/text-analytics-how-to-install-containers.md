---
title: Installera och kör Docker-behållare för API för textanalys
titleSuffix: Azure Cognitive Services
description: Använd Docker-behållare för API för textanalys för att utföra naturlig språk bearbetning, till exempel sentiment analys, lokalt.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, cog-serv-seo-aug-2020
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: aahi
keywords: lokal, Docker, container, sentiment analys, naturlig språk bearbetning
ms.openlocfilehash: ac82781ed4a05fbbca7f5b16edb0b5349dfc400a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102432085"
---
# <a name="install-and-run-text-analytics-containers"></a>Installera och kör Textanalys-containrar

> [!NOTE]
> * Behållaren för Attitydanalys-och språk identifiering är nu allmänt tillgängliga. Extraherings behållaren för nyckel fraser är tillgänglig som en icke-grind offentlig för hands version.
> * Enhets länkning och NER är för närvarande inte tillgängliga som en behållare.
> * För att komma åt Textanalys för hälso container krävs ett [formulär för begäran](https://aka.ms/csgate). För närvarande debiteras du inte för dess användning.
> * Platserna för behållar avbildningar kan ha ändrats nyligen. Läs den här artikeln om du vill se den uppdaterade platsen för den här behållaren.

Med containrar kan du köra API:er för textanalys i din egen miljö och de är bra för dina specifika säkerhets- och datastyrningskrav. Textanalys behållare tillhandahåller avancerad naturlig språk bearbetning över rå text och innehåller tre huvud funktioner: sentiment analys, extrahering av nyckel fraser och språk identifiering. 

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/cognitive-services/) innan du börjar.

> [!IMPORTANT]
> Det kostnads fria kontot är begränsat till 5 000 transaktioner per månad och bara <a href="https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics" target="_blank">pris nivåerna</a> **kostnads fri** och **standard** är giltiga för behållare. Mer information om avgifter för transaktions begär Anden finns i [data begränsningar](../overview.md#data-limits).

## <a name="prerequisites"></a>Förutsättningar

Om du vill köra någon av de Textanalys behållarna måste du ha värd datorn och behållar miljöerna.

## <a name="preparation"></a>Förberedelse

Du måste uppfylla följande krav innan du använder Textanalys behållare:

|Obligatorisk|Syfte|
|--|--|
|Docker-motorn| Du behöver Docker-motorn installerad på en [värddator](#the-host-computer). Docker innehåller paket som konfigurerar Docker-miljön på [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) och [Linux](https://docs.docker.com/engine/installation/#supported-platforms). En introduktion till grunderna för Docker och containrar finns i [Docker-översikt](https://docs.docker.com/engine/docker-overview/).<br><br> Docker måste konfigureras för att tillåta att behållarna ansluter till och skicka fakturerings data till Azure. <br><br> **I Windows** måste Docker också konfigureras för att stödja Linux-behållare.<br><br>|
|Bekant med Docker | Du bör ha grundläggande kunskaper om Docker-koncept, t. ex. register, databaser, behållare och behållar avbildningar, samt kunskaper om grundläggande `docker` kommandon.| 
|Textanalys resurs |För att du ska kunna använda behållaren måste du ha:<br><br>En Azure [textanalys-resurs](../../cognitive-services-apis-create-account.md) med den kostnads fria (F0) eller standard [pris nivån](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/). Du måste hämta den associerade API-nyckeln och slut punkts-URI: n genom att gå till resursens **nyckel-och slut punkts** sida i Azure Portal. <br><br>**{Api_key}**: en av de två tillgängliga resurs nycklarna. <br><br>**{ENDPOINT_URI}**: slut punkten för din resurs. |

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>Värddatorn

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Krav och rekommendationer för behållare

I följande tabell beskrivs de lägsta och rekommenderade specifikationerna för Textanalys behållare. Minst 2 gigabyte (GB) minne krävs och varje processor kärna måste vara minst 2,6 gigahertz (GHz) eller snabbare. De tillåtna transaktionerna per avsnitt (TPS) visas också.

|  | Minimala specifikationer för värden | Specifikationer för rekommenderade värden | Minsta TPS | Maximalt TPS|
|---|---------|-------------|--|--|
| **Språk identifiering, extrahering av nyckel fraser**   | 1 kärna, 2 GB minne | 1 kärna, 4 GB minne |15 | 30|
| **Attitydanalys**   | 1 kärna, 2 GB minne | 4 kärnor, 8 GB minne |15 | 30|
| **Textanalys för hälso-1 dokument/begäran**   |  4 kärnor, 10 GB minne | 6 kärnor, 12 GB minne |15 | 30|
| **Textanalys för hälso tillstånd – 10 dokument/begäran**   |  6 kärnor, 16 GB minne | 8 kärnor, 20 GB minne |15 | 30|

PROCESSOR kärnor och minne motsvarar `--cpus` `--memory` inställningarna och som används som en del av `docker run` kommandot.

## <a name="get-the-container-image-with-docker-pull"></a>Hämta behållar avbildningen med `docker pull`

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]

Behållar avbildningar för Textanalys finns tillgängliga på Microsoft Container Registry.

# <a name="sentiment-analysis"></a>[Attitydanalys ](#tab/sentiment)

[!INCLUDE [docker-pull-sentiment-analysis-container](../includes/docker-pull-sentiment-analysis-container.md)]

# <a name="key-phrase-extraction-preview"></a>[Extrahering av diskussionsämne (för hands version)](#tab/keyphrase)

[!INCLUDE [docker-pull-key-phrase-extraction-container](../includes/docker-pull-key-phrase-extraction-container.md)]

# <a name="language-detection"></a>[Språkidentifiering](#tab/language)

[!INCLUDE [docker-pull-language-detection-container](../includes/docker-pull-language-detection-container.md)]

# <a name="text-analytics-for-health-preview"></a>[Textanalys för hälsa (för hands version)](#tab/healthcare)

[!INCLUDE [docker-pull-health-container](../includes/docker-pull-health-container.md)]

***

## <a name="how-to-use-the-container"></a>Använda behållaren

När behållaren är på [värddatorn](#the-host-computer)använder du följande process för att arbeta med behållaren.

1. [Kör behållaren](#run-the-container-with-docker-run)med de fakturerings inställningar som krävs.
1. [Fråga behållarens förutsägelse slut punkt](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Kör behållaren med `docker run`

Använd kommandot [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) för att köra behållarna. Behållaren fortsätter att köras tills du stoppar den.

> [!IMPORTANT]
> * Docker-kommandona i följande avsnitt använder omvänt snedstreck, `\` som ett fortsättnings streck. Ersätt eller ta bort detta baserat på värd operativ systemets krav. 
> * `Eula`Alternativen, `Billing` och `ApiKey` måste anges för att köra behållaren, annars startar inte behållaren.  Mer information finns i [fakturering](#billing).
> * Sentiment-analys och språk identifierings behållare är allmänt tillgängliga. Extraherings behållaren för nyckel fraser använder v2 för API: et och är en för hands version.

# <a name="sentiment-analysis"></a>[Attitydanalys](#tab/sentiment)

[!INCLUDE [docker-run-sentiment-analysis-container](../includes/docker-run-sentiment-analysis-container.md)]

# <a name="key-phrase-extraction-preview"></a>[Extrahering av diskussionsämne (för hands version)](#tab/keyphrase)

[!INCLUDE [docker-run-key-phrase-extraction-container](../includes/docker-run-key-phrase-extraction-container.md)]

# <a name="language-detection"></a>[Språkidentifiering](#tab/language)

[!INCLUDE [docker-run-language-detection-container](../includes/docker-run-language-detection-container.md)]

# <a name="text-analytics-for-health-preview"></a>[Textanalys för hälsa (för hands version)](#tab/healthcare)

[!INCLUDE [docker-run-health-container](../includes/docker-run-health-container.md)]

***

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Köra frågor mot containerns förutsägelseslutpunkt

Containern innehåller REST-baserade slutpunkts-API:er för frågeförutsägelse.

Använd värden, `http://localhost:5000`, för container-API:er.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Stoppa containern

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Felsökning

Om du kör behållaren med en utgående [montering](../text-analytics-resource-container-config.md#mount-settings) och loggning aktive rad genererar behållaren loggfiler som är till hjälp vid fel sökning av problem som inträffar när du startar eller kör behållaren.

[!INCLUDE [Cognitive Services FAQ note](../../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Fakturering

Textanalys behållare skickar fakturerings information till Azure med hjälp av en _textanalys_ resurs på ditt Azure-konto. 

[!INCLUDE [Container's Billing Settings](../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Mer information om dessa alternativ finns i [Configure containers](../text-analytics-resource-container-config.md).

## <a name="summary"></a>Sammanfattning

I den här artikeln har du lärt dig begrepp och arbets flöde för att ladda ned, installera och köra Textanalys behållare. Sammanfattningsvis:

* Textanalys tillhandahåller tre Linux-behållare för Docker, som kapslar in olika funktioner:
   * *Attitydanalys*
   * *Extrahering av diskussionsämne (för hands version)* 
   * *Språkidentifiering*
   * *Textanalys för hälsa (för hands version)*
* Behållar avbildningar hämtas från Microsoft Container Registry (MCR) eller för hands versions lagrings plats.
* Behållar avbildningar körs i Docker.
* Du kan använda antingen REST API eller SDK för att anropa åtgärder i Textanalys behållare genom att ange behållarens värd-URI.
* Du måste ange fakturerings information när du instansierar en behållare.

> [!IMPORTANT]
> Cognitive Services behållare är inte licensierade att köras utan att vara anslutna till Azure för mätning. Kunderna behöver göra det möjligt för behållarna att kommunicera fakturerings information med mät tjänsten hela tiden. Cognitive Services behållare skickar inte kund information (t. ex. text som analyseras) till Microsoft.

## <a name="next-steps"></a>Nästa steg

* Granska [Konfigurera behållare](../text-analytics-resource-container-config.md) för konfigurations inställningar
* Läs vanliga [frågor och svar (FAQ)](../text-analytics-resource-faq.md) för att lösa problem som rör funktioner.
