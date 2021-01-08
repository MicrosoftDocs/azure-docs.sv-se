---
title: Service Bus köer utdata från Azure Stream Analytics
description: I den här artikeln beskrivs Service Bus köer som utdata för Azure Stream Analytics.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 9f71a0f9c6d3fdca4b81be3a69479c78236fcc31
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98014220"
---
# <a name="service-bus-queues-output-from-azure-stream-analytics"></a>Service Bus köer utdata från Azure Stream Analytics

[Service Bus köer](../service-bus-messaging/service-bus-queues-topics-subscriptions.md) erbjuder en leverans av FIFO-meddelanden till en eller flera konkurrerande konsumenter. Normalt tas meddelanden emot och bearbetas av mottagarna i den temporala ordning som de lades till i kön. Varje meddelande tas emot och bearbetas bara av en meddelande konsument.

I [kompatibilitetsnivå 1,2](stream-analytics-compatibility-level.md)använder Azure Stream Analytics protokollet [Advanced Message Queueing Protocol (AMQP)](../service-bus-messaging/service-bus-amqp-overview.md) för att skriva till Service Bus köer och ämnen. Med AMQP kan du skapa hybrid program mellan plattformar med ett öppet standard protokoll.

## <a name="output-configuration"></a>Konfiguration av utdata

I följande tabell visas egenskaps namnen och deras beskrivningar för att skapa utdata från kön.

