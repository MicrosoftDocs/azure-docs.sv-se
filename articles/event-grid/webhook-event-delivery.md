---
title: Leverans av webhook-händelser
description: I den här artikeln beskrivs webhook-händelsen leverans och slut punkts verifiering när du använder Webhooks.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: e9a52d0cb3e4e880d91e1b748d97ef3041298930
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87461246"
---
# <a name="webhook-event-delivery"></a>Leverans av webhook-händelser
Webhooks är ett av många sätt att ta emot händelser från Azure Event Grid. När en ny händelse är klar skickar Event Grid tjänsten en HTTP-begäran till den konfigurerade slut punkten med händelsen i begär ande texten.

Precis som många andra tjänster som har stöd för webhookar kräver Event Grid att du förvarar ägarskapet för webhook-slutpunkten innan den börjar leverera händelser till den slut punkten. Detta krav förhindrar att en obehörig användare översvämmar din slut punkt med händelser. När du använder någon av de tre Azure-tjänsterna i listan nedan hanterar Azure-infrastrukturen automatiskt den här verifieringen:

- Azure Logic Apps med [Event Grid koppling](/connectors/azureeventgrid/)
- Azure Automation via [webhook](../event-grid/ensure-tags-exists-on-new-virtual-machines.md)
- Azure Functions med [Event Grid-utlösare](../azure-functions/functions-bindings-event-grid.md)

## <a name="endpoint-validation-with-event-grid-events"></a>Slut punkts validering med Event Grid händelser
Om du använder någon annan typ av slut punkt, till exempel en HTTP-utlösare baserad Azure-funktion, måste du delta i en validerings hand skakning med Event Grid. Event Grid stöder två sätt att verifiera prenumerationen.

1. **Synkron hand skakning**: när händelse prenumerationen skapas skickar Event Grid en prenumerations validerings händelse till din slut punkt. Schemat för den här händelsen liknar andra Event Grid-händelser. Data delen av den här händelsen innehåller en `validationCode` egenskap. Programmet verifierar att verifierings förfrågan är för en förväntad händelse prenumeration och returnerar verifierings koden i svaret synkront. Den här hand skaknings mekanismen stöds i alla Event Grid-versioner.

2. **Asynkron hand skakning**: i vissa fall kan du inte returnera ValidationCode i svar synkront. Om du till exempel använder en tjänst från tredje part (t. ex. [`Zapier`](https://zapier.com) eller [ifttt](https://ifttt.com/)) kan du inte program mässigt svara med validerings koden.

   Från och med version 2018-05-01 – för hands version har Event Grid stöd för en manuell validerings hand skakning. Om du skapar en händelse prenumeration med ett SDK eller verktyg som använder API-version 2018-05-01-Preview eller senare, skickar Event Grid en `validationUrl` egenskap i data delen av prenumerations verifierings händelsen. Om du vill slutföra hand skakningen letar du reda på URL: en i händelse data och gör en GET-begäran till den. Du kan antingen använda en REST-klient eller din webbläsare.

   Den angivna webb adressen är giltig i **5 minuter**. Under denna tid är etablerings statusen för händelse prenumerationen `AwaitingManualAction` . Om du inte slutför den manuella verifieringen inom 5 minuter anges etablerings statusen till `Failed` . Du måste skapa händelse prenumerationen igen innan du startar den manuella verifieringen.

   Den här autentiseringsmetoden kräver också att webhook-slutpunkten returnerar en HTTP-statuskod på 200 så att den vet att posten för verifierings händelsen accepterades innan den kan placeras i manuellt validerings läge. Med andra ord, om slut punkten returnerar 200 men inte returnerar ett verifierings svar synkront, övergår läget till det manuella validerings läget. Om validerings-URL: en visas inom 5 minuter anses verifierings hand skakningen vara lyckad.

> [!NOTE]
> Det finns inte stöd för att använda självsignerade certifikat för verifiering. Använd ett signerat certifikat från en kommersiell certifikat utfärdare (CA) i stället.

### <a name="validation-details"></a>Verifierings information

- Vid skapande/uppdatering av händelse prenumerationen skickar Event Grid en prenumerations validerings händelse till mål slut punkten.
- Händelsen innehåller ett huvud värde "AEG-Event-Type: SubscriptionValidation".
- Händelse texten har samma schema som andra Event Grid händelser.
- Händelsens händelse-egenskap är `Microsoft.EventGrid.SubscriptionValidationEvent` .
- Egenskapen data för händelsen innehåller en `validationCode` egenskap med en slumpmässigt genererad sträng. Till exempel "validationCode: acb13...".
- Händelse data innehåller även en `validationUrl` egenskap med en URL för att verifiera prenumerationen manuellt.
- Matrisen innehåller bara verifierings händelsen. Andra händelser skickas i en separat begäran när du har tillbaka verifierings koden.
- EventGrid dataplanen SDK: er har klasser som motsvarar händelse data för prenumerations verifiering och prenumerations verifierings svar.

Ett exempel på en SubscriptionValidationEvent visas i följande exempel:

```json
[
  {
    "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
    "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "subject": "",
    "data": {
      "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
      "validationUrl": "https://rp-eastus2.eventgrid.azure.net:553/eventsubscriptions/estest/validate?id=512d38b6-c7b8-40c8-89fe-f46f9e9622b6&t=2018-04-26T20:30:54.4538837Z&apiVersion=2018-05-01-preview&token=1A1A1A1A"
    },
    "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
    "eventTime": "2018-01-25T22:12:19.4556811Z",
    "metadataVersion": "1",
    "dataVersion": "1"
  }
]
```

För att bevisa slut punktens ägarskap kan du gå tillbaka till verifierings koden i egenskapen validationResponse, som visas i följande exempel:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Du måste returnera en HTTP 200 OK svars status kod. HTTP 202 accepterades inte som ett giltigt verifierings svar för Event Grid prenumeration. Http-begäran måste slutföras inom 30 sekunder. Om åtgärden inte slutförs inom 30 sekunder avbryts åtgärden och du kan försöka igen efter 5 sekunder. Om alla försöken Miss lyckas behandlas det som ett fel i validerings hand skakningen.

Du kan också validera prenumerationen manuellt genom att skicka en GET-begäran till verifierings-URL: en. Händelse prenumerationen är i ett väntande tillstånd tills den har verifierats. Verifierings-URL: en använder port 553. Om brand Väggs reglerna blockerar port 553 kan regler behöva uppdateras för lyckad hand skakning.

Ett exempel på hur du hanterar en prenumerations validerings hand skakning finns i ett [C#-exempel](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs).

## <a name="endpoint-validation-with-cloudevents-v10"></a>Slut punkts validering med CloudEvents v 1.0
Om du redan är bekant med Event Grid kan du vara medveten om Event Gridens slut punkts validerings hand skakning för att förhindra missbruk. CloudEvents v 1.0 implementerar egna [insemantiker för missbruks skydd](webhook-event-delivery.md) med hjälp av http-alternativ-metoden. Du kan läsa mer om det [här](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection). När du använder CloudEvents-schemat för utdata använder Event Grid med CloudEvents v 1.0 missbruk-skyddet i stället för mekanismen för Event Grid validerings händelser.

## <a name="next-steps"></a>Nästa steg
I följande artikel finns information om hur du felsöker händelse prenumerations valideringar: 

[Felsöka händelse prenumerations valideringar](troubleshoot-subscription-validation.md)
