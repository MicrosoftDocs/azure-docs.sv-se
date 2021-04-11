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
ms.date: 03/11/2021
ms.author: aahi
ms.openlocfilehash: 8b596a5e54c0b59c4c0b49aa5cdc4fd6477d46dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104599349"
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

***

## <a name="next-steps"></a>Nästa steg

* [Använda namngiven enhets igenkänning i Textanalys](how-tos/text-analytics-how-to-entity-linking.md)
