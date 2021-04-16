---
title: Azure Service Bus autentisering och | Microsoft Docs
description: Autentisera appar för Service Bus med SAS-autentisering (signatur för delad åtkomst).
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: ccb526abd99be50e33c8adb918186944b7af3bd6
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516662"
---
# <a name="service-bus-authentication-and-authorization"></a>Service Bus, autentisering och auktorisering
Det finns två sätt att autentisera och auktorisera åtkomst Azure Service Bus resurser: Azure Activity Directory (Azure AD) och Signaturer för delad åtkomst (SAS). Den här artikeln innehåller information om hur du använder dessa två typer av säkerhetsmekanismer. 

## <a name="azure-active-directory"></a>Azure Active Directory
Azure AD-integrering för Service Bus-resurser ger Azure rollbaserad åtkomstkontroll (RBAC) för mer information om en klients åtkomst till resurser. Du kan använda Azure RBAC för att bevilja behörigheter till ett säkerhetsobjekt, som kan vara en användare, en grupp eller ett huvudnamn för programtjänsten. Säkerhetsobjekt autentiseras av Azure AD för att returnera en OAuth 2.0-token. Token kan användas för att auktorisera en begäran om åtkomst Service Bus en resurs (kö, ämne och så vidare).

Mer information om autentisering med Azure AD finns i följande artiklar:

- [Autentisera med hanterade identiteter](service-bus-managed-service-identity.md)
- [Autentisera från ett program](authenticate-application.md)

> [!NOTE]
> [Service Bus REST API](/rest/api/servicebus/) stöder OAuth-autentisering med Azure AD.

> [!IMPORTANT]
> Auktorisering av användare eller program med OAuth 2.0-token som returneras av Azure AD ger överlägsen säkerhet och användarvänlighet över signaturer för delad åtkomst (SAS). Med Azure AD behöver du inte lagra token i din kod och riskera potentiella säkerhetsrisker. Vi rekommenderar att du använder Azure AD med dina Azure Service Bus program när det är möjligt. 

## <a name="shared-access-signature"></a>Signatur för delad åtkomst
[Med SAS-autentisering](service-bus-sas.md) kan du ge en användare åtkomst till Service Bus resurser med specifika rättigheter. SAS-autentisering i Service Bus omfattar konfiguration av en kryptografisk nyckel med associerade rättigheter för en Service Bus resurs. Klienter kan sedan få åtkomst till resursen genom att presentera en SAS-token, som består av resurs-URI som används och ett utgångsdatum signerat med den konfigurerade nyckeln.

Du kan konfigurera nycklar för SAS på en Service Bus namnområde. Nyckeln gäller för alla meddelandeentiteter inom det namnområdet. Du kan också konfigurera nycklar i Service Bus köer och ämnen. SAS stöds också på [Azure Relay](../azure-relay/relay-authentication-and-authorization.md).

Om du vill använda SAS kan du konfigurera en auktoriseringsregel för delad åtkomst för ett namnområde, en kö eller ett ämne. Den här regeln består av följande element:

* *KeyName:* identifierar regeln.
* *PrimaryKey:* en kryptografisk nyckel som används för att signera/verifiera SAS-token.
* *SecondaryKey:* en kryptografisk nyckel som används för att signera/verifiera SAS-token.
* *Rättigheter:* representerar samlingen med **behörigheterna Lyssna,** **Skicka** **eller** Hantera beviljad.

Auktoriseringsregler som konfigureras på namnområdesnivå kan bevilja åtkomst till alla entiteter i ett namnområde för klienter med token signerade med motsvarande nyckel. Du kan konfigurera upp till 12 sådana auktoriseringsregler på Service Bus namnområde, kö eller ämne. Som standard konfigureras en auktoriseringsregel för delad åtkomst med alla rättigheter för varje namnområde när den först etableras.

För att få åtkomst till en entitet kräver klienten en SAS-token som genereras med hjälp av en specifik auktoriseringsregel för delad åtkomst. SAS-token genereras med hjälp av HMAC-SHA256 för en resurssträng som består av den resurs-URI som åtkomsten begärts till och ett utgångsdatum med en kryptografisk nyckel som är associerad med auktoriseringsregeln.

SAS-autentiseringsstöd för Service Bus ingår i Azure .NET SDK version 2.0 och senare. SAS har stöd för en auktoriseringsregel för delad åtkomst. Alla API:er som accepterar en anslutningssträng som en parameter har stöd för SAS-anslutningssträngar.

> [!IMPORTANT]
> Om du använder Azure Active Directory Access Control (även kallat Access Control Service eller ACS) med Service Bus bör du observera att stödet för den här metoden nu är begränsat och att du bör migrera ditt program för att använda [SAS](service-bus-migrate-acs-sas.md) eller använda OAuth 2.0-autentisering med Azure AD (rekommenderas). Mer information om utfasning av ACS finns i [det här blogginlägget.](/archive/blogs/servicebus/upcoming-changes-to-acs-enabled-namespaces)

## <a name="next-steps"></a>Nästa steg
Mer information om autentisering med Azure AD finns i följande artiklar:

- [Autentisering med hanterade identiteter](service-bus-managed-service-identity.md)
- [Autentisering från ett program](authenticate-application.md)

Mer information om autentisering med SAS finns i följande artiklar:

- [Autentisering med SAS](service-bus-sas.md)
