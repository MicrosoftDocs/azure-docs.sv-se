---
title: ta med fil
description: ta med fil
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 01/05/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 5c0585182fcd6899bdd123607f3b7d46a97253e9
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97956544"
---
## <a name="trusted-microsoft-services"></a>Betrodda Microsoft-tjänster
När du aktiverar inställningen **Tillåt att betrodda Microsoft-tjänster kringgår den här brand Väggs** inställningen beviljas följande tjänster åtkomst till dina Event Hubs-resurser.

| Betrodd tjänst | Användnings scenarier som stöds | 
| --------------- | ------------------------- | 
| Azure Event Grid | Tillåter Azure Event Grid att skicka händelser till Event Hub i Event Hubs-namnrymden. Du måste också utföra följande steg: <ul><li>Aktivera systemtilldelad identitet för ett ämne eller en domän</li><li>Lägg till identiteten i rollen Azure Event Hubs data Sender i Event Hubs-namnrymden</li><li>Konfigurera sedan händelse prenumerationen som använder en händelsehubben som en slut punkt för att använda den systemtilldelade identiteten.</li></ul> <p>Mer information finns i [händelse leverans med en hanterad identitet](../articles/event-grid/managed-service-identity.md)</p>|
| Azure Monitor (diagnostikinställningar) | Tillåter Azure Monitor att skicka diagnostikinformation till Event Hub i Event Hubs-namnrymden. |
| Azure Stream Analytics | Gör att ett Azure Stream Analytics jobb kan läsa data från ([indata](../articles/stream-analytics/stream-analytics-add-inputs.md)) eller skriva data till ([utdata](../articles/stream-analytics/event-hubs-output.md)) Event Hub i Event Hubs-namnrymden. <p>**Viktigt**: Stream Analyticss jobbet ska konfigureras för att använda en **hanterad identitet** för att komma åt händelsehubben. Mer information finns i [använda hanterade identiteter för att komma åt händelsehubben från ett Azure Stream Analytics jobb (för hands version)](../articles/stream-analytics/event-hubs-managed-identity.md). </p>|
| Azure IoT Hub | Tillåter IoT Hub att skicka meddelanden till händelse nav i namn området för Händelsehubben. Du måste också utföra följande steg: <ul><li>Aktivera systemtilldelad identitet för din IoT Hub</li><li>Lägg till identiteten i rollen Azure Event Hubs data Sender i Event Hubs-namnområdet.</li><li>Konfigurera sedan IoT Hub som använder en händelsehubben som en anpassad slut punkt för att använda den identitetsbaserade autentiseringen.</li></ul>
