---
title: Taggar för Cognitive Services-containeravbildningar
titleSuffix: Azure Cognitive Services
description: En omfattande lista över alla avbildnings taggar för kognitiva tjänst behållare.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 11/17/2020
ms.author: aahi
ms.openlocfilehash: c3023c32f2a31235f5a733639a548c190197b413
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99981127"
---
# <a name="azure-cognitive-services-container-image-tags-and-release-notes"></a>Bild taggar för Azure Cognitive Services container och viktig information

Azure Cognitive Services erbjuder många behållar avbildningar. Behållar registren och motsvarande databaser varierar mellan behållar avbildningar. Varje behållar avbildnings namn innehåller flera taggar. En behållar avbildnings tag är en mekanism för version av behållar avbildningen. Den här artikeln är avsedd att användas som en omfattande referens för att visa alla Cognitive Services behållar avbildningar och de tillgängliga taggarna.

> [!TIP]
> När du använder ska du vara noga med att tänka på [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) SKIFT läge för behållar registret, databasen, behållar avbildningens namn och motsvarande tagg – eftersom de är **SKIFT läges känsliga**.

## <a name="anomaly-detector"></a>Avvikelseidentifiering

Behållar avbildningen för [avvikelse detektor][ad-containers] finns i `mcr.microsoft.com` behållar registrets syndikering. Den finns i `azure-cognitive-services/decision` lagrings platsen och får namnet `anomaly-detector` . Det fullständigt kvalificerade namnet på behållar avbildningen är, `mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector` .

Den här behållar avbildningen har följande Taggar tillgängliga. Du kan också hitta en fullständig lista över [taggar i MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/decision/anomaly-detector/tags/list).

# <a name="latest-version"></a>[Senaste version](#tab/current)

| Bildtaggar                    | Kommentarer |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013560003-amd64-preview` |      |

# <a name="previous-versions"></a>[Tidigare versioner](#tab/previous)

| Bildtaggar                    | Kommentarer |
|-------------------------------|:------|
| `1.1.012300001-amd64-preview` |       |

---

## <a name="read-ocr-optical-character-recognition"></a>Läs OCR (optisk tecken läsning)

Du hittar [visuellt innehåll][cv-containers] Read OCR container-avbildningen i `mcr.microsoft.com` behållar registrets syndikering. Den finns i `azure-cognitive-services` lagrings platsen och får namnet `read` . Det fullständigt kvalificerade namnet på behållar avbildningen är, `mcr.microsoft.com/azure-cognitive-services/vision/read` .

Den här behållar avbildningen har följande Taggar tillgängliga. Du kan också hitta en fullständig lista över [taggar i MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/vision/read/tags/list).

# <a name="latest-version"></a>[Senaste version](#tab/current)

Viktig information för `3.2-preview.2` :
* Distroless-version
* ReadingOrder-parameter för att välja mellan text rad ordning i JSON-svar
* Utökad loggning
* Snabb korrigeringar till CJK-modell

| Bildtaggar                    | Kommentarer |
|-------------------------------|:------|
|3.2.2.014850001-49e0eac6-amd64-Preview                     |       |

# <a name="previous-versions"></a>[Tidigare versioner](#tab/previous)

Viktig information för `v2.0.013250001-amd64-preview` :

* Öka minnes användningen ytterligare för container.
* Extern cache krävs för installation av multi-poddar. Du kan till exempel konfigurera Redis för cachelagring.
* Korrigerade saknade resultat när Redis cache har kon figurer ATS och `ResultExpirationPeriod` är inställt på 0.
* Ta bort begär ande text storleks begränsning för 26MB. Container kan nu acceptera >26MB-filer.
* Lägg till en tidstämpel och build-version till konsol loggning.

Viktig information för `1.1.013050001-amd64-preview`

* `ReadEngineConfig:ResultExpirationPeriod`Initierings konfiguration för behållare har lagts till för att ange när systemet ska rensa igenkännings resultat. 
    * Inställningen är i timmar och standardvärdet är 48 timmar.
    * Inställningen kan minska minnes användningen för lagring av resultat, särskilt när behållare i minnet används.
    * Exempel 1. ReadEngineConfig: ResultExpirationPeriod = 1 rensar systemet igenkännings resultatet 1 tim efter processen.
    * Om den här konfigurationen är inställd på 0 rensas igenkännings resultatet av systemet när resultatet har hämtats.

* Ett internt Server fel 500 har åtgärd ATS när ett ogiltigt bild format skickas till systemet. Det kommer nu att returnera ett 400-fel:

    ```json
    {
        "error": {
        "code": "InvalidImageSize",
        "message": "Image must be between 1024 and 209715200 bytes."
        }
    }
    ```

| Bildtaggar                    | Kommentarer |
|-------------------------------|:------|
| `2.0.013250001-amd64-preview` |       |
| `1.1.013050001-amd64-preview` |       |
| `1.1.011580001-amd64-preview` |       |
| `1.1.009920003-amd64-preview` |       |
| `1.1.009910003-amd64-preview` |       |

---


## <a name="form-recognizer"></a>Formigenkänning

[Formulär igenkännings][fr-containers] behållar avbildningen finns i `mcr.microsoft.com` behållar registrets syndikering. Den finns i `azure-cognitive-services/custom-form` lagrings platsen och får namnet `labeltool` . Det fullständigt kvalificerade namnet på behållar avbildningen är, `mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool` .

Den här behållar avbildningen har följande Taggar tillgängliga. Du kan också hitta en fullständig lista över [taggar i MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/custom-form/labeltool/tags/list).

# <a name="latest-version"></a>[Senaste version](#tab/current)

| Bildtaggar                    | Kommentarer |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |


# <a name="previous-versions"></a>[Tidigare versioner](#tab/previous)

| Bildtaggar                    | Kommentarer |
|-------------------------------|:------|
| `1.1.008640001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |

---

## <a name="language-understanding-luis"></a>Language Understanding (LUIS)

[Luis][lu-containers] behållar avbildningen finns i `mcr.microsoft.com` behållar registrets syndikering. Den finns i `azure-cognitive-services/language` lagrings platsen och får namnet `luis` . Det fullständigt kvalificerade namnet på behållar avbildningen är, `mcr.microsoft.com/azure-cognitive-services/language/luis` .

Den här behållar avbildningen har följande Taggar tillgängliga. Du kan också hitta en fullständig lista över [taggar i MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/language/luis/tags/list).

# <a name="latest-version"></a>[Senaste version](#tab/current)

