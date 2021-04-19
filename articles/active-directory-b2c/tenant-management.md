---
title: Hantera Azure Active Directory B2C
titleSuffix: Azure Active Directory B2C
description: Lär dig hur du hanterar Azure Active Directory B2C klientorganisation. Lär dig vilka Azure AD-funktioner som stöds i Azure AD B2C, hur du använder administratörsroller för att hantera resurser och hur du lägger till arbetskonton och gästanvändare i din Azure AD B2C klientorganisation.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/19/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c3ea17a4f6dc2fb5134c6ceb1ae37d25e0881365
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107715521"
---
# <a name="manage-your-azure-active-directory-b2c-tenant"></a>Hantera din Azure Active Directory B2C klient

I Azure Active Directory B2C (Azure AD B2C) representerar en klientorganisation din katalog med konsumentanvändare. Varje Azure AD B2C klientorganisation är separat och åtskild från andra Azure AD B2C klientorganisation. En Azure AD B2C klient skiljer sig från en Azure Active Directory klientorganisation, som du kanske redan har. I den här artikeln får du lära dig hur du hanterar Azure AD B2C klientorganisation.

## <a name="supported-azure-ad-features"></a>Azure AD-funktioner som stöds

Azure AD B2C förlitar sig på Azure AD-plattformen. Följande Azure AD-funktioner kan användas i din Azure AD B2C klientorganisation.

