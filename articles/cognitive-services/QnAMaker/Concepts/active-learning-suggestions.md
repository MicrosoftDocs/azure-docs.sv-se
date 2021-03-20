---
title: Aktiva utbildnings förslag – QnA Maker
description: Med aktiva utbildnings förslag kan du förbättra din kunskaps Bass kvalitet genom att föreslå alternativa frågor, baserat på användar-och användar sändningar, till din fråga och ditt svars par.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: e1a8043912c984be46f85bd384a7049da27028b3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96353246"
---
# <a name="active-learning-suggestions"></a>Aktiva utbildnings förslag

Med funktionen för _aktiva utbildnings förslag_ kan du förbättra kvaliteten på din kunskaps bas genom att föreslå alternativa frågor, baserat på användar-och användar sändningar till din fråga och svar-paret. Du kan granska dessa förslag, antingen lägga till dem i befintliga frågor eller avvisa dem.

Kunskaps basen ändras inte automatiskt. För att alla ändringar ska börja gälla måste du godkänna förslagen. Dessa förslag lägger till frågor, men ändrar inte eller tar inte bort befintliga frågor.

## <a name="what-is-active-learning"></a>Vad är aktiv inlärning?

QnA Maker lär sig nya variationer i frågan med implicit och uttrycklig feedback.

* [Implicit feedback](#how-qna-makers-implicit-feedback-works) – rankningen förstår när en användar fråga har flera svar med poäng som är mycket nära och som ser ut som feedback. Du behöver inte göra något för att detta ska ske.
* [Explicit feedback](#how-you-give-explicit-feedback-with-the-train-api) – när flera svar med lite variation i poängen returneras från kunskaps basen, ber klient programmet användaren som frågan är rätt fråga om. Användarens uttryckliga feedback skickas till QnA Maker med träna- [API: et](../How-to/improve-knowledge-base.md#train-api).

Båda metoderna ger rangordningen med liknande frågor som är klustrade.

## <a name="how-active-learning-works"></a>Så här fungerar Active Learning

Aktiv inlärning utlöses baserat på resultaten av de viktigaste svaren som returneras av QnA Maker. Om Poäng skillnaderna mellan QnA-par som matchar frågan ligger inom ett litet intervall, anses frågan vara ett möjligt förslag (som en alternativ fråga) för varje möjligt QnA-par. När du har accepterat den föreslagna frågan för ett speciellt QnA-par avvisas den för de andra paren. Du måste komma ihåg att spara och träna när du har accepterat förslag.

Active Learning ger bästa möjliga förslag i fall där slut punkterna får en rimlig mängd och olika användnings frågor. Om 5 eller fler liknande frågor grupperas, var 30: e minut, föreslår QnA Maker de användarbaserade frågorna till kunskaps bas verktyget för att godkänna eller avvisa. Alla förslag är grupperade efter likhet och de vanligaste förslagen för alternativa frågor som visas baserat på frekvensen för specifika frågor av slutanvändare.

När frågorna föreslås i QnA Maker portal måste du granska och godkänna eller avvisa dessa förslag. Det finns inget API för att hantera förslag.

## <a name="turn-on-active-learning"></a>Aktivera aktiv inlärning

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/v1)

Som standard är aktiv inlärning **inaktiverat**.
Så här använder du Active Learning:
* Du måste [Aktivera aktiv inlärning](../How-To/use-active-learning.md#turn-on-active-learning-for-alternate-questions) så att QNA Maker samlar in alternativa frågor om din kunskaps bas.
* Om du vill se de föreslagna alternativa frågorna [använder du visnings alternativen](../How-To/improve-knowledge-base.md#view-suggested-questions) på sidan Redigera.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/v2)

Som **standard är aktiv inlärning i QNA Maker** hanterad (för hands version). Om du vill se de föreslagna alternativa frågorna [använder du visnings alternativen](../How-To/improve-knowledge-base.md#view-suggested-questions) på sidan Redigera.

---

## <a name="how-qna-makers-implicit-feedback-works"></a>Så här fungerar QnA Makers implicit feedback

QnA Makers implicita feedback använder en algoritm för att fastställa poängen och gör aktiva utbildnings förslag. Algoritmen för att bestämma närhet är inte en enkel beräkning. Intervallen i följande exempel är inte avsedda att korrigeras utan bör användas som vägledning för att endast förstå effekten av algoritmen.

När en frågas Poäng är mycket trygg, t. ex. 80%, är poängen som beaktas för aktiv inlärning en bred, ungefär inom 10%. När förtroende poängen minskar, till exempel 40%, minskar intervallet för poängen, ungefär inom 4%.

I följande JSON-svar från en fråga till QnA Maker s generateAnswer, visas poängen för A, B och C nära och betraktas som förslag.

```json
{
  "activeLearningEnabled": true,
  "answers": [
    {
      "questions": [
        "Q1"
      ],
      "answer": "A1",
      "score": 80,
      "id": 15,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q2"
      ],
      "answer": "A2",
      "score": 78,
      "id": 16,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q3"
      ],
      "answer": "A3",
      "score": 75,
      "id": 17,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q4"
      ],
      "answer": "A4",
      "score": 50,
      "id": 18,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    }
  ]
}
```

QnA Maker vet inte vilket svar som är det bästa svaret. Använd QnA Maker Portals lista med förslag för att välja det bästa svaret och träna igen.


## <a name="how-you-give-explicit-feedback-with-the-train-api"></a>Hur du ger explicit feedback med träna-API: et

QnA Maker behöver uttrycklig feedback om vilka av svaren som var det bästa svaret. Hur det bästa svaret fastställs är upp till dig och kan innehålla:

* Feedback från användare och välj ett av svaren.
* Affärs logik, till exempel att fastställa ett acceptabelt Poäng intervall.
* En kombination av både feedback och affärs logik.

Använd [träna API](/rest/api/cognitiveservices/qnamaker4.0/runtime/train) för att skicka rätt svar till QNA Maker när användaren har markerat det.

## <a name="next-step"></a>Nästa steg

> [!div class="nextstepaction"]
> [Fråga kunskaps basen](query-knowledge-base.md)