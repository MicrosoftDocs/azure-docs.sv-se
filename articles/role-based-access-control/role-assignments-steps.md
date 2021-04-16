---
title: Steg för att tilldela en Azure-roll – Azure RBAC
description: Lär dig hur du tilldelar Azure-roller till användare, grupper, tjänsthuvudnamn eller hanterade identiteter med hjälp av rollbaserad åtkomstkontroll i Azure (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 04/14/2021
ms.author: rolyon
ms.openlocfilehash: 40a17da6383fb1f368c74a82fefa71991cdc1b19
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517682"
---
# <a name="steps-to-assign-an-azure-role"></a>Steg för att tilldela en Azure-roll

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] Den här artikeln beskriver de avancerade stegen för att tilldela Azure-roller med [hjälp av Azure Portal](role-assignments-portal.md), [Azure PowerShell](role-assignments-powershell.md), [Azure CLI](role-assignments-cli.md)eller [REST API](role-assignments-rest.md).

## <a name="step-1-determine-who-needs-access"></a>Steg 1: Fastställ vem som behöver åtkomst

Du måste först bestämma vem som behöver åtkomst. Du kan tilldela en roll till en användare, grupp, tjänstens huvudnamn eller en hanterad identitet. Detta kallas även för ett *säkerhetsobjekt.*

![Säkerhetsobjekt för en rolltilldelning](./media/shared/rbac-security-principal.png)

- Användare – en person som har en profil i Azure Active Directory. Du kan även tilldela roller till användare i andra klientorganisationer. Information om användare i andra organisationer finns i [Azure Active Directory B2B](../active-directory/external-identities/what-is-b2b.md).
- Grupp – en uppsättning användare som skapas i Azure Active Directory. När du tilldelar en roll till en grupp får alla användare i gruppen den rollen. 
- Tjänstens huvudnamn – en säkerhetsidentitet som används av program eller tjänster för att få åtkomst till specifika Azure-resurser. Du kan se det som en *användaridentitet* (användarnamn och lösenord eller certifikat) för ett program.
- Hanterad identitet – en identitet i Azure Active Directory som hanteras automatiskt av Azure. Normalt använder du [hanterade identiteter](../active-directory/managed-identities-azure-resources/overview.md) när du utvecklar molnbaserade program för att hantera uppgifterna för autentisering i Azure-tjänster.

## <a name="step-2-select-the-appropriate-role"></a>Steg 2: Välj lämplig roll

Behörigheter grupperas tillsammans i en *rolldefinition*. Det kallas vanligtvis bara för en *roll*. Du kan välja från en lista med flera inbyggda roller. Om de inbyggda rollerna inte uppfyller organisationens specifika krav, kan du skapa egna anpassade roller.

![Rolldefinition för en rolltilldelning](./media/shared/rbac-role-definition.png)

Följande listar fyra grundläggande inbyggda roller. De första tre gäller för alla resurstyper.

