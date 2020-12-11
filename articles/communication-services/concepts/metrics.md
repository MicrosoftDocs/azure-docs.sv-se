---
title: Mått definitioner för Azure Communication Service
titleSuffix: An Azure Communication Services concept document
description: Det här dokumentet beskriver definitioner av mått som är tillgängliga i Azure Portal.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 05/19/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 460cca051d743102e734971cf45069362e24c147
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97106011"
---
# <a name="metrics-overview"></a>Mått översikt

Azure Communication Services tillhandahåller för närvarande mått för chatt och SMS. [Azure Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md) kan användas för att rita egna diagram, undersöka avvikelser i dina mått värden och förstå din API-trafik genom att använda de mått data som chatt-och SMS-begäranden genererar.

## <a name="where-to-find-metrics"></a>Var du hittar mått

Chatt-och SMS-tjänster i Azure Communication Services genererar mått för API-begäranden. De här måtten finns på bladet mått i kommunikations tjänst resursen. Du kan också skapa permanenta instrument paneler med bladet arbets böcker under kommunikations tjänst resursen.

## <a name="metric-definitions"></a>Mått definitioner

Det finns två typer av begär Anden som representeras i kommunikations tjänst mått: **API-begäranden för chatt** och **SMS API-begäranden**.

Både chatt-och SMS API-begäran mått innehåller tre dimensioner som du kan använda för att filtrera dina mått data. De här dimensionerna kan aggregeras tillsammans med `Count` agg regerings typen och har stöd för alla standard Azure agg regerings tids serier `Sum` , inklusive,, `Average` `Min` och `Max` .

Mer information om sammansättnings typer och tids serie agg regeringar som stöds hittar du [i avancerade funktioner i Azure Metrics Explorer](../../azure-monitor/platform/metrics-charts.md#changing-aggregation)

- **Åtgärd** – alla åtgärder eller vägar som kan anropas på ACS Chat Gateway.
- **Status kod** – status kod svaret som skickas efter begäran.
- **StatusSubClass** – status kod serien som skickas efter svaret. 


### <a name="chat-api-request-metric-operations"></a>Åtgärder för att chatta API-begäran

Följande åtgärder är tillgängliga för API: er för API-begäran:

| Åtgärd/väg    | Beskrivning                                                                                    |
| -------------------- | ---------------------------------------------------------------------------------------------- |
| GetChatMessage       | Hämtar ett meddelande med meddelande-ID. |
| ListChatMessages     | Hämtar en lista över chatt meddelanden från en tråd. |
| SendChatMessage      | Skickar ett chatt meddelande till en tråd. |
| UpdateChatMessage    | Uppdaterar ett chatt meddelande. |
| DeleteChatMessage    | Tar bort ett chatt meddelande. |
| GetChatThread        | Hämtar en chatt-tråd. |
| ListChatThreads      | Hämtar listan över chatt-trådar för en användare. |
| UpdateChatThread     | Uppdaterar egenskaperna för en chatt-tråd. |
| CreateChatThread     | Skapar en chatt-tråd. |
| DeleteChatThread     | Tar bort en tråd. |
| GetReadReceipts      | Hämtar Läs kvitton för en tråd. |
| SendReadReceipt      | Skickar en Läs mottagnings händelse till en tråd för en användares räkning. |
| SendTypingIndicator           | Publicerar en Skriv händelse till en tråd för en användares räkning. |
| ListChatThreadParticipants    | Hämtar medlemmar i en tråd. |
| AddChatThreadParticipants     | Lägger till tråd medlemmar i en tråd. Ingen ändring görs om det redan finns medlemmar. |
| RemoveChatThreadParticipant   | Ta bort en medlem från en tråd. |

:::image type="content" source="./media/chat-metric.png" alt-text="Mått för chatt-API-begäran.":::

Om en begäran görs till en åtgärd som inte känns igen får du ett värde svar för "felaktig väg".

### <a name="sms-api-requests"></a>SMS API-begäranden

Följande åtgärder är tillgängliga på SMS API-begäran mått:

| Åtgärd/väg    | Beskrivning                                                                                    |
| -------------------- | ---------------------------------------------------------------------------------------------- |
| SMSMessageSent       | Skickar ett SMS-meddelande. |
| SMSDeliveryReportsReceived     | Hämtar rapporter om SMS-leverans |
| SMSMessagesReceived      | Hämtar SMS-meddelanden. |


:::image type="content" source="./media/sms-metric.png" alt-text="Mått för SMS API-begäran.":::

### <a name="authentication-api-requests"></a>API-begäranden för autentisering

Följande åtgärder är tillgängliga i mått för autentiserings-API-begär Anden:

| Åtgärd/väg    | Beskrivning                                                                                    |
| -------------------- | ---------------------------------------------------------------------------------------------- |
| CreateIdentity       | Skapar en identitet som representerar en enskild användare. |
| DeleteIdentity       | Tar bort en identitet. |
| Skapatoken          | Skapar en åtkomsttoken. |
| RevokeToken          | Återkallar alla åtkomsttoken som skapats för en identitet före angiven tid. |

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [data plattforms mått](../../azure-monitor/platform/data-platform-metrics.md)
