---
title: Fördefinierad domän referens – LUIS
titleSuffix: Azure Cognitive Services
description: Referens för fördefinierade domäner, som är färdiga samlingar av avsikter och entiteter från Language Understanding intelligenta tjänster (LUIS).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.openlocfilehash: 73b279f98011181b329cdb010041022ab0da57f0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95018641"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>Fördefinierad domän referens för din LUIS-app
Den här referensen innehåller information om [fördefinierade domäner](./howto-add-prebuilt-models.md), som är färdiga samlingar av intentor och entiteter som Luis erbjuder.

[Anpassade domäner](luis-how-to-start-new-app.md), med kontrast, börjar utan avsikter och modeller. Du kan lägga till eventuella fördefinierade domän intentor och entiteter i en anpassad modell.

## <a name="prebuilt-domains-per-language"></a>Fördefinierade domäner per språk

Tabellen nedan sammanfattar de domäner som stöds för närvarande. Stöd för engelska är vanligt vis mer komplett än andra.

| Entitetstyp       | EN-US      | ZH-CN   | DE    | FR     | ES    | IT      | PT-BR |  JP  |      KO |        NL |    TR |
|:-----------------:|:-------:|:-------:|:-----:|:------:|:-----:|:-------:| :-------:| :-------:| :-------:| :-------:|  :-------:|
| Kalender  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
|Kommunikation  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| E-post     | ✓    | ✓       | ✓   | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| HomeAutomation          | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Kommentarer     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Placerar   | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| RestaurantReservation  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Att göra     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Verktyg      | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Vädret        | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Webb    | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |

Fördefinierade domäner **stöds inte** i:

* Franska, kanadensisk
* Hindi
* Spanska mexikanska

## <a name="next-steps"></a>Nästa steg

Lär dig den [enkla entiteten](reference-entity-simple.md).