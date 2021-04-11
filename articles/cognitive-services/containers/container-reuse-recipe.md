---
title: Recept för Docker-behållare
titleSuffix: Azure Cognitive Services
description: Lär dig hur du skapar, testar och lagrar behållare med några eller alla konfigurations inställningar för distribution och åter användning.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 2226e4e7c5df60a4a601408da04a1f2e67c4dc4f
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106067783"
---
# <a name="create-containers-for-reuse"></a>Skapa containrar för återanvändning

Använd de här behållar recepten för att skapa Cognitive Services behållare som kan återanvändas. Behållare kan skapas med vissa eller alla konfigurations inställningar så att de _inte_ behövs när behållaren startas.

När du har det här nya lager behållaren (med inställningar) och du har testat det lokalt, kan du lagra behållaren i ett behållar register. När behållaren startar, kommer den bara att behöva de inställningar som för närvarande inte är lagrade i behållaren. Den privata register behållaren innehåller konfigurations utrymme som du kan överföra inställningarna i.

## <a name="docker-run-syntax"></a>Docker-körning av syntax

Alla `docker run` exempel i det här dokumentet förutsätter en Windows-konsol med ett `^` fortsättnings streck. Tänk på följande när du använder:

* Ändra inte ordningen på argumenten om du inte är bekant med Docker-behållare.
* Om du använder ett annat operativ system än Windows, eller en annan konsol än Windows-konsol, använder du rätt konsol/Terminal, kommandosyntax för monteringar och linje fortsättnings text för din konsol och ditt system.  Eftersom Cognitive Services container är ett Linux-operativsystem använder mål monteringen en syntax för en mappvy i Linux-typ.
* `docker run` exempel använder katalogen från `c:` enheten för att undvika eventuella behörighets konflikter i Windows. Om du behöver använda en speciell katalog som indatalistan kan du behöva ge Docker-tjänstens behörighet.

## <a name="store-no-configuration-settings-in-image"></a>Spara inga konfigurations inställningar i avbildningen

Exempel `docker run` kommandona för varje tjänst lagrar inte några konfigurations inställningar i behållaren. När du startar behållaren från en-konsol eller register tjänst måste dessa konfigurations inställningar skickas. Den privata register behållaren innehåller konfigurations utrymme som du kan överföra inställningarna i.

## <a name="reuse-recipe-store-all-configuration-settings-with-container"></a>Återanvänd recept: lagra alla konfigurations inställningar med behållare

För att lagra alla konfigurations inställningar skapar du en `Dockerfile` med dessa inställningar.

Problem med den här metoden:

* Den nya behållaren har ett separat namn och en annan tagg än den ursprungliga behållaren.
* För att kunna ändra de här inställningarna måste du ändra värdena för Dockerfile, återskapa avbildningen och publicera dem igen i registret.
* Om någon får åtkomst till ditt behållar register eller din lokala värd kan de köra behållaren och använda Cognitive Services slut punkter.
* Om din kognitiva tjänst inte kräver inmatade monteringar lägger du inte till `COPY` raderna i din Dockerfile.

Skapa Dockerfile, hämta från den befintliga Cognitive Services-behållare som du vill använda och Använd Docker-kommandon i Dockerfile för att ange eller hämta information som container behöver.

Det här exemplet:

* Anger fakturerings slut punkten `{BILLING_ENDPOINT}` från värdens miljö nyckel med hjälp av `ENV` .
* Ställer in fakturerings-API-nyckeln `{ENDPOINT_KEY}` från värdens miljö nyckel med "kuvert".

### <a name="reuse-recipe-store-billing-settings-with-container"></a>Återanvänd recept: lagra fakturerings inställningar med behållare

I det här exemplet visas hur du skapar den Textanalys sentiment-containern från en Dockerfile.

```Dockerfile
FROM mcr.microsoft.com/azure-cognitive-services/sentiment:latest
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
```

