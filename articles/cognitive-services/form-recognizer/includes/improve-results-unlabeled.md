---
author: laujan
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: lajanuar
ms.openlocfilehash: 89b035397ea2050ae7e61f2a19310b6a7fb4192c
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467238"
---
Undersök `"confidence"` värdena för varje nyckel/värde-resultat under `"pageResults"` noden. Du bör också titta på förtroende poängen i `"readResults"` noden, som motsvarar Läs åtgärden text. Säkerheten för Läs resultaten påverkar inte tillförlitligheten för nyckel-/värde extraherings resultatet, så du bör kontrol lera båda.
* Om förtroende poängen för Läs åtgärden är låg, försöker du förbättra kvaliteten på dina inaktuella dokument (se [krav](../overview.md#input-requirements)för inläsning).
* Om förtroende poängen för extraherings åtgärden för nyckel/värde är låg, se till att dokumenten som analyseras är av samma typ som dokumenten som används i inlärnings uppsättningen. Om dokumenten i inlärnings uppsättningen har variationer i utseendet kan du överväga att dela upp dem i olika mappar och träna en modell för varje variation.

Förtroende poängen som du använder beror på ditt användnings fall, men vanligt vis är det en bra idé att rikta in dig på poängen på 80% eller högre. För mer känsliga fall, t. ex. läsning av medicinska poster eller fakturerings besked, rekommenderas resultatet 100%.