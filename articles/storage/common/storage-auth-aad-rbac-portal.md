---
title: Använd Azure Portal för att tilldela en Azure-roll för data åtkomst
titleSuffix: Azure Storage
description: Lär dig hur du använder Azure Portal för att tilldela behörigheter till ett Azure Active Directory säkerhets objekt med rollbaserad åtkomst kontroll i Azure (Azure RBAC). Azure Storage stöder inbyggda och Azure-anpassade roller för autentisering via Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/10/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: d63a31eae57d09f1658f5f19c1518cb0648fa4e8
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100373765"
---
# <a name="use-the-azure-portal-to-assign-an-azure-role-for-access-to-blob-and-queue-data"></a>Använda Azure-portalen för tilldelning av en Azure-roll för åtkomst till blob- och ködata

Azure Active Directory (Azure AD) tillåter åtkomst rättigheter till skyddade resurser via [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../../role-based-access-control/overview.md). Azure Storage definierar en uppsättning inbyggda Azure-roller som omfattar vanliga uppsättningar behörigheter som används för att få åtkomst till BLOB-eller Queue-data.

När en Azure-roll tilldelas till ett säkerhets objekt för Azure AD ger Azure åtkomst till dessa resurser för säkerhets objekt. Åtkomst kan begränsas till prenumerations nivån, resurs gruppen, lagrings kontot eller en enskild behållare eller kö. Ett säkerhets objekt i Azure AD kan vara en användare, en grupp, ett huvud namn för program tjänsten eller en [hanterad identitet för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md).

Den här artikeln beskriver hur du använder Azure Portal för att tilldela Azure-roller. Azure Portal tillhandahåller ett enkelt gränssnitt för att tilldela Azure-roller och hantera åtkomst till dina lagrings resurser. Du kan också tilldela Azure-roller för blob-och Queue-resurser med hjälp av Azures kommando rads verktyg eller API: er för Azure Storage hantering. Mer information om Azure-roller för lagrings resurser finns i [autentisera åtkomst till Azure-blobbar och köer med hjälp av Azure Active Directory](storage-auth-aad.md).

## <a name="azure-roles-for-blobs-and-queues"></a>Azure-roller för blobbar och köer

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Bestäm resurs omfång

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="assign-azure-roles-using-the-azure-portal"></a>Tilldela Azure-roller med hjälp av Azure Portal

När du har bestämt lämplig omfattning för en roll tilldelning navigerar du till resursen i Azure Portal. Visa inställningarna för **Access Control (IAM)** för resursen och följ de här anvisningarna för att hantera roll tilldelningar:

1. Tilldela rätt Azure Storage Azure-roll för att bevilja åtkomst till ett säkerhets objekt i Azure AD.

