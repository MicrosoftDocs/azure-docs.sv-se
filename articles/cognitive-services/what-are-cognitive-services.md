---
title: Vad är Azure Cognitive Services?
titleSuffix: Azure Cognitive Services
description: Cognitive Services gör AI tillgängligt för alla utvecklare utan att kräva expert kunskaper om Machine Learning och data vetenskap. Du behöver bara göra ett API-anrop från ditt program för att lägga till möjligheten att se (avancerad bilds ökning och igenkänning), höra, tala, Sök och besluts fattande i dina appar.
services: cognitive-services
author: nitinme
manager: nitinme
keywords: kognitiva tjänster, kognitiv intelligens, kognitiva lösningar, AI-tjänster, kognitiv förståelse, kognitiva funktioner
ms.service: cognitive-services
ms.subservice: ''
ms.topic: overview
ms.date: 10/22/2020
ms.author: nitinme
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: 85bcdb319431f1079cdbc8b94f0313f1dd6b67d6
ms.sourcegitcommit: 27cd3e515fee7821807c03e64ce8ac2dd2dd82d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103602009"
---
# <a name="what-are-azure-cognitive-services"></a>Vad är Azure Cognitive Services?

Azure Cognitive Services är molnbaserade tjänster med REST-API: er och SDK: er för klient bibliotek som kan hjälpa dig att bygga kognitiv information i dina program. Du kan lägga till kognitiva funktioner i dina program utan att ha artificiell intelligens (AI) eller data vetenskaps kunskaper. Azure Cognitive Services omfatta olika AI-tjänster som gör att du kan skapa kognitiva lösningar som kan se, höra, tala, förstå och till och med fatta beslut.

## <a name="categories-of-cognitive-services"></a>Cognitive Services kategorier

Katalogen med kognitiva tjänster som ger kognitiv förståelse kategoriseras i fem huvud pelare:

* Visuellt innehåll
* Tal
* Språk
* Beslut
* Sök

Följande avsnitt i den här artikeln innehåller en lista över tjänster som ingår i dessa fem pelare.

## <a name="vision-apis"></a>Vision-API: er

|Tjänstens namn|Beskrivning av tjänst|
|:-----------|:------------------|
|[Visuellt innehåll](./computer-vision/index.yml "Visuellt innehåll")|Tjänsten Visuellt innehåll ger dig till gång till avancerade kognitiva algoritmer för bearbetning av bilder och information som returneras.|
|[Custom Vision Service](./custom-vision-service/overview.md "Custom Vision Service")|Med Custom Vision Service kan du bygga anpassade bild klassificerare.|
|[Ansiktsigenkänning](./face/index.yml "Ansikte")| Ansikts tjänsten ger till gång till avancerade ansikts algoritmer, vilket möjliggör identifiering och igenkänning av ansikts attribut.|
|[Formigenkänning](./form-recognizer/index.yml "Formigenkänning")|Formulär tolken identifierar och extraherar nyckel/värde-par och tabell data från formulär dokument. matar sedan ut strukturerade data, inklusive relationerna i original filen.|
|[Video Indexer](../media-services/video-indexer/video-indexer-overview.md "Video Indexer")|Med Video Indexer kan du extrahera insikter från videon.|

## <a name="speech-apis"></a>Tal-API: er

|Tjänstens namn|Beskrivning av tjänst|
|:-----------|:------------------|
|[Tal tjänst](./speech-service/index.yml "Tjänst för taligenkänning")|Tal tjänsten lägger till tal aktiverade funktioner i program. Tal tjänsten innehåller olika funktioner som tal-till-text, text till tal, tal översättning och många fler.|
<!--
|[Speaker Recognition API](./speech-service/speaker-recognition-overview.md "Speaker Recognition API") (Preview)|The Speaker Recognition API provides algorithms for speaker identification and verification.|
|[Bing Speech](./speech-service/how-to-migrate-from-bing-speech.md "Bing Speech") (Retiring)|The Bing Speech API provides you with an easy way to create speech-enabled features in your applications.|
|[Translator Speech](/azure/cognitive-services/translator-speech/ "Translator Speech") (Retiring)|Translator Speech is a machine translation service.|
-->
## <a name="language-apis"></a>Språk-API: er

