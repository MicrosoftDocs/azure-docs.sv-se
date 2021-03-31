---
title: ta med fil
description: ta med fil
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 03/08/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 0e487b3ab3663c765c052f2064201865508ef57f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "102511053"
---
## <a name="trusted-microsoft-services"></a>Betrodda Microsoft-tjänster
När du aktiverar inställningen **Tillåt att betrodda Microsoft-tjänster kringgår den här brand Väggs** inställningen beviljas följande tjänster åtkomst till dina Event Hubs-resurser.

| Betrodd tjänst | Användnings scenarier som stöds | 
| --------------- | ------------------------- | 
| Azure Event Grid | Tillåter Azure Event Grid att skicka händelser till Event Hub i Event Hubs-namnrymden. Du måste också utföra följande steg: <ul><li>Aktivera systemtilldelad identitet för ett ämne eller en domän</li><li>Lägg till identiteten i rollen Azure Event Hubs data Sender i Event Hubs-namnrymden</li><li>Konfigurera sedan händelse prenumerationen som använder en händelsehubben som en slut punkt för att använda den systemtilldelade identiteten.</li></ul> <p>Mer information finns i [händelse leverans med en hanterad identitet](../articles/event-grid/managed-service-identity.md)</p>|
| Azure Monitor (diagnostikinställningar) | Tillåter Azure Monitor att skicka diagnostikinformation till Event Hub i Event Hubs-namnrymden. Azure Monitor kan läsa från händelsehubben och skriva data till händelsehubben. |
| Azure Stream Analytics | Gör att ett Azure Stream Analytics jobb kan läsa data från ([indata](../articles/stream-analytics/stream-analytics-add-inputs.md)) eller skriva data till ([utdata](../articles/stream-analytics/event-hubs-output.md)) Event Hub i Event Hubs-namnrymden. <p>**Viktigt**: Stream Analyticss jobbet ska konfigureras för att använda en **hanterad identitet** för att komma åt händelsehubben. Mer information finns i [använda hanterade identiteter för att komma åt händelsehubben från ett Azure Stream Analytics jobb (för hands version)](../articles/stream-analytics/event-hubs-managed-identity.md). </p>|
| Azure IoT Hub | Tillåter IoT Hub att skicka meddelanden till händelse nav i namn området för Händelsehubben. Du måste också utföra följande steg: <ul><li>Aktivera systemtilldelad identitet för din IoT Hub</li><li>Lägg till identiteten i rollen Azure Event Hubs data Sender i Event Hubs-namnområdet.</li><li>Konfigurera sedan IoT Hub som använder en händelsehubben som en anpassad slut punkt för att använda den identitetsbaserade autentiseringen.</li></ul>
| Azure API Management | <p>Med tjänsten API Management kan du skicka händelser till en händelsehubben i Event Hubs namn området.</p> <ul><li>Du kan utlösa anpassade arbets flöden genom att skicka händelser till händelsehubben när ett API anropas med hjälp av principen för att [skicka begäran](../articles/api-management/api-management-sample-send-request.md).</li><li>Du kan också behandla en Event Hub som Server del i ett API. En exempel princip finns i [autentisera med hjälp av en hanterad identitet för att få åtkomst till en Event Hub](https://github.com/Azure/api-management-policy-snippets/blob/master/examples/Authenticate%20using%20Managed%20Identity%20to%20access%20Event%20Hub.xml). Du måste också utföra följande steg:<ol><li>Aktivera systemtilldelad identitet på API Management-instansen. Instruktioner finns i [använda hanterade identiteter i Azure API Management](../articles/api-management/api-management-howto-use-managed-service-identity.md).</li><li>Lägg till identiteten i rollen **Azure Event Hubs data Sender** i Event Hubs-namnrymden</li></ol></li></ul> | 
