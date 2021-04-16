---
title: Översikt över Chat SDK för Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Läs mer om Azure Communication Services Chat SDK.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 561855704d157f9ad826b5db83600a79d9437fc6
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107500693"
---
# <a name="chat-sdk-overview"></a>Översikt över Chat SDK 

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include-chat.md)]

Azure Communication Services-SDK:er för chatt kan användas för att lägga till omfattande realtidschattar i dina program.
    
## <a name="chat-sdk-capabilities"></a>Funktioner för chatt-SDK    

I följande lista visas en uppsättning funktioner som för närvarande är tillgängliga i Communication Services för chatt.  

| Grupp med funktioner | Funktion | JavaScript  | Java | .NET | Python | iOS | Android |
|-----------------|-------------------|---|-----|----|-----|----|----|
| Kärnfunktioner | Skapa en chatttråd mellan 2 eller fler användare                                                     | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |    
|                   | Uppdatera ämnet för en chatttråd                                                                              | ✔️   | ✔️ | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | Lägga till eller ta bort deltagare från en chatttråd                                                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | Välj om du vill dela chattmeddelandehistorik med deltagaren som läggs till                                   | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   | 
|                   | Hämta en lista över deltagare i en chatttråd                                                                          | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   | 
|                   | Ta bort en chatttråd                                                                                              | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|                   | Givet en kommunikationsanvändare hämtar du listan över chatttrådar som användaren är en del av                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | Hämta information för en viss chatttråd                                                                              | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |   
|                   | Skicka och ta emot meddelanden i en chatttråd                                                                            | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | Uppdatera innehållet i det skickade meddelandet                                                                               | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |    
|                   | Ta bort ett meddelande som du har skickat tidigare                                                                                                      | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |    
|                   | Läs kvitton för meddelanden som har lästs av andra deltagare i en chatt                                        | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | Få ett meddelande när deltagarna aktivt skriver ett meddelande i en chatttråd                                         | ✔️   | ✔️   | ✔️    | ✔️    |  ✔️    | ✔️   | 
|                   | Hämta alla meddelanden i en chatttråd                                                                        | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   | 
|                   | Skicka Unicode-emojis som en del av meddelandeinnehåll                                                                            | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|Realtidsmeddelanden (aktiveras av tillverkarspecifika signalpaket**)|  Chattklienter kan prenumerera för att få realtidsuppdateringar för inkommande meddelanden och andra åtgärder som sker i en chatttråd. En lista över uppdateringar som stöds för aviseringar i realtid finns i [Chattbegrepp](concepts.md#real-time-notifications)                                     | ✔️   | ❌    | ❌  | ❌  | ✔️  | ✔️  |   
| Integrering med Azure Event Grid             | Använd de chatthändelser som är Azure Event Grid för att ansluta anpassade meddelandetjänster eller publicera händelsen till en webhook för att köra affärslogik som att uppdatera CRM-poster när en chatt har slutförts   | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
| Rapportera </br>(Den här informationen finns under fliken Övervakning för din Communication Services resurs på Azure Portal)      | Förstå API-trafik från din chattapp genom att övervaka publicerade mått i Azure Metrics Explorer och ställa in aviseringar för att identifiera avvikelser     | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | Övervaka och felsöka din Communication Services genom att aktivera diagnostisk loggning för din resurs    | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |   


**Det egna signalpaketet implementeras med hjälp av webbsocketar. Det går tillbaka till lång avsökning om webbsocketar inte stöds.  

## <a name="javascript-chat-sdk-support-by-os-and-browser"></a>Stöd för JavaScript Chat SDK från operativsystem och webbläsare    

I följande tabell visas en uppsättning webbläsare och versioner som stöds och som är tillgängliga för närvarande.
    
|                                  | Windows          | macOS          | Ubuntu | Linux  | Android | iOS    | iPad OS|
|--------------------------------|----------------|--------------|-------|------|------|------|-------|
| **Chatt-SDK** | Firefox,*Chrome*, ny Edge | Firefox,*Chrome,* Safari* | Chrome*  | Chrome* | Chrome* | Safari* | Safari* |

*Observera att den senaste versionen stöds utöver de föregående två versionerna.<br/>   

## <a name="next-steps"></a>Nästa steg   

> [!div class="nextstepaction"] 
> [Kom igång med chatt](../../quickstarts/chat/get-started.md)    

Följande dokument kan vara intressanta för dig:  
- Bekanta dig med [chattbegrepp](../chat/concepts.md)
- Förstå hur [priser](../pricing.md#chat) fungerar för chatt
