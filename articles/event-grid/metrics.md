---
title: Mått som stöds av Azure Event Grid
description: Den här artikeln innehåller Azure Monitor mått som stöds av Azure Event Grids tjänsten.
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: 321e318f9dab87fde20b33a6a3a906b020ada622
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104588740"
---
# <a name="metrics-supported-by-azure-event-grid"></a>Mått som stöds av Azure Event Grid
Den här artikeln innehåller listor över Event Grid mått som kategoriseras efter namn områden. 

## <a name="system-topics"></a>Systemämnen

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Ja|Avancerade filter utvärderingar|Antal|Totalt|Totalt antal avancerade filter som utvärderas över händelse prenumerationer för det här ämnet.|EventSubscriptionName|
|DeadLetteredCount|Ja|Obeställbara, Brevade händelser|Antal|Totalt|Totalt antal inloggade obeställbara meddelanden som matchar den här händelse prenumerationen|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|Inga|Misslyckade leverans händelser|Antal|Totalt|Totalt antal händelser som inte levererades till den här händelse prenumerationen|Fel, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Ja|Levererade händelser|Antal|Totalt|Totalt antal händelser levererade till den här händelse prenumerationen|EventSubscriptionName|
|DestinationProcessingDurationInMs|Inga|Varaktighet för mål bearbetning|Millisekunder|Genomsnitt|Varaktighet för mål bearbetning i millisekunder|EventSubscriptionName|
|DroppedEventCount|Ja|Ignorerade händelser|Antal|Totalt|Totalt antal ignorerade händelser som matchar den här händelse prenumerationen|DropReason, EventSubscriptionName|
|MatchedEventCount|Ja|Matchade händelser|Antal|Totalt|Totalt antal händelser som matchar den här händelse prenumerationen|EventSubscriptionName|
|PublishFailCount|Ja|Publicera misslyckade händelser|Antal|Totalt|Totalt antal händelser som inte publicerats till det här avsnittet|ErrorType, fel|
|PublishSuccessCount|Ja|Publicerade händelser|Antal|Totalt|Totalt antal händelser som publicerats i det här avsnittet|Inga dimensioner|
|PublishSuccessLatencyInMs|Ja|Slutför svars tid för publicering|Millisekunder|Totalt|Publicera lyckad svars tid i millisekunder|Inga dimensioner|
|UnmatchedEventCount|Ja|Omatchade händelser|Antal|Totalt|Totalt antal händelser som inte matchar någon av händelse prenumerationerna för det här avsnittet|Inga dimensioner|


## <a name="custom-topics"></a>Anpassade ämnen

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Ja|Avancerade filter utvärderingar|Antal|Totalt|Totalt antal avancerade filter som utvärderas över händelse prenumerationer för det här ämnet.|EventSubscriptionName|
|DeadLetteredCount|Ja|Obeställbara, Brevade händelser|Antal|Totalt|Totalt antal inloggade obeställbara meddelanden som matchar den här händelse prenumerationen|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|Inga|Misslyckade leverans händelser|Antal|Totalt|Totalt antal händelser som inte levererades till den här händelse prenumerationen|Fel, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Ja|Levererade händelser|Antal|Totalt|Totalt antal händelser levererade till den här händelse prenumerationen|EventSubscriptionName|
|DestinationProcessingDurationInMs|Inga|Varaktighet för mål bearbetning|Millisekunder|Genomsnitt|Varaktighet för mål bearbetning i millisekunder|EventSubscriptionName|
|DroppedEventCount|Ja|Ignorerade händelser|Antal|Totalt|Totalt antal ignorerade händelser som matchar den här händelse prenumerationen|DropReason, EventSubscriptionName|
|MatchedEventCount|Ja|Matchade händelser|Antal|Totalt|Totalt antal händelser som matchar den här händelse prenumerationen|EventSubscriptionName|
|PublishFailCount|Ja|Publicera misslyckade händelser|Antal|Totalt|Totalt antal händelser som inte publicerats till det här avsnittet|ErrorType, fel|
|PublishSuccessCount|Ja|Publicerade händelser|Antal|Totalt|Totalt antal händelser som publicerats i det här avsnittet|Inga dimensioner|
|PublishSuccessLatencyInMs|Ja|Slutför svars tid för publicering|Millisekunder|Totalt|Publicera lyckad svars tid i millisekunder|Inga dimensioner|
|UnmatchedEventCount|Ja|Omatchade händelser|Antal|Totalt|Totalt antal händelser som inte matchar någon av händelse prenumerationerna för det här avsnittet|Inga dimensioner|

