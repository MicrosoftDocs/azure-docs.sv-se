---
title: Snabb start – Anslut till ett team möte
author: askaur
ms.author: askaur
ms.date: 12/08/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 0c41771af81989ff965098a762338216db54fd27
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97578147"
---
## <a name="join-the-meeting-chat"></a>Delta i Mötes chatten 

När team samverkan är aktiverat kan en kommunikations tjänst användare ansluta till Team anropet som gäst användare med hjälp av det anropande klient biblioteket. När du ansluter till samtalet läggs de till som deltagare till mötes chatten, där de kan skicka och ta emot meddelanden med andra användare på anropet. Användaren har inte åtkomst till chatt meddelanden som har skickats innan de anslöt till anropet. 

## <a name="get-a-teams-meeting-chat-thread-for-a-communication-services-user"></a>Skaffa en team Meeting Chat-tråd för en kommunikations tjänst användare

Först instansierar `ChatThreadClient` du en för mötets chatt-tråd. Parsa Mötes länken eller Använd Graph API: er med mötes-ID: t för att hämta tråd-ID: t. 

- Länken Teams möte ser ut så här: `https://teams.microsoft.com/l/meetup-join/meeting_chat_thread_id/1606337455313?context=some_context_here` . Tråd-ID `meeting_chat_thread_id` är i den länken. 
- Om du har mötes-ID: t kan du använda [Graph API](https://docs.microsoft.com/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta) för att hämta tråd-ID: t. [Get API](https://docs.microsoft.com/graph/api/onlinemeeting-get?view=graph-rest-beta&tabs=http%22%20%5C) -svaret kommer att ha ett- `chatInfo` objekt som innehåller `threadID` . 

När du har ett chatt-ID kan du hämta en chatt-klient med hjälp av Java Script Chat klient bibliotek: 

```javascript
let chatThreadClient = await chatClient.getChatThreadClient(threadId); 

console.log(`Chat Thread client for threadId:${chatThreadClient.threadId}`); 
```
  
`chatThreadClient`Kan användas för att visa medlemmar i chatt-tråden, Hämta chatt-historik och skicka meddelanden.  

## <a name="send-and-receive-messages"></a>Skicka och ta emot meddelanden  

Använd `SendMessage` för att skicka ett meddelande till mötes chatten. För att ta emot inkommande meddelanden är möjligheten att prenumerera på händelser som `chatMessageReceived` inte stöds eftersom real tids signaler inte har Aktiver ATS för det här scenariot. För att få de senaste meddelandena kan du avsöka `ListMessages` API: et. För samverkan scenariot `ListMessages` stöder API: et nu tre nya meddelande typer:
- `RichText/HTML`
- `ThreadActivity/MemberJoined`
- `ThreadActivity/MemberLeft` </br>

Mer information om meddelande typer finns [här](../../../concepts/chat/concepts.md). 

**Obs!** för närvarande går det bara att skicka och ta emot meddelanden för samverkan scenarier med team. Andra funktioner som att skriva indikatorer och kommunikations tjänster användare som lägger till eller tar bort andra användare från Team-mötet stöds inte ännu.  

 
