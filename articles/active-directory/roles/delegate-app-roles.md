---
title: Delegera program hantering administratörs behörighet – Azure AD | Microsoft Docs
description: Bevilja behörigheter för program åtkomst hantering i Azure Active Directory
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1e8a0f1919da125a571429e1efff06589c7e85a
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/15/2021
ms.locfileid: "103466719"
---
# <a name="delegate-app-registration-permissions-in-azure-active-directory"></a>Delegera registrerings behörigheter för app i Azure Active Directory

Den här artikeln beskriver hur du använder behörigheter som beviljats av anpassade roller i Azure Active Directory (Azure AD) för att hantera dina program hanterings behov. I Azure AD kan du delegera program skapande-och hanterings behörigheter på följande sätt:

- [Begränsa vem som kan skapa program](#restrict-who-can-create-applications) och hantera de program som de skapar. Som standard i Azure AD kan alla användare registrera program och hantera alla aspekter av program som de skapar. Detta kan begränsas till att endast tillåta valda personer som har behörighet.
- [Tilldela en eller flera ägare till ett program](#assign-application-owners). Detta är ett enkelt sätt att ge någon möjlighet att hantera alla aspekter av Azure AD-konfigurationen för ett enskilt program.
- [Tilldela en inbyggd administrativ roll](#assign-built-in-application-admin-roles) som beviljar åtkomst för att hantera konfiguration i Azure AD för alla program. Detta är det rekommenderade sättet att ge IT-experter åtkomst till att hantera omfattande program konfigurations behörigheter utan att bevilja åtkomst till att hantera andra delar av Azure AD som inte är relaterade till program konfigurationen.
- [Skapa en anpassad roll](#create-and-assign-a-custom-role-preview) som definierar mycket specifika behörigheter och tilldela den till någon antingen till omfånget för ett enskilt program som en begränsad ägare, eller i katalogens omfattning (alla program) som en begränsad administratör.

Det är viktigt att överväga att bevilja åtkomst med hjälp av någon av ovanstående metoder av två orsaker. Innan du delegerar möjligheten att utföra administrativa uppgifter minskar du den globala administratörs belastningen. För det andra ökar säkerheten position med begränsade behörigheter och risken för obehörig åtkomst minskas. Rikt linjer för planering av roll säkerhet finns i [skydda privilegie rad åtkomst för Hybrid-och moln distributioner i Azure AD](security-planning.md).

## <a name="restrict-who-can-create-applications"></a>Begränsa vem som kan skapa program

Som standard i Azure AD kan alla användare registrera program och hantera alla aspekter av program som de skapar. Alla har också möjlighet att samtycka till appar som har åtkomst till företags information för deras räkning. Du kan välja att selektivt bevilja dessa behörigheter genom att ställa in globala växlar till "nej" och lägga till de valda användarna i rollen Application Developer.

### <a name="to-disable-the-default-ability-to-create-application-registrations-or-consent-to-applications"></a>Så här inaktiverar du standard möjligheten att skapa program registreringar eller medgivande till program

1. Logga in på din Azure AD-organisation med ett konto som är berättigat till rollen global administratör i din Azure AD-organisation.
1. Ange ett eller båda av följande:

    - På [sidan användar inställningar för din organisation](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings)ställer du in inställningen för **användare kan registrera program** på Nej. Då inaktive ras standard möjligheten för användare att skapa program registreringar.
    - I [användar inställningarna för företags program](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)ställer du in **användarnas medgivande till program som har åtkomst till företags data för deras ställe** -inställning till Nej. Detta inaktiverar standard möjligheten för användare att samtycka till program som har åtkomst till företags information för deras räkning.

### <a name="grant-individual-permissions-to-create-and-consent-to-applications-when-the-default-ability-is-disabled"></a>Bevilja enskilda behörigheter för att skapa och godkänna program när standard möjligheten är inaktive rad

Tilldela rollen programutvecklare för att ge möjlighet att skapa program registreringar när inställningen **användare kan registrera program** är inställd på Nej. Den här rollen beviljar också behörighet att godkännas för en persons räkning när **användarna kan godkänna att appar som har åtkomst till företagets data för deras räkning** är inställda på Nej. Som ett system beteende läggs de automatiskt till som första ägare när en användare skapar en ny program registrering. Ägarskaps behörigheter ger användaren möjlighet att hantera alla aspekter av en program registrering eller ett företags program som de äger.

## <a name="assign-application-owners"></a>Tilldela program ägare

Tilldelning av ägare är ett enkelt sätt att ge möjlighet att hantera alla aspekter av Azure AD-konfigurationen för en specifik program registrering eller ett företags program. När en användare skapar en ny program registrering läggs de automatiskt till som den första ägaren, som ett system beteende. Ägarskaps behörigheter ger användaren möjlighet att hantera alla aspekter av en program registrering eller ett företags program som de äger. Den ursprungliga ägaren kan tas bort och ytterligare ägare kan läggas till.

### <a name="enterprise-application-owners"></a>Företags program ägare

Som ägare kan en användare hantera den organisatoriska konfigurationen av företags programmet, till exempel konfiguration av enkel inloggning, etablering och användar tilldelning. En ägare kan också lägga till eller ta bort andra ägare. Till skillnad från globala administratörer kan ägarna bara hantera de företags program som de äger.

I vissa fall inkluderar företags program som skapats från program galleriet både ett företags program och en program registrering. När detta är sant lägger till en ägare till företags programmet automatiskt ägaren till motsvarande program registrering som ägare.

### <a name="to-assign-an-owner-to-an-enterprise-application"></a>Tilldela en ägare till ett företags program

1. Logga in på [din Azure AD-organisation](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) med ett konto som är berättigat till program administratören eller moln program administratören för organisationen.
1. På [sidan Appregistreringar](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) för organisationen väljer du en app för att öppna översikts sidan för appen.
1. Välj **ägare** om du vill se en lista över ägare för appen.
1. Välj **Lägg till** för att välja en eller flera ägare som ska läggas till i appen.

> [!IMPORTANT]
> Användare och tjänstens huvud namn kan vara ägare till program registreringar. Endast användare kan vara ägare till företags program. Grupper kan inte tilldelas som ägare till någon.
>
> Ägare kan lägga till autentiseringsuppgifter till ett program och använda dessa autentiseringsuppgifter för att personifiera programmets identitet. Programmet kan ha fler behörigheter än ägaren och kan därför vara en höjning av behörighet över vad ägaren har åtkomst till som användare eller tjänstens huvud namn. En program ägare kan potentiellt skapa eller uppdatera användare eller andra objekt vid personifiering av programmet, beroende på programmets behörigheter.

## <a name="assign-built-in-application-admin-roles"></a>Tilldela inbyggda program administratörs roller

Azure AD har en uppsättning inbyggda administratörs roller för att bevilja åtkomst till att hantera konfigurationen i Azure AD för alla program. Dessa roller är det rekommenderade sättet att ge IT-experter åtkomst till att hantera omfattande program konfigurations behörigheter utan att bevilja åtkomst till att hantera andra delar av Azure AD som inte är relaterade till program konfigurationen.

- Program administratör: användare med den här rollen kan skapa och hantera alla aspekter av företags program, program registreringar och programproxy-inställningar. Den här rollen ger också möjlighet att godkänna delegerade behörigheter och program behörigheter exklusive Microsoft Graph. Användare som har tilldelats den här rollen läggs inte till som ägare när de skapar nya program registreringar eller företags program.
- Moln program administratör: användare med den här rollen har samma behörigheter som program administratörs rollen, förutom möjligheten att hantera programproxyn. Användare som har tilldelats den här rollen läggs inte till som ägare när de skapar nya program registreringar eller företags program.

Mer information och se beskrivningen av dessa roller finns i [inbyggda Azure AD-roller](permissions-reference.md).

Följ anvisningarna i guiden [tilldela roller till användare med Azure Active Directory](../fundamentals/active-directory-users-assign-role-azure-portal.md) instruktions guide för att tilldela rollen program administratör eller moln program administratör.

> [!IMPORTANT]
> Program administratörer och moln program administratörer kan lägga till autentiseringsuppgifter till ett program och använda dessa autentiseringsuppgifter för att personifiera programmets identitet. Programmet kan ha behörigheter som är en utökning av behörighet över administratörs rollens behörigheter. En administratör i den här rollen kan potentiellt skapa eller uppdatera användare eller andra objekt vid personifiering av programmet, beroende på programmets behörigheter.
> Ingen av rollerna ger möjlighet att hantera inställningar för villkorlig åtkomst.

## <a name="create-and-assign-a-custom-role-preview"></a>Skapa och tilldela en anpassad roll (förhands granskning)

Att skapa anpassade roller och tilldela anpassade roller är separata steg:

- [Skapa en anpassad *roll definition*](custom-create.md) och [Lägg till behörigheter till den från en för inställnings lista](custom-available-permissions.md). Detta är samma behörigheter som används i de inbyggda rollerna.
- [Skapa en *roll tilldelning*](custom-assign-powershell.md) för att tilldela den anpassade rollen.

Den här separationen gör att du kan skapa en enda roll definition och sedan tilldela den flera gånger i olika *omfång*. En anpassad roll kan tilldelas i hela organisationen, eller så kan den tilldelas i omfånget om ett enskilt Azure AD-objekt. Ett exempel på ett objekt omfång är en registrering av en enda app. Med olika omfång kan samma roll definition tilldelas till Lisa över alla app-registreringar i organisationen och sedan till Naveen över enbart appen contoso utgifts rapporter.

Tips när du skapar och använder anpassade roller för att delegera program hantering:
- Anpassade roller beviljar endast åtkomst i de senaste program registrerings bladen i Azure AD-portalen. De beviljar inte åtkomst i bladet för den äldre appens registreringar.
- Anpassade roller beviljar inte åtkomst till Azure AD-portalen när användar inställningen "begränsa åtkomst till Azure AD-administrationskonsolen" är inställd på Ja.
- Appregistreringar användaren har åtkomst till att använda roll tilldelningar visas bara på fliken alla program på registrerings sidan för appen. De visas inte i fliken ' ägda program '.

Mer information om grunderna för anpassade roller finns i [Översikt över anpassade roller](custom-overview.md), samt hur du [skapar en anpassad roll](custom-create.md) och hur du [tilldelar en roll](custom-assign-powershell.md).

## <a name="next-steps"></a>Nästa steg

- [Under typer och behörigheter för program registrering](custom-available-permissions.md)
- [Roll referens för Azure AD-administratör](permissions-reference.md)
