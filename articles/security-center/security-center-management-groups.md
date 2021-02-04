---
title: Få överblick över hela organisationen för Azure Security Center | Microsoft Docs
description: Den här artikeln förklarar hur du hanterar din säkerhets position i stor skala genom att tillämpa principer för alla prenumerationer som är kopplade till din Azure Active Directory-klient.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b85c0e93-9982-48ad-b23f-53b367f22b10
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/04/2021
ms.author: memildin
ms.openlocfilehash: 13cbc2e9451221fef951eb6fac4c6b2772275122
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99556421"
---
# <a name="organize-management-groups-subscriptions-and-tenant-wide-visibility"></a>Organisera hanterings grupper, prenumerationer och synligheten för hela klienten

Den här artikeln förklarar hur du hanterar din organisations säkerhets position i stor skala genom att använda säkerhets principer för alla Azure-prenumerationer som är kopplade till din Azure Active Directory-klient.

För att få insyn i säkerhets position för alla prenumerationer som registrerats i Azure AD-klienten, måste en Azure-roll med tillräcklig Läs behörighet tilldelas till rot hanterings gruppen.


## <a name="organize-your-subscriptions-into-management-groups"></a>Organisera dina prenumerationer i hanterings grupper

### <a name="introduction-to-management-groups"></a>Introduktion till hanterings grupper

Med Azures hanterings grupper kan du effektivt hantera åtkomst, principer och rapportering av prenumerations grupper, samt hantera hela Azure-egendomen på ett effektivt sätt genom att utföra åtgärder på rot hanterings gruppen. Du kan organisera prenumerationer i hanterings grupper och tillämpa dina styrnings principer i hanterings grupperna. Alla prenumerationer i en hanteringsgrupp ärver automatiskt de principer som tillämpas på hanteringsgruppen. 

Varje Azure AD-klient får en enda toppnivå hanterings grupp som kallas för **rot hanterings gruppen**. Rothanteringsgruppen är inbyggd i hierarkin så att alla hanteringsgrupper och prenumerationer är dess underordnade element. Med den här gruppen kan globala principer och Azure Role-tilldelningar tillämpas på katalog nivå. 

