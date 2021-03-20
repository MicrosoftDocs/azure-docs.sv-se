---
title: Diagnostikloggar för Hybridanslutningar
description: Den här artikeln innehåller en översikt över alla aktiviteter och diagnostikloggar som är tillgängliga för Azure Relay.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 9b459750ad1445da89a8e89a10a35b878bfb64e1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100590874"
---
# <a name="enable-diagnostics-logs-for-azure-relay-hybrid-connections"></a>Aktivera diagnostikloggar för Azure Relay Hybridanslutningar
När du börjar använda Azure Relay Hybridanslutningar kanske du vill övervaka hur och när dina lyssnare och avsändare öppnas och stängs och hur dina Hybridanslutningar skapas och meddelanden skickas. Den här artikeln innehåller en översikt över aktivitets-och diagnos loggar som tillhandahålls av tjänsten Azure Relay. 

Du kan visa två typer av loggar för Azure Relay:

- [Aktivitets loggar](../azure-monitor/essentials/platform-logs-overview.md): dessa loggar innehåller information om åtgärder som utförs mot ditt namn område i Azure Portal eller genom Azure Resource Manager mall. De här loggarna är alltid aktiverade. Exempel: "skapa eller uppdatera namnrymd", "skapa eller uppdatera hybrid anslutning". 
- [Diagnostikloggar](../azure-monitor/essentials/platform-logs-overview.md): du kan konfigurera diagnostikloggar för en mer omfattande vy av allt som händer med åtgärder och åtgärder som utförs mot ditt namn område med hjälp av API: et, eller via SDK för språk.

## <a name="view-activity-logs"></a>Visa aktivitetsloggar
Om du vill visa aktivitets loggar för Azure Relay namn området växlar du till **aktivitets logg** sidan i Azure Portal.

![Azure Relay-aktivitets logg](./media/diagnostic-logs/activity-log.png)

## <a name="enable-diagnostic-logs"></a>Aktivera diagnostikloggar

> [!NOTE]
> Diagnostikloggar är bara tillgängliga för Hybridanslutningar, inte för Windows Communication Foundation WCF-relä.

Så här aktiverar du diagnostikloggar:

1. I [Azure Portal](https://portal.azure.com)går du till ditt Azure Relay-namnområde och väljer sedan **diagnostiska inställningar** under **övervakning**.
1. På sidan **diagnostikinställningar** väljer du **Lägg till diagnostisk inställning**.  

   ![Länken "Lägg till diagnostisk inställning"](./media/diagnostic-logs/add-diagnostic-setting.png)

1. Konfigurera diagnostikinställningar genom att utföra följande steg:
    1. I rutan **namn** anger du ett namn för diagnostikinställningar.  
    2. Välj **HybridConnectionsEvent** som typ av logg. 
    3. Välj någon av följande tre **destinationer** för dina diagnostikloggar:  
        1. Om du väljer **Arkiv till ett lagrings konto** konfigurerar du det lagrings konto där diagnostikloggar ska lagras.  
        2. Om du väljer **Stream till en händelsehubben** konfigurerar du den händelsehubben som du vill strömma diagnostikloggar till.
        3. Om du väljer **Skicka till Log Analytics** anger du vilken instans av Log Analytics diagnostiken ska skickas till.  

        ![Exempel på diagnostikinställningar](./media/diagnostic-logs/sample-diagnostic-settings.png)
1. Spara inställningarna genom att välja **Spara** i verktygsfältet.

De nya inställningarna börjar gälla om 10 minuter. Loggarna visas i det konfigurerade lagrings målet i fönstret **diagnostikloggar** . Mer information om hur du konfigurerar diagnostikinställningar finns i [översikten över Azure Diagnostics-loggar](../azure-monitor/essentials/platform-logs-overview.md).


## <a name="schema-for-hybrid-connections-events"></a>Schema för Hybrid anslutnings händelser
I händelse loggen för Hybrid anslutningar ingår de element som anges i följande tabell:

| Name | Beskrivning |
| ------- | ------- |
| ResourceId | Resurs-ID för Azure Resource Manager |
| ActivityId | Internt ID som används för att identifiera den angivna åtgärden. Kan även kallas "TrackingId" |
| Slutpunkt | Vidarebefordrings resursens adress |
| OperationName | Den typ av Hybridanslutningar-åtgärd som loggas |
| EventTimeString | UTC-tidsstämpeln för logg posten |
| Meddelande | Det detaljerade meddelandet av händelsen |
| Kategori | Händelsens kategori. För närvarande finns det bara `HybridConnectionsEvents` . 


## <a name="sample-hybrid-connections-event"></a>Exempel händelse för Hybrid anslutningar
Här är ett exempel på en hybrid anslutnings händelse i JSON-format. 

```json
{
    "resourceId": "/SUBSCRIPTIONS/0000000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/MyResourceGroup/PROVIDERS/MICROSOFT.RELAY/NAMESPACES/MyRelayNamespace",
    "ActivityId": "7006a0db-27eb-445c-939b-ce86133014cc",
    "endpoint": "sb://myrelaynamespace.servicebus.windows.net/myhybridconnection/7006a0db-27eb-445c-939b-ce86133014cc_G5",
    "operationName": "Microsoft.Relay/HybridConnections/NewSenderRegistering",
    "EventTimeString": "2020-04-27T20:27:57.3842810Z",
    "message": "A new sender is registering.",
    "category": "HybridConnectionsEvent"
}
```

## <a name="events-and-operations-captured-in-diagnostic-logs"></a>Händelser och åtgärder som har registrerats i diagnostikloggar

| Åtgärd | Beskrivning | 
| --------- | ----------- | 
| AuthorizationFailed | Auktoriseringen misslyckades.|
| InvalidSasToken | Ogiltig SAS-token. | 
| ListenerAcceptingConnection | Lyssnaren accepterar anslutning. |
| ListenerAcceptingConnectionTimeout | Den lyssnare som accepterar anslutningen har nått sin tids gräns. |
| ListenerAcceptingHttpRequestFailed | Lyssnaren som accepterar HTTP-begäran misslyckades på grund av ett undantag. |
| ListenerAcceptingRequestTimeout | Den lyssnare som accepterar begäran har nått sin tids gräns. |  
| ListenerClosingFromExpiredToken | Lyssnaren stängs eftersom säkerhetstoken har upphört att gälla. | 
| ListenerRejectedConnection | Lyssnaren har avvisat anslutningen. |
| ListenerReturningHttpResponse | Lyssnaren returnerar ett HTTP-svar. |  
| ListenerReturningHttpResponseFailed | Lyssnaren returnerar ett HTTP-svar med en felkod. | 
 ListenerSentHttpResponse | Relä tjänsten har tagit emot ett HTTP-svar från lyssnaren. | 
| ListenerUnregistered | Lyssnaren har avregistrerats. | 
| ListenerUnresponsive | Lyssnaren svarar inte när ett svar returneras. | 
| MessageSendingToListener | Meddelandet skickas till lyssnaren. |
| MessageSentToListener | Meddelandet skickas till lyssnaren. | 
| NewListenerRegistered | En ny lyssnare har registrerats. |
| NewSenderRegistering | Ny avsändare registreras. | 
| ProcessingRequestFailed | Det gick inte att bearbeta en hybrid anslutnings åtgärd. | 
| SenderConnectionClosed | Avsändarens anslutning har stängts. |
| SenderListenerConnectionEstablished | Avsändaren och lyssnaren etablerade anslutningen har upprättats. |
| SenderSentHttpRequest | Avsändaren skickade en HTTP-begäran. | 


## <a name="next-steps"></a>Nästa steg

Mer information om Azure Relay finns i:

* [Introduktion till Azure Relay](relay-what-is-it.md)
* [Kom igång med Relay hybridanslutningar](relay-hybrid-connections-dotnet-get-started.md)
