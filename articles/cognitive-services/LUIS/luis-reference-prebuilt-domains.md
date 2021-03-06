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
ms.openlocfilehash: 6f9637a24bcb722fc20ee08408c8bb3d8b240113
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076329"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>Fördefinierad domän referens för din LUIS-app
Den här referensen innehåller information om [fördefinierade domäner](./howto-add-prebuilt-models.md), som är färdiga samlingar av intentor och entiteter som Luis erbjuder.

[Anpassade domäner](luis-how-to-start-new-app.md), med kontrast, börjar utan avsikter och modeller. Du kan lägga till eventuella fördefinierade domän intentor och entiteter i en anpassad modell.

## <a name="prebuilt-domains-per-language"></a>Fördefinierade domäner per språk

Tabellen nedan sammanfattar de domäner som stöds för närvarande. Stöd för engelska är vanligt vis mer komplett än andra.

|      Entitetstyp      | EN-US | ZH-CN | DE | FR | ES | IT | PT-BR | JP | KO | NL | TR |
|:---------------------:|:-----:|:-----:|:--:|:--:|:--:|:--:|------:|---:|---:|---:|---:|
|       Kalender        |   ✓   |   ✓   | ✓  | ✓  | ✓  | ✓  |     ✓ |  ✓ |  ✓ |  ✓ |  ✓ |
|     Kommunikation     |   ✓   |   ✓   | ✓  | ✓  | ✓  | ✓  |     ✓ |  ✓ |  ✓ |  ✓ |  ✓ |
|         E-post         |   ✓   |   ✓   | ✓  | ✓  | ✓  | ✓  |     ✓ |  ✓ |  ✓ |  ✓ |  ✓ |
|    HomeAutomation     |   ✓   |   ✓   | ✓  | ✓  | ✓  | ✓  |     ✓ |  ✓ |  ✓ |  ✓ |  ✓ |
|         Kommentarer         |   ✓   |   ✓   | ✓  | ✓  | ✓  | ✓  |     ✓ |  ✓ |  ✓ |  ✓ |  ✓ |
|        Placerar         |   ✓   |   ✓   | ✓  | ✓  | ✓  | ✓  |     ✓ |  ✓ |  ✓ |  ✓ |  ✓ |
| RestaurantReservation |   ✓   |   ✓   | ✓  | ✓  | ✓  | ✓  |     ✓ |  ✓ |  ✓ |  ✓ |  ✓ |
|         Att göra          |   ✓   |   ✓   | ✓  | ✓  | ✓  | ✓  |     ✓ |  ✓ |  ✓ |  ✓ |  ✓ |
|       Verktyg       |   ✓   |   ✓   | ✓  | ✓  | ✓  | ✓  |     ✓ |  ✓ |  ✓ |  ✓ |  ✓ |
|        Vädret        |   ✓   |   ✓   | ✓  | ✓  | ✓  | ✓  |     ✓ |  ✓ |  ✓ |  ✓ |  ✓ |
|          Webb          |   ✓   |   ✓   | ✓  | ✓  | ✓  | ✓  |     ✓ |  ✓ |  ✓ |  ✓ |  ✓ |

Fördefinierade domäner **stöds inte** i:

* Franska, kanadensisk
* Hindi
* Spanska mexikanska

## <a name="next-steps"></a>Nästa steg

Lär dig den [enkla entiteten](reference-entity-simple.md).