## <a name="domains"></a>Domains

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Ja|Avancerade filter utvärderingar|Antal|Totalt|Totalt antal avancerade filter som utvärderas över händelse prenumerationer för det här ämnet.|Ämne, EventSubscriptionName, DomainEventSubscriptionName|
|DeadLetteredCount|Ja|Obeställbara, Brevade händelser|Antal|Totalt|Totalt antal inloggade obeställbara meddelanden som matchar den här händelse prenumerationen|Ämne, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|
|DeliveryAttemptFailCount|Inga|Misslyckade leverans händelser|Antal|Totalt|Totalt antal händelser som inte levererades till den här händelse prenumerationen|Ämne, EventSubscriptionName, DomainEventSubscriptionName, fel, ErrorType|
|DeliverySuccessCount|Ja|Levererade händelser|Antal|Totalt|Totalt antal händelser levererade till den här händelse prenumerationen|Ämne, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|Inga|Varaktighet för mål bearbetning|Millisekunder|Genomsnitt|Varaktighet för mål bearbetning i millisekunder|Ämne, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|Ja|Ignorerade händelser|Antal|Totalt|Totalt antal ignorerade händelser som matchar den här händelse prenumerationen|Ämne, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|MatchedEventCount|Ja|Matchade händelser|Antal|Totalt|Totalt antal händelser som matchar den här händelse prenumerationen|Ämne, EventSubscriptionName, DomainEventSubscriptionName|
|PublishFailCount|Ja|Publicera misslyckade händelser|Antal|Totalt|Totalt antal händelser som inte publicerats till det här avsnittet|Ämne, ErrorType, fel|
|PublishSuccessCount|Ja|Publicerade händelser|Antal|Totalt|Totalt antal händelser som publicerats i det här avsnittet|Avsnitt|
|PublishSuccessLatencyInMs|Ja|Slutför svars tid för publicering|Millisekunder|Totalt|Publicera lyckad svars tid i millisekunder|Inga dimensioner|

## <a name="event-subscriptions"></a>Prenumerationer på händelser

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Ja|Obeställbara, Brevade händelser|Antal|Totalt|Totalt antal inloggade obeställbara meddelanden som matchar den här händelse prenumerationen|DeadLetterReason|
|DeliveryAttemptFailCount|Inga|Misslyckade leverans händelser|Antal|Totalt|Totalt antal händelser som inte levererades till den här händelse prenumerationen|Fel, ErrorType|
|DeliverySuccessCount|Ja|Levererade händelser|Antal|Totalt|Totalt antal händelser levererade till den här händelse prenumerationen|Inga dimensioner|
|DestinationProcessingDurationInMs|Inga|Varaktighet för mål bearbetning|Millisekunder|Genomsnitt|Varaktighet för mål bearbetning i millisekunder|Inga dimensioner|
|DroppedEventCount|Ja|Ignorerade händelser|Antal|Totalt|Totalt antal ignorerade händelser som matchar den här händelse prenumerationen|DropReason|
|MatchedEventCount|Ja|Matchade händelser|Antal|Totalt|Totalt antal händelser som matchar den här händelse prenumerationen|Inga dimensioner|


## <a name="extension-topics"></a>Avsnitt om tillägg

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|PublishFailCount|Ja|Publicera misslyckade händelser|Antal|Totalt|Totalt antal händelser som inte publicerats till det här avsnittet|ErrorType, fel|
|PublishSuccessCount|Ja|Publicerade händelser|Antal|Totalt|Totalt antal händelser som publicerats i det här avsnittet|Inga dimensioner|
|PublishSuccessLatencyInMs|Ja|Slutför svars tid för publicering|Millisekunder|Totalt|Publicera lyckad svars tid i millisekunder|Inga dimensioner|
|UnmatchedEventCount|Ja|Omatchade händelser|Antal|Totalt|Totalt antal händelser som inte matchar någon av händelse prenumerationerna för det här avsnittet|Inga dimensioner|

