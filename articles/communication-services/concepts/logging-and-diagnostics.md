---
title: Kommunikations tjänst loggar
titleSuffix: An Azure Communication Services concept document
description: Lär dig mer om loggning i Azure Communication Services
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 5ed75a7f8c7915645778696282e179af3a4d4091
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103493602"
---
# <a name="communication-services-logs"></a>Kommunikations tjänst loggar

Azure Communication Services erbjuder loggnings funktioner som du kan använda för att övervaka och felsöka kommunikations tjänst lösningen. Dessa funktioner kan konfigureras via Azure Portal.

## <a name="enable-diagnostic-logs-in-your-resource"></a>Aktivera diagnostikloggar i din resurs

Loggning är inaktive rad som standard när en resurs skapas. Om du vill aktivera loggning navigerar du till bladet **diagnostikinställningar** på resurs-menyn under avsnittet **övervakning** . Klicka sedan på **Lägg till diagnostisk inställning**.

Välj sedan det mål för arkiv som du vill använda. För närvarande har vi stöd för lagrings konton och Log Analytics som Arkiv mål. När du har valt de typer av loggar som du vill avbilda sparar du diagnostikinställningar.
 
Nya inställningar börjar gälla om tio minuter. Loggarna kommer att visas i det konfigurerade lagrings målet i fönstret loggar i kommunikations tjänst resursen.

:::image type="content" source="./media/diagnostic-settings.png" alt-text="Alternativ för ACS-diagnostiska inställningar.":::

Mer information om hur du konfigurerar diagnostik finns i översikten över [Azures resurs loggar](../../azure-monitor/essentials/platform-logs-overview.md).

## <a name="resource-log-categories"></a>Resurs loggs kategorier

Kommunikations tjänsterna erbjuder tre typer av loggar som du kan aktivera:

* **Användnings loggar** – tillhandahåller användnings data som är associerade med varje fakturerings tjänst erbjudande
* **Chat-operativa loggar** – innehåller grundläggande information om chatt tjänsten
* **SMS-operativa loggar** – innehåller grundläggande information som rör SMS-tjänsten
* **Drift loggar med autentisering** – innehåller grundläggande information om Autentiseringstjänsten

### <a name="usage-logs-schema"></a>Schema för användnings loggar

| Egenskap | Beskrivning |
| -------- | ---------------|
| Timestamp | Tidsstämpeln (UTC) för när loggen genererades. |
| Åtgärds namn | Åtgärden som är kopplad till logg posten. |
| Åtgärds version | `api-version`Associerat med åtgärden, om operationName utfördes med hjälp av ett API. Om det inte finns något API som motsvarar den här åtgärden representerar-versionen den åtgärd som är associerad med åtgärden i framtiden. |
| Kategori | Händelsens logg kategori. Kategori är den granularitet som du kan använda för att aktivera eller inaktivera loggar för en viss resurs. Egenskaperna som visas i en händelses egenskaps-BLOB är desamma inom en viss logg kategori och resurs typ. |
| Korrelations-ID | ID för korrelerade händelser. Kan användas för att identifiera korrelerade händelser mellan flera tabeller. |
| Egenskaper | Andra data som gäller för olika kommunikations tjänster. |
| Post-ID | Unikt ID för en specifik användnings post. |
| Användningstyp | Användnings läge. (till exempel chatt, PSTN, NAT osv.) |
| Enhets typ | Den typ av enhet som användningen baseras på för ett angivet användnings läge. (till exempel minuter, megabyte, meddelanden osv.). |
| Kvantitet | Antalet enheter som används eller används för den här posten. |

### <a name="chat-operational-logs"></a>Chat-operativa loggar

| Egenskap | Beskrivning |
| -------- | ---------------|
| TimeGenerated | Tidsstämpeln (UTC) för när loggen genererades. |
| OperationName | Åtgärden som är kopplad till logg posten. |
| CorrelationID | ID för korrelerade händelser. Kan användas för att identifiera korrelerade händelser mellan flera tabeller. |
| OperationVersion | Den API-version som är kopplad till åtgärden, om operationName utfördes med hjälp av ett API. Om det inte finns något API som motsvarar den här åtgärden representerar-versionen den åtgärd som är associerad med åtgärden i framtiden. |
| Kategori | Händelsens logg kategori. Kategori är den granularitet som du kan använda för att aktivera eller inaktivera loggar för en viss resurs. Egenskaperna som visas i en händelses egenskaps-BLOB är desamma inom en viss logg kategori och resurs typ. |
| ResultType | Status för åtgärden. |
| ResultSignature | Åtgärdens under status. Om den här åtgärden motsvarar ett REST API-anrop är det här fältet HTTP-statuskod för motsvarande REST-anrop. |
| ResultDescription | Den statiska text beskrivningen för den här åtgärden. |
| DurationMs | Åtgärdens varaktighet i millisekunder. |
| CallerIpAddress | IP-adressen för anroparen, om åtgärden motsvarar ett API-anrop som kommer från en entitet med en offentligt tillgänglig IP-adress. |
| Nivå | Händelsens allvarlighets grad. |
| URI | URI för begäran. |
| UserId | Begär avsändarens användar-ID. |
| ChatThreadId | Det tråd-ID för chatt som är associerat med begäran. |
| ChatMessageId | Det chatt meddelande-ID som är associerat med begäran. |
| SdkType | Den SDK-typ som används i begäran. |
| PlatformType | Den plattforms typ som används i begäran. |