- [Ägare](built-in-roles.md#owner) – Har fullständig åtkomst till alla resurser, inklusive rätten att delegera åtkomst till andra.
- [Deltagare](built-in-roles.md#contributor) – Kan skapa och hantera alla typer av Azure-resurser, men kan inte bevilja åtkomst till andra.
- [Läsare](built-in-roles.md#reader) – Kan visa befintliga Azure-resurser.
- [Administratör för användaråtkomst](built-in-roles.md#user-access-administrator) – Kan hantera användarnas åtkomst till Azure-resurser.

Resten av de inbyggda rollerna tillåter hantering av specifika Azure-resurser. Till exempel tillåter rollen [Virtuell datordeltagare](built-in-roles.md#virtual-machine-contributor) att en användare skapar och hanterar virtuella datorer.

1. Börja med den omfattande artikeln om [inbyggda Azure-roller.](built-in-roles.md) Tabellen överst i artikeln är ett index för informationen senare i artikeln.

1. I den här artikeln går du till tjänstkategorin (till exempel beräkning, lagring och databaser) för den resurs som du vill bevilja behörigheter till. Det enklaste sättet att hitta det du letar efter är vanligtvis att söka på sidan efter ett relevant nyckelord, till exempel "blob", "virtuell dator" och så vidare.

1. Granska rollerna som anges för tjänstkategorin och identifiera de specifika åtgärder som du behöver. Börja alltid med den mest restriktiva rollen.

    Om ett säkerhetsobjekt till exempel behöver läsa blobar i ett Azure Storage-konto, men inte behöver skrivåtkomst, väljer du [Storage Blob Data Reader](built-in-roles.md#storage-blob-data-reader) i stället för Storage Blob [Data-deltagare](built-in-roles.md#storage-blob-data-contributor) (och definitivt inte rollen Storage [Blob Data-ägare på](built-in-roles.md#storage-blob-data-owner) administratörsnivå). Du kan alltid uppdatera rolltilldelningarna senare efter behov.

1. Om du inte hittar någon lämplig roll kan du skapa en [anpassad roll](custom-roles.md).

## <a name="step-3-identify-the-needed-scope"></a>Steg 3: Identifiera det omfång som behövs

*Omfång* är den uppsättning resurser som åtkomsten som gäller för. I Azure kan du ange ett omfång på fyra nivåer: [hanteringsgrupp,](../governance/management-groups/overview.md)prenumeration, [resursgrupp](../azure-resource-manager/management/overview.md#resource-groups)och resurs. Omfång är strukturerade i en överordnad/underordnad relation. Varje hierarkinivå gör omfånget mer specifikt. Du kan tilldela roller på vilken som helst av dessa omfångsnivåer. Vilken nivå du väljer avgör hur brett rollen tillämpas. Lägre nivåer ärver rollbehörigheter från högre nivåer. 

![Omfång för en rolltilldelning](./media/shared/rbac-scope.png)

När du tilldelar en roll i ett överordnat omfång ärvs dessa behörigheter till underordnade omfång. Exempel:

- Om du tilldelar [rollen](built-in-roles.md#reader) Läsare till en användare i hanteringsgruppsomfånget kan användaren läsa allt i alla prenumerationer i hanteringsgruppen.
- Om du tilldelar [rollen Faktureringsläsare](built-in-roles.md#billing-reader) till en grupp i prenumerationsomfånget kan medlemmarna i den gruppen läsa faktureringsdata för varje resursgrupp och resurs i prenumerationen.
- Om du tilldelar rollen [Deltagare](built-in-roles.md#contributor) till ett program i resursgruppomfånget kan den hantera resurser av alla typer i den resursgruppen, men inte i andra resursgrupper i prenumerationen.

 Mer information finns i Förstå [omfånget](scope-overview.md).

## <a name="step-4-check-your-prerequisites"></a>Steg 4. Kontrollera dina krav

Om du vill tilldela roller måste du vara inloggad med en användare som har [](built-in-roles.md#owner) tilldelats en roll som har skrivbehörighet för rolltilldelningar, till exempel Ägare eller Administratör för användaråtkomst i det omfång som du försöker tilldela rollen. [](built-in-roles.md#user-access-administrator) Om du vill ta bort en rolltilldelning måste du på samma sätt ha behörigheten ta bort rolltilldelningar.

- `Microsoft.Authorization/roleAssignments/write`
- `Microsoft.Authorization/roleAssignments/delete`

Om ditt användarkonto inte har behörighet att tilldela en roll i din prenumeration visas ett felmeddelande om att ditt konto "inte har behörighet att utföra åtgärden "Microsoft.Authorization/roleAssignments/write". I det här fallet kontaktar du administratörerna för din prenumeration eftersom de kan tilldela behörigheterna för din räkning.

Om du använder ett huvudnamn för tjänsten för att tilldela roller kan du få felet "Otillräcklig behörighet för att slutföra åtgärden". Det här felet beror troligen på att Azure försöker leta upp identiteten för mottagare i Azure Active Directory (Azure AD) och tjänstens huvudnamn kan inte läsa Azure AD som standard. I det här fallet måste du ge tjänstens huvudnamn behörighet att läsa data i katalogen. Om du använder Azure CLI kan du också skapa rolltilldelningen med objekt-ID:t assignee för att hoppa över Azure AD-sökning. Mer information finns i [Felsöka Azure RBAC](troubleshooting.md).

## <a name="step-5-assign-role"></a>Steg 5. Tilldela rollen

När du känner till säkerhetsobjekt, roll och omfång kan du tilldela rollen. Du kan tilldela roller med hjälp Azure Portal, Azure PowerShell, Azure CLI, Azure-API:er eller REST-API:er. Du kan ha upp till **2 000** rolltilldelningar i varje prenumeration. Den här gränsen omfattar rolltilldelningar för prenumerationen, resursgruppen och resursomfången. Du kan ha upp till **500** rolltilldelningar i varje hanteringsgrupp.

I följande artiklar finns detaljerade anvisningar för hur du tilldelar roller.

- [Tilldela Azure-roller med hjälp av Azure Portal](role-assignments-portal.md)
- [Tilldela Azure-roller med Azure PowerShell](role-assignments-powershell.md)
- [Tilldela Azure-roller med Hjälp av Azure CLI](role-assignments-cli.md)
- [Tilldela Azure-roller med hjälp av REST API](role-assignments-rest.md)

## <a name="next-steps"></a>Nästa steg

- [Självstudie: Bevilja en användare åtkomst till Azure-resurser med hjälp av Azure Portal](quickstart-assign-role-user-portal.md)