| Bildtaggar                    | Kommentarer |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.012280003-amd64-preview` |       |


# <a name="previous-version"></a>[Tidigare version](#tab/previous)

| Bildtaggar                    | Kommentarer |
|-------------------------------|:------|
| `1.1.012130003-amd64-preview` |       |

---

## <a name="custom-speech-to-text"></a>Custom Speech till text

Du hittar en behållar avbildning från [Custom Speech till text][sp-cstt] i `mcr.microsoft.com` behållar registrets syndikering. Den finns i `azure-cognitive-services/speechservices/` lagrings platsen och får namnet `custom-speech-to-text` . Det fullständigt kvalificerade namnet på behållar avbildningen är, `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text` . Du kan också hitta en fullständig lista över [taggar i MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/custom-speech-to-text/tags/list).


# <a name="latest-version"></a>[Senaste version](#tab/current)

Versions anmärkning för `2.9.0-amd64` :

**Funktion**
* Mer fel information om problem vid hämtning av anpassade modeller efter ID.
* Hypotes stöds som standard i konversations resultat.

Observera att på grund av listan över inkluderade fraser har storleken på behållar avbildningen ökat.

| Bildtaggar                    | Kommentarer | Sammandrag                                                                   |
|-------------------------------|:------|:-------------------------------------------------------------------------|
| `latest`                      |       | `sha256:bb1707cadba7add464a86df46d7d340c699692fe8df8d2222816189f06447a1b`|
| `2.9.0-amd64`                 |       | `sha256:bb1707cadba7add464a86df46d7d340c699692fe8df8d2222816189f06447a1b`|


# <a name="previous-version"></a>[Tidigare version](#tab/previous)

Versions anmärkning för `2.7.0-amd64` :

**Funktioner**
* Interpunktion anges som standard som aktive rad.

Observera att på grund av listan över inkluderade fraser har storleken på behållar avbildningen ökat.

Versions anmärkning för `2.6.0-amd64` :

**Funktioner**
* Stöd för phraselist v2 
* Fras listorna stöds i följande språk:
    * en – au
    * en-ca
    * en-GB
    * en-in
    * sv-se
    * zh-cn
* Stöd för nedladdning av anpassad bas modell. 
    * Använd `BaseModelLocale=<locale>` med `docker run` kommandot
* Fullständigt migrerat till .NET 3,1

**Fixe**
* Åtgärdade ett problem där förtroende poängen var alltid 1 i Diarization-läge
* Migrerat till TextAnalytics 3,0-API: et

Observera att på grund av listan över inkluderade fraser har storleken på behållar avbildningen ökat.

Versions anmärkning för `2.5.0-amd64` :

**Funktioner**
* Stöd för anpassat uttal av anpassade modeller
* Stöd för Azure och Azures Azures myndighets moln

**Fixe**
* Åtgärda kör som-problem som inte är rot användare i Diarization-läge

| Bildtaggar                    | Kommentarer               |
|-------------------------------|:--------------------|
| `2.7.0-amd64`                 |                     |
| `2.6.0-amd64`                 |                     |
| `2.5.0-amd64`                 |   första GA-versionen    |

---

## <a name="custom-text-to-speech"></a>Anpassad text till tal

Du hittar den [anpassade text till tal-][sp-ctts] behållar avbildningen i `mcr.microsoft.com` behållar registrets syndikering. Den finns i `azure-cognitive-services/speechservices/` lagrings platsen och får namnet `custom-text-to-speech` . Det fullständigt kvalificerade namnet på behållar avbildningen är, `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech` . Du kan också hitta en fullständig lista över [taggar i MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/custom-text-to-speech/tags/list).


# <a name="latest-version"></a>[Senaste version](#tab/current)

Versions anmärkning för `1.11.0-amd64` :

**Funktion**
* Mer fel information om problem vid hämtning av anpassade modeller efter ID.

| Bildtaggar                    | Kommentarer | Sammandrag                                                                   |
|-------------------------------|:------|:-------------------------------------------------------------------------|
| `latest`                      |       | `sha256:a4a5758a368bc56590eb1c1552fa2bda54f27be9cb0f491468388b9f90cb9110` |
| `1.11.0-amd64`                |       | `sha256:a4a5758a368bc56590eb1c1552fa2bda54f27be9cb0f491468388b9f90cb9110` |


# <a name="previous-version"></a>[Tidigare version](#tab/previous)

Versions anmärkning för `1.9.0-amd64` :

Vanlig månatlig utgåva

Versions anmärkning för `1.8.0-amd64` :

**Funktioner**
* Fullständigt migrerat till .NET 3,1

Versions anmärkning för `1.7.0-amd64` :

**Funktion**
* Delvis migrerad till .NET 3,1

| Bildtaggar                    | Kommentarer               |
|-------------------------------|:--------------------|
| `1.9.0-amd64`                 |                     |
| `1.8.0-amd64`                 |                     |
| `1.7.0-amd64`                 |   första GA-versionen    |

---

## <a name="speech-to-text"></a>Tal till text

Du hittar en behållar avbildning från [tal till text][sp-stt] i `mcr.microsoft.com` behållar registrets syndikering. Den finns i `azure-cognitive-services/speechservices/` lagrings platsen och får namnet `speech-to-text` . Det fullständigt kvalificerade namnet på behållar avbildningen är, `mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text` . Du kan hitta en fullständig lista över [taggar på MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/speech-to-text/tags/list).

Eftersom tal-till-text v-2.5.0 stöds stöds bilder i regionen *USA, amerikanska myndigheter* . Använd den *amerikanska regeringens* fakturerings slut punkt och API-nycklar när du använder den här regionen.

# <a name="latest-version"></a>[Senaste version](#tab/current)

Versions anmärkning för `2.9.0-amd64-<locale>` :

**Funktion**
* Mer fel information om problem vid hämtning av anpassade modeller efter ID.
* Hypotes stöds som standard i konversations resultat.

Observera att på grund av listan över inkluderade fraser har storleken på behållar avbildningen ökat. 

| Bildtaggar                    | Kommentarer                                                                                                |
|-------------------------------|:-----------------------------------------------------------------------------------------------------|
| `latest`                      | Behållar avbildning med `en-US` språkvarianten.                                                             |
| `2.9.0-amd64-<locale>`        | Ersätt `<locale>` med något av de tillgängliga språken i listan nedan. Till exempel `2.9.0-amd64-en-us`. |

Den här behållaren har följande tillgängliga språk.

| Språk för v-2.9.0           | Kommentarer                                    | Sammandrag                                                                    |
|-----------------------------|:-----------------------------------------|:--------------------------------------------------------------------------|
| `ar-ae`                     | Behållar avbildning med `ar-AE` språkvarianten. | `sha256:08885bedb2993daf0c918ecdc6ec775f7982ffa5ca561e80ab9b8a103cde8194` |
| `ar-bh`                     | Behållar avbildning med `ar-BH` språkvarianten. | `sha256:41e7942e4026beaad93e50f199a6a2d855f77c74e60bc9636bf2bf2c7d3bd482` |
| `ar-eg`                     | Behållar avbildning med `ar-EG` språkvarianten. | `sha256:d27f383435770aa01bb4117ba2d50a05ec172a1da35c4920ab43cd0fb74f44c2` |
| `ar-iq`                     | Behållar avbildning med `ar-IQ` språkvarianten. | `sha256:ca2734a6bfc562c4c07981358051d281fb5e089815b9eac14c66a0e6f92e9858` |
| `ar-jo`                     | Behållar avbildning med `ar-JO` språkvarianten. | `sha256:57429ee8e95a76ec953f1b1f94b39a20507626cd7fe5431df826912e5b959e41` |
| `ar-kw`                     | Behållar avbildning med `ar-KW` språkvarianten. | `sha256:08885bedb2993daf0c918ecdc6ec775f7982ffa5ca561e80ab9b8a103cde8194` |
| `ar-lb`                     | Behållar avbildning med `ar-LB` språkvarianten. | `sha256:4c5fb6fdc08343e8640222583373effae3d03907cf1262a4fad3303df9385797` |
| `ar-om`                     | Behållar avbildning med `ar-OM` språkvarianten. | `sha256:5ffd280908e3ee65fcb7bea0b532844f9d8510044ab4c2c612dc3c235938ad0a` |
| `ar-qa`                     | Behållar avbildning med `ar-QA` språkvarianten. | `sha256:08885bedb2993daf0c918ecdc6ec775f7982ffa5ca561e80ab9b8a103cde8194` |
| `ar-sa`                     | Behållar avbildning med `ar-SA` språkvarianten. | `sha256:08885bedb2993daf0c918ecdc6ec775f7982ffa5ca561e80ab9b8a103cde8194` |
| `ar-sy`                     | Behållar avbildning med `ar-SY` språkvarianten. | `sha256:00f3d1fd6ccb857ccef8a72322336e7a097d04027411f0dcc5499b44229fb470` |
| `bg-bg`                     | Behållar avbildning med `bg-BG` språkvarianten. | `sha256:aa6ae12f786dcaa028e5867abba198effed875b6bc4cbafd4be37349e95dceef` |
| `ca-es`                     | Behållar avbildning med `ca-ES` språkvarianten. | `sha256:515a940ccd76ef1926bab3ad259e1cc7ac2bd90bb3860d28f83d0f6324b3f0fe` |
| `cs-cz`                     | Behållar avbildning med `cs-CZ` språkvarianten. | `sha256:03f6242d73de64c3eb3347400ea6e7408a8816bd96f3d6368ea2a8193accd457` |
| `da-dk`                     | Behållar avbildning med `da-DK` språkvarianten. | `sha256:ed6714e804ff2d1bbd41512c78906ad9b8827dfdfed0076a271817e075c2ec40` |
| `de-de`                     | Behållar avbildning med `de-DE` språkvarianten. | `sha256:386f2bb4c4b6ba797919ddcb5bbc9942bf8a03e774f9b01438f9bae0928414ef` |
| `el-gr`                     | Behållar avbildning med `el-GR` språkvarianten. | `sha256:28696d10c78404fec033794e6e6ae0bfd92b0dab5cf7eb1d24cc2cdfbfcb646d` |
| `en-au`                     | Behållar avbildning med `en-AU` språkvarianten. | `sha256:dd9ce70f83767a5bdc52fd62b96e09ce6f79ecc1903ed8e116753099b06b03cd` |
| `en-ca`                     | Behållar avbildning med `en-CA` språkvarianten. | `sha256:70095cf952565256f3a0927358d0fd802d28fe1c3b89b26ead31ba1127cd0b06` |
| `en-gb`                     | Behållar avbildning med `en-GB` språkvarianten. | `sha256:836bc38328636799ec9c8717618d51ab8b50ea2f0dc9663f342c4454938c9b23` |
| `en-hk`                     | Behållar avbildning med `en-HK` språkvarianten. | `sha256:eda3702d95d4ae3b64ceb93bda42e8522776e141a18b2a3dde3bc3fcf0e9a2b8` |
| `en-ie`                     | Behållar avbildning med `en-IE` språkvarianten. | `sha256:bfc2126fffb947bf10ac379efb70db3d2c7ee2c16dd541a5b86e03e73d7d477c` |
| `en-in`                     | Behållar avbildning med `en-IN` språkvarianten. | `sha256:5660d02eabf4e1e9f58e7993ed7e5917b1990b41ed35a484a715d7265400cd0b` |
| `en-nz`                     | Behållar avbildning med `en-NZ` språkvarianten. | `sha256:891c1805fd8011865de7371ffd4bde85d879341f2100e8053bbbc722d7c792bc` |
| `en-ph`                     | Behållar avbildning med `en-PH` språkvarianten. | `sha256:21d6d46398f940a769241fdfffec5658356e54b4127b44efe5e061724f7a7681` |
| `en-sg`                     | Behållar avbildning med `en-SG` språkvarianten. | `sha256:6f473b8ba56bad098c21a0c0496cb312dafcfb83dc1a2e1aff21011f6b39321d` |
| `en-us`                     | Behållar avbildning med `en-US` språkvarianten. | `sha256:20aa22d24e35f7d92ceac96d2cbab8ce46ee0ed7bb601f18fa867f1bd0bcf5ab` |
| `en-za`                     | Behållar avbildning med `en-ZA` språkvarianten. | `sha256:5e5ad2b016a1ceac500813e0a68ff4108ddf5a4ca98cb0aed4930b6d1e8920dd` |
| `es-ar`                     | Behållar avbildning med `es-AR` språkvarianten. | `sha256:b372d9e32e7b518bb9949d8db459bd4e300304e53aed1342aba65a054d4a4c25` |
| `es-bo`                     | Behållar avbildning med `es-BO` språkvarianten. | `sha256:d3538f3834c554ebebbdfe75e261a06f104dfa27143353601c3a6a3d41025129` |
| `es-cl`                     | Behållar avbildning med `es-CL` språkvarianten. | `sha256:0bb100ef5313b182a59c08949e4baf1086bde2c1a6bca3324c4e052f465f7632` |
| `es-co`                     | Behållar avbildning med `es-CO` språkvarianten. | `sha256:cdab27080ef3ded55dcf89cf85bc2ae16de1372f84a42d836ff5f20612b68a61` |
| `es-cr`                     | Behållar avbildning med `es-CR` språkvarianten. | `sha256:e4ea51ffa38f347adc7c0642d50237cfa045683f52b5e3e726e4c28688231d35` |
| `es-cu`                     | Behållar avbildning med `es-CU` språkvarianten. | `sha256:f81c0b7f774d64e673a1311d00604f5e4837fdba4d8fb4a2ab0c8bb8b7fde87d` |
| `es-do`                     | Behållar avbildning med `es-DO` språkvarianten. | `sha256:78035c54e649e34cd8276a402f9c9845e13bc40503da6c2f631698a16a049c67` |
| `es-ec`                     | Behållar avbildning med `es-EC` språkvarianten. | `sha256:e4e4d9c123e452f8ae89bf6cc1292a406f7b482668e36b48ef2fbb29f14c4360` |
| `es-es`                     | Behållar avbildning med `es-ES` språkvarianten. | `sha256:10a4ddd279633cc8696b00be77f6e9309494a560244a325982522aaa805806e7` |
| `es-gt`                     | Behållar avbildning med `es-GT` språkvarianten. | `sha256:a603a8f9c1778808df5d14e3fa1c7e993ef9cca3e0b515a4d4586c2c3a1d14b6` |
| `es-hn`                     | Behållar avbildning med `es-HN` språkvarianten. | `sha256:4f539f8019c489623868bf02f3c61ed4b66d3a85e89250a9b484717a91e9489e` |
| `es-mx`                     | Behållar avbildning med `es-MX` språkvarianten. | `sha256:20fc3806f08ad4e6fd5fb1f71318f1f5b591e2085ee4cbba2f25ea06135e5f6a` |
| `es-ni`                     | Behållar avbildning med `es-NI` språkvarianten. | `sha256:d65520a4f628f6a416171ac58341579fdffba97ddd2941a910bda385d31c735d` |
| `es-pa`                     | Behållar avbildning med `es-PA` språkvarianten. | `sha256:d38ea88613f5db6d6d9f879ef92a204c524bb27766848b825d1e6ce2a9b13cf7` |
| `es-pe`                     | Behållar avbildning med `es-PE` språkvarianten. | `sha256:02205d1ecc29feed3ac8442dbdc1855c419749d9dcbd98028a5d1619166f0328` |
| `es-pr`                     | Behållar avbildning med `es-PR` språkvarianten. | `sha256:c9c3e1ac800120a14f472c8be62730a489e00f29df29fe770a56429ea1c09ef5` |
| `es-py`                     | Behållar avbildning med `es-PY` språkvarianten. | `sha256:859c24c40e65bc19a866218466eb7678f71205bedfcb6ee3180b6cb721194b9a` |
| `es-sv`                     | Behållar avbildning med `es-SV` språkvarianten. | `sha256:036f13d34005f5d6634387c9d13c3535724795b0d6cad832fc46363609fc2f11` |
| `es-us`                     | Behållar avbildning med `es-US` språkvarianten. | `sha256:b8eb300d0a11dc397d0bab02e1f6b26de6091595fd052ebb607f196c28d16f1c` |
| `es-uy`                     | Behållar avbildning med `es-UY` språkvarianten. | `sha256:0ffba124ecd79777ca08055689a1d853916ccd8c8f2806d0001edf5eb4aa42fa` |
| `es-ve`                     | Behållar avbildning med `es-VE` språkvarianten. | `sha256:4d7caf48264eaf18bb2d07b0258d6f64b7c26815fdbdf812718dd8e88f1a6d1e` |
| `et-ee`                     | Behållar avbildning med `et-EE` språkvarianten. | `sha256:310abdc1a8490990a99ce061f04c9d49cafb7a452fbfdc2790de6f60e1505c6c` |
| `fi-fi`                     | Behållar avbildning med `fi-FI` språkvarianten. | `sha256:8f209d30b2d148224b296c2d2c204b5970fbe7aaf5eb3289cf8b6644bfd78373` |
| `fr-ca`                     | Behållar avbildning med `fr-CA` språkvarianten. | `sha256:11b718d4b86d606b198e47deaa25f6ce164cfc53267048e3d2dbe1bc8500cc5a` |
| `fr-fr`                     | Behållar avbildning med `fr-FR` språkvarianten. | `sha256:7a4264a0e9560e6aa3fdee80c3e3f55a0e26cddce8ebbeb7a9c87693ab451a25` |
| `ga-ie`                     | Behållar avbildning med `ga-IE` språkvarianten. | `sha256:bbc764ac08b2ef10ac58a8f9534d4d375109fdf16ab75c8cdbf2d57aa692d3e2` |
| `gu-in`                     | Behållar avbildning med `gu-IN` språkvarianten. | `sha256:2d0a83b7bcf1cfc50cf013c95442519e5236a146b7968e75e129b3a5c33ad3a1` |
| `hi-in`                     | Behållar avbildning med `hi-IN` språkvarianten. | `sha256:f0ee8f259035ac5dd9ef38807495d0f8d989ddbb8eacf83893f1fea22265e6b4` |
| `hr-hr`                     | Behållar avbildning med `hr-HR` språkvarianten. | `sha256:6101ecac9f5f35c1ea1b8cd8e52fdbbc1be2582e4f3e385c16509fd95a002217` |
| `hu-hu`                     | Behållar avbildning med `hu-HU` språkvarianten. | `sha256:9e94c4d6fff73058ce4eef609b8404430a429c6961648655c915cb2fac10656f` |
| `it-it`                     | Behållar avbildning med `it-IT` språkvarianten. | `sha256:44986ad44bb53eaf350e0865e62ea5ba7f37d1f5b52e388f61f56fd7afe8ff32` |
| `ja-jp`                     | Behållar avbildning med `ja-JP` språkvarianten. | `sha256:6b7aaa828d1b2d2fce1831e540e08ba60307088b90ca32e96fd002a67aff926b` |
| `ko-kr`                     | Behållar avbildning med `ko-KR` språkvarianten. | `sha256:1abeda544a7579daac7f8b8f8d34a2cc63b4bd3631e474315d424973ae024ab0` |
| `lt-lt`                     | Behållar avbildning med `lt-LT` språkvarianten. | `sha256:455da50a7db591df7be69d7cd361a77734b9249101d8cf86b807f0350b5167ef` |
| `lv-lv`                     | Behållar avbildning med `lv-LV` språkvarianten. | `sha256:676e17b6223e35d1897b46536e6f523e1d18b78f834b62ec00bb126ad3a2e71a` |
| `mr-in`                     | Behållar avbildning med `mr-IN` språkvarianten. | `sha256:dbfb97e52dc4b4c71dec1a9e622714f004b1e59d7900260e09a85bf15912fccd` |
| `mt-mt`                     | Behållar avbildning med `mt-MT` språkvarianten. | `sha256:19f7f644ae3a0639fdcc53acc065d0e534b74c07f8c095418d4d4d444c566bf1` |
| `nb-no`                     | Behållar avbildning med `nb-NO` språkvarianten. | `sha256:d3a13ab6fa2eb5d5ca0e3281b1092452650e9ede8749f6edcab990e3bbb8d198` |
| `nl-nl`                     | Behållar avbildning med `nl-NL` språkvarianten. | `sha256:7ad5e61f9a72c600bdc79e4c04ac63c239951ac4c0d44e02fe0607a6aff356cc` |
| `pl-pl`                     | Behållar avbildning med `pl-PL` språkvarianten. | `sha256:fe6a4812534d704b145b84fd8857fb3d9052f67fcbbd5d490c5902082e295195` |
| `pt-br`                     | Behållar avbildning med `pt-BR` språkvarianten. | `sha256:adcd34941d4ace7db01bd476d61c9bbafe071419932b4cfae5231cf202af3a14` |
| `pt-pt`                     | Behållar avbildning med `pt-PT` språkvarianten. | `sha256:0534a7e4b391f1ee666b248a274879c081496ed4939b0ad33154d8a96fd67f94` |
| `ro-ro`                     | Behållar avbildning med `ro-RO` språkvarianten. | `sha256:091ea4a31652ff9dbc6259636f6c12b0ceb79a269e2cf3cdec677a1914b6a64e` |
| `ru-ru`                     | Behållar avbildning med `ru-RU` språkvarianten. | `sha256:5eef3ae8afb445e60bb913edd6eed1415abb0bfbc439978f69f4cba7b61c8e6e` |
| `sk-sk`                     | Behållar avbildning med `sk-SK` språkvarianten. | `sha256:98709e9349d889b57933317005af42770e47ce8178a7d9c737d9fbdd81148478` |
| `sl-si`                     | Behållar avbildning med `sl-SI` språkvarianten. | `sha256:3a9139334c4780dc6f6a9b0f15fba5292e16ecf1f5d45fe49a9c8ef3b0e110b3` |
| `sv-se`                     | Behållar avbildning med `sv-SE` språkvarianten. | `sha256:b29b2a65d83c20d65ba4e4fbca66f9fc07e536e161f90448c2bb360eb8de1e55` |
| `ta-in`                     | Behållar avbildning med `ta-IN` språkvarianten. | `sha256:4302e1d979b24a23595ee2b1fd074a57ee36166ce9ac400a3deb397341ae52b2` |
| `te-in`                     | Behållar avbildning med `te-IN` språkvarianten. | `sha256:69be11a63199d9a6f63ac346e689051ba9cd5214894b110da2879aaa0f4a8e88` |
| `th-th`                     | Behållar avbildning med `th-TH` språkvarianten. | `sha256:2e4167dacdcb2c9d91930356ebae311b6b33ceb3e85f908422e880edbd42da64` |
| `tr-tr`                     | Behållar avbildning med `tr-TR` språkvarianten. | `sha256:d46289ee9ba71c9c1dbbefa5da439e71310af74633c9d6d6d448d2ebee60da02` |
| `zh-cn`                     | Behållar avbildning med `zh-CN` språkvarianten. | `sha256:49eeee500e07ffd3056ba8aab314d6c8458399a8c0d6d44ce1d9aebf50ddca06` |
| `zh-hk`                     | Behållar avbildning med `zh-HK` språkvarianten. | `sha256:5a3251ad6df9565d44dd422de4fa0d83a9b50c8a80ec15213403482940d2b2fc` |
| `zh-tw`                     | Behållar avbildning med `zh-TW` språkvarianten. | `sha256:2c45dd90b0c19d7f12b1be44d3e85fe2603cea2389c2877b79d6de351839cf6a` |


# <a name="previous-version"></a>[Tidigare version](#tab/previous)

Versions anmärkning för `2.7.0-amd64-<locale>` :

**Funktioner**
* Stöd för följande nya språk:
    * ar-BH, ar-sweetiq, ar-Jo, ar-LB, ar-om, ar-sy
    * bg-bg
    * el-gr
    * en-hk, en-IE, en-pH, en-TG, en-za
    * ES-p.a., es-es, ES-cl, es-co, ES-CR, ES-Cu, ES--, ES-EG, ES-gt, ES-, ES-, ES-, ES-uy, ES-sa, ES-US, ES-PR, ES-sa, ES-USA, ES-, ES-ve
    * et-ee
    * ga-ie
    * hr-hr
    * hu-hu
    * lt-lt
    * lv-lv
    * MT-MT
    * ro-ro
    * sk-sk
    * SL-SL
* Interpunktion är aktiverat som standard.

Observera att på grund av listan över inkluderade fraser har storleken på behållar avbildningen ökat. 

Versions anmärkning för `2.6.0-amd64-<locale>` :

**Funktioner**
* Uppgraderas till de senaste modellerna och migreras fullständigt till .NET 3,1
* Stöd för phraselist v2
* Fras listorna stöds i följande språk:
    * en – au
    * en-ca
    * en-GB
    * en-in
    * sv-se
    * zh-cn
* Stöd för nya nationella inställningar `cs-CZ` 
    * Versaler och skiljetecken stöds inte för närvarande.

**Fixe**
* Åtgärdar ett problem där Tillförlitlighets poängen alltid är 1 i Diarization-läge
* Migrerad Använd TextAnalytics 3,0-API: et

Observera att på grund av listan över inkluderade fraser har storleken på behållar avbildningen ökat. 

Versions anmärkning för `2.5.0-amd64-<locale>` :

**Funktioner**
* Stöd för Azures moln för amerikanska myndigheter

**Fixe**
* Åtgärdar ett problem med att köra som en icke-rot användare i Diarization-läge

| Bildtaggar                  | Kommentarer                                    |
|-----------------------------|:-----------------------------------------|
| `2.7.0-amd64-<locale>`      | Ersätt `<locale>` med något av de tillgängliga språken i listan nedan. Till exempel `2.7.0-amd64-en-us`. |
| `2.6.0-amd64-<locale>`      | Ersätt `<locale>` med något av de tillgängliga språken i listan nedan. Till exempel `2.6.0-amd64-en-us`. |
| `2.5.0-amd64-<locale>`      | Ersätt `<locale>` med något av de tillgängliga språken i listan nedan. Till exempel `2.5.0-amd64-en-us`. |


Den här behållaren har följande tillgängliga språk.

| Språk för v-2.7.0           | Kommentarer                                    | Sammandrag                                                                   |
|-----------------------------|:-----------------------------------------|:-------------------------------------------------------------------------|
| `ar-ae`                     | Behållar avbildning med `ar-AE` språkvarianten. | `sha256:c8e99e71e6740cf671f3bf79de8b7dd890122cb674eedd2440e71e7cbc4c66b` |
| `ar-bh`                     | Behållar avbildning med `ar-BH` språkvarianten. | `sha256:5a2c140661f50d0c95587121ec1ab8895289f4dda5b3ad14074413e869e6bd4` |
| `ar-eg`                     | Behållar avbildning med `ar-EG` språkvarianten. | `sha256:783bb8321fcfb7890b0c99935099f7e84c85a698c2fe0031c661e265358d79c` |
| `ar-iq`                     | Behållar avbildning med `ar-IQ` språkvarianten. | `sha256:abd0101f73c1cf71f30da7b11b93d2a7ac8877dbfcfc2d34553d20705aca7a2` |
| `ar-jo`                     | Behållar avbildning med `ar-JO` språkvarianten. | `sha256:d4c7fd2a1637e163aa106c23b6a759e8c78366c60ece83b3aabfe93ebabae07` |
| `ar-kw`                     | Behållar avbildning med `ar-KW` språkvarianten. | `sha256:c8e99e71e6740cf671f3bf79de8b7dd890122cb674eedd2440e71e7cbc4c66b` |
| `ar-lb`                     | Behållar avbildning med `ar-LB` språkvarianten. | `sha256:20e5c9105e86625c72de54290a6eb07630d35c3760f729c4b855e3661583dfe` |
| `ar-om`                     | Behållar avbildning med `ar-OM` språkvarianten. | `sha256:97f1b44f2cbb837a2ef86441a0a52a07f706240edb6ef6618ee4db8cbbe1c19` |
| `ar-qa`                     | Behållar avbildning med `ar-QA` språkvarianten. | `sha256:c8e99e71e6740cf671f3bf79de8b7dd890122cb674eedd2440e71e7cbc4c66b` |
| `ar-sa`                     | Behållar avbildning med `ar-SA` språkvarianten. | `sha256:c8e99e71e6740cf671f3bf79de8b7dd890122cb674eedd2440e71e7cbc4c66b` |
| `ar-sy`                     | Behållar avbildning med `ar-SY` språkvarianten. | `sha256:51980a2e2c3dd3548deedcedaf5fc688db602a5eced1a4b7df7d10750393623` |
| `bg-bg`                     | Behållar avbildning med `bg-BG` språkvarianten. | `sha256:1c1acf0fbb353ebb04692f37eb4d4cdf0b4e309720dd7e709001dada0d1ea81` |
| `ca-es`                     | Behållar avbildning med `ca-ES` språkvarianten. | `sha256:c60baa0007f61c7652b97b49645215de63411125d627c974c09222e316df204` |
| `cs-cz`                     | Behållar avbildning med `cs-CZ` språkvarianten. | `sha256:3fa09fc3a6bde6b77df2444aae8fc78b5f25fb9010171d1682db116ea5801f5` |
| `da-dk`                     | Behållar avbildning med `da-DK` språkvarianten. | `sha256:4b26dbba50c2771943880b68e0e4ea0713d0e3bb8bad884454849bccc9e94a3` |
| `de-de`                     | Behållar avbildning med `de-DE` språkvarianten. | `sha256:5109ed80b1fecf4db0328adcd50528d0aa9e726b5fc84587c40aaea4e91256d` |
| `el-gr`                     | Behållar avbildning med `el-GR` språkvarianten. | `sha256:fc8b466c588bf097efac2b79454d5ac0df5c6990398f07ede9be7e1d536e4bd` |
| `en-au`                     | Behållar avbildning med `en-AU` språkvarianten. | `sha256:3461892a27fc3eb3f9610b2def00bc15f380c6b9797c90ceca19e6abb55f6a6` |
| `en-ca`                     | Behållar avbildning med `en-CA` språkvarianten. | `sha256:a0509be39785f1e869bd96ab10e7c07d3f4e61c9aa17ff5900076e7bd64ba11` |
| `en-gb`                     | Behållar avbildning med `en-GB` språkvarianten. | `sha256:1b976fc7ac109e61dcf74af3652c12535e3db92931d2d0bb2ea59bd46f9efed` |
| `en-hk`                     | Behållar avbildning med `en-HK` språkvarianten. | `sha256:0b1e1df101f978869c98f6e50632712016b8311fc89b334e7f44e968d64bf2f` |
| `en-ie`                     | Behållar avbildning med `en-IE` språkvarianten. | `sha256:c5ba0d3c7219ce39f0b918a51a7cae8a65c277f564279cad920e068725aa39f` |
| `en-in`                     | Behållar avbildning med `en-IN` språkvarianten. | `sha256:e907f07be498f024103f6fe6abffa23e242bf3585724741b29a2f3f41d0899c` |
| `en-nz`                     | Behållar avbildning med `en-NZ` språkvarianten. | `sha256:66845f6ce20ae71d609867c6eb4772366ce042499e4bcdce4c1b579daf7fad7` |
| `en-ph`                     | Behållar avbildning med `en-PH` språkvarianten. | `sha256:e7874653bf66b1a1ab344b3391eb8767be34260b7f11b62fd057cbe17b805b2` |
| `en-sg`                     | Behållar avbildning med `en-SG` språkvarianten. | `sha256:827cdb158280e6f4037f4815410c7aa78abf9c6467876c1504aecfef787bdd7` |
| `en-us`                     | Behållar avbildning med `en-US` språkvarianten. | `sha256:248d17340055e3e137219ddc234c605e6a53ceead136ea55c9697c352da6a8d` |
| `en-za`                     | Behållar avbildning med `en-ZA` språkvarianten. | `sha256:a8abc99f498db7088bb25acec47da81e90b6a5eaa1c6f78e0f9a314d839d0ae` |
| `es-ar`                     | Behållar avbildning med `es-AR` språkvarianten. | `sha256:edf78429630851b6eb01f54f8a8a1aeeda9971c6a834403a204662eda22b3b9` |
| `es-bo`                     | Behållar avbildning med `es-BO` språkvarianten. | `sha256:5832b44f1da2f6b9a097c99babfbc370d8d0eabe1ff8daabec2c3f482dc9d63` |
| `es-cl`                     | Behållar avbildning med `es-CL` språkvarianten. | `sha256:409a712b96235e154472134f96ff9272265f1e5b555e00ad03c2260b0781009` |
| `es-co`                     | Behållar avbildning med `es-CO` språkvarianten. | `sha256:99792bc083dc16e0edf15491e6a840d786c9140b747551563a8d98f66f0b415` |
| `es-cr`                     | Behållar avbildning med `es-CR` språkvarianten. | `sha256:21fe14a538e5b8b2d288b00b8f5a02d87469e285f32e725155042079f336ac9` |
| `es-cu`                     | Behållar avbildning med `es-CU` språkvarianten. | `sha256:05d40eae01cec4c42c4febd379cd61373eb43d0aacfd47b988bb95e6a6ad216` |
| `es-do`                     | Behållar avbildning med `es-DO` språkvarianten. | `sha256:73dd0e0d4f39a259563ee7cc18c2e72c9ab20c52905fe343e0413ca7c4b3f0d` |
| `es-ec`                     | Behållar avbildning med `es-EC` språkvarianten. | `sha256:c3e69139ef365fe9332b5b68b43458242c7dad9d9f2b557431272306e81cb9e` |
| `es-es`                     | Behållar avbildning med `es-ES` språkvarianten. | `sha256:bd83fcfc116ba645a0e12a7a93b6ada74a8f701172f826a91c5f223a1dbaa61` |
| `es-gt`                     | Behållar avbildning med `es-GT` språkvarianten. | `sha256:5bb9b18b91b74e123e3720893d88bfcb0a87dac31a1f7171d23c7cb1fa09fee` |
| `es-hn`                     | Behållar avbildning med `es-HN` språkvarianten. | `sha256:941d108a4b76eb554e8f13cf5090665a702de3ebf35b75e4350f0916dfccd72` |
| `es-mx`                     | Behållar avbildning med `es-MX` språkvarianten. | `sha256:cebea03732781b4425500d162ae6580bbd7ce9b5f4ede988c4570fe311d8567` |
| `es-ni`                     | Behållar avbildning med `es-NI` språkvarianten. | `sha256:8ba165f94ad840936ebd0af17a0a63aa08a6292e7ad9029f5b93eef41165eb9` |
| `es-pa`                     | Behållar avbildning med `es-PA` språkvarianten. | `sha256:c61b7f1b6801a03c3eab0dd1aede87017a86bc7368ded2f8bad8d9e5f60d0d3` |
| `es-pe`                     | Behållar avbildning med `es-PE` språkvarianten. | `sha256:447a3ab3f302aba24d201d9f5b2877ffcd64dfd5e9d6b88d9924847160b2de2` |
| `es-pr`                     | Behållar avbildning med `es-PR` språkvarianten. | `sha256:a53b3295c986e91ee8cf93ebe1057b997c76ef7f99913508b859311a194fdd4` |
| `es-py`                     | Behållar avbildning med `es-PY` språkvarianten. | `sha256:85b3f75e75e63e29521daf772ee68a59ac2428579512501aa81dc51a2315652` |
| `es-sv`                     | Behållar avbildning med `es-SV` språkvarianten. | `sha256:db5ece7ba536e38d5de59cd37807630ab76589dcf1c97e253f98d7f44d9424e` |
| `es-us`                     | Behållar avbildning med `es-US` språkvarianten. | `sha256:99f2743725bb71e25543484f49bcfde14584ccbbaaa912678938d69d965075a` |
| `es-uy`                     | Behållar avbildning med `es-UY` språkvarianten. | `sha256:a3e11c16a97a1ae76408d812b2fee1e4b3ba07160bbcb62a22814523568ee5d` |
| `es-ve`                     | Behållar avbildning med `es-VE` språkvarianten. | `sha256:8cb431aafd84263ead8de946377c1d3f2ddfa7e172b8a4c5aa7ba477c5b41f0` |
| `et-ee`                     | Behållar avbildning med `et-EE` språkvarianten. | `sha256:943e7cf894e9d75341a58993104824c1c8cd8da1322cc5a732e9d53882c6523` |
| `fi-fi`                     | Behållar avbildning med `fi-FI` språkvarianten. | `sha256:35658e9dce796cb96a1371f250398e86351ea1b5ada080da7ce8471b30c7cae` |
| `fr-ca`                     | Behållar avbildning med `fr-CA` språkvarianten. | `sha256:62256cad671e8baa03fdd4c5f4eca7d5c5effedd64cafd9020ba72c9c4210e0` |
| `fr-fr`                     | Behållar avbildning med `fr-FR` språkvarianten. | `sha256:b385993232d9daa327d1a7b067268927b17f36eed3e8d423748794544c62746` |
| `ga-ie`                     | Behållar avbildning med `ga-IE` språkvarianten. | `sha256:ab9abdb993b0f7487edda8200f1393ac44ba4888c0f444a02afb6c85ca3e393` |
| `gu-in`                     | Behållar avbildning med `gu-IN` språkvarianten. | `sha256:328e69488f2948722d7ccc97e266071f61a8c9f65cd671688490955806526de` |
| `hi-in`                     | Behållar avbildning med `hi-IN` språkvarianten. | `sha256:b9b0bfec80aa53d06ea2cbd9097f753ec5caaf00ac2f00321ae7ad916fd7fa6` |
| `hr-hr`                     | Behållar avbildning med `hr-HR` språkvarianten. | `sha256:ab849cd2eeea682f8958bba8986fe90f0f7bb3b447512a10cf464e8e1ce4ea5` |
| `hu-hu`                     | Behållar avbildning med `hu-HU` språkvarianten. | `sha256:30f239b155d91523442cf74a1f2732304fa2b50ae7b786833bb6a020b982621` |
| `it-it`                     | Behållar avbildning med `it-IT` språkvarianten. | `sha256:288f95413870eb9d33bf1dabfa6fbd6b55b0faa52e4d5face3171d1dd4ddbdd` |
| `ja-jp`                     | Behållar avbildning med `ja-JP` språkvarianten. | `sha256:e3ab37a80c215dec565eca212f57eb81887fc2894452868dff92e3bd42c4bb9` |
| `ko-kr`                     | Behållar avbildning med `ko-KR` språkvarianten. | `sha256:c1208b8459333b606af516cd7806e9d4d5e002247bb1225e1f246563b356890` |
| `lt-lt`                     | Behållar avbildning med `lt-LT` språkvarianten. | `sha256:8dec331161d3c29fc65ba6651fcc6cfe69fa314519f408b5f9f8eb27da09830` |
| `lv-lv`                     | Behållar avbildning med `lv-LV` språkvarianten. | `sha256:7cf31282910b339666bb2b0a555caa7fc6ae414eea4423a41f35c3527f83235` |
| `mr-in`                     | Behållar avbildning med `mr-IN` språkvarianten. | `sha256:9cb012bd58ef7723d4905d6fa3c1fde96e33c354b3d96d4e3ff69cf6e1bfe3a` |
| `mt-mt`                     | Behållar avbildning med `mt-MT` språkvarianten. | `sha256:a0094c032ea555b168ec5751ab3257337d902d526e9ae335671fb751a352378` |
| `nb-no`                     | Behållar avbildning med `nb-NO` språkvarianten. | `sha256:6bbc326e20a6a785b1ca33143b42a060858efb67b863a267d6efb7aebb48f87` |
| `nl-nl`                     | Behållar avbildning med `nl-NL` språkvarianten. | `sha256:94b4ddf4cc80fa666e422f8416aea3f98ebe4842dfe9b1f4bfea7c47eb61127` |
| `pl-pl`                     | Behållar avbildning med `pl-PL` språkvarianten. | `sha256:58e5f78bf772c3c8cbd5f0c5d6e67f5348e04e3f893d84738a2a3e964bab256` |
| `pt-br`                     | Behållar avbildning med `pt-BR` språkvarianten. | `sha256:f500ef956bd28807f40df1f9f0520e437c5084f61a3be6d1379e746887d5b7c` |
| `pt-pt`                     | Behållar avbildning med `pt-PT` språkvarianten. | `sha256:c841d2dbe5f40adf6039242c106985febb1a44212feb55d9769fe31134ec116` |
| `ro-ro`                     | Behållar avbildning med `ro-RO` språkvarianten. | `sha256:93271c39c0a134e987a069c2a65289acff9869ae0d90fdcb39928c9ef0fd86b` |
| `ru-ru`                     | Behållar avbildning med `ru-RU` språkvarianten. | `sha256:8d6b3c600e56cc96813b8c14b7916c5539a20ba561dc1c6d5bbef6285d6eef6` |
| `sk-sk`                     | Behållar avbildning med `sk-SK` språkvarianten. | `sha256:6d604092cc6c964663a1c97d91c8f1c8cf4b46d07427d03f7041c0cc55eb521` |
| `sl-si`                     | Behållar avbildning med `sl-SI` språkvarianten. | `sha256:f237ed58fedefcc749e74be1258cc70e5a690ee6c5a6b6388bd24075faa61da` |
| `sv-se`                     | Behållar avbildning med `sv-SE` språkvarianten. | `sha256:da4233e6658b00eefdadb9d4acd889c6550a5e2a4a7af7a9f915c878abd4c9c` |
| `ta-in`                     | Behållar avbildning med `ta-IN` språkvarianten. | `sha256:22b77606d25e9c2f52bf3cad6218782b4719f6a9dcfadc770468d266758a56c` |
| `te-in`                     | Behållar avbildning med `te-IN` språkvarianten. | `sha256:7f4d11372862ca1d65fc9b868e2d775701b8e6eabd786c90c4e9ab82ba86e88` |
| `th-th`                     | Behållar avbildning med `th-TH` språkvarianten. | `sha256:69033bcd7c0f59d31bafec6c2b7a9ff343928cdd58c16105415c291d555d37b` |
| `tr-tr`                     | Behållar avbildning med `tr-TR` språkvarianten. | `sha256:4b7d339846a0d371dfe25aa2e626f131003c01329c9a1da468eb3703ef176ea` |
| `zh-cn`                     | Behållar avbildning med `zh-CN` språkvarianten. | `sha256:a428459830fb766083212f71c5638a65ce30d8dd84f6c624ae22768e8a76976` |
| `zh-hk`                     | Behållar avbildning med `zh-HK` språkvarianten. | `sha256:7a2903462b67336a6ce4c8e2faac42052f0a4392d1d5eb3839758cc8d0429f1` |
| `zh-tw`                     | Behållar avbildning med `zh-TW` språkvarianten. | `sha256:30fd2b3660e047d24a46fbba14ba282f15bc0339ec93f49afd0d02ff4069146` |

| Språk för v-2.6.0           | Kommentarer                                    |
|-----------------------------|:-----------------------------------------|
| `ar-ae`                     | Behållar avbildning med `ar-AE` språkvarianten. |
| `ar-eg`                     | Behållar avbildning med `ar-EG` språkvarianten. |
| `ar-kw`                     | Behållar avbildning med `ar-KW` språkvarianten. |
| `ar-qa`                     | Behållar avbildning med `ar-QA` språkvarianten. |
| `ar-sa`                     | Behållar avbildning med `ar-SA` språkvarianten. |
| `ca-es`                     | Behållar avbildning med `ca-ES` språkvarianten. |
| `cs-cz`                     | Behållar avbildning med `cs-CZ` språkvarianten. |
| `da-dk`                     | Behållar avbildning med `da-DK` språkvarianten. |
| `de-de`                     | Behållar avbildning med `de-DE` språkvarianten. |
| `en-au`                     | Behållar avbildning med `en-AU` språkvarianten. |
| `en-ca`                     | Behållar avbildning med `en-CA` språkvarianten. |
| `en-gb`                     | Behållar avbildning med `en-GB` språkvarianten. |
| `en-in`                     | Behållar avbildning med `en-IN` språkvarianten. |
| `en-nz`                     | Behållar avbildning med `en-NZ` språkvarianten. |
| `en-us`                     | Behållar avbildning med `en-US` språkvarianten. |
| `es-es`                     | Behållar avbildning med `es-ES` språkvarianten. |
| `es-mx`                     | Behållar avbildning med `es-MX` språkvarianten. |
| `fi-fi`                     | Behållar avbildning med `fi-FI` språkvarianten. |
| `fr-ca`                     | Behållar avbildning med `fr-CA` språkvarianten. |
| `fr-fr`                     | Behållar avbildning med `fr-FR` språkvarianten. |
| `gu-in`                     | Behållar avbildning med `gu-IN` språkvarianten. |
| `hi-in`                     | Behållar avbildning med `hi-IN` språkvarianten. |
| `it-it`                     | Behållar avbildning med `it-IT` språkvarianten. |
| `ja-jp`                     | Behållar avbildning med `ja-JP` språkvarianten. |
| `ko-kr`                     | Behållar avbildning med `ko-KR` språkvarianten. |
| `mr-in`                     | Behållar avbildning med `mr-IN` språkvarianten. |
| `nb-no`                     | Behållar avbildning med `nb-NO` språkvarianten. |
| `nl-nl`                     | Behållar avbildning med `nl-NL` språkvarianten. |
| `pl-pl`                     | Behållar avbildning med `pl-PL` språkvarianten. |
| `pt-br`                     | Behållar avbildning med `pt-BR` språkvarianten. |
| `pt-pt`                     | Behållar avbildning med `pt-PT` språkvarianten. |
| `ru-ru`                     | Behållar avbildning med `ru-RU` språkvarianten. |
| `sv-se`                     | Behållar avbildning med `sv-SE` språkvarianten. |
| `ta-in`                     | Behållar avbildning med `ta-IN` språkvarianten. |
| `te-in`                     | Behållar avbildning med `te-IN` språkvarianten. |
| `th-th`                     | Behållar avbildning med `th-TH` språkvarianten. |
| `tr-tr`                     | Behållar avbildning med `tr-TR` språkvarianten. |
| `zh-cn`                     | Behållar avbildning med `zh-CN` språkvarianten. |
| `zh-hk`                     | Behållar avbildning med `zh-HK` språkvarianten. |
| `zh-tw`                     | Behållar avbildning med `zh-TW` språkvarianten. |

| Språk för v-2.5.0           | Kommentarer                                    |
|-----------------------------|:-----------------------------------------|
| `ar-ae`                     | Behållar avbildning med `ar-AE` språkvarianten. |
| `ar-eg`                     | Behållar avbildning med `ar-EG` språkvarianten. |
| `ar-kw`                     | Behållar avbildning med `ar-KW` språkvarianten. |
| `ar-qa`                     | Behållar avbildning med `ar-QA` språkvarianten. |
| `ar-sa`                     | Behållar avbildning med `ar-SA` språkvarianten. |
| `ca-es`                     | Behållar avbildning med `ca-ES` språkvarianten. |
| `da-dk`                     | Behållar avbildning med `da-DK` språkvarianten. |
| `de-de`                     | Behållar avbildning med `de-DE` språkvarianten. |
| `en-au`                     | Behållar avbildning med `en-AU` språkvarianten. |
| `en-ca`                     | Behållar avbildning med `en-CA` språkvarianten. |
| `en-gb`                     | Behållar avbildning med `en-GB` språkvarianten. |
| `en-in`                     | Behållar avbildning med `en-IN` språkvarianten. |
| `en-nz`                     | Behållar avbildning med `en-NZ` språkvarianten. |
| `en-us`                     | Behållar avbildning med `en-US` språkvarianten. |
| `es-es`                     | Behållar avbildning med `es-ES` språkvarianten. |
| `es-mx`                     | Behållar avbildning med `es-MX` språkvarianten. |
| `fi-fi`                     | Behållar avbildning med `fi-FI` språkvarianten. |
| `fr-ca`                     | Behållar avbildning med `fr-CA` språkvarianten. |
| `fr-fr`                     | Behållar avbildning med `fr-FR` språkvarianten. |
| `gu-in`                     | Behållar avbildning med `gu-IN` språkvarianten. |
| `hi-in`                     | Behållar avbildning med `hi-IN` språkvarianten. |
| `it-it`                     | Behållar avbildning med `it-IT` språkvarianten. |
| `ja-jp`                     | Behållar avbildning med `ja-JP` språkvarianten. |
| `ko-kr`                     | Behållar avbildning med `ko-KR` språkvarianten. |
| `mr-in`                     | Behållar avbildning med `mr-IN` språkvarianten. |
| `nb-no`                     | Behållar avbildning med `nb-NO` språkvarianten. |
| `nl-nl`                     | Behållar avbildning med `nl-NL` språkvarianten. |
| `pl-pl`                     | Behållar avbildning med `pl-PL` språkvarianten. |
| `pt-br`                     | Behållar avbildning med `pt-BR` språkvarianten. |
| `pt-pt`                     | Behållar avbildning med `pt-PT` språkvarianten. |
| `ru-ru`                     | Behållar avbildning med `ru-RU` språkvarianten. |
| `sv-se`                     | Behållar avbildning med `sv-SE` språkvarianten. |
| `ta-in`                     | Behållar avbildning med `ta-IN` språkvarianten. |
| `te-in`                     | Behållar avbildning med `te-IN` språkvarianten. |
| `th-th`                     | Behållar avbildning med `th-TH` språkvarianten. |
| `tr-tr`                     | Behållar avbildning med `tr-TR` språkvarianten. |
| `zh-cn`                     | Behållar avbildning med `zh-CN` språkvarianten. |
| `zh-hk`                     | Behållar avbildning med `zh-HK` språkvarianten. |
| `zh-tw`                     | Behållar avbildning med `zh-TW` språkvarianten. |

---

## <a name="text-to-speech"></a>Text till tal

Det går att hitta [text till tal-][sp-tts] behållar avbildningen i `mcr.microsoft.com` behållar registrets syndikering. Den finns i `azure-cognitive-services/speechservices/` lagrings platsen och får namnet `text-to-speech` . Det fullständigt kvalificerade namnet på behållar avbildningen är, `mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech` .

Den här behållar avbildningen har följande Taggar tillgängliga. Du kan också hitta en fullständig lista över [taggar i MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/text-to-speech/tags/list).


# <a name="latest-version"></a>[Senaste version](#tab/current)

Versions anmärkning för `1.11.0-amd64-<locale-and-voice>` :

**Funktion**
* Mer fel information om problem vid hämtning av anpassade modeller efter ID.

| Bildtaggar                                  | Kommentarer                                                                                                         |
|---------------------------------------------|:--------------------------------------------------------------------------------------------------------------|
| `latest`                                    | Behållar avbildning med `en-US` språket och `en-US-AriaRUS` rösten.                                            | 
| `1.11.0-amd64-<locale-and-voice>`           | Ersätt `<locale>` med något av de tillgängliga språken i listan nedan. Till exempel `1.11.0-amd64-en-us-ariarus`. |

| Språk för v-1.11.0                         | Kommentarer                                                                      | Sammandrag                         |
|---------------------------------------------|:---------------------------------------------------------------------------|:-------------------------------|
| `ar-eg-hoda`                                | Behållar avbildning med `ar-EG` språket och `ar-EG-Hoda` rösten.            | `sha256:7ba558f444ea482eca87b3e850e9b416c71391282b26a590d1ee3d9a81350188` |
| `ar-sa-naayf`                               | Behållar avbildning med `ar-SA` språket och `ar-SA-Naayf` rösten.           | `sha256:7f0afcc205340dea7ffd959812dcba6a11448f6c5c1ab55c1422a360bd876137` |
| `bg-bg-ivan`                                | Behållar avbildning med `bg-BG` språket och `bg-BG-Ivan` rösten.            | `sha256:fde80af0e2e8e49b49ddec5f1502a246cf308328738d6f572f0043e625673782` |
| `ca-es-herenarus`                           | Behållar avbildning med `ca-ES` språket och `ca-ES-HerenaRUS` rösten.       | `sha256:fb2b50b128aa84ad0cd05db2462337d316ff2d2d78f393c5a9dece588a80654e` |
| `cs-cz-jakub`                               | Behållar avbildning med `cs-CZ` språket och `cs-CZ-Jakub` rösten.           | `sha256:9dde22e5e2164bee77aaf9fe4e8fc141d9dfbe3c92c4b07da969d34aa14f7fd0` |
| `da-dk-hellerus`                            | Behållar avbildning med `da-DK` språket och `da-DK-HelleRUS` rösten.        | `sha256:4a756cd10ad21dcc2b1c7006ec961f7e267f6d2204d9ad4efd6d4730d67a4ccc` |
| `de-at-michael`                             | Behållar avbildning med `de-AT` språket och `de-AT-Michael` rösten.         | `sha256:9d531c162c4279830f99ef0d44a506a023a0137723aab3adff7a663043a1c576` |
| `de-ch-karsten`                             | Behållar avbildning med `de-CH` språket och `de-CH-Karsten` rösten.         | `sha256:353d07168b4a44fcc12a0239f5bf20e2d29365b9abe26b9b844fb6194e7c9bcc` |
| `de-de-heddarus`                            | Behållar avbildning med `de-DE` språket och `de-DE-Hedda` rösten.           | `sha256:d76ff817fc154ba0f5ce1abb93c5a0269fe5bf7b4feb3b3fe9fe8ffe6fd4fee4` |
| `de-de-hedda`                               | Behållar avbildning med `de-DE` språket och `de-DE-Hedda` rösten.           | `sha256:d76ff817fc154ba0f5ce1abb93c5a0269fe5bf7b4feb3b3fe9fe8ffe6fd4fee4` |
| `de-de-stefan-apollo`                       | Behållar avbildning med `de-DE` språket och `de-DE-Stefan-Apollo` rösten.   | `sha256:8e22964dc4b77c05f602f72b0e706a534a89a271c4d17b5117af122c34df9a18` |
| `el-gr-stefanos`                            | Behållar avbildning med `el-GR` språket och `el-GR-Stefanos` rösten.        | `sha256:fcd6288d5fd4ddfe3d3e65e860895f6f7a7e81216c7113f71e7b1b01eb501150` |
| `en-au-catherine`                           | Behållar avbildning med `en-AU` språket och `en-AU-Catherine` rösten.       | `sha256:e49a5ec17b696a3a73d10383d369a2ff88ccddb812898a2eedefe6e6a009ce5a` |
| `en-au-hayleyrus`                           | Behållar avbildning med `en-AU` språket och `en-AU-HayleyRUS` rösten.       | `sha256:b7fb06bd992982c7e2e71da217898da45b742aab08e901bfcef9c43acf546bc0` |
| `en-ca-heatherrus`                          | Behållar avbildning med `en-CA` språket och `en-CA-HeatherRUS` rösten.      | `sha256:efd7d85845ca597937b8cbea7724cf31797855e0de5f30d66984ab9bac688152` |
| `en-ca-linda`                               | Behållar avbildning med `en-CA` språket och `en-CA-Linda` rösten.           | `sha256:8211077d55b440dbb26e42db6322b35ef6ec88e8c2ec6647831e0046668ed8a4` |
| `en-gb-george-apollo`                       | Behållar avbildning med `en-GB` språket och `en-GB-George-Apollo` rösten.   | `sha256:f6e924720b71d8f9a1edd4f5f2280e9054263eb79ce5364e03c9b802ad92f2dd` |
| `en-gb-hazelrus`                            | Behållar avbildning med `en-GB` språket och `en-GB-HazelRUS` rösten.        | `sha256:de702f70c53e4c1647e5fdd3432d37dc8972e069fcc103a1fc2b0be70f0d6d71` |
| `en-gb-susan-apollo`                        | Behållar avbildning med `en-GB` språket och `en-GB-Susan-Apollo` rösten.    | `sha256:5077cb575ffeb64e3d70184a68259438821891f6c9865350d2f887ea43ee99c1` |
| `en-ie-sean`                                | Behållar avbildning med `en-IE` språket och `en-IE-Sean` rösten.            | `sha256:c6f734cc12f04697a4d9b2003c46c5a4efd8c68da90838debb5628d9f8e70104` |
| `en-in-heera-apollo`                        | Behållar avbildning med `en-IN` språket och `en-IN-Heera-Apollo` rösten.    | `sha256:f5a78e857bc1563cbcd74f7b856bc2e4bd981675b397aeccfa134137f1cd3392` |
| `en-in-priyarus`                            | Behållar avbildning med `en-IN` språket och `en-IN-PriyaRUS` rösten.        | `sha256:667729cafd6bf5afe071a0a2989f836943e3bb6d3d1ebe35b7fab9bb311bfebc` |
| `en-in-ravi-apollo`                         | Behållar avbildning med `en-IN` språket och `en-IN-Ravi-Apollo` rösten.     | `sha256:e46533f972235f297dd31fd338638f5117e3f04fa4a434d678d1cecc76db023b` |
| `en-us-aria24krus`                          | Behållar avbildning med `en-US` språket och `en-US-Aria24kRUS` rösten.      | `sha256:a8f881b60021468dbd96d9733606bd00f7f889ccb523d1773492a8301128e596` |
| `en-us-ariarus`                             | Behållar avbildning med `en-US` språket och `en-US-AriaRUS` rösten.         | `sha256:a8f881b60021468dbd96d9733606bd00f7f889ccb523d1773492a8301128e596` |
| `en-us-benjaminrus`                         | Behållar avbildning med `en-US` språket och `en-US-BenjaminRUS` rösten.     | `sha256:53ee105977b6440f1a7fe5088255a9c6e437c39b7c66e5cd4aba984a1667b25c` |
| `en-us-guy24krus`                           | Behållar avbildning med `en-US` språket och `en-US-Guy24kRUS` rösten.       | `sha256:537d2018f414b825aa9995d2e15e0bdb0119e45f2c6fc10d326e3df6f49ef713` |
| `en-us-zirarus`                             | Behållar avbildning med `en-US` språket och `en-US-ZiraRUS` rösten.         | `sha256:05da3347d457ca040cbe9b3e3d586d298a844f906b34ef7b6d768c247274ff1f` |
| `es-es-helenarus`                           | Behållar avbildning med `es-ES` språket och `es-ES-HelenaRUS` rösten.       | `sha256:481cc43ba896a0d3291903af84120fa618130e2a2c8dce9b0ef23172b66858a8` |
| `es-es-laura-apollo`                        | Behållar avbildning med `es-ES` språket och `es-ES-Laura-Apollo` rösten.    | `sha256:8cb9d071a1e01dc3e63d5f1b1c040aa6fee94488a5bbd60f2c91704abfd921cc` |
| `es-es-pablo-apollo`                        | Behållar avbildning med `es-ES` språket och `es-ES-Pablo-Apollo` rösten.    | `sha256:da293ff5c49435c020044614962382040f41b6339ec83677301921a6dabbafb7` |
| `es-mx-hildarus`                            | Behållar avbildning med `es-MX` språket och `es-MX-HildaRUS` rösten.        | `sha256:9677d5bbbbe0c73df93948d4ecf3f367830ef9e7cfb3b42557cf94ec514b6c68` |
| `es-mx-raul-apollo`                         | Behållar avbildning med `es-MX` språket och `es-MX-Raul-Apollo` rösten.     | `sha256:a5109a6a659aa321892d4c6844e102ac72990fc2d58f32e45a072b291849fee8` |
| `fi-fi-heidirus`                            | Behållar avbildning med `fi-FI` språket och `fi-FI-HeidiRUS` rösten.        | `sha256:f8f1aa8168660ee1c21dfa4a92530bcba6f1aeb765cee9087a6cc29d7c332a8a` |
| `fr-ca-caroline`                            | Behållar avbildning med `fr-CA` språket och `fr-CA-Caroline` rösten.        | `sha256:450f0f75f26299a89a80efc3ce93b42d6447a32022aaf4f88edc935e56100191` |
| `fr-ca-harmonierus`                         | Behållar avbildning med `fr-CA` språket och `fr-CA-HarmonieRUS` rösten.     | `sha256:7b18adf90e6db8f8e2c5955f38aa0adfbdbd10a9a95e2cf13035b9c5416000e8` |
| `fr-ch-guillaume`                           | Behållar avbildning med `fr-CH` språket och `fr-CH-Guillaume` rösten.       | `sha256:ec3c238d0bfc3d26f20349ade1c4e19805b796f4bb3d5bf1fe4a9801b1ea1471` |
| `fr-fr-hortenserus`                         | Behållar avbildning med `fr-FR` språket och `fr-FR-HortenseRUS` rösten.     | `sha256:7b13613a9c5260e03ed831c79e5538633b4201867068ca0e1624b2c39fa8cf39` |
| `fr-fr-julie-apollo`                        | Behållar avbildning med `fr-FR` språket och `fr-FR-Julie-Apollo` rösten.    | `sha256:162c777447e3077438865332ac34df956be43c0429ce9962bcf5df9b210dbf01` |
| `fr-fr-paul-apollo`                         | Behållar avbildning med `fr-FR` språket och `fr-FR-Paul-Apollo` rösten.     | `sha256:8cdf28dc31d40a69eb6720fd42b8c19792f973c4e58760abbb6573c6129c81c1` |
| `he-il-asaf`                                | Behållar avbildning med `he-IL` språket och `he-IL-Asaf` rösten.            | `sha256:3f9ec9201deca21f5e3e561d6dd673ee6fb2a7f13b4cae2985ffb69622994b99` |
| `hi-in-hemant`                              | Behållar avbildning med `hi-IN` språket och `hi-IN-Hemant` rösten.          | `sha256:c6de645816587116384ada93c02257f257a13a4b696e1bd8aeecebb9a9668f15` |
| `hi-in-kalpana-apollo`                      | Behållar avbildning med `hi-IN` språket och `hi-IN-Kalpana-Apollo` rösten.  | `sha256:455ab4c9bc7c2457e2e48265065789a54513e07a1dc9e4bc108651f118f1570d` |
| `hi-in-kalpana`                             | Behållar avbildning med `hi-IN` språket och `hi-IN-Kalpana` rösten.         | `sha256:455ab4c9bc7c2457e2e48265065789a54513e07a1dc9e4bc108651f118f1570d` |
| `hr-hr-matej`                               | Behållar avbildning med `hr-HR` språket och `hr-HR-Matej` rösten.           | `sha256:6ac24252194f91cd815736bd8be03fb95e0b965fabed5de4c631e99cd917da97` |
| `hu-hu-szabolcs`                            | Behållar avbildning med `hu-HU` språket och `hu-HU-Szabolcs` rösten.        | `sha256:bf20ea91d922beb682e321a31cabb11ebec474f47edcf4e3787882e2a204b3b5` |
| `id-id-andika`                              | Behållar avbildning med `id-ID` språket och `id-ID-Andika` rösten.          | `sha256:859bef31e5d882b508154ec00632e5e1e95bc8ea2dde6198f157703d759746c7` |
| `it-it-cosimo-apollo`                       | Behållar avbildning med `it-IT` språket och `it-IT-Cosimo-Apollo` rösten.   | `sha256:b6c81ab4bd0aba217977b0bd83a8a65f7c09b5954cda0870dea15aec0dbbe1ed` |
| `it-it-luciarus`                            | Behållar avbildning med `it-IT` språket och `it-IT-LuciaRUS` rösten.        | `sha256:e216a1390a0d4d9f111c56c1d655f36614947eea18d6ec91a9f6d050048b1ad4` |
| `ja-jp-ayumi-apollo`                        | Behållar avbildning med `ja-JP` språket och `ja-JP-Ayumi-Apollo` rösten.    | `sha256:ba2042523ea1fff9d2c8b805ac36075169c3aecce0c965d09e326c06eab5a36f` |
| `ja-jp-harukarus`                           | Behållar avbildning med `ja-JP` språket och `ja-JP-HarukaRUS` rösten.       | `sha256:fdbc8f59fc1c4b52c11d248ee9a5d7fe4e58343f036e558fbb33282e24d5b71f` |
| `ja-jp-ichiro-apollo`                       | Behållar avbildning med `ja-JP` språket och `ja-JP-Ichiro-Apollo` rösten.   | `sha256:08ea0ed61ac152dc5caea2d4cacc81175c272cb4a835eecaa7f8e7c5485740b7` |
| `ko-kr-heamirus`                            | Behållar avbildning med `ko-KR` språket och `ko-KR-HeamiRUS` rösten.        | `sha256:40ff95e5fb92278e369b4f37d7dbb109431ecb115b1b9516aa887e6bb4fd030b` |
| `ms-my-rizwan`                              | Behållar avbildning med `ms-MY` språket och `ms-MY-Rizwan` rösten.          | `sha256:70cfe68a81ee860136cfaed35909f522c28c20ef5514c2d9d96c283892f8b7f5` |
| `nb-no-huldarus`                            | Behållar avbildning med `nb-NO` språket och `nb-NO-HuldaRUS` rösten.        | `sha256:9941cda0e65884900532e6a0ba68e475f373277105594bf09e67225450192d3c` |
| `nl-nl-hannarus`                            | Behållar avbildning med `nl-NL` språket och `nl-NL-HannaRUS` rösten.        | `sha256:c71d980dfc70575421d1589c74e8b3e7cc036551412d0ad0f89dbc543252a405` |
| `pl-pl-paulinarus`                          | Behållar avbildning med `pl-PL` språket och `pl-PL-PaulinaRUS` rösten.      | `sha256:e5fbd98a70eb1dcf80c446b48b8f17e47ac12853bb255f0aed174c78196de257` |
| `pt-br-daniel-apollo`                       | Behållar avbildning med `pt-BR` språket och `pt-BR-Daniel-Apollo` rösten.   | `sha256:9f57f9847f2372fa341cf037410ac68ada1c3075ab9b77cffbcf01d199f7c1f5` |
| `pt-br-heloisarus`                          | Behållar avbildning med `pt-BR` språket och `pt-BR-HeloisaRUS` rösten.      | `sha256:ef546532c582392e6ed47df55c0fbfa6dca6d3e523547089263b57354a4efb1a` |
| `pt-pt-heliarus`                            | Behållar avbildning med `pt-PT` språket och `pt-PT-HeliaRUS` rösten.        | `sha256:116aefb76ddf39bed379c023c8260d2607314ad1b31ddef83ec2818ad9805a0b` |
| `ro-ro-andrei`                              | Behållar avbildning med `ro-RO` språket och `ro-RO-Andrei` rösten.          | `sha256:6968fdefdd798adab48faeb40857c8cdca55712dbf4806703e11ccdfab874051` |
| `ru-ru-ekaterinarus`                        | Behållar avbildning med `ru-RU` språket och `ru-RU-EkaterinaRUS` rösten.    | `sha256:48add20e3c147fb4be26c948841a12736c8b10d053aa7d25984df8e4016e939f` |
| `ru-ru-irina-apollo`                        | Behållar avbildning med `ru-RU` språket och `ru-RU-Irina-Apollo` rösten.    | `sha256:ce5c055aedb3f9323f41a9de8d8f3dd23fb2ad0621d499f914f5cb3856e995f3` |
| `ru-ru-pavel-apollo`                        | Behållar avbildning med `ru-RU` språket och `ru-RU-Pavel-Apollo` rösten.    | `sha256:badc02f9ccdee13ab7dbd4e178bd5c57d332cc3acd2d4a9a3f889d317e0517be` |
| `sk-sk-filip`                               | Behållar avbildning med `sk-SK` språket och `sk-SK-Filip` rösten.           | `sha256:763d4fe74b6f04a976482880eed76175854f659bb5bfcb315dce8ef69acead2e` |
| `sl-si-lado`                                | Behållar avbildning med `sl-SI` språket och `sl-SI-Lado` rösten.            | `sha256:73374363f9b69e03b8b9de34b319d7797876a3dae40bdce0830a67cf4bb4d4f2` |
| `sv-se-hedvigrus`                           | Behållar avbildning med `sv-SE` språket och `sv-SE-HedvigRUS` rösten.       | `sha256:317d6b5d69f56c9087cd1e8004e60a48841b997937dcdccc97e7c0b2e2ffb631` |
| `ta-in-valluvar`                            | Behållar avbildning med `ta-IN` språket och `ta-IN-Valluvar` rösten.        | `sha256:d1aaad1d5f32a910e245e6c117178c0703d39035e4053fe2dd2bb646fc02f7b8` |
| `te-in-chitra`                              | Behållar avbildning med `te-IN` språket och `te-IN-Chitra` rösten.          | `sha256:0224ac3b2de11c4f6ef65ce0bdcd1b9c4112ea472b3bd5626fdff47a5185f54c` |
| `th-th-pattara`                             | Behållar avbildning med `th-TH` språket och `th-TH-Pattara` rösten.         | `sha256:16c7384bfe210f30e09eae3542a58ff9bdbfa9253fdf4d380a53b37809f82c7d` |
| `tr-tr-sedarus`                             | Behållar avbildning med `tr-TR` språket och `tr-TR-SedaRUS` rösten.         | `sha256:5c7786c00a66346438ee4065e3eaa03ef9f8323ba839068344492b8a3b6d997a` |
| `vi-vn-an`                                  | Behållar avbildning med `vi-VN` språket och `vi-VN-An` rösten.              | `sha256:6925744597c45eed8761a9597f3525f435dd420b67ff775a73211fdef9cd9cb2` |
| `zh-cn-huihuirus`                           | Behållar avbildning med `zh-CN` språket och `zh-CN-HuihuiRUS` rösten.       | `sha256:b38a3f465062853b171d2bce6c6d8afa14d223e24bfd5ea0827e34c26a09a2c8` |
| `zh-cn-kangkang-apollo`                     | Behållar avbildning med `zh-CN` språket och `zh-CN-Kangkang-Apollo` rösten. | `sha256:fa9555e2f520340457d5cebe469af40516237fb9398a5f90046565655b2862f8` |
| `zh-cn-yaoyao-apollo`                       | Behållar avbildning med `zh-CN` språket och `zh-CN-Yaoyao-Apollo` rösten.   | `sha256:d7eeca43e45d09a1c22611f865fb1f8b42673688a11a2acffd37a4e08a7fd8c4` |
| `zh-hk-danny-apollo`                        | Behållar avbildning med `zh-HK` språket och `zh-HK-Danny-Apollo` rösten.    | `sha256:ee7257c0179fbe015324b4d29f16fe93964e5f1901906240477fb1d820a500f2` |
| `zh-hk-tracy-apollo`                        | Behållar avbildning med `zh-HK` språket och `zh-HK-Tracy-Apollo` rösten.    | `sha256:dfa4effbf7d0ec6c9130c142241b3e247e226e13dc218fd44f986ca1c7fff2ed` |
| `zh-hk-tracyrus`                            | Behållar avbildning med `zh-HK` språket och `zh-HK-TracyRUS` rösten.        | `sha256:dfa4effbf7d0ec6c9130c142241b3e247e226e13dc218fd44f986ca1c7fff2ed` |
| `zh-tw-hanhanrus`                           | Behållar avbildning med `zh-TW` språket och `zh-TW-HanHanRUS` rösten.       | `sha256:263153fd6e05970e04af9a9bd95fb13591f0138ac030a632a6a78d95936afa4b` |
| `zh-tw-yating-apollo`                       | Behållar avbildning med `zh-TW` språket och `zh-TW-Yating-Apollo` rösten.   | `sha256:b8289bb550b9328d83d6a7ec93bdf9524087222f537a55db0b2eb5402c2bf663` |
| `zh-tw-zhiwei-apollo`                       | Behållar avbildning med `zh-TW` språket och `zh-TW-Zhiwei-Apollo` rösten.   | `sha256:af4bc0ef2211f69a92541bb14596341375e1003aef541aefcea7843192046b4c` |


# <a name="previous-version"></a>[Tidigare version](#tab/previous)

Versions anmärkning för `1.9.0-amd64-<locale-and-voice>` :

* Vanlig månatlig utgåva

Versions anmärkning för `1.8.0-amd64-<locale-and-voice>` :

**Funktion**

* Fullständigt migrerat till .NET 3,1

Versions anmärkning för `1.7.0-amd64-<locale-and-voice>` :

**Funktion**

* Uppgraderade komponenter till .NET 3,1

| Bildtaggar                                  | Kommentarer                                                                                                         |
|---------------------------------------------|:--------------------------------------------------------------------------------------------------------------|
| `1.9.0-amd64-<locale-and-voice>`            | Ersätt `<locale>` med något av de tillgängliga språken i listan nedan. Till exempel `1.9.0-amd64-en-us-ariarus`.  |
| `1.8.0-amd64-<locale-and-voice>`            | Ersätt `<locale>` med något av de tillgängliga språken i listan nedan. Till exempel `1.8.0-amd64-en-us-ariarus`.  |
| `1.7.0-amd64-<locale-and-voice>`            | första GA-versionen. Ersätt `<locale>` med något av de tillgängliga språken i listan nedan. Till exempel `1.7.0-amd64-en-us-ariarus`.  |

| Språk för v-1.9.0                          | Kommentarer                                                                      | Sammandrag                         |
|---------------------------------------------|:---------------------------------------------------------------------------|:-------------------------------|
| `ar-eg-hoda`                                | Behållar avbildning med `ar-EG` språket och `ar-EG-Hoda` rösten.            | `sha256:2b19cfd2212d6517b286aa18617d2f9d1dd1520078b559cbbf9240599270d10` | 
| `ar-sa-naayf`                               | Behållar avbildning med `ar-SA` språket och `ar-SA-Naayf` rösten.           | `sha256:6063aae5fb15c62b234cf945220916516a06ca81354c5311dee02af4d8cb0d3` |
| `bg-bg-ivan`                                | Behållar avbildning med `bg-BG` språket och `bg-BG-Ivan` rösten.            | `sha256:c6786916464755e64ffa64e69e8f3e7ef16115bac00bb6ea1e45368c42c58d1` |
| `ca-es-herenarus`                           | Behållar avbildning med `ca-ES` språket och `ca-ES-HerenaRUS` rösten.       | `sha256:2a8a1accbf99e2746c9345b77e2f261e0111227312c402cc2e1cd8760cdc82a` |
| `cs-cz-jakub`                               | Behållar avbildning med `cs-CZ` språket och `cs-CZ-Jakub` rösten.           | `sha256:3e464356bb08c9c966af2b28a88ccafd591aecd2e37a0fedb356bd443720e8d` |
| `da-dk-hellerus`                            | Behållar avbildning med `da-DK` språket och `da-DK-HelleRUS` rösten.        | `sha256:b85c43080804103673ff99dddea644a516c4103e8b1f11fa3dd34857492cd40` |
| `de-at-michael`                             | Behållar avbildning med `de-AT` språket och `de-AT-Michael` rösten.         | `sha256:87b57ee61f964e4d72e75d860c499fa3b3d8dbda6a96c97d696beb20aa8b2a9` |
| `de-ch-karsten`                             | Behållar avbildning med `de-CH` språket och `de-CH-Karsten` rösten.         | `sha256:ab1385b9746f4f054204302b9d564a433ae03748021b8ed71b4a3a224af1e9b` |
| `de-de-heddarus`                            | Behållar avbildning med `de-DE` språket och `de-DE-Hedda` rösten.           | `sha256:82185a710c87f9dde678d88036867559ab3bf5f08f234d60d1548d3e106db57` |
| `de-de-hedda`                               | Behållar avbildning med `de-DE` språket och `de-DE-Hedda` rösten.           | `sha256:82185a710c87f9dde678d88036867559ab3bf5f08f234d60d1548d3e106db57` |
| `de-de-stefan-apollo`                       | Behållar avbildning med `de-DE` språket och `de-DE-Stefan-Apollo` rösten.   | `sha256:56a1c63e7e6a0f5623ddc1f6a44ac6e51471d073e02e14e8c8b1e577930d816` |
| `el-gr-stefanos`                            | Behållar avbildning med `el-GR` språket och `el-GR-Stefanos` rösten.        | `sha256:ccbbb09f29ff8f276e246037183c7a3e9a3eb5bf33a942b22205cce3c6857f2` |
| `en-au-catherine`                           | Behållar avbildning med `en-AU` språket och `en-AU-Catherine` rösten.       | `sha256:0c7374890f963e1ae9507e89dc9965a94723bd57802826c0677cd5262189783` |
| `en-au-hayleyrus`                           | Behållar avbildning med `en-AU` språket och `en-AU-HayleyRUS` rösten.       | `sha256:7430bf8eace8294ca085f36ea56399261b2b4f69027e86649e8f3868fc3d811` |
| `en-ca-heatherrus`                          | Behållar avbildning med `en-CA` språket och `en-CA-HeatherRUS` rösten.      | `sha256:0166ce1de3d669ea4ad80738c63369b7032125a54ecabade07241d740a94cfe` |
| `en-ca-linda`                               | Behållar avbildning med `en-CA` språket och `en-CA-Linda` rösten.           | `sha256:50bed6a7bde9b793d307bcc3ace4c0f28d4a33c7a4dad9b3a394dc39a3e1c28` |
| `en-gb-george-apollo`                       | Behållar avbildning med `en-GB` språket och `en-GB-George-Apollo` rösten.   | `sha256:50b800c0018a39609ddb1cee1b10062bf38a907644c393d20786db7c3ade748` |
| `en-gb-hazelrus`                            | Behållar avbildning med `en-GB` språket och `en-GB-HazelRUS` rösten.        | `sha256:2aa79394dfeac8cec0cc1704a5199949cfccf347fe61161d02c7000c4ffcfa6` |
| `en-gb-susan-apollo`                        | Behållar avbildning med `en-GB` språket och `en-GB-Susan-Apollo` rösten.    | `sha256:7a3174b3aae5f10241e731d392b56f124808cdd506f881ced919ced73d836c0` |
| `en-ie-sean`                                | Behållar avbildning med `en-IE` språket och `en-IE-Sean` rösten.            | `sha256:2457202fadb2354fc8d3666432096bd87c07760a4e3f4dbcc49853fff658577` |
| `en-in-heera-apollo`                        | Behållar avbildning med `en-IN` språket och `en-IN-Heera-Apollo` rösten.    | `sha256:e4068cd7ca4272ea94819e2ba8743d2a76c8710b162db5e9ecbde6c92c12877` |
| `en-in-priyarus`                            | Behållar avbildning med `en-IN` språket och `en-IN-PriyaRUS` rösten.        | `sha256:9d63a0ed53ac06178ab84588551421c0e1d04b8bad3321410ebb99c3ca2a9e8` |
| `en-in-ravi-apollo`                         | Behållar avbildning med `en-IN` språket och `en-IN-Ravi-Apollo` rösten.     | `sha256:67049c9ce591336655943f5030afcfdaa150a8aace7b372425a69cc33a6b7b9` |
| `en-us-aria24krus`                          | Behållar avbildning med `en-US` språket och `en-US-Aria24kRUS` rösten.      | `sha256:a95acf6874bf3df7ae8e96be779f80cb5405d21250227b0c4b3ddbcb3014082` |
| `en-us-ariarus`                             | Behållar avbildning med `en-US` språket och `en-US-AriaRUS` rösten.         | `sha256:a95acf6874bf3df7ae8e96be779f80cb5405d21250227b0c4b3ddbcb3014082` |
| `en-us-benjaminrus`                         | Behållar avbildning med `en-US` språket och `en-US-BenjaminRUS` rösten.     | `sha256:93cd49adaaa2a1bdfb06ab655be164ae66f206cb7c03a2cbd59e5fba70610ab` |
| `en-us-guy24krus`                           | Behållar avbildning med `en-US` språket och `en-US-Guy24kRUS` rösten.       | `sha256:7b788bfcaae4c63c274ca15924bfd861cfcafd5fec13f685d80babc25b2949d` |
| `en-us-zirarus`                             | Behållar avbildning med `en-US` språket och `en-US-ZiraRUS` rösten.         | `sha256:bfc87a77df5695ad43481348500fba8f6a7b495708fba200706049469b5ba97` |
| `es-es-helenarus`                           | Behållar avbildning med `es-ES` språket och `es-ES-HelenaRUS` rösten.       | `sha256:0b6c17aca75efb64aa9bfc0d83303038fe58d4b2fb1fc94c9380a4335b80796` |
| `es-es-laura-apollo`                        | Behållar avbildning med `es-ES` språket och `es-ES-Laura-Apollo` rösten.    | `sha256:d6fcffc944c37a2dd0de29c39b82f3f8cce3a95ad925d2814ed7538335d5d4f` |
| `es-es-pablo-apollo`                        | Behållar avbildning med `es-ES` språket och `es-ES-Pablo-Apollo` rösten.    | `sha256:a460bc53d9083d3c3770129995cf96cc1069ae4e8101f1739d304fe210f0af0` |
| `es-mx-hildarus`                            | Behållar avbildning med `es-MX` språket och `es-MX-HildaRUS` rösten.        | `sha256:5b7578fc5b00158dfa674d95a3f1d57f22eb285e8333b4006d1fe1808bda7ba` |
| `es-mx-raul-apollo`                         | Behållar avbildning med `es-MX` språket och `es-MX-Raul-Apollo` rösten.     | `sha256:03922fb017783c86d788c72e01c7ede440f8f3c913c86cab19bad4dfc2e4a2b` |
| `fi-fi-heidirus`                            | Behållar avbildning med `fi-FI` språket och `fi-FI-HeidiRUS` rösten.        | `sha256:146c1f98d6fa061016eba41db6e7b654eef222d37f35406d4b43477bb2ff897` |
| `fr-ca-caroline`                            | Behållar avbildning med `fr-CA` språket och `fr-CA-Caroline` rösten.        | `sha256:1ee2e53f12ad1c72665d2aef64e9d4a7f9ea05670cad84dcae5e75409494f32` |
| `fr-ca-harmonierus`                         | Behållar avbildning med `fr-CA` språket och `fr-CA-HarmonieRUS` rösten.     | `sha256:a21d25d3ac699af4e9ba9194aadd9b45f35fd9205224f3429a4c7da41fc38fe` |
| `fr-ch-guillaume`                           | Behållar avbildning med `fr-CH` språket och `fr-CH-Guillaume` rösten.       | `sha256:216125a9bd89a95d3c4dc2d7e031398659427b3aa7d4663d23a65737972e42b` |
| `fr-fr-hortenserus`                         | Behållar avbildning med `fr-FR` språket och `fr-FR-HortenseRUS` rösten.     | `sha256:795a698120eecbd80c48e738f73300739c1698ca859130ddb4236317bcdf70f` |
| `fr-fr-julie-apollo`                        | Behållar avbildning med `fr-FR` språket och `fr-FR-Julie-Apollo` rösten.    | `sha256:f6eb70d523c435c2e3a713b32a8af4a781df7ec043caad2fc7f458ee341eb2f` |
| `fr-fr-paul-apollo`                         | Behållar avbildning med `fr-FR` språket och `fr-FR-Paul-Apollo` rösten.     | `sha256:28864c662a20f459b3051b1da2967a605e06267e6408285f7c2552748cf4eed` |
| `he-il-asaf`                                | Behållar avbildning med `he-IL` språket och `he-IL-Asaf` rösten.            | `sha256:eaa834bac6b69abef096b36a8baead741db78fe438af3d30f60abde3631d639` |
| `hi-in-hemant`                              | Behållar avbildning med `hi-IN` språket och `hi-IN-Hemant` rösten.          | `sha256:cfea0fa7cce9cc512f2fbb8b76f1c00fe5c32fad853c90b15934cf4ee6262fa` |
| `hi-in-kalpana-apollo`                      | Behållar avbildning med `hi-IN` språket och `hi-IN-Kalpana-Apollo` rösten.  | `sha256:afbd6cc0413f3a3c9f6df044b6df6d9dac9e8e888c2cb619fefbdc3e105c644` |
| `hi-in-kalpana`                             | Behållar avbildning med `hi-IN` språket och `hi-IN-Kalpana` rösten.         | `sha256:afbd6cc0413f3a3c9f6df044b6df6d9dac9e8e888c2cb619fefbdc3e105c644` |
| `hr-hr-matej`                               | Behållar avbildning med `hr-HR` språket och `hr-HR-Matej` rösten.           | `sha256:86683597c62752b4d769b69e5294979fafd4c277aaef1536e1cb19f9f06c0bf` |
| `hu-hu-szabolcs`                            | Behållar avbildning med `hu-HU` språket och `hu-HU-Szabolcs` rösten.        | `sha256:aa64eed28ca2ad060e2e02188e0401bf34e4caf7e2182b70a30ce33b3c11c9c` |
| `id-id-andika`                              | Behållar avbildning med `id-ID` språket och `id-ID-Andika` rösten.          | `sha256:0e1394d231a57a1df8163ccb634dc2ef2f8103b10608a40ab3efc5c0fbe9ded` |
| `it-it-cosimo-apollo`                       | Behållar avbildning med `it-IT` språket och `it-IT-Cosimo-Apollo` rösten.   | `sha256:eef97f2817fc24405823a5fe4e825244db32279b44c0e6631e8ad9a5c1acf40` |
| `it-it-luciarus`                            | Behållar avbildning med `it-IT` språket och `it-IT-LuciaRUS` rösten.        | `sha256:ebc331b0685f482d2f55619fa81fd451fd7c8f107f9cd7ad159bc6213ae4e33` |
| `ja-jp-ayumi-apollo`                        | Behållar avbildning med `ja-JP` språket och `ja-JP-Ayumi-Apollo` rösten.    | `sha256:e9cb7dfd2eec154c8f3d530c16b66e8558c5955a2edaede69740067f00e43cf` |
| `ja-jp-harukarus`                           | Behållar avbildning med `ja-JP` språket och `ja-JP-HarukaRUS` rösten.       | `sha256:93ce2ef6177c0d8ac70b61df8b11fcbcdfd3c0be0cc51cd8644f26679a741c2` |
| `ja-jp-ichiro-apollo`                       | Behållar avbildning med `ja-JP` språket och `ja-JP-Ichiro-Apollo` rösten.   | `sha256:6a18bae69ac63b42ba992b8b74d8d31d91ca984d61b5f62f38be988cf38645e` |
| `ko-kr-heamirus`                            | Behållar avbildning med `ko-KR` språket och `ko-KR-HeamiRUS` rösten.        | `sha256:7a48252d4ada2af43f9266a70113426d330bac192348cbdc929022295a0e727` |
| `ms-my-rizwan`                              | Behållar avbildning med `ms-MY` språket och `ms-MY-Rizwan` rösten.          | `sha256:90e2ecac14f8e960934fd013d208fc2a0afe1bfff037d5648d422bda8d8a76e` |
| `nb-no-huldarus`                            | Behållar avbildning med `nb-NO` språket och `nb-NO-HuldaRUS` rösten.        | `sha256:217b61bd6244b5effda8f12a2c563ce1b4572e9c5b8a08df143665f9ff754e4` |
| `nl-nl-hannarus`                            | Behållar avbildning med `nl-NL` språket och `nl-NL-HannaRUS` rösten.        | `sha256:fbff48dfc9dfadadf377867b28f6e3a3bd605e59da20f77a531efcc7d85d16e` |
| `pl-pl-paulinarus`                          | Behållar avbildning med `pl-PL` språket och `pl-PL-PaulinaRUS` rösten.      | `sha256:856a033a09925773fa4b4531e199ab7c03c537f366acecbda60f8d21735725e` |
| `pt-br-daniel-apollo`                       | Behållar avbildning med `pt-BR` språket och `pt-BR-Daniel-Apollo` rösten.   | `sha256:2d1ec975f1aee56a6fc6039d154fb3f2fbeb4636f7078c5dfe99aeddb6a3634` |
| `pt-br-heloisarus`                          | Behållar avbildning med `pt-BR` språket och `pt-BR-HeloisaRUS` rösten.      | `sha256:b7d629f37ab3305274764264dc08fab5236e60ef18d40e987618115db67ce44` |
| `pt-pt-heliarus`                            | Behållar avbildning med `pt-PT` språket och `pt-PT-HeliaRUS` rösten.        | `sha256:8b380ae7e4aac9d4ada4d15fa9e667387bc9ca038796d9b6999953bfbc97259` |
| `ro-ro-andrei`                              | Behållar avbildning med `ro-RO` språket och `ro-RO-Andrei` rösten.          | `sha256:b00ca7f1411169a5baf7263a8d7e5eed1a72084d9489eaf458429dfc338564a` |
| `ru-ru-ekaterinarus`                        | Behållar avbildning med `ru-RU` språket och `ru-RU-EkaterinaRUS` rösten.    | `sha256:31c588c31e3ac67305af66091e7756dfc4ca454317d0228116ea0b2fedf5d71` |
| `ru-ru-irina-apollo`                        | Behållar avbildning med `ru-RU` språket och `ru-RU-Irina-Apollo` rösten.    | `sha256:e76437f8da7c279b38d2643defc997a13b4a364e9a212895cdb33a9a3f6457f` |
| `ru-ru-pavel-apollo`                        | Behållar avbildning med `ru-RU` språket och `ru-RU-Pavel-Apollo` rösten.    | `sha256:461c1efa6cce0b10a87f338bc637aca76aef8458061a688870fb3343d682da0` |
| `sk-sk-filip`                               | Behållar avbildning med `sk-SK` språket och `sk-SK-Filip` rösten.           | `sha256:7fb0cfab4c0fe2913eb20f28a25c6663015d62f82e7e7864d9f7fac2d27697b` |
| `sl-si-lado`                                | Behållar avbildning med `sl-SI` språket och `sl-SI-Lado` rösten.            | `sha256:5336173d410e10ffeb5dc211a583887e33754319c757914955057d398dfbb0a` |
| `sv-se-hedvigrus`                           | Behållar avbildning med `sv-SE` språket och `sv-SE-HedvigRUS` rösten.       | `sha256:5dc8cdcc3054386bf69596707d9d261d4db5bfd09f1882ceb4e29238a34b24e` |
| `ta-in-valluvar`                            | Behållar avbildning med `ta-IN` språket och `ta-IN-Valluvar` rösten.        | `sha256:74ea485f23e4c1fe0029e06894860aa0188c36c0e14ea3584a06d4216ccef56` |
| `te-in-chitra`                              | Behållar avbildning med `te-IN` språket och `te-IN-Chitra` rösten.          | `sha256:ff2977a98ef691da543db08be9cfe04d7fc3bf8f78b29310c163e47303b2ddd` |
| `th-th-pattara`                             | Behållar avbildning med `th-TH` språket och `th-TH-Pattara` rösten.         | `sha256:ba7e2c0e5e75d9f2b52aa50c97728616c43e81f48c15e24665e4c2ea5770a8f` |
| `tr-tr-sedarus`                             | Behållar avbildning med `tr-TR` språket och `tr-TR-SedaRUS` rösten.         | `sha256:375a8ceae89ea1f0dda551feff30ae3679231189b527992edbc49988d042d66` |
| `vi-vn-an`                                  | Behållar avbildning med `vi-VN` språket och `vi-VN-An` rösten.              | `sha256:b6f82148295b38b4039c45c48695ec50b4e97cd02b18d49c39bf9fca3bec958` |
| `zh-cn-huihuirus`                           | Behållar avbildning med `zh-CN` språket och `zh-CN-HuihuiRUS` rösten.       | `sha256:3e773931f3adaac92cba43773a241692a2b471ebe73ec51c475df8ff63b7ee1` |
| `zh-cn-kangkang-apollo`                     | Behållar avbildning med `zh-CN` språket och `zh-CN-Kangkang-Apollo` rösten. | `sha256:05fc0d5075a1094caf70d98b4a9469952be52cb6eb4d9f7b9ff4ae961100c7b` |
| `zh-cn-yaoyao-apollo`                       | Behållar avbildning med `zh-CN` språket och `zh-CN-Yaoyao-Apollo` rösten.   | `sha256:d7613bcefc48e85b9d6f07c8cd223c16d4958bcf7f24087575250e97c593ac1` |
| `zh-hk-danny-apollo`                        | Behållar avbildning med `zh-HK` språket och `zh-HK-Danny-Apollo` rösten.    | `sha256:efe22bc123dac9312dcaeb859a377d81f61fbb25ef46e4678d36ec6bebc5d32` |
| `zh-hk-tracy-apollo`                        | Behållar avbildning med `zh-HK` språket och `zh-HK-Tracy-Apollo` rösten.    | `sha256:802c60bc65012c03ffe96268dca79b8c6dcd0c5cc6180ec271c50ef5c9ba132` |
| `zh-hk-tracyrus`                            | Behållar avbildning med `zh-HK` språket och `zh-HK-TracyRUS` rösten.        | `sha256:802c60bc65012c03ffe96268dca79b8c6dcd0c5cc6180ec271c50ef5c9ba132` |
| `zh-tw-hanhanrus`                           | Behållar avbildning med `zh-TW` språket och `zh-TW-HanHanRUS` rösten.       | `sha256:95d58922463d577d4c4722ab722a5768af35fb62236d47f6709717dea758909` |
| `zh-tw-yating-apollo`                       | Behållar avbildning med `zh-TW` språket och `zh-TW-Yating-Apollo` rösten.   | `sha256:33eec6e3aaaedafaf3969746eeaf97a1760e763505decfe2abaa03f5054bfd2` |
| `zh-tw-zhiwei-apollo`                       | Behållar avbildning med `zh-TW` språket och `zh-TW-Zhiwei-Apollo` rösten.   | `sha256:456db2898b2e5a9c30b7071ce6ea3f141438cbf1aa4899c7ffccfc2f0dde5bd` |

| Språk för v-1.8.0                          | Kommentarer                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `ar-eg-hoda`                                | Behållar avbildning med `ar-EG` språket och `ar-EG-Hoda` rösten.            |
| `ar-sa-naayf`                               | Behållar avbildning med `ar-SA` språket och `ar-SA-Naayf` rösten.           |
| `bg-bg-ivan`                                | Behållar avbildning med `bg-BG` språket och `bg-BG-Ivan` rösten.            |
| `ca-es-herenarus`                           | Behållar avbildning med `ca-ES` språket och `ca-ES-HerenaRUS` rösten.       |
| `cs-cz-jakub`                               | Behållar avbildning med `cs-CZ` språket och `cs-CZ-Jakub` rösten.           |
| `da-dk-hellerus`                            | Behållar avbildning med `da-DK` språket och `da-DK-HelleRUS` rösten.        |
| `de-at-michael`                             | Behållar avbildning med `de-AT` språket och `de-AT-Michael` rösten.         |
| `de-ch-karsten`                             | Behållar avbildning med `de-CH` språket och `de-CH-Karsten` rösten.         |
| `de-de-hedda`                               | Behållar avbildning med `de-DE` språket och `de-DE-Hedda` rösten.           |
| `de-de-heddarus`                            | Behållar avbildning med `de-DE` språket och `de-DE-Hedda` rösten.           |
| `de-de-stefan-apollo`                       | Behållar avbildning med `de-DE` språket och `de-DE-Stefan-Apollo` rösten.   |
| `el-gr-stefanos`                            | Behållar avbildning med `el-GR` språket och `el-GR-Stefanos` rösten.        |
| `en-au-catherine`                           | Behållar avbildning med `en-AU` språket och `en-AU-Catherine` rösten.       |
| `en-au-hayleyrus`                           | Behållar avbildning med `en-AU` språket och `en-AU-HayleyRUS` rösten.       |
| `en-ca-heatherrus`                          | Behållar avbildning med `en-CA` språket och `en-CA-HeatherRUS` rösten.      |
| `en-ca-linda`                               | Behållar avbildning med `en-CA` språket och `en-CA-Linda` rösten.           |
| `en-gb-george-apollo`                       | Behållar avbildning med `en-GB` språket och `en-GB-George-Apollo` rösten.   |
| `en-gb-hazelrus`                            | Behållar avbildning med `en-GB` språket och `en-GB-HazelRUS` rösten.        |
| `en-gb-susan-apollo`                        | Behållar avbildning med `en-GB` språket och `en-GB-Susan-Apollo` rösten.    |
| `en-ie-sean`                                | Behållar avbildning med `en-IE` språket och `en-IE-Sean` rösten.            |
| `en-in-heera-apollo`                        | Behållar avbildning med `en-IN` språket och `en-IN-Heera-Apollo` rösten.    |
| `en-in-priyarus`                            | Behållar avbildning med `en-IN` språket och `en-IN-PriyaRUS` rösten.        |
| `en-in-ravi-apollo`                         | Behållar avbildning med `en-IN` språket och `en-IN-Ravi-Apollo` rösten.     |
| `en-us-benjaminrus`                         | Behållar avbildning med `en-US` språket och `en-US-BenjaminRUS` rösten.     |
| `en-us-guy24krus`                           | Behållar avbildning med `en-US` språket och `en-US-Guy24kRUS` rösten.       |
| `en-us-aria24krus`                          | Behållar avbildning med `en-US` språket och `en-US-Aria24kRUS` rösten.      |
| `en-us-ariarus`                             | Behållar avbildning med `en-US` språket och `en-US-AriaRUS` rösten.         |
| `en-us-zirarus`                             | Behållar avbildning med `en-US` språket och `en-US-ZiraRUS` rösten.         |
| `es-es-helenarus`                           | Behållar avbildning med `es-ES` språket och `es-ES-HelenaRUS` rösten.       |
| `es-es-laura-apollo`                        | Behållar avbildning med `es-ES` språket och `es-ES-Laura-Apollo` rösten.    |
| `es-es-pablo-apollo`                        | Behållar avbildning med `es-ES` språket och `es-ES-Pablo-Apollo` rösten.    |
| `es-mx-hildarus`                            | Behållar avbildning med `es-MX` språket och `es-MX-HildaRUS` rösten.        |
| `es-mx-raul-apollo`                         | Behållar avbildning med `es-MX` språket och `es-MX-Raul-Apollo` rösten.     |
| `fi-fi-heidirus`                            | Behållar avbildning med `fi-FI` språket och `fi-FI-HeidiRUS` rösten.        |
| `fr-ca-caroline`                            | Behållar avbildning med `fr-CA` språket och `fr-CA-Caroline` rösten.        |
| `fr-ca-harmonierus`                         | Behållar avbildning med `fr-CA` språket och `fr-CA-HarmonieRUS` rösten.     |
| `fr-ch-guillaume`                           | Behållar avbildning med `fr-CH` språket och `fr-CH-Guillaume` rösten.       |
| `fr-fr-hortenserus`                         | Behållar avbildning med `fr-FR` språket och `fr-FR-HortenseRUS` rösten.     |
| `fr-fr-julie-apollo`                        | Behållar avbildning med `fr-FR` språket och `fr-FR-Julie-Apollo` rösten.    |
| `fr-fr-paul-apollo`                         | Behållar avbildning med `fr-FR` språket och `fr-FR-Paul-Apollo` rösten.     |
| `he-il-asaf`                                | Behållar avbildning med `he-IL` språket och `he-IL-Asaf` rösten.            |
| `hi-in-hemant`                              | Behållar avbildning med `hi-IN` språket och `hi-IN-Hemant` rösten.          |
| `hi-in-kalpana-apollo`                      | Behållar avbildning med `hi-IN` språket och `hi-IN-Kalpana-Apollo` rösten.  |
| `hi-in-kalpana`                             | Behållar avbildning med `hi-IN` språket och `hi-IN-Kalpana` rösten.         |
| `hr-hr-matej`                               | Behållar avbildning med `hr-HR` språket och `hr-HR-Matej` rösten.           |
| `hu-hu-szabolcs`                            | Behållar avbildning med `hu-HU` språket och `hu-HU-Szabolcs` rösten.        |
| `id-id-andika`                              | Behållar avbildning med `id-ID` språket och `id-ID-Andika` rösten.          |
| `it-it-cosimo-apollo`                       | Behållar avbildning med `it-IT` språket och `it-IT-Cosimo-Apollo` rösten.   |
| `it-it-luciarus`                            | Behållar avbildning med `it-IT` språket och `it-IT-LuciaRUS` rösten.        |
| `ja-jp-ayumi-apollo`                        | Behållar avbildning med `ja-JP` språket och `ja-JP-Ayumi-Apollo` rösten.    |
| `ja-jp-harukarus`                           | Behållar avbildning med `ja-JP` språket och `ja-JP-HarukaRUS` rösten.       |
| `ja-jp-ichiro-apollo`                       | Behållar avbildning med `ja-JP` språket och `ja-JP-Ichiro-Apollo` rösten.   |
| `ko-kr-heamirus`                            | Behållar avbildning med `ko-KR` språket och `ko-KR-HeamiRUS` rösten.        |
| `ms-my-rizwan`                              | Behållar avbildning med `ms-MY` språket och `ms-MY-Rizwan` rösten.          |
| `nb-no-huldarus`                            | Behållar avbildning med `nb-NO` språket och `nb-NO-HuldaRUS` rösten.        |
| `nl-nl-hannarus`                            | Behållar avbildning med `nl-NL` språket och `nl-NL-HannaRUS` rösten.        |
| `pl-pl-paulinarus`                          | Behållar avbildning med `pl-PL` språket och `pl-PL-PaulinaRUS` rösten.      |
| `pt-br-daniel-apollo`                       | Behållar avbildning med `pt-BR` språket och `pt-BR-Daniel-Apollo` rösten.   |
| `pt-br-heloisarus`                          | Behållar avbildning med `pt-BR` språket och `pt-BR-HeloisaRUS` rösten.      |
| `pt-pt-heliarus`                            | Behållar avbildning med `pt-PT` språket och `pt-PT-HeliaRUS` rösten.        |
| `ro-ro-andrei`                              | Behållar avbildning med `ro-RO` språket och `ro-RO-Andrei` rösten.          |
| `ru-ru-ekaterinarus`                        | Behållar avbildning med `ru-RU` språket och `ru-RU-EkaterinaRUS` rösten.    |
| `ru-ru-irina-apollo`                        | Behållar avbildning med `ru-RU` språket och `ru-RU-Irina-Apollo` rösten.    |
| `ru-ru-pavel-apollo`                        | Behållar avbildning med `ru-RU` språket och `ru-RU-Pavel-Apollo` rösten.    |
| `sk-sk-filip`                               | Behållar avbildning med `sk-SK` språket och `sk-SK-Filip` rösten.           |
| `sl-si-lado`                                | Behållar avbildning med `sl-SI` språket och `sl-SI-Lado` rösten.            |
| `sv-se-hedvigrus`                           | Behållar avbildning med `sv-SE` språket och `sv-SE-HedvigRUS` rösten.       |
| `ta-in-valluvar`                            | Behållar avbildning med `ta-IN` språket och `ta-IN-Valluvar` rösten.        |
| `te-in-chitra`                              | Behållar avbildning med `te-IN` språket och `te-IN-Chitra` rösten.          |
| `th-th-pattara`                             | Behållar avbildning med `th-TH` språket och `th-TH-Pattara` rösten.         |
| `tr-tr-sedarus`                             | Behållar avbildning med `tr-TR` språket och `tr-TR-SedaRUS` rösten.         |
| `vi-vn-an`                                  | Behållar avbildning med `vi-VN` språket och `vi-VN-An` rösten.              |
| `zh-cn-huihuirus`                           | Behållar avbildning med `zh-CN` språket och `zh-CN-HuihuiRUS` rösten.       |
| `zh-cn-kangkang-apollo`                     | Behållar avbildning med `zh-CN` språket och `zh-CN-Kangkang-Apollo` rösten. |
| `zh-cn-yaoyao-apollo`                       | Behållar avbildning med `zh-CN` språket och `zh-CN-Yaoyao-Apollo` rösten.   |
| `zh-hk-danny-apollo`                        | Behållar avbildning med `zh-HK` språket och `zh-HK-Danny-Apollo` rösten.    |
| `zh-hk-tracy-apollo`                        | Behållar avbildning med `zh-HK` språket och `zh-HK-Tracy-Apollo` rösten.    |
| `zh-hk-tracyrus`                            | Behållar avbildning med `zh-HK` språket och `zh-HK-TracyRUS` rösten.        |
| `zh-tw-hanhanrus`                           | Behållar avbildning med `zh-TW` språket och `zh-TW-HanHanRUS` rösten.       |
| `zh-tw-yating-apollo`                       | Behållar avbildning med `zh-TW` språket och `zh-TW-Yating-Apollo` rösten.   |
| `zh-tw-zhiwei-apollo`                       | Behållar avbildning med `zh-TW` språket och `zh-TW-Zhiwei-Apollo` rösten.   |

| Språk för v-1.7.0                          | Kommentarer                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `ar-eg-hoda`                                | Behållar avbildning med `ar-EG` språket och `ar-EG-Hoda` rösten.            |
| `ar-sa-naayf`                               | Behållar avbildning med `ar-SA` språket och `ar-SA-Naayf` rösten.           |
| `bg-bg-ivan`                                | Behållar avbildning med `bg-BG` språket och `bg-BG-Ivan` rösten.            |
| `ca-es-herenarus`                           | Behållar avbildning med `ca-ES` språket och `ca-ES-HerenaRUS` rösten.       |
| `cs-cz-jakub`                               | Behållar avbildning med `cs-CZ` språket och `cs-CZ-Jakub` rösten.           |
| `da-dk-hellerus`                            | Behållar avbildning med `da-DK` språket och `da-DK-HelleRUS` rösten.        |
| `de-at-michael`                             | Behållar avbildning med `de-AT` språket och `de-AT-Michael` rösten.         |
| `de-ch-karsten`                             | Behållar avbildning med `de-CH` språket och `de-CH-Karsten` rösten.         |
| `de-de-hedda`                               | Behållar avbildning med `de-DE` språket och `de-DE-Hedda` rösten.           |
| `de-de-heddarus`                            | Behållar avbildning med `de-DE` språket och `de-DE-Hedda` rösten.           |
| `de-de-stefan-apollo`                       | Behållar avbildning med `de-DE` språket och `de-DE-Stefan-Apollo` rösten.   |
| `el-gr-stefanos`                            | Behållar avbildning med `el-GR` språket och `el-GR-Stefanos` rösten.        |
| `en-au-catherine`                           | Behållar avbildning med `en-AU` språket och `en-AU-Catherine` rösten.       |
| `en-au-hayleyrus`                           | Behållar avbildning med `en-AU` språket och `en-AU-HayleyRUS` rösten.       |
| `en-ca-heatherrus`                          | Behållar avbildning med `en-CA` språket och `en-CA-HeatherRUS` rösten.      |
| `en-ca-linda`                               | Behållar avbildning med `en-CA` språket och `en-CA-Linda` rösten.           |
| `en-gb-george-apollo`                       | Behållar avbildning med `en-GB` språket och `en-GB-George-Apollo` rösten.   |
| `en-gb-hazelrus`                            | Behållar avbildning med `en-GB` språket och `en-GB-HazelRUS` rösten.        |
| `en-gb-susan-apollo`                        | Behållar avbildning med `en-GB` språket och `en-GB-Susan-Apollo` rösten.    |
| `en-ie-sean`                                | Behållar avbildning med `en-IE` språket och `en-IE-Sean` rösten.            |
| `en-in-heera-apollo`                        | Behållar avbildning med `en-IN` språket och `en-IN-Heera-Apollo` rösten.    |
| `en-in-priyarus`                            | Behållar avbildning med `en-IN` språket och `en-IN-PriyaRUS` rösten.        |
| `en-in-ravi-apollo`                         | Behållar avbildning med `en-IN` språket och `en-IN-Ravi-Apollo` rösten.     |
| `en-us-benjaminrus`                         | Behållar avbildning med `en-US` språket och `en-US-BenjaminRUS` rösten.     |
| `en-us-guy24krus`                           | Behållar avbildning med `en-US` språket och `en-US-Guy24kRUS` rösten.       |
| `en-us-aria24krus`                          | Behållar avbildning med `en-US` språket och `en-US-Aria24kRUS` rösten.      |
| `en-us-ariarus`                             | Behållar avbildning med `en-US` språket och `en-US-AriaRUS` rösten.         |
| `en-us-zirarus`                             | Behållar avbildning med `en-US` språket och `en-US-ZiraRUS` rösten.         |
| `es-es-helenarus`                           | Behållar avbildning med `es-ES` språket och `es-ES-HelenaRUS` rösten.       |
| `es-es-laura-apollo`                        | Behållar avbildning med `es-ES` språket och `es-ES-Laura-Apollo` rösten.    |
| `es-es-pablo-apollo`                        | Behållar avbildning med `es-ES` språket och `es-ES-Pablo-Apollo` rösten.    |
| `es-mx-hildarus`                            | Behållar avbildning med `es-MX` språket och `es-MX-HildaRUS` rösten.        |
| `es-mx-raul-apollo`                         | Behållar avbildning med `es-MX` språket och `es-MX-Raul-Apollo` rösten.     |
| `fi-fi-heidirus`                            | Behållar avbildning med `fi-FI` språket och `fi-FI-HeidiRUS` rösten.        |
| `fr-ca-caroline`                            | Behållar avbildning med `fr-CA` språket och `fr-CA-Caroline` rösten.        |
| `fr-ca-harmonierus`                         | Behållar avbildning med `fr-CA` språket och `fr-CA-HarmonieRUS` rösten.     |
| `fr-ch-guillaume`                           | Behållar avbildning med `fr-CH` språket och `fr-CH-Guillaume` rösten.       |
| `fr-fr-hortenserus`                         | Behållar avbildning med `fr-FR` språket och `fr-FR-HortenseRUS` rösten.     |
| `fr-fr-julie-apollo`                        | Behållar avbildning med `fr-FR` språket och `fr-FR-Julie-Apollo` rösten.    |
| `fr-fr-paul-apollo`                         | Behållar avbildning med `fr-FR` språket och `fr-FR-Paul-Apollo` rösten.     |
| `he-il-asaf`                                | Behållar avbildning med `he-IL` språket och `he-IL-Asaf` rösten.            |
| `hi-in-hemant`                              | Behållar avbildning med `hi-IN` språket och `hi-IN-Hemant` rösten.          |
| `hi-in-kalpana-apollo`                      | Behållar avbildning med `hi-IN` språket och `hi-IN-Kalpana-Apollo` rösten.  |
| `hi-in-kalpana`                             | Behållar avbildning med `hi-IN` språket och `hi-IN-Kalpana` rösten.         |
| `hr-hr-matej`                               | Behållar avbildning med `hr-HR` språket och `hr-HR-Matej` rösten.           |
| `hu-hu-szabolcs`                            | Behållar avbildning med `hu-HU` språket och `hu-HU-Szabolcs` rösten.        |
| `id-id-andika`                              | Behållar avbildning med `id-ID` språket och `id-ID-Andika` rösten.          |
| `it-it-cosimo-apollo`                       | Behållar avbildning med `it-IT` språket och `it-IT-Cosimo-Apollo` rösten.   |
| `it-it-luciarus`                            | Behållar avbildning med `it-IT` språket och `it-IT-LuciaRUS` rösten.        |
| `ja-jp-ayumi-apollo`                        | Behållar avbildning med `ja-JP` språket och `ja-JP-Ayumi-Apollo` rösten.    |
| `ja-jp-harukarus`                           | Behållar avbildning med `ja-JP` språket och `ja-JP-HarukaRUS` rösten.       |
| `ja-jp-ichiro-apollo`                       | Behållar avbildning med `ja-JP` språket och `ja-JP-Ichiro-Apollo` rösten.   |
| `ko-kr-heamirus`                            | Behållar avbildning med `ko-KR` språket och `ko-KR-HeamiRUS` rösten.        |
| `ms-my-rizwan`                              | Behållar avbildning med `ms-MY` språket och `ms-MY-Rizwan` rösten.          |
| `nb-no-huldarus`                            | Behållar avbildning med `nb-NO` språket och `nb-NO-HuldaRUS` rösten.        |
| `nl-nl-hannarus`                            | Behållar avbildning med `nl-NL` språket och `nl-NL-HannaRUS` rösten.        |
| `pl-pl-paulinarus`                          | Behållar avbildning med `pl-PL` språket och `pl-PL-PaulinaRUS` rösten.      |
| `pt-br-daniel-apollo`                       | Behållar avbildning med `pt-BR` språket och `pt-BR-Daniel-Apollo` rösten.   |
| `pt-br-heloisarus`                          | Behållar avbildning med `pt-BR` språket och `pt-BR-HeloisaRUS` rösten.      |
| `pt-pt-heliarus`                            | Behållar avbildning med `pt-PT` språket och `pt-PT-HeliaRUS` rösten.        |
| `ro-ro-andrei`                              | Behållar avbildning med `ro-RO` språket och `ro-RO-Andrei` rösten.          |
| `ru-ru-ekaterinarus`                        | Behållar avbildning med `ru-RU` språket och `ru-RU-EkaterinaRUS` rösten.    |
| `ru-ru-irina-apollo`                        | Behållar avbildning med `ru-RU` språket och `ru-RU-Irina-Apollo` rösten.    |
| `ru-ru-pavel-apollo`                        | Behållar avbildning med `ru-RU` språket och `ru-RU-Pavel-Apollo` rösten.    |
| `sk-sk-filip`                               | Behållar avbildning med `sk-SK` språket och `sk-SK-Filip` rösten.           |
| `sl-si-lado`                                | Behållar avbildning med `sl-SI` språket och `sl-SI-Lado` rösten.            |
| `sv-se-hedvigrus`                           | Behållar avbildning med `sv-SE` språket och `sv-SE-HedvigRUS` rösten.       |
| `ta-in-valluvar`                            | Behållar avbildning med `ta-IN` språket och `ta-IN-Valluvar` rösten.        |
| `te-in-chitra`                              | Behållar avbildning med `te-IN` språket och `te-IN-Chitra` rösten.          |
| `th-th-pattara`                             | Behållar avbildning med `th-TH` språket och `th-TH-Pattara` rösten.         |
| `tr-tr-sedarus`                             | Behållar avbildning med `tr-TR` språket och `tr-TR-SedaRUS` rösten.         |
| `vi-vn-an`                                  | Behållar avbildning med `vi-VN` språket och `vi-VN-An` rösten.              |
| `zh-cn-huihuirus`                           | Behållar avbildning med `zh-CN` språket och `zh-CN-HuihuiRUS` rösten.       |
| `zh-cn-kangkang-apollo`                     | Behållar avbildning med `zh-CN` språket och `zh-CN-Kangkang-Apollo` rösten. |
| `zh-cn-yaoyao-apollo`                       | Behållar avbildning med `zh-CN` språket och `zh-CN-Yaoyao-Apollo` rösten.   |
| `zh-hk-danny-apollo`                        | Behållar avbildning med `zh-HK` språket och `zh-HK-Danny-Apollo` rösten.    |
| `zh-hk-tracy-apollo`                        | Behållar avbildning med `zh-HK` språket och `zh-HK-Tracy-Apollo` rösten.    |
| `zh-hk-tracyrus`                            | Behållar avbildning med `zh-HK` språket och `zh-HK-TracyRUS` rösten.        |
| `zh-tw-hanhanrus`                           | Behållar avbildning med `zh-TW` språket och `zh-TW-HanHanRUS` rösten.       |
| `zh-tw-yating-apollo`                       | Behållar avbildning med `zh-TW` språket och `zh-TW-Yating-Apollo` rösten.   |
| `zh-tw-zhiwei-apollo`                       | Behållar avbildning med `zh-TW` språket och `zh-TW-Zhiwei-Apollo` rösten.   |

---

## <a name="neural-text-to-speech"></a>Neurala text till tal

Du hittar [neurala text till tal-][sp-ntts] behållar avbildningen i `mcr.microsoft.com` behållar registrets syndikering. Den finns i `azure-cognitive-services/speechservices/` lagrings platsen och får namnet `neural-text-to-speech` . Det fullständigt kvalificerade namnet på behållar avbildningen är, `mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech` .

Den här behållar avbildningen har följande Taggar tillgängliga. Du kan också hitta en fullständig lista över [taggar i MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/neural-text-to-speech/tags/list).


# <a name="latest-version"></a>[Senaste version](#tab/current)

Viktig information för `v1.3.0` :
* Neurala-behållaren för text till tal är nu allmänt tillgänglig. 

| Bildtaggar                                  | Kommentarer                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | Behållar avbildning med `en-US` språket och `en-US-AriaNeural` rösten.      |
| `1.3.0-amd64-<locale-and-voice>`    | Ersätt `<locale>` med något av de tillgängliga språken i listan nedan. Till exempel `1.3.0-amd64-en-us-arianeural`. |


| v 1.3.0-språkområden och röster           | Kommentarer                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `de-de-katjaneural`                 | Behållar avbildning med `de-DE` språket och `de-DE-KatjaNeural` rösten.     |
| `en-au-natashaneural`               | Behållar avbildning med `en-AU` språket och `en-AU-NatashaNeural` rösten.   |
| `en-ca-claraneural`                 | Behållar avbildning med `en-CA` språket och `en-CA-ClaraNeural` rösten.     |
| `en-gb-libbyneural`                 | Behållar avbildning med `en-GB` språket och `en-GB-LibbyNeural` rösten.     |
| `en-gb-mianeural`                   | Behållar avbildning med `en-GB` språket och `en-GB-MiaNeural` rösten.       |
| `en-us-arianeural`                  | Behållar avbildning med `en-US` språket och `en-US-AriaNeural` rösten.      |
| `en-us-guyneural`                   | Behållar avbildning med `en-US` språket och `en-US-GuyNeural` rösten.       |
| `es-es-elviraneural`                | Behållar avbildning med `es-ES` språket och `es-ES-ElviraNeural` rösten.    |
| `es-mx-dalianeural`                 | Behållar avbildning med `es-MX` språket och `es-MX-DaliaNeural` rösten.     |
| `fr-ca-sylvieneural`                | Behållar avbildning med `fr-CA` språket och `fr-CA-SylvieNeural` rösten.    |
| `fr-fr-deniseneural`                | Behållar avbildning med `fr-FR` språket och `fr-FR-DeniseNeural` rösten.    |
| `it-it-elsaneural`                  | Behållar avbildning med `it-IT` språket och `it-IT-ElsaNeural` rösten.      |
| `ja-jp-nanamineural`                | Behållar avbildning med `ja-JP` språket och `ja-JP-NanamiNeural` rösten.    |
| `ko-kr-sunhineural`                 | Behållar avbildning med `ko-KR` språket och `ko-KR-SunHiNeural` rösten.     |
| `pt-br-franciscaneural`             | Behållar avbildning med `pt-BR` språket och `pt-BR-FranciscaNeural` rösten. |
| `zh-cn-xiaoxiaoneural`              | Behållar avbildning med `zh-CN` språket och `zh-CN-XiaoxiaoNeural` rösten.  |

# <a name="previous-version"></a>[Tidigare version](#tab/previous)

| Bildtaggar                                  | Kommentarer                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | Behållar avbildning med `en-US` språket och `en-US-AriaNeural` rösten.      |
| `1.2.0-amd64-<locale-and-voice>-preview`    | Ersätt `<locale>` med något av de tillgängliga språken i listan nedan. Till exempel `1.2.0-amd64-en-us-arianeural-preview`. |


| v-1.2.0 för hands versioner och röster           | Kommentarer                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | Behållar avbildning med `en-US` språket och `en-US-AriaNeural` rösten.      |
| `de-de-katjaneural-preview`                 | Behållar avbildning med `de-DE` språket och `de-DE-KatjaNeural` rösten.     |
| `en-au-natashaneural-preview`               | Behållar avbildning med `en-AU` språket och `en-AU-NatashaNeural` rösten.   |
| `en-ca-claraneural-preview`                 | Behållar avbildning med `en-CA` språket och `en-CA-ClaraNeural` rösten.     |
| `en-gb-libbyneural-preview`                 | Behållar avbildning med `en-GB` språket och `en-GB-LibbyNeural` rösten.     |
| `en-gb-mianeural-preview`                   | Behållar avbildning med `en-GB` språket och `en-GB-MiaNeural` rösten.       |
| `en-us-arianeural-preview`                  | Behållar avbildning med `en-US` språket och `en-US-AriaNeural` rösten.      |
| `en-us-guyneural-preview`                   | Behållar avbildning med `en-US` språket och `en-US-GuyNeural` rösten.       |
| `es-es-elviraneural-preview`                | Behållar avbildning med `es-ES` språket och `es-ES-ElviraNeural` rösten.    |
| `es-mx-dalianeural-preview`                 | Behållar avbildning med `es-MX` språket och `es-MX-DaliaNeural` rösten.     |
| `fr-ca-sylvieneural-preview`                | Behållar avbildning med `fr-CA` språket och `fr-CA-SylvieNeural` rösten.    |
| `fr-fr-deniseneural-preview`                | Behållar avbildning med `fr-FR` språket och `fr-FR-DeniseNeural` rösten.    |
| `it-it-elsaneural-preview`                  | Behållar avbildning med `it-IT` språket och `it-IT-ElsaNeural` rösten.      |
| `ja-jp-nanamineural-preview`                | Behållar avbildning med `ja-JP` språket och `ja-JP-NanamiNeural` rösten.    |
| `ko-kr-sunhineural-preview`                 | Behållar avbildning med `ko-KR` språket och `ko-KR-SunHiNeural` rösten.     |
| `pt-br-franciscaneural-preview`             | Behållar avbildning med `pt-BR` språket och `pt-BR-FranciscaNeural` rösten. |
| `zh-cn-xiaoxiaoneural-preview`              | Behållar avbildning med `zh-CN` språket och `zh-CN-XiaoxiaoNeural` rösten.  |

---

## <a name="speech-language-detection"></a>Språkidentifiering

Behållar avbildningen av [tal språk identifiering][sp-lid] finns i `mcr.microsoft.com` behållar registrets syndikering. Den finns i `azure-cognitive-services/speechservices/` lagrings platsen och får namnet `language-detection` . Det fullständigt kvalificerade namnet på behållar avbildningen är, `mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection` .

Den här behållar avbildningen har följande Taggar tillgängliga. Du kan också hitta en fullständig lista över [taggar i MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/language-detection/tags/list).

| Bildtaggar                                  | Kommentarer                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                       |      |
| `1.1.0-amd64-preview`                       |      |

## <a name="key-phrase-extraction"></a>Extrahering av nyckelfraser

behållar avbildningen finns i `mcr.microsoft.com` behållar registrets syndikering. Den finns i `azure-cognitive-services/textanalytics/` lagrings platsen och får namnet `keyphrase` . Det fullständigt kvalificerade namnet på behållar avbildningen är, `mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase` .

Den här behållar avbildningen har följande Taggar tillgängliga. Du kan också hitta en fullständig lista över [taggar i MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/keyphrase/tags/list).

# <a name="latest-version"></a>[Senaste version](#tab/current)


| Bildtaggar                    | Kommentarer |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013570001-amd64` |       |

