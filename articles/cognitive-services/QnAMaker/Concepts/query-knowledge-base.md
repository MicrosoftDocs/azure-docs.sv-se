---
title: Fråga kunskaps basen – QnA Maker
description: 'En kunskaps bas måste publiceras. När den har publicerats frågas kunskaps basen vid körnings förutsägelse slut punkten med generateAnswer-API: et.'
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: c723d1446c90290929bc8cad066b4744e284f3f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103008679"
---
# <a name="query-the-knowledge-base-for-answers"></a>Fråga kunskaps basen efter svar

En kunskaps bas måste publiceras. När den har publicerats frågas kunskaps basen vid körnings förutsägelse slut punkten med generateAnswer-API: et. Frågan innehåller frågetexten och andra inställningar för att hjälpa QnA Maker välja den bästa möjliga matchningen med ett svar.

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>Hur QnA Maker bearbetar en användar fråga för att välja det bästa svaret

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/v1)

Den utbildade och [publicerade](../quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) QNA Maker kunskaps basen tar emot en användar fråga, från en robot eller något annat klient program, i [GenerateAnswer-API: et](../how-to/metadata-generateanswer-usage.md). Följande diagram illustrerar processen när användar frågan tas emot.

![Ranknings modell processen för en användar fråga](../media/qnamaker-concepts-knowledgebase/ranker-v1.png)

### <a name="ranker-process"></a>Rangordnings process

Processen beskrivs i följande tabell.

|Steg|Syfte|
|--|--|
|1|Klient programmet skickar användar frågan till [GenerateAnswer-API: et](../how-to/metadata-generateanswer-usage.md).|
|2|QnA Maker förbearbetar användar frågan med språk identifiering, stavnings-och ord separatorer.|
|3|Den här förbearbetningen görs för att ändra användar frågan för bästa Sök resultat.|
|4|Den här ändrade frågan skickas till ett Azure Kognitiv sökning-index som tar emot `top` antalet resultat. Om rätt svar inte finns i dessa resultat ökar du värdet `top` något. Normalt är ett värde på 10 för `top` Works i 90% av frågorna. Azure Search-filter [stoppar ord](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/STOPWORDS.md) i det här steget.|
|5|QnA Maker använder syntaktiskt och semantiskt baserat funktionalisering för att fastställa likheten mellan användar frågan och hämtade QnA-resultat.|
|6|Den dator som är inlärt i rangordnings modellen använder de olika funktionerna, från steg 5, för att fastställa förtroende poängen och den nya rangordnings ordningen.|
|7|De nya resultaten returneras till klient programmet i rangordnings ordning.|
|||

Funktioner som används inkluderar, men är inte begränsade till semantik på Word-nivå, prioritet på term nivå i en sökkorpus och djup lärt semantiska modeller för att fastställa likhet och relevans mellan två text strängar.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/v2)

Den utbildade och [publicerade](../quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) QNA Maker kunskaps basen tar emot en användar fråga, från en robot eller något annat klient program, i [GenerateAnswer-API: et](../how-to/metadata-generateanswer-usage.md). Följande diagram illustrerar processen när användar frågan tas emot.

![Ranknings modell processen för för hands versionen av användar frågan](../media/qnamaker-concepts-knowledgebase/ranker-v2.png)

### <a name="ranker-process"></a>Rangordnings process

Processen beskrivs i följande tabell.

|Steg|Syfte|
|--|--|
|1|Klient programmet skickar användar frågan till [GenerateAnswer-API: et](../how-to/metadata-generateanswer-usage.md).|
|2|QnA Maker förbearbetar användar frågan med språk identifiering, stavnings-och ord separatorer.|
|3|Den här förbearbetningen görs för att ändra användar frågan för bästa Sök resultat.|
|4|Den här ändrade frågan skickas till ett Azure Kognitiv sökning-index som tar emot `top` antalet resultat. Om rätt svar inte finns i dessa resultat ökar du värdet `top` något. Normalt är ett värde på 10 för `top` Works i 90% av frågorna. Azure Search-filter [stoppar ord](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/STOPWORDS.md) i det här steget.|
|5|QnA Maker använder den stegvisa transformatorbaserade modellen för att fastställa likheten mellan användar frågan och kandidat QnA resultat som hämtats från Azure Kognitiv sökning. Transformerad modell är en djup inlärnings modell med flera språk, som fungerar vågrätt för alla språk för att fastställa förtroende poängen och den nya rangordnings ordningen.|
|6|De nya resultaten returneras till klient programmet i rangordnings ordning.|
|||

Rangordningen fungerar på alla alternativa frågor och svar för att hitta de bäst matchade QnA-par för användar frågan. Användarna har flexibiliteten att konfigurera beställaren så att den bara ställer frågor till rangordnings tjänsten. 

---

## <a name="http-request-and-response-with-endpoint"></a>HTTP-begäran och-svar med slut punkten
När du publicerar din kunskaps bas skapar tjänsten en REST-baserad HTTP-slutpunkt som kan integreras i ditt program, ofta en chatt-robot.

### <a name="the-user-query-request-to-generate-an-answer"></a>Förfrågan om användar frågan för att generera ett svar

En användar fråga är den fråga som slutanvändaren ber om kunskaps basen, till exempel `How do I add a collaborator to my app?` . Frågan är ofta i ett naturligt språk format eller ett fåtal nyckelord som representerar frågan, till exempel `help with collaborators` . Frågan skickas till din kunskaps bas från en HTTP-begäran i klient programmet.

```json
{
    "question": "How do I add a collaborator to my app?",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 20,
    "strictFilters": [
    {
        "name": "QuestionType",
        "value": "Support"
    }],
    "userId": "sd53lsY="
}
```
Du styr svaret genom att ställa in egenskaper som [scoreThreshold](./confidence-score.md#choose-a-score-threshold), [Top](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)och [strictFilters](../how-to/query-knowledge-base-with-metadata.md).

Använd [konversations kontext](../how-to/query-knowledge-base-with-metadata.md) med [funktioner för flera funktioner](../how-to/multiturn-conversation.md) för att hålla konversationen att förfina frågorna och Svaren för att hitta rätt och slutgiltigt svar.

### <a name="the-response-from-a-call-to-generate-an-answer"></a>Svaret från ett anrop för att generera ett svar

HTTP-svaret är det svar som hämtats från kunskaps basen, baserat på den bästa matchningen för en specifik användar fråga. Svaret innehåller svaret och förutsägelse poängen. Om du har begärt fler än ett Top svar med `top` egenskapen får du fler än ett Top svar, var och en med poäng.

```json
{
    "answers": [
        {
            "questions": [
                "How do I add a collaborator to my app?",
                "What access control is provided for the app?",
                "How do I find user management and security?"
            ],
            "answer": "Use the Azure portal to add a collaborator using Access Control (IAM)",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "QuestionType",
                    "value": "Support"
                },
                {
                    "name": "ToolDependency",
                    "value": "Azure Portal"
                }
            ]
        }
    ]
}
```


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Förtroende Poäng](./confidence-score.md)
