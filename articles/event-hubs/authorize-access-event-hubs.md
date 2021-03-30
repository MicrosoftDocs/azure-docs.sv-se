---
title: Ge åtkomst till Azure Event Hubs
description: Den här artikeln innehåller information om olika alternativ för att auktorisera åtkomst till Azure Event Hubs-resurser.
ms.topic: conceptual
ms.date: 06/23/2020
ms.author: spelluru
ms.openlocfilehash: 5fc35387f4ac28ad4dd28bea0013bcdf1e1e9f02
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92332390"
---
# <a name="authorize-access-to-azure-event-hubs"></a>Ge åtkomst till Azure Event Hubs
Varje gång du publicerar eller använder händelser/data från en händelsehubben försöker klienten få åtkomst till Event Hubs resurser. Varje begäran till en säker resurs måste auktoriseras så att tjänsten kan se till att klienten har de behörigheter som krävs för att publicera/använda data. 

Azure Event Hubs erbjuder följande alternativ för att auktorisera åtkomst till säkra resurser:

- Azure Active Directory
- Signatur för delad åtkomst

> [!NOTE]
> Den här artikeln gäller för både Event Hubs-och [Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md) scenarier. 

## <a name="azure-active-directory"></a>Azure Active Directory
Azure Active Directory (Azure AD)-integration för Event Hubs resurser ger Azure-rollbaserad åtkomst kontroll (Azure RBAC) för detaljerad kontroll över en klients åtkomst till resurser. Du kan använda Azure RBAC för att bevilja behörighet till säkerhets objekt, som kan vara en användare, en grupp eller ett huvud namn för program tjänsten. Säkerhets objekt autentiseras av Azure AD för att returnera en OAuth 2,0-token. Token kan användas för att auktorisera en begäran om åtkomst till en Event Hubs-resurs.

Mer information om hur du autentiserar med Azure AD finns i följande artiklar:

- [Autentisera begär anden till Azure-Event Hubs med Azure Active Directory](authenticate-application.md)
- [Ge åtkomst till Event Hubs resurser med Azure Active Directory](authorize-access-azure-active-directory.md).

## <a name="shared-access-signatures"></a>Signaturer för delad åtkomst 
Signaturer för delad åtkomst (SAS) för Event Hubs resurser ger begränsad delegerad åtkomst till Event Hubs resurser. Att lägga till begränsningar i tidsintervall för vilka signaturen är giltig eller för behörigheter som den ger ger flexibilitet vid hantering av resurser. Mer information finns i [autentisera med signaturer för delad åtkomst (SAS)](authenticate-shared-access-signature.md). 

Auktorisering av användare eller program med hjälp av en OAuth 2,0-token som returnerades av Azure AD ger överlägsen säkerhet och enkel användning över signaturer för delad åtkomst (SAS). Med Azure AD behöver du inte lagra åtkomsttoken med din kod och potentiella säkerhets risker. Även om du kan fortsätta att använda signaturer för delad åtkomst (SAS) för att ge detaljerade åtkomst till Event Hubs-resurser, erbjuder Azure AD liknande funktioner utan att behöva hantera SAS-tokens eller bekymra dig om att återkalla en komprometterad SAS. 

Som standard är alla Event Hubs resurser säkra och är bara tillgängliga för kontots ägare. Även om du kan använda någon av de olika behörighets strategierna som beskrivs ovan för att ge klienterna åtkomst till Event Hub-resurser. Microsoft rekommenderar att du använder Azure AD när det är möjligt för maximal säkerhet och enkel användning.

Mer information om auktorisering med SAS finns i [auktorisera åtkomst till Event Hubs resurser med hjälp av signaturer för delad åtkomst](authorize-access-shared-access-signature.md).

## <a name="next-steps"></a>Nästa steg
- Granska [Azure RBAC-exempel](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac) som publicerats i vår GitHub-lagringsplats. 
- Se följande artiklar:
    - [Autentisera begär anden till Azure Event Hubs från ett program med Azure Active Directory](authenticate-application.md)
    - [Autentisera en hanterad identitet med Azure Active Directory för att få åtkomst till Event Hubs resurser](authenticate-managed-identity.md)
    - [Autentisera begär anden till Azure Event Hubs med signaturer för delad åtkomst](authenticate-shared-access-signature.md)
    - [Ge åtkomst till Event Hubs resurser med Azure Active Directory](authorize-access-azure-active-directory.md)
    - [Ge åtkomst till Event Hubs resurser med signaturer för delad åtkomst](authorize-access-shared-access-signature.md)

