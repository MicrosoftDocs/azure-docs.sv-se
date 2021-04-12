---
title: Vad är Language Understanding Intelligent Service (LUIS)?
description: Language Understanding (LUIS) – en molnbaserad API-tjänst som använder maskin inlärning till konversationer, naturligt språk för att förutsäga innebörd och extrahering av information.
keywords: Azure, artificiell intelligens, AI, naturlig språk bearbetning, NLP, natur språks förståelse, NLU, LUIS, konversations AI, AI chattrobot, NLP AI, Azure Luis
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: overview
ms.date: 03/22/2021
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: f32f58bebc0a7d64443259981590e368b109b19b
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278957"
---
# <a name="what-is-language-understanding-luis"></a>Vad är Language Understanding Intelligent Service (LUIS)?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Language Understanding (LUIS) är en molnbaserad diskussions AI-tjänst som använder anpassad maskin inlärnings information till en användares konversation, naturligt språk text för att förutsäga den övergripande innebörden och hämta relevant detaljerad information.

Ett klientprogram för LUIS är alla konversationsanpassade program som kommunicerar med en användare på naturligt språk för att utföra en uppgift. Exempel på klient program är appar för sociala medier, AI-chattrobotar och tal aktiverade Skriv bords program.

![Konceptuell bild av 3 klient program som arbetar med Cognitive Services Language Understanding (LUIS)](./media/luis-overview/luis-entry-point.png "Konceptuell bild av 3 klient program som arbetar med Cognitive Services Language Understanding (LUIS)")

Den här dokumentationen innehåller följande artikel typer:  

* [**Snabb starter**](luis-get-started-create-app.md) hjälper dig att komma igång med instruktioner för att göra förfrågningar till tjänsten.  
* [**Instruktions guider**](luis-how-to-start-new-app.md) innehåller instruktioner för att använda tjänsten på mer exakta eller anpassade sätt.  
* [**Begreppen**](artificial-intelligence.md) ger djupgående förklaringar av tjänst funktionerna och funktionerna.  
* [**Självstudier**](tutorial-intents-only.md) är längre guider som visar hur du använder tjänsten som en komponent i bredare affärs lösningar.  

## <a name="use-luis-in-a-chat-bot"></a>Använda LUIS i en chattrobot

<a name="Accessing-LUIS"></a>

När Azure LUIS-appen har publicerats skickar ett klient program yttranden (text) till LUISs slut punkts- [API][endpoint-apis] för naturlig språk bearbetning och tar emot resultatet som JSON-svar. Ett vanligt klientprogram för LUIS är en chattrobot.


![Konceptuell bilder av LUIS fungerar med chatt-roboten för att förutsäga användar text med naturligt språk förståelse (NLP)](./media/luis-overview/LUIS-chat-bot-request-response.svg "Konceptuell bilder av LUIS fungerar med chatt-roboten för att förutsäga användar text med naturligt språk förståelse (NLP")

|Steg|Action|
|:--|:--|
|1|Klientprogrammet skickar användarens _yttrande_ (text med användarens egna ord), "I want to call my HR rep." (”Jag vill ringa HR-personalen.”) till LUIS-slutpunkten som en HTTP-begäran.|
|2|Med LUIS kan du utforma dina anpassade språk modeller för att lägga till information i ditt program. Datorns förkonfigurerade språk modeller tar användarens ostrukturerade indata och returnerar ett JSON-formaterat svar, med en topp avsikt `HRContact` . Det minsta JSON-slutpunktssvaret innehåller frågeyttrandet och avsikten med högsta poäng. Det kan också extrahera data, till exempel entiteten _kontakt typ_ .|
|3|Klientprogrammet använder JSON-svaret för att fatta beslut om hur användarens begäranden sak uppfyllas. Dessa beslut kan innehålla besluts träd i bot Framework-koden och anrop till andra tjänster. |

LUIS-appen tillhandahåller information så att klientprogrammet kan fatta smarta beslut. LUIS har inte dessa alternativ.

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-understanding-nlu"></a>Natur språks förståelse (NLU)

