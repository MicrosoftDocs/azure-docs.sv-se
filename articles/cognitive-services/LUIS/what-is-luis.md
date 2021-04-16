---
title: översikt Language Understanding (LUIS)
description: Language Understanding (LUIS) – en molnbaserad API-tjänst som använder maskininlärning till konversationsspråk, naturligt språk för att förutsäga innebörd och extrahera information.
keywords: Azure, artificial intelligence, ai, natural language processing, nlp, natural language understanding, nlu, LUIS, conversational AI, ai chatbot, nlp ai, azure luis
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: overview
ms.date: 04/13/2021
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: f46586b3f120cf191d88b7de9cf8686ca9b16cca
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503780"
---
# <a name="what-is-language-understanding-luis"></a>Vad är Language Understanding Intelligent Service (LUIS)?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Language Understanding (LUIS) är en molnbaserad konversations-AI-tjänst som tillämpar anpassad maskininlärningsinformation på en användares konversationsbaserade, naturliga språktext för att förutsäga den övergripande innebörden och hämta relevant, detaljerad information. LUIS ger åtkomst via dess anpassade [portal,](https://www.luis.ai) [API:er och][endpoint-apis] [SDK-klientbibliotek.](client-libraries-rest-api.md)

För första gången användare följer du de här stegen för att logga in på [LUIS-portalen](sign-in-luis-portal.md "logga in på LUIS-portalen") För att komma igång kan du prova luis-fördefinierade domänappar eller så kan du [skapa din app.](get-started-portal-build-app.md) [](luis-get-started-create-app.md)

Den här dokumentationen innehåller följande artikeltyper:  

* [**Snabbstarter**](luis-get-started-create-app.md) är komma igång-instruktioner som vägleder dig genom att göra begäranden till tjänsten.  
* [**Instruktionsguider**](luis-how-to-start-new-app.md) innehåller instruktioner för att använda tjänsten på mer specifika eller anpassade sätt.  
* [**Begrepp**](artificial-intelligence.md) ger djupgående förklaringar av tjänstens funktioner och funktioner.  
* [**Självstudier**](tutorial-intents-only.md) är längre guider som visar hur du använder tjänsten som en komponent i bredare affärslösningar.  

## <a name="what-does-luis-offer"></a>Vad erbjuder LUIS 

* **Enkelhet:** LUIS avlastar dig från behovet av egen AI-expertis eller tidigare maskininlärningskunskaper. Med bara några få klickningar kan du skapa ett eget konversations-AI-program. Du kan skapa ditt anpassade program genom att följa någon av våra [snabbstarter,](get-started-portal-build-app.md)eller så kan du använda någon av våra [fördefinierade domänappar.](luis-get-started-create-app.md)
* **Säkerhet, sekretess och efterlevnad:** Luis stöds av Azure-infrastrukturen och erbjuder säkerhet, sekretess och efterlevnad i företagsklass. Dina data förblir dina; du kan ta bort dina data när som helst. Dina data krypteras när de lagras. Mer information om detta finns [här](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy).
* **Integrering:** integrera enkelt din LUIS-app med andra Microsoft-tjänster som [Microsoft Bot Framework,](https://docs.microsoft.com/composer/tutorial/tutorial-luis) [QnA Maker](../QnAMaker/choose-natural-language-processing-service.md)och [Speech Service](../Speech-Service/quickstarts/intent-recognition.md).


## <a name="luis-scenarios"></a>LUIS-scenarier
* [Skapa en konversationsrobot](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/conversational-bot)i företagsklass: Den här referensarkitekturen beskriver hur du skapar en konversationsrobot i företagsklass (chattrobot) med hjälp av Azure Bot Framework.
* [Chattrobot för](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/commerce-chatbot)handel: Tillsammans gör Azure Bot Service och Language Understanding-tjänsten att utvecklare kan skapa konversationsgränssnitt för olika scenarier som bankväsende, resor och underhållning.
* [Kontrollera IoT-enheter](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/iot-controlling-devices-with-voice-assistant)med hjälp av en röstassistent: Skapa sömlösa konversationsgränssnitt med alla dina Internettillgängliga enheter – från din anslutna TV eller anslut till enheter i ett anslutet kraftverk.


## <a name="application-development-life-cycle"></a>Livscykel för programutveckling

![Livscykel för LUIS-apputveckling](./media/luis-overview/luis-dev-lifecycle.png "Luis-programmets livscykel för utfasning")

-   **Planera:** Identifiera de scenarier som användarna kan använda ditt program för. Definiera de åtgärder och relevant information som måste identifieras.
-   **Skapa:** Använd redigeringsresursen för att utveckla din app. Börja med att definiera [avsikter och](luis-concept-intent.md) [entiteter](luis-concept-entity-types.md). Lägg sedan till [träningsyttranden](luis-concept-utterance.md) för varje avsikt. 
-   **Testa och förbättra:** Börja testa din modell med andra yttranden för att få en känsla för hur appen fungerar och du kan avgöra om någon förbättring behövs. Du kan förbättra ditt program genom att följa dessa [metodtips.](luis-concept-best-practices.md) 
-   **Publicera:** Distribuera din app för förutsägelse och fråga slutpunkten med hjälp av din förutsägelseresurs. Läs mer om redigerings- och förutsägelseresurser [här.](luis-how-to-azure-subscription.md#luis-resources) 
-   **Anslut:** Anslut till andra tjänster, till exempel [Microsoft Bot Framework,](https://docs.microsoft.com/composer/tutorial/tutorial-luis) [QnA Maker](../QnAMaker/choose-natural-language-processing-service.md)och [Speech Service](../Speech-Service/quickstarts/intent-recognition.md). 
-    [Förfina: Granska slutpunktsyttranden](luis-concept-review-endpoint-utterances.md) för att förbättra ditt program med verkliga exempel

Läs mer om att planera och skapa ditt program [här.](luis-how-plan-your-app.md)

## <a name="next-steps"></a>Nästa steg

* [Vad är nytt med](whats-new.md "Nyheter") tjänsten och dokumentationen
* [Skapa en LUIS-app](tutorial-intents-only.md)
* [API-referens][endpoint-apis]
* [Bästa praxis](luis-concept-best-practices.md)
* [Utvecklarresurser](developer-reference-resource.md "Resurser för utvecklare") för LUIS.
* [Planera din app med](luis-how-plan-your-app.md "Planera din app") avsikter [och entiteter](luis-concept-intent.md "Avseenden") . [](luis-concept-entity-types.md "Enheter")

[bot-framework]: /bot-framework/
[flow]: /connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/
