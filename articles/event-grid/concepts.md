---
title: Azure Event Grid begrepp
description: Beskriver Azure Event Grid och dess begrepp. Definierar flera viktiga komponenter i Event Grid.
ms.topic: conceptual
ms.date: 01/21/2021
ms.openlocfilehash: 6edc8a3980bfea15f28cfb7114bb9f8350a47a3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98685711"
---
# <a name="concepts-in-azure-event-grid"></a>Begrepp i Azure Event Grid

I den här artikeln beskrivs huvud begreppen i Azure Event Grid.

## <a name="events"></a>Händelser

En händelse är den minsta mängden information som fullständigt beskriver något som hände i systemet. Varje händelse har gemensam information, t. ex. Händelsens källa, tid då händelsen ägde rum och unik identifierare. Varje händelse har också en speciell information som endast är relevant för den speciella typen av händelse. Till exempel, en händelse som handlar om en fil som har skapats i Azure Storage innehåller information om filen, såsom värdet `lastTimeModified`. Eller en händelse i Event Hubs har URL:en för Capture-filen. 

Den största tillåtna storleken för en händelse är 1 MB. Händelser över 64 KB debiteras i steg om 64 KB. De egenskaper som skickas i en händelse finns i [Azure Event Grid händelse schema](event-schema.md).

## <a name="publishers"></a>Utgivare

En utgivare är användaren eller organisationen som bestämmer sig för att skicka händelser till Event Grid. Microsoft publicerar händelser för flera Azure-tjänster. Du kan publicera händelser från ditt eget program. Organisationer som är värdar för tjänster utanför Azure kan utfärda händelser via Event Grid.

## <a name="event-sources"></a>Händelsekällor

En händelse källa är den plats där händelsen inträffar. Varje händelsekälla är relaterat till en eller flera händelsetyper. Till exempel är Azure Storage händelsekällan för händelser som skapas i blobbar. IoT Hub är händelsekällan för händelser som har skapats på enheter. Ditt program är händelsekällan för anpassade händelser som du definierar. Händelsekällor ansvarar för att skicka händelser till Event Grid.