[Luis tillhandahåller artificiell intelligens (AI)](artificial-intelligence.md "LUIS tillhandahåller artificiell intelligens (AI)") i form av NLU, en delmängd av naturliga språk bearbetning AI.

LUIS-appen innehåller en domänbaserad naturlig språk modell. Du kan starta LUIS-appen med en fördefinierad domänmodell, bygga din egen modell eller blanda delar av en fördefinierad modell med egen anpassad information.

* LUIS med **fördefinierad modell** har många fördefinierade domänmodeller som innehåller avsikter, yttranden och fördefinierade yttranden. Du kan använda de fördefinierade entiteterna utan att behöva använda den fördefinierade modellens avsikter och yttranden. [Fördefinierade domänmodeller](./howto-add-prebuilt-models.md "Färdiga domän modeller") innehåller hela designen för dig och är ett bra sätt att börja använda LUIS snabbt.

* **Anpassad modell** LUIS ger dig flera olika sätt att identifiera dina egna anpassade modeller, inklusive avsikter och entiteter. Entiteter innehåller enheter för maskin inlärning, speciella eller litterala entiteter och en kombination av Machine-Learning och Literal.

Lär dig mer om [NLP AI](artificial-intelligence.md "NLP")och det Luis NLU.

## <a name="step-1-design-and-build-your-model"></a>Steg 1: utforma och skapa din modell

