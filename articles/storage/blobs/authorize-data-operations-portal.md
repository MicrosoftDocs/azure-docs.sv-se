---
title: Välj hur du vill ge åtkomst till BLOB-data i Azure Portal
titleSuffix: Azure Storage
description: När du har åtkomst till BLOB-data med hjälp av Azure Portal, gör portalen förfrågningar till Azure Storage under försättsblad. Dessa förfrågningar till Azure Storage kan autentiseras och auktoriseras med hjälp av antingen ditt Azure AD-konto eller lagrings kontots åtkomst nyckel.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/08/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: blobs
ms.custom: contperf-fy21q1
ms.openlocfilehash: 8c963f11a34217253f02cb5d116d66cdbf8bcc19
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033966"
---
# <a name="choose-how-to-authorize-access-to-blob-data-in-the-azure-portal"></a>Välj hur du vill ge åtkomst till BLOB-data i Azure Portal

När du har åtkomst till BLOB-data med hjälp av [Azure Portal](https://portal.azure.com), gör portalen förfrågningar till Azure Storage under försättsblad. En begäran till Azure Storage kan godkännas med hjälp av antingen ditt Azure AD-konto eller lagrings kontots åtkomst nyckel. Portalen visar vilken metod du använder och gör att du kan växla mellan de två om du har rätt behörighet.  

Du kan också ange hur du vill auktorisera en enskild BLOB-uppladdning i Azure Portal. Som standard använder portalen den metod som du redan använder för att auktorisera en BLOB-uppladdnings åtgärd, men du kan ändra den här inställningen när du laddar upp en blob.

## <a name="permissions-needed-to-access-blob-data"></a>Behörigheter som krävs för att få åtkomst till BLOB-data

Beroende på hur du vill ge åtkomst till BLOB-data i Azure Portal måste du ha vissa behörigheter. I de flesta fall tillhandahålls dessa behörigheter via rollbaserad åtkomst kontroll i Azure (Azure RBAC). Mer information om Azure RBAC finns i [Vad är Azure rollbaserad åtkomst kontroll (Azure RBAC)?](../../role-based-access-control/overview.md).

### <a name="use-the-account-access-key"></a>Använda kontots åtkomst nyckel

För att få åtkomst till BLOB-data med åtkomst nyckeln för kontot måste du ha en Azure-roll som är tilldelad till dig som innehåller Azure RBAC **-åtgärden Microsoft. Storage/storageAccounts/listnycklar/Action**. Den här Azure-rollen kan vara en inbyggd eller anpassad roll. Inbyggda roller som stöder **Microsoft. Storage/storageAccounts/listnycklar/Action** är:

- Rollen Azure Resource Manager [ägare](../../role-based-access-control/built-in-roles.md#owner)
- Rollen Azure Resource Manager [Contributor](../../role-based-access-control/built-in-roles.md#contributor)
- Rollen [lagrings konto deltagare](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

När du försöker få åtkomst till BLOB-data i Azure Portal, kontrollerar portalen först om du har tilldelats en roll med **Microsoft. Storage/storageAccounts/listnycklar/Action**. Om du har tilldelats en roll med den här åtgärden använder portalen konto nyckeln för att få åtkomst till BLOB-data. Om du inte har tilldelats en roll med den här åtgärden försöker portalen komma åt data med ditt Azure AD-konto.

> [!NOTE]
> Administratören för rollen administratör för den klassiska prenumerationen och Co-Administrator innehåller motsvarigheten till Azure Resource Manager [Owner](../../role-based-access-control/built-in-roles.md#owner) -rollen. **Ägar** rollen innehåller alla åtgärder, inklusive **Microsoft. Storage/storageAccounts/listnycklar/Action**, så en användare med någon av dessa administrativa roller kan också komma åt BLOB-data med konto nyckeln. Mer information finns i [klassiska prenumerationer på administratörs roller, Azure-roller och Azure AD-administratörer](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

### <a name="use-your-azure-ad-account"></a>Använd ditt Azure AD-konto

För att få åtkomst till BLOB-data från Azure Portal med ditt Azure AD-konto måste båda följande satser vara sanna för dig:

- Du har tilldelats rollen som Azure Resource Managers [läsare](../../role-based-access-control/built-in-roles.md#reader) , minst begränsat till lagrings kontots nivå eller högre. Rollen **läsare** ger mest begränsade behörigheter, men en annan Azure Resource Manager roll som beviljar åtkomst till lagrings kontots hanterings resurser är också acceptabel.
- Du har tilldelats antingen en inbyggd eller anpassad roll som ger åtkomst till BLOB-data.

Roll **tilldelnings rollen eller** en annan Azure Resource Manager roll tilldelning krävs så att användaren kan visa och navigera i lagrings kontots hanterings resurser i Azure Portal. Azure-rollerna som beviljar åtkomst till BLOB-data ger inte åtkomst till lagrings kontots hanterings resurser. För att få åtkomst till BLOB-data i portalen behöver användaren behörighet att navigera i lagrings konto resurser. Mer information om det här kravet finns i [tilldela rollen läsare för åtkomst till portalen](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access).

De inbyggda roller som stöder åtkomst till dina BLOB-data är:

- [Storage BLOB data-ägare](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner): för POSIX-åtkomstkontroll för Azure Data Lake Storage Gen2.
- [Storage BLOB data-deltagare](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Läs-/skriv-/borttagnings behörigheter för blobbar.
- [Storage BLOB data Reader](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader): skrivskyddade behörigheter för blobbar.

Anpassade roller har stöd för olika kombinationer av samma behörigheter som tillhandahålls av de inbyggda rollerna. Mer information om hur du skapar anpassade Azure-roller finns i [Azure-anpassade roller](../../role-based-access-control/custom-roles.md) och [förstå roll definitioner för Azure-resurser](../../role-based-access-control/role-definitions.md).

> [!IMPORTANT]
> För hands versionen av Storage Explorer i Azure Portal stöder inte användning av Azure AD-autentiseringsuppgifter för att visa och ändra BLOB-data. Storage Explorer i Azure Portal använder alltid konto nycklar för att komma åt data. Om du vill använda Storage Explorer i Azure Portal måste du ha tilldelats en roll som innehåller **Microsoft. Storage/storageAccounts/listnycklar/Action**.

## <a name="navigate-to-blobs-in-the-azure-portal"></a>Navigera till blobbar i Azure Portal

Om du vill visa BLOB-data i portalen navigerar du till **översikten** för ditt lagrings konto och klickar på länkarna för **blobbar**. Du kan också navigera till avsnittet **BLOB service** på menyn.

:::image type="content" source="media/anonymous-read-access-configure/blob-public-access-portal.png" alt-text="Skärm bild som visar hur du navigerar till BLOB-data i Azure Portal":::

## <a name="determine-the-current-authentication-method"></a>Fastställ den aktuella autentiseringsmetoden

När du navigerar till en behållare anger Azure Portal om du för närvarande använder kontots åtkomst nyckel eller ditt Azure AD-konto för att autentisera.

### <a name="authenticate-with-the-account-access-key"></a>Autentisera med konto åtkomst nyckeln

Om du autentiserar med åtkomst nyckeln för kontot visas **åtkomst nyckeln** som anges som autentiseringsmetod i portalen:

:::image type="content" source="media/authorize-data-operations-portal/auth-method-access-key.png" alt-text="Skärm bild som visar användare som för närvarande använder behållare med konto nyckeln":::

Om du vill växla till att använda Azure AD-konto klickar du på länken som är markerad i bilden. Om du har rätt behörigheter via de Azure-roller som har tilldelats dig kan du fortsätta. Men om du saknar rätt behörighet visas ett fel meddelande som liknar följande:

:::image type="content" source="media/authorize-data-operations-portal/auth-error-azure-ad.png" alt-text="Det gick inte att visa om Azure AD-kontot inte stöder åtkomst":::

Observera att inga blobbar visas i listan om ditt Azure AD-konto saknar behörighet att visa dem. Klicka på länken **Växla till åtkomst för att komma åt nyckeln** för att använda åtkomst nyckeln för autentisering igen.

### <a name="authenticate-with-your-azure-ad-account"></a>Autentisera med ditt Azure AD-konto

Om du autentiserar med hjälp av ditt Azure AD-konto visas **Azure AD-användarkontot** som autentiseringsmetod i portalen:

:::image type="content" source="media/authorize-data-operations-portal/auth-method-azure-ad.png" alt-text="Skärm bild som visar användare som för närvarande använder behållare med Azure AD-konto":::

Om du vill växla till att använda kontots åtkomst nyckel klickar du på länken som är markerad i bilden. Om du har åtkomst till konto nyckeln kan du fortsätta. Men om du saknar åtkomst till konto nyckeln visas ett fel meddelande som liknar följande:

:::image type="content" source="media/authorize-data-operations-portal/auth-error-access-key.png" alt-text="Fel som visas om du inte har åtkomst till konto nyckeln":::

Observera att inga blobbar visas i listan om du inte har åtkomst till konto nycklarna. Klicka på länken **Växla till Azure AD-användarkonto** för att använda ditt Azure AD-konto för autentisering igen.

## <a name="specify-how-to-authorize-a-blob-upload-operation"></a>Ange hur en BLOB-uppladdning ska auktoriseras

När du laddar upp en BLOB från Azure Portal kan du ange om du vill autentisera och auktorisera åtgärden med åtkomst nyckeln för kontot eller med dina autentiseringsuppgifter för Azure AD. Som standard använder portalen den aktuella autentiseringsmetoden som visas i [fastställa den aktuella autentiseringsmetoden](#determine-the-current-authentication-method).

Följ dessa steg om du vill ange hur du ska auktorisera en BLOB-uppladdnings åtgärd:

1. I Azure Portal navigerar du till den behållare där du vill ladda upp en blob.
1. Välj knappen **Ladda upp**.
1. Expandera avsnittet **Avancerat** om du vill visa de avancerade egenskaperna för blobben.
1. I fältet **Autentiseringstyp** anger du om du vill auktorisera uppladdnings åtgärden med hjälp av ditt Azure AD-konto eller med konto åtkomst nyckeln, som du ser i följande bild:

    :::image type="content" source="media/authorize-data-operations-portal/auth-blob-upload.png" alt-text="Skärm bild som visar hur du ändrar Authorization-metoden vid BLOB-uppladdning":::

## <a name="next-steps"></a>Nästa steg

- [Autentisera åtkomst till Azure-blobbar och köer med hjälp av Azure Active Directory](../common/storage-auth-aad.md)
- [Använd Azure Portal för att tilldela en Azure-roll för åtkomst till blob-och Queue-data](../common/storage-auth-aad-rbac-portal.md)
- [Använd Azure CLI för att tilldela en Azure-roll för åtkomst till blob-och Queue-data](../common/storage-auth-aad-rbac-cli.md)
- [Använd Azure PowerShell-modulen för att tilldela en Azure-roll för åtkomst till blob-och Queue-data](../common/storage-auth-aad-rbac-powershell.md)