|Funktion  |Azure AD  | Azure AD B2C |
|---------|---------|---------|
| [Grupper](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) | Grupper kan användas för att hantera administrativa konton och användarkonton.| Grupper kan användas för att hantera administrativa konton. [Konsumentkonton](user-overview.md#consumer-user) stöder inte grupper. |
| [Bjuda External Identities gäster](../active-directory//external-identities/add-users-administrator.md)| Du kan bjuda in gästanvändare och konfigurera External Identities funktioner som federation och inloggning med Facebook- och Google-konton. | Du kan bara bjuda in en Microsoft-konto eller en Azure AD-användare som gäst till din Azure AD-klientorganisation för åtkomst till program eller hantering av klienter. För [konsumentkonton](user-overview.md#consumer-user)använder du Azure AD B2C användarflöden och anpassade principer för att hantera användare och registrera eller logga in med externa identitetsproviders, till exempel Google eller Facebook. |
| [Roller och administratörer](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)| Fullständigt stöd för administrativa konton och användarkonton. | Roller stöds inte med [konsumentkonton.](user-overview.md#consumer-user) Konsumentkonton har inte åtkomst till några Azure-resurser.|
| [Egna domännamn](../active-directory/roles/permissions-reference.md#) |  Du kan endast använda anpassade Azure AD-domäner för administrativa konton. | [Konsumentkonton](user-overview.md#consumer-user) kan logga in med ett användarnamn, telefonnummer eller valfri e-postadress. Du kan använda anpassade [domäner i](custom-domain.md) omdirigerings-URL:er.|
| [Villkorlig åtkomst](../active-directory/roles/permissions-reference.md#) | Fullständigt stöd för administrativa konton och användarkonton. | En delmängd av funktionerna för villkorsstyrd åtkomst i Azure AD stöds med [konsumentkonton.](user-overview.md#consumer-user) Lean hur du konfigurerar Azure AD B2C [anpassad domän.](conditional-access-user-flow.md)|

## <a name="other-azure-resources-in-your-tenant"></a>Andra Azure-resurser i din klientorganisation

I en Azure AD B2C klientorganisation kan du inte etablera andra Azure-resurser, till exempel virtuella datorer, Azure-webbappar eller Azure-funktioner. Du måste skapa dessa resurser i din Azure AD-klientorganisation.

## <a name="azure-ad-b2c-accounts-overview"></a>översikt Azure AD B2C konton

Följande typer av konton kan skapas i en Azure AD B2C klientorganisation:

I en Azure AD B2C klient finns det flera typer av konton som kan skapas enligt beskrivningen i artikeln Översikt över [användarkonton i Azure Active Directory B2C.](user-overview.md)

- **Arbetskonto** – Ett arbetskonto kan komma åt resurser i en klientorganisation och kan hantera klienter med en administratörsroll.
- **Gästkonto** – Ett gästkonto kan bara vara ett Microsoft-konto eller en Azure Active Directory som kan användas för att komma åt program eller hantera klienter.
- **Konsumentkonto** – Ett konsumentkonto används av en användare av de program som du har registrerat med Azure AD B2C.

Mer information om dessa kontotyper finns [i Översikt över användarkonton i Azure Active Directory B2C](user-overview.md). Alla användare som ska tilldelas att hantera din Azure AD B2C klientorganisation måste ha ett Azure AD-användarkonto så att de kan komma åt Azure-relaterade tjänster. Du kan lägga till en sådan användare genom att skapa ett konto [](#invite-an-administrator-guest-account) (arbetskonto) i din Azure AD B2C-klientorganisation eller genom att bjuda in dem till din Azure AD B2C-klientorganisation som gästanvändare. [](#add-an-administrator-work-account)

## <a name="use-roles-to-control-resource-access"></a>Använd roller för att styra åtkomsten till resurser

När du planerar din strategi för åtkomstkontroll är det bäst att tilldela användare den minst privilegierade rollen som krävs för att få åtkomst till resurser. I följande tabell beskrivs de primära resurserna i din Azure AD B2C klientorganisation och de lämpligaste administrativa rollerna för de användare som hanterar dem.

|Resurs  |Beskrivning  |Roll  |
|---------|---------|---------|
|[Programregistreringar](tutorial-register-applications.md) | Skapa och hantera alla aspekter av dina webb-, mobil- och interna programregistreringar inom Azure AD B2C.|[Programadministratör](../active-directory/roles/permissions-reference.md#global-administrator)|
|[Identitetsproviders](add-identity-provider.md)| Konfigurera den [lokala identitetsprovidern](identity-provider-local.md) och externa sociala identitetsproviders eller företagsidentitetsproviders. | [Administratör för extern identitetsprovider](../active-directory/roles/permissions-reference.md#external-identity-provider-administrator)|
|[API-anslutningsprogram](add-api-connector.md)| Integrera dina användarflöden med webb-API:er för att anpassa användarupplevelsen och integrera med externa system.|[Administratör för användarflödesattribut för externt ID](../active-directory/roles/permissions-reference.md#external-id-user-flow-administrator)|
|[Företagsanpassning](customize-ui.md#configure-company-branding)| Anpassa dina användarflödessidor.| [Global administratör](../active-directory/roles/permissions-reference.md#global-administrator)|
|[Användarattribut](user-flow-custom-attributes.md)| Lägga till eller ta bort anpassade attribut som är tillgängliga för alla användarflöden.| [Administratör för användarflödesattribut för externt ID](../active-directory/roles/permissions-reference.md#external-id-user-flow-attribute-administrator)|
|Hantera användare| Hantera [konsumentkonton och](manage-users-portal.md) administrativa konton enligt beskrivningen i den här artikeln.| [Användaradministratör](../active-directory/roles/permissions-reference.md#user-administrator)|
|Roller och administratörer| Hantera rolltilldelningar i Azure AD B2C katalog. Skapa och hantera grupper som kan tilldelas till Azure AD B2C roller. |[Global administratör,](../active-directory/roles/permissions-reference.md#global-administrator) [privilegierad rolladministratör](../active-directory/roles/permissions-reference.md#privileged-role-administrator)|
|[Användarflöden](user-flow-overview.md)|För snabb konfiguration och aktiverande av vanliga identitetsuppgifter, till exempel registrering, inloggning och profilredigering.| [Administratör för användarflödesattribut för externt ID](../active-directory/roles/permissions-reference.md#external-id-user-flow-administrator)|
|[Anpassade principer](user-flow-overview.md)| Skapa, läsa, uppdatera och ta bort alla anpassade principer i Azure AD B2C.| [B2C IEF-principadministratör](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator)|
|[Principnycklar](policy-keys-overview.md)|Lägg till och hantera krypteringsnycklar för signering och validering av token, klienthemligheter, certifikat och lösenord som används i anpassade principer.|[B2C IEF-administratör för nyckeluppsättning](../active-directory/roles/permissions-reference.md#b2c-ief-keyset-administrator)|


## <a name="add-an-administrator-work-account"></a>Lägga till en administratör (arbetskonto)

Följ dessa steg om du vill skapa ett nytt administrativt konto:

1. Logga in på Azure Portal [med](https://portal.azure.com/) behörigheter som global administratör eller privilegierad rolladministratör.
1. Välj filtret **Katalog + prenumeration** på den översta menyn och välj sedan den katalog som innehåller din Azure AD B2C klientorganisation.
1. Under **Azure-tjänster** väljer **du Azure AD B2C**. Eller använd sökrutan för att hitta och välja **Azure AD B2C**.
1. Under **Hantera** väljer du **Användare**.
1. Välj **Ny användare**.
1. På sidan **Användare** anger du information för den här användaren:

   - **Namn**. Krävs. Den nya användarens för- och efternamn. Till exempel *Mary Parker*.
   - **Användarnamn**. Krävs. Användarnamnet för den nya användaren. Till exempel `mary@contoso.com`.
     Domändelen av användarnamnet måste använda antingen det första standarddomännamnet, *\<yourdomainname> .onmicrosoft.com*.
   - **Grupper**. Du kan också lägga till användaren i en eller flera befintliga grupper. Du kan också lägga till användaren i grupper vid ett senare tillfälle. 
   - **Katalogroll:** Om du behöver administratörsbehörighet för Azure AD för användaren kan du lägga till dem i en Azure AD-roll. Du kan tilldela användaren att vara en Global administratör eller en eller flera av de begränsade administratörsrollerna i Azure AD. Mer information om hur du tilldelar roller finns i [Använda roller för att kontrollera resursåtkomst.](#use-roles-to-control-resource-access)
   - **Jobbinformation:** Du kan lägga till mer information om användaren här eller göra det senare. 

1. Kopiera det automatiskt genererade lösenordet som anges i **rutan** Lösenord. Du måste ge det här lösenordet till användaren för att logga in för första gången.
1. Välj **Skapa**.

Användaren skapas och läggs till i din Azure AD B2C klientorganisation. Det är bättre att ha minst ett arbetskonto som är inbyggt i din Azure AD B2C som tilldelats rollen Global administratör. Det här kontot kan betraktas som ett "break-glass"-konto.

## <a name="invite-an-administrator-guest-account"></a>Bjud in en administratör (gästkonto)

Du kan också bjuda in en ny gästanvändare att hantera din klientorganisation. Gästkontot är det bästa alternativet när din organisation också har Azure AD eftersom livscykeln för den här identiteten kan hanteras externt. 

Följ dessa steg om du vill bjuda in en användare:

1. Logga in på Azure Portal [behörigheter](https://portal.azure.com/) som Global administratör eller Privilegierad rolladministratör.
1. Välj filtret **Katalog +** prenumeration på den översta menyn och välj sedan den katalog som innehåller din Azure AD B2C klientorganisation.
1. Under **Azure-tjänster** väljer **du Azure AD B2C**. Eller använd sökrutan för att hitta och välja **Azure AD B2C**.
1. Under **Hantera** väljer du **Användare**.
1. Välj **Nytt gästkonto.**
1. På sidan **Användare** anger du information för den här användaren:

   - **Namn**. Krävs. Den nya användarens för- och efternamn. Till exempel *Mary Parker*.
   - **E-postadress**. Krävs. E-postadressen för den användare som du vill bjuda in. Till exempel `mary@contoso.com`.   
   - **Personligt meddelande:** Du lägger till ett personligt meddelande som ska ingå i e-postmeddelandet med inbjudan.
   - **Grupper**. Du kan också lägga till användaren i en eller flera befintliga grupper. Du kan också lägga till användaren i grupper vid ett senare tillfälle.
   - **Katalogroll:** Om du behöver administratörsbehörighet för Azure AD för användaren kan du lägga till dem i en Azure AD-roll. Du kan tilldela användaren att vara en Global administratör eller en eller flera av de begränsade administratörsrollerna i Azure AD. Mer information om hur du tilldelar roller finns i [Använda roller för att styra resursåtkomst.](#use-roles-to-control-resource-access)
   - **Jobbinformation:** Du kan lägga till mer information om användaren här eller göra det senare.

1. Välj **Skapa**.

Ett e-postmeddelande med inbjudan skickas till användaren. Användaren måste acceptera inbjudan för att kunna logga in.

### <a name="resend-the-invitation-email"></a>Skicka om e-postinbjudan

Om gästen inte fick e-postinbjudan eller om inbjudan har upphört att gälla kan du skicka om inbjudan. Som ett alternativ till e-postinbjudan kan du ge en gäst en direktlänk för att acceptera inbjudan. Så här skickar du om inbjudan och hämtar direktlänken:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj filtret **Katalog + prenumeration** på den översta menyn och välj sedan den katalog som innehåller din Azure AD B2C klientorganisation.
1. Under **Azure-tjänster** väljer **du Azure AD B2C**. Eller använd sökrutan för att hitta och välja **Azure AD B2C**.
1. Under **Hantera** väljer du **Användare**.
1. Sök efter och välj den användare som du vill skicka om inbjudan till.
1. I **| Profilsida,** under **Identitet,** väljer **du (Hantera).**
    
    ![Skärmbild som visar hur du skickar om e-postinbjudan till gästkontot.](./media/tenant-management/guest-account-resend-invite.png)

1. För **Skicka om inbjudan?** väljer du **Ja.** När **Är du säker på att du vill skicka om en inbjudan?** väljer du **Ja.**
1. Azure AD B2C skickar inbjudan. Du kan också kopiera inbjudnings-URL:en och ange den direkt till gästen.
    
    ![Skärmbild som visar hur du hämtar inbjudnings-URL:en.](./media/tenant-management/guest-account-invitation-url.png)  
 
## <a name="add-a-role-assignment"></a>Lägg till en rolltilldelning

Du kan tilldela en roll när du [skapar en användare eller](#add-an-administrator-work-account) bjuder in en [gästanvändare.](#invite-an-administrator-guest-account) Du kan lägga till en roll, ändra rollen eller ta bort en roll för en användare:

1. Logga in på Azure Portal [behörigheter](https://portal.azure.com/) som Global administratör eller Privilegierad rolladministratör.
1. Välj filtret **Katalog +** prenumeration på den översta menyn och välj sedan den katalog som innehåller din Azure AD B2C klientorganisation.
1. Under **Azure-tjänster** väljer **du Azure AD B2C**. Eller använd sökrutan för att hitta och välja **Azure AD B2C**.
1. Under **Hantera** väljer du **Användare**.
1. Välj den användare som du vill ändra rollerna för. Välj sedan **Tilldelade roller.**
1. Välj **Lägg till tilldelningar,** välj den roll som du vill tilldela (till *exempel Programadministratör*) och välj sedan Lägg **till**.

## <a name="remove-a-role-assignment"></a>Ta bort en rolltilldelning

Följ dessa steg om du behöver ta bort en rolltilldelning från en användare:

1. Välj **Azure AD B2C**, **välj** Användare och sök sedan efter och välj användaren.
1. Välj **Tilldelade roller.** Välj den roll som du vill ta bort, till *exempel Programadministratör* och välj sedan Ta **bort tilldelning.**

## <a name="review-administrator-account-role-assignments"></a>Granska rolltilldelningar för administratörskonto

Som en del av en granskningsprocess granskar du vanligtvis vilka användare som är tilldelade till specifika roller i Azure AD B2C katalogen. Använd följande steg för att granska vilka användare som för närvarande är tilldelade privilegierade roller.

1. Logga in på Azure Portal [med](https://portal.azure.com/) behörigheter som global administratör eller privilegierad rolladministratör.
1. Välj filtret **Katalog + prenumeration** på den översta menyn och välj sedan den katalog som innehåller din Azure AD B2C klientorganisation.
1. Under **Azure-tjänster** väljer **du Azure AD B2C**. Eller använd sökrutan för att hitta och välja **Azure AD B2C**.
1. Under **Hantera** väljer du **Roller och administratörer.**
1. Välj en roll, till exempel **Global administratör**. **Rollrollen | Sidan Tilldelningar** visar en lista över användare med den rollen.

## <a name="delete-an-administrator-account"></a>Ta bort ett administratörskonto

Om du vill ta bort en befintlig användare måste du ha *Global administratör* rolltilldelning. Globala administratörer kan ta bort alla användare, inklusive andra administratörer. *Användaradministratörer* kan ta bort alla användare som inte är administratörer.

1. I din Azure AD B2C-katalog väljer **du Användare** och sedan den användare som du vill ta bort.
1. Välj **Ta bort** och sedan Ja **för** att bekräfta borttagningen.

Användaren tas bort och visas inte längre på sidan **Användare – Alla** användare. Användaren kan visas på sidan **Borttagna användare** under de kommande 30 dagarna och kan återställas under den tiden. Mer information om hur du återställer en användare finns i [Återställa eller ta bort en nyligen borttagna användare med hjälp av Azure Active Directory](../active-directory/fundamentals/active-directory-users-restore.md).

## <a name="protect-administrative-accounts"></a>Skydda administrativa konton

Vi rekommenderar att du skyddar alla administratörskonton med multifaktorautentisering (MFA) för mer säkerhet. MFA är en identitetsverifieringsprocess under inloggningen som uppmanar användaren att ange en mer form av identifiering, till exempel en verifieringskod på sin mobila enhet eller en begäran i Microsoft Authenticator appen.

![Autentiseringsmetoder som används på skärmbilden av inloggningen](./media/tenant-management/sing-in-with-multi-factor-authentication.png)

Du kan aktivera [Standardinställningar för Azure AD-säkerhet](../active-directory/fundamentals/concept-fundamentals-security-defaults.md) för att tvinga alla administrativa konton att använda MFA.



## <a name="next-steps"></a>Nästa steg

- [Skapa en Azure Active Directory B2C-klientorganisation i Azure-portalen](tutorial-create-tenant.md)

