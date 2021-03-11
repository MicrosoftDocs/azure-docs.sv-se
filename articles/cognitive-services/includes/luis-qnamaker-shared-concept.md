---
title: ta med fil
description: ta med fil
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.custom: include file
ms.date: 04/16/2020
ms.openlocfilehash: b2b405e7f6babf84ef8297310a5a7a34c3f2dc11
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102623273"
---
Cognitive Services tillhandahåller två bearbetnings tjänster för naturligt språk, [language Understanding](../luis/what-is-luis.md) och [QNA Maker](../qnamaker/overview/overview.md), var och en med olika ändamål. Förstå när du ska använda varje tjänst och hur de ska komplettera varandra.

Med naturlig språk bearbetning (NLP) kan ditt klient program, till exempel en chatt-robot, arbeta med dina användare med hjälp av naturligt språk. En användare anger en mening eller en fras. Användarens text kan ha dålig grammatik, stavning och interpunktion. Kognitiva tjänster kan fungera i användar meningen ändå, vilket returnerar information som chatt-roboten behöver för att hjälpa användaren.

## <a name="cognitive-services-with-nlp"></a>Cognitive Services med NLP

Language Understanding (LUIS) och QnA Maker ger NLP. Klient programmet skickar in text på naturligt språk. Tjänsten använder texten, bearbetar den och returnerar ett resultat.

## <a name="when-to-use-each-service"></a>När du ska använda varje tjänst

Language Understanding (LUIS) och QnA Maker lösa olika problem. LUIS bestämmer avsikten för en användares text (kallas även en uttryck), medan QnA Maker bestämmer svaret på användarens text (kallas även fråga).

För att kunna välja rätt tjänst måste du förstå användar texten som kommer från klient programmet och vilken information som klient programmet behöver för att hämta från kognitiva tjänster.

Om din Chat-robot tar emot texten `How do I get to the Human Resources building on the Seattle North campus?` använder du diagrammet nedan för att förstå hur varje tjänst fungerar med texten.

|Tjänst|Klient programmet fastställer|
|--|--|
|LUIS|**Bestämmer användarens avsikt** för text – tjänsten returnerar inte svaret på frågan. Den här texten klassificeras exempelvis som matchning av `FindLocation` avsikten.<br>|
|QnA Maker|**Returnerar svaret till frågan** från en anpassad kunskaps bas. Till exempel bestäms den här texten som en fråga med det statiska textsvaret  `Get on the #9 bus and get off at Franklin street` .|
|||

> [!div class="mx-imgBorder"]
> ![Informations grafiken för att avgöra när du ska använda LUIS och när du ska använda QnA Maker](./luis-qna-maker-together-decision.png)

## <a name="when-do-you-use-luis"></a>När använder du LUIS?

Använd LUIS när du behöver känna till avsikten med uttryck som ingår i en process i chattroboten. Om du fortsätter med exempel texten, `How do I get to the Human Resources building on the Seattle North campus?` och du vet att användarens avsikt är att hitta en plats, kan du skicka information om uttryck (som dras ut med entiteter) till en annan tjänst, till exempel en transport server, för att få svaret.

Du behöver inte kombinera LUIS och QnA Maker för att avgöra avsikten.

Du kan kombinera de två tjänsterna för den här uttryck, om Chat-roboten måste bearbeta texten baserat på avsikter och entiteter (med LUIS) och hitta det angivna statiska textsvaret (med QnA Maker).

## <a name="when-do-you-use-qna-maker"></a>När använder du QnA Maker?

Använd QnA Maker när du har en statisk kunskapsbas med svar. Den här kunskapsbasen är anpassad efter dina behov, som du har skapat med dokument som PDF-filer och webbadresser.

Fortsätt med exemplet uttryck, `How do I get to the Human Resources building on the Seattle North campus?` Skicka texten som en fråga till din publicerade QNA Maker-tjänst och få det bästa svaret.

Du behöver inte kombinera LUIS och QnA Maker för att fastställa svaret på frågan.

Du kan kombinera de två tjänsterna för den här uttryck, om Chat-roboten måste bearbeta texten baserat på avsikter och entiteter (med LUIS) samt hitta svaret (med hjälp av QnA Maker).

## <a name="use-both-services-when-your-knowledge-base-is-incomplete"></a>Använd båda tjänsterna när din kunskaps bas är ofullständig

Om du skapar din QnA Maker kunskaps bas men vet att ämnes domänen ändras (till exempel information om tiden) kan du kombinera LUIS-och QnA Maker-tjänster. På så sätt kan du använda informationen i din kunskaps bas, men du kan också använda LUIS för att ta reda på en användares avsikt. När klient programmet har avsikten kan det begära relevant information från en annan källa.

Ditt klient program måste övervaka både LUIS och QnA Maker svar för Scores. Om poängen från QnA Maker under vissa valfria gränser, använder du informationen om avsikten och entiteten som returnerades från LUIS för att skicka informationen till en tredjepartstjänst.

Om du fortsätter med exempel texten, `How do I get to the Human Resources building on the Seattle North campus?` antar vi att QNA Maker returnerar en poäng med låg exakthet. Använd den avsikt som returnerades från LUIS `FindLocation` och eventuella extraherade entiteter, till exempel `Human Resources building` och `Seattle North campus` , för att skicka den här informationen till en mappnings-eller Sök tjänst för ett annat svar.

Du kan presentera detta svar från tredje part för användaren för verifiering. När du har en användares godkännande kan du gå tillbaka till QnA Maker för att lägga till informationen för att utveckla din kunskap.

## <a name="use-both-services-when-your-chat-bot-needs-more-information"></a>Använd båda tjänsterna när din chatt-robot behöver mer information

Om din Chat-robot behöver mer information än vad som finns i tjänsten kan du använda båda tjänsterna och bearbeta båda svaren i klient programmet för att fortsätta via ett besluts träd.

Använd verktyget bot Framework **[DISPATCH CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)** för att hjälpa till att bygga en process som fungerar med båda tjänsterna. Det här verktyget skapar en LUIS app av avsikter som skickar mellan LUIS och QnA Maker som underordnade appar. [Läs mer](/azure/bot-service/bot-builder-tutorial-dispatch?tabs=cs) om integrering med Luis-, QNA Maker-och bot-ramverket.

Använd robot Builder-exemplet, **NLP med sändning**, i [C#](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/14.nlp-with-dispatch) eller [Node.js](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/14.nlp-with-dispatch)för att implementera den här typen av chatt-robot.

## <a name="best-practices"></a>Bästa praxis

Implementera bästa praxis för varje tjänst:

* Metod tips för [Luis](../luis/luis-concept-best-practices.md)
* Metod tips för [QNA Maker](../qnamaker/concepts/best-practices.md)

## <a name="see-also"></a>Se även

* [Language Understanding (LUIS)](../luis/what-is-luis.md)
* [QnA Maker](../qnamaker/overview/overview.md)
* [Skicka CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)
* [Robot Framework-exempel](https://github.com/Microsoft/BotBuilder-Samples)
* [Azure bot-tjänst](/azure/bot-service/bot-service-overview-introduction)
* [Azure bot-emulator](https://github.com/Microsoft/BotFramework-Emulator)
* [Webb Chat för bot Framework](https://github.com/microsoft/BotFramework-WebChat)