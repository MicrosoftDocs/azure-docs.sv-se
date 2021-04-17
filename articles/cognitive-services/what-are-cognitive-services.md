---
title: Vad är Azure Cognitive Services?
titleSuffix: Azure Cognitive Services
description: Cognitive Services AI är tillgängligt för alla utvecklare utan att maskininlärning och datavetenskap krävs. Du behöver bara göra ett API-anrop från ditt program för att lägga till möjligheten att se (avancerad bildsökning och igenkänning), höra, tala, söka och fatta beslut i dina appar.
services: cognitive-services
author: nitinme
manager: nitinme
keywords: cognitive services, cognitive intelligence, cognitive solutions, ai services, cognitive understanding, cognitive features
ms.service: cognitive-services
ms.subservice: ''
ms.topic: overview
ms.date: 10/22/2020
ms.author: nitinme
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: c89131cc34d45ea94f3bb290ac11ec86f4b83be3
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587620"
---
# <a name="what-are-azure-cognitive-services"></a>Vad är Azure Cognitive Services?

Azure Cognitive Services är molnbaserade tjänster med REST API:er och klientbiblioteks-API:er som hjälper dig att bygga in kognitiv intelligens i dina program. Du kan lägga till kognitiva funktioner i dina program utan att ha artificiell intelligens (AI) eller datavetenskapskunskaper. Azure Cognitive Services omfattar olika AI-tjänster som gör att du kan skapa kognitiva lösningar som kan se, höra, tala, förstå och till och med fatta beslut.

## <a name="categories-of-cognitive-services"></a>Kategorier av Cognitive Services

Katalogen med kognitiva tjänster som ger kognitiv förståelse är indelade i fem huvudsakliga pelare:

* Visuellt innehåll
* Tal
* Språk
* Beslut
* Sök

Följande avsnitt i den här artikeln innehåller en lista över tjänster som ingår i dessa fem grundpelare.

## <a name="vision-apis"></a>API:er för vision

|Tjänstens namn|Beskrivning av tjänst|
|:-----------|:------------------|
|[Visuellt innehåll](./computer-vision/index.yml "Visuellt innehåll")|Tjänsten Visuellt innehåll ger dig tillgång till avancerade kognitiva algoritmer för bearbetning av bilder och returnering av information. Se [Visuellt innehåll snabbstart](./computer-vision/quickstarts-sdk/client-library.md) för att komma igång med tjänsten.|
|[Custom Vision Service](./custom-vision-service/index.yml "Custom Vision Service")|Med Custom Vision Service kan du skapa, distribuera och förbättra dina egna bild klassificerare. En bild klassificerare är en AI-tjänst som tillämpar etiketter på bilder, baserat på deras visuella egenskaper. |
|[Ansiktsigenkänning](./face/index.yml "Ansikte")| Tjänsten Ansiktsigenkänning ger åtkomst till avancerade ansiktsalgoritmer, vilket möjliggör identifiering och igenkänning av ansiktsattribut. Se [Snabbstart för ansiktsstart](./face/quickstarts/client-libraries.md) för att komma igång med tjänsten.|
|[Formigenkänning](./form-recognizer/index.yml "Formigenkänning")|Formigenkänning identifierar och extraherar nyckel/värde-par och tabelldata från formulärdokument; matar sedan ut strukturerade data, inklusive relationerna i den ursprungliga filen. Se [Formigenkänning snabbstart för](./form-recognizer/quickstarts/client-library.md) att komma igång.|
|[Video Indexer](../media-services/video-indexer/video-indexer-overview.md "Video Indexer")|Video Indexer kan du extrahera insikter från videon. Se [Video Indexer snabbstart för](/azure/media-services/video-indexer/video-indexer-get-started) att komma igång.|

## <a name="speech-apis"></a>Speech-API:er

