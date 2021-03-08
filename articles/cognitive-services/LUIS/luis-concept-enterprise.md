---
title: Enterprise-koncept – LUIS
titleSuffix: Azure Cognitive Services
description: Förstå design koncept för stora LUIS-appar eller flera appar, inklusive LUIS och QnA Maker tillsammans.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: b8110323afda2ad445ffe279030ee7f3035e2b71
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2021
ms.locfileid: "102455407"
---
# <a name="enterprise-strategies-for-a-luis-app"></a>Företags strategier för en LUIS-app
Granska dessa design strategier för företags appen.

## <a name="when-you-expect-luis-requests-beyond-the-quota"></a>När du förväntar dig LUIS-begäranden utanför kvoten

LUIS har en månatlig kvot och en kvot på per sekund, baserat på Azure-resursens pris nivå. 

Om din LUIS-app begär frekvens överskrider den tillåtna [kvoten](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)kan du:

* Sprid belastningen till fler LUIS-appar med [samma app-definition](#use-multiple-apps-with-same-app-definition). Detta inkluderar, om du vill, att köra LUIS från en [behållare](luis-container-howto.md). 
* Skapa och [tilldela flera nycklar](#assign-multiple-luis-keys-to-same-app) till appen. 

### <a name="use-multiple-apps-with-same-app-definition"></a>Använd flera appar med samma app-definition
Exportera den ursprungliga LUIS-appen och importera sedan appen tillbaka till separata appar. Varje app har sitt eget app-ID. När du publicerar, i stället för att använda samma nyckel i alla appar, skapar du en separat nyckel för varje app. Utjämna belastningen över alla appar så att ingen enskild app blir överbelastad. Lägg till [Application Insights](./luis-csharp-tutorial-bf-v4.md) för att övervaka användningen. 

För att få samma främsta avsikt mellan alla appar, se till att det är tillräckligt brett för att förutsäga att LUIS inte är tillräckligt bred, vilket ger olika resultat mellan appar för smärre variationer i yttranden. 

När du tränar dessa appar på samma nivå måste du [träna med alla data](luis-how-to-train.md#train-with-all-data).

Ange en enda app som huvud server. Alla yttranden som föreslås för granskning ska läggas till i huvudappen och sedan flyttas tillbaka till alla andra appar. Detta är antingen en fullständig export av appen eller läser in den märkta yttranden från huvud servern till de underordnade. Inläsning kan göras från antingen [Luis](luis-reference-regions.md) -webbplatsen eller redigerings-API: et för en [enskild uttryck](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08) eller för en [batch](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09). 

Schemalägg en regelbunden granskning, till exempel varannan vecka, [slut punkts yttranden](luis-how-to-review-endpoint-utterances.md) för aktiv utbildning, och omträna och publicera igen. 

### <a name="assign-multiple-luis-keys-to-same-app"></a>Tilldela flera LUIS-nycklar till samma app
Om din LUIS-app tar emot fler slut punkts träffar än kvoten för en enskild nyckel kan du skapa och tilldela fler nycklar till LUIS-appen. Skapa en Traffic Manager eller belastningsutjämnare för att hantera slut punkts frågorna över slut punkts nycklarna. 

## <a name="when-your-monolithic-app-returns-wrong-intent"></a>När monolitisk-appen returnerar fel avsikt
Om din app är avsedd att förutsäga en mängd olika användar yttranden bör du överväga att implementera [sändnings modellen](#dispatch-tool-and-model). Genom att dela upp en monolitisk-app kan LUIS fokusera på identifiering mellan syften i stället för att bli förvirrande mellan olika syften i den överordnade appen och underordnade appar. 

Schemalägg en regelbunden [granskning av slut punkts yttranden](luis-how-to-review-endpoint-utterances.md) för aktiv inlärning, till exempel varannan vecka, och sedan omträna och publicera igen. 

## <a name="when-you-need-to-have-more-than-500-intents"></a>När du behöver fler än 500-avsikter
Anta att du utvecklar en Office-assistent som har över 500-avsikter. Om 200-avsikter relaterar till schemaläggning av möten, 200 handlar om påminnelser, 200 för att få information om kollegor och 200 för att skicka e-post, grupp avsikter så att varje grupp finns i en enda app och skapa sedan en app på översta nivån som innehåller varje avsikt. Använd [sändnings modellen](#dispatch-tool-and-model) för att bygga appen på den översta nivån. Ändra sedan din robot till att använda det sammanhängande anropet, som du ser i [sändnings modellens självstudier](/azure/bot-service/bot-builder-tutorial-dispatch?tabs=cs&view=azure-bot-service-4.0). 

## <a name="when-you-need-to-combine-several-luis-and-qna-maker-apps"></a>När du behöver kombinera flera LUIS-och QnA Maker-appar
Om du har flera LUIS-och QnA Maker-appar som behöver svara på en bot använder du [sändnings modellen](#dispatch-tool-and-model) för att skapa appen på den översta nivån.  Ändra sedan din robot till att använda det sammanhängande anropet, som du ser i [sändnings modellens självstudier](/azure/bot-service/bot-builder-tutorial-dispatch?tabs=cs&view=azure-bot-service-4.0). 

## <a name="dispatch-tool-and-model"></a>Leverans verktyg och-modell
Använd kommando rads verktyget [Dispatch][dispatch-tool] som finns i [BotBuilder-tools](https://github.com/Microsoft/botbuilder-tools) för att kombinera flera LUIS och/eller QNA Maker appar till en överordnad Luis-app. Med den här metoden kan du ha en överordnad domän, inklusive alla ämnen och olika underordnade ämnes domäner i separata appar. 

![Konceptuell bild av sändnings arkitektur](./media/luis-concept-enterprise/dispatch-architecture.png)

Den överordnade domänen anges i LUIS med en version som har angetts `Dispatch` i listan över appar. 

Chatt-roboten tar emot uttryck och skickar sedan till den överordnade LUIS-appen för förutsägelse. Det mest förväntade syftet från den överordnade appen avgör vilken LUIS-underordnad app som anropas härnäst. Chatt-roboten skickar uttryck till den underordnade appen för en mer detaljerad förutsägelse.

Förstå hur den här hierarkin av anrop görs från Bot Builder v4 [dispatcher-Application-självstudie](/azure/bot-service/bot-builder-tutorial-dispatch?tabs=cs&view=azure-bot-service-4.0).  

### <a name="intent-limits-in-dispatch-model"></a>Begränsningar för avsikt i sändnings modellen
Ett sändnings program har 500 sändnings källor som motsvarar 500-intentor, som Max. 

## <a name="more-information"></a>Mer information

* [Robot Framework SDK](https://github.com/Microsoft/botframework)
* [Själv studie kurs om sändnings modell](/azure/bot-service/bot-builder-tutorial-dispatch?tabs=cs&view=azure-bot-service-4.0)
* [Skicka CLI](https://github.com/Microsoft/botbuilder-tools)
* Robot exempel för sändnings modell – [.net](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/14.nlp-with-dispatch), [Node.js](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/14.nlp-with-dispatch)

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [testar en batch](luis-how-to-batch-test.md)

[dispatcher-application-tutorial]: /azure/bot-service/bot-builder-tutorial-dispatch
[dispatch-tool]: https://aka.ms/dispatch-tool