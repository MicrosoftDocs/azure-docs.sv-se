---
title: Ge åtkomst med Azure Active Directory
description: Den här artikeln innehåller information om hur du auktoriserar åtkomst till Azure SignalR service-resurser med hjälp av Azure Active Directory.
author: terencefan
ms.author: tefa
ms.date: 08/03/2020
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: 37c2e41b5c81f941245b895ecd144baee3b9db6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97797583"
---
# <a name="authorize-access-to-azure-signalr-service-resources-using-azure-active-directory"></a>Ge åtkomst till Azure SignalR service-resurser med hjälp av Azure Active Directory
Azure SignalR service stöder användning av Azure Active Directory (Azure AD) för att auktorisera begär anden till Azure SignalR service-resurser. Med Azure AD kan du använda rollbaserad åtkomst kontroll (RBAC) för att bevilja behörighet till ett säkerhets objekt, som kan vara en användare eller ett huvud namn för en program tjänst. Mer information om roller och roll tilldelningar finns i [förstå de olika rollerna](../role-based-access-control/overview.md).

## <a name="overview"></a>Översikt
När ett säkerhets objekt (ett program) försöker få åtkomst till en Azure SignalR service-resurs måste begäran vara auktoriserad. Med Azure AD är åtkomst till en resurs en två stegs process. 

 1. Först autentiseras säkerhets objektets identitet och en OAuth 2,0-token returneras. Resurs namnet för att begära en token är `https://signalr.azure.com/` .
 2. Därefter skickas token som en del av en begäran till Azure SignalR-tjänsten för att ge åtkomst till den angivna resursen.

Autentiserings steget kräver att en programbegäran innehåller en OAuth 2,0-åtkomsttoken vid körning. Om Hub-servern körs i en Azure-entitet, till exempel en virtuell Azure-dator, en skalnings uppsättning för virtuella datorer eller en Azure Function-app, kan den använda en hanterad identitet för att få åtkomst till resurserna. Information om hur du autentiserar begär Anden som görs av en hanterad identitet i Azure SignalR service finns i [autentisera åtkomst till Azure SignalR service-resurser med Azure Active Directory och hanterade identiteter för Azure-resurser](authenticate-managed-identity.md). 

Auktoriserings steget kräver att en eller flera RBAC-roller tilldelas säkerhets objekt. Azure SignalR service tillhandahåller RBAC-roller som omfattar uppsättningar med behörigheter för Azure Signals-resurser. Rollerna som tilldelas ett säkerhets objekt bestämmer vilka behörigheter som huvud kontot ska ha. Mer information om RBAC-roller finns i [inbyggda roller i Azure för Azure SignalR-tjänsten](#azure-built-in-roles-for-azure-signalr-service). 

SignalR Hub-servern som inte körs i en Azure-entitet kan också auktorisera med Azure AD. Information om hur du begär en åtkomsttoken och använder den för att auktorisera begär Anden för Azure SignalR service-resurser finns i [autentisera åtkomst till Azure SignalR service med Azure AD från ett program](authenticate-application.md). 

## <a name="azure-built-in-roles-for-azure-signalr-service"></a>Inbyggda Azure-roller för Azure SignalR service

- [SignalR app server]
- [SignalR service Reader]
- [SignalR tjänstens ägare]

## <a name="assign-rbac-roles-for-access-rights"></a>Tilldela RBAC-roller för åtkomst rättigheter
Azure Active Directory (Azure AD) tillåter åtkomst rättigheter till skyddade resurser via [rollbaserad åtkomst kontroll (RBAC)](../role-based-access-control/overview.md). Azure SignalR service definierar en uppsättning inbyggda Azure-roller som omfattar vanliga uppsättnings behörigheter som används för att komma åt Azure SignalR-tjänsten och du kan också definiera anpassade roller för åtkomst till resursen.

När en RBAC-roll tilldelas till ett säkerhets objekt i Azure AD ger Azure åtkomst till dessa resurser för säkerhets objekt. Åtkomst kan begränsas till prenumerations nivån, resurs gruppen eller någon Azure SignalR service-resurs. Ett säkerhets objekt i Azure AD kan vara en användare, ett program eller en [hanterad identitet för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="built-in-roles-for-azure-signalr-service"></a>Inbyggda roller för Azure SignalR service
Azure tillhandahåller följande inbyggda Azure-roller för att auktorisera åtkomst till Azure SignalR service-resursen med hjälp av Azure AD och OAuth:

### <a name="signalr-app-server"></a>SignalR-app server

Använd den här rollen för att ge åtkomst för att få en tillfällig åtkomst nyckel för signering av klientens token.

### <a name="signalr-serverless-contributor"></a>SignalR Server lös deltagare

Använd den här rollen för att ge åtkomst till att hämta en klient-token från Azure SignalR-tjänsten direkt.

## <a name="next-steps"></a>Nästa steg

Se följande relaterade artiklar:

- [Autentisera ett program med Azure AD till för att få åtkomst till Azure Signaling-tjänsten](authenticate-application.md)
- [Autentisera en hanterad identitet med Azure AD för att få åtkomst till Azure SignalR service](authenticate-managed-identity.md)