Rot hanterings gruppen skapas automatiskt när du gör någon av följande åtgärder: 
- Öppna **hanteringsgrupper** i [Azure Portal](https://portal.azure.com).
- Skapa en hanterings grupp med ett API-anrop.
- Skapa en hanterings grupp med PowerShell. PowerShell-instruktioner finns i [skapa hanterings grupper för hantering av resurser och organisationer](../governance/management-groups/create-management-group-portal.md).

Hanterings grupper krävs inte för att publicera Security Center, men vi rekommenderar att du skapar minst en så att rot hanterings gruppen skapas. När gruppen har skapats kommer alla prenumerationer i Azure AD-klienten att länkas till den. 


En detaljerad översikt över hanterings grupper finns i artikeln [organisera dina resurser med Azures hanterings grupper](../governance/management-groups/overview.md) .

### <a name="view-and-create-management-groups-in-the-azure-portal"></a>Visa och skapa hanterings grupper i Azure Portal

1. Från [Azure Portal](https://portal.azure.com)använder du sökrutan i det övre fältet för att hitta och öppna **hanteringsgrupper**.

    :::image type="content" source="./media/security-center-management-groups/open-management-groups-service.png" alt-text="Åtkomst till dina hanterings grupper":::

    Listan över hanterings grupper visas.

1. Om du vill skapa en hanterings grupp väljer du **Lägg till hanterings grupp**, anger relevant information och väljer **Spara**.

    :::image type="content" source="media/security-center-management-groups/add-management-group.png" alt-text="Lägga till en hanterings grupp i Azure":::

    - **Hanterings gruppens ID** är katalogens unika identifierare som används för att skicka kommandon i den här hanterings gruppen. Den här identifieraren kan inte redige ras när den används i hela Azure-systemet för att identifiera den här gruppen. 
    - Fältet visnings namn är det namn som visas i Azure Portal. Ett separat visnings namn är ett valfritt fält när du skapar hanterings gruppen och kan ändras när som helst.  


### <a name="add-subscriptions-to-a-management-group"></a>Lägga till prenumerationer i en hanterings grupp
Du kan lägga till prenumerationer i hanterings gruppen som du skapade.

1. Under **hanteringsgrupper** väljer du hanterings gruppen för din prenumeration.

    :::image type="content" source="./media/security-center-management-groups/management-group-subscriptions.png" alt-text="Välj en hanterings grupp för din prenumeration":::

1. När grupp sidan öppnas väljer du **information**

    :::image type="content" source="./media/security-center-management-groups/management-group-details-page.png" alt-text="Öppna sidan information i en hanterings grupp":::

1. På sidan information om gruppen väljer du **Lägg till prenumeration** och väljer sedan prenumerationer och väljer **Spara**. Upprepa tills du har lagt till alla prenumerationer i omfånget.

    :::image type="content" source="./media/security-center-management-groups/management-group-add-subscriptions.png" alt-text="Lägga till en prenumeration i en hanterings grupp":::
   > [!IMPORTANT]
   > Hanterings grupper kan innehålla både prenumerationer och underordnade hanterings grupper. När du tilldelar en användare en Azure-roll till den överordnade hanterings gruppen ärvs åtkomsten av prenumerationerna på den underordnade hanterings gruppen. Principer som anges i den överordnade hanterings gruppen ärvs också av de underordnade. 


## <a name="grant-tenant-wide-permissions-to-yourself"></a>Bevilja behörighet för hela klienten själv

En användare med rollen Azure Active Directory (AD) för **Global administratör** kan ha ansvar för hela företaget, men saknar Azure-behörighet att visa den organisations information som finns i Azure Security Center. Behörighets höjning krävs eftersom roll tilldelningar för Azure AD inte beviljar åtkomst till Azure-resurser. 

> [!TIP]
> Läs mer om behörighets höjning för rollen global administratör i [öka åtkomsten för att hantera alla Azure-prenumerationer och hanterings grupper](../role-based-access-control/elevate-access-global-admin.md).

Så här tilldelar du dig själv behörigheter på klient nivå:

1. Om din organisation hanterar resurs åtkomst med [Azure AD Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-configure.md)eller något annat PIM-verktyg måste den globala administratörs rollen vara aktiv för användaren enligt proceduren nedan.

1. Som global administratörs användare utan en tilldelning i rot hanterings gruppen för klient organisationen öppnar du Security Center sidan **Översikt** och väljer länken för den sammanslagna länken för **hela klienten** i banderollbilden. 

    :::image type="content" source="media/security-center-management-groups/enable-tenant-level-permissions-banner.png" alt-text="Aktivera behörigheter för klient organisations nivå i Azure Security Center":::

1. Välj den nya Azure-roll som ska tilldelas. 

    :::image type="content" source="media/security-center-management-groups/enable-tenant-level-permissions-form.png" alt-text="Formulär för att definiera behörigheter för klient organisations nivå som ska tilldelas till din användare":::

    > [!TIP]
    > I allmänhet krävs rollen som säkerhets administratör för att tillämpa principer på rotnivån, medan säkerhets läsaren räcker för att tillhandahålla synligheten på klient nivå. Mer information om de behörigheter som har beviljats av de här rollerna finns i beskrivningen av den [inbyggda rollen säkerhets administratör](../role-based-access-control/built-in-roles.md#security-admin) eller den [inbyggda rollen säkerhets läsare](../role-based-access-control/built-in-roles.md#security-reader).
    >
    > För skillnader mellan dessa roller som är särskilt för Security Center, se tabellen i [roller och tillåtna åtgärder](security-center-permissions.md#roles-and-allowed-actions).

    Vyn i hela organisationen uppnås genom att du beviljar roller på klient organisationens rot hanterings grupps nivå.  

1. Logga ut från Azure Portal och logga sedan in igen.

1. När du har förhöjd åtkomst, öppna eller uppdatera Azure Security Center för att kontrol lera att du har insyn i alla prenumerationer under Azure AD-klienten. 


## <a name="request-tenant-wide-permissions-when-yours-are-insufficient"></a>Begär behörigheter för klient åtkomst när dina inte är tillräckliga

Om du loggar in på Security Center och ser en banderoll som visar att vyn är begränsad kan du klicka på genom att skicka en begäran till den globala administratören för din organisation. I begäran kan du ta med den roll som du vill tilldela och den globala administratören fattar ett beslut om vilken roll som ska beviljas. 

Det är det globala administratörs beslutet om du vill acceptera eller avvisa dessa förfrågningar. 

> [!IMPORTANT]
> Du kan bara skicka en begäran var sjunde dag.

Så här begär du utökade behörigheter från den globala administratören:

1. Öppna Azure Security Center från Azure Portal.

1. Om du ser banderollen "du ser begränsad information". Markera det.

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions.png" alt-text="Banderoll som informerar användaren om att de kan begära behörigheter för hela klienten.":::

1. I formuläret detaljerad begäran väljer du önskad roll och motivering för varför du behöver dessa behörigheter.

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions-details.png" alt-text="Informations sida för att begära klient behörighet från din globala Azure-administratör":::

1. Välj **begär åtkomst**.

    Ett e-postmeddelande skickas till den globala administratören. E-postmeddelandet innehåller en länk till Security Center där de kan godkänna eller avvisa begäran.

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions-email.png" alt-text="E-posta till den globala administratören för nya behörigheter":::

    När den globala administratören väljer **Granska begäran** och slutför processen, skickas ett e-postmeddelande till den begär ande användaren. 

## <a name="assign-azure-roles-to-other-users"></a>Tilldela Azure-roller till andra användare

### <a name="assign-azure-roles-to-users-through-the-azure-portal"></a>Tilldela Azure-roller till användare via Azure Portal: 
1. Logga in på [Azure-portalen](https://portal.azure.com). 
1. Om du vill visa hanterings grupper väljer du **alla tjänster** på huvud menyn i Azure och väljer sedan **hanteringsgrupper**.
1.  Välj en hanterings grupp och välj **information**.

    :::image type="content" source="./media/security-center-management-groups/management-group-details.PNG" alt-text="Skärm bild för Hanteringsgrupper information":::

1. Välj **åtkomst kontroll (IAM)** sedan **roll tilldelningar**.
1. Välj **Lägg till rolltilldelning**.
1. Välj den roll som ska tilldelas och användaren och välj sedan **Spara**.  
   
   ![Skärm bild för Lägg till säkerhets läsar roll](./media/security-center-management-groups/asc-security-reader.png)

### <a name="assign-azure-roles-to-users-with-powershell"></a>Tilldela Azure-roller till användare med PowerShell: 
1. Installera [Azure PowerShell](/powershell/azure/install-az-ps).
2. Kör följande kommandon: 

    ```azurepowershell
    # Login to Azure as a Global Administrator user
    Connect-AzAccount
    ```

3. Logga in med autentiseringsuppgifter för global administratör när du uppmanas till det. 

    ![Skärm bild för inloggnings varning](./media/security-center-management-groups/azurerm-sign-in.PNG)

4. Ge läsar roll behörighet genom att köra följande kommando:

    ```azurepowershell
    # Add Reader role to the required user on the Root Management Group
    # Replace "user@domian.com” with the user to grant access to
    New-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/"
    ```
5. Om du vill ta bort rollen använder du följande kommando: 

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/" 
    ```

## <a name="remove-elevated-access"></a>Ta bort utökad åtkomst 

När Azure-rollerna har tilldelats till användarna bör klient administratören ta bort sig själv från rollen som administratör för användar åtkomst.

1. Logga in på [Azure Portal](https://portal.azure.com) eller [Azure Active Directory administrations Center](https://aad.portal.azure.com).

2. Välj **Azure Active Directory** i navigerings listan och välj sedan **Egenskaper**.

3. Under **åtkomst hantering för Azure-resurser** anger du växeln till **Nej**.

4. Spara inställningen genom att välja **Spara**.



## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur du får insyn i hela organisationen för Azure Security Center. Relaterad information finns i:

- [Behörigheter i Azure Security Center](security-center-permissions.md)