---
title: ta med fil
ms.topic: include
ms.custom: include file
ms.date: 5/17/2020
ms.openlocfilehash: 79a45825be66b44d869ef741917436de87a1f5d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "83653188"
---
Lär dig [begrepp](../luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation) för normalisering och hur du använder [versions](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) -API: er för att uppdatera de här inställningarna eller Använd sidan för Luis-portalens **hanterings** avsnitt, **Inställningar** .


|Användar gränssnitts inställning|API-inställning|Information|
|--|--|--|
|Använd icke-deterministisk utbildning|`UseAllTrainingData`|Träning använder en liten procent andel av negativ sampling. Om du vill använda alla data i stället för den små negativa insamlingen anger du till `true` . |
|Normalisera dia kritiska tecken|`NormalizeDiacritics`|Normaliserar dia kritiska tecken ersätter tecknen med dia kritiska tecken i yttranden med vanliga tecken. Den här inställningen är bara tillgänglig på [språk](../luis-reference-application-settings.md#diacritics-normalization) som stöder dia kritiska tecken.|
|Normalisera interpunktion|`NormalizePunctuation`|Normalisera interpunktion innebär att innan dina modeller blir utbildade och innan dina slut punkts frågor hämtas, tas skiljetecken bort från yttranden.|
|Normalisera ord former|`NormalizeWordForm`|Ignorera ord former bortom roten.|
