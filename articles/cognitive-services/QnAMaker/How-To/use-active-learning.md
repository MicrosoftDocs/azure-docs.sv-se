---
title: Använda aktiv utbildning med kunskaps bas – QnA Maker
description: Lär dig att förbättra kvaliteten på din kunskaps bas med aktiv inlärning. Granska, acceptera eller avvisa, Lägg till utan att ta bort eller ändra befintliga frågor.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: ffc1a0a401de634c1932b9653f231b377c4f154e
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99591930"
---
# <a name="active-learning"></a>Aktiv inlärning

Med funktionen för _aktiva utbildnings förslag_ kan du förbättra kvaliteten på din kunskaps bas genom att föreslå alternativa frågor, baserat på användar-och användar sändningar till din fråga och svar-paret. Du kan granska dessa förslag, antingen lägga till dem i befintliga frågor eller avvisa dem.

Kunskaps basen ändras inte automatiskt. För att alla ändringar ska börja gälla måste du godkänna förslagen. Dessa förslag lägger till frågor, men ändrar inte eller tar inte bort befintliga frågor.

## <a name="what-is-active-learning"></a>Vad är aktiv inlärning?

QnA Maker lär sig nya variationer i frågan med implicit och uttrycklig feedback.

* [Implicit feedback](#how-qna-makers-implicit-feedback-works) – rankningen förstår när en användar fråga har flera svar med poäng som är mycket nära och som ser ut som feedback. Du behöver inte göra något för att detta ska ske.
* [Explicit feedback](#how-you-give-explicit-feedback-with-the-train-api) – när flera svar med lite variation i poängen returneras från kunskaps basen, ber klient programmet användaren som frågan är rätt fråga om. Användarens uttryckliga feedback skickas till QnA Maker med träna- [API: et](../How-To/improve-knowledge-base.md#train-api).

Båda metoderna ger rangordningen med liknande frågor som är klustrade.

## <a name="how-active-learning-works"></a>Så här fungerar Active Learning

Aktiv inlärning utlöses baserat på resultaten av de viktigaste svaren som returneras av QnA Maker. Om Poäng skillnaderna mellan QnA-par som matchar frågan ligger inom ett litet intervall, anses frågan vara ett möjligt förslag (som en alternativ fråga) för varje möjligt QnA-par. När du har accepterat den föreslagna frågan för ett speciellt QnA-par avvisas den för de andra paren. Du måste komma ihåg att spara och träna när du har accepterat förslag.

Active Learning ger bästa möjliga förslag i fall där slut punkterna får en rimlig mängd och olika användnings frågor. Om 5 eller fler liknande frågor grupperas, var 30: e minut, föreslår QnA Maker de användarbaserade frågorna till kunskaps bas verktyget för att godkänna eller avvisa. Alla förslag är grupperade efter likhet och de vanligaste förslagen för alternativa frågor som visas baserat på frekvensen för specifika frågor av slutanvändare.

När frågorna föreslås i QnA Maker portal måste du granska och godkänna eller avvisa dessa förslag. Det finns inget API för att hantera förslag.

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

## <a name="upgrade-runtime-version-to-use-active-learning"></a>Uppgradera runtime-versionen för att använda aktiv inlärning

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/v1)

Active Learning stöds i runtime-version 4.4.0 och senare. Om din kunskaps bas har skapats på en tidigare version kan du [Uppgradera körningen](set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates) för att använda den här funktionen.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/v2)

I QnA Maker hanterad (förhands granskning), eftersom körnings miljön är värd för själva tjänsten QnA Maker, behöver du inte uppgradera körnings miljön manuellt.

---

## <a name="turn-on-active-learning-for-alternate-questions"></a>Aktivera aktiv inlärning för alternativa frågor

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/v1)

Aktiv inlärning är inaktiverat som standard. Aktivera det om du vill se föreslagna frågor. När du har aktiverat aktiv inlärning måste du skicka information från klient appen till QnA Maker. Mer information finns i [arkitektoniskt flöde för att använda GenerateAnswer och träna API: er från en bot](improve-knowledge-base.md#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot).

1. Välj **publicera** för att publicera kunskaps basen. Aktiva inlärnings frågor samlas endast in från GenerateAnswer API förutsägelse-slut punkten. Frågorna i test fönstret i QnA Makers portalen påverkar inte aktiv inlärning.

1. Om du vill aktivera aktiv inlärning i QnA Maker portal går du till det övre högra hörnet och väljer ditt **namn**, gå till [**tjänst inställningar**](https://www.qnamaker.ai/UserSettings).

    ![Aktivera alternativ för den föreslagna frågan i Active Learning på sidan tjänst inställningar. Välj ditt användar namn på menyn längst upp till höger och välj sedan tjänst inställningar.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Hitta QnA Maker tjänsten och växla sedan **aktiv inlärning**.

    > [!div class="mx-imgBorder"]
    > [![På sidan tjänst inställningar växlar du till funktionen aktiv inlärning. Om du inte kan växla funktionen kan du behöva uppgradera tjänsten.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    > [!Note]
    > Den exakta versionen på föregående bild visas som ett exempel. Din version kan vara annorlunda.

    När **aktiv inlärning** har Aktiver ATS föreslår kunskaps basen nya frågor med jämna mellanrum baserat på frågor som skickats av användaren. Du kan inaktivera **aktiv inlärning** genom att växla inställningen igen.
    
# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/v2)

Som **standard är aktiv inlärning i QNA Maker** hanterad (för hands version). Om du vill se de föreslagna alternativa frågorna [använder du visnings alternativen](../How-To/improve-knowledge-base.md#view-suggested-questions) på sidan Redigera.

---

## <a name="review-suggested-alternate-questions"></a>Granska föreslagna alternativa frågor

[Granska alternativa föreslagna frågor](improve-knowledge-base.md) på sidan **Redigera** i varje kunskaps bas.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en kunskaps bas](./manage-knowledge-bases.md)
