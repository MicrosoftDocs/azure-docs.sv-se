---
title: Azure Service Bus autentisering och auktorisering | Microsoft Docs
description: Autentisera appar för att Service Bus med autentisering med signatur för delad åtkomst (SAS).
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 7b287b209fbcd5bc2782505095aeae4390107803
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98060222"
---
# <a name="service-bus-authentication-and-authorization"></a>Service Bus, autentisering och auktorisering
Det finns två sätt att autentisera och auktorisera åtkomst till Azure Service Bus resurser: Azure Activity Directory (Azure AD) och signaturer för delad åtkomst (SAS). Den här artikeln innehåller information om hur du använder dessa två typer av säkerhetsmekanismer. 

## <a name="azure-active-directory"></a>Azure Active Directory
Azure AD-integrering för Service Bus-resurser ger Azure-rollbaserad åtkomst kontroll (Azure RBAC) för detaljerad kontroll över en klients åtkomst till resurser. Du kan använda Azure RBAC för att bevilja behörighet till ett säkerhets objekt, som kan vara en användare, en grupp eller ett program tjänst objekt. Säkerhets objekt autentiseras av Azure AD för att returnera en OAuth 2,0-token. Token kan användas för att auktorisera en begäran om åtkomst till en Service Bus resurs (kö, ämne osv.).

Mer information om hur du autentiserar med Azure AD finns i följande artiklar:

- [Autentisera med hanterade identiteter](service-bus-managed-service-identity.md)
- [Autentisera från ett program](authenticate-application.md)

> [!NOTE]
> [Service Bus REST API](/rest/api/servicebus/) stöder OAuth-autentisering med Azure AD.

> [!IMPORTANT]
> Auktorisering av användare eller program med OAuth 2,0-token som returnerades av Azure AD ger överlägsen säkerhet och enkel användning över signaturer för delad åtkomst (SAS). Med Azure AD behöver du inte lagra tokens i din kod och potentiella säkerhets risker. Vi rekommenderar att du använder Azure AD med Azure Service Bus program när det är möjligt. 

## <a name="shared-access-signature"></a>Signatur för delad åtkomst
[SAS-autentisering](service-bus-sas.md) gör att du kan ge en användare åtkomst till Service Bus resurser med vissa rättigheter. SAS-autentisering i Service Bus inbegriper konfigurationen av en krypterings nyckel med tillhör ande rättigheter på en Service Bus resurs. Klienter kan sedan få åtkomst till den resursen genom att presentera en SAS-token som består av resurs-URI: n som används och förfallo datum signerad med den konfigurerade nyckeln.

Du kan konfigurera nycklar för SAS på en Service Bus-namnrymd. Nyckeln gäller för alla meddelande enheter inom det namn området. Du kan också konfigurera nycklar i Service Bus köer och ämnen. SAS stöds också på [Azure Relay](../azure-relay/relay-authentication-and-authorization.md).

Om du vill använda SAS kan du konfigurera ett [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) -objekt i ett namn område, en kö eller ett ämne. Den här regeln består av följande element:

* *Namn*: identifierar regeln.
* *PrimaryKey*: en kryptografisk nyckel som används för att signera/validera SAS-token.
* *SecondaryKey*: en kryptografisk nyckel som används för att signera/validera SAS-token.
* *Rättigheter*: representerar samlingen av beviljade behörigheterna **Lyssna**, **Skicka** eller **Hantera** .

Auktoriseringsregler som kon figurer ATS på namn områdes nivån kan ge åtkomst till alla entiteter i ett namn område för klienter med token som signerats med motsvarande nyckel. Du kan konfigurera upp till 12 sådana auktoriseringsregler på en Service Bus namnrymd, kö eller ämne. Som standard konfigureras en [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) med alla rättigheter för varje namnrymd när den först har tillhandahållits.

För att få åtkomst till en entitet kräver klienten en SAS-token som genereras med en speciell [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). SAS-token genereras med hjälp av HMAC-SHA256 för en resurs sträng som består av resurs-URI till vilken åtkomst begärs, och ett förfallo datum med en kryptografisk nyckel som är kopplad till auktoriseringsregeln.

Stöd för SAS-autentisering för Service Bus ingår i Azure .NET SDK-versionerna 2,0 och senare. SAS har stöd för en [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Alla API: er som accepterar en anslutnings sträng som parameter är stöd för SAS-anslutningssträngar.

> [!IMPORTANT]
> Om du använder Azure Active Directory Access Control (även kallat Access Control Service eller ACS) med Service Bus, Observera att stödet för den här metoden är begränsat och du bör [migrera ditt program till att använda SAS](service-bus-migrate-acs-sas.md) eller använda OAuth 2,0-autentisering med Azure AD (rekommenderas). Mer information om utfasning av ACS finns i [det här blogg inlägget](/archive/blogs/servicebus/upcoming-changes-to-acs-enabled-namespaces).

## <a name="next-steps"></a>Nästa steg
Mer information om hur du autentiserar med Azure AD finns i följande artiklar:

- [Autentisering med hanterade identiteter](service-bus-managed-service-identity.md)
- [Autentisering från ett program](authenticate-application.md)

Mer information om hur du autentiserar med SAS finns i följande artiklar:

- [Autentisering med SAS](service-bus-sas.md)
