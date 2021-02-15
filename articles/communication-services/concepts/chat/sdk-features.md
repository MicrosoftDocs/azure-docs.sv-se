---
title: Översikt över Chat-klient bibliotek för Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Lär dig mer om klient biblioteket Azure Communication Services chat.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 93f90520f9a5f6ec424a7558418abfa4de4699ee
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100364842"
---
# <a name="chat-client-library-overview"></a>Översikt över klientbiblioteket för chatt

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Azure Communication Services Chat-klient bibliotek kan användas för att lägga till omfattande chatt i real tid till dina program.

## <a name="chat-client-library-capabilities"></a>Funktioner i chatt-klient bibliotek

I följande lista visas en uppsättning funktioner som för närvarande är tillgängliga i kommunikations tjänstens klient bibliotek för kommunikations tjänster.

| Grupp med funktioner | Funktion                                                                                                          | JS  | Java | .NET | Python |
| ----------------- | ------------------------------------------------------------------------------------------------------------------- | --- | ----- | ---- | -----  |
| Kärn funktioner | Skapa en chatt-tråd mellan två eller flera användare (upp till 250 användare)                                                       | ✔️   | ✔️  | ✔️    | ✔️   |
|                   | Uppdatera ämnet i en chatt-tråd                                                                              | ✔️   | ✔️ | ✔️    | ✔️   |
|                   | Lägga till eller ta bort medlemmar från en chatt-tråd                                                                           | ✔️   | ✔️  | ✔️    | ✔️  |
|                   | Välj om du vill dela chatt meddelande historik med nyligen tillagda medlemmar – *alla/inga/upp till en viss tid* | ✔️   | ✔️   | ✔️    | ✔️  |
|                   | Hämta en lista över alla chatt-medlemmars trådar                                                                          | ✔️   | ✔️  | ✔️ | ✔️ |
|                   | Ta bort en chatt-tråd                                                                                              | ✔️   | ✔️  | ✔️    | ✔️  |
|                   | Hämta en lista över en användares tråd medlemskap i chatten                                                                  | ✔️   | ✔️  | ✔️    | ✔️  |
|                   | Hämta information om en viss chatt-tråd                                                                              | ✔️   | ✔️  | ✔️ | ✔️ |
|                   | Skicka och ta emot meddelanden i en chatt-tråd                                                                            | ✔️   | ✔️   | ✔️    | ✔️  |
|                   | Redigera innehållet i ett meddelande efter att det har skickats                                                                   | ✔️   | ✔️  | ✔️ | ✔️ |
|                   | Ta bort ett meddelande                                                                                                       | ✔️   | ✔️  | ✔️ | ✔️ |
|                   | Tagga ett meddelande med prioritet som normal eller hög vid tidpunkten för sändning                                               | ✔️   | ✔️  | ✔️    | ✔️   |
|                   | Skicka och ta emot Läs kvitton för meddelanden som har lästs av medlemmar <br/> *Inte tillgängligt när det finns fler än 20 medlemmar i en chatt-tråd*    | ✔️   | ✔️  | ✔️    | ✔️   |
|                   | Skicka och ta emot Skriv meddelanden när en medlem aktivt skriver ett meddelande i en chatt-tråd <br/> *Inte tillgängligt när det finns fler än 20 medlemmar i en chatt-tråd*      | ✔️   | ✔️   | ✔️    | ✔️    |
|                   | Hämta alla meddelanden i en chatt-tråd <br/> *Unicode-emojis stöds*                                                  | ✔️   | ✔️  | ✔️    | ✔️  |
|                   | Skicka emojis som en del av meddelande innehållet                                                                              | ✔️   | ✔️  | ✔️    | ✔️  |
|Signalering i real tid (aktiverat av patentskyddat signal paket * *)| Få ett meddelande när en användare får ett nytt meddelande i en chatt-tråd som de är medlem i                                     | ✔️   | ❌    | ❌  | ❌  |
|                    | Få ett meddelande när ett meddelande har redigerats av en annan medlem i en chatt-tråd som de är medlem i                | ✔️   | ❌    | ❌    | ❌  |
|                    | Få ett meddelande när ett meddelande har tagits bort av en annan medlem i en chatt-tråd som de är medlem i                | ✔️   | ❌    | ❌    | ❌  |
|                    | Få ett meddelande när en annan Chat-tråd medlem skriver                                                             | ✔️   | ❌    | ❌    | ❌  |
|                    | Få ett meddelande när en annan medlem har läst ett meddelande (Läs kvitto) i Chat-tråden                               | ✔️   | ❌    | ❌    | ❌  |
| Händelser             | Använd Event Grid för att prenumerera på användar aktivitet i Chat-trådar och integrera anpassade meddelande tjänster eller affärs logik     | ✔️   | ✔️  | ✔️    | ✔️  |
| Övervakning        | Övervaka användning avseende meddelanden som skickas                                                                               | ✔️   | ✔️  | ✔️    | ✔️  |
|                    | Övervaka kvalitet och status för API-begäranden som görs av din app och konfigurera aviseringar via portalen                                                          | ✔️   | ✔️  | ✔️    | ✔️  |
|Ytterligare funktioner | Använd [API:er för Cognitive Services](../../../cognitive-services/index.yml) tillsammans med klient biblioteket i Chat för att aktivera intelligenta funktioner – *språk översättning & sentiment analys av det inkommande meddelandet på en klient, tal till text konvertering för att skapa ett meddelande när medlemmen pratar, osv.*                                                                                         | ✔️   | ✔️  | ✔️    | ✔️  |

* * Det tillverkarspecifika signal paketet implementeras med hjälp av Web Sockets. Det kommer att återgå till lång avsökning om Web Sockets inte stöds.

## <a name="javascript-chat-client-library-support-by-os-and-browser"></a>Java Script Chat-klient bibliotek stöd av OS och webbläsare

Följande tabell visar en uppsättning webbläsare och versioner som stöds och som är tillgängliga för tillfället.

|                                  | Windows          | macOS          | Ubuntu | Linux  | Android | iOS    | iPad OS|
| -------------------------------- | ---------------- | -------------- | ------- | ------ | ------ | ------ | -------|
| **Klient bibliotek för chat** | Firefox *, Chrome*, ny kant | Firefox *, Chrome*, Safari * | Chrome  | Chrome | Chrome | Safari | Safari |


* Observera att den senaste versionen stöds utöver de tidigare två versionerna.<br/>

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kom igång med chatt](../../quickstarts/chat/get-started.md)

Följande dokument kan vara intressanta för dig:

- Bekanta dig med [chatt-koncept](../chat/concepts.md)
