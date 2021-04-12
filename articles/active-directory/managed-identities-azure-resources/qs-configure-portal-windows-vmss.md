---
title: Konfigurera hanterade identiteter på skalnings uppsättningen för virtuella datorer – Azure AD
description: Stegvisa instruktioner för att konfigurera hanterade identiteter för Azure-resurser på en skalnings uppsättning för virtuella datorer med hjälp av Azure Portal.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73c2f4167d6ce5465a879f6b2ef7e96d91065c4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97590929"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-vmss-using-the-azure-portal"></a>Konfigurera hanterade identiteter för Azure-resurser på en skalnings uppsättning för virtuella datorer (VMSS) med hjälp av Azure Portal

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterade identiteter för Azure-resurser tillhandahåller Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering, utan att ha autentiseringsuppgifter i din kod. 

I den här artikeln använder du PowerShell för att lära dig hur du utför följande hanterade identiteter för Azure-resurser på en skalnings uppsättning för virtuella datorer:

- Om du inte känner till hanterade identiteter för Azure-resurser kan du läsa [avsnittet Översikt](overview.md).
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Ditt konto måste ha följande Azure-roll tilldelningar för att utföra hanterings åtgärderna i den här artikeln:

    > [!NOTE]
    > Inga ytterligare Azure AD Directory-roll tilldelningar krävs.

    - [Virtuell dator deltagare](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) för att aktivera och ta bort systemtilldelad hanterad identitet från en skalnings uppsättning för virtuella datorer.

## <a name="system-assigned-managed-identity"></a>Systemtilldelad hanterad identitet

I det här avsnittet får du lära dig hur du aktiverar och inaktiverar den systemtilldelade hanterade identiteten med hjälp av Azure Portal.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-virtual-machine-scale-set"></a>Aktivera systemtilldelad hanterad identitet när en skalnings uppsättning för virtuell dator skapas

För närvarande stöder Azure Portal inte att aktivera systemtilldelad hanterad identitet under skapandet av en skalnings uppsättning för virtuella datorer. Läs i stället följande snabb starts artikel för virtuell dator för att skapa en skalnings uppsättning för virtuella datorer och fortsätt sedan till nästa avsnitt för information om hur du aktiverar systemtilldelad hanterad identitet på en virtuell dators skalnings uppsättning:

- [Skapa en skalnings uppsättning för virtuell dator i Azure Portal](../../virtual-machine-scale-sets/quick-create-portal.md)  

### <a name="enable-system-assigned-managed-identity-on-an-existing-virtual-machine-scale-set"></a>Aktivera systemtilldelad hanterad identitet på en befintlig virtuell dators skalnings uppsättning

Aktivera den systemtilldelade hanterade identiteten på en virtuell dators skalnings uppsättning som ursprungligen etablerades utan den:

1. Logga in på [Azure Portal](https://portal.azure.com) med ett konto som är kopplat till Azure-prenumerationen som innehåller den virtuella datorns skal uppsättning.

2. Navigera till önskad skalnings uppsättning för virtuella datorer.

3. Under **systemtilldelad**, **status** väljer du **på** och klickar sedan på **Spara**:

   ![Skärm bild som visar sidan "identitets (förhands granskning)" med "systemtilldelat" valt, status "på" och knappen "Spara" markerad.](./media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png) 

### <a name="remove-system-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Ta bort systemtilldelad hanterad identitet från en skalnings uppsättning för virtuell dator

Om du har en skalnings uppsättning för virtuella datorer som inte längre behöver en systemtilldelad hanterad identitet:

1. Logga in på [Azure Portal](https://portal.azure.com) med ett konto som är kopplat till Azure-prenumerationen som innehåller den virtuella datorns skal uppsättning. Kontrol lera också att ditt konto tillhör en roll som ger dig Skriv behörighet för den virtuella datorns skal uppsättning.

2. Navigera till önskad skalnings uppsättning för virtuella datorer.

3. Under **systemtilldelat**, **status** väljer du **av** och klickar sedan på **Spara**:

   ![Skärm bild för konfigurations sida](./media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)

## <a name="user-assigned-managed-identity"></a>Användartilldelad hanterad identitet

I det här avsnittet får du lära dig hur du lägger till och tar bort en användardefinierad hanterad identitet från en skalnings uppsättning för virtuella datorer med hjälp av Azure Portal.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Tilldela en användardefinierad hanterad identitet när en skalnings uppsättning för virtuell dator skapas

För närvarande stöder Azure Portal inte tilldelning av en användardefinierad hanterad identitet när en virtuell dators skalnings uppsättning skapas. Se i stället följande artikel för att skapa en virtuell dators skalnings uppsättning för att först skapa en skalnings uppsättning för virtuella datorer och fortsätt sedan till nästa avsnitt för information om hur du tilldelar en användardefinierad hanterad identitet till den:

- [Skapa en skalnings uppsättning för virtuell dator i Azure Portal](../../virtual-machine-scale-sets/quick-create-portal.md)

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>Tilldela en användardefinierad hanterad identitet till en befintlig skalnings uppsättning för virtuella datorer

1. Logga in på [Azure Portal](https://portal.azure.com) med ett konto som är kopplat till Azure-prenumerationen som innehåller den virtuella datorns skal uppsättning.
2. Navigera till önskad skalnings uppsättning för virtuell dator och klicka på **identitet**, **tilldelad användare** och **\+ Lägg sedan till**.

   ![Skärm bild som visar sidan "identitet" med "tilldelade användare" och knappen "Lägg till" markerad.](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vmss-screenshot1.png)

3. Klicka på den användare-tilldelade identitet som du vill lägga till i skalnings uppsättningen för den virtuella datorn och klicka sedan på **Lägg till**.
   
   ![Lägg till användardefinierad identitet till VMSS](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Ta bort en användardefinierad hanterad identitet från en skalnings uppsättning för virtuella datorer

1. Logga in på [Azure Portal](https://portal.azure.com) med ett konto som är kopplat till den Azure-prenumeration som innehåller den virtuella datorn.
2. Navigera till önskad skalnings uppsättning för virtuell dator och klicka på **identitet**, **tilldelad användare**, namnet på den användare som tilldelats den hanterade identitet som du vill ta bort och klicka sedan på **ta bort** (klicka på **Ja** i bekräftelse fönstret).

   ![Ta bort användarens tilldelade identitet från en VMSS](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vmss-screenshot.png)


## <a name="next-steps"></a>Nästa steg

- Med hjälp av Azure Portal ger du en skalnings uppsättning hanterad identitet för virtuella Azure-datorer [till en annan Azure-resurs](howto-assign-access-portal.md).
