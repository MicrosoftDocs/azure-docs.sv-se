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
ms.openlocfilehash: 520dc611e49675f35b8ba0330448438192770773
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106168883"
---
# <a name="chat-sdk-overview"></a>Översikt över chatt SDK 

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include-chat.md)]

Azure Communication Services Chat SDK: er kan användas för att lägga till omfattande chatt i real tid till dina program.
    
## <a name="chat-sdk-capabilities"></a>Chatta SDK-funktioner    

I följande lista visas en uppsättning funktioner som för närvarande är tillgängliga i kommunikations tjänsterna för chatt-SDK: er.  

| Grupp med funktioner | Funktion | JavaScript  | Java | .NET | Python | iOS | Android |
|-----------------|-------------------|---|-----|----|-----|----|----|
| Kärn funktioner | Skapa en chatt-tråd mellan två eller flera användare                                                     | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |    
|                   | Uppdatera ämnet i en chatt-tråd                                                                              | ✔️   | ✔️ | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | Lägga till eller ta bort deltagare från en chatt-tråd                                                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | Välj om du vill dela chatt meddelande historik med deltagaren som läggs till                                   | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   | 
|                   | Hämta en lista över deltagare i en chatt-tråd                                                                          | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   | 
|                   | Ta bort en chatt-tråd                                                                                              | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|                   | Med en kommunikations användare får du en lista över chatt-trådar som användaren ingår i                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | Hämta information om en viss chatt-tråd                                                                              | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |   
|                   | Skicka och ta emot meddelanden i en chatt-tråd                                                                            | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | Uppdatera innehållet i det skickade meddelandet                                                                               | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |    
|                   | Ta bort ett meddelande som du har skickat                                                                                                      | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |    
|                   | Läs kvitton för meddelanden som har lästs av andra deltagare i en chatt                                        | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | Få ett meddelande när deltagare aktivt skriver ett meddelande i en chatt-tråd                                         | ✔️   | ✔️   | ✔️    | ✔️    |  ✔️    | ✔️   | 
|                   | Hämta alla meddelanden i en chatt-tråd                                                                        | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   | 
|                   | Skicka Unicode-emojis som en del av meddelande innehållet                                                                            | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|Real tids aviseringar (aktiverade av patentskyddade signal paket * *)|  Chatt-klienter kan prenumerera på att få real tids uppdateringar för inkommande meddelanden och andra åtgärder som inträffar i en chatt-tråd. Om du vill se en lista över uppdateringar som stöds för meddelanden i real tid, se [chatt-begrepp](concepts.md#real-time-notifications)                                     | ✔️   | ❌    | ❌  | ❌  | ❌  | ❌  | 
| Integrering med Azure Event Grid             | Använd de Chat-händelser som är tillgängliga i Azure Event Grid för att skicka anpassade meddelande tjänster eller publicera händelsen till en webhook för att köra affärs logik som att uppdatera CRM-poster när en chatt har körts   | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
| Rapportera </br>(Den här informationen är tillgänglig under fliken övervakning för kommunikations tjänst resursen på Azure Portal)      | Förstå API-trafik från chatt-appen genom att övervaka publicerade mått i Azure Metrics Explorer och ange aviseringar för att identifiera avvikelser     | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | Övervaka och Felsök kommunikations tjänst lösningen genom att aktivera diagnostisk loggning för din resurs    | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |   


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
- Förstå hur [prissättning](../pricing.md#chat) fungerar för chatt
