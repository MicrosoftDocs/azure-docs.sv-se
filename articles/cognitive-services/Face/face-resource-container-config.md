---
title: Konfigurera behållare – ansikte
titleSuffix: Azure Cognitive Services
description: Runtime-miljön för ansikts behållare konfigureras med hjälp av `docker run` kommando argumenten. Det finns både obligatoriska och valfria inställningar.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 5287fe6df4a2f0cb728bf0e705a4ce989ca38487
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105934044"
---
# <a name="configure-face-docker-containers"></a>Konfigurera ansikts Docker-behållare

Runtime-miljön för **ansikts** behållare konfigureras med hjälp av `docker run` kommando argumenten. Den här behållaren har flera inställningar som krävs, tillsammans med några valfria inställningar. Det finns flera [exempel](#example-docker-run-commands) på kommandot. De behållar-/regionsspecifika inställningarna är fakturerings inställningarna. 

## <a name="configuration-settings"></a>Konfigurationsinställningar

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting)Inställningarna, [`Billing`](#billing-configuration-setting) och [`Eula`](#eula-setting) används tillsammans och du måste ange giltiga värden för alla dessa tre. i annat fall startar inte behållaren. Mer information om hur du använder dessa konfigurations inställningar för att instansiera en behållare finns i [fakturering](face-how-to-install-containers.md#billing).

## <a name="apikey-configuration-setting"></a>Konfigurations inställning för ApiKey

`ApiKey`Inställningen anger den Azure-resurs nyckel som används för att spåra fakturerings information för behållaren. Du måste ange ett värde för ApiKey och värdet måste vara en giltig nyckel för den _Cognitive Services_ resurs som angetts för [`Billing`](#billing-configuration-setting) konfigurations inställningen.

Du hittar den här inställningen på följande plats:

* Azure Portal: **Cognitive Services** resurs hantering under **nycklar**

## <a name="applicationinsights-setting"></a>ApplicationInsights-inställning

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Konfigurations inställning för fakturering

`Billing`Inställningen anger slut punkts-URI för den _Cognitive Services_ resursen på Azure som används för att mäta fakturerings information för behållaren. Du måste ange ett värde för den här konfigurations inställningen och värdet måste vara en giltig slut punkts-URI för en _Cognitive Services_ -resurs på Azure. Behållar rapporteringen visar var 10 till 15: e minut.

Du hittar den här inställningen på följande plats:

* Azure Portal: **Cognitive Services** översikt, etiketterad `Endpoint`

Kom ihåg att lägga till _ansikts_ dirigering till slut punkts-URI: n som visas i exemplet. 

|Obligatorisk| Name | Datatyp | Beskrivning |
|--|------|-----------|-------------|
|Ja| `Billing` | Sträng | URI för fakturerings slut punkt. Mer information om hur du skaffar fakturerings-URI: n finns i [samla in obligatoriska parametrar](face-how-to-install-containers.md#gathering-required-parameters). Mer information och en fullständig lista över regionala slut punkter finns i [anpassade under domän namn för Cognitive Services](../cognitive-services-custom-subdomains.md). |

<!-- specific to face only -->

## <a name="cloudai-configuration-settings"></a>Konfigurations inställningar för CloudAI

Konfigurations inställningarna i `CloudAI` avsnittet innehåller enhetsspecifika alternativ som är unika för din behållare. Följande inställningar och objekt stöds för behållaren FACET i `CloudAI` avsnittet

| Name | Datatyp | Beskrivning |
|------|-----------|-------------|
| `Storage` | Objekt | Det lagrings scenario som används av ansikts behållaren. Mer information om lagrings scenarier och tillhör ande inställningar för `Storage` objektet finns i [Inställningar för lagrings scenario](#storage-scenario-settings) |

### <a name="storage-scenario-settings"></a>Inställningar för lagrings scenario

Behållaren ansikte lagrar BLOB-, cache-, metadata-och Queue-data, beroende på vad som lagras. Till exempel lagras utbildnings index och resultat för en **LargePersonGroup** som BLOB-data. Ansikts behållaren innehåller två olika lagrings scenarier när du interagerar med och lagrar dessa typer av data:

* Minne  
  Alla fyra typer av data lagras i minnet. De är inte distribuerade eller är inte permanenta. Om ansikts behållaren har stoppats eller tagits bort, förstörs alla data i lagrings utrymmet för den behållaren.  
  Det här är standard lagrings scenariot för behållaren FACET.
* Azure  
  Behållaren ansikts använder Azure Storage och Azure Cosmos DB för att distribuera dessa fyra typer av data över beständig lagring. Blob-och Queue-data hanteras av Azure Storage. Metadata och cachelagrade data hanteras av Azure Cosmos DB. Om ansikts behållaren har stoppats eller tagits bort, förblir alla data i lagringen för den behållaren lagrade i Azure Storage och Azure Cosmos DB.  
  De resurser som används av Azure Storage-scenariot har följande ytterligare krav
  * Den Azure Storage resursen måste använda StorageV2-kontots typ
  * Den Azure Cosmos DB resursen måste använda Azure Cosmos DBs API för MongoDB

Lagrings scenarierna och de associerade konfigurations inställningarna hanteras av `Storage` objektet under `CloudAI` konfigurations avsnittet. Följande konfigurations inställningar är tillgängliga i `Storage` objektet:

| Name | Datatyp | Beskrivning |
|------|-----------|-------------|
| `StorageScenario` | Sträng | Det lagrings scenario som stöds av behållaren. Följande värden är tillgängliga<br/>`Memory` – Standardvärde. Container använder icke-beständig, icke-distribuerad lagring och minnes intern lagring för en nod temporär användning. Om behållaren har stoppats eller tagits bort förstörs lagringen för den behållaren.<br/>`Azure` -Container använder Azure-resurser för lagring. Om behållaren har stoppats eller tagits bort sparas lagringen för den behållaren.|
| `ConnectionStringOfAzureStorage` | Sträng | Anslutnings strängen för den Azure Storage resurs som används av behållaren.<br/>Den här inställningen gäller endast om `Azure` har angetts för `StorageScenario` konfigurations inställningen. |
| `ConnectionStringOfCosmosMongo` | Sträng | MongoDB anslutnings sträng för den Azure Cosmos DB resurs som används av behållaren.<br/>Den här inställningen gäller endast om `Azure` har angetts för `StorageScenario` konfigurations inställningen. |

Följande kommando anger till exempel scenariot för Azure Storage och ger exempel på anslutnings strängar för Azure Storage och Cosmos DB resurser som används för att lagra data för ansikts behållaren.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

Lagrings scenariot hanteras separat från indata monteringar och utgående monteringar. Du kan ange en kombination av dessa funktioner för en enda behållare. Följande kommando definierar t. ex. en Docker-bindning montera till `D:\Output` mappen på värddatorn som utmatnings monteringen och instansierar sedan en behållare från ansikts behållar avbildningen och sparar loggfilerna i JSON-format till utmatnings monteringen. Kommandot anger även scenariot för Azure Storage och ger exempel på anslutnings strängar för Azure Storage och Cosmos DB resurser som används för att lagra data för ansikts behållaren.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 --mount type=bind,source=D:\Output,destination=/output containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 Logging:Disk:Format=json CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

## <a name="eula-setting"></a>Licens avtals inställning

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluent-inställningar

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Inställningar för autentiseringsuppgifter för http-proxy

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Loggningsinställningar
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Monterings inställningar

Använd bind-monteringar för att läsa och skriva data till och från behållaren. Du kan ange en inmatnings montering eller utmatnings montering genom `--mount` att ange alternativet i kommandot [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) .

Ansikts behållare använder inte indata eller utdata monteras för att lagra utbildning eller tjänst data. 

Den exakta syntaxen för värd monterings platsen varierar beroende på värd operativ systemet. Dessutom kanske [värd datorns](face-how-to-install-containers.md#the-host-computer)monterings plats inte är tillgänglig på grund av en konflikt mellan behörigheter som används av Docker-tjänstkontot och värd monterings platsens behörigheter. 

|Valfritt| Name | Datatyp | Beskrivning |
|-------|------|-----------|-------------|
|Inte tillåten| `Input` | Sträng | Ansikts behållare använder inte detta.|
|Valfritt| `Output` | Sträng | Målet för utmatnings monteringen. Standardvärdet är `/output`. Detta är platsen för loggarna. Detta inkluderar behållar loggar. <br><br>Exempel:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Exempel på Docker-körnings kommandon 

I följande exempel används konfigurations inställningarna för att illustrera hur du skriver och använder `docker run` kommandon.  När den körs fortsätter behållaren att köras tills du [stoppar](face-how-to-install-containers.md#stop-the-container) den.

* **Rad fortsättnings bokstav**: Docker-kommandona i följande avsnitt använder omvänt snedstreck, `\` som ett fortsättnings steg. Ersätt eller ta bort detta baserat på värd operativ systemets krav. 
* **Argument ordning**: Ändra inte ordningen på argumenten om du inte är bekant med Docker-behållare.

Ersätt {_argument_name_} med dina egna värden:

| Platshållare | Värde | Format eller exempel |
|-------------|-------|---|
| **{API_KEY}** | Resursens slut punkts nyckel `Face` på sidan med Azure- `Face` nycklar. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | Värdet för fakturerings slut punkten är tillgängligt på sidan Azure- `Face` Översikt.| Se [samla in obligatoriska parametrar](face-how-to-install-containers.md#gathering-required-parameters) för explicita exempel. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> `Eula`Alternativen, `Billing` och `ApiKey` måste anges för att köra behållaren, annars startar inte behållaren.  Mer information finns i [fakturering](face-how-to-install-containers.md#billing).
> ApiKey-värdet är **nyckeln** på sidan med Azures `Cognitive Services` resurs nycklar. 

## <a name="face-container-docker-examples"></a>Docker-exempel för ansikts behållare

Följande Docker-exempel är för ansikts behållaren. 

### <a name="basic-example"></a>Basic-exempel 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>Loggnings exempel 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={ENDPOINT_URI} ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Nästa steg

* Granska [hur du installerar och kör behållare](face-how-to-install-containers.md)
