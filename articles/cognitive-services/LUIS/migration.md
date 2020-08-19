---
title: Översikt över migrering – LUIS
description: Förstå vad som finns på en migrerings Sök väg
ms.topic: how-to
ms.date: 05/22/2020
ms.openlocfilehash: 4080dd27414c2da551b60ad430278c18768337c9
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88587828"
---
# <a name="migration-in-luis"></a>Migrering i LUIS

Flera objekt finns på en migrerings Sök väg. Använd följande tabell för att förstå vad som påverkas och när du behöver fullständig migrering.

|Område|Beskrivning|Slut för ande datum för migrering|
|--|--|--|
|[Förutsägelse-API: er](luis-migration-api-v3.md)|Migrera till v3-API.|TBD|
|[Redigera API:er](luis-migration-authoring-entities.md)|Migrera till v3-API.|TBD|
|[Skapar resurs](luis-migration-authoring.md)|Migrera från ingen redigerings resurs till att använda en LUIS Authoring-resurs i LUIS-portalen.|2 november 2020 |
|[Nödvändig funktion](luis-migration-authoring-entities.md#api-change-constraint-replaced-with-required-feature)|Den här ändringen gjordes i maj 2020 på Build-konferensen och gäller endast v3-redigerings-API: erna där en app använder en begränsad funktion.|19 juni 2020|
|[Sammansatt entitet](migrate-from-composite-entity.md)|Uppgradera den sammansatta entiteten till en enhet för maskin inlärning för att bygga en entitet som tar emot mer kompletta förutsägelser med bättre dekomposition för fel sökning av entiteten.|TBD|
