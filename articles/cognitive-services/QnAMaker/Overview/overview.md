---
title: Vad är QnA Maker-tjänsten?
description: QnA Maker är en molnbaserad NLP-tjänst som enkelt skapar ett naturligt samtals lager över dina data. Den kan användas för att hitta det mest lämpliga svaret för alla angivna språk från en särskild språk version, från din anpassade kunskaps bas (KB) information.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: overview
ms.date: 01/22/2021
ms.custom: cog-serv-seo-aug-2020
keywords: QNA Maker, robotar med låg kod, flera konversationer
ms.openlocfilehash: a75b5939e8848ca898d7279b55ab2c4faa4ed72a
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98919569"
---
# <a name="what-is-qna-maker"></a>Vad är QnA Maker?

QnA Maker är en molnbaserad NLP-tjänst (Natural Language Processing) som gör att du kan skapa ett naturligt samtals lager över dina data. Den används för att hitta det lämpligaste svaret för indata från din anpassade kunskaps bas (KB) information.

QnA Maker används ofta för att bygga konversations klient program, bland annat sociala medie program, chatt-robotar och talbaserade Skriv bords program.

## <a name="when-to-use-qna-maker"></a>När du ska använda QnA Maker

* **När du har statisk information** – Använd QNA Maker när du har statisk information i kunskaps basen med svar. Den här kunskaps basen är anpassad efter dina behov, som du har skapat med dokument som [PDF-filer och URL: er](../Concepts/data-sources-and-content.md).
* **När du vill ge samma svar på en begäran, fråga eller kommando** – när olika användare skickar samma fråga returneras samma svar.
* **När du vill filtrera statisk information baserat på meta-information** – Lägg till [metadata](../how-to/metadata-generateanswer-usage.md) -taggar för att tillhandahålla ytterligare filtrerings alternativ som är relevanta för klient programmets användare och information. Common metadata-information inkluderar [CHI2TEST](../how-to/chit-chat-knowledge-base.md), innehålls typ, format, innehålls syfte och innehållets aktualitet.
* **När du vill hantera en robot konversation som innehåller statisk information** – din kunskaps bas tar en användares konversations text eller kommando och svarar på den. Om svaret är en del av ett fördefinierat konversations flöde, som representeras i din kunskaps bas med [multi-turn-kontext](../how-to/multiturn-conversation.md), kan roboten enkelt tillhandahålla det här flödet.

## <a name="what-is-a-knowledge-base"></a>Vad är en kunskaps bas?

QnA Maker [importerar ditt innehåll](../Concepts/plan.md) till en kunskaps bas för fråga-och svars par. Import processen extraherar information om relationen mellan delarna i det strukturerade och det delvis strukturerade innehållet för att innebära relationer mellan frågan och svars paren. Du kan redigera de här frågorna och svars paren eller lägga till nya par.

Innehållet i frågan och svars paret innehåller:
* Alla alternativa formulär för frågan
* Metadata-taggar som används för att filtrera svars alternativ under sökningen
* Följ anvisningarna för att fortsätta Sök förfiningen

![Exempel fråga och svara med metadata](../media/qnamaker-overview-learnabout/example-question-and-answer-with-metadata.png)

När du har publicerat din kunskaps bas skickar ett klient program en användares fråga till din slut punkt. Din QnA Maker tjänst bearbetar frågan och svarar med det bästa svaret.

## <a name="create-a-chat-bot-programmatically"></a>Skapa en chatt-robot program mässigt

När en QnA Maker kunskaps bas har publicerats skickar ett klient program en fråga till din kunskaps bas slut punkt och tar emot resultatet som ett JSON-svar. Ett vanligt klient program för QnA Maker är en chatt-robot.

![Ställ en robot till en fråga och få svar från kunskaps bas innehållet](../media/qnamaker-overview-learnabout/bot-chat-with-qnamaker.png)

|Steg|Action|
|:--|:--|
|1|Klient programmet skickar användarens _fråga_ (text i sina egna ord), "Hur gör jag för att program mässigt uppdatera min kunskaps bas?" till din kunskaps bas slut punkt.|
|2|QnA Maker använder den utbildade kunskaps basen för att tillhandahålla rätt svar och eventuella uppföljnings instruktioner som kan användas för att förfina sökningen efter det bästa svaret. QnA Maker returnerar ett JSON-formaterat svar.|
|3|Klient programmet använder JSON-svaret för att fatta beslut om hur du ska fortsätta konversationen. Dessa beslut kan omfatta det bästa svaret och Visa fler alternativ för att förfina sökningen efter det bästa svaret. |
|||

## <a name="build-low-code-chat-bots"></a>Bygg robotar för att chatta med låga koder

På QnA Makers portalen får du en fullständig redigerings upplevelse för kunskaps basen. Du kan importera dokument i det aktuella formuläret till din kunskaps bas. Dessa dokument (till exempel vanliga frågor och svar, produkt handböcker, kalkyl blad eller webb sidor) konverteras till frågor och svars par. Varje par analyseras för uppföljnings instruktioner och är anslutna till andra par. Det slutliga _markdown_ -formatet stöder omfattande presentationer, inklusive bilder och länkar.

När din kunskaps bas har redigerats publicerar du kunskaps basen till en fungerande [Azure Web App-robot](https://azure.microsoft.com/services/bot-service/) utan att skriva någon kod. Testa din robot i [Azure Portal](https://portal.azure.com) eller ladda ned den och fortsätt utveckla.

## <a name="high-quality-responses-with-layered-ranking"></a>Hög kvalitets svar med skikt rangordning

QnA Makers systemet är ett skiktat rangordnings sätt. Data lagras i Azure Search, som också fungerar som det första rangordnings skiktet. De främsta resultaten från Azure Search skickas sedan genom QnA Maker NLP omrangordnings modell för att producera de slutliga resultaten och förtroende poängen.

## <a name="multi-turn-conversations"></a>Konversation med flera turer

QnA Maker tillhandahåller frågor med flera frågor och aktiv utbildning för att hjälpa dig att förbättra dina grundläggande frågor och svar-par.

**Med frågor med flera turn** får du möjlighet att ansluta frågor och svar-par. Den här anslutningen gör att klient programmet kan tillhandahålla ett bästa svar och ger fler frågor för att förfina sökningen efter ett slut svar.

När kunskaps basen tar emot frågor från användare på den publicerade slut punkten, QnA Maker använda **sig** av de verkliga frågorna för att föreslå ändringar i kunskaps basen för att förbättra kvaliteten.

## <a name="development-lifecycle"></a>Utvecklingscykel

QnA Maker ger redigering, utbildning och publicering tillsammans med samarbets behörigheter för att integrera i livs cykeln för fullständig utveckling.

> [!div class="mx-imgBorder"]
> ![Konceptuell bild av utvecklings cykel](../media/qnamaker-overview-learnabout/development-cycle.png)


## <a name="complete-a-quickstart"></a>Slutför en snabb start

Vi erbjuder snabb starter i de flesta populära programmeringsspråk, som var och en har utformats för att lära dig grundläggande design mönster, och du kan köra kod på mindre än 10 minuter. Se följande lista för snabb starten för varje funktion.

* [Kom igång med QnA Maker klient bibliotek](../quickstarts/quickstart-sdk.md)
* [Kom igång med QnA Maker Portal](../quickstarts/create-publish-knowledge-base.md)

## <a name="next-steps"></a>Nästa steg
QnA Maker innehåller allt du behöver för att bygga, hantera och distribuera din anpassade kunskaps bas.

> [!div class="nextstepaction"]
> [Granska de senaste ändringarna](../whats-new.md)
