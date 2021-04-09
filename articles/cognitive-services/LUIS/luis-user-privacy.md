---
title: Exportera & ta bort data – LUIS
titleSuffix: Azure Cognitive Services
description: Du har fullständig kontroll över att visa, exportera och ta bort data. Ta bort kund information för att säkerställa sekretess och efterlevnad.
services: cognitive-services
manager: nitinme
ms.custom: seodec18, references_regions
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 12/10/2020
ms.openlocfilehash: 0a2d0ce683261ca3460c7aeaa0d7a42152b81a1e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "98680200"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>Exportera och ta bort dina kund data i Language Understanding (LUIS) i Cognitive Services

Ta bort kund information för att säkerställa sekretess och efterlevnad.

## <a name="summary-of-customer-data-request-features"></a>Sammanfattning av funktioner för begäranden om kunddata
Language Understanding Intelligent Service (LUIS) bevarar kund innehåll för att köra tjänsten, men LUIS-användaren har fullständig kontroll över att visa, exportera och ta bort data. Detta kan göras via LUIS- [webbportalen](luis-reference-regions.md) eller [Luis Authoring (kallas även programmerings programmering)](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Kund innehållet lagras krypterat i Microsoft Regional Azure Storage och innehåller:

- Användar konto innehåll som samlas in vid registreringen
- Tränings data som krävs för att bygga modeller
- Loggade användar frågor som används av [aktiv inlärning](luis-concept-review-endpoint-utterances.md) för att förbättra modellen
  - Användare kan stänga av loggning av frågor genom `&log=false` att lägga till i begäran, information [här](troubleshooting.md#how-can-i-disable-the-logging-of-utterances)

## <a name="deleting-customer-data"></a>Tar bort kund information
LUIS-användare har fullständig kontroll för att ta bort alla användar innehåll, antingen via LUIS-webbportalen eller LUIS redigering (även kallat programmatisk) API: er. I följande tabell visas länkar som hjälper dig med båda:

| | **Användarkonto** | **Program** | **Exempel på uttryck** | **Slut användar frågor** |
| --- | --- | --- | --- | --- |
| **Portal** | [Länk](luis-concept-data-storage.md#delete-an-account) | [Länk](luis-how-to-start-new-app.md#delete-app) | [Länk](luis-concept-data-storage.md#utterances-in-an-intent) | [Active Learning-yttranden](luis-how-to-review-endpoint-utterances.md#disable-active-learning)<br>[Loggade yttranden](luis-concept-data-storage.md#disable-logging-utterances) |
| **API:er** | [Länk](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [Länk](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [Länk](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [Länk](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>Exportera kund information
LUIS-användare har fullständig kontroll för att visa data på portalen, men de måste exporteras genom LUIS-redigering (även kallat programmering). I följande tabell visas länkar som hjälper dig med data exporter via LUIS Authoring (kallas även program mässig) API: er:

| | **Användarkonto** | **Program** | **Uttryck (s)** | **Slut användar frågor** |
| --- | --- | --- | --- | --- |
| **API:er** | [Länk](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [Länk](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [Länk](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [Länk](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |

## <a name="location-of-active-learning"></a>Plats för aktiv inlärning

För att aktivera [aktiv inlärning](luis-how-to-review-endpoint-utterances.md#log-user-queries-to-enable-active-learning)lagras användarens loggade yttranden som togs emot vid de publicerade Luis-slutpunkterna i följande Azure-geografiska områden:

* [Europa](#europe)
* [Australien](#australia)
* [USA](#united-states)

Med undantag för aktiva inlärnings data (beskrivs nedan) följer LUIS [data lagrings metoder för regionala tjänster](https://azuredatacentermap.azurewebsites.net/).

[!INCLUDE [portal consolidation](includes/portal-consolidation.md)]


### <a name="europe"></a>Europa

Europa-redigering (även kallade programmerings-API: er) finns i Azures Europa geografi och har stöd för distribution av slut punkter till följande Azure-geografiska områden:

* Europa
* Frankrike
* Storbritannien

När du distribuerar till dessa Azure-geografiska områden kommer yttranden som tagits emot av slut punkten från slutanvändare av appen att lagras i Azures Europa geografi för aktiv inlärning.

### <a name="australia"></a>Australien

Australiens redigering (även kallat programmerings-API: er) finns i Azures region i Australien och stöder distribution av slut punkter till följande Azure-geografiska områden:

* Australien

När du distribuerar till dessa Azure-geografiska områden kommer de yttranden som tas emot av slut punkten från slutanvändare av appen att lagras i Azures region för Australien för aktiv inlärning.

### <a name="united-states"></a>USA

USA redigering (även kallade program-API: er) finns i Azures USA geografi och stöd för distribution av slut punkter till följande Azure-geografiska områden:

* Azure-geografiska områden stöds inte av redigerings regionerna Europa eller Australien

När du distribuerar till dessa Azure-geografiska områden kommer yttranden som tagits emot av slut punkten från slutanvändare av appen att lagras i Azures USA geografi för aktiv inlärning. 

## <a name="disable-active-learning"></a>Inaktivera aktiv inlärning

Om du vill inaktivera aktiv inlärning, se [inaktivera aktiv inlärning](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Information om hur du hanterar lagrade yttranden finns i [ta bort uttryck](luis-how-to-review-endpoint-utterances.md#delete-utterance).


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Referens för LUIS-regioner](./luis-reference-regions.md)
