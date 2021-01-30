---
title: Kategorier som stöds för identifiering av namngiven entitet
titleSuffix: Azure Cognitive Services
description: Lär dig mer om entiteter som stöds i API för textanalys.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 01/22/2021
ms.author: aahi
ms.openlocfilehash: 883c5a20612f4dab44c0d06776ee287b27174ab9
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99097311"
---
# <a name="supported-entity-categories-in-the-text-analytics-api-v3"></a>Entitets kategorier som stöds i API för textanalys v3

Använd den här artikeln för att hitta enhets kategorier som kan returneras av den [namngivna enhets igenkänningen](how-tos/text-analytics-how-to-entity-linking.md) (ner). NER kör en förutsägelse modell för att identifiera och kategorisera namngivna entiteter från ett indatamängds dokument.

En för hands version av NER v 3.1 är också tillgänglig, vilket innebär att du kan identifiera personlig `PII` information () och hälso tillstånd ( `PHI` ). Du kan också klicka på fliken **hälsa** för att se en lista över kategorier som stöds i textanalys för hälso tillstånd. 

Du kan hitta en lista med typer som returneras av version 2,1 i [migreringsguiden](migration-guide.md?tabs=named-entity-recognition)

## <a name="entity-categories"></a>Enhets kategorier

#### <a name="general"></a>[Allmänt](#tab/general)

[!INCLUDE [supported entity types - general](./includes/entity-types/general-entities.md)]

#### <a name="pii"></a>[IDENTIFIERBAR](#tab/personal)

[!INCLUDE [supported entity types - personally identifying information](./includes/entity-types/personal-information-entities.md)]

#### <a name="health"></a>[Hälsa](#tab/health)

[!INCLUDE [biomedical entity types](./includes/entity-types/health-entities.md)]

**_

## <a name="next-steps"></a>Nästa steg

_ [Hur du använder namngiven entitets igenkänning i textanalys](how-tos/text-analytics-how-to-entity-linking.md)
