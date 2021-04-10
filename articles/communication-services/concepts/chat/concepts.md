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
ms.openlocfilehash: 389d2282812406c50cddf255be2219fa203b0895
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729668"
---
# <a name="chat-concepts"></a>Chattbegrepp 

Azure Communication Services Chat SDK: er kan användas för att lägga till text i real tid i dina program. Den här sidan sammanfattar viktiga chatt-koncept och-funktioner.    

Se [översikten över kommunikations tjänsterna i Chat SDK](./sdk-features.md) för att lära dig mer om de olika SDK-språken och-funktionerna.  

## <a name="chat-overview"></a>Översikt över chatt    

Chat-konversationer sker inom **chatt-trådar**. Chatt-trådar har följande egenskaper:

- En chatt-tråd identifieras unikt av sin `ChatThreadId` . 
- Chatt-trådar kan ha en eller många användare som deltagare som kan skicka meddelanden till den. 
- En användare kan vara en del av en eller flera chatt-trådar. 
- Endast tråd deltagarna har åtkomst till en specifik chatt-tråd, och endast de kan utföra åtgärder för chatt-trådar. Dessa åtgärder omfattar att skicka och ta emot meddelanden, lägga till deltagare och ta bort deltagare. 
- Användare läggs automatiskt till som deltagare till alla chatt-trådar som de skapar.

### <a name="user-access"></a>Användaråtkomst
Vanligt vis har trådens skapare och deltagare samma åtkomst nivå till tråden och kan köra alla relaterade åtgärder som är tillgängliga i SDK, inklusive ta bort den. Deltagarna har inte skriv åtkomst till meddelanden som skickats av andra deltagare, vilket innebär att endast meddelande avsändaren kan uppdatera eller ta bort sina skickade meddelanden. Om en annan deltagare försöker göra det får de ett fel meddelande. 

Om du vill begränsa åtkomsten till chatt funktioner för en uppsättning användare kan du konfigurera åtkomst som en del av din betrodda tjänst. Din betrodda tjänst är den tjänst som dirigerar autentiseringen och auktoriseringen av chatt-deltagare. Vi kommer att utforska detta i detalj nedan.  

### <a name="chat-data"></a>Chat-data 
Kommunikations tjänster lagrar Chat-historik tills den tas bort uttryckligen. Konversations deltagare i chatten kan använda `ListMessages` för att Visa meddelande historik för en viss tråd. Användare som har tagits bort från en chatt kan visa tidigare meddelande historik, men de kan inte skicka eller ta emot nya meddelanden som en del av den här chatt-tråden. En helt inaktiv tråd utan deltagare tas automatiskt bort efter 30 dagar. Mer information om data som lagras av kommunikations tjänster finns i dokumentationen om [Sekretess](../privacy.md).  

### <a name="service-limits"></a>Tjänstbegränsningar  
- Det maximala antalet deltagare som tillåts i en chatt-tråd är 250.   
- Den största tillåtna meddelande storleken är cirka 28 KB.  
- För chatt-trådar med fler än 20 deltagare stöds inte Läs kvitton och skriv indikator funktioner.    

## <a name="chat-architecture"></a>Chatt-arkitektur    

Det finns två kärn delar för att chatta arkitektur: 1) klient program för betrodda tjänster och 2).    

:::image type="content" source="../../media/chat-architecture.png" alt-text="Diagram som visar kommunikations tjänsternas Chat-arkitektur."::: 

 - **Betrodd tjänst:** Om du vill hantera en chatt korrekt behöver du en tjänst som hjälper dig att ansluta till kommunikations tjänster med hjälp av resurs anslutnings strängen. Den här tjänsten ansvarar för att skapa chatt-trådar, lägga till och ta bort deltagare och utfärda åtkomsttoken till användare. Du hittar mer information om åtkomsttoken i snabb starten för våra [åtkomsttoken](../../quickstarts/access-tokens.md) .  
 - **Klient app:**  Klient programmet ansluter till din betrodda tjänst och tar emot åtkomsttoken som används av användarna för att ansluta direkt till kommunikations tjänsterna. När din betrodda tjänst har skapat chatt-tråden och lagt till användare som deltagare kan de använda-klient appen för att ansluta till chatt-tråden och skicka meddelanden. Använd funktionen för aviseringar i real tid, som vi diskuterar nedan, i din klient app för att prenumerera på meddelanden & tråd uppdateringar från andra deltagare.
    
        
## <a name="message-types"></a>Meddelande typer    

Som en del av meddelande historiken delar chatten användarspecifika meddelanden och meddelanden som genereras av systemet. System meddelanden genereras när en chatt-tråd uppdateras och kan hjälpa dig att identifiera när en deltagare har lagts till eller tagits bort eller när chatt-tråd ämnet uppdaterades. När du anropar `List Messages` eller `Get Messages` på en chatt-tråd kommer resultatet att innehålla båda typerna av meddelanden i kronologisk ordning.

För användardefinierade meddelanden kan meddelande typen anges i `SendMessageOptions` när ett meddelande skickas till en chatt-tråd. Om inget värde anges, kommer kommunikations tjänsterna att ange det som standard `text` . Det är viktigt att ställa in det här värdet när du skickar HTML. När `html` anges används innehållet i kommunikations tjänsterna för att säkerställa att det återges säkert på klient enheter.
 - `text`: Ett oformaterat textmeddelande som består av och skickas av en användare som en del av en chatt-tråd. 
 - `html`: Ett formaterat meddelande med HTML, sammansatt och skickat av en användare som en del av chatt-tråden. 

Typer av system meddelanden: 
 - `participantAdded`: System meddelande som anger att en eller flera deltagare har lagts till i chatt-tråden.
 - `participantRemoved`: System meddelande som anger att en deltagare har tagits bort från chatt-tråden.
 - `topicUpdated`: System meddelande som anger tråd ämnet har uppdaterats.

## <a name="real-time-notifications"></a>Meddelanden i real tid  

Vissa SDK: er (t. ex. java script Chat SDK) stöder meddelanden i real tid. Med den här funktionen kan klienter Lyssna på kommunikations tjänster för uppdateringar och inkommande meddelanden i real tid till en chatt-tråd utan att behöva avsöka API: erna. Klient programmet kan prenumerera på följande händelser:
 - `chatMessageReceived` – När ett nytt meddelande skickas till en chatt-tråd av en deltagare.
 - `chatMessageEdited` – När ett meddelande redige ras i en chatt-tråd. 
 - `chatMessageDeleted` – När ett meddelande tas bort i en chatt-tråd.   
 - `typingIndicatorReceived` – När en annan deltagare skickar en Skriv indikator till chatt-tråden.    
 - `readReceiptReceived` – När en annan deltagare skickar ett Läs kvitto för ett meddelande som de har läst.  
 - `chatThreadCreated` – När en chatt-tråd skapas av en kommunikations tjänst användare.    
 - `chatThreadDeleted` – När en chatt-tråd tas bort av en kommunikations tjänst användare.    
 - `chatThreadPropertiesUpdated` – När egenskaper för chat-trådar uppdateras. för närvarande stöds bara uppdaterings avsnittet för tråden. 
 - `participantsAdded` – När en användare läggs till som en deltagare i en chatt-tråd.     
 - `participantsRemoved` – När en befintlig deltagare tas bort från chatt-tråden.

Meddelanden i real tid kan användas för att tillhandahålla en chat-upplevelse i real tid för dina användare. Om du vill skicka push-meddelanden för meddelanden som missats av användarna medan de var borta, integreras kommunikations tjänsterna med Azure Event Grid för att publicera Chat-relaterade händelser (post åtgärd) som kan anslutas till din anpassade app Notification Service. Mer information finns i [Server händelser](https://docs.microsoft.com/azure/event-grid/event-schema-communication-services?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fcommunication-services%2Ftoc.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json).


## <a name="build-intelligent-ai-powered-chat-experiences"></a>Bygg intelligenta, AI-baserade chatt upplevelser   

Du kan använda [Azure kognitiva API: er](../../../cognitive-services/index.yml) med chatt SDK för att bygga användnings fall som:

- Gör det möjligt för användare att chatta med varandra på olika språk.  
- Hjälp en support agent att prioritera biljetter genom att identifiera ett negativt sentiment av ett inkommande meddelande från en kund. 
- Analysera inkommande meddelanden för nyckel identifiering och entitets igenkänning och fråga relevant information till användaren i din app baserat på meddelandets innehåll.

Ett sätt att uppnå detta är genom att låta din betrodda tjänst agera som deltagare i en chatt-tråd. Anta att du vill aktivera översättning av språk. Den här tjänsten ansvarar för att lyssna på meddelanden som utbyts av andra deltagare [1], anropar kognitiva API: er för att översätta innehållet till det önskade språket [2, 3] och skicka det översatta resultatet som ett meddelande i chatt-tråden [4].

På så sätt kommer meddelande historiken innehålla både original meddelanden och översatta meddelanden. I klient programmet kan du lägga till logik för att visa det ursprungliga eller översatta meddelandet. I [den här snabb](../../../cognitive-services/translator/quickstart-translator.md) starten får du veta hur du använder kognitiva API: er för att översätta text till olika språk. 
    
:::image type="content" source="../media/chat/cognitive-services.png" alt-text="Diagram som visar Cognitive Services interagera med kommunikations tjänster."::: 

## <a name="next-steps"></a>Nästa steg   

> [!div class="nextstepaction"] 
> [Kom igång med chatt](../../quickstarts/chat/get-started.md)    

Följande dokument kan vara intressanta för dig:  
- Bekanta dig med chatt- [SDK: n](sdk-features.md)
