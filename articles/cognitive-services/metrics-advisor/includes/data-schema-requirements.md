---
title: Krav för data schema
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: 4c55c25621df1925b6ed6c374d8af88551eb1e46
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "96231437"
---
Metrics Advisor är en tjänst för avvikelse identifiering, diagnostik och analys i Time Series. Som en AI-driven tjänst använder den dina data för att träna modellen som används. Tjänsten accepterar tabeller med sammanställda data med följande kolumner:

* **Mått** (obligatoriskt): en eller flera kolumner som innehåller numeriska värden.
* **Timestamp** (valfritt): noll eller en kolumn med typen `DateTime` eller `String` . När den här kolumnen inte anges anges tidsstämpeln som start tid för varje inmatnings period. Formatera tidsstämpeln i: `yyyy-MM-ddTHH:mm:ssZ` . 
  * **Din tidstämpel ska matcha måttets granularitet. Ett dagligt mått bör till exempel se till att timme, minut och sekund på tidsstämpeln är `00:00:00` märkta som**.
* **Dimension** (valfritt): kolumner kan vara av vilken datatyp som helst. Var försiktig när du arbetar med stora volymer med kolumner och värden för att förhindra att stora mängder dimensioner bearbetas.

> [!Note]
> För varje mått bör det bara finnas en tidsstämpel per mått, motsvarande en dimensions kombination. Sammanställ dina data före onboarding eller Använd frågan för att ange de data som ska matas in.