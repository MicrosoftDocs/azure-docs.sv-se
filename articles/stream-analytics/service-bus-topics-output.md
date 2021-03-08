---
title: Service Bus ämnena utdata från Azure Stream Analytics
description: I den här artikeln beskrivs Service Bus ämnen som utdata för Azure Stream Analytics.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: bee6e5547ad78554467f23968230721ba029aba6
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2021
ms.locfileid: "102451770"
---
# <a name="service-bus-topics-output-from-azure-stream-analytics"></a>Service Bus ämnena utdata från Azure Stream Analytics

Service Bus köer tillhandahåller en en-till-en-kommunikations metod från sändare till mottagare. [Service Bus ämnen](/previous-versions/azure/hh367516(v=azure.100)) innehåller en en-till-många-form av kommunikation.

I följande tabell visas egenskaps namnen och deras beskrivningar för att skapa ett Service Bus avsnitts utdata.

| Egenskapsnamn | Beskrivning |
| --- | --- |
| Utdataalias |Ett eget namn som används i frågor för att dirigera frågeresultatet till den här Service Bus ämnet. |
| Service Bus namnrymd |En behållare för en uppsättning meddelande enheter. När du skapade en ny händelsehubben, skapade du också ett Service Bus-namnområde. |
| Ämnes namn |Ämnen är meddelande enheter, liknande händelse hubbar och köer. De är utformade för att samla in händelse strömmar från enheter och tjänster. När ett ämne skapas får det också ett särskilt namn. Meddelanden som skickas till ett ämne är inte tillgängliga om inte en prenumeration skapas, så se till att det finns en eller flera prenumerationer i avsnittet. |
| Ämnes princip namn |När du skapar ett Service Bus ämne kan du också skapa principer för delad åtkomst på fliken **Konfigurera** . Varje princip för delad åtkomst har ett namn, behörigheter som du anger och åtkomst nycklar. |
| Ämnes princip nyckel |Den delade åtkomst nyckeln som används för att autentisera åtkomsten till Service Bus-namnrymden. |
| Händelseserialiseringsformat |Serialiserings formatet för utdata. JSON, CSV och Avro stöds. |
| Kodning |Om du använder CSV eller JSON-format måste du ange en kodning. UTF-8 är det enda kodnings format som stöds just nu. |
| Avgränsare |Gäller endast för CSV-serialisering. Stream Analytics stöder ett antal vanliga avgränsare för serialisering av data i CSV-format. Värden som stöds är komma, semikolon, blank steg, TABB och lodrätt fält. |
| Egenskaps kolumner | Valfritt. Kommaavgränsade kolumner som måste bifogas som användar egenskaper för det utgående meddelandet i stället för nytto lasten. Mer information om den här funktionen finns i avsnittet [anpassade metadata för utdata](#custom-metadata-properties-for-output). |
| Kolumner i system egenskap | Valfritt. Nyckel värdes par med system egenskaper och motsvarande kolumn namn som måste kopplas till det utgående meddelandet i stället för nytto lasten. |

Antalet partitioner [baseras på Service Bus SKU och storlek](../service-bus-messaging/service-bus-partitioning.md). Partitionsnyckel är ett unikt heltals värde för varje partition.

## <a name="partitioning"></a>Partitionering

Partitionering väljs automatiskt. Antalet partitioner baseras på [Service Bus SKU och storlek](../service-bus-messaging/service-bus-partitioning.md). Partitionsnyckel är ett unikt heltals värde för varje partition. Antalet utgående skrivare är detsamma som antalet partitioner i avsnittet utdata.

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

   :::image type="content" source="media/service-bus-topics-output/property-columns.png" alt-text="Egenskaps kolumner":::

Följande bild visar förväntade egenskaper för utmatnings meddelande som inspekterats i EventHub med hjälp av [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer).

:::image type="content" source="media/service-bus-topics-output/custom-properties.png" alt-text="Anpassade egenskaper för händelse":::

## <a name="system-properties"></a>Systemegenskaper

Du kan koppla frågenoder som [system egenskaper](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#properties) till den utgående Service Bus-kön eller ämnes meddelanden. De här kolumnerna hamnar inte i nytto lasten i stället för motsvarande BrokeredMessage [system egenskap](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#properties) fylls med värdena i kolumnen fråga.
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