|Tjänstens namn|Beskrivning av tjänst|
|:-----------|:------------------|
|[Speech Service](./speech-service/index.yml "Tjänst för taligenkänning")|Taltjänsten lägger till talaktiverade funktioner i program. Taltjänsten innehåller olika funktioner som tal till text, text till tal, talöversättning och mycket mer.|
<!--
|[Speaker Recognition API](./speech-service/speaker-recognition-overview.md "Speaker Recognition API") (Preview)|The Speaker Recognition API provides algorithms for speaker identification and verification.|
|[Bing Speech](./speech-service/how-to-migrate-from-bing-speech.md "Bing Speech") (Retiring)|The Bing Speech API provides you with an easy way to create speech-enabled features in your applications.|
|[Translator Speech](/azure/cognitive-services/translator-speech/ "Translator Speech") (Retiring)|Translator Speech is a machine translation service.|
-->
## <a name="language-apis"></a>Språk-API:er

|Tjänstens namn|Beskrivning av tjänst|
|:-----------|:------------------|
|[Language Understanding LUIS](./luis/index.yml "Language Understanding")|Language Understanding (LUIS) är en molnbaserad konversations-AI-tjänst som tillämpar anpassad maskininlärningsinformation på en användares konversationsbaserade, naturliga språktext för att förutsäga den övergripande innebörden och hämta relevant, detaljerad information. [Se LUIS-snabbstart](./luis/get-started-portal-build-app.md) för att komma igång med tjänsten.|
|[QnA Maker](./qnamaker/index.yml "QnA Maker")|QnA Maker kan du skapa en tjänst för frågor och svar från ditt halvstrukturerade innehåll. [Se QnA Maker snabbstart](./qnamaker/quickstarts/create-publish-knowledge-base.md) för att komma igång med tjänsten.|
|[Textanalys](./text-analytics/index.yml "Textanalys")| Textanalys ger bearbetning av naturligt språk i råtext för attitydanalys, extrahering av nyckelfraser och språkidentifiering. Se [Textanalys snabbstart för](./text-analytics/quickstarts/client-libraries-rest-api.md) att komma igång med tjänsten.|
|[Översättare](./translator/index.yml "Översättare")|Translator tillhandahåller maskinbaserad textöversättning nästan i realtid.|
| [Avancerad läsare](./immersive-reader/index.yml "Avancerad läsare") | Avancerad läsare lägger till funktioner för skärmläsning och förståelse i dina program. Se [Avancerad läsare snabbstart](./immersive-reader/quickstarts/client-libraries.md) för att komma igång med tjänsten. |

## <a name="decision-apis"></a>Besluts-API:er

|Tjänstens namn|Beskrivning av tjänst|
|:-----------|:------------------|
|[Avvikelseidentifiering](./anomaly-detector/index.yml "Avvikelseidentifiering") |Avvikelseidentifiering kan du övervaka och identifiera avvikelser i dina tidsseriedata. Se [Avvikelseidentifiering snabbstart](./anomaly-detector/quickstarts/client-libraries.md) för att komma igång med tjänsten|
|[Content Moderator](./content-moderator/overview.md "Content Moderator")|Content Moderator övervakning för eventuellt stötande, oönskat och riskabelt innehåll. Se [Content Moderator snabbstart för](./content-moderator/client-libraries.md) att komma igång med tjänsten.|
|[Metrics Advisor](./metrics-advisor/index.yml) (förhandsversion) | Metrics Advisor tillhandahåller anpassningsbar avvikelseidentifiering för flera tidsseriedata och en komplett webbportal som hjälper dig att använda tjänsten. Se [Metrics Advisor snabbstart för](./metrics-advisor/quickstarts/rest-api-and-client-library.md) att komma igång med tjänsten. |
|[Personanpassning](./personalizer/index.yml "Personanpassning")|Med Personanpassare kan du välja den bästa upplevelsen att visa dina användare och lära dig av deras beteende i realtid. Se [Snabbstart för personanpassare](./personalizer/quickstart-personalizer-sdk.md) för att komma igång med tjänsten.|

## <a name="search-apis"></a>Sök API:er

