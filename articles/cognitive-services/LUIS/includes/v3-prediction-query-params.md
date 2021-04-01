---
title: Parametrar för v3 API-frågesträng
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 06/30/2020
ms.openlocfilehash: 14380dd781fc1eebfe7edb0a816ff8af0f2f17b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "91309444"
---
V3 API-frågeparametrar innehåller:

|Frågeparameter|LUIS-portalens namn|Typ|Version|Standardvärde|Syfte|
|--|--|--|--|--|--|
|`log`|Spara loggar|boolean|V2 & V3|falskt|Lagra fråga i logg filen. Standardvärdet är false.|
|`query`|-|sträng|Endast v3|Ingen standard – det krävs i GET-begäran|**I v2** är uttryck som ska förutsägas i `q` parametern. <br><br>**I v3** skickas funktionerna i- `query` parametern.|
|`show-all-intents`|Inkludera resultat för alla avsikter|boolean|Endast v3|falskt|Returnera alla avsikter med motsvarande Poäng i objektet **förutsägelse. avsikter** . Avsikter returneras som objekt i ett överordnat `intents` objekt. Detta ger program mässig åtkomst utan att behöva hitta avsikten i en matris: `prediction.intents.give` . I v2 returnerades dessa i en matris. |
|`verbose`|Ta med mer information om entiteter|boolean|V2 & V3|falskt|**I v2** returnerades alla förväntade avsikter när värdet är true. Om du behöver alla förutsägande syften använder du v3-parametrarna för `show-all-intents` .<br><br>**I v3** innehåller den här parametern endast information om entitetens metadata för entitet förutsägelse.  |
|`timezoneOffset`|-|sträng|V2|-|Tids zonen som används för datetimeV2-entiteter.|
|`datetimeReference`|-|sträng|V3|-|[Tids zonen](../luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) som används för datetimeV2-entiteter. Ersätter `timezoneOffset` från v2.|