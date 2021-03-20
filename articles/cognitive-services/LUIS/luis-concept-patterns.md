---
title: Hjälp förutsägelse för mönster – LUIS
titleSuffix: Azure Cognitive Services
description: Ett mönster gör att du kan få bättre precision för en avsikt utan att tillhandahålla många fler yttranden.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/11/2019
ms.openlocfilehash: cda71c6e825ff27ba9b03e1306ccb287663e8613
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95025967"
---
# <a name="patterns-improve-prediction-accuracy"></a>Mönster förbättrar förutsägelse noggrannhet
Mönster är utformade för att förbättra precisionen när flera yttranden är mycket lika.  Ett mönster gör att du kan få bättre precision för en avsikt utan att tillhandahålla många fler yttranden.

## <a name="patterns-solve-low-intent-confidence"></a>Mönster löser låg säkerhet
Överväg en personalavdelningen som rapporterar om organisations diagrammet i förhållande till en medarbetare. Utifrån en medarbetares namn och relation returnerar LUIS de anställda. Överväg en anställd, tom, med ett chefs namn Alice och ett lag med underordnade med namnet: Michael, Rebecca och Carl.

![Bild av organisations schema](./media/luis-concept-patterns/org-chart.png)

|Yttranden|Förväntad avsikt|Avsikts poängen|
|--|--|--|
|Vem är tom som underordnad?|GetOrgChart|.30|
|Vem är underordnad tom?|GetOrgChart|.30|

Om en app har mellan 10 och 20 yttranden med olika längd på mening, olika ord ordning och även olika ord (synonymer till "underordnad", "hantera", "rapport") kan LUIS returnera en låg exakthet. Skapa ett mönster för att hjälpa LUIS förstå ord ordningens betydelse.

Mönster löser följande situationer:

* Poängen för avsikt är låg
* Rätt avsikt är inte det översta resultatet, men för nära det högsta resultatet.

## <a name="patterns-are-not-a-guarantee-of-intent"></a>Mönster är inte en garanti för avsikt
Mönster använder en blandning av förutsägelse teknik. Att ange ett avsikts sätt för en mall uttryck i ett mönster är inte en garanti för avsikts förutsägelse, men det är en stark signal.

<a name="patterns-do-not-improve-entity-detection"></a>

## <a name="patterns-do-not-improve-machine-learning-entity-detection"></a>Mönster kan inte förbättra identifiering av maskin inlärnings enheter

Ett mönster är främst avsett att hjälpa till att förutsäga avsikter och roller. _Mönstret. en_ entitet används för att extrahera fria formulär enheter. När mönster använder entiteter kan ett mönster inte identifiera en enhet för maskin inlärning.

Vänta inte att se förbättrad enhets förutsägelse om du komprimerar flera yttranden till ett enda mönster. För att enkla entiteter ska kunna startas måste du lägga till yttranden eller använda List entiteter, annars går det inte att starta mönstret.

## <a name="patterns-use-entity-roles"></a>Mönster använder entitets roller
Om två eller flera entiteter i ett mönster är kontext bundna, använder mönster enhets [roller](./luis-concept-entity-types.md) för att extrahera sammanhangsbaserad information om entiteter.

## <a name="prediction-scores-with-and-without-patterns"></a>Förutsägelse resultat med och utan mönster
Med tillräckligt många exempel yttranden skulle LUIS kunna öka förutsägelse förtroendet utan mönster. Mönster ökar förtroende poängen utan att behöva ange så många yttranden.

## <a name="pattern-matching"></a>Mönster matchning
Ett mönster matchas baserat på identifiering av entiteterna inuti mönstret först och sedan verifieras resten av orden och ord ordningen för mönstret. Entiteter krävs i mönstret för ett mönster som ska matchas. Mönstret används på token-nivå, inte på Character-nivå.

## <a name="pattern-only-apps"></a>Endast Pattern-appar
Du kan bygga en app med avsikter som inte har något exempel yttranden, så länge det finns ett mönster för varje avsikt. För en app-only-app bör mönstret inte innehålla enheter för maskin inlärning eftersom dessa kräver exempel yttranden.

## <a name="patternany-entity"></a>Entiteten Pattern.any

[!INCLUDE [Pattern.any entity - concepts](./includes/pattern-any-entity.md)]

## <a name="best-practices"></a>Bästa praxis
Lär dig [metod tips](luis-concept-best-practices.md).

## <a name="pattern-syntax"></a>Mönstersyntax

Lär dig mönstermatchningssyntax från [Pattern-syntaxen](reference-pattern-syntax.md).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om mönster:

* [Lägga till mönster](luis-how-to-model-intent-pattern.md)
* [Hur du lägger till mönster. valfri entitet](luis-how-to-add-entities.md#add-a-patternany-entity)
* [Syntax för mönster](reference-pattern-syntax.md)

> [!div class="nextstepaction"]
> [Lär dig hur du implementerar mönster i den här självstudien](luis-tutorial-pattern.md)
