---
title: Azure Relay autentisering och auktorisering | Microsoft Docs
description: Den här artikeln innehåller en översikt över autentisering med signatur för delad åtkomst (SAS) med Azure Relay-tjänsten.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 4b0a5c7a092155a006419eedd170a63abed42bb3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87033385"
---
# <a name="azure-relay-authentication-and-authorization"></a>Azure Relay autentisering och auktorisering

Program kan autentiseras för Azure Relay att använda autentisering med signatur för delad åtkomst (SAS). SAS-autentisering gör det möjligt för program att autentisera till Azure Relay tjänsten med hjälp av en åtkomst nyckel som kon figurer ATS i relä namn området. Du kan sedan använda den här nyckeln för att generera en token för signatur för delad åtkomst som klienter kan använda för att autentisera till relä tjänsten.

## <a name="shared-access-signature-authentication"></a>Autentisering av signatur för delad åtkomst

[SAS-autentisering](../service-bus-messaging/service-bus-sas.md) gör att du kan ge en användare åtkomst till Azure Relay resurser med vissa rättigheter. SAS-autentisering innebär konfiguration av en kryptografisk nyckel med tillhör ande behörigheter på en resurs. Klienter kan sedan få åtkomst till den resursen genom att presentera en SAS-token som består av resurs-URI: n som används och förfallo datum signerad med den konfigurerade nyckeln.

Du kan konfigurera nycklar för SAS i ett relä namn område. Till skillnad från Service Bus meddelanden stöder [relä hybridanslutningar](relay-hybrid-connections-protocol.md) obehöriga eller anonyma avsändare. Du kan aktivera anonym åtkomst för entiteten när du skapar den, som du ser i följande skärm bild från portalen:

![En dialog ruta med namnet "skapa hybrid anslutning" har en text ruta med namnet "namn" och en kryss ruta med etiketten "kräver klientautentisering", som är markerad.][0]

Om du vill använda SAS kan du konfigurera ett [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) -objekt i ett relä namn område som består av följande:

* *Namn* som identifierar regeln.
* *PrimaryKey* är en kryptografisk nyckel som används för att signera/validera SAS-token.
* *SecondaryKey* är en kryptografisk nyckel som används för att signera/validera SAS-token.
* *Rättigheter* som representerar samlingen av beviljade behörigheterna Lyssna, skicka eller hantera.

Auktoriseringsregler som kon figurer ATS på namn områdes nivå kan bevilja åtkomst till alla relä anslutningar i ett namn område för klienter med token som signerats med motsvarande nyckel. Upp till 12 sådana auktoriseringsregler kan konfigureras i ett relä namn område. Som standard konfigureras en [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) med alla rättigheter för varje namnrymd när den först har tillhandahållits.

För att få åtkomst till en entitet kräver klienten en SAS-token som genereras med en speciell [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). SAS-token genereras med hjälp av HMAC-SHA256 för en resurs sträng som består av resurs-URI till vilken åtkomst begärs, och ett förfallo datum med en kryptografisk nyckel som är kopplad till auktoriseringsregeln.

Stöd för SAS-autentisering för Azure Relay ingår i Azure .NET SDK-versionerna 2,0 och senare. SAS har stöd för en [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Alla API: er som accepterar en anslutnings sträng som parameter är stöd för SAS-anslutningssträngar.

## <a name="next-steps"></a>Nästa steg

- Fortsätt att läsa [Service Bus autentisering med signaturer för delad åtkomst](../service-bus-messaging/service-bus-sas.md) för mer information om SAS.
- Mer information om Hybridanslutningar-funktionen finns i [Azure Relay Hybridanslutningars protokoll hand bok](relay-hybrid-connections-protocol.md) .
- För motsvarande information om Service Bus autentisering och auktorisering av meddelanden, se [Service Bus autentisering och auktorisering](../service-bus-messaging/service-bus-authentication-and-authorization.md). 

[0]: ./media/relay-authentication-and-authorization/hcanon.png