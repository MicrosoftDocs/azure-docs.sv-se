---
title: Design med modeller – LUIS
description: Språk förståelse tillhandahåller flera olika typer av modeller. Vissa modeller kan användas på fler än ett sätt.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: 576ba945018d13db9cd24888f3c41a2215857694
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "91316519"
---
# <a name="design-with-intent-and-entity-models"></a>Design med avsikts-och enhets modeller

Språk förståelse tillhandahåller två typer av modeller som du kan använda för att definiera ditt program schema. Ditt program schema avgör vilken information du får från förutsägelsen av en ny användares uttryck.

App-schemat bygger på modeller som du skapar med hjälp av [maskin undervisninger](#authoring-uses-machine-teaching):
* [Avsikter](#intents-classify-utterances) klassificera användar yttranden
* [Entiteter](#entities-extract-data) extraherar data från uttryck

## <a name="authoring-uses-machine-teaching"></a>Redigering använder dator undervisning

LUIS-metoden för dator undervisning gör att du enkelt kan lära dig begrepp till en dator. Att förstå _Machine Learning_ är inte nödvändigt för att använda Luis. I stället meddelar läraren ett begrepp till LUIS genom att tillhandahålla exempel på konceptet och förklara hur ett begrepp ska modelleras med andra relaterade begrepp. Du, som lärare, kan också förbättra LUIS-modellen interaktivt genom att identifiera och korrigera förutsägelse misstag.

<a name="v3-authoring-model-decomposition"></a>

## <a name="intents-classify-utterances"></a>Avsikter – klassificera talindata

En avsikt klassificerar exempel yttranden för att lära LUIS om avsikten. Exempel på yttranden i ett avsikts sätt används som positiva exempel på uttryck. Samma yttranden används som negativa exempel i alla andra syften.

Överväg en app som behöver fastställa en användares avsikt att beställa en bok och en app som behöver leverans adressen för kunden. Den här appen har två avsikter: `OrderBook` och `ShippingLocation` .

Följande uttryck är ett **positivt exempel** för `OrderBook` avsikten och ett **negativt exempel** för `ShippingLocation` och `None` avsikter:

`Buy the top-rated book on bot architecture.`

## <a name="entities-extract-data"></a>Entiteter – extrahera data

En entitet representerar en data enhet som du vill extrahera från uttryck. En enhet för maskin inlärning är en enhet på den översta nivån som innehåller underentiteter, som också är maskin inlärnings enheter.

Ett exempel på en enhet för maskin inlärning är en ordning för en plan biljett. Det här är en enskild transaktion med många mindre data enheter, till exempel datum, tid, antal platser, typ av säte, till exempel första klass eller turist klass, ursprungs plats, målplats och mål områdes val.

## <a name="intents-versus-entities"></a>Avsikter jämfört med entiteter

Avsikt är det önskade resultatet av _hela_ uttryck medan entiteter är bitar av data som extraherats från uttryck. Vanligt vis är avsikter knutna till åtgärder som klient programmet ska vidta. Entiteter är information som behövs för att utföra den här åtgärden. Från ett programmerings perspektiv utlöser en avsikt ett metod anrop och entiteterna används som parametrar för det metod anropet.

Den här uttryck _måste_ ha en avsikt och _kan_ ha entiteter:

`Buy an airline ticket from Seattle to Cairo`

Den här uttryck har en enda avsikt:

* Köpa en plan biljett

Den här uttryck _kan_ ha flera entiteter:

* Platser för Seattle (ursprung) och Cairo (mål)
* Antalet för en enskild biljett

## <a name="entity-model-decomposition"></a>Dekomposition av enhets modell

LUIS stöder _modell dekomposition_ med redigerings-API: erna och avbryter ett begrepp i mindre delar. På så sätt kan du skapa modeller med förtroende för hur de olika delarna konstrueras och förväntas.

Modell dispositionen har följande delar:

* [avsikter](#intents-classify-utterances)
    * [egenskaper](#features)
* [enheter för maskin inlärning](reference-entity-machine-learned-entity.md)
    * underentiteter (även maskin inlärnings enheter)
        * [egenskaper](#features)
            * [fras lista](luis-concept-feature.md)
            * [icke-utbildnings enheter](luis-concept-feature.md) som [reguljära uttryck](reference-entity-regular-expression.md), [listor](reference-entity-list.md)och [färdiga entiteter](luis-reference-prebuilt-entities.md)

<a name="entities-extract-data"></a>
<a name="machine-learned-entities"></a>

## <a name="features"></a>Funktioner

En [funktion](luis-concept-feature.md) är en särskiljande egenskap eller attribut för data som systemet iakttar. Machine Learning-funktioner ger LUIS viktiga stackar för var du ska leta efter saker som särskiljer ett koncept. De är tips som LUIS kan använda, men inte hårda regler. Dessa tips används tillsammans med etiketterna för att hitta data.

## <a name="patterns"></a>Mönster

[Mönster](luis-concept-patterns.md) är utformade för att förbättra precisionen när flera yttranden är mycket lika. Ett mönster gör att du kan få bättre precision för en avsikt utan att tillhandahålla många fler yttranden.

## <a name="extending-the-app-at-runtime"></a>Utöka appen vid körning

Appens schema (modeller och funktioner) har tränats och publicerats till förutsägelse slut punkten. Du kan [Skicka ny information](schema-change-prediction-runtime.md)tillsammans med användarens uttryck till förutsägelse slut punkten för att förstärka förutsägelsen.

## <a name="next-steps"></a>Nästa steg

* Förstå [avsikter](luis-concept-intent.md) och [entiteter](luis-concept-entity-types.md).
* Lär dig mer om [funktioner](luis-concept-feature.md)