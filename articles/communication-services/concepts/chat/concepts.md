---
title: Chatta koncept i Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Lär dig mer om chatt-koncept för kommunikations tjänster.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 9b249bddc4cd269933a39b5baf77995aec1e82b3
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653942"
---
# <a name="chat-concepts"></a>Chattbegrepp

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Azure Communication Services Chat-klient bibliotek kan användas för att lägga till text i real tid i dina program. Den här sidan sammanfattar viktiga chatt-koncept och-funktioner.

Se [Översikt över kommunikations tjänstens klient bibliotek](./sdk-features.md) för att lära dig mer om aktuella klient biblioteks språk och-funktioner.

## <a name="chat-overview"></a>Översikt över chatt 

Chat-konversationer sker inom chatt-trådar. En chatt-tråd kan innehålla många meddelanden och många användare. Varje meddelande tillhör en enda tråd och en användare kan vara en del av en eller flera trådar. 

Varje användare i chatten kallas för en medlem. Du kan ha upp till 250 medlemmar i en chatt-tråd. Endast tråd medlemmar kan skicka och ta emot meddelanden eller lägga till/ta bort medlemmar i en chatt-tråd. Den största tillåtna meddelande storleken är ungefär 28KB. Du kan hämta alla meddelanden i en chatt-tråd med hjälp av `List/Get Messages` åtgärden. Kommunikations tjänsterna lagrar chatten tills du kör en borttagnings åtgärd i chatten eller meddelandet, eller tills inga medlemmar återstår i chatt-tråden där det är överblivna och bearbetas för borttagning.   

För chatt-trådar med fler än 20 medlemmar inaktive ras Läs kvitton och skriv indikator funktioner. 

## <a name="chat-architecture"></a>Chatt-arkitektur

Det finns två kärn delar för att chatta arkitektur: 1) klient program för betrodda tjänster och 2).

:::image type="content" source="../../media/chat-architecture.png" alt-text="Diagram som visar kommunikations tjänsternas Chat-arkitektur.":::

 - **Betrodd tjänst:** Om du vill hantera en chatt korrekt behöver du en tjänst som hjälper dig att ansluta till kommunikations tjänster med hjälp av resurs anslutnings strängen. Den här tjänsten ansvarar för att skapa chatt-trådar, hantera tråd medlemskap och tillhandahålla åtkomsttoken till användare. Du hittar mer information om åtkomsttoken i snabb starten för våra [åtkomsttoken](../../quickstarts/access-tokens.md) .

 - **Klient app:**  Klient programmet ansluter till din betrodda tjänst och tar emot åtkomsttoken som används för att ansluta direkt till kommunikations tjänsterna. När anslutningen har upprättats kan klient programmet skicka och ta emot meddelanden.

Vi rekommenderar att du skapar åtkomsttoken med hjälp av den betrodda tjänst nivån. I det här scenariot är Server sidan ansvarig för att skapa och hantera användare och utfärda sina token.
    
## <a name="message-types"></a>Meddelande typer

Kommunikations tjänster-chatt delar användarspecifika meddelanden och systemgenererade meddelanden som kallas **tråd aktiviteter**. Tråd aktiviteter skapas när en chatt-tråd uppdateras. När du anropar `List Messages` eller `Get Messages` på en chatt-tråd innehåller resultatet de användare-genererade textmeddelandena och system meddelandena i kronologisk ordning. Detta hjälper dig att identifiera när en medlem har lagts till eller tagits bort eller när chatt ämnet uppdaterades. Följande meddelande typer stöds:  

 - `Text`: Ett oformaterat textmeddelande som består av och skickas av en användare som en del av en chat-konversation.
 - `RichText/HTML`: Ett formaterat textmeddelande. Observera att kommunikations tjänst användare för närvarande inte kan skicka RichText-meddelanden. Den här meddelande typen stöds av meddelanden som skickas från Team användare till kommunikations tjänster användare i team interop-scenarier.

 - `ThreadActivity/ParticipantAdded`: Ett system meddelande som anger att en eller flera deltagare har lagts till i chatt-tråden. Exempel:

```
{
            "id": "1613589626560",
            "type": "participantAdded",
            "sequenceId": "7",
            "version": "1613589626560",
            "content":
            {
                "participants":
                [
                    {
                        "id": "8:acs:d2a829bc-8523-4404-b727-022345e48ca6_00000008-511c-4df6-f40f-343a0d003226",
                        "displayName": "Jane",
                        "shareHistoryTime": "1970-01-01T00:00:00Z"
                    }
                ],
                "initiator": "8:acs:d2a829bc-8523-4404-b727-022345e48ca6_00000008-511c-4ce0-f40f-343a0d003224"
            },
            "createdOn": "2021-02-17T19:20:26Z"
        }
```

