---
title: Använda Azure Cognitive Services-containrar lokalt
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder Docker-containrar för Cognitive Services lokalt.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, cog-serv-seo-aug-2020
ms.service: cognitive-services
ms.topic: article
ms.date: 04/16/2021
ms.author: aahi
keywords: lokalt, Docker, container, Kubernetes
ms.openlocfilehash: c40e91d81df448021be74af768bc9d5952b263dd
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588232"
---
# <a name="azure-cognitive-services-containers"></a>Azure Cognitive Services-containrar

Azure Cognitive Services innehåller flera [Docker-containrar](https://www.docker.com/what-container) som gör att du kan använda samma API:er som är tillgängliga i Azure lokalt. Med dessa containrar får du flexibiliteten att föra Cognitive Services närmare dina data av efterlevnads-, säkerhets- eller andra driftskäl. Stöd för containrar är för närvarande tillgängligt för en delmängd av Azure Cognitive Services.

> [!VIDEO https://www.youtube.com/embed/hdfbn4Q8jbo]

Containerisering är en metod för programvarudistribution där ett program eller en tjänst, inklusive dess beroenden & konfiguration, paketeras tillsammans som en containeravbildning. Med liten eller ingen ändring kan en containeravbildning distribueras på en containervärd. Containrar är isolerade från varandra och det underliggande operativsystemet, med ett mindre fotavtryck än en virtuell dator. Containrar kan instansieras från containeravbildningar för kortsiktiga uppgifter och tas bort när de inte längre behövs.

## <a name="features-and-benefits"></a>Funktioner och fördelar

- **Oföränderlig** infrastruktur: Gör det möjligt för DevOps-team att utnyttja en konsekvent och tillförlitlig uppsättning kända systemparametrar, samtidigt som de kan anpassa sig efter förändringar. Containrar ger flexibiliteten att pivotera i ett förutsägbart ekosystem och undvika konfigurationsavdrift.
- **Kontroll över data:** Välj var dina data bearbetas av Cognitive Services. Detta kan vara viktigt om du inte kan skicka data till molnet utan behöver åtkomst till API:er för Cognitive Services. Stöd för konsekvens i hybridmiljöer – för data, hantering, identitet och säkerhet.
- **Kontroll över modelluppdateringar:** Flexibilitet vid versionshantering och uppdatering av modeller som distribuerats i deras lösningar.
- **Portabel** arkitektur: Gör att du kan skapa en portabel programarkitektur som kan distribueras i Azure, lokalt och på gränsen. Containrar kan distribueras direkt [till Azure Kubernetes Service](../aks/index.yml), [Azure Container Instances](../container-instances/index.yml)eller till ett [Kubernetes-kluster](https://kubernetes.io/) som distribueras [till Azure Stack](/azure-stack/operator). Mer information finns i [Distribuera Kubernetes till Azure Stack](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).
- **Högt dataflöde/låg latens:** Ger kunderna möjlighet att skala för krav på högt dataflöde och låg latens genom att göra det möjligt för Cognitive Services att köras fysiskt nära deras programlogik och data. Containrar hanterar inte transaktioner per sekund (TPS) och kan göras för att skala upp och ut för att hantera efterfrågan om du tillhandahåller nödvändiga maskinvaruresurser.
- **Skalbarhet:** Med den ständigt växande populariteten för containerisering och containerorkestreringsprogramvara, till exempel Kubernetes; skalbarhet ligger i framkant när det gäller tekniska framsteg. Programutvecklingen bygger på en skalbar klustergrund och ger hög tillgänglighet.

## <a name="containers-in-azure-cognitive-services"></a>Containrar i Azure Cognitive Services

Azure Cognitive Services innehåller följande uppsättning Docker-containrar, där var och en innehåller en delmängd av funktioner från tjänster i Azure Cognitive Services. Instruktioner och bildplatser finns i tabellerna nedan. En lista över [containeravbildningar](containers/container-image-tags.md) är också tillgänglig.

### <a name="decision-containers"></a>Beslutscontainrar

| Tjänst |  Container | Description | Tillgänglighet |
|--|--|--|--|
| [Avvikelseidentifiering][ad-containers] | **Avvikelseidentifiering** ([bild](https://hub.docker.com/_/microsoft-azure-cognitive-services-decision-anomaly-detector))  | Med Avvikelseidentifiering-API:et kan du övervaka och identifiera avvikelser i dina tidsseriedata med maskininlärning. | Allmänt tillgänglig |

### <a name="language-containers"></a>Språkcontainrar

| Tjänst |  Container | Description | Tillgänglighet |
|--|--|--|--|
| [LUIS][lu-containers] |  **LUIS** ([bild](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409)) | Läser in en tränad eller publicerad Language Understanding-modell, även kallad en LUIS-app, i en Docker-container och ger åtkomst till frågeförutsägelser från containerns API-slutpunkter. Du kan samla in frågeloggar från containern och ladda upp dem tillbaka till [LUIS-portalen](https://www.luis.ai) för att förbättra appens förutsägelsenoggrannhet. | Allmänt tillgänglig |
| [Textanalys][ta-containers-keyphrase] | **Extrahering av diskussionsämne** ([bild](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409)) | Extraherar nyckelfraser för att identifiera huvudpunkterna. Exempel: För den inmatade texten ”Maten var härlig och personalen var underbar” returnerar API:et de huvudsakliga diskussionsämnena: ”mat” och ”underbar personal”. | Förhandsgranskning |
| [Textanalys][ta-containers-language] |  **Text Språkidentifiering** ([bild](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409)) | För upp till 120 språk identifierar vilket språk indatatexten är skriven på och rapporterar en enda språkkod för varje dokument som skickas på begäran. Språkkoden paras med poäng som anger styrkan hos poängen. | Allmänt tillgänglig |
| [Textanalys][ta-containers-sentiment] | **Attitydanalys v3** ([bild](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409)) | Analyserar råtext för att få ledtrådar om positiv eller negativ attityd. Den här versionen av attitydanalys returnerar sentimentetiketter (till *exempel positiva* eller *negativa*) för varje dokument och mening i det. |  Allmänt tillgänglig |
| [Textanalys][ta-containers-health] |  **Textanalys för hälsa** | Extrahera och märka medicinsk information från ostrukturerad medicinsk text. | Gated preview (Gated preview). [Begär åtkomst][request-access]. |

### <a name="speech-containers"></a>Speech-containrar

> [!NOTE]
> Om du vill använda Speech-containrar måste du fylla i ett [formulär för onlinebegäran.](https://aka.ms/csgate)

| Tjänst |  Container | Description | Tillgänglighet |
|--|--|--|
| [Speech Service API][sp-containers-stt] |  **Tal till text** ([bild](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-speech-to-text)) | Transkriberar kontinuerlig realtidsöversättning av tal till text. | Allmänt tillgänglig |
| [Speech Service API][sp-containers-cstt] | **Anpassat tal till text** [(bild)](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-speech-to-text) | Transkriberar kontinuerligt tal i realtid till text med hjälp av en anpassad modell. | Allmänt tillgänglig |
| [Speech Service API][sp-containers-tts] | **Text till tal** ([bild](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-text-to-speech)) | Konverterar text till naturligt tal. | Allmänt tillgänglig |
| [Speech Service API][sp-containers-ctts] | **Anpassad text till tal** [(bild)](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-text-to-speech) | Konverterar text till tal med naturligt ljud med hjälp av en anpassad modell. | Gated preview |
| [Speech Service API][sp-containers-ntts] | **Neural text till tal** [(bild)](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-neural-text-to-speech) | Konverterar text till tal med naturligt ljud med djup teknik för neuralt nätverk, vilket möjliggör mer naturligt syntetiserat tal. | Allmänt tillgänglig |
| [Speech Service API][sp-containers-lid] | **Talspråksidentifiering** ([bild](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-language-detection)) | Avgör språket för talat ljud. | Gated preview |

### <a name="vision-containers"></a>Containrar för visuellt innehåll

> [!WARNING]
> Den 11 juni 2020 meddelade Microsoft att de inte kommer att sälja teknik för ansiktsigenkänning till polismyndigheter i USA förrän starka föreskrifter som rör användningen av dessa tekniker och som bygger på de mänskliga rättigheterna har införts. Därför kan kunder inte använda funktioner för ansiktsigenkänning som ingår i Azure-tjänster, till exempel Ansiktsigenkänning eller Video Indexer, om en kund är eller tillåter användning av sådana tjänster av eller för en polisavdelning på USA.

| Tjänst |  Container | Description | Tillgänglighet |
|--|--|--|--|
| [Visuellt innehåll][cv-containers] | **Läsa OCR** ([bild](https://hub.docker.com/_/microsoft-azure-cognitive-services-vision-read)) | Med read OCR-containern kan du extrahera tryckt och handskriven text från bilder och dokument med stöd för filformaten JPEG, PNG, BMP, PDF och TIFF. Mer information finns i [läs-API-dokumentationen.](./computer-vision/overview-ocr.md) | Gated preview (Gated preview). [Begär åtkomst][request-access]. |
| [Rumslig analys][spa-containers] | **Rumslig analys** ([bild](https://hub.docker.com/_/microsoft-azure-cognitive-services-vision-spatial-analysis)) | Analyserar strömmande video i realtid för att förstå rumsliga relationer mellan människor, deras rörelser och interaktioner med objekt i fysiska miljöer. | Gated preview (Gated preview). [Begär åtkomst][request-access]. |
| [Ansiktsigenkänning][fa-containers] | **Ansiktsigenkänning** | Identifierar ansikten i bilder och identifierar attribut, inklusive ansiktsmärken (till exempel näsa och ögon), kön, ålder och andra maskinförutsagda ansiktsdrag. Förutom identifiering kan Ansiktsigenkänning kontrollera om två ansikten i samma bild eller olika bilder är likadana med hjälp av en förtroendepoäng, eller jämföra ansikten mot en databas för att se om det redan finns ett liknande eller identiskt ansikte. Den kan också ordna liknande ansikten i grupper med hjälp av delade visuella egenskaper. | Inte tillgänglig |
| [Form känna igen][fr-containers] | **Formigenkänning** | Form Understanding använder maskininlärningsteknik för att identifiera och extrahera nyckel/värde-par och tabeller från formulär. | Inte tillgänglig | 


<!--
|[Personalizer](./personalizer/what-is-personalizer.md) |F0, S0|**Personalizer** ([image](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Azure Personalizer is a cloud-based API service that allows you to choose the best experience to show to your users, learning from their real-time behavior.|
-->

Dessutom stöds vissa containrar i Cognitive Services [resurserbjudandet för flera](cognitive-services-apis-create-account.md) tjänster. Du kan skapa en enda Cognitive Services En-i-ett-resurs och använda samma faktureringsnyckel för tjänster som stöds för följande tjänster:

* Visuellt innehåll
* Ansikte
* LUIS
* Textanalys

## <a name="prerequisites"></a>Förutsättningar

Du måste uppfylla följande krav innan du använder Azure Cognitive Services containrar:

**Docker-motor:** Du måste ha Docker-motorn installerad lokalt. Docker innehåller paket som konfigurerar Docker-miljön på [macOS,](https://docs.docker.com/docker-for-mac/) [Linux](https://docs.docker.com/engine/installation/#supported-platforms)och [Windows.](https://docs.docker.com/docker-for-windows/) I Windows måste Docker konfigureras för att stödja Linux-containrar. Docker-containrar kan också distribueras direkt [till Azure Kubernetes Service](../aks/index.yml) eller [Azure Container Instances](../container-instances/index.yml).

Docker måste konfigureras så att containrarna kan ansluta till och skicka faktureringsdata till Azure.

Kunskaper om Microsoft Container Registry och **Docker:** Du bör ha grundläggande kunskaper om både Microsoft Container Registry- och Docker-begrepp som register, lagringsdatabaser, containrar och containeravbildningar samt kunskaper om grundläggande `docker` kommandon.

En introduktion till grunderna för Docker och containrar finns i [Docker-översikt](https://docs.docker.com/engine/docker-overview/).

Enskilda containrar kan också ha sina egna krav, inklusive krav på server- och minnesallokering.

[!INCLUDE [Cognitive Services container security](containers/includes/cognitive-services-container-security.md)]

## <a name="developer-samples"></a>Utvecklarexempel

Utvecklarexempel finns på vår [GitHub-lagringsplats.](https://github.com/Azure-Samples/cognitive-services-containers-samples)

## <a name="next-steps"></a>Nästa steg

Lär dig [mer om containerrecept](containers/container-reuse-recipe.md) som du kan använda med Cognitive Services.

Installera och utforska funktionerna som tillhandahålls av containrar i Azure Cognitive Services:

* [Avvikelseidentifiering containrar][ad-containers]
* [Visuellt innehåll containrar][cv-containers]
* [Ansiktscontainrar][fa-containers]
* [Formigenkänning containrar][fr-containers]
* [LANGUAGE UNDERSTANDING(LUIS)-containrar][lu-containers]
* [Speech Service API-containrar][sp-containers]
* [Textanalys containrar][ta-containers]

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
[request-access]: https://aka.ms/csgate