## <a name="partner-namespaces"></a>Partner namn rymder

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Ja|Obeställbara, Brevade händelser|Antal|Totalt|Totalt antal inloggade obeställbara meddelanden som matchar den här händelse prenumerationen|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|Inga|Misslyckade leverans händelser|Antal|Totalt|Totalt antal händelser som inte levererades till den här händelse prenumerationen|Fel, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Ja|Levererade händelser|Antal|Totalt|Totalt antal händelser levererade till den här händelse prenumerationen|EventSubscriptionName|
|DestinationProcessingDurationInMs|Inga|Varaktighet för mål bearbetning|Millisekunder|Genomsnitt|Varaktighet för mål bearbetning i millisekunder|EventSubscriptionName|
|DroppedEventCount|Ja|Ignorerade händelser|Antal|Totalt|Totalt antal ignorerade händelser som matchar den här händelse prenumerationen|DropReason, EventSubscriptionName|
|MatchedEventCount|Ja|Matchade händelser|Antal|Totalt|Totalt antal händelser som matchar den här händelse prenumerationen|EventSubscriptionName|
|PublishFailCount|Ja|Publicera misslyckade händelser|Antal|Totalt|Totalt antal händelser som inte publicerats till det här avsnittet|ErrorType, fel|
|PublishSuccessCount|Ja|Publicerade händelser|Antal|Totalt|Totalt antal händelser som publicerats i det här avsnittet|Inga dimensioner|
|PublishSuccessLatencyInMs|Ja|Slutför svars tid för publicering|Millisekunder|Totalt|Publicera lyckad svars tid i millisekunder|Inga dimensioner|
|UnmatchedEventCount|Ja|Omatchade händelser|Antal|Totalt|Totalt antal händelser som inte matchar någon av händelse prenumerationerna för det här avsnittet|Inga dimensioner|


## <a name="partner-topics"></a>Partnerämnen

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Ja|Avancerade filter utvärderingar|Antal|Totalt|Totalt antal avancerade filter som utvärderas över händelse prenumerationer för det här ämnet.|EventSubscriptionName|
|DeadLetteredCount|Ja|Obeställbara, Brevade händelser|Antal|Totalt|Totalt antal inloggade obeställbara meddelanden som matchar den här händelse prenumerationen|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|Inga|Misslyckade leverans händelser|Antal|Totalt|Totalt antal händelser som inte levererades till den här händelse prenumerationen|Fel, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Ja|Levererade händelser|Antal|Totalt|Totalt antal händelser levererade till den här händelse prenumerationen|EventSubscriptionName|
|DestinationProcessingDurationInMs|Inga|Varaktighet för mål bearbetning|Millisekunder|Genomsnitt|Varaktighet för mål bearbetning i millisekunder|EventSubscriptionName|
|DroppedEventCount|Ja|Ignorerade händelser|Antal|Totalt|Totalt antal ignorerade händelser som matchar den här händelse prenumerationen|DropReason, EventSubscriptionName|
|MatchedEventCount|Ja|Matchade händelser|Antal|Totalt|Totalt antal händelser som matchar den här händelse prenumerationen|EventSubscriptionName|
|PublishFailCount|Ja|Publicera misslyckade händelser|Antal|Totalt|Totalt antal händelser som inte publicerats till det här avsnittet|ErrorType, fel|
|PublishSuccessCount|Ja|Publicerade händelser|Antal|Totalt|Totalt antal händelser som publicerats i det här avsnittet|Inga dimensioner|
|UnmatchedEventCount|Ja|Omatchade händelser|Antal|Totalt|Totalt antal händelser som inte matchar någon av händelse prenumerationerna för det här avsnittet|Inga dimensioner|


## <a name="next-steps"></a>Nästa steg
Se följande artikel: [diagnostikloggar](diagnostic-logs.md)
