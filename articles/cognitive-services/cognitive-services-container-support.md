---
title: Använd Azure Cognitive Services-behållare lokalt
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder Docker-behållare för att använda Cognitive Services lokalt.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, cog-serv-seo-aug-2020
ms.service: cognitive-services
ms.topic: article
ms.date: 12/16/2020
ms.author: aahi
keywords: lokal, Docker, behållare, Kubernetes
ms.openlocfilehash: f5caac0a0da3595075dddd5a273a6883a0cca468
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "97861816"
---
# <a name="azure-cognitive-services-containers"></a>Azure Cognitive Services-containrar

Azure Cognitive Services tillhandahåller flera [Docker-behållare](https://www.docker.com/what-container) som låter dig använda samma API: er som är tillgängliga i Azure, lokalt. Med hjälp av de här behållarna får du flexibiliteten att ta Cognitive Services närmare dina data för efterlevnad, säkerhet eller andra drift orsaker. Container support är för närvarande tillgängligt för en delmängd av Azure Cognitive Services.

> [!VIDEO https://www.youtube.com/embed/hdfbn4Q8jbo]

Skapa behållare är en metod för program varu distribution där ett program eller en tjänst, inklusive dess beroenden & konfiguration, paketeras tillsammans som en behållar avbildning. Med lite eller ingen ändring kan en behållar avbildning distribueras på en behållar värd. Behållare är isolerade från varandra och det underliggande operativ systemet, med en mindre plats än en virtuell dator. Behållare kan instansieras från behållar avbildningar för kortsiktiga uppgifter och tas bort när de inte längre behövs.

## <a name="features-and-benefits"></a>Funktioner och fördelar

- **Oåterkallelig infrastruktur**: Aktivera DevOps Teams för att utnyttja en konsekvent och tillförlitlig uppsättning kända system parametrar, samtidigt som du kan anpassa för att ändra. Behållare ger flexibiliteten att pivotera i ett förutsägbart eko system och undvika konfigurations avvikelser.
- **Kontroll över data**: Välj var dina data ska bearbetas av Cognitive Services. Detta kan vara viktigt om du inte kan skicka data till molnet men behöver åtkomst till API:er för Cognitive Services. Stöd för konsekvens i hybrid miljöer – över data, hantering, identitet och säkerhet.
- **Kontroll över modell uppdateringar**: flexibilitet vid versions hantering och uppdatering av modeller som distribuerats i deras lösningar.
- **Portabel arkitektur**: gör det möjligt att skapa en bärbar program arkitektur som kan distribueras på Azure, lokalt och på gränsen. Behållare kan distribueras direkt till [Azure Kubernetes-tjänsten](../aks/index.yml), [Azure Container instances](../container-instances/index.yml)eller till ett [Kubernetes](https://kubernetes.io/) -kluster som distribuerats till [Azure Stack](/azure-stack/operator). Mer information finns i [distribuera Kubernetes till Azure Stack](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).
- **Högt data flöde/låg latens**: ge kunderna möjlighet att skala för höga krav på data flöden och låg latens genom att aktivera Cognitive Services att köra fysiskt nära sin program logik och data. Behållare omfattar inte Cap-transaktioner per sekund (TPS) och kan göras för att skala upp och ut för att hantera efter frågan om du anger de nödvändiga maskin varu resurserna.
- **Skalbarhet**: med den ständigt växande populariteten av skapa behållare och behållar Dirigerings program, till exempel Kubernetes; skalbarhet är i det tekniska förskottet i Forefront. Genom att bygga vidare på en skalbar kluster bas kan program utveckling tillgodose hög tillgänglighet.

## <a name="containers-in-azure-cognitive-services"></a>Behållare i Azure Cognitive Services

Azure Cognitive Services-behållare innehåller följande uppsättning Docker-behållare, som var och en innehåller en delmängd funktioner från tjänster i Azure Cognitive Services. Du kan hitta instruktioner och bild platser i tabellerna nedan. Det finns också en lista över [behållar avbildningar](containers/container-image-tags.md) .

### <a name="decision-containers"></a>Besluts behållare

| Tjänst |  Container | Beskrivning | Tillgänglighet |
|--|--|--|--|
| [Avvikelse detektor][ad-containers] | **Avvikelse detektor** ([bild](https://hub.docker.com/_/microsoft-azure-cognitive-services-decision-anomaly-detector))  | Med API: t för avvikelse identifiering kan du övervaka och identifiera avvikelser i dina Time Series-data med Machine Learning. | Allmänt tillgänglig |

### <a name="language-containers"></a>Språkcontainrar

| Tjänst |  Container | Beskrivning | Tillgänglighet |
|--|--|--|--|
| [LUIS][lu-containers] |  **Luis** ([bild](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409)) | Läser in en utbildad eller publicerad Language Understanding modell, som även kallas LUIS-app, i en Docker-behållare och ger åtkomst till frågans förutsägelser från behållarens API-slutpunkter. Du kan samla in frågeuttryck från behållaren och överföra tillbaka dem till Luis- [portalen](https://www.luis.ai) för att förbättra appens förutsägelse noggrannhet. | Allmänt tillgänglig |
| [Textanalys][ta-containers-keyphrase] | **Extrahering av diskussionsämne** ([bild](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409)) | Extraherar viktiga fraser för att identifiera huvud punkterna. Exempel: För den inmatade texten ”Maten var härlig och personalen var underbar” returnerar API:et de huvudsakliga diskussionsämnena: ”mat” och ”underbar personal”. | Förhandsgranskning |
| [Textanalys][ta-containers-language] |  **Text språkidentifiering** ([bild](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409)) | För upp till 120 språk identifierar det språk som indatamängden är skrivet i och rapporterar en enda språkkod för varje dokument som skickas på begäran. Språkkoden paras med poäng som anger styrkan hos poängen. | Förhandsgranskning |
| [Textanalys][ta-containers-sentiment] | **Attitydanalys v3** ([bild](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409)) | Analyserar rå text för LED trådar om positiv eller negativ sentiment. Den här versionen av sentiment-analys returnerar sentiment etiketter (till exempel *positivt* eller *negativt*) för varje dokument och mening i det. |  Allmänt tillgänglig |
| [Textanalys][ta-containers-health] |  **Textanalys för hälsa** | Extrahera och märk medicinsk information från ostrukturerad klinisk text. | Gated Preview. [Begär åtkomst][request-access]. |

### <a name="speech-containers"></a>Speech-containrar

> [!NOTE]
> Om du vill använda tal behållare måste du fylla i ett [formulär för online-begäran](https://aka.ms/csgate).

| Tjänst |  Container | Beskrivning | Tillgänglighet |
|--|--|--|
| [Speech Service API][sp-containers-stt] |  **Tal till text** ([bild](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-speech-to-text)) | Transkriberar kontinuerlig realtidsöversättning av tal till text. | Allmänt tillgänglig |
| [Speech Service API][sp-containers-cstt] | **Custom Speech-till-text** ([bild](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-speech-to-text)) | Översätter kontinuerlig real tids tal till text med hjälp av en anpassad modell. | Allmänt tillgänglig |
| [Speech Service API][sp-containers-tts] | **Text till tal** ([bild](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-text-to-speech)) | Konverterar text till naturligt tal. | Allmänt tillgänglig |
| [Speech Service API][sp-containers-ctts] | **Anpassad text till tal** ([bild](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-text-to-speech)) | Konverterar text till naturligt ljuds tal med hjälp av en anpassad modell. | Gated Preview |
| [Speech Service API][sp-containers-ntts] | **Neurala text till tal** ([bild](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-neural-text-to-speech)) | Konverterar text till naturligt ljuds tal med djup neurala nätverks teknik, vilket ger mer naturliga syntetiskt syntetiskt tal. | Allmänt tillgänglig |
| [Speech Service API][sp-containers-lid] | **Igenkänning av tal språk** ([bild](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-language-detection)) | Anger språket för det talade ljudet. | Gated Preview |

### <a name="vision-containers"></a>Containrar för visuellt innehåll

> [!WARNING]
> Den 11 juni 2020 meddelade Microsoft att de inte kommer att sälja teknik för ansiktsigenkänning till polismyndigheter i USA förrän starka föreskrifter som rör användningen av dessa tekniker och som bygger på de mänskliga rättigheterna har införts. Därför kan kunder inte använda ansikts igenkännings funktioner eller funktioner som ingår i Azure-tjänster, till exempel ansikte eller Video Indexer, om en kund är eller tillåter att sådana tjänster används av eller för en polis avdelning i USA.

| Tjänst |  Container | Beskrivning | Tillgänglighet |
|--|--|--|--|
| [Visuellt innehåll][cv-containers] | **Läs OCR** ([bild](https://hub.docker.com/_/microsoft-azure-cognitive-services-vision-read)) | Med Read OCR-behållaren kan du extrahera utskrift och handskriven text från bilder och dokument med stöd för JPEG-, PNG-, BMP-, PDF-och TIFF-filformat. Mer information finns i [Read API-dokumentationen](./computer-vision/concept-recognizing-text.md). | Gated Preview. [Begär åtkomst][request-access]. |
| [Rumslig analys][spa-containers] | **Rums analys** ([bild](https://hub.docker.com/_/microsoft-azure-cognitive-services-vision-spatial-analysis)) | Analyserar direktuppspelad video i real tid för att förstå spatiala relationer mellan människor, deras rörelse och interaktioner med objekt i fysiska miljöer. | Gated Preview. [Begär åtkomst][request-access]. |
| [Ansiktsigenkänning][fa-containers] | **Ansiktsigenkänning** | Identifierar människo ansikten i bilder och identifierar attribut, inklusive ansikts landmärken (till exempel näsaer och ögon), kön, ålder och andra maskin förväntade ansikts funktioner. Förutom identifiering kan FACET kontrol lera om två ansikten i samma bild eller olika bilder är desamma genom att använda en säkerhets poäng, eller jämföra ansikten mot en databas för att se om det redan finns ett liknande eller identiskt ansikte. Det kan också organisera liknande ansikten i grupper med hjälp av delade visuella egenskaper. | Inte tillgänglig |
| [Formulär igenkänning][fr-containers] | **Formigenkänning** | Forms förståelse använder Machine Learning-teknik för att identifiera och extrahera nyckel/värde-par och tabeller från formulär. | Inte tillgänglig | 


<!--
|[Personalizer](./personalizer/what-is-personalizer.md) |F0, S0|**Personalizer** ([image](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Azure Personalizer is a cloud-based API service that allows you to choose the best experience to show to your users, learning from their real-time behavior.|
-->

Dessutom stöds vissa behållare i Cognitive Services resurs erbjudande i [flera tjänster](cognitive-services-apis-create-account.md) . Du kan skapa en enda Cognitive Services alla-i-en-resurs och använda samma fakturerings nyckel mellan tjänster som stöds för följande tjänster:

* Visuellt innehåll
* Ansikte
* LUIS
* Textanalys

## <a name="prerequisites"></a>Förutsättningar

Du måste uppfylla följande krav innan du använder Azure Cognitive Services-behållare:

**Docker-motorn**: du måste ha Docker-motorn installerad lokalt. Docker innehåller paket som konfigurerar Docker-miljön på [MacOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms)och [Windows](https://docs.docker.com/docker-for-windows/). I Windows måste Docker konfigureras för att stödja Linux-behållare. Docker-behållare kan också distribueras direkt till [Azure Kubernetes-tjänsten](../aks/index.yml) eller [Azure Container instances](../container-instances/index.yml).

Docker måste konfigureras för att tillåta att behållarna ansluter till och skicka fakturerings data till Azure.

**Bekant med Microsoft container Registry och Docker**: du bör ha grundläggande kunskaper om både Microsoft container Registry-och Docker-koncept, t. ex. register, databaser, behållare och behållar avbildningar samt kunskaper om grundläggande `docker` kommandon.

En introduktion till grunderna för Docker och containrar finns i [Docker-översikt](https://docs.docker.com/engine/docker-overview/).

Enskilda behållare kan också ha sina egna krav, inklusive krav på Server och minnesallokering.

[!INCLUDE [Cognitive Services container security](containers/includes/cognitive-services-container-security.md)]

## <a name="developer-samples"></a>Utvecklarexempel

Exempel på utvecklare finns på vår [GitHub-lagringsplats](https://github.com/Azure-Samples/cognitive-services-containers-samples).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [behållar recept](containers/container-reuse-recipe.md) som du kan använda med Cognitive Services.

Installera och utforska de funktioner som finns i behållare i Azure Cognitive Services:

* [Avvikelse detektor behållare][ad-containers]
* [Visuellt innehåll behållare][cv-containers]
* [Ansikts behållare][fa-containers]
* [Formulär igenkännings behållare][fr-containers]
* [Language Understanding (LUIS) behållare][lu-containers]
* [API-behållare för Speech service][sp-containers]
* [Textanalys behållare][ta-containers]

<!--* [Personalizer containers](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409)
-->

[ad-containers]: anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: face/face-how-to-install-containers.md
[fr-containers]: form-recognizer/form-recognizer-container-howto.md
[lu-containers]: luis/luis-container-howto.md
[sp-containers]: speech-service/speech-container-howto.md
[spa-containers]: ./computer-vision/spatial-analysis-container.md
[sp-containers-lid]: speech-service/speech-container-howto.md?tabs=lid
[sp-containers-stt]: speech-service/speech-container-howto.md?tabs=stt
[sp-containers-cstt]: speech-service/speech-container-howto.md?tabs=cstt
[sp-containers-tts]: speech-service/speech-container-howto.md?tabs=tts
[sp-containers-ctts]: speech-service/speech-container-howto.md?tabs=ctts
[sp-containers-ntts]: speech-service/speech-container-howto.md?tabs=ntts
[ta-containers]: text-analytics/how-tos/text-analytics-how-to-install-containers.md
[ta-containers-keyphrase]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-containers-language]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-containers-sentiment]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment
[ta-containers-health]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=health
[request-access]: https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u