> [!NOTE]
> Letar du [efter Azure Cognitive Search?](../search/index.yml) Även om Cognitive Services används för vissa uppgifter är det en annan sökteknik som stöder andra scenarier.

|Tjänstens namn|Beskrivning av tjänst|
|:-----------|:------------------|
|[Nyhetssökning i Bing](/azure/cognitive-services/bing-news-search/ "Nyhetssökning i Bing")|Nyhetssökning i Bing returnerar en lista med nyhetsartiklar som har fastställts vara relevanta för användarens fråga.|
|[Videosökning i Bing](/azure/cognitive-services/Bing-Video-Search/ "Videosökning i Bing")|Videosökning i Bing returnerar en lista över videor som har fastställts vara relevanta för användarens fråga.|
|[Webbsökning i Bing](./bing-web-search/index.yml "Webbsökning i Bing")|Webbsökning i Bing returnerar en lista med sökresultat som har fastställts vara relevanta för användarens fråga.|
|[Automatiska förslag i Bing](/azure/cognitive-services/Bing-Autosuggest "Automatiska förslag i Bing")|Automatiska förslag i Bing kan du skicka en partiell sökfråga till Bing och få tillbaka en lista över föreslagna frågor.|
|[Anpassad sökning i Bing](/azure/cognitive-services/bing-custom-search "Anpassad sökning i Bing")|Anpassad Bing-sökning kan du skapa skräddarsydda sökupplevelser för ämnen som är viktiga för dig.|
|[Entitetssökning i Bing](/azure/cognitive-services/bing-entities-search/ "Entitetssökning i Bing")|Entitetssökning i Bing returnerar information om entiteter som Bing bedömer som relevanta för en användares fråga.|
|[Bildsökning i Bing](/azure/cognitive-services/bing-image-search "Bildsökning i Bing")|Bildsökning i Bing returnerar en visning av bilder som har fastställts vara relevanta för användarens fråga.|
|[Visuell sökning i Bing](/azure/cognitive-services/bing-visual-search "Visuell sökning i Bing")|Visuell sökning i Bing ger insikter om en bild, till exempel visuellt liknande bilder, shoppingkällor för produkter som finns i bilden och relaterade sökningar.|
|[Bing-sökning efter lokala företag](/azure/cognitive-services/bing-local-business-search/ "Bing-sökning efter lokala företag")| Bing-företagssökning i närområde-API:et gör det möjligt för dina program att hitta kontakt- och platsinformation om lokala företag baserat på sökfrågor.|
|[Stavningskontroll i Bing](/azure/cognitive-services/bing-spell-check/ "Stavningskontroll i Bing")|Stavningskontroll i Bing kan du utföra kontextbaserad kontroll av grammatik och stavning.|

## <a name="get-started-with-cognitive-services"></a>Kom igång med Cognitive Services

Börja med att skapa Cognitive Services resurs med praktiska snabbstarter med hjälp av följande metoder:

* [Azure-portalen](cognitive-services-apis-create-account.md?tabs=multiservice%2Cwindows "Azure Portal")
* [Azure CLI](cognitive-services-apis-create-account-cli.md?tabs=windows "Azure CLI")
* [Azure SDK-klientbibliotek](cognitive-services-apis-create-account-cli.md?tabs=windows "cognitive-services-apis-create-account-client-library?pivots=programming-language-csharp")
* [Azure Resource Manager-mallar (ARM)](./create-account-resource-manager-template.md?tabs=portal "Azure Resource Manager-mallar (ARM)")

## <a name="using-cognitive-services-in-different-development-environments"></a>Använda Cognitive Services i olika utvecklingsmiljöer

Med Azure Cognitive Services har du tillgång till flera utvecklingsalternativ, till exempel:

* Automatiserings- och integreringsverktyg som Logic Apps och Power Automate.
* Distributionsalternativ som Azure Functions och App Service. 
* Cognitive Services Docker-containrar för säker åtkomst.
* Verktyg som Apache Spark, Azure Databricks, Azure Synapse Analytics och Azure Kubernetes Service för stordatascenarier. 

