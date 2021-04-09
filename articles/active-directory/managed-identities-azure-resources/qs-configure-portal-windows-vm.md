---
title: Konfigurera hanterade identiteter med Azure Portal – Azure AD
description: Steg för steg-instruktioner för att konfigurera hanterade identiteter för Azure-resurser på en virtuell Azure-dator med hjälp av Azure Portal.
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
ms.openlocfilehash: 78c973537e958b7199c137c81e72091f70b84ddb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97590995"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-vm-using-the-azure-portal"></a>Konfigurera hanterade identiteter för Azure-resurser på en virtuell dator med hjälp av Azure Portal

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterade identiteter för Azure-resurser tillhandahåller Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering, utan att ha autentiseringsuppgifter i din kod. 

I den här artikeln får du lära dig hur du aktiverar och inaktiverar system-och användarspecifika hanterade identiteter för en virtuell Azure-dator (VM) med hjälp av Azure Portal. 

## <a name="prerequisites"></a>Förutsättningar

- Om du inte känner till hanterade identiteter för Azure-resurser kan du läsa [avsnittet Översikt](overview.md).
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.

## <a name="system-assigned-managed-identity"></a>Systemtilldelad hanterad identitet

I det här avsnittet får du lära dig hur du aktiverar och inaktiverar den systemtilldelade hanterade identiteten för den virtuella datorn med hjälp av Azure Portal.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-vm"></a>Aktivera systemtilldelad hanterad identitet när en virtuell dator skapas

För att aktivera systemtilldelad hanterad identitet på en virtuell dator när den skapas, måste ditt konto ha roll tilldelningen [virtuell dator deltagare](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) .  Inga ytterligare roll tilldelningar för Azure AD-katalogen krävs.

- På fliken **hantering** i avsnittet **identitet** växlar du till **på för** **hanterad tjänst identitet** .  

![Aktivera systemtilldelad identitet när en virtuell dator skapas](./media/msi-qs-configure-portal-windows-vm/enable-system-assigned-identity-vm-creation.png)

Använd följande snabb starter för att skapa en virtuell dator: 

- [Skapa en virtuell Windows-dator med Azure Portal](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine) 
- [Skapa en virtuell Linux-dator med Azure-portalen](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)


### <a name="enable-system-assigned-managed-identity-on-an-existing-vm"></a>Aktivera systemtilldelad hanterad identitet på en befintlig virtuell dator

Om du vill aktivera systemtilldelad hanterad identitet på en virtuell dator som ursprungligen etablerades utan den, måste ditt konto ha roll tilldelningen [virtuell dator deltagare](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) .  Inga ytterligare roll tilldelningar för Azure AD-katalogen krävs.

1. Logga in på [Azure Portal](https://portal.azure.com) med ett konto som är kopplat till den Azure-prenumeration som innehåller den virtuella datorn.

2. Navigera till önskad virtuell dator och välj **identitet**.

3. Under **systemtilldelad**, **status** väljer du **på** och klickar sedan på **Spara**:

   ![Skärm bild som visar sidan "identitets (förhands granskning)" med status "tilldelad" inställd på "på".](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

### <a name="remove-system-assigned-managed-identity-from-a-vm"></a>Ta bort systemtilldelad hanterad identitet från en virtuell dator

För att ta bort systemtilldelad hanterad identitet från en virtuell dator måste ditt konto ha roll tilldelningen [virtuell dator deltagare](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) .  Inga ytterligare roll tilldelningar för Azure AD-katalogen krävs.

Om du har en virtuell dator som inte längre behöver systemtilldelad hanterad identitet:

1. Logga in på [Azure Portal](https://portal.azure.com) med ett konto som är kopplat till den Azure-prenumeration som innehåller den virtuella datorn. 

2. Navigera till önskad virtuell dator och välj **identitet**.

3. Under **systemtilldelat**, **status** väljer du **av** och klickar sedan på **Spara**:

   ![Skärm bild för konfigurations sida](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)

## <a name="user-assigned-managed-identity"></a>Användartilldelad hanterad identitet

 I det här avsnittet får du lära dig hur du lägger till och tar bort en användare som tilldelats en hanterad identitet från en virtuell dator med hjälp av Azure Portal.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-vm"></a>Tilldela en användardefinierad identitet när en virtuell dator skapas

För att tilldela en användardefinierad identitet till en virtuell dator måste ditt konto ha roll tilldelningarna [virtuell dator deltagare](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) och [hanterad identitets operatör](../../role-based-access-control/built-in-roles.md#managed-identity-operator) . Inga ytterligare roll tilldelningar för Azure AD-katalogen krävs.

För närvarande stöder Azure Portal inte tilldelning av en användardefinierad hanterad identitet när en virtuell dator skapas. Läs i stället någon av följande snabb starts artiklar för virtuella datorer för att först skapa en virtuell dator och fortsätt sedan till nästa avsnitt för information om hur du tilldelar en användardefinierad hanterad identitet till den virtuella datorn:

- [Skapa en virtuell Windows-dator med Azure Portal](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Skapa en virtuell Linux-dator med Azure-portalen](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-vm"></a>Tilldela en användardefinierad hanterad identitet till en befintlig virtuell dator

För att tilldela en användardefinierad identitet till en virtuell dator måste ditt konto ha roll tilldelningarna [virtuell dator deltagare](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) och [hanterad identitets operatör](../../role-based-access-control/built-in-roles.md#managed-identity-operator) . Inga ytterligare roll tilldelningar för Azure AD-katalogen krävs.

1. Logga in på [Azure Portal](https://portal.azure.com) med ett konto som är kopplat till den Azure-prenumeration som innehåller den virtuella datorn.
2. Navigera till önskad VM och klicka på **identitet**, **tilldelad användare** och **\+ Lägg sedan till**.

   ![Skärm bild som visar sidan "identitet" med "tilldelade användare" och knappen "Lägg till" markerad.](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot1.png)

3. Klicka på den användare-tilldelade identitet som du vill lägga till i den virtuella datorn och klicka sedan på **Lägg till**.

    ![Lägg till användardefinierad hanterad identitet till den virtuella datorn](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-managed-identity-from-a-vm"></a>Ta bort en användare som tilldelats en hanterad identitet från en virtuell dator

För att ta bort en tilldelad identitet från en virtuell dator måste ditt konto ha roll tilldelningen [virtuell dator deltagare](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) . Inga ytterligare roll tilldelningar för Azure AD-katalogen krävs.

1. Logga in på [Azure Portal](https://portal.azure.com) med ett konto som är kopplat till den Azure-prenumeration som innehåller den virtuella datorn.
2. Gå till önskad VM och klicka på **identitet**, **tilldelad användare**, namnet på den användare som tilldelats den hanterade identitet som du vill ta bort och klicka sedan på **ta bort** (klicka på **Ja** i bekräftelse fönstret).

   ![Ta bort användardefinierad hanterad identitet från en virtuell dator](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vm-screenshot.png)

## <a name="next-steps"></a>Nästa steg

- Med hjälp av Azure Portal ger du en Azure VM-hanterad identitets [åtkomst till en annan Azure-resurs](howto-assign-access-portal.md).