| Egenskapsnamn | Beskrivning |
| --- | --- |
| Utdataalias |Ett eget namn som används i frågor för att dirigera frågeresultatet till den här Service Bus kön. |
| Service Bus namnrymd |En behållare för en uppsättning meddelande enheter. |
| Könamn |Namnet på Service Bus kön. |
| Princip namn för kö |När du skapar en kö kan du också skapa principer för delad åtkomst på fliken **Konfigurera** i kön. Varje princip för delad åtkomst har ett namn, behörigheter som du anger och åtkomst nycklar. |
| Princip nyckel för kö |Den delade åtkomst nyckeln som används för att autentisera åtkomsten till Service Bus-namnrymden. |
| Händelseserialiseringsformat |Serialiserings formatet för utdata. JSON, CSV och Avro stöds. |
| Kodning |För CSV och JSON är UTF-8 det enda kodnings format som stöds just nu. |
| Avgränsare |Gäller endast för CSV-serialisering. Stream Analytics stöder ett antal vanliga avgränsare för serialisering av data i CSV-format. Värden som stöds är komma, semikolon, blank steg, TABB och lodrätt fält. |
| Format |Gäller endast för JSON-typ. **Raden separerad** anger att utdata är formaterade genom att ha varje JSON-objekt avgränsat med en ny rad. Om du väljer **rad avgränsad** läses ett objekt i taget av JSON. Själva hela innehållet skulle inte vara en giltig JSON. **Matris** anger att utdata är formaterad som en matris med JSON-objekt. |
| Egenskaps kolumner | Valfritt. Kommaavgränsade kolumner som måste bifogas som användar egenskaper för det utgående meddelandet i stället för nytto lasten. Mer information om den här funktionen finns i avsnittet [anpassade metadata för utdata](#custom-metadata-properties-for-output). |
| Kolumner i system egenskap | Valfritt. Nyckel värdes par med system egenskaper och motsvarande kolumn namn som måste kopplas till det utgående meddelandet i stället för nytto lasten.  |

Antalet partitioner [baseras på Service Bus SKU och storlek](../service-bus-messaging/service-bus-partitioning.md). Partitionsnyckel är ett unikt heltals värde för varje partition.

## <a name="partitioning"></a>Partitionering

Partitionering väljs automatiskt. Antalet partitioner baseras på [Service Bus SKU och storlek](../service-bus-messaging/service-bus-partitioning.md). Partitionsnyckel är ett unikt heltals värde för varje partition. Antalet utgående skrivare är detsamma som antalet partitioner i kön för utdata.

## <a name="output-batch-size"></a>Batchstorlek för utdata

Den maximala meddelande storleken är 256 KB per meddelande för standard nivån och 1 MB för Premium nivån. Mer information finns i [Service Bus gränser](../service-bus-messaging/service-bus-quotas.md). Använd en enskild händelse per meddelande för att optimera.

## <a name="custom-metadata-properties-for-output"></a>Anpassade egenskaper för metadata för utdata

Du kan koppla frågeegenskaper som användar egenskaper till dina utgående meddelanden. De här kolumnerna hamnar inte i nytto lasten. Egenskaperna finns i form av en ord lista i utmatnings meddelandet. *Key* är kolumnens namn och *värde* är kolumnens värde i ord listan för egenskaper. Alla Stream Analytics data typer stöds förutom post och matris.

I följande exempel `DeviceId` läggs fälten och till i `DeviceStatus` metadata.

1. Använd följande fråga:

   ```sql
   select *, DeviceId, DeviceStatus from iotHubInput
   ```

1. Konfigurera `DeviceId,DeviceStatus` som egenskaps kolumner i utdata.

   :::image type="content" source="media/service-bus-queues-output/property-columns.png" alt-text="Egenskaps kolumner":::

Följande bild visar förväntade egenskaper för utmatnings meddelande som inspekterats i EventHub med hjälp av [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer).

:::image type="content" source="media/service-bus-queues-output/custom-properties.png" alt-text="Anpassade egenskaper för händelse":::

## <a name="system-properties"></a>Systemegenskaper

Du kan koppla frågenoder som [system egenskaper](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet&preserve-view=true#properties) till den utgående Service Bus-kön eller ämnes meddelanden.

De här kolumnerna hamnar inte i nytto lasten i stället för motsvarande BrokeredMessage [system egenskap](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet&preserve-view=true#properties) fylls med värdena i kolumnen fråga.
Dessa system egenskaper stöds – `MessageId, ContentType, Label, PartitionKey, ReplyTo, SessionId, CorrelationId, To, ForcePersistence, TimeToLive, ScheduledEnqueueTimeUtc` .

Sträng värden för de här kolumnerna tolkas som motsvarande typ av system egenskaps värde och eventuella tolknings fel behandlas som data fel.
Det här fältet anges som ett JSON-objekt format. Information om det här formatet är följande:

* Omgivet av klammerparenteser {} .
* Skrivet i nyckel/värde-par.
* Nycklar och värden måste vara strängar.
* Nyckel är systemets egenskaps namn och värde är frågans kolumn namn.
* Nycklar och värden avgränsas med kolon.
* Varje nyckel/värde-par avgränsas med ett kommatecken.

Här visas hur du använder den här egenskapen –

* Frågeterm `select *, column1, column2 INTO queueOutput FROM iotHubInput`
* Kolumner i system egenskap: `{ "MessageId": "column1", "PartitionKey": "column2"}`

Detta anger `MessageId` om Service Bus Queue-meddelanden med `column1` värdena och PartitionKey har angetts med värdet `column2` .

## <a name="next-steps"></a>Nästa steg

* [Snabbstart: Skapa ett Stream Analytics-jobb med hjälp av Azure-portalen](stream-analytics-quick-create-portal.md)
* [Snabb start: skapa ett Azure Stream Analytics jobb med hjälp av Azure CLI](quick-create-azure-cli.md)
* [Snabb start: skapa ett Azure Stream Analytics jobb med en ARM-mall](quick-create-azure-resource-manager.md)
* [Snabb start: skapa ett Stream Analytics jobb med Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Snabb start: skapa ett Azure Stream Analytics jobb med hjälp av Visual Studio](stream-analytics-quick-create-vs.md)
* [Snabb start: skapa ett Azure Stream Analytics jobb i Visual Studio Code](quick-create-visual-studio-code.md)