Mer information finns i [Cognitive Services utvecklingsalternativ](./cognitive-services-development-options.md).

<!--
## Subscription management

Once you are signed in with your Microsoft Account, you can access [My subscriptions](https://www.microsoft.com/cognitive-services/subscriptions "My subscriptions") to show the products you are using, the quota remaining, and the ability to add additional products to your subscription.

## Upgrade to unlock higher limits

All APIs have a free tier, which has usage and throughput limits.  You can increase these limits by using a paid offering and selecting the appropriate pricing tier option when deploying the service in the Azure portal. [Learn more about the offerings and pricing](https://azure.microsoft.com/pricing/details/cognitive-services/ "offerings and pricing"). You'll need to set up an Azure subscriber account with a credit card and a phone number. If you have a special requirement or simply want to talk to sales, click "Contact us" button at the top the pricing page.
-->

## <a name="using-cognitive-services-securely"></a>Använda Cognitive Services på ett säkert sätt

Azure Cognitive Services tillhandahåller en säkerhetsmodell i flera lager, inklusive autentisering [via](authentication.md "autentisering") Azure Active Directory autentiseringsuppgifter, en giltig resursnyckel och Azure [Virtual Networks](cognitive-services-virtual-networks.md " Azure Virtual Networks").

## <a name="containers-for-cognitive-services"></a>Containrar för Cognitive Services

 Azure Cognitive Services tillhandahåller flera Docker-containrar som gör att du kan använda samma API:er som är tillgängliga i Azure lokalt. Med dessa containrar får du flexibiliteten att föra Cognitive Services närmare dina data av efterlevnads-, säkerhets- eller andra driftskäl. Läs mer om [Cognitive Services Containers](cognitive-services-container-support.md "Cognitive Services-containrar").

## <a name="regional-availability"></a>Regional tillgänglighet

API:erna i Cognitive Services finns i ett växande nätverk av Microsoft-hanterade datacenter. Du hittar regional tillgänglighet för varje API i listan [över Azure-regioner.](https://azure.microsoft.com/regions "Lista över Azure-regioner")

Letar du efter en region som vi inte stöder ännu? Meddela oss genom att skicka in en funktionsbegäran på [vårt UserVoice-forum.](https://cognitive.uservoice.com/ "UserVoice-forumet")

## <a name="supported-cultural-languages"></a>Kulturella språk som stöds

Cognitive Services har stöd för en mängd olika kulturella språk på servicenivå. Du hittar språktillgänglighet för varje API i listan [över språk som stöds.](language-support.md "lista över språk som stöds")

## <a name="certifications-and-compliance"></a>Certifieringar och efterlevnad

Cognitive Services har tilldelats certifieringar som CSA STAR-certifiering, FedRAMP Moderate och HIPAA BAA. Du kan [ladda](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942 "ladda ned") ned certifieringar för dina egna granskningar och säkerhetsgranskningar.

Om du vill förstå sekretess- och datahantering går du till [Säkerhetscenter.](https://servicetrust.microsoft.com/ "Säkerhetscenter")

## <a name="support"></a>Support

Cognitive Services flera supportalternativ som hjälper dig att gå vidare med att skapa intelligenta program. Cognitive Services har också en stark community med utvecklare som kan hjälpa dig att besvara dina specifika frågor. En fullständig lista över tillgängliga alternativ finns i Cognitive Services [och hjälpalternativ](cognitive-services-support-options.md "Cognitive Services alternativ för support och hjälp").

## <a name="next-steps"></a>Nästa steg

* [Skapa ett Cognitive Services-konto](cognitive-services-apis-create-account.md "Skapa ett Cognitive Services-konto")
* [Vad är nytt i Cognitive Services dokument](whats-new-docs.md "Vad är nytt i Cognitive Services dokument")
* [Planera och hantera kostnader för Cognitive Services](plan-manage-costs.md)
