---
title: Översikt över användarkonton i Azure Active Directory B2C
description: Lär dig mer om vilka typer av användarkonton som kan användas i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 4b35cfeded13a50e5e27c240b0826f1d108ff7eb
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529439"
---
# <a name="overview-of-user-accounts-in-azure-active-directory-b2c"></a>Översikt över användarkonton i Azure Active Directory B2C

I Azure Active Directory B2C (Azure AD B2C) finns det flera typer av konton som kan skapas. Azure Active Directory, Active Directory B2B och Active Directory B2C resurs i de typer av användarkonton som kan användas.

Följande typer av konton är tillgängliga:

- **Arbetskonto** – Ett arbetskonto kan komma åt resurser i en klientorganisation och kan hantera klienter med en administratörsroll.
- **Gästkonto** – Ett gästkonto kan bara vara ett Microsoft-konto eller en Azure Active Directory som kan användas för att komma åt program eller hantera klienter.
- **Konsumentkonto** – Ett konsumentkonto används av en användare av de program som du har registrerat med Azure AD B2C. Konsumentkonton kan skapas genom att:
  - Användaren som går igenom ett användarflöde för registrering i ett Azure AD B2C program
  - Använda Microsoft Graph API
  - Använda Azure Portal

## <a name="work-account"></a>Arbetskonto

Ett arbetskonto skapas på samma sätt för alla klienter baserat på Azure AD. Om du vill skapa ett arbetskonto kan du använda informationen i [Snabbstart: Lägga till nya användare i Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md). Ett arbetskonto skapas med alternativet **Ny** användare i Azure Portal.

När du lägger till ett nytt arbetskonto måste du tänka på följande konfigurationsinställningar:

- **Namn** och **Användarnamn –** Egenskapen **Namn** innehåller användarens givna och efternamn. Användarnamnet **är** den identifierare som användaren anger för att logga in. Användarnamnet innehåller den fullständiga domänen. Domännamnsdelen av användarnamnet måste antingen vara det första standarddomännamnet *your-domain.onmicrosoft.com*, eller [](../active-directory/fundamentals/add-custom-domain.md) ett verifierat, icke-federerat anpassat domännamn, till *exempel contoso.com*. 
- **E-post** – Den nya användaren kan också logga in med en e-postadress. Vi stöder inte specialtecken eller flerabyte tecken i e-post, till exempel japanska tecken.
- **Profil** – Kontot konfigureras med en profil med användardata. Du har möjlighet att ange förnamn, efternamn, befattning och avdelningsnamn. Du kan redigera profilen när kontot har skapats.
- **Grupper** – Använd grupper för att utföra hanteringsuppgifter, till exempel tilldela licenser eller behörigheter till många användare eller enheter samtidigt. Du kan placera det nya kontot i en befintlig [grupp](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) i din klientorganisation.
- **Katalogroll** – Du måste ange den åtkomstnivå som användarkontot har till resurser i din klientorganisation. Följande behörighetsnivåer är tillgängliga:

    - **Användare** – Användare kan komma åt tilldelade resurser men kan inte hantera de flesta klientresurser.
    - **Global administratör** – Globala administratörer har fullständig kontroll över alla klientresurser.
    - **Begränsad administratör** – Välj den administrativa rollen eller rollerna för användaren. Mer information om vilka roller som kan väljas finns i [Tilldela administratörsroller i Azure Active Directory](../active-directory/roles/permissions-reference.md).

### <a name="create-a-work-account"></a>Skapa ett arbetskonto

Du kan använda följande information för att skapa ett nytt arbetskonto:

- [Azure-portalen](../active-directory/fundamentals/add-users-azure-active-directory.md)
- [Microsoft Graph](/graph/api/user-post-users)

### <a name="update-a-user-profile"></a>Uppdatera en användarprofil

Du kan använda följande information för att uppdatera en användares profil:

- [Azure-portalen](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
- [Microsoft Graph](/graph/api/user-update)

### <a name="reset-a-password-for-a-user"></a>Återställa ett lösenord för en användare

Du kan använda följande information för att återställa lösenordet för en användare:

- [Azure-portalen](../active-directory/fundamentals/active-directory-users-reset-password-azure-portal.md)
- [Microsoft Graph](/graph/api/user-update)

## <a name="guest-user"></a>Gästanvändare

Du kan bjuda in externa användare till din klientorganisation som gästanvändare. Ett vanligt scenario för att bjuda in en gästanvändare till Azure AD B2C klientorganisation är att dela administrationens ansvarsområden. Ett exempel på hur du använder ett gästkonto finns [i Egenskaper för en Azure Active Directory B2B-samarbetsanvändare](../active-directory/external-identities/user-properties.md).

När du bjuder in en gästanvändare till din klientorganisation anger du mottagarens e-postadress och ett meddelande som beskriver inbjudan. Inbjudningslänken tar användaren till sidan för medgivande. Om en inkorg inte är kopplad till e-postadressen kan användaren navigera till sidan för medgivande genom att gå till en Microsoft-sida med hjälp av de inbjudna autentiseringsuppgifterna. Användaren tvingas sedan lösa in inbjudan på samma sätt som när användaren klickar på länken i e-postmeddelandet. Exempel: `https://myapps.microsoft.com/B2CTENANTNAME`.

Du kan också använda [api:Microsoft Graph för](/graph/api/invitation-post) att bjuda in en gästanvändare.

## <a name="consumer-user"></a>Konsumentanvändare

Användaren kan logga in på program som skyddas av Azure AD B2C, men kan inte komma åt Azure-resurser som Azure Portal. Användaren kan använda ett lokalt konto eller federerade konton, till exempel Facebook eller Twitter. Ett konsumentkonto skapas med hjälp av ett användarflöde för registrering eller [inloggning,](user-flow-overview.md)med hjälp av Microsoft Graph-API:et eller med hjälp av Azure Portal.

Du kan ange de data som samlas in när ett konsumentanvändarkonto skapas. Mer information finns i Lägga [till användarattribut och anpassa användarindata.](configure-user-input.md)

Mer information om hur du hanterar konsumentkonton finns [i Hantera Azure AD B2C-användarkonton med Microsoft Graph](./microsoft-graph-operations.md).

### <a name="migrate-consumer-user-accounts"></a>Migrera användarkonton för konsumenter

Du kan behöva migrera befintliga konsumentanvändarkonton från valfri identitetsprovider till Azure AD B2C. Mer information finns i [Migrera användare till Azure AD B2C](user-migration.md).
