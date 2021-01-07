---
title: Steg för att lägga till en roll tilldelning – Azure RBAC
description: Lär dig hur du tilldelar Azure-roller till användare, grupper, tjänstens huvud namn eller hanterade identiteter med hjälp av rollbaserad åtkomst kontroll i Azure (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 09/30/2020
ms.author: rolyon
ms.openlocfilehash: 565b84603a14452dd2ec49f8cf61da128503bc03
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2021
ms.locfileid: "97964260"
---
# <a name="steps-to-add-a-role-assignment"></a>Steg för tillägg av en rolltilldelning

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] Den här artikeln beskriver de övergripande stegen för att lägga till en roll tilldelning med hjälp av [Azure Portal](role-assignments-portal.md), [Azure POWERSHELL](role-assignments-powershell.md), [Azure CLI](role-assignments-cli.md)eller [REST API](role-assignments-rest.md).

## <a name="step-1-determine-who-needs-access"></a>Steg 1: Bestäm vem som behöver åtkomst

Du måste först fastställa vem som behöver åtkomst. Du kan tilldela en roll till en användare, grupp, tjänstens huvud namn eller en hanterad identitet. Detta kallas även för ett *säkerhets objekt*.

![Säkerhetsobjekt för en rolltilldelning](./media/shared/rbac-security-principal.png)

- Användare – en person som har en profil i Azure Active Directory. Du kan även tilldela roller till användare i andra klientorganisationer. Information om användare i andra organisationer finns i [Azure Active Directory B2B](../active-directory/external-identities/what-is-b2b.md).
- Grupp – en uppsättning användare som skapas i Azure Active Directory. När du tilldelar en roll till en grupp får alla användare i gruppen den rollen. 
- Tjänstens huvudnamn – en säkerhetsidentitet som används av program eller tjänster för att få åtkomst till specifika Azure-resurser. Du kan se det som en *användaridentitet* (användarnamn och lösenord eller certifikat) för ett program.
- Hanterad identitet – en identitet i Azure Active Directory som hanteras automatiskt av Azure. Normalt använder du [hanterade identiteter](../active-directory/managed-identities-azure-resources/overview.md) när du utvecklar molnbaserade program för att hantera uppgifterna för autentisering i Azure-tjänster.

## <a name="step-2-find-the-appropriate-role"></a>Steg 2: hitta rätt roll

Behörigheter grupperas tillsammans i en *roll definition*. Den brukar bara kallas en *roll*. Du kan välja från en lista över flera inbyggda roller. Om de inbyggda rollerna inte uppfyller organisationens specifika krav, kan du skapa egna anpassade roller.

![Rolldefinition för en rolltilldelning](./media/shared/rbac-role-definition.png)

Följande listar fyra grundläggande inbyggda roller. De första tre gäller för alla resurstyper.