Information om hur du implementerar någon av de Event Grid källorna som stöds finns i [händelse källor i Azure Event Grid](overview.md#event-sources).

## <a name="topics"></a>Ämnen

Avsnittet Event Grid innehåller en slut punkt där källan skickar händelser. Avsnittet Event Grid skapas i utgivaren och avgör om en händelse källa behöver ett ämne eller mer än ett ämne. Ett ämne används för en samling relaterade händelser. För att svara på vissa typer av händelser, bestämmer prenumeranter vilka ämnen som ska prenumerera på.

**System ämnen** är inbyggda ämnen som tillhandahålls av Azure-tjänster som Azure Storage, Azure Event Hubs och Azure Service Bus. Du kan skapa system ämnen i din Azure-prenumeration och prenumerera på dem. Mer information finns i [Översikt över system ämnen](system-topics.md). 

**Anpassade ämnen** är program-och tredje parts ämnen. När du skapar eller tilldelas åtkomst till ett anpassat ämne visas detta anpassade ämne i din prenumeration. Mer information finns i avsnittet om [anpassade ämnen](custom-topics.md). När du designar ditt program är du flexibel när du bestämmer hur många ämnen som ska skapas. Skapa ett anpassat ämne för varje kategori av relaterade händelser för stora lösningar. Tänk dig ett program som skickar händelser som handlar om att ändra användarkonton och bearbeta beställningar. Det är osannolikt alla händelsehanteraren vill ha båda händelsekategorier. Skapa två anpassade ämnen och låt händelsehanteraren prenumerera på det mest relevanta. För små lösningar kanske du föredrar att skicka alla händelser till ett enda ämne. Händelse prenumeranter kan filtrera efter de händelse typer som de vill ha.

Det finns en annan typ av ämne: **partner ämne**. Med funktionen [partner händelser](partner-events-overview.md) kan en SaaS-provider från tredje part publicera händelser från sina tjänster så att de blir tillgängliga för konsumenter som kan prenumerera på dessa händelser. SaaS-providern visar en ämnes typ, ett **partner ämne**, som prenumeranter använder för att använda händelser. Den erbjuder även en ren pub-sub-modell genom att åtskilja problem och ägarskap av resurser som används av händelse utgivare och prenumeranter.

## <a name="event-subscriptions"></a>Prenumerationer på händelser

En prenumeration visar Event Grid vilka händelser på ett ämne som du är intresse rad av att ta emot. När du skapar prenumerationen anger du en slut punkt för att hantera händelsen. Du kan filtrera de händelser som skickas till slut punkten. Du kan filtrera efter händelse typ eller ämnes mönster. Mer information finns i [Event Grid prenumerations schema](subscription-creation-schema.md).

Exempel på hur du skapar prenumerationer finns i:

* [Azure CLI-exempel för Event Grid](cli-samples.md)
* [Azure PowerShell-exempel för Event Grid](powershell-samples.md)
* [Azure Resource Manager-mallar för Event Grid](template-samples.md)

Information om hur du hämtar dina aktuella Event Grid-prenumerationer finns i [fråga Event Grid prenumerationer](query-event-subscriptions.md).

## <a name="event-subscription-expiration"></a>Förfallo datum för händelse prenumeration
Händelseprenumerationen upphör att gälla automatiskt efter det datumet. Ange ett förfallo datum för händelse prenumerationer som bara behövs under en begränsad tid och som du inte vill bekymra dig om att rensa dessa prenumerationer. Om du till exempel skapar en händelse prenumeration för att testa ett scenario kanske du vill ange ett förfallo datum. 

Ett exempel på hur du anger förfallo datum finns i [Prenumerera med avancerade filter](how-to-filter-events.md#subscribe-with-advanced-filters).

## <a name="event-handlers"></a>Händelsehanterare

Från ett Event Grid perspektiv är en händelse hanterare den plats där händelsen skickas. Hanteraren vidtar ytterligare åtgärder för att bearbeta händelsen. Event Grid stöder flera typer av hanterare. Du kan använda en Azure-tjänst som stöds eller din egen webhook som hanterare. Beroende på typen av hanterare Event Grid följande olika mekanismer för att garantera att händelsen levereras. För HTTP webhook-händelse hanterare görs ett nytt försök tills hanteraren returnerar status koden `200 – OK` . För Azure Storage kö görs ett nytt försök till händelserna tills Kötjänst har bearbetat meddelandet push-överförts till kön.

Information om hur du implementerar en Event Grid hanterare som stöds finns i [händelse hanterare i Azure Event Grid](event-handlers.md).

## <a name="security"></a>Säkerhet

Event Grid tillhandahåller säkerhet för att prenumerera på ämnen och publicera ämnen. När du prenumererar måste du ha tillräcklig behörighet i avsnittet om resurs-eller händelse rutnät. När du publicerar måste du ha en SAS-token eller nyckel-autentisering för ämnet. Mer information finns i [Event Grid säkerhet och autentisering](security-authentication.md).

## <a name="event-delivery"></a>Händelseleverans

Om Event Grid inte kan bekräfta att en händelse har mottagits av prenumerantens slut punkt, kommer den att leverera om händelsen. Mer information finns i [Event Grid meddelande leverans och försök igen](delivery-and-retry.md).

## <a name="batching"></a>Batchbearbetning

När du använder ett anpassat ämne måste händelser alltid publiceras i en matris. Detta kan vara en batch med en för låg data flödes scenarier, men för stora mängder användnings fall rekommenderar vi att du grupperar flera händelser per publicering för att uppnå högre effektivitet. Batchar kan vara upp till 1 MB och den maximala storleken för en händelse är 1 MB. 

## <a name="next-steps"></a>Nästa steg

* En introduktion till Event Grid finns i [Om Event Grid](overview.md).
* Information om hur du snabbt kommer igång med Event Grid finns i [skapa och dirigera anpassade händelser med Azure Event Grid](custom-event-quickstart.md).
