---
title: Översikt över användar konton i Azure Active Directory B2C
description: Lär dig mer om vilka typer av användar konton som kan användas i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c42b79cb8b92b5f96f31eb58f091ff0ce79b84f5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101652138"
---
# <a name="overview-of-user-accounts-in-azure-active-directory-b2c"></a>Översikt över användar konton i Azure Active Directory B2C

I Azure Active Directory B2C (Azure AD B2C) finns flera typer av konton som kan skapas. Azure Active Directory, Active Directory B2B och Active Directory B2C resurs i de typer av användar konton som kan användas.

Följande typer av konton är tillgängliga:

- **Arbets konto** – ett arbets konto kan komma åt resurser i en klient, och med en administratörs roll kan de hantera klienter.
- **Gäst konto** – ett gäst konto kan bara vara en Microsoft-konto eller en Azure Active Directory användare som kan användas för att få åtkomst till program eller hantera klienter.
- **Konsument konto** – ett konsument konto används av en användare av de program som du har registrerat med Azure AD B2C. Konsument konton kan skapas av:
  - Användaren går igenom ett registrerings användar flöde i ett Azure AD B2C program
  - Använda Microsoft Graph API
  - Använda Azure Portal

## <a name="work-account"></a>Arbets konto

Ett arbets konto skapas på samma sätt för alla klienter som baseras på Azure AD. Om du vill skapa ett arbets konto kan du använda informationen i [snabb start: Lägg till nya användare i Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md). Ett arbets konto skapas med hjälp av det **nya användar** alternativet i Azure Portal.

När du lägger till ett nytt arbets konto måste du tänka på följande konfigurations inställningar:

- **Namn** och **användar namn** – egenskapen **Name** innehåller användarens tilldelade och efter namn. **Användar namnet** är det ID som användaren anger för att logga in. Användar namnet innehåller den fullständiga domänen. Domän namns delen av användar namnet måste antingen vara det initiala standard domän namnet *your-domain.onmicrosoft.com*, eller ett verifierat, icke-federerat [anpassat domän](../active-directory/fundamentals/add-custom-domain.md) namn, till exempel *contoso.com*.
- **Profil** -kontot har kon figurer ATS med en profil med användar data. Du har möjlighet att ange förnamn, efter namn, befattning och avdelnings namn. Du kan redigera profilen när kontot har skapats.
- **Grupper** – Använd grupper för att utföra hanterings uppgifter, till exempel tilldela licenser eller behörigheter till många användare eller enheter på samma gång. Du kan ställa in det nya kontot i en befintlig [grupp](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) i din klient organisation.
- **Katalog roll** – du måste ange den åtkomst nivå som användar kontot måste ha för resurser i din klient organisation. Följande behörighets nivåer är tillgängliga:

    - **Användare** – användare har åtkomst till tilldelade resurser, men kan inte hantera de flesta klient resurser.
    - **Global administratör** – globala administratörer har fullständig kontroll över alla klient resurser.
    - **Begränsad administratör** – Välj den administrativa rollen eller rollerna för användaren. Mer information om vilka roller som kan väljas finns [i tilldela administratörs roller i Azure Active Directory](../active-directory/roles/permissions-reference.md).

### <a name="create-a-work-account"></a>Skapa ett arbets konto

Du kan använda följande information för att skapa ett nytt arbets konto:

- [Azure-portalen](../active-directory/fundamentals/add-users-azure-active-directory.md)
- [Microsoft Graph](/graph/api/user-post-users)

### <a name="update-a-user-profile"></a>Uppdatera en användar profil

Du kan använda följande information för att uppdatera profilen för en användare:

- [Azure-portalen](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
- [Microsoft Graph](/graph/api/user-update)

### <a name="reset-a-password-for-a-user"></a>Återställa ett lösen ord för en användare

Du kan använda följande information för att återställa lösen ordet för en användare:

- [Azure-portalen](../active-directory/fundamentals/active-directory-users-reset-password-azure-portal.md)
- [Microsoft Graph](/graph/api/user-update)

## <a name="guest-user"></a>Gäst användare

Du kan bjuda in externa användare till din klient som gäst användare. Ett typiskt scenario för att bjuda in en gäst användare till din Azure AD B2C klient organisation är att dela administrations ansvar. Ett exempel på hur du använder ett gäst konto finns i [Egenskaper för en Azure Active Directory B2B-samarbets användare](../active-directory/external-identities/user-properties.md).

När du bjuder in en gäst användare till din klient anger du e-postadressen till mottagaren och ett meddelande som beskriver inbjudan. Länken inbjudan tar användaren till sidan medgivande. Om en inkorg inte är kopplad till e-postadressen kan användaren gå till sidan medgivande genom att gå till en Microsoft-sida med de inbjudna autentiseringsuppgifterna. Användaren tvingas sedan att lösa in inbjudan på samma sätt som om du klickar på länken i e-postmeddelandet. Exempel: `https://myapps.microsoft.com/B2CTENANTNAME`.

Du kan också använda [Microsoft Graph API](/graph/api/invitation-post?view=graph-rest-beta) för att bjuda in en gäst användare.

## <a name="consumer-user"></a>Konsument användare

Konsument användaren kan logga in på program som skyddas av Azure AD B2C, men kan inte komma åt Azure-resurser som Azure Portal. Konsument användaren kan använda ett lokalt konto eller federerade konton, till exempel Facebook eller Twitter. Ett konsument konto skapas med hjälp av ett [registrerings-eller inloggnings användar flöde](user-flow-overview.md)med hjälp av Microsoft Graph-API eller med hjälp av Azure Portal.

Du kan ange de data som samlas in när ett konsument användar konto skapas. Mer information finns i [lägga till användarattribut och anpassa användarindata](configure-user-input.md).

Mer information om hur du hanterar konsument konton finns i [hantera Azure AD B2C användar konton med Microsoft Graph](./microsoft-graph-operations.md).

### <a name="migrate-consumer-user-accounts"></a>Migrera konsument användar konton

Du kanske behöver migrera befintliga konsument användar konton från valfri identitetsprovider till Azure AD B2C. Mer information finns i [Migrera användare till Azure AD B2C](user-migration.md).