Skapa och kör behållaren [lokalt](#how-to-use-container-on-your-local-host) eller från din [privata register behållare](#how-to-add-container-to-private-registry) efter behov.

### <a name="reuse-recipe-store-billing-and-mount-settings-with-container"></a>Återanvänd recept: lagra fakturerings-och monterings inställningar med behållare

Det här exemplet visar hur du använder Language Understanding och hur du sparar fakturering och modeller från Dockerfile.

* Kopierar modell filen Language Understanding (LUIS) från värdens fil system med hjälp av `COPY` .
* LUIS-containern har stöd för mer än en modell. Om alla modeller lagras i samma mapp behöver du en `COPY` instruktion.
* Kör Docker-filen från den relativa överordnade katalogen för modellens indatamängds katalog. I följande exempel kör du och- `docker build` `docker run` kommandona från den relativa överordnad för `/input` . Det första `/input` kommandot i `COPY` kommandot är värd datorns katalog. Den andra `/input` är behållarens katalog.

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
COPY /input /input
```

Skapa och kör behållaren [lokalt](#how-to-use-container-on-your-local-host) eller från din [privata register behållare](#how-to-add-container-to-private-registry) efter behov.

## <a name="how-to-use-container-on-your-local-host"></a>Så här använder du behållare på den lokala värden

Om du vill bygga Docker-filen ersätter du `<your-image-name>` med det nya namnet på avbildningen och använder sedan:

```console
docker build -t <your-image-name> .
```

Köra avbildningen och ta bort den när behållaren stoppas ( `--rm` ):

```console
docker run --rm <your-image-name>
```

## <a name="how-to-add-container-to-private-registry"></a>Lägga till behållare i privata register

Följ dessa steg om du vill använda Dockerfile och placera den nya avbildningen i ditt privata behållar register.  

1. Skapa en `Dockerfile` med texten från Återanvänd recept. En `Dockerfile` saknar tillägg.

1. Ersätt alla värden i vinkelparenteser med dina egna värden.

1. Bygg filen i en avbildning på kommando raden eller terminalen med hjälp av följande kommando. Ersätt värdena i vinkelparenteser, `<>` med ditt egna behållar namn och tagg.  

    Alternativet tag, `-t` , är ett sätt att lägga till information om vad du har ändrat för behållaren. Ett behållar namn på anger till exempel `modified-LUIS` att den ursprungliga behållaren har lager. Ett taggnamn `with-billing-and-model` visar hur language Understanding (Luis)-behållaren har ändrats.

    ```Bash
    docker build -t <your-new-container-name>:<your-new-tag-name> .
    ```

1. Logga in på Azure CLI från en-konsol. Det här kommandot öppnar en webbläsare och kräver autentisering. När du har autentiserat kan du stänga webbläsaren och fortsätta att arbeta i-konsolen.

    ```azurecli
    az login
    ```

1. Logga in i ditt privata register med Azure CLI från en-konsol.

    Ersätt värdena i vinkelparenteser, `<my-registry>` med ditt eget register namn.  

    ```azurecli
    az acr login --name <my-registry>
    ```

    Du kan också logga in med Docker-inloggning om du har tilldelats ett huvud namn för tjänsten.

    ```Bash
    docker login <my-registry>.azurecr.io
    ```

1. Tagga behållaren med den privata register platsen. Ersätt värdena i vinkelparenteser, `<my-registry>` med ditt eget register namn. 

    ```Bash
    docker tag <your-new-container-name>:<your-new-tag-name> <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

    Om du inte använder ett taggnamn är det `latest` underförstådda.

1. Skicka den nya avbildningen till ditt privata behållar register. När du visar ditt privata behållar register blir behållar namnet som används i följande CLI-kommando namnet på lagrings platsen.

    ```Bash
    docker push <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa och Använd Azure Container instance](azure-container-instance-recipe.md)

<!--
## Store input and output configuration settings

Bake in input params only

FROM containerpreview.azurecr.io/microsoft/cognitive-services-luis:<tag>
COPY luisModel1 /input/
COPY luisModel2 /input/

## Store all configuration settings

If you are a single manager of the container, you may want to store all settings in the container. The new, resulting container will not need any variables passed in to run. 

Issues with this approach:

* In order to change these settings, you will have to change the values of the Dockerfile and rebuild the file. 
* If someone gets access to your container registry or your local host, they can run the container and use the Cognitive Services endpoints. 

The following _partial_ Dockerfile shows how to statically set the values for billing and model. This example uses the 

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing=<billing value>
ENV apikey=<apikey value>
COPY luisModel1 /input/
COPY luisModel2 /input/
```

->