---
title: Använda Docker Compose för att distribuera flera containrar
titleSuffix: Azure Cognitive Services
description: Lär dig hur du distribuerar flera Cognitive Services behållare. Den här artikeln visar hur du dirigerar flera Docker-behållar avbildningar med hjälp av Docker Compose.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/29/2020
ms.author: aahi
ms.openlocfilehash: cedcf8a3fcd656c4af0ca7493c598791d35d20d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "95996141"
---
# <a name="use-docker-compose-to-deploy-multiple-containers"></a>Använda Docker Compose för att distribuera flera containrar

Den här artikeln visar hur du distribuerar flera Azure Cognitive Services-behållare. Mer specifikt lär du dig hur du använder Docker Compose för att dirigera flera Docker-behållar avbildningar.

> [Docker Compose](https://docs.docker.com/compose/) är ett verktyg för att definiera och köra Docker-program med flera behållare. I skapa, använder du en YAML-fil för att konfigurera programmets tjänster. Sedan skapar du och startar alla tjänster från konfigurationen genom att köra ett enda kommando.

Det kan vara användbart att dirigera flera behållar avbildningar på en dator med en enda värddator. I den här artikeln ska vi samla samman behållarna för läsning och formulär igenkänning.

## <a name="prerequisites"></a>Förutsättningar

Den här proceduren kräver flera verktyg som måste installeras och köras lokalt:

* En Azure-prenumeration. Om du inte har ett konto kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/cognitive-services) innan du börjar.
* [Docker-motorn](https://www.docker.com/products/docker-engine). Bekräfta att Docker CLI fungerar i ett konsol fönster.
* En Azure-resurs med rätt pris nivå. Endast följande pris nivåer fungerar med den här behållaren:
  * **Visuellt innehåll** resurs med endast F0 eller standard pris nivå.
  * **Formulär igenkännings** resurs med endast F0 eller standard pris nivå.
  * **Cognitive Services** resurs med pris nivån S0.
* Om du använder en gated Preview-behållare måste du fylla i [formuläret för online-begäran](https://aka.ms/csgate/) för att använda den.

## <a name="docker-compose-file"></a>Docker Compose-fil

YAML-filen definierar alla tjänster som ska distribueras. Dessa tjänster är beroende av antingen en `DockerFile` eller en befintlig behållar avbildning. I det här fallet ska vi använda två förhands gransknings bilder. Kopiera och klistra in följande YAML-fil och spara den som *Docker-Compose. yaml*. Ange lämpliga **apiKey**-, **fakturerings**-och **EndpointUri** -värden i filen.

```yaml
version: '3.7'
services:
  forms:
    image: "mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout"
    environment:
       eula: accept
       billing: # < Your form recognizer billing URL >
       apikey: # < Your form recognizer API key >
       FormRecognizer__ComputerVisionApiKey: # < Your form recognizer API key >
       FormRecognizer__ComputerVisionEndpointUri: # < Your form recognizer URI >
    volumes:
       - type: bind
         source: E:\publicpreview\output
         target: /output
       - type: bind
         source: E:\publicpreview\input
         target: /input
    ports:
      - "5010:5000"

  ocr:
    image: "mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview"
    environment:
      eula: accept
      apikey: # < Your computer vision API key >
      billing: # < Your computer vision billing URL >
    ports:
      - "5021:5000"
```

> [!IMPORTANT]
> Skapa de kataloger på värddatorn som anges under noden **volymer** . Den här metoden krävs eftersom katalogerna måste finnas innan du försöker montera en avbildning med hjälp av volym bindningar.

## <a name="start-the-configured-docker-compose-services"></a>Starta den konfigurerade Docker Compose-tjänsterna

Med en Docker-fil kan du hantera alla faser i en definierad tjänst livs cykel: starta, stoppa och återskapa tjänster; Visa tjänst status; och logg strömning. Öppna ett kommando rads gränssnitt från projekt katalogen (där Docker-Compos. yaml-filen finns).

> [!NOTE]
> Undvik fel genom att kontrol lera att värddatorn delar enheter med Docker-motorn på rätt sätt. Om *E:\publicpreview* till exempel används som en katalog i filen *Docker-Compos. yaml* delar du enhet **E** med Docker.

Kör följande kommando från kommando rads gränssnittet för att starta (eller starta om) alla de tjänster som definierats i filen *Docker-Compos. yaml* :

```console
docker-compose up
```

Den första gången Docker kör kommandot **Docker-sammanställning** med hjälp av den här konfigurationen hämtar de avbildningar som kon figurer ATS under noden **tjänster** och laddar sedan ned och monterar dem:

```console
Pulling forms (mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout:)...
latest: Pulling from azure-cognitive-services/form-recognizer/layout
743f2d6c1f65: Pull complete
72befba99561: Pull complete
2a40b9192d02: Pull complete
c7715c9d5c33: Pull complete
f0b33959f1c4: Pull complete
b8ab86c6ab26: Pull complete
41940c21ed3c: Pull complete
e3d37dd258d4: Pull complete
cdb5eb761109: Pull complete
fd93b5f95865: Pull complete
ef41dcbc5857: Pull complete
4d05c86a4178: Pull complete
34e811d37201: Pull complete
Pulling ocr (mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview:)...
latest: Pulling from /azure-cognitive-services/vision/read:3.1-preview
f476d66f5408: Already exists
8882c27f669e: Already exists
d9af21273955: Already exists
f5029279ec12: Already exists
1a578849dcd1: Pull complete
45064b1ab0bf: Download complete
4bb846705268: Downloading [=========================================>         ]  187.1MB/222.8MB
c56511552241: Waiting
e91d2aa0f1ad: Downloading [==============================================>    ]  162.2MB/176.1MB
```

När avbildningarna har hämtats startas avbildnings tjänsterna:

```console
Starting docker_ocr_1   ... done
Starting docker_forms_1 ... doneAttaching to docker_ocr_1, docker_forms_1forms_1  | forms_1  | forms_1  | Notice: This Preview is made available to you on the condition that you agree to the Supplemental Terms of Use for Microsoft Azure Previews [https://go.microsoft.com/fwlink/?linkid=2018815], which supplement your agreement [https://go.microsoft.com/fwlink/?linkid=2018657] governing your use of Azure. If you do not have an existing agreement governing your use of Azure, you agree that your agreement governing use of Azure is the Microsoft Online Subscription Agreement [https://go.microsoft.com/fwlink/?linkid=2018755] (which incorporates the Online Services Terms [https://go.microsoft.com/fwlink/?linkid=2018760]). By using the Preview you agree to these terms.
forms_1  | 
forms_1  | 
forms_1  | Using '/input' for reading models and other read-only data.
forms_1  | Using '/output/forms/812d811d1bcc' for writing logs and other output data.
forms_1  | Logging to console.
forms_1  | Submitting metering to 'https://westus2.api.cognitive.microsoft.com/'.
forms_1  | WARNING: No access control enabled!
forms_1  | warn: Microsoft.AspNetCore.Server.Kestrel[0]
forms_1  |       Overriding address(es) 'http://+:80'. Binding to endpoints defined in UseKestrel() instead.
forms_1  | Hosting environment: Production
forms_1  | Content root path: /app/forms
forms_1  | Now listening on: http://0.0.0.0:5000
forms_1  | Application started. Press Ctrl+C to shut down.
ocr_1    | 
ocr_1    | 
ocr_1    | Notice: This Preview is made available to you on the condition that you agree to the Supplemental Terms of Use for Microsoft Azure Previews [https://go.microsoft.com/fwlink/?linkid=2018815], which supplement your agreement [https://go.microsoft.com/fwlink/?linkid=2018657] governing your use of Azure. If you do not have an existing agreement governing your use of Azure, you agree that your agreement governing use of Azure is the Microsoft Online Subscription Agreement [https://go.microsoft.com/fwlink/?linkid=2018755] (which incorporates the Online Services Terms [https://go.microsoft.com/fwlink/?linkid=2018760]). By using the Preview you agree to these terms.
ocr_1    |
ocr_1    | 
ocr_1    | Logging to console.
ocr_1    | Submitting metering to 'https://westcentralus.api.cognitive.microsoft.com/'.
ocr_1    | WARNING: No access control enabled!
ocr_1    | Hosting environment: Production
ocr_1    | Content root path: /
ocr_1    | Now listening on: http://0.0.0.0:5000
ocr_1    | Application started. Press Ctrl+C to shut down.
```

## <a name="verify-the-service-availability"></a>Kontrol lera tjänstens tillgänglighet

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

Här är några exempel på utdata:

```
IMAGE ID            REPOSITORY                                                                 TAG
2ce533f88e80        mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout          latest
4be104c126c5        mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview         latest
```

### <a name="test-containers"></a>Test behållare

Öppna en webbläsare på värddatorn och gå till **localhost** genom att använda den angivna porten från *Docker-Compos. yaml* -filen, till exempel http://localhost:5021/swagger/index.html . Du kan till exempel använda funktionen **testa IT** i API: et för att testa formulär tolkens slut punkt. Båda behållarna Swagger Pages bör vara tillgängliga och testable.

![Formulär igenkännings behållare](media/form-recognizer-swagger-page.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Cognitive Services behållare](../cognitive-services-container-support.md)