Utforma din modell med kategorier av **[användar avsikter som kallas](luis-concept-intent.md "avsikter")** avsikter. Varje avsikt behöver exempel på **[yttranden](luis-concept-utterance.md "yttranden")** från användaren. Varje uttryck kan tillhandahålla data som måste extraheras med [enheter för maskin inlärning](luis-concept-entity-types.md#effective-machine-learned-entities "enheter för maskin inlärning").

|Exempel på användaryttrande|Avsikt|Extraherade data|
|-----------|-----------|-----------|
|`Book a flight to Seattle?`|BookFlight (Boka flyg)|Seattle|
|`When does your store open?`|StoreHoursAndLocation (Butikens öppettider och plats)|open (öppen)|
|`Schedule a meeting at 1pm with Bob in Distribution`|ScheduleMeeting (Boka möte)|1pm, Bob (kl. 13, Bob)|

Skapa modellen med [redigerings](https://go.microsoft.com/fwlink/?linkid=2092087 "redigering") -API: erna, eller med **[Luis-portalen](https://www.luis.ai "LUIS-portalen")** eller båda. Lär dig mer om hur du skapar med [portalen](get-started-portal-build-app.md "portal") och [SDK-klient biblioteken](./client-libraries-rest-api.md?pivots=rest-api "Bibliotek för SDK-klient").

## <a name="step-2-get-the-query-prediction"></a>Steg 2: Hämta frågan förutsägelse

När appens modell har tränats och publicerats till slut punkten skickar ett klient program (till exempel en chatt-robot) yttranden till API: et för förutsägelse [slut punkt](https://go.microsoft.com/fwlink/?linkid=2092356 "slutpunkt") . API: t tillämpar modellen på uttryck för analys och svarar med förutsägelse resultatet i JSON-format.

Det minsta JSON-slutpunktssvaret innehåller frågeyttrandet och avsikten med högsta poäng. Det kan också extrahera data som följande **kontakt typ** entitet och övergripande sentiment.

```JSON
{
    "query": "I want to call my HR rep",
    "prediction": {
        "topIntent": "HRContact",
        "intents": {
            "HRContact": {
                "score": 0.8582669
            }
        },
        "entities": {
            "Contact Type": [
                "call"
            ]
        },
        "sentiment": {
            "label": "neutral",
            "score": 0.5
        }
    }
}
```

## <a name="step-3-improve-model-prediction"></a>Steg 3: förbättra modell förutsägelsen

När din LUIS-app har publicerats och tar emot verkliga användar yttranden ger LUIS [aktiv inlärning](luis-concept-review-endpoint-utterances.md "aktiv inlärning") av slut punkts yttranden för att förbättra förutsägelse noggrannhet. Granska dessa förslag som en del av ditt vanliga underhålls arbete i utvecklings livs cykeln.

<a name="using-luis"></a>

## <a name="development-lifecycle-and-tools"></a>Livs cykel och verktyg för utveckling
LUIS innehåller verktyg, versions hantering och samarbete med andra LUIS-författare som kan integreras i hela [utvecklings livs cykeln](luis-concept-app-iteration.md "livs cykel för utveckling").

Language Understanding (LUIS), som REST API, kan användas med valfri produkt, tjänst eller ramverk med en HTTP-begäran. LUIS tillhandahåller också klient bibliotek (SDK: er) för flera viktigaste programmeringsspråk. Läs mer om de [resurser](developer-reference-resource.md "resurser för utvecklare") som tillhandahålls av utvecklare.

Verktyg för att snabbt och enkelt använda LUIS med en robot:
* [Luis CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS "LUIS CLI") NPM-paketet ger redigering och förutsägelse med antingen ett fristående kommando rads verktyg eller som import.
* [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen "LUISGen") LUISGen är ett verktyg för att generera starkt typbestämd C#- och TypeScript-källkod från en exporterad LUIS-modell.
* Med [Dispatch](https://aka.ms/dispatch-tool "Dispatch") kan flera LUIS- och QnA Maker-appar användas via en överordnad app som använder en dispatcher-modell.
* [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown "LUDown") LUDown är ett kommando rads verktyg som hjälper dig att hantera språk modeller för din robot.

## <a name="integrate-with-a-bot"></a>Integrera med en robot

Använd [Azure bot-tjänsten](/azure/bot-service/ "Azure bot-tjänst") med [Microsoft bot Framework](https://dev.botframework.com/ "Microsoft Bot Framework") för att bygga och distribuera en chatt-robot. Utforma och utveckla med det grafiska gränssnitts verktyget, [Composer](/composer/ "Composer")eller [arbetsrobots exempel](https://github.com/microsoft/BotBuilder-Samples "fungerande robot exempel") som är utformade för de främsta bot scenarierna.

## <a name="integrate-with-other-cognitive-services"></a>Integrera med andra Cognitive Services

Andra Cognitive Services-tjänster som används med LUIS:
* Med [QnA Maker](../QnAMaker/overview/overview.md "QnA Maker") kan flera typer av text kombineras till en kunskapsbas med frågor och svar.
* [Speech Service](../Speech-Service/overview.md "Tjänst för taligenkänning") konverterar begäranden med talat språk till text.

LUIS tillhandahåller funktioner från Textanalys som en del av dina befintliga LUIS-resurser. Den här funktionen omfattar [sentiment analys](luis-how-to-publish-app.md#configuring-publish-settings "sentiment-analys") och [extrahering av nyckel fraser](luis-reference-prebuilt-keyphrase.md "extrahering av nyckel fraser") med den fördefinierade nyckel frasen entitet.

## <a name="learn-with-the-quickstarts"></a>Lär dig med snabb starter

Lär dig mer om LUIS med praktiska snabb starter med hjälp av [portalen](get-started-portal-build-app.md "portal") och [klient biblioteken för SDK](./client-libraries-rest-api.md?pivots=rest-api "Bibliotek för SDK-klient").


## <a name="deploy-on-premises-using-docker-containers"></a>Distribuera lokalt med Docker-behållare

[Använd Luis-behållare](luis-container-howto.md) för att distribuera API-funktioner lokalt. Med dessa Docker-behållare kan du ta tjänsten närmare dina data för efterlevnad, säkerhet eller andra drift orsaker.

## <a name="next-steps"></a>Nästa steg

* [Vad är nytt](whats-new.md "Nyheter") med tjänsten och dokumentationen
* [Planera din app](luis-how-plan-your-app.md "Planera din app") med [avsikter](luis-concept-intent.md "avsikter") och [entiteter](luis-concept-entity-types.md "poster").

[bot-framework]: /bot-framework/
[flow]: /connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/