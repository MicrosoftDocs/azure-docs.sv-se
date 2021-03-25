---
title: Översikt över chatt SDK för Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Lär dig mer om Azure Communication Services Chat SDK.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: b275c3af2e92dc5af677120b5082751d19676b2e
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110822"
---
# <a name="chat-sdk-overview"></a>Översikt över chatt SDK 

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]    

Azure Communication Services Chat SDK: er kan användas för att lägga till omfattande chatt i real tid till dina program.
    
## <a name="chat-sdk-capabilities"></a>Chatta SDK-funktioner    

I följande lista visas en uppsättning funktioner som för närvarande är tillgängliga i kommunikations tjänsterna för chatt-SDK: er.  

| Grupp med funktioner | Funktion | JavaScript  | Java | .NET | Python | iOS | Android |
|-----------------|-------------------|---|-----|----|-----|----|----|
| Kärn funktioner | Skapa en chatt-tråd mellan två eller flera användare (upp till 250 användare)                                                       | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |    
|                   | Uppdatera ämnet i en chatt-tråd                                                                              | ✔️   | ✔️ | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | Lägga till eller ta bort deltagare från en chatt-tråd                                                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | Välj om du vill dela chatt meddelande historik med deltagaren som läggs till                                   | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   | 
|                   | Hämta en lista över deltagare i en chatt-tråd                                                                          | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   | 
|                   | Ta bort en chatt-tråd                                                                                              | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|                   | Med en kommunikations användare får du en lista över chatt-trådar som användaren ingår i                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | Hämta information om en viss chatt-tråd                                                                              | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |   
|                   | Skicka och ta emot meddelanden i en chatt-tråd                                                                            | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | Redigera innehållet i ett skickat meddelande                                                                                | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |   
|                   | Ta bort ett meddelande                                                                                                       | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |   
|                   | Läs kvitton för meddelanden som har lästs av andra deltagare i en chatt <br/> *Inte tillgängligt när det finns fler än 20 deltagare i en chatt-tråd*    | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | Få ett meddelande när deltagare aktivt skriver ett meddelande i en chatt-tråd <br/> *Inte tillgängligt när det finns fler än 20 medlemmar i en chatt-tråd*      | ✔️   | ✔️   | ✔️    | ✔️    |  ✔️    | ✔️   | 
|                   | Hämta alla meddelanden i en chatt-tråd <br/>                                                                         | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | Skicka Unicode-emojis som en del av meddelande innehållet                                                                            | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|Real tids signalering (aktive rad av patentskyddat signal paket * *)|  Prenumerera för att få uppdateringar i real tid för inkommande meddelanden och andra åtgärder i chatt-appen. Om du vill se en lista över uppdateringar som stöds för real tids signalering, se [chatt-koncept](concepts.md#real-time-signaling)                                     | ✔️   | ❌    | ❌  | ❌  | ❌  | ❌  |    
| Event Grid support             | Använd integration med Azure Event Grid och konfigurera kommunikations tjänsten att köra affärs logik baserat på Chat-aktivitet eller ansluta en anpassad Push Notification-tjänst   | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
| Övervakning        | Använd Mät värdena för API-begäranden som släpps i Azure Portal för att skapa instrument paneler, övervaka hälso tillståndet för chatt-appen och ange aviseringar för att identifiera avvikelser      | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|                   | Konfigurera kommunikations tjänst resursen för att ta emot chatt-operativa loggar för övervakning och diagnostik          | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  


* * Det tillverkarspecifika signal paketet implementeras med hjälp av Web Sockets. Det kommer att återgå till lång avsökning om Web Sockets inte stöds.  

## <a name="javascript-chat-sdk-support-by-os-and-browser"></a>Java Script Chat SDK-stöd från OS och webbläsare    

Följande tabell visar en uppsättning webbläsare och versioner som stöds och som är tillgängliga för tillfället.
    
|                                  | Windows          | macOS          | Ubuntu | Linux  | Android | iOS    | iPad OS|
|--------------------------------|----------------|--------------|-------|------|------|------|-------|
| **Chatta SDK** | Firefox *, Chrome*, ny kant | Firefox *, Chrome*, Safari * | Chrome  | Chrome | Chrome | Safari | Safari |

* Observera att den senaste versionen stöds utöver de tidigare två versionerna.<br/>   

## <a name="next-steps"></a>Nästa steg   

> [!div class="nextstepaction"] 
> [Kom igång med chatt](../../quickstarts/chat/get-started.md)    

Följande dokument kan vara intressanta för dig:  
- Bekanta dig med [chatt-koncept](../chat/concepts.md)