# <a name="previous-versions"></a>[Tidigare versioner](#tab/previous)

| Bildtaggar                    | Kommentarer |
|-------------------------------|:------|
| `1.1.012840001-amd64` |       |
| `1.1.012830001-amd64`    |       |

---

## <a name="text-language-detection"></a>Identifiering av språk i text

[Språkidentifiering][ta-la] behållar avbildningen finns i `mcr.microsoft.com` behållar registrets syndikering. Den finns i `azure-cognitive-services/textanalytics/` lagrings platsen och får namnet `language` . Det fullständigt kvalificerade namnet på behållar avbildningen är, `mcr.microsoft.com/azure-cognitive-services/textanalytics/language`


Den här behållar avbildningen har följande Taggar tillgängliga. Du kan också hitta en fullständig lista över [taggar i MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/language/tags/list).

# <a name="latest-versions"></a>[Senaste versionerna](#tab/current)

| Bildtaggar                    | Kommentarer |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013570001-amd64` | |
   

# <a name="previous-versions"></a>[Tidigare versioner](#tab/previous)


| Bildtaggar                    | Kommentarer |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.012840001-amd64` |   |
| `1.1.012830001-amd64` |   |

---

## <a name="sentiment-analysis"></a>Attitydanalys

[Attitydanalys][ta-se] behållar avbildningen finns i `mcr.microsoft.com` behållar registrets syndikering. Den finns i `azure-cognitive-services/textanalytics/` lagrings platsen och får namnet `sentiment` . Det fullständigt kvalificerade namnet på behållar avbildningen är, `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment`

