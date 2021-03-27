---
title: Azure Event Grid-ange anpassade huvuden för levererade händelser
description: Beskriver hur du kan ange anpassade sidhuvuden (eller leverans egenskaper) för levererade händelser.
ms.topic: conceptual
ms.date: 03/24/2021
ms.openlocfilehash: e2eff6b0d1dc78f0d558bb8e4e1ad79c62c52657
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630606"
---
# <a name="delivery-with-custom-headers"></a>Leverans med anpassade rubriker
Med händelse prenumerationer kan du konfigurera HTTP-huvuden som ingår i levererade händelser. Med den här funktionen kan du ange anpassade huvuden som krävs av ett mål. Du kan ställa in upp till 10 huvuden när du skapar en händelse prenumeration. Varje rubrik värde får inte vara större än 4 096 (4K) byte.

Du kan ange anpassade rubriker för de händelser som levereras till följande destinationer:

- Webhooks
- Azure Service Bus ämnen och köer
- Azure Event Hubs
- Relä Hybridanslutningar

När du skapar en händelse prenumeration i Azure Portal kan du använda fliken **leverans egenskaper** för att ange anpassade HTTP-huvuden. På den här sidan kan du ange fasta och dynamiska huvud värden.

## <a name="setting-static-header-values"></a>Ange statiska huvud värden
Om du vill ange rubriker med ett fast värde anger du namnet på rubriken och dess värde i motsvarande fält:

:::image type="content" source="./media/delivery-properties/static-header-property.png" alt-text="Leverans egenskaper – statisk":::

Kanske vill du kontrol lera att kontrollen **är hemlig?** när du anger känsliga data. Känsliga data visas inte på Azure Portal. 

## <a name="setting-dynamic-header-values"></a>Ange dynamiska huvud värden
Du kan ange värdet för en rubrik baserat på en egenskap i en inkommande händelse. Använd JsonPath-syntax för att referera till en inkommande händelse egenskap svärdet som ska användas som värde för en rubrik i utgående begär Anden. Om du till exempel vill ange värdet för en rubrik med namnet **kanal** med värdet för egenskaps **systemet** för inkommande händelse i händelse data konfigurerar du din händelse prenumeration på följande sätt:

:::image type="content" source="./media/delivery-properties/dynamic-header-property.png" alt-text="Leverans egenskaper-dynamiska":::

## <a name="examples"></a>Exempel
Det här avsnittet innehåller några exempel på hur du använder leverans egenskaper.

### <a name="setting-the-authorization-header-with-a-bearer-token-non-normative-example"></a>Ange Authorization-huvudet med en Bearer-token (icke-normativ exempel)

Ange ett värde för ett Authorization-huvud för att identifiera begäran med din webhook-hanterare. Ett Authorization-huvud kan anges om du inte [skyddar din webhook med Azure Active Directory](secure-webhook-delivery.md).

| Huvudnamn   | Rubrik typ | Huvudvärde |
| :--           | :--         | :--            |
|`Authorization` | Statisk | `BEARER SlAV32hkKG...`|

Utgående begär Anden ska nu innehålla den rubrik som angetts för händelse prenumerationen:

```console
GET /home.html HTTP/1.1

Host: acme.com

User-Agent: <user-agent goes here>

Authorization: BEARER SlAV32hkKG...
```

> [!NOTE]
> Att definiera auktoriseringsarkiv är ett lämpliga-alternativ när målet är en webhook. Den bör inte användas för [funktioner som prenumererar på resurs-ID](/rest/api/eventgrid/eventsubscriptions/createorupdate#azurefunctioneventsubscriptiondestination), Service Bus, Event Hubs och hybridanslutningar eftersom dessa destinationer stöder egna autentiseringsscheman när de används med event Grid.

### <a name="service-bus-example"></a>Service Bus exempel
Azure Service Bus stöder användningen av ett [BrokerProperties HTTP-huvud](/rest/api/servicebus/message-headers-and-properties#message-headers) för att definiera meddelande egenskaper när du skickar enskilda meddelanden. Värdet för `BrokerProperties` rubriken ska anges i JSON-formatet. Om du till exempel behöver ange meddelande egenskaper när du skickar ett enskilt meddelande till Service Bus, så ange sidhuvudet på följande sätt:

| Huvudnamn | Rubrik typ | Huvudvärde |
| :-- | :-- | :-- |
|`BrokerProperties` | Statisk     | `BrokerProperties:  { "MessageId": "{701332E1-B37B-4D29-AA0A-E367906C206E}", "TimeToLive" : 90}` |


### <a name="event-hubs-example"></a>Event Hubs exempel

Om du behöver publicera händelser till en speciell partition inom en händelsehubben definierar du ett [BrokerProperties HTTP-huvud](/rest/api/eventhub/event-hubs-runtime-rest#common-headers) för händelse prenumerationen och anger den partitionsnyckel som identifierar mål händelsehubben.

| Huvudnamn | Rubrik typ | Huvudvärde                                  |
| :-- | :-- | :-- |
|`BrokerProperties` | Statisk | `BrokerProperties: {"PartitionKey": "0000000000-0000-0000-0000-000000000000000"}`  |


### <a name="configure-time-to-live-on-outgoing-events-to-azure-storage-queues"></a>Konfigurera Time to Live på utgående händelser till Azure Storage köer
För Azure Storage köers mål kan du bara konfigurera Time-to-Live-meddelandet som utgående meddelande kommer att ha när det har levererats till en Azure Storage kö. Om ingen tid anges är meddelandets standard tid till 7 dagar. Du kan också ange att händelsen aldrig upphör att gälla.

:::image type="content" source="./media/delivery-properties/delivery-properties-storage-queue.png" alt-text="Leverans egenskaper – lagrings kön":::

## <a name="next-steps"></a>Nästa steg
Mer information om händelse leverans finns i följande artikel:

- [Leverans och nytt försök](delivery-and-retry.md)
- [Leverans av webhook-händelser](webhook-event-delivery.md)
- [Händelsefiltrering](event-filtering.md)