- [Ägare](built-in-roles.md#owner) – Har fullständig åtkomst till alla resurser, inklusive rätten att delegera åtkomst till andra.
- [Deltagare](built-in-roles.md#contributor) – Kan skapa och hantera alla typer av Azure-resurser, men kan inte bevilja åtkomst till andra.
- [Reader](built-in-roles.md#reader) – kan visa befintliga Azure-resurser.
- [Administratör för användaråtkomst](built-in-roles.md#user-access-administrator) – Kan hantera användarnas åtkomst till Azure-resurser.

Resten av de inbyggda rollerna tillåter hantering av specifika Azure-resurser. Till exempel tillåter rollen [Virtuell datordeltagare](built-in-roles.md#virtual-machine-contributor) att en användare skapar och hanterar virtuella datorer.

1. Börja med den omfattande artikeln, [inbyggda Azure-roller](built-in-roles.md). Tabellen överst i artikeln är ett index i informationen längre fram i artikeln.

1. I artikeln navigerar du till tjänst kategorin (till exempel Compute, Storage och databaser) för den resurs som du vill bevilja behörigheter för. Det enklaste sättet att hitta det du letar efter är vanligt vis att söka på sidan efter ett relevant nyckelord, t. ex. "BLOB", "virtuell dator" och så vidare.

1. Granska de roller som anges för tjänst kategorin och identifiera de åtgärder som du behöver. Börja alltid med den mest restriktiva rollen.

    Om ett säkerhets objekt till exempel behöver läsa blobbar i ett Azure Storage-konto, men inte behöver skriv åtkomst, väljer du [Storage BLOB data Reader](built-in-roles.md#storage-blob-data-reader) snarare än [Storage BLOB data-deltagare](built-in-roles.md#storage-blob-data-contributor) (och definitivt inte är administratörs rollen för [lagrings data ägare](built-in-roles.md#storage-blob-data-owner) på administratörs nivå). Du kan alltid uppdatera roll tilldelningarna senare efter behov.

1. Om du inte hittar en lämplig roll kan du skapa en [anpassad roll](custom-roles.md).

## <a name="step-3-identify-the-needed-scope"></a>Steg 3: identifiera omfattningen som krävs

*Omfång* är den uppsättning resurser som åtkomsten som gäller för. I Azure kan du ange ett omfång på fyra nivåer: [hanterings grupp](../governance/management-groups/overview.md), prenumeration, [resurs grupp](../azure-resource-manager/management/overview.md#resource-groups)och resurs. Omfång är strukturerade i en överordnad/underordnad relation. Varje nivå i hierarkin gör omfånget mer information. Du kan tilldela roller på någon av dessa nivåer av omfång. Nivån som du väljer avgör hur mycket rollen tillämpas. Lägre nivåer ärver roll behörigheter från högre nivåer. 

![Omfång för en rolltilldelning](./media/shared/rbac-scope.png)

När du tilldelar en roll vid en överordnad omfattning, ärvs dessa behörigheter till de underordnade omfången. Ett exempel:

- Om du tilldelar rollen [läsare](built-in-roles.md#reader) till en användare i hanterings gruppens omfattning kan den användaren läsa allt i alla prenumerationer i hanterings gruppen.
- Om du tilldelar rollen [fakturerings läsare](built-in-roles.md#billing-reader) till en grupp i prenumerations omfånget kan medlemmar i den gruppen läsa fakturerings data för varje resurs grupp och resurs i prenumerationen.
- Om du tilldelar rollen [Deltagare](built-in-roles.md#contributor) till ett program i resursgruppomfånget kan den hantera resurser av alla typer i den resursgruppen, men inte i andra resursgrupper i prenumerationen.

 Mer information finns i [förstå omfattning](scope-overview.md).

## <a name="step-4-check-your-prerequisites"></a>Steg 4. Kontrol lera dina krav

Om du vill tilldela roller måste du vara inloggad med en användare som har tilldelats en roll som har Skriv behörighet för roll tilldelning, till exempel [ägare](built-in-roles.md#owner) eller [administratör för användar åtkomst](built-in-roles.md#user-access-administrator) i den omfattning som du försöker tilldela rollen. Om du vill ta bort en roll tilldelning måste du också ha behörigheten Ta bort roll tilldelningar.

- `Microsoft.Authorization/roleAssignments/write`
- `Microsoft.Authorization/roleAssignments/delete`

Om ditt användar konto inte har behörighet att tilldela en roll i din prenumeration visas ett fel meddelande om att ditt konto inte har behörighet att utföra åtgärden Microsoft. Authorization/roleAssignments/Write. I det här fallet kontaktar du prenumerations administratörerna eftersom de kan tilldela behörigheter för din räkning.

## <a name="step-5-add-role-assignment"></a>Steg 5. Lägg till rolltilldelning

När du känner till säkerhets objekt, roll och omfattning kan du tilldela rollen. Du kan lägga till roll tilldelningar med hjälp av Azure Portal, Azure PowerShell, Azure CLI, Azure SDK: er eller REST-API: er. Du kan ha upp till **2000** roll tilldelningar i varje prenumeration. Den här gränsen omfattar roll tilldelningar i prenumerations-, resurs grupps-och resurs omfång. Du kan ha upp till **500** roll tilldelningar i varje hanterings grupp.

Se följande artiklar för detaljerade anvisningar om hur du lägger till roll tilldelningar.

- [Lägga till eller ta bort rolltilldelningar för Azure med hjälp av Azure-portalen](role-assignments-portal.md)
- [Lägga till eller ta bort Azure-rolltilldelningar med hjälp av Azure PowerShell](role-assignments-powershell.md)
- [Lägga till eller ta bort Azure-rolltilldelningar med hjälp av Azure CLI](role-assignments-cli.md)
- [Lägga till eller ta bort Azure-rolltilldelningar med hjälp av REST API](role-assignments-rest.md)

## <a name="next-steps"></a>Nästa steg

- [Självstudie: ge en användare åtkomst till Azure-resurser med hjälp av Azure Portal](quickstart-assign-role-user-portal.md)