Den här behållar avbildningen har följande Taggar tillgängliga. Du kan också hitta en fullständig lista över [taggar i MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/sentiment/tags/list).

| Bildtaggar | Kommentarer                                         |
|------------|:----------------------------------------------|
| `latest`   |                                               |
| `3.0-en`   | Attitydanalys v3 (engelska)               |
| `3.0-es`   | Attitydanalys v3 (spanska)               |
| `3.0-fr`   | Attitydanalys v3 (franska)                |
| `3.0-it`   | Attitydanalys v3 (italiensk)               |
| `3.0-de`   | Attitydanalys v3 (tyska)                |
| `3.0-zh`   | Attitydanalys v3 (kinesiskt-förenklat)  |
| `3.0-zht`  | Attitydanalys v3 (kinesiska – traditionell) |
| `3.0-ja`   | Attitydanalys v3 (japanska)              |
| `3.0-pt`   | Attitydanalys v3 (portugisiska)            |
| `3.0-nl`   | Attitydanalys v3 (nederländska)                 |
| `2.1`    | Attitydanalys v2      |

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-stt]: ../speech-service/speech-container-howto.md?tabs=stt
[sp-cstt]: ../speech-service/speech-container-howto.md?tabs=cstt
[sp-tts]: ../speech-service/speech-container-howto.md?tabs=tts
[sp-ctts]: ../speech-service/speech-container-howto.md?tabs=ctts
[sp-ntts]: ../speech-service/speech-container-howto.md?tabs=ntts
[sp-lid]: ../speech-service/speech-container-howto.md?tabs=lid
[ta-kp]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-la]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-se]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment
