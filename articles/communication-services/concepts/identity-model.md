---
title: Identitetsmodell
titleSuffix: An Azure Communication Services concept
description: Lär dig mer om identiteter och åtkomsttoken
author: tomaschladek
manager: nmurav
services: azure-communication-services
ms.author: tchladek
ms.date: 10/26/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 254d35331459e70ad56bcef43569f51ff6f50a93
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101656218"
---
# <a name="identity-model"></a>Identitetsmodell

Azure Communication Services är en oberoende tjänst. Den här designen ger flera fördelar:

- Återanvänd befintliga identiteter från ditt identitets hanterings system
- Ger flexibilitet för integrerings scenarier
- Håller dina identiteter privata i Azure Communication Services

I stället för att duplicera information i systemet, kommer du att underhålla mappnings relationen som affärs ärendet kräver. Du kan till exempel mappa identiteter 1:1, 1: N, N:1, N:M. Externa identifierare som telefonnummer, användare, enheter, program och GUID kan inte användas för identitet i Azure Communication Services. Åtkomsttoken som skapas för en Azure Communication Services-identitet används för att få åtkomst till primitiver som chatt eller samtal.

## <a name="identity"></a>Identitet

Du kan skapa identiteter med hjälp av identitets biblioteket för Azure Communication Services. En identitet fungerar som en identifierare i konversationer. Den används för att skapa åtkomsttoken. Samma identitet kan ingå i flera samtidiga sessioner över flera enheter. En identitet kan ha flera aktiva åtkomsttoken på samma gång.

Borttagningen av en identitet, resurs eller prenumeration invaliderar alla åtkomsttoken. Den här åtgärden tar också bort alla data som lagras för identiteten. En borttagen identitet kan inte skapa nya åtkomsttoken eller komma åt tidigare lagrade data (till exempel chat-meddelanden).

Du debiteras inte för antalet identiteter som du har. I stället debiteras du för användning av primitiver. Antalet identiteter behöver inte begränsa hur du mappar appens identiteter till Azure Communication Services-identiteter.

Med en kopplings frihet får du sekretess ansvar. Om en användare vill tas bort från systemet måste du ta bort alla identiteter som är associerade med den användaren.

Azure Communication Services tillhandahåller inte särskilda identiteter för anonyma användare. Den behåller inte mappningen mellan användare och identiteter och kan inte avgöra om en identitet är anonym. Du kan utforma identitets begreppet så att det passar dina behov. Vi rekommenderar att du skapar en ny identitet för varje anonym användare i varje program.

Alla som har en giltig åtkomsttoken kan komma åt aktuellt identitets innehåll. Användarna kan till exempel komma åt chatt meddelanden som skickas. Åtkomsten är begränsad enbart till omfattningar som ingår i åtkomsttoken. Mer information [finns i avsnittet om åtkomsttoken](#access-tokens) i den här artikeln.

### <a name="identity-mapping"></a>Identitets mappning

Azure Communication Services replikerar inte funktionerna i Azure Identity Management-systemet. Det ger inte kunderna möjlighet att använda kundspecifika identiteter. Kunder kan till exempel inte använda ett telefonnummer eller en e-postadress. Azure Communication Services innehåller i stället unika identifierare. Du kan tilldela dessa unika identifierare till programmets identiteter. Azure Communication Services lagrar inte någon typ av information som kan avslöja användarnas verkliga identitet.

Du kan undvika att duplicera information i systemet genom att planera hur du mappar användare från din identitets domän till Azure Communication Services-identiteter. Du kan följa alla typer av mönster. Du kan till exempel använda 1:1, 1: N, N:1 eller M:N. Bestäm om en enskild användare mappas till en enda identitet eller till flera identiteter.

När en ny identitet skapas lagrar du dess mappning till programmets användare eller användare. Eftersom identiteter kräver åtkomst-token för att använda primitiver måste identiteten vara känd för programmets användare eller användare.

Om du använder en Relations databas för att lagra användar information kan du justera designen utifrån ditt mappnings scenario. För scenarier som mappar 1:1 eller N:1 kanske du vill lägga till en `CommunicationServicesId` kolumn i tabellen för att lagra din Azure Communication Services-identitet. I scenarier där relationen 1: N eller N:M används kan du överväga att skapa en separat tabell i Relations databasen.

## <a name="access-tokens"></a>Åtkomsttokens

En åtkomsttoken är en JSON Web Token (JWT) som kan användas för att få åtkomst till Azure Communication Service-primitiver. En åtkomsttoken som utfärdas har integritets skydd. Det innebär att dess anspråk inte kan ändras efter att den har utfärdats. Så en manuell ändring av egenskaper som identitet, förfallo datum eller omfång kommer att ogiltig förklara åtkomsttoken. Om primitiver används med ogiltiga token kommer åtkomsten att nekas till de primitiva.

Egenskaperna för en åtkomsttoken är:
* Autentiseringsidentitet.
* Dag.
* Omfattningar.

En åtkomsttoken är alltid giltig i 24 timmar. När den har gått ut är åtkomsttoken ogiltig och kan inte användas för att få åtkomst till någon primitiv.

En identitet behöver ett sätt att begära en ny åtkomsttoken från en server-side-tjänst. *Omfattnings* parametern definierar en uppsättning primitiver som kan användas. Azure Communication Services stöder följande omfattningar för åtkomsttoken.

|Namn|Beskrivning|
|---|---|
|Chatt|  Ger möjlighet att delta i en chatt|
|VoIP|  Ger möjlighet att anropa identiteter och telefonnummer|


Om du vill återkalla en åtkomsttoken innan förfallo tiden använder du Azure Communication Services-identitets biblioteket. Åter kallelse av token är inte omedelbart. Det tar upp till 15 minuter att sprida. Borttagning av en identitet, resurs eller prenumeration återkallar alla åtkomsttoken.

Om du vill ta bort en användares möjlighet att få åtkomst till vissa funktioner kan du återkalla alla åtkomsttoken. Utfärda sedan en ny åtkomsttoken som har en mer begränsad uppsättning omfång.

I Azure Communication Services återkallar sig en rotation av åtkomst nycklar alla aktiva åtkomsttoken som skapats med hjälp av en tidigare åtkomst nyckel. Alla identiteter förlorar åtkomst till Azure Communication Services och de måste utfärda nya åtkomsttoken.

Vi rekommenderar att du utfärdar åtkomsttoken på Server sidan och inte i klient programmet. Anledningen är att det krävs en åtkomst nyckel eller en hanterad identitet för att utfärda. Av säkerhets skäl rekommenderas inte delning av åtkomst nycklar med klient programmet.

Klient programmet bör använda en betrodd tjänst slut punkt som kan autentisera dina klienter. Slut punkten ska utfärda åtkomsttoken för deras räkning. Mer information finns i [klient-och server arkitektur](./client-and-server-architecture.md).

Om du cachelagrar åtkomsttoken till en lagrings plats rekommenderar vi att du använder kryptering. En åtkomsttoken är känsliga data. Den kan användas för skadlig aktivitet om den inte är skyddad. Någon som har en åtkomsttoken kan starta SDK och få åtkomst till API: et. Det tillgängliga API: t är endast begränsat baserat på de omfattningar som åtkomsttoken har. Vi rekommenderar att du utfärdar åtkomsttoken som bara har de nödvändiga omfattningarna.

## <a name="next-steps"></a>Nästa steg

* En introduktion till hantering av åtkomst till token finns i [skapa och hantera åtkomsttoken](../quickstarts/access-tokens.md).
* En introduktion till autentisering finns i [autentisera till Azure Communication Services](./authentication.md).
* En introduktion till data placering och sekretess finns i [region tillgänglighet och data placering](./privacy.md).