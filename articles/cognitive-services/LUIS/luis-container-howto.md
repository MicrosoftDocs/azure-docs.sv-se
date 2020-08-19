---
title: Docker-behållare – LUIS
titleSuffix: Azure Cognitive Services
description: LUIS-behållaren läser in din utbildade eller publicerade app i en Docker-behållare och ger åtkomst till frågans förutsägelser från behållarens API-slutpunkter.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: f5409fea1cdbbc35e9068fae6b3ba7fbc2a95580
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88547400"
---
# <a name="install-and-run-luis-docker-containers"></a>Installera och köra LUIS Docker-behållare

Behållaren Language Understanding (LUIS) läser in din utbildade eller publicerade Language Understanding modell. Som en [Luis-app](https://www.luis.ai)ger Docker-behållaren åtkomst till frågan förutsägelser från BEHÅLLAREns API-slutpunkter. Du kan samla in loggar från behållaren och överföra tillbaka dem till Language Understanding-appen för att förbättra appens förutsägelse noggrannhet.

Följande video visar hur du använder den här behållaren.

[![Demonstration av behållare för Cognitive Services](./media/luis-container-how-to/luis-containers-demo-video-still.png)](https://aka.ms/luis-container-demo)

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/cognitive-services/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Observera följande krav för att köra LUIS-behållaren:

|Obligatorisk|Syfte|
|--|--|
|Docker-motorn| Du behöver Docker-motorn installerad på en [värddator](#the-host-computer). Docker innehåller paket som konfigurerar Docker-miljön på [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) och [Linux](https://docs.docker.com/engine/installation/#supported-platforms). En introduktion till grunderna för Docker och containrar finns i [Docker-översikt](https://docs.docker.com/engine/docker-overview/).<br><br> Docker måste konfigureras för att tillåta att behållarna ansluter till och skicka fakturerings data till Azure. <br><br> **I Windows**måste Docker också konfigureras för att stödja Linux-behållare.<br><br>|
|Bekant med Docker | Du bör ha grundläggande kunskaper om Docker-koncept, t. ex. register, databaser, behållare och behållar avbildningar, samt kunskaper om grundläggande `docker` kommandon.|
|Azure `Cognitive Services` Resource och Luis [paketerad app](luis-how-to-start-new-app.md) -fil |För att du ska kunna använda behållaren måste du ha:<br><br>* En _Cognitive Services_ Azure-resurs och den associerade fakturerings nyckeln för fakturerings slut punktens URI. Båda värdena är tillgängliga på sidorna översikt och nycklar för resursen och krävs för att starta behållaren. <br>* En utbildad eller publicerad app paketeras som monterad inström till behållaren med dess associerade app-ID. Du kan hämta den paketerade filen från LUIS-portalen eller redigera-API: er. Om du får LUIS paketerad app från [redigerings-API: er](#authoring-apis-for-package-file), behöver du också din _redigerings nyckel_.<br><br>Dessa krav används för att skicka kommando rads argument till följande variabler:<br><br>**{AUTHORING_KEY}**: den här nyckeln används för att hämta den paketerade appen från Luis-tjänsten i molnet och ladda upp frågan loggar tillbaka till molnet. Formatet är `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.<br><br>**{APP_ID}**: detta ID används för att välja appen. Formatet är `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.<br><br>**{Api_key}**: den här nyckeln används för att starta behållaren. Du kan hitta slut punkts nyckeln på två platser. Det första är Azure Portal i _Cognitive Services_ resursens nyckel lista. Slut punkts nyckeln är också tillgänglig i LUIS-portalen på sidan nycklar och inställningar för slut punkt. Använd inte start nyckeln.<br><br>**{ENDPOINT_URI}**: slut punkten enligt vad som anges på översikts sidan.<br><br>[Redigerings nyckeln och slut punkts nyckeln](luis-limits.md#key-limits) har olika syfte. Använd dem inte interoförändrade. |

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="authoring-apis-for-package-file"></a>Redigera API: er för paketfil

Redigera API: er för paketerade appar:

* [API för publicerat paket](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip)
* [Icke-publicerat, endast intränat paket-API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip)

### <a name="the-host-computer"></a>Värddatorn

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Krav och rekommendationer för behållare

I tabellen nedan visas de lägsta och rekommenderade värdena för behållar värden. Dina krav kan ändras beroende på trafik volym.

|Container| Minimum | Rekommenderas | TPS<br>(Minimum, maximum)|
|-----------|---------|-------------|--|
|LUIS|1 kärna, 2 GB minne|1 kärna, 4 GB minne|20, 40|

* Varje kärna måste vara minst 2,6 gigahertz (GHz) eller snabbare.
* TPS-transaktioner per sekund

Core och minne motsvarar `--cpus` `--memory` inställningarna och som används som en del av `docker run` kommandot.

## <a name="get-the-container-image-with-docker-pull"></a>Hämta behållar avbildningen med `docker pull`

Använd [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) kommandot för att ladda ned en behållar avbildning från `mcr.microsoft.com/azure-cognitive-services/luis` lagrings platsen:

```
docker pull mcr.microsoft.com/azure-cognitive-services/luis:latest
```

En fullständig beskrivning av tillgängliga taggar, till exempel som `latest` används i föregående kommando, finns i [Luis](https://go.microsoft.com/fwlink/?linkid=2043204) på Docker Hub.

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Använda behållaren

När behållaren är på [värddatorn](#the-host-computer)använder du följande process för att arbeta med behållaren.

![Process för att använda Language Understanding-behållare (LUIS)](./media/luis-container-how-to/luis-flow-with-containers-diagram.jpg)

1. [Exportera paket](#export-packaged-app-from-luis) för container från Luis-portalen eller Luis-API: er.
1. Flytta paket filen till den nödvändiga **indatafilen** på [värddatorn](#the-host-computer). Byt inte namn på, ändra, Skriv över eller expandera LUIS-paketfilen.
1. [Kör behållaren](#run-the-container-with-docker-run)med nödvändiga inställningar för montering och fakturering av _indatakälla_ . Fler [exempel](luis-container-configuration.md#example-docker-run-commands) på `docker run` kommandot är tillgängliga.
1. [Fråga efter behållarens förutsägelse slut punkt](#query-the-containers-prediction-endpoint).
1. När du är färdig med behållaren importerar du [slut punkts loggarna](#import-the-endpoint-logs-for-active-learning) från utmatnings monteringen i Luis-portalen och [stoppar](#stop-the-container) behållaren.
1. Använd LUIS-portalens [aktiva utbildning](luis-how-to-review-endpoint-utterances.md) på sidan **Granska slut punkt yttranden** för att förbättra appen.

Appen som körs i behållaren kan inte ändras. För att kunna ändra appen i behållaren måste du ändra appen i LUIS-tjänsten med hjälp av [Luis](https://www.luis.ai) -portalen eller använda Luis- [redigerings-API: erna](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f). Träna och/eller publicera och hämta sedan ett nytt paket och kör behållaren igen.

LUIS-appen i behållaren kan inte exporteras tillbaka till LUIS-tjänsten. Endast fråga loggar kan överföras.

## <a name="export-packaged-app-from-luis"></a>Exportera paketerad app från LUIS

LUIS-containern kräver en utbildad eller publicerad LUIS-app för att besvara förutsägelse frågor för användar yttranden. För att hämta LUIS-appen använder du antingen det utbildade eller publicerade paket-API: et.

Standard platsen är `input` under katalogen i relation till den plats där du kör `docker run` kommandot.

Placera paket filen i en katalog och referera till den här katalogen som indata-montering när du kör Docker-behållaren.

### <a name="package-types"></a>Paket typer

Monterings katalogen för indatamängden kan innehålla **produktions**-, **mellanlagrings**-och **versions** modeller för appen samtidigt. Alla paket är monterade.

|Typ av paket|API för frågans slut punkt|Tillgänglighet för fråga|Paketets fil namns format|
|--|--|--|--|
|Versions|HÄMTA, PUBLICERA|Endast behållare|`{APP_ID}_v{APP_VERSION}.gz`|
|Mellanlagring|HÄMTA, PUBLICERA|Azure och container|`{APP_ID}_STAGING.gz`|
|Produktion|HÄMTA, PUBLICERA|Azure och container|`{APP_ID}_PRODUCTION.gz`|

> [!IMPORTANT]
> Byt inte namn på, ändra, Skriv över eller expandera LUIS-paketfilerna.

### <a name="packaging-prerequisites"></a>Förpacknings krav

Innan du packar ett LUIS-program måste du ha följande:

|Förpacknings krav|Information|
|--|--|
|Resurs instans för Azure _Cognitive Services_|Regioner som stöds är<br><br>USA, västra ( `westus` )<br>Västeuropa ( `westeurope` )<br>Östra Australien ( `australiaeast` )|
|Tränad eller publicerad LUIS-app|Utan [stödda beroenden][unsupported-dependencies]. |
|Åtkomst till [värd datorns](#the-host-computer)fil system |Värddatorn måste tillåta en [indata-montering](luis-container-configuration.md#mount-settings).|

### <a name="export-app-package-from-luis-portal"></a>Exportera appaket från LUIS-portalen

LUIS- [portalen](https://www.luis.ai) ger möjlighet att exportera den utbildade eller publicerade appens paket.

### <a name="export-published-apps-package-from-luis-portal"></a>Exportera den publicerade appens paket från LUIS-portalen

Den publicerade appens paket är tillgängligt från List sidan **Mina appar** .

1. Logga in på LUIS- [portalen](https://www.luis.ai).
1. Markera kryss rutan till vänster om appens namn i listan.
1. Välj **Exportera** objekt från kontext verktygsfältet ovanför listan.
1. Välj **export for container (gzip)**.
1. Välj miljö för **produktions plats** eller **mellanlagringsplats**.
1. Paketet laddas ned från webbläsaren.

![Exportera det publicerade paketet för behållaren från App-sidans export meny](./media/luis-container-how-to/export-published-package-for-container.png)

### <a name="export-versioned-apps-package-from-luis-portal"></a>Exportera den versions bara appens paket från LUIS-portalen

Den versions bara appens paket är tillgängligt på sidan **versions** lista.

1. Logga in på LUIS- [portalen](https://www.luis.ai).
1. Välj appen i listan.
1. Välj **Hantera** i appens navigerings fält.
1. Välj **versioner** i det vänstra navigerings fältet.
1. Markera kryss rutan till vänster om versions namnet i listan.
1. Välj **Exportera** objekt från kontext verktygsfältet ovanför listan.
1. Välj **export for container (gzip)**.
1. Paketet laddas ned från webbläsaren.

![Exportera det utbildade paketet för behållaren från sidan Exportera på versioner-menyn](./media/luis-container-how-to/export-trained-package-for-container.png)

### <a name="export-published-apps-package-from-api"></a>Exportera den publicerade appens paket från API

Använd följande REST API metod för att paketera en LUIS-app som du redan har [publicerat](luis-how-to-publish-app.md). Ersätt dina egna lämpliga värden för plats hållarna i API-anropet med hjälp av tabellen under HTTP-specifikationen.

```http
GET /luis/api/v2.0/package/{APP_ID}/slot/{SLOT_NAME}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Platshållare | Värde |
|-------------|-------|
| **{APP_ID}** | Program-ID för den publicerade LUIS-appen. |
| **{SLOT_NAME}** | Den publicerade LUIS-appens miljö. Använd något av följande värden:<br/>`PRODUCTION`<br/>`STAGING` |
| **{AUTHORING_KEY}** | Redigerings nyckeln för LUIS-kontot för den publicerade LUIS-appen.<br/>Du kan hämta din redigerings nyckel från sidan **användar inställningar** på Luis-portalen. |
| **{AZURE_REGION}** | Lämplig Azure-region:<br/><br/>`westus` – Västra USA<br/>`westeurope` – Västeuropa<br/>`australiaeast` – Australien, öst |

Information om hur du hämtar det publicerade paketet finns i [API-dokumentationen här][download-published-package]. Om det har hämtats är svaret en LUIS-paketfil. Spara filen på den lagrings plats som angetts för behållaren för indata-montering.

### <a name="export-versioned-apps-package-from-api"></a>Exportera det versions bara programmets paket från API

Använd följande REST API metod för att paketera ett LUIS-program som du redan har [tränat](luis-how-to-train.md). Ersätt dina egna lämpliga värden för plats hållarna i API-anropet med hjälp av tabellen under HTTP-specifikationen.

```http
GET /luis/api/v2.0/package/{APP_ID}/versions/{APP_VERSION}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Platshållare | Värde |
|-------------|-------|
| **{APP_ID}** | Program-ID för den tränade LUIS-appen. |
| **{APP_VERSION}** | Program versionen av den tränade LUIS-appen. |
| **{AUTHORING_KEY}** | Redigerings nyckeln för LUIS-kontot för den publicerade LUIS-appen.<br/>Du kan hämta din redigerings nyckel från sidan **användar inställningar** på Luis-portalen. |
| **{AZURE_REGION}** | Lämplig Azure-region:<br/><br/>`westus` – Västra USA<br/>`westeurope` – Västeuropa<br/>`australiaeast` – Australien, öst |

Information om hur du hämtar det versions bara paketet finns i [API-dokumentationen här][download-versioned-package]. Om det har hämtats är svaret en LUIS-paketfil. Spara filen på den lagrings plats som angetts för behållaren för indata-montering.

## <a name="run-the-container-with-docker-run"></a>Kör behållaren med `docker run`

Använd kommandot [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) för att köra behållaren. Läs om hur du [samlar in nödvändiga parametrar](#gathering-required-parameters) för information om hur du hämtar `{ENDPOINT_URI}` och- `{API_KEY}` värden.

[Exempel](luis-container-configuration.md#example-docker-run-commands) på `docker run` kommandot är tillgängliga.

```console
docker run --rm -it -p 5000:5000 ^
--memory 4g ^
--cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output\,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis ^
Eula=accept ^
Billing={ENDPOINT_URI} ^
ApiKey={API_KEY}
```

* I det här exemplet används katalogen utanför `C:` enheten för att undvika eventuella behörighets konflikter i Windows. Om du behöver använda en speciell katalog som indatalistan kan du behöva ge Docker-tjänstens behörighet.
* Ändra inte ordningen på argumenten om du inte är bekant med Docker-behållare.
* Om du använder ett annat operativ system använder du rätt konsol/Terminal, kommandosyntax för montering och linje fortsättnings text för systemet. Dessa exempel förutsätter en Windows-konsol med ett linje fortsättnings steg `^` . Eftersom behållaren är ett Linux-operativsystem använder mål-Mount en syntax för en mappvy i Linux-typ.

Det här kommandot:

* Kör en behållare från LUIS container image
* Läser in LUIS-appen från indataports montering vid *C:\input*, som finns på behållar värden
* Allokerar två processor kärnor och 4 GB minne
* Exponerar TCP-port 5000 och allokerar en pseudo-TTY för behållaren
* Sparar behållaren och LUIS loggar för att mata ut montering vid *C:\output*, som finns på behållar värden
* Tar automatiskt bort behållaren när den har avslut ATS. Behållar avbildningen är fortfarande tillgänglig på värddatorn.

Fler [exempel](luis-container-configuration.md#example-docker-run-commands) på `docker run` kommandot är tillgängliga.

> [!IMPORTANT]
> `Eula`Alternativen, `Billing` och `ApiKey` måste anges för att köra behållaren, annars startar inte behållaren.  Mer information finns i [fakturering](#billing).
> ApiKey-värdet är **nyckeln** från sidan **Azure-resurser** på Luis-portalen och finns också på sidan med Azures `Cognitive Services` resurs nycklar.

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="endpoint-apis-supported-by-the-container"></a>Slut punkts-API: er som stöds av behållaren

Både v2-och [v3](luis-migration-api-v3.md) -versioner av API: et är tillgängliga med behållaren.

## <a name="query-the-containers-prediction-endpoint"></a>Köra frågor mot containerns förutsägelseslutpunkt

Containern innehåller REST-baserade slutpunkts-API:er för frågeförutsägelse. Slut punkter för publicerade appar (mellanlagring eller produktion) har en _annan_ väg än slut punkter för versions program.

Använd värden, `http://localhost:5000`, för container-API:er.

# <a name="v3-prediction-endpoint"></a>[V3 förutsägelse slut punkt](#tab/v3)

|Pakettyp|HTTP-verb|Väg|Frågeparametrar|
|--|--|--|--|
|Publicerad|HÄMTA, PUBLICERA|`/luis/v3.0/apps/{appId}/slots/{slotName}/predict?`|`query={query}`<br>[`&verbose`]<br>[`&log`]<br>[`&show-all-intents`]|
|Versions|HÄMTA, PUBLICERA|`/luis/v3.0/apps/{appId}/versions/{versionId}/predict?`|`query={query}`<br>[`&verbose`]<br>[`&log`]<br>[`&show-all-intents`]|

Frågeparametrarna konfigurerar hur och vad som returneras i svaret för frågan:

|Frågeparameter|Typ|Syfte|
|--|--|--|
|`query`|sträng|Användarens uttryck.|
|`verbose`|boolean|Ett booleskt värde som anger om alla metadata ska returneras för de förväntade modellerna. Standardvärdet är false.|
|`log`|boolean|Loggar frågor som kan användas senare för [aktiv inlärning](luis-how-to-review-endpoint-utterances.md). Standardvärdet är false.|
|`show-all-intents`|boolean|Ett booleskt värde som anger om alla intentor eller den översta bedömnings metoden ska returneras. Standardvärdet är false.|

# <a name="v2-prediction-endpoint"></a>[V2-förutsägelse slut punkt](#tab/v2)

|Pakettyp|HTTP-verb|Väg|Frågeparametrar|
|--|--|--|--|
|Publicerad|[Hämta](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78), [publicera](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)|`/luis/v2.0/apps/{appId}?`|`q={q}`<br>`&staging`<br>[`&timezoneOffset`]<br>[`&verbose`]<br>[`&log`]<br>|
|Versions|HÄMTA, PUBLICERA|`/luis/v2.0/apps/{appId}/versions/{versionId}?`|`q={q}`<br>[`&timezoneOffset`]<br>[`&verbose`]<br>[`&log`]|

Frågeparametrarna konfigurerar hur och vad som returneras i svaret för frågan:

|Frågeparameter|Typ|Syfte|
|--|--|--|
|`q`|sträng|Användarens uttryck.|
|`timezoneOffset`|nummer|Med timezoneOffset kan du [ändra den tidszon](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) som används av den fördefinierade entiteten datetimeV2.|
|`verbose`|boolean|Returnerar alla avsikter och deras resultat när värdet är true. Standardvärdet är false, vilket endast returnerar Top-metoden.|
|`staging`|boolean|Returnerar fråga från tillfälliga miljö resultat om värdet är true. |
|`log`|boolean|Loggar frågor som kan användas senare för [aktiv inlärning](luis-how-to-review-endpoint-utterances.md). Standardvärdet är true.|

***

### <a name="query-the-luis-app"></a>Fråga LUIS-appen

Ett exempel på en spiral-kommando för att skicka frågor till behållaren för en publicerad app är:

# <a name="v3-prediction-endpoint"></a>[V3 förutsägelse slut punkt](#tab/v3)

Om du vill fråga en modell på en plats använder du följande API:

```bash
curl -G \
-d verbose=false \
-d log=true \
--data-urlencode "query=turn the lights on" \
"http://localhost:5000/luis/v3.0/apps/{APP_ID}/slots/production/predict"
```

Om du vill göra frågor till **mellanlagrings** miljön ersätter du `production` i vägen med `staging` :

`http://localhost:5000/luis/v3.0/apps/{APP_ID}/slots/staging/predict`

Om du vill fråga en versions modell använder du följande API:

```bash
curl -G \
-d verbose=false \
-d log=false \
--data-urlencode "query=turn the lights on" \
"http://localhost:5000/luis/v3.0/apps/{APP_ID}/versions/{APP_VERSION}/predict"
```

# <a name="v2-prediction-endpoint"></a>[V2-förutsägelse slut punkt](#tab/v2)

Om du vill fråga en modell på en plats använder du följande API:

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APP_ID}?q=turn%20on%20the%20lights&staging=false&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
Om du vill göra frågor till **mellanlagrings** miljön ändrar du värdet för **mellanlagringsplatsen** för frågesträngen till sant:

`staging=true`

Om du vill fråga en versions modell använder du följande API:

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APP_ID}/versions/{APP_VERSION}?q=turn%20on%20the%20lights&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
Versions namnet får innehålla högst 10 tecken och innehåller endast tecken som tillåts i en URL.

***

## <a name="import-the-endpoint-logs-for-active-learning"></a>Importera slut punkts loggarna för aktiv inlärning

Om en utgående montering har angetts för LUIS-behållaren, sparas i utdatakatalogen, där `{INSTANCE_ID}` är behållar-ID. Appens fråga logg innehåller frågan, svaret och tidsstämplar för varje förutsägelse fråga som skickats till behållaren LUIS.

Följande plats visar den kapslade katalog strukturen för behållarens loggfiler.
```
/output/luis/{INSTANCE_ID}/
```

Välj din app från LUIS-portalen och välj sedan **Importera slut punkts loggar** för att ladda upp loggarna.

![Importera behållarens loggfiler för aktiv inlärning](./media/luis-container-how-to/upload-endpoint-log-files.png)

När loggen har laddats upp [granskar du slut punkts](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-review-endpoint-utterances) yttranden i Luis-portalen.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Stoppa containern

Om du vill stänga av behållaren trycker du på **CTRL + C**i den kommando rads miljö där behållaren körs.

## <a name="troubleshooting"></a>Felsökning

Om du kör behållaren med en utgående [montering](luis-container-configuration.md#mount-settings) och loggning aktive rad genererar behållaren loggfiler som är till hjälp vid fel sökning av problem som inträffar när du startar eller kör behållaren.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Fakturering

LUIS-behållaren skickar fakturerings information till Azure med hjälp av en _Cognitive Services_ resurs på ditt Azure-konto.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Mer information om dessa alternativ finns i [Configure containers](luis-container-configuration.md).

<!--blogs/samples/video courses -->
[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Sammanfattning

I den här artikeln har du lärt dig begrepp och arbets flöde för att ladda ned, installera och köra Language Understanding-behållare (LUIS). Sammanfattningsvis:

* Language Understanding (LUIS) innehåller en Linux-behållare för Docker som tillhandahåller slut punkts frågan förutsägelser av yttranden.
* Behållar avbildningar hämtas från Microsoft Container Registry (MCR).
* Behållar avbildningar körs i Docker.
* Du kan använda REST API för att fråga behållarens slut punkter genom att ange behållarens värd-URI.
* Du måste ange fakturerings information när du instansierar en behållare.

> [!IMPORTANT]
> Cognitive Services behållare är inte licensierade att köras utan att vara anslutna till Azure för mätning. Kunderna behöver göra det möjligt för behållarna att kommunicera fakturerings information med mät tjänsten hela tiden. Cognitive Services behållare skickar inte kund information (till exempel den bild eller text som analyseras) till Microsoft.

## <a name="next-steps"></a>Nästa steg

* Granska [Konfigurera behållare](luis-container-configuration.md) för konfigurations inställningar.
* Se [begränsningar för Luis container](luis-container-limitations.md) för kända funktions begränsningar.
* Se [fel sökning](troubleshooting.md) för att lösa problem som rör Luis-funktioner.
* Använd fler [Cognitive Services behållare](../cognitive-services-container-support.md)

<!-- Links - external -->
[download-published-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip
[download-versioned-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip

[unsupported-dependencies]: luis-container-limitations.md#unsupported-dependencies-for-latest-container
