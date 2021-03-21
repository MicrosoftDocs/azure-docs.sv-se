---
title: Ge åtkomst till Azure App konfiguration med Azure Active Directory
description: Aktivera Azure RBAC för att ge åtkomst till din Azure App konfigurations instans
author: AlexandraKemperMS
ms.author: alkemper
ms.date: 05/26/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: f29be1807dfcc314c89d30301107670a970263ce
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102172883"
---
# <a name="authorize-access-to-azure-app-configuration-using-azure-active-directory"></a>Ge åtkomst till Azure App konfiguration med Azure Active Directory
Förutom att använda hash-baserade Message Authentication Code (HMAC), kan Azure App konfiguration använda Azure Active Directory (Azure AD) för att auktorisera begär anden till konfigurations instanser för appar.  Med Azure AD kan du använda rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att ge behörighet till ett säkerhets objekt.  Ett säkerhets objekt kan vara en användare, en [hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md) eller ett [huvud namn för program tjänsten](../active-directory/develop/app-objects-and-service-principals.md).  Mer information om roller och roll tilldelningar finns i [förstå olika roller](../role-based-access-control/overview.md).

## <a name="overview"></a>Översikt
Begär Anden som görs av ett säkerhets objekt för att få åtkomst till en app Configuration-resurs måste vara auktoriserade. Med Azure AD är åtkomst till en resurs en två stegs process:
1. Säkerhets objektets identitet autentiseras och en OAuth 2,0-token returneras.  Resurs namnet för att begära en token är det `https://login.microsoftonline.com/{tenantID}` som `{tenantID}` matchar Azure Active Directory klient-ID som tjänstens huvud namn tillhör.
2. Token skickas som en del av en begäran till appens konfigurations tjänst för att ge åtkomst till den angivna resursen.

Autentiserings steget kräver att en programbegäran innehåller en OAuth 2,0-åtkomsttoken vid körning.  Om ett program körs i en Azure-entitet, till exempel en Azure Functions app, en Azure-webbapp eller en virtuell Azure-dator, kan den använda en hanterad identitet för att få åtkomst till resurserna.  Information om hur du autentiserar begär Anden som görs av en hanterad identitet för att Azure App konfiguration finns i [autentisera åtkomst till Azure App konfigurations resurser med Azure Active Directory och hanterade identiteter för Azure-resurser](howto-integrate-azure-managed-service-identity.md).

Auktoriserings steget kräver att en eller flera Azure-roller tilldelas säkerhets objekt. Azure App-konfigurationen tillhandahåller Azure-roller som omfattar uppsättningar med behörigheter för konfigurations resurser för appar. Rollerna som tilldelas ett säkerhets objekt avgör vilka behörigheter som har angetts för huvud kontot. Mer information om Azure-roller finns i [inbyggda roller i Azure för Azure App konfiguration](#azure-built-in-roles-for-azure-app-configuration). 

## <a name="assign-azure-roles-for-access-rights"></a>Tilldela Azure-roller för åtkomst rättigheter
Azure Active Directory (Azure AD) tillåter åtkomst rättigheter till skyddade resurser via [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../role-based-access-control/overview.md).

När en Azure-roll tilldelas till ett säkerhets objekt för Azure AD ger Azure åtkomst till dessa resurser för säkerhets objekt. Åtkomsten är begränsad till appens konfigurations resurs. Ett säkerhets objekt i Azure AD kan vara en användare eller ett huvud namn för program tjänsten eller en [hanterad identitet för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="azure-built-in-roles-for-azure-app-configuration"></a>Inbyggda Azure-roller för Azure App konfiguration
Azure tillhandahåller följande inbyggda Azure-roller för att auktorisera åtkomst till konfigurations data för appar med hjälp av Azure AD och OAuth:

- **Data ägare för app Configuration**: Använd den här rollen för att ge Läs-/skriv-/borttagnings åtkomst till konfigurations data för appar. Detta ger inte åtkomst till appens konfigurations resurs.
- **Konfigurations data läsare för appar**: Använd den här rollen för att ge Läs åtkomst till konfigurations data för appar. Detta ger inte åtkomst till appens konfigurations resurs.
- **Deltagare**: Använd den här rollen för att hantera appens konfigurations resurs. Även om konfigurations data för appar kan nås med hjälp av åtkomst nycklar ger den här rollen ingen direkt åtkomst till data med hjälp av Azure AD.
- **Läsare**: Använd den här rollen för att ge Läs behörighet till appens konfigurations resurs. Detta ger inte åtkomst till resursens åtkomst nycklar eller data som är lagrade i app-konfigurationen.

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du använder [hanterade identiteter](howto-integrate-azure-managed-service-identity.md) för att administrera konfigurations tjänsten för appar.