|Tjänstens namn|Beskrivning av tjänst|
|:-----------|:------------------|
|[Language Understanding LUIS](./luis/index.yml "Language Understanding")|Language Understanding-tjänsten (LUIS) gör så att ditt program kan förstå vad en person vill när den använder egna ord.|
|[QnA Maker](./qnamaker/index.yml "QnA Maker")|Med QnA Maker kan du bygga en fråge-och svars tjänst från det delvis strukturerade innehållet.|
|[Textanalys](./text-analytics/index.yml "Textanalys")| Textanalys tillhandahåller bearbetning av naturligt språk över rå text för sentiment-analys, extrahering av nyckel fraser och språk identifiering.|
|[Översättare](./translator/index.yml "Översättare")|Translator tillhandahåller datorspecifik text översättning i nära real tid.|
| [Avancerad läsare](./immersive-reader/index.yml "Avancerad läsare") | Avancerad läsare lägger till skärm läsnings-och förståelse funktioner i dina program. |

## <a name="decision-apis"></a>Besluts-API: er

|Tjänstens namn|Beskrivning av tjänst|
|:-----------|:------------------|
|[Avvikelseidentifiering](./anomaly-detector/index.yml "Avvikelseidentifiering") |Med avvikelse detektor kan du övervaka och identifiera avvikelser i dina tids serie data.|
|[Content Moderator](./content-moderator/overview.md "Content Moderator")|Content Moderator tillhandahåller övervakning av potentiellt stötande, olämpligt och riskfylldt innehåll.|
|[Mått rådgivare](./metrics-advisor/index.yml) (förhands granskning) | Metrics Advisor ger anpassningsbar avvikelse identifiering på variate Time Series-data och en helt aktuell webb portal som hjälper dig att använda tjänsten.|
|[Personanpassning](./personalizer/index.yml "Personanpassning")|Med personanpassa kan du välja den bästa upplevelsen som ska visas för användarna, lära sig från deras real tids beteende.|

## <a name="search-apis"></a>Sök-API: er

> [!NOTE]
> Letar du efter [Azure-kognitiv sökning](../search/index.yml)? Även om det använder Cognitive Services för vissa uppgifter, är det en annan Sök teknik som har stöd för andra scenarier.

|Tjänstens namn|Beskrivning av tjänst|
|:-----------|:------------------|
|[Nyhetssökning i Bing](/azure/cognitive-services/bing-news-search/ "Nyhetssökning i Bing")|Nyhetssökning i Bing returnerar en lista med nyhets artiklar som har visat sig vara relevanta för användarens fråga.|
|[Videosökning i Bing](/azure/cognitive-services/Bing-Video-Search/ "Videosökning i Bing")|Videosökning i Bing returnerar en lista med videor som bedöms vara relevanta för användarens fråga.|
|[Webbsökning i Bing](./bing-web-search/index.yml "Webbsökning i Bing")|Webbsökning i Bing returnerar en lista med Sök resultat som har fastställts vara relevanta för användarens fråga.|
|[Automatiska förslag i Bing](/azure/cognitive-services/Bing-Autosuggest "Automatiska förslag i Bing")|Med Automatiska förslag i Bing kan du skicka en del sökords term till Bing och få tillbaka en lista över föreslagna frågor.|
|[Anpassad sökning i Bing](/azure/cognitive-services/bing-custom-search "Anpassad sökning i Bing")|Med Anpassad Bing-sökning kan du skapa skräddarsydda Sök upplevelser för ämnen som du bryr dig om.|
|[Entitetssökning i Bing](/azure/cognitive-services/bing-entities-search/ "Entitetssökning i Bing")|Entitetssökning i Bing returnerar information om entiteter som Bing avgör är relevant för användarens fråga.|
|[Bildsökning i Bing](/azure/cognitive-services/bing-image-search "Bildsökning i Bing")|Bildsökning i Bing returnerar en visning av bilder som har fastställts vara relevanta för användarens fråga.|
|[Visuell sökning i Bing](/azure/cognitive-services/bing-visual-search "Visuell sökning i Bing")|Visuell sökning i Bing returnerar insikter om en bild, till exempel visuellt likartade bilder, shopping källor för produkter som finns i avbildningen och relaterade sökningar.|
|[Bing-sökning efter lokala företag](/azure/cognitive-services/bing-local-business-search/ "Bing-sökning efter lokala företag")| Med API för lokal sökning i Bing kan dina program hitta kontakt-och plats information om lokala företag baserat på Sök frågor.|
|[Stavningskontroll i Bing](/azure/cognitive-services/bing-spell-check/ "Stavningskontroll i Bing")|Med Stavningskontroll i Bing kan du utföra sammanhangsbaserad grammatik och stavnings kontroll.|

## <a name="development-options"></a>Utvecklingsalternativ 

Med Azure och Cognitive Services har du till gång till flera utvecklings alternativ, till exempel:

* Automatiserings-och integrations verktyg som Logic Apps och Energis par automatisering.
* Distributions alternativ som Azure Functions och App Service. 
* Cognitive Services Docker-behållare för säker åtkomst.
* Verktyg som Apache Spark, Azure Databricks, Azure Synapse Analytics och Azure Kubernetes-tjänsten för stora data scenarier. 

Läs mer i [Cognitive Services utvecklings alternativ](./cognitive-services-development-options.md).

## <a name="learn-with-the-quickstarts"></a>Lär dig med snabb starter

Börja med att skapa en Cognitive Services-resurs med praktiska snabb starter med hjälp av följande metoder:

* [Azure-portalen](cognitive-services-apis-create-account.md?tabs=multiservice%2Cwindows "Azure Portal")
* [Azure CLI](cognitive-services-apis-create-account-cli.md?tabs=windows "Azure CLI")
* [Klient bibliotek för Azure SDK](cognitive-services-apis-create-account-cli.md?tabs=windows "kognitiva tjänster-API: er-Create-Account-Client-library? pivoting = Programming-Language-csharp")
* [Azure Resource Manager-mallar (ARM)](./create-account-resource-manager-template.md?tabs=portal "Azure Resource Manager-mallar (ARM)")

<!--
## Subscription management

Once you are signed in with your Microsoft Account, you can access [My subscriptions](https://www.microsoft.com/cognitive-services/subscriptions "My subscriptions") to show the products you are using, the quota remaining, and the ability to add additional products to your subscription.

## Upgrade to unlock higher limits

All APIs have a free tier, which has usage and throughput limits.  You can increase these limits by using a paid offering and selecting the appropriate pricing tier option when deploying the service in the Azure portal. [Learn more about the offerings and pricing](https://azure.microsoft.com/pricing/details/cognitive-services/ "offerings and pricing"). You'll need to set up an Azure subscriber account with a credit card and a phone number. If you have a special requirement or simply want to talk to sales, click "Contact us" button at the top the pricing page.
-->

## <a name="using-cognitive-services-securely"></a>Använda Cognitive Services på ett säkert sätt

Azure Cognitive Services tillhandahåller en skiktad säkerhets modell, inklusive [autentisering](authentication.md "autentisering") via Azure Active Directory autentiseringsuppgifter, en giltig resurs nyckel och [virtuella Azure-nätverk](cognitive-services-virtual-networks.md " Azure Virtual Networks").

## <a name="containers-for-cognitive-services"></a>Containrar för Cognitive Services

 Cognitive Services tillhandahåller behållare för distribution i Azure-molnet eller lokalt. Läs mer om [Cognitive Services behållare](cognitive-services-container-support.md "Cognitive Services-containrar").

## <a name="regional-availability"></a>Regional tillgänglighet

API: erna i Cognitive Services finns i ett växande nätverk av Microsoft-hanterade data Center. Du kan hitta regional tillgänglighet för varje API i [listan över Azure-regioner](https://azure.microsoft.com/regions "Lista över Azure-regioner").

Letar du efter en region som vi inte stöder ännu? Berätta för oss genom att arkivera en funktions förfrågan i vårt [UserVoice-forum](https://cognitive.uservoice.com/ "UserVoice-forumet").

## <a name="supported-cultural-languages"></a>Kulturella språk som stöds

Cognitive Services har stöd för ett brett utbud av kultur språk på service nivå. Du kan hitta språk tillgängligheten för varje API i [listan språk som stöds](language-support.md "lista över språk som stöds").

## <a name="certifications-and-compliance"></a>Certifieringar och efterlevnad

Cognitive Services har tilldelats certifieringar som CSA STAR-certifiering, FedRAMP måttlig och HIPAA BAA. Du kan [Ladda ned](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942 "ladda ned") certifieringar för dina egna granskningar och säkerhets granskningar.

Om du vill förstå sekretess-och data hantering går du till [säkerhets Center](https://servicetrust.microsoft.com/ "Säkerhetscenter").

## <a name="support"></a>Support

Cognitive Services innehåller flera support alternativ som hjälper dig att gå vidare med att skapa smarta program. Cognitive Services har också en stark grupp utvecklare som kan hjälpa dig att besvara dina frågor. En fullständig lista över tillgängliga alternativ finns i [Cognitive Services support och hjälp alternativ](cognitive-services-support-options.md "Cognitive Services support och hjälp alternativ").

## <a name="next-steps"></a>Nästa steg

* [Skapa ett Cognitive Services-konto](cognitive-services-apis-create-account.md "Skapa ett Cognitive Services-konto")
* [Nyheter i Cognitive Services dokument](whats-new-docs.md "Nyheter i Cognitive Services dokument")