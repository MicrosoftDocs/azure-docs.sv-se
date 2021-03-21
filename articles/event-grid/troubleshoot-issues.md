---
title: Felsöka Event Grid problem
description: Den här artikeln innehåller olika sätt att felsöka Azure Event Grid problem
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: d30b8464de90474ad74853cc423de700b41226a4
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "104720567"
---
# <a name="troubleshoot-azure-event-grid-issues"></a>Felsöka Azure Event Grid problem
Den här artikeln innehåller information som hjälper dig att felsöka Azure Event Grid problem. 

## <a name="diagnostic-logs"></a>Diagnostikloggar
Aktivera diagnostikinställningar för Event Grid ämnen eller domäner för att avbilda och Visa fel loggar för publicering och leverans. Mer information finns i [diagnostikloggar](enable-diagnostic-logs-topic.md).

## <a name="metrics"></a>Mått
Du kan visa mått för Event Grid ämnen och prenumerationer och skapa aviseringar på dem. Mer information finns i [Event Grid mått](monitor-event-delivery.md).

## <a name="alerts"></a>Aviseringar
Skapa aviseringar för Azure Event Grid mått och aktivitets logg åtgärder. Mer information finns i [varningar om Event Grid mått och aktivitets loggar](set-alerts.md).

## <a name="subscription-validation-issues"></a>Problem med prenumerations validering
När händelse prenumerationen skapas kan du få ett fel meddelande om att det inte gick att verifiera den angivna slut punkten. Information om hur du felsöker prenumerations validerings problem finns i [felsöka Event Grid prenumerations](troubleshoot-subscription-validation.md)verifieringar. 

## <a name="network-connectivity-issues"></a>Problem med nätverks anslutningen
Det finns olika orsaker till att klient program inte kan ansluta till ett Event Grid ämne/domän. De anslutnings problem som du upplever kan vara permanenta eller tillfälliga. Information om hur du löser dessa problem finns i [Felsöka anslutnings problem](troubleshoot-network-connectivity.md).

## <a name="error-codes"></a>Felkoder
Om du får fel meddelanden med felkoder som 400, 409 och 403, se [felsöka Event Grid fel](troubleshoot-errors.md). 

## <a name="distributed-tracing-net"></a>Distribuerad spårning (.NET)
Event Grid .NET-biblioteket stöder distribution av spårning. För att följa [rikt linjerna för CloudEvents-specifikationen](https://github.com/cloudevents/spec/blob/master/extensions/distributed-tracing.md) för att distribuera spårning, anger biblioteket `traceparent` och `tracestate` på [ExtensionAttributes](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventgrid/Azure.Messaging.EventGrid/src/Customization#L126) för en `CloudEvent` när distribuerad spårning är aktiverat. Läs mer om hur du aktiverar distribuerad spårning i ditt program genom att ta en titt på dokumentationen för Azure SDK [Distributed tracing](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Azure.Core/samples/Diagnostics.md#Distributed-tracing).

### <a name="sample"></a>Exempel
Se [exempel på rad räknare](/samples/azure/azure-sdk-for-net/line-counter/). Den här exempel appen illustrerar användningen av lagring, Event Hubs och Event Grid klienter tillsammans med ASP.NET Core integrering, distribuerad spårning och värdbaserade tjänster. Det gör det möjligt för användare att ladda upp en fil till en BLOB, som utlöser en Event Hubs händelse som innehåller fil namnet. Event Hubs-processorn tar emot händelsen och hämtar sedan bloben och räknar antalet rader i filen. Appen visar en länk till en sida som innehåller rad antalet. När användaren klickar på länken publiceras en CloudEvent som innehåller namnet på filen med hjälp av Event Grid.

## <a name="next-steps"></a>Nästa steg
Om du behöver mer hjälp kan du publicera ditt problem i [Stack Overflow-forumet](https://stackoverflow.com/questions/tagged/azure-eventgrid) eller öppna ett [support ärende](https://azure.microsoft.com/support/options/). 