1. Tilldela rollen Azure Resource Manager [läsare](../../role-based-access-control/built-in-roles.md#reader) till användare som behöver åtkomst till behållare eller köer via Azure Portal med sina autentiseringsuppgifter för Azure AD.

I följande avsnitt beskrivs var och en av de här stegen i detalj.

> [!IMPORTANT]
> När du skapar ett Azure Storage-konto tilldelas du inte automatiskt behörigheter för åtkomst till data via Azure AD. Du måste uttryckligen tilldela dig själv en Azure-roll för Azure Storage. Du kan tilldela den på nivån för din prenumeration, resurs grupp, lagrings konto eller behållare eller kö.
>
> Innan du tilldelar dig själv en roll för data åtkomst kommer du att kunna komma åt data i ditt lagrings konto via Azure Portal eftersom Azure Portal även kan använda konto nyckeln för data åtkomst. Mer information finns i [Välj hur du godkänner åtkomst till BLOB-data i Azure Portal](../blobs/authorize-data-operations-portal.md).
>
> Om lagrings kontot är låst med ett Azure Resource Manager skrivskyddat lås, förhindrar låset tilldelningen av Azure RBAC-roller som är begränsade till lagrings kontot eller till en data behållare (BLOB container eller queue).

### <a name="assign-an-azure-built-in-role"></a>Tilldela en inbyggd Azure-roll

Innan du tilldelar en roll till ett säkerhets objekt måste du ta hänsyn till omfattningen av de behörigheter som du beviljar. Granska avsnittet [Bestäm resurs omfång](#determine-resource-scope) för att bestämma lämplig omfattning.

Proceduren som visas här tilldelar en behållare en roll som är begränsad till en behållare, men du kan följa samma steg för att tilldela en roll som är begränsad till en kö:

1. I [Azure Portal](https://portal.azure.com)går du till ditt lagrings konto och visar **översikten** för kontot.
1. Under tjänster väljer du **blobbar**.
1. Leta upp den behållare som du vill tilldela en roll till och Visa behållarens inställningar.
1. Välj **åtkomst kontroll (IAM)** om du vill visa inställningar för åtkomst kontroll för behållaren. Välj fliken **roll tilldelningar** om du vill se en lista över roll tilldelningar.

    :::image type="content" source="media/storage-auth-aad-rbac-portal/portal-access-control-container.png" alt-text="Skärm bild som visar inställningar för åtkomst kontroll för behållare":::

1. Klicka på knappen **Lägg till roll tilldelning** för att lägga till en ny roll.
1. I fönstret **Lägg till roll tilldelning** väljer du den Azure Storages roll som du vill tilldela. Sök sedan efter det säkerhets objekt som du vill tilldela rollen.

    :::image type="content" source="media/storage-auth-aad-rbac-portal/add-rbac-role.png" alt-text="Skärm bild som visar hur du tilldelar en Azure-roll":::

1. Klicka på **Spara**. Identiteten som du har tilldelat rollen visas i listan under den rollen. Följande bild visar till exempel att användaren lade till nu har Läs behörighet till data i behållaren med namnet *Sample-container*.

    :::image type="content" source="media/storage-auth-aad-rbac-portal/container-scoped-role.png" alt-text="Skärm bild som visar en lista över användare som har tilldelats en roll":::

Du kan följa liknande steg för att tilldela en roll som är begränsad till lagrings kontot, resurs gruppen eller prenumerationen.

### <a name="assign-the-reader-role-for-portal-access"></a>Tilldela rollen läsare för åtkomst till portalen

När du tilldelar en inbyggd eller anpassad roll för Azure Storage till ett säkerhets objekt beviljar du behörigheter till säkerhets objekt för att utföra åtgärder på data i ditt lagrings konto. De inbyggda rollerna för **data läsare** ger Läs behörighet för data i en behållare eller kö, medan de inbyggda rollerna för **data deltagare** ger Läs-, skriv-och borttagnings behörighet till en behållare eller kö. Behörigheter tilldelas till den angivna resursen.  
Om du till exempel tilldelar rollen **Storage BLOB data Contributor** till användare Maria på nivån för en behållare med namnet **Sample-container**, beviljas Marys Läs-, skriv-och borttagnings åtkomst till alla blobar i behållaren.

Men om Mary vill visa en BLOB i Azure Portal, ger inte rollen **Storage BLOB data Contributor** av sig tillräckliga behörigheter för att navigera via portalen till blobben för att kunna visa den. Ytterligare Azure AD-behörigheter krävs för att navigera via portalen och Visa de andra resurser som är synliga där.

Om användarna behöver kunna komma åt blobar i Azure Portal, tilldelar du dem en ytterligare Azure-roll, rollen [läsare](../../role-based-access-control/built-in-roles.md#reader) , till dessa användare på lagrings kontots nivå eller över. Rollen **läsare** är en Azure Resource Manager roll som tillåter användare att Visa lagrings konto resurser, men inte ändra dem. Den ger inte Läs behörighet till data i Azure Storage, men endast för att hantera resurser för hantering.

Följ dessa steg om du vill tilldela rollen **läsare** så att en användare kan komma åt blobbar från Azure Portal. I det här exemplet är tilldelningen begränsad till lagrings kontot:

1. Navigera till ditt lagringskonto på [Azure-portalen](https://portal.azure.com).
1. Välj **åtkomst kontroll (IAM)** om du vill visa inställningarna för åtkomst kontroll för lagrings kontot. Välj fliken **roll tilldelningar** om du vill se en lista över roll tilldelningar.
1. I fönstret **Lägg till roll tilldelning** väljer du rollen **läsare** . 
1. I fältet **tilldela åtkomst till väljer du** **Azure AD-användare, grupp eller tjänstens huvud namn**.
1. Sök efter den säkerhets princip som du vill tilldela rollen.
1. Spara roll tilldelningen.

Du behöver bara tilldela rollen **läsare** för användare som behöver åtkomst till blobbar eller köer med hjälp av Azure Portal.

> [!IMPORTANT]
> För hands versionen av Storage Explorer i Azure Portal stöder inte användning av Azure AD-autentiseringsuppgifter för att visa och ändra BLOB-eller Queue data. Storage Explorer i Azure Portal använder alltid konto nycklar för att komma åt data. Om du vill använda Storage Explorer i Azure Portal måste du ha tilldelats en roll som innehåller **Microsoft. Storage/storageAccounts/listnycklar/Action**.

## <a name="next-steps"></a>Nästa steg

- Mer information om Azure-roller för lagrings resurser finns i [autentisera åtkomst till Azure-blobbar och köer med hjälp av Azure Active Directory](storage-auth-aad.md). 
- Om du vill veta mer om Azure RBAC, se [Vad är Azures rollbaserad åtkomst kontroll (Azure RBAC)?](../../role-based-access-control/overview.md).
- Information om hur du tilldelar och hanterar roll tilldelningar i Azure med Azure PowerShell, Azure CLI eller REST API finns i följande artiklar:
    - [Lägga till eller ta bort roll tilldelningar i Azure med hjälp av modulen Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
    - [Lägga till eller ta bort roll tilldelningar i Azure med hjälp av Azure CLI](../../role-based-access-control/role-assignments-cli.md)
    - [Lägga till eller ta bort Azure-rolltilldelningar med hjälp av REST API](../../role-based-access-control/role-assignments-rest.md)
- Information om hur du auktoriserar åtkomst till behållare och köer från dina lagrings program finns i [använda Azure AD med Azure Storage-program](storage-auth-aad-app.md).