- `ThreadActivity/ParticipantRemoved`: System meddelande som anger att en deltagare har tagits bort från chatt-tråden. Exempel:

```
{
            "id": "1613589627603",
            "type": "participantRemoved",
            "sequenceId": "8",
            "version": "1613589627603",
            "content":
            {
                "participants":
                [
                    {
                        "id": "8:acs:d2a829bc-8523-4404-b727-022345e48ca6_00000008-511c-4df6-f40f-343a0d003226",
                        "displayName": "Jane",
                        "shareHistoryTime": "1970-01-01T00:00:00Z"
                    }
                ],
                "initiator": "8:acs:d2a829bc-8523-4404-b727-022345e48ca6_00000008-511c-4ce0-f40f-343a0d003224"
            },
            "createdOn": "2021-02-17T19:20:27Z"
        }
```

- `ThreadActivity/TopicUpdate`: System meddelande som anger tråd ämnet har uppdaterats. Exempel:

```
{
            "id": "1613589623037",
            "type": "topicUpdated",
            "sequenceId": "2",
            "version": "1613589623037",
            "content":
            {
                "topic": "New topic",
                "initiator": "8:acs:d2a829bc-8523-4404-b727-022345e48ca6_00000008-511c-4ce0-f40f-343a0d003224"
            },
            "createdOn": "2021-02-17T19:20:23Z"
        }
```

## <a name="real-time-signaling"></a>Real tids signalering 

Klient biblioteket för chat Java Script innehåller real tids signalering. Detta gör det möjligt för klienter att lyssna efter uppdateringar och inkommande meddelanden i real tid till en chatt-tråd utan att behöva avsöka API: erna. Tillgängliga händelser är:

 - `ChatMessageReceived` – När ett nytt meddelande skickas till en chatt-tråd som användaren är medlem i. Den här händelsen skickas inte för automatiskt genererade system meddelanden som vi beskrivit i föregående avsnitt.  
 - `ChatMessageEdited` – När ett meddelande redige ras i en chatt-tråd som användaren är medlem i. 
 - `ChatMessageDeleted` – När ett meddelande tas bort i en chatt-tråd som användaren är medlem i. 
 - `TypingIndicatorReceived` – När en annan medlem skriver ett meddelande i en chatt-tråd som användaren är medlem i. 
 - `ReadReceiptReceived` – När en annan medlem har läst meddelandet som användaren skickade i en chatt-tråd. 

## <a name="chat-events"></a>Chatta händelser 

Med real tids signaler kan användarna chatta i real tid. Dina tjänster kan använda Azure Event Grid för att prenumerera på chatt-relaterade händelser. Mer information finns i [begrepp för händelse hantering](../event-handling.md).

## <a name="using-cognitive-services-with-chat-client-library-to-enable-intelligent-features"></a>Använda Cognitive Services med klient biblioteket i Chat för att aktivera intelligenta funktioner

Du kan använda [Azure kognitiva API: er](../../../cognitive-services/index.yml) med klient biblioteket i Chat för att lägga till smarta funktioner i dina program. Du kan till exempel:

- Gör det möjligt för användare att chatta med varandra på olika språk. 
- Hjälp en support agent att prioritera biljetter genom att identifiera ett negativt sentiment av ett inkommande problem från en kund.
- Analysera inkommande meddelanden för nyckel identifiering och entitets igenkänning och fråga relevant information till användaren i din app baserat på meddelandets innehåll.

Ett sätt att uppnå detta är genom att låta din betrodda tjänst agera som medlem i en chatt. Anta att du vill aktivera översättning av språk. Den här tjänsten ansvarar för att lyssna på meddelanden som utbyts av andra medlemmar [1], anropar kognitiva API: er för att översätta innehållet till det önskade språket [2, 3] och skicka det översatta resultatet som ett meddelande i chatt-tråden [4]. 

På så sätt kommer meddelande historiken innehålla både original meddelanden och översatta meddelanden. I klient programmet kan du lägga till logik för att visa det ursprungliga eller översatta meddelandet. I [den här snabb](../../../cognitive-services/translator/quickstart-translator.md) starten får du veta hur du använder kognitiva API: er för att översätta text till olika språk. 

:::image type="content" source="../media/chat/cognitive-services.png" alt-text="Diagram som visar Cognitive Services interagera med kommunikations tjänster.":::

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kom igång med chatt](../../quickstarts/chat/get-started.md)

Följande dokument kan vara intressanta för dig:

- Bekanta dig med [klient biblioteket för chat](sdk-features.md)