### <a name="sms-operational-logs"></a>SMS-operativa loggar

| Egenskap | Beskrivning |
| -------- | ---------------|
| TimeGenerated | Tidsstämpeln (UTC) för när loggen genererades. |
| OperationName | Åtgärden som är kopplad till logg posten. |
| CorrelationID | ID för korrelerade händelser. Kan användas för att identifiera korrelerade händelser mellan flera tabeller. |
| OperationVersion | Den API-version som är kopplad till åtgärden, om operationName utfördes med hjälp av ett API. Om det inte finns något API som motsvarar den här åtgärden representerar-versionen den åtgärd som är associerad med åtgärden i framtiden. |
| Kategori | Händelsens logg kategori. Kategori är den granularitet som du kan använda för att aktivera eller inaktivera loggar för en viss resurs. Egenskaperna som visas i en händelses egenskaps-BLOB är desamma inom en viss logg kategori och resurs typ. |
| ResultType | Status för åtgärden. |
| ResultSignature | Åtgärdens under status. Om den här åtgärden motsvarar ett REST API-anrop är det här fältet HTTP-statuskod för motsvarande REST-anrop. |
| ResultDescription | Den statiska text beskrivningen för den här åtgärden. |
| DurationMs | Åtgärdens varaktighet i millisekunder. |
| CallerIpAddress | IP-adressen för anroparen, om åtgärden motsvarar ett API-anrop som kommer från en entitet med en offentligt tillgänglig IP-adress. |
| Nivå | Händelsens allvarlighets grad. |
| URI | URI för begäran. |
| OutgoingMessageLength | Antalet tecken i det utgående meddelandet. |
| IncomingMessageLength | Antalet tecken i det inkommande meddelandet. |
| DeliveryAttempts | Antalet försök som har gjorts att leverera det här meddelandet. |
| PhoneNumber | Telefonnumret som SMS-meddelandet skickas till. |
| SdkType | Den SDK-typ som används i begäran. |
| PlatformType | Den plattforms typ som används i begäran. |
| Metod | Den metod som används i begäran. |

### <a name="authentication-operational-logs"></a>Fungerande loggar för autentisering

| Egenskap | Beskrivning |
| -------- | ---------------|
| TimeGenerated | Tidsstämpeln (UTC) för när loggen genererades. |
| OperationName | Åtgärden som är kopplad till logg posten. |
| CorrelationID | ID för korrelerade händelser. Kan användas för att identifiera korrelerade händelser mellan flera tabeller. |
| OperationVersion | Den `api-version` som är associerad med åtgärden, om den `operationName` utfördes med hjälp av ett API. Om det inte finns något API som motsvarar den här åtgärden representerar-versionen den åtgärd som är associerad med åtgärden i framtiden. |
| Kategori | Händelsens logg kategori. Kategori är den granularitet som du kan använda för att aktivera eller inaktivera loggar för en viss resurs. Egenskaperna som visas i en händelses egenskaps-BLOB är desamma inom en viss logg kategori och resurs typ. |
| ResultType | Status för åtgärden. |
| ResultSignature | Åtgärdens under status. Om den här åtgärden motsvarar ett REST API-anrop är det här fältet HTTP-statuskod för motsvarande REST-anrop. |
| DurationMs | Åtgärdens varaktighet i millisekunder. |
| CallerIpAddress | IP-adressen för anroparen, om åtgärden motsvarar ett API-anrop som kommer från en entitet med en offentligt tillgänglig IP-adress. |
| Nivå | Händelsens allvarlighets grad. |
| URI | URI för begäran. |
| SdkType | Den SDK-typ som används i begäran. |
| PlatformType | Den plattforms typ som används i begäran. |
| Identitet | Kommunikations tjänstens identitet som är relaterad till åtgärden. |
| Omfattningar | De kommunikations tjänst omfattningar som finns i åtkomsttoken. |
