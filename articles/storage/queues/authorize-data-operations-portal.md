---
title: Välj hur du vill ge åtkomst till köade data i Azure Portal
titleSuffix: Azure Storage
description: När du kommer åt Queue data med hjälp av Azure Portal, gör portalen förfrågningar till Azure Storage under försättsblad. Dessa förfrågningar till Azure Storage kan autentiseras och auktoriseras med hjälp av antingen ditt Azure AD-konto eller lagrings kontots åtkomst nyckel.
author: tamram
services: storage
ms.author: tamram
ms.reviewer: ozguns
ms.date: 09/08/2020
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: contperf-fy21q1
ms.openlocfilehash: 504d2eb939758e6045a2af095c66093c8754cb94
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97590757"
---
# <a name="choose-how-to-authorize-access-to-queue-data-in-the-azure-portal"></a>Välj hur du vill ge åtkomst till köade data i Azure Portal

När du kommer åt Queue data med hjälp av [Azure Portal](https://portal.azure.com), gör portalen förfrågningar till Azure Storage under försättsblad. En begäran till Azure Storage kan godkännas med hjälp av antingen ditt Azure AD-konto eller lagrings kontots åtkomst nyckel. Portalen visar vilken metod du använder och gör att du kan växla mellan de två om du har rätt behörighet.

## <a name="permissions-needed-to-access-queue-data"></a>Behörigheter som krävs för att komma åt Queue data

Beroende på hur du vill ge åtkomst till köade data i Azure Portal måste du ha vissa behörigheter. I de flesta fall tillhandahålls dessa behörigheter via rollbaserad åtkomst kontroll i Azure (Azure RBAC). Mer information om Azure RBAC finns i [Vad är Azure rollbaserad åtkomst kontroll (Azure RBAC)?](../../role-based-access-control/overview.md).

### <a name="use-the-account-access-key"></a>Använda kontots åtkomst nyckel

Om du vill komma åt Queue data med åtkomst nyckeln för kontot måste du ha en Azure-roll som är tilldelad till dig som innehåller Azure RBAC-åtgärden `Microsoft.Storage/storageAccounts/listkeys/action` . Den här Azure-rollen kan vara en inbyggd eller anpassad roll. Inbyggda roller som stöder `Microsoft.Storage/storageAccounts/listkeys/action` är:

- Rollen Azure Resource Manager [ägare](../../role-based-access-control/built-in-roles.md#owner)
- Rollen Azure Resource Manager [Contributor](../../role-based-access-control/built-in-roles.md#contributor)
- [Rollen lagrings konto deltagare](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

När du försöker komma åt Queue data i Azure Portal, kontrollerar portalen först om du har tilldelats en roll med `Microsoft.Storage/storageAccounts/listkeys/action` . Om du har tilldelats en roll med den här åtgärden använder portalen konto nyckeln för att komma åt data i kön. Om du inte har tilldelats en roll med den här åtgärden försöker portalen komma åt data med ditt Azure AD-konto.

> [!NOTE]
> Administratör och **medadministratör** för rollen administratör i den klassiska prenumerationen är motsvarigheten till Azure Resource Manager **-** [`Owner`](../../role-based-access-control/built-in-roles.md#owner) rollen. **Ägar** rollen innehåller alla åtgärder, inklusive `Microsoft.Storage/storageAccounts/listkeys/action` , så en användare med någon av dessa administrativa roller kan också komma åt köade data med konto nyckeln. Mer information finns i [klassiska prenumerationer på administratörs roller, Azure-roller och Azure AD-administratörer](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

### <a name="use-your-azure-ad-account"></a>Använd ditt Azure AD-konto

Om du vill komma åt Queue data från Azure Portal med ditt Azure AD-konto måste båda följande satser vara sanna för dig:

- Du har tilldelats Azure Resource Manager [`Reader`](../../role-based-access-control/built-in-roles.md#reader) -rollen minst till lagrings kontots nivå eller högre. Rollen **läsare** ger mest begränsade behörigheter, men en annan Azure Resource Manager roll som beviljar åtkomst till lagrings kontots hanterings resurser är också acceptabel.
- Du har tilldelats antingen en inbyggd eller anpassad roll som ger åtkomst till köa data.

Roll **tilldelnings rollen eller** en annan Azure Resource Manager roll tilldelning krävs så att användaren kan visa och navigera i lagrings kontots hanterings resurser i Azure Portal. Azure-rollerna som beviljar åtkomst till köa data ger inte åtkomst till lagrings kontots hanterings resurser. Användaren måste ha behörighet att navigera i lagrings konto resurser för att få åtkomst till köa data i portalen. Mer information om det här kravet finns i [tilldela rollen läsare för åtkomst till portalen](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access).

De inbyggda roller som stöder åtkomst till dina kubdata är:

- [Data deltagare i Storage Queue](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Läs-/skriv-/borttagnings behörigheter för köer.
- [Data läsare för lagrings köer](../../role-based-access-control/built-in-roles.md#storage-queue-data-reader): Läs behörighet för köer.

Anpassade roller har stöd för olika kombinationer av samma behörigheter som tillhandahålls av de inbyggda rollerna. Mer information om hur du skapar anpassade Azure-roller finns i [Azure-anpassade roller](../../role-based-access-control/custom-roles.md) och [förstå roll definitioner för Azure-resurser](../../role-based-access-control/role-definitions.md).

Det finns inte stöd för att Visa köer med en klassisk prenumerations administratörs roll. Om du vill visa en lista över köer måste användaren ha tilldelats rollen som Azure Resource Managers **läsare** , rollen **data läsare för lagrings kön** eller rollen **data deltagare i lagrings kön** .

> [!IMPORTANT]
> För hands versionen av Storage Explorer i Azure Portal stöder inte användning av Azure AD-autentiseringsuppgifter för att visa och ändra köade data. Storage Explorer i Azure Portal använder alltid konto nycklar för att komma åt data. Om du vill använda Storage Explorer i Azure Portal måste du ha tilldelats en roll som innehåller `Microsoft.Storage/storageAccounts/listkeys/action` .

## <a name="navigate-to-queues-in-the-azure-portal"></a>Navigera till köer i Azure Portal

Om du vill visa köa data i portalen går du till **översikten** för ditt lagrings konto och klickar på länkarna för **köer**. Du kan också navigera till avsnittet **kötjänst** på menyn.

:::image type="content" source="media/authorize-data-operations-portal/queue-access-portal.png" alt-text="Skärm bild som visar hur du navigerar till köa data i Azure Portal":::

## <a name="determine-the-current-authentication-method"></a>Fastställ den aktuella autentiseringsmetoden

När du navigerar till en kö anger Azure Portal om du för närvarande använder kontots åtkomst nyckel eller ditt Azure AD-konto för att autentisera.

### <a name="authenticate-with-the-account-access-key"></a>Autentisera med konto åtkomst nyckeln

Om du autentiserar med åtkomst nyckeln för kontot visas **åtkomst nyckeln** som anges som autentiseringsmetod i portalen:

:::image type="content" source="media/authorize-data-operations-portal/auth-method-access-key.png" alt-text="Skärm bild som visar användare som har åtkomst till köer med konto nyckeln":::

Om du vill växla till att använda Azure AD-konto klickar du på länken som är markerad i bilden. Om du har rätt behörigheter via de Azure-roller som har tilldelats dig kan du fortsätta. Men om du saknar rätt behörighet visas ett fel meddelande som liknar följande:

:::image type="content" source="media/authorize-data-operations-portal/auth-error-azure-ad.png" alt-text="Det gick inte att visa om Azure AD-kontot inte stöder åtkomst":::

Observera att inga köer visas i listan om ditt Azure AD-konto saknar behörighet att visa dem. Klicka på länken **Växla till åtkomst för att komma åt nyckeln** för att använda åtkomst nyckeln för autentisering igen.

### <a name="authenticate-with-your-azure-ad-account"></a>Autentisera med ditt Azure AD-konto

Om du autentiserar med hjälp av ditt Azure AD-konto visas **Azure AD-användarkontot** som autentiseringsmetod i portalen:

:::image type="content" source="media/authorize-data-operations-portal/auth-method-azure-ad.png" alt-text="Skärm bild som visar användare som har åtkomst till köer med Azure AD-konto":::

Om du vill växla till att använda kontots åtkomst nyckel klickar du på länken som är markerad i bilden. Om du har åtkomst till konto nyckeln kan du fortsätta. Om du däremot saknar åtkomst till konto nyckeln visar Azure Portal ett fel meddelande.

Köer visas inte i portalen om du inte har åtkomst till konto nycklarna. Klicka på länken **Växla till Azure AD-användarkonto** för att använda ditt Azure AD-konto för autentisering igen.

## <a name="next-steps"></a>Nästa steg

- [Autentisera åtkomst till Azure-blobbar och köer med hjälp av Azure Active Directory](../common/storage-auth-aad.md)
- [Använd Azure Portal för att tilldela en Azure-roll för åtkomst till blob-och Queue-data](../common/storage-auth-aad-rbac-portal.md)
- [Använd Azure CLI för att tilldela en Azure-roll för åtkomst till blob-och Queue-data](../common/storage-auth-aad-rbac-cli.md)
- [Använd Azure PowerShell-modulen för att tilldela en Azure-roll för åtkomst till blob-och Queue-data](../common/storage-auth-aad-rbac-powershell.md)
